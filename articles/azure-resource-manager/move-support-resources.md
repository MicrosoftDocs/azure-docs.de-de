---
title: Unterstützung des Verschiebevorgangs je nach Azure-Ressourcentyp
description: Eine Liste von Azure-Ressourcentypen, die in eine neue Ressourcengruppe oder ein neues Abonnement verschoben werden können.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 6/6/2019
ms.author: tomfitz
ms.openlocfilehash: 314b28edbd5770186d96fb2a2b203f26ff27bda0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752349"
---
# <a name="move-operation-support-for-resources"></a>Unterstützung des Verschiebevorgangs für Ressourcen
In diesem Artikel wird aufgeführt, für welche Azure-Ressourcentypen der Verschiebevorgang unterstützt wird. Auch wenn ein Ressourcentyp den Verschiebevorgang unterstützt, kann dies bestimmten Bedingungen unterliegen, die das Verschieben der Ressource verhindern. Ausführliche Informationen über Bedingungen, die sich auf Verschiebevorgänge auswirken, finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](resource-group-move-resources.md).

Um die Daten als Datei mit durch Trennzeichen getrennten Werten abzurufen, laden Sie [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) herunter.

## <a name="microsoftaad"></a>Microsoft.AAD
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| domainservices | Nein | Nein |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| tenants | Nein | Nein |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| actionRules | Ja | Ja |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| service | Ja | Ja |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| configurationstores | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| apiapps | Nein | Nein |
| appidentities | Nein | Nein |
| gateways | Nein | Nein |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| policyassignments | Nein | Nein |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| automationaccounts | Ja | Ja |
| automationaccounts/configurations | Ja | Ja |
| automationaccounts/runbooks | Ja | Ja |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| b2cdirectories | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| registrations | Ja | Ja |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| backupvault | Nein | Nein |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| batchaccounts | Ja | Ja |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | Nein | Nein |
| fileservers | Nein | Nein |
| jobs | Nein | Nein |
| workspaces | Nein | Nein |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| mapapis | Nein | Nein |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| biztalk | Ja | Ja |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| blockchainmembers | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nein | Nein |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| redis | Ja | Ja |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| profiles | JA | Ja |
| profiles/endpoints | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| certificateorders | Ja | Ja |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| domainnames | Ja | Nein |
| virtualmachines | Ja | Nein |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nein | Nein |
| reservedips | Nein | Nein |
| virtualnetworks | Nein | Nein |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| storageaccounts | Ja | Nein |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| availabilitysets | Ja | Ja |
| disks | Ja | Ja |
| galleries | Nein | Nein |
| galleries/images | Nein | Nein |
| galleries/images/versions | Nein | Nein |
| hostgroups | Nein | Nein |
| hostgroups/hosts | Nein | Nein |
| images | Ja | Ja |
| proximityplacementgroups | Nein | Nein |
| restorepointcollections | Nein | Nein |
| sharedvmimages | Nein | Nein |
| sharedvmimages/versions | Nein | Nein |
| snapshots | Ja | Ja |
| virtualmachines | Ja | Ja |
| virtualmachines/extensions | Ja | Ja |
| virtualmachinescalesets | Ja | Ja |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| containergroups | Nein | Nein |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| containergroups | Nein | Nein |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| registries | Ja | Ja |
| registries/buildtasks | Ja | Ja |
| registries/replications | Ja | Ja |
| registries/tasks | Ja | Ja |
| registries/webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| containerservices | Nein | Nein |
| managedclusters | Nein | Nein |
| openshiftmanagedclusters | Nein | Nein |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| applications | JA | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| connectors | JA | Ja |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| hubs | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| jobs | Nein | Nein |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nein | Nein |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| workspaces | Nein | Nein |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| catalogs | Ja | Ja |
| datacatalogs | Nein | Nein |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| connectionmanagers | Nein | Nein |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| packages | Nein | Nein |
| plans | Nein | Nein |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| datafactories | Ja | Ja |
| factories | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Nein | Nein |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| services | Nein | Nein |
| services/projects | Nein | Nein |
| slots | Nein | Nein |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| servers | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| servers | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| servergroups | Nein | Nein |
| servers | Ja | Ja |
| serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| artifactsources | Ja | Ja |
| rollouts | Ja | Ja |
| servicetopologies | Ja | Ja |
| servicetopologies/services | Ja | Ja |
| servicetopologies/services/serviceunits | Ja | Ja |
| steps | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| elasticpools | Nein | Nein |
| elasticpools/iothubtenants | Nein | Nein |
| iothubs | Ja | Ja |
| provisioningservices | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| Controller | Nein | Nein |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| labcenters | Nein | Nein |
| labs | Ja | Nein |
| labs/environments | Ja | Ja |
| labs/servicerunners | Ja | Ja |
| labs/virtualmachines | Ja | Nein |
| schedules | Ja | Ja |

