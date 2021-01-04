---
title: 'Schnellstart: REST-API des Azure SignalR-Diensts'
description: Erfahren Sie anhand von Beispielen, wie Sie die REST-API mit Azure SignalR Service verwenden. Sie finden hier Details zur REST-API-Spezifikation.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 2613f91e8c7f1ad3a05792a9a165f4560c09a637
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874524"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Schnellstart: Senden von Nachrichten in Echtzeit aus einer Konsolen-App

Der Azure SignalR-Dienst stellt die [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) zur Verfügung, um Kommunikationsszenarien zwischen Servern und Clients (z.B. Broadcasting) zu unterstützen. Sie können jede beliebige Programmiersprache auswählen, die die REST-API aufrufen kann. Sie können Nachrichten an alle verbundenen Clients, an einen bestimmten Client anhand seines Namens oder an eine Gruppe von Clients übermitteln.

In diesem Schnellstart erfahren Sie, wie Sie Nachrichten aus einer Befehlszeilenanwendung an verbundene Clientanwendungen in C# senden können.

## <a name="prerequisites"></a>Voraussetzungen

Dieser Schnellstart kann unter MacOS, Windows oder Linux ausgeführt werden.

* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* Ein Text-Editor oder Code-Editor Ihrer Wahl.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsapi).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter <https://portal.azure.com/> mit Ihrem Azure-Konto beim Azure-Portal an.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsapi).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsapi).

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Während der Dienst bereitgestellt wird, bereiten wird den Code vor. Klonen Sie die [Beispiel-App von GitHub](https://github.com/aspnet/AzureSignalR-samples.git), legen Sie die Verbindungszeichenfolge des SignalR-Diensts fest, und führen Sie die Anwendung lokal aus.

1. Öffnen Sie ein Git-Terminalfenster. Wechseln Sie in einen Ordner, in dem Sie das Beispielprojekt klonen möchten.

1. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsapi).

## <a name="build-and-run-the-sample"></a>Erstellen und Ausführen des Beispiels

Dieses Beispiel ist eine Konsolen-App, die die Verwendung des Azure SignalR-Diensts zeigt. Sie bietet zwei Modi:

- Servermodus: Verwenden Sie einfache Befehle zum Aufrufen der REST-API des Azure SignalR-Diensts.
- Clientmodus: Stellen Sie eine Verbindung mit dem Azure SignalR-Dienst her, und empfangen Sie Nachrichten vom Server.

Sie können auch herausfinden, wie Sie ein Zugriffstoken zur Authentifizierung mit dem Azure SignalR-Dienst generieren.

### <a name="build-the-executable-file"></a>Erstellen der ausführbaren Datei

Wir verwenden macOS osx.10.13-x64 als Beispiel. Sie können [Referenzmaterial](/dotnet/core/rid-catalog) zum Buildvorgang auf anderen Plattformen nutzen.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Starten Sie einen Client.

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Starten Sie einen Server.

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsapi).

## <a name="run-the-sample-without-publishing"></a>Führen Sie das Beispiel ohne Veröffentlichung aus.

Sie können auch den folgenden Befehl ausführen, um einen Server oder Client zu starten.

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Verwenden von „user-secrets“ zum Angeben der Verbindungszeichenfolge

Sie können `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` im Stammverzeichnis des Beispiels ausführen. Danach benötigen Sie die Option `-c "<ConnectionString>"` nicht mehr.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsapi).

## <a name="usage"></a>Verwendung

Nachdem der Server gestartet wurde, verwenden Sie den Befehl zum Senden von Nachrichten:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Sie können mehrere Clients mit unterschiedlichen Clientnamen starten.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsapi).

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Integration in Drittanbieterdienste

Der Azure SignalR-Dienst ermöglicht die Integration von Drittanbieterdiensten in das System.

### <a name="definition-of-technical-specifications"></a>Definition der technischen Spezifikationen

