---
title: Erstellen einer Azure AD-App mithilfe von PowerShell für den Zugriff auf die Azure Media Services-API | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mithilfe von PowerShell eine Azure AD-App (Azure Active Directory) erstellen und diese für den Zugriff auf die Azure Media Services-API einrichten.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ff7f8bc27d358c667b10c0bd3383e78b20494303
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680130"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Erstellen einer Azure AD-App mithilfe von PowerShell für die Verwendung mit der Azure Media Services-API

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

In diesem Artikel erfahren Sie, wie Sie mithilfe eines PowerShell-Skripts eine Azure AD-Anwendung (Azure Active Directory) und einen Dienstprinzipal erstellen, um auf Azure Media Services-Ressourcen zuzugreifen.  

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Beginnen Sie mit einer [kostenlosen Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/), falls Sie kein Konto haben. 
- Media Services-Konto. Weitere Informationen finden Sie unter [Erstellen eines Azure Media Services-Kontos mithilfe des Azure-Portals](media-services-portal-create-account.md).

- Azure PowerShell. Weitere Informationen finden Sie unter [Vorgehensweise zur Verwendung von Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Erstellen einer Azure AD-App mithilfe von PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe von Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Verwalten der rollenbasierten Zugriffssteuerung mit Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Vorgehensweise zur manuellen Konfiguration von Daemon-Apps mithilfe von Zertifikaten](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit dem [Hochladen von Dateien in Ihr Konto](media-services-portal-upload-files.md).
