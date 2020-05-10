# 全国高校物联网应用创新大赛



一、简介

参赛作品是“基于物联网的车辆事故智能报警系统”，它对车辆运行情况进行实时监控。一旦发生车辆事故导致安全气囊打开时，系统便会检测到这一状态，并立即获取事故的定位信息、人员信息并进行自动报警。当交警服务中心接收到报警电话和相关信息后，就会立即通知离事故发生地最近的医院前往救援。

![](/2015-01-20-项目-全国高校物联网应用创新大赛/系统流程图.png)

![](/2015-01-20-项目-全国高校物联网应用创新大赛/系统流程图2.png)

系统工作流程：

1、 当车载客户端监测到安全气囊打开后，它先从 OBD 系统中读取出车辆故障码等车辆信息，记录到单片机内存中，然后启动蓝牙模块，把车辆信息发送到手机客户端；

2、当手机客户端收到车辆故障码后，
(1)、启动第一重自动报警：从手机内存卡读出驾车人员信息，启动 GPS 对车辆进行定位读出车辆经纬度，然后把车辆信息、人员信息、定位信息一并通过 GPRS 发送到交警信息中心服务器；
(2)、启动第二重自动报警：调用手机的拨号功能，自动拨打 110 报警电话；
(3)、当手机报警功能被触发后， 手机开始倒计时 15 秒。用户可以在 15 秒内点击取消按钮中断报警， 否则在计时结束后执行报警任务。

3、当交警信息中心服务器收到手机发送过来的报警信息后，会马上把报警信息显示在屏幕上，并响起警铃，通知交警人员，前往事故现在进行处理。

二、实现

我负责开发的部分是 Server 端的“交警信息中心”网站，和 Client 端的“EmergencyCall”安卓 App。

1、Server 端

基于 .NET 的 WebService 框架，采用 Model -> DAL -> BLL 三层架构。Server 端的主要功能是：提供 WebService 方法给安卓 客户端调用，接收其发送过来的数据，经过处理后插入数据库，最后显示到网页上。

Server 端主要的两个文件是：网站主页 TICSystemDefault.aspx 和 WebService 接口 TICSystemWebService.asmx。网站主页每3秒自动刷新一次，把所有数据即时地显示到网页上。WebService 接口负责和安卓端数据交互。其他文件分别实现解析Json字符串并转化成Model、操作数据库、绑定数据到网页等等。

![](/2015-01-20-项目-全国高校物联网应用创新大赛/Server端.png)

其中提供给安卓端调用的 WebService 方法：

```
// webservice方法
[WebMethod]
public int AddAccidentInfo(string JasonAccidentInfo)
{
    AccidentInfoModel AccidentInfo = ModelJsonChange<AccidentInfoModel>.JsonToModel(JasonAccidentInfo);
    return accidentDal.AddAccidentInfo(AccidentInfo);
}

//Helper类，实现Json和Model的互转
public class ModelJsonChange<T>
{
    //Model转换为Json
    public static string ModelToJson(T model)
    {
        JavaScriptSerializer js = new JavaScriptSerializer();
        return js.Serialize(model);
    }

    //Json转换为Model
    public static T JsonToModel(string json)
    {
        JavaScriptSerializer js = new JavaScriptSerializer();
        return js.Deserialize<T>(json);
    }
}
```

2、Client 端

Client 端的安卓 App 名为“EmergencyCall”。

![](/2015-01-20-项目-全国高校物联网应用创新大赛/MainActivity.png)

其中分为五大功能模块：

(1). SaveActivity：保存、修改用户的个人信息。我使用了SharedPreferences来保存这些数据。SharedPreferences是Android平台上一个轻量级的存储类，用来保存应用的一些常用配置。

![](/2015-01-20-项目-全国高校物联网应用创新大赛/SaveActivity.png)

关键代码（为节省篇幅，以下均省略 OnCreate、setContentView、addActivity、findViewById 等基本的常用代码，只显示实现某功能的关键代码）：

