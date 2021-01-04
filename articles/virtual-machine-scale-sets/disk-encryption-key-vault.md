---
title: Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption
description: Dieser Artikel enthält Schritte zum Erstellen und Konfigurieren eines Schlüsseltresors für die Verwendung mit Azure Disk Encryption.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: acd2ae54d81fb508d5f8c02262cf8c2f0f071fb5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016793"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption

Azure Disk Encryption verwendet Azure Key Vault zum Steuern und Verwalten von Verschlüsselungsschlüsseln und Geheimnissen für die Datenträgerverschlüsselung.  Weitere Informationen zu Schlüsseltresoren finden Sie unter [Erste Schritte mit Azure Key Vault](../key-vault/general/overview.md) und [Schützen eines Schlüsseltresors](../key-vault/general/secure-your-key-vault.md).

Das Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption umfasst drei Schritte:

1. Erstellen einer Ressourcengruppe (bei Bedarf)
2. Erstellen eines Schlüsseltresors 
3. Festlegen von erweiterten Zugriffsrichtlinien für den Schlüsseltresor

Diese Schritte werden in den folgenden Schnellstarts veranschaulicht:

Sie können auch einen Schlüssel für die Schlüsselverschlüsselung (Key Encryption Key, KEK) generieren oder importieren.

## <a name="install-tools-and-connect-to-azure"></a>Installieren von Tools und Herstellen einer Verbindung mit Azure

Die Schritte in diesem Artikel können mit der [Azure-Befehlszeilenschnittstelle](/cli/azure/), dem [Azure PowerShell-Az-Modul](/powershell/azure/) oder dem [Azure-Portal](https://portal.azure.com) abgeschlossen werden.

### <a name="connect-to-your-azure-account"></a>Herstellen einer Verbindung mit Ihrem Azure-Konto

Vor der Verwendung der Azure-Befehlszeilenschnittstelle oder von Azure PowerShell müssen Sie zunächst eine Verbindung mit Ihrem Azure-Abonnement herstellen. Dies erfolgt durch [Anmelden mit der Azure-Befehlszeilenschnittstelle](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0) oder Bereitstellen der Anmeldeinformationen im Azure-Portal nach Aufforderung.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure Disk Encryption](disk-encryption-overview.md)
- [Verschlüsseln von Betriebssystem- und angefügten Datenträgern in einer VM-Skalierungsgruppe mit Azure CLI](disk-encryption-cli.md)
- [Verschlüsseln von Betriebssystem- und angefügten Datenträgern in einer VM-Skalierungsgruppe mit Azure PowerShell](disk-encryption-powershell.md)
