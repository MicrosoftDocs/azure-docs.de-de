---
title: Grundlegendes zum Azure Active Directory-App-Manifest | Microsoft-Dokumentation
description: Sie erhalten ausführliche Informationen über das Azure Active Directory-App-Manifest, das die Identitätskonfiguration einer Anwendung in einem Azure AD-Mandanten darstellt, und für die OAuth-Autorisierung, den Zustimmungsprozess und andere Zwecke genutzt wird.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d369891624256e98ba8d46168cc9c10c41d37b8d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235240"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-App-Manifest

Das Anwendungsmanifest enthält eine Definition aller Attribute eines Anwendungsobjekts auf der Microsoft Identity Platform. Darüber hinaus dient es als Mechanismus für die Aktualisierung des Anwendungsobjekts. Weitere Informationen zur Anwendungsentität und zum zugehörigen Schema finden Sie in der [Dokumentation zur Anwendungsentität der Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Sie können die Attribute einer App über das Azure-Portal oder programmgesteuert mit der [REST-API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) oder [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications) konfigurieren. Es gibt aber einige Szenarien, in denen Sie das App-Manifest bearbeiten müssen, um das Attribut einer App zu konfigurieren. Zu diesen Szenarien gehören:

* Wenn Sie die App als mehrinstanzenfähige Azure AD-App und für persönliche Microsoft-Konten registriert haben, können Sie die unterstützten Microsoft-Konten nicht über die Benutzeroberfläche ändern. In diesem Fall müssen Sie den unterstützten Kontotyp mithilfe des Anwendungsmanifest-Editors ändern.
* Wenn Sie Berechtigungen und Rollen definieren müssen, die von Ihrer App unterstützt werden, müssen Sie das Anwendungsmanifest ändern.

## <a name="configure-the-app-manifest"></a>Konfigurieren des App-Manifests

Konfigurieren Sie das Anwendungsmanifest wie folgt:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie den Dienst **Azure Active Directory** und anschließend **App-Registrierungen** aus.
1. Wählen Sie die App aus, die Sie konfigurieren möchten.
1. Wählen Sie auf der Seite **Übersicht** der App den Abschnitt **Manifest**. Ein webbasierter Manifest-Editor wird geöffnet, mit dem Sie das Manifest im Portal bearbeiten können. Optional können Sie **Herunterladen** wählen, um das Manifest lokal zu bearbeiten, und dann **Hochladen** verwenden, um es wieder auf Ihre Anwendung anzuwenden.

## <a name="manifest-reference"></a>Manifestreferenz

> [!NOTE]
> Wird nach der **Beschreibung** nicht die Spalte **Beispielwert** angezeigt, maximieren Sie das Browserfenster, und scrollen oder wischen Sie, bis die Spalte **Beispielwert** angezeigt wird.

| Schlüssel  | Werttyp | BESCHREIBUNG  | Beispielwert |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Nullable Int32 | Gibt die Zugriffstokenversion an, die von der Ressource erwartet wird. Dadurch ändern sich die Version und das Format des erzeugten JWT unabhängig vom Endpunkt oder Client, der zum Anfordern des Zugriffstokens verwendet wird.<br/><br/>Der verwendete Endpunkt, v1.0 oder v2.0, wird vom Client ausgewählt und wirkt sich nur auf die Version der ID-Token aus. Ressourcen müssen `accesstokenAcceptedVersion` explizit konfigurieren, um das unterstützte Zugriffstokenformat anzugeben.<br/><br/>Mögliche Werte für `accesstokenAcceptedVersion` sind 1, 2 oder null. Wenn der Wert null ist, wird standardmäßig 1 verwendet. Dies entspricht dem v1.0-Endpunkt. <br/><br/>Wenn `signInAudience` ist `AzureADandPersonalMicrosoftAccount`, muss der Wert `2` sein  | `2` |
| `addIns` | Sammlung | Definiert ein benutzerdefiniertes Verhalten, mit dem eine App in bestimmten Kontexten von einem Verbraucherdienst aufgerufen werden kann. Beispielsweise kann für Anwendungen, die Dateidatenströme rendern können, die addIns-Eigenschaft für die Funktionalität „FileHandler“ festgelegt werden. Dadurch können Dienste wie Office 365 die Anwendung im Kontext eines Dokuments aufrufen, an dem der Benutzer arbeitet. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boolean | Gibt den Fallbackanwendungstyp zurück. Azure AD leitet den Anwendungstyp standardmäßig von „replyUrlsWithType“ ab. Es gibt bestimmte Szenarien, in den Azure AD den Client-App-Typ nicht bestimmen kann (etwa beim [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3)-Fluss, in dem die HTTP-Anforderung ohne URL-Umleitung ausgeführt wird). In diesen Fällen interpretiert Azure AD den Anwendungstyp basierend auf dem Wert dieser Eigenschaft. Wird für diesen Wert TRUE verwendet, wird der Fallbackanwendungstyp als öffentlicher Client festgelegt, etwa als installierte App, die auf einem mobilen Gerät ausgeführt wird. Der Standardwert ist FALSE. Das bedeutet, dass der Fallbackanwendungstyp ein vertraulicher Client ist, etwa eine Web-App. | `false` |
| `availableToOtherTenants` | Boolean | TRUE, wenn die Anwendung für andere Mandanten freigegeben wird; andernfalls FALSE. <br><br> _Hinweis: Nur in der (älteren) Benutzeroberfläche „App-Registrierungen“ verfügbar. Wird in der aktuellen Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `signInAudience` ersetzt._ | |
| `appId` | Zeichenfolge | Gibt den eindeutigen Bezeichner für die App an, die einer App von Azure AD zugewiesen wird. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Sammlung | Gibt Auflistung der Rollen an, die von einer App deklariert werden können. Diese Rollen können Benutzern, Gruppen oder Dienstprinzipalen zugewiesen werden. Weitere Beispiele und Informationen finden Sie unter [Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](howto-add-app-roles-in-azure-ad-apps.md). | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | Zeichenfolge | Der Anzeigename für die App. <br><br> _Hinweis: Nur in der (älteren) Benutzeroberfläche „App-Registrierungen“ verfügbar. Wird in der aktuellen Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `name` ersetzt._ | `"MyRegisteredApp"` |
| `errorUrl` | Zeichenfolge | Nicht unterstützt. | |
| `groupMembershipClaims` | Zeichenfolge | Konfiguriert den in einem Benutzer- oder OAuth 2.0-Zugriffstoken ausgegebenen Anspruch `groups`, der von der App erwartet wird. Um dieses Attribut festzulegen, verwenden Sie einen der folgenden gültigen Zeichenfolgenwerte:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (für Sicherheitsgruppen und Azure AD-Rollen)<br/>- `"All"` (damit werden alle Sicherheitsgruppen, Verteilergruppen und Azure AD-Verzeichnisrollen abgerufen, in denen der angemeldete Benutzer ein Mitglied ist) | `"SecurityGroup"` |
| `homepage` | Zeichenfolge | Die URL zur Startseite der Anwendung. <br><br> _Hinweis: Nur in der (älteren) Benutzeroberfläche „App-Registrierungen“ verfügbar. Wird in der aktuellen Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `signInUrl` ersetzt._ | `"https://MyRegisteredApp"` |
| `objectId` | Zeichenfolge | Ein eindeutiger Bezeichner für die App im Verzeichnis. <br><br> _Hinweis: Nur in der (älteren) Benutzeroberfläche „App-Registrierungen“ verfügbar. Wird in der aktuellen Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `id` ersetzt._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | Zeichenfolge | Die optionalen Ansprüche, die im Token vom Sicherheitstokendienst für diese spezifische App zurückgegeben werden.<br>Zu diesem Zeitpunkt können Apps, die sowohl persönliche Konten als auch Azure AD unterstützen (registriert über das App-Registrierungsportal), keine optionalen Ansprüche verwenden. Apps, die nur für Azure AD unter Verwendung des Endpunkts v2.0 registriert sind, können jedoch die optionalen Ansprüche abrufen, die sie im Manifest angefordert haben. Weitere Informationen finden Sie unter [Optionale Ansprüche](active-directory-optional-claims.md). | `null` |
| `id` | Zeichenfolge | Ein eindeutiger Bezeichner für die App im Verzeichnis. Diese ID ist nicht der Bezeichner, der verwendet wird, um die App in einer Protokolltransaktion zu bezeichnen. Sie dient dazu, in Verzeichnisabfragen auf das Objekt zu verweisen. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | Benutzerdefinierte URIs, die eine Web-App innerhalb des zugehörigen Azure AD-Mandanten oder innerhalb einer überprüften benutzerdefinierten Domäne eindeutig identifizieren, wenn es sich um eine mehrinstanzenfähige App handelt. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | Zeichenfolge | Gibt die Links zu den Nutzungsbedingungen und Datenschutzbestimmungen der App an. Die Nutzungsbedingungen und Datenschutzbestimmungen werden auf der Oberfläche für die Benutzerzustimmung angezeigt. Weitere Informationen finden Sie unter [Vorgehensweise: Nutzungsbedingungen und Datenschutzerklärung für registrierte Azure Active Directory-Apps](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Sammlung | Enthält Verweise auf von der App zugewiesene Anmeldeinformationen, auf Zeichenfolgen basierende gemeinsame geheime Schlüssel und X.509-Zertifikate. Diese Anmeldeinformationen werden beim Anfordern von Zugriffstokens verwendet (wenn die App nicht als Ressource, sondern als Client agiert). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | Wird zur Bündelung der Zustimmung verwendet, wenn Sie eine aus zwei Teilen bestehende Lösung haben (eine Client-App und eine benutzerdefinierte Web-API-App). Wenn Sie hier die App-ID der Client-App eingeben, muss der Benutzer nur einmal seine Zustimmung zur Verwendung der Client-App geben. Azure AD weiß, dass die Zustimmung für die Clientanwendung implizit die Zustimmung zur Verwendung der Web-API beinhaltet, und stellt die Dienstprinzipale für die Client- und Web-API automatisch bereit. Die Client- und Web-API-App müssen beim selben Mandanten registriert sein. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | Zeichenfolge | Schreibgeschützter Wert, der auf die CDN-URL des Logos verweist, das im Portal hochgeladen wurde | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | Zeichenfolge | Die URL zum Abmelden von der App. | `"https://MyRegisteredAppLogout"` |
| `name` | Zeichenfolge | Der Anzeigename für die App. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Boolean | Dieser Wert gibt an, ob die Web-App Zugriffstoken des impliziten OAuth 2.0-Flusses anfordern kann. Die Standardeinstellung ist „false“. Dieses Flag wird für browserbasierte Apps wie JavaScript-basierte Single-Page-Webanwendungen verwendet. Wenn Sie weitere Informationen benötigen, geben Sie im Inhaltsverzeichnis `OAuth 2.0 implicit grant flow` ein, und sehen Sie sich die Themen zum impliziten Flow an. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boolean | Dieser Wert gibt an, ob die Web-App ID-Token des impliziten OAuth 2.0-Flusses anfordern kann. Die Standardeinstellung ist „false“. Dieses Flag wird für browserbasierte Apps wie JavaScript-basierte Single-Page-Webanwendungen verwendet. | `false` |
| `oauth2Permissions` | Sammlung | Gibt die Sammlung von OAuth 2.0-Berechtigungsbereichen an, die die Web-API-App (Ressource) für Client-Apps verfügbar macht. Diese Berechtigungsbereiche können Client-Apps im Zuge der Zustimmung gewährt werden. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code>|
| `oauth2RequiredPostResponse` | Boolean | Gibt an, ob Azure AD im Rahmen von OAuth 2.0-Tokenanforderungen POST-Anforderungen zulässt (im Gengensatz zu GET-Anforderungen). Beim Standartwert „false“ sind nur GET-Anforderungen zulässig. | `false` |
| `parentalControlSettings` | Zeichenfolge | `countriesBlockedForMinors` gibt an, die Länder/Regionen an, in denen die App für Minderjährige blockiert wird.<br>`legalAgeGroupRule` gibt die Regel für die rechtliche Altersgruppe an, die für Benutzer der App gilt. Dieser Wert kann auf `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` oder `BlockMinors` festgelegt werden.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>}</code> |
| `passwordCredentials` | Sammlung | Siehe Beschreibung für die `keyCredentials`-Eigenschaft. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>]</code> |
| `preAuthorizedApplications` | Sammlung | Listet Anwendungen und angeforderte Berechtigungen für die implizite Einwilligung auf. Ein Administrator muss seine Einwilligung zur Anwendung gegeben haben. Bei „preAuthorizedApplications“ muss der Benutzer den angeforderten Berechtigungen nicht zustimmen. Für die in „preAuthorizedApplications“ aufgelisteten Berechtigungen ist keine Benutzereinwilligung erforderlich. Weitere angeforderte Berechtigungen, die nicht in „preAuthorizedApplications“ aufgeführt sind, erfordern jedoch die Benutzereinwilligung. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Boolean | Gibt an, ob es sich bei dieser Anwendung um einen öffentlichen Client handelt (beispielsweise eine installierte Anwendung auf einem mobilen Gerät). <br><br> _Hinweis: Nur in der (älteren) Benutzeroberfläche „App-Registrierungen“ verfügbar. Wird in der aktuellen Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `allowPublicClient` ersetzt._ | |
| `publisherDomain` | Zeichenfolge | Die überprüfte Herausgeberdomäne für die Anwendung. Schreibgeschützt. | https://www.contoso.com |
| `replyUrls` | Zeichenfolgenarray | Diese Eigenschaft, die mehrere Werte zulässt, enthält die Liste der registrierten „redirect_uri“-Werte, die von Azure AD bei der Rückgabe von Token als Ziele akzeptiert werden. <br><br> _Hinweis: Nur in der (älteren) Benutzeroberfläche „App-Registrierungen“ verfügbar. Wird in der aktuellen Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `replyUrlsWithType` ersetzt._ | |
| `replyUrlsWithType` | Sammlung | Diese Eigenschaft, die mehrere Werte zulässt, enthält die Liste der registrierten „redirect_uri“-Werte, die von Azure AD bei der Rückgabe von Token als Ziele akzeptiert werden. Jeder URI-Wert muss einen zugehörigen App-Typwert enthalten. Folgende Typwerte werden unterstützt: `Web` und `InstalledClient`. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Sammlung | Mit dynamischer Einwilligung steuert `requiredResourceAccess` die Einwilligungsoberfläche für Administratoren und Benutzer, die statische Einwilligung verwenden. Die Oberfläche für die Benutzerzustimmung für den Allgemeinfall wird jedoch nicht gesteuert.<br>`resourceAppId` ist der eindeutige Bezeichner für die Ressource, auf die die App zugreifen muss. Dieser Wert muss mit der „appId“ identisch sein, die für die Zielressourcen-App deklariert wurde.<br>`resourceAccess` ist ein Array, das die OAuth 2.0-Berechtigungsbereiche und App-Rollen auflistet, welche die App für die jeweilige Ressource erfordert. Enthält die `id`- und `type`-Werte der jeweiligen Ressourcen. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `samlMetadataUrl` | Zeichenfolge | Die URL zu den SAML-Metadaten für die App. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | Zeichenfolge | Gibt die URL zur Startseite der App an. | `https://MyRegisteredApp` |
| `signInAudience` | Zeichenfolge | Gibt an, welche Microsoft-Konten für die aktuelle Anwendung unterstützt werden. Folgende Werte werden unterstützt:<ul><li>**AzureADMyOrg:** Benutzer mit einem Geschäfts-, Schul- oder Unikonto von Microsoft im Azure AD-Mandanten meiner Organisation (einzelner Mandant)</li><li>**AzureADMultipleOrgs:** Benutzer mit einem Geschäfts-, Schul- oder Unikonto von Microsoft im Azure AD-Mandanten einer beliebigen Organisation (mehrinstanzenfähig)</li> <li>**AzureADandPersonalMicrosoftAccount:** Benutzer mit einem persönlichen Microsoft-Konto oder einem Geschäfts-, Schul- oder Unikonto im Azure AD-Mandanten einer beliebigen Organisation</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | Benutzerdefinierte Zeichenfolgen, die zum Kategorisieren und Identifizieren der Anwendung verwendet werden können | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Häufige Probleme

