# 支付宝条形码支付

沙箱环境下可以直接下载沙箱环境的APP进行测试，`authCode`就是支付宝客户端`付钱`中条码下的一窜数字。[APP下载-沙箱工具](https://auth.alipay.com/login/ant_sso_index.htm?goto=https%3A%2F%2Fopenhome.alipay.com%2Fplatform%2FappDaily.htm%3Ftab%3Dtool)


### IJPay 接口

```
	/**
	 * 条形码支付、声波支付
	 * @param model
	 * @param notifyUrl
	 * @return {String}
	 * @throws {AlipayApiException}
	 */
	public static String tradePay(AlipayTradePayModel model, String notifyUrl) throws AlipayApiException {
		AlipayTradePayResponse response = tradePayToResponse(model,notifyUrl);
		return response.getBody();
	}
	/**
	 * 交易支付接口
	 * 条形码支付、声波支付
	 * @param model
	 * @param notifyUrl
	 * @return {AlipayTradePayResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayTradePayResponse tradePayToResponse(AlipayTradePayModel model, String notifyUrl) throws AlipayApiException{
		AlipayTradePayRequest request = new AlipayTradePayRequest();
		request.setBizModel(model);// 填充业务参数
		request.setNotifyUrl(notifyUrl);
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().execute(request); // 通过AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient()调用API，获得对应的response类
	}
```

### 代码示例
https://github.com/Javen205/IJPay-Demo



```
	/**
	 * 条形码支付
	 */
	public void tradePay() {
		String authCode = getPara("auth_code");
		String subject = "Javen 支付宝条形码支付测试";
		String totalAmount = "100";
		String notifyUrl = notify_domain + "/alipay/notify_url";

		AlipayTradePayModel model = new AlipayTradePayModel();
		model.setAuthCode(authCode);
		model.setSubject(subject);
		model.setTotalAmount(totalAmount);
		model.setOutTradeNo(StringUtils.getOutTradeNo());
		model.setScene("bar_code");
		try {
			String resultStr = AliPayApi.tradePay(model,notifyUrl);
			renderText(resultStr);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

```


```
	/**
	 * 条形码支付
	 */
	@RequestMapping(value ="/tradePay")
	@ResponseBody
	public String  tradePay(@RequestParam("auth_code") String authCode) {
		String subject = "Javen 支付宝条形码支付测试";
		String totalAmount = "100";
		String notifyUrl = aliPayBean.getDomain() + "/alipay/notify_url";

		AlipayTradePayModel model = new AlipayTradePayModel();
		model.setAuthCode(authCode);
		model.setSubject(subject);
		model.setTotalAmount(totalAmount);
		model.setOutTradeNo(StringUtils.getOutTradeNo());
		model.setScene("bar_code");
		try {
			return AliPayApi.tradePay(model,notifyUrl);
		} catch (Exception e) {
			e.printStackTrace();
		}

		return null;
	}
```