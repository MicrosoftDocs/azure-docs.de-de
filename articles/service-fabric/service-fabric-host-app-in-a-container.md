---
title: Bereitstellen von .NET-Apps in einem Container in Azure Service Fabric | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine vorhandene .NET-Anwendung mit Visual Studio in einen Container packen und Container in Service Fabric lokal debuggen. Die Containeranwendung wird per Push an eine Azure-Containerregistrierung übertragen und in einem Service Fabric-Cluster bereitgestellt. Bei der Bereitstellung in Azure nutzt die Anwendung Azure SQL-Datenbank für die dauerhafte Speicherung von Daten.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/18/2018
ms.author: aljo
ms.openlocfilehash: 33f742c7de340df41f5d946c891e9896d7d2a012
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66119064"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Tutorial: Bereitstellen einer .NET-App in einem Windows-Container in Azure Service Fabric

In diesem Tutorial wird gezeigt, wie Sie eine vorhandene ASP.NET-Anwendung in einen Container und als Service Fabric-Anwendung packen.  Führen Sie die Container lokal im Service Fabric-Bereitstellungscluster aus, und stellen Sie dann die Anwendung in Azure bereit.  Die Anwendung speichert Daten dauerhaft in [Azure SQL-Datenbank](/azure/sql-database/sql-database-technical-overview). 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Packen einer vorhandenen Anwendung in einen Container mithilfe von Visual Studio
> * Erstellen einer Azure SQL-Datenbank
> * Erstellen einer Azure-Containerregistrierung
> * Bereitstellen einer Service Fabric-Anwendung in Azure


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

1. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.
2. Installieren Sie [Docker CE für Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), damit Sie Container unter Windows 10 ausführen können.
3. Installieren Sie [Service Fabric-Runtime (Version 6.2 oder höher)](service-fabric-get-started.md) und das [Service Fabric SDK (Version 3.1 oder höher)](service-fabric-get-started.md).
4. Installieren Sie [Visual Studio 2017 (Version 15.7 oder höher)](https://www.visualstudio.com/) mit den Workloads **Azure-Entwicklung** und **ASP.NET und Webentwicklung**.
5. Installieren Sie [Azure PowerShell][link-azure-powershell-install].
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Herunterladen und Ausführen von Fabrikam Fiber CallCenter
Laden Sie die Beispielanwendung [Fabrikam Fiber CallCenter][link-fabrikam-github] herunter.  Klicken Sie auf den Link **download archive** (Archiv herunterladen).  Extrahieren Sie im Verzeichnis *sourceCode* in der Datei *fabrikam.zip* die Datei *sourceCode.zip*, und extrahieren Sie anschließend das Verzeichnis *VS2015* auf Ihrem Computer.

Vergewissern Sie sich, dass die Fabrikam Fiber CallCenter-Anwendung ohne Fehler erstellt und ausgeführt wird.  Starten Sie Visual Studio als **Administrator**, und öffnen Sie die Datei [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Drücken Sie F5, um die Anwendung zu debuggen und auszuführen.

![Fabrikam-Webbeispiel][fabrikam-web-page]

## <a name="containerize-the-application"></a>Packen der Anwendung in Container
Klicken Sie mit der rechten Maustaste auf das Projekt **FabrikamFiber.Web**, und klicken Sie dann auf **Hinzufügen** > **Unterstützung für Containerorchestrator**.  Wählen Sie **Service Fabric** als Containerorchestrator aus, und klicken Sie auf **OK**.

Klicken Sie auf **Ja**, um jetzt von Docker- auf Windows-Container umzusteigen.

In der Projektmappe wird das neue Service Fabric-Anwendungsprojekt **FabrikamFiber.CallCenterApplication** erstellt.  Dem vorhandenen Projekt **FabrikamFiber.Web** wird eine Dockerfile-Datei hinzugefügt.  Dem Projekt **FabrikamFiber.Web** wird darüber hinaus das Verzeichnis **PackageRoot** hinzugefügt. Es enthält das Dienstmanifest und Einstellungen für den neuen Dienst „FabrikamFiber.Web“. 

Der Container kann jetzt erstellt und in einer Service Fabric-Anwendung verpackt werden. Nachdem Sie das Containerimage auf dem Computer erstellt haben, können Sie es per Push an eine beliebige Containerregistrierung weiterleiten, und per Pull auf jeden beliebigen Host zur Ausführung abrufen.

## <a name="create-an-azure-sql-db"></a>Erstellen einer Azure SQL-Datenbank
Wenn Sie die Fabrikam Fiber CallCenter-Anwendung in der Produktion ausführen, müssen die Dateien dauerhaft in einer Datenbank gespeichert werden. Derzeit besteht keine Möglichkeit, die dauerhafte Speicherung von Daten in einem Container sicherzustellen. Daher können Sie Produktionsdaten in SQL Server nicht in einem Container speichern.

Wir empfehlen [Azure SQL-Datenbank](/azure/sql-database/sql-database-get-started-powershell). Führen Sie zum Einrichten und Ausführen einer verwalteten SQL Server-Datenbank in Azure das folgende Skript aus.  Ändern Sie die Skriptvariablen nach Bedarf. *clientIP* ist die IP-Adresse Ihres Entwicklungscomputers. Notieren Sie sich den vom Skript ausgegebenen Servernamen. 

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```
> [!TIP]
> Hinter einer Unternehmensfirewall entspricht die IP-Adresse Ihres Entwicklungscomputers unter Umständen nicht der IP-Adresse, die über das Internet verfügbar gemacht wird. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und suchen Sie im Abschnitt „SQL-Datenbanken“ nach Ihrer Datenbank, um zu überprüfen, ob die Datenbank über die richtige IP-Adresse für die Firewallregel verfügt. Klicken Sie auf ihren Namen und dann im Abschnitt „Übersicht“ auf „Serverfirewall festlegen“. „Client-IP-Adresse“ ist die IP-Adresse Ihres Entwicklungscomputers. Stellen Sie sicher, dass sie mit der IP-Adresse unter der Regel „AllowClient“ übereinstimmt.

## <a name="update-the-web-config"></a>Aktualisieren der Webkonfiguration
Aktualisieren Sie im Projekt **FabrikamFiber.Web** die Verbindungszeichenfolge in der Datei **web.config** so, dass sie auf die SQL Server-Instanz im Container verweist.  Aktualisieren Sie den Teil *Server* der Verbindungszeichenfolge in den vom vorherigen Skript erstellten Servernamen. Er sollte ungefähr „fab-fiber-751718376.database.windows.net“ lauten.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Sie können für lokales Debuggen jede beliebigen SQL Server-Instanz verwenden, solange sie von Ihrem Host aus erreichbar ist. Allerdings unterstützt **localdb** keine `container -> host`-Kommunikation. Wenn Sie eine andere SQL-Datenbank beim Erstellen eines Releasebuilds Ihrer Webanwendung verwenden möchten, fügen Sie eine weitere Verbindungszeichenfolge zu Ihrer Datei *web.release.config* hinzu.

## <a name="run-the-containerized-application-locally"></a>Lokales Ausführen der Containeranwendung
Drücken Sie **F5**, um die Anwendung in einem Container im lokalen Service Fabric-Entwicklungscluster auszuführen und zu debuggen. Klicken Sie auf **Ja**, wenn ein Meldungsfeld angezeigt wird, in dem Sie dazu aufgefordert werden, der Gruppe „ServiceFabricAllowedUsers“ Lese- und Ausführungsberechtigungen für Ihr Visual Studio-Projektverzeichnis zu gewähren.

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung
Die Anwendung wird lokal ausgeführt. Bereiten Sie nun die Bereitstellung in Azure vor.  Containerimages müssen in einer Containerregistrierung gespeichert werden.  Erstellen Sie mit dem folgenden Skript eine [Azure-Containerregistrierung](/azure/container-registry/container-registry-intro). Der Containerregistrierungsname ist für andere Azure-Abonnements sichtbar, daher muss er eindeutig sein.
Vor der Bereitstellung der Anwendung in Azure übertragen Sie das Containerimage per Push an diese Registrierung.  Bei der Bereitstellung im Cluster in Azure durch die Anwendung wird das Containerimage aus dieser Registrierung abgerufen.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Erstellen eines Service Fabric-Clusters in Azure
Service Fabric-Anwendungen werden auf einem Cluster ausgeführt, einem durch ein Netzwerk verbundenen Satz von virtuellen oder physischen Computern.  Erstellen Sie vor dem Bereitstellen der Anwendung in Azure einen Service Fabric-Cluster in Azure.

Ihre Möglichkeiten:
- Erstellen eines Testclusters über Visual Studio. Mit dieser Option können Sie direkt über Visual Studio einen sicheren Cluster mit Ihrer bevorzugten Konfiguration erstellen. 
- [Erstellen eines sicheren Clusters aus einer Vorlage](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

In diesem Tutorial wird ein Cluster über Visual Studio erstellt. Dies ist ideal für Testszenarien. Falls Sie eine andere Möglichkeit zum Erstellen eines Clusters oder einen vorhandenen Cluster verwenden, können Sie Ihren Verbindungsendpunkt kopieren und einfügen oder ihn aus Ihrem Abonnement auswählen. 

Bevor Sie beginnen, öffnen Sie im Projektmappen-Explorer „FabrikamFiber.Web“ > „PackageRoot“ > „ServiceManifest.xml“. Notieren Sie sich den Port für das unter **Endpunkt** aufgeführte Web-Front-End. 

Beim Erstellen des Clusters: 

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Anwendungsprojekt **FabrikamFiber.CallCenterApplication**, und klicken Sie anschließend auf **Veröffentlichen**.

2. Melden Sie sich unter Verwendung Ihres Azure-Kontos an, um auf Ihre Abonnements zugreifen zu können. 

3. Wählen Sie in der Dropdownliste **Verbindungsendpunkt** die Option **Neuen Cluster erstellen...** aus.    
        
4. Ändern Sie im Dialogfeld **Cluster erstellen** die folgenden Einstellungen:

    a. Geben Sie im Feld **Clustername** den Namen Ihres Clusters sowie das gewünschte Abonnement und den gewünschten Standort an. Notieren Sie sich den Namen Ihrer Clusterressourcengruppe.

    b. Optional: Ändern Sie ggf. die Knotenanzahl. Standardmäßig sind drei Knoten vorhanden (die Mindestanzahl zum Testen von Service Fabric-Szenarien).

    c. Klicken Sie auf die Registerkarte **Zertifikat**. Geben Sie auf dieser Registerkarte ein Kennwort ein, um das Zertifikat Ihres Clusters zu schützen. Dieses Zertifikat trägt zum Schutz Ihres Clusters bei. Sie können auch den Pfad ändern, unter dem das Zertifikat gespeichert werden soll. Visual Studio kann das Zertifikat auch für Sie importieren. Dieser Schritt ist erforderlich, um die Anwendung im Cluster bereitstellen zu können.

    d. Klicken Sie auf die Registerkarte **VM-Detail**. Geben Sie das Kennwort an, das Sie für die virtuellen Computer verwenden möchten, die den Cluster bilden. Die Kombination aus Benutzername und Kennwort kann zum Herstellen einer Remoteverbindung mit dem virtuellen Computer verwendet werden. Wählen Sie außerdem eine VM-Größe aus, und ändern Sie bei Bedarf das VM-Image. 

    > [!IMPORTANT]
    >Wählen Sie eine SKU, die die Ausführung von Containern unterstützt. Das Windows Server-Betriebssystem auf Ihren Clusterknoten muss mit dem Windows Server-Betriebssystem Ihres Containers kompatibel sein. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Windows](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). In diesem Tutorial wird standardmäßig ein auf Windows Server 2016 LTSC basierendes Docker-Image erstellt. Auf diesem Image basierende Container können in Clustern ausgeführt werden, die mit Windows Server 2016 Datacenter mit Containern erstellt wurden. Wenn Sie jedoch einen Cluster erstellen oder einen vorhandenen Cluster verwenden, der auf Windows Server Datacenter Core 1709 mit Containern basiert, müssen Sie das Windows Server-Betriebssystemimage ändern, auf dem der Container basiert. Öffnen Sie die **Dockerfile**-Datei im Projekt **FabrikamFiber.Web**, kommentieren Sie die vorhandene `FROM`-Anweisung (basierend auf `windowsservercore-ltsc`) aus, und heben Sie die Auskommentierung der `FROM`-Anweisung basierend auf `windowsservercore-1709` auf. 

    e. Listen Sie auf der Registerkarte **Erweitert** den Anwendungsport auf, der bei der Clusterbereitstellung im Lastenausgleichsmodul geöffnet sein soll. Dies ist der Port, den Sie sich vor der Erstellung des Clusters notiert haben. Sie können auch einen vorhandenen Application Insights-Schlüssel hinzufügen, der als Routingziel für Anwendungsprotokolldateien verwendet werden soll.

    f. Klicken Sie nach Abschluss der Einstellungsänderungen auf die Schaltfläche **Erstellen**. 
1. Die Erstellung kann einige Minuten dauern. Im Ausgabefenster wird eine entsprechende Information angezeigt, wenn der Cluster erstellt wurde.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Zulassen des Zugriffs auf die SQL-Datenbank durch die in Azure ausgeführte Anwendung
Sie haben zuvor eine SQL-Firewallregel erstellt, um Ihrer lokal ausgeführten Anwendung den Zugriff zu gewähren.  Als Nächstes müssen Sie der in Azure ausgeführten Anwendung den Zugriff auf die SQL-Datenbank erteilen.  Erstellen Sie einen [VNET-Dienstendpunkt](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) für den Service Fabric-Cluster und anschließend eine Regel, um diesem Endpunkt den Zugriff auf die SQL-Datenbank zu gewähren. Geben Sie unbedingt die Variable der Clusterressourcengruppe an, die Sie beim Erstellen des Clusters notiert haben. 

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Bereitstellen der Anwendung für Azure
Nachdem die Anwendung nun bereit ist, können Sie sie direkt aus Visual Studio im Cluster in Azure bereitstellen.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Anwendungsprojekt **FabrikamFiber.CallCenterApplication**, und klicken Sie anschließend auf **Veröffentlichen**.  Wählen Sie unter **Verbindungsendpunkt** den Endpunkt des zuvor erstellten Clusters aus.  Wählen Sie unter **Azure Container Registry** die zuvor erstellte Containerregistrierung aus.  Klicken Sie auf **Veröffentlichen**, um die Anwendung im Cluster in Azure bereitzustellen.

![Veröffentlichen der Anwendung][publish-app]

Im Fenster „Ausgabe“ können Sie den Fortschritt der Bereitstellung nachverfolgen.  Öffnen Sie nach der Bereitstellung der Anwendung einen Browser, und geben Sie die Clusteradresse und den Anwendungsport ein. Beispiel: http:\//fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Fabrikam-Webbeispiel][fabrikam-web-page-deployed]

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Einrichten von Continuous Integration und Continuous Deployment (CI/CD) mit einem Service Fabric-Cluster
Informationen zum Konfigurieren der CI/CD-Anwendungsbereitstellung für einen Service Fabric-Cluster mithilfe von Azure DevOps finden Sie unter [Tutorial: Bereitstellen einer Anwendung mit CI/CD in einem Service Fabric-Cluster](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). Der in dem Tutorial beschriebene Vorgang gilt auch für dieses Projekt (FabrikamFiber). Überspringen Sie einfach den Download des Voting-Beispiels, und ersetzen Sie den Repositorynamen „Voting“ durch „FabrikamFiber“.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Entfernen Sie alle erstellten Ressourcen, wenn Sie fertig sind.  Am einfachsten ist es, die Ressourcengruppen zu entfernen, die den Service Fabric-Cluster, Azure SQL-Datenbank und Azure Container Registry enthalten.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Packen einer vorhandenen Anwendung in einen Container mithilfe von Visual Studio
> * Erstellen einer Azure SQL-Datenbank
> * Erstellen einer Azure-Containerregistrierung
> * Bereitstellen einer Service Fabric-Anwendung in Azure

Im nächsten Teil des Tutorials erfahren Sie, wie Sie [Eine Containeranwendung mit CI/CD in einem Service Fabric-Cluster bereitstellen](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
m-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
