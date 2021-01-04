---
title: Verbinden von Datenquellen mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Datenquellen wie Microsoft 365 Defender (vormals Microsoft Threat Protection), Microsoft 365 und Office 365, Azure AD, ATP und Cloud App Security mit Azure Sentinel verbinden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 432104c7e2cb9486dfb47a793ae73829d8705b60
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655815"
---
# <a name="connect-data-sources"></a>Herstellen einer Verbindung mit Datenquellen

Nachdem Sie Azure Sentinel aktiviert haben, müssen Sie als Erstes eine Verbindung mit Ihren Datenquellen herstellen. Azure Sentinel enthält eine Reihe von Connectors für Microsoft-Lösungen, die vorkonfiguriert verfügbar sind und Echtzeitintegration bieten, u. a. für Microsoft 365 Defender-Lösungen (ehemals Microsoft Threat Protection), Microsoft 365-Quellen (darunter Office 365, Azure AD, Azure ATP, Microsoft Defender for Identity (ehemals Azure ATP) und Microsoft Cloud App Security). Außerdem stehen integrierte Connectors für Sicherheitslösungen von anderen Anbietern als Microsoft zur Verfügung. Sie können auch Common Event Format (CEF), Syslog oder eine REST-API verwenden, um Ihre Datenquellen mit Azure Sentinel zu verbinden.

1. Wählen Sie im Menü die Option **Data connectors** (Datenconnectors) aus. Auf dieser Seite können Sie die gesamte Liste der von Azure Sentinel bereitstellten Connectors sowie deren Status anzeigen. Wählen Sie den gewünschten Connector für die Verbindungsherstellung und anschließend **Open connector page** (Connectorseite öffnen) aus. 

   ![Datenconnectors-Katalog](./media/collect-data/collect-data-page.png)

1. Vergewissern Sie sich auf der Seite des spezifischen Connectors, dass alle Voraussetzungen erfüllt sind, und folgen Sie anschließend der Anleitung, um die Daten mit Azure Sentinel zu verbinden. Möglicherweise dauert es etwas, bis die Protokollsynchronisierung mit Azure Sentinel beginnt. Nach erfolgreicher Verbindungsherstellung werden eine Zusammenfassung der Daten im Diagramm **Empfangene Daten** sowie der Konnektivitätsstatus der Datentypen angezeigt.

   ![Konfigurieren von Datenconnectors](./media/collect-data/opened-connector-page.png)
  
1. Klicken Sie auf die Registerkarte **Nächste Schritte**, um eine Liste mit vorgefertigten Inhalten anzuzeigen, die von Azure Sentinel für den spezifischen Datentyp bereitgestellt wird.

   ![Nächste Schritte für Connectors](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Datenverbindungsmethoden

Die folgenden Datenverbindungsmethoden werden von Azure Sentinel unterstützt:

- **Dienst-zu-Dienst-Integration**:<br> Einige Dienste werden nativ verbunden, z. B. AWS und Microsoft-Dienste. Diese Dienste nutzen die Azure-Basiskomponenten für die sofortige Integration. Die folgenden Lösungen können mit wenigen Klicks verbunden werden:
    - [Amazon Web Services – CloudTrail](connect-aws.md)
    - [Azure Active Directory](connect-azure-active-directory.md) – Überwachungs- und Anmeldeprotokolle
    - [Azure-Aktivität](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure DDoS Protection](connect-azure-ddos-protection.md)
    - [Azure Defender für IoT](connect-asc-iot.md) (früher: Azure Security Center für IoT)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Firewall](connect-azure-firewall.md)
    - [Azure Security Center](connect-azure-security-center.md): Lösungen für Benachrichtigungen aus Azure Defender
    - [Microsoft Web Application Firewall (WAF)](connect-azure-waf.md) (früher: Microsoft WAF)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Domänennamenserver](connect-dns.md)
    - [Microsoft 365 Defender](connect-microsoft-365-defender.md) schließt MDATP-Rohdaten ein
    - [Microsoft Defender for Endpoint](connect-microsoft-defender-advanced-threat-protection.md) (vormals Microsoft Defender Advanced Threat Protection)
    - [Microsoft Defender for Identity](connect-azure-atp.md) (vormals Azure Advanced Threat Protection)
    - [Microsoft Defender für Office 365](connect-office-365-advanced-threat-protection.md) (früher: Office 365 Advanced Threat Protection)
    - [Office 365](connect-office-365.md) (jetzt mit Teams!)
    - [Windows-Firewall](connect-windows-firewall.md)
    - [Windows-Sicherheitsereignisse](connect-windows-security-events.md)

