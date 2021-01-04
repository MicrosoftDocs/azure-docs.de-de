---
title: 'Azure Key Vault: Verschieben eines Tresors in ein anderes Abonnement | Microsoft-Dokumentation'
description: Anleitung zum Verschieben eines Schlüsseltresors in ein anderes Abonnement.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: e0cd4cad74257dbf83ec8d30405eacca341a8d31
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289531"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Verschieben einer Azure Key Vault-Instanz in ein anderes Abonnement

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht

> [!IMPORTANT]
> **Das Verschieben eines Schlüsseltresors in ein anderes Abonnement stellt einen Breaking Change Ihrer Umgebung dar.**
> Stellen Sie sicher, dass Ihnen die Auswirkungen dieser Änderung bekannt sind, und befolgen Sie die Anleitungen in diesem Artikel sorgfältig, bevor Sie sich entscheiden, einen Schlüsseltresor in ein neues Abonnement zu verschieben.
> Wenn Sie verwaltete Dienstidentitäten (Managed Service Identities, MSI) verwenden, lesen Sie die Anweisungen für Aktionen im Anschluss an das Verschieben am Ende des Dokuments. 

Wenn Sie einen Schlüsseltresor erstellen, wird er automatisch an die standardmäßige Azure Active Directory-Mandanten-ID für das Abonnement gebunden, in dem er erstellt wurde. Außerdem werden auch alle Zugriffsrichtlinieneinträge an diese Mandanten-ID gebunden. Wenn Sie Ihr Azure-Abonnement aus Mandant A in Mandant B verschieben, können die Dienstprinzipale (Benutzer und Anwendungen) in Mandant B nicht auf Ihre vorhandenen Schlüsseltresore zugreifen. Gehen Sie wie folgt vor, um dies zu beheben:

* Ändern Sie die Mandanten-ID, die allen vorhandenen Schlüsseltresoren im Abonnement zugeordnet ist, in den Mandanten B.
* Entfernen Sie alle vorhandenen Zugriffsrichtlinieneinträge.
* Fügen Sie neue Zugriffsrichtlinieneinträge hinzu, die Mandant B zugeordnet sind.

## <a name="limitations"></a>Einschränkungen

> [!IMPORTANT]
> **Für die Datenträgerverschlüsselung verwendete Schlüsseltresore können nicht verschoben werden** Wenn Sie Key Vault mit Datenträgerverschlüsselung für einen virtuellen Computer verwenden, kann der Schlüsseltresor nicht in eine andere Ressourcengruppe oder ein anderes Abonnement verschoben werden, solange die Datenträgerverschlüsselung aktiviert ist. Sie müssen die Datenträgerverschlüsselung deaktivieren, bevor Sie den Schlüsseltresor in eine neue Ressourcengruppe oder ein neues Abonnement verschieben. 

Einige Dienstprinzipale (Benutzer und Anwendungen) sind an einen bestimmten Mandanten gebunden. Wenn Sie Ihren Schlüsseltresor in ein Abonnement in einem anderen Mandanten verschieben, besteht die Möglichkeit, dass Sie den Zugriff auf einen bestimmten Dienstprinzipal nicht wiederherstellen können. Stellen Sie sicher, dass alle wichtigen Dienstprinzipale in dem Mandanten vorhanden sind, in den Sie Ihren Schlüsseltresor verschieben.

## <a name="design-considerations"></a>Überlegungen zum Entwurf

Ihre Organisation hat möglicherweise Azure Policy mit Erzwingung oder Ausschlüssen auf Abonnementebene implementiert. In dem Abonnement, in dem der Schlüsseltresor derzeit enthalten ist, und in dem Abonnement, in das Sie den Schlüsseltresor verschieben, gilt möglicherweise ein unterschiedlicher Satz von Richtlinienzuweisungen. Ein Konflikt in den Richtlinienanforderungen kann Ihre Anwendungen potenziell unterbrechen.

### <a name="example"></a>Beispiel

