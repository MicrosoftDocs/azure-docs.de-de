---
title: Ändern der FabricTransport-Einstellungen in Azure-Service Fabric-Akteuren | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Konfigurieren der Kommunikationseinstellungen von Akteuren in Azure Service Fabric.
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: b6cff24e5a7812a88673d80476819e51e6f5da35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125067"
---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>Konfigurieren von FabricTransport-Einstellungen für Reliable Actors

Hier folgen Einstellungen, die Sie konfigurieren können:
- C#: [FabricTransportRemotingSettings](
https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)
- Java: [FabricTransportRemotingSettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)

Sie können die Standardkonfiguration von FabricTransport auf folgende Weise ändern.

## <a name="assembly-attribute"></a>Assembly-Attribut

Das [FabricTransportActorRemotingProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN)-Attribut muss auf die Akteurclient- und Akteurdienstassemblys angewendet werden.

Das folgende Beispiel zeigt, wie der Standardwert der Einstellungen „FabricTransport“ und „OperationTimeout“ geändert werden kann:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   Das zweite Beispiel ändert die Standardwerte von „FabricTransport MaxMessageSize“ und „OperationTimeoutInSeconds“.

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>Konfigurationspaket

Sie können ein [Konfigurationspaket](service-fabric-application-and-service-manifests.md) verwenden, um die Standardkonfiguration zu ändern.

> [!IMPORTANT]
> Auf Linux-Knoten müssen Zertifikate das PEM-Format aufweisen. Weitere Informationen zum Ermitteln und Konfigurieren von Zertifikaten für Linux finden Sie unter [Konfigurieren von Zertifikaten in Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>Konfigurieren der FabricTransport-Einstellungen für den Akteurdienst

Fügen Sie den Abschnitt „TransportSettings“ zur Datei „settings.xml“ hinzu.

Der Akteurcode sucht standardmäßig im Format „&lt;Akteurname&gt;TransportSettings“ nach „SectionName“. Wenn es nicht gefunden wird, wird im Format „TransportSettings“ nach „SectionName“ gesucht.

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>Konfigurieren der FabricTransport-Einstellungen für die Akteurclientassembly

Wenn der Client nicht als Teil eines Diensts ausgeführt wird, können Sie die Datei „&lt;Name der ausführbaren Clientdatei&gt;.settings.xml“ am gleichen Speicherort erstellen, an dem sich auch die ausführbare Clientdatei befindet. Fügen Sie dann den Abschnitt „TransportSettings“ zu dieser Datei hinzu. „SectionName“ muss „TransportSettings“ lauten.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
      <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

* Konfigurieren von FabricTransport-Einstellungen zum Schützen des Actordiensts/Clients mit sekundärem Zertifikat.
  Sekundäre Zertifikatinformationen können durch Hinzufügen des Parameters „CertificateFindValuebySecondary“ hinzugefügt werden.
  Es folgt ein Beispiel für den Listener „TransportSettings“.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
  <Parameter Name="CertificateFindValuebySecondary" Value="h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C,a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
   Es folgt ein Beispiel für den Client „TransportSettings“.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
  <Parameter Name="CertificateFindValuebySecondary" Value="a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662,h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
  * Konfigurieren von FabricTransport-Einstellungen zum Schützen des Actordiensts/Clients mithilfe des Antragstellernamens.
    Der Benutzer muss „findType“ als „findBySubjectName“ angeben sowie „CertificateIssuerThumbprints“ und „CertificateRemoteCommonNames“ hinzufügen.
    Es folgt ein Beispiel für den Listener „TransportSettings“.

    ```xml
    <Section Name="TransportSettings">
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
    <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Alice" />
    <Parameter Name="CertificateIssuerThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
    <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Bob" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    </Section>
    ```
    Es folgt ein Beispiel für den Client „TransportSettings“.

  ```xml
   <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
  <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Bob" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
