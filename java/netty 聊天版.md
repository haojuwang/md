## netty 聊天版

实现以下功能：

* 1，一个客户端上线/下线，通知所有的用户。

* 2，一个客户端发送消息，广播所有的客户端。


#### server端代码

* 1，Server.java

```
public class Server {

    public static void main(String... arg) throws Exception {

        //负责接收客户端连接
        NioEventLoopGroup boss = new NioEventLoopGroup();

        //负责处理客户端连接
        NioEventLoopGroup workerGroup = new NioEventLoopGroup();


        try {
            ServerBootstrap bootstrap = new ServerBootstrap();
            bootstrap.group(boss, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .handler(new LoggingHandler(LogLevel.INFO))
                    .childHandler(new ServerChatinitlizer());

            //绑定端口号
            ChannelFuture channelFuture = bootstrap.bind(8899).sync();
            channelFuture.channel().closeFuture().sync();

        } finally {
            boss.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }

    }
}

``` 


* 2,ServerChatinitlizer.java

```
public class ServerChatinitlizer extends ChannelInitializer<SocketChannel> {

    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();

        //编码解码器

        // 换行分割解码器 \r\n
        pipeline.addLast(new DelimiterBasedFrameDecoder(4096, Delimiters.lineDelimiter()));

        pipeline.addLast(new StringDecoder(CharsetUtil.UTF_8));
        pipeline.addLast(new StringEncoder(CharsetUtil.UTF_8));

        //自己的处理器
        pipeline.addLast(new ServerChatHandler());


    }
}

```

* 3,ServerChatHandler.java

```
/**
 * 客户端上线/下线 提示所有的人
 * 发送消息发给所有的人
 */
public class ServerChatHandler extends SimpleChannelInboundHandler<String> {

    //保存已建立所用用户的实例
    private static ChannelGroup channelGroup = new DefaultChannelGroup(GlobalEventExecutor.INSTANCE);


    //接收客户端发送的消息
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, String msg) throws Exception {
        Channel channel = ctx.channel();

        channelGroup.forEach(ch -> {
            if (ch == channel) {  //表示自己
                channel.writeAndFlush("[自己]  "  + msg + "\n");
            } else {
                ch.writeAndFlush(ch.remoteAddress() + "  发送的消息  " + msg + "\n");
            }
        });

    }

    //客户端与服务器端建立好连接
    @Override
    public void handlerAdded(ChannelHandlerContext ctx) throws Exception {
        System.out.println("handlerAdded");
        Channel channel = ctx.channel();
        //广播已连接的客户端 有新的用户上线
        channelGroup.writeAndFlush("[服务器]-" + channel.remoteAddress() +" 上线\n");
        // 保存用户实例
        channelGroup.add(channel);

    }

    //链接断开
    @Override
    public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {

        System.out.println("handlerRemoved");
        Channel channel = ctx.channel();
        //广播已连接的客户端 用户下线
        channelGroup.writeAndFlush("[服务器]-" + channel.remoteAddress() + " 下线\n");

        //channelGroup 会自动 剔除已断开的用户连接

    }


    //处于活动状态
    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        Channel channel = ctx.channel();
        System.out.println("[服务器]-" + channel.remoteAddress() +" 上线\n");
    }


    //下线
    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        super.channelInactive(ctx);
        Channel channel = ctx.channel();
        System.out.println("[服务器]-" + channel.remoteAddress() +  " 下线\n");
    }


    // 发生异常关闭连接
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace();
        ctx.close();
    }
}


```


#### client端代码

* 1,Client.java

```
public class Client {
    public static void main(String... arg) throws Exception {
        EventLoopGroup eventLoopGroup = new NioEventLoopGroup();

        try {

            Bootstrap bootstrap = new Bootstrap();
            bootstrap.group(eventLoopGroup).channel(NioSocketChannel.class)
                    .handler(new ChatInitializer());

           Channel channel = bootstrap.connect("127.0.0.1",8899).sync().channel();

            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
            while (true){
                channel.writeAndFlush(br.readLine()+"\r\n");
            }

        } finally {
            eventLoopGroup.shutdownGracefully();
        }

    }
}

```

* 2, ChatInitializer.java

```
public class ChatInitializer extends ChannelInitializer<SocketChannel> {

    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();

        pipeline.addLast(new DelimiterBasedFrameDecoder(4096, Delimiters.lineDelimiter()));
        pipeline.addLast(new StringEncoder(CharsetUtil.UTF_8));
        pipeline.addLast(new StringDecoder(CharsetUtil.UTF_8));

        pipeline.addLast(new ChatHandler());

    }
}

```

* 3, ChatHandler

```
public class ChatHandler extends SimpleChannelInboundHandler<String> {
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, String msg) throws Exception {

        System.out.println(" 接收到数据 " + msg);

    }
}

```























