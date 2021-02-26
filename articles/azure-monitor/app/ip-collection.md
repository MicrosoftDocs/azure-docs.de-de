---
title: Sammlung von Azure Application Insights-IP-Adressen | Microsoft-Dokumentation
description: Grundlegendes zur Handhabung von IP-Adressen und der Geolocation mit Azure Application Insights
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3a8969d5d0df2fdacf78815c6f47c7c6bfa73a37
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521916"
---
# <a name="geolocation-and-ip-address-handling"></a>Geolocation und Verarbeitung von IP-Adressen

In diesem Artikel wird erläutert, wie die Geolocationsuche und die Verarbeitung von IP-Adressen in Application Insights funktionieren. Außerdem erfahren Sie, wie Sie das Standardverhalten ändern können.

## <a name="default-behavior"></a>Standardverhalten

IP-Adressen werden standardmäßig temporär gesammelt, aber nicht in Application Insights gespeichert. Der Standardprozess verläuft wie folgt:

Wenn Telemetriedaten an Azure gesendet werden, wird die IP-Adresse genutzt, um eine Geolocationsuche mithilfe von [GeoLite2 von MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/) durchzuführen. Die Ergebnisse dieser Suche werden verwendet, um die Felder `client_City`, `client_StateOrProvince` und `client_CountryOrRegion` aufzufüllen. Die Adresse wird dann verworfen, und in das `client_IP`-Feld wird `0.0.0.0` geschrieben.

* Browsertelemetrie: Die IP-Adresse des Absenders wird vorübergehend erfasst. Die IP-Adresse wird vom Erfassungsendpunkt berechnet.
* Servertelemetrie: Die Client-IP-Adresse wird vorübergehend vom Application Insights-Telemetriemodul erfasst. IP-Adressen werden lokal nicht erfasst, wenn der `X-Forwarded-For`-Header festgelegt wird.

Dieses Verhalten ist beabsichtigt und soll die unnötige Erfassung persönlicher Daten vermeiden. Die Erfassung persönlicher Daten sollte, wenn möglich, vermieden werden. 

## <a name="overriding-default-behavior"></a>Überschreiben des Standardverhaltens

Laut Standardeinstellung werden IP-Adressen nicht erfasst. Es besteht jedoch weiterhin die Flexibilität, dieses Verhalten zu überschreiben. Sie sollten jedoch darauf achten, dass die Erfassung nicht gegen Complianceanforderungen oder lokale Regelungen verstößt. 

Weitere Informationen zur Behandlung persönlicher Daten in Application Insights finden Sie im [Leitfaden zu persönlichen Daten](../platform/personal-data-mgmt.md).

## <a name="storing-ip-address-data"></a>Speichern von IP-Adressdaten

Die Eigenschaft `DisableIpMasking` der Application Insights-Komponente muss auf `true` festgelegt werden, um das Erfassen und Speichern von IP-Adressen zu aktivieren. Diese Eigenschaft kann entweder durch Azure Resource Manager-Vorlagen oder durch Aufrufen der REST-API festgelegt werden. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Wenn Sie nur das Verhalten für eine einzelne Application Insights-Ressource ändern müssen, verwenden Sie das Azure-Portal. 

1. Navigieren Sie zu Ihrer Application Insights-Ressource > **Automatisierung** > **Vorlage exportieren**. 

2. Klicken Sie auf **Bereitstellen**.

    ![Rot hervorgehobene Schaltfläche mit dem Wort „Bereitstellen“](media/ip-collection/deploy.png)

3. Klicken Sie auf **Vorlage bearbeiten**.

    ![Rot hervorgehobene Schaltfläche mit dem Wort „Bearbeiten“](media/ip-collection/edit-template.png)

