---
title: Informationen zum Bereitstellen optionaler Ansprüche für Ihre Azure AD-Anwendung | Microsoft-Dokumentation
description: Anleitung zum Hinzufügen benutzerdefinierter oder zusätzlicher Ansprüche zu den SAML 2.0- und JWT-Token (JSON Web Token), die von Azure Active Directory ausgestellt werden.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c0e5035331cbe4f54926f0ae60ae0c5c31f6a9a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119719"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Gewusst wie: Bereitstellen optionaler Ansprüche für Ihre Azure AD-App

Dieses Feature wird von Anwendungsentwicklern verwendet, um anzugeben, welche Ansprüche in Token enthalten sein sollen, die an ihre Anwendung gesendet werden. Sie können optionale Ansprüche zu folgenden Zwecken verwenden:

- Auswählen zusätzlicher Ansprüche, die in Token für Ihre Anwendung aufgenommen werden sollen
- Ändern des Verhaltens bestimmter Ansprüche, die von Azure AD in Token zurückgegeben werden
- Hinzufügen und Zugreifen auf benutzerdefinierte Ansprüche für Ihre Anwendung

Die Listen der Standardansprüche finden Sie in der Anspruchsdokumentation unter [Zugriffstoken](access-tokens.md) und [id_token](id-tokens.md). 

Zwar werden optionale Ansprüche von Token der Formate v1.0 und v2.0 sowie von SAML-Token unterstützt, sie besitzen jedoch den größten Wert bei der Umstellung von v1.0 auf v2.0. Eines der Ziele des [v2.0 Azure AD-Endpunkts](active-directory-appmodel-v2-overview.md) ist eine geringere Tokengröße, um optimale Leistung von Clients zu gewährleisten. Daher sind mehrere Ansprüche, die zuvor in den Zugriffs- und ID-Token enthalten waren, nicht mehr in v2.0-Token vorhanden und müssen für einzelne Anwendungen speziell angefordert werden.

**Tabelle 1: Anwendbarkeit**

| Kontotyp | V1.0-Token | V2.0-Token  |
|--------------|---------------|----------------|
| Persönliches Microsoft-Konto  | –  | Unterstützt|
| Azure AD-Konto      | Unterstützt | Unterstützt |

## <a name="v10-and-v20-optional-claims-set"></a>Gruppe optionaler Ansprüche in v1.0 und v2.0

