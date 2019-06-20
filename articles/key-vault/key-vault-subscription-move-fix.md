---
title: 'Ändern der Mandanten-ID des Schlüsseltresors nach einem Abonnementwechsel: Azure Key Vault | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie die Mandanten-ID für einen Schlüsseltresor ändern, nachdem ein Abonnement in einen anderen Mandanten verschoben wurde.
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: f32146697be234a8a288ff991b1f7adf6e76dc7e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724494"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Ändern der Mandanten-ID des Schlüsseltresors nach einer Abonnementverschiebung

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>F: Mein Abonnement wurde von Mandant A zu Mandant B verschoben. Wie ändere ich die Mandanten-ID für meinen vorhandenen Schlüsseltresor und lege für Mandant B die richtigen ACLs für Prinzipale fest?

Wenn Sie in einem Abonnement einen neuen Schlüsseltresor erstellen, wird er automatisch an die standardmäßige Azure Active Directory-Mandanten-ID für das Abonnement gebunden. Außerdem werden auch alle Zugriffsrichtlinieneinträge an diese Mandanten-ID gebunden. Wenn Sie Ihr Azure-Abonnement aus Mandant A in Mandant B verschieben, können die Prinzipale (Benutzer und Anwendungen) in Mandant B nicht auf Ihre vorhandenen Schlüsseltresore zugreifen. Gehen Sie wie folgt vor, um dies zu beheben:

* Ändern Sie die Mandanten-ID, die allen vorhandenen Schlüsseltresoren des Abonnements zugeordnet ist, in den Mandanten B.
* Entfernen Sie alle vorhandenen Zugriffsrichtlinieneinträge.
* Fügen Sie neue Zugriffsrichtlinieneinträge hinzu, die Mandant B zugeordnet sind.

Wenn beispielsweise der Schlüsseltresor „myvault“ in einem Abonnement enthalten ist, das von Mandant A zu Mandant B verschoben wurde, können Sie die Mandanten-ID für diesen Schlüsseltresor ändern und alte Zugriffsrichtlinien entfernen.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Da sich dieser Tresor vor der Verschiebung unter Mandant A befunden hat, lautet der ursprüngliche Wert von **$vault.Properties.TenantId** „Mandant A“, während er für **(Get-AzContext).Tenant.TenantId** „Mandant B“ lautet.

Nachdem Sie Ihren Tresor nun der richtigen Mandanten-ID zugeordnet haben und alte Zugriffsrichtlinieneinträge entfernt wurden, können Sie neue Zugriffsrichtlinieneinträge mit [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy)festlegen.

## <a name="next-steps"></a>Nächste Schritte

Besuchen Sie die [Azure Key Vault-Foren](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault), wenn Sie Fragen zu Azure Key Vault haben.
