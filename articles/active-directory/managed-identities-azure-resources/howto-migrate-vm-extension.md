---
title: Beenden der Verwendung der VM-Erweiterung für verwaltete Identitäten – Azure AD
description: Eine Schritt-für-Schritt-Anleitung für den Umstieg von der VM-Erweiterung auf Azure Instance Metadata Service (IMDS) für die Authentifizierung.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: barclayn
ROBOTS: NOINDEX
ms.openlocfilehash: dca5f9ed2911ae3042fb9871f849212ec18b1b58
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539382"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Umsteigen von der VM-Erweiterung für verwaltete Identitäten auf Azure Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>VM-Erweiterung für verwaltete Identitäten

Die VM-Erweiterung für verwaltete Identitäten dient zum Anfordern von Token für eine verwaltete Identität des virtuellen Computers. Der Workflow umfasst folgende Schritte:

1. Die Workload in der Ressource ruft zunächst den lokalen Endpunkt `http://localhost/oauth2/token` auf, um ein Zugriffstoken anzufordern.
2. Die VM-Erweiterung verwendet die Anmeldeinformationen für die verwaltete Identität, um ein Zugriffstoken von Azure AD anzufordern. 
3. Das Zugriffstoken wird an den Aufrufer zurückgegeben und kann zur Authentifizierung bei Diensten verwendet werden, die die Azure AD-Authentifizierung unterstützen (beispielsweise Azure Key Vault oder Azure Storage).

Die VM-Erweiterung für verwaltete Identitäten wird aufgrund diverser Einschränkungen (siehe nächster Abschnitt) durch die Verwendung des entsprechenden Endpunkts in Azure Instance Metadata Service (IMDS) ersetzt.

### <a name="provision-the-extension"></a>Bereitstellen der Erweiterung 

Wenn Sie einen virtuellen Computer oder eine VM-Skalierungsgruppe mit einer verwalteten Identität konfigurieren, können Sie die verwalteten Identitäten optional für die VM-Erweiterung für Azure-Ressourcen bereitstellen, indem Sie das Cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) mit dem Parameter `-Type` verwenden. Sie können abhängig von der Art des virtuellen Computers entweder `ManagedIdentityExtensionForWindows` oder `ManagedIdentityExtensionForLinux` übergeben und mithilfe des Parameters `-Name` einen Namen festlegen. Der `-Settings`-Parameter gibt den Port an, der vom OAuth-Token-Endpunkt für den Tokenabruf verwendet wird:

```azurepowershell-interactive
$settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Die VM-Erweiterung kann auch mithilfe der Azure Resource Manager-Bereitstellungsvorlage bereitgestellt werden. Hierzu muss dem Vorlagenabschnitt `resources` der folgende JSON-Code hinzugefügt werden. (Verwenden Sie `ManagedIdentityExtensionForLinux` für die Namens- und Typelemente für die Linux-Version.)

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
    "apiVersion": "2018-06-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.ManagedIdentity",
        "type": "ManagedIdentityExtensionForWindows",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "port": 50342
        }
    }
}
```
    
    
Bei Verwendung von VM-Skalierungsgruppen können Sie die Erweiterung für verwaltete Identitäten für Azure-Ressourcen für eine VM-Skalierungsgruppe auch mithilfe des Cmdlets [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) bereitstellen. Sie können abhängig von der Art der VM-Skalierungsgruppe entweder `ManagedIdentityExtensionForWindows` oder `ManagedIdentityExtensionForLinux` übergeben und mithilfe des Parameters `-Name` einen Namen festlegen. Der `-Settings`-Parameter gibt den Port an, der vom OAuth-Token-Endpunkt für den Tokenabruf verwendet wird:

   ```azurepowershell-interactive
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Wenn Sie die VM-Skalierungsgruppenerweiterung mithilfe der Azure Resource Manager-Bereitstellungsvorlage bereitstellen möchten, fügen Sie dem Vorlagenabschnitt `extensionpProfile` den folgenden JSON-Code hinzu. (Verwenden Sie `ManagedIdentityExtensionForLinux` für die Namens- und Typelemente für die Linux-Version.)

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "ManagedIdentityWindowsExtension",
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                },
                "protectedSettings": {}
            }
        }
```

Es kann vorkommen, dass die Bereitstellung der VM-Erweiterung aufgrund von Fehlern beim DNS-Lookup nicht erfolgreich ist. Starten Sie in diesem Fall den virtuellen Computer neu, und versuchen Sie es noch mal. 

