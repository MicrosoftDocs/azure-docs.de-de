---
title: Erweiterungsressourcentypen
description: Listet die Azure-Ressourcentypen auf, mit denen die Funktionen anderer Ressourcentypen erweitert werden.
ms.topic: conceptual
ms.date: 11/14/2020
ms.openlocfilehash: 5561c480dd5a2849588ed2288eb5bcc35fc1446c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658450"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Ressourcentypen, die Funktionen anderer Ressourcen erweitern

Eine Erweiterungsressource ist eine Ressource, die einer anderen Ressource Funktionen hinzufügt. Die Ressourcensperre ist beispielsweise eine Erweiterungsressource. Sie wenden eine Ressourcensperre auf eine andere Ressource an, um zu verhindern, dass sie gelöscht oder geändert wird. Es ist nicht sinnvoll, eine Ressourcensperre allein zu erstellen. Eine Erweiterungsressource wird immer auf eine andere Ressource angewendet.

## <a name="microsoftadvisor"></a>Microsoft.Advisor

- Microsoft.Advisor/configurations
- Microsoft.Advisor/recommendations
- Microsoft.Advisor/suppressions

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

- Microsoft.AlertsManagement/alerts

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft.Authorization/denyAssignments
- Microsoft.Authorization/locks
- Microsoft.Authorization/policyAssignments
- Microsoft.Authorization/policyDefinitions
- Microsoft.Authorization/policyExemptions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Microsoft.Authorization/roleDefinitions

## <a name="microsoftautomanage"></a>Microsoft.Automanage

- Microsoft.Automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft.Billing

- Microsoft.Billing/billingPeriods
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints

## <a name="microsoftconsumption"></a>Microsoft.Consumption

- Microsoft.Consumption/AggregatedCost
- Microsoft.Consumption/Balances
- Microsoft.Consumption/Budgets
- Microsoft.Consumption/Charges
- Microsoft.Consumption/CostTags
- Microsoft.Consumption/credits
- Microsoft.Consumption/events
- Microsoft.Consumption/Forecasts
- Microsoft.Consumption/lots
- Microsoft.Consumption/Marketplaces
- Microsoft.Consumption/Pricesheets
- Microsoft.Consumption/products
- Microsoft.Consumption/ReservationDetails
- Microsoft.Consumption/ReservationRecommendationDetails
- Microsoft.Consumption/ReservationRecommendations
- Microsoft.Consumption/ReservationSummaries
- Microsoft.Consumption/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

- Microsoft.ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

- Microsoft.CostManagement/Alerts
- Microsoft.CostManagement/Budgets
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Exports
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/Insights
- Microsoft.CostManagement/Query
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/Views

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

- Microsoft.CustomProviders/associations

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

- Microsoft.GuestConfiguration/configurationProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software

## <a name="microsoftinsights"></a>microsoft.insights

- microsoft.insights/baseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricbaselines
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topology
- microsoft.insights/transactions

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

- Microsoft.KubernetesConfiguration/extensions
- Microsoft.KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

- Microsoft.Maintenance/applyUpdates
- Microsoft.Maintenance/configurationAssignments
- Microsoft.Maintenance/updates

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

- Microsoft.ManagedIdentity/Identities

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

- Microsoft.OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

- Microsoft.OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

- Microsoft.PolicyInsights/attestations
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/events
- Microsoft.ResourceHealth/impactedResources
- Microsoft.ResourceHealth/notifications

## <a name="microsoftresources"></a>Microsoft.Resources

- Microsoft.Resources/links
- Microsoft.Resources/tags

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments
- Microsoft.Security/Compliances
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/devices
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/iotSensors
- Microsoft.Security/jitPolicies
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

- Microsoft.SecurityInsights/aggregations
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/automationRules
- Microsoft.SecurityInsights/bookmarks
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/entities
- Microsoft.SecurityInsights/incidents
- Microsoft.SecurityInsights/settings
- Microsoft.SecurityInsights/threatIntelligence
- Microsoft.SecurityInsights/watchlists

## <a name="microsoftserialconsoleppe"></a>Microsoft.SerialConsole.PPE

- Microsoft.SerialConsole.PPE/serialPorts

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

- Microsoft.SoftwarePlan/hybridUseBenefits

## <a name="microsoftsupport"></a>microsoft.support

- microsoft.support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

- Microsoft.WorkloadMonitor/components
- Microsoft.WorkloadMonitor/monitorInstances
- Microsoft.WorkloadMonitor/monitors
- Microsoft.WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Nächste Schritte

- Verwenden Sie [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid), um die Ressourcen-ID für eine Erweiterungsressource in einer Azure Resource Manager-Vorlage abzurufen.
- Ein Beispiel für das Erstellen einer Erweiterungsressource in einer Vorlage finden Sie unter [Event Grid-Ereignisabonnements](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