## <a name="microsoftdns"></a>microsoft.dns
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| dnszones | Nein | Nein |
| dnszones/a | Nein | Nein |
| dnszones/aaaa | Nein | Nein |
| dnszones/cname | Nein | Nein |
| dnszones/mx | Nein | Nein |
| dnszones/ptr | Nein | Nein |
| dnszones/srv | Nein | Nein |
| dnszones/txt | Nein | Nein |
| trafficmanagerprofiles | Nein | Nein |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| domains | Ja | Ja |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| domains | Ja | Ja |
| topics | Ja | Ja |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | Ja | Ja |
| namespaces | Ja | Ja |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| hanainstances | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | Ja | Ja |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| machines | Nein | Nein |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| datamanagers | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| jobs | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |
| actiongroups | Ja | Ja |
| activitylogalerts | Nein | Nein |
| alertrules | Ja | Ja |
| autoscalesettings | Ja | Ja |
| components | Ja | Ja |
| guestdiagnosticsettings | Nein | Nein |
| metricalerts | Nein | Nein |
| notificationgroups | Nein | Nein |
| notificationrules | Nein | Nein |
| scheduledqueryrules | Ja | Ja |
| webtests | Ja | Ja |
| workbooks | Ja | Ja |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| checknameavailability | Ja | Ja |
| graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| hsmpools | Nein | Nein |
| vaults | Ja | Ja |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| labaccounts | Ja | Ja |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| hostingenvironments | Nein | Nein |
| integrationaccounts | Ja | Ja |
| integrationserviceenvironments | Nein | Nein |
| isolatedenvironments | Nein | Nein |
| workflows | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| commitmentplans | Ja | Ja |
| webservices | Ja | Nein |
| workspaces | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Ja | Ja |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |
| accounts/workspaces | Nein | Nein |
| accounts/workspaces/projects | Nein | Nein |
| teamaccounts | Nein | Nein |
| teamaccounts/workspaces | Nein | Nein |
| teamaccounts/workspaces/projects | Nein | Nein |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| hostingaccounts | Nein | Nein |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| workspaces | Nein | Nein |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| userassignedidentities | Nein | Nein |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| classicdevservices | Nein | Nein |

## <a name="microsoftmedia"></a>Microsoft.Media
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| mediaservices | Ja | Ja |
| mediaservices/liveevents | Ja | Ja |
| mediaservices/streamingendpoints | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| assessmentprojects | Nein | Nein |
| migrateprojects | Nein | Nein |
| projects | Nein | Nein |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| netappaccounts | Nein | Nein |
| netappaccounts/capacitypools | Nein | Nein |
| netappaccounts/capacitypools/volumes | Nein | Nein |
| netappaccounts/capacitypools/volumes/mounttargets | Nein | Nein |
| netappaccounts/capacitypools/volumes/snapshots | Nein | Nein |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| applicationgateways | Nein | Nein |
| applicationgatewaywebapplicationfirewallpolicies | Nein | Nein |
| applicationsecuritygroups | Ja | Ja |
| azurefirewalls | Ja | Ja |
| bastionhosts | Nein | Nein |
| connections | Ja | Ja |
| ddoscustompolicies | Ja | Ja |
| ddosprotectionplans | Nein | Nein |
| dnszones | Ja | Ja |
| expressroutecircuits | Nein | Nein |
| expressroutecrossconnections | Nein | Nein |
| expressroutegateways | Nein | Nein |
| expressrouteports | Nein | Nein |
| frontdoors | Ja | Ja |
| frontdoorwebapplicationfirewallpolicies | Ja | Ja |
| loadbalancers | Ja | Ja |
| localnetworkgateways | Ja | Ja |
| natgateways | Ja | Ja |
| networkintentpolicies | Ja | Ja |
| networkinterfaces | Ja | Ja |
| networkprofiles | Nein | Nein |
| networksecuritygroups | Ja | Ja |
| networkwatchers | Ja | Ja |
| networkwatchers/connectionmonitors | Ja | Ja |
| networkwatchers/lenses | Ja | Ja |
| networkwatchers/pingmeshes | Ja | Ja |
| p2svpngateways | Nein | Nein |
| privatednszones | Ja | Ja |
| privatednszones/virtualnetworklinks | Ja | Ja |
| privateendpoints | Nein | Nein |
| privatelinkservices | Nein | Nein |
| publicipaddresses | Ja | Ja |
| publicipprefixes | Ja | Ja |
| routefilters | Nein | Nein |
| routetables | Ja | Ja |
| securegateways | Ja | Ja |
| serviceendpointpolicies | Ja | Ja |
| trafficmanagerprofiles | Ja | Ja |
| virtualhubs | Nein | Nein |
| virtualnetworkgateways | Ja | Ja |
| virtualnetworks | Ja | Ja |
| virtualnetworktaps | Nein | Nein |
| virtualwans | Nein | Nein |
| vpngateways | Nein | Nein |
| vpnsites | Nein | Nein |
| webapplicationfirewallpolicies | Ja | Ja |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| namespaces | Ja | Ja |
| namespaces/notificationhubs | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| workspaces | Ja | Ja |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| managementconfigurations | Ja | Ja |
| solutions | Ja | JA |
| views | JA | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| peerings | Nein | Nein |

