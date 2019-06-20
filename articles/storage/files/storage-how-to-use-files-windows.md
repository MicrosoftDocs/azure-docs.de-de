---
title: Verwenden einer Azure-Dateifreigabe mit Windows | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure-Dateifreigabe mit Windows und Windows Server verwenden.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 899bf4bbf201ae785a4f49c7f278de75fb48945e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64926270"
---
# <a name="use-an-azure-file-share-with-windows"></a>Verwenden einer Azure-Dateifreigabe mit Windows
[Azure Files](storage-files-introduction.md) ist das benutzerfreundliche Clouddateisystem von Microsoft. Azure-Dateifreigaben können in Windows und Windows Server nahtlos verwendet werden. In diesem Artikel werden die Überlegungen zur Verwendung einer Azure-Dateifreigabe mit Windows und Windows Server behandelt.

Wenn Sie eine Azure-Dateifreigabe außerhalb der Azure-Region verwenden möchten, in der sie gehostet wird (beispielsweise lokal oder in einer anderen Azure-Region), muss das Betriebssystem SMB 3.0 unterstützen. 

Sie können Azure-Dateifreigaben in einer Windows-Installation verwenden, die entweder auf einem virtuellen Azure-Computer oder lokal ausgeführt wird. In der folgenden Tabelle wird veranschaulicht, welche Betriebssystemversionen den Zugriff auf Dateifreigaben in den einzelnen Umgebungen unterstützen:

| Windows-Version        | SMB-Version | Einbindung in Azure-VM möglich | Lokale Einbindung möglich |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019    | SMB 3.0 | Ja | Ja |
| Windows 10<sup>1</sup> | SMB 3.0 | Ja | Ja |
| Halbjährlicher Windows Server-Kanal<sup>2</sup> | SMB 3.0 | Ja | Ja |
| Windows Server 2016    | SMB 3.0     | Ja                   | Ja                  |
| Windows 8.1            | SMB 3.0     | Ja                   | Ja                  |
| Windows Server 2012 R2 | SMB 3.0     | Ja                   | Ja                  |
| Windows Server 2012    | SMB 3.0     | Ja                   | Ja                  |
| Windows 7              | SMB 2.1     | Ja                   | Nein                   |
| Windows Server 2008 R2 | SMB 2.1     | Ja                   | Nein                   |

<sup>1</sup>Windows 10, Versionen 1507, 1607, 1703, 1709, 1803 und 1809  
<sup>2</sup>Windows Server, Versionen 1709 und 1803

> [!Note]  
> Es empfiehlt sich, immer die neueste KB für Ihre Windows-Version zu verwenden.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen 
* **Speicherkontoname**: Zum Einbinden einer Azure-Dateifreigabe benötigen Sie den Namen des Speicherkontos.

* **Speicherkontoschlüssel**: Zum Einbinden einer Azure-Dateifreigabe benötigen Sie den primären (oder sekundären) Speicherschlüssel. SAS-Schlüssel können derzeit nicht zum Einbinden verwendet werden.

