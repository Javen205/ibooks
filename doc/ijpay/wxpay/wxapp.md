### 文档

[小程序支付官方文档](https://pay.weixin.qq.com/wiki/doc/api/wxa/wxa_api.php?chapter=7_3&index=1)


### 接入步骤

1. 小程序内调用[登录接口](https://pay.weixin.qq.com/wiki/doc/api/wxa/wxa_api.php?chapter=7_4&index=3)，获取到用户的openid 
2. 调用支付[统一下单](https://pay.weixin.qq.com/wiki/doc/api/wxa/wxa_api.php?chapter=9_1&index=1)生成预付订单(参考`公用API`)
3. 预付订单[二次签名](https://pay.weixin.qq.com/wiki/doc/api/wxa/wxa_api.php?chapter=7_7&index=3)(参考`公用API`)
4. 小程序调起[支付API](https://pay.weixin.qq.com/wiki/doc/api/wxa/wxa_api.php?chapter=7_7&index=5)
5. 服务端异步通知处理(参考`服务端异步通知`)


[官方业务流程介绍](https://pay.weixin.qq.com/wiki/doc/api/wxa/wxa_api.php?chapter=7_4&index=3)

### 代码示例
https://gitee.com/javen205/IJPay-Demo
https://github.com/javen205/IJPay-Demo

```

	/**
	 * 微信小程序支付
	 */
	public void aappPay(){
		
		String ip = IpKit.getRealIp(getRequest());
		if (StrKit.isBlank(ip)) {
			ip = "127.0.0.1";
		}
		
		Map<String, String> params = WxPayApiConfigKit.getWxPayApiConfig()
				.setAttach("IJPay 测试  -By Javen")
				.setBody("IJPay 小程序支付测试  -By Javen")
				.setSpbillCreateIp(ip)
				.setTotalFee("100")
				.setTradeType(WxPayApi.TradeType.JSAPI)
				.setNotifyUrl(notify_url)
				.setOutTradeNo(String.valueOf(System.currentTimeMillis()))
				.build();
				
		String xmlResult =  WxPayApi.pushOrder(false,params);
		
log.info(xmlResult);
		Map<String, String> result = PaymentKit.xmlToMap(xmlResult);
		
		String return_code = result.get("return_code");
		String return_msg = result.get("return_msg");
		if (!PaymentKit.codeIsOK(return_code)) {
			ajax.addError(return_msg);
			renderJson(ajax);
			return;
		}
		String result_code = result.get("result_code");
		if (!PaymentKit.codeIsOK(result_code)) {
			ajax.addError(return_msg);
			renderJson(ajax);
			return;
		}
		// 以下字段在return_code 和result_code都为SUCCESS的时候有返回
		String prepay_id = result.get("prepay_id");
		//封装调起微信支付的参数https://pay.weixin.qq.com/wiki/doc/api/wxa/wxa_api.php?chapter=7_7&index=5
		Map<String, String> packageParams = new HashMap<String, String>();
		packageParams.put("appId", WxPayApiConfigKit.getWxPayApiConfig().getAppId());
		packageParams.put("timeStamp", System.currentTimeMillis() / 1000 + "");
		packageParams.put("nonceStr", System.currentTimeMillis() + "");
		packageParams.put("package", "prepay_id="+prepay_id);
		packageParams.put("signType", "MD5");
		String packageSign = PaymentKit.createSign(packageParams, WxPayApiConfigKit.getWxPayApiConfig().getPaternerKey());
		packageParams.put("paySign", packageSign);
		
		String jsonStr = JsonKit.toJson(packageParams);
log.info("最新返回小程序支付的参数:"+jsonStr);
		renderJson(jsonStr);
	}
```