### <a name="remove-the-extension"></a>Entfernen der Erweiterung 
Wenn Sie die Erweiterung entfernen möchten, verwenden Sie die Azure-Befehlszeilenschnittstelle mit [az vm extension delete](/cli/azure/vm/) (bzw. [az vmss extension delete](/cli/azure/vmss) für VM-Skalierungsgruppen) und dem Schalter `-n ManagedIdentityExtensionForWindows` oder `-n ManagedIdentityExtensionForLinux` (abhängig von der Art des virtuellen Computers), oder verwenden Sie `Remove-AzVMExtension` für PowerShell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```azurepowershell-interactive
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Abrufen eines Tokens mithilfe der VM-Erweiterung

In der folgenden Beispielanforderung wird der VM-Erweiterungsendpunkt für verwaltete Identitäten für Azure-Ressourcen verwendet:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | BESCHREIBUNG |
| ------- | ----------- |
| `GET` | Das HTTP-Verb, mit dem angegeben wird, dass Sie Daten vom Endpunkt abrufen möchten. In diesem Fall ist dies ein OAuth-Zugriffstoken. | 
| `http://localhost:50342/oauth2/token` | Der Endpunkt für verwaltete Identitäten für Azure-Ressourcen, wobei 50342 der Standardport und konfigurierbar ist. |
| `resource` | Ein Abfragezeichenfolgenparameter, der den App-ID-URI der Zielressource angibt. Er wird auch im Anspruch `aud` (audience) des ausgestellten Tokens angezeigt. In diesem Beispiel wird ein Token für den Zugriff auf Azure Resource Manager angefordert, das über den App-ID-URI `https://management.azure.com/` verfügt. |
| `Metadata` | Ein HTTP-Anforderungsheader-Feld, das für verwaltete Identitäten für Azure-Ressourcen als Maßnahme gegen SSRF-Angriffe (Server Side Request Forgery) erforderlich ist. Dieser Wert muss auf „true“ (in Kleinbuchstaben) festgelegt werden.|
| `object_id` | (Optional) Ein Abfragezeichenfolgen-Parameter, der den object_id-Wert der verwalteten Identität angibt, für die das Token gelten soll. Erforderlich, wenn Ihr virtueller Computer über mehrere vom Benutzer zugewiesene verwaltete Identitäten verfügt.|
| `client_id` | (Optional) Ein Abfragezeichenfolgen-Parameter, der den client_id-Wert der verwalteten Identität angibt, für die das Token gelten soll. Erforderlich, wenn Ihr virtueller Computer über mehrere vom Benutzer zugewiesene verwaltete Identitäten verfügt.|


Beispiel für eine Antwort:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | BESCHREIBUNG |
| ------- | ----------- |
| `access_token` | Das angeforderte Zugriffstoken. Beim Aufrufen einer geschützten REST-API wird das Token als „Bearertoken“ in das `Authorization`-Anforderungsheader-Feld eingebettet, damit der Aufrufer von der API authentifiziert werden kann. | 
| `refresh_token` | Wird von verwalteten Identitäten für Azure-Ressourcen nicht verwendet. |
| `expires_in` | Der Zeitraum in Sekunden, wie lange das Zugriffstoken ab dem Zeitpunkt der Ausstellung gültig ist, bevor es abläuft. Der Ausstellungszeitpunkt ist im Anspruch `iat` des Tokens zu finden. |
| `expires_on` | Der Zeitpunkt, zu dem das Zugriffstoken abläuft. Das Datum wird als Anzahl von Sekunden ab „1970-01-01T0:0:0Z UTC“ (entspricht dem Anspruch `exp` des Tokens) dargestellt. |
| `not_before` | Der Zeitpunkt, ab dem das Zugriffstoken wirksam ist und akzeptiert werden kann. Das Datum wird als Anzahl von Sekunden ab „1970-01-01T0:0:0Z UTC“ (entspricht dem Anspruch `nbf` des Tokens) dargestellt. |
| `resource` | Die Ressource, für die das Zugriffstoken angefordert wurde (Übereinstimmung mit dem Abfragezeichenfolgenparameter `resource` der Anforderung). |
| `token_type` | Der Typ des Tokens. In diesem Fall ein „Bearerzugriffstoken“, sodass die Ressource Zugriff auf den Bearer dieses Tokens gewähren kann. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Behandeln von Problemen mit der VM-Erweiterung 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Neustarten der VM-Erweiterung nach einem Fehler

Unter Windows und bestimmten Versionen von Linux kann das folgende Cmdlet verwendet werden, um die Erweiterung manuell neu zu starten, wenn sie beendet wurde:

