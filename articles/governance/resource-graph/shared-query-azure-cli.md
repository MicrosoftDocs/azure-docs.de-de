---
title: 'Schnellstart: Erstellen einer freigegebenen Abfrage mit der Azure CLI'
description: In dieser Schnellstartanleitung führen Sie die Schritte zum Aktivieren der Resource Graph-Erweiterung für die Azure CLI und zum Erstellen einer freigegebenen Abfrage aus.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ec1b010771790339a13777624f04c7bd2db01f11
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594382"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Schnellstart: Erstellen einer freigegebenen Resource Graph-Abfrage mithilfe der Azure CLI

Der erste Schritt der Verwendung von Azure Resource Graph mit der [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure/) ist, zu überprüfen, ob die Erweiterung installiert ist. Dieser Schnellstart führt Sie durch das Hinzufügen der Erweiterung zu Ihrer Azure-Befehlszeilenschnittstelle. Sie können die Erweiterung mit lokal installierter Azure CLI oder über die [Azure Cloud Shell](https://shell.azure.com) verwenden.

Am Ende dieses Prozesses haben Sie die Erweiterung der Installation der Azure-Befehlszeilenschnittstelle Ihrer Wahl hinzugefügt und erstellen eine freigegebene Resource Graph-Abfrage.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Hinzufügen der Resource Graph-Erweiterung

Um die Azure-Befehlszeilenschnittstelle für die Arbeit mit Azure Resource Graph zu aktivieren, muss die Erweiterung hinzugefügt werden. Diese Erweiterung funktioniert überall, wo die Azure-Befehlszeilenschnittstelle verwendet werden kann, einschließlich [bash unter Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (sowohl eigenständig als auch innerhalb des Portals), [Azure CLI-Docker-Image](https://hub.docker.com/_/microsoft-azure-cli) oder lokaler Installation.

1. Überprüfen Sie, ob die aktuelle Azure CLI-Version (mindestens **2.8.0**) installiert ist. Falls es noch nicht installiert ist, befolgen Sie [diese Anweisungen](/cli/azure/install-azure-cli-windows).

1. Verwenden Sie in der Azure CLI-Umgebung Ihrer Wahl [az extension add](/cli/azure/extension#az_extension_add), um die Resource Graph-Erweiterung mit dem folgenden Befehl zu importieren:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Überprüfen Sie, ob die Erweiterung installiert wurde und in der erwarteten Version vorliegt (mindestens **1.1.0**) mit [az extension list](/cli/azure/extension#az_extension_list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Erstellen einer freigegebenen Resource Graph-Abfrage

Nachdem die Azure CLI-Erweiterung der gewünschten Umgebung hinzugefügt wurde, können Sie eine freigegebenen Resource Graph-Abfrage erstellen. Bei der freigegebenen Abfrage handelt es sich um ein Azure Resource Manager Objekt, dem Sie Berechtigung für den Azure Resource Graph-Explorer erteilen oder das Sie im Azure Resource Graph-Explorer ausführen können. Die Abfrage fasst die Anzahl aller Ressourcen zusammen, gruppiert nach _Standort_.

1. Erstellen Sie eine Ressourcengruppe mit [az group create](/cli/azure/group#az_group_create), um die freigegebene Azure Resource Graph-Abfrage zu speichern. Diese Ressourcengruppe hat den Namen `resource-graph-queries`, und der Standort ist `westus2`.

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Erstellen Sie die freigegebene Azure Resource Graph-Abfrage mit der `graph`-Erweiterung und dem Befehl [az graph shared-query create](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_create):

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Listet die freigegebenen Abfragen in der neuen Ressourcengruppe auf. Der Befehl [az graph shared-query list](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_list) gibt ein Array von Werten zurück.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Um nur eine einzelne freigegebene Abfrage als Ergebnis zu erhalten, verwenden Sie Befehl [az graph shared-query show](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_show).

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Führen Sie die freigegebene Abfrage in Azure CLI mit der `{{shared-query-uri}}`-Syntax in einem [az graph query](/cli/azure/ext/resource-graph/graph#ext_resource_graph_az_graph_query)-Befehl aus.
   Kopieren Sie zunächst das Feld `id` aus dem Ergebnis des vorherigen `show`-Befehls. Ersetzen Sie den `shared-query-uri`-Text im Beispiel durch den Wert aus dem Feld `id`, aber behalten Sie die umgebenden Zeichen `{{` und `}}` bei.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > Die `{{shared-query-uri}}`-Syntax ist ein **Previewfunktion**.

Eine weitere Möglichkeit, freigegebene Resource Graph-Abfragen zu finden, ist das Azure-Portal. Suchen Sie über die Suchleiste des Portals nach „Resource Graph-Abfragen“. Wählen Sie die freigegebene Abfrage aus. Auf der Seite **Übersicht** wird die gespeicherte Abfrage auf der Registerkarte **Abfrage** angezeigt. Mit der Schaltfläche **Bearbeiten** können Sie die Abfrage im [Resource Graph-Explorer](./first-query-portal.md) öffnen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die freigegebene Resource Graph-Abfrage, Resource Graph-Ressourcengruppe und Resource Graph-Erweiterung aus der Azure CLI-Umgebung entfernen möchten, verwenden Sie hierfür folgende Befehle:

- [az graph shared-query delete](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [az extension remove](/cli/azure/extension#az_extension_remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie die Resource Graph-Erweiterung Ihrer Azure CLI-Umgebung hinzugefügt und eine freigegebene Abfrage ausgeführt. Wenn Sie mehr über die Resource Graph-Sprache erfahren möchten, fahren Sie mit der Seite mit den Details zur Abfragesprache fort.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Abfragesprache](./concepts/query-language.md).