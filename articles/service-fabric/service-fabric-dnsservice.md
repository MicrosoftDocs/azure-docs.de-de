---
title: DNS-Dienst in Azure Service Fabric | Microsoft-Dokumentation
description: Verwenden Sie den DNS-Dienst von Azure Service Fabric zum Ermitteln von Microservices aus dem Cluster heraus.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/20/2018
ms.author: aljo
ms.openlocfilehash: 3b3262eadc732c23000a66f24aaeeed4d9794db0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60947655"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS-Dienst in Azure Service Fabric
Der DNS-Dienst ist ein optionaler Systemdienst, den Sie in Ihrem Cluster aktivieren können, um andere Dienste mithilfe des DNS-Protokolls zu ermitteln. 

Viele Dienste, insbesondere Containerdienste, können über eine bereits vorhandene URL aufgerufen werden. Es ist wünschenswert, diese Dienste mithilfe des standardmäßigen DNS-Protokolls auflösen zu können, anstatt das Service Fabric Naming Service-Protokoll zu verwenden. Mit dem DNS-Dienst können Sie einem Dienstnamen DNS-Namen zuordnen und so Endpunkt-IP-Adressen auflösen. Diese Funktion hält die Portabilität von Containerdiensten über verschiedene Plattformen hinweg aufrecht und kann „Lift & Shift“-Szenarien vereinfachen, da Sie vorhandene Dienst-URLs verwenden können und keinen Code umschreiben müssen, um den Naming Service zu nutzen. 

Der DNS-Dienst ordnet DNS-Namen den Dienstnamen zu, die wiederum vom Naming Service aufgelöst werden, um den Dienstendpunkt zurückzugeben. Der DNS-Name für den Dienst wird zum Zeitpunkt der Erstellung bereitgestellt. Das folgende Diagramm zeigt, wie der DNS-Dienst für zustandslose Dienste funktioniert.

![Dienstendpunkte](./media/service-fabric-dnsservice/stateless-dns.png)

Ab Service Fabric-Version 6.3 wurde das Service Fabric-DNS-Protokoll erweitert und umfasst ein Schema für die Adressierung partitionierter zustandsbehafteter Dienste. Mit diesen Erweiterungen können Sie die IP-Adressen bestimmter Partitionen mithilfe einer Kombination aus dem DNS-Namen des zustandsbehafteten Diensts und dem Partitionsnamen auflösen. Alle drei Partitionsschemas werden unterstützt:

- Namenspartitionierung
- Bereichspartitionierung
- Singleton-Partitionierung

Das folgende Diagramm zeigt, wie der DNS-Dienst für partitionierte zustandsbehaftete Dienste funktioniert.

![Endpunkte für zustandsbehafteten Dienst](./media/service-fabric-dnsservice/stateful-dns.png)

