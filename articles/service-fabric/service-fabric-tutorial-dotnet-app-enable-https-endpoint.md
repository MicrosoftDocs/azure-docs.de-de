---
title: Hinzufügen eines HTTPS-Endpunkts zu einer Service Fabric-App in Azure mithilfe von Kestrel | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie einem ASP.NET Core-Front-End-Webdienst mit Kestrel einen HTTPS-Endpunkt hinzufügen und die Anwendung in einem Cluster bereitstellen.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 48dd09bf70e99adc250027df872266bea39a786b
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302406"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>Tutorial: Hinzufügen eines HTTPS-Endpunkts zu einem ASP.NET Core-Front-End-Dienst mit Web-API mithilfe von Kestrel

Dieses Tutorial ist der dritte Teil einer Reihe.  Hier erfahren Sie, wie Sie HTTPS in einem ASP.NET Core-Dienst unter Service Fabric aktivieren. Am Ende des Tutorials verfügen Sie über eine Abstimmungsanwendung mit einem HTTPS-fähigen ASP.NET Core-Web-Front-End, das am Port 443 lauscht. Wenn Sie die Abstimmungsanwendung nicht manuell im Tutorial [Bereitstellen einer Anwendung in einem Service Fabric-Cluster in Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md) erstellen möchten, können Sie den [Quellcode für die fertige Anwendung herunterladen](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/).