Die Gruppe optionaler Ansprüche, die standardmäßig zur Verwendung in Anwendungen bereitstehen, sind nachfolgend aufgeführt. Informationen zum Hinzufügen benutzerdefinierter optionaler Ansprüche für Ihre Anwendung finden Sie unter [Verzeichniserweiterungen](#configuring-directory-extension-optional-claims) weiter unten. Beim Hinzufügen von Ansprüchen zum **Zugriffstoken** gilt dies für Zugriffstoken, die *für* die Anwendung (eine Web-API) und nicht *von* der Anwendung angefordert werden. Dadurch wird sichergestellt, dass unabhängig von dem Client, der auf Ihre API zugreift, die richtigen Daten im Zugriffstoken vorhanden sind, die zum Authentifizieren bei Ihrer API verwendet werden.

> [!NOTE]
> Die Mehrzahl dieser Ansprüche kann in JWTs für v1.0- und v2.0-Token, jedoch nicht in SAML-Token eingeschlossen werden. Ausnahmen stellen die Ansprüche dar, die einen entsprechenden Hinweis in der Spalte „Tokentyp“ enthalten. Consumerkonten unterstützen eine Teilmenge dieser Ansprüche, die in der Spalte als „Benutzertyp“ gekennzeichnet sind.  Viele der aufgelisteten Ansprüche gelten nicht für Consumerbenutzer (sie besitzen keinen Mandanten, daher weist `tenant_ctry` keinen Wert auf).  

**Tabelle 2: Gruppe optionaler Ansprüche in v1.0 und v2.0**

| NAME                       |  BESCHREIBUNG   | Tokentyp | Benutzertyp | Notizen  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Zeitpunkt der letzten Authentifizierung des Benutzers. Siehe OpenID Connect-Spezifikation.| JWT        |           |  |
| `tenant_region_scope`      | Region des Ressourcenmandanten | JWT        |           | |
| `home_oid`                 | Bei Gastbenutzern: Die Objekt-ID des Benutzers im Home-Mandanten des Benutzers.| JWT        |           | |
| `sid`                      | Sitzungs-ID, die zur sitzungsbezogenen Abmeldung des Benutzers verwendet wird. | JWT        |  Persönliche Konten und Azure AD-Konten   |         |
| `platf`                    | Geräteplattform    | JWT        |           | Beschränkt auf verwaltete Geräte, die den Gerätetyp überprüfen können.|
| `verified_primary_email`   | Stammt von „PrimaryAuthoritativeEmail“ des Benutzers.      | JWT        |           |         |
| `verified_secondary_email` | Stammt von „SecondaryAuthoritativeEmail“ des Benutzers.   | JWT        |           |        |
| `enfpolids`                | IDs erzwungener Richtlinien. Eine Liste der Richtlinien-IDs, die für den aktuellen Benutzer ausgewertet wurden. | JWT |  |  |
| `vnet`                     | Informationen zum VNET-Spezifizierer | JWT        |           |      |
| `fwd`                      | IP-Adresse.| JWT    |   | Fügt die ursprüngliche IPv4-Adresse des anfordernden Clients hinzu (wenn innerhalb eines VNET). |
| `ctry`                     | Land des Benutzers | JWT |  | Azure AD gibt den optionalen Anspruch `ctry` zurück, wenn er vorhanden ist und der Wert des Anspruchs aus einem standardmäßigen Ländercode mit zwei Buchstaben besteht, z.B. FR, JP oder SZ. |
| `tenant_ctry`              | Land des Ressourcenmandanten | JWT | | |
| `xms_pdl`          | Bevorzugter Datenspeicherort   | JWT | | Für Multi-Geo-Mandanten ist dies ein aus drei Buchstaben bestehender Code, der die geografische Region anzeigt, in der sich der Benutzer befindet. Weitere Informationen finden Sie unter [Azure Active Directory Connect-Synchronisierung: Konfigurieren des bevorzugten Datenspeicherorts für Office 365-Ressourcen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Zum Beispiel: `APC` für Asien-Pazifik. |
| `xms_pl`                   | Bevorzugte Benutzersprache  | JWT ||Die bevorzugte Sprache des Benutzers, falls festgelegt. Wird in Szenarios mit Gastzugriff aus dem Basismandanten abgerufen. Format: Sprachraum-Land (z.B.: en-us) |
| `xms_tpl`                  | Bevorzugte Mandantensprache| JWT | | Die bevorzugte Sprache des Ressourcenmandanten, falls festgelegt. Format: Sprachraum (z.B.: en) |
| `ztdid`                    | ID der Bereitstellung ohne manuelles Eingreifen | JWT | | Die für [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) verwendete Geräteidentität |
| `email`                    | Die adressierbaren E-Mail-Adresse dieses Benutzers, wenn der Benutzer über eine verfügt.  | JWT, SAML | MSA, AAD | Dieser Wert ist standardmäßig enthalten, wenn der Benutzer ein Gast im Mandanten ist.  Für verwaltete Benutzer (Benutzer innerhalb des Mandanten) muss er über diese optionale Anforderung oder – nur in V2. 0 – mit dem OpenID-Bereich angefordert werden.  Für verwaltete Benutzer muss die E-Mail-Adresse im [Office-Verwaltungsportal](https://portal.office.com/adminportal/home#/users) festgelegt sein.| 
| `groups`| Optionale Formatierung für Gruppenansprüche |JWT, SAML| |Wird in Verbindung mit der Einstellung „GroupMembershipClaims“ im [Anwendungsmanifest](reference-app-manifest.md) verwendet, das ebenfalls festgelegt sein muss. Weitere Informationen finden Sie weiter unten unter [Gruppenansprüche](#Configuring-group-optional claims). Weitere Informationen zu Gruppenansprüchen finden Sie unter [Konfigurieren von Gruppenansprüchen](../hybrid/how-to-connect-fed-group-claims.md).
| `acct`             | Benutzerkontostatus im Mandanten. | JWT, SAML | | Wenn der Benutzer dem Mandanten angehört, lautet der Wert `0`. Bei einem Gastbenutzer lautet der Wert `1`. |
| `upn`                      | Anspruch „UserPrincipalName“. | JWT, SAML  |           | Obwohl dieser Anspruch automatisch hinzugefügt wird, können Sie ihn als einen optionalen Anspruch angeben, um zusätzliche Eigenschaften zum Ändern des Verhaltens im Fall eines Gastbenutzer anzufügen.  |

### <a name="v20-optional-claims"></a>Optionale Ansprüche in v2.0

Diese Ansprüche sind in Azure AD v1.0-Token immer enthalten, jedoch nie in v2.0-Token, sofern nicht angefordert. Diese Ansprüche gelten nur für JWTs (ID-Token und Zugriffstoken). 

**Tabelle 3: Nur in v2.0 enthaltene optionale Ansprüche**

| JWT-Anspruch     | NAME                            | BESCHREIBUNG                                | Notizen |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-Adresse                      | Die IP-Adresse, von der aus sich der Client angemeldet hat.   |       |
| `onprem_sid`  | Lokale Sicherheits-ID |                                             |       |
| `pwd_exp`     | Kennwortablaufzeit        | Datum und Uhrzeit, zu der das Kennwort abläuft. |       |
| `pwd_url`     | Kennwortänderungs-URL             | Eine URL, die der Benutzer besuchen kann, um sein Kennwort zu ändern.   |   |
| `in_corp`     | Innerhalb des Unternehmensnetzwerks        | Signalisiert, ob sich der Client aus dem Unternehmensnetzwerk anmeldet. Andernfalls ist der Anspruch nicht enthalten.   |  Basierend auf den Einstellungen für [vertrauenswürdige IP-Adressen](../authentication/howto-mfa-mfasettings.md#trusted-ips) in der MFA.    |
| `nickname`    | Spitzname                        | Ein zusätzlicher Name für den Benutzer, der vom Vor- oder Nachnamen abweicht. | 
| `family_name` | Last Name (Nachname)                       | Gibt den Nachnamen des Benutzers entsprechend der Definition im Benutzerobjekt an. <br>„family_name“: „Miller“ | In MSA und AAD unterstützt   |
| `given_name`  | Vorname                      | Gibt den Vornamen des Benutzers entsprechend der Definition im Benutzerobjekt an.<br>"given_name": "Frank"                   | In MSA und AAD unterstützt  |
| `upn`         | Benutzerprinzipalname | Ein Bezeichner für den Benutzer, der mit dem Parameter „username_hint“ verwendet werden kann.  Dies ist kein dauerhafter Bezeichner für den Benutzer und sollte möglichst nicht zur Datenzuordnung verwendet werden. | Informationen zur Konfiguration des Anspruchs finden Sie weiter unten unter [Zusätzliche Eigenschaften](#additional-properties-of-optional-claims). |

### <a name="additional-properties-of-optional-claims"></a>Zusätzliche Eigenschaften optionaler Ansprüche

Einige optionale Ansprüche können so konfiguriert werden, dass sie auf andere Art zurückgegeben werden. Diese zusätzlichen Eigenschaften unterstützen in erster Linie die Migration lokaler Anwendungen mit unterschiedlichen Datenerwartungen. So ist `include_externally_authenticated_upn_without_hash` beispielsweise hilfreich bei Clients, die keine Hashzeichen (`#`) im UPN verarbeiten können.

**Tabelle 4: Werte zum Konfigurieren optionaler Ansprüche**

| Eigenschaftenname  | Name der zusätzlichen Eigenschaft | BESCHREIBUNG |
|----------------|--------------------------|-------------|
| `upn`          |                          | Kann für SAML- und JWT-Antworten und für v1.0- und v2.0-Token verwendet werden. |
|                | `include_externally_authenticated_upn`  | Bezieht den Gast-UPN ein, wie er im Ressourcenmandanten gespeichert ist. Zum Beispiel, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Wie oben, außer dass die Hashzeichen (`#`) durch Unterstriche (`_`) ersetzt werden, z.B. `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Beispiel für zusätzliche Eigenschaften

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Dieses OptionalClaims-Objekt bewirkt, dass das an den Client zurückgegebene ID-Token einen weiteren UPN mit den zusätzlichen Informationen zum Home- und zum Ressourcenmandanten enthält. Dies ändert den `upn`-Anspruch im Token nur dann, wenn der Benutzer ein Gastbenutzer im Mandanten ist (und einen anderen Identitätsanbieter für die Authentifizierung verwendet). 

## <a name="configuring-optional-claims"></a>Konfigurieren optionaler Ansprüche

Sie können optionale Ansprüche für Ihre Anwendung konfigurieren, indem Sie das Anwendungsmanifest ändern (siehe Beispiel unten). Weitere Informationen finden Sie im Artikel [Grundlegendes zum Azure AD-Anwendungsmanifest](reference-app-manifest.md).

> [!IMPORTANT]
> Zugriffstoken werden **immer** mit dem Manifest der Ressource und nicht des Clients generiert.  Daher entspricht die Ressource in der Anforderung `...scope=https://graph.microsoft.com/user.read...` dem Graph.  Folglich wird das Zugriffstoken unter Verwendung des Graph-Manifests und nicht des Clientmanifests erstellt.  Wenn Sie das Manifest für Ihre Anwendung ändern, hat das daher keinen Einfluss auf die Token für den Graph.  Um zu überprüfen, ob Ihre Änderungen an `accessToken` wirksam sind, fordern Sie ein Token für Ihre Anwendung, aber keine andere App an.  

**Beispielschema:**

```json
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": false
             }
      ],
      "accessToken": [
             {
                    "name": "ipaddr", 
                    "essential": false
              }
      ],
      "saml2Token": [
              {
                    "name": "upn", 
                    "essential": false
               },
               {
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": false
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>„OptionalClaims“-Typ

Deklariert die von einer Anwendung angeforderten optionalen Ansprüche. Eine Anwendung kann optionale Ansprüche so konfigurieren, dass sie in jedem der drei Tokentypen (ID-Token, Zugriffstoken, SAML 2-Token) zurückgegeben werden, die vom Sicherheitstokendienst empfangen werden können. Die Anwendung kann einen anderen Satz optionaler Ansprüche für die Rückgabe im jeweiligen Tokentyp konfigurieren. Die „OptionalClaims“-Eigenschaft der Anwendungsentität ist ein „OptionalClaims“-Objekt.

**Tabelle 5: Eigenschaften des Typs „OptionalClaims“**

| NAME        | Type                       | BESCHREIBUNG                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Sammlung (OptionalClaim) | Die optionalen Ansprüche, die im JWT-ID-Token zurückgegeben werden. |
| `accessToken` | Sammlung (OptionalClaim) | Die optionalen Ansprüche, die im JWT-Zugriffstoken zurückgegeben werden. |
| `saml2Token`  | Sammlung (OptionalClaim) | Die optionalen Ansprüche, die im SAML-Token zurückgegeben werden.   |

### <a name="optionalclaim-type"></a>„OptionalClaim“-Typ

Enthält einen optionalen Anspruch, der einer Anwendung oder einem Dienstprinzipal zugeordnet ist. Die „idToken“-, „accessToken“- und „saml2Token“-Eigenschaften des [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type)-Typs ist eine Sammlung von „OptionalClaim“.
Wenn durch einen bestimmten Anspruch unterstützt, können Sie auch das Verhalten von „OptionalClaim“ mithilfe des Felds „AdditionalProperties“ ändern.

**Tabelle 6: Eigenschaften des Typs „OptionalClaim“**

| NAME                 | Type                    | BESCHREIBUNG                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Der Name des optionalen Anspruchs.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Die Quelle (Verzeichnisobjekt) des Anspruchs. Es gibt vordefinierte Ansprüche und benutzerdefinierte Ansprüche aus Erweiterungseigenschaften. Wenn der Quellwert „null“ ist, ist der Anspruch ein vordefinierter optionaler Anspruch. Wenn der Quellwert „user“ ist, ist der Wert in der „name“-Eigenschaft die Erweiterungseigenschaft aus dem Benutzerobjekt. |
| `essential`            | Edm.Boolean             | Wenn der Wert „true“ ist, ist der vom Client angegebene Anspruch erforderlich, um eine reibungslose Autorisierung für die jeweilige vom Endbenutzer angeforderte Aufgabe sicherzustellen. Der Standardwert ist „false“.                                                                                                             |
| `additionalProperties` | Sammlung (Edm.String) | Zusätzliche Eigenschaften des Anspruchs. Wenn eine Eigenschaft in dieser Sammlung vorhanden ist, ändert sie das Verhalten des optionalen Anspruchs, der in der „name“-Eigenschaft angegeben ist.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurieren von optionalen Ansprüchen für die Verzeichniserweiterung

Zusätzlich zu den optionalen Standardansprüchen können Sie auch Token so konfigurieren, dass sie Verzeichnisschemaerweiterungen enthalten. Weitere Informationen finden Sie unter [Verzeichnisschemaerweiterungen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Diese Funktion ist nützlich, um zusätzliche Benutzerinformationen anzufügen, die von Ihrer App verwendet werden können, z. B. einen zusätzlichen Bezeichner oder eine wichtige Konfigurationsoption, die vom Benutzer festgelegt wurde. 

> [!Note]
> Verzeichnisschemaerweiterungen sind eine auf AAD beschränkte Funktion, d. h., wenn Ihr Anwendungsmanifest eine benutzerdefinierte Erweiterung erfordert und sich ein MSA-Benutzer bei Ihrer App anmeldet, werden diese Erweiterungen nicht zurückgegeben.

### <a name="directory-extension-formatting"></a>Formatierung der Verzeichniserweiterung

Verwenden Sie bei Erweiterungsattributen den vollständigen Namen der Erweiterung (im Format `extension_<appid>_<attributename>`) im Anwendungsmanifest. Die `<appid>` muss mit der ID der Anwendung übereinstimmen, die den Anspruch anfordert. 

Innerhalb des JWT werden diese Ansprüche mit dem folgenden Namensformat ausgegeben: `extn.<attributename>`

Innerhalb der SAML-Token werden diese Ansprüche mit dem folgenden URI-Format ausgegeben: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Konfigurieren von optionalen Gruppenansprüchen

   > [!NOTE]
   > Die Fähigkeit, Gruppennamen für Benutzer und Gruppen auszugeben, die lokal synchronisiert werden, befindet sich noch in der Phase der öffentlichen Vorschau.

Dieser Abschnitt behandelt die Konfigurationsoptionen unter den optionalen Ansprüchen, um die in Gruppenansprüchen verwendeten Gruppenattribute von der Standardgruppen-ObjectID in von lokalem Windows Active Directory synchronisierte Attribute zu ändern.
> [!IMPORTANT]
> Unter [Konfigurieren von Gruppenansprüchen für Anwendungen mit Azure Active Directory](../hybrid/how-to-connect-fed-group-claims.md) finden Sie weitere Informationen, einschließlich wichtiger Einschränkungen für die öffentliche Vorschau von Gruppenansprüchen aus lokalen Attributen.

1. Wählen Sie im Portal folgende Optionen aus: Azure Active Directory > Anwendungsregistrierungen > Anwendung auswählen > Manifest.

2. Aktivieren Sie Gruppenmitgliedschaftsansprüche, indem Sie den groupMembershipClaim-Wert ändern.

   Gültige Werte sind:

   - All
   - SecurityGroup
   - DistributionList
   - DirectoryRole

   Beispiel:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Standardmäßig werden ObjectIDs von Gruppen im Wert des Gruppenanspruchs ausgegeben.  Um den Anspruchswert so zu ändern, dass er lokale Gruppenattribute enthält, oder um den Anspruchstyp zu „role“ zu ändern, verwenden Sie die Konfiguration für optionale Ansprüche wie folgt:

3. Legen Sie optionale Ansprüche für die Gruppennamenkonfiguration fest.

   Wenn die Gruppen im Token die lokalen AD-Gruppenattribute im Abschnitt mit optionalen Ansprüchen enthalten sollen, geben Sie an, welcher optionale Anspruch des Tokentyps angewendet werden soll. Geben Sie außerdem den Namen des angeforderten optionalen Anspruchs sowie weitere gewünschte Eigenschaften an.  Es können mehrere Tokentypen aufgelistet werden:

   - idToken für das OIDC-ID-Token
   - accessToken für das OAuth/OIDC-Zugriffstoken
   - Saml2Token für SAML-Token

   > [!NOTE]
   > Der Typ „Saml2Token“ gilt für Token sowohl im SAML1.1- als auch im SAML2.0-Format.

   Ändern Sie für jeden relevanten Tokentyp den Gruppenanspruch so, dass der Abschnitt „Optionale Ansprüche“ im Manifest verwendet wird. Das Schema für optionale Ansprüche sieht folgendermaßen aus:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schema für optionale Ansprüche | Wert |
   |----------|-------------|
   | **name:** | Muss „groups“ lauten. |
   | **source:** | Wird nicht verwendet. Auslassen oder NULL angeben. |
   | **essential:** | Wird nicht verwendet. Auslassen oder FALSE angeben. |
   | **additionalProperties:** | Liste zusätzlicher Eigenschaften.  Gültige Optionen sind „sam_account_name“, „dns_domain_and_sam_account_name“, „netbios_domain_and_sam_account_name“, „emit_as_roles“. |

   In „additionalProperties“ ist nur eine dieser Optionen erforderlich: „sam_account_name“, „dns_domain_and_sam_account_name“, „netbios_domain_and_sam_account_name“.  Wenn mehrere dieser Optionen vorhanden sind, wird die erste verwendet, alle weiteren werden ignoriert.

   Einige Anwendungen erfordern Gruppeninformationen über Benutzer im Rollenanspruch.  Um den Anspruchstyp von einem Gruppenanspruch zu einem Rollenanspruch zu ändern, fügen Sie „emit_as_roles“ zu den zusätzlichen Eigenschaften hinzu.  Die Gruppenwerte werden im Rollenanspruch ausgegeben.

   > [!NOTE]
   > Wenn „emit_as_roles“ verwendet wird, sind konfigurierte Anwendungsrollen, denen der Benutzer zugewiesen ist, nicht im Rollenanspruch enthalten.

**Beispiele:** Ausgeben von Gruppen als Gruppennamen in OAuth-Zugriffstoken im Format „dnsDomainName\sAMAccountName“

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Ausgeben von Gruppennamen, die im Format „netbiosDomain\sAMAccountName“ als Rollenanspruch in SAML- und OIDC-ID-Token zurückgegeben werden sollen:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }

 ```

## <a name="optional-claims-example"></a>Beispiel für optionale Ansprüche

In diesem Abschnitt können Sie ein Szenario durchgehen, um zu erfahren, wie Sie optionale Ansprüche für Ihre Anwendung verwenden können.
Es sind mehrere Optionen für das Aktualisieren der Eigenschaften in der Identitätskonfiguration einer Anwendung zum Aktivieren und Konfigurieren optionaler Ansprüche verfügbar:
-   Sie können das Anwendungsmanifest ändern. Im folgenden Beispiel wird diese Methode zum Ausführen der Konfiguration verwendet. Lesen Sie zuerst das Dokument [Grundlegendes zum Azure AD-Anwendungsmanifest](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest), um eine Einführung in das Manifest zu erhalten.
-   Sie können zum Aktualisieren Ihrer Anwendung auch eine Anwendung schreiben, die die [Graph-API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) nutzt. Die [Referenz für Entitäten und komplexe Typen](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) im Graph-API-Referenzhandbuch kann beim Konfigurieren der optionalen Ansprüche hilfreich sein.

**Beispiel:** Im folgenden Beispiel ändern Sie das Manifest einer Anwendung, um Ansprüche zu Zugriffs-, ID- und SAML-Token hinzuzufügen, die für die Anwendung vorgesehen sind.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie nach der Authentifizierung Ihren Azure AD-Mandanten aus, indem Sie ihn in der oberen rechten Ecke der Seite auswählen.
1. Wählen Sie auf der linken Seite **App-Registrierungen** aus.
1. Suchen Sie in der Liste nach der Anwendung, für die Sie optionale Ansprüche konfigurieren möchten, und klicken Sie darauf.
1. Klicken Sie auf der Anwendungsseite auf **Manifest**, um den Inline-Manifest-Editor zu öffnen. 
1. Das Manifest kann mit diesem Editor direkt bearbeitet werden. Das Manifest folgt dem Schema für die [Anwendungsentität](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) und wird nach dem Speichern automatisch formatiert. Neue Elemente werden der `OptionalClaims`-Eigenschaft hinzugefügt.

    ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
            "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
            "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }

    ```

    In diesem Fall wurden verschiedene optionale Ansprüche den einzelnen Tokentypen hinzugefügt, die von der Anwendung empfangen werden können. Die ID-Token enthalten jetzt den UPN für Verbundbenutzer in vollständiger Form (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Die Zugriffstoken, die andere Clients für diese Anwendung anfordern, enthalten jetzt den Anspruch „auth_time“. Die SAML-Token enthalten jetzt die Verzeichnisschemaerweiterung „skypeId“ (in diesem Beispiel lautet die App-ID für diese App „ab603c56068041afb2f6832e2a17e237“). Die SAML-Token stellen die Skype-ID als `extension_skypeId` bereit.

1. Wenn Sie das Manifest aktualisiert haben, klicken Sie auf **Speichern**, um das Manifest zu speichern.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Standardansprüche, die von Azure AD bereitgestellt werden.

- [ID-Token](id-tokens.md)
- [Zugriffstoken](access-tokens.md)
