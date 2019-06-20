---
title: 'Azure Resource Manager: Tagunterstützung für Ressourcen'
description: Zeigt, welche Azure-Ressourcentypen Tags unterstützen. Enthält Details für alle Azure-Dienste.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 05/10/2019
ms.author: tomfitz
ms.openlocfilehash: 7ef37323fb8150e3a6b52800bfafa2585ae328c2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523233"
---
# <a name="tag-support-for-azure-resources"></a>Tagunterstützung für Azure-Ressourcen
In diesem Artikel erfahren Sie, ob ein Ressourcentyp [Tags](resource-group-using-tags.md) unterstützt. Die Spalte mit der Bezeichnung **Tagunterstützung** zeigt an, ob der Ressourcentyp eine Eigenschaft für das Tag hat. Die Spalte mit der Bezeichnung **Tag in Kostenbericht** gibt an, ob dieser Ressourcentyp das Tag an den Kostenbericht übergibt.

Um die Daten als Datei mit durch Trennzeichen getrennten Werten abzurufen, laden Sie [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) herunter.

## <a name="microsoftaad"></a>Microsoft.AAD
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| DomainServices | Ja | Ja |
| DomainServices/oucontainer | Nein | Nein |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nein |  Nein |
| diagnosticSettingsCategories | Nein |  Nein |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| supportProviders | Nein |  Nein |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| aadsupportcases | Nein |  Nein |
| addsservices | Nein |  Nein |
| agents | Nein |  Nein |
| anonymousapiusers | Nein |  Nein |
| Konfiguration | Nein |  Nein |
| Protokolle | Nein |  Nein |
| reports | Nein |  Nein |
| services | Nein |  Nein |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| Konfigurationen | Nein |  Nein |
| generateRecommendations | Nein |  Nein |
| empfehlungen | Nein |  Nein |
| suppressions | Nein |  Nein |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| actionRules | Nein |  Nein |
| alerts | Nein |  Nein |
| alertsList | Nein |  Nein |
| alertsSummary | Nein |  Nein |
| alertsSummaryList | Nein |  Nein |
| smartDetectorAlertRules | Nein |  Nein |
| smartDetectorRuntimeEnvironments | Nein |  Nein |
| smartGroups | Nein |  Nein |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| reportFeedback | Nein |  Nein |
| service | Ja | Ja |
| validateServiceName | Nein |  Nein |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| attestationProviders | Nein |  Nein |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| classicAdministrators | Nein |  Nein |
| denyAssignments | Nein |  Nein |
| elevateAccess | Nein |  Nein |
| locks | Nein |  Nein |
| Berechtigungen | Nein |  Nein |
| policyAssignments | Nein |  Nein |
| policyDefinitions | Nein |  Nein |
| policySetDefinitions | Nein |  Nein |
| providerOperations | Nein |  Nein |
| roleAssignments | Nein |  Nein |
| roleDefinitions | Nein |  Nein |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| automationAccounts | Ja | Ja |
| automationAccounts/configurations | Ja | Ja |
| automationAccounts/jobs | Nein |  Nein |
| automationAccounts/runbooks | Ja | Ja |
| automationAccounts/softwareUpdateConfigurations | Nein | Nein |
| automationAccounts/webhooks | Nein |  Nein |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| environments | Nein |  Nein |
| environments/accounts | Nein |  Nein |
| environments/accounts/namespaces | Nein |  Nein |
| environments/accounts/namespaces/configurations | Nein |  Nein |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| b2cDirectories | Ja | Nein |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| registrations | Ja | Ja |
| registrations/customerSubscriptions | Nein |  Nein |
| registrations/products | Nein |  Nein |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| batchAccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| billingAccounts | Nein |  Nein |
| billingAccounts/billingProfiles | Nein |  Nein |
| billingAccounts/billingProfiles/billingSubscriptions | Nein |  Nein |
| billingAccounts/billingProfiles/invoices | Nein |  Nein |
| billingAccounts/billingProfiles/invoices/pricesheet | Nein |  Nein |
| billingAccounts/billingProfiles/operationStatus | Nein |  Nein |
| billingAccounts/billingProfiles/paymentMethods | Nein |  Nein |
| billingAccounts/billingProfiles/policies | Nein |  Nein |
| billingAccounts/billingProfiles/pricesheet | Nein |  Nein |
| billingAccounts/billingProfiles/products | Nein |  Nein |
| billingAccounts/billingProfiles/transactions | Nein |  Nein |
| billingAccounts/billingSubscriptions | Nein |  Nein |
| billingAccounts/departments | Nein |  Nein |
| billingAccounts/eligibleOffers | Nein |  Nein |
| billingAccounts/enrollmentAccounts | Nein |  Nein |
| billingAccounts/invoices | Nein |  Nein |
| billingAccounts/invoiceSections | Nein |  Nein |
| billingAccounts/invoiceSections/billingSubscriptions | Nein |  Nein |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Nein |  Nein |
| billingAccounts/invoiceSections/importRequests | Nein |  Nein |
| billingAccounts/invoiceSections/initiateImportRequest | Nein |  Nein |
| billingAccounts/invoiceSections/initiateTransfer | Nein |  Nein |
| billingAccounts/invoiceSections/operationStatus | Nein |  Nein |
| billingAccounts/invoiceSections/products | Nein |  Nein |
| billingAccounts/invoiceSections/transfers | Nein |  Nein |
| billingAccounts/products | Nein |  Nein |
| billingAccounts/projects | Nein |  Nein |
| billingAccounts/projects/billingSubscriptions | Nein |  Nein |
| billingAccounts/projects/importRequests | Nein |  Nein |
| billingAccounts/projects/initiateImportRequest | Nein |  Nein |
| billingAccounts/projects/operationStatus | Nein |  Nein |
| billingAccounts/projects/products | Nein |  Nein |
| billingAccounts/transactions | Nein |  Nein |
| billingPeriods | Nein |  Nein |
| BillingPermissions | Nein |  Nein |
| billingProperty | Nein |  Nein |
| BillingRoleAssignments | Nein |  Nein |
| BillingRoleDefinitions | Nein |  Nein |
| CreateBillingRoleAssignment | Nein |  Nein |
| departments | Nein |  Nein |
| enrollmentAccounts | Nein |  Nein |
| importRequests | Nein |  Nein |
| importRequests/acceptImportRequest | Nein |  Nein |
| importRequests/declineImportRequest | Nein |  Nein |
| invoices | Nein |  Nein |
| transfers | Nein |  Nein |
| transfers/acceptTransfer | Nein |  Nein |
| transfers/declineTransfer | Nein |  Nein |
| transfers/operationStatus | Nein |  Nein |
| usagePlans | Nein |  Nein |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| mapApis | Ja | Ja |
| updateCommunicationPreference | Nein |  Nein |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| BizTalk | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Nein |  Nein |
| blueprintAssignments/assignmentOperations | Nein |  Nein |
| blueprintAssignments/operations | Nein |  Nein |
| blueprints | Nein |  Nein |
| blueprints/artifacts | Nein |  Nein |
| blueprints/versions | Nein |  Nein |
| blueprints/versions/artifacts | Nein |  Nein |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| botServices | Ja | Ja |
| botServices/channels | Nein |  Nein |
| botServices/connections | Nein |  Nein |

