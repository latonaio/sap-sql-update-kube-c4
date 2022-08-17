# sap-sql-update-kube-c4

sap-sql-update-kube-c4 は、主にエッジコンピューティング環境において SAP API で取得したデータに SQL で保持された静的データとの差分がある場合に、SQLを更新するマイクロサービスです。  
本マイクロサービスには、SQLテーブルへのレコードの登録機能、更新機能が含まれます
本マイクロサービスは、SAP API の Runtime で取得されたデータを RabbitMQ のキューで受け取ります。  

## 動作環境  

* OS: Linux OS  
* CPU: ARM/AMD/Intel  
* Kubernetes  
* [sap-sandbox-c4hana](https://github.com/latonaio/sap-sandbox-c4hana) に含まれる API Runtimes

## クラウド環境での利用  
sap-sql-update-kube-c4 は、クラウド環境においても、利用可能なように設計されています。  

## SQLの登録更新 モジュール SQLBOILER の利用
sap-sql-update-kube-c4 は、SQLの登録更新モジュールとして、[SQLBOILER](https://github.com/volatiletech/sqlboiler)を利用しています。  

## 事前準備（SQLデータベースからのソースコードの生成）
sap-sql-update-kube-c4 のランタイム環境を利用するための事前準備として、  

```
/database/model_generate.sh
```
  
の実行をします。  
これにより、対象のSQLデータベースを読み込み、当該SQLデータベースの登録更新モジュールとして必要なソースコードが、/database/models/ 内に自動生成されます。  
（同一環境内での２回目以降の実行ではソースコードが上書きされます）  

model_generate.sh の 対象データベース接続時の情報は、/database/sqlboiler.toml に書かれる必要があります。    

sap-sql-update-kube-c4 には、ソースコードの初期値として、下記のSAP SQL テーブルを対象として生成されたソースコード群が、/database/models/ 内に含まれています。  

## SQLBoiler のバージョン
本レポジトリは、[SQLBoiler](https://github.com/volatiletech/sqlboiler) 4.8.6 のバージョンを使用しています。  
model_generate.sh のコマンドで、/database/models/ 内のソースコードを生成すると、当該ソースコード内の SQLBoiler のバージョンが最新になるのでご注意ください。      
バージョンを最新にした際は、併せて go modのバージョンを更新し、go mod tidy を行なってください。  

## 対象とする SAP SQL テーブル
本レポジトリには、以下の SAP SQLテーブル のレコードの登録・更新機能が含まれます。  

### Central Functions ###

#### Account And Individual Customer Master ####

* [AccountAndIndividualCustomerMasterCustomerMaster](https://github.com/latonaio/sap-account-and-individual-customer-master-sql/blob/main/sap-account-and-individual-customer-master-sql-data.sql)
* [AccountAndIndividualCustomerMasterAddress](https://github.com/latonaio/sap-account-and-individual-customer-master-sql/blob/main/sap-account-and-individual-customer-master-sql-address-data.sql)    

#### Business User ####

* [BusinessUserC4BusinessUserCollection](https://github.com/latonaio/sap-business-user-sql-c4/blob/main/sap-business-user-sql-c4-business-user-collection-data.sql)
* [BusinessUserC4BusinessUserRoleAssignment](https://github.com/latonaio/sap-business-user-sql-c4/blob/main/sap-business-user-sql-c4-business-user-role-assignment-data.sql)
* [BusinessUserC4EmployeeBasic](https://github.com/latonaio/sap-business-user-sql-c4/blob/main/sap-business-user-sql-c4-employee-basic-data.sql)

#### Business Partner ####

* [BusinessPartnerC4PartnerAddress](https://bitbucket.org/latonaio/sap-business-partner-sql-c4/src/master/sap-business-partner-sql-c4-partner-address-data.sql)
* [BusinessPartnerC4PartnerCollection](https://bitbucket.org/latonaio/sap-business-partner-sql-c4/src/master/sap-business-partner-sql-c4-partner-collection-data.sql)
* [BusinessPartnerC4PartnerHasPartnerContact](https://bitbucket.org/latonaio/sap-business-partner-sql-c4/src/master/sap-business-partner-sql-c4-partner-has-partner-contact-data.sql)
* [BusinessPartnerC4ServiceAgentCollection](https://bitbucket.org/latonaio/sap-business-partner-sql-c4/src/master/sap-business-partner-sql-c4-service-agent-collection-data.sql)

#### Business Partner Relationship ####

* [BusinessPartnerRelationshipRelationship](https://github.com/latonaio/sap-business-partner-relationship-sql/blob/main/sap-business-partner-relationship-sql-relationship-data.sql)

#### Competitor ####

* [CompetitorCompetitorCollection](https://github.com/latonaio/sap-competitor-sql/blob/main/sap-competitor-sql-competitor-collection-data.sql)
* [CompetitorCompetitorSalesOrganizationCollection](https://github.com/latonaio/sap-competitor-sql/blob/main/sap-competitor-sql-competitor-sales-organization-collection-data.sql)

#### Product ####

* [ProductC4ProductCollection](https://github.com/latonaio/sap-product-sql-c4/blob/main/sap-product-sql-c4-product-collection-data.sql)
* [ProductC4ProductOtherDescriptions](https://github.com/latonaio/sap-product-sql-c4/blob/main/sap-product-sql-c4-product-other-descriptions-data.sql)
* [ProductC4ProductSalesProcessInformation](https://github.com/latonaio/sap-product-sql-c4/blob/main/sap-product-sql-c4-product-sales-process-information-data.sql)

#### Competitor Product ####

* [CompetitorProductCompetitorProductCollection](https://github.com/latonaio/sap-competitor-product-sql/blob/main/sap-competitor-product-sql-competitor-product-collection-data.sql)

#### Service Category Catalog ####

* [ServiceCategoryCatalogCatalogCollection](https://github.com/latonaio/sap-service-category-catalog-sql/blob/main/sap-service-category-catalog-sql-catalog-collection-data.sql)
* [ServiceCategoryCatalogCatalogName](https://github.com/latonaio/sap-service-category-catalog-sql/blob/main/sap-service-category-catalog-sql-catalog-collection-data.sql)
* [ServiceCategoryCatalogCatalogUsage](https://github.com/latonaio/sap-service-category-catalog-sql/blob/main/sap-service-category-catalog-sql-catalog-usage-data.sql)

#### Sales Territory ####

* [ServiceSalesTerritorySalesTerritoryCollection](https://github.com/latonaio/sap-sales-territory-sql/blob/main/sap-sales-territory-sql-sales-territory-collection-data.sql)

### Touch Points ###

#### Contact ####

* [ContactContactCollection](https://github.com/latonaio/sap-contact-sql/blob/main/sap-contact-sql-contact-collection-data.sql)
* [ContactContactIsContactPersonFor](https://github.com/latonaio/sap-contact-sql/blob/main/sap-contact-sql-contact-is-contact-person-for-data.sql)
* [ContactCorporateAccount](https://github.com/latonaio/sap-contact-sql/blob/main/sap-contact-sql-corporate-account-data.sql)
* [ContactIndividualCustomerCollection](https://github.com/latonaio/sap-contact-sql/blob/main/sap-contact-sql-individual-customer-collection-data.sql)

#### Activity ####

* [ActivityAppointmentCollection](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-appointment-collection-data.sql)
* [ActivityAppointmentCollectionInvolvedParties](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-appointment-collection-involved-parties-data.sql)
* [ctivityAppointmentCollectionOtherParties](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-appointment-collection-involved-parties-data.sql)
* [ActivityEmailCollection](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-email-collection-recipients-data.sql)
* [ActivityEmailCollectionRecipients](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-email-collection-recipients-data.sql)
* [ActivityEmailCollectionSenderParty](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-email-collection-sender-party-data.sql)
* [ActivityTaskCollection](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-task-collection-data.sql)
* [ActivityTasksInvolvedParties](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-tasks-involved-parties-data.sql)
* [ActivityVisitCollection](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-visit-collection-data.sql)
* [ActivityVisitParty](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-visit-party-data.sql)
* [ActivityVisitWorklistItem](https://github.com/latonaio/sap-activity-sql/blob/main/sap-activity-sql-visit-worklist-item-data.sql)

#### Chat Activity ####

* [ChatActivityChatActivityCollection](https://github.com/latonaio/sap-chat-activity-sql/blob/main/sap-chat-activity-sql-chat-activity-collection-data.sql)
* [ChatActivityChatActivityParties](https://github.com/latonaio/sap-chat-activity-sql/blob/main/sap-chat-activity-sql-chat-activity-parties-data.sql)
* [ChatActivityChatActivityTextCollection](https://github.com/latonaio/sap-chat-activity-sql/blob/main/sap-chat-activity-sql-chat-activity-text-collection-data.sql)

#### Memo Activity ####

* [MemoActivityActivityCollection](https://github.com/latonaio/sap-memo-activity-sql/blob/main/sap-memo-activity-sql-activity-collection-data.sql)

#### Campaign ####

* [CampaignCampaignCollection](https://github.com/latonaio/sap-campaign-sql/blob/main/sap-campaign-sql-campaign-collection-data.sql)
* [CampaignCampaignInboundBizTxDocRef](https://github.com/latonaio/sap-campaign-sql/blob/main/sap-campaign-sql-campaign-inbound-biz-tx-doc-ref-data.sql)

#### Promotion ####

* [PromotionPromotionCollection](https://github.com/latonaio/sap-promotion-sql/blob/main/sap-promotion-sql-promotion-collection-data.sql)
* [PromotionPromotionParty](https://github.com/latonaio/sap-promotion-sql/blob/main/sap-promotion-sql-promotion-party-data.sql)

#### Route ####

* [RouteRouteCollection](https://github.com/latonaio/sap-route-sql/blob/main/sap-route-sql-route-collection-data.sql)
* [RouteRouteAccountCollection](https://github.com/latonaio/sap-route-sql/blob/main/sap-route-sql-route-account-collection.data.sql)
* [RouteRouteInvolvedPartiesCollection](https://github.com/latonaio/sap-route-sql/blob/main/sap-route-sql-route-involved-parties-collection-data.sql)
https://github.com/latonaio/sap-route-sql/blob/main/sap-route-sql-route-collection-data.sql
### Operations  ###

#### PerfectStore ####

* [PerfectStoreExecutionStoreValuationCollection](https://github.com/latonaio/sap-perfect-store-execution-sql/blob/main/sap-perfect-store-execution-sql-store-valuation-collection-data.sql)
* [PerfectStoreExecutionSellingPointValuation](https://github.com/latonaio/sap-perfect-store-execution-sql/blob/main/sap-perfect-store-execution-sql-selling-point-valuation-data.sql)

#### Contract ####

* [Contractcontractcollection](https://github.com/latonaio/sap-contract-sql/blob/main/sap-contract-sql-contract-collection-data.sql)
* [ContractContractExternalPriceComponent](https://github.com/latonaio/sap-contract-sql/blob/main/sap-contract-sql-contract-external-price-component-data.sql)
* [ContractContractItemCollection](https://github.com/latonaio/sap-contract-sql/blob/main/sap-contract-sql-contract-item-collection-data.sql)
* [ContractontractParty](https://github.com/latonaio/sap-contract-sql/blob/main/sap-contract-sql-contract-party-data.sql)

#### Sales Order ####

* [SalesOrderHeader](https://github.com/latonaio/sap-sales-order-sql/blob/main/sap-sales-order-sql-header-data.sql)
* [SalesOrderHeaderPartner](https://github.com/latonaio/sap-sales-order-sql/blob/main/sap-sales-order-sql-header-partner-data.sql)
* [SalesOrderItem](https://github.com/latonaio/sap-sales-order-sql/blob/main/sap-sales-order-sql-item-data.sql)
* [SalesOrderItemPricingElement](https://github.com/latonaio/sap-sales-order-sql/blob/main/sap-sales-order-sql-item-pricing-element-data.sql)
* [SalesOrderItemScheduleLine](https://github.com/latonaio/sap-sales-order-sql/blob/main/sap-sales-order-sql-item-schedule-line-data.sql)

#### Sales Price And Discount List ####

* [SalesPriceAndDiscountListList](https://github.com/latonaio/sap-sales-price-and-discount-list-sql/blob/main/sap-sales-price-and-discount-list-sql-list-data.sql)
* [SalesPriceAndDiscountListListItems](https://github.com/latonaio/sap-sales-price-and-discount-list-sql/blob/main/sap-sales-price-and-discount-list-sql-list-items-data.sql)

#### Payments ####

* [PaymentsPaymentCollection](https://github.com/latonaio/sap-payments-sql/blob/main/sap-payments-sql-payment-collection-data.sql)
* [PaymentsPaymentCollectionItems](https://github.com/latonaio/sap-payments-sql/blob/main/sap-payments-sql-payment-collection-items-data.sql)


### Employee ###

#### Employee Basic Data ####

* [EmployeeBasicDataBusinessUserCollection](https://github.com/latonaio/sap-employee-basic-data-sql/blob/main/sap-employee-basic-data-sql-business-user-collection-data.sql)
* [EmployeeBasicDataBusinessUserRoleAssignmentData](https://github.com/latonaio/sap-employee-basic-data-sql/blob/main/sap-employee-basic-data-sql-business-user-role-assignment-data.sql)
* [EmployeeBasicDataEmployeeBasicData](https://github.com/latonaio/sap-employee-basic-data-sql/blob/main/sap-employee-basic-data-sql-employee-basic-data.sql)

#### Job Definition ####

* [JobDefinitionJobDefinitionCollection](https://github.com/latonaio/sap-job-definition-sql/blob/main/sap-job-definition-sql-job-definition-collection-data.sql)

#### Time Entry ####

* [TimeEntryTimeEntryCollection](https://github.com/latonaio/sap-job-definition-sql/blob/main/sap-job-definition-sql-job-definition-collection-data.sql)

### Authentication ###

#### Identity ####

* [IdentityIdentity](https://github.com/latonaio/sap-identity-sql/blob/main/sap-identity-sql-identity-data.sql)

### Marketing ###

#### Survey Design ####

* [SurveyDesignSurveyRootCollection](https://github.com/latonaio/sap-survey-design-sql/blob/main/sap-survey-root-collection-data.sql)

#### Survey ####

* [SurveySurveyCollection](https://github.com/latonaio/sap-survey-sql/blob/main/sap-survey-sql-survey-collection-data.sql)

#### Survey Response ####

* [SurveyResponseBasic](https://github.com/latonaio/sap-survey-response-sql/blob/main/sap-survey-response-sql-basic-data.sql)


## RabbitMQ からの JSON Input

sap-sql-update-kube-c4 は、Inputとして、RabbitMQ からのメッセージをJSON形式で受け取ります。 

## RabbitMQ からのメッセージ受信による イベントドリヴン の ランタイム実行

sap-sql-update-kube-c4 は、RabbitMQ からのメッセージを受け取ると、イベントドリヴンでランタイムを実行します。  
AION の仕様では、Kubernetes 上 の 当該マイクロサービスPod は 立ち上がったまま待機状態で当該メッセージを受け取り、（コンテナ起動などの段取時間をカットして）即座にランタイムを実行します。　

## RabbitMQ の マスタサーバ環境

sap-sql-update-kube-c4 が利用する RabbitMQ のマスタサーバ環境は、[rabbitmq-on-kubernetes](https://github.com/latonaio/rabbitmq-on-kubernetes) です。  
当該マスタサーバ環境は、同じエッジコンピューティングデバイスに配置されても、別の物理(仮想)サーバ内に配置されても、どちらでも構いません。

## RabbitMQ の Golang Runtime ライブラリ
sap-sql-update-kube-c4 は、RabbitMQ の Golang Runtime ライブラリ として、[rabbitmq-golang-client](https://github.com/latonaio/rabbitmq-golang-client)を利用しています。

## デプロイ・稼働
sap-sql-update-kube-c4 の デプロイ・稼働 を行うためには、aion-service-definitions の services.yml に、本レポジトリの services.yml を設定する必要があります。

kubectl apply - f 等で Deployment作成後、以下のコマンドで Pod が正しく生成されていることを確認してください。
```
$ kubectl get pods
```

## Input JSON の例
例えば、[sap-api-integrations-product-reads-rmq-kube-c4](https://github.com/latonaio/sap-api-integrations-product-reads-rmq-kube-c4)で出力されたデータは、以下の形式でインプットされます。  
```
{
	"cursor": "/Users/latona2/bitbucket/sap-api-integrations-product-reads-c4/SAP_API_Caller/caller.go#L53",
	"function": "sap-api-integrations-product-reads-c4/SAP_API_Caller.(*SAPAPICaller).ProductCollection",
	"level": "INFO",
	"message": [
		{
			"ObjectID": "00163E03A0701EE288BE9895233EBD27",
			"ProductID": "P140100",
			"UUID": "00163E03-A070-1EE2-88BE-9895233EBD27",
			"Language": "EN",
			"LanguageText": "English",
			"Description": "GS Marengo Womens Mountain Bike",
			"ProductCategoryID": "CUSTOMER-01",
			"Status": "3",
			"StatusText": "Blocked",
			"Usage": "",
			"UsageText": "",
			"Division": "",
			"DivisionText": "",
			"BaseUOM": "EA",
			"BaseUOMText": "Each",
			"CreatedBy": "SAP WORKER",
			"LastChangedBy": "Eddie Smoke",
			"CreatedOn": "2012-10-30T03:16:40+09:00",
			"LastChangedOn": "2015-02-04T06:07:03+09:00",
			"CreatedByUUID": "00163E03-A070-1EE2-88B6-F539A6B028F3",
			"LastChangedByUUID": "00163E03-A070-1EE2-88BA-39BD20F290B5",
			"ExternalSystem": "CRM",
			"ExternalID": "P140100",
			"EntityLastChangedOn": "2015-02-04T06:07:03+09:00",
			"ETag": "2016-07-13T20:41:32+09:00",
			"ProductOtherDescriptions": "https://sandbox.api.sap.com/sap/c4c/odata/v1/c4codataapi/ProductCollection('00163E03A0701EE288BE9895233EBD27')/ProductOtherDescriptions",
			"ProductSalesProcessInformation": "https://sandbox.api.sap.com/sap/c4c/odata/v1/c4codataapi/ProductCollection('00163E03A0701EE288BE9895233EBD27')/ProductSalesProcessInformation"
		}
	],
	"time": "2022-06-22T09:16:05+09:00"
}
```
