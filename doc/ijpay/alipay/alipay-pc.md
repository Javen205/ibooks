# 支付宝电脑网站支付

电脑网站支付又称PC支付

### 官方接入文档

https://docs.open.alipay.com/270

### IJPay中的接口

```
	/**
	 * 电脑网站支付(PC支付)
	 * @param httpResponse
	 * @param model
	 * @param notifyUrl
	 * @param returnUrl
	 * @throws {AlipayApiException}
	 * @throws IOException
	 */
	public static void tradePage(HttpServletResponse httpResponse, AlipayTradePayModel model, String notifyUrl, String returnUrl) throws AlipayApiException, IOException{
		AlipayTradePagePayRequest request = new AlipayTradePagePayRequest();
		request.setBizModel(model);
		request.setNotifyUrl(notifyUrl);
		request.setReturnUrl(returnUrl);
		String form  = AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().pageExecute(request).getBody();//调用SDK生成表单
		httpResponse.setContentType("text/html;charset=" + AliPayApiConfigKit.getAliPayApiConfig().getCharset());
		httpResponse.getWriter().write(form);//直接将完整的表单html输出到页面
	    httpResponse.getWriter().flush();
	    httpResponse.getWriter().close();
	}
```

### 代码示例

https://github.com/Javen205/IJPay-Demo


[Spring Boot版本Demo](https://gitee.com/javen205/IJPay-Demo/blob/SpringBoot/src/main/java/com/ijpay/controller/alipay/AliPayController.java)


```
	/**
	 * PC支付
	 */
	@RequestMapping(value = "/pcPay") 
	@ResponseBody
	public void pcPay(HttpServletResponse response){
		try {
			String totalAmount = "88.88"; 
			String outTradeNo =StringUtils.getOutTradeNo();
			log.info("pc outTradeNo>"+outTradeNo);
			
			String returnUrl = aliPayBean.getDomain() + "/alipay/return_url";
			String notifyUrl = aliPayBean.getDomain() + "/alipay/notify_url";
			AlipayTradePayModel model = new AlipayTradePayModel();
			
			model.setOutTradeNo(outTradeNo);
			model.setProductCode("FAST_INSTANT_TRADE_PAY");
			model.setTotalAmount(totalAmount);
			model.setSubject("Javen PC支付测试");
			model.setBody("Javen IJPay PC支付测试");
			
			AliPayApi.tradePage(response,model , notifyUrl, returnUrl);
		} catch (Exception e) {
			e.printStackTrace();
		}
		
	}
```

[JFinal版本Demo](https://gitee.com/javen205/IJPay-Demo/blob/master/src/main/java/com/ijpay/controller/alipay/AliPayController.java)

```
	/**
	 * PC支付
	 */
	public void pcPay(){
		try {
			String totalAmount = "88.88"; 
			String outTradeNo =StringUtils.getOutTradeNo();
			log.info("pc outTradeNo>"+outTradeNo);
			
			String returnUrl = notify_domain + "/alipay/return_url";
			String notifyUrl = notify_domain + "/alipay/notify_url";
			AlipayTradePayModel model = new AlipayTradePayModel();
			
			model.setOutTradeNo(outTradeNo);
			model.setProductCode("FAST_INSTANT_TRADE_PAY");
			model.setTotalAmount(totalAmount);
			model.setSubject("Javen PC支付测试");
			model.setBody("Javen IJPay PC支付测试");
			//花呗分期相关的设置

			/**
			 * 测试环境不支持花呗分期的测试
			 * hb_fq_num代表花呗分期数，仅支持传入3、6、12，其他期数暂不支持，传入会报错；
			 * hb_fq_seller_percent代表卖家承担收费比例，商家承担手续费传入100，用户承担手续费传入0，仅支持传入100、0两种，其他比例暂不支持，传入会报错。
			 */
//			ExtendParams extendParams = new ExtendParams();
//			extendParams.setHbFqNum("3");
//			extendParams.setHbFqSellerPercent("0");
//			model.setExtendParams(extendParams);

			
			AliPayApi.tradePage(getResponse(),model , notifyUrl, returnUrl);
			renderNull();
		} catch (Exception e) {
			e.printStackTrace();
		}
		
	}
```

