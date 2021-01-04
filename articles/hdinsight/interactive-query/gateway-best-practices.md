---
title: Ausführliche Informationen zum Gateway und bewährte Methoden für Apache Hive in Azure HDInsight
description: Erfahren Sie, wie Sie durch die bewährten Methoden zur Ausführung von Hive-Abfragen über das Azure HDInsight-Gateway navigieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 3db411df69a754857220867865522f8e4fa24030
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011488"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Ausführliche Informationen zum Gateway und bewährte Methoden für Apache Hive in Azure HDInsight

Das Azure HDInsight-Gateway ist das HTTPS-Front-End für HDInsight-Cluster. Das Gateway ist zuständig für: Authentifizierung, Hostauflösung, Dienstermittlung und andere hilfreiche Features, die für ein modernes verteiltes System erforderlich sind. Die vom Gateway zur Verfügung gestellten Features führen zu einem gewissen Mehraufwand, für den in diesem Dokument die bewährten Methoden zur Navigation beschrieben werden. Es werden auch Gateway-Problembehandlungstechniken besprochen.

## <a name="the-hdinsight-gateway"></a>HDInsight-Gateway

Das HDInsight-Gateway ist der einzige Teil eines HDInsight-Clusters, der über das Internet öffentlich zugänglich ist. Auf den Gatewaydienst kann über den internen Gatewayendpunkt `clustername-int.azurehdinsight.net` zugegriffen werden, ohne das öffentliche Internet zu verwenden. Der interne Gatewayendpunkt ermöglicht das Herstellen von Verbindungen zum Gatewaydienst, ohne das virtuelle Netzwerk des Clusters zu verlassen. Das Gateway bearbeitet alle Anforderungen, die an den Cluster gesendet werden, und leitet diese Anforderungen an die richtigen Komponenten und Clusterhosts weiter.

Das folgende Diagramm veranschaulicht grob, wie das Gateway eine Abstraktion vor all den verschiedenen Hostauflösungsmöglichkeiten innerhalb von HDInsight bietet.

![Diagramm zur Hostauflösung](./media/gateway-best-practices/host-resolution-diagram.png "Diagramm zur Hostauflösung")

## <a name="motivation"></a>Motivation

Die Motivation, ein Gateway vor HDInsight-Clustern zu platzieren, besteht darin, eine Schnittstelle für Dienstermittlung und Benutzerauthentifizierung bereitzustellen. Die vom Gateway bereitgestellten Authentifizierungsmechanismen sind besonders relevant für Cluster mit aktiviertem Enterprise-Sicherheitspaket.

Für die Dienstermittlung liegt der Vorteil des Gateways darin, dass auf jede Komponente innerhalb des Clusters als unterschiedlicher Endpunkt unter der Gatewaywebsite (`clustername.azurehdinsight.net/hive2`) zugegriffen werden kann, im Gegensatz zu einer Vielzahl von `host:port`-Kombinationen.

Zur Authentifizierung ermöglicht das Gateway Benutzern die Authentifizierung mit einem `username:password`-Anmeldeinformationspaar. Bei Clustern mit aktiviertem Enterprise-Sicherheitspaket wären diese Anmeldeinformationen der Benutzername und das Kennwort des Benutzers für die Domäne. Für die Authentifizierung bei HDInsight-Clustern über das Gateway ist es nicht erforderlich, dass der Client ein Kerberos-Ticket erwirbt. Da das Gateway `username:password`-Anmeldeinformationen akzeptiert und das Kerberos-Ticket des Benutzers im Auftrag des Benutzers erwirbt, können sichere Verbindungen zum Gateway von jedem Clienthost hergestellt werden, auch von Clients, die mit anderen AA-DDS-Domänen als dem (ESP-) Cluster verbunden sind.

## <a name="best-practices"></a>Bewährte Methoden

Das Gateway ist ein einzelner Dienst ( Lastenausgleich über zwei Hosts), der für die Weiterleitung und Authentifizierung von Anforderungen zuständig ist. Das Gateway kann zu einem Engpass beim Durchsatz von Hive-Abfragen werden, die eine bestimmte Größe überschreiten. Eine Verschlechterung der Abfrageleistung kann beobachtet werden, wenn sehr große **SELECT**-Abfragen über ODBC oder JDBC auf dem Gateway ausgeführt werden. „Sehr groß“ bezeichnet Abfragen, die mehr als 5 GB Daten in Zeilen oder Spalten umfassen. Diese Abfrage könnte eine lange Liste von Zeilen und/oder eine große Anzahl von Spalten enthalten.

Die Leistungseinbußen des Gateways bei großen Abfragen sind darauf zurückzuführen, dass die Daten vom zugrunde liegenden Datenspeicher (ADLS Gen2) übertragen werden müssen an den HDInsight Hive-Server, das Gateway und schließlich über die JDBC- oder ODBC-Treiber an den Clienthost.

Das folgende Diagramm veranschaulicht die Schritte, die die SELECT-Abfrage umfasst.

![Ergebnisdiagramm](./media/gateway-best-practices/result-retrieval-diagram.png "Ergebnisdiagramm")

Apache Hive ist eine relationale Abstraktion auf einem HDFS-kompatiblen Dateisystem. Diese Abstraktion bedeutet, dass **SELECT**-Anweisungen in Hive den **READ**-Vorgängen im Dateisystem entsprechen. Die **READ**-Vorgänge werden in das entsprechende Schema übersetzt, bevor sie dem Benutzer gemeldet werden. Die Wartezeit dieses Prozesses steigt mit dem Umfang der Daten und der Gesamtzahl der Hops, die erforderlich sind, um den Endbenutzer zu erreichen.

