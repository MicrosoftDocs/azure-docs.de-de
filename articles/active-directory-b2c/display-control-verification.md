---
title: Überprüfen von Ansprüchen mithilfe von Anzeigesteuerelementen
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie mithilfe von Azure AD B2C-Anzeigesteuerelementen die Ansprüche in den von Ihren benutzerdefinierten Richtlinien angegebenen User Journeys überprüfen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd90be6d93dc5ca399ac87daba0ca44fa7e88ff8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94532493"
---
# <a name="verification-display-control"></a>Anzeigesteuerselemente zur Überprüfung

Verwenden Sie ein [Anzeigesteuerelement](display-controls.md) zur Überprüfung, um einen Anspruch, z. B. eine E-Mail-Adresse oder Telefonnummer, mit einem an den Benutzer gesendeten Überprüfungscode zu überprüfen.

## <a name="verificationcontrol-actions"></a>VerificationControl-Aktionen

Das Anzeigesteuerelement zur Überprüfung umfasst zwei Schritte (Aktionen):

1. Anfordern eines Ziels vom Benutzer, z. B. eine E-Mail-Adresse oder Telefonnummer, an die der Überprüfungscode gesendet wird. Wenn der Benutzer die Schaltfläche **Code senden** auswählt, wird die **SendCode-Aktion** des Anzeigesteuerelements zur Überprüfung ausgeführt. Die **SendCode-Aktion** generiert einen Code, erstellt den zu sendenden Inhalt und sendet ihn an den Benutzer. Der Wert der Adresse kann vorausgefüllt werden und als zweistufige Authentifizierung dienen.

    ![Beispielseite für „Code senden“](media/display-control-verification/display-control-verification-email-action-01.png)

1. Nachdem der Code gesendet wurde, liest der Benutzer die Nachricht, gibt den Überprüfungscode in dem bereitgestellten Anzeigecodeelement ein und wählt **Code überprüfen** aus. Durch Auswählen von **Code überprüfen** wird die **VerifyCode-Aktion** ausgeführt, um den der Adresse zugeordneten Code zu überprüfen. Wenn der Benutzer **Neuen Code senden** auswählt, wird die erste Aktion erneut ausgeführt.

    ![Beispielseite für „Code überprüfen“](media/display-control-verification/display-control-verification-email-action-02.png)

## <a name="verificationcontrol-required-elements"></a>Erforderliche Elemente für VerificationControl

**VerificationControl** muss folgende Elemente enthalten:

- Der Typ von `DisplayControl` ist `VerificationControl`.
- `DisplayClaims`
  - **Senden an:** mindestens ein Anspruch, der angibt, wohin der Überprüfungscode gesendet wird. Beispiele: *E-Mail* oder *Landesvorwahl* und *Telefonnummer*.
  - **Überprüfungscode:** Anspruch des Überprüfungscodes, den der Benutzer angibt, nachdem der Code gesendet wurde. Dieser Anspruch muss als erforderlich und der `ControlClaimType` muss auf `VerificationCode` festgelegt werden.
- Ausgabeanspruch (optional), der an die selbstbestätigte Seite zurückgegeben wird, nachdem der Benutzer den Überprüfungsvorgang abgeschlossen hat, Beispiele: *E-Mail* oder *Landesvorwahl* und *Telefonnummer*. Im selbstbestätigten technischen Profil werden die Ansprüche verwendet, um die Daten zu speichern oder die Ausgabeansprüche zum nächsten Orchestrierungsschritt weiterzuleiten.
- Zwei `Action`-Elemente mit folgenden Namen:
  - **SendCode:** sendet einen Code an den Benutzer. Diese Aktion enthält normalerweise zwei technische Validierungsprofile, um einen Code zu generieren und zu senden.
  - **VerifyCode:** überprüft den Code. Diese Aktion enthält normalerweise ein einzelnes technisches Validierungsprofil.

Im folgenden Beispiel wird das Textfeld **E-Mail** auf der Seite angezeigt. Wenn der Benutzer seine E-Mail-Adresse eingibt und **Code senden** auswählt, wird die **SendCode**-Aktion am Azure AD B2C-Back-End ausgelöst.

Der Benutzer gibt dann den **Überprüfungscode** ein und wählt **Code überprüfen** aus, um die **VerifyCode**-Aktion am Back-End auszulösen. Wenn alle Validierungen erfolgreich durchgeführt wurden, gilt **VerificationControl** als abgeschlossen, und der Benutzer kann mit dem nächsten Schritt fortfahren.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
