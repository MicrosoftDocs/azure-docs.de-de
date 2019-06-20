---
title: Verstehen und Beheben von WebHCat-Fehlern in HDInsight – Azure
description: Erfahren Sie mehr zu Ursachen und Lösungen von Fehlern, die WebHCat in HDInsight zurückgibt.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: cfbd42a67f9c9d6c66df3787b53575dc9e918e35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067987"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Verstehen und Beheben von Fehlern in HDInsight, die von WebHCat ausgegeben wurden

Hier erhalten Sie Informationen zu Fehlern, die bei Verwendung von WebHCat mit HDInsight auftreten können, und zu deren Behebung. WebHCat wird in clientseitigen Tools wie Azure PowerShell oder den Data Lake-Tools für Visual Studio intern verwendet.

## <a name="what-is-webhcat"></a>Was ist WebHCat?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) ist eine REST-API für [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), eine Tabellen- und Speicherverwaltungsebene für Apache Hadoop. WebHCat ist in HDInsight-Clustern standardmäßig aktiviert und wird von verschiedenen Tools zum Übermitteln von Aufträgen, Abfragen des Auftragsstatus und ähnlichem verwendet, ohne dass eine Anmeldung beim Cluster erforderlich ist.

## <a name="modifying-configuration"></a>Konfigurationsänderungen

> [!IMPORTANT]  
> Einige der in diesem Dokument aufgeführten Fehler treten auf, weil ein konfigurierter Maximalwert überschritten wurde. Wenn im Lösungsschritt erklärt wird, dass Sie einen Wert ändern sollten, müssen Sie für die Änderung wie folgt vorgehen:

* Für **Windows**-Cluster: Verwenden Sie eine Skriptaktion, um den Wert während der Erstellung des Clusters zu konfigurieren. Weitere Informationen finden Sie unter [Entwickeln von Skriptaktionen](hdinsight-hadoop-script-actions-linux.md).

* Für **Linux**-Cluster: Verwenden Sie Apache Ambari (Web- oder REST-API), um den Wert zu ändern. Weitere Informationen finden Sie unter [Verwalten von HDInsight mit Ambari](hdinsight-hadoop-manage-ambari.md)


### <a name="default-configuration"></a>Standardkonfiguration

Die Überschreitung der folgenden Standardwerte kann die Leistung von WebHCat beeinträchtigen oder Fehler verursachen:

| Einstellung | Funktionsbeschreibung | Standardwert |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |Die maximale Anzahl an Aufträgen, die gleichzeitig aktiv sein können (ausstehend oder in Bearbeitung). |10.000 |
| [templeton.exec.max-procs][max-procs] |Die maximale Anzahl an Anforderungen, die gleichzeitig bearbeitet werden können. |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Die Anzahl der Tage, die der Auftragsverlauf gespeichert wird. |7 Tage |

## <a name="too-many-requests"></a>Zu viele Anforderungen

**HTTP-Statuscode**: 429

| Ursache | Lösung |
| --- | --- |
| Sie haben die maximale Anzahl an Anforderungen überschritten, die WebHCat pro Minute gleichzeitig bearbeiten kann (Standardwert 20). |Reduzieren Sie die Arbeitslast, um sicherzustellen, dass Sie nicht mehr als die maximale Anzahl gleichzeitig bearbeitbarer Anforderungen übergeben, oder erhöhen Sie den Grenzwert für gleichzeitig bearbeitbare Anforderungen durch Änderung von `templeton.exec.max-procs`. Weitere Informationen finden Sie unter [Konfigurationsänderungen](#modifying-configuration). |

## <a name="server-unavailable"></a>Server nicht verfügbar

**HTTP-Statuscode**: 503

| Ursache | Lösung |
| --- | --- |
| Dieser Statuscode tritt gewöhnlich bei einem Failover zwischen dem primären und sekundären Hauptknoten des Clusters auf. |Warten Sie zwei Minuten, bevor Sie den Vorgang wiederholen. |

## <a name="bad-request-content-could-not-find-job"></a>Ungültiger Anforderungsinhalt: Auftrag nicht gefunden

**HTTP-Statuscode**: 400

| Ursache | Lösung |
| --- | --- |
| Auftragsdetails wurden durch die Auftragsverlaufsbereinigung gelöscht. |Die Standardaufbewahrungszeit für den Auftragsverlauf beträgt 7 Tage. Sie kann durch Ändern von `mapreduce.jobhistory.max-age-ms` geändert werden. Weitere Informationen finden Sie unter [Konfigurationsänderungen](#modifying-configuration). |
| Der Auftrag wurde aufgrund eines Failovers beendet. |Versuchen Sie den Auftrag bis zu zwei Minuten lang zu wiederholen. |
| Eine ungültige Auftrags-ID wurde verwendet. |Prüfen Sie, ob die Auftrags-ID richtig ist. |

## <a name="bad-gateway"></a>Ungültiger Gateway

**HTTP-Statuscode**: 502

| Ursache | Lösung |
| --- | --- |
| Im WebHCat-Prozess findet eine interne Bereinigung statt. |Warten Sie, bis die Bereinigung den WebHCat-Dienst beendet oder neu gestartet hat. |
| Timeout beim Warten auf eine Antwort vom ResourceManager-Dienst. Dieser Fehler kann auftreten, wenn die Anzahl der aktiven Anwendungen den konfigurierten Maximalwert (standardmäßig 10.000) überschreitet. |Warten Sie, bis die aktiven Aufträge abgeschlossen sind, oder erhöhen Sie den Grenzwert für gleichzeitig ausgeführte Aufträge durch Änderung von `yarn.scheduler.capacity.maximum-applications`. Weitere Informationen finden Sie im Abschnitt [Konfigurationsänderungen](#modifying-configuration). |
| Versuch, alle Aufträge mit dem Aufruf [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) abzurufen, wenn `Fields` auf `*` festgelegt ist |Rufen Sie nicht *alle* Auftragsdetails ab. Rufen Sie stattdessen mit `jobid` nur die Details von Aufträgen ab, deren Auftrags-ID höher als ein bestimmter Wert ist. Oder Sie sollten kein `Fields` |
| Der WebHCat-Dienst ist während eines Failovers des Hauptknotens nicht verfügbar. |Warten Sie zwei Minuten, bevor Sie den Vorgang wiederholen. |
| Von WebHCat wurden mehr als 500 Aufträge übermittelt, deren Verarbeitung noch aussteht. |Warten Sie, bis die derzeit ausstehenden Aufträge abgeschlossen sind, bevor Sie weitere Aufträge übermitteln. |

[maximum-applications]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