```azurepowershell-interactive
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Hierbei gilt: 
- Erweiterungsname und -typ für Windows: `ManagedIdentityExtensionForWindows`
- Erweiterungsname und -typ für Linux: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Beim Feature „Automatisierungsskript“ tritt ein Fehler auf, wenn versucht wird, einen Schemaexport für die Erweiterung für verwaltete Identitäten für Azure-Ressourcen durchzuführen.

Wenn verwaltete Identitäten für Azure-Ressourcen auf einem virtuellen Computer aktiviert sind, wird beim Versuch, das Feature „Automatisierungsskript“ für den virtuellen Computer oder seine Ressourcengruppe zu verwenden, der folgende Fehler angezeigt:

![Exportfehler beim Automatisierungsskript für verwaltete Identitäten für Azure-Ressourcen](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Das Schema der VM-Erweiterung für verwaltete Identitäten für Azure-Ressourcen kann derzeit nicht in eine Ressourcengruppenvorlage exportiert werden. Die generierte Vorlage weist daher keine Konfigurationsparameter auf, mit denen die verwalteten Identitäten Azure-Ressourcen in der Ressource aktiviert werden können. Diese Abschnitte können anhand der Beispiele in [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe einer Vorlage](qs-configure-template-windows-vm.md) manuell hinzugefügt werden.

Wenn die Schemaexportfunktion für die VM-Erweiterung für verwaltete Identitäten für Azure-Ressourcen (wird voraussichtlich im Januar 2019 als veraltet gekennzeichnet) verfügbar ist, wird sie in [Exportieren von Ressourcengruppen, die VM-Erweiterungen enthalten](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions) aufgelistet.

## <a name="limitations-of-the-virtual-machine-extension"></a>Einschränkungen der VM-Erweiterung 

Die Verwendung der VM-Erweiterung geht mit mehreren signifikanten Einschränkungen einher. 

 * Die schwerwiegendste Einschränkung besteht darin, dass die Anmeldeinformationen für Tokenanforderungen auf dem virtuellen Computer gespeichert werden. Gelingt es einem Angreifer, auf den virtuellen Computer zuzugreifen, kann er die Anmeldeinformationen entwenden. 
 * Darüber hinaus wird die VM-Erweiterung von mehreren Linux-Distributionen immer noch nicht unterstützt. Das führt zu hohen Entwicklungskosten, da die Erweiterung für jede dieser Distributionen angepasst, erstellt und getestet werden muss. Aktuell werden lediglich die folgenden Linux-Distributionen unterstützt: 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Die Bereitstellung virtueller Computer mit verwalteten Identitäten hat Auswirkungen auf die Leistung, da die VM-Erweiterung ebenfalls bereitgestellt werden muss. 
 * Und schließlich unterstützt die VM-Erweiterung nur 32 benutzerseitig zugewiesene verwaltete Identitäten pro virtuellem Computer. 

## <a name="azure-instance-metadata-service"></a>Azure-Instanzmetadatendienst

[Azure Instance Metadata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) ist ein REST-Endpunkt, der Informationen zu ausgeführten VM-Instanzen bereitstellt, die zum Verwalten und Konfigurieren Ihrer virtuellen Computer verwendet werden können. Der Endpunkt ist unter einer bekannten, nicht routingfähigen IP-Adresse verfügbar (`169.254.169.254`), auf die nur innerhalb des virtuellen Computers zugegriffen werden kann.

Das Anfordern von Token mit Azure IMDS hat zahlreiche Vorteile. 

1. Da sich der Dienst außerhalb des virtuellen Computers befindet, sind die von verwalteten Identitäten verwendeten Anmeldeinformationen nicht mehr auf dem virtuellen Computer vorhanden. Stattdessen werden sie auf dem Hostcomputer des virtuellen Azure-Computers gehostet und geschützt.   
2. Verwaltete Identitäten können von allen in Azure IaaS unterstützten Windows- und Linux-Betriebssystemen verwendet werden.
3. Die Bereitstellung ist schneller und einfacher, da die VM-Erweiterung nicht mehr bereitgestellt werden muss.
4. Mit dem IMDS-Endpunkt können einem einzelnen virtuellen Computer bis zu 1.000 benutzerseitig zugewiesene verwaltete Identitäten zugewiesen werden.
5. Die Anforderungen für IMDS unterscheiden sich nicht wesentlich von den Anforderungen für die VM-Erweiterung. Dadurch lassen sich bereits vorhandene Bereitstellungen, die momentan die VM-Erweiterung verwenden, relativ einfach umstellen.

Aus diesen Gründen werden Token mithilfe von Azure IMDS angefordert, nachdem die VM-Erweiterung als veraltet gekennzeichnet wurde. 


## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer zum Abrufen eines Zugriffstokens](how-to-use-vm-token.md)
* [Azure-Instanzmetadatendienst](../../virtual-machines/windows/instance-metadata-service.md)
