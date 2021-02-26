---
title: 'Tutorial: Entwickeln eines SCIM-Endpunkts für die Benutzerbereitstellung in Apps von Azure AD'
description: Das System für die domänenübergreifende Identitätsverwaltung (System for Cross-domain Identity Management, SCIM) standardisiert die automatische Benutzerbereitstellung. In diesem Tutorial erfahren Sie, wie Sie einen SCIM-Endpunkt entwickeln, Ihre SCIM-API mit Azure Active Directory integrieren und mit der Automatisierung der Bereitstellung von Benutzern und Gruppen in Ihren Cloudanwendungen beginnen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 02/01/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 35efcd4059ab654178fb87c133a6f64721caf7d2
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989060"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Tutorial: Entwickeln eines SCIM-Endpunkts und Planen seiner Bereitstellung

Als Anwendungsentwickler können Sie die SCIM-Benutzerverwaltungs-API (System for Cross-Domain Identity Management, System für die domänenübergreifende Identitätsverwaltung) verwenden, um die automatische Bereitstellung von Benutzern und Gruppen zwischen Ihrer Anwendung und Azure AD (AAD) zu aktivieren. In diesem Artikel erfahren Sie, wie Sie einen SCIM-Endpunkt erstellen und in den AAD-Bereitstellungsdienst integrieren. Die SCIM-Spezifikation bietet ein allgemeines Benutzerschema für die Bereitstellung. Bei der Verwendung mit Verbundstandards wie SAML oder OpenID Connect bietet SCIM Administratoren eine auf Standards basierende End-to-End-Lösung für die Zugriffsverwaltung.

