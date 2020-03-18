---
page_type: sample
description: "Cet exemple est composé d’une application web Python qui appelle les appels API Microsoft Graph courants."
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
# Démonstration de l’application web Python utilisant Microsoft Intelligent Security Graph

![language:Python](https://img.shields.io/badge/Language-Python-blue.svg?style=flat-square) ![license:MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)

Microsoft Graph fournit des API REST pour l’intégration auprès de prestataires Intelligent Security Graph qui permettent à votre application de récupérer des alertes, mettre à jour les propriétés du cycle de vie des alertes et d’envoyer facilement une notification d’alerte. Cet exemple est constitué d’une application web Python qui invoque les appels API Microsoft Graph Security courants, en utilisant les [demandes](http://docs.python-requests.org/en/master/) de la bibliothèque HTTP pour appeler ces API Microsoft Graph :

| API | Point de terminaison |
| | ------------------- | ------------------------------------------ | ---- |
| Recevez des alertes | /sécurité/alertes | [documents](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/alert) |
| Obtenir un profil utilisateur | /moi | [documents](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_get) |
| Créer un abonnement webhook | /inscriptions | [documents](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) |

Pour plus d’informations sur cet exemple, voir [Prise en main de Microsoft Graph dans une application Python](https://developer.microsoft.com/en-us/graph/docs/concepts/python
).

* [Conditions préalables](#prerequisites)
* [Installation](#installation)
* [Exécution de l’exemple](#running-the-sample)
* [Fonction d’aide sendmail](#sendmail-helper-function)
* [Contribution](#contributing)
* [Ressources](#resources)

## Conditions préalables

Avant d’installer l'exemple :

* Installez Python sur [https://www.python.org/](https://www.python.org/). Nous avons testé le code avec Python 3.6, mais les versions de Python 3. x doivent fonctionner. Si votre base de code s’exécute sous Python 2.7, vous pouvez trouver utile d’utiliser les outils [3to2](https://pypi.python.org/pypi/3to2) pour porter le code vers Python 2.7.
* Pour enregistrer votre application afin d’accéder à Microsoft Graph, vous avez besoin d’un [compte Microsoft](https://www.outlook.com) ou d’un [compte Office 365 pour les entreprises](https://msdn.microsoft.com/en-us/office/office365/howto/setup-development-environment#bk_Office365Account). Si vous n’avez aucun de ces éléments, vous pouvez créer gratuitement un compte Microsoft sur [outlook.com](https://www.outlook.com).

## Installation

Suivez ces étapes pour installer les exemples :

1. Clonez le référentiel en utilisation l’une des commandes suivantes :
    * ```git clone https://github.com/microsoftgraph/python-security-rest-sample.git```

2. Créez et activez un environnement virtuel (en option). Si vous découvrez les environnements virtuels Python, [Miniconda](https://conda.io/miniconda.html) est un excellent point de départ.
3. Dans le dossier racine de votre référentiel cloné, installez les dépendances de l’exemple répertoriées dans le fichier ```requirements.txt``` à l’aide de la commande suivante : ```pip install-r requirements.txt```.

## Configuration

Pour configurer les exemples, vous devez inscrire une nouvelle application dans le [Portail d’inscription des applications](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft.

Suivez ces étapes pour inscrire une nouvelle application :

1. Connectez-vous au [portail d’inscription de l'application](https://go.microsoft.com/fwlink/?linkid=2083908) en utilisant votre compte personnel, professionnel ou scolaire.
2. Sélectionnez **Nouvelle inscription**.
3. Entrez le nom de l’application, entrez `http://localhost :5000/login/authorized` comme URL de redirection.
    > **Remarque :** Si vous souhaitez que votre application soit mutualisée, sélectionnez des `Comptes dans n’importe quel répertoire d’organisation` dans la section **types de comptes pris en charge**.
4. Sélectionner **Inscription**.
5. Vous verrez ensuite la page de présentation de votre application. Copiez et enregistrez le champ d'**ID de l’application**. Vous en aurez besoin plus tard pour terminer le processus de configuration.
6. Sous **Certificats & secrets**, sélectionnez **Nouvelle clé secrète client** et ajoutez une description rapide. Un nouveau secret s’affiche dans la colonne **Valeur**. Copiez ce mot de passe. Vous en aurez besoin plus tard pour terminer le processus de configuration.
7. Sous **Autorisations API**, sélectionnez **Ajouter une autorisation** > **Microsoft Graph**.
8. Sous **Autorisations déléguées**, ajoutez les autorisations/étendues requises pour l’exemple. Cet exemple a besoin des autorisations **User.Read**, **SecurityEvents.ReadWrite.All**et **SecurityActions.ReadWrite.All**.
    >**Remarque** : Voir la [Référence des autorisations Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) pour obtenir plus d’informations sur le modèle d’autorisation de Graph.

Procédez comme suit afin d'autoriser [webhook](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) pour accéder à l’exemple via un tunnel NGROK :

> **Remarque** : Cette opération est obligatoire si vous voulez tester l’exemple d’Écouteur de notification sur localhost. Vous devez exposer un point de terminaison public HTTPS pour créer un abonnement et recevoir des notifications de Microsoft Graph. Pendant le test, vous pouvez utiliser ngrok pour autoriser temporairement les messages de Microsoft Graph à passer par un tunnel vers un port localhost sur votre ordinateur.

1. Téléchargez [ngrok](https://ngrok.com/download).
2. Suivez les instructions d’installation sur le site internet ngrok.
3. Exécutez ngrok, si vous utilisez Windows. Exécutez « ngrok.exe http 5000 » pour démarrer ngrok et ouvrir un tunnel vers votre port localhost 5000.
4. Ensuite, mettez à jour le fichier `config.py` avec votre URL ngrok.

    ![image ngrok](static/images/ngrok.PNG)

Pour terminer la configuration de l’exemple, modifiez le fichier ```config.py``` dans le dossier racine de votre référentiel cloné, puis suivez les instructions pour entrer votre ID client et la clé secrète client (qui sont appelées ID d’application et mot de passe dans le portail d’inscription des applications). Mettez à jour la propriété `notificationUrl` dans le fichier ```config.py``` pour refléter votre URL ngrok. Enregistrez ensuite la modification. Une fois que vous avez terminé ces étapes et que vous avez reçu l'[accord d’administrateur](#Get-Admin-consent-to-view-Security-data) pour votre application, vous pouvez exécuter l’exemple ```sample.py```, comme décrit ci-dessous.

## Obtenir l'accord de l’administrateur pour afficher les données de sécurité

1. Fournissez à votre administrateur votre **ID de l’application** et l’**URI de redirection** que vous avez utilisé au cours des étapes précédentes. L’administrateur de l’organisation (ou un autre utilisateur autorisé à donner des accords pour des ressources organisationnelles) est nécessaire pour accorder l’autorisation à l’application.
2. En tant qu’administrateur du client pour votre organisation, ouvrez une fenêtre de navigateur et conformez l’URL suivante dans la barre d’adresses :
 https://login.microsoftonline.com/common/adminconsent?client_id=APPLICATION_ID&state=12345&redirect_uri=REDIRECT_URL
 ou APPLICATION_ID est l'ID d'application et REDIRECT_URL est l'URL de redirection du portail d’inscription de l’application v2, une fois que vous avez cliqué sur votre application pour afficher ses propriétés.
3. Une fois la connexion effectuée, l’administrateur du locataire verra une fenêtre de dialogue comme celle-ci (suivant les autorisations demandées par l’application) :

   ![Dialogue du consentement de l’étendue](static/images/Scope.png)

4. Lorsque l’administrateur du client accepte cette boîte de dialogue, il lui accorde l’autorisation d’utiliser cette application pour tous les utilisateurs de son organisation.

> **Remarque :** Pour plus d’informations sur le flux d’autorisation, consultez l’article [Autorisation et API de Microsoft Graph Security](https://developer.microsoft.com/en-us/graph/docs/concepts/security-authorization)

## Exécution de l’exemple

1. À l’invite de commandes : ```python sample.py```
2. Dans le navigateur, accédez à [http://localhost:5000/](http://localhost:5000).
3. Sélectionnez **Connectez-vous à l’aide de Microsoft** et authentifiez-vous avec une identité Microsoft *. onmicrosoft.com.

Formulaire qui permet de créer une requête d’alerte filtrée en sélectionnant des valeurs dans les menus déroulants :
-
Par défaut, les 5 premières alertes de chaque fournisseur de l’API de sécurité sont sélectionnées. Vous pouvez toutefois choisir de récupérer 1, 5, 10 ou 20 alertes par fournisseur.

Une fois que vous avez sélectionné vos choix, cliquez sur **Obtenir des alertes**. Un appel REST est envoyé à Microsoft Graph et un tableau contenant toutes les alertes reçues s’affiche sous le formulaire :

![Alertes reçues](static/images/getAlerts.PNG)

Dans la section suivante, vous verrez un formulaire « Gérer les alertes » dans lequel vous pouvez mettre à jour les propriétés de cycle de vie d’un ID d’alerte spécifique.
Une fois l’alerte mise à jour, les métadonnées de l’alerte d’origine s’affichent au-dessus de l’alerte mise à jour.

![Alertes mises à jour](static/images/updateAlerts.PNG)

Enfin, l’application autorise l’envoi de notifications webhook à partir de Microsoft Graph à votre exemple d’application lorsqu’une alerte correspondant à votre ressource webhook est mise à jour. Pour afficher les notifications de webhook, créez un abonnement webhook. Cliquez ensuite sur « Notifier » pour ouvrir une autre page qui affiche les notifications webhook lorsqu'elles sont envoyées vers votre application.

   >**Remarque :** Si vous exécutez cet exemple sur votre ordinateur local, cette section nécessite l'utilisation de `ngrok` pour créer et recevoir correctement des notifications.

![Section de webhook](static/images/webhook.PNG)

## Contribution

Ces exemples sont open source et publiées sous la[Licence MIT](https://github.com/microsoftgraph/python-security-rest-sample/blob/master/LICENSE). Les problèmes (y compris les demandes de fonctionnalité et/ou les questions relatives à cet exemple) et les [demandes de tirage](https://github.com/microsoftgraph/python-security-rest-sample/pulls) sont bienvenus. Si vous souhaitez voir un autre exemple de Python pour Microsoft Graph, nous sommes intéressés par ces commentaires également. veuillez consigner un [problème](https://github.com/microsoftgraph/python-security-rest-sample/issues) et faites-le nous savoir.

Ce projet a adopté le [Code de conduite Open Source de Microsoft](https://opensource.microsoft.com/codeofconduct/). Pour en savoir plus, reportez-vous à la [FAQ relative au code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou contactez [opencode@microsoft.com](mailto:opencode@microsoft.com) pour toute question ou tout commentaire.

Votre avis compte beaucoup pour nous. Communiquez avec nous sur [Stack Overflow](https://stackoverflow.com/questions/tagged/microsoft-graph-security). Posez vos questions avec la balise [Microsoft-Graph-Security].

## Ressources

Documentation :

* [Utiliser Microsoft Graph pour intégrer l'API Security](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/security-api-overview)
* Documentation [Alertes de liste](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/alert_list) Microsoft Graph
* [Référence des autorisations de Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference)

Exemples :

* [Exemples d’authentification Python pour Microsoft Graph](https://github.com/microsoftgraph/python-sample-auth)
* [Envoyer un e-mail via Microsoft Graph de Python](https://github.com/microsoftgraph/python-sample-send-mail)
* [Utilisation des réponses paginées de Microsoft Graph dans Python](https://github.com/microsoftgraph/python-sample-pagination)
* [Utilisation des extensions ouvertes Graph dans Python](https://github.com/microsoftgraph/python-sample-open-extensions)

Packages :

* [Flask-OAuthlib](https://flask-oauthlib.readthedocs.io/en/latest/)
* [Demandes : HTTP pour les humains](http://docs.python-requests.org/en/master/)

Copyright (c) 2018 Microsoft Corporation. Tous droits réservés.
