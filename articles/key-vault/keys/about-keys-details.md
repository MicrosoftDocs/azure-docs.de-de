---
title: 'Schlüsseltypen, Algorithmen und Vorgänge: Azure Key Vault'
description: Hier finden Sie Details zu unterstützten Schlüsseltypen, Algorithmen und Vorgängen.
services: key-vault
author: amitbapat
manager: msmbaldwin
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: ambapat
ms.openlocfilehash: 76eedaabf52cf2d56b2feaa6dc2748c25bf7696c
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423754"
---
# <a name="key-types-algorithms-and-operations"></a>Schlüsseltypen, Algorithmen und Vorgänge

Key Vault unterstützt zwei Ressourcentypen: Tresore und verwaltete HSMs. Beide Typen unterstützen verschiedene Verschlüsselungsschlüssel. Eine Übersicht über die unterstützten Schlüsseltypen sowie Schutztypen für den jeweiligen Ressourcentyp finden Sie unter [Informationen zu Schlüsseln](about-keys.md).

Die folgende Tabelle fasst die Schlüsseltypen und unterstützten Algorithmen zusammen.

|Schlüsseltypen/Größen/Kurven| Verschlüsseln/Entschlüsseln<br>(Umschließen/Aufheben der Umschließung) | Signieren/Überprüfen | 
| --- | --- | --- |
|EC-P256, EC-P256K, EC-P384, EC-521|Nicht verfügbar|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA-OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES (128 Bit, 256 Bit)| AES-KW<br>AES-GCM<br>AES-CBC| Nicht verfügbar| 
|||

##  <a name="ec-algorithms"></a>EC-Algorithmen
 Die folgenden Algorithmusbezeichner werden mit EC-HSM-Schlüsseln unterstützt:

### <a name="curve-types"></a>Kurventypen