```
// 声明
private EditText EditText_userName;// 姓名
private EditText EditText_userPhone;// 电话
private EditText EditText_userCar;// 车牌
private SharedPreferences sharedPrefrences;
private Editor editor;
private static final String FILENAME = "filename";// 要存储的文件名

// 进入页面时，将已经保存在文件中的内容取出，显示到三个文本框中
sharedPrefrences = this.getSharedPreferences(FILENAME, MODE_PRIVATE);
EditText_userName.setText(sharedPrefrences.getString("userName", ""));
EditText_userPhone.setText(sharedPrefrences.getString("userPhone", ""));
EditText_userCar.setText(sharedPrefrences.getString("userCar", ""));

// 保存按钮
savebutton.setOnClickListener(new View.OnClickListener() {
	@Override
	public void onClick(View v) {
		editor = getSharedPreferences(FILENAME, MODE_PRIVATE).edit();
		editor.putString("userName", EditText_userName.getText().toString());
		editor.putString("userPhone", EditText_userPhone.getText().toString());
		editor.putString("userCar", EditText_userCar.getText().toString());
		editor.commit();
		Intent intent = new Intent();
		intent.setClass(SaveActivity.this, MainActivity.class);
		startActivity(intent);
	}
});
```

(2). BlueToothActivity：蓝牙连接，使手机通过蓝牙与车上的硬件模块通讯。

![](/2015-01-20-项目-全国高校物联网应用创新大赛/BlueToothActivity.png)

关键代码：

```
// 通过蓝牙连接硬件
BluetoothAdapter btAdapt = BluetoothAdapter.getDefaultAdapter();
BluetoothDevice btDev = btAdapt.getRemoteDevice(address);// "00:11:00:18:05:45"
Method m = btDev.getClass().getMethod("createRfcommSocket", new Class[] { int.class });
BluetoothSocket btSocket = (BluetoothSocket) m.invoke(btDev, Integer.valueOf(1));
try {
	btSocket.connect();
	Log.e(TAG, " BT connection established, data transfer link open.");
	Toast.makeText(testBlueTooth.this, "连接成功", Toast.LENGTH_SHORT).show();
	
	// 进入护航界面
	Intent intent = new Intent();
	intent.setClass(testBlueTooth.this, DisplayActivity.class);
	startActivity(intent);
	
} catch (IOException e) {
	Log.e(TAG, " Connection failed.", e);
	setTitle("连接失败..");
}
```

(3). DisplayActivity：连接成功后，手机将一直监控车载硬件模块的状态，接收硬件模块发送过来的状态码。

![](/2015-01-20-项目-全国高校物联网应用创新大赛/DisplayActivity.png)

关键代码：

```
// 监听车载硬件模块发送过来的消息
ConnectedThread manageThread = new ConnectedThread();
Handler mHandler = new MyHandler();
manageThread.Start();
public ConnectedThread() {
	isRecording = false;
	this.wait = 50;
	thread = new Thread(new ReadRunnable());
}
private class ReadRunnable implements Runnable {
	public void run() {
		while (isRecording) {

			//从蓝牙端口获取接收到的消息
			try {
				inStream = testBlueTooth.btSocket.getInputStream();
			} catch (IOException e) {}

			// 处理获取的消息，设置长度为20
			int length = 20;
			byte[] temp = new byte[length];
			if (inStream != null) {
				try {
					int len = inStream.read(temp, 0, length - 1);
					Log.e("available", String.valueOf(len));
					if (len > 0) {
						byte[] btBuf = new byte[len];
						System.arraycopy(temp, 0, btBuf, 0, btBuf.length);
						String readStr1 = new String(btBuf, encodeType);
						// 如果收到非空数据，说明事故发生，发送“01”给mHandler
						mHandler.obtainMessage(01, len, -1, readStr1).sendToTarget();
					}
					Thread.sleep(wait);
				} catch (Exception e) {
					// 否则发送“00”，表示没有事故发生(这样好像逻辑有点问题)
					mHandler.sendEmptyMessage(00);
				}
			}

		}
	}
}

// 接收到状态码后的后续处理
private class MyHandler extends Handler {
	@Override
	public void dispatchMessage(Message msg) {
		String info = (String) msg.obj;
		if (StopFlag) {
			if (info.equals("01")) {
				
				StopFlag=false;
				
				// 报警
				Intent intent = new Intent();
				intent.setClass(DisplayActivity.this, PostActivity.class);
				startActivity(intent);
				// finish();
			}
		}
	}
}
```

(4). PostActivity：进入事故报警流程，获取用户的个人信息、地理位置信息和当前时间，然后通过 GPRS 发送到交警信息中心网站。发送的数据格式是 Json。

![](/2015-01-20-项目-全国高校物联网应用创新大赛/PostActivity.png)

具体工作流程：启动线程 time_runnable 计时 -> 计时结束，发送报警信息给 time_handler -> time_handler 转发报警信息给 http_handler -> http_handler 启动线程 http_runnable 执行 http 报警。HttpPost 用到了外部包 ksoap2 。

