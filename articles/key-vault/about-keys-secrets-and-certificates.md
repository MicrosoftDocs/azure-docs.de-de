---
title: Informationen zu Schlüsseln, Geheimnissen und Zertifikaten im Azure Key Vault
description: Hier finden Sie eine Übersicht über die Azure Key Vault-REST-Schnittstelle sowie Informationen für Entwickler zu Schlüsseln, Geheimnissen und Zertifikaten.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b50f98c4abaeda3ac1805b73aa18fe6c29596426
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708686"
---
# <a name="about-keys-secrets-and-certificates"></a>Informationen zu Schlüsseln, Geheimnissen und Zertifikaten

Microsoft Azure-Anwendungen und -Benutzer können verschiedene Arten von Geheimnissen und Schlüsseldaten in Azure Key Vault speichern:

- Kryptografische Schlüssel: Unterstützen mehrere Schlüsseltypen und Algorithmen und ermöglicht die Verwendung von Hardwaresicherheitsmodulen (Hardware Security Modules, HSM) für Schlüssel von hohem Wert. 
- Geheimnisse: Bieten einen sicheren Speicher für Geheimnisse wie Kennwörter und Datenbank-Verbindungszeichenfolgen.
- Zertifikate: Unterstützen Zertifikate, die auf Schlüsseln und Geheimnissen aufbauen, und fügt ein Feature für die automatisierte Verlängerung hinzu.
- Azure Storage: Kann die Schlüssel eines Azure Storage-Kontos für Sie verwalten. Intern kann Key Vault Schlüssel für ein Azure Storage-Konto auflisten (synchronisieren) und die Schlüssel in regelmäßigen Abständen erneut generieren (rotieren). 

Weitere allgemeine Informationen zu Key Vault finden Sie unter [Was ist Azure Key Vault?](/azure/key-vault/key-vault-whatis)

## <a name="azure-key-vault"></a>Azure Key Vault

Die folgenden Abschnitte enthalten allgemeine Informationen über die Implementierung des Key Vault-Diensts.

### <a name="supporting-standards"></a>Unterstützung von Standards

Die Spezifikationen von JavaScript Object Notation (JSON) und JavaScript Object Signing and Encryption (JOSE) sind wichtige Hintergrundinformationen.  

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Datentypen

In den JOSE-Spezifikationen finden Sie relevante Datentypen für Schlüssel, Verschlüsselung und Signatur.  

-   **algorithm**: ein unterstützter Algorithmus für einen Schlüsselvorgang, z.B. RSA1_5.  
-   **ciphertext-value**: Verschlüsselungstextoktette, codiert mit Base64URL.  
-   **digest-value**: die Ausgabe eines Hashalgorithmus, codiert mit Base64URL.  
-   **key-type**: einer der unterstützten Schlüsseltypen, z.B. RSA (Rivest-Shamir-Adleman).  
-   **plaintext-value**: Klartextoktette, codiert mit Base64URL.  
-   **signature-value**: die Ausgabe eines Signaturalgorithmus, codiert mit Base64URL.  
-   **base64URL**: ein mit Base64URL [RFC4648] codierter Binärwert.  
-   **boolean**: entweder TRUE oder FALSE.  
-   **Identity**: eine Identität in Azure Active Directory (AAD).  
-   **IntDate**: ein dezimaler JSON-Wert, der die Anzahl von Sekunden von 1970-01-01T0:0:0Z UTC bis zum angegebenen UTC-Datum / zur angegebenen UTC-Uhrzeit darstellt. Details in Bezug auf Datum/Uhrzeit im Allgemeinen und UTC im Besonderen finden Sie der Dokumentation zu RFC 3339.  

### <a name="objects-identifiers-and-versioning"></a>Objekte, Bezeichner und Versionsverwaltung

In Key Vault gespeicherte Objekte werden versioniert, wenn eine neue Instanz eines Objekts erstellt wird. Jeder Version wird ein eindeutiger Bezeichner und eine URL zugewiesen. Wenn ein Objekt zum ersten Mal erstellt wird, erhält es einen eindeutigen Versionsbezeichner und wird als aktuelle Version des Objekts gekennzeichnet. Beim Erstellen einer neuen Instanz mit dem gleichen Objektnamen erhält das neue Objekt einen eindeutigen Versionsbezeichner und wird zur aktuellen Version.  

Objekte in Key Vault können mit dem aktuellen Bezeichner oder einem versionsspezifischen Bezeichner adressiert werden. Wenn z.B. für einen Schlüssel mit dem Namen `MasterKey` Vorgänge mit dem aktuellen Bezeichner durchgeführt werden, verwendet das System die neueste verfügbare Version. Das Ausführen von Vorgängen mit dem versionsspezifischen Bezeichner veranlasst das System, die spezifische Version des Objekts zu verwenden.  

Objekte werden in Key Vault über eine URL eindeutig identifiziert. Unabhängig vom geografischen Standort dürfen keine zwei Objekte im System die gleiche URL aufweisen. Die vollständige URL zu einem Objekt wird Objektbezeichner genannt. Die URL besteht aus einem Präfix, das den Schlüsseltresor, den Objekttyp, den vom Benutzer bereitgestellten Objektnamen und eine Objektversion identifiziert. Beim Objektnamen wird Groß-/Kleinschreibung nicht unterschieden, und er ist unveränderlich. Bezeichner, die nicht die Objektversion enthalten, heißen Basisbezeichner.  

Weitere Informationen finden Sie unter [Authentifizierung, Anforderungen und Antworten](authentication-requests-and-responses.md).

Ein Objektbezeichner hat das folgende allgemeine Format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Hinweis:  

|||  
|-|-|  
|`keyvault-name`|Der Name eines Schlüsseltresors im Microsoft Azure Key Vault-Dienst.<br /><br /> Schlüsseltresornamen werden vom Benutzer ausgewählt und sind global eindeutig.<br /><br /> Der Key Vault-Name muss zwischen 3 und 24 Zeichen lang sein und darf nur die Ziffern 0-9, die Buchstaben a-z und A-Z sowie das Minuszeichen („-“) enthalten.|  
|`object-type`|Der Typ des Objekts, entweder „Schlüssel“ oder „Geheimnisse“.|  
|`object-name`|Ein `object-name` ist ein vom Benutzer bereitgestellter Name und muss innerhalb eines Schlüsseltresors eindeutig sein. Der Name muss zwischen 1 und 127 Zeichen lang sein und darf nur die Ziffern 0-9, die Buchstaben a-z und A-Z sowie das Minuszeichen („-“) enthalten.|  
|`object-version`|Ein `object-version` ist ein vom System generierter, 32 Zeichen langer Zeichenfolgenbezeichner, der optional verwendet wird, um eine eindeutige Version eines Objekts zu adressieren.|  