![Bereitstellen von Azure AD für eine App mit SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM ist eine standardisierte Definition von zwei Endpunkten: einem `/Users`- und einem `/Groups`-Endpunkt. Es verwendet allgemeine REST-Verben zum Erstellen, Aktualisieren und Löschen von Objekten und ein vordefiniertes Schema für allgemeine Attribute wie Gruppenname, Benutzername, Vorname, Nachname und E-Mail-Adresse. Apps, die eine SCIM 2.0 REST-API bieten, können den Aufwand für die Arbeit mit einer proprietären Benutzerverwaltungs-API reduzieren oder eliminieren. So ist beispielsweise jeder konforme SCIM-Client in der Lage, eine HTTP POST-Anforderung für ein JSON-Objekt an den `/Users`-Endpunkt zu senden, um einen neuen Benutzereintrag zu erstellen. Anstatt eine leicht abweichende API für dieselben grundlegenden Aktionen zu benötigen, können Apps, die dem SCIM-Standard entsprechen, sofort die Vorteile bereits vorhandener Clients, Tools und Codes nutzen. 

Das in SCIM 2.0 definierte Standard-Benutzerobjektschema und die REST-APIs für die Verwaltung (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) ermöglichen eine einfachere Integration von Identitätsanbietern und Apps. Anwendungsentwickler, die einen SCIM-Endpunkt erstellen, können die Integration mit jedem SCIM-konformen Client durchführen, ohne selbst Anpassungen vornehmen zu müssen.

Die Automatisierung der Bereitstellung für eine Anwendung erfordert die Erstellung und Integration eines SCIM-Endpunkts mit dem Azure AD-SCIM-Client. Verwenden Sie die folgenden Schritte, um mit der Bereitstellung von Benutzern und Gruppen in Ihrer Anwendung zu beginnen. 
    
1. Entwerfen Ihres Benutzer- und Gruppenschemas

   Identifizieren Sie die Objekte und Attribute Ihrer Anwendung, um zu ermitteln, wie sie mit dem von der AAD-SCIM-Implementierung unterstützten Benutzer- und Gruppenschema zusammenhängen.

1. Verstehen der AAD-SCIM-Implementierung

   Machen Sie sich mit der Implementierung des AAD-SCIM-Clients vertraut, um die Behandlung sowie die Antworten Ihrer SCIM-Protokollanforderungen zu modellieren.

1. Erstellen eines SCIM-Endpunkts

   Ein Endpunkt muss SCIM 2.0-kompatibel sein, damit er in den AAD-Bereitstellungsdienst integriert werden kann. Optional können Sie Bibliotheken und Codebeispiele der Microsoft Common Language Infrastructure (CLI) verwenden, um Ihren Endpunkt zu erstellen. Diese Beispiele dienen nur als Referenz und zu Testzwecken. Sie sollten nicht als Abhängigkeiten in Ihrer Produktions-App verwendet werden.

1. Integrieren Ihres SCIM-Endpunkts in den AAD-SCIM-Client 

   Wenn Ihre Organisation eine Drittanbieteranwendung verwendet, um ein von AAD unterstütztes SCIM 2.0-Profil zu implementieren, können Sie sowohl die Bereitstellung als auch die Aufhebung der Bereitstellung von Benutzern und Gruppen schnell automatisieren.

1. Veröffentlichen Ihrer Anwendung im AAD-Anwendungskatalog 

   Erleichtern Sie es Ihren Kunden, Ihre Anwendung zu entdecken und die Bereitstellung einfach zu konfigurieren. 

![Schritte zur Integration eines SCIM-Endpunkts mit Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="design-your-user-and-group-schema"></a>Entwerfen Ihres Benutzer- und Gruppenschemas

Jede Anwendung benötigt verschiedene Attribute, um einen Benutzer oder eine Gruppe erstellen zu können. Beginnen Sie Ihre Integration, indem Sie die erforderlichen Objekte (Benutzer, Gruppen) und Attribute (Name, Vorgesetzter, Positionsbezeichnung, usw.) identifizieren, die Ihre Anwendung benötigt. 

Mit dem SCIM-Standard wird ein Schema zum Verwalten von Benutzern und Gruppen definiert. 

Für das **Kernbenutzerschema** sind nur drei Attribute erforderlich (alle anderen sind optional):

- `id`: Vom Dienstanbieter definierter Bezeichner
- `externalId`: Vom Client definierter Bezeichner
- `meta`: *Schreibgeschützte*, vom Dienstanbieter verwaltete Metadaten

Neben dem **Kernbenutzerschema** definiert der SCIM-Standard eine **Unternehmensbenutzererweiterung** mit einem Modell für die Erweiterung des Benutzerschemas, um die Anforderungen Ihrer Anwendung zu erfüllen. 

Wenn für Ihre Anwendung beispielsweise sowohl die E-Mail-Adresse als auch der Vorgesetzte eines Benutzers benötigt wird, können Sie mit dem **Kernschema** die E-Mail-Adresse des Benutzers und mit dem **Unternehmensbenutzerschema** den Vorgesetzten des Benutzers erfassen.

Gehen Sie zum Entwerfen Ihres Schemas wie folgt vor:

1. Listen Sie die Attribute auf, die für Ihre Anwendung erforderlich sind, und kategorisieren Sie sie als Attribute für die Authentifizierung (z. B. Anmeldename und E-Mail-Adresse), als Attribute für die Verwaltung des Benutzerlebenszyklus (z. B. Status/aktiv) und als andere Attribute, die benötigt werden, damit die Anwendung funktioniert (z. B. Vorgesetzter, Tag).

1. Überprüfen Sie, ob die Attribute bereits im **Kernbenutzerschema** oder im **Unternehmensbenutzerschema** definiert sind. Falls nicht, muss eine entsprechende Erweiterung des Benutzerschemas definiert werden. Im folgenden Beispiel wurde der Benutzer erweitert, um die Bereitstellung eines Benutzertags (`tag`) zu ermöglichen.

1. Ordnen Sie SCIM-Attribute den Benutzerattributen in Azure AD zu. Wenn für eines der Attribute, die Sie in Ihrem SCIM-Endpunkt definiert haben, keine eindeutige Entsprechung im Azure AD-Benutzerschema vorhanden ist, bitten Sie den Mandantenadministrator, das Schema zu erweitern, oder verwenden Sie ein Erweiterungsattribut, wie im Anschluss für die Eigenschaft `tags` gezeigt:

|Erforderliches App-Attribut|Zugeordnetes SCIM-Attribut|Zugeordnetes Azure AD-Attribut|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.familyName|surName|
|workMail|emails[type eq “work”].value|E-Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|aktiv|isSoftDeleted (berechneter Wert, wird für Benutzer nicht gespeichert)|

**Beispielliste mit erforderlichen Attributen**

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen@testuser.com",
     "id": "48af03ac28ad4fb88478",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
}   
```
**Durch JSON-Nutzdaten definiertes Beispielschema**

> [!NOTE]
> Die JSON-Darstellung enthält neben den für die Anwendung erforderlichen Attributen auch die erforderlichen Attribute `id`, `externalId` und `meta`.

Die Unterscheidung zwischen `/User` und `/Group` ist hilfreich, um Standardbenutzerattribute in Azure AD der SCIM-RFC zuzuordnen. Weitere Informationen finden Sie unter [Tutorial: Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md).


| Azure Active Directory-Benutzer | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktiv |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

**Beispielliste mit Benutzer- und Gruppenattributen**

| Azure Active Directory-Gruppe | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

**Beispielliste mit Gruppenattributen**

> [!NOTE]
> Es ist nicht erforderlich, sowohl Benutzer als auch Gruppen oder alle hier gezeigten Attribute zu unterstützen. Hierbei handelt es sich lediglich um eine Referenz, die zeigt, wie Attribute in Azure AD häufig Eigenschaften im SCIM-Protokoll zugeordnet sind. 

Im SCIM-RFC sind verschiedene Endpunkte definiert. Sie können mit dem Endpunkt `/User` beginnen und dann Erweiterungen vornehmen. 

|Endpunkt|BESCHREIBUNG|
|--|--|
|/User|Führt CRUD-Vorgänge für ein Benutzerobjekt aus.|
|/Group|Führt CRUD-Vorgänge für ein Gruppenobjekt aus.|
|/ServiceProviderConfig|Stellt Einzelheiten zu den unterstützten Features des SCIM-Standards bereit, z. B. die unterstützten Ressourcen und die Authentifizierungsmethode.|
|/ResourceTypes|Definiert die Metadaten für jede Ressource|
|/Schemas|Der Satz von unterstützten Attributen kann bei einzelnen Clients und Dienstanbietern unterschiedlich sein. Ein Dienstanbieter enthält möglicherweise `name`, `title` und `emails`, während ein anderer `name`, `title` und `phoneNumbers` verwendet. Der Endpunkt „/Schemas“ ermöglicht die Erkennung der unterstützten Attribute.|
|/Bulk|Massenvorgänge (bulk operations) ermöglichen das Ausführen von Vorgängen für eine große Sammlung von Ressourcenobjekten in einem einzigen Schritt (z. B. Aktualisieren der Mitgliedschaften für eine große Gruppe).|

**Beispielliste mit Endpunkten**

> [!NOTE]
> Verwenden Sie den Endpunkt `/Schemas`, wenn Sie benutzerdefinierte Attribute unterstützen möchten oder wenn sich Ihr Schema häufig ändert, da ein Client so automatisch das aktuelle Schema abrufen kann. Verwenden Sie den Endpunkt `/Bulk`, um Gruppen zu unterstützen.

## <a name="understand-the-aad-scim-implementation"></a>Verstehen der AAD-SCIM-Implementierung

Wenn Sie eine SCIM 2.0-Benutzerverwaltungs-API unterstützen möchten, erhalten Sie in diesem Abschnitt Informationen zur Implementierung des AAD-SCIM-Clients sowie zur Modellierung der Behandlung und Antworten Ihrer SCIM-Protokollanforderungen.

> [!IMPORTANT]
> Das Verhalten der Azure AD-SCIM-Implementierung wurde zuletzt am 18. Dezember 2018 aktualisiert. Informationen zu den Änderungen finden Sie unter [Einhaltung des SCIM 2.0-Protokolls des Azure AD-Benutzerbereitstellungsdiensts](application-provisioning-config-problem-scim-compatibility.md).

Im Rahmen der [SCIM 2.0-Protokollspezifikation](http://www.simplecloud.info/#Specification) muss Ihre Anwendung die folgenden Anforderungen unterstützen:

|Anforderung|Referenzhinweise (SCIM-Protokoll)|
|-|-|
|Erstellen von Benutzern und optional auch Gruppen|[Abschnitt 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)|
|Ändern von Benutzern oder Gruppen mit PATCH-Anforderungen|[Abschnitt 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). Durch die Unterstützung wird sichergestellt, dass Gruppen und Benutzer auf leistungsstarke Weise bereitgestellt werden.|
|Abrufen einer bekannten Ressource für einen zuvor erstellten Benutzer oder eine zuvor erstellte Gruppe|[Abschnitt 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)|
|Abfragen von Benutzern oder Gruppen|[Abschnitt 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Standardmäßig werden Benutzer anhand ihrer `id` abgerufen und nach `username` und `externalId` abgefragt. Gruppen werden nach `displayName` abgefragt.|
|Abfragen eines Benutzers nach ID und Vorgesetztem|Abschnitt 3.4.2|
|Abfragen von Gruppen nach ID und Mitglied|Abschnitt 3.4.2|
|Filter [excludedAttributes=members](#get-group) beim Abfragen der Gruppenressource|Abschnitt 3.4.2.5|
|Akzeptieren eines einzelnen Bearertokens für die Authentifizierung und Autorisierung von AAD gegenüber Ihrer Anwendung||
|Vorläufiges Löschen eines Benutzers (`active=false`) und Wiederherstellen des Benutzers (`active=true`)|Das Benutzerobjekt sollte unabhängig vom Aktivitätsstatus des Benutzers in einer Anforderung zurückgegeben werden. Der Benutzer sollte nur dann nicht zurückgegeben werden, wenn er endgültig aus der Anwendung gelöscht wurde.|

Verwenden Sie bei der Implementierung eines SCIM-Endpunkts die allgemeinen Richtlinien, um die Kompatibilität mit AAD zu gewährleisten:

* `id` ist eine erforderliche Eigenschaft für alle Ressourcen. Für jede Antwort, die eine Ressource zurückgibt, muss sichergestellt werden, dass jede Ressource diese Eigenschaft aufweist. Eine Ausnahme ist `ListResponse` mit 0 (null) Elementen.
* Eine Antwort auf eine Abfrage-/Filteranforderung muss immer eine `ListResponse` sein.
* Gruppen sind optional, werden jedoch nur unterstützt, wenn die SCIM-Implementierung Anforderungen vom Typ **PATCH** unterstützt.
* Die Antwort von **PATCH** muss nicht die gesamte Ressource enthalten.
* Von Microsoft AAD werden nur die folgenden Operatoren verwendet: `eq`, `and`
* Unterscheiden Sie bei Strukturelementen in SCIM nicht zwischen Groß- und Kleinschreibung, insbesondere bei Vorgangswerten (`op`) für **PATCH** (gemäß Definition im [Abschnitt 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2)). Von AAD werden die Werte von `op` als **Add** (Hinzufügen), **Replace** (Ersetzen) und **Remove** (Entfernen) ausgegeben.
* Von Microsoft AAD werden Anforderungen zum Abrufen eines zufälligen Benutzers und einer zufälligen Gruppe gesendet, um sicherzustellen, dass der Endpunkt und die Anmeldeinformationen gültig sind. Dies wird auch im Rahmen des Flows **Verbindung testen** im [Azure-Portal](https://portal.azure.com) durchgeführt. 
* Das Attribut, nach dem die Ressourcen abgefragt werden können, muss als entsprechendes Attribut für die Anwendung im [Azure-Portal](https://portal.azure.com) festgelegt werden. Weitere Informationen finden Sie unter [Tutorial: Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md).
* HTTPS-Unterstützung auf Ihrem SCIM-Endpunkt

### <a name="user-provisioning-and-deprovisioning"></a>Benutzerbereitstellung und Aufheben der Bereitstellung

Die folgende Abbildung zeigt die Nachrichten, die von AAD an einen SCIM-Dienst gesendet werden, um den Lebenszyklus eines Benutzers im Identitätsspeicher Ihrer Anwendung zu verwalten:  

![Zeigt die Abfolge der Durchführung und Aufhebung einer Benutzerbereitstellung](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Abfolge der Durchführung und Aufhebung einer Benutzerbereitstellung*

### <a name="group-provisioning-and-deprovisioning"></a>Gruppenbereitstellung und Aufheben der Bereitstellung

Die Gruppenbereitstellung und die Aufhebung einer Gruppenbereitstellung sind optional. Die folgende Abbildung zeigt die Nachrichten, die von AAD bei entsprechender Implementierung und Aktivierung an einen SCIM-Dienst gesendet werden, um den Lebenszyklus einer Gruppe im Identitätsspeicher Ihrer Anwendung zu verwalten. Diese Nachrichten unterscheiden sich von den Nachrichten zu Benutzern auf zwei Arten:

* Für Anforderungen zum Abrufen von Gruppen wird angegeben, dass das members-Attribut aus allen Ressourcen ausgeschlossen wird, die als Antwort auf die Anforderung bereitgestellt werden.  
* Bei Anforderungen für die Ermittlung, ob ein Referenzattribut einen bestimmten Wert hat, handelt es sich um Anforderungen zum members-Attribut.  

![Zeigt die Abfolge der Durchführung und Aufhebung einer Gruppenbereitstellung](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Abfolge der Durchführung und Aufhebung einer Gruppenbereitstellung*

### <a name="scim-protocol-requests-and-responses"></a>SCIM-Protokollanforderungen und -antworten
Dieser Abschnitt enthält vom AAD-SCIM-Client ausgegebene SCIM-Beispielanforderungen und erwartete Beispielantworten. Die besten Ergebnisse erzielen Sie, wenn Sie Ihre App so codieren, dass diese Anforderungen in diesem Format verarbeitet und die erwarteten Antworten ausgegeben werden.

> [!IMPORTANT]
> Informationen dazu, wie und wann die im Anschluss beschriebenen Vorgänge durch den AAD-Benutzerbereitstellungsdienst ausgegeben werden, finden Sie unter [Vorgänge während der Bereitstellung: Startzyklus und Inkrementell](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Funktionsweise der Bereitstellung](how-provisioning-works.md).

[Vorgänge für Benutzer](#user-operations)
  - [Benutzer erstellen](#create-user) ([Anforderung](#request) / [Antwort](#response))
  - [Benutzer abrufen](#get-user) ([Anforderung](#request-1) / [Antwort](#response-1))
  - [Benutzer nach Abfrage abrufen](#get-user-by-query) ([Anforderung](#request-2) / [Antwort](#response-2))
  - [Benutzer nach Abfrage abrufen – keine Ergebnisse](#get-user-by-query---zero-results) ([Anforderung](#request-3) / [Antwort](#response-3))
  - [Benutzer aktualisieren [mehrwertige Eigenschaften]](#update-user-multi-valued-properties) ([Anforderung](#request-4) / [Antwort](#response-4))
  - [Benutzer aktualisieren [einwertige Eigenschaften]](#update-user-single-valued-properties) ([Anforderung](#request-5) / [Antwort](#response-5)) 
  - [Benutzer deaktivieren](#disable-user) ([Anforderung](#request-14) / [Antwort](#response-14))
  - [Benutzer löschen](#delete-user) ([Anforderung](#request-6) / [Antwort](#response-6))

[Vorgänge für Gruppen](#group-operations)
  - [Gruppe erstellen](#create-group) ([Anforderung](#request-7) / [Antwort](#response-7))
  - [Gruppe abrufen](#get-group) ([Anforderung](#request-8) / [Antwort](#response-8))
  - [Gruppe nach „displayName“ abrufen](#get-group-by-displayname) ([Anforderung](#request-9) / [Antwort](#response-9))
  - [Gruppe aktualisieren [Nichtmitglieder-Attribute]](#update-group-non-member-attributes) ([Anforderung](#request-10) / [Antwort](#response-10))
  - [Gruppe aktualisieren [Mitglieder hinzufügen]](#update-group-add-members) ([Anforderung](#request-11) / [Antwort](#response-11))
  - [Gruppe aktualisieren [Mitglieder entfernen]](#update-group-remove-members) ([Anforderung](#request-12) / [Antwort](#response-12))
  - [Gruppe löschen](#delete-group) ([Anforderung](#request-13) / [Antwort](#response-13))

### <a name="user-operations"></a>Vorgänge für Benutzer

* Benutzer können anhand des Attributs `userName` oder `email[type eq "work"]` abgefragt werden.  

#### <a name="create-user"></a>Benutzer erstellen

##### <a name="request"></a>Anforderung

*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Antwort

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Benutzer abrufen

###### <a name="request"></a><a name="request-1"></a>Anforderung
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Antwort (Benutzer gefunden)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Anforderung
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Antwort (Benutzer nicht gefunden. Beachten Sie, dass das Detail nicht erforderlich ist, sondern nur den Status angibt.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Benutzer nach Abfrage abrufen

##### <a name="request"></a><a name="request-2"></a>Anforderung

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="get-user-by-query---zero-results"></a>Benutzer nach Abfrage abrufen – keine Ergebnisse

##### <a name="request"></a><a name="request-3"></a>Anforderung

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a><a name="response-3"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-user-multi-valued-properties"></a>Benutzer aktualisieren [mehrwertige Eigenschaften]

##### <a name="request"></a><a name="request-4"></a>Anforderung

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Benutzer aktualisieren [einwertige Eigenschaften]

##### <a name="request"></a><a name="request-5"></a>Anforderung

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Benutzer deaktivieren

##### <a name="request"></a><a name="request-14"></a>Anforderung

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Antwort

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Benutzer löschen

##### <a name="request"></a><a name="request-6"></a>Anforderung

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Antwort

*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>Vorgänge für Gruppen

* Gruppen sollen immer mit einer leeren Mitgliederliste erstellt werden.
* Gruppen können anhand des Attributs `displayName` abgefragt werden.
* Bei einer Aktualisierung der Gruppe mit PATCH-Anforderung sollte in der Antwort *HTTP 204 No Content* angezeigt werden. Es ist nicht ratsam, einen Text mit einer Liste aller Mitglieder zurückzugeben.
* Die Rückgabe aller Mitglieder der Gruppe muss nicht unterstützt werden.

#### <a name="create-group"></a>Erstellen einer Gruppe

##### <a name="request"></a><a name="request-7"></a>Anforderung

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Antwort

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Gruppe abrufen

##### <a name="request"></a><a name="request-8"></a>Anforderung

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Antwort
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Gruppe nach „displayName“ abrufen

##### <a name="request"></a><a name="request-9"></a>Anforderung
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Gruppe aktualisieren [Nichtmitglieder-Attribute]

##### <a name="request"></a><a name="request-10"></a>Anforderung

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Antwort

*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>Gruppe aktualisieren [Mitglieder hinzufügen]

##### <a name="request"></a><a name="request-11"></a>Anforderung

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Antwort

*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>Gruppe aktualisieren [Mitglieder entfernen]

##### <a name="request"></a><a name="request-12"></a>Anforderung

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Antwort

*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>Gruppe löschen

##### <a name="request"></a><a name="request-13"></a>Anforderung

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Antwort

*HTTP/1.1 204 No Content*

### <a name="security-requirements"></a>Sicherheitsanforderungen
**TLS-Protokollversionen**

TLS 1.2 und TLS 1.3 sind die einzigen akzeptierten TLS-Protokollversionen. Andere TLS-Versionen sind nicht zulässig. Alle SSL-Versionen sind unzulässig. 
- Die Größe von RSA-Schlüsseln muss mindestens 2.048 Bit betragen.
- ECC-Schlüssel müssen mindestens 256 Bit groß und mit einer genehmigten elliptischen Kurve erzeugt worden sein

**Schlüssellängen**

Alle Dienste müssen X.509-Zertifikate verwenden, die mit kryptografischen Schlüsseln ausreichender Länge erzeugt wurden. Das bedeutet:

**Verschlüsselungssammlungen**

Alle Dienste müssen so konfiguriert sein, dass sie die folgenden Verschlüsselungssammlungen in exakt der nachstehend angegebenen Reihenfolge verwenden. Hinweis: Wenn Sie nur ein RSA-Zertifikat haben, sind die installierten ECDSA-Verschlüsselungssammlungen wirkungslos. </br>

TLS 1.2-Verschlüsselungssammlungen (Minimum):

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP-Bereiche
Der Azure AD-Bereitstellungsdienst wird zurzeit unter den IP-Bereichen für AzureActiveDirectory betrieben, die [hier](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all) aufgelistet sind. Sie können die unter dem Tag AzureActiveDirectory aufgeführten IP-Adressbereiche hinzufügen, um den Datenverkehr vom Azure AD-Bereitstellungsdienst in Ihre Anwendung zuzulassen. Beachten Sie, dass Sie die IP-Adressbereichsliste sorgfältig auf berechnete Adressen überprüfen müssen. Eine Adresse wie 40.126.25.32 könnte in der IP-Adressbereichsliste als 40.126.0.0/18 dargestellt werden. Sie können die IP-Adressbereichsliste mithilfe der folgenden [API](/rest/api/virtualnetwork/servicetags/list) auch programmgesteuert abrufen.

## <a name="build-a-scim-endpoint"></a>Erstellen eines SCIM-Endpunkts

Nachdem Sie das Schema entworfen und die Azure AD-SCIM-Implementierung verstanden haben, können Sie mit der Entwicklung Ihres SCIM-Endpunkts beginnen. Anstatt bei Null anzufangen und die Implementierung komplett selbst zu erstellen, können Sie auf eine Reihe von Open-Source-SCIM-Bibliotheken zurückgreifen, die von der SCIM-Community veröffentlicht werden.

Eine Anleitung zum Erstellen eines SCIM-Endpunkts, einschließlich Beispielen, finden Sie unter [Tutorial: Entwickeln eines SCIM-Beispielendpunkts](use-scim-to-build-users-and-groups-endpoints.md).

Das Open-Source-[Referenzcodebeispiel](https://aka.ms/SCIMReferenceCode) für .NET Core, das vom Azure AD-Bereitstellungsteam veröffentlicht wird, ist eine solche Ressource, die Ihnen einen schnellen Einstieg in die Entwicklung ermöglicht. Nachdem Sie den SCIM-Endpunkt erstellt haben, sollten Sie ihn testen. Sie können die Sammlung von [Postman-Tests](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) verwenden, die als Teil des Referenzcodes bereitgestellt werden, oder die [oben](#user-operations) aufgeführten Beispielanforderungen/-antworten ausführen.  

   > [!Note]
   > Der Referenzcode soll Ihnen den Einstieg in das Erstellen des SCIM-Endpunkts erleichtern und wird in unveränderter Form zur Verfügung gestellt. Beiträge aus der Community sind stets willkommen, um den Code weiter auszubauen und zu pflegen.

Die Lösung besteht aus zwei Projekten: _Microsoft.SCIM_ und _Microsoft.SCIM.WebHostsample_.

Das Projekt _Microsoft.SCIM_ ist eine Bibliothek und definiert die Komponenten des Webdiensts, welcher der SCIM-Spezifikation entspricht. Er deklariert die Schnittstelle _Microsoft.SCIM.IProvider_. Anforderungen werden in Aufrufe der Methoden des Anbieters übersetzt, die für den Betrieb in einem Identitätsspeicher programmiert werden.

![Aufschlüsselung: Eine Anforderung, die in Aufrufe der Methoden des Anbieters übersetzt wurde](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Das Projekt _Microsoft.SCIM.WebHostSample_ ist eine ASP.NET Core-Webanwendung von Visual Studio, die auf der _leeren_ Vorlage basiert. Dadurch kann der Beispielcode eigenständig bereitgestellt werden und in Containern oder in Internetinformationsdiensten gehostet werden. Implementiert wird auch die Schnittstelle _Microsoft.SCIM.IProvider_, die Klassen im Arbeitsspeicher als Beispielidentitätsspeicher beibehält.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Erstellen eines benutzerdefinierten SCIM-Endpunkts

Der SCIM-Dienst muss über eine HTTP-Adresse und über ein Serverauthentifizierungszertifikat mit einer der folgenden Stammzertifizierungsstellen verfügen:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign
* DST Root CA X3

Der .NET Core-SDK enthält ein HTTPS-Entwicklungszertifikat, das bei der Entwicklung verwendet werden kann. Dieses Zertifikat wird im Rahmen der Erstausführung installiert. Je nach Ausführung der ASP.NET Core-Webanwendung lauscht es an einem anderen Port:

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Weitere Informationen zu HTTPS in ASP.NET Core erhalten Sie über folgenden Link: [Erzwingen von HTTPS in ASP.NET Core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Behandeln der Endpunktauthentifizierung

Anforderungen aus Azure Active Directory enthalten ein OAuth 2.0-Bearertoken. Alle Dienste, die die Anforderung empfangen, müssen den Aussteller als Azure Active Directory für den erwarteten Azure Active Directory-Mandanten authentifizieren.

Im Token wird der Aussteller durch einen iss-Anspruch identifiziert (z. B. `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`). In diesem Beispiel wird die Basisadresse des Anspruchswerts (`https://sts.windows.net`) zum Identifizieren von Azure Active Directory als Aussteller verwendet, während das Segment mit der relativen Adresse (_cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_) ein eindeutiger Bezeichner des Azure Active Directory-Mandanten ist, für den das Token ausgestellt wurde.

Zielgruppe für das Token ist die Anwendungsvorlagen-ID für die Anwendung im Katalog. Jede in einem einzelnen Mandanten registrierte Anwendung empfängt mit SCIM-Anforderungen möglicherweise denselben `iss`-Anspruch. Die Anwendungsvorlagen-ID für alle benutzerdefinierten Apps lautet _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_. Das vom Azure AD-Bereitstellungsdienst generierte Token sollte nur für Tests verwendet werden. Es darf nicht in Produktionsumgebungen verwendet werden.

Im Beispielcode werden Anforderungen mit dem Microsoft.AspNetCore.Authentication.JwtBearer-Paket authentifiziert. Mit dem folgenden Code wird erzwungen, dass Anforderungen an Endpunkte des Diensts mit dem von Azure Active Directory für einen bestimmten Mandanten ausgegebenen Bearertoken authentifiziert werden:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Ein Bearertoken ist auch zur Verwendung der bereitgestellten [Postman-Tests](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) und zum lokalen Debuggen mithilfe von „localhost“ erforderlich. Für den Beispielcode werden ASP.NET Core-Umgebungen verwendet, damit im Entwicklungsstadium die Authentifizierungsoptionen geändert und selbstsignierte Token verwendet werden können.

Weitere Informationen zu mehreren Umgebungen in ASP.NET Core finden Sie unter [Verwenden von mehreren Umgebungen in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

Mit dem folgenden Code wird erzwungen, dass Anforderungen an Endpunkte des Diensts mit einem Bearertoken, das mit einem benutzerdefinierten Schlüssel signiert wurde, authentifiziert werden:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        })
        .AddJwtBearer(options =>
        {
            options.TokenValidationParameters =
                new TokenValidationParameters
                {
                    ValidateIssuer = false,
                    ValidateAudience = false,
                    ValidateLifetime = false,
                    ValidateIssuerSigningKey = false,
                    ValidIssuer = "Microsoft.Security.Bearer",
                    ValidAudience = "Microsoft.Security.Bearer",
                    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                };
        });
    }
