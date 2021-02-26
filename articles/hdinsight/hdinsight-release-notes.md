---
title: Anmerkungen zu dieser Version von Azure HDInsight
description: Die neuesten Versionshinweise für Azure HDInsight. Rufen Sie Tipps zur Entwicklung und Details für Hadoop, Spark, R Server, Hive und mehr ab.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f6b7000812f1adfe6ff7bd93711c9b8fe4ff9adc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988358"
---
# <a name="azure-hdinsight-release-notes"></a>Versionshinweise zu Azure HDInsight

Dieser Artikel enthält Informationen zu den **neuesten** Versionsupdates für Azure HDInsight. Informationen zu neueren Versionen finden Sie unter [Versionshinweise zu HDInsight – Archiv](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Zusammenfassung

Azure HDInsight ist unter Enterprisekunden einer der beliebtesten Dienste für Open-Source-Analysen in Azure.

Wenn Sie die Anmerkungen zu dieser Version abonnieren möchten, sehen Sie sich die Releases auf [diesem GitHub-Repository](https://github.com/hdinsight/release-notes/releases) an.

## <a name="release-date-02052021"></a>Veröffentlichungsdatum: 05.02.2021

Diese Version gilt sowohl für HDInsight 3.6 als auch für HDInsight 4.0. Das HDInsight-Release wird über mehrere Tage für alle Regionen verfügbar gemacht. Das hier angegebene Veröffentlichungsdatum entspricht dem Veröffentlichungsdatum in der ersten Region. Es kann sein, dass die unten angegebenen Änderungen bei Ihnen erst ein paar Tage später verfügbar werden.

## <a name="new-features"></a>Neue Funktionen
### <a name="dav4-series-support"></a>Unterstützung der Dav4-Serie
In HDInsight wurde in dieser Version Unterstützung für die Dav4-Serie hinzugefügt. Weitere Informationen zur [Dav4-Serie finden Sie hier](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series).

### <a name="kafka-rest-proxy-ga"></a>Kafka-REST-Proxy allgemein verfügbar 
Mithilfe des REST-Proxys von Kafka können Sie über eine REST-API per HTTPS mit Ihrem Kafka-Cluster interagieren. Der Kafka-REST-Proxy ist ab dieser Version allgemein verfügbar. Weitere Informationen zum [Kafka-REST-Proxy finden Sie hier](https://docs.microsoft.com/azure/hdinsight/kafka/rest-proxy).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Wechsel zu Azure-VM-Skalierungsgruppen
HDInsight verwendet jetzt virtuelle Azure-Computer für die Bereitstellung des Clusters. Der Dienst wird schrittweise zu [Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md) migriert. Der gesamte Prozess kann Monate dauern. Nachdem Ihre Regionen und Abonnements migriert wurden, werden neu erstellte HDInsight-Cluster ohne Kundenaktionen in VM-Skalierungsgruppen ausgeführt. Es wird kein Breaking Change erwartet.

## <a name="deprecation"></a>Eingestellte Unterstützung
### <a name="disabled-vm-sizes"></a>Deaktivierte VM-Größen
Ab dem 9. Januar 2021 hindert HDInsight alle Kunden daran, Cluster mit den VM-Größen standand_A8, standand_A9, standand_A10 und standand_A11 zu erstellen. Vorhandene Cluster werden unverändert ausgeführt. Sie sollten zu HDInsight 4.0 wechseln, um potenzielle System-/Supportunterbrechungen zu vermeiden.

## <a name="behavior-changes"></a>Verhaltensänderungen
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>Änderung der VM-Standardgröße für Cluster in Ev3-Serie 
Di VM-Standardgröße für Cluster wird von der D-Serie in die Ev3-Serie geändert. Diese Änderung gilt für Hauptknoten und Workerknoten. Um zu vermeiden, dass sich diese Änderung auf Ihre getesteten Workflows auswirkt, geben Sie die VM-Größen an, die Sie in der ARM-Vorlage verwenden möchten.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Netzwerkschnittstellen-Ressource nicht sichtbar für Cluster, die in Azure-VM-Skalierungsgruppen ausgeführt werden
HDInsight wird schrittweise zu Azure-VM-Skalierungsgruppen migriert. Netzwerkschnittstellen virtueller Computer sind für Kunden in Clustern, die Azure-VM-Skalierungsgruppen verwenden, nicht mehr sichtbar.


### <a name="breaking-change-for-net-for-apache-spark-100"></a>Breaking Change für .NET für Apache Spark 1.0.0
In der nächsten Version von HDInsight wird die erste offizielle Hauptversion von .NET für Apache Spark eingeführt. Sie stellt neben anderen Features DataFrame-API-Vollständigkeit für Spark 2.4.x und Spark 3.0.x bereit. Es gibt Breaking Changes für diese Hauptversion. Informationen zu den Schritten zum Aktualisieren von Code und Pipelines finden Sie in [diesem Migrationsleitfaden](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10). [Hier](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight)erhalten Sie weitere Informationen.

## <a name="upcoming-changes"></a>Bevorstehende Änderungen
Die folgenden Änderungen werden in kommenden Versionen durchgeführt.

### <a name="default-cluster-version-will-be-changed-to-40"></a>Standardclusterversion wird in 4.0 geändert
Ab Februar 2021 wird die Standardversion des HDInsight-Clusters von 3.6 in 4.0 geändert. Weitere Informationen zu verfügbaren Versionen finden Sie unter [Verfügbare Versionen](./hdinsight-component-versioning.md#available-versions). Erfahren Sie mehr über die Neuerungen in [HDInsight 4.0](./hdinsight-version-release.md).

### <a name="os-version-upgrade"></a>Upgrade der Betriebssystemversion
Für HDInsight erfolgt ein Upgrade der Betriebssystemversion von Ubuntu 16.04 auf 18.04. Das Upgrade wird vor April 2021 abgeschlossen sein.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Ende der Unterstützung von HDInsight 3.6 am 30. Juni 2021
Die Unterstützung von HDInsight 3.6 endet bald. Ab dem 30. Juni 2021 können Kunden keine neuen HDInsight 3.6-Cluster mehr erstellen. Vorhandene Cluster werden unverändert ohne Unterstützung durch Microsoft ausgeführt. Sie sollten zu HDInsight 4.0 wechseln, um potenzielle System-/Supportunterbrechungen zu vermeiden.

## <a name="bug-fixes"></a>Behebung von Programmfehlern
HDInsight sorgt weiterhin für Verbesserungen bei der Clusterzuverlässigkeit und -leistung. 

## <a name="component-version-change"></a>Änderung der Komponentenversion
Für dieses Release gibt es keine Änderung der Komponentenversion. Die aktuellen Komponentenversionen für HDInsight 4.0 und HDInsight 3.6 finden Sie in [dieser Dokumentation](./hdinsight-component-versioning.md).