* **Stellen Sie sicher, Port 445 geöffnet ist**: Das SMB-Protokoll erfordert dass TCP-Port 445 geöffnet ist. Wenn Port 445 gesperrt ist, sind keine Verbindungen möglich. Mithilfe des Cmdlets `Test-NetConnection` können Sie überprüfen, ob Ihre Firewall Port 445 blockiert. Informationen zu verschiedenen Möglichkeiten, den blockierten Port 445 zu umgehen, finden Sie [hier](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked).

    Für den folgenden PowerShell-Code wird vorausgesetzt, dass Sie das Azure PowerShell-Modul installiert haben. Weitere Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](https://docs.microsoft.com/powershell/azure/install-az-ps). Denken Sie daran, `<your-storage-account-name>` und `<your-resource-group-name>` durch die entsprechenden Namen für Ihr Speicherkonto zu ersetzen.

    ```powershell
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    ```

    Wenn das Verbinden erfolgreich war, wird die folgende Ausgabe angezeigt:

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > Der oben genannte Befehl gibt die aktuelle IP-Adresse des Speicherkontos zurück. Diese IP-Adresse bleibt nicht garantiert gleich und kann sich jederzeit ändern. Verwenden Sie keine Hartkodierung für diese IP-Adresse in Skripts oder einer Firewallkonfiguration. 

## <a name="using-an-azure-file-share-with-windows"></a>Verwenden einer Azure-Dateifreigabe mit Windows
Zum Verwenden einer Azure-Dateifreigabe mit Windows müssen Sie sie entweder einbinden, d.h. ihr einen Laufwerkbuchstaben oder Bereitstellungspunktpfad zuweisen, oder über ihren [UNC-Pfad](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx) darauf zugreifen. 

Im Gegensatz zu anderen SMB-Freigaben, mit denen Sie vielleicht interagiert haben, wie z B. jene, die auf einem Windows Server-Computer, Linux Samba-Server oder NAS-Gerät gehostet werden, unterstützen Azure-Dateifreigaben derzeit keine Kerberos-Authentifizierung mit Ihrer Active Directory (AD) oder Azure Active Directory (AAD)-Identität. Allerdings [arbeiten](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles) wir bereits an dieser Funktion. Stattdessen müssen Sie für den Zugriff auf Ihre Azure-Dateifreigabe den Speicherkontoschlüssel für das Speicherkonto verwenden, das Ihre Azure-Dateifreigabe enthält. Ein Speicherkontoschlüssel ist ein Administratorschlüssel für ein Speicherkonto, der Administratorberechtigungen für alle Dateien und Ordner in der Dateifreigabe, auf die Sie zugreifen, sowie für alle Dateifreigaben und anderen Speicherressourcen (Blobs, Warteschlangen, Tabellen usw.) in Ihrem Speicherkonto umfasst. Wenn dies für Ihre Workload nicht ausreicht, kann die [Azure-Dateisynchronisierung](storage-files-planning.md#data-access-method) den Mangel an Kerberos-Authentifizierung und ACL-Unterstützung in der Zwischenzeit ausgleichen, bis die AAD-basierte Kerberos-Authentifizierung und ACL-Unterstützung öffentlich verfügbar sind.

Ein allgemeines Muster für das Migrieren von Branchenanwendungen per Lift & Shift, die eine SMB-Dateifreigabe an Azure erwarten, ist die Verwendung einer Azure-Dateifreigabe als Alternative für die Ausführung eines dedizierten Windows-Dateiservers auf einem virtuellen Azure-Computer. Ein wichtiger Aspekt für die erfolgreiche Migration einer Branchenanwendung zur Verwendung einer Azure-Dateifreigabe ist, dass viele Branchenanwendungen im Kontext eines dedizierten Dienstkontos mit begrenzten Berechtigungen anstatt des Administratorkontos eines virtuellen Computers ausgeführt werden. Daher müssen Sie sicherstellen, dass Sie die Anmeldeinformationen für die Azure-Dateifreigabe aus dem Kontext des Dienstkontos und nicht Ihres Administratorkontos einbinden/speichern.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Beibehalten von Anmeldeinformationen für eine Azure-Dateifreigabe in Windows  
Das Hilfsprogramm [Cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) ermöglicht Ihnen das Speichern Ihrer Speicherkonto-Anmeldeinformationen in Windows. Dies bedeutet, dass Sie keine Anmeldeinformationen angeben müssen, wenn Sie auf eine Azure-Dateifreigabe über deren UNC-Pfad zugreifen oder die Azure-Dateifreigabe einbinden möchten. Zum Speichern der Anmeldeinformationen Ihres Speicherkontos führen Sie die folgenden PowerShell-Befehle aus und ersetzen dabei `<your-storage-account-name>` und `<your-resource-group-name>` entsprechend.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command ("cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)")
```

Mithilfe des Auflistungsparameters können Sie überprüfen, ob das Cmdkey-Hilfsprogramm die Anmeldeinformationen für das Speicherkonto gespeichert hat:

```powershell
cmdkey /list
```

Wenn die Anmeldeinformationen für Ihre Azure-Dateifreigabe erfolgreich gespeichert wurden, lautet die erwartete Ausgabe wie folgt (in der Liste sind möglicherweise zusätzliche Schlüssel gespeichert):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Sie sollten jetzt ohne Angabe zusätzlicher Anmeldeinformationen in der Lage sein, die Freigabe einzubinden oder darauf zuzugreifen.

#### <a name="advanced-cmdkey-scenarios"></a>Erweiterte Cmdkey-Szenarien
Für Cmdkey sind zwei zusätzliche Szenarien zu berücksichtigen: Speichern von Anmeldeinformationen für einen anderen Benutzer auf dem Computer, z.B. ein Dienstkonto, und Speichern von Anmeldeinformationen auf einem Remotecomputer mit PowerShell-Remoting.

Das Speichern der Anmeldeinformationen für einen anderen Benutzer auf dem Computer ist äußerst einfach: Nachdem Sie sich bei Ihrem Konto angemeldet haben, führen Sie einfach den folgenden PowerShell-Befehl aus:

```powershell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Daraufhin wird ein neues PowerShell-Fenster im Benutzerkontext Ihres Dienstkontos (oder Benutzerkontos) geöffnet. Anschließend können Sie wie [oben](#persisting-azure-file-share-credentials-in-windows) beschriebenen das Cmdkey-Hilfsprogramm verwenden.

Das Speichern der Anmeldeinformationen auf einem Remotecomputer mithilfe von PowerShell-Remoting ist jedoch nicht möglich, da Cmdkey keinen Zugriff – auch nicht für Hinzufügungen – auf den Anmeldeinformationsspeicher zulässt, wenn der Benutzer über PowerShell-Remoting angemeldet ist. Es wird empfohlen, sich mit [Remotedesktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows) auf dem Computer anzumelden.

### <a name="mount-the-azure-file-share-with-powershell"></a>Einbinden der Azure-Dateifreigabe mithilfe von PowerShell
Führen Sie die folgenden Befehle in einer normalen (d.h. ohne erhöhte Rechte) PowerShell-Sitzung aus, um die Azure-Dateifreigabe einzubinden. Denken Sie daran `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` und `<desired-drive-letter>` durch die richtigen Informationen zu ersetzen.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> Mit der Option `-Persist` des Cmdlets `New-PSDrive` lässt sich die Dateifreigabe beim Starten nur erneut einbinden, wenn die Anmeldeinformationen gespeichert sind. Sie können die Anmeldeinformationen wie [oben beschriebenen](#persisting-azure-file-share-credentials-in-windows) mithilfe von Cmdkey speichern. 

Falls gewünscht können Sie die Einbindung der Azure-Dateifreigabe mit dem folgenden PowerShell-Cmdlet aufheben.

```powershell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Einbinden der Azure-Dateifreigabe über den Explorer
> [!Note]  
> Beachten Sie, dass sich die folgenden Anweisungen auf Windows 10 beziehen und bei älteren Versionen unter Umständen geringfügig unterscheiden können. 

1. Öffnen Sie den Datei-Explorer. Verwenden Sie dazu entweder das Startmenü, oder drücken Sie WINDOWS-TASTE+E.

2. Navigieren Sie im linken Fensterbereich zu **Dieser PC**. Dadurch ändern sich die verfügbaren Menüs auf dem Menüband. Wählen Sie im Menü „Computer“ die Option **Netzlaufwerk verbinden** aus.
    
    ![Screenshot: Dropdownmenü „Netzlaufwerk verbinden“](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Kopieren Sie den UNC-Pfad aus dem Bereich **Verbinden** im Azure-Portal. 

    ![UNC-Pfad aus dem Verbindungsbereich von Azure Files](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. Wählen Sie den Laufwerkbuchstaben aus, und geben Sie den UNC-Pfad ein. 
    
    ![Screenshot des Dialogfelds „Netzlaufwerk verbinden“](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. Verwenden Sie den Speicherkontonamen mit vorangestelltem `AZURE\` als Benutzername und einen Speicherkontoschlüssel als Kennwort.
    
    ![Screenshot des Dialogfelds für die Netzwerkanmeldeinformationen](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Verwenden Sie die Azure-Dateifreigabe wie gewünscht.
    
    ![Die Azure-Dateifreigabe ist nun eingebunden.](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Wenn Sie die Einbindung der Azure-Dateifreigabe wieder aufheben möchten, klicken Sie im Explorer unter **Netzwerkadressen** mit der rechten Maustaste auf den Eintrag für die Freigabe, und wählen Sie **Trennen**.

### <a name="accessing-share-snapshots-from-windows"></a>Zugreifen auf Freigabemomentaufnahmen von Windows
Wenn Sie manuell oder automatisch über ein Skript oder einen Dienst wie Azure Backup eine Freigabemomentaufnahme erstellt haben, können Sie vorherige Versionen einer Freigabe, eines Verzeichnisses oder einer bestimmten Datei aus einer Dateifreigabe unter Windows anzeigen. Sie können über das [Azure-Portal](storage-how-to-use-files-portal.md), über [Azure PowerShell](storage-how-to-use-files-powershell.md) und über die [Azure-Befehlszeilenschnittstelle](storage-how-to-use-files-cli.md) eine Momentaufnahme erstellen.

#### <a name="list-previous-versions"></a>Liste der vorherigen Versionen
Navigieren Sie zu dem Element oder übergeordneten Element, das wiederhergestellt werden soll. Doppelklicken Sie darauf, um zum gewünschten Verzeichnis zu navigieren. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie im Menü **Eigenschaften** aus.

![Kontextmenü für ein ausgewähltes Verzeichnis](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Wählen Sie **Vorherige Versionen** aus, um die Liste der Freigabemomentaufnahmen für dieses Verzeichnis anzuzeigen. Je nach Netzwerkgeschwindigkeit und Anzahl der Freigabemomentaufnahmen im Verzeichnis kann es einige Sekunden dauern, bis die Liste geladen ist.

![Registerkarte „Vorherige Versionen“](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Sie können **Öffnen** auswählen, um eine bestimmte Momentaufnahme zu öffnen. 

![Geöffnete Momentaufnahme](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Wiederherstellen aus einer vorherigen Version
Wählen Sie **Wiederherstellen**, um den Inhalt des gesamten Verzeichnisses rekursiv zum Erstellungszeitpunkt der Freigabemomentaufnahme am ursprünglichen Speicherort zu kopieren.
 ![Wiederherstellungsschaltfläche in Warnmeldung](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Sichern von Windows/Windows Server
Zum Einbinden einer Azure-Dateifreigabe unter Windows muss Port 445 zugänglich sein. Viele Organisationen blockieren Port 445 aufgrund von mit SMB 1 verbundenen Sicherheitsrisiken. SMB 1, auch bekannt als CIFS (Common Internet File System) ist ein älteres Dateisystemprotokoll, das in Windows und Windows Server enthalten ist. SMB 1 ist ein veraltetes, ineffizientes und vor allem unsicheres Protokoll. Die gute Nachricht ist, dass Azure Files SMB 1 nicht unterstützt und alle unterstützten Versionen von Windows und Windows Server das Entfernen oder Deaktivieren von SMB 1 ermöglichen. Wir [empfehlen immer dringend](https://aka.ms/stopusingsmb1), unter Windows den SMB 1-Client und -Server vor der Verwendung von Azure-Dateifreigaben in der Produktion zu entfernen oder zu deaktivieren.

Die folgende Tabelle enthält ausführliche Informationen zum Status von SMB 1 für jede Version von Windows:

| Windows-Version                           | SMB 1-Standardstatus | Deaktivierungs-/Entfernungsmethode       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019 (Vorschauversion)             | Deaktiviert             | Entfernen mit Windows-Funktion |
| Windows Server, Versionen ab 1709            | Deaktiviert             | Entfernen mit Windows-Funktion |
| Windows 10, Versionen ab 1709                | Deaktiviert             | Entfernen mit Windows-Funktion |
| Windows Server 2016                       | Enabled              | Entfernen mit Windows-Funktion |
| Windows 10,Versionen 1507, 1607 und 1703 | Enabled              | Entfernen mit Windows-Funktion |
| Windows Server 2012 R2                    | Enabled              | Entfernen mit Windows-Funktion | 
| Windows 8.1                               | Enabled              | Entfernen mit Windows-Funktion | 
| Windows Server 2012                       | Enabled              | Deaktivieren mit Registrierung       | 
| Windows Server 2008 R2                    | Enabled              | Deaktivieren mit Registrierung       |
| Windows 7                                 | Enabled              | Deaktivieren mit Registrierung       | 

### <a name="auditing-smb-1-usage"></a>Überwachung der Verwendung von SMB 1
> Gilt für Windows Server 2019 (Vorschauversion), den halbjährlichen Windows Server-Kanal (Versionen 1709 und 1803), Windows Server 2016, Windows 10 (Versionen 1507, 1607, 1703, 1709 und 1803), Windows Server 2012 R2 und Windows 8.1

Vor dem Entfernen von SMB 1 in Ihrer Umgebung kann es von Vorteil sein, die Verwendung von SMB 1 zu überwachen, um festzustellen, ob Clients durch die Änderung beschädigt werden. Wenn Anforderungen für SMB-Dateifreigaben mit SMB 1 erfolgen, wird im Ereignisprotokoll unter `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` ein Überwachungsereignis aufgezeichnet. 

> [!Note]  
> Zum Aktivieren der Überwachungsunterstützung unter Windows Server 2012 R2 und Windows 8.1 installieren Sie mindestens [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Zum Aktivieren der Überwachung führen Sie das folgende Cmdlet in einer PowerShell-Sitzung mit erhöhten Rechten aus:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Entfernen von SMB 1 von Windows Server
> Gilt für Windows Server 2019 (Vorschauversion), den halbjährlichen Windows Server-Kanal (Versionen 1709 und 1803), Windows Server 2016, Windows Server 2012 R2

Zum Entfernen von SMB 1 von einer Windows Server-Instanz führen Sie das folgende Cmdlet in einer PowerShell-Sitzung mit erhöhten Rechten aus:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Starten Sie den Server neu, um den Entfernungsvorgang abzuschließen. 

> [!Note]  
> Ab Windows 10 und Windows Server-Version 1709, ist SMB 1 nicht mehr standardmäßig installiert und verfügt über separate Windows-Funktionen für den SMB 1-Client und den SMB 1-Server. Es wird immer empfohlen, sowohl den SMB-1-Server (`FS-SMB1-SERVER`) als auch den SMB 1-Client (`FS-SMB1-CLIENT`) nicht zu installieren.

### <a name="removing-smb-1-from-windows-client"></a>Entfernen von SMB 1 von Windows-Clients
> Gilt für Windows 10 (Versionen 1507, 1607, 1703, 1709 und 1803) und Windows 8.1

Zum Entfernen von SMB 1 von Ihrem Windows-Client führen Sie das folgende Cmdlet in einer PowerShell-Sitzung mit erhöhten Rechten aus:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Starten Sie Ihren PC neu, um den Entfernungsvorgang abzuschließen.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Deaktivieren von SMB 1 unter älteren Versionen von Windows/Windows Server
> Gilt für Windows Server 2012, Windows Server 2008 R2 und Windows 7

SMB 1 kann unter älteren Versionen von Windows/Windows Server nicht vollständig entfernt werden, lässt sich jedoch über die Registrierung deaktivieren. Zum Deaktivieren von SMB 1 erstellen Sie unter `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` den neuen Registrierungsschlüssel `SMB1` vom Typ `DWORD` mit dem Wert `0`.

Dazu kann auch problemlos das folgende PowerShell-Cmdlet verwendet werden:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Nach der Erstellung dieses Registrierungsschlüssels müssen Sie den Server neu starten, um SMB 1 zu deaktivieren.

### <a name="smb-resources"></a>SMB-Ressourcen
- [Verwendung von SMB 1 beenden](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [Clearinghouse für SMB 1-Produkte](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Erkennen von SMB 1 in Ihrer Umgebung mit DSCEA](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [Deaktivieren von SMB 1 über eine Gruppenrichtlinie](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Files finden Sie unter diesen Links:
- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Häufig gestellte Fragen](../storage-files-faq.md)
- [Troubleshoot Azure File storage problems in Windows](storage-troubleshoot-windows-file-connection-problems.md) (Beheben von Problemen mit Azure File Storage unter Windows)      
