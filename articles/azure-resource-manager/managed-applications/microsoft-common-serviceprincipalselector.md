---
title: Benutzeroberflächenelement ServicePrincipalSelector
description: Hier wird das Benutzeroberflächenelement Microsoft.Common.ServicePrincipalSelector für das Azure-Portal beschrieben. Es enthält ein Steuerelement zum Auswählen einer Anwendung und ein Textfeld zum Eingeben eines Kennworts oder Zertifikatfingerabdrucks.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184449"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Benutzeroberflächenelement Microsoft.Common.ServicePrincipalSelector

Ein Steuerelement, mit dem Benutzer einen vorhandenen [Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) auswählen oder eine neue Anwendung registrieren können. Wenn Sie **Neu erstellen** auswählen, durchlaufen Sie die Schritte zum Registrieren einer neuen Anwendung. Wenn Sie eine vorhandene Anwendung auswählen, stellt das Steuerelement ein Textfeld zum Eingeben eines Kennworts oder Zertifikatfingerabdrucks bereit.

## <a name="ui-samples"></a>Benutzeroberflächenbeispiele

Sie können eine Standardanwendung verwenden, eine neue Anwendung erstellen oder eine vorhandene Anwendung verwenden.

### <a name="use-default-application-or-create-new"></a>Verwenden der Standardanwendung oder Erstellen einer neuen Anwendung

Die Standardansicht wird durch die Werte in der `defaultValue`-Eigenschaft festgelegt, und der **Dienstprinzipaltyp** ist auf **Neu erstellen** festgelegt. Wenn die `principalId`-Eigenschaft einen gültigen Globally Unique Identifier (GUID) enthält, sucht das Steuerelement nach der `objectId` der Anwendung. Der Standardwert ist gültig, wenn der Benutzer keine Auswahl im Steuerelement trifft.

Wenn Sie eine neue Anwendung registrieren möchten, wählen Sie **Auswahl ändern** aus. Das Dialogfeld **Anwendung registrieren** wird angezeigt. Geben Sie einen **Namen** und **unterstützten Kontotyp** ein, und wählen Sie die Schaltfläche **Registrieren** aus.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Microsoft.Common.ServicePrincipalSelectoder anfängliche Ansicht.":::

Nachdem Sie eine neue Anwendung registriert haben, verwenden Sie **Authentifizierungstyp**, um ein Kennwort oder einen Zertifikatfingerabdruck einzugeben.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Microsoft.Common.ServicePrincipalSelectoder Authentifizierung.":::

### <a name="use-existing-application"></a>Verwenden einer vorhandenen Anwendung

Um eine vorhandene Anwendung zu verwenden, wählen Sie **Vorhandene auswählen** und dann **Auswahl treffen** aus. Verwenden Sie das Dialogfeld **Anwendung auswählen**, um nach dem Namen der Anwendung zu suchen. Wählen Sie in den Ergebnissen die gewünschte Anwendung aus, und klicken Sie dann auf die Schaltfläche **Auswählen**. Nachdem Sie eine Anwendung ausgewählt haben, wird **Authentifizierungstyp** im Steuerelement angezeigt. Dort können Sie ein Kennwort oder einen Zertifikatfingerabdruck eingeben.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft.Common.ServicePrincipalSelectoder vorhandene Anwendung auswählen.":::

## <a name="schema"></a>Schema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Bemerkungen

- Die erforderlichen Eigenschaften lauten wie folgt:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Gibt die Standardwerte `principalId` und `name` an.

- Die optionalen Eigenschaften lauten wie folgt:
  - `toolTip`: Fügt jeder Bezeichnung eine QuickInfo `infoBalloon` an.
  - `visible`: Ausblenden oder Anzeigen des Steuerelements.
  - `options`: Gibt an, ob die Zertifikatsfingerabdruck-Option zur Verfügung gestellt werden soll.
  - `constraints`: RegEx-Einschränkungen für die Kennwortüberprüfung.

## <a name="example"></a>Beispiel

Das Folgende ist ein Beispiel für das `Microsoft.Common.ServicePrincipalSelector`-Steuerelement. Mit der `defaultValue`-Eigenschaft wird `principalId` als Platzhalter für eine standardmäßige Anwendungsbezeichner-GUID auf `<default guid>` festgelegt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Beispielausgabe

Die `appId` ist die ID der Anwendungsregistrierung, die Sie ausgewählt oder erstellt haben. Die `objectId` ist ein Array von Objekt-IDs für die Dienstprinzipale, die für die ausgewählte Anwendungsregistrierung konfiguriert sind.

Wenn im Steuerelement keine Auswahl getroffen wird, lautet der `newOrExisting`-Eigenschaftswert **new**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Wenn **Neu erstellen** oder eine vorhandene Anwendung im Steuerelement ausgewählt wird, lautet der `newOrExisting`-Eigenschaftswert **existing**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
- Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).