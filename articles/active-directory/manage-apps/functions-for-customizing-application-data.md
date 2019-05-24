---
title: Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory | Microsoft Docs
description: Erfahren Sie, wie Ausdruckszuordnungen verwendet werden können, um Attributwerte während der automatisierten Bereitstellung von SaaS-App-Objekten in Azure Active Directory in ein akzeptables Format zu transformieren.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec1994169891d5256436ac4de741339c865bb268
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824638"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory
Wenn Sie die Bereitstellung für eine SaaS-Anwendung konfigurieren, ist einer der Attributzuordnungstypen, die Sie angeben können, eine Ausdruckszuordnung. Für diese müssen Sie einen skriptartigen Ausdruck schreiben, mit dem Sie die Daten Ihrer Benutzer in Formate umwandeln können, die für die SaaS-Anwendung einfacher zu akzeptieren sind.

## <a name="syntax-overview"></a>Syntaxübersicht
Die Syntax für die Ausdrücke für Attributzuordnungen ist den Funktionen von Visual Basic for Applications (VBA) ähnlich.

* Der gesamte Ausdruck muss mittels Funktionen definiert werden, die aus einem Namen mit darauffolgenden Argumenten in Klammern bestehen:  <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* Sie können Funktionen ineinander verschachteln. Beispiel:  <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Sie können drei verschiedene Argumententypen an die Funktionen übergeben:
  
  1. Attribute, die in eckige Klammern eingeschlossen werden müssen. Beispiel: [Attributname]
  2. Zeichenfolgenkonstanten, die in doppelte Anführungszeichen eingeschlossen werden müssen. Beispiel:  „USA“
  3. Andere Funktionen Beispiel:  FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Bei Zeichenfolgenkonstanten, in denen ein umgekehrter Schrägstrich ( \ ) oder ein Anführungszeichen ( " ) benötigt wird, muss dieser bzw. dieses mit einem umgekehrten Schrägstrichsymbol ( \ ) versehen werden. Beispiel:  „Unternehmensname: \\"Contoso\\""

## <a name="list-of-functions"></a>Liste der Funktionen
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)

- - -
### <a name="append"></a>Anfügen
**Funktion:**<br> Append(Quelle, Suffix)

**Beschreibung:**<br> Fügt das Suffix am Ende eines angegebenen Quellzeichenfolgenwerts an.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **Suffix** |Erforderlich |Zeichenfolge |Die Zeichenfolge, die Sie am Ende des Quellwerts anfügen möchten |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Funktion:**<br> FormatDateTime(Quelle, Eingabeformat, Ausgabeformat)

