---
title: Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Enthält eine Einführung in die Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5aa8f675e3fd36fbebfecf42db0f02b0f0f00115
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995980"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal 

Mit Azure AD-Berichten (Azure Active Directory) können Sie alle Informationen abrufen, die Sie zum Ermitteln des Zustands Ihrer Umgebung benötigen.



Diese Architektur für die Berichterstellung umfasst die folgenden Komponenten:

- **Aktivität** 
    - **Anmeldungen:** Der [Bericht „Anmeldungen“](concept-sign-ins.md) enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
    - **Überwachungsprotokolle:** Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.
    - **Bereitstellungsprotokolle** - [Bereitstellungsprotokolle](./concept-provisioning-logs.md) ermöglichen Kunden die Überwachung von Aktivitäten durch den Bereitstellungsdienst, z. B. die Erstellung einer Gruppe in ServiceNow oder eines aus Workday importierten Benutzers. 
- **Security** 
    - **Riskante Anmeldungen**: Eine [riskante Anmeldung](../identity-protection/overview-identity-protection.md) ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist. 
    - **Benutzer mit Risikomarkierung**: Ein [Benutzer mit Risikomarkierung](../identity-protection/overview-identity-protection.md) ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Dieser Artikel enthält eine Übersicht über den Überwachungsbericht.
 
## <a name="who-can-access-the-data"></a>Wer kann auf die Daten zugreifen?

* Benutzer mit den Rollen **Sicherheitsadministrator**, **Sicherheitsleseberechtigter**, **Berichtsleser**, **Globaler Leser** oder **Globaler Administrator**

## <a name="audit-logs"></a>Überwachungsprotokolle

Die Azure AD-Überwachungsprotokolle stellen Datensätze mit Systemaktivitäten für Compliancezwecke bereit. Wählen Sie zum Auswählen des Überwachungsberichts in **Azure Active Directory** im Abschnitt **Überwachung** die Option **Überwachungsprotokolle** aus. 



Ein Überwachungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Datum und Uhrzeit des Auftretens
- Dienst, der das Vorkommen protokolliert hat
- Kategorie und Name der Aktivität (*Was*) 
- Status der Aktivität (Erfolg oder Fehler)
- Ziel
- Initiator/Akteur einer Aktivität (Wer)

![Überwachungsprotokolle](./media/concept-audit-logs/listview.png "Überwachungsprotokolle")

Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

![Überwachungsspalten](./media/concept-audit-logs/columns.png "Überwachungsspalten")

Sie können dann weitere Felder anzeigen oder Felder entfernen, die bereits angezeigt werden.

![Felder entfernen](./media/concept-audit-logs/columnselect.png "Felder entfernen")

Wählen Sie in der Listenansicht ein Element aus, um ausführlichere Informationen zu erhalten.

![Element auswählen](./media/concept-audit-logs/details.png "Element auswählen")


## <a name="filtering-audit-logs"></a>Filtern von Überwachungsprotokollen

Sie können die Überwachungsdaten in den folgenden Feldern filtern:

- Dienst
- Category
- Aktivität
- Status
- Ziel
- Initiiert von (Akteur)
- Datumsbereich

![Filterobjekt](./media/concept-audit-logs/filter.png "Filter-Objekt")

Bei Verwendung des Filters **Dienst** können Sie in einer Dropdownliste die folgenden Dienste auswählen:

- All
- AAD Management UX
- Zugriffsüberprüfungen
- Kontobereitstellung
- Anwendungsproxy
- Authentifizierungsmethoden
- B2C
- Bedingter Zugriff
- Kernverzeichnis
- Berechtigungsverwaltung
- Hybridauthentifizierung
- Schutz der Identität (Identity Protection)
- Invited Users (Eingeladene Benutzer)
- MIM Service (MIM-Dienst)
- MyApps
- PIM
- Self-Service-Gruppenverwaltung
- Self-Service-Kennwortverwaltung
- Terms of Use (Nutzungsbedingungen)

Bei Verwendung des Filters **Kategorie** können Sie eine der folgenden Filteroptionen auswählen:

- All
- AdministrativeUnit
- ApplicationManagement
- Authentifizierung
- Authorization
- Kontakt
- Sicherungsmedium
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- Bezeichnung
- Andere
- PermissionGrantPolicy
- Richtlinie
- ResourceManagement
- RoleManagement
- UserManagement

Der Filter **Aktivität** basiert auf der getroffenen Auswahl für die Kategorie und den und Aktivitätsressourcentyp. Sie können entweder eine bestimmte Aktivität verwenden oder alle auswählen. 

Sie können die Liste aller Überwachungsaktivitäten mithilfe der Graph-API abrufen: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Mit dem Filter **Status** können Sie eine Filterung basierend auf dem Status eines Überprüfungsvorgangs durchführen. Folgende Statuswerte sind möglich:

- All
- Erfolg
- Fehler

Bei Verwendung des Filters **Ziel** können Sie anhand des Beginns des Namens oder des Benutzerprinzipalnamens (User Principal Name, UPN) ein bestimmtes Ziel suchen. Bei dem Zielnamen und dem UPN wird die Groß- und Kleinschreibung berücksichtigt. 

