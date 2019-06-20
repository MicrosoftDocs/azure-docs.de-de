---
title: 'Azure Active Directory B2C: BuildingBlocks | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das BuildingBlocks-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 67770661f0660b9a84d16bbbc7d86cfcbca2cfdf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511583"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Das **BuildingBlocks**-Element wird im [TrustFrameworkPolicy](trustframeworkpolicy.md)-Element hinzugefügt.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
 </BuildingBlocks>
```

Das **BuildingBlocks**-Element enthält die folgenden Elemente, die in der angegebenen Reihenfolge festgelegt werden müssen:

- [ClaimsSchema:](claimsschema.md) Definieren die Anspruchstypen, auf die als Teil der Richtlinie verwiesen werden kann. In diesem Element deklarieren Sie Ihre Anspruchstypen. Anspruchstypen funktioniert ähnlich wie Variablen in vielen anderen Programmiersprachen. Sie können den Anspruchstyp verwenden, um Daten vom Benutzer Ihrer Anwendung zu sammeln, Ansprüche von Identitätsanbietern sozialer Netzwerke zu empfangen, Daten aus einer benutzerdefinierten REST-API zu senden und zu empfangen oder beliebige interne Daten zu speichern, die von Ihrer benutzerdefinierten Richtlinie verwendet werden. 

- [Predicates und PredicateValidationsInput:](predicates.md) Ermöglichen Ihnen einen Überprüfungsprozess, mithilfe dessen Sie sicherstellen können, dass nur ordnungsgemäß formatierte Daten in einen Anspruch eingegeben werden.
 
- [ClaimsTransformations:](claimstransformations.md) Enthalten eine Liste von Anspruchstransformationen, die in Ihrer Richtlinie verwendet werden können.  Eine Anspruchstransformation konvertiert einen Anspruch in einen anderen. In der Anspruchstransformation geben Sie eine der folgenden Transformationsmethoden an: 
    - Ändern der Groß-/Kleinschreibung eines String-Anspruchs in die angegebene Schreibweise. Zum Beispiel Ändern einer Zeichenfolge von Kleinbuchstaben in Großbuchstaben.
    - Vergleichen von zwei Ansprüchen und Zurückgeben eines Anspruchs mit TRUE, wodurch angegeben wird, ob zwei Ansprüche übereinstimmen. Andernfalls wird FALSE zurückgegeben.
    - Erstellen eines String-Anspruchs aus dem angegebenen Parameter in der Richtlinie.
    - Erstellen einer zufälligen Zeichenfolge mithilfe des Zufallszahlengenerators.
    - Formatieren eines Anspruchs anhand der angegebenen Formatzeichenfolge. Diese Transformation verwenden die C#-Methode `String.Format`.

- [ContentDefinitions:](contentdefinitions.md) Enthält URLs für HTML5-Vorlagen zur Verwendung in Ihrer User Journey. In einer benutzerdefinierten Richtlinie definiert eine Inhaltsdefinition den URI der HTML5-Seite, die für einen angegebenen Schritt in der User Journey verwendet wird. Dies könnten beispielsweise die Seiten für das Registrieren oder Anmelden oder das Zurücksetzen des Kennworts oder Fehlerseiten sein. Sie können das Aussehen und Verhalten durch Überschreiben von LoadUri für die HTML5-Datei ändern. Alternativ können Sie neue Inhaltsdefinitionen gemäß Ihrer Anforderungen erstellen. Dieses Element kann einen Verweis auf lokalisierte Ressourcen in Form einer Lokalisierungs-ID enthalten.

- [Localization:](localization.md) Ermöglicht die Unterstützung mehrerer Sprachen. Die Unterstützung von Lokalisierung in Richtlinien ermöglicht Ihnen das Einrichten der Liste unterstützter Sprachen sowie das Auswählen einer Standardsprache. Sprachspezifische Zeichenfolgen und Sammlungen werden ebenfalls unterstützt.


