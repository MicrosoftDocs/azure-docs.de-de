---
title: 'Azure Service Fabric: Beispiele für das Reliable Services-Anwendungsmanifest | Microsoft Docs'
description: Erfahren Sie, wie Sie die Einstellungen für das Anwendungs- und Dienstmanifest für eine Service Fabric Reliable Services-Anwendung konfigurieren.
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
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6c4c8f0ee6aa12c58e02f71b42312cd6872076aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60719157"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Reliable Services: Beispiele für Anwendungs- und Dienstmanifeste
Im Folgenden finden Sie Beispiele für die Anwendungs- und Dienstmanifeste einer Service Fabric-Anwendung mit einem ASP.NET Core Web-Front-End und einem zustandsbehafteten Back-End. Der Zweck dieser Beispiele besteht darin, zu zeigen, welche Einstellungen verfügbar sind und wie sie verwendet werden. Diese Anwendungs- und Dienstmanifeste basieren auf den [Service Fabric .NET-Schnellstart](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)manifesten.

Die folgenden Features werden gezeigt:

|Manifest|Features|
|---|---|
|[Anwendungsmanifest](#application-manifest)| [Ressourcenkontrolle](service-fabric-resource-governance.md), [Ausführen eines Diensts als lokales Administratorkonto](service-fabric-application-runas-security.md), [Anwenden einer Standardrichtlinie auf alle Dienstcodepakete](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [Erstellen von Benutzer- und Gruppenprinzipalen](service-fabric-application-runas-security.md), gemeinsames Verwenden eines Datenpakets durch Dienstinstanzen, [Außerkraftsetzen von Dienstendpunkten](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService-Dienstmanifest| [Ausführen eines Skripts beim Dienststart](service-fabric-run-script-at-service-startup.md), [Definieren eines HTTPS-Endpunkts](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService-Dienstmanifest| [Deklarieren eines Konfigurationspakets](service-fabric-application-and-service-manifests.md), [Deklarieren ein Datenpakets](service-fabric-application-and-service-manifests.md), [Konfigurieren eines Endpunkts](service-fabric-service-manifest-resources.md)| 

Weitere Informationen zu bestimmten XML Elementen finden Sie unter [Anwendungsmanifestelemente](#application-manifest-elements), [VotingWeb-Dienstmanifestelemente](#votingweb-service-manifest-elements) und [VotingData-Dienstmanifestelemente](#votingdata-service-manifest-elements).

## <a name="application-manifest"></a>Anwendungsmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>VotingWeb-Dienstmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>VotingData-Dienstmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
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

### <a name="resourceoverrides-element"></a>ResourceOverrides-Element
Gibt die Ressourcenüberschreibungen für in Dienstmanifestressourcen deklarierte Endpunkte an. Weitere Informationen finden Sie unter [ResourceOverrides-Element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement).

### <a name="endpoints-element"></a>Endpoints-Element
Die außer Kraft zu setzenden Endpunkte. Weitere Informationen finden Sie unter [Endpoints-Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType).

### <a name="endpoint-element"></a>Endpoint-Element
Der im Dienstmanifest deklarierte Endpunkt, der außer Kraft gesetzt werden soll. Weitere Informationen finden Sie unter [Endpoint-Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement).

### <a name="policies-element"></a>Policies-Element
Beschreibt Richtlinien (Endpunktbindung, Paketfreigabe, ausführendes Konto und Sicherheitszugriff), die auf das importierte Dienstmanifest angewendet werden sollen. Weitere Informationen finden Sie unter [Policies-Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement).

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy-Element
Definiert die Richtlinie zur Ressourcenkontrolle, die auf der Ebene des gesamten Dienstpakets angewendet wird. Weitere Informationen finden Sie unter [ServicePackageResourceGovernancePolicy-Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType).

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy-Element
Gibt Ressourcengrenzwerte für ein Codepaket an. Weitere Informationen finden Sie unter [ResourceGovernancePolicy-Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement).

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy-Element
Gibt an, ob ein Code-, Konfigurations- oder Datenpaket von Dienstinstanzen des gleichen Diensttyps gemeinsam verwendet werden soll. Weitere Informationen finden Sie unter [PackageSharingPolicy-Element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType).

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy-Element
Erteilt Zugriffsberechtigungen für einen Prinzipal in einer in einem Dienstmanifest festgelegten Ressource (etwa einem Endpunkt). Es ist in der Regel sehr nützlich, den Zugriff von Diensten auf verschiedene Ressourcen zu steuern und einzuschränken, um Sicherheitsrisiken zu minimieren. Dies ist besonders wichtig, wenn die Anwendung auf der Grundlage von einer Sammlung von Diensten aus einem Marketplace erstellt wurde, die von verschiedenen Entwicklern entwickelt wurden. Weitere Informationen finden Sie unter [SecurityAccessPolicy-Element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement).

### <a name="runaspolicy-element"></a>RunAsPolicy-Element
Gibt das lokale Benutzer- oder Systemkonto an, unter dem ein Dienstcodepaket ausgeführt wird. Domänenkonten werden in Windows Server-Bereitstellungen unterstützt, in denen Azure Active Directory verfügbar ist. Standardmäßig werden Anwendungen unter dem Konto ausgeführt, unter dem der Prozess „Fabric.exe“ ausgeführt wird. Anwendungen können auch unter anderen Konten ausgeführt werden. Diese müssen im Abschnitt für Prinzipale deklariert werden. Wenn Sie eine RunAs-Richtlinie auf einen Dienst anwenden und im Dienstmanifest Endpunktressourcen mit dem HTTP-Protokoll deklariert sind, müssen Sie auch „SecurityAccessPolicy“ angeben. Diese Richtlinie soll sicherstellen, dass Ports, die diesen Endpunkten zugeordnet sind, richtig auf der Zugriffssteuerungsliste für das RunAs-Benutzerkonto eingetragen sind, in dem der Dienst ausgeführt wird. Für einen HTTPS-Endpunkt müssen Sie darüber hinaus „EndpointBindingPolicy“ definieren, um den Namen des Zertifikats anzugeben, der an den Client zurückgegeben werden soll. Weitere Informationen finden Sie unter [RunAsPolicy-Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement).

### <a name="defaultservices-element"></a>DefaultServices-Element
Deklariert Dienstinstanzen, die automatisch erstellt werden, wenn eine Anwendung mit diesem Anwendungstyp instanziiert wird. Weitere Informationen finden Sie unter [DefaultServices-Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType).

### <a name="service-element"></a>Service-Element
Deklariert die automatische Erstellung eines Dienst beim Instanziieren der Anwendung. Weitere Informationen finden Sie unter [Service-Element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType).

### <a name="statefulservice-element"></a>StatefulService-Element
Definiert einen zustandsbehafteten Dienst. Weitere Informationen finden Sie unter [StatefulService-Element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement).

### <a name="statelessservice-element"></a>StatelessService-Element
Definiert einen zustandslosen Dienst. Weitere Informationen finden Sie unter [StatelessService-Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement).

### <a name="principals-element"></a>Principals-Element
Beschreibt die Sicherheitsprinzipale (Benutzer, Gruppen), die für die Anwendung zur Ausführung von Diensten und sicheren Ressourcen erforderlich sind. Auf Prinzipale wird in den Richtlinienabschnitten verwiesen. Weitere Informationen finden Sie unter [Principals-Element](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType).

### <a name="groups-element"></a>Groups-Element
Deklariert eine Reihe von Gruppen als Sicherheitsprinzipale, auf die in Richtlinien verwiesen werden kann. Gruppen sind sinnvoll, wenn es für verschiedene Diensteinstiegspunkte mehrere Benutzer gibt, die auf Gruppenebene bestimmte allgemeine Berechtigungen benötigen. Weitere Informationen finden Sie unter [Groups-Element](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType).

### <a name="group-element"></a>Group-Element
Deklariert eine Gruppe als Sicherheitsprinzipal, auf den in Richtlinien verwiesen werden kann. Weitere Informationen finden Sie unter [Group-Element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement).

### <a name="membership-element"></a>Membership-Element
 Weitere Informationen finden Sie unter [Membership-Element](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement).

### <a name="systemgroup-element"></a>SystemGroup-Element
 Weitere Informationen finden Sie unter [SystemGroup-Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement).

### <a name="users-element"></a>Users-Element
Deklariert eine Reihe von Benutzern als Sicherheitsprinzipale, auf die in Richtlinien verwiesen werden kann. Weitere Informationen finden Sie unter [Users-Element](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType).

### <a name="user-element"></a>User-Element
Deklariert einen Benutzer als Sicherheitsprinzipal, auf den in Richtlinien verwiesen werden kann. Weitere Informationen finden Sie unter [User-Element](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement).

### <a name="memberof-element"></a>MemberOf-Element
Benutzer können jeder bestehenden Mitgliedschaftsgruppe hinzugefügt werden, sodass sie alle Eigenschaften und Sicherheitseinstellungen der jeweiligen Mitgliedschaftsgruppe übernehmen können. Die Mitgliedschaftsgruppe kann verwendet werden, um externe Ressourcen zu schützen, auf die verschiedene Dienste oder derselbe Dienst (auf einem anderen Computer) zugreifen müssen. Weitere Informationen finden Sie unter [MemberOf-Element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement).

### <a name="systemgroup-element"></a>SystemGroup-Element
Das System, dem der Benutzer hinzugefügt werden soll.  Das System muss im Abschnitt für Gruppen definiert werden. Weitere Informationen finden Sie unter [SystemGroup-Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement).

### <a name="group-element"></a>Group-Element
Die Gruppe, der der Benutzer hinzugefügt werden soll.  Die Gruppe muss im Abschnitt für Gruppen definiert werden. Weitere Informationen finden Sie unter [Group-Element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement).

### <a name="policies-element"></a>Policies-Element
Beschreibt die Richtlinien (Protokollsammlung, standardmäßiges Ausführungskonto, Integrität und Sicherheitszugriff), die auf Anwendungsebene angewendet werden sollen. Weitere Informationen finden Sie unter [Policies-Element](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType).

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy-Element
Geben Sie ein Standardbenutzerkonto für alle Dienstcodepakete an, für die kein bestimmter RunAsPolicy-Wert im Abschnitt „ServiceManifestImport“ definiert ist. Weitere Informationen finden Sie unter [DefaultRunAsPolicy-Element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType).




## <a name="votingweb-service-manifest-elements"></a>VotingWeb-Dienstmanifestelemente
### <a name="servicemanifest-element"></a>ServiceManifest-Element
Beschreibt deklarativ den Typ und die Version des Diensts. Das Element listet die unabhängig aktualisierbaren Code-, Konfigurations- und Datenpakete auf, die ein Dienstpaket bilden, mit dem ein oder mehrere Diensttypen unterstützt werden. Ressourcen, Diagnoseeinstellungen und Dienstmetadaten wie Diensttyp, Integritätseigenschaften und Lastenausgleichsmetriken werden ebenfalls angegeben. Weitere Informationen finden Sie unter [ServiceManifest-Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType).

### <a name="servicetypes-element"></a>ServiceTypes-Element
Definiert, welche Diensttypen von „CodePackage“ in diesem Manifest unterstützt werden. Wenn ein Dienst mit einem dieser Diensttypen instanziiert wird, werden alle Codepakete, die in diesem Manifest deklariert sind, durch Ausführen ihrer Einstiegspunkte aktiviert. Diensttypen werden auf Ebene der Manifeste und nicht auf Ebene der Codepakete deklariert. Weitere Informationen finden Sie unter [ServiceTypes-Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType).

### <a name="statelessservicetype-element"></a>StatelessServiceType-Element
Beschreibt den Typ eines zustandslosen Diensts. Weitere Informationen finden Sie unter [StatelessServiceType-Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType).

### <a name="codepackage-element"></a>CodePackage-Element
Beschreibt ein Codepaket, das einen festgelegten Diensttyp unterstützt. Wenn ein Dienst mit einem dieser Diensttypen instanziiert wird, werden alle Codepakete, die in diesem Manifest deklariert sind, durch Ausführen ihrer Einstiegspunkte aktiviert. Die resultierenden Prozesse registrieren die unterstützten Diensttypen zur Laufzeit. Wenn mehrere Codepakete vorhanden sind, werden sie alle aktiviert, wenn im System nach einem der deklarierten Diensttypen gesucht wird. Weitere Informationen finden Sie unter [CodePackage-Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement).

### <a name="setupentrypoint-element"></a>SetupEntryPoint-Element
Ein privilegierter Einstiegspunkt, der standardmäßig mit den gleichen Anmeldeinformationen wie Service Fabric (normalerweise dem NETWORKSERVICE-Konto) vor jedem anderen Einstiegspunkt ausgeführt wird. Die durch „EntryPoint“ angegebene ausführbare Datei ist üblicherweise der Diensthost mit langer Ausführungsdauer. Das Vorhandensein eines separaten Setupeinstiegspunkts vermeidet, dass der Diensthost über längere Zeiträume mit hohen Berechtigungen ausgeführt werden muss. Weitere Informationen finden Sie unter [SetupEntryPoint-Element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType).

### <a name="exehost-element"></a>ExeHost-Element
 Weitere Informationen finden Sie unter [ExeHost-Element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement).

### <a name="program-element"></a>Program-Element
Der Name der ausführbaren Datei.  Beispiel: „MySetup.bat“ oder „MyServiceHost.exe“ Weitere Informationen finden Sie unter [Program-Element](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType).

### <a name="arguments-element"></a>Arguments-Element
 Weitere Informationen finden Sie unter [Arguments-Element](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType).

### <a name="workingfolder-element"></a>WorkingFolder-Element
Das Arbeitsverzeichnis für den Prozess im Codepaket auf dem Clusterknoten, auf dem die Anwendung bereitgestellt wird. Sie können drei Werte angeben: „Work“ (Standardwert), „CodePackage“ oder „CodeBase“. „CodeBase“ gibt an, dass das Arbeitsverzeichnis auf das Verzeichnis festgelegt ist, in dem die EXE-Datei im Codepaket definiert ist. „CodePackage“ legt das Arbeitsverzeichnis auf den Stamm des Codepakets unabhängig davon fest, wo die EXE-Datei im Codepaketverzeichnis definiert ist. „Work“ legt das Arbeitsverzeichnis auf einen eindeutigen Ordner fest, der auf dem Knoten erstellt wird.  Dieser Ordner ist für die gesamte Anwendungsinstanz identisch. Standardmäßig ist das Arbeitsverzeichnis aller Prozesse in der Anwendung auf den Arbeitsordner der Anwendung festgelegt. Hier können die Prozesse die Daten schreiben. Das Schreiben von Daten in das Codepaket oder die Codebasis wird nicht empfohlen, da diese Ordner von verschiedenen Anwendungsinstanzen gemeinsam verwendet werden können und ggf. gelöscht werden. Weitere Informationen finden Sie unter [WorkingFolder-Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType).

### <a name="consoleredirection-element"></a>ConsoleRedirection-Element

> [!WARNING]
> Verwenden Sie die Konsolenumleitung nicht in einer Produktionsanwendung, sondern nur für die lokale Entwicklung und beim Debuggen. Leitet die Konsolenausgabe aus dem Startskript in eine Ausgabedatei im Anwendungsordner namens „log“ auf dem Clusterknoten um, auf dem die Anwendung bereitgestellt und ausgeführt wird. Weitere Informationen finden Sie unter [ConsoleRedirection-Element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType).

### <a name="entrypoint-element"></a>EntryPoint-Element
Die durch „EntryPoint“ angegebene ausführbare Datei ist üblicherweise der Diensthost mit langer Ausführungsdauer. Das Vorhandensein eines separaten Setupeinstiegspunkts vermeidet, dass der Diensthost über längere Zeiträume mit hohen Berechtigungen ausgeführt werden muss. Die von „EntryPoint“ angegebene ausführbare Datei wird ausgeführt, nachdem „SetupEntryPoint“ erfolgreich beendet wurde. Der resultierende Prozess wird überwacht und neu gestartet (er beginnt wieder mit „SetupEntryPoint“), sofern er beendet wird oder abstürzt. Weitere Informationen finden Sie unter [EntryPoint-Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType).

### <a name="exehost-element"></a>ExeHost-Element
 Weitere Informationen finden Sie unter [ExeHost-Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType).

### <a name="configpackage-element"></a>ConfigPackage-Element
Deklariert einen Ordner, der durch das Name-Attribut benannt wird und die Datei „Settings.xml“ enthält, unter „PackageRoot“. Diese Datei enthält Abschnitte mit benutzerdefinierten Schlüssel-Wert-Paar-Einstellungen, die der Prozess zur Laufzeit einlesen kann. Wenn sich während eines Upgrades nur die Version von „ConfigPackage“ geändert hat, wird der ausgeführte Prozess nicht neu gestartet. Stattdessen benachrichtigt ein Rückruf den Prozess, dass sich Konfigurationseinstellungen geändert haben, sodass sie dynamisch neu geladen werden können. Weitere Informationen finden Sie unter [ConfigPackage-Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement).

### <a name="resources-element"></a>Resources-Element
Beschreibt die von diesem Dienst verwendeten Ressourcen, die ohne Änderung des kompilierten Codes deklariert und beim Bereitstellen des Dienst geändert werden können. Der Zugriff auf diese Ressourcen wird mit den Abschnitten für Prinzipale und Richtlinien im Anwendungsmanifest gesteuert. Weitere Informationen finden Sie unter [Resources-Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType).

### <a name="endpoints-element"></a>Endpoints-Element
Definiert Endpunkte für den Dienst. Weitere Informationen finden Sie unter [Endpoints-Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType).

### <a name="endpoint-element"></a>Endpoint-Element
Der im Dienstmanifest deklarierte Endpunkt, der außer Kraft gesetzt werden soll. Weitere Informationen finden Sie unter [Endpoint-Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement).



## <a name="votingdata-service-manifest-elements"></a>VotingData-Dienstmanifestelemente
### <a name="servicemanifest-element"></a>ServiceManifest-Element
Beschreibt deklarativ den Typ und die Version des Diensts. Das Element listet die unabhängig aktualisierbaren Code-, Konfigurations- und Datenpakete auf, die ein Dienstpaket bilden, mit dem ein oder mehrere Diensttypen unterstützt werden. Ressourcen, Diagnoseeinstellungen und Dienstmetadaten wie Diensttyp, Integritätseigenschaften und Lastenausgleichsmetriken werden ebenfalls angegeben. Weitere Informationen finden Sie unter [ServiceManifest-Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType).

### <a name="servicetypes-element"></a>ServiceTypes-Element
Definiert, welche Diensttypen von „CodePackage“ in diesem Manifest unterstützt werden. Wenn ein Dienst mit einem dieser Diensttypen instanziiert wird, werden alle Codepakete, die in diesem Manifest deklariert sind, durch Ausführen ihrer Einstiegspunkte aktiviert. Diensttypen werden auf Ebene der Manifeste und nicht auf Ebene der Codepakete deklariert. Weitere Informationen finden Sie unter [ServiceTypes-Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType).

### <a name="statefulservicetype-element"></a>StatefulServiceType-Element
Beschreibt den Typ eines zustandsbehafteten Diensts. Weitere Informationen finden Sie unter [StatefulServiceType-Element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType).

### <a name="codepackage-element"></a>CodePackage-Element
Beschreibt ein Codepaket, das einen festgelegten Diensttyp unterstützt. Wenn ein Dienst mit einem dieser Diensttypen instanziiert wird, werden alle Codepakete, die in diesem Manifest deklariert sind, durch Ausführen ihrer Einstiegspunkte aktiviert. Die resultierenden Prozesse registrieren die unterstützten Diensttypen zur Laufzeit. Wenn mehrere Codepakete vorhanden sind, werden sie alle aktiviert, wenn im System nach einem der deklarierten Diensttypen gesucht wird. Weitere Informationen finden Sie unter [CodePackage-Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement).

### <a name="entrypoint-element"></a>EntryPoint-Element
Die durch „EntryPoint“ angegebene ausführbare Datei ist üblicherweise der Diensthost mit langer Ausführungsdauer. Das Vorhandensein eines separaten Setupeinstiegspunkts vermeidet, dass der Diensthost über längere Zeiträume mit hohen Berechtigungen ausgeführt werden muss. Die von „EntryPoint“ angegebene ausführbare Datei wird ausgeführt, nachdem „SetupEntryPoint“ erfolgreich beendet wurde. Der resultierende Prozess wird überwacht und neu gestartet (er beginnt wieder mit „SetupEntryPoint“), sofern er beendet wird oder abstürzt. Weitere Informationen finden Sie unter [EntryPoint-Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType).

### <a name="exehost-element"></a>ExeHost-Element
 Weitere Informationen finden Sie unter [ExeHost-Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType).

### <a name="program-element"></a>Program-Element
Der Name der ausführbaren Datei.  Beispiel: „MySetup.bat“ oder „MyServiceHost.exe“ Weitere Informationen finden Sie unter [Program-Element](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType).

### <a name="workingfolder-element"></a>WorkingFolder-Element
Das Arbeitsverzeichnis für den Prozess im Codepaket auf dem Clusterknoten, auf dem die Anwendung bereitgestellt wird. Sie können drei Werte angeben: „Work“ (Standardwert), „CodePackage“ oder „CodeBase“. „CodeBase“ gibt an, dass das Arbeitsverzeichnis auf das Verzeichnis festgelegt ist, in dem die EXE-Datei im Codepaket definiert ist. „CodePackage“ legt das Arbeitsverzeichnis auf den Stamm des Codepakets unabhängig davon fest, wo die EXE-Datei im Codepaketverzeichnis definiert ist. „Work“ legt das Arbeitsverzeichnis auf einen eindeutigen Ordner fest, der auf dem Knoten erstellt wird.  Dieser Ordner ist für die gesamte Anwendungsinstanz identisch. Standardmäßig ist das Arbeitsverzeichnis aller Prozesse in der Anwendung auf den Arbeitsordner der Anwendung festgelegt. Hier können die Prozesse die Daten schreiben. Das Schreiben von Daten in das Codepaket oder die Codebasis wird nicht empfohlen, da diese Ordner von verschiedenen Anwendungsinstanzen gemeinsam verwendet werden können und ggf. gelöscht werden. Weitere Informationen finden Sie unter [WorkingFolder-Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType).

### <a name="configpackage-element"></a>ConfigPackage-Element
Deklariert einen Ordner, der durch das Name-Attribut benannt wird und die Datei „Settings.xml“ enthält, unter „PackageRoot“. Diese Datei enthält Abschnitte mit benutzerdefinierten Schlüssel-Wert-Paar-Einstellungen, die der Prozess zur Laufzeit einlesen kann. Wenn sich während eines Upgrades nur die Version von „ConfigPackage“ geändert hat, wird der ausgeführte Prozess nicht neu gestartet. Stattdessen benachrichtigt ein Rückruf den Prozess, dass sich Konfigurationseinstellungen geändert haben, sodass sie dynamisch neu geladen werden können. Weitere Informationen finden Sie unter [ConfigPackage-Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement).

### <a name="datapackage-element"></a>DataPackage-Element
Deklariert einen Ordner, der durch das Name-Attribut benannt wird und statische Datendateien enthält, die zur Laufzeit vom Prozess verarbeitet werden sollen, unter „PackageRoot“. Service Fabric verwendet alle in den Host- und Supportpaketen angegebenen EXE- und DLLHOST-Dateien, wenn eines der im Dienstmanifest angegebenen Datenpakete aktualisiert wird. Weitere Informationen finden Sie unter [DataPackage-Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement).

### <a name="resources-element"></a>Resources-Element
Beschreibt die von diesem Dienst verwendeten Ressourcen, die ohne Änderung des kompilierten Codes deklariert und beim Bereitstellen des Dienst geändert werden können. Der Zugriff auf diese Ressourcen wird mit den Abschnitten für Prinzipale und Richtlinien im Anwendungsmanifest gesteuert. Weitere Informationen finden Sie unter [Resources-Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType).

### <a name="endpoints-element"></a>Endpoints-Element
Definiert Endpunkte für den Dienst. Weitere Informationen finden Sie unter [Endpoints-Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType).

### <a name="endpoint-element"></a>Endpoint-Element
Der im Dienstmanifest deklarierte Endpunkt, der außer Kraft gesetzt werden soll. Weitere Informationen finden Sie unter [Endpoint-Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement).

