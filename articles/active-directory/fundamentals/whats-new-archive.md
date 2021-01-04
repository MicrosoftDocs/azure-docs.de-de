---
title: Archiv für Neuerungen in Azure Active Directory | Microsoft-Dokumentation
description: Die Versionshinweise zu Neuerungen im Abschnitt „Übersicht“ dieses Inhaltssatzes enthalten sechs Monate Aktivität. Nach 6 Monaten werden die Elemente aus den Hauptartikel entfernt und in diesem Archivartikel abgelegt.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd6aa8c1936d9f07421f6508755a5b6d4852813
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859180"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archiv für Neuerungen in Azure Active Directory

Der primäre Artikel [Neuerungen in Azure Active Directory – Anmerkungen zu dieser Version](whats-new.md) enthält die Aktualisierungen aus den letzten sechs Monaten, während dieser Artikel alle älteren Informationen enthält.

„Neuerungen in Azure Active Directory – Anmerkungen zu dieser Version“ bieten Ihnen Informationen über:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern
- Veraltete Funktionen
- Pläne für Änderungen

---
## <a name="may-2020"></a>Mai 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Außerbetriebnahme von Eigenschaften in signIns-, riskyUsers- und riskDetections-APIs

**Typ:** Plan für Änderung  
**Dienstkategorie**: Identitätsschutz  
**Produktfunktion:** Identitätssicherheit und -schutz

Derzeit werden Aufzählungstypen verwendet, um die Eigenschaft „riskType“ in der riskDetections-API und in „riskyUserHistoryItem“ (Vorschauversion) darzustellen. Aufzählungstypen werden auch für die riskEventTypes-Eigenschaft in der signIns-API verwendet. In Zukunft werden diese Eigenschaften als Zeichenfolgen dargestellt. 

Kunden müssen bis zum 09.09.2020 auf die riskEventType-Eigenschaft in der riskDetections-API (Betaversion) und der riskyUserHistoryItem-API wechseln und auf die riskEventTypes_v2-Eigenschaft in der signIns-API (Betaversion) 2020 umstellen. Zu diesem Zeitpunkt werden die aktuellen Eigenschaften „riskType“ und „riskEventTypes“ zurückgezogen. Weitere Informationen finden Sie unter [Änderungen an Risikoereigniseigenschaften und Identitätsschutz-APIs auf Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Veraltung der riskEventTypes-Eigenschaft in der signIns v 1.0-API auf Microsoft Graph

**Typ:** Plan für Änderung  
**Dienstkategorie:** Berichterstellung  
**Produktfunktion:** Identitätssicherheit und -schutz

Aufzählungstypen wechseln zu Zeichenfolgentypen, wenn sie im September 2020 Risikoereigniseigenschaften in Microsoft Graph darstellen. Diese Änderung wirkt sich nicht nur auf die Vorschau-APIs aus, sondern auch auf die signIns-API in der Produktion.

Wir haben eine neue riskEventsTypes_v2 (string)-Eigenschaft für die signIns v1.0-API eingeführt. Wir werden die aktuelle riskEventTypes (enum)-Eigenschaft am 11.06.2022 in Übereinstimmung mit der Veraltungsrichtlinie für Microsoft Graph zurückziehen. Kunden müssen bis zum 11.06.2022 zur riskEventTypes_v2-Eigenschaft der v 1.0-signins-API wechseln. Weitere Informationen finden Sie unter [Veraltung der riskEventTypes-Eigenschaft in der signIns v 1.0-API auf Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Bevorstehende Änderungen an MFA-E-Mail-Benachrichtigungen

**Typ:** Plan für Änderung  
**Dienstkategorie:** MFA  
**Produktfunktion**: Identitätssicherheit und -schutz
 

Wir nehmen die folgenden Änderungen an den E-Mail-Benachrichtigungen für die Cloud-MFA vor:

E-Mail-Benachrichtigungen werden von den folgenden Adressen gesendet: azure-noreply@microsoft.com und msonlineservicesteam@microsoftonline.com. Wir aktualisieren den Inhalt von E-Mails mit Betrugswarnungen, um die erforderlichen Schritte zum Entsperren von Verwendungen besser anzugeben.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Neue Self-Service-Registrierung für Benutzer in Verbunddomänen, die nicht auf Microsoft Teams zugreifen können, weil sie nicht mit Azure Active Directory synchronisiert sind.

**Typ:** Plan für Änderung  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 

Derzeit können Benutzer, die sich in Azure AD-Verbunddomänen befinden, aber nicht mit dem Mandanten synchronisiert sind, nicht auf Teams zugreifen. Ab Ende Juni wird diese neue Funktion es ihnen ermöglichen, indem sie die vorhandene Registrierungsfunktion mit E-Mail-Überprüfung erweitert. Dies ermöglicht Benutzern, die sich bei einem verbundenen IDP anmelden können, aber noch kein Benutzerobjekt in Azure ID haben, über ein automatisch erstelltes Benutzerobjekt zu verfügen und für Teams authentifiziert zu werden. Ihr Benutzerobjekt wird als „Self-Service-Registrierung“ gekennzeichnet. Dies ist eine Erweiterung der vorhandenen Funktion zum Durchführen der per E-Mail überprüften Selbstregistrierung von Benutzern in verwalteten Domänen und kann mit demselben Flag gesteuert werden. Diese Änderung wird in den folgenden zwei Monaten vollständig ausgeführt. Achten Sie [hier](../enterprise-users/directory-self-service-signup.md) auf Dokumentationsupdates.
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Anstehende Korrektur: Das OIDC-Ermittlungsdokument für die Azure Government-Cloud wird aktualisiert, sodass es auf die richtigen Graph-Endpunkte verweist.

**Typ:** Plan für Änderung  
**Dienstkategorie:** Unabhängige Clouds  
**Produktfunktion:** Benutzerauthentifizierung
 
Ab Juni gibt das OIDC-Ermittlungsdokument [Microsoft Identity Platform und das OpenID Connect-Protokoll](../develop/v2-protocols-oidc.md) auf dem [Azure Government-Cloud](../develop/authentication-national-cloud.md)-Endpunkt (login.microsoftonline.us) den richtigen [Nationale Cloudbereitstellungen](/graph/deployments)-Endpunkt (https://graph.microsoft.us oder https://dod-graph.microsoft.us) auf Basis des bereitgestellten Mandanten zurück.  Derzeit wird der falsche Graph-Endpunkt (graph.microsoft.com) „msgraph_host“-Feld angezeigt.  

Diese Fehlerbehebung wird schrittweise über ungefähr 2 Monate hinweg eingeführt.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government-Benutzer können sich nicht mehr bei „login.microsoftonline.com“ anmelden.

**Typ:** Geplante Änderung  
**Dienstkategorie:** Unabhängige Clouds  
**Produktfunktion:** Benutzerauthentifizierung
 
Am 1. Juni 2018 hat sich die offizielle Azure Active Directory (Azure AD)-Autorität für Azure Government von https://login-us.microsoftonline.com in https://login.microsoftonline.us geändert. Wenn Sie eine Anwendung in einem Azure Government-Mandanten besitzen, müssen Sie Ihre Anwendung aktualisieren, damit die Benutzer am US-Endpunkt angemeldet werden.

Ab dem 5. Mai wird in Azure AD mit der Durchsetzung der Endpunktänderung begonnen. Azure Government-Benutzer werden daran gehindert, sich mit dem öffentlichen Endpunkt (microsoftonline.com) bei in Azure Government-Mandanten gehosteten Apps anzumelden. Für betroffene Apps wird eine Fehlermeldung „AADSTS900439 – USGClientNotSupportedOnPublicEndpoint“ angezeigt. 

Diese Änderung wird schrittweise eingeführt, wobei die Erzwingung für alle Apps voraussichtlich im Juni 2020 erfolgt. Weitere Einzelheiten finden Sie im [Azure Government-Blogbeitrag](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>Die SAML-Anforderung für einmaliges Abmelden sendet jetzt eine NameID im richtigen Format

**Typ:** Korrigiert  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
Wenn ein Benutzer auf die Abmeldung klickt (z. B. im MyApps-Portal), sendet Azure AD eine SAML-Nachricht für einmaliges Abmelden an jede App, die in der Benutzersitzung aktiv ist und für die eine Abmelde-URL konfiguriert ist. Diese Nachrichten enthalten eine NameID in einem persistenten Format.

Wenn das ursprüngliche SAML-Anmeldetoken ein anderes Format für die NameID verwendete (z. B. email/UPN), konnte die SAML-App die NameID in der Abmeldenachricht nicht mit einer vorhandenen Sitzung korrelieren (da sich die in beiden Nachrichten verwendeten NameIDs unterschieden), was dazu führte, dass die Abmeldenachricht von der SAML-App verworfen wurde und der Benutzer angemeldet bleiben musste. Diese Korrektur bewirkt, dass die Abmeldenachricht mit der für die Anwendung konfigurierten NameID konsistent ist.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Rolle „Hybrididentitätsadministrator“ ist jetzt bei der Cloudbereitstellung verfügbar

**Typ:** Neues Feature  
**Dienstkategorie:** Azure AD-Cloudbereitstellung  
**Produktfunktion:** Identity Lifecycle Management
 
IT-Administratoren können jetzt die neue „Hybridadministrator“-Rolle als am wenigsten privilegierte Rolle zum Einrichten der Azure ADConnect-Cloudbereitstellung verwenden. Mit dieser neuen Rolle ist es nicht mehr erforderlich, die Rolle „Globaler Administrator“ zum Einrichten und Konfigurieren der Cloudbereitstellung zu verwenden. [Weitere Informationen](../roles/delegate-by-task.md#connect)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog verfügbar – Mai 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Mai 2020 haben wir die folgenden 36 neuen Anwendungen mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-Engaged](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insuite](../saas-apps/insuite-tutorial.md), [Change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX Assurance Platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [Smart Global Governance](../saas-apps/smart-global-governance-tutorial.md), [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO for BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), [EasySSO for Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii](../saas-apps/torii-tutorial.md), [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md), [Humanage](../saas-apps/humanage-tutorial.md), [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md), [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md), [ShareVault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [TextExpander](../saas-apps/textexpander-tutorial.md), [Anyone Home CRM](../saas-apps/anyone-home-crm-tutorial.md), [askSpoke](../saas-apps/askspoke-tutorial.md), [ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

Die Dokumentation zu allen Anwendungen finden Sie auch hier: https://aka.ms/AppsTutorial.

Informationen zum Auflisten Ihrer Anwendung im Azure AD-App-Katalog finden Sie hier: https://aka.ms/AzureADAppRequest.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Reiner Berichtsmodus für den bedingten Zugriff jetzt allgemein verfügbar

**Typ:** Neues Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz

Mit dem [reinen Berichtsmodus für den bedingten Azure AD-Zugriff](../conditional-access/concept-conditional-access-report-only.md) können Sie das Ergebnis einer Richtlinie auswerten, ohne die Zugriffssteuerungen zu erzwingen. Im reinen Berichtsmodus können Sie Richtlinien in Ihrer Organisation vor der Aktivierung testen und deren Auswirkungen verstehen, sodass die Bereitstellung sicherer und einfacher wird. In den letzten Monaten haben wir eine hohe Akzeptanz des reinen Berichtsmodus festgestellt. Richtlinien im reinen Berichtsmodus weisen bereits einen Umfang von mehr als 26 Millionen Benutzern auf. Mit der heutigen Ankündigung werden neue Richtlinien für den bedingten Azure AD-Zugriff standardmäßig im reinen Berichtsmodus erstellt. Dies bedeutet, dass Sie die Auswirkungen Ihrer Richtlinien vom Zeitpunkt der Erstellung an überwachen können. Benutzer der MS Graph-APIs können zudem [Richtlinien im reinen Berichtsmodus programmgesteuert verwalten](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="self-service-sign-up-for-guest-users"></a>Self-Service-Registrierung für Gastbenutzer

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Mit „Externe Identitäten“ in Azure AD können Sie Personen außerhalb Ihrer Organisation den Zugriff auf Ihre Apps und Ressourcen erlauben. Gleichzeitig können sie sich mit der von ihnen bevorzugten Identität anmelden. Wenn Sie eine Anwendung für externe Benutzer freigeben, wissen Sie möglicherweise nicht immer im Voraus, wer Zugriff auf die Anwendung benötigt. Mit [Self-Service-Registrierung](../external-identities/self-service-sign-up-overview.md) können Sie Gastbenutzern ermöglichen, sich zu registrieren und ein Gastkonto für Ihre LOB-Apps (Line of Business, Branche) zu erhalten. Der Registrierungsfluss kann erstellt und so angepasst werden, dass er Azure AD und soziale Identitäten unterstützt. Sie können auch während der Registrierung zusätzliche Informationen über den Benutzer sammeln.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbeitsmappe für Erkenntnisse und Berichterstellung zum bedingten Zugriff jetzt allgemein verfügbar

**Typ:** Neues Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz

Die [Arbeitsmappe für Erkenntnisse und Berichterstellung](../conditional-access/howto-conditional-access-insights-reporting.md) bietet Administratoren eine Zusammenfassungsansicht des bedingten Azure AD-Zugriffs in ihrem Mandanten. Mit der Möglichkeit, eine einzelne Richtlinie auszuwählen, können Administratoren besser verstehen, was jede Richtlinie bewirkt, und Änderungen in Echtzeit überwachen. Die Arbeitsmappe streamt die in Azure Monitor gespeicherten Daten, die Sie [anhand dieser Anweisungen](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) in wenigen Minuten einrichten können. Damit das Dashboard leichter zu finden ist, haben wir es auf die neue Registerkarte „Erkenntnisse und Berichte“ im Menü „Bedingter Azure AD-Zugriff“ verschoben.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Blatt „Richtliniendetails“ für den bedingten Zugriff als öffentliche Vorschau verfügbar

**Typ:** Neues Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz

Auf dem neuen Blatt [Richtliniendetails](../conditional-access/troubleshoot-conditional-access.md) wird angezeigt, welche Zuweisungen, Bedingungen und Steuerelemente während der Auswertung der Richtlinie für bedingten Zugriff erfüllt wurden. Sie können auf das Blatt zugreifen, indem Sie in den Anmeldedetails auf der Registerkarte „Bedingter Zugriff“ oder der Registerkarte „Nur Bericht“ eine Zeile auswählen.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Neue Abfragefunktionen für Verzeichnisobjekte in Microsoft Graph sind in Public Preview

**Typ:** Neues Feature  
**Dienstkategorie:** MS Graph **Produktfunktion**: Entwickleroberfläche

Für Microsoft Graph-Verzeichnisobjekt-APIs werden neue Funktionen eingeführt, die Zähl-, Such-, Filter- und Sortiervorgänge ermöglichen. So erhalten Entwickler die Möglichkeit, unsere Verzeichnisobjekte ohne Problemumgehungen wie In-Memory-Filterung und -Sortierung schnell abzufragen. Weitere Informationen finden Sie in diesem [Blogbeitrag](https://aka.ms/CountFilterMSGraphAAD).

Wir sind zurzeit in der Public Preview und bitten um Feedback. Bitte senden Sie Ihre Kommentare mit dieser [kurzen Umfrage](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Konfigurieren von SAML-basiertem einmaligem Anmelden unter Verwendung von Microsoft Graph-APIs (Beta)

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** SSO
 
Unterstützung für das Erstellen und Konfigurieren einer Anwendung aus dem Azure AD-Katalog mithilfe von MS Graph-APIs in der Betaversion ist jetzt verfügbar. Wenn Sie SAML-basiertes einmaliges Anmelden für mehrere Instanzen einer Anwendung einrichten müssen, sparen Sie Zeit, indem Sie die Microsoft Graph-APIs verwenden, um die [Konfiguration von SAML-basiertem einmaligem Anmelden zu automatisieren](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – Mai 2020

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic (nach Organisation)](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML-Tokenverschlüsselung ist allgemein verfügbar

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** SSO
 
[Die SAML-Tokenverschlüsselung](../manage-apps/howto-saml-token-encryption.md) ermöglicht die Konfiguration von Anwendungen für den Empfang verschlüsselter SAML-Assertionen. Die Funktion ist nun in allen Clouds allgemein verfügbar.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Gruppennamenansprüche in Anwendungstoken sind allgemein verfügbar

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** SSO
 
Die in einem Token ausgestellten Gruppenansprüche können jetzt auf die Gruppen beschränkt werden, die der Anwendung zugewiesen sind.  Dies ist besonders wichtig, wenn Benutzer einer großen Anzahl von Gruppen angehören und das Risiko besteht, dass die Beschränkungen der Tokengröße überschritten werden. Mit dieser neuen Funktion ist die Möglichkeit, [Token Gruppennamen hinzuzufügen](../hybrid/how-to-connect-fed-group-claims.md), allgemein verfügbar.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback unterstützt jetzt das Festlegen von Geschäftstelefonnummer-Attributen

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Identity Lifecycle Management
 
Wir haben die Workday Writeback-Bereitstellungs-App erweitert, um jetzt das Rückschreiben von Geschäftstelefonnummer- und Mobilnummerattributen zu unterstützen. Zusätzlich zu E-Mail-Adresse und Benutzername können Sie nun die Workday Writeback-Bereitstellungs-App so konfigurieren, dass Telefonnummerwerte von Azure AD an Workday weitergeleitet werden. Weitere Informationen zum Konfigurieren des Rückschreibens von Telefonnummern finden Sie im Tutorial zur [Workday Writeback](../saas-apps/workday-writeback-tutorial.md)-App. 

---

### <a name="publisher-verification-preview"></a>Herausgeberüberprüfung (Vorschauversion)

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Entwickleroberfläche
 
Die Herausgeberüberprüfung (Vorschauversion) unterstützt Administratoren und Endbenutzer dabei, die Authentizität von Anwendungsentwicklern zu verstehen, die in die Microsoft Identity Platform integriert sind. Weitere Informationen finden Sie unter [Herausgeberüberprüfung (Vorschau)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Autorisierungscodeflow für Single-Page-Apps

**Typ:** Geändertes Feature **Dienstkategorie:** Authentifizierung **Produktfunktion:** Entwickleroberfläche

Aufgrund der [Cookieeinschränkungen von Drittanbietern wie Safari ITP](../develop/reference-third-party-cookies-spas.md) moderner Browser müssen SPAs anstelle des impliziten Flows zum Verwalten des einmaligen Anmeldens den Autorisierungscodeflow verwenden. MSAL.js v 2.x unterstützt jetzt den Autorisierungscodeflow. Dort sind entsprechende Updates für das Azure-Portal vorhanden, sodass Sie Ihre SPA so aktualisieren können, dass sie den Typ „spa“ hat und den Autorisierungscodeflow verwendet. Anleitungen finden Sie unter [Schnellstart: Anmelden von Benutzern und Abrufen eines Zugriffstokens in einer JavaScript-SPA mithilfe des Autorisierungscodeflows](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Verbesserte Filterung für Geräte in Public Preview

**Typ:** Geändertes Feature   
**Dienstkategorie:** Geräteverwaltung **Produktfunktion:** Lebenszyklusverwaltung für Geräte
 
Bisher konnten nur die Filter „Aktiviert“ und „Aktivitätsdatum“ verwendet werden. Nun können Sie die [Liste der Geräte nach weiteren Eigenschaften filtern](../devices/device-management-azure-portal.md#device-list-filtering-preview), einschließlich Betriebssystemtyp, Verknüpfungstyp, Konformität und mehr. Diese Ergänzungen sollten das Auffinden eines bestimmten Geräts vereinfachen.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Die neue Benutzeroberfläche für App-Registrierungen in Azure AD B2C ist jetzt allgemein verfügbar

**Typ:** Geändertes Feature   
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** Identity Lifecycle Management
 
Die neue Benutzeroberfläche für App-Registrierungen in Azure AD B2C ist jetzt allgemein verfügbar. 

Bisher mussten Sie Ihre kundenorientierten B2C-Anwendungen mithilfe der Legacyoberfläche „Anwendungen“ getrennt von Ihren restlichen Apps verwalten. Dies bedeutete die Nutzung unterschiedlicher Oberflächen zur App-Erstellung an unterschiedlichen Stellen in Azure.

Mit der neuen Benutzeroberfläche können alle App-Registrierungen bei B2C und Azure AD von einem Ort aus konsistent verwaltet werden. Unabhängig davon, ob Sie eine kundenorientierte App verwalten müssen oder eine App, die Zugriff auf Microsoft Graph hat, um Azure AD B2C-Ressourcen programmgesteuert zu verwalten, müssen Sie nur eine Vorgehensweise erlernen.

Um zu der neuen Benutzeroberfläche zu gelangen, navigieren Sie im Azure AD B2C-Dienst und wählen das App-Registrierungsblatt aus. Der Zugriff auf die Benutzeroberfläche ist auch über den Azure Active Directory-Dienst möglich.

Die Azure AD B2C-Benutzeroberfläche für App-Registrierungen basiert auf der allgemeinen [Oberfläche für App-Registrierungen](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) bei einem Azure AD-Mandanten, ist jedoch an die Anforderungen von Azure AD B2C angepasst. Die ältere „Anwendungen“-Benutzeroberfläche wird in Zukunft als veraltet eingestuft.

Weitere Informationen finden Sie unter [Die neue App-Registrierungsbenutzeroberfläche für Azure AD B2C](../../active-directory-b2c/app-registrations-training-guide.md).

---
## <a name="april-2020"></a>April 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Umgebung für die kombinierte Registrierung von Sicherheitsinformationen jetzt allgemein verfügbar

**Typ:** Neues Feature

**Dienstkategorie**: Authentifizierungen (Anmeldungen)

**Produktfunktion:** Identitätssicherheit und -schutz

Die Umgebung für die kombinierte Registrierung für Multi-Factor Authentication (MFA) und Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) ist jetzt allgemein verfügbar. Diese neue Registrierungsumgebung bietet den Benutzern die Möglichkeit, sich in einem einzigen schrittweisen Prozess für MFA und SSPR zu registrieren. Wenn Sie die neue Umgebung für Ihre Organisation bereitstellen, können sich die Benutzer schneller und unkomplizierter registrieren. Lesen Sie [hier](https://bit.ly/3etiRyQ) den Blogbeitrag.

---

### <a name="continuous-access-evaluation"></a>Fortlaufende Zugriffsevaluierung

**Typ:** Neues Feature

**Dienstkategorie**: Authentifizierungen (Anmeldungen)

**Produktfunktion:** Identitätssicherheit und -schutz

Die fortlaufende Zugriffsevaluierung ist ein neues Sicherheitsfeature, das bei Ereignissen in Azure AD (z. B. Löschen von Benutzerkonten) die Durchsetzung von Richtlinien in nahezu Echtzeit bei den vertrauenden Seiten ermöglicht, die Azure AD-Zugriffstoken verwenden. Dieses Feature wird zuerst für Teams- und Outlook-Clients eingeführt. Weitere Einzelheiten finden Sie in unserem [Blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) und der [Dokumentation](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-Anmeldung: Mitarbeiter in Service und Produktion können sich bei Anwendungen auf Basis von Azure AD mit ihrer Telefonnummer und ohne Kennwort anmelden

**Typ:** Neues Feature

**Dienstkategorie**: Authentifizierungen (Anmeldungen)

**Produktfunktion:** Benutzerauthentifizierung

Office startet eine Reihe von vorwiegend mobilen Business-Apps, die sich an unkonventionelle Organisationen und an Mitarbeiter in großen Organisationen richten, die E-Mails nicht als primäre Kommunikationsmethode verwenden. Zielgruppe für diese Apps sind Mitarbeiter in Service und Produktion, Mitarbeiter ohne PC-gestützten Arbeitsplatz, Außendienstmitarbeiter oder Mitarbeiter im Einzelhandel, die möglicherweise von Ihrem Arbeitgeber keine E-Mail-Adresse erhalten oder auf einen Computer bzw. Informationstechnologie zugreifen können. Dieses Projekt bietet diesen Mitarbeiter die Möglichkeit, sich durch Eingabe einer Telefonnummer und Austausch eines Codes bei Geschäftsanwendungen anzumelden. Weitere Informationen finden Sie in unserer [Administratordokumentation](../authentication/howto-authentication-sms-signin.md) und der [Endbenutzerdokumentation](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Einladen von internen Benutzern zur Verwendung der B2B-Zusammenarbeit

**Typ:** Neues Feature

**Dienstkategorie:** B2B

**Produktfunktion:**

Die B2B-Einladungsfunktion wird derzeit erweitert, damit in Zukunft vorhandene interne Konten zur Verwendung von Anmeldeinformationen für die B2B-Zusammenarbeit eingeladen werden können. Zu diesem Zweck wird neben den typischen Parametern (z. B. E-Mail-Adresse des Eingeladenen) das Benutzerobjekt an die Einladungs-API übergeben. Objekt-ID, UPN, Gruppenmitgliedschaft, App-Zuweisung usw. des Benutzers bleiben unverändert. In Zukunft verwendet er jedoch B2B zum Authentifizieren mit den Anmeldeinformationen seines Basismandanten und nicht die internen, vor der Einladung verwendeten Anmeldeinformationen. Ausführliche Informationen hierzu finden Sie in der [Dokumentation](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Reiner Berichtsmodus für den bedingten Zugriff jetzt allgemein verfügbar

**Typ:** Neues Feature

**Dienstkategorie**: Bedingter Zugriff

**Produktfunktion:** Identitätssicherheit und -schutz

Mit dem [reinen Berichtsmodus für den bedingten Azure AD-Zugriff](../conditional-access/concept-conditional-access-report-only.md) können Sie das Ergebnis einer Richtlinie auswerten, ohne die Zugriffssteuerungen zu erzwingen. Im reinen Berichtsmodus können Sie Richtlinien in Ihrer Organisation vor der Aktivierung testen und deren Auswirkungen verstehen, sodass die Bereitstellung sicherer und einfacher wird. In den letzten Monaten haben wir eine hohe Akzeptanz des reinen Berichtsmodus festgestellt. Richtlinien im reinen Berichtsmodus weisen bereits einen Umfang von mehr als 26 Millionen Benutzer auf. Mit dieser Ankündigung werden neue Richtlinien für den bedingten Azure AD-Zugriff standardmäßig im reinen Berichtsmodus erstellt. Dies bedeutet, dass Sie die Auswirkungen Ihrer Richtlinien vom Zeitpunkt der Erstellung an überwachen können. Benutzer der MS Graph-APIs können zudem [Richtlinien im reinen Berichtsmodus programmgesteuert verwalten](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbeitsmappe für Erkenntnisse und Berichterstellung für den bedingten Zugriff jetzt allgemein verfügbar

**Typ:** Neues Feature

**Dienstkategorie**: Bedingter Zugriff

**Produktfunktion:** Identitätssicherheit und -schutz

Die [Arbeitsmappe für Erkenntnisse und Berichterstellung](../conditional-access/howto-conditional-access-insights-reporting.md) für den bedingten Zugriff bietet Administratoren eine Zusammenfassungsansicht des bedingten Azure AD-Zugriffs in ihrem Mandanten. Mit der Möglichkeit, eine einzelne Richtlinie auszuwählen, können Administratoren besser verstehen, was jede Richtlinie bewirkt, und Änderungen in Echtzeit überwachen. Die Arbeitsmappe streamt die in Azure Monitor gespeicherten Daten, die Sie [anhand dieser Anweisungen](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) in wenigen Minuten einrichten können. Damit das Dashboard leichter zu finden ist, haben wir es auf die neue Registerkarte „Erkenntnisse und Berichte“ im Menü „Bedingter Azure AD-Zugriff“ verschoben.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Blatt „Richtliniendetails“ für den bedingten Zugriff als öffentliche Vorschau verfügbar

**Typ:** Neues Feature

**Dienstkategorie**: Bedingter Zugriff

**Produktfunktion:** Identitätssicherheit und -schutz

Auf dem neuen Blatt [Richtliniendetails](../conditional-access/troubleshoot-conditional-access.md) wird angezeigt, welche Zuweisungen, Bedingungen und Steuerelemente während der Auswertung der Richtlinie für bedingten Zugriff erfüllt wurden. Sie können auf das Blatt zugreifen, indem Sie in den Anmeldedetails auf der Registerkarte **Bedingter Zugriff** oder der Registerkarte **Nur Bericht** eine Zeile auswählen.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – April 2020

**Typ:** Neues Feature

**Dienstkategorie**: Unternehmens-Apps

**Produktfunktion:** Integration von Drittanbieterprodukten

Im April 2020 haben wir diese 31 neuen Apps mit Verbundunterstützung in den App-Katalog aufgenommen: 

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO for Jira](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365 Mover](https://app.mover.io/login), [Speaker Engage](https://speakerengage.com/login.php), [Honestly](../saas-apps/honestly-tutorial.md), [Ally](../saas-apps/ally-tutorial.md), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 People](../saas-apps/gr8-people-tutorial.md), [Pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [Harmony](../saas-apps/harmony-tutorial.md), [Timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet CLICK](../saas-apps/synchronet-click-tutorial.md), [empower](https://www.made-in-office.com/en/), [Fortes Change Cloud](../saas-apps/fortes-change-cloud-tutorial.md), [Litmus](../saas-apps/litmus-tutorial.md), [GroupTalk](https://recorder.grouptalk.com/), [Frontify](../saas-apps/frontify-tutorial.md), [MongoDB Cloud](../saas-apps/mongodb-cloud-tutorial.md), [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md), [COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md), [Trend Micro Web Security (TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Unterstützung der Delta-Abfrage in Microsoft Graph für oAuth2PermissionGrant in der öffentlichen Vorschau verfügbar

**Typ:** Neues Feature

**Dienstkategorie:** MS Graph

**Produktfunktion:** Entwickleroberfläche

Die Delta-Abfrage für oAuth2PermissionGrant ist in der öffentlichen Vorschau verfügbar. Sie können jetzt Änderungen nachverfolgen, ohne kontinuierlich Microsoft Graph abfragen zu müssen. [Weitere Informationen.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Unterstützung der Delta-Abfrage in Microsoft Graph für Organisationskontakte jetzt allgemein verfügbar

**Typ:** Neues Feature

**Dienstkategorie:** MS Graph

**Produktfunktion:** Entwickleroberfläche

Die Delta-Abfrage für Organisationskontakte ist allgemein verfügbar. Sie können jetzt Änderungen an Produktions-Apps nachverfolgen, ohne kontinuierlich Microsoft Graph abfragen zu müssen. Ersetzen Sie vorhandenen Code, der kontinuierlich orgContact-Daten abruft, durch die Delta-Abfrage, um die Leistung deutlich zu verbessern. [Weitere Informationen.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Unterstützung der Delta-Abfrage in Microsoft Graph für Anwendungen allgemein verfügbar

**Typ:** Neues Feature

**Dienstkategorie:** MS Graph

**Produktfunktion:** Entwickleroberfläche

Die Delta-Abfrage für Anwendungen ist allgemein verfügbar. Sie können jetzt Änderungen an Produktions-Apps nachverfolgen, ohne kontinuierlich Microsoft Graph abfragen zu müssen. Ersetzen Sie vorhandenen Code, der kontinuierlich Anwendungsdaten abruft, durch die Delta-Abfrage, um die Leistung deutlich zu verbessern. [Weitere Informationen.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Unterstützung der Delta-Abfrage in Microsoft Graph für Verwaltungseinheiten in der öffentlichen Vorschau verfügbar

**Typ:** Neues Feature

**Dienstkategorie:** MS Graph

**Produktfunktion:** In der Entwicklerumgebung ist die Delta-Abfrage für Verwaltungseinheiten in der öffentlichen Vorschau verfügbar! Sie können jetzt Änderungen nachverfolgen, ohne kontinuierlich Microsoft Graph abfragen zu müssen. [Weitere Informationen.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Verwalten von Telefonnummern für die Authentifizierung und mehr in neuen Microsoft Graph-Beta-APIs

**Typ:** Neues Feature

**Dienstkategorie:** MS Graph

**Produktfunktion:** Entwickleroberfläche

Diese APIs sind ein wichtiges Tool zum Verwalten der Authentifizierungsmethoden Ihrer Benutzer. Sie können jetzt programmgesteuert die für MFA und die Self-Service-Kennwortzurücksetzung (SSPR) verwendeten Authentifikatoren vorab registrieren und verwalten. Hierbei handelt es sich um eine der am häufigsten gewünschten Funktionen im Bereich Azure AD MFA, SSPR und Microsoft Graph. Mit den neuen APIs in dieser Veröffentlichungswelle haben Sie folgende Möglichkeiten:

- Lesen, Hinzufügen, Aktualisieren und Entfernen von Telefonnummern zum Authentifizieren des Benutzers
- Zurücksetzen des Kennworts für einen Benutzer
- Aktivieren und Deaktivieren der SMS-Anmeldung

Weitere Informationen finden Sie unter [Übersicht über die Azure AD-Authentifizierungsmethoden-API](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Verwaltungseinheiten – öffentliche Vorschau

**Typ:** Neues Feature

**Dienstkategorie:** Azure AD-Rollen

**Produktfunktion:** Zugriffssteuerung

Verwaltungseinheiten ermöglichen es Ihnen, Administratorberechtigungen zu erteilen, die auf eine von Ihnen definierte Abteilung, Region oder ein anderes Segment Ihrer Organisation beschränkt sind. Mithilfe von Verwaltungseinheiten können Sie Berechtigungen an regionale Administratoren delegieren oder Richtlinien auf granularer Ebene festlegen. Beispielsweise kann ein Benutzerkontoadministrator nur in seiner Verwaltungseinheit Profilinformationen aktualisieren, Kennwörter zurücksetzen und Benutzern Lizenzen zuweisen.

Mit Verwaltungseinheiten kann ein zentraler Administrator folgende Aktionen ausführen:

- Erstellen einer Verwaltungseinheit für die dezentrale Verwaltung von Ressourcen
- Zuweisen einer Rolle mit Administratorberechtigungen nur für Azure AD-Benutzer in einer Verwaltungseinheit
- Auffüllen der Verwaltungseinheiten mit Benutzern und Gruppen nach Bedarf

Weitere Informationen finden Sie unter [Verwalten von Verwaltungseinheiten in Azure Active Directory (Vorschau)](../roles/administrative-units.md).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Integrierte Rollen für Druckeradministrator und Druckertechniker

**Typ:** Neues Feature

**Dienstkategorie:** Azure AD-Rollen

**Produktfunktion:** Zugriffssteuerung

**Druckeradministrator**: Benutzer mit dieser Rolle können Drucker registrieren und alle Aspekte der gesamten Druckerkonfiguration in der Microsoft Universal Print-Lösung verwalten, einschließlich der Microsoft Universal Print-Connectoreinstellungen. Sie können in alle delegierten Druckberechtigungsanforderungen einwilligen. Druckeradministratoren haben außerdem Zugriff auf Druckberichte. 

**Druckertechniker**: Benutzer mit dieser Rolle können Drucker registrieren und den Druckerstatus in der Microsoft Universal Print-Lösung verwalten. Sie können außerdem alle Connectorinformationen lesen. Zu den Hauptaufgaben, die ein Druckertechniker nicht ausführen kann, gehören das Festlegen von Benutzerberechtigungen für Drucker sowie das Freigeben von Druckern. [Weitere Informationen.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Integrierte Rolle für Hybrididentitätsadministrator

**Typ:** Neues Feature

**Dienstkategorie:** Azure AD-Rollen

**Produktfunktion:** Zugriffssteuerung

Benutzer mit dieser Rolle können Dienste und Einstellungen im Zusammenhang mit der Aktivierung der Hybrididentität in Azure AD aktivieren, konfigurieren und verwalten. Diese Rolle bietet die Möglichkeit, Azure AD für eine der drei unterstützten Authentifizierungsmethoden – Kennworthashsynchronisierung, Pass-Through-Authentifizierung oder Verbund (AD FS oder Drittanbieterverbund) – zu konfigurieren und eine zugehörige lokale Infrastruktur für die Aktivierung bereitzustellen. Die lokale Infrastruktur umfasst Bereitstellungs- und PTA-Agents. Diese Rolle gewährt die Fähigkeit, S-SSO (Seamless Single Sign-On, nahtloses einmaliges Anmelden) zu aktivieren, um eine nahtlose Authentifizierung auf Geräten ohne Windows 10 bzw. Computern ohne Windows Server 2016 zu ermöglichen. Außerdem bietet diese Rolle zur Überwachung und Problembehandlung die Möglichkeit, Anmeldeprotokolle anzuzeigen und auf Integritäts- und Analysedaten zuzugreifen. [Weitere Informationen.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Integrierte Rolle für Netzwerkadministrator

**Typ:** Neues Feature

**Dienstkategorie:** Azure AD-Rollen

**Produktfunktion:** Zugriffssteuerung

Benutzer mit dieser Rolle können Empfehlungen zur Netzwerkumkreisarchitektur von Microsoft überprüfen, die auf Netzwerktelemetriedaten von Benutzerstandorten basieren. Die Netzwerkleistung für Microsoft 365 basiert auf einer sorgfältigen Netzwerkumkreisarchitektur für Unternehmenskunden, die im Allgemeinen für den Benutzerstandort spezifisch ist. Diese Rolle ermöglicht das Bearbeiten von ermittelten Benutzerstandorten und das Konfigurieren von Netzwerkparametern für diese Standorte, um verbesserte Telemetriemessungen und Entwurfsempfehlungen zu ermöglichen. [Weitere Informationen.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Massenaktivitäten und -Downloads im Azure AD-Verwaltungsportal

**Typ:** Neues Feature

**Dienstkategorie:** Benutzerverwaltung

**Produktfunktion**: Verzeichnis

Jetzt können Sie Massenaktivitäten für Benutzer und Gruppen in Azure AD ausführen, indem Sie eine CSV-Datei in das Azure AD-Verwaltungsportal hochladen. Sie können Benutzer erstellen, Benutzer löschen und Gastbenutzer einladen. Sie können außerdem Mitglieder zu einer Gruppe hinzufügen und daraus entfernen.

Sie können auch Listen von Azure AD-Ressourcen aus dem Azure AD-Verwaltungsportal herunterladen. Sie können eine Liste der Benutzer im Verzeichnis, eine Liste der Gruppen im Verzeichnis und die Mitglieder einer bestimmten Gruppe herunterladen.

Weitere Informationen finden Sie über die folgenden Links:

- [Erstellen von Benutzern](../enterprise-users/users-bulk-add.md) oder [Einladen von Gastbenutzern](../external-identities/tutorial-bulk-invite.md)
- [Löschen von Benutzern](../enterprise-users/users-bulk-delete.md) oder [Wiederherstellen gelöschter Benutzer](../enterprise-users/users-bulk-restore.md)
- [Herunterladen der Benutzerliste](../enterprise-users/users-bulk-download.md) oder [Herunterladen der Gruppenliste](../enterprise-users/groups-bulk-download.md)
- [Hinzufügen (Importieren) von Mitgliedern](../enterprise-users/groups-bulk-import-members.md) oder [Entfernen von Mitgliedern](../enterprise-users/groups-bulk-remove-members.md) oder [Herunterladen der Mitgliederliste](../enterprise-users/groups-bulk-download-members.md) für eine Gruppe

---

### <a name="my-staff-delegated-user-management"></a>Delegierte Benutzerverwaltung „Meine Mitarbeiter“

**Typ:** Neues Feature

**Dienstkategorie:** Benutzerverwaltung

**Produktfunktion:**

„Meine Mitarbeiter“ bietet Managern in Service und Produktion (z. B. Geschäftsführer) die Möglichkeit sicherzustellen, dass ihre Mitarbeiter auf ihre Azure AD-Konten zugreifen können. Anstatt einen zentralen Helpdesk zu verwenden, können Organisationen häufige Aufgaben wie das Zurücksetzen von Kennwörtern oder das Ändern von Telefonnummern an einen Manager in Service oder Produktion delegieren. Bei Verwendung von „Meine Mitarbeiter“ kann ein Benutzer, der nicht auf sein Konto zugreifen kann, ohne die Hilfe des Helpdesks oder der IT-Abteilung mit wenigen Klicks den Zugriff wiedererlangen. Weitere Informationen finden Sie unter [Verwalten von Benutzern mit „Meine Mitarbeiter“ (Vorschau)](../roles/my-staff-configure.md) und [Delegieren der Benutzerverwaltung mit „Meine Mitarbeiter“ (Vorschau)](../user-help/my-staff-team-manager.md).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Aktualisierte Endbenutzerumgebung für Zugriffsüberprüfungen

**Typ:** Geändertes Feature

**Dienstkategorie**: Zugriffsüberprüfungen

**Produktfunktion:** Identity Governance

Wir haben die Überprüfungsumgebung für Azure AD-Zugriffsüberprüfungen im Portal „Meine Apps“ aktualisiert. Ab Ende April wird Ihren Prüfern, die in der Überprüfungsumgebung der Azure AD-Zugriffsüberprüfungen angemeldet sind, ein Banner angezeigt, das ihnen ermöglicht, die aktualisierte Umgebung in „Mein Zugriff“ zu testen. Beachten Sie, dass die aktualisierte Umgebung für Zugriffsüberprüfungen die gleiche Funktionalität hat wie die aktuelle Umgebung, jedoch neben neuen Funktionen auch eine verbesserte Benutzeroberfläche aufweist, damit Ihre Benutzer produktiv arbeiten können. [Hier](../governance/perform-access-review.md) erfahren Sie mehr über die aktualisierte Umgebung. Diese öffentliche Vorschauversion gilt bis Ende Juli 2020. Ab Ende Juli werden Prüfer, die sich nicht für die Vorschauumgebung entschieden haben, automatisch zu „Mein Zugriff“ geleitet, um Zugriffsüberprüfungen durchzuführen. Wenn Ihre Prüfer jetzt schon dauerhaft auf die Vorschauumgebung in „Mein Zugriff“ umgestellt werden sollen, können Sie dies [hier](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u) anfordern.

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday-Apps für die eingehende Benutzerbereitstellung und das Rückschreiben von Benutzern unterstützen jetzt die neuesten Versionen der Workday Web Services-API

**Typ:** Geändertes Feature

**Dienstkategorie:** App-Bereitstellung

**Produktfunktion:** 

Nach dem Feedback unserer Kunden haben wir nun die Workday-Apps für die eingehende Benutzerbereitstellung und das Rückschreiben von Benutzern im Katalog für Unternehmensanwendungen aktualisiert, um die neuesten Versionen der Workday Web Services (WWS)-API zu unterstützen. Mit dieser Änderung können Kunden die Version der WWS-API angeben, die sie in der Verbindungszeichenfolge verwenden möchten. Dadurch haben die Kunden die Möglichkeit, weitere Human Resources-Attribute abzurufen, die in den Workday-Versionen verfügbar sind. Die Workday-App zum Rückschreiben verwendet nun den empfohlenen Workday-Webdienst „Change_Work_Contact_Info“, um die Einschränkungen von „Maintain_Contact_Info“ zu beseitigen.

Wenn in der Verbindungszeichenfolge keine Version angegeben ist, verwenden die Workday-Apps für die eingehende Benutzerbereitstellung weiterhin WWS v21.1. Um zu den neuesten Workday-APIs für die eingehende Benutzerbereitstellung zu wechseln, müssen Kunden die Verbindungszeichenfolge, wie im [Tutorial](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) dokumentiert, und zusätzlich den für Workday-Attribute verwendeten XPath-Code aktualisieren, wie im [Referenzhandbuch für Workday-Attribute](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) beschrieben. 

Um die neue API für das Rückschreiben zu verwenden, sind in der Workday-App zum Bereitstellen des Rückschreibens keine Änderungen erforderlich. Stellen Sie auf Seiten von Workday sicher, dass das Konto „Workday Integration System User“ (ISU) über Berechtigungen zum Aufrufen des Geschäftsprozesses „Change_Work_Contact“ verfügt, wie im Tutorial unter [Konfigurieren der Sicherheitsrichtlinienberechtigungen für Geschäftsprozesse](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions) beschrieben. 

Wir haben die [Anleitung in unserem Tutorial](../saas-apps/workday-inbound-tutorial.md) aktualisiert, um die Unterstützung der neuen API-Version widerzuspiegeln.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Benutzer mit der Rolle „Standardzugriff“ jetzt im Bereitstellungsumfang enthalten

**Typ:** Geändertes Feature

**Dienstkategorie:** App-Bereitstellung

**Produktfunktion:** Identity Lifecycle Management

In der Vergangenheit waren Benutzer mit der Rolle „Standardzugriff“ nicht im Bereitstellungsumfang enthalten. Aus dem Kundenfeedback ging der Wunsch hervor, dass Benutzer mit dieser Rolle im Bereitstellungsumfang enthalten sein sollten. Ab dem 16. April 2020 werden Benutzer mit der Rolle „Standardzugriff“ in allen neuen Bereitstellungskonfigurationen berücksichtigt. Wir werden das Verhalten der vorhandenen Bereitstellungskonfigurationen schrittweise ändern, um die Bereitstellung von Benutzern mit dieser Rolle zu unterstützen. [Weitere Informationen.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Aktualisierte Benutzeroberfläche für die Bereitstellung

**Typ:** Geändertes Feature

**Dienstkategorie:** App-Bereitstellung

**Produktfunktion:** Identity Lifecycle Management

Wir haben die Bereitstellungsumgebung aktualisiert, um eine stärker fokussierte Verwaltungsansicht zu erstellen. Wenn Sie zum Blatt für die Bereitstellung einer Unternehmensanwendung navigieren, die bereits konfiguriert wurde, können Sie den Fortschritt der Bereitstellung und die Verwaltung von Aktionen (z. B. Starten, Beenden und Neustarten der Bereitstellung) problemlos überwachen. [Weitere Informationen.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Dynamische Gruppenregelüberprüfung jetzt in der öffentlichen Vorschau verfügbar

**Typ:** Geändertes Feature

**Dienstkategorie**: Gruppenverwaltung

**Produktfunktion:** Kollaboration

Azure Active Directory (Azure AD) bietet nun die Möglichkeit, dynamische Gruppenregeln zu überprüfen. Auf der Registerkarte **Regeln überprüfen** können Sie Ihre dynamische Regel anhand von Beispielgruppenmitgliedern überprüfen, um zu bestätigen, dass die Regel erwartungsgemäß funktioniert. Beim Erstellen oder Aktualisieren von Regeln für dynamische Gruppen möchten Administratoren wissen, ob ein Benutzer oder ein Gerät Mitglied der Gruppe sein wird. So können sie besser auswerten, ob ein Benutzer oder Gerät die Regelkriterien erfüllt und eine entsprechende Problembehandlung durchführen, wenn keine Mitgliedschaft erwartet wird.

Weitere Informationen finden Sie unter [Überprüfen einer dynamischen Gruppenmitgliedschaftsregel (Vorschau)](../enterprise-users/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Identity Secure Score – Aktualisierungen von Sicherheitsstandards und MFA-Verbesserungsaktionen

**Typ:** Geändertes Feature

**Dienstkategorie:** Nicht verfügbar

**Produktfunktion:** Identitätssicherheit und -schutz

**Unterstützung von Sicherheitsstandards für Azure AD Verbesserungsaktionen:** Bei Microsoft Secure Score werden die Verbesserungsaktionen aktualisiert, um die [Sicherheitsstandards in Azure AD](./concept-fundamentals-security-defaults.md) zu unterstützen. Dadurch wird es einfacher, Ihre Organisation mit vorkonfigurierten Sicherheitseinstellungen vor häufigen Angriffen zu schützen. Dies betrifft die folgenden Verbesserungsaktionen:

- Sicherstellen, dass alle Benutzer MFA für den sicheren Zugriff durchführen können
- Anfordern der MFA für Administratorrollen
- Aktivieren der Richtlinie zum Blockieren älterer Authentifizierungsmethoden
 
**Aktualisierungen für MFA-Verbesserungaktionen:** Unter Berücksichtigung der Tatsache, dass Unternehmen bei der Anwendung von funktionierenden Richtlinien für ihr Unternehmen höchste Sicherheit gewährleisten müssen, hat Microsoft Secure Score drei Verbesserungsaktionen im Zusammenhang mit Multi-Factor Authentication (MFA) entfernt und zwei hinzugefügt.

Entfernte Verbesserungsaktionen:

- Registrieren aller Benutzer für Multi-Factor Authentication (MFA)
- Anfordern der MFA für alle Benutzer
- Anfordern der MFA für privilegierte Azure AD-Rollen

Hinzugefügte Verbesserungsaktionen:

- Sicherstellen, dass alle Benutzer MFA für den sicheren Zugriff durchführen können
- Anfordern der MFA für Administratorrollen

Diese neuen Verbesserungsaktionen setzen voraus, dass sich Benutzer oder Administratoren in Ihrem Verzeichnis für die mehrstufige Authentifizierung (MFA) registrieren und den richtigen Satz von Richtlinien einrichten, die den Anforderungen Ihrer Organisation entsprechen. Das Hauptziel besteht darin, flexibel zu bleiben und gleichzeitig sicherzustellen, dass sich alle Benutzer und Administratoren mit mehrstufigen oder risikobasierten Eingabeaufforderungen für die Identitätsüberprüfung authentifizieren können. Dies kann das Einrichten mehrerer Richtlinien für die Anwendung bereichsbezogener Entscheidungen oder das Festlegen von Sicherheitsstandards (ab 16. März) bedeuten, die Microsoft entscheiden lassen, wann Benutzer zur mehrstufigen Authentifizierung (MFA) aufgefordert werden sollen. [Erfahren Sie mehr über die Neuerungen in Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score#whats-new).

---

## <a name="march-2020"></a>März 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Nicht verwaltete Azure Active Directory-Konten im B2B-Update für März 2021

**Typ:** Plan für Änderung  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
**Ab dem 31. März 2021** wird das Einlösen von Einladungen durch die Erstellung von nicht verwalteten Azure Active Directory-Konten und -Mandanten für B2B-Zusammenarbeitsszenarien von Microsoft nicht mehr unterstützt. Zur Vorbereitung hierauf raten wir Ihnen, sich für die [Authentifizierung mit Einmalkennung per E-Mail](../external-identities/one-time-passcode.md) zu entscheiden.

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Benutzer mit der Rolle „Standardzugriff“ im Bereitstellungsumfang enthalten

**Typ:** Plan für Änderung  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Identity Lifecycle Management
 
In der Vergangenheit waren Benutzer mit der Rolle „Standardzugriff“ nicht im Bereitstellungsumfang enthalten. Aus dem Kundenfeedback ging der Wunsch hervor, dass Benutzer mit dieser Rolle im Bereitstellungsumfang enthalten sein sollten. Wir arbeiten an der Implementierung einer Änderung, damit Benutzer mit der Rolle „Standardzugriff“ in allen neuen Bereitstellungskonfigurationen berücksichtigt werden. Wir werden schrittweise das Verhalten der bereits vorhandenen Bereitstellungskonfigurationen ändern, um die Bereitstellung von Benutzern mit dieser Rolle zu unterstützen. Es ist keine Kundenaktion erforderlich. Wir werden in unserer [Dokumentation](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) ein Update veröffentlichen, sobald diese Änderung vorgenommen wurde.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B-Zusammenarbeit in von 21Vianet-Mandanten (Azure China 21Vianet) betriebenen Microsoft Azure-Instanzen verfügbar

**Typ:** Plan für Änderung  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Die Funktionen der Azure AD B2B-Zusammenarbeit werden in Microsoft Azure-Instanzen verfügbar gemacht, die von 21Vianet-Mandanten (Azure China 21Vianet) betrieben werden. Dadurch können Benutzer in einem Azure China 21Vianet-Mandanten nahtlos mit Benutzern in anderen Azure China 21Vianet-Mandanten zusammenarbeiten. [Weitere Informationen zur Azure AD B2B-Zusammenarbeit](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Neugestaltung der Einladungs-E-Mail für die Azure AD B2B-Zusammenarbeit

**Typ:** Plan für Änderung  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Die [E-Mails](../external-identities/invitation-email-elements.md), die der Einladungsdienst für die Zusammenarbeit in Azure AD B2B sendet, um Benutzer in das Verzeichnis einzuladen, werden neu gestaltet, um die Einladungsinformationen und die nächsten Schritte des Benutzers besser zu verdeutlichen.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Anzeigen von HomeRealmDiscovery-Richtlinienänderungen in Überwachungsprotokollen

**Typ:** Korrigiert  
**Dienstkategorie:** Audit  
**Produktfunktion:** Überwachung und Berichterstellung
 
Bisher waren Änderungen an der [HomeRealmDiscovery-Richtlinie](../manage-apps/configure-authentication-for-federated-users-portal.md) nicht in den Überwachungsprotokollen enthalten. Diesen Fehler haben wir korrigiert. Jetzt können Sie erkennen, wann, wie und von wem die Richtlinie geändert wurde. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – März 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im März 2020 haben wir die folgenden 51 neuen Apps mit Verbundunterstützung in den App-Katalog aufgenommen: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One China](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML App](../saas-apps/profitco-saml-app-tutorial.md), [iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md), [contexxt.ai SPHERE](https://contexxt-sphere.com/login), [Wisdom By Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), [Flare Digital Signage](https://spark-dev.pixelnebula.com/login), [Logz.io – Cloud Observability for Engineers](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [SpectrumU](../saas-apps/spectrumu-tutorial.md), [BizzContact](https://bizzcontact.app/), [Elqano SSO](../saas-apps/elqano-sso-tutorial.md), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [Netvision Compas](../saas-apps/netvision-compas-tutorial.md), [FCM HUB](../saas-apps/fcm-hub-tutorial.md), [RIB A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](../saas-apps/golinks-tutorial.md), [Datadog](../saas-apps/datadog-tutorial.md), [Zscaler B2B User Portal](../saas-apps/zscaler-b2b-user-portal-tutorial.md), [LIFT](../saas-apps/lift-tutorial.md), [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [Skills Workflow](../saas-apps/skills-workflow-tutorial.md), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx), [IP Platform](../saas-apps/ip-platform-tutorial.md), [InVision](../saas-apps/invision-tutorial.md), [Pipedrive](../saas-apps/pipedrive-tutorial.md), [Showcase Workshop](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](../saas-apps/greenlight-integration-platform-tutorial.md), [Greenlight Compliant Access Management](../saas-apps/greenlight-compliant-access-management-tutorial.md), [Grok Learning](../saas-apps/grok-learning-tutorial.md), [Miradore Online](https://login.online.miradore.com/), [Khoros Care](../saas-apps/khoros-care-tutorial.md), [AskYourTeam](../saas-apps/askyourteam-tutorial.md), [TruNarrative](../saas-apps/trunarrative-tutorial.md), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](../saas-apps/britive-tutorial.md), [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-days](../saas-apps/e-days-tutorial.md), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn Ferry 360](../saas-apps/korn-ferry-360-tutorial.md), [Campus Café](../saas-apps/campus-cafe-tutorial.md), [Catchpoint](../saas-apps/catchpoint-tutorial.md), [Code42](../saas-apps/code42-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B-Zusammenarbeit in Azure Government-Mandanten verfügbar

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** B2B/B2C
 
Die Funktionen der Azure AD B2B-Zusammenarbeit sind jetzt für die Zusammenarbeit zwischen einigen Azure Government-Mandanten verfügbar.  Um festzustellen, ob Ihr Mandant diese Funktionen nutzen kann, befolgen Sie die Anweisungen unter [Wie kann ich erkennen, ob die B2B-Zusammenarbeit in meinem Mandanten der Azure-Cloud für US-Behörden verfügbar ist?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor-Integration für Azure-Protokolle jetzt in Azure Government verfügbar

**Typ:** Neues Feature  
**Dienstkategorie:** Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 
Die Integration von Azure AD-Protokollen in Azure Monitor ist jetzt in Azure Government verfügbar. Sie können Azure AD-Protokolle (Überwachungs- und Anmeldeprotokolle) an ein Speicherkonto, einen Event Hub und Log Analytics weiterleiten. Sehen Sie sich die [ausführliche Dokumentation](../reports-monitoring/concept-activity-logs-azure-monitor.md) sowie die [Bereitstellungspläne für die Berichterstellung und Überwachung](../reports-monitoring/plan-monitoring-and-reporting.md) für Azure AD-Szenarien an.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Identity Protection-Aktualisierung in Azure Government

**Typ:** Neues Feature  
**Dienstkategorie**: Identitätsschutz  
**Produktfunktion:** Identitätssicherheit und -schutz

Wir freuen uns, Ihnen mitteilen zu können, dass wir jetzt die aktualisierte [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) -Umgebung im [Microsoft Azure Government-Portal](https://portal.azure.us/) eingeführt haben. Weitere Informationen finden Sie in unserem [Ankündigungsblogbeitrag](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Notfallwiederherstellung: Herunterladen und Speichern der Bereitstellungskonfiguration

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Identity Lifecycle Management
 
Der Azure AD-Bereitstellungsdienst stellt umfangreiche Konfigurationsfunktionen bereit. Kunden müssen in der Lage sein, ihre Konfiguration zu speichern, um diese später verwenden oder einen Rollback auf eine als funktionierend bekannte Version durchführen zu können. Wir haben die Möglichkeit hinzugefügt, Ihre Bereitstellungskonfiguration als JSON-Datei herunterzuladen und bei Bedarf hochzuladen. [Weitere Informationen](../app-provisioning/export-import-provisioning-configuration.md)

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Die Self-Service-Kennwortzurücksetzung (SSPR) erfordert jetzt zwei Gates für Administratoren in von 21Vianet (Azure China 21Vianet) betriebenen Microsoft Azure-Instanzen 

**Typ:** Geändertes Feature  
**Dienstkategorie:** Self-Service-Kennwortzurücksetzung  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Bisher benötigten Administratoren, die in von 21Vianet (Azure China 21Vianet) betriebenen Microsoft Azure-Instanzen die Self-Service-Kennwortzurücksetzung (SSPR) zum Zurücksetzen ihrer eigenen Kennwörter verwendeten, nur ein „Gate“ (Abfrage), um ihre Identität zu beweisen. In öffentlichen und anderen nationalen Clouds müssen Administratoren bei der Verwendung von SSPR in der Regel zwei Gates verwenden, um ihre Identität nachzuweisen. Da in Azure China 21Vianet jedoch keine SMS oder Telefonanrufe unterstützt wurden, haben wir die Kennwortzurücksetzung von Administratoren über ein Gate erlaubt.

Wir sind jetzt dabei, zwischen Azure China 21Vianet und der öffentlichen Cloud gleiche Bedingungen für die SSPR-Funktion herzustellen. In Zukunft müssen Administratoren bei der Verwendung von SSPR zwei Gates verwenden. Unterstützt werden SMS, Telefonanrufe sowie Benachrichtigungen und Codes der Authenticator-App. [Weitere Informationen](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences)

---

### <a name="password-length-is-limited-to-256-characters"></a>Kennwortlänge auf 256 Zeichen beschränkt

**Typ:** Geändertes Feature  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
Um die Zuverlässigkeit des Azure AD-Diensts sicherzustellen, sind die Benutzerkennwörter jetzt auf 256 Zeichen beschränkt. Benutzer mit längeren Kennwörtern werden aufgefordert, ihr Kennwort bei der nächsten Anmeldung zu ändern, indem sie sich entweder an ihren Administrator wenden oder die SSPR-Funktion (Self-Service-Kennwortzurücksetzung) verwenden.

Diese Änderung ist am 13. März 2020, 10 Uhr PST (18 Uhr UTC), in Kraft getreten. Der Fehler lautet AADSTS 50052, InvalidPasswordExceedsMaxLength. Weitere Einzelheiten finden Sie in den [Hinweisen zu wichtigen Änderungen](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters).

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD-Anmeldeprotokolle jetzt für alle kostenlosen Mandanten über das Azure-Portal verfügbar

**Typ:** Geändertes Feature  
**Dienstkategorie:** Berichterstellung  
**Produktfunktion:** Überwachung und Berichterstellung
 
Ab sofort können Kunden mit kostenlosen Mandanten bis zu sieben Tage lang auf die [Azure AD-Anmeldeprotokolle im Azure-Portal](../reports-monitoring/concept-sign-ins.md) zugreifen. Bisher waren Anmeldeprotokolle nur für Kunden mit Azure Active Directory Premium-Lizenzen verfügbar. Durch diese Änderung können alle Mandanten über das Portal auf diese Protokolle zugreifen.

> [!NOTE]
> Kunden benötigen weiterhin eine Premium-Lizenz (Azure Active Directory Premium P1 oder P2), wenn sie über die Microsoft Graph-API und Azure Monitor auf die Anmeldeprotokolle zugreifen wollen.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Einstellung der Option für verzeichnisweite Gruppen in den allgemeinen Gruppeneinstellungen im Azure-Portal

**Typ:** Als veraltet markiert  
**Dienstkategorie**: Gruppenverwaltung  
**Produktfunktion:** Kollaboration

Um Kunden eine flexiblere Möglichkeit zu bieten, optimale verzeichnisweite Gruppen für ihre Anforderungen zu erstellen, haben wir die Option **verzeichnisweite Gruppen** der Einstellungen unter **Gruppen** > **Allgemein** im Azure-Portal durch einen Link zur [dynamischen Gruppendokumentation](../enterprise-users/groups-dynamic-membership.md) ersetzt. Wir haben unsere Dokumentation um zusätzliche Anweisungen erweitert, damit Administratoren Gruppen mit allen Benutzern (mit oder ohne Gastbenutzer) erstellen können.

---

## <a name="february-2020"></a>Februar 2020

### <a name="upcoming-changes-to-custom-controls"></a>Bevorstehende Änderungen an benutzerdefinierten Steuerelementen

**Typ:** Plan für Änderung  
**Dienstkategorie:** MFA  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Wir planen, die aktuell in der Vorschau befindlichen benutzerdefinierten Steuerelemente durch einen Ansatz zu ersetzen, der vom Partner bereitgestellte Authentifizierungsfunktionen erlaubt, um nahtlos mit Azure Active Directory-Administrator- und Endbenutzerumgebungen zusammenzuarbeiten. Derzeit unterliegen MFA-Partnerlösungen folgenden Einschränkungen: Sie funktionieren nur nach Eingabe eines Kennworts und dienen nicht als MFA für die Step-up-Authentifizierung in anderen wichtigen Szenarien. Zudem sind sie nicht in Verwaltungsfunktionen für Endbenutzer- oder administrative Anmeldeinformationen integriert. Mit der neuen Implementierung können vom Partner bereitgestellte Authentifizierungsfaktoren zusammen mit integrierten Faktoren für wichtige Szenarien verwendet werden. Hierzu zählen u.a. Registrierung, Nutzung, MFA-Ansprüche, Step-up-Authentifizierung, Berichterstellung und Protokollierung. 

Neben dem neuen Design werden benutzerdefinierte Steuerelemente weiterhin in der Vorschau unterstützt, bis die allgemeine Verfügbarkeit erreicht ist. Dadurch erhalten Kunden Zeit für die Migration zum neuen Design. Aufgrund der Einschränkungen des aktuellen Ansatzes werden neue Anbieter erst dann integriert, wenn das neue Design verfügbar ist. Wir arbeiten eng mit Kunden und Anbietern zusammen und werden Ihnen zu gegebener Zeit den zeitlichen Ablauf mitteilen. [Weitere Informationen](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identity Secure Score – Updates für Verbesserungsaktionen im Zusammenhang mit MFA

**Typ:** Plan für Änderung  
**Dienstkategorie:** MFA  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Unter Berücksichtigung der Tatsache, dass Unternehmen bei der Anwendung von funktionierenden Richtlinien für ihr Unternehmen höchste Sicherheit gewährleisten müssen, entfernt Microsoft Secure Score drei Verbesserungsaktionen im Zusammenhang mit Multi-Factor Authentication (MFA) und fügt zwei hinzu.

Die folgenden Verbesserungsaktionen werden entfernt:

- Registrieren aller Benutzer für MFA
- Anfordern der MFA für alle Benutzer
- Anfordern der MFA für privilegierte Azure AD-Rollen

Die folgenden Verbesserungsaktionen werden hinzugefügt:

- Für einen sicheren Zugriff wird gewährleistet, dass alle Benutzer die mehrstufige Authentifizierung (MFA) ausführen können
- Anfordern der MFA für Administratorrollen

Diese neuen Verbesserungsaktionen setzen voraus, dass sich Benutzer oder Administratoren in Ihrem Verzeichnis für die mehrstufige Authentifizierung (MFA) registrieren und den richtigen Satz von Richtlinien einrichten, die den Anforderungen Ihrer Organisation entsprechen. Das Hauptziel besteht darin, flexibel zu bleiben und gleichzeitig sicherzustellen, dass sich alle Benutzer und Administratoren mit mehrstufigen oder risikobasierten Eingabeaufforderungen für die Identitätsüberprüfung authentifizieren können. Dies kann das Festlegen von Sicherheitsstandards bedeuten, die Microsoft entscheiden lassen, wann Benutzer zur mehrstufigen Authentifizierung aufgefordert werden sollen, oder auch das Einrichten mehrerer Richtlinien für die Anwendung bereichsbezogener Entscheidungen. Diese Updates für die Verbesserungsaktionen sehen auch vor, dass Basisschutzrichtlinien nicht mehr in die Bewertungsberechnungen einbezogen werden. [Erfahren Sie mehr über die anstehenden Neuerungen in Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services – SKU-Auswahl

**Typ:** Neues Feature  
**Dienstkategorie**: Azure AD Domain Services  
**Produktfunktion**: Azure AD Domain Services
 
Dem Kundenfeedback zufolge wünschten sich Kunden von Azure AD Domain Services mehr Flexibilität bei der Auswahl von Leistungsstufen für ihre Instanzen. Ab dem 1. Februar 2020 haben wir das dynamische Modell (bei dem Azure AD anhand der Objektanzahl Leistung und Tarif ermittelte) auf ein Modell mit Selbstauswahl umgestellt. Kunden können nun eine Leistungsstufe auswählen, die ihrer Umgebung entspricht. Diese Änderung ermöglicht es uns auch, neue Szenarien wie Ressourcengesamtstrukturen und Premiumfunktionen wie tägliche Sicherungen zu aktivieren. Die Objektanzahl ist jetzt unbegrenzt für alle SKUs, wir bieten jedoch weiterhin Vorschläge für die Objektanzahl auf jeder Stufe an.

**Es ist keine direkte Kundenaktion erforderlich.** Bei Bestandskunden bestimmt die dynamische Stufe, die am 1. Februar 2020 verwendet wurde, die neue Standardstufe. Diese Änderung hat keine Auswirkungen auf Preise oder Leistung. In Zukunft müssen Azure AD DS-Kunden die Leistungsanforderungen auswerten, wenn sich Verzeichnisgröße und Workloadmerkmale ändern. Ein Wechsel zwischen den Dienstebenen ist auch in Zukunft ein unterbrechungsfreier Vorgang. Wir werden Kunden jedoch nicht mehr auf Basis des Verzeichniswachstums automatisch auf eine neue Ebene umstellen. Außerdem gibt es keine Preiserhöhung. Die neue Preisgestaltung entspricht dem aktuellen Abrechnungsmodell. Weitere Informationen finden Sie in der [Dokumentation zu Azure AD DS-SKUs](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) und auf der [Preisseite für Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Februar 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Februar 2020 haben wir die folgenden 31 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen: 

[IamIP Patent Platform](../saas-apps/iamip-patent-platform-tutorial.md), [Experience Cloud](../saas-apps/experience-cloud-tutorial.md), [NS1 SSO For Azure](../saas-apps/ns1-sso-azure-tutorial.md), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), [In Case of Crisis – Online Portal](../saas-apps/in-case-of-crisis-online-portal-tutorial.md), [BIC Cloud Design](../saas-apps/bic-cloud-design-tutorial.md), [Beekeeper Azure AD Data Connector](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md), [Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](../saas-apps/verkada-command-tutorial.md), [Splashtop](../saas-apps/splashtop-tutorial.md), [Syxsense](../saas-apps/syxsense-tutorial.md), [EAB Navigate](../saas-apps/eab-navigate-tutorial.md), [New Relic (Limited Release)](../saas-apps/new-relic-limited-release-tutorial.md), [Thulium](https://admin.thulium.com/login/instance), [Ticket Manager](../saas-apps/ticketmanager-tutorial.md), [Template Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [Health Support System](../saas-apps/health-support-system-tutorial.md), [MURAL](https://app.mural.co/signup), [Hive](../saas-apps/hive-tutorial.md), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](../saas-apps/firmex-vdr-tutorial.md), [ThingLink for Teachers and Schools](https://www.thinglink.com/), [Coda](../saas-apps/coda-tutorial.md), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](../saas-apps/wedo-tutorial.md), [InvitePeople](https://invitepeople.com/login), [Reprints Desk – Article Galaxy](../saas-apps/reprints-desk-article-galaxy-tutorial.md), [TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – Februar 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-Unterstützung für FIDO2-Sicherheitsschlüssel in Hybridumgebungen

**Typ:** Neues Feature  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
Wir geben die Azure AD-Unterstützung für FIDO2-Sicherheitsschlüssel in Hybridumgebungen (Public Preview) bekannt. Benutzer können sich jetzt mit FIDO2-Sicherheitsschlüsseln bei ihren hybrid in Azure AD eingebundenen Geräten unter Windows 10 anmelden und über die nahtlose Anmeldung (Seamless Sign-On, SSO) auf ihre lokalen und Cloudressourcen zugreifen. Die Unterstützung für Hybridumgebungen stand bei unseren Kunden mit kennwortloser Authentifizierung ganz oben auf der Wunschliste, da wir zunächst die FIDO2-Unterstützung für in Azure AD eingebundene Geräte in der Public Preview gestartet haben. Die kennwortlose Authentifizierung mit modernen Technologien wie biometrische Merkmale und Verschlüsselung mit öffentlichen/privaten Schlüsseln ist nicht nur bequem und benutzerfreundlich, sondern auch sicher. Mit dieser Public Preview haben Sie nun die Möglichkeit, eine moderne Authentifizierung wie FIDO2-Sicherheitsschlüssel für den Zugriff auf herkömmliche Active Directory-Ressourcen zu verwenden. Weitere Informationen finden Sie unter [Einmaliges Anmelden bei lokalen Ressourcen](../authentication/howto-authentication-passwordless-security-key-on-premises.md). 

Unter [Aktivieren von FIDO2-Sicherheitsschlüsseln für Ihren Mandanten](../authentication/howto-authentication-passwordless-security-key.md) finden Sie schrittweise Anleitungen, um mit der Verwendung zu beginnen. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Neue Umgebung „Mein Konto“ jetzt allgemein verfügbar

**Typ:** Geändertes Feature  
**Dienstkategorie:** Mein Profil/Konto  
**Produktfunktion:** Endbenutzerumgebungen
 
„Mein Konto“, die zentrale Anlaufstelle für alle Kontoverwaltungsaufgaben für Endbenutzer, ist jetzt allgemein verfügbar! Endbenutzer können über eine URL oder den Header der neuen Umgebung „Meine Apps“ auf die neue Site zugreifen. Unter [Übersicht über das Portal „Mein Konto“](../user-help/my-account-portal-overview.md) erfahren Sie mehr über die Selbstbedienungsfunktionen der neuen Umgebung.

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>URL der Site „Mein Konto“ aktualisiert auf myaccount.microsoft.com

**Typ:** Geändertes Feature  
**Dienstkategorie:** Mein Profil/Konto  
**Produktfunktion:** Endbenutzerumgebungen
 
Die URL für die neue Endbenutzerumgebung „Mein Konto“ wird nächsten Monat auf `https://myaccount.microsoft.com` aktualisiert. Weitere Informationen zur Umgebung und allen Kontoselbstbedienungsfunktionen für Endbenutzer finden Sie unter [Hilfe zum Portal „Mein Konto“](../user-help/my-account-portal-overview.md).

---

## <a name="january-2020"></a>Januar 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Das neue Portal „Meine Apps“ ist jetzt allgemein verfügbar

**Typ:** Plan für Änderung  
**Dienstkategorie:** Meine Apps  
**Produktfunktion:** Endbenutzerumgebungen
 
Führen Sie für Ihre Organisation ein Upgrade auf das neue Portal „Meine Apps“ durch, das jetzt allgemein verfügbar ist! Weitere Informationen zum neuen Portal und zu Sammlungen finden Sie unter [Erstellen von Sammlungen im Portal „Meine Apps“](../manage-apps/access-panel-collections.md).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Arbeitsbereiche in Azure AD wurden in Sammlungen umbenannt

**Typ:** Geändertes Feature  
**Dienstkategorie:** Meine Apps   
**Produktfunktion:** Endbenutzerumgebungen
 
Arbeitsbereiche (die Filter, die Administratoren zum Organisieren Ihrer Benutzer-Apps konfigurieren können) werden nun als Sammlungen bezeichnet. Weitere Informationen zum Konfigurieren dieser Sammlungen finden Sie unter [Erstellen von Sammlungen im Portal „Meine Apps“](../manage-apps/access-panel-collections.md).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Telefonregistrierung und -anmeldung mit benutzerdefinierten Richtlinien in Azure AD B2C (Öffentliche Vorschau)

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C
 
Mit der Telefonregistrierung und -anmeldung können Entwickler und Unternehmen ihren Kunden die Registrierung und Anmeldung mit einem Einmalkennwort ermöglichen, das per SMS an die Telefonnummer des Benutzers gesendet wird. Mit dieser Funktion kann der Kunden auch seine Telefonnummer ändern, falls er keinen Zugriff auf das Telefon mehr hat. Dank der Leistungsfähigkeit benutzerdefinierter Richtlinien sowie der telefonischen Registrierung und Anmeldung können Entwickler ihre Marke über die Seitenanpassung kommunizieren. Weitere Informationen finden Sie unter [Einrichten von Telefonregistrierung und -anmeldung mit benutzerdefinierten Richtlinien in Azure AD B2C](../../active-directory-b2c/phone-authentication.md).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Neue Bereitstellungsconnectors im Azure AD-Anwendungskatalog – Januar 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Neue Verbund-Apps im Azure AD-Anwendungskatalog – Januar 2020

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten
 
Im Januar 2020 haben wir die folgenden 33 neuen Apps mit Verbundunterstützung in den App-Katalog aufgenommen: 

[JOSA](../saas-apps/josa-tutorial.md), [Fastly Edge Cloud](../saas-apps/fastly-edge-cloud-tutorial.md), [Terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](../saas-apps/upshotly-tutorial.md), [LeaveBot](https://leavebot.io/#home), [DataCamp](../saas-apps/datacamp-tutorial.md), [TripActions](../saas-apps/tripactions-tutorial.md), [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](../saas-apps/dotcom-monitor-tutorial.md), [SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft und JDE](../saas-apps/ssogen-tutorial.md), [Hosted MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [Yuhu Property Management Platform](../saas-apps/yuhu-property-management-platform-tutorial.md), [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](../saas-apps/upwork-enterprise-tutorial.md), [Talentsoft](../saas-apps/talentsoft-tutorial.md), [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), [ContractSafe Saml2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md), [Maxient Conduct Manager Software](../saas-apps/maxient-conduct-manager-software-tutorial.md), [Helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [ PrinterLogic SaaS](../saas-apps/printerlogic-saas-tutorial.md), [Taskize Connect](../saas-apps/taskize-connect-tutorial.md), [Sandwai](https://app.sandwai.com/), [EZRentOut](../saas-apps/ezrentout-tutorial.md), [AssetSonar](../saas-apps/assetsonar-tutorial.md), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="two-new-identity-protection-detections"></a>Zwei neue Erkennungen in Identity Protection

**Typ:** Neues Feature  
**Dienstkategorie**: Identitätsschutz  
**Produktfunktion:** Identitätssicherheit und -schutz
 
Identity Protection wurde um zwei neue, mit der Anmeldung verknüpfte Erkennungstypen ergänzt: „Verdächtige Regeln zur Posteingangsänderung“ und „Unmöglicher Ortswechsel“. Diese Offline-Erkennungen werden von MACS (Microsoft Cloud App Security) erkannt und beeinflussen das Benutzer- und Anmelderisiko in Identity Protection. Weitere Informationen zu diesen Erkennungen finden Sie unter [Risikotypen für die Anmeldung](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Breaking Change: URI-Fragmente werden nicht über die Anmeldeumleitung übergeben

**Typ:** Geändertes Feature  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung
 
Ab dem 8. Februar 2020 fügt der Dienst ein leeres Fragment an die Anforderung an, wenn eine Anforderung an login.microsoftonline.com gesendet wird, um einen Benutzer anzumelden.  Dadurch wird eine Klasse von Umleitungsangriffen verhindert, indem sichergestellt wird, dass der Browser jedes vorhandene Fragment in der Anforderung bereinigt. Keine Anwendung sollte eine Abhängigkeit von diesem Verhalten aufweisen. Weitere Informationen finden Sie unter [Breaking Changes](../develop/reference-breaking-changes.md#february-2020) in der Dokumentation zu Microsoft Identity Platform.

---

## <a name="december-2019"></a>Dezember 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrieren der SAP SuccessFactors-Bereitstellung in Azure AD und lokale AD-Instanzen (Public Preview)

**Typ:** Neues Feature  
**Dienstkategorie:** App-Bereitstellung  
**Produktfunktion:** Identity Lifecycle Management

Nun können Sie SAP SuccessFactors als autorisierende Identitätsquelle in Azure AD integrieren. Diese Integration unterstützt Sie bei der Automatisierung des gesamten Identitätslebenszyklus, einschließlich der Verwendung HR-basierter Ereignissen wie Neueinstellungen oder Kündigungen, um die Bereitstellung von Azure AD-Konten zu steuern.

Weitere Informationen zur Einrichtung der eingehenden SAP SuccessFactors-Bereitstellung in Azure AD finden Sie im Tutorial [Konfigurieren der Benutzerbereitstellung von SAP SuccessFactors zu Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Unterstützung für angepasste E-Mails in Azure AD B2C (Public Preview)

**Typ:** Neues Feature  
**Dienstkategorie**: B2C – Kundenidentitätsverwaltung  
**Produktfunktion:** B2B/B2C

Mit Azure AD B2C können Sie nun angepasste E-Mails erstellen, wenn Ihre Benutzer sich für Ihre Apps registrieren. Mithilfe von Anzeigesteuerelementen (Vorschauversion) und einem E-Mail-Drittanbieter wie [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/) oder einer benutzerdefinierten REST-API können Sie eigene Vorlagen, **Absenderadressen** und Betrefftexte verwenden. Darüber hinaus werden die Lokalisierung und benutzerdefinierte Einstellungen für einmalige Kennwörter unterstützt.

Weitere Informationen finden Sie unter [Benutzerdefinierte E-Mail-Überprüfung in Azure Active Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Ersetzung von Baselinerichtlinien durch Sicherheitsstandards

**Typ:** Geändertes Feature  
**Dienstkategorie:** Andere  
**Produktfunktion:** Identitätssicherheit und -schutz

Als Teil eines standardmäßig sicheren Authentifizierungsmodells werden die vorhandenen Baselineschutzrichtlinien aus allen Mandanten entfernt. Diese Entfernung soll bis Ende Februar abgeschlossen sein. Sicherheitsstandards sind der Ersatz für diese Baselineschutzrichtlinien. Wenn Sie Baselineschutzrichtlinien verwenden, müssen Sie die Umstellung auf die neue Richtlinie mit Sicherheitsstandards oder den bedingten Zugriff planen. Wenn Sie diese Richtlinien nicht verwendet haben, müssen Sie keine Maßnahmen ergreifen.

Weitere Informationen zu den neuen Sicherheitsstandards finden Sie unter [Was sind Sicherheitsstandards?](./concept-fundamentals-security-defaults.md). Weitere Informationen über Richtlinien für den bedingten Zugriff finden Sie unter [Allgemeine Richtlinien für bedingten Zugriff](../conditional-access/concept-conditional-access-policy-common.md).

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Unterstützung für das SameSite-Attribut und Chrome 80

**Typ:** Plan für Änderung  
**Dienstkategorie**: Authentifizierungen (Anmeldungen)  
**Produktfunktion:** Benutzerauthentifizierung

Im Rahmen des standardmäßig sicheren Modells für Cookies wird geändert, wie der Chrome 80-Browser Cookies ohne `SameSite`-Attribut verarbeitet. Cookies ohne das `SameSite`-Attribut werden so behandelt, als wäre dieses auf `SameSite=Lax` festgelegt. Dadurch blockiert Chrome bestimmte domänenübergreifenden Cookiefreigaben, von denen Ihre App möglicherweise abhängig ist. Sie können das bisherige Verhalten von Chrome beibehalten, indem Sie das `SameSite=None`-Attribut verwenden und das `Secure`-Attribut hinzufügen, damit auf seitenübergreifende Cookies nur über HTTPS-Verbindungen zugegriffen werden kann. Diese Änderung in Chrome soll bis zum 4. Februar 2020 abgeschlossen sein.

Wir empfehlen unseren Entwicklern, ihre Apps anhand dieses Leitfadens zu testen:

- Legen Sie den Standardwert für die Einstellung **Sicheres Cookie verwenden** auf **Ja** fest.

- Legen Sie den Standardwert für das **SameSite**-Attribut auf **Keine** fest.

- Fügen Sie ein zusätzliches `SameSite`-Attribut von **Secure** hinzu.

Weitere Informationen finden Sie unter [Kommende Änderungen an SameSite-Cookies in ASP.NET und ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) und [Mögliche Unterbrechung bei Kundenwebsites und Microsoft-Produkten und -Diensten in Chrome-Version 79 und höher](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Neuer Hotfix für Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Typ:** Korrigiert  
**Dienstkategorie:** Microsoft Identity Manager  
**Produktfunktion:** Identity Lifecycle Management

Ein Hotfixrolluppaket (Build 4.6.34.0) ist für Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) verfügbar. Dieses Rolluppaket behebt Probleme und implementiert Verbesserungen, die im Abschnitt „Behobene Probleme und Verbesserungen in diesem Update“ beschrieben werden.

Weitere Informationen und das downloadbare Hotfixpaket finden Sie unter [Microsoft Identity Manager 2016 Service Pack 2 (Build 4.6.34.0) Updaterollup](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Neuer AD FS-Bericht zu App-Aktivitäten zum Migrieren von Apps zu Azure AD (Public Preview)

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** SSO

Verwenden Sie den neuen Bericht zu App-Aktivitäten für Active Directory-Verbunddienste (AD FS) im Azure-Portal, um zu ermitteln, welche Ihrer Apps zu Azure AD migriert werden können. Im Bericht werden alle AD FS-Apps im Hinblick auf die Kompatibilität mit Azure AD bewertet und auf Probleme überprüft sowie Anleitungen zum Vorbereiten einzelner Apps für die Migration bereitgestellt.

Weitere Informationen finden Sie unter [Verwenden das AD FS-Berichts für App-Aktivitäten zur Migration von Apps in Azure AD](../manage-apps/migrate-adfs-application-activity.md).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Neuer Workflow für Benutzer zum Anfordern der Administratoreinwilligung (Public Preview)

**Typ:** Neues Feature  
**Dienstkategorie:** Unternehmens-Apps  
**Produktfunktion:** Zugriffssteuerung

Der neue Workflow für die Administratoreinwilligung bietet Administratoren eine Möglichkeit, den Zugriff auf Apps zu gewähren, für die eine Administratoreinwilligung erforderlich ist. Wenn ein Benutzer versucht, auf eine App zuzugreifen, aber selbst keine Einwilligung erteilen kann, kann dieser jetzt eine Anforderung zur Administratoreinwilligung senden. Die Anforderung wird per E-Mail gesendet und in eine Warteschlange eingereiht, auf die alle Administratoren über das Azure-Portal zugreifen können, die als „Prüfer“ festgelegt sind. Nachdem eine ausstehende Anforderung überprüft wurde, wird der anfordernde Benutzer über die Entscheidung informiert.

Weitere Informationen finden Sie unter [Konfigurieren des Workflows für die Administratoreinwilligung (Vorschauversion)](../manage-apps/configure-admin-consent-workflow.md).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Neue Option „Azure AD App Registrations Token configuration“ (Konfiguration von Azure AD-App-Registrierungstoken) zur Verwaltung optionaler Ansprüche (Public Preview)

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Entwickleroberfläche

Das neue Blatt **Azure AD App Registrations Token configuration** (Konfiguration von Azure AD-App-Registrierungstoken) im Azure-Portal zeigt App-Entwicklern eine dynamische Liste optionaler Ansprüche für ihre Apps an. Mit den neuen Optionen können Sie die Migration von Azure AD-Apps optimieren und Fehlkonfigurationen bei optionalen Ansprüchen auf ein Minimum reduzieren.

Weitere Informationen finden Sie unter [Bereitstellen optionaler Ansprüche für Ihre Azure AD-App](../develop/active-directory-optional-claims.md).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Neuer zweistufiger Genehmigungsworkflow in der Azure AD-Berechtigungsverwaltung (Public Preview)

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Berechtigungsverwaltung

Wir haben einen neuen zweistufigen Genehmigungsworkflow eingeführt, der es Ihnen ermöglicht, dass zwei genehmigende Personen die Anforderung eines Benutzers für den Zugriffs auf ein Paket genehmigen müssen. So können Sie z. B. festlegen, dass der Vorgesetzte des anfordernden Benutzers zunächst die Genehmigung erteilen muss. Anschließend muss der Ressourcenbesitzer die Genehmigung erteilen. Wenn eine der genehmigenden Personen keine Genehmigung verteilt, wird der Zugriff verweigert.

Weitere Informationen finden Sie unter [Ändern der Anforderungs- und Genehmigungseinstellungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung](../governance/entitlement-management-access-package-request-policy.md).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Updates für die Seite „Meine Apps“ sowie neue Arbeitsbereiche (Public Preview)

**Typ:** Neues Feature  
**Dienstkategorie:** Meine Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten

Sie können jetzt anpassen, wie die Benutzer Ihrer Organisation die aktualisierte Seite „Meine Apps“ anzeigen und darauf zugreifen. Die neue Seite umfasst auch das neue Feature „Arbeitsbereiche“, mit dem Benutzer Apps einfacher suchen und organisieren können.

Weitere Informationen zur neuen Seite „Meine Apps“ und zum Erstellen von Arbeitsbereichen finden Sie unter [Erstellen von Arbeitsbereichen im Portal „Meine Apps“](../manage-apps/access-panel-collections.md).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Unterstützung von Google-Konten für Azure AD B2B Collaboration (allgemeine Verfügbarkeit)

**Typ:** Neues Feature  
**Dienstkategorie:** B2B  
**Produktfunktion:** Benutzerauthentifizierung

Google-Konten (Gmail-Konten) werden jetzt in Azure AD unterstützt, um die Zusammenarbeit für Benutzer und Partner zu erleichtern. Ihre Partner müssen kein neues Microsoft-Konto mehr erstellen und verwalten. Microsoft Teams unterstützt Google-Benutzer jetzt auf allen Clients sowie in den gemeinsamen und mandantenbezogenen Authentifizierungsendpunkten vollständig.

Weitere Informationen finden Sie unter [Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer](../external-identities/google-federation.md).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge-Unterstützung für Mobilgeräte für bedingten Zugriff und einmaliges Anmelden (allgemeine Verfügbarkeit)

**Typ:** Neues Feature  
**Dienstkategorie**: Bedingter Zugriff  
**Produktfunktion:** Identitätssicherheit und -schutz

Azure AD für Microsoft Edge in iOS und Android unterstützt jetzt einmaliges Anmelden und bedingten Zugriff in Azure AD:

- **Einmaliges Anmelden für Microsoft Edge:** Das einmalige Anmelden ist jetzt für native Clients wie Microsoft Outlook und Microsoft Edge für alle mit Azure AD verknüpften Apps verfügbar.

- **Bedingter Zugriff für Microsoft Edge:** Durch anwendungsbasierte Richtlinien für den bedingten Zugriff müssen die Benutzer durch Microsoft Intune geschützte Browser wie Microsoft Edge verwenden.

Weitere Informationen zum bedingten Zugriff und zum einmaligen Anmelden mit Microsoft Edge finden Sie im Blogbeitrag [Jetzt allgemein verfügbar: Unterstützung für bedingten Zugriff und einmaliges Anmelden für Mobilgeräte mit Microsoft Edge](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179). Weitere Informationen zur Einrichtung Ihrer Client-Apps mit [App-basiertem bedingten Zugriff](../conditional-access/app-based-conditional-access.md) oder [gerätebasiertem bedingten Zugriff](../conditional-access/require-managed-devices.md) finden Sie unter [Verwalten des Webzugriffs durch einen mittels Microsoft Intune-Richtlinien geschützte Browser](/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD-Berechtigungsverwaltung (allgemeine Verfügbarkeit)

**Typ:** Neues Feature  
**Dienstkategorie:** Sonstige  
**Produktfunktion:** Berechtigungsverwaltung

Die Azure AD-Berechtigungsverwaltung ist ein neues Identity-Governance-Feature, mit dem Organisationen Identitäts- und Zugriffslebenszyklen bedarfsgesteuert verwalten können. Mit diesem neuen Feature können Workflows für Zugriffsanforderungen, Zugriffszuweisungen, Überprüfungen und das Ablaufen von Gruppen, Apps und SharePoint Online-Websites automatisiert werden.

Mit der Azure AD-Berechtigungsverwaltung können Sie den Zugriff für interne Mitarbeiter und externe Benutzer Ihrer Organisation festlegen, die Zugriff auf diese Ressourcen benötigen.

Weitere Informationen finden Sie unter [Was ist die Azure AD-Berechtigungsverwaltung?](../governance/entitlement-management-overview.md#license-requirements).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisieren der Bereitstellung von Benutzerkonten für diese neu unterstützten SaaS-Apps

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten  

Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

[SAP Cloud Platform Identity Authentication Service](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md), [RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md), [SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md), [Miro](../saas-apps/miro-provisioning-tutorial.md), [Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [Infor CloudSuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md), [OfficeSpace Software](../saas-apps/officespace-software-provisioning-tutorial.md), [Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar (November 2019)

**Typ:** Neues Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** Integration von Drittanbieterprodukten

Im November 2019 haben wir diese 21 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Airtable](../saas-apps/airtable-tutorial.md), [Hootsuite](../saas-apps/hootsuite-tutorial.md), [Blue Access for Members (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [Bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](../saas-apps/motus-tutorial.md), [MyAryaka](../saas-apps/myaryaka-tutorial.md), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [Foko Retail](../saas-apps/foko-retail-tutorial.md), [Qmarkets Idea & Innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md), [uniFLOW Online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [Jisc Student Voter Registration](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Neuer und verbesserter Azure AD-App-Katalog

**Typ:** Geändertes Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** SSO

Wir haben den Azure AD-App-Katalog überarbeitet, damit Sie einfacher nach vorintegrierten Apps suchen können, die die Bereitstellung, OpenID Connect und SAML in Ihrem Azure Active Directory-Mandanten unterstützen.

Weitere Informationen finden Sie unter [Hinzufügen einer Anwendung zum Azure Active Directory-Mandanten](../manage-apps/add-application-portal.md).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Erhöhtes Zeichenlimit für App-Rollendefinitionen (240 statt 120 Zeichen)

**Typ:** Geändertes Feature  
**Dienstkategorie**: Unternehmens-Apps  
**Produktfunktion:** SSO

Kunden haben uns gemeldet, dass das Zeichenlimit von 120 Zeichen für die App-Rollendefinition bei einigen Apps und Diensten zu kurz ist. Deshalb haben wir die maximale Länge für Rollendefinitionen auf 240 Zeichen erhöht.

Weitere Informationen zu App-spezifischen Rollendefinitionen finden Sie unter [Hinzufügen von App-Rollen in Ihrer App und Empfangen der Rollen im Token](../develop/howto-add-app-roles-in-azure-ad-apps.md).

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Die identityRiskEvent-API für Azure AD Identity Protection-Risikoerkennungen wurde als veraltet markiert.

**Typ:** Plan für Änderung der **Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Als Reaktion auf Feedback von Entwicklern können Azure AD Premium P2-Abonnenten jetzt komplexe Abfragen für Risikoerkennungsdaten von Azure AD Identity Protection durchführen, indem sie die neue riskDetection-API für Microsoft Graph verwenden. Die vorhandene Betaversion der [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta)-API gibt etwa ab dem **10. Januar 2020** keine Daten mehr zurück. Wenn Ihre Organisation die identityRiskEvent-API verwendet, sollten Sie zur neuen riskDetection-API wechseln.

Weitere Informationen zur neuen riskDetection-API finden Sie in der [Referenzdokumentation zur riskDetection-API](/graph/api/resources/riskdetection).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Unterstützung des Anwendungsproxys für das SameSite-Attribut und Chrome 80

**Typ:** Plan für Änderung der **Dienstkategorie:** App-Proxy-**Produktfunktion:** Zugriffssteuerung

Einige Wochen vor dem Release des Chrome 80-Browsers planen wir eine Aktualisierung der Art und Weise, in der Cookies des Anwendungsproxys das **SameSite**-Attribut behandeln. Ab dem Release von Chrome 80 wird jedes Cookie, das das **SameSite**-Attribut nicht angibt, so behandelt, als wäre es auf `SameSite=Lax` festgelegt.

Um negative Auswirkungen dieser Änderung zu vermeiden, aktualisieren wir den Zugriff und die Sitzungscookies des Anwendungsproxys wie folgt:

- Der Standardwert für die Einstellung **Sicheres Cookie verwenden** wird auf **Ja** festgelegt.

- Der Standardwert für das **SameSite**-Attribut wird auf **Keins** festgelegt.

    >[!NOTE]
    > Zugriffscookies des Anwendungsproxys wurden immer exklusiv über sichere Kanäle übertragen. Diese Änderungen gelten nur für Sitzungscookies.

Weitere Informationen zu den Cookieeinstellungen für den Anwendungsproxy finden Sie unter [Cookieeinstellungen für den Zugriff auf lokale Anwendungen in Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>App-Registrierungen (Legacy) und App-Verwaltung im Anwendungsregistrierungsportal (apps.dev.microsoft.com) sind nicht mehr verfügbar.

**Typ:** Plan für Änderung der **Dienstkategorie:** N/V-**Produktfunktion:** Entwickleroberfläche

Benutzer mit Azure AD-Konten sind nicht mehr in der Lage, Anwendungen über das Anwendungsregistrierungsportal (apps.dev.microsoft.com) zu registrieren oder zu verwalten oder Anwendungen in der Benutzeroberfläche für App-Registrierungen (Legacy) im Azure-Portal zu registrieren und zu verwalten.

Weitere Informationen zur neuen Benutzeroberfläche für App-Registrierungen finden Sie unter [Trainingshandbuch: App-Registrierungen im Azure-Portal](../develop/quickstart-register-app.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Benutzer müssen sich während der Migration von der benutzerbasierten MFA zur auf bedingtem Zugriff basierenden MFA nicht erneut registrieren

**Typ:** Feste **Dienstkategorie:** MFA-**Produktfunktion:** Identitätssicherheit und -schutz

Wir haben ein bekanntes Problem behoben, das dazu führte, dass Benutzer sich erneut registrieren mussten, wenn die benutzerbasierte Multi-Factor Authentication (MFA) für sie deaktiviert und dann die MFA über eine Richtlinie für bedingten Zugriff aktiviert wurde.

Um zu erzwingen, dass Benutzer sich erneut registrieren müssen, können Sie die Option **Erneute MFA-Registrierung erforderlich** aus den Authentifizierungsmethoden für den Benutzer im Azure AD-Portal auswählen. Weitere Informationen zum Migrieren von Benutzern von der benutzerbasierten MFA zur auf bedingtem Zugriff basierenden MFA finden Sie unter [Konvertieren von Benutzern von „MFA pro Benutzer“ zu „MFA mit bedingtem Zugriff“](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Neue Funktionen zum Transformieren und Senden von Ansprüchen in Ihrem SAML-Token

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Wir haben weitere Funktionen hinzugefügt, um Sie beim Anpassen und Senden von Ansprüchen in Ihrem SAML-Token zu unterstützen. Diese neuen Funktionen umfassen Folgendes:

- Zusätzliche Funktionen zum Transformieren von Ansprüchen, mit denen Sie den im Anspruch gesendeten Wert ändern können.

- Die Möglichkeit, mehrere Transformationen auf einen einzelnen Anspruch anzuwenden.

- Die Möglichkeit, die Anspruchsquelle anzugeben, basierend auf dem Benutzertyp und der Gruppe, zu der der Benutzer gehört.

Detaillierte Informationen zu diesen neuen Funktionen und zu ihrer Verwendung finden Sie unter [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Neue Seite „Meine Anmeldungen“ für Endbenutzer in Azure AD

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Überwachung und Berichterstellung

Wir haben eine neue Seite **Meine Anmeldungen** (https://mysignins.microsoft.com) hinzugefügt, damit die Benutzer Ihrer Organisation ihre letzten Anmeldungen anzeigen können, um nach ungewöhnlichen Aktivitäten zu suchen. Auf dieser neuen Seite sehen die Benutzer Folgendes:

- Informationen dazu, ob jemand versucht, ihr Kennwort zu erraten.

- Informationen dazu, ob ein Angreifer sich erfolgreich bei ihrem Konto angemeldet hat und an welchem Standort die Anmeldung erfolgt ist.

- Auf welche Apps der Angreifer versucht hat, zuzugreifen.

Weitere Informationen finden Sie im Blog [Users can now check their sign-in history for unusual activity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) (Benutzer können jetzt ihren Anmeldeverlauf auf ungewöhnliche Aktivitäten überprüfen).

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migration der Azure AD Domain Services (Azure AD DS) von klassischen virtuellen Netzwerken zu virtuellen Azure Resource Manager-Netzwerken

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD Domain Services-**Produktfunktion:** Azure AD Domain Services

Wir haben gute Nachrichten für diejenigen unserer Kunden, die bisher noch klassische virtuelle Netzwerke nutzen! Sie können jetzt eine einmalige Migration von einem klassischen virtuellen Netzwerk zu einem vorhandenen virtuellen Resource Manager-Netzwerk durchführen. Nach dem Wechsel zum virtuellen Resource Manager-Netzwerk können Sie von den zusätzlichen und besseren Features wie differenzierten Kennwortrichtlinien, E-Mail-Benachrichtigungen und Überwachungsprotokollen profitieren.

Weitere Informationen finden Sie unter [Vorschauversion: Migrieren von Azure AD Domain Services vom klassischen VNET-Modell zu Resource Manager](../../active-directory-domain-services/migrate-from-classic-vnet.md).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Updates für das Azure AD B2C-Seitenvertragslayout

**Typ:** Neues Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Wir haben einige Änderungen an Version 1.2.0 des Seitenvertrags für Azure AD B2C vorgenommen. In dieser aktualisierten Version können Sie jetzt die Ladereihenfolge Ihrer Elemente steuern, wodurch sich auch das Flackern eliminieren lässt, das beim Laden des Stylesheets (CSS) auftritt.

Eine vollständige Liste aller Änderungen am Seitenvertrag finden Sie im [Versionsänderungsprotokoll](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Update für die Seite „Meine Apps“ sowie neue Arbeitsbereiche (Public Preview)

**Typ:** Neues Feature-**Dienstkategorie:** Meine Apps-**Produktfunktion:** Zugriffssteuerung

Sie können jetzt anpassen, wie die Benutzer Ihrer Organisation die brandneue Seite „Meine Apps“ anzeigen und darauf zugreifen. Gleichzeitig erleichtert das neue Feature für Arbeitsbereiche die Suche nach Apps. Die Funktion für Arbeitsbereiche fungiert als Filter für die Apps, auf die Benutzer Ihrer Organisation bereits zugreifen können.

Weitere Informationen zum Rollout der neuen Benutzeroberfläche „Meine Apps“ und zum Erstellen von Arbeitsbereichen finden Sie unter [Erstellen von Arbeitsbereichen im Portal „Meine Apps“ (Vorschau)](../manage-apps/access-panel-collections.md).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Unterstützung für das Abrechnungsmodell mit monatlich aktiven Benutzern (allgemeine Verfügbarkeit)

**Typ:** Neues Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Azure AD B2C unterstützt jetzt die Abrechnung basierend auf monatlich aktiven Benutzern (Monthly Active Users, MAU). Die MAU-Abrechnung basiert auf der Anzahl eindeutiger Benutzer mit Authentifizierungsaktivitäten während eines Kalendermonats. Bestandskunden können jederzeit zu dieser neuen Abrechnungsmethode wechseln.

Ab 1. November 2019 erfolgt die Abrechnung für alle neuen Kunden automatisch nach dieser Methode. Diese Abrechnungsmethode bietet Kunden Kostenvorteile und die Möglichkeit zur Vorausplanung.

Weitere Informationen finden Sie unter [Upgrade auf das Abrechnungsmodell mit monatlich aktiven Benutzern](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar (Oktober 2019)

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Oktober 2019 haben wir diese 35 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[In Case of Crisis – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno Journey](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm), [Salestim](https://www.salestim.com/), [Learnster](../saas-apps/learnster-tutorial.md), [Dynatrace](../saas-apps/dynatrace-tutorial.md), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](../saas-apps/freshworks-tutorial.md), [eCornell](../saas-apps/ecornell-tutorial.md), [ShipHazmat](../saas-apps/shiphazmat-tutorial.md), [Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [Contentful](../saas-apps/contentful-tutorial.md), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europe](https://www.hirevue.com/), [HireVue Coordinate – USOnly](https://www.hirevue.com/), [HireVue Coordinate – US](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org](../saas-apps/visitorg-tutorial.md), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](../saas-apps/paylocity-tutorial.md), [Mail Luck!](../saas-apps/mail-luck-tutorial.md), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](../saas-apps/eab-navigate-impl-tutorial.md), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Konsolidiertes Menüelement „Sicherheit“ im Azure AD-Portal

**Typ:** Geändertes Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Sie können jetzt im Azure-Portal über das neue Menüelement **Sicherheit** sowie über die **Suchleiste** auf alle verfügbaren Azure AD-Sicherheitsfeatures zugreifen. Darüber hinaus bietet die neue Landing Page **Sicherheit** – **Sicherheit – Erste Schritte** – Links zu unserer öffentlichen Dokumentation sowie zu Leitfäden zu den Themen Sicherheit und Bereitstellung.

Das neue Menü **Sicherheit** bietet Folgendes:

- Bedingter Zugriff
- Schutz der Identität (Identity Protection)
- Security Center
- Identity Secure Score
- Authentifizierungsmethoden
- MFA
- Risikoberichte: gefährdete Benutzer, riskante Anmeldungen, Risikoerkennungen
- Und vieles mehr...

Weitere Informationen finden Sie unter [Sicherheit – Erste Schritte](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Ablaufrichtlinie für Office 365-Gruppen um automatische Verlängerung erweitert

**Typ:** Geändertes Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Identity Lifecycle Management

Die Ablaufrichtlinie für Office 365-Gruppen wurde erweitert, sodass Gruppen, die von ihren Mitgliedern aktiv genutzt werden, automatisch verlängert werden. Die automatische Verlängerung erfolgt basierend auf der Benutzeraktivität in allen Office 365-Apps wie z. B. Outlook, SharePoint und Teams.

Mit dieser Erweiterung werden Benachrichtigungen zum Ablauf von Gruppen reduziert, und Sie können sicherstellen, dass aktive Gruppen verfügbar bleiben. Wenn Sie bereits eine aktive Ablaufrichtlinie für Ihre Office 365-Gruppen eingerichtet haben, müssen Sie nichts unternehmen, um diese neue Funktion zu aktivieren.

Weitere Informationen finden Sie unter [Konfigurieren der Ablaufrichtlinie für Office 365-Gruppen](../enterprise-users/groups-lifecycle.md).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Aktualisierte Erstellungsfunktion für Azure AD Domain Services (Azure AD DS)

**Typ:** Geändertes Feature-**Dienstkategorie:** Azure AD Domain Services-**Produktfunktion:** Azure AD Domain Services

Wir haben Azure AD Domain Services (Azure AD DS) mit einer neuen und verbesserten Erstellungsfunktion aktualisiert, dank der Sie mit nur drei Klicks eine verwaltete Domäne erstellen können. Darüber hinaus können Sie Azure AD DS jetzt aus einer Vorlage hochladen und bereitstellen.

Weitere Informationen finden Sie im [Tutorial: Erstellen und Konfigurieren einer Azure Active Directory Domain Services-Instanz](../../active-directory-domain-services/tutorial-create-instance.md).

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Geplante Änderung: Einstellung der Power BI-Inhaltspakete

**Typ:** Plan für Änderung der **Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Ab dem 1. Oktober 2019 werden in Power BI alle Inhaltspakete, einschließlich des Azure AD-Power BI-Inhaltspakets, als veraltet markiert. Als Alternative zu diesem Inhaltspaket können Sie Azure AD-Arbeitsmappen verwenden, um Erkenntnisse aus Ihren Azure AD-bezogenen Diensten zu gewinnen. Es werden noch weitere Arbeitsmappen bereitgestellt, einschließlich Arbeitsmappen zu Richtlinien für den bedingten Zugriff im reinen Berichtsmodus, auf der App-Zustimmung basierender Erkenntnisse u. v. m.

Weitere Informationen zu Arbeitsmappen finden Sie unter [Verwenden von Azure Monitor-Arbeitsmappen für Azure Active Directory-Berichte](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Weitere Informationen zur Einstellung der Inhaltspakete finden Sie im Blogbeitrag mit der [Ankündigung der allgemeinen Verfügbarkeit von Power BI-Vorlagen-Apps](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/).

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>„Mein Profil“ wird umbenannt und in die Kontoseite von Microsoft Office integriert

**Typ:** Plan für Änderung der **Dienstkategorie:** Mein Profil/Konto-**Produktfunktion:** Zusammenarbeit

Ab Oktober wird aus der Umgebung „Mein Profil“ die Umgebung „Mein Konto“. Im Rahmen dieser Änderung werden alle Vorkommen von **Mein Profil** in **Mein Konto** geändert. Zusätzlich zu der Namensänderung und einigen Entwurfsverbesserungen ist die aktualisierte Umgebung zusätzlich in die Kontoseite von Microsoft Office integriert. Insbesondere können Sie von der Seite **Kontoübersicht** auf Office-Installationen und-Abonnements sowie von der Seite **Datenschutz** auf Office-bezogene Kontakteinstellungen zugreifen.

Weitere Informationen zur Umgebung „Mein Profil“ (Vorschau) finden Sie unter [Übersicht über das Portal „Mein Profil“ (Vorschauversion)](../user-help/my-account-portal-overview.md).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Massenverwaltung von Gruppen und Mitgliedern mithilfe von CSV-Dateien im Azure AD-Portal (Öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Wir freuen uns, die Verfügbarkeit der Massenverwaltung von Gruppen im Azure AD-Portal in der öffentlichen Vorschau ankündigen zu können. Sie können jetzt mithilfe einer CSV-Datei und dem Azure AD-Portal Gruppen- und Mitgliederlisten verwalten. Dazu zählen folgende Aktionen:

- Hinzufügen oder Entfernen von Mitgliedern zu/aus einer Gruppe

- Herunterladen der Gruppenliste aus dem Verzeichnis

- Herunterladen der Gruppenmitgliederliste für eine bestimmte Gruppe

Weitere Informationen finden Sie unter [Massenhinzufügen von Mitgliedern](../enterprise-users/groups-bulk-import-members.md), [Massenentfernen von Mitgliedern ](../enterprise-users/groups-bulk-remove-members.md), [Massendownload von Mitgliederlisten](../enterprise-users/groups-bulk-download-members.md) und [Massendownload von Gruppenlisten](../enterprise-users/groups-bulk-download.md).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Die dynamische Zustimmung wird jetzt durch einen neuen Endpunkt für die Administratorzustimmung unterstützt

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Wir haben zur Unterstützung der dynamischen Zustimmung einen neuen Endpunkt für die Administratorzustimmung erstellt. Dies ist hilfreich für Apps, für die das Modell der dynamischen Zustimmung auf der Microsoft Identity Platform verwendet werden soll.

Weitere Informationen zum Verwenden des neuen Endpunkts finden Sie unter [Verwenden des Endpunkts für die Administratorzustimmung](../develop/v2-admin-consent.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar (September 2019)

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im September 2019 haben wir diese 29 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for Ethidex Compliance Office&trade; – Single  sign-on](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [iServer Portal](../saas-apps/iserver-portal-tutorial.md), [SKYSITE](../saas-apps/skysite-tutorial.md), [Concur Travel and Expense](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/`, [ARC Facilities](../saas-apps/arc-facilities-tutorial.md), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), [Wide Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRAKU](../saas-apps/renraku-tutorial.md), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), `https://app.penneo.com/`, `https://app.testhtm.com/settings/email-integration`, [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](../saas-apps/whitesource-tutorial.md), [Hosted Heritage Online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC](../saas-apps/idc-tutorial.md), [CakeHR](../saas-apps/cakehr-tutorial.md), [BIS](../saas-apps/bis-tutorial.md), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](../saas-apps/sonarqube-tutorial.md), [Adobe Identity Management](../saas-apps/tutorial-list.md), [Discovery Benefits SSO](../saas-apps/discovery-benefits-sso-tutorial.md), [Amelio](https://app.amelio.co/), `https://itask.yipinapp.com/`

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-global-reader-role"></a>Neue Azure AD-Rolle „Globaler Leser“

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD-Rollen **Produktfunktion:** Zugriffssteuerung

Ab dem 24. September 2019 führen wir eine neue Azure Active Directory (AD)-Rolle namens „Globaler Leser“ ein. Diese Einführung beginnt bei Produktions- und globalen Cloud-Kunden (Global Cloud Customers, GCC) und erreicht im Oktober den weltweiten Verbreitungsgrad.

Die Rolle „Globaler Leser“ ist das Gegenstück zu „Globaler Administrator“, hat jedoch eine Nur-Lese-Berechtigung. Benutzer in dieser Rolle können in Microsoft 365-Diensten Einstellungen und administrative Informationen lesen, aber keine Verwaltungsaktionen ausführen. Wir haben die Rolle „Globaler Leser“ erstellt, um die Anzahl der globalen Administratoren in Ihrer Organisation zu reduzieren. Da Konten für globale Administratoren leistungsstark und anfällig für Angriffe sind, empfehlen wir, weniger als fünf globale Administratoren einzurichten. Die Rolle „Globaler Leser“ wird für Planungen, Audits oder Untersuchungen empfohlen. Wir empfehlen auch, die Rolle „Globaler Leser“ mit anderen eingeschränkten Administratorrollen (z.B. Exchange-Administrator) zu kombinieren, um bei der Arbeit leichter auf die Rolle „Globaler Administrator“ verzichten zu können.

Die Rolle „Globaler Leser“ funktioniert mit dem neuen Microsoft 365 Admin Center, Exchange Admin Center, Teams Admin Center, Security Center, Compliance Center, Azure AD Admin Center und Device Management Admin Center.

>[!NOTE]
> Beim Start der öffentlichen Vorschauversion funktioniert die Rolle „Globaler Leser“ mit folgenden Diensten nicht: SharePoint, Privileged Access Management, Kunden-Lockbox, Vertraulichkeitsbezeichnungen, Teams Lifecycle, Teams Reporting & Call Analytics, Teams IP Phone Device Management und Teams-App-Katalog.

Weitere Informationen finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../roles/permissions-reference.md).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Zugriff auf einen lokalen Berichtsserver von Ihrer Power BI Mobile-App mit dem Azure Active Directory-Anwendungsproxy

**Typ:** Neues Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Zugriffssteuerung

Die neue Integration zwischen Power BI Mobile-App und Azure AD-Anwendungsproxy ermöglicht die sichere Anmeldung bei der Power BI Mobile-App und die Anzeige aller Berichte Ihrer Organisation, die auf dem lokalen Power BI-Berichtsserver gehostet werden.

Informationen zur Power BI Mobile-App finden Sie auf der [Power BI-Website](https://powerbi.microsoft.com/mobile/) Dort erfahren Sie auch, wo Sie die App herunterladen können. Weitere Informationen zum Einrichten der Power BI Mobile-App mit dem Azure AD-Anwendungsproxy finden Sie unter [Aktivieren des Remotezugriffs auf Power BI Mobile mit dem Azure AD-Anwendungsproxy](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Neue Version des AzureADPreview-Moduls in PowerShell verfügbar

**Typ:** Geändertes Feature-**Dienstkategorie:** Andere **Produktfunktion:** Verzeichnis

Dem AzureADPreview-Modul wurden neue Cmdlets hinzugefügt, mit denen Sie benutzerdefinierte Rollen in Azure AD definieren und zuweisen können, darunter:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Neue Version von Azure AD Connect

**Typ:** Geändertes Feature-**Dienstkategorie:** Andere **Produktfunktion:** Verzeichnis

Für Kunden mit automatischen Upgrade haben wir eine aktualisierte Version von Azure AD Connect veröffentlicht. Diese neue Version enthält verschiedene neue Features, Verbesserungen und Fehlerbehebungen. Weitere Informationen zu dieser neuen Version finden Sie unter [Azure AD Connect: Verlauf der Versionsveröffentlichungen](../hybrid/reference-connect-version-history.md#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA) Server, Version 8.0.2, ist jetzt verfügbar

**Typ:** Feste **Dienstkategorie:** MFA-**Produktfunktion:** Identitätssicherheit und -schutz

Bestehende Kunden, die MFA Server vor dem 1. Juli 2019 aktiviert haben, können jetzt die aktuelle Version von MFA Server (Version 8.0.2) herunterladen. In dieser neuen Version wurden folgende Verbesserungen vorgenommen:

- Ein Problem wurde behoben, sodass der Benutzer eine E-Mail erhält, wenn bei der Azure AD-Synchronisierung ein Benutzer von „Deaktiviert“ in „Aktiviert“ geändert wird.

- Ein Problem wurde behoben, sodass Kunden erfolgreich ein Upgrade ausführen und gleichzeitig die Tags-Funktionalität weiter nutzen können.

- Der Ländercode für den Kosovo (+383) wurde hinzugefügt.

- Eine einmalige Umgehung des Audit-Trails (Audit-Protokollierung) wurde zu MultiFactorAuthSvc.log hinzugefügt.

- Die Leistung des Web Service SDKs wurde verbessert.

- Kleinere Fehler wurden behoben.

Seit dem 1. Juli 2019 bietet Microsoft MFA Server nicht mehr für neue Bereitstellungen an. Neue Kunden, die eine mehrstufige Authentifizierung benötigen, sollten cloudbasierte Azure AD Multi-Factor Authentication verwenden. Weitere Informationen finden Sie unter [Planen einer cloudbasierten Azure AD Multi-Factor Authentication-Bereitstellung](../authentication/howto-mfa-getstarted.md).

---

## <a name="august-2019"></a>August 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Erweiterte Suche, Filterung und Sortierung für Gruppen sind im Azure AD-Portal (Public Preview) verfügbar.

**Typ:** Neues Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Wir freuen uns, die Verfügbarkeit der erweiterten gruppenbezogenen Erfahrungen im Azure AD-Portal in der Public Preview ankündigen zu können. Diese Verbesserungen helfen Ihnen mit den folgenden Bereitstellungen, Gruppen und Mitgliederlisten besser zu verwalten:

- Erweiterte Suchfunktionen wie z. B. Suchen von Teilzeichenfolgen in Gruppenlisten.
- Erweiterte Filter- und Sortieroptionen für Mitglieds- und Besitzerlisten.
- Neue Suchfunktionen für Mitglieds- und Besitzerlisten.
- Genauere Gruppenanzahl für große Gruppen.

Weitere Informationen finden Sie unter [Verwalten von Gruppen im Azure-Portal](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Neue benutzerdefinierte Rollen für die App-Registrierungsverwaltung (Public Preview) verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD-Rollen **Produktfunktion:** Zugriffssteuerung

Benutzerdefinierte Rollen (die mit einem P1- oder P2-Abonnement von Azure AD verfügbar sind) bieten nun einen differenzierteren Zugriff, indem Sie Rollendefinitionen mit bestimmten Berechtigungen erstellen und diese Rollen dann bestimmten Ressourcen zuweisen können. Derzeit erstellen Sie benutzerdefinierte Rollen mithilfe von Berechtigungen zum Verwalten von App-Registrierungen und weisen diese Rolle dann einer bestimmten App zu. Weitere Informationen zu benutzerdefinierten Rollen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory (Vorschauversion)](../roles/custom-overview.md).

Wenn Sie Unterstützung für zusätzliche Berechtigungen oder Ressourcen benötigen, die derzeit nicht verfügbar sind, können Sie Ihr Feedback auf unserer [Azure-Feedbackwebsite](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) übermitteln. Wir nutzen Ihre Anforderung, um unsere Updateroadmap zu ergänzen.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Neue Bereitstellungsprotokolle helfen Ihnen bei der Überwachung und der Problembehandlung für die App-Bereitstellung (Public Preview).

**Typ:** Neues Feature-**Dienstkategorie:** App-Bereitstellungs-**Produktfunktion:** Identity Lifecycle Management

Mit neuen Bereitstellungsprotokollen können Sie die Bereitstellung von Benutzern und Gruppen überwachen und eventuelle Probleme beheben. Diese neuen Protokolldateien enthalten Informationen zu Folgendem:

- Welche Gruppen wurden erfolgreich in [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) erstellt?
- Welche Rollen wurden aus [Amazon Web Services (AWS)](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-amazon-web-services-aws) importiert?
- Welche Mitarbeiter wurden nicht aus [Workday](../saas-apps/workday-inbound-tutorial.md) importiert?

Weitere Informationen finden Sie unter [Bereitstellungsberichte im Azure Active Directory-Portal (Vorschau)](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Neue Sicherheitsberichte für alle Azure AD-Administratoren (allgemeine Verfügbarkeit)

**Typ:** Neues Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Standardmäßig können bald alle Azure AD Administratoren in Azure AD auf moderne Sicherheitsberichte zugreifen. Bis Ende September können Sie das Banner oben in den modernen Sicherheitsberichten verwenden, um zurück zu den alten Berichten zu wechseln.

Die modernen Sicherheitsberichte bieten zusätzliche Funktionen gegenüber den älteren Versionen, einschließlich der folgenden:

- Erweiterte Filterung und Sortierung
- Massenaktionen, z.B. das Verwerfen des Benutzerrisikos
- Bestätigung kompromittierter oder sicherer Entitäten
- Risikostatus, darunter: „Gefährdet“, „Verworfen“, „Bereinigt“ und „Gefährdung bestätigt“
- Neue risikobezogene Erkennungen (verfügbar für Azure AD Premium-Abonnenten)

Weitere Informationen finden Sie unter [Riskante Benutzer](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), [Riskante Anmeldungen](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins) und [Risikoerkennungen](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Benutzerseitig zugewiesene verwaltete Identität für Virtual Machines und Virtual Machine Scale Sets verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Verwaltete Identitäten für Azure-Ressourcen-**Produktfunktion:** Entwickleroberfläche

Benutzerseitig zugewiesene verwaltete Identitäten sind nun für Virtual Machines und Virtual Machine Scale Sets verfügbar. Dabei kann Azure eine Identität in dem Azure AD-Mandanten erstellen, der vom verwendeten Abonnement als vertrauenswürdig eingestuft wird, und einer oder mehreren Azure-Dienstinstanzen zuweisen. Weitere Informationen zu benutzerseitig zugewiesenen verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../managed-identities-azure-resources/overview.md).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Benutzer können Ihre Kennwörter mithilfe einer mobilen App oder eines Hardwaretokens zurücksetzen (allgemeine Verfügbarkeit)

**Typ:** Geändertes Feature-**Dienstkategorie:** Self-Service-Kennwortzurücksetzungs-**Produktfunktion:** Benutzerauthentifizierung

Benutzer, die eine mobile App in Ihrer Organisation registriert haben, können jetzt ihr eigenes Kennwort zurücksetzen, indem sie eine Benachrichtigung von der Microsoft Authenticator-App bestätigen oder einen Code aus ihrer mobilen App oder ein Hardwaretoken eingeben.

Weitere Informationen finden Sie unter [Vorgehensweise: Self-Service-Kennwortzurücksetzung in Azure AD](../authentication/concept-sspr-howitworks.md). Weitere Informationen zur Benutzeroberfläche finden Sie unter [Übersicht über die Kennwortzurücksetzung für Ihr Geschäfts-, Schul- oder Unikonto](../user-help/active-directory-passwords-reset-register.md).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoriert den freigegebenen MSAL.NET-Cache für „Im Auftrag von“-Szenarien

**Typ:** Feste **Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Ab Azure AD Authentication Library (ADAL.NET) Version 5.0.0-preview müssen App-Entwickler [einen Cache pro Konto für Web-Apps und Web-APIs serialisieren](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Andernfalls kann es in einigen Szenarien mit Verwendung des [„Im Auftrag von“-Ablaufs](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) für Java und für einige spezifische Anwendungsfälle von `UserAssertion` zu Rechteerweiterungen kommen. Zur Vermeidung dieses Sicherheitsrisikos wird der freigegebene Cache in Verbindung mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) für „Im Auftrag von“-Szenarien von ADAL.NET jetzt ignoriert.

Weitere Informationen zu diesem Problem finden Sie unter [Active Directory-Authentifizierungsbibliothek: Sicherheitsrisiko in Verbindung mit der Rechteerweiterung](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – August 2019

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im August 2019 haben wir diese 26 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Civic Platform](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox](../saas-apps/cognidox-tutorial.md), [Viareport's Inativ Portal (Europa)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy Attendance](../saas-apps/academy-attendance-tutorial.md), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync by Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargebee](../saas-apps/chargebee-tutorial.md), [deliver.media&trade; Portal](https://portal.deliver.media), [Frontline Education](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](../saas-apps/blink-tutorial.md), [Vocoli](../saas-apps/vocoli-tutorial.md), [ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md), [Sigstr](../saas-apps/sigstr-tutorial.md), [Darwinbox](../saas-apps/darwinbox-tutorial.md), [Watch by Colors](../saas-apps/watch-by-colors-tutorial.md), [Harness](../saas-apps/harness-tutorial.md), [EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Neue Versionen der PowerShell-Module AzureAD und AzureADPreview verfügbar

**Typ:** Geändertes Feature-**Dienstkategorie:** Andere **Produktfunktion:** Verzeichnis

Es sind neue Updates der PowerShell-Module AzureAD und AzureADPreview verfügbar:

- Dem `Get-AzureADDirectoryRole`-Parameter im AzureAD-Modul wurde der neue Parameter `-Filter` hinzugefügt. Mit diesem Parameter können Sie nach den Verzeichnisrollen filtern, die vom Cmdlet zurückgegeben werden.
- Dem AzureADPreview-Modul wurden neue Cmdlets hinzugefügt, mit denen Sie benutzerdefinierte Rollen in Azure AD definieren und zuweisen können, darunter:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Verbesserungen an der Benutzeroberfläche des Generators für dynamische Gruppenregeln im Azure-Portal

**Typ:** Geändertes Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Wir haben einige Verbesserungen an der Benutzeroberfläche des Generators für dynamische Gruppenregeln vorgenommen, der im Azure-Portal verfügbar ist und Ihnen beim Einrichten einer neuen Regel oder dem Ändern vorhandener Regeln hilft. Diese Designverbesserung ermöglicht Ihnen, Regeln mit bis zu fünf Ausdrücken (anstelle von einem) zu erstellen. Wir haben außerdem die Geräteeigenschaftenliste aktualisiert und dabei veraltete Geräteeigenschaften entfernt.

Weitere Informationen finden Sie unter [Verwalten von Regeln für dynamische Mitgliedschaft](../enterprise-users/groups-dynamic-membership.md).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Neue Microsoft Graph-App-Berechtigung für die Verwendung mit Zugriffsüberprüfungen verfügbar

**Typ:** Geändertes Feature-**Dienstkategorie:** Zugriffsüberprüfungen-**Produktfunktion:** Identity Governance

Wir haben die neue Microsoft Graph-App-Berechtigung `AccessReview.ReadWrite.Membership` eingeführt, die es Apps ermöglicht, automatisch Zugriffsüberprüfungen für Gruppenmitgliedschaften und App-Zuweisungen zu erstellen und abzurufen. Diese Berechtigung kann von geplanten Aufträgen oder als Teil der Automatisierung verwendet werden, ohne dass ein angemeldeter Benutzerkontext erforderlich ist.

Weitere Informationen finden Sie im Blogbeitrag [Example how to create Azure AD access reviews using Microsoft Graph app permissions with PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241) (Beispiel zum Erstellen von Azure AD-Zugriffsüberprüfungen mithilfe von Microsoft Graph-App-Berechtigungen mit PowerShell).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD-Aktivitätsprotokolle jetzt für Instanzen der Government-Cloud in Azure Monitor verfügbar

**Typ:** Geändertes Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Wir freuen uns, bekannt geben zu können, dass Azure AD-Aktivitätsprotokolle jetzt in Azure Monitor für Instanzen der Government-Cloud verfügbar sind. Sie können jetzt Azure AD-Protokolle an Ihr Speicherkonto oder einen Event Hub senden, um sie in Ihre SIEM-Tools wie [Sumologic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md), [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md) oder [ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md) zu integrieren.

Weitere Informationen zum Einrichten von Azure Monitor finden Sie unter [Azure AD-Aktivitätsprotokolle in Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualisieren Ihrer Benutzer auf die neue, erweiterte Benutzeroberfläche für Sicherheitsinformationen

**Typ:** Geändertes Feature-**Dienstkategorie:**  Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Am 25. September 2019 werden wir die alte, nicht erweiterte Benutzeroberfläche für Sicherheitsinformationen zum Registrieren und Verwalten von Benutzersicherheitsinformationen einstellen und nur noch die neue, [erweiterte Version](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271) aktivieren. Dies bedeutet, dass Ihre Benutzer die alte Benutzeroberfläche nicht mehr verwenden können.

Weitere Informationen zur erweiterten Benutzeroberfläche für Sicherheitsinformationen finden Sie in unserer [Administratordokumentation](../authentication/concept-registration-mfa-sspr-combined.md) und in unserer [Benutzerdokumentation](../user-help/security-info-setup-signin.md).

#### <a name="to-turn-on-this-new-experience-you-must"></a>So aktivieren Sie diese neue Funktion

1. Melden Sie sich als globaler Administrator oder Benutzeradministrator beim Azure-Portal an.

2. Navigieren Sie zu **Azure Active Directory > Benutzereinstellungen > Einstellungen für Zugriffspanel-Vorschaufeatures verwalten**.

3. Wählen Sie im Bereich **Benutzer können Vorschaufeatures zum Registrieren und Verwalten von Sicherheitsinformationen verwenden (erweitert)** die Option **Ausgewählt** aus, und wählen Sie dann entweder eine Benutzergruppe oder **Alle** aus, um dieses Feature für alle Benutzer im Mandanten zu aktivieren.

4. Wählen Sie im Bereich „Benutzer können Vorschaufeatures zum Registrieren und Verwalten von Sicherheitsinformationen verwenden (erweitert)“ die Option **Keine** aus.

5. Speichern Sie die Einstellungen.

    Nach dem Speichern der Einstellungen haben Sie keinen Zugriff mehr auf die alte Benutzeroberfläche für Sicherheitsinformationen.

>[!Important]
>Wenn Sie diese Schritte nicht vor dem 25. September 2019 ausführen, wird für Ihren Azure Active Directory-Mandanten automatisch die erweiterte Benutzeroberfläche aktiviert. Wenn Sie Fragen haben, kontaktieren Sie uns unter registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Authentifizierungsanforderungen mit POST-Anmeldungen strenger validiert

**Typ:** Geändertes Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Standards

Ab dem 2. September 2019 werden Authentifizierungsanforderungen, die die POST-Methode verwenden, genauer anhand der HTTP-Standards überprüft. Insbesondere werden Leerzeichen und doppelte Anführungszeichen (") nicht mehr aus den Anforderungsformularwerten entfernt. Es wird nicht erwartet, dass diese Änderungen vorhandene Clients unterbrechen. Durch diese Änderungen soll sichergestellt werden, dass an Azure AD gesendete Anforderungen jedes Mal zuverlässig verarbeitet werden.

Weitere Informationen finden Sie in den [Hinweisen zu Breaking Changes in Azure AD](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Geplante Änderung: Aktualisierung des Anwendungsproxydiensts zur ausschließlichen Unterstützung von TLS 1.2

**Typ:** Plan für Änderung der **Dienstkategorie:** App-Proxy-**Produktfunktion:** Zugriffssteuerung

Zur Bereitstellung einer bestmöglichen Verschlüsselung wird der Anwendungsproxydienst-Zugriff künftig ausschließlich auf TLS 1.2-Protokolle beschränkt. Diese Einschränkung wird zunächst für Kunden eingeführt, die bereits TLS 1.2-Protokolle verwenden, deshalb hat diese Änderung keine sichtbaren Auswirkungen. Die vollständige Einstellung der Protokolle TLS 1.0 und TLS 1.1 erfolgt am 31. August 2019. Kunden, die zurzeit noch TLS 1.0 und TLS 1.1 verwenden, werden rechtzeitig benachrichtigt, um sich auf diese Änderung vorzubereiten.

Um die Verbindung mit dem Anwendungsproxydienst während der Umstellung sicherzustellen, wird empfohlen, die Aktualisierung aller Client/Server- und Browser/Server-Kombinationen auf die Verwendung von TLS 1.2 sicherzustellen. Beziehen Sie bei dieser Aktualisierung auch alle Clientsysteme ein, die von Ihren Mitarbeitern für den Zugriff auf Apps verwendet werden, die über den Anwendungsproxydienst veröffentlicht werden.

Weitere Informationen finden Sie unter [Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Geplante Änderung: Aktualisiertes Design für den Anwendungskatalog

**Typ:** Plan für Änderung der **Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Für das Design des Bereichs **Aus Katalog hinzufügen** auf dem Blatt **Anwendung hinzufügen** sind Änderungen an der Benutzeroberfläche geplant. Durch diese Änderungen können Sie leichter nach Apps suchen, die eine automatische Bereitstellung, OpenID Connect, SAML (Security Assertion Markup Language) und Kennwort-SSO (Single Sign-On) unterstützen.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Geplante Änderung: Entfernung der MFA-Server-IP-Adresse aus der Office 365-IP-Adresse

**Typ:** Plan für Änderung der **Dienstkategorie:** MFA-**Produktfunktion:** Identitätssicherheit und -schutz

Die MFA-Server-IP-Adresse wird aus der [Office 365-IP-Adresse und dem URL-Webdienst](/office365/enterprise/office-365-ip-web-service) entfernt. Wenn Sie sich aktuell auf diese Seiten stützen, um Ihre Firewalleinstellungen zu aktualisieren, müssen Sie sicherstellen, dass auch die Liste der IP-Adressen einbezogen wird, die im Abschnitt **Anforderungen an die Azure Multi-Factor Authentication-Server-Firewall** im Artikel [Erste Schritte mit Azure Multi-Factor Authentication-Server](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements) dokumentiert ist.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Für reine App-Token muss die Client-App ab sofort im Ressourcenmandanten vorhanden sein

**Typ:** Feste **Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Am 26. Juli 2019 wurde die Bereitstellung von reinen App-Token über die [Zuweisung von Clientanmeldeinformationen](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) geändert. Bisher konnten Apps Token abrufen, um andere Apps aufzurufen – unabhängig davon, ob die Client-App sich im Mandanten befand. Dieses Verhalten wurde aktualisiert, sodass Ressourcen mit einem einzelnen Mandanten (gelegentlich auch Web-APIs genannt) nur durch Client-Apps aufgerufen werden können, die im Ressourcenmandanten enthalten sind.

Wenn Ihre App nicht im Ressourcenmandanten enthalten ist, erhalten Sie diese Fehlermeldung: `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`. Um das Problem zu beheben, müssen Sie den Client-App-Dienstprinzipal im Mandanten erstellen, entweder über den [Endpunkt für die Administratorzustimmung](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) oder [über PowerShell](../develop/howto-authenticate-service-principal-powershell.md). So wird sichergestellt, dass Ihr Mandant der App die Berechtigung zum Betrieb innerhalb des Mandanten erteilt hat.

Weitere Informationen finden Sie unter [Neuerungen bei der Authentifizierung](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Eine vorhandene Zustimmung zwischen Client und API ist nach wie vor nicht erforderlich. Apps müssen weiterhin eigene Autorisierungsüberprüfungen durchführen.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Neue Anmeldung ohne Kennwort bei Azure AD über FIDO2-Sicherheitsschlüssel

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Azure AD-Kunden können ab sofort Richtlinien zum Verwalten von FIDO2-Sicherheitsschlüsseln für die Benutzer und Gruppen in ihrer Organisation festlegen. Endbenutzer können ihre Sicherheitsschlüssel außerdem selbst registrieren, die Schlüssel bei der Arbeit auf FIDO-fähigen Geräten zur Anmeldung bei ihren Microsoft-Konten für Websites verwenden sowie sich bei ihren in Azure AD eingebundenen Windows 10-Geräten anmelden.

Weitere Informationen für Administratoren finden Sie unter [Aktivieren des kennwortlosen Anmeldens für Azure AD (Vorschauversion)](../authentication/concept-authentication-passwordless.md), Informationen für Endbenutzer finden Sie unter [Einrichten der Sicherheitsinformationen zur Verwendung eines Sicherheitsschlüssels (Vorschau)](../user-help/security-info-setup-security-key.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Juli 2019

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Juli 2019 haben wir diese 18 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Ungerboeck Software](../saas-apps/ungerboeck-software-tutorial.md), [Bright Pattern Omnichannel Contact Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), [Clever Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO Access for Ethidex Compliance Office&trade;](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [Hype](../saas-apps/hype-tutorial.md), [Abstract](../saas-apps/abstract-tutorial.md), [Ascentis](../saas-apps/ascentis-tutorial.md), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](../saas-apps/wandera-tutorial.md), [TwineSocial](https://twinesocial.com/), [Kallidus](../saas-apps/kallidus-tutorial.md), [HyperAnna](../saas-apps/hyperanna-tutorial.md), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisieren der Bereitstellung von Benutzerkonten für diese neu unterstützten SaaS-Apps

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Überwachung und Berichterstellung

Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser sichern können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Neues Azure AD Domain Services-Diensttag für Netzwerksicherheitsgruppe

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD Domain Services-**Produktfunktion:** Azure AD Domain Services

Wenn Sie nicht länger lange Listen von IP-Adressen und Adressbereichen verwalten möchten, können Sie das neue **AzureActiveDirectoryDomainServices**-Netzwerkdiensttag in Ihrer Azure-Netzwerksicherheitsgruppe verwenden, um den eingehenden Datenverkehr im Subnetz Ihres virtuellen Azure AD Domain Services-Netzwerks zu schützen.

Weitere Informationen zu diesem neuen Diensttag finden Sie unter [Netzwerkaspekte für die Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Neue Sicherheitsüberwachungen für Azure AD Domain Services (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD Domain Services-**Produktfunktion:** Azure AD Domain Services

Wir freuen uns, die Veröffentlichung der Azure AD Domain Services-Sicherheitsüberwachung in der öffentlichen Vorschau bekanntzugeben. Die Sicherheitsüberwachung bietet Ihnen wertvollen Einblick in Ihre Authentifizierungsdienste, indem Sicherheitsüberwachungsereignisse über das Azure AD Domain Service-Portal an Zielressourcen wie Azure Storage, Azure Log Analytics-Arbeitsbereiche und Azure Event Hubs gestreamt werden.

Weitere Informationen finden Sie unter [Aktivieren von Sicherheitsüberwachungen in Azure AD Domain Services (Vorschauversion)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Neue Berichte „Authentifizierungsmethoden: Nutzung und Erkenntnisse“ (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Self-Service-Kennwortzurücksetzungs-**Produktfunktion:** Überwachung und Berichterstellung

Die neuen Berichte „Authentifizierungsmethoden: Nutzung und Erkenntnisse“ ermöglichen ein besseres Verständnis der Registrierung und Verwendung von Features wie Azure AD Multi-Factor Authentication und Self-Service-Kennwortzurücksetzung innerhalb Ihrer Organisation. Beispielsweise erfahren Sie, wie viele Benutzer für jedes Feature registriert sind, wie häufig die Self-Service-Kennwortzurücksetzung zum Zurücksetzen von Kennwörtern verwendet wird und über welche Methode die Zurücksetzung erfolgt.

Weitere Informationen finden Sie unter [Verwendung und Erkenntnisse von Authentifizierungsmethoden (Vorschau)](../authentication/howto-authentication-methods-usage-insights.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Neue Sicherheitsberichte für alle Azure AD-Administratoren (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Alle Azure AD-Administratoren können nun das Banner im oberen Bereich vorhandener Sicherheitsberichte – beispielsweise den Bericht **Benutzer mit Risikomarkierung** – auswählen, um die neue Sicherheitsfunktionalität zu nutzen, wie in den Berichten **Riskante Benutzer** und **Riskante Anmeldungen** gezeigt. Im weiteren Verlauf werden alle Sicherheitsberichte von den alten Versionen auf die neuen Versionen umgestellt, wobei die neuen Berichte die folgenden zusätzlichen Funktionen bieten:

- Erweiterte Filterung und Sortierung

- Massenaktionen, z.B. das Verwerfen des Benutzerrisikos

- Bestätigung kompromittierter oder sicherer Entitäten

- Risikostatus, darunter: „Gefährdet“, „Verworfen“, „Bereinigt“ und „Gefährdung bestätigt“

Weitere Informationen finden Sie unter [Bericht „Riskante Benutzer“](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) und [Bericht über riskante Anmeldungen](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Neue Sicherheitsüberwachungen für Azure AD Domain Services (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD Domain Services-**Produktfunktion:** Azure AD Domain Services

Wir freuen uns, die Veröffentlichung der Azure AD Domain Services-Sicherheitsüberwachung in der öffentlichen Vorschau bekanntzugeben. Die Sicherheitsüberwachung bietet Ihnen wertvollen Einblick in Ihre Authentifizierungsdienste, indem Sicherheitsüberwachungsereignisse über das Azure AD Domain Service-Portal an Zielressourcen wie Azure Storage, Azure Log Analytics-Arbeitsbereiche und Azure Event Hubs gestreamt werden.

Weitere Informationen finden Sie unter [Aktivieren von Sicherheitsüberwachungen in Azure AD Domain Services (Vorschauversion)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Neuer B2B-Direktverbund über SAML/WS-Verbund (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** B2B-**Produktfunktion:** B2B/B2C

Der Direktverbund erleichtert die Zusammenarbeit mit Partnern, die eine andere, von der IT-verwaltete Identitätslösung als Azure AD verwenden. Erreicht wird dies durch die Zusammenarbeit mit Identitätssystemen, die die SAML- oder WS-Verbundstandards unterstützen. Nachdem Sie eine Direktverbundbeziehung mit einem Partner eingerichtet haben, können neu eingeladene Gastbenutzer aus dieser Domäne unter Verwendung ihrer vorhandenen Organisationskonten mit Ihnen zusammenarbeiten. Dies ermöglicht eine nahtlosere Zusammenarbeit für Ihre Gastbenutzer.

Weitere Informationen finden Sie unter [Direkter Verbund mit AD FS und Drittanbietern für Gastbenutzer (Vorschau)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisieren der Bereitstellung von Benutzerkonten für diese neu unterstützten SaaS-Apps

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Überwachung und Berichterstellung

Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser schützen können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Neue Überprüfung auf doppelte Gruppennamen im Azure AD-Portal

**Typ:** Neues Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Wenn Sie einen Gruppennamen über das Azure AD-Portal erstellen oder aktualisieren, wird ab sofort eine Überprüfung durchgeführt, um festzustellen, ob Sie einen vorhandenen Gruppennamen in Ihrer Ressource duplizieren. Wenn festgestellt wird, dass der Name bereits von einer anderen Gruppe verwendet wird, werden Sie aufgefordert, den Namen zu ändern.

Weitere Informationen finden Sie unter [Verwalten von Gruppen im Azure AD-Portal](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD unterstützt ab sofort statische Abfrageparameter in Antwort-URIs (Umleitungs-URIs)

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Azure AD-Apps können jetzt Antwort-URIs mit statischen Abfrageparametern (z.B. `https://contoso.com/oauth2?idp=microsoft`) für OAuth 2.0-Anforderungen registrieren und verwenden. Der statische Abfrageparameter wird, ebenso wie jeder andere Bestandteil des Antwort-URI, einem Zeichenfolgenabgleich unterzogen. Wenn keine registrierte Zeichenfolge vorhanden ist, die dem URL-decodierten Umleitungs-URI entspricht, wird die Anforderung abgelehnt. Wird der Antwort-URI gefunden, wird die gesamte Zeichenfolge – einschließlich des statischen Abfrageparameters – zum Umleiten des Benutzers verwendet.

Dynamische Antwort-URIs sind weiterhin untersagt, weil sie ein Sicherheitsrisiko darstellen und nicht dazu verwendet werden können, statische Informationen über eine Authentifizierungsanforderung hinweg beizubehalten. Verwenden Sie zu diesem Zweck den `state`-Parameter.

Aktuell werden Abfrageparameter in den Bildschirmen für die App-Registrierung im Azure-Portal weiterhin blockiert. Sie können jedoch das App-Manifest manuell bearbeiten, um Abfrageparameter in Ihre App einzufügen und diese zu testen. Weitere Informationen finden Sie unter [Neuerungen bei der Authentifizierung](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Aktivitätsprotokolle (MS Graph-APIs) für Azure AD jetzt über PowerShell-Cmdlets verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Wir freuen uns, ankündigen zu können, dass Azure AD-Aktivitätsprotokolle (Überwachungsberichte und Berichte zu Anmeldungen) jetzt über das Azure AD-PowerShell-Modul zur Verfügung stehen. Bisher konnten Sie eigene Skripts über MS Graph-API-Endpunkte erstellen. Jetzt wurde diese Funktionalität auf PowerShell-Cmdlets ausgeweitet.

Weitere Informationen zur Verwendung dieser Cmdlets finden Sie unter [Azure AD PowerShell-Cmdlets für die Berichterstellung](../reports-monitoring/reference-powershell-reporting.md).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Aktualisierte Filtersteuerelemente für Überwachungs- und Anmeldeprotokolle in Azure AD

**Typ:** Geändertes Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Die Berichte zu Überwachungs- und Anmeldeprotokollen wurden aktualisiert, sodass Sie nun verschiedene Filter anwenden können, ohne diese als Spalten auf den Berichtsbildschirmen hinzufügen zu müssen. Zusätzlich können Sie ab sofort entscheiden, wie viele Filter Sie auf dem Bildschirm anzeigen möchten. All diese Updates greifen ineinander, sodass Ihre Berichte besser lesbar sind und genauer an Ihre Anforderungen angepasst werden können.

Weitere Informationen zu diesen Aktualisierungen finden Sie unter [Filtern von Überwachungsprotokollen](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) und [Filtern von Anmeldeaktivitäten](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Neue riskDetection-API für Microsoft Graph (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Wir freuen uns, Ihnen mitteilen zu können, dass die neue riskDetection-API für Microsoft Graph ab sofort in der öffentlichen Vorschau verfügbar ist. Sie können diese neue API verwenden, um eine Liste der Erkennungen von identitätsschutzbezogenen Benutzern und Anmelderisiken in Ihrem Unternehmen anzuzeigen. Sie können diese API auch verwenden, um Ihre Risikoerkennung effizienter abzufragen, einschließlich Details über die Erkennungstyp, Status, Level und vieles mehr.

Weitere Informationen finden Sie in der [Referenzdokumentation zur riskDetection-API](/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Juni 2019

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Juni 2019 haben wir diese 22 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](../saas-apps/expensein-tutorial.md), [Helper Helper](../saas-apps/helper-helper-tutorial.md), [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md), [CyberArk SAML Authentication](../saas-apps/cyberark-saml-authentication-tutorial.md), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisieren der Bereitstellung von Benutzerkonten für diese neu unterstützten SaaS-Apps

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Überwachung und Berichterstellung

Sie können ab sofort das Erstellen, Aktualisieren und Löschen von Benutzerkonten für diese neu integrierten Apps automatisieren:

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser sichern können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Anzeigen des Fortschritts des Azure AD-Bereitstellungsdiensts in Echtzeit

**Typ:** Geändertes Feature-**Dienstkategorie:** App-Bereitstellungs-**Produktfunktion:** Identity Lifecycle Management

Wir haben die Azure AD-Bereitstellungsbenutzeroberfläche mit einer neuen Statusanzeige aktualisiert, die Ihnen anzeigt, wie weit Sie im Prozess der Benutzerbereitstellung fortgeschritten sind. Diese aktualisierte Benutzeroberfläche liefert auch Informationen über die Anzahl der Benutzer, die während des aktuellen Zyklus bereitgestellt wurden, sowie darüber, wie viele Benutzer bisher bereitgestellt wurden.

Weitere Informationen finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Unternehmensbranding wird jetzt auf Abmelde- und Fehlerbildschirmen angezeigt.

**Typ:** Geändertes Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Wir haben Azure AD so aktualisiert, dass Ihr Unternehmensbranding nun auf den Abmelde- und Fehlerbildschirmen sowie auf der Anmeldeseite angezeigt wird. Sie müssen dieses Feature nicht explizit aktivieren. Azure AD verwendet einfach die Ressourcen, die Sie bereits im Bereich **Unternehmensbranding** des Azure-Portals eingerichtet haben.

Weitere Informationen zur Einrichtung Ihres Unternehmensbrandings finden Sie unter [Hinzufügen von Branding zur Azure Active Directory-Anmeldeseite Ihrer Organisation](./customize-branding.md).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication-Server (MFA) ist nicht mehr für neue Bereitstellungen verfügbar

**Typ:** Veraltete **Dienstkategorie:** MFA-**Produktfunktion:** Identitätssicherheit und -schutz

Ab dem 1. Juli 2019 bietet Microsoft keine MFA-Server mehr für neue Bereitstellungen an. Neue Kunden, die eine Multi-Factor Authentication in ihrer Organisation einrichten möchten, müssen jetzt die cloudbasierte Multi-Factor Authentication von Azure AD verwenden. Kunden, die MFA-Server vor dem 1. Juli aktiviert haben, werden keine Änderung feststellen. Sie können weiterhin die neueste Version herunterladen, zukünftige Updates erhalten und Aktivierungsdaten generieren.

Weitere Informationen finden Sie unter [Erste Schritte mit Azure Multi-Factor Authentication-Server](../authentication/howto-mfaserver-deploy.md). Weitere Informationen zur cloudbasierten Azure AD Multi-Factor Authentication finden Sie unter [Planen einer cloudbasierten Azure AD Multi-Factor Authentication-Bereitstellung](../authentication/howto-mfa-getstarted.md).

---

## <a name="may-2019"></a>Mai 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Änderung des Diensts: Zukünftige Unterstützung nur für TLS 1.2-Protokolle auf dem Anwendungsproxy-Dienst

**Typ:** Plan für Änderung der **Dienstkategorie:** App-Proxy-**Produktfunktion:** Zugriffssteuerung

Zur Bereitstellung erstklassiger Verschlüsselung für unsere Kunden beschränken wir den Zugriff ausschließlich auf TLS 1.2-Protokolle im Anwendungsproxydienst. Diese Änderung wird schrittweise für Kunden eingeführt, die bereits ausschließlich TLS 1.2-Protokolle verwenden, sodass Sie keine Änderungen feststellen sollten.

TLS 1.0 und TLS 1.1 sind am 31. August 2019 veraltet, aber wir werden noch zusätzliche erweiterte Benachrichtigungen senden, sodass Sie Zeit haben, sich auf die Änderung vorzubereiten. Stellen Sie zur Vorbereitung auf diese Änderung sicher, dass Ihre Client-Server- und Browser-Server-Kombinationen einschließlich aller Clients, mit denen Ihre Benutzer auf Apps zugreifen, die durch den Anwendungsproxy veröffentlicht werden, zur Verwendung des TLS 1.2-Protokolls aktualisiert werden, um die Verbindung mit dem Anwendungsproxy-Dienst zu verwalten. Weitere Informationen finden Sie unter [Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Verwenden des Nutzungs- und Erkenntnisberichts zum Anzeigen Ihrer app-bezogenen Anmeldungsdaten

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Überwachung und Berichterstellung

Nun können Sie mit dem Nutzungs- und Erkenntnisbericht im Bereich **Unternehmensanwendungen** des Azure-Portals eine anwendungsorientierte Ansicht Ihrer Anmeldungsdaten abrufen, einschließlich Informationen über:

- Am meisten verwendete Apps in Ihrer Organisation

- Apps mit den meisten fehlerhaften Anmeldungen

- Häufigste Anmeldefehler bei den einzelnen Apps

Weitere Informationen zu diesem Feature finden Sie unter [Bericht „Nutzung & Erkenntnisse“ im Azure Active Directory-Portal](../reports-monitoring/concept-usage-insights-report.md).

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatisieren Ihrer Benutzerbereitstellung für Cloud-Apps mit Azure AD

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Überwachung und Berichterstellung

Befolgen Sie diese neuen Tutorials, um den Azure AD-Bereitstellungsdienst zu verwenden, um das Erstellen, Löschen und Aktualisieren von Benutzerkonten für die folgenden, cloudbasierten Apps zu automatisieren:

- [Comeet](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Sie können auch dieses neue [Dropbox-Tutorial](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) befolgen, das Informationen zur Bereitstellung von Gruppenobjekten enthält.

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser sichern können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identity Secure Score ist jetzt in Azure AD verfügbar (allgemeine Verfügbarkeit)

**Typ:** Neues Feature-**Dienstkategorie:** N/V-**Produktfunktion:** Identitätssicherheit und -schutz

Sie können jetzt Ihren Identitätssicherheitsstatus mit dem Feature Identity Secure Score in Azure AD überwachen und verbessern. Das Feature Identity Secure Score nutzt ein einzelnes Dashboard, um Sie bei Folgendem zu unterstützen:

- Objektives Messen Ihres Identitätssicherheitsstatus mit einer Bewertung von 1 bis 223

- Planen von Verbesserungen Ihrer Identitätssicherheit

- Überprüfen des Erfolgs Ihrer Sicherheitsverbesserungen

Weitere Informationen zum Feature Identity Secure Score finden Sie unter [Was ist der Identity Secure Score in Azure Active Directory?](./identity-secure-score.md).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Neue Benutzeroberfläche für App-Registrierungen ist jetzt verfügbar (allgemeine Verfügbarkeit)

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Entwickleroberfläche

Die Benutzeroberfläche [App-Registrierungen](https://aka.ms/appregistrations) ist jetzt allgemein verfügbar. Diese neue Oberfläche umfasst alle wesentlichen Funktionen, die Ihnen aus dem Azure-Portal und dem Anwendungsregistrierungsportal vertraut sind, und verbessert sie folgendermaßen:

- **Bessere App-Verwaltung.** Anstatt Ihre Apps in verschiedenen Portalen anzuzeigen, können Sie jetzt alle Apps an einem Ort sehen.

- **Vereinfachte App-Registrierung.** Von der verbesserten Navigationserfahrung bis zur überarbeiteten Berechtigungsauswahlerfahrung können Sie Ihre Apps jetzt einfacher registrieren und verwalten.

- **Ausführlichere Informationen.** Sie finden weitere Informationen zu Ihrer App einschließlich Schnellstarthandbüchern und vielem mehr.

Weitere Informationen finden Sie unter [Microsoft Identity Platform (vormals Azure Active Directory für Entwickler)](../develop/index.yml) und [App registrations experience is now generally available! (Benutzerfreundliche App-Registrierung ist jetzt allgemein verfügbar!)](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/). Blogankündigung.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Neue Funktionen verfügbar in der riskyUser-API für Schutz der Identität (Identity Protection)

**Typ:** Neues Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Wir freuen uns, ankündigen zu können, dass Sie jetzt die riskyUser-API verwenden können, um den Risikoverlauf von Benutzern abzurufen, riskante Benutzer zu schließen und Benutzer als gefährdet zu bestätigen. Mit dieser Änderung können Sie effizienter den Risikostatus Ihrer Benutzer aktualisieren und ihren Risikoverlauf verstehen.

Weitere Informationen finden Sie in der [Referenzdokumentation zur riskyUser-API](/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Mai 2019

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Mai 2019 haben wir diese 21 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [Real Links](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [Displayr](../saas-apps/displayr-tutorial.md), [Templafy](../saas-apps/templafy-tutorial.md), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](../saas-apps/aclp-tutorial.md), [OutSystems](../saas-apps/outsystems-tutorial.md), [Meta4 Global HR](../saas-apps/meta4-global-hr-tutorial.md), [Quantum Workplace](../saas-apps/quantum-workplace-tutorial.md), [Cobalt](../saas-apps/cobalt-tutorial.md), [webMethods API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [NEOGOV](../saas-apps/neogov-tutorial.md), [Foodee](../saas-apps/foodee-tutorial.md), [MyVR](../saas-apps/myvr-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Verbesserte Gruppenerstellung und Verwaltungserfahrungen im Azure AD-Portal

**Typ:** Neues Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Wir haben die gruppenbezogenen Erfahrungen im Azure AD-Portal verbessert. Diese Verbesserungen ermöglichen Administratoren, Gruppenlisten und Mitgliederlisten besser zu verwalten und weitere Erstellungsoptionen bereitzustellen.

Die Verbesserungen umfassen:

- Einfaches Filtern von Mitgliedschaftstyp und Gruppentyp.

- Hinzufügen neuer Spalten, z.B. Quelle und E-Mail-Adresse.

- Möglichkeit zum Auswählen mehrerer Gruppen-, Mitglieder. und Besitzerlisten zum mühelosen Löschen.

- Möglichkeit zum Auswählen einer E-Mail-Adresse und Hinzufügen von Besitzern während der Gruppenerstellung.

Weitere Informationen dazu finden Sie in [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurieren einer Benennungsrichtlinie für Office 365-Gruppen im Azure AD-Portal (allgemeine Verfügbarkeit)

**Typ:** Geändertes Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Administratoren können jetzt im Azure AD-Portal eine Benennungsrichtlinie für Office 365-Gruppen konfigurieren. Diese Änderung hilft, einheitliche Namenskonventionen für Office 365-Gruppen durchzusetzen, die von Benutzern in Ihrer Organisation erstellt oder bearbeitet werden.

Sie haben zwei Möglichkeiten, Benennungsrichtlinien für Office 365-Gruppen zu konfigurieren:

- Definieren Sie Präfixe oder Suffixe, die einem Gruppennamen automatisch hinzugefügt werden.

- Laden Sie eine benutzerdefinierte Liste blockierter Wörter hoch, die in Gruppennamen Ihrer Organisation nicht zulässig sind (z. B. „CEO, Gehalt, Personalabteilung“).

Weitere Informationen finden Sie unter [Erzwingen einer Benennungsrichtlinie für Office 365-Gruppen](../enterprise-users/groups-naming-policy.md).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph-API-Endpunkte sind jetzt für Azure AD-Aktivitätsprotokolle verfügbar (allgemeine Verfügbarkeit)

**Typ:** Geändertes Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Wir freuen uns, Ihnen die allgemeine Verfügbarkeit der Unterstützung von Microsoft Graph-API-Endpunkten für Azure AD-Aktivitätsprotokolle ankündigen zu können. Mit diesem Release können Sie nun Version 1.0 von Azure AD-Überwachungsprotokollen als auch die Anmeldeprotokolle-APIs verwenden.

Weitere Informationen hierzu finden Sie unter [Übersicht über die Überwachungsprotokoll-API in Azure AD](/graph/api/resources/azure-ad-auditlog-overview).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratoren können jetzt den bedingten Zugriff für den kombinierten Registrierungsprozess (öffentliche Vorschau) verwenden.

**Typ:** Neues Feature-**Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Administratoren können jetzt Richtlinien für bedingten Zugriff für die Verwendung durch die kombinierte Registrierungsseite erstellen. Dies umfasst die Anwendung von Richtlinien, um die Registrierung zuzulassen, wenn:

- Benutzer sich in einem vertrauenswürdigen Netzwerk befinden.

- Benutzer ein geringes Anmelderisiko darstellen.

- Benutzer sich auf einem verwalteten Gerät befinden.

- Benutzer den Nutzungsbedingungen (Terms Of Use, TOU) der Organisation zustimmen.

Weitere Informationen zu bedingtem Zugriff und Zurücksetzen von Kennwörtern finden Sie in dem [Blogbeitrag über bedingten Zugriff für die kombinierte MFA- und Kennwortzurücksetzungsregistrierung in Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Weitere Informationen zu Richtlinien für bedingten Zugriff für den kombinierten Registrierungsprozess finden Sie unter [Richtlinien für bedingten Zugriff für die kombinierte Registrierung](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration). Weitere Informationen zu den Azure AD-Nutzungsbedingungen finden Sie unter [Anzeige von Nutzungsbedingungen für Benutzer](../conditional-access/terms-of-use.md).

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Neue Azure AD Threat Intelligence-Erkennung ist jetzt im Rahmen von Azure AD Identity Protection verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Die Azure AD Threat Intelligence-Erkennung ist jetzt im Rahmen des aktualisierten Features „Azure AD Identity Protection“ verfügbar. Diese neue Funktionalität zeigt ungewöhnliche Benutzeraktivitäten für einen bestimmten Benutzer oder Aktivitäten an, die bekannten Angriffsmustern entsprechen (basierend auf internen und externen Threat Intelligence-Quellen von Microsoft).

Weitere Informationen über die aktualisierte Version von Azure AD Identity Protection finden Sie im Blog [Four major Azure AD Identity Protection enhancements are now in public preview (Vier wichtige Verbesserungen von Azure AD Identity Protection sind jetzt in der öffentlichen Vorschau)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) und im Artikel [Was ist Azure Active Directory Identity Protection (aktualisiert)?](../identity-protection/overview-identity-protection.md) . Weitere Informationen zur Azure AD Threat Intelligence-Erkennung finden Sie im Artikel [Referenz für Risikoerkennungen in Azure Active Directory Identity Protection](../identity-protection/concept-identity-protection-risks.md).

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Die Berechtigungsverwaltung von Azure AD ist jetzt öffentlich verfügbar (Public Preview)

**Typ:** Neues Feature-**Dienstkategorie:** Identity Governance-**Produktfunktion:** Identity Governance

Die Berechtigungsverwaltung von Azure AD ist jetzt in der Public Preview verfügbar und hilft Kunden, die Verwaltung von Zugriffspaketen zu delegieren. Dabei wird neben der Zugriffsdauer auch definiert, wie Mitarbeiter und Geschäftspartner Zugriff anfordern können und wer den Zugriff genehmigen muss. Mit Zugriffspaketen können Mitgliedschaften in Azure AD und Office 365-Gruppen, Rollenzuweisungen in Unternehmensanwendungen und Rollenzuweisungen für SharePoint Online-Sites verwaltet werden. Weitere Informationen zur Berechtigungsverwaltung finden Sie in der [Übersicht zur Berechtigungsverwaltung von Azure AD](../governance/entitlement-management-overview.md). Weitere Informationen zu den vielseitigen Funktionen von Azure AD Identity Governance, einschließlich Privileged Identity Management, Zugriffsüberprüfungen und Nutzungsbedingungen, finden Sie unter [Was ist Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurieren einer Benennungsrichtlinie für Office 365-Gruppen im Azure AD-Portal (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Administratoren können jetzt im Azure AD-Portal eine Benennungsrichtlinie für Office 365-Gruppen konfigurieren. Diese Änderung hilft, einheitliche Namenskonventionen für Office 365-Gruppen durchzusetzen, die von Benutzern in Ihrer Organisation erstellt oder bearbeitet werden.

Sie haben zwei Möglichkeiten, Benennungsrichtlinien für Office 365-Gruppen zu konfigurieren:

- Definieren Sie Präfixe oder Suffixe, die einem Gruppennamen automatisch hinzugefügt werden.

- Laden Sie eine benutzerdefinierte Liste blockierter Wörter hoch, die in Gruppennamen Ihrer Organisation nicht zulässig sind (z. B. „CEO, Gehalt, Personalabteilung“).

Weitere Informationen finden Sie unter [Erzwingen einer Benennungsrichtlinie für Office 365-Gruppen](../enterprise-users/groups-naming-policy.md).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-Aktivitätsprotokolle jetzt in Azure Monitor verfügbar (Allgemeine Verfügbarkeit)

**Typ:** Neues Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

In Reaktion auf Ihr Feedback zu Visualisierungen anhand von Azure AD-Aktivitätsprotokollen haben wir ein neues Insights-Feature in Log Analytics eingeführt. Dieses Feature umfasst interaktive Vorlagen, so genannte „Arbeitsmappen“, die auf einfache Weise Einblicke in Ihre Azure AD-Ressourcen bieten. Die vorkonfigurierten Arbeitsmappen liefern Details zu Apps oder Benutzern und umfassen Folgendes:

- **Anmeldungen**: Enthält Details zu Apps und Benutzern, einschließlich Anmeldeort, verwendetes Betriebssystem oder Browserclient und -version sowie Anzahl erfolgreicher oder fehlerhafter Anmeldungen.

- **Legacyauthentifizierung und bedingter Zugriff.** Enthält Details zu Apps und Benutzern, die die Legacyauthentifizierung verwenden, einschließlich Multi-Factor Authentication, die durch Richtlinien für bedingten Zugriff ausgelöst wird, Apps mit Richtlinien für bedingten Zugriff usw.

- **Analyse von Anmeldefehlern**: Hiermit können Sie ermitteln, ob Anmeldefehler aufgrund von Benutzeraktionen, Richtlinienproblemen oder der Infrastruktur auftreten.

- **Benutzerdefinierte Berichte**: Sie können neue Arbeitsmappen erstellen oder vorhandene bearbeiten, um das Insights-Feature an die Anforderungen Ihrer Organisation anzupassen.

Weitere Informationen finden Sie unter [Verwenden von Azure Monitor-Arbeitsmappen für Azure Active Directory-Berichte](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – April 2019

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im April 2019 haben wir diese 21 neuen Apps mit Verbundunterstützung in den App-Katalog aufgenommen:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks Single Sign-On](../saas-apps/hrworks-single-sign-on-tutorial.md), [Percolate](../saas-apps/percolate-tutorial.md), [MobiControl](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md), [Shibumi](../saas-apps/shibumi-tutorial.md), [Benchling](../saas-apps/benchling-tutorial.md), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](../saas-apps/pagedna-tutorial.md), [EduBrite LMS](../saas-apps/edubrite-lms-tutorial.md), [RStudio Connect](../saas-apps/rstudio-connect-tutorial.md), [AMMS](../saas-apps/amms-tutorial.md), [Mitel Connect](../saas-apps/mitel-connect-tutorial.md), [Alibaba Cloud (Role-based SSO)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [Certent Equity Management](../saas-apps/certent-equity-management-tutorial.md), [Sectigo Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md), [GreenOrbit](../saas-apps/greenorbit-tutorial.md), [Workgrid](../saas-apps/workgrid-tutorial.md), [monday.com](../saas-apps/mondaycom-tutorial.md), [SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md), [Indiggo](https://indiggolead.com/)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Neue Häufigkeitsoption und Auswahl mehrerer Rollen für Zugriffsüberprüfungen

**Typ:** Neues Feature-**Dienstkategorie:** Zugriffsüberprüfungen-**Produktfunktion:** Identity Governance

Die Neuerungen in den Azure AD-Zugriffsüberprüfungen bieten folgende Möglichkeiten:

- Sie können die Häufigkeit von Zugriffsüberprüfungen neben den bereits vorhandenen Optionen „Wöchentlich“, „Monatlich“, „Vierteljährlich“ und „Jährlich“ jetzt auch in **Halbjährlich** ändern.

- Sie können beim Erstellen einer einzelnen Zugriffsüberprüfung mehrere Azure AD- und Azure-Ressourcenrollen auswählen. In diesem Fall werden alle Rollen mit denselben Einstellungen eingerichtet, und alle Prüfer werden gleichzeitig benachrichtigt.

Weitere Informationen zum Erstellen einer Zugriffsüberprüfung finden Sie unter [Erstellen einer Zugriffsüberprüfung für Gruppen oder Anwendungen in Azure AD-Zugriffsüberprüfungen](../governance/create-access-review.md).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect-E-Mail-Benachrichtigungssysteme werden umgestellt – für einige Kunden ändern sich die E-Mail-Absenderinformationen

**Typ:** Geändertes Feature-**Dienstkategorie:** AD Sync-**Produktfunktion:** Plattform

Unsere E-Mail-Benachrichtigungssysteme werden derzeit von Azure AD Connect umgestellt. Daher empfangen einige Kunden möglicherweise neue E-Mail-Absenderinformationen. Um dies zu beheben, müssen Sie der Erlaubnisliste Ihrer Organisation `azure-noreply@microsoft.com` hinzufügen, da Sie andernfalls keine wichtigen Benachrichtigungen von Ihren Office 365-, Azure- oder Synchronisierungsdiensten erhalten.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Änderungen am UPN-Suffix zwischen Verbunddomänen in Azure AD Connect jetzt erfolgreich durchführbar

**Typ:** Feste **Dienstkategorie:** AD Sync-**Produktfunktion:** Plattform

Sie können das UPN-Suffix eines Benutzers jetzt erfolgreich von einer Verbunddomäne in eine andere Verbunddomäne in Azure AD Connect ändern. Durch diesen Fix erhalten Sie während des Synchronisierungszyklus nicht mehr die FederatedDomainChangeError-Fehlermeldung oder eine Benachrichtigungs-E-Mail mit folgendem Wortlaut: „Dieses Objekt kann in Azure Active Directory nicht aktualisiert werden, da das Attribut [FederatedUser.UserPrincipalName] ungültig ist. Aktualisieren Sie den Wert in Ihren lokalen Verzeichnisdiensten“.

Weitere Informationen finden Sie unter [Beheben von Fehlern während der Synchronisierung](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Erhöhte Sicherheit mithilfe der Richtlinie für auf App-Schutz basierenden bedingten Zugriff in Azure AD (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Auf App-Schutz basierender bedingter Zugriff steht jetzt über die Richtlinie **App-Schutz erforderlich** zur Verfügung. Diese neue Richtlinie hilft, die Sicherheit in Ihrer Organisation zu erhöhen, indem verhindert wird, dass:

- Benutzer ohne eine Microsoft Intune-Lizenz auf Apps zugreifen können.

- Benutzer eine Microsoft Intune-App-Schutzrichtlinie abrufen können.

- Benutzer ohne eine konfigurierte Microsoft Intune-App-Schutzrichtlinie auf Apps zugreifen können.

Weitere Informationen finden Sie unter [Vorschreiben einer App-Schutzrichtlinie für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs](../conditional-access/app-protection-based-conditional-access.md).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Neue Unterstützung für einmaliges Anmelden und bedingten Zugriff mit Azure AD in Microsoft Edge (Öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Wir haben unsere Azure AD-Unterstützung für Microsoft Edge erweitert, einschließlich der Bereitstellung von neuer Unterstützung für einmaliges Anmelden und bedingten Zugriff mit Azure AD. Wenn Sie bisher Microsoft Intune Managed Browser verwendet haben, können Sie jetzt stattdessen Microsoft Edge verwenden.

Weitere Informationen zum Einrichten und Verwalten Ihrer Geräte und Apps mithilfe des bedingten Zugriffs finden Sie unter [Vorschreiben der Verwendung verwalteter Geräte für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs](../conditional-access/require-managed-devices.md) und [Vorschreiben der Verwendung von genehmigten Client-Apps für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs](../conditional-access/app-based-conditional-access.md). Weitere Informationen zum Verwalten des Zugriffs mithilfe von Microsoft Edge mit Microsoft Intune-Richtlinien finden Sie unter [Verwalten des Internetzugriffs mithilfe eines durch die Microsoft Intune-Richtlinie geschützten Browsers](/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>März 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Unterstützung von Identity Experience Framework und benutzerdefinierter Richtlinie ist jetzt in Azure Active Directory B2C verfügbar (GA)

**Typ:** Neues Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Sie können jetzt in Azure AD B2C benutzerdefinierte Richtlinien inklusive der folgenden Aufgaben erstellen, die bedarfsorientiert und im Rahmen unserer Azure-SLA unterstützt werden:

- Erstellen und Hochladen benutzerdefinierter User Journeys für die Authentifizierung mit benutzerdefinierten Richtlinien.

- Beschreiben von User Journeys als Austauschvorgänge zwischen Anspruchsanbietern (Schritt für Schritt)

- Definieren der bedingten Verzweigung in User Journeys

- Transformieren und Zuordnen von Ansprüchen für die Verwendung in Echtzeitentscheidungen und -kommunikation.

- Verwenden REST-API-fähiger Dienste in Ihren benutzerdefinierten User Journeys für die Authentifizierung. Beispielsweise mit E-Mail-Anbietern, CRMs und proprietären Autorisierungssystemen.

- Einrichten von Verbünden mit Identitätsanbietern, die mit dem OpenIDConnect-Protokoll konform sind. Beispielsweise mit mehrinstanzenfähigem Azure AD, Anbietern von sozialen Konten oder Anbietern der zweistufigen Authentifizierung.

Weitere Informationen zum Erstellen benutzerdefinierter Richtlinien finden Sie unter [Entwicklerhinweise zu benutzerdefinierten Richtlinien in Azure Active Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md), und lesen Sie den [Blogbeitrag von Alex Simon mit den Fallstudien](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – März 2019

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im März 2019 haben wir diese 14 neuen Apps mit Verbundunterstützung in den App-Katalog aufgenommen:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fulcrum](../saas-apps/fulcrum-tutorial.md), [ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md), [Explanation-Based Auditing System](../saas-apps/explanation-based-auditing-system-tutorial.md), [Lean](../saas-apps/lean-tutorial.md), [Powerschool Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode](https://cinode.com/), [Iris Intranet](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw](../saas-apps/smartdraw-tutorial.md), [Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md), [TAS](../saas-apps/tas-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Neue Zscaler- und Atlassian-Bereitstellungsconnectors im Azure AD-Katalog – März 2019

**Typ:** Neues Feature-**Dienstkategorie:** App-Bereitstellungs-**Produktfunktion:** Integration von Drittanbieterprodukten

Automatisieren Sie das Erstellen, Aktualisieren und Löschen von Benutzerkonten für die folgenden Apps:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md), [Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md), [Zscaler Two](../saas-apps/zscaler-two-provisioning-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-provisioning-tutorial.md), [Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Weitere Informationen dazu, wie Sie Ihre Organisation durch die automatisierte Bereitstellung von Benutzerkonten besser sichern können, finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Wiederherstellen und Verwalten Ihrer gelöschten Office 365-Gruppen im Azure AD-Portal

**Typ:** Neues Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Sie können Ihre gelöschten Office 365-Gruppen jetzt vom Azure AD-Portal aus anzeigen und verwalten. Diese Änderung erleichtert Ihnen zu sehen, welche Gruppen wiederhergestellt werden können, und ermöglicht Ihnen gleichzeitig, endgültig alle Gruppen zu löschen, die Ihre Organisation nicht mehr benötigt.

Weitere Informationen finden Sie unter [Anzeigen und Verwalten der wiederherstellbaren gelöschten Office 365-Gruppen](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Einmaliges Anmelden steht jetzt für Azure AD-SAML-gesicherte lokale Apps über den Anwendungsproxy zur Verfügung (Public Preview).

**Typ:** Neues Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Zugriffssteuerung

Sie können nun eine Benutzeroberfläche für einmaliges Anmelden (SSO) für lokale, SAML-authentifizierte Apps zusammen mit Remotezugriff auf diese Apps über den Anwendungsproxy bereitstellen. Weitere Informationen zum Einrichten von SAML-SSO mit Ihren lokalen Apps finden Sie unter [SAML-SSO (einmaliges Anmelden) für lokale Anwendungen mit dem Anwendungsproxy (Vorschauversion)](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Client-Apps in Anforderungsschleifen werden unterbrochen, um Zuverlässigkeit und Benutzerfreundlichkeit zu verbessern.

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Client-Apps können in einem kurzen Zeitraum Hunderte der gleichen Anmeldeanforderungen falsch ausgeben. Alle diese Anforderungen, ob erfolgreich oder nicht, tragen zu einer schlechten Benutzererfahrung und erhöhten Workloads für den IDP bei, was zu einer höheren Latenz für alle Benutzer und einer geringeren Verfügbarkeit des IDP führt.

Dieses Update sendet eine `invalid_grant`-Fehlermeldung: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` an Client-Apps, die mehrmals über den Rahmen des normalen Betriebs hinausgehend in einem kurzen Zeitraum doppelte Anforderungen ausgeben. Client-Apps, bei denen dieses Problem auftritt, sollten eine interaktive Eingabeaufforderung anzeigen, die den Benutzer zur erneuten Anmeldung auffordert. Weitere Informationen zu dieser Änderung und zur Korrektur Ihrer App bei Auftreten dieses Fehlers finden Sie unter [In der Schleife befindliche Clients werden unterbrochen](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Neue Benutzeroberfläche für Überwachungsprotokolle jetzt verfügbar

**Typ:** Geändertes Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Wir haben eine neue Azure AD-**Überwachungsprotokolle**-Seite erstellt, um die Lesbarkeit zu verbessern und die Art, in der Sie Ihre Informationen suchen. Um die neue **Überwachungsprotokolle**-Seite anzuzeigen, wählen Sie **Überwachungsprotokolle** im Abschnitt **Aktivität** von Azure AD aus.

![Neue Seite „Überwachungsprotokolle“ mit Beispielinformationen](media/whats-new/audit-logs-page.png)

Weitere Informationen über die neue Seite **Überwachungsprotokolle** finden Sie unter [Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal](../reports-monitoring/concept-audit-logs.md#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Neue Warnungen und Anleitungen, um das versehentliche Aussperren des Administrators aufgrund falsch konfigurierter Richtlinien für den bedingten Zugriff zu verhindern

**Typ:** Geändertes Feature-**Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Um zu verhindern, dass Administratoren sich versehentlich selbst von ihren eigenen Mandanten aus durch falsch konfigurierte Richtlinien für bedingten Zugriff aussperren, haben wir neue Warnungen und aktualisierte Anleitungen im Azure-Portal erstellt. Weitere Informationen zu den neuen Anleitungen finden Sie im Artikel [Was sind Dienstabhängigkeiten beim bedingten Azure Active Directory-Zugriff?](../conditional-access/service-dependencies.md).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Verbesserte Benutzeroberfläche zur Anzeige von Nutzungsbedingungen für Endbenutzer auf mobilen Geräten

**Typ:** Geändertes Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Governance

Wir haben unsere vorhandene Benutzeroberfläche zur Anzeige von Nutzungsbedingungen aktualisiert, um Ihnen auf einem mobilen Gerät zu erleichtern, Nutzungsbedingungen zu überprüfen und ihnen zuzustimmen. Sie können jetzt vergrößern und verkleinern, zurückgehen, Informationen herunterladen und Links auswählen. Weitere Informationen zu den aktualisierten Nutzungsbedingungen finden Sie unter [Anzeige von Nutzungsbedingungen für Benutzer](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Neue Benutzeroberfläche zum Herunterladen von Azure AD-Aktivitätsprotokollen verfügbar

**Typ:** Geändertes Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Sie können große Mengen von Aktivitätsprotokollen jetzt direkt über das Azure-Portal herunterladen. Dieses Update ermöglicht Ihnen Folgendes:

- Herunterladen von bis zu 250.000 Zeilen.

- Erhalten einer Benachrichtigung bei Abschluss des Downloads.

- Anpassen Ihres Dateinamens.

- Bestimmen Ihres Ausgabeformats, entweder JSON oder CSV.

Weitere Informationen zu diesem Feature finden Sie unter [Schnellstart: Herunterladen eines Überwachungsberichts über das Azure-Portal](../reports-monitoring/quickstart-download-audit-report.md).

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Wichtige Änderung: Updates für die Bedingungsauswertung bei Exchange ActiveSync (EAS)

**Typ:** Plan für Änderung der **Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Zugriffssteuerung

Wir aktualisieren momentan die Art, in der Exchange ActiveSync (EAS) die folgenden Bedingungen auswertet:

- Standort des Benutzers basierend auf Land, Region oder IP-Adresse

- Anmelderisiko

- Geräteplattform

Wenn Sie diese Bedingungen zuvor in Ihren Richtlinien für bedingten Zugriff verwendet haben, denken Sie daran, dass das Bedingungsverhalten sich ändern könnte. Wenn Sie z.B. zuvor die Benutzerstandortbedingung in einer Richtlinie verwendet haben, stellen Sie möglicherweise fest, dass die Richtlinie jetzt je nach Standort des Benutzers übersprungen wird.

---

## <a name="february-2019"></a>Februar 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurierbare Azure AD SAML-Tokenverschlüsselung (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Sie können jetzt jede unterstützte SAML-App so konfigurieren, dass sie verschlüsselte SAML-Token erhält. Bei Konfiguration und Verwendung mit einer App verschlüsselt Azure AD die ausgegebenen SAML-Assertionen mit einem öffentlichen Schlüssel, der von einem in Azure AD gespeicherten Zertifikat bereitgestellt wird.

Weitere Informationen zum Konfigurieren Ihrer SAML-Tokenverschlüsselung finden Sie unter [Konfigurieren der Azure AD-SAML-Tokenverschlüsselung](../manage-apps/howto-saml-token-encryption.md).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Erstellen einer Zugriffsüberprüfung für Gruppen oder Apps mit Azure AD-Zugriffsüberprüfungen

**Typ:** Neues Feature-**Dienstkategorie:** Zugriffsüberprüfungen-**Produktfunktion:** Governance

Sie können jetzt mehrere Gruppen oder Apps in eine einzige Azure AD-Zugriffsüberprüfung für die Gruppenmitgliedschaft oder App-Zuweisung einschließen. Zugriffsüberprüfungen mit mehreren Gruppen oder Apps werden mit denselben Einstellungen eingerichtet, und alle einbezogenen Prüfer werden gleichzeitig benachrichtigt.

Weitere Informationen zum Erstellen einer Zugriffsüberprüfung mithilfe von Azure AD-Zugriffsüberprüfungen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppenmitgliedern oder dem Anwendungszugriff mit Azure AD](../governance/create-access-review.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Februar 2019

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Februar 2019 haben wir diese 27 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md), [MindTickle](../saas-apps/mindtickle-tutorial.md), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](../saas-apps/airstack-tutorial.md), [Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [IDrive](../saas-apps/idrive-tutorial.md), [Skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md), [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md), Permission Click, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile](../saas-apps/smartfile-tutorial.md), [Pexip](../saas-apps/pexip-tutorial.md), [Stormboard](../saas-apps/stormboard-tutorial.md), [Seismic](../saas-apps/seismic-tutorial.md), [Share A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](../saas-apps/bugsnag-tutorial.md), [webMethods Integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [Knowledge Anywhere LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md), [OU Campus](../saas-apps/ou-campus-tutorial.md), [Periscope Data](../saas-apps/periscope-data-tutorial.md), [Netop Portal](../saas-apps/netop-portal-tutorial.md), [smartvid.io](../saas-apps/smartvid.io-tutorial.md), [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-tutorial.md), [ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Erweiterte kombinierte MFA-/SSPR-Registrierung

**Typ:** Geändertes Feature-**Dienstkategorie:** Self-Service-Kennwortzurücksetzungs-**Produktfunktion:** Benutzerauthentifizierung

Als Reaktion auf Kundenfeedback haben wir die Vorschauoberfläche für die kombinierte MFA-/SSPR-Registrierung verbessert, damit Ihre Benutzer ihre Sicherheitsinformationen für MFA und SSPR schneller registrieren können.

**Gehen Sie wie folgt vor, um die verbesserte Oberfläche für Ihre Benutzer sofort zu aktivieren:**

1. Melden Sie sich als globaler Administrator oder Benutzeradministrator beim Azure-Portal an, und wechseln Sie zu **Azure Active Directory > Benutzereinstellungen > Einstellungen für Zugriffspanel-Vorschaufeatures verwalten**.

2. Wählen Sie unter der Option **Benutzer können Vorschaufunktionen zum Registrieren und Verwalten von Sicherheitsinformationen verwenden (Aktualisierung)** aus, ob Sie die Features für eine **ausgewählte Gruppe von Benutzern** oder für **alle Benutzer** aktivieren möchten.

Im Laufe der nächsten Wochen entfernen wir die Möglichkeit, die alte Vorschauoberfläche für die kombinierte MFA-/SSPR-Registrierung für Mandanten zu aktivieren, für die sie noch nicht aktiviert wurde.

**Gehen Sie wie folgt vor, um festzustellen, ob das Steuerelement für Ihren Mandanten entfernt wird:**

1. Melden Sie sich als globaler Administrator oder Benutzeradministrator beim Azure-Portal an, und wechseln Sie zu **Azure Active Directory > Benutzereinstellungen > Einstellungen für Zugriffspanel-Vorschaufeatures verwalten**.

2. Wenn die Option **Benutzer können Vorschaufunktionen zum Registrieren und Verwalten von Sicherheitsinformationen verwenden** auf **Kein** festgelegt ist, wird die Option von Ihrem Mandanten entfernt.

Unabhängig davon, ob Sie zuvor die alte Vorschauoberfläche für die kombinierte MFA-/SSPR-Registrierung für Benutzer aktiviert haben, wird die alte Benutzeroberfläche zu einem späteren Zeitpunkt deaktiviert. Aus diesem Grund raten wir Ihnen, so schnell wie möglich zur neuen erweiterten Oberfläche zu wechseln.

Weitere Informationen zur erweiterte Registrierungsoberfläche finden Sie unter [Cool enhancements to the Azure AD combined MFA and password reset registration experience](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271) (Verbesserungen an der Oberfläche für die kombinierte Oberfläche für die MFA-Registrierung und Kennwortzurücksetzung).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Aktualisierte Richtlinienverwaltungsoberfläche für Benutzerflows

**Typ:** Geändertes Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Wir haben den Vorgang zur Richtlinienerstellung und -verwaltung für Benutzerflows (zuvor als integrierte Richtlinien bezeichnet) vereinfacht. Diese neue Oberfläche gilt jetzt standardmäßig für all Ihre Azure AD-Mandanten.

Über die Symbole „Lächeln“ und „Stirnrunzeln“ im Bereich **Feedback senden** im oberen Bereich des Portals können Sie zusätzliches Feedback und Vorschläge angeben.

Weitere Informationen zur neuen Richtlinienverwaltungsoberfläche finden Sie im Blog [Azure AD B2C now has JavaScript customization and many more new features](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) (Azure AAD B2C bietet jetzt JavaScript-Anpassung und viele weitere Features).

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Auswahl bestimmter von Azure AD B2C bereitgestellter Seitenelementversionen

**Typ:** Neues Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Jetzt können Sie eine bestimmte Version der von Azure AD B2C bereitgestellten Seitenelemente auswählen. Wenn Sie eine bestimmte Version auswählen, können Sie Ihre Updates testen, bevor sie auf einer Seite angezeigt werden, und Sie können vorhersagbares Verhalten erzielen. Darüber hinaus können Sie jetzt auswählen, dass bestimmte Seitenversionen erzwungen werden, um JavaScript-Anpassungen zuzulassen. Um dieses Feature zu aktivieren, wechseln Sie in Ihren Benutzerflows zur Seite **Eigenschaften**.

Weitere Informationen zur Auswahl bestimmter Versionen von Seitenelementen finden Sie im Blog [Azure AD B2C now has JavaScript customization and many more new features](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) (Azure AAD B2C bietet jetzt JavaScript-Anpassung und viele weitere Features).

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurierbare Endbenutzerkennwortanforderungen für B2C (GA)

**Typ:** Neues Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Jetzt können Sie die Kennwortkomplexität Ihrer Organisation für Ihre Endbenutzer einrichten, statt die native Azure AD-Kennwortrichtlinie verwenden zu müssen. Auf dem Blatt **Eigenschaften** Ihrer Benutzerflows (zuvor als integrierte Richtlinien bezeichnet) können Sie die Kennwortkomplexität **Einfach** oder **Stark** auswählen oder **benutzerdefinierte** Anforderungen erstellen.

Weitere Informationen zur Konfiguration von Anforderungen für die Kennwortkomplexität finden Sie unter [Konfigurieren der Komplexitätsanforderungen für Kennwörter in Azure Active Directory B2C](../../active-directory-b2c/user-flow-password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Neue Standardvorlagen für benutzerdefinierte mit der Marke versehene Authentifizierungsoberflächen

**Typ:** Neues Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Mit unseren neuen Standardvorlagen auf dem Blatt **Seitenlayouts** Ihrer Benutzerflows (zuvor als integrierte Richtlinien bezeichnet) können Sie einen benutzerdefinierten mit Ihrer Marke versehenen Authentifizierungsvorgang für Ihre Benutzer erstellen.

Weitere Informationen zur Verwendung der Vorlagen finden Sie unter [Azure AD B2C now has JavaScript customization and many more new features](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) (Azure AAD B2C bietet jetzt JavaScript-Anpassung und viele weitere Features).

---

## <a name="january-2019"></a>Januar 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-Zusammenarbeit über Authentifizierung mit Einmalkennung (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** B2B-**Produktfunktion:** B2B/B2C

Für B2B-Gastbenutzer, die sich auf andere Weise (z.B. über Azure AD, ein Microsoft-Konto (MSA) oder den Verbund mit Google) nicht authentifizieren können, haben wir die Authentifizierung mit Einmalkennung eingeführt. Diese neue Authentifizierungsmethode bedeutet, dass Gastbenutzer kein neues Microsoft-Konto erstellen müssen. Stattdessen kann ein Gastbenutzer beim Einlösen einer Einladung oder beim Zugriff auf eine freigegebene Ressource einen temporären Code anfordern, der an eine E-Mail-Adresse gesendet wird. Mit diesem temporären Code kann der Gastbenutzer seine Anmeldung fortsetzen.

Weitere Informationen finden Sie unter [Authentifizierung mit Einmalkennung per E-Mail (Vorschauversion)](../external-identities/one-time-passcode.md) und im Blogartikel [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (Nahtlose Freigabe und Zusammenarbeit in Azure AD für alle Benutzer mit beliebigen Konten).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Neue Cookieeinstellungen für den Azure AD-Anwendungsproxy

**Typ:** Neues Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Zugriffssteuerung

Wir haben drei neue Cookieeinstellungen eingeführt, die für Ihre Apps verfügbar sind, die über den Anwendungsproxy veröffentlicht werden:

- **Nur-HTTP-Cookie verwenden.** Legt das **HTTPOnly**-Flag für Ihre Anwendungsproxyzugriffs- und Sitzungscookies fest. Das Aktivieren dieser Einstellung bietet zusätzliche Sicherheitsvorteile, z.B. verhindert es das Kopieren oder Ändern von Cookies durch clientseitige Skripts. Sie sollten dieses Flag wg. der zusätzlichen Vorteile aktivieren (wählen Sie **Ja** aus).

- **Sicheres Cookie verwenden.** Legt das **Secure**-Flag für Ihre Anwendungsproxyzugriffs- und Sitzungscookies fest. Das Aktivieren dieser Einstellung bietet zusätzliche Sicherheitsvorteile, indem sichergestellt wird, dass Cookies nur über sichere TLS-Kanäle wie z.B. HTTPS übertragen werden. Sie sollten dieses Flag wg. der zusätzlichen Vorteile aktivieren (wählen Sie **Ja** aus).

- **Beständiges Cookie verwenden.** Verhindert, dass Zugriffcookies ablaufen, wenn der Webbrowser geschlossen wird. Diese Cookies existieren für die Lebensdauer des Zugriffstokens. Allerdings werden die Cookies zurückgesetzt, wenn der Ablaufzeitpunkt erreicht ist, oder wenn der Benutzer das Cookie manuell löscht. Sie sollten die Standardeinstellung **Nein** beibehalten und die Einstellung nur für ältere Apps aktivieren, die keine Cookies zwischen Prozessen freigeben.

Weitere Informationen zu den neuen Cookies finden Sie unter [Cookieeinstellungen für den Zugriff auf lokale Anwendungen in Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Januar 2019

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Juli 2019 haben wir diese 35 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [Talent Palette](../saas-apps/talent-palette-tutorial.md), [Infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco Umbrella](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md), [Expiration Reminder](../saas-apps/expiration-reminder-tutorial.md), [InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md), [CorpTax](../saas-apps/corptax-tutorial.md), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service PICCO](../saas-apps/cloud-service-picco-tutorial.md), [AuditBoard](../saas-apps/auditboard-tutorial.md), [iProva](../saas-apps/iprova-tutorial.md), [Workable](../saas-apps/workable-tutorial.md), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Coralogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [ARES for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](../saas-apps/idid-manager-tutorial.md), [HighGear](../saas-apps/highgear-tutorial.md), [Visitly](../saas-apps/visitly-tutorial.md), [Korn Ferry ALP](../saas-apps/korn-ferry-alp-tutorial.md), [Acadia](../saas-apps/acadia-tutorial.md), [Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Neue Verbesserungen an Azure AD Identity Protection (Public Preview)

**Typ:** Geändertes Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Wir freuen uns, bekanntgeben zu können, dass wir dem Public Preview-Angebot von Azure AD Identity Protection die folgenden Erweiterungen hinzugefügt haben:

- Eine aktualisierte und stärker integrierte Benutzeroberfläche

- Zusätzliche APIs

- Verbesserte Risikobewertung durch Machine Learning

- Produktweite Ausrichtung bezüglich riskanter Benutzer und riskanter Anmeldungen

Weitere Informationen über die Erweiterungen finden Sie unter [Was ist Azure Active Directory Identity Protection? (aktualisiert)](../identity-protection/overview-identity-protection.md), um mehr zu erfahren und Ihre Meinung mittels der Eingabeaufforderungen im Produkt mitzuteilen.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Neue App-Sperrfunktion für die Microsoft Authenticator-App auf iOS- und Android-Geräten

**Typ:** Neues Feature-**Dienstkategorie:** Microsoft Authenticator-App-**Produktfunktion:** Identitätssicherheit und -schutz

Um die Sicherheit Ihrer Einmalpasscodes, App-Informationen und App-Einstellungen zu erhöhen, können Sie in der Microsoft Authenticator-App das Feature „App-Sperre“ aktivieren. Das Aktivieren der App-Sperre bedeutet, dass Sie bei jedem Öffnen der Microsoft Authenticator-App aufgefordert werden, sich mit Ihrer PIN oder Ihren biometrischen Daten zu authentifizieren.

Weitere Informationen finden Sie unter [Microsoft Authenticator-App – häufig gestellte Fragen](../user-help/user-help-auth-app-faq.md).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Erweiterte Exportfunktionen für Azure AD Privileged Identity Management (PIM)

**Typ:** Neues Feature-**Dienstkategorie:** Privileged Identity Management-**Produktfunktion:** Privileged Identity Management

Privileged Identity Management-Administratoren (PIM) können jetzt alle aktiven und geeigneten Rollenzuweisungen für eine bestimmte Ressource exportieren, die Rollenzuweisungen für alle untergeordneten Ressourcen eingeschlossen. In der Vergangenheit war es für Administratoren nicht ganz einfach, eine vollständige Liste mit den Rollenzuweisungen für ein Abonnement zu erhalten, und die Rollenzuweisungen mussten für jede spezifische Ressource exportiert werden.

Weitere Informationen finden Sie unter [Anzeigen des Aktivitäts- und Überwachungsverlaufs für Azure-Ressourcenrollen in PIM](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="novemberdecember-2018"></a>November/Dezember 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Aus dem Synchronisierungsbereich entfernte Benutzer werden nicht mehr auf reine Cloudkonten umgestellt

**Typ:** Feste **Dienstkategorie:** Benutzerverwaltungs-**Produktfunktion:** Verzeichnis

>[!Important]
>Wir haben Ihre Verärgerung bezüglich dieser Fehlerbehebung zur Kenntnis genommen und verstanden. Aus diesem Grund haben wir die Änderung rückgängig gemacht, bis wir eine Fehlerbehebung anbieten können, die in Ihrer Organisation leichter implementiert werden kann.

Wir haben einen Fehler behoben, bei dem das DirSyncEnabled-Flag eines Benutzers fälschlicherweise in **False** geändert wurde, wenn das Objekt von Active Directory Domain Services (AD DS) aus dem Synchronisierungsbereich ausgeschlossen und dann im darauf folgenden Synchronisierungszyklus in Azure AD in den Papierkorb verschoben wurde. Wenn der Benutzer aus dem Synchronisierungsbereich ausgeschlossen und anschließend aus dem Azure AD-Papierkorb wiederhergestellt wird, bleibt das Benutzerkonto infolge dieser Korrektur wie erwartet von der lokalen AD-Instanz synchronisiert und kann nicht in der Cloud verwaltet werden, weil die Autoritätsquelle (Source of Authority, SoA) als lokale AD-Instanz erhalten bleibt.

Vor dieser Korrektur bestand ein Problem, wenn das DirSyncEnabled-Flag in „False“ geändert wurde. Es hat den falschen Eindruck vermittelt, dass diese Konten in reine Cloudobjekte umgewandelt wurden und die Konten in der Cloud verwaltet werden könnten. Die Konten behielten jedoch ihre SoA und alle synchronisierten-Eigenschaften (Schattenattribute) aus der lokalen AD-Instanz weiterhin als lokal bei. Dieser Zustand verursachte mehrere Probleme in Azure AD und anderen Cloudworkloads (wie Exchange Online), die davon ausgingen, diese Konten als von AD synchronisiert zu behandeln, die sich aber jetzt wie reine Cloudkonten verhielten.

Die einzige Möglichkeit, ein von AD synchronisiertes Konto tatsächlich in ein reines Cloudkonto umzuwandeln, besteht derzeit darin, DirSync auf Mandantenebene zu deaktivieren, wodurch ein Back-End-Vorgang zum Übertragen der SoA ausgelöst wird. Diese Art der Änderung der SoA erfordert (ist aber nicht beschränkt auf) das Bereinigen aller entsprechenden lokalen Attribute (wie LastDirSyncTime und Schattenattribute) und das Senden eines Signals an andere Cloudworkloads, das entsprechende Objekt ebenfalls in ein reines Cloudkonto umzuwandeln.

Durch diese Korrektur werden daher direkte Updates für das ImmutableID-Attribut eines von AD synchronisierten Benutzers verhindert, die in der Vergangenheit in einigen Szenarien erforderlich waren. Entwurfsbedingt soll die ImmutableID eines Objekts in Azure AD (wie der Name schon sagt) unveränderlich sein. Zur Unterstützung solcher Szenarien wurden neue Features in Azure AD Connect Health und in den Azure AD Connect-Synchronisierungsclient implementiert:

- **Umfangreiche ImmutableID-Updates für viele Benutzer in einem mehrstufigen Ansatz**

  Sie müssen beispielsweise eine langwierige gesamtstrukturübergreifende AD DS-Migration durchführen. Lösung: Verwenden Sie Azure AD Connect zum **Konfigurieren des Quellankers**, und kopieren Sie beim Migrieren des Benutzers die vorhandenen ImmutableID-Werte aus Azure AD in das „ms-DS-ConsistencyGuid“-Attribut der neuen Gesamtstruktur des lokalen AD DS-Benutzers. Weitere Informationen finden Sie unter [Verwendung von „ms-DS-ConsistencyGuid“ in „sourceAnchor“](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

- **Umfangreiche ImmutableID-Updates für viele Benutzer in einem Durchgang**

  Ihnen unterläuft beispielsweise bei der Implementierung von Azure AD Connect ein Fehler, und nun müssen Sie das Attribut „SourceAnchor“ ändern. Lösung: Deaktivieren Sie DirSync auf Mandantenebene, und löschen Sie alle ungültigen ImmutableID-Werte. Weitere Informationen finden Sie unter [Deaktivieren der Verzeichnissynchronisierung für Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Erneutes Abgleichen (Zuordnen) eines lokalen Benutzers mit einem vorhandenen Benutzer in Azure AD** Beispiel: Ein in AD DS neu erstellter Benutzer generiert ein Duplikat in einem Azure AD-Konto statt es einem vorhandenen Azure AD-Konto (verwaisten Objekt) neu zuzuordnen. Lösung: Verwenden Sie Azure AD Connect Health im Azure-Portal, um den Quellanker/die ImmutableID neu zuzuordnen. Weitere Informationen finden Sie unter [Szenario: Verwaistes Objekt](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Breaking Change: Aktualisierungen des Überwachungs- und Anmeldeprotokollschemas über Azure Monitor

**Typ:** Geändertes Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Wir veröffentlichen derzeit die Überwachungs- und Anmeldeprotokollstreams über Azure Monitor, sodass Sie die Protokolldateien nahtlos in Ihre SIEM-Tools oder in Log Analytics integrieren können. Basierend auf Ihrem Feedback und in Vorbereitung auf die Ankündigung der allgemeinen Verfügbarkeit dieses Features nehmen wir an unserem Schema die folgenden Änderungen vor. Diese Schemaänderungen und die zugehörigen Dokumentationsaktualisierungen erfolgen bis zur ersten Januarwoche.

#### <a name="new-fields-in-the-audit-schema"></a>Neue Felder im Überwachungsschema
Wir fügen ein neues Feld **Vorgangstyp** zum Angeben des für die Ressource ausgeführten Vorgangstyps hinzu. Beispiele hierfür sind **Hinzufügen**, **Aktualisieren** oder **Löschen**.

#### <a name="changed-fields-in-the-audit-schema"></a>Geänderte Felder im Überwachungsschema
Im Überwachungsschema werden die folgenden Felder geändert:

|Feldname|Änderung|Alte Werte|Neue Werte|
|----------|------------|----------|----------|
|Category|Das war das Feld **Dienstname**. Jetzt heißt das Feld **Überwachungskategorien**. **Dienstname** wurde in das Feld **loggedByService** umbenannt.|<ul><li>Kontobereitstellung</li><li>Kernverzeichnis</li><li>Self-Service-Kennwortzurücksetzung</li></ul>|<ul><li>Benutzerverwaltung</li><li>Gruppenverwaltung</li><li>App-Verwaltung</li></ul>|
|targetResources|Enthält **TargetResourceType** auf der obersten Ebene.|&nbsp;|<ul><li>Richtlinie</li><li>App</li><li>Benutzer</li><li>Group</li></ul>|
|loggedByService|Gibt den Namen des Diensts an, der das Überwachungsprotokoll generiert hat.|Null|<ul><li>Kontobereitstellung</li><li>Kernverzeichnis</li><li>Self-Service-Kennwortzurücksetzung</li></ul>|
|Ergebnis|Stellt das Ergebnis der Überwachungsprotokolle bereit. Früher wurde das als Auflistung angegeben, aber jetzt wird der tatsächliche Wert angezeigt.|<ul><li>0</li><li>1</li></ul>|<ul><li>Erfolg</li><li>Fehler</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Geänderte Felder im Anmeldeschema
Im Anmeldeschema werden die folgenden Felder geändert:

|Feldname|Änderung|Alte Werte|Neue Werte|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Das war das Feld **conditionalaccessPolicies**. Jetzt heißt das Feld **appliedConditionalAccessPolicies**.|Keine Änderung|Keine Änderung|
|conditionalAccessStatus|Stellt das Ergebnis des Status der Richtlinie für bedingten Zugriff bei der Anmeldung bereit. Früher wurde das als Auflistung angegeben, aber jetzt wird der tatsächliche Wert angezeigt.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Erfolg</li><li>Fehler</li><li>Nicht angewendet</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: Ergebnis|Stellt das Ergebnis des Status der einzelnen Richtlinien für bedingten Zugriff bei der Anmeldung bereit. Früher wurde das als Auflistung angegeben, aber jetzt wird der tatsächliche Wert angezeigt.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Erfolg</li><li>Fehler</li><li>Nicht angewendet</li><li>Disabled</li></ul>|

Weitere Informationen zum Schema finden Sie unter [Interpretieren des Azure AD-Überwachungsprotokollschemas in Azure Monitor (Vorschauversion)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Identity Protection-Verbesserungen am überwachten Machine Learning-Modell und am Risikobewertungsmodul

**Typ:** Geändertes Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Risikobewertungen

Verbesserungen am Identity Protection-bezogenen Benutzer- und Anmelderisikobewertungsmodul können zu einer besseren Genauigkeit und Abdeckung des Benutzerrisikos beitragen. Administratoren werden möglicherweise feststellen, dass die Benutzerrisikostufe nicht mehr direkt mit der Risikostufe bestimmter Erkennungen verknüpft ist und sich die Anzahl und Stufe von riskanten Anmeldeereignissen erhöht hat.

Risikoerkennungen werden jetzt vom überwachten Machine Learning-Modell ausgewertet, das das Benutzerrisiko mit zusätzlichen Features der Anmeldungen des Benutzers und einem Muster von Erkennungen berechnet. Auf Grundlage dieses Modells werden dem Administrator möglicherweise Benutzer mit hohen Risikobewertungen angezeigt, obwohl die mit diesem Benutzer verknüpften Erkennungen ein geringes oder mittleres Risiko darstellen.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratoren können ihr eigenes Kennwort mithilfe der Microsoft Authenticator-App zurücksetzen (öffentliche Vorschau)

**Typ:** Geändertes Feature-**Dienstkategorie:** Self-Service-Kennwortzurücksetzungs-**Produktfunktion:** Benutzerauthentifizierung

Azure AD-Administratoren können jetzt ihr eigenes Kennwort mithilfe von Benachrichtigungen der Microsoft Authenticator-App oder mit Code aus einer mobilen Authentifikator-App oder einem Hardwaretoken zurücksetzen. Administratoren können ihr eigenes Kennwort jetzt mit zwei der folgenden Methoden zurücksetzen:

- Benachrichtigung der Microsoft Authenticator-App

- Code aus einer anderen mobilen Authentifikator-App/aus einem Hardwaretoken

- Email

- Telefonanruf

- Textnachricht

Weitere Informationen zur Verwendung der Microsoft Authenticator-App zum Zurücksetzen von Kennwörtern finden Sie unter [Funktionsweise der Self-Service-Kennwortzurücksetzung in Azure AD – mobile App und SSPR (Vorschau)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr).

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Neue Azure AD-Rolle „Cloudgeräteadministrator“ (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Geräteregistrierung und -verwaltung-**Produktfunktion:** Zugriffssteuerung

Administratoren können Benutzern die neue Rolle „Cloudgeräteadministrator“ zuweisen, damit sie Aufgaben eines Cloudgeräteadministrators ausführen können. Benutzer, denen die Rolle „Cloudgeräteadministrator“ zugewiesen ist, können Geräte in Azure AD aktivieren, deaktivieren und löschen sowie Windows 10-BitLocker-Schlüssel (falls vorhanden) im Azure-Portal lesen.

Weitere Informationen zu Rollen und Berechtigungen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](../roles/permissions-reference.md).

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Verwalten Ihrer Geräte mit dem neuen Aktivitätszeitstempel in Azure AD (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Geräteregistrierung und -verwaltung-**Produktfunktion:** Lebenszyklusverwaltung für Geräte

Wir wissen, dass Sie im Laufe der Zeit die Geräte Ihrer Organisation in Azure AD aktualisieren und außer Betrieb nehmen müssen, um zu vermeiden, dass Ihre Umgebung veraltete Geräte enthält. Um Sie bei diesem Prozess zu unterstützen, werden Ihre Geräte in Azure AD jetzt mit einem neuen Aktivitätszeitstempel aktualisiert, sodass Sie den Gerätelebenszyklus einfacher verwalten können.

Weitere Informationen zum Abrufen und Verwenden dieses Zeitstempels finden Sie unter [Anleitung: Verwalten der veralteten Geräte in Azure AD](../devices/manage-stale-devices.md).

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratoren können erzwingen, dass Benutzer den Nutzungsbedingungen auf jedem Gerät zustimmen müssen

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Governance

Administratoren können jetzt die Option **Zustimmung der Benutzer auf jedem Gerät erforderlich** aktivieren, damit Ihre Benutzer Ihren Nutzungsbedingungen auf jedem Gerät zustimmen müssen, das sie in Ihrem Mandanten verwenden.

Weitere Informationen finden Sie unter [Nutzungsbedingungsfeature für Azure Active Directory im Abschnitt „Nutzungsbedingungen pro Gerät“](../conditional-access/terms-of-use.md#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratoren können festlegen, dass die Einwilligungen für Nutzungsbedingungen basierend auf einer Zeitplanserie ablaufen

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Governance


Administratoren können jetzt die Option **Ablauf für Einwilligungen** aktivieren, damit die Einwilligungen für Nutzungsbedingungen von allen Benutzern basierend auf der festgelegten Zeitplanserie ablaufen. Es kann ein jährlicher, halbjährlicher, vierteljährlicher oder monatlicher Zeitplan ausgewählt werden. Nach Ablauf der Nutzungsbedingungen müssen die Benutzer erneut zustimmen.

Weitere Informationen finden Sie unter [Nutzungsbedingungsfeature für Azure Active Directory im Abschnitt „Hinzufügen von Nutzungsbedingungen“](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratoren können festlegen, dass die Einwilligungen für Nutzungsbedingungen basierend auf dem Zeitplan der einzelnen Benutzer ablaufen

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Governance

Administratoren können jetzt einen Zeitraum angeben, in dem die Benutzer den Nutzungsbedingungen erneut zustimmen müssen. Administratoren können beispielsweise festlegen, dass die Benutzer den Nutzungsbedingungen alle 90 Tage erneut zustimmen müssen.

Weitere Informationen finden Sie unter [Nutzungsbedingungsfeature für Azure Active Directory im Abschnitt „Hinzufügen von Nutzungsbedingungen“](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Neue E-Mails von Azure AD Privileged Identity Management (PIM) für Azure Active Directory-Rollen

**Typ:** Neues Feature-**Dienstkategorie:** Privileged Identity Management-**Produktfunktion:** Privileged Identity Management

Kunden, die Azure AD Privileged Identity Management (PIM) verwenden, können jetzt eine wöchentliche Zusammenfassung per E-Mail mit den folgenden Informationen der letzten sieben Tage erhalten:

- Übersicht über die wichtigsten dauerhaften Zuweisungen berechtigter Rollen

- Anzahl von Benutzern, die Rollen aktivieren

- Anzahl von Benutzern, denen Rollen in PIM zugewiesen sind

- Anzahl von Benutzern, denen Rollen außerhalb von PIM zugewiesen sind

- Anzahl von Benutzern, denen eine Rolle in PIM dauerhaft zugewiesen wurde

Weitere Informationen zu PIM und den verfügbaren E-Mail-Benachrichtigungen finden Sie unter [E-Mail-Benachrichtigungen in PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Die gruppenbasierte Lizenzierung ist jetzt allgemein verfügbar

**Typ:** Geändertes Feature-**Dienstkategorie:** Andere **Produktfunktion:** Verzeichnis

Die gruppenbasierte Lizenzierung befindet sich nicht mehr in der öffentlichen Vorschauphase und ist jetzt allgemein verfügbar. Im Rahmen dieser allgemeinen Version haben wir dieses Feature skalierbarer gestaltet und Optionen hinzugefügt, mit denen Sie Zuweisungen der gruppenbasierten Lizenzierung für einen einzelnen Benutzer erneut verarbeiten und die gruppenbasierte Lizenzierung für Office 365 E3-/A3-Lizenzen verwenden können.

Weitere Informationen zur gruppenbasierten Lizenzierung finden Sie unter [Was ist die gruppenbasierte Lizenzierung in Azure Active Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – November 2018

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im November 2018 haben wir diese 26 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md), [Gra-Pe](../saas-apps/grape-tutorial.md), [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [Infinite Campus](../saas-apps/infinitecampus-tutorial.md), [Alaya](https://alayagood.com), [HeyBuddy](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [Drift](../saas-apps/drift-tutorial.md), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](../saas-apps/everbridge-tutorial.md), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound SSO](../saas-apps/allbound-sso-tutorial.md), [Plex Apps – Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps – UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [CRAFTS – Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD-Protokolle funktionieren jetzt mit Azure Log Analytics (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Wir freuen uns, Ihnen mitteilen zu können, dass Sie Ihre Azure AD-Protokolle jetzt an Azure Log Analytics weiterleiten können. Dieses Feature – das auf der Wunschliste unserer Kunden ganz oben stand – ermöglicht einen noch besseren Zugang zu Analysefunktionen für Business, Betrieb und Sicherheit und bietet eine Methode für die Überwachung Ihrer Infrastruktur. Weitere Informationen finden Sie im Blog [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) (Azure Active Directory-Aktivitätsprotokolle jetzt in Azure Log Analytics verfügbar).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Oktober 2018

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Oktober 2018 haben wir diese 14 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[My Award Points](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive](../saas-apps/myworkdrive-tutorial.md), [BorrowBox](../saas-apps/borrowbox-tutorial.md), Dialpad, [ON24 Virtual Environment](../saas-apps/on24-tutorial.md), [RingCentral](../saas-apps/ringcentral-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [Appraisd](../saas-apps/appraisd-tutorial.md), [Workspot Control](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services – E-Mail-Benachrichtigungen

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD Domain Services-**Produktfunktion:** Azure AD Domain Services

Azure AD Domain Services bietet Warnungen im Azure-Portal zu fehlerhaften Konfigurationen oder Problemen in Ihrer verwalteten Domäne. Diese Warnungen umfassen Schrittanleitungen, sodass Sie die Probleme beheben können, ohne sich an den Support wenden zu müssen.

Ab Oktober können Sie die Benachrichtigungseinstellungen für Ihre verwaltete Domäne anpassen, sodass beim Auftreten neuer Warnungen eine E-Mail an eine angegebene Gruppe von Personen gesendet wird. So müssen Sie nicht mehr dauernd das Portal auf Updates überprüfen.

Weitere Informationen finden Sie unter [Benachrichtigungseinstellungen in Azure AD Domain Services](../../active-directory-domain-services/notifications.md).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Das Azure AD-Portal unterstützt die Verwendung der ForceDelete-Domänen-API zum Löschen von benutzerdefinierten Domänen

**Typ:** Geändertes Feature-**Dienstkategorie:** Verzeichnisverwaltungs-**Produktfunktion:** Verzeichnis

Wir freuen uns, Ihnen mitteilen zu können, dass Sie jetzt die ForceDelete-Domänen-API verwenden können, um Ihre benutzerdefinierten Domänennamen zu löschen. Über die API werden Verweise wie Benutzer, Gruppen und Apps mit Ihrem benutzerdefinierten Domänennamen (contoso.com) asynchron wieder in den standardmäßigen Domänennamen (contoso.onmicrosoft.com) umbenannt.

Dank dieser Änderung können Sie Ihren benutzerdefinierten Domänennamen schneller löschen, wenn dieser von Ihrem Unternehmen nicht mehr verwendet wird oder wenn Sie den Domänennamen für eine andere Azure AD-Instanz verwenden möchten.

Weitere Informationen finden Sie unter [Löschen eines benutzerdefinierten Domänennamens](../enterprise-users/domains-manage.md#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>September 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Aktualisierte Berechtigungen der Administratorrolle für dynamische Gruppen

**Typ:** Feste **Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Wir haben ein Problem behoben, damit mit bestimmten Administratorrollen jetzt dynamische Mitgliedschaftsregeln erstellt und aktualisiert werden können, ohne dass der Benutzer der Besitzer der Gruppe sein müssen.

Die Rollen lauten:

- Globaler Administrator

- Intune-Administrator

- Benutzeradministrator

Weitere Informationen finden Sie unter [Erstellen einer dynamischen Gruppe und Überprüfen des Status](../enterprise-users/groups-create-rule.md).

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Vereinfachte Konfigurationseinstellungen für einmaliges Anmelden (SSO) bei einigen Drittanbieter-Apps

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Uns ist bewusst, dass die Einrichtung des einmaligen Anmeldens (SSO) für SaaS-Apps (Software-as-a-Service) aufgrund der speziellen Konfiguration von Apps eine Herausforderung darstellen kann. Wir haben eine vereinfachte Benutzeroberfläche für die Konfiguration erstellt, um die SSO-Konfigurationseinstellungen für die folgenden Drittanbieter-SaaS-Apps automatisch einzufügen:

- Zendesk

- ArcGis Online

- Jamf Pro

Sie können diese 1-Klick-Oberfläche verwenden, indem Sie für die App zu **Azure-Portal** > **SSO-Konfiguration** navigieren. Weitere Informationen finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md).

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Seite „Azure Active Directory – An welchem Ort befinden sich Ihre Daten?“

**Typ:** Neues Feature-**Dienstkategorie:** Andere **Produktfunktion:** GoLocal

Wählen Sie auf der Seite **Azure Active Directory – An welchem Ort befinden sich Ihre Daten?** die Region Ihres Unternehmens aus, um anzuzeigen, in welchem Azure-Rechenzentrum sich Ihre ruhenden Azure AD-Daten für alle Azure AD-Dienste befinden. Sie können die Informationen nach bestimmten Azure AD-Diensten für die Region Ihres Unternehmens filtern.

Informationen zum Zugriff auf dieses Feature und weitere Informationen finden Sie unter [Azure Active Directory – An welchem Ort befinden sich Ihre Daten?](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Neuer Bereitstellungsplan verfügbar für Zugriffsbereich „Meine Apps“

**Typ:** Neues Feature-**Dienstkategorie:** Meine Apps-**Produktfunktion:** SSO

Erkunden Sie den neuen Bereitstellungsplan, der für den Zugriffsbereich „Meine Apps“ verfügbar ist (https://aka.ms/deploymentplans).
Im Zugriffsbereich „Meine Apps“ können Benutzer an einem zentralen Ort nach ihren Apps suchen und darauf zugreifen. In diesem Portal können Benutzer auch Self-Service-Funktionen verwenden, z.B. das Anfordern des Zugriffs auf Apps und Gruppen oder das Verwalten des Zugriffs auf diese Ressourcen im Namen von anderen Personen.

Weitere Informationen finden Sie unter [Was ist das MyApps-Portal?](../user-help/my-apps-portal-end-user-access.md).

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Neue Registerkarte „Problembehandlung und Support“ auf der Seite „Anmeldungen“ im Azure-Portal

**Typ:** Neues Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Die neue Registerkarte **Problembehandlung und Support** auf der Seite **Anmeldungen** des Azure-Portals dient Administratoren und Engineers als Hilfe beim Behandeln von Problemen mit der Azure AD-Anmeldung. Diese neue Registerkarte enthält den Fehlercode, die Fehlermeldung und Empfehlungen zur Problemlösung (falls zutreffend), um die Behebung des Problems zu ermöglichen. Wenn das Problem nicht lösbar ist, können Sie auch eine neue Möglichkeit zum Erstellen eines Supporttickets nutzen, indem Sie die Option **In Zwischenablage kopieren** verwenden. Bei dieser Option werden die Felder **Anforderungs-ID** und **Datum (UTC)** für die Protokolldatei in Ihrem Supportticket ausgefüllt.

![Anmeldeprotokolle mit der neuen Registerkarte](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Verbesserte Unterstützung für benutzerdefinierte Erweiterungseigenschaften, die zum Erstellen von dynamischen Mitgliedschaftsregeln verwendet werden

**Typ:** Geändertes Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Mit diesem Update können Sie jetzt im Regel-Generator für dynamische Benutzergruppen auf den Link **Get custom extension properties** (Benutzerdefinierte Erweiterungseigenschaften abrufen) klicken, Ihre eindeutige App-ID eingeben und die vollständige Liste mit benutzerdefinierten Erweiterungseigenschaften erhalten, die Sie beim Erstellen einer dynamischen Mitgliedschaftsregeln für Benutzer verwenden. Diese Liste kann auch aktualisiert werden, um neue benutzerdefinierte Erweiterungseigenschaften für diese App zu erhalten.

Weitere Informationen zur Verwendung von benutzerdefinierten Erweiterungseigenschaften für dynamische Mitgliedschaftsregeln finden Sie unter [Erweiterungseigenschaften und benutzerdefinierte Erweiterungseigenschaften](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Neue genehmigte Client-Apps für den app-basierten bedingten Zugriff mit Azure AD

**Typ:** Plan für Änderung der **Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Die folgenden Apps wurden der Liste der [genehmigten Client-Apps](../conditional-access/concept-conditional-access-conditions.md#client-apps) hinzugefügt:

- Microsoft To-Do

- Microsoft Stream

Weitere Informationen finden Sie unter

- [App-basierter bedingter Zugriff mit Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Neue Unterstützung für Self-Service-Kennwortzurücksetzung über den Windows 7/8/8.1-Sperrbildschirm

**Typ:** Neues Feature-**Dienstkategorie:** SSPR-**Produktfunktion:** Benutzerauthentifizierung

Nachdem Sie dieses neue Feature eingerichtet haben, wird Ihren Benutzern ein Link zum Zurücksetzen Ihres Kennworts über den **Sperrbildschirm** eines Geräts mit Windows 7, Windows 8 oder Windows 8.1 angezeigt. Wenn Benutzer auf diesen Link klicken, werden sie durch den gleichen Ablauf zur Kennwortzurücksetzung wie im Webbrowser geleitet.

Weitere Informationen finden Sie unter [How to enable password reset from Windows 7, 8, and 8.1](../authentication/howto-sspr-windows.md) (Aktivieren der Kennwortzurücksetzung unter Windows 7, 8 und 8.1).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Änderungshinweis: Autorisierungscodes können nicht mehr wiederverwendet werden.

**Typ:** Plan für Änderung der **Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Ab dem 15. November 2018 akzeptiert Azure AD bereits zuvor verwendete Authentifizierungscodes für Apps nicht mehr. Diese Sicherheitsänderung trägt dazu bei, Azure AD an die OAuth-Spezifikation anzupassen. Sie wird auf v1- und v2-Endpunkten erzwungen.

Wenn Ihre App Autorisierungscodes zum Abrufen von Token für mehrere Ressourcen wiederverwendet, sollten Sie den Code für das Abrufen eines Aktualisierungstokens verwenden. Verwenden Sie dieses Aktualisierungstoken anschließend, um die zusätzlichen Token für andere Ressourcen abzurufen. Autorisierungscodes können nur einmal verwendet werden, Aktualisierungstoken hingegen können mehrere Male und für mehrere Ressourcen verwendet werden. Für jede App, die einen Authentifizierungscode im OAuth-Codefluss erneut verwenden möchte, wird ein Fehler vom Typ „invalid_grant“ zurückgegeben.

Informationen hierzu und zu anderen protokollbezogenen Änderungen finden Sie in der [vollständigen Liste mit den Neuerungen bei der Authentifizierung](../develop/reference-breaking-changes.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – September 2018

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im September 2018 haben wir diese 16 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Comeet Recruiting Software](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [Snowflake](../saas-apps/snowflake-tutorial.md), NavigoCloud, [Figma](../saas-apps/figma-tutorial.md), join.me, [ZephyrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback](../saas-apps/silverback-tutorial.md), Riverbed Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO for Azure, SurveyMonkey, [Convene](../saas-apps/convene-tutorial.md), [dmarcian](../saas-apps/dmarcian-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Unterstützung für zusätzliche Transformationsmethoden für Ansprüche

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Wir haben die neuen Transformationsmethoden „ToLower()“ und „ToUpper()“ eingeführt, die über die SAML-basierte Seite **SSO-Konfiguration** auf SAML-Token angewendet werden können.

Weitere Informationen finden Sie unter [Gewusst wie: Anpassen ausgestellter Ansprüche im SAML-Token für Unternehmensanwendungen in Azure AD](../develop/active-directory-saml-claims-customization.md).

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Aktualisierte SAML-basierte Benutzeroberfläche für die App-Konfiguration (Vorschauversion)

**Typ:** Geändertes Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Im Rahmen unserer aktualisierten SAML-basierten Benutzeroberfläche für die App-Konfiguration erhalten Sie Folgendes:

- Eine aktualisierte Benutzeroberfläche mit Schritt-für-Schritt-Anleitung für die Konfiguration Ihrer SAML-basierten Apps

- Bessere Informationen zu fehlenden oder fehlerhaften Teilen der Konfiguration

- Möglichkeit zum Hinzufügen von mehreren E-Mail-Adressen für Benachrichtigungen zum Zertifikatablauf

- Neue Transformationsmethoden „ToLower()“ und „ToUpper()“ für Ansprüche und mehr

- Möglichkeit zum Hochladen Ihres eigenen Tokensignaturzertifikats für Ihre Unternehmens-Apps

- Möglichkeit zum Festlegen des NameID-Formats für SAML-Apps und des NameID-Werts als Verzeichniserweiterungen

Klicken Sie zum Aktivieren dieser aktualisierten Ansicht oben auf der Seite **Einmaliges Anmelden** auf den Link **Neue Benutzeroberfläche ausprobieren**. Weitere Informationen finden Sie im [Tutorial: Konfigurieren des SAML-basierten einmaligen Anmeldens für eine Anwendung mit Azure Active Directory](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>August 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Änderungen an Azure Active Directory-IP-Adressbereichen

**Typ:** Plan für Änderung der **Dienstkategorie:** Andere **Produktfunktion:** Plattform

Es werden größere IP-Bereiche in Azure AD eingeführt. Wenn Sie Azure AD-IP-Adressbereiche für Ihre Firewalls, Router oder Netzwerksicherheitsgruppen konfiguriert haben, müssen Sie diese daher aktualisieren. Wir führen dieses Update durch, sodass Sie die Konfiguration der IP-Bereiche für die Firewalls, Router oder Netzwerksicherheitsgruppen nicht erneut ändern müssen, wenn in Azure AD neue Endpunkte hinzugefügt werden.

Der Netzwerkdatenverkehr wird in den nächsten zwei Monaten in diese neuen Bereiche verschoben. Um einen ununterbrochenen Dienst gewährleisten zu können, müssen Sie Ihren IP-Adressen vor dem 10. September 2018 die folgenden aktualisierten Werten hinzufügen:

- 20.190.128.0/18

- 40.126.0.0/18

Es wird dringend empfohlen, die alten IP-Adressbereiche erst zu entfernen, nachdem Ihr gesamter Netzwerkdatenverkehr in die neuen Bereiche verschoben wurde. Aktualisierungen zu dieser Verschiebung und Informationen dazu, wann die alten Bereiche entfernt werden können, finden Sie unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Änderungshinweis: Autorisierungscodes können nicht mehr wiederverwendet werden.

**Typ:** Plan für Änderung der **Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Ab dem 15. November 2018 akzeptiert Azure AD bereits zuvor verwendete Authentifizierungscodes für Apps nicht mehr. Diese Sicherheitsänderung trägt dazu bei, Azure AD an die OAuth-Spezifikation anzupassen. Sie wird auf v1- und v2-Endpunkten erzwungen.

Wenn Ihre App Autorisierungscodes zum Abrufen von Token für mehrere Ressourcen wiederverwendet, sollten Sie den Code für das Abrufen eines Aktualisierungstokens verwenden. Verwenden Sie dieses Aktualisierungstoken anschließend, um die zusätzlichen Token für andere Ressourcen abzurufen. Autorisierungscodes können nur einmal verwendet werden, Aktualisierungstoken hingegen können mehrere Male und für mehrere Ressourcen verwendet werden. Für jede App, die einen Authentifizierungscode im OAuth-Codefluss erneut verwenden möchte, wird ein Fehler vom Typ „invalid_grant“ zurückgegeben.

Informationen hierzu und zu anderen protokollbezogenen Änderungen finden Sie in der [vollständigen Liste mit den Neuerungen bei der Authentifizierung](../develop/reference-breaking-changes.md).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Zusammengeführte Sicherheitsinformationenverwaltung für Self-Service-Kennwortzurücksetzung (SSPR) und Multi-Factor Authentication (MFA)

**Typ:** Neues Feature-**Dienstkategorie:** SSPR-**Produktfunktion:** Benutzerauthentifizierung

Dieses neue Feature hilft Benutzern bei der Verwaltung ihrer Sicherheitsinformationen (z.B. Telefonnummer, mobile App usw.) für SSPR und MFA an einem zentralen Ort und mit einer einheitlichen Benutzeroberfläche, wofür früher zwei verschiedenen Umgebungen notwendig waren.

Diese zusammengeführte Benutzeroberfläche funktioniert auch für Personen, die entweder SSPR oder MFA verwenden. Wenn darüber hinaus Ihre Organisation die MFA- oder SSPR-Registrierung nicht erzwingt, können sich Personen trotzdem mit beliebigen Verfahren unter Verwendung von MFA- oder SSPR-Sicherheitsinformationen anmelden, sofern diese von Ihrer Organisation im Meine Apps-Portal zugelassen wurden.

Dies ist eine abonnierbare Public Preview. Administratoren können die neue Oberfläche (falls gewünscht) für eine ausgewählte Gruppe von Benutzern oder alle Benutzer in einem Mandanten aktivieren. Weitere Informationen zur zusammengeführten Benutzeroberfläche finden Sie im Blogbeitrag [Converged experience](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/) (Zusammengeführte Benutzeroberfläche).

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Neue Einstellung „HTTP-Only Cookies“ (Nur HTTP-Cookies) in Azure AD-Anwendungsproxy-Apps

**Typ:** Neues Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Zugriffssteuerung

Für Anwendungsproxy-Apps steht die neue Einstellung **HTTP-Only Cookies** (Nur HTTP-Cookies) zur Verfügung. Diese Einstellung bietet zusätzliche Sicherheit, da in HTTP-Antwortheader für den Zugriff auf den Anwendungsproxy und für Sitzungscookies das Flag „HTTPOnly“ eingefügt wird. Dieses beendet den Zugriff auf das Cookie von einem clientseitigen Skript und verhindert damit weitere Aktionen wie das Kopieren oder Ändern des Cookies. Auch wenn dieses Flag zuvor nicht verwendet wurde, wurden Ihre Cookies immer verschlüsselt und über eine TLS-Verbindung übertragen, um es vor ungewünschten Änderungen zu schützen.

Diese Einstellung nicht mit Apps kompatibel, die ActiveX-Steuerelemente verwenden, z.B. Remotedesktop. In diesen Fällen wird das Deaktivieren dieser Einstellung empfohlen.

Weitere Informationen zur Einstellung „HTTP-Only Cookies“ (Nur HTTP-Cookies) finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) für Azure-Ressourcen unterstützt Verwaltungsgruppen-Ressourcentypen

**Typ:** Neues Feature-**Dienstkategorie:** Privileged Identity Management-**Produktfunktion:** Privileged Identity Management

Die Einstellungen für die Just-In-Time-Aktivierung und -Zuweisung können jetzt auf Verwaltungsgruppen-Ressourcentypen angewandt werden, wie dies bereits für Abonnements, Ressourcengruppen und Ressourcen (z.B. virtuelle Computer, App Services und mehr) möglich war. Darüber hinaus kann jede Person mit einer Rolle mit Administratorzugriff auf eine Verwaltungsgruppe diese Ressource in PIM ermitteln und verwalten.

Weitere Informationen zu PIM und Azure-Ressourcen finden Sie unter [Ermitteln und Verwalten von Azure-Ressourcen mit Privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md).

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Anwendungszugriff (Vorschau) mit schnellerem Zugriff auf das Azure AD-Portal

**Typ:** Neues Feature-**Dienstkategorie:** Privileged Identity Management-**Produktfunktion:** Privileged Identity Management

Wenn Sie heute eine Rolle mithilfe von PIM aktivieren, kann die Anwendung der Berechtigungen mehr als 10 Minuten dauern. Wenn Sie den Anwendungszugriff, der sich derzeit in der Public Preview befindet, verwenden, können Administratoren direkt auf das Azure AD-Portal zugreifen, nachdem die Aktivierungsanforderung abgeschlossen ist.

Derzeit unterstützt der Anwendungszugriff nur das Azure AD-Portal und Azure-Ressourcen. Weitere Informationen zu PIM und dem Anwendungszugriff finden Sie unter [Was ist Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – August 2018

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im August 2018 haben wir diese 16 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Hornbill](../saas-apps/hornbill-tutorial.md), [Bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md), [Sauce Labs - Mobile and Web Testing](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), [Meta Networks Connector](../saas-apps/metanetworksconnector-tutorial.md), [Way We Do](../saas-apps/waywedo-tutorial.md), [Spotinst](../saas-apps/spotinst-tutorial.md), [ProMaster (by Inlogik)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), [Dossier](../saas-apps/dossier-tutorial.md), [N2F - Expense reports](../saas-apps/n2f-expensereports-tutorial.md), [Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md), [SafeConnect](../saas-apps/safeconnect-tutorial.md), [ZenQMS](../saas-apps/zenqms-tutorial.md), [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md).

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Native Tableau-Unterstützung jetzt im Azure AD-Anwendungsproxy verfügbar

**Typ:** Geändertes Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Zugriffssteuerung

Mit unserem Update von OpenID Connect auf das OAuth 2.0-Codeberechtigungsprotokoll für unser Vorauthentifizierungsprotokoll müssen Sie keine zusätzliche Konfiguration zum Verwenden von Tableau mit dem Anwendungsproxy mehr vornehmen. Diese Protokolländerung hilft auch dem Anwendungsproxy bei der besseren Unterstützung moderner Apps mit Nur-HTTP-Umleitungen, die häufig in JavaScript- und HTML-Tags unterstützt werden.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Neu: Unterstützung für das Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer in Azure Active Directory (Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** B2B-**Produktfunktion:** B2B/B2C

Durch das Einrichten eines Verbunds mit Google in Ihrer Organisation können Sie eingeladenen Gmail-Benutzern die Anmeldung bei Ihren freigegebenen Apps und Ressourcen mit ihren bestehenden Google-Konten ermöglichen, ohne dass sie ein persönliches Microsoft-Konto (MSA) oder Azure AD-Konto erstellen müssen.

Dies ist eine abonnierbare Public Preview. Weitere Informationen zum Google-Verbund finden Sie unter [Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer](../external-identities/google-federation.md).

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Verbesserungen an Azure Active Directory-E-Mail-Benachrichtigungen

**Typ:** Geändertes Feature-**Dienstkategorie:** Andere **Produktfunktion:** Identity Lifecycle Management

Azure Active Directory-E-Mails (Azure AD) weisen nun ein aktualisiertes Layout sowie Änderungen an der E-Mail-Adresse des Absenders und dem Anzeigenamen des Empfängers auf, wenn sie über die folgenden Dienste gesendet wurden:

- Azure AD-Zugriffsüberprüfungen
- Azure AD Connect Health
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Benachrichtigungen zu ablaufenden Zertifikaten von Unternehmen-Apps
- Benachrichtigungen zum Bereitstellungsdienst von Unternehmen-Apps

Die E-Mail-Benachrichtigungen werden von folgender E-Mail-Adresse und folgendem Anzeigenamen gesendet:

- E-Mail-Adresse: azure-noreply@microsoft.com
- Anzeigename: Microsoft Azure

Ein Beispiel für einige der neuen E-Mail-Layouts und weitere Informationen finden Sie unter [E-Mail-Benachrichtigungen in Azure AD PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-Aktivitätsprotokolle sind jetzt über Azure Monitor verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Die Azure AD-Aktivitätsprotokolle stehen jetzt in der Public Preview für Azure Monitor (die gesamte Plattform umfassender Überwachungsdienst von Azure) zur Verfügung. Azure Monitor bietet langfristige Aufbewahrung und nahtlose Integration, zusätzlich zu diesen Verbesserungen:

- Langfristige Aufbewahrung durch Routing Ihrer Protokolldateien an Ihr eigenes Azure-Speicherkonto.

- Nahtlose SIEM-Integration, ohne dass Sie benutzerdefinierte Skripts schreiben oder verwalten müssen.

- Nahtlose Integration in Ihre eigenen benutzerdefinierten Lösungen, Analysetools oder Lösungen zur Verwaltung von Incidents.

Weitere Informationen zu diesen neuen Funktionen finden Sie in unserem Blog [Azure AD activity logs in Azure Monitor diagnostics is now in public preview (Azure AD-Aktivitätsprotokolle in der Azure Monitor-Diagnose stehen jetzt in der Public Preview zur Verfügung)](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) und unserer Dokumentation [Azure AD-Aktivitätsprotokolle in Azure Monitor (Vorschauversion)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informationen für den bedingten Zugriff werden dem Azure AD-Anmeldebericht hinzugefügt

**Typ:** Neues Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Identitätssicherheit und -schutz

Dieses Update zeigt Ihnen, welche Richtlinien ausgewertet werden, wenn ein Benutzer sich mit dem Ergebnis der Richtlinie anmeldet. Darüber hinaus enthält der Bericht jetzt den Typ der vom Benutzer verwendeten Client-App, sodass Sie älteren Protokolldatenverkehr identifizieren können. Berichtseinträge können jetzt auch nach einer Korrelations-ID durchsucht werden, die in der an den Benutzer gerichteten Fehlermeldung enthalten ist und dazu verwendet werden kann, die entsprechende Anmeldungsanforderung zu identifizieren und dort ggf. eine Problembehandlung durchzuführen.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Anzeige von Legacyauthentifizierungen über Protokolle zur Anmeldeaktivität

**Typ:** Neues Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Dank der Einführung des Felds **Client-App** in den Protokollen zur Anmeldeaktivität können Kunden jetzt Benutzer anzeigen, die Authentifizierungen einer Vorgängerversion nutzen. Kunden können auf diese Informationen zugreifen, indem sie die Microsoft Graph-API für Anmeldungen oder die Protokolle zur Anmeldeaktivität im Azure AD-Portal verwenden. Hier können Sie das Steuerelement **Client-App** nutzen, um nach Legacyauthentifizierungen zu filtern. Weitere Informationen finden Sie in der Dokumentation.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Juli 2018

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Juli 2018 haben wir diese 16 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Innovation Hub](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [Certain Admin SSO](../saas-apps/certainadminsso-tutorial.md), PSUC Staging, [iPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [Screencast-O-Matic](../saas-apps/screencast-tutorial.md), PowerSchool Unified Classroom, [Eli Onboarding](../saas-apps/elionboarding-tutorial.md), [Bomgar Remote Support](../saas-apps/bomgarremotesupport-tutorial.md), [Nimblex](../saas-apps/nimblex-tutorial.md), [Imagineer WebVision](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC](../saas-apps/insight4grc-tutorial.md), [SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](../saas-apps/skillsbase-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Neue Integrationen zur Benutzerbereitstellung in SaaS-Apps – Juli 2018

**Typ:** Neues Feature-**Dienstkategorie:** App-Bereitstellungs-**Produktfunktion:** Integration von Drittanbieterprodukten

Azure AD ermöglicht Ihnen das automatisierte Erstellen, Warten und Entfernen von Benutzeridentitäten in SaaS-Anwendungen, z.B. Dropbox, Salesforce, ServiceNow und anderen. Für Juli 2018 haben wir für die folgenden Anwendungen im Azure AD-App-Katalog Unterstützung für die Benutzerbereitstellung hinzugefügt:

- [Cisco Webex](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Eine Liste mit allen Anwendungen, für die die Benutzerbereitstellung im Azure AD-Katalog unterstützt wird, finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health für die Synchronisierung – eine einfachere Möglichkeit, um Synchronisierungsfehler in Verbindung mit verwaisten und doppelten Attributen zu beheben.

**Typ:** Neues Feature-**Dienstkategorie:** AD Connect-**Produktfunktion:** Überwachung und Berichterstellung

Azure AD Connect Health führt die Self-Service-Wartung ein, die Sie beim Hervorheben und Beheben von Synchronisierungsfehlern unterstützt. Dieses Feature kann für die Problembehandlung bei Synchronisierungsfehlern aufgrund doppelter Attribute und zur Behebung von in Azure AD verwaisten Objekten verwendet werden. Diese Diagnose hat folgende Vorteile:

- Eingrenzung von Synchronisierungsfehlern mit doppelten Attributen, Bereitstellung spezifischer Behebungsverfahren

- Anwendung einer Korrektur für dedizierte Azure AD-Szenarien, Beheben von Fehlern in einem einzigen Schritt

- Kein Upgrade- oder Konfigurationsaufwand zur Aktivierung und Verwendung dieses Features

Weitere Informationen finden Sie unter [Diagnose und Behebung von Synchronisierungsfehlern aufgrund doppelter Attribute](../hybrid/how-to-connect-health-diagnose-sync-errors.md).

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuelle Updates der Azure AD- und MSA-Anmeldebenutzeroberflächen

**Typ:** Geändertes Feature-**Dienstkategorie:** Azure AD-**Produktfunktion:** Benutzerauthentifizierung

Wir haben die Benutzeroberfläche für die Anmeldung bei Microsoft-Onlinediensten wie Office 365 und Azure aktualisiert. Durch diese Änderung sind die Bildschirme weniger überladen und übersichtlicher. Weitere Informationen zu dieser Änderung finden Sie im Blog [Upcoming improvements to the Azure AD sign-in experience (Bevorstehende Verbesserungen der Azure AD-Anmeldebenutzeroberfläche)](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Neues Release von Azure AD Connect – Juli 2018

**Typ:** Geändertes Feature-**Dienstkategorie:** App-Bereitstellungs-**Produktfunktion:** Identity Lifecycle Management

Das neueste Release von Azure AD Connect umfasst:

- Programmfehlerbehebungen und Unterstützungsupdates

- Allgemeine Verfügbarkeit der PingFederate-Integration

- Updates des aktuellen SQL 2012-Clients

Weitere Informationen zu diesem Update finden Sie unter [Azure AD Connect: Verlauf der Versionsveröffentlichungen](../hybrid/reference-connect-version-history.md).

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Updates der Endbenutzeroberfläche der Nutzungsbedingungen

**Typ:** Geändertes Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Governance

Wir aktualisieren die Zustimmungszeichenfolge in der Endbenutzeroberfläche der ToU.

**Aktueller Text.** Für den Zugriff auf die [Mandantenname]-Ressourcen müssen Sie die Nutzungsbedingungen lesen.<br>**Neuer Text.** Für den Zugriff auf die [Mandantenname]-Ressource müssen Sie die Nutzungsbedingungen lesen.

**Aktueller Text:** Wenn Sie „Akzeptieren“ auswählen, bedeutet dies, dass Sie allen oben aufgeführten Nutzungsbedingungen zustimmen.<br>**Neuer Text:** Klicken Sie auf „Akzeptieren“, um zu bestätigen, dass Sie die Nutzungsbedingungen gelesen und verstanden haben.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Passthrough-Authentifizierung unterstützt ältere Protokolle und Anwendungen

**Typ:** Geändertes Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Passthrough-Authentifizierung unterstützt jetzt ältere Protokolle und Apps. Folgende Einschränkungen werden jetzt durch vollständige Unterstützung aufgehoben:

- Benutzeranmeldungen bei älteren Office-Clientanwendungen, Office 2010 und Office 2013, ohne moderne Authentifizierung.

- Zugriff auf Kalenderfreigabe und Frei-/Gebucht-Informationen in Exchange-Hybridumgebungen nur in Office 2010.

- Benutzeranmeldungen bei Skype for Business-Clientanwendungen ohne moderne Authentifizierung.

- Benutzeranmeldungen bei PowerShell Version 1.0.

- Das Apple Device Enrollment Program (Apple DEP. Programm zur Geräteregistrierung) mit dem iOS-Setup-Assistenten.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Zusammengeführte Sicherheitsinformationenverwaltung für Self-Service-Kennwortzurücksetzung und Multi-Factor Authentication

**Typ:** Neues Feature-**Dienstkategorie:** SSPR-**Produktfunktion:** Benutzerauthentifizierung

Diese neue Funktion ermöglicht Benutzern die Verwaltung ihrer Sicherheitsinformationen (z.B. Telefonnummer, E-Mail-Adresse, mobile App usw.) für die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) und Multi-Factor Authentication (MFA) in einer einzigen Benutzeroberfläche. Benutzer müssen nicht mehr die gleichen Sicherheitsinfos für SSPR und MFA in zwei unterschiedliche Benutzeroberflächen registrieren. Diese neue Benutzeroberfläche gilt auch für Benutzer, die entweder über SSPR oder MFA verfügen.

Wenn eine Organisation die MFA- oder SSPR-Registrierung nicht erzwingt, können Benutzer ihre Sicherheitsinformationen über das **Meine Apps**-Portal registrieren. Von dort aus können Benutzer mit beliebigen für MFA oder SSPR aktivierten Methoden registrieren.

Dies ist eine abonnierbare Public Preview. Administratoren können die neue Oberfläche (falls gewünscht) für eine ausgewählte Gruppe von Benutzern oder alle Benutzer in einem Mandanten aktivieren.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Verwenden Sie die Microsoft Authenticator-App, um Ihre Identität zu bestätigen, wenn Sie Ihr Kennwort zurücksetzen.

**Typ:** Geändertes Feature-**Dienstkategorie:** SSPR-**Produktfunktion:** Benutzerauthentifizierung

Mit diesem Feature können Benutzer, die keine Administratoren sind, ihre Identität bestätigen, während sie ein Kennwort mithilfe einer Benachrichtigung oder mittels Code aus der Microsoft Authenticator-App (oder einer beliebigen anderen Authenticator-App) zurücksetzen. Nachdem Administratoren diese Methode zur Self-Service-Kennwortzurücksetzung aktiviert haben, können Benutzer, die eine mobile App über „aka.ms/mfasetup“ oder „aka.ms/setupsecurityinfo“ registriert haben, ihre mobile App beim Zurücksetzen des Kennworts als Überprüfungsmethode verwenden.

Die Benachrichtigung über die mobile App kann nur als Teil einer Richtlinie aktiviert werden, die zwei Methoden zum Zurücksetzen Ihres Kennworts erfordert.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Änderungshinweis: Sicherheitsfix für den Flow für die delegierte Autorisierung für Apps, die die Azure AD-Aktivitätsprotokolle-API verwenden

**Typ:** Plan für Änderung der **Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Aufgrund unserer strengeren Durchsetzung von Sicherheitsmaßnahmen mussten wir die Berechtigungen für Apps ändern, die für den Zugriff auf [Azure AD-Aktivitätsprotokolle-APIs](../reports-monitoring/concept-reporting-api.md) einen Flow für die delegierte Autorisierung verwenden. Diese Änderung erfolgt am **26. Juni 2018**.

Wenn eine Ihrer Apps Azure AD-Aktivitätsprotokolle-APIs verwendet, gehen Sie folgendermaßen vor, um sicherzustellen, dass die App nach Durchführung der Änderung noch betriebsfähig ist.

**So aktualisieren Sie Ihre App-Berechtigungen**

1. Melden Sie sich beim Azure-Portal an. Wählen Sie **Azure Active Directory** und dann **App-Registrierungen** aus.
2. Wählen Sie die App aus, die die Azure AD-Aktivitätsprotokolle-API nutzt. Wählen Sie dann **Einstellungen**, danach **Erforderliche Berechtigungen** und schließlich die API **Microsoft Azure Active Directory** aus.
3. Aktivieren Sie im Bereich **Delegierte Berechtigungen** des Blatts **Zugriff aktivieren** das Kontrollkästchen neben **Verzeichnisdaten lesen**, und wählen Sie dann **Speichern** aus.
4. Wählen Sie **Berechtigungen erteilen** und dann **Ja** aus.

    >[!Note]
    >Sie müssen globaler Administrator sein, um Berechtigungen für die App zu erteilen.

Weitere Informationen finden Sie im Abschnitt [Erteilen von Berechtigungen](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) des Artikels „Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API“.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurieren von TLS-Einstellungen zum Herstellen einer Verbindung mit Azure AD-Diensten zum Zweck der PCI-DSS-Konformität

**Typ:** Neues Feature-**Dienstkategorie:** N/V-**Produktfunktion:** Plattform

Transport Layer Security (TLS) ist ein Protokoll, das Datenschutz und -integrität zwischen zwei kommunizierenden Anwendungen gewährleistet, und derzeit das am weitesten verbreitete Sicherheitsprotokoll.

Der [PCI Security Standards Council](https://www.pcisecuritystandards.org/) hat festgelegt, dass frühe Versionen von TLS und Secure Sockets Layer (SSL) deaktiviert werden müssen, um neue und sicherere Anwendungsprotokolle zu ermöglichen, wobei Konformität ab dem **30. Juni 2018** gegeben sein muss. Diese Änderung bedeutet, dass Sie TLS 1.0 deaktivieren müssen, wenn Sie eine Verbindung mit Azure AD-Diensten herstellen und PCI-DSS-Konformität benötigen. Mehrere TLS-Versionen sind verfügbar, aber TLS 1.2 ist die neueste für Azure Active Directory Services verfügbare Version. Es wird ausdrücklich empfohlen, für Client/Server- und Browser/Server-Kombinationen direkt zu TLS 1.2 zu wechseln.

Veraltete Browser unterstützen möglicherweise keine neueren TLS-Versionen wie z.B. TLS 1.2. Um festzustellen, welche Versionen von TLS von Ihrem Browser unterstützt werden, besuchen Sie die Website [Qualys SSL Labs](https://www.ssllabs.com/), und klicken Sie auf **Test your browser**. Sie sollten auf die neueste Version Ihres Webbrowsers aktualisieren und vorzugsweise nur TLS 1.2 aktivieren.

**Aktivieren von TLS 1.2 je nach Browser**

- **Microsoft Edge und Internet Explorer (beide werden mit Internet Explorer festgelegt)**

    1. Öffnen Sie Internet Explorer, und wählen Sie **Tools** > **Internetoptionen** > **Erweitert** aus.
    2. Wählen Sie im Bereich **Sicherheit** die Option **TLS 1.2 verwenden** aus, und klicken Sie dann auf **OK**.
    3. Schließen Sie alle Browserfenster, und starten Sie Internet Explorer neu.

- **Google Chrome**

    1. Öffnen Sie Google Chrome. Geben Sie in die Adressleiste *chrome://settings/* ein, und drücken Sie die **EINGABETASTE**.
    2. Blenden Sie die Optionen unter **Erweitert** ein. Wechseln Sie zum Abschnitt **System**, und klicken Sie auf **Proxy-Einstellungen öffnen**.
    3. Wählen Sie im Feld **Interneteigenschaften** die Registerkarte **Erweitert** aus. Wechseln Sie zum Abschnitt **Sicherheit**, wählen Sie **TLS 1.2 verwenden** aus, und klicken Sie dann auf  **OK**.
    4. Schließen Sie alle Browserfenster, und starten Sie Google Chrome neu.

- **Mozilla Firefox**

    1. Öffnen Sie Firefox. Geben Sie in die Adressleiste *about:config* ein, und drücken Sie dann die **EINGABETASTE**.
    2. Suchen Sie nach dem Begriff *TLS*, und wählen Sie dann den Eintrag **security.tls.version.max** aus.
    3. Legen Sie den Wert auf **3** fest, um den Browser zu zwingen, Versionen bis TLS 1.2 zu verwenden, und klicken Sie dann auf **OK**.

        >[!NOTE]
        >Die Firefox-Version 60.0 unterstützt TLS 1.3, sodass Sie den Wert „security.tls.version.max“ auch auf **4** festlegen können.

    4. Schließen Sie alle Browserfenster, und starten Sie Mozilla Firefox neu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Juni 2018

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Juni 2018 haben wir diese 15 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Skytap](../saas-apps/skytap-tutorial.md), [Settling music](../saas-apps/settlingmusic-tutorial.md), [Branchenspezifische App mit Unterstützung von SAML 1.1-Token](../saas-apps/saml-tutorial.md), [Supermood](../saas-apps/supermood-tutorial.md), [Autotask](../saas-apps/autotaskendpointbackup-tutorial.md), [Endpoint Backup](../saas-apps/autotaskendpointbackup-tutorial.md), [Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TonicDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [lokales SharePoint](../saas-apps/sharepoint-on-premises-tutorial.md), [ForeSee CX Suite](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard](../saas-apps/vidyard-tutorial.md), [ChronicX](../saas-apps/chronicx-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md). Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Kennwortschutz für Azure AD in Public Preview verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Benutzerauthentifizierung

Nutzen Sie den Kennwortschutz für Azure AD, um leicht zu erratende Kennwörter aus Ihrer Umgebung zu verbannen. Das Verbannen dieser Kennwörter trägt dazu bei, das Risiko einer Gefährdung durch einen Kennwort-Spray-Angriff zu verringern.

Der Kennwortschutz für Azure AD hilft insbesondere bei folgenden Aufgaben:

- Schützen der Konten Ihrer Organisation in sowohl Azure AD als auch Windows Server Active Directory (AD).
- Verhindern, dass Ihre Benutzer Kennwörter in einer Liste der mehr als 500 am häufigsten verwendeten Kennwörter und über 1 Mio. Zeichenersetzungsvarianten dieser Kennwörter verwenden.
- Verwalten des Kennwortschutzes für Azure AD an zentraler Stelle im Azure AD-Portal, sowohl für Azure AD als auch für lokales Windows Server AD.

Weitere Informationen zum Kennwortschutz für Azure AD finden Sie unter [Beseitigen falscher Kennwörter in Ihrer Organisation](../authentication/concept-password-ban-bad.md).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Neue Vorlage für eine Richtlinie für bedingten Zugriff namens „Alle Gäste“, die während der Erstellung der Nutzungsbedingungen erstellt wird

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Governance

Während der Erstellung der Nutzungsbedingungen wird eine neue Vorlage für eine Richtlinie für bedingten Zugriff auch für „Alle Gäste“ und „Alle Apps“ erstellt. Diese neue Richtlinienvorlage wendet die neu erstellten Nutzungsbedingungen an und optimiert den Erstellungs- und Erzwingungsprozess für Gäste.

Weitere Informationen finden Sie unter [Nutzungsbedingungsfeature für Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Neue „benutzerdefinierte“ Vorlage für eine Richtlinie für bedingten Zugriff, die während der Erstellung der Nutzungsbedingungen erstellt wird

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Governance

Während der Erstellung der Nutzungsbedingungen wird auch eine neue „benutzerdefinierte“ Vorlage für eine Richtlinie für bedingten Zugriff erstellt. Mit dieser neuen Richtlinienvorlage können Sie die Nutzungsbedingungen erstellen und dann sofort zum Blatt für die Erstellung von Richtlinien mit bedingtem Zugriff wechseln, ohne manuell durch das Portal navigieren zu müssen.

Weitere Informationen finden Sie unter [Nutzungsbedingungsfeature für Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Neue und umfassende Anleitung zum Bereitstellen von Azure AD Multi-Factor Authentication

**Typ:** Neues Feature-**Dienstkategorie:** Andere **Produktfunktion:** Identitätssicherheit und -schutz

Wir haben eine neue detaillierte Anleitung zum Bereitstellen von Azure AD Multi-Factor Authentication (MFA) in Ihrer Organisation veröffentlicht.

Die MFA-Bereitstellungsanleitung finden Sie auf GitHub im Repository [Identity Deployment Guides](./active-directory-deployment-plans.md). Um Feedback zu den Bereitstellungsanleitungen zu geben, verwenden Sie das Formular [Deployment Plan Feedback](https://aka.ms/deploymentplanfeedback). Bei Fragen zu den Bereitstellungsanleitungen wenden Sie sich unter [IDGitDeploy](mailto:idgitdeploy@microsoft.com) an uns.

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Delegierte Azure AD-App-Verwaltungsrollen in Public Preview

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Zugriffssteuerung

Administratoren können nun App-Verwaltungsaufgaben delegieren, ohne die Rolle „Globaler Administrator“ zuzuweisen. Es gibt die folgenden neuen Rollen und Funktionen:

- **Neue Azure AD-Standardadministratorrollen:**

    - **Anwendungsadministrator.** Erteilt die Berechtigung, alle Aspekte aller Apps zu verwalten, einschließlich Registrierung, Einstellungen für einmaliges Anmelden (SSO), Zuweisung und Lizenzierung von Apps, Proxyeinstellungen für Apps und Zustimmung (mit Ausnahme von Azure AD-Ressourcen).

    - **Cloudanwendungsadministrator.** Erteilt alle Berechtigungen des Anwendungsadministrators außer für den App-Proxy, da dieser keinen lokalen Zugriff bietet.

    - **Anwendungsentwickler.** Erteilt die Berechtigung zum Erstellen von App-Registrierungen, auch wenn die Option **Benutzer die Registrierung von Apps erlauben** deaktiviert ist.

- **Besitz (wird vergleichbar mit dem Gruppenbesitzprozess je App-Registrierung und Unternehmens-App eingerichtet):**

    - **App-Registrierungsbesitzer.** Erteilt die Berechtigung, alle Aspekte der eigenen App-Registrierung zu verwalten, einschließlich App-Manifest und Hinzufügung weiterer Besitzer.

    - **Unternehmens-App-Besitzer.** Erteilt die Berechtigung, zahlreiche Aspekte eigener Unternehmens-Apps zu verwalten, einschließlich Einstellungen für einmaliges Anmelden (SSO), App-Zuweisungen und Zustimmung (mit Ausnahme von Azure AD-Ressourcen).

Weitere Informationen zur Public Preview finden Sie im Blogbeitrag [Azure AD delegated application management roles are in public preview! (Delegierte Azure AD-App-Verwaltungsrollen in Public Preview)](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) . Weitere Informationen zu Rollen und Berechtigungen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](../roles/permissions-reference.md).

---

## <a name="may-2018"></a>Mai 2018

### <a name="expressroute-support-changes"></a>Änderungen der Unterstützung für ExpressRoute

**Typ:** Plan für Änderung der **Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Plattform

„Software-as-a-Service“-Angebote wie Azure Active Directory (Azure AD) sind so konzipiert, dass sie direkt über das Internet – ohne ExpressRoute oder sonstige private VPN-Tunnel – am besten funktionieren. Aus diesem Grund beenden wir am **1. August 2018** die Unterstützung für Dienste für ExpressRoute für Azure AD, die öffentliches Azure-Peering und Azure-Communitys in Microsoft-Peering nutzen. Bei allen Diensten, die von dieser Änderung betroffen sind, ist möglicherweise die allmähliche Verschiebung des Azure AD-Datenverkehrs von ExpressRoute zum Internet spürbar.

Während wir unsere Unterstützung ändern, wissen wir allerdings auch, dass es noch Situationen gibt, in denen Sie möglicherweise eine dedizierte Gruppe von Leitungen für Ihren Authentifizierungsdatenverkehr verwenden müssen. Aus diesem Grund wird Azure AD weiterhin Einschränkungen des IP-Adressbereichs pro Mandant mithilfe von ExpressRoute und Diensten, die bereits im Microsoft-Peering enthalten sind, mit der Community „Other Office 365 Online services“ unterstützen. Wenn Ihre Dienste betroffen sind, Sie jedoch ExpressRoute benötigen, müssen Sie die folgenden Schritte ausführen:

- **Wenn Sie sich im öffentlichen Azure-Peering befinden.** Wechseln Sie zu Microsoft-Peering, und registrieren Sie sich für die Community **Other Office 365 Online services (12076:5100)** . Weitere Informationen über den Wechsel vom öffentlichen Azure-Peering zum Microsoft-Peering finden Sie im Artikel [Umstellen von öffentlichem Peering auf Microsoft-Peering](../../expressroute/how-to-move-peering.md).

- **Wenn Sie sich im Microsoft-Peering befinden.** Registrieren Sie sich für die Community **Other Office 365 Online service (12076:5100)** . Weitere Informationen zu Routinganforderungen finden Sie im Artikel über ExpressRoute-Routinganforderungen im Abschnitt [Unterstützung für BGP-Communitys](../../expressroute/expressroute-routing.md#bgp).

Wenn Sie weiterhin dedizierte Leitungen verwenden müssen, müssen Sie mit Ihrem Microsoft-Kontoteam erörtern, wie Sie die Autorisierung für die Nutzung der Community **Other Office 365 Online service (12076:5100)** erhalten. Das von MS Office verwaltete Prüfungsgremium überprüft, ob Sie diese Leitungen benötigen, und stellt sicher, dass Sie verstehen, welche technischen Auswirkungen damit verbunden sind, sie beizubehalten. Nicht autorisierte Abonnements, die versuchen, Weiterleitungsfilter für Office 365 zu erstellen, erhalten eine Fehlermeldung.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph-APIs zum Verwalten der Nutzungsbedingungen

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Entwickleroberfläche

Wir haben Microsoft Graph-APIs für die Verwaltung der Azure AD-Nutzungsbedingungen hinzugefügt. Sie können das Objekt mit den Nutzungsbedingungen erstellen, aktualisieren und löschen.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Möglichkeit zum Hinzufügen des mehrinstanzenfähigen Azure AD-Endpunkts als Identitätsanbieter in Azure AD B2C

**Typ:** Neues Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Mit benutzerdefinierten Richtlinien können Sie jetzt den gemeinsamen Endpunkt für Azure AD als Identitätsanbieter in Azure AD B2C hinzufügen. Auf diese Weise können Sie einen zentralen Zugangspunkt (Single Point of Entry) für alle Azure AD-Benutzer verwenden, die sich an Ihren Anwendungen anmelden. Weitere Informationen finden Sie unter [Azure Active Directory B2C: Zulassen, dass Benutzer sich mithilfe von benutzerdefinierten Richtlinien bei einem mehrinstanzenfähigen Azure AD-Identitätsanbieter anmelden](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant-custom.md).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Verwendung interner URLs für einen standortunabhängigen App-Zugriff – dank Erweiterung zur sicheren Anmeldung bei „Meine Apps“ und Azure AD-Anwendungsproxy

**Typ:** Neues Feature-**Dienstkategorie:** Meine Apps-**Produktfunktion:** SSO

Benutzer können über interne URLs jetzt auch dann auf Anwendungen zugreifen, wenn sie sich außerhalb Ihres Unternehmensnetzwerks befinden, indem sie die Erweiterung zur sicheren Anmeldung bei „Meine Apps“ verwenden. Dies funktioniert mit allen Anwendungen, die Sie per Azure AD-Anwendungsproxy veröffentlicht haben, und mit jedem Browser, für den die Browsererweiterung für den Zugriffsbereich installiert ist. Die Funktion für die URL-Umleitung wird automatisch aktiviert, nachdem sich ein Benutzer an der Erweiterung angemeldet hat. Die Erweiterung ist für [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) und [Firefox](https://go.microsoft.com/fwlink/?linkid=866366) als Download verfügbar.

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – Datenspeicherung in Europa für europäische Kunden

**Typ:** Neues Feature-**Dienstkategorie:** Andere **Produktfunktion:** GoLocal

Die Daten von Kunden in Europa müssen in Europa bleiben und dürfen nicht außerhalb von europäischen Rechenzentren repliziert werden. Dies ist erforderlich, um den Datenschutz zu gewährleisten und EU-Gesetze einzuhalten. [Dieser Artikel](./active-directory-data-storage-eu.md) enthält spezifische Details dazu, welche Identitätsinformationen in Europa gespeichert werden, und außerdem Details zu den Informationen, die außerhalb von europäischen Datencentern gespeichert werden.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Neue Integrationen zur Benutzerbereitstellung in SaaS-Apps – Mai 2018

**Typ:** Neues Feature-**Dienstkategorie:** App-Bereitstellungs-**Produktfunktion:** Integration von Drittanbieterprodukten

Azure AD ermöglicht Ihnen das automatisierte Erstellen, Warten und Entfernen von Benutzeridentitäten in SaaS-Anwendungen, z.B. Dropbox, Salesforce, ServiceNow und anderen. Für Mai 2018 haben wir für die folgenden Anwendungen im Azure AD-App-Katalog Unterstützung für die Benutzerbereitstellung hinzugefügt:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Eine Liste mit allen Anwendungen, für die die Benutzerbereitstellung im Azure AD-Katalog unterstützt wird, finden Sie unter [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md).

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Möglichkeit zur regelmäßigen Durchführung von Azure AD-Zugriffsüberprüfungen für Gruppen- und App-Zugriff

**Typ:** Neues Feature-**Dienstkategorie:** Zugriffsüberprüfungen-**Produktfunktion:** Governance

Die Zugriffsüberprüfung von Gruppen und Apps ist für Azure AD Premium P2 jetzt allgemein verfügbar.  Administratoren können die Zugriffsüberprüfungen von Gruppenmitgliedschaften und Anwendungszuordnungen so konfigurieren, dass sie automatisch in bestimmten Intervallen durchgeführt werden, z.B. monatlich oder einmal pro Quartal.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-Aktivitätsprotokolle (Anmeldevorgänge und Überwachung) jetzt über MS Graph verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Azure AD-Aktivitätsprotokolle, die Anmeldungen und Überwachungsprotokolle umfassen, sind jetzt über die Microsoft Graph-API verfügbar. Wir haben zwei Endpunkte über die Microsoft Graph-API verfügbar gemacht, um auf diese Protokolle zuzugreifen. Sehen Sie sich zum Einstieg unsere [Dokumente](../reports-monitoring/concept-reporting-api.md) zum programmgesteuerten Zugriff auf Azure AD-Berichterstellungs-APIs an.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Verbesserungen an der B2B-Oberfläche für die Einlösung und einfacheres Verlassen einer Organisation

**Typ:** Neues Feature-**Dienstkategorie:** B2B-**Produktfunktion:** B2B/B2C

**Just-in-Time-Einlösung:** Nachdem Sie eine Ressource für einen Gastbenutzer per B2B-API freigegeben haben, müssen Sie keine spezielle Einladungs-E-Mail mehr senden. In den meisten Fällen kann der Gastbenutzer auf die Ressource zugreifen, und der Einlösevorgang wird „just in time“ durchgeführt. Es entstehen keine Probleme aufgrund von verloren gegangenen E-Mails mehr. Sie müssen Ihren Gastbenutzern nicht mehr die folgende Frage stellen: „Haben Sie auf den vom System gesendeten Link für die Einlösung geklickt?“ Sobald SPO den Einladungs-Manager verwendet, können Anhänge aus der Cloud für alle Benutzer dieselbe kanonische URL verwenden – sowohl intern als auch extern und in jedem Stadium der Einlösung.

**Moderne Oberfläche für die Einlösung:** Es gibt keine Startseite mit geteiltem Bildschirm mehr. Benutzer sehen eine moderne Oberfläche für die Zustimmung, auf der die Datenschutzerklärung der einladenden Organisation angezeigt wird – genau wie bei Drittanbieter-Apps.

**Gastbenutzer können die Organisation verlassen:** Sobald die Beziehung eines Benutzers mit einer Organisation beendet ist, kann dieser die Organisation selbstständig verlassen. Anrufe beim Administrator der einladenden Organisation zum „Entfernen“ des Benutzers entfallen, ebenso wie das Erstellen von Supporttickets.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar – Mai 2018

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Mai 2018 haben wir diese 18 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix Data3Sixty Govern, [Yodeck](../saas-apps/infogix-tutorial.md), [Jamf Pro](../saas-apps/jamfprosamlconnector-tutorial.md), [KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md), [Envi MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly](../saas-apps/launchdarkly-tutorial.md), [Adobe Captivate Prime](../saas-apps/adobecaptivateprime-tutorial.md), [Montage Online](../saas-apps/montageonline-tutorial.md), [まなびポケット](../saas-apps/manabipocket-tutorial.md), OpenReel, [Arc Publishing - SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [Riskware](../saas-apps/riskware-tutorial.md), [Flock](../saas-apps/flock-tutorial.md), [Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md).

Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Neue detaillierte Bereitstellungsanleitungen für Azure Active Directory

**Typ:** Neues Feature-**Dienstkategorie:** Andere **Produktfunktion:** Verzeichnis

Neue detaillierte Anleitungen für die Bereitstellung von Azure Active Directory (Azure AD), einschließlich Self-Service-Kennwortzurücksetzung, einmaliges Anmelden, bedingter Zugriff, App-Proxy, Benutzerbereitstellung, Active Directory-Verbunddienste (AD FS) zu Pass-Through-Authentifizierung und AD FS zu Kennworthashsynchronisierung.

Die Bereitstellungsanleitungen finden Sie auf GitHub im Repository [Identity Deployment Guides](./active-directory-deployment-plans.md). Um Feedback zu den Bereitstellungsanleitungen zu geben, verwenden Sie das Formular [Deployment Plan Feedback](https://aka.ms/deploymentplanfeedback). Bei Fragen zu den Bereitstellungsanleitungen wenden Sie sich unter [IDGitDeploy](mailto:idgitdeploy@microsoft.com) an uns.

---

### <a name="enterprise-applications-search---load-more-apps"></a>Suche nach Unternehmensanwendungen – Laden weiterer Apps

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Haben Sie Probleme, Ihre Anwendungen bzw. Dienstprinzipale zu finden? Wir haben eine Funktion hinzugefügt, um mehr Anwendungen in Ihre Liste der Unternehmensanwendungen zu laden. Standardmäßig werden 20 Anwendungen angezeigt. Sie können ab sofort auf **Weitere laden** klicken, um zusätzliche Anwendungen anzuzeigen.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Die im Mai veröffentlichte Version von AADConnect umfasst eine öffentliche Vorschau der PingFederate-Integration, wichtige Sicherheitsupdates, zahlreiche Fehlerkorrekturen und neue großartige Tools für die Problembehandlung.

**Typ:** Geändertes Feature-**Dienstkategorie:** AD Connect-**Produktfunktion:** Identity Lifecycle Management

Die im Mai veröffentlichte Version von AADConnect umfasst eine öffentliche Vorschau der PingFederate-Integration, wichtige Sicherheitsupdates, zahlreiche Fehlerkorrekturen und neue großartige Tools für die Problembehandlung. Die Versionshinweise finden Sie [hier](../hybrid/reference-connect-version-history.md).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-Zugriffsüberprüfungen: automatische Anwendung

**Typ:** Geändertes Feature-**Dienstkategorie:** Zugriffsüberprüfungen-**Produktfunktion:** Governance

Zugriffsüberprüfungen von Gruppen und Apps sind für Azure AD Premium P2 jetzt allgemein verfügbar. Ein Administrator kann konfigurieren, dass die Änderungen des Prüfers automatisch auf diese Gruppe oder App angewendet werden, sobald die Zugriffsüberprüfung abgeschlossen wurde. Der Administrator kann auch festlegen, was mit dem fortgesetzten Zugriff des Benutzers geschieht, wenn Prüfer nicht antworten. Mögliche Optionen hierbei sind: Zugriff entfernen, Zugriff beibehalten oder Systemempfehlungen annehmen.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Für neue Apps können ID-Token nicht mehr über den response_mode „query“ zurückgegeben werden.

**Typ:** Geändertes Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Ab dem 25.04.2018 erstellte Apps sind nicht mehr in der Lage, ein **id_token** über den response_mode **query** anzufordern.  Damit entspricht Azure AD den OIDC-Spezifikationen und trägt dazu bei, die Angriffsfläche Ihrer Apps zu verringern.  Vor dem 25.04.2018 erstellte Apps werden nicht daran gehindert, den response_mode **query** mit dem response_typ **id_token** zu verwenden.  Beim Anfordern eines id_token aus Azure AD wird dieser Fehler zurückgegeben: **AADSTS70007: „query“ ist beim Anfordern eines Tokens kein unterstützter Wert von „response_mode“** .

Die response_mode-Werte **fragment** und **form_post** funktionieren weiterhin. Vergewissern Sie sich beim Erstellen neuer Anwendungsobjekte (z.B. für die App-Proxyverwendung) vor dem Erstellen einer neuen Anwendung, dass einer dieser response_mode-Werte verwendet wird.

---

## <a name="april-2018"></a>April 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C-Zugriffstoken jetzt allgemein verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Sie können nun mit Zugriffstoken auf Web-APIs zugreifen, die von Azure AD B2C geschützt werden. Das Feature wird von der Public Preview in die allgemeine Verfügbarkeit verlegt. Die Benutzeroberfläche zum Konfigurieren von Azure AD B2C-Anwendungen und Web-APIs wurde verbessert, und andere kleinere Verbesserungen wurden vorgenommen.

Weitere Informationen finden Sie unter [Azure AD B2C: Anfordern von Zugriffstoken](../../active-directory-b2c/access-tokens.md).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testen der SSO-Konfiguration für SAML-basierte Anwendungen

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Beim Konfigurieren von SAML-basierten SSO-Anwendungen können Sie die Integration auf der Konfigurationsseite testen. Wenn während des Anmeldens ein Fehler auftritt, können Sie den Fehler auf der Testbenutzeroberfläche bereitstellen, und Azure AD bietet Ihnen Lösungsschritte für das spezifische Problem an.

Weitere Informationen finden Sie unter

- [Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind](../manage-apps/view-applications-portal.md)
- [Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD-Nutzungsbedingungen jetzt mit benutzerbasierter Berichterstellung

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Compliance

Administratoren können jetzt bestimmte Nutzungsbedingungen auswählen und alle Benutzer anzeigen, die diesen Nutzungsbedingungen zugestimmt haben, sowie Datum/Uhrzeit der Zustimmung.

Weitere Informationen finden Sie unter [Nutzungsbedingungsfeature für Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: AD FS-Extranetsperrschutz für riskante IP-Adressen

**Typ:** Neues Feature-**Dienstkategorie:** Andere **Produktfunktion:** Überwachung und Berichterstellung

Connect Health unterstützt nun die Möglichkeit, IP-Adressen zu erkennen, die einen Schwellenwert stündlicher oder täglicher fehlerhafter Benutzername/Kennwort-Anmeldungen überschreiten. Dieses Feature bietet folgende Funktionen:

- Umfassender Bericht, der IP-Adressen und die Anzahl der fehlerhaften stündlichen/täglichen Anmeldungen auf der Basis eines anpassbaren Schwellenwerts anzeigt.
- E-Mail-basierte Warnungen, die anzeigen, wenn eine bestimmte IP-Adresse den Schwellenwert fehlerhafter stündlicher/täglicher Benutzername/Kennwort-Anmeldungen überschritten hat.
- Eine Downloadoption für eine detaillierte Datenanalyse

Weitere Informationen finden Sie unter [Bericht über riskante IP-Adressen (Public Preview)](../hybrid/how-to-connect-health-adfs.md).

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Einfache App-Konfiguration mit Metadatendatei oder URL

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Auf der Enterprise-Anwendungsseite können Administratoren eine SAML-Metadatendatei zum Konfigurieren der SAML-basierten Anmeldung für Azure AD-Katalog-Anwendungen sowie für nicht aus dem Katalog stammende Anwendungen hochladen.

Darüber hinaus können Sie die Azure AD-Anwendungsverbundmetadaten-URL zum Konfigurieren von einmaligem Anmelden mit der entsprechenden Anwendung verwenden.

Weitere Informationen finden Sie unter [Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind](../manage-apps/view-applications-portal.md).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD-Nutzungsbedingungen jetzt allgemein verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Compliance


Azure AD-Nutzungsbedingungen wurden von „Public Preview“ in „Allgemein verfügbar“ geändert.

Weitere Informationen finden Sie unter [Nutzungsbedingungsfeature für Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zulassen oder Blockieren von Einladungen für B2B-Benutzer von bestimmten Organisationen

**Typ:** Neues Feature-**Dienstkategorie:** B2B-**Produktfunktion:** B2B/B2C


Sie können jetzt angeben, für welche Partnerorganisationen Sie die Azure AD B2B-Zusammenarbeit und Freigaben einrichten möchten. Zu diesem Zweck können Sie wahlweise eine Liste der spezifischen Zulassungs- oder Verweigerungsdomänen erstellen. Wenn eine Domäne mit diesen Funktionen blockiert wird, können Mitarbeiter nicht mehr Einladungen an Personen in dieser Domäne senden.

So können Sie leichter den Zugriff auf Ihre Ressourcen steuern und dabei die Benutzerfreundlichkeit für berechtigte Benutzer garantieren.

Dieses B2B-Zusammenarbeitsfeature steht allen Kunden von Azure Active Directory zur Verfügung, und in Verbindung mit Azure AD Premium-Features wie bedingtem Zugriff und Identitätsschutz können Sie damit differenzierter steuern, wann und wie sich externe Geschäftsbenutzer anmelden und Zugriff erhalten.

Weitere Informationen finden Sie unter [Zulassen oder Blockieren von Einladungen für B2B-Benutzer von bestimmten Organisationen](../external-identities/allow-deny-list.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im April 2018 haben wir diese 13 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

Criterion HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (On-Premises)](../saas-apps/secretserver-on-premises-tutorial.md), [Dynamic Signal](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [OrgChart Now](../saas-apps/orgchartnow-tutorial.md), [Ziflow](../saas-apps/ziflow-tutorial.md), [AppNeta Performance Monitor](../saas-apps/appneta-tutorial.md), [Elium](../saas-apps/elium-tutorial.md), [Fluxx Labs](../saas-apps/fluxxlabs-tutorial.md), [Cisco Cloud](../saas-apps/ciscocloud-tutorial.md), Shelf, [SafetyNet](../saas-apps/safetynet-tutorial.md)

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md).

Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Gewähren des Zugriffs auf Ihre lokalen Anwendungen für B2B-Benutzer in Azure AD (Public Preview)

**Typ:** Neues Feature-**Dienstkategorie:** B2B-**Produktfunktion:** B2B/B2C

Wenn Ihr Unternehmen Funktionen der Azure Active Directory B2B-Zusammenarbeit (Azure AD) verwendet, um Gastbenutzer aus Partnerunternehmen zu Ihrem Azure AD einzuladen, können Sie für diese B2B-Benutzer jetzt Zugriff auf lokale Apps bereitstellen. Diese lokalen Apps können die SAML-basierte Authentifizierung oder die integrierte Windows-Authentifizierung (Integrated Windows Authentication, IWA) mit eingeschränkter Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) verwenden.

Weitere Informationen finden Sie unter [Gewähren des Zugriffs auf Ihre lokalen Anwendungen für B2B-Benutzer in Azure AD](../external-identities/hybrid-cloud-to-on-premises.md).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Tutorials zur SSO-Integration im Azure Marketplace verfügbar

**Typ:** Geändertes Feature-**Dienstkategorie:** Andere **Produktfunktion:** Integration von Drittanbieterprodukten

Wenn eine im [Azure-Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) aufgelistete Anwendung einmaliges Anmelden auf SAML-Basis unterstützt, erhalten Sie beim Klicken auf **Jetzt anfordern** das der Anwendung zugeordnete Integrationstutorial.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Verbesserte Leistung bei der automatischen Azure AD-Benutzerbereitstellung in SaaS-Anwendungen

**Typ:** Geändertes Feature-**Dienstkategorie:** App-Bereitstellungs-**Produktfunktion:** Integration von Drittanbieterprodukten

Bisher konnten Kunden, die die Connectors für Azure Active Directory-Benutzerbereitstellung für SaaS-Anwendungen nutzten (z.B. Salesforce, ServiceNow und Box) eine schwache Leistung feststellen, wenn ihre Azure AD-Mandanten über 100.000 kombinierte Benutzer und Gruppen enthielten, und sie legten mit Benutzer- und Gruppenzuweisungen fest, welche Benutzer bereitgestellt werden sollten.

Am 2. April 2018 wurden sehr bedeutende Verbesserungen der Leistung des Azure AD-Bereitstellungsdiensts wirksam, die die erforderliche Zeitspanne zur Ausführung der Erstsynchronisierung von Azure Active Directory mit SaaS-Zielanwendungen erheblich reduzieren.

In der Folge können viele Kunden, bei denen die Erstsynchronisierungen mit Apps mehrere Tage dauerten oder niemals abgeschlossen wurden, den Vorgang jetzt innerhalb Minuten oder Stunden abschließen.

Weitere Informationen finden Sie unter [Vorgänge während der Bereitstellung](../..//active-directory/app-provisioning/how-provisioning-works.md).

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Self-Service-Kennwortzurücksetzung über Windows 10-Sperrbildschirm für hybride Computer mit Azure AD-Einbindung

**Typ:** Geändertes Feature-**Dienstkategorie:** Self-Service-Kennwortzurücksetzungs-**Produktfunktion:** Benutzerauthentifizierung

Das Windows 10-SSPR-Feature wurde aktualisiert, sodass es die Unterstützung für hybride Computer mit Azure AD-Einbindung einschließt. Dieses in Windows 10 RS4 verfügbare Feature ermöglicht Benutzern, ihre Kennwörter vom Sperrbildschirm eines Windows 10-Computers aus zurückzusetzen. Benutzer, die für die Self-Service-Kennwortzurücksetzung zugelassen und registriert sind, können dieses Feature nutzen.

Weitere Informationen finden Sie unter [Azure AD-Kennwortzurücksetzung über den Anmeldebildschirm](../authentication/howto-sspr-windows.md).

---

## <a name="march-2018"></a>März 2018

### <a name="certificate-expire-notification"></a>Benachrichtigung über Zertifikatablauf

**Typ:** Feste **Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Azure AD sendet eine Benachrichtigung, wenn ein Zertifikat für eine Katalog- oder Nicht-Kataloganwendung kurz vor dem Ablauf steht.

Einige Benutzer haben für Unternehmensanwendungen, die für SAML-basiertes einmaliges Anmelden konfiguriert waren, keine Benachrichtigungen erhalten. Dieses Problem wurde behoben. Azure AD sendet Benachrichtigungen für Zertifikate, die in 7, 30 und 60 Tagen ablaufen. Sie können dieses Ereignis in den Überwachungsprotokollen sehen.

Weitere Informationen finden Sie unter

- [Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Identitätsanbieter Twitter und GitHub in Azure AD B2C

**Typ:** Neues Feature-**Dienstkategorie:** B2C – Consumer Identity Management-**Produktfunktion:** B2B/B2C

Sie können in Azure AD B2C jetzt Twitter oder GitHub als Identitätsanbieter hinzufügen. Twitter stellt von der öffentlichen Vorschau auf allgemeine Verfügbarkeit um. GitHub wird als öffentliche Vorschau veröffentlicht.

Weitere Informationen finden Sie unter [Was ist die Azure AD B2B-Zusammenarbeit?](../external-identities/what-is-b2b.md).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Einschränken des Browserzugriffs durch Verwendung von Intune Managed Browser mit anwendungsbasiertem bedingtem Azure AD-Zugriff für iOS und Android

**Typ:** Neues Feature-**Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

**Jetzt in der Public Preview!**

**Intune Managed Browser – SSO:** Ihre Mitarbeiter können einmaliges Anmelden übergreifend für native Clients (wie Microsoft Outlook) und Intune Managed Browser für alle mit Azure AD verbundenen Apps verwenden.

**Intune Managed Browser – Unterstützung von bedingtem Zugriff:** Mithilfe von Richtlinien für anwendungsbasierten bedingten Zugriff können Sie nun für Mitarbeiter die Verwendung von Intune Managed Browser erzwingen.

Weitere Informationen hierzu finden Sie in unserem [Blogbeitrag](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Weitere Informationen finden Sie unter

- [Einrichten des app-basierten bedingten Zugriffs](../conditional-access/app-based-conditional-access.md)

- [Verwalten des Internetzugriffs mittels Richtlinien für Managed Browser mit Microsoft Intune](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App-Proxy-Cmdlets im allgemein verfügbaren PowerShell-Modul

**Typ:** Neues Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Zugriffssteuerung

Das allgemein verfügbare PowerShell-Modul bietet jetzt Unterstützung für Anwendungsproxy-Cmdlets! Das erfordert, dass Sie bei PowerShell-Modulen auf dem aktuellen Stand sein müssen. Wenn Sie mehr als ein Jahr in Rückstand geraten, funktionieren einige Module möglicherweise nicht mehr.

Weitere Informationen finden Sie unter [AzureAD](/powershell/module/Azuread/).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Native Office 365-Clients werden vom nahtlosen einmaligen Anmelden mithilfe eines nicht interaktiven Protokolls unterstützt

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Benutzer, die native Office 365-Clients (Version 16.0.8730.xxxx und höher) einsetzen, genießen mit nahtlosem einmaligem Anmelden eine Anmeldung ohne Benutzereingriff. Diese Unterstützung wird durch die Ergänzung von Azure AD durch ein nicht interaktives Protokoll (WS-Trust) bereitgestellt.

Weitere Informationen finden Sie unter [Wie funktioniert die Anmeldung auf einem nativen Client mit dem nahtlosen einmaligen Anmelden?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work).

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Benutzer kommen mit dem nahtlosen einmaligen Anmelden in den Genuss einer Anmeldung ohne Benutzereingriff, wenn eine Anwendung Anmeldeanforderungen an Azure AD-Mandantenendpunkte sendet

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Benutzer erhalten mit dem nahtlosen einmaligen Anmelden eine Anmeldung ohne Benutzereingriff, wenn eine Anwendung (z.B. `https://contoso.sharepoint.com`) Anmeldeanforderungen nicht mehr an den allgemeinen Azure AD-Endpunkt (`https://login.microsoftonline.com/common/<...>`) sendet, sondern an Mandantenendpunkte von Azure AD – d.h. `https://login.microsoftonline.com/contoso.com/<..>` oder `https://login.microsoftonline.com/<tenant_ID>/<..>`.

Weitere Informationen finden Sie unter [Nahtlose einmalige Anmeldung mit Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Für das Rollout des nahtlosen einmaligen Anmeldens muss den Intranet-Zoneneinstellungen des Benutzers nur noch eine Azure AD-URL statt zwei wie bisher hinzugefügt werden

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Für das Rollout des nahtlosen einmaligen Anmeldens für Ihre Benutzer müssen Sie den Intranet-Zoneneinstellungen Ihrer Benutzer nur eine Azure AD-URL mithilfe einer Gruppenrichtlinie in Active Directory hinzufügen: `https://autologon.microsoftazuread-sso.com`. Bisher mussten Kunden zwei URLs hinzufügen.

Weitere Informationen finden Sie unter [Nahtlose einmalige Anmeldung mit Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im März 2018 haben wir diese 15 neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), Assistant von FirstAgenda, [YardiOne](../saas-apps/yardione-tutorial.md), Vtiger CRM, inwink, [Amplitude](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md), [Bersin](../saas-apps/bersin-tutorial.md), [Mercell](../saas-apps/mercell-tutorial.md), [Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md).

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md).

Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM für Azure-Ressourcen ist allgemein verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Privileged Identity Management-**Produktfunktion:** Privileged Identity Management

Wenn Sie Azure AD Privileged Identity Management für Verzeichnisrollen verwenden, können Sie jetzt die PIM-Funktionen für zeitgesteuerten Zugriff und Zuweisungen für Azure-Ressourcenrollen verwenden, wie etwa Abonnements, Ressourcengruppen, virtuelle Computer und beliebige andere vom Azure Resource Manager unterstützte Ressourcen. Erzwingen der Multi-Factor Authentication bei der Just-In-Time-Aktivierung von Rollen und Planen von Aktivierungen in Abstimmung mit genehmigten Änderungsfenstern. Darüber hinaus wurden in dieser Version Verbesserungen hinzugefügt, die in der öffentlichen Vorschau nicht verfügbar waren, darunter eine aktualisierte Benutzeroberfläche, Genehmigungsworkflows und die Möglichkeit zur Verlängerung bald ablaufender Rollen und zur Erneuerung abgelaufener Rollen.

Weitere Informationen finden Sie unter [PIM für Azure-Ressourcen (Vorschauversion)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Hinzufügen von optionalen Ansprüchen zu App-Token (öffentliche Vorschau)

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Ihre Azure AD-App kann jetzt benutzerdefinierte oder optionale Ansprüche in JWTs oder SAML-Token anfordern.  Dabei handelt es sich um den Benutzer oder Mandanten betreffende Ansprüche, die aufgrund von Größen- oder Anwendbarkeitseinschränkungen nicht standardmäßig im Token enthalten sind.  Diese Funktion befindet sich aktuell in der öffentlichen Vorschau für Azure AD-Apps v1.0- und v2.0-Endpunkten.  Informationen zu den Ansprüchen, die hinzugefügt werden können, und zum Bearbeiten Ihres Anwendungsmanifests für ihre Anforderung finden Sie in der Dokumentation.

Weitere Informationen finden Sie unter [Optionale Ansprüche in Azure AD](../develop/active-directory-optional-claims.md) (Optionale Ansprüche in Azure AD).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD unterstützt PKCE für sicherere OAuth-Flows

**Typ:** Neues Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Die Azure AD-Dokumentation wurde um Unterstützung für PKCE ergänzt, das eine sicherere Kommunikation beim Gewährungsflow des OAuth 2.0-Autorisierungscodes ermöglicht.  An v1.0- und v2.0-Endpunkten werden sowohl S256- als auch Klartext-Abfragecodes unterstützt.

Weitere Informationen finden Sie unter [Anfordern eines Autorisierungscodes](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code).

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Unterstützung der Bereitstellung aller in der Workday Get_Workers-API verfügbaren Benutzerattributwerte

**Typ:** Neues Feature-**Dienstkategorie:** App-Bereitstellungs-**Produktfunktion:** Integration von Drittanbieterprodukten

Die öffentliche Vorschau der eingehenden Bereitstellung von Workday in Active Directory und Azure AD unterstützt jetzt die Funktion zum Extrahieren und Bereitstellen aller in der Workday Get_Workers-API verfügbaren Attributwerte. Dadurch wird jenseits der in der ursprünglichen Version des Workday-Connectors für eingehende Bereitstellung mitgelieferten Attribute Unterstützung für Hunderte weiterer Standard- und benutzerdefinierter Attribute hinzugefügt.

Weitere Informationen finden Sie unter [Anpassen der Liste der Workday-Benutzerattribute](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Ändern der Gruppenmitgliedschaft von dynamisch in statisch (und umgekehrt)

**Typ:** Neues Feature-**Dienstkategorie:** Gruppenverwaltungs-**Produktfunktion:** Zusammenarbeit

Sie können ändern, wie die Mitgliedschaft in einer Gruppe verwaltet wird. Dies ist hilfreich, wenn Sie im System den gleichen Gruppennamen und die dazugehörige ID beibehalten möchten, damit alle vorhandenen Verweise auf die Gruppe weiterhin gültig sind. Wenn eine neue Gruppe erstellt wird, müssen diese Verweise aktualisiert werden.
Wir haben das Azure AD Admin Center um Unterstützung für diese Funktionalität erweitert. Kunden können jetzt vorhandene Gruppen von dynamischer Mitgliedschaft auf zugewiesene Mitgliedschaft umstellen und umgekehrt. Die vorhandenen PowerShell-Cmdlets stehen ebenfalls weiterhin zur Verfügung.

Weitere Informationen finden Sie unter [Regeln für eine dynamische Mitgliedschaft für Gruppen in Azure Active Directory](../enterprise-users/groups-dynamic-membership.md).

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Verbessertes Abmeldeverhalten bei nahtlosem einmaligem Anmelden

**Typ:** Geändertes Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Selbst wenn sie sich explizit bei einer durch Azure AD geschützten Anwendung abgemeldet hatten, wurden Benutzer bei einem erneuten Zugriffsversuch auf eine Azure AD-Anwendung aus dem Unternehmensnetzwerk von in der Domäne registrierten Geräten aus bisher mithilfe von nahtlosem einmaligem Anmelden automatisch wieder angemeldet. Mit dieser Änderung wird die Abmeldung unterstützt.  Dadurch können Benutzer für die erneute Anmeldung das gleiche oder ein anderes Azure AD-Konto auswählen, statt mithilfe von nahtlosem Anmelden automatisch angemeldet zu werden.

Weitere Informationen finden Sie unter [Nahtlose einmalige Anmeldung mit Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="application-proxy-connector-version-154020-released"></a>Anwendungsproxyconnector Version 1.5.402.0 veröffentlicht

**Typ:** Geändertes Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Identitätssicherheit und -schutz

Das Rollout dieser Connectorversion erfolgt nach und nach im Lauf des Novembers. Diese neue Connectorversion enthält die folgenden Änderungen:

- Der Connector legt jetzt Cookies auf Domänen- statt auf Unterdomänenebene fest. Dadurch wird ein reibungsloseres Verhalten beim einmaligen Anmelden sichergestellt, und redundante Authentifizierungsaufforderungen werden vermieden.
- Unterstützung für aufgeteilte Codierungsanforderungen
- Verbesserte Connector-Integritätsüberwachung
- Verschiedene behobene Programmfehler und verbesserte Stabilität

Weitere Informationen finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](../manage-apps/application-proxy-connectors.md).

---

## <a name="february-2018"></a>Februar 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Verbesserte Navigation zum Verwalten von Benutzern und Gruppen

**Typ:** Plan für Änderung der **Dienstkategorie:** Verzeichnisverwaltungs-**Produktfunktion:** Verzeichnis

Die Navigationserfahrung zum Verwalten von Benutzern und Gruppen wurde optimiert. Sie können nun von der Verzeichnisübersicht aus direkt zur Liste aller Benutzer navigieren, um einfacher auf die Liste der gelöschten Benutzer zugreifen zu können. Sie können auch aus der Verzeichnisübersicht direkt zur Liste aller Gruppen navigieren, was den Zugriff auf die Einstellungen der Gruppenverwaltung erleichtert. Und von der Verzeichnisübersichtsseite aus können Sie außerdem nach einem Benutzer, einer Gruppe, einer Unternehmensanwendung oder einer App-Registrierung suchen.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Verfügbarkeit von Anmeldungen und Überwachungsberichten in Microsoft Azure, betrieben von 21Vianet (Azure China 21Vianet)

**Typ:** Neues Feature-**Dienstkategorie:** Azure Stack-**Produktfunktion:** Überwachung und Berichterstellung

Azure AD-Aktivitätsprotokollberichte sind nun in Microsoft Azure-Instanzen verfügbar, betrieben von 21Vianet (Azure China 21Vianet). Die folgenden Protokolle sind enthalten:

- **Aktivitätsprotokolle zu Anmeldevorgängen**: Umfasst alle Anmeldungsprotokolle, die Ihrem Mandanten zugeordnet sind.

- **Self-Service-Kennwortüberwachungsprotokolle**: Umfasst alle SSPR-Überwachungsprotokolle.

- **Verzeichnisverwaltungs-Überwachungsprotokolle**: Umfasst alle mit der Verzeichnisverwaltung zusammenhängenden Überwachungsprotokolle wie Benutzerverwaltung, App-Verwaltung und andere.

Mit diesen Protokollen können Sie Einblicke in den Zustand Ihrer Umgebung gewinnen. Die bereitgestellten Daten ermöglichen Ihnen Folgendes:

- Ermitteln, wie Ihre Apps und Dienste von Ihren Benutzern genutzt werden.

- Behandeln von Problemen, die Ihre Benutzer an der Erledigung ihrer Arbeit hindern.

Weitere Informationen zum Verwenden dieser Berichte finden Sie unter [Azure Active Directory-Berichterstellung](../reports-monitoring/overview-reports.md).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Verwenden der Rolle „Berichtsleser“ (Nicht-Administratorrolle) zum Anzeigen von Azure AD-Aktivitätsberichten

**Typ:** Neues Feature-**Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Überwachung und Berichterstellung

Als Teil des Kundenfeedbacks, Nicht-Administratorrollen den Zugriff auf die Azure AD-Aktivitätsprotokolle zu ermöglichen, haben wir eine Möglichkeit geschaffen, dass Benutzer, die die Rolle „Berichtsleser“ ausüben, auf die Anmelde- und Überwachungsaktivitäten im Azure-Portal sowie über die Microsoft Graph-API zugreifen können.

Weitere Informationen zum Verwenden dieser Berichte finden Sie unter [Azure Active Directory-Berichterstellung](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID-Anspruch als Benutzerattribut und Benutzer-ID verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** SSO

Sie können **EmployeeID** als Benutzerbezeichner und Benutzerattribut für Mitgliederbenutzer und B2B-Gäste in SAML-basierten Anmeldeanwendungen von der Benutzeroberfläche der Unternehmensanwendung aus konfigurieren.

Weitere Informationen finden Sie unter [Anpassen ausgestellter Ansprüche im SAML-Token für Unternehmensanwendungen in Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Vereinfachte Anwendungsverwaltung mithilfe von Platzhaltern im Azure AD-Anwendungsproxy

**Typ:** Neues Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Benutzerauthentifizierung

Um die Anwendungsbereitstellung zu vereinfachen und Ihren Verwaltungsaufwand zu verringern, unterstützen wir nun die Möglichkeit, Anwendungen mithilfe von Platzhaltern zu veröffentlichen. Um eine Platzhalteranwendung zu veröffentlichen, können Sie dem Standardablauf bei der Veröffentlichung von Anwendungen folgen, aber einen Platzhalter in den internen und externen URLs verwenden.

Weitere Informationen finden Sie unter [Platzhalteranwendungen im Azure Active Directory-Anwendungsproxy](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Neue Cmdlets zur Unterstützung der Konfiguration des Anwendungsproxys

**Typ:** Neues Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Plattform

Das neueste Release der Vorschau des Moduls AzureAD PowerShell Preview enthält neue Cmdlets, die es Kunden ermöglichen, Anwendungsproxyanwendungen mithilfe von PowerShell zu konfigurieren.

Die folgenden Cmdlets sind neu:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Neue Cmdlets zur Unterstützung der Konfiguration von Gruppen

**Typ:** Neues Feature-**Dienstkategorie:** App-Proxy-**Produktfunktion:** Plattform

Das neueste Release des AzureAD PowerShell-Moduls enthält Cmdlets zum Verwalten von Gruppen in Azure AD. Diese Cmdlets waren zuvor im AzureADPreview-Modul verfügbar und werden nun dem AzureAD-Modul hinzugefügt.

Folgende Gruppen-Cmdlets werden nun für die allgemeine Verfügbarkeit freigegeben:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Ein neues Release von Azure AD Connect ist verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** AD Sync-**Produktfunktion:** Plattform

Azure AD Connect ist das bevorzugte Tool zur Synchronisierung von Daten zwischen Azure AD und lokalen Datenquellen, einschließlich Windows Server Active Directory und LDAP.

>[!Important]
>Bei diesem Build wurden Änderungen an Schemas und Synchronisierungsregeln vorgenommen. Der Azure AD Connect-Synchronisierungsdienst löst nach einem Upgrade die Schritte „Vollständiger Import“ und „Vollständige Synchronisierung“ aus. Informationen zum Ändern dieses Verhaltens finden Sie unter [Zurückstellen der vollständigen Synchronisierung nach dem Upgrade](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).

Dieses Release enthält die folgenden Updates und Änderungen:

**Behobene Probleme**

- Es wurde ein Problem mit dem Zeitfenster für Hintergrundaufgaben auf der Seite „Partitionsfilterung“ behoben, das beim Wechsel auf die nächste Seite entstand.

- Es wurde ein Problem behoben, das während einer benutzerdefinierten ConfigDB-Aktion zu einer Zugriffsverletzung führte.

- Es wurde ein Problem mit der Wiederherstellung nach einem SQL-Verbindungstimeout behoben.

- Es wurde ein Problem behoben, bei dem Zertifikate mit SAN-Platzhaltern eine Voraussetzungsprüfung nicht bestanden.

- Es wurde ein Problem behoben, das zu einem Absturz von „miiserver.exe“ während eines Azure AD-Connectorexports führte.

- Es wurde ein Problem mit ungültigen Kennworteingabeversuchen behoben, die protokolliert wurden, wenn die Ausführung eine Änderung der Konfiguration durch den Azure AD-Assistenten zum Herstellen von Verbindungen verursachte.

**Neue Features und Verbesserungen**

- Anwendungstelemetrie: Administratoren können diese Datenklasse aktivieren/deaktivieren.

- Azure AD-Integritätsdaten: Administratoren müssen das Integritätsportal öffnen, um die Integritätseinstellungen zu steuern. Sobald die Dienstrichtlinie geändert wurde, wird sie von den Agents gelesen und erzwungen.

- Zusätzliche Aktionen zum Konfigurieren des Geräterückschreibens und eine Statusanzeige für die Seiteninitialisierung.

- Verbesserte allgemeine Diagnose mit HTML-Berichten und vollständiger Datenerfassung in einem ZIP-Text- bzw. HTML-Bericht.

- Die Zuverlässigkeit des automatischen Upgrades wurde verbessert, und es wurden zusätzliche Telemetriedaten hinzugefügt, um sicherzustellen, dass die Integrität des Servers ermittelt werden kann.

- Eingeschränkte Berechtigungen für privilegierte Konten im AD Connector-Konto. Bei Neuinstallationen schränkt der Assistent die Berechtigungen ein, über die privilegierte Konten im MSOL-Konto verfügen, nachdem das MSOL-Konto erstellt wurde. Die Änderungen betreffen Express-Installationen und benutzerdefinierte Installationen mit automatischer Kontoerstellung.

- Das Installationsprogramm wurde so geändert, dass bei einer Neuinstallation von AAD Connect keine SA-Berechtigung mehr erforderlich ist.

- Neues Hilfsprogramm, um Synchronisierungsprobleme für ein bestimmtes Objekt zu beheben. Zurzeit überprüft das Hilfsprogramm Folgendes:

    - Diskrepanz beim UserPrincipalName zwischen dem synchronisierten Benutzerobjekt und dem Benutzerkonto im Azure AD-Mandanten.

    - Ausschluss des Objekts von der Synchronisierung aufgrund der Domänenfilterung.

    - Ausschluss des Objekts von der Synchronisierung aufgrund der Filterung nach Organisationseinheit.

- Neues Hilfsprogramm, um den aktuellen Kennworthash zu synchronisieren, der im lokalen Active Directory-Verzeichnis für ein bestimmtes Benutzerkonto gespeichert ist. Das Hilfsprogramm erfordert keine Kennwortänderung.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Anwendungen, die die Intune App-Schutzrichtlinien unterstützen, wurden für die Verwendung mit anwendungsbasiertem bedingten Azure AD-Zugriff hinzugefügt

**Typ:** Geändertes Feature-**Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Wir haben weitere Anwendungen hinzugefügt, die anwendungsbasierten bedingten Zugriff unterstützen. Jetzt können Sie Zugriff auf Office 365 und andere mit Azure AD verbundene Cloudanwendungen erhalten, indem Sie diese genehmigten Client-Apps verwenden.

Die folgenden Anwendungen werden bis Ende Februar hinzugefügt:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Weitere Informationen finden Sie unter

- [Genehmigte Client-App als Voraussetzung](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [App-basierter bedingter Zugriff mit Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Update der Nutzungsbedingungen für Mobilgeräte

**Typ:** Geändertes Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Compliance

Wenn die Nutzungsbedingungen angezeigt werden, können Sie nun auf Folgendes klicken: **Probleme mit der Anzeige? Klicken Sie hier**. Durch Klicken auf diesen Link werden die Nutzungsbedingungen nativ auf Ihrem Gerät geöffnet. Unabhängig vom Schriftgrad des Dokuments oder der Bildschirmgröße des Geräts können Sie das Dokument bei Bedarf zoomen und lesen.

---

## <a name="january-2018"></a>Januar 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Neue Verbund-Apps im Azure AD-App-Katalog verfügbar

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Januar 2018 wurden die folgenden neuen Apps mit Verbundunterstützung im App-Katalog hinzugefügt:

[IBM OpenPages](../saas-apps/ibmopenpages-tutorial.md), [OneTrust Privacy Management Software](../saas-apps/onetrust-tutorial.md), [Dealpath](../saas-apps/dealpath-tutorial.md), IriusRisk Federated Directory und [Fidelity NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md).

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md).

Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="sign-in-with-additional-risk-detected"></a>Anmeldung mit erhöhtem Risiko erkannt

**Typ:** Neues Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

Welche Informationen Sie zu einer erkannten Risikoerkennung erhalten, hängt von Ihrem Azure AD-Abonnement ab. Die Azure AD Premium P2-Edition bietet die ausführlichsten Informationen zu allen zugrunde liegenden erkannten Ereignissen.

Bei der Azure AD Premium P1-Edition werden Erkennungen, die nicht durch Ihre Lizenz abgedeckt sind, als Risikoerkennung „Anmeldung mit erhöhtem Risiko erkannt“ angezeigt.

Weitere Informationen finden Sie unter [Azure Active Directory risk detections](../identity-protection/overview-identity-protection.md) (Azure Active Directory-Risikoerkennungen).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ausblenden von Office 365-Anwendungen in Zugriffsbereichen von Endbenutzern

**Typ:** Neues Feature-**Dienstkategorie:** Meine Apps-**Produktfunktion:** SSO

Durch eine neue Benutzereinstellung können Sie jetzt besser steuern, wie Office 365-Anwendungen in den Zugriffsbereichen Ihrer Benutzer angezeigt werden. Diese Option ist hilfreich, um die Anzahl der Apps in den Zugriffsbereichen eines Benutzers zu reduzieren, sofern nur Office-Apps im Office-Portal anzeigen werden sollen. Die Einstellung befindet sich in den **Benutzereinstellungen** und lautet **Benutzer können Office 365-Apps nur im Office 365-Portal anzeigen**.

Weitere Informationen finden Sie unter [Ausblenden einer Anwendung auf der Benutzeroberfläche in Azure Active Directory](../manage-apps/hide-application-from-user-portal.md).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Nahtloses Anmelden bei Apps mit direkt über die App-URL aktiviertem Kennwort-SSO

**Typ:** Neues Feature-**Dienstkategorie:** Meine Apps-**Produktfunktion:** SSO

Die Browsererweiterung „Meine Apps“ ist jetzt über ein nützliches Tool verfügbar, mit dem Sie die Funktion zum einmaligen Anmelden „Meine Apps“ als Verknüpfung in Ihrem Browser verwenden können. Nach der Installation wird dem Benutzer ein Waffelsymbol im Browser angezeigt, das Schnellzugriff auf Apps bietet. Benutzer können ab sofort von folgenden Vorteilen profitieren:

- Die Möglichkeit, sich direkt bei Apps per Kennwort-SSO über die Anmeldeseite der App anzumelden
- Starten einer App mithilfe der Schnellsuchfunktion
- Verknüpfungen für zuletzt verwendete Apps über die Erweiterung
- Die Erweiterung ist für Microsoft Edge, Chrome und Firefox verfügbar.

Weitere Informationen finden Sie unter [Erweiterung zur sicheren Anmeldung bei „Meine Apps“](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Die Azure AD-Verwaltungsoberfläche im klassischen Azure-Portal wurde eingestellt

**Typ:** Veraltete **Dienstkategorie:** Azure AD-**Produktfunktion:** Verzeichnis

Die Azure AD-Administratoroberfläche im klassischen Azure-Portal ist seit dem 8. Januar 2018 eingestellt. Diese Änderung fand im Zuge der Einstellung des klassischen Azure-Portals selbst statt. Für sämtliche portalbasierte Verwaltungsaufgaben von Azure AD müssen Sie künftig das [Azure AD Admin Center](https://aad.portal.azure.com) verwenden.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Das PhoneFactor-Webportal wurde eingestellt

**Typ:** Veraltete **Dienstkategorie:** Azure AD-**Produktfunktion:** Verzeichnis

Am 8. Januar 2018 wurde das PhoneFactor-Webportal eingestellt. Die Funktionen des Portals, nämlich die Verwaltung des MFA-Servers, wurden zum Azure-Portal unter portal.azure.com migriert.

Die MFA-Konfiguration befindet sich unter: **Azure Active Directory \> MFA Server**

---

### <a name="deprecate-azure-ad-reports"></a>Einstellung von Azure AD-Berichten

**Typ:** Veraltete **Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Identity Lifecycle Management


Im Zuge der allgemeinen Verfügbarkeit der neuen Azure Active Directory-Verwaltungskonsole und der neuen APIs, die ab sofort für Aktivitäts- und Sicherheitsberichte verfügbar sind, wurden die Berichts-APIs auf dem Endpunkt unter „/reports“ am 31. Dezember 2017 eingestellt.

**Was ist verfügbar?**

Im Rahmen der Umstellung auf die neue Verwaltungskonsole haben wir zwei neue APIs zum Abrufen von Azure AD-Aktivitätsprotokollen zur Verfügung gestellt. Die neuen APIs bieten umfangreichere Filter- und Sortierfunktionen sowie mehr Überprüfungs- und Anmeldeaktivitäten. Auf die Daten, die bisher in den Sicherheitsberichten verfügbar waren, kann jetzt über die API für Identity Protection-Risikoerkennungen in Microsoft Graph zugegriffen werden.

Weitere Informationen finden Sie unter

- [Erste Schritte mit der Berichterstellungs-API von Azure Active Directory](../reports-monitoring/concept-reporting-api.md)

- [Erste Schritte mit Azure Active Directory Identity Protection und Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>Dezember 2017

### <a name="terms-of-use-in-the-access-panel"></a>Nutzungsbedingungen im Zugriffsbereich

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Compliance

Ab sofort können Sie die von Ihnen zuvor akzeptierten Nutzungsbedingungen im Zugriffsbereich einsehen.

Folgen Sie diesen Schritten:

1. Navigieren Sie zum [MyApps-Portal](https://myapps.microsoft.com), und melden Sie sich an.

2. Wählen Sie oben rechts Ihren Namen und dann in der Liste **Profil** aus.

3. Klicken Sie in Ihrem **Profil** auf **Nutzungsbedingungen lesen**.

4. Nun können Sie sich die akzeptierten Nutzungsbedingungen ansehen.

Weitere Informationen finden Sie unter [Nutzungsbedingungsfeature (Vorschauversion) für Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="new-azure-ad-sign-in-experience"></a>Neuer Anmeldevorgang für Azure AD

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD-**Produktfunktion:** Benutzerauthentifizierung

Die Benutzeroberfläche des Azure AD- und Microsoft-Kontoidentitätssystems wurden hinsichtlich eines konsistenten Aussehens und Verhaltens neu gestaltet. Darüber hinaus erfasst die Azure AD-Anmeldeseite zuerst den Benutzernamen, gefolgt von den Anmeldeinformationen auf einem zweiten Bildschirm.

Weitere Informationen finden Sie unter [The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (Die neue Umgebung für die Azure AD-Anmeldung ist jetzt als Public Preview verfügbar).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Weniger Anmeldeeingabeaufforderungen: Eine neue „Angemeldet bleiben“-Umgebung für die Azure AD-Anmeldung

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD-**Produktfunktion:** Benutzerauthentifizierung

Das Kontrollkästchen **Angemeldet bleiben** auf der Azure AD-Anmeldeseite wurde durch eine neue Eingabeaufforderung ersetzt, die nach erfolgreicher Authentifizierung angezeigt wird.

Wenn Sie für diese Eingabeaufforderung **Ja** auswählen, erhalten Sie vom Dienst ein beständiges Aktualisierungstoken. Dies ist das gleiche Verhalten wie beim Aktivieren des Kontrollkästchens **Angemeldet bleiben** in der alten Umgebung. Bei Verbundmandanten wird diese Eingabeaufforderung angezeigt, nachdem Sie sich erfolgreich bei dem Verbunddienst authentifiziert haben.

Weitere Informationen finden Sie unter [Weniger Anmeldeeingabeaufforderungen: Die neue „Angemeldet bleiben“-Umgebung für Azure AD befindet sich in der Vorschauphase](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Hinzufügen einer Konfiguration, um vor dem Akzeptieren das Erweitern der Nutzungsbedingungen zu erfordern

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Compliance

Eine Option für Administratoren wurde hinzugefügt, mit der Benutzer aufgefordert werden können, die Nutzungsbedingungen vor dem Akzeptieren zu erweitern.

Wählen Sie **Ein** oder **Aus** aus, um die Erweiterung der Nutzungsbedingungen durch den Benutzer zu erfordern. Bei der Einstellung **Ein** wird vorausgesetzt, dass sich Benutzer diese vor dem Akzeptieren der Nutzungsbedingungen ansehen.

Weitere Informationen finden Sie unter [Nutzungsbedingungsfeature (Vorschauversion) für Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Bereichsbezogene Aktivierung für berechtigte Rollenzuweisungen

**Typ:** Neues Feature-**Dienstkategorie:** Privileged Identity Management-**Produktfunktion:** Privileged Identity Management

Mit der bereichsbezogenen Aktivierung können Sie berechtigte Rollenzuweisungen für Azure-Ressourcen mit weniger Autonomie aktivieren, als die ursprünglichen Standardwerte für die Zuweisung vorsehen. Beispielsweise wird Ihnen „Besitzer“ für ein Abonnement in Ihrem Mandanten zugewiesen. Mit der bereichsbezogenen Aktivierung können Sie die Besitzerrolle für bis zu fünf innerhalb des Abonnements enthaltene Ressourcen aktivieren (z.B. für Ressourcengruppen und virtuelle Computer). Durch Festlegen des Gültigkeitsbereichs der Aktivierung kann die Wahrscheinlichkeit verringert werden, dass unerwünschte Änderungen an wichtigen Azure-Ressourcen vorgenommen werden.

Weitere Informationen finden Sie unter [Was ist Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Neue Verbund-Apps im Azure AD-App-Katalog

**Typ:** Neues Feature-**Dienstkategorie:** Enterprise Apps-**Produktfunktion:** Integration von Drittanbieterprodukten

Im Dezember 2017 haben wir diese neuen Apps mit Verbundunterstützung in unseren App-Katalog aufgenommen:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital StoreFront](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet](../saas-apps/factset-tutorial.md), [IMAGE WORKS](../saas-apps/imageworks-tutorial.md), [MOBI](../saas-apps/mobi-tutorial.md), [MobileIron Azure AD-Integration](../saas-apps/mobileiron-tutorial.md), [Reflektive](../saas-apps/reflektive-tutorial.md), [SAML SSO for Bamboo by resolution GmbH](../saas-apps/bamboo-tutorial.md), [SAML SSO for Bitbucket by resolution GmbH](../saas-apps/bitbucket-tutorial.md), [Vodeclic](../saas-apps/vodeclic-tutorial.md), WebHR, Zenegy Azure AD-Integration.

Weitere Informationen zu den Apps finden Sie unter [SaaS-Anwendungsintegration mit Azure Active Directory](../saas-apps/tutorial-list.md).

Weitere Informationen zum Listen Ihrer Anwendung im Azure AD-App-Katalog finden Sie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Genehmigungsworkflows für Azure AD-Verzeichnisrollen

**Typ:** Geändertes Feature-**Dienstkategorie:** Privileged Identity Management-**Produktfunktion:** Privileged Identity Management

Ein Genehmigungsworkflow für Azure AD-Verzeichnisrollen ist allgemein verfügbar.

Mit dem Genehmigungsworkflow können Administratoren für privilegierte Rollen festlegen, dass berechtigte Rollenmitglieder die Rollenaktivierung anfordern müssen, bevor sie die privilegierte Rolle verwenden können. Es können Genehmigungsverantwortlichkeiten an verschiedene Benutzer und Gruppen delegiert werden. Berechtigte Rollenmitglieder erhalten eine Benachrichtigung, wenn die Genehmigung abgeschlossen und ihre Rolle aktiv ist.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Passthrough-Authentifizierung: Unterstützung von Skype for Business

**Typ:** Geändertes Feature-**Dienstkategorie:** Authentifizierungen-**Produktfunktion** (Anmeldungen): Benutzerauthentifizierung

Die Pass-Through-Authentifizierung unterstützt jetzt Benutzeranmeldungen bei Skype for Business-Clientanwendungen, die die moderne Authentifizierung, einschließlich Online- und Hybridtopologien, unterstützen.

Weitere Informationen finden Sie unter [Mit moderner Authentifizierung unterstützte Skype for Business-Topologien](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Updates für die Azure RBAC in Azure AD Privileged Identity Management (Vorschauversion)

**Typ:** Geändertes Feature-**Dienstkategorie:** Privileged Identity Management-**Produktfunktion:** Privileged Identity Management

Mit der aktualisierten Public Preview von Azure AD Privileged Identity Management (PIM) für die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) haben Sie nun folgende Möglichkeiten:

* Nicht mehr Verwaltung als nötig
* Erfordern der Genehmigung zum Aktivieren von Ressourcenrollen
* Planen einer zukünftigen Rollenaktivierung, die eine Genehmigung für Azure AD- und Azure-Rollen erfordert

Weitere Informationen finden Sie unter [PIM für Azure-Ressourcen (Vorschauversion)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="november-2017"></a>November 2017

### <a name="access-control-service-retirement"></a>Einstellung des Access Control Service

**Typ:** Plan für Änderung der **Dienstkategorie:** Access Control Service-**Produktfunktion:** Access Control Service

Der Azure Active Directory Access Control (auch als Access Control Service bezeichnet) wird Ende 2018 eingestellt. Weitere Informationen, u.a. ein ausführlicher Zeitplan und allgemeine Migrationsanweisungen, werden in den nächsten Wochen bereitgestellt. Auf dieser Seite können Sie uns Ihre Kommentare oder Fragen zum Access Control Service mitteilen. Ein Teammitglied wird diese dann beantworten.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Beschränkung des Browserzugriffs auf Intune Managed Browser

**Typ:** Plan für Änderung der **Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Sie können den Browserzugriff auf Office 365 und weitere mit Azure AD verbundene Cloud-Apps mithilfe von Intune Managed Browser als genehmigte App einschränken.

Ab sofort können Sie folgende Bedingung für den anwendungsbasierten bedingten Zugriff konfigurieren:

**Client-Apps:** Browser

**Welche Auswirkung hat die Änderung?**

Der Zugriff wird blockiert, wenn Sie diese Bedingung verwenden. Sobald die Vorschauversion verfügbar ist, ist für den gesamten Zugriff die Verwendung der Managed Browser-Anwendung erforderlich.

Achten Sie in kommenden Blogbeiträgen und Anmerkungen zu dieser Version auf Informationen zu dieser Funktionalität.

Weitere Informationen finden Sie unter [Bedingter Zugriff in Azure Active Directory](../conditional-access/overview.md).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Neue genehmigte Client-Apps für den app-basierten bedingten Zugriff mit Azure AD

**Typ:** Plan für Änderung der **Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Die folgenden Apps wurden der Liste der [genehmigten Client-Apps](../conditional-access/concept-conditional-access-conditions.md#client-apps) hinzugefügt:

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Weitere Informationen finden Sie unter

- [Genehmigte Client-App als Voraussetzung](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [App-basierter bedingter Zugriff mit Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Unterstützung von Nutzungsbedingungen für mehrere Sprachen

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Compliance

Administratoren können jetzt neue Nutzungsbedingungen erstellen, die mehrere PDF-Dokumente enthalten. Diese PDF-Dateien können mit einer zugehörigen Sprache gekennzeichnet werden. Benutzern wird dann die PDF-Datei angezeigt, die der Sprache in den Einstellungen entspricht. Falls keine Übereinstimmung vorliegt, wird die Standardsprache angezeigt.

---

### <a name="real-time-password-writeback-client-status"></a>Echtzeitclientstatus beim Kennwortrückschreiben

**Typ:** Neues Feature-**Dienstkategorie:** Self-Service-Kennwortzurücksetzungs-**Produktfunktion:** Benutzerauthentifizierung

Nun können Sie den Status Ihres lokalen Clients für das Kennwortrückschreiben anzeigen. Diese Option steht im Abschnitt **Lokale Integration** auf der Seite [Kennwortzurücksetzung](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) zur Verfügung.

Wenn Verbindungsprobleme bei Ihrem lokalen Client für das Kennwortrückschreiben vorliegen, wird eine Fehlermeldung mit folgenden Informationen angezeigt:

- Informationen dazu, warum keine Verbindung mit dem lokalen Client für das Kennwortrückschreiben möglich ist
- Ein Link zur Dokumentation, die Sie bei der Problembehandlung unterstützt

Weitere Informationen finden Sie unter [Lokale Integration](../authentication/concept-sspr-howitworks.md#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>App-basierter bedingter Zugriff mit Azure AD

**Typ:** Neues Feature-**Dienstkategorie:** Azure AD-**Produktfunktion:** Identitätssicherheit und -schutz

Sie können jetzt über den [App-basierten bedingten Zugriff mit Azure AD](../conditional-access/app-based-conditional-access.md) den Zugriff auf Office 365 und weitere mit Azure AD verbundene Cloud-Apps auf [genehmigte Client-Apps](../conditional-access/concept-conditional-access-conditions.md#client-apps) beschränken, die Intune-App-Schutzrichtlinien unterstützen. Intune-App-Schutzrichtlinien werden verwendet, um Unternehmensdaten in diesen Clientanwendungen zu konfigurieren und zu schützen.

Durch das Kombinieren von [app-basierten](../conditional-access/app-based-conditional-access.md) und [gerätebasierten](../conditional-access/require-managed-devices.md) Richtlinien für den bedingten Zugriff können Sie Daten für private Geräte und Unternehmensgeräte flexibel schützen.

Für den app-basierten bedingten Zugriff stehen ab sofort die folgenden Bedingungen und Steuerelemente zur Verfügung:

**Bedingung für unterstützte Plattform**

- iOS
- Android

**Client-Apps-Bedingung**

- Mobile Apps und Desktop-Apps

**Zugriffssteuerung**

- Genehmigte Client-App erforderlich

Weitere Informationen finden Sie unter [App-basierter bedingter Zugriff mit Azure Active Directory](../conditional-access/app-based-conditional-access.md).

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Verwalten von Azure AD-Geräten im Azure-Portal

**Typ:** Neues Feature-**Dienstkategorie:** Geräteregistrierung und -verwaltung-**Produktfunktion:** Identitätssicherheit und -schutz

Ab jetzt finden Sie alle mit Azure AD verbundenen Geräte sowie die gerätebezogenen Aktivitäten an einem Ort. Es steht eine neue Verwaltungsoberfläche zur Verfügung, mit der Sie all ihre Geräteidentitäten und -einstellungen im Azure-Portal verwalten können. Dieses Release ermöglicht Folgendes:

- Anzeigen all Ihrer Geräte, die für den bedingten Zugriff in Azure AD zur Verfügung stehen.
- Anzeigen von Eigenschaften, einschließlich in Hybrid-Azure AD eingebundenen Geräten
- Auffinden von BitLocker-Schlüsseln für Ihre in Azure AD eingebundenen Geräte, Verwalten Ihrer Geräte mit Intune und vieles mehr
- Verwalten von gerätebezogenen Azure AD-Einstellungen

Weitere Informationen finden Sie unter [Verwalten von Geräten mit dem Azure-Portal](../devices/device-management-azure-portal.md).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Unterstützung der macOS-Geräteplattform für den bedingten Azure AD-Zugriff

**Typ:** Neues Feature-**Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Ab sofort können Sie macOS als Geräteplattformbedingung in Ihre Richtlinie für den bedingten Azure AD-Zugriff einschließen (oder ausschließen). Durch das Hinzufügen von macOS als unterstützte Geräteplattform haben Sie folgende Möglichkeiten:

- **Registrieren und Verwalten von macOS-Geräten mit Intune**: Ähnlich wie für andere Plattformen (z.B. iOS und Android) steht für macOS eine Unternehmensportalanwendung bereit, um einheitliche Registrierungen durchzuführen. Sie können ein Gerät mithilfe der neuen Unternehmensportal-App für macOS bei Intune und Azure AD registrieren.
- **Sicherstellen, dass macOS-Geräte die in Intune definierten Konformitätsrichtlinien Ihrer Organisation einhalten**: In Intune im Azure-Portal können Sie nun Konformitätsrichtlinien für macOS-Geräte einrichten.
- **Beschränken des Zugriffs auf Anwendungen in Azure AD auf ausschließlich konforme macOS-Geräte**: Für die Erstellung bedingter Zugriffsrichtlinien ist macOS als separate Geräteplattformoption festgelegt. Nun können Sie macOS-spezifische Richtlinien für den bedingten Zugriff für die festgelegte Zielanwendung in Azure erstellen.

Weitere Informationen finden Sie unter

- [Erstellen einer Gerätekonformitätsrichtlinie für macOS-Geräte mit Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Erweiterung „Netzwerkrichtlinienserver“ für Azure AD Multi-Factor Authentication

**Typ:** Neues Feature-**Dienstkategorie:**  Mehrstufige Authentifizierung-**Produktfunktion:** Benutzerauthentifizierung

Die Erweiterung „Netzwerkrichtlinienserver“ für Azure AD Multi-Factor Authentication fügt Ihrer Authentifizierungsinfrastruktur unter Verwendung Ihrer vorhandenen Server cloudbasierte Funktionen zur mehrstufigen Authentifizierung hinzu. Mit der Erweiterung „Netzwerkrichtlinienserver“ können Sie zu Ihrem bestehenden Authentifizierungsablauf eine Überprüfung per Telefonanruf, SMS oder Telefon-App hinzufügen. Dabei müssen keine neuen Server installiert, konfiguriert und verwaltet werden.

Diese Erweiterung wurde für Organisationen entwickelt, die VPN-Verbindungen schützen möchten, ohne den Azure Multi-Factor Authentication-Server bereitzustellen. Die Erweiterung „Netzwerkrichtlinienserver“ fungiert als Adapter zwischen RADIUS und cloudbasierter Azure AD Multi-Factor Authentication, um eine zweite Stufe der Authentifizierung für Verbund- oder synchronisierte Benutzer bereitzustellen.

Weitere Informationen finden Sie unter [Integrieren Ihrer vorhandenen NPS-Infrastruktur in Azure AD Multi-Factor Authentication](../authentication/howto-mfa-nps-extension.md).

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Wiederherstellen oder dauerhaftes Entfernen gelöschter Benutzer

**Typ:** Neues Feature-**Dienstkategorie:** Benutzerverwaltungs-**Produktfunktion:** Verzeichnis

Im Azure AD Admin Center haben Sie jetzt folgende Möglichkeiten:

- Wiederherstellen eines gelöschten Benutzers
- Dauerhaftes Löschen eines Benutzers

**So können Sie es ausprobieren**

1. Wählen Sie im Azure AD Admin Center im Abschnitt **Verwalten** die Option [Alle Benutzer](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) aus.

2. Wählen Sie in der Liste **Anzeigen** die Option **Kürzlich gelöschte Benutzer** aus.

3. Wählen Sie mindestens einen kürzlich gelöschten Benutzer aus, und stellen Sie den Benutzer dann entweder wieder her, oder löschen Sie ihn dauerhaft.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Neue genehmigte Client-Apps für den app-basierten bedingten Zugriff mit Azure AD

**Typ:** Geändertes Feature-**Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Die folgenden Apps wurden der Liste der [genehmigten Client-Apps](../conditional-access/concept-conditional-access-conditions.md#client-apps) hinzugefügt:

- Microsoft Planner
- Azure Information Protection

Weitere Informationen finden Sie unter

- [Genehmigte Client-App als Voraussetzung](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [App-basierter bedingter Zugriff mit Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Einfügen von „OR“ zwischen Steuerelementen in einer Richtlinie für den bedingten Zugriff

**Typ:** Geändertes Feature-**Dienstkategorie:** Bedingter Zugriff-**Produktfunktion:** Identitätssicherheit und -schutz

Ab sofort können Sie „OR“ (eines der ausgewählten Steuerelemente ist erforderlich) zwischen Steuerelementen für den bedingten Zugriff einfügen. Durch dieses Feature können Sie Richtlinien mit einem „OR“ zwischen Steuerelementen für den Zugriff erstellen. Beispielsweise können Sie mit diesem Feature eine Richtlinie erstellen, sodass der Benutzer sich entweder über die Multi-Factor Authentication anmelden „OR“ (oder) ein konformes Gerät verwenden muss.

Weitere Informationen finden Sie unter [Steuerelemente beim bedingten Zugriff mit Azure Active Directory](../conditional-access/controls.md).

---

### <a name="aggregation-of-real-time-risk-detections"></a>Aggregation von Echtzeitrisikoerkennungen

**Typ:** Geändertes Feature-**Dienstkategorie:** Identity Protection-**Produktfunktion:** Identitätssicherheit und -schutz

In Azure AD Identity Protection werden jetzt alle Echtzeitrisikoerkennungen, die von derselben IP-Adresse stammen, für einen vorgegebenen Tag für jeden Risikoerkennungstyp aggregiert. Durch diese Änderung werden weniger Risikoerkennungen ohne Änderung der Benutzersicherheit angezeigt.

Die zugrunde liegende Echtzeiterkennung erfolgt bei jeder Benutzeranmeldung. Wenn Sie eine Sicherheitsrichtlinie für das Anmelderisiko auf Multi-Factor Authentication oder eine Blockierung des Zugriffs festgelegt haben, wird diese bei jeder riskanten Anmeldung weiterhin ausgelöst.

---

## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Einstellung von Azure AD-Berichten

**Typ:** Plan für Änderung der **Dienstkategorie:** Berichterstellungs-**Produktfunktion:** Identity Lifecycle Management

Azure-Portal bietet Ihnen Folgendes:

- Eine neue Azure AD-Verwaltungskonsole
- Neue APIs für Aktivitäts- und Sicherheitsberichte

Aufgrund dieser neuen Funktionen werden die Berichts-APIs auf dem Endpunkt unter „/reports“ am 10. Dezember 2017 eingestellt.

---

### <a name="automatic-sign-in-field-detection"></a>Automatische Erkennung von Anmeldefeldern

**Typ:** Feste **Dienstkategorie:** Meine Apps-**Produktfunktion:** Einmaliges Anmelden

Azure AD unterstützt die automatische Erkennung von Anmeldefeldern für Anwendungen, die ein HTML-Benutzernamen- und Kennwortfeld rendern. Diese Schritte sind unter [So erfassen Sie Anmeldefelder für eine Anwendung automatisch](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app) dokumentiert. Sie finden diese Funktion, indem Sie im [Azure-Portal](https://aad.portal.azure.com) auf der Seite **Unternehmensanwendungen** eine *Nicht-Katalog*-Anwendung hinzufügen. Zusätzlich können Sie den Modus **Einmaliges Anmelden** in dieser neuen Anwendung auf **Kennwortbasiertes einmaliges Anmelden** festlegen, indem Sie eine Web-URL eingeben und die Seite anschließend speichern.

Aufgrund eines Dienstproblems war diese Funktion vorübergehend deaktiviert. Das Problem wurde behoben, und die automatische Erkennung des Anmeldefelds ist nun wieder verfügbar.

---

### <a name="new-multi-factor-authentication-features"></a>Neue Multi-Factor Authentication-Features

**Typ:** Neues Feature-**Dienstkategorie:** Mehrstufige Authentifizierung-**Produktfunktion:** Identitätssicherheit und -schutz

Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist ein wesentlicher Bestandteil des Schutzes Ihrer Organisation. Um die Anmeldeinformationen anpassungsfähiger und das Benutzererlebnis nahtloser zu gestalten, wurden die folgenden Features hinzugefügt:

- Die Captcha-Ergebnisse bei der mehrstufigen Authentifizierung sind unmittelbar in den Azure AD-Anmeldebericht integriert, einschließlich des programmgesteuerten Zugriffs auf MFA-Ergebnisse.
- Die MFA-Konfiguration wird stärker in die zentrale Azure AD-Konfigurationsumgebung im Azure-Portal integriert.

Mit dieser öffentlichen Vorschau werden MFA-Verwaltung und -Berichterstellung zu einem integrierten Bestandteil der zentralen Azure AD-Konfigurationsumgebung. Nun können Sie die MFA-Verwaltungsportalfunktionalität in der Azure AD-Oberfläche verwalten.

Weitere Informationen finden Sie in der [Referenz zur Berichterstellung für die mehrstufige Authentifizierung im Azure-Portal](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Nutzungsbedingungen

**Typ:** Neues Feature-**Dienstkategorie:** Nutzungsbedingungen-**Produktfunktion:** Compliance

Mithilfe der Azure AD-Nutzungsbedingungen können Sie Benutzern Informationen wie relevante Haftungsausschlüsse zur Erfüllung gesetzlicher oder Complianceanforderungen zur Verfügung stellen.

Sie können Azure AD-Nutzungsbedingungen in den folgenden Szenarien verwenden:

- Allgemeine Nutzungsbedingungen für alle Benutzer in Ihrer Organisation
- Spezifische Nutzungsbedingungen basierend auf Benutzerattributen (z.B. Ärzte im Vergleich zu Krankenschwestern oder einheimische Mitarbeiter im Vergleich zu ausländischen Mitarbeitern) von dynamischen Gruppen
- Spezifische Nutzungsbedingungen für den Zugriff auf bedeutende Geschäftsanwendungen wie Salesforce

Weitere Informationen finden Sie im [Azure AD B2C](../conditional-access/terms-of-use.md).

---

### <a name="enhancements-to-privileged-identity-management"></a>Verbesserungen an Privileged Identity Management

**Typ:** Neues Feature-**Dienstkategorie:** Privileged Identity Management-**Produktfunktion:** Privileged Identity Management

Mit Azure AD Privileged Identity Management können Sie den Zugriff auf Azure-Ressourcen (Vorschauversion) für folgende Elemente innerhalb Ihrer Organisation verwalten, steuern und überwachen:

- Abonnements
- Ressourcengruppen
- Virtuelle Computer

Alle Ressourcen im Azure-Portal, die die Azure RBAC-Funktion nutzen, können von sämtlichen Sicherheits- und Lebenszyklusverwaltungsfunktionen profitieren, die Azure AD Privileged Identity Management bereitstellt.

Weitere Informationen finden Sie unter [Privileged Identity Management für Azure-Ressourcen](../privileged-identity-management/azure-pim-resource-rbac.md).

---

### <a name="access-reviews"></a>Zugriffsüberprüfungen

**Typ:** Neues Feature-**Dienstkategorie:** Zugriffsüberprüfungen-**Produktfunktion:** Compliance

Organisationen können mithilfe von Zugriffsüberprüfungen (in der Vorschauversion) effizient Gruppenmitgliedschaften und den Zugriff auf Unternehmensanwendungen verwalten:

- Sie können den Gastbenutzerzugriff erneut bestätigen, indem Sie den Zugriff auf Anwendungen und Gruppenmitgliedschaften im Rahmen einer Zugriffsüberprüfung überprüfen. Anhand der Erkenntnisse aus den Zugriffsüberprüfungen können Prüfer effizient entscheiden, ob Gäste weiterhin Zugriff haben sollen.
- Mithilfe von Zugriffsüberprüfungen können Sie den Anwendungszugriff von Mitarbeitern sowie deren Gruppenmitgliedschaften neu zertifizieren.

Die Kontrollen der Zugriffsüberprüfung können in unternehmensrelevanten Programmen zusammengefasst werden, um Überprüfungen für compliance- oder risikokritische Anwendungen nachzuverfolgen.

Weitere Informationen finden Sie unter [Azure AD-Zugriffsüberprüfungen](../governance/access-reviews-overview.md).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Möglichkeit zum Ausblenden von Drittanbieteranwendungen in „Meine Apps“ und im Office 365-App-Startprogramm

**Typ:** Neues Feature-**Dienstkategorie:** Meine Apps-**Produktfunktion:** Einmaliges Anmelden

Mit der neuen Eigenschaft **App ausblenden** können Sie nun Apps, die in Ihren Benutzerportalen angezeigt werden, besser verwalten. Sie können Apps ausblenden für den Fall, dass App-Kacheln für Back-End-Dienste angezeigt werden oder Kacheln doppelt vorhanden sind und die App-Startprogramme von Benutzern überladen. Die Umschaltfläche befindet sich im Abschnitt **Eigenschaften** der Drittanbieter-App und hat die Bezeichnung **Für Benutzer sichtbar?** . Sie können eine App auch programmgesteuert über PowerShell ausblenden.

Weitere Informationen finden Sie unter [Ausblenden einer Anwendung auf der Benutzeroberfläche in Azure Active Directory](../manage-apps/hide-application-from-user-portal.md).


**Was ist verfügbar?**

 Im Rahmen der Umstellung auf die neue Verwaltungskonsole stehen zwei neue APIs zum Abrufen von Azure AD-Aktivitätsprotokollen zur Verfügung. Die neuen APIs bieten umfangreichere Filter- und Sortierfunktionen sowie mehr Überprüfungs- und Anmeldeaktivitäten. Auf die Daten, die bisher in den Sicherheitsberichten verfügbar waren, kann jetzt über die API für Identity Protection-Risikoerkennungen in Microsoft Graph zugegriffen werden.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix für Identity Manager

**Typ:** Geändertes Feature-**Dienstkategorie:** Identity Manager-**Produktfunktion:** Identity Lifecycle Management

Ein Hotfixrollup-Paket (Build 4.4.1642.0) ist ab dem 25. September 2017 für Identity Manager 2016 Service Pack 1 verfügbar. Dieser Rolluppaket enthält Folgendes:

- Problembehebungen und Verbesserungen
- Ein kumulatives Update, das alle Identity Manager 2016 Service Pack 1-Updates bis Build 4.4.1459.0 für Identity Manager 2016 ersetzt
- Identity Manager 2016 Build 4.4.1302.0 wird vorausgesetzt.

Weitere Informationen finden Sie unter [Hotfixrollup-Paket (Build 4.4.1642.0) ist für Identity Manager 2016 Service Pack 1 verfügbar](https://support.microsoft.com/help/4021562).

---
