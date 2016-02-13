title: 在Unity3D中使用线程池来跨线程访问主线程
date: 2016-01-26 12:43:50
categories: [Unity3D]
tags: [多线程,Unity3D,线程池]
---
&emsp;&emsp;最近开始考虑借鉴cocos2d-JS中AssetsManager来为Unity3D编写一套热更新的框架，其实从博主的角度来讲，我是非常不愿意做这样的工作的，因为虽然热更新是国内手机游戏的刚性需求，可是事实是，即使大量的手机游戏都做了热更新这块的工作，手机游戏玩家大量流失的情况依然相当严重，所以说如果游戏本身并没有让玩家心动的内容，热更新这件事情还不如不做呢，因为在这种情况下除了浪费玩家手机的电量和流量以外并没有什么实质性的意义。虽然国内的游戏非常依赖渠道，因此游戏的质量就变得不再重要，可是我相信只要是优秀的游戏总会有人去玩的，国内的游戏行业就是这样痛苦并快乐着，不管到什么时候我们对游戏的初心都不应该改变啊！
<!--more-->
#由热更新需求引发的跨线程访问问题
&emsp;&emsp;好了，我们继续回到热更新的话题，这个正在设计中的热更新框架其实不能叫做热更新框架，因为它借鉴了cocos2d-JS中AssetsManager，它是一个资源更新的框架，它负责在每次启动游戏的时候对资源进行更新，而这些资源在更新后如何表现在游戏中，则是由开发者自己来决定的，因为热更新是一个非常宽泛的内容，除了开发者自己有谁知道它想要更新哪些内容呢？这个热更新框架的核心内容是下载资源，相信熟悉Unity3D的朋友都觉得这是一个非常简单的需求。因为Unity3D给大家封装好了一个HTTP的工具类WWW，因为Unity3D引擎在可视化编辑、组件式结构、跨平台性能这三个方面做的非常平民化，导致国内各种线上、线下的培训机构此起彼伏，一个游戏引擎广为人知对开发者来说绝对是件好事，因为从虚幻四引擎在国内的现状就可以看出这一点，引擎相关的文档匮乏让喜欢这个引擎的人望而却步。我非常喜欢虚幻四引擎强大的画面表现能力，可是这个引擎对硬件能力、编程能力、学习能力上的高要求每每让人有些难以适应。

&emsp;&emsp;可惜国内对某件事物的追捧常常停留在表面，越是在这种情况下我们对引擎本身的探索就应该越深入，说Unity3D引擎画面差的各位朋友，你们有尝试过对这个引擎进行优化吗？说虚幻四引擎各种优点的各位朋友，你们用虚幻四引擎开发的游戏在哪里呢？说Unity3D游戏引擎简单的各位朋友，你们尝试过对引擎进行深度定制吗？我觉得在这样一个浮夸烦躁的时代，我们更需要静下心来好好地去学习更加有深度地内容，这种思维一直体现在我的博客里，当我决心不再写邯郸学步的入门教程的时候，我开始了解游戏引擎相关的更多内容，我的视野不再局限在Unity3D这个引擎，因为视频、教程、博客、书籍都不会告诉你别人从来没有遇到过的问题。一个熟悉Unity3D的API而不熟悉.NET甚至图形学的人是无法称之为Unity3D高手的。回到问题本身，一个简单的C#异步下载的代码实现如下，Unity3D这个引擎并不会告诉你这些：

```
 public void Download(string URL, string fileName, Action<long,long> download, Action complate)
{
    //发起一个非阻塞的异步HTTP请求
    HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(URL);
    request.BeginGetResponse(asnycResult =>
    {

        HttpWebRequest _request = (HttpWebRequest)asnycResult.AsyncState;
        HttpWebResponse _reponse = (HttpWebResponse)_request.EndGetResponse(asnycResult);

        //获取文件的总长度
        long totalBytes = _reponse.ContentLength;
        //当前复制的长度
        long copiedBytes = 0;

        //获取输入流
        Stream input = _reponse.GetResponseStream();
        //定义输出流
        Stream outpot = new FileStream(fileName, FileMode.Create);

        //定义缓冲区大小
        byte[] data = new byte[1024];

        //开始读取数据
        int size = input.Read(data, 0, data.Length);
        while(size > 0)
        {
            copiedBytes += size;
            outpot.Write(data, 0, size);
            size = input.Read(data, 0, data.Length);
            //执行回调函数
            download(totalBytes, copiedBytes);
        }

        //关闭输入流
        input.Close();
        //关闭输出流
        outpot.Close();

        //关闭请求
        _reponse.Close();

        //执行回调函数
        complate();

    }, request);

    //关闭请求
    //request.Abort();
}
```

这段代码暂时没有考虑异常处理，因为我们这里关注的热点并非这段代码。注意到在这段代码中设置了两个通过委托实现的回调函数，我们将通过这两个回调函数来捕捉相关的过程事件，因此在实际使用中的这个方法可以写成下面这样：

```
Download("http://localhost:4000/3DRipperDXSetup.exe","D:\\3DRipperDXSetup.exe",
    download:(copiedBytes,totalBytes)=>
    {
        //在这里处理下载中的逻辑
    },
    complate:()=>
    {
        //在这里处理下载完成后的逻辑
    });
```

我们知道.NET中的异步方法本质上是通过多线程来实现的，所以当我们在一个继承自MonoBehaviour的脚本中使用这个方法的时候，实际上是在做跨线程访问，因为MonoBehaviour是运行在一个线程中，所以当我们试图在这个方法中调用Unity3D的API如UI元素时，这个时候我们在做跨线程访问。其实这里和我们开发.NET程序时跨线程访问UI线程是一样的，因此当我们尝试调用UI元素的时候，Unity3D就会提示我们不能直接访问主线程，那么该如何解决这个问题呢？答案就是线程池！

#使用线程池解决跨线程访问问题



