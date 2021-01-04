---
title: Behandeln von Azure Files-Problemen unter Windows
description: Hier finden Sie Informationen zum Behandeln von Azure Files-Problemen unter Windows. In diesem Artikel werden allgemeine Probleme im Zusammenhang mit Azure Files aufgeführt, die auftreten können, wenn Sie eine Verbindung von Windows-Clients herstellen, und es werden mögliche Lösungen beschrieben. Nur für SMB-Freigaben
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: aef332e54fa650e1abbebe671560238d7eb318de
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492045"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Behandeln von Azure Files-Problemen unter Windows (SMB)

Dieser Artikel beschreibt allgemeine Probleme im Zusammenhang mit Microsoft Azure Files, wenn Sie eine Verbindung von Windows-Clients herstellen. Darüber hinaus werden die möglichen Ursachen und Lösungen für diese Probleme bereitgestellt. Zusätzlich zu den Schritten zur Problembehandlung in diesem Artikel können Sie auch [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) verwenden, um sicherzustellen, dass die Windows-Clientumgebung über richtige erforderliche Komponenten verfügt. AzFileDiagnostics automatisiert die Erkennung eines Großteils der Symptome, die in diesem Artikel erwähnt werden und hilft, Ihre Umgebung einzurichten, um eine optimale Leistung zu erzielen.

> [!IMPORTANT]
> Der Inhalt dieses Artikels gilt nur für SMB-Freigaben. Weitere Informationen zu NFS-Freigaben finden Sie unter [Behandeln von Problemen mit Azure NFS-Dateifreigaben](storage-troubleshooting-files-nfs.md).

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fehler 5 beim Bereitstellen einer Azure-Dateifreigabe

Wenn Sie versuchen, eine Dateifreigabe bereitzustellen, erhalten Sie möglicherweise den folgenden Fehler:

- „Systemfehler 5 ist aufgetreten. Zugriff verweigert.“

### <a name="cause-1-unencrypted-communication-channel"></a>Ursache 1: Unverschlüsselter Kommunikationskanal

Aus Sicherheitsgründen werden Verbindungen mit Azure-Dateifreigaben blockiert, wenn der Kommunikationskanal nicht verschlüsselt ist und der Verbindungsversuch nicht von dem gleichen Datencenter aus erfolgt, in dem sich die Azure-Dateifreigaben befinden. Unverschlüsselte Verbindungen innerhalb desselben Datencenters können auch blockiert werden, wenn die Einstellung [Sichere Übertragung erforderlich](../common/storage-require-secure-transfer.md) für das Speicherkonto aktiviert ist. Ein verschlüsselter Kommunikationskanal wird nur dann bereitgestellt, wenn das Clientbetriebssystem des Benutzers SMB-Verschlüsselung unterstützt.

Windows 8, Windows Server 2012 und höhere Versionen jedes Systems handeln Anforderung aus, die SMB 3.0 umfassen, wodurch die Verschlüsselung unterstützt wird.

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

1. Stellen Sie eine Verbindung von einem Client aus her, der SMB-Verschlüsselung unterstützt (Windows 8, Windows Server 2012 oder höher), oder stellen Sie eine Verbindung von einem virtuellen Computer aus her, der sich im selben Datencenter wie das Azure-Speicherkonto befindet, das für die Azure-Dateifreigabe verwendet wird.
2. Vergewissern Sie sich, dass die Einstellung [Sichere Übertragung erforderlich](../common/storage-require-secure-transfer.md) im Speicherkonto deaktiviert ist, wenn der Client keine SMB-Verschlüsselung unterstützt.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Ursache 2: Virtuelle Netzwerk- oder Firewallregeln sind für das Speicherkonto aktiviert. 

Wenn für das Speicherkonto Regeln für das virtuelle Netzwerk (VNET) und die Firewall konfiguriert sind, wird dem Netzwerkverkehr der Zugriff verweigert, es sei denn, der Client-IP-Adresse oder dem virtuellen Netzwerk wird der Zugriff gestattet.

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Stellen Sie sicher, dass virtuelle Netzwerk- und Firewallregeln für das Speicherkonto ordnungsgemäß konfiguriert sind. Um zu testen, ob virtuelle Netzwerk- oder Firewallregeln das Problem verursachen, ändern Sie vorübergehend die Einstellung für das Speicherkonto in **Zugriff aus allen Netzwerken zulassen**. Weitere Informationen finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../common/storage-network-security.md).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Ursache 3: Berechtigungen auf Freigabeebene sind bei Verwendung der identitätsbasierten Authentifizierung falsch

