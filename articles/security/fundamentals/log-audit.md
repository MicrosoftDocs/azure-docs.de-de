---
title: Azure-Sicherheitsprotokollierung und -Überwachung | Microsoft-Dokumentation
description: Informieren Sie sich über die in Azure verfügbaren Protokolle und die Einblicke in die Sicherheit, die Sie erhalten können.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: 79c15f2df82125f20dcfaf9992d9047b632c253d
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412798"
---
# <a name="azure-security-logging-and-auditing"></a>Azure-Sicherheitsprotokollierung und -Überwachung

Azure bietet eine breite Palette an konfigurierbaren Optionen für die Sicherheitsüberwachung und -Protokollierung, damit Sie Lücken in Ihren Sicherheitsrichtlinien und -mechanismen bestimmen können. Dieser Artikel erläutert das Generieren, Sammeln und Analysieren von Sicherheitsprotokollen aus Diensten, die in Azure gehostet werden.

> [!Note]
> Bestimmte Empfehlungen in diesem Artikel können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen sowie zu höheren Lizenz- oder Abonnementkosten führen.

## <a name="types-of-logs-in-azure"></a>Protokolltypen in Azure

Cloudanwendungen sind komplexe Systeme mit zahlreichen Variablen. Die Protokollierung von Daten kann Ihnen Erkenntnisse über Ihre Anwendungen bieten und Ihnen in folgenden Punkten helfen:

- Beheben aufgetretener Probleme oder verhindern potenzieller neuer Probleme
- Verbessern der Anwendungsleistung oder Wartbarkeit
- Automatisieren von Aktionen, die andernfalls manuellen Eingriff erfordern würden

Azure-Protokolle werden in der folgenden Typen kategorisiert:
* **Steuerungs-/Verwaltungsprotokolle** enthalten Informationen zu CREATE-, UPDATE- und DELETE-Vorgänge in Azure Resource Manager. Weitere Informationen finden Sie in [Azure-Aktivitätsprotokolle](../../azure-monitor/platform/platform-logs-overview.md).

* **Datenebenenprotokolle** enthalten Informationen über Ereignisse, die bei der Nutzung der Azure-Ressource aufgetreten sind. Beispiele für diesen Protokolltyp sind System-, Sicherheits- und Anwendungsprotokolle des Windows-Ereignisprotokolls auf einem virtuellen Computer (VM) sowie die über Azure Monitor konfigurierten [Diagnoseprotokolle](../../azure-monitor/platform/platform-logs-overview.md).

* **Verarbeitete Ereignisse** enthalten Informationen zu analysierten Ereignisse/Warnungen, die in Ihrem Auftrag verarbeitet wurden. Beispiele für diesen Typ sind [Azure Security Center-Warnungen](../../security-center/security-center-managing-and-responding-alerts.md), bei denen [Azure Security Center](../../security-center/security-center-introduction.md) Ihr Abonnement verarbeitet und analysiert hat und prägnante Sicherheitswarnungen ausgibt.

Die folgende Tabelle enthält die wichtigsten Protokolltypen, die in Azure verfügbar sind:

| Protokollkategorie | Protokolltyp | Verwendung | Integration |
| ------------ | -------- | ------ | ----------- |
|[Aktivitätsprotokolle](../../azure-monitor/platform/platform-logs-overview.md)|Ereignisse der Steuerungsebene für Azure Resource Manager-Ressourcen|  Bieten Einblicke in Vorgänge, die für Ressourcen Ihres Abonnements durchgeführt wurden.|    REST-API und [Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md)|
|[Azure-Ressourcenprotokolle](../../azure-monitor/platform/platform-logs-overview.md)|Häufige Daten zum Betrieb der Azure Resource Manager-Ressourcen im Abonnement|   Bieten einen Einblick in Vorgänge, die Ihre Ressource selbst ausgeführt hat.| Azure Monitor|
|[Azure Active Directory-Berichterstellung](../../active-directory/reports-monitoring/overview-reports.md)|Protokolle und Berichte | Enthält Informationen über Aktivitäten zur Benutzeranmeldung und Systemaktivitäten zur Benutzer- und Gruppenverwaltung.|[Graph-API](../../active-directory/develop/microsoft-graph-intro.md)|
|[Virtuelle Computer und Clouddienste](../../azure-monitor/learn/quick-collect-azurevm.md)|Windows-Ereignisprotokolldienst und Linux-Syslog|  Erfasst Systemdaten und Protokollierungsdaten auf den virtuellen Computern und überträgt die Daten in ein Speicherkonto Ihrer Wahl.|   Windows mit [WAD](../../azure-monitor/platform/diagnostics-extension-overview.md) (Microsoft Azure-Diagnosespeicher) und Linux in Azure Monitor|
|[Azure-Speicheranalyse](/rest/api/storageservices/fileservices/storage-analytics)|Speicherprotokollierung, stellt Metrikdaten für ein Speicherkonto bereit|Bietet einen Einblick in Nachverfolgungsanforderungen und ermöglicht die Analyse von Verwendungstrends sowie die Diagnose von Problemen mit dem Speicherkonto.| REST-API oder [Clientbibliothek](/dotnet/api/overview/azure/storage)|
|[NSG-Flussprotokolle (Netzwerksicherheitsgruppe)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON-Format, zeigt eingehende und ausgehende Datenflüsse auf Regelbasis|Zeigt Informationen zu ein- und ausgehendem IP-Datenverkehr über eine Netzwerksicherheitsgruppe an.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application Insight](../../azure-monitor/app/app-insights-overview.md)|Protokolle, Ausnahmen und benutzerdefinierte Diagnosen|  Bietet einen für Webentwickler konzipierten Dienst zur Verwaltung der Anwendungsleistung (Application Performance Management, APM) auf mehreren Plattformen.| REST-API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Prozessdaten/Sicherheitswarnungen](../../security-center/security-center-introduction.md)|   Azure Security Center-Warnungen, Azure Monitor-Protokollwarnungen|    Bietet Sicherheitsinformationen und Warnungen.|  REST-APIs, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Protokollintegration mit lokalen SIEM-Systemen
In dem Artikel [Integrieren von Sicherheitslösungen in Azure Security Center](../../security-center/security-center-partner-integration.md) erfahren Sie, wie Sie Azure Security Center-Warnungen sowie in Azure-Diagnoseprotokollen und -Überwachungsprotokollen erfasste Sicherheitsereignisse virtueller Computer mit Azure Monitor-Protokollen oder Ihrer SIEM-Lösung synchronisieren.

## <a name="next-steps"></a>Nächste Schritte

- [Überwachung und Protokollierung](management-monitoring-overview.md): Schützen von Daten durch Beibehalten der Sichtbarkeit und schnelles Reagieren auf zeitnahe Sicherheitswarnungen.

- [Sicherheitsprotokollierung und Sammeln von Überwachungsprotokollen in Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Erzwingen Sie diese Einstellungen, um sicherzustellen, dass Ihre Azure-Instanzen die richtigen Sicherheits- und Überwachungsprotokolle sammeln.

- [Konfigurieren von Überwachungseinstellungen für eine Websitesammlung](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Wenn Sie Administrator einer Websitesammlung sind, können Sie den Verlauf der Aktionen für einzelne Benutzer und den Verlauf von Aktionen, die während eines bestimmten Zeitraums ausgeführt wurden, abrufen.

- [Durchsuchen des Überwachungsprotokolls in Microsoft 365 Security Center:](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance) Durchsuchen Sie mit Microsoft 365 Security Center das vereinheitlichte Überwachungsprotokoll, um Benutzer- und Administratoraktivitäten in Ihrer Organisation anzuzeigen.