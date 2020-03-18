---
page_type: sample
description: "Esta muestra consiste en una aplicación web en Python que invoca las llamadas comunes de la API de seguridad de Microsoft Graph"
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
# Demostración de la aplicación web de Python usando el Microsoft Intelligent Security Graph

![language: Python](https://img.shields.io/badge/Language-Python-blue.svg?style=flat-square) ![licencia: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)

Microsoft Graph proporciona API REST para integrarse con los proveedores de Intelligent Security Graph que permiten que su aplicación recupere alertas, actualice las propiedades del ciclo de vida de las alertas y envíe fácilmente por correo electrónico una notificación de alerta. Este ejemplo consiste en una aplicación web en Python que invoca las llamadas comunes de la API de seguridad de Microsoft Graph, utilizando la biblioteca de[peticiones](http://docs.python-requests.org/en/master/) HTTP para llamar a estas API de Microsoft Graph:

| API | Extremo |
| ------------------- | ---------------------------------- | ---- |
| Obtener alertas | /seguridad/alertas | [documentos](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/alert) |
| Obtener el perfil de usuario | /yo | [ documentos](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_get) |
| Crear una suscripción al webhook | /suscripciones| [documentos](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks) |

Para obtener más información sobre este ejemplo, consulte [Empieza con Microsoft Graph en una aplicación Python ](https://developer.microsoft.com/en-us/graph/docs/concepts/python
)

* [Requisitos previos](#prerequisites)
* [Instalación](#installation)
* [Ejecución del ejemplo](#running-the-sample)
* [Función auxiliar de sendmail](#sendmail-helper-function)
* [Colaboradores](#contributing)
* [Recursos](#resources)

## Requisitos previos

Antes de instalar la muestra:

* Instale Python desde [https://www.python.org/](https://www.python.org/). Hemos probado el código con Python 3.6, pero cualquier versión de Python 3.x debería funcionar. Si su código base se ejecuta en Python 2.7, puede que le resulte útil utilizar las herramientas[3to2](https://pypi.python.org/pypi/3to2) para portar el código a Python 2.7.
* Para registrar su solicitud de acceso a Microsoft Graph, necesitará una [cuenta de Microsoft](https://www.outlook.com) o una [cuenta de Office 365 para empresas](https://msdn.microsoft.com/en-us/office/office365/howto/setup-development-environment#bk_Office365Account). Si no tienes uno de estos, puedes crear una cuenta de Microsoft gratis en [outlook.com](https://www.outlook.com).

## Instalación

Siga estos pasos para instalar las muestras:

1. Clonar el repo, usando uno de estos comandos:
    * ```git clone https://github.com/microsoftgraph/python-sample-console-app.git```

2. Crear y activar un entorno virtual (opcional). Si no está familiarizado con los entornos virtuales de Python, [Miniconda](https://conda.io/miniconda.html) es un buen punto de partida.
3. En la carpeta raíz de su repositorio clonado, instale las dependencias del ejemplo que aparecen en el archivo ```requirements.txt``` con este comando: ```pip install -r requirements.txt```.

## Configuración

Para configurar las muestras, tendrá que registrar una nueva aplicación en el Microsoft [Portal de registro de aplicaciones](https://go.microsoft.com/fwlink/?linkid=2083908).

Siga estos pasos para registrar una nueva solicitud:

1. Inicie sesión en el [Portal de registro de solicitudes ](https://go.microsoft.com/fwlink/?linkid=2083908) usando su cuenta personal, de trabajo o de la escuela.
2. Seleccione **Nuevo registro**.
3. Introduzca un nombre de aplicación, introduzca `http://localhost:5000/login/authorized` como la URL de redireccionamiento.
    > **Nota:** Si desea que su solicitud sea de múltiples arrendatarios, seleccione `Cuentas en cualquier directorio de la organización` en la sección **tipos de cuenta compatibles**.
4. Seleccione **Registrarse**.
5. A continuación verás la página de resumen de tu aplicación. Copie y guarde el campo de **Id. de la aplicación**. Lo necesitará más tarde para completar el proceso de configuración.
6. En **Certificados y secretos**, elija **nuevo cliente secreto** y añada una pequeña descripción. Un nuevo secreto será mostrado en la columna **valor**. Copie esta contraseña. Lo necesitará más tarde para completar el proceso de configuración.
7. En **permisos de la API**, elija **agregar un permiso** > **Microsoft Graph**.
8. En **Permisos Delegados**, agregue los permisos/ámbitos requeridos para el ejemplo. Este ejemplo requiere **User.Read**, **SecurityEvents.ReadWrite.All**y **SecurityActions.ReadWrite.All** permisos.
    >**Nota**: Consulte [Referencia de los permisos de Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) para más información sobre el modelo de permiso de Graph.

Siga estos pasos para permitir[webhooks](https://developer.microsoft.com/en-us/graph/docs/concepts/webhooks)para acceder a la muestra a través de un túnel de NGROK:

> **Nota**: Esto es necesario si se quiere probar la muestra del Notification Listener en el localhost. Debes exponer un punto final HTTPS público para crear una suscripción y recibir notificaciones de Microsoft Graph. Mientras realizas las pruebas, puedes usar ngrok para permitir temporalmente que los mensajes de Microsoft Graph hagan un túnel hacia un puerto local en tu computadora.

1. Descargue [ngrok](https://ngrok.com/download).
2. Siga las instrucciones de instalación en el sitio web de ngrok.
3. Ejecute ngrok, si está usando Windows. Ejecute "ngrok.exe http 5000" para iniciar ngrok y abrir un túnel hacia su puerto local 5000.
4. Después, actualice el archivo `config. js` con la URL ngrok.

    ![imagen ngrok](static/images/ngrok.PNG)

Como último paso para configurar la muestra, modifique el archivo ```config.py``` en la carpeta raíz de su repo clonado, y siga las instrucciones para introducir su ID de cliente y su Secreto de cliente (que se denominan ID de aplicación y contraseña en el portal de registro de aplicaciones). Actualice la propiedad `notificationUrl` en el archivo ```config.py``` para reflejar la url ngrok. Luego guarde el cambio Después de haber completado estos pasos y haber recibido el[consentimiento del administrador](#Get-Admin-consent-to-view-Security-data) para tu aplicación, serás capaz de ejecutar la muestra```sample.py``` como se indica a continuación.

## Obtener el consentimiento del administrador para ver los datos de seguridad

1. Proporcione a su administrador su **identificador de la aplicación** y la **URI de redireccionamiento ** que utilizó en los pasos anteriores. El administrador de la organización (u otro usuario autorizado para otorgar el consentimiento para los recursos de la organización) debe dar su consentimiento a la solicitud.
2. Como administrador de su organización, abra una ventana del navegador y cree la siguiente URL en la barra de direcciones:
https://login.microsoftonline.com/common/adminconsent?client_id=APPLICATION_ID&state=12345&redirect_uri=REDIRECT_URL donde APPLICATION_ID es el ID de la aplicación y
REDIRECT_URL es el URL de redireccionamiento de los valores del portal de registro de la aplicación V2 después de hacer clic en su aplicación para ver sus propiedades.
3. Después de iniciar sesión, se presentará al administrador del inquilino un diálogo como el siguiente (dependiendo de los permisos que solicite la aplicación):

   ![Diálogo de consentimiento del alcance](static/images/Scope.png)

4. Cuando el administrador del inquilino acepta este diálogo, está dando su consentimiento para que todos los usuarios de su organización utilicen esta aplicación.

> **Nota:** Para más detalles sobre el flujo de autorización, lea [Autorización y el  API Microsoft Graph Security](https://developer.microsoft.com/en-us/graph/docs/concepts/security-authorization)

## Ejecución del ejemplo

1. En la línea de comandos: ```python sample.py```.
2. En tu navegador, navega a [http://localhost:5000](http://localhost:5000) 
3. Elija**Iniciar sesión con Microsoft**y autenticarse con una identidad de Microsoft *.onmicrosoft.com.

Un formulario que permite construir una consulta de alerta filtrada seleccionando valores de los menús desplegables:
-
De forma predeterminada, se seleccionarán las 5 alertas principales de cada proveedor de seguridad de la API. Pero puede seleccionar para recuperar 1, 5, 10 o 20 alertas de cada proveedor.

Después de que hayas seleccionado tus opciones, haz clic en**Obtener alertas**. Se enviará una llamada REST al Microsoft Graph, y una tabla con todas las alertas recibidas se mostrará debajo del formulario:

![Alertas recibidas](static/images/getAlerts.PNG)

En la siguiente sección verás un formulario "Administrar alertas" donde puedes actualizar las propiedades del ciclo de vida de una alerta específica - por el ID de la alerta.
Una vez que se actualiza la alerta, los metadatos de la alerta original se muestran encima de la alerta actualizada.

![Alertas actualizadas](static/images/updateAlerts.PNG)

Por último, la aplicación permite que se envíen notificaciones de webhook desde el Microsoft Graph a tu aplicación de muestra cuando se actualice una alerta que coincida con tu recurso de webhook. Para ver las notificaciones de webhook, cree una suscripción a webhook. Luego haz clic en "Notificar" para abrir otra página que mostrará las notificaciones de webhook a medida que se vayan enviando a tu aplicación.

   >**Nota:** Si está ejecutando esta muestra en su máquina local, entonces esta sección requiere que `ngrok` cree y reciba notificaciones correctamente.

![sección webhook](static/images/webhook.PNG)

## Colaboradores

Estos ejemplos son el origen abierto, lanzados con la [licencia MIT](https://github.com/microsoftgraph/python-security-rest-sample/blob/master/LICENSE). Los comentarios (incluidas las solicitudes de características y/o las preguntas sobre este ejemplo) y [solicitudes de incorporación de cambios](https://github.com/microsoftgraph/python-security-rest-sample/pulls) son bienvenidos. Si hay algún otro ejemplo de Python que le gustaría ver para Microsoft Graph, también nos interesan estos comentarios: envíe un [problema](https://github.com/microsoftgraph/python-security-rest-sample/issues) y háganoslo saber.

Este proyecto ha adoptado la [Código de conducta de código abierto de Microsoft](https://opensource.microsoft.com/codeofconduct/). Para más información, consulte el[Código de Conducta FAQ](https://opensource.microsoft.com/codeofconduct/faq/)o póngase en contacto con [opencode@microsoft.com](mailto:opencode@microsoft.com)con cualquier pregunta o comentario adicional.

Su opinión es importante para nosotros. Conecte con nosotros en [Stack Overflow](https://stackoverflow.com/questions/tagged/microsoft-graph-security). Etiquete sus preguntas con [Microsoft-Graph-Security].

## Recursos

Documentación:

* [Utilice el Microsoft Graph para integrarse con el API de seguridad](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/security-api-overview)
* Documentación[Alertas de la lista](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/alert_list)Microsoft Graph
* [Referencia de permisos de Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference)

Ejemplos:

* [Muestras de autenticación de Python para Microsoft Graph](https://github.com/microsoftgraph/python-sample-auth)
* [Envío de correo a través de Microsoft Graph desde Python](https://github.com/microsoftgraph/python-sample-send-mail) 
* [Trabajando con respuestas paginadas de Microsoft Graph en Python](https://github.com/microsoftgraph/python-sample-pagination)
* [Trabajando con las extensiones abiertas de Graph en Python ](https://github.com/microsoftgraph/python-sample-open-extensions)

Paquetes:

* [Flask-Oauthlib](https://flask-oauthlib.readthedocs.io/en/latest/)
* [Solicitudes: HTTP para humanos](http://docs.python-requests.org/en/master/)

Copyright (c) 2018 Microsoft Corporation. Todos los derechos reservados.