...
```

Senden Sie eine GET-Anforderung an den Tokencontroller, um ein gültiges Bearertoken abzurufen. Die Methode _GenerateJSONWebToken_ ist für die Erstellung eines Tokens verantwortlich, das den für die Entwicklung konfigurierten Parametern entspricht:

```csharp
private string GenerateJSONWebToken()
{
    // Create token key
    SymmetricSecurityKey securityKey =
        new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
    SigningCredentials credentials =
        new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

    // Set token expiration
    DateTime startTime = DateTime.UtcNow;
    DateTime expiryTime = startTime.AddMinutes(120);

    // Generate the token
    JwtSecurityToken token =
        new JwtSecurityToken(
            "Microsoft.Security.Bearer",
            "Microsoft.Security.Bearer",
            null,
            notBefore: startTime,
            expires: expiryTime,
            signingCredentials: credentials);

    string result = new JwtSecurityTokenHandler().WriteToken(token);
    return result;
}
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Vorgehensweise beim Bereitstellen und beim Aufheben der Bereitstellung von Benutzern

***Beispiel 1: Abfragen des Diensts nach einem passenden Benutzer***

Von Azure Active Directory (AAD) wird der Dienst nach einem Benutzer mit einem Attributwert vom Typ `externalId` abgefragt, der dem Wert des Attributs „mailNickname“ eines Benutzers in AAD entspricht. Die Abfrage wird als Hypertext Transfer-Protokoll-Anforderung (HTTP-Anforderung) wie in diesem Beispiel ausgedrückt, wobei „jyoung“ ein Beispiel für ein mailNickname-Attribut eines Benutzers in Azure Active Directory ist.

