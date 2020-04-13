# Python中如何将int类型转换为byte类型

1. 使用int类的to_bytes方法

   ```python
   int_data = 10
   # 一般int32型数据在其他语言中占4个字节，所以一般转换为4字节bytes
   # byteorder是大小码，而一般pc使用的是小码
   byte_data = 10.to_bytes(4, byteorder='little')
   ```

2. 如何将byte数据转换为整型数据

   ```python
   int_data = int.from_bytes(byte_data, byteorder='little')
   ```

3. 字符串encode方法也会将字符串转换为bytes数据，但是这个bytes数据是将字符串按照asic码来转换为bytes，因此是这样转换出来的数据是字符串，不是数字。如果需要将数字转换为bytes，使用to_bytes方法。