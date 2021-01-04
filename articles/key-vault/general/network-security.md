---
title: 'Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken: Azure Key Vault'
description: Schrittweise Anweisungen zum Konfigurieren von Key Vault-Firewalls und virtuellen Netzwerken
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: d1b1c27fe0136220d5a1851af4a5c24102a37da1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015552"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken

In diesem Artikel erfahren Sie, wie Sie die Azure Key Vault-Firewall konfigurieren. Dieses Dokument enthält ausführliche Informationen zu den verschiedenen Konfigurationen für die Key Vault-Firewall. Außerdem erfahren Sie hier Schritt für Schritt, wie Sie Azure Key Vault für die Zusammenarbeit mit anderen Anwendungen und Azure-Diensten konfigurieren.

## <a name="firewall-settings"></a>Firewalleinstellungen

In diesem Abschnitt werden die verschiedenen Konfigurationsmöglichkeiten der Azure Key Vault-Firewall beschrieben.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault-Firewall deaktiviert (Standardeinstellung)

Wenn Sie einen neuen Schlüsseltresor erstellen, ist die Azure Key Vault-Firewall standardmäßig deaktiviert. Alle Anwendungen und Azure-Dienste können auf den Schlüsseltresor zugreifen und Anforderungen an den Schlüsseltresor senden. Diese Konfiguration bedeutet allerdings nicht, dass jeder beliebige Benutzer Vorgänge für Ihren Schlüsseltresor ausführen kann. Für den Zugriff auf im Schlüsseltresor gespeicherte Geheimnisse, Schlüssel und Zertifikate sind trotzdem eine Azure Active Directory-Authentifizierung und Zugriffsrichtlinienberechtigungen erforderlich. Ausführlichere Informationen zur Schlüsseltresorauthentifizierung finden Sie unter [Grundlagen der Key Vault-Authentifizierung](./authentication-fundamentals.md).

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault-Firewall aktiviert (nur vertrauenswürdige Dienste)

Wenn Sie die Key Vault-Firewall aktivieren, können Sie festlegen, dass vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlaubt werden soll. Die Liste der vertrauenswürdigen Dienste deckt nicht alle Azure-Dienste ab. Azure DevOps ist beispielsweise nicht in der Liste der vertrauenswürdigen Dienste enthalten. **Das bedeutet nicht, dass Dienste, die nicht in der Liste der vertrauenswürdigen Dienste enthalten sind, nicht vertrauenswürdig oder unsicher sind.** Die Liste der vertrauenswürdigen Dienste umfasst Dienste, bei denen der gesamte im Dienst ausgeführte Code von Microsoft kontrolliert wird. Da Benutzer in Azure-Diensten wie Azure DevOps benutzerdefinierten Code schreiben können, bietet Microsoft keine Option zur Erstellung einer Pauschalgenehmigung für den Dienst. Außerdem gilt: Die Tatsache, dass ein Dienst in der Liste vertrauenswürdiger Dienste enthalten ist, bedeutet nicht, dass er für alle Szenarien zulässig ist.

