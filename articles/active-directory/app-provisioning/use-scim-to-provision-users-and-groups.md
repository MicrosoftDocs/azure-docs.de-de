---
title: 'Tutorial: Entwickeln eines SCIM-Endpunkts für die Benutzerbereitstellung in Apps von Azure AD'
description: Das System für die domänenübergreifende Identitätsverwaltung (System for Cross-domain Identity Management, SCIM) standardisiert die automatische Benutzerbereitstellung. In diesem Tutorial erfahren Sie, wie Sie einen SCIM-Endpunkt entwickeln, Ihre SCIM-API mit Azure Active Directory integrieren und mit der Automatisierung der Bereitstellung von Benutzern und Gruppen in Ihren Cloudanwendungen beginnen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: c9738d25fdcb1c0ccda70ec116eb369f8b50e980
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027474"
---
# <a name="tutorial---build-a-scim-endpoint-and-configure-user-provisioning-with-azure-ad"></a>Tutorial: Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung mit Azure AD

Als Anwendungsentwickler können Sie die SCIM-Benutzerverwaltungs-API (System for Cross-Domain Identity Management, System für die domänenübergreifende Identitätsverwaltung) verwenden, um die automatische Bereitstellung von Benutzern und Gruppen zwischen Ihrer Anwendung und Azure AD zu aktivieren. In diesem Artikel wird beschrieben, wie ein SCIM-Endpunkt erstellt und in den Azure AD-Bereitstellungsdienst integriert wird. Die SCIM-Spezifikation bietet ein allgemeines Benutzerschema für die Bereitstellung. Bei der Verwendung mit Verbundstandards wie SAML oder OpenID Connect bietet SCIM Administratoren eine auf Standards basierende End-to-End-Lösung für die Zugriffsverwaltung.

SCIM ist eine standardisierte Definition von zwei Endpunkten: einem „/Users“- und einem „/Group“-Endpunkt. Es verwendet allgemeine REST-Verben zum Erstellen, Aktualisieren und Löschen von Objekten und ein vordefiniertes Schema für allgemeine Attribute wie Gruppenname, Benutzername, Vorname, Nachname und E-Mail-Adresse. Apps, die eine SCIM 2.0 REST-API bieten, können den Aufwand für die Arbeit mit einer proprietären Benutzerverwaltungs-API reduzieren oder eliminieren. So ist z. B. jeder konforme SCIM-Client in der Lage, ein HTTP POST für ein JSON-Objekt an den „/Users“-Endpunkt zu senden, um einen neuen Benutzereintrag zu erstellen. Anstatt eine leicht abweichende API für dieselben grundlegenden Aktionen zu benötigen, können Apps, die dem SCIM-Standard entsprechen, sofort die Vorteile bereits vorhandener Clients, Tools und Codes nutzen. 

