---
title: Erstellen Ihrer ersten Funktion in Azure mit Visual Studio
description: Erstellen und veröffentlichen Sie eine durch HTTP ausgelöste Azure-Funktion mit Visual Studio.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, Compute, serverlose Architektur
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/17/2018
ms.author: glenga
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 1c07da0121b8e350c55b139901d56cfe2133386d
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864499"
---
# <a name="create-your-first-function-using-visual-studio"></a>Erstellen Ihrer ersten Funktion mit Visual Studio

Mit Azure Functions können Sie Code in einer [serverlosen](https://azure.microsoft.com/solutions/serverless/) Umgebung ausführen, ohne vorher eine VM erstellen oder eine Webanwendung veröffentlichen zu müssen.

In diesem Artikel erfahren Sie, wie Sie die Visual Studio 2019-Tools für Azure Functions verwenden, um eine „hello world“-Funktion lokal zu erstellen und zu testen. Anschließend veröffentlichen Sie den Funktionscode in Azure. Diese Tools sind als Teil der Azure-Entwicklungsworkload in Visual Studio 2019 verfügbar.

![localhost-Antwort der Funktion im Browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Dieses Thema enthält ein [Video](#watch-the-video) zu einigen grundlegenden Schritten.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Installieren Sie [Visual Studio 2019](https://azure.microsoft.com/downloads/), und vergewissern Sie sich, dass auch die Workload **Azure-Entwicklung** installiert ist.

* Stellen Sie sicher, dass Sie über die [neuesten Azure Functions-Tools](functions-develop-vs.md#check-your-tools-version) verfügen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Erstellen eines Funktionen-App-Projekts

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio erstellt ein Projekt, das eine Klasse mit den Codebausteinen für den gewählten Funktionstyp enthält. Mit dem **FunctionName**-Attribut der Methode wird der Name der Funktion festgelegt. Mit dem **HttpTrigger**-Attribut wird angegeben, dass die Funktion mit einer HTTP-Anforderung ausgelöst wird. Mit Code in den Codebausteinen wird eine HTTP-Antwort gesendet, die einen Wert aus dem Anforderungstext oder der Abfragezeichenfolge enthält. Sie können Eingabe- und Ausgabebindungen einer Funktion hinzufügen, indem Sie die geeigneten Attribute auf die Methode anwenden. Weitere Informationen finden Sie im Abschnitt [Trigger und Bindungen](functions-dotnet-class-library.md#triggers-and-bindings) in der [C#-Entwicklerreferenz zu Azure Functions](functions-dotnet-class-library.md).

Nachdem Sie nun Ihr Funktionsprojekt und eine Funktion mit HTTP-Trigger erstellt haben, können Sie sie auf Ihrem lokalen Computer testen.

## <a name="test-the-function-locally"></a>Lokales Testen der Funktion

Mit Azure Functions Core-Tools können Sie ein Azure Functions-Projekt auf dem lokalen Entwicklungscomputer ausführen. Sie werden beim ersten Starten einer Funktion in Visual Studio zum Installieren dieser Tools aufgefordert.

1. Drücken Sie F5, um Ihre Funktion zu testen. Akzeptieren Sie die entsprechende Aufforderung von Visual Studio zum Herunterladen und Installieren der Azure Functions Core (CLI)-Tools. Sie müssen möglicherweise auch eine Firewallausnahme aktivieren, damit die Tools HTTP-Anforderungen verarbeiten können.

2. Kopieren Sie die URL Ihrer Funktion aus der Azure Functions-Laufzeitausgabe.

    ![Lokale Azure-Laufzeit](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-debugging.png)

3. Fügen Sie die URL der HTTP-Anforderung in die Adresszeile des Browsers ein. Hängen Sie anschließend die Abfragezeichenfolge `?name=<YOUR_NAME>` an diese URL an, und führen Sie die Anforderung aus. Hier ist die Antwort des Browsers auf die von der Funktion zurückgegebene lokale GET-Anforderung abgebildet: 

    ![localhost-Antwort der Funktion im Browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local.png)

4. Drücken Sie **UMSCHALT+F5**, um das Debuggen zu beenden.

Nachdem Sie sichergestellt haben, dass die Funktion auf Ihrem lokalen Computer richtig ausgeführt wird, können Sie das Projekt in Azure veröffentlichen.

## <a name="publish-the-project-to-azure"></a>Veröffentlichen des Projekts in Azure

Sie müssen in Ihrem Azure-Abonnement über eine Funktions-App verfügen, bevor Sie Ihr Projekt veröffentlichen können. Sie können eine Funktions-App direkt in Visual Studio erstellen.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testen der Funktion in Azure

1. Kopieren Sie die Basis-URL der Funktions-App von der Seite „Veröffentlichungsprofil“. Ersetzen Sie den Teil `localhost:port` der URL, die Sie beim lokalen Testen der Funktion verwendet haben, durch die neue Basis-URL. Stellen Sie wie zuvor sicher, dass Sie die Abfragezeichenfolge `?name=<YOUR_NAME>` an diese URL anhängen und die Anforderung ausführen.

    Die URL, über die Ihre per HTTP ausgelöste Funktion aufgerufen wird, sollte das folgende Format haben:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Fügen Sie diese neue URL für die HTTP-Anforderung in die Adresszeile des Browsers ein. Hier ist die Antwort des Browsers auf die von der Funktion zurückgegebene GET-Remoteanforderung abgebildet:

    ![Funktionsantwort im Browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="watch-the-video"></a>Video ansehen

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

## <a name="next-steps"></a>Nächste Schritte

Sie haben Visual Studio genutzt, um eine C#-Funktions-App mit einer einfachen Funktion zu erstellen und zu veröffentlichen, die per HTTP ausgelöst wird.

* Informationen zum Hinzufügen von Eingabe- und Ausgabebindungen mit Integration in andere Dienste finden Sie [hier](functions-develop-vs.md#add-bindings).
* Weitere Informationen zum Entwickeln von Funktionen als .NET Klassenbibliotheken finden Sie [hier](functions-dotnet-class-library.md).