## <a name="microsoftcache"></a>Microsoft.Cache
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| Redis | Ja | Ja |
| RedisConfigDefinition | Nein |  Nein |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| appliedReservations | Nein |  Nein |
| calculatePrice | Nein |  Nein |
| catalogs | Nein |  Nein |
| commercialReservationOrders | Nein |  Nein |
| reservationOrders | Nein |  Nein |
| reservationOrders/calculateRefund | Nein |  Nein |
| reservationOrders/merge | Nein |  Nein |
| reservationOrders/reservations | Nein |  Nein |
| reservationOrders/reservations/revisions | Nein |  Nein |
| reservationOrders/return | Nein |  Nein |
| reservationOrders/split | Nein |  Nein |
| reservationOrders/swap | Nein |  Nein |
| reservations | Nein |  Nein |
| ressourcen | Nein |  Nein |
| validateReservationOrder | Nein |  Nein |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| edgenodes | Nein |  Nein |
| profiles | JA | Ja |
| profiles/endpoints | Ja | Ja |
| profiles/endpoints/customdomains | Nein |  Nein |
| profiles/endpoints/origins | Nein |  Nein |
| validateProbe | Nein |  Nein |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| certificateOrders | Ja | Ja |
| certificateOrders/certificates | Nein |  Nein |
| validateCertificateRegistrationInformation | Nein |  Nein |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| capabilities | Nein |  Nein |
| domainNames | Nein |  Nein |
| domainNames/capabilities | Nein |  Nein |
| domainNames/internalLoadBalancers | Nein |  Nein |
| domainNames/serviceCertificates | Nein |  Nein |
| domainNames/slots | Nein |  Nein |
| domainNames/slots/roles | Nein |  Nein |
| moveSubscriptionResources | Nein |  Nein |
| operatingSystemFamilies | Nein |  Nein |
| operatingSystems | Nein |  Nein |
| quotas | Nein |  Nein |
| resourceTypes | Nein |  Nein |
| validateSubscriptionMoveAvailability | Nein |  Nein |
| virtualMachines | Nein |  Nein |
| virtualMachines/diagnosticSettings | Nein |  Nein |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Nein |  Nein |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| capabilities | Nein |  Nein |
| expressRouteCrossConnections | Nein |  Nein |
| expressRouteCrossConnections/peerings | Nein |  Nein |
| gatewaySupportedDevices | Nein |  Nein |
| networkSecurityGroups | Nein |  Nein |
| quotas | Nein |  Nein |
| reservedIps | Nein |  Nein |
| virtualNetworks | Nein |  Nein |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nein |  Nein |
| virtualNetworks/virtualNetworkPeerings | Nein |  Nein |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| capabilities | Nein |  Nein |
| disks | Nein |  Nein |
| images | Nein |  Nein |
| osImages | Nein |  Nein |
| osPlatformImages | Nein |  Nein |
| publicImages | Nein |  Nein |
| quotas | Nein |  Nein |
| storageAccounts | Nein |  Nein |
| storageAccounts/services | Nein |  Nein |
| storageAccounts/services/diagnosticSettings | Nein |  Nein |
| storageAccounts/vmImages | Nein |  Nein |
| vmImages | Nein |  Nein |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| RateCard | Nein |  Nein |
| UsageAggregates | Nein |  Nein |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| availabilitySets | Ja | Ja |
| disks | Ja | Ja |
| images | Ja | Ja |
| restorePointCollections | Ja | Ja |
| restorePointCollections/restorePoints | Nein |  Nein |
| sharedVMImages | Ja | Ja |
| sharedVMImages/versions | Ja | Ja |
| snapshots | Ja | Ja |
| virtualMachines | Ja | Ja |
| virtualMachines/diagnosticSettings | Nein |  Nein |
| virtualMachines/extensions | Ja | Ja |
| virtualMachineScaleSets | Ja | Ja |
| virtualMachineScaleSets/extensions | Nein |  Nein |
| virtualMachineScaleSets/networkInterfaces | Nein |  Nein |
| virtualMachineScaleSets/publicIPAddresses | Nein |  Nein |
| virtualMachineScaleSets/virtualMachines | Nein |  Nein |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nein |  Nein |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| AggregatedCost | Nein |  Nein |
| Bilanzen | Nein |  Nein |
| Budgets | Nein |  Nein |
| Charges | Nein |  Nein |
| CostTags | Nein |  Nein |
| credits | Nein |  Nein |
| events | Nein |  Nein |
| Vorhersagen | Nein |  Nein |
| lots | Nein |  Nein |
| Marketplaces | Nein |  Nein |
| Pricesheets | Nein |  Nein |
| products | Nein |  Nein |
| ReservationDetails | Nein |  Nein |
| ReservationRecommendations | Nein |  Nein |
| ReservationSummaries | Nein |  Nein |
| ReservationTransactions | Nein |  Nein |
| `Tags` | Nein |  Nein |
| Begriffe | Nein |  Nein |
| UsageDetails | Nein |  Nein |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| containerGroups | Ja | Ja |
| serviceAssociationLinks | Nein |  Nein |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| registries | Ja | Ja |
| registries/builds | Nein |  Nein |
| registries/builds/cancel | Nein |  Nein |
| registries/builds/getLogLink | Nein |  Nein |
| registries/buildTasks | Ja | Ja |
| registries/buildTasks/steps | Nein |  Nein |
| registries/eventGridFilters | Nein |  Nein |
| registries/getBuildSourceUploadUrl | Nein |  Nein |
| registries/GetCredentials | Nein |  Nein |
| registries/importImage | Nein |  Nein |
| registries/queueBuild | Nein |  Nein |
| registries/regenerateCredential | Nein |  Nein |
| registries/regenerateCredentials | Nein |  Nein |
| registries/replications | Ja | Ja |
| registries/runs | Nein |  Nein |
| registries/runs/cancel | Nein |  Nein |
| registries/scheduleRun | Nein |  Nein |
| registries/tasks | Ja | Ja |
| registries/updatePolicies | Nein |  Nein |
| registries/webhooks | Ja | Ja |
| registries/webhooks/getCallbackConfig | Nein |  Nein |
| registries/webhooks/ping | Nein |  Nein |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| containerServices | Ja | Ja |
| managedClusters | Ja | Ja |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| applications | JA | Ja |
| updateCommunicationPreference | Nein |  Nein |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| Alerts | Nein |  Nein |
| BillingAccounts | Nein |  Nein |
| Connectors | Ja | Ja |
| Departments | Nein |  Nein |
| Dimensionen | Nein |  Nein |
| EnrollmentAccounts | Nein |  Nein |
| Abfragen | Nein |  Nein |
| Registrieren | Nein |  Nein |
| Reportconfigs | Nein |  Nein |
| Berichte | Nein |  Nein |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| hubs | Ja | Ja |
| hubs/authorizationPolicies | Nein |  Nein |
| hubs/connectors | Nein |  Nein |
| hubs/connectors/mappings | Nein |  Nein |
| hubs/interactions | Nein |  Nein |
| hubs/kpi | Nein |  Nein |
| hubs/links | Nein |  Nein |
| hubs/profiles | Nein |  Nein |
| hubs/roleAssignments | Nein |  Nein |
| hubs/roles | Nein |  Nein |
| hubs/suggestTypeSchema | Nein |  Nein |
| hubs/views | Nein |  Nein |
| hubs/widgetTypes | Nein |  Nein |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| jobs | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| workspaces | Ja | Nein |
| workspaces/virtualNetworkPeerings | Nein |  Nein |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| catalogs | Ja | Ja |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| connectionManagers | Ja | Ja |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| dataFactories | Ja | Nein |
| dataFactories/diagnosticSettings | Nein |  Nein |
| dataFactorySchema | Nein |  Nein |
| factories | Ja | Nein |
| factories/integrationRuntimes | Nein |  Nein |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |
| accounts/dataLakeStoreAccounts | Nein |  Nein |
| accounts/storageAccounts | Nein |  Nein |
| accounts/storageAccounts/containers | Nein |  Nein |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |
| accounts/eventGridFilters | Nein |  Nein |
| accounts/firewallRules | Nein |  Nein |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| services | Ja | Ja |
| services/projects | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| servers | Ja | Ja |
| servers/recoverableServers | Nein |  Nein |
| servers/virtualNetworkRules | Nein |  Nein |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| servers | Ja | Ja |
| servers/recoverableServers | Nein |  Nein |
| servers/virtualNetworkRules | Nein |  Nein |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| servers | Ja | Ja |
| servers/advisors | Nein |  Nein |
| servers/queryTexts | Nein |  Nein |
| servers/recoverableServers | Nein |  Nein |
| servers/topQueryStatistics | Nein |  Nein |
| servers/virtualNetworkRules | Nein |  Nein |
| servers/waitStatistics | Nein |  Nein |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| IotHubs | Ja | Ja |
| IotHubs/eventGridFilters | Nein |  Nein |
| ProvisioningServices | Ja | Ja |
| usages | Nein |  Nein |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| Controller | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| labs | Ja | Ja |
| labs/serviceRunners | Ja | Ja |
| labs/virtualMachines | Ja | Ja |
| schedules | Ja | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Nein |  Nein |
| databaseAccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| domains | Ja | Ja |
| domains/domainOwnershipIdentifiers | Nein |  Nein |
| generateSsoRequest | Nein |  Nein |
| topLevelDomains | Nein |  Nein |
| validateDomainRegistrationInformation | Nein |  Nein |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| lcsprojects | Nein |  Nein |
| lcsprojects/clouddeployments | Nein |  Nein |
| lcsprojects/connectors | Nein |  Nein |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| domains | Ja | Nein |
| domains/topics | Nein |  Nein |
| eventSubscriptions | Nein |  Nein |
| extensionTopics | Nein |  Nein |
| topics | Ja | Nein |
| topicTypes | Nein |  Nein |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| clusters | Ja | Nein |
| namespaces | Ja | Nein |
| namespaces/authorizationrules | Nein |  Nein |
| namespaces/disasterrecoveryconfigs | Nein |  Nein |
| namespaces/eventhubs | Nein |  Nein |
| namespaces/eventhubs/authorizationrules | Nein |  Nein |
| namespaces/eventhubs/consumergroups | Nein |  Nein |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| Features | Nein |  Nein |
| providers | Nein |  Nein |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| enroll | Nein |  Nein |
| galleryitems | Nein |  Nein |
| generateartifactaccessuri | Nein |  Nein |
| myareas | Nein |  Nein |
| myareas/areas | Nein |  Nein |
| myareas/areas/areas | Nein |  Nein |
| myareas/areas/areas/galleryitems | Nein |  Nein |
| myareas/areas/galleryitems | Nein |  Nein |
| myareas/galleryitems | Nein |  Nein |
| Registrieren | Nein |  Nein |
| ressourcen | Nein |  Nein |
| retrieveresourcesbyid | Nein |  Nein |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Nein |  Nein |
| software | Nein |  Nein |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| hanaInstances | Ja |  Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| clusters | Ja | Ja |
| clusters/applications | Nein |  Nein |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| jobs | Ja | Ja |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| labelGroups | Nein |  Nein |
| labelGroups/labels | Nein |  Nein |
| labelGroups/labels/conditions | Nein |  Nein |
| labelGroups/labels/subLabels | Nein |  Nein |
| labelGroups/labels/subLabels/conditions | Nein |  Nein |

