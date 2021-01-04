---
title: Ressourcen ohne Beschränkung auf 800 Instanzen
description: Aufstellung der Azure-Ressourcentypen, die mehr als 800 Instanzen in einer Ressourcengruppe aufweisen können.
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: cec7ea87bbe81d3bfcca37f3133ac650e66f849f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913499"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Ressourcen ohne Beschränkung auf 800 Instanzen pro Ressourcengruppe

Standardmäßig können Sie bis zu 800 Instanzen eines Ressourcentyps in jeder Ressourcengruppe bereitstellen. Einige Ressourcentypen sind jedoch von der Beschränkung auf 800 Instanzen ausgenommen. In diesem Artikel sind die Azure-Ressourcentypen aufgeführt, die mehr als 800 Instanzen in einer Ressourcengruppe aufweisen können. Alle anderen Ressourcentypen sind auf 800 Instanzen beschränkt.

Für einige Ressourcentypen müssen Sie sich an den Support wenden, damit die Beschränkung auf 800 Instanzen aufgehoben wird. Diese Ressourcentypen sind in diesem Artikel angegeben.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* edgeSubscriptions
* linkedSubscriptions
* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices: Standardmäßig auf 800 Instanzen beschränkt. Dieser Grenzwert kann bei Kontaktaufnahme mit dem Support erhöht werden.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* snapshots
* virtualMachineScaleSets: Standardmäßig auf 800 Instanzen beschränkt Dieser Grenzwert kann bei Kontaktaufnahme mit dem Support erhöht werden.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registries/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* registries/buildTasks/steps/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

* instances

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* serverGroups
* servers
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* labs/virtualMachines: Standardmäßig auf 800 Instanzen beschränkt. Dieser Grenzwert kann bei Kontaktaufnahme mit dem Support erhöht werden.
* schedules

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* Namespaces

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses: Standardmäßig auf 800 Instanzen beschränkt. Dieser Grenzwert kann bei Kontaktaufnahme mit dem Support erhöht werden.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections: Standardmäßig auf 800 Instanzen beschränkt. Dieser Grenzwert kann bei Kontaktaufnahme mit dem Support erhöht werden.

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* capacities: Standardmäßig auf 800 Instanzen beschränkt. Dieser Grenzwert kann bei Kontaktaufnahme mit dem Support erhöht werden.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Namespaces

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Namespaces

## <a name="microsoftsingularity"></a>Microsoft.Singularity

* accounts
* accounts/accountQuotaPolicies
* accounts/groupPolicies
* accounts/jobs
* accounts/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* servers/databases

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Nächste Schritte

Eine vollständige Übersicht der Kontingente und Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](azure-subscription-service-limits.md).
