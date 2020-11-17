## receipt验证结果解释

原文请查看[验证收据](https://developer.apple.com/documentation/appstorereceipts/responsebody)

* 由于苹果原文的格式，大多国内开发者不适应其表述格式，为了简化开发人员的理解成本，将响应体作为一个json格式文件，对其中每个字段进行解释。方便整体看到苹果的数据格式，以及对应的含义。解释如下：

```json
{
	"environment" : "Sandbox", //"Production",生成收据的环境
	"is-retryable" : 1, //请求期间发生错误的指示。1表示临时错误，稍后重新验证该收据。0表示一个不能解决的问题，不要重试了。仅适用于状态码21100-21199之间
	"latest_receipt" : "abc", //最新的base64编码的收据。仅针对包含自动续期的收据返回。
	"latest_receipt_info" : [ //包含所有IAP交易的数组，这排除了已被app标记完成的消耗型产品。仅当收据包含自动续期订阅返回
        {
            "cancellation_date" : "124", //ISO8601格式的取消一笔交易的时间。此字段仅适用于退款交易。
            "cancellation_date_ms" : "1243", //Unix纪元时间格式，以毫秒为单位。取消交易时间，或者续订升级的时间。此字段仅适用于退款交易。
            "cancellation_date_pst" : "124", //取消交易的太平洋时间。仅适用于退款交易
            "cancellation_reason" : "1", //退款原因。1表示客户由于您应用的实际或感知问题而取消了交易；0表示其他原因，例如顾客意外购买。
            "expires_date": "", //到期时间或续订时间。ISO 8601格式
            "expires_date_ms" : "1345", //unix纪元时间格式的过期时间或续订时间，毫秒为单位。
            "expires_date_pst" : "", //在太平洋时区的 过期时间或续订时间。
            "is_in_intro_offer_period" : "true", //续订是否在推介促销优惠价格期间。"true"/"false"。
            "is_trial_period" : "false", //订阅是否在免费试用期内的指示器。"true"/"false"。
            "is_upgraded" : "", //指示 一个由于升级而被取消的交易。该字段仅在升级交易中存在。
            "offer_code_ref_name" : "ef24vvg", //优惠代码名称。参考pending_renewal_info.offer_code_ref_name
            "original_purchase_date" : "23", //ISO 8601格式的原始内购时间。
            "original_purchase_date_ms" : "1234", //unix纪元格式的原始内购时间。对于自动续期订阅，该值指示订阅初次购买的时间。
            "original_purchase_date_pst" : "", //太平洋时区的 原始购买时间。
            "original_transaction_id" : "445", //原始交易标识符，参考pending_renewal_info.original_transaction_id
            "product_id" : "com.meitu.myxj.autorenew.vip1", //已购产品的唯一标识符
            "promotional_offer_id" : "423", //参考receipt.in_app.promotional_offer_id
            "purchase_date" : "2020-06-02 07:27:54 Etc/GMT", //AppStore为购买或恢复产品在用户账户中收费的时间。格式类似于ISO 8601
            "purchase_date_ms" : "1591082874000", //对于消耗，非消耗，非续期订阅，AppStore为已购或恢复产品在用户账户收费的时间。以unix纪元格式，毫秒为单位。对于续期产品，AppStore向用户账户收取购买时间或过一段时间后的续订时间。
            "purchase_date_pst" : "2020-06-02 00:27:54 America/Los_Angeles", //太平洋时间格式，AppStore收费时间。
            "quantity" : "1", //消耗型产品购买的数量。最大值为10.
            "subscription_group_identifier" : "14", //订阅所属群组的标识符
            "transaction_id" : "1435", //购买、恢复、续订交易的唯一标识符
            "web_order_line_item_id" : "235", // 跨设备购买事件(包括订阅续订事件)的唯一标识符。此值是识别订阅购买的主键。
        },
	],
	"pending_renewal_info" : [ //一个数组，每个元素包含有product_id标识的每个续订的待续订信息。仅针对包含自动续订的收据返回
		{
			"auto_renew_product_id" : "qwe", //当前续订的首选项。值是一个product_id。仅当用户降级或跨级一个不同时长的订阅才出现该字段。
			"auto_renew_status" : "1", // 1：订阅将在当前订阅期结束时续订； 0：用户已关闭续订
			"expiration_intent" : "1", // 1:用户自愿取消订阅；2：账单错误，比如用户支付信息无效；3：用户不同意最近的涨价；4：续订时无法购买产品；5：未知错误
			"grace_period_expires_date" : "3435", //以ISO 8601的日期时间格式的 续订宽限期的到期时间，
			"grace_period_expires_date_ms" : "123", //以UNIX纪元时间格式的订阅宽限期的到期时间，以毫秒为单位。仅当App开启了账单宽限期且用户续订时发生错误，此字段才存在。使用此时间格式来处理日期。
			"grace_period_expires_date_pst" : "134", //宽限期到期时间，太平洋时区。
			"is_in_billing_retry_period" : "1", // 1:AppStore正尝试续订； 0:AppStore已停止尝试续订.仅当续订处于计费重试的状态时，才存在。
			"offer_code_ref_name" : "1fwg2", //苹果后台配置的优惠代码名称，当用户兑换优惠代码才显示该字段。 
			"original_transaction_id" : "1245", //参考receipt.in_app.original_transaction_id
			"price_consent_status" : "1", //订阅涨价的同意状态。仅当通知顾客涨价后才显示该字段。默认值为0，如果顾客同意了，则改为"1".
			"product_id" : "wnr", //已购产品的唯一标识符。
		},
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
	"status" : 0 //收据有效时为0，或者当有错误发生的时候为一个状态码。状态码反应整个应用收据的状态。
}
```
