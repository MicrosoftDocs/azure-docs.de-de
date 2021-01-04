---
title: Referenz zu Ausdrücken und Funktionen der Azure AD Connect-Cloudbereitstellung
description: Referenz
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a775ce6b7c560783a22697c5dd92288c5d5b7d4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96343704"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory
Wenn Sie die Cloudbereitstellung konfigurieren, ist einer der Attributzuordnungstypen, die Sie angeben können, eine Ausdruckszuordnung. 

Die Ausdruckszuordnung ermöglicht das Anpassen von Attributen mithilfe eines skriptähnlichen Ausdrucks.  Dadurch können Sie die lokalen Daten in einen neuen oder anderen Wert transformieren.  Beispiel: Sie möchten zwei Attribute zu einem einzelnen Attribut zusammenführen, da dieses einzelne Attribut von einer Ihrer Cloudanwendungen verwendet wird.

Im folgenden Dokument werden die skriptähnlichen Ausdrücke behandelt, die zum Transformieren der Daten verwendet werden.  Dies ist nur ein Teil des Vorgangs.  Als Nächstes müssen Sie diesen Ausdruck verwenden und in eine Webanforderung an Ihren Mandanten einfügen.  Weitere Informationen finden Sie unter [Transformationen](how-to-transformation.md).

## <a name="syntax-overview"></a>Syntaxübersicht
Die Syntax für die Ausdrücke für Attributzuordnungen ist den Funktionen von Visual Basic for Applications (VBA) ähnlich.

