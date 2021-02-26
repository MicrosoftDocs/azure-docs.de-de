---
title: Anpassen von SAML-Tokenansprüchen für eine App
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie die von Microsoft Identity Platform ausgegebenen Ansprüche im SAML-Token für Unternehmensanwendungen anpassen.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 05447f41ca891adfe14533a74dfedf153e3c5773
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102698"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen

Microsoft Identity Platform unterstützt derzeit einmaliges Anmelden (Single Sign-On, SSO) für die meisten Unternehmensanwendungen, darunter auch die im Azure AD-App-Katalog integrierten Anwendungen sowie benutzerdefinierte Anwendungen. Wenn sich ein Benutzer mithilfe des SAML 2.0-Protokolls über Microsoft Identity Platform bei einer Anwendung authentifiziert, sendet Microsoft Identity Platform ein Token an die Anwendung (über HTTP POST). Die Anwendung überprüft und verwendet dann das Token, um den Benutzer anzumelden, anstatt den Benutzernamen und das Kennwort anzufordern. Diese SAML-Token enthalten Informationen über den Benutzer, die als *Ansprüche* bezeichnet werden.

Ein *Anspruch* (Claim) bezeichnet Informationen, die ein Identitätsanbieter über einen Benutzer in dem für diesen Benutzer ausgestellten Token angibt. Im [SAML-Token](https://en.wikipedia.org/wiki/SAML_2.0) sind diese Daten in der Regel in der SAML-Attributanweisung enthalten. Die eindeutige ID des Benutzers wird normalerweise im SAML-Betreff dargestellt und auch als Namensbezeichner bezeichnet.

Standardmäßig stellt Microsoft Identity Platform für die Anwendung ein SAML-Token aus, das einen `NameIdentifier`-Anspruch enthält, dessen Wert dem Benutzernamen (auch als Benutzerprinzipalname bezeichnet) in Azure AD entspricht, durch den der Benutzer eindeutig identifiziert werden kann. Das SAML-Token enthält auch zusätzliche Ansprüche, die E-Mail-Adresse des Benutzers, Vorname und Nachname enthält.

Um die im SAML-Token für die Anwendung ausgestellten Ansprüche anzuzeigen oder zu bearbeiten, öffnen Sie die Anwendung im Azure-Portal. Öffnen Sie dann den Abschnitt **Benutzerattribute und Ansprüche**.

![Öffnen des Abschnitts „Benutzerattribute und Ansprüche“ im Azure-Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Es gibt zwei Gründe dafür, dass Sie die im SAML-Token ausgegebenen Ansprüche möglicherweise bearbeiten müssen:

* Die Anwendung erfordert, dass es sich beim `NameIdentifier`- oder NameID-Anspruch nicht um den in Azure AD gespeicherten Benutzernamen (oder Benutzerprinzipalnamen) handelt.
* Die Anwendung wurde so geschrieben, dass sie einen anderen Satz an Anspruchs-URIs oder Anspruchswerten erfordert.

## <a name="editing-nameid"></a>Bearbeiten der NameID

Gehen Sie wie folgt vor, um die NameID (den Wert für den Namensbezeichner) zu bearbeiten:

1. Öffnen Sie die Seite **Wert für Namensbezeichner**.
1. Wählen Sie das gewünschte Attribut oder die auf das Attribut anzuwendende Transformation aus. Optional können Sie auch das Format für den NameID-Anspruch angeben.

   ![NameID-Wert (Wert für Namensbezeichner) bearbeiten](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-Format

Wenn die SAML-Anforderung das NameIDPolicy-Element in einem bestimmten Format enthält, berücksichtigt Microsoft Identity Platform das Format in der Anforderung.

Wenn die SAML-Anforderung kein Element für NameIDPolicy enthält, gibt Microsoft Identity Platform die NameID in dem von Ihnen angegebenen Format aus. Wenn kein Format angegeben ist, verwendet Microsoft Identity Platform das Standardquellformat, das der ausgewählten Anspruchsquelle zugeordnet ist.

Im Dropdownmenü **Namensbezeichnerformat auswählen** können Sie eine der folgenden Optionen auswählen.

| NameID-Format | BESCHREIBUNG |
|---------------|-------------|
| **Standard** | Microsoft Identity Platform verwendet das Standardquellformat. |
| **Persistent** | Microsoft Identity Platform verwendet „Persistent“ als NameID-Format. |
| **EmailAddress** | Microsoft Identity Platform verwendet „EmailAddress“ als NameID-Format. |
| **Nicht angegeben** | Microsoft Identity Platform verwendet „Unspecified“ als NameID-Format. |

Eine vorübergehende NameID wird ebenfalls unterstützt, ist jedoch im Dropdownmenü nicht verfügbar und kann in Azure nicht konfiguriert werden. Weitere Informationen zum NameIDPolicy-Attribut finden Sie unter [SAML-Protokoll für einmaliges Anmelden](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attributes

Wählen Sie die gewünschte Quelle für den Anspruch `NameIdentifier` (oder NameID) aus. Sie können aus folgenden Optionen auswählen:

| Name | BESCHREIBUNG |
|------|-------------|
| Email | E-Mail-Adresse des Benutzers |
| userprincipalName | Benutzerprinzipalname (User Principal Name, UPN) des Benutzers |
| onpremisessamaccount | Der SAM-Kontoname der aus der lokalen Azure AD-Instanz synchronisiert wurde. |
| objectid | Objekt-ID des Benutzers in Azure AD |
| employeeid | Mitarbeiter-ID des Benutzers |
| Verzeichniserweiterungen | Verzeichniserweiterungen, die [über die Azure AD Connect-Synchronisierung aus dem lokalen Active Directory synchronisiert wurden](../hybrid/how-to-connect-sync-feature-directory-extensions.md). |
| Erweiterungsattribute 1–15 | Die lokalen Erweiterungsattribute, die zur Erweiterung des Azure AD-Schemas verwendet werden. |

Weitere Informationen finden Sie in [Table 3: Valid ID values per source (Tabelle 3: Gültige ID-Werte pro Quelle)](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Sie können in Azure AD definierten Ansprüchen einen beliebigen konstanten (statischen) Wert zuweisen. Führen Sie folgende Schritte aus, um einen konstanten Wert zuzuweisen:

1. Klicken Sie im <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> im Abschnitt **Benutzerattribute und Ansprüche** auf das Symbol **Bearbeiten**´, um die Ansprüche zu bearbeiten.
1. Klicken Sie auf den erforderlichen Anspruch, den Sie ändern möchten.
1. Geben Sie als **Quellattribut** den konstanten Wert (ohne Anführungszeichen) entsprechend Ihrer Organisation ein, und klicken Sie auf **Speichern**.

    ![Abschnitt „Organisationsattribute und Ansprüche“ im Azure-Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Der konstante Wert wird wie unten dargestellt angezeigt.

    ![Abschnitt „Attribute und Ansprüche bearbeiten“ im Azure-Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

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

### <a name="claim-transformations"></a>Anspruchstransformationen

So wenden Sie eine Transformation auf ein Benutzerattribut an

1. Wählen Sie unter **Anspruch verwalten** die Option *Transformation* als Anspruchsquelle aus, um die Seite **Transformation verwalten** zu öffnen.
2. Wählen Sie in der Dropdownliste der Transformationen die Funktion aus. Abhängig von der ausgewählten Funktion müssen Sie Parameter und einen konstanten Wert angeben, die in der Transformation ausgewertet werden sollen. Weitere Informationen zu den verfügbaren Funktionen finden Sie in der folgenden Tabelle.
3. Wenn Sie mehrere Transformationen anwenden möchten, klicken Sie auf **Transformation hinzufügen**. Sie können maximal zwei Transformationen auf einen Anspruch anwenden. Beispielsweise könnten Sie zuerst das E-Mail-Präfix von `user.mail`extrahieren. Dann könnten Sie die Zeichenfolge in Großbuchstaben umwandeln.

   ![Transformieren mehrerer Ansprüche](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Zum Transformieren von Ansprüchen können Sie die folgenden Funktionen verwenden.

| Funktion | BESCHREIBUNG |
|----------|-------------|
| **ExtractMailPrefix()** | Entfernt das Domänensuffix aus der E-Mail-Adresse oder dem Benutzerprinzipalnamen. Dadurch wird nur der erste Teil des Benutzernamens übergeben (z.B. „joe_smith“ anstelle von joe_smith@contoso.com). |
| **Join()** | Erstellt einen neuen Wert durch Verknüpfen von zwei Attributen. Optional können Sie ein Trennzeichen zwischen den beiden Attributen verwenden. Bei der NameID-Anspruchstransformation beschränkt sich die Verknüpfung auf eine verifizierte Domäne. Wenn der ausgewählte Wert für die Benutzer-ID über eine Domäne verfügt, wird der Benutzername extrahiert, an den ausgewählte überprüfte Domäne angefügt werden soll. Wenn Sie z.B. die E-Mail-Adresse (joe_smith@contoso.com) als Wert für die Benutzer-ID und „contoso.onmicrosoft.com“ als überprüfte Domäne verwenden, erhalten Sie joe_smith@contoso.onmicrosoft.com. |
| **ToLowercase()** | Konvertiert die Zeichen des ausgewählten Attributs in Kleinbuchstaben. |
| **ToUppercase()** | Konvertiert die Zeichen des ausgewählten Attributs in Großbuchstaben. |
| **Contains()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe dem angegebenen Wert entspricht. Andernfalls können Sie eine andere Ausgabe angeben, wenn keine Übereinstimmung vorhanden ist.<br/>Beispiel: Sie möchten einen Anspruch ausgeben, dessen Wert die E-Mail-Adresse des Benutzers ist, wenn er die Domäne „@contoso.com“ enthält, andernfalls soll der Benutzerprinzipalname ausgegeben werden. Hierzu konfigurieren Sie die folgenden Werte:<br/>*Parameter 1 (Eingabe)* : user.email<br/>*Wert*: „@contoso.com“<br/>Parameter 2 (Ausgabe): user.email<br/>Parameter 3 (Ausgabe, wenn keine Übereinstimmung vorhanden ist): user.userprincipalname |
| **EndWith()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe mit dem angegebenen Wert endet. Andernfalls können Sie eine andere Ausgabe angeben, wenn keine Übereinstimmung vorhanden ist.<br/>Beispiel: Sie möchten einen Anspruch ausgeben, dessen Wert der Mitarbeiter-ID des Benutzers entspricht, wenn die Mitarbeiter-ID mit „000“ endet. Andernfalls soll ein Erweiterungsattribut ausgegeben werden. Hierzu konfigurieren Sie die folgenden Werte:<br/>*Parameter 1 (Eingabe)* : user.employeeid<br/>*Value*: „000“<br/>Parameter 2 (Ausgabe): user.employeeid<br/>Parameter 3 (Ausgabe, wenn keine Übereinstimmung vorhanden ist): user.extensionattribute1 |
| **StartWith()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe mit dem angegebenen Wert beginnt. Andernfalls können Sie eine andere Ausgabe angeben, wenn keine Übereinstimmung vorhanden ist.<br/>Beispiel: Sie möchten einen Anspruch ausgeben, bei dem der Wert der Mitarbeiter-ID des Benutzer entspricht, wenn das Land bzw. die Region mit „US“ beginnt. Andernfalls soll ein Erweiterungsattribut ausgegeben werden. Hierzu konfigurieren Sie die folgenden Werte:<br/>*Parameter 1 (Eingabe)* : user.country<br/>*Value*: „US“<br/>Parameter 2 (Ausgabe): user.employeeid<br/>Parameter 3 (Ausgabe, wenn keine Übereinstimmung vorhanden ist): user.extensionattribute1 |
| **Extract() – nach dem Abgleich** | Gibt die Teilzeichenfolge bei Übereinstimmung mit dem angegebenen Wert zurück.<br/>Beispiel: Wenn der Eingabewert „Finance_BSimon“ und der übereinstimmende Wert „Finance_“ ist, dann lautet die Ausgabe des Anspruchs „BSimon“. |
| **Extract() – vor dem Abgleich** | Gibt die Teilzeichenfolge zurück, bis sie mit dem angegebenen Wert übereinstimmt.<br/>Beispiel: Wenn der Eingabewert „BSimon_US“ und der übereinstimmende Wert „_US“ ist, dann lautet die Ausgabe des Anspruchs „BSimon“. |
| **Extract() – zwischen Abgleichen** | Gibt die Teilzeichenfolge zurück, bis sie mit dem angegebenen Wert übereinstimmt.<br/>Beispiel: Wenn der Eingabewert „Finance_BSimon_US“ ist, der erste übereinstimmende Wert „Finance\_“ und der zweite übereinstimmende Wert „\_US“ lautet, dann ist die Ausgabe des Anspruchs „BSimon“. |
| **ExtractAlpha() – Präfix** | Gibt den alphabetischen Teil des Präfixes der Zeichenfolge zurück.<br/>Beispiel: Wenn der Eingabewert „BSimon_123“ lautet, wird „BSimon“ zurückgegeben. |
| **ExtractAlpha() – Suffix** | Gibt den alphabetischen Teil des Suffixes der Zeichenfolge zurück.<br/>Beispiel: Wenn der Eingabewert „123_Simon“ lautet, wird „Simon“ zurückgegeben. |
| **ExtractNumeric() – Präfix** | Gibt den numerischen Teil des Präfixes der Zeichenfolge zurück.<br/>Beispiel: Wenn der Eingabewert „123_BSimon“ lautet, wird „123“ zurückgegeben. |
| **ExtractNumeric() – Suffix** | Gibt den numerischen Teil des Suffixes der Zeichenfolge zurück.<br/>Beispiel: Wenn der Eingabewert „BSimon_123“ lautet, wird „123“ zurückgegeben. |
| **IfEmpty()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe null oder leer ist.<br/>Beispiel: Sie möchten ein in einem Erweiterungsattribut (extensionattribute) gespeichertes Attribut ausgeben, wenn die Mitarbeiter-ID für einen bestimmten Benutzer leer ist. Hierzu konfigurieren Sie die folgenden Werte:<br/>Parameter 1 (Eingabe): user.employeeid<br/>Parameter 2 (Ausgabe): user.extensionattribute1<br/>Parameter 3 (Ausgabe, wenn keine Übereinstimmung vorhanden ist): user.employeeid |
| **IfNotEmpty()** | Gibt ein Attribut oder eine Konstante aus, wenn die Eingabe nicht null oder leer ist.<br/>Beispiel: Sie möchten ein in einem Erweiterungsattribut (extensionattribute) gespeichertes Attribut ausgeben, wenn die Mitarbeiter-ID für einen bestimmten Benutzer nicht leer ist. Hierzu konfigurieren Sie die folgenden Werte:<br/>Parameter 1 (Eingabe): user.employeeid<br/>Parameter 2 (Ausgabe): user.extensionattribute1 |

Wenn Sie zusätzliche Transformationen benötigen, senden Sie Ihre Vorschläge an das [Azure AD-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599). Verwenden Sie dort die Kategorie *SaaS-Anwendung*.

## <a name="emitting-claims-based-on-conditions"></a>Ausgeben von Ansprüchen aufgrund von Bedingungen

Sie können die Anspruchsquelle auf Basis des Benutzertyps und der Gruppe, zu der der Benutzer gehört, angeben. 

Folgende Benutzertypen stehen zur Auswahl:
- **Alle**: Alle Benutzer dürfen auf die Anwendung zugreifen.
- **Mitglieder**: Native Mitglieder des Mandanten
- **Alle Gäste**: Der Benutzer stammt aus einer externen Organisation mit oder ohne Azure AD.
- **AAD-Gäste**: Der Gastbenutzer gehört zu einer anderen Organisation, die Azure AD verwendet.
- **Externe Gäste**: Der Gastbenutzer gehört zu einer externen Organisation, die nicht über Azure AD verfügt.


Diese Option ist hilfreich bei einem Szenario, bei dem sich die Anspruchsquelle für einen Gast und einen Mitarbeiter, die auf eine Anwendung zugreifen, unterscheidet. Sie könnten beispielsweise angeben, dass die NameID von „user.email“ abgerufen wird, wenn der Benutzer ein Mitarbeiter ist, oder von „user.extensionattribute1“, wenn der Benutzer ein Gast ist.

So fügen Sie eine Anspruchsbedingung hinzu

1. Erweitern Sie unter **Anspruch verwalten** die Anspruchsbedingungen.
2. Wählen Sie den Benutzertyp aus.
3. Wählen Sie die Gruppe(n) aus, zu der bzw. denen der Benutzer gehören soll. Sie können übergreifend über alle Ansprüche für eine bestimmte Anwendung bis zu 50 eindeutige Gruppen auswählen. 
4. Wählen Sie die **Quelle** aus, aus der der Anspruch den zugehörigen Wert abruft. Sie können ein Benutzerattribut aus der Dropdownliste für „Quellattribut“ auswählen oder eine Transformation auf das Benutzerattribut anwenden, bevor es als Anspruch ausgegeben wird.

Die Reihenfolge, in der Sie die Bedingungen hinzufügen, spielt eine wichtige Rolle. Azure AD wertet die Bedingungen absteigend (von oben nach unten) aus, um den im Anspruch auszugebenden Wert zu bestimmen. Der letzte Wert, der mit dem Ausdruck übereinstimmt, wird im Anspruch ausgegeben.

Beispiel: Britta Simon ist Gastbenutzer im Contoso-Mandanten. Sie gehört zu einer anderen Organisation, die ebenfalls Azure AD verwendet. Wenn Britta bei der folgenden Konfiguration für die Fabrikam-Anwendung versucht, sich bei Fabrikam anzumelden, wertet Microsoft Identity Platform die Bedingungen wie folgt aus.

Zuerst überprüft Microsoft Identity Platform, ob der Benutzertyp von Britta `All guests` lautet. Da dies der Fall ist, weist Microsoft Identity Platform anschließend `user.extensionattribute1` die Quelle für den Anspruch zu. Zweitens überprüft Microsoft Identity Platform, ob der Benutzertyp von Britta `AAD guests` lautet. Da dies ebenfalls zutrifft, weist Microsoft Identity Platform `user.mail` die Quelle für den Anspruch zu. Abschließend wird der Anspruch mit dem Wert `user.mail` für Britta ausgegeben.

![Bedingte Konfiguration von Ansprüchen](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Nächste Schritte

* [Anwendungsverwaltung in Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure AD-Anwendungskatalog enthalten sind](../manage-apps/configure-saml-single-sign-on.md)
* [Problembehandlung bei SAML-basiertem einmaligem Anmelden](../manage-apps/debug-saml-sso-issues.md)