>[!NOTE]
> Dies ist nur ein Beispiel. Nicht alle Benutzer verfügen über ein mailNickname-Attribut, und der Wert eines Benutzers ist möglicherweise im Verzeichnis nicht eindeutig. Das für den Abgleich verwendete Attribut (in diesem Fall `externalId`) kann auch in den [AAD-Attributzuordnungen](customize-application-attributes.md) konfiguriert werden.

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Im Beispielcode wird die Anforderung in einen Aufruf der QueryAsync-Methode des Dienstanbieters übersetzt. Dies ist die Signatur dieser Methode: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  
// Microsoft.SCIM.IQueryParameters is defined in 
// Microsoft.SCIM.Protocol.  

Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

In der Beispielabfrage für einen Benutzer mit einem bestimmten Wert für das Attribut `externalId` lauten die Werte der Argumente, die an die QueryAsync-Methode übergeben werden, wie folgt:

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***Beispiel 2: Bereitstellen eines Benutzers***

Wenn in der Antwort auf eine an den Webdienst gesendete Abfrage für einen Benutzer mit einem `externalId`-Attributwert, der dem mailNickname-Attributwert eines Benutzers entspricht, keine Benutzer zurückgegeben werden, wird von AAD die Bereitstellung eines Benutzers angefordert, der dem Benutzer in AAD entspricht.  Dies ist ein Beispiel für eine Anforderung dieser Art: 

