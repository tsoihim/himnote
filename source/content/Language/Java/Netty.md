> Netty is an NIO client server framework which enables quick and easy development of network applications such as protocol servers and clients. It greatly simplifies and streamlines network programming such as TCP and UDP socket server.

### Netty란?
- 자바 네트워크 어플레케이션 프레임워크, TCP/UDP 서버 코드 작성을 용이하게 해줌

### Netty 코드 구성
1. Netty I/O Event Handler
    - ChannelInboundHandler는 다양한 handlerMethod를 오버라이드해 사용할 수 있도록 지원
        - 아래 예제의 ChannelInboundHandlerAdapter는 ChannelInboundHandler의 구현체
        - 커스텀이 필요한 경우 직접 핸들러 구현하여 사용하면 됨
    - channelRead 메소드를 구현하면 클라이언트로부터 온 메시지 수신 이벤트가 발생할 때 호출됨
        - 아래와 같이 ByteBuf로 수신하면 참조카운트 객체이므로 이 경우 핸들러가 release할 의무가 있음
        - 따라서 보통 아래와 같이 사용하기도 함
        ```java
        @Override
        public void channelRead(ChannelHandlerContext ctx, Object msg) {
            try {
                // Do something with msg
            } finally {
                ReferenceCountUtil.release(msg);
            }
        }
        ```
    - exceptionCaught 메소드는 I/O 에러 등 Netty 이벤트 처리서 exception 발생 시 Throwable과 호출
        - 대부분의 경우에 로그를 찍는 것이 좋음
    - DISCARD 서버 핸들러 예제
    ```java
    package io.netty.example.discard;
    
    import io.netty.buffer.ByteBuf;
    
    import io.netty.channel.ChannelHandlerContext;
    import io.netty.channel.ChannelInboundHandlerAdapter;
    
    /**
     * Handles a server-side channel.
     */
    public class DiscardServerHandler extends ChannelInboundHandlerAdapter { // (1)
    
        @Override
        public void channelRead(ChannelHandlerContext ctx, Object msg) { // (2)
            // Discard the received data silently.
            ((ByteBuf) msg).release(); // (3)
        }
    
        @Override
        public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) { // (4)
            // Close the connection when an exception is raised.
            cause.printStackTrace();
            ctx.close();
        }
    }
    ```
    
2. Netty I/O Server (Multiplexer)
    - NioEventLoopGroup은 I/O operation을 핸들링하는 멀티스레드 이벤트 루프
        - EpollEventLoopGroup 혹은 클라이언트용 EventLoopGroup 등 다양한 구현체 지원
    - 서버 프로그램 작성 시 듣기 식별자용 bossGroup을 만들고 연결 식별자용 workerGroup을 생성
        - 얼마나 많은 스레드가 사용되고 생성된 채널에 매핑될지는 EventLoopGroup 구현에 의존하며 생성자에서 설정 가능할 수 있음
    - ServerBootstrap는 서버 셋업 도와주는 helper 클래스
        - Channel 이용하여 바로 서버 설정해도 되지만 대부분 이렇게는 안함 (불편)
        - 아래 예제에서는 NioServerSocketChannel 클래스 이용하여 새로운 채널 init하는 식으로 구현
    - 새로이 연결된 Channel에 의해 아래 ServerBootstrap의 handler가 수행됨
        - ChannelInitializer 클래스는 Channel init 시 설정할 게 있을 경우 사용 가능            
        - 아래와 같이 특정 핸들러(DiscardServerHandler)로 서버 앱 구현하기 위해 새로이 연결된 Channel의 ChannelPipeline을 설정하는 경우가 일반적
	        - **ChannelPipeline** - ChannelHandler의 리스트로 Channel의 in/outound 이벤트를 핸들링하거나 인터셉트
        - Channel 구현체 자체의 파라미터 또한 세팅 가능 (tcpNoDelay, keepAlice 등)
            - ChannelOption, ChannelConfig 쪽 API 참고
            - Nagle 등 TCP/IP 관련 지식 필요. 경우에 따라 유연하게 설정
            - option과 childOption 차이는 옵션을 듣기, 연결 채널 둘 중 어느 곳에 적용할지의 차이
    - DISCARD 서버 부트스트랩 예제
    ```java
    package io.netty.example.discard;
        
    import io.netty.bootstrap.ServerBootstrap;
    
    import io.netty.channel.ChannelFuture;
    import io.netty.channel.ChannelInitializer;
    import io.netty.channel.ChannelOption;
    import io.netty.channel.EventLoopGroup;
    import io.netty.channel.nio.NioEventLoopGroup;
    import io.netty.channel.socket.SocketChannel;
    import io.netty.channel.socket.nio.NioServerSocketChannel;
        
    /**
     * Discards any incoming data.
     */
    public class DiscardServer {
        
        private int port;
        
        public DiscardServer(int port) {
            this.port = port;
        }
        
        public void run() throws Exception {
            EventLoopGroup bossGroup = new NioEventLoopGroup(); // (1)
            EventLoopGroup workerGroup = new NioEventLoopGroup();
            try {
                ServerBootstrap b = new ServerBootstrap(); // (2)
                b.group(bossGroup, workerGroup)
                 .channel(NioServerSocketChannel.class) // (3)
                 .childHandler(new ChannelInitializer<SocketChannel>() { // (4)
                     @Override
                     public void initChannel(SocketChannel ch) throws Exception {
                         ch.pipeline().addLast(new DiscardServerHandler());
                     }
                 })
                 .option(ChannelOption.SO_BACKLOG, 128)          // (5)
                 .childOption(ChannelOption.SO_KEEPALIVE, true); // (6)
        
                // Bind and start to accept incoming connections.
                ChannelFuture f = b.bind(port).sync(); // (7)
        
                // Wait until the server socket is closed.
                // In this example, this does not happen, but you can do that to gracefully
                // shut down your server.
                f.channel().closeFuture().sync();
            } finally {
                workerGroup.shutdownGracefully();
                bossGroup.shutdownGracefully();
            }
        }
        
        public static void main(String[] args) throws Exception {
            int port = 8080;
            if (args.length > 0) {
                port = Integer.parseInt(args[0]);
            }
    
            new DiscardServer(port).run();
        }
    }
    ```