Die folgende Tabelle enthält alle Versionen der REST-APIs, die aktuell unterstützt werden. Außerdem finden Sie hier die Zieldatei für die jeweilige Version.

Version | API-Zustand | Tür | Spezifisch
--- | --- | --- | ---
`1.0-preview` | Verfügbar | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Verfügbar | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Die folgende Liste enthält die verfügbaren APIs für die jeweilige Version.

API | 1.0-preview | 1.0
--- | --- | ---
[Übertragen an alle](#broadcast) | **&#x2713;** | **&#x2713;**
[Übertragen an eine Gruppe](#broadcast-group) | **&#x2713;** | **&#x2713;**
Übertragen an einige Gruppen | **&#x2713;** (Veraltet) | `N / A`
[Senden an einen Benutzer](#send-user) | **&#x2713;** | **&#x2713;**
Senden an einige Benutzer | **&#x2713;** (Veraltet) | `N / A`
[Hinzufügen eines Benutzers zu einer Gruppe](#add-user-to-group) | `N / A` | **&#x2713;**
[Entfernen eines Benutzers aus einer Gruppe](#remove-user-from-group) | `N / A` | **&#x2713;**
[Überprüfen, ob Benutzer vorhanden sind](#check-user-existence) | `N / A` | **&#x2713;**
[Entfernen eines Benutzers aus allen Gruppen](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Senden an eine Verbindung](#send-connection) | `N / A` | **&#x2713;**
[Hinzufügen einer Verbindung zu einer Gruppe](#add-connection-to-group) | `N / A` | **&#x2713;**
[Entfernen einer Verbindung aus einer Gruppe](#remove-connection-from-group) | `N / A` | **&#x2713;**
[Schließen einer Clientverbindung](#close-connection) | `N / A` | **&#x2713;**
[Dienstintegrität](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Übertragen an alle

Version | API-HTTP-Methode | Anfrage-URL | Anforderungstext
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Wie oben

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Übertragen an eine Gruppe

Version | API-HTTP-Methode | Anfrage-URL | Anforderungstext
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Wie oben

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>Senden an einen Benutzer

Version | API-HTTP-Methode | Anfrage-URL | Anforderungstext
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Wie oben

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Hinzufügen eines Benutzers zu einer Gruppe

Version | API-HTTP-Methode | Anfrage-URL
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Entfernen eines Benutzers aus einer Gruppe

Version | API-HTTP-Methode | Anfrage-URL
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Überprüfen, ob Benutzer in einer Gruppe vorhanden sind

API-Version | API-HTTP-Methode | Anfrage-URL
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Statuscode der Antwort | BESCHREIBUNG
---|---
`200` | Benutzer vorhanden
`404` | Benutzer nicht vorhanden

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Entfernen eines Benutzers aus allen Gruppen

API-Version | API-HTTP-Methode | Anfrage-URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Senden einer Nachricht an eine Verbindung

API-Version | API-HTTP-Methode | Anfrage-URL | Anforderungstext
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Hinzufügen einer Verbindung zu einer Gruppe

API-Version | API-HTTP-Methode | Anfrage-URL
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Entfernen einer Verbindung aus einer Gruppe

API-Version | API-HTTP-Methode | Anfrage-URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>Schließen einer Clientverbindung

API-Version | API-HTTP-Methode | Anfrage-URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Dienstintegrität

API-Version | API-HTTP-Methode | Anfrage-URL
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Statuscode der Antwort | BESCHREIBUNG
---|---
`200` | Dienst fehlerfrei
`5xx` | Dienstfehler

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsapi).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsapi).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie mithilfe der REST-API eine Echtzeitnachricht von SignalR Service an Clients übertragen. Im nächsten Artikel erfahren Sie mehr über die Entwicklung und Bereitstellung von Azure-Funktionen mit SignalR Service-Bindung auf der Grundlage der REST-API.

> [!div class="nextstepaction"]
> [Entwickeln von Azure Functions mithilfe von Azure SignalR Service-Bindungen](signalr-quickstart-azure-functions-csharp.md)
