---
title: Zuweisen von Administratorrollen in Azure Active Directory | Microsoft Docs
description: Eine Administratorrolle kann zum Hinzufügen von Benutzern, zum Zuweisen von Administratorrollen, zum Zurücksetzen von Benutzerkennwörtern sowie zum Verwalten von Benutzerlizenzen oder Domänen verwendet werden. Ein Benutzer, dem eine Administratorrolle zugewiesen wurde, verfügt für alle Dienste, die Ihre Organisation abonniert hat, über die gleichen Berechtigungen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/15/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 6e663fe275fc195cb611e1032adc147bf4e99b1d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932148"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Zuweisen von Administratorrollen in Azure Active Directory

Mithilfe von Azure Active Directory (Azure AD) können Sie verschiedene Administratoren bestimmen, um unterschiedliche Funktionen zu erfüllen. Administratoren haben Zugriff auf verschiedene Funktionen im Azure-Portal und können abhängig von ihrer Rolle unter anderem Benutzer erstellen oder bearbeiten, anderen administrative Rollen zuweisen, Benutzerkennwörter zurücksetzen, Benutzerlizenzen verwalten und Domänen verwalten. Ein Benutzer mit einer Administratorrolle besitzt dieselben Berechtigungen für alle Clouddienste, die Ihre Organisation abonniert hat, unabhängig davon, ob Sie die Rolle im Office 365-Portal, im Azure-Portal oder mithilfe des Azure AD-Moduls für Windows PowerShell zuweisen.

## <a name="details-about-the-global-administrator-role"></a>Details zur globalen Administratorrolle
Der globale Administrator hat Zugriff auf alle administrativen Funktionen. Standardmäßig wird der Person, die sich für ein Azure-Abonnement registriert, die globale Administratorrolle für das Verzeichnis zugewiesen. Nur globale Administratoren können weitere Administratorrollen zuweisen.

