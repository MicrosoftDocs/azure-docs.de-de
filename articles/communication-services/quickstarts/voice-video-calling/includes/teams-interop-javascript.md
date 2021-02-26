---
title: 'Schnellstart: Beitreten zu einer Teams-Besprechung'
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: fa7f279e13e5dbb59af7eb3a7939be1edf0f7d5d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552433"
---
## <a name="prerequisites"></a>Voraussetzungen

- Eine funktionierende [Communication Services-Telefonie-App](../getting-started-with-calling.md)
- Eine [Teams-Bereitstellung](/deployoffice/teams-install)


## <a name="add-the-teams-ui-controls"></a>Hinzufügen der Steuerelemente der Teams-Benutzeroberfläche

Ersetzen Sie den Code in „index.html“ durch den folgenden Codeausschnitt.
Das Textfeld dient zur Eingabe des Teams-Besprechungskontexts, und die Schaltfläche wird verwendet, um der angegebenen Besprechung beizutreten:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Aktivieren der Steuerelemente der Teams-Benutzeroberfläche

Ersetzen Sie den Code in „client.js“ durch den folgenden Codeausschnitt.

```javascript
import { CallClient } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Abrufen des Teams-Besprechungslinks

Der Teams-Besprechungslink kann mithilfe der Graph-APIs abgerufen werden. Dies wird in der [Graph-Dokumentation](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) erläutert.
Das Communication Services-SDK für Telefonie akzeptiert einen vollständigen Teams-Besprechungslink. Dieser Link wird als Teil der `onlineMeeting`-Ressource zurückgegeben, auf die Sie über die [`joinWebUrl`-Eigenschaft](/graph/api/resources/onlinemeeting?view=graph-rest-beta) zugreifen können. Sie können die erforderlichen Besprechungsinformationen auch aus der URL **Besprechung beitreten** in der Teams-Besprechungseinladung selbst abrufen.

## <a name="run-the-code"></a>Ausführen des Codes

Webpack-Benutzer können `webpack-dev-server` verwenden, um Ihre App zu erstellen und auszuführen. Führen Sie den folgenden Befehl aus, um Ihren Anwendungshost auf einem lokalen Webserver zu bündeln:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Navigieren Sie in Ihrem Browser zu http://localhost:8080/. Daraufhin sollte Folgendes angezeigt werden:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Screenshot der fertigen JavaScript-Anwendung":::

Fügen Sie den Teams-Kontext in das Textfeld ein, und klicken Sie auf *Join Teams Meeting* (Teams-Besprechung beitreten), um der Teams-Besprechung über Ihre Communication Services-Anwendung beizutreten.
