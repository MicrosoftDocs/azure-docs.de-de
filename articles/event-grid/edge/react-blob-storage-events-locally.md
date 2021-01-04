---
title: Reagieren auf Blob Storage-Modulereignisse – Azure Event Grid IoT Edge | Microsoft-Dokumentation
description: Reagieren auf Blob Storage-Modulereignisse
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005060"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Tutorial: Reagieren auf Blob Storage-Ereignisse in IoT Edge (Preview)
In diesem Artikel erfahren Sie, wie Sie Azure Blob Storage für das IoT-Modul bereitstellen, das als Event Grid-Herausgeber fungieren würde, um Ereignisse bei der Blob-Erstellung und Blob-Löschung an Event Grid zu senden.  

Eine Übersicht über Azure Blob Storage in IoT Edge finden Sie unter [Azure Blob Storage in IoT Edge](../../iot-edge/how-to-store-data-blob.md) und seinen Features.

> [!WARNING]
> Azure Blob Storage-Integration in IoT Edge mit Event Grid befindet sich in der Vorschauphase.

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

* Ein **Azure-Abonnement**: Falls Sie noch kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free). 
* **Azure IoT Hub- und IoT Edge-Gerät**: Befolgen Sie die Schritte im Schnellstart für [Linux](../../iot-edge/quickstart-linux.md)- oder [Windows-Geräte](../../iot-edge/quickstart.md), sofern nicht bereits geschehen.

## <a name="deploy-event-grid-iot-edge-module"></a>Bereitstellen von Event Grid in einem IoT Edge-Modul

Es gibt mehrere Möglichkeiten, wie Sie Module auf einem IoT Edge-Gerät bereitstellen können. Alle sind für Azure Event Grid in IoT Edge geeignet. In diesem Artikel werden die Schritte zum Bereitstellen von Event Grid in IoT Edge über das Azure-Portal beschrieben.

>[!NOTE]
> In diesem Tutorial stellen Sie das Event Grid-Modul ohne Persistenz bereit. Das heißt, dass alle in diesem Tutorial erstellten Themen und Abonnements beim erneuten Bereitstellen des Moduls gelöscht werden. Weitere Informationen zum Einrichten von Persistenz finden Sie in den folgenden Artikeln: [Beibehalten des Status in Linux](persist-state-linux.md) oder [Beibehalten des Status in Windows](persist-state-windows.md). Für Produktionsworkloads empfiehlt es sich, das Event Grid-Modul mit Persistenz zu installieren.


### <a name="select-your-iot-edge-device"></a>Auswählen Ihres IoT Edge-Geräts

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
1. Navigieren Sie zu Ihrer IoT Hub-Instanz.
1. Wählen Sie im Menü im Abschnitt **Automatische Geräteverwaltung** die Option **IoT Edge** aus. 
1. Klicken Sie in der Liste der Geräte auf die ID des Zielgeräts.
1. Wählen Sie **Module festlegen** aus. Lassen Sie die Seite geöffnet. Sie fahren mit den Schritten im nächsten Abschnitt fort.

### <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Das Azure-Portal verfügt über einen Assistenten, der Sie durch die Erstellung eines Bereitstellungsmanifests führt, damit Sie das JSON-Dokument nicht manuell erstellen müssen.  Der Assistent umfasst drei Schritte: **Hinzufügen von Modulen**, **Angeben von Routen** und **Überprüfen der Bereitstellung**.

### <a name="add-modules"></a>Hinzufügen von Modulen

