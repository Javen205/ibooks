# 支付宝扫码支付


具体流程这里就不详细介绍了，请参考之前写的一篇文章 [支付宝支付-扫码支付详解](http://blog.csdn.net/zyw_java/article/details/71437269)


### IJPay 接口

```
	/**
	 * 统一收单线下交易预创建
	 * 扫码支付
	 * @param model
	 * @param notifyUrl
	 * @return {String}
	 * @throws {AlipayApiException}
	 */
	public static String tradePrecreatePay(AlipayTradePrecreateModel model, String notifyUrl) throws AlipayApiException{
		AlipayTradePrecreateResponse response = tradePrecreatePayToResponse(model,notifyUrl);
		return response.getBody();
	}
	/**
	 * 扫码支付
	 * @param model
	 * @param notifyUrl
	 * @return {AlipayTradePrecreateResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayTradePrecreateResponse tradePrecreatePayToResponse(AlipayTradePrecreateModel model, String notifyUrl) throws AlipayApiException{
		AlipayTradePrecreateRequest request = new AlipayTradePrecreateRequest();
		request.setBizModel(model);
		request.setNotifyUrl(notifyUrl);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request);
	}
```

### 代码示例
https://github.com/Javen205/IJPay-Demo



```
	/**
	 * 扫码支付
	 */
	@RequestMapping(value ="/tradePrecreatePay")
	@ResponseBody
	public String tradePrecreatePay() {
		String subject = "Javen 支付宝扫码支付测试";
		String totalAmount = "86";
		String storeId = "123";
		String notifyUrl = aliPayBean.getDomain() + "/alipay/notify_url";

		AlipayTradePrecreateModel model = new AlipayTradePrecreateModel();
		model.setSubject(subject);
		model.setTotalAmount(totalAmount);
		model.setStoreId(storeId);
		model.setTimeoutExpress("5m");
		model.setOutTradeNo(StringUtils.getOutTradeNo());
		try {
			String resultStr = AliPayApi.tradePrecreatePay(model, notifyUrl);
			JSONObject jsonObject = JSONObject.parseObject(resultStr);
			return jsonObject.getJSONObject("alipay_trade_precreate_response").getString("qr_code");
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}
```