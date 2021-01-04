---
title: Stackify Retrace-Agent-Erweiterung für Azure Linux
description: Bereitstellen des Stackify Retrace-Agents für Linux auf einem virtuellen Linux-Computer
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: akjosh
ms.openlocfilehash: 0f9ac4f7c88e72cb6ddadc2450947697b0e3c6ef
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961973"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace-Agent-Erweiterung für Linux

## <a name="overview"></a>Übersicht

Stackify stellt Produkte bereit, die Details zu Ihrer Anwendung ermitteln, um rasch Probleme aufdecken und beheben zu können. Für Entwicklerteams ist Retrace ein äußerst nützliches Tool zur Steigerung der App-Leistung mit vollständiger Integration für verschiedene Umgebungen. Es vereint Tools, die für jedes Entwicklungsteam unerlässlich sind.

Retrace ist das EINZIGE Tool, das für alle Umgebungen sämtliche der folgenden Funktionen über eine zentrale Plattform bereitstellt.

* Steuerung der Anwendungsleistung (Application Performance Management, APM)
* Anwendungs- und Serverprotokollierung
* Nachverfolgen und Überwachen von Fehlern
* Server, Anwendung und benutzerdefinierte Metriken

**Informationen zur Stackify-Agent-Erweiterung für Linux**

Diese Erweiterung stellt einen Installationspfad für den Retrace-Agent für Linux zur Verfügung. 

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem 

Der Retrace-Agent kann für folgende Linux-Distributionen ausgeführt werden:

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 und 17.04 |
| Debian | 7.9 und höher, 8.2 und höher und 9 |
| Red Hat | 6.7 und höher und 7.1 und höher |
| CentOS | 6.3 und höher und 7.0 und höher |

### <a name="internet-connectivity"></a>Internetkonnektivität

Um die Stackify-Agent-Erweiterung für Linux verwenden zu können, muss der virtuelle Zielcomputer mit dem Internet verbunden sein. 

Eventuell müssen Sie Ihre Netzwerkkonfiguration dahingehend anpassen, dass Verbindungen mit Stackify zugelassen werden. Informationen hierzu finden Sie unter https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Erweiterungsschema

---

Der folgende JSON-Code zeigt das Schema für die Stackify Retrace-Agent-Erweiterung. Für die Erweiterung sind `environment` und `activationKey` erforderlich.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Bereitstellung von Vorlagen 

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Das im vorherigen Abschnitt erläuterte JSON-Schema kann in einer Azure Resource Manager-Vorlage verwendet werden, um die Stackify Retrace-Agent-Erweiterung für Linux während einer Bereitstellung über eine Azure Resource Manager-Vorlage auszuführen.  

Der JSON-Code für eine Erweiterung des virtuellen Computers kann innerhalb der VM-Ressource geschachtelt oder im Stamm bzw. auf der obersten Ebene einer Resource Manager-JSON-Vorlage platziert werden. Die Platzierung des JSON-Codes wirkt sich auf den Wert von Name und Typ der Ressource aus. Weitere Informationen finden Sie unter „Festlegen von Name und Typ für untergeordnete Ressourcen“.

Im folgenden Beispiel wird davon ausgegangen, dass die Stackify Retrace-Erweiterung für Linux in der VM-Ressource geschachtelt ist. Beim Schachteln der Ressource für die Erweiterung wird der JSON-Code im "resources": []-Objekt des virtuellen Computers platziert.

Für die Erweiterung sind `environment` und `activationKey` erforderlich.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Beim Platzieren des JSON-Codes für die Erweiterung im Stamm der Vorlage enthält der Name der Ressource einen Verweis auf die übergeordnete VM, und der Typ spiegelt die geschachtelte Konfiguration wider.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell-Bereitstellung

Mit dem Befehl `Set-AzVMExtension` können Sie die Stackify Retrace-Agent-VM-Erweiterung für Linux auf einem vorhandenen virtuellen Computer bereitstellen. Vor dem Ausführen des Befehls müssen die öffentliche und die private Konfiguration in einer PowerShell-Hashtabelle gespeichert werden.

Für die Erweiterung sind `environment` und `activationKey` erforderlich.

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle 

Mit dem Azure CLI-Tool können Sie die Stackify Retrace-VM-Agent-Erweiterung für Linux auf einem vorhandenen virtuellen Computer bereitstellen.  

Für die Erweiterung sind `environment` und `activationKey` erforderlich.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="error-codes"></a>Fehlercodes

| Fehlercode | Bedeutung | Mögliche Aktion |
| :---: | --- | --- |
| 10 | Installationsfehler | wget ist erforderlich. |
| 20 | Installationsfehler | Python ist erforderlich. |
| 30 | Installationsfehler | sudo ist erforderlich. |
| 40 | Installationsfehler | activationKey ist erforderlich. |
| 51 | Installationsfehler | Betriebssystemverteilung wird nicht unterstützt. |
| 60 | Installationsfehler | Umgebung ist erforderlich. |
| 70 | Installationsfehler | Unknown |
| 80 | Fehler beim Aktivieren | Fehler bei der Einrichtung des Diensts |
| 90 | Fehler beim Aktivieren | Fehler beim Starten des Diensts |
| 100 | Fehler beim Deaktivieren | Fehler beim Beenden des Diensts |
| 110 | Fehler beim Deaktivieren | Fehler beim Entfernen des Diensts |
| 120 | Fehler bei der Deinstallation | Fehler beim Beenden des Diensts |

Wenn Sie weitere Unterstützung benötigen, wenden Sie sich unter https://support.stackify.com an den Stackify-Support.