---
title: Benutzeroberflächenelement „StorageBlobSelector“
description: Hier wird das Benutzeroberflächenelement „Microsoft.Storage.StorageBlobSelector“ für das Azure-Portal beschrieben.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754260"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Benutzeroberflächenelement „Microsoft.Storage.StorageBlobSelector“

Dies ist ein Steuerelement zum Auswählen eines Blobs aus einem Azure-Speicherkonto.

## <a name="ui-sample"></a>Benutzeroberflächenbeispiel

Dem Benutzer wird die Option angezeigt, nach verfügbaren Speicherblobs zu suchen.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft.Storage.StorageBlobSelector – Durchsuchen":::

Nachdem der Benutzer auf **Durchsuchen** geklickt hat, kann er ein Speicherkonto auswählen.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft.Storage.StorageBlobSelector – Auswählen des Speichers":::

Der Benutzer sieht die Container im Speicherkonto und kann einen auswählen.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft.Storage.StorageBlobSelector – Auswählen eines Containers":::

Der Benutzer kann eine Datei aus dem Container auswählen.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft.Storage.StorageBlobSelector – Datei":::

Das Steuerelement wird aktualisiert, um den ausgewählten Dateinamen anzuzeigen.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft.Storage.StorageBlobSelector – Anzeigen der ausgewählten Datei":::

## <a name="schema"></a>Schema

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>Beispielausgabe

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>Bemerkungen

Die Eigenschaft **constraints.allowedFileExtensions** gibt die zulässigen Dateitypen an.

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zum Erstellen von Benutzeroberflächendefinitionen finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
* Eine Beschreibung der allgemeinen Eigenschaften in Benutzeroberflächenelementen finden Sie unter [CreateUiDefinition-Elemente](create-uidefinition-elements.md).
