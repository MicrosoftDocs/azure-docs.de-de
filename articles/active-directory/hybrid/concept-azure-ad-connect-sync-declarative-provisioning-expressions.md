---
title: 'Azure AD Connect: Ausdrücke für die deklarative Bereitstellung | Microsoft-Dokumentation'
description: Erläutert die Ausdrücke für die deklarative Bereitstellung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60245506"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect-Synchronisierung: Grundlegendes zu Ausdrücken für die deklarative Bereitstellung
Die Azure AD Connect-Synchronisierung basiert auf der deklarativen Bereitstellung, die erstmals in Forefront Identity Manager 2010 eingeführt wurde. Sie ermöglicht Ihnen die Implementierung Ihrer gesamten Geschäftslogik zur Identitätsintegration, ohne kompilierten Code schreiben zu müssen.

Ein wesentlicher Bestandteil der deklarativen Bereitstellung ist die in den Attributflüssen verwendete Ausdruckssprache. Die verwendete Sprache ist eine Teilmenge von Microsoft ® Visual Basic ® for Applications (VBA). Diese Sprache wird in Microsoft Office verwendet, und Benutzer mit Erfahrungen mit VBScript werden sie wiedererkennen. Die Ausdruckssprache für die deklarative Bereitstellung verwendet nur Funktionen und ist keine strukturierte Sprache. Es gibt keine Methoden oder Anweisungen. Funktionen werden stattdessen geschachtelt, um den Programmablauf auszudrücken.

Weitere Informationen finden Sie unter [Willkommen bei der VBA-Sprachreferenz für Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Die Attribute sind stark typisiert. Eine Funktion akzeptiert nur Attribute des richtigen Typs. Zudem muss die Groß-/Kleinschreibung beachtet werden. Sowohl bei Funktions- als auch Attributnamen muss die Groß-/Kleinschreibung korrekt sein. Andernfalls wird ein Fehler ausgegeben.

## <a name="language-definitions-and-identifiers"></a>Sprachdefinitionen und Bezeichner
* Bei Funktionen folgen auf den Namen Argumente in Klammern: FunctionName(argument 1, argument N).
* Attribute werden durch eckige Klammern gekennzeichnet: [attributeName].
* Parameter werden durch Prozentzeichen gekennzeichnet: %ParameterName%.
* Zeichenfolgenkonstanten werden in Anführungszeichen gesetzt: Beispiel: "Contoso" (Hinweis: Es muss sich um gerade Anführungszeichen ("") handeln. Typografische Anführungszeichen (“”) sind nicht zulässig.)
* Numerische Werte werden ohne Anführungszeichen ausgedrückt und im Dezimalformat vorliegen. Hexadezimalwerten weisen das Präfix "&H" auf. Beispiel: 98052, &HFF.
* Boolesche Werte werden mit Konstanten ausgedrückt: True, False.
* Integrierte Konstanten und Literale werden nur mit ihrem Namen ausgedrückt: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
Bei der deklarativen Bereitstellung werden viele Funktionen verwendet, um das Transformieren von Attributwerten zu ermöglichen. Diese Funktionen können geschachtelt werden, sodass das Ergebnis einer Funktion an eine andere Funktion übergeben wird.

`Function1(Function2(Function3()))`

Die vollständige Liste der Funktionen finden Sie in der [Funktionsreferenz](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parameter
Ein Parameter wird entweder durch einen Connector oder einen Administrator unter Verwendung von PowerShell definiert. Parameter enthalten üblicherweise Werte, die sich je nach System unterscheiden, z.B. der Name der Domäne, in der sich der Benutzer befindet. Diese Parameter können in Attributflüssen verwendet werden.

Der Active Directory Connector stellt folgende Parameter für eingehende Synchronisierungsregeln bereit:

| Parametername | Comment |
| --- | --- |
| Domain.Netbios |NetBIOS-Format der Domäne, die gerade importiert wird, z.B. „FABRIKAMSALES“ |
| Domain.FQDN |FQDN-Format der Domäne, die gerade importiert wird, z.B. „sales.fabrikam.com“ |
| Domain.LDAP |LDAP-Format der Domäne, die gerade importiert wird, z.B. „DC=sales,DC=fabrikam,DC=com“ |
| Forest.Netbios |NetBIOS-Format des Gesamtstrukturnamens, der gerade importiert wird, z.B. „FABRIKAMCORP“ |
| Forest.FQDN |FQDN-Format des Gesamtstrukturnamens, der gerade importiert wird, z.B. „fabrikam.com“ |
| Forest.LDAP |LDAP-Format des Gesamtstrukturnamens, der gerade importiert wird, z.B. „DC=fabrikam,DC=com“ |

Das System stellt den folgenden Parameter bereit, mit dem der Bezeichner des derzeit ausgeführten Connectors abgerufen wird:  
`Connector.ID`

Hier sehen Sie ein Beispiel, in dem die Metaverseattributdomäne mit dem NetBIOS-Namen der Domäne aufgefüllt wird, in der sich der Benutzer befindet:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatoren
Folgende Operatoren können verwendet werden:

* **Vergleich**: &lt;, &lt;=, &lt;&gt;, =, &gt;, &gt;=
* **Mathematik**: +, -, \*, -
* **Zeichenfolge**: &amp; (Verkettung)
* **Logischer Ausdruck**: &&amp;amp;amp;amp; (und), || (oder)
* **Auswertungsreihenfolge**: ( )

Operatoren werden von links nach rechts ausgewertet und haben bei der Auswertung die gleiche Priorität. Dies bedeutet, dass der Multiplikator (\*) nicht vor der Subtraktion (-) ausgewertet wird. „2\*(5+3)“ ist nicht dasselbe wie „2\*5+3“. Die Klammern werden verwendet, um die Reihenfolge der Auswertung zu ändern, wenn die Auswertungsreihenfolge von links nach rechts nicht geeignet ist.

## <a name="multi-valued-attributes"></a>Mehrwertige Attribute
Die Funktionen können sowohl für einwertige als auch für mehrwertige Attribute verwendet werden. Bei mehrwertigen Attributen wird die Funktion für jeden Wert ausgeführt, und auf alle Werte wird die gleiche Funktion angewendet.

Beispiel:  
`Trim([proxyAddresses])` – Führt eine Trim-Funktion für jeden Wert im Attribut „proxyAddress“ aus.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` – Ersetzt für jeden Wert mit dem @-sign-Zeichen die Domäne durch „@contoso.com“.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` – Sucht nach der SIP-Adresse und entfernt sie aus den Werten.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Konfigurationsmodell finden Sie unter [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md)(Grundlegendes zur deklarativen Bereitstellung).
* Unter [Grundlegendes zur Standardkonfiguration](concept-azure-ad-connect-sync-default-configuration.md)wird die standardmäßige Verwendung der deklarativen Bereitstellung veranschaulicht.
* Unter [Ändern der Standardkonfiguration](how-to-connect-sync-change-the-configuration.md)wird beschrieben, wie Sie mit der deklarativen Bereitstellung eine praktische Änderung vornehmen.

**Übersichtsthemen**

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)

**Referenzthemen**

* [Azure AD Connect-Synchronisierung: Funktionsreferenz](reference-connect-sync-functions-reference.md)

