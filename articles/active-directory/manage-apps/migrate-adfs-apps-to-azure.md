---
title: Verschieben von Apps aus AD FS in Azure AD | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Organisationen Anwendungen in Azure AD verschieben können. Der Schwerpunkt liegt hierbei auf SaaS-Anwendungen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f77e322ffd7eec78fe13650f40c93f914706d557
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824627"
---
# <a name="move-applications-from-ad-fs-to-azure-ad"></a>Verschieben von Anwendungen aus AD FS in Azure AD 

In diesem Artikel erfahren Sie, wie Anwendungen von AD FS in Azure Active Directory (Azure AD) verschoben werden. Der Schwerpunkt liegt auf SaaS-Verbundanwendungen. 

Dieser Artikel enthält keine Schritt-für-Schritt-Anleitung. Es enthält eine konzeptuelle Anleitung zur Durchführung der Migration, indem erläutert wird, wie lokale Konfigurationen in Azure AD übersetzt werden können. Außerdem werden allgemeine Szenarien behandelt.

## <a name="introduction"></a>Einführung

Wenn Sie über ein lokales Verzeichnis mit Benutzerkonten verfügen, ist die Wahrscheinlichkeit hoch, dass Sie mindestens eine oder zwei Apps nutzen. Diese Apps sind so konfiguriert, dass Benutzer darauf zugreifen können, indem sie sich mit diesen Identitäten anmelden.

Wie die meisten anderen Organisationen auch, befinden Sie sich wahrscheinlich auf dem Weg zur Einführung von Cloudanwendungen und -identitäten. Vielleicht funktionieren Office 365 und Azure AD Connect bei Ihnen schon. Vielleicht haben Sie cloudbasierte SaaS-Anwendungen für einige wichtige Workloads eingerichtet, aber noch nicht für alle.  

Viele Organisationen nutzen SaaS- oder benutzerdefinierte Branchen-Apps (Line-of-Business, LOB) im direkten Verbund mit einem lokalen Anmeldedienst, z.B. Active Directory-Verbunddienste (AD FS), neben Office 365- und Azure AD-basierten Apps. In diesem Leitfaden erfahren Sie, warum und wie Sie Ihre Anwendungen in Azure AD verschieben.

>[!NOTE]
>Der Leitfaden enthält ausführliche Informationen zur Konfiguration und Migration von SaaS-Apps sowie allgemeine Informationen zu benutzerdefinierten Branchen-Apps. Eine ausführlichere Anleitung für benutzerdefinierte Branchen-Apps ist für die Zukunft geplant.

![Apps mit direkter lokaler Verbindung](media/migrate-adfs-apps-to-azure/migrate1.png)

