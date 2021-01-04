---
title: Entfernen des Zugriffs auf eine Delegierung
description: Erfahren Sie, wie Sie Zugriff auf Ressourcen entfernen können, die an einen Dienstanbieter für Azure Lighthouse delegiert worden waren.
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: d3442bb9fd2f6e7423fd4bf28cace1f7fd91ad80
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608451"
---
# <a name="remove-access-to-a-delegation"></a>Entfernen des Zugriffs auf eine Delegierung

Nachdem das Abonnement oder die Ressourcengruppe eines Kunden an einen Dienstanbieter für [Azure Lighthouse](../overview.md) delegiert wurde, kann die Delegierung bei Bedarf entfernt werden. Nachdem eine Delegierung entfernt wurde, gilt der Zugriff [der delegierten Azure-Ressourcenverwaltung](../concepts/azure-delegated-resource-management.md), der den Benutzern im Dienstanbietermandanten zuvor gewährt wurde, nicht mehr.

Das Entfernen einer Delegierung kann durch einem Benutzer entweder im Kundenmandanten oder im Dienstanbietermandanten ausgeführt werden, sofern der Benutzer über die entsprechenden Berechtigungen verfügt.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch können [Unternehmen, die mehrere Mandanten verwalten](../concepts/enterprise.md), denselben Prozess verwenden.

## <a name="customers"></a>Kunden

Benutzer im Mandanten des Kunden, die über die [integrierte Rolle „Besitzer“](../../role-based-access-control/built-in-roles.md#owner) für ein Abonnement verfügen, können Dienstanbieterzugriff auf dieses Abonnement (oder auf Ressourcengruppen in diesem Abonnement) entfernen. Zu diesem Zweck kann ein Benutzer im Mandanten des Kunden zur Seite [Dienstanbieter](view-manage-service-providers.md#add-or-remove-service-provider-offers) im Azure-Portal navigieren, nach dem Angebot auf dem Bildschirm **Dienstanbieterangebote** suchen und das Papierkorbsymbol in der Zeile für dieses Angebot auswählen.

Nach dem Bestätigten des Löschvorgangs können keine Benutzer im Mandanten des Dienstanbieters auf die zuvor delegierten Ressourcen zugreifen.

## <a name="service-providers"></a>Dienstanbieter

Benutzer in einem Mandanten, der verwaltet wird, können den Zugriff auf delegierte Ressourcen entfernen, wenn Ihnen die [Rolle zum Löschen der Registrierungszuweisung für verwaltete Dienste](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) für die Ressourcen des Kunden erteilt wurden. Wenn diese Rolle keinem Dienstanbieterbenutzer zugewiesen wurde, kann die Delegierung nur durch einen Benutzer im Mandanten des Kunden entfernt werden.

Im Beispiel unten wird eine Zuweisung gezeigt, die die **Rolle zum Löschen der Registrierungszuweisung für verwaltete Dienste** erteilt. Diese kann während des [Onboardingvorgangs](onboard-customer.md) in einer Parameterdatei enthalten sein:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Diese Rolle kann auch in einer **Autorisierung** ausgewählt werden, wenn ein [Angebot für einen verwalteten Dienst](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) zum Veröffentlichen in Azure Marketplace erstellt wird.

Ein Benutzer mit dieser Berechtigung kann eine Delegierung auf eine der folgenden Arten entfernen.

### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie zur Seite [Meine Kunden](view-manage-customers.md).
2. Wählen Sie **Delegierungen** aus.
3. Suchen Sie die Delegierung, die Sie entfernen möchten, und wählen Sie dann das Papierkorbsymbol aus, das in der Zeile angezeigt wird.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur [delegierten Azure-Ressourcenverwaltung](../concepts/azure-delegated-resource-management.md)
- [Anzeigen und Verwalten von Kunden](view-manage-customers.md), indem sie im Azure-Portal zu **Meine Kunden** navigieren.
- Erfahren Sie, wie Sie eine [vorherige Delegierung aktualisieren](update-delegation.md).
