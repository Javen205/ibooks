### 文档&博客

[微信扫码支付官方文档](https://pay.weixin.qq.com/wiki/doc/api/native.php?chapter=6_1)  
[微信开发之扫码支付](http://blog.csdn.net/zyw_java/article/details/54024162)

### 扫码支付模式介绍

* 模式一
  > 模式一开发前，商户必须在公众平台后台设置支付回调URL。URL实现的功能：**接收用户扫码后微信支付系统回调的productid和openid**；URL设置详见[回调地址设置](https://pay.weixin.qq.com/wiki/doc/api/native.php?chapter=6_3)。
* 模式二
  > 模式二与模式一相比，流程更为简单，不依赖设置的回调支付URL。商户后台系统先调用微信支付的**统一下单接口**，微信后台系统返回链接参数code\_url，商户后台系统将code\_url值生成二维码图片，用户使用微信客户端扫码后发起支付。注意：code\_url有效期为2小时，过期后扫码不能再发起支付。

模式一与模式二的区别

* 二维码生成场景不同
  > 模式一是根据微信支付规定格式生成二维码展示给用户扫码，模式二是通过统一下单生成预付订单以及code\_url其中code\_url就是生成二维码的URL。

* 授权获取用户openId

  > 模式一不需要而模式二需要授权获取用户的openId

* 二维码有效期

> 模式一永久有效而模式二有效期为2小时

### 接入步骤

* 模式一

  > 1. 根据微信生成二维码规则生成二维码
  > 2. 回调商户支付URL
  > 3. 调用统一下单接口提交支付交易
  > 4. 处理支付结果

* 模式二

  > 1. 授权获取用户的openId
  > 2. 调用统一下单接口生成预支付交易
  > 3. 根据统一下单接口返回code\_url生成二维码
  > 4. 处理支付结果

### 生成二维码

> 模式一

```
//根据生成二维码规则生成二维码URL
String qrCodeUrl=WxPayApi.getCodeUrl(config.getAppId(), config.getMchId(),product_id, config.getPaternerKey(), true);
//使用第三方工具生成二维码
ZxingKit.encode(qrCodeUrl, BarcodeFormat.QR_CODE, 3, ErrorCorrectionLevel.H, "png", 200, 200,        PathKit.getWebRootPath()+File.separator+name );
```

生成二维码具体实现

```
    /**
     * 扫码模式一之生成二维码
     * 
     * @param appid
     * @param mch_id
     * @param product_id
     * @param partnerKey
     * @param isToShortUrl
     *            是否转化为短连接
     * @return {String}
     */
    public static String getCodeUrl(String appid, String mch_id, String product_id, String partnerKey,
            boolean isToShortUrl) {
        String url = "weixin://wxpay/bizpayurl?sign=XXXXX&appid=XXXXX&mch_id=XXXXX&product_id=XXXXX&time_stamp=XXXXX&nonce_str=XXXXX";
        String timeStamp = Long.toString(System.currentTimeMillis() / 1000);
        String nonceStr = Long.toString(System.currentTimeMillis());
        Map<String, String> packageParams = new HashMap<String, String>();
        packageParams.put("appid", appid);
        packageParams.put("mch_id", mch_id);
        packageParams.put("product_id", product_id);
        packageParams.put("time_stamp", timeStamp);
        packageParams.put("nonce_str", nonceStr);
        String packageSign = PaymentKit.createSign(packageParams, partnerKey);
        String qrCodeUrl = PaymentKit.replace(url, "XXXXX", packageSign, appid, mch_id, product_id, timeStamp,
                nonceStr);
        if (isToShortUrl) {
            String shortResult = WxPayApi
                    .toShortUrl(PaymentKit.buildShortUrlParasMap(appid, null, mch_id, null, qrCodeUrl, partnerKey));
            Map<String, String> shortMap = PaymentKit.xmlToMap(shortResult);
            String return_code = shortMap.get("return_code");
            if (PaymentKit.codeIsOK(return_code)) {
                String result_code = shortMap.get("result_code");
                if (PaymentKit.codeIsOK(result_code)) {
                    qrCodeUrl = shortMap.get("short_url");
                }
            }
        }

        return qrCodeUrl;
    }
```

> 模式二

模式二生成二维码步骤请参考`公用API`中的统一下单接口，示例代码请参考  
下文`代码示例`

### 代码示例

[https://gitee.com/javen205/IJPay-Demo](https://gitee.com/javen205/IJPay-Demo)

> 模式一

```
    /**
     * 生成支付二维码（模式一）并在页面上显示
     */
    public void scanCode1(){
        try {
            String product_id = getPara("productId");
            if (StrKit.isBlank(product_id)) {
                ajax.addError("productId is null");
                renderJson(ajax);
                return;
            }
            WxPayApiConfig config = WxPayApiConfigKit.getWxPayApiConfig();
            //获取扫码支付（模式一）url
            String qrCodeUrl=WxPayApi.getCodeUrl(config.getAppId(), config.getMchId(),product_id, config.getPaternerKey(), true);
            log.info(qrCodeUrl);
            //生成二维码保存的路径
            String name = "payQRCode1.png";
            Boolean encode = ZxingKit.encode(qrCodeUrl, BarcodeFormat.QR_CODE, 3, ErrorCorrectionLevel.H, "png", 200, 200,
                    PathKit.getWebRootPath()+File.separator+name );
            if (encode) {
                //在页面上显示
                ajax.success(name);
                renderJson(ajax);
            }
        } catch (Exception e) {
            ajax.addError("系统异常："+e.getMessage());
            renderJson(ajax);
            e.printStackTrace();
        }
    }
```

```
    /**
     * 扫码支付模式一回调
     * 已测试
     */
    public void wxpay(){
        try {

            String result  = HttpKit.readData(getRequest());
            System.out.println("callBack_xml>>>"+result);
            /**
             * 获取返回的信息内容中各个参数的值
             */
            Map<String, String> map = PaymentKit.xmlToMap(result);
            for (String key : map.keySet()) {
                   System.out.println("key= "+ key + " and value= " + map.get(key));
            }

            String appid=map.get("appid");
            String openid = map.get("openid");
            String mch_id = map.get("mch_id");
            String is_subscribe = map.get("is_subscribe");
            String nonce_str = map.get("nonce_str");
            String product_id = map.get("product_id");
            String sign = map.get("sign");
            Map<String, String> packageParams = new HashMap<String, String>();
            packageParams.put("appid", appid);
            packageParams.put("openid", openid);
            packageParams.put("mch_id",mch_id);
            packageParams.put("is_subscribe",is_subscribe);
            packageParams.put("nonce_str",nonce_str);
            packageParams.put("product_id", product_id);

            String packageSign = PaymentKit.createSign(packageParams, WxPayApiConfigKit.getWxPayApiConfig().getPaternerKey());
            // 统一下单文档地址：https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=9_1


            String ip = IpKit.getRealIp(getRequest());
            if (StrKit.isBlank(ip)) {
                ip = "127.0.0.1";
            }

            Map<String, String> params = WxPayApiConfigKit.getWxPayApiConfig()
                    .setAttach("IJPay 扫码模式一测试  -By Javen")
                    .setBody("IJPay 扫码模式一测试  -By Javen")
                    .setOpenId(openid)
                    .setSpbillCreateIp(ip)
                    .setTotalFee("100")
                    .setTradeType(TradeType.NATIVE)
                    .setNotifyUrl(notify_url)
                    .setOutTradeNo(String.valueOf(System.currentTimeMillis()))
                    .build();

            String xmlResult = WxPayApi.pushOrder(false,params);
            log.info("prepay_xml>>>"+xmlResult);

            /**
             * 发送信息给微信服务器
             */
            Map<String, String> payResult = PaymentKit.xmlToMap(xmlResult);

            String return_code = payResult.get("return_code");
            String result_code = payResult.get("result_code");

            if (StrKit.notBlank(return_code) && StrKit.notBlank(result_code) && return_code.equalsIgnoreCase("SUCCESS")&&result_code.equalsIgnoreCase("SUCCESS")) {
                // 以下字段在return_code 和result_code都为SUCCESS的时候有返回
                String prepay_id = payResult.get("prepay_id");

                Map<String, String> prepayParams = new HashMap<String, String>();
                prepayParams.put("return_code", "SUCCESS");
                prepayParams.put("appId", appid);
                prepayParams.put("mch_id", mch_id);
                prepayParams.put("nonceStr", System.currentTimeMillis() + "");
                prepayParams.put("prepay_id", prepay_id);
                String prepaySign = null;
                if (sign.equals(packageSign)) {
                    prepayParams.put("result_code", "SUCCESS");
                }else {
                    prepayParams.put("result_code", "FAIL");
                    prepayParams.put("err_code_des", "订单失效");   //result_code为FAIL时，添加该键值对，value值是微信告诉客户的信息
                }
                prepaySign = PaymentKit.createSign(prepayParams, WxPayApiConfigKit.getWxPayApiConfig().getPaternerKey());
                prepayParams.put("sign", prepaySign);
                String xml = PaymentKit.toXml(prepayParams);
                log.error(xml);
                renderText(xml);

            }
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
```

> 模式二

```
    /**
     * 扫码支付模式二
     */
    public void scanCode2() {    
        //String openId="o5NJx1dVRilQI6uUVSaBDuLnM3iM";
        String openId = (String) getSession().getAttribute("openId");

        String total_fee=getPara("total_fee");

        if (StrKit.isBlank(openId)) {
            ajax.addError("openId is null");
            renderJson(ajax);
            return;
        }
        if (StrKit.isBlank(total_fee)) {
            ajax.addError("支付金额不能为空");
            renderJson(ajax);
            return;
        }

        String ip = IpKit.getRealIp(getRequest());
        if (StrKit.isBlank(ip)) {
            ip = "127.0.0.1";
        }

        Map<String, String> params = WxPayApiConfigKit.getWxPayApiConfig()
                .setAttach("IJPay 测试  -By Javen")
                .setBody("IJPay 扫码支付2测试  -By Javen")
                .setOpenId(openId)
                .setSpbillCreateIp(ip)
                .setTotalFee(total_fee)
                .setTradeType(TradeType.NATIVE)
                .setNotifyUrl(notify_url)
                .setOutTradeNo(String.valueOf(System.currentTimeMillis()))
                .build();

        String xmlResult = WxPayApi.pushOrder(false,params);

log.info(xmlResult);
        Map<String, String> result = PaymentKit.xmlToMap(xmlResult);

        String return_code = result.get("return_code");
        String return_msg = result.get("return_msg");
        if (!PaymentKit.codeIsOK(return_code)) {
            System.out.println(return_msg);
            renderText(xmlResult);
            return;
        }
        String result_code = result.get("result_code");
        if (!PaymentKit.codeIsOK(result_code)) {
            System.out.println(return_msg);
            renderText(xmlResult);
            return;
        }
        //生成预付订单success

        String qrCodeUrl = result.get("code_url");
        String name = "payQRCode2.png";

        Boolean encode = ZxingKit.encode(qrCodeUrl, BarcodeFormat.QR_CODE, 3, ErrorCorrectionLevel.H, "png", 200, 200,
                PathKit.getWebRootPath()+File.separator+name);
        if (encode) {
//            renderQrCode(qrCodeUrl, 200, 200);
            //在页面上显示
            ajax.success(name);
            renderJson(ajax);
        }
    }
```



