# 向量算法
## 1. 点乘 Dot
1. 返回值：float
2. 计算：a·b=|a|·|b|cos<a,b>
3. 当<0 : 90°-180°的范围 <br> >0: 0°-90°<br> =0: 90°

## 2. 叉乘 Cross
1. 返回值：Vector 
2. 计算： c =a x b
3. 叉乘返回值为两个向量的法向量
4. 右手法则：axb ≠ bxa ,axb = – bxa
5. 可以使用叉乘计算旋转方向：法向量平行轴的值>0：顺时针，<0 ：逆时针