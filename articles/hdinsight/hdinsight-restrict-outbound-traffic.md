---
title: Konfigurieren von Einschränkungen des ausgehenden Netzwerkdatenverkehrs für Azure HDInsight-Cluster
description: Erfahren Sie, wie Sie Einschränkungen des ausgehenden Netzwerkdatenverkehrs für Azure HDInsight-Cluster konfigurieren.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/30/2019
ms.openlocfilehash: 35b9731d82f190cee142173ddf688a92ebf30a38
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730660"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Konfigurieren des ausgehenden Netzwerkdatenverkehrs für Azure HDInsight-Cluster mithilfe von Firewall (Vorschau)

In diesem Artikel werden die Schritte beschrieben, mit denen Sie aus Ihrem HDInsight-Cluster ausgehenden Datenverkehr mithilfe von Azure Firewall schützen. Die folgenden Schritte setzen voraus, dass Sie eine Azure Firewall-Instanz für einen vorhandenen Cluster konfigurieren. Wenn Sie einen neuen Cluster bereitstellen – und hinter einer Firewall – erstellen Sie zunächst Ihren HDInsight-Cluster und das Subnetz, und führen Sie dann die Schritte in diesem Handbuch aus.

## <a name="background"></a>Hintergrund

Azure HDInsight-Cluster werden normalerweise in Ihrem eigenen virtuellen Netzwerk bereitgestellt. Der Cluster verfügt über Abhängigkeiten von Diensten außerhalb dieses virtuellen Netzwerks, die erfordern, dass der Netzwerkzugriff ordnungsgemäß funktioniert.

Mehrere Abhängigkeiten erfordern eingehenden Datenverkehr. Der eingehende Verwaltungsdatenverkehr kann über eine Firewallgerät nicht gesendet werden. Die Quelladressen für diesen Datenverkehr sind bekannt und werden [hier](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) veröffentlicht. Sie können auch Netzwerksicherheitsgruppen-Regeln (NSG) mit diesen Informationen erstellen, um bei den Clustern eingehenden Datenverkehr zu sichern.

Die Abhängigkeiten des ausgehenden HDInsight-Datenverkehrs werden fast ausschließlich mit FQDNs definiert, hinter denen sich keine statischen IP-Adressen befinden. Das Fehlen statischer Adressen bedeutet, dass Netzwerksicherheitsgruppen (NSGs) nicht verwendet werden können, um den ausgehenden Datenverkehr eines Clusters zu sperren. Die Adressen ändern sich häufig, sodass keine Regeln auf Grundlage der aktuellen Auflösung aufgestellt und keine NSGs damit eingerichtet werden können.

