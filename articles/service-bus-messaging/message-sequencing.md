---
title: Azure Service Bus のメッセージのシーケンス処理とタイムスタンプ | Microsoft Docs
description: タイムスタンプを使用して、Service Bus メッセージのシーケンスと順序を保持します。
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: a67a9d01f686c6aa8a569239667ae14d7bf087a9
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="message-sequencing-and-timestamps"></a>メッセージのシーケンス処理とタイムスタンプ

シーケンス処理とタイムスタンプは、受信または参照するメッセージの [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) プロパティおよび [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) プロパティを通じて Service Bus のすべてのエンティティとサーフェスで常に利用できる 2 つの機能です。

メッセージの絶対的な順序が重要なケースや、コンシューマーが信頼できるメッセージの一意識別子を必要とするケースでは、ブローカーは、キューまたはトピックを基準とした、ギャップのない増分するシーケンス番号をメッセージに付けます。 パーティション分割されたエンティティの場合、シーケンス番号はパーティションを基準として発行されます。

**SequenceNumber** の値は、メッセージがブローカーおよび関数によって受け入れおよび格納されるときに内部識別子として割り当てられる 64 ビットの整数です。 パーティション分割されたエンティティの場合、最上位の 16 ビットはパーティション識別子を表します。 48/64 ビット範囲が使い果たされると、シーケンス番号は 0 にロールオーバーされます。

シーケンス番号はクライアントではなく中立的な中央機関によって割り当てられるため、一意識別子として信頼できます。 また、この番号は真の到着順序を表しており、順序の基準としてはタイムスタンプよりも正確です。なぜなら、タイムスタンプは極端に高いメッセージ送信率での十分な分解能がなく、ブローカーの所有権がノード間で移動する状況では (最小限ではありますが) 時間のずれが生じる可能性があるからです。

絶対的な到着順序は、たとえば限定数の商品を先着順で提供するビジネス シナリオ (コンサート チケットの販売など) で最後の商品を提供する際に重要となります。

タイムスタンプ機能は中立的で信頼できる機関として機能し、**EnqueuedTimeUtc** プロパティで示される、メッセージが到着した UTC 時刻を正確にキャプチャします。 この値は、ビジネス シナリオが期限に依存する場合 (たとえば、作業項目が特定の日付の午前 0 時より前に送信されたが、キューのバックログでかなり遅れて処理される場合など) に役立ちます。

## <a name="scheduled-messages"></a>スケジュール設定されたメッセージ

メッセージを、遅延処理されるようにキューまたはトピックに送信できます。たとえば、特定の時刻にシステムによって処理可能になるようにジョブをスケジュール設定できます。 この機能により、分散された時間ベースのスケジューラが実現します。

スケジュール設定されたメッセージは、定義されたエンキュー時刻まではキューで具体化されません。 その時刻より前であれば、スケジュール設定されたメッセージをキャンセルできます。 キャンセルすると、メッセージが削除されます。

メッセージをスケジュール設定するには、通常の送信パス経由でメッセージを送信するときに [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) プロパティを設定するか、[ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API で明示的に設定します。 後者の場合、スケジュール設定されたメッセージの **SequenceNumber** がただちに返されます。必要な場合はこれを後で使用して、スケジュール設定されたメッセージをキャンセルできます。 スケジュール設定されたメッセージとそのシーケンス番号を、[メッセージのブラウズ](message-browsing.md)を使用して調べることもできます。

スケジュール設定されたメッセージの **SequenceNumber** は、メッセージがこの状態にある間のみ有効です。 アクティブ状態に移行すると、メッセージは即時にエンキューされた場合のようにキューに追加され、新しい **SequenceNumber** を割り当てられます。

この機能は個別のメッセージにアンカーされており、メッセージは 1 回しかエンキューできないため、Service Bus はメッセージの定期的なスケジュールをサポートしていません。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus の基礎](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)