---
title: Entwickeln von Azure Functions mithilfe von Visual Studio| Microsoft-Dokumentation
description: In diesem Artikel erhalten Sie Informationen über das Entwickeln und Testen von Azure-Funktionen mithilfe von Azure Functions-Tools für Visual Studio 2019.
services: functions
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: glenga
ms.openlocfilehash: 4366f09ccc9a3b2335e0aa84b7fb7398825cb87e
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864530"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Entwickeln von Azure Functions mithilfe von Visual Studio  

Azure Functions-Tools für Visual Studio 2019 ist eine Erweiterung für Visual Studio, mit der Sie C#-Funktionen entwickeln, testen und in Azure bereitstellen können. Wenn Sie zum ersten Mal mit Azure Functions arbeiten, finden Sie unter [Einführung in Azure Functions](functions-overview.md) weitere Informationen.

Azure Functions-Tools bieten folgende Vorteile: 

* Bearbeiten, Erstellen und Ausführen von Funktionen auf Ihrem lokalen Entwicklungscomputer. 
* Veröffentlichen von Azure Functions-Projekten direkt in Azure. 
* Verwenden von WebJobs-Attributen zum Deklarieren von Funktionsbindungen direkt in C#-Code. Sie müssen keine separate function.json-Datei für Bindungsdefinitionen mehr beibehalten.
* Entwickeln und Bereitstellen vorkompilierter C#-Funktionen. Vorkompilierte Funktionen bieten eine bessere Leistung beim Kaltstart als auf skriptbasierende Funktionen von C#. 
* Codieren Ihrer Funktionen in C# während Sie von allen Vorteilen der Entwicklung mit Visual Studio profitieren. 

Dieser Artikel erläutert detailliert, wie Sie die Azure Functions-Tools für Visual Studio 2019 verwenden, um C#-Funktionen zu entwickeln und in Azure zu veröffentlichen. Bevor Sie diesen Artikel lesen, sollten Sie die Schnellstartanleitung [Erstellen Ihrer ersten Funktion mit Visual Studio](functions-create-your-first-function-visual-studio.md) durcharbeiten. 

> [!IMPORTANT]
> Kombinieren Sie die lokale Entwicklung und die Portalentwicklung nicht in der gleichen Funktions-App. Bei der Veröffentlichung aus einem lokalen Projekt in einer Funktions-App überschreibt der Bereitstellungsprozess alle Funktionen, die Sie im Portal entwickelt haben.

## <a name="prerequisites"></a>Voraussetzungen

