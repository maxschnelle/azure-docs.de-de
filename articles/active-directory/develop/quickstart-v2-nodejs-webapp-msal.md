---
title: 'Schnellstart: Hinzufügen von Authentifizierung zu einer Node-Web-App mit MSAL Node | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Authentifizierung bei einer Node.js-Web-App und der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Node.js implementieren.
services: active-directory
author: amikuma
manager: saeeda
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: amikuma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: cfe1b66662d73c18934ef9ecc884adee79493eb6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92640986"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Schnellstart: Anmelden von Benutzern und Abrufen eines Zugriffstokens in einer Node-Web-App mithilfe des Autorisierungscodeflows

In diesem Schnellstart führen Sie ein Codebeispiel aus, mit dem veranschaulicht wird, wie Benutzer von persönlichen Konten sowie Geschäfts-, Schul- und Unikonten in einer Node.js-Web-App mithilfe des Autorisierungscodeflows angemeldet werden können. Das Codebeispiel veranschaulicht ferner das Abrufen eines Zugriffstokens zum Aufrufen einer Web-API, in diesem Fall der Microsoft Graph-API. Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

In dieser Schnellstartanleitung wird die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Node.js (MSAL Node) beim Autorisierungscodeflow verwendet.

> [!IMPORTANT]
> MSAL Node [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement ([kostenloses Azure-Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrieren und Herunterladen Ihrer Schnellstartanwendung
>
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
>
> 1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
> 1. Wenn Sie in Ihrem Konto auf mehrere Mandanten zugreifen können, wählen Sie rechts oben das Konto aus, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
> 1. Wählen Sie [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) aus.
> 1. Wählen Sie **Neue Registrierung** aus.
> 1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** einen Namen für Ihre Anwendung ein.
> 1. Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
> 1. Legen Sie den Wert für **Umleitungs-URI** auf `http://localhost:3000/redirect` fest.
> 1. Wählen Sie **Registrieren** . 
> 1. Notieren Sie sich für die spätere Verwendung auf der Seite **Übersicht** den Wert von **Anwendungs-ID (Client)** .
> 1. Wählen Sie unter **Zertifikate und Geheimnisse** die Option **Neuer geheimer Clientschlüssel** aus.  Lassen Sie die Beschreibung und den Standardablauf leer, und klicken Sie auf **Hinzufügen** .
> 1. Notieren Sie sich den **Wert** des **geheimen Clientschlüssels** zur späteren Verwendung.

#### <a name="step-2-download-the-project"></a>Schritt 2: Herunterladen des Projekts

> [!div renderon="docs"]
> Um das Projekt mit einem Webserver unter Verwendung von Node.js auszuführen, [laden Sie die Kernprojektdateien herunter](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Ausführen des Projekts mit einem Webserver unter Verwendung von Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>Schritt 3: Konfigurieren Ihrer Node-App
>
> Extrahieren Sie das Projekt, öffnen Sie den Ordner *ms-identity-node-main* und dann die Datei *index.js* .
> Legen Sie die `clientID` mit der **Anwendungs-ID (Client)** fest.
> Legen Sie den `clientSecret` mit dem **Wert** des **geheimen Clientschlüssels** fest.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> Ändern Sie die Werte im Abschnitt `config`, wie hier beschrieben:
>
> - `Enter_the_Application_Id_Here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
> - `Enter_the_Client_Secret_Here` ist der **Wert** des **geheimen Clientschlüssels** für die von Ihnen registrierte Anwendung.
>
> Der Standardwert `authority` stellt die Azure-Hauptcloud (global) dar:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Den Wert für **Anwendungs-ID (Client)** finden Sie im Azure-Portal auf der Seite **Übersicht** der App-Registrierung. Wechseln Sie zu **Zertifikate und Geheimnisse** , um einen neuen **geheimen Clientschlüssel** abzurufen oder zu generieren.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>Schritt 4: Ausführen des Projekts

Führen Sie das Projekt mithilfe von Node.js aus:

1. Führen Sie im Projektverzeichnis die folgenden Befehle aus, um den Server zu starten:
    ```console
    npm install
    npm start
    ```
1. Navigieren Sie zu `http://localhost:3000/`.

1. Wählen Sie **Anmelden** aus, um den Anmeldeprozess zu starten.

    Bei der ersten Anmeldung werden Sie aufgefordert, einzuwilligen, dass die Anwendung auf Ihr Profil zugreifen und Sie anmelden darf. Nachdem Sie sich erfolgreich angemeldet haben, wird in der Befehlszeile eine Protokollmeldung angezeigt.

## <a name="more-information"></a>Weitere Informationen

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

Bei seiner Ausführung hostet das Beispiel einen Webserver auf „localhost“, Port 3000. Wenn ein Webbrowser auf diese Website zugreift, leitet das Beispiel den Benutzer sofort zu einer Microsoft-Authentifizierungsseite weiter. Aus diesem Grund enthält das Beispiel kein *html* oder Anzeigeelemente. Bei erfolgreicher Authentifizierung wird die Meldung „OK“ angezeigt.

### <a name="msal-node"></a>MSAL Node

Über die Bibliothek „MSAL Node“ werden Benutzer angemeldet und die Token angefordert, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet werden. Sie können die neueste Version mithilfe von Node.js-Paket-Manager (npm) herunterladen:

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen von Auth zu einer vorhandenen Web-App – GitHub-Codebeispiel >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code/readme.md)