- **Externe Lösungen per API**: Einige Datenquellen werden mit APIs verbunden, die von der verbundenen Datenquelle bereitgestellt werden. Normalerweise werden bei den meisten Sicherheitstechnologien verschiedene APIs bereitgestellt, über die Ereignisprotokolle abgerufen werden können. Mit den APIs wird eine Verbindung mit Azure Sentinel hergestellt, und es werden bestimmte Datentypen erfasst und an Azure Log Analytics gesendet. Per API verbundene Appliances sind:
    
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Okta SSO](connect-okta-single-sign-on.md)
    - [Orca Security](connect-orca-security-alerts.md)
    - [Perimeter 81-Protokolle](connect-perimeter-81-logs.md)
    - [Proofpoint TAP](connect-proofpoint-tap.md)
    - [Qualys VM](connect-qualys-vm.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [VMware Carbon Black Cloud Endpoint Standard](connect-vmware-carbon-black.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Externe Lösungen per Agent**: Azure Sentinel kann über einen Agent mit allen anderen Datenquellen verbunden werden, die ein Echtzeit-Protokollstreaming über das Syslog-Protokoll durchführen können.

    Für die meisten Appliances wird das Syslog-Protokoll zum Senden von Nachrichten genutzt, die das eigentliche Protokoll und die Daten zum Protokoll enthalten. Das Format der Protokolle variiert zwar, aber die meisten Appliances unterstützen die CEF-basierte Formatierung für Protokolldaten. 

    Der Azure Sentinel-Agent, bei dem es sich eigentlich um den Log Analytics-Agent handelt, konvertiert Protokolle im Common Event Format in ein Format, das von Log Analytics erfasst werden kann. Je nach Appliancetyp wird der Agent entweder direkt auf der Appliance oder auf einem dedizierten Linux-basierten Server für die Protokollweiterleitung installiert. Der Agent für Linux empfängt Ereignisse vom Syslog-Daemon per UDP. Falls für einen Linux-Computer aber die Erfassung einer großen Menge von Syslog-Ereignissen erwartet wird, werden diese per TCP über den Syslog-Daemon an den Agent und von dort an Log Analytics gesendet.

    - **Firewalls, Proxys und Endpunkte – CEF:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Forcepoint-Produkte](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Illusive Networks AMS](connect-illusive-attack-management-system.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
        - [Weitere CEF-basierte Appliances](connect-common-event-format.md)
    - **Firewalls, Proxys und Endpunkte – Syslog:**
        - [Infoblox NIOS](connect-infoblox.md)
        - [Pulse Connect Secure](connect-pulse-connect-secure.md)
        - [Sophos XG](connect-sophos-xg-firewall.md)
        - [Symantec Proxy SG](connect-symantec-proxy-sg.md)
        - [Symantec VIP](connect-symantec-vip.md)
        - [Weitere Syslog-basierte Appliances](connect-syslog.md)
    - DLP-Lösungen
    - [Threat Intelligence-Anbieter](connect-threat-intelligence.md)
    - [DNS-Computer](connect-dns.md): Der Agent wird direkt auf dem DNS-Computer installiert.
    - [Azure Stack-VMs](connect-azure-stack.md)
    - Linux-Server
    - Andere Clouds
    
## <a name="agent-connection-options"></a>Agent-Verbindungsoptionen<a name="agent-options"></a>

Zum Verbinden Ihrer externen Appliance mit Azure Sentinel muss der Agent auf einem dedizierten Computer (VM oder lokal) bereitgestellt werden, um die Kommunikation zwischen der Appliance und Azure Sentinel zu unterstützen. Sie können den Agent automatisch oder manuell bereitstellen. Die automatische Bereitstellung ist nur verfügbar, wenn es sich bei Ihrem dedizierten Computer um einen neuen virtuellen Computer handelt, den Sie in Azure erstellen. 


![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativ können Sie den Agent manuell auf einem vorhandenen virtuellen Azure-Computer, auf einem virtuellen Computer in einer anderen Cloud oder auf einem lokalen Computer bereitstellen.

![CEF lokal](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Zuordnen von Datentypen mit Azure Sentinel-Verbindungsoptionen


| **Datentyp** | **Herstellen einer Verbindung** | **Datenconnector** | **Kommentare** |
|------|---------|-------------|------|
| AWSCloudTrail | [Herstellen einer Verbindung mit AWS](connect-aws.md) | &#10003; | |
| AzureActivity | [Herstellen einer Verbindung mit Azure-Aktivitäten](connect-azure-activity.md) und [Übersicht über Aktivitätsprotokolle](../azure-monitor/platform/platform-logs-overview.md)| &#10003; | |
| AuditLogs | [Herstellen einer Verbindung mit Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Herstellen einer Verbindung mit Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Azure-Diagnose](../firewall/firewall-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Azure Information Protection-Berichte](/azure/information-protection/reports-aip)<br>[Herstellen einer Verbindung mit Azure Information Protection](connect-azure-information-protection.md)  | &#10003; | Dabei wird in der Regel die Funktion **InformationProtectionEvents** zusätzlich zum Datentyp verwendet. Weitere Informationen finden Sie unter [Ändern der Berichte und Erstellen von benutzerdefinierten Abfragen](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries).|
| AzureNetworkAnalytics_CL  | [Schema der Datenverkehrsanalyse](../network-watcher/traffic-analytics.md) [Datenverkehrsanalyse](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Herstellen einer Verbindung mit CEF](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [Herstellen einer Verbindung mit Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Herstellen einer Verbindung mit Windows-Sicherheitsereignissen](connect-windows-security-events.md)  | &#10003; | Informationen zu den Arbeitsmappen für unsichere Protokolle finden Sie unter [Einrichten einer Arbeitsmappe für unsichere Protokolle](./quickstart-get-visibility.md#use-built-in-workbooks).  |
| syslog | [Herstellen einer Verbindung mit Syslog](connect-syslog.md) | &#10003; | |
| Microsoft Web Application Firewall (WAF) – (AzureDiagnostics) |[Herstellen einer Verbindung mit Microsoft Web Application Firewall](./connect-azure-waf.md) | &#10003; | |
| SymantecICDx_CL | [Herstellen einer Verbindung mit Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [Herstellen einer Verbindung mit Threat Intelligence](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Monitor-Dienstzuordnung](../azure-monitor/insights/service-map.md)<br>[Onboarding von Azure Monitor-VM-Insights](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Aktivieren von Azure Monitor-VM-Insights](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Verwenden von Onboarding für einen einzelnen virtuellen Computer](../azure-monitor/insights/vminsights-enable-portal.md)<br>  [Verwenden von Onboarding per Richtlinie](../azure-monitor/insights/vminsights-enable-policy.md)| &#10007; | VM Insights-Arbeitsmappe  |
| DnsEvents | [Herstellen einer Verbindung mit DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [Herstellen einer Verbindung mit IIS-Protokollen](../azure-monitor/platform/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [Herstellen einer Verbindung mit Wire Data](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [Herstellen einer Verbindung mit der Windows-Firewall](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [Herstellen einer Verbindung mit Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Herstellen von Verbindungen mit Microsoft Defender for Identity](connect-azure-atp.md) (vormals Azure ATP) | &#10003; | |
| ASC SecurityAlert  | [Verbinden von Azure Defender-Benachrichtigungen](connect-azure-security-center.md) aus Azure Security Center  | &#10003; | |
| MCAS SecurityAlert  | [Herstellen einer Verbindung mit Microsoft Cloud App Security](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (Ereignis) | [Herstellen einer Verbindung mit Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Herstellen einer Verbindung mit Windows-Ereignissen](../azure-monitor/platform/data-sources-windows-events.md) <br> [Sysmon-Parser herunterladen](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | Die Sysmon-Sammlung wird auf virtuellen Computern standardmäßig nicht installiert. Weitere Informationen zum Installieren des Sysmon-Agents finden Sie unter [Sysmon](/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatisieren des VM-Bestands](../automation/change-tracking/overview.md)| &#10007; | |
| ConfigurationChange  | [Automatisieren der VM-Nachverfolgung](../automation/change-tracking/overview.md) | &#10007; | |
| F5 BIG-IP | [Herstellen einer Verbindung mit F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Herstellen einer Verbindung mit Barracuda](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>Nächste Schritte

- Für den Einstieg in Azure Sentinel benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/free/)registrieren.
- Hier erfahren Sie, wie Sie [Ihre Daten in Azure Sentinel integrieren](quickstart-onboard.md) und [Einblicke in Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).