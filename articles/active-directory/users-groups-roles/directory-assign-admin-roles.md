---
title: Beschreibungen und Berechtigungen der Administratorrolle – Azure Active Directory | Microsoft-Dokumentation
description: Eine Administratorrolle kann zum Hinzufügen von Benutzern, zum Zuweisen von Administratorrollen, zum Zurücksetzen von Benutzerkennwörtern sowie zum Verwalten von Benutzerlizenzen oder Domänen verwendet werden.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 06/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d31bde1a33d622c2c0b7aa716cbbbfbc8ef42ecf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514574"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Berechtigungen der Administratorrolle in Azure Active Directory

Mithilfe von Azure Active Directory (Azure AD) können Sie Administratoren mit eingeschränkten Rechten bestimmen, um Identitätsaufgaben in Rollen mit weniger Rechten zu verwalten. Administratoren können für Zwecke wie das Hinzufügen oder Ändern von Benutzern, das Zuweisen von Administratorrollen, das Zurücksetzen von Benutzerkennwörtern, das Verwalten von Benutzerlizenzen oder das Verwalten von Domänennamen festgelegt werden. Die Standardberechtigungen für Benutzer können nur in den Benutzereinstellungen in Azure AD geändert werden.

Der globale Administrator hat Zugriff auf alle administrativen Funktionen. Der Person, die sich für ein Azure-Abonnement registriert, wird standardmäßig die globale Administratorrolle für das Verzeichnis zugewiesen. Nur globale Administratoren und Administratoren für privilegierte Rollen können Administratorrollen delegieren. Um das Risiko für Ihr Unternehmen zu verringern, wird empfohlen, diese Rolle nur wenigen Personen in Ihrem Unternehmen zuzuweisen.


## <a name="assign-or-remove-administrator-roles"></a>Zuweisen oder Entfernen von Administratorrollen

