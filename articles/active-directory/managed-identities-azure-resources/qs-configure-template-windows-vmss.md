---
title: 'Konfigurieren von Vorlagen zur Verwendung von verwalteten Identitäten in VM-Skalierungsgruppen: Azure Active Directory'
description: Schrittweise Anweisungen zum Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe mithilfe einer Azure Resource Manager-Vorlage.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 377bbb9ce111f3cf2daf8426e128186711c30e5f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587450"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer Azure VM-Skalierungsgruppe mithilfe einer Vorlage

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure Resource Manager-Bereitstellungsvorlage die folgenden Vorgänge für verwaltete Identitäten für Azure-Ressourcen ausführen können:
- Aktivieren und Deaktivieren der systemzugewiesenen verwalteten Identität in einer Azure VM-Skalierungsgruppe
- Hinzufügen und Entfernen einer benutzerzugewiesenen verwalteten Identität in einer Azure VM-Skalierungsgruppe

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#managed-identity-types)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Um die Verwaltungsvorgänge in diesem Artikel auszuführen, benötigt Ihr Konto die folgenden Zuweisungen der rollenbasierten Azure-Zugriffssteuerung:

    > [!NOTE]
    > Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

    - [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor), um eine VM-Skalierungsgruppe zu erstellen und die vom System und/oder Benutzer zugewiesene verwaltete Identität in einer VM-Skalierungsgruppe zu aktivieren bzw. daraus zu entfernen
    - [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor), um eine vom Benutzer zugewiesene verwaltete Identität zu erstellen
    - [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator), um eine vom Benutzer zugewiesene verwaltete Identität einer VM-Skalierungsgruppe zuzuweisen bzw. daraus zu entfernen

## <a name="azure-resource-manager-templates"></a>Azure-Ressourcen-Manager-Vorlagen

