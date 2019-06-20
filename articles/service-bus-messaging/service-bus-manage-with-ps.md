---
title: Verwenden von PowerShell zum Verwalten von Azure Service Bus-Ressourcen | Microsoft-Dokumentation
description: Verwenden des PowerShell-Moduls zum Erstellen und Verwalten von Service Bus-Ressourcen
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 0d15aa4d7b8a922f7606b7c4d1b357a80b3cbfab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311045"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Verwenden von PowerShell zum Verwalten von Service Bus-Ressourcen

Microsoft Azure PowerShell ist eine Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung von Azure-Diensten steuern und automatisieren können. In diesem Artikel wird beschrieben, wie Sie mithilfe des [Resource Manager-PowerShell-Moduls für Service Bus](/powershell/module/az.servicebus) Service Bus-Entitäten (Namespaces, Warteschlangen, Themen und Abonnements) über eine lokale Azure PowerShell-Konsole oder ein lokales Azure PowerShell-Skript bereitstellen und verwalten.

Sie können Service Bus-Entitäten zudem mithilfe von Azure Resource Manager-Vorlagen erstellen. Weitere Informationen finden Sie im Artikel [Erstellen von Service Bus-Ressourcen mithilfe von Azure Resource Manager-Vorlagen](service-bus-resource-manager-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Azure erwerben][purchase options], [Spezielle Angebote][member offers] oder [Erstellen Sie noch heute Ihr kostenloses Azure-Konto][free account].
* Einen Computer mit Azure PowerShell. Anweisungen finden Sie unter [Erste Schritte mit Azure PowerShell-Cmdlets](/powershell/azure/get-started-azureps).
* Allgemeine Kenntnisse über PowerShell-Skripts, NuGet-Pakete und .NET Framework.

## <a name="get-started"></a>Erste Schritte

Im ersten Schritt verwenden Sie PowerShell zum Anmelden bei Ihrem Azure-Konto und Ihrem Azure-Abonnement. Befolgen Sie die Anweisungen unter [Erste Schritte mit Azure PowerShell-Cmdlets](/powershell/azure/get-started-azureps), um sich bei Ihrem Azure-Konto anzumelden, die Ressourcen in Ihrem Azure-Abonnement abzurufen und auf diese zuzugreifen.

## <a name="provision-a-service-bus-namespace"></a>Bereitstellen eines Service Bus-Namespace

Für die Arbeit mit Service Bus-Namespaces können Sie die Cmdlets [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace) und [Set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) verwenden.

In diesem Beispiel werden einige lokale Variablen im Skript erstellt: `$Namespace` und `$Location`.

* `$Namespace` ist der Name des Service Bus-Namespace, mit dem Sie arbeiten möchten.
* `$Location` identifiziert das Rechenzentrum, in dem Sie den Namespace bereitstellen.
* `$CurrentNamespace` speichert den Referenznamespace, den Sie abrufen (oder erstellen).

In einem tatsächlichen Skript können `$Namespace` und `$Location` als Parameter übergeben werden.

Dieser Teil des Skripts führt Folgendes aus:

1. Es wird versucht, einen Service Bus-Namespace mit dem angegebenen Namen zu finden.
2. Wenn der Namespace gefunden wird, erfolgt eine Meldung, was gefunden wurde.
3. Wenn der Namespace nicht gefunden wird, wird der Namespace erstellt und anschließend der neu erstellte Namespace abgerufen.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Erstellen einer Namespace-Autorisierungsregel

Im folgenden Beispiel wird gezeigt, wie Namespace-Autorisierungsregeln mithilfe der Cmdlets [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule) und [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) verwaltet werden.

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Führen Sie zum Erstellen einer Warteschlange oder eines Themas mit dem Skript aus dem vorherigen Abschnitt eine Namespaceüberprüfung durch. Erstellen Sie dann die Warteschlange:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Ändern von Warteschlangeneigenschaften

Nach dem Ausführen des Skripts aus dem vorherigen Abschnitt können Sie mit dem Cmdlet [Set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) die Eigenschaften einer Warteschlange wie im folgenden Beispiel aktualisieren:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Bereitstellen weiterer Service Bus-Entitäten

Mit dem [PowerShell-Modul für Service Bus](/powershell/module/az.servicebus) können Sie andere Entitäten bereitstellen, z.B. Themen und Abonnements. Diese Cmdlets ähneln in ihrer Syntax den im vorherigen Abschnitt vorgestellten Cmdlets zur Warteschlangenerstellung.

## <a name="next-steps"></a>Nächste Schritte

- Die vollständige Dokumentation zum Resource Manager-PowerShell-Modul für Service Bus finden Sie [hier](/powershell/module/az.servicebus). Auf dieser Seite sind alle verfügbaren Cmdlets aufgeführt.
- Informationen zum Verwenden von Azure Resource Manager-Vorlagen finden Sie im Artikel [Erstellen von Service Bus-Ressourcen mithilfe von Azure Resource Manager-Vorlagen](service-bus-resource-manager-overview.md).
- Informationen zu [Service Bus .NET-Verwaltungsbibliotheken](service-bus-management-libraries.md).

Service Bus-Entitäten können auch auf andere Weise verwaltet werden. Dies wird in den folgenden Blogbeiträgen beschrieben:

* [How to create Service Bus queues, topics and subscriptions using a PowerShell script (Erstellen von Service Bus-Warteschlangen, -Themen und -Abonnements mithilfe eines PowerShell-Skripts, in englischer Sprache)](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [How to create a Service Bus Namespace and an Event Hub using a PowerShell script (Erstellen eines Service Bus-Namespace und eines Event Hubs mithilfe eines PowerShell-Skripts, in englischer Sprache)](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Service Bus PowerShell-Skripts](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
