---
title: Herstellen einer Verbindung zwischen Ihrer Java-Funktion und Azure Storage
description: Hier erfahren Sie, wie Sie eine per HTTP ausgelöste Java-Funktion mithilfe einer Queue Storage-Ausgabebindung mit Azure Storage verbinden.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: b4381c4acbea8a3b7d86d9c32aaf9cea24cf15fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422653"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Herstellen einer Verbindung zwischen Ihrer Java-Funktion und Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In diesem Artikel erfahren Sie, wie Sie die Funktion, die Sie im [vorherigen Schnellstartartikel](./create-first-function-cli-java.md?tabs=bash,browser) erstellt haben, mit einer Azure Storage-Warteschlange integrieren. Die Ausgabebindung, die Sie dieser Funktion hinzufügen, schreibt Daten aus einer HTTP-Anforderung in eine Nachricht in der Warteschlange.

Die meisten Bindungen erfordern eine gespeicherte Verbindungszeichenfolge, die Functions verwendet, um auf den gebundenen Dienst zuzugreifen. Um diese Verbindung zu vereinfachen, verwenden Sie das Storage-Konto, das Sie mit Ihrer Funktions-App erstellt haben. Die Verbindung mit diesem Konto ist bereits in einer App-Einstellung namens `AzureWebJobsStorage` gespeichert.  

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Schritte in [Teil 1 der Java-Schnellstartanleitung](./create-first-function-cli-java.md?tabs=bash,browser) aus, bevor Sie mit diesem Artikel beginnen.

## <a name="download-the-function-app-settings"></a>Herunterladen der Funktions-App-Einstellungen

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Aktivieren von Erweiterungsbundles

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Jetzt können Sie dem Projekt die Storage-Ausgabebindung hinzufügen.

## <a name="add-an-output-binding"></a>Hinzufügen einer Ausgabebindung

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Hinzufügen von Code, der die Ausgabebindung verwendet

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Nun können Sie die neue Ausgabebindung lokal testen.

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

Verwenden Sie wie zuvor den folgenden Befehl, um das Projekt zu erstellen und die Functions-Runtime lokal zu starten:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Da Sie Erweiterungsbündel in der Datei „host.json“ aktiviert haben, wurde die [Storage-Bindungserweiterung](functions-bindings-storage-blob.md#add-to-your-functions-app) zusammen mit den übrigen Microsoft-Bindungserweiterungen während des Starts für Sie heruntergeladen und installiert.

Lösen Sie die Funktion wie zuvor über die Befehlszeile aus. Verwenden Sie dazu cURL in einem neuen Terminalfenster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Diesmal erstellt die Ausgabebindung außerdem eine Warteschlange namens `outqueue` in Ihrem Speicherkonto und fügt eine Nachricht mit dieser selben Zeichenfolge hinzu.

Als Nächstes verwenden Sie die Azure CLI, um die neue Warteschlange anzuzeigen und sicherzustellen, dass eine Nachricht hinzugefügt wurde. Sie können Ihre Warteschlange auch mithilfe des [Microsoft Azure Storage-Explorers][Azure Storage Explorer] oder im [Azure-Portal](https://portal.azure.com) anzeigen.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Erneutes Bereitstellen des Projekts 

Führen Sie zum Aktualisieren Ihrer veröffentlichten App den folgenden Befehl erneut aus:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Sie können auch wieder cURL verwenden, um die bereitgestellte Funktion zu testen. Übergeben Sie wie zuvor den Wert `AzureFunctions` im Text der POST-Anforderung an die URL. Beispiel:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Sie können [die Storage-Warteschlangennachricht erneut untersuchen](#query-the-storage-queue), um zu überprüfen, ob die Ausgabebindung wie erwartet eine neue Nachricht in der Warteschlange generiert.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben Ihre mittels HTTP ausgelöste Funktion so aktualisiert, dass sie Daten in eine Storage-Warteschlange schreibt. Weitere Informationen zum Entwickeln von Azure-Funktionen mit Java finden Sie im [Java-Entwicklerhandbuch für Azure Functions](functions-reference-java.md) sowie unter [Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md). Beispiele für vollständige Funktionsprojekte in Java finden Sie in den [Functions-Beispielen für Java](/samples/browse/?products=azure-functions&languages=Java). 

Als Nächstes sollten Sie die Application Insights-Überwachung für Ihre Funktions-App aktivieren:

> [!div class="nextstepaction"]
> [Aktivieren der Application Insights-Integration](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/
