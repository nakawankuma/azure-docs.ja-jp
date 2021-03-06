---
title: Microsoft Azure Multi-Factor Authentication におけるユーザーの状態
description: Azure Multi-Factor Authentication でユーザーの状態を確認してください。
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 02505a9b535279acd7a39edd7317c1a7812db011
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>ユーザーまたはグループに 2 段階認証を要求する方法

2 段階認証を要求する 2 つの方法のいずれかを実行します。 1 つ目は、個々のユーザーに Azure Multi-Factor Authentication (MFA) を有効にする方法です。 ユーザーを個別に有効にした場合は、サインインするたびに 2 段階認証が実行されます (信頼済み IP アドレスからサインインするときや、_記憶されたデバイス_の機能が有効なときなど、一部例外があります)。 2 つ目は、特定の条件下で 2 段階認証を要求する条件付きアクセス ポリシーを設定する方法です。

>[!TIP] 
>2 段階認証を要求するには、これらの方法のうちの 1 つを選択してください。両方は選択できません。 ユーザーを Azure Multi-Factor Authentication に対して有効にすると、すべての条件付きアクセス ポリシーが無効になります。

## <a name="which-option-is-right-for-you"></a>どちらのオプションが適しているか

**ユーザーの状態を変更することで Azure Multi-Factor Authentication を有効にする方法**は、2 段階認証を要求するための従来のアプローチです。 これは、クラウド内の Azure MFA と Azure MFA Server の両方に対して機能します。 有効にしたすべてのユーザーがサインインするたびに 2 段階認証が実行されます。 ユーザーを有効にすると、そのユーザーに影響を与える可能性があるすべての条件付きアクセス ポリシーが無効になります。 