1. Wählen Sie im Abschnitt **Bereitstellungsmodule** die Option **Hinzufügen** aus.
1. Wählen Sie in der Dropdownliste mit den Modultypen den Eintrag **IoT Edge-Modul** aus.
1. Geben Sie den Namen, das Image und die Optionen für die Containererstellung für den Container an:

   * **Name**: eventgridmodule
   * **Image-URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Optionen für Containererstellung**:

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. Klicken Sie unten auf der Seite auf **Speichern**.
 1. Fahren Sie mit dem nächsten Abschnitt fort, um vor der gemeinsamen Bereitstellung das Azure Event Grid-Abonnent-Modul hinzuzufügen.

    >[!IMPORTANT]
    > In diesem Tutorial erfahren Sie, wie Sie das Event Grid-Modul so bereitstellen, dass HTTP- und HTTPS-Anforderungen zulässig sind, und wie Sie die Clientauthentifizierung deaktivieren. Für Produktionsworkloads empfiehlt es sich, dass Sie lediglich HTTPS-Anforderungen und Abonnenten mit aktivierter Clientauthentifizierung aktivieren. Weitere Informationen zum sicheren Konfigurieren des Event Grid-Moduls finden Sie unter [Sicherheit und Authentifizierung](security-authentication.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Bereitstellen des IoT Edge-Moduls für Event Grid-Abonnenten

In diesem Abschnitt erfahren Sie, wie Sie ein weiteres IoT-Modul als Ereignishandler bereitstellen, an den Ereignisse übermittelt werden können.

### <a name="add-modules"></a>Hinzufügen von Modulen

1. Wählen Sie im Abschnitt **Bereitstellungsmodule** erneut die Option **Hinzufügen** aus. 
1. Wählen Sie in der Dropdownliste mit den Modultypen den Eintrag **IoT Edge-Modul** aus.
1. Geben Sie den Namen, das Image und die Optionen für die Containererstellung für den Container an:

   * **Name**: subscriber
   * **Image-URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Optionen für Containererstellung**: Keine
1. Klicken Sie unten auf der Seite auf **Speichern**.
1. Fahren Sie mit dem nächsten Abschnitt fort, um das Azure Blob Storage-Modul hinzuzufügen.

## <a name="deploy-azure-blob-storage-module"></a>Bereitstellen des Azure Blob Storage-Moduls

In diesem Abschnitt erfahren Sie, wie Sie das Azure Blob Storage-Modul bereitstellen, das als Event Grid-Herausgeber fungieren würde, der Blob-Erstellungs- und Blob-Löschereignisse veröffentlicht.

### <a name="add-modules"></a>Hinzufügen von Modulen

1. Wählen Sie im Abschnitt **Bereitstellungsmodule** die Option **Hinzufügen** aus.
2. Wählen Sie in der Dropdownliste mit den Modultypen den Eintrag **IoT Edge-Modul** aus.
3. Geben Sie den Namen, das Image und die Optionen für die Containererstellung für den Container an:

   * **Name**: azureblobstorageoniotedge
   * **Image-URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **Optionen für Containererstellung**:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Das Blob Storage-Modul kann Ereignisse mithilfe von HTTPS und HTTP veröffentlichen. 
   > - Wenn Sie die clientbasierte Authentifizierung für Event Grid aktiviert haben, sollten Sie sicherstellen, dass Sie den Wert EVENTGRID_ENDPOINT wie folgt aktualisieren, um HTTPS zuzulassen: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`.
   > - Fügen Sie dem obigen JSON-Code außerdem `AllowUnknownCertificateAuthority=true` als weitere Umgebungsvariable hinzu. Bei der Kommunikation mit EventGrid über HTTPS ermöglicht **AllowUnknownCertificateAuthority**, dass das Speichermodul selbstsignierten EventGrid-Serverzertifikaten vertraut.

4. Aktualisieren Sie den kopierten JSON-Code mit folgenden Informationen:

   - Ersetzen Sie `<your storage account name>` durch einen leicht zu merkenden Namen. Kontonamen sollten 3 bis 24 Zeichen lang sein und nur Kleinbuchstaben und Zahlen enthalten. Leerzeichen sind nicht zulässig.

   - Ersetzen Sie `<your storage account key>` durch einen Base64-Schlüssel mit 64 Bytes. Sie können einen Schlüssel mit Tools wie [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) generieren. Mit diesen Anmeldeinformationen greifen Sie über andere Module auf Blob Storage zu.

   - Ersetzen Sie `<event grid module name>` durch den Namen Ihres Event Grid-Moduls.
   - Ersetzen Sie `<storage mount>` gemäß Ihrem Containerbetriebssystem.
     - Für Linux-Container **my-volume:/blobroot**
     - Für Windows-Container **my-volume:C:/BlobRoot**

5. Klicken Sie unten auf der Seite auf **Speichern**.
6. Klicken Sie auf **Weiter**, um mit dem Abschnitt über Routen fortzufahren.

    > [!NOTE]
    > Wenn Sie eine Azure-VM als Edge-Gerät verwenden, fügen Sie eine Regel für eingehende Ports hinzu, um eingehenden Datenverkehr an den in diesem Tutorial verwendeten Hostports zuzulassen: 4438, 5888, 8080 und 11002. Anweisungen zum Hinzufügen der Regel finden Sie unter [Öffnen von Ports für einen virtuellen Computer](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Einrichten von Routen

Behalten Sie Standardrouten bei, und wählen Sie **Weiter** aus, um mit dem Abschnitt für die Überprüfung fortzufahren.

### <a name="review-deployment"></a>Überprüfen der Bereitstellung

1. Im Abschnitt zur Überprüfung wird das JSON-Bereitstellungsmanifest angezeigt, das basierend auf Ihrer Auswahl in den vorherigen Abschnitten erstellt wurde. Vergewissern Sie sich, dass die folgenden vier Module angezeigt werden: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **subscriber** und **azureblobstorageoniotedge**, und dass alle bereitgestellt werden.
2. Überprüfen Sie die Bereitstellungsinformationen, und klicken Sie dann auf **Senden**.

## <a name="verify-your-deployment"></a>Überprüfen Ihrer Bereitstellung

1. Nach Übermittlung der Bereitstellung kehren Sie zur Seite „IoT Edge“ Ihres IoT-Hubs zurück.
2. Wählen Sie das **IoT Edge-Zielgerät** für die Bereitstellung aus, um dessen Details zu öffnen.
3. Überprüfen Sie in den Gerätedetails, dass die Module „eventgridmodule“, „subscriber“ und „azureblobstorageoniotedge“ sowohl als **In Bereitstellung angegeben** als auch als **Vom Gerät gemeldet** aufgeführt werden.

   Unter Umständen dauert es einen Moment, bis das Modul auf dem Gerät gestartet und an IoT Hub gemeldet wurde. Aktualisieren Sie die Seite, um einen aktualisierten Status anzuzeigen.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Veröffentlichen von BlobCreated- und BlobDeleted-Ereignissen

1. Dieses Modul erstellt automatisch ein Thema **MicrosoftStorage-** . Stellen Sie sicher, dass dieses vorhanden ist.
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Beispielausgabe:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - Wenn Clientauthentifizierung über den SAS-Schlüssel aktiviert ist, sollte für den HTTPS-Flow der zuvor angegebene SAS-Schlüssel als Header hinzugefügt werden. Daher lautet die curl-Anforderung wie folgt: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Für den HTTPS-Flow lautet die curl-Anforderung folgendermaßen, wenn Clientauthentifizierung über das Zertifikat aktiviert ist: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Abonnenten können sich für Ereignisse registrieren, die in einem Thema veröffentlicht werden. Zum Empfang von Ereignissen müssen Sie ein Event Grid-Abonnement für das Thema **MicrosoftStorage** erstellen.
    1. Erstellen Sie „blobsubscription.json“ mit dem folgenden Inhalt. Ausführliche Informationen zur Nutzlast finden Sie in unserer [API-Dokumentation](api.md).

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > Die **endpointType**-Eigenschaft gibt an, dass es sich bei dem Abonnenten um einen **Webhook** handelt.  Die **endpointUrl** gibt die URL an, an der der Abonnent auf Ereignisse lauscht. Diese URL entspricht der aus dem Azure Functions-Beispiel, das Sie zuvor bereitgestellt haben.

    2. Führen Sie den folgenden Befehl aus, um ein Abonnement für das Thema zu erstellen. Vergewissern Sie sich, dass der HTTP-Statuscode `200 OK` angezeigt wird.

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Wenn Clientauthentifizierung über den SAS-Schlüssel aktiviert ist, sollte für den HTTPS-Flow der zuvor angegebene SAS-Schlüssel als Header hinzugefügt werden. Daher lautet die curl-Anforderung wie folgt: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Für den HTTPS-Flow lautet die curl-Anforderung folgendermaßen, wenn Clientauthentifizierung über das Zertifikat aktiviert ist: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Führen Sie den folgenden Befehl aus, um zu bestätigen, dass das Abonnement erfolgreich erstellt wurde. Der HTTP-Statuscode „200 OK“ sollte zurückgegeben werden.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Beispielausgabe:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Wenn Clientauthentifizierung über den SAS-Schlüssel aktiviert ist, sollte für den HTTPS-Flow der zuvor angegebene SAS-Schlüssel als Header hinzugefügt werden. Daher lautet die curl-Anforderung wie folgt: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Für den HTTPS-Flow lautet die curl-Anforderung folgendermaßen, wenn Clientauthentifizierung über das Zertifikat aktiviert ist: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Laden Sie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) herunter, und [verbinden Sie ihn mit Ihrem lokalen Speicher](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer).

## <a name="verify-event-delivery"></a>Überprüfen der Ereignisübermittlung

### <a name="verify-blobcreated-event-delivery"></a>Überprüfen der BlobCreated-Ereignisübermittlung

1. Laden Sie Dateien als Blockblobs aus Azure Storage-Explorer in den lokalen Speicher hoch, woraufhin das Modul Erstellungsereignisse automatisch veröffentlicht. 
2. Überprüfen Sie die Abonnentenprotokolle auf Erstellenereignisse. Befolgen Sie die Schritte zum [Überprüfen der Ereignisübermittlung](pub-sub-events-webhook-local.md#verify-event-delivery).

    Beispielausgabe:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Überprüfen der BlobDeleted-Ereignisübermittlung

1. Löschen Sie Blobs mithilfe von Azure Storage-Explorer aus dem lokalen Speicher, woraufhin das Modul Löschereignisse automatisch veröffentlicht. 
2. Überprüfen Sie die Abonnentenprotokolle für Löschereignisse. Befolgen Sie die Schritte zum [Überprüfen der Ereignisübermittlung](pub-sub-events-webhook-local.md#verify-event-delivery).

    Beispielausgabe:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Glückwunsch! Sie haben das Tutorial abgeschlossen. Die folgenden Abschnitte bieten Details zu den Ereigniseigenschaften.

### <a name="event-properties"></a>Ereigniseigenschaften

Im Folgenden finden Sie die Liste der unterstützten Ereigniseigenschaften mit ihren zugehörigen Typen und Beschreibungen. 

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| topic | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| subject | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| eventType | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| eventTime | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| id | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| data | Objekt (object) | Ereignisdaten für Blob Storage. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| api | Zeichenfolge | Der Vorgang, durch den das Ereignis ausgelöst wurde. Es kann sich um einen der folgenden Werte handeln: <ul><li>BlobCreated: zulässige Werte sind: `PutBlob` und `PutBlockList`.</li><li>BlobDeleted: zulässige Werte sind: `DeleteBlob`, `DeleteAfterUpload` und `AutoDelete`. <p>Das `DeleteAfterUpload`-Ereignis wird generiert, wenn ein Blob automatisch gelöscht wird, weil die gewünschte Eigenschaft „deleteAfterUpload“ auf „true“ festgelegt ist. </p><p>Das `AutoDelete`-Ereignis wird generiert, wenn ein Blob automatisch gelöscht wird, weil der Wert der gewünschten Eigenschaft „deleteAfterMinutes“ abgelaufen ist.</p></li></ul>|
| clientRequestId | Zeichenfolge | Vom Client bereitgestellte Anforderungs-ID für den Speicher-API-Vorgang. Diese ID kann zur Korrelation mit Azure Storage-Diagnoseprotokollen anhand des Felds „client-request-id“ in den Protokollen verwendet und in Clientanforderungen mit dem Header „x-ms-client-request-id“ bereitgestellt werden. Ausführliche Informationen finden Sie unter [Protokollformat](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | Zeichenfolge | Vom Dienst generierte Anforderungs-ID für den Speicher-API-Vorgang. Kann zum Korrelieren mit Azure Storage-Diagnoseprotokolle mithilfe des Felds „request-id-header“ in den Protokollen verwendet werden, und wird vom einleitenden API-Aufruf im „x-ms-request-id“-Header zurückgegeben. Informationen finden Sie unter [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Storage Analytics-Protokollformat). |
| eTag | Zeichenfolge | Der Wert, den Sie verwenden können, um Vorgänge bedingt auszuführen. |
| contentType | Zeichenfolge | Der für das Blob angegebene Inhaltstyp. |
| contentLength | integer | Die Größe des Blobs in Byte. |
| blobType | Zeichenfolge | Der Blobtyp. Gültige Werte sind „BlockBlob“ oder „PageBlob“. |
| url | Zeichenfolge | Der Pfad des Blobs. <br>Wenn der Client eine Blob-REST-API verwendet, hat die URL die folgende Struktur: *\<storage-account-name\>.blob.core.windows.net/\<container-name\>/\<file-name\>* . <br>Wenn der Client eine Data Lake Storage-REST-API verwendet, hat die URL die folgende Struktur: *\<storage-account-name\>.dfs.core.windows.net/\<file-system-name\>/\<file-name\>* . |


## <a name="next-steps"></a>Nächste Schritte

Informationen finden Sie in den folgenden Artikeln der Blob Storage-Dokumentation:

- [Filtern von Blob Storage-Ereignissen](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Empfohlene Vorgehensweisen für die Verwendung von Blob Storage-Ereignissen](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

In diesem Tutorial haben Sie Ereignisse veröffentlicht, indem Sie Blobs in einem Azure Blob Storage erstellt oder gelöscht haben. Sehen Sie sich die anderen Tutorials an, um zu erfahren, wie Sie Ereignisse an die Cloud weiterleiten (Azure Event Hub oder Azure IoT Hub): 

- [Weiterleiten von Ereignissen an Azure Event Grid](forward-events-event-grid-cloud.md)
- [Weiterleiten von Ereignissen an Azure IoT Hub](forward-events-iothub.md)
