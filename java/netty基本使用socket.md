## netty基本使用- socket通信
* 1，netty 大大简化了socket开发，提高了socket的性能。
* 2，Netty提供异步的、事件驱动的网络应用程序框架和工具，
  用以快速开发高性能、高可靠性的网络服务器和客户端程序。

#### 系列文章
[netty 基本使用- 作为http服务器][gcssloop]{:target="_blank"}
[gcssloop]: http://www.jianshu.com/p/cd88723c96dc

#### 服务器端代码
**ServerSocket.java**
```
	public class ServerSocket {
    public static void main(String ...arg) throws Exception {

        //负责接收客户端连接
        NioEventLoopGroup bossGroup = new NioEventLoopGroup();

        //负责处理连接
        NioEventLoopGroup wokerGroup = new NioEventLoopGroup();

        try{

            ServerBootstrap bootstrap = new ServerBootstrap();

            bootstrap.group(bossGroup,wokerGroup)
                    .channel(NioServerSocketChannel.class)
                    .childHandler(new ServerInitializer());

            //绑定端口号
            ChannelFuture channelFuture = bootstrap.bind(9999).sync();
            channelFuture.channel().closeFuture().sync();

        } finally {
            bossGroup.shutdownGracefully();
            wokerGroup.shutdownGracefully();
        }

    }
}

```

** ServerInitializer.java **
```
public class ServerInitializer extends ChannelInitializer<SocketChannel> {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
      ChannelPipeline pipeline = ch.pipeline();

      //数据分包，组包，粘包
      pipeline.addLast(new LengthFieldBasedFrameDecoder(Integer.MAX_VALUE,0,4,0,4));
      pipeline.addLast(new LengthFieldPrepender(4));

      pipeline.addLast(new StringDecoder(CharsetUtil.UTF_8));
      pipeline.addLast(new StringEncoder(CharsetUtil.UTF_8));

      pipeline.addLast(new ServerHandler());


    }
}
```
** ServerHandler.java 处理业务 **
```
	public class ServerHandler extends SimpleChannelInboundHandler<String> {

    	@Override
    	protected void channelRead0(ChannelHandlerContext ctx, String msg) throws 	Exception {
    	    //接收到的数据
    	    System.out.println(ctx.channel().remoteAddress()+" , "+msg);
	
    	    //返回给客户端的数据
    	    ctx.channel().writeAndFlush("server: "+ UUID.randomUUID());
    	}
	
    	@Override
    	public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws 	Exception {
    	    cause.printStackTrace();
    	    ctx.close();
    	}
	}

```

#### 客户端端代码
** ClientSocket.java **
```
	public class ClientSocket {
    public static void main(String[] arg) throws Exception {
        NioEventLoopGroup eventLoopGroup = new NioEventLoopGroup();

        try {
            Bootstrap bootstrap = new Bootstrap();
            bootstrap.group(eventLoopGroup).channel(NioSocketChannel.class)
                    .handler(new Clientinitializer());

            ChannelFuture channelFuture = bootstrap.connect("localhost", 9999).sync();
            channelFuture.channel().closeFuture().sync();

        } finally {
            eventLoopGroup.shutdownGracefully();
        }
    }
}
```

** Clientinitializer.java **
```
	public class Clientinitializer extends ChannelInitializer<SocketChannel> {

    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();

        //数据分包，组包，粘包
        pipeline.addLast(new LengthFieldBasedFrameDecoder(Integer.MAX_VALUE,0,4,0,4));
        pipeline.addLast(new LengthFieldPrepender(4));

        pipeline.addLast(new StringDecoder(CharsetUtil.UTF_8));
        pipeline.addLast(new StringEncoder(CharsetUtil.UTF_8));

        pipeline.addLast(new ClientHandler());

    }
}

```

**ClientHandler.java 处理业务 **
```
public class ClientHandler extends SimpleChannelInboundHandler<String> {

    //接收服务端数据&发送数据
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, String msg) throws Exception {

        System.out.println("客户端接收到的消息： "+msg);

        ctx.writeAndFlush(LocalDateTime.now());

        //完成通信后关闭连接
        //ctx.close();
    }

    //和服务器建立连接
    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
      ctx.writeAndFlush("在吗！！！！");
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace();
        ctx.close();
    }
}
```


** 可以多次和服务器端通信的写法 **
```
	public class ClientSocket2 {
    public static void main(String[] arg) throws Exception {
        NioEventLoopGroup eventLoopGroup = new NioEventLoopGroup();

        try {
            Bootstrap bootstrap = new Bootstrap();
            bootstrap.group(eventLoopGroup).channel(NioSocketChannel.class)
                    .handler(new Clientinitializer());

            ChannelFuture channelFuture = bootstrap.connect("localhost", 9999).sync();
            Channel channel = channelFuture.channel();

            //接收输入的数据
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in, CharsetUtil.UTF_8));
            while (true) {
                String sendMsg = bufferedReader.readLine() ;
                if ("esc".equals(sendMsg)) {
                    channel.close();
                    break;
                }

                sendMsg += "\r\n";
                channel.writeAndFlush(sendMsg);
            }


        } finally {
            eventLoopGroup.shutdownGracefully();
        }
    }
}


```

#### LengthFieldBasedFrameDecoder
  netty 常用的处理大数据分包传输问题的解决类。

* maxFrameLength：解码的帧的最大长度。
* lengthFieldOffset： 长度属性的起始位（偏移位），包中存放有整个大数据包长度的字节，这段字节的其实位置。
* lengthFieldLength：长度属性的长度，即存放整个大数据包长度的字节所占的长度。
* lengthAdjustmen：长度调节值，在总长被定义为包含包头长度时，修正信息长度。
* initialBytesToStrip：跳过的字节数，根据需要我们跳过lengthFieldLength个字节，以便接收端直接接受到不含“长度属性”的内容。

#### LengthFieldPrepender 编码类 
编码类，自动将

+----------------+

| "HELLO, WORLD" |

+----------------+

格式的数据转换成

+--------+----------------+

 + 0x000C | "HELLO, WORLD" |

 +--------+----------------+

格式的数据 

####参考文章
[netty 数据分包、组包、粘包处理机制][123]{:target="_blank"}
[123]: http://blog.163.com/linfenliang@126/blog/static/127857195201210821145721/



