---
title: JavaScript-Beispiele – Azure Active Directory B2C | Microsoft-Dokumentation
description: Hier finden Sie Informationen zur Verwendung von JavaScript in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9e19df7c50ca9d2c57ab385a567f4911b200c5e2
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510889"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-Beispiele für die Verwendung in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Sie können Ihren Azure Active Directory (Azure AD) B2C-Anwendungen Ihren eigenen clientseitigen JavaScript-Code hinzufügen. Um JavaScript für Ihre Anwendungen zu aktivieren, müssen Sie ein Element zu Ihrer [benutzerdefinierten Richtlinie](active-directory-b2c-overview-custom.md) hinzufügen, einen [Seitenvertrag](page-contract.md) auswählen und [b2clogin.com](b2clogin.md) in Ihren Anforderungen verwenden. In diesem Artikel wird beschrieben, wie Sie Ihre benutzerdefinierte Richtlinie ändern können, um die Skriptausführung zu aktivieren.

> [!NOTE]
> Wenn Sie JavaScript für Benutzerflows aktivieren möchten, finden Sie Informationen unter [JavaScript und Seitenvertragsversionen in Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Wählen Sie einen Seitenvertrag für die Benutzeroberflächenelemente Ihrer Anwendung aus. Wenn Sie JavaScript verwenden möchten, müssen Sie eine Vertragsversion der Seite für alle Ihre Inhaltsdefinitionen in Ihrer benutzerdefinierten Richtlinie definieren.

## <a name="add-the-scriptexecution-element"></a>Hinzufügen des ScriptExecution-Elements

Sie aktivieren die Skriptausführung, indem Sie dem [RelyingParty](relyingparty.md)-Element das **ScriptExecution**-Element hinzufügen.

1. Öffnen Sie Ihre benutzerdefinierte Richtliniendatei. Beispiel: *SignUpOrSignin.xml*.
2. Fügen Sie das **ScriptExecution**-Element zum **UserJourneyBehaviors**-Element von **RelyingParty** hinzu:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Speichern Sie die Datei, und laden Sie sie hoch.

## <a name="guidelines-for-using-javascript"></a>Richtlinien für die Verwendung von JavaScript

Beachten Sie die folgenden Richtlinien, wenn Sie die Benutzeroberfläche Ihrer Anwendung mit JavaScript anpassen:

- Binden Sie kein Click-Ereignis an `<a>`-HTML-Elemente.
- Machen Sie sich nicht von Azure AD B2C-Code oder Kommentaren abhängig.
- Ändern Sie nicht die Reihenfolge oder die Hierarchie von Azure AD B2C-HTML-Elementen. Verwenden Sie eine Azure AD B2C-Richtlinie, um die Reihenfolge der Benutzeroberflächenelemente zu steuern.
- Sie können jeden RESTful-Dienst unter Berücksichtigung der folgenden Überlegungen aufrufen:
    - Möglicherweise müssen Sie die CORS-Einstellung für den RESTful-Dienst so festlegen, dass clientseitige HTTP-Aufrufe zulässig sind.
    - Stellen Sie sicher, dass Ihr RESTful-Dienst sicher ist und nur das Protokoll HTTPS verwendet.
    - Verwenden Sie JavaScript nicht direkt, um Azure AD B2C-Endpunkte aufzurufen.
- Sie können Ihren JavaScript-Code einbetten oder einen Link zu externen JavaScript-Dateien hinzufügen. Stellen Sie bei Verwendung einer externen JavaScript-Datei sicher, dass Sie die absolute URL und keine relative URL verwenden.
- JavaScript-Frameworks:
    - Azure AD B2C verwendet eine bestimmte Version von jQuery. Fügen Sie keine weitere Version von jQuery hinzu. Wenn mehr als eine Version auf derselben Seite verwendet wird, treten Probleme auf.
    - Die Verwendung von RequireJS wird nicht unterstützt.
    - Die meisten JavaScript-Frameworks werden von Azure AD B2C nicht unterstützt.
- Azure AD B2C-Einstellungen (wie die aktuelle Sprache der Benutzeroberfläche) können durch Aufrufen der Objekte `window.SETTINGS` und `window.CONTENT` gelesen werden. Ändern Sie nicht den Wert dieser Objekte.
- Verwenden Sie zum Anpassen der Azure AD B2C-Fehlermeldung Lokalisierung in einer Richtlinie.
- Wenn etwas durch die Verwendung einer Richtlinie erreicht werden kann, ist dies im Allgemeinen die empfohlene Methode.

## <a name="javascript-samples"></a>JavaScript-Beispiele

### <a name="show-or-hide-a-password"></a>Anzeigen oder Ausblenden eines Kennworts

Eine gängige Methode zur Unterstützung Ihrer Kunden bei der erfolgreichen Registrierung besteht darin, zuzulassen, dass sie sehen können, was sie als Kennwort eingegeben haben. Diese Option unterstützt die Benutzer bei der Registrierung, indem die Benutzer auf einfache Weise ihr Kennwort sehen und bei Bedarf korrigieren können. Jedes Feld des Typs Kennwort verfügt über ein Kontrollkästchen mit der Bezeichnung **Kennwort anzeigen**.  Dadurch kann der Benutzer das Kennwort als Nur-Text anzeigen. Fügen Sie den folgenden Codeausschnitt in Ihre Registrierungs- oder Anmeldevorlage für eine selbstbestätigte Seite ein:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Hinzufügen von Nutzungsbedingungen

Fügen Sie den folgenden Code in die Seite ein, auf der Sie ein Kontrollkästchen für **Nutzungsbedingungen** hinzufügen möchten. Dieses Kontrollkästchen ist in der Regel auf Ihren Seiten für die Registrierung über ein lokales Konto und die Registrierung über ein Konto eines sozialen Netzwerks erforderlich.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Ersetzen Sie im Code den Platzhalter `termsOfUseUrl` durch den Link zu Ihren Nutzungsbedingungen. Erstellen Sie für Ihr Verzeichnis ein neues Benutzerattribut namens **termsOfUse**, und fügen Sie dann **termsOfUse** als Benutzerattribut ein.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie die Benutzeroberfläche Ihrer Anwendungen anpassen können, finden Sie unter [Anpassen der Benutzeroberfläche einer Anwendung mithilfe einer benutzerdefinierten Richtlinie in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
