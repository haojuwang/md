# java nio





## Nio新增功能

1,进行异步IO操作的缓冲区ByteBuffer等。

2，进行异步IO操作的管道Pipe。

3，进行各种IO操作（异步或者同步）的channel,包括ServerSocketChannel和SocketChannel。

4，多种字符集的编码能力和解码能力；

5，实现非阻塞IO操作的多路复用器selector.

6,基于流行的Perl实现的正则表达式类库。

7，文件通道FileChannel.



## 没有解决的问题

1，没有统一的文件属性（例如读写权限）.

2,API能力较弱，例如目录的级联创建和递归遍历，往往需要自己实现；

3，底层存储系统的一些高级API无法使用。

4，所有的文件操作都是同步阻塞调用，不支持异步文件读写操作。



## 传统的Bio

1，网络编程的基本模型是Client/Server 模型，也是两个进程之间进行相互通信，其中服务端提供位置信息（绑定的IP 地址和监听端口）,客户端通过连接操作向服务器端监听的地址发起连接请求，通过三次握手建立连接，如果连接建立成功,双方就可以通过网络套接字(Socket)进行通信。

2，在基于传统同步阻塞模型开发中，ServerSocket 负责绑定IP地址，启动监听端口，Socket负责发起连接操作。连接成功之后，双方通过输入输出流进行同步阻塞通信。



采用BIO通信模型的服务端，通常由一个独立的Acceptor线程负责监听客户端的链接，他接收到客户端链接请求之后为每个客户端创建一个新的线程进行链路处理，处理完成之后，通过输出流返回应答给客户端，线程销毁，这就是典型的一请求一应答的通信模型。



![BIO执行流程](../images/BIO执行流程.png)



​	该模型最大的问题就是缺乏弹性伸缩能力，当客户端并发访问量增加后，服务端的线程个数和客户端并发访问数呈1：1的正比关系，由于线程是Java虚拟机非常宝贵的线程资源，当线程数膨胀之后，系统的性能将急剧下降，随着并发量的继续增大，系统会发生线程堆栈溢出，创建新线程失败等问题，并最终导致进程宕机或者僵死，不能对外提供服务。



## NIO 编程的优点

- 客户端发起的连接操作是异步的，可以通过在多路复用器注册OP_CONNECT等待后续结果，不需要像之前的客户端那样被同步阻塞。
- SocketChannel的后续读写操作都是异步，如果没有课读写的数据他不会同步等待，直接返回，这样IO通信线程就可以处理其他的链路，不需要同步等待这个链路可用。
- 线程模型的优化：由于Jdk的Selector在linux等主流操作系统上通过epoll实现，他没有连接句柄数的限制，这意味着一个Selector线程可以同时处理成千上万个客户端连接，而且性能不会随着客户端的增加而线性下降。因此，他非常适合做高性能，高负载的网络服务器。






##缓冲区Buffer

```
Buffer 是一个对象，它包含一些要写入或者要读的数据。在NIO类库中加入Buffer 对象，体现了新库与IO的一个重要区别。


在NIO 库中，所有数据都是用缓冲区处理的。写入写出都要经过缓冲区。

缓冲区实质上是一个数组。通常它是一个字节数组(ByteBuffer),也可以使用其它种类的数组，但是一个缓冲区不仅仅是一个数组，缓冲区提供了对数据结构化访问以及维护读写位置(limit)等信息。
```



### 缓冲区种类

* ByteBuffer:字节缓冲区。
* CharBuffer:字符缓冲区。
* ShortBuffer:短整形缓冲区。
* IntBuffer:整形缓冲区。
* LongBuffer:长整型缓冲区。
* FloatBuffer:浮点缓冲区。
* DoubleBuffer:双精度浮点型缓冲区。

![Buffer继承关系图](../images/Buffer继承关系图.png)





## 通道Channel

```
Channel 是一个通道，他就像来自水管一样，网络通过Channel 读取和写入。

通道与流不同之处在于通道是双向的，流只是在一个方向上移动(一个流必须是InputStream 或者OutputStream的子类)，而通道可以读，写或者二者同时进行。

因为Channel 是全双工的，所以他可以比流更好地映射底层操作系统的Api。特别是Unix网络编程模型中，底层操作系统的通道都是全双工的，同时支持读写操作。
```

![Channel 继承图](../images/Channel 继承图.png)





## 多路复用器Selector

```
多路复用器提供选择已经就绪的任务能力。

Selector 会不断地轮询注册在其上的Channel,如果某个Channel上面发生读或者写事件，这个Channel就处于就绪状态，会被Selector轮询出来，然后通过SelectionKey 可以获取就绪Channel的集合，进行后续的IO操作。

一个多路复用器Selector可以同时轮询多个Channel,这也就意味着只需要一个线程负责Selector的轮询，就可以接入成千上万的客户端，这确实是个非常巨大的进步.
```







## NIO服务器端序列图

![NIO服务器端序列图](../images/NIO服务器端序列图.png)



* 步骤一：打开ServerSocketChannel ,用于监听客户端的连接，它是所有客户端连接的父通道。

```java
ServerSocketChannel acceptorSvr = ServerSocketChannel.open();

```




* 步骤二：绑定监听端口，设置连接为非阻塞模式

