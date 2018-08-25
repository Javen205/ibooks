# 支付宝异步通知

### 官方接入文档

1. [PC支付异步通知文档](https://docs.open.alipay.com/270/105902/)
2. [WAP支付异步通知文档](https://docs.open.alipay.com/203/105286/)
3. [当面付异步通知-仅用于扫码支付](https://docs.open.alipay.com/194/103296)
4. [App支付服务器同步/异步通知参数说明](https://docs.open.alipay.com/204/105301/)

### 异步通知处理步骤

1. 验证签名
2. 校验订单去重
3. 处理业务逻辑
4. 响应处理结果


### 验证签名

```
//获取支付宝POST过来反馈信息
Map<String, String> params = AliPayApi.toMap(getRequest());
//验证签名
boolean verify_result = AlipaySignature.rsaCheckV1(params, alipay_public_key, charset,sign_type);

```

### 代码示例

https://github.com/Javen205/IJPay-Demo

```
public void notify_url() {
		try {
			// 获取支付宝POST过来反馈信息
			Map<String, String> params = AliPayApi.toMap(getRequest());

			for (Map.Entry<String, String> entry : params.entrySet()) {
				System.out.println(entry.getKey() + " = " + entry.getValue());
			}

			boolean verify_result = AlipaySignature.rsaCheckV1(params, alipay_public_key, charset,
					sign_type);

			if (verify_result) {// 验证成功
				// TODO 请在这里加上商户的业务逻辑程序代码 异步通知可能出现订单重复通知 需要做去重处理
				System.out.println("notify_url 验证成功succcess");
				renderText("success");
				return;
			} else {
				System.out.println("notify_url 验证失败");
				// TODO
				renderText("failure");
				return;
			}
		} catch (AlipayApiException e) {
			e.printStackTrace();
			renderText("failure");
		}
	}
```




```
@RequestMapping(value = "/notify_url")
	@ResponseBody
	public String  notify_url(HttpServletRequest request) {
		try {
			// 获取支付宝POST过来反馈信息
			Map<String, String> params = AliPayApi.toMap(request);
			for (Map.Entry<String, String> entry : params.entrySet()) {
				System.out.println(entry.getKey() + " = " + entry.getValue());
			}

			boolean verify_result = AlipaySignature.rsaCheckV1(params, aliPayBean.getPublicKey(), "UTF-8",
					"RSA2");

			if (verify_result) {// 验证成功
				// TODO 请在这里加上商户的业务逻辑程序代码 异步通知可能出现订单重复通知 需要做去重处理
				System.out.println("notify_url 验证成功succcess");
				return "success";
			} else {
				System.out.println("notify_url 验证失败");
				// TODO
				return "failure";
			}
		} catch (AlipayApiException e) {
			e.printStackTrace();
			return "failure";
		}
	}
```