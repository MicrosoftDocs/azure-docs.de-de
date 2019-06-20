---
title: Definieren eines technischen Validierungsprofils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein technisches Azure Active Directory-Profil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f20c3c6d6821b5a8bbdb74101095431f6f7f18f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511907"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Validierungsprofils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ein technisches Validierungsprofil ist ein ganz einfaches technisches Profil aus jedem beliebigen Protokoll, z. B. [Azure Active Directory](active-directory-technical-profile.md) oder einer [REST-API](restful-technical-profile.md). Das technische Validierungsprofil gibt Ausgabeansprüche oder eine Fehlermeldung „HTTP 409“ (Konfliktantwort-Statuscode) mit den folgenden Daten zurück:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Ansprüche, die von einem technischen Validierungsprofil zurückgegeben werden, werden wieder dem Anspruchsbehälter hinzugefügt. Sie können solche Ansprüche in den nächsten technischen Validierungsprofilen verwenden.

Technische Validierungsprofile werden in der Reihenfolge ausgeführt, in der sie im **ValidationTechnicalProfiles**-Element angezeigt werden. Sie können in einem technischen Validierungsprofil konfigurieren, ob die Ausführung jeglicher nachfolgender technischer Validierungsprofile fortgesetzt werden soll, wenn das technische Validierungsprofil einen Fehler auslöst oder erfolgreich ist.

Technische Validierungsprofile können bedingt ausgeführt werden, basierend auf Vorbedingungen, die im **ValidationTechnicalProfiles**-Element definiert sind. Beispielsweise können Sie überprüfen, ob ein bestimmter Anspruch vorhanden ist, oder ob ein Anspruch gleich oder ungleich dem angegebenen Wert ist.

Ein selbstbestätigtes technisches Profil kann definieren, dass ein technisches Validierungsprofil verwendet wird, um einige oder alle seiner Ausgabeansprüche zu validieren. Alle Eingabeansprüche des technischen Profils, auf das verwiesen wird, müssen in den Ausgabeansprüchen des verweisenden technischen Validierungsprofils enthalten sein.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Das **ValidationTechnicalProfiles**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Ein technisches Profil, mit dem einige oder alle Ausgabeansprüche des verweisenden technischen Profils überprüft werden sollen. |

Das **ValidationTechnicalProfile**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner eines technischen Profils, das bereits in der Richtlinie oder der übergeordneten Richtlinie definiert ist. |
|ContinueOnError|Nein| Zeigt an, ob die Validierung jeglicher nachfolgender technischer Validierungsprofile fortgesetzt werden soll, wenn das technische Validierungsprofil einen Fehler auslöst. Mögliche Werte: `true` oder `false` (Standard, die Verarbeitung weiterer Validierungsprofile wird beendet und ein Fehler zurückgegeben). |
|ContinueOnSuccess | Nein | Zeigt an, ob die Validierung jeglicher nachfolgender Validierungsprofile fortgesetzt werden soll, wenn das technische Validierungsprofil erfolgreich ist. Mögliche Werte: `true` oder `false`. Der Standardwert ist `true`, was bedeutet, dass die Verarbeitung weiterer Validierungsprofile fortgesetzt wird. |

Das **ValidationTechnicalProfile**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | Eine Liste von Vorbedingungen, die für die Ausführung des technischen Validierungsprofil erfüllt sein müssen. |

Das **Precondition**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| `Type` | Ja | Der Typ der Überprüfung oder Abfrage, die für die Vorbedingung ausgeführt werden soll. Entweder wird `ClaimsExist` angegeben, um sicherzustellen, dass Aktionen ausgeführt werden, wenn die angegebenen Ansprüche im aktuellen Satz von Ansprüchen des Benutzers vorhanden sind, oder es wird `ClaimEquals` angegeben, damit die Aktionen ausgeführt werden, wenn der angegebene Anspruch vorhanden ist und sein Wert gleich dem angegebenen Wert ist. |
| `ExecuteActionsIf` | Ja | Zeigt an, ob die Aktionen in der Vorbedingung ausgeführt werden sollen, wenn der Test „true“ oder „false“ ist. |

Das **Precondition**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Wert | 1:n | Die Daten, die von der Überprüfung verwendet werden. Wenn der Typ dieser Überprüfung `ClaimsExist` ist, gibt dieses Feld eine ClaimTypeReferenceId an, die abzufragen ist. Wenn der Typ der Überprüfung `ClaimEquals` ist, gibt dieses Feld eine ClaimTypeReferenceId an, die abzufragen ist. Während ein anderes Wertelement den zu überprüfenden Wert enthält.|
| Aktion | 1:1 | Die Aktion, die ausgeführt werden soll, wenn die Überprüfung der Vorbedingung innerhalb eines Orchestrierungsschritts „true“ ist. Der Wert der **Aktion** wird auf `SkipThisValidationTechnicalProfile` festgelegt. Gibt an, dass das zugeordnete technische Validierungsprofil nicht ausgeführt werden soll. |

### <a name="example"></a>Beispiel

Im folgenden Beispiel werden diese technischen Validierungsprofile verwendet:

1. Das erste technische Validierungsprofil überprüft Benutzeranmeldeinformationen und wird nicht fortgesetzt, wenn ein Fehler auftritt, z. B. ein ungültiger Benutzername oder ein ungültiges Kennwort.
2. Das nächste technische Validierungsprofil wird nicht ausgeführt, wenn der „userType“-Anspruch nicht vorhanden ist oder wenn der Wert von „usertype“ `Partner` ist. Das technische Validierungsprofil versucht, das Benutzerprofil aus der internen Kundendatenbank zu lesen, und wird fortgesetzt, wenn ein Fehler auftritt, z.B. ein nicht verfügbarer REST-API-Dienst oder ein beliebiger interner Fehler.
3. Das letzte technische Validierungsprofil wird nicht ausgeführt, wenn der „userType“-Anspruch nicht vorhanden war oder wenn der Wert von „usertype“ `Customer` ist. Das technische Validierungsprofil versucht, das Benutzerprofil aus der internen Partnerdatenbank zu lesen, und wird fortgesetzt, wenn ein Fehler auftritt, z.B. ein nicht verfügbarer REST-API-Dienst oder ein beliebiger interner Fehler.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