```
POST https://.../scim/Users HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung@testuser.com",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

Im Beispielcode wird die Anforderung in einen Aufruf der CreateAsync-Methode des Dienstanbieters übersetzt. Dies ist die Signatur dieser Methode: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  

Task<Resource> CreateAsync(IRequest<Resource> request);
```

In der Anforderung einer Benutzerbereitstellung entspricht der Wert des Ressourcenarguments einer Instanz der Klasse „Microsoft.SCIM.Core2EnterpriseUser“, die in der Microsoft.SCIM.Schemas-Bibliothek definiert ist.  Wenn die Anforderung der Benutzerbereitstellung erfolgreich ist, soll die Implementierung der Methode eine Instanz der Klasse „Microsoft.SCIM.Core2EnterpriseUser“ zurückgeben. Dabei muss der Wert der Eigenschaft „Identifier“ auf den eindeutigen Bezeichner des neu bereitgestellten Benutzers eingestellt sein.  

***Beispiel 3: Abfragen des aktuellen Status eines Benutzers*** 

Zum Aktualisieren eines Benutzers, der in einem Identitätsspeicher mit vorgelagertem SCIM vorhanden ist, geht Azure Active Directory so vor, dass der aktuelle Status dieses Benutzers vom Dienst per Anforderung abgefragt wird. Die Anforderung hierzu sieht wie folgt aus: 

```
GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

Im Beispielcode wird die Anforderung in einen Aufruf der RetrieveAsync-Methode des Dienstanbieters übersetzt. Dies ist die Signatur dieser Methode: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource and 
// Microsoft.SCIM.IResourceRetrievalParameters 
// are defined in Microsoft.SCIM.Schemas 

Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

Im Beispiels für eine Anforderung zum Abrufen des aktuellen Status eines Benutzers lauten die Werte der Eigenschaften des Objekts, das als Wert des parameters-Arguments angegeben wird, wie folgt: 
  
* Bezeichner: „54D382A4-2050-4C03-94D1-E769F1D15682“
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Beispiel 4: Abfragen des Werts eines zu aktualisierenden Referenzattributs*** 

Wenn ein Verweisattribut aktualisiert werden soll, fragt Azure Active Directory den Dienst ab, um zu ermitteln, ob der aktuelle Wert des Verweisattributs im Identitätsspeicher mit vorgelagertem Dienst bereits mit dem Wert dieses Attributs in Azure Active Directory übereinstimmt. Bei Benutzern ist das einzige Attribut, für das der aktuelle Wert auf diese Weise abgefragt wird, das manager-Attribut. Dies ist ein Beispiel für eine Anforderung, mit der ermittelt wird, ob das „manager“-Attribut eines Benutzerobjekts derzeit über einen bestimmten Wert verfügt: Im Beispielcode wird die Anforderung in einen Aufruf der QueryAsync-Methode des Dienstanbieters übersetzt. Der Wert der Eigenschaften des Objekts, das als Wert des parameters-Arguments angegeben wird, lautet wie folgt: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: „ID“
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: „54D382A4-2050-4C03-94D1-E769F1D15682“
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: „2819c223-7f76-453a-919d-413861904646“
* parameters.RequestedAttributePaths.ElementAt(0): „ID“
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Hier kann der Wert von Index x „0“ und der Wert von Index y „1“ lauten, oder der Wert von x kann „1“ und der Wert von y „0“ lauten. Dies hängt von der Reihenfolge bei den Ausdrücken des Filterabfrageparameters ab.   

***Beispiel 5: Anforderung von Azure AD an einen SCIM-Dienst zur Aktualisierung eines Benutzers*** 

Dies ist ein Beispiel für eine Anforderung von Azure Active Directory an einen SCIM-Dienst zum Aktualisieren eines Benutzers: 

```
PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

Im Beispielcode wird die Anforderung in einen Aufruf der UpdateAsync-Methode des Dienstanbieters übersetzt. Dies ist die Signatur dieser Methode: 

