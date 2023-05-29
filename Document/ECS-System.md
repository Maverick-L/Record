# System
系统恢复运行描述：每一个System都会维护一个属于自己的EntityQuery队列，且都继承自ComponentSystemBase，在当EntityQuery队列已经被定义之后且为空的时候，或者ComponentSystemBase.Enable =false 的时候系统停止运行，

**可以使用AlwaysUpdateSystemAttribute来控制Update永久运行,不受EntityQuery队列影响**

## Unity提供的父类类型解析

### 1. ComponentSystemBase
 >所有的System都需要继承的父类，提供基础父类的方法。
 #### 提供的方法
1. OnCreate() 在创建的时候调用
2. OnStartRunning() 在当系统恢复运行的第一次Update之前调用
3. OnUpdate() 每一帧调用
4. OnStopRunning() 当系统暂停运行的时候调用一次
5. OnDestory() 系统被删除的时候调用
6. ForEach 此方法放在后面单独描述
> 当使用ForEach 语句设定依赖的Component的时候，如果现在的Entity中第一次拥有依赖的Component的时候，标记为系统恢复运行。当所有的Entity中都没有依赖的Component的时候，OnUpdate将不再调用。如果不适用ForEach语句，则一直处于系统运行中。

### 2. SystemBase :ComponentSystemBase
>Unity官方现在想要使用SystemBase去融合ComponentSystem和JobComponentSystem，此类内部同时支持这两个类的内容。

### 3. ComponentSystem :ComponentSystemBase
> 使用在主线程中进行或者未使用专门针对ECS优化的Job系统实现的System子类

### 4. JobComponentSystem :ComponentSystemBase
> 可以使用Job系统去结合**IJobTrunk**使用，实现多线程控制

## 多线程Job处理
  在使用Schedule创建Job的时候，在每一个ForEach 的时候创建的Job并不是并行的，而是根据顺序等待的。我们可以使用**JobHandle.CombineDependencies**方法来将所有的Job并行，但是要注意ForEach内部的参数，因为当多个Job需要修改同一个Entity的时候，其所在的Job就会进行排队等待。

## 核心方法单独描述
### SystemBase.ForEach
#### 注意点
1. ForEach 的lambda表达式只能使用定义域在方法内的变量。

#### 方法介绍.
1. Run() 在主线程执行
2. Schedule() 单独开辟一个线程处理
3. ScheduleParallel() 线程并行，并行原理是按照Chunk分配，每一个Chunk单独分配一个线程处理。
> chunk : ECS中采用EntityArchetpe来分配，拥有相同的组件的Entity会被分配到一个Chunk中，当一个Chunk空间不够的时候，就会创建一个新的Chunk空间。
4. 对EntityQueue设置的代码部分,用来确定EntityQueue筛选Entity的规则
   - WithAll<> ,必须拥有这里设置的所有项
   - WithAny<> ,只需要满足
   - WithNone<> ,不包含这其中的任何一个
   - Options
5. WithName() 给你的Job设置一个名字
10. lambda 表达式内部 允许传参：entity ，entityQueurIndex,nativeThreadIndex  

## Sync Point 同步点
  不允许在Foreach 或者Job 中使用结构性改变的操作。这样会产生同步点，会导致当前线程等待所有的Job任务完成。会限制一段时间内使用线程的能力。结构性改变还会导致一些对Component的引用丢失，包括 DynamicBuff的实例和通过 ComponentSystemBase.GetComponentDataFromEntity 获取的组件实例


### 结构性操作
- Create Entities
- Deleting Entities
- Adding Components to an entities
- Remove Components to an entities
- Changing the value of shared Components

### 避免同步点方法
1. 使用ECBs（Entity Command Buffers）来创造一个结构性改变队列，而不是立即执行。ECBS会在帧结束之后的一个时间点内创造一个同步点，当执行ECBS的时候会将跨帧的多个同步点合并，放到同一个同步点操作。
> 可以创建 继承自 EntityCommandBufferSystem的 System,放到当前组的第一个或者最后一个。通过这个ECB系统可以使当前一组内的所有的System的结构性操作合并到一起
2. 在主线程上进行结构性操作

## Write Groups 过滤组
### 规则
当System中启用了WriteGroups之后，EntityQueue会将WithAll 或者 WithAny 中的属于Component组件的过滤组的Component加入到WithNone中，除非你将某个过滤组内的成员加入到了WithAll 或者WithAny 中。因为Component被加入到了WithNone中，所以EntitiyQueue将会移除拥有这些Component的组件。
### 设定方法
~~~
public struct W : IComponentData
{
    public int value;
}

[WriteGroup(typeof(W))]
public struct A : IComponentData
{
   public int value;
}

[WriteGroup(typeof(W))]
public struct B : IComponentData
{
    public int value;
}

[UpdateInGroup(typeof(RenderUpdateGroup))]
public class TestSystem : SystemBase
{
    protected override void OnCreate()
    {
        var e1 = EntityManager.CreateEntity(typeof(A));
        var e2 = EntityManager.CreateEntity(typeof(W));
        var e3 = EntityManager.CreateEntity(typeof(A), typeof(B));
        var e4 = EntityManager.CreateEntity(typeof(W), typeof(A), typeof(B));
        EntityManager.SetName(e1, "1");
        EntityManager.SetName(e2, "2");
        EntityManager.SetName(e3, "3");
        EntityManager.SetName(e4, "4");
    }
    protected override void OnUpdate()
    {
        Entities.WithEntityQueryOptions( EntityQueryOptions.FilterWriteGroup).ForEach((Entity entity,ref W w) => {

            Debug.LogError("1111111111111111:"+entity.Index);
        }).Run();
    }
}
~~~
如代码，最终输出的entity中只有第一个，没有第四个。