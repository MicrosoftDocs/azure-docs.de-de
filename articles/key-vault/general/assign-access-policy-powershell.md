---
title: Zuweisen einer Azure Key Vault-Zugriffsrichtlinie
description: Erfahren Sie, Sie einem Dienstprinzipal oder einer Anwendungsidentität mithilfe des Azure-Portals, der Azure-Befehlszeilenschnittstelle oder von Azure PowerShell eine Key Vault-Zugriffsrichtlinie zuweisen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: fb28b4b678b37f69331b2ecff6272fd7aa64d191
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287621"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Zuweisen einer Key Vault-Zugriffsrichtlinie mit Azure PowerShell

Eine Key Vault-Zugriffsrichtlinie legt fest, ob ein bestimmter Dienstprinzipal (eine Anwendung oder eine Benutzergruppe) verschiedene Vorgänge für [Geheimnisse](../secrets/index.yml), [Schlüssel](../keys/index.yml) und [Zertifikate](../certificates/index.yml) in Key Vault ausführen kann. Sie können mithilfe des [Azure-Portals](assign-access-policy-portal.md), der [Azure-Befehlszeilenschnittstelle](assign-access-policy-cli.md) oder von Azure PowerShell (dieser Artikel) Zugriffsrichtlinien zuweisen.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Weitere Informationen zum Erstellen von Gruppen in Azure Active Directory mithilfe von Azure PowerShell finden Sie unter [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) und [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Konfigurieren von PowerShell und Anmelden

1. Um Befehle lokal auszuführen, installieren Sie [Azure PowerShell](/powershell/azure/), sofern dies noch nicht geschehen ist.

    Um Befehle direkt in der Cloud auszuführen, verwenden Sie [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Nur lokale PowerShell:

    1. Installieren sie das [Azure Active Directory PowerShell-Modul](https://www.powershellgallery.com/packages/AzureAD).

    1. Melden Sie sich bei Azure an:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Abrufen der Objekt-ID

Ermitteln Sie die Objekt-ID der Anwendung, der Gruppe oder des Benutzers, der oder dem Sie die Zugriffsrichtlinie zuweisen möchten:

- Anwendungen und andere Dienstprinzipale: Verwenden Sie das Cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) mit dem Parameter `-SearchString`, um die Ergebnisse nach dem Namen des gewünschten Dienstprinzipals zu filtern:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Gruppen: Verwenden Sie das Cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) mit dem Parameter `-SearchString`, um die Ergebnisse nach dem Namen der gewünschten Gruppe zu filtern:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    In der Ausgabe wird die Objekt-ID als `Id` aufgeführt.

- Benutzer: Verwenden Sie das Cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser), und übergeben Sie dabei die E-Mail-Adresse des Benutzers im Parameter `-UserPrincipalName`.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    In der Ausgabe wird die Objekt-ID als `Id` aufgeführt.

## <a name="assign-the-access-policy"></a>Zuweisen der Zugriffsrichtlinie

Verwenden Sie das Cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), um die Zugriffsrichtlinie zuzuweisen:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Sie müssen `-PermissionsToSecrets`, `-PermissionsToKeys` und `-PermissionsToCertificates` nur einschließen, wenn Sie diesen spezifischen Typen Berechtigungen zuweisen. Die zulässigen Werte für `<secret-permissions>`, `<key-permissions>` und `<certificate-permissions>` sind in der Dokumentation [Set-AzKeyVaultAccessPolicy – Parameter](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) angegeben.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Key Vault – Sicherheit: Identitäts- und Zugriffsverwaltung](overview-security.md#identity-and-access-management)
- [Sicherer Zugriff auf einen Schlüsseltresor](secure-your-key-vault.md)
- [Entwicklerhandbuch zu Azure Key Vault](developers-guide.md)
- [Bewährte Methoden bei Azure Key Vault](best-practices.md)