```csharp
// System.Threading.Tasks.Tasks and 
// System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in
// Microsoft.SCIM.Service.
// Microsoft.SCIM.IPatch, 
// is defined in Microsoft.SCIM.Protocol. 

Task UpdateAsync(IRequest<IPatch> request);
```

Im Beispiel für eine Anforderung zum Aktualisieren eines Benutzers verfügt das Objekt, das als Wert des patch-Arguments angegeben wird, über diese Eigenschaftswerte: 

|Argument|Wert|
|-|-|
|ResourceIdentifier.Identifier|„54D382A4-2050-4C03-94D1-E769F1D15682“|
|ResourceIdentifier.SchemaIdentifier|"urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"|
|(PatchRequest as PatchRequest2).Operations.Count|1|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName|OperationName.Add|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath|"manager"|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count|1|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference|http://.../scim/Users/2819c223-7f76-453a-919d-413861904646|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value| 2819c223-7f76-453a-919d-413861904646|

***Beispiel 6: Aufheben der Bereitstellung eines Benutzers***

Um die Bereitstellung für einen Benutzer aus einem Identitätsspeicher mit vorgelagertem SCIM-Dienst aufzuheben, wird von AAD eine Anforderung wie die folgende gesendet:

```
DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

Im Beispielcode wird die Anforderung in einen Aufruf der DeleteAsync-Methode des Dienstanbieters übersetzt. Dies ist die Signatur dieser Methode: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.IResourceIdentifier, 
// is defined in Microsoft.SCIM.Protocol. 

Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Im Beispiel für eine Anforderung zum Aufheben der Bereitstellung eines Benutzers verfügt das Objekt, das als Wert des resourceIdentifier-Arguments angegeben wird, über diese Eigenschaftswerte: 

* ResourceIdentifier.Identifier: „54D382A4-2050-4C03-94D1-E769F1D15682“
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="integrate-your-scim-endpoint-with-the-aad-scim-client"></a>Integrieren Ihres SCIM-Endpunkts in den AAD-SCIM-Client

Azure AD kann für das automatische Bereitstellen von zugewiesenen Benutzern und Gruppen für Anwendungen konfiguriert werden, die ein bestimmtes Profil des [SCIM 2.0-Protokolls](https://tools.ietf.org/html/rfc7644) implementieren. Die Einzelheiten des Profils sind im Abschnitt [Verstehen der AAD-SCIM-Implementierung](#understand-the-aad-scim-implementation) dokumentiert.

Überprüfen Sie beim Anbieter Ihrer Anwendung oder in der Dokumentation zu Ihrer Anwendung, ob diese Anforderungen voll erfüllt werden.

> [!IMPORTANT]
> Die Azure AD-SCIM-Implementierung basiert auf dem Azure AD-Benutzerbereitstellungsdienst, der auf die ständige Synchronisierung der Benutzer zwischen Azure AD und der Zielanwendung ausgelegt ist, und implementiert eine ganz bestimmte Reihe von Standardvorgängen. Es ist wichtig, diese Verhaltensweisen zu verstehen, um das Verhalten des Azure AD-SCIM-Clients nachvollziehen zu können. Weitere Informationen finden Sie in [Vorgänge während der Bereitstellung: Startzyklus und Inkrementell](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Funktionsweise der Bereitstellung](how-provisioning-works.md).

### <a name="getting-started"></a>Erste Schritte

Anwendungen, die das SCIM-Profil wie in diesem Artikel beschrieben erfüllen, können über das Feature „Nicht-Kataloganwendung“ im Azure AD-Anwendungskatalog mit Azure Active Directory verbunden werden. Nachdem die Verbindung hergestellt wurde, führt Azure AD alle 40 Minuten einen Synchronisierungsprozess aus, bei dem der SCIM-Endpunkt der Anwendung auf zugewiesene Benutzer und Gruppen abgefragt wird. Entsprechend den Details der Zuweisung werden Benutzer und Gruppen dann erstellt oder geändert.

**So verbinden Sie eine Anwendung, die SCIM unterstützt:**

1. Melden Sie sich beim [AAD-Portal](https://aad.portal.azure.com) an. Beachten Sie, dass Sie auf eine kostenlose Testversion für Azure Active Directory mit P2-Lizenzen zugreifen können, indem Sie sich für das [Entwicklerprogramm](https://developer.microsoft.com/office/dev-program) registrieren.
1. Wählen Sie im linken Bereich die Option **Unternehmensanwendungen** aus. Eine Liste mit allen konfigurierten Apps wird angezeigt, einschließlich Apps, die aus dem Katalog hinzugefügt wurden.
1. Wählen Sie **+ Neue Anwendung** >  **+ Eigene Anwendung erstellen** aus.
1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie die Option *Beliebige andere, nicht im Katalog zu findende Anwendung integrieren* und dann **Hinzufügen** aus, um ein App-Objekt zu erstellen. Die neue App wird der Liste mit den Unternehmensanwendungen hinzugefügt und mit dem App-Verwaltungsbildschirm geöffnet.

   ![Screenshot des Azure AD-Anwendungskatalogs](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
   *Azure AD-Anwendungskatalog*

   > [!NOTE]
   > Wenn Sie den alten App-Katalog verwenden, befolgen Sie die Anweisungen auf dem folgenden Bildschirm.
   
   ![Screenshot: Alter Azure AD-App-Katalog](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
   *Alter Azure AD-App-Katalog*

1. Wählen Sie auf dem App-Verwaltungsbildschirm im linken Bereich die Option **Bereitstellung**.
1. Wählen Sie im Menü **Bereitstellungsmodus** die Option **Automatisch** aus.

   ![Beispiel: Bereitstellungsseite einer App im Azure-Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurieren der Bereitstellung im Azure-Portal*

1. Geben Sie im Feld **Mandanten-URL** die URL des SCIM-Endpunkts der Anwendung ein. Beispiel: `https://api.contoso.com/scim/`
1. Wenn der SCIM-Endpunkt ein OAuth-Bearertoken benötigt, das von einem anderen Aussteller als Azure AD stammt, kopieren Sie das erforderliche OAuth-Bearertoken in das optionale Feld **Geheimes Token**. Wird dieses Feld leer gelassen, fügt Azure AD in jede Anforderung ein von Azure AD ausgestelltes OAuth-Bearertoken ein. Apps, die Azure AD als Identitätsanbieter verwenden, können dieses von Azure AD ausgestellte Token überprüfen. 
   > [!NOTE]
   > Es wird ***nicht*** empfohlen, dieses Feld leer zu lassen und sich auf ein von Azure AD generiertes Token zu verlassen. Diese Option steht in erster Linie zu Testzwecken zur Verfügung.
1. Wählen Sie die Option **Verbindung testen**, damit Azure Active Directory versucht, eine Verbindung mit dem SCIM-Endpunkt herzustellen. Wenn der Versuch nicht erfolgreich ist, werden Fehlerinformationen angezeigt.  

    > [!NOTE]
    > Die Option **Verbindung testen** fragt den SCIM-Endpunkt nach einem Benutzer ab, der nicht vorhanden ist, und verwendet dabei einen zufälligen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) als entsprechende Eigenschaft, die in der Azure AD-Konfiguration ausgewählt wurde. Die erwartete richtige Antwort ist „HTTP 200 OK“ mit einer leeren SCIM ListResponse-Meldung.

1. Wählen Sie bei einer erfolgreichen Verbindungsherstellung mit der Anwendung die Option **Speichern**, um die Administratoranmeldeinformationen zu speichern.
1. Im Abschnitt **Zuordnungen** stehen zwei Sätze von [Attributzuordnungen](customize-application-attributes.md) zur Verfügung: eine für Benutzerobjekte und eine für Gruppenobjekte. Wählen Sie beide nacheinander aus, um die Attribute zu überprüfen, die von Azure Active Directory mit Ihrer App synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzer und Gruppen in Ihrer App für Updatevorgänge verwendet werden. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    > [!NOTE]
    > Sie können die Synchronisierung von Gruppenobjekten optional deaktivieren. Deaktivieren Sie dazu die Zuordnung „Gruppen“.

