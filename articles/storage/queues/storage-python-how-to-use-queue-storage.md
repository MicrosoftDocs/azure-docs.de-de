---
title: 'Verwenden von Azure Queue Storage mit Python: Azure Storage'
description: Erfahren Sie, wie Sie mit dem Azure-Warteschlangendienst in Python Warteschlangen erstellen und löschen sowie Nachrichten einfügen, abrufen und löschen können.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/25/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: ac75b89548d346945901d752672ef0f08601ccfb
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345651"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Verwenden von Azure Queue Storage mit Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Übersicht

In diesem Artikel werden häufige Szenarien für die Verwendung des Azure Queue Storage-Diensts veranschaulicht. Die erläuterten Szenarien umfassen das Einfügen, Einsehen, Abrufen und Löschen von Warteschlangennachrichten. Der Code für das Erstellen und Löschen von Warteschlangen wird ebenfalls behandelt.

Die Beispiele in diesem Artikel sind in Python geschrieben und verwenden die [Azure Queue Storage-Clientbibliothek für Python]. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Herunterladen und Installieren des Azure Storage SDKs für Python

Das [Azure Storage SDK für Python](https://github.com/azure/azure-storage-python) erfordert Python Version 2.7, 3.3 oder höher.

### <a name="install-via-pypi"></a>Installieren über PyPI

Geben Sie für die Installation über Python Package Index (PyPI) folgenden Befehl ein:

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Wenn Sie ein Upgrade aus dem Azure Storage SDK für Python Version 0.36 oder früher vornehmen, deinstallieren Sie das ältere SDK mit `pip uninstall azure-storage`, bevor Sie das neueste Paket installieren.

Informationen zu alternativen Installationsmethoden finden Sie unter [Azure SDK für Python].

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurieren Ihrer Anwendung für den Zugriff auf den Warteschlangenspeicher

# <a name="python-v12"></a>[Python v12](#tab/python)

Das [QueueClient](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient)-Objekt ermöglicht Ihnen die Arbeit mit Warteschlangen. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf eine Azure-Warteschlange zugreifen möchten, folgenden Code ein:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Das Objekt [QueueService](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2) ermöglicht es Ihnen, mit Warteschlangen zu arbeiten. Der folgende Code erstellt ein `QueueService`-Objekt. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure Storage zugreifen möchten, folgenden Code hinzu:

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

Das Paket `os` stellt Unterstützung für das Abrufen einer Umgebungsvariable bereit. Das Paket `uuid` stellt Unterstützung für das Generieren eines eindeutigen Bezeichners für einen Warteschlangennamen bereit.

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Die Verbindungszeichenfolge wird aus der zuvor festgelegten Umgebungsvariable `AZURE_STORAGE_CONNECTION_STRING` abgerufen.

# <a name="python-v12"></a>[Python v12](#tab/python)

Der folgende Code erstellt mithilfe der Verbindungszeichenfolge für den Speicher ein `QueueClient`-Objekt.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Der folgende Code erstellt mithilfe der Verbindungszeichenfolge für den Speicher ein `QueueService`-Objekt.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

## <a name="insert-a-message-into-a-queue"></a>Einfügen einer Nachricht in eine Warteschlange

# <a name="python-v12"></a>[Python v12](#tab/python)

Verwenden Sie zum Einfügen einer Nachricht in eine Warteschlange die Methode [send_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-).

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Zum Einfügen einer Nachricht in eine Warteschlange verwenden Sie die [put_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-)-Methode, um eine neue Nachricht zu erstellen und zur Warteschlange hinzuzufügen.

```python
message = u"Hello World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

Azure-Warteschlangennachrichten werden als Text gespeichert. Wenn Sie Binärdaten speichern möchten, richten Sie die Funktionen für die Base64-Codierung und -Decodierung ein, bevor Sie eine Nachricht in die Warteschlange stellen.

# <a name="python-v12"></a>[Python v12](#tab/python)

Konfigurieren Sie die Funktionen für die Base64-Codierung und -Decodierung im Warteschlangenclientobjekt.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Konfigurieren Sie die Funktionen für die Base64-Codierung und -Decodierung im Warteschlangendienstobjekt.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="peek-at-messages"></a>Einsehen von Nachrichten

# <a name="python-v12"></a>[Python v12](#tab/python)

Sie können Nachrichten einsehen, ohne sie aus der Warteschlange zu entfernen. Rufen Sie dazu die Methode [peek_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) auf. Standardmäßig wird von `peek_messages` jeweils nur eine Nachricht angeschaut.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Sie können Nachrichten einsehen, ohne sie aus der Warteschlange zu entfernen. Rufen Sie dazu die Methode [peek_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#peek-messages-queue-name--num-messages-none--timeout-none-) auf. Standardmäßig wird von `peek_messages` jeweils nur eine Nachricht angeschaut.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht einen Task repräsentiert, können Sie dieses Feature verwenden, um den Status des Tasks zu aktualisieren.

# <a name="python-v12"></a>[Python v12](#tab/python)

Der folgende Code verwendet die [update_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-)-Methode zum Aktualisieren einer Nachricht. Das Sichtbarkeitstimeout ist auf 0 festgelegt, d. h., die Nachricht wird sofort angezeigt, und der Inhalt wird aktualisiert.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Der folgende Code verwendet die [update_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-)-Methode zum Aktualisieren einer Nachricht. Das Sichtbarkeitstimeout ist auf 0 festgelegt, d. h., die Nachricht wird sofort angezeigt, und der Inhalt wird aktualisiert.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello World Again")
```

---

## <a name="get-the-queue-length"></a>Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen.

# <a name="python-v12"></a>[Python v12](#tab/python)

Die Methode [get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) ruft Eigenschaften der Warteschlange, wie z. B. `approximate_message_count`, aus dem Warteschlangendienst ab.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Die Methode [get_queue_metadata](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-queue-metadata-queue-name--timeout-none-) ruft Metadaten der Warteschlange, wie z. B. `approximate_message_count`, aus dem Warteschlangendienst ab.

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden.

## <a name="dequeue-messages"></a>Entfernen von Nachrichten aus Warteschlangen

Das Entfernen einer Nachricht aus einer Warteschlange erfolgt in zwei Stufen. Wenn Ihr Code eine Nachricht nicht verarbeiten kann, stellt dieser zweistufige Prozess sicher, dass Sie dieselbe Nachricht erneut erhalten und den Vorgang erneut ausführen können. Rufen Sie nach der erfolgreichen Verarbeitung der Nachricht `delete_message` auf.

# <a name="python-v12"></a>[Python v12](#tab/python)

Wenn Sie [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) aufrufen, wird standardmäßig die nächste Nachricht in der Warteschlange abgerufen. Die für `receive_messages` zurückgegebene Nachricht ist für anderen Code, mit dem Nachrichten aus dieser Warteschlange gelesen werden, nicht mehr sichtbar. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-) aufrufen.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Wenn Sie [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) aufrufen, wird standardmäßig die nächste Nachricht in der Warteschlange abgerufen. Die für `get_messages` zurückgegebene Nachricht ist für anderen Code, mit dem Nachrichten aus dieser Warteschlange gelesen werden, nicht mehr sichtbar. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#delete-message-queue-name--message-id--pop-receipt--timeout-none-) aufrufen.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt.

# <a name="python-v12"></a>[Python v12](#tab/python)

Das folgende Codebeispiel verwendet die Methode [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-), um Nachrichten in Batches abzurufen. Danach wird jede Nachricht in jedem Batch mithilfe einer geschachtelten `for`-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Das folgende Codebeispiel verwendet [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), um 16 Nachrichten in einem einzigen Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer `for`-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Löschen einer Warteschlange

# <a name="python-v12"></a>[Python v12](#tab/python)

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die [delete_queue](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-)-Methode auf.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die [delete_queue](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#delete-queue-queue-name--fail-not-exist-false--timeout-none-)-Methode auf.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Queue Storage vertraut gemacht haben, lesen Sie die folgenden Artikel, um mehr zu erfahren.

- [API-Referenz zu Azure-Warteschlangen für Python](/python/api/azure-storage-queue)
- [Python Developer Center](https://azure.microsoft.com/develop/python/)
- [REST-API für Azure-Speicherdienste](/rest/api/storageservices/)

[Azure Queue Storage-Clientbibliothek für Python]: https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue
[Azure SDK für Python]: https://github.com/azure/azure-sdk-for-python
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
