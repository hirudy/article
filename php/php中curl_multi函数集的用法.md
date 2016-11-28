## 一、引言
这段时间比较忙，已经很久没有写博客了。今天我就来聊聊我关于curl_multi_*函数集的使用心得，关于http请求的问题。

当我们用户php发起一个http请求的时候。我们会首先想到用什么？没错，我们会创建curl来请求。当我们在一次执行中需要发起多个http请求呢。这简单，对每一个URL发起一次url请求。请求玩第1个再请求第2个....这就完了？哪我们还说个啥。

官网链接：[http://php.net/manual/zh/book.curl.php](http://php.net/manual/zh/book.curl.php)
## 二、多次简单的curl请求弊端
![图1.串行请求与并行请求](http://upload-images.jianshu.io/upload_images/301894-869b3b06a213a35c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们举个栗子。现在有三个http请求。每个请求耗时2s。如果按照简单的curl请求（图1-(1)）。耗时6s.这是不能容忍的。如果请求的个数越多耗时约多。

有没有一种方式来缩小查询时间？能不能三个http请求同时执行（如图1-(1)）？有很多方法来解决这个问题，将耗时减少到2s。如：多进程、线程、事件循环、curl_multi_\*等等。最简单的方式就是通过curl_multi_\*函数来完成。事实上curl_multi_\*内部实现就是用的事件循环。

## 三、简单的curl_multi_*运用
```php
<?php
/**
 *
 * curl_multi_*简单运用
 *
 * @author: rudy
 * @date: 2016/07/12
 */

/**
 * 根据url,postData获取curl请求对象,这个比较简单,可以看官方文档
 */
function getCurlObject($url,$postData=array(),$header=array()){
    $options = array();
    $url = trim($url);
    $options[CURLOPT_URL] = $url;
    $options[CURLOPT_TIMEOUT] = 10;
    $options[CURLOPT_USERAGENT] = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/44.0.2403.89 Safari/537.36';
    $options[CURLOPT_RETURNTRANSFER] = true;
    //    $options[CURLOPT_PROXY] = '127.0.0.1:8888';
    foreach($header as $key=>$value){
        $options[$key] =$value;
    }
    if(!empty($postData) && is_array($postData)){
        $options[CURLOPT_POST] = true;
        $options[CURLOPT_POSTFIELDS] = http_build_query($postData);
    }
    if(stripos($url,'https') === 0){
        $options[CURLOPT_SSL_VERIFYPEER] = false;
    }
    $ch = curl_init();
    curl_setopt_array($ch,$options);

    return $ch;
}

// 创建三个待请求的url对象
$chList = array();
$chList[] = getCurlObject('https://www.baidu.com');
$chList[] = getCurlObject('http://www.jd.com');
$chList[] = getCurlObject('http://www.jianshu.com/');

// 创建多请求执行对象
$downloader = curl_multi_init();

// 将三个待请求对象放入下载器中
foreach ($chList as $ch){
    curl_multi_add_handle($downloader,$ch);
}

// 轮询
do {
    while (($execrun = curl_multi_exec($downloader, $running)) == CURLM_CALL_MULTI_PERFORM) ;
    if ($execrun != CURLM_OK) {
        break;
    }

    // 一旦有一个请求完成，找出来，处理,因为curl底层是select，所以最大受限于1024
    while ($done = curl_multi_info_read($downloader))
    {
        // 从请求中获取信息、内容、错误
        $info = curl_getinfo($done['handle']);
        $output = curl_multi_getcontent($done['handle']);
        $error = curl_error($done['handle']);

        // 将请求结果保存,我这里是打印出来
        print $output;
//        print "一个请求下载完成!\n";

        // 把请求已经完成了得 curl handle 删除
        curl_multi_remove_handle($downloader, $done['handle']);
    }

    // 当没有数据的时候进行堵塞，把 CPU 使用权交出来，避免上面 do 死循环空跑数据导致 CPU 100%
    if ($running) {
        $rel = curl_multi_select($downloader, 1);
        if($rel == -1){
            usleep(1000);
        }
    }

    if( $running == false){
        break;
    }
} while (true);

// 下载完毕,关闭下载器
curl_multi_close($downloader);
echo "所有请求下载完成!";
```
在该例子中，首先创建三个或多个要请求的url请求对象。通过curl_multi_\*函数创建下载器。将请求写入下载器中。最后轮询。等待三个请求现在完成。做处理。

## 四、复杂的curl_multi_\*运用
这就是curl_multi_\*用法？too yong too simple！在上面的例子中。下载器$downloader中的请求是一开始就添加好了的。我们能不能动态的向下载器中添加请求。动态的从下载器中取出已经完成了的请求。想想。这是什么？这不就是爬虫的核心部分-动态下载器。如何动态的添加？我们可以用多进程通过IPC添加。我们可以通过协程通过队列添加等待。
>1. curl_multi_\*函数实现的HTTP压测工具：
[https://github.com/hirudy/phplib/blob/master/phpAb.php](https://github.com/hirudy/phplib/blob/master/phpAb.php)。
>2. curl_multi_\*实现的http请求类：
[https://github.com/hirudy/phplib](https://github.com/hirudy/phplib)
>3. 我这实现了一个通过协程+curl_multi_\*的爬虫框架。
Tspider:[https://github.com/hirudy/Tspider](https://github.com/hirudy/Tspider)。
单进程可处理请求2000-5000/min。