Wie beim Azure-Portal und der Skripterstellung bieten [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Vorlagen die Möglichkeit, von einer Azure-Ressourcengruppe definierte neue bzw. geänderte Ressourcen bereitzustellen. Für die Vorlagenbearbeitung und -bereitstellung sind mehrere Optionen sowohl lokal als auch portalbasiert verfügbar, einschließlich:

   - Verwenden einer [benutzerdefinierten Vorlage aus Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template). Dies ermöglicht Ihnen, eine ganz neue Vorlage zu erstellen oder eine Vorlage zu verwenden, die auf einer vorhandenen, häufig genutzten Vorlage oder einer [Schnellstartvorlage](https://azure.microsoft.com/documentation/templates/) basiert.
   - Ableiten von einer vorhandenen Ressourcengruppe, indem eine Vorlage aus [der ursprünglichen Bereitstellung](../../azure-resource-manager/templates/export-template-portal.md) oder aus dem [aktuellen Status der Bereitstellung](../../azure-resource-manager/templates/export-template-portal.md) exportiert wird.
   - Verwenden eines lokalen [JSON-Editors (z.B. VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) und anschließendes Hochladen und Bereitstellen mithilfe von PowerShell oder CLI.
   - Verwenden des [Azure-Ressourcengruppenprojekts](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) von Visual Studio für das Erstellen und Bereitstellen einer Vorlage.  

Unabhängig von der gewählten Option ist die Vorlagensyntax während der ursprünglichen und erneuten Bereitstellung identisch. Die Aktivierung verwalteter Identitäten für Azure-Ressourcen auf einem neuen oder vorhandenen virtuellen Computer erfolgt auf dieselbe Weise. Standardmäßig führt Azure Resource Manager außerdem ein [inkrementelles Update](../../azure-resource-manager/templates/deployment-modes.md) für Bereitstellungen durch.

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt aktivieren und deaktivieren Sie die systemzugewiesene verwaltete Identität mithilfe einer Azure Resource Manager-Vorlage.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Aktivieren der vom System zugewiesenen verwalteten Identität bei der Erstellung einer VM-Skalierungsgruppe oder in einer vorhandenen VM-Skalierungsgruppe

1. Verwenden Sie unabhängig davon, ob Sie sich bei Azure lokal oder über das Azure-Portal anmelden, ein Konto, das dem Azure-Abonnement zugeordnet ist, das die VM-Skalierungsgruppe enthält.
2. Um die vom System zugewiesene verwaltete Identität zu aktivieren, laden Sie die Vorlage in einem Editor, suchen Sie die gewünschte Ressource `Microsoft.Compute/virtualMachinesScaleSets` im Ressourcenabschnitt, und fügen Sie die `identity`-Eigenschaft auf der gleichen Ebene wie die `"type": "Microsoft.Compute/virtualMachinesScaleSets"`-Eigenschaft hinzu. Verwenden Sie die folgende Syntax:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Sie können die verwalteten Identitäten für die Erweiterung der VM-Skalierungsgruppe von Azure-Ressourcen optional bereitstellen, indem Sie sie als `extensionProfile`-Element in der Vorlage angeben. Dieser Schritt ist optional, da Sie für den Tokenabruf auch den Azure IMDS-Identitätsendpunkt (Instance Metadata Service) verwenden können.  Weitere Informationen finden Sie unter [Migrieren von der VM-Erweiterung zu Azure IMDS für die Authentifizierung](howto-migrate-vm-extension.md).


4. Wenn Sie fertig sind, sollten die folgenden Abschnitte dem Ressourcenabschnitt der Vorlage hinzugefügt worden sein, und diese sollte wie folgt aussehen:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
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
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Deaktivieren einer vom System zugewiesenen verwalteten Identität in einer Azure VM-Skalierungsgruppe

Wenn für die VM-Skalierungsgruppe keine vom System zugewiesene verwaltete Identität mehr benötigt wird, gehen Sie wie folgt vor:

1. Verwenden Sie unabhängig davon, ob Sie sich bei Azure lokal oder über das Azure-Portal anmelden, ein Konto, das dem Azure-Abonnement zugeordnet ist, das die VM-Skalierungsgruppe enthält.

2. Laden Sie die Vorlage in einen [Editor](#azure-resource-manager-templates), und suchen Sie nach der gewünschten `Microsoft.Compute/virtualMachineScaleSets`-Ressource im Abschnitt `resources`. Wenn Ihre VM nur über eine vom System zugewiesene verwaltete Identität verfügt, können Sie diese deaktivieren, indem Sie den Identitätstyp in `None` ändern.

   **Microsoft.Compute/virtualMachineScaleSets-API, Version 2018-06-01**

   Wenn `2018-06-01` als apiVersion verwendet wird und Ihr virtueller Computer sowohl vom System als auch vom Benutzer zugewiesene verwaltete Identitäten enthält, entfernen Sie `SystemAssigned` aus dem Identitätstyp und behalten `UserAssigned` zusammen mit den userAssignedIdentities-Wörterbuchwerten bei.

   **Microsoft.Compute/virtualMachineScaleSets-API, Version 2018-06-01**

   Wenn `2017-12-01` als apiVersion verwendet wird und Ihre VM-Skalierungsgruppe sowohl vom System als auch vom Benutzer zugewiesene verwaltete Identitäten enthält, entfernen Sie `SystemAssigned` aus dem Identitätstyp und behalten `UserAssigned` zusammen mit dem `identityIds`-Array der benutzerzugewiesenen verwalteten Identitäten bei.



   Das folgende Beispiel zeigt, wie Sie eine vom System zugewiesene verwaltete Identität aus einer VM-Skalierungsgruppe ohne vom Benutzer zugewiesene verwaltete Identitäten entfernen:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt weisen Sie einer VM-Skalierungsgruppe mithilfe der Azure Resource Manager-Vorlage eine benutzerzugewiesene verwaltete Identität zu.

> [!Note]
> Weitere Informationen zum Erstellen einer vom Benutzer zugewiesenen verwalteten Identität mit einer Azure Resource Manager-Vorlage finden Sie unter [Erstellen einer vom Benutzer zugewiesenen verwalteten Identität](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einer VM-Skalierungsgruppe

1. Fügen Sie unter dem `resources`-Element den folgenden Eintrag hinzu, um Ihrer VM-Skalierungsgruppe eine benutzerzugewiesene verwaltete Identität zuzuweisen.  Achten Sie darauf, dass Sie `<USERASSIGNEDIDENTITY>` durch den Namen der vom Benutzer zugewiesenen verwalteten Identität ersetzen, die Sie erstellt haben.

   **Microsoft.Compute/virtualMachineScaleSets-API, Version 2018-06-01**

   Wenn `2018-06-01` als apiVersion verwendet wird, werden die vom Benutzer zugewiesenen verwalteten Identitäten im `userAssignedIdentities`-Wörterbuchformat gespeichert, und der Wert `<USERASSIGNEDIDENTITYNAME>` muss in einer im Abschnitt `variables` der Vorlage definierten Variable gespeichert werden.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets-API, Version 2017-12-01**

   Wenn `2017-12-01` oder früher als `apiVersion` verwendet wird, werden die vom Benutzer zugewiesenen verwalteten Identitäten im `identityIds`-Array gespeichert, und der Wert `<USERASSIGNEDIDENTITYNAME>` muss in einer im Variablenabschnitt der Vorlage definierten Variable gespeichert werden.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> Sie können die verwalteten Identitäten für die Erweiterung der VM-Skalierungsgruppe von Azure-Ressourcen optional bereitstellen, indem Sie sie als `extensionProfile`-Element in der Vorlage angeben. Dieser Schritt ist optional, da Sie für den Tokenabruf auch den Azure IMDS-Identitätsendpunkt (Instance Metadata Service) verwenden können.  Weitere Informationen finden Sie unter [Migrieren von der VM-Erweiterung zu Azure IMDS für die Authentifizierung](howto-migrate-vm-extension.md).

3. Wenn Sie fertig sind, sollte Ihre Vorlage wie folgt aussehen:

   **Microsoft.Compute/virtualMachineScaleSets-API, Version 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
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
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines-API, Version 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
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
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität aus einer Azure VM-Skalierungsgruppe

Wenn für die VM-Skalierungsgruppe eine benutzerzugewiesene verwaltete Identität nicht mehr benötigt wird, gehen Sie wie folgt vor:

1. Verwenden Sie unabhängig davon, ob Sie sich bei Azure lokal oder über das Azure-Portal anmelden, ein Konto, das dem Azure-Abonnement zugeordnet ist, das die VM-Skalierungsgruppe enthält.

2. Laden Sie die Vorlage in einen [Editor](#azure-resource-manager-templates), und suchen Sie nach der gewünschten `Microsoft.Compute/virtualMachineScaleSets`-Ressource im Abschnitt `resources`. Wenn Ihre VM-Skalierungsgruppe nur über eine vom Benutzer zugewiesene verwaltete Identität verfügt, können Sie diese deaktivieren, indem Sie den Identitätstyp in `None` ändern.

   Das folgende Beispiel zeigt, wie Sie alle vom Benutzer zugewiesenen verwalteten Identitäten von einem virtuellen Computer ohne vom System zugewiesene verwaltete Identitäten entfernen:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets-API, Version 2018-06-01**

   Um eine einzelne verwaltete Identität, die von einem Benutzer zugewiesen wurde, aus einer VM-Skalierungsgruppe zu entfernen, müssen Sie sie aus dem `userAssignedIdentities`-Wörterbuch entfernen.

   Wenn Sie eine vom System zugewiesene Identität verwenden, behalten Sie den Wert `type` unter dem Wert `identity` bei.

   **Microsoft.Compute/virtualMachineScaleSets-API, Version 2017-12-01**

   Um eine einzelne benutzerzugewiesene verwaltete Identität aus einer VM-Skalierungsgruppe zu entfernen, entfernen Sie sie aus dem `identityIds`-Array.

   Wenn Sie eine vom System zugewiesene verwaltete Identität verwenden, behalten Sie den Wert `type` unter dem Wert `identity` bei.

## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)