# Numpy 进行图像(矩阵)掩膜的方法

1. 使用Numpy的位元计算函数：

   ```python
   # image为原始图片
   # mask为掩膜
   # image和mask形状一样
   # 图片是int8 也就是8位int 
   # 当mask中的元素只有0000 0000(16进制为0 10进制为0) 和 1111 1111(16进制为FF 10进制为255)
   # 通过二进制位的与运算保留mask中值为255位置的像素
   np.bitwise_and(image, mask)
   ```

2. 使用Numpy的where()三目运算函数：

   ```python
   # image为原始图片
   # mask为掩膜
   # image和mask形状一样
   # 这个函数的意思是：
   # 当mask中某一元素的值为真，那么该位置的值取image相应位置的值
   # 当mask中某一元素的值为假，那么该位置的值取np.zeros_like(image)相应位置的值
   np.where(mask, image, np.zeros_like(image)相应位置的值)
   ```

3. 使用numpy元素下标选中的方法：

   ```python
   # image为原始图片
   # mask为掩膜
   # image和mask形状一样
   # 选中image中mask等于0位置的元素，并将这些元素设置为0
   # image可能是3通道的彩色图，这里可以直接等于0背后是numpy广播机制优化的结果
   # 当然可以直接 image[mask==0] = [0, 0, 0]
   image[mask==0] = 0
   ```

   

