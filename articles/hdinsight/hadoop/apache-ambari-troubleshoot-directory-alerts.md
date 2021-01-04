---
title: Apache Ambari-Verzeichniswarnungen in Azure HDInsight
description: Erörterung und Analyse möglicher Ursachen und Lösungen für Apache Ambari-Verzeichniswarnungen in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: d8b1f705eb08d1c58b32e0cbd7c57722a6de93cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998310"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Szenario: Apache Ambari-Verzeichniswarnungen in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Sie erhalten Fehlermeldungen von Apache Ambari, die wie folgt aussehen:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Ursache

Die in der Ambari-Warnung genannten Verzeichnisse fehlen auf den betroffenen Workerknoten.

## <a name="resolution"></a>Lösung

Erstellen Sie die fehlenden Verzeichnisse manuell auf den betroffenen Workerknoten.

1. Stellen Sie eine SSH-Verbindung mit dem jeweiligen Workerknoten her.

1. Rufen Sie den Root-Benutzer ab: `sudo su`.

1. Erstellen Sie rekursiv die erforderlichen Verzeichnisse.

1. Ändern Sie den Besitzer und die Gruppe für diese Verzeichnisse.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Deaktivieren Sie die Warnung auf der Apache Ambari-Benutzeroberfläche, und dann aktivieren Sie die Warnung.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]