Wie Sie ermitteln, ob ein Dienst, den Sie verwenden möchten, in der Liste der vertrauenswürdigen Dienste enthalten ist, erfahren Sie [hier](./overview-vnet-service-endpoints.md#trusted-services).

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault-Firewall aktiviert (IPv4-Adressen und -Bereiche – statische IP-Adressen)

Wenn Sie für einen bestimmten Dienst den Zugriff auf den Schlüsseltresor durch die Key Vault-Firewall autorisieren möchten, können Sie seine IP-Adresse der Positivliste der Key Vault-Firewall hinzufügen. Diese Konfiguration eignet sich am besten für Dienste mit statischen IP-Adressen oder bekannten Bereichen.

Gehen Sie wie folgt vor, wenn Sie eine IP-Adresse oder einen Bereich einer Azure-Ressource (beispielsweise eine Web- oder Logik-App) zulassen möchten:

1. Anmelden beim Azure-Portal
1. Wählen Sie die Ressource (spezifische Instanz des Diensts) aus.
1. Klicken Sie unter „Einstellungen“ auf das Blatt Eigenschaften“.
1. Suchen Sie nach dem Feld „IP-Adresse“.
1. Kopieren Sie den Wert oder Bereich, und fügen Sie ihn in die Positivliste der Schlüsseltresorfirewall ein.

Wenn Sie einem gesamten Azure-Dienst den Zugriff durch die Key Vault-Firewall gestatten möchten, verwenden Sie die [Liste der öffentlich dokumentierten IP-Adressen von Azure-Rechenzentren](https://www.microsoft.com/download/details.aspx?id=41653). Suchen Sie die IP-Adressen, die dem gewünschten Dienst in der gewünschten Region zugeordnet sind, und fügen Sie sie wie oben beschrieben der Key Vault-Firewall hinzu.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault-Firewall aktiviert (virtuelle Netzwerke – dynamische IP-Adressen)

Wenn Sie versuchen, eine Azure-Ressource (beispielsweise einen virtuellen Computer) über den Schlüsseltresor zuzulassen, kann es vorkommen, dass Sie keine statischen IP-Adressen verwenden können und ggf. nicht für alle IP-Adressen für Azure Virtual Machines den Zugriff auf Ihren Schlüsseltresor zulassen möchten.

Erstellen Sie in diesem Fall die Ressource in einem virtuellen Netzwerk, und lassen Sie dann für Datenverkehr aus dem spezifischen virtuellen Netzwerk und Subnetz den Zugriff auf Ihren Schlüsseltresor zu. Führen Sie dazu die folgenden Schritte aus.

1. Anmelden beim Azure-Portal
1. Wählen Sie den Schlüsseltresor aus, den Sie konfigurieren möchten.
1. Wählen Sie das Blatt „Netzwerk“ aus.
1. Wählen Sie „+ Vorhandenes virtuelles Netzwerk hinzufügen“ aus.
1. Wählen Sie das virtuelle Netzwerk und das Subnetz aus, für die Sie den Zugriff durch die Schlüsseltresorfirewall zulassen möchten.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault-Firewall aktiviert (Private Link)

Informationen zum Konfigurieren einer Private Link-Verbindung für Ihren Schlüsseltresor finden Sie [hier](./private-link-service.md).

> [!IMPORTANT]
> Nachdem Firewallregeln in Kraft sind, können Benutzer nur Vorgänge auf Key Vault-[Datenebene](secure-your-key-vault.md#data-plane-access-control) ausführen, wenn ihre Anforderungen aus zulässigen virtuellen Netzwerken oder IPv4-Adressbereichen stammen. Dies gilt auch für den Zugriff auf den Schlüsseltresor aus dem Azure-Portal. Obwohl Benutzer im Azure-Portal zu einem Schlüsseltresor navigieren können, können sie möglicherweise keine Schlüssel, Geheimnisse oder Zertifikate auflisten, wenn ihr Clientcomputer nicht in der Zulassungsliste enthalten ist. Dies wirkt sich auch auf die Key Vault-Auswahl anderer Azure-Dienste aus. Benutzer können möglicherweise eine Liste von Schlüsseltresoren einsehen, aber keine Schlüssel auflisten, wenn Firewallregeln ihren Clientcomputer blockieren.

> [!NOTE]
> Bedenken Sie dabei folgende Konfigurationseinschränkungen:
> * Maximal 127 VNET-Regeln und 127 IPv4-Regeln sind zulässig. 
> * IP-Netzwerkregeln sind nur für öffentliche IP-Adressen zulässig. Für private Netzwerke reservierte IP-Adressbereiche (gemäß RFC 1918) sind in IP-Adressregeln nicht zulässig. Private Netzwerke enthalten Adressen, die mit **10.** , **172.16-31** und **192.168.** beginnen. 
> * Derzeit werden nur IPv4-Adressen unterstützt.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Im Folgenden finden Sie Anweisungen zum Konfigurieren von Key Vault-Firewalls und virtuellen Netzwerken mithilfe des Azure-Portals:

1. Navigieren Sie zum Schlüsseltresor, den Sie absichern möchten.
2. Wählen Sie **Netzwerk** und anschließend die Registerkarte **Firewalls und virtuelle Netzwerke** aus.
3. Wählen Sie unter **Zugriff erlauben von** den Eintrag **Ausgewählte Netzwerke** aus.
4. Wenn Sie vorhandene virtuelle Netzwerke zu Firewalls und VNET-Regeln hinzufügen möchten, klicken Sie auf **+ Vorhandene virtuelle Netzwerke hinzufügen**.
5. Wählen Sie auf dem angezeigten neuen Blatt das Abonnement, die virtuellen Netzwerke und Subnetze aus, denen Sie Zugriff auf diesen Schlüsseltresor gewähren möchten. Wenn für die von Ihnen ausgewählten virtuellen Netzwerke und Subnetze keine Dienstendpunkte aktiviert sind, bestätigen Sie, dass Sie Dienstendpunkte aktivieren möchten, und wählen Sie **Aktivieren** aus. Es kann bis zu 15 Minuten dauern, bis diese Änderung wirksam wird.
6. Fügen Sie unter **IP-Netzwerke** IPv4-Adressbereiche hinzu, indem Sie IPv4-Adressbereiche in [CIDR-Notation (Classless Inter-Domain Routing)](https://tools.ietf.org/html/rfc4632) oder einzelne IP-Adressen eingeben.
7. Wenn Sie zulassen möchten, dass vertrauenswürdige Microsoft-Dienste die Key Vault-Firewall umgehen, wählen Sie „Ja“ aus. Eine vollständige Liste der aktuellen vertrauenswürdigen Key Vault-Dienste finden Sie unter folgendem Link: [Azure Key Vault – Vertrauenswürdige Dienste](./overview-vnet-service-endpoints.md#trusted-services).
7. Wählen Sie **Speichern** aus.

Sie können auch neue virtuelle Netzwerke und Subnetze hinzufügen und dann Dienstendpunkte für die neu erstellten virtuellen Netzwerke und Subnetze aktivieren, indem Sie **+ Neues virtuelles Netzwerk hinzufügen** auswählen. Folgen Sie dann den Anweisungen.

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI 

Im Folgenden finden Sie Anweisungen zum Konfigurieren von Key Vault-Firewalls und virtuellen Netzwerken mithilfe der Azure-Befehlszeilenschnittstelle.

1. [Installieren Sie die Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli), und [melden Sie sich an](/cli/azure/authenticate-azure-cli).

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Im Folgenden finden Sie Anweisungen zum Konfigurieren von Key Vault-Firewalls und virtuellen Netzwerken mithilfe der PowerShell:

1. Installieren Sie die neueste Version von [Azure PowerShell](/powershell/azure/install-az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps).

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

## <a name="references"></a>References
* ARM-Vorlagenreferenz: [Azure Key Vault: ARM-Vorlagenreferenz](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI-Befehle: [az keyvault network-rule](/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-Cmdlets: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Nächste Schritte

* [VNET-Dienstendpunkte für Key Vault](overview-vnet-service-endpoints.md)
* [Schützen Ihrer Key Vault-Instanz](secure-your-key-vault.md)