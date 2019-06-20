---
title: Auslaufen von Security Center-Funktionen (Juli 2019) | Microsoft-Dokumentation
description: In diesem Artikel werden die Features in Security Center erläutert, die am 31. Juli 2019 eingestellt werden.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 069345f9c2d0fff0b580365153d8be13bb4ba204
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952138"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Auslaufen von Security Center-Funktionen (Juli 2019)

Wir haben in den letzten sechs Monaten verschiedene [Verbesserungen](https://azure.microsoft.com/updates/?product=security-center) am Azure Security Center vorgenommen.
Mit Verbesserung der Funktionen werden am 31. Juli 2019 eine Reihe redundanter Features sowie zugehörige APIs aus dem Security Center entfernt.  

Die meisten dieser eingestellten Features können mit neuer Funktionalität in Azure Security Center oder Azure Log Analytics ersetzt werden. Andere Features können mithilfe von [Azure Sentinel (Vorschau)](https://azure.microsoft.com/services/azure-sentinel/) implementiert werden.

Zu den eingestellten Security Center-Features zählen:

- [Ereignisdashboard](#menu_events)
- [Menüeintrag „Search“](#menu_search)
- [Link „Identität und Zugriff (klassisch) anzeigen“ in Identität und Zugriff (Vorschau)](#menu_classicidentity)
- [Schaltfläche „Sicherheit Ereignisse Karte“ in Sicherheitshinweise zuordnen (Vorschau)](#menu_securityeventsmap)
- [Benutzerdefinierte Warnungsregeln (Vorschau)](#menu_customalerts)
- [Schaltfläche „Untersuchen“ in Bedrohungsschutz-Sicherheitswarnungen](#menu_investigate)
- [Teile der Sicherheitslösungen](#menu_solutions)
- [Bearbeiten von Sicherheitskonfigurationen für Sicherheitsrichtlinien](#menu_securityconfigurations)
- [Dashboard Sicherheit und Überwachung (ursprünglich im OMS-Portal) für Log Analytics-Arbeitsbereiche](#menu_securityomsdashboard)

In diesem Artikel erhalten Sie ausführliche Informationen zu jedem eingestellten Feature sowie zu den Schritten, mit denen Sie Ersatzfeatures implementieren können.

## Ereignisdashboard<a name="menu_events"></a>

Security Center nutzt Microsoft Monitoring Agent für die Erfassung verschiedener sicherheitsbezogener Konfigurationen und Ereignisse auf Ihren Computern. Diese Ereignisse werden in Ihren Arbeitsbereichen gespeichert. Mit den [Ereignisdashboards](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) können Sie diese Daten anzeigen, und Sie bieten Ihnen einen Einstiegspunkt in Log Analytics.

Wir stellen das Ereignisdashboard ein, das angezeigt wird, wenn Sie einen Arbeitsbereich auswählen:

![Ereignisdashboard][2]

### <a name="events-dashboard---the-new-experience"></a>Ereignisdashboard – die neue Erfahrung

Wir haben Ihnen empfohlen, die nativen Funktionen von Azure Log Analytics zu verwenden, um relevante Ereignisse in Ihren Arbeitsbereichen anzuzeigen.

Wenn Sie benutzerdefinierte relevante Ereignisse in Security Center erstellt haben, werden Sie darauf zugreifen können. Wechseln Sie in Log Analytics zu **Arbeitsbereich auswählen** > **Gespeicherte Suchen**. Ihre Daten werden nicht verloren gehen oder geändert. Auf demselben Bildschirm in Log Analytics sind auch native relevante Ereignisse verfügbar.

![Gespeicherte Suchvorgänge – Arbeitsbereich][3]

## Menüeintrag „Search“<a name="menu_search"></a>

Azure Security Center verwendet derzeit die Azure Monitor-Protokollsuche, um Ihre Sicherheitsdaten abzurufen und zu analysieren. Dieser Bildschirm fungiert als Fenster zur Suchseite von Log Analytics und ermöglicht Benutzern, Suchabfragen zu ihrem ausgewählten Arbeitsbereich auszuführen. Weitere Informationen finden Sie unter [Azure Security Center-Suche](https://docs.microsoft.com/azure/security-center/security-center-search). Wir stellen dieses Suchfenster ein:

![Seite „Search“][4]

### <a name="search-menu-entry---the-new-experience"></a>Suchmenüeintrag – die neue Erfahrung

Wir empfehlen Ihnen, die nativen Funktionen von Azure Log Analytics zum Ausführen von Suchabfragen für Ihre Arbeitsbereiche zu nutzen. Wechseln Sie zu Azure Log Analytics, und wählen Sie **Protokolle** aus.

![Seite „Protokolle“ von Log Analytics][5]

## Identität und Zugriff (klassisch) (Vorschau)<a name="menu_classicidentity"></a>

In der klassischen „Identität und Zugriff“-Benutzeroberfläche in Security Center wird derzeit ein Dashboard mit Informationen zu Identität und Zugriff in Log Analytics angezeigt. So zeigen Sie dieses Dashboard an:

1. Wählen Sie **Klassische Identität & Zugriff anzeigen** aus.

   ![Identitätsseite][6]

1. Zeigen Sie das **Dashboard für Identität und Zugriff** an.

    ![Identitätsseite – Arbeitsbereichsauswahl][7]

1. Wählen Sie einen Arbeitsbereich zum Öffnen des Dashboards für **Identität und Zugriff** in Log Analytics zum Anzeigen von Identitäts- und Zugriffsinformationen in Ihrem Arbeitsbereich aus.

   ![Identitätsseite – Dashboard][8]

Wir stellen alle drei in den vorherigen Schritten gezeigten Bildschirme ein. Ihre Daten sind weiterhin in der Log Analytics-Sicherheitslösung verfügbar und werden weder geändert noch entfernt.

### <a name="classic-identity--access-preview---the-new-experience"></a>Identität und Zugriff (klassisch) in der Vorschau – die neue Erfahrung

Das Log Analytics-Dashboard hat Einblicke zu einem einzelnen Arbeitsbereich angezeigt. Allerdings bieten die nativen Funktionen von Security Center Einblick in alle Abonnements und Arbeitsbereiche, die ihnen zugeordnet sind. Sie können auf eine benutzerfreundliche Ansicht zugreifen, die Ihnen ermöglicht, sich auf das Wesentliche zu konzentrieren, mit Empfehlungen, deren Rangfolge sich nach ihrer Sicherheitsbewertung richtet.

Alle Features des Dashboards **Identität und Zugriff** in Log Analytics können durch Auswählen von **Identität und Zugriff (Vorschau)** in Security Center aufgerufen werden.

![Identitätsseite – Einstellung der klassischen Benutzeroberfläche][9]

## Karte von Sicherheitsereignissen<a name="menu_securityeventsmap"></a>

Security Center bietet Ihnen eine [Karte mit Sicherheitswarnungen und Informationen zu Bedrohungen](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) zur Identifizierung der Sicherheitsrisiken. Wenn Sie auf die Schaltfläche **Klicken Sie auf Sicherheit Ereignisse Karte** klicken, wird ein Dashboard geöffnet, in dem Sie Rohdaten zu Sicherheitsereignissen für den ausgewählten Arbeitsbereich einsehen können.

Wir entfernen die Schaltfläche **Klicken Sie auf Sicherheit Ereignisse Karte** und das arbeitsbereichsspezifische Dashboard.

![Karte mit Sicherheitswarnungen – Schaltfläche][10]

Wenn Sie die Schaltfläche **Klicken Sie auf Sicherheit Ereignisse Karte** auswählen, öffnen Sie das Dashboard „Informationen zu Bedrohungen“. Wir stellen das Dashboard „Informationen zu Bedrohungen“ ein.  

![Dashboard „Informationen zu Bedrohungen“][11]

Wenn Sie einen Arbeitsbereich auswählen, um das zugehörige Dashboard „Informationen zu Bedrohungen“ aufzurufen, öffnen Sie den Bildschirm mit der Karte für Sicherheitswarnungen (Vorschau) in Log Analytics. Wir stellen diesen Bildschirm ein.

![Karte mit Sicherheitswarnungen in Log Analytics][12]

Ihre vorhandenen Daten sind weiterhin in der Log Analytics-Sicherheitslösung verfügbar und werden weder geändert noch entfernt.

### <a name="security-events-map---the-new-experience"></a>Karte mit Sicherheitsereignissen – die neue Erfahrung

Wir empfehlen Ihnen, die in Security Center integrierte Karte mit Sicherheitswarnungen zu verwenden: **Karte mit Sicherheitswarnungen (Vorschau)** . Diese Funktion bietet eine optimierte Benutzeroberfläche und funktioniert mit allen Abonnements und zugehörigen Arbeitsbereichen. Sie erhalten eine allgemeine Übersicht Ihrer Umgebung, die nicht auf einem einzelnen Arbeitsbereich beschränkt ist.

## Benutzerdefinierte Warnungsregeln (Vorschau)<a name="menu_customalerts"></a>

Wir stellen [die Funktion für benutzerdefinierte Warnungsregeln](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) am 30. Juni 2019 ein, da die zugrunde liegende Infrastruktur eingestellt wird. Bis dahin können Sie vorhandene benutzerdefinierte Warnungsregeln bearbeiten, jedoch keine neuen hinzufügen. Sie sollten [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) aktivieren, um vorhandene Warnungen automatisch zu migrieren und neue Warnungen zu erstellen. Alternativ können Sie Ihre Warnungen mit Azure Monitor-Protokollwarnungen erstellen.

Wenn Sie Ihre vorhandenen Warnungen beibehalten möchten, migrieren Sie sie nach Azure Sentinel:

1. Öffnen Sie Azure Sentinel, und wählen Sie den Arbeitsbereich aus, in dem Ihre benutzerdefinierten Warnungen gespeichert sind.
1. Wählen Sie **Analytics** im Menü aus, um Ihre Warnungen automatisch zu migrieren.

![Benutzerdefinierte Warnungen][13]

Wenn Sie nicht am Übergang zu Azure Sentinel interessiert sind, sollten Sie Ihre Warnungen mit Azure Monitor-Protokollwarnungen erstellen. Anleitungen finden Sie unter  [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) und [Protokollwarnungen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Weitere Informationen zum Einstellen benutzerdefinierter Warnungen finden Sie unter [Benutzerdefinierte Warnungsregeln in Azure Security Center (Vorschauversion)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Sicherheitswarnungen – Untersuchung<a name="menu_investigate"></a>

[Das Untersuchungsfeature](https://docs.microsoft.com/azure/security-center/security-center-investigation) in Security Center unterstützt Sie beim Selektieren eines potenziellen Sicherheitsvorfalls. Mit diesem Feature können Sie den Umfang eines Vorfalls erkennen und die Hauptursache identifizieren. Wir entfernen dieses Feature aus Security Center, da es durch eine verbesserte Funktion in [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) ersetzt wurde.

![Sicherheitsvorfall][14]

Wenn Sie die **Untersuchen**-Schaltfläche in einem **Sicherheitsvorfall**-Bildschirm auswählen, öffnen Sie das Untersuchungsdashboard (Vorschau) in Log Analytics. Wir stellen das Untersuchungsdashboard ein.  

Ihre vorhandenen Daten sind weiterhin in der Log Analytics-Sicherheitslösung verfügbar und werden weder geändert noch entfernt.

![Untersuchungsdashboard in Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Untersuchung – die neue Erfahrung

Sie sollten zu [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) wechseln, damit Ihnen umfassende Untersuchungsfunktionen zur Verfügung stehen. Azure Sentinel bietet leistungsstarke Such- und Abfragetools zum Aufspüren von Sicherheitsbedrohungen in den Datenquellen Ihrer Organisation.  

## Teile der Sicherheitslösungen<a name="menu_solutions"></a>

Security Center kann [integrierte Sicherheitslösungen in Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) aktivieren. Wir stellen die folgenden Partnerlösungen aus Security Center ein. Diese Lösungen sind in [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) zusammen mit einer Reihe zusätzlicher Datenquellen aktiviert.

- [Herstellen einer Verbindung mit Datenquellen](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integration von Sicherheitslösungen, die das Common Event Format (CEF) unterstützen](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Nach der Einstellung können Sie keinen der in der obigen Liste erwähnten Lösungstypen hinzufügen oder ändern, weder über die Benutzeroberfläche noch die API.

Wenn Sie über vorhandene verbundene Lösungen verfügen, empfehlen wir Ihnen, Sie in Azure Sentinel zu verschieben.

![Security Center-Lösungen][16]

## Bearbeiten von Sicherheitskonfigurationen für Sicherheitsrichtlinien<a name="menu_securityconfigurations"></a>

Azure Security Center überwacht Sicherheitskonfigurationen durch Anwenden von [über 150 empfohlenen Regeln](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). für die Härtung des Betriebssystems. Diese Regeln beziehen sich u.a. auf Firewalls, Überwachung, Kennwortrichtlinien. Wenn auf einem Computer eine angreifbare Konfiguration festgestellt wird, generiert Security Center eine Sicherheitsempfehlung. Im Bildschirm [Sicherheitskonfigurationen bearbeiten](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) können Benutzer die standardmäßige Sicherheitskonfiguration des Betriebssystems in Security Center anpassen.

Wir stellen diese Previewfunktion ein. Wenn Sie nach dem Einstellungsdatum Ihre Sicherheitskonfigurationen auf ihre Standardwerte zurücksetzen möchten, können Sie dies über API oder PowerShell nach diesen [Anweisungen](https://aka.ms/ascresetsecurityconfigurations) tun.

![Sicherheitskonfigurationen bearbeiten][17]

### <a name="edit-security-configurations---the-new-experience"></a>Bearbeiten von Sicherheitskonfigurationen – die neue Erfahrung

Wir beabsichtigen, dass Security Center den [Guest Configuration-Agent](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) unterstützt. Aus diesem Update resultiert ein viel umfangreicherer Featuresatz, einschließlich der Unterstützung weiterer Betriebssysteme und Integration von Azure-Gastsystemrichtlinien für Gastkonfigurationen. Nachdem diese Änderungen aktiviert sind, haben Sie auch die Möglichkeit, Konfigurationen nach Maß zu steuern und automatisch auf neue Ressourcen anzuwenden.

## Dashboard „Sicherheit und Überwachung“ für Log Analytics-Arbeitsbereiche<a name="menu_securityomsdashboard"></a>

Das Dashboard „Sicherheit und Überwachung“ wurde ursprünglich im OMS-Portal verwendet. In Log Analytics bietet das Dashboard einen arbeitsbereichsspezifischen Überblick über relevante Sicherheitsereignisse und -risiken, eine Bedrohungsanalyse-Karte sowie eine Identitäts- und Zugriffsbewertung von Sicherheitsereignissen, die im Arbeitsbereich gespeichert sind. Wir werden das Dashboard entfernen. Wie bereits in der Benutzeroberfläche des Dashboards empfohlen, sollten Sie zum Azure Security Center wechseln.

![Sicherheitsdashboard von Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Dashboard „Sicherheit und Überwachung“ – die neue Erfahrung

Sie sollten zu Azure Security Center wechseln. Dort erhalten Sie dieselbe, mehrere Abonnements und die zugehörigen Arbeitsbereiche übergreifende Sicherheitsübersicht und außerdem einen umfangreicheren Funktionssatz.

Die ursprünglichen Log Analytics-Abfragen zum Ausfüllen des Dashboards „Sicherheit und Überwachung“ finden Sie im [GitHub-Repository](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) für Security Center.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- Erfahren Sie mehr über [Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
