---
title: Bereitstellen der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Hier finden Sie sämtliche Schritte zum Bereitstellen der Azure-Dateisynchronisierung, die Sie im Azure-Portal, mithilfe von PowerShell oder mit der Azure-Befehlszeilenschnittstelle ausführen können.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4814a12a870d5317ad91c3514327ba0daad7ed69
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225369"
---
# <a name="deploy-azure-file-sync"></a>Bereitstellen der Azure-Dateisynchronisierung
Mit der Azure-Dateisynchronisierung können Sie die Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Mit der Azure-Dateisynchronisierung werden Ihre Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Sie können weltweit so viele Caches wie nötig nutzen.

Es wird dringend empfohlen, die Anleitungen [Planning for an Azure Files deployment](storage-files-planning.md) (Planung für eine Azure Files-Bereitstellung, in englischer Sprache) und [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md) zu lesen, bevor Sie die in diesem Artikel beschriebenen Schritte ausführen.

## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Eine Azure-Dateifreigabe in derselben Region, in der Sie die Azure-Dateisynchronisierung bereitstellen möchten. Weitere Informationen finden Sie unter
    - [Region availability](storage-sync-files-planning.md#azure-file-sync-region-availability) (Regionale Verfügbarkeit, in englischer Sprache) für die Azure-Dateisynchronisierung.
    - Eine Schritt-für-Schritt-Beschreibung zum Erstellen einer Dateifreigabe finden Sie unter [Erstellen einer Dateifreigabe](storage-how-to-create-file-share.md).
1. Mindestens eine unterstützte Instanz von Windows Server oder Windows Server-Cluster für die Synchronisierung mit der Azure-Dateisynchronisierung. Weitere Informationen zu unterstützten Versionen von Windows Server und empfohlenen Systemressourcen finden Sie unter [Überlegungen zu Windows-Dateiservern](storage-sync-files-planning.md#windows-file-server-considerations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Eine Azure-Dateifreigabe in derselben Region, in der Sie die Azure-Dateisynchronisierung bereitstellen möchten. Weitere Informationen finden Sie unter
    - [Region availability](storage-sync-files-planning.md#azure-file-sync-region-availability) (Regionale Verfügbarkeit, in englischer Sprache) für die Azure-Dateisynchronisierung.
    - Eine Schritt-für-Schritt-Beschreibung zum Erstellen einer Dateifreigabe finden Sie unter [Erstellen einer Dateifreigabe](storage-how-to-create-file-share.md).
1. Mindestens eine unterstützte Instanz von Windows Server oder Windows Server-Cluster für die Synchronisierung mit der Azure-Dateisynchronisierung. Weitere Informationen zu unterstützten Versionen von Windows Server und empfohlenen Systemressourcen finden Sie unter [Überlegungen zu Windows-Dateiservern](storage-sync-files-planning.md#windows-file-server-considerations).

1. Das Azure PowerShell-Modul kann mit PowerShell 5.1 oder PowerShell 6+ verwendet werden. Sie können das Azure PowerShell-Modul für Azure-Dateisynchronisierung auf jedem unterstützten System verwenden, auch auf Nicht-Windows-Systemen. Aber das Cmdlet für die Serverregistrierung muss immer direkt für die Windows Server-Instanz ausgeführt werden, die Sie registrieren (entweder direkt oder per PowerShell-Remoting). Unter Windows Server 2012 R2 können Sie anhand des Werts der **PSVersion**-Eigenschaft des **$PSVersionTable**-Objekts überprüfen, ob mindestens PowerShell 5.1\* ausgeführt wird:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Wenn die **PSVersion** niedriger als5.1.\* ist, wie dies bei den meisten neuen Installationen von Windows Server 2012 R2 der Fall ist, können Sie problemlos ein Upgrade ausführen, indem Sie [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) herunterladen und installieren. Das Paket, das für Windows Server 2012 R2 heruntergeladen und installiert werden sollte, lautet **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**. 

    PowerShell 6+ kann mit jedem unterstützten System verwendet und über seine [GitHub-Seite](https://github.com/PowerShell/PowerShell#get-powershell) heruntergeladen werden. 

    > [!Important]  
    > Wenn Sie die Benutzeroberfläche für Serverregistrierung verwenden möchten, statt direkt aus PowerShell zu registrieren, müssen Sie PowerShell 5.1 verwenden.

1. Wenn Sie entschieden haben, PowerShell 5.1 zu verwenden, müssen Sie sich vergewissern, dass mindestens .NET 4.7.2 installiert ist. Erfahren Sie mehr über [.NET Framework-Versionen und -Abhängigkeiten](/dotnet/framework/migration-guide/versions-and-dependencies) auf Ihrem System.

    > [!Important]  
    > Wenn Sie .NET 4.7.2+ auf dem Windows Server Core installieren, müssen Sie die Installation mit den Flags `quiet` und `norestart` durchführen. Andernfalls wird die Installation fehlschlagen. Wenn Sie beispielsweise .NET 4.8 installieren, würde der Befehl wie folgt aussehen:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Das PowerShell-Modul „Az“, das installiert werden kann, indem die folgenden Anweisungen ausgeführt werden: [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Bei der Installation des Az PowerShell-Moduls wird das Az.StorageSync-Modul nun automatisch installiert.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Eine Azure-Dateifreigabe in derselben Region, in der Sie die Azure-Dateisynchronisierung bereitstellen möchten. Weitere Informationen finden Sie unter
    - [Region availability](storage-sync-files-planning.md#azure-file-sync-region-availability) (Regionale Verfügbarkeit, in englischer Sprache) für die Azure-Dateisynchronisierung.
    - Eine Schritt-für-Schritt-Beschreibung zum Erstellen einer Dateifreigabe finden Sie unter [Erstellen einer Dateifreigabe](storage-how-to-create-file-share.md).
1. Mindestens eine unterstützte Instanz von Windows Server oder Windows Server-Cluster für die Synchronisierung mit der Azure-Dateisynchronisierung. Weitere Informationen zu unterstützten Versionen von Windows Server und empfohlenen Systemressourcen finden Sie unter [Überlegungen zu Windows-Dateiservern](storage-sync-files-planning.md#windows-file-server-considerations).

1. [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

   Wenn Sie möchten, können Sie auch Azure Cloud Shell verwenden, um die Schritte in diesem Tutorial auszuführen.  Azure Cloud Shell ist eine interaktive Shellumgebung, die Sie über Ihren Browser nutzen können.  Starten Sie Cloud Shell mit einer der folgenden Methoden:

   - Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**. Mit **Ausprobieren** wird Azure Cloud Shell geöffnet, der Code wird jedoch nicht automatisch in Cloud Shell kopiert.

   - Öffnen Sie Cloud Shell, indem Sie zu [https://shell.azure.com](https://shell.azure.com) navigieren.

   - Wählen Sie im [Azure-Portal](https://portal.azure.com) rechts oben im Menü die Schaltfläche **Cloud Shell** aus.

1. Melden Sie sich an.

   Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az-login) an, falls Sie eine lokale Installation der Befehlszeilenschnittstelle verwenden.

   ```azurecli
   az login
   ```

    Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.

1. Installieren Sie die Erweiterung [az filesync](/cli/azure/ext/storagesync/storagesync) für die Azure-Befehlszeilenschnittstelle.

   ```azurecli
   az extension add --name storagesync
   ```

   Nachdem Sie den Verweis auf die Erweiterung **storagesync** installiert haben, wird folgende Warnung angezeigt.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Vorbereiten von Windows Server für die Verwendung mit der Azure-Dateisynchronisierung
Deaktivieren Sie für jeden Server, den Sie mit der Azure-Dateisynchronisierung verwenden möchten, einschließlich aller Serverknoten in einem Failovercluster, die **Verstärkte Sicherheitskonfiguration für Internet Explorer**. Dies ist nur für die anfängliche Serverregistrierung erforderlich. Sie können sie nach dem Registrieren des Servers erneut aktivieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Sie können diesen Schritt überspringen, wenn Sie Azure File Sync auf dem Windows Server Core bereitstellen.

1. Öffnen Sie den Server-Manager.
2. Klicken Sie auf **Lokaler Server**:  
    ![„Lokaler Server“ auf der linken Seite der Server Manager-Benutzeroberfläche](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Wählen Sie im Unterbereich **Eigenschaften** den Link für **Verstärkte Sicherheitskonfiguration für IE** aus.  
    ![Bereich „Verstärkte Sicherheitskonfiguration für IE“ auf der Server Manager-Benutzeroberfläche](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Wählen Sie im Dialogfeld **Verstärkte Sicherheitskonfiguration für Internet Explorer** für **Administratoren** und **Benutzer** **Aus** aus:  
    ![Popupfenster „Verstärkte Sicherheitskonfiguration für Internet Explorer“, in dem „Aus“ gewählt ist](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Um die Verstärkte Sicherheitskonfiguration für Internet Explorer zu deaktivieren, führen Sie Folgendes in einer PowerShell-Sitzung mit erhöhten Rechten aus:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Befolgen Sie die Anweisungen für das Azure-Portal oder PowerShell.

---

## <a name="deploy-the-storage-sync-service"></a>Bereitstellen des Speichersynchronisierungsdiensts 
Die Bereitstellung der Azure-Dateisynchronisierung beginnt mit der Platzierung einer **Speichersynchronisierungsdienst**-Ressource in einer Ressourcengruppe Ihres ausgewählten Abonnements. Sie sollten von diesen Ressourcen so wenig wie möglich bereitstellen. Sie schaffen eine Vertrauensstellung zwischen Ihren Servern und dieser Ressource, und ein Server kann nur bei einem Speichersynchronisierungsdienst registriert werden. Darum sollten Sie so viele Speichersynchronisierungsdienste bereitstellen, wie Sie benötigen, um Gruppen von Servern zu trennen. Bedenken Sie, dass Server aus verschiedenen Speichersynchronisierungsdiensten nicht miteinander synchronisiert werden können.

> [!Note]
> Der Speichersynchronisierungsdienst hat Zugriffsberechtigungen aus dem Abonnement und der Ressourcengruppe geerbt, in der er bereitgestellt wurde. Sie sollten sorgfältig überprüfen, wer darauf zugreifen darf. Entitäten mit Schreibzugriff können die Synchronisierung neuer Sätze von Dateien aus Server starten, die bei diesem Speichersynchronisierungsdienst registriert sind, und bewirken, dass Daten in Azure-Speicher übertragen werden, auf den sie zugreifen können.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigieren Sie zum Bereitstellen eines Speichersynchronisierungsdiensts zum [Azure-Portal](https://portal.azure.com/), klicken Sie auf *Ressource erstellen*, und suchen Sie dann nach „Azure-Dateisynchronisierung“. Wählen Sie in den Suchergebnissen **Azure-Dateisynchronisierung** aus, und klicken Sie auf **Erstellen**, um die Registerkarte **Speichersynchronisierung bereitstellen** zu öffnen.

Geben Sie in dem neuen Bereich, der geöffnet wird, Folgendes ein:

- **Name**: Ein eindeutiger Name (pro Region) für den Speichersynchronisierungsdienst.
- **Abonnement**: Das Abonnement, in dem Sie den Speichersynchronisierungsdienst erstellen möchten. Abhängig von der Konfigurationsstrategie Ihrer Organisation haben Sie möglicherweise Zugriff auf ein oder mehrere Abonnements. Die Abrechnung für die einzelnen Clouddienste (z.B. Azure Files) erfolgt innerhalb eines Azure-Abonnements.
- **Ressourcengruppe**: Eine Ressourcengruppe ist eine logische Gruppe von Azure-Ressourcen, z.B. ein Speicherkonto oder Speichersynchronisierungsdienst. Sie können für die Azure-Dateisynchronisierung eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe auswählen. (Wir empfehlen, Ressourcengruppen als Container zu verwenden, um Ressourcen für Ihre Organisation logisch voneinander zu trennen, etwa indem Sie alle Personalressourcen oder alle Ressourcen für ein bestimmtes Projekt gruppieren.)
- **Standort**: Die Region, in der Sie die Azure-Dateisynchronisierung bereitstellen möchten. In dieser Liste sind nur die unterstützten Regionen verfügbar.

Wenn Sie fertig sind, wählen Sie **Erstellen** aus, um den Speichersynchronisierungsdienst bereitzustellen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ersetzen Sie `<Az_Region>`, `<RG_Name>` und `<my_storage_sync_service>` durch Ihre eigenen Werte. Verwenden Sie anschließend die folgenden Befehle, um einen Speichersynchronisierungsdienst zu erstellen und bereitzustellen:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Befolgen Sie die Anweisungen für das Azure-Portal oder PowerShell.

---

## <a name="install-the-azure-file-sync-agent"></a>Installieren des Azure-Dateisynchronisierungs-Agents
Der Azure-Dateisynchronisierungs-Agent ist ein herunterladbares Paket, mit dem ein Windows Server-Computer mit einer Azure-Dateifreigabe synchronisiert werden kann. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Sie können den Agent aus dem [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) herunterladen. Doppelklicken Sie nach Abschluss des Downloads auf das MSI-Paket, um die Installation des Azure-Dateisynchronisierungs-Agents zu starten.

> [!Important]  
> Wenn Sie die Azure-Dateisynchronisierung mit einem Failovercluster verwenden möchten, muss der Azure-Dateisynchronisierungs-Agent auf jedem Knoten im Cluster installiert werden. Jeder Knoten im Cluster muss registriert werden, um mit der Azure-Dateisynchronisierung verwendet werden zu können.

Folgendes Vorgehen wird empfohlen:
- Übernehmen Sie den Standardinstallationspfad („C:\Programme\Microsoft Files\Azure\StorageSyncAgent“), um die Problembehandlung und Serverwartung zu vereinfachen.
- Aktivieren Sie Microsoft Update, um die Azure-Dateisynchronisierung auf dem aktuellen Stand zu halten. Alle Updates des Azure-Dateisynchronisierungs-Agents, einschließlich Funktionsupdates und Hotfixe, erfolgen über Microsoft Update. Es wird empfohlen, das neueste Update der Azure-Dateisynchronisierung zu installieren. Weitere Informationen finden Sie unter [Updaterichtlinie für Azure-Dateisynchronisierung](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Wenn die Installation des Azure-Dateisynchronisierungs-Agents abgeschlossen ist, wird automatisch die Benutzeroberfläche der Serverregistrierung geöffnet. Vor der Registrierung benötigen Sie einen Speichersynchronisierungsdienst. Wie Sie einen Speichersynchronisierungsdienst erstellen, erfahren Sie im nächsten Abschnitt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Führen Sie den folgenden PowerShell-Code aus, um die entsprechende Version des Azure-Dateisynchronisierungs-Agents für Ihr Betriebssystem herunterzuladen und auf Ihrem System zu installieren.

> [!Important]  
> Wenn Sie die Azure-Dateisynchronisierung mit einem Failovercluster verwenden möchten, muss der Azure-Dateisynchronisierungs-Agent auf jedem Knoten im Cluster installiert werden. Jeder Knoten im Cluster muss registriert werden, um mit der Azure-Dateisynchronisierung verwendet werden zu können.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Befolgen Sie die Anweisungen für das Azure-Portal oder PowerShell.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrieren eines Windows-Servers beim Speichersynchronisierungsdienst
Durch das Registrieren des Windows-Servers bei einem Speichersynchronisierungsdienst wird eine Vertrauensstellung zwischen dem Server (oder Cluster) und dem Speichersynchronisierungsdienst geschaffen. Ein Server kann nur bei einem Speichersynchronisierungsdienst registriert und mit anderen Servern und Azure-Dateifreigaben synchronisiert werden, die demselben Speichersynchronisierungsdienst zugeordnet sind.

> [!Note]
> Die Serverregistrierung verwendet Ihre Azure-Anmeldeinformationen zum Erstellen einer Vertrauensstellung zwischen dem Speichersynchronisierungsdienst und Ihrer Windows Server-Instanz, doch anschließend erstellt der Server seine eigene Identität, die gültig ist, solange der Server registriert bleibt und das aktuelle Shared Access Signature-Token (Speicher-SAS) gültig ist, und verwendet sie. Sobald die Registrierung des Servers aufgehoben ist, kann kein neues SAS-Token an den Server ausgegeben werden, sodass der Server nicht mehr auf Ihre Azure-Dateifreigaben zugreifen kann und damit jede Synchronisierung beendet wird.

Der Administrator, der den Server registriert, muss Mitglied der Verwaltungsrollen **Besitzer** oder **Mitwirkender** für den angegebenen Speichersynchronisierungsdienst sein. Dies kann unter **Access Control (IAM)** im Azure-Portal für den Speichersynchronisierungsdienst konfiguriert werden.

Es ist auch möglich, Administratoren, die Server registrieren können, von solchen zu unterscheiden, die außerdem die Synchronisierung konfigurieren dürfen in einem Speichersynchronisierungsdienst. Hierfür müssen Sie eine benutzerdefinierte Rolle erstellen, in der Sie die Administratoren auflisten, die nur zum Registrieren von Servern berechtigt sind, und dieser benutzerdefinierten Rolle müssen Sie die folgenden Berechtigungen erteilen:

* „Microsoft.StorageSync/storageSyncServices/registeredServers/write“
* „Microsoft.StorageSync/storageSyncServices/read“
* „Microsoft.StorageSync/storageSyncServices/workflows/read“
* „Microsoft.StorageSync/storageSyncServices/workflows/operations/read“

# <a name="portal"></a>[Portal](#tab/azure-portal)
Die Benutzeroberfläche der Serverregistrierung sollte sich nach der Installation des Azure-Dateisynchronisierungs-Agents automatisch öffnen. Wenn dies nicht der Fall ist, können Sie sie aus ihrem Dateispeicherort manuell öffnen: „C:\Programme\Azure\StorageSyncAgent\ServerRegistration.exe“. Wenn die Benutzeroberfläche der Serverregistrierung geöffnet ist, wählen Sie **Anmelden** aus, um mit der Registrierung zu beginnen.

Nach der Anmeldung werden die folgenden Informationen abgefragt:

![Screenshot der Serverregistrierungs-Benutzeroberfläche](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-Abonnement**: Das Abonnement, das den Speichersynchronisierungsdienst enthält (siehe [Bereitstellen des Speichersynchronisierungsdiensts](#deploy-the-storage-sync-service)). 
- **Ressourcengruppe**: Die Ressourcengruppe, die den Speichersynchronisierungsdienst enthält.
- **Speichersynchronisierungsdienst**: Der Name des Speichersynchronisierungsdiensts, bei dem Sie den Server registrieren möchten.

Nachdem Sie die entsprechenden Informationen ausgewählt haben, wählen Sie **Registrieren** aus, um die Serverregistrierung abzuschließen. Während des Registrierungsvorgangs werden Sie erneut aufgefordert, sich anzumelden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Befolgen Sie die Anweisungen für das Azure-Portal oder PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Erstellen einer Synchronisierungsgruppe und eines Cloudendpunkts
Eine Synchronisierungsgruppe definiert die Synchronisierungstopologie für einen Satz von Dateien. Endpunkte innerhalb einer Synchronisierungsgruppe bleiben miteinander synchron. Eine Synchronisierungsgruppe muss einen Cloudendpunkt enthalten, der eine Azure-Dateifreigabe und einen oder mehrere Serverendpunkte darstellt. Ein Serverendpunkt stellt einen Pfad auf einem registrierten Server dar. Ein Server kann in mehreren Synchronisierungsgruppen über Serverendpunkte verfügen. Sie können so viele Synchronisierungsgruppen erstellen, wie Sie für die angemessene Beschreibung Ihrer gewünschten Synchronisierungstopologie benötigen.

Ein Cloudendpunkt ist ein Zeiger auf eine Azure-Dateifreigabe. Alle Serverendpunkte werden mit einem Cloudendpunkt synchronisiert, sodass der Cloudendpunkt zum Hub wird. Das Speicherkonto für die Azure-Dateifreigabe muss sich in der gleichen Region wie der Speichersynchronisierungsdienst befinden. Mit einer Ausnahme wird die gesamte Azure-Dateifreigabe synchronisiert: Ein besonderer Ordner, vergleichbar mit dem versteckten Ordner „System Volume Information“ auf einem NTFS-Volume, wird bereitgestellt. Dieses Verzeichnis heißt „.SystemShareInformation“. Er enthält wichtige Synchronisierungsmetadaten, die nicht mit anderen Endpunkten synchronisiert werden. Sie dürfen ihn weder verwenden noch löschen!

> [!Important]  
> Sie können Änderungen an jedem Cloudendpunkt oder Serverendpunkt in der Synchronisierungsgruppe vornehmen und Ihre Dateien mit den anderen Endpunkten in der Synchronisierungsgruppe synchronisieren. Wenn Sie eine Änderung direkt am Cloudendpunkt (Azure-Dateifreigabe) vornehmen, müssen Änderungen zunächst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen entdeckt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird nur einmal alle 24 Stunden gestartet. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu Azure Files](storage-files-faq.md#afs-change-detection).

Der Administrator, der den Cloudendpunkt erstellt, muss Mitglied der Verwaltungsrolle **Besitzer** für das Speicherkonto sein, das die Azure-Dateifreigabe enthält, auf die der Cloudendpunkt verweist. Dies kann unter **Access Control (IAM)** im Azure-Portal für das Speicherkonto konfiguriert werden.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Um eine Synchronisierungsgruppe zu erstellen, navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Speichersynchronisierungsdienst, und wählen Sie dann **+ Synchronisierungsgruppe** aus:

![Erstellen einer neuen Synchronisierungsgruppe im Azure-Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Geben Sie im Bereich, der jetzt geöffnet wird, die folgenden Informationen ein, um eine Synchronisierungsgruppe bei einem Cloudendpunkt zu erstellen:

- **Name der Synchronisierungsgruppe**: Der Name der zu erstellenden Synchronisierungsgruppe. Dieser Name muss innerhalb des Speichersynchronisierungsdiensts eindeutig sein, es kann jedoch ein beliebiger Name sein, der für Sie Sinn ergibt.
- **Abonnement**: Das Abonnement, in dem Sie unter [Bereitstellen des Speichersynchronisierungsdiensts](#deploy-the-storage-sync-service) weiter oben den Speichersynchronisierungsdienst bereitgestellt haben.
- **Speicherkonto**: Wenn Sie **Speicherkonto auswählen** auswählen, wird ein weiterer Bereich angezeigt, in dem Sie das Speicherkonto auswählen können, das die Azure-Dateifreigabe enthält, mit der Sie die Synchronisierung ausführen möchten.
- **Azure-Dateifreigabe**: Der Name der Azure-Dateifreigabe, mit der die Synchronisierung ausgeführt werden soll.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Um die Synchronisierungsgruppe zu erstellen, führen Sie den folgenden PowerShell-Befehl aus. Denken Sie daran, `<my-sync-group>` mit dem gewünschten Namen der Synchronisierungsgruppe zu ersetzen.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Nachdem die Synchronisierungsgruppe erfolgreich erstellt worden ist, können Sie Ihren Cloudendpunkt erstellen. Achten Sie darauf, dass Sie `<my-storage-account>` und `<my-file-share>` durch die erwarteten Werte ersetzen.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az storagesync sync-group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create), um eine neue Synchronisierungsgruppe zu erstellen.  Verwenden Sie [az configure](/cli/azure/reference-index#az-configure), um eine Ressourcengruppe für alle CLI-Befehle als Standard festzulegen.

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Verwenden Sie den Befehl [az storagesync sync-group cloud-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create), um einen neuen Cloudendpunkt zu erstellen.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Erstellen eines Serverendpunkts
Ein Serverendpunkt stellt einen bestimmten Speicherort auf einem registrierten Server dar, z. B. einen Ordner auf einem Servervolume. Ein Serverendpunkt muss ein Pfad auf einem registrierten Server sein (statt einer eingebundenen Freigabe), und um Cloudtiering verwenden zu können, darf der Pfad sich nicht auf einem Systemvolume befinden. Network Attached Storage (NAS) wird nicht unterstützt.

> [!NOTE]
> Das Ändern des Pfads oder Laufwerkbuchstabens nachdem Sie einen Serverendpunkt auf einem Volume erstellt haben, wird nicht unterstützt. Stellen Sie sicher, dass Sie einen endgültigen Pfad auf dem registrierten Server verwenden.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Um einen Serverendpunkt hinzuzufügen, wechseln Sie zu der neu erstellten Synchronisierungsgruppe, und wählen Sie dann **Serverendpunkt hinzufügen** aus.

![Hinzufügen eines neuen Serverendpunkts im Bereich „Synchronisierungsgruppe“](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Geben Sie im Bereich **Serverendpunkt hinzufügen** die folgenden Informationen ein, um einen Serverendpunkt zu erstellen:

- **Registrierter Server**: Der Name des Servers oder Clusters, auf bzw. in dem Sie den Serverendpunkt erstellen möchten.
- **Pfad**: Der Windows Server-Pfad, der als Teil der Synchronisierungsgruppe synchronisiert werden soll.
- **Cloudtiering**: Ein Schalter, mit dem Cloudtiering aktiviert oder deaktiviert wird. Mit Cloudtiering kann für selten verwendete oder selten einem Zugriff ausgesetzte Dateien Tiering nach Azure Files festgelegt werden.
- **Freier Speicherplatz auf Volume**: Die Menge an freiem Speicherplatz auf dem Volume, auf dem sich der Serverendpunkt befindet, die reserviert werden soll. Wenn z.B. für ein Volume mit einem einzigen Serverendpunkt „Freier Speicherplatz auf Volume“ auf 50 % festgelegt ist, wird ungefähr die Hälfte der Daten nach Azure Files ausgelagert. Die Azure-Dateifreigabe enthält immer eine vollständige Kopie der Daten in der Synchronisierungsgruppe, unabhängig davon, ob Cloudtiering aktiviert ist.
- **Anfänglicher Downloadmodus**: Dies ist eine optionale Auswahl, beginnend mit der Agent-Version 11, die hilfreich sein kann, wenn Dateien in der Azure-Dateifreigabe, aber nicht auf dem Server vorhanden sind. Eine solche Situation kann beispielsweise auftreten, wenn Sie einen Serverendpunkt erstellen, um einer Synchronisierungsgruppe einen weiteren Zweigstellenserver hinzuzufügen, oder wenn Sie die Notfallwiederherstellung für einen ausgefallenen Server durchführen. Wenn das Cloudtiering aktiviert ist, wird standardmäßig nur der Namespace abgerufen, anfangs kein Dateiinhalt. Dies ist hilfreich, wenn Sie der Ansicht sind, dass eher Benutzerzugriffsanforderungen entscheiden sollen, welche Dateiinhalte auf den Server abgerufen werden. Wenn das Cloudtiering deaktiviert ist, ist der Standard, dass der Namespace zuerst heruntergeladen wird, und anschließend werden Dateien auf Grundlage des Zeitstempels der letzten Änderung abgerufen, bis die lokale Kapazität erreicht ist. Sie können den anfänglichen Downloadmodus jedoch in „Nur Namespace“ ändern. Ein dritter Modus kann nur verwendet werden, wenn das Cloudtiering für diesen Serverendpunkt deaktiviert ist. In diesem Modus wird vermieden, den Namespace zuerst abzurufen. Dateien werden nur auf dem lokalen Server angezeigt, wenn Sie vollständig heruntergeladen werden konnten. Dieser Modus ist nützlich, wenn eine Anwendung beispielsweise erfordert, dass vollständige Dateien vorhanden sind, und keine mehrstufige Dateien in ihrem Namespace tolerieren kann.

Wählen Sie **Erstellen** aus, um den Serverendpunkt hinzuzufügen. Ihre Dateien bleiben jetzt zwischen der Azure-Dateifreigabe und Windows Server synchron. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Führen Sie die folgenden PowerShell-Befehle aus, um den Serverendpunkt zu erstellen, und achten Sie darauf, dass Sie `<your-server-endpoint-path>` und `<your-volume-free-space>` durch die gewünschten Werte ersetzen und die optionale Einstellung für die optionale anfängliche Downloadrichtlinie überprüfen.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az storagesync sync-group server-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create), um einen neuen Serverendpunkt zu erstellen.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Konfigurieren von Firewall- und VNET-Einstellungen

### <a name="portal"></a>Portal
Wenn Sie Ihre Azure-Dateisynchronisierung mit Firewall- und VNET-Einstellungen konfigurieren möchten, gehen Sie wie folgt vor:

1. Navigieren Sie im Azure-Portal zu dem Speicherkonto, das Sie schützen möchten.
1. Wählen Sie im linken Menü die Option **Firewalls und virtuelle Netzwerke** aus:
1. Wählen Sie unter **Zugriff erlauben von** die Option **Ausgewählte Netzwerke** aus.
1. Vergewissern Sie sich, dass die IP-Adresse oder das virtuelle Netzwerk Ihres Servers unter dem entsprechenden Abschnitt aufgeführt ist.
1. Vergewissern Sie sich, dass das Kontrollkästchen **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktiviert ist.
1. Klicken Sie auf **Save** (Speichern), um Ihre Einstellungen zu speichern.

![Konfigurieren von Firewall- und VNET-Einstellungen für die Azure-Dateisynchronisierung](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Onboarding bei der Azure-Dateisynchronisierung
Zum erstmaligen Onboarding bei der Azure-Dateisynchronisierung ohne Ausfallzeiten unter Beibehaltung der vollen Dateitreue und Zugriffssteuerungsliste (ACL) werden folgende Schritte empfohlen:
 
1. Stellen Sie einen Speichersynchronisierungsdienst bereit.
1. Erstellen Sie eine Synchronisierungsgruppe.
1. Installieren Sie den Azure-Dateisynchronisierungs-Agent auf dem Server mit dem vollständigen Dataset.
1. Registrieren Sie diesen Server, und erstellen Sie einen Serverendpunkt in der Freigabe. 
1. Lassen Sie die Synchronisierung den vollständigen Upload in die Azure-Dateifreigabe (den Cloudendpunkt) durchführen.  
1. Installieren Sie nach Abschluss des anfänglichen Uploads den Azure-Dateisynchronisierungs-Agent auf allen verbleibenden Servern.
1. Erstellen Sie auf den verbleibenden Servern neue Dateifreigaben.
1. Erstellen Sie nach Wunsch Serverendpunkte in neuen Dateifreigaben mit der Cloudtieringrichtlinie. (Für diesen Schritt muss zusätzlicher Speicherplatz für das anfängliche Setup zur Verfügung stehen.)
1. Lassen Sie den Azure-Dateisynchronisierungs-Agent eine schnelle Wiederherstellung des gesamten Namespace ohne die eigentliche Datenübertragung durchführen. Nach der vollständigen Synchronisierung des Namespace füllt das Synchronisierungsmodul den lokalen Speicherplatz basierend auf der Cloudtieringrichtlinie für den Serverendpunkt auf. 
1. Vergewissern Sie sich, dass die Synchronisierung abgeschlossen wird, und testen Sie Ihre Topologie bei Bedarf. 
1. Leiten Sie Benutzer und Anwendungen zu dieser neuen Freigabe um.
1. Optional können Sie duplizierte Freigaben auf den Servern löschen.
 
Wenn Sie nicht über zusätzlichen Speicherplatz für das erste Onboarding verfügen und an die vorhandenen Freigaben anhängen möchten, können Sie für die Daten in den Azure-Dateifreigaben vorab ein Seeding ausführen. Dieser Ansatz wird ausdrücklich nur dann empfohlen, wenn Sie Ausfallzeiten akzeptieren und absolut keine Datenveränderungen auf den Serverfreigaben während des ersten Onboardingprozesses garantieren können. 
 
1. Stellen Sie sicher, dass sich die Daten auf den Servern während des Onboardingprozesses nicht ändern können.
1. Führen Sie auf den Azure-Dateifreigaben vorab ein Seeding mit den Serverdaten mithilfe eines Datenübertragungstools über den SBM durch. Beispiel: Robocopy. Sie können auch AzCopy über REST verwenden. Achten Sie darauf, AzCopy mit den entsprechenden Schaltern zu verwenden, um die Zeitstempel und Attribute der ACLs zu erhalten.
1. Erstellen Sie eine Azure-Dateisynchronisierungstopologie mit den gewünschten Serverendpunkten, die auf die vorhandenen Freigaben zeigen.
1. Lassen Sie die Synchronisierung den Abstimmungsprozess auf allen Endpunkten abschließen. 
1. Sobald die Abstimmung abgeschlossen ist, können Sie Freigaben für Änderungen öffnen.
 
Für das Vorabseeding gelten aktuell einige Einschränkungen: 
- Datenänderungen auf dem Server, bevor die Synchronisierungstopologie vollständig in Betrieb ist, können zu Konflikten auf den Serverendpunkten führen.  
- Nachdem der Cloudendpunkt erstellt wurde, führt die Azure-Dateisynchronisierung einen Prozess aus, um die Dateien in der Cloud zu erkennen, bevor die anfängliche Synchronisierung gestartet wird. Die zum Abschluss dieses Prozesses benötigte Zeit variiert je nach den verschiedenen Faktoren wie Netzwerkgeschwindigkeit, verfügbare Bandbreite und Anzahl der Dateien und Ordner. Grob geschätzt schafft der Erkennungsprozess in der Vorschauversion ca. 10 Dateien pro Sekunde. Selbst wenn das Vorabseeding schnell erfolgt, kann die Gesamtzeit bis zur Inbetriebnahme eines voll funktionsfähigen Systems erheblich länger sein, wenn für die Daten in der Cloud vorab ein Seeding durchgeführt wird.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Self-Service-Wiederherstellung mit „Vorherige Versionen“ und Volumeschattenkopie-Dienst (VSS)

> [!IMPORTANT]
> Die folgenden Informationen können nur für Version 9 (oder höhere Versionen) des Speichersynchronisierungs-Agents verwendet werden. Niedrigere Versionen als Version 9 verfügen nicht über die StorageSyncSelfService-Cmdlets.

„Vorherige Versionen“ ist ein Windows-Feature, mit dem Sie serverseitige VSS-Momentaufnahmen eines Volumes nutzen können, um wiederherstellbare Versionen einer Datei auf einem SMB-Client darzustellen.
Dies ermöglicht ein leistungsfähiges Szenario, das häufig als Self-Service-Wiederherstellung bezeichnet wird. Es ist für die direkte Nutzung durch Information-Worker gedacht, ohne dass die Wiederherstellung von einem IT-Administrator durchgeführt werden muss.

VSS-Momentaufnahmen und Vorherige Versionen funktionieren unabhängig von der Azure-Dateisynchronisierung. Das Cloudtiering muss aber auf einen kompatiblen Modus festgelegt werden. Auf einem Volume können mehrere Serverendpunkte der Azure-Dateisynchronisierung angeordnet sein. Sie müssen den folgenden PowerShell-Aufruf für jedes Volume durchführen. Dies gilt auch, wenn nur ein Serverendpunkt vorhanden ist, für den Sie das Cloudtiering nutzen möchten bzw. bereits nutzen.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS-Momentaufnahmen werden für ein gesamtes Volume erstellt. Standardmäßig können für ein bestimmtes Volume bis zu 64 Momentaufnahmen vorhanden sein, sofern genügend Platz für die Speicherung verfügbar ist. Dies wird von VSS automatisch durchgeführt. Beim Standardzeitplan für Momentaufnahmen werden zwei Momentaufnahmen pro Tag erstellt (am Montag und Freitag). Dieser Zeitplan kann über einen geplanten Windows-Task konfiguriert werden. Mit dem obigen PowerShell-Cmdlet werden zwei Schritte ausgeführt:
1. Das Cloudtiering der Azure-Dateisynchronisierung auf dem angegebenen Volume wird so konfiguriert, dass es mit vorherigen Versionen kompatibel ist. Hierbei wird sichergestellt, dass eine Datei auch dann aus einer vorherigen Version wiederhergestellt werden kann, wenn sie per Tiering auf dem Server in der Cloud ausgelagert wurde. 
1. Der Standardzeitplan für VSS wird aktiviert. Sie können dies später noch ändern. 

> [!Note]  
> Hierbei sind zwei wichtige Punkte zu beachten:
>- Wenn Sie den Parameter „-Force“ verwenden und VSS derzeit aktiviert ist, wird der aktuelle VSS-Zeitplan für Momentaufnahmen außer Kraft gesetzt und durch den Standardzeitplan ersetzt. Stellen Sie sicher, dass Sie Ihre benutzerdefinierte Konfiguration speichern, bevor Sie das Cmdlet ausführen.
> - Wenn Sie dieses Cmdlet auf einem Clusterknoten verwenden, müssen Sie es auch auf allen anderen Knoten im Cluster ausführen! 

Sie können das folgende Cmdlet ausführen, um zu ermitteln, ob Kompatibilität für die Self-Service-Wiederherstellung besteht.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Hiermit werden alle Volumes des Servers aufgelistet, und es wird jeweils die Anzahl von Tagen mit Kompatibilität für das Cloudtiering angegeben. Diese Anzahl wird basierend auf den maximal möglichen Momentaufnahmen pro Volume und dem Standardzeitplan für Momentaufnahmen automatisch berechnet. Standardmäßig können also alle vorherigen Versionen, die einem Information-Worker angezeigt werden, für die Wiederherstellung verwendet werden. Dies gilt auch, wenn Sie den Standardzeitplan ändern, um weitere Momentaufnahmen zu erstellen.
Falls Sie den Zeitplan aber so ändern, dass auf dem Volume eine Momentaufnahme verfügbar ist, die älter als die Anzahl von Kompatibilitätstagen ist, können Benutzer diese ältere Momentaufnahme (vorherige Version) nicht für die Wiederherstellung verwenden.

> [!Note]
> Die Aktivierung der Self-Service-Wiederherstellung kann sich auf den Verbrauch und die Kosten Ihres Azure-Speichers auswirken. Diese Auswirkung ist auf Dateien beschränkt, die derzeit auf dem Server ausgelagert sind. Durch die Aktivierung dieses Features wird sichergestellt, dass in der Cloud eine Dateiversion verfügbar ist, auf die über einen „Vorherige Versionen“-Eintrag (VSS-Momentaufnahme) verwiesen werden kann.
>
> Wenn Sie das Feature deaktivieren, nimmt der Verbrauch von Azure-Speicher langsam ab, bis das Zeitfenster der Kompatibilitätstage verstrichen ist. Es gibt keine Möglichkeit, diesen Vorgang zu beschleunigen. 

Die maximale Standardanzahl von VSS-Momentaufnahmen pro Volume (64) sowie der Standardzeitplan für deren Erstellung führt zu einem maximalen Zeitraum von 45 Tagen mit vorherigen Versionen, für die ein Information-Worker die Wiederherstellung durchführen kann. Dies hängt davon ab, wie viele VSS-Momentaufnahmen Sie auf Ihrem Volume speichern können.

Falls die maximale Anzahl von 64 VSS-Momentaufnahmen pro Volume für Sie keine geeignete Einstellung ist, können Sie [diesen Wert über einen Registrierungsschlüssel ändern](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Damit der neue Grenzwert wirksam wird, müssen Sie das Cmdlet erneut ausführen, um die Kompatibilität mit vorherigen Versionen auf jedem Volume, auf dem diese zuvor aktiviert waren, zu ermöglichen. Hierbei verwenden Sie das „-Force“-Flag, um die neue maximale Anzahl von VSS-Momentaufnahmen pro Volume zu berücksichtigen. Dies ergibt eine neu berechnete Anzahl von Kompatibilitätstagen. Beachten Sie Folgendes: Diese Änderung wird nur für neue Dateien wirksam, die per Tiering ausgelagert werden, und es werden alle Anpassungen des VSS-Zeitplans außer Kraft gesetzt, die Sie ggf. vorgenommen haben.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Proaktives Abrufen neuer und geänderter Dateien von einer Azure-Dateifreigabe

Mit der Agent-Version 11 wird ein neuer Modus auf einem Serverendpunkt verfügbar. Dieser Modus ermöglicht global verteilt angesiedelten Unternehmen, dass der Servercache in einer Remoteregion vorab aufgefüllt wird, sogar bevor lokale Benutzer auf Dateien zugreifen. Wenn dieser Modus auf einem Serverendpunkt aktiviert ist, führt er dazu, dass dieser Server Dateien abruft, die in der Azure-Dateifreigabe erstellt oder geändert wurden.

### <a name="scenario"></a>Szenario

Ein global verteilt angesiedeltes Unternehmen verfügt über Zweigstellen in den USA und in Indien. Am Morgen (US-Zeit) erstellen Information-Worker einen neuen Ordner und neue Dateien für ein ganz neues Projekt, an dem sie den gesamten Tag arbeiten. Die Azure-Dateisynchronisierung synchronisiert Ordner und Dateien auf die Azure-Dateifreigabe (den Cloudendpunkt). Information Worker in Indien setzen die Arbeit an dem Projekt in ihrer Zeitzone fort. Wenn diese am Morgen eintreffen, müssen auf dem lokalen, für Azure-Dateisynchronisierung aktivierten Server in Indien diese neuen Dateien lokal verfügbar sein, damit das Team in Indien effizient aus einem lokalen Cache heraus weiterarbeiten kann. Wenn Sie diesen Modus aktivieren, wird verhindert, dass der anfängliche Dateizugriff aufgrund eines bedarfsgesteuerten Abrufs langsamer ist, und dem Server wird ermöglicht, die Dateien proaktiv abzurufen, sobald sie in der Azure-Dateifreigabe geändert oder erstellt wurden.

> [!IMPORTANT]
> Es ist wichtig, zu verstehen, dass das so enge Nachverfolgen von Änderungen in der Azure-Dateifreigabe auf dem Server Ihren ausgehenden Datenverkehr und somit die Rechnung von Azure erhöhen kann. Wenn auf den Server abgerufene Dateien nicht tatsächlich lokal benötigt werden, kann ein unnötiger Abruf auf den Server negative Folgen haben. Verwenden Sie diesen Modus, wenn Sie wissen, dass das Vorabauffüllen des Caches auf einem Server mit aktuellen Änderungen aus der Cloud einen positiven Effekt auf Benutzer oder Anwendungen haben wird, die die Dateien auf diesem Server verwenden.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Aktivieren eines Serverendpunkts zum proaktiven Abrufen der Änderung von einer Azure-Dateifreigabe

# <a name="portal"></a>[Portal](#tab/proactive-portal)

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Speichersynchronisierungsdienst, wählen Sie die richtige Synchronisierungsgruppe aus, und identifizieren Sie dann den Serverendpunkt, für den Sie Änderungen in der Azure-Dateifreigabe (Cloudendpunkt) eng nachverfolgen möchten.
1. Suchen Sie im Abschnitt „Cloudtiering“ das Thema „Herunterladen von Azure-Dateifreigaben“. Der aktuell ausgewählte Modus wird angezeigt, und Sie können ihn so ändern, dass Änderungen an der Azure-Dateifreigabe enger nachverfolgt und diese proaktiv auf den Server abgerufen werden.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Eine Abbildung, die das Downloadverhalten der Azure-Dateifreigabe für einen derzeit gültigen Serverendpunkt darstellt sowie eine Schaltfläche zum Öffnen eines Menüs, das dessen Änderung gestattet.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

Sie können Eigenschaften von Serverendpunkten in PowerShell mithilfe des Cmdlets [Set-AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) ändern.

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrieren einer DFS-R-Bereitstellung (DFS-Replikation) zur Azure-Dateisynchronisierung
So migrieren eine DFS-R-Bereitstellung zur Azure-Dateisynchronisierung

1. Erstellen Sie eine Synchronisierungsgruppe, um die DFS-R-Topologie darzustellen, die Sie ersetzen möchten.
2. Beginnen Sie auf dem Server, der den vollständigen Satz von Daten in der DFS-R-Topologie, die migriert werden soll, enthält. Installieren Sie auf diesem Server die Azure-Dateisynchronisierung.
3. Registrieren Sie diesen Server, und erstellen Sie einen Serverendpunkt, zu dem der erste Server migriert werden soll. Aktivieren Sie nicht das Cloudtiering.
4. Lassen Sie alle Daten mit der Azure-Dateifreigabe (Cloudendpunkt) synchronisieren.
5. Installieren und registrieren Sie den Azure-Dateisynchronisierungs-Agent auf jedem der verbleibenden DFS-R-Server.
6. Deaktivieren Sie DFS-R. 
7. Erstellen Sie einen Serverendpunkt auf jedem DFS-R-Server. Aktivieren Sie nicht das Cloudtiering.
8. Vergewissern Sie sich, dass die Synchronisierung abgeschlossen wird, und testen Sie Ihre Topologie bei Bedarf.
9. Setzen Sie DFS-R außer Kraft.
10. Cloudtiering kann jetzt bei Bedarf auf allen Serverendpunkten aktiviert werden.

Weitere Informationen finden Sie unter [Zusammenarbeit zwischen der Azure-Dateisynchronisierung und DFS (verteiltes Dateisystem)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Nächste Schritte
- [Hinzufügen/Entfernen eines Serverendpunkts für die Azure-Dateisynchronisierung](storage-sync-files-server-endpoint.md)
- [Registrieren/Aufheben der Registrierung eines Servers bei der Azure-Dateisynchronisierung](storage-sync-files-server-registration.md)
- [Überwachen der Azure-Dateisynchronisierung](storage-sync-files-monitoring.md)