**Beschreibung:**<br> Konvertiert eine Datumszeichenfolge aus einem Format in ein anderes Format.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **Eingabeformat** |Erforderlich |Zeichenfolge |Erwartetes Format des Quellwerts. Unterstützte Formate finden Sie unter [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **Ausgabeformat** |Erforderlich |Zeichenfolge |Format des Ausgabedatums. |

- - -
### <a name="join"></a>Join
**Funktion:**<br>  Join(Trennzeichen, Quelle1, Quelle2, …)

**Beschreibung:**<br> „Join()“ ist vergleichbar mit „Append()“, kann jedoch mehrere **Quellzeichenfolgenwerte** in einer einzelnen Zeichenfolge kombinieren, wobei die Werte jeweils durch eine **Trennzeichenfolge** getrennt werden.

Wenn einer der Quellwerte ein mehrwertiges Attribut ist, werden die einzelnen Werte in diesem Attribut miteinander verknüpft und dabei durch den Trennzeichenwert getrennt.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Trennzeichen** |Erforderlich |Zeichenfolge |Zeichenfolge, die zur Trennung von Quellwerten verwendet wird, wenn diese zu einer einzelnen Zeichenfolge zusammengesetzt werden. Kann "" sein, wenn kein Trennzeichen erforderlich ist. |
| **source1 … sourceN** |Erforderlich, unterschiedlich oft |Zeichenfolge |Zeichenfolgenwerte, die zusammengesetzt werden sollen. |

- - -
### <a name="mid"></a>Mid
**Funktion:**<br>  Mid(Quelle, Start, Länge)

**Beschreibung:**<br>  Gibt eine Teilzeichenfolge des Quellwerts zurück. Eine Teilzeichenfolge ist eine Zeichenfolge, die nur einige der Zeichen aus der Quellzeichenfolge enthält.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge |Normalerweise der Name des Attributs. |
| **start** |Erforderlich |integer |Index in der **Quellzeichenfolge** , an dem die Teilzeichenfolge beginnen soll. Das erstes Zeichen in der Zeichenfolge hat den Index 1, das zweite Zeichen hat den Index 2 usw. |
| **Länge** |Erforderlich |Ganze Zahl |Die Länge der Teilzeichenfolge. Wenn die Länge außerhalb der **Quellzeichenfolge** endet, gibt die Funktion die Teilzeichenfolge zwischen **Startindex** und dem Ende der **Quellzeichenfolge** zurück. |

- - -
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funktion:**<br> NormalizeDiacritics(Quelle)

**Beschreibung:**<br> Erfordert ein einzelnes Zeichenfolgenargument. Gibt die Zeichenfolge zurück und ersetzt sämtliche diakritische Zeichen durch entsprechende nicht diakritische Zeichen. Dient in der Regel dazu, Vor- und Nachnamen mit diakritischen Zeichen (Akzenten) in zulässige Werte zu konvertieren, die in verschiedenen Benutzer-IDs verwendet werden können (etwa in Benutzerprinzipalnamen, SAM-Kontonamen und E-Mail-Adressen).

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge | In der Regel ein Attribut für einen Vor- oder Nachnamen. |

- - -
### <a name="not"></a>not
**Funktion:**<br>  Not(Quelle)

**Beschreibung:**<br> Kehrt den booleschen Wert der **Quelle** um. Lautet der **Quellwert** also *True*, gibt die Funktion *False* zurück. Andernfalls gibt sie "*True*" zurück.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Boolesche Zeichenfolge |Die erwarteten **Quellwerte** sind TRUE oder FALSE. |

- - -
### <a name="replace"></a>Replace
**Funktion:**<br> Replace(Quelle, AlterWert, RegexMuster, RegexGruppenname, Ersatzwert, Ersatzattributname, Vorlage)

**Beschreibung:**<br>
Ersetzt Werte in einer Zeichenfolge. Sie funktioniert unterschiedlich, je nachdem, welche Parameter angegeben werden:

* Bei Angabe von **AlterWert** und **Ersatzwert**:
  
  * Ersetzt alle Vorkommen von „AlterWert“ in der Quelle durch „Ersatzwert“.
* Bei Angabe von **AlterWert** und **Vorlage**:
  
  * Ersetzt alle Vorkommen von **AlterWert** in **Vorlage** durch den **Quellwert**).
* Bei Angabe von **RegexMuster**, **RegexGruppenname** und **Ersatzwert**:
  
  * Ersetzt alle Werte, die mit dem "AlterWertRegexMuster" in der Quellzeichenfolge übereinstimmen, durch den "Ersatzwert".
* Bei Angabe von **RegexMuster**, **RegexGruppenname** und **Ersatzeigenschaftsname**:
  
  * Falls kein Wert für **Quelle** vorhanden ist, wird **Quelle** zurückgegeben.
  * Ist ein Wert für **Quelle** vorhanden, wird der Ersatzwert aus der Eigenschaft mit **Ersatzeigenschaftsname** unter Verwendung von **RegexMuster** und **RegexGruppenname** extrahiert. Der Ersatzwert wird als Ergebnis zurückgegeben.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **AlterWert** |Optional |Zeichenfolge |Wert, der in **Quelle** oder **Vorlage** ersetzt werden soll. |
