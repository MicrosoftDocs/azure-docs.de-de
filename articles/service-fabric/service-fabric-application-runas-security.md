---
title: Ausführen eines Azure Service Fabric-Diensts unter System- und lokalen Sicherheitskonten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Service Fabric-Anwendung unter System- und lokalen Sicherheitskonten ausführen.  Erstellen Sie Sicherheitsprinzipale, und übernehmen Sie die Run-As-Richtlinie zur sicheren Ausführung der Dienste.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: aljo
ms.openlocfilehash: 28cd1162d7cae2b3a16062bdf18a2971e1f05aad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60621172"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Ausführen eines Diensts als lokales Benutzerkonto oder lokales Systemkonto
Durch Verwenden von Azure Service Fabric können Sie Anwendungen sichern, die im Cluster unter verschiedenen Benutzerkonten ausgeführt werden. Standardmäßig werden Service Fabric-Anwendungen unter dem Konto ausgeführt, unter dem der Prozess „Fabric.exe“ ausgeführt wird. Service Fabric bietet auch die Möglichkeit, Anwendungen für einen lokalen Benutzer oder ein Systemkonto auszuführen. Unterstützte lokale Systemkontotypen sind **LocalUser**, **NetworkService**, **LocalService** und **LocalSystem**.  Wenn Sie Service Fabric auf einem eigenständigen Windows-Cluster ausführen, können Sie einen Dienst unter [Active Directory-Domänenkonten](service-fabric-run-service-as-ad-user-or-group.md) oder [gruppenverwalteten Dienstkonten](service-fabric-run-service-as-gmsa.md) ausführen.

Im Anwendungsmanifest definieren Sie die Benutzerkonten, die zum Ausführen von Diensten oder zum Sichern von Ressourcen im Abschnitt **Prinzipale** erforderlich sind. Sie können auch Benutzergruppen definieren und erstellen, sodass mehrere Benutzer gemeinsam verwaltet werden können. Dies ist nützlich, wenn es für verschiedene Diensteinstiegspunkte mehrere Benutzer gibt, die auf Gruppenebene bestimmte gemeinsame Berechtigungen benötigen.  Die Benutzer werden in einer RunAs-Richtlinie referenziert, die auf einen bestimmten Dienst oder auf alle Dienste in der Anwendung angewendet wird. 

Standardmäßig wird die RunAs-Richtlinie auf den Haupteinstiegspunkt angewendet.  Sie können eine RunAs-Richtlinie auch auf den Setupeinstiegspunkt anwenden, wenn Sie [bestimmte Setupvorgänge mit hohen Berechtigungen in einem Systemkonto ausführen](service-fabric-run-script-at-service-startup.md) müssen. Sie können die Richtlinie auch sowohl auf Haupt- und Setupeinstiegspunkte anwenden.  

> [!NOTE] 
> Wenn Sie eine RunAs-Richtlinie auf einen Dienst anwenden und das Dienstmanifest Endpunktressourcen mit dem HTTP-Protokoll deklariert, müssen Sie eine Richtlinie vom Typ **SecurityAccessPolicy** angeben.  Weitere Informationen finden Sie unter [Zuweisen einer Sicherheitszugriffsrichtlinie für HTTP- und HTTPS-Endpunkte](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Ausführen eines Diensts als lokaler Benutzer
Sie können einen lokalen Benutzer erstellen, der zum Sichern eines Diensts in der Anwendung dienen kann. Wenn ein Konto vom Typ **LocalUser** im Abschnitt „Principals“ des Anwendungsmanifests angegeben wird, erstellt Service Fabric lokale Benutzerkonten auf Computern, auf denen die Anwendung bereitgestellt wird. Standardmäßig weisen diese Konten nicht die gleichen Namen auf wie die im Anwendungsmanifest angegebenen (z.B. *Customer3* im folgenden Anwendungsmanifestbeispiel). Stattdessen werden sie dynamisch generiert und verfügen über zufällige Kennwörter.

Der Abschnitt **RunAsPolicy** für ein **ServiceManifestImport**-Element gibt das Benutzerkonto aus dem Abschnitt **Prinzipale** an, das zum Ausführen des Dienstcodepakets verwendet wird.  Das folgende Beispiel zeigt, wie Sie einen lokalen Benutzer erstellen und eine RunAs-Richtlinie auf den Haupteinstiegspunkt anwenden:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Erstellen einer lokalen Benutzergruppe
Sie können Benutzergruppen erstellen und dieser einen oder mehrere Benutzer hinzufügen. Dies ist nützlich, falls es für verschiedene Diensteinstiegspunkte mehrere Benutzer gibt, die auf Gruppenebene bestimmte allgemeine Berechtigungen benötigen. Das folgende Beispiel für ein Anwendungsmanifest zeigt eine lokale Gruppe namens *LocalAdminGroup*, die über Administratorrechte verfügt. Zwei Benutzer, *Customer1* und *Customer2*, wurden dieser lokalen Gruppe als Mitglieder hinzugefügt. Im Abschnitt **ServiceManifestImport** wird eine RunAs-Richtlinie angewendet, um das Codepaket *Stateful1Pkg* als *Customer2* auszuführen.  Eine weitere RunAs-Richtlinie wird angewendet, um das Codepaket *Web1Pkg* als *Customer1* auszuführen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Anwenden einer Standardrichtlinie auf alle Dienstcodepakete
Verwenden Sie den Abschnitt **DefaultRunAsPolicy**, um ein Standardbenutzerkonto für alle Codepakete anzugeben, für die keine bestimmte **RunAsPolicy** definiert ist. Wenn die meisten Codepakete, die im Dienstmanifest einer Anwendung angegeben sind, im gleichen Benutzerkonto ausgeführt werden müssen, kann für die Anwendung einfach eine RunAs-Standardrichtlinie mit diesem Benutzerkonto definiert werden. Im folgenden Beispiel wird beispielsweise Folgendes angegeben: Wenn für ein Codepaket keine **RunAsPolicy** angegeben ist, soll das Codepaket für den Benutzer **MyDefaultAccount** ausgeführt werden, der im Abschnitt „Prinzipale“ angegeben ist.  Unterstützte Kontotypen sind LocalUser, NetworkService, LocalSystem und LocalService.  Wenn Sie einen lokalen Benutzer oder Dienst verwenden, geben Sie auch den Kontonamen und das Kennwort an.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Lokales Debuggen eines Codepakets mit Konsolenumleitung
Gelegentlich ist es zum Debuggen hilfreich, die Konsolenausgabe eines ausgeführten Diensts auszuwerten. Sie können im Dienstmanifest eine Richtlinie für die Konsolenumleitung am Einstiegspunkt festlegen, die die Ausgabe in eine Datei schreibt. Die Dateiausgabe wird in den Anwendungsordner **log** auf dem Clusterknoten geschrieben, auf dem die Anwendung bereitgestellt und ausgeführt wird. 

> [!WARNING]
> Verwenden Sie die Richtlinie zur Konsolenumleitung nie in einer Anwendung, die in der Produktionsumgebung bereitgestellt wurde, da sich dies auf das Anwendungsfailover auswirken kann. Verwenden Sie diese *nur* für die lokale Entwicklung und das Debuggen.  
> 
> 

Das folgende Dienstmanifestbeispiel zeigt das Aktivieren der Konsolenumleitung mit einem FileRetentionCount-Wert:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nächste Schritte
* [Informationen zum Anwendungsmodell](service-fabric-application-model.md)
* [Angeben von Ressourcen in einem Dienstmanifest](service-fabric-service-manifest-resources.md)
* [Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
