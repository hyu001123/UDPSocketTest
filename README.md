# UDPSocketTest
以UDP的形式进行Socket连接，熟悉并总结tcp、UDP的不同及优缺点
Netty创建步骤：

NIO通讯服务端步骤：
1、创建ServerSocketChannel,为它配置非阻塞模式
2、绑定监听，配置TCP参数，录入backlog大小等
3、创建一个独立的IO线程，用于轮询多路复用器Selector
4、创建Selector，将之前的ServerSocketChannel注册到Selector上，并设置监听标识位SelectionKey.ACCEPT
5、启动IO线程，在循环体中执行Selector.select()方法，轮询就绪的通道
6、当轮询到处于就绪的通道时，需要进行判断操作位，如果是ACCEPT状态，说明是新的客户端介入，则调用accept方法接受新的客户端。
7、设置新接入客户端的一些参数，并将其通道继续注册到Selector之中。设置监听标识等
8、如果轮询的通道操作位是READ，则进行读取，构造Buffer对象等
9、更细节的还有数据没发送完成继续发送的问题


Netty实现通讯的步骤：
1、创建两个NIO线程组，一个专门用来网络事件处理（接受客户端连接），另一个则进行网络通讯读写
2、创建一个ServerBootstrap对象，配置Netty的一系列参数，例如接受传入数据的缓存大小等。
3、创建一个实际处理数据的类ChannelInitializer,进行初始化的准备工作，比如设置传入数据的字符集，格式，实现实际处理数据的接口。
4、绑定端口，执行同步阻塞方法等待服务器启动即可。

当对于NIO模型，netty简单、健壮、性能稳定，而且这几步都是模板式开发，以后可以直接用，开发只需专注实际处理数据类的实现。
高性能NIO框架Netty-整合kryo高性能数据传输
高性能NIO框架Netty-整合Protobuf高性能数据传输
netty还可实现对断线重连及心跳包的接口，调用方便，便于开发。

1、TCP与UDP区别总结：
1、TCP面向连接（如打电话要先拨号建立连接）;UDP是无连接的，即发送数据之前不需要建立连接
2、TCP提供可靠的服务。也就是说，通过TCP连接传送的数据，无差错，不丢失，不重复，且按序到达;UDP尽最大努力交付，即不保证可靠交付Tcp通过校验和，重传控制，
序号标识，滑动窗口、确认应答实现可靠传输。如丢包时的重发控制，还可以对次序乱掉的分包进行顺序控制。
3、UDP具有较好的实时性，工作效率比TCP高，适用于对高速传输和实时性有较高的通信或广播通信。
4.每一条TCP连接只能是点到点的;UDP支持一对一，一对多，多对一和多对多的交互通信
5、TCP对系统资源要求较多，UDP对系统资源要求较少。TCP协议中会包含相关Socket的信息，比如我们可以通过Socket知道收发对方的IP及端口号，而采用UDP协议它只
负责传输需发送的信息内容，不会包含双方的端口信息。

2、为什么UDP有时比TCP更有优势?
UDP以其简单、传输快的优势，在越来越多场景下取代了TCP,如实时游戏。
（1）网速的提升给UDP的稳定性提供可靠网络保障，丢包率很低，如果使用应用层重传，能够确保传输的可靠性。
（2）TCP为了实现网络通信的可靠性，使用了复杂的拥塞控制算法，建立了繁琐的握手过程，由于TCP内置的系统协议栈中，极难对其进行改进。
采用TCP，一旦发生丢包，TCP会将后续的包缓存起来，等前面的包重传并接收到后再继续发送，延时会越来越大，基于UDP对实时性要求较为严格的情况下，采用自定义重
传机制，能够把丢包产生的延迟降到最低，尽量减少网络问题对游戏性造成影响。


Android之Socket的基于UDP传输
接收方创建步骤：
1.  创建一个DatagramSocket对象，并指定监听的端口号
DatagramSocket socket = new  DatagramSocket (4567);
2. 创建一个byte数组用于接收
byte data[] = new byte[1024];
3. 创建一个空的DatagramPackage对象
 DatagramPackage package = new DatagramPackage(data , data.length);
4. 使用receive方法接收发送方所发送的数据,同时这也是一个阻塞的方法
socket.receive(package); 
5. 得到发送过来的数据
new String(package.getData() , package.getOffset() , package.getLength());

发送方创建步骤：
1.  创建一个DatagramSocket对象
DatagramSocket socket = new  DatagramSocket (4567);
2.  创建一个 InetAddress ， 相当于是地址
InetAddress serverAddress = InetAddress.getByName("想要发送到的那个IP地址"); 
3. 这是随意发送一个数据
String str = "hello";
4.  转为byte类型
byte data[] = str.getBytes();
5.  创建一个DatagramPacket 对象，并指定要讲这个数据包发送到网络当中的哪个地址，以及端口号
DatagramPacket  package = new DatagramPacket (data , data.length , serverAddress , 4567);
6.  调用DatagramSocket对象的send方法 发送数据
 socket . send(package);
