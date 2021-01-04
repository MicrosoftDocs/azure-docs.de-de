---
title: Benutzerprofilattribute in Azure Active Directory B2C
description: Hier finden Sie Informationen zu den Attributen von Benutzerressourcentypen, die vom Azure AD B2C-Verzeichnisbenutzerprofil unterstützt werden. Informieren Sie sich über integrierte Attribute, Erweiterungen und darüber, wie Attribute Microsoft Graph zugeordnet werden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85030285810433dc77d1f466d160c50d1f89770e
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750406"
---
# <a name="user-profile-attributes"></a>Benutzerprofilattribute

Ihr Azure Active Directory (Azure AD) B2C-Verzeichnisbenutzerprofil umfasst einen integrierten Satz von Attributen wie Vorname, Nachname, Ort, Postleitzahl und Telefonnummer. Sie können das Benutzerprofil mit Ihren eigenen Anwendungsdaten erweitern, ohne dass ein externer Datenspeicher erforderlich ist. Die meisten Attribute, die in Azure AD B2C-Benutzerprofilen verwendet werden können, werden auch von Microsoft Graph unterstützt. In diesem Artikel werden die unterstützten Azure AD B2C-Benutzerprofilattribute beschrieben. Außerdem werden die Attribute angeführt, die nicht von Microsoft Graph unterstützt werden, und es wird auf die Microsoft Graph-Attribute hingewiesen, die nicht in Azure AD B2C verwendet werden sollten.

> [!IMPORTANT]
> Sie sollten keine integrierten Attribute oder Erweiterungsattribute verwenden, um vertrauliche personenbezogene Daten zu speichern, wie z. B. Kontoanmeldeinformationen, amtliche Identifikationsnummern, Karteninhaberdaten, Finanzkontodaten, Gesundheitsdaten oder vertrauliche Hintergrundinformationen.

Sie können auch externe Systeme integrieren. Sie können z. B. Azure AD B2C für die Authentifizierung verwenden und eine externe Datenbank für Kundenbeziehungsmanagement (Customer Relationship Management, CRM) oder Kundendatenbank als autoritative Quelle der Kundendaten nutzen. Weitere Informationen finden Sie unter [Lösung für Remoteprofile](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile).

In der nachstehenden Tabelle sind die Attribute von [Benutzerressourcentypen](/graph/api/resources/user) aufgeführt, die vom Azure AD B2C-Verzeichnisbenutzerprofil unterstützt werden. Darin sind die folgenden Informationen zu den einzelnen Attributen enthalten:

- Der von Azure AD B2C verwendete Attributname (gefolgt vom Microsoft Graph-Namen in Klammern, falls dieser anders lautet)
- Attributdatentyp
- Attributbeschreibung
- Ob das Attribut im Azure-Portal verfügbar ist
- Ob das Attribut in einem Benutzerflow verwendet werden kann
- Ob das Attribut in einem [technischen Azure AD-Profil](active-directory-technical-profile.md) in einer benutzerdefinierten Richtlinie verwendet werden kann und in welchem Abschnitt (&lt;InputClaims&gt;, &lt;OutputClaims&gt; oder &lt;PersistedClaims&gt;)