| **RegexMuster** |Optional |Zeichenfolge |Regex-Muster für den Wert, der in der **Quelle**ersetzt wird. Wenn "Ersatzeigenschaftsname" verwendet wird, das Muster, das zum Extrahieren des Werts aus der Ersatzeigenschaft verwendet wird. |
| **RegexGruppenname** |Optional |Zeichenfolge |Name der Gruppe im **RegexMuster**. Nur bei Verwendung von „Ersatzeigenschaftsname“ wird der Wert dieser Gruppe als „Ersatzwert“ aus der Ersatzeigenschaft extrahiert. |
| **Ersatzwert** |Optional |Zeichenfolge |Neuer Wert, durch den der alte Wert ersetzt wird. |
| **Ersatzattributname** |Optional |Zeichenfolge |Name des Attributs, das für den Ersatzwert verwendet werden soll, wenn die Quelle keinen Wert besitzt. |
| **Vorlage** |Optional |Zeichenfolge |Bei Angabe des Werts **Vorlage** wird **AlterWert** in der Vorlage gesucht und durch den Quellwert ersetzt. |

- - -
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funktion:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**Beschreibung:**<br> Es sind mindestens zwei Argumente erforderlich, bei denen es sich um eindeutige Regeln für die Generierung von Werten handelt, die mit Ausdrücken definiert werden. Mit der Funktion wird jede Regel ausgewertet. Anschließend wird der Wert überprüft, der generiert wurde, um die Eindeutigkeit in der Ziel-App bzw. im Zielverzeichnis sicherzustellen. Der erste eindeutige Wert, der gefunden wird, wird zurückgegeben. Wenn alle Werte am Ziel bereits vorhanden sind, wird der Eintrag hinterlegt, und die Ursache wird in den Überwachungsprotokollen protokolliert. Für die Anzahl von Argumenten, die bereitgestellt werden können, gilt keine Obergrenze.

> [!NOTE]
>1. Dies ist eine Funktion der obersten Ebene, die nicht geschachtelt werden kann.
>2. Diese Funktion darf nur für die Erstellung von Einträgen verwendet werden. Legen Sie die Eigenschaft **Zuordnung anwenden** auf **Nur beim Erstellen von Objekten** fest.


**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |Mindestens zwei erforderlich, keine Obergrenze |Zeichenfolge | Liste mit auszuwertenden Regeln für die Generierung eindeutiger Werte |


- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktion:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beschreibung:**<br> Gibt eine einzelne appRoleAssignment aus der Liste aller appRoleAssignments zurück, die einem Benutzer für eine bestimmte Anwendung zugewiesen sind. Diese Funktion ist erforderlich, um das appRoleAssignments-Objekt in eine einzelne Namenszeichenfolge für eine Rolle zu konvertieren. Beachten Sie, dass die bewährte Methode darin besteht, sicherzustellen, dass einem Benutzer nur jeweils eine appRoleAssignment zugewiesen ist. Wenn mehrere Rollen zugewiesen sind, ist die zurückgegebene Zeichenfolge für die Rolle möglicherweise nicht vorhersehbar. 

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Erforderlich |Zeichenfolge |**[appRoleAssignments]**-Objekt |

- - -
### <a name="split"></a>Split
**Funktion:**<br> Split(Quelle, Trennzeichen)

**Beschreibung:**<br> Unterteilt eine Zeichenfolge mithilfe des angegebenen Trennzeichens in ein mehrwertiges Array.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge |**Quelle** , der aktualisiert werden soll. |
| **Trennzeichen** |Erforderlich |Zeichenfolge |Gibt das Zeichen zum Aufteilen der Zeichenfolge an (Beispiel: „,“). |

- - -
### <a name="stripspaces"></a>StripSpaces
**Funktion:**<br>  StripSpaces(Quelle)

**Beschreibung:**<br>  Entfernt alle Leerzeichen (" ") aus der Quellzeichenfolge.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge |**Quelle** , der aktualisiert werden soll. |

- - -
### <a name="switch"></a>Switch
**Funktion:**<br>  Switch(Quelle, Standardwert, Schlüssel1, Wert1, Schlüssel2, Wert2, …)

