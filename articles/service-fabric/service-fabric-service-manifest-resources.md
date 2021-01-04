---
title: Angeben von Service Fabric-Dienstendpunkten
description: 'Gewusst wie: Beschreiben von Endpunktressourcen in einem Dienstmanifest, einschließlich der Einrichtung von HTTPS-Endpunkten'
ms.topic: conceptual
ms.date: 09/16/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0ed5a4aa8993f52d42b97288cd143e6114ff36ff
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033305"
---
# <a name="specify-resources-in-a-service-manifest"></a>Angeben von Ressourcen in einem Dienstmanifest
## <a name="overview"></a>Übersicht
Service Fabric-Anwendungen und -Dienste werden mit Manifestdateien definiert und mit einer Versionsangabe versehen. Eine allgemeine Übersicht über „ServiceManifest.xml“ und „ApplicationManifest.xml“ finden Sie unter [Service Fabric-Anwendungs- und -Dienstmanifeste](service-fabric-application-and-service-manifests.md).

Mit dem Dienstmanifest können vom Dienst verwendete Ressourcen deklariert oder geändert werden, ohne dass der kompilierte Code geändert werden muss. Service Fabric unterstützt die Konfiguration von Endpunktressourcen für den Dienst. Der Zugriff auf die im Dienstmanifest angegebenen Ressourcen kann über das SecurityGroup-Element im Anwendungsmanifest gesteuert werden. Die Deklaration von Ressourcen ermöglicht es, dass diese Ressourcen zur Bereitstellungszeit geändert werden, sodass der Dienst keinen neuen Konfigurationsmechanismus einführen muss. Die Schemadefinition für die Datei „ServiceManifest.xml“ wird mit dem Service Fabric SDK und den Service Fabric-Tools unter *C:\Programme\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* installiert und ist unter [ServiceFabricServiceModel.xsd – Schemadokumentation](service-fabric-service-model-schema.md) dokumentiert.

## <a name="endpoints"></a>Endpunkte
Wenn eine Endpunktressource im Dienstmanifest definiert wird, weist Service Fabric Ports aus dem Bereich der reservierten Anwendungsports zu, sollte ein Port nicht explizit angegeben sein. Sehen Sie sich beispielsweise den Endpunkt *ServiceEndpoint1* an, der im Codeausschnitt aus dem Manifest im Anschluss an diesen Absatz angegeben ist. Außerdem können Dienste auch einen bestimmten Port einer Ressource anfordern. Dienstreplikate, die auf unterschiedlichen Clusterknoten ausgeführt werden, können unterschiedlichen Portnummern zugewiesen werden, während für Replikate eines Diensts auf demselben Knoten derselbe Port verwendet wird. Die Dienstreplikate können dann diese Ports nach Bedarf für die Replikation und das Überwachen auf Clientanforderungen nutzen.

Wenn Sie einen Dienst aktivieren, der einen HTTPS-Endpunkt angibt, legt Service Fabric den Zugriffssteuerungseintrag für den Port fest, bindet das angegebene Serverzertifikat an den Port und erteilt auch der Identität, als die der Dienst ausgeführt wird, Berechtigungen für den privaten Schlüssel des Zertifikats. Der Aktivierungsfluss wird jedes Mal aufgerufen, wenn Service Fabric gestartet oder die Zertifikatdeklaration der Anwendung über ein Upgrade geändert wird. Das Endpunktzertifikat wird auch auf Änderungen/Verlängerungen überwacht, und die Berechtigungen werden bei Bedarf regelmäßig erneut angewendet.

Nach der Beendigung des Diensts bereinigt Service Fabric den Eintrag für die Endpunkt-Zugriffssteuerung und entfernt die Zertifikatbindung. Auf den privaten Schlüssel des Zertifikats angewendete Berechtigungen werden jedoch nicht bereinigt.

> [!WARNING] 
> Statische Ports sollten sich nicht mit dem im Clustermanifest angegebenen Anwendungsportbereich überschneiden. Wenn Sie einen statischen Port angeben, weisen Sie ihn außerhalb des Anwendungsportbereichs zu, andernfalls führt dies zu Portkonflikten. Mit Release 6.5CU2 werden wir eine **Integritätswarnung** ausgeben, wenn wir einen solchen Konflikt erkennen, aber die Bereitstellung in Übereinstimmung mit dem ausgelieferten 6.5-Verhalten fortsetzen. Es ist jedoch möglich, dass wir die Anwendungsbereitstellung ab den nächsten Hauptversionen unterbinden.
>
> Mit Version 7.0 werden wir eine **Integritätswarnung** ausgeben, wenn wir feststellen, dass die Nutzung des Anwendungsportbereichs über „HostingConfig::ApplicationPortExhaustThresholdPercentage“ hinausgeht (standardmäßig 80 %).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Wenn ein einzelnes Dienstpaket mehrere Codepakete enthält, muss im Abschnitt **Endpoints** ebenfalls auf das Codepaket verwiesen werden.  Beispiel: Wenn **ServiceEndpoint2a** und **ServiceEndpoint2b** Endpunkte desselben Servicepakets sind, die auf unterschiedliche Codepakete verweisen, wird das Codepaket des jeweiligen Endpunkts wie folgt identifiziert:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Weitere Informationen zum Verweisen auf Endpunkte aus der Datei mit den Konfigurationspaketeinstellungen („settings.xml“) finden Sie unter [Konfigurieren zustandsbehafteter Reliable Services](service-fabric-reliable-services-configuration.md) .

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Beispiel: Angeben eines HTTP-Endpunkts für Ihren Dienst
Das folgende Dienstmanifest definiert eine TCP-Endpunktressource und zwei HTTP-Endpunktressourcen im &lt;Resources&gt;-Element.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Beispiel: Angeben eines HTTPS-Endpunkts für Ihren Dienst
Das HTTPS-Protokoll ermöglicht die Serverauthentifizierung und wird auch zum Verschlüsseln der Kommunikation zwischen Client und Server verwendet. Um HTTPS für Ihren Service Fabric-Dienst zu aktivieren, geben Sie dieses Protokoll im Abschnitt *Ressourcen -> Endpunkte -> Endpunkt* des Dienstmanifests an, wie weiter oben für den Endpunkt *ServiceEndpoint3* gezeigt.

