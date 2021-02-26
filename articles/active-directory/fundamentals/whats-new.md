---
title: Neuigkeiten Versionshinweise – Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu den Neuerungen in Azure Active Directory, z. B. aktuelle Versionshinweise, bekannte Probleme, Fehlerbehebungen, veraltete Funktionen und anstehende Änderungen.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c835ddcee332499ef7853d0d2fa4fcdbcf8a1cf
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988864"
---
# <a name="whats-new-in-azure-active-directory"></a>Neuerungen in Azure Active Directory

>Lassen Sie sich über Updates auf dieser Seite benachrichtigen, indem Sie die URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` kopieren und in Ihren ![RSS-Symbol](./media/whats-new/feed-icon-16x16.png)-Feedreader einfügen.

Es werden fortlaufend Verbesserungen an Azure AD vorgenommen. Damit Sie bezüglich der neuesten Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern
- Veraltete Funktionen
- Pläne für Änderungen

Besuchen Sie regelmäßig diese Seite. Diese wird monatlich aktualisiert. Wenn Sie nach Elementen suchen, die älter als sechs Monate sind, können Sie sie im [Archiv zu den Neuerungen in Azure Active Directory](whats-new-archive.md) finden.

---
## <a name="january-2021"></a>Januar 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Das Feld „Geheimes Token“ wird beim Konfigurieren der Bereitstellung zum Pflichtfeld

**Typ:** Plan für Änderung  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Identity Lifecycle Management

Bisher konnte das Feld „Geheimes Token“ beim Einrichten der Bereitstellung für eine benutzerdefinierte Anwendung/BYOA leer bleiben. Diese Funktion war ausschließlich für Testzwecke vorgesehen. Wir werden die Benutzeroberfläche aktualisieren und das Feld zum Pflichtfeld machen. 

Kunden können diese Anforderung zu Testzwecken umgehen, indem sie in der Browser-URL ein Featureflag verwenden. [Weitere Informationen](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery)
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-firstline-workers-at-scale"></a>Öffentliche Vorschau: Skalierbares Anpassen und Konfigurieren von gemeinsam genutzten Android-Geräten für Mitarbeiter in Service und Produktion

**Typ:** Neues Feature  
**Dienstkategorie:** Geräteregistrierung und -verwaltung  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Das Azure AD- und das Microsoft Endpoint Manager-Team haben sich zusammengetan, um das Anpassen, Skalieren und Sichern der Geräte Ihrer Mitarbeiter in Service und Produktion zu ermöglichen.

Mit den folgenden Vorschaufunktionen können Sie folgende Aufgaben ausführen:
- Skalierbares Bereitstellen von gemeinsam genutzten Android-Geräten mit Microsoft Endpoint Manager
- Sicherer Zugriff für Schichtarbeiter mithilfe des gerätebasierten bedingten Zugriffs
- Anpassen der Anmeldeumgebung für Schichtarbeiter mit verwaltetem Startbildschirm

Weitere Informationen finden Sie unter [Skalierbares Anpassen und Konfigurieren von gemeinsam genutzten Android-Geräten für Mitarbeiter in Service und Produktion](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Öffentliche Vorschau: Bereitstellungsprotokolle können jetzt als CSV-oder JSON-Datei heruntergeladen werden

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Identity Lifecycle Management

Kunden können die Bereitstellungsprotokolle über die Benutzeroberfläche und die Graph-API als CSV- oder JSON-Datei herunterladen. Weitere Informationen finden Sie unter [Bereitstellungsberichte im Azure Active Directory-Portal](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Öffentliche Vorschau: Zuweisen von Cloudgruppen zu benutzerdefinierten Azure AD-Rollen und zu bereichsbezogenen Rollen für Verwaltungseinheiten

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
Kunden können eine Cloudgruppe zu benutzerdefinierten Azure AD-Rollen oder zu bereichsbezogenen Rollen für Verwaltungseinheiten zuweisen. Informationen zum Verwenden dieser Funktion finden Sie unter [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen in Azure Active Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Allgemeine Verfügbarkeit: Azure AD Connect-Cloudsynchronisierung (bisher als Cloudbereitstellung bezeichnet)

**Typ:** Neues Feature  
**Dienstkategorie:** Azure AD Connect-Cloudsynchronisierung  
**Produktfunktion:** Identity Lifecycle Management
 
Die Azure AD Connect-Cloudsynchronisierung ist jetzt für alle Kunden allgemein verfügbar.

Die Azure AD Connect-Cloud verlagert die aufwändige Arbeit der Transformationslogik in die Cloud und reduziert Ihren lokalen Aufwand. Außerdem sind mehrere einfache Agent-Bereitstellungen für eine höhere Synchronisierungsverfügbarkeit enthalten. [Weitere Informationen](https://aka.ms/cloudsyncGA)
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Allgemeine Verfügbarkeit: Integrierte Rollen „Administrator für Angriffssimulation“ und „Autor der Angriffsnutzdaten“

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
In der rollenbasierten Zugriffssteuerung gibt es zwei neue Rollen, die Benutzern zugewiesen werden können: „Administrator für Angriffssimulation“ und „Autor der Angriffsnutzdaten“. 

Benutzer mit der Rolle [Administrator für Angriffssimulation](../roles/permissions-reference.md#attack-simulation-administrator) haben Zugriff auf alle Simulationen im Mandanten und können folgende Aktionen ausführen:
- Erstellen und Verwalten aller Aspekte von Angriffssimulationen
- Starten/Planen einer Simulation
-  Überprüfen der Simulationsergebnisse 

Benutzer mit der Rolle [Autor der Angriffsnutzdaten](../roles/permissions-reference.md#attack-payload-author) können Angriffsnutzdaten erstellen, aber nicht real starten oder planen. Die Angriffsnutzdaten stehen dann allen Administratoren im Mandanten zur Verfügung und können von diesen zum Erstellen einer Simulation verwendet werden.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Allgemeine Verfügbarkeit: Integrierte Rolle „Leseberechtigter für Berichte mit Nutzungszusammenfassung“

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
Benutzer mit der Rolle „Leseberechtigter für Berichte mit Nutzungszusammenfassung“ können zur Nutzungs- und Produktivitätsbewertung im Microsoft 365 Admin Center auf aggregierte Daten und zugehörige Erkenntnisse auf Mandantenebene zugreifen. Sie können jedoch nicht auf Details oder Erkenntnisse auf Benutzerebene zugreifen. 

Im Microsoft 365 Admin Center für die beiden Berichte unterscheiden wir zwischen aggregierten Daten auf Mandantenebene und Details auf Benutzerebene. Diese Rolle bietet eine zusätzliche Sicherheitsebene für die personenbezogenen Daten einzelner Benutzer. [Weitere Informationen](../roles/permissions-reference.md#usage-summary-reports-reader)

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Allgemeine Verfügbarkeit: Gewährungstyp „App-Schutzrichtlinie erforderlich“ beim bedingten Zugriff von Azure AD

**Typ:** Neue Funktion  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Beim bedingten Zugriff von Azure AD ist jetzt der Gewährungstyp „App-Schutzrichtlinie erforderlich“ allgemein verfügbar. 

Die Richtlinie ermöglicht die folgenden Funktionen:
- Zugriff nur bei Verwendung einer mobilen Anwendung, die den Intune-App-Schutz unterstützt
- Zugriff nur für Benutzer, die eine Intune-App-Schutzrichtlinie an die mobile Anwendung übermittelt haben

Weitere Informationen zum Einrichten einer Richtlinie für bedingten Zugriff für den App-Schutz finden Sie [hier](../conditional-access/app-protection-based-conditional-access.md).
 
---

### <a name="general-availability---email-one-time-passcode"></a>Allgemeine Verfügbarkeit: Einmalkennung per E-Mail

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Mit der Einmalkennung per E-Mail können Unternehmen auf der ganzen Welt mit anderen Personen zusammenarbeiten, indem sie ihnen einen Link oder eine Einladung per E-Mail senden. Eingeladene Benutzer können anhand der Einmalkennung, die an ihre E-Mail-Adresse gesendet wurde, ihre Identität bestätigen und auf die Ressourcen ihres Partners zugreifen. [Weitere Informationen](../external-identities/one-time-passcode.md) 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog: Januar 2021

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Weitere Informationen finden Sie unter [Was versteht man unter der automatischen Bereitstellung von SaaS-App-Benutzern in Azure AD?](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar: Januar 2021

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten

Im Januar 2021 haben wir die folgenden 29 neuen Anwendungen mit Verbundunterstützung in unseren Anwendungskatalog aufgenommen:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenue Formation](https://formation.bienvenue.pro/login), [AIDA Healthcare SSO](https://aidaforparents.com/login/organizations), [International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md), [Notion](../saas-apps/notion-tutorial.md), [Syndio](../saas-apps/syndio-tutorial.md), [Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md), [Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data](https://www.ecolab.com/), [Vacancy Filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), [Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md), [Illusive Networks](../saas-apps/illusive-networks-tutorial.md), [Proware](../saas-apps/proware-tutorial.md), [Splan Visitor](../saas-apps/splan-visitor-tutorial.md), [Aruba User Experience Insight](../saas-apps/aruba-user-experience-insight-tutorial.md), [Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md), [Perimeter 81](../saas-apps/perimeter-81-tutorial.md), [Burp Suite Enterprise Edition](https://docs.microsoft.com/azure/active-directory/saas-apps/burp-suite-enterprise-edition-tutorial)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial.

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest. 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Öffentliche Vorschau: Mitglied der zweiten Führungsebene kann als alternative genehmigende Person festgelegt werden

**Typ:** Geändertes Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
In der Berechtigungsverwaltung steht jetzt bei der Auswahl von genehmigenden Personen eine zusätzliche Option zur Verfügung. Wenn Sie als erste genehmigende Person „Vorgesetzter als genehmigende Person“ auswählen, steht im Feld „Alternative genehmigende Person“ zusätzlich die Option „Mitglied der zweiten Führungsebene als alternative genehmigende Person“ zur Verfügung. Wenn Sie diese Option auswählen, müssen Sie eine stellvertretende genehmigende Person hinzufügen, an die die Anforderung weitergeleitet wird, wenn das System das Mitglied der zweiten Führungsebene nicht finden kann. [Weitere Informationen](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Allgemeine Verfügbarkeit: Navigieren zu Teams direkt vom Portal „Mein Zugriff“

**Typ:** Geändertes Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
Jetzt können Sie Teams direkt vom Portal „Mein Zugriff“ starten. 

Melden Sie sich hierzu bei „Mein Zugriff“ (https://myaccess.microsoft.com/) an, navigieren Sie zu „Zugriffspakete“, und wechseln Sie dann zur Registerkarte „Aktiv“, um alle Zugriffspakete anzuzeigen, auf die Sie bereits zugreifen können. Wenn Sie das ausgewählte Zugriffspaket erweitern und auf Teams zeigen, können Sie das Programm starten, indem Sie auf die Schaltfläche „Öffnen“ klicken. [Weitere Informationen](../governance/entitlement-management-request-access.md)
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Verbesserte Protokollierung und Benutzeraufforderungen für riskante Gastbenutzer

**Typ:** Geändertes Feature  
**Dienstkategorie:** Schutz der Identität (Identity Protection)  
**Produktfunktion:** Identitätssicherheit und -schutz
 

Protokollierung und Benutzeraufforderungen für riskante Gastbenutzer wurden aktualisiert. Weitere Informationen finden Sie unter [Identity Protection und B2B-Benutzer](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>Dezember 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Public Preview: Integrierte Azure AD B2C-Richtlinie für Telefonregistrierung und -anmeldung

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
Mit der integrierten B2C-Richtlinie für Telefonregistrierung und -anmeldung können IT-Administratoren und Entwickler einer Organisation ihren Endbenutzern ermöglichen, sich in Benutzerflows mithilfe einer Telefonnummer anzumelden und zu registrieren. Weitere Informationen finden Sie unter [Einrichten der telefonischen Registrierung und Anmeldung für Benutzerflows (Vorschau)](../../active-directory-b2c/phone-authentication-user-flows.md).

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Allgemeine Verfügbarkeit: Sicherheitsstandards jetzt standardmäßig für alle Mandanten aktiviert

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Zum Schutz der Benutzerkonten werden Sicherheitsstandards jetzt für alle Mandanten automatisch aktiviert, die ab dem 12. November 2020 erstellt wurden. Die Sicherheitsstandards erzwingen unter anderem die folgenden Richtlinien:
- Alle Benutzer und Administratoren müssen sich mithilfe der Microsoft Authenticator-App für die MFA registrieren.
- Kritische Administratorrollen müssen die MFA bei jeder Anmeldung durchführen. Alle anderen Benutzer werden bei Bedarf zur MFA aufgefordert. 
- Die Legacyauthentifizierung wird mandantenweit blockiert. 

Weitere Informationen finden Sie unter [Was sind Sicherheitsstandards?](../fundamentals/concept-fundamentals-security-defaults.md).

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Allgemeine Verfügbarkeit: Gruppen mit bis zu 250.000 Mitgliedern in Azure AD Connect

**Typ:** Geändertes Feature  
**Dienstkategorie**: AD Connect  
**Produktfunktion:** Identity Lifecycle Management
 
Microsoft hat einen neuen Endpunkt (API) für Azure AD Connect bereitgestellt, mit dem die Leistung der Synchronisierungsdienstvorgänge für Azure Active Directory verbessert wird. Wenn Sie den neuen [V2-Endpunkt](../hybrid/how-to-connect-sync-endpoint-api-v2.md) verwenden, profitieren Sie beim Exportieren aus und Importieren in Azure AD von erheblichen Leistungssteigerungen. Dieser neue Endpunkt unterstützt die folgenden Szenarios:

- Synchronisieren von Gruppen mit bis zu 250.000 Mitgliedern
- Leistungssteigerungen beim Exportieren aus und Importieren in Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Allgemeine Verfügbarkeit: Berechtigungsverwaltung für Mandanten in der Azure China-Cloud verfügbar

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 

Der Funktionsumfang der Berechtigungsverwaltung ist jetzt für alle Mandanten in der Azure China-Cloud verfügbar. Weitere Informationen finden Sie in der [Dokumentation zur Identitätsgovernance](https://docs.azure.cn/zh-cn/active-directory/governance/).

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog (Dezember 2020)

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten

Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Bizagi Studio für die Automatisierung digitaler Prozesse](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar (Dezember 2020)

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Dezember 2020 haben wir die folgenden 18 neuen Anwendungen mit Verbundunterstützung in den App-Katalog aufgenommen:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold), [Guider](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [Pims 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [vonage](../saas-apps/vonage-tutorial.md), [Count Me In – Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md), [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial.

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest.

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Navigieren zu Teams direkt über das Portal „Mein Zugriff“

**Typ:** Geänderte Funktion  
**Dienstkategorie:** Benutzerzugriffsverwaltung **Produktfunktion:** Berechtigungsverwaltung

Jetzt können Sie Teams direkt über das Portal „Mein Zugriff“ starten. Melden Sie sich hierzu bei [Mein Zugriff](https://myaccess.microsoft.com/) an, navigieren Sie zu **Zugriffspakete**, und wechseln Sie dann zur Registerkarte **Aktiv**, um alle Zugriffspakete anzuzeigen, auf die Sie bereits zugreifen können. Wenn Sie das Zugriffspaket erweitern und auf Teams zeigen, können Sie das Programm starten, indem Sie auf die Schaltfläche **Öffnen** klicken. 

Weitere Informationen zum Portal „Mein Zugriff“ finden Sie unter [Anfordern des Zugriffs auf ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Öffentliche Vorschau: Mitglied der zweiten Führungsebene kann als alternative genehmigende Person festgelegt werden

**Typ:** Geändertes Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung

In der Berechtigungsverwaltung ist jetzt im Genehmigungsprozess eine zusätzliche Option verfügbar. Wenn Sie als erste genehmigende Person „Vorgesetzter als genehmigende Person“ auswählen, steht im Feld „Alternative genehmigende Person“ zusätzlich die Option „Mitglied der zweiten Führungsebene als alternative genehmigende Person“ zur Verfügung. Wenn Sie diese Option auswählen, müssen Sie eine stellvertretende genehmigende Person hinzufügen, an die die Anforderung weitergeleitet wird, wenn das System das Mitglied der zweiten Führungsebene nicht finden kann.

Weitere Informationen finden Sie unter [Ändern der Genehmigungseinstellungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>November 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Einstellung von TLS 1.0, TLS 1.1 und 3DES in Azure Active Directory

**Typ:** Plan für Änderung  
**Dienstkategorie:** Alle Azure AD-Anwendungen  
**Produktfunktion:** Standards

Die folgenden Protokolle werden zum 30. Juni 2021 weltweit in allen Azure Active Directory-Regionen als veraltet gekennzeichnet:

- TLS 1.0
- TLS 1.1
- 3DES-Verschlüsselungssammlung (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Betroffene Umgebungen:
- Kommerzielle Azure-Cloud
- Office 365 GCC und WW

Zugehörige Ankündigung: Bei allen Client-Server- und Browser-Server-Kombinationen sollten TLS 1.2 und moderne Verschlüsselungssammlungen verwendet werden, um eine sichere Verbindung mit Azure Active Directory für Azure-, Office 365- und Microsoft 365-Dienste zu gewährleisten. Diese Änderung steht im Zusammenhang mit der Ankündigung: [Einstellung von TLS 1.0, TLS 1.1 und 3DES in Azure Active Directory in der US Gov-Cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – November 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten

Im November 2020 haben wir die folgenden 52 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Travel & Expense Management](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [Itslearning File Picker](https://pmteam.itslearning.com/), [Crises Control](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum – Study Island](https://app.studyisland.com/cfw/login/), [Virtual Risk Manager](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview – Recruit](https://recruit.talview.com/login), Real Time Translator, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [En-trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [EVA Check-in](https://my.evacheckin.com/organization), [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [Coupa Risk Assess](../saas-apps/coupa-risk-assess-tutorial.md), [Lucid (Alle Produkte)](../saas-apps/lucid-tutorial.md), [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [Freight Measure](https://www.gpcsl.com/freight.html), [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), [Nature Research](../saas-apps/nature-research-tutorial.md), [Play Digital Signage](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Sunrise Software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [Mentor® by eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial.

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest.

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Public Preview: benutzerdefinierte Rollen für Unternehmens-Apps

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
 [Benutzerdefinierte RBAC-Rollen für die delegierte Verwaltung von Unternehmensanwendungen](../roles/custom-available-permissions.md) befinden sich jetzt in der öffentlichen Vorschau. Diese neuen Berechtigungen basieren auf den benutzerdefinierten Rollen für die Verwaltung von App-Registrierungen, die eine präzisere Steuerung des Zugriffs Ihrer Administratoren ermöglichen. Es werden in Zukunft noch weitere Berechtigungen zum Delegieren der Verwaltung von Azure AD veröffentlicht.

Einige häufige Delegierungsszenarien:
- Zuweisen von Benutzern und Gruppen, die auf SAML-basierte SSO-Anwendungen zugreifen können
- Erstellen von Azure AD-Kataloganwendungen
- Aktualisieren und Lesen grundlegender SAML-Konfigurationen für SAML-basierte SSO-Anwendungen
- Verwalten von Signaturzertifikaten für SAML-basierte SSO-Anwendungen
- Aktualisieren der E-Mail-Adressen für Benachrichtigungen bei ablaufenden Anmeldezertifikaten für SAML-basierte SSO-Anwendungen
- Aktualisieren der SAML-Tokensignatur und des Anmeldealgorithmus für SAML-basierte SSO-Anwendungen
- Erstellen, Löschen und Aktualisieren von Benutzerattributen und -ansprüchen für SAML-basierte SSO-Anwendungen
- Aktivieren, Deaktivieren und Neustarten von Bereitstellungsaufträgen
- Aktualisieren von Attributzuordnungen
- Lesen von Bereitstellungseinstellungen, die dem Objekt zugeordnet sind
- Lesen von Bereitstellungseinstellungen, die Ihrem Dienstprinzipal zugeordnet sind
- Autorisieren des Anwendungszugriffs für die Bereitstellung

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Öffentliche Vorschau: Vom Azure AD-Anwendungsproxy wird der Zugriff auf Anwendungen, die Header für die Authentifizierung nutzen, per einmaligem Anmelden nativ unterstützt

**Typ:** Neue Funktion  
**Dienstkategorie:** Anwendungsproxy  
**Produktfunktion:** Zugriffssteuerung
 
Vom Azure AD-Anwendungsproxy (Azure Active Directory) wird der Zugriff auf Anwendungen, die Header für die Authentifizierung nutzen, per einmaligem Anmelden nativ unterstützt. Sie können Headerwerte konfigurieren, die für Ihre Anwendung in Azure AD benötigt werden. Die Headerwerte werden über den Anwendungsproxy an die Anwendung gesendet. Weitere Informationen finden Sie unter [Headerbasiertes einmaliges Anmelden für lokale Apps mit dem Azure AD-Anwendungsproxy](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md).
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Allgemeine Verfügbarkeit: Telefonregistrierung und -anmeldung mit benutzerdefinierten Richtlinien in Azure AD B2C

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C

Mit der Telefonregistrierung und -anmeldung können Entwickler und Unternehmen ihren Kunden die Registrierung und Anmeldung mit einem Einmalkennwort ermöglichen, das per SMS an die Telefonnummer des Benutzers gesendet wird. Mit dieser Funktion kann der Kunden auch seine Telefonnummer ändern, falls er keinen Zugriff auf das Telefon mehr hat. Dank der Leistungsfähigkeit benutzerdefinierter Richtlinien können Entwickler und Unternehmen ihre Marke durch Seitenanpassung kommunizieren. Weitere Informationen finden Sie unter [Einrichten von Telefonregistrierung und -anmeldung mit benutzerdefinierten Richtlinien in Azure AD B2C](../../active-directory-b2c/phone-authentication.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – November 2020

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic-Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Weitere Informationen finden Sie unter [Automatisieren der Bereitstellung von Benutzern für SaaS-Anwendungen mit Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Public Preview: E-Mail-Anmeldung mit Proxyadressen ist jetzt über den gestaffelten Rollout möglich

**Typ:** Neues Feature  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
Mandantenadministratoren können jetzt den gestaffelten Rollout zum Bereitstellen einer E-Mail-Anmeldung mit Proxyadressen für bestimmte Azure AD-Gruppen verwenden. Dies ist nützlich, um das Feature zu testen, bevor es über die Richtlinie für die Startbereichsermittlung im gesamten Mandanten bereitgestellt wird. Anweisungen zum Bereitstellen der E-Mail-Anmeldung mit Proxyadressen über den gestaffelten Rollout finden Sie in der [Dokumentation](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Eingeschränkte Vorschau: Anmeldediagnose

**Typ:** Neues Feature  
**Dienstkategorie:** Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 
Mit dem ersten Vorschaurelease der Anmeldediagnose können Administratoren jetzt Benutzeranmeldungen überprüfen. Administratoren erhalten kontextbezogene, spezifische und relevante Details sowie Leitfäden dazu, was bei einer Anmeldung geschehen ist und wie eventuelle Probleme behoben werden können. Die Diagnose ist sowohl auf Azure AD-Ebene als auch auf den Blättern für Diagnose und Lösung beim bedingten Zugriff verfügbar. Die in diesem Release behandelten Diagnoseszenarien betreffen den bedingten Zugriff, die mehrstufige Authentifizierung und erfolgreiche Anmeldungen.

Weitere Informationen finden Sie unter [Welche Funktion hat die Anmeldediagnose in Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Verbessert: ungewöhnliche Anmeldeeigenschaften

**Typ:** Geändertes Feature  
**Dienstkategorie:** Schutz der Identität (Identity Protection)  
**Produktfunktion:** Identitätssicherheit und -schutz

  Die Erkennung ungewöhnlicher Anmeldeeigenschaften wurde aktualisiert. Kunden bemerken möglicherweise eine höhere Anzahl von Erkennungen ungewöhnlicher Anmeldeeigenschaften mit einem hohen Risiko. Weitere Informationen finden Sie unter [Was bedeutet Risiko?](../identity-protection/concept-identity-protection-risks.md).
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Public Preview: Aktualisierung des Cloudbereitstellungs-Agents jetzt verfügbar (Version: 1.1.281.0)

**Typ:** Geändertes Feature  
**Dienstkategorie:** Azure AD-Cloudbereitstellung  
**Produktfunktion:** Identity Lifecycle Management
 
Der Cloudbereitstellungs-Agent wurde als Public Preview veröffentlicht und ist jetzt über das Portal verfügbar. Dieses Release enthält mehrere Verbesserungen, einschließlich der Unterstützung von gMSAs für Ihre Domänen, die eine höhere Sicherheit, verbesserte Zyklen bei der anfänglichen Synchronisierung und Unterstützung für große Gruppen bieten. Weitere Informationen finden Sie in der [Versionsgeschichte](../app-provisioning/provisioning-agent-release-version-history.md) zum Release. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Endpunkt für BitLocker-Wiederherstellungsschlüssel-API jetzt unter „/informationProtection“

**Typ:** Geändertes Feature  
**Dienstkategorie:** Gerätezugriffsverwaltung  
**Produktfunktion:** Lebenszyklusverwaltung für Geräte
 
Bisher konnten Sie BitLocker-Schlüssel über den Endpunkt „/bitlocker“ wiederherstellen. Dieser Endpunkt wird zukünftig als veraltet gekennzeichnet. Deshalb sollten Kunden mit der Nutzung der API beginnen, die jetzt unter „/informationProtection“ bereitgestellt wird. 

Unter [BitLocker-Wiederherstellungs-API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) finden Sie Updates an der Dokumentation zu diesen Änderungen.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Allgemeine Verfügbarkeit: Unterstützung für Anwendungsproxy beim HTML5-Webclient für Remotedesktopdienste

**Typ:** Geändertes Feature  
**Dienstkategorie:** Anwendungsproxy  
**Produktfunktion:** Zugriffssteuerung
 
Die Unterstützung für den Azure AD-Anwendungsproxy beim Webclient für Remotedesktopdienste (RDS) ist jetzt allgemein verfügbar. Der Webclient für Remotedesktopdienste ermöglicht Benutzern den Zugriff auf die Remotedesktopinfrastruktur über HTML5-fähige Browser wie Microsoft Edge, Internet Explorer 11, Google Chrome usw. Benutzer können von überall mit Remote-Apps oder -desktops interagieren, wie sie es mit einem lokalen Gerät tun würden. 

Mithilfe des Azure AD-Anwendungsproxys können Sie die Sicherheit Ihrer RDS-Bereitstellung erhöhen, indem Sie die Vorauthentifizierung und die Richtlinien für bedingten Zugriff für alle Arten von Rich Client-Apps erzwingen. Weitere Informationen finden Sie unter [Veröffentlichen des Remotedesktops per Azure AD-Anwendungsproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Neu: verbesserter Dienst für dynamische Gruppen jetzt in der Public Preview

**Typ:** Geändertes Feature  
**Dienstkategorie**: Gruppenverwaltung  
**Produktfunktion:** Kollaboration
 
Der erweiterte Dienst für dynamische Gruppen befindet sich jetzt in der Public Preview. Neue Kunden, die in ihren Mandanten dynamische Gruppen erstellen, verwenden den neuen Dienst. Die Zeit für das Erstellen einer dynamischen Gruppe ist proportional zur Größe der erstellten Gruppe und nicht zur Größe des Mandanten. Mit diesem Update wird die Leistung bei großen Mandanten erheblich verbessert, wenn Kunden kleinere Gruppen erstellen. 

Der neue Dienst hilft auch beim Hinzufügen und Entfernen von Mitgliedern, da Attributänderungen innerhalb weniger Minuten durchgeführt werden. Außerdem blockieren einzelne Verarbeitungsfehler nicht die Verarbeitung des Mandanten. Weitere Informationen zum Erstellen dynamischer Gruppen finden Sie in unserer [Dokumentation](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Oktober 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Lokale Azure AD-Hybrid-Agents von TLS-Zertifikatänderungen für Azure betroffen

**Typ:** Plan für Änderung  
**Dienstkategorie:** –  
**Produktfunktion**: Plattform

Microsoft aktualisiert Azure-Dienste für die Verwendung von TLS-Zertifikaten aus einer anderen Gruppe von Stammzertifizierungsstellen (Certificate Authorities, CAs). Diese Aktualisierung war notwendig, da die aktuellen Zertifikate der Zertifizierungsstellen eine der grundlegenden Anforderungen des Zertifizierungsstellen-/Browserforums nicht erfüllen. Diese Änderung wirkt sich auf lokal installierte Azure AD-Hybrid-Agents aus, die in festgeschriebenen Umgebungen mit einer festen Liste von Stammzertifikaten verwendet werden und so aktualisiert werden müssen, dass die neuen Zertifikataussteller als vertrauenswürdig eingestuft werden.

Diese Änderung führt zu einer Dienstunterbrechung, wenn Sie nicht sofort Maßnahmen ergreifen. Diese Agents umfassen [Anwendungsproxyconnectors](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) für den Remotezugriff auf die lokale Bereitstellung, [Pass-Through-Authentifizierungs-Agents](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect), über die sich Ihre Benutzer mit denselben Kennwörtern bei Anwendungen anmelden können, und Agents für die [Vorschau für Cloudbereitstellung](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect), die die Synchronisierung von AD mit Azure AD durchführen. 

Wenn in Ihrer Umgebung Firewallregeln festgelegt wurden, die dazu führen, dass ausgehende Aufrufe nur für bestimmte CRL-Downloads (Certificate Revocation List, Zertifikatssperrliste) zugelassen werden, müssen Sie die folgenden CRL- und OCSP-URLs zulassen. Vollständige Informationen zu der Änderung und den CRL- und OCSP-URLs, auf die der Zugriff ermöglicht wird, finden Sie unter [TLS-Zertifikatänderungen für Azure](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Bereitstellungsereignisse werden aus Überwachungsprotokollen entfernt und ausschließlich in Bereitstellungsprotokollen veröffentlicht

**Typ:** Plan für Änderung  
**Dienstkategorie:** Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 
Aktivitäten des SCIM-[Bereitstellungsdiensts](../app-provisioning/user-provisioning.md) werden sowohl in den Überwachungsprotokollen als auch in den Bereitstellungsprotokollen protokolliert. Dazu gehören Aktivitäten wie z. B. die Erstellung eines Benutzers in ServiceNow, die Gruppierung in GSuite oder der Import einer Rolle aus AWS. In Zukunft werden diese Ereignisse nur in den Bereitstellungsprotokollen veröffentlicht. Diese Änderung wird implementiert, um doppelte Ereignisse in den Protokollen sowie zusätzliche Kosten zu vermeiden, die dadurch entstehen, dass Kunden die Protokolle in Log Analytics nutzen. 

Wir werden Sie informieren, sobald ein Datum feststeht. Diese Änderung ist nicht für das Kalenderjahr 2020 geplant. 

> [!NOTE]
> Sie wirkt sich nur auf die vom Bereitstellungsdienst ausgegebenen Synchronisierungsereignisse aus. Andere Ereignisse in den Überwachungsprotokollen sind davon nicht betroffen. Ereignisse wie z. B. die Erstellung einer Anwendung, einer Richtlinie für bedingten Zugriff oder eines Benutzers im Verzeichnis werden weiterhin in den Überwachungsprotokollen ausgegeben. [Weitere Informationen](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context)
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Lokale Azure AD-Hybrid-Agents von TLS-Zertifikatänderungen (Transport Layer Security) für Azure betroffen

**Typ:** Plan für Änderung  
**Dienstkategorie:** –  
**Produktfunktion**: Plattform
 
Microsoft aktualisiert Azure-Dienste für die Verwendung von TLS-Zertifikaten aus einer anderen Gruppe von Stammzertifizierungsstellen (Certificate Authorities, CAs). Es wird ein Update durchgeführt, da die aktuellen Zertifikate der Zertifizierungsstellen eine der grundlegenden Anforderungen des Zertifizierungsstellen-/Browserforums nicht erfüllen. Diese Änderung wirkt sich auf lokal installierte Azure AD-Hybrid-Agents aus, die in festgeschriebenen Umgebungen mit einer festen Liste von Stammzertifikaten verwendet werden. Diese Agents müssen so aktualisiert werden, dass die neuen Zertifikataussteller als vertrauenswürdig eingestuft werden.

Diese Änderung führt zu einer Dienstunterbrechung, wenn Sie nicht sofort Maßnahmen ergreifen. Dies betrifft folgende Agents: 
- [Anwendungsproxyconnectors](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) für den Remotezugriff auf die lokale Bereitstellung 
- [Pass-Through-Authentifizierungs-Agents](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect), über die sich Ihre Benutzer mit denselben Kennwörtern bei Anwendungen anmelden können
- Agents für die [Vorschau der Cloudbereitstellung](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect), die die Synchronisierung von AD mit Azure AD durchführen 

Wenn in Ihrer Umgebung Firewallregeln festgelegt wurden, die dazu führen, dass ausgehende Aufrufe nur für bestimmte CRL-Downloads (Certificate Revocation List, Zertifikatssperrliste) zugelassen werden, müssen Sie CRL- und OCSP-URLs zulassen. Vollständige Informationen zu der Änderung und den CRL- und OCSP-URLs, auf die der Zugriff ermöglicht wird, finden Sie unter [TLS-Zertifikatänderungen für Azure](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Einstellung von TLS 1.0, TLS 1.1 und 3DES in Azure Active Directory in der US Gov-Cloud

**Typ:** Plan für Änderung  
**Dienstkategorie:** Alle Azure AD-Anwendungen  
**Produktfunktion:** Standards
 
In Azure Active Directory werden die folgenden Protokolle bis zum 31. März 2021 eingestellt:
- TLS 1.0
- TLS 1.1
- 3DES-Verschlüsselungssammlung (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Bei allen Client-Server- und Browser-Server-Kombinationen sollten TLS 1.2 und moderne Verschlüsselungssammlungen verwendet werden, um eine sichere Verbindung mit Azure Active Directory für Azure-, Office 365- und Microsoft 365-Dienste zu gewährleisten.

Betroffene Umgebungen:
- Azure US Gov
- [Office 365 GCC High und DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Zuweisen von Anwendungen zu Rollen im Bereich von Verwaltungseinheiten und Objekten

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
Diese Funktion ermöglicht die Zuweisung einer Anwendung (SPN) zu einer Administratorrolle im Bereich der Verwaltungseinheiten. Weitere Informationen finden Sie unter [Zuweisen von bereichsbezogenen Rollen zu einer Verwaltungseinheit](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Gastbenutzer können nun deaktiviert und gelöscht werden, wenn ihnen der Zugriff auf eine Ressource verweigert wird

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
„Deaktivieren und löschen“ ist eine erweiterte Steueroption in Azure AD-Zugriffsüberprüfungen, damit Organisationen externe Gäste in Gruppen und Apps besser verwalten können. Wenn Gästen in einer Zugriffsüberprüfung der Zugriff verweigert wird, werden sie durch **Deaktivieren und löschen** automatisch 30 Tage für die Anmeldung blockiert. Nach 30 Tagen werden sie vollständig aus dem Mandanten entfernt.

Weitere Informationen finden Sie unter [Deaktivieren und Löschen externer Identitäten mit Azure AD-Zugriffsüberprüfungen (Vorschau)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Ersteller von Zugriffsüberprüfungen können in E-Mails an Prüfer benutzerdefinierte Nachrichten hinzufügen

**Typ:** Neue Funktion  
**Dienstkategorie**: Zugriffsüberprüfungen  
**Produktfunktion:** Identity Governance
 
In Azure AD-Zugriffsüberprüfungen können Administratoren, die Überprüfungen erstellen, jetzt benutzerdefinierte Nachrichten an die Prüfer schreiben. Die Prüfer erhalten die Nachricht in der E-Mail, in der sie aufgefordert werden, die Überprüfung abzuschließen. Weitere Informationen zur Verwendung dieser Funktion finden Sie unter Schritt 14 des Abschnitts [Erstellen einer oder mehrerer Zugriffsüberprüfungen](../governance/create-access-review.md#create-one-or-more-access-reviews).

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – Oktober 2020

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Integrations-Assistent für Azure AD B2C

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
Der Integrations-Assistent (Vorschau) ist jetzt für App-Registrierungen in Azure AD B2C verfügbar. Über diesen Assistenten können Sie Anwendungen für gängige Szenarien konfigurieren. Weitere Informationen finden Sie unter [Bewährte Methoden und Empfehlungen für Microsoft Identity Platform](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Anzeigen der Rollenvorlagen-ID auf der Benutzeroberfläche des Azure-Portals

**Typ:** Neues Feature  
**Dienstkategorie:** Azure-Rollen  
**Produktfunktion:** Zugriffssteuerung
 

Sie können nun die Vorlagen-ID jeder Azure AD-Rolle im Azure-Portal anzeigen. Wählen Sie in Azure AD die **Beschreibung** der ausgewählten Rolle aus. 

Es wird empfohlen, dass Kunden im PowerShell-Skript und im Code anstelle des Anzeigenamens Rollenvorlagen-IDs verwenden. Die Rollenvorlagen-ID wird zur Verwendung in [directoryRoles](/graph/api/resources/directoryrole)- und [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta)-Objekten unterstützt. Weitere Informationen finden Sie unter [Rollenvorlagen-IDs](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>API-Connectors für Azure AD B2C-Benutzerflows für die Registrierung befinden sich jetzt in der öffentlichen Vorschauphase

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 

API-Connectors sind nun zur Verwendung mit Azure Active Directory B2C verfügbar. Mit API-Connectors können Sie Web-APIs verwenden, um Ihre Benutzerflows für die Registrierung anzupassen und mit externen Cloudsystemen zu integrieren. Mit API-Connectors können Sie folgende Aktionen ausführen:

- Integrieren in benutzerdefinierte Genehmigungsworkflows
- Überprüfen von Benutzereingabedaten
- Überschreiben von Benutzerattributen 
- Ausführen von benutzerdefinierter Geschäftslogik 

 Weitere Informationen finden Sie unter [Verwenden von API-Connectors zum Anpassen und Erweitern von Flows zur Benutzerregistrierung](../../active-directory-b2c/api-connectors-overview.md).

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>State-Eigenschaft für verbundene Organisationen in der Berechtigungsverwaltung

**Typ:** Neues Feature  
**Dienstkategorie:** Verzeichnisverwaltungs-**Produktfunktion:** Berechtigungsverwaltung
 

 Alle verbundenen Organisationen verfügen jetzt über die zusätzliche Eigenschaft „State“. Mit „State“ wird gesteuert, wie die verbundene Organisation in Richtlinien verwendet wird, die auf „Alle konfigurierten verbundenen Organisationen“ verweisen. Der Wert ist entweder „configured“ (konfiguriert, d. h., die Organisation befindet sich im Bereich der Richtlinien, die die „Alle“-Klausel verwenden) oder „proposed“ (vorgeschlagen, d. h., die Organisation befindet sich nicht im Gültigkeitsbereich).  

Bei manuell erstellten verbundenen Organisationen wird als Standardeinstellung „configured“ festgelegt. Automatisch erstellte verbundene Organisationen (die über Richtlinien erstellt werden, die es jedem Benutzer aus dem Internet ermöglichen, Zugriff anzufordern) werden standardmäßig auf „proposed“ festgelegt.  Alle verbundenen Organisationen, die vor dem 9. September 2020 erstellt wurden, werden auf „configured“ festgelegt. Administratoren können diese Eigenschaft bei Bedarf aktualisieren. [Weitere Informationen](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically)
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Externe Azure Active Directory-Identitäten verfügen jetzt über erweiterte Premium-Sicherheitseinstellungen für B2C

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
Der risikobasierte bedingte Zugriff und die Risikoerkennungsfunktionen von Identity Protection sind nun in [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md) verfügbar. Mit diesen erweiterten Sicherheitsfunktionen haben Kunden jetzt folgende Möglichkeiten:
- Verwenden von Intelligent Insights zur Risikobewertung bei B2C-Anwendungen und Endbenutzerkonten. Zu den Risikoerkennungen zählen ungewöhnliche Ortswechsel, anonyme IP-Adressen, mit Schadsoftware verknüpfte IP-Adressen und Azure AD Threat Intelligence. Auch portal- und API-basierte Berichte sind verfügbar.
- Automatisches Behandeln von Risiken durch Konfigurieren von Richtlinien für die adaptive Authentifizierung für B2C-Benutzer. App-Entwickler und Administratoren können Risiken in Echtzeit mindern, indem sie eine mehrstufige Authentifizierung (MFA) erforderlich machen oder den Zugriff blockieren, je nachdem, welche Benutzerrisikostufe erkannt wird und welche zusätzlichen Kontrollen basierend auf Standort, Gruppe und Anwendung verfügbar sind.
- Integration in Azure AD B2C-Benutzerflows und benutzerdefinierte Richtlinien. Die Bedingungen können über integrierte Benutzerflows in Azure AD B2C ausgelöst oder in benutzerdefinierte B2C-Richtlinien integriert werden. Wie andere Aspekte des B2C-Benutzerflows kann auch das Messaging für Endbenutzer angepasst werden. Die Anpassung erfolgt entsprechend den Optionen der Organisation für Sprache, Marke und Risikominderung.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – Oktober 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Oktober 2020 haben wir die folgenden 27 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee – Productivity Superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/), [Zone Technologies Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [Appointment Reminder](https://app.appointmentreminder.co.nz/account/login), [Cloud.Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetly](https://app.greetly.com/), [OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), [Loop Flow CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [Retail Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial.

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest.

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Bereitstellungsprotokolle können jetzt an Log Analytics übertragen werden

**Typ:** Neues Feature  
**Dienstkategorie:** Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 

Durch Veröffentlichen Ihrer Bereitstellungsprotokolle in Log Analytics haben Sie folgende Möglichkeiten:
- Speichern von Bereitstellungsprotokollen für mehr als 30 Tage
- Definieren von benutzerdefinierten Warnungen und Benachrichtigungen
- Erstellen von Dashboards zum Visualisieren der Protokolle
- Ausführen komplexer Abfragen zum Analysieren der Protokolle 

Informationen zur Verwendung der Funktion finden Sie unter [Grundlegendes zur Integration der Bereitstellung in Azure Monitor-Protokolle](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Bereitstellungsprotokolle können nun von Anwendungsbesitzern angezeigt werden

**Typ:** Geändertes Feature  
**Dienstkategorie:** Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 
Sie können Anwendungsbesitzern nun ermöglichen, die Aktivitäten des Bereitstellungsdiensts zu überwachen und Probleme zu beheben, ohne ihnen eine privilegierte Rolle zuzuweisen oder einen IT-Engpass zu verursachen. [Weitere Informationen](../reports-monitoring/concept-provisioning-logs.md)
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Umbenennung von 10 Azure Active Directory-Rollen

**Typ:** Geändertes Feature  
**Dienstkategorie:** Azure-Rollen  
**Produktfunktion:** Zugriffssteuerung
 
Einige integrierte Azure Active Directory-Rollen (Azure AD) haben Namen, die sich von den in Microsoft 365 Admin Center, im Azure AD-Portal und in Microsoft Graph angezeigten Namen unterscheiden. Diese Inkonsistenz kann Probleme bei automatisierten Prozessen verursachen. Mit diesem Update werden 10 Rollennamen umbenannt, um sie zu vereinheitlichen. In der folgenden Tabelle sind die neuen Rollennamen aufgeführt:

![Tabelle mit neuen Rollennamen](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C-Unterstützung für den Autorisierungscodeflow für SPAs mithilfe von MSAL JS 2.x

**Typ:** Geändertes Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
MSAL.js Version 2.x bietet jetzt Unterstützung für den Autorisierungscodeflow für Single-Page-Web-Apps (SPAs). Azure AD B2C unterstützt nun die Verwendung des SPA-App-Typs im Azure-Portal sowie die Verwendung des MSAL.js-Autorisierungscodeflows mit PKCE für Single-Page-Apps. Dadurch können in SPAs unter Verwendung von Azure AD B2C das einmalige Anmelden (SSO) in neueren Browsern beibehalten und neuere Empfehlungen für das Authentifizierungsprotokoll eingehalten werden. Informationen zu den ersten Schritten finden Sie im Tutorial [Registrieren einer Single-Page-Webanwendung (SPA) in Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md).

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Aktualisierung der Einstellung „Mehrstufige Authentifizierung auf vertrauenswürdigem Gerät speichern“

**Typ:** Geändertes Feature  
**Dienstkategorie:** MFA  
**Produktfunktion:** Identitätssicherheit und -schutz
 

Wir haben vor Kurzem die Funktion [Mehrstufige Authentifizierung auf vertrauenswürdigem Gerät speichern](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) aktualisiert, um die Authentifizierung auf bis zu 365 Tage zu verlängern. Bei Azure Active Directory (Azure AD) Premium-Lizenzen kann darüber hinaus die [Richtlinie für bedingten Zugriff – Anmeldehäufigkeit von Benutzern](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) verwendet werden, die mehr Flexibilität bei den Einstellungen für die erneute Authentifizierung bietet.

Um eine optimale Benutzerfreundlichkeit zu gewährleisten, empfiehlt es sich, die Lebensdauer von Sitzungen für vertrauenswürdige Geräte und Standorte sowie für Sitzungen mit geringem Risiko mithilfe der Anmeldehäufigkeit für bedingten Zugriff zu verlängern. Diese stellt eine Alternative zur Einstellung „Mehrstufige Authentifizierung auf vertrauenswürdigem Gerät speichern“ dar. Lesen Sie zum Einstieg den [neuesten Leitfaden zur Optimierung der erneuten Authentifizierung](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>September 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – September 2020

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot Security Awareness](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Cloudbereitstellungsaktualisierung, Public Preview

**Typ:** Neues Feature  
**Dienstkategorie:** Azure AD-Cloudbereitstellung, **Produktfunktion:** Identity Lifecycle Management
 
Die Azure AD Connect-Cloudbereitstellungsaktualisierung (Public Preview) bietet zwei wichtige Verbesserungen, die anhand von Kundenfeedback entwickelt wurden: 

- Attributzuordnung über das Azure-Portal

    Mit diesem Feature können IT-Administratoren Benutzer-, Gruppen- oder Kontaktattribute aus AD in Azure AD mithilfe verschiedener Zuordnungstypen zuordnen, die heute vorhanden sind. Die Attributzuordnung ist eine Funktion zum Standardisieren von Werten der Attribute, die von Active Directory an Azure Active Directory übertragen werden. Sie können bestimmen, ob der Attributwert von AD direkt Azure AD zugeordnet werden soll oder ob Ausdrücke verwendet werden sollen, um die Attributwerte beim Bereitstellen von Benutzern zu transformieren. [Weitere Informationen](../cloud-provisioning/how-to-attribute-mapping.md)

- Bedarfsgesteuerte Bereitstellung oder Testen der Benutzertransformation

    Nachdem Sie Ihre Konfiguration eingerichtet haben, möchten Sie vermutlich testen, ob die Benutzertransformation wie erwartet funktioniert, bevor Sie diese auf alle Ihre Benutzer im Bereich anwenden. Bei der bedarfsgesteuerten Bereitstellung können IT-Administratoren den Distinguished Name (DN) eines AD-Benutzers eingeben und überprüfen, ob er wie erwartet synchronisiert wird. Die bedarfsgesteuerte Bereitstellung bietet eine gute Möglichkeit, um sicherzustellen, dass die Attributzuordnungen, die Sie zuvor vorgenommen haben, erwartungsgemäß funktionieren. [Weitere Informationen](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Überwachte BitLocker-Wiederherstellung in Azure AD – Public Preview

**Typ:** Neues Feature  
**Dienstkategorie:** Gerätezugriffsverwaltung  
**Produktfunktion:** Lebenszyklusverwaltung für Geräte
 
Wenn IT-Administratoren oder Endbenutzer den BitLocker-Wiederherstellungsschlüssel lesen, auf den sie zugreifen können, generiert Azure Active Directory jetzt ein Überwachungsprotokoll, das erfasst, wer auf den Wiederherstellungsschlüssel zugegriffen hat. Dieselbe Überwachung liefert Details zu dem Gerät, dem der BitLocker-Schlüssel zugeordnet ist.

Endbenutzer können [über das Portal „Mein Konto“ auf ihre Wiederherstellungsschlüssel zugreifen](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). IT-Administratoren können über die [BitLocker-Wiederherstellungsschlüssel-API in der Betaversion](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) oder über das Azure AD-Portal auf Wiederherstellungsschlüssel zugreifen. Weitere Informationen finden Sie unter [Anzeigen oder Kopieren von BitLocker-Schlüsseln im Azure AD-Portal](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Integrierte Rolle „Teams-Geräteadministrator“

**Typ:** Neues Feature  
**Dienstkategorie:** RBAC  
**Produktfunktion:** Zugriffssteuerung
 
Benutzer mit der Rolle [Teams-Geräteadministrator](../roles/permissions-reference.md#teams-devices-administrator) können im Teams Admin Center [von Teams zertifizierte Geräte](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) verwalten. 

Benutzer mit dieser Rolle können alle Geräte auf einen Blick anzeigen und haben die Möglichkeit, Geräte zu suchen und zu filtern. Außerdem kann der Benutzer Details zu jedem Gerät überprüfen, darunter das angemeldete Konto sowie Marke und Modell des Geräts. Der Benutzer kann die Einstellungen auf dem Gerät ändern und die Softwareversionen aktualisieren. Diese Rolle gewährt keine Berechtigungen zum Überprüfen der Teams-Aktivität und der Anrufqualität des Geräts.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Erweiterte Abfragefunktionen für Verzeichnisobjekte

**Typ:** Neues Feature  
**Dienstkategorie:** MS Graph  
**Produktfunktion:** Entwickleroberfläche
 
Alle neuen Abfragefunktionen, die für Verzeichnisobjekte in Azure AD-APIs eingeführt wurden, sind jetzt im v1.0-Endpunkt und für die Produktionsumgebung verfügbar. Mit den OData-Standardoperatoren können Entwickler Verzeichnisobjekte und zugehörige Verknüpfungen zählen, durchsuchen, filtern und sortieren.

Weitere Informationen finden Sie in [dieser Dokumentation](https://aka.ms/BlogPostMezzoGA). Sie können über diese [kurze Umfrage](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u) auch Feedback senden.
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Öffentliche Vorschau: Fortlaufende Zugriffsevaluierung für Mandanten, die Richtlinien für bedingten Zugriff konfiguriert haben

**Typ:** Neues Feature  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Fortlaufende Zugriffsevaluierung (Continuous Access Evaluation, CAE) ist jetzt in der öffentlichen Vorschau für Azure AD-Mandanten mit Richtlinien für bedingten Zugriff verfügbar. Mit CAE werden kritische sicherheitsrelevante Ereignisse und Richtlinien in Echtzeit ausgewertet. Dies umfasst Kontodeaktivierungen, Kennwortzurücksetzungen und Standortänderungen. Weitere Informationen finden Sie unter [Fortlaufende Zugriffsevaluierung](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Öffentliche Vorschau: Benutzern, die ein Zugriffspaket anfordern, zusätzliche Fragen stellen, um bessere Genehmigungsentscheidungen treffen zu können

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
Administratoren können jetzt verlangen, dass Benutzer, die ein Zugriffspaket anfordern, im Portal „Mein Zugriff“ der Azure AD-Berechtigungsverwaltung zusätzliche Fragen beantworten müssen, die über das Angeben einer geschäftlichen Begründung hinausgehen. Die Antworten der Benutzer werden dann den genehmigenden Personen angezeigt, damit diese eine genauere Entscheidung über die Zugriffsgenehmigung treffen können. Weitere Informationen finden Sie unter [Sammeln zusätzlicher Anfordererinformationen für die Genehmigung (Vorschau)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Öffentliche Vorschau: Erweiterte Benutzerverwaltung

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerverwaltung  
**Produktfunktion:** Benutzerverwaltung
 

Das Azure AD-Portal wurde aktualisiert, um die Benutzersuche auf den Seiten „Alle Benutzer“ und „Gelöschte Benutzer“ zu vereinfachen. Zu den Änderungen in dieser Vorschau gehören: 
- Mehr sichtbare Benutzereigenschaften einschließlich Objekt-ID, Status der Verzeichnissynchronisierung, Erstellungstyp und Identitätsaussteller.
- Die Suchfunktion lässt jetzt eine Kombination aus Namen, E-Mail-Adressen und Objekt-IDs zu.
- Erweiterte Filterung nach Benutzertyp (Mitglied, Gast, kein), Status der Verzeichnissynchronisierung, Erstellungstyp, Firmenname und Domänenname.
- Neue Sortierfunktionen für Eigenschaften wie Name, Benutzerprinzipalname und Löschdatum.
- Eine neue Gesamtanzahl von Benutzern, die bei Suchvorgängen oder Filtern aktualisiert werden.

Weitere Informationen finden Sie unter [Erweiterungen bei der Benutzerverwaltung (Vorschau) – Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Neues Notizenfeld für Unternehmensanwendungen

**Typ:** Neues Feature  
**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Sie können Unternehmensanwendungen Textnotizen hinzufügen. Sie können alle relevanten Informationen hinzufügen, die Sie beim Verwalten von Anwendungen unter Unternehmensanwendungen unterstützen. Weitere Informationen finden Sie unter [Quickstart: Konfigurieren von Eigenschaften für eine Anwendung in Ihrem Azure AD-Mandanten (Azure Active Directory)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – September 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten

Im September 2020 haben wir die folgenden 34 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[VMware Horizon - Unified Access Gateway](), [Pulse Secure PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_Server](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [Gender Fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [Grammarly](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile Locker](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [Priva Identity & Access Management](https://my.priva.com/), [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [Secured Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Secured Signing Enterprise Portal AAD Setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec Online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft – Protected by F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial.

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest.

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Neue Delegierungsrolle in der Azure AD-Berechtigungsverwaltung: Zugriffspaketzuweisungsmanager

**Typ:** Neues Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 
In der Azure AD-Berechtigungsverwaltung wurde eine neue Rolle „Zugriffspaket-Zuweisungsmanager“ hinzugefügt, um granulare Berechtigungen zum Verwalten von Zuweisungen bereitzustellen. Sie können jetzt Aufgaben an einen Benutzer in dieser Rolle delegieren, der die Zuweisungsverwaltung eines Zugriffspakets an einen Geschäftsinhaber delegieren kann. Ein Zugriffspaket-Zuweisungsmanager kann jedoch die Zugriffspaketrichtlinien oder andere Eigenschaften, die von den Administratoren festgelegt wurden, nicht ändern. 

Durch diese neue Rolle profitieren Sie von den geringsten Berechtigungen, die zum Delegieren der Verwaltung von Zuweisungen und zum Aufrechterhalten der administrativen Kontrolle für alle anderen Zugriffspaketkonfigurationen erforderlich sind. Weitere Informationen finden Sie unter [Berechtigungsverwaltungsrollen](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Änderungen am Onboardingvorgang in Privileged Identity Management

**Typ:** Geändertes Feature  
**Dienstkategorie:** Privileged Identity Management  
**Produktfunktion**: Privileged Identity Management
 
Zuvor erforderte das Onboarding in Privileged Identity Management (PIM) die Benutzereinwilligung und einen Onboardingvorgang auf dem PIM-Blatt, das die Registrierung in Azure AD MFA enthielt. Mit der kürzlich erfolgten Integration der PIM-Funktion in das Azure AD-Blatt „Rollen und Administratoren“ wird diese Funktion entfernt. Jeder Mandant mit einer gültigen P2-Lizenz wird automatisch in PIM integriert.

Das Onboarding in PIM hat keine direkten negativen Auswirkungen auf Ihren Mandanten. Sie können die folgenden Änderungen erwarten:
- Zusätzliche Zuweisungsoptionen wie aktive oder berechtigte Rollen mit Start- und Endzeit, wenn Sie eine Zuweisung in PIM oder in Azure AD auf dem Blatt „Rollen und Administratoren“ vornehmen. 
- Zusätzliche Bereichsmechanismen wie administrative Einheiten und benutzerdefinierte Rollen wurden direkt in die Zuweisungsoberfläche eingeführt. 
- Wenn Sie ein globaler Administrator oder ein Administrator für privilegierte Rollen sind, erhalten Sie möglicherweise einige zusätzliche E-Mails wie die wöchentliche PIM-Zusammenfassung. 
- Darüber hinaus wird Ihnen möglicherweise auch der MS-PIM-Dienstprinzipal im Überwachungsprotokoll für die Rollenzuweisung angezeigt. Diese erwartete Änderung sollte sich nicht auf Ihren regulären Workflow auswirken.

 Weitere Informationen finden Sie unter [Einstieg in Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD-Berechtigungsverwaltung: Im Bereich „Auswählen“ der Zugriffspaketressourcen werden jetzt standardmäßig die Ressourcen angezeigt, die sich aktuell im ausgewählten Katalog befinden.

**Typ:** Geändertes Feature  
**Dienstkategorie:** Benutzerzugriffsverwaltung  
**Produktfunktion:** Berechtigungsverwaltung
 

Im Flow zum Erstellen von Zugriffspaketen unter der Registerkarte „Ressourcenrollen“ wird das Verhalten des Bereichs „Auswählen“ geändert. Derzeit werden im Standardverhalten alle im Besitz des Benutzers befindlichen Ressourcen sowie dem ausgewählten Katalog hinzugefügte Ressourcen angezeigt. 

Dieses Verhalten wird geändert, sodass standardmäßig nur die derzeit im Katalog hinzugefügten Ressourcen angezeigt werden, damit Benutzer problemlos Ressourcen aus dem Katalog auswählen können. Durch das Update wird die Auffindbarkeit der Ressourcen verbessert, die den Zugriffspaketen hinzugefügt werden können, und das Risiko minimiert, dass versehentlich Ressourcen hinzugefügt werden, die sich im Besitz des Benutzers befinden und nicht Teil des Katalogs sind. Weitere Informationen finden Sie unter [Erstellen eines neuen Zugriffspakets in der Azure AD-Berechtigungsverwaltung](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>August 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aktualisierungen bei den Anforderungen an die Firewall für Microsoft Azure Multi-Factor Authentication-Server

**Typ:** Plan für Änderung  
**Dienstkategorie:** MFA  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Ab dem 1. Oktober 2020 benötigen die Anforderungen an die Firewall für Azure MFA-Server zusätzliche IP-Adressbereiche.

Wenn es in Ihrer Organisation ausgehende Firewallregeln gibt, aktualisieren Sie diese Regeln, damit Ihre MFA-Server mit allen erforderlichen IP-Adressbereichen kommunizieren können. Die IP-Adressbereiche werden in [Anforderungen an die Azure Multi-Factor Authentication-Server-Firewall](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements) dokumentiert.

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Bevorstehende Änderungen der Benutzerumgebung für Identitätssicherheitsbewertung

**Typ:** Plan für Änderung  
**Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Wir aktualisieren das Portal für Identitätssicherheitsbewertung, sodass es mit den Änderungen übereinstimmt, die in der [neuen Release](/microsoft-365/security/mtp/microsoft-secure-score-whats-new) von Microsoft Secure Score eingeführt wurden. 

Die Vorschauversion mit den Änderungen wird ab Anfang September verfügbar sein. Die Änderungen in der Vorschauversion umfassen Folgendes:
- „Identitätssicherheitsbewertung“ wurde in „Sicherheitsbewertung für Identitäten“ zur Markenausrichtung mit Microsoft Secure Score (Microsoft Sicherheitsbewertung) umbenannt
- Punkte werden auf Standardskalierung normalisiert und in Prozentsätzen statt in Punkten gemeldet

In dieser Vorschau können Kunden zwischen der vorhandenen Umgebung und der neuen Umgebung wechseln. Diese Vorschau gilt bis Ende November 2020. Nach der Vorschau werden die Kunden automatisch zur neuen Benutzerumgebung geleitet.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Neue eingeschränkte Gastzugriffsberechtigungen in Azure AD – Öffentliche Vorschau

**Typ:** Neues Feature  
**Dienstkategorie:** Zugriffssteuerung   
**Produktfunktion:** Benutzerverwaltung

Wir haben die Berechtigungen auf Verzeichnisebene für Gastbenutzer aktualisiert. Diese Berechtigungen ermöglichen es Administratoren, zusätzliche Einschränkungen und Kontrollen für externen Gastbenutzerzugriff anzufordern. Administratoren können jetzt zusätzliche Einschränkungen für den Zugriff externer Gäste auf Profil- und Mitgliedschaftsinformationen für Benutzer und Gruppen hinzufügen. Mit dieser öffentlichen Previewfunktion können Kunden den externen Benutzerzugriff durch Verschleiern von Gruppenmitgliedschaften bedarfsorientiert verwalten. Dies schließt auch die Einschränkung von Gastbenutzern beim Anzeigen von Mitgliedschaften der Gruppe (n) ein, in der sie sich gerade befinden.

Weitere Informationen finden Sie unter [Restricted Guest Access Permissions](../enterprise-users/users-restrict-guest-permissions.md) (Eingeschränkte Berechtigungen für Gastzugriff) und [Users Default Permissions](./users-default-permissions.md) (Standardberechtigungen für Benutzer).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Allgemeine Verfügbarkeit von Delta-Abfragen für Dienstprinzipale

**Typ:** Neues Feature  
**Dienstkategorie:** MS Graph  
**Produktfunktion:** Entwickleroberfläche
 
Die Delta-Abfrage in Microsoft Graph unterstützt jetzt den Ressourcentyp in v1.0:
- Dienstprinzipal

Jetzt können Clients Änderungen an diesen Ressourcen effizient nachverfolgen und die beste Lösung zum Synchronisieren von Änderungen daran mit einem lokalen Datenspeicher bereitstellen. Informationen zum Konfigurieren dieser Ressourcen in einer Abfrage finden Sie unter [Nachverfolgen von Änderungen bei Microsoft Graph-Daten mithilfe einer Delta-Abfrage](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Allgemeine Verfügbarkeit von Delta-Abfragen für oAuth2PermissionGrant

**Typ:** Neues Feature  
**Dienstkategorie:** MS Graph  
**Produktfunktion:** Entwickleroberfläche

Die Delta-Abfrage in Microsoft Graph unterstützt jetzt den Ressourcentyp in v1.0:
- OAuth2PermissionGrant

Clients können jetzt Änderungen an diesen Ressourcen effizient nachverfolgen und die beste Lösung zum Synchronisieren von Änderungen daran mit einem lokalen Datenspeicher bereitstellen. Informationen zum Konfigurieren dieser Ressourcen in einer Abfrage finden Sie unter [Nachverfolgen von Änderungen bei Microsoft Graph-Daten mithilfe einer Delta-Abfrage](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – August 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten

Im August 2020 haben wir die folgenden 25 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Enlyft Dynamics 365 Connector](http://enlyft.com/), [Serraview Space Utilization Software Solutions](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Uniq](https://web.uniq.app/), [Visibly](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum – Courseware Assessments Exact Path](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec Clearance](https://www.clearance.network/), [iSAMS](../saas-apps/isams-tutorial.md), [VeraSMART](../saas-apps/verasmart-tutorial.md), [Amiko](https://amiko.web.rivero.app/), [Twingate](https://auth.twingate.com/signup), [Funnel Leasing](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scalefusion](https://scalefusion.com/users/sign_in/), [Bpanda](https://goto.bpanda.com/login), [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect), [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md), [Wandera End User](https://www.wandera.com/)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial.

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest.

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Ressourcengesamtstrukturen jetzt für Azure AD DS verfügbar 

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD Domain Services   
**Produktfunktion**: Azure AD Domain Services
 
Die Funktionalität von Ressourcengesamtstrukturen in Azure AD Domain Services ist jetzt allgemein verfügbar. Sie können jetzt die Autorisierung ohne Kennworthashsynchronisierung zur Nutzung von Azure AD Domain Services aktivieren, einschließlich Smartcardautorisierung. Weitere Informationen finden Sie unter [Konzepte und Features zu Replikatgruppen für Azure Active Directory Domain Services (Vorschauversion)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Regionale Replikatunterstützung für verwaltete Azure AD DS-Domänen jetzt verfügbar

**Typ:** Neues Feature   
**Dienstkategorie**: Azure AD Domain Services  
**Produktfunktion**: Azure AD Domain Services
 
Sie können eine verwaltete Domäne erweitern, sodass mehrere Replikatgruppen pro Azure AD-Mandant festgelegt sind. Replikatgruppen können einem beliebigen virtuellen Netzwerk mit Peering in einer beliebigen Azure-Region mit Azure AD Domain Services-Unterstützung hinzugefügt werden. Zusätzliche Replikatgruppen in verschiedenen Azure-Regionen bieten eine geografische Notfallwiederherstellung für Legacyanwendungen, wenn eine Azure-Region offline geschaltet wird. Weitere Informationen finden Sie unter [Konzepte und Features zu Replikatgruppen für Azure Active Directory Domain Services (Vorschauversion)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Allgemeine Verfügbarkeit von Azure AD Meine Anmeldungen

**Typ:** Neues Feature  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Endbenutzerumgebungen
 
Azure AD Meine Anmeldungen ist ein neues Feature, mit dem Unternehmensbenutzer ihren Anmeldeverlauf auf ungewöhnliche Aktivitäten überprüfen können. Darüber hinaus können Endbenutzer mit diesem Feature bei verdächtigen Aktivitäten „Das war ich nicht“ oder „Das war ich“ melden. Weitere Informationen zur Verwendung dieses Features finden Sie unter [Anzeigen und Suchen Ihrer letzten Anmeldeaktivität auf der Seite „Meine Anmeldungen“](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Die HR-gesteuerte Benutzerbereitstellung von SAP SuccessFactors in Azure AD ist jetzt allgemein verfügbar

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Identity Lifecycle Management
 
Sie können jetzt SAP SuccessFactors als autoritative Identitätsquelle in Azure AD integrieren und den End-to-End-Identitätslebenszyklus mithilfe von HR-Ereignissen wie Neueinstellungen und Kündigungen automatisieren, um die Bereitstellung und Aufhebung der Bereitstellung von Konten in Azure AD zu fördern. 

Weitere Informationen zum Konfigurieren der eingehenden Bereitstellung von SAP SuccessFactors in Azure AD finden Sie im Tutorial [Konfigurieren der Benutzerbereitstellung von SAP SuccessFactors in Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Benutzerdefinierte Open ID Connect-Unterstützung der MS Graph-API für Azure AD B2C

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
Zuvor konnten Anbieter von Custom Open ID Connect nur über das Azure-Portal hinzugefügt oder verwaltet werden. Jetzt können die Azure AD B2C-Kunden diese Anbieter auch über die Betaversion von Microsoft Graph-APIs hinzufügen und verwalten. Informationen zum Konfigurieren dieser Ressource mit APIs finden Sie unter [Ressourcentyp „identityProvider“](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Zuweisen von in Azure AD integrierten Rollen zu Cloudgruppen

**Typ:** Neues Feature  
**Dienstkategorie:** Azure AD-Rollen  
**Produktfunktion:** Zugriffssteuerung

Mit diesem neuen Feature können Sie Cloudgruppen jetzt in Azure AD integrierte Rollen zuweisen. Beispielsweise können Sie der Gruppe „Contoso_SharePoint_Admins“ die SharePoint-Rolle „Administrator“ zuweisen. Sie können die Gruppe auch mithilfe von PIM (Privileged Identity Management) zu einem berechtigten Mitglied der Rolle machen, statt ihr ständigen Zugriff zu gewähren. Informationen zum Konfigurieren dieses Features finden Sie unter [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen in Azure Active Directory (Vorschau)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Integrierte Rolle „Business Leader für Insights“ jetzt verfügbar

**Typ:** Neues Feature  
**Dienstkategorie:** Azure AD-Rollen  
**Produktfunktion:** Zugriffssteuerung
 
Benutzer in der Rolle „Business Leader für Insights“ können über die [M365-Insights-Anwendung](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics) auf eine Gruppe von Dashboards und Erkenntnissen zugreifen. Dies umfasst Vollzugriff auf alle Dashboards und die dargestellten Funktionen für Erkenntnisse sowie das Durchsuchen von Daten. Benutzer in dieser Rolle haben jedoch keinen Zugriff auf die Einstellungen für Produktkonfiguration, für die die Rolle „Insights-Administrator“ zuständig ist. Weitere Informationen zu dieser Rolle finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../roles/permissions-reference.md#insights-business-leader).
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Integrierte Rolle „Insights-Administrator“ jetzt verfügbar

**Typ:** Neues Feature  
**Dienstkategorie:** Azure AD-Rollen  
**Produktfunktion:** Zugriffssteuerung
 
Benutzer in der Rolle „Insights-Administrator“ können auf alle administrativen Funktionen in der [M365-Insights-Anwendung](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics) zugreifen. Ein Benutzer in dieser Rolle kann Verzeichnisinformationen lesen, die Dienstintegrität überwachen, Supporttickets einordnen und auf die Einstellungsaspekte für Insights-Administratoren zugreifen. Weitere Informationen zu dieser Rolle finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../roles/permissions-reference.md#insights-administrator).
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Anwendungsadministrator und Cloudanwendungsadministrator können Erweiterungseigenschaften von Anwendungen verwalten

**Typ:** Geändertes Feature  
**Dienstkategorie:** Azure AD-Rollen  
**Produktfunktion:** Zugriffssteuerung
 
Zuvor konnte nur der globale Administrator die [Erweiterungseigenschaft](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http) verwalten. Wir aktivieren diese Funktionalität jetzt auch für den Anwendungsadministrator und den Cloudanwendungsadministrator.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 Hotfix 4.6.263.0 und Connectors 1.1.1301.0

**Typ:** Geändertes Feature  
**Dienstkategorie:** Microsoft Identity Manager  
**Produktfunktion:** Identity Lifecycle Management

Ein [Hotfixrollup-Paket (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) ist für Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) verfügbar. Dieses Rollup-Paket enthält Updates für die Microsoft Identity Manager-Zertifikatverwaltung (Microsoft Identity Manager Certificate Management, MIM CM), den MIM-Synchronisierungs-Manager und die PAM-Komponenten (Privileged Access Management). Außerdem enthält der Build der MIM-generischen Connectors 1.1.1301.0 Updates für den Graph-Connector.

---
 


