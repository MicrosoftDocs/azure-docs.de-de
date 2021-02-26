---
title: 'Verwenden von kundenseitig verwalteten Schlüsseln (Bring Your Own Key, BYOK): Portal'
description: In diesem Tutorial verwenden Sie das Azure-Portal, um kundenseitig verwaltete Schlüssel oder BYOK (Bring Your Own Key) mit einem Azure Media Services-Speicherkonto zu aktivieren.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: e34649162c7a30d4ab43aa068d2c864b5c2d90e0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955699"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Tutorial: Verwenden von kundenseitig verwalteten Schlüsseln oder BYOK mit Media Services über das Azure-Portal

Mit der 2020-05-01-API können Sie einen vom Kunden verwalteten RSA-Schlüssel mit einem Azure Media Services-Konto verwenden, das eine vom System verwaltete Identität hat. Dieses Tutorial behandelt die Schritte im Azure-Portal.

Dies sind die verwendeten Dienste:

- Azure Storage
- Azure Key Vault
- Azure Media Services

In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal für Folgendes verwenden:

> [!div class="checklist"]
> - Erstellen Sie eine Ressourcengruppe.
> - Erstellen eines Speicherkontos mit einer vom System verwalteten Identität.
> - Erstellen eines Media Services-Kontos mit einer vom System verwalteten Identität.
> - Erstellen eines Schlüsseltresors zum Speichern eines vom Kunden verwalteten RSA-Schlüssels.

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Abonnement.

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/free/) erstellen.

## <a name="system-managed-keys"></a>Systemseitig verwaltete Schlüssel

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> Für die folgenden Schritte zum Erstellen eines Speicherkontos wählen Sie in „Erweiterte Einstellungen“ die Option für systemseitig verwaltete Schlüssel aus.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> Für die folgenden Schritte zur Speicherverschlüsselung wählen Sie die Option **Kundenseitig verwalteter Schlüssel** aus.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>Ändern des Schlüssels

Media Services erkennt Änderungen des Schlüssels automatisch. OPTIONAL: Um diesen Vorgang zu testen, erstellen Sie eine weitere Schlüsselversion für denselben Schlüssel. Media Services sollte erkennen, dass dieser Schlüssel geändert wurde.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die von Ihnen erstellten Ressourcen nicht weiter verwenden möchten und *sie Ihnen nicht mehr in Rechnung gestellt werden sollen*, löschen Sie sie.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, und lernen Sie das...
> [!div class="nextstepaction"]
> [Codieren einer Remotedatei anhand einer URL und Streamen des Videos über REST](stream-files-tutorial-with-rest.md)
