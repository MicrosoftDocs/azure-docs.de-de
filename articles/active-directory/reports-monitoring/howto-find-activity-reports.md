---
title: Suchen von Benutzeraktivitätsberichten von Azure Active Directory im Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wo sich die Azure Active Directory-Benutzeraktivitätsberichte im Azure-Portal befinden.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d47072713c57576abe780134792c3a5cbc27127c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438286"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Speicherorte von Aktivitätsberichten im Azure-Portal

In diesem Artikel wird beschrieben, wie Sie Benutzeraktivitätsberichte von Azure Active Directory (Azure AD) im Azure-Portal finden.

## <a name="audit-logs-report"></a>Bericht „Überwachungsprotokolle“

Im Bericht „Überwachungsprotokolle“ sind mehrere Berichte zu Anwendungsaktivitäten in einer gemeinsamen Ansicht für die kontextbasierte Berichterstellung kombiniert. Greifen Sie wie folgt auf den Bericht „Überwachungsprotokolle“ zu:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Wählen Sie oben rechts Ihr Verzeichnis aus, und wählen Sie dann im linken Navigationsbereich das Blatt **Azure Active Directory**.
3. Wählen Sie auf dem Blatt „Azure Active Directory“ im Abschnitt **Aktivität** die Option **Überwachungsprotokolle**. 

    ![Überwachungsprotokolle](./media/howto-find-activity-reports/482.png "Überwachungsprotokolle")

Im Bericht „Überwachungsprotokolle“ sind die folgenden Berichte zusammengefasst:

* Überwachungsbericht
* Kennwortzurücksetzungsaktivität
* Aktivität "Registrierung für Zurücksetzen des Kennworts"
* Self-Service-Gruppenaktivität
* Namensänderungen für Office 365-Gruppen
* Kontobereitstellungsaktivität
* Status des Kennwortrollovers
* Kontobereitstellungsfehler

### <a name="filtering-on-audit-logs"></a>Filtern nach Überwachungsprotokollen

Sie können die erweiterte Filterung im Überwachungsbericht verwenden, um auf eine bestimmte Kategorie von Überwachungsdaten zuzugreifen, indem Sie diese im Filter **Kategorie** angeben. Um alle Aktivitäten im Zusammenhang mit Benutzern anzuzeigen, wählen Sie z.B. die Kategorie **UserManagement**. 

Zu den Kategorien zählen:

- Alle
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autorisierung
- Kontakt
- Gerät
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Andere
- Richtlinie
- ResourceManagement
- RoleManagement
- UserManagement

Mithilfe des Dropdownfilters **Dienst** können Sie auch nach einem bestimmten Dienst filtern. Wenn Sie beispielsweise alle Überwachungsereignisse anzeigen möchten, die sich auf die Self-Service-Kennwortverwaltung beziehen, wählen Sie den Filter **Self-Service-Kennwortverwaltung** aus.

Zu diesen Diensten gehören:

- Alle
- Zugriffsüberprüfungen
- Kontobereitstellung 
- Anwendungs-SSO
- Authentifizierungsmethoden
- B2C
- Bedingter Zugriff
- Kernverzeichnis
- Berechtigungsverwaltung
- Schutz der Identität (Identity Protection)
- Invited Users (Eingeladene Benutzer)
- PIM
- Self-Service-Gruppenverwaltung
- Self-Service-Kennwortverwaltung
- Terms of Use (Nutzungsbedingungen)

## <a name="sign-ins-report"></a>Bericht zu Anmeldeaktivitäten 

Die Ansicht **Anmeldungen** enthält alle Benutzeranmeldungen und den Bericht **Anwendungsnutzung**. Informationen zur Anwendungsnutzung finden Sie zudem in der Übersicht über die **Unternehmensanwendungen** im Abschnitt **Verwalten**.

