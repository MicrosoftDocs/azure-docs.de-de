---
title: Grundlegendes zum Azure Active Directory-App-Manifest
titleSuffix: Microsoft identity platform
description: Sie erhalten ausführliche Informationen über das Azure Active Directory-App-Manifest, das die Identitätskonfiguration einer Anwendung in einem Azure AD-Mandanten darstellt, und für die OAuth-Autorisierung, den Zustimmungsprozess und andere Zwecke genutzt wird.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 04/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 1f4e1def81fc79ec159ce6be825793a9bd8d0ce7
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286958"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-App-Manifest

Das Anwendungsmanifest enthält eine Definition aller Attribute eines Anwendungsobjekts auf der Microsoft Identity Platform. Darüber hinaus dient es als Mechanismus für die Aktualisierung des Anwendungsobjekts. Weitere Informationen zur Anwendungsentität und zum zugehörigen Schema finden Sie in der [Dokumentation zur Anwendungsentität der Graph-API](/graph/api/resources/application).

Sie können die Attribute einer App über das Azure-Portal oder programmgesteuert mit der [REST-API](/graph/api/resources/application) oder [PowerShell](/powershell/module/azuread#applications) konfigurieren. Es gibt aber einige Szenarien, in denen Sie das App-Manifest bearbeiten müssen, um das Attribut einer App zu konfigurieren. Zu diesen Szenarien gehören:

* Wenn Sie die App als mehrinstanzenfähige Azure AD-App und für persönliche Microsoft-Konten registriert haben, können Sie die unterstützten Microsoft-Konten nicht über die Benutzeroberfläche ändern. In diesem Fall müssen Sie den unterstützten Kontotyp mithilfe des Anwendungsmanifest-Editors ändern.
* Wenn Sie Berechtigungen und Rollen definieren müssen, die von Ihrer App unterstützt werden, müssen Sie das Anwendungsmanifest ändern.

## <a name="configure-the-app-manifest"></a>Konfigurieren des App-Manifests

Konfigurieren Sie das Anwendungsmanifest wie folgt:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Suchen Sie nach dem **Azure Active Directory** -Dienst, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen** aus.
1. Wählen Sie die App aus, die Sie konfigurieren möchten.
1. Wählen Sie auf der Seite **Übersicht** der App den Abschnitt **Manifest**. Ein webbasierter Manifest-Editor wird geöffnet, mit dem Sie das Manifest im Portal bearbeiten können. Optional können Sie **Herunterladen** wählen, um das Manifest lokal zu bearbeiten, und dann **Hochladen** verwenden, um es wieder auf Ihre Anwendung anzuwenden.

## <a name="manifest-reference"></a>Manifestreferenz

In diesem Abschnitt werden die Attribute im Anwendungsmanifest beschrieben.

### <a name="id-attribute"></a>id-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| id | String |

Ein eindeutiger Bezeichner für die App im Verzeichnis. Diese ID ist nicht der Bezeichner, der verwendet wird, um die App in einer Protokolltransaktion zu bezeichnen. Sie dient dazu, in Verzeichnisabfragen auf das Objekt zu verweisen.

Beispiel:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| accessTokenAcceptedVersion | Nullable Int32 |

Gibt die Zugriffstokenversion an, die von der Ressource erwartet wird. Dieser Parameter ändert die Version und das Format des erzeugten JWT unabhängig vom Endpunkt oder Client, der zum Anfordern des Zugriffstokens verwendet wird.

Der verwendete Endpunkt, v1.0 oder v2.0, wird vom Client ausgewählt und wirkt sich nur auf die Version der ID-Token aus. Ressourcen müssen `accesstokenAcceptedVersion` explizit konfigurieren, um das unterstützte Zugriffstokenformat anzugeben.

Mögliche Werte für `accesstokenAcceptedVersion` sind 1, 2 oder null. Wenn der Wert null (0) ist, ist dieser Parameter standardmäßig 1. Dies entspricht dem v1.0-Endpunkt.

Wenn `signInAudience` auf `AzureADandPersonalMicrosoftAccount` festgelegt ist, muss der Wert `2` lauten.

Beispiel:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| addIns | Collection |

Definiert ein benutzerdefiniertes Verhalten, mit dem eine App in bestimmten Kontexten von einem Verbraucherdienst aufgerufen werden kann. Beispielsweise kann für Anwendungen, die Dateidatenströme rendern können, die `addIns`-Eigenschaft für die Funktionalität „FileHandler“ festgelegt werden. Dieser Parameter ermöglicht Diensten wie Microsoft 365, die Anwendung im Kontext eines Dokuments aufzurufen, an dem der Benutzer arbeitet.

Beispiel:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| allowPublicClient | Boolean |

Gibt den Fallbackanwendungstyp zurück. Azure AD leitet den Anwendungstyp standardmäßig von „replyUrlsWithType“ ab. Es gibt bestimmte Szenarien, in denen Azure AD den Client-App-Typ nicht bestimmen kann. Ein solches Szenario ist beispielsweise der [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3)-Flow, bei dem die HTTP-Anforderung ohne URL-Umleitung erfolgt. In diesen Fällen interpretiert Azure AD den Anwendungstyp basierend auf dem Wert dieser Eigenschaft. Wird für diesen Wert TRUE verwendet, wird der Fallbackanwendungstyp als öffentlicher Client festgelegt, etwa als installierte App, die auf einem mobilen Gerät ausgeführt wird. Der Standardwert ist FALSE. Das bedeutet, dass der Fallbackanwendungstyp ein vertraulicher Client ist, etwa eine Web-App.

Beispiel:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>availableToOtherTenants-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| availableToOtherTenants | Boolean |

Ist auf „true“ festgelegt, wenn die Anwendung für andere Mandanten freigegeben wird; andernfalls „false“.

> [!NOTE]
> Dieses Attribut ist nur in der Benutzeroberfläche **App-Registrierungen (Vorgängerversion)** verfügbar. In der Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `signInAudience` ersetzt.

### <a name="appid-attribute"></a>appId-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| appId | String |

Gibt den eindeutigen Bezeichner für die App an, die einer App von Azure AD zugewiesen wird.

Beispiel:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| appRoles | Collection |

Gibt Auflistung der Rollen an, die von einer App deklariert werden können. Diese Rollen können Benutzern, Gruppen oder Dienstprinzipalen zugewiesen werden. Weitere Beispiele und Informationen finden Sie unter [Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](howto-add-app-roles-in-azure-ad-apps.md).

Beispiel:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>displayName-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| displayName | String |

Der Anzeigename für die App.

> [!NOTE]
> Dieses Attribut ist nur in der Benutzeroberfläche **App-Registrierungen (Vorgängerversion)** verfügbar. In der Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `name` ersetzt.

### <a name="errorurl-attribute"></a>errorUrl-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| errorUrl | String |

Nicht unterstützt.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
|groupMembershipClaims | String |

Konfiguriert den in einem Benutzer- oder OAuth 2.0-Zugriffstoken ausgegebenen Anspruch `groups`, der von der App erwartet wird. Um dieses Attribut festzulegen, verwenden Sie einen der folgenden gültigen Zeichenfolgenwerte:

- `"None"`
- `"SecurityGroup"` (für Sicherheitsgruppen und Azure AD-Rollen)
- `"ApplicationGroup"` (diese Option umfasst nur Gruppen, die der Anwendung zugewiesen sind)
- `"All"` (damit werden alle Sicherheitsgruppen, Verteilergruppen und Azure AD-Verzeichnisrollen abgerufen, in denen der angemeldete Benutzer ein Mitglied ist)

Beispiel:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>homepage-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| homepage |String |

Die URL zur Startseite der Anwendung.

> [!NOTE]
> Dieses Attribut ist nur in der Benutzeroberfläche **App-Registrierungen (Vorgängerversion)** verfügbar. In der Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `signInUrl` ersetzt.

### <a name="objectid-attribute"></a>objectId-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
|objectId | String |

Ein eindeutiger Bezeichner für die App im Verzeichnis.

Dies ist nur in der Benutzeroberfläche **App-Registrierungen (Vorgängerversion)** verfügbar. In der Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `id` ersetzt.

Beispiel:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>optionalClaims-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| optionalClaims | String |

Die optionalen Ansprüche, die im Token vom Sicherheitstokendienst für diese spezifische App zurückgegeben werden.

Zu diesem Zeitpunkt können Apps, die sowohl persönliche Konten als auch Azure AD unterstützen (registriert über das App-Registrierungsportal), keine optionalen Ansprüche verwenden. Apps, die nur für Azure AD unter Verwendung des Endpunkts v2.0 registriert sind, können jedoch die optionalen Ansprüche abrufen, die sie im Manifest angefordert haben. Weitere Informationen finden Sie unter [Optionale Ansprüche](active-directory-optional-claims.md).

Beispiel:

```json
    "optionalClaims": null,
```



### <a name="identifieruris-attribute"></a>identifierUris-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| identifierUris | String Array |

Benutzerdefinierte URIs, die eine Web-App innerhalb des zugehörigen Azure AD-Mandanten oder innerhalb einer überprüften benutzerdefinierten Domäne eindeutig identifizieren, wenn es sich um eine mehrinstanzenfähige App handelt.

Beispiel:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| informationalUrls | String |

Gibt die Links zu den Nutzungsbedingungen und Datenschutzbestimmungen der App an. Die Nutzungsbedingungen und Datenschutzbestimmungen werden auf der Oberfläche für die Benutzerzustimmung angezeigt. Weitere Informationen finden Sie unter [Vorgehensweise: Nutzungsbedingungen und Datenschutzerklärung für registrierte Azure Active Directory-Apps](howto-add-terms-of-service-privacy-statement.md).

Beispiel:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>keyCredentials-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| keyCredentials | Collection |

Enthält Verweise auf von der App zugewiesene Anmeldeinformationen, auf Zeichenfolgen basierende gemeinsame geheime Schlüssel und X.509-Zertifikate. Diese Anmeldeinformationen werden beim Anfordern von Zugriffstoken verwendet (wenn die App nicht als Ressource, sondern als Client agiert).

Beispiel:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| knownClientApplications | String Array |

Wird zur Bündelung der Zustimmung verwendet, wenn Sie eine aus zwei Teilen bestehende Lösung haben (eine Client-App und eine benutzerdefinierte Web-API-App). Wenn Sie hier die App-ID der Client-App eingeben, muss der Benutzer nur einmal seine Zustimmung zur Verwendung der Client-App geben. Azure AD weiß, dass die Zustimmung zum Client der impliziten Zustimmung zur Web-API entspricht. Dienstprinzipale werden automatisch sowohl für den Client als auch die Web-API bereitgestellt. Die Client- und Web-API-App müssen beim selben Mandanten registriert sein.

Beispiel:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| logoUrl | String |

Schreibgeschützter Wert, der auf die CDN-URL des Logos verweist, das im Portal hochgeladen wurde

Beispiel:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| logoutUrl | String |

Die URL zum Abmelden von der App.

Beispiel:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>name-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| name | String |

Der Anzeigename für die App.

Beispiel:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolean |

Dieser Wert gibt an, ob die Web-App Zugriffstoken des impliziten OAuth 2.0-Flusses anfordern kann. Die Standardeinstellung ist „false“. Dieses Flag wird für browserbasierte Apps, z. B. JavaScript-basierte Single-Page-Anwendungen, verwendet. Wenn Sie weitere Informationen benötigen, geben Sie im Inhaltsverzeichnis `OAuth 2.0 implicit grant flow` ein, und sehen Sie sich die Themen zum impliziten Flow an.

Beispiel:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolean |

Dieser Wert gibt an, ob die Web-App ID-Token des impliziten OAuth 2.0-Flusses anfordern kann. Die Standardeinstellung ist „false“. Dieses Flag wird für browserbasierte Apps, z. B. JavaScript-basierte Single-Page-Anwendungen, verwendet.

Beispiel:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| oauth2Permissions | Collection |

Gibt die Sammlung von OAuth 2.0-Berechtigungsbereichen an, die die Web-API-App (Ressource) für Client-Apps verfügbar macht. Diese Berechtigungsbereiche können Client-Apps im Zuge der Zustimmung gewährt werden.

Beispiel:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolean |

Gibt an, ob Azure AD im Rahmen von OAuth 2.0-Tokenanforderungen POST-Anforderungen zulässt (im Gengensatz zu GET-Anforderungen). Beim Standartwert „false“ sind nur GET-Anforderungen zulässig.

Beispiel:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| parentalControlSettings | String |

- `countriesBlockedForMinors` gibt die Länder/Regionen an, in denen die App für Minderjährige blockiert wird.
- `legalAgeGroupRule` gibt die Regel für die rechtliche Altersgruppe an, die für Benutzer der App gilt. Dieser Wert kann auf `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` oder `BlockMinors` festgelegt werden.

Beispiel:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| passwordCredentials | Collection |

Siehe Beschreibung für die `keyCredentials`-Eigenschaft.

Beispiel:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| preAuthorizedApplications | Collection |

Listet Anwendungen und angeforderte Berechtigungen für die implizite Einwilligung auf. Ein Administrator muss seine Einwilligung zur Anwendung gegeben haben. Bei „preAuthorizedApplications“ muss der Benutzer den angeforderten Berechtigungen nicht zustimmen. Für die in „preAuthorizedApplications“ aufgelisteten Berechtigungen ist keine Benutzereinwilligung erforderlich. Weitere angeforderte Berechtigungen, die nicht in „preAuthorizedApplications“ aufgeführt sind, erfordern jedoch die Benutzereinwilligung.

Beispiel:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>publicClient-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| publicClient | Boolean|

Gibt an, ob es sich bei dieser Anwendung um einen öffentlichen Client handelt (beispielsweise eine installierte Anwendung auf einem mobilen Gerät).

Diese Eigenschaft ist nur in der Benutzeroberfläche **App-Registrierungen (Vorgängerversion)** verfügbar. In der Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `allowPublicClient` ersetzt.

### <a name="publisherdomain-attribute"></a>publisherDomain-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| publisherDomain | String |

Die überprüfte Herausgeberdomäne für die Anwendung. Schreibgeschützt.

Beispiel:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>replyUrls-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| replyUrls | Zeichenfolgenarray |

Diese Eigenschaft, die mehrere Werte zulässt, enthält die Liste der registrierten „redirect_uri“-Werte, die von Azure AD bei der Rückgabe von Token als Ziele akzeptiert werden.

Diese Eigenschaft ist nur in der Benutzeroberfläche **App-Registrierungen (Vorgängerversion)** verfügbar. In der Benutzeroberfläche [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) durch `replyUrlsWithType` ersetzt.

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| replyUrlsWithType | Collection |

Diese Eigenschaft, die mehrere Werte zulässt, enthält die Liste der registrierten „redirect_uri“-Werte, die von Azure AD bei der Rückgabe von Token als Ziele akzeptiert werden. Jeder URI-Wert muss einen zugehörigen App-Typwert enthalten. Folgende Typwerte werden unterstützt:

- `Web`
- `InstalledClient`
- `Spa`

Erfahren Sie mehr über [Einschränkungen bei der Antwort-URL](./reply-url.md).

Beispiel:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| requiredResourceAccess | Collection |

Mit dynamischer Einwilligung steuert `requiredResourceAccess` die Einwilligungsoberfläche für Administratoren und Benutzer, die statische Einwilligung verwenden. Dieser Parameter steuert jedoch nicht die Oberfläche für die Benutzerzustimmung für den Allgemeinfall.

- `resourceAppId` ist der eindeutige Bezeichner für die Ressource, auf die die App zugreifen muss. Dieser Wert muss mit der „appId“ identisch sein, die für die Zielressourcen-App deklariert wurde.
- `resourceAccess` ist ein Array, das die OAuth 2.0-Berechtigungsbereiche und App-Rollen auflistet, welche die App für die jeweilige Ressource erfordert. Enthält die `id`- und `type`-Werte der jeweiligen Ressourcen.

Beispiel:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| samlMetadataUrl | String |

Die URL zu den SAML-Metadaten für die App.

Beispiel:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| signInUrl | String |

Gibt die URL zur Startseite der App an.

Beispiel:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| signInAudience | String |

Gibt an, welche Microsoft-Konten für die aktuelle Anwendung unterstützt werden. Diese Werte werden unterstützt:
- `AzureADMyOrg`: Benutzer mit einem Geschäfts-, Schul- oder Unikonto von Microsoft im Azure AD-Mandanten meiner Organisation (z. B. einzelner Mandant)
- `AzureADMultipleOrgs`: Benutzer mit einem Geschäfts-, Schul- oder Unikonto von Microsoft im Azure AD-Mandanten einer beliebigen Organisation (z. B. mehrere Mandanten)
- `AzureADandPersonalMicrosoftAccount`: Benutzer mit einem persönlichen Microsoft-Konto oder einem Geschäfts-, Schul- oder Unikonto im Azure AD-Mandanten einer beliebigen Organisation
- `PersonalMicrosoftAccount`: Persönliche Konten, die für die Anmeldung bei Diensten wie Xbox und Skype verwendet werden

Beispiel:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>tags-Attribut

| Schlüssel | Werttyp |
| :--- | :--- |
| tags | String Array  |

Benutzerdefinierte Zeichenfolgen, die zum Kategorisieren und Identifizieren der Anwendung verwendet werden können

Beispiel:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Häufige Probleme

### <a name="manifest-limits"></a>Grenzwerte für das Manifest

Ein Anwendungsmanifest verfügt über mehrere Attribute, die als Sammlungen bezeichnet werden, beispielsweise „appRoles“, „keyCredentials“, „knownClientApplications“, „identifierUris“, „redirectUris“, „requiredResourceAccess“ und „oauth2Permissions“. Im gesamten Anwendungsmanifest für jede Anwendung wurde die Gesamtanzahl von Einträgen in allen kombinierten Sammlungen auf 1200 begrenzt. Wenn Sie vorher im Anwendungsmanifest 100 Umleitungs-URIs angeben, können Sie in allen anderen kombinierten Sammlungen, aus denen das Manifest besteht, nur noch 1.100 weitere Einträge verwenden.

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

Wenn Sie versuchen, ein zuvor heruntergeladenes Manifest hochzuladen, wird möglicherweise eine der folgenden Fehlermeldungen angezeigt. Die Ursache dieses Fehlers ist wahrscheinlich, dass der Manifest-Editor dann eine neuere Version des Schemas unterstützt, die nicht mit der übereinstimmt, die Sie hochladen möchten.

* „Fehler beim Aktualisieren der Anwendung xxxxxx“. Fehlerdetail: Ungültiger Objektbezeichner ‚nicht definiert‘. []."
* „Fehler beim Aktualisieren der Anwendung xxxxxx.“ Fehlerdetail: Mindestens ein Eigenschaftswert ist ungültig. []."
* „Fehler beim Aktualisieren der Anwendung xxxxxx.“ Fehlerdetail: „availableToOtherTenants“ darf in dieser API-Version nicht zur Aktualisierung festgelegt werden. []."
* „Fehler beim Aktualisieren der Anwendung xxxxxx.“ Fehlerdetail: Aktualisierungen der replyUrls-Eigenschaft sind für diese Anwendung nicht zulässig. Verwenden Sie stattdessen die Eigenschaft ‚replyUrlsWithType‘. []."
* „Fehler beim Aktualisieren der Anwendung xxxxxx.“ Fehlerdetail: Es wurde ein Wert ohne einen Typnamen gefunden, und es ist kein erwarteter Typ verfügbar. Wenn das Modell angegeben wird, muss jeder Wert in der Nutzlast einen Typ haben. Dieser kann entweder in der Nutzlast oder explizit durch den Aufrufer angegeben oder implizit aus dem übergeordneten Wert abgeleitet werden. []"

Wenn eine dieser Fehlermeldungen angezeigt wird, werden folgende Aktionen empfohlen:

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
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