Ein ähnliches Verhalten kann bei der Ausführung von **CREATE**- oder **INSERT**-Anweisungen für große Datenmengen auftreten, da diese Befehle **WRITE**-Vorgänge im zugrunde liegenden Dateisystem entsprechen. Ziehen Sie in Betracht, Daten (z. B. unformatiertes ORC) in das Dateisystem/Data Lake zu schreiben, anstatt sie mit **INSERT** oder **LOAD** zu laden.

In Clustern mit aktiviertem Enterprise-Sicherheitspaket können ausreichend komplexe Apache Ranger-Richtlinien eine Verlangsamung der Abfragekompilierungszeit verursachen, was zu einem Gatewayzeitlimit führen kann. Wenn in einem ESP-Cluster ein Gatewayzeitlimit festgestellt wird, sollten Sie in Erwägung ziehen, die Anzahl der Ranger-Richtlinien zu reduzieren oder zu kombinieren.

## <a name="troubleshooting-techniques"></a>Problembehandlungstechniken

Es gibt mehrere Ansätze für das Entschärfen und das Verstehen von Leistungsproblemen, die im Rahmen des oben beschriebenen Verhaltens auftreten. Verwenden Sie die folgende Prüfliste, wenn eine Verschlechterung der Abfrageleistung über das HDInsight-Gateway festgestellt wird:

* Verwenden Sie die **LIMIT**-Klausel, wenn Sie große **SELECT**-Abfragen ausführen. Die **LIMIT**-Klausel reduziert die Gesamtzahl der an den Clienthost gemeldeten Zeilen. Die **LIMIT**-Klausel betrifft nur die Ergebnisgenerierung und ändert nicht den Abfrageplan. Um die **LIMIT**-Klausel auf den Abfrageplan anzuwenden, verwenden Sie die Konfiguration `hive.limit.optimize.enable`. **LIMIT** kann mit einem Offset mit der Argumentform **LIMIT x,y** kombiniert werden.

* Benennen Sie die für Sie interessanten Spalten, wenn Sie **SELECT**-Abfragen ausführen, anstatt **SELECT \** _ zu verwenden. Wenn Sie weniger Spalten auswählen, verringert sich die Menge der gelesenen Daten.

_ Versuchen Sie, die betreffende Abfrage über Apache Beeline auszuführen. Wenn das Abrufen von Ergebnissen über Apache Beeline längere Zeit in Anspruch nimmt, rechnen Sie mit Verzögerungen beim Abrufen derselben Ergebnisse über externe Tools.

* Testen Sie eine einfache Hive-Abfrage, um sicherzustellen, dass eine Verbindung mit dem HDInsight-Gateway hergestellt werden kann. Versuchen Sie, eine einfache Abfrage von zwei oder mehr externen Tools auszuführen, um sicherzustellen, dass kein Einzeltool auf Probleme stößt.

* Überprüfen Sie alle Apache Ambari-Warnungen, um sicherzustellen, dass die HDInsight-Dienste fehlerfrei sind. Ambari-Warnungen können zur Berichterstellung und Analyse in Azure Monitor integriert werden.

* Wenn Sie einen externen Hive-Metastore verwenden, überprüfen Sie, ob die Azure SQL DB-DTU für den Hive-Metastore ihr Limit nicht erreicht hat. Wenn sich die DTU ihrem Limit nähert, müssen Sie die Datenbankgröße erhöhen.

* Stellen Sie sicher, dass alle Tools von Drittanbietern wie PBI oder Tableau die Paginierung zum Anzeigen von Tabellen oder Datenbanken verwenden. Erkundigen Sie sich bei Ihren Supportpartnern nach diesen Tools zur Unterstützung bei der Paginierung. Das wichtigste Tool für die Paginierung ist der JDBC-Parameter `fetchSize`. Eine kleine Abrufgröße kann zu einer verminderten Gatewayleistung führen, aber eine zu große Abrufgröße kann zu einem Gatewayzeitlimit führen. Die Optimierung der Abrufgröße muss auf der Grundlage der Workload erfolgen.

* Wenn Ihre Datenpipeline das Lesen großer Datenmengen aus dem zugrunde liegenden Speicher des HDInsight-Clusters erfordert, sollten Sie die Verwendung eines Tools in Betracht ziehen, das direkt mit Azure Storage verbunden ist, z. B. Azure Data Factory.

* Ziehen Sie die Verwendung von Apache Hive LLAP in Betracht, wenn interaktive Workloads ausgeführt werden, da LLAP möglicherweise eine reibungslosere Erfahrung für die schnelle Rückgabe von Abfrageergebnissen bietet.

* Erwägen Sie, die Anzahl der für den Hive-Metastore-Dienst verfügbaren Threads mit `hive.server2.thrift.max.worker.threads` zu erhöhen. Diese Einstellung ist besonders relevant, wenn eine hohe Anzahl gleichzeitiger Benutzer Abfragen an den Cluster senden.

* Verringern Sie die Anzahl der Wiederholungsversuche, die verwendet werden, um das Gateway von externen Tools aus zu erreichen. Wenn mehrere Wiederholungsversuche durchgeführt werden, sollten Sie eine Wiederholungsrichtlinie mit exponentiellem Backoff befolgen.

* Ziehen Sie in Betracht, die Komprimierung von Hive mit den Konfigurationen `hive.exec.compress.output` und `hive.exec.compress.intermediate` zu aktivieren.

## <a name="next-steps"></a>Nächste Schritte

* [Apache Beeline in HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md)
* [Schritte zur Problembehandlung für das HDInsight-Gateway](./troubleshoot-gateway-timeout.md)
* [Virtuelle Netzwerke für HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [HDInsight mit ExpressRoute](../connect-on-premises-network.md)