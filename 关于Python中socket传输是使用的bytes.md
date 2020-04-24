# 关于Python中socket传输中使用的bytes对象

## bytes对象的索引和切片结果不一样

bytes 对象是由整数构成的序列（类似于元组），因此对于一个 bytes 对象 b，b[0] 将为一个整数，而 b[0:1] 将为一个长度为 1 的 bytes 对象。 （这与文本字符串不同，索引和切片所产生的将都是一个长度为 1 的字符串）。

```python
# 例子
test_bytes = b'message'
test_bytes[-1]  # 会得到整数101
test_bytes[-1:] # 会得到bytes b"e"
```



## socket传输一般会采用三种常用方式:

1. 固定包长的数据包

   ```python
   # send
   # 每次发送的消息字节长度是固定的
   sock.send((10).to_bytes(1, 'little'))
   
   # recive
   def recvall(sock, count):
           """
           接收函数
           固定消息长度的传输方式
   
           参数
               sock  socket对象
               count 已知socket传输消息字节数
           """
           buf = b''#buf是一个byte类型
           while count:
               #这里的recv的参数count,表示一次性从缓冲区读取count字节的bytes数据
               newbuf = sock.recv(count)
               if not newbuf: return None
               buf += newbuf
               count -= len(newbuf)
           return buf
       
   # 接收1个字节的数据,字节的长度是已知并且固定的
   recive_msg = recvall(sock, 1)
   ```

   

2. 以指定字符（串）为包的结束标志

   ```python
   # send
   # 每次发送的消息后面加上约定好的终止符
   # 每次发送的消息字节长度可以是动态的
   eof = (10).to_bytes(1, 'little') # 10对应的ascii码就是\n
   message = 'message'
   sock.send((message + '\n').encode('utf-8'))
   
   # recive
   def recive_message(sock, eof, buffer_size=1024):
           """
           接收函数
           通过设定终止符的接收方式
   
           参数
               sock  socket对象
               eof   终止符,是单个字节的bytes类型数据
               count 已知socket传输消息字节数
           """
           buf = []
           while True:
               newbuf = sock.recv(buffer_size)
               if not newbuf:
                   return None
               
               if newbuf[-1:] == eof:
                   # bytes对象的索引和切片结果不一样
                   # bytes索引得到的是该索引对应的单字节btyes转换的int对象
                   # bytes切片得到的是该切片对应的单字节或多字节的btyes对象
                   # 因此想要获得一个bytes末尾的单个字节的bytes,应当这样bytes_object[-1:]
                   # bytes_object[-1]会得到bytes_object[-1:]对应的int对象
                   buf.append(newbuf[0:-1])
                   break
               else:
                   buf.append(newbuf)
           return b''.join(buf)
       
   # 这种方式可以接收变长的消息,但是消息的内容中包含终止符需要对该终止符进行转意
   # 否则接收的数据会出现错误
   recive_msg = recvall(sock)
   ```

   

3. 包头 + 包体格式

   ```python
   # send
   # 发送的消息总是按照包头和包体的格式发送
   # 包头是一个固定字节长度的消息,内容是包体的字节长度
   # 因此包体的长度是动态变化的
   message = 'a long message'
   sock.send(len(message).to_bytes(4, 'little'))
   sock.send(message.encode('utf-8'))
   
   # recive
   def recvall(sock, count):
           """
           接收函数
           固定消息长度的传输方式
   
           参数
               sock  socket对象
               count 已知socket传输消息字节数
           """
           buf = b''#buf是一个byte类型
           while count:
               # 这里的recv的参数count,表示一次性从缓冲区读取count字节的bytes数据
               newbuf = sock.recv(count)
               if not newbuf: return None
               buf += newbuf
               count -= len(newbuf)
           return buf
       
   # 分两次接收数据
   # 第一次接收包头
   # 第二次接收包体
   recive_length = recvall(sock, 4)
   recive_msg = recvall(sock, recive_length)
   ```

   