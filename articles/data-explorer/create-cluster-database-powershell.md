---
title: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mithilfe von PowerShell
description: Hier erfahren Sie, wie Sie mithilfe von PowerShell einen Azure Data Explorer-Cluster und eine Datenbank erstellen.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c8cc81b3c61e2faf65bf8a06c69ca7f2b906c9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497325"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mithilfe von PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. In diesem Artikel erstellen Sie einen Cluster und eine Datenbank mit PowerShell. Sie können PowerShell-Cmdlets und -Skripts unter Windows oder Linux oder in [Azure Cloud Shell](../cloud-shell/overview.md) mit [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) ausführen, um Azure Data Explorer-Cluster und -Datenbanken zu erstellen und zu konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Azure CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens die Azure CLI-Version 2.0.4 verwenden. Führen Sie `az --version` aus, um Ihre Version zu überprüfen. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Konfigurieren von Parametern

Die folgenden Schritte sind nicht erforderlich, wenn Sie Befehle in Azure Cloud Shell ausführen. Wenn Sie die Befehlszeilenschnittstelle lokal ausführen, führen Sie Schritt 1 und 2 aus, um sich bei Azure anzumelden und Ihr aktuelles Abonnement festzulegen:

1. Führen Sie den folgenden Befehl aus, um sich bei Azure anzumelden:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Legen Sie das Abonnement fest, in dem der Cluster erstellt werden soll:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Wenn Sie die Azure CLI lokal oder in Azure Cloud Shell ausführen, müssen Sie das Az.Kusto-Modul auf Ihrem Gerät installieren:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Erstellen des Azure Data Explorer-Clusters

1. Erstellen Sie Ihren Cluster mit dem folgenden Befehl:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | NAME | *mykustocluster* | Der gewünschte Name Ihres Clusters.|
   | Sku | *D13_v2* | Die SKU, die für Ihren Cluster verwendet wird. |
   | ResourceGroupName | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |

    Sie können auch noch weitere optionale Parameter verwenden, etwa die Kapazität des Clusters.

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich erstellt wurde:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Wenn das Ergebnis `provisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erfolgreich erstellt.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Erstellen der Datenbank im Azure Data Explorer-Cluster

1. Erstellen Sie Ihre Datenbank mit dem folgenden Befehl:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | ClusterName | *mykustocluster* | Der Name Ihres Clusters, in dem die Datenbank erstellt werden soll.|
   | NAME | *mykustodatabase* | Der Name Ihrer Datenbank.|
   | ResourceGroupName | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |
   | SoftDeletePeriod | *3650:00:00:00* | Der Zeitraum, für den Daten für Abfragen verfügbar sein sollen. |
   | HotCachePeriod | *3650:00:00:00* | Der Zeitraum, für den Daten im Cache verfügbar sein sollen. |

1. Führen Sie den folgenden Befehl aus, um die erstellte Datenbank anzuzeigen:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Jetzt verfügen Sie über einen Cluster und eine Datenbank.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

* Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei.
* Löschen Sie den Cluster, um Ressourcen zu bereinigen. Wenn Sie einen Cluster löschen, werden auch alle darin enthaltenen Datenbanken gelöscht. Verwenden Sie den folgenden Befehl, um Ihren Cluster zu löschen:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Zusätzliche Az.Kusto-Befehle](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Erfassen von Daten mit dem .NET Standard SDK für Azure Data Explorer (Vorschauversion)](net-standard-ingest-data.md)
