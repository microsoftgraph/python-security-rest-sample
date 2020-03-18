---
page_type: sample
description: "Esta amostra consiste em um aplicativo da Web Python que invoca chamadas comuns da API de segurança do Microsoft Graph."
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
# Demonstração do aplicativo Web Python usando o gráfico de segurança inteligente da Microsoft

![language:Python](https://img.shields.io/badge/Language-Python-blue.svg?style=flat-square) ![license:MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)

O Microsoft Graph fornece APIs REST para integração com provedores de gráficos de segurança inteligente que permitem que seu aplicativo recupere alertas, atualize as propriedades do ciclo de vida de alerta e envie facilmente uma notificação de alerta por e-mail. Este exemplo é formado por um aplicativo Web Python que invoca chamadas comuns da API de segurança do Microsoft Graph, usando a biblioteca HTTP [Solicitações](http://docs.python-requests.org/en/master/) para chamar estas APIs do Microsoft Graph:

| API | Ponto de extremidade |
| | ------------------- | ------------------------------------------ | ---- |
| Receber alertas | /Security/Alerts |[documentos](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/alert) |
| Obter perfil de usuário | /me |[documentos](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_get) |
| Criar uma assinatura do webhook | /subscriptions |[documentos](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) |

Para obter mais informações sobre esse exemplo, confira [comece a usar o Microsoft Graph em um aplicativo Python](https://developer.microsoft.com/en-us/graph/docs/concepts/python
).

* [Pré-requisitos](#prerequisites)
* [Instalação](#installation)
* [Execução da amostra](#running-the-sample)
* [Função auxiliar Sendmail](#sendmail-helper-function)
* [Colaboração](#contributing)
* [Recursos](#resources)

## Pré-requisitos

Antes de instalar o exemplo:

* Instale Python a partir de [https://www.python.org/](https://www.python.org/). Testamos o código com Python 3.6, mas qualquer versão do Python 3\. x deve funcionar. Se a base de código estiver em execução no Python 2.7, talvez seja útil usar as ferramentas [3to2](https://pypi.python.org/pypi/3to2) para portar o código para o Python 2.7.
* Para registrar seu aplicativo do Access para o Microsoft Graph, você precisará de uma conta [da Microsoft](https://www.outlook.com) ou de uma [conta do Office 365 para empresas](https://msdn.microsoft.com/en-us/office/office365/howto/setup-development-environment#bk_Office365Account). Caso não tenha uma delas, você poderá criar uma conta da Microsoft gratuitamente em [outlook.com](https://www.outlook.com).

## Instalação

Siga estas etapas para instalar os exemplos:

1. Clone o repositório, usando um destes comandos:
    * ```git clone https://github.com/microsoftgraph/python-security-rest-sample.git```

2. Crie e ative um ambiente virtual (opcional). Se você não estiver familiarizado com os ambientes virtuais Python, [Miniconda](https://conda.io/miniconda.html) é um ótimo lugar para começar.
3. Na pasta raiz do seu repositório clonado, instale as dependências da amostra como listadas no arquivo ```requirements.txt``` com este comando: ```pip install -r requirements.txt```.

## Configuração

Para configurar os exemplos, você precisará registrar um novo aplicativo no [Portal de Registro de Aplicativo Microsoft](https://go.microsoft.com/fwlink/?linkid=2083908).

Siga as etapas a seguir para registrar um novo aplicativo:

1. Entre no [Portal de Registro de Aplicativos](https://go.microsoft.com/fwlink/?linkid=2083908) usando sua conta pessoal ou uma conta corporativa ou de estudante.
2. Escolha **Novo registro**.
3. Digite o nome do aplicativo, digite `http://localhost:5000/login/authorized` como a URL de redirecionamento.
    > **Observação:** Se você quiser que seu aplicativo tenha vários locatários, selecione `Contas em qualquer diretório organizacional` na seção **Tipos de conta com suporte**.
4. Marque **Registrar**.
5. Em seguida, você verá a página de visão geral do seu aplicativo. Copie e salve o **campo identificação do aplicativo**. Ele será necessário mais tarde para concluir o processo de configuração.
6. Em **Certificados & segredos**, escolha **Novo segredo do cliente** e adicione uma descrição rápida. Será exibido um novo segredo na coluna **Valor**. Copie essa senha. Ela será necessária mais tarde para concluir o processo de configuração.
7. Em **permissões de API**, escolha **Adicionar uma permissão** > **Microsoft Graph**.
8. Em **Permissões Delegadas**, adicione as permissões/escopos necessários à amostra. Este exemplo exige as permissões **User.Read**, **SecurityEvents.ReadWrite.All**, e **SecurityActions.ReadWrite.All**.
    >**Observação**: Confira as [Referências de permissões do Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) para saber mais sobre o modelo de permissão do gráfico.

Siga estas etapas para permitir que o [webhook](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) acesse o exemplo por meio de um túnel NGROK:

> **Observação**: Isso é necessário se você deseja testar o Ouvinte de Notificação de exemplo no localhost. Você deve expor um ponto de extremidade HTTPS público para criar uma assinatura e receber as notificações do Microsoft Graph. Ao testar o, você pode usar o ngrok para permitir temporariamente que as mensagens do Microsoft Graph sejam encapsuladas para uma porta localhost em seu computador.

1. Baixar [ngrok](https://ngrok.com/download).
2. Siga as instruções de instalação no site ngrok.
3. Execute o ngrok, se você estiver usando o Windows. Execute o "ngrok. exe http 5000" para iniciar a ngrok e abrir um túnel para a sua porta localhost 5000.
4. Em seguida, atualize o arquivo `config.py` com a URL do ngrok.

    ![ngrok image](static/images/ngrok.PNG)

Como etapa final na configuração do exemplo, modifique o arquivo ```config.py``` na pasta raiz do seu repositório clonado e siga as instruções para inserir a ID do cliente e o segredo do cliente (que são referidos como ID do aplicativo e senha no portal de registro de aplicativo). Atualize a propriedade `notificationUrl` no arquivo ```config.py``` para refletir a URL do ngrok. Em seguida, salve a alteração. Depois de concluir essas etapas e ter recebido [consentimento de administrador](#Get-Admin-consent-to-view-Security-data) para o seu aplicativo, você poderá executar o exemplo```sample.py``` conforme descrito abaixo.

## Obtenha consentimento do administrador para exibir dados de segurança

1. Forneça ao seu administrador sua **ID do aplicativo** e o **URI de redirecionamento** que você usou nas etapas anteriores. É necessário que o administrador da organização (ou outro usuário autorizado a conceder consentimento para recursos organizacionais) conceda um consentimento para o aplicativo.
2. Como o administrador de locatários para sua organização, abra uma janela do navegador e crie a seguinte URL na barra de endereços:
https://login.microsoftonline.com/common/adminconsent?client_id=APPLICATION_ID&state=12345&redirect_uri=REDIRECT_URL
em que APPLICATION_ID é a ID do aplicativo e REDIRECT_URL os valores da URL de redirecionamento do portal de registro do aplicativo v2 depois de clicar em seu aplicativo para exibir suas propriedades.
3. Após a fazer o logon, o administrador do locatário receberá uma caixa de diálogo como a seguinte (dependendo de quais permissões o aplicativo estiver solicitando):

   ![Caixa de diálogo consentimento de escopo](static/images/Scope.png)

4. Quando o administrador de locatários concordar com essa caixa de diálogo, ele estará concedendo consentimento para que todos os usuários da organização usem esse aplicativo.

> **Observação:** Para obter mais detalhes sobre o fluxo de autorização, leia [Autorização e API de segurança do Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/security-authorization)

## Execução da amostra

1. No prompt de comando: ```python sample.py```
2. No navegador, navegue até [http://localhost:5000](http://localhost:5000)
3. Escolha **entrar com a Microsoft** e autentique com uma identidade *. onmicrosoft.com da Microsoft.

Um formulário que permite a criação de uma consulta de alerta filtrada, selecionando valores de menus suspensos:
-
Por padrão, os cinco principais alertas de cada provedor de API de segurança serão selecionados. No entanto, você pode optar por recuperar 1, 5, 10 ou 20 alertas de cada provedor.

Depois de selecionar as opções, clique em **Receber alertas**. Uma chamada REST será enviada para o Microsoft Graph e uma tabela com todos os alertas recebidos será exibida abaixo do formulário:

![Alertas recebidos](static/images/getAlerts.PNG)

Na próxima seção, você verá o formulário "Gerenciar alertas", onde você poderá atualizar as propriedades do ciclo de vida para um alerta específico por ID de alerta.
Depois que o alerta for atualizado, os metadados do alerta original serão exibidos acima do alerta atualizado.

![Alertas atualizados](static/images/updateAlerts.PNG)

Por fim, o aplicativo permite que notificações do webhook sejam enviadas do Microsoft Graph para seu aplicativo de amostra quando um alerta correspondente ao recurso do webhook for atualizado. Para exibir as notificações do webhook, crie uma assinatura do webhook. Em seguida, clique em "notificar" para abrir outra página que exibirá as notificações do webhook enquanto elas forem empurradas para o aplicativo.

   >**Observação:** Se você estiver executando o exemplo em sua máquina local, esta seção exige `ngrok` para criar e receber notificações corretamente.

![seção webhook](static/images/webhook.PNG)

## Colaboração

Esses exemplos são de open-source, liberados sob a [Licença MIT](https://github.com/microsoftgraph/python-security-rest-sample/blob/master/LICENSE). Dúvidas (incluindo solicitações de recursos e/ou perguntas sobre esse exemplo) e [solicitações pull](https://github.com/microsoftgraph/python-security-rest-sample/pulls) são bem-vindas. Também estamos interessados se houver outra amostra de Python para o Microsoft Graph que você gostaria de ver, conte mais a respeito nos comentários. Registre um [problema](https://github.com/microsoftgraph/python-security-rest-sample/issues) e fale conosco!

Este projeto adotou o [Código de Conduta de Código Aberto da Microsoft](https://opensource.microsoft.com/codeofconduct/).  Para saber mais, confira [Perguntas frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate [opencode@microsoft.com](mailto:opencode@microsoft.com) se tiver outras dúvidas ou comentários.

Seus comentários são importantes para nós. Junte-se a nós na página do [Stack Overflow](https://stackoverflow.com/questions/tagged/microsoft-graph-security). Marque suas perguntas com [Microsoft-Graph-Security].

## Recursos

Documentação:

* [Usar Microsoft Graph para se integrar à API de segurança](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/security-api-overview)
* [Alertas de lista](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/alert_list) do Microsoft Graph 
* [Referência de permissões do Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference)

Exemplos:

* [Amostras de autenticação do Python para Microsoft Graph](https://github.com/microsoftgraph/python-sample-auth)
* [Enviar e-mails do Python por meio do Microsoft Graph](https://github.com/microsoftgraph/python-sample-send-mail)
* [Trabalhar com respostas paginadas do Microsoft Graph no Python](https://github.com/microsoftgraph/python-sample-pagination)
* [Trabalhar com extensões abertas do Graph no Python](https://github.com/microsoftgraph/python-sample-open-extensions)

Pacotes:

* [Flask-OAuthlib](https://flask-oauthlib.readthedocs.io/en/latest/)
* [Solicitações: HTTP for Humans](http://docs.python-requests.org/en/master/)

Copyright (c) 2018 Microsoft Corporation. Todos os direitos reservados.
