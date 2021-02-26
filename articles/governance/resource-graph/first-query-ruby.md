---
title: 'Schnellstart: Ihre erste Ruby-Abfrage'
description: In dieser Schnellstartanleitung führen Sie die Schritte zum Aktivieren des Resource Graph-Gems für Ruby und zum Ausführen Ihrer ersten Abfrage aus.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 41769359eaa1f930d34b0a182f35da38a6f5109d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920036"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe von Ruby

Wenn Sie Azure Resource Graph verwenden möchten, müssen Sie zuerst überprüfen, ob die erforderlichen Gems für Ruby installiert sind. In dieser Schnellstartanleitung werden Sie durch den Prozess zum Hinzufügen der Gems zu Ihrer Ruby-Installation geführt.

Am Ende dieses Prozesses haben Sie Ihrer Ruby-Installation die Gems hinzugefügt und Ihre erste Resource Graph-Abfrage ausgeführt.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Ein Azure-Dienstprinzipal, einschließlich _clientId_ und _clientSecret_.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Erstellen des Resource Graph-Projekts

Damit Ruby Azure Resource Graph abfragen kann, muss der Gem zur `Gemfile` hinzugefügt werden. Dieses Gem funktioniert überall dort, wo Ruby verwendet werden kann, einschließlich [Azure Cloud Shell](https://shell.azure.com), [Bash unter Windows 10](/windows/wsl/install-win10) oder lokal installiert.

1. Überprüfen Sie, ob die aktuelle Ruby-Version (mindestens **2.7.1**) installiert ist. Falls sie noch nicht installiert ist, laden Sie die Version von [Ruby-Lang.org](https://www.ruby-lang.org/en/downloads/) herunter.

1. Initialisieren Sie in der Ruby-Umgebung Ihrer Wahl ein Paket in einem neuen Projektordner:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Aktualisieren Sie Ihre `Gemfile` mit den Gems, die für Azure Resource Graph benötigt werden. Die aktualisierte Datei sollte in etwa wie in diesem Beispiel aussehen:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. Führen Sie vom Projektordner aus `bundle install` aus. Bestätigen Sie mit `bundle list`, dass die Gems installiert wurden.

1. Erstellen Sie im gleichen Projektordner `argQuery.rb` mit dem folgenden Code, und speichern Sie die aktualisierte Datei:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>Ausführen Ihrer ersten Resource Graph-Abfrage

Wenn das Ruby-Skript gespeichert und einsatzbereit ist, ist es an der Zeit, eine einfache Resource Graph-Abfrage auszuprobieren. Die Abfrage gibt die ersten fünf Azure-Ressourcen mit dem **Namen** und **Ressourcentyp** der einzelnen Ressourcen zurück.

Jeder Aufruf von `argQuery` enthält Variablen, die Sie durch Ihre eigenen Werte ersetzen müssen:

- Ersetzen Sie `{tenantId}` durch Ihre Mandanten-ID.
- `{clientId}`: Ersetzen Sie diese ID durch die Client-ID Ihres Dienstprinzipals.
- `{clientSecret}`: Ersetzen Sie dieses Geheimnis durch den geheimen Clientschlüssel Ihres Dienstprinzipals.
- Ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID.

1. Wechseln Sie in den Projektordner, in dem Sie die Dateien `Gemfile` und `argClient.rb` erstellt haben.

1. Führen Sie Ihre erste Azure Resource Graph-Abfrage mit den Gems und der `resources`-Methode aus:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Da dieses Abfragebeispiel keinen Sortierungsmodifizierer wie z.B. `order by` umfasst, ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen.

1. Ändern Sie den letzten Parameter in `argQuery.rb`, und ändern Sie die Abfrage so, dass die Sortierung (`order by`) nach der Eigenschaft **Name** erfolgt:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Genau wie bei der ersten Abfrage ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen. Die Reihenfolge der Abfragebefehle ist wichtig. In diesem Beispiel kommt `order by` nach `limit`. Durch diese Befehlsreihenfolge werden die Abfrageergebnisse zuerst eingeschränkt und dann sortiert.

1. Ändern Sie den letzten Parameter in `argQuery.rb`, und ändern Sie die Abfrage so, dass zuerst die Sortierung (`order by`) nach der Eigenschaft **Name** und dann die Begrenzung (`limit`) auf die ersten fünf Ergebnisse erfolgt:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Wenn die letzte Abfrage mehrmals ausgeführt wird und in Ihrer Umgebung keine Änderungen vorgenommenen werden, sind die zurückgegebenen Ergebnisse konsistent und nach der Eigenschaft **Name** sortiert, aber immer noch auf die ersten fünf Ergebnisse begrenzt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die installierten Gems aus Ihrer Ruby-Umgebung entfernen möchten, können Sie dazu den folgenden Befehl verwenden:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> Das Gem `azure_mgmt_resourcegraph` hat Abhängigkeiten wie `ms_rest` und `ms_rest_azure`, die je nach Ihrer Umgebung möglicherweise auch installiert wurden. Sie können diese Gems auch deinstallieren, wenn sie nicht mehr benötigt werden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Ihrer Ruby-Umgebung die Resource Graph-Gems hinzugefügt und Ihre erste Abfrage ausgeführt. Wenn Sie mehr über die Resource Graph-Sprache erfahren möchten, fahren Sie mit der Seite mit den Details zur Abfragesprache fort.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Abfragesprache](./concepts/query-language.md).