Die Lösung zum Sichern ausgehender Adressen besteht in der Verwendung eines Firewallgeräts, das den ausgehenden Datenverkehr basierend auf Domänennamen kontrolliert. Azure Firewall kann ausgehenden HTTP- und HTTPS-Datenverkehr basierend auf den FQDN des Ziels oder [FQDN-Tags](https://docs.microsoft.com/azure/firewall/fqdn-tags) beschränken.

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurieren von Azure Firewall mit HDInsight

Hier sehen Sie eine Zusammenfassung der Schritte, um von HDInsight ausgehenden Datenverkehr mit Azure Firewall zu sperren:
1. Erstellen einer Firewall.
1. Hinzufügen von Anwendungsregeln zur Firewall
1. Fügen Sie der Firewall Netzwerkregeln hinzu.
1. Erstellen Sie eine Routingtabelle.

### <a name="create-a-new-firewall-for-your-cluster"></a>Erstellen einer neuen Firewall für Ihren Cluster

1. Erstellen Sie ein Subnetz namens **AzureFirewallSubnet** in dem virtuellen Netzwerk, in dem sich Ihr Cluster befindet. 
1. Erstellen Sie eine neue Firewall namens **Test-FW01** gemäß den Schritten im [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurieren der Firewall mit Anwendungsregeln

Erstellen Sie eine Anwendungsregelsammlung, die dem Cluster ermöglicht, wichtige Informationen zu senden und zu empfangen.

Wählen Sie die neue Firewall **Test-FW01** über das Azure-Portal aus. Klicken Sie unter **Einstellungen** > **Anwendungsregelsammlung** > **Anwendungsregelsammlung hinzufügen** auf **Regeln**.

![Titel: Hinzufügen einer Anwendungsregelsammlung](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Führen Sie auf dem Bildschirm **Anwendungsregelsammlung hinzufügen** die folgenden Schritte aus:

1. Geben Sie **Name** und **Priorität** ein, und klicken Sie im Dropdownmenü **Aktion** auf **Zulassen**.
1. Fügen Sie die folgenden Regeln hinzu:
    1. Eine Regel zum Zulassen von HDInsight- und Windows Update-Datenverkehr:
        1. Geben Sie im Abschnitt **FQDN-Tags** einen **Namen** ein, und legen Sie **Quelladressen** auf `*` fest.
        1. Wählen Sie **HDInsight** und das **WindowsUpdate** im **FQDN-Tags**- Dropdownmenü aus.
    1. Eine Regel zum Zulassen von Windows-Anmeldeaktivität:
        1. Geben Sie im Abschnitt **Ziel-FQDN** einen **Namen** ein, und legen Sie **Quelladressen** auf `*` fest.
        1. Geben Sie `https:443` unter **Protokoll:Port** und `login.windows.net` unter **Ziel-FQDNS** ein.
    1. Wenn Ihr Cluster durch WASB gesichert ist, fügen Sie eine Regel für WASB hinzu:
        1. Geben Sie im Abschnitt **Ziel-FQDN** einen **Namen** ein, und legen Sie **Quelladressen** auf `*` fest.
        1. Geben Sie `http:80,https:443` unter **Protokoll:Port** und die URL des Speicherkontos unter **Ziel-FQDNS** ein. Das Format sieht etwa so aus: <Name_des_Speicherkontos.blob.core.windows.net>. Um NUR HTTPS-Verbindungen zu verwenden, stellen Sie sicher, dass die Option [Sichere Übertragung erforderlich](https://docs.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer) im Speicherkonto aktiviert ist.
1. Klicken Sie auf **Hinzufügen**.

![Titel: Eingeben der Details der Anwendungsregelsammlung](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurieren der Firewall mit Netzwerkregeln

Erstellen Sie die Netzwerkregeln, um Ihren HDInsight-Cluster ordnungsgemäß zu konfigurieren.

1. Wählen Sie die neue Firewall **Test-FW01** über das Azure-Portal aus.
1. Klicken Sie unter **Einstellungen** > **Netzwerkregelsammlung** > **Netzwerkregelsammlung hinzufügen** auf **Regeln**.
1. Geben Sie im Bildschirm **Netzwerkregelsammlung hinzufügen** **Name** und **Priorität** ein, und klicken Sie im **Aktion**-Dropdownmenü auf **Zulassen**.
1. Erstellen Sie die folgenden Regeln:
    1. Eine Netzwerkregel im Abschnitt „IP-Adressen“, die dem Cluster ermöglicht, die Uhrsynchronisierung mit NTP auszuführen.
        1. Geben Sie im Abschnitt **Regeln** einen **Namen** ein, und wählen Sie in der **Protokoll**-Dropdownliste den Eintrag **UDP** aus.
        1. Legen Sie **Quelladressen** und **Zieladressen** auf `*` fest.
        1. Legen Sie **Zielports** auf 123 fest.
    1. Wenn Sie das Enterprise-Sicherheitspaket (Enterprise Security Package, ESP) verwenden, fügen Sie im Abschnitt „IP-Adressen“ eine Netzwerkregel hinzu, die die Kommunikation mit AAD-DS für ESP-Cluster ermöglicht.
        1. Bestimmen Sie die zwei IP-Adressen für Ihre Domänencontroller.
        1. Geben Sie in der nächsten Zeile im Abschnitt **Regeln** einen **Namen** ein, und wählen Sie **Beliebig** in der **Protokoll**-Dropdownliste aus.
        1. Legen Sie **Quelladressen** auf `*` fest.
        1. Geben Sie alle IP-Adressen für Ihre Domänencontroller durch Kommas getrennt in **Zieladressen** ein.
        1. Legen Sie **Zielports** auf `*` fest.
    1. Wenn Sie Azure Data Lake Storage verwenden, können Sie im Abschnitt „IP-Adressen“ eine Netzwerkregel zur Behandlung eines SNI-Problems mit ADLS Gen1 und Gen2 hinzufügen. Diese Option leitet den Datenverkehr zur Firewall, was zu höheren Kosten für große Datenmengen führen könnte, aber der Datenverkehr wird in Firewallprotokollen protokolliert und überwacht.
        1. Bestimmen Sie die IP-Adresse für Ihr Data Lake Storage-Konto. Sie können mit einem PowerShell-Befehl wie `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` den FQDN in eine IP-Adresse auflösen.
        1. Geben Sie in der nächsten Zeile im Abschnitt **Regeln** einen **Namen** ein, und wählen Sie in der **Protokoll**-Dropdownliste den Eintrag **TCP** aus.
        1. Legen Sie **Quelladressen** auf `*` fest.
        1. Geben Sie die IP-Adresse für Ihr Speicherkonto in **Zieladressen** ein.
        1. Legen Sie **Zielports** auf `*` fest.
    1. (Optional) Wenn Sie Log Analytics verwenden, erstellen Sie im Abschnitt „IP-Adressen“ eine Netzwerkregel, um die Kommunikation mit Ihrem Log Analytics-Arbeitsbereich zu ermöglichen.
        1. Geben Sie in der nächsten Zeile im Abschnitt **Regeln** einen **Namen** ein, und wählen Sie in der **Protokoll**-Dropdownliste den Eintrag **TCP** aus.
        1. Legen Sie **Quelladressen** auf `*` fest.
        1. Legen Sie **Zieladressen** auf `*` fest.
        1. Legen Sie **Zielports** auf `12000` fest.
    1. Konfigurieren Sie im Abschnitt „Diensttags“ für SQL eine Regel, die es Ihnen erlaubt, SQL-Datenverkehr zu protokollieren und zu überwachen, sofern Sie im HDInsight-Subnetz keine Dienstendpunkte für SQL Server konfiguriert haben, wodurch die Firewall umgangen wird.
        1. Geben Sie in der nächsten Zeile im Abschnitt **Regeln** einen **Namen** ein, und wählen Sie in der **Protokoll**-Dropdownliste den Eintrag **TCP** aus.
        1. Legen Sie **Quelladressen** auf `*` fest.
        1. Legen Sie **Zieladressen** auf `*` fest.
        1. Wählen Sie **Sql** in der **Diensttags**-Dropdownliste aus.
        1. Legen Sie **Zielports** auf `1433,11000-11999,14000-14999` fest.
1. Klicken Sie auf **Hinzufügen**, um die Erstellung Ihrer Netzwerkregelsammlung abzuschließen.

![Titel: Eingeben der Details der Anwendungsregelsammlung](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Erstellen und Konfigurieren einer Routingtabelle

Erstellen Sie eine Routingtabelle mit den folgenden Einträgen:

1. Sechs Adressen aus [dieser Liste der für die HDInsight-Verwaltung erforderlichen IP-Adressen](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) mit dem nächstem Hop **Internet**:
    1. Vier IP-Adressen für alle Cluster in allen Regionen
    1. Zwei für die Region, in der der Cluster erstellt wird, spezifische IP-Adressen
1. Eine Route für ein virtuelles Gerät für IP-Adresse 0.0.0.0/0, wobei der nächste Hop Ihre private Azure Firewall-IP-Adresse ist.

Um z.B. die Routingtabelle für ein in der US-Region „USA, Mitte“ erstelltes Cluster zu konfigurieren, verwenden Sie die folgenden Schritte:

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie Ihre Azure Firewall-Instanz **Test-FW01** aus. Kopieren Sie die auf dem Blatt **Übersicht** aufgelistete **Private IP-Adresse**. In diesem Beispiel verwenden wir die **Beispieladresse 10.1.1.4**
1. Erstellen Sie eine neue Routingtabelle.
1. Klicken Sie unter **Einstellungen** auf **Routen**.
1. Klicken Sie auf **Hinzufügen**, um Routen für die IP-Adressen in der folgenden Tabelle zu erstellen.

| Routenname | Adresspräfix | Typ des nächsten Hops | Adresse des nächsten Hops |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Nicht verfügbar |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Nicht verfügbar |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Nicht verfügbar |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Nicht verfügbar |
| 13.67.223.215 | 13.67.223.215/32 | Internet | Nicht verfügbar |
| 40.86.83.253 | 40.86.83.253/32 | Internet | Nicht verfügbar |
| 0.0.0.0 | 0.0.0.0/0 | Virtuelles Gerät | 10.1.1.4 |

Schließen Sie die Konfiguration der Routingtabelle ab:

1. Weisen Sie die Routingtabelle, die Sie erstellt haben, durch Klicken auf **Subnetze** unter **Einstellungen** und dann **Zuordnen** Ihrem HDInsight-Subnetz zu.
1. Wählen Sie auf dem Bildschirm **Subnetz zuordnen** das virtuelle Netzwerk, in dem Ihr Cluster erstellt wurde, und das **HDInsight-Subnetz**, das Sie für Ihren HDInsight-Cluster verwendet haben.
1. Klicken Sie auf **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Datenverkehr für Edgeknoten- oder benutzerdefinierte Anwendung

Mithilfe der oben genannten Schritte kann der Cluster problemlos ausgeführt werden. Sie müssen weiterhin Abhängigkeiten konfigurieren, um Ihren benutzerdefinierten Anwendungen ggf. die Ausführung auf den Edgeknoten zu ermöglichen.

Anwendungsabhängigkeiten müssen identifiziert und der Azure Firewall-Instanz oder der Routingtabelle hinzugefügt werden.

Für den Anwendungsdatenverkehr müssen Routen erstellt werden, um Probleme durch asymmetrisches Routing zu vermeiden.

Wenn Ihre Anwendungen andere Abhängigkeiten aufweisen, müssen diese Ihrer Azure Firewall-Instanz hinzugefügt werden. Erstellen Sie Anwendungsregeln, um HTTP/HTTPS-Datenverkehr und Netzwerkregeln zuzulassen.

## <a name="logging"></a>Protokollierung

Azure Firewall kann Protokolle an ein paar andere Speichersysteme senden. Anweisungen zum Konfigurieren der Protokollierung für Ihre Firewall finden Sie in den Schritten von [Tutorial: Überwachen von Azure Firewall-Protokollen und -Metriken](../firewall/tutorial-diagnostics.md).

Nachdem Sie die Protokollierung eingerichtet haben, können Sie, wenn Sie bei Log Analytics eingehende Daten protokollieren, blockierten Datenverkehr mit einer Abfrage wie z.B. der Folgenden anzeigen:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Die Integration Ihrer Azure Firewall-Instanz in Azure Monitor-Protokolle ist nützlich, wenn Sie eine Anwendung erstmals einrichten und nicht alle Anwendungsabhängigkeiten kennen. Weitere Informationen zu Azure Monitor-Protokollen finden Sie unter [Analysieren von Protokolldaten in Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="access-to-the-cluster"></a>Zugriff auf den Cluster
Nach der erfolgreichen Einrichtung der Firewall können Sie den internen Endpunkt (https://<clustername>-int.azurehdinsight.net) verwenden, um aus dem VNET auf Ambari zuzugreifen. Um den öffentlichen Endpunkt (https://<clustername>.azurehdinsight.net) oder den SSH-Endpunkt (<clustername>-ssh.azurehdinsight.net) zu verwenden, stellen Sie sicher, dass Sie über die richtigen Routen in der Routingtabelle verfügen und NSG-Regeln eingerichtet haben, um das Problem mit dem asymmetrischen Routing zu vermeiden, das [hier](https://docs.microsoft.com/en-us/azure/firewall/integrate-lb) beschrieben wird.

## <a name="configure-another-network-virtual-appliance"></a>Konfigurieren eines anderen virtuellen Netzwerkgeräts

>[!Important]
> Die folgenden Informationen sind **nur** erforderlich, wenn Sie ein anderes virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) als Azure Firewall konfigurieren möchten.

Mit den vorherigen Anweisungen können Sie Azure Firewall zum Einschränken des ausgehenden Datenverkehrs Ihres HDInsight-Clusters konfigurieren. Azure Firewall wird automatisch dazu konfiguriert, den Datenverkehr für viele der häufigen, wichtigen Szenarien zuzulassen. Wenn Sie ein anderes virtuelles Netzwerkgerät nutzen möchten, müssen Sie eine Reihe zusätzlicher Features manuell konfigurieren. Bedenken Sie Folgendes beim Konfigurieren Ihres virtuellen Netzwerkgeräts:

* Dienste, die Dienstendpunkte unterstützen, sollten mit Dienstendpunkten konfiguriert werden.
* IP-Adressabhängigkeiten gelten für Nicht-HTTP/S-Datenverkehr (TCP- und UDP-Datenverkehr).
* FQDN-HTTP/HTTPS-Endpunkte können in Ihrem virtuellen Netzwerkgerät bereitgestellt werden.
* Platzhalter-HTTP/HTTPS-Endpunkte sind Abhängigkeiten, die basierend auf einer Reihe von Qualifizierern variieren können.
* Weisen Sie die Routingtabelle zu, die Sie für Ihr HDInsight-Subnetz erstellen.

### <a name="service-endpoint-capable-dependencies"></a>Dienstendpunktfähige Abhängigkeiten

| **Endpunkt** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP-Adressabhängigkeiten

| **Endpunkt** | **Details** |
|---|---|
| \*:123 | NTP-Uhrzeitüberprüfung. Datenverkehr wird an mehreren Endpunkten am Port 123 überprüft. |
| [Hier](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) veröffentlichte IP-Adressen | Diese gehören zum HDInsight-Dienst. |
| AAD-DS – private IP-Adressen für ESP-Cluster |
| \*:16800 für KMS-Aktivierung von Windows |
| \*12000 für Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN-HTTP/HTTPS-Abhängigkeiten

>[!Important]
> Die folgende Liste enthält nur ein paar der wichtigsten FQDNs. Sie erhalten die vollständige Liste der FQDNs für die Konfiguration Ihres virtuellen Netzwerkgeräts [in dieser Datei](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Endpunkt**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Nächste Schritte

* [Virtuelle Netzwerkarchitektur mit Azure HDInsight](hdinsight-virtual-network-architecture.md)
