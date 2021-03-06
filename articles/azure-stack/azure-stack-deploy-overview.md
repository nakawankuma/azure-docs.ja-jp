---
title: Azure Stack Development Kit の評価 | Microsoft Docs
description: 評価目的で Azure Stack 開発キットをデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 1deabcf64b3fbf3cbc89232c340a8882cd2591e8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>クイックスタート: Azure Stack Development Kit の評価
[Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) は、Azure Stack の機能やサービスを評価したり、実演したりするためにデプロイできるテスト/開発環境です。 ASDK を使い始めるには、ホスト コンピューター ハードウェアを準備してから、いくつかのスクリプトを実行する必要があります (インストールには数時間かかります)。 その後、管理者ポータルとユーザー ポータルにサインインし、Azure Stack の使用を開始することができます。

## <a name="prerequisites"></a>前提条件 
ASDK をインストールする前に、開発キットをホストするコンピューター (開発キット ホスト) を準備する必要があります。 開発キット ホスト コンピューターは、ハードウェア、ソフトウェア、およびネットワークの最小要件を満たしている必要があります。 

デプロイの ID ソリューションとして、Azure Active Directory (Azure AD) と Active Directory フェデレーション サービス (AD FS) のどちらを使用するかも選択する必要があります。 

開発キット ホストが最小限のハードウェア要件を満たしていることを確認し、インストール プロセスをスムーズに実行できるように、デプロイを開始する前に ID ソリューションを決定してください。 

**[ASDK デプロイ計画に関する考慮事項を確認する](.\asdk\asdk-deploy-considerations.md)**

> [!TIP]
> 開発キット ホスト コンピューターにオペレーティング システムをインストールした後、ハードウェアがすべての要件を満たしていることを確認するには、[Azure Stack デプロイ要件チェック ツール](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)を使用できます。

## <a name="download-and-extract-the-deployment-package"></a>デプロイ パッケージをダウンロードし、展開する
開発キット ホスト コンピューターが ASDK の基本的なインストール要件を満たしていることを確認したら、次の手順は ASDK デプロイ パッケージをダウンロードして展開することです。 デプロイ パッケージには、Cloudbuilder.vhdx ファイルが含まれています。このファイルは、起動可能なオペレーティング システムと Azure Stack インストール ファイルを含む仮想ハード ドライブです。

開発キット ホストや別のコンピューターに、デプロイ パッケージをダウンロードできます。 展開されたデプロイ ファイルは 60 GB の空きディスク容量を占めます。そのため、別のコンピューターを利用することで、開発キット ホストのハードウェア要件を緩和できます。

**[Azure Stack Development Kit (ASDK) をダウンロードして展開する](.\asdk\asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>開発キットのホスト コンピューターを準備する
ASDK をホスト コンピューターにインストールする前に、環境を準備し、VHD から起動するようにシステムを構成する必要があります。 開発キット ホスト コンピューターは、準備ができると、ASDK のデプロイを開始できるように、CloudBuilder.vhdx 仮想マシン ハード ドライブから起動します。

**[ASDK ホスト コンピューターを準備する](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>ホスト コンピューターに ASDK をインストールする
開発キット ホスト コンピューターの準備ができたら、ASDK を CloudBuilder.vhdx イメージにデプロイできます。 ASDK は、asdk-installer.ps1 PowerShell スクリプトをダウンロードして実行することによって提供されるグラフィカル ユーザー インターフェイス (GUI) を使用するか、完全に[コマンド ライン](.\asdk\asdk-deploy-powershell.md)から、デプロイすることができます。 

> [!NOTE]
> 必要に応じて、ホスト コンピューターが CloudBuilder.vhdx で起動された後、ASDK をインストールする "*前*" に、[Azure Stack のテレメトリ設定](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)を構成することができます。


**[Azure Stack Development Kit (ASDK) をインストールする](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>デプロイ後の構成を実行する
ASDK をインストールした後、推奨されるインストール後の確認と構成変更がいくつかあります。 test-AzureStack コマンドレットを使用してインストールが正常に行われたことを検証し、Azure Stack PowerShell および GitHub ツールをインストールすることができます。 

Azure AD を使用するデプロイの後は、Azure Stack の管理者とテナント ポータルの両方をアクティブにする必要があります。 アクティブにすることによって、ディレクトリの全ユーザーに関して適切なアクセス許可 (同意のページに一覧が表示されます) を Azure Stack ポータルと Azure Resource Manager に付与することに同意したことになります。

評価期間が終了する前に開発キット ホストのパスワードが期限切れにならないように、パスワードの有効期限のポリシーをリセットする必要もあります。

> [!NOTE]
> 必要に応じて、ASDK のインストール "*後*" に [Azure Stack のテレメトリ設定](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)を構成することもできます。

**[ASDK デプロイ後のタスク](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Azure に登録する
Azure Stack に [Azure マーケットプレース項目をダウンロード](.\asdk\asdk-marketplace-item.md)できるように、Azure Stack を Azure に登録する必要があります。

**[Azure Stack を Azure に登録する](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>次の手順
お疲れさまでした。 これらの手順を完了すると、[管理者](https://adminportal.local.azurestack.external)ポータルと[ユーザー](https://portal.local.azurestack.external) ポータルの両方を開発キット環境で利用できるようになります。 
