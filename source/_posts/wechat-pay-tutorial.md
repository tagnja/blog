---
title: 微信公众号之微信支付开发教程
date: 2020-08-07 11:15:54
categories: 
- Java
- Java Web
tags: wechat
---



本文将介绍微信支付开发相关内容，微信支付有很多种途径，本文主要以 JSAPI 支付为例。接下来将介绍微信支付的基础和 JSAPI 支付开发过程。

## 微信支付的基础

### 微信支付产品

- 付款码支付：付款码支付是用户展示微信钱包内的“刷卡条码/二维码”给商户系统扫描后直接完成支付的模式。主要应用线下面对面收银的场景。
- Native 支付：Native支付是商户系统按微信支付协议生成支付二维码，用户再用微信“扫一扫”完成支付的模式。该模式适用于PC网站支付、实体店单品或订单支付、媒体广告支付等场景。
- JSAPI支付：JSAPI支付是用户在微信中打开商户的H5页面，商户在H5页面通过调用微信支付提供的JSAPI接口调起微信支付模块完成支付。
- APP支付：APP支付又称移动端支付，是商户通过在移动端应用APP中集成开放SDK调起微信支付模块完成支付的模式。
- H5支付：H5支付主要是在手机、ipad等移动设备中通过浏览器来唤起微信支付的支付产品。
- 小程序支付：小程序支付是专门被定义使用在小程序中的支付产品。目前在小程序中能且只能使用小程序支付的方式来唤起微信支付。

以上不同的微信支付产品对应不同的微信支付系统的 API。其中，线上使用的支付方式：JSAPI 支付，APP 支付，H5 支付。对于在微信公众号内的微信支付，一般使用 JSAPI 支付方式。

### 常见术语名词解释

- 微信公众平台：微信公众平台是微信公众账号申请入口和管理后台。商户可以在公众平台提交基本资料、业务资料、财务资料申请开通微信支付功能。
- 微信开放平台：微信开放平台是商户APP接入微信支付开放接口的申请入口，通过此平台可申请微信APP支付。
- 微信商户平台：微信商户平台是微信支付相关的商户功能集合，包括参数配置、支付数据查询与统计、在线退款、代金券或立减优惠运营等功能。
- 微信支付系统：微信支付系统是指完成微信支付流程中涉及的API接口、后台业务处理系统、账务系统、回调通知等系统的总称。
- 商户证书：商户证书是微信提供的二进制文件，商户系统发起与微信支付后台服务器通信请求的时候，作为微信支付后台识别商户真实身份的凭据。
- 签名：商户后台和微信支付后台根据相同的密钥和算法生成一个结果，用于校验双方身份合法性。签名的算法由微信支付制定并公开，常用的签名方式有：MD5、SHA1、SHA256、HMAC等。
- Openid：用户在公众号内的身份标识，不同公众号拥有不同的openid。商户后台系统通过登录授权、支付通知、查询订单等API可获取到用户的openid。主要用途是判断同一个用户，对用户发送客服消息、模版消息等。企业号用户需要使用企业号userid转openid接口与openid互换接口)将企业成员的userid转换成openid。

### 申请开通微信支付功能

微信公众号商户可以在微信公众平台提交开通微信支付的申请，第三方APP商户可以在微信开放平台提交申请。申请提交后，微信支付工作人员审核资料无误将开通相应的微信支付权限。微信支付申请审核通过后，商户在申请资料填写的邮箱中收取到由微信支付小助手发送的邮件，此邮件包含开发时需要使用的支付账户信息。

- APPID：appid是微信公众账号或开放平台APP的唯一标识，在公众平台申请公众账号或者在开放平台申请APP账号后，微信会自动分配对应的appid，用于标识该应用。
- 微信支付商户号 mch_id：商户申请微信支付后，由微信支付分配的商户收款账号。
- API 密钥：交易过程生成签名的密钥，仅保留在商户系统和微信支付后台，不会在网络中传播。商户妥善保管该Key，切勿在网络中传输，不能在其他客户端中存储，保证key不会被泄漏。
- Appsecret：AppSecret是APPID对应的接口密码，用于[获取接口调用凭证](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html)access_token时使用。在微信支付中，先通过[OAuth2.0接口](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html)获取用户openid，此openid用于微信内网页支付模式下单接口使用。
- 商户平台登录帐号。
- 商户平台登录密码。

开通微信支付功能后，会得到额外三个用于开发微信支付的参数：mch_id, API 密钥, Appsecret。这三个参数在调用微信支付系统的 API 时需要用到。 



## 微信 JSAPI 支付开发

**微信微信 JSAPI 支付开发前提**