**条件付きアクセス ポリシーを使って Azure Multi-Factor Authentication を有効にする方法**の方が、より柔軟に 2 段階認証を要求できます。 これは、クラウド内の Azure MFA に対してのみ機能します。ただし、_条件付きアクセス_は、[Azure Active Directory の有料機能](https://www.microsoft.com/cloud-platform/azure-active-directory-features)です。 個々のユーザーだけでなくグループに適用する条件付きアクセス ポリシーを作成することができます。 危険度の高いグループには、危険度の低いグループよりも多くの制限を割り当てることや、危険度の高いクラウド アプリにのみ 2 段階認証を要求して、危険度の低いクラウド アプリではスキップすることができます。 

どちらのオプションでも、ユーザーは、要求を有効にしてから初めてサインインしたときに Azure Multi-Factor Authentication の登録を求められます。 また、どちらのオプションも、構成可能な [Azure Multi-Factor Authentication の設定](howto-mfa-mfasettings.md)と連携します。

## <a name="enable-azure-mfa-by-changing-user-status"></a>ユーザーの状態を変更することで Azure MFA を有効する

Azure Multi-factor Authentication のユーザー アカウントには、次の 3 つの異なる状態があります。

| 状態 | [説明] | 非ブラウザー アプリに影響があるか | ブラウザー アプリに影響があるか | 影響を受ける先進認証 |
|:---:|:---:|:---:|:--:|:--:|
| 無効 |新しいユーザーの既定の状態は、Azure MFA に登録されていません。 |いいえ  |いいえ  |いいえ  |
| 有効 |ユーザーは Azure MFA にサインインできますが、登録されていません。 次回のサインイン時に登録することを求められます。 |いいえ。  これらは登録プロセスが完了するまで機能し続けます。 | はい。 セッションの有効期限が切れると、Azure MFA の登録が必要になります。| はい。 アクセス トークンの有効期限が切れると、Azure MFA の登録が必要になります。 |
| 適用 |ユーザーは、Azure MFA にサインインして Azure MFA に対する登録プロセスを完了しています。 |はい。  アプリはアプリ パスワードを必要とします。 |はい。 ログイン時に Azure MFA が必要です。 | はい。 ログイン時に Azure MFA が必要です。 |

ユーザーの状態は、管理者がユーザーをAzure MFA に登録し、ユーザーが登録プロセスを完了したかどうかを反映します。

すべてのユーザーの状態は、*[無効]* から始まります。 管理者がユーザーを Azure MFA に登録すると、ユーザーの状態は *[有効]* に変わります。 [有効] 状態のときにユーザーがサインインして登録プロセスを完了すると、ユーザーの状態は *[適用]* に変わります。  

### <a name="view-the-status-for-a-user"></a>ユーザーの状態を表示する

ユーザーの状態を表示および管理できるページにアクセスするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのユーザー]** に移動します。
3. **[Multi-Factor Authentication]** を選択します。
   ![Select Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. 新しいページが開き、ユーザーの状態が表示されます。
   ![多要素認証のユーザーの状態 - スクリーンショット](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>ユーザーの状態を変更する

1. 前述の手順を使用して、Azure Multi-Factor Authentication の**ユーザー** ページを取得します。
2. Azure MFA で有効にするユーザーを見つけます。 上部でビューを変更することが必要になる場合があります。 
   ![ユーザーを見つける - スクリーンショット](./media/howto-mfa-userstates/enable1.png)
3. ユーザーの名前の横にあるチェック ボックスをオンにします。
4. 右側にある **[クイック操作]** で **[有効にする]** または **[無効にする]** を選択します。
   ![選択したユーザーを有効にする - スクリーンショット](./media/howto-mfa-userstates/user1.png)

   >[!TIP]
   >"*有効*" なユーザーは、Azure MFA に登録すると自動的に "*適用*" に切り替えられます。 ユーザーの状態を手動で "*適用*" に変更してしないでください。 

5. 開いたポップアップ ウィンドウで選択内容を確認します。 

ユーザーを有効にした後は、ユーザーにメールで通知します。 次回サインインしたときに登録を求められることをユーザーに伝えます。 また、最新の認証をサポートしていない非ブラウザー アプリを組織で使用している場合は、アプリ パスワードを作成する必要があります。 ユーザーが参照できるように、[Azure MFA エンドユーザー ガイド](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md)に関するページのリンクを含めることもできます。

### <a name="use-powershell"></a>PowerShell の使用
[Azure AD PowerShell](/powershell/azure/overview) を使用してユーザーの状態を変更するには、`$st.State` を変更します。 状態は 3 つあります。

* 有効
* 適用
* 無効  

ユーザーを直接 "*適用*" の状態に移さないでください。 "適用" 状態に移行しても、ユーザーが MFA の登録を終えておらず、[アプリのパスワード](howto-mfa-mfasettings.md#app-passwords)を取得していないため、ブラウザーベースでないアプリが動作を停止します。 

一括でユーザーを有効にする必要がある場合は、PowerShell の使用をお勧めします。 ユーザーの一覧をループ処理して各ユーザーを有効にする PowerShell スクリプトを作成します。

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

次のスクリプトは一例です。

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>条件付きアクセス ポリシーを使用して Azure MFA を有効にする

_条件付きアクセス_は、構成オプションを多数備えた Azure Active Directory の有料機能です。 次の手順で、ポリシーを作成する方法を 1 つ説明します。 詳細については、「[Azure Active Directory の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)」を参照してください。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]** > **[条件付きアクセス]** に移動します。
3. **[新しいポリシー]** を選択します。
4. **[割り当て]** で、**[ユーザーとグループ]** を選択します。 **[含める]** タブと **[除外]** タブを使って、ポリシーによって管理するユーザーとグループを指定します。
5. **[割り当て]** で、**[クラウド アプリ]** を選択します。 **[すべてのクラウド アプリ]** を含めることを選択します。
6. **[アクセス制御]** で、**[許可]** を選択します。 **[多要素認証が必要です]** を選択します。
7. **[ポリシーを有効にする]** を **[オン]** にして、**[保存]** を選択します。

条件付きアクセス ポリシーのその他のオプションを使用すると、2 段階認証を要求する必要のあるタイミングを厳密に指定できます。 たとえば、請負業者が、ドメインに参加していないデバイスで、信頼されていないネットワークから調達アプリにアクセスしようとしたときに、2 段階認証を要求することを指定したポリシーを作成できます。 

## <a name="next-steps"></a>次の手順

- [条件付きアクセスのベスト プラクティス](../active-directory-conditional-access-best-practices.md)についてヒントを得ます。

- [ユーザーとデバイス](howto-mfa-userdevicesettings.md)の Azure Multi-Factor Authentication 設定を管理します。
