---
title: Packen Ihrer Azure Service Fabric-Dienste in Container unter Windows
description: Erfahren Sie, wie Sie Ihre Reliable Services und Reliable Actors von Service Fabric unter Windows in Container packen.
ms.topic: conceptual
ms.date: 5/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e0c5f3ecf0334ad2190d8542e54388a2a25d30fb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999806"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Packen Ihrer Reliable Services und Reliable Actors von Service Fabric in Container unter Windows

Service Fabric unterstützt das Packen von Service Fabric-Microservices (auf Reliable Services und Reliable Actors basierende Dienste) in Container. Weitere Informationen finden Sie unter [Service Fabric und Container](service-fabric-containers-overview.md).

Dieses Dokument enthält Anweisungen für die Schritte zum Ausführen Ihrer Dienste in einem Windows-Container.

> [!NOTE]
> Dieses Feature steht derzeit nur für Windows zur Verfügung. Wenn Sie Container verwenden möchten, muss der Cluster unter Windows Server 2016 mit Containern betrieben werden.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Schritte zum Packen Ihrer Service Fabric-Anwendung in einen Container

1. Öffnen Sie Ihre Service Fabric-Anwendung in Visual Studio.

2. Fügen Sie Ihrem Projekt die Klasse [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) hinzu. Bei dem Code in dieser Klasse handelt es sich um ein Hilfsprogramm, mit dem die Binärdateien der Service Fabric-Laufzeit korrekt in der Anwendung geladen werden, wenn die Ausführung innerhalb eines Containers erfolgt.

3. Initialisieren Sie für jedes Codepaket, das Sie in einen Container packen möchten, das Ladeprogramm am Programmeinstiegspunkt. Fügen Sie Ihrer Programmeinstiegspunkt-Datei den statischen Konstruktor aus dem folgenden Codeausschnitt hinzu.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Erstellen und [Packen](service-fabric-package-apps.md#Package-App) Sie Ihr Projekt. Klicken Sie zum Erstellen des Projekts und eines Pakets im Projektmappen-Explorer mit der rechten Maustaste auf das Anwendungsprojekt, und wählen Sie den Befehl **Paket** aus.

5. Führen Sie für jedes Codepaket, das Sie in einen Container packen möchten, das PowerShell-Skript [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1) aus. Die Nutzung lautet wie folgt:

    Vollständiges .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Das Skript erstellt unter „$dockerPackageOutputDirectoryPath“ einen Ordner mit Docker-Artefakten. Ändern Sie die generierte Dockerfile-Datei, um Ports verfügbar zu machen (`expose`), Setupskripts auszuführen usw. Dies erfolgt nach Ihrem Bedarf.

6. Als Nächstes müssen Sie Ihr Docker-Containerpaket [erstellen](service-fabric-get-started-containers.md#Build-Containers) und an Ihr Repository [pushen](service-fabric-get-started-containers.md#Push-Containers).

7. Ändern Sie die Dateien „ApplicationManifest.xml“ und „ServiceManifest.xml“, um das Containerimage, Repositoryinformationen, die Registrierungsauthentifizierung und die Port-zu-Host-Zuordnung hinzuzufügen. Informationen zum Ändern der Manifeste finden Sie unter [Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Windows](service-fabric-get-started-containers.md). Die Codepaketdefinition im Dienstmanifest muss durch das entsprechende Containerimage ersetzt werden. Legen Sie den Einstiegspunkt auf einen Containerhosttyp fest.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Fügen Sie die Port-zu-Host-Zuordnung für Ihren Replikator- und Dienstendpunkt hinzu. Da diese beiden Ports zur Laufzeit von Service Fabric zugewiesen werden, wird der Containerport auf „0“ festgelegt, um den zugewiesenen Port für die Zuordnung zu verwenden.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Informationen zum Konfigurieren des Containerisolationsmodus finden Sie unter [Konfigurieren des Isolationsmodus]( ./service-fabric-get-started-containers.md#configure-isolation-mode). Windows unterstützt zwei Isolationsmodi für Container: Prozesse und Hyper-V. In den folgenden Codeausschnitten wird gezeigt, wie der Isolationsmodus in der Anwendungsmanifestdatei angegeben wird.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Standardmäßig erhalten Service Fabric-Anwendungen Zugriff auf die Service Fabric-Runtime über einen Endpunkt, der anwendungsspezifische Anforderungen akzeptiert. Sie sollten das Deaktivieren dieses Zugriffs in Erwägung ziehen, wenn die Anwendung nicht vertrauenswürdigen Code hostet. Weitere Informationen finden Sie unter [Bewährte Methoden für die Sicherheit in Service Fabric](service-fabric-best-practices-security.md#platform-isolation). Um den Zugriff auf die Service Fabric-Runtime zu deaktivieren, fügen Sie die folgende Einstellung im Abschnitt „Policies“ des Anwendungsmanifests für das importierte Dienstmanifest hinzu:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Zum Testen der Anwendung muss sie in einem Cluster bereitgestellt werden, der mindestens über die Version 5.7 verfügt. Bei Runtimes unter Version 6.1 müssen Sie die Clustereinstellungen bearbeiten und aktualisieren, um diese Previewfunktion zu aktivieren. Führen Sie die Schritte in [diesem Artikel](service-fabric-cluster-fabric-settings.md) aus, um die folgende Einstellung hinzuzufügen:
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. Führen Sie als Nächstes die [Bereitstellung](service-fabric-deploy-remove-applications.md) des bearbeiteten Anwendungspakets für diesen Cluster durch.

Sie verfügen nun über eine in einen Container gepackte Service Fabric-Anwendung, die in Ihrem Cluster ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Ausführen von [Containern in Service Fabric](service-fabric-get-started-containers.md)
* Weitere Informationen zum [Anwendungslebenszyklus](service-fabric-application-lifecycle.md) von Service Fabric