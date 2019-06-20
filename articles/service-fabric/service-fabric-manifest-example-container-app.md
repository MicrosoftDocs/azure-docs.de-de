---
title: Manifestbeispiele für Azure Service Fabric-Containeranwendungen | Microsoft Docs
description: Erfahren Sie, wie Sie die Einstellungen für das Anwendungs- und Dienstmanifest für eine Service Fabric-Anwendung mit mehreren Containern konfigurieren.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 622e6f7552d91cdb9ccf3668c302496c68a5920f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60719242"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Mehrere Container: Beispiele für Anwendungs- und Dienstmanifeste
Im Folgenden finden Sie Beispiele für die Anwendungs- und Dienstmanifeste für eine Service Fabric-Anwendung mit mehreren Containern. Der Zweck dieser Beispiele besteht darin, zu zeigen, welche Einstellungen verfügbar sind und wie sie verwendet werden. Diese Anwendungs- und Dienstmanifeste basieren auf den Manifesten für das [Windows Server 2016-Containerbeispiel](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows).

Die folgenden Features werden gezeigt:

|Manifest|Features|
|---|---|
|[Anwendungsmanifest](#application-manifest)| [Überschreiben von Umgebungsvariablen](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [Konfigurieren der Port-zu-Host-Zuordnung des Containers](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [Konfigurieren der Authentifizierung der Containerregistrierung](service-fabric-get-started-containers.md#configure-container-registry-authentication), [Ressourcenkontrolle](service-fabric-resource-governance.md), [Festlegen des Isolierungsmodus](service-fabric-get-started-containers.md#configure-isolation-mode), [Angeben buildspezifischer Containerimages des Betriebssystems](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService-Dienstmanifest](#frontendservice-service-manifest)| [Festlegen von Umgebungsvariablen](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [Konfigurieren eines Endpunkts](service-fabric-get-started-containers.md#configure-communication), Übergeben von Befehlen an den Container, [Importieren eines Zertifikats in einen Container](service-fabric-securing-containers.md)| 
|[BackEndService-Dienstmanifest](#backendservice-service-manifest)|[Festlegen von Umgebungsvariablen](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [Konfigurieren eines Endpunkts](service-fabric-get-started-containers.md#configure-communication), [Konfigurieren des Volumetreibers](service-fabric-containers-volume-logging-drivers.md)| 

Weitere Informationen zu bestimmten XML Elementen finden Sie unter [Anwendungsmanifestelemente](#application-manifest-elements), [FrontEndService-Dienstmanifestelemente](#frontendservice-service-manifest-elements) und [BackEndService-Dienstmanifestelemente](#backendservice-service-manifest-elements).

## <a name="application-manifest"></a>Anwendungsmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService-Dienstmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService-Dienstmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="application-manifest-elements"></a>Anwendungsmanifestelemente
### <a name="applicationmanifest-element"></a>ApplicationManifest-Element
Beschreibt deklarativ den Typ und die Version der Anwendung. Es wird auf ein oder mehrere Dienstmanifeste der zugehörigen Dienste verwiesen, um einen Anwendungstyp zu bilden. Konfigurationseinstellungen der zugehörigen Dienste können mithilfe von parametrisierten Anwendungseinstellungen überschrieben werden. Standarddienste, Dienstvorlagen, Prinzipale, Richtlinien, Diagnosesetup und Zertifikate können ebenfalls auf Anwendungsebene deklariert werden. Weitere Informationen finden Sie unter [ApplicationManifest-Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType).

### <a name="parameters-element"></a>Parameters-Element
Deklariert die Parameter, die in diesem Anwendungsmanifest verwendet werden. Der Wert dieser Parameter kann angegeben werden, wenn die Anwendung instanziiert wird, und kann zum Überschreiben der Anwendungs- oder Dienstkonfigurationseinstellungen verwendet werden. Weitere Informationen finden Sie unter [Parameters-Element](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType).

### <a name="parameter-element"></a>Parameter-Element
Ein in diesem Manifest zu verwendender Anwendungsparameter. Der Parameterwert kann während der Anwendungsinstanziierung geändert werden. Falls kein Wert angegeben wird, wird der Standardwert verwendet. Weitere Informationen finden Sie unter [Parameter-Element](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement).

### <a name="servicemanifestimport-element"></a>ServiceManifestImport-Element
Importiert ein vom Dienstentwickler erstelltes Dienstmanifest. Ein Dienstmanifest muss für jeden zugehörigen Dienst in der Anwendung importiert werden. Für das Dienstmanifest können Konfigurationsüberschreibungen und Richtlinien deklariert werden. Weitere Informationen finden Sie unter [ServiceManifestImport-Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType).

### <a name="servicemanifestref-element"></a>ServiceManifestRef-Element
Importiert das Dienstmanifest nach Verweis. Derzeit muss die Dienstmanifestdatei (ServiceManifest.xml) im Buildpaket enthalten sein. Weitere Informationen finden Sie unter [ServiceManifestRef-Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement).

### <a name="policies-element"></a>Policies-Element
Beschreibt Richtlinien (Endpunktbindung, Paketfreigabe, ausführendes Konto und Sicherheitszugriff), die auf das importierte Dienstmanifest angewendet werden sollen. Weitere Informationen finden Sie unter [Policies-Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement).

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy-Element
Definiert die Richtlinie zur Ressourcenkontrolle, die auf der Ebene des gesamten Dienstpakets angewendet wird. Weitere Informationen finden Sie unter [ServicePackageResourceGovernancePolicy-Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType).

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy-Element
Gibt Ressourcengrenzwerte für ein Codepaket an. Weitere Informationen finden Sie unter [ResourceGovernancePolicy-Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement).

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies-Element
Gibt Richtlinien für die Aktivierung der Containerhosts an. Weitere Informationen finden Sie unter [Policies-Element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement).

### <a name="repositorycredentials-element"></a>RepositoryCredentials-Element
Anmeldeinformationen für das Containerimagerepository, aus dem Images per Pull abgerufen werden sollen Weitere Informationen finden Sie unter [RepositoryCredentials-Element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType).

### <a name="portbinding-element"></a>PortBinding-Element
Gibt an, welche Endpunktressource an den bereitgestellten Containerport gebunden werden soll. Weitere Informationen finden Sie unter [PortBinding-Element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType).

### <a name="volume-element"></a>Volume-Element
Gibt das an den Container zu bindende Volume an. Weitere Informationen finden Sie unter [Volume-Element](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType).

### <a name="driveroption-element"></a>DriverOption-Element
Treiberoptionen, die an den Treiber übergeben werden sollen Weitere Informationen finden Sie unter [DriverOption-Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType).

### <a name="imageoverrides-element"></a>ImageOverrides-Element
Windows Server-Container sind möglicherweise nicht mit allen Versionen des Betriebssystems kompatibel.  Sie können mehrere Betriebssystemimages pro Container angeben und mit den Buildversionen des Betriebssystems kennzeichnen. Rufen Sie die Buildversion des Betriebssystems ab, indem Sie „winver“ an einer Windows-Eingabeaufforderung ausführen. Wenn das zugrunde liegende Betriebssystem die Buildversion 16299 (Windows Server-Version 1709) ist, wählt Service Fabric das mit Os="16299" gekennzeichnete Containerimage aus. Ein nicht gekennzeichnetes Containerimage wird für alle Versionen des Betriebssystems als funktionierend angenommen und überschreibt das im Dienstmanifest angegebene Image. Weitere Informationen finden Sie unter [ImageOverrides-Element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType).

### <a name="image-element"></a>Image-Element
Containerimage, das der Buildversionsnummer des zu startenden Betriebssystems entspricht. Wenn das Betriebssystemattribut nicht angegeben wird, wird das Containerimage für alle Versionen des Betriebssystems als funktionierend angenommen und überschreibt das im Dienstmanifest angegebene Image. Weitere Informationen finden Sie unter [Image-Element](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType).

### <a name="environmentoverrides-element"></a>EnvironmentOverrides-Element
 Weitere Informationen finden Sie unter [EnvironmentOverrides-Element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement).

### <a name="environmentvariable-element"></a>EnvironmentVariable-Element
Umgebungsvariable Weitere Informationen finden Sie unter [EnvironmentVariable-Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType).

### <a name="certificateref-element"></a>CertificateRef-Element
Gibt Informationen zu einem X509-Zertifikat an, das für die Containerumgebung verfügbar gemacht werden soll. Das Zertifikat muss im LocalMachine-Speicher aller Clusterknoten installiert werden.
Beim Start der Anwendung liest die Laufzeit das Zertifikat und generiert eine PFX-Datei und ein Kennwort (unter Windows) oder eine PEM-Datei (unter Linux).
Auf die PFX-Datei und das Kennwort kann im Container über die Umgebungsvariablen Certificates_ServicePackageName_CodePackageName_CertName_PFX und Certificates_ServicePackageName_CodePackageName_CertName_Password zugegriffen werden. Auf die PEM-Datei kann im Container über die Umgebungsvariablen Certificates_ServicePackageName_CodePackageName_CertName_PEM und Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey zugegriffen werden. Weitere Informationen finden Sie unter [CertificateRef-Element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType).

### <a name="defaultservices-element"></a>DefaultServices-Element
Deklariert Dienstinstanzen, die automatisch erstellt werden, wenn eine Anwendung mit diesem Anwendungstyp instanziiert wird. Weitere Informationen finden Sie unter [DefaultServices-Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType).

### <a name="service-element"></a>Service-Element
Deklariert die automatische Erstellung eines Dienst beim Instanziieren der Anwendung. Weitere Informationen finden Sie unter [Service-Element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType).

### <a name="statelessservice-element"></a>StatelessService-Element
Definiert einen zustandslosen Dienst. Weitere Informationen finden Sie unter [StatelessService-Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement).


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService-Dienstmanifestelemente
### <a name="servicemanifest-element"></a>ServiceManifest-Element
Beschreibt deklarativ den Typ und die Version des Diensts. Das Element listet die unabhängig aktualisierbaren Code-, Konfigurations- und Datenpakete auf, die ein Dienstpaket bilden, mit dem ein oder mehrere Diensttypen unterstützt werden. Ressourcen, Diagnoseeinstellungen und Dienstmetadaten wie Diensttyp, Integritätseigenschaften und Lastenausgleichsmetriken werden ebenfalls angegeben. Weitere Informationen finden Sie unter [ServiceManifest-Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType).

### <a name="servicetypes-element"></a>ServiceTypes-Element
Definiert, welche Diensttypen von „CodePackage“ in diesem Manifest unterstützt werden. Wenn ein Dienst mit einem dieser Diensttypen instanziiert wird, werden alle Codepakete, die in diesem Manifest deklariert sind, durch Ausführen ihrer Einstiegspunkte aktiviert. Diensttypen werden auf Ebene der Manifeste und nicht auf Ebene der Codepakete deklariert. Weitere Informationen finden Sie unter [ServiceTypes-Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType).

### <a name="statelessservicetype-element"></a>StatelessServiceType-Element
Beschreibt den Typ eines zustandslosen Diensts. Weitere Informationen finden Sie unter [StatelessServiceType-Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType).

### <a name="codepackage-element"></a>CodePackage-Element
Beschreibt ein Codepaket, das einen festgelegten Diensttyp unterstützt. Wenn ein Dienst mit einem dieser Diensttypen instanziiert wird, werden alle Codepakete, die in diesem Manifest deklariert sind, durch Ausführen ihrer Einstiegspunkte aktiviert. Die resultierenden Prozesse registrieren die unterstützten Diensttypen zur Laufzeit. Wenn mehrere Codepakete vorhanden sind, werden sie alle aktiviert, wenn im System nach einem der deklarierten Diensttypen gesucht wird. Weitere Informationen finden Sie unter [CodePackage-Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement).

### <a name="entrypoint-element"></a>EntryPoint-Element
Die durch „EntryPoint“ angegebene ausführbare Datei ist üblicherweise der Diensthost mit langer Ausführungsdauer. Das Vorhandensein eines separaten Setupeinstiegspunkts vermeidet, dass der Diensthost über längere Zeiträume mit hohen Berechtigungen ausgeführt werden muss. Die von „EntryPoint“ angegebene ausführbare Datei wird ausgeführt, nachdem „SetupEntryPoint“ erfolgreich beendet wurde. Der resultierende Prozess wird überwacht und neu gestartet (er beginnt wieder mit „SetupEntryPoint“), sofern er beendet wird oder abstürzt. Weitere Informationen finden Sie unter [EntryPoint-Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType).

### <a name="containerhost-element"></a>ContainerHost-Element
 Weitere Informationen finden Sie unter [ContainerHost-Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType).

### <a name="imagename-element"></a>ImageName-Element
Das Repository und Image auf https://hub.docker.com oder Azure Container Registry. Weitere Informationen finden Sie unter [ImageName-Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType).

### <a name="environmentvariables-element"></a>EnvironmentVariables-Element
Dient zum Übergeben von Umgebungsvariablen an Ihren Container oder Ihre EXE-Datei.  Weitere Informationen finden Sie unter [EnvironmentVariables-Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType).

### <a name="environmentvariable-element"></a>EnvironmentVariable-Element
Umgebungsvariable Weitere Informationen finden Sie unter [EnvironmentVariable-Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType).

### <a name="configpackage-element"></a>ConfigPackage-Element
Deklariert einen Ordner, der durch das Name-Attribut benannt wird und die Datei „Settings.xml“ enthält. Diese Datei enthält Abschnitte mit benutzerdefinierten Schlüssel-Wert-Paar-Einstellungen, die der Prozess zur Laufzeit einlesen kann. Wenn sich während eines Upgrades nur die Version von „ConfigPackage“ geändert hat, wird der ausgeführte Prozess nicht neu gestartet. Stattdessen benachrichtigt ein Rückruf den Prozess, dass sich Konfigurationseinstellungen geändert haben, sodass sie dynamisch neu geladen werden können. Weitere Informationen finden Sie unter [ConfigPackage-Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement).

### <a name="datapackage-element"></a>DataPackage-Element
Deklariert einen Ordner, der durch das Name-Attribut benannt wird und statische Datendateien enthält. Service Fabric verwendet alle in den Host- und Supportpaketen angegebenen EXE- und DLLHOST-Dateien, wenn eines der im Dienstmanifest angegebenen Datenpakete aktualisiert wird. Weitere Informationen finden Sie unter [DataPackage-Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement).

### <a name="resources-element"></a>Resources-Element
Beschreibt die von diesem Dienst verwendeten Ressourcen, die ohne Änderung des kompilierten Codes deklariert und beim Bereitstellen des Dienst geändert werden können. Der Zugriff auf diese Ressourcen wird mit den Abschnitten für Prinzipale und Richtlinien im Anwendungsmanifest gesteuert. Weitere Informationen finden Sie unter [Resources-Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType).

### <a name="endpoints-element"></a>Endpoints-Element
Definiert Endpunkte für den Dienst. Weitere Informationen finden Sie unter [Endpoints-Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType).

### <a name="endpoint-element"></a>Endpoint-Element
Weitere Informationen finden Sie unter [Endpoint-Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement).


## <a name="backendservice-service-manifest-elements"></a>BackEndService-Dienstmanifestelemente
### <a name="servicemanifest-element"></a>ServiceManifest-Element
Beschreibt deklarativ den Typ und die Version des Diensts. Das Element listet die unabhängig aktualisierbaren Code-, Konfigurations- und Datenpakete auf, die ein Dienstpaket bilden, mit dem ein oder mehrere Diensttypen unterstützt werden. Ressourcen, Diagnoseeinstellungen und Dienstmetadaten wie Diensttyp, Integritätseigenschaften und Lastenausgleichsmetriken werden ebenfalls angegeben. Weitere Informationen finden Sie unter [ServiceManifest-Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType).

### <a name="servicetypes-element"></a>ServiceTypes-Element
Definiert, welche Diensttypen von „CodePackage“ in diesem Manifest unterstützt werden. Wenn ein Dienst mit einem dieser Diensttypen instanziiert wird, werden alle Codepakete, die in diesem Manifest deklariert sind, durch Ausführen ihrer Einstiegspunkte aktiviert. Diensttypen werden auf Ebene der Manifeste und nicht auf Ebene der Codepakete deklariert. Weitere Informationen finden Sie unter [ServiceTypes-Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType).

### <a name="statelessservicetype-element"></a>StatelessServiceType-Element
Beschreibt den Typ eines zustandslosen Diensts. Weitere Informationen finden Sie unter [StatelessServiceType-Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType).

### <a name="codepackage-element"></a>CodePackage-Element
Beschreibt ein Codepaket, das einen festgelegten Diensttyp unterstützt. Wenn ein Dienst mit einem dieser Diensttypen instanziiert wird, werden alle Codepakete, die in diesem Manifest deklariert sind, durch Ausführen ihrer Einstiegspunkte aktiviert. Die resultierenden Prozesse registrieren die unterstützten Diensttypen zur Laufzeit. Wenn mehrere Codepakete vorhanden sind, werden sie alle aktiviert, wenn im System nach einem der deklarierten Diensttypen gesucht wird. Weitere Informationen finden Sie unter [CodePackage-Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement).

### <a name="entrypoint-element"></a>EntryPoint-Element
Die durch „EntryPoint“ angegebene ausführbare Datei ist üblicherweise der Diensthost mit langer Ausführungsdauer. Das Vorhandensein eines separaten Setupeinstiegspunkts vermeidet, dass der Diensthost über längere Zeiträume mit hohen Berechtigungen ausgeführt werden muss. Die von „EntryPoint“ angegebene ausführbare Datei wird ausgeführt, nachdem „SetupEntryPoint“ erfolgreich beendet wurde. Der resultierende Prozess wird überwacht und neu gestartet (er beginnt wieder mit „SetupEntryPoint“), sofern er beendet wird oder abstürzt. Weitere Informationen finden Sie unter [EntryPoint-Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType).

### <a name="containerhost-element"></a>ContainerHost-Element
Weitere Informationen finden Sie unter [ContainerHost-Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType).

### <a name="imagename-element"></a>ImageName-Element
Das Repository und Image auf https://hub.docker.com oder Azure Container Registry. Weitere Informationen finden Sie unter [ImageName-Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType).

### <a name="commands-element"></a>Commands-Element
Dient zum Übergeben einer durch Kommas getrennten Liste mit Befehlen an den Container. Weitere Informationen finden Sie unter [Commands-Element](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType).

### <a name="environmentvariables-element"></a>EnvironmentVariables-Element
Dient zum Übergeben von Umgebungsvariablen an Ihren Container oder Ihre EXE-Datei.  Weitere Informationen finden Sie unter [EnvironmentVariables-Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType).

### <a name="environmentvariable-element"></a>EnvironmentVariable-Element
Umgebungsvariable Weitere Informationen finden Sie unter [EnvironmentVariable-Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType).

### <a name="configpackage-element"></a>ConfigPackage-Element
Deklariert einen Ordner, der durch das Name-Attribut benannt wird und die Datei „Settings.xml“ enthält. Diese Datei enthält Abschnitte mit benutzerdefinierten Schlüssel-Wert-Paar-Einstellungen, die der Prozess zur Laufzeit einlesen kann. Wenn sich während eines Upgrades nur die Version von „ConfigPackage“ geändert hat, wird der ausgeführte Prozess nicht neu gestartet. Stattdessen benachrichtigt ein Rückruf den Prozess, dass sich Konfigurationseinstellungen geändert haben, sodass sie dynamisch neu geladen werden können. Weitere Informationen finden Sie unter [ConfigPackage-Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement).

### <a name="resources-element"></a>Resources-Element
Beschreibt die von diesem Dienst verwendeten Ressourcen, die ohne Änderung des kompilierten Codes deklariert und beim Bereitstellen des Dienst geändert werden können. Der Zugriff auf diese Ressourcen wird mit den Abschnitten für Prinzipale und Richtlinien im Anwendungsmanifest gesteuert. Weitere Informationen finden Sie unter [Resources-Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType).

### <a name="endpoints-element"></a>Endpoints-Element
Definiert Endpunkte für den Dienst. Weitere Informationen finden Sie unter [Endpoints-Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType).

### <a name="endpoint-element"></a>Endpoint-Element
 Weitere Informationen finden Sie unter [Endpoint-Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement).

