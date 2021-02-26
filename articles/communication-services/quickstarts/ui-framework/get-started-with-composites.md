---
title: Erste Schritte mit zusammengesetzten Komponenten des Benutzeroberflächen-Frameworks von Azure Communication Services (SDK)
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung finden Sie Informationen zu den ersten Schritten mit zusammengesetzten Komponenten des Benutzeroberflächen-Frameworks.
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 17a8369463a27acffc8bdc52c48e4ae0624f41cd
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539683"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Schnellstart: Erste Schritte mit zusammengesetzten Komponenten des Benutzeroberflächen-Frameworks

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Beginnen Sie mit der Verwendung von Azure Communication Services, indem Sie das Benutzeroberflächen-Framework verwenden, um schnell Kommunikationsumgebungen in Ihre Anwendungen zu integrieren. In dieser Schnellstartanleitung erfahren Sie, wie Sie zusammengesetzte Komponenten des Benutzeroberflächen-Frameworks in Ihre Anwendung integrieren, um Kommunikationsumgebungen bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/): Active LTS- und Maintenance LTS-Versionen (Empfehlung: Node 12)
- Eine aktive Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](./../create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Initiieren der zusammengesetzten Komponente für Anrufe. Erfahren Sie mehr über das [Erstellen und Verwalten von Benutzerzugriffstoken](./../access-tokens.md).

## <a name="setting-up"></a>Einrichten

Für das Benutzeroberflächen-Framework muss eine React-Umgebung eingerichtet werden. Dies wird im nächsten Schritt gezeigt. Falls Sie bereits über eine React-App verfügen, können Sie diesen Abschnitt überspringen.

### <a name="set-up-react-app"></a>Einrichten einer React-App

In dieser Schnellstartanleitung wird die Vorlage „create-react-app“ verwendet. Weitere Informationen finden Sie unter [Erstellen einer neuen React-App](https://reactjs.org/docs/create-a-new-react-app.html).

```console

npx create-react-app my-app

cd my-app

```

Am Ende dieses Prozesses sollten Sie über eine vollständige Anwendung im Ordner `my-app` verfügen. In dieser Schnellstartanleitung werden Dateien im Ordner `src` geändert.

### <a name="install-the-package"></a>Installieren des Pakets

Verwenden Sie den Befehl `npm install`, um die Clientbibliothek für Telefonie von Azure Communication Services für JavaScript zu installieren. Verschieben Sie den bereitgestellten Tarball (private Vorschau) in das Verzeichnis „my-app“.

```console

//Private Preview install tarball

npm install --save ./{path for tarball}

```

Die Option `--save` listet die Bibliothek als Abhängigkeit in Ihrer Datei **package.json** auf.

### <a name="run-create-react-app"></a>Ausführen der React-App für die Erstellung

Führen Sie den folgenden Befehl aus, um die Installation der React-App für die Erstellung zu testen:

```console

npm run start 

```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen dienen zur Behandlung einiger der wichtigsten Features der Benutzeroberflächen-Clientbibliothek von Azure Communication Services:

| Name                                  | BESCHREIBUNG                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| GroupCall | Zusammengesetzte Komponente, von der eine Gruppenanrufumgebung mit Teilnehmergalerie und Steuerelementen gerendert wird. |
| GroupChat | Zusammengesetzte Komponente, von der eine Gruppenchatumgebung mit Chatthread und Chateingabe gerendert wird. |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Initialisieren der zusammengesetzten Komponenten für Anrufe und Chat

Navigieren Sie in `my-app` zum Ordner `src`, und suchen Sie nach der Datei `app.js`. Hier platzieren wir den folgenden Code, um unsere zusammengesetzten Komponenten für Gruppenchat und Gruppenanrufe zu initialisieren. Sie können wählen, welche Sie verwenden möchten (je nachdem, welche Art von Kommunikationsumgebung Sie erstellen). Bei Bedarf können auch beide gleichzeitig verwendet werden. Zum Initialisieren der Komponenten wird ein aus Azure Communication Services abgerufenes Zugriffstoken benötigt. Ausführliche Informationen zum Abrufen von Zugriffstoken finden Sie unter [Schnellstart: Erstellen und Verwalten von Zugriffstoken](./../access-tokens.md).

> [!NOTE]
> Von den Komponenten werden keine Zugriffstoken, Gruppen-IDs oder Thread-IDs generiert. Diese Elemente stammen aus Diensten, die die entsprechenden Schritte durchlaufen, um diese IDs zu generieren und an die Clientanwendung zu übergeben. Weitere Informationen finden Sie unter [Client- und Serverarchitektur](./../../concepts/client-and-server-architecture.md).
> 
> Beispiel: Von der zusammengesetzten Komponente für Gruppenchats wird erwartet, dass die Benutzer-ID (`userId`), die dem für die Initialisierung verwendeten Token (`token`) zugewiesen ist, bereits mit der bereitgestellten Thread-ID (`threadId`) verknüpft wurde. Wenn das Token nicht mit der Thread-ID verknüpft wurde, tritt für die zusammengesetzte Komponente für Gruppenchats ein Fehler auf. Weitere Informationen zum Chat finden Sie hier: [Schnellstart: Hinzufügen von Chatfunktionen zu Ihrer App](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

```

## <a name="run-quickstart"></a>Ausführen des Schnellstarts

Verwenden Sie zum Ausführen des obigen Codes den folgenden Befehl:

```console

npm run start 

```

Um die Funktionen vollständig testen zu können, benötigen Sie einen zweiten Client mit Anruf- und Chatfunktion, der an dem Anruf und an dem Chatthread teilnehmen kann. Dazu können Sie beispielsweise das [Hero-Beispiel für Gruppenanrufe](./../../samples/calling-hero-sample.md) und das [Hero-Beispiel für Gruppenchats](./../../samples/chat-hero-sample.md) verwenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Erste Schritte mit Basiskomponenten des Benutzeroberflächen-Frameworks](./get-started-with-components.md)

Weitere Informationen finden Sie in den folgenden Ressourcen:
- [Benutzeroberflächen-Frameworks von Azure Communication Services](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funktionen des Benutzeroberflächen-Frameworks](./../../concepts/ui-framework/ui-sdk-features.md)
