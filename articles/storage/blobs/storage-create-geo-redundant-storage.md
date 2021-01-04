---
title: 'Tutorial: Erstellen einer hochverfügbaren Anwendung mit Blob Storage'
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie geozonenredundanten Speicher mit Lesezugriff (RA-GZRS) verwenden, um Ihre Anwendungsdaten hochverfügbar zu machen.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc, devx-track-python, devx-track-js, devx-track-csharp
ms.subservice: blobs
ms.openlocfilehash: 1c1ba7d8cd0e4202003a98153a48e0593d1fcd04
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543152"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Tutorial: Erstellen einer hochverfügbaren Anwendung mit Blobspeicher

Dieses Tutorial ist der erste Teil einer Serie. In diesem Teil erfahren Sie, wie Sie Hochverfügbarkeit für Ihre Anwendungsdaten in Azure herstellen.

Am Ende des Tutorials verfügen Sie über eine Konsolenanwendung, die ein Blob in ein RA-GZRS-Speicherkonto ([geozonenredundanter Speicher mit Lesezugriff](../common/storage-redundancy.md)) hochlädt und aus diesem abruft.

Georedundanz in Azure Storage repliziert Transaktionen asynchron von einer primären Region in eine sekundäre Region, die Hunderte von Kilometern entfernt ist. Dieser Replikationsprozess garantiert, dass die Daten in der sekundären Region letztendlich konsistent sind. Die Konsolenanwendung ermittelt mithilfe des Musters [Trennschalter](/azure/architecture/patterns/circuit-breaker), mit welchem Endpunkt eine Verbindung hergestellt werden soll, und wechselt automatisch zwischen Endpunkten, wenn Fehler und Wiederherstellungen simuliert werden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Im ersten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Speicherkonto erstellen
> * Festlegen der Verbindungszeichenfolge
> * Ausführen der Konsolenanwendung

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

# <a name="net"></a>[.NET](#tab/dotnet)