Im dritten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Definieren eines HTTPS-Endpunkts im Dienst
> * Konfigurieren von Kestrel für die Verwendung von HTTPS
> * Installieren des SSL-Zertifikats auf den Remoteclusterknoten
> * Gewähren von Zugriff auf den privaten Schlüssel des Zertifikats für „NETWORK SERVICE“
> * Öffnen von Port 443 im Azure-Lastenausgleich
> * Bereitstellen der Anwendung in einem Remotecluster

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Erstellen einer .NET Service Fabric-Anwendung](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Bereitstellen der Anwendung in einem Remotecluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Hinzufügen eines HTTPS-Endpunkts zu einem ASP.NET Core-Front-End-Dienst
> * [Konfigurieren von CI/CD mit Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Einrichten der Überwachung und Diagnose für die Anwendung](service-fabric-tutorial-monitoring-aspnet.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Installieren Sie Visual Studio 2019](https://www.visualstudio.com/) (Version 15.5 oder höher) mit den Workloads **Azure-Entwicklung** und **ASP.NET und Webentwicklung**.
* [Installieren Sie das Service Fabric SDK](service-fabric-get-started.md).

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>Beziehen eines Zertifikats oder Erstellen eines selbstsignierten Entwicklungszertifikats

Verwenden Sie für Produktionsanwendungen ein Zertifikat einer [Zertifizierungsstelle](https://wikipedia.org/wiki/Certificate_authority). Zu Entwicklungs- und Testzwecken können Sie auch ein selbstsigniertes Zertifikat erstellen und verwenden. Das Skript *CertSetup.ps1* aus dem Service Fabric SDK erstellt ein selbstsigniertes Zertifikat und importiert es in den Zertifikatspeicher `Cert:\LocalMachine\My`. Öffnen Sie eine Eingabeaufforderung als Administrator, und führen Sie den folgenden Befehl aus, um ein Zertifikat mit dem Antragsteller „CN=mytestcert“ zu erstellen:

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

Falls Sie bereits über eine PFX-Zertifikatdatei verfügen, führen Sie den folgenden Befehl aus, um das Zertifikat in den Zertifikatspeicher `Cert:\LocalMachine\My` zu importieren:

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>Definieren eines HTTPS-Endpunkts im Dienstmanifest

Starten Sie Visual Studio als **Administrator**, und öffnen Sie die Projektmappe „Voting“. Öffnen Sie *VotingWeb/PackageRoot/ServiceManifest.xml* im Projektmappen-Explorer. Das Dienstmanifest definiert die Dienstendpunkte.  Suchen Sie den Abschnitt **Endpoints**, und bearbeiten Sie den vorhandenen Endpunkt „ServiceEndpoint“.  Ändern Sie den Namen in „EndpointHttps“, und legen Sie das Protokoll auf *https*, den Typ auf *Input*, und den Port auf *443* fest.  Speichern Sie die Änderungen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>Konfigurieren von Kestrel für die Verwendung von HTTPS

Öffnen Sie die Datei *VotingWeb/VotingWeb.cs* im Projektmappen-Explorer.  Konfigurieren Sie Kestrel für die Verwendung von HTTPS und für die Suche des Zertifikats im Speicher `Cert:\LocalMachine\My`. Fügen Sie die folgenden using-Anweisungen hinzu:

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

Aktualisieren Sie `ServiceInstanceListener`, damit dieser den neuen Endpunkt (*EndpointHttps*) verwendet und am Port 443 lauscht. Wenn Sie den Webhost für die Verwendung von Kestrel-Server konfigurieren, müssen Sie Kestrel so konfigurieren, dass an allen Netzwerkschnittstellen auf IPv6-Adressen gelauscht wird: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`.

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Fügen Sie außerdem die folgende Methode hinzu, damit Kestrel das Zertifikat im Speicher `Cert:\LocalMachine\My` anhand des Antragstellers findet.  

Ersetzen Sie „&lt;your_CN_value&gt;“ durch „mytestcert“, falls Sie mithilfe des vorherigen PowerShell-Befehls ein selbstsigniertes Zertifikat erstellt haben, oder verwenden Sie den allgemeinen Namen Ihres Zertifikats.

```csharp
private X509Certificate2 GetCertificateFromStore()
{
    var store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var currentCerts = certCollection.Find(X509FindType.FindBySubjectDistinguishedName, "CN=<your_CN_value>", false);
        return currentCerts.Count == 0 ? null : currentCerts[0];
    }
    finally
    {
        store.Close();
    }
}
```

## <a name="give-network-service-access-to-the-certificates-private-key"></a>Gewähren von Zugriff auf den privaten Schlüssel des Zertifikats für „NETWORK SERVICE“

In einem vorherigen Schritt haben Sie das Zertifikat in den Speicher `Cert:\LocalMachine\My` auf dem Entwicklungscomputer importiert.  Gewähren Sie nun dem Konto, das den Dienst ausführt (standardmäßig „NETWORK SERVICE“), explizit Zugriff auf den privaten Schlüssel des Zertifikats. Dieser Schritt kann manuell über das Tool „certlm.msc“ ausgeführt werden. Es empfiehlt sich jedoch, automatisch ein PowerShell-Skript auszuführen. Hierzu können Sie unter **SetupEntryPoint** im Dienstmanifest [ein Startskript konfigurieren](service-fabric-run-script-at-service-startup.md).

### <a name="configure-the-service-setup-entry-point"></a>Konfigurieren des Setupeinstiegspunkts für Dienste

Öffnen Sie *VotingWeb/PackageRoot/ServiceManifest.xml* im Projektmappen-Explorer.  Fügen Sie im Abschnitt **CodePackage** den Knoten **SetupEntryPoint** und anschließend den Knoten **ExeHost** hinzu.  Legen Sie in **ExeHost** die Option **Program** auf „Setup.bat“ und die Option **WorkingFolder** auf „CodePackage“ fest.  Beim Start des VotingWeb-Diensts wird vor dem Start von „VotingWeb.exe“ das Skript „Setup.bat“ aus dem Ordner „CodePackage“ ausgeführt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>Hinzufügen des Batchskripts und des PowerShell-Setupskripts

Um PowerShell über den Punkt **SetupEntryPoint** auszuführen, können Sie „PowerShell.exe“ in einer Batchdatei ausführen, die auf eine PowerShell-Datei verweist. Fügen Sie zunächst die Batchdatei dem Dienstprojekt hinzu.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **VotingWeb**, klicken Sie auf **Hinzufügen**->**Neues Element**, und fügen Sie eine neue Datei namens „Setup.bat“ hinzu.  Bearbeiten Sie die Datei *Setup.bat*, und fügen Sie den folgenden Befehl hinzu:

```bat
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

Legen Sie in den Dateieigenschaften von *Setup.bat* die Option **In Ausgabeverzeichnis kopieren** auf „Kopieren, wenn neuer“ fest.

![Festlegen der Dateieigenschaften][image1]

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **VotingWeb**, klicken Sie auf **Hinzufügen**->**Neues Element**, und fügen Sie eine neue Datei namens „SetCertAccess.ps1“ hinzu.  Bearbeiten Sie die Datei *SetCertAccess.ps1*, und fügen Sie das folgende Skript hinzu:

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

Legen Sie in den Dateieigenschaften von *SetCertAccess.ps1* die Option **In Ausgabeverzeichnis kopieren** auf „Kopieren, wenn neuer“ fest.

### <a name="run-the-setup-script-as-a-local-administrator"></a>Ausführen des Setupskripts als lokaler Administrator

Standardmäßig wird die ausführbare Setupeinstiegspunkt-Datei des Diensts unter den gleichen Anmeldeinformationen ausgeführt wie Service Fabric (in der Regel das NetworkService-Konto). Für *SetCertAccess.ps1* sind Administratorrechte erforderlich. Im Anwendungsmanifest können Sie die Sicherheitsberechtigungen zur Ausführung des Startskripts unter einem lokalen Administratorkonto ändern.

Öffnen Sie *Voting/ApplicationPackageRoot/ApplicationManifest.xml* im Projektmappen-Explorer. Erstellen Sie zunächst einen Abschnitt vom Typ **Principals**, und fügen Sie einen neuen Benutzer hinzu (beispielsweise „SetupAdminUser“). Fügen Sie das Benutzerkonto „SetupAdminUser“ der Systemgruppe „Administratoren“ hinzu.
Konfigurieren Sie als Nächstes im Abschnitt **ServiceManifestImport** von „VotingWebPkg“ eine Richtlinie vom Typ **RunAsPolicy**, um den Prinzipal „SetupAdminUser“ auf den Setupeinstiegspunkt anzuwenden. Diese Richtlinie teilt Service Fabric mit, dass die Datei „Setup.bat“ als „SetupAdminUser“ (mit Administratorrechten) ausgeführt wird.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
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
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung

Wählen Sie im Projektmappen-Explorer die Anwendung **Voting** aus, und legen Sie die Eigenschaft **Anwendungs-URL** auf „https:\//localhost:443“ fest.

Speichern Sie alle Dateien, und drücken Sie F5, um die Anwendung lokal auszuführen.  Nach der Bereitstellung der Anwendung wird https:\//localhost:443 in einem Webbrowser geöffnet. Bei Verwendung eines selbstsignierten Zertifikats wird eine Warnung mit dem Hinweis angezeigt, dass Ihr PC der Sicherheit dieser Website nicht vertraut.  Navigieren Sie weiter zur Webseite.

![Voting-Anwendung][image2]

## <a name="install-certificate-on-cluster-nodes"></a>Installieren des Zertifikats auf Clusterknoten

Installieren Sie das Zertifikat im Speicher `Cert:\LocalMachine\My` aller Remoteclusterknoten, bevor Sie die Anwendung in Azure bereitstellen.  Dienste können in verschiedene Knoten im Cluster verschoben werden.  Wenn der Front-End-Webdienst auf einem Clusterknoten gestartet wird, sucht das Startskript das Zertifikat und konfiguriert Zugriffsberechtigungen.

Exportieren Sie zunächst das Zertifikat in eine PFX-Datei. Öffnen Sie die Anwendung „certlm.msc“, und navigieren Sie zu **Eigene Zertifikate**>**Zertifikate**.  Klicken Sie mit der rechten Maustaste auf das Zertifikat *mytestcert*, und klicken Sie anschließend auf **Alle Aufgaben**>**Exportieren**.

![Exportieren des Zertifikats][image4]

Klicken Sie im Export-Assistenten auf **Ja, privaten Schlüssel exportieren**, und wählen Sie die Option für das PFX-Format aus.  Exportieren Sie die Datei nach *C:\Benutzer\sfuser\votingappcert.pfx*.

Installieren Sie als Nächstes das Zertifikat mithilfe des Cmdlets [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) auf dem Remotecluster.

> [!Warning]
> Für Entwicklungs- und Testanwendungen reicht ein selbstsigniertes Zertifikat. Für Produktionsanwendungen muss dagegen ein Zertifikat von einer [Zertifizierungsstelle](https://wikipedia.org/wiki/Certificate_authority) verwendet werden.

```powershell
Connect-AzAccount

$vaultname="sftestvault"
$certname="VotingAppPFX"
$certpw="!Password321#"
$groupname="voting_RG"
$clustername = "votinghttps"
$ExistingPfxFilePath="C:\Users\sfuser\votingappcert.pfx"

$appcertpwd = ConvertTo-SecureString -String $certpw -AsPlainText -Force

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $certpw

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $certpw
   } | ConvertTo-Json

$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
Write-Host "Writing secret to $certname in vault $vaultname"
$secret = Set-AzureKeyVaultSecret -VaultName $vaultname -Name $certname -SecretValue $secretValue

# Add a certificate to all the VMs in the cluster.
Add-AzServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $secret.Id -Verbose
```

## <a name="open-port-443-in-the-azure-load-balancer"></a>Öffnen von Port 443 im Azure-Lastenausgleich

Öffnen Sie im Lastenausgleich den Port 443, sofern er noch nicht geöffnet ist.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzLoadBalancer
```

## <a name="deploy-the-application-to-azure"></a>Bereitstellen der Anwendung für Azure

Speichern Sie alle Dateien, wechseln Sie vom Debug- in den Releasemodus, und drücken Sie F6, um das Projekt neu zu erstellen.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Voting**, und klicken Sie auf **Veröffentlichen**. Wählen Sie den Verbindungsendpunkt des Clusters aus, den Sie im Tutorial [Bereitstellen einer Anwendung in einem Service Fabric-Cluster in Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md) erstellt haben, oder wählen Sie einen anderen Cluster aus.  Klicken Sie auf **Veröffentlichen**, und veröffentlichen Sie die Anwendung für den Remotecluster.

Wenn die Anwendung bereitgestellt wurde, navigieren Sie in einem Webbrowser zu [https://mycluster.region.cloudapp.azure.com:443](https://mycluster.region.cloudapp.azure.com:443). (Aktualisieren Sie die URL mit dem Verbindungsendpunkt für Ihren Cluster.) Bei Verwendung eines selbstsignierten Zertifikats wird eine Warnung mit dem Hinweis angezeigt, dass Ihr PC der Sicherheit dieser Website nicht vertraut.  Navigieren Sie weiter zur Webseite.

![Voting-Anwendung][image3]

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Definieren eines HTTPS-Endpunkts im Dienst
> * Konfigurieren von Kestrel für die Verwendung von HTTPS
> * Installieren des SSL-Zertifikats auf den Remoteclusterknoten
> * Gewähren von Zugriff auf den privaten Schlüssel des Zertifikats für „NETWORK SERVICE“
> * Öffnen von Port 443 im Azure-Lastenausgleich
> * Bereitstellen der Anwendung in einem Remotecluster

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Konfigurieren von CI/CD mit Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
