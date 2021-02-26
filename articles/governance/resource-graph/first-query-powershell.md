---
title: 'Schnellstart: Ihre erste PowerShell-Abfrage'
description: In dieser Schnellstartanleitung führen Sie die Schritte zum Aktivieren des Resource Graph-Moduls für Azure PowerShell und zum Ausführen Ihrer ersten Abfrage aus.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 131bed4fe60035682a317e186f11561bc005b298
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917672"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mit Azure PowerShell

Wenn Sie Azure Resource Graph verwenden möchten, müssen Sie sich zunächst vergewissern, dass das Modul für Azure PowerShell installiert ist. Dieser Schnellstart führt Sie durch das Hinzufügen des Moduls zu Ihrer Azure PowerShell-Installation.

Am Ende dieses Prozesses haben Sie das Modul zu Ihrer Azure PowerShell-Installation hinzugefügt und Ihre erste Resource Graph-Abfrage ausgeführt.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>Hinzufügen des Resource Graph-Moduls

Um Azure PowerShell für die Abfrage von Azure Resource Graph zu aktivieren, muss das Modul hinzugefügt werden. Dieses Modul kann mit einer lokal installierten PowerShell-Instanz, mit [Azure Cloud Shell](https://shell.azure.com) oder mit dem [PowerShell-Docker-Image](https://hub.docker.com/_/microsoft-powershell) verwendet werden.

### <a name="base-requirements"></a>Basisanforderungen

Das Azure Resource Graph-Modul setzt folgende Software voraus:

- Azure PowerShell 1.0.0 oder höher. Falls es noch nicht installiert ist, befolgen Sie [diese Anweisungen](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 oder höher. Falls es nicht installiert oder aktuell ist, befolgen Sie [diese Anweisungen](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Installieren des Moduls

Das Resource Graph-Modul für PowerShell ist **Az.ResourceGraph**.

1. Führen Sie an einer **Administratoreingabeaufforderung** von PowerShell den folgenden Befehl aus:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Vergewissern Sie sich, dass das Modul importiert wurde und die neueste Version (0.7.5) hat:

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Ausführen Ihrer ersten Resource Graph-Abfrage

Nachdem das Azure PowerShell-Modul der gewünschten Umgebung hinzugefügt wurde, können Sie eine einfache Resource Graph-Abfrage ausprobieren. Die Abfrage gibt die ersten fünf Azure-Ressourcen mit dem **Namen** und **Ressourcentyp** der einzelnen Ressourcen zurück.

1. Führen Sie Ihre erste Azure Resource Graph-Abfrage mit dem `Search-AzGraph`-Cmdlet aus:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Da dieses Abfragebeispiel keinen Sortierungsmodifizierer wie z.B. `order by` umfasst, ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen.

1. Aktualisieren Sie die Abfrage auf `order by` der Eigenschaft **Name**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Genau wie bei der ersten Abfrage ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen. Die Reihenfolge der Abfragebefehle ist wichtig. In diesem Beispiel kommt `order by` nach `limit`. Durch diese Befehlsreihenfolge werden die Abfrageergebnisse zuerst eingeschränkt und dann sortiert.

1. Aktualisieren Sie die Abfrage, sodass zuerst eine Sortierung (`order by`) nach der Eigenschaft **Name** vorgenommen wird, und begrenzen Sie die Ergebnisse mithilfe von `limit` auf die fünf relevantesten Ergebnisse:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Wenn die letzte Abfrage mehrmals ausgeführt wird und in Ihrer Umgebung keine Änderungen vorgenommenen werden, sind die zurückgegebenen Ergebnisse konsistent und nach der Eigenschaft **Name** sortiert, aber immer noch auf die ersten fünf Ergebnisse begrenzt.

> [!NOTE]
> Beachten Sie, dass das Cmdlet `Search-AzGraph` standardmäßig Abonnements im Standardkontext verwendet, wenn die Abfrage keine Ergebnisse aus einem Abonnement zurückgibt, auf das Sie bereits Zugriff haben. Wenn Sie die Liste der Abonnement-IDs anzeigen möchten, die Teil des Standardkontexts sind, führen Sie `(Get-AzContext).Account.ExtendedProperties.Subscriptions` aus. Wenn Sie alle Abonnements durchsuchen möchten, auf die Sie Zugriff haben, können Sie „PSDefaultParameterValues“ für das Cmdlet `Search-AzGraph` durch Ausführen von `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}` festlegen.
   
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Resource Graph-Modul aus der Azure PowerShell-Umgebung entfernen möchten, verwenden Sie hierfür folgenden Befehl:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Dies löscht nicht die zuvor heruntergeladene Moduldatei. Sie wird nur aus der ausgeführten PowerShell-Umgebung entfernt.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie das Resource Graph-Modul zu ihrer Azure PowerShell-Umgebung hinzugefügt und Ihre erste Abfrage ausgeführt. Wenn Sie mehr über die Resource Graph-Sprache erfahren möchten, fahren Sie mit der Seite mit den Details zur Abfragesprache fort.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Abfragesprache](./concepts/query-language.md).