## <a name="microsoftinsights"></a>microsoft.insights
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| actiongroups | Ja | Ja |
| activityLogAlerts | Ja | Ja |
| alertrules | Ja | Ja |
| automatedExportSettings | Nein |  Nein |
| autoscalesettings | Ja | Ja |
| baseline | Nein |  Nein |
| calculatebaseline | Nein |  Nein |
| components | Ja | Ja |
| components/events | Nein |  Nein |
| components/pricingPlans | Nein |  Nein |
| components/query | Nein |  Nein |
| diagnosticSettings | Nein |  Nein |
| diagnosticSettingsCategories | Nein |  Nein |
| eventCategories | Nein |  Nein |
| eventtypes | Nein |  Nein |
| extendedDiagnosticSettings | Nein |  Nein |
| logDefinitions | Nein |  Nein |
| logprofiles | Nein |  Nein |
| Protokolle | Nein |  Nein |
| metricAlerts | Ja | Ja |
| migrateToNewPricingModel | Nein |  Nein |
| myWorkbooks | Nein |  Nein |
| Abfragen | Nein |  Nein |
| rollbackToLegacyPricingModel | Nein |  Nein |
| scheduledqueryrules | Ja | Ja |
| vmInsightsOnboardingStatuses | Nein |  Nein |
| webtests | Ja | Ja |
| workbooks | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nein |  Nein |
| diagnosticSettingsCategories | Nein |  Nein |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| deletedVaults | Nein |  Nein |
| vaults | Ja | Ja |
| vaults/accessPolicies | Nein |  Nein |
| vaults/secrets | Nein |  Nein |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| clusters | Ja | Ja |
| clusters/databases | Nein |  Nein |
| clusters/databases/dataconnections | Nein |  Nein |
| clusters/databases/eventhubconnections | Nein |  Nein |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| labaccounts | Ja | Ja |
| users | Nein |  Nein |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| Protokolle | Nein |  Nein |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| integrationAccounts | Ja | Ja |
| workflows | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| commitmentPlans | Ja | Ja |
| webServices | Ja | Ja |
| Arbeitsbereiche | Ja | Ja |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |
| accounts/workspaces | Ja | Ja |
| accounts/workspaces/projects | Ja | Ja |
| teamAccounts | Ja | Ja |
| teamAccounts/workspaces | Ja | Ja |
| teamAccounts/workspaces/projects | Ja | Ja |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| workspaces | Ja | Ja |
| workspaces/computes | Nein |  Nein |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| Identities | Nein |  Nein |
| userAssignedIdentities | Ja | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| getEntities | Nein |  Nein |
| managementGroups | Nein |  Nein |
| ressourcen | Nein |  Nein |
| startTenantBackfill | Nein |  Nein |
| tenantBackfillStatus | Nein |  Nein |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |
| accounts/eventGridFilters | Nein |  Nein |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| offers | Nein |  Nein |
| offerTypes | Nein |  Nein |
| offerTypes/publishers | Nein |  Nein |
| offerTypes/publishers/offers | Nein |  Nein |
| offerTypes/publishers/offers/plans | Nein |  Nein |
| offerTypes/publishers/offers/plans/agreements | Nein |  Nein |
| offerTypes/publishers/offers/plans/configs | Nein |  Nein |
| offerTypes/publishers/offers/plans/configs/importImage | Nein |  Nein |
| privategalleryitems | Nein |  Nein |
| products | Nein |  Nein |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| classicDevServices | Ja | Ja |
| updateCommunicationPreference | Nein |  Nein |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| agreements | Nein |  Nein |
| offertypes | Nein |  Nein |