```
import android.location.Location;
import android.location.LocationListener;
import android.location.LocationManager;
import org.json.JSONException;
import org.json.JSONObject;
import org.ksoap2.SoapEnvelope;
import org.ksoap2.serialization.SoapObject;
import org.ksoap2.serialization.SoapSerializationEnvelope;
import org.ksoap2.transport.HttpTransportSE;

// 获取当前时间
public String GetNowDateTime() {
	SimpleDateFormat sDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
	String date = sDateFormat.format(new java.util.Date());
	return date;
}

// 获取地理位置
public String GetLocation() {

	LocationManager locationManager = (LocationManager) getSystemService(LOCATION_SERVICE);
	Criteria criteria = new Criteria();
	criteria.setAccuracy(Criteria.ACCURACY_COARSE);
	criteria.setAltitudeRequired(false);
	criteria.setBearingRequired(false);
	criteria.setCostAllowed(true);
	criteria.setPowerRequirement(Criteria.POWER_HIGH);
	criteria.setSpeedRequired(false);

	String currentProvider = locationManager.getBestProvider(criteria, true);
	Location currentLocation = locationManager.getLastKnownLocation(currentProvider);
	if (currentLocation == null) {
		locationManager.requestLocationUpdates(currentProvider, 0, 0, locationListener);
	}
	while (true) {
		currentLocation = locationManager.getLastKnownLocation(currentProvider);
		if (currentLocation != null) {
			Log.d("Location", "Latitude: " + currentLocation.getLatitude());
			Log.d("Location", "Longitude: " + currentLocation.getLongitude());
			break;
		} else {
			Log.d("Location", "Latitude: " + 0);
			Log.d("Location", "Longitude: " + 0);
		}
		try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			Log.e("Location", e.getMessage());
		}
	}

	String userLocation = "经度" + currentLocation.getLongitude() + "；纬度" + currentLocation.getLatitude();
	return userLocation;
}
```

```
// time_runnable线程：计时15秒，5秒后给time_handler发送信息
Runnable time_runnable = new Runnable() {
    @Override
    public void run() {
        try {
            Thread.sleep(15000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        Message message = new Message();
        time_handler.sendMessage(message);
    }
};

// http_runnable线程：发送Http报警
Runnable http_runnable = new Runnable() {
    @Override
    public void run() {
        String AccidentTime = GetNowDateTime();
        String AccidentPlace = GetLocation();
        String PersonName = sharedPrefrences.getString("userName", "");
        String PersonPhone = sharedPrefrences.getString("userPhone", "");
        String CarNumber = sharedPrefrences.getString("userCar", "");
        //发送
        HttpPost(AccidentTime, AccidentPlace, PersonName, PersonPhone, CarNumber);
    }
};

// 启动time_runnable（在OnCreate中）
new Thread(time_runnable).start();

// 计时结束，给http_handler转发报警信息
time_handler = new Handler() {
	@Override
	public void handleMessage(Message msg) {
		if (IsPost) {
			
			IsPost = false;
			time_runnable = null;
			
			// 转发报警信息给http_handler
			Message message = new Message();
			http_handler.sendMessage(message);

			// 跳转到拨号报警页面
			Intent intent = new Intent();
			intent.setClass(PostActivity.this, PhoneActivity.class);
			startActivity(intent);
			//finish();
		}
	}
};

// 启动线程http_runnable，执行http报警
Handler http_handler = new Handler() {
    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        new Thread(http_runnable).start();
    }
};
```

(5). PhoneActivity：同样倒计时15秒，计时结束后自动拨号报警。

![](/2015-01-20-项目-全国高校物联网应用创新大赛/PhoneActivity.png)

关键代码：

```
// 启动子线程开始计时
new Thread(time_runnable).start();

// 计时结束，进行拨号报警
time_handler = new Handler() {
	@Override
	public void handleMessage(Message msg) {
		if (IsPhone) {
			
			IsPhone = false;
			time_runnable = null;
			
			// 拨打报警电话
			Intent intent = new Intent(Intent.ACTION_CALL, Uri.parse("tel:110"));
			PhoneActivity.this.startActivity(intent);
			
			MyApplication.getInstance().exit();
			//finish();
		}
	}
};
```

3、车载硬件模块

这部分是做硬件的同学实现的，安装在车辆的OBD接口上面，不断地给车辆发送查询命令，查询安全气囊的状态码，如果发现安全气囊打开了，那么断定发生了严重事故，立刻通过蓝牙模块发送报警信息给用户的手机，以启动后续的自动报警流程。由于实验条件和时间的限制，目前只实现了简单版。

![](/2015-01-20-项目-全国高校物联网应用创新大赛/车载硬件模块.jpg)

