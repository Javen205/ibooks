# 支付宝手机网站支付

手机网站支付又称WAP支付

### 官方接入文档

https://docs.open.alipay.com/203

### IJPay 接口

```
	/**
	 * WAP支付
	 * @param response
	 * @param model
	 * @param returnUrl
	 * @param notifyUrl
	 * @throws {AlipayApiException}
	 * @throws IOException
	 */
	public static void wapPay(HttpServletResponse response,AlipayTradeWapPayModel model,String returnUrl,String notifyUrl) throws AlipayApiException, IOException {
		String form = wapPayStr(response, model, returnUrl, notifyUrl);
		HttpServletResponse httpResponse = response;
		httpResponse.setContentType("text/html;charset=" + AliPayApiConfigKit.getAliPayApiConfig().getCharset());
		httpResponse.getWriter().write(form);// 直接将完整的表单html输出到页面
		httpResponse.getWriter().flush();
	}
	/**
	 * WAP支付
	 * @param response
	 * @param model
	 * @param returnUrl
	 * @param notifyUrl
	 * @return {String}
	 * @throws {AlipayApiException}
	 * @throws IOException
	 */
	public static String wapPayStr(HttpServletResponse response,AlipayTradeWapPayModel model,String returnUrl,String notifyUrl) throws AlipayApiException, IOException {
		AlipayTradeWapPayRequest alipayRequest = new AlipayTradeWapPayRequest();// 创建API对应的request
		alipayRequest.setReturnUrl(returnUrl);
		alipayRequest.setNotifyUrl(notifyUrl);// 在公共参数中设置回跳和通知地址
		alipayRequest.setBizModel(model);// 填充业务参数
		return AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().pageExecute(alipayRequest).getBody(); // 调用SDK生成表单
	}
```

### 代码示例

https://github.com/Javen205/IJPay-Demo


[Spring Boot版本Demo](https://gitee.com/javen205/IJPay-Demo/blob/SpringBoot/src/main/java/com/ijpay/controller/alipay/AliPayController.java)

```
	@RequestMapping(value = "/wapPay") 
	@ResponseBody
	public void wapPay(HttpServletResponse response) {
		String body = "我是测试数据-By Javen";
		String subject = "Javen Wap支付测试";
		String totalAmount = "1";
		String passbackParams = "1";
		String returnUrl = aliPayBean.getDomain() + "/alipay/return_url";
		String notifyUrl = aliPayBean.getDomain() + "/alipay/notify_url";

		AlipayTradeWapPayModel model = new AlipayTradeWapPayModel();
		model.setBody(body);
		model.setSubject(subject);
		model.setTotalAmount(totalAmount);
		model.setPassbackParams(passbackParams);
		String outTradeNo = StringUtils.getOutTradeNo();
		System.out.println("wap outTradeNo>"+outTradeNo);
		model.setOutTradeNo(outTradeNo);
		model.setProductCode("QUICK_WAP_PAY");

		try {
			AliPayApi.wapPay(response, model, returnUrl, notifyUrl);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
```

[Jfinal 版Demo](https://gitee.com/javen205/IJPay-Demo/blob/master/src/main/java/com/ijpay/controller/alipay/AliPayController.java)

```
	/**
	 * Wap支付
	 */
	public void wapPay() {
		String body = "我是测试数据-By Javen";
		String subject = "Javen Wap支付测试";
		String totalAmount = "1";
		String passbackParams = "1";
		String returnUrl = notify_domain + "/alipay/return_url";
		String notifyUrl = notify_domain + "/alipay/notify_url";

		AlipayTradeWapPayModel model = new AlipayTradeWapPayModel();
		model.setBody(body);
		model.setSubject(subject);
		model.setTotalAmount(totalAmount);
		model.setPassbackParams(passbackParams);
		String outTradeNo = StringUtils.getOutTradeNo();
		System.out.println("wap outTradeNo>"+outTradeNo);
		model.setOutTradeNo(outTradeNo);
		model.setProductCode("QUICK_WAP_PAY");

		try {
			AliPayApi.wapPay(getResponse(), model, returnUrl, notifyUrl);
		} catch (Exception e) {
			e.printStackTrace();
		}
		renderNull();
	}
```

>Demo中参数是配置在`.properties`文件当中当然你也可以配置到数据库。 项目默认使用dev环境下的配置。 如果`src/main/resources`没有`dev`文件夹可以复制`production`一份并修改为`dev`，再修改`dev`中相关的配置参数


### 唤起支付宝APP

需要商户APP的WebView处理alipays协议，具体实现可以参考[手机网站支付唤起支付宝app
](https://docs.open.alipay.com/203/107091)

### 手机网站支付转Native支付

如何实现手机网站转Native支付？

步骤一： 在接入方App中拦截H5的URL；
步骤二： 调用新增拦截+支付二合一接口(payInterceptorWithUrl...)进行URL拦截及支付转化。

**接口原型**
```
/**
 * 支付宝H5支付URL拦截器，完成拦截及支付方式转化
 * 
 * @param h5PayUrl          待过滤拦截的 URL
 * @param isShowPayLoading  是否出现loading
 * @param callback          异步回调接口
 * 
 * @return true：表示URL为支付宝支付URL，URL已经被拦截并支付转化；false：表示URL非支付宝支付URL；
 * 
 */
 public synchronized boolean payInterceptorWithUrl(final String h5PayUrl, final boolean isShowPayLoading, final H5PayCallback callback)
```

[具体处理初步参照官方接口说明](https://docs.open.alipay.com/203/106493)



