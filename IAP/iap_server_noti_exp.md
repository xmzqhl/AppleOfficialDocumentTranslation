# IAP 服务器通知的解释

原文请查看[App Store服务器通知](https://developer.apple.com/documentation/appstoreservernotifications/responsebody)

将服务器通知的格式，以及字段含义解释如下：

```json
{
	"auto_renew_adam_id" : "435", //App Store Connect生成的标识符，AppStore使用该标识符来唯一标识用户订阅续订的续期订阅。将此值视为64位整数
	"auto_renew_product_id" : "com.meitu.myxj.autorenew.vip1",//用户续订的自动续期订阅的产品标识符
	"auto_renew_status" : "true", //可能的值为true，false。自动续期订阅的当前续订状态。注意这些值跟收据中的auto_renew_status不同
	"auto_renew_status_change_date" : "2020", //用户打开或关闭自动续期订阅的续订状态的时间。格式类似于ISO 8601标准
	"auto_renew_status_change_date_ms" : "54321", //unix纪元时间，毫秒
	"auto_renew_status_change_date_pst" : "", //太平洋时间
	"environment" : "PROD", //AppStore生成收据的环境。可能的值：Sandbox, PROD
	"expiration_intent" : "", //订阅过期的原因。该字段仅在一个过期的自动续期订阅中显示。1：用户自愿取消；2：账单错误，比如用户支付信息无效；3：用户不同意涨价；4：续订时iap产品无效；5：未知错误
	"latest_expired_receipt" : "fefw", //最新的base64编码的收据。过期的交易该字段显示在通知中，而不是latest_receipt字段。沙盒环境下该字段已移除。使用unified_receipt.Latest_receipt_info替代
	"latest_expired_receipt_info" : [
	],
	"latest_receipt" : "gef",//最新的base64编码的交易收据。该字段在沙盒环境下已移除。使用unified_receipt对象里的latest_receipt_info替代。
	"latest_receipt_info" : { //在latest_receipt值的json表示。该字段在收据中是一个数组，但是在服务器通知中是一个对象。沙盒下已移除，使用unified_receipt.Latest_receipt_info替代

	},
	"notification_type" : "", //触发该通知的订阅事件。
	"password" : "6543", //跟你在验证收据时请求里提交的共享密钥password字段相同。
	"unified_receipt" : { //包含有关app内最近的IAP交易信息的对象

	},
	"bid" : "com.meitu.myxj", //一个包含应用bundleID的字符串
	"bvrs" : "ytre", //一个包含app bundle版本号的字符串
} 
```