-   **P-256**: Die NIST-Kurve P-256, definiert unter [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K**: Die SEC-Kurve SECP256K1, definiert unter [SEC 2: Recommended Elliptic Curve Domain Parameters (SEC 2: Empfohlene Domänenparameter für elliptische Kurven)](https://www.secg.org/sec2-v2.pdf).
-   **P-384**: Die NIST-Kurve P-384, definiert unter [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521**: Die NIST-Kurve P-521, definiert unter [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** – ECDSA für SHA-256-Hashs und -Schlüssel, erstellt mit P-256-Kurve. Dieser Algorithmus wird im [RFC 7518](https://tools.ietf.org/html/rfc7518) beschrieben.
-   **ES256K** – ECDSA für SHA-256-Hashs und -Schlüssel, erstellt mit P-256K-Kurve. Bei diesem Algorithmus steht die Standardisierung noch aus.
-   **ES384** – ECDSA für SHA-384-Hashs und -Schlüssel, erstellt mit P-384-Kurve. Dieser Algorithmus wird im [RFC 7518](https://tools.ietf.org/html/rfc7518) beschrieben.
-   **ES512** – ECDSA für SHA-512-Hashs und -Schlüssel, erstellt mit P-521-Kurve. Dieser Algorithmus wird im [RFC 7518](https://tools.ietf.org/html/rfc7518) beschrieben.

##  <a name="rsa-algorithms"></a>RSA-Algorithmen  
 Die folgenden Algorithmusbezeichner werden mit RSA- und RSA-HSM-Schlüsseln in unterstützt:  

### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5**: RSAES-PKCS1-V1_5 [RFC3447] Schlüsselverschlüsselung  
-   **RSA-OAEP**: RSAES unter Verwendung von Optimal Asymmetric Encryption Padding (OAEP) [RFC3447], wobei die Standardparameter durch RFC 3447 in Abschnitt A.2.1 angegeben werden. Diese Standardparameter verwenden eine Hashfunktion von SHA-1 und eine Maskengenerierungsfunktion von MGF1 mit SHA-1.  
-  **RSA-OAEP-256**: RSAES mit OAEP (Optimal Asymmetric Encryption Padding), einer Hashfunktion vom Typ SHA-256 und einer MGF1-Maskengenerierungsfunktion mit SHA-256

### <a name="signverify"></a>SIGN/VERIFY

-   **PS256**: RSASSA-PSS unter Verwendung von SHA-256 und MGF1 mit SHA-256, wie in [RFC7518](https://tools.ietf.org/html/rfc7518) beschrieben.
-   **PS384**: RSASSA-PSS unter Verwendung von SHA-384 und MGF1 mit SHA-384, wie in [RFC7518](https://tools.ietf.org/html/rfc7518) beschrieben.
-   **PS512**: RSASSA-PSS unter Verwendung von SHA-512 und MGF1 mit SHA-512, wie in [RFC7518](https://tools.ietf.org/html/rfc7518) beschrieben.
-   **RS256**: RSASSA-PKCS-v1_5 mithilfe von SHA-256. Der von der Anwendung bereitgestellte Zusammenfassungswert muss mithilfe von SHA-256 berechnet werden und 32 Byte lang sein.  
-   **RS384**: RSASSA-PKCS-v1_5 mithilfe von SHA-384. Der von der Anwendung bereitgestellte Zusammenfassungswert muss mithilfe von SHA-384 berechnet werden und 48 Byte lang sein.  
-   **RS512**: RSASSA-PKCS-v1_5 mithilfe von SHA-512. Der von der Anwendung bereitgestellte Zusammenfassungswert muss mithilfe von SHA-512 berechnet werden und 64 Byte lang sein.  
-   **RSNULL**: Siehe [RFC2437](https://tools.ietf.org/html/rfc2437). Hierbei handelt es sich um einen speziellen Anwendungsfall für bestimmte TLS-Szenarien.  

##  <a name="symmetric-key-algorithms"></a>Algorithmen für symmetrische Schlüssel
- **AES-KW**: AES-Schlüsselverpackung ([RFC3394](https://tools.ietf.org/html/rfc3394))
- **AES-GCM**: AES-Verschlüsselung im Galois-Zählermodus ([NIST SP800-38d](https://csrc.nist.gov/publications/sp800))
- **AES-CBC**: AES-Verschlüsselung im Verschlüsselungsblockverkettungs-Modus ([NIST SP800-38d](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> Die aktuelle AES-GCM-Implementierung und die entsprechenden APIs sind experimentell. Die Implementierung und die APIs können sich in zukünftigen Iterationen noch erheblich ändern. 

##  <a name="key-operations"></a>Schlüsselvorgänge

Verwaltete HSMs unterstützen die folgenden Vorgänge für Schlüsselobjekte:  

-   **Erstellen**: Ermöglicht einem Client, einen Schlüssel in Key Vault zu erstellen. Der Wert des Schlüssels wird von Key Vault generiert und gespeichert und nicht für den Client freigegeben. In Key Vault können asymmetrische Schlüssel erstellt werden.  
-   **Import**: Ermöglicht einem Client, einen vorhandenen Schlüssel in Key Vault zu importieren. Asymmetrische Schlüssel können mithilfe einer Reihe unterschiedlicher Paketerstellungsmethoden in einem JWK-Konstrukt in Key Vault importiert werden. 
-   **Update** (Aktualisieren): Ermöglicht einem Client mit ausreichenden Berechtigungen, die Metadaten (Schlüsselattribute) zu ändern, die einem zuvor in Key Vault gespeicherten Schlüssel zugeordnet sind.  
-   **Löschen:** Ermöglicht einem Client mit ausreichenden Berechtigungen das Löschen eines Schlüssels aus Key Vault.  
-   **List** (Auflisten): Ermöglicht einem Client das Auflisten aller Schlüssel in einem bestimmten Schlüsseltresor.  
-   **List versions** (Versionen auflisten): Ermöglicht einem Client das Auflisten aller Versionen eines bestimmten Schlüssels in einem bestimmten Key Vault.  
-   **Get** (Abrufen): Ermöglicht einem Client das Abrufen des öffentlichen Teils eines bestimmten Schlüssels in einem Schlüsseltresor.  
-   **Backup** (Sichern): Exportiert einen Schlüssel in einer geschützten Form.  
-   **Restore** (Wiederherstellen): Importiert einen zuvor gesicherten Schlüssel.  

Weitere Informationen finden Sie unter [Schlüsselvorgänge in der Referenz zur REST-API für Azure Key Vault](/rest/api/keyvault).  

Nachdem ein Schlüssel in Key Vault erstellt wurde, können die folgenden kryptografischen Vorgänge mit dem Schlüssel ausgeführt werden:  

-   **Sign an Verify** (Signieren und überprüfen): Streng genommen handelt es sich hierbei um den Vorgang „sign hash“ (Hash signieren) oder „verify hash“ (Hash überprüfen), da Key Vault das Hashing von Inhalten im Rahmen der Signaturerstellung nicht unterstützt. Anwendungen sollten ein Hashing der Daten durchführen, die lokal signiert werden sollen, und dann anfordern, dass Key Vault den Hash signiert. Die Überprüfung signierter Hashes wird aus Gründen der Vereinfachung für Anwendungen unterstützt, die möglicherweise keinen Zugriff auf (öffentliches) Schlüsselmaterial haben. Um eine optimale Anwendungsleistung zu erzielen, sollten VERIFY-Vorgänge lokal ausgeführt werden.  
-   **Key Encryption / Wrapping** (Verschlüsselung mit Schlüssel / Umschließen): Ein in Key Vault gespeicherter Schlüssel kann zum Schutz eines anderen Schlüssels verwendet werden. In der Regel handelt es sich dabei um einen symmetrischen Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK). Wenn der Schlüssel in Key Vault asymmetrisch ist, wird die Schlüsselverschlüsselung verwendet. RSA-OAEP und die WRAPKEY/UNWRAPKEY-Vorgänge sind beispielsweise äquivalent zu ENCRYPT/DECRYPT. Wenn der Schlüssel in Key Vault symmetrisch ist, wird die Schlüsselumschließung verwendet. Beispiel: AES-KW. Der WRAPKEY-Vorgang wird aus Gründen der Vereinfachung für Anwendungen unterstützt, die möglicherweise keinen Zugriff auf (öffentliches) Schlüsselmaterial haben. Um eine optimale Anwendungsleistung zu erzielen, sollten WRAPKEY-Vorgänge lokal ausgeführt werden.  
-   **Encrypt and Decrypt** (Verschlüsseln und Entschlüsseln): Ein in Key Vault gespeicherter Schlüssel kann zum Verschlüsseln oder Entschlüsseln eines einzelnen Datenblocks verwendet werden. Die Größe des Blocks wird durch den Schlüsseltyp und den ausgewählten Verschlüsselungsalgorithmus bestimmt. Der ENCRYPT-Vorgang wird aus Gründen der Vereinfachung für Anwendungen unterstützt, die möglicherweise keinen Zugriff auf (öffentliches) Schlüsselmaterial haben. Um eine optimale Anwendungsleistung zu erzielen, sollten ENCRYPT-Vorgänge lokal ausgeführt werden.  

Obwohl WRAPKEY/UNWRAPKEY mit asymmetrischen Schlüsseln überflüssig erscheinen mögen (da die Vorgänge äquivalent zu ENCRYPT/DECRYPT sind), ist die Verwendung unterschiedlicher Vorgänge wichtig. Die Unterscheidung sorgt für eine semantische und autorisierungsbezogene Trennung dieser Vorgänge sowie für Konsistenz, wenn andere Schlüsseltypen vom Dienst unterstützt werden.  

Key Vault unterstützt keine EXPORT-Vorgänge. Sobald ein Schlüssel im System bereitgestellt ist, kann er weder extrahiert noch sein Schlüsselmaterial geändert werden. Möglicherweise benötigen Key Vault-Benutzer ihren Schlüssel jedoch für andere Anwendungsfälle, beispielsweise nachdem er gelöscht wurde. In diesem Fall können die Vorgänge BACKUP und RESTORE verwendet werden, um den Schlüssel in geschützter Form zu exportieren und zu importieren. Vom BACKUP-Vorgang erstellte Schlüssel können außerhalb von Key Vault nicht verwendet werden. Alternativ dazu kann der IMPORT-Vorgang für mehrere Key Vault-Instanzen verwendet werden.  

Benutzer können die kryptografischen Vorgänge, die Key Vault pro Schlüssel unterstützt, mithilfe der key_ops-Eigenschaft des JWK-Objekts einschränken.  

Weitere Informationen zu JWK-Objekten finden Sie unter [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Schlüsselattribute

Zusätzlich zum Schlüsselmaterial können die folgenden Attribute angegeben werden. In einer JSON-Anforderung sind das Schlüsselwort „attributes“ und die Klammern „{“ „}“ auch dann erforderlich, wenn keine Attribute angegeben werden.  

- *enabled*: Boolesch, optional, Standardwert ist **true**. Gibt an, ob der Schlüssel aktiviert und für kryptografische Vorgänge verwendbar ist. Das Attribut *enabled* wird in Verbindung mit *nbf* und *exp* verwendet. Wenn ein Vorgang zwischen *nbf* und *exp* auftritt, wird er nur zugelassen, wenn *enabled* auf **true** gesetzt ist. Vorgänge außerhalb des *nbf* / *exp*-Fensters sind automatisch nicht zugelassen, mit Ausnahme bestimmter Vorgangstypen unter [besonderen Bedingungen](#date-time-controlled-operations).
- *nbf*: IntDate, optional, Standardwert ist „now“. Das Attribut *nbf* („not before“, nicht vor) gibt den Zeitpunkt an, vor dem der Schlüssel NICHT für kryptografische Vorgänge verwendet werden darf, mit Ausnahme bestimmter Vorgangstypen unter [besonderen Bedingungen](#date-time-controlled-operations). Die Verarbeitung des *nbf*-Attributs erfordert, dass das aktuelle Datum / die aktuelle Uhrzeit mindestens dem Wert des *nbf*-Attributs entsprechen MUSS. Key Vault KANN einen kleinen Spielraum von in der Regel nicht mehr als einigen wenigen Minuten einräumen, um Abweichungen der Uhr zu berücksichtigen. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.  
- *exp*: IntDate, optional, Standardwert ist „forever“. Das Attribut *exp* („expiration time“, Ablaufzeit) gibt die Ablaufzeit an, nach deren Verstreichen der Schlüssel NICHT für kryptografische Vorgänge verwendet werden darf, mit Ausnahme bestimmter Vorgangstypen unter [besonderen Bedingungen](#date-time-controlled-operations). Die Verarbeitung des *exp*-Attributs erfordert, dass das aktuelle Datum / die aktuelle Uhrzeit vor dem Wert des *exp*-Attributs liegen MUSS. Key Vault KANN einen kleinen Spielraum von in der Regel nicht mehr als einigen wenigen Minuten einräumen, um Abweichungen der Uhr zu berücksichtigen. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.  

Es gibt zusätzliche schreibgeschützte Attribute, die in alle Antworten einbezogen werden, die Schlüsselattribute enthalten:  

- *created*: IntDate, optional. Das *created*-Attribut gibt an, wann diese Version des Schlüssels erstellt wurde. Der Wert ist NULL für Schlüssel, die vor dem Hinzufügen dieses Attributs erstellt wurden. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.  
- *updated*: IntDate, optional. Das *updated*-Attribut gibt an, wann diese Version des Schlüssels aktualisiert wurde. Der Wert ist NULL für Schlüssel, die vor dem Hinzufügen dieses Attributs zuletzt aktualisiert wurden. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.  

Weitere Informationen zu IntDate und anderen Datentypen finden Sie unter „Informationen zu Schlüsseln, Geheimnissen und Zertifikaten“ im Abschnitt [Datentypen](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Durch Datum und Uhrzeit gesteuerte Vorgänge

Noch nicht gültige und abgelaufene Schlüssel, die außerhalb des *nbf* / *exp*-Fensters liegen, sind in **decrypt**-, **unwrap**- und **verify**-Vorgängen verwendbar (keine Rückgabe von „403 – verboten“). Grund für die Verwendung des „Noch-nicht-gültig“-Status ist, den Test eines Schlüssels vor der Verwendung in der Produktion zu ermöglichen. Grund für die Verwendung des abgelaufenen Zustands ist, Wiederherstellungsvorgänge bei Daten zu ermöglichen, die erstellt wurden, als der Schlüssel gültig war. Sie können den Zugriff auf einen Schlüssel auch mittels Key Vault-Richtlinien oder durch Aktualisieren des *enabled*-Schlüsselattributs mit **false** deaktivieren.

Weitere Informationen zu Datentypen finden Sie unter [Datentypen](../general/about-keys-secrets-certificates.md#data-types).

Weitere Informationen zu anderen möglichen Attributen finden Sie unter [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Schlüsseltags

Sie können zusätzliche anwendungsspezifische Metadaten in Form von Tags angeben. Key Vault unterstützt bis zu 15 Tags, von denen jedes einen 256 Zeichen langen Namen und einen Wert von 256 Zeichen aufweisen kann.  

> [!NOTE] 
> Tags sind für Aufrufer lesbar, die über die Berechtigung *list* oder *get* für den Schlüssel verfügen.

##  <a name="key-access-control"></a>Schlüsselzugriffssteuerung

Die Zugriffssteuerung für Schlüssel, die von Key Vault verwaltet werden, ist auf der Ebene eines Schlüsseltresors möglich, der als Schlüsselcontainer fungiert. Die Zugriffssteuerungsrichtlinie für Schlüssel unterscheidet sich von der Zugriffssteuerungsrichtlinie für Geheimnisse im selben Key Vault. Benutzer können einen oder mehrere Tresore zum Speichern von Schlüsseln erstellen und müssen für eine dem Szenario entsprechende Segmentierung und Verwaltung von Schlüsseln sorgen. Die Zugriffssteuerung für Schlüssel ist unabhängig von der Zugriffssteuerung für Geheimnisse.  

Die folgenden Berechtigungen können auf Benutzer-/Dienstprinzipalbasis im Schlüsselzugriffssteuerungs-Eintrag für einen Tresor erteilt werden. Diese Berechtigungen spiegeln die für ein Schlüsselobjekt zulässigen Vorgänge wider.  Das Gewähren des Zugriffs auf einen Dienstprinzipal im Schlüsseltresor ist ein einmaliger Vorgang und bleibt für alle Azure-Abonnements gleich. Sie können damit beliebig viele Zertifikate bereitstellen. 

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

## <a name="next-steps"></a>Nächste Schritte
- [Informationen zu Key Vault](../general/overview.md)
- [Informationen zu verwaltetem HSM](../managed-hsm/overview.md)
- [Informationen zu Geheimnissen](../secrets/about-secrets.md)
- [Informationen zu Zertifikaten](../certificates/about-certificates.md)
- [Übersicht über die Key Vault-REST-API](../general/about-keys-secrets-certificates.md)
- [Authentifizierung, Anforderungen und Antworten](../general/authentication-requests-and-responses.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
