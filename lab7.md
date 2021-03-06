# 实验7：Android网络编程

## 7.1 知识点

###1.     

###2.     

## 7.2 实例步骤

###1. 根据位置信息从中国天气网获取天气信息
简要说明……  
详细步骤……  

###2. Socket编程
简要说明  
　　这个例子只用了TCP协议下的Socket编程，因此这里只讨论TCP的情况。  
　　在java中，服务器端socket、bind、listen等操作被封装在ServerSocket类库中，客户端socket、connect等操作被封装在Socket类库中。如需了解这些操作细节，可学习C语言下的socket编程。  

详细步骤      
服务器端步骤   
1). servs = new ServerSocket(port);　　　//创建套接字，port 为指定的端口号  
2). socket = servs.accept();　　　　　　　//等待连接，主线程会阻塞在这里  
3). 为每个连接创建线程来服务;  
4). 线程中进行读写socket【通信】。  

客户端步骤   
1). socket = Socket("ip_str", port);　　//创建套接字，ip_str 为服务器端IP，port为端口号，和服务器端指定的要一样    
2). 读写socket。　　　　　　　　　　　　//1).中已经执行连接服务器的操作，所以接下来可以直接读写。  
注意：    
1). 客户端连接服务器的操作要放在新线程里面。  
2). 需要添加权限：
```
    <uses-permission android:name="android.permission.INTERNET"/>  
```
3). 真机调试的时候，真机和主机要在同一个局域网。  
核心代码  
服务器端：[(所有源码)](https://github.com/isshe/Android-20160303/tree/master/MultiChatServ)
``` java
public class Myserver {
    //定义保存所有的Socket的ArrayList
    public static ArrayList<Socket> socketList = new ArrayList<Socket>();
    
    public static void main(String[] args) 
        throws IOException
    {
        ServerSocket ss = new ServerSocket(9402);               //端口号为9402
        while (true)
        {
            Socket s = ss.accept();
            socketList.add(s);　　　　　　　　　　　//把新连接加入ArrayList中。
            //每连接一个客户端就开一个线程为之服务
            new Thread(new ServerThread(s)).start();
        }   
    }   
}  
```
客户端代码：[(所有源码)](https://github.com/isshe/Android-20160303/tree/master/MultiChat/app/src/main/java/com/example/dell/multichat)
``` java
    public void run()
    {
        try {
            s = new Socket("192.168.240.22", 9402);            //IP是服务器IP， 端口号和服务器一致
            br = new BufferedReader((new InputStreamReader(s.getInputStream())));
            os = s.getOutputStream();       
            
            //do something

        }
        catch (...)
        {
            //...
        }
    }
```
另一个例子：https://github.com/hzuapps/android-labs/tree/master/app/src/main/java/edu/hzuapps/androidworks/homeworks/net1314080903204/tcp_tester 

###3. 从aqicn.org获取PM2.5信息@ZhengQZ123
简要说明……  
    利用aqicn.org提供的接口，显示pm2.5的数值
详细步骤……  
1. 获取网络权限
```
    <uses-permission android:name="android.permission.INTERNET"/>  
```
2.读取http://aqicn.org/publishingdata/json提供的json数据
``` 
                     BufferedReader reader = new BufferedReader(new InputStreamReader(new
                     URL("http://aqicn.org/publishingdata/json").openStream(), "utf-8"));
                     String line=null;
                     StringBuffer content =new StringBuffer();
                     while((line=reader.readLine())!=null)
                     {
                        content.append(line);
                     }
 ```
 3.解析获取到的json数据
 ``` 
                    try {
                        JSONArray jsonarr=new JSONArray(s);
                        JSONObject firstJO=jsonarr.getJSONObject(0);
                        JSONArray pollutants=firstJO.getJSONArray("pollutants");
                        JSONObject firstPollutants=pollutants.getJSONObject(0);
                        System.out.println("cityName=" firstJO.getString("cityName") ",local=" firstJO.getString("localName"));
                        String cityName= firstJO.getString("cityName");
                        String localName= firstJO.getString("localName");
                        Double pollutant=firstPollutants.getDouble("value");
                        String a=cityName+" "+localName+":"+pollutant;
                        firstJO.getString("localName"),firstPollutants.getDouble("value"));
                        tvPmData.setText(a);
                    } catch (JSONException e) {
                        e.printStackTrace();
                    }
``` 
4.将获取到的数据按照需要显示出来 tvPmData.setText(a)

activity代码：(https://github.com/ZhengQZ123/android-labs/blob/master/app/src/main/java/edu/hzuapps/androidworks/homeworks/net1314080903247/Net1314080903247Activity.java)

AndroidManifest代码：(https://github.com/ZhengQZ123/android-labs/blob/master/app/src/main/java/edu/hzuapps/androidworks/homeworks/net1314080903247/AndroidManifest.xml)

activity xml代码：(https://github.com/ZhengQZ123/android-labs/blob/master/app/src/main/res/layout/activity_net1314080903247.xml)