**Beschreibung:**<br> Wenn der **Quellwert** einem **Schlüssel** entspricht, wird der **Wert** für diesen **Schlüssel** zurückgegeben. Wenn der **Quellwert** keinem Schlüssel entspricht, wird der **Standardwert** zurückgegeben.  **Schlüssel-** und **Wertparameter** müssen immer paarweise angegeben werden. Die Funktion erwartet immer eine gerade Anzahl von Parametern.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge |**Source** , der aktualisiert werden soll. |
| **defaultValue** |Optional |Zeichenfolge |Der Standardwert, der verwendet werden soll, wenn die Quelle mit keinem Schlüssel übereinstimmt. Kann eine leere Zeichenfolge ("") sein. |
| **key** |Erforderlich |Zeichenfolge |**Schlüssel**, der mit dem **Quellwert** verglichen werden soll. |
| **value** |Erforderlich |Zeichenfolge |Der Ersatzwert für die **Quelle** , die mit dem Schlüssel übereinstimmt. |

- - -
### <a name="tolower"></a>ToLower
**Funktion:**<br> ToLower(source, culture)

**Beschreibung:**<br> Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Kleinbuchstaben. Ohne Angabe der *culture*-Information wird die invariante Kultur verwendet.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **culture** |Optional |Zeichenfolge |Das Format für den Kulturnamen lautet basierend auf dem Standard RFC 4646 *languagecode2-country/regioncode2*, wobei *languagecode2* der aus zwei Buchstaben bestehende Sprachcode und *country/regioncode2* der aus zwei Buchstaben bestehende Subkulturcode ist. Beispiele sind „ja-JP“ für Japanisch (Japan) und „en-US“ für Englisch (USA). In Fällen, in denen kein aus zwei Buchstaben bestehender Sprachcode verfügbar ist, wird ein aus drei Buchstaben bestehender, von ISO 639-2 abgeleiteter Code verwendet.|

- - -
### <a name="toupper"></a>ToUpper
**Funktion:**<br> ToUpper(source, culture)

**Beschreibung:**<br> Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Großbuchstaben. Ohne Angabe der *culture*-Information wird die invariante Kultur verwendet.

**Parameter:**<br> 

| NAME | Erforderlich/wiederholt | Type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Zeichenfolge |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **culture** |Optional |Zeichenfolge |Das Format für den Kulturnamen lautet basierend auf dem Standard RFC 4646 *languagecode2-country/regioncode2*, wobei *languagecode2* der aus zwei Buchstaben bestehende Sprachcode und *country/regioncode2* der aus zwei Buchstaben bestehende Subkulturcode ist. Beispiele sind „ja-JP“ für Japanisch (Japan) und „en-US“ für Englisch (USA). In Fällen, in denen kein aus zwei Buchstaben bestehender Sprachcode verfügbar ist, wird ein aus drei Buchstaben bestehender, von ISO 639-2 abgeleiteter Code verwendet.|

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

* **EINGABE:** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
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

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com")
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Beispieleingabe/-ausgabe:**

* **EINGABE** (PreferredFirstName): „John“
* **EINGABE** (PreferredLastName): „Smith“
* **OUTPUT**: "John.Smith@contoso.com", wenn der UPN-Wert John.Smith@contoso.com nicht bereits im Verzeichnis vorhanden ist
* **OUTPUT**: "J.Smith@contoso.com", wenn der UPN-Wert John.Smith@contoso.com bereits im Verzeichnis vorhanden ist
* **OUTPUT**: "Jo.Smith@contoso.com", wenn die beiden obigen UPN-Werte bereits im Verzeichnis enthalten sind

## <a name="related-articles"></a>Verwandte Artikel
* [Automatisieren der Bereitstellung/Bereitstellungsaufhebung von Benutzern für SaaS-Apps](user-provisioning.md)
* [Anpassen von Attributzuordnungen für die Benutzerbereitstellung](customize-application-attributes.md)
* [Bereichsfilter für die Benutzerbereitstellung](define-conditional-rules-for-provisioning-user-accounts.md)
* [Verwenden von SCIM für die automatische Bereitstellung von Benutzern und Gruppen aus Azure Active Directory für Anwendungen](use-scim-to-provision-users-and-groups.md)
* [Kontobereitstellungsbenachrichtigungen](user-provisioning.md)
* [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)
