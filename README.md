# pay
微信支付、支付宝支付

**安装**
```
composer require woodlsy/pay
```

### 一、alipay
- 配置
```php
$alipayConfig = [
    'appId' => '2016615151551515',
    'privateKeyFilePath' => '/config/rsa/alipay/rsa_private_key.pem', // 私钥地址
    'alipayPublicKey' => 'MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAuo2LF+uJ7D+3Qb6PwFU2....' // 阿里公钥
    'notify_url' => 'https://xxxxxx.com/pay/notify/alipay.json',
    'sigin_type' => 'RSA2',
];
```
- App 发起支付
```php
$alipay = (new Alipay($alipayConfig));
$alipay->setAmount('0.01');
$alipay->setSubject('商品订单');
$alipay->setOrderSn('DD'.time()); // 订单号
$alipay->setBackParams(['orderTyoe'=>1,'orderId'=>'111111']);
// 返回的$string给app sdk发起支付
$string = $alipay->appPay();
```

- 异步通知回调
```php
$alipay = (new Alipay($alipayConfig));
$res = $alipay->rsaCheckV1($params);
if (false === $res) {
    echo '验签失败';
}
```
- 退款
```php
$aliRefund = (new AliRefund($alipayConfig));
$aliRefund->setAmount('0.01');
$aliRefund->setTradeNo('201908012200142034056540'); // 支付宝单号
$aliRefund->setOutTradeNo('2019080122001420'); // 商家单号  和支付宝单号二选一
$aliRefund->setOutRequestNo('201912050101'); // 退款单号
$refundInfo = $aliRefund->refund(); // $refundInfo是一个对象
```

### 二、wechat

- 配置
```php
$config = [
    'app_id' => 'xxxxxxxxxxxx',
    'mch_id' => '111111111111111',
    'key' => 'xxxxxxxxxxxxxxxxxxxx',
    'notify_url' => 'https://xxxxxxxxx/pay/notify/wechat.json',
    'sigin_type' => 'HMAC-SHA256',
    'SSLCert'    => APP_PATH . '/rsa/wechat/apiclient_cert.pem',
    'SSLKey'     => APP_PATH . '/rsa/wechat/apiclient_key.pem',
];
```
- APP发起支付
```php
$wechatpay = (new Wechatpay($config));
$wechatpay->goUnifiedorder();
$wechatpay->setAttach(xxxxx));
$wechatpay->setSubject(商品订单);
$wechatpay->setAmount(1);
$wechatpay->setOutTradeNo(xxxxxxxxxx);
/*******若是JSAPI发起支付 start*********/
$wechatpay->setOpenId('openid');
$wechatpay->setTradeType('JSAPI');
/*******若是JSAPI发起支付 end*********/      
$res = $wechatpay->appPay();
if ('SUCCESS' !== (string) $res['return_code'] || 'SUCCESS' !== (string) $res['result_code']) {
    echo '发起支付失败';
}
```

- 异步通知回调
```php
$wechatpay = (new Wechatpay($config));
if (false === $wechatpay->verifySign($params)) {
    echo '验签失败';
}
```

- 退款
```php
$wechatpay = (new WechatRefund($config));
$wechatpay->setTransactionId('420000038520190801775231'); // 微信交易号
$wechatpay->setOutRefundNo('201912050101'); // 退款单号
$wechatpay->setTotalFee('9900'); // 原订单总金额，单位分
$wechatpay->setRefundFee('2000'); // 退款金额
$refundInfo = $wechatpay->execute(); // $refundInfo为数组
```

