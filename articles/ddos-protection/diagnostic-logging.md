---
title: Azure DDoS Protection Standard – Berichte und Datenflussprotokolle
description: Erfahren Sie, wie Sie Berichte und Datenflussprotokolle konfigurieren.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 42d4467a6441e517ef3194f701eb47e7e68505b1
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806306"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Anzeigen und Konfigurieren der DDoS-Diagnoseprotokollierung

Die Standardversion von Azure DDoS Protection liefert per Analyse von DDoS-Angriffen ausführliche Erkenntnisse zu Angriffen und ermöglicht eine Visualisierung. Kunden, die ihre virtuellen Netzwerke vor DDoS-Angriffen schützen, verfügen über detaillierte Einblicke in den Datenverkehr von Angriffen und die Aktionen, die zur Eindämmung des Angriffs durchgeführt werden. Hierfür werden Berichte zur Angriffsentschärfung und Protokolle zum Verlauf der Entschärfung genutzt. Über Azure Monitor werden umfangreiche Telemetriedaten bereitgestellt. Diese umfassen u. a. detaillierte Metriken während der Dauer eines DDoS-Angriffs. Warnungen können für beliebige durch den DDoS-Schutz verfügbar gemachte Azure Monitor-Metriken konfiguriert werden. Die Protokollierung kann zudem mit [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics und Azure Storage für die erweiterte Analyse über die Schnittstelle für die Azure Monitor-Diagnose integriert werden.

Für Azure DDoS Protection Standard sind die folgenden Diagnoseprotokolle verfügbar: 

- **DDoSProtectionNotifications:** Sie erhalten immer eine Benachrichtigung, wenn eine Ressource mit öffentlicher IP-Adresse angegriffen wird und wenn der Angriff entschärft wurde.
- **DDoSMitigationFlowLogs:** Mit Datenflussprotokollen zur Entschärfung von Angriffen können Sie den verworfenen Datenverkehr, weitergeleiteten Datenverkehr und andere interessante Datenpunkte bei einem aktiven DDoS-Angriff nahezu in Echtzeit überprüfen. Sie können den konstanten Datenstrom dieser Daten in Azure Sentinel oder Ihren SIEM-Drittanbietersystemen per Event Hub erfassen, um eine Überwachung nahezu in Echtzeit zu ermöglichen, gegebenenfalls Aktionen durchzuführen und Ihre erforderlichen Abwehrmaßnahmen zu ermitteln.
- **DDoSMitigationReports:** Für Berichte zur Entschärfung von Angriffen werden die NetFlow-Protokolldaten verwendet. Diese Daten werden aggregiert, um ausführliche Informationen zum Angriff auf Ihre Ressource zu liefern. Jedes Mal, wenn eine öffentliche IP-Ressource angegriffen wird, beginnt die Berichterstellung, sobald der Entschärfungsvorgang gestartet wurde. Alle fünf Minuten wird ein inkrementeller Bericht generiert, und für den gesamten Entschärfungszeitraum wird ein Abschlussbericht erstellt. So wird sichergestellt, dass Sie bei einem länger andauernden DDoS-Angriff die aktuellste Momentaufnahme des Berichts zur Entschärfung (alle fünf Minuten) und eine vollständige Zusammenfassung nach Abschluss der Entschärfung anzeigen können. 
- **AllMetrics:** Dieses Protokoll enthält alle Metriken, die während eines DDoS-Angriffs verfügbar sind. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren von DDoS-Diagnoseprotokollen, einschließlich Benachrichtigungen, Entschärfungsberichten und Entschärfungs-Datenflussprotokollen 
> * Aktivieren der Diagnoseprotokollierung für alle öffentlichen IP-Adressen in einem definierten Bereich
> * Anzeigen von Protokolldaten in Arbeitsmappen

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Damit Sie die Schritte in diesem Tutorial ausführen können, müssen Sie zunächst einen [Azure DDoS Protection Standard-Schutzplan](manage-ddos-protection.md) erstellen. Außerdem muss DDoS Protection Standard in einem virtuellen Netzwerk aktiviert sein.
- DDoS überwacht öffentliche IP-Adressen, die Ressourcen in einem virtuellen Netzwerk zugewiesen sind. Wenn Sie keine Ressourcen mit öffentlichen IP-Adressen im virtuellen Netzwerk besitzen, müssen Sie zunächst eine Ressource mit einer öffentlichen IP-Adresse erstellen. Sie können die öffentliche IP-Adresse aller Ressourcen (einschließlich Azure Load Balancer-Instanzen, bei denen sich die virtuellen Back-End-Computer im virtuellen Netzwerk befinden) überwachen, die über Resource Manager (nicht klassisch) bereitgestellt werden und unter [virtuelles Netzwerk für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) aufgeführt sind, mit Ausnahme der Ressourcen für Azure App Service-Umgebungen. Um mit diesem Tutorial fortzufahren, können Sie schnell einen virtuellen [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer erstellen.    

## <a name="configure-ddos-diagnostic-logs"></a>Konfigurieren von DDoS-Diagnoseprotokollen

Wenn Sie die Diagnoseprotokollierung für alle öffentlichen IP-Adressen in einer Umgebung automatisch aktivieren möchten, fahren Sie mit [Aktivieren der Diagnoseprotokollierung für alle öffentlichen IP-Adressen](#enable-diagnostic-logging-on-all-public-ips) fort.

1. Wählen Sie oben im Portal auf der linken Seite **Alle Dienste** aus.
2. Geben Sie *Monitor* in das Feld **Filter** ein. Wenn **Monitor** in den Ergebnissen angezeigt wird, wählen Sie diese Angabe aus.
3. Klicken Sie unter **Einstellungen** auf **Diagnoseeinstellungen**.
4. Wählen Sie das **Abonnement** und die **Ressourcengruppe** aus, die die öffentliche zu protokollierende IP-Adresse enthält.
5. Wählen Sie unter **Ressourcentyp** die Option **Öffentliche IP-Adresse** aus und dann die öffentliche IP-Adresse, für die Protokolle aktiviert werden sollen.
6. Klicken Sie auf **Diagnoseeinstellung hinzufügen**. Wählen Sie unter **Kategoriedetails** unter den folgenden Optionen die von Ihnen benötigten aus, und klicken Sie dann auf **Speichern**.

    ![DDoS-Diagnoseeinstellungen](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. Wählen Sie unter **Zieldetails** unter den folgenden Optionen die von Ihnen benötigten aus:

    - **In einem Speicherkonto archivieren:** Daten werden in einem Azure Storage-Konto gespeichert. Weitere Informationen zu dieser Option finden Sie unter [Archivieren von Ressourcenprotokollen](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **An einen Event Hub streamen:** Erlaubt einem Protokollempfänger das Erfassen von Protokollen mithilfe eines Azure-Event Hubs. Event Hubs ermöglichen die Integration in Splunk oder andere SIEM-Systeme. Weitere Informationen zu dieser Option finden Sie unter [Streamen von Ressourcenprotokollen an Event Hubs](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **An Log Analytics senden:** Schreibt Protokolle in den Azure Monitor-Dienst. Weitere Informationen zu dieser Option finden Sie unter [Sammeln von Azure-Dienstprotokollen und Metriken zur Verwendung in Azure Monitor-Protokollen](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="log-schemas"></a>Protokollschemas

In der folgenden Tabelle sind die Feldnamen und Beschreibungen aufgeführt:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Feldname | BESCHREIBUNG |
| --- | --- |
| **TimeGenerated** | Hier werden das Datum und die Uhrzeit (UTC) der Erstellung der Benachrichtigung angegeben. |
| **ResourceId** | Hier wird die Ressourcen-ID Ihrer öffentlichen IP-Adresse angegeben. |
| **Kategorie** | Bei Benachrichtigungen ist der Wert für dieses Feld `DDoSProtectionNotifications`.|
| **ResourceGroup** | Hier wird die Ressourcengruppe angegeben, die Ihre öffentliche IP-Adresse und Ihr virtuelles Netzwerk enthält. |
| **SubscriptionId** | Hier wird die Abonnement-ID für Ihren DDoS-Schutzplan angegeben. |
| **Ressource** | Hier wird der Name Ihrer öffentlichen IP-Adresse angegeben. |
| **ResourceType** | Der Wert für dieses Feld ist immer `PUBLICIPADDRESS`. |
| **OperationName** | Bei Benachrichtigungen ist der Wert für dieses Feld `DDoSProtectionNotifications`.  |
| **Meldung** | Hier werden Details zum Angriff angegeben. |
| **Type** | Hier wird der Benachrichtigungstyp angegeben. Mögliche Werte sind: `MitigationStarted`. `MitigationStopped`. |
| **PublicIpAddress** | Hier wird Ihre öffentliche IP-Adresse angegeben. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Feldname | BESCHREIBUNG |
| --- | --- |
| **TimeGenerated** | Hier werden das Datum und die Uhrzeit (UTC) der Erstellung des Datenflussprotokolls angegeben. |
| **ResourceId** | Hier wird die Ressourcen-ID Ihrer öffentlichen IP-Adresse angegeben. |
| **Kategorie** | Bei Datenflussprotokollen ist der Wert für dieses Feld `DDoSMitigationFlowLogs`.|
| **ResourceGroup** | Hier wird die Ressourcengruppe angegeben, die Ihre öffentliche IP-Adresse und Ihr virtuelles Netzwerk enthält. |
| **SubscriptionId** | Hier wird die Abonnement-ID für Ihren DDoS-Schutzplan angegeben. |
| **Ressource** | Hier wird der Name Ihrer öffentlichen IP-Adresse angegeben. |
| **ResourceType** | Der Wert für dieses Feld ist immer `PUBLICIPADDRESS`. |
| **OperationName** | Bei Datenflussprotokollen ist der Wert für dieses Feld `DDoSMitigationFlowLogs`. |
| **Meldung** | Hier werden Details zum Angriff angegeben. |
| **SourcePublicIpAddress** | Hier wird die öffentliche IP-Adresse des Clients angegeben, der Datenverkehr zu Ihrer öffentlichen IP-Adresse generiert. |
| **SourcePort** | Hier wird eine Portnummer zwischen 0 und 65535 angegeben. |
| **DestPublicIpAddress** | Hier wird Ihre öffentliche IP-Adresse angegeben. |
| **DestPort** | Hier wird eine Portnummer zwischen 0 und 65535 angegeben. |
| **Protokoll** | Hier wird der Protokolltyp angegeben. Mögliche Werte sind `tcp`, `udp` oder `other`.|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Feldname | BESCHREIBUNG |
| --- | --- |
| **TimeGenerated** | Hier werden das Datum und die Uhrzeit (UTC) der Erstellung des Berichts angegeben. |
| **ResourceId** | Hier wird die Ressourcen-ID Ihrer öffentlichen IP-Adresse angegeben. |
| **Kategorie** | Bei Benachrichtigungen ist der Wert für dieses Feld `DDoSProtectionNotifications`.|
| **ResourceGroup** | Hier wird die Ressourcengruppe angegeben, die Ihre öffentliche IP-Adresse und Ihr virtuelles Netzwerk enthält. |
| **SubscriptionId** | Hier wird die Abonnement-ID für Ihren DDoS-Schutzplan angegeben. |
| **Ressource** | Hier wird der Name Ihrer öffentlichen IP-Adresse angegeben. |
| **ResourceType** | Der Wert für dieses Feld ist immer `PUBLICIPADDRESS`. |
| **OperationName** | Bei Entschärfungsberichten ist der Wert für dieses Feld `DDoSMitigationReports`. |
| **ReportType** | Mögliche Werte sind `Incremental` und `PostMitigation`.|
| **MitigationPeriodStart** | Hier werden das Datum und die Uhrzeit (UTC) des Beginns der Entschärfung angegeben.  |
| **MitigationPeriodEnd** | Hier werden das Datum und die Uhrzeit (UTC) des Endes der Entschärfung angegeben. |
| **IPAddress** | Hier wird Ihre öffentliche IP-Adresse angegeben. |
| **AttackVectors** |  Hier werden die Angriffstypen aufgeschlüsselt. Schlüssel sind `TCP SYN flood`, `TCP flood`, `UDP flood`, `UDP reflection` und `Other packet flood`.|
| **TrafficOverview** |  Hier wird der Angriffsdatenverkehr aufgeschlüsselt. Schlüssel sind `Total packets`, `Total packets dropped`, `Total TCP packets`, `Total TCP packets dropped`, `Total UDP packets`, `Total UDP packets dropped`, `Total Other packets` und `Total Other packets dropped`. |
| **Protokolle** | Hier werden die betroffenen Protokolle aufgeschlüsselt. Schlüssel sind `TCP`, `UDP` und `Other`. |
| **DropReasons** | Hier werden die Gründe für verworfene Pakete aufgeschlüsselt. Schlüssel sind `Protocol violation invalid TCP syn`, `Protocol violation invalid TCP`, `Protocol violation invalid UDP`, `UDP reflection`, `TCP rate limit exceeded`, `UDP rate limit exceeded`, `Destination limit exceeded`, `Other packet flood`, `Rate limit exceeded` und `Packet was forwarded to service`. |
| **TopSourceCountries** | Hier werden die zehn wichtigsten Quellländer für eingehenden Datenverkehr aufgeschlüsselt. |
| **TopSourceCountriesForDroppedPackets** | Hier werden die zehn wichtigsten Quellländer für Angriffsdatenverkehr aufgeschlüsselt, der entschärft wird/wurde. |
| **TopSourceASNs** | Hier werden die zehn wichtigsten autonomen Systemnummern (ASN) für den eingehenden Datenverkehr aufgeschlüsselt.  |
| **SourceContinents** | Hier werden die Quellkontinente für eingehenden Datenverkehr aufgeschlüsselt. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Aktivieren der Diagnoseprotokollierung für alle öffentlichen IP-Adressen

Diese [Vorlage](https://aka.ms/ddosdiaglogs) erstellt eine Azure Policy-Definition, um die Diagnoseprotokollierung für alle Protokolle für öffentliche IP-Adressen in einem definierten Bereich automatisch zu aktivieren.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FEnable%2520Diagnostic%2520Logging%2FAzure%2520Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Anzeigen von Protokolldaten in Arbeitsmappen

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel-Datenconnector

Sie können Protokolle mit Azure Sentinel verbinden, Ihre Daten in Arbeitsmappen anzeigen und analysieren, benutzerdefinierte Warnungen erstellen und dies in Untersuchungsprozesse integrieren. Informationen zum Herstellen einer Verbindung mit Azure Sentinel finden Sie unter [Herstellen einer Verbindung mit Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Azure Sentinel-DDoS-Connector](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection-Arbeitsmappe

Zum Bereitstellen einer Arbeitsmappe zur Angriffsanalyse können Sie diese [ARM-Vorlage (Azure Resource Manager)](https://aka.ms/ddosworkbook) verwenden. Mit dieser Arbeitsmappe können Sie Angriffsdaten in mehreren filterbaren Panels visualisieren, um leicht nachzuvollziehen, was auf dem Spiel steht. 

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS Protection-Arbeitsmappe](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Überprüfen und Testen

Wenn Sie zur Überprüfung Ihrer Protokolle einen DDoS-Angriff simulieren möchten, lesen Sie den Artikel [Durchführen von Simulationstests](test-through-simulations.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

- Konfigurieren von DDoS-Diagnoseprotokollen, einschließlich Benachrichtigungen, Entschärfungsberichten und Entschärfungs-Datenflussprotokollen 
- Aktivieren der Diagnoseprotokollierung für alle öffentlichen IP-Adressen in einem definierten Bereich
- Anzeigen von Protokolldaten in Arbeitsmappen

Informationen zur Konfiguration von Angriffswarnungen finden Sie im nächsten Tutorial.

> [!div class="nextstepaction"]
> [Anzeigen und Konfigurieren von DDoS-Schutzwarnungen](alerts.md)
