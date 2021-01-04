---
title: 'Azure AD Connect-Synchronisierung: Funktionsreferenz | Microsoft-Dokumentation'
description: Referenz der Ausdrücke für die deklarative Bereitstellung in der Azure AD Connect-Synchronisierung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6c8be064ade8182355c320e948b3b60b846033d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348057"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect-Synchronisierung: Funktionsreferenz
In der Azure AD Connect-Synchronisierung werden Funktionen verwendet, um Attributwerte während der Synchronisierung zu ändern.  
Die Syntax der Funktionen wird im folgenden Format ausgedrückt:  
`<output type> FunctionName(<input type> <position name>, ..)`

Wenn eine Funktion überladen ist und mehrere Syntaxen akzeptiert, werden alle gültigen Syntaxen aufgeführt.  
Die Funktionen sind stark typisiert und überprüfen, ob der übergebene Typ dem dokumentierten Typ entspricht.  
Stimmt der Typ nicht überein, tritt ein Fehler auf.

Die Typen werden mit der folgenden Syntax ausgedrückt:

* **bin** – binär
* **bool** – boolesch
* **dt** – UTC-Datum/-Uhrzeit
* **enum** – Enumeration von bekannten Konstanten
* **exp** – Ausdruck, der zu einem booleschen Wert ausgewertet werden soll
* **mvbin** – mehrwertiger Binärwert
* **mvstr** – mehrwertige Zeichenfolge
* **mvref** – mehrwertiger Verweis
* **num** – numerisch
* **ref** – Verweis
* **str** – Zeichenfolge
* **var** – eine Variante (fast) jedes anderen Typs
* **void** – gibt keinen Wert zurück

Funktionen mit den Typen **mvbin**, **mvstr** und **mvref** können nur für mehrwertige Attribute verwendet werden. Funktionen mit **bin**, **str** und **ref** können sowohl für einwertige als auch für mehrwertige Attribute verwendet werden.

## <a name="functions-reference"></a>Funktionsreferenz

