---
title: "Azure Stack ネットワーク: 違いと考慮事項"
description: "Azure Stack でネットワークを操作する際の違いと考慮事項について説明します。"
services: azure-stack
keywords: 
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 02/28/2018
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 4c881a5f5e64ddc9fc67060208f3bef6ae0f5028
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="considerations-for-azure-stack-networking"></a>Azure Stack ネットワークに関する考慮事項

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack ネットワークは Azure に備えられている機能の多くを備えていますが、デプロイを開始する前に把握しておいていただきたい違いがいくつかあります。


この記事では、Azure Stack のネットワークと その機能に固有の考慮事項の概要を示します。 Azure Stack と Azure の違いの概要については、[重要な考慮事項](azure-stack-considerations.md)のトピックを参照してください。


## <a name="cheat-sheet-networking-differences"></a>チート シート: ネットワークの違い

|サービス | Feature | Azure (グローバル) | Azure Stack |
| --- | --- | --- | --- |
| DNS | マルチテナント DNS | サポートされています| まだサポートされていません|
| |DNS AAAA レコード|サポートされています|サポートされていません|
| |サブスクリプションあたりの DNS ゾーン数|100 (既定値)<br>ご希望により増やすことができます。|100|
| |ゾーンあたりの DNS レコード セット数|5000 (既定値)<br>ご希望により増やすことができます。|5000|
||ゾーンの委任用のネーム サーバー|Azure では、作成されるユーザー (テナント) ゾーンごとに 4 つのネーム サーバーを提供します。|Azure Stack では、作成されるユーザー (テナント) ゾーンごとに 2 つのネーム サーバーを提供します。|
| Virtual Network|仮想ネットワーク ピアリング|同じリージョンに存在する 2 つの仮想ネットワークを Azure のバックボーン ネットワークを介して接続します。|まだサポートされていません|
| |IPv6 アドレス|[ネットワーク インターフェイス構成](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)の一部として IPv6 アドレスを割り当てることができます。|IPv4 のみがサポートされています。|
|VPN ゲートウェイ|ポイント対サイト VPN ゲートウェイ|サポートされています|まだサポートされていません|
| |VNet 間ゲートウェイ|サポートされています|まだサポートされていません|
| |VPN ゲートウェイの SKU|Basic、GW1、GW2、GW3、Standard High Performance、Ultra-High Performance をサポートします。 |Basic、Standard、および High-Performance の SKU をサポートします。|
|Load Balancer|IPv6 パブリック IP アドレス|ロード バランサーへの IPv6 パブリック IP アドレスの割り当てをサポートします。|IPv4 のみがサポートされています。|
|Network Watcher|Network Watcher テナント ネットワーク監視機能|サポートされています|まだサポートされていません|
|CDN|Content Delivery Network のプロファイル|サポートされています|まだサポートされていません|
|Application gateway|レイヤー 7 の負荷分散|サポートされています|まだサポートされていません|
|Traffic Manager|アプリケーションのパフォーマンスと信頼性を最適にするように着信トラフィックをルーティングします。|サポートされています|まだサポートされていません|
|ExpressRoute|オンプレミス インフラストラクチャや共用施設から Microsoft クラウド サービスに高速なプライベート接続を設定します。|サポートされています|Express Route 回線への Azure Stack の接続をサポートします。|

## <a name="next-steps"></a>次の手順

[Azure Stack の DNS](azure-stack-dns.md)
