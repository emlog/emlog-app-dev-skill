---
title: '官网应用商店API'
sidebar_label: 🛍️ 应用商店API
sidebar_position: 5
---

# 🛍️ 官网应用商店API

开发者在官网应用商店售卖作品，产生订单，应用商店提供相关 API 来完成订单查询等操作。

## 接口鉴权

### 注册码授权

* 请求方式：POST/GET 和具体接口请求方式相同
* 鉴权所需参数:

| 参数    | 是否必填 | 描述                                                            |
|-------|------|---------------------------------------------------------------|
| emkey | 必填   | 开发者（用户）在官网的PRO注册码 （需[付费注册](https://www.emlog.net/register)获得） |

## 订单查询

* 通过订单号，在鉴权 emkey 对应的开发者（用户）售卖和购买的订单范围内查询订单信息。
* 接口URL：https://emlog.net/order/detail
* 请求方式：GET
* 接口鉴权方式：注册码授权
* 返回格式：JSON
* 请求参数：

| 参数       | 是否必填 | 描述  |
|----------|------|-----|
| order_id | 必填   | 订单号 |

返回结果

- 订单不存在返回空数组

```json
{
    "order_id": "652fd30b94kg4",
    "pay_type": "wechat",
    "pay_price": 9.9,
    "sku_name": "plugin",
    "sku_id": 600,
    "seller_uid": 5707,
    "refund_amount": 0,
    "status": "已支付"
}
```

| 参数            | 描述                         |
|---------------|----------------------------|
| order_id      | 订单号                        |
| pay_type      | 支付方式，wechat、 alipay        |
| pay_price     | 支付金额                       |
| sku_name      | 应用类型，插件 plugin，模板 template |
| sku_id        | 应用 ID                      |
| seller_uid    | 开发者 UID                    |
| refund_amount | 退款金额                       |
| status        | 订单状态：已支付、待支付、已退款           |

## 常见错误信息

- 报错返回格式：json

| 错误信息                                  | 描述   | http状态码 |
|---------------------------------------|------|---------|
| Incorrect developer registration code | 签名错误 | 200     |

### 错误返回示例

```json
{
    "code": 1001,
    "msg": "Incorrect developer registration code"
}
```

---

## PHP调用示例

```php
<?php

// 查询订单信息接口URL
$url = 'https://emlog.net/order/detail';

// 请求参数
$emkey = 'YOUR_DEVELOPER_REGISTRATION_CODE';  // 替换为你在官网获取的 pro 版本注册码
$order_id = '652fd30b94kg4';  // 替换为实际的订单号

// 构建请求参数数组
$params = [
    'emkey' => $emkey,
    'order_id' => $order_id,
];

// 构建请求URL
$request_url = $url . '?' . http_build_query($params);

// 发起请求
$response = file_get_contents($request_url);

// 处理返回结果
if ($response === false) {
    // 请求失败
    echo "请求失败";
} else {
    // 解析 JSON 格式的返回结果
    $result = json_decode($response, true);

    // 判断是否有错误信息
    if (isset($result['code'])) {
        // 输出错误信息
        echo "错误代码: " . $result['code'] . "\n";
        echo "错误消息: " . $result['msg'] . "\n";
    } else {
        // 输出订单信息
        echo "订单号: " . $result['order_id'] . "\n";
        echo "支付方式: " . $result['pay_type'] . "\n";
        echo "支付金额: " . $result['pay_price'] . "\n";
        echo "商品名称: " . $result['sku_name'] . "\n";
        echo "商品ID: " . $result['sku_id'] . "\n";
        echo "卖家UID: " . $result['seller_uid'] . "\n";
        echo "订单状态: " . $result['status'] . "\n";
    }
}
?>

```

