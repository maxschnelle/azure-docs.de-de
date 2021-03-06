---
title: 'Schnellstart: Beitreten zu einer Teams-Besprechung'
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: dd75e5e97e5dca898ba10e91528782861fb949ec
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114592"
---
## <a name="prerequisites"></a>Voraussetzungen

- Eine funktionierende [Communication Services-Telefonie-App](../getting-started-with-calling.md)
- Eine [Teams-Bereitstellung](https://docs.microsoft.com/deployoffice/teams-install)

## <a name="enable-teams-interoperability"></a>Aktivieren der Teams-Interoperabilität

Das Teams-Interoperabilitätsfeature befindet sich derzeit in der privaten Vorschau. Wenn Sie dieses Feature für Ihre Communication Services-Ressource aktivieren möchten, senden Sie eine E-Mail an [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com), und geben Sie darin Folgendes an:

1. Die Abonnement-ID des Azure-Abonnements, das Ihre Communication Services-Ressource enthält.
2. Ihre Teams-Mandanten-ID. Diese erhalten Sie am einfachsten, indem Sie [einen Link zum Team abrufen und weitergeben](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team).

Sie müssen Mitglied der besitzenden Organisation beider Entitäten sein, um dieses Feature verwenden zu können.

## <a name="add-the-teams-ui-controls"></a>Hinzufügen der Steuerelemente der Teams-Benutzeroberfläche

Fügen Sie in Ihrem HTML-Code ein neues Textfeld und eine Schaltfläche hinzu. Das Textfeld dient zur Eingabe des Teams-Besprechungskontexts, und die Schaltfläche wird verwendet, um der angegebenen Besprechung beizutreten:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Aktivieren der Steuerelemente der Teams-Benutzeroberfläche

Nun kann die Schaltfläche **Join Teams Meeting** (Teams-Besprechung beitreten) an den Code für den Beitritt zur angegebenen Teams-Besprechung gebunden werden:

```javascript
meetingButton.addEventListener("click", () => {
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Abrufen des Besprechungskontexts

Der Teams-Kontext kann mithilfe der Graph-APIs abgerufen werden. Dies wird in der [Graph-Dokumentation](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?view=graph-rest-beta&tabs=http) erläutert.

Die erforderlichen Besprechungsinformationen können auch der URL für den Besprechungsbeitritt aus der Besprechungseinladung entnommen werden. 

## <a name="run-the-code"></a>Ausführen des Codes

Webpack-Benutzer können `webpack-dev-server` verwenden, um Ihre App zu erstellen und auszuführen. Führen Sie den folgenden Befehl aus, um Ihren Anwendungshost auf einem lokalen Webserver zu bündeln:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Navigieren Sie in Ihrem Browser zu http://localhost:8080/. Daraufhin sollte Folgendes angezeigt werden:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Screenshot der fertigen JavaScript-Anwendung":::

Fügen Sie den Teams-Kontext in das Textfeld ein, und klicken Sie auf *Join Teams Meeting* (Teams-Besprechung beitreten), um der Teams-Besprechung über Ihre Communication Services-Anwendung beizutreten.

