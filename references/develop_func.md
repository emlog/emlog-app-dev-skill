# 🔨 系统调用和通用函数

应用可以直接使用 emlog 系统内置的类和方法，能够提高开发效率，可以直接用于模板和插件开发的通用方法和函数。

## 输入输出

### Input类 ：获取 GET、POST 变量

请使用核心的 Input 类来获取 GET 和 POST 提交的变量，不要直接使用 $a = $_POST['xxxx'] 的方式来获取，可能造成 SQL 注入等安全问题。

```php
// 读取通过 POST 提交的字符串，默认值设置为空
$var_name = Input::postStrVar('var_name', '');
// 读取通过 POST 提交的数字类型，默认值设置为 0
$var_name = Input::postIntVar('var_name', 0);

// 读取通过 GET 提交的字符串，默认值设置为空
$var_name = Input::getStrVar('var_name', '');
// 读取通过 GET 提交的数字类型，默认值设置为 0
$var_name = Input::getIntVar('var_name', 0);

// 读取 POST 提交的数字类型的数组，如: name="ids[]"，默认值为：[]
$logs = Input::postIntArray('blog');
// 读取 POST 提交的字符串类型的数组，如: name="someting[]"，默认值为：[]
$logs = Input::postStrArray('blog');

// 读取通过 GET, POST, and COOKIE 提交的字符串，默认值设置为空
$var_name = Input::requestStrVar('var_name', '');
// 读取通过 GET, POST, and COOKIE 提交的数字类型，默认值设置为 0
$var_name = Input::requestNumVar('var_name', 0);
```

### Output类： json输出类

可以快捷的输出json格式的成功和错误数据结构

Output::ok();

```php
// 输出成功
Output::ok();

// 输出成功，带返回data数据
$data = ['id'=>1];
Output::ok($data);
```

Output::error();

```php
// 输出错误
Output::error('id error');

// 输出错误，返回http状态200
Output::error('id error', 200);
```

输出json格式：

```json
{
  "code": 0,
  "msg": "",
  "data": ""
}
```

## 获取系统设置

使用 Option 类的静态方法 get 可以获取系统的一些设置选项，如下：

```php
Option::get('att_maxsize') // 文件上传最大限制
Option::get('att_type') // 允许上传的文件类型
Option::get('att_imgmaxw') // 上传图片生成缩略图，最大尺寸:宽
Option::get('att_imgmaxh') // 上传图片生成缩略图，最大尺寸:高
```

## 获取URL路由

获取用户当前访问的URL路由信息， 方便主题等针对不同URL进行个性化输出。

```php
$D = Dispatcher::getInstance();
echo $D->_path; // 当前访问的URL路径
echo $D->_model; // 系统路由到的模块
echo $D->_params; // 系统路由捕获到的参数
```

## 对接支付