Greifen Sie wie folgt auf den Bericht „Anmeldungen“ zu:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Wählen Sie oben rechts Ihr Verzeichnis aus, und wählen Sie dann im linken Navigationsbereich das Blatt **Azure Active Directory**.
3. Wählen Sie auf dem Blatt „Azure Active Directory“ im Abschnitt **Aktivität** die Option **Anmeldungen**. 

    ![Ansicht „Anmeldungen“](./media/howto-find-activity-reports/483.png "Ansicht „Anmeldungen“")


### <a name="filtering-on-application-name"></a>Filtern nach Anwendungsname

Sie können den Bericht „Anmeldungen“ verwenden, um Details zur Anwendungsnutzung anzuzeigen, indem Sie nach dem Benutzernamen oder Anwendungsnamen filtern.

![Seite mit Filter nach Anmeldeereignissen](./media/howto-find-activity-reports/07.png "Seite mit Filter nach Anmeldeereignissen")

## <a name="security-reports"></a>Sicherheitsberichte

### <a name="anomalous-activity-reports"></a>Berichte zu anomalen Aktivitäten

Mit Berichten zu anomalen Aktivitäten werden Informationen zu sicherheitsbezogenen Risikoereignissen bereitgestellt, die von Azure AD erkannt und für die Berichte erstellt werden können.

Die folgende Tabelle listet die Sicherheitsberichte zur anormalen Aktivität von Azure AD und die entsprechenden Risikoereignistypen im Azure-Portal auf. Weitere Informationen finden Sie unter [Azure Active Directory-Risikoereignisse](concept-risk-events.md).  


| Bericht zur anormalen Aktivität von Azure AD |  Typ des Identity Protection-Risikoereignisses|
| :--- | :--- |
| Benutzer mit kompromittierten Anmeldeinformationen | Kompromittierte Anmeldeinformationen |
| Irreguläre Anmeldeaktivitäten | Unmöglicher Ortswechsel zu atypischen Orten |
| Anmeldungen von möglicherweise infizierten Geräten | Anmeldungen von infizierten Geräten|
| Anmeldungen von unbekannten Quellen | Anmeldungen von anonymen IP-Adressen |
| Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten | Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten |
| - | Anmeldungen von unbekannten Standorten |

Folgende Sicherheitsberichte zur anormalen Aktivität von Azure AD gehören nicht zu Risikoereignissen im Azure-Portal:

* Anmeldungen nach mehreren Fehlern
* Anmeldungen aus mehreren geografischen Regionen


### <a name="detected-risk-events"></a>Erkannte Risikoereignisse

