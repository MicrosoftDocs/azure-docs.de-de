---
title: 'Verwenden von Queue Storage mit Python: Azure Storage'
description: Erfahren Sie, wie Sie mit dem Azure-Warteschlangendienst mit Python Warteschlangen erstellen und löschen sowie Nachrichten einfügen, abrufen und löschen können.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 75f04893067d92813207bd656fc3368239ae9303
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142787"
---
# <a name="how-to-use-queue-storage-from-python"></a>Verwenden des Warteschlangenspeichers mit Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Übersicht
In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Warteschlangenspeicherdienst demonstriert. Die Beispiele sind in Python geschrieben und verwenden das [Microsoft Azure Storage-SDK für Python]. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Herunterladen und Installieren des Azure Storage SDK für Python

Das [Azure Storage SDK für Python](https://github.com/azure/azure-storage-python) erfordert Python 2.7, 3.3, 3.4, 3.5 oder 3.6.
 
### <a name="install-via-pypi"></a>Installation über PyPI

Geben Sie für die Installation über Python Package Index (PyPI) folgenden Befehl ein:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Wenn Sie ein Upgrade aus dem Azure Storage SDK für Python Version 0.36 oder früher vornehmen, deinstallieren Sie das ältere SDK mit `pip uninstall azure-storage`, bevor Sie das neueste Paket installieren.

Informationen zu alternativen Installationsmethoden finden Sie unter [Azure Storage SDK für Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Anzeigen der Beispielanwendung

Um eine Beispielanwendung anzuzeigen und auszuführen, die zeigt, wie Python mit Azure Queues verwendet wird, finden Sie unter [Azure Storage: Erste Schritte mit Azure-Warteschlangen in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Um die Beispielanwendung auszuführen, stellen Sie sicher, dass Sie die `azure-storage-queue`- und `azure-storage-common`-Pakete beide installiert haben.

## <a name="how-to-create-a-queue"></a>Anleitung: Erstellen einer Warteschlange

Das Objekt **QueueService** ermöglicht es Ihnen, mit Warteschlangen zu arbeiten. Der folgende Code erstellt ein **QueueService** -Objekt. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Code hinzu:

```python
from azure.storage.queue import QueueService
```

Der folgende Code erstellt ein **QueueService** -Objekt unter Verwendung des Speicherkontonamens und Kontoschlüssels. Ersetzen Sie „myaccount“ und „mykey“ durch Ihren Kontonamen und Schlüssel.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Anleitung: Einfügen einer Nachricht in eine Warteschlange
Zum Einfügen einer Nachricht in eine Warteschlange verwenden Sie die **put\_message**-Methode, um eine neue Nachricht zu erstellen und zur Warteschlange hinzuzufügen.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Anleitung: Einsehen der nächsten Nachricht
Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **peek\_messages** aufrufen. Standardmäßig wird von **peek\_messages** jeweils nur eine Nachricht angeschaut.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Anleitung: Entfernen von Nachrichten aus Warteschlangen
Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie **get\_messages** aufrufen, wird standardmäßig die nächste Nachricht aus der Warteschlange abgerufen. Die für **get\_messages** zurückgegebene Nachricht ist für andere Codes nicht mehr sichtbar, die Nachrichten aus dieser Warteschlange lesen. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **delete\_message** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **delete\_message** direkt nach der Verarbeitung der Nachricht auf.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können.
Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **get\_messages** verwendet, um 16 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer for Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Anleitung: Ändern des Inhalts von Nachrichten in der Warteschlange
Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Im Code unten wird die **update\_message**-Methode zum Aktualisieren einer Nachricht verwendet. Das Sichtbarkeitstimeout ist auf 0 festgelegt, d. h., die Nachricht wird sofort angezeigt, und der Inhalt wird aktualisiert.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Anleitung: Abrufen der Warteschlangenlänge
Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode **get\_queue\_metadata** ruft Metadaten zur Warteschlange vom Warteschlangendienst sowie **approximate_message_count** ab. Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Anleitung: Löschen einer Warteschlange
Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **delete\_queue** auf.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen von Queue Storage vertraut gemacht haben, lesen Sie die folgenden Artikel, um mehr zu erfahren.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API für Azure-Speicherdienste](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage-SDK für Python]: https://github.com/Azure/azure-storage-python