Wenn Benutzer mithilfe der Active Directory (AD)- oder Azure Active Directory Domain Services (Azure AD DS)-Authentifizierung auf die Azure-Dateifreigabe zugreifen, schlägt der Zugriff mit dem Fehler „Zugriff verweigert“ fehl, wenn Berechtigungen auf Freigabeebene falsch sind. 

### <a name="solution-for-cause-3"></a>Lösung für Ursache 3

Überprüfen, ob Berechtigungen ordnungsgemäß konfiguriert sind:

- **Active Directory (AD)** siehe [Zuweisen von Berechtigungen auf Freigabeebene für eine Identität](./storage-files-identity-ad-ds-assign-permissions.md).

    Berechtigungszuweisungen auf Freigabeebene werden für Gruppen und Benutzer unterstützt, die mithilfe von Azure AD Connect von Active Directory (AD) zu Azure Active Directory (Azure AD) synchronisiert wurden.  Vergewissern Sie sich, dass Gruppen und Benutzer, denen Berechtigungen auf Freigabeebene zugewiesen werden, nicht zu den nicht unterstützten „reinen Cloudgruppen“ gehören.
- **Azure Active Directory Domain Services (Azure AD DS)** siehe [Zuweisen von Zugriffsberechtigungen zu einer Identität](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>„Fehler 53“, „Fehler 67“ oder „Fehler 87“ beim Versuch, eine Azure-Dateifreigabe einzubinden oder die Einbindung aufzuheben

Wenn Sie versuchen, eine Dateifreigabe aus einem lokalen oder einem anderen Datencenter einzubinden, erhalten Sie möglicherweise die folgenden Fehler:

- „Systemfehler 53 ist aufgetreten. Der Netzwerkpfad wurde nicht gefunden.“
- „Systemfehler 67 ist aufgetreten. „Der Netzwerkname wurde nicht gefunden.“
- „Systemfehler 87 ist aufgetreten. „Der Parameter ist falsch.“

### <a name="cause-1-port-445-is-blocked"></a>Ursache 1: Port 445 ist blockiert

„Systemfehler 53“ und „Systemfehler 67“ können auftreten, wenn die von Port 445 ausgehende Kommunikation zu einem Azure Files-Datencenter blockiert ist. Um eine Zusammenfassung der ISPs anzuzeigen, die den Zugang von Port 445 aus zulassen oder verweigern, gehen Sie auf [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Um festzustellen, ob Port 445 durch Ihre Firewall oder Ihren Internetdienstanbieter blockiert wird, verwenden Sie das Tool [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) oder das Cmdlet `Test-NetConnection`. 

Um das Cmdlet `Test-NetConnection` verwenden zu können, muss das Azure PowerShell-Modul installiert sein. Weitere Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps). Denken Sie daran, `<your-storage-account-name>` und `<your-resource-group-name>` durch die entsprechenden Namen für Ihr Speicherkonto zu ersetzen.

   
```azurepowershell
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
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> Der oben genannte Befehl gibt die aktuelle IP-Adresse des Speicherkontos zurück. Diese IP-Adresse bleibt nicht garantiert gleich und kann sich jederzeit ändern. Verwenden Sie keine Hartkodierung für diese IP-Adresse in Skripts oder einer Firewallkonfiguration.

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

#### <a name="solution-1---use-azure-file-sync"></a>Lösung 1: Azure-Dateisynchronisierung
Mit der Azure-Dateisynchronisierung können Ihre lokalen Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe werden. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Die Azure-Dateisynchronisierung erfolgt über Port 443 und kann daher als Umgehung für den Zugriff auf Azure Files von Clients verwendet werden, bei denen Port 445 gesperrt ist. [Erfahren Sie, wie Sie die Azure-Dateisynchronisierung einrichten](./storage-sync-files-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>Lösung 2: VPN
Indem Sie ein VPN für Ihr spezifisches Speicherkonto einrichten, durchläuft der Datenverkehr im Vergleich zum Internet einen sicheren Tunnel. Befolgen Sie die [Anweisungen zur Einrichtung eines VPN](storage-files-configure-p2s-vpn-windows.md) für den Zugriff auf Azure Files aus Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Lösung 3: Aufheben der Sperre von Port 445 mithilfe Ihres ISP/IT-Administrators
Arbeiten Sie mit Ihrer IT-Abteilung oder dem ISP zusammen, um den Ausgang von Datenverkehr über Port 445 für [IP-Bereiche von Azure](https://www.microsoft.com/download/details.aspx?id=41653) zu öffnen.

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Lösung 4: REST-API-basierte Tools wie Storage-Explorer/PowerShell
Azure Files unterstützt neben SMB auch REST. Der REST-Zugriff erfolgt über Port 443 (Standard: TCP). Es gibt verschiedene mit der REST-API geschriebene Tools, die eine vielseitige Benutzeroberfläche bereitstellen. [Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) ist eines davon. [Laden Sie Storage-Explorer herunter, installieren Sie ihn](https://azure.microsoft.com/features/storage-explorer/), und stellen eine Verbindung mit Ihrer Dateifreigabe in Azure Files her. Sie können auch [PowerShell](./storage-how-to-use-files-powershell.md) verwenden, die auch die REST-API nutzt.

### <a name="cause-2-ntlmv1-is-enabled"></a>Ursache 2: NTLMv1 ist aktiviert

„Systemfehler 53“ oder „Systemfehler 87“ können auftreten, wenn die NTLMv1-Kommunikation auf dem Client aktiviert ist. Azure Files unterstützt nur die NTLMv2-Authentifizierung. Wenn NTLMv1 aktiviert ist, ist der Client weniger sicher. Aus diesem Grund wird die Kommunikation für Azure Files blockiert. 

Um zu bestimmen, ob dies die Ursache des Fehlers ist, überprüfen Sie, ob der folgende Registrierungsunterschlüssel auf den Wert 3 festgelegt ist:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa &gt; LmCompatibilityLevel**

Weitere Informationen finden Sie im Thema [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) im TechNet.

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Setzen Sie den Wert **LmCompatibilityLevel** auf den Standardwert 3 im folgenden Registrierungsunterschlüssel zurück:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Fehler 1816: Unzureichendes Kontingent für die Verarbeitung dieses Befehls

### <a name="cause"></a>Ursache

Der Fehler 1816 tritt auf, wenn Sie die Obergrenze der parallelen offenen Handles erreichen, die für eine Datei oder ein Verzeichnis in der Azure-Dateifreigabe zulässig sind. Weitere Informationen finden Sie unter [Skalierbarkeitsziele für Azure Files](./storage-files-scale-targets.md#azure-files-scale-targets).

### <a name="solution"></a>Lösung

Reduzieren Sie die Anzahl der gleichzeitig geöffneten Handles, indem Sie einige Handles schließen und es anschließend erneut versuchen. Weitere Informationen finden Sie unter [Checkliste zur Leistung und Skalierbarkeit von Microsoft Azure Storage](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Verwenden Sie das PowerShell-Cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle), um geöffnete Handles für eine Dateifreigabe, ein Verzeichnis oder eine Datei anzuzeigen.  

Verwenden Sie das PowerShell-Cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle), um geöffnete Handles für eine Dateifreigabe, ein Verzeichnis oder eine Datei zu schließen.

> [!Note]  
> Die Cmdlets Get-AzStorageFileHandle und Close-AzStorageFileHandle sind im Az PowerShell-Modul, Version 2.4 oder höher, enthalten. Informationen zum Installieren des neuesten Az PowerShell-Moduls finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fehler „Kein Zugriff“ beim Versuch, auf eine Azure-Dateifreigabe zuzugreifen oder sie zu löschen  
Wenn Sie versuchen, auf eine Azure-Dateifreigabe im Portal zuzugreifen oder sie zu löschen, wird möglicherweise folgende Fehlermeldung angezeigt:

Kein Zugriff  
Fehlercode: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Ursache 1: Virtuelle Netzwerk- oder Firewallregeln sind für das Speicherkonto aktiviert.

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

Stellen Sie sicher, dass virtuelle Netzwerk- und Firewallregeln für das Speicherkonto ordnungsgemäß konfiguriert sind. Um zu testen, ob virtuelle Netzwerk- oder Firewallregeln das Problem verursachen, ändern Sie vorübergehend die Einstellung für das Speicherkonto in **Zugriff aus allen Netzwerken zulassen**. Weitere Informationen finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Ursache 2: Ihr Benutzerkonto besitzt keinen Zugriff auf das Speicherkonto

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Navigieren Sie zu dem Speicherkonto, in dem sich die Azure-Dateifreigabe befindet, klicken Sie auf **Zugriffssteuerung (IAM)** , und überprüfen Sie, ob Ihr Benutzerkonto Zugriff auf das Speicherkonto besitzt. Weitere Informationen finden Sie unter [Schützen Ihres Speicherkontos mit rollenbasierter Zugriffssteuerung (Azure RBAC)](../blobs/security-recommendations.md#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Datei oder Verzeichnis kann nicht geändert, verschoben/umbenannt oder gelöscht werden
Einer der Hauptzwecke einer Dateifreigabe ist, dass mehrere Benutzer und Anwendungen gleichzeitig mit Dateien und Verzeichnissen in der Freigabe interagieren können. Um diese Interaktion zu unterstützen, bieten Dateifreigaben verschiedene Methoden zur Vermittlung des Zugriffs auf Dateien und Verzeichnisse.

Wenn Sie eine Datei aus einer bereitgestellten Azure-Dateifreigabe über SMB öffnen, fordert die Anwendung bzw. das Betriebssystem ein Dateihandle an, bei dem es sich um einen Verweis auf die Datei handelt. Unter anderem gibt die Anwendung beim Anfordern eines Dateihandles einen Dateifreigabemodus an, der die Exklusivitätsebene des Zugriffs auf die Datei bestimmt, die durch Azure Files erzwungen wird: 

- `None`: Sie verfügen über exklusiven Zugriff. 
- `Read`: Andere Benutzer können die Datei lesen, während Sie diese geöffnet haben.
- `Write`: Andere Benutzer können in die Datei schreiben, während Sie diese geöffnet haben. 
- `ReadWrite`: Eine Kombination der Freigabemodi `Read` und `Write`.
- `Delete`: Andere Benutzer können die Datei löschen, während Sie diese geöffnet haben. 

Zwar weist das FileREST-Protokoll als statusfreies Protokoll kein Konzept für Dateihandles auf, doch bietet es einen ähnlichen Mechanismus zur Vermittlung des Zugriffs auf Dateien und Ordner, der vom Skript, der Anwendung oder dem Dienst verwendet werden kann: Dateileases. Wenn eine Datei geleast ist, wird Sie wie ein Dateihandle mit dem Dateifreigabemodus `None` behandelt. 

Obwohl Dateihandles und Leases einen wichtigen Zweck erfüllen, können sie manchmal verwaist sein. Dies kann dann Probleme beim Ändern oder Löschen von Dateien verursachen. Möglicherweise werden Fehlermeldungen wie die folgenden angezeigt:

- Der Prozess kann nicht auf die Datei zugreifen, da sie bereits von einem anderen Prozess verwendet wird.
- Die Aktion kann nicht ausgeführt werden, weil die Datei in einem anderen Programm geöffnet ist.
- Das Dokument ist für die Bearbeitung durch einen anderen Benutzer gesperrt.
- Die angegebene Ressource ist zum Löschen durch einen SMB-Client markiert.

Die Lösung für dieses Problem hängt davon ab, ob es durch ein verwaistes Dateihandle oder eine verwaiste Lease verursacht wird. 

### <a name="cause-1"></a>Ursache 1
Ein Dateihandle verhindert, dass eine Datei bzw. ein Verzeichnis geändert oder gelöscht wird. Sie können das PowerShell-Cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) verwenden, um geöffnete Handles anzuzeigen. 

Wenn alle SMB-Clients ihre geöffneten Handles für eine Datei oder ein Verzeichnis geschlossen haben und das Problem weiterhin auftritt, können Sie das Schließen eines Dateihandles erzwingen.

### <a name="solution-1"></a>Lösung 1
Um das Schließen eines Dateihandles zu erzwingen, verwenden Sie das PowerShell-Cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle). 

> [!Note]  
> Die Cmdlets Get-AzStorageFileHandle und Close-AzStorageFileHandle sind im Az PowerShell-Modul, Version 2.4 oder höher, enthalten. Informationen zum Installieren des neuesten Az PowerShell-Moduls finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

### <a name="cause-2"></a>Ursache 2
Eine Dateilease verhindert, dass eine Datei geändert oder gelöscht wird. Mit dem folgenden PowerShell-Cmdlet können Sie überprüfen, ob eine Datei über eine Dateilease verfügt, Dazu ersetzen Sie `<resource-group>`, `<storage-account>`, `<file-share>` und `<path-to-file>` durch die entsprechenden Werte für Ihre Umgebung:

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

Wenn eine Datei über eine Lease verfügt, sollte das zurückgegebene Objekt die folgenden Eigenschaften enthalten:

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>Lösung 2
Um eine Lease für eine Datei zu entfernen, können Sie die Lease freigeben oder die Lease unterbrechen. Zum Freigeben der Lease benötigen Sie die Lease-ID, die Sie beim Erstellen der Lease festgelegt haben. Sie benötigen die Lease-ID nicht, um die Lease zu unterbrechen.

Im folgenden Beispiel wird gezeigt, wie Sie die Lease für die unter Ursache 2 angegebene Datei unterbrechen (dieses Beispiel wird mit den PowerShell-Variablen aus Ursache 2 fortgesetzt):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Langsames Kopieren von Dateien in und aus Azure Files unter Windows

Die Leistung ist möglicherweise langsam, wenn Sie versuchen, Dateien in den Azure-Dateidienst zu übertragen.

- Wenn Sie keine bestimmte Anforderung für die Mindest-E/A-Größe haben, empfehlen wir Ihnen für eine optimale Leistung die Verwendung von 1 MiB als E/A-Größe.
-   Wenn Sie die endgültige Größe einer Datei kennen, die Sie mit Schreibvorgängen erweitern, und Ihre Software keine Kompatibilitätsprobleme aufweist, wenn das noch nicht geschriebene Fragment in der Datei Nullen enthält, legen Sie die Dateigröße im Voraus fest (anstatt jeden Schreibvorgang zu einem Erweiterungsschreibvorgang zu machen).
-   Verwenden Sie die richtige Kopiermethode:
    -   Verwenden Sie [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) für Übertragungen zwischen zwei Dateifreigaben.
    -   Verwenden Sie [Robocopy](./storage-files-deployment-guide.md#robocopy) zwischen Dateifreigaben auf einem lokalen Computer.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Überlegungen zu Windows 8.1 oder Windows Server 2012 R2

Stellen Sie für Clients, die Windows 8.1 oder Windows Server 2012 R2 ausführen, sicher, dass der Hotfix [KB3114025](https://support.microsoft.com/help/3114025) installiert ist. Dieser Hotfix verbessert die Leistung beim Erstellen und Schließen eines Handle.

Sie können das folgende Skript ausführen, um zu überprüfen, ob der Hotfix installiert wurde:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Wenn der Hotfix installiert wurde, wird die folgende Ausgabe angezeigt:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Bei Windows Server 2012 R2-Images in Azure Marketplace ist der Hotfix KB3114025 ab Dezember 2015 standardmäßig installiert.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Kein Ordner mit einem Laufwerkbuchstaben in „Arbeitsplatz“ oder „Dieser PC“

Wenn Sie eine Azure-Dateifreigabe als Administrator mithilfe des Befehls „net use“ zuordnen, scheint es so, als würde die Freigabe fehlen.

### <a name="cause"></a>Ursache

Standardmäßig wird der Windows-Datei-Explorer nicht als Administrator ausgeführt. Wenn Sie „net use“ von einer administrativen Eingabeaufforderung aus ausführen, ordnen Sie das Netzlaufwerk als Administrator zu. Da zugeordnete Laufwerke benutzerorientiert sind, zeigt das angemeldete Benutzerkonto die Laufwerke nicht an, wenn sie unter einem anderen Benutzerkonto eingebunden werden.

### <a name="solution"></a>Lösung
Binden Sie die Freigabe von einer Befehlszeile für Benutzer ohne Administratorrechte aus ein. Alternativ können Sie [diesem TechNet-Thema folgen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)), um den Registrierungswert **EnableLinkedConnections** zu konfigurieren.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Der Befehl „net use“ schlägt fehl, wenn das Speicherkonto einen Schrägstrich enthält

### <a name="cause"></a>Ursache

Der Befehl „net use“ interpretiert einen Schrägstrich (/) als Befehlszeilenoption. Wenn der Name Ihres Benutzerkontos mit einem Schrägstrich beginnt, schlägt die Zuordnung des Laufwerks fehl.

### <a name="solution"></a>Lösung

Sie können eine der folgenden Methoden verwenden, um dieses Problem zu umgehen:

- Führen Sie den folgenden PowerShell-Befehl aus:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Von einer Batchdatei aus können Sie wie unten angegeben den Befehl ausführen:

  `Echo new-smbMapping ... | powershell -command –`

- Setzen Sie den Schlüssel in doppelte Anführungszeichen, um dieses Problem zu umgehen, es sei denn, der Schrägstich ist das erste Zeichen. Wenn dies der Fall ist, verwenden Sie den interaktiven Modus, und geben Sie Ihr Kennwort separat ein, oder generieren Sie Ihre Schlüssel neu, um einen Schlüssel zu erhalten, der nicht mit einem Schrägstrich beginnt.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Die Anwendung oder der Dienst kann auf kein bereitgestelltes Azure Files-Laufwerk zugreifen.

### <a name="cause"></a>Ursache

Laufwerke werden pro Benutzer eingebunden. Wenn Ihre Anwendung oder der Dienst unter einem anderen Benutzerkonto als dem ausgeführt wird, welches das Laufwerk bereitgestellt hat, wird für die Anwendung das Laufwerk nicht angezeigt.

### <a name="solution"></a>Lösung

Verwenden Sie eine der folgenden Lösungen:

-   Binden Sie das Laufwerk vom selben Benutzerkonto ein, das auch die Anwendung enthält. Sie können ein Tool wie z.B. PsExec verwenden.
- Übergeben Sie den Speicherkontonamen und -schlüssel an die Parameter des „net use“-Befehls.
- Verwenden Sie die den Befehl cmdkey, um die Anmeldeinformationen in Credential Manager hinzuzufügen. Führen Sie dies über eine Befehlszeile im Kontext des Dienstkontos aus. Verwenden Sie dazu entweder eine interaktive Anmeldung oder `runas`.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Ordnen Sie die Freigabe direkt zu, ohne einen zugeordneten Laufwerkbuchstaben zu verwenden. Einige Anwendungen stellen möglicherweise nicht ordnungsgemäß die Verbindung mit dem Laufwerkbuchstaben wieder her, sodass die Verwendung des vollständigen UNC-Pfads zuverlässiger sein kann. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Nachdem Sie die erforderlichen Schritte der Anleitung durchgeführt haben, erhalten Sie womöglich die folgende Fehlermeldung, wenn Sie „net use“ für das System- bzw. Netzwerkdienstkonto ausführen:„Systemfehler 1312 ist aufgetreten. „Systemfehler 1312 ist aufgetreten. Eine angegebene Anmeldesitzung ist nicht vorhanden. Sie wird möglicherweise bereits verwendet.“ Wenn dies auftritt, vergewissern Sie sich, dass der an „net use“ übergebene Benutzername Domäneninformationen enthält (z.B. „[Speicherkontoname].file.core.windows.net“).

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Fehler „You are copying a file to a destination that does not support encryption“ (Sie kopieren eine Datei in ein Ziel, das die Verschlüsselung nicht unterstützt)

Wenn eine Datei über das Netzwerk kopiert wird, wird die Datei auf dem Quellcomputer entschlüsselt, in Klartext übermittelt und am Ziel erneut verschlüsselt. Möglicherweise wird jedoch der folgende Fehler angezeigt, wenn Sie versuchen, eine verschlüsselte Datei zu kopieren: „You are copying the file to a destination that does not support encryption“ (Sie kopieren die Datei in ein Ziel, das die Verschlüsselung nicht unterstützt).

### <a name="cause"></a>Ursache
Dieses Problem kann auftreten, wenn Sie ein verschlüsselndes Dateisystem (EFS, Encrypting File System) verwenden. Mit BitLocker verschlüsselte Dateien können in Azure Files kopiert werden. Azure Files unterstützt NTFS EFS jedoch nicht.

### <a name="workaround"></a>Problemumgehung
Um eine Datei über das Netzwerk zu kopieren, müssen Sie sie zunächst entschlüsseln. Verwenden Sie eine der folgenden Methoden:

- Verwenden Sie den **copy /d**-Befehl. Sie können die verschlüsselten Dateien dadurch als entschlüsselte Dateien am Ziel speichern.
- Legen Sie den folgenden Registrierungsschlüssel fest:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Werttyp = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

Beachten Sie jedoch, dass sich das Festlegen des Registrierungsschlüssels auf alle Kopiervorgänge von Netzwerkfreigaben auswirkt.

## <a name="slow-enumeration-of-files-and-folders"></a>Langsame Enumeration von Dateien und Ordnern

### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn auf dem Clientcomputer nicht ausreichend Cache für große Verzeichnisse vorhanden ist.

### <a name="solution"></a>Lösung

Um dieses Problem zu beheben, passen Sie den Registrierungswert **DirectoryCacheEntrySizeMax** an, um das Zwischenspeichern größerer Verzeichnisauflistungen auf dem Clientcomputer zu ermöglichen:

- Standort: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Wertname: DirectoryCacheEntrySizeMax 
- Werttyp: DWORD
 
 
Sie können den Wert beispielsweise auf 0x100000 festlegen und überprüfen, ob sich die Leistung verbessert.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Fehler „AadDsTenantNotFound“ beim Aktivieren der Authentifizierung von Azure Active Directory Domain Services (Azure AD DS) für Azure Files mit der Fehlermeldung „Unable to locate active tenants with tenant Id aad-tenant-id“ (Es wurden keine aktiven Mandanten mit der Mandanten-ID „aad-tenant-id“ gefunden.)

### <a name="cause"></a>Ursache

Der Fehler „AadDsTenantNotFound“ tritt auf, wenn Sie versuchen, die [Aktivierung der Azure Active Directory Domain Services-Authentifizierung über Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) für ein Speicherkonto vorzunehmen, für das [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md) nicht im Azure AD-Mandanten des zugehörigen Abonnements erstellt wurde.  

### <a name="solution"></a>Lösung

Aktivieren Sie Azure AD DS für den Azure AD-Mandanten des Abonnements, in dem Ihr Speicherkonto bereitgestellt ist. Sie benötigen Administratorrechte für den Azure AD-Mandanten, um eine verwaltete Domäne erstellen zu können. Wenn Sie nicht der Administrator des Azure AD-Mandanten sind, wenden Sie sich an den Administrator, und folgen Sie der Schrittanleitung zum [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne](../../active-directory-domain-services/tutorial-create-instance.md).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Azure Files mit AD-Anmeldeinformationen kann nicht eingebunden werden 

### <a name="self-diagnostics-steps"></a>Selbstdiagnoseschritte
Stellen Sie zunächst sicher, dass Sie alle vier Schritte durchgeführt haben, um die [Azure Files-AD-Authentifizierung](./storage-files-identity-auth-active-directory-enable.md) zu aktivieren.

Versuchen Sie dann, die [Azure-Dateifreigabe mit dem Speicherkontoschlüssel](./storage-how-to-use-files-windows.md) einzubinden. Wenn beim Einbinden ein Fehler aufgetreten ist, laden Sie [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) herunter, um die Clientausführungsumgebung zu überprüfen und die nicht kompatible Clientkonfiguration zu erkennen, die zu einem Zugriffsfehler für Azure Files führen würde. Zudem stellt das Tool Anleitungen zur Selbsthilfe bereit und erfasst die Diagnoseablaufverfolgungen.

Drittens können Sie das Cmdlet Debug-AzStorageAccountAuth ausführen, um eine Reihe grundlegender Überprüfungen Ihrer AD-Konfiguration mit dem angemeldeten AD-Benutzer durchzuführen. Dieses Cmdlet wird von der [Version AzFilesHybrid v0.1.2+](https://github.com/Azure-Samples/azure-files-samples/releases) unterstützt. Sie müssen dieses Cmdlet mit einem AD-Benutzer ausführen, der über die Besitzerberechtigung für das Zielspeicherkonto verfügt.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
Das Cmdlet führt diese nachfolgenden Überprüfungen der Reihe nach durch und bietet Anleitungen zu Fehlern:
1. CheckADObjectPasswordIsCorrect: Sicherstellen, dass das für die AD-Identität konfigurierte Kennwort, das das Speicherkonto darstellt, mit dem kerb1- oder kerb2-Schlüssel des Speicherkontos übereinstimmt. Wenn das Kennwort falsch ist, können Sie [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) ausführen, um das Kennwort zurückzusetzen. 
2. CheckADObject: Vergewissern Sie sich, dass in Active Directory ein Objekt vorhanden ist, das das Speicherkonto darstellt und den richtigen SPN (Dienstprinzipalnamen) aufweist. Wenn der SPN nicht ordnungsgemäß eingerichtet ist, führen Sie das Cmdlet „Set-AD“ aus, das im Debug-Cmdlet zurückgegeben wird, um den SPN zu konfigurieren.
3. CheckDomainJoined: Überprüfen Sie, ob der Clientcomputer Mitglied der AD-Domäne ist. Wenn Ihr Computer nicht in die AD-Domäne eingebunden ist, finden Sie weitere Informationen in diesem [Artikel](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) mit Anweisungen zum Domänenbeitritt.
4. CheckPort445Connectivity: Überprüfen Sie, ob Port 445 für SMB-Verbindungen geöffnet ist. Wenn der erforderliche Port nicht geöffnet ist, finden Sie im Tool zur Problembehandlung [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) Informationen zu Konnektivitätsproblemen mit Azure Files.
5. CheckSidHasAadUser: Überprüfung, ob der angemeldete AD-Benutzer mit Azure AD synchronisiert ist. Wenn Sie nachschlagen möchten, ob ein bestimmter AD-Benutzer mit Azure AD synchronisiert ist, können Sie den Benutzernamen und die Domäne in den Eingabeparametern angeben. 
6. CheckGetKerberosTicket: Versuch, ein Kerberos-Ticket für die Verbindung mit dem Speicherkonto zu erhalten. Wenn kein gültiges Kerberos-Token vorhanden ist, führen Sie das Cmdlet „klist get cifs/storage-account-name.file.core.windows.net“ aus, und betrachten Sie den Fehlercode, um die Ursache für den Fehler beim Ticketabruf zu ermitteln.
7. CheckStorageAccountDomainJoined: Überprüfung, ob die AD-Authentifizierung aktiviert wurde und die AD-Eigenschaften des Kontos eingegeben wurden. Wenn dies nicht der Fall ist, finden Sie [hier](./storage-files-identity-ad-ds-enable.md) eine Anleitung zum Aktivieren der AD DS-Authentifizierung für Azure Files. 

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Berechtigungen auf Verzeichnis-/Dateiebene (Windows-ACLs) können nicht mit Windows-Datei-Explorer konfiguriert werden

### <a name="symptom"></a>Symptom

Beim Versuch, Windows-ACLs mit dem Datei-Explorer auf einer bereitgestellten Dateifreigabe zu konfigurieren, treten möglicherweise die folgenden Symptome auf:
- Nachdem Sie auf der Registerkarte „Sicherheit“ auf „Berechtigung bearbeiten“ geklickt haben, wird der Berechtigungs-Assistent nicht geladen. 
- Wenn Sie versuchen, einen neuen Benutzer oder eine neue Gruppe auszuwählen, zeigt der Domänenort nicht die richtige AD DS-Domäne an. 

### <a name="solution"></a>Lösung

Wir empfehlen Ihnen, als Problemumgehung die Berechtigungen auf Verzeichnis-/Dateiebene mithilfe des [icacls-Tools](/windows-server/administration/windows-commands/icacls) zu konfigurieren. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Fehler beim Ausführen des Cmdlets „Join-AzStorageAccountForAuth“

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Error: „Der Verzeichnisdienst kann keinen relativen Bezeichner zuweisen“

Dieser Fehler kann auftreten, wenn ein Domänencontroller, der die FSMO-Rolle (RID-Master) innehat, nicht verfügbar ist oder aus der Domäne entfernt und aus einer Sicherung wiederhergestellt wurde.  Vergewissern Sie sich, dass alle Domänencontroller ausgeführt werden und verfügbar sind.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Fehler: „Die Positionsparameter können nicht gebunden werden, da keine Namen angegeben wurden.“

Dieser Fehler wird wahrscheinlich durch einen Syntaxfehler im Befehl „Join-AzStorageAccountforAuth“ verursacht.  Überprüfen Sie den Befehl auf Rechtschreib- oder Syntaxfehler, und vergewissern Sie sich, dass die neueste Version des AzFilesHybrid-Moduls (https://github.com/Azure-Samples/azure-files-samples/releases) installiert ist.  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Azure Files-Unterstützung der lokalen AD DS-Authentifizierung für die AES 256-Kerberos-Verschlüsselung

Mit dem [AzFilesHybrid-Modul v0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases) haben wir die Azure Files-Unterstützung der lokalen AD DS-Authentifizierung für die AES 256-Kerberos-Verschlüsselung eingeführt. Wenn Sie die AD DS-Authentifizierung mit einer Modulversion unter v0.2.2 aktiviert haben, müssen Sie das neueste AzFilesHybrid-Modul (v0.2.2+) herunterladen und PowerShell nachfolgend ausführen. Wenn Sie die AD DS-Authentifizierung für Ihr Speicherkonto noch nicht aktiviert haben, können Sie zur Aktivierung diesem [Leitfaden](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) folgen. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.
[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.