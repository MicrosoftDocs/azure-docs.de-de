---
title: Unterstützung des Verschiebevorgangs nach Ressourcentyp
description: Eine Liste von Azure-Ressourcentypen, die in eine neue Ressourcengruppe oder ein neues Abonnement verschoben werden können.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 0b1304b273523d42ad62f4e392fb803e1c174b69
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937524"
---
# <a name="move-operation-support-for-resources"></a>Unterstützung des Verschiebevorgangs für Ressourcen

In diesem Artikel wird aufgeführt, für welche Azure-Ressourcentypen der Verschiebevorgang unterstützt wird. Außerdem finden Sie hier Informationen zu speziellen Bedingungen, die beim Verschieben einer Ressource berücksichtigt werden müssen.

> [!IMPORTANT]
> In den meisten Fällen kann eine untergeordnete Ressource nicht unabhängig von der übergeordneten Ressource verschoben werden. Der Ressourcentyp von untergeordneten Ressourcen hat das Format `<resource-provider-namespace>/<parent-resource>/<child-resource>`. `Microsoft.ServiceBus/namespaces/queues` ist beispielsweise eine untergeordnete Ressource von `Microsoft.ServiceBus/namespaces`. Wenn Sie die übergeordnete Ressource verschieben, wird automatisch auch die untergeordnete Ressource verschoben. Wenn Sie in diesem Artikel keine untergeordnete Ressource sehen, können Sie davon ausgehen, dass sie zusammen mit der übergeordneten Ressource verschoben wird. Falls die übergeordnete Ressource das Verschieben nicht unterstützt, kann die untergeordnete Ressource nicht verschoben werden.

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
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
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
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Nein | Nein |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Nein | Nein |
> | diagnosticsettingscategories | Nein | Nein |
> | privatelinkforazuread | Ja | Ja |
> | tenants | Ja | Ja |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | supportproviders | Nein | Nein |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Nein | Nein |
> | addsservices | Nein | Nein |
> | agents | Nein | Nein |
> | anonymousapiusers | Nein | Nein |
> | Konfiguration | Nein | Nein |
> | logs | Nein | Nein |
> | reports | Nein | Nein |
> | servicehealthmetrics | Nein | Nein |
> | services | Nein | Nein |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Konfigurationen | Nein | Nein |
> | generaterecommendations | Nein | Nein |
> | metadata | Nein | Nein |
> | empfehlungen | Nein | Nein |
> | suppressions | Nein | Nein |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionRules | Ja | Ja |
> | alerts | Nein | Nein |
> | alertslist | Nein | Nein |
> | alertsmetadata | Nein | Nein |
> | alertssummary | Nein | Nein |
> | alertssummarylist | Nein | Nein |
> | smartdetectoralertrules | Ja | Ja |
> | smartgroups | Nein | Nein |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Ein auf die Verbrauchs-SKU festgelegter API Management-Dienst kann nicht verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | reportfeedback | Nein | Nein |
> | Dienst | Ja | Ja |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ja | Ja |
> | configurationstores/eventgridfilters | Nein | Nein |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | spring | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for App Service resources](./move-limitations/app-service-move-limitations.md) (Anleitung zum Verschieben von App Service-Ressourcen).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Nein | Nein |
> | appidentities | Nein | Nein |
> | gateways | Nein | Nein |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ja | Ja |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicadministrators | Nein | Nein |
> | dataaliases | Nein | Nein |
> | denyassignments | Nein | Nein |
> | elevateaccess | Nein | Nein |
> | findorphanroleassignments | Nein | Nein |
> | locks | Nein | Nein |
> | Berechtigungen | Nein | Nein |
> | policyassignments | Nein | Nein |
> | policydefinitions | Nein | Nein |
> | policysetdefinitions | Nein | Nein |
> | privatelinkassociations | Nein | Nein |
> | resourcemanagementprivatelinks | Nein | Nein |
> | roleassignments | Nein | Nein |
> | roleassignmentsusagemetrics | Nein | Nein |
> | roledefinitions | Nein | Nein |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbooks müssen sich in der gleichen Ressourcengruppe befinden wie das Automation-Konto.
>
> Weitere Informationen finden Sie unter [Verschieben Ihres Azure Automation-Kontos in ein anderes Abonnement](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ja | Ja |
> | automationaccounts/configurations | Ja | Ja |
> | automationaccounts/runbooks | Ja | Ja |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | privateclouds | Ja | Ja |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |
> | b2ctenants | Nein | Nein |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Nein | Nein |
> | hybriddatamanagers | Nein | Nein |
> | postgresinstances | Nein | Nein |
> | sqlinstances | Nein | Nein |
> | sqlmanagedinstances | Nein | Nein |
> | sqlserverinstances | Nein | Nein |
> | sqlserverregistrations | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Nein | Nein |
> | registrations | Ja | Ja |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Nein | Nein |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Nein | Nein |
> | billingperiods | Nein | Nein |
> | billingpermissions | Nein | Nein |
> | billingproperty | Nein | Nein |
> | billingroleassignments | Nein | Nein |
> | billingroledefinitions | Nein | Nein |
> | departments | Nein | Nein |
> | enrollmentaccounts | Nein | Nein |
> | invoices | Nein | Nein |
> | transfers | Nein | Nein |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Nein | Nein |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | Nein | Nein |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nein | Nein |
> | cordamembers | Nein | Nein |
> | watchers | Nein | Nein |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tokenservices | Nein | Nein |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nein | Nein |
> | blueprints | Nein | Nein |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Wenn die Azure Cache for Redis-Instanz mit einem virtuellen Netzwerk konfiguriert ist, kann die Instanz nicht in ein anderes Abonnement verschoben werden. Weitere Informationen finden Sie unter [Einschränkungen beim Verschieben von Netzwerkressourcen](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | Ja | Ja |
> | redisenterprise | Nein | Nein |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Nein | Nein |
> | calculateexchange | Nein | Nein |
> | calculateprice | Nein | Nein |
> | calculatepurchaseprice | Nein | Nein |
> | catalogs | Nein | Nein |
> | commercialreservationorders | Nein | Nein |
> | Börse | Nein | Nein |
> | reservationorders | Nein | Nein |
> | reservations | Nein | Nein |
> | ressourcen | Nein | Nein |
> | validatereservationorder | Nein | Nein |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nein | Nein |
> | cdnwebapplicationfirewallpolicies | Ja | Ja |
> | edgenodes | Nein | Nein |
> | profiles | Ja | Ja |
> | profiles/endpoints | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for App Service resources](./move-limitations/app-service-move-limitations.md) (Anleitung zum Verschieben von App Service-Ressourcen).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ja | Ja |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | Nein | Nein |
> | domainnames | Ja | Nein |
> | quotas | Nein | Nein |
> | resourcetypes | Nein | Nein |
> | validatesubscriptionmoveavailability | Nein | Nein |
> | virtualmachines | Ja | Ja |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Nein | Nein |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | Nein | Nein |
> | expressroutecrossconnections | Nein | Nein |
> | expressroutecrossconnections/peerings | Nein | Nein |
> | gatewaysupporteddevices | Nein | Nein |
> | networksecuritygroups | Nein | Nein |
> | quotas | Nein | Nein |
> | reservedips | Nein | Nein |
> | virtualnetworks | Nein | Nein |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | disks | Nein | Nein |
> | images | Nein | Nein |
> | osimages | Nein | Nein |
> | osplatformimages | Nein | Nein |
> | publicimages | Nein | Nein |
> | quotas | Nein | Nein |
> | storageaccounts | Ja | Nein |
> | vmimages | Nein | Nein |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Vorgänge | Nein | Nein |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ratecard | Nein | Nein |
> | usageaggregates | Nein | Nein |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for virtual machines](./move-limitations/virtual-machines-move-limitations.md) (Anleitung zum Verschieben virtueller Computer).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ja | Ja |
> | diskaccesses | Nein | Nein |
> | diskencryptionsets | Nein | Nein |
> | disks | Ja | Ja |
> | galleries | Nein | Nein |
> | galleries/images | Nein | Nein |
> | galleries/images/versions | Nein | Nein |
> | hostgroups | Nein | Nein |
> | hostgroups/hosts | Nein | Nein |
> | images | Ja | Ja |
> | proximityplacementgroups | Ja | Ja |
> | restorepointcollections | Nein | Nein |
> | restorepointcollections/restorepoints | Nein | Nein |
> | sharedvmextensions | Nein | Nein |
> | sharedvmimages | Nein | Nein |
> | sharedvmimages/versions | Nein | Nein |
> | snapshots | Ja | Ja |
> | sshpublickeys | Nein | Nein |
> | virtualmachines | Ja | Ja |
> | virtualmachines/extensions | Ja | Ja |
> | virtualmachinescalesets | Ja | Ja |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Nein | Nein |
> | balances | Nein | Nein |
> | budgets | Nein | Nein |
> | charges | Nein | Nein |
> | costtags | Nein | Nein |
> | credits | Nein | Nein |
> | events | Nein | Nein |
> | forecasts | Nein | Nein |
> | lots | Nein | Nein |
> | marketplaces | Nein | Nein |
> | pricesheets | Nein | Nein |
> | products | Nein | Nein |
> | reservationdetails | Nein | Nein |
> | reservationrecommendationdetails | Nein | Nein |
> | reservationrecommendations | Nein | Nein |
> | reservationsummaries | Nein | Nein |
> | reservationtransactions | Nein | Nein |
> | tags | Nein | Nein |
> | tenants | Nein | Nein |
> | terms | Nein | Nein |
> | usagedetails | Nein | Nein |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Nein | Nein |
> | serviceassociationlinks | Nein | Nein |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | Ja | Ja |
> | registries/agentpools | Ja | Ja |
> | registries/buildtasks | Ja | Ja |
> | registries/replications | Ja | Ja |
> | registries/tasks | Ja | Ja |
> | registries/webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Nein | Nein |
> | managedclusters | Nein | Nein |
> | openshiftmanagedclusters | Nein | Nein |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | Nein | Nein |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nein | Nein |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alerts | Nein | Nein |
> | billingaccounts | Nein | Nein |
> | budgets | Nein | Nein |
> | cloudconnectors | Nein | Nein |
> | connectors | Ja | Ja |
> | departments | Nein | Nein |
> | dimensions | Nein | Nein |
> | enrollmentaccounts | Nein | Nein |
> | exports | Nein | Nein |
> | externalbillingaccounts | Nein | Nein |
> | forecast | Nein | Nein |
> | Abfrage | Nein | Nein |
> | Registrieren | Nein | Nein |
> | reportconfigs | Nein | Nein |
> | reports | Nein | Nein |
> | settings | Nein | Nein |
> | showbackrules | Nein | Nein |
> | views | Nein | Nein |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | Nein | Nein |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | requests | Nein | Nein |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associations | Nein | Nein |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Nein | Nein |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableskus | Nein | Nein |
> | databoxedgedevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Nein | Nein |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | Ja | Ja |
> | datacatalogs | Nein | Nein |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nein | Nein |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | packages | Nein | Nein |
> | plans | Nein | Nein |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Ja | Ja |
> | factories | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nein | Nein |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Nein | Nein |
> | services/projects | Nein | Nein |
> | slots | Nein | Nein |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nein | Nein |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Ja | Ja |
> | servers | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Ja | Ja |
> | servergroups | Nein | Nein |
> | servers | Ja | Ja |
> | serversv2 | Ja | Ja |
> | singleservers | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ja | Ja |
> | rollouts | Ja | Ja |
> | servicetopologies | Ja | Ja |
> | servicetopologies/services | Ja | Ja |
> | servicetopologies/services/serviceunits | Ja | Ja |
> | steps | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Ja | Ja |
> | hostpools | Ja | Ja |
> | workspaces | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nein | Nein |
> | elasticpools/iothubtenants | Nein | Nein |
> | iothubs | Ja | Ja |
> | provisioningservices | Ja | Ja |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Controller | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Nein | Nein |
> | labs | Ja | Nein |
> | labs/environments | Ja | Ja |
> | labs/servicerunners | Ja | Ja |
> | labs/virtualmachines | Ja | Nein |
> | schedules | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nein | Nein |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Nein | Nein |
> | databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Ja | Ja |
> | generatessorequest | Nein | Nein |
> | topleveldomains | Nein | Nein |
> | validatedomainregistrationinformation | Nein | Nein |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Ja | Ja |
> | eventsubscriptions | Nein, kann nicht unabhängig verschoben, aber automatisch mit abonnierter Ressource verschoben werden. | Nein, kann nicht unabhängig verschoben, aber automatisch mit abonnierter Ressource verschoben werden. |
> | extensiontopics | Nein | Nein |
> | partnernamespaces | Ja | Ja |
> | partnerregistrations | Nein | Nein |
> | partnertopics | Ja | Ja |
> | systemtopics | Ja | Ja |
> | topics | Ja | Ja |
> | topictypes | Nein | Nein |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |
> | Namespaces | Ja | Ja |
> | sku | Nein | Nein |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Nein | Nein |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Namespaces | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | featureproviders | Nein | Nein |
> | Features | Nein | Nein |
> | providers | Nein | Nein |
> | subscriptionfeatureregistrations | Nein | Nein |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nein | Nein |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Nein | Nein |
> | automanagedvmconfigurationprofiles | Nein | Nein |
> | guestconfigurationassignments | Nein | Nein |
> | software | Nein | Nein |
> | softwareupdateprofile | Nein | Nein |
> | softwareupdates | Nein | Nein |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nein | Nein |
> | sapmonitors | Nein | Nein |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Nein | Nein |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> Sie können HDInsight-Cluster in ein neues Abonnement oder eine neue Ressourcengruppe verschieben. Dagegen können die mit dem HDInsight-Cluster verknüpften Netzwerkressourcen (z.B. virtuelles Netzwerk, NIC oder Load Balancer) nicht zwischen Abonnements verschoben werden. Darüber hinaus kann eine NIC, die an einen virtuellen Computer für den Cluster angefügt ist, nicht in eine neue Ressourcengruppe verschoben werden.
>
> Beim Verschieben eines HDInsight-Clusters in ein neues Abonnement sollten Sie zunächst andere Ressourcen (z.B. das Speicherkonto) verschieben. Verschieben Sie erst anschließend den HDInsight-Cluster.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | machines | Ja | Ja |
> | machines/extensions | Ja | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | Nein | Nein |
> | vnfs | Nein | Nein |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Nein | Nein |
> | networkscopes | Nein | Nein |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights

