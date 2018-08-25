
# IJPay支付宝初始化

在介绍IJPay中支付宝支付接口初始化之前先介绍一下支付宝服务端的SDK。因为IJPay支付宝支付接口是基于此SDK进行再次封装的。

### 支付宝服务端SDK

[服务器SDK介绍以及下载地址](https://docs.open.alipay.com/54)

使用SDK分为以下几个步骤

1. 实例化客户端
2. 实例化具体API对应的request类
3. 设置请求参数\(SDK已经封装掉了公共参数，这里只需要传入业务参数\)
4. 执行API请求
5. 处理业务逻辑

以下是支付宝SDK中WAP支付示例

```
//实例化客户端
AlipayClient alipayClient = new DefaultAlipayClient("https://openapi.alipay.com/gateway.do", APP_ID, APP_PRIVATE_KEY, "json", CHARSET, ALIPAY_PUBLIC_KEY, "RSA2");
//实例化具体API对应的request类,类名称和接口名称对应,当前调用接口名称：alipay.open.public.template.message.industry.modify 
AlipayOpenPublicTemplateMessageIndustryModifyRequest request = new AlipayOpenPublicTemplateMessageIndustryModifyRequest();
//SDK已经封装掉了公共参数，这里只需要传入业务参数
//此次只是参数展示，未进行字符串转义，实际情况下请转义
request.setBizContent("  {" +
"    \"primary_industry_name\":\"IT科技/IT软件与服务\"," +
"    \"primary_industry_code\":\"10001/20102\"," +
"    \"secondary_industry_code\":\"10001/20102\"," +
"    \"secondary_industry_name\":\"IT科技/IT软件与服务\"" +
" }");
AlipayOpenPublicTemplateMessageIndustryModifyResponse response = alipayClient.execute(request); 
//调用成功，则处理业务逻辑
if(response.isSuccess()){
    //.....
}
```

> 可以从[JavaSDK集成示例](https://docs.open.alipay.com/54)中看到每次都要初始化客户端，需要查询对应的API以及复杂参数，接入步骤比较复杂。IJPay就是为了解决以上问题而诞生的。具体接入步骤后面会详细介绍，我们先来看看IJPay中支付宝接口的初始化。

### IJPay 支付宝初始化

**IJPay中的支付宝支付支持多应用接入**初始化客户端代码如下

```
AliPayApiConfig aliPayApiConfig = AliPayApiConfig.New()
        .setAppId(app_id)
        .setAlipayPublicKey(alipay_public_key)
        .setCharset(charset)
        .setPrivateKey(private_key)
        .setServiceUrl(service_url)
        .setSignType(sign_type)
        .build();
AliPayApiConfigKit.setThreadLocalAliPayApiConfig(aliPayApiConfig);
```

当然支付接口中也不用每次都设置`AliPayApiConfig`，可以通过`AliPayApiConfigKit.getApiConfig(String appId)`获取`AliPayApiConfig`

```
//通过appId获取配置
AliPayApiConfig aliPayApiConfig =AliPayApiConfigKit.getApiConfig(appId);
```

### 代码示例

[https://gitee.com/javen205/IJPay-Demo](https://gitee.com/javen205/IJPay-Demo)

Demo中参数是配置在`.properties`文件当中当然你也可以配置到数据库。 项目默认使用dev环境下的配置。 如果`src/main/resources`没有`dev`文件夹可以复制`production`一份并修改为`dev`，再修改`dev`中相关的配置参数

**多应用支持，JFinal完整版示例代码如下：**

```
/**
 * @Email javen205@126.com
 * @author Javen
 */
public class AliPayApiInterceptor implements Interceptor {
    AlipayService alipayService = new AlipayService();
    AppService appService = new AppService();
    AjaxResult ajax = new AjaxResult();

    @Override
    public void intercept(Invocation inv) {
        Controller controller = inv.getController();
        String appId = controller.getPara("appId");
        if (StrKit.isBlank(appId)) {
            controller.renderJson(ajax.addError("应用的编号不能为空"));
            return;
        }
        //判断应用是否存在
        App app = appService.getAppByAppId(appId, 1);
        AliPayApiConfig aliPayApiConfig = null;
        if (app !=null) {
            // 通过应用的appId查询支付宝的配置
            Alipay alipay = alipayService.getAlipayByAppId(appId);
            if (alipay != null) {
                try {
                    aliPayApiConfig = AliPayApiConfigKit.getApiConfig(alipay.getAppId());
                } catch (Exception e) {
                    LogKit.error("实例化AliPayApiConfig...");
                    // 如果Map中没有当前支付宝的实例就初始化并添加到Map中
                    aliPayApiConfig = AliPayApiConfig.New()
                            .setAppId(alipay.getAppId())
                            .setAlipayPublicKey(alipay.getPublicKey())
                            .setCharset(IJPayConsts.CHARSET)
                            .setPrivateKey(alipay.getPrivateKey())
                            .setServiceUrl(alipay.getServerUrl())
                            .setSignType(IJPayConsts.SIGN_TYPE)
                            .build();
                }
            }
        }
        if (aliPayApiConfig != null) {
            AliPayApiConfigKit.setThreadLocalAliPayApiConfig(aliPayApiConfig);
            controller.setAttr("app", app);
            inv.invoke();
        } else {
            LogKit.error("aliPayApiConfig is null");
            controller.renderJson(ajax.addError("此应用暂未配置支付宝支付的参数"));
        }
    }
}
```