Anweisungen zum Zuweisen von Administratorrollen zu einem Benutzer in Azure Active Directory finden Sie unter [Anzeigen und Zuweisen von Administratorrollen in Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Verfügbare Rollen

Die folgenden Administratorrollen sind verfügbar:

* **[Anwendungsadministrator:](#application-administrator)** Benutzer mit dieser Rolle können alle Aspekte von Unternehmensanwendungen, Anwendungsregistrierungen und Anwendungsproxyeinstellungen erstellen und verwalten. Diese Rolle ermöglicht auch die Zustimmung zu delegierten Berechtigungen und Anwendungsberechtigungen mit Ausnahme von Microsoft Graph und Azure AD Graph. Benutzer, denen diese Rolle zugewiesen wurde, werden bei der Erstellung neuer Anwendungsregistrierungen oder Unternehmensanwendungen nicht als Besitzer hinzugefügt.

  <b>Wichtig</b>: Diese Rolle ermöglicht die Verwaltung von Anmeldeinformationen für Anwendungen. Benutzer, die dieser Rolle zugewiesen sind, können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Anwendung zu imitieren. Wenn der Identität der Anwendung der Zugriff auf Azure Active Directory gewährt wurde, z.B. die Berechtigung, Benutzer oder andere Objekte zu erstellen oder zu aktualisieren, kann ein dieser Rolle zugewiesener Benutzer diese Aktionen ausführen, während er die Identität der Anwendung annimmt. Diese Fähigkeit, die Identität der Anwendung anzunehmen, bedeutet ggf. eine Rechteerweiterung im Vergleich zu den Rollenzuweisungen des Benutzers in Azure AD. Beachten Sie, dass das Zuweisen eines Benutzers zur Anwendungsadministratorrolle ihm die Möglichkeit gibt, die Identität einer Anwendung anzunehmen.

* **[Anwendungsentwickler:](#application-developer)** Benutzer mit dieser Rolle können Anwendungsregistrierungen erstellen, wenn die Einstellung „Benutzer können Anwendungen registrieren“ auf „Nein“ festgelegt ist. Diese Rolle ermöglicht auch die Berechtigung, im eigenen Namen zuzustimmen, wenn die Einstellung „Benutzer können Apps zustimmen, die in ihrem Namen auf Unternehmensdaten zugreifen“ auf „Nein“ festgelegt ist. Benutzer, denen diese Rolle zugewiesen wurde, werden bei der Erstellung neuer Anwendungsregistrierungen oder Unternehmensanwendungen als Besitzer hinzugefügt.

* **[Authentifizierungsadministrator](#authentication-administrator)** : Benutzer mit dieser Rolle können kennwortlose Anmeldeinformationen festlegen oder zurücksetzen. Authentifizierungsadministratoren können Benutzer auffordern, ihre vorhandenen Anmeldeinformationen ohne Kennwort (z.B. MFA, FIDO) erneut zu registrieren und **auf einem Gerät gespeicherte MFA-Informationen** zu widerrufen. Dadurch werden Benutzer, die keine Administratoren sind oder denen nur die folgenden Rollen zugewiesen wurden, bei der nächsten Anmeldung zur Eingabe ihrer MFA-Anmeldeinformationen aufgefordert:
  * Authentifizierungsadministrator
  * Rolle „Verzeichnis lesen“
  * Gasteinladender
  * Nachrichtencenter-Leser
  * Meldet Reader

  Die Rolle „Authentifizierungsadministrator“ ist derzeit als öffentliche Vorschauversion verfügbar. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Wichtig</b>: Benutzer mit dieser Rolle können Anmeldeinformationen für Benutzer ändern, die Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen innerhalb und außerhalb von Azure Active Directory haben. Das bedeutet, dass Benutzer, die Anmeldeinformationen ändern können, ggf. auch die Identität und die Berechtigungen des betreffenden Benutzers annehmen können. Beispiel:

  * Besitzer von Anwendungsregistrierungen und Unternehmensanwendungen, die Anmeldeinformationen von Apps verwalten können, die sie besitzen. Diese Apps können über höhere Berechtigungen in Azure AD und in anderen Diensten verfügen, die Authentifizierungsadministratoren nicht gewährt werden. So kann ein Authentifizierungsadministrator die Identität eines Anwendungsbesitzers annehmen und dann die Identität einer privilegierten Anwendung durch Aktualisieren der Anmeldeinformationen für die Anwendung annehmen.
  * Besitzer von Azure-Abonnements, die ggf. auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure zugreifen können.
  * Besitzer von Sicherheitsgruppen und Office 365-Gruppen, die die Gruppenmitgliedschaft verwalten können. Diese Gruppen können Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure AD und in anderen Diensten gewähren.
  * Administratoren in anderen Diensten außerhalb von Azure AD wie Exchange Online, Office Security and Compliance Center und Personalwesen.
  * Nichtadministratoren wie Führungskräfte, Rechtsberater und Mitarbeiter der Personalabteilung mit Zugriff auf vertrauliche oder private Informationen.

* **[Azure Information Protection-Administrator](#azure-information-protection-administrator)** : Benutzer mit dieser Rolle besitzen alle Berechtigungen für den Azure Information Protection-Dienst. Sie können Bezeichnungen für die Azure Information Protection-Richtlinie konfigurieren, Schutzvorlagen verwalten und den Schutz aktivieren. Diese Rolle gewährt keine Berechtigungen für Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health oder Office 365 Security & Compliance Center.

* **[B2C-Benutzerflowadministrator:](#b2c-user-flow-administrator)** Benutzer mit dieser Rolle können B2C-Benutzerflows (auch als „integrierte“ Richtlinien bezeichnet) im Azure-Portal erstellen und verwalten. Durch Erstellen oder Bearbeiten von Benutzerflows können diese Benutzer den HTML-/CSS-/JavaScript-Inhalt für Benutzer ändern, die MFA-Anforderungen pro Benutzerflow ändern, Ansprüche im Token ändern und Sitzungseinstellungen für alle Richtlinien im Mandanten anpassen. Auf der anderen Seite bietet diese Rolle nicht die Möglichkeit, Benutzerdaten zu überprüfen oder Änderungen an Attributen vorzunehmen, die im Mandantenschema enthalten sind. Änderungen an Identity Experience Framework-Richtlinien (benutzerdefiniert) sind auch außerhalb des Bereichs dieser Rolle möglich.

* **[B2C-Administrator für Benutzerflowattribute:](#b2c-user-flow-attribute-administrator)** Benutzer mit dieser Rolle können benutzerdefinierte Attribute, die für alle Benutzerflows im Mandanten verfügbar sind, hinzufügen und löschen. Daher können Benutzer mit dieser Rolle dem Benutzerschema neue Elemente hinzufügen und diese ändern und haben damit Einfluss auf das Verhalten aller Benutzerflows. Indirekt kann dies auch ändern, welche Daten von Benutzern abgefragt und letztendlich als Ansprüche an Anwendungen gesendet werden. Diese Rolle kann keine Benutzerflows bearbeiten.

* **[B2C-IEF-Schlüsselsatzadministrator:](#b2c-ief-keyset-administrator)**    Benutzer können die Richtlinienschlüssel und -geheimnisse für Tokenverschlüsselung, Tokensignaturen und Verschlüsselung/Entschlüsselung von Ansprüchen erstellen und verwalten. Durch das Hinzufügen neuer Schlüssel zu vorhandenen Schlüsselcontainern kann dieser Administrator mit eingeschränkten Rechten, nach Bedarf Rollover für Geheimnisse ausführen, ohne dass dies Auswirkungen auf vorhandene Anwendungen hat. Diese Benutzer können den vollständigen Inhalt dieser Geheimnisse und deren Ablaufdaten anzeigen – auch nach deren Erstellung.
    
  <b>Wichtig:</b> Dies ist eine vertrauliche Rolle. Die Administratorrolle für Schlüsselsätze sollte sorgfältig überwacht und mit Bedacht für Präproduktion und Produktion zugewiesen werden.

* **[B2C-IEF-Richtlinienadministrator:](#b2c-ief-policy-administrator)** Benutzer mit dieser Rolle können alle benutzerdefinierten Richtlinien in Azure AD B2C erstellen, lesen, aktualisieren und löschen und haben daher vollständige Kontrolle über das Identity Experience Framework im zugehörigen Azure AD B2C-Mandanten. Durch das Bearbeiten von Richtlinien können diese Benutzer einen direkten Verbund mit externen Identitätsanbietern einrichten, das Verzeichnisschema und alle benutzerseitigen Inhalte (HTML, CSS, JavaScript) sowie die Anforderungen für das Abschließen einer Authentifizierung ändern, neue Benutzer erstellen, Benutzerdaten an externe Systeme senden (einschließlich einer vollständigen Migration) und alle Benutzerinformationen bearbeiten (einschließlich vertraulicher Felder wie Kennwörter und Telefonnummern). Andererseits kann diese Rolle keine Verschlüsselungsschlüssel ändern oder Geheimnisse für den Verbund im Mandanten bearbeiten.

  <b>Wichtig:</b> Der B2-IEF-Richtlinienadministrator ist eine streng vertrauliche Rolle, die nur sehr wenigen Benutzern für Mandanten in der Produktion zugewiesen werden sollte. Aktivitäten dieser Benutzer sollten streng überwacht werden. Dies gilt vor allem für Mandanten in der Produktion.

* **[Rechnungsadministrator:](#billing-administrator)** Tätigt Käufe, verwaltet Abonnements und Supporttickets und überwacht die Dienstintegrität.

* **[Cloudanwendungsadministrator:](#cloud-application-administrator)** Benutzer mit dieser Rolle haben die gleichen Berechtigungen wie die Rolle des Anwendungsadministrators, mit Ausnahme der Möglichkeit, den Anwendungsproxy zu verwalten. Diese Rolle ermöglicht die Erstellung und Verwaltung aller Aspekte von Unternehmensanwendungen und Anwendungsregistrierungen. Diese Rolle ermöglicht auch die Zustimmung zu delegierten Berechtigungen und Anwendungsberechtigungen mit Ausnahme von Microsoft Graph und Azure AD Graph. Benutzer, denen diese Rolle zugewiesen wurde, werden bei der Erstellung neuer Anwendungsregistrierungen oder Unternehmensanwendungen nicht als Besitzer hinzugefügt.

  <b>Wichtig</b>: Diese Rolle ermöglicht die Verwaltung von Anmeldeinformationen für Anwendungen. Benutzer, die dieser Rolle zugewiesen sind, können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Anwendung zu imitieren. Wenn der Identität der Anwendung der Zugriff auf Azure Active Directory gewährt wurde, z.B. die Berechtigung, Benutzer oder andere Objekte zu erstellen oder zu aktualisieren, kann ein dieser Rolle zugewiesener Benutzer diese Aktionen ausführen, während er die Identität der Anwendung annimmt. Diese Fähigkeit, die Identität der Anwendung anzunehmen, bedeutet ggf. eine Rechteerweiterung im Vergleich zu den Rollenzuweisungen des Benutzers in Azure AD. Beachten Sie, dass das Zuweisen eines Benutzers zur Cloudanwendungsadministrator-Rolle ihm die Möglichkeit gibt, die Identität einer Anwendung anzunehmen.

* **[Cloudgeräteadministrator:](#cloud-device-administrator)** Benutzer mit dieser Rolle können Geräte in Azure AD aktivieren, deaktivieren und löschen sowie Windows 10-BitLocker-Schlüssel (falls vorhanden) im Azure-Portal lesen. Die Rolle gewährt keine Berechtigungen für die Verwaltung anderer Eigenschaften auf dem Gerät.

* **[Complianceadministrator:](#compliance-administrator)** Benutzer mit dieser Rolle verfügen über Berechtigungen zum Verwalten von Compliancefeatures im Microsoft 365 Compliance Center, Microsoft 365 Admin Center, Azure, und Office 365 Security & Compliance Center. Die Benutzer können auch alle Features im Exchange Admin Center sowie im Teams und Skype for Business Admin Center verwalten und Supporttickets für Azure und Microsoft 365 erstellen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  Geben Sie in | Möglich
  ----- | ----------
  [Microsoft 365 Compliance Center](https://protection.office.com) | Schützen und Verwalten Ihrer Organisationsdaten für Microsoft 365-Dienste<br>Verwalten von Konformitätwarnungen
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Nachverfolgen, Zuweisen und Überprüfen der Einhaltung gesetzlicher Vorschriften durch Ihre Organisation
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Verwalten der Datengovernance<br>Durchführen von Untersuchung zu rechtlichen Aspekten und von Daten<br>Verwalten von DRS-Anforderungen
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Anzeigen aller Intune-Überwachungsdaten
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Verfügt über schreibgeschützten Zugriff und kann Warnungen verwalten<br>Kann Dateirichtlinien erstellen und ändern und Dateigovernanceaktionen zulassen<br> Kann alle unter „Datenverwaltung“ integrierten Berichte anzeigen

<!--* **[Compliance Data Administrator](#compliance-data-administrator)**: Users with this role have permissions to protect and track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Users can also manage all features within the Exchange admin center, Compliance Manager, and Teams & Skype for Business admin center and create support tickets for Azure and Microsoft 365.

  In | Can do
  ----- | ----------
  [Microsoft 365 compliance center](https://protection.office.com) | Monitor compliance-related policies across Microsoft 365 services<br>Manage compliance alerts
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Track, assign, and verify your organization's regulatory compliance activities
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage data governance<br>Perform legal and data investigation<br>Manage Data Subject Request
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | View all Intune audit data
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Has read-only permissions and can manage alerts<br>Can create and modify file policies and allow file governance actions<br> Can view all the built-in reports under Data Management
-->
* **[Administrator für bedingten Zugriff:](#conditional-access-administrator)** Benutzer mit dieser Rolle können Azure Active Directory-Einstellungen für den bedingten Zugriff verwalten.
  > [!NOTE]
  > Um die Exchange ActiveSync-Richtlinie für bedingten Zugriff in Azure bereitzustellen, muss der Benutzer auch ein globaler Administrator sein.
  
* **[Genehmigende Person für den Kunden-Lockbox-Zugriff:](#customer-lockbox-access-approver)** Verwaltet [Kunden-Lockbox-Anforderungen](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) in Ihrer Organisation. Sie erhalten E-Mail-Benachrichtigungen für Kunden-Lockbox-Anforderungen und können Anforderungen über das Microsoft 365 Admin Center genehmigen und ablehnen. Außerdem können sie das Feature Kunden-Lockbox aktivieren und deaktivieren. Nur globale Administratoren können die Kennwörter von Personen, die dieser Rolle zugewiesen sind, zurücksetzen.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Geräteadministratoren:](#device-administrators)** Diese Rolle kann nur als zusätzlicher lokaler Administrator in den [Geräteeinstellungen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/) zugewiesen werden. Benutzer mit dieser Rolle werden auf allen Windows 10-Geräten, die in Azure Active Directory eingebunden sind, als Administratoren für den lokalen Computer festgelegt. Sie haben nicht die Möglichkeit zum Verwalten von Geräteobjekten in Azure Active Directory. 

* **[Verzeichnis lesen:](#directory-readers)** Diese Rolle sollte nur älteren Anwendungen zugewiesen werden, die das [Consent Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md) nicht unterstützen. Weisen Sie sie keinen Benutzern zu.

* **[Konten zur Verzeichnissynchronisierung:](#directory-synchronization-accounts)** Nicht verwenden. Diese Rolle wird automatisch dem Azure AD Connect-Dienst zugewiesen und ist weder für eine andere Verwendung vorgesehen, noch wird eine andere Verwendung unterstützt.

* **[Verzeichnis schreiben:](#directory-writers)** Dies ist eine Legacyrolle, die Anwendungen ohne Unterstützung für das [Consent Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md) zugewiesen wird. Diese Rolle sollte keinem Benutzer zugewiesen werden.

* **[Dynamics 365-Administrator/CRM-Administrator:](#crm-service-administrator)** Benutzer mit dieser Rolle besitzen globale Berechtigungen innerhalb von Microsoft Dynamics 365 Online, wenn der Dienst verfügbar ist, und können Supporttickets verwalten und die Dienstintegrität überwachen. Weitere Informationen finden Sie unter [Verwenden der Dienstadministratorrolle zum Verwalten Ihres Mandanten](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > In der Microsoft Graph-API, der Azure AD Graph-API und Azure AD PowerShell wird diese Rolle als „Dynamics 365-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „Dynamics 365-Administrator“.

* **[Exchange-Administrator:](#exchange-service-administrator)** Benutzer mit dieser Rolle besitzen globale Berechtigungen in Microsoft Exchange Online, wenn der Dienst verfügbar ist. Außerdem haben sie die Möglichkeit, alle Office 365-Gruppen zu erstellen und zu verwalten, Supporttickets zu verwalten und die Dienstintegrität zu überwachen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und in Azure AD PowerShell wird diese Rolle als „Exchange-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „Exchange-Administrator“. Im [Exchange Admin Center](https://go.microsoft.com/fwlink/p/?LinkID=529144) lautet sie „Exchange Online-Administrator“. 

* **[Externer Identitätsanbieteradministrator:](#external-identity-provider-administrator)** Dieser Administrator verwaltet den Verbund zwischen Azure Active Directory-Mandanten und externen Identitätsanbietern. Benutzer mit dieser Rolle können neue Identitätsanbieter hinzufügen und alle verfügbaren Einstellungen (z. B. Authentifizierungspfad, Dienst-ID, zugeordnete Schlüsselcontainer) konfigurieren. Diese Benutzer können festlegen, dass der Mandant Authentifizierungen von externen Identitätsanbietern vertraut. Die resultierenden Auswirkungen auf die Benutzerumgebung hängt vom Typ des Mandanten ab:
  * Azure Active Directory-Mandanten für Mitarbeiter und Partner: Das Hinzufügen eines Verbunds (z. B. mit Gmail) hat sofortige Auswirkungen auf Einladungen für Gäste, die noch nicht eingelöst wurden. Weitere Informationen finden Sie unter [Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Azure Active Directory B2C-Mandanten: Das Hinzufügen eines Verbunds (z. B. mit Facebook oder einer anderen Azure Active Directory-Instanz) wirkt sich nicht sofort auf die Endbenutzerflows aus, sondern erst, wenn der Identitätsanbieter als Option in einem Benutzerflow (einer integrierten Richtlinie) hinzugefügt wird. Ein Beispiel finden Sie unter [Konfigurieren eines Microsoft-Kontos als Identitätsanbieter](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app). Um Benutzerflows zu ändern, ist die Rolle „B2C-Benutzerflowadministrator“ mit eingeschränkten Rechen erforderlich.

* **[Globaler Administrator/Unternehmensadministrator:](#company-administrator)** Benutzer mit dieser Rolle besitzen Zugriff auf alle administrativen Features in Azure Active Directory sowie Dienste, die Azure Active Directory-Identitäten nutzen, z.B. Microsoft 365 Security Center, Microsoft 365 Compliance Center, Exchange Online, SharePoint Online oder Skype for Business Online. Die Person, die die Anmeldung für den Azure Active Directory-Mandanten vornimmt, wird ein globaler Administrator. Nur globale Administratoren können weitere Administratorrollen zuweisen. In Ihrem Unternehmen können mehrere globale Administratoren vorhanden sein. Globale Administratoren können das Kennwort für alle Benutzer und alle anderen Administratoren zurücksetzen.

  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und in Azure AD PowerShell wird diese Rolle als „Unternehmensadministrator“ identifiziert. Im [Azure-Portal](https://portal.azure.com)ist dies der „globale Administrator“.
  >
  >

* **[Gasteinladender:](#guest-inviter)** Wenn die Benutzereinstellung **Mitglieder können einladen** auf „Nein“ festgelegt ist, können Benutzer in dieser Rolle Azure Active Directory B2B-Gastbenutzereinladungen verwalten. Weitere Informationen zur B2B-Zusammenarbeit finden Sie unter [Was ist die Azure AD B2B-Zusammenarbeit?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Es sind keine anderen Berechtigungen eingeschlossen.

* **[Intune-Administrator:](#intune-service-administrator)** Benutzer mit dieser Rolle besitzen globale Berechtigungen in Microsoft Intune Online, wenn der Dienst verfügbar ist. Darüber hinaus beinhaltet diese Rolle die Möglichkeit, Benutzer und Geräte zum Zuordnen von Richtlinien zu verwalten sowie Gruppen zu erstellen und zu verwalten. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung mit Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).
  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und in Azure AD PowerShell wird diese Rolle als „Intune-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „Intune-Administrator“.
  
 * **[Kaizala-Administrator](#kaizala-administrator)** : Benutzer mit dieser Rolle besitzen globale Berechtigungen zum Verwalten von Einstellungen in Microsoft Kaizala, wenn der Dienst verfügbar ist, und können Supporttickets verwalten und die Dienstintegrität überwachen.
Darüber hinaus können diese Benutzer auf Berichte zur Einführung und Nutzung von Kaizala durch Mitglieder der Organisation sowie auf Geschäftsberichte zugreifen, die mithilfe von Kaizala-Aktionen generiert wurden. 

* **[Lizenzadministrator:](#license-administrator)** Benutzer mit dieser Rolle können Lizenzzuweisungen für Benutzer und Gruppen (unter Verwendung der gruppenbasierten Lizenzierung) hinzufügen, entfernen und aktualisieren sowie den Verwendungsstandort für Benutzer verwalten. Mit dieser Rolle ist es nicht möglich, Abonnements zu erwerben oder zu verwalten, Gruppen zu erstellen oder zu verwalten oder Benutzer zu erstellen oder zu verwalten (mit Ausnahme des Verwendungsstandorts). Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

* **[Nachrichtencenter-Datenschutzleseberechtigter](#message-center-privacy-reader)** : Benutzer mit dieser Rolle können alle Benachrichtigungen im Nachrichtencenter überwachen, einschließlich Nachrichten zum Datenschutz. Nachrichtencenter-Datenschutzleseberechtigte erhalten E-Mail-Benachrichtigungen, einschließlich Nachrichten zum Datenschutz, und können Benachrichtigungen mithilfe der Einstellungen im Nachrichtencenter abbestellen. Nur der globale Administrator und Nachrichtencenter-Datenschutzleseberechtigte können Nachrichten zum Datenschutz lesen. Darüber hinaus umfasst diese Rolle die Berechtigung zum Anzeigen von Gruppen, Domänen und Abonnements. Diese Rolle umfasst keine Berechtigung zum Anzeigen, Erstellen oder Verwalten von Service Requests.

* **[Nachrichtencenter-Leser:](#message-center-reader)** Benutzer mit dieser Rolle können Benachrichtigungen und empfohlene Integritätsupdates für ihre Organisation und die konfigurierten Dienste wie Exchange, Intune und Microsoft Teams im [Office 365-Nachrichtencenter](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) überwachen. Nachrichtencenter-Leser erhalten eine wöchentliche E-Mail-Übersicht der Beiträge und Updates und können Beiträge in Office 365 teilen. In Azure AD haben Benutzer mit dieser Rolle nur schreibgeschützten Zugriff auf Azure AD-Dienste wie Benutzer und Gruppen. Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

* **[Partnersupport der Ebene 1:](#partner-tier1-support)** Nicht verwenden. Diese Rolle wurde als veraltet markiert und wird aus Azure AD entfernt. Diese Rolle ist für einige wenige Wiederverkaufspartner von Microsoft und nicht zur allgemeinen Verwendung vorgesehen.

* **[Partnersupport der Ebene 2:](#partner-tier2-support)** Nicht verwenden. Diese Rolle wurde als veraltet markiert und wird aus Azure AD entfernt. Diese Rolle ist für einige wenige Wiederverkaufspartner von Microsoft und nicht zur allgemeinen Verwendung vorgesehen.

* **[Helpdeskadministrator (Kennwort)](#helpdesk-administrator)** Benutzer mit dieser Rolle können Kennwörter ändern, Aktualisierungstoken für ungültig erklären, Dienstanforderungen verwalten und die Dienstintegrität überwachen. Wenn ein Aktualisierungstoken für ungültig erklärt wird, muss sich der Benutzer erneut anmelden. Helpdeskadministratoren können Kennwörter zurücksetzen und Aktualisierungstoken anderer Benutzer annullieren, die keine Administratoren sind oder denen nur die folgenden Rollen zugewiesen wurden:
  * Rolle „Verzeichnis lesen“
  * Gasteinladender
  * Helpdeskadministrator
  * Nachrichtencenter-Leser
  * Meldet Reader
  
  <b>Wichtig</b>: Benutzer mit dieser Rolle können Kennwörter für Benutzer ändern, die Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen innerhalb und außerhalb von Azure Active Directory haben. Benutzer, die Kennwörter ändern können, können ggf. auch die Identität und die Berechtigungen des betreffenden Benutzers annehmen. Beispiel:
  * Besitzer von Anwendungsregistrierungen und Unternehmensanwendungen, die Anmeldeinformationen von Apps verwalten können, die sie besitzen. Diese Apps können über höhere Berechtigungen in Azure AD und in anderen Diensten verfügen, die Helpdeskadministratoren nicht gewährt werden. So kann ein Helpdeskadministrator die Identität eines Anwendungsbesitzers annehmen und dann die Identität einer privilegierten Anwendung durch Aktualisieren der Anmeldeinformationen für die Anwendung annehmen.
  * Besitzer von Azure-Abonnements, die ggf. auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure zugreifen können.
  * Besitzer von Sicherheitsgruppen und Office 365-Gruppen, die die Gruppenmitgliedschaft verwalten können. Diese Gruppen können Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure AD und in anderen Diensten gewähren.
  * Administratoren in anderen Diensten außerhalb von Azure AD wie Exchange Online, Office Security and Compliance Center und Personalwesen.
  * Nichtadministratoren wie Führungskräfte, Rechtsberater und Mitarbeiter der Personalabteilung mit Zugriff auf vertrauliche oder private Informationen.


  > [!NOTE]
  > Das Delegieren von administrativen Berechtigungen für Teilmengen von Benutzern und das Anwenden von Richtlinien auf eine Teilmenge der Benutzer kann über [Verwaltungseinheiten (Vorschauversion)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units) erfolgen.


  > [!NOTE]
  > Im [Azure-Portal](https://portal.azure.com/) hieß diese Rolle früher „Kennwortadministrator“. Wir ändern den Namen in „Helpdeskadministrator“, damit er dem Namen in Azure AD PowerShell, Azure AD Graph-API und Microsoft Graph-API entspricht. Für kurze Zeit verwenden wir im Azure-Portal den Namen „Helpdeskadministrator (Kennwort)“, bevor wir ihn in „Helpdeskadministrator“ ändern.


* **[Power BI-Administrator:](#power-bi-service-administrator)** Benutzer mit dieser Rolle besitzen globale Berechtigungen innerhalb von Microsoft Power BI, wenn der Dienst verfügbar ist, und können Supporttickets verwalten und die Dienstintegrität überwachen. Weitere Informationen finden Sie unter [Grundlegendes zur Power BI-Administratorrolle](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und Azure AD PowerShell wird diese Rolle als „Power BI-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „Power BI-Administrator“.

* **[Privilegierter Authentifizierungsadministrator](#privileged-authentication-administrator)** : Benutzer mit dieser Rolle können Anmeldeinformationen ohne Kennwort für alle Benutzer, einschließlich globaler Administratoren, festlegen oder zurücksetzen. Privilegierte Authentifizierungsadministratoren können eine erneute Registrierung von Benutzern anhand einer vorhandenen Anmeldeinformation ohne Kennwort (z.B. MFA, FIDO) erzwingen und „Speichern der MFA auf dem Gerät“ widerrufen. In diesem Fall werden alle Benutzer bei der nächsten Anmeldung zur MFA-Eingabe aufgefordert. Privilegierte Authentifizierungsadministratoren können folgende Aktionen ausführen:
  * Benutzer zwingen, sich mit vorhandenen Anmeldeinformationen ohne Kennwort (z.B. MFA, FIDO) erneut zu registrieren
  * „Speichern der MFA auf dem Gerät“ widerrufen, wodurch bei der nächsten Anmeldung zur MFA-Eingabe aufgefordert wird

* **[Administrator für privilegierte Rollen:](#privileged-role-administrator)** Benutzer mit dieser Rolle können Rollenzuweisungen in Azure Active Directory und Azure AD Privileged Identity Management verwalten. Überdies ermöglicht diese Rolle Verwaltung aller Aspekte von Privileged Identity Management und administrativer Einheiten.

  <b>Wichtig</b>: Diese Rolle ermöglicht die Verwaltung von Zuweisungen für alle Azure AD-Rollen, einschließlich der globalen Administratorrolle. Diese Rolle umfasst keine anderen privilegierten Funktionen in Azure AD wie das Erstellen oder Aktualisieren von Benutzern. Benutzer, die dieser Rolle zugewiesen sind, können sich selbst oder anderen jedoch zusätzliche Berechtigungen gewähren, indem sie zusätzliche Rollen zuweisen.

* **[Berichtsleser:](#reports-reader)** Benutzer mit dieser Rolle können Nutzungsberichtsdaten und das Berichtsdashboard im Microsoft 365 Admin Center sowie das Einführungskontextpaket in Power BI anzeigen. Darüber hinaus stellt die Rolle Zugriff auf Anmeldeberichte und -aktivitäten in Azure AD und von der Microsoft Graph-Berichterstellungs-API zurückgegebene Daten zur Verfügung. Ein Benutzer, dem die Rolle „Meldet Reader“ zugewiesen ist, kann nur auf relevante Nutzungs- und Anpassungsmetriken zugreifen. Sie verfügen nicht über Administratorrechte, um Einstellungen zu konfigurieren oder auf produktspezifische Verwaltungskonsolen wie das Exchange Admin Center zuzugreifen. Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

* **[Suchadministrator](#search-administrator)** : Benutzer mit dieser Rolle haben Vollzugriff auf alle Microsoft Search-Verwaltungsfunktionen im Microsoft 365 Admin Center. Suchadministratoren können die Rollen „Suchadministrator“ und „Such-Editor“ an Benutzer delegieren und Inhalte wie Lesezeichen, Fragen und Antworten sowie Standorte erstellen und verwalten. Darüber hinaus können diese Benutzer das Nachrichtencenter anzeigen, die Dienstintegrität überwachen und Service Requests erstellen.

* **[Such-Editor](#search-editor)** : Benutzer mit dieser Rolle können Inhalte für Microsoft Search im Microsoft 365 Admin Center erstellen, verwalten und löschen. Hierzu gehören Inhalte wie Lesezeichen, Fragen und Antworten sowie Standorte.

* **[Sicherheitsadministrator:](#security-administrator)** Benutzer mit dieser Rolle haben Berechtigungen zur Verwaltung sicherheitsrelevanter Funktionen im Microsoft 365 Security Center, Azure Active Directory Identity Protection, Azure Information Protection und Office 365 Security & Compliance Center. Weitere Informationen zu Office 365-Berechtigungen finden Sie unter [Berechtigungen im Office 365 Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  Geben Sie in | Möglich
  --- | ---
  [Microsoft 365 Security Center](https://protection.office.com) | Überwachen von sicherheitsrelevanten Richtlinien in Microsoft 365-Diensten<br>Verwalten von Sicherheitsbedrohungen und Warnungen<br>Anzeigen von Berichten
  Identity Protection Center | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“<br>Darüber hinaus die Möglichkeit, alle Vorgänge im Identity Protection Center außer des Zurücksetzens von Kennwörtern auszuführen
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alle Berechtigungen der Rolle „Sicherheitsleseberechtigter“<br>**Kann keine** Azure AD-Rollenzuweisungen oder -Einstellungen verwalten
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Verwalten von Sicherheitsrichtlinien<br>Anzeigen, Untersuchen und Reagieren auf Sicherheitsbedrohungen<br>Anzeigen von Berichten
  Azure Advanced Threat Protection | Überwachen und Reagieren auf verdächtige Sicherheitsaktivitäten
  Windows Defender ATP und EDR | Zuweisen von Rollen<br>Verwalten von Computergruppen<br>Konfigurieren der Endpunkt-Bedrohungserkennung und der automatisierten Korrektur<br>Anzeigen, Untersuchen und Reagieren auf Warnungen
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Anzeigen von Benutzern, Geräten, Registrierung, Konfiguration und Anwendungsinformationen<br>Kann keine Änderungen an Intune vornehmen
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Hinzufügen von Administratoren, Richtlinien und Einstellungen, Hochladen von Protokollen und Ausführen von Governanceaktionen
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kann Sicherheitsrichtlinien und -zustände anzeigen, Sicherheitsrichtlinien bearbeiten sowie Warnungen und Empfehlungen anzeigen und verwerfen.
  [Office 365-Dienstintegrität](https://docs.microsoft.com/office365/enterprise/view-service-health) | Anzeigen der Integrität von Office 365-Diensten

<!--* **[Security operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, as well as the ability to read Azure Active Directory sign-in reports and audit logs, and in Office 365 Security & Compliance Center.

  In | Can do
  --- | ---
  [Microsoft 365 security center](https://protection.office.com) | All permissions of the Security Reader role<br>View, investigate, and respond to security threats alerts
  Identity Protection Center | All permissions of the Security Reader role<br>Additionally, the ability to perform all Identity Protection Center operations except for resetting passwords
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | All permissions of the Security Reader role
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  Windows Defender ATP and EDR | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | All permissions of the Security Reader role
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | All permissions of the Security Reader role
  [Office 365 service health](https://docs.microsoft.com/office365/enterprise/view-service-health) | View the health of Office 365 services
-->
* **[Sicherheitsleseberechtigter:](#security-reader)** Benutzer mit dieser Rolle besitzen globalen schreibgeschützten Zugriff auf sicherheitsbezogene Features, einschließlich alle Informationen in Microsoft 365 Security Center, Azure Active Directory, Identity Protection, und Privileged Identity Management, sowie die Möglichkeit zum Lesen von Azure Active Directory-Anmeldeberichten und Überwachungsprotokollen und für das Office 365 Security & Compliance Center. Weitere Informationen zu Office 365-Berechtigungen finden Sie unter [Berechtigungen im Office 365 Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  Geben Sie in | Möglich
  --- | ---
  [Microsoft 365 Security Center](https://protection.office.com) | Anzeigen von sicherheitsrelevanten Richtlinien in Microsoft 365-Diensten<br>Anzeigen von Sicherheitsbedrohungen und Warnungen<br>Anzeigen von Berichten
  Identity Protection Center | Lesen von allen Sicherheitsberichten und Einstellungsinformationen für die Sicherheitsfunktionen<br><ul><li>Antispam<li>Verschlüsselung<li>Verhindern von Datenverlusten<li>Antischadsoftware<li>Erweiterter Schutz vor Bedrohungen<li>Antiphishing<li>Nachrichtenflussregeln
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Verfügt über schreibgeschützten Zugriff auf alle eingeblendeten Informationen in Azure AD PIM: Richtlinien und Berichte für Azure AD-Rollenzuweisungen, Sicherheitsüberprüfungen und in Zukunft Lesezugriff auf Richtliniendaten und Berichte für Szenarien zusätzlich zur Azure AD-Rollenzuweisung.<br>**Kann sich nicht** für Azure AD PIM registrieren oder Änderungen durchführen. Im PIM-Portal oder über PowerShell können Personen mit dieser Rolle zusätzliche Rollen (z.B. globaler Administrator oder Administrator für privilegierte Rollen) aktivieren, wenn der Benutzer dafür geeignet ist.
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Anzeigen von Sicherheitsrichtlinien<br>Anzeigen und Untersuchen von Sicherheitsbedrohungen<br>Anzeigen von Berichten
  Windows Defender ATP und EDR | Anzeigen und Untersuchen von Warnungen
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Anzeigen von Benutzern, Geräten, Registrierung, Konfiguration und Anwendungsinformationen Kann keine Änderungen an Intune vornehmen
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Verfügt über schreibgeschützten Zugriff und kann Warnungen verwalten
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kann Empfehlungen und Warnungen sowie Sicherheitsrichtlinien und -zustände anzeigen, aber keine Änderungen vornehmen.
  [Office 365-Dienstintegrität](https://docs.microsoft.com/office365/enterprise/view-service-health) | Anzeigen der Integrität von Office 365-Diensten

* **[Dienstunterstützungsadministrator:](#service-support-administrator)** Benutzer mit dieser Rolle können bei Microsoft Supportanfragen für Azure- und Office 365-Dienste öffnen sowie das Dienstdashboard und das Nachrichtencenter im [Azure-Portal](https://portal.azure.com) und im [Microsoft 365 Admin Center](https://admin.microsoft.com) anzeigen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und Azure AD PowerShell wird diese Rolle als „Dienstsupportadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com), im [Microsoft 365 Admin Center](https://admin.microsoft.com) und im Intune-Portal lautet sie „Dienstadministrator“.

* **[SharePoint-Administrator:](#sharepoint-service-administrator)** Benutzer mit dieser Rolle besitzen globale Berechtigungen innerhalb von Microsoft SharePoint Online, wenn der Dienst verfügbar ist, und können alle Office 365-Gruppen erstellen und verwalten, Supporttickets verwalten und die Dienstintegrität überwachen. Weitere Informationen finden Sie unter [Informationen zu Administratorrollen von Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und Azure AD PowerShell wird diese Rolle als „SharePoint-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „SharePoint-Administrator“.

* **[Skype for Business-Administrator:](#lync-service-administrator)** Benutzer mit dieser Rolle besitzen globale Berechtigungen in Microsoft Skype for Business, wenn der Dienst vorhanden ist, sowie die Berechtigung zur Verwaltung von Skype-spezifischen Benutzerattributen in Azure Active Directory. Darüber hinaus bietet diese Rolle die Möglichkeit, Supporttickets zu verwalten und die Dienstintegrität zu überwachen, sowie auf Teams und Skype for Business Admin Center zuzugreifen. Das Konto muss auch für Teams lizenziert sein, andernfalls kann es keine PowerShell-Cmdlets von Teams ausführen. Weitere Informationen dazu finden Sie unter [Skype for Business Online-Administrator](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) und Informationen zur Teams-Lizenzierung unter [Add-On-Lizenzierung für Skype for Business und Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing).

  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und Azure AD PowerShell wird diese Rolle als „Lync-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com/) lautet sie „Skype for Business-Administrator“.

* **[Teams-Administrator:](#teams-service-administrator)** Benutzer mit dieser Rolle können alle Aspekte der Microsoft Teams-Workload über das Admin Center von Microsoft Teams und Skype for Business und die entsprechenden PowerShell-Module verwalten. Dazu zählen unter anderem alle Verwaltungstools im Zusammenhang mit Telefonie, Messaging, Besprechungen und den Teams selbst. Außerdem bietet diese Rolle die Möglichkeit, alle Office 365-Gruppen zu erstellen und zu verwalten, Supporttickets zu verwalten und die Dienstintegrität zu überwachen.
  > [!NOTE]
  > In der Microsoft Graph-API, der Azure AD Graph-API und Azure AD PowerShell wird diese Rolle als „Teams-Dienstadministrator“ bezeichnet. Im [Azure-Portal](https://portal.azure.com) lautet sie „Teams-Administrator“.

* **[Teams-Kommunikationsadministrator:](#teams-communications-administrator)** Benutzer mit dieser Rolle können Aspekte der Microsoft Teams-Workload im Zusammenhang mit Sprache und Telefonie verwalten. Dazu gehören die Verwaltungstools für die Telefonnummernzuweisung, Sprach- und Besprechungsrichtlinien und der uneingeschränkte Zugriff auf das Toolset zur Anrufanalyse.

* **[Teams-Kommunikationssupporttechniker:](#teams-communications-support-engineer)** Benutzer in dieser Rolle können Kommunikationsprobleme innerhalb von Microsoft Teams und Skype for Business mithilfe der Problembehandlungstools für Benutzeranrufe im Admin Center für Microsoft Teams und Skype for Business behandeln. Benutzer in dieser Rolle können vollständige Anrufdatensatzinformationen für alle Teilnehmer anzeigen. Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

* **[Teams-Kommunikationssupportspezialist:](#teams-communications-support-specialist)** Benutzer in dieser Rolle können Kommunikationsprobleme innerhalb von Microsoft Teams und Skype for Business mithilfe der Problembehandlungstools für Benutzeranrufe im Admin Center für Microsoft Teams und Skype for Business behandeln. Benutzer in dieser Rolle können Benutzerdetails im Anruf nur für den bestimmten Benutzer anzeigen, nach dem sie gesucht haben. Diese Rolle kann keine Supporttickets anzeigen, erstellen oder verwalten.

* **[Benutzeradministrator](#user-administrator)** : Benutzer mit dieser Rolle können Benutzer erstellen und mit einigen Einschränkungen (siehe unten) auch verwalten. Außerdem können sie Richtlinien für den Kennwortablauf aktualisieren. Außerdem können Benutzer mit dieser Rolle Gruppen erstellen und verwalten. Zudem haben sie die Möglichkeit, Benutzeransichten und Supporttickets zu verwalten sowie die Dienstintegrität zu überwachen.

  | | |
  | --- | --- |
  |Allgemeine Berechtigungen|<p>Erstellen von Benutzern und Gruppen</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Verwalten von Office-Supporttickets<p>Aktualisieren von Richtlinien für den Kennwortablauf|
  |<p>Für alle Benutzer einschließlich aller Administratoren</p>|<p>Verwalten von Lizenzen</p><p>Verwalten aller Benutzereigenschaften mit Ausnahme des Benutzerprinzipalnamens</p>
  |Nur für Benutzer, die keine Administratoren sind und keine der folgenden eingeschränkten Administratorrollen haben:<ul><li>Rolle „Verzeichnis lesen“<li>Gasteinladender<li>Helpdeskadministrator<li>Nachrichtencenter-Leser<li>Meldet Reader<li>Benutzeradministrator|<p>Löschen und Wiederherstellen</p><p>Deaktivieren und Aktivieren</p><p>Annullieren von Aktualisierungstoken</p><p>Verwalten aller Benutzereigenschaften einschließlich des Benutzerprinzipalnamens</p><p>Kennwort zurücksetzen</p><p>Aktualisieren von Geräteschlüsseln (FIDO)</p>
  
  <b>Wichtig</b>: Benutzer mit dieser Rolle können Kennwörter für Benutzer ändern, die Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen innerhalb und außerhalb von Azure Active Directory haben. Benutzer, die Kennwörter ändern können, können ggf. auch die Identität und die Berechtigungen des betreffenden Benutzers annehmen. Beispiel:
  * Besitzer von Anwendungsregistrierungen und Unternehmensanwendungen, die Anmeldeinformationen von Apps verwalten können, die sie besitzen. Diese Apps können über höhere Berechtigungen in Azure AD und in anderen Diensten verfügen, die Benutzeradministratoren nicht gewährt werden. So kann ein Benutzeradministrator die Identität eines Anwendungsbesitzers annehmen und dann die Identität einer privilegierten Anwendung durch Aktualisieren der Anmeldeinformationen für die Anwendung annehmen.
  * Besitzer von Azure-Abonnements, die ggf. auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure zugreifen können.
  * Besitzer von Sicherheitsgruppen und Office 365-Gruppen, die die Gruppenmitgliedschaft verwalten können. Diese Gruppen können Zugriff auf vertrauliche oder private Informationen bzw. kritische Konfigurationen in Azure AD und in anderen Diensten gewähren.
  * Administratoren in anderen Diensten außerhalb von Azure AD wie Exchange Online, Office Security and Compliance Center und Personalwesen.
  * Nichtadministratoren wie Führungskräfte, Rechtsberater und Mitarbeiter der Personalabteilung mit Zugriff auf vertrauliche oder private Informationen.

## <a name="role-permissions"></a>Rollenberechtigungen
In den folgenden Tabellen werden die spezifischen Berechtigungen der einzelnen Rollen in Azure Active Directory beschrieben. Einige Rollen können zusätzliche Berechtigungen in Microsoft-Diensten außerhalb von Azure Active Directory aufweisen.

### <a name="application-administrator"></a>Anwendungsadministrator
Kann alle Aspekte von App-Registrierungen und Enterprise-Apps erstellen und verwalten.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aktualisieren der applications.audience-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/authentication/update | Aktualisieren der applications.authentication-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/basic/update | Aktualisieren der Basiseigenschaften für Anwendungen in Azure Active Directory |
| microsoft.aad.directory/applications/create | Erstellen von Anwendungen in Azure Active Directory |
| microsoft.aad.directory/applications/credentials/update | Aktualisieren der applications.credentials-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/delete | Löschen von Anwendungen in Azure Active Directory |
| microsoft.aad.directory/applications/owners/update | Aktualisieren der applications.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/permissions/update | Aktualisieren der applications.permissions-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/policies/update | Aktualisieren der applications.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/create | Erstellen von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/read | Lesen von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/update | Aktualisieren von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/delete | Löschen von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/auditLogs/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Überwachungsprotokolle (auditLogs) in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Lesen der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aktualisieren der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/create | Erstellen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Löschen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Lesen der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aktualisieren der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Lesen der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren der servicePrincipals.appRoleAssignedTo-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aktualisieren der servicePrincipals.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/audience/update | Aktualisieren der servicePrincipals.audience-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aktualisieren der servicePrincipals.authentication-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualisieren der Basiseigenschaften für servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/create | Erstellen von servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aktualisieren der servicePrincipals.credentials-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/delete | Löschen von servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/owners/update | Aktualisieren der servicePrincipals.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aktualisieren der servicePrincipals.permissions-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualisieren der servicePrincipals.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/signInReports/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Anmeldeberichte (signInReports) in Azure Active Directory |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="application-developer"></a>Anwendungsentwickler
Erstellen von Anwendungsregistrierungen unabhängig von der Einstellung „Benutzer können Anwendungen registrieren“.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Erstellen von Anwendungen in Azure Active Directory Der Ersteller wird als erster Besitzer hinzugefügt, und das erstellte Objekt wird auf das Kontingent von 250 erstellten Objekten angerechnet, das für den Ersteller gilt. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Erstellen von appRoleAssignments in Azure Active Directory Der Ersteller wird als erster Besitzer hinzugefügt, und das erstellte Objekt wird auf das Kontingent von 250 erstellten Objekten angerechnet, das für den Ersteller gilt. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Erstellen von oAuth2PermissionGrants in Azure Active Directory Der Ersteller wird als erster Besitzer hinzugefügt, und das erstellte Objekt wird auf das Kontingent von 250 erstellten Objekten angerechnet, das für den Ersteller gilt. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Erstellen von servicePrincipals in Azure Active Directory Der Ersteller wird als erster Besitzer hinzugefügt, und das erstellte Objekt wird auf das Kontingent von 250 erstellten Objekten angerechnet, das für den Ersteller gilt. |

### <a name="authentication-administrator"></a>Authentifizierungsadministrator
Ist berechtigt, Informationen zur Authentifizierungsmethode für alle Benutzer ohne Administratorrechte anzuzeigen, festzulegen und zurückzusetzen.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Kennzeichnen sämtlicher Aktualisierungstoken für Benutzer in Azure Active Directory als ungültig. |
| microsoft.aad.directory/users/strongAuthentication/update | Aktualisieren der Eigenschaften der strengen Authentifizierung (z. B. MFA-Anmeldeinformationen). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="azure-information-protection-administrator"></a>Azure Information Protection-Administrator
Verwalten sämtlicher Aspekte des Azure Information Protection-Diensts.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Verwalten sämtlicher Aspekte von Azure Information Protection |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="b2c-user-flow-administrator"></a>B2C-Benutzerflowadministrator
Erstellen und Verwalten aller Aspekte von Benutzerflows

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Erstellen und Konfigurieren von Benutzerflows in Azure Active Directory B2C |

### <a name="b2c-user-flow-attribute-administrator"></a>B2C-Administrator für Benutzerflowattribute
Erstellen und Verwalten des Attributschemas für alle Benutzerabläufe

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Lesen und Konfigurieren von Benutzerattributen in Azure Active Directory B2C |

### <a name="b2c-ief-keyset-administrator"></a>B2C-IEF-Schlüsselsatzadministrator
Verwalten von Geheimnissen für Verbund und Verschlüsselung im Identity Experience Framework

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Lesen und Konfigurieren von Schlüsselsätzen in Azure Active Directory B2C |

### <a name="b2c-ief-policy-administrator"></a>B2C-IEF-Richtlinienadministrator
Erstellen und Verwalten von Vertrauensframework-Richtlinien im Identity Experience Framework

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Lesen und Konfigurieren benutzerdefinierter Richtlinien in Azure Active Directory B2C |

### <a name="billing-administrator"></a>Abrechnungsadministrator
Ausführen von allgemeinen Abrechnungsaufgaben wie der Aktualisierung der Zahlungsinformationen.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Aktualisieren der Basiseigenschaften für die Organisation in Azure Active Directory |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.commerce.billing/allEntities/allTasks | Verwalten sämtlicher Aspekte der Office 365-Abrechnung. |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="desktop-analytics-administrator"></a>Desktop Analytics-Administrator
Kann Desktop Analytics und die Dienste für Office-Anpassung und -Richtlinien verwalten. Bei Desktop Analytics umfasst dies die Möglichkeit, den Assetbestand anzuzeigen, Bereitstellungspläne zu erstellen sowie die Bereitstellung und den Integritätsstatus zu anzuzeigen. Beim Dienst für Office-Anpassung und -Richtlinien ermöglicht diese Rolle den Benutzern die Verwaltung von Office-Richtlinien.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Verwalten aller Aspekte von Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="cloud-application-administrator"></a>Cloudanwendungsadministrator
Erstellen und Verwalten sämtlicher Aspekte von App-Registrierungen und Enterprise-Apps außer App-Proxy.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aktualisieren der applications.audience-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/authentication/update | Aktualisieren der applications.authentication-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/basic/update | Aktualisieren der Basiseigenschaften für Anwendungen in Azure Active Directory |
| microsoft.aad.directory/applications/create | Erstellen von Anwendungen in Azure Active Directory |
| microsoft.aad.directory/applications/credentials/update | Aktualisieren der applications.credentials-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/delete | Löschen von Anwendungen in Azure Active Directory |
| microsoft.aad.directory/applications/owners/update | Aktualisieren der applications.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/permissions/update | Aktualisieren der applications.permissions-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/policies/update | Aktualisieren der applications.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/create | Erstellen von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/update | Aktualisieren von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/delete | Löschen von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/auditLogs/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Überwachungsprotokolle (auditLogs) in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/create | Erstellen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Lesen der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aktualisieren der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Löschen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Lesen der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aktualisieren der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Lesen der policies.applicationConfiguration-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren der servicePrincipals.appRoleAssignedTo-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aktualisieren der servicePrincipals.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/audience/update | Aktualisieren der servicePrincipals.audience-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aktualisieren der servicePrincipals.authentication-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualisieren der Basiseigenschaften für servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/create | Erstellen von servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aktualisieren der servicePrincipals.credentials-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/delete | Löschen von servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/owners/update | Aktualisieren der servicePrincipals.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aktualisieren der servicePrincipals.permissions-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualisieren der servicePrincipals.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/signInReports/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Anmeldeberichte (signInReports) in Azure Active Directory |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="cloud-device-administrator"></a>Cloudgeräteadministrator
Vollzugriff zum Verwalten von Geräten in Azure AD.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Überwachungsprotokolle (auditLogs) in Azure Active Directory |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lesen der devices.bitLockerRecoveryKeys-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/devices/delete | Löschen von Geräten in Azure Active Directory |
| microsoft.aad.directory/devices/disable | Deaktivieren von Geräten in Azure Active Directory |
| microsoft.aad.directory/devices/enable | Aktivieren von Geräten in Azure Active Directory |
| microsoft.aad.directory/signInReports/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Anmeldeberichte (signInReports) in Azure Active Directory |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |

### <a name="company-administrator"></a>Unternehmensadministrator
Verwalten sämtlicher Aspekte von Azure AD und Microsoft-Diensten, die Azure AD-Identitäten verwenden.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren von Standardeigenschaften in microsoft.aad.cloudAppSecurity |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Erstellen und Löschen von administrativeUnits und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/applications/allProperties/allTasks | Erstellen und Löschen von Anwendungen und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Erstellen und Löschen von appRoleAssignments und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/auditLogs/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Überwachungsprotokolle (auditLogs) in Azure Active Directory |
| microsoft.aad.directory/contacts/allProperties/allTasks | Erstellen und Löschen von Kontakten und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/contracts/allProperties/allTasks | Erstellen und Löschen von Verträgen und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/devices/allProperties/allTasks | Erstellen und Löschen von Geräten und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Erstellen und Löschen von directoryRoles und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Erstellen und Löschen von directoryRoleTemplates und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/domains/allProperties/allTasks | Erstellen und Löschen von Domänen und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/groups/allProperties/allTasks | Erstellen und Löschen von Gruppen und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Erstellen und Löschen von groupSettings und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Erstellen und Löschen von groupSettingTemplates und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Erstellen und Löschen von loginTenantBranding und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Erstellen und Löschen von oAuth2PermissionGrants und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/organization/allProperties/allTasks | Erstellen und Löschen der Organisationen und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/policies/allProperties/allTasks | Erstellen und Löschen von Richtlinien und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Erstellen und Löschen von roleAssignments und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Erstellen und Löschen von roleDefinitions und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Erstellen und Löschen von scopedRoleMemberships und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/serviceAction/activateService | Ausführen der Dienstaktion Activateservice in Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Ausführen der Dienstaktion DisableDirectoryFeature in Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Ausführen der Dienstaktion Enabledirectoryfeature in Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Ausführen der Dienstaktion Getavailableextentionproperties in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Erstellen und Löschen von servicePrincipals und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/signInReports/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Anmeldeberichte (signInReports) in Azure Active Directory |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Erstellen und Löschen von subscribedSkus und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directory/users/allProperties/allTasks | Erstellen und Löschen von Benutzern und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory |
| microsoft.aad.directorySync/allEntities/allTasks | Ausführen sämtlicher Aktionen in Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Erstellen und Löschen aller Ressourcen und Lesen und Aktualisieren von Standardeigenschaften in microsoft.identityProtection |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lesen aller Ressourcen in microsoft.aad.privilegedIdentityManagement |
| microsoft.azure.advancedThreatProtection/allEntities/read | Lesen aller Ressourcen in microsoft.azure.advancedThreatProtection |
| microsoft.azure.informationProtection/allEntities/allTasks | Verwalten sämtlicher Aspekte von Azure Information Protection |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.commerce.billing/allEntities/allTasks | Verwalten sämtlicher Aspekte der Office 365-Abrechnung. |
| microsoft.intune/allEntities/allTasks | Verwalten sämtlicher Aspekte von Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Verwalten sämtlicher Aspekte von Office 365 Compliance-Manager |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Verwalten aller Aspekte von Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Verwalten sämtlicher Aspekte von Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Verwalten sämtlicher Aspekte der Office 365 Kunden-Lockbox |
| microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten in microsoft.office365.messageCenter |
| microsoft.office365.messageCenter/securityMessages/read | Lesen von securityMessages in microsoft.office365.messageCenter |
| microsoft.office365.protectionCenter/allEntities/allTasks | Verwalten sämtlicher Aspekte von Office 365 Protection Center |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren von Standardeigenschaften in microsoft.office365.securityComplianceCenter |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.sharepoint/allEntities/allTasks | Erstellen und Löschen aller Ressourcen und Lesen und Aktualisieren von Standardeigenschaften in microsoft.office365.sharepoint |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Verwalten sämtlicher Aspekte von Skype for Business Online |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |
| microsoft.office365.usageReports/allEntities/read | Lesen von Office 365-Nutzungsberichten. |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Verwalten sämtlicher Aspekte von Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Verwalten sämtlicher Aspekte von Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Lesen aller Ressourcen in microsoft.windows.defenderAdvancedThreatProtection |

### <a name="compliance-administrator"></a>Complianceadministrator
Lesen und Verwalten der Konformitätskonfiguration und der zugehörigen Berichte in Azure AD und Office 365.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Verwalten sämtlicher Aspekte von Office 365 Compliance-Manager |
| microsoft.office365.exchange/allEntities/allTasks | Verwalten sämtlicher Aspekte von Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.sharepoint/allEntities/allTasks | Erstellen und Löschen aller Ressourcen und Lesen und Aktualisieren von Standardeigenschaften in microsoft.office365.sharepoint |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Verwalten sämtlicher Aspekte von Skype for Business Online |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="conditional-access-administrator"></a>Administrator für den bedingten Zugriff
Verwalten von Funktionen zum bedingten Zugriff.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Lesen der policies.conditionalAccess-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Aktualisieren der policies.conditionalAccess-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/conditionalAccess/create | Erstellen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/conditionalAccess/delete | Löschen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Lesen der policies.conditionalAccess-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Aktualisieren der policies.conditionalAccess-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Lesen der policies.conditionalAccess-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Aktualisieren der policies.conditionalAccess-Eigenschaft in Azure Active Directory |

### <a name="crm-service-administrator"></a>CRM-Dienstadministrator
Verwalten sämtlicher Aspekte des Produkts Dynamics 365.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Verwalten sämtlicher Aspekte von Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="customer-lockbox-access-approver"></a>Genehmigende Person für den LockBox-Kundenzugriff
Kann Microsoft-Supportanfragen zum Zugriff auf Benutzerorganisationsdaten genehmigen.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Verwalten sämtlicher Aspekte der Office 365 Kunden-Lockbox |

### <a name="device-administrators"></a>Geräteadministratoren
Benutzer, die dieser Rolle zugewiesen wurden, werden der lokalen Administratorgruppe auf in Azure AD eingebundenen Geräten hinzugefügt.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Lesen der Basiseigenschaften für groupSettings in Azure Active Directory |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Lesen der Basiseigenschaften für groupSettingTemplates in Azure Active Directory |

### <a name="directory-readers"></a>Rolle „Verzeichnis lesen“
Lesen von grundlegenden Verzeichnisinformationen. Die Rolle gewährt Zugriff auf Anwendungen und ist nicht für Benutzer vorgesehen.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Lesen der Basiseigenschaften für administrativeUnits in Azure Active Directory |
| microsoft.aad.directory/administrativeUnits/members/read | Lesen der administrativeUnits.members-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/basic/read | Lesen der Basiseigenschaften für Anwendungen in Azure Active Directory |
| microsoft.aad.directory/applications/owners/read | Lesen der applications.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/applications/policies/read | Lesen der applications.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/contacts/basic/read | Lesen der Basiseigenschaften für Kontakte in Azure Active Directory |
| microsoft.aad.directory/contacts/memberOf/read | Lesen der contacts.memberOf-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/contracts/basic/read | Lesen der Basiseigenschaften für Verträge in Azure Active Directory |
| microsoft.aad.directory/devices/basic/read | Lesen der Basiseigenschaften für Geräte in Azure Active Directory |
| microsoft.aad.directory/devices/memberOf/read | Lesen der devices.memberOf-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/devices/registeredOwners/read | Lesen der devices.registeredOwners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/devices/registeredUsers/read | Lesen der devices.registeredUsers-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/directoryRoles/basic/read | Lesen der Basiseigenschaften für directoryRoles in Azure Active Directory |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Lesen der directoryRoles.eligibleMembers-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/directoryRoles/members/read | Lesen der directoryRoles.members-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/domains/basic/read | Lesen der Basiseigenschaften für Domänen in Azure Active Directory |
| microsoft.aad.directory/groups/appRoleAssignments/read | Lesen der groups.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/basic/read | Lesen der Basiseigenschaften für Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/memberOf/read | Lesen der groups.memberOf-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/members/read | Lesen der groups.members-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/owners/read | Lesen der groups.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/settings/read | Lesen der groups.settings-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groupSettings/basic/read | Lesen der Basiseigenschaften für groupSettings in Azure Active Directory |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Lesen der Basiseigenschaften für groupSettingTemplates in Azure Active Directory |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Lesen der Basiseigenschaften für oAuth2PermissionGrants in Azure Active Directory |
| microsoft.aad.directory/organization/basic/read | Lesen der Basiseigenschaften für die Organisation in Azure Active Directory |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Lesen der organizations.trustedCAsForPasswordlessAuth-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/roleAssignments/basic/read | Lesen der Basiseigenschaften für roleAssignments in Azure Active Directory |
| microsoft.aad.directory/roleDefinitions/basic/read | Lesen der Basiseigenschaften für roleDefinitions in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Lesen der servicePrincipals.appRoleAssignedTo-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Lesen der servicePrincipals.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/basic/read | Lesen der Basiseigenschaften für servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Lesen der servicePrincipals.memberOf-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Lesen der servicePrincipals.oAuth2PermissionGrants-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Lesen der servicePrincipals.ownedObjects-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/owners/read | Lesen der servicePrincipals.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/policies/read | Lesen der servicePrincipals.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/subscribedSkus/basic/read | Lesen der Basiseigenschaften für subscribedSkus in Azure Active Directory |
| microsoft.aad.directory/users/appRoleAssignments/read | Lesen der users.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/basic/read | Lesen der Basiseigenschaften für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/directReports/read | Lesen der users.directReports-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/manager/read | Lesen der users.manager-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/memberOf/read | Lesen der users.memberOf-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Lesen der users.oAuth2PermissionGrants-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/ownedDevices/read | Lesen der users.ownedDevices-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/ownedObjects/read | Lesen der users.ownedObjects-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/registeredDevices/read | Lesen der users.registeredDevices-Eigenschaft in Azure Active Directory |

### <a name="directory-synchronization-accounts"></a>Konten zur Verzeichnissynchronisierung
Nur vom Azure AD Connect-Dienst verwendet.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Aktualisieren der organization.dirSync-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/create | Erstellen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/delete | Löschen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/basic/read | Lesen der Basiseigenschaften für Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/basic/update | Aktualisieren der Basiseigenschaften für Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/owners/read | Lesen der policies.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/owners/update | Aktualisieren der policies.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Lesen der policies.policiesAppliedTo-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/tenantDefault/update | Aktualisieren der policies.tenantDefault-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Lesen der servicePrincipals.appRoleAssignedTo-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren der servicePrincipals.appRoleAssignedTo-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Lesen der servicePrincipals.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aktualisieren der servicePrincipals.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/audience/update | Aktualisieren der servicePrincipals.audience-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aktualisieren der servicePrincipals.authentication-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/basic/read | Lesen der Basiseigenschaften für servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualisieren der Basiseigenschaften für servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/create | Erstellen von servicePrincipals in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aktualisieren der servicePrincipals.credentials-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Lesen der servicePrincipals.memberOf-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Lesen der servicePrincipals.oAuth2PermissionGrants-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/owners/read | Lesen der servicePrincipals.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/owners/update | Aktualisieren der servicePrincipals.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Lesen der servicePrincipals.ownedObjects-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aktualisieren der servicePrincipals.permissions-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/policies/read | Lesen der servicePrincipals.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualisieren der servicePrincipals.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directorySync/allEntities/allTasks | Ausführen sämtlicher Aktionen in Azure AD Connect. |

### <a name="directory-writers"></a>Verzeichnis schreiben
Lesen und Schreiben von grundlegenden Verzeichnisinformationen. Die Rolle gewährt Zugriff auf Anwendungen und ist nicht für Benutzer vorgesehen.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/groups/create | Erstellen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/createAsOwner | Erstellen von Gruppen in Azure Active Directory Der Ersteller wird als erster Besitzer hinzugefügt, und das erstellte Objekt wird auf das Kontingent von 250 erstellten Objekten angerechnet, das für den Ersteller gilt. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aktualisieren der groups.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/basic/update | Aktualisieren der Basiseigenschaften für Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/members/update | Aktualisieren der groups.members-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/owners/update | Aktualisieren der groups.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/settings/update | Aktualisieren der groups.settings-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groupSettings/basic/update | Aktualisieren der Basiseigenschaften für groupSettings in Azure Active Directory |
| microsoft.aad.directory/groupSettings/create | Erstellen der groupSettings in Azure Active Directory |
| microsoft.aad.directory/groupSettings/delete | Löschen von groupSettings in Azure Active Directory |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualisieren der users.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/assignLicense | Verwalten der Lizenzen für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/basic/update | Aktualisieren der Basiseigenschaften für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Kennzeichnen sämtlicher Aktualisierungstoken für Benutzer in Azure Active Directory als ungültig. |
| microsoft.aad.directory/users/manager/update | Aktualisieren der users.manager-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/userPrincipalName/update | Aktualisieren der users.userPrincipalName-Eigenschaft in Azure Active Directory |

### <a name="exchange-service-administrator"></a>Exchange-Dienstadministrator
Verwalten sämtlicher Aspekte des Produkts Exchange.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aktualisieren der groups.unified-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/unified/basic/update | Aktualisieren der Basiseigenschaften von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/create | Erstellen von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/delete | Löschen von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/members/update | Aktualisieren der Mitgliedschaft von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/owners/update | Aktualisieren des Besitzers von Office 365-Gruppen |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Verwalten sämtlicher Aspekte von Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="external-identity-provider-administrator"></a>Externer Identitätsanbieteradministrator
Konfigurieren von Identitätsanbietern für die Verwendung in einem direkten Verbund

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Lesen und Konfigurieren von Identitätsanbietern in Azure Active Directory B2C |

### <a name="guest-inviter"></a>Gasteinladender
Diese Rolle kann Gastbenutzer einladen, unabhängig von der Einstellung „Mitglieder können Gäste einladen“.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Lesen der users.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/basic/read | Lesen der Basiseigenschaften für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/directReports/read | Lesen der users.directReports-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/inviteGuest | Einladen von Gastbenutzern in Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Lesen der users.manager-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/memberOf/read | Lesen der users.memberOf-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Lesen der users.oAuth2PermissionGrants-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/ownedDevices/read | Lesen der users.ownedDevices-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/ownedObjects/read | Lesen der users.ownedObjects-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/registeredDevices/read | Lesen der users.registeredDevices-Eigenschaft in Azure Active Directory |

### <a name="helpdesk-administrator"></a>Helpdeskadministrator
Zurücksetzen von Kennwörtern für Nicht-Administratoren und Helpdeskadministratoren.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lesen der devices.bitLockerRecoveryKeys-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Kennzeichnen sämtlicher Aktualisierungstoken für Benutzer in Azure Active Directory als ungültig. |
| microsoft.aad.directory/users/password/update | Aktualisieren der Kennwörter für alle Benutzer in Azure Active Directory. Weitere Informationen finden Sie in der Onlinedokumentation. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="intune-service-administrator"></a>Intune-Dienstadministrator
Verwalten sämtlicher Aspekte des Produkts Intune.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aktualisieren der Basiseigenschaften für Kontakte in Azure Active Directory |
| microsoft.aad.directory/contacts/create | Erstellen von Kontakten in Azure Active Directory |
| microsoft.aad.directory/contacts/delete | Löschen von Kontakten in Azure Active Directory |
| microsoft.aad.directory/devices/basic/update | Aktualisieren der Basiseigenschaften für Geräte in Azure Active Directory |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lesen der devices.bitLockerRecoveryKeys-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/devices/create | Erstellen von Geräten in Azure Active Directory |
| microsoft.aad.directory/devices/delete | Löschen von Geräten in Azure Active Directory |
| microsoft.aad.directory/devices/registeredOwners/update | Aktualisieren der devices.registeredOwners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/devices/registeredUsers/update | Aktualisieren der devices.registeredUsers-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aktualisieren der groups.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/basic/update | Aktualisieren der Basiseigenschaften für Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/create | Erstellen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/createAsOwner | Erstellen von Gruppen in Azure Active Directory Der Ersteller wird als erster Besitzer hinzugefügt, und das erstellte Objekt wird auf das Kontingent von 250 erstellten Objekten angerechnet, das für den Ersteller gilt. |
| microsoft.aad.directory/groups/delete | Löschen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/hiddenMembers/read | Lesen der groups.hiddenMembers-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/members/update | Aktualisieren der groups.members-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/owners/update | Aktualisieren der groups.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/restore | Wiederherstellen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/settings/update | Aktualisieren der groups.settings-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualisieren der users.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/basic/update | Aktualisieren der Basiseigenschaften für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/manager/update | Aktualisieren der users.manager-Eigenschaft in Azure Active Directory |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.intune/allEntities/allTasks | Verwalten sämtlicher Aspekte von Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |

### <a name="kaizala-administrator"></a>Kaizala-Administrator
Kann Einstellungen für Microsoft Kaizala verwalten.  

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >  
  
| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen von Office 365 Admin Center. |

### <a name="license-administrator"></a>Lizenzadministrator
Kann Produktlizenzen für Benutzer und Gruppen verwalten.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Verwalten der Lizenzen für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/usageLocation/update | Aktualisieren der users.usageLocation-Eigenschaft in Azure Active Directory |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |

### <a name="lync-service-administrator"></a>Lync-Dienstadministrator
Verwalten sämtlicher Aspekte des Produkts Skype for Business.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Verwalten sämtlicher Aspekte von Skype for Business Online |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="message-center-privacy-reader"></a>Nachrichtencenter-Datenschutzleseberechtigter
Kann Nachrichtencenter-Beiträge, Nachrichten zum Datenschutz, Gruppen, Domänen und Abonnements lesen.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten in microsoft.office365.messageCenter |
| microsoft.office365.messageCenter/securityMessages/read | Lesen von securityMessages in microsoft.office365.messageCenter |

### <a name="message-center-reader"></a>Nachrichtencenter-Leser
Lesen von Nachrichten und Updates für die Organisation ausschließlich im Office 365-Nachrichtencenter. 

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten in microsoft.office365.messageCenter |

### <a name="partner-tier1-support"></a>Partnersupport der Ebene 1
Verwenden Sie diese Rolle nicht – sie ist nicht zur allgemeinen Verwendung vorgesehen.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aktualisieren der Basiseigenschaften für Kontakte in Azure Active Directory |
| microsoft.aad.directory/contacts/create | Erstellen von Kontakten in Azure Active Directory |
| microsoft.aad.directory/contacts/delete | Löschen von Kontakten in Azure Active Directory |
| microsoft.aad.directory/groups/create | Erstellen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/createAsOwner | Erstellen von Gruppen in Azure Active Directory Der Ersteller wird als erster Besitzer hinzugefügt, und das erstellte Objekt wird auf das Kontingent von 250 erstellten Objekten angerechnet, das für den Ersteller gilt. |
| microsoft.aad.directory/groups/members/update | Aktualisieren der groups.members-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/owners/update | Aktualisieren der groups.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualisieren der users.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/assignLicense | Verwalten der Lizenzen für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/basic/update | Aktualisieren der Basiseigenschaften für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/delete | Löschen von Benutzern in Azure Active Directory |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Kennzeichnen sämtlicher Aktualisierungstoken für Benutzer in Azure Active Directory als ungültig. |
| microsoft.aad.directory/users/manager/update | Aktualisieren der users.manager-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/password/update | Aktualisieren der Kennwörter für alle Benutzer in Azure Active Directory. Weitere Informationen finden Sie in der Onlinedokumentation. |
| microsoft.aad.directory/users/restore | Wiederherstellen gelöschter Benutzerkonten in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aktualisieren der users.userPrincipalName-Eigenschaft in Azure Active Directory |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="partner-tier2-support"></a>Partnersupport der Ebene 2
Verwenden Sie diese Rolle nicht – sie ist nicht zur allgemeinen Verwendung vorgesehen.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aktualisieren der Basiseigenschaften für Kontakte in Azure Active Directory |
| microsoft.aad.directory/contacts/create | Erstellen von Kontakten in Azure Active Directory |
| microsoft.aad.directory/contacts/delete | Löschen von Kontakten in Azure Active Directory |
| microsoft.aad.directory/domains/allTasks | Erstellen und Löschen von Domänen und Lesen und Aktualisieren der Standardeigenschaften in Azure Active Directory |
| microsoft.aad.directory/groups/create | Erstellen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/delete | Löschen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/members/update | Aktualisieren der groups.members-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/restore | Wiederherstellen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/organization/basic/update | Aktualisieren der Basiseigenschaften für die Organisation in Azure Active Directory |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualisieren der users.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/assignLicense | Verwalten der Lizenzen für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/basic/update | Aktualisieren der Basiseigenschaften für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/delete | Löschen von Benutzern in Azure Active Directory |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Kennzeichnen sämtlicher Aktualisierungstoken für Benutzer in Azure Active Directory als ungültig. |
| microsoft.aad.directory/users/manager/update | Aktualisieren der users.manager-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/password/update | Aktualisieren der Kennwörter für alle Benutzer in Azure Active Directory. Weitere Informationen finden Sie in der Onlinedokumentation. |
| microsoft.aad.directory/users/restore | Wiederherstellen gelöschter Benutzerkonten in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aktualisieren der users.userPrincipalName-Eigenschaft in Azure Active Directory |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="power-bi-service-administrator"></a>Power BI-Dienstadministrator
Verwalten sämtlicher Aspekte des Produkts Power BI.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.powerApps.powerBI/allEntities/allTasks | Verwalten sämtlicher Aspekte von Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="privileged-authentication-administrator"></a>Privilegierter Authentifizierungsadministrator
Darf Informationen zur Authentifizierungsmethode für alle Benutzer (mit und ohne Administratorrechte) anzeigen, festlegen und zurücksetzen.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Kennzeichnen sämtlicher Aktualisierungstoken für Benutzer in Azure Active Directory als ungültig. |
| microsoft.aad.directory/users/strongAuthentication/update | Aktualisieren der Eigenschaften der strengen Authentifizierung (z. B. MFA-Anmeldeinformationen). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="privileged-role-administrator"></a>Administrator für privilegierte Rollen
Dieser Administrator kann Rollenzuweisungen in Azure AD und alle Aspekte von Privileged Identity Management verwalten.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Erstellen und Löschen aller Ressourcen und Lesen und Aktualisieren von Standardeigenschaften in microsoft.aad.privilegedIdentityManagement |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Lesen und Konfigurieren der servicePrincipals.appRoleAssignedTo-Eigenschaft in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Lesen und Konfigurieren der servicePrincipals.oAuth2PermissionGrants-Eigenschaft in Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Erstellen und Verwalten von Verwaltungseinheiten (einschließlich Mitgliedern). |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Erstellen und Verwalten von Rollenzuweisungen. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Erstellen und Verwalten von Rollendefinitionen. |

### <a name="reports-reader"></a>Meldet Reader
Lesen von Anmeldungs- und Überwachungsberichten.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Überwachungsprotokolle (auditLogs) in Azure Active Directory |
| microsoft.aad.directory/signInReports/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Anmeldeberichte (signInReports) in Azure Active Directory |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.usageReports/allEntities/read | Lesen von Office 365-Nutzungsberichten. |

### <a name="search-administrator"></a>Suchadministrator
Kann alle Aspekte der Microsoft Search-Einstellungen erstellen und verwalten.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten in microsoft.office365.messageCenter |
| microsoft.office365.search/allEntities/allProperties/allTasks | Erstellen und Löschen aller Ressourcen sowie Lesen und Aktualisieren aller Eigenschaften in „microsoft.office365.search“. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |
| microsoft.office365.usageReports/allEntities/read | Lesen von Office 365-Nutzungsberichten. |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |

### <a name="search-editor"></a>Such-Editor
Kann redaktionelle Inhalte wie Lesezeichen, Fragen und Antworten, Standorte und Grundrisse erstellen und verwalten.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Lesen von Nachrichten in microsoft.office365.messageCenter |
| microsoft.office365.search/content/allProperties/allTasks | Erstellen und Löschen von Inhalten sowie Lesen und Aktualisieren aller Eigenschaften in „microsoft.office365.search“. |
| microsoft.office365.usageReports/allEntities/read | Lesen von Office 365-Nutzungsberichten. |

### <a name="security-administrator"></a>Sicherheitsadministrator
Dieser Administrator kann Sicherheitsinformationen und -berichte lesen und die Konfiguration in Azure AD und Office 365 verwalten.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Aktualisieren der applications.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/auditLogs/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Überwachungsprotokolle (auditLogs) in Azure Active Directory |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lesen der devices.bitLockerRecoveryKeys-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/basic/update | Aktualisieren der Basiseigenschaften für Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/create | Erstellen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/delete | Löschen von Richtlinien in Azure Active Directory |
| microsoft.aad.directory/policies/owners/update | Aktualisieren der policies.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/policies/tenantDefault/update | Aktualisieren der policies.tenantDefault-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualisieren der servicePrincipals.policies-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/signInReports/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Anmeldeberichte (signInReports) in Azure Active Directory |
| microsoft.aad.identityProtection/allEntities/read | Lesen aller Ressourcen in microsoft.aad.identityProtection |
| microsoft.aad.identityProtection/allEntities/update | Aktualisieren aller Ressourcen in microsoft.aad.identityProtection |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lesen aller Ressourcen in microsoft.aad.privilegedIdentityManagement |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Lesen sämtlicher Aspekte von Office 365 Protection Center. |
| microsoft.office365.protectionCenter/allEntities/update | Aktualisieren aller Ressourcen in microsoft.office365.protectionCenter |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |

### <a name="security-reader"></a>Sicherheitsleseberechtigter
Lesen von Sicherheitsinformationen und Berichten in Azure AD und Office 365.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Überwachungsprotokolle (auditLogs) in Azure Active Directory |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lesen der devices.bitLockerRecoveryKeys-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/signInReports/allProperties/read | Lesen aller Eigenschaften (einschließlich der privilegierten Eigenschaften) für Anmeldeberichte (signInReports) in Azure Active Directory |
| microsoft.aad.identityProtection/allEntities/read | Lesen aller Ressourcen in microsoft.aad.identityProtection |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lesen aller Ressourcen in microsoft.aad.privilegedIdentityManagement |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Lesen sämtlicher Aspekte von Office 365 Protection Center. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |

### <a name="service-support-administrator"></a>Dienstunterstützungsadministrator
Lesen von Service Health-Informationen und Verwalten von Supporttickets.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="sharepoint-service-administrator"></a>SharePoint-Dienstadministrator
Verwalten sämtlicher Aspekte des SharePoint-Diensts.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aktualisieren der groups.unified-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/unified/basic/update | Aktualisieren der Basiseigenschaften von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/create | Erstellen von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/delete | Löschen von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/members/update | Aktualisieren der Mitgliedschaft von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/owners/update | Aktualisieren des Besitzers von Office 365-Gruppen |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.sharepoint/allEntities/allTasks | Erstellen und Löschen aller Ressourcen und Lesen und Aktualisieren von Standardeigenschaften in microsoft.office365.sharepoint |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

### <a name="teams-communications-administrator"></a>Teams-Kommunikationsadministrator
Kann Anruf- und Besprechungsfunktionen im Microsoft Teams-Dienst verwalten.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |
| microsoft.office365.usageReports/allEntities/read | Lesen von Office 365-Nutzungsberichten. |

### <a name="teams-communications-support-engineer"></a>Teams-Kommunikationssupporttechniker
Kann Kommunikationsprobleme in Teams mithilfe von erweiterten Tools behandeln.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |

### <a name="teams-communications-support-specialist"></a>Teams-Kommunikationssupportspezialist
Kann Kommunikationsprobleme in Teams mithilfe von allgemeinen Tools behandeln.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |

### <a name="teams-service-administrator"></a>Teams-Dienstadministrator
Kann den Microsoft Teams-Dienst verwalten.

  > [!NOTE]
  > Diese Rolle verfügt über zusätzliche Berechtigungen außerhalb von Azure Active Directory. Weitere Informationen finden Sie in der Rollenbeschreibung oben.
  >
  >

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Lesen der groups.hiddenMembers-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aktualisieren der groups.unified-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/unified/basic/update | Aktualisieren der Basiseigenschaften von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/create | Erstellen von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/delete | Löschen von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/members/update | Aktualisieren der Mitgliedschaft von Office 365-Gruppen |
| microsoft.aad.directory/groups/unified/owners/update | Aktualisieren des Besitzers von Office 365-Gruppen |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |
| microsoft.office365.usageReports/allEntities/read | Lesen von Office 365-Nutzungsberichten. |

### <a name="user-administrator"></a>Benutzeradministrator
Dieser Administrator kann alle Aspekte von Benutzern und Gruppen verwalten, einschließlich der Kennwortzurücksetzung für eingeschränkte Administratoren.

| **Aktionen** | **Beschreibung** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Erstellen von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/delete | Löschen von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/appRoleAssignments/update | Aktualisieren von appRoleAssignments in Azure Active Directory |
| microsoft.aad.directory/contacts/basic/update | Aktualisieren der Basiseigenschaften für Kontakte in Azure Active Directory |
| microsoft.aad.directory/contacts/create | Erstellen von Kontakten in Azure Active Directory |
| microsoft.aad.directory/contacts/delete | Löschen von Kontakten in Azure Active Directory |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aktualisieren der groups.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/basic/update | Aktualisieren der Basiseigenschaften für Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/create | Erstellen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/createAsOwner | Erstellen von Gruppen in Azure Active Directory Der Ersteller wird als erster Besitzer hinzugefügt, und das erstellte Objekt wird auf das Kontingent von 250 erstellten Objekten angerechnet, das für den Ersteller gilt. |
| microsoft.aad.directory/groups/delete | Löschen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/hiddenMembers/read | Lesen der groups.hiddenMembers-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/members/update | Aktualisieren der groups.members-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/owners/update | Aktualisieren der groups.owners-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/groups/restore | Wiederherstellen von Gruppen in Azure Active Directory |
| microsoft.aad.directory/groups/settings/update | Aktualisieren der groups.settings-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualisieren der users.appRoleAssignments-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/assignLicense | Verwalten der Lizenzen für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/basic/update | Aktualisieren der Basiseigenschaften für Benutzer in Azure Active Directory |
| microsoft.aad.directory/users/create | Erstellen von Benutzern in Azure Active Directory |
| microsoft.aad.directory/users/delete | Löschen von Benutzern in Azure Active Directory |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Kennzeichnen sämtlicher Aktualisierungstoken für Benutzer in Azure Active Directory als ungültig. |
| microsoft.aad.directory/users/manager/update | Aktualisieren der users.manager-Eigenschaft in Azure Active Directory |
| microsoft.aad.directory/users/password/update | Aktualisieren der Kennwörter für alle Benutzer in Azure Active Directory. Weitere Informationen finden Sie in der Onlinedokumentation. |
| microsoft.aad.directory/users/restore | Wiederherstellen gelöschter Benutzerkonten in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aktualisieren der users.userPrincipalName-Eigenschaft in Azure Active Directory |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren von Azure Service Health |
| microsoft.azure.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Azure-Supporttickets |
| microsoft.office365.webPortal/allEntities/basic/read | Lesen der Basiseigenschaften für alle Ressourcen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lesen und Konfigurieren des Office 365-Dienststatus. |
| microsoft.office365.supportTickets/allEntities/allTasks | Erstellen und Verwalten von Office 365-Supporttickets. |

## <a name="role-template-ids"></a>Rollenvorlagen-IDs

Rollenvorlagen-IDs werden hauptsächlich von Graph-API- oder PowerShell-Benutzern verwendet.

Graph displayName | Anzeigename des Azure-Portals | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Anwendungsadministrator | Anwendungsadministrator | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Anwendungsentwickler | Anwendungsentwickler | CF1C38E5-3621-4004-A7CB-879624DCED7C
Authentifizierungsadministrator | Authentifizierungsadministrator | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure Information Protection-Administrator | Azure Information Protection-Administrator | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C-Benutzerflowadministrator | B2C-Benutzerflowadministrator | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C-Administrator für Benutzerflowattribute | B2C-Administrator für Benutzerflowattribute | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C-IEF-Schlüsselsatzadministrator | B2C-IEF-Schlüsselsatzadministrator | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C-IEF-Richtlinienadministrator | B2C-IEF-Richtlinienadministrator | 3edaf663-341e-4475-9f94-5c398ef6c070
Abrechnungsadministrator | Rechnungsadministrator | b0f54661-2d74-4c50-afa3-1ec803f12efe
Desktop Analytics-Administrator | Desktop Analytics-Administrator | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Cloudanwendungsadministrator | Cloudanwendungsadministrator | 158c047a-c907-4556-b7ef-446551a6b5f7
Cloudgeräteadministrator | Cloudgeräteadministrator | 7698a772-787b-4ac8-901f-60d6b08affd2
Unternehmensadministrator | Globaler Administrator | 62e90394-69f5-4237-9190-012177145e10
Complianceadministrator | Complianceadministrator | 17315797-102d-40b4-93e0-432062caca18
Administrator für den bedingten Zugriff | Administrator für den bedingten Zugriff | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM-Dienstadministrator | Dynamics 365-Administrator | 44367163-eba1-44c3-98af-f5787879f96a
Genehmigende Person für den LockBox-Kundenzugriff | Genehmigende Person für den Kunden-Lockbox-Zugriff | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Geräteadministratoren | Geräteadministratoren | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Geräteeinbindung | Geräteeinbindung | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Geräte-Manager | Geräte-Manager | 2b499bcd-da44-4968-8aec-78e1674fa64d
Gerätebenutzer | Gerätebenutzer | d405c6df-0af8-4e3b-95e4-4d06e542189e
Rolle „Verzeichnis lesen“ | Rolle „Verzeichnis lesen“ | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Konten zur Verzeichnissynchronisierung | Konten zur Verzeichnissynchronisierung | d29b2b05-8046-44ba-8758-1e26182fcf32
Verzeichnis schreiben | Verzeichnis schreiben | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange-Dienstadministrator | Exchange-Administrator | 29232cdf-9323-42fd-ade2-1d097af3e4de
Externer Identitätsanbieteradministrator | Externer Identitätsanbieteradministrator | be2f45a1-457d-42af-a067-6ec1fa63bc45
Gasteinladender | Gasteinladender | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Helpdeskadministrator | Kennwortadministrator | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Intune-Dienstadministrator | Intune-Administrator | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala-Administrator | Kaizala-Administrator | 74ef975b-6605-40af-a5d2-b9539d836353
Lizenzadministrator | Lizenzadministrator | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync-Dienstadministrator | Skype for Business-Administrator | 75941009-915a-4869-abe7-691bff18279e
Nachrichtencenter-Datenschutzleseberechtigter | Nachrichtencenter-Datenschutzleseberechtigter | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Nachrichtencenter-Leser | Nachrichtencenter-Leser | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Partnersupport der Ebene 1 | Partnersupport der Ebene 1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Partnersupport der Ebene 2 | Partnersupport der Ebene 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Power BI-Dienstadministrator | Power BI-Administrator | a9ea8996-122f-4c74-9520-8edcd192826c
Privilegierter Authentifizierungsadministrator | Privilegierter Authentifizierungsadministrator | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrator für privilegierte Rollen | Administrator für privilegierte Rollen | e8611ab8-c189-46e8-94e1-60213ab1f814
Meldet Reader | Berichtsleser | 4a5d8f65-41da-4de4-8968-e035b65339cf
Suchadministrator | Suchadministrator | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Such-Editor | Such-Editor | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Sicherheitsadministrator | Sicherheitsadministrator | 194ae4cb-b126-40b2-bd5b-6091b380977d
Sicherheitsleseberechtigter | Sicherheitsleseberechtigter | 5d6b6bb7-de71-4623-b4af-96380a352509
Dienstunterstützungsadministrator | Dienstadministrator | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint-Dienstadministrator | SharePoint-Administrator | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams-Kommunikationsadministrator | Teams-Kommunikationsadministrator | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams-Kommunikationssupporttechniker | Teams-Kommunikationssupporttechniker | f70938a0-fc10-4177-9e90-2178f8765737
Teams-Kommunikationssupportspezialist | Teams-Kommunikationssupportspezialist | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams-Dienstadministrator | Teams-Dienstadministrator | 69091246-20e8-4a56-aa4d-066075b2a7a8
Benutzer | Benutzer | a0b1b346-4d3e-4e8b-98f8-753987be4970
Benutzerkontoadministrator | Benutzeradministrator | fe930be7-5e62-47db-91af-98c3a49a38b1
Geräteeinbindung am Arbeitsplatz | Geräteeinbindung am Arbeitsplatz | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Veraltete Rollen

Die folgenden Rollen sollten nicht verwendet werden. Sie sind veraltet und werden aus Azure AD entfernt.

* Ad-hoc-Lizenzadministrator
* Geräteeinbindung
* Geräte-Manager
* Gerätebenutzer
* Benutzererstellung mit E-Mail-Überprüfung
* Postfachadministrator
* Geräteeinbindung am Arbeitsplatz

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Zuweisen eines Benutzers als Administrator eines Azure-Abonnements finden Sie unter [Verwalten des Zugriffs mithilfe der RBAC und des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).
* Informationen dazu, wie der Zugriff auf Ressourcen in Microsoft Azure gesteuert wird, finden Sie unter [Grundlegendes zum Zugriff auf Ressourcen in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Weitere Informationen zur Beziehung zwischen Azure Active Directory und Ihrem Azure-Abonnement finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
