---
title: 'Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken: Azure Key Vault'
description: Schrittweise Anweisungen zum Konfigurieren von Key Vault-Firewalls und virtuellen Netzwerken
services: key-vault
author: amitbapat
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: a6f2e899e8be39abdefaf9d4f524eae457673c1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64694402"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken

In diesem Artikel erhalten Sie schrittweise Anleitungen zum Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken, um den Zugriff auf Ihren Schlüsseltresor einzuschränken. Die [VNET-Dienstendpunkte für Key Vault](key-vault-overview-vnet-service-endpoints.md) ermöglichen Ihnen, den Zugriff auf angegebene virtuelle Netzwerke und eine Gruppe von IPv4-Adressbereichen (Internet Protocol, Version 4) zu beschränken.

> [!IMPORTANT]
> Nachdem Firewallregeln in Kraft sind, können Benutzer nur Vorgänge auf Key Vault-[Datenebene](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) ausführen, wenn ihre Anforderungen aus zulässigen virtuellen Netzwerken oder IPv4-Adressbereichen stammen. Dies gilt auch für den Zugriff auf den Schlüsseltresor aus dem Azure-Portal. Obwohl Benutzer im Azure-Portal zu einem Schlüsseltresor navigieren können, können sie möglicherweise keine Schlüssel, Geheimnisse oder Zertifikate auflisten, wenn ihr Clientcomputer nicht in der Zulassungsliste enthalten ist. Dies wirkt sich auch auf die Key Vault-Auswahl anderer Azure-Dienste aus. Benutzer können möglicherweise eine Liste von Schlüsseltresoren einsehen, aber keine Schlüssel auflisten, wenn Firewallregeln ihren Clientcomputer blockieren.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Im Folgenden finden Sie Anweisungen zum Konfigurieren von Key Vault-Firewalls und virtuellen Netzwerken mithilfe des Azure-Portals:

1. Navigieren Sie zum Schlüsseltresor, den Sie absichern möchten.
2. Wählen Sie **Firewalls und virtuelle Netzwerke** aus.
3. Wählen Sie unter **Zugriff erlauben von** den Eintrag **Ausgewählte Netzwerke** aus.
4. Wenn Sie vorhandene virtuelle Netzwerke zu Firewalls und VNET-Regeln hinzufügen möchten, klicken Sie auf **+ Vorhandene virtuelle Netzwerke hinzufügen**.
5. Wählen Sie auf dem angezeigten neuen Blatt das Abonnement, die virtuellen Netzwerke und Subnetze aus, denen Sie Zugriff auf diesen Schlüsseltresor gewähren möchten. Wenn für die von Ihnen ausgewählten virtuellen Netzwerke und Subnetze keine Dienstendpunkte aktiviert sind, bestätigen Sie, dass Sie Dienstendpunkte aktivieren möchten, und wählen Sie **Aktivieren** aus. Es kann bis zu 15 Minuten dauern, bis diese Änderung wirksam wird.
6. Fügen Sie unter **IP-Netzwerke** IPv4-Adressbereiche hinzu, indem Sie IPv4-Adressbereiche in [CIDR-Notation (Classless Inter-Domain Routing)](https://tools.ietf.org/html/rfc4632) oder einzelne IP-Adressen eingeben.
7. Wählen Sie **Speichern** aus.

Sie können auch neue virtuelle Netzwerke und Subnetze hinzufügen und dann Dienstendpunkte für die neu erstellten virtuellen Netzwerke und Subnetze aktivieren, indem Sie **+ Neues virtuelles Netzwerk hinzufügen** auswählen. Folgen Sie dann den Anweisungen.

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI 

Im Folgenden finden Sie Anweisungen zum Konfigurieren von Key Vault-Firewalls und virtuellen Netzwerken mithilfe der Azure-Befehlszeilenschnittstelle.

1. [Installieren Sie die Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli), und [melden Sie sich an](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Listen Sie die verfügbaren VNET-Regeln auf. Wenn Sie keine Regeln für diesen Schlüsselspeicher festgelegt haben, ist die Liste leer.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Aktivieren Sie den Dienstendpunkt für Key Vault in einem vorhandenen virtuellen Netzwerk und Subnetz.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Fügen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz hinzu.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Fügen Sie einen IP-Adressbereich hinzu, aus dem Datenverkehr zugelassen werden soll.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Wenn dieser Schlüsselspeicher für vertrauenswürdige Dienste zugänglich sein sollte, legen Sie `bypass` auf `AzureServices` fest.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Aktivieren Sie die Netzwerkregeln, indem Sie die Standardaktion auf `Deny` festlegen.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Im Folgenden finden Sie Anweisungen zum Konfigurieren von Key Vault-Firewalls und virtuellen Netzwerken mithilfe der PowerShell:

1. Installieren Sie die neueste Version von [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps), und [melden Sie sich an](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Listen Sie die verfügbaren VNET-Regeln auf. Wenn Sie keine Regeln für diesen Schlüsselspeicher festgelegt haben, ist die Liste leer.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Aktivieren Sie den Dienstendpunkt für Key Vault in einem vorhandenen virtuellen Netzwerk und Subnetz.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Fügen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz hinzu.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Fügen Sie einen IP-Adressbereich hinzu, aus dem Datenverkehr zugelassen werden soll.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Wenn dieser Schlüsselspeicher für vertrauenswürdige Dienste zugänglich sein sollte, legen Sie `bypass` auf `AzureServices` fest.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Aktivieren Sie die Netzwerkregeln, indem Sie die Standardaktion auf `Deny` festlegen.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referenzen

* Azure CLI-Befehle: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-Cmdlets: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Nächste Schritte

* [VNET-Dienstendpunkte für Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Schützen Ihrer Key Vault-Instanz](key-vault-secure-your-key-vault.md)
