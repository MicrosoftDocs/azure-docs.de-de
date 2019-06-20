---
title: Verwenden von Python zum Senden und Empfangen von Nachrichten – Azure Event Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Python Ereignisse senden, empfangen und über Event Hubs gestreamte Ereignisse erfassen.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 88fdaec9e19c082a6fe981dc4d9a0e015335f1e2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60202972"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Verwenden von Azure Event Hubs aus einer Python-Anwendung
Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der pro Sekunde Millionen von Ereignissen empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Weitere Informationen finden Sie unter [Einführung in Event Hubs](event-hubs-what-is-event-hubs.md). 

Dieser Artikel enthält Links zu Artikeln, die Ihnen zeigen, wie Sie die folgenden Aufgaben über eine in **Python** geschriebene Anwendung ausführen können:

- [Senden von Ereignissen an einen Event Hub](#send-events-to-event-hubs)
- [Empfangen von Ereignissen von einem Event Hub](#receive-events-from-event-hubs)
- Lesen von erfassten Ereignisdaten aus einem Azure-Speicher 

## <a name="prerequisites"></a>Voraussetzungen
- Erstellen Sie einen Event Hub, indem Sie einem dieser Schnellstarts folgen: [Azure-Portal](event-hubs-create.md), [Azure-Befehlszeilenschnittstelle](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [Azure Resource Manager-Vorlage](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4 oder höher auf Ihrem Computer installiert.

## <a name="install-python-package"></a>Installieren des Python-Pakets

Um die Python-Pakete für Event Hubs zu installieren, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält, und führen Sie dann diesen Befehl aus: 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Senden von Ereignissen an Event Hubs
Der folgende Code zeigt Ihnen, wie Sie über eine Python-Anwendung Ereignisse an einen Event Hub senden: 

1. Erstellen Sie Variablen, die die URL des Event Hub, den Schlüsselnamen und den Schlüsselwert enthalten. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Erstellen Sie einen Event Hubs-Client, fügen Sie einen Absender hinzu, führen Sie den Client aus, senden Sie das Ereignis mit dem Absender und stoppen Sie den Client, wenn Sie fertig sind. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Ein vollständiges Tutorial zum Senden von Ereignissen an einen Event Hub aus einer in Python geschriebenen Anwendung finden Sie in [diesem Artikel](event-hubs-python-get-started-send.md).

## <a name="receive-events-from-event-hubs"></a>Empfangen von Ereignissen von Event Hubs
Der folgende Code zeigt Ihnen, wie Sie über eine Python-Anwendung Ereignisse von einem Event Hub empfangen: 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Ein vollständiges Tutorial zum Empfangen von Ereignissen von einem Event Hub aus einer in Python geschriebenen Anwendung finden Sie in [diesem Artikel](event-hubs-python-get-started-receive.md).

## <a name="read-capture-event-data-from-azure-storage"></a>Lesen von erfassten Ereignisdaten aus Azure Storage
Der folgende Code zeigt Ihnen, wie Sie erfasste Ereignisdaten, die in einem **Azure-Blob-Speicher** gespeichert sind, aus einer Python-Anwendung lesen können: Aktivieren Sie das Feature **Capture** für den Event Hub mithilfe der folgenden Anweisungen: [Aktivieren von Event Hubs Capture über das Azure-Portal](event-hubs-capture-enable-through-portal.md) Senden Sie dann einige Ereignisse an den Event Hub, bevor Sie den Code testen. 

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

Ein vollständiges Tutorial zum Lesen erfasster Event Hubs-Daten in einem Azure-Blob-Speicher aus einer in Python geschriebenen Anwendung finden Sie in [diesem Artikel](event-hubs-capture-python.md).

## <a name="github-samples"></a>GitHub-Beispiele
Weitere Python-Beispiele finden Sie im [Git-Repository azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/).

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die Artikel im Abschnitt „Konzepte“ ab [Event Hubs-Features im Überblick](event-hubs-features.md).