## <a name="microsoftmedia"></a>Microsoft.Media
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| mediaservices | Ja | Ja |
| mediaservices/accountFilters | Nein |  Nein |
| mediaservices/assets | Nein |  Nein |
| mediaservices/assets/assetFilters | Nein |  Nein |
| mediaservices/contentKeyPolicies | Nein |  Nein |
| mediaservices/eventGridFilters | Nein |  Nein |
| mediaservices/liveEventOperations | Nein |  Nein |
| mediaservices/liveEvents | Ja | Ja |
| mediaservices/liveEvents/liveOutputs | Nein |  Nein |
| mediaservices/liveOutputOperations | Nein |  Nein |
| mediaservices/streamingEndpointOperations | Nein |  Nein |
| mediaservices/streamingEndpoints | Ja | Ja |
| mediaservices/streamingLocators | Nein |  Nein |
| mediaservices/streamingPolicies | Nein |  Nein |
| mediaservices/transforms | Nein |  Nein |
| mediaservices/transforms/jobs | Nein |  Nein |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| projects | Ja | Ja |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| applicationGateways | Ja | Nein |
| applicationSecurityGroups | Ja | Ja |
| azureFirewallFqdnTags | Nein |  Nein |
| azureFirewalls | Ja | Nein |
| bgpServiceCommunities | Nein |  Nein |
| connections | Ja | Ja |
| ddosCustomPolicies | Ja | Ja |
| ddosProtectionPlans | Ja | Ja |
| dnsOperationStatuses | Nein |  Nein |
| dnszones | Ja | Ja |
| dnszones/A | Nein |  Nein |
| dnszones/AAAA | Nein |  Nein |
| dnszones/all | Nein |  Nein |
| dnszones/CAA | Nein |  Nein |
| dnszones/CNAME | Nein |  Nein |
| dnszones/MX | Nein |  Nein |
| dnszones/NS | Nein |  Nein |
| dnszones/PTR | Nein |  Nein |
| dnszones/recordsets | Nein |  Nein |
| dnszones/SOA | Nein |  Nein |
| dnszones/SRV | Nein |  Nein |
| dnszones/TXT | Nein |  Nein |
| expressRouteCircuits | Ja  | Nein |
| expressRouteServiceProviders | Nein |  Nein |
| frontdoors | Ja | Ja |
| frontdoorWebApplicationFirewallPolicies | Ja | Ja |
| getDnsResourceReference | Nein |  Nein |
| interfaceEndpoints | Ja | Ja |
| internalNotify | Nein |  Nein |
| loadBalancers | Ja | Nein |
| localNetworkGateways | Ja | Ja |
| natGateways | Ja | Ja |
| networkIntentPolicies | Ja | Ja |
| networkInterfaces | Ja | Ja |
| networkProfiles | Ja | Ja |
| networkSecurityGroups | Ja | Ja |
| networkWatchers | Ja | Nein |
| networkWatchers/connectionMonitors | Ja | Nein |
| networkWatchers/lenses | Ja | Nein |
| networkWatchers/pingMeshes | Ja | Nein |
| privateLinkServices | Ja | Ja |
| publicIPAddresses | Ja | Ja |
| publicIPPrefixes | Ja | Ja |
| routeFilters | Ja | Ja |
| routeTables | Ja | Ja |
| serviceEndpointPolicies | Ja | Ja |
| trafficManagerGeographicHierarchies | Nein |  Nein |
| trafficmanagerprofiles | Ja | Ja |
| trafficmanagerprofiles/heatMaps | Nein |  Nein |
| virtualHubs | Ja | Ja |
| virtualNetworkGateways | Ja | Nein |
| virtualNetworks | Ja | Ja |
| virtualNetworks/subnets | Nein |  Nein |
| virtualNetworkTaps | Ja | Ja |
| virtualWans | Ja | Ja |
| vpnGateways | Ja | Nein |
| vpnSites | Ja | Ja |
| webApplicationFirewallPolicies | Ja | Ja |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| namespaces | Ja | Nein |
| namespaces/notificationHubs | Ja | Nein |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| devices | Nein |  Nein |
| linkTargets | Nein |  Nein |
| storageInsightConfigs | Nein |  Nein |
| workspaces | Ja | Ja |
| workspaces/dataSources | Nein |  Nein |
| workspaces/linkedServices | Nein |  Nein |
| workspaces/query | Nein |  Nein |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| managementassociations | Nein |  Nein |
| managementconfigurations | Ja | Ja |
| solutions | Ja | JA |
| views | JA | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| policyEvents | Nein |  Nein |
| policyStates | Nein |  Nein |
| policyTrackedResources | Nein |  Nein |
| remediations | Nein |  Nein |

