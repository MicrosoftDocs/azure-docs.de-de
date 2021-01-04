---
title: Referenz zum Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory
description: Erfahren Sie, wie Ausdruckszuordnungen verwendet werden können, um Attributwerte während der automatisierten Bereitstellung von SaaS-App-Objekten in Azure Active Directory in ein akzeptables Format zu transformieren. Enthält eine Referenzliste mit Funktionen
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 02/05/2020
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: fe18513bbfc69fb63f8b1b248b7cf02ad55b467d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027372"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>Referenz zum Schreiben von Ausdrücken für Attributzuordnungen in Azure AD

Wenn Sie die Bereitstellung für eine SaaS-Anwendung konfigurieren, ist einer der Attributzuordnungstypen, die Sie angeben können, eine Ausdruckszuordnung. Für diese müssen Sie einen skriptartigen Ausdruck schreiben, mit dem Sie die Daten Ihrer Benutzer in Formate umwandeln können, die für die SaaS-Anwendung einfacher zu akzeptieren sind.

## <a name="syntax-overview"></a>Syntaxübersicht

Die Syntax für die Ausdrücke für Attributzuordnungen ist den Funktionen von Visual Basic for Applications (VBA) ähnlich.

* Der gesamte Ausdruck muss mittels Funktionen definiert werden, die aus einem Namen mit darauffolgenden Argumenten in Klammern bestehen:  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* Sie können Funktionen ineinander verschachteln. Beispiel:  *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Sie können drei verschiedene Argumententypen an die Funktionen übergeben:
  
  1. Attribute, die in eckige Klammern eingeschlossen werden müssen. Beispiel: [Attributname]
  2. Zeichenfolgenkonstanten, die in doppelte Anführungszeichen eingeschlossen werden müssen. Beispiel: „USA“
  3. Andere Funktionen Beispiel: FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Bei Zeichenfolgenkonstanten, in denen ein umgekehrter Schrägstrich ( \ ) oder ein Anführungszeichen ( " ) benötigt wird, muss dieser bzw. dieses mit einem umgekehrten Schrägstrichsymbol ( \ ) versehen werden. Beispiel: „Unternehmensname: \\"Contoso\\""
* Bei der Syntax muss die Groß- und Kleinschreibung beachtet werden – ein wichtiger Punkt, wenn Sie die Funktionen nicht direkt von hier kopieren, sondern als Zeichenfolgen eingeben. 

## <a name="list-of-functions"></a>Liste der Funktionen

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp;[InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Anfügen

**Funktion:** Append(Quelle, Suffix)

**Beschreibung:** Fügt das Suffix am Ende eines angegebenen Quellzeichenfolgenwerts an.

**Parameter:**

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **Suffix** |Erforderlich |String |Die Zeichenfolge, die Sie am Ende des Quellwerts anfügen möchten |

---
### <a name="bitand"></a>BitAnd
**Funktion:** BitAnd(value1, value2)

**Beschreibung:**  Diese Funktion konvertiert beide Parameter in die binäre Darstellung und legt ein Bit auf Folgendes fest:

- 0 – wenn mindestens eines der entsprechenden Bits in „value1“ und „value2“ den Wert „0“ aufweist
- 1 – wenn beide entsprechenden Bits 1 sind.

Anders gesagt: sie gibt in allen Fällen 0 zurück, außer wenn die entsprechenden Bits beider Parameter 1 sind.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value1** |Erforderlich |num |Numerischer Wert, der mit „value2“ mit AND verarbeitet werden soll|
| **value2** |Erforderlich |num |Numerischer Wert, der mit „value1“ mit AND verarbeitet werden soll|

**Beispiel:** 
`BitAnd(&HF, &HF7)`

11110111 AND 00000111 = 00000111, daher gibt `BitAnd` „7“ zurück, den Binärwert von 00000111.

---
### <a name="cbool"></a>ZBool
**Funktion:**  
`CBool(Expression)`

**Beschreibung:**  
`CBool` gibt einen booleschen Wert zurück, der auf dem ausgewerteten Ausdruck basiert. Wenn die Auswertung des Ausdrucks einen Wert ungleich 0 (null) ergibt, gibt `CBool` *True* zurück. Andernfalls wird *False* zurückgegeben.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **expression** |Erforderlich | expression | Ein beliebiger gültiger Ausdruck |

**Beispiel:** 
`CBool([attribute1] = [attribute2])`                                                                    
Gibt True zurück, wenn beide Attribute den gleichen Wert haben.

---
### <a name="coalesce"></a>Coalesce
**Funktion:** Coalesce(source1, source2, ..., defaultValue)

**Beschreibung:** Gibt den ersten Quellwert zurück, der nicht NULL ist. Wenn alle Argumente NULL sind und „defaultValue“ vorhanden ist, wird der Standardwert (defaultValue) zurückgegeben. Wenn alle Argumente NULL sind und „defaultValue“ nicht vorhanden ist, gibt Coalesce NULL zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **source1 … sourceN** | Erforderlich | String |Erforderlich, unterschiedlich oft. Normalerweise der Name des Attributs aus dem Quellobjekt |
| **defaultValue** | Optional | String | Der zu verwendende Standardwert, wenn alle Quellwerte NULL sind. Kann eine leere Zeichenfolge ("") sein.

---
### <a name="converttobase64"></a>ConvertToBase64
**Funktion:** ConvertToBase64(source)

**Beschreibung:**  Die ConvertToBase64-Funktion konvertiert eine Zeichenfolge in eine Unicode-Base64-Zeichenfolge.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Eine in Base64 zu konvertierende Zeichenfolge|

**Beispiel:** 
`ConvertToBase64("Hello world!")`

Gibt „SABlAGwAbABvACAAdwBvAHIAbABkACEA“ zurück.

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funktion:** ConvertToUTF8Hex(source)

**Beschreibung:**  Die ConvertToUTF8Hex-Funktion konvertiert eine Zeichenfolge in einen hexadezimal-codierten UTF8-Wert.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Eine in UTF-8 (Hex) zu konvertierende Zeichenfolge|

**Beispiel:** 
`ConvertToUTF8Hex("Hello world!")`

Gibt „48656C6C6F20776F726C6421“ zurück.

---
### <a name="count"></a>Anzahl
**Funktion:** Count(attribute)

**Beschreibung:**  Die Count-Funktion gibt die Anzahl von Elementen in einem mehrwertigen Attribut zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **attribute** |Erforderlich |Attribut |Mehrwertiges Attribut, aus dem Elemente gezählt werden|

---
### <a name="cstr"></a>CStr
**Funktion:** CStr(value)

**Beschreibung:** Die CStr-Funktion konvertiert einen Wert in einen Zeichenfolgendatentyp.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value** |Erforderlich | Numerischer Wert, Verweisattribut oder boolescher Wert | Kann ein numerischer Wert, ein Verweisattribut oder ein boolescher Wert sein. |

**Beispiel:** 
`CStr([dn])`

Gibt „cn=Joe,dc=contoso,dc=com“ zurück

---
### <a name="datefromnum"></a>DateFromNum
**Funktion:** DateFromNum(value)

**Beschreibung:**  Die DateFromNum-Funktion konvertiert einen Wert im AD-Datumsformat in einen DateTime-Typ.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value** |Erforderlich | Date | AD-Datum, das in den DateTime-Typ konvertiert werden soll |

**Beispiel:** 
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

Gibt einen DateTime-Wert zurück, der für den 1. Januar 2012 um 23:00 Uhr steht.

---
### <a name="formatdatetime"></a>FormatDatumZeit
**Funktion:** FormatDateTime(Quelle, Eingabeformat, Ausgabeformat)

**Beschreibung:** Konvertiert eine Datumszeichenfolge aus einem Format in ein anderes Format.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **Eingabeformat** |Erforderlich |String |Erwartetes Format des Quellwerts. Unterstützte Formate sind unter [/dotnet/standard/base-types/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings) aufgeführt. |
| **Ausgabeformat** |Erforderlich |String |Format des Ausgabedatums. |

---
### <a name="guid"></a>Guid
**Funktion:** Guid()

**Beschreibung:** Die Guid-Funktion generiert eine neue GUID nach dem Zufallsprinzip.

---
### <a name="iif"></a>IIF
**Funktion:** IIF(condition,valueIfTrue,valueIfFalse)

**Beschreibung:**  Die IIF-Funktion gibt basierend auf einer angegebenen Bedingung einen Wert aus einem Satz möglicher Werte zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **condition** |Erforderlich |Variable oder Ausdruck |Ein Wert oder Ausdruck, der als TRUE oder FALSE ausgewertet werden kann. |
| **valueIfTrue** |Erforderlich |Variable oder Zeichenfolge | Der zurückgegebene Wert, wenn die Bedingung als „True“ ausgewertet wird. |
| **valueIfFalse** |Erforderlich |Variable oder Zeichenfolge |Der zurückgegebene Wert, wenn die Bedingung als „False“ ausgewertet wird.|

**Beispiel:** 
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**Funktion:** InStr(value1,value2,start,compareType)

**Beschreibung:**  Die InStr-Funktion sucht nach dem ersten Vorkommen einer Teilzeichenfolge in einer Zeichenfolge.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value1** |Erforderlich |String |Zu durchsuchende Zeichenfolge |
| **value2** |Erforderlich |String |Zu suchende Zeichenfolge |
| **start** |Optional |Integer |Startposition für die Suche nach der Teilzeichenfolge|
| **compareType** |Optional |Enum |Kann „vbTextCompare“ oder „vbBinaryCompare“ sein |

**Beispiel:** 
`InStr("The quick brown fox","quick")`

Wird als 5 ausgewertet

`InStr("repEated","e",3,vbBinaryCompare)`

Wird als 7 ausgewertet.

---
### <a name="isnull"></a>IsNull
**Funktion:** IsNull(Expression)

**Beschreibung:**  Die IsNull-Funktion gibt „True“ zurück, wenn die Auswertung des Ausdrucks einen Nullwert ergibt. Für ein Attribut wird ein Nullwert durch die Abwesenheit des Attributs ausgedrückt.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **expression** |Erforderlich |expression |Der auszuwertende Ausdruck |

**Beispiel:** 
`IsNull([displayName])`

Gibt „True“ zurück, wenn das Attribut nicht vorhanden ist

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funktion:** IsNullOrEmpty(Expression)

**Beschreibung:**  Die IsNullOrEmpty-Funktion gibt „True“ zurück, wenn der Ausdruck einem Nullwert oder einer leeren Zeichenfolge entspricht. Wird für ein Attribut als „True“ ausgewertet, wenn das Attribut nicht vorhanden oder zwar vorhanden, aber eine leere Zeichenfolge ist.
Die Umkehrung dieser Funktion heißt "IsPresent".

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **expression** |Erforderlich |expression |Der auszuwertende Ausdruck |

**Beispiel:** 
`IsNullOrEmpty([displayName])`

Gibt „True“ zurück, wenn das Attribut nicht vorhanden ist oder eine leere Zeichenfolge darstellt

---
### <a name="ispresent"></a>IsPresent
**Funktion:** IsPresent (Ausdruck)

**Beschreibung:**  Die IsPresent-Funktion gibt „True“ zurück, wenn der Ausdruck zu einer Zeichenfolge ausgewertet wird, die kein Nullwert und nicht leer ist. Die umgekehrte Funktion heißt „IsNullOrEmpty“.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **expression** |Erforderlich |expression |Der auszuwertende Ausdruck |

**Beispiel:** 
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**Funktion:** IsString(Expression)

**Beschreibung:**  Die IsString-Funktion wird als „True“ ausgewertet, wenn der Ausdruck als Zeichenfolgentyp ausgewertet werden kann.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **expression** |Erforderlich |expression |Der auszuwertende Ausdruck |

---
### <a name="item"></a>Element
**Funktion:** Item(attribute, index)

**Beschreibung:**  Die Item-Funktion gibt ein Element aus einer mehrwertigen Zeichenfolge oder einem mehrwertigen Attribut zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **attribute** |Erforderlich |attribute |Das zu durchsuchende mehrwertige Attribut |
| **Index** |Erforderlich |Integer | Index für ein Element in der mehrwertigen Zeichenfolge|

**Beispiel:** 
`Item([proxyAddresses], 1)`

---
### <a name="join"></a>Join
**Funktion:**  Join(Trennzeichen, Quelle1, Quelle2, …)

**Beschreibung:** „Join()“ ist vergleichbar mit „Append()“, kann jedoch mehrere **Quellzeichenfolgenwerte** in einer einzelnen Zeichenfolge kombinieren, wobei die Werte jeweils durch eine **Trennzeichenfolge** getrennt werden.

Wenn einer der Quellwerte ein mehrwertiges Attribut ist, werden die einzelnen Werte in diesem Attribut miteinander verknüpft und dabei durch den Trennzeichenwert getrennt.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Trennzeichen** |Erforderlich |String |Zeichenfolge, die zur Trennung von Quellwerten verwendet wird, wenn diese zu einer einzelnen Zeichenfolge zusammengesetzt werden. Kann "" sein, wenn kein Trennzeichen erforderlich ist. |
| **source1 … sourceN** |Erforderlich, unterschiedlich oft |String |Zeichenfolgenwerte, die zusammengesetzt werden sollen. |

---
### <a name="left"></a>Left
**Funktion:** Left(String,NumChars)

**Beschreibung:**  Die Left-Funktion gibt eine angegebene Anzahl von Zeichen von der linken Seite einer Zeichenfolge zurück. Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
Wenn "numChars" < 0, wird die Eingabezeichenfolge zurückgegeben.
Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.
Wenn die Zeichenfolge weniger Zeichen enthält als in „numChars“ angegeben, wird eine identische Zeichenfolge (also eine Zeichenfolge, die alle in Parameter 1 enthaltenen Zeichen enthält) zurückgegeben.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **String** |Erforderlich |attribute | Die Zeichenfolge, aus der Zeichen zurückgegeben werden sollen |
| **NumChars** |Erforderlich |Integer | Eine Zahl, die die Anzahl von Zeichen angibt, die ab dem Anfang der Zeichenfolge (links) zurückgegeben werden sollen|

**Beispiel:** 
`Left("John Doe", 3)`

Gibt „Joh“ zurück.

---
### <a name="mid"></a>Mid
**Funktion:**  Mid(Quelle, Start, Länge)

**Beschreibung:**  Gibt eine Teilzeichenfolge des Quellwerts zurück. Eine Teilzeichenfolge ist eine Zeichenfolge, die nur einige der Zeichen aus der Quellzeichenfolge enthält.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs. |
| **start** |Erforderlich |integer |Index in der **Quellzeichenfolge** , an dem die Teilzeichenfolge beginnen soll. Das erstes Zeichen in der Zeichenfolge hat den Index 1, das zweite Zeichen hat den Index 2 usw. |
| **length** |Erforderlich |integer |Die Länge der Teilzeichenfolge. Wenn die Länge außerhalb der **Quellzeichenfolge** endet, gibt die Funktion die Teilzeichenfolge zwischen **Startindex** und dem Ende der **Quellzeichenfolge** zurück. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funktion:** NormalizeDiacritics(Quelle)

**Beschreibung:** Erfordert ein einzelnes Zeichenfolgenargument. Gibt die Zeichenfolge zurück und ersetzt sämtliche diakritische Zeichen durch entsprechende nicht diakritische Zeichen. Dient in der Regel dazu, Vor- und Nachnamen mit diakritischen Zeichen (Akzenten) in zulässige Werte zu konvertieren, die in verschiedenen Benutzer-IDs verwendet werden können (etwa in Benutzerprinzipalnamen, SAM-Kontonamen und E-Mail-Adressen).

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String | In der Regel ein Attribut für einen Vor- oder Nachnamen. |

---
### <a name="not"></a>Not
**Funktion:**  Not(Quelle)

**Beschreibung:** Kehrt den booleschen Wert der **Quelle** um. Lautet der **Quellwert** also „True“, gibt die Funktion „False“ zurück. Andernfalls gibt sie „True“ zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Boolesche Zeichenfolge |Die erwarteten **Quellwerte** sind TRUE oder FALSE. |

---
### <a name="numfromdate"></a>NumFromDate
**Funktion:** NumFromDate (Wert)

**Beschreibung:** Die NumFromDate-Funktion konvertiert einen „DateTime“-Wert in das Active Directory-Format, das zum Festlegen von Attributen wie [accountExpires](/windows/win32/adschema/a-accountexpires) erforderlich ist. Verwenden Sie diese Funktion zum Konvertieren von „DateTime“-Werten, die von HR-Cloud-Apps wie Workday und SuccessFactors empfangen werden, in die entsprechende Azure AD-Darstellung. 

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value** |Erforderlich | String | Zeichenfolge mit Datum und Uhrzeit im unterstützten Format. Unterstützte Formate finden Sie unter https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Beispiel:**
* Beispiel für Workday: Wenn Sie das Attribut *ContractEndDate* von Workday im Format *2020-12-31-08:00* dem Feld *accountExpires* in AD zuordnen möchten, erfahren Sie hier, wie Sie diese Funktion verwenden und den Zeitzonenoffset so ändern, dass er Ihrem Gebietsschema entspricht. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Beispiel für SuccessFactors: Wenn Sie das Attribut *endDate* von SuccessFactors im Format *M/d/yyyy hh:mm:ss tt* dem Feld *accountExpires* in AD zuordnen möchten, erfahren Sie hier, wie Sie diese Funktion verwenden und den Zeitzonenoffset so ändern, dass er Ihrem Gebietsschema entspricht.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Funktion:** RemoveDuplicates(attribute)

**Beschreibung:**  Die RemoveDuplicates-Funktion stellt sicher, dass in einer übergebenen mehrwertigen Zeichenfolge jeder Wert eindeutig ist.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **attribute** |Erforderlich |Mehrwertiges Attribut |Mehrwertiges Attribut, aus dem Duplikate entfernt werden|

**Beispiel:** 
`RemoveDuplicates([proxyAddresses])`  Gibt ein bereinigtes proxyAddress-Attribut zurück, aus dem alle doppelten Werte entfernt wurden.

---
### <a name="replace"></a>Replace
**Funktion:** Replace(Quelle, AlterWert, RegexMuster, RegexGruppenname, Ersatzwert, Ersatzattributname, Vorlage)

**Beschreibung:** Hiermit werden Werte in einer Zeichenfolge unter Beachtung der Groß-/Kleinschreibung ersetzt. Die Funktion weist ein anderes Verhalten auf, je nachdem, welche Parameter angegeben werden:

* Bei Angabe von **AlterWert** und **Ersatzwert**:
  
  * Ersetzt alle Vorkommen von **AlterWert** in der **Quelle** durch **Ersatzwert**.
* Bei Angabe von **AlterWert** und **Vorlage**:
  
  * Ersetzt alle Vorkommen von **AlterWert** in **Vorlage** durch den **Quellwert**).
* Bei Angabe von **RegexMuster** und **Ersatzwert**:

  * Die Funktion wendet das **RegexMuster** auf die Zeichenfolge in **Quelle** an, und Sie können die RegexGruppennamen zum Erstellen der Zeichenfolgen für **Ersatzwert** verwenden.
* Bei Angabe von **RegexMuster**, **RegexGruppenname** und **Ersatzwert**:
  
  * Die Funktion wendet das **RegexMuster** auf die **Quellzeichenfolge** an und ersetzt alle mit **RegexGruppenname** übereinstimmenden Werte durch **Ersatzwert**.
* Bei Angabe von **RegexMuster**, **RegexGruppenname** und **Ersatzattributname**:
  
  * Falls kein Wert für **Quelle** vorhanden ist, wird **Quelle** zurückgegeben.
  * Wenn **Quelle** einen Wert umfasst, wendet die Funktion das **RegexMuster** auf die Zeichenfolge in **Quelle** an und ersetzt alle mit **RegexGruppenname** übereinstimmenden Werte durch den Wert, der **Ersatzattributname** zugeordnet ist.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Objekt **Quelle**. |
| **AlterWert** |Optional |String |Wert, der in **Quelle** oder **Vorlage** ersetzt werden soll. |
| **RegexMuster** |Optional |String |Regex-Muster für den Wert, der in der **Quelle** ersetzt wird. Oder, bei Verwendung von **Ersatzeigenschaftsname**, das Muster, das zum Extrahieren des Werts aus **Ersatzeigenschaftsname** verwendet wird. |
| **RegexGruppenname** |Optional |String |Name der Gruppe im **RegexMuster**. Nur bei Verwendung von **Ersatzeigenschaftsname** wird der Wert dieser Gruppe als **Ersatzwert** aus **Ersatzeigenschaftsname** extrahiert. |
| **Ersatzwert** |Optional |String |Neuer Wert, durch den der alte Wert ersetzt wird. |
| **Ersatzattributname** |Optional |String |Name des Attributs, das als Ersatzwert verwendet werden soll. |
| **Vorlage** |Optional |String |Bei Angabe des Werts **Vorlage** wird **AlterWert** in der Vorlage gesucht und durch den Wert von **Quelle** ersetzt. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funktion:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**Beschreibung:** Es sind mindestens zwei Argumente erforderlich, bei denen es sich um eindeutige Regeln für die Generierung von Werten handelt, die mit Ausdrücken definiert werden. Mit der Funktion wird jede Regel ausgewertet. Anschließend wird der Wert überprüft, der generiert wurde, um die Eindeutigkeit in der Ziel-App bzw. im Zielverzeichnis sicherzustellen. Der erste eindeutige Wert, der gefunden wird, wird zurückgegeben. Wenn alle Werte am Ziel bereits vorhanden sind, wird der Eintrag hinterlegt, und die Ursache wird in den Überwachungsprotokollen protokolliert. Für die Anzahl von Argumenten, die bereitgestellt werden können, gilt keine Obergrenze.


 - Dies ist eine Funktion der obersten Ebene, die nicht geschachtelt werden kann.
 - Diese Funktion kann nicht auf Attribute angewandt werden, die über eine Rangfolge für den Abgleich verfügen.   
 - Diese Funktion darf nur für die Erstellung von Einträgen verwendet werden. Legen Sie die Eigenschaft **Zuordnung anwenden** auf **Nur beim Erstellen von Objekten** fest.
 - Diese Funktion wird derzeit nur für die „Benutzerbereitstellung von Workday in Active Directory“ unterstützt. Sie kann nicht für andere Bereitstellungsanwendungen verwendet werden. 


**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |Mindestens zwei erforderlich, keine Obergrenze |String | Liste mit auszuwertenden Regeln für die Generierung eindeutiger Werte |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktion:** SingleAppRoleAssignment([appRoleAssignments])

**Beschreibung:** Gibt eine einzelne appRoleAssignment aus der Liste aller appRoleAssignments zurück, die einem Benutzer für eine bestimmte Anwendung zugewiesen sind. Diese Funktion ist erforderlich, um das appRoleAssignments-Objekt in eine einzelne Namenszeichenfolge für eine Rolle zu konvertieren. Beachten Sie, dass die bewährte Methode darin besteht, sicherzustellen, dass einem Benutzer nur jeweils eine appRoleAssignment zugewiesen ist. Wenn mehrere Rollen zugewiesen sind, ist die zurückgegebene Zeichenfolge für die Rolle möglicherweise nicht vorhersehbar. 

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Erforderlich |String |**[appRoleAssignments]** -Objekt |

---
### <a name="split"></a>Split
**Funktion:** Split(Quelle, Trennzeichen)

**Beschreibung:** Unterteilt eine Zeichenfolge mithilfe des angegebenen Trennzeichens in ein mehrwertiges Array.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |**Quelle** , der aktualisiert werden soll. |
| **Trennzeichen** |Erforderlich |String |Gibt das Zeichen zum Aufteilen der Zeichenfolge an (Beispiel: „,“). |

---
### <a name="stripspaces"></a>StripSpaces
**Funktion:**  StripSpaces(Quelle)

**Beschreibung:**  Entfernt alle Leerzeichen (" ") aus der Quellzeichenfolge.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |**Quelle** , der aktualisiert werden soll. |

---
### <a name="switch"></a>Schalter
**Funktion:**  Switch(Quelle, Standardwert, Schlüssel1, Wert1, Schlüssel2, Wert2, …)

**Beschreibung:** Wenn der **Quellwert** einem **Schlüssel** entspricht, wird der **Wert** für diesen **Schlüssel** zurückgegeben. Wenn der **Quellwert** keinem Schlüssel entspricht, wird der **Standardwert** zurückgegeben.  **Schlüssel-** und **Wertparameter** müssen immer paarweise angegeben werden. Die Funktion erwartet immer eine gerade Anzahl von Parametern. Die Funktion sollte nicht für referenzielle Attribute wie etwa „manager“ verwendet werden. 

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |**Source** , der aktualisiert werden soll. |
| **defaultValue** |Optional |String |Der Standardwert, der verwendet werden soll, wenn die Quelle mit keinem Schlüssel übereinstimmt. Kann eine leere Zeichenfolge ("") sein. |
| **key** |Erforderlich |String |**Schlüssel**, der mit dem **Quellwert** verglichen werden soll. |
| **value** |Erforderlich |String |Der Ersatzwert für die **Quelle** , die mit dem Schlüssel übereinstimmt. |

---
### <a name="tolower"></a>ToLower
**Funktion:** ToLower(source, culture)

**Beschreibung:** Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Kleinbuchstaben. Ohne Angabe der *culture*-Information wird die invariante Kultur verwendet.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **culture** |Optional |String |Das Format für den Kulturnamen lautet basierend auf dem Standard RFC 4646 *languagecode2-country/regioncode2*, wobei *languagecode2* der aus zwei Buchstaben bestehende Sprachcode und *country/regioncode2* der aus zwei Buchstaben bestehende Subkulturcode ist. Beispiele sind „ja-JP“ für Japanisch (Japan) und „en-US“ für Englisch (USA). In Fällen, in denen kein aus zwei Buchstaben bestehender Sprachcode verfügbar ist, wird ein aus drei Buchstaben bestehender, von ISO 639-2 abgeleiteter Code verwendet.|

---
### <a name="toupper"></a>ToUpper
**Funktion:** ToUpper(source, culture)

**Beschreibung:** Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Großbuchstaben. Ohne Angabe der *culture*-Information wird die invariante Kultur verwendet.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **culture** |Optional |String |Das Format für den Kulturnamen lautet basierend auf dem Standard RFC 4646 *languagecode2-country/regioncode2*, wobei *languagecode2* der aus zwei Buchstaben bestehende Sprachcode und *country/regioncode2* der aus zwei Buchstaben bestehende Subkulturcode ist. Beispiele sind „ja-JP“ für Japanisch (Japan) und „en-US“ für Englisch (USA). In Fällen, in denen kein aus zwei Buchstaben bestehender Sprachcode verfügbar ist, wird ein aus drei Buchstaben bestehender, von ISO 639-2 abgeleiteter Code verwendet.|

---
### <a name="word"></a>Wort
**Funktion:** Word(String,WordNumber,Delimiters)

**Beschreibung:**  Die Word-Funktion gibt ein in einer Zeichenfolge enthaltenes Wort auf der Grundlage von Parametern zurück, die die zu verwendenden Trennzeichen und die Nummer des zurückzugebenden Worts beschreiben. Alle Folgen von Zeichen in einer Zeichenfolge, die durch eines der in „delimiters“ enthaltenen Zeichen getrennt werden, werden als Wörter behandelt:

Wenn "WordNumber" < 1, wird eine leere Zeichenfolge zurückgegeben
Wenn "string" einen Nullwert hat, wird eine leere Zeichenfolge zurückgegeben
Falls „string“ weniger Wörter enthält als für „WordNumber“ angegeben, wird eine leere Zeichenfolge zurückgegeben. Dies gilt auch, wenn „string“ keine durch Trennzeichen identifizierten Wörter enthält.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **String** |Erforderlich |Mehrwertiges Attribut |Eine Zeichenfolge, aus der ein Wort zurückgegeben werden soll.|
| **WordNumber** |Erforderlich | Integer | Eine Zahl, die angibt, welche Wortnummer zurückgegeben werden soll|
| **delimiters** |Erforderlich |String| Eine Zeichenfolge, die das/die Trennzeichen darstellt, das/die zum Identifizieren von Wörtern verwendet werden soll(en)|

**Beispiel:** 
`Word("The quick brown fox",3," ")`

Gibt „brown“ zurück

`Word("This,string!has&many separators",3,",!&#")`

Gibt „has“ zurück

---

## <a name="examples"></a>Beispiele
### <a name="strip-known-domain-name"></a>Entfernen eines bekannten Domänennamens
Sie müssen einen bekannten Domänennamen aus der E-Mail-Adresse eines Benutzers entfernen, um einen Benutzernamen zu erhalten. Wenn die Domäne beispielsweise "contoso.com" lautet, können Sie den folgenden Ausdruck verwenden:

**Ausdruck:**  
`Replace([mail], "@contoso.com", , ,"", ,)`

**Beispieleingabe/-ausgabe:** 

* **EINGABE** (mail): "john.doe@contoso.com"
* **AUSGABE**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Anfügen eines konstanten Suffixes an einen Benutzernamen
Wenn Sie eine Salesforce Sandbox verwenden, müssen Sie möglicherweise ein weiteres Suffix an alle Benutzernamen anfügen, bevor Sie diese synchronisieren.

**Ausdruck:**  
`Append([userPrincipalName], ".test")`

**Beispieleingabe/-ausgabe:** 

* **EINGABE**: (userPrincipalName): "John.Doe@contoso.com"
* **AUSGABE**:  "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generieren eines Benutzeralias durch Verketten von Teilen des Vor- und Nachnamens
Sie müssen einen Benutzeralias generieren, indem Sie die ersten drei Buchstaben des Vornamens und die ersten fünf Buchstaben des Nachnamens des Benutzers verwenden.

**Ausdruck:**  
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Beispieleingabe/-ausgabe:** 

* **EINGABE** (givenName): „John“
* **EINGABE** (surname): „Doe“
* **AUSGABE**:  „JohDoe“

### <a name="remove-diacritics-from-a-string"></a>Entfernen diakritischer Zeichen aus einer Zeichenfolge
Sie müssen Zeichen mit Akzent durch entsprechende Zeichen ohne Akzent ersetzen.

**Ausdruck:** NormalizeDiacritics([givenName])

**Beispieleingabe/-ausgabe:** 

* **EINGABE** (givenName): „Zoë“
* **AUSGABE**:  „Zoe“

### <a name="split-a-string-into-a-multi-valued-array"></a>Unterteilen einer Zeichenfolge in ein mehrwertiges Array
Sie müssen eine durch Trennzeichen getrennte Liste von Zeichenfolgen in ein Array unterteilen, das in ein mehrwertiges Attribut wie das PermissionSets-Attribut von Salesforce eingegeben werden kann. In diesem Beispiel wurde eine Liste von Berechtigungssätzen in extensionAttribute5 in Azure AD eingegeben.

**Ausdruck:** Split([extensionAttribute5], ",")

**Beispieleingabe/-ausgabe:** 

* **EINGABE:** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **AUSGABE:** ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Ausgabedatum eines Datums als Zeichenfolge in einem bestimmten Format
Sie möchten Datumsangaben in einem bestimmten Format an eine SaaS-Anwendung senden. Beispielsweise möchten Sie Datumsangaben für ServiceNow formatieren.

**Ausdruck:** 

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Beispieleingabe/-ausgabe:**

* **EINGABE** (extensionAttribute1): 20150123105347.1Z
* **AUSGABE**:  2015-01-23

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersetzen eines Werts anhand eines vordefinierten Satzes von Optionen

Sie müssen die Zeitzone des Benutzers anhand des Bundesstaatscodes festlegen, der in Azure AD gespeichert ist. Wenn der Bundesstaatscode keiner der vordefinierten Optionen entspricht, soll der Standardwert "Australien/Sydney" verwendet werden.

**Ausdruck:**  
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Beispieleingabe/-ausgabe:**

* **EINGABE** (state): „QLD“
* **AUSGABE**: „Australien/Brisbane“

### <a name="replace-characters-using-a-regular-expression"></a>Ersetzen von Zeichen mit einem regulären Ausdruck
Sie müssen Zeichen suchen, die mit einem regulären Ausdruck übereinstimmen, und diese entfernen.

**Ausdruck:** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Beispieleingabe/-ausgabe:**

* **EINGABE:** (mailNickname: "john_doe72"
* **AUSGABE**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konvertieren des generierten userPrincipalName-Werts (UPN) in Kleinbuchstaben
Im folgenden Beispiel wird der UPN-Wert durch die Verkettung der Quellfelder PreferredFirstName und PreferredLastName generiert, und die ToLower-Funktion konvertiert alle Zeichen der generierten Zeichenfolge in Kleinbuchstaben. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Beispieleingabe/-ausgabe:**

* **EINGABE** (PreferredFirstName): „John“
* **EINGABE** (PreferredLastName): „Smith“
* **AUSGABE**: „john.smith@contoso.com“

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generieren eines eindeutigen Werts für das Attribut userPrincipalName (UPN)
Basierend auf dem Vornamen, zweiten Vornamen und Nachnamen müssen Sie einen Wert für das UPN-Attribut generieren und die Eindeutigkeit im AD-Zielverzeichnis überprüfen, bevor Sie den Wert dem UPN-Attribut zuweisen.

**Ausdruck:** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Beispieleingabe/-ausgabe:**

* **EINGABE** (PreferredFirstName): „John“
* **EINGABE** (PreferredLastName): „Smith“
* **OUTPUT**: "John.Smith@contoso.com", wenn der UPN-Wert John.Smith@contoso.com nicht bereits im Verzeichnis vorhanden ist
* **OUTPUT**: "J.Smith@contoso.com", wenn der UPN-Wert John.Smith@contoso.com bereits im Verzeichnis vorhanden ist
* **OUTPUT**: "Jo.Smith@contoso.com", wenn die beiden obigen UPN-Werte bereits im Verzeichnis enthalten sind

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>E-Mail-Wert übermitteln, wenn nicht NULL, andernfalls userPrincipalName-Wert übermitteln
Sie möchten das E-Mail-Attribut übermitteln, wenn es vorhanden ist. Andernfalls möchten Sie stattdessen den Wert von „userPrincipalName“ übermitteln.

**Ausdruck:**  
`Coalesce([mail],[userPrincipalName])`

**Beispieleingabe/-ausgabe:** 

* **EINGABE** (mail): NULL
* **EINGABE** (userPrincipalName): "John.Doe@contoso.com"
* **AUSGABE**:  "John.Doe@contoso.com"

## <a name="related-articles"></a>Verwandte Artikel
* [Automatisieren der Bereitstellung/Bereitstellungsaufhebung von Benutzern für SaaS-Apps](../app-provisioning/user-provisioning.md)
* [Anpassen von Attributzuordnungen für die Benutzerbereitstellung](../app-provisioning/customize-application-attributes.md)
* [Bereichsfilter für die Benutzerbereitstellung](define-conditional-rules-for-provisioning-user-accounts.md)
* [Verwenden von SCIM für die automatische Bereitstellung von Benutzern und Gruppen aus Azure Active Directory für Anwendungen](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Kontobereitstellungsbenachrichtigungen](../app-provisioning/user-provisioning.md)
* [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)