![Bereitstellen von Azure AD für eine App mit SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Das in SCIM 2.0 definierte Standard-Benutzerobjektschema und die REST-APIs für die Verwaltung (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) ermöglichen eine einfachere Integration von Identitätsanbietern und Apps. Anwendungsentwickler, die einen SCIM-Endpunkt erstellen, können die Integration mit jedem SCIM-konformen Client durchführen, ohne selbst Anpassungen vornehmen zu müssen.

Die Automatisierung der Bereitstellung für eine Anwendung erfordert die Erstellung und Integration eines SCIM-Endpunkts mit dem Azure AD-SCIM-Client. Führen Sie die folgenden Schritte aus, um die Bereitstellung von Benutzern und Gruppen in Ihrer Anwendung zu starten. 
    
  * **[Schritt 1: Entwerfen Ihres Benutzer- und Gruppenschemas](#step-1-design-your-user-and-group-schema)** Identifizieren Sie die Objekte und Attribute, die Ihre Anwendung benötigt, und legen Sie fest, wie sie dem von der Azure AD SCIM-Implementierung unterstützten Benutzer- und Gruppenschema zugeordnet werden.

  * **[Schritt 2: Verstehen der Azure AD-SCIM-Implementierung](#step-2-understand-the-azure-ad-scim-implementation)** Erfahren Sie, wie der Azure AD SCIM-Client implementiert wird, und modellieren Sie die Behandlung sowie die Antworten Ihrer SCIM-Protokollanforderung.

  * **[Schritt 3: Erstellen eines SCIM-Endpunkts](#step-3-build-a-scim-endpoint)** Ein Endpunkt muss SCIM 2.0-kompatibel sein, damit er in den Azure AD-Bereitstellungsdienst integriert werden kann. Optional können Sie Bibliotheken und Codebeispiele der Microsoft Common Language Infrastructure (CLI) verwenden, um Ihren Endpunkt zu erstellen. Diese Beispiele dienen nur als Referenz und für Tests. Es wird nicht empfohlen, Ihre Produktionsanwendung so zu codieren, dass sie von ihnen abhängig wird.

  * **[Schritt 4: Integrieren Ihres SCIM-Endpunkts mit dem Azure AD SCIM-Client](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Wenn Ihre Organisation eine Drittanbieteranwendung verwendet, die das von Azure AD unterstützte Profil von SCIM 2.0 implementiert, können Sie sofort mit der Automatisierung der Bereitstellung und der Aufhebung der Bereitstellung von Benutzern und Gruppen beginnen.

  * **[Schritt 5: Veröffentlichen Ihrer Anwendung im Azure AD-Anwendungskatalog](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Erleichtern Sie es Ihren Kunden, Ihre Anwendung zu entdecken und die Bereitstellung einfach zu konfigurieren. 

![Schritte zur Integration eines SCIM-Endpunkts mit Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Schritt 1: Entwerfen Ihres Benutzer- und Gruppenschemas

Jede Anwendung erfordert unterschiedliche Attribute, um einen Benutzer oder eine Gruppe zu erstellen. Starten Sie Ihre Integration, indem Sie die Objekte (Benutzer, Gruppen) und Attribute (Name, Vorgesetzter, Positionsbezeichnung, usw.) identifizieren, die Ihre Anwendung benötigt. Mit dem SCIM-Standard wird ein Schema zum Verwalten von Benutzern und Gruppen definiert. Für das Kernbenutzerschema sind nur drei Attribute erforderlich: **id** (vom Dienstanbieter definierter Bezeichner), **externalId** (vom Client definierter Bezeichner) und **Meta** (schreibgeschützte, vom Dienstanbieter verwaltete Metadaten). Alle anderen Attribute sind optional. Neben dem Kernbenutzerschema definiert der SCIM-Standard eine Unternehmensbenutzererweiterung und ein Modell für die Erweiterung des Benutzerschemas, um die Anforderungen Ihrer Anwendung zu erfüllen. Wenn für Ihre Anwendung beispielsweise der Vorgesetzte des Benutzers erforderlich ist, können Sie mit dem Unternehmensbenutzerschema den Vorgesetzten des Benutzers und mit dem Kernschema die E-Mail-Adresse des Benutzers erfassen. Führen Sie die folgenden Schritte aus, um Ihr Schema zu entwerfen:
  1. Listen Sie die Attribute auf, die für Ihre Anwendung erforderlich sind. Es kann hilfreich sein, die Anforderungen wie folgt zu unterteilen: Attribute zur Authentifizierung (z. B. Anmeldename und E-Mail-Adresse), Attribute zur Verwaltung des Benutzerlebenszyklus (z. B. Status/aktiv) und weitere Attribute, die zum Funktionieren der jeweiligen Anwendung erforderlich sind (z. B. Vorgesetzter, Tag).
  2. Prüfen Sie, ob diese Attribute bereits im Kern- oder Unternehmensbenutzerschema definiert sind. Für Attribute, die Sie benötigen und die nicht im Kern- oder Unternehmensbenutzerschema enthalten sind, müssen Sie eine Erweiterung des Benutzerschemas definieren. Im folgenden Beispiel wurde für den Benutzer eine Erweiterung hinzugefügt, um die Bereitstellung eines „Tags“ für den Benutzer zu ermöglichen. Am besten beginnen Sie mit dem Kern- und dem Unternehmensbenutzerschema und erweitern diese später um zusätzliche benutzerdefinierte Schemas.  
  3. Ordnen Sie die SCIM-Attribute den Benutzerattributen in Azure AD zu. Wenn eines der Attribute, die Sie im SCIM-Endpunkt definiert haben, im Azure AD-Benutzerschema keine eindeutige Entsprechung hat, ist die Wahrscheinlichkeit groß, dass die Daten bei den meisten Mandanten überhaupt nicht im Benutzerobjekt gespeichert sind. Überlegen Sie, ob dieses Attribut beim Erstellen eines Benutzers eine Wahlmöglichkeit sein kann. Wenn das Attribut für das Funktionieren Ihrer Anwendung wichtig ist, bitten Sie den Mandantenadministrator, sein Schema zu erweitern, oder verwenden Sie ein Erweiterungsattribut, wie weiter unten für die Eigenschaft „Tags“ gezeigt.

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabelle 1: Auflisten der benötigten Attribute 
| Schritt 1: Bestimmen der für Ihre App erforderlichen Attribute| Schritt 2: Zuordnen der App-Anforderungen zum SCIM-Standard| Schritt 3: Zuordnen von SCIM-Attributen zu Azure AD-Attributen|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.lastName|lastName|
|workMail|Emails[type eq “work”].value|E-Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|aktiv|isSoftDeleted (berechneter Wert, wird für Benutzer nicht gespeichert)|

Das oben definierte Schema kann mit den nachstehenden JSON-Nutzdaten dargestellt werden. Hinweis: Die JSON-Darstellung enthält neben den für die Anwendung erforderlichen Attributen auch die erforderlichen Attribute `id`, `externalId` und `meta`.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
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

### <a name="table-2-default-user-attribute-mapping"></a>Tabelle 2: Standardzuordnung von Benutzerattributen
Sie können dann die folgende Tabelle verwenden, um zu verstehen, wie die von Ihrer Anwendung benötigten Attribute einem Attribut in Azure AD und dem SCIM RFC zugeordnet werden können. Sie können [anpassen](customize-application-attributes.md), wie Attribute zwischen Azure AD und Ihrem SCIM-Endpunkt zugeordnet werden. Beachten Sie, dass Sie nicht sowohl Benutzer als auch Gruppen oder alle unten aufgeführten Attribute unterstützen müssen. Sie sind eine Referenz dafür, wie Attribute in Azure AD oft zu Eigenschaften im SCIM-Protokoll zugeordnet werden. 

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


### <a name="table-3-default-group-attribute-mapping"></a>Tabelle 3: Standardzuordnung von Gruppenattributen

| Azure Active Directory-Gruppe | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

Im SCIM-RFC sind verschiedene Endpunkte definiert. Sie können mit dem Endpunkt „/User“ beginnen und von dort erweitern. Der Endpunkt „/Schemas“ ist hilfreich, wenn Sie benutzerdefinierte Attribute verwenden oder sich Ihr Schema häufig ändert. Er bietet dem Client die Möglichkeit, automatisch das jeweils aktuelle Schema abzurufen. Der Endpunkt „/Bulk“ ist besonders hilfreich für die Unterstützung von Gruppen. In der folgenden Tabelle sind die verschiedenen, im SCIM-Standard definierten Endpunkte beschrieben.
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabelle 4: Bestimmen der zu entwickelnden Endpunkte
|ENDPOINT|DESCRIPTION|
|--|--|
|/User|Führt CRUD-Vorgänge für ein Benutzerobjekt aus.|
|/Group|Führt CRUD-Vorgänge für ein Gruppenobjekt aus.|
|/ServiceProviderConfig|Stellt Einzelheiten zu den unterstützten Features des SCIM-Standards bereit, z. B. die unterstützten Ressourcen und die Authentifizierungsmethode.|
|/ResourceTypes|Definiert die Metadaten für jede Ressource|
|/Schemas|Der Satz von unterstützten Attributen kann bei einzelnen Clients und Dienstanbietern unterschiedlich sein. Ein Dienstanbieter enthält möglicherweise `name`, `title` und `emails`, während ein anderer `name`, `title` und `phoneNumbers` verwendet. Der Endpunkt „/Schemas“ ermöglicht die Erkennung der unterstützten Attribute.|
|/Bulk|Massenvorgänge (bulk operations) ermöglichen das Ausführen von Vorgängen für eine große Sammlung von Ressourcenobjekten in einem einzigen Schritt (z. B. Aktualisieren der Mitgliedschaften für eine große Gruppe).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Schritt 2: Verstehen der Azure AD-SCIM-Implementierung
> [!IMPORTANT]
> Das Verhalten der Azure AD-SCIM-Implementierung wurde zuletzt am 18. Dezember 2018 aktualisiert. Informationen zu den Änderungen finden Sie unter [Einhaltung des SCIM 2.0-Protokolls des Azure AD-Benutzerbereitstellungsdiensts](application-provisioning-config-problem-scim-compatibility.md).

Wenn Sie eine Anwendung erstellen, die eine SCIM 2.0-Benutzerverwaltungs-API unterstützt, ist dieser Abschnitt hilfreich. Darin wird detailliert beschrieben, wie der Azure AD-SCIM-Client implementiert wird. Außerdem wird gezeigt, wie Sie die Verarbeitung von SCIM-Protokollanforderungen und -antworten modellieren sollten. Nachdem Sie den SCIM-Endpunkt implementiert haben, können Sie ihn durch Ausführen der im vorherigen Abschnitt beschriebenen Schritte testen.

Im Rahmen der [SCIM 2.0-Protokollspezifikation](http://www.simplecloud.info/#Specification) muss Ihre Anwendung die folgenden Anforderungen erfüllen:

* Unterstützt das Erstellen von Benutzern (und optional auch von Gruppen) gemäß [Abschnitt 3.3 des SCIM-Protokolls](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Unterstützt das Ändern von Benutzern bzw. Gruppen mit PATCH-Anforderungen gemäß [Abschnitt 3.5.2 des SCIM-Protokolls](https://tools.ietf.org/html/rfc7644#section-3.5.2). Durch die Unterstützung wird sichergestellt, dass Gruppen und Benutzer auf leistungsstarke Weise bereitgestellt werden. 
* Unterstützt das Abrufen einer bekannten Ressource für einen zuvor erstellten Benutzer oder eine Gruppe gemäß [Abschnitt 3.4.1 des SCIM-Protokolls](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Unterstützt das Abfragen von Benutzern bzw. Gruppen gemäß [Abschnitt 3.4.2 des SCIM-Protokolls](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Standardmäßig werden Benutzer anhand ihrer `id` abgerufen und nach `username` und `externalId` abgefragt. Gruppen werden nach `displayName` abgefragt.  
* Unterstützt das Abfragen von Benutzern nach ID und nach Manager gemäß Abschnitt 3.4.2 des SCIM-Protokolls.  
* Unterstützt das Abfragen von Gruppen nach ID und nach Mitglied gemäß Abschnitt 3.4.2 des SCIM-Protokolls.  
* Unterstützt den Filter [excludedAttributes=members](#get-group) beim Abfragen der Gruppenressource gemäß Abschnitt 3.4.2.5 des SCIM-Protokolls.
* Akzeptiert ein einzelnes Bearertoken für die Authentifizierung und Autorisierung von Azure AD für Ihre Anwendung.
* Unterstützt das vorläufige Löschen eines Benutzers `active=false` und das Wiederherstellen des Benutzers `active=true` (das Benutzerobjekt sollte in einer Anforderung zurückgegeben werden, unabhängig davon, ob der Benutzer aktiv ist oder nicht). Der Benutzer sollte nur dann nicht zurückgegeben werden, wenn er endgültig aus der Anwendung gelöscht wurde. 

Befolgen Sie bei der Implementierung eines SCIM-Endpunkts die folgenden allgemeinen Richtlinien, um die Kompatibilität mit Azure AD sicherzustellen:

* `id` ist eine erforderliche Eigenschaft für alle Ressourcen. Für jede Antwort, die eine Ressource zurückgibt, muss sichergestellt werden, dass jede Ressource diese Eigenschaft aufweist. Eine Ausnahme ist `ListResponse` mit 0 (null) Elementen.
* Eine Antwort auf eine Abfrage-/Filteranforderung muss immer eine `ListResponse` sein.
* Gruppen sind optional, werden jedoch nur unterstützt, wenn die SCIM-Implementierung PATCH-Anforderungen unterstützt.
* Die PATCH-Antwort muss nicht die gesamte Ressource enthalten.
* Microsoft Azure AD verwendet nur die folgenden Operatoren:  
    - `eq`
    - `and`
* Unterscheiden Sie bei Strukturelementen in SCIM nicht zwischen Groß- und Kleinschreibung, insbesondere bei `op`-PATCH-Vorgangswerten gemäß der Definition unter https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD gibt die Werte von „op“ mit `Add`, `Replace` und `Remove` aus.
* Microsoft Azure AD sendet Anforderungen zum Abrufen eines zufälligen Benutzers und einer zufälligen Gruppe, um sicherzustellen, dass der Endpunkt und die Anmeldeinformationen gültig sind. Dies erfolgt auch im Rahmen des **Testverbindungsflows** im [Azure-Portal](https://portal.azure.com). 
* Das Attribut, nach dem die Ressourcen abgefragt werden können, sollte als entsprechendes Attribut für die Anwendung im [Azure-Portal](https://portal.azure.com) festgelegt werden. Weitere Informationen finden Sie unter [Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md).
* HTTPS-Unterstützung auf Ihrem SCIM-Endpunkt

### <a name="user-provisioning-and-deprovisioning"></a>Benutzerbereitstellung und Aufheben der Bereitstellung

In der folgenden Abbildung sind die Nachrichten dargestellt, die Azure Active Directory an einen SCIM-Dienst sendet, um den Lebenszyklus eines Benutzers im Identitätsspeicher Ihrer Anwendung zu verwalten.  

![Zeigt die Abfolge der Durchführung und Aufhebung einer Benutzerbereitstellung](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Abfolge der Durchführung und Aufhebung einer Benutzerbereitstellung*

### <a name="group-provisioning-and-deprovisioning"></a>Gruppenbereitstellung und Aufheben der Bereitstellung

Die Gruppenbereitstellung und die Aufhebung einer Gruppenbereitstellung sind optional. In der folgenden Abbildung sind die Nachrichten dargestellt, die Azure AD bei entsprechender Implementierung und Aktivierung an einen SCIM-Dienst sendet, um den Lebenszyklus einer Gruppe im Identitätsspeicher Ihrer Anwendung zu verwalten.  Diese Nachrichten unterscheiden sich von den Nachrichten zu Benutzern auf zwei Arten:

* Für Anforderungen zum Abrufen von Gruppen wird angegeben, dass das members-Attribut aus allen Ressourcen ausgeschlossen wird, die als Antwort auf die Anforderung bereitgestellt werden.  
* Bei Anforderungen für die Ermittlung, ob ein Referenzattribut einen bestimmten Wert hat, handelt es sich um Anforderungen zum members-Attribut.  

![Zeigt die Abfolge der Durchführung und Aufhebung einer Gruppenbereitstellung](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Abfolge der Durchführung und Aufhebung einer Gruppenbereitstellung*

### <a name="scim-protocol-requests-and-responses"></a>SCIM-Protokollanforderungen und -antworten
Dieser Abschnitt enthält vom Azure AD-SCIM-Client ausgegebene SCIM-Beispielanforderungen und erwartete Beispielantworten. Die besten Ergebnisse erzielen Sie, wenn Sie Ihre App so codieren, dass diese Anforderungen in diesem Format verarbeitet und die erwarteten Antworten ausgegeben werden.

> [!IMPORTANT]
> Um zu verstehen, wie und wann der Azure AD-Benutzerbereitstellungsdienst die unten beschriebenen Vorgänge ausgibt, lesen Sie [Vorgänge während der Bereitstellung: Startzyklus und Inkrementell](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Funktionsweise der Bereitstellung](how-provisioning-works.md).

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

###### <a name="request"></a>Anforderung

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

## <a name="step-3-build-a-scim-endpoint"></a>Schritt 3: Erstellen eines SCIM-Endpunkts

Nachdem Sie das Schema entworfen und die Azure AD-SCIM-Implementierung verstanden haben, können Sie mit der Entwicklung Ihres SCIM-Endpunkts beginnen. Anstatt bei Null anzufangen und die Implementierung komplett selbst zu erstellen, können Sie auf eine Reihe von Open-Source-SCIM-Bibliotheken zurückgreifen, die von der SCIM-Community veröffentlicht werden.

Der Open-Source-[Referenzcode](https://aka.ms/SCIMReferenceCode) für .NET Core, der vom Azure AD-Bereitstellungsteam veröffentlicht wird, ist eine solche Ressource, die Ihnen einen schnellen Einstieg in die Entwicklung ermöglicht. Nachdem Sie den SCIM-Endpunkt erstellt haben, sollten Sie ihn testen. Sie können die Sammlung von [Postman-Tests](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) verwenden, die als Teil des Referenzcodes bereitgestellt werden, oder die [oben](#user-operations) aufgeführten Beispielanforderungen/-antworten ausführen.  

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

Weitere Informationen zu mehreren Umgebungen in ASP.NET Core erhalten Sie über den folgenden Link: [Verwenden von mehreren Umgebungen in ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

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

***Beispiel 1. Abfragen des Diensts nach einem passenden Benutzer** _

Azure Active Directory fragt den Dienst nach einem Benutzer mit einem `externalId`-Attributwert ab, der mit dem mailNickname-Attributwert eines Benutzers in Azure AD übereinstimmt. Die Abfrage wird als Hypertext Transfer-Protokoll-Anforderung (HTTP-Anforderung) wie in diesem Beispiel ausgedrückt, wobei „jyoung“ ein Beispiel für ein mailNickname-Attribut eines Benutzers in Azure Active Directory ist.

>[!NOTE]
> Dies ist nur ein Beispiel. Nicht alle Benutzer verfügen über ein mailNickname-Attribut, und der Wert eines Benutzers ist möglicherweise im Verzeichnis nicht eindeutig. Das für den Abgleich verwendete Attribut (in diesem Fall `externalId`) kann auch in den [Azure AD-Attributzuordnungen](customize-application-attributes.md) konfiguriert werden.

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

_ parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***Beispiel 2. Bereitstellen eines Benutzers** _

Wenn in der Antwort auf eine Abfrage an den Webdienst für einen Benutzer mit einem `externalId`-Attributwert, der mit dem mailNickname-Attributwert eines Benutzers übereinstimmt, keine Benutzer zurückgegeben werden, stellt Azure Active Directory die folgende Anforderung: Der Dienst muss einen Benutzer bereitstellen, der dem Benutzer in Azure Active Directory entspricht.  Dies ist ein Beispiel für eine Anforderung dieser Art: 

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
   "userName":"jyoung",
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

_*_Beispiel 3: Abfragen des aktuellen Status eines Benutzers_*_ 

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
  
_ Identifier: „54D382A4-2050-4C03-94D1-E769F1D15682“
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Beispiel 4. Abfragen des Werts eines zu aktualisierenden Referenzattributs** _ 

Wenn ein Verweisattribut aktualisiert werden soll, fragt Azure Active Directory den Dienst ab, um zu ermitteln, ob der aktuelle Wert des Verweisattributs im Identitätsspeicher mit vorgelagertem Dienst bereits mit dem Wert dieses Attributs in Azure Active Directory übereinstimmt. Bei Benutzern ist das einzige Attribut, für das der aktuelle Wert auf diese Weise abgefragt wird, das manager-Attribut. Dies ist ein Beispiel für eine Anforderung, mit der ermittelt wird, ob das „manager“-Attribut eines Benutzerobjekts derzeit über einen bestimmten Wert verfügt: Im Beispielcode wird die Anforderung in einen Aufruf der QueryAsync-Methode des Dienstanbieters übersetzt. Der Wert der Eigenschaften des Objekts, das als Wert des parameters-Arguments angegeben wird, lautet wie folgt: 
  
_ parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: „ID“
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: „54D382A4-2050-4C03-94D1-E769F1D15682“
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: „2819c223-7f76-453a-919d-413861904646“
* parameters.RequestedAttributePaths.ElementAt(0): „ID“
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Hier kann der Wert von Index x „0“ und der Wert von Index y „1“ lauten, oder der Wert von x kann „1“ und der Wert von y „0“ lauten. Dies hängt von der Reihenfolge bei den Ausdrücken des Filterabfrageparameters ab.   

***Beispiel 5. Anforderung von Azure AD an einen SCIM-Dienst zur Aktualisierung eines Benutzers** _ 

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
  
_ ResourceIdentifier.Identifier: „54D382A4-2050-4C03-94D1-E769F1D15682“
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

***Beispiel 6. Aufheben der Bereitstellung eines Benutzers** _

Um die Bereitstellung für einen Benutzer aus einem Identitätsspeicher mit vorgelagertem SCIM-Dienst aufzuheben, sendet Azure AD eine Anforderung der folgenden Art:

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

_ ResourceIdentifier.Identifier: „54D382A4-2050-4C03-94D1-E769F1D15682“
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Schritt 4: Integrieren Ihres SCIM-Endpunkts mit dem Azure AD SCIM-Client

Azure AD kann für das automatische Bereitstellen von zugewiesenen Benutzern und Gruppen für Anwendungen konfiguriert werden, die ein bestimmtes Profil des [SCIM 2.0-Protokolls](https://tools.ietf.org/html/rfc7644) implementieren. Die Details des Profils sind in [Schritt 2: Verstehen der Azure AD SCIM-Implementierung](#step-2-understand-the-azure-ad-scim-implementation) dokumentiert.

Überprüfen Sie beim Anbieter Ihrer Anwendung oder in der Dokumentation zu Ihrer Anwendung, ob diese Anforderungen voll erfüllt werden.

> [!IMPORTANT]
> Die Azure AD-SCIM-Implementierung basiert auf dem Azure AD-Benutzerbereitstellungsdienst, der auf die ständige Synchronisierung der Benutzer zwischen Azure AD und der Zielanwendung ausgelegt ist, und implementiert eine ganz bestimmte Reihe von Standardvorgängen. Es ist wichtig, diese Verhaltensweisen zu verstehen, um das Verhalten des Azure AD-SCIM-Clients nachvollziehen zu können. Weitere Informationen finden Sie in [Vorgänge während der Bereitstellung: Startzyklus und Inkrementell](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Funktionsweise der Bereitstellung](how-provisioning-works.md).

### <a name="getting-started"></a>Erste Schritte

Anwendungen, die das SCIM-Profil wie in diesem Artikel beschrieben erfüllen, können über das Feature „Nicht-Kataloganwendung“ im Azure AD-Anwendungskatalog mit Azure Active Directory verbunden werden. Nachdem die Verbindung hergestellt wurde, führt Azure AD alle 40 Minuten einen Synchronisierungsprozess aus, bei dem der SCIM-Endpunkt der Anwendung auf zugewiesene Benutzer und Gruppen abgefragt wird. Entsprechend den Details der Zuweisung werden Benutzer und Gruppen dann erstellt oder geändert.

**So verbinden Sie eine Anwendung, die SCIM unterstützt:**

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com) an. Beachten Sie, dass Sie auf eine kostenlose Testversion für Azure Active Directory mit P2-Lizenzen zugreifen können, indem Sie sich für das [Entwicklerprogramm](https://developer.microsoft.com/office/dev-program) registrieren.
2. Wählen Sie im linken Bereich die Option **Unternehmensanwendungen** aus. Eine Liste mit allen konfigurierten Apps wird angezeigt, einschließlich Apps, die aus dem Katalog hinzugefügt wurden.
3. Wählen Sie **+ Neue Anwendung** > **Alle** > **Nicht-Kataloganwendung**.
4. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Hinzufügen**, um ein App-Objekt zu erstellen. Die neue App wird der Liste mit den Unternehmensanwendungen hinzugefügt und mit dem App-Verwaltungsbildschirm geöffnet.

   ![Screenshot des Azure AD-Anwendungskatalogs](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD-Anwendungskatalog*

5. Wählen Sie auf dem App-Verwaltungsbildschirm im linken Bereich die Option **Bereitstellung**.
6. Wählen Sie im Menü **Bereitstellungsmodus** die Option **Automatisch** aus.

   ![Beispiel: Bereitstellungsseite einer App im Azure-Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurieren der Bereitstellung im Azure-Portal*

7. Geben Sie im Feld **Mandanten-URL** die URL des SCIM-Endpunkts der Anwendung ein. Beispiel: `https://api.contoso.com/scim/`
8. Wenn der SCIM-Endpunkt ein OAuth-Bearertoken benötigt, das von einem anderen Aussteller als Azure AD stammt, kopieren Sie das erforderliche OAuth-Bearertoken in das optionale Feld **Geheimes Token**. Wird dieses Feld leer gelassen, fügt Azure AD in jede Anforderung ein von Azure AD ausgestelltes OAuth-Bearertoken ein. Apps, die Azure AD als Identitätsanbieter verwenden, können dieses von Azure AD ausgestellte Token überprüfen. 
   > [!NOTE]
   > Es wird **_nicht_* _ empfohlen, dieses Feld leer zu lassen und sich auf ein von Azure AD generiertes Token zu verlassen. Diese Option steht in erster Linie zu Testzwecken zur Verfügung.
9. Wählen Sie die Option _ *Verbindung testen**, damit Azure Active Directory versucht, eine Verbindung mit dem SCIM-Endpunkt herzustellen. Wenn der Versuch nicht erfolgreich ist, werden Fehlerinformationen angezeigt.  

    > [!NOTE]
    > Die Option **Verbindung testen** fragt den SCIM-Endpunkt nach einem Benutzer ab, der nicht vorhanden ist, und verwendet dabei einen zufälligen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) als entsprechende Eigenschaft, die in der Azure AD-Konfiguration ausgewählt wurde. Die erwartete richtige Antwort ist „HTTP 200 OK“ mit einer leeren SCIM ListResponse-Meldung.

10. Wählen Sie bei einer erfolgreichen Verbindungsherstellung mit der Anwendung die Option **Speichern**, um die Administratoranmeldeinformationen zu speichern.
11. Im Abschnitt **Zuordnungen** stehen zwei Sätze von [Attributzuordnungen](customize-application-attributes.md) zur Verfügung: eine für Benutzerobjekte und eine für Gruppenobjekte. Wählen Sie beide nacheinander aus, um die Attribute zu überprüfen, die von Azure Active Directory mit Ihrer App synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzer und Gruppen in Ihrer App für Updatevorgänge verwendet werden. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    > [!NOTE]
    > Sie können die Synchronisierung von Gruppenobjekten optional deaktivieren. Deaktivieren Sie dazu die Zuordnung „Gruppen“.

12. Im Feld **Bereich** unter **Einstellungen** wird festgelegt, welche Benutzer und Gruppen synchronisiert werden. Wählen Sie **Nur zugewiesene Benutzer und Gruppen synchronisieren** (empfohlen) aus, damit nur Benutzer und Gruppen synchronisiert werden, die auf der Registerkarte **Benutzer und Gruppen** zugewiesen sind.
13. Legen Sie den **Bereitstellungsstatus** nach Abschluss der Konfiguration auf **Ein** fest.
14. Wählen Sie **Speichern**, um den Azure AD-Bereitstellungsdienst zu starten.
15. Wenn nur zugewiesene Benutzer und Gruppen synchronisiert werden (empfohlen), sollten Sie darauf achten, dass die Registerkarte **Benutzer und Gruppen** ausgewählt ist und die Benutzer bzw. Gruppen zugewiesen sind, die synchronisiert werden sollen.

Nachdem der erste Zyklus gestartet wurde, können Sie im linken Bereich die Option **Bereitstellungsprotokolle** auswählen, um den Fortschritt zu überwachen. Hier werden alle Aktionen angezeigt, die vom Bereitstellungsdienst für Ihre App durchgeführt werden. Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](check-status-user-account-provisioning.md).

> [!NOTE]
> Der erste Zyklus dauert länger als spätere Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Schritt 5: Veröffentlichen Ihrer Anwendung im Azure AD-Anwendungskatalog

Wenn Sie eine Anwendung erstellen, die von mehreren Mandanten verwendet wird, können Sie sie im Azure AD-Anwendungskatalog zur Verfügung stellen. Dies erleichtert Organisationen das Auffinden der Anwendung und das Konfigurieren der Bereitstellung. Das Veröffentlichen Ihrer App im Azure AD-Katalog und das Verfügbarmachen der Bereitstellung für andere ist einfach. Die entsprechenden Schritte sind [hier](../develop/v2-howto-app-gallery-listing.md) angegeben. Microsoft wird mit Ihnen zusammenarbeiten, um Ihre Anwendung in unseren Katalog zu integrieren, Ihren Endpunkt zu testen und die [Dokumentation](../saas-apps/tutorial-list.md) zum Onboarding für Kunden freizugeben.

### <a name="gallery-onboarding-checklist"></a>Onboardingprüfliste für den Katalog
Verwenden Sie die folgende Prüfliste, um ein schnelles Onboarding Ihrer Anwendung zu gewährleisten und den Kunden eine reibungslose Bereitstellung zu bieten. Die Informationen werden beim Onboarding für den Katalog von Ihnen erfasst. 
> [!div class="checklist"]
> * Unterstützung eines [SCIM 2.0](#step-2-understand-the-azure-ad-scim-implementation)-Benutzer- und Gruppenendpunkts (nur einer ist erforderlich, aber beide werden empfohlen)
> * Unterstützung von mindestens 25 Anforderungen pro Sekunde und Mandant, um sicherzustellen, dass Benutzer und Gruppen ohne Verzögerung bereitgestellt werden bzw. deren Bereitstellung aufgehoben wird (erforderlich)
> * Einrichtung eines Kontakts für technische und supportbezogene Fragen, um Kunden nach dem Katalogonboarding zu unterstützen (erforderlich)
> * 3 nicht ablaufende Testanmeldeinformationen für Ihre Anwendung (erforderlich)
> * Unterstützung der OAuth-Autorisierungscodegenehmigung oder eines langlebigen Tokens gemäß der Beschreibung weiter unten (erforderlich)
> * Einrichtung einer Anlaufstelle für technische und supportbezogene Fragen, um Kunden nach dem Katalogonboarding zu unterstützen (erforderlich)
> * Unterstützung der Aktualisierung mehrerer Gruppenmitgliedschaften mit einem einzelnen PATCH (empfohlen) 
> * Öffentliche Dokumentation Ihres SCIM-Endpunkts (empfohlen) 
> * [Unterstützung der Schemaerkennung (empfohlen)](https://tools.ietf.org/html/rfc7643#section-6)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorisierung für Bereitstellungsconnectors im Anwendungskatalog
Die SCIM-Spezifikation definiert kein SCIM-spezifisches Schema für die Authentifizierung und Autorisierung. Sie stützt sich auf die Verwendung bestehender Branchenstandards. Der Azure AD-Bereitstellungsclient unterstützt zwei Autorisierungsmethoden für Anwendungen im Katalog. 

|Autorisierungsmethode|Vorteile|Nachteile|Support|
|--|--|--|--|
|Benutzername und Kennwort (von Azure AD nicht empfohlen oder unterstützt)|Einfache Implementierung|Unsicher – [Ihr KeNNwort ist unwichtig](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Unterstützt von Fall zu Fall für Katalog-Apps. Nicht unterstützt für Nicht-Katalog-Apps.|
|Langlebiges Bearertoken|Bei langlebigen Token muss kein Benutzer anwesend sein. Admins können sie beim Einrichten der Bereitstellung leicht verwenden.|Langlebige Token können nur schwer mit einem Administrator geteilt werden, ohne unsichere Methoden wie E-Mail zu verwenden. |Unterstützt für Katalog- und Nicht-Katalog-Apps. |
|OAuth-Autorisierungscodegenehmigung|Zugriffstoken sind sehr viel kurzlebiger als Kennwörter und verfügen über einen automatischen Aktualisierungsmechanismus, den langlebige Bearertoken nicht haben.  Bei der ersten Autorisierung muss ein echter Benutzer anwesend sein, was einen gewissen Grad an Verantwortlichkeit bedeutet. |Ein Benutzer muss anwesend sein. Wenn der Benutzer das Unternehmen verlässt, wird das Token ungültig, und die Autorisierung muss erneut erfolgen.|Wird nur für Katalog-Apps unterstützt. Sie können über die Benutzeroberfläche jedoch ein Zugriffstoken als geheimes Token für kurzfristige Testzwecke bereitstellen. An der Unterstützung für Autorisierung über OAuth-Code für nicht im Katalog enthaltenen Anwendungen wird noch gearbeitet.|
|Genehmigung von OAuth-Clientanmeldeinformationen|Zugriffstoken sind sehr viel kurzlebiger als Kennwörter und verfügen über einen automatischen Aktualisierungsmechanismus, den langlebige Bearertoken nicht haben. Sowohl die Autorisierungscodegenehmigung als auch die Genehmigung von Clientanmeldeinformationen gehören zum gleichen Typ Zugriffstoken. Ein Wechsel zwischen diesen beiden Methoden ist daher für die API transparent.  Die Bereitstellung kann vollständig automatisiert werden. Neue Token können ohne Benutzerinteraktion und im Hintergrund angefordert werden. ||Nicht unterstützt für Katalog- und Nicht-Katalog-Apps. Diese Unterstützung befindet sich in unserem Backlog.|

> [!NOTE]
> Es wird nicht empfohlen, das Tokenfeld auf der Benutzeroberfläche der benutzerdefinierten App zur Konfiguration der Azure AD-Bereitstellung leer zu lassen. Das generierte Token steht in erster Linie zu Testzwecken zur Verfügung.

**Ablauf der OAuth-Autorisierungscodegenehmigung:** Der Bereitstellungsdienst unterstützt die [Autorisierungscodegenehmigung](https://tools.ietf.org/html/rfc6749#page-24). Nachdem Sie Ihre Anforderung zur Veröffentlichung Ihrer App im Katalog übermittelt haben, wird unser Team mit Ihnen zusammenarbeiten, um die folgenden Informationen zu sammeln:
*  URL für Autorisierung: Eine URL des Clients, um die Autorisierung des Ressourcenbesitzers über die Umleitung des Benutzer-Agents zu erhalten. Der Benutzer wird zu dieser URL umgeleitet, um den Zugriff zu autorisieren. Beachten Sie, dass diese URL derzeit nicht für einzelne Mandanten konfiguriert werden kann.
*  URL für den Tokenaustausch: Eine URL des Clients, um eine Autorisierungsgenehmigung für ein Zugriffstoken auszutauschen, typischerweise mit Clientauthentifizierung. Beachten Sie, dass diese URL derzeit nicht für einzelne Mandanten konfiguriert werden kann.
*  Client-ID: Der Autorisierungsserver stellt dem registrierten Client eine Client-ID aus, die eine eindeutige Zeichenfolge ist, die die vom Client bereitgestellten Registrierungsinformationen darstellt.  Die Client-ID ist kein Geheimnis. Sie wird dem Ressourcenbesitzer verfügbar gemacht und **darf nicht** allein für die Clientauthentifizierung verwendet werden.  
*  Geheimer Clientschlüssel: Der geheime Clientschlüssel ist ein Geheimnis, das vom Autorisierungsserver generiert wird. Es sollte ein eindeutiger Wert sein, der nur dem Autorisierungsserver bekannt ist. 

Beachten Sie, dass OAuth v1 aufgrund der Gefährdung des geheimen Clientschlüssels nicht unterstützt wird. OAuth v2 dagegen wird unterstützt.  

Bewährte Methoden (empfohlen, aber nicht erforderlich):
* Unterstützen Sie mehrere Umleitungs-URLs. Administratoren können die Bereitstellung sowohl über „portal.azure.com“ als auch über „aad.portal.azure.com“ konfigurieren. Die Unterstützung mehrerer Umleitungs-URLs stellt sicher, dass Benutzer den Zugriff von beiden Portalen aus autorisieren können.
* Unterstützen Sie mehrere Geheimnisse, um eine reibungslose Erneuerung der Geheimnisse ohne Ausfallzeiten sicherzustellen. 

OAuth-Autorisierungscodeflow:
1. Ein Benutzer meldet sich im Azure-Portal an und klickt auf „Unternehmensanwendungen“ > „Anwendung“ > „Bereitstellung“ > „Autorisieren“.
2. Der Benutzer wird im Azure-Portal zur Autorisierungs-URL (Anmeldeseite für die Drittanbieter-App) weitergeleitet.
3. Der Administrator stellt Anmeldeinformationen für die Drittanbieteranwendung bereit. 
4. Die Drittanbieter-App leitet den Benutzer zurück zum Azure-Portal und stellt den Autorisierungscode bereit. 
5. Der Azure AD-Bereitstellungsdienst ruft die Token-URL auf und stellt den Autorisierungscode bereit. Die Drittanbieteranwendung reagiert mit dem Zugriffstoken, dem Aktualisierungstoken und einem Ablaufdatum.
6. Wenn der Bereitstellungszyklus beginnt, überprüft der Dienst, ob das aktuelle Zugriffstoken gültig ist und tauscht es bei Bedarf gegen ein neues aus. Das Zugriffstoken wird in jeder für die App veröffentlichten Anforderung bereitgestellt, und die Gültigkeit der Anforderung wird bei jeder Anforderung überprüft.

> [!NOTE]
> Aktuell ist es zwar nicht möglich, OAuth für eine Anwendung einzurichten, die nicht aus dem Katalog stammt, Sie können jedoch manuell ein Zugriffstoken auf Ihrem Autorisierungsserver generieren und in das Feld für ein geheimes Token der Anwendung einfügen, die nicht aus dem Katalog stammt. Dies ermöglicht es Ihnen, die Kompatibilität Ihres SCIM-Servers mit dem SCIM-Client in Azure AD zu überprüfen, bevor Sie ein Onboarding im App-Katalog ausführen, für den die Autorisierung über OAuth-Code unterstützt wird.  

**Langlebige OAuth-Bearertoken:** Wenn Ihre Anwendung den Ablauf der OAuth-Autorisierungscodegenehmigung nicht unterstützt, können Sie auch ein langlebiges OAuth-Bearertoken generieren, mit dem ein Administrator die Integration der Bereitstellung einrichten kann. Das Token sollte unbefristet sein, andernfalls wird der Bereitstellungsauftrag nach Ablauf des Token [unter Quarantäne gestellt](application-provisioning-quarantine-status.md). Dieses Token muss eine Größe von weniger als 1 KB aufweisen.  

Teilen Sie uns über [UserVoice](https://aka.ms/appprovisioningfeaturerequest) mit, wenn weitere Methoden zur Authentifizierung und Autorisierung erforderlich sind.

### <a name="gallery-go-to-market-launch-check-list"></a>Markteinführungsprüfliste für den Katalog
Es empfiehlt sich, die vorhandene Dokumentation zu aktualisieren und unsere gemeinsame Integration in Ihren Marketingkanälen hervorzuheben, um über die Integration zu informieren und Interesse dafür zu wecken.  Die folgende Prüfliste enthält empfohlene Aktivitäten zur Unterstützung der Einführung:

* **Bereitschaft von Vertrieb und Kundensupport:** Stellen Sie sicher, dass Ihre Vertriebs- und Supportteams mit den Integrationsfunktionen vertraut sind und sich zu ihnen äußern können. Weisen Sie Ihre Vertriebs- und Supportteams ein, stellen Sie häufig gestellte Fragen bereit, und nehmen Sie die Integration in Ihr Vertriebsmaterial auf. 
* **Blogbeitrag und/oder Pressemitteilung:** Erstellen Sie einen Blogbeitrag oder eine Pressemitteilung mit einer Beschreibung der gemeinsamen Integration, der Vorteile und der ersten Schritte. [Beispiel: Pressemitteilung von Imprivata und Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Soziale Medien:** Nutzen Sie soziale Medien wie Twitter, Facebook oder LinkedIn, um bei Ihren Kunden für die Integration zu werben. Schließen Sie @AzureAD ein, damit wir Ihren Beitrag retweeten können. [Beispiel: Twitter-Beitrag von Imprivata](https://twitter.com/azuread/status/1123964502909779968)
* **Marketingwebsite:** Erstellen oder aktualisieren Sie Ihre Marketingseiten (Integrationsseite, Partnerseite, Preisseite und Ähnliches), um die Verfügbarkeit der gemeinsamen Integration einzuschließen. [Beispiel: Integrationsseite von Pingboard](https://pingboard.com/org-chart-for), [Integrationsseite von Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Preisseite von Monday.com](https://monday.com/pricing/) 
* **Technische Dokumentation:** Erstellen Sie einen Hilfecenter-Artikel oder eine technische Dokumentation mit den ersten Schritten für Kunden. [Beispiel: Integration von Envoy und Microsoft Azure Active Directory](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Kundenkommunikation:** Machen Sie Kunden über Ihre Kundenkommunikationskanäle (monatliche Newsletter, E-Mail-Kampagnen, Produktanmerkungen) auf die neue Integration aufmerksam. 

## <a name="related-articles"></a>Verwandte Artikel

* [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Apps](user-provisioning.md)
* [Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md)
* [Schreiben von Ausdrücken für Attributzuordnungen](functions-for-customizing-application-data.md)
* [Bereichsfilter für die Benutzerbereitstellung](define-conditional-rules-for-provisioning-user-accounts.md)
* [Kontobereitstellungsbenachrichtigungen](user-provisioning.md)
* [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)