## <a name="microsoftportal"></a>Microsoft.Portal
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| consoles | Nein |  Nein |
| dashboards | Ja | Ja |
| userSettings | Nein |  Nein |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| capacities | Ja | Ja |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| accounts | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Nein |  Nein |
| vaults | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| namespaces | Ja | Ja |
| namespaces/authorizationrules | Nein |  Nein |
| namespaces/hybridconnections | Nein |  Nein |
| namespaces/hybridconnections/authorizationrules | Nein |  Nein |
| namespaces/wcfrelays | Nein |  Nein |
| namespaces/wcfrelays/authorizationrules | Nein |  Nein |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| ressourcen | Nein |  Nein |
| subscriptionsStatus | Nein |  Nein |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Nein |  Nein |
| childAvailabilityStatuses | Nein |  Nein |
| childResources | Nein |  Nein |
| events | Nein |  Nein |
| impactedResources | Nein |  Nein |
| Benachrichtigungen | Nein |  Nein |

## <a name="microsoftresources"></a>Microsoft.Resources
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| deployments | Nein |  Nein |
| deployments/operations | Nein |  Nein |
| links | Nein |  Nein |
| notifyResourceJobs | Nein |  Nein |
| providers | Nein |  Nein |
| resourceGroups | Nein |  Nein |
| ressourcen | Nein |  Nein |
| subscriptions | Nein |  Nein |
| subscriptions/providers | Nein |  Nein |
| subscriptions/resourceGroups | Nein |  Nein |
| subscriptions/resourcegroups/resources | Nein |  Nein |
| subscriptions/resources | Nein |  Nein |
| subscriptions/tagnames | Nein |  Nein |
| subscriptions/tagNames/tagValues | Nein |  Nein |
| tenants | Nein |  Nein |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| applications | JA | Ja |
| saasresources | Nein |  Nein |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| flows | Ja | Ja |
| jobcollections | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Nein |  Nein |
| searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Nein |  Nein |
| alerts | Nein |  Nein |
| allowedConnections | Nein |  Nein |
| appliances | Nein |  Nein |
| applicationWhitelistings | Nein |  Nein |
| AutoProvisioningSettings | Nein |  Nein |
| Compliances | Nein |  Nein |
| dataCollectionAgents | Nein |  Nein |
| discoveredSecuritySolutions | Nein |  Nein |
| externalSecuritySolutions | Nein |  Nein |
| InformationProtectionPolicies | Nein |  Nein |
| jitNetworkAccessPolicies | Nein |  Nein |
| monitoring | Nein |  Nein |
| monitoring/antimalware | Nein |  Nein |
| monitoring/baseline | Nein |  Nein |
| monitoring/patch | Nein |  Nein |
| Richtlinien | Nein |  Nein |
| pricings | Nein |  Nein |
| securityContacts | Nein |  Nein |
| securitySolutions | Nein |  Nein |
| securitySolutionsReferenceData | Nein |  Nein |
| securityStatus | Nein |  Nein |
| securityStatus/endpoints | Nein |  Nein |
| securityStatus/subnets | Nein |  Nein |
| securityStatus/virtualMachines | Nein |  Nein |
| securityStatuses | Nein |  Nein |
| securityStatusesSummaries | Nein |  Nein |
| settings | Nein |  Nein |
| Tasks | Nein |  Nein |
| topologies | Nein |  Nein |
| workspaceSettings | Nein |  Nein |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nein |  Nein |
| diagnosticSettingsCategories | Nein |  Nein |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| namespaces | Ja | Nein |
| namespaces/authorizationrules | Nein |  Nein |
| namespaces/disasterrecoveryconfigs | Nein |  Nein |
| namespaces/eventgridfilters | Nein |  Nein |
| namespaces/queues | Nein |  Nein |
| namespaces/queues/authorizationrules | Nein |  Nein |
| namespaces/topics | Nein |  Nein |
| namespaces/topics/authorizationrules | Nein |  Nein |
| namespaces/topics/subscriptions | Nein |  Nein |
| namespaces/topics/subscriptions/rules | Nein |  Nein |
| premiumMessagingRegions | Nein |  Nein |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| clusters | Ja | Ja |
| clusters/applications | Nein |  Nein |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| applications | JA | Ja |
| gateways | Ja | JA |
| networks | Ja | Ja |
| secrets | Ja | Ja |
| volumes | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| SignalR | Ja | Ja |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Ja | Ja |
| appliances | Ja | Ja |
| applicationDefinitions | Ja | Ja |
| applications | JA | Ja |
| jitRequests | Ja | Ja |

