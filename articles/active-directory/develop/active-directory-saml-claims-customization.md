---
title: Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen in Azure AD | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Ansprüche im SAML-Token für Unternehmensanwendungen in Azure AD anpassen.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 622525705979cd6a7a088c606ac167d28f8f6482
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950999"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Gewusst wie: Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen

Azure Active Directory (Azure AD) unterstützt derzeit einmaliges Anmelden (Single Sign-On, SSO) für die meisten Unternehmensanwendungen, einschließlich bereits im Azure AD-App-Katalog integrierter Anwendungen sowie benutzerdefinierter Anwendungen. Wenn sich ein Benutzer mithilfe des SAML 2.0-Protokolls über Azure AD bei einer Anwendung authentifiziert, sendet Azure AD ein Token an die Anwendung (über eine HTTP POST-Anfrage). Die Anwendung überprüft und verwendet dann das Token, um den Benutzer anzumelden, anstatt den Benutzernamen und das Kennwort anzufordern. Diese SAML-Token enthalten Informationen über den Benutzer, die als *Ansprüche* bezeichnet werden.

Ein *Anspruch* (Claim) bezeichnet Informationen, die ein Identitätsanbieter über einen Benutzer in dem für diesen Benutzer ausgestellten Token angibt. Im [SAML-Token](https://en.wikipedia.org/wiki/SAML_2.0) sind diese Daten in der Regel in der SAML-Attributanweisung enthalten. Die eindeutige ID des Benutzers wird normalerweise im SAML-Betreff dargestellt und auch als Namensbezeichner bezeichnet.

Standardmäßig stellt Azure AD der Anwendung ein SAML-Token aus, das einen `NameIdentifier`-Anspruch enthält, dessen Wert dem Benutzernamen (auch als Benutzerprinzipalname bezeichnet) in Azure AD entspricht, durch den der Benutzer eindeutig identifiziert werden kann. Das SAML-Token enthält auch zusätzliche Ansprüche, die E-Mail-Adresse des Benutzers, Vorname und Nachname enthält.

Um die im SAML-Token für die Anwendung ausgestellten Ansprüche anzuzeigen oder zu bearbeiten, öffnen Sie die Anwendung im Azure-Portal. Öffnen Sie dann den Abschnitt **Benutzerattribute und Ansprüche**.

![Abschnitt „Benutzerattribute und Ansprüche“](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Es gibt zwei Gründe dafür, dass Sie die im SAML-Token ausgegebenen Ansprüche möglicherweise bearbeiten müssen:

* Die Anwendung erfordert, dass es sich beim `NameIdentifier`- oder NameID-Anspruch nicht um den in Azure AD gespeicherten Benutzernamen (oder Benutzerprinzipalnamen) handelt.
* Die Anwendung wurde so geschrieben, dass sie einen anderen Satz an Anspruchs-URIs oder Anspruchswerten erfordert.

## <a name="editing-nameid"></a>Bearbeiten der NameID

Gehen Sie wie folgt vor, um die NameID (den Wert für den Namensbezeichner) zu bearbeiten:

1. Öffnen Sie die Seite **Wert für Namensbezeichner**.
1. Wählen Sie das gewünschte Attribut oder die auf das Attribut anzuwendende Transformation aus. Optional können Sie auch das Format für den NameID-Anspruch angeben.

   ![NameID-Wert (Wert für Namensbezeichner) bearbeiten](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-Format

Wenn die SAML-Anforderung das NameIDPolicy-Element in einem bestimmten Format enthält, berücksichtigt Azure AD das Format in der Anforderung.

Wenn die SAML-Anforderung kein Element für NameIDPolicy enthält, gibt Azure AD die NameID in dem von Ihnen angegebenen Format aus. Wenn kein Format angegeben ist, verwendet Azure AD das Standardquellformat, das der ausgewählten Anspruchsquelle zugeordnet ist.

Im Dropdownmenü **Namensbezeichnerformat auswählen** können Sie eine der folgenden Optionen auswählen.

| NameID-Format | BESCHREIBUNG |
|---------------|-------------|
| **Standard** | Azure AD verwendet das Standardquellformat. |
| **Persistent** | Azure AD verwendet das NameID-Format „Persistent“. |
| **EmailAddress** | Azure AD verwendet das NameID-Format „EmailAddress“. |
| **Nicht angegeben** | Azure AD verwendet das NameID-Format „Unspecified“. |
| **Transient** (Vorübergehend) | Azure AD verwendet das NameID-Format „Transient“. |

Weitere Informationen zum NameIDPolicy-Attribut finden Sie unter [SAML-Protokoll für einmaliges Anmelden](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attribute

Wählen Sie die gewünschte Quelle für den Anspruch `NameIdentifier` (oder NameID) aus. Sie können aus folgenden Optionen auswählen:

| NAME | BESCHREIBUNG |
|------|-------------|
| E-Mail | E-Mail-Adresse des Benutzers |
| userprincipalName | Benutzerprinzipalname (User Principal Name, UPN) des Benutzers |
| onpremisessamaccount | Der SAM-Kontoname der aus der lokalen Azure AD-Instanz synchronisiert wurde. |
| objectid | Objekt-ID des Benutzers in Azure AD |
| employeeid | Mitarbeiter-ID des Benutzers |
| Verzeichniserweiterungen | Verzeichniserweiterungen, die [über die Azure AD Connect-Synchronisierung aus dem lokalen Active Directory synchronisiert wurden](../hybrid/how-to-connect-sync-feature-directory-extensions.md). |
| Erweiterungsattribute 1–15 | Die lokalen Erweiterungsattribute, die zur Erweiterung des Azure AD-Schemas verwendet werden. |

Weitere Informationen finden Sie in [Table 3: Valid ID values per source (Tabelle 3: Gültige ID-Werte pro Quelle)](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Besondere Ansprüche – Transformationen

Sie können auch die Funktionen für Anspruchstransformationen verwenden.

| Funktion | BESCHREIBUNG |
|----------|-------------|
| **ExtractMailPrefix()** | Entfernt das Domänensuffix aus der E-Mail-Adresse oder dem Benutzerprinzipalnamen. Dadurch wird nur der erste Teil des Benutzernamens übergeben (z.B. „joe_smith“ anstelle von joe_smith@contoso.com). |
| **Join()** | Verknüpft ein Attribut mit einer überprüften Domäne. Wenn der ausgewählte Wert für die Benutzer-ID über eine Domäne verfügt, wird der Benutzername extrahiert, an den ausgewählte überprüfte Domäne angefügt werden soll. Wenn Sie z.B. die E-Mail-Adresse (joe_smith@contoso.com) als Wert für die Benutzer-ID und „contoso.onmicrosoft.com“ als überprüfte Domäne verwenden, erhalten Sie joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Konvertiert die Zeichen des ausgewählten Attributs in Kleinbuchstaben. |
| **ToUpper()** | Konvertiert die Zeichen des ausgewählten Attributs in Großbuchstaben. |

## <a name="adding-application-specific-claims"></a>Hinzufügen anwendungsspezifischer Ansprüche

Gehen Sie wie folgt vor, um anwendungsspezifische Ansprüche hinzuzufügen:

1. Wählen Sie im Abschnitt **Benutzerattribute und Ansprüche** die Option **Neuen Anspruch hinzufügen** aus, um die Seite **Benutzeransprüche verwalten** zu öffnen.
1. Geben Sie den **Namen** der Ansprüche ein. Der Wert muss nicht unbedingt einem URI-Muster gemäß der SAML-Spezifikation folgen. Wenn Sie ein URI-Muster benötigen, können Sie es im Feld **Namespace** angeben.
1. Wählen Sie die **Quelle** aus, aus der der Anspruch den zugehörigen Wert abruft. Sie können ein Benutzerattribut aus der Dropdownliste für „Quellattribut“ auswählen oder eine Transformation auf das Benutzerattribut anwenden, bevor es als Anspruch ausgegeben wird.

### <a name="application-specific-claims---transformations"></a>Anwendungsspezifische Ansprüche – Transformationen

Sie können auch die Funktionen für Anspruchstransformationen verwenden.

| Funktion | BESCHREIBUNG |
|----------|-------------|
| **ExtractMailPrefix()** | Entfernt das Domänensuffix aus der E-Mail-Adresse oder dem Benutzerprinzipalnamen. Dadurch wird nur der erste Teil des Benutzernamens übergeben (z.B. „joe_smith“ anstelle von joe_smith@contoso.com). |
| **Join()** | Erstellt einen neuen Wert durch Verknüpfen von zwei Attributen. Optional können Sie ein Trennzeichen zwischen den beiden Attributen verwenden. |
| **ToLower()** | Konvertiert die Zeichen des ausgewählten Attributs in Kleinbuchstaben. |
| **ToUpper()** | Konvertiert die Zeichen des ausgewählten Attributs in Großbuchstaben. |
| **Contains()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe dem angegebenen Wert entspricht. Andernfalls können Sie eine andere Ausgabe angeben, wenn keine Übereinstimmung vorhanden ist.<br/>Beispiel: Sie möchten einen Anspruch ausgeben, dessen Wert die E-Mail-Adresse des Benutzers ist, wenn er die Domäne „@contoso.com“ enthält, andernfalls soll der Benutzerprinzipalname ausgegeben werden. Hierzu konfigurieren Sie die folgenden Werte:<br/>*Parameter 1 (Eingabe)* : user.email<br/>*Wert*: „@contoso.com“<br/>Parameter 2 (Ausgabe): user.email<br/>Parameter 3 (Ausgabe, wenn keine Übereinstimmung vorhanden ist): user.userprincipalname |
| **EndWith()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe mit dem angegebenen Wert endet. Andernfalls können Sie eine andere Ausgabe angeben, wenn keine Übereinstimmung vorhanden ist.<br/>Beispiel: Sie möchten einen Anspruch ausgeben, dessen Wert die Mitarbeiter-ID des Benutzers ist, wenn „employeeid“ mit „000“ endet, andernfalls soll ein Erweiterungsattribut ausgegeben werden. Hierzu konfigurieren Sie die folgenden Werte:<br/>*Parameter 1 (Eingabe)* : user.employeeid<br/>*Value*: „000“<br/>Parameter 2 (Ausgabe): user.employeeid<br/>Parameter 3 (Ausgabe, wenn keine Übereinstimmung vorhanden ist): user.extensionattribute1 |
| **StartWith()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe mit dem angegebenen Wert beginnt. Andernfalls können Sie eine andere Ausgabe angeben, wenn keine Übereinstimmung vorhanden ist.<br/>Wenn Sie beispielsweise einen Anspruch ausgeben möchten, bei dem der Wert die „employeeid“ des Benutzer ist, wenn das Land bzw. die Region mit „US“ beginnt, und Sie andernfalls ein Erweiterungsattribut ausgeben möchten. Hierzu konfigurieren Sie die folgenden Werte:<br/>*Parameter 1 (Eingabe)* : user.country<br/>*Value*: „US“<br/>Parameter 2 (Ausgabe): user.employeeid<br/>Parameter 3 (Ausgabe, wenn keine Übereinstimmung vorhanden ist): user.extensionattribute1 |
| **Extract() – nach dem Abgleich** | Gibt die Teilzeichenfolge bei Übereinstimmung mit dem angegebenen Wert zurück.<br/>Beispiel: Wenn der Eingabewert „Finance_BSimon“ und der übereinstimmende Wert „Finance_“ ist, dann lautet die Ausgabe des Anspruchs „BSimon“. |
| **Extract() – vor dem Abgleich** | Gibt die Teilzeichenfolge zurück, bis sie mit dem angegebenen Wert übereinstimmt.<br/>Beispiel: Wenn der Eingabewert „BSimon_US“ und der übereinstimmende Wert „_US“ ist, dann lautet die Ausgabe des Anspruchs „BSimon“. |
| **Extract() – zwischen Abgleichen** | Gibt die Teilzeichenfolge zurück, bis sie mit dem angegebenen Wert übereinstimmt.<br/>Beispiel: Wenn der Eingabewert „Finance_BSimon_US“ ist, der erste übereinstimmende Wert „Finance_“ und der zweite übereinstimmende Wert „_US“ lautet, dann ist die Ausgabe des Anspruchs „BSimon“. |
| **ExtractAlpha() – Präfix** | Gibt den alphabetischen Teil des Präfixes der Zeichenfolge zurück.<br/>Beispiel: Wenn der Eingabewert „BSimon_123“ lautet, wird „BSimon“ zurückgegeben. |
| **ExtractAlpha() – Suffix** | Gibt den alphabetischen Teil des Suffixes der Zeichenfolge zurück.<br/>Beispiel: Wenn der Eingabewert „123_Simon“ lautet, wird „Simon“ zurückgegeben. |
| **ExtractNumeric() – Präfix** | Gibt den numerischen Teil des Präfixes der Zeichenfolge zurück.<br/>Beispiel: Wenn der Eingabewert „123_BSimon“ lautet, wird „123“ zurückgegeben. |
| **ExtractNumeric() – Suffix** | Gibt den numerischen Teil des Suffixes der Zeichenfolge zurück.<br/>Beispiel: Wenn der Eingabewert „BSimon_123“ lautet, wird „123“ zurückgegeben. |
| **IfEmpty()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe null oder leer ist.<br/>Beispiel: Sie möchten ein in einem Erweiterungsattribut (extensionattribute) gespeichertes Attribut ausgeben, wenn die Mitarbeiter-ID (employeeid) für einen bestimmten Benutzer leer ist. Hierzu konfigurieren Sie die folgenden Werte:<br/>Parameter 1 (Eingabe): user.employeeid<br/>Parameter 2 (Ausgabe): user.extensionattribute1<br/>Parameter 3 (Ausgabe, wenn keine Übereinstimmung vorhanden ist): user.employeeid |
| **IfNotEmpty()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe nicht null oder leer ist.<br/>Beispiel: Sie möchten ein in einem Erweiterungsattribut (extensionattribute) gespeichertes Attribut ausgeben, wenn die Mitarbeiter-ID (employeeid) für einen bestimmten Benutzer nicht leer ist. Hierzu konfigurieren Sie die folgenden Werte:<br/>Parameter 1 (Eingabe): user.employeeid<br/>Parameter 2 (Ausgabe): user.extensionattribute1 |

Wenn Sie zusätzliche Transformationen benötigen, senden Sie Ihre Vorschläge an das [Azure AD-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599). Verwenden Sie dort die Kategorie *SaaS-Anwendung*.

## <a name="next-steps"></a>Nächste Schritte

* [Anwendungsverwaltung in Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure AD-Anwendungskatalog enthalten sind](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Problembehandlung bei SAML-basiertem einmaligem Anmelden](howto-v1-debug-saml-sso-issues.md)