- 申请商户的微信支付账号。商户在微信公众平台或开放平台提交微信支付申请，申请成功后可获取其支付账号的相关参数用于微信支付开发。
- 设置支付目录。1）商户最后请求拉起微信支付收银台的页面地址我们称之为“支付目录”，例如：`https://www.weixin.com/pay.php`。2）商户实际的支付目录（支付页面URL）必须和在微信支付商户平台设置的一致。3）如果支付授权目录设置为顶级域名（例如：`https://www.weixin.com/` ），那么只校验顶级域名，不校验后缀。
- 设置授权域名。在统一下单接口中要求必传用户openid，即需要进行网页授权，获取用户信息。

**微信支付系统主要提供的 API 和功能**

- 支付下单、查询订单、支付结果通知（微信回调）
- 申请退款、查询退款、退款通知（微信回调）

详细的 API 列表可参考微信支付官方文档：[微信支付 API 列表](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=9_1)



**微信 JSAPI 支付开发需要的参数**

- 商户号（mch_id）
- API 密钥（key）：交易过程用于生成签名的密钥，仅保留在商户系统和微信支付后台。
- API 证书（application_cert.pem）：微信支付接口中，涉及资金回滚的接口会使用到 API 证书，包括退款、撤销接口。证书文件主要用于退款时发送 SSL 请求。
- 当然还包括公众平台的参数（appId，appSecret）。

其中公众平台的 appId 与微信支付系统的 mch_id 将公众号应用与微信支付帐号进行了绑定。

**微信 JSAPI 支付功能的实现过程**

- 用户发起支付请求。
- 公众号后端，生成商户自己的订单数据（商户订单号），保存到数据库订单表。
- 调用微信统一下单API，得到预付单信息（prepay_id）。下单成功后更新数据库订单表。
- 生成公众号页面调用 JSAPI 支付接口需要的参数并签名（paySign），将参数返回给前端页面。
- 公众号前端页面，调用 JSAPI 支付接口请求支付。
- 微信支付系统验证参数的合法性，若参数合法则弹出微信的确认支付的页面。用户输入支付密码，密码验证通过后会跳转到微信的支付成功页面，并同时发起微信支付的回调。
- 在支付成功页面点击返回商家，跳转到商户指定的页面。商户系统后端调用微信订单查询 API，查询支付结果，生成商户自己的支付结果页面。
- 公众号后端，处理微信支付的回调。进行业务处理后，返回处理结果给微信支付系统。

详细业务流程可参考微信支付官方文档：[JSAPI 支付业务流程时序图](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_4)



**公众号网页调用 JSSDK 支付接口（调起微信支付）**

可以使用微信 JSSDK 的支付 API。也可以使用微信支付封装的JS方法。

1）JSSDK 发起支付请求

```javascript
wx.ready(function(
	// auto execution
	wx.chooseWXPay({
	  timestamp: 0, // 支付签名时间戳，注意微信jssdk中的所有使用timestamp字段均为小写。但最新版的支付后台生成签名使用的timeStamp字段名需大写其中的S字符
	  nonceStr: '', // 支付签名随机串，不长于 32 位
	  package: '', // 统一支付接口返回的prepay_id参数值，提交格式如：prepay_id=\*\*\*）
	  signType: '', // 签名方式，默认为'SHA1'，使用新版支付需传入'MD5'
	  paySign: '', // 支付签名
	  success: function (res) {
	    // 支付成功后的回调函数
	  }
	});
));
```

2）微信支付平台封装的方法

```html
<script>
function onBridgeReady(){
   WeixinJSBridge.invoke(
      'getBrandWCPayRequest', {
         "appId":"wx2421b1c4370ec43b",     //公众号名称，由商户传入     
         "timeStamp":"1395712654",         //时间戳，自1970年以来的秒数     
         "nonceStr":"e61463f8efa94090b1f366cccfbbb444", //随机串     
         "package":"prepay_id=u802345jgfjsdfgsdg888",     
         "signType":"MD5",         //微信签名方式：     
         "paySign":"70EA570631E4BB79628FBCA90534C63FF7FADD89" //微信签名 
      },
      function(res){
      if(res.err_msg == "get_brand_wcpay_request:ok" ){
      // 使用以上方式判断前端返回,微信团队郑重提示：
            //res.err_msg将在用户支付成功后返回ok，但并不保证它绝对可靠。
      } 
   }); 
}

if (typeof WeixinJSBridge == "undefined"){
   if( document.addEventListener ){
       document.addEventListener('WeixinJSBridgeReady', onBridgeReady, false);
   }else if (document.attachEvent){
       document.attachEvent('WeixinJSBridgeReady', onBridgeReady); 
       document.attachEvent('onWeixinJSBridgeReady', onBridgeReady);
   }
}else{
   onBridgeReady();
}
</script>
```

**注意事项**

微信支付需要在微信APP中调试，不能在微信开发者工具中调试。



## References

[1] [微信支付V2版API接口文档](https://pay.weixin.qq.com/wiki/doc/api/index.html)

[2] [微信支付开发文档3.0](https://pay.weixin.qq.com/wiki/doc/apiv3/wxpay/pages/index.shtml)