---
title: Bereitstellen und Verwalten von Notification Hubs mit PowerShell
description: Erstellen und Verwalten von Notification Hubs mit PowerShell für Automation
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 4dbbaeea736dd46478ad9992201ea28bd7bfc2ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457837"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Bereitstellen und Verwalten von Notification Hubs mit PowerShell

## <a name="overview"></a>Übersicht

In diesem Artikel wird das Erstellen und Verwalten von Azure Notification Hubs mithilfe von PowerShell veranschaulicht. Die folgenden allgemeinen Automatisierungsaufgaben werden in diesem Artikel dargestellt.

- Erstellen eines Notification Hubs
- Festlegen von Anmeldeinformationen

Wenn Sie auch einen neuen Servicebus-Namespace für Ihre Notification Hubs erstellen müssen, finden Sie Informationen dazu unter [Verwalten von Service Bus mit PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Das Verwalten von Notification Hubs wird nicht direkt von den in Azure PowerShell enthaltenen Cmdlets unterstützt. Der beste Ansatz für PowerShell ist der Verweis auf die Microsoft.Azure.NotificationHubs.dll-Assembly. Die Assembly wird zusammen mit dem [Microsoft Azure Notification Hubs-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)verteilt.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen], [Spezielle Angebote] oder [Kostenlose Testversion].
- Einen Computer mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell].
- Allgemeine Kenntnisse über PowerShell-Skripts, NuGet-Pakete und .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Einschließen eines Verweises auf die .NET-Assembly für Service Bus

Das Verwalten von Azure Notification Hubs ist noch nicht in den PowerShell-Cmdlets in Azure PowerShell enthalten. Um Notification Hubs bereitzustellen, können Sie den .NET-Client aus dem [Microsoft Azure Notification Hubs-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)verwenden.

Sie müssen zunächst sicherstellen, dass Ihr Skript die **Microsoft.Azure.NotificationHubs.dll** -Assembly findet, die als NuGet-Paket in einem Visual Studio-Projekt installiert wird. Aus Gründen der Flexibilität führt das Skript folgende Schritte aus:

1. Es bestimmt den Pfad, in dem es aufgerufen wurde.
2. Es durchläuft den Pfad, bis es einen Ordner mit dem Namen `packages`gefunden hat. Dieser Ordner wird erstellt, wenn Sie NuGet-Pakete für Visual Studio-Projekte installieren.
3. Es durchsucht den Ordner `packages` rekursiv nach einer Assembly mit der Bezeichnung `Microsoft.Azure.NotificationHubs.dll`.
4. Es verweist auf die Assembly, sodass die Typen zur späteren Verwendung zur Verfügung stehen.

Diese Schritte werden in einem PowerShell-Skript folgendermaßen implementiert:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Erstellen der `NamespaceManager`-Klasse

Notification Hubs bereitzustellen, erstellen Sie eine Instanz der [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) -Klasse aus dem SDK.

Sie können mithilfe des in Azure Power Shell enthaltenen Cmdlets [Get-AzureSBAuthorizationRule] eine Autorisierungsregel abrufen, die zur Bereitstellung einer Verbindungszeichenfolge verwendet wird. Ein Verweis auf die `NamespaceManager`-Instanz wird in der `$NamespaceManager`-Variable gespeichert. `$NamespaceManager` wird verwendet, um einen Notification Hub bereitzustellen.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Bereistellung eines neuen Notification Hubs

Verwenden Sie zum Bereitstellen eines neuen Notification Hubs die [.NET-API für Notification Hubs].

In diesem Teil des Skripts erstellen Sie vier lokale Variablen.

1. `$Namespace`: Legen Sie diese Variable auf den Namen des Namespace fest, in dem Sie einen Notification Hub erstellen möchten.
2. `$Path`: Legen Sie diesen Pfad auf den Namen des neuen Notification Hubs fest.  Beispiel: "MyHub".
3. `$WnsPackageSid`: Legen Sie diese Variable auf die Paket-SID für Ihre Windows-App aus dem [Windows Dev Center](https://developer.microsoft.com/en-us/windows) fest.
4. `$WnsSecretkey`: Legen Sie diese mithilfe von [Windows Dev Center](https://developer.microsoft.com/en-us/windows) auf den geheimen Schlüssel für Ihre Windows-App fest.

Diese Variablen werden verwendet, um eine Verbindung mit dem Namespace herzustellen und einen neuen Notification Hub zu erstellen, damit Benachrichtigungen von Windows Notification Services (WNS) mit WNS-Anmeldeinformationen für eine Windows-App verarbeitet werden können. Weitere Informationen zum Abrufen der Paket-SID und des geheimen Schlüssels finden Sie im Lernprogramm [Erste Schritte mit Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .

- Der Codeausschnitt verwendet das `NamespaceManager`-Objekt, um zu prüfen, ob der von `$Path` erkannte Notification Hub vorhanden ist.
- Falls er nicht vorhanden ist, erstellt das Skript eine `NotificationHubDescription` mit WNS-Anmeldeinformationen und übergibt diese an die `CreateNotificationHub`-Methode in der `NamespaceManager`-Klasse.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Weitere Ressourcen

- [Verwalten von Service Bus mit PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [How to create Service Bus queues, topics and subscriptions using a PowerShell script (Erstellen von Service Bus-Warteschlangen, -Themen und -Abonnements mithilfe eines PowerShell-Skripts, in englischer Sprache)](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [How to create a Service Bus Namespace and an Event Hub using a PowerShell script (Erstellen eines Service Bus-Namespace und eines Event Hubs mithilfe eines PowerShell-Skripts, in englischer Sprache)](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Es stehen auch einige einsatzbereite Skripts zum Download zur Verfügung:

- [Service Bus PowerShell-Skripts](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Kaufoptionen]: https://azure.microsoft.com/pricing/purchase-options/
[Spezielle Angebote]: https://azure.microsoft.com/pricing/member-offers/
[Kostenlose Testversion]: https://azure.microsoft.com/pricing/free-trial/
[Installieren und Konfigurieren von Azure PowerShell]: /powershell/azureps-cmdlets-docs
[.NET-API für Notification Hubs]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
