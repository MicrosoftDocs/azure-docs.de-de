---
title: Azure-Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen
description: Liste der Dienste, die verwaltete Identitäten für Azure-Ressourcen und die Azure AD-Authentifizierung unterstützen
services: active-directory
author: MarkusVi
ms.author: priyamo
ms.date: 05/09/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0809423472bde0240145d43f4c28d9594b517928
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65518687"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Mit einer verwalteten Identität können Sie sich bei jedem Dienst authentifizieren, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Verwaltete Identitäten für Azure-Ressourcen und die Azure AD-Authentifizierung werden derzeit in Azure integriert. Sie sollten sich regelmäßig über Aktualisierungen informieren.

> [!NOTE]
> „Verwaltete Identitäten für Azure-Ressourcen“ ist der neue Name für den Dienst, der früher als „Verwaltete Dienstidentität“ (Managed Service Identity, MSI) bezeichnet wurde.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen

Die folgenden Azure-Dienste unterstützen verwaltete Identitäten für Azure-Ressourcen:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Vorschau | Vorschau | Vorschau | 
| Vom Benutzer zugewiesen | Vorschau | Vorschau | Vorschau | Vorschau |

Konfigurieren Sie die verwaltete Identität für Azure Virtual Machines anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md)
- [Azure-Ressourcen-Manager-Vorlagen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Skalierungsgruppen für virtuelle Azure-Computer

|Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Vorschau | Vorschau | Vorschau |
| Vom Benutzer zugewiesen | Vorschau | Vorschau | Vorschau | Vorschau |

Konfigurieren Sie die verwaltete Identität für Azure Virtual Machine Scale Sets anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md)
- [Azure-Ressourcen-Manager-Vorlagen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Verfügbar | Verfügbar | Verfügbar |
| Vom Benutzer zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure App Service anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure-Befehlszeilenschnittstelle](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-Vorlage](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Verwenden Sie die folgende Liste, um eine verwaltete Identität mit [Azure Blueprints](../../governance/blueprints/overview.md) zu nutzen:

- [Azure-Portal: Blaupausenzuweisung](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST-API: Blaupausenzuweisung](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure-Funktionen

Typ der verwalteten Identität |Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Verfügbar | Verfügbar | Verfügbar |
| Vom Benutzer zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Functions anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure-Befehlszeilenschnittstelle](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-Vorlage](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Vorschau | Vorschau | Nicht verfügbar | Vorschau |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Logic Apps anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager-Vorlage](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Data Factory V2 anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar | Verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure API Management anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure Resource Manager-Vorlage](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Linux: Vorschau<br>Windows: Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Linux: Vorschau<br>Windows: Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Container Instances anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Befehlszeilenschnittstelle](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-Vorlage](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-Dienste, die die Azure AD-Authentifizierung unterstützen

Die folgenden Dienste unterstützen die Azure AD-Authentifizierung und wurden mit Clientdiensten getestet, die verwaltete Identitäten für Azure-Ressourcen verwenden.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Konfigurieren Sie den Zugriff auf Azure Resource Manager anhand der folgenden Liste:

- [Zuweisen des Zugriffs im Azure-Portal](howto-assign-access-portal.md)
- [Zuweisen des Zugriffs mithilfe von PowerShell](howto-assign-access-powershell.md)
- [Zuweisen des Zugriffs mithilfe der Azure CLI](howto-assign-access-CLI.md)
- [Zuweisen des Zugriffs mit Azure Resource Manager-Vorlagen](../../role-based-access-control/role-assignments-template.md)

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Verfügbar |
| Azure Government | `https://management.usgovcloudapi.net/` | Verfügbar |
| Azure Deutschland | `https://management.microsoftazure.de/` | Verfügbar |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Verfügbar |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Verfügbar |
| Azure Government | `https://vault.usgovcloudapi.net` | Verfügbar |
| Azure Deutschland |  `https://vault.microsoftazure.de` | Verfügbar |
| Azure China 21Vianet | `https://vault.azure.cn` | Verfügbar |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Verfügbar |
| Azure Government |  | Nicht verfügbar. |
| Azure Deutschland |   | Nicht verfügbar. |
| Azure China 21Vianet |  | Nicht verfügbar. |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Verfügbar |
| Azure Government | `https://database.usgovcloudapi.net/` | Verfügbar |
| Azure Deutschland | `https://database.cloudapi.de/` | Verfügbar |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Verfügbar |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Vorschau |
| Azure Government |  | Nicht verfügbar. |
| Azure Deutschland |   | Nicht verfügbar. |
| Azure China 21Vianet |  | Nicht verfügbar. |

### <a name="azure-service-bus"></a>Azure-Servicebus

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Vorschau |
| Azure Government |  | Nicht verfügbar. |
| Azure Deutschland |   | Nicht verfügbar. |
| Azure China 21Vianet |  | Nicht verfügbar. |

### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-Blobs und -Warteschlangen

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` | Verfügbar |
| Azure Government | `https://storage.azure.com/` | Verfügbar |
| Azure Deutschland | `https://storage.azure.com/` | Verfügbar |
| Azure China 21Vianet | `https://storage.azure.com/` | Verfügbar |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | Ressourcen-ID | Status |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Verfügbar |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Verfügbar |
| Azure Deutschland | `https://*.asazure.cloudapi.de` | Verfügbar |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Verfügbar |