* **Certificate**
  * [CertExtensionOids](#certextensionoids)
  * [CertFormat](#certformat)
  * [CertFriendlyName](#certfriendlyname)
  * [CertHashString](#certhashstring)
  * [CertIssuer](#certissuer)
  * [CertIssuerDN](#certissuerdn)
  * [CertIssuerOid](#certissueroid)
  * [CertKeyAlgorithm](#certkeyalgorithm)
  * [CertKeyAlgorithmParams](#certkeyalgorithmparams)
  * [CertNameInfo](#certnameinfo)
  * [CertNotAfter](#certnotafter)
  * [CertNotBefore](#certnotbefore)
  * [CertPublicKeyOid](#certpublickeyoid)
  * [CertPublicKeyParametersOid](#certpublickeyparametersoid)
  * [CertSerialNumber](#certserialnumber)
  * [CertSignatureAlgorithmOid](#certsignaturealgorithmoid)
  * [CertSubject](#certsubject)
  * [CertSubjectNameDN](#certsubjectnamedn)
  * [CertSubjectNameOid](#certsubjectnameoid)
  * [CertThumbprint](#certthumbprint)
  * [CertVersion](#certversion)
  * [IsCert](#iscert)
* **Konvertierung**
  * [CBool](#cbool)
  * [CDate](#cdate)
  * [CGuid](#cguid)
  * [ConvertFromBase64](#convertfrombase64)
  * [ConvertToBase64](#converttobase64)
  * [ConvertFromUTF8Hex](#convertfromutf8hex)
  * [ConvertToUTF8Hex](#converttoutf8hex)
  * [CNum](#cnum)
  * [CRef](#cref)
  * [CStr](#cstr)
  * [StringFromGuid](#stringfromguid)
  * [StringFromSid](#stringfromsid)
* **Datum/Uhrzeit**
  * [DateAdd](#dateadd)
  * [DateFromNum](#datefromnum)
  * [FormatDateTime](#formatdatetime)
  * [Now](#now)
  * [NumFromDate](#numfromdate)
* **Verzeichnis**
  * [DNComponent](#dncomponent)
  * [DNComponentRev](#dncomponentrev)
  * [EscapeDNComponent](#escapedncomponent)
* **Auswertung**
  * [IsBitSet](#isbitset)
  * [IsDate](#isdate)
  * [IsEmpty](#isempty)
  * [IsGuid](#isguid)
  * [IsNull](#isnull)
  * [IsNullOrEmpty](#isnullorempty)
  * [IsNumeric](#isnumeric)
  * [IsPresent](#ispresent)
  * [IsString](#isstring)
* **Mathematisch**
  * [BitAnd](#bitand)
  * [BitOr](#bitor)
  * [RandomNum](#randomnum)
* **Multi*valued**
  * [Contains](#contains)
  * [Count](#count)
  * [Element](#item)
  * [ItemOrNull](#itemornull)
  * [Join](#join)
  * [RemoveDuplicates](#removeduplicates)
  * [Split](#split)
* **Programmablauf**
  * [Fehler](#error)
  * [IIF](#iif)
  * [Auswählen](#select)
  * [Switch](#switch)
  * [Where](#where)
  * [With](#with)
* **Text**
  * [GUID](#guid)
  * [InStr](#instr)
  * [InStrRev](#instrrev)
  * [LCase](#lcase)
  * [Left](#left)
  * [Len](#len)
  * [LTrim](#ltrim)
  * [Mid](#mid)
  * [PadLeft](#padleft)
  * [PadRight](#padright)
  * [PCase](#pcase)
  * [Replace](#replace)
  * [ReplaceChars](#replacechars)
  * [Right](#right)
  * [RTrim](#rtrim)
  * [Trim](#trim)
  * [UCase](#ucase)
  * [Word](#word)

---
### <a name="bitand"></a>BitAnd
**Beschreibung:**  
Die BitAnd-Funktion legt angegebene Bits auf einen Wert fest.

**Syntax:**  
`num BitAnd(num value1, num value2)`

* value1, value2: Numerische Werte, die mit AND verknüpft werden sollen

**Hinweise:**  
Diese Funktion konvertiert beide Parameter in die binäre Darstellung und legt ein Bit auf Folgendes fest:

* 0 – wenn mindestens eines der entsprechenden Bits in *value1* und *value2* den Wert „0“ aufweist
* 1 – wenn beide entsprechenden Bits 1 sind.

Anders gesagt: sie gibt in allen Fällen 0 zurück, außer wenn die entsprechenden Bits beider Parameter 1 sind.

**Beispiel:**  
`BitAnd(&HF, &HF7)`  
Gibt 7 zurück, da durch die Verknüpfung mit AND die Hexadezimalwerte „F“ und „F7“ zu diesem Wert ausgewertet werden.

---
### <a name="bitor"></a>BitOr
**Beschreibung:**  
Die BitOr-Funktion legt angegebene Bits auf einen Wert fest.

**Syntax:**  
`num BitOr(num value1, num value2)`

* value1, value2: Numerische Werte, die mit OR verknüpft werden sollen.

**Hinweise:**  
Diese Funktion konvertiert beide Parameter in die binäre Darstellung und legt ein Bit auf 1 fest, wenn mindestens eins der entsprechenden Bits in „mask“ und „flag“ 1 ist, und auf 0, wenn beide entsprechenden Bits 0 sind. Anders gesagt gibt sie in allen Fällen 1 zurück, außer wenn die entsprechenden Bits der beiden Parameter 0 sind.

---
### <a name="cbool"></a>ZBool
**Beschreibung:**  
Die CBool-Funktion gibt einen booleschen Wert zurück, der auf dem ausgewerteten Ausdruck basiert.

**Syntax:**  
`bool CBool(exp Expression)`

**Hinweise:**  
Wenn die Auswertung des Ausdrucks einen Wert ungleich null ergibt, gibt CBool TRUE zurück. Andernfalls wird FALSE zurückgegeben.

**Beispiel:**  
`CBool([attrib1] = [attrib2])`  

Gibt True zurück, wenn beide Attribute den gleichen Wert haben.

---
### <a name="cdate"></a>ZDate
**Beschreibung:**  
Die CDate-Funktion gibt einen UTC-DateTime-Wert aus einer Zeichenfolge zurück. "DateTime" ist kein synchronisierungsspezifischer Attributtyp, wird jedoch von einigen Funktionen verwendet.

**Syntax:**  
`dt CDate(str value)`

* Wert: Eine Zeichenfolge mit Datum, Uhrzeit und optional einer Zeitzone

**Hinweise:**  
Die zurückgegebene Zeichenfolge ist immer ein UTC-Wert.

**Beispiel:**  
`CDate([employeeStartTime])`  
Gibt einen DateTime-Wert auf Basis der Anfangszeit des Mitarbeiters zurück.

`CDate("2013-01-10 4:00 PM -8")`  
Gibt einen DateTime-Wert zurück, der „2013-01-11 12:00 AM“ darstellt.


---
### <a name="certextensionoids"></a>CertExtensionOids
**Beschreibung:**  
Gibt die OID-Werte aller kritischen Erweiterungen eines Zertifikatobjekts zurück

**Syntax:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certformat"></a>CertFormat
**Beschreibung:**  
Gibt den Formatnamen dieses X.509v3-Zertifikats zurück

**Syntax:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Beschreibung:**  
Gibt den zugeordneten Alias eines Zertifikats zurück

**Syntax:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certhashstring"></a>CertHashString
**Beschreibung:**  
Gibt den SHA1-Hashwert des X. 509v3-Zertifikats als hexadezimale Zeichenfolge zurück

**Syntax:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certissuer"></a>CertIssuer
**Beschreibung:**  
Gibt den Namen der Zertifizierungsstelle zurück, die das X.509v3-Zertifikat ausgestellt hat

**Syntax:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Beschreibung:**  
Gibt den Distinguished Name des Zertifikatausstellers zurück

**Syntax:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certissueroid"></a>CertIssuerOid
**Beschreibung:**  
Gibt die OID des Zertifikatausstellers zurück

**Syntax:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Beschreibung:**  
Gibt die Schlüsselalgorithmusinformationen zu diesem X.509v3-Zertifikat als Zeichenfolge zurück

**Syntax:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Beschreibung:**  
Gibt die Schlüsselalgorithmusparameter für das X.509v3-Zertifikat als hexadezimale Zeichenfolge zurück

**Syntax:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certnameinfo"></a>CertNameInfo
**Beschreibung:**  
Gibt die Namen des Antragstellers und des Ausstellers aus einem Zertifikat zurück

**Syntax:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.
*   X509NameType: Der X509NameType-Wert des Antragstellers.
*   includesIssuerName: TRUE, um den Ausstellernamen einzuschließen, andernfalls FALSE

---
### <a name="certnotafter"></a>CertNotAfter
**Beschreibung:**  
Gibt das Uhrzeit (Ortszeit) zurück, zu der das Zertifikat abläuft

**Syntax:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certnotbefore"></a>CertNotBefore
**Beschreibung:**  
Gibt die Uhrzeit (Ortszeit) zurück, zu der ein Zertifikat gültig wird

**Syntax:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Beschreibung:**  
Gibt die OID des öffentlichen Schlüssels für das X.509v3-Zertifikat zurück

**Syntax:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Beschreibung:**  
Gibt die OID der Parameter des öffentlichen Schlüssels für das X.509v3-Zertifikat zurück

**Syntax:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Beschreibung:**  
Gibt die Seriennummer des X.509v3-Zertifikats zurück

**Syntax:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Beschreibung:**  
Gibt die OID des Algorithmus zurück, mit dem die Signatur eines Zertifikats erstellt wurde

**Syntax:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certsubject"></a>CertSubject
**Beschreibung:**  
Ruft den Distinguished Name des Antragstellers für das Zertifikat ab

**Syntax:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Beschreibung:**  
Gibt den Distinguished Name des Antragstellers für das Zertifikat zurück

**Syntax:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Beschreibung:**  
Gibt die OID des Antragstellernamens aus einem Zertifikat zurück

**Syntax:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certthumbprint"></a>CertThumbprint
**Beschreibung:**  
Gibt den Fingerabdruck eines Zertifikats zurück

**Syntax:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="certversion"></a>CertVersion
**Beschreibung:**  
Gibt die X.509-Formatversion eines Zertifikats zurück

**Syntax:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.

---
### <a name="cguid"></a>CGuid
**Beschreibung:**  
Die CGuid-Funktion konvertiert die Zeichenfolgendarstellung einer GUID in ihre binäre Darstellung.

**Syntax:**  
`bin CGuid(str GUID)`

* Eine in diesem Muster formatierte Zeichenfolge: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx oder {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>Enthält
**Beschreibung:**  
Die Contains-Funktion sucht in einem mehrwertigen Attribut nach einer Zeichenfolge.

**Syntax:**  
`num Contains (mvstring attribute, str search)` (mit Berücksichtigung der Groß-/Kleinschreibung)  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` (mit Berücksichtigung der Groß-/Kleinschreibung)

* attribute: Das zu durchsuchende mehrwertige Attribut.
* search: Die im Attribut zu suchende Zeichenfolge.
* Casetype: „CaseInsensitive“ (ohne Berücksichtigung der Groß-/Kleinschreibung) oder „CaseSensitive“ (mit Berücksichtigung der Groß-/Kleinschreibung).

Gibt den Index in dem mehrwertigen Attribut zurück, in dem die Zeichenfolge gefunden wurde. Wenn die Zeichenfolge nicht gefunden wird, wird 0 zurückgegeben.

**Hinweise:**  
Bei mehrwertigen Zeichenfolgenattributen werden in den Werten Teilzeichenfolgen gefunden.  
Für Verweisattribute muss die Suchzeichenfolge genau mit dem Wert übereinstimmen, der als Übereinstimmung betrachtet wird.

**Beispiel:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Wenn das proxyAddresses-Attribut eine primäre E-Mail-Adresse besitzt (angegeben durch „SMTP:“ [in Großbuchstaben]), wird das proxyAddresses-Attribut zurückgegeben. Andernfalls wird ein Fehler zurückgegeben.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Beschreibung:**  
Die ConvertFromBase64-Funktion konvertiert den angegebenen Base64-codierten Wert in eine reguläre Zeichenfolge.

**Syntax:**  
`str ConvertFromBase64(str source)` – setzt Unicode-Codierung voraus  
`str ConvertFromBase64(str source, enum Encoding)`

* source: Base64-codierte Zeichenfolge  
* Codierung: Unicode, ASCII, UTF8

**Beispiel**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Beide Beispiele geben "*Hello world!* " zurück.

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Beschreibung:**  
Die ConvertFromUTF8Hex-Funktion konvertiert den angegebenen UTF8-Hex-codierten Wert in eine Zeichenfolge.

**Syntax:**  
`str ConvertFromUTF8Hex(str source)`

* source: UTF8-2-Byte-codierte Zeichenfolge

**Hinweise:**  
Der Unterschied zwischen dieser Funktion und „ConvertFromBase64([],UTF8)“ besteht darin, dass das Ergebnis vom DN-Attribut verwertet werden kann.  
Dieses Format wird von Azure Active Directory als DN verwendet.

**Beispiel:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Gibt *Hello world!* zurück.

---
### <a name="converttobase64"></a>ConvertToBase64
**Beschreibung:**  
Die ConvertToBase64-Funktion konvertiert eine Zeichenfolge in eine Unicode-Base64-Zeichenfolge.  
Konvertiert den Wert eines Arrays von ganzen Zahlen in die entsprechende mit Base64-Ziffern codierte Zeichenfolgendarstellung.

**Syntax:**  
`str ConvertToBase64(str source)`

**Beispiel:**  
`ConvertToBase64("Hello world!")`  
Gibt „SABlAGwAbABvACAAdwBvAHIAbABkACEA“ zurück.

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Beschreibung:**  
Die ConvertToUTF8Hex-Funktion konvertiert eine Zeichenfolge in einen hexadezimal-codierten UTF8-Wert.

**Syntax:**  
`str ConvertToUTF8Hex(str source)`

**Hinweise:**  
Das Ausgabeformat dieser Funktion wird von Azure Active Directory als DN-Attributformat verwendet.

**Beispiel:**  
`ConvertToUTF8Hex("Hello world!")`  
Gibt „48656C6C6F20776F726C6421“ zurück.

---
### <a name="count"></a>Anzahl
**Beschreibung:**  
Die Count-Funktion gibt die Anzahl von Elementen in einem mehrwertigen Attribut zurück.

**Syntax:**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**Beschreibung:**  
Die CNum-Funktion nimmt eine Zeichenfolge entgegen und gibt einen numerischen Datentyp zurück.

**Syntax:**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Beschreibung:**  
Konvertiert eine Zeichenfolge in ein Verweisattribut.

**Syntax:**  
`ref CRef(str value)`

**Beispiel:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**Beschreibung:**  
Die CStr-Funktion konvertiert in einen Zeichenfolgendatentyp.

**Syntax:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* value: Kann ein numerischer Wert, ein Verweisattribut oder ein boolescher Wert sein.

**Beispiel:**  
`CStr([dn])`  
Könnte „cn=Joe,dc=contoso,dc=com“ zurückgeben.

---
### <a name="dateadd"></a>DateAdd
**Beschreibung:**  
Gibt einen Datumswert zurück, der ein Datum enthält, zu dem ein angegebenes Zeitintervall addiert wurde.

**Syntax:**  
`dt DateAdd(str interval, num value, dt date)`

* interval: Zeichenfolgenausdruck, der das Zeitintervall angibt, das hinzugefügt werden soll. Die Zeichenfolge muss einen der folgenden Werte aufweisen:
  * yyyy: Jahr
  * q: Quartal
  * m: Monat
  * y: Tag des Jahres
  * d: Tag
  * w: Wochentag
  * ww: Woche
  * h: Stunde
  * n: Minute
  * s: Sekunde
* value: Die Anzahl der Einheiten, die Sie hinzufügen möchten. Der Wert kann positiv (für Datumsangaben in der Zukunft) oder negativ (für Datumsangaben in der Vergangenheit) sein.
* date: DateTime-Wert, der das Datum darstellt, dem das Intervall hinzugefügt wird.

**Beispiel:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Addiert drei Monate und gibt einen DateTime-Wert zurück, der „2001-04-01“ darstellt.

---
### <a name="datefromnum"></a>DateFromNum
**Beschreibung:**  
Die DateFromNum-Funktion konvertiert einen Wert im AD-Datumsformat in einen DateTime-Typ.

**Syntax:**  
`dt DateFromNum(num value)`

**Beispiel:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Gibt einen DateTime-Wert zurück, der „2012-01-01 23:00:00“ darstellt.

---
### <a name="dncomponent"></a>DNComponent
**Beschreibung:**  
Die DNComponent-Funktion gibt den Wert einer angegebenen DN-Komponente von links beginnend zurück.

**Syntax:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: Das zu interpretierende Verweisattribut
* ComponentNumber: Die Komponente im zurückzugebenden DN

**Beispiel:**  
`DNComponent(CRef([dn]),1)`  
Falls der DN „cn=Joe,ou=…“ lautet, wird „Joe“ zurückgegeben.

---
### <a name="dncomponentrev"></a>DNComponentRev
**Beschreibung:**  
Die DNComponentRev-Funktion gibt den Wert einer angegebenen DN-Komponente von rechts beginnend (also vom Ende her) zurück.

**Syntax:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: Das zu interpretierende Verweisattribut
* ComponentNumber: Die Komponente im zurückzugebenden DN
* Optionen: DC – alle Komponenten mit „dc=“ ignorieren

**Beispiel:**  
Falls der DN „cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com“ lautet, geben beide  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
„US“ zurück.

---
### <a name="error"></a>Fehler
**Beschreibung:**  
Die Error-Funktion wird verwendet, um einen benutzerdefinierten Fehler zurückzugeben.

**Syntax:**  
`void Error(str ErrorMessage)`

**Beispiel:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Wenn das accountName-Attribut nicht vorhanden ist, wird ein Fehler für das Objekt ausgegeben.

---
### <a name="escapedncomponent"></a>EscapeDNComponent
**Beschreibung:**  
Die EscapeDNComponent-Funktion versieht eine Komponente eines DN mit Escapezeichen, sodass sie im LDAP dargestellt werden kann.

**Syntax:**  
`str EscapeDNComponent(str value)`

**Beispiel:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Stellt sicher, dass das Objekt in einem LDAP-Verzeichnis erstellt werden kann, auch wenn das displayName-Attribut Zeichen enthält, die in LDAP mit Escapezeichen versehen werden müssen.

---
### <a name="formatdatetime"></a>FormatDatumZeit
**Beschreibung:**  
Die FormatDateTime-Funktion wird verwendet, um einen DateTime-Wert in eine Zeichenfolge in einem angegebenen Format zu formatieren.

**Syntax:**  
`str FormatDateTime(dt value, str format)`

* value: Ein Wert im DateTime-Format
* format: Eine Zeichenfolge, die das Format darstellt, in das konvertiert werden soll.

**Hinweise:**  
Die möglichen Werte für das Format finden Sie hier: [Benutzerdefinierte Datums- und Uhrzeitformate für die FORMAT-Funktion](/dax/custom-date-and-time-formats-for-the-format-function).

**Beispiel:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Ergibt „2007-12-25“.

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Kann „20140905081453.0Z“ ergeben.

---
### <a name="guid"></a>Guid
**Beschreibung:**  
Die Guid-Funktion generiert eine neue GUID nach dem Zufallsprinzip.

**Syntax:**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Beschreibung:**  
Die IIF-Funktion gibt basierend auf einer angegebenen Bedingung einen Wert aus einem Satz möglicher Werte zurück.

**Syntax:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condition: Ein beliebiger Wert oder Ausdruck, der als True oder False ausgewertet werden kann.
* valueIfTrue: Der zurückgegebene Wert, wenn die Bedingung als „True“ ausgewertet wird.
* valueIfFalse: Der zurückgegebene Wert, wenn die Bedingung als „False“ ausgewertet wird.

**Beispiel:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Gibt bei Praktikanten den Alias eines Benutzers mit einem vorangestellten „t-“ zurück. Andernfalls wird der unveränderte Alias des Benutzers zurückgegeben.

---
### <a name="instr"></a>InStr
**Beschreibung:**  
Die InStr-Funktion sucht nach dem ersten Vorkommen einer Teilzeichenfolge in einer Zeichenfolge.

**Syntax:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: Zu durchsuchende Zeichenfolge
* stringmatch: Zu findende Zeichenfolge
* start: Startposition zum Finden der Teilzeichenfolge
* compare: VbTextCompare oder VbBinaryCompare

**Hinweise:**  
Gibt die Position zurück, an der die Teilzeichenfolge gefunden wurde (oder 0, wenn sie nicht gefunden wurde).

**Beispiel:**  
`InStr("The quick brown fox","quick")`  
Wird als 5 ausgewertet.

`InStr("repEated","e",3,vbBinaryCompare)`  
Wird als 7 ausgewertet.

---
### <a name="instrrev"></a>InStrRev
**Beschreibung:**  
Die InStrRev-Funktion sucht nach dem letzten Vorkommen einer Teilzeichenfolge in einer Zeichenfolge.

**Syntax:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: Zu durchsuchende Zeichenfolge
* stringmatch: Zu findende Zeichenfolge
* start: Startposition zum Finden der Teilzeichenfolge
* compare: VbTextCompare oder VbBinaryCompare

**Hinweise:**  
Gibt die Position zurück, an der die Teilzeichenfolge gefunden wurde (oder 0, wenn sie nicht gefunden wurde).

**Beispiel:**  
`InStrRev("abbcdbbbef","bb")`  
Gibt 7 zurück.

---
### <a name="isbitset"></a>IsBitSet
**Beschreibung:**  
Die IsBitSet-Funktion testet, ob ein Bit festgelegt ist.

**Syntax:**  
`bool IsBitSet(num value, num flag)`

* value: Ein numerischer Wert, der ausgewertet wird. flag: Ein numerischer Wert, der das Bit angibt, das ausgewertet werden soll.

**Beispiel:**  
`IsBitSet(&HF,4)`  
Gibt „True“ zurück, da Bit 4 auf den Hexadezimalwert „F“ festgelegt ist.

---
### <a name="isdate"></a>IsDate
**Beschreibung:**  
Die IsDate-Funktion wird als „True“ ausgewertet, wenn der Ausdruck als DateTime-Typ ausgewertet werden kann.

**Syntax:**  
`bool IsDate(var Expression)`

**Hinweise:**  
Wird verwendet, um zu bestimmen, ob „CDate()“ erfolgreich sein kann.

---
### <a name="iscert"></a>IsCert
**Beschreibung:**  
Gibt TRUE zurück, wenn die Rohdaten in ein .NET X509Certificate2-Zertifikatobjekt serialisiert werden können

**Syntax:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Darstellung eines Bytearrays eines X.509-Zertifikats. Das Bytearray kann aus binär (DER) codierten oder Base64-codierten X.509-Daten bestehen.
---
### <a name="isempty"></a>IsEmpty
**Beschreibung:**  
Die IsEmpty-Funktion wird als „True“ ausgewertet, wenn das Attribut in CS oder MV vorhanden ist, jedoch zu einer leeren Zeichenfolge ausgewertet wird.

**Syntax:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**Beschreibung:**  
Die IsGuid-Funktion wird als „True“ ausgewertet, wenn die Zeichenfolge in eine GUID konvertiert werden kann.

**Syntax:**  
`bool IsGuid(str GUID)`

**Hinweise:**  
Eine GUID ist als eine Zeichenfolge definiert, die einem der folgenden Muster entspricht: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx oder {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Wird verwendet, um zu bestimmen, ob „CGuid()“ erfolgreich sein kann.

**Beispiel:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Wenn „StrAttribute“ ein GUID-Format besitzt, wird die binäre Darstellung zurückgegeben. Andernfalls wird ein Nullwert zurückgegeben.

---
### <a name="isnull"></a>IsNull
**Beschreibung:**  
Die IsNull-Funktion gibt „True“ zurück, wenn die Auswertung des Ausdrucks einen Nullwert ergibt.

**Syntax:**  
`bool IsNull(var Expression)`

**Hinweise:**  
Für ein Attribut wird ein Nullwert durch die Abwesenheit des Attributs ausgedrückt.

**Beispiel:**  
`IsNull([displayName])`  
Gibt „True“ zurück, wenn das Attribut nicht in CS oder MV vorhanden ist.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Beschreibung:**  
Die IsNullOrEmpty-Funktion gibt „True“ zurück, wenn der Ausdruck einem Nullwert oder einer leeren Zeichenfolge entspricht.

**Syntax:**  
`bool IsNullOrEmpty(var Expression)`

**Hinweise:**  
Wird für ein Attribut als „True“ ausgewertet, wenn das Attribut nicht vorhanden oder zwar vorhanden, aber eine leere Zeichenfolge ist.  
Die Umkehrung dieser Funktion heißt "IsPresent".

**Beispiel:**  
`IsNullOrEmpty([displayName])`  
Gibt „True“ zurück, wenn das Attribut in CS oder MV nicht vorhanden oder eine leere Zeichenfolge ist.

---
### <a name="isnumeric"></a>IsNumeric
**Beschreibung:**  
Die IsNumeric-Funktion gibt einen booleschen Wert zurück, der angibt, ob ein Ausdruck als Zahlentyp ausgewertet werden kann.

**Syntax:**  
`bool IsNumeric(var Expression)`

**Hinweise:**  
Wird verwendet, um zu bestimmen, ob „CNum()“ erfolgreich zum Analysieren des Ausdrucks verwendet werden kann.

---
### <a name="isstring"></a>IsString
**Beschreibung:**  
Die IsString-Funktion wird als „True“ ausgewertet, wenn der Ausdruck als Zeichenfolgentyp ausgewertet werden kann.

**Syntax:**  
`bool IsString(var expression)`

**Hinweise:**  
Wird verwendet, um zu bestimmen, ob „CStr()“ erfolgreich zum Analysieren des Ausdrucks verwendet werden kann.

---
### <a name="ispresent"></a>IsPresent
**Beschreibung:**  
Die IsPresent-Funktion gibt „True“ zurück, wenn der Ausdruck zu einer Zeichenfolge ausgewertet wird, die kein Nullwert und nicht leer ist.

**Syntax:**  
`bool IsPresent(var expression)`

**Hinweise:**  
Die umgekehrte Funktion heißt „IsNullOrEmpty“.

**Beispiel:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Element
**Beschreibung:**  
Die Item-Funktion gibt ein Element aus einer mehrwertigen Zeichenfolge oder einem mehrwertigen Attribut zurück.

**Syntax:**  
`var Item(mvstr attribute, num index)`

* attribute: Mehrwertiges Attribut
* index: Index für ein Element in der mehrwertigen Zeichenfolge

**Hinweise:**  
Die Item-Funktion lässt sich mit der Contains-Funktion kombinieren, da letztere den Index für ein Element im mehrwertigen Attribut zurückgibt.

Gibt einen Fehler aus, wenn der Index außerhalb des gültigen Bereichs liegt.

**Beispiel:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Gibt die primäre E-Mail-Adresse zurück.

---
### <a name="itemornull"></a>ItemOrNull
**Beschreibung:**  
Die ItemOrNull-Funktion gibt ein Element aus einer mehrwertigen Zeichenfolge/einem mehrwertigen Attribut zurück.

**Syntax:**  
`var ItemOrNull(mvstr attribute, num index)`

* attribute: Mehrwertiges Attribut
* index: Index für ein Element in der mehrwertigen Zeichenfolge

**Hinweise:**  
Die ItemOrNull-Funktion lässt sich mit der Contains-Funktion kombinieren, da letztere den Index für ein Element im mehrwertigen Attribut zurückgibt.

Liegt der Index außerhalb des gültigen Bereichs, wird ein Nullwert zurückgegeben.

---
### <a name="join"></a>Join
**Beschreibung:**  
Die Join-Funktion akzeptiert eine Zeichenfolge mit mehreren Werten und gibt eine einwertige Zeichenfolge zurück, zwischen deren einzelnen Elementen das angegebene Trennzeichen eingefügt ist.

**Syntax:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* Attribut: Mehrwertiges Attribut mit Zeichenfolgen, die verknüpft werden sollen.
* delimiter: Eine beliebige Zeichenfolge, die die Teilzeichenfolgen in der zurückgegebenen Zeichenfolge trennt. Wenn nicht angegeben, wird das Leerzeichen (" ") verwendet. Wenn "delimiter" eine Zeichenfolge der Länge 0 ("") oder "Nothing" ist, werden alle Elemente in der Liste ohne Trennzeichen verkettet.

**Anmerkungen**  
Zwischen den Funktionen „Join“ und „Split“ besteht Parität. Die Funktion "Join" nimmt ein Array von Zeichenfolgen entgegen, verknüpft sie mit einer Trennzeichenfolge und gibt eine einzige Zeichenfolge zurück. Die Funktion "Split" nimmt eine Zeichenfolge entgegen, trennt sie mit dem Trennzeichen und gibt ein Array von Zeichenfolgen zurück. Ein wichtiger Unterschied ist jedoch, dass "Join" Zeichenfolgen mit einer beliebigen Trennzeichenfolge verketten kann, "Split" aber nur Zeichenfolgen mit einem einzigen Trennzeichen trennen kann.

**Beispiel:**  
`Join([proxyAddresses],",")`  
Kann Folgendes zurückgeben: SMTP:john.doe@contoso.com, smtp:jd@contoso.com

---
### <a name="lcase"></a>Kleinbst
**Beschreibung:**  
Die LCase-Funktion konvertiert alle Zeichen in einer Zeichenfolge in Kleinbuchstaben.

**Syntax:**  
`str LCase(str value)`

**Beispiel:**  
`LCase("TeSt")`  
Gibt „test“ zurück.

---
### <a name="left"></a>Left
**Beschreibung:**  
Die Left-Funktion gibt eine angegebene Anzahl von Zeichen von der linken Seite einer Zeichenfolge zurück.

**Syntax:**  
`str Left(str string, num NumChars)`

* string: Zeichenfolge, aus der Zeichen zurückgegeben werden
* numChars: Diese Zahl gibt die Anzahl der Zeichen an, die vom Anfang der Zeichenfolge (links) zurückgeben werden

**Hinweise:**  
Eine Zeichenfolge, die die ersten durch „numChars“ angegebenen Zeichen in der Zeichenfolge enthält:

* Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
* Wenn "numChars" < 0, wird die Eingabezeichenfolge zurückgegeben.
* Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.

Wenn die Zeichenfolge weniger Zeichen enthält als in „numChars“ angegeben, wird eine identische Zeichenfolge (also eine Zeichenfolge, die alle in Parameter 1 enthaltenen Zeichen enthält) zurückgegeben.

**Beispiel:**  
`Left("John Doe", 3)`  
Gibt „Joh“ zurück.

---
### <a name="len"></a>Len
**Beschreibung:**  
Die Len-Funktion gibt die Anzahl von Zeichen in einer Zeichenfolge zurück.

**Syntax:**  
`num Len(str value)`

**Beispiel:**  
`Len("John Doe")`  
Gibt „8“ zurück.

---
### <a name="ltrim"></a>LGlätten
**Beschreibung:**  
Die LTrim-Funktion entfernt führende Leerzeichen aus einer Zeichenfolge.

**Syntax:**  
`str LTrim(str value)`

**Beispiel:**  
`LTrim(" Test ")`  
Gibt „Test “ zurück.

---
### <a name="mid"></a>Mid
**Beschreibung:**  
Die Mid-Funktion gibt eine bestimmte Anzahl von Zeichen ab einer bestimmten Position in einer Zeichenfolge zurück.

**Syntax:**  
`str Mid(str string, num start, num NumChars)`

* string: Zeichenfolge, aus der Zeichen zurückgegeben werden
* start: Eine Zahl zur Angabe der Ausgangsposition in der Zeichenfolge, ab der Zeichen zurückgegeben werden
* numChars: Eine Zahl zur Angabe der Zeichen, die ab der Position in der Zeichenfolge zurückgegeben werden

**Hinweise:**  
Gibt die durch „numChars“ angegebene Anzahl von Zeichen ab der Ausgangsposition in der Zeichenfolge zurück.  
Eine Zeichenfolge, die "numChars" Zeichen ab der Ausgangsposition in der Zeichenfolge enthält:

* Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
* Wenn "numChars" < 0, wird die Eingabezeichenfolge zurückgegeben.
* Wenn "start" > Länge der Zeichenfolge, wird die Eingabezeichenfolge zurückgegeben
* Wenn "start" < = 0, wird die Eingabezeichenfolge zurückgegeben
* Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.

Wenn in der Zeichenfolge nicht so viele Zeichen ab der Ausgangsposition enthalten sind wie durch „numChars“ angegeben, werden so viele Zeichen wie möglich zurückgegeben.

**Beispiel:**  
`Mid("John Doe", 3, 5)`  
Gibt „hn Do“ zurück.

`Mid("John Doe", 6, 999)`  
Gibt „Doe“ zurück.

---
### <a name="now"></a>jetzt
**Beschreibung:**  
Die Now-Funktion gibt einen DateTime-Wert zurück, der das aktuelle Datum und die aktuelle Uhrzeit gemäß Systemdatum und -uhrzeit Ihres Computers angibt.

**Syntax:**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Beschreibung:**  
Die NumFromDate-Funktion gibt ein Datum im AD-Datumsformat zurück.

**Syntax:**  
`num NumFromDate(dt value)`

**Beispiel:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Gibt „129699324000000000“ zurück.

---
### <a name="padleft"></a>PadLeft
**Beschreibung:**  
Die PadLeft-Funktion füllt eine Zeichenfolge nach links bis zu einer bestimmten Länge mit einem bestimmten Auffüllzeichen auf.

**Syntax:**  
`str PadLeft(str string, num length, str padCharacter)`

* string: Die aufzufüllende Zeichenfolge.
* length: Eine ganze Zahl, die die gewünschte Länge der Zeichenfolge angibt.
* padCharacter: Eine Zeichenfolge, die aus einem einzelnen Zeichen besteht, das als Auffüllzeichen verwendet werden soll

**Hinweise:**

* Wenn die Länge der Zeichenfolge "length" unterschreitet, wird "padCharacter" wiederholt an den Anfang der Zeichenfolge (links) gesetzt, bis ihre Länge "length" entspricht.
* "padCharacter" kann ein Leerzeichen sein, jedoch kein Nullwert.
* Wenn die Länge der Zeichenfolge gleich oder größer als "length" ist, wird die Zeichenfolge unverändert zurückgegeben.
* Wenn die Länge der Zeichenfolge größer als oder gleich "length" ist, wird eine zur Zeichenfolge identische Zeichenfolge zurückgegeben.
* Wenn die Länge der Zeichenfolge kleiner als "length" ist, wird eine neue Zeichenfolge in gewünschter Länge zurückgegeben, die die mit "padCharacter" aufgefüllte Zeichenfolge enthält.
* Wenn die Zeichenfolge einen Nullwert aufweist, gibt die Funktion eine leere Zeichenfolge zurück.

**Beispiel:**  
`PadLeft("User", 10, "0")`  
Gibt „000000User“ zurück.

---
### <a name="padright"></a>PadRight
**Beschreibung:**  
Die PadRight-Funktion füllt eine Zeichenfolge nach rechts bis zu einer bestimmten Länge mit einem bestimmten Auffüllzeichen auf.

**Syntax:**  
`str PadRight(str string, num length, str padCharacter)`

* string: Die aufzufüllende Zeichenfolge.
* length: Eine ganze Zahl, die die gewünschte Länge der Zeichenfolge angibt.
* padCharacter: Eine Zeichenfolge, die aus einem einzelnen Zeichen besteht, das als Auffüllzeichen verwendet werden soll

**Hinweise:**

* Wenn die Länge der Zeichenfolge kleiner als "length" ist, wird "padCharacter" wiederholt an das Ende der Zeichenfolge (rechts) gesetzt, bis ihre Länge "length" entspricht.
* "padCharacter" kann ein Leerzeichen sein, jedoch kein Nullwert.
* Wenn die Länge der Zeichenfolge gleich oder größer als "length" ist, wird die Zeichenfolge unverändert zurückgegeben.
* Wenn die Länge der Zeichenfolge größer als oder gleich "length" ist, wird eine zur Zeichenfolge identische Zeichenfolge zurückgegeben.
* Wenn die Länge der Zeichenfolge kleiner als "length" ist, wird eine neue Zeichenfolge in gewünschter Länge zurückgegeben, die die mit "padCharacter" aufgefüllte Zeichenfolge enthält.
* Wenn die Zeichenfolge einen Nullwert aufweist, gibt die Funktion eine leere Zeichenfolge zurück.

**Beispiel:**  
`PadRight("User", 10, "0")`  
Gibt „User000000“ zurück.

---
### <a name="pcase"></a>PCase
**Beschreibung:**  
Die PCase-Funktion konvertiert das erste Zeichen jedes durch Leerzeichen getrennten Worts in einer Zeichenfolge in einen Großbuchstaben und alle anderen Zeichen in Kleinbuchstaben.

**Syntax:**  
`String PCase(string)`

**Hinweise:**

* Mit dieser Funktion können vollständig aus Großbuchstaben bestehende Wörter (etwa Akronyme) nicht in Wörter mit korrekter Groß-/Kleinschreibung umgewandelt werden.

**Beispiel:**  
`PCase("TEsT")`  
Gibt „test“ zurück.

`PCase(LCase("TEST"))`  
Gibt „Test“ zurück.

---
### <a name="randomnum"></a>RandomNum
**Beschreibung:**  
Die RandomNum-Funktion gibt eine Zufallszahl innerhalb eines bestimmten Intervalls zurück.

**Syntax:**  
`num RandomNum(num start, num end)`

* start: Eine Zahl, die die untere Grenze des zu generierenden Zufallswerts angibt
* end: Eine Zahl, die die obere Grenze des zu generierenden Zufallswerts angibt

**Beispiel:**  
`Random(100,999)`  
Kann „734“ zurückgeben.

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Beschreibung:**  
Die RemoveDuplicates-Funktion stellt sicher, dass in einer übergebenen mehrwertigen Zeichenfolge jeder Wert eindeutig ist.

**Syntax:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Beispiel:**  
`RemoveDuplicates([proxyAddresses])`  
Gibt ein bereinigtes proxyAddress-Attribut zurück, aus dem alle doppelten Werte entfernt wurden.

---
### <a name="replace"></a>Replace
**Beschreibung:**  
Die Replace-Funktion ersetzt alle Vorkommen einer Zeichenfolge durch eine andere Zeichenfolge.

**Syntax:**  
`str Replace(str string, str OldValue, str NewValue)`

* string: Eine Zeichenfolge, in der Werte ersetzt werden sollen
* OldValue: Die Zeichenfolge, die gesucht und ersetzt werden soll
* NewValue: Die Zeichenfolge, die die gesuchte Zeichenfolge ersetzen soll

**Hinweise:**  
Die Funktion erkennt die folgenden speziellen Moniker:

* \n – neue Zeile
* \r – Wagenrücklauf
* \t – Tabulator

**Beispiel:**  
`Replace([address],"\r\n",", ")`  
Ersetzt CRLF durch ein Komma und ein Leerzeichen, was etwa wie folgt aussehen kann: „One Microsoft Way, Redmond, WA, USA“.

---
### <a name="replacechars"></a>ReplaceChars
**Beschreibung:**  
Die ReplaceChars-Funktion ersetzt alle Vorkommen von Zeichen, die in der ReplacePattern-Zeichenfolge gefunden werden.

**Syntax:**  
`str ReplaceChars(str string, str ReplacePattern)`

* string: Eine Zeichenfolge, in der Zeichen ersetzt werden sollen
* ReplacePattern: Eine Zeichenfolge, die ein Wörterbuch mit zu ersetzenden Zeichen enthält

Das Format lautet "{source1}:{target1},{source2}:{target2},{sourceN},{targetN}", wobei "source" das zu findende Zeichen und "target" die Zeichenfolge ist, durch die es ersetzt wird.

**Hinweise:**

* Die Funktion ersetzt alle Vorkommen von definierten Quellen durch die Ziele.
* Die Quelle muss genau ein Zeichen (Unicode) sein.
* Die Quelle kann nicht leer sein oder mehrere Zeichen enthalten (Analysefehler).
* Das Ziel kann mehrere Zeichen enthalten, etwa ö:oe, β:ss.
* Das Ziel kann leer sein, um anzugeben, dass das Zeichen entfernt werden soll.
* Bei der Quelle wird die Groß-/Kleinschreibung beachtet, und es muss eine genaue Übereinstimmung vorliegen.
* Die reservierten Zeichen "," (Komma) und ":" (Doppelpunkt) können mit dieser Funktion nicht ersetzt werden.
* Leerzeichen und andere Whitespaces in der Zeichenfolge "ReplacePattern" werden ignoriert.

**Beispiel:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Gibt „Raksmorgas“ zurück.

`ReplaceChars("O’Neil",%ReplaceString%)`  
Gibt „ONeil“ zurück. (Der Apostroph wird entfernt.)

---
### <a name="right"></a>Right
**Beschreibung:**  
Die Right-Funktion gibt eine bestimmte Anzahl von Zeichen von der rechten Seite einer Zeichenfolge (also vom Ende her) zurück.

**Syntax:**  
`str Right(str string, num NumChars)`

* string: Zeichenfolge, aus der Zeichen zurückgegeben werden
* numChars: Diese Zahl gibt die Anzahl der Zeichen an, die vom Ende der Zeichenfolge (rechts) zurückgeben werden

**Hinweise:**  
Die durch „numChars“ angegebene Anzahl von Zeichen wird ab der letzten Position der Zeichenfolge zurückgegeben.

Eine Zeichenfolge, die die letzten "numChars" Zeichen in der Zeichenfolge enthält:

* Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
* Wenn "numChars" < 0, wird die Eingabezeichenfolge zurückgegeben.
* Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.

Wenn die Zeichenfolge weniger Zeichen enthält als in "numChars" angegeben, wird eine identische Zeichenfolge zurückgegeben.

**Beispiel:**  
`Right("John Doe", 3)`  
Gibt „Doe“ zurück.

---
### <a name="rtrim"></a>RGlätten
**Beschreibung:**  
Die RTrim-Funktion entfernt nachfolgende Leerzeichen aus einer Zeichenfolge.

**Syntax:**  
`str RTrim(str value)`

**Beispiel:**  
`RTrim(" Test ")`  
Gibt „ Test“ zurück.

---
### <a name="select"></a>Select
**Beschreibung:**  
Verarbeitet alle Werte in einem mehrwertigen Attribut (oder der Ausgabe eines Ausdrucks), basierend auf der angegebenen Funktion

**Syntax:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: Stellt ein Element im mehrwertigen Attribut dar
* attribute: Das mehrwertige Attribut
* expression: Ein Ausdruck, der eine Sammlung von Werten zurückgibt
* condition: Jede Funktion, die ein Element im Attribut verarbeiten kann

**Beispiele:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Gibt alle Werte im mehrwertigen Attribut „otherPhone“ zurück, nachdem Bindestriche (-) entfernt wurden

---
### <a name="split"></a>Split
**Beschreibung:**  
Die Split-Funktion nimmt eine durch Trennzeichen getrennte Zeichenfolge entgegen und wandelt sie in eine mehrwertige Zeichenfolge um.

**Syntax:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* value: Die Zeichenfolge mit Trennzeichen
* delimiter: Einzelnes Zeichen, das als Trennzeichen verwendet werden soll
* limit: Maximale Anzahl von Werten, die zurückgegeben werden kann

**Beispiel:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Gibt eine mehrwertige Zeichenfolge mit zwei Elementen zurück, die für das proxyAddress-Attribut nützlich sind.

---
### <a name="stringfromguid"></a>StringFromGuid
**Beschreibung:**  
Die StringFromGuid-Funktion nimmt eine binäre GUID entgegen und konvertiert sie in eine Zeichenfolge.

**Syntax:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Beschreibung:**  
Die StringFromSid-Funktion konvertiert ein Bytearray mit einer enthaltenen Sicherheits-ID in eine Zeichenfolge.

**Syntax:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Schalter
**Beschreibung:**  
Mit der Switch-Funktion wird ein einzelner Wert auf der Grundlage ausgewerteter Bedingungen zurückgegeben.

**Syntax:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: Variantenausdruck, den Sie auswerten möchten
* value: Wert, der zurückgegeben werden soll, wenn der entsprechende Ausdruck True ergibt

**Hinweise:**  
Die Argumentliste der Switch-Funktion setzt sich aus Ausdrucks- und Wertpaaren zusammen. Die Ausdrücke werden von links nach rechts ausgewertet, und der Wert, der mit dem ersten als True ausgewerteten Ausdruck verknüpft ist, wird zurückgegeben. Wenn die Teile nicht richtig paarweise angegeben werden, tritt ein Laufzeitfehler auf.

Wenn z. B. "expr1" True ist, gibt "Switch" "value1" zurück. Wenn "expr-1" False, aber "expr-2" True ist, gibt "Switch" "value-2" zurück usw.

Switch gibt nichts zurück, wenn:

* Keiner der Ausdrücke TRUE ist.
* Der erste als TRUE ausgewertete Ausdruck über einen zugehörigen Wert verfügt, der Null ist.

„Switch“ wertet alle Ausdrücke aus, auch wenn die Funktion nur einen von ihnen zurückgibt. Aus diesem Grund sollten Sie auf unerwünschte Nebeneffekte achten. Wenn z. B. die Berechnung eines beliebigen Ausdrucks zu einer Division durch 0 führt, tritt ein Fehler auf.

„value“ kann auch die Error-Funktion sein. In diesem Fall würde dann eine benutzerdefinierte Zeichenfolge zurückgegeben.

**Beispiel:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Gibt die Sprache zurück, die in einigen Großstädten gesprochen wird. Andernfalls wird ein Fehler zurückgegeben.

---
### <a name="trim"></a>Glätten
**Beschreibung:**  
Die Trim-Funktion entfernt führende und nachfolgende Leerzeichen aus einer Zeichenfolge.

**Syntax:**  
`str Trim(str value)`  

**Beispiel:**  
`Trim(" Test ")`  
Gibt „test“ zurück.

`Trim([proxyAddresses])`  
Entfernt führende und nachfolgende Leerzeichen für jeden Wert im proxyAddress-Attribut.

---
### <a name="ucase"></a>Großbst
**Beschreibung:**  
Die UCase-Funktion konvertiert alle Zeichen in einer Zeichenfolge in Großbuchstaben.

**Syntax:**  
`str UCase(str string)`

**Beispiel:**  
`UCase("TeSt")`  
Gibt „test“ zurück.

---
### <a name="where"></a>Hierbei gilt:

**Beschreibung:**  
Gibt eine Teilmenge von Werten aus einem mehrwertigen Attribut (oder die Ausgabe eines Ausdrucks) zurück, basierend auf der spezifischen Bedingung

**Syntax:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: Stellt ein Element im mehrwertigen Attribut dar
* attribute: Das mehrwertige Attribut
* condition: Ein beliebiger Ausdruck, der als TRUE oder FALSE ausgewertet werden kann
* expression: Ein Ausdruck, der eine Sammlung von Werten zurückgibt

**Beispiel:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Gibt die Zertifikatwerte im mehrwertigen Attribut „userCertificate“ zurück, die nicht abgelaufen sind

---
### <a name="with"></a>With
**Beschreibung:**  
Die With-Funktion stellt eine Möglichkeit zum Vereinfachen eines komplexen Ausdrucks mithilfe einer Variablen bereit. Dabei wird ein Teilausdruck dargestellt, der mindestens im komplexen Ausdruck angezeigt wird.

**Syntax:** 
`With(var variable, exp subExpression, exp complexExpression)`  
* variable: Stellt den Teilausdruck dar
* subExpression: Der durch eine Variable dargestellte Teilausdruck
* complexExpression: Ein komplexer Ausdruck

**Beispiel:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Ist funktionell gleichwertig mit:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Hiermit werden nur nicht abgelaufene Zertifikatwerte aus dem Attribut „userCertificate“ zurückgegeben.


---
### <a name="word"></a>Wort
**Beschreibung:**  
Die Word-Funktion gibt ein in einer Zeichenfolge enthaltenes Wort auf der Grundlage von Parametern zurück, die die zu verwendenden Trennzeichen und die Nummer des zurückzugebenden Worts beschreiben.

**Syntax:**  
`str Word(str string, num WordNumber, str delimiters)`

* string: Zeichenfolge, aus der ein Wort zurückgegeben wird
* WordNumber: Eine Zahl, die die Nummer des zurückzugebenden Worts angibt
* delimiters: Eine Zeichenfolge, die das/die Trennzeichen angibt, das/die verwendet werden soll(en), um Wörter zu identifizieren

**Hinweise:**  
Alle Folgen von Zeichen in einer Zeichenfolge, die durch eines der in „delimiters“ enthaltenen Zeichen getrennt werden, werden als Wörter behandelt:

* Wenn "WordNumber" < 1, wird eine leere Zeichenfolge zurückgegeben
* Wenn "string" einen Nullwert hat, wird eine leere Zeichenfolge zurückgegeben

Falls „string“ weniger Wörter enthält als für „WordNumber“ angegeben, wird eine leere Zeichenfolge zurückgegeben. Dies gilt auch, wenn „string“ keine durch Trennzeichen identifizierten Wörter enthält.

**Beispiel:**  
`Word("The quick brown fox",3," ")`  
Gibt „brown“ zurück.

`Word("This,string!has&many separators",3,",!&#")`  
Gibt „has“ zurück.

## <a name="additional-resources"></a>Weitere Ressourcen
* [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)