4. Nehmen Sie die folgenden Änderungen an der JSON-Datei für Ihre Ressource vor, und klicken Sie dann auf **Speichern**:

    ![Screenshot: Einfügen eines Kommas nach "IbizaAIExtension" und einer neuen Zeile darunter mit "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Wenn eine Fehlermeldung mit folgendem Text angezeigt wird: **_The resource group is in a location that is not supported by one or more resources in the template. Please choose a different resource group._** (Die Ressourcengruppe befindet sich an einem Speicherort, der von keiner Ressource in der Vorlage unterstützt wird. Bitte wählen Sie eine andere Ressourcengruppe). Wählen Sie vorübergehend eine andere Ressourcengruppe aus der Dropdownliste aus, und wählen Sie dann erneut die ursprüngliche Ressourcengruppe aus, um den Fehler zu beheben.

5. Klicken Sie auf **Ich stimme zu** > **Kaufen**. 

    ![Kontrollkästchen mit Häkchen vor der rot hervorgehobenen Schaltfläche „Ich stimme den oben genannten Geschäftsbedingungen zu“ über einer rot hervorgehobenen Schaltfläche mit dem Wort „Kaufen“](media/ip-collection/purchase.png)

    In diesem Fall wird tatsächlich nichts Neues erworben. Es wird lediglich die Konfiguration der vorhandenen Application Insights-Ressource aktualisiert.

6. Sobald die Bereitstellung abgeschlossen ist, werden neue Telemetriedaten aufgezeichnet.

    Wenn Sie die Vorlage noch mal auswählen und bearbeiten, wird nur die Standardvorlage ohne der neu hinzugefügten Eigenschaft angezeigt. Wenn die IP-Adressdaten nicht angezeigt werden und Sie bestätigen möchten, dass `"DisableIpMasking": true` festgelegt ist, führen Sie den folgenden PowerShell-Befehl aus: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Daraufhin wird eine Liste mit den Eigenschaften zurückgegeben. Eine der Eigenschaften sollte `DisableIpMasking: true` beinhalten. Wenn Sie vor dem Bereitstellen der neuen Eigenschaft mit Azure Resource Manager den PowerShell-Befehl ausführen, ist die Eigenschaft nicht vorhanden.

### <a name="rest-api"></a>REST-API

Die [REST-API](/rest/api/azure/)-Payload, um die gleichen Änderungen vorzunehmen, sieht wie folgt aus:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Telemetrieinitialisierer

Wenn Sie eine flexiblere Alternative als `DisableIpMasking` benötigen, können Sie einen [Telemetrieinitialisierer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) verwenden, um die gesamte oder einen Teil der IP-Adresse in ein benutzerdefiniertes Feld zu kopieren. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Wenn Sie auf `ISupportProperties` nicht zugreifen können, stellen Sie sicher, dass Sie die neueste stabile Version des Application Insights SDK ausführen. `ISupportProperties` sind für hohe Kardinalitätswerte gedacht, während `GlobalProperties` für niedrige Kardinalitätswerte wie Regionsname, Umgebungsname usw. besser geeignet sind. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Aktivieren des Telemetrieinitialisierers für ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Aktivieren des Telemetrieinitialisierers für ASP.NET Core

Sie können den Telemetrieinitialisierer für ASP.NET Core auf die gleiche Weise wie für ASP.NET erstellen, müssen zum Aktivieren des Initialisierers aber das folgende Beispiel als Verweis verwenden:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[Clientseitiges JavaScript](#tab/javascript)

### <a name="client-side-javascript"></a>Clientseitiges JavaScript

Im Gegensatz zu serverseitigen SDKs berechnet das clientseitige JavaScript-SDK keine IP-Adresse. Die IP-Adressenberechnung für clientseitige Telemetriedaten wird standardmäßig am Erfassungsendpunkt in Azure ausgeführt. 

Wenn Sie IP-Adressen direkt auf der Clientseite berechnen möchten, müssen Sie eine eigene benutzerdefinierte Logik hinzufügen und das Ergebnis zum Festlegen des Tags `ai.location.ip` verwenden. Wenn `ai.location.ip` festgelegt ist, wird die IP-Adressenberechnung nicht vom Erfassungsendpunkt durchgeführt, und die angegebene IP-Adresse wird zum Durchführen der Geolocationsuche verwendet. In diesem Szenario wird die IP-Adresse weiterhin standardmäßig zurückgesetzt. 

Sie können einen Telemetrieinitialisierer verwenden, der die von Ihnen in `ai.location.ip` bereitgestellten IP-Adressdaten in ein separates benutzerdefiniertes Feld kopiert, um die gesamte von der benutzerdefinierten Logik berechnete IP-Adresse beizubehalten. Im Gegensatz zu den serverseitigen SDKs wiederum wird, ohne sich auf Bibliotheken von Drittanbietern oder eine eigene benutzerdefinierte Sammlungslogik zu verlassen, die IP-Adresse vom clientseitigen SDK nicht berechnet.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

Wenn clientseitige Daten einen Proxy durchlaufen, bevor sie an den Erfassungsendpunkt weitergeleitet werden, könnte die IP-Adressenberechnung die IP-Adresse des Proxys und nicht des Clients anzeigen. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Anzeigen der Ergebnisse des Telemetrieinitialisierers

Senden Sie neuen Datenverkehr an Ihre Website, und warten Sie dann einige Minuten. Führen Sie dann eine Abfrage aus, um zu überprüfen, ob die Erfassung erfolgreich ist:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Die neu erfassten IP-Adressen werden in der Spalte `customDimensions_client-ip` angezeigt. Für die Standardspalte `client-ip` werden die vier Oktette dennoch jeweils mit Nullen besetzt angezeigt. 

Wenn Tests auf dem Localhost ausgeführt werden und der Wert für `customDimensions_client-ip` `::1` lautet, handelt es sich bei diesem Wert um das erwartete Verhalten. `::1` stellt die Loopbackadresse in IPv6 dar. Die Adresse entspricht `127.0.0.1` in IPv4.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [Sammlung persönlicher Daten](../platform/personal-data-mgmt.md) finden Sie in Application Insights.

* Erfahren Sie mehr über die Funktionsweise der [IP-Adressensammlung](https://apmtips.com/posts/2016-07-05-client-ip-address/) in Application Insights. (Dieser Artikel ist ein älterer externer Blogbeitrag von einem unserer Techniker. Zwar wurde der Beitrag vor der Implementierung des aktuellen Standardverhaltens verfasst, bei dem die IP-Adresse als `0.0.0.0` aufgezeichnet wird, allerdings wird die Funktionsweise des integrierten `ClientIpHeaderTelemetryInitializer` ausführlicher erklärt.)
