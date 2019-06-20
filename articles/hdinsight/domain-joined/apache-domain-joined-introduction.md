---
title: Einführung in die Apache Hadoop-Sicherheit mit dem Enterprise-Sicherheitspaket
description: Hier erfahren Sie, wie das Enterprise-Sicherheitspaket die vier Grundsäulen der Unternehmenssicherheit unterstützt.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0582fa8b26bee05e4d2948037cc39a71ed656fce
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243953"
---
# <a name="what-is-enterprise-security-package-in-azure-hdinsight"></a>Worum handelt es sich beim Enterprise-Sicherheitspaket in Azure HDInsight?

Azure HDInsight unterstützte bislang nur einen einzelnen lokalen Administratorbenutzer. Für kleinere Anwendungsteams oder Abteilungen hat das hervorragend funktioniert. Mit der zunehmenden Verbreitung von Apache Hadoop-basierten Workloads im Unternehmenssektor gewinnen jedoch professionelle Funktionen wie Active Directory-basierte Authentifizierung, Unterstützung mehrerer Benutzer und rollenbasierte Zugriffssteuerung immer mehr an Bedeutung. 

Mit dem Enterprise-Sicherheitspaket können Sie einen HDInsight-Cluster erstellen, der in eine Active Directory-Domäne eingebunden ist. Sie können dann eine Liste mit Mitarbeitern des Unternehmens konfigurieren, die eine Authentifizierung per Azure Active Directory für die Anmeldung am HDInsight-Cluster durchführen können. Externe Benutzer können sich nicht bei dem HDInsight-Cluster anmelden oder darauf zugreifen. 