- 对接支付：安装并配置 [支付 SDK 插件](https://www.emlog.net/plugin/detail/726) 
  
## 订单系统

emlog核心内置了订单表及订单数据模型来管理订单，见： include/model/order_model.php

### 创建订单

```php
// app_name:应用名称，如主题或者插件的英文别名，用于区分不同应用的订单
// pay_type:支付类型  如：alipay、wechat、credit（积分支付）
// sku_name:商品类型，如资源、文章、积分、vip等：resource 、article 、credits、vip
// sku_id:商品 ID， 如：文章ID，资源id，等自定义商品ID
// price:价格
// UID :当前用户 ID
$pay_type = 'wechat';
$sku_name = 'app';
$sku_id = 1;
$price = 100;
$Order_Model = new Order_Model('app_name');
$order_id = $Order_Model->createOrder(UID, $pay_type, $sku_name, $sku_id, $price);
```

### 根据订单 ID 查询订单

```php
$order_id = Input::postStrVar('order_id');
$Order_Model = new Order_Model('app_name');
$order = $Order_Model->getOrderById($order_id);
```

### 更新订单状态

```php
$Order_Model = new Order_Model('app_name');
$data = [
    'order_id' => $out_trade_no, // 支付平台回调的订单号
    'pay_price' => $result['total_amount'], // 支付平台回调的支付金额
    'out_trade_no' => $result['trade_no'], // 支付平台回调的交易号
    'update_time' => time(),
];
$r = $Order_Model->updateOrder($out_trade_no, $data);
```

### 订单数据结构

以下表格展示了订单数据结构的字段及其对应的类型说明。

| 字段名           | 类型   | 说明                                             |
| ---------------- | ------ | ------------------------------------------------ |
| id               | 整数   | 订单ID                                           |
| order_uid        | 整数   | 用户ID                                           |
| sku_name         | 字符串 | 商品名称，如：resource 、app、 vip 、 article 等 |
| sku_id           | 整数   | 商品ID，如文章ID                                 |
| price            | 浮点数 | 订单价格                                         |
| pay_price        | 浮点数 | 支付金额                                         |
| refund_amount    | 浮点数 | 退款金额                                         |
| update_timestamp | 时间戳 | 更新时间戳                                       |
| create_timestamp | 时间戳 | 创建时间戳                                       |
| update_time      | 字符串 | 更新日期时间                                     |
| create_time      | 字符串 | 创建日期时间                                     |
| out_trade_no     | 字符串 | 外部交易号 ，如支付宝返回的流水号                |
| pay_type         | 字符串 | 支付方式，如：alipay、wechat                     |



## 对接 AI

### 获取文本对话模型配置

```php
// 返回：api_url、api_key、model
AI::getCurrentModelInfo();
```

### AI 对话

```php
$prompt = 'hello';
$response = AI::chat($prompt);
echo $response;
```

### 获取图像生成模型配置

```php
// 返回：api_url、api_key、model
AI::getCurrentImageModelInfo();
```

### 生成图像并保存到资源库

```php
$prompt = '画一只小猫';
$response = AI::generateImageAndSave($prompt);
echo $response;
```

## 获取用户信息

```php

$userModel = new User_Model();

// 获取用户信息
$user = $userModel->getOneUser($uid);

$avatar = $user['photo']; // 头像
$nickname = $user['nickname']; // 昵称
$credits = $user['credits']; // 积分

```

## 积分增加、扣减

```php

$userModel = new User_Model();

// 为用户增加积分
$userModel->addCredits($uid, 100);

// 扣减用户积分
$userModel->reduceCredits($uid, 50);

```

应用可以使用核心的数据库模型来查询和处理文章、资源、用户等数据，相关模型和方法可以参考 include/model 目录下Model类。

## 全局函数

### 发送邮件通知

```php
$mail = 'xxx@qq.com';
$title = '邮件标题';
$content = '邮件内容';
Notice::sendMail($mail, $title, $content);
```

### 截取指定长度的内容

```php
//第一个参数：要截取的内容
//第二个参数：截取长度
//第三个参数：是否过滤内容中的html标签 1过滤 0不过滤
//如下：截取日志内容的前180个字符，并过滤html标签

echo subContent($value['log_description'], 180, 1);

```

### 获取文章URL

```php
// 参数1：$article_id 文章ID
<?= Url::log($article_id)?>
```

### 获取文章分类页URL

```php
// 参数1：$sort_id 分类ID
<?= Url::sort($sort_id)?>
```

### 获取用户IP

```php
echo getIp();
```

### 获取内容中的第一张图片

```php
// 参数$content：文章内容，可以是 markdown 或者 html 格式内容
// 返回：图片的 URL
$imageUrl = getFirstImage($content);
```

### 获取用户的Gravatar头像

```php
// 参数：email
// 返回：头像 URL
$avatar = getGravatar($email);
```

### 友好的时间描述

```php
// 参数：$timestamp unix 时间戳
// 返回：友好的时间描述，如：1分钟前
echo smartDate($timestamp);
```

### 输出友好的提示信息

信息内容支持html

```php
emMsg('请先安装 <a href="/admin/store.php?keyword=支付sdk">支付SDK插件</a>');
```
