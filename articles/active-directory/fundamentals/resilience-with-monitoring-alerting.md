---
title: Resilienz durch Überwachung und Analyse mit Azure AD B2C | Microsoft-Dokumentation
description: Resilienz durch Überwachung und Analyse mit Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 766fd80ae5f7450c8e45d10afa4612a788a8d5fc
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602155"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Resilienz durch Überwachung und Analyse

Durch die Überwachung werden Verfügbarkeit und Leistung Ihrer Anwendungen und Dienste maximiert. Sie bietet eine umfassende Lösung für das Sammeln, Analysieren und Behandeln von Telemetriedaten aus Ihrer Infrastruktur und Ihren Anwendungen. Mithilfe von Warnungen werden Sie proaktiv benachrichtigt, wenn Probleme mit Ihrem Dienst oder Ihren Anwendungen erkannt werden. Mit Warnungen können Sie Probleme identifizieren und beheben, bevor die Endbenutzer Ihres Diensts sie bemerken. [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) hilft Ihnen beim Analysieren und Durchsuchen der Überwachungs- und Anmeldeprotokolle und beim Erstellen benutzerdefinierter Ansichten.

## <a name="monitor-and-get-notified-through-alerts"></a>Überwachung und Benachrichtigung durch Warnungen

Die Überwachung Ihres Systems und der Infrastruktur ist überaus wichtig, um die allgemeine Integrität ihrer Dienste sicherzustellen. Zunächst werden Geschäftsmetriken definiert, z. B. Aufnahme neuer Benutzer, Authentifizierungsraten von Endbenutzern und Konvertierung. Konfigurieren Sie derartige Indikatoren für die Überwachung. Wenn Sie aufgrund einer Werbeaktion oder des Datenverkehrs an Feiertagen Planungen für ein bevorstehendes höheres Verkehrsaufkommen erstellen, überarbeiten Sie speziell für dieses Ereignis Ihre Schätzungen und den entsprechenden Benchmarkwert für die Geschäftsmetriken. Kehren Sie nach dem Ereignis zum vorherigen Benchmarkwert zurück.

In vergleichbarer Weise ist für das Erkennen von Fehlern oder Leistungsunterbrechungen das Einrichten einer guten Baseline und das Definieren von Warnungen eine unverzichtbare Vorgehensweise, um schnell auf anstehende Probleme reagieren zu können.

