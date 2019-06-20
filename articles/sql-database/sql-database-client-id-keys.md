---
title: Abrufen von Werten zum Authentifizieren einer App – Azure SQL-Datenbank | Microsoft-Dokumentation
description: Erstellen Sie einen Dienstprinzipalnamen für den Zugriff auf SQL-Datenbank aus dem Code.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 1d60e875b12f02c957ebd6259eb0e7267f23ee51
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150199"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Abrufen der erforderlichen Werte aus dem Code zum Authentifizieren einer Anwendung für den Zugriff auf die SQL-Datenbank

Zum Erstellen und Verwalten der SQL-Datenbank über den Code müssen Sie Ihre App in der Azure Active Directory-Domäne (AAD) in dem Abonnement registrieren, in dem die Azure-Ressourcen erstellt wurden.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen aus einer Anwendung

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Mit dem folgenden PowerShell-Skript werden die Active Directory (AD)-Anwendung und der Dienstprinzipal erstellt, den wir zum Authentifizieren der C#-App benötigen. Das Skript gibt Werte aus, die für das vorhergehende C#-Beispiel erforderlich sind. Ausführliche Informationen finden Sie unter [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe von Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

    # Sign in to Azure.
    Connect-AzAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create an AAD app
    $azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Weitere Informationen
* [Erstellen einer SQL-Datenbank mit C#](sql-database-get-started-csharp.md)
* [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md)