Sie verfügen über eine Anwendung, die mit dem Schlüsseltresor, der Zertifikate erstellt, verbunden ist. Die Zertifikate sind zwei Jahre gültig. Das Abonnement, in das Sie den Schlüsseltresor verschieben möchten, verfügt über eine Richtlinienzuweisung, die die Erstellung von Zertifikaten blockiert, die länger als ein Jahr gültig sind. Nachdem Sie Ihren Schlüsseltresor in das neue Abonnement verschoben haben, wird der Vorgang zum Erstellen eines Zertifikats, das zwei Jahre gültig ist, durch eine Azure Policy-Zuweisung blockiert.

### <a name="solution"></a>Lösung

Überprüfen Sie auf der Seite „Azure Policy“ im Azure-Portal die Richtlinienzuweisungen für Ihr aktuelles Abonnement und das Zielabonnement, und stellen Sie sicher, dass keine Konflikte bestehen.

## <a name="prerequisites"></a>Voraussetzungen

* Zugriffsebene „Mitwirkender“ oder höher für das aktuelle Abonnement mit dem Schlüsseltresor
* Zugriffsebene „Mitwirkende“ oder höher für das Abonnement, in das Sie Ihren Schlüsseltresor verschieben möchten
* Eine Ressourcengruppe im neuen Abonnement

## <a name="procedure"></a>Verfahren

### <a name="moving-key-vault-to-a-new-subscription-within-the-same-tenant"></a>Verschieben des Schlüsseltresors in ein neues Abonnement innerhalb desselben Mandanten

1. Anmelden beim Azure-Portal
2. Navigieren zum Schlüsseltresor
3. Klicken auf die Registerkarte „Übersicht“
4. Auswählen der Schaltfläche „Verschieben“
5. Auswählen der Option „In ein anderes Abonnement verschieben“ im Dropdownmenü
6. Auswählen der Ressourcengruppe, in die der Schlüsseltresor verschoben werden soll
7. Bestätigen der Warnung zum Verschieben von Ressourcen
8. Auswählen von „OK“

### <a name="additional-steps-if-you-moved-key-vault-to-a-subscription-in-a-new-tenant"></a>Weitere Schritte nach dem Verschieben des Schlüsseltresors in ein Abonnement in einem neuen Mandanten

Wenn Sie Ihren Schlüsseltresor in ein Abonnement in einem neuen Mandanten verschoben haben, müssen Sie die Mandanten-ID manuell aktualisieren und alte Zugriffsrichtlinien entfernen. Hier finden Sie Tutorials für diese Schritte mit PowerShell und der Azure-Befehlszeilenschnittstelle. Wenn Sie PowerShell verwenden, müssen Sie möglicherweise den unten dokumentierten Befehl Clear-AzContext ausführen, damit Sie Ressourcen außerhalb des aktuell ausgewählten Bereichs anzeigen können. 

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Nachdem Sie Ihren Tresor nun der richtigen Mandanten-ID zugeordnet haben und alte Zugriffsrichtlinieneinträge entfernt wurden, können Sie neue Zugriffsrichtlinieneinträge mit dem Azure PowerShell-Cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) oder dem Azure CLI-Befehl [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) festlegen.

Wenn Sie eine verwaltete Identität für Azure-Ressourcen verwenden, müssen Sie sie ebenfalls auf den neuen Azure Active Directory-Mandanten aktualisieren. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Verwaltete Identitäten: Übersicht](../../active-directory/managed-identities-azure-resources/overview.md).

Wenn Sie verwaltete Identitäten verwenden, müssen Sie auch die Identität aktualisieren, weil sich die alte Identität nicht mehr im richtigen Azure Active Directory-Mandanten befindet. Informationen zum Beheben dieses Problems finden Sie in den folgenden Dokumenten. 

* [Aktualisieren von MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Übertragen eines Abonnements in ein neues Verzeichnis](../../role-based-access-control/transfer-subscription.md)