## <a name="key-vault-keys"></a>Key Vault-Schlüssel

### <a name="keys-and-key-types"></a>Schlüssel und Schlüsseltypen

Kryptografische Schlüssel in Key Vault werden als JSON Web Key-Objekte (JWK) dargestellt. Die grundlegenden JWK/JWA-Spezifikationen wurden erweitert, um Schlüsseltypen zu ermöglichen, die für die Key Vault-Implementierung eindeutig sind. Das Importieren von Schlüsseln mit anbieterspezifischer HSM-Paketerstellung ermöglicht einen sicheren Transport von Schlüsseln, die nur in Key Vault-HSMs verwendet werden dürfen.  

- **„Soft“-Schlüssel**: Schlüssel, die in der Software von Key Vault verarbeitet, aber im Ruhezustand unter Verwendung eines Systemschlüssels, der sich in einem HSM befindet, verschlüsselt werden. Clients können einen vorhandenen RSA- oder EC-Schlüssel (Elliptic Curve, elliptische Kurve) importieren oder anfordern, dass Key Vault einen solchen Schlüssel generiert.
- **„Hard“-Schlüssel**: Schlüssel, die in einem HSM (Hardwaresicherheitsmodul) verarbeitet werden. Diese Schlüssel werden in einer der HSM Security Worlds von Key Vault geschützt (es gibt in jeder geografischen Region eine Security World, um die Isolation aufrechtzuerhalten). Clients können einen RSA- oder EC-Schlüssel importieren, entweder in „Soft“-Form oder durch Exportieren von einem kompatiblen HSM-Gerät. Clients können auch anfordern, dass Key Vault einen Schlüssel generiert. Dieser Schlüsseltyp fügt dem JWK das T-Attribut hinzu, um das HSM-Schlüsselmaterial zu tragen.

     Weitere Informationen zu geografischen Grenzen finden Sie unter [Datenschutz](https://azure.microsoft.com/support/trust-center/privacy/).  

Key Vault unterstützt nur RSA- und Elliptic Curve-Schlüssel. 

-   **EC**: „Soft“-Elliptic Curve-Schlüssel.
-   **EC-HSM**: „Hard“-Elliptic Curve-Schlüssel.
-   **RSA**: „Soft“-RSA-Schlüssel.
-   **RSA-HSM**: „Hard“-RSA-Schlüssel.

Key Vault unterstützt RSA-Schlüssel der Größen 2048, 3072 und 4096. Key Vault unterstützt die Elliptic Curve-Schlüsseltypen P-256, P-384, P-521 und P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Kryptografischer Schutz

Die kryptografischen Module, die Key Vault verwendet – sowohl HSM als auch Software – sind FIPS-geprüft (Federal Information Processing Standards). Für die Ausführung im FIPS-Modus müssen Sie keine besonderen Maßnahmen ergreifen. Schlüssel, die als HSM-geschützt **erstellt** oder **importiert** wurden, werden in einem HSM verarbeitet und gemäß FIPS 140-2 Level 2 geprüft. Schlüssel, die als softwaregeschützt **erstellt** oder **importiert** wurden, werden in kryptografischen Modulen verarbeitet und gemäß FIPS 140-2 Level 1 geprüft. Weitere Informationen finden Sie unter [Schlüssel und Schlüsseltypen](#keys-and-key-types).

###  <a name="ec-algorithms"></a>EC-Algorithmen
 Die folgenden Algorithmusbezeichner werden mit EC- und EC-HSM-Schlüsseln in Key Vault unterstützt. 

#### <a name="curve-types"></a>Kurventypen

-   **P-256**: Die NIST-Kurve P-256, definiert unter [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K**: Die SEC-Kurve SECP256K1, definiert unter [SEC 2: Recommended Elliptic Curve Domain Parameters (SEC 2: Empfohlene Domänenparameter für elliptische Kurven)](https://www.secg.org/sec2-v2.pdf).
-   **P-384**: Die NIST-Kurve P-384, definiert unter [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521**: Die NIST-Kurve P-521, definiert unter [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** – ECDSA für SHA-256-Hashs und -Schlüssel, erstellt mit P-256-Kurve. Dieser Algorithmus wird im [RFC 7518](https://tools.ietf.org/html/rfc7518) beschrieben.
-   **ES256K** – ECDSA für SHA-256-Hashs und -Schlüssel, erstellt mit P-256K-Kurve. Bei diesem Algorithmus steht die Standardisierung noch aus.
-   **ES384** – ECDSA für SHA-384-Hashs und -Schlüssel, erstellt mit P-384-Kurve. Dieser Algorithmus wird im [RFC 7518](https://tools.ietf.org/html/rfc7518) beschrieben.
-   **ES512** – ECDSA für SHA-512-Hashs und -Schlüssel, erstellt mit P-521-Kurve. Dieser Algorithmus wird im [RFC 7518](https://tools.ietf.org/html/rfc7518) beschrieben.

###  <a name="rsa-algorithms"></a>RSA-Algorithmen  
 Die folgenden Algorithmusbezeichner werden mit RSA- und RSA-HSM-Schlüsseln in Key Vault unterstützt.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5**: RSAES-PKCS1-V1_5 [RFC3447] Schlüsselverschlüsselung  
-   **RSA-OAEP**: RSAES unter Verwendung von Optimal Asymmetric Encryption Padding (OAEP) [RFC3447], wobei die Standardparameter durch RFC 3447 in Abschnitt A.2.1 angegeben werden. Diese Standardparameter verwenden eine Hashfunktion von SHA-1 und eine Maskengenerierungsfunktion von MGF1 mit SHA-1.  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256**: RSASSA-PKCS-v1_5 mithilfe von SHA-256. Der von der Anwendung bereitgestellte Zusammenfassungswert muss mithilfe von SHA-256 berechnet werden und 32 Byte lang sein.  
-   **RS384**: RSASSA-PKCS-v1_5 mithilfe von SHA-384. Der von der Anwendung bereitgestellte Zusammenfassungswert muss mithilfe von SHA-384 berechnet werden und 48 Byte lang sein.  
-   **RS512**: RSASSA-PKCS-v1_5 mithilfe von SHA-512. Der von der Anwendung bereitgestellte Zusammenfassungswert muss mithilfe von SHA-512 berechnet werden und 64 Byte lang sein.  
-   **RSNULL**: Siehe [RFC2437], ein spezialisierter Anwendungsfall, um bestimmte TLS-Szenarios zu ermöglichen.  

###  <a name="key-operations"></a>Schlüsselvorgänge

Key Vault unterstützt die folgenden Vorgänge bei Schlüsselobjekten:  

-   **Create** (Erstellen): Ermöglicht einem Client, einen Schlüssel in Key Vault zu erstellen. Der Wert des Schlüssels wird von Key Vault generiert und gespeichert und nicht für den Client freigegeben. In Key Vault können asymmetrische Schlüssel erstellt werden.  
-   **Import:** Ermöglicht einem Client, einen vorhandenen Schlüssel in Key Vault zu importieren. Asymmetrische Schlüssel können mithilfe einer Reihe unterschiedlicher Paketerstellungsmethoden in einem JWK-Konstrukt in Key Vault importiert werden. 
-   **Update** (Aktualisieren): Ermöglicht einem Client mit ausreichenden Berechtigungen, die Metadaten (Schlüsselattribute) zu ändern, die einem zuvor in Key Vault gespeicherten Schlüssel zugeordnet sind.  
-   **Löschen**: Ermöglicht einem Client mit ausreichenden Berechtigungen das Löschen eines Schlüssels aus Key Vault.  
-   **List** (Auflisten): Ermöglicht einem Client das Auflisten aller Schlüssel in einem bestimmten Schlüsseltresor.  
-   **List versions** (Versionen auflisten): Ermöglicht einem Client das Auflisten aller Versionen eines bestimmten Schlüssels in einem bestimmten Key Vault.  
-   **Get** (Abrufen): Ermöglicht einem Client das Abrufen des öffentlichen Teils eines bestimmten Schlüssels in einem Schlüsseltresor.  
-   **Backup** (Sichern): Exportiert einen Schlüssel in einer geschützten Form.  
-   **Restore** (Wiederherstellen): Importiert einen zuvor gesicherten Schlüssel.  

Weitere Informationen finden Sie unter [Schlüsselvorgänge in der Referenz zur REST-API für Azure Key Vault](/rest/api/keyvault).  

Nachdem ein Schlüssel in Key Vault erstellt wurde, können die folgenden kryptografischen Vorgänge mit dem Schlüssel ausgeführt werden:  

-   **Sign an Verify** (Signieren und überprüfen): Streng genommen handelt es sich hierbei um den Vorgang „sign hash“ (Hash signieren) oder „verify hash“ (Hash überprüfen), da Key Vault das Hashing von Inhalten im Rahmen der Signaturerstellung nicht unterstützt. Anwendungen sollten ein Hashing der Daten durchführen, die lokal signiert werden sollen, und dann anfordern, dass Key Vault den Hash signiert. Die Überprüfung signierter Hashes wird aus Gründen der Vereinfachung für Anwendungen unterstützt, die möglicherweise keinen Zugriff auf (öffentliches) Schlüsselmaterial haben. Um eine optimale Anwendungsleistung zu erzielen, stellen Sie sicher, dass die Vorgänge lokal ausgeführt werden.  
-   **Key Encryption / Wrapping** (Verschlüsselung mit Schlüssel / Umschließen): Ein in Key Vault gespeicherter Schlüssel kann zum Schutz eines anderen Schlüssels verwendet werden. In der Regel handelt es sich dabei um einen symmetrischen Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK). Wenn der Schlüssel in Key Vault asymmetrisch ist, wird die Schlüsselverschlüsselung verwendet. RSA-OAEP und die WRAPKEY/UNWRAPKEY-Vorgänge sind beispielsweise äquivalent zu ENCRYPT/DECRYPT. Wenn der Schlüssel in Key Vault symmetrisch ist, wird die Schlüsselumschließung verwendet. Beispiel: AES-KW. Der WRAPKEY-Vorgang wird aus Gründen der Vereinfachung für Anwendungen unterstützt, die möglicherweise keinen Zugriff auf (öffentliches) Schlüsselmaterial haben. Um eine optimale Anwendungsleistung zu erzielen, sollten WRAPKEY-Vorgänge lokal ausgeführt werden.  
-   **Encrypt and Decrypt** (Verschlüsseln und Entschlüsseln): Ein in Key Vault gespeicherter Schlüssel kann zum Verschlüsseln oder Entschlüsseln eines einzelnen Datenblocks verwendet werden. Die Größe des Blocks wird durch den Schlüsseltyp und den ausgewählten Verschlüsselungsalgorithmus bestimmt. Der ENCRYPT-Vorgang wird aus Gründen der Vereinfachung für Anwendungen unterstützt, die möglicherweise keinen Zugriff auf (öffentliches) Schlüsselmaterial haben. Um eine optimale Anwendungsleistung zu erzielen, sollten ENCRYPT-Vorgänge lokal ausgeführt werden.  

Obwohl WRAPKEY/UNWRAPKEY mit asymmetrischen Schlüsseln überflüssig erscheinen mögen (da die Vorgänge äquivalent zu ENCRYPT/DECRYPT sind), ist die Verwendung unterschiedlicher Vorgänge wichtig. Die Unterscheidung sorgt für eine semantische und autorisierungsbezogene Trennung dieser Vorgänge sowie für Konsistenz, wenn andere Schlüsseltypen vom Dienst unterstützt werden.  

Key Vault unterstützt keine EXPORT-Vorgänge. Sobald ein Schlüssel im System bereitgestellt ist, kann er weder extrahiert noch sein Schlüsselmaterial geändert werden. Möglicherweise benötigen Key Vault-Benutzer ihren Schlüssel jedoch für andere Anwendungsfälle, beispielsweise nachdem er gelöscht wurde. In diesem Fall können die Vorgänge BACKUP und RESTORE verwendet werden, um den Schlüssel in geschützter Form zu exportieren und zu importieren. Vom BACKUP-Vorgang erstellte Schlüssel können außerhalb von Key Vault nicht verwendet werden. Alternativ dazu kann der IMPORT-Vorgang für mehrere Key Vault-Instanzen verwendet werden.  

Benutzer können die kryptografischen Vorgänge, die Key Vault pro Schlüssel unterstützt, mithilfe der key_ops-Eigenschaft des JWK-Objekts einschränken.  

Weitere Informationen zu JWK-Objekten finden Sie unter [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Schlüsselattribute

Zusätzlich zum Schlüsselmaterial können die folgenden Attribute angegeben werden. In einer JSON-Anforderung sind die Attribute Schlüsselwort und Klammern, „{“ „}“, auch dann erforderlich, wenn keine Attribute angegeben werden.  

- *enabled*: Boolesch, optional, Standardwert ist **true**. Gibt an, ob der Schlüssel aktiviert und für kryptografische Vorgänge verwendbar ist. Das Attribut *enabled* wird in Verbindung mit *nbf* und *exp* verwendet. Wenn ein Vorgang zwischen *nbf* und *exp* auftritt, wird er nur zugelassen, wenn *enabled* auf **true** gesetzt ist. Vorgänge außerhalb des *nbf* / *exp*-Fensters sind automatisch nicht zugelassen, mit Ausnahme bestimmter Vorgangstypen unter [besonderen Bedingungen](#date-time-controlled-operations).
- *nbf*: IntDate, optional, Standardwert ist „now“. Das Attribut *nbf* („not before“, nicht vor) gibt den Zeitpunkt an, vor dem der Schlüssel NICHT für kryptografische Vorgänge verwendet werden darf, mit Ausnahme bestimmter Vorgangstypen unter [besonderen Bedingungen](#date-time-controlled-operations). Die Verarbeitung des *nbf*-Attributs erfordert, dass das aktuelle Datum / die aktuelle Uhrzeit mindestens dem Wert des *nbf*-Attributs entsprechen MUSS. Key Vault KANN einen kleinen Spielraum von in der Regel nicht mehr als einigen wenigen Minuten einräumen, um Abweichungen der Uhr zu berücksichtigen. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.  
- *exp*: IntDate, optional, Standardwert ist „forever“. Das Attribut *exp* („expiration time“, Ablaufzeit) gibt die Ablaufzeit an, nach deren Verstreichen der Schlüssel NICHT für kryptografische Vorgänge verwendet werden darf, mit Ausnahme bestimmter Vorgangstypen unter [besonderen Bedingungen](#date-time-controlled-operations). Die Verarbeitung des *exp*-Attributs erfordert, dass das aktuelle Datum / die aktuelle Uhrzeit vor dem Wert des *exp*-Attributs liegen MUSS. Key Vault KANN einen kleinen Spielraum von in der Regel nicht mehr als einigen wenigen Minuten einräumen, um Abweichungen der Uhr zu berücksichtigen. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.  

Es gibt zusätzliche schreibgeschützte Attribute, die in alle Antworten einbezogen werden, die Schlüsselattribute enthalten:  

- *created*: IntDate, optional. Das *created*-Attribut gibt an, wann diese Version des Schlüssels erstellt wurde. Der Wert ist NULL für Schlüssel, die vor dem Hinzufügen dieses Attributs erstellt wurden. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.  
- *updated*: IntDate, optional. Das *updated*-Attribut gibt an, wann diese Version des Schlüssels aktualisiert wurde. Der Wert ist NULL für Schlüssel, die vor dem Hinzufügen dieses Attributs zuletzt aktualisiert wurden. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.  

Weitere Informationen zu IntDate und anderen Datentypen finden Sie unter [Datentypen](#data-types).  

#### <a name="date-time-controlled-operations"></a>Durch Datum und Uhrzeit gesteuerte Vorgänge

Noch nicht gültige und abgelaufene Schlüssel, die außerhalb des *nbf* / *exp*-Fensters liegen, sind in **decrypt**-, **unwrap**- und **verify**-Vorgängen verwendbar (ohne Rückgabe von „403 Verboten“). Grund für die Verwendung des „Noch-nicht-gültig“-Status ist, den Test eines Schlüssels vor der Verwendung in der Produktion zu ermöglichen. Grund für die Verwendung des abgelaufenen Zustands ist, Wiederherstellungsvorgänge bei Daten zu ermöglichen, die erstellt wurden, als der Schlüssel gültig war. Sie können den Zugriff auf einen Schlüssel auch mittels Key Vault-Richtlinien oder durch Aktualisieren des *enabled*-Schlüsselattributs mit **false** deaktivieren.

Weitere Informationen zu Datentypen finden Sie unter [Datentypen](#data-types).

Weitere Informationen zu anderen möglichen Attributen finden Sie unter [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Schlüsseltags

Sie können zusätzliche anwendungsspezifische Metadaten in Form von Tags angeben. Key Vault unterstützt bis zu 15 Tags, von denen jedes einen 256 Zeichen langen Namen und einen Wert von 256 Zeichen aufweisen kann.  

>[!Note]
>Tags sind für Aufrufer lesbar, die über die *list*- oder *get*-Berechtigung für diesen Objekttyp (Schlüssel, Geheimnisse oder Zertifikate) verfügen.

###  <a name="key-access-control"></a>Schlüsselzugriffssteuerung

Die Zugriffssteuerung für Schlüssel, die von Key Vault verwaltet werden, ist auf der Ebene eines Schlüsseltresors möglich, der als Schlüsselcontainer fungiert. Die Zugriffssteuerungsrichtlinie für Schlüssel unterscheidet sich von der Zugriffssteuerungsrichtlinie für Geheimnisse im selben Key Vault. Benutzer können einen oder mehrere Tresore zum Speichern von Schlüsseln erstellen und müssen für eine dem Szenario entsprechende Segmentierung und Verwaltung von Schlüsseln sorgen. Die Zugriffssteuerung für Schlüssel ist unabhängig von der Zugriffssteuerung für Geheimnisse.  

Die folgenden Berechtigungen können auf Benutzer-/Dienstprinzipalbasis im Schlüsselzugriffssteuerungs-Eintrag für einen Tresor erteilt werden. Diese Berechtigungen spiegeln präzise die für ein Schlüsselobjekt zulässigen Vorgänge wider:  

- Berechtigungen für Schlüsselverwaltungsvorgänge
  - *get*: Lesen des öffentlichen Teils eines Schlüssels sowie seiner Attribute
  - *list*: Auflisten der Schlüssel oder Versionen eines in einem Schlüsseltresor gespeicherten Schlüssels
  - *update*: Aktualisieren der Attribute für einen Schlüssel
  - *create*: Erstellen neuer Schlüssel
  - *import*: Importieren eines Schlüssels in einen Schlüsseltresor
  - *delete*: Löschen des Schlüsselobjekts
  - *recover*: Wiederherstellen eines gelöschten Schlüssels
  - *backup*: Sichern eines Schlüssels in einem Schlüsseltresor
  - *restore*: Wiederherstellen eines gesicherten Schlüssels in einem Schlüsseltresor

- Berechtigungen für kryptografische Vorgänge
  - *decrypt*: Verwenden des Schlüssels zum Aufheben des Schutzes einer Folge von Bytes
  - *encrypt*: Verwenden des Schlüssels zum Schützen einer beliebigen Folge von Bytes
  - *unwrapKey*: Verwenden des Schlüssels zum Aufheben des Schutzes eines umschlossenen symmetrischen Schlüssels
  - *wrapKey*: Verwenden des Schlüssels zum Schützen eines symmetrischen Schlüssels
  - *verify*: Verwenden des Schlüssels zum Überprüfen von Digests  
  - *sign*: Verwenden des Schlüssels zum Signieren von Digests
    
- Berechtigungen für privilegierte Vorgänge
  - *purge*: Bereinigen (dauerhaftes Löschen) eines gelöschten Schlüssels

Weitere Informationen zum Arbeiten mit Schlüsseln finden Sie in der [REST-API-Referenz für Key Vault](/rest/api/keyvault). Informationen zum Einrichten von Berechtigungen finden Sie unter [Tresore – Erstellen oder Aktualisieren](/rest/api/keyvault/vaults/createorupdate) und [Vaults – Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Key Vault-Geheimnisse 

### <a name="working-with-secrets"></a>Arbeiten mit Geheimnissen

Aus Entwicklerperspektive akzeptieren Key Vault-APIs geheime Werte als Zeichenfolge und geben sie auch in dieser Form zurück. Intern speichert und verwaltet Key Vault Geheimnisse als Oktettsequenzen (8-Bit-Bytes), wobei jede Sequenz maximal 25.000 Bytes groß sein darf. Der Key Vault-Dienst bietet keine Semantik für Geheimnisse. Der Dienst akzeptiert nur die Daten, verschlüsselt sie, speichert sie und gibt einen Bezeichner (ID) für das Geheimnis zurück. Der Bezeichner kann dazu verwendet werden, das Geheimnis zu einem späteren Zeitpunkt abzurufen.  

Bei hochvertraulichen Daten sollten zusätzliche Schutzebenen für die Daten eingerichtet werden. Die Verschlüsselung von Daten mithilfe eines separaten Schutzschlüssels vor dem Speichern in Key Vault ist nur ein Beispiel hierfür.  

Key Vault unterstützt auch ein contentType-Feld für Geheimnisse. Clients können den Inhaltstyp eines Geheimnisses als Hilfestellung beim Interpretieren der geheimen Daten beim Abrufen angeben. Dieses Feld darf maximal 255 Zeichen lang sein. Es gibt keine vordefinierten Werte. Die Verwendungsempfehlung dient als Hinweis für das Interpretieren der geheimen Daten. Wenn eine Implementierung z.B. sowohl Kennwörter als auch Zertifikate als Geheimnisse speichert, verwenden Sie dieses Feld zur Unterscheidung. Es gibt keine vordefinierten Werte.  

### <a name="secret-attributes"></a>Geheimnisattribute

Zusätzlich zu den Geheimnisdaten können die folgenden Attribute angegeben werden:  

- *exp*: IntDate, optional, Standardwert ist **forever**. Das *exp*-Attribut (Ablaufzeit) gibt die Ablaufzeit an, nach deren Verstreichen die geheimen Daten – außer in [bestimmten Situationen](#date-time-controlled-operations) – NICHT abgerufen werden sollen. Dieses Feld dient nur zu **Informationszwecken**, da es die Benutzer des Azure Key Vault-Diensts darüber informiert, dass ein bestimmter geheimer Schlüssel möglicherweise nicht verwendet werden kann. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.   
- *nbf*: IntDate, optional, Standardwert ist **now**. Das *nbf*-Attribut („not before“, nicht vor) gibt den Zeitpunkt an, vor dem die geheimen Daten – außer in [bestimmten Situationen](#date-time-controlled-operations) – NICHT abgerufen werden sollen. Dieses Feld dient nur zu **Informationszwecken**. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält. 
- *enabled*: Boolesch, optional, Standardwert ist **true**. Dieses Attribut gibt an, ob die Geheimnisdaten abgerufen werden können. Das enabled-Attribut wird in Verbindung mit *nbf* und *exp* verwendet. Wenn ein Vorgang zwischen *nbf* und *exp* auftritt, wird er nur zugelassen, wenn „enabled“ auf **true** festgelegt ist. Vorgänge außerhalb des *nbf*- und *exp*-Fensters sind automatisch unzulässig, [bestimmte Situationen](#date-time-controlled-operations) ausgenommen.  

Es gibt zusätzliche schreibgeschützte Attribute, die in alle Antworten einbezogen werden, die Geheimnisattribute enthalten:  

- *created*: IntDate, optional. Das „created“-Attribut gibt an, wann diese Version des Geheimnisses erstellt wurde. Dieser Wert ist NULL für Geheimnisse, die vor dem Hinzufügen dieses Attributs erstellt wurden. Der Wert muss eine Zahl sein, die einen IntDate-Wert enthält.  
- *updated*: IntDate, optional. Das updated-Attribut gibt an, wann diese Version des Geheimnisses aktualisiert wurde. Dieser Wert ist NULL für Geheimnisse, die vor dem Hinzufügen dieses Attributs zuletzt aktualisiert wurden. Der Wert muss eine Zahl sein, die einen IntDate-Wert enthält.

#### <a name="date-time-controlled-operations"></a>Durch Datum und Uhrzeit gesteuerte Vorgänge

Der **get**-Geheimnisvorgang funktioniert für noch nicht gültige und abgelaufene Geheimnisse außerhalb des *nbf* / *exp*-Fensters. Das Aufrufen eines **get**-Geheimnisvorgangs für ein noch nicht gültiges Geheimnis ist zu Testzwecken möglich. Abrufvorgänge (per **get**) für ein abgelaufenes Geheimnis können für Wiederherstellungsvorgänge verwendet werden.

Weitere Informationen zu Datentypen finden Sie unter [Datentypen](#data-types).  

### <a name="secret-access-control"></a>Geheimniszugriffssteuerung

Die Zugriffssteuerung für Geheimnisse, die in Key Vault verwaltet werden, wird auf der Ebene der Key Vault-Instanz bereitgestellt, die diese Geheimnisse enthält. Die Zugriffssteuerungsrichtlinie für Geheimnisse unterscheidet sich von der Zugriffssteuerungsrichtlinie für Schlüssel im selben Key Vault. Benutzer können einen oder mehrere Tresore zum Speichern von Geheimnissen erstellen und müssen für eine dem Szenario entsprechende Segmentierung und Verwaltung von Geheimnissen sorgen.   

Die folgenden Berechtigungen können pro Prinzipal im Zugriffssteuerungseintrag für Geheimnisse in einem Tresor verwendet werden und spiegeln die für ein Geheimnisobjekt zulässigen Vorgänge präzise wider:  

- Berechtigungen für Geheimnisverwaltungsvorgänge
  - *get*: Lesen eines Geheimnisses  
  - *list*: Auflisten der Geheimnisse oder Versionen eines in einem Schlüsseltresor gespeicherten Geheimnisses  
  - *set*: Erstellen eines Geheimnisses  
  - *delete*: Löschen eines Geheimnisses  
  - *recover*: Wiederherstellen eines gelöschten Geheimnisses
  - *backup*: Sichern eines Geheimnisses in einem Schlüsseltresor
  - *restore*: Wiederherstellen eines gesicherten Geheimnisses in einem Schlüsseltresor

- Berechtigungen für privilegierte Vorgänge
  - *purge*: Bereinigen (dauerhaftes Löschen) eines gelöschten Geheimnisses

Weitere Informationen zur Verwendung von Geheimnissen finden Sie unter den [Geheimnisvorgängen in der Referenz zur REST-API für Azure Key Vault](/rest/api/keyvault). Informationen zum Einrichten von Berechtigungen finden Sie unter [Tresore – Erstellen oder Aktualisieren](/rest/api/keyvault/vaults/createorupdate) und [Vaults – Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Geheimnistags  
Sie können zusätzliche anwendungsspezifische Metadaten in Form von Tags angeben. Key Vault unterstützt bis zu 15 Tags, von denen jedes einen 256 Zeichen langen Namen und einen Wert von 256 Zeichen aufweisen kann.  

>[!Note]
>Tags sind für Aufrufer lesbar, die über die *list*- oder *get*-Berechtigung für diesen Objekttyp (Schlüssel, Geheimnisse oder Zertifikate) verfügen.

## <a name="key-vault-certificates"></a>Key Vault-Zertifikate

Die Unterstützung von Key Vault-Zertifikaten ermöglicht die Verwaltung Ihrer x509-Zertifikate sowie folgende Szenarien:  

-   Ermöglicht einem Zertifikatbesitzer die Erstellung eines Zertifikats über einen Key Vault-Erstellungsvorgang oder durch den Import eines vorhandenen Zertifikats. Dies schließt sowohl selbstsignierte als auch von einer Zertifizierungsstelle generierte Zertifikate ein.
-   Ermöglicht dem Besitzer eines Key Vault-Zertifikats die Implementierung von sicherem Speicher sowie die Verwaltung von X509-Zertifikaten ohne Interaktion mit privaten Schlüsseln.  
-   Ermöglicht einem Zertifikatbesitzer die Erstellung einer Richtlinie, die Key Vault anweist, den Lebenszyklus eines Zertifikats zu verwalten.  
-   Ermöglicht Zertifikatbesitzern die Angabe von Kontaktinformationen zur Benachrichtigung über Lebenszyklusereignisse wie Ablauf und Verlängerung eines Zertifikats.  
-   Unterstützt die automatische Verlängerung mit dem ausgewählten Aussteller – Key Vault-Partneranbieter oder -Partnerzertifizierungsstellen für X509-Zertifikate.

>[!Note]
>Anbieter/Zertifizierungsstellen, mit denen keine Partnerschaft besteht, sind ebenfalls zulässig. Das Feature für die automatische Verlängerung wird von ihnen jedoch nicht unterstützt.

### <a name="composition-of-a-certificate"></a>Zusammensetzung eines Zertifikats

Wenn ein Key Vault-Zertifikat erstellt wird, werden auch ein adressierbarer Schlüssel und ein Geheimnis gleichen Namens erstellt. Der Key Vault-Schlüssel ermöglicht Schlüsselvorgänge, und das Key Vault-Geheimnis ermöglicht den Abruf des Zertifikatwerts als Geheimnis. Ein Key Vault-Zertifikat enthält auch öffentliche X509-Zertifikatmetadaten.  

Bezeichner und Version der Zertifikate entsprechen denen von Schlüsseln und Geheimnissen. Eine bestimmte Version eines mit der Key Vault-Zertifikatversion erstellten adressierbaren Schlüssels und Geheimnisses ist in der Antwort des Key Vault-Zertifikats verfügbar.
 
![Zertifikate sind komplexe Objekte](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Exportierbarer oder nicht exportierbarer Schlüssel

Wenn ein Key Vault-Zertifikat erstellt wird, kann es aus dem adressierbaren Geheimnis mit dem privaten Schlüssel entweder im PFX- oder PEM-Format abgerufen werden. Die zum Erstellen des Zertifikats verwendete Richtlinie muss angeben, dass der Schlüssel exportierbar ist. Wenn die Richtlinie angibt, dass der Schlüssel nicht exportierbar ist, ist der private Schlüssel beim Abruf als Geheimnis kein Teil des Werts.  

Der adressierbare Schlüssel erhält bei nicht exportierbaren Key Vault-Zertifikaten höhere Relevanz. Die Vorgänge des adressierbaren Key Vault-Schlüssels werden vom Feld *keyusage* der Key Vault-Zertifikatrichtlinie zugeordnet, mit der das Key Vault-Zertifikat erstellt wird.  

Zwei Typen des Schlüssels werden unterstützt – *RSA* oder *RSA-HSM* mit Zertifikaten. Die Exportierbarkeit ist nur mit RSA zulässig, sie wird nicht von RSA-HSM unterstützt.  

### <a name="certificate-attributes-and-tags"></a>Zertifikatattribute und Tags

Außer den Zertifikatmetadaten, einem adressierbaren Schlüssel und einem adressierbaren Geheimnis enthält ein Key Vault-Zertifikat auch Attribute und Tags.  

#### <a name="attributes"></a>Attribute

Die Zertifikatattribute werden in Attributen des adressierbaren Schlüssels und Geheimnisses gespiegelt, wenn ein Key Vault-Zertifikat erstellt wird.  

Ein Key Vault-Zertifikat weist folgende Attribute auf:  

-   *enabled*: Boolesch, optional, Standardwert ist **true**. Mit diesem Attribut kann angegeben werden, ob die Zertifikatdaten als Geheimnis oder als funktionsfähiger Schlüssel abgerufen werden können. Das Attribut wird auch in Verbindung mit *nbf* und *exp* verwendet, wenn ein Vorgang zwischen *nbf* und *exp* stattfindet. Der Vorgang wird nur zugelassen, wenn „enabled“ auf „true“ festgelegt ist. Vorgänge außerhalb des Fensters zwischen *nbf* und *exp* werden automatisch nicht zugelassen.  

Es gibt zusätzliche schreibgeschützte Attribute, die in die Antwort einbezogen werden:

-   *created*: IntDate: gibt an, wann diese Version des Zertifikats erstellt wurde  
-   *updated*: IntDate: gibt an, wann diese Version des Zertifikats aktualisiert wurde  
-   *exp*: IntDate: enthält den Wert des Ablaufdatums des X.509-Zertifikats  
-   *nbf*: IntDate: enthält den Wert des Datums des X.509-Zertifikats  

> [!Note] 
> Wenn ein Key Vault-Zertifikat abläuft, sind sein adressierbarer Schlüssel und sein Geheimnis nicht mehr funktionsfähig.  

#### <a name="tags"></a>`Tags`

 Vom Client angegebenes Wörterbuch von Schlüssel-Wert-Paaren, die Tags in Schlüsseln und Geheimnissen ähneln.  

 > [!Note]
> Tags sind für Aufrufer lesbar, die über die *list*- oder *get*-Berechtigung für diesen Objekttyp (Schlüssel, Geheimnisse oder Zertifikate) verfügen.

### <a name="certificate-policy"></a>Zertifikatrichtlinie

Eine Zertifikatrichtlinie enthält Informationen zum Erstellen und Verwalten des Lebenszyklus eines Key Vault-Zertifikats. Wenn ein Zertifikat mit privatem Schlüssel in den Schlüsseltresor importiert wird, wird durch Lesen des X509-Zertifikats eine Standardrichtlinie erstellt.  

Wenn ein Key Vault-Zertifikat von Grund auf neu erstellt wird, muss eine Richtlinie angegeben werden. Die Richtlinie gibt an, wie diese oder die nächste Key Vault-Zertifikatversion erstellt werden soll. Nachdem eine Richtlinie eingerichtet wurde, ist bei nachfolgenden Erstellungsvorgängen für zukünftige Versionen keine weitere Richtlinie erforderlich. Es gibt nur eine Instanz einer Richtlinie für alle Versionen eines Key Vault-Zertifikats.  

Auf hoher Ebene enthält eine Zertifikatsrichtlinie folgende Informationen:  

-   Eigenschaften des X.509-Zertifikats: Enthält den Namen und einen alternativen Namen des Antragstellers sowie weitere Eigenschaften zum Erstellen einer X.509-Zertifikatanforderung.  
-   Schlüsseleigenschaften: enthalten Schlüsseltyp, Schlüssellänge sowie Felder zur Exportierbarkeit und Wiederverwendung des Schlüssels. Diese Felder teilen Key Vault mit, wie ein Schlüssel generiert werden soll.  
-   Geheimniseigenschaften: enthält Geheimniseigenschaften wie den Inhaltstyp eines adressierbaren Geheimnisses zum Generieren des Geheimniswerts, um das Zertifikat als Geheimnis abzurufen.  
-   Lebensdaueraktionen: enthält Lebensdaueraktionen für das Key Vault-Zertifikat. Jede Lebensdaueraktion enthält:  

     - Trigger: wird über Tage vor Ablauf oder Lebensdauerprozentsatz angegeben  

     - Aktion: Angabe des Aktionstyps – *emailContacts* oder *autoRenew*  

-   Aussteller: Parameter über den zum Ausstellen von X.509-Zertifikaten zu verwendenden Aussteller des Zertifikats.  
-   Richtlinienattribute: enthält Attribute, die der Richtlinie zugeordnet sind  

#### <a name="x509-to-key-vault-usage-mapping"></a>Zuordnung von X509 zur Key Vault-Verwendung

In der folgenden Tabelle wird eine X509-Schlüsselverwendungsrichtlinie effektiven Schlüsselvorgängen eines Schlüssels zugeordnet, der im Rahmen einer Key Vault-Zertifikatserstellung erstellt wurde.

|**X509-Flags für Schlüsselverwendung**|**Key Vault-Schlüsseloptionen**|**Standardverhalten**|
|----------|--------|--------|
|DataEncipherment|encrypt, decrypt| – |
|DecipherOnly|Entschlüsseln| –  |
|DigitalSignature|sign, verify| Key Vault-Standard ohne Verwendungsspezifikation zum Zeitpunkt der Zertifikatserstellung | 
|EncipherOnly|Verschlüsseln| – |
|KeyCertSign|sign, verify|–|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault-Standard ohne Verwendungsspezifikation zum Zeitpunkt der Zertifikatserstellung | 
|NonRepudiation|sign, verify| – |
|crlsign|sign, verify| – |

### <a name="certificate-issuer"></a>Zertifikataussteller

Ein Key Vault-Zertifikatsobjekt enthält eine Konfiguration zur Kommunikation mit einem ausgewählten Zertifikatsausstelleranbieter über das Bestellen von X509-Zertifikaten.  

-   Key Vault-Partner mit den folgenden Zertifikatsausstelleranbietern für SSL-Zertifikate

|**Anbietername**|**Standorte**|
|----------|--------|
|DigiCert|Wird an allen Key Vault-Dienststandorten in öffentlicher Cloud und Azure Government unterstützt|
|GlobalSign|Wird an allen Key Vault-Dienststandorten in öffentlicher Cloud und Azure Government unterstützt|

Bevor ein Zertifikatsaussteller in einer Key Vault-Instanz erstellt werden kann, müssen die folgenden erforderlichen Schritte 1 und 2 erfolgreich durchgeführt werden.  

1. Integrieren in Anbieter von Zertifizierungsstellen (CA)  

    -   Ein Organisationsadministrator muss sein Unternehmen (z.B. Contoso) in mindestens einen CA-Anbieter integrieren.  

2. Der Administrator erstellt die Anmeldeinformationen der anfordernden Person, damit Key Vault SSL-Zertifikate registrieren (und erneuern) kann.  

    -   Stellt die Konfiguration zum Erstellen eines Ausstellerobjekts des Anbieters im Schlüsseltresor bereit  

Weitere Informationen zum Erstellen von Ausstellerobjekten im Zertifikateportal finden Sie im [Blog zu Key Vault-Zertifikaten](https://aka.ms/kvcertsblog).  

Key Vault ermöglicht die Erstellung mehrerer Ausstellerobjekte mit unterschiedlicher Ausstelleranbieterkonfiguration. Sobald ein Ausstellerobjekt erstellt ist, kann in einer oder mehreren Zertifikatrichtlinien auf seinen Namen verwiesen werden. Beim Verweis auf das Ausstellerobjekt wird Key Vault angewiesen, die Konfiguration gemäß den Angaben im Ausstellerobjekt zu verwenden, wenn das X509-Zertifikat bei Zertifikaterstellung und -verlängerung beim CA-Anbieter angefordert wird.  

Ausstellerobjekte werden im Tresor erstellt und können nur in demselben Tresor mit Key Vault-Zertifikaten verwendet werden.  

### <a name="certificate-contacts"></a>Zertifikatkontakte

Zertifikatkontakte enthalten Kontaktinformationen zum Senden von Benachrichtigungen, die durch Zertifikatlebensdauer-Ereignisse ausgelöst werden. Die Kontaktinformationen werden von allen Zertifikaten im Schlüsseltresor gemeinsam genutzt. Eine Benachrichtigung zu einem Ereignis eines beliebigen Zertifikats im Schlüsseltresor wird an alle angegebenen Kontakte gesendet.  

Wenn für die Richtlinie eines Zertifikats eine automatische Verlängerung festgelegt ist, wird bei den folgenden Ereignissen eine Benachrichtigung gesendet.  

- Vor der Zertifikatverlängerung
- Nach der Zertifikatverlängerung, um mitzuteilen, ob das Zertifikat erfolgreich verlängert wurde, oder ob ein Fehler aufgetreten ist, sodass die manuelle Zertifikatverlängerung erforderlich ist.  

  Wenn in der Richtlinie für ein Zertifikat die manuelle Verlängerung festgelegt ist (nur E-Mail), wird eine Benachrichtigung gesendet, sobald das Zertifikat verlängert werden muss.  

### <a name="certificate-access-control"></a>Zertifikatzugriffssteuerung

 Die Zugriffssteuerung für Zertifikate wird von Key Vault verwaltet und wird von der Key Vault-Instanz bereitgestellt, die diese Zertifikate enthält. Die Zugriffssteuerungsrichtlinie für Zertifikate unterscheidet sich von der Zugriffssteuerungsrichtlinie für Schlüssel und Geheimnisse im selben Key Vault. Benutzer können einen oder mehrere Tresore zum Speichern von Zertifikaten erstellen und müssen für eine dem Szenario entsprechende Segmentierung und Verwaltung von Zertifikaten sorgen.  

 Die folgenden Berechtigungen können pro Prinzipal im Zugriffssteuerungseintrag für Geheimnisse in einem Schlüsseltresor verwendet werden und spiegeln die für ein Geheimnisobjekt zulässigen Vorgänge präzise wider:  

- Berechtigungen für Zertifikatverwaltungsvorgänge
  - *get*: Abrufen der aktuellen oder einer beliebigen anderen Version eines Zertifikats 
  - *list*: Auflisten der aktuellen Zertifikate oder von Versionen eines Zertifikats  
  - *update*: Aktualisieren eines Zertifikats
  - *create*: Erstellen eines Key Vault-Zertifikats
  - *import*: Importieren von Zertifikatmaterial in ein Key Vault-Zertifikat
  - *delete*: Löschen eines Zertifikats, seiner Richtlinie und aller seiner Versionen  
  - *recover*: Wiederherstellen eines gelöschten Zertifikats
  - *backup*: Sichern eines Zertifikats in einem Schlüsseltresor
  - *restore*: Wiederherstellen eines gesicherten Zertifikats in einem Schlüsseltresor
  - *managecontacts*: Verwalten von Key Vault-Zertifikatkontakten  
  - *manageissuers*: Verwalten von Key Vault-Zertifikatautoritäten/-Zertifikatausstellern
  - *getissuers*: Abrufen der Autoritäten/Aussteller eines Zertifikats
  - *listissuers*: Auflisten der Autoritäten/Aussteller eines Zertifikats  
  - *setissuers*: Erstellen oder Aktualisieren der Autoritäten/Aussteller eines Key Vault-Zertifikats  
  - *deleteissuers*: Löschen der Autoritäten/Aussteller eines Key Vault-Zertifikats  
 
- Berechtigungen für privilegierte Vorgänge
  - *purge*: Bereinigen (dauerhaftes Löschen) eines gelöschten Zertifikats

Weitere Informationen finden Sie unter den [Zertifikatvorgängen in der Referenz zur REST-API für Azure Key Vault](/rest/api/keyvault). Informationen zum Einrichten von Berechtigungen finden Sie unter [Tresore – Erstellen oder Aktualisieren](/rest/api/keyvault/vaults/createorupdate) und [Vaults – Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Verwaltung eines Azure-Speicherkontoschlüssels

Key Vault kann Azure-Speicherkontoschlüssel verwalten:

- Intern kann Key Vault die Schlüssel eines Azure-Speicherkontos auflisten (synchronisieren). 
- Key Vault kann die Schlüssel in regelmäßigen Abständen erneut generieren (rotieren).
- Schlüsselwerte werden nie als Antwort an den Aufrufer zurückgegeben.
- Key Vault verwaltet Schlüssel von Speicherkonten und klassischen Speicherkonten.

Weitere Informationen finden Sie unter [Azure Key Vault-Speicherkontoschlüssel](key-vault-ovw-storage-keys.md).

### <a name="storage-account-access-control"></a>Speicherkonto-Zugriffssteuerung

Die folgenden Berechtigungen können beim Autorisieren eines Benutzer- oder Anwendungsprinzipals zur Ausführung von Vorgängen in einem verwalteten Speicherkonto verwendet werden:  

- Berechtigungen für Vorgänge in verwalteten Speicherkonten und SAS-Definitionen
  - *get*: Abrufen von Informationen zu einem Speicherkonto 
  - *list*: Auflisten der von einem Schlüsseltresor verwalteten Speicherkonten
  - *update*: Aktualisieren eines Speicherkontos
  - *delete*: Löschen von Speicherkonten  
  - *recover*: Wiederherstellen eines gelöschten Speicherkontos
  - *backup*: Sichern eines Speicherkontos
  - *restore*: Wiederherstellen eines gesicherten Speicherkontos in einem Schlüsseltresor
  - *set*: Erstellen oder Aktualisieren eines Speicherkontos
  - *regeneratekey*: Erneutes Generieren eines angegebenen Schlüsselwerts für ein Speicherkonto
  - *getsas*: Abrufen von Informationen über eine SAS-Definition für ein Speicherkonto
  - *listsas*: Auflisten der Speicher-SAS-Definitionen für ein Speicherkonto
  - *deletesas*: Löschen einer SAS-Definition aus einem Speicherkonto
  - *setsas*: Erstellen oder Aktualisieren einer neuen SAS-Definition bzw. von neuen SAS-Attributen für ein Speicherkonto

- Berechtigungen für privilegierte Vorgänge
  - *purge*: Bereinigen (dauerhaftes Löschen) eines verwalteten Speicherkontos

Weitere Informationen finden Sie in der [REST-API-Referenz für Key Vault](/rest/api/keyvault). Informationen zum Einrichten von Berechtigungen finden Sie unter [Tresore – Erstellen oder Aktualisieren](/rest/api/keyvault/vaults/createorupdate) und [Vaults – Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Siehe auch

- [Authentifizierung, Anforderungen und Antworten](authentication-requests-and-responses.md)
- [Key Vault-Versionen](key-vault-versions.md)
- [Entwicklerhandbuch für Key Vault](/azure/key-vault/key-vault-developers-guide)
