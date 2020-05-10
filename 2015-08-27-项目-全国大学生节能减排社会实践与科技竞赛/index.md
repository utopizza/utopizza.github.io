# 全国大学生节能减排社会实践与科技竞赛



一、简介

参赛作品是《面向物联网的无线无源风速仪》，设计一种面向物联网的无线无源小型三杯式风速仪，其工作原理是：
(1) 无源：在有风时通过一个内置的小型发电装置，捕获风能，转化为电能供应自身工作，在无风时使用后备电源。为节省电源，大部分时间处于休眠状态，除非被上位机唤醒进行数据交互；
(2) 无线：使用zigbee无线通讯模块，与上位机进行通信。

![](/2015-08-27-项目-全国大学生节能减排社会实践与科技竞赛/装置实物.png)

其中我实现的部分是上位机软件，主要功能：定时向串口发送数据采集命令，然后从串口读取风速仪返回的风速值，显示到屏幕上。

![](/2015-08-27-项目-全国大学生节能减排社会实践与科技竞赛/上位机采集风速值.png)

![](/2015-08-27-项目-全国大学生节能减排社会实践与科技竞赛/上位机软件.jpg)

二、上位机软件的实现

我用 WinForm + MS.Chart 的方式来实现，用 WinForm 来制作窗体，用 MS.Chart 来实现风速值的实时显示。上位机的采集数据的过程包括：定时、读写串口、CRC校验、字符串和十六进制等的数值转换、更新图形界面、监控报警最大风速值等。关键代码：

初始化

```
//用于更新图形化界面的事件委托
delegate void UpdateTextEventHandler_experimental(List<double> experimentalWSList);
UpdateTextEventHandler_experimental updateTextEventHandler_experimental;
delegate void UpdateChartEventHandler_experimental(string experimentalWSstr);
UpdateChartEventHandler_experimental updateChartEventHandler_experimental;

//初始化串口
public void InitCOM_experimental(string PortName)
{
    experimentalPort = new SerialPort(PortName);
    experimentalPort.BaudRate = 9600;//波特率
    experimentalPort.Parity = Parity.None;//无奇偶校验位
    experimentalPort.StopBits = StopBits.One;//一个停止位
    experimentalPort.DataBits = 8;//8位数据位
    experimentalPort.DataReceived += experimentalPort_DataReceived;//接收返回数据时触发的事件
}
```

接收数据，更新图形化界面

```
//串口接收到风速仪返回数据时触发的事件
private void experimentalPort_DataReceived(object sender, SerialDataReceivedEventArgs e)
{
    //读取缓冲区的数据，这里应该先处理读取的数据
    int[] buffer = new int[7];
    for (int i = 0; i < buffer.Length; i++)
    {
        buffer[i] = experimentalPort.ReadByte();
    }
    double newWindSpeed = Convert.ToDouble(DataHandler_ExperimentalMachine(buffer));
    speedWarnTest(newWindSpeed);
    experimentalWSList.Add(newWindSpeed);

    //更新Textbox
    updateTextEventHandler_experimental = new UpdateTextEventHandler_experimental(UpdateTextBox_experimental);
    this.Invoke(updateTextEventHandler_experimental, new List<double>[] { experimentalWSList });

    //更新Chart
    updateChartEventHandler_experimental = new UpdateChartEventHandler_experimental(UpdateChart_experimental);
    this.Invoke(updateChartEventHandler_experimental, new string[] { newWindSpeed.ToString() });
}
```
    
发送数据采集命令

```
//用于发送采集命令的定时器
private void SendCommand_Timer()
{
    myTimer = new System.Timers.Timer();
    myTimer.Elapsed += new ElapsedEventHandler(SendCommand_TimedEvent);
    myTimer.AutoReset = true;
    myTimer.Enabled = true;
    myTimer.Interval = 1000;
}

//发送采集命令事件
private void SendCommand_TimedEvent(object sender, System.Timers.ElapsedEventArgs e)
{
    SendCommand_ExperimentalMachine();
}

//发送采集命令具体操作：写数据到串口
private void SendCommand_ExperimentalMachine()
{
    //ID 03 00 00 00 01 CR0 CR1；十六进制，ID为设备地址，CR0，CR1为CRC校验位，其他固定
    byte[] commandByte = { 0x01, 0x03, 0x00, 0x00, 0x00, 0x01 }; //01 03 00 00 00 01 0A 84
    byte[] commandByteCRC = CRC(commandByte, commandByte.Length);
    experimentalPort.Write(commandByteCRC, 0, commandByteCRC.Length);
}

//实验机型CRC校验算法
private byte[] CRC(byte[] byteArray, int datalen)
{
    byte CRC16Lo, CRC16Hi, CL, CH, SaveHi, SaveLo;
    int i, Flag;
    CRC16Lo = 0xFF;
    CRC16Hi = 0xFF;
    CL = 0x01;
    CH = 0xA0;
    for (i = 0; i < datalen; i++)
    {
        CRC16Lo ^= byteArray[i];
        for (Flag = 0; Flag < 8; Flag++)
        {
            SaveHi = CRC16Hi;
            SaveLo = CRC16Lo;
            CRC16Hi >>= 1;
            CRC16Lo >>= 1;
            if ((SaveHi & 0x01) == 0x01)
            {
                CRC16Lo |= 0x80;
            }
            if ((SaveLo & 0x01) == 0x01)
            {
                CRC16Hi ^= CH;
                CRC16Lo ^= CL;
            }
        }
    }

    byte[] newByteArray = new byte[datalen + 2];
    for (int j = 0; j < datalen; j++)
    {
        newByteArray[j] = byteArray[j];
    }
    newByteArray[datalen] = CRC16Lo;
    newByteArray[datalen + 1] = CRC16Hi;
    //return (CRC16Hi << 8) | CRC16Lo;
    return newByteArray;
}
```
    
三、调试

1 先使用虚拟串口，让COM1和COM2连接

![](/2015-08-27-项目-全国大学生节能减排社会实践与科技竞赛/调试-虚拟串口.png)

2 打开串口助手，连接COM1，模拟风速仪

![](/2015-08-27-项目-全国大学生节能减排社会实践与科技竞赛/调试-串口助手.png)

3 启动上位机软件，连接COM2，测试数据采集命令的发送、风速值的采集和显示

![](/2015-08-27-项目-全国大学生节能减排社会实践与科技竞赛/调试-上位机软件.png)

