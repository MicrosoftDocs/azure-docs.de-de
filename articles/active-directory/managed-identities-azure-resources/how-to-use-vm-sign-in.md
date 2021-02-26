---
title: Verwenden von verwalteten Identitäten auf einem virtuellen Azure-Computer für die Anmeldung – Azure AD
description: Schrittweise Anleitungen und Beispiele für die Skriptclient-Anmeldung und den Ressourcenzugriff mit verwalteten Identitäten auf einem virtuellen Azure-Computer für Azure-Ressourcen (Dienstprinzipal).
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 61e83bd27c9434c4222e0161e3b643b183d1aa84
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090959"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Verwenden von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer für die Anmeldung 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Dieser Artikel enthält PowerShell- und CLI-Skriptbeispiele für die Anmeldung mit verwalteten Identitäten für Azure-Ressourcen sowie eine Anleitung zu wichtigen Themen, z.B. zur Fehlerbehandlung.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Wenn Sie die Azure PowerShell- oder Azure CLI-Beispiele in diesem Artikel verwenden möchten, müssen Sie die neueste Version von [Azure PowerShell](/powershell/azure/install-az-ps) bzw. der [Azure CLI](/cli/azure/install-azure-cli) installieren. 

> [!IMPORTANT]
> - Bei allen Beispielskripts in diesem Artikel wird vorausgesetzt, dass der Befehlszeilenclient auf einem virtuellen Computer mit verwalteten Identitäten für Azure-Ressourcen ausgeführt wird. Verwenden Sie die Funktion „Verbinden“ im Azure-Portal zum Herstellen einer Remoteverbindung mit Ihrem virtuellen Computer. Weitere Informationen zur Aktivierung von verwalteten Identitäten für Azure-Ressourcen auf einer VM finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einer VM über das Azure-Portal](qs-configure-portal-windows-vm.md) oder in einem der verwandten Artikel (PowerShell, CLI, Vorlage oder Azure SDK). 
> - Um Fehler beim Zugriff auf Ressourcen zu verhindern, muss die verwaltete Identität des virtuellen Computers mindestens über den Zugriff „Leser“ im entsprechenden Bereich (virtueller Computer oder höher) verfügen, um Azure Resource Manager-Vorgänge auf dem virtuellen Computer zu ermöglichen. Weitere Informationen finden Sie unter [Zuweisen verwalteter Identitäten für den Zugriff auf Azure-Ressourcen über das Azure-Portal](howto-assign-access-portal.md).

## <a name="overview"></a>Übersicht

Verwaltete Identitäten für Azure-Ressourcen stellen ein [Dienstprinzipalobjekt](../develop/developer-glossary.md#service-principal-object) bereit, das [bei der Aktivierung von verwalteten Identitäten für Azure-Ressourcen](overview.md) auf dem virtuellen Computer erstellt wird. Der Dienstprinzipal kann Zugriff auf Azure-Ressourcen erhalten und von Skript- oder Befehlszeilenclients als Identität für die Anmeldung und den Zugriff auf Ressourcen verwendet werden. Bislang war für einen Skriptclient für den Zugriff auf geschützte Ressourcen unter seiner eigenen Identität Folgendes erforderlich:  

   - Registrierung und Zustimmung als vertrauliche und Webclientanwendung in Azure AD
   - Anmeldung unter dem zugehörigen Dienstprinzipal mit den Anmeldeinformationen der Anwendung (wahrscheinlich im Skript eingebettet)

Mithilfe verwalteter Identitäten für Azure-Ressourcen muss Ihr Skriptclient beides nicht mehr ausführen, da er sich unter dem Dienstprinzipal der verwalteten Identitäten für Azure-Ressourcen anmelden kann. 

## <a name="azure-cli"></a>Azure CLI

Mit diesem Skript wird Folgendes veranschaulicht:

1. Anmelden bei Azure AD unter dem Dienstprinzipal der verwalteten Identität des virtuellen Computers für Azure-Ressourcen  
2. Aufrufen von Azure Resource Manager und Abrufen der Dienstprinzipal-ID des virtuellen Computers. Die Verwaltung des Tokenabrufs und der Tokenverwendung erfolgt automatisch über die Befehlszeilenschnittstelle. Achten Sie darauf, dass Sie `<VM-NAME>` durch den Namen Ihres virtuellen Computers ersetzen.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Mit diesem Skript wird Folgendes veranschaulicht:

1. Anmelden bei Azure AD unter dem Dienstprinzipal der verwalteten Identität des virtuellen Computers für Azure-Ressourcen  
2. Aufrufen eines Azure Resource Manager-Cmdlets zum Abrufen von Informationen zu dem virtuellen Computer. Die Verwaltung der Tokenverwendung erfolgt automatisch über PowerShell.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Ressourcen-IDs für Azure-Dienste

Eine Liste mit Ressourcen, die Azure AD unterstützen und mit verwalteten Identitäten für Azure-Ressourcen getestet wurden, und die jeweiligen zugehörigen Ressourcen-IDs finden Sie unter [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

## <a name="error-handling-guidance"></a>Anleitung zur Fehlerbehandlung 

Antworten wie beispielsweise die folgenden können angeben, dass die verwaltete Identität des virtuellen Computers für Azure-Ressourcen nicht ordnungsgemäß konfiguriert wurde:

- Mit PowerShell: *Invoke-WebRequest: Es konnte keine Verbindung mit dem Remoteserver hergestellt werden.*
- Über die CLI: *MSI: Failed to retrieve a token from `http://localhost:50342/oauth2/token` with an error of 'HTTPConnectionPool(host='localhost', port=50342)* (MSI: Fehler beim Abrufen eines Tokens aus 'http://localhost:50342/oauth2/token' mit dem Fehler 'HTTPConnectionPool[host='localhost', port=50342]) 

Wenn einer dieser Fehler ausgegeben wird, kehren Sie im [Azure-Portal](https://portal.azure.com) zum virtuellen Azure-Computer zurück, wechseln Sie zur Seite **Identität**, und stellen Sie sicher, dass **Vom System zugewiesen** auf „Ja“ eingestellt ist.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Aktivieren verwalteter Identitäten für Azure-Ressourcen auf einer Azure-VM finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mit PowerShell](qs-configure-powershell-windows-vm.md) und [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md)