## <a name="microsoftsql"></a>Microsoft.SQL
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| managedInstances | Ja | Ja |
| managedInstances/databases | Ja (siehe Hinweis unten) | Ja |
| managedInstances/databases/backupShortTermRetentionPolicies | Nein | Nein |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nein | Nein |
| managedInstances/databases/vulnerabilityAssessments | Nein | Nein |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nein | Nein |
| managedInstances/encryptionProtector | Nein | Nein |
| managedInstances/keys | Nein | Nein |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nein | Nein |
| managedInstances/vulnerabilityAssessments | Nein | Nein |
| servers | Ja | Ja |
| servers/administrators | Nein |  Nein |
| servers/communicationLinks | Nein |  Nein |
| servers/databases | Ja (siehe Hinweis unten) | Ja |
| servers/encryptionProtector | Nein |  Nein |
| servers/firewallRules | Nein |  Nein |
| servers/keys | Nein |  Nein |
| servers/restorableDroppedDatabases | Nein |  Nein |
| servers/serviceobjectives | Nein |  Nein |
| servers/tdeCertificates | Nein |  Nein |

> [!NOTE]
> Die Masterdatenbank unterstützt keine Tags, diese werden jedoch in anderen Datenbanken, z.B. Azure SQL Data Warehouse-Datenbanken, unterstützt. Azure SQL Data Warehouse-Datenbanken müssen sich im aktiven (nicht angehaltenen) Zustand befinden.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Ja | Ja |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nein |  Nein |
| SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| storageAccounts | Ja | Ja |
| storageAccounts/blobServices | Nein |  Nein |
| storageAccounts/fileServices | Nein |  Nein |
| storageAccounts/queueServices | Nein |  Nein |
| storageAccounts/services | Nein |  Nein |
| storageAccounts/tableServices | Nein |  Nein |
| usages | Nein |  Nein |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| storageSyncServices | Ja | Ja |
| storageSyncServices/registeredServers | Nein |  Nein |
| storageSyncServices/syncGroups | Nein |  Nein |
| storageSyncServices/syncGroups/cloudEndpoints | Nein |  Nein |
| storageSyncServices/syncGroups/serverEndpoints | Nein |  Nein |
| storageSyncServices/workflows | Nein |  Nein |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| managers | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| streamingjobs | Ja (siehe Hinweis unten) | Ja |
| streamingjobs/diagnosticSettings | Nein |  Nein |

