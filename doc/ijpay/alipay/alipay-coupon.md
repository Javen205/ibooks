# 支付宝收发现金红包

为PC网站，移动应用和H5场景提供收发现金红包支付通道的一款产品。主要应用于用户与用户互发红包和企业给用户发放营销红包。开发者可以应用于IM红包（钉钉红包），H5红包等。

### 官方接入文档

https://docs.open.alipay.com/301/106168
### IJPay 接口

```
	/**
	 * 红包无线支付接口
	 * @param model
	 * @return {String}
	 * @throws {AlipayApiException}
	 */
	public static String fundCouponOrderAppPay(AlipayFundCouponOrderAppPayModel model) throws AlipayApiException{
		AlipayFundCouponOrderAppPayResponse response = fundCouponOrderAppPayToResponse(model);
		return response.getBody();
	}
	/**
	 * 红包无线支付接口
	 * @param model
	 * @return {AlipayFundCouponOrderAppPayResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayFundCouponOrderAppPayResponse fundCouponOrderAppPayToResponse(AlipayFundCouponOrderAppPayModel model) throws AlipayApiException{
		AlipayFundCouponOrderAppPayRequest request = new AlipayFundCouponOrderAppPayRequest();
		request.setBizModel(model);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request);
	}
	
	/**
	 * 红包页面支付接口
	 * @param model
	 * @return {String}
	 * @throws {AlipayApiException}
	 */
	public static String fundCouponOrderPagePay(AlipayFundCouponOrderPagePayModel model) throws AlipayApiException{
		AlipayFundCouponOrderPagePayResponse response = fundCouponOrderPagePayToResponse(model);
		return response.getBody();
	}
	/**
	 * 红包页面支付接口
	 * @param model
	 * @return {AlipayFundCouponOrderPagePayResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayFundCouponOrderPagePayResponse fundCouponOrderPagePayToResponse(AlipayFundCouponOrderPagePayModel model) throws AlipayApiException{
		AlipayFundCouponOrderPagePayRequest request = new AlipayFundCouponOrderPagePayRequest();
		request.setBizModel(model);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request);
	}
	/**
	 * 红包协议支付接口
	 * @param model
	 * @return {String}
	 * @throws {AlipayApiException}
	 */
	public static String fundCouponOrderAgreementPay(AlipayFundCouponOrderAgreementPayModel model) throws AlipayApiException{
		AlipayFundCouponOrderAgreementPayResponse response = fundCouponOrderAgreementPayToResponse(model);
		return response.getBody();
	}
	/**
	 * 红包协议支付接口
	 * @param model
	 * @return {AlipayFundCouponOrderAgreementPayResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayFundCouponOrderAgreementPayResponse fundCouponOrderAgreementPayToResponse(AlipayFundCouponOrderAgreementPayModel model) throws AlipayApiException{
		AlipayFundCouponOrderAgreementPayRequest request = new AlipayFundCouponOrderAgreementPayRequest();
		request.setBizModel(model);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request);
	}
	/**
	 * 红包打款接口
	 * @param model
	 * @return {String}
	 * @throws {AlipayApiException}
	 */
	public static String fundCouponOrderDisburse(AlipayFundCouponOrderDisburseModel model) throws AlipayApiException{
		AlipayFundCouponOrderDisburseResponse response = fundCouponOrderDisburseToResponse(model);
		return response.getBody();
	}
	/**
	 * 红包打款接口
	 * @param model
	 * @return {AlipayFundCouponOrderDisburseResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayFundCouponOrderDisburseResponse fundCouponOrderDisburseToResponse(AlipayFundCouponOrderDisburseModel model) throws AlipayApiException{
		AlipayFundCouponOrderDisburseRequest request = new AlipayFundCouponOrderDisburseRequest();
		request.setBizModel(model);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request);
	}
	/**
	 * 红包退回接口
	 * @param model
	 * @return {String}
	 * @throws {AlipayApiException}
	 */
	public static String fundCouponOrderRefund(AlipayFundCouponOrderRefundModel model) throws AlipayApiException{
		AlipayFundCouponOrderRefundResponse response = fundCouponOrderRefundToResponse(model);
		return response.getBody();
	}
	/**
	 * 红包退回接口
	 * @param model
	 * @return {AlipayFundCouponOrderRefundResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayFundCouponOrderRefundResponse fundCouponOrderRefundToResponse(AlipayFundCouponOrderRefundModel model) throws AlipayApiException{
		AlipayFundCouponOrderRefundRequest request = new AlipayFundCouponOrderRefundRequest();
		request.setBizModel(model);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request);
	}
	/**
	 * 红包明细查询接口
	 * @param model
	 * @return {String}
	 * @throws {AlipayApiException}
	 */
	public static String fundCouponOperationQuery(AlipayFundCouponOperationQueryModel model) throws AlipayApiException{
		AlipayFundCouponOperationQueryResponse response = fundCouponOperationQueryToResponse(model);
		return response.getBody();
	}
	/**
	 * 红包明细查询接口
	 * @param model
	 * @return {AlipayFundCouponOperationQueryResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayFundCouponOperationQueryResponse fundCouponOperationQueryToResponse(AlipayFundCouponOperationQueryModel model) throws AlipayApiException{
		AlipayFundCouponOperationQueryRequest request = new AlipayFundCouponOperationQueryRequest();
		request.setBizModel(model);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request);
	}
```
### 代码示例

* https://gitee.com/javen205/IJPay-Demo
* https://github.com/javen205/IJPay-Demo

>提示：沙箱环境无法测试红包接口必须使用线上应用测试。
