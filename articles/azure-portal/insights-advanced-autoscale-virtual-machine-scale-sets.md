<properties
	pageTitle="Azure Insights: VM スケール セットに対する Resource Manager テンプレートを使用した高度な自動スケール構成 | Microsoft Azure"
	description="複数のルールとプロファイルに基づいて VM スケール セットの自動スケールと、スケール アクションに関する電子メールおよび webhoook の通知を構成します。"
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="ashwink"/>

# VM スケール セットに対する Resource Manager テンプレートを使用した高度な自動スケール構成

仮想マシン スケール セット (VMSS) を使用すると、パフォーマンス メトリックのしきい値、定期的なスケジュール、または特定の日付に基づいてスケールアウトすることができます。また、スケール アクションに対して電子メール通知や webhook 通知を構成することもできます。このチュートリアルでは、上記のすべてを VM スケール セットで Resource Manager テンプレートを使用して構成する例を示します。

>[AZURE.NOTE] このチュートリアルでは VM スケール セットの手順について説明しますが、Cloud Services や Web Apps の自動スケールを設定する場合にも同じ手順を使用できます。CPU などシンプルなパフォーマンス メトリックに基づいて VM スケール セットに単純なスケールイン/スケールアウトを設定する方法については、[Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) や [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) のドキュメントを参照してください。



