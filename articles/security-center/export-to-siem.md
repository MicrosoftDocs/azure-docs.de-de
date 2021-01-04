---
title: Streamen von Warnungen aus Azure Security Center in SIEM-Systeme (Security Information & Event Management) und andere Überwachungslösungen
description: Erfahren Sie, wie Sie Sicherheitswarnungen in Azure Sentinel, Drittanbieter-SIEMs, SOAR- oder ITSM-Lösungen streamen.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 6ffb6ced6fc828733dd627943a3d4b54e8293ad2
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791901"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Streamen von Warnungen in eine SIEM-, SOAR- oder IT Service Management-Lösung

Azure Security Center kann Ihre Sicherheitswarnungen in die beliebtesten Lösungen für SIEM (Security Information & Event Management), SOAR (Security Orchestration Automated Response) und ITSM (IT Service Management) streamen.

Es gibt native Azure-Tools, mit denen sichergestellt wird, dass Sie die Warnungsdaten in allen heute gebräuchlichen Lösungen anzeigen können. Dazu zählen:

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **QRadar von IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Streamen von Warnungen in Azure Sentinel 

Security Center ist nativ in Azure Sentinel, der cloudnativen SIEM- und SOAR-Lösung von Azure, integriert. 

[Erfahren Sie mehr über Azure Sentinel](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Azure Sentinel-Connectors für Security Center

Azure Sentinel umfasst integrierte Connectors für Azure Security Center auf Abonnement- und Mandantenebene:

- [Streamen von Warnungen in Azure Sentinel auf Abonnementebene](../sentinel/connect-azure-security-center.md)
- [Verbinden aller Abonnements im Mandanten mit Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Konfigurieren der Erfassung aller Überwachungsprotokolle in Azure Sentinel 

Eine weitere Alternative zum Untersuchen von Security Center-Warnungen in Azure Sentinel ist das Streamen der Überwachungsprotokolle in Azure Sentinel:
    - [Herstellen einer Verbindung mit Windows-Sicherheitsereignissen](../sentinel/connect-windows-security-events.md)
    - [Sammeln von Daten aus Linux-basierten Quellen mithilfe von Syslog](../sentinel/connect-syslog.md)
    - [Verknüpfen von Daten aus dem Azure-Aktivitätsprotokoll](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure Sentinel wird auf Grundlage der Datenmenge abgerechnet, die zur Analyse in Azure Sentinel erfasst und im Log Analytics-Arbeitsbereich von Azure Monitor gespeichert wurde. Azure Sentinel bietet ein flexibles und vorhersagbares Preismodell. [Weitere Informationen finden Sie auf der Seite „Azure Sentinel – Preise“](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Streamen von Warnungen mit der Microsoft Graph-Sicherheits-API

Security Center bietet eine vorkonfigurierte Integration in die Microsoft Graph-Sicherheits-API. Es ist keine Konfiguration erforderlich, und es fallen keine zusätzlichen Kosten an. 

Mithilfe dieser API können Sie Warnungen von Ihrem **gesamten Mandanten** (und Daten von vielen anderen Microsoft-Sicherheitsprodukten) in Drittanbieter-SIEMs und andere beliebte Plattformen streamen:

- **Splunk Enterprise und Splunk Cloud** : [Verwenden des Microsoft Graph-Sicherheits-API-Add-Ons für Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI** : [Herstellen einer Verbindung mit der Microsoft Graph-Sicherheits-API in Power BI Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow** : [Installieren und Konfigurieren der Microsoft Graph-Sicherheits-API-Anwendung aus dem ServiceNow Store](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar** : [Geräteunterstützungsmodul von IBM für Azure Security Center über die Microsoft Graph-API](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks** , **Anomali** , **Lookout** , **InSpark** und mehr: [Microsoft Graph-Sicherheits-API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Erfahren Sie mehr über die Microsoft Graph-Sicherheits-API](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Streamen von Warnungen mit Azure Monitor 

Zum Streamen von Warnungen in **ArcSight** , **Splunk** , **SumoLogic** , Syslog-Server, **LogRhythm** , **Logz.io Cloud Observability Platform** und andere Überwachungslösungen stellen Sie eine Verbindung zwischen Security Center und Azure Monitor über Azure Event Hubs her:

1. Aktivieren Sie das [fortlaufende Exportieren](continuous-export.md), um Security Center-Warnungen auf Abonnementebene in einen dedizierten Azure Event Hub zu streamen. 
    > [!TIP]
    > Informationen zur Durchführung auf Verwaltungsgruppenebene mithilfe von Azure Policy finden Sie unter [Erstellen von Konfigurationen zur Automatisierung des fortlaufenden Exports](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies).

1. [Stellen Sie eine Verbindung zwischen dem Azure Event Hub und Ihrer bevorzugten Lösung mithilfe des integrierten Connectors von Azure Monitor her](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. Optional können Sie die unformatierten Protokolle in den Azure Event Hub streamen und eine Verbindung mit Ihrer bevorzugten Lösung herstellen. Weitere Informationen finden Sie unter [Verfügbare Überwachungsdaten](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Die Ereignisschemas der exportierten Datentypen finden Sie bei den [Event Hub-Ereignisschemas](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde erläutert, wie Sie sicherstellen, dass Ihre Azure Security Center-Warnungsdaten im SIEM-, SOAR- oder ITSM-Tool Ihrer Wahl verfügbar sind. Verwandte Informationen finden Sie hier:

- [Was ist Azure Sentinel?](../sentinel/overview.md)
- [Warnungsüberprüfung in Azure Security Center](security-center-alert-validation.md): Überprüfen, ob Warnungen ordnungsgemäß konfiguriert sind
- [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md)