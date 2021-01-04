---
title: Verwalten des Benutzerzugriffs in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure AD B2C in Ihrer Anwendung Minderjährige identifizieren, Geburtsdatum und Daten zu Land/Region erfassen und Akzeptanz von Nutzungsbedingungen abrufen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fadc739f16ce9690a735be22758f58857ff8b9ff
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951620"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Verwalten des Benutzerzugriffs in Azure Active Directory B2C

In diesem Artikel wird erläutert, wie Sie den Benutzerzugriff auf Ihre Anwendungen mithilfe von Azure Active Directory B2C (Azure AD B2C) verwalten können. Die Zugriffsverwaltung in Ihrer Anwendung umfasst Folgendes:

- Identifizieren von Minderjährigen und Steuern des Benutzerzugriffs auf Ihre Anwendung
- Anfordern der Zustimmung durch die Eltern, wenn Minderjährige Ihre Anwendungen nutzen möchten
- Erfassen des Geburtsdatums und von Daten zu Land/Region von Benutzern.
- Einholen der Zustimmung zu den Nutzungsbedingungen und Durchführen der Alterskontrolle

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Steuern des Zugriffs durch Minderjährige

Für Anwendungen und Organisationen kann die Entscheidung getroffen werden, für Minderjährige die Nutzung von Anwendungen und Diensten, die nicht für diese Zielgruppe bestimmt sind, zu sperren. Alternativ hierzu kann für Anwendungen und Organisationen auch entschieden werden, Minderjährige zuzulassen und dann die Erteilung der Zustimmung durch die Eltern zu verwalten und zulässige Benutzeroberflächen bereitzustellen, die gemäß den Geschäftsregeln und den Regulierungsbestimmungen zulässig sind.

Wenn ein Benutzer als minderjährig identifiziert wird, können Sie für den Benutzerflow in Azure AD B2C eine von drei Optionen auswählen:

- **Signiertes JWT-ID-Token an die Anwendung zurücksenden**: Der Benutzer wird im Verzeichnis registriert, und ein Token wird an die Anwendung zurückgegeben. Die Anwendung wird dann durch Anwenden von Geschäftsregeln fortgesetzt. Beispielsweise kann die Anwendung mit einem Prozess fortfahren, bei dem die Zustimmung der Eltern eingeholt wird. Zur Verwendung dieser Methode geben Sie an, dass Sie die Ansprüche **ageGroup** und **consentProvidedForMinor** von der Anwendung erhalten möchten.

- **Nicht signiertes JSON-Token an die Anwendung senden**: Azure AD B2C benachrichtigt die Anwendung darüber, dass der Benutzer minderjährig ist, und stellt den Zustimmungsstatus der Eltern für den Benutzer bereit. Die Anwendung wird dann durch Anwenden von Geschäftsregeln fortgesetzt. Ein JSON-Token führt keine erfolgreiche Authentifizierung bei der Anwendung durch. Die Anwendung muss den nicht authentifizierten Benutzer gemäß den Ansprüchen verarbeiten, die im JSON-Token enthalten sind, z.B. **name**, **email**, **ageGroup** und **consentProvidedForMinor**.

- **Benutzer blockieren**: Wenn ein Benutzer minderjährig ist und die Zustimmung der Eltern nicht erteilt wurde, kann Azure AD B2C den Benutzer benachrichtigen, dass er blockiert ist. Es wird kein Token ausgestellt, der Zugriff wird blockiert, und das Benutzerkonto wird während des Registrierungsvorgangs nicht erstellt. Zur Implementierung dieser Benachrichtigung stellen Sie eine geeignete HTML/CSS-Inhaltsseite bereit, um den Benutzer zu informieren und die entsprechenden Optionen anzuzeigen. Für die Anwendung sind bei neuen Registrierungen keine weiteren Aktionen erforderlich.

## <a name="get-parental-consent"></a>Einholen der Zustimmung der Eltern

Je nach Regulierung der Anwendung muss unter Umständen die Zustimmung durch einen Benutzer erteilt werden, der als erwachsene Person verifiziert wurde. Azure AD B2C enthält keine Benutzeroberfläche zur Verifizierung des Alters einer Person und zur Erteilung der Zustimmung durch eine verifizierte erwachsene Person für einen minderjährigen Benutzer. Diese Benutzeroberfläche muss von der Anwendung oder einem anderen Dienstanbieter bereitgestellt werden.

Hier ist ein Beispiel für einen Benutzerflow zum Einholen der elterlichen Zustimmung angegeben:

1. Bei einem Vorgang der [Microsoft Graph-API](/graph/use-the-api) wird der Benutzer als minderjährig identifiziert, und die Benutzerdaten werden in Form eines nicht signierten JSON-Tokens an die Anwendung zurückgegeben.

2. Die Anwendung verarbeitet das JSON-Token und zeigt einen Hinweis für die minderjährige Person an, dass die Zustimmung der Eltern erforderlich ist. Die Zustimmung eines Elternteils wird dann online angefordert.

3. In Azure AD B2C wird ein Anmeldeprozess angezeigt, über den sich der Benutzer auf normale Weise anmelden kann, und es wird ein Token für die Anwendung ausgegeben, das **legalAgeGroupClassification = "minorWithParentalConsent"** enthält. Die Anwendung erfasst die E-Mail-Adresse eines Elternteils und prüft, ob es sich bei dieser Person um einen Erwachsenen handelt. Zu diesem Zweck wird eine vertrauenswürdige Quelle genutzt, z.B. Einwohnermeldeamt-Daten, Lizenzverifizierung oder Kreditkartennachweis. Wenn die Überprüfung erfolgreich durchgeführt wurde, wird die minderjährige Person in der Anwendung aufgefordert, sich über den Azure AD B2C-Benutzerflow anzumelden. Falls die Zustimmung verweigert wird (z. B. **legalAgeGroupClassification = “minorWithoutParentalConsent”** ), gibt Azure AD B2C ein JSON-Token (keine Anmeldedaten) an die Anwendung zurück, um den Zustimmungsprozess neu zu starten. Der Benutzerflow kann optional angepasst werden, damit eine minderjährige oder erwachsene Person Zugriff auf das Konto eines Minderjährigen erlangen kann. Hierzu wird ein Registrierungscode an die hinterlegte E-Mail-Adresse des Minderjährigen oder des Erwachsenen gesendet.

4. Die Anwendung zeigt für die minderjährige Person eine Option an, mit der die Zustimmung widerrufen werden kann.

5. Wenn entweder die minderjährige oder die erwachsene Person die Zustimmung widerruft, kann die Microsoft Graph-API verwendet werden, um **consentProvidedForMinor** in **denied** zu ändern. Alternativ hierzu kann für die Anwendung auch die Entscheidung getroffen werden, eine minderjährige Person zu löschen, für die die Zustimmung widerrufen wurde. Optional ist es möglich, den Benutzerflow so anzupassen, dass die authentifizierte minderjährige Person (oder ein Elternteil, der das Konto der minderjährigen Person verwendet) die Zustimmung widerrufen kann. Azure AD B2C zeichnet **consentProvidedForMinor** als **denied** auf.

Weitere Informationen zu **legalAgeGroupClassification**, **consentProvidedForMinor** und **ageGroup** finden Sie unter [User resource type](/graph/api/resources/user) (Benutzerressourcentyp). Weitere Informationen zu benutzerdefinierten Attributen finden Sie unter [Verwenden benutzerdefinierter Attribute zum Erfassen von Informationen über Ihre Kunden](user-flow-custom-attributes.md). Bei der Adressierung von erweiterten Attributen mit der Microsoft Graph-API müssen Sie die lange Version des Attributs verwenden, z. B.*extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Erfassen des Geburtsdatums und von Daten zu Land/Region von Benutzern

Anwendungen können sich beim Erfassen des Geburtsdatums und von Land/Region-Informationen von allen Benutzern während der Registrierung auf Azure AD B2C verlassen. Falls diese Informationen nicht bereits vorhanden sind, kann die Anwendung sie während des nächsten Authentifizierungsprozesses (Anmeldung) vom Benutzer anfordern. Benutzer können nicht fortfahren, ohne ihr Geburtsdatum und Land/Region-Informationen anzugeben. Azure AD B2C verwendet die Informationen, um festzustellen, ob die Person gemäß der gesetzlichen Vorschriften dieses Landes / dieser Region minderjährig ist.

Ein benutzerdefinierter Benutzerflow ermöglicht das Erfassen von Geburtsdatum und Land/Region-Informationen und kann mit der Azure AD B2C-Anforderungstransformation die **ageGroup** bestimmen und das Ergebnis im Verzeichnis speichern (oder Geburtsdatum und Land/Region-Informationen direkt speichern).

