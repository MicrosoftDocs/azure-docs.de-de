---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 5c2959a1bf6225c164f8538c3c437e464d834b96
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026511"
---
## <a name="create-a-ruby-application"></a>Erstellen einer Ruby-Anwendung
Anweisungen finden Sie unter [Ruby on Rails-Webanwendung auf Azure VM](/previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Um Service Bus zu verwenden, müssen Sie das Azure-Ruby-Paket herunterladen und verwenden. Dieses enthält eine Reihe von Bibliotheken, die mit den Speicher-REST-Diensten kommunizieren.

### <a name="use-rubygems-to-obtain-the-package"></a>Verwenden von RubyGems zum Abrufen des Pakets
1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).
2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### <a name="import-the-package"></a>Importieren des Pakets
Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Einrichten einer Service Bus-Verbindung
Verwenden Sie den folgenden Code, um die Werte für Namespace, Schlüsselname, Schlüssel, Signaturgeber und Host festzulegen:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Legen Sie den Wert für den Namespace auf den von Ihnen erstellten Wert statt auf die gesamte URL fest. Verwenden Sie beispielsweise **Beispielnamespace**, anstelle von „Beispielnamespace.servicebus.windows.net“.

Beim Arbeiten mit mehreren Namespaces können Sie den Schlüssel und seinen Namen beim Erstellen von `SharedAccessSigner`-Objekten an den Konstruktor übergeben.

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```