![Abbildung der Überwachungs- und Analysekomponenten](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Implementieren von Überwachung und Warnungen

- **Überwachung:** Verwenden Sie [Azure Monitor](https://docs.microsoft.com/azure/active-directory-b2c/azure-monitor), um die Integrität anhand wichtiger Servicelevelziele (Service Level Objectives, SLOs) fortlaufend zu überwachen und Benachrichtigungen zu erhalten, sobald eine kritische Änderung auftritt. Definieren Sie zunächst eine Azure AD B2C-Richtlinie oder eine Anwendung als kritische Komponente Ihrer Geschäftstätigkeit, deren Integrität überwacht werden muss, um das Servicelevelziel zu gewährleisten. Identifizieren Sie Schlüsselindikatoren, die Ihren Servicelevelzielen entsprechen.
Verfolgen Sie beispielsweise die folgenden Metriken, da ein plötzlicher Abfall bei einem dieser Werte zu einem Geschäftsverlust führen kann.

  - **Anforderungen insgesamt**: Die Gesamtanzahl der Anforderungen „n“, die an die Azure AD B2C-Richtlinie gesendet werden.

  - **Erfolgsrate (%)** : Erfolgreiche Anforderungen im Verhältnis zur Gesamtanzahl von Anforderungen.

  Greifen Sie auf die [Schlüsselindikatoren](https://docs.microsoft.com/azure/active-directory-b2c/view-audit-logs) in [Application Insights](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights) zu, wo richtlinienbasierte Azure AD B2C-Protokolle, [Überwachungsprotokolle](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights) und Anmeldeprotokolle gespeichert werden.  

   - **Visualisierungen**: Verwenden Sie in Log Analytics erstellte Dashboards, um die wichtigsten Indikatoren visuell zu überwachen.

   - **Aktueller Zeitraum**: Erstellen Sie temporale Diagramme zum Anzeigen von Änderungen an der Gesamtzahl der Anforderungen und der Erfolgsrate (%) im aktuellen Zeitraum (z. B. aktuelle Woche).

   - **Vorheriger Zeitraum**: Erstellen Sie zu Referenzzwecken temporale Diagramme mit Änderungen an der Gesamtzahl der Anforderungen und der Erfolgsrate (%) in einem vergangenen Zeitraum (z. B. letzte Woche).

- **Warnungen**: Mithilfe von Log Analytics können Sie [Warnungen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) definieren, die ausgelöst werden, wenn bei den Schlüsselindikatoren plötzliche Änderungen auftreten. Diese Änderungen wirken sich u. U. negativ auf die SLOs aus. Für Warnungen werden verschiedene Benachrichtigungsmethoden (z. B. E-Mail, SMS und Webhooks) verwendet. Definieren Sie zunächst ein Kriterium, das als Schwellenwert zum Auslösen der Warnung fungiert. Beispiel:
  - Warnung bei abruptem Abfall der Gesamtzahl der Anforderungen: Löst eine Warnung aus, wenn die Anzahl der gesamten Anforderungen abrupt abnimmt. Wenn beispielsweise die Gesamtzahl der Anforderungen im Vergleich zum vorherigen Zeitraum um 25 % sinkt, wird eine Warnung ausgelöst.  
  - Warnung bei signifikantem Abfall der Erfolgsrate (%): Löst eine Warnung aus, wenn die Erfolgsrate der ausgewählten Richtlinie deutlich sinkt.
  - Wenn Sie eine Warnung erhalten, beheben Sie das Problem mithilfe von [Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views), [Application Insights](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-with-application-insights#:~:text=Setup%20Application%20Insights%201%20Go%20to%20the%20Azure,left-menu%2C%20and%20click%20on%20it.%20More%20items...%20) und der [VS Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) für Azure AD B2C. Nachdem Sie das Problem behoben und eine aktualisierte Anwendung oder Richtlinie bereitgestellt haben, werden weiterhin die wichtigsten Indikatoren überwacht, bis sie sich wieder im normalen Bereich befinden.

- **Service-Warnungen**: Verwenden Sie die [Warnungen zum Servicelevel von Azure AD B2C](https://docs.microsoft.com/azure/service-health/service-health-overview), um bei Dienstproblemen, geplanten Wartungen sowie Integritäts- und Sicherheitsempfehlungen benachrichtigt zu werden.

- **Berichte**: [Mithilfe von Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) können Sie Berichte erstellen, die Ihnen helfen, sich mit Benutzererkenntnissen, technischen Herausforderungen und Wachstumschancen vertraut zu machen.
  - **Integritätsdashboard**: Erstellen Sie mit der Funktion [Azure Dashboard](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards) benutzerdefinierte Dashboards, die das Hinzufügen von Diagrammen mithilfe von Log Analytics-Abfragen unterstützen. Identifizieren Sie beispielsweise ein Muster erfolgreicher und fehlgeschlagener Anmeldungen, Fehlerursachen und Telemetriedaten zu Geräten, die für die Anforderung verwendet wurden.
  - **Abgebrochene Azure AD B2C-Journeys**: Verwenden Sie die [Arbeitsmappe](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys), um die Liste der abgebrochenen Azure AD B2C-Journeys zu verfolgen, bei denen der Benutzer den Anmelde- oder Registrierungsvorgang gestartet, aber nie beendet hat. Sie enthält Details zur Richtlinien-ID und eine Aufschlüsselung der Schritte, die der Benutzer vor dem Abbrechen des Vorgangs ausgeführt hat.
  - **Azure AD B2C-Überwachungsarbeitsmappen**: Verwenden Sie die [Überwachungsarbeitsmappen](https://github.com/azure-ad-b2c/siem), die Azure AD B2C-Dashboard, MFA-Vorgänge (Multi-Factor Authentication), den Bericht über bedingte Zugriffe und Suchprotokolle nach Korrelations-ID enthalten, um sich einen besseren Einblick in die Integrität Ihrer Azure AD B2C-Umgebung zu verschaffen.
  
## <a name="next-steps"></a>Nächste Schritte

- [Resilienzressourcen für Azure AD B2C-Entwickler](resilience-b2c.md)
  - [Resiliente Endbenutzerumgebung](resilient-end-user-experience.md)
  - [Resiliente Schnittstellen mit externen Prozessen](resilient-external-processes.md)
  - [Resilienz durch bewährte Entwicklermethoden](resilience-b2c-developer-best-practices.md)
- [Erzielen von Resilienz in der Authentifizierungsinfrastruktur](resilience-in-infrastructure.md)
- [Mehr Resilienz bei Authentifizierung und Autorisierung in Ihren Anwendungen](resilience-app-development-overview.md)
