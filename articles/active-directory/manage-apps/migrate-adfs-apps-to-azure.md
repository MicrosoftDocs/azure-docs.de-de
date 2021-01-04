---
title: Verschieben der Anwendungsauthentifizierung von AD FS in Azure Active Directory
description: In diesem Artikel wird erläutert, wie Organisationen Anwendungen in Azure AD verschieben können. Der Schwerpunkt liegt hierbei auf SaaS-Anwendungen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: e15009dd935d91971ce4212ff44b67a1ca6fa363
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745379"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Verschieben der Anwendungsauthentifizierung von Active Directory-Verbunddiensten (AD FS) in Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) bietet eine universelle Identitätsplattform, die Ihren Mitarbeitern, Partnern und Kunden eine einzige Identität für den Zugriff auf Anwendungen und die Zusammenarbeit auf beliebigen Plattformen und Geräten bereitstellt. Azure AD verfügt über eine [vollständige Suite von Funktionen zur Identitätsverwaltung](../fundamentals/active-directory-whatis.md). Die Standardisierung der Anwendungsauthentifizierung und -autorisierung (App) auf Azure AD ermöglicht es Ihnen, die Vorteile dieser Funktionen zu nutzen.

> [!TIP]
> Dieser Artikel wurde für eine Entwicklerzielgruppe geschrieben. Für Projektmanager und Administratoren, die die Umstellung einer Anwendung auf Azure AD planen, empfiehlt es sich, unser Whitepaper (PDF) [Migrieren der Anwendungsauthentifizierung zu Azure AD](https://aka.ms/migrateapps/whitepaper) zu lesen.

## <a name="introduction"></a>Einführung

Wenn Sie über ein lokales Verzeichnis verfügen, das Benutzerkonten enthält, haben Sie wahrscheinlich viele Anwendungen, bei denen sich Benutzer authentifizieren. Jede dieser Apps ist so konfiguriert, dass Benutzer mit ihren Identitäten darauf zugreifen können.


Benutzer können sich auch direkt bei Ihrem lokalen Active Directory authentifizieren. Active Directory-Verbunddienste (AD FS) sind ein auf Standards basierender lokaler Identitätsdienst. AD FS erweitert die Möglichkeit, die Funktion für das einmalige Anmelden (Single Sign-on, SSO) für vertrauenswürdige Geschäftspartnern zu verwenden, ohne dass sich Benutzer separat bei jeder Anwendung anmelden müssen. Dies wird als Verbund bezeichnet.

Viele Organisationen nutzen SaaS- oder LOB-Apps (Software-as-a-Service bzw. Line-of-Business) direkt im Verbund von AD FS zusammen mit auf Microsoft 365 und Azure AD basierenden Apps.

![Anwendungen mit direkter lokaler Verbindung](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Ihr Ziel besteht darin, einzelne Zugriffssteuerungen und Richtlinien für Ihre lokalen Umgebungen und Cloudumgebungen zu verwenden, um die Anwendungssicherheit zu erhöhen.**

![Anwendungen, die über Azure AD verbunden sind](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Typen der zu migrierenden Apps

Das Migrieren Ihrer gesamten Anwendungsauthentifizierung zu Azure AD ist optimal, da dieser Dienst eine einzelne Steuerungsebene für die Identitäts- und Zugriffsverwaltung bietet.

Ihre Anwendungen können moderne oder ältere Protokolle für die Authentifizierung verwenden. Sie sollten zuerst Anwendungen migrieren, die moderne Authentifizierungsprotokolle verwenden (z. B. SAML und OpenID Connect). Diese Apps können für die Authentifizierung mit Azure AD entweder über einen integrierten Connector im App-Katalog oder durch Registrieren der Anwendung in Azure AD neu konfiguriert werden. Apps, die ältere Protokolle verwenden, können mit [Anwendungsproxys](./what-is-application-proxy.md) integriert werden.

Weitere Informationen finden Sie unter [Anwendungsverwaltung mit Azure Active Directory](./what-is-application-management.md).

Sie können den [AD FS-Anwendungsaktivitätsbericht über das Migrieren von Anwendungen zu Azure AD](./migrate-adfs-application-activity.md) verwenden, wenn [Azure Active Directory Connect Health aktiviert ist](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Migrationsvorgang

Wenn Sie Ihre App-Authentifizierung in Azure AD verschieben, sollten Sie Ihre Apps und die Konfiguration entsprechend testen. Es wird empfohlen, weiterhin vorhandene Testumgebungen für Migrationstests zu verwenden, die in die Produktionsumgebung verschoben werden. Wenn eine Testumgebung zurzeit nicht verfügbar ist, können Sie diese je nach Architektur der Anwendung mithilfe von [Azure App Service](https://azure.microsoft.com/services/app-service/) oder [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB) einrichten.

Sie können einen separaten Azure AD-Testmandanten einrichten, der bei der Entwicklung Ihrer App-Konfigurationen verwendet werden soll.

Der Migrationsvorgang könnte wie folgt aussehen:

**Phase 1 (aktueller Status): Authentifizierung von Produktions-App mit AD FS**

![Migrationsphase 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)


**Phase 2 (optional): Testinstanz der App, die auf den Azure-Testmandanten verweist**

Aktualisieren Sie die Konfiguration, um mit der Testinstanz der App auf einen Azure AD-Testmandanten zu verweisen. Nehmen Sie dabei alle erforderlichen Änderungen vor. Die App kann mit Benutzern im Azure AD-Testmandanten getestet werden. Während des Entwicklungsprozesses können Sie Tools wie [Fiddler](https://www.telerik.com/fiddler) verwenden, um Anforderungen und Antworten zu vergleichen und zu überprüfen.

Wenn das Einrichten eines separaten Testmandanten nicht möglich ist, überspringen Sie diese Phase, und richten Sie eine Testinstanz einer App ein. Verweisen Sie damit wie in Phase 3 beschrieben auf den Azure AD-Produktionsmandanten.

![Migrationsphase 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Phase 3: Test-App, die auf Azure AD-Produktionsmandanten verweist**

Aktualisieren Sie die Konfiguration, um mit der Testinstanz der App auf Ihre Azure-Produktionsinstanz zu verweisen. Sie können dies jetzt mit Benutzern in Ihrer Produktionsinstanz testen. Lesen Sie sich ggf. noch mal den Abschnitt dieses Artikels über das Migrieren durch.

![Migrationsphase 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Phase 4: Produktions-App, die auf Azure AD-Produktionsmandanten verweist**

Aktualisieren Sie die Konfiguration Ihrer Produktionsanwendung so, dass sie auf Ihren Azure-Produktionsmandanten verweist.

![Migrationsphase 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Apps, die sich mit AD FS authentifizieren, können Active Directory-Gruppen für Berechtigungen verwenden. Verwenden Sie die [Azure AD Connect-Synchronisierung](../hybrid/how-to-connect-sync-whatis.md), um Identitätsdaten zwischen Ihrer lokalen Umgebung und Azure AD vor der Migration zu synchronisieren. Überprüfen Sie diese Gruppen und Mitgliedschaften vor der Migration, damit Sie denselben Benutzern Zugriff gewähren können, wenn die Anwendung migriert wird.

### <a name="line-of-business-lob-apps"></a>LOB-Apps (Line-of-Business)

LOB-Apps werden intern von Ihrer Organisation entwickelt oder als Standardpaketprodukt verfügbar gemacht, das in Ihrem Rechenzentrum installiert wird. Beispiele hierfür sind Apps, die auf Windows Identity Foundation- und SharePoint-Apps basieren (nicht SharePoint Online).

LOB-Apps, für die OAuth 2.0, OpenID Connect oder der WS-Verbund verwendet werden, können als [App-Registrierungen](../develop/quickstart-register-app.md) in Azure AD integriert werden. Integrieren Sie benutzerdefinierte Apps, die SAML 2.0 oder den WS-Verbund als [nicht im Katalog enthaltene Anwendungen](./add-application-portal.md) auf der Unternehmensanwendungsseite im [Azure-Portal](https://portal.azure.com/) verwenden.

## <a name="saml-based-single-sign-on"></a>SAML-basiertes einmaliges Anmelden

Apps, die SAML 2.0 für die Authentifizierung verwenden, können für das [SAML-basierte einmalige Anmelden](./what-is-single-sign-on.md) (SAML-basiertes SSO) konfiguriert werden. Mit dem [SAML-basierten einmaligen Anmelden](./what-is-single-sign-on.md) können Sie Benutzer basierend auf Regeln, die Sie in Ihren SAML-Ansprüchen definieren, bestimmten Anwendungsrollen zuordnen.

Informationen zum Konfigurieren einer SaaS-Anwendung für SAML-basiertes einmaliges Anmelden finden Sie unter [Konfigurieren des SAML-basierten einmaligen Anmeldens](./view-applications-portal.md).

![Screenshots: SSO mit SAML für Benutzer ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Viele SaaS-Anwendungen verfügen über ein [anwendungsspezifisches Tutorial](../saas-apps/tutorial-list.md), in dem die Konfiguration des SAML-basierten einmaligen Anmeldens genau beschrieben wird.

![App-Tutorial](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Einige Apps können unter Umständen leicht migriert werden. Aber für Apps mit komplexeren Anforderungen, z.B. benutzerdefinierten Ansprüchen, ist unter Umständen zusätzlicher Konfigurationsaufwand in Azure AD bzw. Azure AD Connect erforderlich. Informationen zu unterstützten Anspruchszuordnungen finden Sie unter [Anpassen von in Token ausgegebenen Ansprüchen für eine bestimmte App in einem Mandanten (Vorschau)](../develop/active-directory-claims-mapping.md).

Beachten Sie bei der Zuordnung von Attributen die folgenden Einschränkungen:

* Nicht alle Attribute, die in AD FS ausgegeben werden können, werden in Azure AD als Attribute zum Ausgeben von SAML-Tokens angezeigt, auch wenn diese Attribute synchronisiert wurden. Wenn Sie das Attribut bearbeiten, werden in der Dropdownliste „Wert“ die unterschiedlichen Attribute angezeigt, die in Azure AD verfügbar sind. Überprüfen Sie die Konfiguration der [Azure AD Connect-Synchronisierung](../hybrid/how-to-connect-sync-whatis.md), um sicherzustellen, dass ein erforderliches Attribut (z. B. „samAccountName“) mit Azure AD synchronisiert wird. Sie können die Erweiterungsattribute verwenden, um jeden Anspruch auszugeben, der nicht Teil des Standardbenutzerschemas in Azure AD ist.

* In den meisten häufigen Szenarios sind für eine App nur der NameID-Anspruch und andere allgemeine Benutzer-ID-Ansprüche erforderlich. Sie können ermitteln, ob zusätzliche Ansprüche erforderlich sind, indem Sie untersuchen, welche Ansprüche Sie für AD FS ausstellen.

* Nicht alle Ansprüche können Ausgaben sein, da einige Ansprüche in Azure AD geschützt werden.

* Die Funktion zum Verwenden verschlüsselter SAML-Tokens befindet sich jetzt in der Vorschauversion. Informationen finden Sie unter [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).



### <a name="software-as-a-service-saas-apps"></a>SaaS-Apps (Software-as-a-Service)

Wenn sich Ihre Benutzer bei Saas-Apps wie Salesforce, ServiceNow oder Workday anmelden und in AD FS integriert sind, verwenden Sie die Verbundanmeldung für SaaS-Apps.

Die meisten SaaS-Anwendungen können bereits in Azure AD konfiguriert werden. Microsoft verfügt im [Azure AD-Anwendungskatalog](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) über viele vorkonfigurierte Verbindungen mit SaaS-Apps, was den Übergang vereinfacht. SAML 2.0-Anwendungen können entweder über den Azure AD-Anwendungskatalog oder als [nicht im Katalog enthaltene Anwendungen](./add-application-portal.md) in Azure AD integriert werden.

Apps, für die OAuth 2.0 oder OpenID Connect verwendet wird, können auf ähnliche Weise als [App-Registrierungen](../develop/quickstart-register-app.md) in Azure AD integriert werden. Apps, die ältere Protokolle verwenden, können den [Azure AD-Anwendungsproxy](./application-proxy.md) für das Authentifizieren mit Azure AD verwenden.

Wenn Sie Probleme mit dem Onboarding Ihrer SaaS-Apps haben, können Sie den [Supportalias für die SaaS-Anwendungsintegration](mailto:SaaSApplicationIntegrations@service.microsoft.com) kontaktieren.

**SAML-Signaturzertifikate für SSO:** Signaturzertifikate sind ein wichtiger Bestandteil jeder SSO-Bereitstellung. Azure AD erstellt die Signaturzertifikate zum Einrichten des SAML-basierten einmaligen Anmeldens für Ihre SaaS-Anwendungen. Nachdem Sie Anwendungen hinzugefügt haben, die im Katalog enthalten oder nicht enthalten sind, konfigurieren Sie die hinzugefügte Anwendung mithilfe der Option für die einmalige Verbundanmeldung. Weitere Informationen finden Sie unter [Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory](./manage-certificates-for-federated-single-sign-on.md).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Apps und Konfigurationen, die heute verschoben werden können

Zu den Apps, die nach aktuellem Stand problemlos verschoben werden können, zählen unter anderem SAML 2.0-Apps, für die die Standkonfigurationselemente und Ansprüche verwendet werden:

* Benutzerprinzipalname

* E-Mail-Adresse

* Vorname

* Surname

* Alternatives Attribut als SAML-**NameID**, z.B. Azure AD-E-Mail-Attribut, E-Mail-Präfix, Mitarbeiter-ID, Erweiterungsattribute 1 - 15 oder lokales **SamAccountName**-Attribut. Weitere Informationen finden Sie im Artikel zum [Bearbeiten des NameIdentifier-Anspruchs](../develop/active-directory-saml-claims-customization.md).

* Benutzerdefinierte Ansprüche.

Folgendes erfordert zusätzliche Konfigurationsschritte für die Migration zu Azure AD:

* Benutzerdefinierte Autorisierungs- oder Multi-Factor Authentication-Regeln (MFA) in AD FS: Sie konfigurieren diese Regeln, indem Sie das Feature [Bedingter Azure AD-Zugriff](../conditional-access/overview.md) verwenden.

* Apps mit mehreren SAML-Endpunkten: Sie konfigurieren diese in Azure AD mithilfe von PowerShell oder der Schnittstelle des Azure-Portals.

* Webdiensteverbund-Apps, z.B. SharePoint-Apps, für die Token der Version SAML 1.1 benötigt werden. Diese können Sie mit PowerShell manuell konfigurieren. Sie können auch eine vorintegrierte generische Vorlage für SharePoint- und SAML 1.1-Anwendungen aus dem Katalog hinzufügen. Wir unterstützen das SAML 2.0-Protokoll.

* Komplexe Transformationsregeln für die Ausstellung von Ansprüchen: Informationen zu unterstützten Anspruchszuordnungen finden Sie in den folgenden Artikeln:
   *  [Zuordnen von Benutzeransprüchen in Azure Active Directory](../develop/active-directory-claims-mapping.md)
   * [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md)



### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Apps und Konfigurationen, die in Azure AD derzeit nicht unterstützt werden

Apps, für die die folgenden Funktionen erforderlich sind, können derzeit nicht migriert werden.

**Protokollfunktionen:**

* Unterstützung für das WS-Trust ActAs-Muster

* SAML-Artefaktauflösung

* Signaturüberprüfung von signierten SAML-Anforderungen ‎Beachten Sie, dass signierte Anforderungen zulässig sind, die Signatur aber nicht überprüft wird.
Da Azure AD das Token nur an Endpunkte zurückgibt, die in der Anwendung vorkonfiguriert werden, ist die Signaturüberprüfung in den meisten Fällen wahrscheinlich nicht erforderlich.

**Ansprüche in Tokenfunktionen:**

* Ansprüche aus Attributen werden außerhalb des Azure AD-Verzeichnisses gespeichert, es sei denn, diese Daten werden mit Azure AD synchronisiert. Weitere Informationen finden Sie in der [Übersicht über die Azure AD-Synchronisierungs-API](/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Ausstellung von mehrwertigen Verzeichnisattributen. Beispielsweise ist es zu diesem Zeitpunkt nicht möglich, einen mehrwertigen Anspruch für Proxyadressen auszugeben.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Zuordnen von App-Einstellungen von AD FS zu Azure AD

Die Migration beginnt damit, wie die Anwendung lokal konfiguriert wird, und mit der Zuordnung der Konfiguration zu Azure AD. Da sich die Funktionsweise von AD FS und Azure AD ähnelt, gelten die Konzepte für das Konfigurieren der Vertrauensstellung, der Anmelde- und Abmelde-URLs und der Bezeichner in beiden Fällen. Dokumentieren Sie die AD FS-Konfigurationseinstellungen Ihrer Anwendungen, damit Sie diese problemlos in Azure AD konfigurieren können.

### <a name="map-app-configuration-settings"></a>Zuordnen von App-Konfigurationseinstellungen

In der folgenden Tabelle werden einige der gängigsten Zuordnungen von Einstellungen zwischen einer AD FS-Vertrauensstellung der vertrauenden Seite und der Azure AD-Unternehmensanwendung beschrieben:

* AD FS: Suchen Sie die Einstellung in der AD FS-Vertrauensstellung der vertrauenden Seite für die App. Klicken Sie mit der rechten Maustaste auf die vertrauende Seite, und klicken Sie auf „Eigenschaften“.

* Azure AD: Die Einstellung ist im [Azure-Portal](https://portal.azure.com/) in den Einstellungen für das einmalige Anmelden jeder Anwendung konfiguriert.

| Konfigurationseinstellung| AD FS| Konfigurieren in Azure AD| SAML-Token |
| - | - | - | - |
| **App sign-on URL** (Anmelde-URL der App) <p>Dies ist die URL für die Anmeldung des Benutzers bei der App in einem vom Dienstanbieter initiierten SAML-Flow.| –| Öffnen Sie die grundlegende SAML-Konfiguration der SAML-basierten Anmeldung.| – |
| **App reply URL** (Antwort-URL der App) <p>Dies ist die URL der App aus der Perspektive des Identitätsanbieters (IdP). Der Benutzer und das Token werden vom Identitätsanbieter hierher gesendet, nachdem sich der Benutzer beim Identitätsanbieter angemeldet hat.  Dies wird auch als **Consumerendpunkt der SAML-Assertion** bezeichnet.| Klicken Sie auf die Registerkarte **Endpunkte**.| Öffnen Sie die grundlegende SAML-Konfiguration der SAML-basierten Anmeldung.| Dies ist das Zielelement im SAML-Token. Beispielwert: `https://contoso.my.salesforce.com` |
| **App sign-out URL** (Abmelde-URL für App) <p>Dies ist die URL, an die Anforderungen für die Abmeldebereinigung gesendet werden, wenn sich ein Benutzer von einer App abmeldet. Der Identitätsanbieter sendet die Anforderung zum Abmelden des Benutzers auch von allen anderen Apps.| Klicken Sie auf die Registerkarte **Endpunkte**.| Öffnen Sie die grundlegende SAML-Konfiguration der SAML-basierten Anmeldung.| – |
| **App identifier** (App-Bezeichner) <p>Dies ist der App-Bezeichner aus der Perspektive des Identitätsanbieters. Der Wert für die Anmelde-URL wird häufig für den Bezeichner verwendet (aber nicht immer).  Dies wird von der App in einigen Fällen auch als „Entitäts-ID“ bezeichnet.| Klicken Sie auf die Registerkarte **Bezeichner**.|Öffnen Sie die grundlegende SAML-Konfiguration der SAML-basierten Anmeldung.| Er wird dem **Audience**-Element im SAML-Token zugeordnet. |
| **App federation metadata** (App-Verbundmetadaten) <p>Dies ist der Speicherort der Verbundmetadaten der App. Der IdP verwendet ihn zum automatischen Aktualisieren von bestimmten Konfigurationseinstellungen, z.B. Endpunkten oder Verschlüsselungszertifikaten.| Klicken Sie auf die Registerkarte **Überwachung**.| N/V. Für Azure AD wird die direkte Nutzung von Anwendungsverbundmetadaten nicht unterstützt. Sie können die Verbundmetadaten manuell importieren.| – |
| **Benutzerbezeichner/Namens-ID** <p>Attribut zum eindeutigen Angeben der Benutzeridentität von Azure AD oder AD FS für Ihre App.  Dieses Attribut ist normalerweise entweder der Benutzerprinzipalname (UPN) oder die E-Mail-Adresse des Benutzers.| Anspruchsregeln: In den meisten Fällen stellt die Anspruchsregel einen Anspruch mit einem Typ aus, der mit dem „NameIdentifier“ endet.| Sie finden den Bezeichner unter dem Header **Benutzerattribute und Ansprüche**. Standardmäßig wird der Benutzerprinzipalname verwendet.| Er wird dem **NameID**-Element im SAML-Token zugeordnet. |
| **Andere Ansprüche** <p>Beispiele für andere Anspruchsinformationen, die häufig vom Identitätsanbieter an die App gesendet werden, sind der Vorname, der Nachname, die E-Mail-Adresse und die Gruppenmitgliedschaft.| In AD FS ist dies unter den anderen Anspruchsregeln der vertrauenden Seite enthalten.| Sie finden den Bezeichner unter dem Header **Benutzerattribute und Ansprüche**. Wählen Sie **Ansicht**, und bearbeiten Sie alle weiteren Benutzerattribute.| – |


### <a name="map-identity-provider-idp-settings"></a>Zuordnen von Einstellungen für Identitätsanbieter

Konfigurieren Sie Ihre Anwendungen so, dass sie für SSO auf Azure AD bzw. AD FS zeigen. Hier konzentrieren wir uns auf SaaS-Apps, die das SAML-Protokoll verwenden. Dieses Konzept gilt jedoch auch für benutzerdefinierte, branchenspezifische Apps.

> [!NOTE]
> Die Konfigurationswerte für Azure AD folgen dem Muster, nach dem „{Mandanten-ID}“ durch Ihre Azure-Mandanten-ID und „{Anwendungs-ID}“ durch die Anwendungs-ID ersetzt werden. Diese Informationen finden Sie im [Azure-Portal](https://portal.azure.com/) unter „Azure Active Directory > Eigenschaften“:

* Klicken Sie auf die Verzeichnis-ID, um die Mandanten-ID anzuzeigen.

* Klicken Sie auf die Anwendungs-ID, um Ihre Anwendungs-ID anzuzeigen.

 Ordnen Sie Azure AD auf hoher Ebene die folgenden wichtigen SaaS-Anwendungskonfigurationselemente zu.



| Element| Konfigurationswert |
| - | - |
| Aussteller des Identitätsanbieters| https:\//sts.windows.net/{Mandanten-ID}/ |
| Anmelde-URL des Identitätsanbieters| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Abmelde-URL des Identitätsanbieters| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Speicherort der Verbundmetadaten| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>Zuordnen von SSO-Einstellungen für SaaS-Apps

SaaS-Apps müssen wissen, wohin die Authentifizierungsanforderungen gesendet und wie die empfangenen Token überprüft werden sollen. In der folgenden Tabelle werden die Elemente zum Konfigurieren der SSO-Einstellungen in der App und die dazugehörigen Werte und Speicherorte in AD FS und Azure AD beschrieben.

| Konfigurationseinstellung| AD FS| Konfigurieren in Azure AD |
| - | - | - |
| **IdP Sign-on URL** (Anmelde-URL des Identitätsanbieters) <p>Dies ist die Anmelde-URL des Identitätsanbieters aus der App-Perspektive (an die der Benutzer zur Anmeldung umgeleitet wird).| Die AD FS-Anmelde-URL ist der AD FS-Verbunddienstname gefolgt von „/adfs/ls/“. <p>Beispiel: `https://fs.contoso.com/adfs/ls/`| Ersetzen Sie „{Mandanten-ID}“ durch die Mandanten-ID. <p> Informationen für Apps, die das SAML-P-Protokoll verwenden: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Informationen für Apps, die das WS-Verbund-Protokoll verwenden: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP sign-out URL** (Abmelde-URL des Identitätsanbieters)<p>Dies ist die Abmelde-URL des Identitätsanbieters aus der App-Perspektive (an die der Benutzer umgeleitet wird, wenn er sich von der App abmelden möchte).| Die Abmelde-URL entspricht entweder der Anmelde-URL, oder es wird die gleiche URL mit dem Zusatz „wa=wsignout1.0“ verwendet. Beispiel: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Ersetzen Sie „{Mandanten-ID}“ durch die Mandanten-ID.<p>Informationen für Apps, die das SAML-P-Protokoll verwenden:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Informationen für Apps, die das WS-Verbund-Protokoll verwenden: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Tokensignaturzertifikat**<p>Der Identitätsanbieter verwendet den privaten Schlüssel des Zertifikats zum Signieren ausgestellter Tokens. Überprüft, ob das Token von demselben IdP stammt, für den für die App die Vertrauensstellung konfiguriert wurde.| Das AD FS-Tokensignaturzertifikat befindet sich in der AD FS-Verwaltung unter **Zertifikate**.| Suchen Sie es im Azure-Portal in den **Eigenschaften für das einmalige Anmelden** der Anwendung unter dem Header **SAML-Signaturzertifikat**. Dort können Sie das Zertifikat für den Upload in die App herunterladen.  <p>Falls die Anwendung über mehr als ein Zertifikat verfügt, sind alle Zertifikate in der XML-Datei mit den Verbundmetadaten enthalten. |
| **Bezeichner/„Aussteller“**<p>Dies ist der Bezeichner des Identitätsanbieters aus der App-Perspektive (auch als „Aussteller-ID“ bezeichnet).<p>Im SAML-Token wird der Wert als „Aussteller“-Element angezeigt.| Der Bezeichner für AD FS ist normalerweise der Verbunddienstbezeichner in der AD FS-Verwaltung unter **Dienst > Verbunddiensteigenschaften bearbeiten**. Beispiel: `http://fs.contoso.com/adfs/services/trust`| Ersetzen Sie „{Mandanten-ID}“ durch die Mandanten-ID.<p>https:\//sts.windows.net/{Mandanten-ID}/ |
| **IdP federation metadata** (Verbundmetadaten des Identitätsanbieters)<p>Dies ist der Speicherort der öffentlich verfügbaren Verbundmetadaten des Identitätsanbieters. (Einige Apps verwenden Verbundmetadaten als Alternative zur individuellen Konfiguration der URLs, des Bezeichners und des Tokensignaturzertifikats durch den Administrator.)| Sie finden die URL für die AD FS-Verbundmetadaten in der AD FS-Verwaltung unter **Dienst > Endpunkte > Metadaten > Typ: Verbundmetadaten**. Beispiel: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Der entsprechende Wert für Azure AD basiert auf dem Muster [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml). Ersetzen Sie „{TenantDomainName}“ durch den Mandantennamen im Format „contoso.onmicrosoft.com“.   <p>Weitere Informationen finden Sie unter [Verbundmetadaten](../azuread-dev/azure-ad-federation-metadata.md). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>AD FS-Sicherheitsrichtlinien in Azure AD

Wenn Sie Ihre App-Authentifizierung in Azure AD verschieben, erstellen Sie Zuordnungen von vorhandenen Sicherheitsrichtlinien zu den entsprechenden oder alternativen Varianten, die in Azure AD verfügbar sind. Wenn Sie sicherstellen, dass diese Zuordnungen ausgeführt werden können, während die von den App-Besitzern benötigten Sicherheitsstandards erfüllt werden, wird der Rest der App-Migration erheblich vereinfacht.

Für jeden Regeltyp und dessen Beispiele wird hier empfohlen, wie die Regel in AD FS aussehen, der äquivalente Code der AD FS-Regelsprache lauten und in Azure AD zugeordnet werden sollte.

### <a name="map-authorization-rules"></a>Zuordnungsautorisierungsregeln

Im Folgenden finden Sie Beispiele für Typen von Autorisierungsregeln in AD FS und deren Zuordnung zu Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Beispiel 1: Allen Benutzern Zugriff gewähren

Option zum Gewähren des Zugriffs für alle Benutzer in AD FS:

![Screenshot der Dialogfelds „Einmaliges Anmelden (SSO) mit SAML einrichten“](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Dies entspricht Azure AD auf eine der folgenden Arten:

Im [Azure-Portal](https://portal.azure.com/):
* Option 1: Festlegen der Option „Benutzerzuweisung erforderlich?“ auf „Nein“ ![Bearbeiten der Zugriffssteuerungsrichtlinie für SaaS-Apps ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Beachten Sie, dass Benutzer für den Zugriff einer Anwendung zugewiesen sein müssen, wenn die Einstellung „Benutzerzuweisung erforderlich?“ auf „Ja“ festgelegt ist. Wenn diese Einstellung auf „Nein“ festgelegt ist, haben alle Benutzer Zugriff. Mit diesem Schalter wird nicht gesteuert, was Benutzern auf der Benutzeroberfläche „Meine Apps“ angezeigt wird.


* Option 2: Weisen Sie Ihre Anwendung in der Registerkarte „Benutzer und Gruppen“ der automatischen Gruppe „Alle Benutzer“ zu. <p>
Sie müssen [dynamischen Gruppen](../enterprise-users/groups-create-rule.md) in Ihrem Azure AD-Mandanten aktivieren, damit die Standardgruppe „Alle Benutzer“ verfügbar ist.

   ![Meine SaaS-Apps in Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Beispiel 2: Explizites Zulassen einer Gruppe

Explizite Gruppenautorisierung in AD FS:


![Screenshot des Dialogfelds „Regel bearbeiten“ für die Anspruchsregel „Domänenadministratoren zulassen“](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


So wird die Regel Azure AD zugeordnet:

Erstellen Sie im [Azure-Portal](https://portal.azure.com/) zunächst [eine Benutzergruppe](../fundamentals/active-directory-groups-create-azure-portal.md), die der Gruppe der Benutzer aus AD FS entspricht, und weisen Sie der Gruppe dann App-Berechtigungen zu:

![Zuweisung hinzufügen ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Beispiel 3: Autorisieren eines bestimmten Benutzers

Explizite Benutzerautorisierung in AD FS:

![Screenshot des Dialogfelds „Regel bearbeiten“ für die Anspruchsregel „Einen bestimmten Benutzer zulassen“; der „Typ des eingehenden Anspruchs“ ist auf „Primäre SID“ eingestellt.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

So wird die Regel Azure AD zugeordnet:

Fügen Sie der App im [Azure-Portal](https://portal.azure.com/) wie unten dargestellt über die App-Registerkarte „Zuweisung hinzufügen“ einen Benutzer hinzu:

![Meine SaaS-Apps in Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)


### <a name="map-multi-factor-authentication-rules"></a>Zuordnen von Regeln für die mehrstufige Authentifizierung

Eine lokale Bereitstellung der [mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA)](../authentication/concept-mfa-howitworks.md) und AD FS funktioniert nach der Migration weiterhin, da Sie über einen Verbund mit AD FS verfügen. Berücksichtigen Sie jedoch die Migration zu den integrierten MFA-Funktionen von Azure, die in Azure AD-Workflows für den bedingten Zugriff verknüpft sind.

Im Folgenden finden Sie Beispiele für Typen von MFA-Regeln in AD FS und deren Zuordnung zu Azure AD basierend auf unterschiedlichen Bedingungen:

MFA-Regeleinstellungen in AD FS:

![Screenshot der Bedingungen für Azure AD im Azure-Portal](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Beispiel 1: Erzwingen der MFA basierend auf Benutzern/Gruppen

Beim Selektor „Benutzer und Gruppen“ handelt es sich um eine Regel, mit der Sie die MFA auf Gruppen- oder Benutzerbasis (Gruppen-SID bzw. primäre SID) erzwingen können. Abgesehen von den Zuweisungen für „Benutzer und Gruppen“ fungieren alle zusätzlichen Kontrollkästchen auf der MFA-Konfigurationsbenutzeroberfläche in AD FS als zusätzliche Regeln, die nach der Durchsetzung der Regel „Benutzer und Gruppen“ ausgewertet werden.


Angeben von MFA-Regeln für einen Benutzer oder eine Gruppe in Azure AD:

1. Erstellen Sie eine [neue Richtlinie für bedingten Zugriff](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Klicken Sie auf **Zuweisungen**. Fügen Sie die Benutzer oder Gruppen hinzu, für die Sie die MFA erzwingen möchten.

3. Konfigurieren Sie wie unten gezeigt die Optionen für die **Zugriffssteuerung**: ‎

![Screenshot des Bereichs „Gewähren“, in dem Sie Zugriff gewähren](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)


 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Beispiel 2: Erzwingen der MFA für nicht registrierte Geräte

Angeben der MFA-Regeln für nicht registrierte Geräte in Azure AD:

1. Erstellen Sie eine [neue Richtlinie für bedingten Zugriff](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Legen Sie die **Zuweisungen** auf **Alle Benutzer** fest.

3. Konfigurieren Sie wie unten gezeigt die Optionen für die **Zugriffssteuerung**: ‎

![Screenshot des Bereichs „Gewähren“, in dem Sie Zugriff gewähren und andere Einschränkungen angeben](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)


Wenn Sie für „Eine der ausgewählten Steuerungen anfordern“ die Option „Für mehrere Steuerelemente“ festlegen, bedeutet das, dass der Zugriff auf Ihre App gewährt wird, wenn eine der im Kontrollkästchen angegebenen Bedingungen vom Benutzer erfüllt wird.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Beispiel 3: Erzwingen der MFA basierend auf dem Standort

Angeben von MFA-Regeln basierend auf dem Standort eines Benutzers in Azure AD:

1. Erstellen Sie eine [neue Richtlinie für bedingten Zugriff](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

1. Legen Sie die **Zuweisungen** auf **Alle Benutzer** fest.

1. [Konfigurieren Sie benannte Standorte in Azure AD](../reports-monitoring/quickstart-configure-named-locations.md). Andernfalls wird der Verbund innerhalb Ihres Unternehmensnetzwerks als vertrauenswürdig eingestuft.

1. Konfigurieren Sie die Regeln für die **Bedingungen**, um die Standorte anzugeben, für die Sie die MFA erzwingen möchten.

![Screenshot des Bereichs „Standorte“ für Bedingungsregeln](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Konfigurieren Sie wie unten gezeigt die Optionen für die **Zugriffssteuerung**:


![Zuordnen von Zugriffssteuerungsrichtlinien](media/migrate-adfs-apps-to-azure/mfa-location-2.png)


### <a name="map-emit-attributes-as-claims-rule"></a>Zuordnen von Attributen als Anspruchsregel

Im Folgenden finden Sie ein Beispiel für das Zuordnen von Attributen in AD FS:

![Screenshot des Dialogfelds „Regel bearbeiten“ zum Ausgeben von Attributen als Ansprüche](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


So wird die Regel Azure AD zugeordnet:

Klicken Sie im [Azure-Portal](https://portal.azure.com/) zunächst auf **Unternehmensanwendungen** und dann auf **Einmaliges Anmelden**, und fügen Sie wie unten dargestellt **SAML-Tokenattribute** hinzu:

![Screenshot der Seite „Einmaliges Anmelden“ für Ihre Enterprise-Anwendung](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Zuordnen integrierter Zugriffssteuerungsrichtlinien

AD FS 2016 verfügt über mehrere integrierte Zugriffssteuerungsrichtlinien, die Sie auswählen können:

![Integrierte Azure AD-Zugriffssteuerung](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)


Sie können eine [neue bedingte Zugriffsrichtlinie](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) verwenden und die Zugriffssteuerungen konfigurieren, um integrierte Richtlinien in Azure AD zu implementieren. Sie können auch den benutzerdefinierten Richtlinien-Designer in AD FS 2016 für die Konfiguration von Zugriffssteuerungsrichtlinien verwenden. Der Regel-Editor enthält eine vollständige Liste der Optionen „Permit“ („Zulassen“) und „Except“ („außer“), mit denen Sie alle Arten von Permutationen durchführen können.

![Zugriffssteuerungsrichtlinien in Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



In dieser Tabelle sind einige nützliche Optionen für „Permit“ („Zulassen“) und „Except“ („außer“) sowie deren Zuordnung zu Azure AD aufgeführt.


| Option | Konfigurieren der „Permit“-Option („Zulassen“) in Azure AD| Konfigurieren der „Except“-Option („außer“) in Azure AD |
| - | - | - |
| In bestimmten Netzwerken| Die Option wird einem [benannten Standort](../reports-monitoring/quickstart-configure-named-locations.md) in Azure AD zugeordnet.| Verwenden Sie für [vertrauenswürdige Standorte](../conditional-access/location-condition.md) die **Exclude**-Option („außer“). |
| In bestimmten Gruppen| [Legen Sie eine Benutzer-/Gruppenzuweisung fest.](./assign-user-or-group-access-portal.md)| Verwenden Sie in „Benutzer und Gruppen“ die **Exclude**-Option („außer“). |
| Auf Geräten mit bestimmter Vertrauensebene| Legen Sie diese Einstellung im Steuerelement „Gerätestatus“ unter „Zuweisungen“ > „Bedingungen“ fest.| Verwenden Sie die Option zum **Ausschließen** unter „Gerätestatus“ > „Bedingung“, und klicken Sie auf **Alle Geräte**. |
| Mit bestimmten Ansprüchen in der Anforderung| Diese Einstellung kann nicht migriert werden.| Diese Einstellung kann nicht migriert werden. |


Beispiel für die Konfiguration der Option „Ausschließen“ für vertrauenswürdige Standorte im Azure-Portal:

![Screenshot: Zuordnen von Zugriffssteuerungsrichtlinien](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Übergeben von Benutzern aus AD FS an Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synchronisieren von AD FS-Gruppen in Azure AD

Wenn Sie Autorisierungsregeln zuordnen, verwenden Apps, die sich mit AD FS authentifizieren, möglicherweise Active Directory-Gruppen für Berechtigungen. Verwenden Sie in diesem Fall [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771), um diese Gruppen mit Azure AD zu synchronisieren, bevor die Anwendungen migriert werden. Stellen Sie sicher, dass Sie diese Gruppen und Mitgliedschaften vor der Migration überprüfen, damit Sie denselben Benutzern Zugriff gewähren können, wenn die Anwendung migriert wird.

Weitere Informationen finden Sie unter [Konfigurieren von Gruppenansprüchen für Anwendungen mit Azure Active Directory (öffentliche Vorschau)](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="setup-user-self-provisioning"></a>Einrichten der eigenständigen Bereitstellung von Benutzern

Einige SaaS-Anwendungen unterstützen die Funktion zum eigenständigen Bereitstellen von Benutzern, wenn diese sich zum ersten Mal bei der Anwendung anmelden. In Azure Active Directory (Azure AD) bezieht sich der Ausdruck „App-Bereitstellung“ auf die automatische Erstellung von Benutzeridentitäten und Rollen in den Cloudanwendungen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), auf die Benutzer Zugriff benötigen. Migrierte Benutzer verfügen bereits über ein Konto in der SaaS-Anwendung. Alle neuen Benutzer, die nach der Migration hinzugefügt werden, müssen bereitgestellt werden. Testen Sie die [Bereitstellung der SaaS-App](../app-provisioning/user-provisioning.md), nachdem die Anwendung migriert wurde.

### <a name="sync-external-users-in-azure-ad"></a>Synchronisieren externer Benutzer in Azure AD

Die vorhandenen externen Benutzer können auf zwei Arten in AD FS eingerichtet werden:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Externe Benutzer mit einem lokalen Konto innerhalb Ihrer Organisation

Diese Konten können weiterhin auf die gleiche Weise verwendet werden wie Ihre internen Benutzerkonten. Diese externen Benutzerkonten weisen in Ihrer Organisation einen Prinzipalnamen auf, obwohl die E-Mail-Adresse des Kontos möglicherweise extern angezeigt wird. Wenn Sie die Migration weiter durchführen, können Sie von den Vorteilen profitieren, die [Azure AD B2B](../external-identities/what-is-b2b.md) bietet, indem Sie diese Benutzer zur Verwendung ihrer eigenen Unternehmensidentität migrieren, wenn eine derartige Identität verfügbar ist. Dies optimiert den Anmeldevorgang für diese Benutzer, da sie häufig mit den Anmeldeinformationen ihres eigenen Unternehmens angemeldet sind. Die Verwaltung Ihrer Organisation wird ebenfalls entlastet, da Konten für externe Benutzer nicht mehr verwaltet werden müssen.

#### <a name="federated-external-identities"></a>Externe Verbundidentitäten

Wenn Sie gerade einen Verbund mit einer externen Organisation erstellen, sind einige Ansätze zu berücksichtigen:

* [Fügen Sie Azure Active Directory B2B-Zusammenarbeitsbenutzer im Azure-Portal hinzu.](../external-identities/add-users-administrator.md) Sie können B2B Collaboration-Einladungen proaktiv aus dem Azure AD-Verwaltungsportal an die Partnerorganisation senden, damit einzelne Mitglieder weiterhin die Apps und Ressourcen verwenden können, die sie sonst auch nutzen.

* [Erstellen Sie einen Workflow für die eigenständige B2B-Anmeldung](../external-identities/self-service-portal.md), bei dem mithilfe der B2B-API für Einladungen Anforderungen für einzelne Benutzer in Ihrer Partnerorganisation generiert werden.

Unabhängig davon, wie vorhandene externe Benutzer konfiguriert sind, verfügen diese wahrscheinlich über Berechtigungen, die ihrem Konto entweder in der Gruppenmitgliedschaft oder in bestimmten Berechtigungen zugeordnet sind. Entscheiden Sie, ob diese Berechtigungen migriert oder entfernt werden müssen. Konten innerhalb Ihrer Organisation, die einen externen Benutzer darstellen, müssen deaktiviert werden, sobald der Benutzer zu einer externen Identität migriert wurde. Der Migrationsvorgang sollte mit Ihren Geschäftspartnern besprochen werden, da sie vorübergehend möglicherweise keine Verbindung mit Ihren Ressourcen herstellen können.

## <a name="migrate-and-test-your-apps"></a>Migrieren und Testen der Apps

Befolgen Sie den in diesem Artikel beschriebenen Migrationsvorgang.

Wechseln Sie dann zum [Azure-Portal](https://aad.portal.azure.com/), um zu testen, ob die Migration erfolgreich war. Befolgen Sie die nachstehenden Anweisungen:
1. Klicken Sie auf **Unternehmensanwendungen** > **Alle Anwendungen**, und suchen Sie die App in der Liste.

1. Klicken Sie auf **Verwalten** > **Benutzer und Gruppen**, um der App mindestens einen Benutzer oder eine Gruppe zuzuweisen.

1. Klicken Sie auf **Verwalten** > **Bedingter Zugriff**. Überprüfen Sie die Liste der Richtlinien, und stellen Sie sicher, dass Sie den Zugriff auf die Anwendung nicht mit einer [Richtlinie für bedingten Zugriff](../conditional-access/overview.md) blockieren.

Vergewissern Sie sich, dass das einmalige Anmelden je nach Konfiguration Ihrer App ordnungsgemäß funktioniert.

| Authentifizierungsart| Testen |
| - | - |
| OAuth und OpenID Connect| Klicken Sie auf **Unternehmensanwendungen > Berechtigungen**, und stellen Sie sicher, dass Sie in den Benutzereinstellungen für Ihre App zugestimmt haben, dass die Anwendung in Ihrer Organisation verwendet werden darf.
‎ |
| SAML-basiertes SSO| Verwenden Sie die Schaltfläche [SAML-Einstellungen testen](./debug-saml-sso-issues.md) unter **Einmaliges Anmelden**.
‎ |
| Kennwortbasiertes einmaliges Anmelden| Laden Sie die [My Apps Secure Sign](../user-help/my-apps-portal-end-user-access.md)[-](../user-help/my-apps-portal-end-user-access.md)[in-Erweiterung](../user-help/my-apps-portal-end-user-access.md) herunter, und installieren Sie diese. Mit dieser Erweiterung können Sie alle Cloud-Apps Ihrer Organisation starten, bei denen Sie einen SSO-Prozess verwenden müssen.
‎ |
| Anwendungsproxy| Stellen Sie sicher, dass Ihr Connector ausgeführt wird und der Anwendung zugewiesen ist. Im Leitfaden zum [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](./application-proxy-troubleshoot.md) finden Sie weitere Unterstützung.
‎ |

> [!NOTE]
> Cookies aus der alten AD FS-Umgebung bleiben weiterhin auf den Computern des Benutzers erhalten. Diese Cookies können Probleme bei der Migration verursachen, da Benutzer an die alte AD FS-Anmeldeumgebung anstelle der neuen Azure AD-Anmeldung umgeleitet werden könnten. Möglicherweise müssen Sie die Benutzerbrowsercookies manuell oder mithilfe eines Skripts löschen. Sie können auch System Center Configuration Manager oder eine ähnliche Plattform verwenden.

### <a name="troubleshoot"></a>Problembehandlung

Wenn beim Testen der migrierten Anwendungen Fehler auftreten, kann die Problembehandlung der erste Schritt sein, bevor wieder auf die vorhandenen vertrauenden AD FS-Seiten zugegriffen wird. Weitere Informationen finden Sie unter [Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory](./debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Rollbackmigration

Wenn die Migration fehlschlägt, empfiehlt es sich, die vorhandenen vertrauenden Seiten auf den AD FS-Servern beizubehalten und den Zugriff auf die vertrauenden Seiten zu entfernen. Dies ermöglicht bei Bedarf einen schnellen Fallback während der Bereitstellung.

### <a name="end-user-communication"></a>Endbenutzerkommunikation

Selbst wenn der geplante Ausfallzeitraum für den Wechsel von AD FS zu Azure AD kurz ist, sollten Sie Ihren Mitarbeitern diese Zeiträume dennoch proaktiv mitteilen. Stellen Sie sicher, dass Ihre App für den Fall von Problemen über eine Schaltfläche für Feedback oder einen Zeiger auf den Helpdesk verfügt.

Nachdem die Bereitstellung abgeschlossen ist, können Sie die Benutzer über die erfolgreiche Bereitstellung informieren und an alle neuen Schritte erinnern, die sie durchführen müssen.

* Weisen Sie die Benutzer an, [Meine Apps](https://myapps.microsoft.com) für den Zugriff auf alle migrierten Anwendungen zu verwenden.

* Erinnern Sie Benutzer daran, dass sie ihre MFA-Einstellungen möglicherweise aktualisieren müssen.

* Wenn die Self-Service-Kennwortzurücksetzung bereitgestellt wird, müssen Benutzer möglicherweise ihre Authentifizierungsmethoden aktualisieren oder überprüfen. Weitere Informationen finden Sie in den [MFA](https://aka.ms/mfatemplates)- und [SSPR](https://aka.ms/ssprtemplates)-Vorlagen für die Endbenutzerkommunikation.

Kommunikation mit externen Benutzern: Probleme haben die größten Auswirkungen auf diese Benutzergruppe. Dies trifft vor allem dann zu, wenn Ihr Sicherheitsstatus andere Regeln für den bedingten Zugriff oder für Risikoprofile für externe Partner festlegt. Stellen Sie sicher, dass externe Partner den Zeitplan für die Cloudmigration kennen und genügend Zeit für die Teilnahme an einer Pilotbereitstellung haben, mit der alle Flows für die Zusammenarbeit mit externe Benutzern getestet werden. Stellen Sie schließlich sicher, dass Ihre Partner im Falle von Problemen auf den Helpdesk zugreifen können.

## <a name="next-steps"></a>Nächste Schritte

Lesen des Whitepapers zum [Migrieren der Anwendungsauthentifizierung zu Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
Einrichten des [bedingten Zugriffs](../conditional-access/overview.md) und der [mehrstufige Authentifizierung (MFA)](../authentication/concept-mfa-howitworks.md)

Codebeispiel mit Einzelschritten: [AD FS to Azure AD application migration playbook for developers](https://aka.ms/adfsplaybook) (Playbook für Entwickler zur Migration von Anwendungen in AD FS zu Azure AD)
