---
title: Verwenden von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer für die Anmeldung
description: Ausführliche Anweisungen und Beispiele für die Verwendung von verwalteten Identitäten eines virtuellen Azure-Computers für die Anmeldung und den Zugriff auf Ressourcen eines Skriptclients
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43aa0859fa67cc6b2f5c5974f072e7b6d4b29527
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66112963"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Verwenden von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer für die Anmeldung 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Dieser Artikel enthält PowerShell- und CLI-Skriptbeispiele für die Anmeldung mit verwalteten Identitäten für Azure-Ressourcen sowie eine Anleitung zu wichtigen Themen, z.B. zur Fehlerbehandlung.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Wenn Sie die Azure PowerShell- oder Azure CLI-Beispiele in diesem Artikel verwenden möchten, müssen Sie die neueste Version von [Azure PowerShell](/powershell/azure/install-az-ps) bzw. der [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) installieren. 

> [!IMPORTANT]
> - Bei allen Beispielskripts in diesem Artikel wird vorausgesetzt, dass der Befehlszeilenclient auf einem virtuellen Computer mit verwalteten Identitäten für Azure-Ressourcen ausgeführt wird. Verwenden Sie die Funktion „Verbinden“ im Azure-Portal zum Herstellen einer Remoteverbindung mit Ihrem virtuellen Computer. Weitere Informationen zur Aktivierung von verwalteten Identitäten für Azure-Ressourcen auf einer VM finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einer VM über das Azure-Portal](qs-configure-portal-windows-vm.md) oder in einem der verwandten Artikel (PowerShell, CLI, Vorlage oder Azure SDK). 
> - Um Fehler beim Zugriff auf Ressourcen zu verhindern, muss die verwaltete Identität des virtuellen Computers mindestens über den Zugriff „Leser“ im entsprechenden Bereich (virtueller Computer oder höher) verfügen, um Azure Resource Manager-Vorgänge auf dem virtuellen Computer zu ermöglichen. Weitere Informationen finden Sie unter [Zuweisen verwalteter Identitäten für den Zugriff auf Azure-Ressourcen über das Azure-Portal](howto-assign-access-portal.md).

## <a name="overview"></a>Übersicht

Verwaltete Identitäten für Azure-Ressourcen bieten ein [Dienstprinzipalobjekt](../develop/developer-glossary.md#service-principal-object), das [bei der Aktivierung von verwalteten Identitäten für Azure-Ressourcen](overview.md#how-does-it-work) auf dem virtuellen Computer erstellt wird. Der Dienstprinzipal kann Zugriff auf Azure-Ressourcen erhalten und von Skript- oder Befehlszeilenclients als Identität für die Anmeldung und den Zugriff auf Ressourcen verwendet werden. Bislang war für einen Skriptclient für den Zugriff auf geschützte Ressourcen unter seiner eigenen Identität Folgendes erforderlich:  

   - Registrierung und Zustimmung als vertrauliche und Webclientanwendung in Azure AD
   - Anmeldung unter dem zugehörigen Dienstprinzipal mit den Anmeldeinformationen der Anwendung (wahrscheinlich im Skript eingebettet)

Mithilfe verwalteter Identitäten für Azure-Ressourcen muss Ihr Skriptclient beides nicht mehr ausführen, da er sich unter dem Dienstprinzipal der verwalteten Identitäten für Azure-Ressourcen anmelden kann. 

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

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

- PowerShell: *Invoke-WebRequest: Es konnte keine Verbindung mit dem Remoteserver hergestellt werden.*
- CLI: *MSI: Failed to retrieve a token from `http://localhost:50342/oauth2/token` with an error of 'HTTPConnectionPool(host='localhost', port=50342)* (MSI: Fehler beim Abrufen eines Tokens aus 'http://localhost:50342/oauth2/token' mit dem Fehler 'HTTPConnectionPool[host='localhost', port=50342]) 

Wenn einer dieser Fehler angezeigt wird, wechseln Sie zu dem virtuellen Azure-Computer im [Azure-Portal](https://portal.azure.com) und:

- Stellen Sie auf der Seite **Identität** sicher, dass **Vom System zugewiesen** auf „Ja“ festgelegt ist.
- Vergewissern Sie sich auf der Seite **Erweiterungen**, dass die Erweiterung für verwaltete Identitäten für Azure-Ressourcen **(gilt ab Januar 2019 als veraltet)** erfolgreich bereitgestellt wurde.

Wenn einer dieser Punkte nicht zutrifft, müssen Sie die verwalteten Identitäten für Azure-Ressourcen möglicherweise erneut für die Ressource bereitstellen oder den Fehler bei der Bereitstellung beheben. Hilfe zur Konfiguration des virtuellen Computers finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](qs-configure-portal-windows-vm.md).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Aktivieren verwalteter Identitäten für Azure-Ressourcen auf einer Azure-VM finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mit PowerShell](qs-configure-powershell-windows-vm.md) und [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md)






