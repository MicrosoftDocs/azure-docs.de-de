---
title: Unterstützung für das regionenübergreifende Verschieben von Azure-Ressourcen
description: Hier werden die Azure-Ressourcentypen aufgelistet, die in Azure-Regionen verschoben werden können.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 18d4d84462d528b718d784ff6a16ecf990ed0d20
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094014"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Unterstützung für das regionenübergreifende Verschieben von Azure-Ressourcen

In diesem Artikel wird erläutert, ob ein Azure-Ressourcentyp für das Verschieben in eine andere Azure-Region unterstützt wird. 

Navigieren Sie direkt zu einem Ressourcenanbieter-Namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | domainservices | Nein | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | diagnosticsettings | Nein |
> | diagnosticsettingscategories | Nein |
> | privatelinkforazuread | Nein |
> | tenants |  Nein |

## <a name="microsoftaddons"></a>microsoft.Addons

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | supportproviders | Nein |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | aadsupportcases | Nein |
> | addsservices | Nein | 
> | agents | Nein | 
> | anonymousapiusers | Nein |
> | Konfiguration | Nein | 
> | logs | Nein | 
> | reports | Nein | 
> | servicehealthmetrics | Nein | 
> | services | Nein | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | Konfigurationen | Nein | 
> | generaterecommendations | Nein |
> | metadata | Nein |
> | empfehlungen | Nein |
> | suppressions | Nein | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | actionRules | Nein | 
> | alerts | Nein | 
> | alertslist | Nein | 
> | alertsmetadata | Nein | 
> | alertssummary | Nein | 
> | alertssummarylist | Nein | 
> | smartdetectoralertrules | Nein | 
> | smartgroups | Nein | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | servers | Nein |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | reportfeedback | Nein |
> | Dienst |  Ja (Vorlage verwenden) <br/><br/> [Regionsübergreifendes Verschieben von API Management](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | configurationstores | Nein | 
> | configurationstores/eventgridfilters | Nein |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | spring | Nein | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | apiapps | Ja (Vorlage verwenden)<br/><br/> [Verschieben einer App Service-Apps in eine andere Region](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Nein | 
> | gateways | Nein | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | attestationproviders | Nein | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | classicadministrators | Nein | 
> | dataaliases | Nein | 
> | denyassignments | Nein | 
> | elevateaccess | Nein | 
> | findorphanroleassignments | Nein | 
> | locks | Nein | 
> | Berechtigungen | Nein | 
> | policyassignments | Nein | 
> | policydefinitions | Nein | 
> | policysetdefinitions | Nein | 
> | privatelinkassociations | Nein | 
> | resourcemanagementprivatelinks | Nein | 
> | roleassignments | Nein | 
> | roleassignmentsusagemetrics | Nein | 
> | roledefinitions | Nein | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | automationaccounts | Ja (Vorlage verwenden) <br/><br/> [Verwenden von Georeplikation](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configurations | Nein | 
> | automationaccounts/runbooks | Nein | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | Subscription |
> | ------------- | ----------- | 
> | privateclouds | Nein | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | b2cdirectories | Nein | 
> | b2ctenants | Nein | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | datacontrollers | Nein | 
> | hybriddatamanagers | Nein | 
> | postgresinstances | Nein | 
> | sqlinstances | Nein | 
> | sqlmanagedinstances | Nein |
> | sqlserverinstances | Nein | 
> | sqlserverregistrations | Nein |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Nein |
> | registrations | Nein | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | clusters | Nein | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | batchaccounts |  Batch-Konten können nicht direkt aus einer Region in eine andere verschoben werden, Sie können jedoch eine Vorlage verwenden, um eine Vorlage zu exportieren, sie zu ändern und dann die Vorlage in der neuen Region bereitzustellen. <br/><br/> Informationen zum [Verschieben eines Batch-Kontos zwischen Regionen](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | billingaccounts | Nein | 
> | billingperiods | Nein | 
> | billingpermissions | Nein | 
> | billingproperty | Nein | 
> | billingroleassignments | Nein | 
> | billingroledefinitions | Nein | 
> | departments | Nein | 
> | enrollmentaccounts | Nein | 
> | invoices | Nein | 
> | transfers | Nein | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | mapapis | Nein | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | biztalk | Nein | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | blockchainmembers | Nein <br/><br/> Das Blockchain-Netzwerk kann keine Knoten in unterschiedlichen Regionen haben. 
> | cordamembers | Nein |
> | watchers | Nein | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | tokenservices | Nein |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | blueprintassignments | Nein | 
> | blueprints | Nein |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | botservices | Nein | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | redis | Nein | 
> | redisenterprise | Nein | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | appliedreservations | Nein | 
> | calculateexchange | Nein | 
> | calculateprice | Nein | 
> | calculatepurchaseprice | Nein | 
> | catalogs | Nein | 
> | commercialreservationorders | Nein | 
> | Börse | Nein |
> | reservationorders | Nein | 
> | reservations | Nein | 
> | ressourcen | Nein | 
> | validatereservationorder | Nein | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nein |
> | edgenodes | Nein
> | profiles | Nein | 
> | profiles/endpoints | Nein | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | certificateorders | Nein | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | capabilities | Nein | 
> | domainnames | Ja | Nein |
> | quotas | Nein | 
> | resourcetypes | Nein |
> | validatesubscriptionmoveavailability | Nein | 
> | virtualmachines | Nein 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Nein | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | capabilities | Nein | 
> | expressroutecrossconnections | Nein | 
> | expressroutecrossconnections/peerings | Nein | 
> | gatewaysupporteddevices | Nein | 
> | networksecuritygroups | Nein |
> | quotas | Nein |
> | reservedips | Nein | 
> | virtualnetworks | Nein | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | disks | Nein | 
> | images | Nein | 
> | osimages | Nein | 
> | osplatformimages | Nein | 
> | publicimages | Nein | 
> | quotas | Nein | 
> | storageaccounts | Ja |  
> | vmimages | Nein |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | Vorgänge | Nein | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 
> | Cognitive Search | Unterstützt mit manuellen Schritten.<br/><br/> Informationen zum [Verschieben Ihres Azure Cognitive Search-Diensts in eine andere Region](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | ratecard | Nein | 
> | usageaggregates | Nein | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | availabilitysets | Ja <br/><br/> Verschieben Sie mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) Verfügbarkeitsgruppen. | 
> | diskaccesses | Nein |
> | diskencryptionsets | Nein | 
> | disks | Ja <br/><br/> Verschieben Sie mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) Azure-VMs und zugehörige Datenträger. | 
> | galleries | Nein | 
> | galleries/images | Nein | 
> | galleries/images/versions | Nein | 
> | hostgroups | Nein | 
> | hostgroups/hosts | Nein | 
> | images | Nein | 
> | proximityplacementgroups | Nein | 
> | restorepointcollections | Nein | 
> | sharedvmimages | Nein | 
> | sharedvmimages/versions | Nein | 
> | snapshots | Nein | 
> | sshpublickeys | Nein |
> | virtualmachines | Ja <br/><br/> Verschieben Sie mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) Azure-VMs. | 
> | virtualmachines/extensions | Nein | 
> | virtualmachinescalesets | Nein | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | aggregatedcost | Nein | 
> | balances | Nein | 
> | budgets | Nein | 
> | charges | Nein | 
> | costtags | Nein | 
> | credits | Nein | 
> | events | Nein | 
> | forecasts | Nein | 
> | lots | Nein | 
> | marketplaces | Nein | 
> | pricesheets | Nein | 
> | products | Nein | 
> | reservationdetails | Nein | 
> | reservationrecommendationdetails | Nein | 
> | reservationrecommendations | Nein | 
> | reservationsummaries | Nein | 
> | reservationtransactions | Nein | 
> | tags | Nein | 
> | tenants | Nein | 
> | terms | Nein | 
> | usagedetails | Nein | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | containergroups | Nein | 
> | serviceassociationlinks | Nein |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | registries | Nein |  
> | registries/agentpools | Nein | 
> | registries/buildtasks | Nein |  
> | registries/replications | Nein | 
> | registries/tasks | Nein |  
> | registries/webhooks | Nein | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | containerservices | Nein |
> | managedclusters | Nein | 
> | openshiftmanagedclusters | Nein | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | applications | Nein | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | alerts | Nein | 
> | billingaccounts | Nein | 
> | budgets | Nein | 
> | cloudconnectors | Nein | 
> | connectors | Nein | 
> | departments | Nein | 
> | dimensions | Nein | 
> | enrollmentaccounts | Nein | 
> | exports | Nein | 
> | externalbillingaccounts | Nein | 
> | forecast | Nein | 
> | Abfrage | Nein | 
> | Registrieren | Nein | 
> | reportconfigs | Nein | 
> | reports | Nein | 
> | settings | Nein | 
> | showbackrules | Nein | 
> | views | Nein | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | hubs | Nein |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | requests | Nein | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | associations | Nein |
> | resourceproviders | Nein | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | jobs | Nein | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | availableskus | Nein |
> | databoxedgedevices | Nein | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | workspaces | Nein | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | catalogs | Nein | 
> | datacatalogs | Nein | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | connectionmanagers | Nein | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | packages | Nein | 
> | plans | Nein | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | datafactories | Nein | 
> | factories | Nein |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | datalakeaccounts | Nein | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | services | Nein | 
> | services/projects | Nein | 
> | slots | Nein | 

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Nein | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | servers | Zum Verschieben eines vorhandenen Servers können Sie ein regionsübergreifendes Lesereplikat verwenden. [Weitere Informationen](../../postgresql/howto-move-regions-portal.md)<br/><br/> Wenn der Dienst mit georedundantem Sicherungsspeicher bereitgestellt wird, können Sie die Geowiederherstellung für die Wiederherstellung in anderen Regionen verwenden. [Weitere Informationen](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | servers | Zum Verschieben eines vorhandenen Servers können Sie ein regionsübergreifendes Lesereplikat verwenden. [Weitere Informationen](../../mysql/howto-move-regions-portal.md)

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | servergroups | Nein | 
> | servers | Zum Verschieben eines vorhandenen Servers können Sie ein regionsübergreifendes Lesereplikat verwenden. [Weitere Informationen](../../postgresql/howto-move-regions-portal.md)
> | serversv2 | Nein | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | artifactsources | Nein | 
> | rollouts | Nein |  
> | servicetopologies | Nein | 
> | servicetopologies/services | Nein |  
> | servicetopologies/services/serviceunits | Nein | 
> | steps | Nein | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | applicationgroups | Nein | 
> | workspaces | Nein | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | elasticpools | Nein. Die Ressource wird nicht verfügbar gemacht.
> | elasticpools/iothubtenants | Nein. Die Ressource wird nicht verfügbar gemacht.
> | iothubs | Ja. [Weitere Informationen](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Nein | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | Controller | Nein | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | Controller | Nein | 
> | AKS-Cluster | Nein<br/><br/> [Weitere Informationen](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) zum Verschieben in eine andere Region.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | labcenters | Nein | 
> | labs | Nein | 
> | labs/environments | Nein |  
> | labs/servicerunners | Nein | 
> | labs/virtualmachines | Nein |  
> | schedules | Nein |  

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Ja, durch Neuerstellen von Ressourcen in einer neuen Region. [Weitere Informationen](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | databaseaccounts | Nein | 
> | databaseaccounts | Nein | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | domains | Nein | 
> | generatessorequest | Nein | 
> | topleveldomains | Nein | 
> | validatedomainregistrationinformation | Nein |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | services | Nein |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | domains | Nein | 
> | eventsubscriptions | Nein |
> | extensiontopics | Nein | 
> | partnernamespaces | Nein | 
> | partnerregistrations | Nein | 
> | partnertopics | Nein | 
> | systemtopics | Nein | 
> | topics | Nein | 
> | topictypes | Nein | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | clusters | Nein |  
> | Namespaces | Ja (mit Vorlage)<br/><br/> [Verschieben eines Event Hub-Namespace in eine andere Region](../../event-hubs/move-across-regions.md) | 
> | sku | Nein |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | experimentworkspaces | Nein | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | Namespaces | Nein | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | featureproviders | Nein | 
> | Features | Nein | 
> | providers | Nein | 
> | subscriptionfeatureregistrations | Nein | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | automanagedaccounts | Nein | 
> | automanagedvmconfigurationprofiles | Nein | 
> | guestconfigurationassignments | Nein | 
> | software | Nein | 
> | softwareupdateprofile | Nein | 
> | softwareupdates | Nein | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | hanainstances | Nein | 
> | sapmonitors | Nein |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | dedicatedhsms | Nein | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | clusters | Nein | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | services | Nein |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | machines | Nein | 
> | machines/extensions | Nein |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | datamanagers |  Nein | 

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | devices | Nein | 
> | vnfs | Nein | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | components | Nein | 
> | networkscopes | Nein | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | jobs |  Nein | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein. [Weitere Informationen](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region)
> | actiongroups |  Nein | 
> | activitylogalerts | Nein | 
> | alertrules |  Nein | 
> | autoscalesettings |  Nein | 
> | baseline | Nein |
> | components |  Nein |  
> | datacollectionrules | Nein | 
> | diagnosticsettings | Nein | 
> | diagnosticsettingscategories | Nein | 
> | eventcategories | Nein | 
> | eventtypes | Nein | 
> | extendeddiagnosticsettings | Nein | |
> | guestdiagnosticsettings | Nein | 
> | listmigrationdate | Nein | 
> | logdefinitions | Nein | 
> | logprofiles | Nein | 
> | logs | Nein | Nein |
> | metricalerts | Nein | 
> | metricbaselines | Nein | 
> | metricbatch | Nein | 
> | metricdefinitions | Nein | 
> | metricnamespaces | Nein | 
> | metrics | Nein | 
> | migratealertrules | Nein |
> | migratetonewpricingmodel | Nein | 
> | myworkbooks | Nein |
> | notificationgroups | Nein | 
> | privatelinkscopes | Nein |
> | rollbacktolegacypricingmodel | Nein |
> | scheduledqueryrules |  Nein | 
> | Topologie | Nein |
> | Transaktionen | Nein |
> | vminsightsonboardingstatuses | Nein |
> | webtests |  Nein | 
> | webtests/gettestresultfile | Nein |
> | workbooks |  Nein |  
> | workbooktemplates | Nein |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | apptemplates | Nein | 
> | iotapps | Nein | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> |  iothub |  Ja (Hub klonen) <br/><br/> [Klonen eines IoT-Hubs in eine andere Region](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung |
> | ------------- | ----------- | 
> | graph | Nein | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | deletedvaults | Nein |
> | hsmpools | Nein | 
> | managedhsms | Nein |
> | vaults |  Nein | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | connectedclusters | Nein | 
> | registeredsubscriptions | Nein | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | Nein | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | clusters |  Nein |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | labaccounts | Nein | 
> | users | Nein | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein, dies ist ein globaler Dienst.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | hostingenvironments | Nein | 
> | integrationaccounts |  Nein |  
> | integrationserviceenvironments | Nein | 
> | integrationserviceenvironments/managedapis | Nein |
> | isolatedenvironments | Nein | 
> | workflows |  Nein |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | commitmentplans |  Nein | 
> | webservices |  Nein | 
> | workspaces |  Nein | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | operationalizationclusters |  Nein | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 
> | teamaccounts | Nein | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | workspaces | Nein | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | configurationassignments | Ja. [Weitere Informationen](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Ja. [Weitere Informationen](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | Nein | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | Identitäten | Nein | 
> | userassignedidentities | Nein | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | managednetworks | Nein | 
> | managednetworks/managednetworkgroups | Nein |
> | managednetworks/managednetworkpeeringpolicies | Nein | 
> | Benachrichtigung | Nein | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Nein | 
> | registrationassignments | Nein |
> | registrationdefinitions | Nein | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | getentities | Nein | 
> | managementgroups | Nein | 
> | managementgroups/settings | Nein | 
> | ressourcen | Nein | 
> | starttenantbackfill | Nein | 
> | tenantbackfillstatus | Nein | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts |  Nein, Azure Maps ist ein räumlicher Dienst. 
> | accounts/privateatlases | Nein

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | offers | Nein | 
> | offertypes | Nein | 
> | privategalleryitems | Nein | 
> | privatestoreclient | Nein | 
> | privatestores | Nein | 
> | products | Nein | 
> | publishers | Nein | 
> | Registrieren | Nein | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | classicdevservices | Nein | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | agreements | Nein | 
> | offertypes | Nein | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | mediaservices |  Nein | 
> | mediaservices/liveevents |  Nein | 
> | mediaservices/streamingendpoints |  Nein | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | appclusters | Nein | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | assessmentprojects | Nein | 
> | migrateprojects | Nein | 
> | movecollections | Nein
> | projects | Nein | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nein | 
> | objectunderstandingaccounts | Nein | 
> | remoterenderingaccounts | Nein | 
> | spatialanchorsaccounts | Nein | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | netappaccounts | Nein | 
> | netappaccounts/capacitypools | Nein | 
> | netappaccounts/capacitypools/volumes | Nein | 
> | netappaccounts/capacitypools/volumes/mounttargets | Nein | 
> | netappaccounts/capacitypools/volumes/snapshots | Nein | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | applicationgateways | Nein |
> | applicationgatewaywebapplicationfirewallpolicies | Nein | 
> | applicationsecuritygroups |  Nein |  
> | azurefirewalls |  Nein |  
> | bastionhosts | Nein | 
> | bgpservicecommunities | Nein |
> | connections |  Nein | 
> | ddoscustompolicies |  Nein | 
> | ddosprotectionplans | Nein | 
> | dnszones |  Nein | 
> | expressroutecircuits | Nein | 
> | expressroutegateways | Nein | 
> | expressrouteserviceproviders | Nein | 
> | firewallpolicies | Nein |
> | frontdoors | Nein | 
> | ipallocations | Nein |
> | ipgroups | Nein |
> | loadbalancers | Ja <br/><br/> Verschieben Sie in- und externe Lastenausgleichsmodule mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md). |
> | localnetworkgateways |  Nein | 
> | natgateways |  Nein | 
> | networkexperimentprofiles | Nein |
> | networkintentpolicies |  Nein | 
> | networkinterfaces | Ja <br/><br/> Verschieben Sie NICs mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md). | 
> | networkprofiles | Nein | 
> | networksecuritygroups | Ja <br/><br/> Verschieben Sie Netzwerksicherheitsgruppen (NSGs) mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md). | 
> | networkwatchers |  Nein |  
> | networkwatchers/connectionmonitors |  Nein | 
> | networkwatchers/flowlogs |  Nein | 
> | networkwatchers/pingmeshes |  Nein | 
> | p2svpngateways | Nein | 
> | privatednszones |  Nein |  
> | privatednszones/virtualnetworklinks | Nein |> | privatednszonesinternal | Nein |
> | privateendpointredirectmaps | Nein |
> | privateendpoints | Nein | 
> | privatelinkservices | Nein | 
> | publicipaddresses | Ja<br/><br/> Verschieben Sie öffentliche IP-Adressen mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md). |
> | publicipprefixes | Nein | 
> | routefilters | Nein | 
> | routetables |  Nein | 
> | securitypartnerproviders | Nein |
> | serviceendpointpolicies |  Nein | 
> | trafficmanagergeographichierarchies | Nein | 
> | trafficmanagerprofiles |  Nein | 
> | trafficmanagerusermetricskeys | Nein |
> | virtualhubs | Nein | 
> | virtualnetworkgateways |  Nein |  
> | virtualnetworks |  Nein | 
> | virtualnetworktaps | Nein | 
> | virtualwans | Nein | 
> | vpngateways (Virtual WAN) | Nein | 
> | vpnsites (Virtual WAN) | Nein | 
> | vpnsites (Virtual WAN) | Nein |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | Namespaces |  Nein | 
> | namespaces/notificationhubs |  Nein |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | osnamespaces | Nein | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | hypervsites | Nein | 
> | importsites | Nein | 
> | serversites | Nein | 
> | vmwaresites | Nein | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | clusters | Nein | 
> | deletedworkspaces | Nein | 
> | linktargets | Nein | 
> | storageinsightconfigs | Nein |
> | workspaces | Nein |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | managementassociations | Nein |
> | managementconfigurations |  Nein | 
> | solutions | Nein |
> | views |  Nein | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | legacypeerings | Nein | 
> | peerasns | Nein | 
> | peeringlocations | Nein | 
> | peerings | Nein | 
> | peeringservicecountries | Nein | 
> | peeringservicelocations | Nein | 
> | peeringserviceproviders | Nein | 
> | peeringservices | Nein | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | policyevents | Nein | 
> | policystates | Nein | 
> | policytrackedresources | Nein | 
> | remediations | Nein | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | consoles | Nein |
> | dashboards | Nein | 
> | usersettings | Nein | 


## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | workspacecollections |  Nein | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | capacities |  Nein | 

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | accounts | Nein | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | availableaccounts | Nein | 
> | providerregistrations | Nein | 
> | rollouts | Nein | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | workspaces | Nein | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | replicationeligibilityresults | Nein |
> | vaults | Nein.<br/><br/> Das Verschieben von Recovery Services-Tresoren für Azure Backup zwischen Azure-Regionen wird nicht unterstützt.<br/><br/> In Recovery Services-Tresoren für Azure Site Recovery können Sie [den Tresor deaktivieren und in der Zielregion neu erstellen](../../site-recovery/move-vaults-across-regions.md). | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | openshiftclusters | Nein | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | Namespaces |  Nein | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | Abfragen |  Nein |  
> | resourcechangedetails | Nein | 
> | resourcechanges | Nein | 
> | ressourcen | Nein | 
> | resourceshistory | Nein | 
> | subscriptionsstatus | Nein | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | childresources | Nein | 
> | emergingissues | Nein | 
> | events | Nein | 
> | metadata | Nein | 
> | Benachrichtigungen | Nein | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung |
> | ------------- | ----------- |
> | deploymentScripts |  Ja<br/><br/>[Verschieben von Ressourcen unter Microsoft.Resources in eine neue Region](microsoft-resources-move-regions.md) |
> | templateSpecs |  Ja<br/><br/>[Verschieben von Ressourcen unter Microsoft.Resources in eine neue Region](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | applications |  Nein | 
> | saasresources | Nein | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Nein |
> | searchservices |  Nein | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | Nein | 
> | advancedthreatprotectionsettings | Nein | 
> | alerts | Nein | 
> | allowedconnections | Nein | 
> | applicationwhitelistings | Nein | 
> | assessmentmetadata | Nein | 
> | assessments | Nein | 
> | autodismissalertsrules | Nein | 
> | automations | Nein | 
> | autoprovisioningsettings | Nein |
> | complianceresults | Nein | 
> | compliances | Nein | 
> | datacollectionagents | Nein | 
> | devicesecuritygroups | Nein | 
> | discoveredsecuritysolutions | Nein | 
> | externalsecuritysolutions | Nein | 
> | informationprotectionpolicies | Nein | 
> | iotsecuritysolutions | Nein | 
> | iotsecuritysolutions/analyticsmodels | Nein | 
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | Nein | 
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | Nein | 
> | jitnetworkaccesspolicies | Nein | 
> | Richtlinien | Nein | 
> | pricings | Nein | 
> | regulatorycompliancestandards | Nein | 
> | regulatorycompliancestandards/regulatorycompliancecontrols | Nein | 
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | Nein | 
> | securitycontacts | Nein | 
> | securitysolutions | Nein | 
> | securitysolutionsreferencedata | Nein | 
> | securitystatuses | Nein | 
> | securitystatusessummaries | Nein | 
> | servervulnerabilityassessments | Nein | 
> | settings | Nein | 
> | subassessments | Nein |
> | Tasks | Nein | 
> | topologies | Nein | 
> | workspacesettings | Nein | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | aggregations | Nein | 
> | alertrules | Nein | 
> | alertruletemplates | Nein | 
> | automationrules | Nein |
> | cases | Nein | 
> | dataconnectors | Nein | 
> | entities | Nein | 
> | entityqueries | Nein |
> | incidents | Nein | 
> | officeconsents | Nein | 
> | settings | Nein | 
> | threatintelligence | Nein | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | consoleservices | Nein | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | gateways | Nein | 
> | nodes | Nein | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | Namespaces |  Nein | 
> | premiummessagingregions | Nein | 
> | sku | Nein | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | applications | Nein | 
> | clusters |  Nein |  
> | containergroups | Nein | 
> | containergroupsets | Nein | 
> | edgeclusters | Nein | 
> | managedclusters | Nein |
> | networks | Nein | 
> | secretstores | Nein | 
> | volumes | Nein | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | applications |  Nein | 
> | containergroups | Nein | 
> | gateways |  Nein | 
> | networks |  Nein | 
> | secrets |  Nein | 
> | volumes |  Nein |  

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | rollouts | Nein | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | signalr |  Nein |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | hybridusebenefits | Nein | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | appliancedefinitions | Nein | 
> | appliances | Nein | 
> | jitrequests | Nein | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | instancepools | Nein | 
> | locations | Nein |
> | managedinstances | Ja <br/><br/> [Erfahren Sie mehr](../../azure-sql/database/move-resources-across-regions.md) über das regionsübergreifende Verschieben verwalteter Instanzen. | 
> | managedinstances/databases | Ja | 
> | servers | Ja | 
> | servers/databases | Ja <br/><br/> [Erfahren Sie mehr](../../azure-sql/database/move-resources-across-regions.md) über das regionsübergreifende Verschieben von Datenbanken.<br/><br/> [Erfahren Sie mehr](../../resource-mover/tutorial-move-region-sql.md) über das Verschieben von Azure SQL-Datenbank-Instanzen mit Azure Resource Mover.  | 
> | servers/elasticpools | Ja <br/><br/> [Erfahren Sie mehr](../../azure-sql/database/move-resources-across-regions.md) über das regionsübergreifende Verschieben von Pools für elastische Datenbanken.<br/><br/> [Erfahren Sie mehr](../../resource-mover/tutorial-move-region-sql.md) über das Verschieben von Pools für elastische Azure SQL-Datenbank-Instanzen mit Azure Resource Mover.  | 
> | virtualclusters | Ja | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Nein |  
> | sqlvirtualmachines |  Nein |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | storageaccounts | Ja<br/><br/> [Verschieben eines Azure Storage-Kontos in eine andere Region](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | caches | Nein | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | storagesyncservices |  Nein | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | storagesyncservices | Nein | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | storagesyncservices | Nein | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | managers | Nein | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | clusters | Nein |
> | streamingjobs |  Nein |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | environments | Nein | 
> | instances | Nein | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | subscriptions | Nein | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | services | Nein | 
> | supporttickets | Nein | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | workspaces | Nein | 
> | workspaces/bigdatapools | Nein | 
> | workspaces/sqlpools | Nein | 


## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | environments |  Nein | 
> | environments/eventsources |  Nein |  
> | environments/referencedatasets |  Nein | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | stores | Nein | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | imagetemplates | Nein | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | account |  Nein | 
> | account/extension |  Nein | 
> | account/project |  Nein | 

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | arczones | Nein | 
> | resourcepools | Nein | 
> | vcenters | Nein | 
> | virtualmachines | Nein | 
> | virtualmachinetemplates | Nein | 
> | virtualnetworks | Nein | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nein | 
> | dedicatedcloudservices | Nein | 
> | virtualmachines | Nein | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | devices | Nein | 
> | vnfs | Nein | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | accounts | Nein | 
> | plans | Nein | 
> | registeredsubscriptions | Nein |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | availablestacks | Nein | 
> | billingmeters | Nein | 
> | certificates | Nein | 
> | connectiongateways |  Nein |  
> | connections |  Nein |  
> | customapis |  Nein | 
> | deletedsites | Nein | 
> | deploymentlocations | Nein | 
> | georegions | Nein | 
> | hostingenvironments | Nein | 
> | kubeenvironments | Nein | 
> | publishingusers | Nein |
> | empfehlungen | Nein | 
> | resourcehealthmetadata | Nein | 
> | runtimes | Nein | 
> | serverfarms | Nein |  
> | serverfarms/eventgridfilters | N
> | sites |  Nein | 
> | sites/premieraddons |  Nein |  
> | sites/slots |  Nein |  
> | sourcecontrols | Nein |
> | staticsites | Nein | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Nein |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- |
> | deviceservices | Nein | 

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | workloads | Nein | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Regionenverschiebung | 
> | ------------- | ----------- | 
> | components | Nein |
> | componentssummary | Nein | 
> | monitorinstances | Nein | 
> | monitorinstancessummary | Nein | 
> | monitors | Nein | 
## <a name="third-party-services"></a>Dienste von Drittanbietern

Derzeit wird der Verschiebevorgang nicht für Drittanbieterdienste unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Resource Mover-Dienst finden Sie [hier](../../resource-mover/overview.md).