In den folgenden Schritten wird die Logik veranschaulicht, mit der aus dem Geburtsdatum des Benutzers die Altersgruppe (**ageGroup**) berechnet wird:

1. Es wird versucht, das Land bzw. die Region anhand des Landes-/Regionscodes in der Liste zu ermitteln. Wenn das Land bzw. die Region nicht gefunden wird, wird auf **Default** (Standard) zurückgegriffen.

2. Bei Vorhandensein des Knotens **MinorConsent** im Länder-/Regionselement:

    a. Es wird das Datum berechnet, an dem der Benutzer geboren sein muss, um als erwachsene Person zu gelten. Wenn der 14. März 2015 z.B. das aktuelle Datum ist und für **MinorConsent** der Wert 18 gilt, kann das Geburtsdatum nicht nach dem 14. März 2000 liegen.

    b. Das Mindestdatum für die Geburt wird mit dem tatsächlichen Geburtsdatum verglichen. Falls das Mindestdatum für die Geburt vor dem Geburtstag des Benutzers liegt, wird für die Berechnung der Altersgruppe das Ergebnis **Minor** zurückgegeben.

3. Falls der Knoten **MinorNoConsentRequired** im Länder-/Regionselement enthalten ist, können Sie die Schritte 2a und 2b wiederholen, indem Sie den Wert aus **MinorNoConsentRequired** verwenden. Die Ausgabe von 2b gibt **MinorNoConsentRequired** zurück, falls das Mindestdatum für die Geburt vor dem Geburtsdatum des Benutzers liegt.

4. Wenn für keine Berechnung TRUE zurückgegeben wurde, wird für die Berechnung **Adult** zurückgegeben.

Wenn eine Anwendung zuverlässig Geburtsdatum oder Land/Region-Informationen mittels anderer Methoden gesammelt hat, kann die Anwendung die Graph-API verwenden, um den Benutzerdatensatz mit diesen Informationen zu aktualisieren. Beispiel:

- Wenn für einen Benutzer bekannt ist, dass es sich um eine erwachsene Person handelt, wird das Verzeichnisattribut **ageGroup** auf den Wert **Adult** aktualisiert.
- Falls ein Benutzer sicher als minderjährig eingestuft wird, wird das Verzeichnisattribut **ageGroup** auf den Wert **Minor** aktualisiert und **consentProvidedForMinor** entsprechend festgelegt.

