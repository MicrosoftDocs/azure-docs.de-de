---
title: Kryptografie – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über kryptografiebasierte Gegenmaßnahmen für durch das Threat Modeling Tool offengelegte Bedrohungen. Lesen Sie die Informationen zur Risikominderung, und sehen Sie sich die Codebeispiele an.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 7af115d8f1244253e461f796c5665609d3b84b21
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517443"
---
# <a name="security-frame-cryptography--mitigations"></a>Sicherheitsrahmen: Kryptografie | Gegenmaßnahmen 

| Produkt/Dienst | Artikel |
| --------------- | ------- |
| **Web Application** | <ul><li>[Ausschließliches Verwenden von genehmigten symmetrischen Blockchiffren und Schlüssellängen](#cipher-length)</li><li>[Verwenden von genehmigten Blockchiffremodi und Initialisierungsvektoren für symmetrische Chiffren](#vector-ciphers)</li><li>[Verwenden von genehmigten asymmetrischen Algorithmen, Schlüssellängen und Auffüllungen](#padding)</li><li>[Verwenden von genehmigten Zufallszahlengeneratoren](#numgen)</li><li>[Vermeiden der Verwendung von symmetrischen Streamchiffren](#stream-ciphers)</li><li>[Verwenden von genehmigten MAC-, HMAC- und schlüsselgebundenen Hashalgorithmen](#mac-hash)</li><li>[Ausschließliches Verwenden von genehmigten kryptografischen Hashfunktionen](#hash-functions)</li></ul> |
| **Datenbank** | <ul><li>[Verwenden von sicheren Verschlüsselungsalgorithmen zum Verschlüsseln von Daten in der Datenbank](#strong-db)</li><li>[SSIS-Pakete sollten verschlüsselt und digital signiert werden](#ssis-signed)</li><li>[Hinzufügen einer digitalen Signatur zu kritischen sicherungsfähigen Elementen von Datenbanken](#securables-db)</li><li>[Verwenden der erweiterbaren Schlüsselverwaltung von SQL Server zum Schützen von Verschlüsselungsschlüsseln](#ekm-keys)</li><li>[Verwenden des Features AlwaysEncrypted, wenn Verschlüsselungsschlüssel für die Datenbank-Engine nicht offengelegt werden sollen](#keys-engine)</li></ul> |
| **IoT-Gerät** | <ul><li>[Sicheres Speichern von kryptografischen Schlüsseln auf dem IoT-Gerät](#keys-iot)</li></ul> | 
| **IoT-Cloudgateway** | <ul><li>[Generieren eines zufälligen symmetrischen Schlüssels mit einer ausreichenden Länge für die Authentifizierung gegenüber IoT Hub](#random-hub)</li></ul> | 
| **Dynamics CRM – mobiler Client** | <ul><li>[Sicherstellen der Verwendung einer Richtlinie für die Geräteverwaltung, die für die Nutzung eine PIN erforderlich macht und das Remotelöschen ermöglicht](#pin-remote)</li></ul> | 
| **Dynamics CRM – Outlook-Client** | <ul><li>[Sicherstellen der Verwendung einer Richtlinie für die Geräteverwaltung, die PIN/Kennwort/automatische Sperre erforderlich macht und alle Daten verschlüsselt (z.B. BitLocker)](#bitlocker)</li></ul> | 
| **Identity Server** | <ul><li>[Sicherstellen, dass für Signaturschlüssel bei Verwendung von Identity Server ein Rollover durchgeführt wird](#rolled-server)</li><li>[Sicherstellen, dass in Identity Server eine in kryptografischer Hinsicht sichere Client-ID und ein sicheres Clientgeheimnis verwendet werden](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Ausschließliches Verwenden von genehmigten symmetrischen Blockchiffren und Schlüssellängen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Für Produkte dürfen nur die symmetrischen Blockchiffren und zugeordneten Schlüssellängen verwendet werden, die vom Crypto Advisor (Kryptografiebeauftragter) Ihrer Organisation ausdrücklich genehmigt wurden. Zu den genehmigten symmetrischen Algorithmen bei Microsoft gehören die folgenden Blockchiffren:</p><ul><li>Für neuen Code sind AES-128, AES-192 und AES-256 akzeptabel.</li><li>In Bezug auf die Abwärtskompatibilität mit vorhandenem Code ist 3DES mit drei Schlüsseln akzeptabel.</li><li>Für Produkte mit symmetrischen Blockchiffren:<ul><li>Für neuen Code ist Advanced Encryption Standard (AES) erforderlich.</li><li>3DES (triple Data Encryption Standard) mit drei Schlüsseln ist in vorhandenem Code für die Abwärtskompatibilität zulässig.</li><li>Alle anderen Blockchiffren, z.B. RC2, DES, 3DES mit zwei Schlüsseln, DESX und Skipjack, dürfen nur zum Entschlüsseln von alten Daten verwendet und müssen nach der Verwendung für die Verschlüsselung ausgetauscht werden.</li></ul></li><li>Für Algorithmen zur Verschlüsselung symmetrischer Blöcke ist für Schlüssel eine Mindestlänge von 128 Bit erforderlich. Der einzige Blockverschlüsselungsalgorithmus, der für neuen Code empfohlen wird, ist AES (AES-128, AES-192 und AES-256 sind akzeptabel).</li><li>3DES mit drei Schlüsseln ist derzeit akzeptabel, wenn dieses Verfahren im vorhandenen Code bereits verwendet wird. Die Umstellung auf AES wird aber empfohlen. DES, DESX, RC2 und Skipjack werden nicht mehr als sicher angesehen. Diese Algorithmen dürfen nur aus Gründen der Abwärtskompatibilität zum Entschlüsseln von vorhandenen Daten verwendet werden, und Daten sollten mit einem empfohlenen Blockchiffre neu verschlüsselt werden.</li></ul><p>Beachten Sie, dass alle symmetrischen Blockchiffren zusammen mit einem genehmigten Chiffremodus verwendet werden müssen. Hierfür muss ein geeigneter Initialisierungsvektor (IV) genutzt werden. Bei einem geeigneten IV handelt es sich normalerweise um eine Zufallszahl und niemals um einen konstanten Wert.</p><p>Die Verwendung von älteren oder in anderer Hinsicht nicht genehmigten Kryptografiealgorithmen und geringeren Schlüssellängen zum Lesen von vorhandenen Daten (im Gegensatz zum Schreiben neuer Daten) kann nach einer Überprüfung durch das „Crypto Board“ Ihrer Organisation erlaubt werden. Es muss aber eine Ausnahme dieser Anforderung beantragt werden. Bei Bereitstellungen in Unternehmen sollte für Produkte erwägt werden, dass Administratoren bei Verwendung von unsicherer Kryptografie zum Lesen von Daten gewarnt werden. Diese Warnungen sollten eine gute Beschreibung und verwertbare Informationen enthalten. In einigen Fällen kann es hilfreich sein, die Nutzung von unsicherer Kryptografie per Gruppenrichtlinie zu steuern.</p><p>Zulässige .NET-Algorithmen für verwaltete kryptografische Flexibilität (sortiert nach Präferenz)</p><ul><li>AesCng (FIPS-konform)</li><li>AuthenticatedAesCng (FIPS-konform)</li><li>AESCryptoServiceProvider (FIPS-konform)</li><li>AESManaged (nicht FIPS-konform)</li></ul><p>Beachten Sie, dass keiner dieser Algorithmen mit der Methode `SymmetricAlgorithm.Create` oder `CryptoConfig.CreateFromName` angegeben werden kann, ohne Änderungen an der Datei „machine.config“ vorzunehmen. Beachten Sie auch, dass AES in älteren .NET-Versionen als .NET 3.5 die Bezeichnung `RijndaelManaged` hat und dass `AesCng` und `AuthenticatedAesCng` per CodePlex verfügbar sind und CNG im zugrunde liegenden Betriebssystem erforderlich ist.</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Verwenden von genehmigten Blockchiffremodi und Initialisierungsvektoren für symmetrische Chiffren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Alle symmetrischen Blockchiffren müssen zusammen mit einem genehmigten Modus für symmetrische Chiffren verwendet werden. Die einzigen genehmigten Modi sind CBC und CTS. Vor allem der Vorgangsmodus ECB (Electronic Code Book) sollte vermieden werden. Für die Verwendung von ECB benötigen Sie die Zustimmung des Crypto Board Ihrer Organisation. Jedwede Nutzung von OFB, CFB, CTR, CCM und GCM oder eines beliebigen anderen Verschlüsselungsmodus muss vom Crypto Board Ihrer Organisation geprüft werden. Die Wiederverwendung des gleichen Initialisierungsvektors (IV) mit Blockchiffren in „Streamingchiffremodi“, z.B. CTR, kann dazu führen, dass verschlüsselte Daten offengelegt werden. Außerdem müssen alle symmetrischen Blockchiffren zusammen mit einem geeigneten Initialisierungsvektor (IV) verwendet werden. Ein geeigneter IV ist eine in kryptografischer Hinsicht sichere Zufallszahl und niemals ein konstanter Wert. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Verwenden von genehmigten asymmetrischen Algorithmen, Schlüssellängen und Auffüllungen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Die Verwendung von unzulässigen kryptografischen Algorithmen stellt ein erhebliches Risiko für die Produktsicherheit dar und muss vermieden werden. Für Produkte dürfen nur kryptografische Algorithmen und die dazugehörigen Schlüssellängen und Auffüllungen verwendet werden, die vom Crypto Board Ihrer Organisation ausdrücklich genehmigt wurden.</p><ul><li>**RSA-** kann für die Verschlüsselung, den Schlüsselaustausch und Signaturen verwendet werden. Für die RSA-Verschlüsselung dürfen nur die Auffüllmodi OAEP oder RSA-KEM verwendet werden. Für vorhandenen Code darf der Auffüllmodus PKCS #1 v1.5 ausschließlich zu Kompatibilitätszwecken genutzt werden. Die Verwendung der Nullauffüllung ist explizit untersagt. Für neuen Code sind Schlüssel mit mindestens 2.048 Bit erforderlich. Für vorhandenen Code dürfen Schlüssel mit weniger als 2.048 Bit nur aus Gründen der Abwärtskompatibilität unterstützt werden, nachdem dies vom Crypto Board Ihrer Organisation geprüft wurde. Schlüssel mit weniger als 1.024 Bit dürfen nur zum Entschlüsseln bzw. Überprüfen von alten Daten verwendet werden und müssen ausgetauscht werden, wenn sie für Verschlüsselungs- oder Signaturvorgänge verwendet werden.</li><li>**ECDSA-** darf nur für Signaturen verwendet werden. Für neuen Code ist ECDSA mit Schlüsseln mit mindestens 256 Bit erforderlich. Für ECDSA-basierte Signaturen muss eine der drei Kurven mit NIST-Genehmigung genutzt werden (P-256, P-384 oder P521). Kurven, die gründlich analysiert wurden, dürfen nur nach einer Prüfung durch das Crypto Board Ihrer Organisation verwendet werden.</li><li>**ECDH-** darf nur für den Schlüsselaustausch verwendet werden. Für neuen Code ist ECDH mit Schlüsseln mit mindestens 256 Bit erforderlich. Für den ECDH-basierten Schlüsselaustausch muss eine der drei Kurven mit NIST-Genehmigung genutzt werden (P-256, P-384 oder P521). Kurven, die gründlich analysiert wurden, dürfen nur nach einer Prüfung durch das Crypto Board Ihrer Organisation verwendet werden.</li><li>**DSA-** ist unter Umständen akzeptabel, nachdem dieses Verfahren vom Crypto Board Ihrer Organisation geprüft und genehmigt wurde. Wenden Sie sich an Ihren Sicherheitsberater, um einen Termin für die Überprüfung durch das Crypto Board Ihrer Organisation auszumachen. Beachten Sie Folgendes, falls die Verwendung von DSA genehmigt wird: Die Nutzung von Schlüsseln mit einer Schlüssellänge von weniger als 2.048 Bit muss untersagt werden. CNG unterstützt ab Windows 8 Schlüssellängen von 2.048 Bit und mehr.</li><li>**Diffie-Hellman-** darf nur für die Verwaltung von Sitzungsschlüsseln verwendet werden. Für neuen Code sind Schlüssellängen von mindestens 2.048 Bit erforderlich. Für vorhandenen Code dürfen Schlüssellängen von weniger als 2.048 Bit nur aus Gründen der Abwärtskompatibilität unterstützt werden, nachdem dies vom Crypto Board Ihrer Organisation geprüft wurde. Schlüssel mit weniger als 1.024 Bit dürfen nicht verwendet werden.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Verwenden von genehmigten Zufallszahlengeneratoren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Für Produkte müssen genehmigte Zufallszahlengeneratoren verwendet werden. Pseudozufallsfunktionen, z.B. die C-Runtimefunktion „rand“, die .NET Framework-Klasse „System.Random“ oder Systemfunktionen wie „GetTickCount“, dürfen in Code dieser Art daher niemals genutzt werden. Die Verwendung des DUAL_EC_DRBG-Algorithmus (Dual Elliptic Curve Random Number Generator) ist untersagt.</p><ul><li>**CNG-** BCryptGenRandom (Die Verwendung des Flags BCRYPT_USE_SYSTEM_PREFERRED_RNG wird empfohlen, sofern der Aufrufer für die Ausführung nicht einen IRQL-Wert größer als Null verwendet [PASSIVE_LEVEL].)</li><li>**CAPI-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (für neue Implementierungen sollte BCryptGenRandom oder CryptGenRandom verwendet werden) * rand_s * SystemPrng (für Kernelmodus)</li><li>**.NET-** RNGCryptoServiceProvider oder RNGCng</li><li>**Windows Store Apps-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom oder .GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+)-** int SecRandomCopyBytes (SecRandomRef rnd, size_t count, uint8_t \*Byte)</li><li>**Apple OS X (<10.7)-** Verwenden Sie „/dev/random“ zum Abrufen von Zufallszahlen.</li><li>**Java (einschließlich Google Android Java-Code)-** java.security.SecureRandom-Klasse. Beachten Sie, dass Entwickler für Android 4.3 (Jelly Bean) die für Android empfohlene Problemumgehung verwenden und ihre Anwendungen aktualisieren müssen, um PRNG mit Entropie über „/dev/urandom“ oder „/dev/random“ explizit zu initialisieren.</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Vermeiden der Verwendung von symmetrischen Streamchiffren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Symmetrische Streamchiffren, z.B. RC4, dürfen nicht verwendet werden. Anstelle von symmetrischen Streamchiffren sollte für Produkte ein Blockchiffre verwendet werden (AES mit einer Schlüssellänge von mindestens 128 Bit). |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Verwenden von genehmigten MAC-, HMAC- und schlüsselgebundenen Hashalgorithmen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Für Produkte dürfen nur genehmigte Algorithmen vom Typ MAC (Message Authentication Code) oder HMAC (Hash-Based Message Authentication Code) verwendet werden.</p><p>Ein Nachrichtenauthentifizierungscode (Message Authentication Code, MAC) ist ein Informationselement, das an eine Nachricht angefügt ist. Hiermit kann der Empfänger sowohl die Echtheit des Absenders als auch die Integrität der Nachricht anhand eines geheimen Schlüssels überprüfen. Die Verwendung von hashbasiertem MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) oder [auf Blockchiffren basierendem MAC](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) ist zulässig, sofern auch alle zugrunde liegenden Hash- oder symmetrischen Verschlüsselungsalgorithmen für die Verwendung genehmigt sind. Dies gilt derzeit für die HMAC-SHA2-Funktionen (HMAC-SHA256, HMAC-SHA384 und HMAC-SHA512) und die auf Blockchiffren basierenden MACs CMAC/OMAC1 und OMAC2 (auf AES-Basis).</p><p>Die Verwendung von HMAC-SHA1 kann aus Gründen der Plattformkompatibilität zulässig sein, aber Sie müssen eine Ausnahme von diesem Verfahren beantragen und den Prozess zur Kryptografieüberprüfung Ihrer Organisation durchlaufen. Eine Kürzung von HMACs auf weniger als 128 Bit ist nicht zulässig. Die Verwendung von Kundenmethoden zum Hashen eines Schlüssels und von Daten ist nicht genehmigt, und vor der Verwendung muss eine Prüfung durch das Crypto Board Ihrer Organisation erfolgen.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Ausschließliches Verwenden von genehmigten kryptografischen Hashfunktionen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Für die Produkte muss die SHA-2-Familie der Hashalgorithmen (SHA256, SHA384 und SHA512) verwendet werden. Wenn ein kürzerer Hash erforderlich ist, z.B. eine Ausgabelänge von 128 Bit für eine Datenstruktur mit dem kürzeren MD5-Hash, ist es für Produktteams zulässig, einen der SHA2-Hashes (normalerweise SHA256) zu kürzen. Beachten Sie, dass SHA384 eine gekürzte Version von SHA512 ist. Das Kürzen von kryptografischen Hashes auf weniger als 128 Bit aus Sicherheitsgründen ist nicht zulässig. Für neuen Code dürfen die Hashalgorithmen MD2, MD4, MD5, SHA-0, SHA-1 oder RIPEMD nicht verwendet werden. Aus computertechnischer Sicht kann es für diese Algorithmen zu Hashkonflikten und somit zu Fehlern kommen.</p><p>Zulässige .NET-Hashalgorithmen für verwaltete kryptografische Flexibilität (sortiert nach Präferenz):</p><ul><li>SHA512Cng (FIPS-konform)</li><li>SHA384Cng (FIPS-konform)</li><li>SHA256Cng (FIPS-konform)</li><li>SHA512Managed (nicht FIPS-konform) (Verwenden Sie in Aufrufen von HashAlgorithm.Create oder CryptoConfig.CreateFromName „SHA512“ als Algorithmusname.)</li><li>SHA384Managed (nicht FIPS-konform) (Verwenden Sie in Aufrufen von HashAlgorithm.Create oder CryptoConfig.CreateFromName „SHA384“ als Algorithmusname.)</li><li>SHA256Managed (nicht FIPS-konform) (Verwenden Sie in Aufrufen von HashAlgorithm.Create oder CryptoConfig.CreateFromName „SHA256“ als Algorithmusname.)</li><li>SHA512CryptoServiceProvider (FIPS-konform)</li><li>SHA256CryptoServiceProvider (FIPS-konform)</li><li>SHA384CryptoServiceProvider (FIPS-konform)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Verwenden von sicheren Verschlüsselungsalgorithmen zum Verschlüsseln von Daten in der Datenbank

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Auswählen eines Verschlüsselungsalgorithmus](/sql/relational-databases/security/encryption/choose-an-encryption-algorithm) |
| **Schritte** | Mit Verschlüsselungsalgorithmen werden Datentransformationen definiert, die von unbefugten Benutzern nicht leicht umgekehrt werden können. In SQL Server können Administratoren und Entwickler zwischen mehreren Algorithmen wählen, z.B. DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-Bit RC4, DESX, 128-Bit AES, 192-Bit AES und 256-Bit AES. |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>SSIS-Pakete sollten verschlüsselt und digital signiert werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Identifizieren der Quelle von Paketen mit digitalen Signaturen](/sql/integration-services/security/identify-the-source-of-packages-with-digital-signatures), [Mindern von Bedrohungen und Sicherheitsrisiken (Integration Services)](/sql/integration-services/security/security-overview-integration-services) |
| **Schritte** | Die Quelle eines Pakets ist die Einzelperson oder die Organisation, die das Paket erstellt hat. Das Ausführen eines Pakets von einer unbekannten oder nicht vertrauenswürdigen Quelle kann riskant sein. Sie sollten digitale Signaturen verwenden, um die unbefugte Manipulation von SSIS-Paketen zu verhindern. Außerdem müssen SSIS-Pakete verschlüsselt werden, um die Vertraulichkeit der Pakete beim Speichern und Übertragen sicherzustellen. |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Hinzufügen einer digitalen Signatur zu kritischen sicherungsfähigen Elementen von Datenbanken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [ADD SIGNATURE (Transact-SQL)](/sql/t-sql/statements/add-signature-transact-sql) |
| **Schritte** | In Fällen, in denen die Integrität eines kritischen sicherungsfähigen Elements von Datenbanken überprüft werden muss, müssen digitale Signaturen verwendet werden. Sicherungsfähige Elemente von Datenbanken, z.B. gespeicherte Prozedur, Funktion, Assembly oder Trigger, können digital signiert werden. Unten ist ein Beispiel dafür angegeben, wann dies hilfreich ist: Angenommen, ein unabhängiger Softwarehersteller (Independent Software Vendor, ISV) leistet Support für eine Software, die an einen Kunden geliefert wurde. Vor der Supportleistung möchte der ISV sicherstellen, dass ein sicherungsfähiges Element der Datenbank in der Software nicht versehentlich oder in böser Absicht bewusst manipuliert wurde. Wenn das sicherungsfähige Element digital signiert ist, kann der ISV die digitale Signatur prüfen und die Integrität bestätigen.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Verwenden der erweiterbaren Schlüsselverwaltung von SQL Server zum Schützen von Verschlüsselungsschlüsseln

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Erweiterbare Schlüsselverwaltung (Extensible Key Management, EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm), [Erweiterbare Schlüsselverwaltung mit Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server) |
| **Schritte** | Mit der erweiterbaren Schlüsselverwaltung (Extensible Key Management, EKM) von SQL Server können die Verschlüsselungsschlüssel, mit denen die Datenbankdateien geschützt werden, auf einem externen Gerät gespeichert werden. Beispiele hierfür sind eine Smartcard, ein USB-Gerät oder ein EKM/HSM-Modul. Hiermit wird auch der Schutz von Daten durch Datenbankadministratoren ermöglicht (mit Ausnahme von Mitgliedern der sysadmin-Gruppe). Daten können mit Verschlüsselungsschlüsseln verschlüsselt werden, auf die nur der Datenbankbenutzer auf dem externen EKM/HSM-Modul Zugriff hat. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>Verwenden des Features AlwaysEncrypted, wenn Verschlüsselungsschlüssel für das Datenbankmodul nicht offengelegt werden sollen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | SQL Azure, lokal |
| **Attribute**              | SQL-Version: V12, MsSQL2016 |
| **Referenzen**              | [„Immer verschlüsselt“ (Datenbank-Engine)](/sql/relational-databases/security/encryption/always-encrypted-database-engine) |
| **Schritte** | Always Encrypted ist ein Feature zum Schützen von vertraulichen Daten, z.B. Kreditkartennummern oder nationalen Identifikationsnummern (z.B. Sozialversicherungsnummer), die in Azure SQL-Datenbank oder SQL Server-Datenbanken gespeichert sind. Mit Always Encrypted können Kunden vertrauliche Daten in Clientanwendungen verschlüsseln, ohne die Verschlüsselungsschlüssel für das Datenbankmodul (SQL-Datenbank oder SQL Server) jemals offenzulegen. Zu diesem Zweck ermöglicht Always Encrypted eine Trennung zwischen den Besitzern der Daten (die diese anzeigen dürfen) und den Personen, die die Daten verwalten (aber ansonsten keinen Zugriff haben). |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Sicheres Speichern von kryptografischen Schlüsseln auf dem IoT-Gerät

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Gerät | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Gerätebetriebssystem: Windows IoT Core, Gerätekonnektivität: Azure IoT-Geräte-SDKs |
| **Referenzen**              | [TPM on Windows IoT Core](/windows/iot-core/secure-your-device/TPM) (TPM unter Windows IoT Core), [Set up TPM on Windows IoT Core](/windows/iot-core/secure-your-device/setuptpm) (Einrichten von TPM unter Windows IoT Core), [Azure IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) (Azure IoT-Geräte-SDK – TPM) |
| **Schritte** | Legen Sie private symmetrische oder Zertifikatschlüssel sicher in einem per Hardware geschützten Speicher ab, z.B. TPM oder Smartcardchips. Windows 10 IoT Core unterstützt die Verwendung eines TPM, und Sie können mehrere kompatible TPMs nutzen: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. Es wird empfohlen, ein Firmware-TPM oder diskretes TPM zu verwenden. Ein Software-TPM sollte nur zu Entwicklungs- und Testzwecken eingesetzt werden. Nachdem ein TPM verfügbar ist und die Schlüssel darin bereitgestellt wurden, sollte der Code zum Generieren des Tokens geschrieben werden, ohne dass darin enthaltene sensible Informationen hartcodiert werden. | 

### <a name="example"></a>Beispiel
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Es ist erkennbar, dass der primäre Geräteschlüssel nicht im Code enthalten ist. Stattdessen ist er im TPM unter Slot 0 gespeichert. Das TPM-Gerät generiert ein SAS-Token mit kurzer Gültigkeitsdauer, das dann zum Herstellen der Verbindung mit dem IoT Hub verwendet wird. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Generieren eines zufälligen symmetrischen Schlüssels mit einer ausreichenden Länge für die Authentifizierung gegenüber IoT Hub

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Cloudgateway | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Wahl des Gateways: Azure IoT Hub |
| **Referenzen**              | –  |
| **Schritte** | IoT Hub enthält eine Geräteidentitätsregistrierung und generiert beim Bereitstellen eines Geräts automatisch einen zufälligen symmetrischen Schlüssel. Es wird empfohlen, dieses Feature der Azure IoT Hub-Identitätsregistrierung zum Generieren des Schlüssels zu nutzen, der für die Authentifizierung eingesetzt wird. IoT Hub ermöglicht auch, dass beim Erstellen des Geräts ein Schlüssel angegeben wird. Wenn während der Bereitstellung des Geräts außerhalb von IoT Hub ein Schlüssel generiert wird, wird die Erstellung eines zufälligen symmetrischen Schlüssels mit mindestens 256 Bit empfohlen. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Sicherstellen der Verwendung einer Richtlinie für die Geräteverwaltung, die für die Nutzung eine PIN erforderlich macht und das Remotelöschen ermöglicht

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Dynamics CRM – Mobiler Client | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Sicherstellen der Verwendung einer Richtlinie für die Geräteverwaltung, die für die Nutzung eine PIN erforderlich macht und das Remotelöschen ermöglicht |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Sicherstellen der Verwendung einer Richtlinie für die Geräteverwaltung, die PIN/Kennwort/automatische Sperre erforderlich macht und alle Daten verschlüsselt (z.B. BitLocker)

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Dynamics CRM – Outlook-Client | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Sicherstellen der Verwendung einer Richtlinie für die Geräteverwaltung, die PIN/Kennwort/automatische Sperre erforderlich macht und alle Daten verschlüsselt (z.B. BitLocker) |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Sicherstellen, dass für Signaturschlüssel bei Verwendung von Identity Server ein Rollover durchgeführt wird

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Identity Server | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Identity Server - Keys, Signatures and Cryptography](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) (Identity Server – Schlüssel, Signaturen und Kryptografie) |
| **Schritte** | Stellen Sie sicher, dass für Signaturschlüssel bei Verwendung von Identity Server ein Rollover durchgeführt wird. Unter dem Link im Abschnitt „Referenzen“ wird beschrieben, wie dies geplant werden sollte, ohne dass es für Anwendungen, die von Identity Server abhängig sind, zu Ausfällen kommt. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Stellen Sie sicher, dass in Identity Server eine in kryptografischer Hinsicht sichere Client-ID und ein sicherer geheimer Clientschlüssel verwendet werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Identity Server | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Stellen Sie sicher, dass in Identity Server eine in kryptografischer Hinsicht sichere Client-ID und ein sicherer geheimer Clientschlüssel verwendet werden. Zum Generieren einer Client-ID und des Geheimnisses sollten die folgenden Richtlinien beachtet werden:</p><ul><li>Generieren einer zufälligen GUID als Client-ID</li><li>Generieren eines kryptografisch zufälligen Schlüssels mit 256 Bit als Geheimnis</li></ul>|