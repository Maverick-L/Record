# World
ECS中的世界是由Entity组和SystemGroup组 组合而成的，SystemGroup控制System的运行顺序。  
其内部的更新顺序主要依靠的是Unity的 PlayerLoopSystem 系统。用这个系统控制三个系统Group，然后系统Group在用来控制下面的System组。

## 宏
#UNITY_DISABLE_AUTOMATIC_SYSTEM_BOOTSTRAP_RUNTIME_WORLD 运行时禁止创建默认世界  
#UNITY_DISABLE_AUTOMATIC_SYSTEM_BOOTSTRAP_EDITOR_WORLD 编辑器模式下禁止创建默认世界  
#UNITY_DISABLE_AUTOMATIC_SYSTEM_BOOTSTRAP 两种情况下都禁止创建默认世界

##  ICustomBootstrap
> 可以在游戏开始的时候需要创建多个世界的时候可以使用此接口来控制创建的世界。
> 实现Initialize 方法 return true 可以不创建DefaultWorld

## 创建新的世界
创建新的世界的时候可以自己完全控制世界内System的运行，也可以使用UnityECS设定好的方法进行System运行控制。

#### 当你使用Unity提供的System运行控制方案的时候需要注意以下地方：
 1. 当你使用New方法创建好一个世界之后，使用ScriptBehaviourUpdateOrder.UpdatePlayerLoop方法进行世界运行加载。
 2. 必须预先添加Ecs提供的三个Group组，至少添加其中一个。
 3. 你所添加的所有的System必须手动使用AddSystemToUpdateList方法进行System和Group的关联。此时和你给System添加的属性方法无关，不会进行自动关联。
 4. 第2点和第3点可以使用DefaultWorldInitialization.AddSystemsToRootLevelSystemGroup此方法忽略，此方法内部会自动使用你给System添加的属性方法处理所有的System。
 5. **多个世界如果都想使用Unity提供的方案进行更新，在UpdatePlayerLoop的时候需要获取PlayerLoop.GetCurrentPlayerLoop(),不能使用DefaultPlayerLoop**

~~~
        //方法1：
        var simulationSystem = logicWorld.GetOrCreateSystem<SimulationSystemGroup>();
        LogicUpdateGroup logicGroup = logicWorld.CreateSystem<LogicUpdateGroup>();
        System2WithLogicGroupType s1 = logicWorld.CreateSystem<System2WithLogicGroupType>();
        System3WithLogicGroupType s2 = logicWorld.CreateSystem<System3WithLogicGroupType>();
        var system = logicWorld.CreateSystem<FloorCreateSystem>();
        simulationSystem.AddSystemToUpdateList(logicGroup);
        logicGroup.AddSystemToUpdateList(system);
        logicGroup.AddSystemToUpdateList(s2);
        logicGroup.AddSystemToUpdateList(s1);
        logicGroup.SortSystems();
        simulationSystem.SortSystems();
        ScriptBehaviourUpdateOrder.UpdatePlayerLoop(logicWorld,PlayerLoop.GetCurrentPlayerLoop());

        //方法2：
        DefaultWorldInitialization.AddSystemsToRootLevelSystemGroups(renderWorld, typeof(RenderUpdateGroup),typeof(LogicUpdateGroup),typeof(System2WithLogicGroupType),typeof(System3WithLogicGroupType));

        ScriptBehaviourUpdateOrder.UpdatePlayerLoop(renderWorld, PlayerLoop.GetCurrentPlayerLoop());
~~~


## 排序
> 针对System的运行先后顺序创建的一个顺序列表，只有在相同的Group组中才会有先后的执行顺序，且顺序可以设定，跨Group组的顺序要先看Group组之间的顺序规则。

> 可以在Window/Analysis/Entity Debugger 中看到具体的分组
### Ecs提供的三个Group组
1. InitializationSystemGroup 分类在初始化的分组中
2. SimulationSystemGroup  分类在逻辑运算的分组中
3. PresentationSystemGroup 分类在渲染的逻辑分组中
4. 继承与ComponentSystemGroup的自己创建的分组，此分组如果不添加属性会自动添加到SimulationSystemGroup 分组中
### Group组的属性方法
1. UpdateInGroup 设定工作的分组
2. UpdateAfter 设定当前的System在那个System之后运行
3. UpdateBefore 设定当前的System在那个System之前运行
4. DisableAutoCreation 设定不会自动添加到所有的组，需要手动添加

