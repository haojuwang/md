## netty 基本使用。
#### netty 概念
	netty 是一个基于NIO的客户端服务器框架，它可以快速并且很容易的开发网络应用程序，
	如客户端服务器协议。netty 大大简化了网络编程，如TCP和UDP socket服务。

#### netty 使用场景
* 1，可以作为rpc的通信的框架远程过程的调用。
* 2，netty可以作为长连接的服务器基于websoket，实现客户端与服务器端长连接。
* 3，netty 可以作为http服务器，类似jetty,tomcat。

#### netty 作为http 服务器 hello world
	netty 学习曲线很陡峭，看官方的例子一头雾水。
	https://netty.io/

gradle 引入jar。
```
	compile 'io.netty:netty-all:4.1.13.Final'
```	
** 实现hello world 具体代码 **
HelloServer.java  主程序
HelloServerlInitializer.java   事件 channel
HelloServerHandler.java 具体处理类

```
public class HelloServer {
    public static void main(String... arg) throws Exception {

        //负责接收客户端连接
        EventLoopGroup boosGroup = new NioEventLoopGroup();
        //处理连接
        EventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            ServerBootstrap bootstrap = new ServerBootstrap();

            bootstrap.group(boosGroup,workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .childHandler(new HelloServerlInitializer());

            //绑定端口号
            ChannelFuture channelFuture = bootstrap.bind(9999).sync();
            channelFuture.channel().closeFuture().sync();

        } finally {
            boosGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();

        }


    }
}

```

----------------------------

```
public class HelloServerlInitializer extends ChannelInitializer<SocketChannel> {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline channelPipeline = ch.pipeline();

        //负载http 请求编码解码
        channelPipeline.addLast(new HttpServerCodec());

        //实际处理请求
        channelPipeline.addLast(new HelloServerHandler());

    }
}
	

```

----------------------------
```
public class HelloServerHandler extends SimpleChannelInboundHandler<HttpObject> {


    @Override
    protected void channelRead0(ChannelHandlerContext ctx, HttpObject msg) throws Exception {

//        System.out.println("接收到请求：  "+msg);
        if(msg instanceof HttpRequest) {
            HttpRequest msgs = (HttpRequest) msg;

            System.out.println("接收到请求：  "+msgs.method());

            //设置返回内容
            ByteBuf content = Unpooled.copiedBuffer("Hello World\n", CharsetUtil.UTF_8);

            //创建响应
            FullHttpResponse response = new DefaultFullHttpResponse(HttpVersion.HTTP_1_1,HttpResponseStatus.OK,content);

            response.headers().set(HttpHeaderNames.CONTENT_TYPE,"text/plain");
            response.headers().set(HttpHeaderNames.CONTENT_LENGTH,content.readableBytes());

            ctx.writeAndFlush(response);

        }

    }
    
}

```

#### 请求方式
curl -X POST 'http://localhost:9999'
curl  'http://localhost:9999'
或者在浏览器中请求
http://localhost:9999
























