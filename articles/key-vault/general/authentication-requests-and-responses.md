---
title: Authentifizierung, Anforderungen und Antworten
description: Erfahren Sie, wie Azure Key Vault Anforderungen und Antworten im JSON-Format verwendet und welche Authentifizierung für die Verwendung eines Schlüsseltresors erforderlich ist.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 58616b647affd33e96357e556ab61f85d1c62129
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752276"
---
# <a name="authentication-requests-and-responses"></a>Authentifizierung, Anforderungen und Antworten

Azure Key Vault bietet zwei Typen von Containern zur Speicherung und Verwaltung von Geheimnissen für Ihre Cloudanwendungen:

|Containertyp|Unterstützte Objekttypen|Endpunkt der Datenebene|
|--|--|--|
| **Tresore**|<ul><li>Softwaregeschützte Schlüssel</li><li>HSM-geschützte Schlüssel (mit Premium-SKU)</li><li>Zertifikate</li><li>Speicherkontoschlüssel</li></ul> | https://{Tresorname}.vault.azure.net
|**Verwaltete HSM** |<ul><li>HSM-geschützte Schlüssel</li></ul> | https://{HSM-Name}.managedhsm.azure.net

Hier sind die URL-Suffixe, die für den Zugriff auf die einzelnen Objekttypen verwendet werden

|Objekttyp|URL-Suffix|
|--|--|
|Softwaregeschützte Schlüssel| /keys |
|HSM-geschützte Schlüssel| /keys |
|Geheimnisse|/secrets|
|Zertifikate| /certificates|
|Speicherkontoschlüssel|/storageaccounts
||

Azure Key Vault unterstützt Anforderungen und Antworten im JSON-Format. Anforderungen an Azure Key Vault werden mithilfe von HTTPS mit einigen URL-Parametern und JSON-codierten Anforderungs- und Antworttexten an eine gültige Azure Key Vault-URL geleitet.

Dieses Thema behandelt Besonderheiten für den Azure Key Vault-Dienst. Allgemeine Informationen zur Verwendung von Azure-REST-Schnittstellen, einschließlich Authentifizierung/Autorisierung und zum Abrufen eines Zugriffstokens finden Sie unter [Azure-REST-API-Referenz](/rest/api/azure).

## <a name="request-url"></a>Anfrage-URL  
 Schlüsselverwaltungsvorgänge verwenden „HTTP DELETE“, „GET“, „PATCH“, „PUT“ und „HTTP POST“, und kryptographische Vorgänge für vorhandene Schlüsselobjekte verwenden „HTTP POST“. Clients, die bestimmte HTTP-Verben nicht unterstützen, können auch „HTTP POST“ mit dem Header „X-HTTP-REQUEST verwenden, um das gewünschte Verb anzugeben. Anforderungen, die normalerweise keinen Text erfordern, müssen bei Verwendung von „HTTP POST“ einen leeren Text enthalten, z.B. bei Verwendung von „POST“ anstelle von „DELETE“.  

 Zum Arbeiten mit Objekten in Azure Key Vault sind folgende Beispiel-URLs verfügbar:  

- Zum Erstellen (CREATE) eines Schlüssels namens „TESTKEY“ in einer Key Vault-Instanz verwenden Sie: `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Zum Importieren (IMPORT) eines Schlüssels namens „IMPORTEDKEY“ in einer Key Vault-Instanz verwenden Sie: `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Zum Abrufen (GET) eines Geheimnisses namens „MYSECRET“ in einer Key Vault-Instanz verwenden Sie: `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Zum Signieren (SIGN) eines Hashes mithilfe eines Schlüssels namens „TESTKEY“ in einer Key Vault-Instanz verwenden Sie: `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

- Die Autorität für eine Anforderung an einen Schlüsseltresor ist immer wie folgt:
  - Für Tresore: `https://{keyvault-name}.vault.azure.net/`
  - Für verwaltete HSMs: `https://{HSM-name}.managedhsm.azure.net/`

  Schlüssel werden immer unter dem Pfad „/keys“ gespeichert, Geheimnisse werden immer unter dem Pfad „/secrets“ gespeichert.  

## <a name="api-version"></a>API-Version  
 Der Azure Key Vault-Dienst unterstützt Protokollversionsverwaltung, um Kompatibilität Clients. Allerdings sind für diese Clients nicht alle Funktionen verfügbar. Clients verwenden, müssen den Abfragezeichenfolgen-Parameter `api-version` verwenden, um die Version des Protokolls anzugeben, das sie unterstützen, da kein Standard gilt.  

 Azure Key Vault-Protokollversionen folgen einem Datumsnummerierungsschema mit dem Format {JJJJ}.{MM}.{TT}.  

