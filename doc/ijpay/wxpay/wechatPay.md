### 公众号支付

[公众号支付官方文档](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=9_1)

[微信开发之公众号支付](http://blog.csdn.net/zyw_java/article/details/54023968)

### 开发步骤

1. 设置支付目录
2. 设置授权域名
3. 授权获取用户信息(openId未知)
4. 统一下单
5. 微信内H5调起支付
6. 支付结果通知

> 建议支付目录设置到支付URL上一级目录


开发步骤-[官方介绍文档](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_3)

### 获取openId
[授权获取用户信息官方文档](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140842)
[微信公众号开发之授权获取用户信息](http://blog.csdn.net/zyw_java/article/details/61415123)


### 统一下单

调用统一下单接口后生成预付订单 参考`公用API`

### 微信内H5调起支付

对生成的预付订单进行二次签名再使用JS API唤起支付 参考`公用API` 

JS部分代码参考-[IJPay-Demo](https://gitee.com/javen205/IJPay-Demo/blob/master/src/main/webapp/WEB-INF/_views/wxpay.html)



### 示例代码

https://gitee.com/javen205/IJPay-Demo

[Spring Boot版本Demo](https://gitee.com/javen205/IJPay-Demo/blob/SpringBoot/src/main/java/com/ijpay/controller/wxpay/WxPayController.java)


```
	/**
	 * 公众号支付
	 */
	@RequestMapping(value ="/webPay",method = {RequestMethod.POST,RequestMethod.GET})
	@ResponseBody
	public AjaxResult webPay(HttpServletRequest request,HttpServletResponse response,
			@RequestParam("total_fee") String total_fee) {
		// openId，采用 网页授权获取 access_token API：SnsAccessTokenApi获取

		String openId = (String) request.getSession().getAttribute("openId");
		
		if (StrKit.isBlank(openId)) {
			result.addError("openId is null");
			return result;
		}
		if (StrKit.isBlank(total_fee)) {
			result.addError("请输入数字金额");
			return result;
		}
		
		String ip = IpKit.getRealIp(request);
		if (StrKit.isBlank(ip)) {
			ip = "127.0.0.1";
		}
		
		Map<String, String> params = WxPayApiConfigKit.getWxPayApiConfig()
				.setAttach("IJPay 公众号支付测试  -By Javen")
				.setBody("IJPay 公众号支付测试  -By Javen")
				.setOpenId(openId)
				.setSpbillCreateIp(ip)
				.setTotalFee(total_fee)
				.setTradeType(TradeType.JSAPI)
				.setNotifyUrl(notify_url)
				.setOutTradeNo(String.valueOf(System.currentTimeMillis()))
				.build();
		
		String xmlResult = WxPayApi.pushOrder(false,params);
log.info(xmlResult);
		Map<String, String> resultMap = PaymentKit.xmlToMap(xmlResult);
		
		String return_code = resultMap.get("return_code");
		String return_msg = resultMap.get("return_msg");
		if (!PaymentKit.codeIsOK(return_code)) {
			result.addError(return_msg);
			return result;
		}
		String result_code = resultMap.get("result_code");
		if (!PaymentKit.codeIsOK(result_code)) {
			result.addError(return_msg);
			return result;
		}
		// 以下字段在return_code 和result_code都为SUCCESS的时候有返回


		String prepay_id = resultMap.get("prepay_id");
		
		Map<String, String> packageParams = PaymentKit.prepayIdCreateSign(prepay_id);
		
		String jsonStr = JSON.toJSONString(packageParams);
		result.success(jsonStr);
		return result;
	}
```

[Jfinal版本Demo](https://gitee.com/javen205/IJPay-Demo/blob/master/src/main/java/com/ijpay/controller/weixin/WxPayController.java)

```
public void webPay() {
		
		// openId，采用 网页授权获取 access_token API：SnsAccessTokenApi获取
		String openId = (String) getSession().getAttribute("openId");
		
		String total_fee=getPara("total_fee");
		
		if (StrKit.isBlank(openId)) {
			ajax.addError("openId is null");
			renderJson(ajax);
			return;
		}
		if (StrKit.isBlank(total_fee)) {
			ajax.addError("请输入数字金额");
			renderJson(ajax);
			return;
		}
		
		String ip = IpKit.getRealIp(getRequest());
		if (StrKit.isBlank(ip)) {
			ip = "127.0.0.1";
		}
		
		Map<String, String> params = WxPayApiConfigKit.getWxPayApiConfig()
				.setAttach("IJPay 公众号支付测试  -By Javen")
				.setBody("IJPay 公众号支付测试  -By Javen")
				.setOpenId(openId)
				.setSpbillCreateIp(ip)
				.setTotalFee(total_fee)
				.setTradeType(TradeType.JSAPI)
				.setNotifyUrl(notify_url)
				.setOutTradeNo(String.valueOf(System.currentTimeMillis()))
				.build();
		
		String xmlResult = WxPayApi.pushOrder(false,params);
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
		
		Map<String, String> packageParams = PaymentKit.prepayIdCreateSign(prepay_id);
		
		String jsonStr = JsonKit.toJson(packageParams);
		ajax.success(jsonStr);
		renderJson(ajax);
	}
```