Dynamische Ports werden vom DNS-Dienst nicht unterstützt. Verwenden Sie zum Auflösen der Dienste, die über dynamische Ports verfügbar gemacht werden, den [Reverseproxydienst](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Aktivieren des DNS-Diensts
> [!NOTE]
> Der DNS-Dienst für Service Fabric-Dienste wird unter Linux noch nicht unterstützt.

Wenn Sie einen Cluster mithilfe des Portals erstellen, ist der DNS-Dienst im Kontrollkästchen **DNS-Dienst einschließen** im Menü **Clusterkonfiguration** standardmäßig aktiviert:

![Aktivieren des DNS-Diensts über das Portal](./media/service-fabric-dnsservice/enable-dns-service.png)

Wenn Sie nicht das Portal verwenden, um Ihren Cluster zu erstellen, oder wenn Sie einen vorhandenen Cluster aktualisieren, müssen Sie den DNS-Dienst in einer Vorlage aktivieren:

- Sie können für das Bereitstellen eines neuen Clusters die [Beispielvorlagen](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) verwenden oder selbst eine Resource Manager-Vorlage erstellen. 
- Zum Aktualisieren eines vorhandenen Clusters können Sie im Portal zur Ressourcengruppe des Clusters navigieren und auf **Automatisierungsskript** klicken, um eine Vorlage zu verwenden, die den aktuellen Zustand des Clusters und anderer Ressourcen in der Gruppe widerspiegelt. Weitere Informationen finden Sie unter [Exportieren der Vorlage aus der Ressourcengruppe](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

Nachdem Sie die Vorlage erstellt haben, aktivieren Sie den DNS-Dienst mit den folgenden Schritten:

1. Überprüfen Sie, ob die `apiversion` für die Ressource `Microsoft.ServiceFabric/clusters` auf `2017-07-01-preview` oder höher festgelegt ist. Wenn nicht, aktualisieren Sie die Ressource, wie im folgenden Beispiel gezeigt:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Aktivieren Sie jetzt den DNS-Dienst mit einer der folgenden Methoden:

   - Um den DNS-Dienst mit Standardeinstellungen zu aktivieren, fügen Sie ihn zum `addonFeatures`-Abschnitt innerhalb des `properties`-Abschnitts hinzu, wie im folgenden Beispiel gezeigt:

       ```json
           "properties": {
              ...

              "addonFeatures": [
                "DnsService"
              ],
              ...
           }
       ```
   - Um den Dienst mit nicht standardmäßigen Einstellungen zu aktivieren, fügen Sie dem `fabricSettings`-Abschnitt innerhalb des `properties`-Abschnitts einen `DnsService`-Abschnitt hinzu. In diesem Fall müssen Sie den DNS-Dienst nicht zu `addonFeatures` hinzufügen. Weitere Informationen zu den Eigenschaften, die für den DNS-Dienst festgelegt werden können, finden Sie bei den [DnsService](./service-fabric-cluster-fabric-settings.md#dnsservice)-Einstellungen.

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
1. Nachdem Sie die Clustervorlage mit Ihren Änderungen aktualisiert haben, wenden Sie die Änderungen an, und lassen Sie das Upgrade fortfahren. Nach Abschluss des Upgrades wird der DNS-Systemdienst in Ihrem Cluster ausgeführt. Der Dienstname lautet `fabric:/System/DnsService`. Sie finden ihn im Service Fabric-Explorer im Dienstabschnitt **System**. 


## <a name="setting-the-dns-name-for-your-service"></a>Festlegen des DNS-Namens für den Dienst
Sie können einen DNS-Namen für Ihre Dienste entweder deklarativ für Standarddienste in der Datei „ApplicationManifest.xml“ oder über PowerShell-Befehle festlegen.

Der DNS-Name für Ihren Dienst lässt sich im gesamten Cluster auflösen, daher müssen Sie die Eindeutigkeit des DNS-Namens im gesamten Cluster sicherstellen. 

Es wird dringend empfohlen, dass Sie ein Benennungsschema für `<ServiceDnsName>.<AppInstanceName>` verwenden, z.B. `service1.application1`. Wenn eine Anwendung mit Docker Compose bereitgestellt wird, werden den Diensten automatisch anhand dieses Benennungsschemas DNS-Namen zugewiesen.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Festlegen des DNS-Namens für einen Standarddienst in der Datei „ApplicationManifest.xml“
Öffnen Sie das Projekt in Visual Studio oder Ihrem bevorzugten Editor, und öffnen Sie die Datei „ApplicationManifest.xml“. Navigieren Sie zum Abschnitt der Standarddienste, und fügen Sie für jeden Dienst das Attribut `ServiceDnsName` hinzu. Im folgenden Beispiel wird gezeigt, wie Sie den DNS-Namen des Diensts auf `service1.application1` festlegen.

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Nachdem die Anwendung bereitgestellt wurde, wird der DNS-Name für die Dienstinstanz in dieser Dienstinstanz im Service Fabric Explorer wie in der folgenden Abbildung angezeigt: 

![Dienstendpunkte](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

Das folgende Beispiel legt den DNS-Namen für einen zustandsbehafteten Dienst auf `statefulsvc.app` fest. Der Dienst verwendet ein benanntes Partitionsschema. Beachten Sie, dass die Partitionsnamen aus Kleinbuchstaben bestehen. Dies ist notwendig für Partitionen, für die DNS-Abfragen durchgeführt werden. Weitere Informationen finden Sie unter [Senden von DNS-Abfragen in einer zustandsbehafteten Dienstpartition](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Festlegen des DNS-Namens für einen Dienst mithilfe von Powershell
Sie können den DNS-Namen für einen Dienst festlegen, während Sie diesen Dienst mit dem PowerShell-Befehl `New-ServiceFabricService` erstellen. Im folgenden Beispiel wird ein neuer zustandsloser Dienst namens mit dem DNS-Namen `service1.application1` erstellt.

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Vorschau:] Senden von DNS-Abfragen in einer zustandsbehafteten Dienstpartition
Ab Service Fabric-Version 6.3 unterstützt der Service Fabric-DNS-Dienst Abfragen für Dienstpartitionen.

Bei Partitionen, die in DNS-Abfragen verwendet werden sollen, gelten folgende Einschränkungen bei der Benennung:

   - Partitionsnamen müssen DNS-konform sein.
   - Partitionsnamen mit mehreren Bezeichnungen (mit einem Punkt „.“ im Namen) dürfen nicht verwendet werden.
   - Partitionsnamen müssen in Kleinbuchstaben geschrieben werden.

DNS-Abfragen für eine Partition sind folgendermaßen partitioniert:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Hinweis:

- *First-Label-Of-Partitioned-Service-DNSName* ist der erste Teil des DNS-Namens Ihres Diensts.
- *PartitionPrefix* ist ein Wert, der im DnsService-Abschnitt des Clustermanifests oder über die Resource Manager-Vorlage des Clusters festgelegt werden kann. Der Standardwert lautet „-“. Weitere Informationen finden Sie bei den [DnsService](./service-fabric-cluster-fabric-settings.md#dnsservice)-Einstellungen.
- *Target-Partition-Name* ist der Name der Partition. 
- *PartitionSuffix* ist ein Wert, der im DnsService-Abschnitt des Clustermanifests oder über die Resource Manager-Vorlage des Clusters festgelegt werden kann. Der Standardwert ist eine leere Zeichenfolge. Weitere Informationen finden Sie bei den [DnsService](./service-fabric-cluster-fabric-settings.md#dnsservice)-Einstellungen.
- *Remaining-Partitioned-Service-DNSName* ist der verbleibende Teil des DNS-Namens Ihres Diensts.

Die folgenden Beispiele zeigen DNS-Abfragen für partitionierte Dienste, die auf einem Cluster mit Standardeinstellungen für `PartitionPrefix` und `PartitionSuffix` ausgeführt werden: 

- Um die Partition „0“ eines Diensts mit dem DNS-Namen `backendrangedschemesvc.application` aufzulösen, der ein Bereichspartitionierungsschema verwendet, geben Sie `backendrangedschemesvc-0.application` an.
- Um die Partition „first“ eines Diensts mit dem DNS-Namen `backendnamedschemesvc.application` aufzulösen, der ein Namenspartitionierungsschema verwendet, geben Sie `backendnamedschemesvc-first.application` an.

Der DNS-Dienst gibt die IP-Adresse des primären Replikats der Partition zurück. Wenn keine Partition angegeben wird, gibt der Dienst die IP-Adresse des primären Replikats einer zufällig ausgewählten Partition zurück.

## <a name="using-dns-in-your-services"></a>Verwenden von DNS in Ihren Diensten
Wenn Sie mehr als einen Dienst bereitstellen, finden Sie die Endpunkte der anderen Dienste, mit denen Sie kommunizieren möchten, über einen DNS-Namen. Der DNS-Dienst funktioniert für zustandslose Dienste und in Service Fabric, Version 6.3 und höher, auch für zustandsbehaftete Dienste. Bei zustandsbehafteten Diensten, die auf Service Fabric-Versionen vor 6.3 ausgeführt werden, können Sie den integrierten [Reverseproxydienst](./service-fabric-reverseproxy.md) für HTTP-Aufrufe verwenden, um eine bestimmte Dienstpartition aufzurufen. 

Dynamische Ports werden vom DNS-Dienst nicht unterstützt. Sie können den Reverseproxydienst verwenden, um Dienste aufzulösen, die dynamische Ports verwenden.

Der folgende Code zeigt, wie Sie einen zustandslosen Dienst über DNS aufrufen. Es handelt sich ganz einfach um einen regulären HTTP-Aufruf, bei dem Sie den DNS-Namen, den Port und ggf. einen optionalen Pfad als Bestandteil der URL angeben.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

Der folgende Code zeigt den Aufruf einer bestimmten Partition eines zustandsbehafteten Diensts. In diesem Fall enthält der DNS-Name den Partitionsnamen (Partition1). Der Aufruf geht von einem Cluster mit Standardwerten für `PartitionPrefix` und `PartitionSuffix` aus.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="known-issues"></a>Bekannte Probleme
* Bei Service Fabric ab Version 6.3 besteht ein Problem mit DNS-Lookups für Dienstnamen, die einen Bindestrich im DNS-Namen enthalten. Weitere Informationen zu diesem Problem finden Sie in [diesem GitHub-Problem](https://github.com/Azure/service-fabric-issues/issues/1197). Eine Fehlerbehebung ist für das nächste 6.3-Update vorgesehen. 

* Der DNS-Dienst für Service Fabric-Dienste wird unter Linux noch nicht unterstützt. Der DNS-Dienst wird für Container unter Linux unterstützt. Eine manuelle Auflösung mithilfe von Fabric Client/ServicePartitionResolver ist die verfügbare Alternative.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Dienstkommunikation innerhalb des Clusters unter [Herstellung einer Verbindung mit Diensten und Kommunikation mit diesen Diensten](service-fabric-connect-and-communicate-with-services.md).

