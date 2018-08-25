### Map转xml

请求微信接口时将Map参数转化为xml

```
PaymentKit.toXml(Map<String, String> params)
```

### xml转Map

将微信接口响应的结果xml转化为Map

```
PaymentKit.xmlToMap(xml);
```

### 获取IP

```
IpKit.getRealIp(HttpServletRequest request);
```

### 创建签名

```
PaymentKit.createSign(Map<String, String> params, String partnerKey);
```

`params`为加密的参数,`partnerKey`为商户平台设置的`API密钥key`

> key设置路径：微信商户平台\(pay.weixin.qq.com\)--&gt;账户设置--&gt;API安全--&gt;密钥设置

### 统一下单

[统一下单接口官方文档](https://pay.weixin.qq.com/wiki/doc/api/native.php?chapter=9_1)

```
WxPayApi.pushOrder(boolean isSandbox, Map<String, String> params);
```

> 代码示例参考具体支付方式中的代码

### 预付订单再次签名

##### 公众号/小程序支付

公众号支付二次签名-[微信内H5调起支付](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7&index=6)  
小程序支付二次签名-[小程序调起支付API](https://pay.weixin.qq.com/wiki/doc/api/wxa/wxa_api.php?chapter=7_7&index=5)

```
PaymentKit.prepayIdCreateSign(String prepay_id)
```

```
    /**
     * 预付订单再次签名
     * @param prepay_id
     * @return <Map<String, String>>
     */
    public static Map<String, String> prepayIdCreateSign(String prepay_id) {
        Map<String, String> packageParams = new HashMap<String, String>();
        packageParams.put("appId", WxPayApiConfigKit.getWxPayApiConfig().getAppId());
        packageParams.put("timeStamp", String.valueOf(System.currentTimeMillis() / 1000));
        packageParams.put("nonceStr", String.valueOf(System.currentTimeMillis()));
        packageParams.put("package", "prepay_id=" + prepay_id);
        packageParams.put("signType", "MD5");
        String packageSign = PaymentKit.createSign(packageParams, WxPayApiConfigKit.getWxPayApiConfig().getPaternerKey());
        packageParams.put("paySign", packageSign);
        return packageParams;
    }
```

##### APP支付

APP支付二次签名-[调起支付](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_12)

```
    /**
     * 预付订单再次签名
     * @param prepay_id
     * @return <Map<String, String>>
     */
    public static Map<String, String> prepayIdCreateSignByApp(String prepay_id) {
        Map<String, String> packageParams = new HashMap<String, String>();
        packageParams.put("appid", WxPayApiConfigKit.getWxPayApiConfig().getAppId());
        packageParams.put("partnerid", WxPayApiConfigKit.getWxPayApiConfig().getMchId());
        packageParams.put("prepayid", prepay_id);
        packageParams.put("package", "Sign=WXPay");
        packageParams.put("noncestr", System.currentTimeMillis() + "");
        packageParams.put("timestamp", System.currentTimeMillis() / 1000 + "");
        String packageSign = PaymentKit.createSign(packageParams, WxPayApiConfigKit.getWxPayApiConfig().getPaternerKey());
        packageParams.put("sign", packageSign);
        return packageParams;
    }
```

### 异步通知验证签名

```
PaymentKit.verifyNotify(Map<String, String> params, String paternerKey)
```

`params`为回调的xml转Map参数,`partnerKey`为商户平台设置的`API密钥key`

> key设置路径：微信商户平台\(pay.weixin.qq.com\)--&gt;账户设置--&gt;API安全--&gt;密钥设置