1. Im Feld **Bereich** unter **Einstellungen** wird festgelegt, welche Benutzer und Gruppen synchronisiert werden. Wählen Sie **Nur zugewiesene Benutzer und Gruppen synchronisieren** (empfohlen) aus, damit nur Benutzer und Gruppen synchronisiert werden, die auf der Registerkarte **Benutzer und Gruppen** zugewiesen sind.
1. Legen Sie den **Bereitstellungsstatus** nach Abschluss der Konfiguration auf **Ein** fest.
1. Wählen Sie **Speichern**, um den Azure AD-Bereitstellungsdienst zu starten.
1. Wenn nur zugewiesene Benutzer und Gruppen synchronisiert werden (empfohlen), sollten Sie darauf achten, dass die Registerkarte **Benutzer und Gruppen** ausgewählt ist und die Benutzer bzw. Gruppen zugewiesen sind, die synchronisiert werden sollen.

Nachdem der erste Zyklus gestartet wurde, können Sie im linken Bereich die Option **Bereitstellungsprotokolle** auswählen, um den Fortschritt zu überwachen. Hier werden alle Aktionen angezeigt, die vom Bereitstellungsdienst für Ihre App durchgeführt werden. Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](check-status-user-account-provisioning.md).

> [!NOTE]
> Der erste Zyklus dauert länger als spätere Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird.

## <a name="publish-your-application-to-the-aad-application-gallery"></a>Veröffentlichen Ihrer Anwendung im AAD-Anwendungskatalog

Wenn Sie eine Anwendung erstellen, die von mehreren Mandanten verwendet wird, können Sie sie im Azure AD-Anwendungskatalog zur Verfügung stellen. Dies erleichtert Organisationen das Auffinden der Anwendung und das Konfigurieren der Bereitstellung. Das Veröffentlichen Ihrer App im Azure AD-Katalog und das Verfügbarmachen der Bereitstellung für andere ist einfach. Die entsprechenden Schritte sind [hier](../develop/v2-howto-app-gallery-listing.md) angegeben. Microsoft wird mit Ihnen zusammenarbeiten, um Ihre Anwendung in unseren Katalog zu integrieren, Ihren Endpunkt zu testen und die [Dokumentation](../saas-apps/tutorial-list.md) zum Onboarding für Kunden freizugeben.

