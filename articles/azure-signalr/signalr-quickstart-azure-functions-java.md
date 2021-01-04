---
title: Erstellen eines Chatraums mit Azure Functions und SignalR Service mithilfe von Java
description: Eine Schnellstartanleitung für die Verwendung des Azure SignalR-Diensts und von Azure Functions zum Erstellen eines Chatraums mithilfe von Java.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: 62f31d8426e86cc20e957f3a47b30109dee0a634
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185673"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Schnellstart: Erstellen eines Chatraums mit Azure Functions und SignalR Service mithilfe von Java

Mit Azure SignalR Service können Sie Ihrer Anwendung ganz einfach Echtzeitfunktionen hinzufügen. Azure Functions ist eine serverlose Plattform, mit der Sie Ihren Code ohne Verwaltung von Infrastruktur ausführen können. In dieser Schnellstartanleitung verwenden Sie Java, um eine serverlose Echtzeit-Chatanwendung mithilfe von SignalR Service und Functions zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/)
- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Dient zum lokalen Ausführen von Azure-Funktions-Apps.

   > [!NOTE]
   > Die erforderlichen SignalR Service-Bindungen in Java werden nur in Version 2.4.419 von Azure Function Core Tools (Hostversion 2.0.12332) oder höher unterstützt.

   > [!NOTE]
   > Für die Installation von Erweiterungen erfordert Azure Functions Core Tools die Installation des [.NET Core SDK](https://www.microsoft.com/net/download). Allerdings sind keine Kenntnisse von .NET erforderlich, um JavaScript Azure Functions-Apps zu erstellen.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), Version 8
- [Apache Maven](https://maven.apache.org), Version 3.0 oder höher

> [!NOTE]
> Dieser Schnellstart kann unter MacOS, Windows oder Linux ausgeführt werden.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter <https://portal.azure.com/> mit Ihrem Azure-Konto beim Azure-Portal an.

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurieren und Ausführen der Azure Functions-App

1. Bestätigen Sie im Browser, in dem das Azure-Portal geöffnet ist, dass die von Ihnen zuvor bereitgestellte SignalR-Dienstinstanz erfolgreich erstellt wurde, indem Sie nach ihrem Namen im Suchfeld oben im Portal suchen. Wählen Sie die Instanz aus, um sie zu öffnen.

    ![Suchen nach der SignalR-Dienstinstanz](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Wählen Sie **Schlüssel** aus, um die Verbindungszeichenfolgen für die SignalR-Dienstinstanz anzuzeigen.

1. Wählen Sie die primäre Verbindungszeichenfolge aus, und kopieren Sie diese.

    ![Erstellen des SignalR-Diensts](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Öffnen Sie in Ihrem Code-Editor den Ordner *src/chat/java* im geklonten Repository.

1. Benennen Sie *local.settings.sample.json* in *local.settings.json* um.

1. Fügen Sie in **local.settings.json** die Verbindungszeichenfolge in den Wert der **AzureSignalRConnectionString**-Einstellung ein. Speichern Sie die Datei .

1. Die Hauptdatei, die die Funktionen enthält, befindet sich unter *src/chat/java/src/main/java/com/function/Functions.java*:

    - **negotiate**: Verwendet die *SignalRConnectionInfo*-Eingabebindung, um gültige Verbindungsinformationen zu generieren und zurückzugeben.
    - **sendMessages**: Empfängt eine Chatnachricht im Anforderungstext und verwendet die *SignalR*-Ausgabebindung, um die Nachricht an alle verbundenen Clientanwendungen zu senden.

1. Stellen Sie im Terminal sicher, dass Sie sich im Ordner *src/chat/java* befinden. Erstellen Sie die Funktions-App.

    ```bash
    mvn clean package
    ```

1. Führen Sie die Funktions-App lokal aus.

    ```bash
    mvn azure-functions:run
    ```
    
Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Treten Probleme auf? Verwenden Sie den [Leitfaden zur Problembehandlung](signalr-howto-troubleshoot-guide.md), oder [informieren Sie uns](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine serverlose Echtzeitanwendung mithilfe von Maven erstellt und ausgeführt. Als Nächstes erfahren Sie, wie Sie Java Azure Functions von Grund auf erstellen.

> [!div class="nextstepaction"]
> [Erstellen Ihrer ersten Funktion mit Java und Maven](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)