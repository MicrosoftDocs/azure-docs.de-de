---
title: Erstellen einer eigenen Komponente für das Benutzeroberflächen-Framework
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine benutzerdefinierte, mit dem Benutzeroberflächen-Framework kompatible Komponente erstellen.
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 88de5ca56738e13446be30fb14df6bcdac360827
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539686"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Schnellstart: Erstellen einer eigenen Komponente für das Benutzeroberflächen-Framework

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Beginnen Sie mit der Verwendung von Azure Communication Services, indem Sie das Benutzeroberflächen-Framework verwenden, um schnell Kommunikationsumgebungen in Ihre Anwendungen zu integrieren.

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der vom Benutzeroberflächen-Framework bereitgestellten vordefinierten Zustandsschnittstelle Ihre eigenen Komponenten erstellen. Diese Vorgehensweise eignet sich perfekt für Entwickler, die mehr Anpassungsmöglichkeiten benötigen und ihre eigenen Entwurfsressourcen für die Umgebung verwenden möchten. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/): Active LTS- und Maintenance LTS-Versionen (Empfehlung: Node 12)
- Eine aktive Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](./../create-communication-resource.md).
- Ein Benutzerzugriffstoken, um den Anruf-Client zu instanziieren. Erfahren Sie mehr über das [Erstellen und Verwalten von Benutzerzugriffstoken](./../access-tokens.md).

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

//For Private Preview install tarball

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
| Anbieter| Fluent-Benutzeroberflächenanbieter, mit dem Entwickler zugrunde liegende Komponenten der Fluent-Benutzeroberfläche ändern können|
| CallingProvider| Anrufanbieter zum Instanziieren eines Anrufs. Zum Hinzufügen von Basiskomponenten erforderlich|
| ChatProvider | Chatanbieter zum Instanziieren eines Chatthreads. Zum Hinzufügen von Basiskomponenten erforderlich|
| connectFuncsToContext | Methode zum Verbinden von Komponenten des Benutzeroberflächen-Frameworks mit zugrunde liegenden Anbietern mit Zuordnungen |
| MapToChatMessageProps | Zuordnung auf Chatnachricht-Datenebene, die Chatnachrichteneigenschaften für Komponenten bereitstellt |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Initialisieren von Chatanbietern unter Verwendung von Anmeldeinformationen für Azure Communication Services

In dieser Schnellstartanleitung wird Chat als Beispiel verwendet. Weitere Informationen zu Anrufen finden Sie unter [Schnellstart: Erste Schritte mit Basiskomponenten des Benutzeroberflächen-Frameworks](./get-started-with-components.md) sowie unter [Schnellstart: Erste Schritte mit zusammengesetzten Komponenten des Benutzeroberflächen-Frameworks](./get-started-with-composites.md).

Navigieren Sie in `my-app` zum Ordner `src`, und suchen Sie nach der Datei `app.js`. Hier platzieren wir den folgenden Code, um unseren Chatanbieter zu initialisieren. Dieser Anbieter ist dafür zuständig, den Kontext der Anruf- und Chatumgebung aufrechtzuerhalten. Zum Initialisieren der Komponenten wird ein aus Azure Communication Services abgerufenes Zugriffstoken benötigt. Ausführliche Informationen zum Abrufen eines Zugriffstokens finden Sie unter [Schnellstart: Erstellen und Verwalten von Zugriffstoken](./../access-tokens.md).

Von Komponenten des Benutzeroberflächen-Frameworks wird für den restlichen Dienst die gleiche allgemeine Architektur verwendet. Von den Komponenten werden keine Zugriffstoken, Gruppen-IDs oder Thread-IDs generiert. Diese Elemente stammen aus Diensten, die die entsprechenden Schritte durchlaufen, um diese IDs zu generieren und an die Clientanwendung zu übergeben. Weitere Informationen finden Sie unter [Client- und Serverarchitektur](./../../concepts/client-and-server-architecture.md).

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Nachdem der Anbieter initialisiert wurde, können Sie mithilfe einer Komponente des Benutzeroberflächen-Frameworks und zusätzlicher Layoutlogik Ihr eigenes Layout erstellen. Der Anbieter übernimmt die Initialisierung der gesamten zugrunde liegenden Logik sowie die ordnungsgemäße Verbindungsherstellung zwischen den verschiedenen Komponenten. Als Nächstes erstellen wir eine benutzerdefinierte Komponente mit Zuordnungen des Benutzeroberflächen-Frameworks, um eine Verbindung mit unserem Chatanbieter herzustellen.


## <a name="create-a-custom-component-using-mappers"></a>Erstellen einer benutzerdefinierten Komponente unter Verwendung von Zuordnungen

Als Erstes erstellen wir eine neue Datei namens `SimpleChatThread.js`, in der wir die Komponente erstellen. Anschließend importieren wir zunächst die erforderlichen Komponenten des Benutzeroberflächen-Frameworks. Hier verwenden wir standardmäßigen HTML-Code und React, um eine vollständig benutzerdefinierte Komponente für einen einfachen Chatthread zu erstellen. Wir verwenden die Methode `connectFuncsToContext`, um benutzerdefinierten Komponenten für `SimpleChatThread` mithilfe der Zuordnung `MapToChatMessageProps` Eigenschaften zuzuordnen. Diese Eigenschaften ermöglichen den Zugriff auf die gesendeten und empfangenen Chatnachrichten, um sie in unserem einfachen Thread aufzufüllen.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Hinzufügen Ihrer benutzerdefinierten Komponente zu Ihrer Anwendung

Nachdem die benutzerdefinierte Komponente nun bereit ist, können wir sie importieren und unserem Layout hinzufügen.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Ausführen des Schnellstarts

Verwenden Sie zum Ausführen des obigen Codes den folgenden Befehl:

```console

npm run start   

```

Damit Sie die Funktionen vollständig testen können, benötigen Sie einen zweiten Client mit Chatfunktion zum Senden von Nachrichten, die von unserem einfachen Chatthread empfangen werden. Dazu können Sie beispielsweise das [Hero-Beispiel für Gruppenanrufe](./../../samples/calling-hero-sample.md) und das [Hero-Beispiel für Gruppenchats](./../../samples/chat-hero-sample.md) verwenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Erste Schritte mit zusammengesetzten Komponenten des Benutzeroberflächen-Frameworks](./get-started-with-composites.md)

Weitere Informationen finden Sie in den folgenden Ressourcen:
- [Benutzeroberflächen-Frameworks von Azure Communication Services](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funktionen des Benutzeroberflächen-Frameworks](./../../concepts/ui-framework/ui-sdk-features.md)
- [Schnellstart: Erste Schritte mit Basiskomponenten des Benutzeroberflächen-Frameworks](./get-started-with-components.md)