## <a name="assign-or-remove-administrator-roles"></a>Zuweisen oder Entfernen von Administratorrollen
Um zu erfahren, wie in Azure Active Directory einem Benutzer Administratorrollen zugewiesen werden, lesen Sie [Zuweisen von Administratorrollen zu einem Benutzer in Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Verfügbare Rollen
Die folgenden Administratorrollen sind verfügbar:

* **Rechnungsadministrator**: Tätigt Käufe, verwaltet Abonnements und Supporttickets und überwacht die Dienstintegrität.

* **Complianceadministrator**: Benutzer mit dieser Rolle verfügen über Verwaltungsberechtigungen innerhalb von Office 365 Security &amp; Compliance Center und Exchange Admin Center. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrator für den bedingten Zugriff**: Benutzer mit dieser Rolle haben die Möglichkeit, Azure Active Directory-Einstellungen für den bedingten Zugriff zu verwalten.
  > [!NOTE]
  > Um die Exchange ActiveSync-Richtlinie für bedingten Zugriff in Azure bereitzustellen, muss der Benutzer auch globaler Administrator sein.
  
* **Dynamics 365-Dienstadministrator:** Benutzer mit dieser Rolle verfügen über globale Berechtigungen in Microsoft CRM Online, sofern der Dienst vorhanden ist, und können Supporttickets verwalten und die Dienstintegrität überwachen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Geräteadministratoren**: Benutzer mit dieser Rolle werden zu lokalen Geräteadministratoren für alle Windows 10-Geräte, die mit Azure Active Directory verknüpft werden. Sie haben nicht die Möglichkeit zum Verwalten von Geräteobjekten in Azure Active Directory.

* **Verzeichnis lesen**: Dies ist eine Legacyrolle, die Anwendungen ohne Unterstützung für das [Consent Framework](active-directory-integrating-applications.md) zugewiesen wird. Diese Rolle sollte keinem Benutzer zugewiesen werden.

* **Konten für die Verzeichnissynchronisierung**: Verwenden Sie diese Rolle nicht. Diese Rolle wird automatisch dem Azure AD Connect-Dienst zugewiesen und ist weder für eine andere Verwendung vorgesehen, noch wird eine andere Verwendung unterstützt.

* **Verzeichnis-Schreiber**: Dies ist eine Legacyrolle, die Anwendungen ohne Unterstützung für das [Consent Framework](active-directory-integrating-applications.md) zugewiesen wird. Diese Rolle sollte keinem Benutzer zugewiesen werden.

* **Exchange-Dienstadministrator**: Benutzer mit dieser Rolle verfügen über globale Berechtigungen in Microsoft Exchange Online, wenn der Dienst vorhanden ist. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Globaler Administrator/Unternehmensadministrator**: Benutzer mit dieser Rolle haben Zugriff auf alle Verwaltungsfeatures in Azure Active Directory sowie Dienste, die einen Verbund mit Azure Active Directory bilden, z.B. Exchange Online, SharePoint Online und Skype for Business Online. Die Person, die die Anmeldung für den Azure Active Directory-Mandanten vornimmt, wird ein globaler Administrator. Nur globale Administratoren können weitere Administratorrollen zuweisen. In Ihrem Unternehmen können mehrere globale Administratoren vorhanden sein. Globale Administratoren können das Kennwort für alle Benutzer und alle anderen Administratoren zurücksetzen.

  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und in Azure AD PowerShell wird diese Rolle als „Unternehmensadministrator“ identifiziert. Im [Azure-Portal](https://portal.azure.com)ist dies der „globale Administrator“.
  >
  >

* **Gasteinladender**: Benutzer mit dieser Rolle können Einladungen für Azure Active Directory B2B-Gastbenutzer verwalten, wenn die Benutzereinstellung „Mitglieder können einladen“ auf „Nein“ gesetzt ist. Weitere Informationen zur B2B-Zusammenarbeit finden Sie unter [Was ist die Azure AD B2B-Zusammenarbeit?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Es sind keine anderen Berechtigungen eingeschlossen.

* **Information Protection-Administrator**: Benutzer mit dieser Rolle verfügen nur über Benutzerrechte für den Azure Information Protection-Dienst. Ihnen werden keine Benutzerrechte für Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health oder Office 365 Security & Compliance Center gewährt. Sie können Bezeichnungen für die Azure Information Protection-Richtlinie konfigurieren, Schutzvorlagen verwalten und den Schutz aktivieren.

* **Intune-Dienstadministrator**: Benutzer mit dieser Rolle verfügen über globale Berechtigungen in Microsoft Intune Online, wenn der Dienst vorhanden ist. Darüber hinaus beinhaltet diese Rolle die Möglichkeit, Benutzer und Geräte zum Zuordnen von Richtlinien zu verwalten sowie Gruppen zu erstellen und zu verwalten.

* **Postfachadministrator:** Diese Rolle wird nur im Rahmen der Exchange Online-E-Mail-Unterstützung für RIM Blackberry-Geräte verwendet. Verwenden Sie diese Rolle nicht, wenn Ihre Organisation keine Exchange Online-E-Mails auf RIM Blackberry-Geräten verwendet.

* **Partner Tier 1 Support** (Partnerunterstützung auf Ebene 1): Nicht verwenden. Diese Rolle wurde als veraltet markiert und wird aus Azure AD entfernt. Diese Rolle ist für einige wenige Wiederverkaufspartner von Microsoft und nicht zur allgemeinen Verwendung vorgesehen.

* **Partner Tier 2 Support** (Partnerunterstützung auf Ebene 2): Nicht verwenden. Diese Rolle wurde als veraltet markiert und wird aus Azure AD entfernt. Diese Rolle ist für einige wenige Wiederverkaufspartner von Microsoft und nicht zur allgemeinen Verwendung vorgesehen.

* **Kennwortadministrator/Helpdeskadministrator**: Benutzer mit dieser Rolle können Kennwörter ändern, Dienstanforderungen verwalten und die Integrität des Diensts überwachen. Helpdeskadministratoren können Kennwörter nur für Benutzer und andere Helpdeskadministratoren ändern. 

  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und Azure AD PowerShell wird diese Rolle als „Helpdeskadministrator“ identifiziert. Im [Azure-Portal](https://portal.azure.com/) lautet diese „Kennwortadministrator“.
  >
  >
  
* **Power BI-Dienstadministrator**: Benutzer mit dieser Rolle verfügen über globale Berechtigungen in Microsoft Power BI, wenn der Dienst vorhanden ist, sowie die Möglichkeit, Supporttickets zu verwalten und die Dienstintegrität zu überwachen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Administrator für privilegierte Rollen**: Benutzer mit dieser Rolle können Rollenzuweisungen in Azure Active Directory und in Azure AD Privileged Identity Management vornehmen. Überdies ermöglicht diese Rolle die vollumfängliche Verwaltung von Privileged Identity Management.

* **Meldet Reader**: Benutzer mit dieser Rolle können Verwendungsberichtsdaten und das Berichtsdashboard in Office 365 Admin Center und das Anpassungskontextpaket in Power BI anzeigen. Darüber hinaus stellt die Rolle Zugriff auf Anmeldeberichte und -aktivitäten in Azure AD und von der Microsoft Graph-Berichterstellungs-API zurückgegebene Daten zur Verfügung. Ein Benutzer, dem die Rolle „Meldet Reader“ zugewiesen ist, kann nur auf relevante Nutzungs- und Anpassungsmetriken zugreifen. Sie verfügen nicht über Administratorrechte, um Einstellungen zu konfigurieren oder auf produktspezifische Verwaltungskonsolen wie das Exchange Admin Center zuzugreifen. 

* **Sicherheitsadministrator**: Benutzer mit dieser Rolle verfügen über alle Leseberechtigungen der Rolle „Sicherheit lesen“ sowie die Möglichkeit, die Konfiguration für sicherheitsrelevante Dienste zu verwalten (z.B. Azure Active Directory Identity Protection, Azure Information Protection, Privileged Identity Management und Office 365 Security &amp; Compliance Center). Weitere Informationen zu Office 365-Berechtigungen finden Sie unter [Berechtigungen im Office 365 Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Sicherheit lesen**: Benutzer mit dieser Rolle verfügen über einen globalen schreibgeschützten Zugriff, einschließlich aller Informationen in Azure Active Directory, Identity Protection, Privileged Identity Management, sowie die Möglichkeit, Azure Active Directory-Anmeldeberichte und Überwachungsprotokolle zu lesen. Die Rolle gewährt außerdem Leseberechtigung im Office 365 Security & Compliance Center. Weitere Informationen zu Office 365-Berechtigungen finden Sie unter [Berechtigungen im Office 365 Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Dienstunterstützungsadministrator**: Benutzer mit dieser Rolle können bei Microsoft Supportanfragen für Azure- und Office 365-Dienste öffnen sowie das Service-Dashboard und das Nachrichtencenter im Azure-Portal und im Office 365-Verwaltungsportal einsehen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **SharePoint-Dienstadministrator**: Benutzer mit dieser Rolle verfügen über globale Berechtigungen in Microsoft SharePoint Online, wenn der Dienst vorhanden ist, sowie die Möglichkeit, Supporttickets zu verwalten und die Dienstintegrität zu überwachen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Skype for Business-/Lync-Dienstadministrator**: Benutzer mit dieser Rolle verfügen über globale Berechtigungen in Microsoft Skype for Business, wenn der Dienst vorhanden ist, sowie die Berechtigung zur Verwaltung von Skype-spezifischen Benutzerattributen in Azure Active Directory. Darüber hinaus bietet diese Rolle die Möglichkeit, Supporttickets zu verwalten und die Dienstintegrität zu überwachen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und in Azure AD PowerShell wird diese Rolle als „Lync-Dienstadministrator“ identifiziert. Im [Azure-Portal](https://portal.azure.com/) lautet diese „Skype for Business-Dienstadministrator“.
  >
  >

* **Benutzerkontoadministrator**: Benutzer mit dieser Rolle können sämtliche Benutzer und Gruppen erstellen und verwalten. Darüber hinaus beinhaltet diese Rolle die Möglichkeit, Supporttickets zu verwalten und die Dienstintegrität zu überwachen. Es gelten einige Einschränkungen. So lässt diese Rolle beispielsweise nicht das Löschen eines globalen Administrators zu. Benutzerkontoadministratoren können Kennwörter für Benutzer, Helpdeskadministratoren und andere Benutzerkontoadministratoren ändern.

## <a name="administrator-permissions"></a>Administratorberechtigungen

### <a name="billing-administrator"></a>Rechnungsadministrator

| Möglich | Nicht möglich |
| --- | --- |
|<p>Anzeigen von Unternehmens- und Benutzerinformationen</p><p>Verwalten von Office-Supporttickets</p><p>Durchführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p> |<p>Zurücksetzen von Benutzerkennwörtern</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der Verzeichnissynchronisierung</p><p>Anzeigen von Überwachungsprotokollen</p>|

### <a name="conditional-access-administrator"></a>Administrator für den bedingten Zugriff

| Möglich | Nicht möglich |
| --- | --- |
|<p>Anzeigen von Unternehmens- und Benutzerinformationen</p><p>Verwalten von Einstellungen für den bedingten Zugriff</p> |<p>Zurücksetzen von Benutzerkennwörtern</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der Verzeichnissynchronisierung</p><p>Anzeigen von Überwachungsprotokollen</p>|

### <a name="global-administrator"></a>Globaler Administrator
| Möglich | Nicht möglich |
| --- | --- |
|<p>Anzeigen von Unternehmens- und Benutzerinformationen</p><p>Verwalten von Office-Supporttickets</p><p>Durchführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Zurücksetzen von Benutzerkennwörtern</p><p>Zurücksetzen von Kennwörtern anderer Administratoren</p> <p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der Verzeichnissynchronisierung</p><p>Aktivieren und Deaktivieren der Multi-Factor Authentication</p><p>Anzeigen von Überwachungsprotokollen</p> |N/V |

### <a name="password-administrator--helpdesk-administrator"></a>Kennwort-/Helpdeskadministrator
| Möglich | Nicht möglich |
| --- | --- |
| <p>Anzeigen von Unternehmens- und Benutzerinformationen</p><p>Verwalten von Office-Supporttickets</p><p>Ändern von Kennwörtern ausschließlich für Benutzer und andere Helpdeskadministratoren</p>|<p>Durchführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der Verzeichnissynchronisierung</p><p>Anzeigen von Berichten</p>|

### <a name="information-protection-administrator"></a>Information Protection-Administrator
Geben Sie in | Möglich
-------- | ---------
Azure Information Protection | <li>Konfigurieren von Bezeichnungen und Einstellungen in globalen und bereichsbezogenen Richtlinien<li>Konfigurieren und Verwalten von Schutzvorlagen<li>Aktivieren oder Deaktivieren des Schutzes
 
### <a name="reports-reader"></a>Meldet Reader 
Möglich | Nicht möglich
------ | ----------
Anzeigen von Azure AD-Anmeldeberichten und -Überwachungsprotokollen<br>Anzeigen von Unternehmens- und Benutzerinformationen<br>Zugreifen auf das Office 365-Nutzungsdashboard | Erstellen und Verwalten von Benutzeransichten<br>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen<br>Delegieren von Administratorrollen an andere Benutzer<br>Verwalten von Unternehmensinformationen

### <a name="security-reader"></a>Sicherheit lesen
| Geben Sie in | Möglich |
| --- | --- |
| Identity Protection Center |Lesen von allen Sicherheitsberichten und Einstellungsinformationen für die Sicherheitsfunktionen<ul><li>Antispam<li>Verschlüsselung<li>Verhindern von Datenverlusten<li>Antischadsoftware<li>Erweiterter Schutz vor Bedrohungen<li>Antiphishing<li>Nachrichtenflussregeln |
| Privileged Identity Management |<p>Verfügt über schreibgeschützten Zugriff auf alle eingeblendeten Informationen in Azure AD PIM: Richtlinien und Berichte für Azure AD-Rollenzuweisungen, Sicherheitsüberprüfungen und in Zukunft Lesezugriff auf Richtliniendaten und Berichte für Szenarios zusätzlich zur Azure AD-Rollenzuweisung.<p>**Kann sich nicht** für Azure AD PIM registrieren oder Änderungen durchführen. Im PIM-Portal oder über PowerShell können Personen mit dieser Rolle zusätzliche Rollen (z.B. globaler Administrator oder Administrator für privilegierte Rollen) aktivieren, wenn der Benutzer für sie geeignet ist. |
| <p>Überwachen der Office 365-Dienstintegrität</p><p>Office 365 Security & Compliance Center</p> |<ul><li>Lesen und Verwalten von Warnungen<li>Lesen von Sicherheitsrichtlinien<li>Lesen von Bedrohungsanalysen, Cloud App Discovery und Quarantäne in Suchen und Untersuchungen<li>Lesen aller Berichte |

### <a name="security-administrator"></a>Sicherheitsadministrator
| Geben Sie in | Möglich |
| --- | --- |
| Identity Protection Center |<ul><li>Alle Berechtigungen der Rolle „Sicherheit lesen“<li>Darüber hinaus die Möglichkeit, alle IPC-Vorgänge außer des Zurücksetzens von Kennwörtern auszuführen. |
| Privileged Identity Management |<ul><li>Alle Berechtigungen der Rolle „Sicherheit lesen“<li>**nicht** verwalten. |
| <p>Überwachen der Office 365-Dienstintegrität</p><p>Office 365 Security & Compliance Center |<ul><li>Alle Berechtigungen der Rolle „Sicherheit lesen“<li>Kann alle Einstellungen im Feature „Advanced Threat Protection“ (Schutz vor Malware und Viren, schadhafte URL-Konfiguration, URL-Ablaufverfolgung usw.) konfigurieren. |

### <a name="service-administrator"></a>Dienstadministrator
| Möglich | Nicht möglich |
| --- | --- |
| <p>Anzeigen von Unternehmens- und Benutzerinformationen</p><p>Verwalten von Office-Supporttickets</p> |<p>Zurücksetzen von Benutzerkennwörtern</p><p>Durchführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der Verzeichnissynchronisierung</p><p>Anzeigen von Überwachungsprotokollen</p> |

### <a name="user-account-administrator"></a>Benutzerkontoadministrator
| Möglich | Nicht möglich |
| --- | --- |
| <p>Anzeigen von Unternehmens- und Benutzerinformationen</p><p>Verwalten von Office-Supporttickets</p><p>Ändern von Kennwörtern nur für Benutzer, Helpdeskadministratoren und andere Benutzerkontoadministratoren durch Benutzerkontoadministratoren</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen (mit Einschränkungen). Er oder Sie kann keinen globalen Administrator löschen oder andere Administratoren erstellen.</p> |<p>Durchführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der Verzeichnissynchronisierung</p><p>Aktivieren und Deaktivieren der Multi-Factor Authentication</p><p>Anzeigen von Überwachungsprotokollen</p> |

### <a name="to-add-a-colleague-as-a-global-administrator"></a>Hinzufügen eines Kollegen als globalen Administrator

1. Melden Sie sich beim [Azure Active Directory Admin Center](https://aad.portal.azure.com) über ein Konto an, das als globaler Administrator für das Mandantenverzeichnis konfiguriert ist.

   ![Öffnen des Azure AD Admin Center](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. Wählen Sie **Benutzer und Gruppen &gt; Alle Benutzer** aus.

3. Suchen Sie den Benutzer, der als globaler Administrator festgelegt werden soll, und öffnen Sie das Blatt für diesen Benutzer.

4. Wählen Sie auf dem Blatt „Benutzer“ die Option **Verzeichnisrolle** aus.
 
5. Wählen Sie auf dem Blatt „Verzeichnisrolle“ die Rolle **Globaler Administrator** aus, und speichern Sie sie.

## <a name="deprecated-roles"></a>Veraltete Rollen

Die folgenden Rollen sollten nicht verwendet werden. Sie wurden als veraltet markiert und werden aus Azure AD entfernt.

* Ad-hoc-Lizenzadministrator
* Benutzererstellung mit E-Mail-Überprüfung
* Geräteeinbindung
* Geräte-Manager
* Gerätebenutzer
* Geräteeinbindung am Arbeitsplatz

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Ändern von Administratoren für ein Azure-Abonnement finden Sie unter [Hinzufügen oder Ändern von Azure-Administratorrollen](../billing-add-change-azure-subscription-administrator.md)
* Informationen dazu, wie der Zugriff auf Ressourcen in Microsoft Azure gesteuert wird, finden Sie unter [Grundlegendes zum Zugriff auf Ressourcen in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* Weitere Informationen zur Beziehung zwischen Azure Active Directory und Ihrem Azure-Abonnement finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Verwalten von Benutzern](active-directory-create-users.md)
* [Verwalten von Kennwörtern](active-directory-manage-passwords.md)
* [Gruppen verwalten](active-directory-manage-groups.md)