## <a name="request-body"></a>Anforderungstext  
 Gemäß der HTTP-Spezifikation dürfen GET-Vorgänge KEINEN Anforderungstext haben, während POST- und PUT-Vorgänge einen Anforderungstext haben müssen. Der Text in DELETE-Vorgängen ist in HTTP optional.  

 Sofern in der Vorgangsbeschreibung nicht anders angegeben, muss der Inhaltstyp des Anforderungstexts „application/json“ sein und ein geeignetes serialisiertes JSON-Objekt für den Inhaltstyp enthalten.  

 Sofern in der Vorgangsbeschreibung nicht anders angegeben, muss der Accept-Anforderungsheader den Medientyp „application/json“ enthalten.  

## <a name="response-body"></a>Antworttext  
 Sofern in der Vorgangsbeschreibung nicht anders angegeben, ist der Inhaltstyp des Antworttexts von sowohl erfolgreichen als auch fehlgeschlagenen Vorgängen „application/json“ und enthält ausführliche Fehlerinformationen.  

## <a name="using-http-post"></a>Verwenden von „HTTP POST“  
 Einige Clients können bestimmte HTTP-Verben wie „PATCH“ oder „DELETE“ möglicherweise nicht verwenden. Azure Key Vault unterstützt „HTTP POST“ als Alternative für diese Clients, vorausgesetzt, dass der Client auch den Header „X-HTTP-METHOD“ zum Angeben des ursprünglichen HTTP-Verbs enthält. Unterstützung für „HTTP POST“ ist für jede der in diesem Dokument definierten APIs angegeben.  

## <a name="error-responses"></a>Fehlerantworten  
 Die Fehlerbehandlung verwendet HTTP-Statuscodes. Typische Ergebnisse sind:  

- 2xx – Erfolg: Für normalen Betrieb. Der Antworttext wird das erwartete Ergebnis enthalten.  

- 3xx – Umleitung: Der Code 304 (Nicht geändert) kann zurückgegeben werden, um einen bedingten GET-Vorgang zu erfüllen. Weitere 3xx-Codes können künftig verwendet werden, um DNS- und Pfadänderungen anzugeben.  

- 4xx – Clientfehler: Für ungültige Anforderungen, fehlende Schlüssel, Syntaxfehler, ungültige Parameter, Authentifizierungsfehler und Ähnliches. Der Antworttext wird ausführliche Fehlererläuterungen enthalten.  

- 5xx – Serverfehler: Für interne Serverfehler. Der Antworttext wird zusammengefasste Fehlerinformationen enthalten.  

  Das System ist darauf ausgelegt, hinter einem Proxy oder einer Firewall zu arbeiten. Daher erhält ein Client möglicherweise andere Fehlercodes.  

  Azure Key Vault gibt auch Fehlerinformationen im Antworttext zurück, wenn ein Problem auftritt. Der Antworttext ist JSON-formatiert und hat das Format:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Authentifizierung  
 Alle Anforderungen an Azure Key Vault MÜSSEN authentifiziert werden. Azure Key Vault unterstützt Azure Active Directory-Zugriffstoken, die mithilfe von OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)] abgerufen werden können. 
 
 Weitere Informationen zur Registrierung Ihrer Anwendung und zur Authentifizierung für die Verwendung von Azure Key Vault finden Sie unter [Registrieren Ihrer Clientanwendung bei Azure AD](/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Zugriffstoken müssen mithilfe des HTTP-Autorisierungsheaders an den Dienst gesendet werden:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Wenn ein Zugriffstoken nicht bereitstellt wird, oder ein Token vom Dienst nicht akzeptiert wird, wird ein HTTP 401-Fehler an den Client zurückgegeben, der den Header „WWW-Authenticate“ enthält, z.B.:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Die Parameter im Header „WWW-Authenticate“ sind:  

-   authorization: Die Adresse des OAuth2-Autorisierungsdiensts, die zum Abrufen eines Zugriffstokens für die Anforderung verwendet werden kann.  

-   resource: Der Name der in der Autorisierungsanforderung zu verwendenden Ressource (`https://vault.azure.net`).

> [!NOTE]
> Key Vault SDK-Clients für Geheimnisse, Zertifikate und Schlüssel stellen beim ersten Aufrufen von Key Vault kein Zugriffstoken zum Abrufen von Mandanteninformationen bereit. Es wird erwartet, dass bei der Verwendung des Key Vault SDK-Clients ein HTTP 401-Fehler angezeigt wird. Bei diesem Client zeigt Key Vault der Anwendung den WWW-Authenticate-Header mit der Ressource und dem Mandanten, wo er das Token anfordern muss. Wenn alles richtig konfiguriert ist, enthält der zweite Aufruf von der Anwendung an Key Vault ein gültiges Token und ist erfolgreich. 
