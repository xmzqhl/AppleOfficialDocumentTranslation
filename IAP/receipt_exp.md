## receipt验证结果说明

原文请查看[收据响应体](https://developer.apple.com/documentation/appstorereceipts/responsebody)

**注意：** 该文档翻译校验于`2021.03.16`，如苹果在之后更新官网，可能会有差异，请注意比对。

* 由于苹果原文的格式，大多国内开发者不适应其表述形式，为了简化开发人员的理解成本，将响应体作为一个json格式文件进行说明，方便看到整体的苹果数据格式，以及对应的含义。解释如下：

```json
{
	"environment" : "Sandbox", //"Production",生成收据的环境
	"is-retryable" : 1, //请求期间发生错误的指示。1表示临时错误，稍后重新验证该收据。0表示一个不能解决的问题，不要重试该收据。仅适用于状态码21100-21199之间
	"latest_receipt" : "abc", //最新的base64编码的收据。仅针对包含自动续期的收据返回。
	"latest_receipt_info" : [ //包含所有IAP交易的数组，这排除了已被app标记完成的消耗型产品。仅当收据包含自动续期订阅返回
        {
            "cancellation_date" : "124", //ISO8601格式的取消一笔交易的时间。此字段仅在退款交易中出现。
            "cancellation_date_ms" : "1243", //Unix纪元时间格式，以毫秒为单位。取消交易时间，或者续订升级的时间。此字段仅适用于退款交易。
            "cancellation_date_pst" : "124", //取消交易的太平洋时间。仅适用于退款交易
            "cancellation_reason" : "1", //退款原因。1表示客户由于您应用的实际或感知问题而取消了交易；0表示其他原因，例如顾客意外购买。可能的值：1, 0
            "expires_date": "", //到期时间或将要的续订时间。ISO 8601格式
            "expires_date_ms" : "1345", //unix纪元时间格式的过期时间或续订时间，毫秒为单位。
            "expires_date_pst" : "", //在太平洋时区的 过期时间或续订时间。
            "in_app_ownership_type" : "FAMILY_SHARED", 指示用户是该产品的购买者，还是可以"家庭共享"访问该产品的家庭成员。FAMILY_SHARED:该交易属于从服务中受益的家庭成员；PURCHASED:该交易属于该购买者
            "is_in_intro_offer_period" : "true", //续订是否在推介促销优惠价格期间。"true"/"false"。
            "is_trial_period" : "false", //订阅是否在免费试用期内的指示器。"true"/"false"。
            "is_upgraded" : "true", //指示 一个由于升级而被取消的交易。该字段仅在升级交易中存在。值:true
            "offer_code_ref_name" : "ef24vvg", //在苹果后台配置的订阅优惠参考名称。当客户兑换一个订阅优惠代码时，显示此字段。使用该值来 确定订阅的销售是否来自优惠代码活动；确定用户兑换的具体优惠；跟踪客户已兑换的订阅优惠代码来根据你的业务模型来限制您提供的折扣                                        
            "original_purchase_date" : "23", //ISO 8601格式的原始内购时间。
            "original_purchase_date_ms" : "1234", //unix纪元格式的原始购买时间，毫秒为单位，使用该时间格式来处理日期。对于自动续期订阅，该值指示订阅初次购买的时间。原始购买日期适用于所有的产品类型，并在相同productID的所有交易中都保持不变
            "original_purchase_date_pst" : "", //太平洋时区的 原始购买时间。
            "original_transaction_id" : "445", //原始交易标识符，参考pending_renewal_info.original_transaction_id
            "product_id" : "com.meitu.myxj.autorenew.vip1", //已购产品的唯一标识符
            "promotional_offer_id" : "423", //用户兑换续期订阅促销优惠的标识符。你在苹果后台创建促销优惠时提供了该值。你可以使用促销优惠id来确认订阅的销售来自于促销优惠、确认用户兑换了哪个促销优惠、根据您的业务模型跟踪用户已兑换的促销优惠以限制您提供的折扣
            "purchase_date" : "2020-06-02 07:27:54 Etc/GMT", //AppStore为购买或恢复产品在用户账户中收费的时间。格式类似于ISO 8601
            "purchase_date_ms" : "1591082874000", //对于消耗，非消耗，非续期订阅，AppStore为已购或恢复产品在用户账户收费的时间。以unix纪元格式，毫秒为单位。对于续期产品，AppStore向用户账户收取购买时间或过一段时间后的续订时间。
            "purchase_date_pst" : "2020-06-02 00:27:54 America/Los_Angeles", //太平洋时间格式，AppStore收费时间。
            "quantity" : "1", //消耗型产品购买的数量。除非使用可变payment对象修改，否则该值通常为1。最大值为10.
            "subscription_group_identifier" : "14", //订阅所属群组的标识符。跟SKProduct的subscriptionGroupIdentifier一致
            "transaction_id" : "1435", //购买、恢复、续订交易的唯一标识符
            "web_order_line_item_id" : "235", // 跨设备购买事件(包括订阅续订事件)的唯一标识符。此值是识别订阅购买的主键。
        },...
	],
	"pending_renewal_info" : [ //一个数组，每个元素包含有product_id标识的每个续订的待续订信息。仅针对包含自动续订的收据返回
		{
			"auto_renew_product_id" : "qwe", //当前续订的首选项。值是一个product_id。仅当用户降级或跨级一个不同时长的订阅才出现该字段。
			"auto_renew_status" : "1", // 1：订阅将在当前订阅期结束时续订； 0：用户已关闭续订
			"expiration_intent" : "1", // 1:用户自愿取消订阅；2：账单错误，比如用户支付信息无效；3：用户不同意最近的涨价；4：续订时无法购买产品；5：未知错误
			"grace_period_expires_date" : "3435", //以ISO 8601的日期时间格式的 续订宽限期的到期时间，
			"grace_period_expires_date_ms" : "123", //以UNIX纪元时间格式的订阅宽限期的到期时间，以毫秒为单位。仅当App开启了账单宽限期且用户续订时发生错误，此字段才存在。使用此时间格式来处理日期。
			"grace_period_expires_date_pst" : "134", //宽限期到期时间，太平洋时区。
			"is_in_billing_retry_period" : "1", // 1:AppStore正尝试续订； 0:AppStore已停止尝试续订.仅当续订处于计费重试的状态时，才存在。如果此字段已移除，并且存在一个具有未来expires_date的新交易，则确定已成功向用户计费；如果该值为1，则通知用户其账号信息可能存在问题。例如，信用卡过期或余额不足可能会阻碍对客户的账户计费。如果值为1，且expires_date是过去的时间，则实施宽限期以提升恢复，当用户处于计费重试状态时，宽限期是免费或限制订阅访问。参阅[https://developer.apple.com/videos/play/wwdc2018/705/]查看更多信息
			"offer_code_ref_name" : "1fwg2", //苹果后台配置的优惠代码名称，当用户兑换优惠代码才显示该字段。使用该值来 确定订阅的销售是否来自优惠代码活动；确定用户兑换的具体优惠；跟踪客户已兑换的订阅优惠代码来根据你的业务模型来限制您提供的折扣。
			"original_transaction_id" : "1245", //参考receipt.in_app.original_transaction_id
			"price_consent_status" : "1", //订阅涨价的同意状态。仅当通知顾客涨价后才显示该字段。默认值为0，如果顾客同意了，则改为"1".可能的值：1，0
			"product_id" : "wnr", //已购产品的唯一标识符。
			"promotional_offer_id" : "aa" //用户兑换的自动续期订阅的促销优惠标识符。在苹果后台创建促销优惠时，您在"促销优惠标识符"字段中提供了该值。
		},...
	],
	"receipt" : { //被发送验证的收据的json表示。
		"adam_id", 3245432, //参阅app_item_id.
		"app_item_id" : 543213, //由App Store Connect生成，被AppStore使用来唯一标识应用购买。仅在生产环境中为app分配此标识符。将此值视为64位整数。
		"application_version" : "3432", //应用版本号。注意是build号，并非常用的版本号。在生产环境中，该值是基于收据设备上应用的当前版本。沙盒下该值始终为"1.0"
		"bundle_id" : "com.meitu.myxj", //包名
		"download_id" : 12424, //app下载交易的唯一标识符
		"expiration_date" : "1234", //通过批量购买计划购买的应用购买收据的过期时间。格式类似于ISO 8601.
		"expiration_date_ms" : "7654321", //批量购买计划购买的过期时间，以unix纪元格式，毫秒为单位。如果这个key不存在，则收据还没有过期。
		"expiration_date_pst" : "36543", //太平洋时区的批量购买计划过期时间。
		"original_application_version" : "4312", 用户原始购买的app版本，该值不会改变。值为build号。沙盒下始终为"1.0"
		"original_purchase_date" : "", //原始应用购买时间，格式类似于ISO 8601.
		"original_purchase_date_ms" : "",
		"original_purchase_date_pst" : "",
		"preorder_date" : "432", //用户预订的可用的 订购该app的时间
		"preorder_date_ms" : "",
		"preorder_date_pst" : "",
		"receipt_creation_date" : "34", //AppStore生成该收据的时间，以ISO 8601格式
		"receipt_creation_date_ms" : "",
		"receipt_creation_date_pst" : "",
		"receipt_type" : "Production", //生成的收据的类型。该值对应购买时App或VPP的环境。可能的值为"Production"/"ProductionVPP"/"ProductionSandbox"/"ProductionVPPSandbox"
		"request_date" : "", //到verifyReceipt的末端的请求被处理和响应被生成的时间。类似于ISO 8601格式。
		"request_date_ms" : "1234",
		"request_date_pst" : "",
		"version_external_identifier" : 0, //标识应用修订版的任意数字，沙盒下始终为0.
		in_app : [ //包含所有内购交易的内购收据字段的数组
                {
                    "cancellation_date" : "1343", //苹果客户支持取消一个交易，或一个续订计划被升级的时间。以ISO 8601格式。该字段仅出现在退款交易
                    "cancellation_date_ms" : "1234",
                    "cancellation_date_pst" : "", //太平洋时间
                    "cancellation_reason" : "1", // 退款交易的原因。1表示因为实际或app内感知问题而取消；0表示其他原因。
                    "expires_date" : "", //订阅过期时间，或续订时间。以类似于ISO 8601格式。
                    "expires_date_ms" : "",
                    "expires_date_pst" : "",
                    "is_in_intro_offer_period" : "true", //续订是否在推介促销优惠价格期间。"true"/"false"。
                    "is_trial_period" : "false", // 订阅是否在免费试用期内的指示器。"true"/"false"。
                    "original_purchase_date" : "23", //原始应用内购买的时间。iso 8601格式
                    "original_purchase_date_ms" : "", //对于续期订阅，该值指示初次购买的时间。
                    "original_purchase_date_pst" : "", 
                    "original_transaction_id" : "4453231", //原始交易标识符。使用此值来 将收据中的交易跟服务器通知事件匹配、管理自动续期订阅、标识在pending_renewal_info区域的订阅，当看到product_id的一个新original_transaction_id值时，将此视为新订阅、区分购买交易与恢复交易或续订交易，在购买交易中，transaction_id始终与original_transaction_id匹配。对于订阅，它指示首次订阅购买。对于恢复或续订，transaction_id与original_transaction_id不匹配、 标识同一订阅的一个或多个续订。
                    "product_id" : "24", //已购产品的唯一标识符
                    "promotional_offer_id" : "34", //用户兑换的订阅优惠的标识符。使用此值来 确认订阅的销售来自促销优惠、确认用户兑换了哪种促销优惠、根据业务来跟踪用户已兑换的优惠以限制提供的折扣。
                    "purchase_date" : "421",//AppStore为购买或恢复产品从用户账户扣费的时间。或为订阅购买或一段时间后的续订的扣费时间。ISO 8601格式
                    "purchase_date_ms" : "4312",
                    "purchase_date_pst" : "543", 
                    "quantity" : "1", //购买的消耗型产品的数量。通常为1，最大值为10.
                    "transaction_id" : "134", //比如购买，恢复，续订交易的唯一标识符。你可以使用此值来1、管理你账户数据库中的订阅。为每个交易存储transaction_id、original_transaction_id和product_id来作为为每个用户交易记录的最佳的实践。每次订阅续订时或在新设备上恢复时，AppStore会生成一个新值；2、将购买的交易与一个恢复的或一个续订的交易区分开，在一个购买交易中，transaction_id始终与original_transaction_id匹配。对于订阅，它指示首次订阅购买。对于恢复或续订，transaction_id与original_transaction_id不匹配。如果用户多次恢复或续订相同的购买，则每次恢复或续订有一个不同的transaction_id。
                    "web_order_line_item_id" : "432", //跨设备购买事件(包括订阅续订事件)的唯一标识符。此值是识别订阅购买的主键。
                }
        ],
	},
	"status" : 0 //收据有效时为0，或者当有错误发生的时候为一个状态码。状态码反应整个应用收据的状态。有关可能的状态码和说明，请查看下文
}
```

<a name='status'></a>
关于最后一个字段`status`表示App凭证的状态。
## status可能的值

### 21000
到AppStore的请求不是用的HTTP POST方法

### 21001
AppStore已不再发送此状态码

### 21002
在receipt-data属性中的数据格式有误，或者服务遇到临时问题。再试一次。

### 21003
该收据无法认证

### 21004
你提供的共享密钥与账户文件的共享密钥不匹配

### 21005
收据服务器暂时无法提供收据。再试一次

### 21006
该收据有效，但订阅已过期。当该状态码返回到你的服务器时，收据数据也会被解码作为响应的一部分。仅针对自动续期订阅的`iOS 6`样式的交易收据。

### 21007
该收据来自于测试环境。但它被发送到了生产环境来验证。

### 21008
该收据来自于生产环境，但它被发送到测试环境验证。

### 21009
内部数据访问错误。重试一次

### 21010
用户账户找不到或已经被删除

## 讨论
该status字段被作为JSON响应体返回。如果收据有效，status的值是0；如果有一个错误发生，是一个状态码。该status的值反应收据整体的状态。例如，如果你发送一个包含了一个过期订阅的有效App收据，响应为0，因为该收据是有效的。

状态码21100-21199是各种内部数据访问错误。

##对上述json字段中某些字段的详细解释如下：

    pending_renewal_info

在json文件中，`pending_renewal_info`是一个数组，其中每个元素都是包含由`product_id`标识的每个续期订阅的待续订信息。一个待续订可能指系统在未来计划的续订，或由于一些原因而失败的续订。

使用此值可获取自动续期订阅的任何待续订交易的重要信息。

仅针对包含自动续期订阅的收据才会返回`pending_renewal_info`数组。如果客户在宽限期内自愿取消订阅续订，AppStore暂停账单重试，并从`pending_renewal_info`中移除交易。如果存在`grace_period_expires_date_ms`键，并且过期日期还没有过去，则订阅处于宽限期。

    original_transaction_id

该字段在json响应的`responseBody.pending_renewal_info`，`responseBody.latest_receipt_info`和`responseBody.receipt.in_app`数组中返回。

除了用户恢复一个交易或者续订一个订阅，该值与[transaction_id](https://developer.apple.com/documentation/appstorereceipts/transaction_id)相同。该值与`transaction`的`transactionIdentifier`属性具有相同的格式；但是，值可能不同。对于自动续期订阅的交易，此值还会显示在json响应的`pending_renewal_info`键中。

你可以使用该值来：

*  将收据中找到的交易与`server-to-server`的通知事件进行匹配。有关更多信息，请参阅[启用AppStore服务器通知](https://developer.apple.com/documentation/appstoreservernotifications/enabling_app_store_server_notifications)

* 管理自动续期订阅。最为最佳做法，请为每个客户的每次交易存储此值，`product_id`，`expires_date_ms`和`purchase_date_ms`。

* 在`pending_renewal_info`部分中标识带有`product_id`的订阅。不要自己依赖`original_transaction_id`的值。当您看到一个`product_id`的新的`original_transaction_id`值时，将此购买视为新订阅。

* 将购买交易与恢复交易或续订交易区分开。在一个购买交易中，`transaction_id`始终与`original_transaction_id`匹配。对于订阅，它表示首次订阅购买。对于恢复或续订交易，`transaction_id`和`original_transaction_id`不匹配。

* 确定同一个订阅的一个或多个续订。

        auto_renew_status
   自动续期订阅的续订状态。字符串。
   
   可能的值，1：订阅将在当前订阅周期结束时续订；0：客户已经为该订阅关闭了自动续订。
   
   该字段在json响应的responseBody.pending_renewal_info数组中返回。
   
   该字段的值，不应解释为客户的订阅状态。你可以使用该值来在您的App中显示其他订阅产品，比如用户可以从当前计划降级到级别较低的订阅计划。
  
  如果`auto_renew_status`为"0"，请考虑在同一订阅群组中提供有吸引力的升级或降级优惠。有关更多信息，请参阅WWDC 2018的[工程订阅](https://developer.apple.com/videos/play/wwdc2018/705/)











