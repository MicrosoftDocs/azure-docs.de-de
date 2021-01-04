---
title: Präziser rollenbasierter Zugriff – Azure HDInsight-Clusterkonfigurationen
description: Informationen zu den Änderungen, die im Rahmen der Migration zum differenzierten rollenbasierten Zugriff für HDInsight-Clusterkonfigurationen erforderlich sind.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: d2e9c1fe89866511f8eae0b900563471cd6e52e9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533307"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrieren zu präzisem rollenbasiertem Zugriff für Clusterkonfigurationen

Wir führen einige wichtige Änderungen ein, um differenzierteren rollenbasierten Zugriff zum Abrufen vertraulicher Informationen zu bieten. Im Rahmen dieser Änderungen müssen Sie möglicherweise **bis zum 3. September 2019** aktiv werden, wenn Sie eine der [betroffenen Entitäten bzw. eines der betroffenen Szenarien](#am-i-affected-by-these-changes) verwenden.

## <a name="what-is-changing"></a>Was ändert sich?

Bisher konnten Geheimnisse von Clusterbenutzern mit den [Azure-Rollen](../role-based-access-control/rbac-and-directory-admin-roles.md) „Besitzer“, „Mitwirkender“ oder „Leser“ über die HDInsight-API abgerufen werden, da sie für jeden mit der Berechtigung `*/read` verfügbar waren. Geheimnisse sind als Werte definiert, mit denen ein Zugriff mit erhöhten Rechten möglich ist, als es die Rolle eines Benutzers gestatten sollte. Dazu zählen Werte wie Clustergateway-HTTP-Anmeldeinformationen, Speicherkontenschlüssel und Datenbankanmeldeinformationen.

Ab dem 3. September 2019 ist für den Zugriff auf diese Geheimnisse die Berechtigung `Microsoft.HDInsight/clusters/configurations/action` erforderlich, d. h., dass Benutzer mit der Rolle „Leser“ nicht mehr darauf zugreifen können. Die Rollen, die über diese Berechtigung verfügen, sind „Mitwirkender“, „Besitzer“ und die neue Rolle „HDInsight-Clusteroperator“. (Weitere Informationen hierzu finden Sie weiter unten.)

Wir führen außerdem eine neue Rolle ein ([HDInsight-Clusteroperator](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator)), mit der Geheimnisse ohne die Administratorberechtigungen der Rollen „Mitwirkender“ oder „Besitzer“ abgerufen werden können. Zusammenfassung:

| Role                                  | Bisher                                                                                       | Zukünftige Entwicklung       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leser                                | - Lesezugriff, einschließlich Geheimnissen                                                                   | - Lesezugriff, **mit Ausnahme von** Geheimnissen |           |   |   |
| HDInsight-Clusteroperator<br>(Neue Rolle) | –                                                                                              | - Lese-/Schreibzugriff, einschließlich Geheimnisse         |   |   |
| Mitwirkender                           | - Lese-/Schreibzugriff, einschließlich Geheimnissen<br>- Erstellen und Verwalten aller Arten von Azure-Ressourcen<br>- Ausführen von Skriptaktionen     | Keine Änderung |
| Besitzer                                 | - Lese-/Schreibzugriff, einschließlich Geheimnissen<br>- Vollzugriff auf alle Ressourcen<br>- Delegieren des Zugriffs an andere Personen<br>- Ausführen von Skriptaktionen | Keine Änderung |

Informationen dazu, wie Sie einem Benutzer die HDInsight-Clusteroperatorrolle hinzufügen, um ihm Lese-/Schreibzugriff auf Clustergeheimnisse zu geben, finden Sie im nachfolgenden Abschnitt [Hinzufügen der HDInsight-Clusteroperator-Rollenzuweisung zu einem Benutzer](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

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

- [**GET /configurations/{NamederKonfiguration}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (vertrauliche Informationen entfernt)
    - Bisher zum Abrufen einzelner Konfigurationstypen (einschließlich Geheimnissen) verwendet.
    - Ab dem 3. September 2019 gibt dieser API-Aufruf einzelne Konfigurationstypen ohne Angabe der Geheimnisse zurück. Um alle Konfigurationen einschließlich der Geheimnisse abzurufen, verwenden Sie den neuen Aufruf „POST /configurations“. Um lediglich Gatewayeinstellungen abzurufen, verwenden Sie den neuen Aufruf „POST /getGatewaySettings“.
- [**GET /configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (veraltet)
    - Bisher zum Abrufen aller Konfigurationen (einschließlich der Geheimnisse) verwendet
    - Ab dem 3. September 2019 ist dieser API-Aufruf als veraltet gekennzeichnet und wird nicht mehr unterstützt. Um zukünftig alle Konfigurationen abzurufen, verwenden Sie den neuen Aufruf „POST /configurations“. Um Konfigurationen abzurufen und dabei sensible Parameter auszulassen, verwenden Sie den Aufruf „GET /configurations/{NamederKonfiguration}“.
- [**POST /configurations/{NamederKonfiguration}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (veraltet)
    - Bisher zum Aktualisieren der Gatewayanmeldeinformationen verwendet.
    - Ab dem 3. September 2019 ist dieser API-Aufruf als veraltet gekennzeichnet und wird nicht mehr unterstützt. Verwenden Sie stattdessen das neue „POST /updateGatewaySettings“.

Die folgenden APIs wurden als Ersatz hinzugefügt:</span>

- [**POST /configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Verwenden Sie diese API, um alle Konfigurationen abzurufen, einschließlich der Geheimnisse.
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Verwenden Sie diese API, um Gatewayeinstellungen abzurufen.
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Verwenden Sie diese API zum Aktualisieren von Gatewayeinstellungen (Benutzername und/oder Kennwort).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

Wenn Sie Version 1.1.1 oder niedriger verwenden, aktualisieren Sie bitte auf die [neueste Version von Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false), um Unterbrechungen zu vermeiden.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit für IntelliJ

Wenn Sie Version 3.20.0 oder niedriger verwenden, aktualisieren Sie bitte auf die [neueste Version des Azure Toolkit für IntelliJ-Plug-Ins](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij), um Unterbrechungen zu vermeiden.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake und Stream Analytics-Tools für Visual Studio

Aktualisieren Sie auf Version 2.3.9000.1 oder höher von [Azure Data Lake und Stream Analytics-Tools für Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview), um Unterbrechungen zu vermeiden.  Hilfe beim Aktualisieren finden Sie in unserer Dokumentation unter [Aktualisieren von Data Lake Tools für Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio).

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

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet&preserve-view=true) gibt **keine sensitiven Parameter** wie Speicherschlüssel (Kernwebsite) oder HTTP-Anmeldeinformationen (Gateway) mehr zurück.
    - Um alle Konfigurationen einschließlich der sensiblen Parameter zurückzugeben, verwenden Sie zukünftig [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet&preserve-view=true).  Beachten Sie, dass Benutzer mit der Rolle „Leser“ nicht imstande sind, diese Rolle zu verwenden. Dadurch ist eine differenzierte Kontrolle der Benutzer möglich, die auf sensible Informationen für einen Cluster zugreifen können. 
    - Um lediglich HTTP-Gatewayanmeldeinformationen abzurufen, verwenden Sie [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet&preserve-view=true). 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet&preserve-view=true) ist mittlerweile veraltet und wurde durch [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet&preserve-view=true) ersetzt. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet&preserve-view=true) und [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet&preserve-view=true) sind jetzt veraltet. HTTP ist jetzt immer aktiviert, sodass diese Methoden nicht mehr benötigt werden.

### <a name="sdk-for-python"></a>SDK für Python

Aktualisieren Sie auf [Version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) oder höher des HDInsight SDK für Python. Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) gibt **keine sensitiven Parameter** wie Speicherschlüssel (Kernwebsite) oder HTTP-Anmeldeinformationen (Gateway) mehr zurück.
    - Um alle Konfigurationen einschließlich der sensiblen Parameter zurückzugeben, verwenden Sie zukünftig [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).  Beachten Sie, dass Benutzer mit der Rolle „Leser“ nicht imstande sind, diese Rolle zu verwenden. Dadurch ist eine differenzierte Kontrolle der Benutzer möglich, die auf sensible Informationen für einen Cluster zugreifen können. 
    - Um lediglich HTTP-Gatewayanmeldeinformationen abzurufen, verwenden Sie [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) ist mittlerweile veraltet und wurde durch [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) ersetzt.

### <a name="sdk-for-java"></a>SDK für Java

Aktualisieren Sie auf [Version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) oder höher des HDInsight SDK für Java. Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden:

- `ConfigurationsInner.get` gibt **keine sensitiven Parameter** wie Speicherschlüssel (Kernwebsite) oder HTTP-Anmeldeinformationen (Gateway) mehr zurück.
- `ConfigurationsInner.update` gilt jetzt als veraltet.

### <a name="sdk-for-go"></a>SDK für Go

Aktualisieren Sie auf [Version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) oder höher des HDInsight SDK für Go. Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) gibt **keine sensitiven Parameter** wie Speicherschlüssel (Kernwebsite) oder HTTP-Anmeldeinformationen (Gateway) mehr zurück.
    - Um alle Konfigurationen einschließlich der sensiblen Parameter zurückzugeben, verwenden Sie zukünftig [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List).  Beachten Sie, dass Benutzer mit der Rolle „Leser“ nicht imstande sind, diese Rolle zu verwenden. Dadurch ist eine differenzierte Kontrolle der Benutzer möglich, die auf sensible Informationen für einen Cluster zugreifen können. 
    - Um lediglich HTTP-Gatewayanmeldeinformationen abzurufen, verwenden Sie [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) ist mittlerweile veraltet und wurde durch [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) ersetzt.

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Aktualisieren Sie auf [Az PowerShell, Version 2.0.0](https://www.powershellgallery.com/packages/Az) oder höher, um Unterbrechungen zu vermeiden.  Möglicherweise sind minimale Codeänderungen erforderlich, wenn Sie eine von diesen Änderungen betroffene Methode verwenden.
- `Grant-AzHDInsightHttpServicesAccess` ist mittlerweile veraltet und wurde durch das neue Cmdlet `Set-AzHDInsightGatewayCredential` ersetzt.
- `Get-AzHDInsightJobOutput` wurde aktualisiert, um den detaillierten rollenbasierten Zugriff auf den Speicherschlüssel zu unterstützen.
    - Benutzer mit den Rollen HDInsight-Clusteroperator, -mitwirkender oder -besitzer sind davon nicht betroffen.
    - Benutzer, die nur über die Rolle „Leser“ verfügen, müssen den Parameter `DefaultStorageAccountKey` explizit angeben.
- `Revoke-AzHDInsightHttpServicesAccess` gilt jetzt als veraltet. HTTP ist jetzt immer aktiviert, sodass dieses Cmdlet nicht mehr benötigt wird.
 Weitere Details finden Sie im [az.HDInsight-Migrationsleitfaden](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Hinzufügen der HDInsight-Clusteroperator-Rollenzuweisung zu einem Benutzer

Ein Benutzer mit der Rolle [Besitzer](../role-based-access-control/built-in-roles.md#owner) kann Benutzern, die Lese-/Schreibzugriff auf sensible HDInsight-Clusterkonfigurationswerte (wie Anmeldeinformationen für das Clustergateway und Speicherkontoschlüssel) benötigen, die Rolle [HDInsight-Clusteroperator](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) zuweisen.

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Die einfachste Möglichkeit zum Hinzufügen dieser Rollenzuweisung bietet der Befehl `az role assignment create` an der Azure-Befehlszeilenschnittstelle.

> [!NOTE]
> Dieser Befehl muss von einem Benutzer mit der Rolle „Besitzer“ ausgeführt werden, da diese Berechtigungen nur von einem solchen Benutzer erteilt werden können. `--assignee` ist der Name des Dienstprinzipals oder die E-Mail-Adresse des Benutzers, dem Sie die Rolle „HDInsight-Clusteroperator“ zuweisen möchten. Sollte ein Fehler aufgrund unzureichender Berechtigungen auftreten, lesen Sie die häufig gestellten Fragen weiter unten.

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

Sie können alternativ das Azure-Portal verwenden, um einem Benutzer die HDInsight-Clusteroperator-Rollenzuweisung hinzuzufügen. Weitere Informationen finden Sie in der Dokumentation unter [„Hinzufügen einer Rollenzuweisung“ im Artikel „Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal“](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment).

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Warum erhalte ich nach dem Aktualisieren meiner API-Anforderungen und/oder meines Tools eine Antwort vom Typ „403 (Verboten)“?

Clusterkonfigurationen befinden sind jetzt hinter einer differenzierten rollenbasierten Zugriffssteuerung, und für den Zugriff wird die Berechtigung `Microsoft.HDInsight/clusters/configurations/*` benötigt. Weisen Sie dem Benutzer oder Dienstprinzipal, der auf Konfigurationen zugreifen möchte, die Rolle „HDInsight-Clusteroperator“, „Mitwirkender“ oder „Besitzer“ zu, um ihm diese Berechtigung zu erteilen.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Warum wird die Meldung „Nicht genügend Berechtigungen zum Abschließen des Vorgangs.“ angezeigt, wenn ich versuche, einem anderen Benutzer oder Dienstprinzipal mithilfe des entsprechenden Azure CLI-Befehls die Rolle „HDInsight-Clusteroperator“ zuzuweisen?

Der Benutzer oder Dienstprinzipal, der den Befehl ausführt, muss neben der Rolle „Besitzer“ auch über ausreichende Azure AD-Berechtigungen verfügen, um nach den Objekt-IDs der zugewiesenen Person zu suchen. Diese Meldung deutet auf unzureichende Azure AD-Berechtigungen hin. Ersetzen Sie das Argument `-–assignee` durch `–assignee-object-id`, und geben Sie als Parameter anstelle des Namens die Objekt-ID der zugewiesenen Person (oder im Falle einer verwalteten Identität: die Prinzipal-ID) an. Weitere Informationen finden Sie in der [Dokumentation zu „az role assignment create“](/cli/azure/role/assignment#az-role-assignment-create) im Abschnitt zu optionalen Parametern.

Sollte das Problem weiterhin bestehen, wenden Sie sich an Ihren Azure AD-Administrator, um die korrekten Berechtigungen zu erhalten.

### <a name="what-will-happen-if-i-take-no-action"></a>Was passiert, wenn ich nichts unternehme?

Ab dem 3. September 2019 werden für einen Aufruf von `GET /configurations` oder `POST /configurations/gateway` keine Informationen mehr zurückgegeben, und der Aufruf von `GET /configurations/{configurationName}` gibt keine vertraulichen Parameter wie Speicherkontoschlüssel oder das Clusterkennwort mehr zurück. Gleiches gilt für die entsprechenden SDK-Methoden und PowerShell-Cmdlets.

Wenn Sie eine ältere Version eines der oben erwähnten Tools für Visual Studio, VSCode, IntelliJ oder Eclipse verwenden, funktionieren diese erst nach einem Update wieder.

Ausführlichere Informationen finden Sie im entsprechenden Abschnitt dieses Dokuments für Ihr Szenario.