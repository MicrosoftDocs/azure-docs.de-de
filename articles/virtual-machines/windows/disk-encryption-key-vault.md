---
title: Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption
description: Dieser Artikel enthält Schritte zum Erstellen und Konfigurieren eines Schlüsseltresors für die Verwendung mit Azure Disk Encryption auf einer Windows-VM.
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9d3d8456e0623ea3f1ef17c5f9f7acb28d0ecd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015371"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption

Azure Disk Encryption verwendet Azure Key Vault zum Steuern und Verwalten von Verschlüsselungsschlüsseln und Geheimnissen für die Datenträgerverschlüsselung.  Weitere Informationen zu Schlüsseltresoren finden Sie unter [Erste Schritte mit Azure Key Vault](../../key-vault/general/overview.md) und [Schützen eines Schlüsseltresors](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Wenn Sie zuvor Azure Disk Encryption mit Azure AD zum Verschlüsseln eines virtuellen Computers verwendet haben, müssen Sie diese Option auch weiterhin zum Verschlüsseln Ihres virtuellen Computers verwenden. Weitere Informationen finden Sie unter [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption mit Azure AD (vorheriges Release)](disk-encryption-key-vault-aad.md).

Das Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption umfasst drei Schritte:

> [!Note]
> Um den Zugriff auf Azure Disk Encryption für die Volumeverschlüsselung zu aktivieren, müssen Sie die Option in den Zugriffsrichtlinieneinstellungen von Azure Key Vault auswählen. Wenn Sie die Firewall im Schlüsseltresor aktiviert haben, müssen Sie zur Registerkarte „Netzwerk“ im Schlüsseltresor wechseln und den Zugriff auf vertrauenswürdige Microsoft-Dienste aktivieren. 

1. Erstellen einer Ressourcengruppe (bei Bedarf)
2. Erstellen eines Schlüsseltresors 
3. Festlegen von erweiterten Zugriffsrichtlinien für den Schlüsseltresor

Diese Schritte werden in den folgenden Schnellstarts veranschaulicht:

- [Erstellen und Verschlüsseln eines virtuellen Windows-Computers mit der Azure-Befehlszeilenschnittstelle](disk-encryption-cli-quickstart.md)
- [Erstellen und Verschlüsseln eines virtuellen Windows-Computers mit Azure PowerShell](disk-encryption-powershell-quickstart.md)

Sie können auch einen Schlüssel für die Schlüsselverschlüsselung (Key Encryption Key, KEK) generieren oder importieren.

> [!Note]
> Die Schritte in diesem Artikel werden im [CLI-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) und im [PowerShell-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts) automatisiert.

## <a name="install-tools-and-connect-to-azure"></a>Installieren von Tools und Herstellen einer Verbindung mit Azure

Die Schritte in diesem Artikel können mit der [Azure-Befehlszeilenschnittstelle](/cli/azure/), dem [Azure PowerShell-Az-Modul](/powershell/azure/) oder dem [Azure-Portal](https://portal.azure.com) abgeschlossen werden.

Während das Portal über Ihren Browser zugänglich ist, erfordern die Azure-Befehlszeilenschnittstelle und Azure PowerShell eine lokale Installation. Weitere Informationen finden Sie unter [Azure Disk Encryption für Windows: Installieren von Tools](disk-encryption-windows.md#install-tools-and-connect-to-azure).

### <a name="connect-to-your-azure-account"></a>Herstellen einer Verbindung mit Ihrem Azure-Konto

Vor der Verwendung der Azure-Befehlszeilenschnittstelle oder von Azure PowerShell müssen Sie zunächst eine Verbindung mit Ihrem Azure-Abonnement herstellen. Dies erfolgt durch [Anmelden mit der Azure-Befehlszeilenschnittstelle](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0) oder Bereitstellen der Anmeldeinformationen im Azure-Portal nach Aufforderung.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Nächste Schritte

- [CLI-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Erfahren Sie etwas über [Azure Disk Encryption-Szenarien auf virtuellen Windows-Computern](disk-encryption-windows.md).
- Erfahren Sie, wie Sie [Probleme mit Azure Disk Encryption behandeln](disk-encryption-troubleshooting.md).
- Lesen Sie die [Azure Disk Encryption-Beispielskripts](disk-encryption-sample-scripts.md).