Der Unternehmensadministrator kann mithilfe von [Apache Ranger](https://hortonworks.com/apache/ranger/) die rollenbasierte Zugriffskontrolle (Role-based Access Control, RBAC) für Apache Hive-Sicherheit konfigurieren. und so den Datenzugriff nur auf die erforderlichen Informationen beschränken. Darüber hinaus kann der Administrator den Datenzugriff von Mitarbeitern und jegliche Änderung der Zugriffssteuerungsrichtlinien überwachen. Der Administrator kann dann ein hohes Maß an Kontrolle über Unternehmensressourcen erreichen.

> [!NOTE]  
> Apache Oozie ist nun für ESP-Cluster aktiviert. Benutzer müssen [Tunneln](../hdinsight-linux-ambari-ssh-tunnel.md) aktivieren, um auf die Oozie-Webbenutzeroberfläche zugreifen zu können.

Die Unternehmenssicherheit basiert auf vier Hauptsäulen: Umgebungssicherheit, Authentifizierung, Autorisierung und Verschlüsselung.

![Vorteile von HDInsight-Clustern mit Enterprise-Sicherheitspaket hinsichtlich der vier Grundsäulen der Unternehmenssicherheit](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Umgebungssicherheit
Die Umgebungssicherheit in HDInsight wird mithilfe von virtuellen Netzwerken und dem Azure VPN Gateway-Dienst erreicht. Ein Unternehmensadministrator kann einen Cluster mit Enterprise-Sicherheitspaket in einem virtuellen Netzwerk erstellen und den Zugriff auf das virtuelle Netzwerk mithilfe von Netzwerksicherheitsgruppen (Firewallregeln) beschränken. Es können nur IP-Adressen, die in den eingehenden Firewallregeln definiert sind, mit dem HDInsight-Cluster kommunizieren. Diese Konfiguration bietet Umgebungssicherheit.

Durch die Verwendung eines VPN Gateway-Diensts lässt sich die Umgebungssicherheit weiter verbessern. Das Gateway fungiert als erste Verteidigungslinie für eingehende, an den HDInsight-Cluster gerichtete Anforderungen. Es nimmt die Anforderung an, überprüft diese und gibt sie erst dann an die anderen Knoten im Cluster weiter. Auf diese Weise ermöglicht das Gateway Umgebungssicherheit an andere Namens- und Datenknoten im Cluster.

## <a name="authentication"></a>Authentication
Ein Unternehmensadministrator kann einen HDInsight-Cluster mit Enterprise-Sicherheitspaket in einem [virtuellen Netzwerk](https://azure.microsoft.com/services/virtual-network/) bereitstellen. Alle Knoten des HDInsight-Clusters werden in die vom Unternehmen verwaltete Domäne eingebunden. Hierzu wird [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) verwendet. 

In dieser Konfiguration können sich Mitarbeiter des Unternehmens mit ihren Domänenanmeldeinformationen bei den Clusterknoten anmelden. Darüber hinaus können sie sich mit ihren Domänenanmeldeinformationen auch bei anderen genehmigten Endpunkten wie Apache Ambari Views, ODBC, JDBC, PowerShell und REST-APIs authentifizieren, um mit dem Cluster zu interagieren. Der Administrator hat uneingeschränkte Kontrolle über die Begrenzung der Anzahl von Benutzern, die über diese Endpunkte mit dem Cluster interagieren.

## <a name="authorization"></a>Autorisierung
In den meisten Unternehmen hat es sich bewährt, dass nicht jeder Mitarbeiter Zugriff auf alle Unternehmensressourcen hat. Analog dazu kann der Administrator für die Clusterressourcen Richtlinien für die rollenbasierte Zugriffssteuerung definieren. 

So kann der Administrator etwa [Apache Ranger](https://hortonworks.com/apache/ranger/) zum Festlegen von Zugriffssteuerungsrichtlinien für Hive konfigurieren. Dadurch wird sichergestellt, dass Mitarbeiter nur auf Daten zugreifen können, die sie für ihre Arbeit benötigen. SSH-Zugriff auf den Cluster ist allein dem Administrator vorbehalten.

## <a name="auditing"></a>Überwachung
Die Überwachung des gesamten Zugriffs auf die Clusterressourcen und der Daten ist erforderlich, um unbefugten oder versehentlichen Zugriff auf die Ressourcen nachzuverfolgen. Dies ist ebenso wichtig wie der Schutz der HDInsight-Clusterressourcen vor nicht autorisierten Benutzern und das Sichern von Daten. 

Der Administrator kann sämtliche Zugriffe auf die Ressourcen und Daten des HDInsight-Clusters anzeigen und entsprechende Berichte erstellen. Außerdem kann er sämtliche Änderungen an den Zugriffssteuerungsrichtlinien anzeigen, die an von Apache Ranger unterstützten Endpunkten vorgenommen werden, und entsprechende Berichte erstellen. 

Ein HDInsight-Cluster mit Enterprise-Sicherheitspaket verwendet für die Suche nach Überwachungsprotokollen die vertraute Apache Ranger-Benutzeroberfläche. Im Back-End werden die Protokolle von Ranger mithilfe von [Apache Solr](https://hortonworks.com/apache/solr/) gespeichert und gesucht.

## <a name="encryption"></a>Verschlüsselung
Der Schutz von Daten ist wichtig, um die Sicherheits- und Compliance-Anforderungen des Unternehmens zu erfüllen. Sie sollten den Zugriff auf Daten durch nicht autorisierte Mitarbeitern nicht nur beschränken, sondern Sie sollten diesen auch verschlüsseln. 

Beide Datenspeicher für HDInsight-Cluster – Azure Blob Storage und Azure Data Lake Storage Gen1/Gen2 – unterstützen die transparente serverseitige [Verschlüsselung von Daten](../../storage/common/storage-service-encryption.md) im Ruhezustand. Sichere HDInsight-Cluster arbeiten reibungslos mit dieser Funktion zur serverseitigen Verschlüsselung ruhender Daten zusammen.

## <a name="next-steps"></a>Nächste Schritte

* [Planen für HDInsight-Cluster mit Enterprise-Sicherheitspaket](apache-domain-joined-architecture.md)
* [Konfigurieren von HDInsight-Clustern mit Enterprise-Sicherheitspaket](apache-domain-joined-configure.md)
* [Verwalten von HDInsight-Clustern mit ESP](apache-domain-joined-manage.md)
* [Konfigurieren von Apache Hive-Richtlinien für HDInsight-Cluster mit ESP](apache-domain-joined-run-hive.md)
* [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

