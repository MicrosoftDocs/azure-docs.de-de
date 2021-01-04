---
title: Entwickeln mit ASP.NET – Azure SignalR Service
description: Eine Schnellstartanleitung zur Verwendung von Azure SignalR Service zum Erstellen eines Chatraums mit dem ASP.NET-Framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: c39ef505b0cea0ad0c03b81683db8441077cd0d2
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874541"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Schnellstart: Erstellen eines Chatraums mit ASP.NET und SignalR Service

Azure SignalR Service basiert auf [SignalR für ASP.NET Core 2.1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1). Diese Version ist **nicht** zu 100 % kompatibel mit ASP.NET SignalR. In Azure SignalR Service wurde das ASP.NET SignalR-Datenprotokoll basierend auf den neuesten ASP.NET Core-Technologien neu implementiert. Bei der Verwendung von Azure SignalR Service für ASP.NET SignalR werden einige ASP.NET SignalR-Funktionen nicht mehr unterstützt, z.B. gibt Azure SignalR keine Nachrichten wieder, wenn der Client erneut eine Verbindung herstellt. Der Forever Frame-Transport und JSONP werden ebenfalls nicht unterstützt. Einige Codeänderungen und die richtige Version der abhängigen Bibliotheken sind erforderlich, damit die ASP.NET SignalR-Anwendung mit SignalR Service funktioniert.

Eine vollständige Liste des Funktionsvergleichs zwischen ASP.NET SignalR und ASP.NET Core SignalR finden Sie im [Dokument zu den Versionsunterschieden](/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1).

In diesem Schnellstart erfahren Sie, wie Sie ASP.NET und Azure SignalR Service für eine ähnliche [Chatraumanwendung](./signalr-quickstart-dotnet-core.md) verwenden.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsnet).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsnet).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Der *serverlose* Modus wird für ASP.NET SignalR-Anwendungen nicht unterstützt. Verwenden Sie immer *Standard* oder *Klassisch* für die Azure SignalR Service-Instanz.

Sie können die in diesem Schnellstart verwendeten Azure-Ressourcen auch mit [Erstellen eines SignalR Service-Skripts](scripts/signalr-cli-create-service.md) erstellen.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsnet).

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Während der Dienst bereitgestellt wird, arbeiten wird mit dem Code. Klonen Sie die [Beispiel-App von GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), legen Sie die Verbindungszeichenfolge des SignalR-Diensts fest, und führen Sie die Anwendung lokal aus.

1. Öffnen Sie ein Git-Terminalfenster. Wechseln Sie in einen Ordner, in dem Sie das Beispielprojekt klonen möchten.

1. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsnet).

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurieren und Ausführen der Chatraum-Web-App

1. Starten Sie Visual Studio, und öffnen Sie die Projektmappe im Ordner *aspnet-samples/ChatRoom/* des geklonten Repositorys.

1. Suchen Sie im Browser, in dem das Azure-Portal geöffnet ist, nach der von Ihnen erstellten Instanz, und wählen Sie diese aus.

1. Wählen Sie **Schlüssel** aus, um die Verbindungszeichenfolgen für die SignalR-Dienstinstanz anzuzeigen.

1. Wählen Sie die primäre Verbindungszeichenfolge aus, und kopieren Sie diese.

1. Legen Sie nun die Verbindungszeichenfolge in der Datei *web.config* fest.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. In *Startup.cs* müssen Sie anstelle von `MapSignalR()``MapAzureSignalR({YourApplicationName})` aufrufen und die Verbindungszeichenfolge übergeben, damit die Anwendung eine Verbindung mit dem Dienst herstellt, anstatt SignalR selbst zu hosten. Ersetzen Sie `{YourApplicationName}` durch den Namen Ihrer Anwendung. Dieser Name ist ein eindeutiger Name, um diese Anwendung von Ihren anderen Anwendungen zu unterscheiden. Sie können `this.GetType().FullName` als Wert verwenden.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Sie müssen auch auf das Dienst-SDK verweisen, bevor Sie diese APIs verwenden. Öffnen Sie **Extras | NuGet-Paket-Manager | Paket-Manager-Konsole**, und führen Sie den folgenden Befehl aus:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Abgesehen von diesen Änderungen bleibt alles andere gleich. Sie können noch immer die Hubschnittstelle verwenden, mit der Sie bereits vertraut sind, um Geschäftslogik zu schreiben.

    > [!NOTE]
    > In der Implementierung wird ein Endpunkt `/signalr/negotiate` für die Aushandlung durch das Azure SignalR Service SDK bereitgestellt. Er gibt eine spezielle Aushandlungsantwort zurück, wenn Clients versuchen, eine Verbindung herzustellen, und leitet Clients an den in der Verbindungszeichenfolge definierten Dienstendpunkt weiter.

1. Drücken Sie <kbd>F5</kbd>, um das Projekt im Debugmodus auszuführen. Sie können erkennen, dass die Anwendung lokal ausgeführt wird. Anstatt eine SignalR-Laufzeit durch die Anwendung selbst zu hosten, wird nun eine Verbindung mit Azure SignalR Service hergestellt.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsnet).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden also dauerhaft gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. Falls Sie die Ressourcen zum Hosten dieses Beispiels in einer vorhandenen Ressourcengruppe erstellt haben, die beizubehaltende Ressourcen enthält, können Sie die Ressourcen einzeln über das jeweilige Blatt löschen, statt die Ressourcengruppe zu löschen.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.

Geben Sie im Textfeld **Nach Name filtern...** den Namen Ihrer Ressourcengruppe ein. In dieser Schnellstartanleitung wurde eine Ressourcengruppe mit dem Namen *SignalRTestResources* verwendet. Klicken Sie in Ihrer Ressourcengruppe in der Ergebnisliste auf **...** und dann auf **Ressourcengruppe löschen**.

![Löschen](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsnet).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine neue Azure SignalR Service-Ressource erstellt und diese mit einer ASP.NET-Web-App verwendet. Als nächstes erfahren Sie, wie Sie Echtzeitanwendungen mit Azure SignalR Service mit ASP.NET Core entwickeln.

> [!div class="nextstepaction"]
> [Azure SignalR Service mit ASP.NET Core](./signalr-quickstart-dotnet-core.md)
