---
title: Lokales HDFS ist in einem Azure HDInsight-Cluster im abgesicherten Modus hängen geblieben.
description: Problembehandlung, wenn das lokale Apache HDFS im abgesicherten Modus auf einem Apache-Cluster in Azure HDInsight hängen bleibt
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 0f4a7702c4f85c162b0485e06cabc99b963ff210
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014702"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Szenario: Lokales HDFS ist in einem Azure HDInsight-Cluster im abgesicherten Modus hängen geblieben.

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Das lokale Apache HDFS (Hadoop Distributed File System) ist im abgesicherten Modus auf dem HDInsight-Cluster hängen geblieben. Sie erhalten eine Fehlermeldung wie die folgende:

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Ursache

Der HDInsight-Cluster wurde auf sehr wenige Knoten zentral herunterskaliert, oder die Anzahl der Knoten liegt nahe am HDFS-Replikationsfaktor.

## <a name="resolution"></a>Lösung

1. Erstellen Sie mithilfe des folgenden Befehls einen HDFS-Statusbericht für den HDInsight-Cluster:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Überprüfen Sie mithilfe des folgenden Befehls die HDFS-Integrität im HDInsight-Cluster:

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Wenn festgestellt wird, dass keine Blöcke fehlen, beschädigt oder unterrepliziert sind, bzw. diese Blöcke ignoriert werden können, führen Sie den folgenden Befehl aus, um den Namensknoten aus dem abgesicherten Modus herauszunehmen:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]