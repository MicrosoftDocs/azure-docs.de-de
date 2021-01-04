---
title: Konfigurieren von Einschränkungen des ausgehenden Netzwerkdatenverkehrs – Azure HDInsight
description: Erfahren Sie, wie Sie Einschränkungen des ausgehenden Netzwerkdatenverkehrs für Azure HDInsight-Cluster konfigurieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: dc6412a85beba67551e7683c8127a65730f9218f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535466"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Konfigurieren des ausgehenden Netzwerkdatenverkehrs für Azure HDInsight-Cluster mittels Firewall

In diesem Artikel werden die Schritte beschrieben, mit denen Sie aus Ihrem HDInsight-Cluster ausgehenden Datenverkehr mithilfe von Azure Firewall schützen. Die folgenden Schritte setzen voraus, dass Sie eine Azure Firewall-Instanz für einen vorhandenen Cluster konfigurieren. Wenn Sie einen neuen Cluster hinter einer Firewall bereitstellen, erstellen Sie zunächst Ihren HDInsight-Cluster und das Subnetz. Führen Sie anschließend die Schritte in dieser Anleitung aus.

## <a name="background"></a>Hintergrund

HDInsight-Cluster werden normalerweise in einem virtuellen Netzwerk bereitgestellt. Der Cluster weist Abhängigkeiten mit Diensten außerhalb dieses virtuellen Netzwerks auf.

Der eingehende Verwaltungsdatenverkehr kann nicht durch eine Firewall gesendet werden. Sie können NSG-Diensttags für den eingehenden Datenverkehr verwenden, wie [hier](./hdinsight-service-tags.md) beschrieben wird. 

Die Abhängigkeiten des ausgehenden HDInsight-Datenverkehrs werden fast ausschließlich mit FQDNs definiert. Hinter diesen FQDNs stehen keine statischen IP-Adressen. Das Fehlen statischer Adressen bedeutet, dass keine Netzwerksicherheitsgruppen (NSGs) verwendet werden können, um den ausgehenden Datenverkehr eines Clusters einzuschränken. Die IP-Adressen ändern sich häufig, sodass keine Regeln auf Grundlage der aktuellen Auflösung aufgestellt werden können.

Sichern Sie ausgehende Adressen mit einer Firewall, die den ausgehenden Datenverkehr basierend auf FQDNs kontrolliert. Azure Firewall schränkt ausgehenden Datenverkehr basierend auf dem FQDN des Ziels oder [FQDN-Tags](../firewall/fqdn-tags.md) ein.

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurieren von Azure Firewall mit HDInsight

Hier sehen Sie eine Zusammenfassung der Schritte, um von HDInsight ausgehenden Datenverkehr mit Azure Firewall zu sperren:

1. Erstellen Sie ein Subnetz.
1. Erstellen einer Firewall.
1. Hinzufügen von Anwendungsregeln zur Firewall
1. Fügen Sie der Firewall Netzwerkregeln hinzu.
1. Erstellen Sie eine Routingtabelle.

### <a name="create-new-subnet"></a>Erstellen eines neuen Subnetzes

Erstellen Sie ein Subnetz namens **AzureFirewallSubnet** in dem virtuellen Netzwerk, in dem sich Ihr Cluster befindet.

### <a name="create-a-new-firewall-for-your-cluster"></a>Erstellen einer neuen Firewall für Ihren Cluster

