---
page_type: sample
description: "Этот пример состоит из веб-приложения Python, выполняющего стандартные вызовы Microsoft Graph Security API"
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
# Демонстрация веб-приложения Python с использованием Microsoft Intelligent Security Graph

![язык:Python](https://img.shields.io/badge/Language-Python-blue.svg?style=flat-square) ![лицензия:MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)

Microsoft Graph предоставляет API REST для интеграции с поставщиками Intelligent Security Graph, позволяющими приложению получать оповещения, обновлять свойства жизненного цикла оповещений и легко отправлять по электронной почте уведомления об оповещениях. Этот пример состоит из веб-приложения Python, выполняющего стандартные вызовы Microsoft Graph Security API, с помощью HTTP-библиотеки [Requests](http://docs.python-requests.org/en/master/) для вызова этих API Microsoft Graph:

| API | Конечная точка |
| | ------------------- | ------------------------------------------ | ---- |
| Получение оповещений | /security/alerts | [документы](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/alert) |
| Получение профиля пользователя | /me | [документы](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_get) |
| Создание подписки веб-перехватчика | /subscriptions | [документы](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) |

Дополнительные сведения об этом примере см. в статье [Начало работы с Microsoft Graph в приложении Python](https://developer.microsoft.com/en-us/graph/docs/concepts/python
).

* [Предварительные требования](#prerequisites)
* [Установка](#installation)
* [Запуск примера](#running-the-sample)
* [Вспомогательная функция отправки по электронной почте](#sendmail-helper-function)
* [Участие](#contributing)
* [Ресурсы](#resources)

## Предварительные требования

Перед установкой примера:

* Установите Python с [https://www.python.org/](https://www.python.org/). Мы тестировали код с использованием Python 3.6, но подойдет любая версия Python 3.x. Если база кода работает с Python 2.7, используйте средства [3to2](https://pypi.python.org/pypi/3to2), чтобы перенести код в Python 2.7.
* Чтобы зарегистрировать приложение для доступа к Microsoft Graph, потребуется [учетная запись Майкрософт](https://www.outlook.com) или [учетная запись Office 365 для бизнеса](https://msdn.microsoft.com/en-us/office/office365/howto/setup-development-environment#bk_Office365Account). Если у вас нет таких учетных записей, можно бесплатно создать учетную запись Майкрософт на сайте [outlook.com](https://www.outlook.com).

## Установка

Выполните следующие действия для установки примеров:

1. Скопируйте репозиторий, используя одну из следующих команд:
    * ```git clone https://github.com/microsoftgraph/python-security-rest-sample.git```

2. Создайте и активируйте виртуальную среду (необязательно). Если вы не знакомы с виртуальными средами Python, [Miniconda](https://conda.io/miniconda.html) — отличный вариант для начала.
3. В корневой папке клонированного репозитория установите зависимости для примера, указанные в файле ```requirements.txt```, с помощью команды: ```pip install -r requirements.txt```.

## Настройка

Чтобы настроить пример, необходимо зарегистрировать новое приложение на [портале регистрации приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Майкрософт.

Чтобы зарегистрировать новое приложение, выполните следующие действия:

1. Войдите на [портал регистрации приложений](https://go.microsoft.com/fwlink/?linkid=2083908) с помощью личной, рабочей или учебной учетной записи.
2. Выберите **Новая регистрация**.
3. Введите имя приложения. В качестве URL-адреса перенаправления введите `http://localhost:5000/login/authorized`.
    > **Примечание.** Если вы хотите, чтобы приложение было мультитенантным, в разделе **Поддерживаемые типы учетных записей** выберите `Учетные записи в любом каталоге организации`.
4. Нажмите **Зарегистрировать**.
5. Затем вы увидите страницу обзора вашего приложения. Скопируйте и сохраните поле **Идентификатор приложения**. Он потребуется позже для завершения настройки.
6. В разделе **Сертификаты и секреты** выберите **Новый секрет клиента** и добавьте краткое описание. Новый секрет отображается в столбце **Значение**. Скопируйте этот пароль. Он потребуется позже для завершения настройки.
7. В разделе **Разрешения API** выберите **Добавить разрешение** > **Microsoft Graph**.
8. В разделе **Делегированные разрешения** добавьте нужные разрешения и области для примера. Этому примеру требуются разрешения **User.Read**, **SecurityEvents.ReadWrite.All** и **SecurityActions.ReadWrite.All**.
    >**Примечание**. Дополнительные сведения о модели разрешений Graph см. в [справочнике по разрешениям Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference).

Выполните следующие действия, чтобы разрешить [веб-перехватчикам](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) доступ к примеру с помощью туннеля NGROK:

> **Примечание**. Это требуется, если вы хотите протестировать пример прослушивателя уведомлений на localhost. Чтобы создать подписку и получать уведомления от Microsoft Graph, нужно предоставить открытую конечную точку HTTPS. Во время тестирования вы можете использовать ngrok для временного разрешения туннелирования сообщений из Microsoft Graph на порт localhost на вашем компьютере.

1. Скачайте [ngrok](https://ngrok.com/download).
2. Выполните инструкции по установке на веб-сайте ngrok.
3. Запустите ngrok, если вы используете Windows. Выполните команду "ngrok.exe http 5000", чтобы запустить ngrok и открыть туннель к порту 5000 localhost.
4. Затем обновите файл `config.py`, используя URL-адрес ngrok.

    ![изображение ngrok](static/images/ngrok.PNG)

На заключительном этапе настройки примера измените файл ```config.py``` в корневой папке скопированного репозитория и выполните инструкции по вводу идентификатора клиента и секрета клиента (называемые идентификатором приложения и паролем на портале регистрации приложений). Обновите свойство `notificationUrl` в файле ```config.py``` в соответствии с URL-адресом ngrok. Затем сохраните изменения. После выполнения этих действий и получения [согласия администратора](#Get-Admin-consent-to-view-Security-data) для вашего приложения, вы сможете запустить пример ```sample.py```, как описано ниже.

## Получение согласия администратора на просмотр данных безопасности

1. Предоставьте администратору **идентификатор приложения** и **URI-адрес перенаправления**, примененные на предыдущих шагах. Администратор организации (или другой пользователь, уполномоченный предоставлять согласие для ресурсов организации) должен предоставить согласие для приложения.
2. В качестве администратора клиента в своей организации откройте окно браузера и введите следующий URL-адрес в адресной строке:
https://login.microsoftonline.com/common/adminconsent?client_id=APPLICATION_ID&state=12345&redirect_uri=REDIRECT_URL. Где
APPLICATION_ID — идентификатор приложения, а REDIRECT_URL — URL-адрес перенаправления из портала регистрации приложений версии 2, отображаемые при щелчке по приложению для просмотра его свойств.
3. После входа администратор клиента увидит диалоговое окно, аналогичное следующему (в зависимости от разрешений, требуемых приложением):

   ![Диалоговое окно подтверждения области](static/images/Scope.png)

4. Если администратор клиента предоставляет согласие в этом диалоговом окне, он разрешает всем пользователям организации использовать это приложение.

> **Примечание.** Дополнительные сведения о потоке авторизации см. в статье [Авторизация и Microsoft Graph Security API](https://developer.microsoft.com/en-us/graph/docs/concepts/security-authorization).

## Запуск примера

1. В командной строке введите: ```python sample.py```.
2. В браузере перейдите по адресу [http://localhost:5000](http://localhost:5000).
3. Выберите **Войти с помощью учетной записи Майкрософт** и пройдите проверку подлинности с помощью удостоверения Майкрософт *.onmicrosoft.com.

Форма, позволяющая создавать отфильтрованный запрос оповещений, выбирая значения в раскрывающихся меню:
-
По умолчанию выбираются 5 основных оповещений от каждого поставщика API безопасности. Но вы можете выбрать получение 1, 5, 10 или 20 оповещений от каждого поставщика.

После выбора щелкните **Get alerts** (Получить оповещения). В Microsoft Graph будет отправлен вызов REST, и под формой отобразится таблица со всеми полученными оповещениями:

![Полученные оповещения](static/images/getAlerts.PNG)

В следующем разделе вы увидите форму "Manage Alerts" (Управление оповещениями), в которой можно обновить свойства жизненного цикла для конкретного оповещения по его идентификатору.
После обновления оповещения метаданные исходного оповещения отображаются над обновленным оповещением.

![Обновленные оповещения](static/images/updateAlerts.PNG)

Наконец, приложение позволяет отправлять уведомления веб-перехватчика из Microsoft Graph в пример приложения при обновлении оповещения, соответствующего ресурсу веб-перехватчика. Чтобы просмотреть уведомления веб-перехватчика, создайте подписку на веб-перехватчик. Затем щелкните "Notify" (Уведомление), чтобы открыть другую страницу, на которой будут отображаться уведомления веб-перехватчика при их отправке в ваше приложение.

   >**Примечание.** Если вы запускаете этот пример на локальном компьютере, для этого раздела требуется `ngrok` для правильного создания и получения уведомлений.

![раздел веб-перехватчика](static/images/webhook.PNG)

## Участие

Это открытые источники, выпущенные в разделе [MIT License](https://github.com/microsoftgraph/python-security-rest-sample/blob/master/LICENSE). Вопросы (включая запросы на добавление функций и/или вопросы о данном примере) и [запросы на вытягивание](https://github.com/microsoftgraph/python-security-rest-sample/pulls) приветствуются. Если вы хотели бы видеть другой образец Python в Microsoft Graph, мы будем рады узнать об этом. Сообщите [о проблеме](https://github.com/microsoftgraph/python-security-rest-sample/issues) и дайте нам знать!

Этот проект соответствует [Правилам поведения разработчиков открытого кода Майкрософт](https://opensource.microsoft.com/codeofconduct/). Дополнительные сведения см. в разделе [часто задаваемых вопросов о правилах поведения](https://opensource.microsoft.com/codeofconduct/faq/). Если у вас возникли вопросы или замечания, напишите нам по адресу [opencode@microsoft.com](mailto:opencode@microsoft.com).

Ваш отзыв важен для нас. Для связи с нами используйте сайт [Stack Overflow](https://stackoverflow.com/questions/tagged/microsoft-graph-security). Помечайте свои вопросы тегом [Microsoft-Graph-Security].

## Ресурсы

Документация:

* [Использование Microsoft Graph для интеграции с API безопасности](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/security-api-overview)
* Документация о [перечислении оповещений](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/alert_list) Microsoft Graph
* [Справочник по разрешениям Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference)

Примеры:

* [Примеры проверки подлинности на Python для Microsoft Graph](https://github.com/microsoftgraph/python-sample-auth)
* [Отправка почты через Microsoft Graph из Python](https://github.com/microsoftgraph/python-sample-send-mail)
* [Работа с разбитыми на страницы ответами Microsoft Graph в Python](https://github.com/microsoftgraph/python-sample-pagination)
* [Работа с открытыми расширениями Graph в Python](https://github.com/microsoftgraph/python-sample-open-extensions)

Пакеты:

* [Flask-OAuthlib](https://flask-oauthlib.readthedocs.io/en/latest/)
* [Запросы: HTTP for Humans](http://docs.python-requests.org/en/master/)

© Корпорация Майкрософт (Microsoft Corporation), 2018. Все права защищены.