## チュートリアル
このチュートリアルでは、VMSS の自動スケール設定の構成と更新に [Azure リソース エクスプローラー](https://resources.azure.com/)を使用します。Azure リソース エクスプローラーを使用すると、Resource Manager テンプレートを使用した Azure リソースの管理を容易に行うことができます。Azure リソース エクスプローラー ツールを初めて使用する場合は、まず、[こちらの概要](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/)をご覧ください。

1. 基本的な自動スケール設定を指定して新しい VMSS をデプロイします。この記事では、Azure クイック スタート ギャラリーの VMSS を使用します。ギャラリーには、基本的な自動スケール テンプレートが付随する Windows VMSS が用意されています。以下の手順は、Linux VMSS インスタンスにも同様に適用できます。

2. VMSS を作成したら、Azure リソース エクスプローラーから VMSS リソースに移動します。Microsoft.Insights ノードの下に次の要素が表示されます。

	![Azure 用エクスプローラー](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

	テンプレートを実行すると、**autoscalewad** という名前の既定の自動スケール設定が作成されます。右側に、この自動スケール設定の定義がすべて表示されます。この例では、既定の自動スケール設定に、CPU 使用率に基づくスケールアウトとスケールインのルールが付属しています。

3. また、スケジュールや特定の要件に基づいてプロファイルとルールを追加することもできます。ここでは、3 つのプロファイルを使用して自動スケール設定を作成します。自動スケールのプロファイルとルールの詳細については、[自動スケールのベスト プラクティス](./insights-autoscale-best-practices.md)に関する記事をご覧ください。

    | プロファイルとルール | Description |
	|---------|-------------------------------------|
	| **プロファイル** | **パフォーマンスまたはメトリック ベース** |
	| ルール | Service Bus キューのメッセージ数が x 以上 |
	| ルール | Service Bus キューのメッセージ数が y 以下 |
	| ルール | CPU 使用率が n 以下 |
	| ルール | CPU 使用率が p 以下 |
	| **プロファイル** | **平日の午前中 (ルールなし)** |
	| **プロファイル** | **製品の発売日 (ルールなし)** |

4. 以下は、このチュートリアルで使用するスケーリング シナリオの例です。
	- _**負荷ベース** - VMSS にホストされているアプリケーションの負荷に基づいてスケールアウトまたはスケールインを行います。_
	- _**メッセージ キュー サイズ** - アプリケーションへの受信メッセージ用の Service Bus キューを使用します。キューのメッセージ数と CPU 使用率を使用して、メッセージ数または CPU 使用率のいずれかがしきい値に達したときにスケール アクションをトリガーするように既定のプロファイルを構成します。_
	- _**週と日の時間帯** - "平日の午前中" という名前の、毎週特定の時間帯に実行されるプロファイルを使用します。履歴データから、この時間帯に一定数の VM インスタンスでアプリケーションの負荷を処理すると効率が良くなることがわかっています。_
	- _**特別な日** - "製品の発売日" プロファイルを追加しました。前もって特定の日の計画立てておくことで、市場への発表やアプリケーションへの新製品の設定による負荷にアプリケーションを備えさせることができます。_
	- _最後の 2 つのプロファイルはパフォーマンス メトリックに基づくルールに組み込むこともできますが、ここではそうはしないで、既定のパフォーマンス メトリックに基づくルールを使用します。定期的なプロファイルと日付ベースのプロファイルでは、ルールの使用は任意です。_

	自動スケール エンジンにおけるプロファイルとルールの優先順位付けは、[自動スケールのベスト プラクティス](insights-autoscale-best-practices.md)に関する記事にも記載されています。自動スケールの一般的なメトリックの一覧については、「[Azure Insights の自動スケールの一般的なメトリック](insights-autoscale-common-metrics.md)」をご覧ください。

5. リソース エクスプローラーが**読み取り/書き込み**モードになっていることを確認します。

	![Autoscalewad, default autoscale setting](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. [編集] をクリックします。自動スケール設定の "profiles" 要素を次の内容に**置き換え**ます。

	![profiles](./media/insights-advanced-autoscale-vmss/profiles.png)

	```
	{
	        "name": "Perf_Based_Scale",
	        "capacity": {
	          "minimum": "2",
	          "maximum": "12",
	          "default": "2"
	        },
	        "rules": [
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 10
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 3
	            },
	            "scaleAction": {
	              "direction": "Decrease",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 85
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 60
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          }
	        ]
	      },
	      {
	        "name": "Weekday_Morning_Hours_Scale",
	        "capacity": {
	          "minimum": "4",
	          "maximum": "12",
	          "default": "4"
	        },
	        "rules": [],
	        "recurrence": {
	          "frequency": "Week",
	          "schedule": {
	            "timeZone": "Pacific Standard Time",
	            "days": [
	              "Monday",
	              "Tuesday",
	              "Wednesday",
	              "Thursday",
	              "Friday"
	            ],
	            "hours": [
	              6
	            ],
	            "minutes": [
	              0
	            ]
	          }
	        }
	      },
	      {
	        "name": "Product_Launch_Day",
	        "capacity": {
	          "minimum": "6",
	          "maximum": "20",
	          "default": "6"
	        },
	        "rules": [],
	        "fixedDate": {
	          "timeZone": "Pacific Standard Time",
	          "start": "2016-06-20T00:06:00Z",
	          "end": "2016-06-21T23:59:00Z"
	        }
	      }
	```
	サポートされているフィールドとその値については、[自動スケールの REST API に関するドキュメント](https://msdn.microsoft.com/ja-JP/library/azure/dn931928.aspx)をご覧ください。

	これで、自動スケール設定に、上記の 3 つのプロファイルを含めることができました。

7. 	最後に、自動スケールの**通知**セクションを見てみましょう。自動スケールの通知を使用すると、スケールアウトまたはスケールインのアクションが正常にトリガーされたときに、3 つのことを実行できます。

	1. サブスクリプションの管理者と共同管理者に通知する。

	2. ユーザーのグループに電子メールを送信する。

	3. webhook 呼び出しをトリガーする。この webhook は、呼び出されると、自動スケールの条件と VMSS リソースに関するメタデータが送信されます。自動スケール webhook のペイロードの詳細については、[自動スケールに関する webhook と電子メールの通知を構成する方法](./insights-autoscale-to-webhook-email.md)に関する記事をご覧ください。

	次のコードを自動スケール設定に追加します。具体的には、値が null である **notification** 要素を置き換えます。

	```
	"notifications": [
	      {
	        "operation": "Scale",
	        "email": {
	          "sendToSubscriptionAdministrator": true,
	          "sendToSubscriptionCoAdministrators": false,
	          "customEmails": [
	              "user1@mycompany.com",
	              "user2@mycompany.com"
	              ]
	        },
	        "webhooks": [
	          {
	            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
	            "properties": {
	              "optional_key1": "optional_value1",
	              "optional_key2": "optional_value2"
	            }
	          }
	        ]
	      }
	    ]

	```

	リソース エクスプローラーの **[配置]** ボタンをクリックすると、自動スケール設定が更新されます。

これで、VM スケール セットの自動スケール設定が更新され、複数のスケール プロファイルとスケール通知が追加されました。

## 次のステップ

次のリンク先を使用して、自動スケールの詳細をご確認ください。

[自動スケールの一般的なメトリック](./insights-autoscale-common-metrics.md)

[Azure の自動スケールのベスト プラクティス](./insights-autoscale-best-practices.md)

[PowerShell を使用した自動スケールの管理](./insights-powershell-samples.md#create-and-manage-autoscale-settings)

[CLI を使用した自動スケールの管理](./insights-cli-samples.md#autoscale)

[自動スケールに関する webhook と電子メールの通知の構成](./insights-autoscale-to-webhook-email.md)

<!---HONumber=AcomDC_0810_2016-->