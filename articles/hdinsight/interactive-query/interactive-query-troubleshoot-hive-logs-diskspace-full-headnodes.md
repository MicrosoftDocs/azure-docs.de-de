---
title: 'Problembehandlung: Apache Hive-Protokolle belegen den gesamten Speicherplatz – Azure HDInsight'
description: In diesem Artikel finden Sie Schritte zur Problembehandlung, wenn Apache Hive-Protokolle den gesamten Speicherplatz auf den Hauptknoten in Azure HDInsight belegen.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 107ec012bf2ff76ee1cbe4c5f8252566a5a16127
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288934"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Szenario: Apache Hive-Protokolle belegen den gesamten Speicherplatz auf den Hauptknoten in Azure HDInsight

In diesem Artikel werden die Problembehandlungsschritte sowie mögliche Problemlösungen bei nicht ausreichendem Speicherplatz auf den Hauptknoten in Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

In einem Apache Hive/LLAP-Cluster nehmen unerwünschte Protokolle den gesamten Speicherplatz auf den Hauptknoten ein. Diese Bedingung kann folgende Probleme verursachen:

- Beim SSH-Zugriff tritt ein Fehler auf, weil auf dem Hauptknoten kein Speicherplatz mehr vorhanden ist.
- Ambari löst den Fehler *HTTP-FEHLER 503: Dienst nicht verfügbar* aus.
- Fehler beim Neustart von HiveServer2 Interactive.

Die `ambari-agent`-Protokolle enthalten beim Auftreten des Problems die folgenden Einträge:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Ursache

In erweiterten Hive-log4j-Konfigurationen gibt der aktuelle Standardlöschzeitplan vor, dass Dateien, die älter als 30 Tage sind (basierend auf dem Datum der letzten Änderung), gelöscht werden.

## <a name="resolution"></a>Lösung

1. Navigieren Sie im Ambari-Portal zur Hive-Komponentenübersicht, und wählen Sie die Registerkarte **Konfigurationen** aus.

2. Wechseln Sie unter **Erweiterte Einstellungen** zum Abschnitt `Advanced hive-log4j`.

3. Legen Sie den Parameter `appender.RFA.strategy.action.condition.age` auf ein Alter Ihrer Wahl fest. In diesem Beispiel wird das Alter auf 14 Tage festgelegt: `appender.RFA.strategy.action.condition.age = 14D`

4. Wenn keine zugehörigen Einstellungen angezeigt werden, fügen Sie die folgenden Einstellungen an:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Legen Sie `hive.root.logger` auf `INFO,RFA` fest, wie im folgenden Beispiel gezeigt. Die Standardeinstellung ist `DEBUG`, was zu großen Protokollen führt.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Speichern Sie die Konfigurationen, und starten Sie die erforderlichen Komponenten neu.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
