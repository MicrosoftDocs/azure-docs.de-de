---
title: Lesen erfasster Daten aus der Python-App – Azure Event Hubs | Microsoft-Dokumentation
description: Ein Beispiel zur Veranschaulichung der Verwendung des Erfassungsfeatures von Event Hubs unter Verwendung des Azure Python SDKs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 639bc4ff9c69bca3d5f8bca6967bfc3e8e6a13d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822423"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Exemplarische Vorgehensweise für Event Hubs Capture: Python

Capture ist ein Feature von Azure Event Hubs. Mit diesem Feature können Sie Streamingdaten in Ihrem Event Hub automatisch an ein Azure Blob Storage-Konto Ihrer Wahl übermitteln. Diese Funktion erleichtert die Batchverarbeitung von Echtzeit-Streamingdaten. In diesem Artikel erfahren Sie, wie Sie die Event Hubs-Erfassung mit Python verwenden. Weitere Informationen zur Event Hubs-Erfassung finden Sie im [Übersichtsartikel](event-hubs-capture-overview.md).

In diesem Beispiel wird das [Azure Python SDK](https://azure.microsoft.com/develop/python/) verwendet, um die Verwendung des Erfassungsfeatures zu veranschaulichen. Mit dem Programm „sender.py“ werden simulierte Telemetriedaten der Umgebung im JSON-Format an Event Hubs gesendet. Der Event Hub ist für die Verwendung des Capture-Features konfiguriert, um diese Daten in Batches in den Blobspeicher zu schreiben. Die App „capturerereader.py“ liest diese Blobs und erstellt jeweils eine Anfügedatei pro Gerät. Anschließend schreibt die App die Daten in CSV-Dateien.

## <a name="what-youll-accomplish"></a>Umfang

1. Erstellen eines Azure Blob Storage-Kontos und eines darin enthaltenen Blobcontainers über das Azure-Portal
2. Erstellen eines Event Hubs-Namespace über das Azure-Portal
3. Erstellen eines Event Hubs mit aktiviertem Capture-Feature über das Azure-Portal
4. Senden von Daten an den Event Hub mithilfe eines Python-Skripts
5. Lesen der Dateien aus der Erfassung und Verarbeiten der Dateien mithilfe eines weiteren Python-Skripts

## <a name="prerequisites"></a>Voraussetzungen

- Python 2.7.x
- Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.
- Ein aktiver [Event Hubs-Namespace und -Event Hub](event-hubs-create.md). 
- Aktivieren Sie das Feature **Capture** für den Event Hub mithilfe der folgenden Anweisungen: [Aktivieren von Event Hubs Capture über das Azure-Portal](event-hubs-capture-enable-through-portal.md)

## <a name="create-an-azure-blob-storage-account"></a>Erstellen eines Azure Blob Storage-Kontos
1. Melden Sie sich beim [Azure-Portal][Azure portal] an.
2. Klicken Sie im linken Bereich des Azure-Portals **Neu** > **Speicher** > **Speicherkonto**.
3. Wählen Sie im Bereich **Speicherkonto erstellen** die erforderlichen Optionen aus, und klicken Sie anschließend auf **Erstellen**.
   
   ![Bereich „Speicherkonto erstellen“][1]
4. Klicken Sie nach dem Erscheinen der Meldung **Die Bereitstellungen waren erfolgreich** auf den Namen des neuen Speicherkontos und dann im Bereich **Zusammenfassung** auf **Blobs**. Klicken Sie oben im Bereich **Blob-Dienst** auf **+ Container**. Geben Sie dem Container den Namen **capture**, und schließen Sie den Bereich **Blob-Dienst**.
5. Klicken Sie im linken Bereich auf **Zugriffsschlüssel**, und kopieren Sie den Namen des Speicherkontos und den Wert von **key1**. Speichern Sie diese Werte im Editor oder an einem anderen temporären Speicherort.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Erstellen eines Python-Skripts zum Senden von Ereignissen an Ihren Event Hub
1. Öffnen Sie Ihren bevorzugten Python-Editor, z.B. [Visual Studio Code][Visual Studio Code].
2. Erstellen Sie ein Skript mit dem Namen **sender.py**. Dieses Skript sendet 200 Ereignisse an Ihren Event Hub. Es handelt sich hierbei um einfache Umweltdaten im JSON-Format.
3. Fügen Sie den folgenden Code in „sender.py“ ein:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Aktualisieren Sie den obigen Code, damit Ihr Namespacename, die Schlüsselwerte und der Event Hub-Name verwendet werden, die Sie beim Erstellen des Event Hubs-Namespace erhalten haben.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Erstellen eines Python-Skripts zum Lesen Ihrer Erfassungsdateien

1. Füllen Sie den Bereich aus, und klicken Sie anschließend auf **Erstellen**.
2. Erstellen Sie ein Skript mit dem Namen **capturereader.py**. Dieses Skript liest die erfassten Dateien und erstellt jeweils eine Datei pro Gerät, um nur die Daten für das jeweilige Gerät zu schreiben.
3. Fügen Sie den folgenden Code in „capturereader.py“ ein:
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
       reader = DataFileReader(open(filename, 'rb'), DatumReader())
       dict = {}
       for reading in reader:
           parsed_json = json.loads(reading["Body"])
           if not 'id' in parsed_json:
               return
           if not dict.has_key(parsed_json['id']):
               list = []
               dict[parsed_json['id']] = list
           else:
               list = dict[parsed_json['id']]
               list.append(parsed_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print 'Processor started using path: ' + os.getcwd()
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
   ```
4. Fügen Sie in den Aufruf von `startProcessing` die entsprechenden Werte für Speicherkontoname und Schlüssel ein.

## <a name="run-the-scripts"></a>Ausführen der Skripts
1. Öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält, und führen Sie dann diese Befehle aus, um die Python-Pakete mit den erforderlichen Komponenten zu installieren:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Wenn Sie über eine ältere Version von **azure-storage** oder **azure** verfügen, müssen Sie unter Umständen die Option **--upgrade** verwenden.
   
   Ggf. müssen Sie auch den folgenden Befehl ausführen. Dies ist jedoch bei den meisten Systeme nicht erforderlich.
   
   ```
   pip install cryptography
   ```
2. Wechseln Sie zu dem Verzeichnis, in dem Sie „sender.py“ und „capturereader.py“ gespeichert haben, und führen Sie den folgenden Befehl aus:
   
   ```
   start python sender.py
   ```
   
   Dieser Befehl startet einen neuen Python-Prozess zum Ausführen des Absenders.
3. Warten Sie einige Minuten, während die Erfassung ausgeführt wird. Geben Sie dann den folgenden Befehl in das ursprüngliche Befehlsfenster ein:
   
   ```
   python capturereader.py
   ```

   Dieser Erfassungsprozessor verwendet das lokale Verzeichnis, um alle Blobs aus dem Speicherkonto/Container herunterzuladen. Er verarbeitet alle Blobs, die nicht leer sind, und schreibt die Ergebnisse als CSV-Dateien in das lokale Verzeichnis.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über die Event Hubs-Erfassung][Overview of Event Hubs Capture]
* [Beispielanwendungen mit Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Übersicht über Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
