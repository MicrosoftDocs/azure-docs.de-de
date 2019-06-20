---
title: Bereitstellen der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Informationen über sämtliche Schritte zum Bereitstellen der Azure-Dateisynchronisierung.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0913e1877c63ed1a8e960676be02a12b45a34a7d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240090"
---
# <a name="deploy-azure-file-sync"></a>Bereitstellen der Azure-Dateisynchronisierung
Mit der Azure-Dateisynchronisierung können Sie die Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Mit der Azure-Dateisynchronisierung werden Ihre Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Sie können weltweit so viele Caches wie nötig nutzen.

Es wird dringend empfohlen, die Anleitungen [Planning for an Azure Files deployment](storage-files-planning.md) (Planung für eine Azure Files-Bereitstellung, in englischer Sprache) und [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md) zu lesen, bevor Sie die in diesem Artikel beschriebenen Schritte ausführen.

## <a name="prerequisites"></a>Voraussetzungen
* Eine Azure-Dateifreigabe in derselben Region, in der Sie die Azure-Dateisynchronisierung bereitstellen möchten. Weitere Informationen finden Sie unter
    - [Region availability](storage-sync-files-planning.md#region-availability) (Regionale Verfügbarkeit, in englischer Sprache) für die Azure-Dateisynchronisierung.
    - Eine Schritt-für-Schritt-Beschreibung zum Erstellen einer Dateifreigabe finden Sie unter [Erstellen einer Dateifreigabe](storage-how-to-create-file-share.md).
* Mindestens eine unterstützte Instanz von Windows Server oder Windows Server-Cluster für die Synchronisierung mit der Azure-Dateisynchronisierung. Weitere Informationen zu unterstützten Versionen von Windows Server finden Sie unter [Interoperability with Windows Server](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability) (Interoperabilität mit Windows Server, in englischer Sprache).
* Das Azure PowerShell-Modul kann mit PowerShell 5.1 oder PowerShell 6+ verwendet werden. Sie können das Azure PowerShell-Modul für Azure-Dateisynchronisierung auf jedem unterstützten System verwenden, auch auf Nicht-Windows-Systemen. Aber das Cmdlet für die Serverregistrierung muss immer direkt für die Windows Server-Instanz ausgeführt werden, die Sie registrieren. Unter Windows Server 2012 R2 können Sie anhand des Werts der **PSVersion**-Eigenschaft des **$PSVersionTable**-Objekts überprüfen, ob mindestens PowerShell 5.1\* ausgeführt wird:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Wenn die PSVersion niedriger als 5.1.\* ist, wie dies bei den meisten neuen Installationen von Windows Server 2012 R2 der Fall ist, können Sie problemlos ein Upgrade ausführen, indem Sie [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) herunterladen und installieren. Das Paket, das für Windows Server 2012 R2 heruntergeladen und installiert werden sollte, lautet **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**. 

    PowerShell 6+ kann mit jedem unterstützten System verwendet und über seine [GitHub-Seite](https://github.com/PowerShell/PowerShell#get-powershell) heruntergeladen werden. 

    > [!Important]  
    > Wenn Sie die Benutzeroberfläche für Serverregistrierung verwenden möchten, statt direkt aus PowerShell zu registrieren, müssen Sie PowerShell 5.1 verwenden.

* Wenn Sie entschieden haben, PowerShell 5.1 zu verwenden, müssen Sie sich vergewissern, dass mindestens .NET 4.7.2 installiert ist. Erfahren Sie mehr über [.NET Framework-Versionen und -Abhängigkeiten](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) auf Ihrem System.
* Das PowerShell-Modul „Az“, das installiert werden kann, indem die folgenden Anweisungen ausgeführt werden: [Installieren und Konfigurieren von Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). 
* Das Modul „Az.StorageSync“, das derzeit unabhängig vom Modul „Az“ installiert wird:

    ```PowerShell
    Install-Module Az.StorageSync -AllowClobber
    ```

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Vorbereiten von Windows Server für die Verwendung mit der Azure-Dateisynchronisierung
Deaktivieren Sie für jeden Server, den Sie mit der Azure-Dateisynchronisierung verwenden möchten, einschließlich aller Serverknoten in einem Failovercluster, die **Verstärkte Sicherheitskonfiguration für Internet Explorer**. Dies ist nur für die anfängliche Serverregistrierung erforderlich. Sie können sie nach dem Registrieren des Servers erneut aktivieren.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Öffnen Sie den Server-Manager.
2. Klicken Sie auf **Lokaler Server**:  
    ![„Lokaler Server“ auf der linken Seite der Server Manager-Benutzeroberfläche](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Wählen Sie im Unterbereich **Eigenschaften** den Link für **Verstärkte Sicherheitskonfiguration für IE** aus.  
    ![Bereich „Verstärkte Sicherheitskonfiguration für IE“ auf der Server Manager-Benutzeroberfläche](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Wählen Sie im Dialogfeld **Verstärkte Sicherheitskonfiguration für Internet Explorer** für **Administratoren** und **Benutzer** **Aus** aus:  
    ![Popupfenster „Verstärkte Sicherheitskonfiguration für Internet Explorer“, in dem „Aus“ gewählt ist](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Um die Verstärkte Sicherheitskonfiguration für Internet Explorer zu deaktivieren, führen Sie Folgendes in einer PowerShell-Sitzung mit erhöhten Rechten aus:

```powershell
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
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Bereitstellen des Speichersynchronisierungsdiensts 
Die Bereitstellung der Azure-Dateisynchronisierung beginnt mit der Platzierung einer **Speichersynchronisierungsdienst**-Ressource in einer Ressourcengruppe Ihres ausgewählten Abonnements. Sie sollten von diesen Ressourcen so wenig wie möglich bereitstellen. Sie schaffen eine Vertrauensstellung zwischen Ihren Servern und dieser Ressource, und ein Server kann nur bei einem Speichersynchronisierungsdienst registriert werden. Darum sollten Sie so viele Speichersynchronisierungsdienste bereitstellen, wie Sie benötigen, um Gruppen von Servern zu trennen. Bedenken Sie, dass Server aus verschiedenen Speichersynchronisierungsdiensten nicht miteinander synchronisiert werden können.

> [!Note]
> Der Speichersynchronisierungsdienst hat Zugriffsberechtigungen aus dem Abonnement und der Ressourcengruppe geerbt, wo er bereitgestellt worden ist. Sie sollten sorgfältig überprüfen, wer darauf zugreifen darf. Entitäten mit Schreibzugriff können die Synchronisierung neuer Sätze von Dateien aus Server starten, die bei diesem Speichersynchronisierungsdienst registriert sind, und bewirken, dass Daten in Azure-Speicher übertragen werden, auf den sie zugreifen können.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Navigieren Sie zum Bereitstellen eines Speichersynchronisierungsdiensts zum [Azure-Portal](https://portal.azure.com/), klicken Sie auf *Ressource erstellen*, und suchen Sie dann nach „Azure-Dateisynchronisierung“. Wählen Sie in den Suchergebnissen **Azure-Dateisynchronisierung** aus, und klicken Sie auf **Erstellen**, um die Registerkarte **Speichersynchronisierung bereitstellen** zu öffnen.

Geben Sie in dem neuen Bereich, der geöffnet wird, Folgendes ein:

- **Name**: Ein eindeutiger Name (pro Abonnement) für den Speichersynchronisierungsdienst.
- **Abonnement**: Das Abonnement, in dem Sie den Speichersynchronisierungsdienst erstellen möchten. Abhängig von der Konfigurationsstrategie Ihrer Organisation haben Sie möglicherweise Zugriff auf ein oder mehrere Abonnements. Die Abrechnung für die einzelnen Clouddienste (z.B. Azure Files) erfolgt innerhalb eines Azure-Abonnements.
- **Ressourcengruppe**: Eine Ressourcengruppe ist eine logische Gruppe von Azure-Ressourcen, z.B. ein Speicherkonto oder Speichersynchronisierungsdienst. Sie können für die Azure-Dateisynchronisierung eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe auswählen. (Wir empfehlen, Ressourcengruppen als Container zu verwenden, um Ressourcen für Ihre Organisation logisch voneinander zu trennen, etwa indem Sie alle Personalressourcen oder alle Ressourcen für ein bestimmtes Projekt gruppieren.)
- **Standort**: Die Region, in der Sie die Azure-Dateisynchronisierung bereitstellen möchten. In dieser Liste sind nur die unterstützten Regionen verfügbar.

Wenn Sie fertig sind, wählen Sie **Erstellen** aus, um den Speichersynchronisierungsdienst bereitzustellen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Ersetzen Sie **<Az_Region>** , **<RG_Name>** und **<my_storage_sync_service>** durch Ihre eigenen Werte. Verwenden Sie anschließend die folgenden Befehle, um einen Speichersynchronisierungsdienst zu erstellen und bereitzustellen:

```powershell
Connect-AzAccount

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

---

## <a name="install-the-azure-file-sync-agent"></a>Installieren des Azure-Dateisynchronisierungs-Agents
Der Azure-Dateisynchronisierungs-Agent ist ein herunterladbares Paket, mit dem ein Windows Server-Computer mit einer Azure-Dateifreigabe synchronisiert werden kann. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Sie können den Agent aus dem [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) herunterladen. Doppelklicken Sie nach Abschluss des Downloads auf das MSI-Paket, um die Installation des Azure-Dateisynchronisierungs-Agents zu starten.

> [!Important]  
> Wenn Sie die Azure-Dateisynchronisierung mit einem Failovercluster verwenden möchten, muss der Azure-Dateisynchronisierungs-Agent auf jedem Knoten im Cluster installiert werden. Jeder Knoten im Cluster muss registriert werden, um mit der Azure-Dateisynchronisierung verwendet werden zu können.

Folgendes Vorgehen wird empfohlen:
- Übernehmen Sie den Standardinstallationspfad („C:\Programme\Microsoft Files\Azure\StorageSyncAgent“), um die Problembehandlung und Serverwartung zu vereinfachen.
- Aktivieren Sie Microsoft Update, um die Azure-Dateisynchronisierung auf dem aktuellen Stand zu halten. Alle Updates des Azure-Dateisynchronisierungs-Agents, einschließlich Funktionsupdates und Hotfixe, erfolgen über Microsoft Update. Es wird empfohlen, das neueste Update der Azure-Dateisynchronisierung zu installieren. Weitere Informationen finden Sie unter [Updaterichtlinie für Azure-Dateisynchronisierung](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Wenn die Installation des Azure-Dateisynchronisierungs-Agents abgeschlossen ist, wird automatisch die Benutzeroberfläche der Serverregistrierung geöffnet. Vor der Registrierung benötigen Sie einen Speichersynchronisierungsdienst. Wie Sie einen Speichersynchronisierungsdienst erstellen, erfahren Sie im nächsten Abschnitt.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
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

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrieren eines Windows-Servers beim Speichersynchronisierungsdienst
Durch das Registrieren des Windows-Servers bei einem Speichersynchronisierungsdienst wird eine Vertrauensstellung zwischen dem Server (oder Cluster) und dem Speichersynchronisierungsdienst geschaffen. Ein Server kann nur bei einem Speichersynchronisierungsdienst registriert und mit anderen Servern und Azure-Dateifreigaben synchronisiert werden, die demselben Speichersynchronisierungsdienst zugeordnet sind.

> [!Note]
> Die Serverregistrierung verwendet Ihre Azure-Anmeldeinformationen zum Erstellen einer Vertrauensstellung zwischen dem Speichersynchronisierungsdienst und Ihrer Windows Server-Instanz, doch anschließend erstellt der Server seine eigene Identität, die gültig ist, solange der Server registriert bleibt und das aktuelle Shared Access Signature-Token (Speicher-SAS) gültig ist, und verwendet sie. Sobald die Registrierung des Servers aufgehoben ist, kann kein neues SAS-Token an den Server ausgegeben werden, sodass der Server nicht mehr auf Ihre Azure-Dateifreigaben zugreifen kann und damit jede Synchronisierung beendet wird.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Die Benutzeroberfläche der Serverregistrierung sollte sich nach der Installation des Azure-Dateisynchronisierungs-Agents automatisch öffnen. Wenn dies nicht der Fall ist, können Sie sie aus ihrem Dateispeicherort manuell öffnen: „C:\Programme\Azure\StorageSyncAgent\ServerRegistration.exe“. Wenn die Benutzeroberfläche der Serverregistrierung geöffnet ist, wählen Sie **Anmelden** aus, um mit der Registrierung zu beginnen.

Nach der Anmeldung werden die folgenden Informationen abgefragt:

![Screenshot der Serverregistrierungs-Benutzeroberfläche](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-Abonnement**: Das Abonnement, das den Speichersynchronisierungsdienst enthält (siehe [Bereitstellen des Speichersynchronisierungsdiensts](#deploy-the-storage-sync-service)). 
- **Ressourcengruppe**: Die Ressourcengruppe, die den Speichersynchronisierungsdienst enthält.
- **Speichersynchronisierungsdienst**: Der Name des Speichersynchronisierungsdiensts, bei dem Sie den Server registrieren möchten.

Nachdem Sie die entsprechenden Informationen ausgewählt haben, wählen Sie **Registrieren** aus, um die Serverregistrierung abzuschließen. Während des Registrierungsvorgangs werden Sie erneut aufgefordert, sich anzumelden.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Erstellen einer Synchronisierungsgruppe und eines Cloudendpunkts
Eine Synchronisierungsgruppe definiert die Synchronisierungstopologie für einen Satz von Dateien. Endpunkte innerhalb einer Synchronisierungsgruppe bleiben miteinander synchron. Eine Synchronisierungsgruppe muss einen Cloudendpunkt enthalten, der eine Azure-Dateifreigabe und einen oder mehrere Serverendpunkte darstellt. Ein Serverendpunkt stellt einen Pfad auf einem registrierten Server dar. Ein Server kann in mehreren Synchronisierungsgruppen über Serverendpunkte verfügen. Sie können so viele Synchronisierungsgruppen erstellen, wie Sie für die angemessene Beschreibung Ihrer gewünschten Synchronisierungstopologie benötigen.

Ein Cloudendpunkt ist ein Zeiger auf eine Azure-Dateifreigabe. Alle Serverendpunkte werden mit einem Cloudendpunkt synchronisiert, sodass der Cloudendpunkt zum Hub wird. Das Speicherkonto für die Azure-Dateifreigabe muss sich in der gleichen Region wie der Speichersynchronisierungsdienst befinden. Mit einer Ausnahme wird die gesamte Azure-Dateifreigabe synchronisiert: Ein besonderer Ordner, vergleichbar mit dem versteckten Ordner „System Volume Information“ auf einem NTFS-Volume, wird bereitgestellt. Dieses Verzeichnis heißt „.SystemShareInformation“. Er enthält wichtige Synchronisierungsmetadaten, die nicht mit anderen Endpunkten synchronisiert werden. Sie dürfen ihn weder verwenden noch löschen!

> [!Important]  
> Sie können Änderungen an jedem Cloudendpunkt oder Serverendpunkt in der Synchronisierungsgruppe vornehmen und Ihre Dateien mit den anderen Endpunkten in der Synchronisierungsgruppe synchronisieren. Wenn Sie eine Änderung direkt am Cloudendpunkt (Azure-Dateifreigabe) vornehmen, müssen Änderungen zunächst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen entdeckt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird nur einmal alle 24 Stunden gestartet. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu Azure Files](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Um eine Synchronisierungsgruppe zu erstellen, navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Speichersynchronisierungsdienst, und wählen Sie dann **+ Synchronisierungsgruppe** aus:

![Erstellen einer neuen Synchronisierungsgruppe im Azure-Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Geben Sie im Bereich, der jetzt geöffnet wird, die folgenden Informationen ein, um eine Synchronisierungsgruppe bei einem Cloudendpunkt zu erstellen:

- **Name der Synchronisierungsgruppe**: Der Name der zu erstellenden Synchronisierungsgruppe. Dieser Name muss innerhalb des Speichersynchronisierungsdiensts eindeutig sein, es kann jedoch ein beliebiger Name sein, der für Sie Sinn ergibt.
- **Abonnement**: Das Abonnement, in dem Sie unter [Bereitstellen des Speichersynchronisierungsdiensts](#deploy-the-storage-sync-service) weiter oben den Speichersynchronisierungsdienst bereitgestellt haben.
- **Speicherkonto**: Wenn Sie **Speicherkonto auswählen** auswählen, wird ein weiterer Bereich angezeigt, in dem Sie das Speicherkonto auswählen können, das die Azure-Dateifreigabe enthält, mit der Sie die Synchronisierung ausführen möchten.
- **Azure-Dateifreigabe**: Der Name der Azure-Dateifreigabe, mit der die Synchronisierung ausgeführt werden soll.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
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

---

## <a name="create-a-server-endpoint"></a>Erstellen eines Serverendpunkts
Ein Serverendpunkt stellt einen bestimmten Speicherort auf einem registrierten Server dar, z. B. einen Ordner auf einem Servervolume. Ein Serverendpunkt muss ein Pfad auf einem registrierten Server sein (statt einer eingebundenen Freigabe), und um Cloudtiering verwenden zu können, darf der Pfad sich nicht auf einem Systemvolume befinden. Network Attached Storage (NAS) wird nicht unterstützt.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Um einen Serverendpunkt hinzuzufügen, wechseln Sie zu der neu erstellten Synchronisierungsgruppe, und wählen Sie dann **Serverendpunkt hinzufügen** aus.

![Hinzufügen eines neuen Serverendpunkts im Bereich „Synchronisierungsgruppe“](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Geben Sie im Bereich **Serverendpunkt hinzufügen** die folgenden Informationen ein, um einen Serverendpunkt zu erstellen:

- **Registrierter Server**: Der Name des Servers oder Clusters, auf bzw. in dem Sie den Serverendpunkt erstellen möchten.
- **Pfad**: Der Windows Server-Pfad, der als Teil der Synchronisierungsgruppe synchronisiert werden soll.
- **Cloudtiering**: Ein Schalter, mit dem Cloudtiering aktiviert oder deaktiviert wird. Mit Cloudtiering kann für selten verwendete oder selten einem Zugriff ausgesetzte Dateien Tiering nach Azure Files festgelegt werden.
- **Freier Speicherplatz auf Volume**: Die Menge an freiem Speicherplatz auf dem Volume, auf dem sich der Serverendpunkt befindet, die reserviert werden soll. Wenn z.B. für ein Volume mit einem einzigen Serverendpunkt „Freier Speicherplatz auf Volume“ auf 50 % festgelegt ist, wird ungefähr die Hälfte der Daten nach Azure Files ausgelagert. Die Azure-Dateifreigabe enthält immer eine vollständige Kopie der Daten in der Synchronisierungsgruppe, unabhängig davon, ob Cloudtiering aktiviert ist.

Wählen Sie **Erstellen** aus, um den Serverendpunkt hinzuzufügen. Ihre Dateien bleiben jetzt zwischen der Azure-Dateifreigabe und Windows Server synchron. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Führen Sie die folgenden PowerShell-Befehle aus, um den Serverendpunkt zu erstellen, und achten Sie darauf, dass Sie `<your-server-endpoint-path>` und `<your-volume-free-space>` mit den gewünschten Werten ersetzen.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

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
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Onboarding bei der Azure-Dateisynchronisierung
Die empfohlenen Schritte zum erstmaligen Onboarding bei der Azure-Dateisynchronisierung ohne Ausfallzeiten unter Beibehaltung der vollen Dateitreue und Zugriffssteuerungsliste (ACL) sind wie folgt:
 
1. Stellen Sie einen Speichersynchronisierungsdienst bereit.
2. Erstellen Sie eine Synchronisierungsgruppe.
3. Installieren Sie den Azure-Dateisynchronisierungs-Agent auf dem Server mit dem vollständigen Dataset.
4. Registrieren Sie diesen Server, und erstellen Sie einen Serverendpunkt in der Freigabe. 
5. Lassen Sie die Synchronisierung den vollständigen Upload in die Azure-Dateifreigabe (den Cloudendpunkt) durchführen.  
6. Installieren Sie nach Abschluss des anfänglichen Uploads den Azure-Dateisynchronisierungs-Agent auf allen verbleibenden Servern.
7. Erstellen Sie auf den verbleibenden Servern neue Dateifreigaben.
8. Erstellen Sie nach Wunsch Serverendpunkte in neuen Dateifreigaben mit der Cloudtieringrichtlinie. (Für diesen Schritt muss zusätzlicher Speicherplatz für das anfängliche Setup zur Verfügung stehen.)
9. Lassen Sie den Azure-Dateisynchronisierungs-Agent eine schnelle Wiederherstellung des gesamten Namespace ohne die eigentliche Datenübertragung durchführen. Nach der vollständigen Synchronisierung des Namespace füllt das Synchronisierungsmodul den lokalen Speicherplatz basierend auf der Cloudtieringrichtlinie für den Serverendpunkt auf. 
10. Vergewissern Sie sich, dass die Synchronisierung abgeschlossen wird, und testen Sie Ihre Topologie bei Bedarf. 
11. Leiten Sie Benutzer und Anwendungen zu dieser neuen Freigabe um.
12. Optional können Sie duplizierte Freigaben auf den Servern löschen.
 
Wenn Sie nicht über zusätzlichen Speicherplatz für das erste Onboarding verfügen und an die vorhandenen Freigaben anhängen möchten, können Sie für die Daten in den Azure-Dateifreigaben vorab ein Seeding ausführen. Dieser Ansatz wird ausdrücklich nur dann empfohlen, wenn Sie Ausfallzeiten akzeptieren und absolut keine Datenveränderungen auf den Serverfreigaben während des ersten Onboardingprozesses garantieren können. 
 
1. Stellen Sie sicher, dass sich die Daten auf den Servern während des Onboardingprozesses nicht ändern können.
2. Führen Sie auf den Azure-Dateifreigaben vorab ein Seeding mit den Serverdaten mithilfe eines Datenübertragungstools über den SBM durch, wie z.B. Robocopy oder direkte SMB-Kopie. Da AzCopy keine Daten über den SMB hochlädt, kann dieses Tool nicht für das Vorabseeding verwendet werden.
3. Erstellen Sie eine Azure-Dateisynchronisierungstopologie mit den gewünschten Serverendpunkten, die auf die vorhandenen Freigaben zeigen.
4. Lassen Sie die Synchronisierung den Abstimmungsprozess auf allen Endpunkten abschließen. 
5. Sobald die Abstimmung abgeschlossen ist, können Sie Freigaben für Änderungen öffnen.
 
Beachten Sie, dass der Ansatz des Vorabseedings derzeit einige Einschränkungen aufweist: 
- Vollständige Originaltreue für Dateien wird nicht beibehalten. Dateien verlieren beispielsweise Zugriffssteuerungslisten und Zeitstempel.
- Datenänderungen auf dem Server, bevor die Synchronisierungstopologie vollständig in Betrieb ist, können zu Konflikten auf den Serverendpunkten führen.  
- Nachdem der Cloudendpunkt erstellt wurde, führt die Azure-Dateisynchronisierung einen Prozess aus, um die Dateien in der Cloud zu erkennen, bevor die anfängliche Synchronisierung gestartet wird. Die zum Abschluss dieses Prozesses benötigte Zeit variiert je nach den verschiedenen Faktoren wie Netzwerkgeschwindigkeit, verfügbare Bandbreite und Anzahl der Dateien und Ordner. Grob geschätzt schafft der Erkennungsprozess in der Vorschauversion ca. 10 Dateien pro Sekunde. Selbst wenn das Vorabseeding schnell erfolgt, kann die Gesamtzeit bis zur Inbetriebnahme eines voll funktionsfähigen Systems erheblich länger sein, wenn für die Daten in der Cloud vorab ein Seeding durchgeführt wird.

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
