---
title: Erste Schritte mit Basiskomponenten des Benutzeroberflächen-Frameworks von Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung finden Sie Informationen zu den ersten Schritten mit Basiskomponenten des Benutzeroberflächen-Frameworks.
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 01fa817bd5173abc3b51e708b69b787c8b655e90
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539711"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Schnellstart: Erste Schritte mit Basiskomponenten des Benutzeroberflächen-Frameworks

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Beginnen Sie mit der Verwendung von Azure Communication Services, indem Sie das Benutzeroberflächen-Framework verwenden, um schnell Kommunikationsumgebungen in Ihre Anwendungen zu integrieren. In dieser Schnellstartanleitung erfahren Sie, wie Sie Basiskomponenten des Benutzeroberflächen-Frameworks in Ihre Anwendung integrieren, um Kommunikationsumgebungen bereitzustellen. 

Im Benutzeroberflächen-Framework stehen zwei Arten von Komponenten zur Verfügung: Basiskomponenten und zusammengesetzte Komponenten.

- **Basiskomponenten** stellen individuelle Kommunikationsfunktionen dar. Mithilfe dieser Grundbausteine lassen sich komplexe Kommunikationsumgebungen erstellen. 
- **Zusammengesetzte Komponenten** sind vorgefertigte Kommunikationsumgebungen für gängige Kommunikationsszenarien. Sie setzen sich aus **Basiskomponenten** zusammen und werden als Paket bereitgestellt, um die Integration in Anwendungen zu vereinfachen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/): Active LTS- und Maintenance LTS-Versionen (Empfehlung: Node 12)
- Eine aktive Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](./../create-communication-resource.md).
- Ein Benutzerzugriffstoken, um den Anruf-Client zu instanziieren. Erfahren Sie mehr über das [Erstellen und Verwalten von Benutzerzugriffstoken](./../access-tokens.md).

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
| CallingProvider| Anrufanbieter zum Instanziieren eines Anrufs. Zum Hinzufügen zusätzlicher Komponenten erforderlich|
| ChatProvider | Chatanbieter zum Instanziieren eines Chatthreads. Zum Hinzufügen zusätzlicher Komponenten erforderlich|
| MediaGallery | Basiskomponente zum Anzeigen von Anrufteilnehmern und der zugehörigen Remotevideostreams |
| MediaControls | Basiskomponente zur Anrufsteuerung (einschließlich Stummschaltung, Video und Bildschirmfreigabe) |
| ChatThread | Basiskomponente zum Rendern eines Chatthreads mit Eingabeindikatoren, Lesebestätigungen und Ähnlichem |
| SendBox | Basiskomponente, die Benutzern die Eingabe von Nachrichten ermöglicht, die an den Thread gesendet werden, an dem sie teilnehmen|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Initialisieren von Anruf- und Chatanbietern unter Verwendung von Anmeldeinformationen für Azure Communication Services

Navigieren Sie in `my-app` zum Ordner `src`, und suchen Sie nach der Datei `app.js`. Hier platzieren wir den folgenden Code, um unsere Anruf- und Chatanbieter zu initialisieren. Diese Anbieter sind dafür zuständig, den Kontext der Anruf- und Chatumgebung aufrechtzuerhalten. Sie können wählen, welche Sie verwenden möchten (je nachdem, welche Art von Kommunikationsumgebung Sie erstellen). Bei Bedarf können auch beide gleichzeitig verwendet werden. Zum Initialisieren der Komponenten wird ein aus Azure Communication Services abgerufenes Zugriffstoken benötigt. Ausführliche Informationen zum Abrufen von Zugriffstoken finden Sie unter [Schnellstart: Erstellen und Verwalten von Zugriffstoken](./../access-tokens.md).

> [!NOTE]
> Von den Komponenten werden keine Zugriffstoken, Gruppen-IDs oder Thread-IDs generiert. Diese Elemente stammen aus Diensten, die die entsprechenden Schritte durchlaufen, um diese IDs zu generieren und an die Clientanwendung zu übergeben. Weitere Informationen finden Sie unter [Client- und Serverarchitektur](./../../concepts/client-and-server-architecture.md).
> 
> Beispiel: Vom Chatanbieter wird erwartet, dass die Benutzer-ID (`userId`), die dem für die Initialisierung verwendeten Token (`token`) zugewiesen ist, bereits mit der bereitgestellten Thread-ID (`threadId`) verknüpft wurde. Wenn das Token nicht mit der Thread-ID verknüpft wurde, tritt für den Chatanbieter ein Fehler auf. Weitere Informationen zum Chat finden Sie hier: [Schnellstart: Hinzufügen von Chatfunktionen zu Ihrer App](./../chat/get-started.md)