> [!NOTE]
> Das Protokoll eines Diensts kann nicht während eines Anwendungsupgrades geändert werden. Ein solche Änderung während des Upgrades würde zu einem Verlust der Abwärtskompatibilität führen.
> 

> [!WARNING] 
> Wenn HTTPS verwendet wird, verwenden Sie nicht den gleichen Port und das gleiche Zertifikat für verschiedene Dienstinstanzen (unabhängig von der Anwendung), die für den gleichen Knoten bereitgestellt werden. Das Upgrade von zwei verschiedenen Diensten unter Verwendung desselben Ports in verschiedenen Anwendungsinstanzen führt zu einem Upgradefehler. Weitere Informationen finden Sie unter [Aktualisieren von mehreren Anwendungen mit HTTPS-Endpunkten](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Im Folgenden finden Sie ein Beispiel für ein ApplicationManifest, das die für einen HTTPS-Endpunkt erforderliche Konfiguration veranschaulicht. Das Server-/Endpunktzertifikat kann durch den Fingerabdruck oder den allgemeinen Namen des Antragstellers deklariert werden, und es muss ein Wert angegeben werden. Bei EndpointRef handelt es sich um einen Verweis auf EndpointResource in ServiceManifest, und dafür muss das HTTPS-Protokoll festgelegt sein. Sie können mehrere Endpunktzertifikate hinzufügen.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Für Linux-Cluster wird für den **MY**-Speicher standardmäßig der Ordner **/var/lib/sfcerts** verwendet.

Ein Beispiel für eine vollständige Anwendung, in der ein HTTPS-Endpunkt genutzt wird, finden Sie unter [Definieren eines HTTPS-Endpunkts im Dienstmanifest](./service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest).

## <a name="port-acling-for-http-endpoints"></a>Verwenden von Port-ACLs für HTTP-Endpunkte
Service Fabric verwendet automatisch ACLs für HTTP(S)-Endpunkte, die standardmäßig angegeben werden. Es erfolgt **keine** automatische Verwendung von ACLs, wenn einem Endpunkt keine [SecurityAccessPolicy](service-fabric-assign-policy-to-endpoint.md) zugeordnet ist und Service Fabric für die Ausführung mit einem Konto mit Administratorberechtigungen konfiguriert ist.

## <a name="overriding-endpoints-in-servicemanifestxml"></a>Außerkraftsetzen von Endpunkten in „ServiceManifest.xml“

Fügen Sie dem Anwendungsmanifest einen Abschnitt vom Typ „ResourceOverrides“ hinzu, der dem Abschnitt „ConfigOverrides“ gleichgestellt ist. In diesem Abschnitt können Sie die Außerkraftsetzung für den Abschnitt „Endpoints“ im Ressourcenabschnitt des Dienstmanifests angeben. Das Überschreiben von Endpunkten wird in Runtime 5.7.217/SDK 2.7.217 und höher unterstützt.

Wenn Sie „EndPoint“ im Dienstmanifest mit „ApplicationParameters“ außer Kraft setzen möchten, ändern Sie das Anwendungsmanifest wie folgt:

Fügen Sie im Abschnitt „ServiceManifestImport“ einen neuen Abschnitt namens „ResourceOverrides“ hinzu.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Fügen Sie unter „Parameters“ Folgendes hinzu:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Beim Bereitstellen der Anwendung können Sie die unten angegebenen Werte als ApplicationParameters übergeben.  Beispiel:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Hinweis: Wenn für einen bestimmten ApplicationParameter kein Wert angegeben wurde, wird der Standardwert für den entsprechenden EndPointName aus dem ServiceManifest verwendet.

Beispiel:

Angenommen, Sie haben im Dienstmanifest Folgendes angegeben:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Wir nehmen weiter an, dass der Port1- und der Protocol1-Wert für die Anwendungsparameter jeweils NULL oder leer ist. Der Port wird von Service Fabric festgelegt, und als Protokoll wird „tcp“ verwendet.

Angenommen, Sie geben einen falschen Wert an – beispielsweise den Zeichenfolgenwert „Foo“ anstelle einer ganzen Zahl für den Port.  Für den Befehl „New-ServiceFabricApplication“ tritt ein Fehler mit dem folgenden Hinweis auf: `The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Sie Endpunkte im Dienstmanifest von Service Fabric definieren. Ausführlichere Beispiele finden Sie unter:

> [!div class="nextstepaction"]
> [Beispiele für Anwendungs- und Dienstmanifeste](service-fabric-manifest-examples.md)

Eine exemplarische Vorgehensweise zum Verpacken und Bereitstellen einer vorhandenen Anwendung in einem Service Fabric-Cluster finden Sie unter:

> [!div class="nextstepaction"]
> [Packen und Bereitstellen einer vorhandenen ausführbaren Datei für Service Fabric](service-fabric-deploy-existing-app.md)