```java
acceptorSvr.socket().bind(new InetSocketAddress(InetAddress.getByName(“IP”),port));

acceptorSvr.configureBlocking(false) ; 
```



* 步骤三： 创建Reactor线程，创建多路复用器并启动线程

  ```
  Selector selector = Selector.open();
  new Thread(new ReactorTask()).start();
  ```

  ​

* 步骤四：将SeverSocketChannel注册到Reactor线程的多路复用器Selector上，监听ACCEPT事件

  ```java
  SelectionKey key = acceptorSvr.register(selector,SelectionKey.OP_ACCEPT,ioHandler)
  ```



* 步骤五：多路复用器在线程run方法的无线循环体内轮询准备就绪的key

  ```java
  int num = selector.select();
  Set selectedKeys = selector.selectedKeys();
  Iterator it = selectedKeys.iterator();
  while(it.hasNext()){
    SelectionKey key = (SelectionKey)id.next();
    /**
    * deal with IO event
    **/
  }
  ```

  ​

* 步骤六：多路复用器监听到有新的客户端接入，处理新的接入请求，完成TCP三次握手，建立物理链路

  ```
  SocketChannel channel = svrChannel.accept();
  ```

  ​

* 步骤七： 设置客户端链路为非阻塞模式

  ```Java
  channel.configureBlocking(false);
  channel.socket().setReuseAddress(true)
  ```

  ​

* 步骤八：将新接入的客户端连接注册到Reactor线程的多路复用器上，监听读操作，读取客户端发送的网络消息

  ```Java
  SelectionKey key = socketChannel.register(selector,SelectionKey.OP_READ,ioHandler);
  ```

  ​

* 步骤九：异步读取客户端请求消息到缓冲区

  ```java
  int readNumber = channel.read(recivedBuffer);
  ```

  ​

* 步骤十：对ByteBuffer进行编解码，如果有半包消息指针reset，继续读取后续的报文，将解码成功的消息封装成Task，投递到业务线程池中，进行业务逻辑编排

  ```java
  Object message = null;
  while(buffer.hasRemain) {
    byteBuffer.mark();
    Object message = decode(byteBuffer);
    if(message == null) {
      byteBuffer.reset();
      break;
    }
    messageList.add(message)
    
  }
  if(!byteBuffer.hasRmain()) {
      byteBuffer.clear();
  } else {
      byteBuffer.compact();
    
  }

  if(messageList != null & !messageList.isEmpty()) {
      for(Object messageE : messageList){
          handlerTask(messageE);
      }
  }
  ```



* 步骤十一： 将POJO对象encode 成ByteBuffer,调用SocketChannel的异步write 接口，将消息异步发送给客户端

  ```java
  socketChannel.write(buffer);
  ```

* 注意：如果发送区TCP 缓冲区满，会导致写半包，此时，需要注册监听写操作位，循环写，直到整包消息写入TCP缓冲区




#### nio 实例



```
public class TimeServer {
	public static void main(String[] args) {
		 int port = 8899;
		  ServerSocket server = null;
		   try {
        server = new ServerSocket(port);
        System.out.println("this time server is start in port: " + port);
        Socket socket = null;
        int i =0;
        while (true) {
            socket = server.accept();
            i++;
            Thread thread =  new Thread(new TimeServerHandler(socket));
            thread.setName(i+" ");
            thread.start();
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (server != null) {
            System.out.println("The time server close");
            try {
                server.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            server = null;
        }
    }
}
}
```



handler

```java
public class TimeServerHandler implements Runnable {

    private Socket socket;

    public TimeServerHandler(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        BufferedReader in = null;
        PrintWriter out = null;

        try {

            in = new BufferedReader(new InputStreamReader(this.socket.getInputStream()));
            out = new PrintWriter(this.socket.getOutputStream(), true);
            String currentTime = null;
            String body = null;
            while (true) {
                body = in.readLine();

                if (body == null) {
                    System.out.println("body === null");

                    break;


                }
                //耗时
                for(int i=0;i<100000;i++) {

                }
                System.out.println("The time server receive order: "+Thread.currentThread().getName() + body);
                currentTime = "query time order".equalsIgnoreCase(body) ? new Date(System.currentTimeMillis()).toString() : "BAD ORDER";

                out.println(currentTime);
            }


        } catch (Exception e) {
            e.printStackTrace();
            if (in != null) {
                try {
                    in.close();
                } catch (IOException e1) {
                    e1.printStackTrace();
                }
            }

            if (out != null) {
                out.close();
                out = null;
            }

            if (this.socket != null) {
                try {
                    this.socket.close();
                } catch (IOException e1) {
                    e1.printStackTrace();
                }

                this.socket = null;
            }
        }


    }
}
```



client

```java
public class TimeClient {
    public static void main(String[] args) {
        int port = 8899;
        Socket socket = null;
        BufferedReader in = null;
        PrintWriter out = null;
        try {

            for (int i = 0; i < 10000; i++) {
                socket = new Socket("127.0.0.1", port);
                in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                out = new PrintWriter(socket.getOutputStream(), true);
                out.println("query time order");
//                out.println();


                System.out.println("send order 2 server succeed.");
                String resp = in.readLine();
                System.out.println("Now is: " + i + "   " + resp);


            }


        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (out != null) {
                out.close();
                out = null;
            }

            if (in != null) {
                try {
                    in.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

                socket = null;
            }
        }
    }
}
```