In dieser Schnellstartanleitung wird ein Fluent-Benutzeroberflächendesign verwendet, um das Erscheinungsbild der Anwendung zu verbessern:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Nachdem der Anbieter initialisiert wurde, können Sie mithilfe von Basiskomponenten des Benutzeroberflächen-Frameworks und zusätzlicher Layoutlogik Ihr eigenes Layout erstellen. Der Anbieter übernimmt die Initialisierung der gesamten zugrunde liegenden Logik sowie die ordnungsgemäße Verbindungsherstellung zwischen den verschiedenen Komponenten. Als Nächstes verwenden wir verschiedene, vom Benutzeroberflächen-Framework bereitgestellte Basiskomponenten, um Kommunikationsumgebungen zu erstellen. Sie können das Layout dieser Komponenten anpassen und weitere benutzerdefinierte Komponenten hinzufügen, die zusammen mit ihnen gerendert werden sollen. 

## <a name="build-ui-framework-calling-component-experiences"></a>Erstellen von Umgebungen mit der Anrufkomponente des Benutzeroberflächen-Frameworks

Für Anrufe verwenden wir die Komponenten `MediaGallery` und `MediaControls`. Weitere Informationen zu diesen Komponenten finden Sie unter [Funktionen des Benutzeroberflächen-Frameworks](./../../concepts/ui-framework/ui-sdk-features.md). Erstellen Sie zunächst im Ordner `src` eine neue Datei namens `CallingComponents.js`. Hier initialisieren wir eine Funktionskomponente für unsere Basiskomponenten, um sie dann in `app.js` zu importieren. Sie können zusätzliches Layout und Formatierungen rund um die Komponenten hinzufügen. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

Am Ende dieser Datei haben wir die Anrufkomponenten mithilfe der Methode `connectFuncsToContext` aus dem Benutzeroberflächen-Framework exportiert, um die Komponenten der Anrufbenutzeroberfläche mithilfe der Zuordnungsfunktion `MapToCallingSetupProps` mit dem zugrunde liegenden Zustand zu verknüpfen. Diese Methode führt dazu, dass die Eigenschaften der Komponente aufgefüllt werden. Diese können wir dann verwenden, um den Zustand zu überprüfen und an dem Anruf teilzunehmen. Mithilfe der Eigenschaft `isCallInitialized` überprüfen wir, ob der Anruf-Agent (`CallAgent`) bereit ist. Anschließend verwenden wir die Methode `joinCall`, um an dem Anruf teilzunehmen. Das Benutzeroberflächen-Framework unterstützt benutzerdefinierte Zuordnungsfunktionen für Szenarien, in denen Entwickler steuern möchten, wie Daten an die Komponenten gepusht werden.

## <a name="build-ui-framework-chat-component-experiences"></a>Erstellen von Umgebungen mit der Chatkomponente des Benutzeroberflächen-Frameworks

Für Chats verwenden wir die Komponenten `ChatThread` und `SendBox`. Weitere Informationen zu diesen Komponenten finden Sie unter [Funktionen des Benutzeroberflächen-Frameworks](./../../concepts/ui-framework/ui-sdk-features.md). Erstellen Sie zunächst im Ordner `src` eine neue Datei namens `ChatComponents.js`. Hier initialisieren wir eine Funktionskomponente für unsere Basiskomponenten, um sie dann in `app.js` zu importieren.

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Hinzufügen von Anruf- und Chatkomponenten zur Hauptanwendung

Als Nächstes fügen wir die Komponenten dem Anrufanbieter (`CallingProvider`) und dem Chatanbieter (`ChatProvider`) in der Datei `app.js` hinzu, wie hier zu sehen:

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

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
> [Schnellstart: Erste Schritte mit zusammengesetzten Komponenten des Benutzeroberflächen-Frameworks](./get-started-with-composites.md)

Weitere Informationen finden Sie in den folgenden Ressourcen:
- [Benutzeroberflächen-Frameworks von Azure Communication Services](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funktionen des Benutzeroberflächen-Frameworks](./../../concepts/ui-framework/ui-sdk-features.md)