* Installieren von [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit der Workload **Azure-Entwicklung**.

  ![Azure-Entwicklung (unter Web & Cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python"></a>[Python](#tab/python)

* [Installieren Sie Python.](https://www.python.org/downloads/)
* Laden Sie das [Azure Storage SDK für Python](https://github.com/Azure/azure-storage-python) herunter, und installieren Sie es.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Installieren Sie [Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Ein Speicherkonto stellt einen eindeutigen Namespace zum Speichern Ihrer Azure Storage-Datenobjekte sowie für den Zugriff auf diese Objekte bereit.

Führen Sie die folgenden Schritte aus, um ein geozonenredundantes Speicherkonto mit Lesezugriff (RA-GZRS) zu erstellen:

1. Wählen Sie im Azure-Portal die Schaltfläche **Ressource erstellen** aus.
2. Wählen Sie auf der Seite **Neu** die Option **Speicherkonto – Blob, Datei, Tabelle, Warteschlange** aus.
4. Füllen Sie das Speicherkontoformular wie in der Abbildung dargestellt mit den folgenden Angaben aus, und klicken Sie auf **Erstellen**.

   | Einstellung       | Beispielwert | BESCHREIBUNG |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Abonnement** | *Mein Abonnement* | Ausführliche Informationen zu Ihren Abonnements finden Sie unter [Abonnements](https://account.azure.com/Subscriptions). |
   | **ResourceGroup** | *myResourceGroup* | Gültige Ressourcengruppennamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Benennungsregeln und Einschränkungen). |
   | **Name** | *mystorageaccount* | Ein eindeutiger Name für Ihr Speicherkonto |
   | **Location** | *USA, Osten* | Wählen Sie einen Standort aus. |
   | **Leistung** | *Standard* | Die Standardleistung ist eine gute Option für das Beispielszenario. |
   | **Kontoart** | *StorageV2* | Es wird die Verwendung eines Speicherkontos vom Typ „Universell v2“ empfohlen. Weitere Informationen zu den Azure Storage-Kontotypen finden Sie unter [Speicherkontoübersicht](../common/storage-account-overview.md). |
   | **Replikation**| *Geozonenredundanter Speicher mit Lesezugriff (RA-GZRS)* | Die primäre Region ist zonenredundant und wird in einer sekundären Region repliziert. Lesezugriff auf die sekundäre Region ist dabei aktiviert. |
   | **Zugriffsebene**| *Heiße Ebene* | Verwenden Sie die heiße Ebene für häufig verwendete Daten. |

    ![Erstellen eines Speicherkontos](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Herunterladen des Beispiels

# <a name="net"></a>[.NET](#tab/dotnet)

[Laden Sie das Beispielprojekt herunter](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), und extrahieren (entzippen) Sie die Datei „storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip“. Sie können auch [Git](https://git-scm.com/) verwenden, um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen. Das Beispielprojekt enthält eine Konsolenanwendung.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python"></a>[Python](#tab/python)

[Laden Sie das Beispielprojekt herunter](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), und extrahieren (entzippen) Sie die Datei „storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip“. Sie können auch [Git](https://git-scm.com/) verwenden, um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen. Das Beispielprojekt enthält eine einfache Python-Anwendung.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Laden Sie das Beispielprojekt herunter](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs), und entzippen Sie die Datei. Sie können auch [Git](https://git-scm.com/) verwenden, um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen. Das Beispielprojekt enthält eine einfache Node.js-Anwendung.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Das Beispiel konfigurieren

# <a name="net"></a>[.NET](#tab/dotnet)

Sie müssen in der Anwendung die Verbindungszeichenfolge für das Speicherkonto angeben. Sie können diese Verbindungszeichenfolge in einer Umgebungsvariablen auf dem lokalen Computer speichern, auf dem die Anwendung ausgeführt wird. Befolgen Sie je nach Betriebssystem die Schritte für eines der unten angegebenen Beispiele, um die Umgebungsvariable zu erstellen.

Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto. Wählen Sie in Ihrem Speicherkonto unter **Einstellungen** die Option **Zugriffsschlüssel** aus. Kopieren Sie die **Verbindungszeichenfolge** aus dem primären oder sekundären Schlüssel. Führen Sie basierend auf Ihrem Betriebssystem einen der folgenden Befehle aus, und ersetzen Sie dabei \<yourconnectionstring\> durch Ihre tatsächliche Verbindungszeichenfolge. Mit diesem Befehl wird eine Umgebungsvariable auf dem lokalen Computer gespeichert. Unter Windows ist die Umgebungsvariable erst verfügbar, wenn Sie die **Eingabeaufforderung** oder die verwendete Shell neu laden.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python"></a>[Python](#tab/python)

Sie müssen in der Anwendung Ihre Anmeldeinformationen für das Speicherkonto angeben. Sie können diese Informationen in einer Umgebungsvariablen auf dem lokalen Computer speichern, auf dem die Anwendung ausgeführt wird. Befolgen Sie je nach Betriebssystem die Schritte für eines der unten angegebenen Beispiele, um die Umgebungsvariablen zu erstellen.

Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto. Wählen Sie in Ihrem Speicherkonto unter **Einstellungen** die Option **Zugriffsschlüssel** aus. Fügen Sie die Werte **Speicherkontoname** und **Schlüssel** in die folgenden Befehle ein, und ersetzen Sie dabei die Platzhalter \<youraccountname\> und \<youraccountkey\>. Mit diesem Befehl werden die Umgebungsvariablen auf dem lokalen Computer gespeichert. Unter Windows ist die Umgebungsvariable erst verfügbar, wenn Sie die **Eingabeaufforderung** oder die verwendete Shell neu laden.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Um dieses Beispiel ausführen zu können, müssen Sie Ihre Anmeldeinformationen für das Speicherkonto der `.env.example`-Datei hinzufügen und sie in `.env` umbenennen.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Sie finden diese Informationen im Azure-Portal, indem Sie zu Ihrem Speicherkonto navigieren und im Abschnitt **Einstellungen** die Option **Zugriffsschlüssel** auswählen.

Installieren Sie die erforderlichen Abhängigkeiten. Öffnen Sie hierzu eine Eingabeaufforderung, navigieren Sie zum Beispielordner, und geben Sie dann `npm install` ein.

---

## <a name="run-the-console-application"></a>Ausführen der Konsolenanwendung

# <a name="net"></a>[.NET](#tab/dotnet)

Drücken Sie in Visual Studio **F5**, oder wählen Sie **Starten** aus, um mit dem Debuggen der Anwendung zu beginnen. Visual Studio stellt fehlende NuGet-Pakete bei entsprechender Konfiguration automatisch wieder her. Weitere Informationen finden Sie unter [Installieren und Neuinstallieren von Paketen mit der Paketwiederherstellung](/nuget/consume-packages/package-restore#package-restore-overview).

Ein Konsolenfenster wird geöffnet, und die Anwendung wird ausgeführt. Die Anwendung lädt das Bild **HelloWorld.png** Bild aus der Projektmappe in das Speicherkonto hoch. Anschließend wird von der Anwendung überprüft, ob das Bild im sekundären RA-GZRS-Endpunkt repliziert wurde. Das Bild wird dann von der Anwendung maximal 999 Mal heruntergeladen. Jeder Lesevorgang wird dabei durch ein **P** oder ein **S** dargestellt. Während **P** für den primären Endpunkt steht, repräsentiert **S** den sekundären Endpunkt.

![Konsolenanwendung wird ausgeführt](media/storage-create-geo-redundant-storage/figure3.png)

Im Beispielcode wird die Aufgabe `RunCircuitBreakerAsync` in der Datei `Program.cs` verwendet, um mit der [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync)-Methode ein Bild aus dem Speicherkonto herunterzuladen. Vor dem Download wird ein [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext)-Objekt initialisiert. Der Vorgangskontext definiert Ereignishandler, die aufgerufen werden, wenn ein Download erfolgreich abgeschlossen wurde oder wenn ein Download fehlschlägt und erneut versucht wird, den Download auszuführen.

# <a name="python"></a>[Python](#tab/python)

Navigieren Sie zum Ausführen der Anwendung in einem Terminal oder über eine Eingabeaufforderung zum Verzeichnis **circuitbreaker.py**, und geben Sie `python circuitbreaker.py` ein. Die Anwendung lädt das Bild **HelloWorld.png** Bild aus der Projektmappe in das Speicherkonto hoch. Anschließend wird von der Anwendung überprüft, ob das Bild im sekundären RA-GZRS-Endpunkt repliziert wurde. Das Bild wird dann von der Anwendung maximal 999 Mal heruntergeladen. Jeder Lesevorgang wird dabei durch ein **P** oder ein **S** dargestellt. Während **P** für den primären Endpunkt steht, repräsentiert **S** den sekundären Endpunkt.

![Konsolenanwendung wird ausgeführt](media/storage-create-geo-redundant-storage/figure3.png)

Im Beispielcode wird die `run_circuit_breaker`-Methode in der Datei `circuitbreaker.py` verwendet, um mit der [get_blob_to_path](/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice?view=azure-python-previous#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-)-Methode ein Bild aus dem Speicherkonto herunterzuladen.

Die retry-Funktion des Storage-Objekts wird auf eine lineare Wiederholungsrichtlinie festgelegt. Mit der retry-Funktion wird ermittelt, ob für eine Anforderung ein Wiederholungsversuch durchgeführt werden soll. Außerdem wird angegeben, wie viele Sekunden lang gewartet werden soll, bevor der Versuch gestartet wird. Legen Sie den Wert **retry\_to\_secondary** auf „true“ fest, wenn für die Anforderung ein Wiederholungsversuch für den sekundären Endpunkt durchgeführt werden soll, falls die erste Anforderung an den primären Endpunkt fehlschlägt. In der Beispielanwendung wird in der `retry_callback`-Funktion des Storage-Objekts eine benutzerdefinierte Wiederholungsrichtlinie definiert.

Vor dem Herunterladen werden die Funktionen [retry_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) und [response_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) für das Dienstobjekt definiert. Mit diesen Funktionen werden die Ereignishandler definiert, die aufgerufen werden, wenn ein Download erfolgreich abgeschlossen wurde oder wenn ein Download fehlschlägt und erneut versucht wird, den Download durchzuführen.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Um das Beispiel auszuführen, öffnen Sie eine Eingabeaufforderung, navigieren Sie zum Beispielordner, und geben Sie dann `node index.js` ein.

Im Beispiel wird ein Container in Ihrem Blobspeicherkonto erstellt, **HelloWorld.png** in den Container geladen und dann wiederholt überprüft, ob der Container und das Image in der sekundären Region repliziert wurden. Nach der Replikation werden sie aufgefordert, **D** oder **Q** (gefolgt von der EINGABETASTE) zum Herunterladen oder Beenden einzugeben. Ihre Ausgabe sollte etwa folgendem Beispiel entsprechen:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

### <a name="net"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Ereignishandler für erneuten Downloadversuch

Der Ereignishandler `OperationContextRetrying` wird aufgerufen und für einen Neuversuch festgelegt, wenn es beim Herunterladen des Bilds zu einem Fehler kommt. Wenn die maximale Anzahl von Wiederholungen erreicht ist, die in der Anwendung definiert ist, wird die [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode)-Eigenschaft der Anforderung in `SecondaryOnly` geändert. Durch diese Einstellung versucht die Anwendung, das Bild vom sekundären Endpunkt herunterzuladen. Diese Konfiguration reduziert die Anforderungszeit für das Bild, da die Anzahl der wiederholten Anforderungen an den primären Endpunkt begrenzt ist.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Ereignishandler für abgeschlossene Anforderung

Der Ereignishandler `OperationContextRequestCompleted` wird aufgerufen, wenn der Download des Bilds erfolgreich ist. Wenn die Anwendung den sekundären Endpunkt verwendet, stellt die Anwendung weiterhin bis zu 20 Anforderungen an diesen Endpunkt. Nach 20 Anforderungen legt die Anwendung für die [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode)-Eigenschaft auf den Wert `PrimaryThenSecondary` zurück und versucht erneut, Anforderungen an den primären Endpunkt zu senden. Wenn eine Anforderung erfolgreich ist, liest die Anwendung weiterhin aus dem primären Endpunkt.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Ereignishandler für erneuten Downloadversuch

Der Ereignishandler `retry_callback` wird aufgerufen und für einen Neuversuch festgelegt, wenn es beim Herunterladen des Bilds zu einem Fehler kommt. Wenn die maximale Anzahl von Wiederholungen erreicht ist, die in der Anwendung definiert ist, wird die [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python)-Eigenschaft der Anforderung in `SECONDARY` geändert. Durch diese Einstellung versucht die Anwendung, das Bild vom sekundären Endpunkt herunterzuladen. Diese Konfiguration reduziert die Anforderungszeit für das Bild, da die Anzahl der wiederholten Anforderungen an den primären Endpunkt begrenzt ist.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Ereignishandler für abgeschlossene Anforderung

Der Ereignishandler `response_callback` wird aufgerufen, wenn der Download des Bilds erfolgreich ist. Wenn die Anwendung den sekundären Endpunkt verwendet, stellt die Anwendung weiterhin bis zu 20 Anforderungen an diesen Endpunkt. Nach 20 Anforderungen legt die Anwendung für die [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python)-Eigenschaft auf den Wert `PRIMARY` zurück und versucht erneut, Anforderungen an den primären Endpunkt zu senden. Wenn eine Anforderung erfolgreich ist, liest die Anwendung weiterhin aus dem primären Endpunkt.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Mit dem Node.js V10 SDK sind Rückrufhandler nicht erforderlich. Stattdessen erstellt das Beispiel eine Pipeline, die mit Optionen für Wiederholungsversuche und einem sekundären Endpunkt konfiguriert wird. Dies ermöglicht der Anwendung, automatisch zur sekundären Pipeline zu wechseln, wenn sie Ihre Daten nicht über die primäre Pipeline erreichen kann.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Nächste Schritte

Im ersten Teil dieser Reihe haben Sie erfahren, wie Sie mit RA-GZRS-Speicherkonten Hochverfügbarkeit für eine Anwendung herstellen.

Im zweiten Teil der Reihe erfahren Sie, wie Sie einen Fehler simulieren, durch den Ihre Anwendung auf den sekundären RA-GZRS-Endpunkt ausweichen muss.

> [!div class="nextstepaction"]
> [Tutorial: Simulieren eines Fehlers bei Zugriff auf redundanten Speicher mit Lesezugriff](simulate-primary-region-failure.md)