---
title: Erstellen von Benutzeroberflächen-Definitionselementen
description: Hier werden die Elemente beschrieben, mit denen Benutzeroberflächendefinitionen für das Azure-Portal erstellt werden.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 3e8bdd85c112d90fd5e10f6e65e48ca6b3f71b6e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410741"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition-Elemente

In diesem Artikel werden das Schema und die Eigenschaften für alle unterstützten Elemente eines CreateUiDefinition-Elements beschrieben.

## <a name="schema"></a>Schema

Das Schema für die meisten Elemente ist wie folgt:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Eigenschaft | Erforderlich | BESCHREIBUNG |
| -------- | -------- | ----------- |
| name | Ja | Ein interner Bezeichner für den Verweis auf eine bestimmte Instanz eines Elements. Am häufigsten wird der Elementname in `outputs` verwendet. Dabei werden die Ausgabewerte der angegebenen Elemente den Parametern der Vorlage zugeordnet. Sie können mit ihm auch den Ausgabewert eines Elements an `defaultValue` eines anderen Elements binden. |
| type | Ja | Das Benutzeroberflächensteuerelement, das für das Element gerendert werden soll. Eine Liste der unterstützten Typen finden Sie unter [Elemente](#elements). |
| label | Ja | Der Anzeigetext des Elements. Einige Elementtypen enthalten mehrere Bezeichnungen. Der Wert kann also ein Objekt sein, das mehrere Zeichenfolgen enthält. |
| defaultValue | Nein | Der Standardwert des Elements. Einige Elementtypen unterstützen komplexe Standardwerte. Der Wert kann also ein Objekt sein. |
| toolTip | Nein | Der Text, der in der QuickInfo des Elements angezeigt werden soll. Ähnlich wie bei `label` unterstützen einige Elemente mehrere QuickInfo-Zeichenfolgen. Inlinelinks können mithilfe von Markdownsyntax eingebettet werden.
| constraints | Nein | Eigenschaften, die zum Anpassen des Überprüfungsverhaltens des Elements verwendet werden. Die unterstützten Eigenschaften für Einschränkungen variieren je nach Elementtyp. Einige Elementtypen unterstützen die Anpassung des Überprüfungsverhaltens nicht und enthalten daher keine constraints-Eigenschaft. |
| Optionen | Nein | Zusätzliche Eigenschaften, die das Verhalten des Elements anpassen. Ähnlich wie bei `constraints` variieren die unterstützten Eigenschaften je nach Elementtyp. |
| visible | Nein | Gibt an, ob das Element angezeigt wird. Ist `true` angegeben, werden das Element und entsprechende untergeordnete Elemente angezeigt. Standardwert: `true`. Verwenden Sie [logische Funktionen](create-uidefinition-functions.md#logical-functions), um den Wert dieser Eigenschaft dynamisch zu steuern.

## <a name="elements"></a>Elemente

Die Dokumentation für die einzelnen Elemente enthält ein Benutzeroberflächenbeispiel, ein Schema, Hinweise zum Verhalten des Elements (in der Regel in Bezug auf Überprüfung und unterstützte Anpassung) und eine Beispielausgabe.

- [Microsoft.Common.CheckBox](microsoft-common-checkbox.md)
- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.EditableGrid](microsoft-common-editablegrid.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.ServicePrincipalSelector](microsoft-common-serviceprincipalselector.md)
- [Microsoft.Common.Slider](microsoft-common-slider.md)
- [Microsoft.Common.TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.KeyVault.KeyVaultCertificateSelector](microsoft-keyvault-keyvaultcertificateselector.md)
- [Microsoft.ManagedIdentity.IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Solutions.ArmApiControl](microsoft-solutions-armapicontrol.md)
- [Microsoft.Solutions.ResourceSelector](microsoft-solutions-resourceselector.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)
- [Microsoft.Storage.StorageBlobSelector](microsoft-storage-storageblobselector.md)

## <a name="next-steps"></a>Nächste Schritte

Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