Azure Functions-Tools sind in der „Azure-Entwicklung“-Workload von [Visual Studio 2017](https://www.visualstudio.com/vs/) oder einer höheren Version enthalten. Stellen Sie sicher, dass Sie die **Azure-Entwicklung**-Workload in die Installation von Visual Studio 2019 einbinden:

![Installieren von Visual Studio 2019 mit der Workload „Azure-Entwicklung“](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Stellen Sie sicher, dass Ihre Visual Studio-Version auf dem neuesten Stand ist und Sie die [neueste Version](#check-your-tools-version) der Azure Functions-Tools verwenden.

### <a name="other-requirements"></a>Sonstige Anforderungen

Zum Erstellen und Bereitstellen von Funktionen benötigen Sie ebenso:

* Ein aktives Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement haben, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

* Azure Storage-Konto Informationen zum Erstellen eines Speicherkontos finden Sie unter [Erstellen Sie ein Speicherkonto](../storage/common/storage-quickstart-create-account.md).

### <a name="check-your-tools-version"></a>Überprüfen Ihrer Tools-Version

1. Wählen Sie im Menü **Extras** auf **Erweiterungen und Updates**. Erweitern Sie **Installierte** > **Tools**, und wählen Sie **Azure Functions und Webauftragstools**.

    ![Überprüfen der Functions-Toolsversion](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Notieren Sie sich die installierte **Version**. Sie können dann diese Version mit der aktuellen Version vergleichen, die [in den Versionshinweisen](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md) aufgeführt ist. 

3. Wenn Ihre Version älter ist, aktualisieren Sie Ihre Tools in Visual Studio, wie im folgenden Abschnitt gezeigt.

### <a name="update-your-tools"></a>Aktualisieren Ihrer Tools

1. Erweitern Sie im Dialogfeld **Erweiterungen und Updates** die Option **Updates** > **Visual Studio Marketplace**, wählen Sie **Azure Functions und Webauftragstools**, und wählen Sie **Aktualisieren**.

    ![Aktualisieren der Functions-Toolsversion](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Nachdem das Toolupdate heruntergeladen wurde, schließen Sie Visual Studio, damit die Tools mit dem VSIX-Installationsprogramm aktualisiert werden.

3. Wählen Sie im Installationsprogramm **OK**, um den Vorgang zu starten, und dann **Ändern**, um die Tools zu aktualisieren. 

4. Nachdem das Update abgeschlossen ist, wählen Sie **Schließen** und starten Visual Studio neu.

## <a name="create-an-azure-functions-project"></a>Erstellen eines Azure Functions-Projekts

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Die Projektvorlage erstellt ein C#-Projekt, installiert das NuGet-Paket `Microsoft.NET.Sdk.Functions` und legt das Zielframework fest. Functions 1.x ist für .NET Framework ausgelegt, und Functions 2.x ist für .NET Standard ausgelegt. Das neue Projekt enthält die folgenden Dateien:

* **host.json**: Ermöglicht das Konfigurieren des Functions-Hosts. Diese Einstellungen gelten für die lokale Ausführung und die Ausführung in Azure. Weitere Informationen finden Sie in der [host.json-Referenz](functions-host-json.md).

* **local.settings.json**: Behält Einstellungen beim lokalen Ausführen von Funktionen bei. Diese Einstellungen werden nicht von Azure benutzt, sondern von den [Azure Functions Core-Tools](functions-run-local.md). Verwenden Sie diese Datei zum Angeben von App-Einstellungen für Umgebungsvariablen, die für Ihre Funktionen erforderlich sind. Fügen Sie dem Array **Values** für jede Verbindung, die für die Funktionsbindungen in Ihrem Projekt erforderlich sind, ein neues Element hinzu. Weitere Informationen finden Sie unter [Datei für lokale Einstellungen](functions-run-local.md#local-settings-file) im Artikel zu Azure Functions Core Tools.

    >[!IMPORTANT]
    >Da die Datei „local.settings.json“ Geheimnisse enthalten kann, müssen Sie sie aus der Quellcodeverwaltung Ihres Projekts ausschließen. Die Einstellung **In Ausgabeverzeichnis kopieren** für diese Datei sollte immer **Kopieren, wenn neuer** lauten. 

Weitere Informationen finden Sie unter [Funktionsklassenbibliotheks-Projekt](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>Konfigurieren des Projekts für die lokale Entwicklung

Die Functions-Laufzeit verwendet intern ein Azure-Speicherkonto. Sie müssen für alle Triggertypen außer HTTP und Webhooks den Schlüssel **Values.AzureWebJobsStorage** auf eine gültige Verbindungszeichenfolge des Azure-Speicherkontos festlegen. Ihre Funktions-App kann auch den [Azure-Speicheremulator](../storage/common/storage-use-emulator.md) für die Verbindungseinstellung **AzureWebJobsStorage** verwenden, die für das Projekt erforderlich ist. Um den Emulator zu verwenden, legen Sie den Wert von **AzureWebJobsStorage** auf `UseDevelopmentStorage=true` fest. Sie müssen diese Einstellung vor der Bereitstellung in eine tatsächliche Speicherverbindung ändern.

So legen Sie die Speicherkonto-Verbindungszeichenfolge fest:

1. Öffnen Sie in Visual Studio den **Cloud-Explorer**, erweitern Sie **Speicherkonto** > **Ihr Speicherkonto**, wählen Sie **Eigenschaften** aus, und kopieren Sie dann den Wert von **Primäre Verbindungszeichenfolge**.

2. Öffnen Sie in Ihrem Projekt die Datei „local.settings.json“, und legen Sie den Wert des Schlüssels **AzureWebJobsStorage** auf die kopierte Verbindungszeichenfolge fest.

3. Wiederholen Sie den vorherigen Schritt zum Hinzufügen von eindeutigen Schlüsseln zum **Values**-Array für alle anderen Verbindungen, die von Ihren Funktionen benötigt werden.

## <a name="add-a-function-to-your-project"></a>Hinzufügen einer Funktion zu Ihrem Projekt

In vorab kompilierten Funktionen werden die von der Funktion verwendeten Bindungen durch Anwendung von Attributen im Code definiert. Bei der Verwendung von Azure Functions-Tools zum Erstellen von Funktionen aus den bereitgestellten Vorlagen werden diese Attribute für Sie angewendet. 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Hinzufügen** > **Neues Element** aus. Wählen Sie **Azure Function** aus, geben Sie einen **Namen** für die Klasse ein, und klicken Sie auf **Hinzufügen**.

2. Wählen Sie den Trigger aus, legen Sie die Bindungseigenschaften fest, und klicken Sie auf **Erstellen**. Im folgenden Beispiel werden die Einstellungen beim Erstellen einer Funktion gezeigt, die durch Queue Storage ausgelöst wird. 

    ![Erstellen einer Funktion mit Auslösung per Warteschlange](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    In diesem Triggerbeispiel wird eine Verbindungszeichenfolge mit einem Schlüssel namens **QueueStorage** verwendet. Diese Einstellung für die Verbindungszeichenfolge muss in der Datei [local.settings.json](functions-run-local.md#local-settings-file) definiert werden.

3. Untersuchen Sie die neu hinzugefügte Klasse. Es wird eine statische **Run**-Methode angezeigt, der das Attribut **FunctionName** zugewiesen wird. Dieses Attribut gibt an, dass die Methode den Einstiegspunkt für die Funktion darstellt.

    Die folgende C#-Klasse stellt beispielsweise eine einfache Funktion dar, die durch Queue Storage ausgelöst wird:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```
    Ein bindungsspezifisches Attribut wird auf jeden Bindungsparameter angewendet, der der Einstiegspunktmethode bereitgestellt wird. Das Attribut verwendet die Bindungsinformationen als Parameter. Im vorherigen Beispiel wurde auf den ersten Parameter ein **QueueTrigger**-Attribut angewendet, das die durch eine Warteschlange ausgelöste Funktion angab. Die Namen der Warteschlange und der Einstellung für die Verbindungszeichenfolge werden als Parameter an das Attribut **QueueTrigger** übergeben. Weitere Informationen finden Sie unter [Azure Queue Storage-Bindungen für Azure Functions](functions-bindings-storage-queue.md#trigger---c-example).
    
Sie können das obige Verfahren nutzen, um Ihrem Funktions-App-Projekt weitere Funktionen hinzuzufügen. Jede Funktion im Projekt kann über einen anderen Trigger verfügen, aber einer Funktion muss genau ein Trigger zugeordnet sein. Weitere Informationen finden Sie unter [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Hinzufügen von Bindungen

Wie bei Triggern auch, werden Eingabe- und Ausgabebindungen Ihrer Funktion als Bindungsattribute hinzugefügt. Fügen Sie Bindungen einer Funktion wie folgt hinzu:

1. Stellen Sie sicher, dass Sie [das Projekt für die lokale Entwicklung konfiguriert haben](#configure-the-project-for-local-development).

2. Fügen Sie für die entsprechende Bindung das geeignete NuGet-Erweiterungspaket hinzu. Weitere Informationen finden Sie im Artikel zu Triggern und Bindungen unter [Lokale Entwicklung in C# mit Visual Studio oder Visual Studio Code](./functions-bindings-register.md#local-csharp). Die bindungsspezifischen Anforderungen für NuGet-Pakete sind im Referenzartikel für die Bindung angegeben. Paketanforderungen für den Event Hubs-Trigger finden Sie im [Referenzartikel zu Event Hubs-Bindungen](functions-bindings-event-hubs.md).

3. Falls für die Bindung bestimmte App-Einstellungen erforderlich sind, können Sie diese der Sammlung **Values** in der [Datei mit lokalen Einstellungen](functions-run-local.md#local-settings-file) hinzufügen. Diese Werte werden verwendet, wenn die Funktion lokal ausgeführt wird. Wenn die Funktion in der Funktions-App in Azure ausgeführt wird, werden die [Einstellungen für Funktions-Apps](#function-app-settings) verwendet.

4. Fügen Sie der Methodensignatur das entsprechende Bindungsattribut hinzu. Im folgenden Beispiel löst eine Warteschlangennachricht die Funktion aus, und die Ausgabebindung erstellt eine neue Warteschlangennachricht mit demselben Text in einer anderen Warteschlange.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   Die Verbindung mit dem Warteschlangenspeicher stammt aus der Einstellung `AzureWebJobsStorage`. Weitere Informationen finden Sie im Referenzartikel für die spezifische Bindung. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testen von Funktionen

Mit Azure Functions Core-Tools können Sie ein Azure Functions-Projekt auf dem lokalen Entwicklungscomputer ausführen. Sie werden beim ersten Starten einer Funktion in Visual Studio zum Installieren dieser Tools aufgefordert.

Drücken Sie F5, um Ihre Funktion zu testen. Akzeptieren Sie die entsprechende Aufforderung von Visual Studio zum Herunterladen und Installieren der Azure Functions Core (CLI)-Tools. Sie müssen möglicherweise auch eine Firewallausnahme aktivieren, damit die Tools HTTP-Anforderungen verarbeiten können.

Wenn das Projekt ausgeführt wird, können Sie Ihren Code wie eine bereitgestellte Funktion testen. Weitere Informationen finden Sie unter [Strategien zum Testen Ihres Codes in Azure Functions](functions-test-a-function.md). Bei der Ausführung im Debug-Modus werden in Visual Studio wie erwartet Haltepunkte erreicht. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Weitere Informationen über die Verwendung der Azure Functions Core-Tools finden Sie unter [Lokales Codieren und Testen von Azure-Funktionen](functions-run-local.md).

## <a name="publish-to-azure"></a>Veröffentlichen in Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Einstellungen für Funktions-Apps

Alle Einstellungen, die Sie in „local.settings.json“ hinzugefügt haben, müssen auch der Funktions-App in Azure hinzugefügt werden. Diese Einstellungen werden nicht automatisch hochgeladen, wenn Sie das Projekt veröffentlichen.

Die einfachste Möglichkeit zum Hochladen der erforderlichen Einstellungen in Ihre Funktions-App in Azure ist die Verwendung des Links **Anwendungseinstellungen verwalten**, der angezeigt wird, nachdem Sie das Projekt veröffentlicht haben.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Dadurch wird das Dialogfeld **Anwendungseinstellungen** für die Funktions-App angezeigt, in dem Sie neue Anwendungseinstellungen hinzufügen oder vorhandene ändern können.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Lokal** stellt einen Einstellungswert in der Datei „local.settings.json“ dar, und **Remote** ist die aktuelle Einstellung in der Funktions-App in Azure.  Wählen Sie **Einstellung hinzufügen**, um eine neue App-Einstellung zu erstellen. Verwenden Sie den Link **Wert aus lokaler Quelle einfügen**, um einen Einstellungswert in das Feld **Remote** zu kopieren. Ausstehende Änderungen werden in die Datei für lokale Einstellungen und die Funktions-App geschrieben, wenn Sie **OK** auswählen.

Sie können die Anwendungseinstellungen auch folgendermaßen verwalten:

* [Mit dem Azure-Portal](functions-how-to-use-azure-function-app-settings.md#settings)
* [Mit der `--publish-local-settings` Option „Veröffentlichen“ in Azure Functions Core Tools](functions-run-local.md#publish)
* [Mit der Azure-Befehlszeilenschnittstelle](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)

## <a name="monitoring-functions"></a>Überwachen von Funktionen

Die empfohlene Methode zum Überwachen der Ausführung Ihrer Funktionen ist die Integration Ihrer Funktions-App in Azure Application Insights. Wenn Sie eine Funktions-App im Azure-Portal erstellen, wird diese Integration standardmäßig für Sie erledigt. Wenn Sie Ihre Funktions-App während der Veröffentlichung in Visual Studio erstellen, erfolgt die Integration Ihrer Funktions-App in Azure nicht.

So aktivieren Sie Application Insights für Ihre Funktions-App:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Functions Core-Tools finden Sie unter [Lokales Codieren und Testen von Azure-Funktionen](functions-run-local.md).

Weitere Informationen zum Entwickeln von Funktionen als .NET-Klassenbibliotheken finden Sie unter [C#-Entwicklerreferenz zu Azure Functions](functions-dotnet-class-library.md). Dieser Artikel enthält auch Links zu Beispielen für die Verwendung von Attributen zum Deklarieren der verschiedenen Typen von Bindungen, die von Azure Functions unterstützt werden.    
