---
title: 'Sicherheit: Migrieren lokaler Apache Hadoop-Cluster zu Azure HDInsight'
description: Erfahren Sie mehr über bewährte Methoden für Sicherheit und DevOps bei der Migration von lokalen Hadoop-Clustern zu Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 6fd59a346a36a304f699894c493d8ba9800489b2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545258"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrieren lokaler Apache Hadoop-Cluster zu Azure HDInsight – bewährte Methoden für Sicherheit und DevOps

Dieser Artikel enthält Empfehlungen für Sicherheit und DevOps in Azure HDInsight-Systemen. Er ist Teil einer Reihe von Artikeln, die bewährte Methoden für die Migration von lokalen Apache Hadoop-Systemen zu Azure HDInsight enthalten.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Schützen und Steuern des Clusters mit dem Enterprise-Sicherheitspaket

Das Enterprise-Sicherheitspaket (ESP) unterstützt die Active Directory-basierte Authentifizierung, Unterstützung mehrerer Benutzer und rollenbasierte Zugriffssteuerung. Bei ausgewählter ESP-Option wird der HDInsight-Cluster mit der Active Directory-Domäne verknüpft, und der Unternehmensadministrator kann mithilfe von Apache Ranger die rollenbasierte Zugriffskontrolle (Role-Based Access Control, RBAC) für Apache Hive-Sicherheit konfigurieren. Der Administrator kann auch den Datenzugriff von Mitarbeitern und jegliche Änderungen der Zugriffssteuerungsrichtlinien überwachen.

Das ESP ist für die folgenden Clustertypen verfügbar: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka und Interactive Query (Hive LLAP).

Verwenden Sie die folgenden Schritte, um den in die Domäne eingebundenen HDInsight-Cluster bereitzustellen:

- Stellen Sie Azure Active Directory (AAD) bereit, indem Sie den Domänennamen übergeben.
- Stellen Sie Azure Active Directory Domain Services (AAD DS) bereit.
- Erstellen Sie das erforderliche virtuelle Netzwerk und Subnetz.
- Stellen Sie einen virtuellen Computer im virtuellen Netzwerk zur Verwaltung von AAD DS bereit.
- Führen Sie den Beitritt des virtuellen Computers zur Domäne durch.
- Installieren Sie AD- und DNS-Tools.
- Lassen Sie vom AAD DS-Administrator eine Organisationseinheit erstellen.
- Aktivieren Sie LDAPS für AAD DS.
- Erstellen Sie ein Dienstkonto in Azure Active Directory mit delegierter Administratorberechtigung für Lesen und Schreiben in der Organisationseinheit. Mit diesem Dienstkonto können dann Computer in die Domäne eingebunden und Computerprinzipale in der Organisationseinheit platziert werden. Hiermit können auch Dienstprinzipale in der Organisationseinheit erstellt werden, die Sie während der Clustererstellung angeben.

    > [!Note]
    > Das Dienstkonto muss kein Administratorkonto für die AD-Domäne sein.