Bei Verwendung des Filters **Initiiert von** können Sie definieren, womit der Name eines Akteurs oder ein Benutzerprinzipalname (UPN) beginnt. Bei dem Namen und dem UPN wird die Groß- und Kleinschreibung berücksichtigt.

Mit dem Filter **Datumsbereich** können Sie einen Zeitrahmen für die zurückgegebenen Daten festlegen.  
Mögliche Werte:

- 7 Tage
- 24 Stunden
- Benutzerdefiniert

Beim Auswählen eines benutzerdefinierten Zeitraums können Sie eine Startzeit und eine Endzeit konfigurieren.

Sie können die gefilterten Daten (bis zu 250.000 Datensätze) auch herunterladen, indem Sie die Schaltfläche **Herunterladen** auswählen. Sie können die Protokolle im CSV- oder JSON-Format herunterladen. Die Anzahl von Datensätzen, die Sie herunterladen können, ist durch die [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md) eingeschränkt.

![Herunterladen von Daten](./media/concept-audit-logs/download.png "Herunterladen von Daten")

## <a name="audit-logs-shortcuts"></a>Verknüpfungen für Überwachungsprotokolle

Neben **Azure Active Directory** bietet das Azure-Portal noch zwei weitere Einstiegspunkte für Überwachungsdaten:

- Benutzer und Gruppen
- Unternehmensanwendungen

### <a name="users-and-groups-audit-logs"></a>Überwachungsprotokolle für Benutzer und Gruppen

Mit Überwachungsberichten, die auf Benutzern und Gruppen basieren, können Sie beispielsweise Antworten auf folgende Fragen erhalten:

- Welche Typen von Updates wurden auf Benutzer angewendet?

- Wie viele Benutzer wurden geändert?

- Wie viele Kennwörter wurden geändert?

- Welche Schritte hat ein Administrator in einem Verzeichnis ausgeführt?

- Welche Gruppen wurden hinzugefügt?

- Sind Gruppen mit Änderungen der Mitgliedschaft vorhanden?

- Haben sich die Besitzer der Gruppe geändert?

- Welche Lizenzen wurden einer Gruppe oder einem Benutzer zugewiesen?

Wenn Sie nur Überwachungsdaten überprüfen möchten, die sich auf Benutzer beziehen, können Sie eine gefilterte Ansicht unter **Überwachungsprotokolle** verwenden. Diese finden Sie auf der Registerkarte **Benutzer** im Abschnitt **Überwachung**. Bei diesem Einstiegspunkt ist die Kategorie **UserManagement** vorab ausgewählt.

![Benutzer](./media/concept-audit-logs/users.png "Benutzer")

Wenn Sie nur Überwachungsdaten überprüfen möchten, die sich auf Gruppen beziehen, können Sie eine gefilterte Ansicht unter **Überwachungsprotokolle** verwenden. Diese finden Sie auf der Registerkarte **Gruppen** im Abschnitt **Überwachung**. Bei diesem Einstiegspunkt ist die Kategorie **GroupManagement** vorab ausgewählt.

![Filtergruppen](./media/concept-audit-logs/groups.png "Filtergruppen")

### <a name="enterprise-applications-audit-logs"></a>Überwachungsprotokolle für Unternehmensanwendungen

Mit Überwachungsberichten, die auf Anwendungen basieren, können Sie beispielsweise Antworten auf folgende Fragen erhalten:

* Welche Anwendungen wurden hinzugefügt oder aktualisiert?
* Welche Anwendungen wurden entfernt?
* Hat sich ein Dienstprinzipal für eine Anwendung geändert?
* Haben sich die Namen von Anwendungen geändert?
* Wer hat die Zustimmung zu einer Anwendung erteilt?

Wenn Sie nur Überwachungsdaten überprüfen möchten, die sich auf Ihre Anwendungen beziehen, können Sie die gefilterte Ansicht unter **Überwachungsprotokolle** im Abschnitt **Aktivität** des Blatts **Unternehmensanwendungen** verwenden. Bei diesem Einstiegspunkt ist für **Aktivitätsressourcentyp** bereits vorab die Option **Unternehmensanwendungen** ausgewählt.

![Unternehmensanwendungen](./media/concept-audit-logs/enterpriseapplications.png "Unternehmensanwendungen")

## <a name="microsoft-365-activity-logs"></a>Microsoft 365-Aktivitätsprotokolle

Sie können Microsoft 365-Aktivitätsprotokolle im [Microsoft 365 Admin Center](/office365/admin/admin-overview/about-the-admin-center) anzeigen. Obwohl Microsoft 365- und Azure AD-Aktivitätsprotokolle einen Großteil der Verzeichnisressourcen gemeinsam nutzen, bietet nur das Microsoft 365 Admin Center eine vollständige Ansicht der Microsoft 365-Aktivitätsprotokolle. 

Mithilfe der [Office 365-Verwaltungs-APIs](/office/office-365-management-api/office-365-management-apis-overview) können Sie auch programmgesteuert auf die Microsoft 365-Aktivitätsprotokolle zugreifen.

## <a name="next-steps"></a>Nächste Schritte

- [Referenz zu Überwachungsaktivitäten von Azure AD](reference-audit-activities.md)
- [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md)
- [Latenzen bei Azure Active Directory-Berichten](reference-reports-latencies.md)