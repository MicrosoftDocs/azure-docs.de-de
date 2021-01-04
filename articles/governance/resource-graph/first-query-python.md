---
title: 'Schnellstart: Ihre erste Python-Abfrage'
description: In dieser Schnellstartanleitung führen Sie die Schritte zum Aktivieren der Resource Graph-Bibliothek für Python und zum Ausführen Ihrer ersten Abfrage aus.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 01e0fd5bfbc5f353474c8305eefc044390c3586a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845107"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe von Python

Wenn Sie Azure Resource Graph verwenden möchten, müssen Sie zuerst überprüfen, ob die erforderlichen Bibliotheken für Python installiert sind. In dieser Schnellstartanleitung werden Sie durch den Prozess zum Hinzufügen der Bibliotheken zu Ihrer Python-Installation geführt.

Am Ende dieses Prozesses haben Sie Ihrer Python-Installation die Bibliotheken hinzugefügt und Ihre erste Resource Graph-Abfrage ausgeführt.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Hinzufügen der Resource Graph-Bibliothek

Damit Python Azure Resource Graph abfragen kann, muss die Bibliothek hinzugefügt werden. Diese Bibliothek funktioniert überall dort, wo Python verwendet werden kann, dies schließt [Bash unter Windows 10](/windows/wsl/install-win10) oder eine lokale Installation ein.

1. Überprüfen Sie, ob die aktuelle Python-Version (mindestens **3.8**) installiert ist. Falls sie noch nicht installiert ist, laden Sie die Version von [Python.org](https://www.python.org/downloads/) herunter.

1. Überprüfen Sie, ob die aktuelle Azure CLI-Version (mindestens **2.5.1**) installiert ist. Falls sie noch nicht installiert ist, lesen Sie den Artikel [Installieren der Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI ist erforderlich, um Python für die Verwendung der **CLI-basierten Authentifizierung** in den folgenden Beispielen zu aktivieren. Informationen zu anderen Optionen finden Sie unter [Authentifizieren mit Azure-Verwaltungsbibliotheken für Python](/azure/developer/python/azure-sdk-authenticate).

1. Führen Sie die Authentifizierung über Azure CLI aus.

   ```azurecli
   az login
   ```

1. Installieren Sie in der Python-Umgebung Ihrer Wahl die erforderlichen Bibliotheken für Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Wenn Python für alle Benutzer installiert ist, muss dieser Befehl in einer Konsole mit erhöhten Rechten ausgeführt werden.

1. Überprüfen Sie, ob die Bibliotheken installiert wurden. `azure-mgmt-resourcegraph` sollte mindestens **2.0.0** sein, `azure-mgmt-resource` sollte mindestens **9.0.0** sein, und `azure-cli-core` sollte mindestens **2.5.0** sein.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Ausführen Ihrer ersten Resource Graph-Abfrage

Nachdem die Python-Bibliotheken der Umgebung Ihrer Wahl hinzugefügt wurden, können Sie jetzt eine einfache Resource Graph-Abfrage ausprobieren. Die Abfrage gibt die ersten fünf Azure-Ressourcen mit dem **Namen** und **Ressourcentyp** der einzelnen Ressourcen zurück.

1. Führen Sie Ihre erste Azure Resource Graph-Abfrage mit den installierten Bibliotheken und der `resources`-Methode aus:

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Da dieses Abfragebeispiel keinen Sortierungsmodifizierer wie z.B. `order by` umfasst, ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen.

1. Aktualisieren Sie den Aufruf von `getresources`, und ändern Sie die Abfrage so, dass die Sortierung (`order by`) nach der Eigenschaft **Name** erfolgt:

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Genau wie bei der ersten Abfrage ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen. Die Reihenfolge der Abfragebefehle ist wichtig. In diesem Beispiel kommt `order by` nach `limit`. Durch diese Befehlsreihenfolge werden die Abfrageergebnisse zuerst eingeschränkt und dann sortiert.

1. Aktualisieren Sie den Aufruf von `getresources`, und ändern Sie die Abfrage so, dass zuerst die Sortierung (`order by`) nach der Eigenschaft **Name** und dann die Begrenzung (`limit`) auf die ersten fünf Ergebnisse erfolgt:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Wenn die letzte Abfrage mehrmals ausgeführt wird und in Ihrer Umgebung keine Änderungen vorgenommenen werden, sind die zurückgegebenen Ergebnisse konsistent und nach der Eigenschaft **Name** sortiert, aber immer noch auf die ersten fünf Ergebnisse begrenzt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die installierten Bibliotheken aus Ihrer Python-Umgebung entfernen möchten, können Sie dazu den folgenden Befehl verwenden:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Ihrer Python-Umgebung die Resource Graph-Bibliotheken hinzugefügt und Ihre erste Abfrage ausgeführt. Wenn Sie mehr über die Resource Graph-Sprache erfahren möchten, fahren Sie mit der Seite mit den Details zur Abfragesprache fort.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Abfragesprache](./concepts/query-language.md).
