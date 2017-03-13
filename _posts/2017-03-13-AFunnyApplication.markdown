---
layout: post
title: A-funny-application-requirement.
date: 2017-03-13 18:48
comments: true
external-url:
categories: Java
---

## 初衷：
做该小玩意儿的目的是希望通过“边做边学”的方式学习java，边做边学是学习一门编程语言最快的途径之一。

## 需求：
总体：本应用的总体需求是做一个简单的爬虫工具，从一个小说网站上面爬去一部小说，以txt格式保存在本地文件系统上。

具体：具体思路和步骤如下：

<1> 根据给定的一个URL下载所对应的页面的HTML数据，可以使用下面的代码：
这里使用的url为：http://longwangchuanshuoba.com

 ```
     public String downloadWebData(String url)throws IOException{
         return downloadWebData(url, "UTF-8");
     }

     public String downloadWebData(String url, String charset) throws IOException {
         URL hh= new URL(url);
         URLConnection connection = hh.openConnection();
         connection.setConnectTimeout(15000);
         connection.setReadTimeout(15000);
         String redirect = connection.getHeaderField("Location");
         if (redirect != null){
             connection = new URL(redirect).openConnection();
         }
         BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), charset));
         StringBuilder builder = new StringBuilder();
         String inputLine;
         while ((inputLine = in.readLine()) != null) {
             builder.append(inputLine).append("\n");
         }
         return builder.toString();
     }
 ```

 测试代码为：
 ```

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.URL;
import java.net.URLConnection;

/**
 * Created by liangyh on 2/11/2017.
 */
public class HttpClient {

    public String downloadWebData(String url)throws IOException{
        return downloadWebData(url, "UTF-8");
    }

    public String downloadWebData(String url, String charset) throws IOException {
        URL hh= new URL(url);
        URLConnection connection = hh.openConnection();
        connection.setConnectTimeout(15000);
        connection.setReadTimeout(15000);
        String redirect = connection.getHeaderField("Location");
        if (redirect != null){
            connection = new URL(redirect).openConnection();
        }
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), charset));
        StringBuilder builder = new StringBuilder();
        String inputLine;
        while ((inputLine = in.readLine()) != null) {
            builder.append(inputLine).append("\n");
        }
        return builder.toString();
    }

    public static void main(String[] args) throws IOException {
        String result = new HttpClient().downloadWebData("http://longwangchuanshuoba.com");
        System.out.println(result);
    }
}
 ```

>上面的爬取代码只需要知道怎么用就可以了，因为这个不是本应用的重点。


<2> 从第1步下载的html文件中提取出新的有用的URL（重点）。

 我们需要从刚刚爬取的html文件中提取出如下所示的URL，比如：`http://longwangchuanshuoba.com/1609.html`，
 这些url对应小说的正文。然后把这些url保存起来，去重。（可以保存在java.util.HashSet中，它有自动去重的功能）。

下面内容是第一步所爬去的内容的一部分：
```
、、、

<li><span><a href="http://longwangchuanshuoba.com/1609.html" rel="bookmark">第七百三十章 不速之客</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1612.html" rel="bookmark">第七百三十一章 都长大了</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1615.html" rel="bookmark">第七百三十二章 他怎样，和我没关系</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1618.html" rel="bookmark">第七百三十三章 龙枪女神的哥哥</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1620.html" rel="bookmark">第七百三十四章 娜儿的闺房</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1622.html" rel="bookmark">第七百三十五章 你还知道回来!</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1624.html" rel="bookmark">第七百三十六章 忐忑的谢邂</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1626.html" rel="bookmark">第七百三十七章 擎天斗罗</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1628.html" rel="bookmark">第七百三十八章 海神阁主的故事</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1630.html" rel="bookmark">第七百三十九章 光暗斗罗</a></span></li>
<li><span><a href="http://longwangchuanshuoba.com/1632.html" rel="bookmark">第七百四十章 惺惺相惜</a></span></li>

、、、

```

 可以使用`正则表达式`提取url。如下所示：
 ```
 Pattern pattern = Pattern.compile("(<a\\shref=[^\\d]*)(\\d{1,}.html)(.*</a>)");
 Matcher matcher = pattern.matcher(htmlPage);

 while(matcher.find()){
     String url = matcher.group(2);
     System.out.println(url);
 }

 ```


<3> 根据上面第二步所提取的url下载它们所对应的html网页内容（和第一步类似）。
从所爬取的html网页数据中提取出小说的正文，同时过滤掉没用的信息（或者先过滤再提取）。
把所得到的小说正文`按照章节顺序`整合在一起，最后保存到本地文件系统上面。


如果要排序，可以使用

```
List<String> result = new ArrayList<String>();
Collections.sort(result);
```

保存数据的方法可以使用下面这个方法（如果数据大于40M，这个方法就不太适用）。
```
public void save(String path, String data){
    if(path == null)throw new IllegalArgumentException("路径不能为空");
    Writer writer = null;
    try {
        writer = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(path)));
        writer.write(data);
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        try {
            if(writer != null) writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

下面是上面方法的测试：会在f盘生成一个test文件
```
import com.huai.httpClient.HttpClient;

import java.io.*;

/**
 * Created by liangyh on 3/13/2017.
 */
public class Test {

    public static void main(String[] args) throws IOException {
        new Test().save("F:\\test.txt", "hello world");
    }

    public void save(String path, String data){
        if(path == null)throw new IllegalArgumentException("路径不能为空");
        Writer writer = null;
        try {
            writer = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(path)));
            writer.write(data);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                if(writer != null) writer.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```


>该需求的由来：本人在追两部小说，《大主宰》和《斗罗大陆3-龙王传说》，
每过几个小时就去打开网页，点击bookmark，看看有没有更新。
现在的需求是：我不需要用浏览器查看有没有更新，只要这两部小说更新了，这个应用就自动推送到我的手机上并提醒我。
本人已经实现并发表到blog上面了。