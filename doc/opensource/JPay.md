# Android支付SDK-JPay


🔥对微信App支付、支付宝App支付、银联APP支付的二次封装,对外提供一个相对简单的接口以及支付结果的回调。


![](https://img.shields.io/github/stars/Javen205/JPay.svg?style=social&label=Stars)    ![](https://img.shields.io/github/forks/Javen205/JPay.svg?style=social&label=Fork)    [![License](https://img.shields.io/badge/license-Apache%202-green.svg)](https://www.apache.org/licenses/LICENSE-2.0)    [![](https://jitpack.io/v/javen205/JPay.svg)](https://jitpack.io/#javen205/JPay)    [![JPay Author](https://img.shields.io/badge/JPay%20OR%20IJPay%20Author-Javen-ff69b4.svg)](http://blog.csdn.net/zyw_java)



GitHub:https://github.com/Javen205/JPay

Gitee:http://gitee.com/Javen205/JPay

[版本更新记录](https://github.com/Javen205/JPay/wiki/%E7%89%88%E6%9C%AC%E6%9B%B4%E6%96%B0%E8%AE%B0%E5%BD%95)

**联系方式**

[![QQ0Group][qq0groupsvg]][qq0group]    [![Email](https://img.shields.io/badge/Email-javendev%40126.com-yellowgreen.svg)](http://blog.csdn.net/zyw_java)

[qq0groupsvg]: https://img.shields.io/badge/QQ群-148540125-fba7f9.svg
[qq0group]: http://shang.qq.com/wpa/qunwpa?idkey=5005dbbee62dac64b34fedc9ff73511762da5d02642a1ef526522fb2e07852dd

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

<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="org.simalliance.openmobileapi.SMARTCARD" />
<uses-permission android:name="android.permission.NFC" />
<uses-feature android:name="android.hardware.nfc.hce"/>
```

##### 2.2注册activity

`application`节点添加如下类容
```
 <!-- 微信支付 -->
        <activity
            android:name="com.jpay.weixin.WXPayEntryActivity"
            android:configChanges="orientation|keyboardHidden|navigation|screenSize"
            android:launchMode="singleTop"
            android:theme="@android:style/Theme.Translucent.NoTitleBar" />
        <activity-alias
            android:name=".wxapi.WXPayEntryActivity"
            android:exported="true"
            android:targetActivity="com.jpay.weixin.WXPayEntryActivity" />
        <!-- 微信支付 end -->


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

         <!-- 银联支付 -->
                <uses-library android:name="org.simalliance.openmobileapi" android:required="false"/>
                <activity
                    android:name="com.unionpay.uppay.PayActivity"
                    android:screenOrientation="portrait"
                    android:configChanges="orientation|keyboardHidden"
                    android:excludeFromRecents="true"
                    android:windowSoftInputMode="adjustResize"/>
                <activity
                    android:name="com.unionpay.UPPayWapActivity"
                    android:configChanges="orientation|keyboardHidden|fontScale"
                    android:screenOrientation="portrait"
                    android:windowSoftInputMode="adjustResize" >
                </activity>
                <!-- 银联支付 end -->
```

### 3. 发起支付

##### 3.1 微信支付


```
JPay.getIntance(mContext).toPay(JPay.PayMode.WXPAY, payParameters, new JPay.JPayListener() {
            @Override
            public void onPaySuccess() {
                Toast.makeText(mContext, "支付成功", Toast.LENGTH_SHORT).show()
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
`payParameters` 为JSON字符串格式如下：
```
{
  "appId": "",
  "partnerId": "",
  "prepayId": "",
  "sign": "",
  "nonceStr" : "",
  "timeStamp": ""
}
```

或者

```
JPay.getIntance(mContext).toWxPay(appId, partnerId, prepayId, nonceStr, timeStamp, sign, new JPay.JPayListener() {
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
##### 3.2 支付宝支付

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

##### 3.3 银联支付

步骤一 调起银联手机控件支付

```
JPay.getIntance(mContext).toUUPay("01",tn, new JPay.JPayListener() {
            @Override
            public void onPaySuccess() {
                Toast.makeText(mContext, "支付成功", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onPayError(int error_code, String message) {
                Toast.makeText(mContext, "支付失败>" + error_code + " " + message, Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onPayCancel() {
                Toast.makeText(mContext, "取消了支付", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onUUPay(String dataOrg, String sign, String mode) {
                Toast.makeText(mContext, "支付成功>需要后台查询订单确认>"+dataOrg+" "+mode, Toast.LENGTH_SHORT).show();
            }
        });
```

>说明
第一个参数`mode` "00" - 启动银联正式环境 "01" - 连接银联测试环境
第二个参数`tn` 后台获取下单交易的流水号
第三方参数是`JPay` 封装的客户端结果的回调,需要在`onUUPay`中自行去后台查询订单的最终支付状态

步骤二 设置回调

```
@Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        try {
            UPPay.getInstance(this).onUUPayResult(data);
        } catch (JSONException e) {
            e.printStackTrace();
        }
        super.onActivityResult(requestCode, resultCode, data);
    }
```

### 4.案例的使用


> appId以及相关的key我们都从服务端获取

#### 4.1 客户端使用说明
 1. 将`AndroidManifest.xml` 的包名修改为申请应用的包名
 2. 将应用中的`build.gradle`的 `applicationId`修改为申请应用的包名
 3. 测试的时候修改默认的签名key

> 将key复制到项目的根目录(app)中并修改`buildTypes` 配置如下

```
 signingConfigs {
        release {
            storeFile file("wxkey")
            storePassword '123456'
            keyAlias '1'
            keyPassword '123456'
        }
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
        debug {
            signingConfig signingConfigs.release
        }
    }
```



#### 4.2 服务端

[IJPay 让支付触手可及，封装了微信支付、支付宝支付、银联支付常用的支付方式以及各种常用的接口](https://github.com/javen205/IJPay)



#### 4.3 参考资料

微信、支付宝APP支付详细介绍参考资料 [博客地址](http://blog.csdn.net/zyw_java)

[10分钟搭建属于自己的ngork服务器，实现内网穿透](http://blog.csdn.net/zyw_java/article/details/70341106)
[Android版-微信APP支付](http://blog.csdn.net/zyw_java/article/details/54024232)

[Android版-支付宝APP支付](http://blog.csdn.net/zyw_java/article/details/54024238)

[支付宝Wap支付你了解多少？](http://blog.csdn.net/zyw_java/article/details/54024253)

**广而告之**

[微信公众号开发：订阅号、服务号](http://git.oschina.net/javen205/weixin_guide)

[AndroidStudio多渠道打包](http://blog.csdn.net/zyw_java/article/details/61420290)

[Android依赖管理与私服搭建](http://blog.csdn.net/zyw_java/article/details/60336030)

[Android Studio 上传aar(Library)到JCenter](http://blog.csdn.net/zyw_java/article/details/60336189)
