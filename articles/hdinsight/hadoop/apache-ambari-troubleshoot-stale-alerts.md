---
title: Veraltete Apache Ambari-Warnungen in Azure HDInsight
description: Erörterung und Analyse möglicher Ursachen und Lösungen für veraltete Apache Ambari-Warnungen in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 9564e149055dcabf205694b3cf6c3ddc03163037
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533834"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Szenario: Veraltete Apache Ambari-Warnungen in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Auf der Benutzeroberfläche von Apache Ambari werden unter Umständen Warnungen wie die folgende angezeigt:

![Beispiel für veraltete Apache Ambari-Warnung](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Ursache

Ambari-Agents überwachen kontinuierlich die Integrität zahlreicher Ressourcen. *Warnungen* können konfiguriert werden, um Sie darüber zu informieren, ob bestimmte Clustereigenschaften innerhalb der vordefinierten Schwellenwerte liegen. Nach jeder Ressourcenüberprüfung melden Ambari-Agents den Status an den Ambari-Server und lösen eine Warnung aus, wenn die Warnungsbedingung erfüllt ist. Wenn eine Warnung nicht gemäß dem im Warnungsprofil festgelegten Intervall überprüft wird, löst der Server eine Warnung für *veraltete Ambari-Serverwarnungen* aus.

Es kann verschiedene Gründe dafür geben, dass eine Integritätsprüfung nicht im festgelegten Intervall ausgeführt wird:

* Die Hosts sind stark ausgelastet (hohe CPU-Auslastung), sodass dem Ambari-Agent nicht genügend Systemressourcen zur Verfügung stehen, um die Warnungen rechtzeitig auszuführen.

* Der Cluster ist während einer Periode mit hoher Auslastung mit der Ausführung zahlreicher Aufträge oder Dienste beschäftigt.

* Eine kleine Anzahl von Hosts im Cluster hostet zahlreiche Komponenten und muss daher viele Warnungen ausführen. Wenn sehr viele Komponenten vorhanden sind, kann es vorkommen, dass die geplanten Intervalle von Warnungsaufträgen nicht eingehalten werden.

## <a name="resolution"></a>Lösung

Verwenden Sie die folgenden Methoden, um Probleme mit veralteten Ambari-Warnungen zu beheben:

### <a name="increase-the-alert-interval-time"></a>Erhöhen des Warnungsintervalls

Sie können den Wert eines Warnungsintervalls basierend auf der Antwortzeit und Auslastung Ihres Clusters erhöhen:

1. Wählen Sie auf der Benutzeroberfläche von Apache Ambari die Registerkarte **Alerts** (Warnungen) aus.
1. Wählen Sie den Namen der gewünschten Warnungsdefinition aus.
1. Wählen Sie in der Definition die Option **Edit** (Bearbeiten) aus.
1. Erhöhen Sie den Wert für **Check Interval** (Überprüfungsintervall), und wählen Sie anschließend **Save** (Speichern) aus.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Erhöhen des Warnungsintervalls für Ambari-Serverwarnungen

1. Wählen Sie auf der Benutzeroberfläche von Apache Ambari die Registerkarte **Alerts** (Warnungen) aus.
1. Wählen Sie in der Dropdownliste **Groups** (Guppen) die Option **AMBARI Default** (Ambari-Standard) aus.
1. Wählen Sie die Warnung **Ambari Server Alerts** (Ambari-Serverwarnungen) aus.
1. Wählen Sie in der Definition die Option **Edit** (Bearbeiten) aus.
1. Erhöhen Sie den Wert für **Check Interval** (Überprüfungsintervall).
1. Erhöhen Sie den Wert für **Interval Multiplier** (Intervallmultiplikator), und wählen Sie anschließend **Save** (Speichern) aus.

### <a name="disable-and-reenable-the-alert"></a>Deaktivieren und Reaktivieren der Warnung

Wenn Sie eine veraltete Warnung verwerfen möchten, deaktivieren Sie sie, und aktivieren Sie sie anschließend wieder:

1. Wählen Sie auf der Benutzeroberfläche von Apache Ambari die Registerkarte **Alerts** (Warnungen) aus.
1. Wählen Sie den Namen der gewünschten Warnungsdefinition aus.
1. Wählen Sie in der Definition ganz rechts auf der Benutzeroberfläche die Option **Enabled** (Aktiviert) aus.
1. Wählen Sie im Popupfenster **Confirmation** (Bestätigung) die Option **Confirm Disable** (Deaktivierung bestätigen) aus.
1. Warten Sie einige Sekunden, bis alle auf der Seite angezeigten Instanzen der Warnung gelöscht wurden.
1. Wählen Sie in der Definition ganz rechts auf der Benutzeroberfläche die Option **Disabled** (Deaktiviert) aus.
1. Wählen Sie im Popupfenster **Confirmation** (Bestätigung) die Option **Confirm Enable** (Aktivierung bestätigen) aus.

### <a name="increase-the-alert-grace-period"></a>Erhöhen der Toleranzperiode für die Warnung

Es gibt eine Toleranzperiode, während der ein Ambari-Agent noch nicht meldet, dass der Zeitplan einer konfigurierten Warnung nicht eingehalten wurde. Wenn der Zeitplan der Warnung nicht eingehalten wurde, die Warnung aber innerhalb der Toleranzperiode ausgeführt wird, wird die veraltete Warnung nicht generiert.

Der Standardwert für `alert_grace_period` beträgt 5 Sekunden. Diese Einstellung kann in „/etc/ambari-agent/conf/ambari-agent.ini“ konfiguriert werden. Erhöhen Sie den Wert für Hosts, bei denen es regelmäßig zu veralteten Warnungen kommt, auf „10“. Starten Sie dann den Ambari-Agent neu.

## <a name="next-steps"></a>Nächste Schritte

Falls Ihr Problem hier nicht erwähnt wurde oder Sie es nicht lösen konnten, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Nutzen Sie [@AzureSupport](https://twitter.com/azuresupport) auf Twitter. das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit. Hierüber hat die Azure-Community Zugriff auf die richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie hierzu im Menü des Portals die Hilfeoption ( **?** ) aus, oder öffnen Sie den Bereich **Hilfe und Support** . Weitere Informationen finden Sie unter [Gewusst wie: Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). 

  Support für die Abonnementverwaltung und Abrechnung ist in Ihrem Microsoft Azure-Abonnement inbegriffen. Technischer Support ist über die [Azure-Supportpläne](https://azure.microsoft.com/support/plans/) verfügbar.