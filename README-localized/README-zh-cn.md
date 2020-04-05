---
page_type: sample
description: "此示例由调用常见 Microsoft Graph security API 调用的 Python Web 应用组成。"
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
# 使用 Microsoft Intelligent Security Graph 演示 Python Web 应用程序

![语言：Python](https://img.shields.io/badge/Language-Python-blue.svg?style=flat-square) ![许可证：MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)

Microsoft Graph 提供与 Intelligent Security Graph 集成的 REST API，能够使应用程序检索警报、更新警报生命周期属性，并轻松发送警报通知电子邮件。此示例由调用常见 Microsoft Graph security API 调用的 Python Web 应用组成，其使用“[请求](http://docs.python-requests.org/en/master/)”HTTP 库来调用 Microsoft Graph API：

| API | 终结点 |
| | ------------------- | ------------------------------------------ | ---- |
| 获取警报 | /security/alerts | [docs](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/alert) |
| 获取用户配置文件 | /me | [docs](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_get) |
| 创建 webhook 订阅 | /subscriptions | [docs](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) |

有关此示例的更多信息，参见“[在 Python 应用中开始使用 Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/python
)”。

* [先决条件](#prerequisites)
* [安装](#installation)
* [运行示例](#running-the-sample)
* [Sendmail 帮助程序功能](#sendmail-helper-function)
* [参与](#contributing)
* [资源](#resources)

## 先决条件

安装示例前：

* 从 [https://www.python.org/](https://www.python.org/) 安装 Python。我们使用 Python 3.6 测试了代码，但任一 Python 3.x 版本也能正常运行。如果代码库在 Python 2.7 下运行，使用 [3to2](https://pypi.python.org/pypi/3to2) 工具有助于将代码移植至 Python 2.7。
* 如果要注册应用程序来访问 Microsoft Graph，需要一个[Microsoft 账户](https://www.outlook.com)或一个 [Office 365 商业版账户](https://msdn.microsoft.com/en-us/office/office365/howto/setup-development-environment#bk_Office365Account)。如果没有，可在 [outlook.com](https://www.outlook.com) 上免费注册 Microsoft 账户。

## 安装

按照以下步骤安装示例：

1. 使用以下任一命令复制库：
    * ```git clone https://github.com/microsoftgraph/python-security-rest-sample.git```

2. 创建并激活虚拟环境（可选）。如果第一次使用 Python 虚拟环境，[Miniconda](https://conda.io/miniconda.html) 是一个良好的起点。
3. 在已复制存储库的根文件夹中，使用此命令： ```pip install -r requirements.txt``` 为列在 ```requirements.txt``` 文件中的示例安装依赖项。

## 配置

若要配置示例，需要在 Microsoft [应用程序注册门户](https://go.microsoft.com/fwlink/?linkid=2083908)中注册新的应用程序。

请按照以下步骤注册新应用程序：

1. 使用个人帐户或者工作或学校帐户登录到“[应用程序注册门户](https://go.microsoft.com/fwlink/?linkid=2083908)”。
2. 选择“**新注册**”。
3. 输入应用程序名称，输入 `http://localhost:5000/login/authorized` 作为重定向 URL。
    > **注意：**如果希望你的应用程序是多租户型的，请在“**支持的帐户类型**”部分中选择“`任何组织目录中的帐户`”。
4. 选择“**注册**”。
5. 接下来，你将看到应用的概述页面。复制并保存“**应用程序 ID**”字段。稍后将需要用它来完成配置过程。
6. 在“**证书和密码**”下，选择“**新建客户端密码**”，然后添加简短说明。新的密码将显示在“**值**”列中。复制此密码。稍后将需要用它来完成配置过程。
7. 在“**API 权限**”下，选择“**添加权限**”>“**Microsoft Graph**”。
8. 在“**委派权限**”下，添加示例所需的权限/作用域。此示例需要 **User.Read**、**SecurityEvents.ReadWrite.All** 和 **SecurityActions.ReadWrite.All** 权限。
    >**注意**：有关 Graph 的权限模型的详细信息，请参阅 [Microsoft Graph 权限引用](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference)。

按照下列步骤，以允许 [webhooks](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) 通过 NGROK 隧道访问示例：

> **注意**：如果想要在 localhost 上测试示例通知侦听器，需要此操作。你必须公开一个公共 HTTPS 终结点才能创建订阅和接收来自 Microsoft Graph 的通知。测试时，你可以使用 ngrok 临时允许消息从 Microsoft Graph 经隧道传输至计算机上的 localhost 端口。

1. 下载 [ngrok](https://ngrok.com/download)。
2. 按照 ngrok 网站上的安装说明进行操作。
3. 如果你使用的是 Windows，请运行 ngrok。运行“ngrok.exe http 5000”来启动 ngrok，并打开一个到 localhost 端口 5000 的隧道。
4. 然后使用 ngrok url 更新 `config.py` 文件。

    ![ngrok 图像](static/images/ngrok.PNG)

作为配置示例的最后一部，更改已复制存储库根文件夹中的 ```config.py``` 文件，并按照说明输入客户端 ID 和客户端密码（涉及应用程序注册门户中的应用 ID 和密码）。在 ```config.py``` 文件中更新 `notificationUrl` 属性，以反映 ngrok url。然后保存更改。完成这些步骤后且获得针对应用程序的“[管理员同意](#Get-Admin-consent-to-view-Security-data)”时，能够运行 ```sample.py``` 示例，具体如下所述。

## 获取查看安全性数据的管理员许可

1. 向管理员提供在前面的步骤中使用的“**应用程序 ID**”和“**重定向 URI**”。组织管理员（或有权为组织资源授予许可的其他用户）需要为应用授予许可。
2. 作为组织的租户管理员，打开浏览器窗口并在地址栏中输入下列 URL：
https://login.microsoftonline.com/common/adminconsent?client_id=APPLICATION_ID&state=12345&redirect_uri=REDIRECT_URL 其中单击应用程序查看属性后，
APPLICATION_ID 是 App V2 注册门户中的应用 ID、REDIRECT_URL 是重定向 URL 值。 
3. 登录后，租户管理员将会看到如下所示的对话框（具体取决于应用程序所请求的权限）：

   ![作用域许可对话框](static/images/Scope.png)

4. 租户管理员同意此对话框时，他/她将为组织中使用此应用的所有用户授予许可。

> **注意：**有关授权流的更多详细信息，请参阅[授权和 Microsoft Graph 安全性 API](https://developer.microsoft.com/en-us/graph/docs/concepts/security-authorization)

## 运行示例

1. 在命令提示符上输入：```python sample.py```
2. 在浏览器中导航到 [http://localhost:5000](http://localhost:5000)。
3. 选择 “**使用 Microsoft 凭据登录**”并使用 Microsoft *.onmicrosoft.com 身份验证身份。

允许通过从下拉菜单中选择值来构建筛选警报查询的窗体：
-
默认选定各安全 API 提供商的前 5 条警报。但是可选择从各提供商检索 1、5、10 或 20 条警报。

选定选项后，单击“**获取警报**”。REST 调用将被发送至 Microsoft Graph，含有所有已收到警报的表格在窗体下方显示：

![警报已收到](static/images/getAlerts.PNG)

在下一部分，将看到“管理警报”窗体，在这里可针对特定的警报，按照警报 ID 更新生命周期属性。
警报更新后，原始警报的元数据在已更新警报上方显示。

![警报已更新](static/images/updateAlerts.PNG)

最后，当更新与 webhook 资源匹配的警报时，允许 webhook 通知从 Microsoft Graph 发送至示例应用程序。如果要查看 webhook 通知，创建 webhook 订阅。然后单击“通知”以打开另一个页面，通知推送至应用时，此页面将显示显示 Webhook 通知。

   >**注意：**如果你是在本地计算机上运行此示例，则此部分需要 `ngrok` 来正确创建和接收通知。

![webhook 部分](static/images/webhook.PNG)

## 参与

这些示例是在 [MIT 许可证](https://github.com/microsoftgraph/python-security-rest-sample/blob/master/LICENSE)下发布的开放源代码。欢迎提出相关问题（包括有关此示例的功能请求和/或疑问）和[拉取请求](https://github.com/microsoftgraph/python-security-rest-sample/pulls)。如果你想查看 Microsoft Graph 的其他 Python 示例，我们也对相应反馈感兴趣 — 请记录[问题](https://github.com/microsoftgraph/python-security-rest-sample/issues)并通知我们！

此项目已采用 [Microsoft 开放源代码行为准则](https://opensource.microsoft.com/codeofconduct/)。有关详细信息，请参阅[行为准则 FAQ](https://opensource.microsoft.com/codeofconduct/faq/)。如有其他任何问题或意见，也可联系 [opencode@microsoft.com](mailto:opencode@microsoft.com)。

我们非常重视你的反馈意见。请在[堆栈溢出](https://stackoverflow.com/questions/tagged/microsoft-graph-security)上与我们联系。使用 [Microsoft-Graph-Security] 标记安全。

## 资源

文档：

* [使用 Microsoft Graph 与 Security API 集成](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/security-api-overview)
* Microsoft Graph [列出警报](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/alert_list)文档
* [Microsoft Graph 权限引用](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference)

示例：

* [Microsoft Graph 的 Python 身份验证示例](https://github.com/microsoftgraph/python-sample-auth)
* [通过 Microsoft Graph 从 Python 发送邮件](https://github.com/microsoftgraph/python-sample-send-mail)
* [在 Python 中处理分页 Microsoft Graph 响应](https://github.com/microsoftgraph/python-sample-pagination)
* [在 Python 中使用 Graph 开放扩展](https://github.com/microsoftgraph/python-sample-open-extensions)

包：

* [Flask-OAuthlib](https://flask-oauthlib.readthedocs.io/en/latest/)
* [请求：HTTP for Humans](http://docs.python-requests.org/en/master/)

版权所有 (c) 2018 Microsoft Corporation。保留所有权利。