* Der gesamte Ausdruck muss mittels Funktionen definiert werden, die aus einem Namen mit darauffolgenden Argumenten in Klammern bestehen: <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* Sie können Funktionen ineinander verschachteln. Beispiel: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Sie können drei verschiedene Argumententypen an die Funktionen übergeben:
  
  1. Attribute, die in eckige Klammern eingeschlossen werden müssen. Beispiel: [Attributname]
  2. Zeichenfolgenkonstanten, die in doppelte Anführungszeichen eingeschlossen werden müssen. Beispiel: „USA“
  3. Andere Funktionen Beispiel: FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Bei Zeichenfolgenkonstanten, in denen ein umgekehrter Schrägstrich ( \ ) oder ein Anführungszeichen ( " ) benötigt wird, muss dieser bzw. dieses mit einem umgekehrten Schrägstrichsymbol ( \ ) versehen werden. Beispiel: „Unternehmensname: \\"Contoso\\""

## <a name="list-of-functions"></a>Liste der Funktionen
| Liste der Funktionen | BESCHREIBUNG |
|-----|----|
|[Append](#append)|Fügt das Suffix am Ende eines angegebenen Quellzeichenfolgenwerts an.|
|[BitAnd](#bitand)|Die BitAnd-Funktion legt angegebene Bits auf einen Wert fest.|
|[CBool](#cbool)|Die CBool-Funktion gibt einen booleschen Wert zurück, der auf dem ausgewerteten Ausdruck basiert.|
|[ConvertFromBase64](#convertfrombase64)|Die ConvertFromBase64-Funktion konvertiert den angegebenen Base64-codierten Wert in eine reguläre Zeichenfolge.|
|[ConvertToBase64](#converttobase64)|Die ConvertToBase64-Funktion konvertiert eine Zeichenfolge in eine Unicode-Base64-Zeichenfolge. |
|[ConvertToUTF8Hex](#converttoutf8hex)|Die ConvertToUTF8Hex-Funktion konvertiert eine Zeichenfolge in einen hexadezimal-codierten UTF8-Wert.|
|[Count](#count)|Die Count-Funktion gibt die Anzahl von Elementen in einem mehrwertigen Attribut zurück.|
|[Cstr](#cstr)|Die CStr-Funktion konvertiert in einen Zeichenfolgendatentyp.|
|[DateFromNum](#datefromnum)|Die DateFromNum-Funktion konvertiert einen Wert im AD-Datumsformat in einen DateTime-Typ.|
|[DNComponent](#dncomponent)|Die DNComponent-Funktion gibt den Wert einer angegebenen DN-Komponente von links beginnend zurück.|
|[Fehler](#error)|Die Error-Funktion wird verwendet, um einen benutzerdefinierten Fehler zurückzugeben.|
|[FormatDateTime](#formatdatetime) |Konvertiert eine Datumszeichenfolge aus einem Format in ein anderes Format.| 
|[GUID](#guid)|Die Guid-Funktion generiert eine neue GUID nach dem Zufallsprinzip.|           
|[IIF](#iif)|Die IIF-Funktion gibt basierend auf einer angegebenen Bedingung einen Wert aus einem Satz möglicher Werte zurück.|
|[InStr](#instr)|Die InStr-Funktion sucht nach dem ersten Vorkommen einer Teilzeichenfolge in einer Zeichenfolge.|
|[IsNull](#isnull)|Die IsNull-Funktion gibt „True“ zurück, wenn die Auswertung des Ausdrucks einen Nullwert ergibt.|
|[IsNullOrEmpty](#isnullorempty)|Die IsNullOrEmpty-Funktion gibt „True“ zurück, wenn der Ausdruck einem Nullwert oder einer leeren Zeichenfolge entspricht.|         
|[IsPresent](#ispresent)|Die IsPresent-Funktion gibt „True“ zurück, wenn der Ausdruck zu einer Zeichenfolge ausgewertet wird, die kein Nullwert und nicht leer ist.|    
|[IsString](#isstring)|Die IsString-Funktion wird als „True“ ausgewertet, wenn der Ausdruck als Zeichenfolgentyp ausgewertet werden kann.|
|[Element](#item)|Die Item-Funktion gibt ein Element aus einer mehrwertigen Zeichenfolge oder einem mehrwertigen Attribut zurück.|
|[Join](#join) |„Join()“ ist vergleichbar mit „Append()“, kann jedoch mehrere **Quellzeichenfolgenwerte** in einer einzelnen Zeichenfolge kombinieren, wobei die Werte jeweils durch eine **Trennzeichenfolge** getrennt werden.| 
|[Left](#left)|Die Left-Funktion gibt eine angegebene Anzahl von Zeichen von der linken Seite einer Zeichenfolge zurück.|
|[Mid](#mid) |Gibt eine Teilzeichenfolge des Quellwerts zurück. Eine Teilzeichenfolge ist eine Zeichenfolge, die nur einige der Zeichen aus der Quellzeichenfolge enthält.|
|[NormalizeDiacritics](#normalizediacritics)|Erfordert ein einzelnes Zeichenfolgenargument. Gibt die Zeichenfolge zurück und ersetzt sämtliche diakritische Zeichen durch entsprechende nicht diakritische Zeichen.|
|[Not](#not) |Kehrt den booleschen Wert der **Quelle** um. Lautet der **Quellwert** also *True*, gibt die Funktion *False* zurück. Andernfalls gibt sie "*True*" zurück.| 
|[RemoveDuplicates](#removeduplicates)|Die RemoveDuplicates-Funktion stellt sicher, dass in einer übergebenen mehrwertigen Zeichenfolge jeder Wert eindeutig ist.| 
|[Replace](#replace) |Ersetzt Werte in einer Zeichenfolge. | 
|[SelectUniqueValue](#selectuniquevalue)|Es sind mindestens zwei Argumente erforderlich, bei denen es sich um eindeutige Regeln für die Generierung von Werten handelt, die mit Ausdrücken definiert werden. Mit der Funktion wird jede Regel ausgewertet. Anschließend wird der Wert überprüft, der generiert wurde, um die Eindeutigkeit in der Ziel-App bzw. im Zielverzeichnis sicherzustellen.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Gibt eine einzelne appRoleAssignment aus der Liste aller appRoleAssignments zurück, die einem Benutzer für eine bestimmte Anwendung zugewiesen sind.| 
|[Split](#split)|Unterteilt eine Zeichenfolge mithilfe des angegebenen Trennzeichens in ein mehrwertiges Array.|
|[StringFromSID](#stringfromsid)|Die StringFromSid-Funktion konvertiert ein Bytearray mit einer enthaltenen Sicherheits-ID in eine Zeichenfolge.| 
|[StripSpaces](#stripspaces) |Entfernt alle Leerzeichen (" ") aus der Quellzeichenfolge.| 
|[Switch](#switch)|Wenn der **Quellwert** einem **Schlüssel** entspricht, wird der **Wert** für diesen **Schlüssel** zurückgegeben. | 
|[ToLower](#tolower)|Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Kleinbuchstaben.| 
|[ToUpper](#toupper)|Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Großbuchstaben.|
|[Trim](#trim)|Die Trim-Funktion entfernt führende und nachfolgende Leerzeichen aus einer Zeichenfolge.|
|[Word](#word)|Die Word-Funktion gibt ein in einer Zeichenfolge enthaltenes Wort auf der Grundlage von Parametern zurück, die die zu verwendenden Trennzeichen und die Nummer des zurückzugebenden Worts beschreiben.|

---
### <a name="append"></a>Anfügen
**Funktion:**<br> Append(Quelle, Suffix)

**Beschreibung:**<br> Fügt das Suffix am Ende eines angegebenen Quellzeichenfolgenwerts an.

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
   | **Suffix** |Erforderlich |String |Die Zeichenfolge, die Sie am Ende des Quellwerts anfügen möchten |

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
 
 `BitAnd(&HF, &HF7)`</br>
 Gibt 7 zurück, da durch die Verknüpfung mit AND die Hexadezimalwerte „F“ und „F7“ zu diesem Wert ausgewertet werden.

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
### <a name="error"></a>Fehler
**Beschreibung:**  
Die Error-Funktion wird verwendet, um einen benutzerdefinierten Fehler zurückzugeben.

**Syntax:**  
`void Error(str ErrorMessage)`

**Beispiel:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Wenn das accountName-Attribut nicht vorhanden ist, wird ein Fehler für das Objekt ausgegeben.

---
### <a name="formatdatetime"></a>FormatDatumZeit
**Funktion:**<br> FormatDateTime(Quelle, Eingabeformat, Ausgabeformat)

**Beschreibung:**<br> Konvertiert eine Datumszeichenfolge aus einem Format in ein anderes Format.

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
   | **Eingabeformat** |Erforderlich |String |Erwartetes Format des Quellwerts. Unterstützte Formate sind unter [/dotnet/standard/base-types/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings) aufgeführt. |
   | **Ausgabeformat** |Erforderlich |String |Format des Ausgabedatums. |

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
### <a name="isstring"></a>IsString
**Beschreibung:**  
Die IsString-Funktion wird als „True“ ausgewertet, wenn der Ausdruck als Zeichenfolgentyp ausgewertet werden kann.

**Syntax:**  
`bool IsString(var expression)`

**Hinweise:**  
Wird verwendet, um zu bestimmen, ob „CStr()“ erfolgreich zum Analysieren des Ausdrucks verwendet werden kann.

---
### <a name="join"></a>Join
**Funktion:**<br> Join(Trennzeichen, Quelle1, Quelle2, …)

**Beschreibung:**<br> „Join()“ ist vergleichbar mit „Append()“, kann jedoch mehrere **Quellzeichenfolgenwerte** in einer einzelnen Zeichenfolge kombinieren, wobei die Werte jeweils durch eine **Trennzeichenfolge** getrennt werden.

Wenn einer der Quellwerte ein mehrwertiges Attribut ist, werden die einzelnen Werte in diesem Attribut miteinander verknüpft und dabei durch den Trennzeichenwert getrennt.

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Trennzeichen** |Erforderlich |String |Zeichenfolge, die zur Trennung von Quellwerten verwendet wird, wenn diese zu einer einzelnen Zeichenfolge zusammengesetzt werden. Kann "" sein, wenn kein Trennzeichen erforderlich ist. |
   | **source1 … sourceN** |Erforderlich, unterschiedlich oft |String |Zeichenfolgenwerte, die zusammengesetzt werden sollen. |

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
Gibt `Joh` zurück.

---
### <a name="mid"></a>Mid
**Funktion:**<br> Mid(Quelle, Start, Länge)

**Beschreibung:**<br> Gibt eine Teilzeichenfolge des Quellwerts zurück. Eine Teilzeichenfolge ist eine Zeichenfolge, die nur einige der Zeichen aus der Quellzeichenfolge enthält.

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs. |
   | **start** |Erforderlich |integer |Index in der **Quellzeichenfolge** , an dem die Teilzeichenfolge beginnen soll. Das erstes Zeichen in der Zeichenfolge hat den Index 1, das zweite Zeichen hat den Index 2 usw. |
   | **length** |Erforderlich |integer |Die Länge der Teilzeichenfolge. Wenn die Länge außerhalb der **Quellzeichenfolge** endet, gibt die Funktion die Teilzeichenfolge zwischen **Startindex** und dem Ende der **Quellzeichenfolge** zurück. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funktion:**<br> NormalizeDiacritics(Quelle)

**Beschreibung:**<br> Erfordert ein einzelnes Zeichenfolgenargument. Gibt die Zeichenfolge zurück und ersetzt sämtliche diakritische Zeichen durch entsprechende nicht diakritische Zeichen. Dient in der Regel dazu, Vor- und Nachnamen mit diakritischen Zeichen (Akzenten) in zulässige Werte zu konvertieren, die in verschiedenen Benutzer-IDs verwendet werden können (etwa in Benutzerprinzipalnamen, SAM-Kontonamen und E-Mail-Adressen).

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Quelle** |Erforderlich |String | In der Regel ein Attribut für einen Vor- oder Nachnamen. |

---
### <a name="not"></a>Not
**Funktion:**<br> Not(Quelle)

**Beschreibung:**<br> Kehrt den booleschen Wert der **Quelle** um. Lautet der **Quellwert** also *True*, gibt die Funktion *False* zurück. Andernfalls gibt sie "*True*" zurück.

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Quelle** |Erforderlich |Boolesche Zeichenfolge |Die erwarteten **Quellwerte** sind TRUE oder FALSE. |

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
**Funktion:**<br> Replace(Quelle, AlterWert, RegexMuster, RegexGruppenname, Ersatzwert, Ersatzattributname, Vorlage)

**Beschreibung:**<br>
Ersetzt Werte in einer Zeichenfolge. Sie funktioniert unterschiedlich, je nachdem, welche Parameter angegeben werden:

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

**Parameter:**<br> 

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
**Funktion:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**Beschreibung:**<br> Es sind mindestens zwei Argumente erforderlich, bei denen es sich um eindeutige Regeln für die Generierung von Werten handelt, die mit Ausdrücken definiert werden. Mit der Funktion wird jede Regel ausgewertet. Anschließend wird der Wert überprüft, der generiert wurde, um die Eindeutigkeit in der Ziel-App bzw. im Zielverzeichnis sicherzustellen. Der erste eindeutige Wert, der gefunden wird, wird zurückgegeben. Wenn alle Werte am Ziel bereits vorhanden sind, wird der Eintrag hinterlegt, und die Ursache wird in den Überwachungsprotokollen protokolliert. Für die Anzahl von Argumenten, die bereitgestellt werden können, gilt keine Obergrenze.

> [!NOTE]
> - Dies ist eine Funktion der obersten Ebene, die nicht geschachtelt werden kann.
> - Diese Funktion kann nicht auf Attribute angewandt werden, die über eine Rangfolge für den Abgleich verfügen.  
> - Diese Funktion darf nur für die Erstellung von Einträgen verwendet werden. Legen Sie die Eigenschaft **Zuordnung anwenden** auf **Nur beim Erstellen von Objekten** fest.
> - Diese Funktion wird derzeit nur für die „Benutzerbereitstellung von Workday in Active Directory“ unterstützt. Sie kann nicht für andere Bereitstellungsanwendungen verwendet werden. 


**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **uniqueValueRule1  … uniqueValueRuleN** |Mindestens zwei erforderlich, keine Obergrenze |String | Liste mit auszuwertenden Regeln für die Generierung eindeutiger Werte |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktion:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beschreibung:**<br> Gibt eine einzelne appRoleAssignment aus der Liste aller appRoleAssignments zurück, die einem Benutzer für eine bestimmte Anwendung zugewiesen sind. Diese Funktion ist erforderlich, um das appRoleAssignments-Objekt in eine einzelne Namenszeichenfolge für eine Rolle zu konvertieren. Beachten Sie, dass die bewährte Methode darin besteht, sicherzustellen, dass einem Benutzer nur jeweils eine appRoleAssignment zugewiesen ist. Wenn mehrere Rollen zugewiesen sind, ist die zurückgegebene Zeichenfolge für die Rolle möglicherweise nicht vorhersehbar. 

**Parameter:**<br> 

  | Name | Erforderlich/wiederholt | type | Notizen |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |Erforderlich |String |**[appRoleAssignments]** -Objekt |

---
### <a name="split"></a>Split
**Funktion:**<br> Split(Quelle, Trennzeichen)

**Beschreibung:**<br> Unterteilt eine Zeichenfolge mithilfe des angegebenen Trennzeichens in ein mehrwertiges Array.

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Quelle** |Erforderlich |String |**Quelle** , der aktualisiert werden soll. |
   | **Trennzeichen** |Erforderlich |String |Gibt das Zeichen zum Aufteilen der Zeichenfolge an (Beispiel: „,“). |

---
### <a name="stringfromsid"></a>StringFromSid
**Beschreibung:**  
Die StringFromSid-Funktion konvertiert ein Bytearray mit einer enthaltenen Sicherheits-ID in eine Zeichenfolge.

**Syntax:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Funktion:**<br> StripSpaces(Quelle)

**Beschreibung:**<br> Entfernt alle Leerzeichen (" ") aus der Quellzeichenfolge.

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Quelle** |Erforderlich |String |**Quelle** , der aktualisiert werden soll. |

---
### <a name="switch"></a>Schalter
**Funktion:**<br> Switch(Quelle, Standardwert, Schlüssel1, Wert1, Schlüssel2, Wert2, …)

**Beschreibung:**<br> Wenn der **Quellwert** einem **Schlüssel** entspricht, wird der **Wert** für diesen **Schlüssel** zurückgegeben. Wenn der **Quellwert** keinem Schlüssel entspricht, wird der **Standardwert** zurückgegeben.  **Schlüssel-** und **Wertparameter** müssen immer paarweise angegeben werden. Die Funktion erwartet immer eine gerade Anzahl von Parametern.

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Quelle** |Erforderlich |String |Der zu überprüfende **Quellwert**. |
   | **defaultValue** |Optional |String |Der Standardwert, der verwendet werden soll, wenn die Quelle mit keinem Schlüssel übereinstimmt. Kann eine leere Zeichenfolge ("") sein. |
   | **key** |Erforderlich |String |**Schlüssel**, der mit dem **Quellwert** verglichen werden soll. |
   | **value** |Erforderlich |String |Der Ersatzwert für die **Quelle** , die mit dem Schlüssel übereinstimmt. |

---
### <a name="tolower"></a>ToLower
**Funktion:**<br> ToLower(source, culture)

**Beschreibung:**<br> Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Kleinbuchstaben. Ohne Angabe der *culture*-Information wird die invariante Kultur verwendet.

**Parameter:**<br> 

   | Name | Erforderlich/wiederholt | type | Notizen |
   | --- | --- | --- | --- |
   | **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
   | **culture** |Optional |String |Das Format für den Kulturnamen lautet basierend auf dem Standard RFC 4646 *languagecode2-country/regioncode2*, wobei *languagecode2* der aus zwei Buchstaben bestehende Sprachcode und *country/regioncode2* der aus zwei Buchstaben bestehende Subkulturcode ist. Beispiele sind „ja-JP“ für Japanisch (Japan) und „en-US“ für Englisch (USA). In Fällen, in denen kein aus zwei Buchstaben bestehender Sprachcode verfügbar ist, wird ein aus drei Buchstaben bestehender, von ISO 639-2 abgeleiteter Code verwendet.|

---

### <a name="toupper"></a>ToUpper
**Funktion:**<br> ToUpper(source, culture)

**Beschreibung:**<br> Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Großbuchstaben. Ohne Angabe der *culture*-Information wird die invariante Kultur verwendet.

**Parameter:**<br> 

  | Name | Erforderlich/wiederholt | type | Notizen |
  | --- | --- | --- | --- |
  | **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
  | **culture** |Optional |String |Das Format für den Kulturnamen lautet basierend auf dem Standard RFC 4646 *languagecode2-country/regioncode2*, wobei *languagecode2* der aus zwei Buchstaben bestehende Sprachcode und *country/regioncode2* der aus zwei Buchstaben bestehende Subkulturcode ist. Beispiele sind „ja-JP“ für Japanisch (Japan) und „en-US“ für Englisch (USA). In Fällen, in denen kein aus zwei Buchstaben bestehender Sprachcode verfügbar ist, wird ein aus drei Buchstaben bestehender, von ISO 639-2 abgeleiteter Code verwendet.|

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

## <a name="examples"></a>Beispiele
### <a name="strip-known-domain-name"></a>Entfernen eines bekannten Domänennamens
Sie müssen einen bekannten Domänennamen aus der E-Mail-Adresse eines Benutzers entfernen, um einen Benutzernamen zu erhalten. <br>
Wenn die Domäne beispielsweise "contoso.com" lautet, können Sie den folgenden Ausdruck verwenden:

**Ausdruck:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Beispieleingabe/-ausgabe:** <br>

* **EINGABE** (mail): "john.doe@contoso.com"
* **AUSGABE**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Anfügen eines konstanten Suffixes an einen Benutzernamen
Wenn Sie eine Salesforce Sandbox verwenden, müssen Sie möglicherweise ein weiteres Suffix an alle Benutzernamen anfügen, bevor Sie diese synchronisieren.

**Ausdruck:** <br>
`Append([userPrincipalName], ".test")`

**Beispieleingabe/-ausgabe:** <br>

* **EINGABE**: (userPrincipalName): "John.Doe@contoso.com"
* **AUSGABE**:  "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generieren eines Benutzeralias durch Verketten von Teilen des Vor- und Nachnamens
Sie müssen einen Benutzeralias generieren, indem Sie die ersten drei Buchstaben des Vornamens und die ersten fünf Buchstaben des Nachnamens des Benutzers verwenden.

**Ausdruck:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Beispieleingabe/-ausgabe:** <br>

* **EINGABE** (givenName): „John“
* **EINGABE** (surname): „Doe“
* **AUSGABE**:  „JohDoe“

### <a name="remove-diacritics-from-a-string"></a>Entfernen diakritischer Zeichen aus einer Zeichenfolge
Sie müssen Zeichen mit Akzent durch entsprechende Zeichen ohne Akzent ersetzen.

**Ausdruck:** <br>
NormalizeDiacritics([givenName])

**Beispieleingabe/-ausgabe:** <br>

* **EINGABE** (givenName): „Zoë“
* **AUSGABE**:  „Zoe“

### <a name="split-a-string-into-a-multi-valued-array"></a>Unterteilen einer Zeichenfolge in ein mehrwertiges Array
Sie müssen eine durch Trennzeichen getrennte Liste von Zeichenfolgen in ein Array unterteilen, das in ein mehrwertiges Attribut wie das PermissionSets-Attribut von Salesforce eingegeben werden kann. In diesem Beispiel wurde eine Liste von Berechtigungssätzen in extensionAttribute5 in Azure AD eingegeben.

**Ausdruck:** <br>
Split([extensionAttribute5], ",")

**Beispieleingabe/-ausgabe:** <br>

* **EINGABE:** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **AUSGABE:** ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Ausgabedatum eines Datums als Zeichenfolge in einem bestimmten Format
Sie möchten Datumsangaben in einem bestimmten Format an eine SaaS-Anwendung senden. <br>
Beispielsweise möchten Sie Datumsangaben für ServiceNow formatieren.

**Ausdruck:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Beispieleingabe/-ausgabe:**

* **EINGABE** (extensionAttribute1): 20150123105347.1Z
* **AUSGABE**:  2015-01-23

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersetzen eines Werts anhand eines vordefinierten Satzes von Optionen

Sie müssen die Zeitzone des Benutzers anhand des Bundesstaatscodes festlegen, der in Azure AD gespeichert ist. <br>
Wenn der Bundesstaatscode keiner der vordefinierten Optionen entspricht, soll der Standardwert "Australien/Sydney" verwendet werden.

**Ausdruck:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Beispieleingabe/-ausgabe:**

* **EINGABE** (state): „QLD“
* **AUSGABE**: „Australien/Brisbane“

### <a name="replace-characters-using-a-regular-expression"></a>Ersetzen von Zeichen mit einem regulären Ausdruck
Sie müssen Zeichen suchen, die mit einem regulären Ausdruck übereinstimmen, und diese entfernen.

**Ausdruck:** <br>

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

**Ausdruck:** <br>

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


## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Was ist die Azure AD Connect-Cloudbereitstellung?](what-is-cloud-provisioning.md)