### <a name="gallery-onboarding-checklist"></a>Onboardingprüfliste für den Katalog
Verwenden Sie die Prüfliste, um ein schnelles Onboarding Ihrer Anwendung sowie eine reibungslose Bereitstellung für Kunden zu gewährleisten. Die Informationen werden beim Onboarding für den Katalog von Ihnen erfasst. 
> [!div class="checklist"]
> * Unterstützung eines [SCIM 2.0](#understand-the-aad-scim-implementation)-Benutzer- und Gruppenendpunkts (nur einer ist erforderlich, aber beide werden empfohlen)
> * Unterstützung von mindestens 25 Anforderungen pro Sekunde und Mandant, um sicherzustellen, dass Benutzer und Gruppen ohne Verzögerung bereitgestellt werden bzw. deren Bereitstellung aufgehoben wird (erforderlich)
> * Einrichtung eines Kontakts für technische und supportbezogene Fragen, um Kunden nach dem Katalogonboarding zu unterstützen (erforderlich)
> * 3 nicht ablaufende Testanmeldeinformationen für Ihre Anwendung (erforderlich)
> * Unterstützung der OAuth-Autorisierungscodegenehmigung oder eines langlebigen Tokens gemäß der Beschreibung weiter unten (erforderlich)
> * Einrichtung einer Anlaufstelle für technische und supportbezogene Fragen, um Kunden nach dem Katalogonboarding zu unterstützen (erforderlich)
> * Unterstützung der Aktualisierung mehrerer Gruppenmitgliedschaften mit einem einzelnen PATCH-Vorgang
> * Öffentliche Dokumentation Ihres SCIM-Endpunkts
> * [Unterstützung der Schemaerkennung](https://tools.ietf.org/html/rfc7643#section-6)

### <a name="authorization-to-provisioning-connectors-in-the-application-gallery"></a>Autorisierung für Bereitstellungsconnectors im Anwendungskatalog
Die SCIM-Spezifikation definiert kein SCIM-spezifisches Schema für die Authentifizierung und Autorisierung und basiert auf bestehenden Branchenstandards.

|Autorisierungsmethode|Vorteile|Nachteile|Support|
|--|--|--|--|
|Benutzername und Kennwort (von Azure AD nicht empfohlen oder unterstützt)|Einfache Implementierung|Unsicher – [Ihr KeNNwort ist unwichtig](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Unterstützt von Fall zu Fall für Katalog-Apps. Nicht unterstützt für Nicht-Katalog-Apps.|
|Langlebiges Bearertoken|Bei langlebigen Token muss kein Benutzer anwesend sein. Admins können sie beim Einrichten der Bereitstellung leicht verwenden.|Langlebige Token können nur schwer mit einem Administrator geteilt werden, ohne unsichere Methoden wie E-Mail zu verwenden. |Unterstützt für Katalog- und Nicht-Katalog-Apps. |
|OAuth-Autorisierungscodegenehmigung|Zugriffstoken sind sehr viel kurzlebiger als Kennwörter und verfügen über einen automatischen Aktualisierungsmechanismus, den langlebige Bearertoken nicht haben.  Bei der ersten Autorisierung muss ein echter Benutzer anwesend sein, was einen gewissen Grad an Verantwortlichkeit bedeutet. |Ein Benutzer muss anwesend sein. Wenn der Benutzer das Unternehmen verlässt, wird das Token ungültig, und die Autorisierung muss erneut erfolgen.|Wird nur für Katalog-Apps unterstützt. Sie können über die Benutzeroberfläche jedoch ein Zugriffstoken als geheimes Token für kurzfristige Testzwecke bereitstellen. An der Unterstützung für die Autorisierung über OAuth-Code für nicht im Katalog enthaltene Anwendungen wird noch gearbeitet – zusätzlich zur Unterstützung für konfigurierbare Authentifizierungs-/Token-URLs in der Katalog-App.|
|Genehmigung von OAuth-Clientanmeldeinformationen|Zugriffstoken sind sehr viel kurzlebiger als Kennwörter und verfügen über einen automatischen Aktualisierungsmechanismus, den langlebige Bearertoken nicht haben. Sowohl die Autorisierungscodegenehmigung als auch die Genehmigung von Clientanmeldeinformationen gehören zum gleichen Typ Zugriffstoken. Ein Wechsel zwischen diesen beiden Methoden ist daher für die API transparent.  Die Bereitstellung kann vollständig automatisiert werden. Neue Token können ohne Benutzerinteraktion und im Hintergrund angefordert werden. ||Nicht unterstützt für Katalog- und Nicht-Katalog-Apps. Diese Unterstützung befindet sich in unserem Backlog.|

> [!NOTE]
> Es wird nicht empfohlen, das Tokenfeld auf der Benutzeroberfläche der benutzerdefinierten App zur Konfiguration der AAD-Bereitstellung leer zu lassen. Das generierte Token steht in erster Linie zu Testzwecken zur Verfügung.

### <a name="oauth-code-grant-flow"></a>Ablauf der OAuth-Codegenehmigung

Vom Bereitstellungsdienst wird die [Autorisierungscodegenehmigung](https://tools.ietf.org/html/rfc6749#page-24) unterstützt. Nachdem Sie Ihre Anforderung zur Veröffentlichung Ihrer App im Katalog übermittelt haben, setzt sich unser Team mit Ihnen in Verbindung, um die folgenden Informationen zu erfassen:

- **Autorisierungs-URL:** Eine URL des Clients, um die Autorisierung des Ressourcenbesitzers über die Umleitung des Benutzer-Agents zu erhalten. Der Benutzer wird zu dieser URL umgeleitet, um den Zugriff zu autorisieren. 

- **URL für den Tokenaustausch:** Eine URL des Clients, um eine Autorisierungsgenehmigung für ein Zugriffstoken auszutauschen (üblicherweise mit Clientauthentifizierung).

- **Client-ID:** Der Autorisierungsserver stellt dem registrierten Client eine Client-ID aus. Dabei handelt es sich um eine eindeutige Zeichenfolge, die die vom Client bereitgestellten Registrierungsinformationen darstellt.  Die Client-ID ist kein Geheimnis. Sie wird dem Ressourcenbesitzer verfügbar gemacht und **darf nicht** allein für die Clientauthentifizierung verwendet werden.  

- **Geheimer Clientschlüssel:** Ein vom Autorisierungsserver generiertes Geheimnis. Hierbei muss es sich um einen eindeutigen Wert handeln, der nur dem Autorisierungsserver bekannt ist. 

> [!NOTE]
> Die **Autorisierungs-URL** und die **URL für den Tokenaustausch** können derzeit nicht für einzelne Mandanten konfiguriert werden.

> [!NOTE]
> OAuth v1 wird aufgrund der Offenlegung des geheimen Clientschlüssels nicht unterstützt. OAuth v2 dagegen wird unterstützt.  

Bewährte Methoden (empfohlen, aber nicht erforderlich):
* Unterstützen Sie mehrere Umleitungs-URLs. Administratoren können die Bereitstellung sowohl über „portal.azure.com“ als auch über „aad.portal.azure.com“ konfigurieren. Die Unterstützung mehrerer Umleitungs-URLs stellt sicher, dass Benutzer den Zugriff von beiden Portalen aus autorisieren können.
* Unterstützen Sie mehrere Geheimnisse, um eine unkomplizierte Verlängerung ohne Downtime zu ermöglichen. 

#### <a name="how-to-setup-oauth-code-grant-flow"></a>Einrichten des Ablaufs der OAuth-Codegenehmigung

1. Melden Sie sich beim Azure-Portal an, und wählen Sie unter **Unternehmensanwendungen** > **Anwendung** > **Bereitstellung** die Option **Autorisieren** aus.

   1. Der Benutzer wird im Azure-Portal zur Autorisierungs-URL (Anmeldeseite für die Drittanbieter-App) weitergeleitet.

   1. Der Administrator stellt Anmeldeinformationen für die Drittanbieteranwendung bereit. 

   1. Die Drittanbieter-App leitet den Benutzer zurück zum Azure-Portal und stellt den Autorisierungscode bereit. 

   1. Der Azure AD-Bereitstellungsdienst ruft die Token-URL auf und stellt den Autorisierungscode bereit. Die Drittanbieteranwendung reagiert mit dem Zugriffstoken, dem Aktualisierungstoken und einem Ablaufdatum.

1. Wenn der Bereitstellungszyklus beginnt, überprüft der Dienst, ob das aktuelle Zugriffstoken gültig ist und tauscht es bei Bedarf gegen ein neues aus. Das Zugriffstoken wird in jeder für die App veröffentlichten Anforderung bereitgestellt, und die Gültigkeit der Anforderung wird bei jeder Anforderung überprüft.

> [!NOTE]
> Zwar ist es nicht möglich, OAuth für eine Anwendung einzurichten, die nicht aus dem Katalog stammt, Sie können allerdings manuell ein Zugriffstoken auf Ihrem Autorisierungsserver generieren und es als geheimes Token für eine katalogfremde Anwendung einfügen. So können Sie die Kompatibilität Ihres SCIM-Servers mit dem AAD-SCIM-Client überprüfen, bevor Sie das Onboarding für den App-Katalog durchführen, wofür die Autorisierung über OAuth-Code unterstützt wird.  

**Langlebige OAuth-Bearertoken:** Wenn Ihre Anwendung den Ablauf der OAuth-Autorisierungscodegenehmigung nicht unterstützt, generieren Sie stattdessen ein langlebiges OAuth-Bearertoken, mit dem ein Administrator die Integration der Bereitstellung einrichten kann. Das Token sollte unbefristet sein, andernfalls wird der Bereitstellungsauftrag nach Ablauf des Token [unter Quarantäne gestellt](application-provisioning-quarantine-status.md).

Teilen Sie uns über [UserVoice](https://aka.ms/appprovisioningfeaturerequest) mit, wenn weitere Methoden zur Authentifizierung und Autorisierung erforderlich sind.

### <a name="gallery-go-to-market-launch-check-list"></a>Markteinführungsprüfliste für den Katalog
Es empfiehlt sich, die vorhandene Dokumentation zu aktualisieren und unsere gemeinsame Integration in Ihren Marketingkanälen hervorzuheben, um über die Integration zu informieren und Interesse dafür zu wecken.  Die folgende Prüfliste enthält empfohlene Aktivitäten zur Unterstützung der Einführung:

> [!div class="checklist"]
> * Stellen Sie sicher, dass Ihre Vertriebs- und Kundensupportteams mit den Integrationsfunktionen vertraut und bereit sind, sich zu ihnen zu äußern. Schulen Sie Ihre Teams, stellen Sie häufig gestellte Fragen bereit, und nehmen Sie die Integration in Ihr Vertriebsmaterial auf. 
> * Erstellen Sie einen Blogbeitrag oder eine Pressemitteilung mit einer Beschreibung der gemeinsamen Integration, der Vorteile und der ersten Schritte. [Beispiel: Pressemitteilung von Imprivata und Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
> * Nutzen Sie soziale Medien wie Twitter, Facebook oder LinkedIn, um bei Ihren Kunden für die Integration zu werben. Schließen Sie @AzureAD ein, damit wir Ihren Beitrag retweeten können. [Beispiel: Twitter-Beitrag von Imprivata](https://twitter.com/azuread/status/1123964502909779968)
> * Erstellen oder aktualisieren Sie Ihre Marketingseiten/Website (Integrationsseite, Partnerseite, Preisseite und Ähnliches), um die Verfügbarkeit der gemeinsamen Integration einzuschließen. [Beispiel: Integrationsseite von Pingboard](https://pingboard.com/org-chart-for), [Integrationsseite von Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Preisseite von Monday.com](https://monday.com/pricing/) 
> * Erstellen Sie einen Hilfecenter-Artikel oder eine technische Dokumentation mit den ersten Schritten für Kunden. [Beispiel: Integration von Envoy und Microsoft Azure Active Directory](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
> * Machen Sie Kunden über Ihre Kundenkommunikationskanäle (monatliche Newsletter, E-Mail-Kampagnen, Produktanmerkungen) auf die neue Integration aufmerksam. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Entwickeln eines SCIM-Beispielendpunkts](use-scim-to-build-users-and-groups-endpoints.md)
> [Was ist die automatisierte Bereitstellung von SaaS-App-Benutzern in Azure AD?](user-provisioning.md)
> [Tutorial: Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md)
> [Referenz zum Schreiben von Ausdrücken für Attributzuordnungen in Azure AD](functions-for-customizing-application-data.md)
> [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](define-conditional-rules-for-provisioning-user-accounts.md)
> [Was ist die automatisierte Bereitstellung von SaaS-App-Benutzern in Azure AD?](user-provisioning.md)
> [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](../saas-apps/tutorial-list.md)
