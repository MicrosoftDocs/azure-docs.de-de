---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 5c00e88e228bf752f18c51741b89463012510e4c
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552834"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../create-communication-resource.md).
- Eine Telefonnummer, die über Ihre Communication Services-Ressource abgerufen wurde. [Beschaffen einer Telefonnummer](../../telephony-sms/get-phone-number.md)
- Ein `User Access Token`, um den Anrufclient zu aktivieren. Weitere Informationen zum [Abrufen eines `User Access Token`](../../access-tokens.md)


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

Der Code lautet wie folgt:

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
      id="callee-phone-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 230px;"
    />
    <div>
      <button id="call-phone-button" type="button">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-phone-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei mit dem Namen **client.js**, die die Anwendungslogik für diese Schnellstartanleitung enthalten soll. Fügen Sie den folgenden Code hinzu, um den Client für Telefonie zu importieren und Verweise auf die DOM-Elemente abzurufen, damit wir unsere Geschäftslogik anfügen können.

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;

const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential('<USER ACCESS TOKEN with PSTN scope>');
    callAgent = await callClient.createCallAgent(tokenCredential);
  //  callPhoneButton.disabled = false;
}

init();

```

## <a name="start-a-call-to-phone"></a>Starten eines Anrufs auf dem Telefon

Geben Sie die Telefonnummer an, die Sie in der Communication Services-Ressource abgerufen haben und mit deren Hilfe der Anruf gestartet wird:
> [!WARNING]
> Beachten Sie, dass Telefonnummern im internationalen Standardformat E.164 angegeben werden sollten. (Beispiel: +12223334444)

Fügen Sie einen Ereignishandler hinzu, um einen Anruf an die von Ihnen angegebene Telefonnummer zu tätigen, wenn Sie auf `callPhoneButton` klicken:


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.call(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: 'YOUR AZURE REGISTERED PHONE NUMBER HERE: +12223334444'}
  });
  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>Beenden eines Anrufs auf dem Telefon

Fügen Sie einen Ereignislistener hinzu, um den aktuellen Anruf zu beenden, wenn auf `hangUpPhoneButton` geklickt wird:

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

Die Eigenschaft `forEveryone` beendet den Anruf für alle Anrufteilnehmer.

## <a name="run-the-code"></a>Ausführen des Codes

Verwenden Sie `webpack-dev-server`, um Ihre App zu erstellen und auszuführen. Führen Sie den folgenden Befehl aus, um den Anwendungshost auf einem lokalen Webserver zu bündeln:


```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Navigieren Sie in Ihrem Browser zu `http://localhost:8080/`. Daraufhin sollte Folgendes angezeigt werden:

:::image type="content" source="../media/javascript/pstn-calling-javascript-app.png" alt-text="Screenshot der fertigen JavaScript-Anwendung":::

Sie können einen Anruf an eine echte Telefonnummer tätigen, indem Sie eine Telefonnummer in das hinzugefügte Textfeld eingeben und auf die Schaltfläche **Start Phone Call** (Telefonanruf starten) klicken.

> [!WARNING]
> Beachten Sie, dass Telefonnummern im internationalen Standardformat E.164 angegeben werden sollten. (Beispiel: +12223334444)
