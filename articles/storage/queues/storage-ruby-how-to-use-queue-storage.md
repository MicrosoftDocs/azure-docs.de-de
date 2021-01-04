---
title: 'Verwenden von Queue Storage mit Ruby: Azure Storage'
description: Erfahren Sie, wie Sie den Azure-Warteschlangendienst zum Erstellen und Löschen von Warteschlangen sowie zum Einfügen, Abrufen und Löschen von Nachrichten verwenden. Die Beispiele wurden in Ruby geschrieben.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 3acce276a12a0437ad8e1d11f85ceaf40943a4c0
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348269"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Verwenden des Warteschlangenspeichers mit Ruby

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarios mit dem Microsoft Azure-Warteschlangendienst erläutert. Die Beispiele wurden mit der Ruby Azure-API erstellt. Zu den Szenarien gehören das **Einfügen** , **Einsehen** , **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie unter [Erstellen einer Ruby-App in App Service unter Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurieren der Anwendung für den Speicherzugriff

Um den Azure-Speicher zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### <a name="use-rubygems-to-obtain-the-package"></a>Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).
2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### <a name="import-the-package"></a>Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Einrichten einer Azure-Speicherverbindung

Das Azure-Modul liest die Umgebungsvariablen **AZURE\_STORAGE\_ACCOUNT** und **AZURE\_STORAGE\_ACCESS_KEY** nach Informationen aus, die erforderlich sind, um eine Verbindung mit dem Azure-Speicherkonto herzustellen. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::QueueService** mit dem folgenden Code angeben:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

So rufen Sie diese Werte aus einem klassischen oder Resource Manager-Speicherkonto im Azure-Portal ab

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.
3. Klicken Sie auf dem Blatt „Einstellungen“ auf der rechten Seite auf **Zugriffsschlüssel**.
4. Auf dem angezeigten Blatt „Zugriffsschlüssel“ sehen Sie Zugriffsschlüssel 1 und Zugriffsschlüssel 2. Sie können beide verwenden.
5. Klicken Sie auf das Symbol „Kopieren“, um den Schlüssel in die Zwischenablage zu kopieren.

## <a name="how-to-create-a-queue"></a>Erstellen einer Warteschlange

Der folgende Code erstellt ein **Azure::QueueService** -Objekt, das Ihnen das Arbeiten mit Warteschlangen ermöglicht.

```ruby
azure_queue_service = Azure::QueueService.new
```

Mit der **create_queue()** -Methode können Sie eine Warteschlange mit dem angegebenen Namen erstellen.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Einfügen einer Nachricht in eine Warteschlange

Mit der **create_message()** -Methode können Sie eine neue Nachricht erstellen und zur Warteschlange hinzufügen.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Einsehen der nächsten Nachricht

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **peek\_messages()** aufrufen. Standardmäßig wird von **peek\_messages()** jeweils nur eine Nachricht angeschaut. Sie können jedoch auch angeben, wie viele Nachrichten Sie abrufen möchten.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Entfernen der nächsten Nachricht aus der Warteschlange

Sie können eine Nachricht in zwei Schritten aus der Warteschlange entfernen.

1. Wenn Sie **list\_messages()** aufrufen, wird standardmäßig die nächste Nachricht aus der Warteschlange abgerufen. Sie können jedoch auch angeben, wie viele Nachrichten Sie abrufen möchten. Die von **list\_messages()** zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Sie übergeben das Sichtbarkeits-Zeitlimit in Sekunden als Parameter.
2. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **delete_message()** aufrufen.

Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **delete\_message()** direkt nach der Verarbeitung der Nachricht auf.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Der folgende Code verwendet die **update_message()** -Methode zum Aktualisieren einer Nachricht. Der Code gibt ein Tupel zurück, das den Pop-Beleg der Warteschlangennachricht und einen Datums-/Uhrzeitwert in UTC zurück, der angibt, wann die Nachricht in der Warteschlange sichtbar sein wird.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können.

1. Sie können auch einen Nachrichtenstapel abrufen.
2. Außerdem können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt.

Das folgende Codebeispiel verwendet **list\_messages()** , um 15 Nachrichten mit einem Aufruf abzurufen. Anschließend werden die Nachrichten ausgegeben und gelöscht. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode **get\_queue\_metadata()** ruft eine ungefähre Nachrichtenanzahl und Metadaten zur Warteschlange vom Warteschlangendienst ab.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Löschen von Warteschlangen

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **delete\_queue()** für das Warteschlangenobjekt auf.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- Besuchen Sie den [Blog des Azure-Speicherteams](/archive/blogs/windowsazurestorage/)
- Besuchen Sie das [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) -Repository auf GitHub

Einen Vergleich zwischen dem in diesem Artikel erläuterten Azure-Warteschlangendienst und den im Artikel [Verwenden von Service Bus-Warteschlangen](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) erörterten Azure Service Bus-Warteschlangen finden Sie unter [Azure-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).