Weitere Informationen zum Erfassen des Geburtsdatums finden Sie unter [Verwenden einer Altersbeschränkung in Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Erfassen der Zustimmung zu den Nutzungsbedingungen

Beim Entwickeln Ihrer Anwendung erfassen Sie die Zustimmung des Benutzers zu den Nutzungsbedingungen normalerweise innerhalb der Anwendung, ohne dass das Benutzerverzeichnis in größerem Maße in diesen Vorgang einbezogen wird. Es kann aber ein Azure AD B2C-Benutzerflow verwendet werden, um die Zustimmung des Benutzers zu den Nutzungsbedingungen einzuholen, den Zugriff bei nicht erteilter Zustimmung zu beschränken und die Zustimmung zu zukünftigen Änderungen der Nutzungsbedingungen basierend auf dem Datum der letzten Zustimmung und dem Datum der letzten Version der Nutzungsbedingungen durchzusetzen.

Die **Nutzungsbedingungen** können auch die „Zustimmung zur Weitergabe von Daten an Dritte“ umfassen. Je nach den Bestimmungen und Geschäftsregeln im jeweiligen Land können Sie die Zustimmung eines Benutzers zu beiden Bedingungen zusammen einholen oder ermöglichen, dass der Benutzer nur einer Bedingung zustimmt.

In den folgenden Schritten wird beschrieben, wie Sie die Nutzungsbedingungen verwalten können:

1. Aufzeichnen der Zustimmung zu den Nutzungsbedingungen und des Zustimmungsdatums über die Graph-API und erweiterte Attribute. Dies kann sowohl über integrierte als auch über benutzerdefinierte Benutzerflows erfolgen. Es wird empfohlen, die Attribute **extension_termsOfUseConsentDateTime** und **extension_termsOfUseConsentVersion** zu erstellen und zu verwenden.

2. Erstellen Sie ein erforderliches Kontrollkästchen mit der Bezeichnung „Nutzungsbedingungen akzeptieren“, und zeichnen Sie das Ergebnis während der Registrierung auf. Dies kann sowohl über integrierte als auch über benutzerdefinierte Benutzerflows erfolgen.

3. In Azure AD B2C werden die Nutzungsbedingungen und die Zustimmung des Benutzers gespeichert. Sie können die Graph-API verwenden, um den Status eines Benutzers abzufragen, indem das Erweiterungsattribut gelesen wird, das zum Aufzeichnen der Antwort verwendet wurde, z.B. **termsOfUseTestUpdateDateTime**. Dies kann sowohl über integrierte als auch über benutzerdefinierte Benutzerflows erfolgen.

4. Anfordern der Zustimmung zu aktualisierten Nutzungsbedingungen, indem das Datum der Zustimmung mit dem Datum der letzten Version der Nutzungsbedingungen verglichen wird. Sie können die Daten nur über einen benutzerdefinierten Benutzerflow vergleichen. Verwenden Sie das erweiterte Attribut **extension_termsOfUseConsentDateTime**, und vergleichen Sie den Wert mit dem Anspruch von **termsOfUseTextUpdateDateTime**. Erzwingen Sie eine neue Zustimmung durch Anzeigen eines Bildschirms zur Identitätsbestätigung, wenn die Zustimmung veraltet ist. Blockieren Sie den Zugriff andernfalls per Richtlinienlogik.

5. Anfordern der Zustimmung zu aktualisierten Nutzungsbedingungen, indem die Versionsnummer der Zustimmung mit der zuletzt akzeptierten Versionsnummer verglichen wird. Sie können Versionsnummern nur über einen benutzerdefinierten Benutzerflow vergleichen. Verwenden Sie das erweiterte Attribut **extension_termsOfUseConsentDateTime**, und vergleichen Sie den Wert mit dem Anspruch von **extension_termsOfUseConsentVersion**. Erzwingen Sie eine neue Zustimmung durch Anzeigen eines Bildschirms zur Identitätsbestätigung, wenn die Zustimmung veraltet ist. Blockieren Sie den Zugriff andernfalls per Richtlinienlogik.

Sie können die Zustimmung zu den Nutzungsbedingungen in den folgenden Szenarien erfassen:

- Ein neuer Benutzer registriert sich. Die Nutzungsbedingungen werden angezeigt, und das Ergebnis der Zustimmung wird gespeichert.
- Ein Benutzer meldet sich an, der zuvor den neuesten bzw. aktiven Nutzungsbedingungen zugestimmt hat. Die Nutzungsbedingungen werden nicht angezeigt.
- Ein Benutzer meldet sich an, der den neuesten bzw. aktiven Nutzungsbedingungen noch nicht zugestimmt hat. Die Nutzungsbedingungen werden angezeigt, und das Ergebnis der Zustimmung wird gespeichert.
- Ein Benutzer meldet sich an, der bereits einer älteren Version der Nutzungsbedingungen zugestimmt hat, die jetzt auf die neueste Version aktualisiert werden. Die Nutzungsbedingungen werden angezeigt, und das Ergebnis der Zustimmung wird gespeichert.

In der folgenden Abbildung ist der empfohlene Benutzerflow dargestellt:

![Flussdiagramm des empfohlenen Benutzerflows für Zustimmungen](./media/manage-user-access/user-flow.png)

Hier ist ein Beispiel für die Zustimmung zu datumsbasierten Nutzungsbedingungen in einem Anspruch. Wenn der Anspruch `extension_termsOfUseConsentDateTime` älter als `2025-01-15T00:00:00` ist, erzwingen Sie eine neue Zustimmung, indem Sie den booleschen Anspruch `termsOfUseConsentRequired` aktivieren und einen selbstbestätigten Bildschirm anzeigen. 

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2025-01-15T00:00:00" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Hier ist ein Beispiel für die Zustimmung zu versionsbasierten Nutzungsbedingungen in einem Anspruch. Wenn der Anspruch `extension_termsOfUseConsentVersion` älter als `V1` ist, erzwingen Sie eine neue Zustimmung, indem Sie den booleschen Anspruch `termsOfUseConsentRequired` aktivieren und einen selbstbestätigten Bildschirm anzeigen.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Löschen und Exportieren von Benutzerdaten finden Sie unter [Verwalten von Benutzerdaten](manage-user-data.md).
- Ein Beispiel für eine benutzerdefinierte Richtlinie, die eine Eingabeaufforderung für Nutzungsbedingungen implementiert, finden Sie unter [Benutzerdefinierte B2C IEF-Richtlinie: Registrieren und Anmelden mit einer Eingabeaufforderung „Nutzungsbedingungen“](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).