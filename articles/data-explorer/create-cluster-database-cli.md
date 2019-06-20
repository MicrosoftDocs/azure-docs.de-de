---
title: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank über die Azure-Befehlszeilenschnittstelle
description: Hier erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle einen Azure Data Explorer-Cluster und eine Datenbank erstellen.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e771def95db00b5de8c27011641a628560952970
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494801"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Erstellen eines Azure Data Explorer-Clusters und einer Datenbank über die Azure-Befehlszeilenschnittstelle

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. In diesem Artikel erstellen Sie einen Cluster und eine Datenbank über die Azure-Befehlszeilenschnittstelle.

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel benötigen Sie ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Azure-Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens die Azure CLI-Version 2.0.4 verwenden. Führen Sie `az --version` aus, um Ihre Version zu überprüfen. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Konfigurieren der CLI-Parameter

Die folgenden Schritte sind nicht erforderlich, wenn Sie Befehle in Azure Cloud Shell ausführen. Wenn Sie die Befehlszeilenschnittstelle lokal ausführen, gehen Sie wie folgt vor, um sich bei Azure anzumelden und Ihr aktuelles Abonnement festzulegen:

1. Führen Sie den folgenden Befehl aus, um sich bei Azure anzumelden:

    ```azurecli-interactive
    az login
    ```

1. Legen Sie das Abonnement fest, in dem der Cluster erstellt werden soll. Ersetzen Sie `MyAzureSub` durch den Namen des gewünschten Azure-Abonnements:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Erstellen des Azure Data Explorer-Clusters

1. Erstellen Sie Ihren Cluster mit dem folgenden Befehl:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | name | *azureclitest* | Der gewünschte Name Ihres Clusters.|
   | sku | *D13_v2* | Die SKU, die für Ihren Cluster verwendet wird. |
   | resource-group | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |

    Sie können auch noch weitere optionale Parameter verwenden, etwa die Kapazität des Clusters.

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich erstellt wurde:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Wenn das Ergebnis `provisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erfolgreich erstellt.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Erstellen der Datenbank im Azure Data Explorer-Cluster

1. Erstellen Sie Ihre Datenbank mit dem folgenden Befehl:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | cluster-name | *azureclitest* | Der Name Ihres Clusters, in dem die Datenbank erstellt werden soll.|
   | name | *clidatabase* | Der Name Ihrer Datenbank.|
   | resource-group | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |
   | soft-delete-period | *P365D* | Gibt den Zeitraum an, wie lange Daten für Abfragen verfügbar sein sollen. Weitere Informationen finden Sie unter [Aufbewahrungsrichtlinie](/azure/kusto/concepts/retentionpolicy). |
   | hot-cache-period | *P31D* | Gibt den Zeitraum an, wie lange Daten im Cache verfügbar sein sollen. Weitere Informationen finden Sie unter [Cacherichtlinie](/azure/kusto/concepts/cachepolicy). |

1. Führen Sie den folgenden Befehl aus, um die erstellte Datenbank anzuzeigen:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Jetzt verfügen Sie über einen Cluster und eine Datenbank.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

* Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei.
* Löschen Sie den Cluster, um Ressourcen zu bereinigen. Wenn Sie einen Cluster löschen, werden auch alle darin enthaltenen Datenbanken gelöscht. Verwenden Sie den folgenden Befehl, um Ihren Cluster zu löschen:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python](python-ingest-data.md)
