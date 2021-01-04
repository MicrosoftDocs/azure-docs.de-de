---
title: 'Gewusst wie: Löschen eines HDInsight-Clusters – Azure'
description: Informationen zu den verschiedenen Möglichkeiten, einen Azure HDInsight-Cluster zu löschen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 49a43f821a159af6944abb9509c24a8dd081be43
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748796"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Löschen eines HDInsight-Clusters mithilfe von Browser, PowerShell oder Azure-Befehlszeilenschnittstelle

Die Abrechnung für einen HDInsight-Cluster beginnt, sobald der Cluster erstellt wurde, und endet mit dem Löschen des Clusters. Die Gebühren werden anteilig nach Minuten erhoben. Daher sollten Sie Ihren Cluster immer löschen, wenn Sie ihn nicht mehr verwenden. In diesem Artikel erfahren Sie, wie Sie einen Cluster mithilfe des [Azure-Portals](https://portal.azure.com), des [Azure PowerShell Az-Modul](/powershell/azure/) und der [Azure CLI](/cli/azure/) löschen.

> [!IMPORTANT]  
> Das Löschen eines HDInsight-Clusters führt nicht zum Löschen der zugeordneten Azure Storage-Konten oder Data Lake Storage-Instanz. Sie können die in diesen Diensten gespeicherten Daten künftig wiederverwenden.

## <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie vom linken Menü zu **Alle Dienste** > **Analytics** > **HDInsight-Cluster** , und wählen Sie Ihren Cluster aus.

3. Wählen Sie in der Standardansicht das Symbol **Löschen** aus. Folgen Sie der Aufforderung, Ihren Cluster zu löschen.

    ![Schaltfläche zum Löschen des HDInsight-Clusters](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Ersetzen Sie im folgenden Code `CLUSTERNAME` durch den Namen Ihres HDInsight-Clusters. Geben Sie über eine PowerShell-Eingabeaufforderung den folgenden Befehl ein, um den Cluster zu löschen:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Ersetzen Sie im folgenden Code `CLUSTERNAME` durch den Namen Ihres HDInsight-Clusters und `RESOURCEGROUP` durch den Namen Ihrer Ressourcengruppe.  Geben Sie über eine Eingabeaufforderung Folgendes ein, um den Cluster zu löschen:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
