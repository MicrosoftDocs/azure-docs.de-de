---
title: Integrieren von API Management in Service Fabric in Azure
description: Lernen Sie die ersten Schritte mit Azure API Management und dem Weiterleiten von Datenverkehr an einen Back-End-Dienst in Service Fabric kennen.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 61a13d80fd67751d77bc77199fa433143ad92048
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007102"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>Integrieren von API Management in Service Fabric in Azure

Bei der Bereitstellung von Azure API Management mit Service Fabric handelt es sich um ein erweitertes Szenario.  API Management ist nützlich, wenn Sie APIs mit einem umfangreichen Satz von Routingregeln für Ihre Service Fabric-Back-End-Dienste veröffentlichen möchten. Cloudanwendungen benötigen normalerweise ein Front-End-Gateway, um für Benutzer, Geräte oder andere Anwendungen einen zentralen Eingangspunkt bereitzustellen. In Service Fabric kann ein Gateway ein beliebiger zustandsloser Dienst sein, der für den Eingang von Datenverkehr ausgelegt ist, z.B. eine ASP.NET Core-Anwendung, Event Hubs, IoT Hub oder Azure API Management.

In diesem Artikel wird beschrieben, wie Sie [Azure API Management](../api-management/api-management-key-concepts.md) mit Service Fabric einrichten, um Datenverkehr an einen Back-End-Dienst in Service Fabric weiterzuleiten.  Am Ende des Tutorials haben Sie API Management für ein VNET bereitgestellt und einen API-Vorgang zum Senden von Datenverkehr an zustandslose Back-End-Dienste konfiguriert. Weitere Informationen zu den Azure API Management-Szenarios mit Service Fabric finden Sie im [Übersichtsartikel](service-fabric-api-management-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability"></a>Verfügbarkeit

> [!IMPORTANT]
> Dieses Feature ist auf den Ebenen **Premium** und **Entwickler** von API Management aufgrund der erforderlichen Unterstützung virtueller Netzwerke verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Vorbereitungen

* Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps) oder die [Azure CLI](/cli/azure/install-azure-cli).
* Erstellen Sie einen sicheren [Windows-Cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) in einer Netzwerksicherheitsgruppe.
* Wenn Sie einen Windows-Cluster bereitstellen, richten Sie eine Windows-Entwicklungsumgebung ein. Installieren Sie [Visual Studio 2019](https://www.visualstudio.com) und die Workloads **Azure-Entwicklung**, **ASP.NET und Webentwicklung** und **Plattformübergreifende .NET Core-Entwicklung**.  Richten Sie dann eine [.NET-Entwicklungsumgebung](service-fabric-get-started.md) ein.

## <a name="network-topology"></a>Netzwerktopologie

Nach dem Erstellen eines sicheren [Windows-Clusters](service-fabric-tutorial-create-vnet-and-windows-cluster.md) in Azure stellen Sie API Management für das virtuelle Netzwerk (VNET) in dem Subnetz und der Netzwerksicherheitsgruppe bereit, die für API Management festgelegt wurden. Für diesen Artikel wurde die Resource Manager-Vorlage für API Management vorkonfiguriert und verwendet die Namen des VNETs, des Subnetzes und der Netzwerksicherheitsgruppe, das bzw. die Sie im [Tutorial zu Windows-Clustern](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eingerichtet haben. In diesem Artikel wird die folgende Topologie in Azure bereitgestellt, in der API Management und Service Fabric sich in Subnetzen des gleichen virtuellen Netzwerks befinden:

 ![Bildunterschrift][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Melden Sie sich bei Azure an, und wählen Sie Ihr Abonnement aus

Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie Ihr Abonnement aus, bevor Sie Azure-Befehle ausführen.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Bereitstellen eines Service Fabric-Back-End-Diensts

Vor dem Konfigurieren von API Management für die Weiterleitung von Datenverkehr an einen Service Fabric-Back-End-Dienst benötigen Sie zunächst einen ausgeführten Dienst zum Akzeptieren von Anforderungen.  

Erstellen Sie mithilfe der Standardvorlage für Web-API-Projekte einen einfachen zustandslosen zuverlässigen ASP.NET Core-Dienst. Dadurch wird ein HTTP-Endpunkt für den Dienst erstellt, den Sie über Azure API Management verfügbar machen.

Starten Sie Visual Studio als Administrator, und erstellen Sie einen ASP.NET Core-Dienst:

 1. Wählen Sie in Visual Studio "Datei -> Neues Projekt" aus.
 2. Wählen Sie die Service Fabric-Anwendungsvorlage unter Cloud aus, und benennen Sie sie **„ApiApplication“** .
 3. Wählen Sie die zustandslose ASP.NET Core-Dienstvorlage aus, und nennen Sie das Projekt **„WebApiService“** .
 4. Wählen Sie die Web-API ASP.NET Core 2.1-Projektvorlage aus.
 5. Nachdem das Projekt erstellt wurde, öffnen Sie die `PackageRoot\ServiceManifest.xml`, und entfernen Sie das `Port`-Attribut aus der Endpunktressourcenkonfiguration:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Das Entfernen des Ports ermöglicht Service Fabric, einen Port dynamisch aus dem Portbereich für Anwendungen anzugeben, der über die Netzwerksicherheitsgruppe in der Resource Manager-Vorlage des Clusters geöffnet wird, und ermöglicht so den Fluss von Datenverkehr aus API Management.

 6. Drücken Sie in Visual Studio F5, um zu überprüfen, ob die Web-API lokal verfügbar ist.

    Öffnen Sie den Service Fabric Explorer, und führen Sie einen Drilldown zu einer bestimmten Instanz des ASP.NET Core-Diensts aus, um die Basisadresse herauszufinden, die durch den Dienst überwacht wird. Fügen Sie `/api/values` an die Basisadresse an, und öffnen Sie diese Adresse in einem Browser. Dadurch wird die Get-Methode für den ValuesController in der Web-API-Vorlage aufgerufen. Hierbei wird die von der Vorlage angegebene Standardantwort zurückgegeben, nämlich ein JSON-Array, das zwei Zeichenfolgen enthält:

    ```json
    ["value1", "value2"]`
    ```

    Dies ist der Endpunkt, den Sie über API Management in Azure verfügbar machen.

 7. Stellen Sie schließlich die Anwendung auf Ihrem Cluster in Azure bereit. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Anwendungsprojekt, und wählen Sie **Veröffentlichen** aus. Geben Sie Ihren Clusterendpunkt (z.B. `mycluster.southcentralus.cloudapp.azure.com:19000`) an, um die Anwendung in Ihrem Service Fabric-Cluster in Azure bereitzustellen.

Ein zustandsloser ASP.NET Core-Dienst mit dem Namen `fabric:/ApiApplication/WebApiService` sollte jetzt in Ihrem Service Fabric-Cluster in Azure ausgeführt werden.

## <a name="download-and-understand-the-resource-manager-templates"></a>Herunterladen und Nachvollziehen der Resource Manager-Vorlagen

Laden Sie die folgenden Resource Manager-Vorlagen und die Parameterdatei herunter, und speichern Sie sie:

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

Die Vorlage *network-apim.json* stellt ein neues Subnetz und eine neue Netzwerksicherheitsgruppe in dem virtuellen Netzwerk bereit, in dem der Service Fabric-Cluster bereitgestellt wurde.

In den folgenden Abschnitten werden die Ressourcen beschrieben, die durch die Vorlage *apim.json* definiert werden. Weitere Informationen finden Sie über die Links zur Referenzdokumentation der jeweiligen Vorlage in den einzelnen Abschnitten. Die in der Parameterdatei *apim.parameters.json* definierten konfigurierbaren Parameter werden weiter unten in diesem Artikel festgelegt.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) beschreibt die API Management-Dienstinstanz: Name, SKU oder Ebene, Speicherort der Ressourcengruppe, Herausgeberinformationen und virtuelles Netzwerk.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) konfiguriert die API Management-Sicherheit. API Management muss bei Ihrem Service Fabric-Cluster mit Clientzertifikaten, die über Zugriff auf den Cluster verfügen, für die Dienstermittlung authentifiziert werden. In diesem Artikel wird dasselbe Zertifikat verwendet, das zuvor beim Erstellen des [Windows-Clusters](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) angegeben wurde und das standardmäßig für den Zugriff auf Ihren Cluster verwendet werden kann.

In diesem Artikel wird dieses Zertifikat für die Clientauthentifizierung und die Knoten-zu-Knoten-Sicherheit im Cluster verwendet. Sie können ein separates Clientzertifikat verwenden, wenn Sie den Zugriff auf Ihr Service Fabric-Cluster konfiguriert haben. Geben Sie den **Namen**, das **Kennwort** und die **Daten** (Base64-codierte Zeichenfolge) der privaten Schlüsseldatei (.pfx) des Clusterzertifikats an, die Sie beim Erstellen des Service Fabric-Clusters angegeben haben.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) beschreibt den Back-End-Dienst, an den Datenverkehr weitergeleitet wird.

Bei Service Fabric-Back-Ends dient der Service Fabric-Cluster anstatt eines bestimmten Service Fabric-Diensts als Back-End. Dies ermöglicht einer einzelnen Richtlinie das Weiterleiten an mehr als einen Dienst im Cluster. Das hier angegebene Feld **url** ist ein vollqualifizierter Dienstname eines Diensts in Ihrem Cluster, an den standardmäßig alle Anforderungen weitergeleitet werden, wenn in einer Back-End-Richtlinie kein Dienstname angegeben ist. Sie können einen fingierten Dienstnamen wie z.B. „fabric:/fake/service“ verwenden, wenn Sie keinen Fallbackdienst einsetzen möchten. **resourceId** gibt den Verwaltungsendpunkt des Clusters an.  **clientCertificateThumbprint** und **serverCertificateThumbprints** identifizieren die zur Authentifizierung mit dem Cluster verwendeten Zertifikate.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) erstellt ein Produkt. Produkte in Azure API Management enthalten eine oder mehrere APIs sowie ein Nutzungskontingent und Nutzungsbedingungen. Nachdem ein Produkt veröffentlicht wurde, können Entwickler das Produkt abonnieren und die APIs des Produkts verwenden.

Geben Sie für **displayName** einen beschreibenden Anzeigenamen und für **description** eine Beschreibung für das Produkt ein. Für diesen Artikel ist ein Abonnement, aber keine Abonnementgenehmigung durch einen Administrator erforderlich.  Der unter **state** angegebene Zustand dieses Produkts lautet „veröffentlicht“ und ist für Abonnenten sichtbar.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) erstellt eine API. Eine API in API Management besteht aus einem Satz von Operationen, die von Clientanwendungen aufgerufen werden können. Nachdem Sie die Operationen hinzugefügt haben, wird die API zu einem Produkt hinzugefügt und kann veröffentlicht werden. Nach der Veröffentlichung der API können Entwickler diese abonnieren und verwenden.

* **displayName** kann ein beliebiger Name für die API sein. Verwenden Sie für diesen Artikel „Service Fabric App“.
* **name** enthält einen eindeutigen und beschreibenden Namen für die API, z.B. „service-fabric-app“. Dieser Name wird in den Entwickler- und Herausgeberportalen angezeigt.
* **serviceUrl** verweist auf den HTTP-Dienst, der die API implementiert. API Management leitet Anfragen an diese Adresse weiter. Bei Service Fabric-Back-Ends wird dieser URL-Wert nicht verwendet. Sie können hier einen beliebigen Wert einfügen. Für diesen Artikel z. B. „http:\//servicefabric“.
* **path** wird an die Basis-URL für den API Management-Dienst angefügt. Alle in einer API Management-Dienstinstanz gehosteten APIs teilen sich dieselbe Basis-URL. API Management unterscheidet APIs durch deren Suffix. Daher muss jede API eines bestimmten Herausgebers ein eindeutiges Suffix haben.
* **protocols** legt fest, welche Protokolle für den Zugriff auf die API verwendet werden können. Geben Sie für diesen Artikel **http** und **https** an.
* **path** ist ein Suffix für die API. Verwenden Sie für diesen Artikel „myapp“.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations): Damit eine API in API Management verwendet werden kann, müssen ihr Vorgänge hinzugefügt werden.  Externe Clients verwenden einen Vorgang zur Kommunikation mit dem im Service Fabric-Cluster ausgeführten zustandslosen ASP.NET Core-Dienst.

Geben Sie zum Hinzufügen eines Vorgangs für eine Front-End-API folgende Werte an:

* **displayName** und **description** beschreiben den Vorgang. Verwenden Sie für diesen Artikel „Values“.
* **method** gibt das HTTP-Verb an.  Legen Sie für diesen Artikel **GET** fest.
* **urlTemplate** wird an die Basis-URL der API angefügt und identifiziert einen einzelnen HTTP-Vorgang.  Verwenden Sie für diesen Artikel `/api/values`, wenn Sie den .NET-Back-End-Dienst hinzugefügt haben, oder `getMessage`, wenn Sie den Java-Back-End-Dienst hinzugefügt haben.  Standardmäßig ist der hier angegebene URL-Pfad der URL-Pfad, der an den Back-End-Service Fabric-Dienst gesendet wird. Wenn Sie hier den gleichen URL-Pfad verwenden, den Ihr Dienst verwendet, z.B. „/api/values“, wird der Vorgang ohne weitere Änderung ausgeführt. Sie können hier auch einen URL-Pfad angeben, der sich von dem von Ihrem Service Fabric-Back-End-Dienst verwendeten URL-Pfad unterscheidet. In diesem Fall müssen Sie später in Ihrer Vorgangsrichtlinie auch ein Pfad-Rewrite angeben.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) erstellt eine Back-End-Richtlinie, die alles verbindet. Hier konfigurieren Sie den Back-End-Service Fabric-Dienst, an den Anforderungen weitergeleitet werden. Sie können diese Richtlinie auf alle API-Vorgänge anwenden.  Weitere Informationen finden Sie in der [Übersicht über Richtlinien](../api-management/api-management-howto-policies.md).

Die [Back-End-Konfiguration für Service Fabric](../api-management/api-management-transformation-policies.md#SetBackendService) bietet die folgenden Anforderungsrouting-Steuerelemente:

* Dienstinstanzauswahl durch Angabe eines Service Fabric-Dienst-Instanznamens entweder hartcodiert (z.B. `"fabric:/myapp/myservice"`) oder aus der HTTP-Anforderung generiert (z.B. `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
* Lösung der Partition durch Generieren eines Partitionsschlüssels mithilfe eines Service Fabric-Partitionierungsschemas.
* Replikatauswahl für zustandsbehaftete Dienste.
* Retry-Bedingungen der Lösung, mit denen Sie die Bedingungen für das erneute Beheben eines Dienstspeicherorts und erneutes Senden einer Anforderung festlegen können.

**policyContent** ist der mit Escapezeichen versehene Json-XML-Inhalt der Richtlinie.  Erstellen Sie für diesen Artikel eine Back-End-Richtlinie, um Anforderungen direkt an den zuvor bereitgestellten zustandslosen .NET- oder Java-Dienst weiterzuleiten. Fügen Sie eine `set-backend-service`-Richtlinie unter „inbound policies“ hinzu.  Ersetzen Sie in den Wert *sf-service-instance-name* durch `fabric:/ApiApplication/WebApiService`, wenn Sie zuvor den .NET-Back-End-Dienst bereitgestellt haben, oder durch `fabric:/EchoServerApplication/EchoServerService`, wenn Sie den Java-Dienst bereitgestellt haben.  *backend-id* verweist auf eine Back-End-Ressource (in diesem Fall auf die in der Vorlage *apim.json* definierte Ressource `Microsoft.ApiManagement/service/backends`). *backend-id* kann auch auf eine andere Back-End-Ressource verweisen, die mithilfe der API Management-APIs erstellt wurde. Legen Sie *backend-id* für diesen Artikel auf den Wert des Parameters *service_fabric_backend_name* fest.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Einen vollständigen Satz von Attributen für die Service Fabric-Back-End-Richtlinie finden Sie in der [API Management-Back-End-Dokumentation](../api-management/api-management-transformation-policies.md#SetBackendService)

## <a name="set-parameters-and-deploy-api-management"></a>Festlegen von Parametern und Bereitstellen von API Management

Geben Sie für die Bereitstellung die folgenden leeren Parameter in der Datei *apim.parameters.json* an.

|Parameter|value|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Entwickler|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;Base64-codierte Zeichenfolge&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|`https://mysfcluster.southcentralus.cloudapp.azure.com:19080`|
|inbound_policy|&lt;XML-Zeichenfolge&gt;|

*certificatePassword* und *serviceFabricCertificateThumbprint* müssen mit dem zum Einrichten des Clusters verwendeten Clusterzertifikat übereinstimmen.

*serviceFabricCertificate* ist das Zertifikat als Base64-codierte Zeichenfolge, die mithilfe des folgenden Skripts generiert werden kann:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

Ersetzen Sie in *inbound_policy* den Wert *sf-service-instance-name* durch `fabric:/ApiApplication/WebApiService`, wenn Sie zuvor den .NET-Back-End-Dienst bereitgestellt haben, oder durch `fabric:/EchoServerApplication/EchoServerService`, wenn Sie den Java-Dienst bereitgestellt haben. *backend-id* verweist auf eine Back-End-Ressource (in diesem Fall auf die in der Vorlage *apim.json* definierte Ressource `Microsoft.ApiManagement/service/backends`). *backend-id* kann auch auf eine andere Back-End-Ressource verweisen, die mithilfe der API Management-APIs erstellt wurde. Legen Sie *backend-id* für diesen Artikel auf den Wert des Parameters *service_fabric_backend_name* fest.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Verwenden Sie das folgende Skript, um die Resource Manager-Vorlagendatei und die Parameterdatei für API Management bereitzustellen:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>Testen

Sie können jetzt versuchen, eine Anforderung an den Back-End-Dienst in Service Fabric über API Management direkt im [Azure-Portal](https://portal.azure.com) zu senden.

 1. Wählen Sie im API Management-Dienst die Option **API** aus.
 2. Wählen Sie in der in den vorherigen Schritten erstellten **Service Fabric-App**-API die Registerkarte **Test** und dann den Vorgang **Values** aus.
 3. Klicken Sie auf die Schaltfläche **Senden**, um eine Testanforderung an den Back-End-Dienst zu senden.  Daraufhin sollte eine HTTP-Antwort ähnlich der folgenden angezeigt werden:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Ein Cluster besteht neben der Clusterressource selbst noch aus weiteren Azure-Ressourcen. Die einfachste Möglichkeit zum Löschen des Clusters und aller darin genutzten Ressourcen besteht darin, die Ressourcengruppe zu löschen.

Melden Sie sich bei Azure an, und wählen Sie die Abonnement-ID aus, mit der Sie den Cluster entfernen möchten.  Sie finden Ihre Abonnement-ID, indem Sie sich beim [Azure-Portal](https://portal.azure.com) anmelden. Löschen Sie die Ressourcengruppe und alle Clusterressourcen mit dem [Remove-AzResourceGroup-Cmdlet](/en-us/powershell/module/az.resources/remove-azresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Verwendung von [API Management](../api-management/import-and-publish.md).

[azure-powershell]: /powershell/azure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
vice-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.jsonn

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
