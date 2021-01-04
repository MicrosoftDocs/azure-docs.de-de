---
title: Benutzeroberflächenelement „KeyVaultCertificateSelector“
description: Hier wird das Benutzeroberflächenelement „Microsoft.KeyVault.KeyVaultCertificateSelector“ für das Azure-Portal beschrieben.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101220"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Benutzeroberflächenelement „Microsoft.KeyVault.KeyVaultCertificateSelector“

Ein Steuerelement zum Auswählen eines Key Vault-Zertifikats.

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel

Der Benutzer hat die Möglichkeit, ein verfügbares Zertifikat auszuwählen.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector":::

Nach dem Auswählen von **Zertifikat auswählen** kann der Benutzer einen Schlüsseltresor und ein Zertifikat aus dem Schlüsseltresor angeben.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector: Auswählen eines Zertifikats":::

Das-Steuerelement wird aktualisiert, um den ausgewählten Schlüsseltresor und den Zertifikatnamen anzuzeigen.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector: Anzeigen des ausgewählten Zertifikats":::

## <a name="schema"></a>Schema

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Beispielausgabe

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
