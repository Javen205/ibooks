
# 支付宝提现

支付宝提现又称**单笔转账到支付宝账户**

### 官方接入文档

https://docs.open.alipay.com/309/106235

### IJPay 接口

```
	/**
	 * 单笔转账到支付宝账户
	 * @param model
	 * @return {boolean}
	 * @throws {AlipayApiException}
	 */
	public static boolean transfer(AlipayFundTransToaccountTransferModel model) throws AlipayApiException{
		AlipayFundTransToaccountTransferResponse response = transferToResponse(model);
		String result = response.getBody();
		if (response.isSuccess()) {
			return true;
		} else {
			//调用查询接口查询数据
			JSONObject jsonObject = JSONObject.parseObject(result);
			String out_biz_no = jsonObject.getJSONObject("alipay_fund_trans_toaccount_transfer_response").getString("out_biz_no");
			AlipayFundTransOrderQueryModel queryModel = new AlipayFundTransOrderQueryModel();
			model.setOutBizNo(out_biz_no);
			boolean isSuccess = transferQuery(queryModel);
			if (isSuccess) {
				return true;
			}
		}
		return false;
	}
	/**
	 * 单笔转账到支付宝账户
	 * @param model
	 * @return {AlipayFundTransToaccountTransferResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayFundTransToaccountTransferResponse transferToResponse(AlipayFundTransToaccountTransferModel model) throws AlipayApiException{
		AlipayFundTransToaccountTransferRequest request = new AlipayFundTransToaccountTransferRequest();
		request.setBizModel(model);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request);
	}
	
	/**
	 * 转账查询接口
	 * @param model
	 * @return {boolean}
	 * @throws {AlipayApiException}
	 */
	public static boolean transferQuery(AlipayFundTransOrderQueryModel model) throws AlipayApiException{
		AlipayFundTransOrderQueryResponse response = transferQueryToResponse(model);
		if(response.isSuccess()){
			return true;
		}
		return false;
	}
	/**
	 * 转账查询接口
	 * @param model
	 * @return {AlipayFundTransOrderQueryResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayFundTransOrderQueryResponse transferQueryToResponse(AlipayFundTransOrderQueryModel model) throws AlipayApiException{
		AlipayFundTransOrderQueryRequest request = new AlipayFundTransOrderQueryRequest();
		request.setBizModel(model);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request);
	}
```

### 代码示例

https://gitee.com/javen205/IJPay-Demo
https://github.com/javen205/IJPay-Demo

[Spring Boot版本Demo](https://gitee.com/javen205/IJPay-Demo/blob/SpringBoot/src/main/java/com/ijpay/controller/alipay/AliPayController.java)

```
	/**
	 * 单笔转账到支付宝账户
	 */
	@RequestMapping(value = "/transfer")
	@ResponseBody
	public boolean transfer() {
		boolean isSuccess = false;
		String total_amount = "66";
		AlipayFundTransToaccountTransferModel model = new AlipayFundTransToaccountTransferModel();
		model.setOutBizNo(StringUtils.getOutTradeNo());
		model.setPayeeType("ALIPAY_LOGONID");
		model.setPayeeAccount("abpkvd0206@sandbox.com");
		model.setAmount(total_amount);
		model.setPayerShowName("测试退款");
		model.setPayerRealName("沙箱环境");
		model.setRemark("javen测试单笔转账到支付宝");

		try {
			isSuccess = AliPayApi.transfer(model);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return isSuccess;
	}
```


[Jfinal 版本Demo](https://gitee.com/javen205/IJPay-Demo/blob/master/src/main/java/com/ijpay/controller/alipay/AliPayController.java)

```
	/**
	 * 单笔转账到支付宝账户
	 */
	public void transfer() {
		boolean isSuccess = false;
		String total_amount = "66";
		AlipayFundTransToaccountTransferModel model = new AlipayFundTransToaccountTransferModel();
		model.setOutBizNo(StringUtils.getOutTradeNo());
		model.setPayeeType("ALIPAY_LOGONID");
		model.setPayeeAccount("abpkvd0206@sandbox.com");
		model.setAmount(total_amount);
		model.setPayerShowName("测试退款");
		model.setPayerRealName("沙箱环境");
		model.setRemark("javen测试单笔转账到支付宝");

		try {
			isSuccess = AliPayApi.transfer(model);
		} catch (Exception e) {
			e.printStackTrace();
		}
		renderJson(isSuccess);
	}
```

