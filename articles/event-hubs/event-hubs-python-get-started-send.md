---
title: Senden oder Empfangen von Ereignissen mit Python und Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält eine exemplarische Vorgehensweise für die Erstellung einer Python-Anwendung, die Ereignisse an Azure Event Hubs sendet.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 6a8f0ddcfe6de904219059c6e761ead4c004732d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461449"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Senden von Ereignissen an oder Empfangen von Ereignissen aus Event Hubs mithilfe von Python

Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der pro Sekunde Millionen von Ereignissen empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Eine ausführliche Übersicht über Event Hubs finden Sie unter [Was ist Azure Event Hubs?](event-hubs-about.md) und [Event Hubs-Features im Überblick](event-hubs-features.md).

In diesem Tutorial wird beschrieben, wie Sie Python-Anwendungen erstellen, die Ereignisse an einen Event Hub senden oder von diesem empfangen. 

> [!NOTE]
> Sie können diese Schnellstartvorlage als Beispiel aus [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) herunterladen, die Zeichenfolgen `EventHubConnectionString` und `EventHubName` durch die Werte für Ihren Event Hub ersetzen und das Beispiel dann ausführen. Alternativ können Sie auch die Schritte in diesem Tutorial ausführen, um eine eigene Vorlage zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.
- Python 3.4 oder höher.
- Verwenden Sie das [Azure-Portal](https://portal.azure.com), um einen Namespace des Typs „Event Hubs“ zu erstellen, und rufen Sie die Verwaltungsanmeldeinformationen ab, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. Erstellen Sie anhand der Anleitung in [diesem Artikel](event-hubs-create.md) einen Namespace und einen Event Hub. Gehen Sie anschließend wie im Artikel beschrieben vor, um den Wert des Zugriffsschlüssels für den Event Hub abzurufen: [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Sie verwenden den Zugriffsschlüssel im Code, den Sie später in diesem Tutorial schreiben. Der Standardschlüsselname lautet: **RootManageSharedAccessKey**.

## <a name="install-python-package"></a>Installieren des Python-Pakets

Um die Python-Pakete für Event Hubs zu installieren, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält, und führen Sie dann diesen Befehl aus: 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>Senden von Ereignisse

### <a name="create-a-python-script-to-send-events"></a>Erstellen eines Python-Skripts zum Senden von Ereignissen

Erstellen Sie als Nächstes eine Python-Anwendung, die Ereignisse an einen Event Hub sendet:

1. Öffnen Sie Ihren bevorzugten Python-Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie ein Skript mit dem Namen **send.py**. Dieses Skript sendet 100 Ereignisse an Ihren Event Hub.
3. Fügen Sie den folgenden Code in „send.py“ ein, und ersetzen Sie dabei die Werte von „ADDRESS“, „USER“ und „KEY“ durch die Werte, die Sie im vorherigen Abschnitt im Azure-Portal abgerufen haben: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

try:
    if not ADDRESS:
        raise ValueError("No EventHubs URL supplied.")

    # Create Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    sender = client.add_sender(partition="0")
    client.run()
    try:
        start_time = time.time()
        for i in range(100):
            print("Sending message: {}".format(i))
            sender.send(EventData(str(i)))
    except:
        raise
    finally:
        end_time = time.time()
        client.stop()
        run_time = end_time - start_time
        logger.info("Runtime: {} seconds".format(run_time))

except KeyboardInterrupt:
    pass
```

### <a name="run-application-to-send-events"></a>Ausführen der Anwendung zum Senden von Ereignissen

Um das Skript auszuführen, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält, und führen Sie dann diesen Befehl aus:

```bash
start python send.py
```

Glückwunsch! Sie haben jetzt Nachrichten an einen Event Hub gesendet.

## <a name="receive-events"></a>Empfangen von Ereignissen

### <a name="create-a-python-script-to-receive-events"></a>Erstellen eines Python-Skripts zum Empfangen von Ereignissen

Erstellen Sie als Nächstes eine Python-Anwendung, die Ereignisse von einem Event Hub empfängt:

1. Öffnen Sie Ihren bevorzugten Python-Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie ein Skript mit dem Namen **recv.py**.
3. Fügen Sie den folgenden Code in „recv.py“ ein, und ersetzen Sie dabei die Werte von „ADDRESS“, „USER“ und „KEY“ durch die Werte, die Sie im vorherigen Abschnitt im Azure-Portal abgerufen haben: 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
        total += 1

    end_time = time.time()
    client.stop()
    run_time = end_time - start_time
    print("Received {} messages in {} seconds".format(total, run_time))

except KeyboardInterrupt:
    pass
finally:
    client.stop()
```

### <a name="receive-events"></a>Empfangen von Ereignissen

Um das Skript auszuführen, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält, und führen Sie dann diesen Befehl aus:

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Features und Terminologie in Azure Event Hubs](event-hubs-features.md)
- [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)

