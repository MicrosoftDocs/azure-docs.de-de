---
title: Neustarten von VMs für Azure HDInsight-Cluster
description: In diesem Artikel erfahren Sie, wie Sie nicht reagierende VMs für Azure HDInsight-Cluster neu starten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: 725b616fec9c2bc4a0540a7941098377e01732e2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546465"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>Neustarten von VMs für HDInsight-Cluster

Azure HDInsight-Cluster enthalten Gruppen von virtuellen Computern (Virtual Machines, VMs) als Clusterknoten. Bei lange in Betrieb befindlichen Clustern ist es möglich, dass diese Knoten aus verschiedenen Gründen nicht mehr reagieren. In diesem Artikel wird beschrieben, wie Sie nicht reagierende VMs in einem HDInsight-Cluster neu starten.

## <a name="when-to-reboot"></a>Situationen, in denen ein Neustart erforderlich ist

> [!WARNING]
> Wenn Sie VMs in einem Cluster neu starten, ist der Knoten nicht mehr verwendbar, und die Dienste auf dem Knoten müssen neu gestartet werden.

Bei einem Neustart eines Knotens wird der Cluster möglicherweise fehlerhaft, und Aufträge können verlangsamt werden oder fehlschlagen. Wenn Sie versuchen, den aktiven Hauptknoten neu zu starten, werden alle Aufträge, die derzeit ausgeführt werden, angehalten. Sie können erst dann wieder Aufträge an den Cluster übermitteln, wenn die Dienste wieder betriebsbereit sind. Aus diesen Gründen sollten Sie VMs nur bei Bedarf neu starten. Sie sollten VMs neu starten, wenn Folgendes zutrifft:

- Sie können über SSH keine Verbindung mit dem Knoten herstellen, dieser reagiert jedoch auf Pings.
- Der Workerknoten ist ausgefallen und hat keinen Heartbeat in der Ambari-Benutzeroberfläche.
- Der temporäre Datenträger auf dem Knoten ist voll.
- Die Prozesstabelle auf der VM enthält viele Einträge, in denen der Prozess abgeschlossen wurde, jedoch mit dem Status „Beendet“ aufgeführt ist.

> [!NOTE]
> Das Neustarten von VMs wird für **HBase** - und **Kafka** -Cluster nicht unterstützt, da das Neustarten zu Datenverlusten führen kann.

## <a name="use-powershell-to-reboot-vms"></a>Verwenden von PowerShell zum Neustarten von VMs

Es sind zwei Schritte erforderlich, um den Neustartvorgang für Knoten auszuführen: Auflisten von Knoten und Neustarten von Knoten.

1. Auflisten von Knoten. Sie können die Liste der Clusterknoten mit [Get-AzHDInsightHost](/powershell/module/az.hdinsight/get-azhdinsighthost) abrufen.

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. Neustarten von Hosts. Nachdem Sie die Namen der Knoten abgerufen haben, die Sie neu starten möchten, starten Sie die Knoten über [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost) neu.

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>Verwenden einer REST-API, um VMs neu zu starten

Sie können das Feature **Ausprobieren** in der API-Dokumentation verwenden, um Anforderungen an HDInsight zu senden. Es sind zwei Schritte erforderlich, um den Neustartvorgang für Knoten auszuführen: Auflisten von Knoten und Neustarten von Knoten.

1. Auflisten von Knoten. Sie können die Liste der Clusterknoten über die REST-API oder in Ambari abrufen. Weitere Informationen finden Sie unter [Virtuelle Computer: Auflisten von Hosts](/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. Neustarten von Hosts. Nachdem Sie die Namen der Knoten abgerufen haben, die Sie neu starten möchten, starten Sie die Knoten neu, indem Sie dazu die REST-API verwenden. Für den Namen eines Knotens gilt folgendes Muster: *Knotentyp(wn/hn/zk/gw)*  + *x* + *erste sechs Zeichen des Clusternamens* . Weitere Informationen finden Sie unter [Virtuelle Computer: Neustarten von Hosts](/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Die tatsächlichen Namen der Knoten, die Sie neu starten möchten, sind in einem JSON-Array im Anforderungstext angegeben.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Nächste Schritte

* [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [REST-API für HDInsight-VMs](/rest/api/hdinsight/virtualmachines)
* [HDInsight-REST-API](/rest/api/hdinsight/)