## <a name="microsoftportal"></a>Microsoft.Portal
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| dashboards | Ja | Ja |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| rootresources | Nein | Nein |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| capacities | Ja | Ja |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Nein | Nein |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| vaults | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| namespaces | Ja | Ja |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| applications | JA | Nein |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| flows | Ja | Ja |
| jobcollections | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| searchservices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Ja | Ja |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| gateways | Nein | Nein |
| nodes | Nein | Nein |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| namespaces | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| applications | Nein | Nein |
| clusters | Ja | Ja |
| containergroups | Nein | Nein |
| containergroupsets | Nein | Nein |
| edgeclusters | Nein | Nein |
| networks | Nein | Nein |
| secretstores | Nein | Nein |
| volumes | Nein | Nein |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| applications | JA | Ja |
| containergroups | Nein | Nein |
| gateways | Ja | JA |
| networks | Ja | Ja |
| secrets | Ja | Ja |
| volumes | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| signalr | Ja | Ja |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nein | Nein |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nein | Nein |
| appliances | Nein | Nein |
| applicationdefinitions | Nein | Nein |
| applications | Nein | Nein |
| jitrequests | Nein | Nein |

## <a name="microsoftsql"></a>Microsoft.Sql
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| instancepools | Ja | Ja |
| managedinstances | Ja | Ja |
| managedinstances/databases | Ja | Ja |
| servers | Ja | Ja |
| servers/databases | Ja | Ja |
| servers/elasticpools | Ja | Ja |
| virtualclusters | Ja | Ja |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Ja | Ja |
| sqlvirtualmachines | Ja | Ja |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| dwvm | Nein | Nein |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| storageaccounts | Ja | Ja |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| caches | Nein | Nein |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nein | Nein |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nein | Nein |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| managers | Nein | Nein |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| streamingjobs | Ja | Ja |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| environments | Nein | Nein |
| environments/eventsources | Nein | Nein |
| instances | Nein | Nein |
| instances/environments | Nein | Nein |
| instances/environments/eventsources | Nein | Nein |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| providerregistrations | Nein | Nein |
| ressourcen | Nein | Nein |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| environments | Ja | Ja |
| environments/eventsources | Ja | Ja |
| environments/referencedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Microsoft.Token
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| stores | Nein | Nein |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| imagetemplates | Nein | Nein |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| account | JA | Ja |
| account/extension | Ja | Ja |
| account/project | Ja | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Ja | Ja |
| dedicatedcloudservices | Ja | Ja |
| virtualmachines | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| certificates | Nein | Ja |
| connectiongateways | Ja | Ja |
| connections | Ja | Ja |
| customapis | Ja | Ja |
| hostingenvironments | Nein | Nein |
| serverfarms | Ja | Ja |
| sites | Ja | Ja |
| sites/premieraddons | Ja | Ja |
| sites/slots | Ja | Ja |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| deviceservices | Nein | Nein |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Ressourcentyp | Ressourcengruppe | Abonnement |
| ------------- | ----------- | ---------- |
| applicationgroups | Nein | Nein |
| hostpools | Nein | Nein |
| workspaces | Nein | Nein |

## <a name="third-party-services"></a>Dienste von Drittanbietern

Derzeit wird der Verschiebevorgang nicht für Drittanbieterdienste unterstützt.

## <a name="next-steps"></a>Nächste Schritte
Befehle zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](resource-group-move-resources.md).
