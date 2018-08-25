# 支付宝声波支付

沙箱环境下可以直接下载沙箱环境的APP进行测试，`authCode`就是支付宝客户端`付钱`中条码下的一窜数字。[APP下载-沙箱工具](https://auth.alipay.com/login/ant_sso_index.htm?goto=https%3A%2F%2Fopenhome.alipay.com%2Fplatform%2FappDaily.htm%3Ftab%3Dtool)

### IJPay 接口

详见条码支付中的IJPay接口

### 代码示例
https://github.com/Javen205/IJPay-Demo



```
	/**
	 * 声波支付
	 */
	public void tradeWavePay() {
		String authCode = getPara("auth_code");
		String subject = "Javen 支付宝声波支付测试";
		String totalAmount = "100";
		String notifyUrl = notify_domain + "/alipay/notify_url";

		AlipayTradePayModel model = new AlipayTradePayModel();
		model.setAuthCode(authCode);
		model.setSubject(subject);
		model.setTotalAmount(totalAmount);
		model.setOutTradeNo(StringUtils.getOutTradeNo());
		model.setScene("wave_code");
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
	 * 声波支付
	 */
	@RequestMapping(value ="/tradeWavePay")
	@ResponseBody
	public String tradeWavePay(@RequestParam("auth_code") String authCode) {
		String subject = "Javen 支付宝声波支付测试";
		String totalAmount = "100";
		String notifyUrl = aliPayBean.getDomain() + "/alipay/notify_url";

		AlipayTradePayModel model = new AlipayTradePayModel();
		model.setAuthCode(authCode);
		model.setSubject(subject);
		model.setTotalAmount(totalAmount);
		model.setOutTradeNo(StringUtils.getOutTradeNo());
		model.setScene("wave_code");
		try {
			return AliPayApi.tradePay(model,notifyUrl);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return  null;
	}
```