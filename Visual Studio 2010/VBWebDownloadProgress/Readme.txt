﻿================================================================================
	           Windows应用程序： VB版网上下载进度应用程序                         
===============================================================================
/////////////////////////////////////////////////////////////////////////////
摘要：
VBWebDownloadProgress这个示例阐述了怎样在下载过程中显示下载进度。

System.Net.WebClient类有一个DownloadProgressChanged事件。
你可以通过注册这个事件来显示进度。但是这个类并不支持暂停或重新开始。

示例中的HttpDownloadClient类可以用来从网上下载数据并且支持以下特性：
1. 设定缓冲区和缓存的大小。
2. 下载整个文件中指定的数据块。
3. 可以开始，暂停，重新开始和取消一次下载。
4. 提供文件的大小，下载的速度以及下载所用的时间。
5. 显示StatusChanged事件，DownloadProgressChanged事件以及DownloadCompleted事件。


   
////////////////////////////////////////////////////////////////////////////////
演示：

步骤一. 在Visual Studio 2010中建立一个示例项目。

步骤二. 运行应用程序VBWebDownloadProgress.exe. 

步骤三. 输入以下的链接作为url.
http://download.microsoft.com/download/9/5/A/95A9616B-7A37-4AF6-BC36-D6EA96C8DAAE/dotNetFx40_Full_x86_x64.exe

步骤四. 输入一个本地路径，如D:\DotNetFx4.exe.

步骤五. 点击“下载”按钮， 你将会看见状态为“正在下载”并且概要为
“已接收：***KB，总共：***KB，速度：***KB/s”，进度条也将会改变。 
       在资源管理器中，你将会找到一个文件就是 D:\DotNetFx4.exe.tmp.

步骤六. 点击“暂停”按钮，你将会看见状态为“已暂停”并且概要为
“已接收：***KB，总共：***KB，速度：***KB/s”，进度条也将会暂停。

步骤七. 点击“重新开始”按钮， 你将会看见状态为“正在下载”并且概要为
“已接收：***KB，总共：***KB，速度：***KB/s”，进度条也将会改变。

步骤八. 当下载完成的时候，你会看到状态为“已完成”并且概要为
“已接收：***KB，总共：***KB，时间：***”，进度条也将会暂停。 
       在资源管理器中，你将会找到一个文件就是 D:\DotNetFx4.exe.


/////////////////////////////////////////////////////////////////////////////
编码逻辑：

1. 当创建一个HttpDownloadClient对象的时候，就会初始化StartPoint，EndPoint，
   BufferSize，MaxCacheSize，BufferCountPerNotification，
   Url，DownloadPath和Status这些字段或属性。

2. 下载开始时，会检查目标文件是否存在，如果不存在，就创建一个和
   将要下载的文件同样大小的本地文件，然后开始在后台线程下载。

3. 下载线程会从响应流中读取一个字节的缓冲区，并把它先储存在MemoryStream的缓存中。
   如果缓存已满或者下载暂停、取消或完成了，就将缓存中的数据写入本地文件中。

4. 当读指定数目的缓冲区时，就会引发DownloadProgressChanged事件。

5. 如果下载任务被暂停了，就保存已经下载文件的大小。
   重新开始的时候，就从一个开始点下载文件。

6. 在当前的下载任务停止时，已用时间和状态都会相应更新。

7. 当下载任务完成或被取消时，就会引发DownloadCompleted事件。


/////////////////////////////////////////////////////////////////////////////
参考文献:
http://msdn.microsoft.com/en-us/library/system.net.webrequest.aspx
http://social.msdn.microsoft.com/Forums/en-US/csharpgeneral/thread/e115d4a1-5800-4f2a-98d8-079de6cf8a1a
/////////////////////////////////////////////////////////////////////////////
