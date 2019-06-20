---
title: Migrieren zu präzisem rollenbasiertem Zugriff für Clusterkonfigurationen | Azure HDInsight
description: Informationen zu den Änderungen, die im Rahmen der Migration zum differenzierten rollenbasierten Zugriff für HDInsight-Clusterkonfigurationen erforderlich sind.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1ec4786291d6e2e5be6785e52cf3ab5bb5bbc690
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754534"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrieren zu präzisem rollenbasiertem Zugriff für Clusterkonfigurationen

Wir führen einige wichtige Änderungen ein, um differenzierteren rollenbasierten Zugriff zum Abrufen vertraulicher Informationen zu bieten. Im Rahmen dieser Änderungen müssen Sie **möglicherweise aktiv werden**, wenn Sie eine der [betroffenen Entitäten bzw. eins der betroffenen Szenarien](#am-i-affected-by-these-changes) verwenden.

## <a name="what-is-changing"></a>Was ändert sich?

Bisher konnten Geheimnisse von Clusterbenutzern mit den [RBAC-Rollen](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) „Besitzer“, „Mitwirkender“ oder „Leser“ über die HDInsight-API abgerufen werden, da sie jedem zur Verfügung standen, der die Berechtigung `*/read` besaß.
In Zukunft wird für den Zugriff auf diese Geheimnisse die Berechtigung `Microsoft.HDInsight/clusters/configurations/*` erforderlich sein, d. h., dass Benutzer mit der Rolle „Leser“ nicht mehr darauf zugreifen können. Geheimnisse sind als Werte definiert, mit denen ein Zugriff mit erhöhten Rechten möglich ist, als es die Rolle eines Benutzers gestatten sollte. Dazu zählen Werte wie Clustergateway-HTTP-Anmeldeinformationen, Speicherkontenschlüssel und Datenbankanmeldeinformationen.

Wir führen außerdem eine neue Rolle [HDInsight-Clusteroperator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) ein, die imstande ist, Geheimnisse ohne die Verwaltungsberechtigungen der Rollen „Mitwirkender“ oder „Besitzer“ abzurufen. Zusammenfassung:

| Rolle                                  | Bisher                                                                                       | Zukünftige Entwicklung       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leser                                | - Lesezugriff, einschließlich Geheimnisse                                                                   | - Lesezugriff, **mit Ausnahme von** Geheimnissen |           |   |   |
| HDInsight-Clusteroperator<br>(Neue Rolle) | –                                                                                              | - Lese-/Schreibzugriff, einschließlich Geheimnisse         |   |   |
| Mitwirkender                           | - Lese-/Schreibzugriff, einschließlich Geheimnisse<br>- Erstellen und Verwalten aller Arten von Azure-Ressourcen     | Keine Änderung |
| Owner (Besitzer)                                 | - Lese-/Schreibzugriff, einschließlich Geheimnisse<br>- Vollzugriff auf alle Ressourcen<br>- Delegieren des Zugriffs an andere Personen | Keine Änderung |

Informationen zum Hinzufügen der HDInsight-Clusteroperatorrolle zu einem Benutzer, um ihm/ihr Lese-/Schreibzugriff auf Clustergeheimnisse zu geben, finden Sie im Abschnitt unten, [Hinzufügen der HDInsight-Clusteroperator-Rollenzuweisung zu einem Benutzer](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Bin ich von diesen Änderungen betroffen?

Die folgenden Entitäten und Szenarien sind betroffen:

- [API](#api): Benutzer der Endpunkte `/configurations` oder `/configurations/{configurationName}`.
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code), Version 1.1.1.1 oder niedriger
- [Azure-Toolkit für IntelliJ](#azure-toolkit-for-intellij), Version 3.20.0 oder niedriger
- [Azure Data Lake und Stream Analytics-Tools für Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio), niedriger als Version 2.3.9000.1.
- [Azure-Toolkit für Eclipse](#azure-toolkit-for-eclipse), Version 3.15.0 oder niedriger.
- [SDK für .NET](#sdk-for-net)
    - [Versionen 1.x oder 2.x](#versions-1x-and-2x): Benutzer, die die Methoden `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` oder `DisableHttp` aus der ConfigurationsOperationsExtensions-Klasse verwenden.
    - [Versionen 3.x und höher](#versions-3x-and-up): Benutzer, die die Methoden `Get`, `Update`, `EnableHttp` oder `DisableHttp` aus der `ConfigurationsOperationsExtensions`-Klasse verwenden.
- [SDK für Python](#sdk-for-python): Benutzer, die die Methoden `get` oder `update` aus der `ConfigurationsOperations`-Klasse verwenden.
- [SDK für Java](#sdk-for-java): Benutzer, die die Methoden `update` oder `get` aus der `ConfigurationsInner`-Klasse verwenden.
- [SDK für Go](#sdk-for-go): Benutzer, die die Methoden `Get` oder `Update` aus der `ConfigurationsClient`-Struktur verwenden.
- [Az.HDInsight PowerShell](#azhdinsight-powershell), niedriger als Version 2.0.0.
Die Migrationsschritte für Ihr Szenario finden Sie in den Abschnitten unten (oder verwenden Sie die Links oben).

### <a name="api"></a>API

Die folgenden APIs werden geändert oder als veraltet gekennzeichnet:

- [**GET /configurations/{NamederKonfiguration}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (vertrauliche Informationen entfernt)
    - Bisher zum Abrufen einzelner Konfigurationstypen (einschließlich Geheimnissen) verwendet.
    - Dieser API-Aufruf gibt nun einzelne Konfigurationstypen zurück, lässt aber die Geheimnisse aus. Um alle Konfigurationen einschließlich der Geheimnisse abzurufen, verwenden Sie den neuen Aufruf „POST /configurations“. Um lediglich Gatewayeinstellungen abzurufen, verwenden Sie den neuen Aufruf „POST /getGatewaySettings“.
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (veraltet)
    - Bisher zum Abrufen aller Konfigurationen (einschließlich der Geheimnisse) verwendet
    - Dieser API-Aufruf wird nicht mehr unterstützt. Um zukünftig alle Konfigurationen abzurufen, verwenden Sie den neuen Aufruf „POST /configurations“. Um Konfigurationen abzurufen und dabei sensible Parameter auszulassen, verwenden Sie den Aufruf „GET /configurations/{NamederKonfiguration}“.
- [**POST /configurations/{NamederKonfiguration}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (veraltet)
    - Bisher zum Aktualisieren der Gatewayanmeldeinformationen verwendet.
    - Dieser API-Aufruf wird als veraltet gekennzeichnet und nicht mehr unterstützt. Verwenden Sie stattdessen das neue „POST /updateGatewaySettings“.

Die folgenden APIs wurden als Ersatz hinzugefügt:</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Verwenden Sie diese API, um alle Konfigurationen abzurufen, einschließlich der Geheimnisse.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Verwenden Sie diese API, um Gatewayeinstellungen abzurufen.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Verwenden Sie diese API zum Aktualisieren von Gatewayeinstellungen (Benutzername und/oder Kennwort).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

Wenn Sie Version 1.1.1 oder niedriger verwenden, aktualisieren Sie bitte auf die [neueste Version von Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false), um Unterbrechungen zu vermeiden.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit für IntelliJ

Wenn Sie Version 3.20.0 oder niedriger verwenden, aktualisieren Sie bitte auf die [neueste Version des Azure Toolkit für IntelliJ-Plug-Ins](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij), um Unterbrechungen zu vermeiden.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake und Stream Analytics-Tools für Visual Studio

Aktualisieren Sie auf Version 2.3.9000.1 oder höher von [Azure Data Lake und Stream Analytics-Tools für Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview), um Unterbrechungen zu vermeiden.  Hilfe beim Aktualisieren finden Sie in unserer Dokumentation unter [Aktualisieren von Data Lake Tools für Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure-Toolkit für Eclipse

Wenn Sie Version 3.15.0 oder niedriger verwenden, aktualisieren Sie bitte auf die [neueste Version des Azure-Toolkit für Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse), um Unterbrechungen zu vermeiden.

### <a name="sdk-for-net"></a>SDK für .NET

#### <a name="versions-1x-and-2x"></a>Versionen 1.x und 2.x

Aktualisieren Sie auf [Version 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) des HDInsight SDK für .NET. Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden:

- `ClusterOperationsExtensions.GetClusterConfigurations` gibt **keine sensitiven Parameter** wie Speicherschlüssel (Kernwebsite) oder HTTP-Anmeldeinformationen (Gateway) mehr zurück.
    - Um alle Konfigurationen einschließlich der sensiblen Parameter zurückzugeben, verwenden Sie zukünftig `ClusterOperationsExtensions.ListConfigurations`.  Beachten Sie, dass Benutzer mit der Rolle „Leser“ nicht imstande sind, diese Rolle zu verwenden. Dadurch ist eine differenzierte Kontrolle der Benutzer möglich, die auf sensible Informationen für einen Cluster zugreifen können.
    - Um lediglich HTTP-Gatewayanmeldeinformationen abzurufen, verwenden Sie `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` ist mittlerweile veraltet und wurde durch `ClusterOperationsExtensions.GetGatewaySettings` ersetzt.

- `ClusterOperationsExtensions.ConfigureHttpSettings` ist mittlerweile veraltet und wurde durch `ClusterOperationsExtensions.UpdateGatewaySettings` ersetzt.

- `ConfigurationsOperationsExtensions.EnableHttp` und `DisableHttp` sind jetzt veraltet. HTTP ist jetzt immer aktiviert, sodass diese Methoden nicht mehr benötigt werden.

#### <a name="versions-3x-and-up"></a>Versionen 3.x und höher

Aktualisieren Sie auf [Version 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) oder höher des HDInsight SDK für .NET. Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) gibt **keine sensitiven Parameter** wie Speicherschlüssel (Kernwebsite) oder HTTP-Anmeldeinformationen (Gateway) mehr zurück.
    - Um alle Konfigurationen einschließlich der sensiblen Parameter zurückzugeben, verwenden Sie zukünftig [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet).  Beachten Sie, dass Benutzer mit der Rolle „Leser“ nicht imstande sind, diese Rolle zu verwenden. Dadurch ist eine differenzierte Kontrolle der Benutzer möglich, die auf sensible Informationen für einen Cluster zugreifen können. 
    - Um lediglich HTTP-Gatewayanmeldeinformationen abzurufen, verwenden Sie [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) ist mittlerweile veraltet und wurde durch [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet) ersetzt. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) und [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) sind jetzt veraltet. HTTP ist jetzt immer aktiviert, sodass diese Methoden nicht mehr benötigt werden.

### <a name="sdk-for-python"></a>SDK für Python

Aktualisieren Sie auf [Version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) oder höher des HDInsight SDK für Python. Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) gibt **keine sensitiven Parameter** wie Speicherschlüssel (Kernwebsite) oder HTTP-Anmeldeinformationen (Gateway) mehr zurück.
    - Um alle Konfigurationen einschließlich der sensiblen Parameter zurückzugeben, verwenden Sie zukünftig [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).  Beachten Sie, dass Benutzer mit der Rolle „Leser“ nicht imstande sind, diese Rolle zu verwenden. Dadurch ist eine differenzierte Kontrolle der Benutzer möglich, die auf sensible Informationen für einen Cluster zugreifen können. 
    - Um lediglich HTTP-Gatewayanmeldeinformationen abzurufen, verwenden Sie [`ConfigurationsOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) ist mittlerweile veraltet und wurde durch [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) ersetzt.

### <a name="sdk-for-java"></a>SDK für Java

Aktualisieren Sie auf [Version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) oder höher des HDInsight SDK für Java. Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) gibt **keine sensitiven Parameter** wie Speicherschlüssel (Kernwebsite) oder HTTP-Anmeldeinformationen (Gateway) mehr zurück.
    - Um alle Konfigurationen einschließlich der sensiblen Parameter zurückzugeben, verwenden Sie zukünftig [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable).  Beachten Sie, dass Benutzer mit der Rolle „Leser“ nicht imstande sind, diese Rolle zu verwenden. Dadurch ist eine differenzierte Kontrolle der Benutzer möglich, die auf sensible Informationen für einen Cluster zugreifen können. 
    - Um lediglich HTTP-Gatewayanmeldeinformationen abzurufen, verwenden Sie [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) ist mittlerweile veraltet und wurde durch [`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable) ersetzt.

### <a name="sdk-for-go"></a>SDK für Go

Aktualisieren Sie auf [Version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) oder höher des HDInsight SDK für Go. Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) gibt **keine sensitiven Parameter** wie Speicherschlüssel (Kernwebsite) oder HTTP-Anmeldeinformationen (Gateway) mehr zurück.
    - Um alle Konfigurationen einschließlich der sensiblen Parameter zurückzugeben, verwenden Sie zukünftig [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List).  Beachten Sie, dass Benutzer mit der Rolle „Leser“ nicht imstande sind, diese Rolle zu verwenden. Dadurch ist eine differenzierte Kontrolle der Benutzer möglich, die auf sensible Informationen für einen Cluster zugreifen können. 
    - Um lediglich HTTP-Gatewayanmeldeinformationen abzurufen, verwenden Sie [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) ist mittlerweile veraltet und wurde durch [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) ersetzt.

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Aktualisieren Sie auf [Az PowerShell, Version 2.0.0](https://www.powershellgallery.com/packages/Az) oder höher, um Unterbrechungen zu vermeiden.  Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden.
- `Grant-AzHDInsightHttpServicesAccess` ist mittlerweile veraltet und wurde durch das neue Cmdlet `Set-AzHDInsightGatewayCredential` ersetzt.
- `Get-AzHDInsightJobOutput` wurde aktualisiert, um den detaillierten rollenbasierten Zugriff auf den Speicherschlüssel zu unterstützen.
    - Benutzer mit den Rollen HDInsight-Clusteroperator, -mitwirkender oder -besitzer sind davon nicht betroffen.
    - Benutzer, die nur über die Rolle „Leser“ verfügen, müssen den Parameter `DefaultStorageAccountKey` explizit angeben.
- `Revoke-AzHDInsightHttpServicesAccess` gilt jetzt als veraltet. HTTP ist jetzt immer aktiviert, sodass dieses Cmdlet nicht mehr benötigt wird.
 Weitere Details finden Sie im [az.HDInsight-Migrationsleitfaden](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Hinzufügen der HDInsight-Clusteroperator-Rollenzuweisung zu einem Benutzer

Ein Benutzer mit der Rolle [Mitwirkender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) oder [Besitzer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) kann Benutzern die Rolle [HDInsight-Clusteroperator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) zuweisen, denen Sie Lese-/Schreibzugriff auf sensible HDInsight-Clusterkonfigurationswerte (wie Clustergateway-Anmeldeinformationen und Speicherkontoschlüssel) erteilen möchten.

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Die einfachste Möglichkeit zum Hinzufügen dieser Rollenzuweisung bietet der Befehl `az role assignemnt create` an der Azure-Befehlszeilenschnittstelle.

> [!NOTE]
> Dieser Befehl muss von einem Benutzer mit der Rolle „Mitwirkender“ oder „Besitzer“ ausgeführt werden, da diese Berechtigungen nur von diesen erteilt werden können. `--assignee` ist die E-Mail-Adresse des Benutzers, dem Sie die HDInsight-Clusteroperatorrolle zuweisen möchten.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Gewähren einer Rolle auf Ressourcenebene (Cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Gewähren einer Rolle auf Ressourcengruppenebene

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Gewähren einer Rolle auf Abonnementebene

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Sie können alternativ das Azure-Portal verwenden, um einem Benutzer die HDInsight-Clusteroperator-Rollenzuweisung hinzuzufügen. Weitere Informationen finden Sie in der Dokumentation [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal – Hinzufügen einer Rollenzuweisung](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
