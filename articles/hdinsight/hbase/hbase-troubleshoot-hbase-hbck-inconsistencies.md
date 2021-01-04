---
title: hbase hbck gibt Inkonsistenzen in Azure HDInsight zurück
description: hbase hbck gibt Inkonsistenzen in Azure HDInsight zurück
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a3f6a709d3e0f1c71f89473b6ed8435c6eea7e1c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540226"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Szenario: Der Befehl `hbase hbck` gibt Inkonsistenzen in Azure HDInsight zurück

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problem: Die Region ist nicht in `hbase:meta` vorhanden.

Die Region xxx ist in HDFS, jedoch nicht in `hbase:meta` aufgeführt oder auf einem beliebigen Regionsserver bereitgestellt.

### <a name="cause"></a>Ursache

Verschiedene Ursachen.

### <a name="resolution"></a>Lösung

1. Korrigieren Sie die Metatabelle, indem Sie Folgendes ausführen:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Weisen Sie RegionServer Regionen zu, indem Sie Folgendes ausführen:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problem: Die Region ist offline.

Die Region xxx wurde auf keinem RegionServer bereitgestellt. Dies bedeutet, dass die Region in `hbase:meta` vorhanden, aber offline ist.

### <a name="cause"></a>Ursache

Verschiedene Ursachen.

### <a name="resolution"></a>Lösung

Schalten Sie Regionen online, indem Sie Folgendes ausführen:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problem: Regionen haben dieselben Start-/Endschlüssel

### <a name="cause"></a>Ursache

Verschiedene Ursachen.

### <a name="resolution"></a>Lösung

Mergen Sie diese überlappenden Bereiche manuell. Navigieren Sie zum Tabellenabschnitt „HBase HMaster“ der Webbenutzeroberfläche, und wählen Sie den Tabellenlink aus, in dem das Problem aufgetreten ist. Sie sehen den Startschlüssel/Endschlüssel für jede Region, die zu dieser Tabelle gehört. Mergen Sie diese überlappenden Bereiche dann. Führen Sie Folgendes in HBase-Shell aus: `merge_region 'xxxxxxxx','yyyyyyy', true`. Beispiel:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

In diesem Szenario müssen Sie RegionA und RegionC mergen, RegionD mit demselben Schlüsselbereich wie RegionB abrufen und dann RegionB und RegionD mergen. xxxxxxx und yyyyyy sind die Hashzeichenfolge am Ende jedes Regionsnamens. Achten Sie darauf, dass Sie nicht zwei diskontinuierliche Regionen mergen. Nach jedem Mergevorgang (z.B. dem Mergen von A und C) startet HBase eine Komprimierung für RegionD. Warten Sie, bis die Komprimierung abgeschlossen ist, bevor Sie einen weiteren Mergevorgang mit RegionD durchführen. Sie finden den Komprimierungsstatus auf dieser Regionsserverseite in der HBase HMaster-Benutzeroberfläche.

---

## <a name="issue-cant-load-regioninfo"></a>Problem: `.regioninfo` kann nicht geladen werden.

`.regioninfo` kann nicht für die Region `/hbase/data/default/tablex/regiony` geladen werden.

### <a name="cause"></a>Ursache

Dies ist höchstwahrscheinlich auf das teilweise Löschen der Region zurückzuführen, wenn RegionServer abstürzt oder die VM neu gestartet wird. Derzeit ist Azure Storage ein Flatblob-Dateisystem, und einige Dateivorgänge sind nicht atomisch.

### <a name="resolution"></a>Lösung

Bereinigen Sie diese verbleibenden Dateien und Ordner manuell:

1. Führen Sie `hdfs dfs -ls /hbase/data/default/tablex/regiony` aus, um zu überprüfen, welche Ordner/Dateien noch darunter vorhanden sind.

1. Führen Sie `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` aus, um alle untergeordneten Dateien/Ordner zu löschen

1. Führen `hdfs dfs -rmr /hbase/data/default/tablex/regiony` Sie aus, um den Regionsordner zu löschen.

---

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.