Sie können im [Azure-Portal](https://portal.azure.com) auf dem Blatt **Azure Active Directory** im Abschnitt **Sicherheit** auf die Berichte zu erkannten Risikoereignissen zugreifen. Die erkannten Risikoereignisse werden in den folgenden Berichten nachverfolgt:   

- [Gefährdete Benutzer](concept-user-at-risk.md)
- [Riskante Anmeldungen](concept-risky-sign-ins.md)

    ![Sicherheitsberichte](./media/howto-find-activity-reports/04.png "Sicherheitsberichte")

## <a name="troubleshoot-issues-with-activity-reports"></a>Behandeln von Problemen mit Aktivitätsberichten

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Fehlende Daten in den heruntergeladenen Aktivitätsprotokollen

#### <a name="symptoms"></a>Symptome 

Ich habe die Aktivitätsprotokolle (Überwachung oder Anmeldungen) heruntergeladen, und für den ausgewählten Zeitraum werden nicht alle Datensätze angezeigt. Warum? 

 ![Berichterstellung](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Ursache

Beim Herunterladen von Aktivitätsprotokollen im Azure-Portal gilt eine Obergrenze von 250.000 Datensätzen, die nach Aktualität sortiert werden. 

#### <a name="resolution"></a>Lösung

Mithilfe von [Azure AD-Berichterstellungs-APIs](concept-reporting-api.md) können jederzeit bis zu einer Millionen Datensätze abgerufen werden.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Fehlende Überwachungsdaten für die zuletzt durchgeführten Aktionen im Azure-Portal

#### <a name="symptoms"></a>Symptome

Ich habe einige Aktionen im Azure-Portal ausgeführt und erwartet, die Überwachungsprotokolle für diese Aktionen auf dem Blatt `Activity logs > Audit Logs` zu finden. Ich kann sie jedoch nicht finden.

 ![Berichterstellung](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Ursache

Aktionen werden nicht sofort in den Aktivitätsprotokollen angezeigt. In der folgenden Tabelle sind unsere Latenzzahlen für Aktivitätsprotokolle aufgezählt. 

| Bericht | &nbsp; | Wartezeit (P95) | Wartezeit (P99) |
|--------|--------|---------------|---------------|
| Verzeichnisüberwachung | &nbsp; | 2 Min. | 5 Min. |
| Anmeldeaktivität | &nbsp; | 2 Min. | 5 Min. | 

#### <a name="resolution"></a>Lösung

Warten Sie 15 Minuten bis zwei Stunden, und überprüfen Sie dann, ob die Aktionen im Protokoll angezeigt werden. Wenn die Protokolle auch nach zwei Stunden nicht angezeigt werden, [erstellen Sie ein Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), und wir nehmen uns der Sache an.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Fehlende Protokolle für die zuletzt durchgeführten Benutzeranmeldungen im Aktivitätsprotokoll zu den Azure AD-Anmeldungen

#### <a name="symptoms"></a>Symptome

Ich habe mich kürzlich beim Azure-Portal angemeldet und erwartet, die Anmeldeprotokolle für diese Aktionen auf dem Blatt `Activity logs > Sign-ins` zu finden. Ich kann sie jedoch nicht finden.

 ![Berichterstellung](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Ursache

Aktionen werden nicht sofort in den Aktivitätsprotokollen angezeigt. In der folgenden Tabelle sind unsere Latenzzahlen für Aktivitätsprotokolle aufgezählt. 

| Bericht | &nbsp; | Wartezeit (P95) | Wartezeit (P99) |
|--------|--------|---------------|---------------|
| Verzeichnisüberwachung | &nbsp; | 2 Min. | 5 Min. |
| Anmeldeaktivität | &nbsp; | 2 Min. | 5 Min. | 

#### <a name="resolution"></a>Lösung

Warten Sie 15 Minuten bis zwei Stunden, und überprüfen Sie dann, ob die Aktionen im Protokoll angezeigt werden. Wenn die Protokolle auch nach zwei Stunden nicht angezeigt werden, [erstellen Sie ein Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), und wir nehmen uns der Sache an.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Ich kann für die Berichtsdaten nicht mehr als 30 Tage im Azure-Portal anzeigen.

#### <a name="symptoms"></a>Symptome

Ich kann nur die Anmelde- und Überwachungsdaten der letzten 30 Tage aus dem Azure-Portal anzeigen. Warum? 

 ![Berichterstellung](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Ursache

Je nach Lizenz gelten für die Speicherung von Aktivitätsberichten durch Azure Active Directory-Aktionen die folgenden Dauern:

| Bericht           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Verzeichnisprüfbericht  | &nbsp; |   7 Tage     | 30 Tage             | 30 Tage             |
| Benutzeranmeldeaktivität | &nbsp; | Nicht verfügbar. Auf dem jeweiligen Benutzerprofilblatt können Sie auf Ihre eigenen Anmeldungen der letzten 7 Tage zugreifen. | 30 Tage | 30 Tage             |

Weitere Informationen finden Sie unter [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Lösung

Sie haben zwei Möglichkeiten, um die Daten länger als 30 Tage beibehalten. Mithilfe der [Azure AD-Berichterstellungs-APIs](concept-reporting-api.md) können Sie die Daten programmgesteuert abrufen und in einer Datenbank speichern. Alternativ können Sie Überwachungsprotokolle in das SIEM-System eines Drittanbieters wie z. B. Splunk oder SumoLogic integrieren.

## <a name="next-steps"></a>Nächste Schritte

* [Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal](concept-audit-logs.md)
* [Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal](concept-sign-ins.md)
* [Azure Active Directory-Risikoereignisse](concept-risk-events.md)
