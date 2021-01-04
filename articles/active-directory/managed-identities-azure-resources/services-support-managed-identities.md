---
title: Azure-Dienste, die verwaltete Identitäten unterstützen – Azure AD
description: Liste der Dienste, die verwaltete Identitäten für Azure-Ressourcen und die Azure AD-Authentifizierung unterstützen
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 10/07/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 2da0407f848b0795414b071b9e082c7ed60a633d
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861883"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Mit einer verwalteten Identität können Sie sich bei jedem Dienst authentifizieren, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Verwaltete Identitäten für Azure-Ressourcen und die Azure AD-Authentifizierung werden derzeit in Azure integriert. Sie sollten sich regelmäßig über Aktualisierungen informieren.

> [!NOTE]
> „Verwaltete Identitäten für Azure-Ressourcen“ ist der neue Name für den Dienst, der früher als „Verwaltete Dienstidentität“ (Managed Service Identity, MSI) bezeichnet wurde.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen

Die folgenden Azure-Dienste unterstützen verwaltete Identitäten für Azure-Ressourcen:


### <a name="azure-api-management"></a>Azure API Management

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | Vorschau | Vorschau | Nicht verfügbar | Vorschau |

Konfigurieren Sie die verwaltete Identität für Azure API Management anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure Resource Manager-Vorlage](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar. | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | ![Verfügbar][check] | ![Verfügbar][check]  | Nicht verfügbar.  | ![Verfügbar][check] |

Konfigurieren Sie die verwaltete Identität für Azure App Configuration anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Befehlszeilenschnittstelle](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | ![Verfügbar][check] | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | ![Verfügbar][check] | ![Verfügbar][check]  | ![Verfügbar][check]  | ![Verfügbar][check] |

Konfigurieren Sie die verwaltete Identität für Azure App Service anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure-Befehlszeilenschnittstelle](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-Vorlage](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes mit Azure Arc-Aktivierung

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Kubernetes mit Azure Arc-Aktivierung [unterstützt derzeit systemseitig zugewiesene Identitäten](../../azure-arc/kubernetes/connect-cluster.md#azure-arc-agents-for-kubernetes). Das Zertifikat der Identität für den verwalteten Dienst wird von allen Kubernetes-Agents mit Azure Arc-Aktivierung zur Kommunikation mit Azure verwendet.

### <a name="azure-automanage"></a>Automatische Azure-Verwaltung

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Im folgenden Dokument finden Sie Informationen zum erneuten Konfigurieren einer verwalteten Identität, wenn Sie Ihr Abonnement zu einem neuen Mandanten verschoben haben:
* [Reparieren eines fehlerhaften Automanage-Kontos](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprint

|Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | Nicht verfügbar |

Verwenden Sie die folgende Liste, um eine verwaltete Identität mit [Azure Blueprints](../../governance/blueprints/overview.md) zu nutzen:

- [Azure-Portal: Blaupausenzuweisung](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST-API: Blaupausenzuweisung](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |


### <a name="azure-container-instances"></a>Azure Container Instances

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | Linux: Vorschau<br>Windows: Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Linux: Vorschau<br>Windows: Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Container Instances anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Befehlszeilenschnittstelle](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-Vorlage](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Container Registry Tasks anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure-Befehlszeilenschnittstelle](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure-Daten-Explorer

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Data Factory V2 anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)



### <a name="azure-event-grid"></a>Azure Event Grid

Typ der verwalteten Identität |Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | Vorschau | Vorschau | Nicht verfügbar | Vorschau |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar  | Nicht verfügbar  | Nicht verfügbar |









### <a name="azure-functions"></a>Azure-Funktionen

Typ der verwalteten Identität |Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | ![Verfügbar][check] | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | ![Verfügbar][check] | ![Verfügbar][check]  | ![Verfügbar][check]  | ![Verfügbar][check]  |

Konfigurieren Sie die verwaltete Identität für Azure Functions anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure-Befehlszeilenschnittstelle](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-Vorlage](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Data Factory V2 anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar in der Region, in der auch der Azure Import/Export-Dienst verfügbar ist | Vorschau | Verfügbar | Verfügbar |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |


Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten in Azure Kubernetes Service](../../aks/use-managed-identity.md).


### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Nicht verfügbar | ![Verfügbar][check] |


Konfigurieren Sie die verwaltete Identität für Azure Logic Apps anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-Vorlage](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | Vorschau | Nicht verfügbar. | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Vorschau | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten mit Azure Machine Learning](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Azure Policy

|Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | ![Verfügbar][check] | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

Konfigurieren Sie die verwaltete Identität für Azure Policy anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/policy/assignment#az-policy-assignment-create)
- [Azure-Ressourcen-Manager-Vorlagen](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Verwaltete Identität für Service Fabric-Anwendungen](../../service-fabric/concepts-managed-identity.md) ist in allen Regionen verfügbar.

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | Nicht verfügbar. | Nicht verfügbar. | Nicht verfügbar. |
| Vom Benutzer zugewiesen | ![Verfügbar][check] | Nicht verfügbar. | Nicht verfügbar. |Nicht verfügbar. |

Informationen zum Konfigurieren der verwalteten Identität für Azure Service Fabric-Anwendungen in allen Regionen finden Sie in der folgenden Liste:

- [Azure Resource Manager-Vorlage](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | Nicht verfügbar. | Nicht verfügbar. | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | Nicht verfügbar. | Nicht verfügbar. | Nicht verfügbar. | Nicht verfügbar. |


Weitere Informationen finden Sie unter [Aktivieren einer systemseitig zugewiesenen verwalteten Identität für eine Azure Spring Cloud-Anwendung](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Vom System zugewiesen | Verfügbar in der Region, in der auch der Azure Stack Edge-Dienst verfügbar ist | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |
| Vom Benutzer zugewiesen | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar | Nicht verfügbar |

### <a name="azure-virtual-machine-scale-sets"></a>Skalierungsgruppen für virtuelle Azure-Computer

|Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Vorschau | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Vorschau | ![Verfügbar][check] |

Konfigurieren Sie die verwaltete Identität für Azure Virtual Machine Scale Sets anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md)
- [Azure-Ressourcen-Manager-Vorlagen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Vorschau | ![Verfügbar][check] |
| Vom Benutzer zugewiesen | ![Verfügbar][check] | ![Verfügbar][check] | Vorschau | ![Verfügbar][check] |

Konfigurieren Sie die verwaltete Identität für Azure Virtual Machines anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md)
- [Azure-Ressourcen-Manager-Vorlagen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure SDKs](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM Image Builder

| Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | Nicht verfügbar. | Nicht verfügbar. | Nicht verfügbar. | Nicht verfügbar. |
| Vom Benutzer zugewiesen | [Verfügbar in unterstützten Regionen](../../virtual-machines/windows/image-builder-overview.md#regions) | Nicht verfügbar. | Nicht verfügbar. | Nicht verfügbar. |

Informationen zum Konfigurieren der verwalteten Identität für Azure VM Image Builder (in Regionen, in denen sie verfügbar ist) finden Sie in der [Übersicht über Image Builder](../../virtual-machines/windows/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Azure SignalR Service

Typ der verwalteten Identität | Allgemein verfügbar<br>Globale Azure-Regionen | Azure Government | Azure Deutschland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Vom System zugewiesen | Vorschau | Vorschau | Nicht verfügbar | Vorschau |
| Vom Benutzer zugewiesen | Vorschau | Vorschau | Nicht verfügbar | Vorschau |

Konfigurieren Sie die verwaltete Identität für Azure SignalR Service anhand der folgenden Liste (in Regionen, in denen sie verfügbar ist):

- [Azure Resource Manager-Vorlage](../../azure-signalr/howto-use-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-Dienste, die die Azure AD-Authentifizierung unterstützen

Die folgenden Dienste unterstützen die Azure AD-Authentifizierung und wurden mit Clientdiensten getestet, die verwaltete Identitäten für Azure-Ressourcen verwenden.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Konfigurieren Sie den Zugriff auf Azure Resource Manager anhand der folgenden Liste:

- [Zuweisen des Zugriffs im Azure-Portal](howto-assign-access-portal.md)
- [Zuweisen des Zugriffs mithilfe von PowerShell](howto-assign-access-powershell.md)
- [Zuweisen des Zugriffs mithilfe der Azure CLI](howto-assign-access-CLI.md)
- [Zuweisen des Zugriffs mit Azure Resource Manager-Vorlagen](../../role-based-access-control/role-assignments-template.md)

| Cloud | Ressourcen-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Verfügbar][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Verfügbar][check] |
| Azure Deutschland | `https://management.microsoftazure.de/` | ![Verfügbar][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Verfügbar][check] |

### <a name="azure-key-vault"></a>Azure-Schlüsseltresor

| Cloud | Ressourcen-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Verfügbar][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Verfügbar][check] |
| Azure Deutschland |  `https://vault.microsoftazure.de` | ![Verfügbar][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Verfügbar][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Cloud | Ressourcen-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Verfügbar][check] |
| Azure Government |  | Nicht verfügbar. |
| Azure Deutschland |   | Nicht verfügbar. |
| Azure China 21Vianet |  | Nicht verfügbar. |

### <a name="azure-sql"></a>Azure SQL

| Cloud | Ressourcen-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Verfügbar][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Verfügbar][check] |
| Azure Deutschland | `https://database.cloudapi.de/` | ![Verfügbar][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Verfügbar][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | Ressourcen-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Verfügbar][check] |
| Azure Government |  | Nicht verfügbar. |
| Azure Deutschland |   | Nicht verfügbar. |
| Azure China 21Vianet |  | Nicht verfügbar. |

### <a name="azure-service-bus"></a>Azure-Servicebus

| Cloud | Ressourcen-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Verfügbar][check] |
| Azure Government |  | ![Verfügbar][check] |
| Azure Deutschland |   | Nicht verfügbar. |
| Azure China 21Vianet |  | Nicht verfügbar. |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-Blobs und -Warteschlangen

| Cloud | Ressourcen-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Verfügbar][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Verfügbar][check] |
| Azure Deutschland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Verfügbar][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Verfügbar][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | Ressourcen-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Verfügbar][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Verfügbar][check] |
| Azure Deutschland | `https://*.asazure.cloudapi.de` | ![Verfügbar][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Verfügbar][check] |

> [!Note]
> Microsoft Power BI bietet auch [Unterstützung für verwaltete Identitäten](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Verfügbar"