Erstellen Sie eine Firewall mit dem Namen **Test-FW01** gemäß den Schritten unter **Bereitstellen der Firewall** in [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurieren der Firewall mit Anwendungsregeln

Erstellen Sie eine Anwendungsregelsammlung, die dem Cluster ermöglicht, wichtige Informationen zu senden und zu empfangen.

1. Wählen Sie die neue Firewall **Test-FW01** über das Azure-Portal aus.

1. Navigieren Sie zu **Einstellungen** > **Regeln** > **Anwendungsregelsammlung** >  **+ Anwendungsregelsammlung hinzufügen** .

    ![Titel: Hinzufügen einer Anwendungsregelsammlung](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Geben Sie im Bildschirm **Anwendungsregelsammlung hinzufügen** Folgendes an:

    **Oberer Abschnitt**

    | Eigenschaft|  Wert|
    |---|---|
    |Name| FwAppRule|
    |Priority|200|
    |Aktion|Allow|

    **Abschnitt „FQDN-Tags“**

    | Name | Quelladresse | FQDN-Tag | Notizen |
    | --- | --- | --- | --- |
    | Regel 1 | * | „WindowsUpdate“ und „HDInsight“ | Für HDI-Dienste erforderlich |

    **Abschnitt „Ziel-FQDNs“**

    | Name | Quelladressen | Protokoll:Port | Ziel-FQDNs | Notizen |
    | --- | --- | --- | --- | --- |
    | Regel 2 | * | https:443 | login.windows.net | Lässt Windows Anmeldeaktivität zu |
    | Regel 3 | * | https:443 | login.microsoftonline.com | Lässt Windows Anmeldeaktivität zu |
    | Regel 4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Ersetzen Sie `storage_account_name` durch den tatsächlichen Namen Ihres Speicherkontos. Sollen nur HTTPS-Verbindungen verwendet werden, vergewissern Sie sich, dass im Speicherkonto die Option [Sichere Übertragung erforderlich](../storage/common/storage-require-secure-transfer.md) aktiviert ist. Wenn Sie einen privaten Endpunkt für den Zugriff auf Speicherkonten verwenden, ist dieser Schritt nicht erforderlich, und der Speicherdatenverkehr wird nicht an die Firewall weitergeleitet.|

   ![Titel: Eingeben der Details der Anwendungsregelsammlung](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Wählen Sie **Hinzufügen** .

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurieren der Firewall mit Netzwerkregeln

Erstellen Sie die Netzwerkregeln, um Ihren HDInsight-Cluster ordnungsgemäß zu konfigurieren.

1. Navigieren Sie im Anschluss an den vorherigen Schritt zu **Netzwerkregelsammlung** >  **+ Netzwerkregelsammlung hinzufügen** .

1. Geben Sie im Bildschirm **Netzwerkregelsammlung hinzufügen** Folgendes an:

    **Oberer Abschnitt**

    | Eigenschaft|  Wert|
    |---|---|
    |Name| FwNetRule|
    |Priority|200|
    |Aktion|Zulassen|

    **Abschnitt „Diensttags“**

    | Name | Protocol | Quelladressen | Diensttags | Zielports | Notizen |
    | --- | --- | --- | --- | --- | --- |
    | Rule_5 | TCP | * | SQL | 1433 | Konfigurieren Sie im Abschnitt „Diensttags“ für SQL eine Netzwerkregel, mit der Sie SQL-Datenverkehr protokollieren und überwachen können, wenn Sie die von HDInsight bereitgestellten SQL-Standardserver verwenden. Es sei denn, Sie haben Dienstendpunkte für SQL Server im HDInsight-Subnetz konfiguriert, wodurch die Firewall umgangen wird. Wenn Sie benutzerdefinierte SQL Server-Instanzen für Metastores von Ambari, Oozie, Ranger und Hive verwenden, müssen Sie nur den Datenverkehr an Ihre eigenen benutzerdefinierten SQL Server-Instanzen zulassen.|
    | Rule_6 | TCP | * | Azure Monitor | * | (optional) Kunden, die die Verwendung der automatischen Skalierungsfunktion planen, sollten diese Regel hinzufügen. |
    
   ![Titel: Eingeben einer Anwendungsregelsammlung](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Wählen Sie **Hinzufügen** .

### <a name="create-and-configure-a-route-table"></a>Erstellen und Konfigurieren einer Routingtabelle

Erstellen Sie eine Routingtabelle mit den folgenden Einträgen:

* Alle IP-Adressen aus [Integritäts- und Verwaltungsdienste](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions), für die der Typ des nächsten Hops **Internet** lautet. Sie sollten vier IPs der generischen Regionen sowie zwei IPs für Ihre spezifische Region umfassen. Diese Regel ist nur erforderlich, wenn ResourceProviderConnection auf *Inbound* (Eingehend) festgelegt ist. Wenn ResourceProviderConnection auf *Outbound* (Ausgehend) festgelegt ist, werden diese IP-Adressen in der UDR nicht benötigt. 

* Eine Route für ein virtuelles Gerät für IP-Adresse 0.0.0.0/0, wobei der nächste Hop Ihre private Azure Firewall-IP-Adresse ist.

Gehen Sie beispielsweise wie folgt vor, um die Routingtabelle für einen in der US-Region „USA, Osten“ erstellten Cluster zu konfigurieren:

1. Wählen Sie Ihre Azure Firewall-Instanz **Test-FW01** aus. Kopieren Sie die auf dem Blatt **Übersicht** aufgelistete **Private IP-Adresse** . In diesem Beispiel verwenden wir die **Beispieladresse 10.0.2.4** .

1. Navigieren Sie anschließend zu **Alle Dienste** > **Netzwerk** > **Routingtabellen** , und wählen Sie **Routingtabelle erstellen** aus.

1. Navigieren Sie unter Ihrer neuen Route zu **Einstellungen** > **Routen** >  **+ Hinzufügen** . Fügen Sie folgende Routen hinzu:

| Routenname | Adresspräfix | Typ des nächsten Hops | Adresse des nächsten Hops |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Nicht verfügbar |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Nicht verfügbar |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Nicht verfügbar |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Nicht verfügbar |
| 13.82.225.233 | 13.82.225.233/32 | Internet | Nicht verfügbar |
| 40.71.175.99 | 40.71.175.99/32 | Internet | Nicht verfügbar |
| 0.0.0.0 | 0.0.0.0/0 | Virtuelles Gerät | 10.0.2.4 |

Schließen Sie die Konfiguration der Routingtabelle ab:

1. Weisen Sie die erstellte Routingtabelle durch Auswählen von **Subnetze** (unter **Einstellungen** ) Ihrem HDInsight-Subnetz zu.

1. Wählen Sie **+ Zuordnen** aus.

1. Wählen Sie im Bildschirm **Subnetz zuordnen** das virtuelle Netzwerk aus, in dem Ihr Cluster erstellt wurde. Geben Sie außerdem das **Subnetz** an, das Sie für Ihren HDInsight-Cluster verwendet haben.

1. Klicken Sie auf **OK** .

## <a name="edge-node-or-custom-application-traffic"></a>Datenverkehr für Edgeknoten- oder benutzerdefinierte Anwendung

Mithilfe der oben genannten Schritte kann der Cluster problemlos ausgeführt werden. Sie müssen weiterhin Abhängigkeiten konfigurieren, um Ihren benutzerdefinierten Anwendungen ggf. die Ausführung auf den Edgeknoten zu ermöglichen.

Anwendungsabhängigkeiten müssen identifiziert und der Azure Firewall-Instanz oder der Routingtabelle hinzugefügt werden.

Für den Anwendungsdatenverkehr müssen Routen erstellt werden, um Probleme durch asymmetrisches Routing zu vermeiden.

Wenn Ihre Anwendungen andere Abhängigkeiten aufweisen, müssen diese Ihrer Azure Firewall-Instanz hinzugefügt werden. Erstellen Sie Anwendungsregeln, um HTTP/HTTPS-Datenverkehr und Netzwerkregeln zuzulassen.

## <a name="logging-and-scale"></a>Protokollierung und Skalierung

Azure Firewall kann Protokolle an ein paar andere Speichersysteme senden. Anweisungen zum Konfigurieren der Protokollierung für Ihre Firewall finden Sie in den Schritten von [Tutorial: Überwachen von Azure Firewall-Protokollen und -Metriken](../firewall/firewall-diagnostics.md).

Nachdem Sie die Protokollierung eingerichtet haben, können Sie bei Verwendung von Log Analytics den blockierten Datenverkehr mit einer Abfrage wie der folgenden anzeigen:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Die Integration von Azure Firewall und Azure Monitor-Protokollen ist nützlich, wenn Sie eine Anwendung erstmalig einrichten. Dies gilt insbesondere dann, wenn Sie nicht alle Anwendungsabhängigkeiten kennen. Weitere Informationen zu Azure Monitor-Protokollen finden Sie unter [Analysieren von Protokolldaten in Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

Weitere Informationen zu den Skalierungsgrenzwerten von Azure Firewall und zu Anforderungssteigerungen finden Sie in [diesem](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) Dokument sowie in den [Häufig gestellten Fragen](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Zugriff auf den Cluster

Nach erfolgreicher Einrichtung der Firewall können Sie den internen Endpunkt (`https://CLUSTERNAME-int.azurehdinsight.net`) verwenden, um innerhalb des virtuellen Netzwerks auf Ambari zuzugreifen.

Wenn Sie den öffentlichen Endpunkt (`https://CLUSTERNAME.azurehdinsight.net`) oder den SSH-Endpunkt (`CLUSTERNAME-ssh.azurehdinsight.net`) verwenden möchten, vergewissern Sie sich, dass in der Routingtabelle und den NSG-Regeln die richtigen Routen festgelegt wurden, um das [hier](../firewall/integrate-lb.md) beschriebene Problem mit asymmetrischem Routing zu vermeiden. Insbesondere in diesem Fall müssen Sie die Client-IP-Adresse in den eingehenden NSG-Regeln zulassen und sie auch der benutzerdefinierten Routingtabelle hinzufügen, wobei der nächste Hop als `internet` festgelegt sein muss. Wenn das Routing nicht ordnungsgemäß eingerichtet ist, kommt es zu einem Timeoutfehler.

## <a name="next-steps"></a>Nächste Schritte

* [Virtuelle Netzwerkarchitektur mit Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Konfigurieren eines virtuellen Netzwerkgeräts](./network-virtual-appliance.md)