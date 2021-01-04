---
title: 'PowerShell-Beispiel: Auflisten von Benutzern und Gruppen für eine Anwendungsproxy-App'
description: Hier finden Sie ein PowerShell-Beispiel, das alle einer bestimmten Anwendungsproxyanwendung von Azure Active Directory (Azure AD) zugewiesenen Benutzer und Gruppen auflistet.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c9885e42dc81d81dddea6205ed85be8276ffbf94
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861713"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Anzeigen der einer Anwendungsproxyanwendung zugewiesenen Benutzer und Gruppen

Mit diesem PowerShell-Beispielskript werden die einer bestimmten Anwendungsproxyanwendung von Azure Active Directory (Azure AD) zugewiesenen Benutzer und Gruppen aufgelistet.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Für dieses Beispiel ist das [Azure AD PowerShell V2-Modul für Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) oder das [Azure AD PowerShell V2-Modul in der Vorschauversion für Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) erforderlich.

## <a name="sample-script"></a>Beispielskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Erläuterung des Skripts

| Get-Help | Notizen |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| Ruft einen Benutzer ab. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| Ruft eine Gruppe ab. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Ruft einen Dienstprinzipal ab. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | Hiermit rufen Sie die Anwendungsrollenzuweisung eines Benutzers ab. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | Hiermit rufen Sie die Anwendungsrollenzuweisung einer Gruppe ab. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure AD PowerShell-Modul finden Sie in der [Übersicht über das Azure AD PowerShell-Modul](/powershell/azure/active-directory/overview).

Weitere PowerShell-Beispiele für den Anwendungsproxy finden Sie unter [Azure AD PowerShell-Beispiele für Azure AD-Anwendungsproxy](../application-proxy-powershell-samples.md).
