## Go Zinx与C#实现tcp交互

### server

服务端是基于Zinx框架开发的服务器应用

```go
func main() {
	//1 创建一个server句柄
	s := znet.NewServer()

	//2 配置路由
	s.AddRouter(0, &PingRouter{})

	//3 开启服务
	s.Serve()
}
```

### Client

客户端使用C#，通过tcp/ip协议和服务端实现数据交互

```C#
public class Demo : MonoBehaviour
{
    private TcpClient tcplient;
    private Thread reThread;
    private void Start()
    {
        Connect();
        DelaySendMessage();
    }

    private void Connect()
    {
        int _port = 3653;
        string _ip = "127.0.0.1";

        IPEndPoint RemoteEndPoint = new IPEndPoint(IPAddress.Parse(_ip), _port);
        tcplient = new TcpClient(RemoteEndPoint.AddressFamily);
        tcplient.Connect(RemoteEndPoint);

        Debug.Log("连接成功!");

        //开启新的线程，不停的接收服务器发来的消息
        reThread = new Thread(Received);
        reThread.Start();

    }

    private async void DelaySendMessage()
    {
        await Task.Delay(TimeSpan.FromSeconds(1));
        //向0这个cmd发送hello
        Send(0, "hello");
        DelaySendMessage();
    }

    /// <summary>
    /// 接收服务端返回的消息
    /// </summary>
    void Received()
    {
        while (true)
        {
            NetworkStream networkStream = tcplient.GetStream();
            MUtils.Message message = ByteUtil.UnPack(networkStream);

            Debug.Log("len: " + message.Length);
            Debug.Log("cmd: " + message.Cmd);
            Debug.Log("data: " + Encoding.UTF8.GetString(message.Bytes));
        }
    }

    /// <summary>
    /// 向服务器发送消息
    /// </summary>
    private void Send(int cmd, string str)
    {
        try
        {
            string msg = str;
            byte[] message = Encoding.UTF8.GetBytes(str);
            tcplient.Client.Send(ByteUtil.Pack(cmd, message));
            Debug.Log("send mesg: " + str);
        }
        catch { }
    }
    
}
```

### Client与Server交互

因为Zinx的消息处理采用，`[MsgLength]|[MsgID]|[Data]`的封包格式，所以客户端向服务器发送数据时，与需要将数据封装成这种格式，接收服务器传来的数据按照格式解包。

```c#
public class ByteUtil
    {
        private const int HEAD_BYTES = 4; //包长字节数
        private const int CMD_BYTES = 4;  //CMD字节数

        /// <summary>
        /// 封包
        /// </summary>
        /// <param name="cmd">cmd id</param>
        /// <param name="byteArr">将要发送的byte[]</param>
        /// <returns>返回封装后的byte[]</returns>
        public static byte[] Pack(int cmd, byte[] byteArr)
        {
            int sendLength = byteArr.Length;
            var sendBuffer = new byte[HEAD_BYTES + CMD_BYTES + sendLength];

            // 4个字节的包长
            var headBuffer = BitConverter.GetBytes(sendLength);
            Buffer.BlockCopy(headBuffer, 0, sendBuffer, 0, HEAD_BYTES);

            // 4个字节的CMD
            var cmdBuffer = BitConverter.GetBytes(cmd);
            Buffer.BlockCopy(cmdBuffer, 0, sendBuffer, HEAD_BYTES, CMD_BYTES);

            // 数据体
            Buffer.BlockCopy(byteArr, 0, sendBuffer, HEAD_BYTES + CMD_BYTES, sendLength);

            return sendBuffer;
        }

        /// <summary>
        /// 解包
        /// </summary>
        /// <param name="networkStream"></param>
        public static Message UnPack(NetworkStream networkStream)
        {
            //包长
            byte[] headBuffer = new byte[HEAD_BYTES];
            networkStream.Read(headBuffer, 0, HEAD_BYTES);
            int len = BitConverter.ToInt32(headBuffer, 0);

            // 4个字节的cmd id
            byte[] cmdBuffer = new byte[CMD_BYTES];
            networkStream.Read(cmdBuffer, 0, CMD_BYTES);
            int cmd = BitConverter.ToInt32(cmdBuffer, 0);

            byte[] buffer = new byte[len];
            int total = 0;
            while (total < len)
            {
                total += networkStream.Read(buffer, 0, len);
            }

            Message message = new Message(cmd, len, buffer);
            return message;
        }

        private static void Read(NetworkStream networkStream, byte[] bytes)
        {
            networkStream.Read(bytes, 0, bytes.Length);
        }
    }

    public class Message
    {
        public int Cmd;
        public int Length;
        public byte[] Bytes;

        public Message(int cmd, int dataLen, byte[] bytes)
        {
            this.Cmd = cmd;
            this.Length = dataLen;
            this.Bytes = bytes;
        }
    }
```