- Stellen Sie den HDInsight ESP-Cluster bereit, indem Sie die folgenden Parameter festlegen:

    |Parameter |BESCHREIBUNG |
    |---|---|
    |Domänenname|Der Domänenname, der Azure AD DS zugeordnet ist.|
    |Domänenbenutzername|Das Dienstkonto in der vom Azure AD DS-Domänencontroller verwalteten Domäne, die im vorherigen Abschnitt erstellt wurde, z.B. `hdiadmin@contoso.onmicrosoft.com`. Dieser Domänenbenutzer wird der Administrator dieses HDInsight-Clusters.|
    |Domänenkennwort|Das Kennwort des Dienstkontos.|
    |Organisationseinheit|Der Distinguished Name der Organisationseinheit, die Sie mit dem HDInsight-Cluster verwenden möchten, z.B. `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Wenn diese Organisationseinheit nicht vorhanden ist, versucht der HDInsight-Cluster, sie mithilfe der Berechtigungen des Dienstkontos zu erstellen.|
    |LDAPS-URL|Beispiel: `ldaps://contoso.onmicrosoft.com:636`.|
    |Zugriff auf Benutzergruppe|Die Sicherheitsgruppe, deren Benutzer Sie mit dem Cluster synchronisieren möchten, z.B. `HiveUsers`. Wenn Sie mehrere Benutzergruppen angeben möchten, trennen Sie diese durch Semikolons (;). Die Gruppen müssen vor dem Erstellen des ESP-Clusters im Verzeichnis vorhanden sein.|

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Einführung in die Apache Hadoop-Sicherheit mit in die Domäne eingebundenen HDInsight-Clustern](../domain-joined/hdinsight-security-overview.md)
- [Planen von in die Azure-Domäne eingebundenen Apache Hadoop-Clustern in HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurieren eines in die Domäne eingebundenen HDInsight-Clusters mit Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synchronisieren von Azure Active Directory-Benutzern in einen HDInsight-Cluster](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurieren von Apache Hive-Richtlinien in HDInsight mit Domänenverknüpfung](../domain-joined/apache-domain-joined-run-hive.md)
- [Ausführen von Apache Oozie in in die Domäne eingebundenen HDInsight Hadoop-Clustern](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementieren der End-to-End-Unternehmenssicherheit

End-to-End-Unternehmenssicherheit kann mithilfe der folgenden Steuerelemente erzielt werden:

**Private und geschützte Datenpipeline (Sicherheit auf Umgebungsebene)**
    - Sicherheit auf Umgebungsebene kann über virtuelle Azure-Netzwerke, Netzwerksicherheitsgruppen und den Gatewaydienst erzielt werden.

**Authentifizierung und Autorisierung für Datenzugriff**
    - Erstellen Sie in die Domäne eingebundene HDInsight-Cluster mit Azure Active Directory Domain Services. (Enterprise-Sicherheitspaket).
    - Verwenden Sie Ambari, um für AD-Benutzer den rollenbasierten Zugriff auf Clusterressourcen bereitzustellen.
    - Verwenden Sie Apache Ranger, um Zugriffssteuerungsrichtlinien für Hive auf Tabellen-/Spalten-/Zeilenebene festzulegen.
    - Der SSH-Zugriff auf den Cluster kann nur auf den Administrator beschränkt werden.

**Überwachung**
    - Zeigen Sie sämtliche Zugriffe auf die Ressourcen und Daten des HDInsight-Clusters an, und erstellen Sie entsprechende Berichte.
    - Zeigen Sie sämtliche Änderungen an den Zugriffssteuerungsrichtlinien an, und erstellen Sie entsprechende Berichte.

**Verschlüsselung**
    - Transparente serverseitige Verschlüsselung mit von Microsoft verwalteten Schlüsseln und vom Kunden verwalteten Schlüsseln
    - Verschlüsselung während der Übertragung mithilfe clientseitiger Verschlüsselung, HTTPS und TLS

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Übersicht über Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)
- [Übersicht über Azure-Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md)
- [Peering in virtuellen Azure-Netzwerken](../../virtual-network/virtual-network-peering-overview.md)
- [Azure Storage-Sicherheitsleitfaden](../../storage/blobs/security-recommendations.md)
- [Azure-Speicherdienstverschlüsselung für ruhende Daten](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Verwenden von Überwachung und Warnung

Weitere Informationen finden Sie im Artikel:

[Azure Monitor – Übersicht](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Aktualisieren von Clustern

Führen Sie regelmäßige Upgrades auf die neueste Version von HDInsight durch, um die neuesten Features zu nutzen. Mithilfe der folgenden Schritte kann der Cluster auf die neueste Version aktualisiert werden:

1. Erstellen Sie einen neuen HDInsight-Cluster TEST mit der neuesten verfügbaren HDInsight-Version.
1. Testen Sie den neuen Cluster, um sicherzustellen, dass die Aufträge und Workloads wie erwartet funktionieren.
1. Ändern Sie Aufträge, Anwendungen oder Workloads nach Bedarf.
1. Sichern Sie alle Daten, die vorübergehend lokal auf dem Clusterknoten gespeichert sind.
1. Löschen Sie den vorhandenen Cluster.
1. Erstellen Sie einen Cluster der neuesten HDInsight-Version im gleichen Subnetz des virtuellen Netzwerks mit dem gleichen Standarddaten- und Metadatenspeicher wie der vorherige Cluster.
1. Importieren Sie alle vorübergehenden Daten, die gesichert wurden.
1. Starten Sie Aufträge/Verarbeiten Sie weiterhin mithilfe des neuen Clusters.

Weitere Informationen finden Sie im Artikel: [Aktualisieren eines HDInsight-Clusters auf eine neue Version](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Patchen von Clusterbetriebssystemen

Weitere Informationen finden Sie im Artikel: [Patchen des Betriebssystems für HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Aufgaben nach der Migration

1. **Korrigieren von Anwendungen** : Führen Sie iterativ die erforderlichen Änderungen an den Aufträgen, Prozessen und Skripts durch.
2. **Durchführen von Tests** : Führen Sie iterativ Funktions- und Leistungstests durch.
3. **Optimieren** : Beheben Sie Leistungsprobleme basierend auf den Ergebnissen der oben genannten Tests, und führen Sie dann erneut Tests durch, um die Leistungsverbesserungen zu überprüfen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über [HDInsight 4.0](./apache-hadoop-introduction.md).