|Name     |type     |BESCHREIBUNG|Azure-Portal|Benutzerabläufe|Benutzerdefinierte Richtlinie|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Gibt an, ob das Benutzerkonto aktiviert oder deaktiviert ist: **true**, wenn das Konto aktiviert ist, andernfalls **false**.|Ja|Nein|Persistent, Ausgabe|
|ageGroup        |String|Die Altersgruppe des Benutzers. Mögliche Werte: „Null“, „Undefined“, „Minor“, „Adult“ und „NotAdult“.|Ja|Nein|Persistent, Ausgabe|
|alternativeSecurityId ([Identities](manage-user-accounts-graph-api.md#identities-property))|String|Eine einzelne Benutzeridentität vom externen Identitätsanbieter.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|alternativeSecurityIds ([Identities](manage-user-accounts-graph-api.md#identities-property))|Sammlung alternativer Sicherheits-IDs|Eine Sammlung von Benutzeridentitäten von externen Identitätsanbietern.|Nein|Nein|Persistent, Ausgabe|
|city            |String|Die Stadt, in der sich der Benutzer befindet. Maximale Länge: 128 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|consentProvidedForMinor|String|Ob die Zustimmung für einen Minderjährigen erteilt wurde. Zulässige Werte: „Null“, „Granted“, „Denied“ oder „notRequired“.|Ja|Nein|Persistent, Ausgabe|
|country         |String|Das Land oder die Region, in dem bzw. der der Benutzer ansässig ist. Beispiel: „US“ oder „UK“. Maximale Länge: 128 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|createdDateTime|Datetime|Das Datum, an dem das Benutzerobjekt erstellt wurde. Schreibgeschützt.|Nein|Nein|Persistent, Ausgabe|
|creationType    |String|Wenn das Benutzerkonto als lokales Konto für einen Azure Active Directory B2C-Mandanten erstellt wurde, lautet der Wert „LocalAccount“ oder „nameCoexistence“. Schreibgeschützt.|Nein|Nein|Persistent, Ausgabe|
|dateOfBirth     |Date|Das Geburtsdatum.|Nein|Nein|Persistent, Ausgabe|
|department      |String|Der Name der Abteilung, in der der Benutzer arbeitet. Maximale Länge: 64 Zeichen.|Ja|Nein|Persistent, Ausgabe|
|displayName     |String|Der Anzeigename für den Benutzer. Maximale Länge: 256 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|facsimileTelephoneNumber<sup>1</sup>|String|Die geschäftliche Faxnummer des Benutzers.|Ja|Nein|Persistent, Ausgabe|
|givenName       |String|Der Vorname des Benutzers. Maximale Länge: 64 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|jobTitle        |String|Die Position des Benutzers. Maximale Länge: 128 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|immutableId     |String|Ein Bezeichner, der in der Regel für Benutzer verwendet wird, die von einer lokalen Active Directory-Instanz migriert werden.|Nein|Nein|Persistent, Ausgabe|
|legalAgeGroupClassification|String|Rechtliche Altersgruppenklassifizierung. Dieses Attribut ist schreibgeschützt und wird basierend auf den Eigenschaften „ageGroup“ und „consentProvidedForMinor“ berechnet. Zulässige Werte: „Null“, „minorWithOutParentalConsent“, „minorWithParentalConsent“, „minorNoParentalConsentRequired“, „notAdult“ und „adult“.|Ja|Nein|Persistent, Ausgabe|
|legalCountry<sup>1</sup>  |String|Land/Region für rechtliche Zwecke.|Nein|Nein|Persistent, Ausgabe|
|mail            |String|Die SMTP-Adresse für den Benutzer, z. B. „bob@contoso.com“. Schreibgeschützt.|Nein|Nein|Persistent, Ausgabe|
|mailNickName    |String|Der E-Mail-Alias für den Benutzer. Maximale Länge: 64 Zeichen.|Nein|Nein|Persistent, Ausgabe|
|mobile (mobilePhone) |String|Die primäre Mobiltelefonnummer des Benutzers. Maximale Länge: 64 Zeichen.|Ja|Nein|Persistent, Ausgabe|
|netId           |String|Die Netz-ID.|Nein|Nein|Persistent, Ausgabe|
|objectId        |String|Ein global eindeutiger Bezeichner (Globally Unique Identifier, GUID), bei dem es sich um den eindeutigen Bezeichner für den Benutzer handelt. Beispiel: 12345678-9abc-def0-1234-56789abcde. Schreibgeschützt, unveränderlich.|Nur Lesezugriff|Ja|Eingabe, Persistent, Ausgabe|
|otherMails      |Zeichenfolgensammlung|Eine Liste zusätzlicher E-Mail-Adressen für den Benutzer. Beispiel: [„bob@contoso.com“, „Robert@fabrikam.com“].|Ja (Alternative E-Mail-Adresse)|Nein|Persistent, Ausgabe|
|password        |String|Das Kennwort für das lokale Konto bei der Benutzererstellung.|Nein|Nein|Persistent|
|passwordPolicies     |String|Die Richtlinie des Kennworts. Dabei handelt sich um eine Zeichenfolge, die aus verschiedenen, durch Komma getrennten Richtliniennamen besteht. Das heißt: „DisablePasswordExpiration, DisableStrongPassword“.|Nein|Nein|Persistent, Ausgabe|
|physicalDeliveryOfficeName (officeLocation)|String|Position des Büros am Unternehmensstandort des Benutzers. Maximale Länge: 128 Zeichen.|Ja|Nein|Persistent, Ausgabe|
|postalCode      |String|Die Postleitzahl der Anschrift des Benutzers. Die Postleitzahl ist spezifisch für das Land oder die Region des Benutzers. In den USA enthält dieses Attribut die Postleitzahl. Maximale Länge: 40 Zeichen.|Ja|Nein|Persistent, Ausgabe|
|preferredLanguage    |String|Die bevorzugte Sprache des Benutzers. Sollte den ISO 639-1-Codes entsprechen. Beispiel:„ en-US“.|Nein|Nein|Persistent, Ausgabe|
|refreshTokensValidFromDateTime|Datetime|Alle vor diesem Zeitpunkt ausgestellten Aktualisierungstoken sind ungültig, und Anwendungen erhalten eine Fehlermeldung, wenn ein ungültiges Aktualisierungstoken zum Abrufen eines neuen Zugriffstokens verwendet wird. Wenn dieser Fall eintritt, muss die Anwendung ein neues Aktualisierungstoken abrufen, indem eine Anforderung an den Autorisierungsendpunkt gesendet wird. Schreibgeschützt.|Nein|Nein|Output|
|signInNames ([Identities](manage-user-accounts-graph-api.md#identities-property)) |String|Der eindeutige Anmeldename des Benutzers des lokalen Kontos eines beliebigen Typs im Verzeichnis. Verwenden Sie dieses Attribut, um einen Benutzer mit einem Anmeldewert abzurufen, ohne den lokalen Kontotyp anzugeben.|Nein|Nein|Eingabe|
|signInNames.userName ([Identities](manage-user-accounts-graph-api.md#identities-property)) |String|Der eindeutige Benutzername des Benutzers des lokalen Kontos im Verzeichnis. Verwenden Sie dieses Attribut, um einen Benutzer mit einem bestimmten Anmeldebenutzernamen zu erstellen oder abzurufen. Wenn Sie nur dieses Attribut in „PersistedClaims“ während eines Patchvorgangs angeben, werden andere Typen von „signInNames“ entfernt. Wenn Sie einen neuen Typ von „signInNames“ hinzufügen möchten, müssen Sie auch vorhandene „signInNames“ beibehalten.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|signInNames.phoneNumber ([Identities](manage-user-accounts-graph-api.md#identities-property)) |String|Die eindeutige Telefonnummer des Benutzers des lokalen Kontos im Verzeichnis. Verwenden Sie dieses Attribut, um einen Benutzer mit einer bestimmten Telefonnummer für die Anmeldung zu erstellen oder abzurufen. Wenn Sie nur dieses Attribut in „PersistedClaims“ während eines Patchvorgangs angeben, werden andere Typen von „signInNames“ entfernt. Wenn Sie einen neuen Typ von „signInNames“ hinzufügen möchten, müssen Sie auch vorhandene „signInNames“ beibehalten.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|signInNames.emailAddress ([Identities](manage-user-accounts-graph-api.md#identities-property))|String|Die eindeutige E-Mail-Adresse des Benutzers des lokalen Kontos im Verzeichnis. Verwenden Sie dieses Attribut, um einen Benutzer mit einer bestimmten E-Mail-Adresse für die Anmeldung zu erstellen oder abzurufen. Wenn Sie nur dieses Attribut in „PersistedClaims“ während eines Patchvorgangs angeben, werden andere Typen von „signInNames“ entfernt. Wenn Sie einen neuen Typ von „signInNames“ hinzufügen möchten, müssen Sie auch vorhandene „signInNames“ beibehalten.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|state           |String|Das Bundesland oder der Kanton in der Adresse des Benutzers. Maximale Länge: 128 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|streetAddress   |String|Die Straße in der geschäftlichen Anschrift des Benutzers. Maximale Länge: 1024 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|Die sekundäre Telefonnummer des Benutzers, die für die mehrstufige Authentifizierung verwendet wird.|Ja|Nein|Persistent, Ausgabe|
|strongAuthenticationEmailAddress<sup>1</sup>|String|Die SMTP-Adresse für den Benutzer. Beispiel: „bob@contoso.com“. Dieses Attribut wird für die Anmeldung mit der Benutzernamenrichtlinie verwendet, um die E-Mail-Adresse des Benutzers zu speichern. Die E-Mail-Adresse wird dann in einem Kennwortzurücksetzungsflow verwendet.|Ja|Nein|Persistent, Ausgabe|
|strongAuthenticationPhoneNumber<sup>2</sup>|String|Die primäre Telefonnummer des Benutzers, die für die mehrstufige Authentifizierung verwendet wird.|Ja|Nein|Persistent, Ausgabe|
|surname         |String|Der Nachname (oder Familienname) des Benutzers. Maximale Länge: 64 Zeichen.|Ja|Ja|Persistent, Ausgabe|
|telephoneNumber (erster Eintrag von „businessPhones“)|String|Die primäre geschäftliche Telefonnummer des Benutzers.|Ja|Nein|Persistent, Ausgabe|
|userPrincipalName    |String|Der Benutzerprinzipalname (User Principal Name, UPN) des Benutzers. Der UPN ist ein Anmeldename im Internetformat für den Benutzer basierend auf dem Internetstandard RFC 822. Die Domäne muss in der Sammlung der überprüften Domänen des Mandanten enthalten sein. Diese Eigenschaft ist beim Erstellen eines Kontos erforderlich. Unveränderlich.|Nein|Nein|Eingabe, Persistent, Ausgabe|
|usageLocation   |String|Erforderlich für Benutzer, denen aufgrund gesetzlicher Anforderungen Lizenzen zugewiesen werden, um die Verfügbarkeit von Diensten in den jeweiligen Ländern/Regionen zu überprüfen. Lässt keine NULL-Werte zu. Ein aus zwei Buchstaben bestehender Länder-/Regionscode (ISO-Standard 3166). Beispiele: „US“, „JP“ und „GB“.|Ja|Nein|Persistent, Ausgabe|
|userType        |String|Ein Zeichenfolgenwert, der zum Klassifizieren von Benutzertypen in Ihrem Verzeichnis verwendet werden kann. Der Wert muss „Member“ (Mitglied) lauten. Schreibgeschützt.|Nur Lesezugriff|Nein|Persistent, Ausgabe|
|userState (externalUserState)<sup>3</sup>|String|Gilt nur für das Azure AD B2B-Konto. Gibt an, ob der Status der Einladung „PendingAcceptance“ (Annahme ausstehend) oder „Accepted“ (Angenommen) lautet.|Nein|Nein|Persistent, Ausgabe|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|Zeigt den Zeitstempel der letzten Änderung der „UserState“-Eigenschaft an.|Nein|Nein|Persistent, Ausgabe|

<sup>1 </sup>Wird von Microsoft Graph nicht unterstützt<br><sup>2 </sup>Weitere Informationen finden Sie unter [MFA-Attribut „Telefonnummer“](#mfa-phone-number-attribute)<br><sup>3 </sup>Sollte mit Azure AD B2C nicht verwendet werden

## <a name="mfa-phone-number-attribute"></a>MFA-Attribut „Telefonnummer“

Wenn Sie für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ein Telefon verwenden, wird die Benutzeridentität anhand des Mobiltelefons überprüft. Wenn Sie programmgesteuert eine Telefonnummer [neu hinzufügen](https://docs.microsoft.com/graph/api/authentication-post-phonemethods), [aktualisieren](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update), [abrufen](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get) oder [löschen](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete) möchten, verwenden Sie die [Authentifizierungsmethode per Telefon](https://docs.microsoft.com/graph/api/resources/phoneauthenticationmethod) der Microsoft Graph-API.

In den [benutzerdefinierten Richtlinien](custom-policy-overview.md) von Azure AD B2C ist die Telefonnummer über den Anspruchstyp `strongAuthenticationPhoneNumber` verfügbar.

## <a name="extension-attributes"></a>Erweiterungsattribute

Sie müssen häufig Ihre eigenen Attribute erstellen, beispielsweise in den folgenden Fällen:

- Für eine Kundenanwendung muss ein Attribut (z. B. **LoyaltyNumber**) beibehalten werden.
- Ein Identitätsanbieter verfügt über eine eindeutige Benutzer-ID, die gespeichert werden muss, z.B. **uniqueUserGUID**.
- Für eine benutzerdefinierte User Journey muss ein Status eines Benutzers (z. B. **migrationStatus**) beibehalten werden.

Azure AD B2C erweitert die für die einzelnen Benutzerkonten gespeicherten Attribute. Erweiterungsattribute [erweitern das Schema](/graph/extensibility-overview#schema-extensions) der Benutzerobjekte im Verzeichnis. Die Erweiterungsattribute können nur für ein Anwendungsobjekt registriert werden, obwohl sie Daten für einen Benutzer enthalten können. Das Erweiterungsattribut wird an die Anwendung namens b2c-extensions-app angefügt. Nehmen Sie keine Änderungen an dieser Anwendung vor. Sie wird von Azure AD B2C zum Speichern von Benutzerdaten verwendet. Sie finden diese Anwendung in Azure Active Directory unter „App-Registrierungen“.

> [!NOTE]
> - Für jedes Benutzerkonto können bis zu 100 Erweiterungsattribute geschrieben werden.
> - Beim Löschen der Anwendung b2c-extensions-app werden diese Erweiterungsattribute zusammen mit allen darin enthaltenen Daten von allen Benutzern entfernt.
> - Wird ein Erweiterungsattribut von der Anwendung gelöscht, wird es aus allen Benutzerkonten entfernt, und die Werte werden gelöscht.
> - Der zugrunde liegende Name des Erweiterungsattributs wird im Format „extension_Anwendungs-ID_Attributname“ generiert. Wenn Sie z. B. das Erweiterungsattribut „LoyaltyNumber“ erstellen und b2c-extensions-app die Anwendungs-ID „831374b3-bd50-41bf-AA54-263ec9e050fc“ aufweist, lautet der Name des zugrunde liegenden Erweiterungsattributs „extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber“. Sie verwenden den zugrunde liegenden Namen, wenn Sie Graph-API-Abfragen zum Erstellen oder Aktualisieren von Benutzerkonten ausführen.

Für das Definieren einer Eigenschaft in einer Schemaerweiterung werden die folgenden Datentypen unterstützt:

|Eigenschaftstyp |Bemerkungen  |
|--------------|---------|
|Boolean    | Mögliche Werte sind **true** und **false**. |
|Datetime   | Muss im ISO 8601-Format angegeben werden. Wird in UTC gespeichert.   |
|Integer    | 32-Bit-Wert.               |
|String     | Maximal 256 Zeichen.     |

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Erweiterungsattribute:
- [Schemaerweiterungen](/graph/extensibility-overview#schema-extensions)
- [Definieren von benutzerdefinierten Attributen in einem Benutzerflow](user-flow-custom-attributes.md)
- [Definieren von benutzerdefinierten Attributen in einer benutzerdefinierten Richtlinie](custom-policy-custom-attributes.md)