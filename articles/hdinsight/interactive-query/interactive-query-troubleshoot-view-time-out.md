---
title: Timeout der Apache Hive-Ansicht bei Abfrageergebnis – Azure HDInsight
description: Timeout der Apache Hive-Ansicht beim Abrufen eines Abfrageergebnisses in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930732"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Szenario: Timeout der Apache Hive-Ansicht beim Abrufen eines Abfrageergebnisses in Azure HDInsight

Dieser Artikel beschreibt Schritte zur Problembehandlung sowie mögliche Lösungen für Probleme bei der Verwendung von Interactive Query-Komponenten in Azure HDInsight-Clustern.

## <a name="issue"></a>Problem

Beim Ausführen bestimmter Abfragen über die Apache Hive-Ansicht tritt unter Umständen der folgende Fehler auf:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Ursache

Der Standardtimeoutwert für die Hive-Ansicht eignet sich möglicherweise nicht für die von Ihnen ausgeführte Abfrage. Der angegebene Zeitraum ist zu kurz, um das Abfrageergebnis über die Hive-Ansicht abzurufen.

## <a name="resolution"></a>Lösung

Erhöhen Sie die Timeouts für die Apache Ambari Hive-Ansicht, indem Sie die folgenden Eigenschaften in `/etc/ambari-server/conf/ambari.properties` festlegen:

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Der Wert von `HIVE_VIEW_INSTANCE_NAME` ist am Ende der URL für die Hive-Ansicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]