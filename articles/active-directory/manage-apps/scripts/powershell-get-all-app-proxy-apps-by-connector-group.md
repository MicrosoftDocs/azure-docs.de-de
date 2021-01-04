---
title: Auflisten der Connectorgruppen des Azure AD-Anwendungsproxys für Apps
description: Hier finden Sie ein PowerShell-Beispiel, das alle Connectorgruppen des Anwendungsproxys von Azure Active Directory (Azure AD) mit den zugewiesenen Anwendungen auflistet.
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
ms.openlocfilehash: 008959c3e22f026314ec28b42b649e6c2dabefee
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861618"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Abrufen aller Anwendungsproxy-Apps und Auflisten der Apps nach Connectorgruppe

Mit diesem PowerShell-Beispielskript werden Informationen zu allen Connectorgruppen des Anwendungsproxys von Azure Active Directory (Azure AD) mit den zugewiesenen Anwendungen aufgelistet.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Für dieses Beispiel ist das [Azure AD PowerShell V2-Modul für Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) oder das [Azure AD PowerShell V2-Modul in der Vorschauversion für Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) erforderlich.

## <a name="sample-script"></a>Beispielskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Erläuterung des Skripts

| Get-Help | Notizen |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Ruft einen Dienstprinzipal ab. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Ruft eine Azure AD-Anwendung ab. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Ruft eine für den Anwendungsproxy in Azure AD konfigurierte Anwendung ab. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Ruft eine Liste aller Connectorgruppen oder (bei entsprechender Angabe) Details der angegebenen Connectorgruppe ab. |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure AD PowerShell-Modul finden Sie in der [Übersicht über das Azure AD PowerShell-Modul](/powershell/azure/active-directory/overview).

Weitere PowerShell-Beispiele für den Anwendungsproxy finden Sie unter [Azure AD PowerShell-Beispiele für Azure AD-Anwendungsproxy](../application-proxy-powershell-samples.md).
