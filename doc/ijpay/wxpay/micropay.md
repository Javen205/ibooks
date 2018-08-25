### 刷卡支付

[刷卡支付官方文档](https://pay.weixin.qq.com/wiki/doc/api/micropay.php?chapter=5_1)

[微信开发之刷卡支付](http://blog.csdn.net/zyw_java/article/details/54024198)
### 测试

[刷卡支付验收用例](https://pay.weixin.qq.com/wiki/doc/api/micropay.php?chapter=23_11)

>注：仿真测试环境中的商户号（父子商户号）需使用真实商户号。

### IJPay 接口

```
	/**	
	 * 刷卡支付
	 * 服务商模式接入文档:https://pay.weixin.qq.com/wiki/doc/api/micropay_sl.php?chapter=9_10&index=1
	 * 商户模式接入文档:
	 * https://pay.weixin.qq.com/wiki/doc/api/micropay.php?chapter=9_10&index=1
	 * 
	 * @param isSandbox
	 *            是否是沙盒环境
	 * @param params
	 *            请求参数
	 * @return {String}
	 */
	public static String micropay(boolean isSandbox, Map<String, String> params) {
		if (isSandbox)
			return WxPayApi.doPost(MICROPAY_SANDBOXNEW_RUL, params);
		return WxPayApi.doPost(MICROPAY_URL, params);
	}
```

### 代码示例

https://gitee.com/javen205/IJPay-Demo

[JFinal 版Demo
](https://gitee.com/javen205/IJPay-Demo/blob/master/src/main/java/com/ijpay/controller/weixin/WxPayController.java)

```
	/**
	 * 刷卡支付
	 * 已测试
	 */
	public void micropay(){
		String auth_code = getPara("auth_code");
		String total_fee = getPara("total_fee");
		
		if (StrKit.isBlank(total_fee)) {
			ajax.addError("支付金额不能为空");
			renderJson(ajax);
			return;
		}
		if (StrKit.isBlank(auth_code)) {
			ajax.addError("auth_code参数错误");
			renderJson(ajax);
			return;
		}
		
		String ip = IpKit.getRealIp(getRequest());
		if (StrKit.isBlank(ip)) {
			ip = "127.0.0.1";
		}
		
		Map<String, String> params = WxPayApiConfigKit.getWxPayApiConfig()
				.setAttach("IJPay 测试  -By Javen")
				.setBody("IJPay 刷卡支付测试 -By Javen")
				.setSpbillCreateIp(ip)
				.setTotalFee(total_fee)
				.setAuthCode(auth_code)
				.setTradeType(TradeType.MICROPAY)
				.setNotifyUrl(notify_url)
				.setOutTradeNo(String.valueOf(System.currentTimeMillis()))
				.build();
				
		String xmlResult =  WxPayApi.micropay(false,params);
		
		//同步返回结果
		log.info("xmlResult:"+xmlResult);
		
		Map<String, String> result = PaymentKit.xmlToMap(xmlResult);
		String return_code = result.get("return_code");
		String return_msg = result.get("return_msg");
		if (!PaymentKit.codeIsOK(return_code)) {
			//通讯失败 
			String err_code = result.get("err_code");
			if (StrKit.notBlank(err_code)) {
				//用户支付中，需要输入密码
				if (err_code.equals("USERPAYING")) {
					//等待5秒后调用【查询订单API】https://pay.weixin.qq.com/wiki/doc/api/micropay.php?chapter=9_2
					
				}
			}
			log.info("提交刷卡支付失败>>"+xmlResult);
			ajax.addError(return_msg);
			renderJson(ajax);
			return;
		}
		
		String result_code = result.get("result_code");
		if (!PaymentKit.codeIsOK(result_code)) {
			//支付失败
			//支付失败
			log.info("支付失败>>"+xmlResult);
			String err_code_des = result.get("err_code_des");
			
			ajax.addError(err_code_des);
			renderJson(ajax);
			return;
		}
		
		//支付成功 
		
		renderText(xmlResult);
	}
```


[Spring Boot版Demo](https://gitee.com/javen205/IJPay-Demo/blob/SpringBoot/src/main/java/com/ijpay/controller/wxpay/WxPayController.java)

```
	/**
	 * 刷卡支付
	 * 已测试
	 */
	@RequestMapping(value = "/micropay",method= {RequestMethod.POST,RequestMethod.GET})
	@ResponseBody
	public AjaxResult micropay(HttpServletRequest request,HttpServletResponse response){
		String auth_code = request.getParameter("auth_code");
		String total_fee = request.getParameter("total_fee");
		
		if (StrKit.isBlank(total_fee)) {
			result.addError("支付金额不能为空");
			return result;
		}
		if (StrKit.isBlank(auth_code)) {
			result.addError("auth_code参数错误");
			return result;
		}
		
		String ip = IpKit.getRealIp(request);
		if (StrKit.isBlank(ip)) {
			ip = "127.0.0.1";
		}
		
		Map<String, String> params = WxPayApiConfigKit.getWxPayApiConfig()
				.setAttach("IJPay 测试  -By Javen")
				.setBody("IJPay 刷卡支付测试 -By Javen")
				.setSpbillCreateIp(ip)
				.setTotalFee(total_fee)
				.setAuthCode(auth_code)
				.setTradeType(TradeType.MICROPAY)
				.setNotifyUrl(notify_url)
				.setOutTradeNo(String.valueOf(System.currentTimeMillis()))
				.build();
				
		String xmlResult =  WxPayApi.micropay(false,params);
		
		//同步返回结果


		log.info("xmlResult:"+xmlResult);
		
		Map<String, String> resultMap = PaymentKit.xmlToMap(xmlResult);
		String return_code = resultMap.get("return_code");
		String return_msg = resultMap.get("return_msg");
		if (!PaymentKit.codeIsOK(return_code)) {
			//通讯失败 


			String err_code = resultMap.get("err_code");
			if (StrKit.notBlank(err_code)) {
				//用户支付中，需要输入密码


				if (err_code.equals("USERPAYING")) {
					//等待5秒后调用【查询订单API】https://pay.weixin.qq.com/wiki/doc/api/micropay.php?chapter=9_2


					
				}
			}
			log.info("提交刷卡支付失败>>"+xmlResult);
			result.addError(return_msg);
			return result;
		}
		
		String result_code = resultMap.get("result_code");
		if (!PaymentKit.codeIsOK(result_code)) {
			//支付失败

			log.info("支付失败>>"+xmlResult);
			String err_code_des = resultMap.get("err_code_des");
			
			result.addError(err_code_des);
			return result;
		}
		//支付成功 

		result.success(xmlResult);
		return result;
	}
```