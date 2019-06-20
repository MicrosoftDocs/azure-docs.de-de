---
title: Erstellen Ihrer ersten Funktion im Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre erste Azure-Funktion für die serverlose Ausführung mit dem Azure-Portal erstellen.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 4a49cb3de2dc36dcd933a0b31b6a55b838eb1336
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864581"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Erstellen Ihrer ersten Funktion im Azure-Portal

Mit Azure Functions können Sie Code in einer [serverlosen](https://azure.microsoft.com/solutions/serverless/) Umgebung ausführen, ohne vorher eine VM erstellen oder eine Webanwendung veröffentlichen zu müssen. In diesem Artikel erfahren Sie, wie Sie Azure Functions verwenden können, um eine „Hello World“-Funktion im Azure-Portal zu erstellen.

![Erstellen einer Funktionen-App im Azure-Portal](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> C#-Entwickler sollten für die [Erstellung ihrer ersten Funktion Visual Studio 2019](functions-create-your-first-function-visual-studio.md) statt das Portal in Erwägung ziehen. 

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter <https://portal.azure.com> mit Ihrem Azure-Konto beim Azure-Portal an.

## <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

Sie müssen über eine Funktionen-App verfügen, die die Ausführung Ihrer Funktionen in Azure hostet. Sie können mit einer Funktionen-App Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung und Freigabe von Ressourcen. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Erstellen Sie als Nächstes in der neuen Funktionen-App eine Funktion.

## <a name="create-function"></a>Erstellen einer Funktion mit Auslösung per HTTP

1. Erweitern Sie Ihre neue Funktions-App, klicken Sie neben **Functions** auf die Schaltfläche **+** , klicken Sie auf **Im Portal**, und klicken Sie anschließend auf **Weiter**.

    ![Functions-Schnellstartanleitung zum Auswählen einer Plattform.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Wählen Sie **Webhook + API** aus, und klicken Sie anschließend auf **Erstellen**.

    ![Schnellstart von Functions im Azure-Portal](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Eine Funktion wird unter Verwendung einer sprachspezifischen Vorlage für eine Funktion mit HTTP-Trigger erstellt.

Nun können Sie die neue Funktion ausführen, indem Sie eine HTTP-Anforderung senden.

## <a name="test-the-function"></a>Testen der Funktion

1. Klicken Sie in der neuen Funktion rechts oben auf **</> Funktions-URL abrufen**, wählen Sie **default (Function key)** (Standard (Funktionsschlüssel)) aus, und klicken Sie dann auf **Kopieren**. 

    ![Kopieren der URL der Funktion aus dem Azure-Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Fügen Sie die URL der Funktion in die Adressleiste Ihres Browsers ein. Fügen Sie den Wert der Abfragezeichenfolge `&name=<yourname>` am Ende der URL hinzu, und drücken Sie die Taste `Enter` auf Ihrer Tastatur, um die Anforderung auszuführen. Daraufhin sollte die von der Funktion zurückgegebene Antwort im Browser angezeigt werden.  

    Das folgende Beispiel zeigt die Antwort im Browser:

    ![Funktionsantwort im Browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Die Anfrage-URL enthält einen Schlüssel, der standardmäßig über HTTP auf Ihre Funktion zugreifen muss.

3. Wenn Ihre Funktion ausgeführt wird, werden Nachverfolgungsinformationen in die Protokolle geschrieben. Kehren Sie zu Ihrer Funktion im Portal zurück, und klicken Sie am unteren Bildschirmrand auf den Pfeil, um **Protokolle** zu erweitern und die Nachverfolgungsausgabe der vorherigen Ausführung anzuzeigen.

   ![Viewer der Funktionsprotokolle im Azure-Portal](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine Funktions-App mit einer einfachen Funktion erstellt, die per HTTP ausgelöst wird.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Weitere Informationen finden Sie unter [HTTP- und Webhookbindungen in Azure Functions](functions-bindings-http-webhook.md).
