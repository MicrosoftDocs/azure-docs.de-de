---
title: 'Azure Sentinel: Referenz zum Datenquellenschema'
description: In diesem Artikel werden die von Azure Sentinel unterstützten Datenquellenschemas von Azure und Drittanbietern mit Links zu ihrer Referenzdokumentation aufgelistet.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 32ec711309190d4bb7c6c98a05b6a75cf88a8f5c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920489"
---
# <a name="data-source-schema-reference"></a>Referenz zum Datenquellenschema

In diesem Artikel werden die unterstützten Datenquellenschemas von Azure und Drittanbietern mit Links zu ihrer Referenzdokumentation aufgelistet.

## <a name="azure-data-sources"></a>Azure-Datenquellen

| type                             | Datenquelle             | Log Analytics-Tabellenname | Schemareferenz |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Anmeldeeigenschaften für Azure AD-Aktivitätsberichte](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure Monitor-Referenz für AuditLogs](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Azure Monitor-Referenz für AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Schemas der Office 365-Verwaltungsaktivitäts-API: <br>- [Gemeinsames Schema ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange-Verwaltungsschema ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange-Postfachschema](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [SharePoint-Basisschema](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [SharePoint-Dateivorgänge](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure-Schlüsseltresor         | AzureDiagnostics       | [Azure Monitor-Referenz für AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Host**                             | Linux                  | syslog                 | [Azure Monitor-Referenz für Syslog](/azure/azure-monitor/reference/tables/syslog) |
| **Network**                          | IIS-Protokolle               | W3CIISLog              | [Azure Monitor-Referenz für W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Network**                          | VMinsights             | VMConnection           | [Azure Monitor-Referenz für VMConnection](/azure/azure-monitor/reference/tables/vmconnection) |
| **Network**                          | Wire Data-Lösung     | WireData               | [Azure Monitor-Referenz für WireData](/azure/azure-monitor/reference/tables/wiredata) |
| **Network**                          | NSG-Flussprotokolle          | AzureNetworkAnalytics  | [Schema und Datenaggregation in Traffic Analytics](/azure/network-watcher/traffic-analytics-schema) |
| | | | |

## <a name="3rd-party-vendor-data-sources"></a>Drittanbieter-Datenquellen

In der folgenden Tabelle sind die unterstützten Drittanbieter und deren Dokumentation zur Syslog- oder CEF-Zuordnung (Common Event Format) für verschiedene unterstützte Protokolltypen aufgeführt. Dies umfasst auch CEF-Feldzuordnungen und Beispielprotokolle für jeden Kategorietyp.

| Typ |    Hersteller |    Produkt | Log Analytics-Tabellenname | Referenz zur CEF-Feldzuordnung  |
| ----- | ----- | ----- | ----- |----- |
| **Network** | Garmisch-Partenkirchen   | PAN-OS    | CommonSecurityLog |   [PAN-OS 9.0 Common Event Format Integration Guide](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (Integrationsleitfaden zum Common Event Format in PAN-OS 9.0; suchen Sie nach *CEF-style Log Formats*) |
| **Network** | Check Point  |ALLE   | CommonSecurityLog | [Beschreibung der Protokollfelder](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Network** | Fortigate   | ALLE   | CommonSecurityLog | [Struktur des Protokollschemas](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Network** | Barracuda | Web Application Firewall |  CommonSecurityLog   | [Konfigurieren von Syslog und anderen Protokollen](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Network** | Cisco | ASA | CommonSecurityLog | [Syslog-Meldungen bei der Cisco ASA-Serie](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Network** | Cisco | Firepower   | CommonSecurityLog | [Syslog-Meldungen von Cisco Firepower Threat Defense](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Network** | Cisco   | Umbrella  | Tabelle der benutzerdefinierten Protokolle  | [Protokollformate und Versionsverwaltung](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Network**   | Cisco | Meraki    | CommonSecurityLog |   [Syslog-Ereignistypen und Protokollbeispiele](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Network**   | Zscaler | Nano Streaming Service (NSS)|   CommonSecurityLog | [Formatieren von NSS-Feeds](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (nur Web-, Firewall-, DNS- und Tunnelprotokolle) |
| **Network**   |F5 | BigIP LTM|    CommonSecurityLog|  [Ereignismeldungen und Angriffstypen](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Network** | F5  | BigIP ASM|    CommonSecurityLog|  [Protokollieren von Anwendungssicherheitsereignissen](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Network** | Citrix  |Web-App-Firewall   | CommonSecurityLog|    [Unterstützung für die CEF-Protokollierung (Common Event Format) in der Anwendungsfirewall](https://support.citrix.com/article/CTX136146) <br>  [NetScaler 12.0: Referenz zu Syslog-Meldungen](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Host** |Symantec | Symantec Endpoint Protection Manager (SEPM) | CommonSecurityLog|[Einstellungen für die externe Protokollierung und Schweregrade für Protokollereignisse für Endpoint Protection Manager](https://support.symantec.com/us/en/article.tech171741.html)|
|**Host** |Trend Micro |Alle |CommonSecurityLog | [Syslog-Inhaltszuordnung – CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den unterstützten Azure Sentinel-Connectors wie CEF-, Syslog-, Agent-, direkten und benutzerdefinierten Connectors:

- [Herstellen einer Verbindung mit Datenquellen](connect-data-sources.md)

- [Azure Sentinel-Connectors für Syslog und CEF und andere Connectors von Drittanbietern](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)