### <a name="manifest-limits"></a>Grenzwerte für das Manifest

Ein Anwendungsmanifest verfügt über mehrere Attribute, die als Sammlungen bezeichnet werden, beispielsweise „approles“, „keycredentials“, „knownClientApplications“, „identifierUris“, „redirectUris“, „requiredResourceAccess“ und „oauth2Permissions“. Im gesamten Anwendungsmanifest für jede Anwendung wurde die Gesamtanzahl von Einträgen in allen kombinierten Sammlungen auf 1200 begrenzt. Wenn Sie im Anwendungsmanifest bereits 100 Umleitungs-URIs angegeben haben, können Sie in allen anderen kombinierten Sammlungen, aus denen das Manifest besteht, nur noch 1.100 weitere Einträge verwenden.

> [!NOTE]
> Falls Sie versuchen, dem Anwendungsmanifest mehr als 1.200 Einträge hinzuzufügen, erhalten Sie möglicherweise die Fehlermeldung **Fehler beim Aktualisieren der Anwendung xxxxxx. Fehlerdetails: Die Größe des Manifests hat den Grenzwert überschritten. Verringern Sie die Anzahl der Werte, und wiederholen Sie die Anforderung.**

### <a name="unsupported-attributes"></a>Nicht unterstützte Attribute