![Apps, die per Azure AD-Verbund verknüpft sind](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-moving-apps-to-azure-ad"></a>Gründe für das Verschieben von Apps in Azure AD

Für Organisationen, die bereits AD FS, Ping oder einen anderen lokalen Authentifizierungsanbieter verwenden, hat das Verschieben von Apps in Azure AD folgende Vorteile:

**Höhere Zugriffssicherheit**
- Konfigurieren Sie präzise Zugriffssteuerungen pro Anwendung, einschließlich Azure Multi-Factor Authentication, indem Sie den [bedingten Zugriff in Azure AD](../active-directory-conditional-access-azure-portal.md) verwenden. Die Richtlinien können auf SaaS- und benutzerdefinierte Apps genauso angewendet werden, wie Sie dies ggf. bereits für Office 365 durchführen.
- Zum Erkennen von Bedrohungen und Unterstützen des Anmeldungsschutzes basierend auf Machine Learning und Heuristik zur Identifizierung von risikobehaftetem Datenverkehr können Sie [Azure AD Identity Protection](../active-directory-identityprotection.md) nutzen.

**Azure AD B2B-Zusammenarbeit**
- Nachdem die Anmeldung an SaaS-Apps basierend auf Azure AD eingerichtet wurde, können Sie Partnern per [Azure AD B2B-Zusammenarbeit](../b2b/what-is-b2b.md) Zugriff auf Cloudressourcen gewähren.

**Vereinfachte Verwaltungsoberfläche und zusätzliche Azure AD-Funktionen**
- Azure AD unterstützt als Identitätsanbieter für SaaS-Apps zusätzliche Funktionen, z.B.:
  - Tokensignaturzertifikate pro Anwendung
  - [Konfigurierbare Zertifikatablaufdaten](manage-certificates-for-federated-single-sign-on.md)
  - [Automatisierte Bereitstellung](user-provisioning.md) von Benutzerkonten (in wichtigen Azure Marketplace-Apps) basierend auf Azure AD-Identitäten

**Bewahren der Vorteile eines lokalen Identitätsanbieters**
- Sie können in den Genuss der Azure AD-Vorteile kommen und Ihre lokale Lösung für die Authentifizierung beibehalten. Auf diese Weise bleiben Ihnen Vorteile wie lokale Lösungen für die Multi-Factor Authentication, Protokollierung und Überwachung erhalten. 

**Hilfe beim Außerkraftsetzen des lokalen Identitätsanbieters**
- Organisationen, die das Produkt für die lokale Authentifizierung außer Kraft setzen möchten, ermöglicht die Verschiebung von Apps in Azure AD einen einfacheren Übergang, da dadurch einige Arbeitsschritte entfallen. 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Zuordnen von lokalen App-Typen zu Apps in Azure AD
Die meisten Apps können basierend auf dem verwendeten Anmeldetyp einigen bestimmten Kategorien zugeordnet werden. Anhand dieser Kategorien wird bestimmt, wie die App in Azure AD dargestellt wird.

Kurz ausgedrückt: SAML 2.0-Anwendungen können entweder über den Azure AD-Anwendungskatalog auf dem Marketplace oder als Anwendungen in Azure AD integriert werden, die nicht über den Marketplace angeboten werden. Apps, für die OAuth 2.0 oder OpenID Connect verwendet wird, können auf ähnliche Weise als *App-Registrierungen* in Azure AD integriert werden. Im Folgenden erhalten Sie ausführlichere Informationen.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Vergleich von SaaS-Verbund-Apps und benutzerdefinierten Branchen-Apps
Verbund-Apps sind Apps, die in die hier aufgeführten Kategorien fallen:

- SaaS-Apps 
    - Wenn sich Ihre Benutzer an SaaS-Apps anmelden, z.B. Salesforce, ServiceNow oder Workday, und Sie die Integration in einen lokalen Identitätsanbieter wie AD FS oder Ping durchführen, verwenden Sie die Verbundanmeldung für SaaS-Apps.
    - Im Allgemeinen nutzen Apps das SAML 2.0-Protokoll für die Verbundanmeldung.
    - Apps, die in diese Kategorie fallen, können als Unternehmensanwendungen in Azure AD integriert werden – entweder über den Marketplace oder als Marketplace-externe Anwendungen.
- Benutzerdefinierte Branchen-Apps
    - Hiermit sind Nicht-SaaS-Apps gemeint, die intern von Ihrer Organisation entwickelt wurden oder als Standardpaketprodukt verfügbar sind, das in Ihrem Rechenzentrum installiert wird. Hierin sind auch SharePoint-Apps und per Windows Identity Foundation erstellte Apps enthalten.
    - Für Apps kann SAML 2.0, Webdiensteverbund, OAuth oder OpenID Connect für die Verbundanmeldung verwendet werden.
    - Benutzerdefinierte Apps, für die OAuth 2.0, OpenID Connect oder der WS-Verbund verwendet werden, können in Azure AD als App-Registrierungen integriert werden. Benutzerdefinierte Apps, für die SAML 2.0 oder der WS-Verbund verwendet werden, können als Marketplace-externe Anwendungen in Unternehmensanwendungen integriert werden.

### <a name="non-federated-apps"></a>Nicht im Verbund angeordnete Apps
Sie können nicht im Verbund angeordnete Apps in Azure AD integrieren, indem Sie den Azure AD-Anwendungsproxy und verwandte Funktionen verwenden. Zu nicht im Verbund angeordneten Apps gehören:
- Apps, für die die integrierte Windows-Authentifizierung direkt mit Active Directory verwendet wird. Sie können diese Apps in Azure AD über den [Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md) integrieren.
- Apps, die über einen Agent in Ihren Anbieter für einmaliges Anmelden integriert werden und Header für die Autorisierung nutzen. Lokale Apps, die einen installierten Agent für die Anmeldung und die headerbasierte Autorisierung verwenden, können für die Azure AD-basierte Anmeldung konfiguriert werden, indem der Azure AD-Anwendungsproxy mit [Ping-Zugriff für Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/) genutzt wird.

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Übersetzen von lokalen Verbund-Apps in Azure AD 
Da sich die Funktionsweise von AD FS und Azure AD ähnelt, gelten die Konzepte für das Konfigurieren der Vertrauensstellung, der Anmelde- und Abmelde-URLs und der Bezeichner in beiden Fällen. Beim Durchführen des Übergangs sollten Sie sich aber über einige kleinere Unterschiede im Klaren sein.

In den folgenden Tabellen sind wichtige Punkte zu AD FS, Azure AD und SaaS-Apps aufgeführt, um Ihnen die Übersetzung zu erleichtern. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Darstellen der App in Azure AD oder AD FS
Die Migration beginnt damit, wie die Anwendung lokal konfiguriert wird, und mit der Zuordnung der Konfiguration zu Azure AD. Die folgende Tabelle enthält eine Zuordnung der Konfigurationselemente der vertrauenden AD FS-Seite zu den entsprechenden Elementen in Azure AD.  
- AD FS-Begriff: Vertrauende Seite oder Vertrauensstellung der vertrauenden Seite.
- Azure AD-Begriff: Unternehmensanwendung oder App-Registrierung (je nach App-Typ).

|App-Konfigurationselement|BESCHREIBUNG|Position in AD FS-Konfiguration|Entsprechende Position in der Azure AD-Konfiguration|SAML-Tokenelement|
|-----|-----|-----|-----|-----|
|Anmelde-URL der App|URL der Anmeldeseite dieser Anwendung. Hierüber meldet sich der Benutzer in einem SP-initiierten SAML-Datenfluss an der App an.|–|In Azure AD ist die Anmelde-URL im Azure-Portal in den **SSO**-Eigenschaften der Anwendung konfiguriert.</br></br>(Unter Umständen müssen Sie **Erweiterte URL-Einstellungen anzeigen** wählen, um die Anmelde-URL anzuzeigen.)|–|
|Antwort-URL der App|Die URL der App aus Sicht des Identitätsanbieters (IdP). An diesen Ort werden der Benutzer und das Token gesendet, nachdem sich der Benutzer beim IdP angemeldet hat.</br></br> Dies wird auch als „Consumerendpunkt der SAML-Assertion“ bezeichnet.|Sie befindet sich in der Vertrauensstellung der vertrauenden Seite von AD FS für die App. Klicken Sie mit der rechten Maustaste auf die vertrauende Seite, und wählen Sie **Eigenschaften** und dann die Registerkarte **Endpunkte**.|In Azure AD ist die Antwort-URL im Azure-Portal in den **SSO**-Eigenschaften der Anwendung konfiguriert.</br></br>(Unter Umständen müssen Sie **Erweiterte URL-Einstellungen anzeigen** wählen, um die Antwort-URL anzuzeigen.)|Ist im SAML-Token dem **Destination**-Element zugeordnet.</br></br> Beispielwert: `https://contoso.my.salesforce.com`|
|Abmelde-URL für App|URL, an die Anforderungen der Art „Abmeldebereinigung“ gesendet werden, wenn sich ein Benutzer von einer App abmeldet, um die Abmeldung von allen anderen Apps durchzuführen, an denen der Benutzer vom IdP angemeldet wurde.|Befindet sich in der AD FS-Verwaltung unter **Vertrauensstellungen der vertrauenden Seite**. Klicken Sie mit der rechten Maustaste auf die vertrauende Seite, und wählen Sie **Eigenschaften** und dann die Registerkarte **Endpunkte**.|N/V. Für Azure AD wird das „einmalige Abmelden“ (also das Abmelden von allen Apps) nicht unterstützt. Der Benutzer wird einfach nur von Azure AD abgemeldet.|–|
|App-ID|Der Bezeichner der App aus IdP-Sicht. Der Wert für die Anmelde-URL wird häufig für den Bezeichner verwendet (aber nicht immer).</br></br> Wird in der App in einigen Fällen auch als „Entitäts-ID“ bezeichnet.|In AD FS ist dies die ID der vertrauenden Seite. Klicken Sie mit der rechten Maustaste auf die Vertrauensstellung der vertrauenden Seite, und wählen Sie **Eigenschaften** und dann die Registerkarte **Bezeichner**.|In Azure AD ist der Bezeichner im Azure-Portal in den **SSO**-Eigenschaften der Anwendung als Bezeichner unter **Domäne und URLs** konfiguriert. (Es kann sein, dass Sie das Kontrollkästchen **Erweiterte URL-Einstellungen anzeigen** aktivieren müssen.)|Entspricht dem **Audience**-Element im SAML-Token.|
|Verbundmetadaten der App|Speicherort der Verbundmetadaten einer App. Der IdP verwendet ihn zum automatischen Aktualisieren von bestimmten Konfigurationseinstellungen, z.B. Endpunkten oder Verschlüsselungszertifikaten.|Die Verbundmetadaten-URL der App befindet sich in der AD FS-Vertrauensstellung der vertrauenden Seite einer App. Klicken Sie mit der rechten Maustaste auf die Vertrauensstellung, wählen Sie **Eigenschaften**, und wählen Sie dann die Registerkarte **Überwachung**.|N/V. Für Azure AD wird die direkte Nutzung von Anwendungsverbundmetadaten nicht unterstützt.|–|
|Benutzer-ID/**NameID**|Attribut zum eindeutigen Angeben der Benutzeridentität von Azure AD oder AD FS für Ihre App.</br></br> Dieses Attribut ist normalerweise entweder der UPN oder die E-Mail-Adresse des Benutzers.|In AD FS ist es als Anspruchsregel der vertrauenden Seite enthalten. In den meisten Fällen stellt die Anspruchsregel einen Anspruch mit einem Typ aus, der auf „nameidentifier“ endet.|In Azure AD finden Sie die Benutzer-ID im Azure-Portal in den **SSO**-Eigenschaften der Anwendung unter der Überschrift **Benutzerattribute**.</br></br>Standardmäßig wird der UPN verwendet.|Wird vom IdP im SAML-Token als **NameID**-Element an die App kommuniziert.|
|Andere Ansprüche, die an die App gesendet werden|Zusätzlich zu Benutzer-ID/**NameID** werden auch andere Anspruchsinformationen häufig vom IdP an die App gesendet. Beispiele hierfür sind Vorname, Nachname, E-Mail-Adresse und Gruppen, in denen der Benutzer Mitglied ist.|In AD FS ist dies unter den anderen Anspruchsregeln der vertrauenden Seite enthalten.|In Azure AD finden Sie die Benutzer-ID im Azure-Portal in den **SSO**-Eigenschaften der Anwendung unter der Überschrift **Benutzerattribute**. Wählen Sie **Ansicht**, und bearbeiten Sie alle weiteren Benutzerattribute.|–|  

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>Darstellen von Azure AD als Identitätsanbieter in einer SaaS-App
Im Rahmen der Migration müssen Sie die App so konfigurieren, dass sie auf Azure AD verweist (nicht den lokalen Identitätsanbieter). In diesem Abschnitt geht es um SaaS-Apps, für die das SAML-Protokoll verwendet wird, und nicht um benutzerdefinierte Branchen-Apps. Die Konzepte gelten aber auch für benutzerdefinierte Branchen-Apps. 

Im Allgemeinen wird für eine SaaS-App anhand von einigen wichtigen Punkten auf Azure AD verwiesen:
- Aussteller des Identitätsanbieters: https&#58;//sts.windows.net/{Mandanten-ID}/
- Anmelde-URL des Identitätsanbieters: https&#58;//login.microsoftonline.com/{Mandanten-ID}/saml2
- Abmelde-URL des Identitätsanbieters: https&#58;//login.microsoftonline.com/{Mandanten-ID}/saml2 
- Speicherort der Verbundmetadaten: https&#58;//login.windows.net/{Mandanten-ID}/federationmetadata/2007-06/federationmetadata.xml?appid={Anwendungs-ID} 

Ersetzen Sie {Mandanten-ID} durch Ihre Mandanten-ID, die Sie im Azure-Portal unter **Azure Active Directory** > **Eigenschaften** als **Verzeichnis-ID** finden. Ersetzen Sie {Anwendungs-ID} durch Ihre Anwendungs-ID, die Sie in den Eigenschaften der Anwendung als **Anwendungs-ID** finden.

In der folgenden Tabelle werden die wichtigen IdP-Konfigurationselemente zum Konfigurieren der SSO-Einstellungen in der App und die dazugehörigen Werte und Speicherorte in AD FS und Azure AD beschrieben. Als Referenz für die Tabelle dient die SaaS-App, die über die Informationen dazu verfügen muss, wohin die Authentifizierungsanforderungen gesendet und wie die empfangenen Token überprüft werden sollen.

|Konfigurationselement|BESCHREIBUNG|AD FS|Azure AD|
|---|---|---|---|
|IdP- </br>Anmelde- </br>URL|Anmelde-URL des IdP aus App-Sicht (an die der Benutzer zur Anmeldung umgeleitet wird).|Die AD FS-Anmelde-URL ist der AD FS-Verbunddienstname, gefolgt von „/adfs/ls/“. Beispiel: https&#58;//fs.contoso.com/adfs/ls/|Der entsprechende Wert für Azure AD basiert auf dem Muster, bei dem {Mandanten-ID} durch Ihre Mandanten-ID ersetzt wird. Sie finden sie im Azure-Portal unter **Azure Active Directory** > **Eigenschaften** als **Verzeichnis-ID**.</br></br>Für Apps, die das SAML-P-Protokoll nutzen: https&#58;//login.microsoftonline.com/{Mandanten-ID}/saml2 </br></br>Für Apps, die das Webdiensteverbund-Protokoll nutzen: https&#58;//login.microsoftonline.com/{Mandanten-ID}/wsfed|
|IdP- </br>Abmeldung </br>URL|Abmelde-URL des IdP aus App-Sicht (an die der Benutzer umgeleitet wird, wenn er sich von der App abmelden möchte).|Für AD FS entspricht die Abmelde-URL entweder der Anmelde-URL, oder es wird die gleiche URL mit dem Zusatz „wa=wsignout1.0“ verwendet. Beispiel: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|Der entsprechende Wert für Azure AD hängt davon ab, ob die App die SAML 2.0-Abmeldung unterstützt.</br></br>Wenn die SAML-Abmeldung von der App unterstützt wird, folgt der Wert einem Muster, bei dem der Wert für {Mandanten-ID} durch die Mandanten-ID ersetzt wird. Sie finden sie im Azure-Portal unter **Azure Active Directory** > **Eigenschaften** als **Verzeichnis-ID**: https&#58;//login.microsoftonline.com/{Mandanten-ID}/saml2</br></br>Wenn die App die SAML-Abmeldung nicht unterstützt: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|Tokenverschlüsselung </br>signatur- </br>Zertifikat|Zertifikat, dessen privater Schlüssel vom IdP zum Signieren von ausgestellten Token verwendet wird. Überprüft, ob das Token von demselben IdP stammt, für den für die App die Vertrauensstellung konfiguriert wurde.|Das AD FS-Tokensignaturzertifikat befindet sich in der AD FS-Verwaltung unter **Zertifikate**.|Für Azure AD finden Sie das Tokensignaturzertifikat im Azure-Portal in den **SSO**-Eigenschaften unter der Überschrift **SAML-Signaturzertifikat**. Dort können Sie das Zertifikat für den Upload in die App herunterladen.</br></br> Falls die Anwendung über mehr als ein Zertifikat verfügt, sind alle Zertifikate in der XML-Datei mit den Verbundmetadaten enthalten.|
|Bezeichner/</br>„Aussteller“|Bezeichner des IdP aus App-Sicht (auch als „Aussteller-ID“ bezeichnet).</br></br>Im SAML-Token wird der Wert als **Issuer**-Element angezeigt.|Der Bezeichner für AD FS ist normalerweise der Verbunddienstbezeichner in der AD FS-Verwaltung unter **Dienst** > **Verbunddiensteigenschaften bearbeiten**. Beispiel: http&#58;//fs.contoso.com/adfs/services/trust|Der entsprechende Wert für Azure AD basiert auf dem Muster, bei dem der Wert für „{Mandanten-ID}“ durch die Mandanten-ID ersetzt wird. Sie finden ihn im Azure-Portal unter **Azure Active Directory** > **Eigenschaften** als **Verzeichnis-ID**: https&#58;//sts.windows.net/{Mandanten-ID}/|
|IdP- </br>Verbund- </br>metadata|Speicherort der öffentlich verfügbaren Verbundmetadaten des IdP. (Einige Apps verwenden Verbundmetadaten als Alternative zur individuellen Konfiguration der URLs, des Bezeichners und des Tokensignaturzertifikats durch den Administrator.)|Sie finden die URL für die AD FS-Verbundmetadaten in der AD FS-Verwaltung unter **Dienst** > **Endpunkte** > **Metadaten** > **Typ: Verbundmetadaten**. Beispiel: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|Der entsprechende Wert für Azure AD basiert auf dem Muster „https&#58;//login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml“. Hierbei wird der Wert für „{TenantDomainName}“ durch den Namen Ihres Mandanten im Format „contoso.onmicrosoft.com“ ersetzt. </br></br>Weitere Informationen finden Sie unter [Verbundmetadaten](../develop/azure-ad-federation-metadata.md).

## <a name="moving-saas-apps"></a>Verschieben von SaaS-Apps
Das Verschieben von SaaS-Apps aus AD FS oder einem anderen Identitätsanbieter in Azure AD ist derzeit ein manueller Prozess. App-spezifische Anleitungen finden Sie in der [Liste mit den Tutorials zur Integration von im Marketplace enthaltenen SaaS-Apps](../saas-apps/tutorial-list.md).

In den Tutorials zur Integration wird vorausgesetzt, dass Sie eine Greenfield-Integration durchführen. Es gibt einige wichtige spezifische Konzepte für die Migration, die Sie kennen sollten, wenn Sie für Ihre Apps die Planung, Bewertung, Konfiguration und Umstellung durchführen:  
- Einige Apps können unter Umständen leicht migriert werden. Aber für Apps mit komplexeren Anforderungen, z.B. benutzerdefinierten Ansprüchen, ist unter Umständen zusätzlicher Konfigurationsaufwand in Azure AD bzw. Azure AD Connect erforderlich.
- In den meisten häufigen Szenarien sind für eine App nur der **NameID**-Anspruch und andere allgemeine Benutzer-ID-Ansprüche erforderlich. Sie können ermitteln, ob zusätzliche Ansprüche erforderlich sind, indem Sie untersuchen, welche Ansprüche Sie für AD FS oder Ihren Drittanbieter-Identitätsanbieter ausstellen.
- Nachdem Sie ermittelt haben, dass zusätzliche Ansprüche erforderlich sind, sollten Sie sicherstellen, dass sie in Azure AD verfügbar sind. Überprüfen Sie die Konfiguration der Azure AD Connect-Synchronisierung, um sich zu vergewissern, dass ein erforderliches Attribut, z.B. **samAccountName**, mit Azure AD synchronisiert wird.
- Nachdem diese Attribute in Azure AD verfügbar sind, können Sie Regeln für die Anspruchsausstellung in Azure AD hinzufügen, um diese Attribute als Ansprüche in ausgestellte Token einzubinden. Sie fügen diese Regeln in den **SSO**-Eigenschaften der App in Azure AD hinzu.

### <a name="assess-what-can-be-moved"></a>Bewerten, welche Elemente verschoben werden können
SAML 2.0-Anwendungen können entweder über den Azure AD-Anwendungskatalog auf dem Marketplace oder als Anwendungen in Azure AD integriert werden, die nicht über den Marketplace angeboten werden.  

Bei einigen Konfigurationen sind zusätzliche Schritte für die Konfiguration in Azure AD erforderlich, und einige andere Konfigurationen werden derzeit nicht unterstützt. Sehen Sie sich die aktuelle Konfiguration Ihrer einzelnen Apps an, um zu ermitteln, was verschoben werden kann. Suchen Sie vor allem nach Folgendem:
- Konfigurierte Anspruchsregeln (Ausstellungstransformationsregeln)
- SAML-**NameID** – Format und Attribut
- Ausgestellte SAML-Tokenversionen
- Andere Konfigurationselemente, z.B. Ausstellungsautorisierungsregeln oder Zugriffssteuerungsrichtlinien und Regeln für die mehrstufige Authentifizierung (zusätzliche Authentifizierung)

#### <a name="what-can-be-moved-today"></a>Was aktuell verschoben werden kann
Zu den Apps, die nach aktuellem Stand problemlos verschoben werden können, zählen unter anderem SAML 2.0-Apps, für die der Standardsatz von Konfigurationselementen und Ansprüchen verwendet wird. Diese Apps können aus folgenden Elementen bestehen:
- Benutzerprinzipalname.
- E-Mail-Adresse.
- Vorname
- Nachname
- Alternatives Attribut als SAML-**NameID**, z.B. Azure AD-E-Mail-Attribut, E-Mail-Präfix, Mitarbeiter-ID, Erweiterungsattribute 1 - 15 oder lokales **SamAccountName**-Attribut. Weitere Informationen finden Sie im Artikel zum [Bearbeiten des NameIdentifier-Anspruchs](../develop/active-directory-saml-claims-customization.md).
- Benutzerdefinierte Ansprüche. Informationen zu den unterstützten Anspruchszuordnungen finden Sie unter [Anspruchszuordnung in Azure Active Directory (Public Preview)](../develop/active-directory-claims-mapping.md) und [Anpassen ausgestellter Ansprüche im SAML-Token für Unternehmensanwendungen in Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Zusätzlich zu benutzerdefinierten Ansprüchen und **NameID**-Elementen gibt es die folgenden Konfigurationen, für die in Azure AD im Rahmen der Migration zusätzliche Konfigurationsschritte erforderlich sind:
- Benutzerdefinierte Autorisierungs- oder Multi-Factor Authentication-Regeln in AD FS. Sie konfigurieren diese Regeln, indem Sie das Feature [Bedingter Azure AD-Zugriff](../active-directory-conditional-access-azure-portal.md) verwenden.
- Apps mit mehreren SAML-Endpunkten. Sie konfigurieren sie in Azure AD mit PowerShell. (Diese Funktion ist im Portal nicht verfügbar.)
- Webdiensteverbund-Apps, z.B. SharePoint-Apps, für die Token der Version SAML 1.1 benötigt werden. Diese müssen Sie mit PowerShell manuell konfigurieren.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Apps und Konfigurationen, die in Azure AD derzeit nicht unterstützt werden
Apps, für die die folgenden Funktionen erforderlich sind, können derzeit nicht migriert werden. Geben Sie im Kommentarbereich Feedback an, falls Sie über Apps mit diesen Featureanforderungen verfügen.
- Protokollfunktionen:
    - Unterstützung für das einmalige SAML-Abmelden (SLO) von allen angemeldeten Apps.
    - Unterstützung für das WS-Trust ActAs-Muster.
    - SAML-Artefaktauflösung.
    - Signaturüberprüfung der signierten SAML-Anforderungen. Beachten Sie, dass signierte Anforderungen zulässig sind, die Signatur aber nicht überprüft wird.
- Tokenfunktionen: 
    - SAML-Tokenverschlüsselung. 
    - Token der Version SAML 1.1 in SAML-Protokollantworten. 
- Ansprüche in Tokenfunktionen:
    - Ausstellung von lokalen Gruppennamen als Ansprüche.
    - Ansprüche aus anderen Speichern als Azure AD.
    - Komplexe Transformationsregeln für die Ausstellung von Ansprüchen. Informationen zu den unterstützten Anspruchszuordnungen finden Sie unter [Anspruchszuordnung in Azure Active Directory (Public Preview)](../develop/active-directory-claims-mapping.md) und [Anpassen ausgestellter Ansprüche im SAML-Token für Unternehmensanwendungen in Azure Active Directory](../develop/active-directory-saml-claims-customization.md).
    - Ausstellung von Verzeichniserweiterungen als Ansprüche.
    - Benutzerdefinierte Angabe des **NameID**-Formats.
    - Ausstellung von mehrwertigen Attributen.

>[!NOTE]
>Azure AD wird ständig weiterentwickelt, um in diesem Bereich Funktionen hinzuzufügen. Wir aktualisieren diesen Artikel in regelmäßigen Abständen. 

### <a name="configure-azure-ad"></a>Konfigurieren von Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Konfigurieren der Einstellungen für das einmalige Anmelden (SSO) für die SaaS-App

In Azure AD konfigurieren Sie die (für Ihre App erforderliche) SAML-Anmeldung in den **SSO**-Eigenschaften der App unter **Benutzerattribute**.

![SSO-Eigenschaften mit Abschnitt „Benutzerattribute“](media/migrate-adfs-apps-to-azure/migrate3.png)

Wählen Sie die Option **Alle weiteren Benutzerattribute anzeigen und bearbeiten**, um die Attribute anzuzeigen, die als Ansprüche im Sicherheitstoken gesendet werden sollen.

![Liste mit Attributen](media/migrate-adfs-apps-to-azure/migrate4.png)

Wählen Sie eine bestimmte Attributzeile aus, die Sie bearbeiten möchten, oder wählen Sie **Attribut hinzufügen**, um ein neues Attribut hinzuzufügen.

![Bereich „Attribut bearbeiten“](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Zuweisen von Benutzern zur App
Damit sich Ihre Benutzer in Azure AD an einer SaaS-App anmelden können, müssen Sie ihnen Zugriff gewähren.

Navigieren Sie zum Zuweisen von Benutzern im Azure AD-Portal auf die Seite der SaaS-App, und wählen Sie in der Randleiste dann **Benutzer und Gruppen**. Wählen Sie zum Hinzufügen eines Benutzers oder einer Gruppe oben im Bereich die Option **Benutzer hinzufügen**. 

![Schaltfläche „Benutzer hinzufügen“ in „Benutzer und Gruppen“](media/migrate-adfs-apps-to-azure/migrate6.png) 

![Bereich „Zuweisung hinzufügen“](media/migrate-adfs-apps-to-azure/migrate7.png)

Zur Überprüfung des Zugriffs sollte Benutzern im [Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md) beim Anmelden die SaaS-App angezeigt werden. Sie finden den Zugriffsbereich unter https://myapps.microsoft.com. In diesem Beispiel wurde dem Benutzer erfolgreich sowohl Zugriff auf Salesforce als auch auf ServiceNow zugewiesen.

![Beispiel für den Zugriffsbereich mit Salesforce- und ServiceNow-App](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>Konfigurieren der SaaS-App
Der Umstellungsprozess vom lokalen Verbund auf Azure AD hängt davon ab, ob die von Ihnen verwendete SaaS-App mehrere Identitätsanbieter unterstützt. Hier sind einige häufig gestellte Fragen zur Unterstützung für mehrere IdPs angegeben:

   **F: Was bedeutet für eine App die Unterstützung von mehreren IdPs?**
    
   A: Mit SaaS-Apps, die mehrere IdPs unterstützen, können Sie alle Informationen zum neuen IdP (in unserem Fall Azure AD) eingeben, bevor Sie den Commit für die Änderung der Anmeldeoberfläche durchführen. Nachdem die Konfiguration abgeschlossen ist, können Sie für die Authentifizierungskonfiguration der App die Umstellung auf Azure AD vornehmen.

   **F: Warum ist es wichtig, dass die SaaS-App mehrere IdPs unterstützt?**

   A: Wenn nicht mehrere IdPs unterstützt werden, muss der Administrator ein kurzes Zeitfenster für einen Dienst- bzw. Wartungsausfall bereitstellen, in dem Azure AD als neuer IdP einer App konfiguriert werden kann. Während dieses Ausfalls sollten Benutzer darüber informiert werden, dass sie sich nicht an ihren Konten anmelden können.

   Wenn eine App mehrere IdPs unterstützt, können zusätzliche IdPs im Voraus konfiguriert werden. Der Administrator kann den IdP dann während der Azure-Umstellung wechseln.

   Benutzer können bei der Authentifizierung auf ihrer Anmeldeseite eine IdP-Auswahl treffen, wenn die App mehrere IdPs unterstützt und Sie mehrere IdPs für die gleichzeitige Verarbeitung der Authentifizierung während der Anmeldung auswählen.

#### <a name="example-support-for-multiple-idps"></a>Beispiel: Unterstützung mehrerer IdPs
In Salesforce finden Sie die IdP-Konfiguration beispielsweise unter **Einstellungen** > **Unternehmenseinstellungen** > **Meine Domäne** > **Authentifizierungskonfiguration**.

![Abschnitt „Authentifizierungskonfiguration“ in der Salesforce-App](media/migrate-adfs-apps-to-azure/migrate9.png)

Da Sie zuvor unter **Identität** > **Einzelanmeldungseinstellungen** die entsprechende Konfiguration erstellt haben, sollten Sie Ihren IdP für die Authentifizierungskonfiguration ändern können. Sie können beispielsweise eine Änderung von AD FS in Azure AD vornehmen. 

![Auswählen von Azure AD als Authentifizierungsdienst](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Optional: Konfigurieren der Benutzerbereitstellung in Azure AD
Wenn Sie möchten, dass Azure AD die Benutzerbereitstellung für eine SaaS-App direkt verarbeitet, helfen Ihnen die Informationen unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](user-provisioning.md) weiter.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Anwendungen mit Azure Active Directory](what-is-application-management.md)
- [Verwalten des Zugriffs auf Apps](what-is-access-management.md)
- [Azure AD Connect und Verbund](../hybrid/how-to-connect-fed-whatis.md)
