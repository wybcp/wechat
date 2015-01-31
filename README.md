# Wechat
微信 SDK

## Install

```shell
composer require overtrue/wechat
```

## Usage

基本使用

```php
<?php

use Overtrue\Wechat\Wechat;
use Overtrue\Wechat\Message;

$options = [
    'app_id'         => 'Your appid !!',
    'secret'         => 'Your secret !!'
    'token'          => 'Your token !!',
    'encodingAESKey' => 'Your encodingAESKey!!' // optional
];

// 初始化Wechat实例
$wechat = Wechat::make($options);

// 获取服务端实例
$server = $wechat->server; // 同理，获取客户端：$client = $wechat->client;

// 接收消息
$server->message('text', function($message){
    error_log("收到来自'{$message['FromUserName']}'的文本消息：" . $message['Content']);
});

$result = $server->run(); 

// 返回值$result为字符串，您可以直接用于echo 或者返回给框架
echo $result;
```

---

### 服务端

```php
$server = $wechat->server;
```

#### 接收消息

语法 ：

```php
$server->message('消息类型', function($message){
    // $message 为微信请求过来的xml转换后的数组
});

```

sample:
```php
$server->message('text', function($message){
    error_log('收到文本消息：' . $message['Content']);
});
$server->message('image', function($message){
    error_log('收到图片消息：' . $message['PicUrl']);
});
$server->message('location', function($message){
    error_log('收到地址消息：' . $message['Label']);
});
$server->message('link', function($message){
    error_log('收到链接消息：' . $message['Url']);
});

$server->run();
```

#### 监听事件

语法：

```php
$server->event('事件类型', function($event){
    // $event 为微信请求过来的xml转换后的数组
});

```

sample:

```php
$server->event('unsubscribe', function($message){
    error_log('收到取消关注事件，取消关注者openid: ' . $message['FromUserName']);
});

$server->event('subscribe', function($message){
    error_log('收到关注事件, 关注者openid' . $message['FromUserName']);
});

$server->run();
```

---
### 客户端

```php
$client = $wechat->client;
```

#### 发送客服消息

```php
// 文本消息
$message = new Message(Message::TEXT);
$message->content = '您好小朋友！';


// 图片消息
$message = new Message(Message::IMAGE);
$message->image = __DIR__ . '/test.jpg';


// 语音消息
$message = new Message(Message::VOICE);
$message->voice = __DIR__ . '/test.mp3';

// 视频消息
$message = new Message(Message::VIDEO);
$message->title = '测试视频标题';
$message->description = '这段视频看完你肯定想转的...';
$message->video = __DIR__ . '/test.mp4';
$message->thumb = __DIR__ . '/video_cover.jpg';//XXX: 仅群发时有用

// 音乐消息
$message = new Message(Message::MUSIC);
$message->title = '测试音乐标题';
$message->description = '一段NB的旋律...';
$message->url = 'http://www.baidu.com/mp3/test.mp3'; 
$message->hq_url = 'http://www.baidu.com/mp3/test_hq.mp3'; // 高清版
$message->thumb = __DIR__ . '/music_cover.jpg';

// 图文消息
$message = new Message(Message::NEWS);
$message->items = array(
    array('标题', '描述1', __DIR__ . '/图片1.jpg', 'http://阅读全文url1'),
    array('标题2', '描述2', __DIR__ . '/图片2.jpg', 'http://阅读全文url2'),
    array('标题3', '描述3', __DIR__ . '/图片3.jpg', 'http://阅读全文url3'),
);

$client->send($message);
```

---

### 处理错误

```
$wechat->error(function($error){
    // $error为Exception对象
    // $error->getCode(); 得到错误码：参考：http://mp.weixin.qq.com/wiki/17/fa4e1434e57290788bde25603fa2fcbd.html
    // $error->getMessage(); 错误消息
});
// ...
```

### 自定义缓存写入/读取

```php
// writer
$wechat->cacheWriter(function($key, $value){
    // cache the value.
    return true;
});

// reader
$wechat->cacheReader(function($key){
    // return the cached value.
    return 缓存的数据;
});
```

## License

MIT