> [!NOTE]
> Sie können kein Tag hinzufügen, wenn Streamingaufträge ausgeführt werden. Beenden Sie die Ressource, um ein Tag hinzuzufügen.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| CreateSubscription | Nein |  Nein |
| SubscriptionDefinitions | Nein |  Nein |
| SubscriptionOperations | Nein |  Nein |

## <a name="microsoftsupport"></a>microsoft.support
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| supporttickets | Nein |  Nein |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| providerRegistrations | Ja | Ja |
| ressourcen | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| environments | Ja | Nein |
| environments/accessPolicies | Nein |  Nein |
| environments/eventsources | Ja | Nein |
| environments/referenceDataSets | Ja | Nein |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| account | JA | Ja |
| account/extension | Ja | Ja |
| account/project | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Nein |  Nein |
| apiManagementAccounts/apiAcls | Nein |  Nein |
| apiManagementAccounts/apis | Nein |  Nein |
| apiManagementAccounts/apis/apiAcls | Nein |  Nein |
| apiManagementAccounts/apis/connectionAcls | Nein |  Nein |
| apiManagementAccounts/apis/connections | Nein |  Nein |
| apiManagementAccounts/apis/connections/connectionAcls | Nein |  Nein |
| apiManagementAccounts/apis/localizedDefinitions | Nein |  Nein |
| apiManagementAccounts/connectionAcls | Nein |  Nein |
| apiManagementAccounts/connections | Nein |  Nein |
| billingMeters | Nein |  Nein |
| certificates | Ja | Ja |
| connectionGateways | Ja | Ja |
| connections | Ja | Ja |
| customApis | Ja | Ja |
| deletedSites | Nein |  Nein |
| functions | Nein |  Nein |
| hostingEnvironments | Ja | Nein |
| hostingEnvironments/multiRolePools | Nein |  Nein |
| hostingEnvironments/multiRolePools/instances | Nein |  Nein |
| hostingEnvironments/workerPools | Nein |  Nein |
| hostingEnvironments/workerPools/instances | Nein |  Nein |
| publishingUsers | Nein |  Nein |
| empfehlungen | Nein |  Nein |
| resourceHealthMetadata | Nein |  Nein |
| runtimes | Nein |  Nein |
| serverFarms | Ja | Ja |
| serverFarms/workers | Nein |  Nein |
| sites | Ja | Ja |
| sites/domainOwnershipIdentifiers | Nein |  Nein |
| sites/hostNameBindings | Nein |  Nein |
| sites/instances | Nein |  Nein |
| sites/instances/extensions | Nein |  Nein |
| sites/premieraddons | Ja | Ja |
| sites/recommendations | Nein |  Nein |
| sites/resourceHealthMetadata | Nein |  Nein |
| sites/slots | Ja | Ja |
| sites/slots/hostNameBindings | Nein |  Nein |
| sites/slots/instances | Nein |  Nein |
| sites/slots/instances/extensions | Nein |  Nein |
| sourceControls | Nein |  Nein |
| validate | Nein |  Nein |
| verifyHostingEnvironmentVnet | Nein |  Nein |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nein |  Nein |
| diagnosticSettingsCategories | Nein |  Nein |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| DeviceServices | Ja | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
| ------------- | ----------- | ----------- |
| components | Nein |  Nein |
| componentsSummary | Nein |  Nein |
| monitorInstances | Nein |  Nein |
| monitorInstancesSummary | Nein |  Nein |
| monitors | Nein |  Nein |
| notificationSettings | Nein |  Nein |

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Anwenden von Tags auf Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](resource-group-using-tags.md).
