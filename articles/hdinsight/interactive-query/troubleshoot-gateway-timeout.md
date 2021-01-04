---
title: Ausnahmen für die Ausführung von Apache Hive-Abfragen mithilfe der Ambari-Hive-Ansicht in Azure HDInsight
description: Schritte zur Problembehandlung beim Ausführen von Apache Hive-Abfragen mithilfe der Apache Ambari-Hive-Ansicht in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: aeedda5c26a2e9dc0fa2b228285cfda45d880d29
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547383"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Ausnahmen für die Ausführung von Apache Hive-Abfragen mithilfe der Ambari-Hive-Ansicht in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Bei der Ausführung einer Apache Hive-Abfrage mithilfe der Apache Ambari-Hive-Ansicht erhalten Sie hin und wieder die folgende Fehlermeldung:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Ursache

Ein Gatewaytimeout.

Der Standardtimeoutwert für Gateways beträgt zwei Minuten. Abfragen mithilfe der Ambari-Hive-Ansicht werden über das Gateway an den `/hive2`-Endpunkt übermittelt. Sobald die Abfrage erfolgreich kompiliert und akzeptiert wurde, gibt der Hive-Server die Methode `queryid` zurück. Clients fragen dann weiter den Status der Abfrage ab. Während dieses Prozesses gibt das HDI-Gateway für den Aufrufer den Gatewaytimeout-Fehler 502.3 zurück, wenn der Hive-Server nicht innerhalb von zwei Minuten eine HTTP-Antwort zurückgibt. Diese Fehler können auftreten, wenn die Abfrage zur Verarbeitung übermittelt wird (geschieht öfter), und auch beim Aufruf zum Abrufen des Status (geschieht eher selten). Für Nutzer können beide Fehler angezeigt werden.

Der HTTP-Handlerthread sollte schnell einen Auftrag vorbereiten und die Methode `queryid` zurückgeben. Aus verschiedenen Gründen könnten jedoch alle Handlerthreads ausgelastet sein. Das würde zu Timeouts für neue Abfragen und die Aufrufe zum Abrufen des Status führen.

### <a name="responsibilities-of-the-http-handler-thread"></a>Aufgaben des HTTP-Handlerthreads

Wenn der Client eine Abfrage an den Hive-Server übermittelt, geschieht im Vordergrundthread Folgendes:

* Analysieren der Anforderung, Ausführen der semantischen Überprüfung
* Abrufen der Sperre
* Suchen im Metastore (wenn erforderlich)
* Kompilieren der Abfrage (DDL oder DML)
* Vorbereiten eines Abfrageplans
* Ausführen der Autorisierung (Ausführen aller anwendbaren Ranger-Richtlinien in sicheren Clustern)

## <a name="resolution"></a>Lösung

In folgender Liste finden Sie allgemeine Empfehlungen, um die Situation zu verbessern:

* Wenn ein externer Hive-Metastore verwendet wird, überprüfen Sie die DB-Metriken, und vergewissern Sie sich, dass die Datenbank nicht überladen ist. Ziehen Sie es in Betracht, die Datenbankebene des Metastore zu skalieren.

* Vergewissern Sie sich, dass die Option zum parallelen Ausführen von Vorgängen aktiviert ist. So können HTTP-Handlerthreads gleichzeitig ausgeführt werden. Starten Sie [Apache Ambari](../hdinsight-hadoop-manage-ambari.md), und navigieren Sie zu **Hive** > **Configs** > **Advanced** > **Custom hive-site** (Hive > Konfigurationen > Erweitert > Benutzerdefinierte Hive-Site). Der Wert für `hive.server2.parallel.ops.in.session` sollte `true` sein.

* Vergewissern Sie sich, dass die VM-SKU des Clusters die Last auch bewältigen kann. Möglicherweise müssen Sie die Last auf verschiedene Cluster aufteilen. Weitere Informationen finden Sie unter [Auswahl eines Clustertyps](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Wenn Apache Ranger auf dem Cluster installiert ist, überprüfen Sie bitte, ob es zu viele Ranger-Richtlinien gibt, die für jede Abfrage ausgewertet werden müssen. Achten Sie darauf, ob es doppelte oder unnötige Richtlinien gibt.

* Überprüfen Sie den **Hiveserver2 Heap Size** -Wert (Heapgröße des Hive-Servers 2) in Ambari. Navigieren Sie zu **Hive** > **Configs** > **Settings** > **Optimization** (Hive > Konfigurationen > Einstellungen > Optimierung). Vergewissern Sie sich, dass der Wert größer als 10 GB ist. Nehmen Sie bei Bedarf Anpassungen vor, um die Leistung zu optimieren.

* Vergewissern Sie sich, dass die Hive-Abfrage optimiert ist. Weitere Informationen dazu finden Sie unter [Optimieren von Apache Hive-Abfragen in Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.