> [!IMPORTANT]
> Berücksichtigen Sie beim Verschieben in ein neues Abonnement die [Abonnementkontingente](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ja | Ja |
> | activitylogalerts | Nein | Nein |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | baseline | Nein | Nein |
> | components | Ja | Ja |
> | datacollectionrules | Nein | Nein |
> | diagnosticsettings | Nein | Nein |
> | diagnosticsettingscategories | Nein | Nein |
> | eventcategories | Nein | Nein |
> | eventtypes | Nein | Nein |
> | extendeddiagnosticsettings | Nein | Nein |
> | guestdiagnosticsettings | Nein | Nein |
> | listmigrationdate | Nein | Nein |
> | logdefinitions | Nein | Nein |
> | logprofiles | Nein | Nein |
> | logs | Nein | Nein |
> | metricalerts | Nein | Nein |
> | metricbaselines | Nein | Nein |
> | metricbatch | Nein | Nein |
> | metricdefinitions | Nein | Nein |
> | metricnamespaces | Nein | Nein |
> | metrics | Nein | Nein |
> | migratealertrules | Nein | Nein |
> | migratetonewpricingmodel | Nein | Nein |
> | myworkbooks | Nein | Nein |
> | notificationgroups | Nein | Nein |
> | privatelinkscopes | Nein | Nein |
> | rollbacktolegacypricingmodel | Nein | Nein |
> | scheduledqueryrules | Ja | Ja |
> | Topologie | Nein | Nein |
> | Transaktionen | Nein | Nein |
> | vminsightsonboardingstatuses | Nein | Nein |
> | webtests | Ja | Ja |
> | webtests/gettestresultfile | Nein | Nein |
> | workbooks | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apptemplates | Nein | Nein |
> | iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Für die Datenträgerverschlüsselung verwendete Schlüsseltresore können nicht in einer Ressourcengruppe im gleichen Abonnement oder zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deletedvaults | Nein | Nein |
> | hsmpools | Nein | Nein |
> | managedhsms | Nein | Nein |
> | vaults | Ja | Ja |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ja | Ja |
> | registeredsubscriptions | Nein | Nein |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Nein | Nein |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nein | Nein |
> | users | Nein | Nein |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nein | Nein |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nein | Nein |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nein | Nein |
> | integrationaccounts | Ja | Ja |
> | integrationserviceenvironments | Ja | Nein |
> | integrationserviceenvironments/managedapis | Ja | Nein |
> | isolatedenvironments | Nein | Nein |
> | workflows | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Nein | Nein |
> | webservices | Ja | Nein |
> | workspaces | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nein | Nein |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nein | Nein |
> | teamaccounts | Nein | Nein |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nein | Nein |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Nein | Nein |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationassignments | Nein | Nein |
> | maintenanceconfigurations | Ja | Ja |
> | updates | Nein | Nein |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Identitäten | Nein | Nein |
> | userassignedidentities | Nein | Nein |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managednetworks | Nein | Nein |
> | managednetworks/managednetworkgroups | Nein | Nein |
> | managednetworks/managednetworkpeeringpolicies | Nein | Nein |
> | Benachrichtigung | Nein | Nein |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Nein | Nein |
> | registrationassignments | Nein | Nein |
> | registrationdefinitions | Nein | Nein |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | getentities | Nein | Nein |
> | managementgroups | Nein | Nein |
> | managementgroups/settings | Nein | Nein |
> | ressourcen | Nein | Nein |
> | starttenantbackfill | Nein | Nein |
> | tenantbackfillstatus | Nein | Nein |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | accounts/privateatlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | offers | Nein | Nein |
> | offertypes | Nein | Nein |
> | privategalleryitems | Nein | Nein |
> | privatestoreclient | Nein | Nein |
> | privatestores | Nein | Nein |
> | products | Nein | Nein |
> | publishers | Nein | Nein |
> | Registrieren | Nein | Nein |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nein | Nein |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | agreements | Nein | Nein |
> | offertypes | Nein | Nein |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | Ja | Ja |
> | mediaservices/liveevents | Ja | Ja |
> | mediaservices/streamingendpoints | Ja | Ja |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | Nein | Nein |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Nein | Nein |
> | migrateprojects | Nein | Nein |
> | movecollections | Nein | Nein |
> | projects | Nein | Nein |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nein | Nein |
> | objectunderstandingaccounts | Nein | Nein |
> | remoterenderingaccounts | Ja | Ja |
> | spatialanchorsaccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nein | Nein |
> | netappaccounts/capacitypools | Nein | Nein |
> | netappaccounts/capacitypools/volumes | Nein | Nein |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for networking resources](./move-limitations/networking-move-limitations.md) (Anleitung zum Verschieben von Netzwerkressourcen).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nein | Nein |
> | applicationgatewaywebapplicationfirewallpolicies | Nein | Nein |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewalls | Nein | Nein |
> | bastionhosts | Nein | Nein |
> | bgpservicecommunities | Nein | Nein |
> | connections | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Nein | Nein |
> | dnszones | Ja | Ja |
> | expressroutecircuits | Nein | Nein |
> | expressroutegateways | Nein | Nein |
> | expressrouteserviceproviders | Nein | Nein |
> | firewallpolicies | Ja | Ja |
> | frontdoors | Nein | Nein |
> | ipallocations | Ja | Ja |
> | ipgroups | Ja | Ja |
> | loadbalancers | Ja: Basic-SKU<br> Ja: Standard-SKU | Ja: Basic-SKU<br>Nein: Standard-SKU |
> | localnetworkgateways | Ja | Ja |
> | natgateways | Nein | Nein |
> | networkexperimentprofiles | Nein | Nein |
> | networkintentpolicies | Ja | Ja |
> | networkinterfaces | Ja | Ja |
> | networkprofiles | Nein | Nein |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Nein | Nein |
> | networkwatchers/connectionmonitors | Ja | Nein |
> | networkwatchers/flowlogs | Ja | Nein |
> | networkwatchers/pingmeshes | Ja | Nein |
> | p2svpngateways | Nein | Nein |
> | privatednszones | Ja | Ja |
> | privatednszones/virtualnetworklinks | Ja | Ja |
> | privatednszonesinternal | Nein | Nein |
> | privateendpointredirectmaps | Nein | Nein |
> | privateendpoints | Nein | Nein |
> | privatelinkservices | Nein | Nein |
> | publicipaddresses | Ja: Basic-SKU<br>Ja: Standard-SKU | Ja: Basic-SKU<br>Nein: Standard-SKU |
> | publicipprefixes | Ja | Ja |
> | routefilters | Nein | Nein |
> | routetables | Ja | Ja |
> | securitypartnerproviders | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagergeographichierarchies | Nein | Nein |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/heatmaps | Nein | Nein |
> | trafficmanagerusermetricskeys | Nein | Nein |
> | virtualhubs | Nein | Nein |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Nein | Nein |
> | virtualrouters | Ja | Ja |
> | virtualwans | Nein | Nein |
> | vpngateways (Virtual WAN) | Nein | Nein |
> | vpnserverconfigurations | Nein | Nein |
> | vpnsites (Virtual WAN) | Nein | Nein |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Namespaces | Ja | Ja |
> | namespaces/notificationhubs | Ja | Ja |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hypervsites | Nein | Nein |
> | importsites | Nein | Nein |
> | serversites | Nein | Nein |
> | vmwaresites | Nein | Nein |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Berücksichtigen Sie beim Verschieben in ein neues Abonnement die [Abonnementkontingente](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Arbeitsbereiche, die ein verknüpftes Automation-Konto besitzen, können nicht verschoben werden. Bevor Sie mit einem Verschiebevorgang beginnen, stellen Sie sicher, dass Sie die Verknüpfung jeglicher Automation-Kontos aufheben.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Nein | Nein |
> | deletedworkspaces | Nein | Nein |
> | linktargets | Nein | Nein |
> | storageinsightconfigs | Nein | Nein |
> | workspaces | Ja | Ja |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nein | Nein |
> | managementconfigurations | Ja | Ja |
> | solutions | Ja | Ja |
> | views | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | legacypeerings | Nein | Nein |
> | peerasns | Nein | Nein |
> | peeringlocations | Nein | Nein |
> | peerings | Nein | Nein |
> | peeringservicecountries | Nein | Nein |
> | peeringservicelocations | Nein | Nein |
> | peeringserviceproviders | Nein | Nein |
> | peeringservices | Nein | Nein |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyevents | Nein | Nein |
> | policystates | Nein | Nein |
> | policytrackedresources | Nein | Nein |
> | remediations | Nein | Nein |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoles | Nein | Nein |
> | dashboards | Ja | Ja |
> | usersettings | Nein | Nein |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nein | Nein |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Nein | Nein |
> | providerregistrations | Nein | Nein |
> | rollouts | Nein | Nein |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Nein | Nein |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Verschieben eines Recovery Services-Tresors zwischen Azure-Abonnements und Ressourcengruppen](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | Nein | Nein |
> | vaults | Ja | Ja |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Nein | Nein |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Namespaces | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Abfragen | Ja | Ja |
> | resourcechangedetails | Nein | Nein |
> | resourcechanges | Nein | Nein |
> | ressourcen | Nein | Nein |
> | resourceshistory | Nein | Nein |
> | subscriptionsstatus | Nein | Nein |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | childresources | Nein | Nein |
> | emergingissues | Nein | Nein |
> | events | Nein | Nein |
> | metadata | Nein | Nein |
> | Benachrichtigungen | Nein | Nein |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deployments | Nein | Nein |
> | deploymentscripts | Nein | Nein |
> | deploymentscripts/logs | Nein | Nein |
> | Verknüpfungen | Nein | Nein |
> | providers | Nein | Nein |
> | resourcegroups | Nein | Nein |
> | ressourcen | Nein | Nein |
> | subscriptions | Nein | Nein |
> | tags | Nein | Nein |
> | templatespecs | Nein | Nein |
> | templatespecs/versions | Nein | Nein |
> | tenants | Nein | Nein |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | Ja | Nein |
> | saasresources | Nein | Nein |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Es ist nicht möglich, mehrere Search-Ressourcen in verschiedenen Regionen gleichzeitig zu verschieben. Verschieben Sie diese stattdessen in mehreren Vorgängen.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | Nein | Nein |
> | searchservices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nein | Nein |
> | advancedthreatprotectionsettings | Nein | Nein |
> | alerts | Nein | Nein |
> | allowedconnections | Nein | Nein |
> | applicationwhitelistings | Nein | Nein |
> | assessmentmetadata | Nein | Nein |
> | assessments | Nein | Nein |
> | autodismissalertsrules | Nein | Nein |
> | automations | Ja | Ja |
> | autoprovisioningsettings | Nein | Nein |
> | complianceresults | Nein | Nein |
> | compliances | Nein | Nein |
> | datacollectionagents | Nein | Nein |
> | devicesecuritygroups | Nein | Nein |
> | discoveredsecuritysolutions | Nein | Nein |
> | externalsecuritysolutions | Nein | Nein |
> | informationprotectionpolicies | Nein | Nein |
> | iotsecuritysolutions | Ja | Ja |
> | iotsecuritysolutions/analyticsmodels | Nein | Nein |
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | Nein | Nein |
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | Nein | Nein |
> | jitnetworkaccesspolicies | Nein | Nein |
> | Richtlinien | Nein | Nein |
> | pricings | Nein | Nein |
> | regulatorycompliancestandards | Nein | Nein |
> | regulatorycompliancestandards/regulatorycompliancecontrols | Nein | Nein |
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | Nein | Nein |
> | securitycontacts | Nein | Nein |
> | securitysolutions | Nein | Nein |
> | securitysolutionsreferencedata | Nein | Nein |
> | securitystatuses | Nein | Nein |
> | securitystatusessummaries | Nein | Nein |
> | servervulnerabilityassessments | Nein | Nein |
> | settings | Nein | Nein |
> | subassessments | Nein | Nein |
> | Tasks | Nein | Nein |
> | topologies | Nein | Nein |
> | workspacesettings | Nein | Nein |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | Nein | Nein |
> | alertrules | Nein | Nein |
> | alertruletemplates | Nein | Nein |
> | automationrules | Nein | Nein |
> | bookmarks | Nein | Nein |
> | cases | Nein | Nein |
> | dataconnectors | Nein | Nein |
> | entities | Nein | Nein |
> | entityqueries | Nein | Nein |
> | incidents | Nein | Nein |
> | officeconsents | Nein | Nein |
> | settings | Nein | Nein |
> | threatintelligence | Nein | Nein |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoleservices | Nein | Nein |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | Nein | Nein |
> | nodes | Nein | Nein |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Namespaces | Ja | Ja |
> | premiummessagingregions | Nein | Nein |
> | sku | Nein | Nein |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | Nein | Nein |
> | clusters | Ja | Ja |
> | containergroups | Nein | Nein |
> | containergroupsets | Nein | Nein |
> | edgeclusters | Nein | Nein |
> | managedclusters | Nein | Nein |
> | networks | Nein | Nein |
> | secretstores | Nein | Nein |
> | volumes | Nein | Nein |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | Ja | Ja |
> | containergroups | Nein | Nein |
> | gateways | Ja | Ja |
> | networks | Ja | Ja |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Nein | Nein |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nein | Nein |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nein | Nein |
> | applications | Nein | Nein |
> | jitrequests | Nein | Nein |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Datenbank und Server müssen sich in der gleichen Ressourcengruppe befinden. Wenn Sie eine SQL Server-Instanz verschieben, werden auch alle ihre Datenbanken verschoben. Dieses Verhalten gilt für Azure SQL-Datenbank- und Azure Synapse Analytics-Datenbanken.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | Nein | Nein |
> | locations | Ja | Ja |
> | managedinstances | Nein | Nein |
> | servers | Ja | Ja |
> | servers/databases | Ja | Ja |
> | servers/databases/backuplongtermretentionpolicies | Ja | Ja |
> | servers/elasticpools | Ja | Ja |
> | servers/jobaccounts | Ja | Ja |
> | servers/jobagents | Ja | Ja |
> | virtualclusters | Ja | Ja |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ja | Ja |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ja | Ja |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | caches | Nein | Nein |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nein | Nein |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nein | Nein |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | Nein | Nein |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Stream Analytics-Aufträge können nicht verschoben werden, während sie ausgeführt werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Nein | Nein |
> | streamingjobs | Ja | Ja |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Nein | Nein |
> | instances | Nein | Nein |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | subscriptions | Nein | Nein |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Nein | Nein |
> | supporttickets | Nein | Nein |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Ja | Ja |
> | workspaces/bigdatapools | Ja | Ja |
> | workspaces/sqlpools | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Ja | Ja |
> | environments/eventsources | Ja | Ja |
> | environments/referencedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | Ja | Ja |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nein | Nein |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!IMPORTANT]
> Informationen zum Ändern des Abonnements für Azure DevOps finden Sie unter [Change or remove the Azure subscription that your organization uses for billing](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json) (Ändern oder Entfernen des Azure-Abonnements, das von Ihrer Organisation für die Abrechnung verwendet wird).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Nein | Nein |
> | account/extension | Nein | Nein |
> | account/project | Nein | Nein |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | arczones | Nein | Nein |
> | resourcepools | Nein | Nein |
> | vcenters | Nein | Nein |
> | virtualmachines | Nein | Nein |
> | virtualmachinetemplates | Nein | Nein |
> | virtualnetworks | Nein | Nein |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nein | Nein |
> | dedicatedcloudservices | Nein | Nein |
> | virtualmachines | Nein | Nein |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | Nein | Nein |
> | vnfs | Nein | Nein |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nein | Nein |
> | plans | Nein | Nein |
> | registeredsubscriptions | Nein | Nein |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for App Service resources](./move-limitations/app-service-move-limitations.md) (Anleitung zum Verschieben von App Service-Ressourcen).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availablestacks | Nein | Nein |
> | billingmeters | Nein | Nein |
> | certificates | Nein | Ja |
> | connectiongateways | Ja | Ja |
> | connections | Ja | Ja |
> | customapis | Ja | Ja |
> | deletedsites | Nein | Nein |
> | deploymentlocations | Nein | Nein |
> | georegions | Nein | Nein |
> | hostingenvironments | Nein | Nein |
> | kubeenvironments | Ja | Ja |
> | publishingusers | Nein | Nein |
> | empfehlungen | Nein | Nein |
> | resourcehealthmetadata | Nein | Nein |
> | runtimes | Nein | Nein |
> | serverfarms | Ja | Ja |
> | serverfarms/eventgridfilters | Nein | Nein |
> | sites | Ja | Ja |
> | sites/premieraddons | Ja | Ja |
> | sites/slots | Ja | Ja |
> | sourcecontrols | Nein | Nein |
> | staticsites | Nein | Nein |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | Nein | Nein |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nein | Nein |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workloads | Nein | Nein |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Nein | Nein |
> | componentssummary | Nein | Nein |
> | monitorinstances | Nein | Nein |
> | monitorinstancessummary | Nein | Nein |
> | monitors | Nein | Nein |

## <a name="third-party-services"></a>Dienste von Drittanbietern

Derzeit wird der Verschiebevorgang nicht für Drittanbieterdienste unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Befehle zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](move-resource-group-and-subscription.md).

Um die Daten als Datei mit durch Trennzeichen getrennten Werten abzurufen, laden Sie [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) herunter.