Das Anwendungsmanifest stellt das Schema des zugrunde liegenden Anwendungsmodells in Azure AD dar. Wenn sich das zugrunde liegende Schema weiterentwickelt, wird der Manifest-Editor von Zeit zu Zeit dem neuen Schema entsprechend aktualisiert. Daher können Sie feststellen, dass neue Attribute im Anwendungsmanifest angezeigt werden. In seltenen Fällen kann es vorkommen, dass Sie eine syntaktische oder semantische Änderung der vorhandenen Attribute feststellen oder dass ein zuvor vorhandenes Attribut nicht mehr unterstützt wird. Beispielsweise werden neue Attribute in [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) angezeigt, die in der (älteren) Benutzeroberfläche „App-Registrierungen“ unter einem anderen Namen bekannt sind.


| App-Registrierungen (Vorgängerversion)| App-Registrierungen           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Die Beschreibung dieser Attribute finden Sie im Abschnitt [Manifestreferenz](#manifest-reference).

Wenn Sie versuchen, ein zuvor heruntergeladenes Manifest hochzuladen, wird möglicherweise eine der folgenden Fehlermeldungen angezeigt. Dies liegt wahrscheinlich daran, dass der Manifest-Editor dann eine neuere Version des Schemas unterstützt, die nicht mit der übereinstimmt, die Sie hochladen möchten.

- **Fehler beim Aktualisieren der Anwendung xxxxxx. Fehlerdetail: Ungültiger Objektbezeichner ‚nicht definiert‘. [].**
- **Fehler beim Aktualisieren der Anwendung xxxxxx. Fehlerdetail: Mindestens ein Eigenschaftswert ist ungültig. [].**
- **Fehler beim Aktualisieren der Anwendung xxxxxx. Fehlerdetail: ‚availableToOtherTenants‘ darf in dieser API-Version nicht zur Aktualisierung festgelegt werden. [].**
- **Fehler beim Aktualisieren der Anwendung xxxxxx. Fehlerdetail: Aktualisierungen der replyUrls-Eigenschaft ist für diese Anwendung nicht zulässig. Verwenden Sie stattdessen die Eigenschaft ‚replyUrlsWithType‘. [].**
- **Fehler beim Aktualisieren der Anwendung xxxxxx. Fehlerdetail: Es wurde ein Wert ohne einen Typnamen gefunden, und es ist kein erwarteter Typ verfügbar. Wenn das Modell angegeben wird, muss jeder Wert in der Nutzlast einen Typ besitzen. Dieser kann entweder in der Nutzlast oder explizit durch den Aufrufer angegeben oder implizit aus dem übergeordneten Wert abgeleitet werden. []**

Wenn eine dieser Fehlermeldungen angezeigt wird, wird Folgendes empfohlen:

1. Bearbeiten Sie die Attribute einzeln im Manifest-Editor, anstatt ein zuvor heruntergeladenes Manifest hochzuladen. In der Tabelle [Manifestreferenz](#manifest-reference) können Sie die Syntax und Semantik alter und neuer Attribute einsehen und damit die gewünschten Attribute bearbeiten. 
1. Wenn Sie in Ihrem Workflow die Manifeste zur späteren Verwendung im Quellrepository speichern müssen, sollten Sie ein Rebase der gespeicherten Manifeste in Ihrem Repository mit dem in der Benutzeroberfläche **App-Registrierungen** angezeigten Manifest ausführen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Beziehungen zwischen den Anwendungs- und Dienstprinzipalobjekten einer App finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure AD](app-objects-and-service-principals.md).
* Definitionen einiger wichtiger Konzepte für Entwickler von Microsoft Identity Platform finden Sie unter [Microsoft Identity Platform – Glossar für Entwickler](developer-glossary.md).

Bitte geben Sie uns über den folgenden Kommentarabschnitt Ihr Feedback, um uns bei der Verbesserung unserer Inhalte zu unterstützen.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
