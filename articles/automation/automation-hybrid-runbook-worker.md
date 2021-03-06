---
title: Azure Automation の Hybrid Runbook Worker
description: この記事では、ローカル データ センターまたはクラウド プロバイダー内のコンピューターで Runbook を実行できるようにする Azure Automation の機能である Hybrid Runbook Worker のインストールと使用について説明します。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7065ec97e1e02dfb4ee873993caac584f6a63ba6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker を使用してデータ センターまたはクラウドのリソースを自動化する

Azure Automation の Runbook は Azure クラウドで実行されるため、他のクラウドやオンプレミス環境のリソースにはアクセスできません。 Azure Automation の Hybrid Runbook Worker 機能を使用すると、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。

次の図にこの機能を示します。

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/automation.png)

Hybrid Runbook Worker ロールの技術的な概要とデプロイの考慮事項については、「[Automation アーキテクチャの概要](automation-offering-get-started.md#automation-architecture-overview)」を参照してください。

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker のグループ

各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。 グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。 要求を処理するワーカーは、グループのメンバーが決定します。 特定のワーカーを指定することはできません。

## <a name="relationship-to-service-management-automation"></a>Service Management Automation との関係

[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) では、ローカル データ センターの Azure Automation でサポートされているものと同じ Runbook を実行することができます。 Windows Azure Pack には SMA 管理用のグラフィカル インターフェイスが含まれているため、SMA は Windows Azure Pack と共にデプロイされます。 Azure Automation とは異なり、SMA には API をホストする Web サーバー、Runbook と SMA の構成を保持するためのデータベース、およびRunbook ジョブを実行する Runbook Worker を含むローカル インストールが必要です。 Azure Automation ではクラウドでこれらのサービスが提供されるため、必要なのはローカル環境での Hybrid Runbook Worker の維持のみです。

既存の SMA ユーザーの場合は、何も変更しなくても Hybrid Runbook Worker で使用する Azure Automation に Runbook を移動できます。ただし、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」の説明に従って、リソースを独自に認証することが前提となります。 SMA の Runbook は、Runbook の認証を提供できるワーカー サーバーのサービス アカウントのコンテキストで実行されます。

Hybrid Runbook Worker 機能を持つ Azure Automation と Service Management Automation のどちらがより要件に合っているかを判断するために、次の条件を使用できます。

* グラフィカル管理インターフェイスが必要な場合、SMA には Windows Azure パックに接続されている、基になるコンポーネントのローカル インストールが必要です。 ローカル Runbook ワーカーにインストールされているエージェントのみを必要とする Azure Automation よりも、ローカル リソースとメンテナンス コストがかかります。 エージェントは Azure によって管理され、メンテナンス コストがより削減されます。
* Azure Automation はその Runbook をクラウド内に格納し、オンプレミスの Hybrid Runbook Worker に配信します。 セキュリティ ポリシーでこの動作が許可されていない場合は、SMA を使用する必要があります。
* SMA は System Center に付属するため、System Center 2012 R2 のライセンスが必要です。 Azure Automation は、階層化されたサブスクリプション モデルに基づきます。
* Azure Automation には、SMA で使用できないグラフィカル Runbook などの拡張機能があります。

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker のインストール

Windows Hybrid Runbook Worker は、以下の 2 つの方法でインストール、構成できます。 Automation Runbook を使用して、Windows コンピューターの構成に必要なプロセスを完全に自動化する方法をお勧めします。 2 番目の方法は、ロールを手動でインストールして構成する手順を段階的に追うものです。

> [!NOTE]
> Hybrid Runbook Worker ロールをサポートするサーバーの構成を Desired State Configuration (DSC) を使用して管理するには、サーバーを DSC ノードとして追加する必要があります。 DSC による管理のためのサーバーのオンボードの詳細については、「[Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」を参照してください。
>
>[更新管理ソリューション](../operations-management-suite/oms-solution-update-management.md)を有効にすると、このソリューションに含まれている Runbook をサポートするために、Log Analytics ワークスペースに接続された Windows コンピューターはすべて Hybrid Runbook Worker として自動的に構成されます。 ただし、このコンピューターは、Automation アカウントで既に定義した可能性のあるハイブリッド worker グループには登録されません。 このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用していれば、このコンピューターを Automation アカウントの Hybrid Runbook Worker に追加して Automation Runbook をサポートできます。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。

Hybrid Runbook Worker のデプロイを開始する前に、[ハードウェアとソフトウェアの要件](automation-offering-get-started.md#hybrid-runbook-worker)および[ネットワークを準備するための情報](automation-offering-get-started.md#network-planning)に関する情報を見直します。 Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。

### <a name="automated-deployment"></a>自動化されたデプロイ

Windows Hybrid Worker ロールのインストールと構成を自動化するには、次の手順を実行します。

1. [PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) の *New-OnPremiseHybridWorker.ps1* スクリプトを Hybrid Runbook Worker ロールを実行しているコンピューターから、または環境内の別のコンピューターから直接ダウンロードし、worker にコピーします。

   *New-OnPremiseHybridWorker.ps1* スクリプトは、実行中に次のパラメーターを必要とします。

   * *AutomationAccountName* (必須) - Automation アカウントの名前。
   * *AAResourceGroupName* (必須) - Automation アカウントに関連付けられているリソース グループの名前。
   * *OMSResourceGroupName* (省略可能) - OMS ワークスペース用のリソース グループの名前。 指定されていない場合は、AAResourceGroupName が使用されます。
   * *HybridGroupName* (必須) - このシナリオをサポートしている Runbook のターゲットとして指定する Hybrid Runbook Worker グループの名前。
   * *SubscriptionID* (必須) - Automation アカウントが存在する Azure サブスクリプション ID。
   * *WorkspaceName* (省略可能) - Log Analytics ワークスペースの名前。 Log Analytics ワークスペースがない場合は、スクリプトがこれを作成して構成します。

     > [!NOTE]
     > 現在、Log Analytics との統合がサポートされている Automation リージョンは、**オーストラリア南東部**、**米国東部 2**、**東南アジア**、および**西ヨーロッパ**だけです。 Automation アカウントがこれらのいずれかのリージョンに存在しない場合、スクリプトは Log Analytics ワークスペースを作成しますが、それらをリンクできないことをユーザーに警告します。

2. コンピューターの**スタート**画面から、管理者モードで **Windows PowerShell** を起動します。
3. PowerShell コマンド ライン シェルから、ダウンロードしたスクリプトを含むフォルダーに移動し、そのスクリプトを実行して *-AutomationAccountName*、*-AAResourceGroupName*、*-OMSResourceGroupName*、*-HybridGroupName*、*-SubscriptionId*、および *-WorkspaceName* パラメーターの値を変更します。

     > [!NOTE]
     > スクリプトの実行後、Azure での認証が求められます。 サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してサインインする**必要があります**。

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. **NuGet** のインストールに同意するように求められ、Azure 資格情報で認証するように求められます。

5. スクリプトが完了したら、[ハイブリッド worker グループ] ブレードには新しいグループとメンバーの数が表示されますが、既存のグループの場合はメンバーの数がインクリメントされます。 **[ハイブリッド worker グループ]** ブレード上にあるリストからグループを選択し、**[ハイブリッド worker]** タイルを選択できます。 **[ハイブリッド worker]** ブレードで、グループの各メンバーが一覧表示されます。

### <a name="manual-deployment"></a>手動デプロイ

最初の 2 つのステップは Automation 環境に対して 1 回だけ実行し、残りのステップは worker コンピューターごとに繰り返します。

#### <a name="1-create-log-analytics-workspace"></a>1.Log Analytics ワークスペースの作成

Log Analytics ワークスペースがまだない場合は、[ワークスペースの管理](../log-analytics/log-analytics-manage-access.md)に関するページの手順に従って作成します。 既存のワークスペースがある場合は、それを使用できます。

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2.Log Analytics ワークスペースに Automation ソリューションを追加する

ソリューションにより、Log Analytics に機能が追加されます。 Automation ソリューションは、Hybrid Runbook Worker のサポートなど、Azure Automation 用の機能を追加します。 ソリューションをワークスペースに追加すると、次の手順でインストールする worker コンポーネントがエージェント コンピューターに自動的にプッシュダウンされます。

[ソリューション ギャラリーを使用したソリューションの追加](../log-analytics/log-analytics-add-solutions.md)に関するページの手順に従って、Log Analytics ワークスペースに **Automation** ソリューションを追加します。

#### <a name="3-install-the-microsoft-monitoring-agent"></a>手順 3.Microsoft Monitoring Agent をインストールする

Microsoft Monitoring Agent は Log Analytics にコンピューターを接続します。 エージェントをオンプレミスのコンピューターにインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

「[Windows コンピューターを Log Analytics に接続する](../log-analytics/log-analytics-windows-agent.md)」の手順に従って、オンプレミスのコンピューターにエージェントをインストールします。 コンピューターごとにこのプロセスを繰り返して、複数の worker を環境に追加できます。

C:\Program Files\Microsoft Monitoring Agent\Agent に **AzureAutomationFiles** という名前のフォルダーが作成されていることを調べて、エージェントが Automation ソリューションを正常にダウンロードしたことを確認できます。 Hybrid Runbook Worker のバージョンを確認するには、C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ に移動し、\\*バージョン* サブフォルダーをメモします。  

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.Runbook 環境をインストールして、Azure Automation に接続する

エージェントを Log Analytics に追加すると、Automation ソリューションは、**Add-HybridRunbookWorker** コマンドレットを含む **HybridRegistration** PowerShell モジュールをプッシュダウンします。 コンピューターに Runbook 環境をインストールして、Azure Automation に登録する場合は、このコマンドレットを使用します。

このモジュールをインポートするには、管理者モードで PowerShell セッションを開き、次のコマンドを実行します。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

次に、以下の構文を使用して、 **Add-HybridRunbookWorker** コマンドレットを実行します。

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

このコマンドレットで必要な情報は、Automation アカウントの **[アカウント設定]** の下の **[キー]** オプションをクリックすることで取得できます。

* **GroupName** は、Hybrid Runbook Worker グループの名前です。 Automation アカウントにこのグループが既に存在する場合は、現在のコンピューターがそれに追加されます。 まだ存在しない場合は、追加されます。
* **EndPoint** は、**[キー]** ページの **[URL]** フィールドです。
* **Token** は、**[キー]** ページの **[プライマリ アクセス キー]** です。

インストールに関する詳細な情報を受け取るには、**Add-HybridRunbookWorker** で **-Verbose** スイッチを使用します。

#### <a name="5-install-powershell-modules"></a>5.PowerShell モジュールをインストールする

Runbook は、Azure Automation 環境にインストールされているモジュールで定義されているアクティビティとコマンドレットをすべて使用できます。 これらのモジュールはオンプレミス コンピューターに自動的に配置されないため、手動でインストールする必要があります。 ただし、Azure モジュールは既定でインストールされ、すべての Azure サービスのコマンドレットと Azure Automation のアクティビティにアクセスできます。

Hybrid Runbook Worker 機能の主な目的はローカル リソースを管理することであるため、ほとんどの場合、これらのリソースをサポートするモジュールをインストールする必要があります。 Windows PowerShell モジュールのインストールについては、「 [モジュールのインストール](http://msdn.microsoft.com/library/dd878350.aspx) 」を参照してください。 インストールされるモジュールは PSModulePath 環境変数によって参照されている場所に置き、ハイブリッド worker によって自動的にインポートされるようにする必要があります。 詳細については、「[Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx)」(PSModulePath インストール パスの変更) を参照してください。

## <a name="removing-hybrid-runbook-worker"></a>Hybrid Runbook Worker の削除

要件に応じて、グループから 1 つ以上の Hybrid Runbook Worker を削除したり、グループを削除することができます。 オンプレミス コンピューターから Hybrid Runbook Worker を削除するには、次の手順を実行します。

1. Azure Portal で、Automation アカウントに移動します。
2. **[設定]** ブレードから、**[キー]** を選択し、フィールドの **[URL]** と **[プライマリ アクセス キー]** の値をメモします。 この情報は、次の手順に必要です。
3. 管理者モードで PowerShell セッションを開き、次のコマンド - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>` を実行します。 削除処理の詳細なログを取得するには、 **-Verbose** スイッチを使用します。

ハイブリッド Worker グループから古いコンピューターを削除するには、省略可能な `machineName` パラメーターを使用します。

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

> [!NOTE]
> これによってコンピューターから Microsoft Monitoring Agent が削除されることはありません。Hybrid Runbook Worker ロールの機能と構成のみが削除されます。

## <a name="remove-hybrid-worker-groups"></a>Hybrid Worker グループを削除する

グループを削除するには、まず、先ほどの手順を使用して、グループのメンバーであるすべてのコンピューターから Hybrid Runbook Worker を削除する必要があります。その後、次の手順を実行してグループを削除します。

1. Azure ポータルで Automation アカウントを開きます。
1. **[プロセス オートメーション]** で **[ハイブリッド Worker グループ]** を選択します。 削除するグループを選択します。 特定のグループを選択すると、**[ハイブリッド Worker グループ]** プロパティ ブレードが表示されます。

   ![Hybrid Runbook Worker グループ ブレード](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. 選択したグループのプロパティ ブレードで、**[削除]** をクリックします。 このアクションの確認を求めるメッセージが表示されるので、続行する場合は **[はい]** をクリックします。

   ![グループの削除の確認ダイアログ](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。

## <a name="troubleshooting"></a>トラブルシューティング

Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うために Microsoft Monitoring Agent に依存しています。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

1. Hybrid Worker がプロキシまたはファイアウォールの内側にある。

   コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

2. Hybrid Worker を実行しているコンピューターが、ハードウェアの最小[要件](automation-offering-get-started.md#hybrid-runbook-worker)を満たしていない。

   Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするよう指定する前に、ハードウェアの最小要件を満たしている必要があります。 満たしていない場合、他のバックグラウンド プロセスのリソース使用状況および実行中の Runbook による競合によっては、コンピューターが過負荷になり、Runbook ジョブが遅延またはタイムアウトします。

   Hybrid Runbook Worker の機能を実行するように指定されているコンピューターがハードウェアの最小要件を満たしていることを確認します。 満たしている場合は、CPU とメモリの使用状況を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。 メモリまたは CPU に負荷がかかる場合は、リソースのボトルネックを解消してエラーを解決するには、アップグレード、プロセッサの追加、またはメモリの増設が必要である可能性があります。 または、最小要件を満たす異なるコンピューティング リソースを選択し、ワークロードがさらに多くのリソースを必要とすることを示したときは拡張します。

3. Microsoft Monitoring Agent サービスが実行されていない。

   Microsoft Monitoring Agent の Windows サービスが実行されていない場合、Hybrid Runbook Worker は Azure Automation と通信できません。 PowerShell で次のコマンドを入力して、このエージェントが実行されていることを確認します: `get-service healthservice`。 サービスが停止している場合は、PowerShell で次のコマンドを入力してサービスを開始します: `start-service healthservice`。

4. **アプリケーションとサービス ログ\Operations Manager** イベント ログに、イベント 4502 と **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** および次の説明を含む EventMessage が表示されます: *サービス \<wsid\>.oms.opinsights.azure.com によって提供された証明書は、Microsoft サービスに使用される証明機関によって発行されていません。TLS/SSL 通信を遮断するプロキシが実行されているかどうかをネットワーク管理者に問い合わせてください。記事 KB3126513 に、接続の問題に関するトラブルシューティング情報が記載されています。*"
    これは、プロキシまたはネットワークのファイアウォールが Microsoft Azure への通信をブロックしていることが原因で発生する可能性があります。 コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

ログは、各ハイブリッド worker の C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes にローカルに格納されます。 Azure Automation へのロールのオンボードに影響する接続などの問題や、通常の動作の実行中に発生した問題を示唆する警告やエラー イベントが、**アプリケーションとサービス ログ\Microsoft-SMA\Operations** と**アプリケーションとサービス ログ\Operations Manager** イベント ログに書き込まれているかどうかをチェックできます。

## <a name="next-steps"></a>次の手順

「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。
