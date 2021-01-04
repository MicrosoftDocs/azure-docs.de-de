---
title: Durch Joins in Apache Hive wird ein OutOfMemory-Fehler verursacht – Azure HDInsight
description: Behandeln von OutOfMemory-Fehlern vom Typ „Limit für GC-Overhead überschritten"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: c227758e31b3b17768b8140475872245b2f34e52
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532950"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Szenario: Durch Joins in Apache Hive wird ein OutOfMemory-Fehler in Azure HDInsight verursacht

Dieser Artikel beschreibt Schritte zur Problembehandlung sowie mögliche Lösungen für Probleme bei der Verwendung von Interactive Query-Komponenten in Azure HDInsight-Clustern.

## <a name="issue"></a>Problem

Von Apache Hive-Joins wird standardmäßig der gesamte Inhalt einer Tabelle in den Arbeitsspeicher geladen, damit ein Join ohne Zuordnung/Reduzierung ausgeführt werden kann. Passt die Hive-Tabelle nicht in den Arbeitsspeicher, ist die Abfrage unter Umständen nicht erfolgreich.

## <a name="cause"></a>Ursache

Beim Ausführen entsprechend großer Join-Vorgänge tritt der folgende Fehler auf:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Lösung

Stellen Sie durch Festlegen des folgenden Hive-Konfigurationswerts sicher, dass Hive bei Join-Vorgängen keine Tabellen in den Arbeitsspeicher lädt, und führen Sie stattdessen einen Zuordnungs-/Reduzierungsschritt aus:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Nächste Schritte

Sollte das Problem durch Festlegen dieses Werts nicht behoben werden, verwenden Sie eine der folgenden Optionen:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Nutzen Sie [@AzureSupport](https://twitter.com/azuresupport) – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit. Hierüber hat die Azure-Community Zugriff auf die richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.