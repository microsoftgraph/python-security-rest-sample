---
page_type: sample
description: "このサンプルは、一般的な Microsoft Graph Security API の呼び出しを呼び出す Python Web アプリケーションから構成されています。"
products:
- ms-graph
languages:
- python
- html
extensions:
  contentType: samples
  technologies:
  - Microsoft Graph
  services:
  - Security 
  createdDate: 4/5/2018 3:22:33 PM
---
# Microsoft インテリジェント セキュリティ グラフを使用した Python Web アプリのデモ

![language:Python](https://img.shields.io/badge/Language-Python-blue.svg?style=flat-square) ![license:MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)

Microsoft Graph は、インテリジェント セキュリティ グラフのプロバイダーと統合するための REST API を提供します。これにより、アプリは警告の受信、警告のライフサイクル プロパティの更新、および警告メールによる通知を容易に実行できます。このサンプルは、一般的な Microsoft Graph Security API の呼び出しを呼び出す Python Web アプリケーションから構成され、[Requests](http://docs.python-requests.org/en/master/) HTTP ライブラリを使用してこれらの Microsoft Graph API を呼び出します。

| API | エンドポイント |
| | ------------------- | ------------------------------------------ | ---- |
| 警告の受信 | /security/alerts | [docs](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/alert) |
| ユーザー プロファイルの取得 | /me | [docs](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_get) |
| Webhook サブスクリプションの作成 | /subscriptions | [docs](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) |

このサンプルの詳細については、「[Python アプリで Microsoft Graph を使ってみる](https://developer.microsoft.com/en-us/graph/docs/concepts/python
)」を参照してください。

* [前提条件](#prerequisites)
* [インストール](#installation)
* [サンプルの実行](#running-the-sample)
* [Sendmail ヘルパー関数](#sendmail-helper-function)
* [投稿](#contributing)
* [リソース](#resources)

## 前提条件

サンプルをインストールする前に:

* [https://www.python.org/](https://www.python.org/) から Python をインストールします。Python 3.6 でコードをテストしましたが、Python 3.x バージョンはすべて正常に動作します。コードベースが Python 2.7 で動作している場合、[3to2](https://pypi.python.org/pypi/3to2) ツールを使用してコードを Python 2.7 に移植すると役に立つかもしれません。
* アプリケーションを Microsoft Graph にアクセスできるように登録するには、[Microsoft アカウント](https://www.outlook.com)、または[一般法人向け Office 365 アカウント](https://msdn.microsoft.com/en-us/office/office365/howto/setup-development-environment#bk_Office365Account)のいずれかが必要です。これらのアカウントをお持ちでない場合は、[outlook.com](https://www.outlook.com) で無料で Microsoft アカウントを作成できます。

## インストール

次の手順に従ってサンプルをインストールします。

1. 次のいずれかのコマンドを使用して、リポジトリの複製を作成します。
    * ```git clone https://github.com/microsoftgraph/python-security-rest-sample.git```

2. 仮想環境を作成してアクティブ化します (オプション)。Python の仮想環境を初めて使用する場合は、[Miniconda](https://conda.io/miniconda.html) は最適な場所です。
3. 複製のリポジトリのルート フォルダーに、```pip install -r requirements.txt``` コマンドを使用して、```requirements.txt``` ファイルにリストされているサンプルの依存関係をインストールします。

## 構成

サンプルを構成するには、Microsoft [アプリケーション登録ポータル](https://go.microsoft.com/fwlink/?linkid=2083908)に新しいアプリケーションを登録する必要があります。

新しいアプリケーションを登録するためのこれらの手順に従います。

1. 個人用アカウントか、職場または学校のアカウントのいずれかを使用して、[](https://go.microsoft.com/fwlink/?linkid=2083908)アプリケーション登録ポータルにサインインします。
2. [**新規登録**] を選択します。
3. アプリケーション名を入力し、リダイレクト URL として `http://localhost:5000/login/authorized` を入力します。
    > **注:**アプリケーションをマルチテナントにしたい場合は、[**サポートされているアカウントの種類**] セクションで、[`任意の組織のディレクトリ内のアカウント`] を選択します。
4. [**登録**] を選択します。
5. 次に、アプリの [概要] ページが表示されます。[**アプリケーション ID**] フィールドをコピーして保存します。構成プロセスを完了するために、後で必要になります。
6. [**証明書とシークレット**] の下で、[**新しいクライアント シークレット**] を選択し、簡単な説明を追加します。[**値**] 列に新しいシークレットが表示されます。このパスワードをコピーします。構成プロセスを完了するために、後で必要になります。
7. [**API のアクセス許可**] の下で、[**アクセス許可の追加**]、[**Microsoft Graph**] の順に選択します。
8. [**委任されたアクセス許可**] の下で、サンプルに必要なアクセス許可と範囲を追加します。このサンプルには、**User.Read**、**SecurityEvents.ReadWrite.All**、および**SecurityActions.ReadWrite.All** アクセス許可が必要です。
    >**注**: Graph のアクセス許可モデルの詳細については、「[Microsoft Graph のアクセス許可のリファレンス](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference)」を参照してください。

[Webhook](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) が NGROK トンネル経由でサンプルにアクセスできるようにするには、次の手順に従います。

> **注**: これは、localhost のサンプル通知リスナーをテストする場合に必要です。サブスクリプションを作成し、Microsoft Graph から通知を受信するには、パブリック HTTPS エンドポイントを公開する必要があります。テスト中は、ngrok を使用して Microsoft Graph からのメッセージをコンピューター上の localhost ポートにトンネリングすることを一時的に許可できます。

1. [ngrok](https://ngrok.com/download) をダウンロードします。
2. ngrok Web サイトの手順に従ってインストールします。
3. Windows を使用している場合、ngrok を実行します。「ngrok.exe http 5000」を実行して ngrok を開始し、localhost ポート 5000 へのトンネルを開きます。
4. 次に、ngrok url で `config.py` ファイルを更新します。

    ![ngrok の画像](static/images/ngrok.PNG)

サンプルを構成する最後の手順として、複製されたリポジトリのルート フォルダーにある ```config.py``` ファイルを変更し、指示に従って、クライアント ID とクライアント シークレット (アプリ登録ポータルでアプリケーション ID とパスワードと呼ばれます) を入力します。ngrok url を反映するように、```config.py``` ファイルの `notificationUrl` プロパティを更新します。次に、変更を保存します。これらの手順を完了し、アプリの[管理者の同意](#Get-Admin-consent-to-view-Security-data)を得た後は、以下の説明に従って ```sample.py``` サンプルを実行できます。

## 管理者の同意を得てセキュリティ データを表示する

1. 管理者に**アプリケーション ID** と前の手順で使用した**リダイレクト URI** を提供します。アプリケーションの同意を得るには、組織の管理者 (または、組織のリソースの同意を与えられている他のユーザー) が必要です。
2. 組織のテナント管理者としてブラウザー ウィンドウを開き、アドレス バーに次の URL を入力します。
https://login.microsoftonline.com/common/adminconsent?client_id=APPLICATION_ID&state=12345&redirect_uri=REDIRECT_URL ここで APPLICATION_ID はアプリケーション ID であり、REDIRECT_URL
は、アプリケーションをクリックしてプロパティを表示した後の App V2 登録ポータルのリダイレクトURL 値です。
3. ログイン後、テナント管理者には、次のようなダイアログが表示されます (アプリケーションが要求しているアクセス許可によって異なります)。

   ![範囲の同意ダイアログ](static/images/Scope.png)

4. テナント管理者がこのダイアログに同意すると、組織のすべてのユーザーがこのアプリケーションを使用することに同意したことになります。

> **注:**認証フローの詳細については、「[承認と Microsoft Graph Security API](https://developer.microsoft.com/en-us/graph/docs/concepts/security-authorization)」を参照してください。

## サンプルの実行

1. コマンド プロンプトで、```python sample.py``` を実行します。
2. ブラウザーで [http://localhost:5000](http://localhost:5000) に移動します。
3. [**Microsoft でサインイン**] を選択して、Microsoft *.onmicrosoft.com ID で認証します。

ドロップダウン メニューから値を選択して、フィルター処理された警告クエリを作成できるようにするフォーム:
-
既定では、各セキュリティ API プロバイダーから上位 5 件の警告が選択されます。ただし、各プロバイダーから 1、5、10、または 20 件の警告を受信するように選択できます。

選択が完了したら、[**警告の受信**] をクリックします。REST 呼び出しは、Microsoft Graph に送信され、受信したすべての警告が含まれるテーブルが下のフォームに表示されます。

![受信した警告](static/images/getAlerts.PNG)

次のセクションでは、「通知の管理」フォームが表示されます。ここで、特定の警告のライフサイクル プロパティを警告 ID ごとに更新できます。
警告を更新すると、元の警告のメタデータが更新された警告の上に表示されます。

![更新された警告](static/images/updateAlerts.PNG)

最後に、Webhook リソースと一致する警告が更新されると、アプリによって Microsoft Graph からサンプル アプリケーションに対して Webhook 通知が送信されます。Webhook 通知を表示するには、Webhook サブスクリプションを作成します。[通知] をクリックすると、別のページが開きます。このページには、アプリにプッシュされたときに、Webhook 通知が表示されます。

   >**注:**このサンプルがローカル マシンで実行されている場合、このセクションでは、適切に通知を作成して受信するには `ngrok` が必要です。

![Webhook セクション](static/images/webhook.PNG)

## 投稿

これらのサンプルはオープン ソースであり、[MIT ライセンス](https://github.com/microsoftgraph/python-security-rest-sample/blob/master/LICENSE)の下でリリースされています。問題 (このサンプルに関する機能のリクエストや質問など) や[プル要求](https://github.com/microsoftgraph/python-security-rest-sample/pulls)は歓迎します。Microsoft Graph で見たい別の Python サンプルがある場合は、そのフィードバックも歓迎しますので、[問題](https://github.com/microsoftgraph/python-security-rest-sample/issues)を記録してお知らせください。

このプロジェクトでは、[Microsoft オープン ソース倫理規定](https://opensource.microsoft.com/codeofconduct/) が採用されています。詳細については、「[倫理規定の FAQ](https://opensource.microsoft.com/codeofconduct/faq/)」を参照してください。また、その他の質問やコメントがあれば、[opencode@microsoft.com](mailto:opencode@microsoft.com) までお問い合わせください。

お客様からのフィードバックを重視しています。[スタック オーバーフロー](https://stackoverflow.com/questions/tagged/microsoft-graph-security)でご連絡ください。[Microsoft-Graph-Security] を使用して質問にタグを付けます。

## リソース

ドキュメント: 

* [Microsoft Graph を使用してセキュリティ API を統合する](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/security-api-overview)
* Microsoft Graph [リストの警告](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/alert_list) ドキュメント
* [Microsoft Graph のアクセス許可のリファレンス](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference)

サンプル:

* [Microsoft Graph 用の Python 認証サンプル](https://github.com/microsoftgraph/python-sample-auth)
* [Microsoft Graph を使用して Python からメールを送信する](https://github.com/microsoftgraph/python-sample-send-mail)
* [Python で改ページされた Microsoft Graph 応答を処理する](https://github.com/microsoftgraph/python-sample-pagination)
* [Python で Graph オープン拡張機能を処理する](https://github.com/microsoftgraph/python-sample-open-extensions)

パッケージ:

* [Flask-OAuthlib](https://flask-oauthlib.readthedocs.io/en/latest/)
* [Requests:HTTP for Humans](http://docs.python-requests.org/en/master/)

Copyright (c) 2018 Microsoft Corporation.All rights reserved.
