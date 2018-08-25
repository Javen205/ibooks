# 支付宝APP支付

目前支持手机系统有：iOS（苹果）、Android（安卓）。

### 官方接入文档

https://docs.open.alipay.com/204

参考博客 [Android版-支付宝APP支付](http://blog.csdn.net/zyw_java/article/details/54024238)

### IJPay 接口

```
	/**
	 * APP支付
	 * @param model
	 * @param notifyUrl
	 * @return {String}
	 * @throws {AlipayApiException}
	 */
	public static String startAppPay(AlipayTradeAppPayModel model, String notifyUrl) throws AlipayApiException{
		AlipayTradeAppPayResponse response = appPayToResponse(model,notifyUrl);
		return response.getBody();
	}
	
	/**
	 * APP支付
	 * https://doc.open.alipay.com/docs/doc.htm?treeId=54&articleId=106370&docType=1
	 * @param model
	 * @param notifyUrl
	 * @return {AlipayTradeAppPayResponse}
	 * @throws {AlipayApiException}
	 */
	public static AlipayTradeAppPayResponse appPayToResponse(AlipayTradeAppPayModel model, String notifyUrl) throws AlipayApiException{
		//实例化具体API对应的request类,类名称和接口名称对应,当前调用接口名称：alipay.trade.app.pay
		AlipayTradeAppPayRequest request = new AlipayTradeAppPayRequest();
		//SDK已经封装掉了公共参数，这里只需要传入业务参数。以下方法为sdk的model入参方式(model和biz_content同时存在的情况下取biz_content)。
		request.setBizModel(model);
		request.setNotifyUrl(notifyUrl);
		//这里和普通的接口调用不同，使用的是sdkExecute
        AlipayTradeAppPayResponse response = AliPayApiConfigKit.getAliPayApiConfig().getAlipayClient().sdkExecute(request);
		return response;
	}
	
	/**
	 * APP支付
	 * @param model
	 * @param notifyUrl
	 * @return {AlipayTradeAppPayResponse}
	 * @throws {AlipayApiException}
	 */
	@Deprecated
	public static AlipayTradeAppPayResponse appPay(AlipayTradeAppPayModel model, String notifyUrl) throws AlipayApiException{
		return appPayToResponse(model, notifyUrl);
	}
```

### 服务端代码示例

生成订单并返回唤起客户端支付的`orderInfo`,具体实现如下：

[Spring Boot版本Demo](https://gitee.com/javen205/IJPay-Demo/blob/SpringBoot/src/main/java/com/ijpay/controller/alipay/AliPayController.java)

```
	/**
	 * app支付
	 */
	@RequestMapping(value = "/appPay")
	@ResponseBody
	public AjaxResult appPay() {
		try {
			AlipayTradeAppPayModel model = new AlipayTradeAppPayModel();
			model.setBody("我是测试数据-By Javen");
			model.setSubject("App支付测试-By Javen");
			model.setOutTradeNo(StringUtils.getOutTradeNo());
			model.setTimeoutExpress("30m");
			model.setTotalAmount("0.01");
			model.setPassbackParams("callback params");
			model.setProductCode("QUICK_MSECURITY_PAY");
			String orderInfo = AliPayApi.startAppPay(model, aliPayBean.getDomain() + "/alipay/notify_url");
			result.success(orderInfo);
		} catch (AlipayApiException e) {
			e.printStackTrace();
			result.addError("system error:"+e.getMessage());
		}
		return result;
	}
```

[JFinal版本Demo](https://gitee.com/javen205/IJPay-Demo/blob/master/src/main/java/com/ijpay/controller/alipay/AliPayController.java)

```
	/**
	 * app支付
	 */
	public void appPay() {
		try {
			AlipayTradeAppPayModel model = new AlipayTradeAppPayModel();
			model.setBody("我是测试数据-By Javen");
			model.setSubject("App支付测试-By Javen");
			model.setOutTradeNo(StringUtils.getOutTradeNo());
			model.setTimeoutExpress("30m");
			model.setTotalAmount("0.01");
			model.setPassbackParams("callback params");
			model.setProductCode("QUICK_MSECURITY_PAY");
			String orderInfo = AliPayApi.startAppPay(model, notify_domain + "/alipay/app_pay_notify");
			result.success(orderInfo);
			renderJson(result);

		} catch (AlipayApiException e) {
			e.printStackTrace();
			result.addError("system error");
		}
	}
```

### 客户端实现

官方[App支付Android集成流程](https://docs.open.alipay.com/204/105296/)这里就不做过多解释了，下面我们来看看如何使用JPay快速接入Android APP支付。



对微信App支付、支付宝App支付、银联App支付的二次封装,对外提供一个相对简单的接口以及支付结果的回调


[![License](https://img.shields.io/badge/license-Apache%202-green.svg)](https://www.apache.org/licenses/LICENSE-2.0)	[![](https://jitpack.io/v/javen205/JPay.svg)](https://jitpack.io/#javen205/JPay)	[![JPay Author](https://img.shields.io/badge/JPay%20OR%20IJPay%20Author-Javen-ff69b4.svg)](http://blog.csdn.net/zyw_java)


GitHub:https://github.com/Javen205/JPay

Gitee:http://gitee.com/Javen205/JPay


**使用方法**

### 1、坐标

Step 1. Add it in your root build.gradle at the end of repositories:
```
	allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
```
Step 2. Add the dependency

```
compile 'com.github.javen205.JPay:JPay:latest.release.here'
```
例如：版本号为`0.0.4`
```
compile 'com.github.javen205.JPay:JPay:0.0.4'
```

### 2. Android Manifest配置

##### 2.1权限声明

```
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```


##### 2.2注册activity

`application`节点添加如下类容
```


        <!-- 支付宝支付 -->

        <activity
            android:name="com.alipay.sdk.app.H5PayActivity"
            android:configChanges="orientation|keyboardHidden|navigation|screenSize"
            android:exported="false"
            android:screenOrientation="behind"
            android:windowSoftInputMode="adjustResize|stateHidden" >
        </activity>
        <activity
            android:name="com.alipay.sdk.app.H5AuthActivity"
            android:configChanges="orientation|keyboardHidden|navigation"
            android:exported="false"
            android:screenOrientation="behind"
            android:windowSoftInputMode="adjustResize|stateHidden" >
        </activity>

        <!-- 支付宝支付 end -->

        
```
### 3. 发起支付
如需开启测试模式只需要在OnCreate中添加如下代码。沙箱环境测试APP支付中请使用沙箱版钱包测试

`EnvUtils.setEnv(EnvUtils.EnvEnum.SANDBOX);`


>通过服务端接口获取到`orderInfo`后唤起App客户端进行支付

```
JPay.getIntance(mContext).toPay(JPay.PayMode.ALIPAY, orderInfo, new JPay.JPayListener() {
			@Override
			public void onPaySuccess() {
				Toast.makeText(mContext, "支付成功", Toast.LENGTH_SHORT).show();
			}

			@Override
			public void onPayError(int error_code, String message) {
				Toast.makeText(mContext, "支付失败>"+error_code+" "+ message, Toast.LENGTH_SHORT).show();
			}

			@Override
			public void onPayCancel() {
				Toast.makeText(mContext, "取消了支付", Toast.LENGTH_SHORT).show();
			}
		});
```

或者

```
Alipay.getInstance(mContext).startAliPay(orderInfo, new JPay.JPayListener() {
			@Override
			public void onPaySuccess() {

			}

			@Override
			public void onPayError(int error_code, String message) {

			}

			@Override
			public void onPayCancel() {

			}
		});
```