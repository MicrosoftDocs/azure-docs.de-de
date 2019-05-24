---
title: Behandeln von Azure Files-Problemen unter Windows | Microsoft-Dokumentation
description: Behandeln von Azure Files-Problemen unter Windows
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 9658ed46e1a46aa3fc2c7fe251fd73b2ef0a13dd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991363"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Behandeln von Azure Files-Problemen unter Windows

Dieser Artikel beschreibt allgemeine Probleme im Zusammenhang mit Microsoft Azure Files, wenn Sie eine Verbindung von Windows-Clients herstellen. Darüber hinaus werden die möglichen Ursachen und Lösungen für diese Probleme bereitgestellt. Zusätzlich zu den Schritten zur Problembehandlung in diesem Artikel können Sie auch [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)  verwenden, um sicherzustellen, dass die Windows-Clientumgebung über die richtigen erforderlichen Komponenten verfügt. AzFileDiagnostics automatisiert die Erkennung eines Großteils der Symptome, die in diesem Artikel erwähnt werden und hilft, Ihre Umgebung einzurichten, um eine optimale Leistung zu erzielen. Sie erhalten diese Informationen auch unter [Problembehandlung für Azure Files-Freigaben](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Hier finden Sie Schritte zum Beheben von Problemen mit dem Verbinden, Zuordnen und Einbinden von Azure Files-Freigaben.

<a id="error5"></a>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fehler 5 beim Bereitstellen einer Azure-Dateifreigabe

Wenn Sie versuchen, eine Dateifreigabe bereitzustellen, erhalten Sie möglicherweise den folgenden Fehler:

- „Systemfehler 5 ist aufgetreten. Zugriff verweigert.“

### <a name="cause-1-unencrypted-communication-channel"></a>Ursache 1: Unverschlüsselter Kommunikationskanal

Aus Sicherheitsgründen werden Verbindungen mit Azure-Dateifreigaben blockiert, wenn der Kommunikationskanal nicht verschlüsselt ist und der Verbindungsversuch nicht von dem gleichen Datencenter aus erfolgt, in dem sich die Azure-Dateifreigaben befinden. Unverschlüsselte Verbindungen innerhalb desselben Datencenters können auch blockiert werden, wenn die Einstellung [Sichere Übertragung erforderlich](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) für das Speicherkonto aktiviert ist. Ein verschlüsselter Kommunikationskanal wird nur dann bereitgestellt, wenn das Clientbetriebssystem des Benutzers SMB-Verschlüsselung unterstützt.

Windows 8, Windows Server 2012 und höhere Versionen jedes Systems handeln Anforderung aus, die SMB 3.0 umfassen, wodurch die Verschlüsselung unterstützt wird.

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

1. Stellen Sie eine Verbindung von einem Client aus her, der SMB-Verschlüsselung unterstützt (Windows 8, Windows Server 2012 oder höher), oder stellen Sie eine Verbindung von einem virtuellen Computer aus her, der sich im selben Datencenter wie das Azure-Speicherkonto befindet, das für die Azure-Dateifreigabe verwendet wird.
2. Vergewissern Sie sich, dass die Einstellung [Sichere Übertragung erforderlich](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) im Speicherkonto deaktiviert ist, wenn der Client keine SMB-Verschlüsselung unterstützt.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Ursache 2: Virtuelle Netzwerk- oder Firewallregeln sind für das Speicherkonto aktiviert. 

Wenn für das Speicherkonto Regeln für das virtuelle Netzwerk (VNET) und die Firewall konfiguriert sind, wird dem Netzwerkverkehr der Zugriff verweigert, es sei denn, der Client-IP-Adresse oder dem virtuellen Netzwerk wird der Zugriff gestattet.

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Stellen Sie sicher, dass virtuelle Netzwerk- und Firewallregeln für das Speicherkonto ordnungsgemäß konfiguriert sind. Um zu testen, ob virtuelle Netzwerk- oder Firewallregeln das Problem verursachen, ändern Sie vorübergehend die Einstellung für das Speicherkonto in **Zugriff aus allen Netzwerken zulassen**. Weitere Informationen finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>„Fehler 53“, „Fehler 67“ oder „Fehler 87“ beim Versuch, eine Azure-Dateifreigabe einzubinden oder die Einbindung aufzuheben

Wenn Sie versuchen, eine Dateifreigabe aus einem lokalen oder einem anderen Datencenter einzubinden, erhalten Sie möglicherweise die folgenden Fehler:

- „Systemfehler 53 ist aufgetreten. Der Netzwerkpfad wurde nicht gefunden.“
- „Systemfehler 67 ist aufgetreten. „Der Netzwerkname wurde nicht gefunden.“
- „Systemfehler 87 ist aufgetreten. „Der Parameter ist falsch.“

### <a name="cause-1-port-445-is-blocked"></a>Ursache 1: Port 445 ist blockiert

„Systemfehler 53“ und „Systemfehler 67“ können auftreten, wenn die von Port 445 ausgehende Kommunikation zu einem Azure Files-Datencenter blockiert ist. Um eine Zusammenfassung der ISPs anzuzeigen, die den Zugang von Port 445 aus zulassen oder verweigern, gehen Sie auf [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Um festzustellen, ob Port 445 durch Ihre Firewall oder Ihren Internetdienstanbieter blockiert wird, verwenden Sie das Tool [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) oder das Cmdlet `Test-NetConnection`. 

Um das Cmdlet `Test-NetConnection` verwenden zu können, muss das Azure PowerShell-Modul installiert sein. Weitere Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps). Denken Sie daran, `<your-storage-account-name>` und `<your-resource-group-name>` durch die entsprechenden Namen für Ihr Speicherkonto zu ersetzen.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Wenn das Verbinden erfolgreich war, wird die folgende Ausgabe angezeigt:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> Der oben genannte Befehl gibt die aktuelle IP-Adresse des Speicherkontos zurück. Diese IP-Adresse bleibt nicht garantiert gleich und kann sich jederzeit ändern. Verwenden Sie keine Hartkodierung für diese IP-Adresse in Skripts oder einer Firewallkonfiguration.

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

#### <a name="solution-1---use-azure-file-sync"></a>Lösung 1: Azure-Dateisynchronisierung
Mit der Azure-Dateisynchronisierung können Ihre lokalen Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe werden. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Die Azure-Dateisynchronisierung erfolgt über Port 443 und kann daher als Umgehung für den Zugriff auf Azure Files von Clients verwendet werden, bei denen Port 445 gesperrt ist. [Erfahren Sie, wie Sie die Azure-Dateisynchronisierung einrichten](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Lösung 2: VPN
Indem Sie ein VPN für Ihr spezifisches Speicherkonto einrichten, durchläuft der Datenverkehr im Vergleich zum Internet einen sicheren Tunnel. Befolgen Sie die [Anweisungen zur Einrichtung eines VPN](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) für den Zugriff auf Azure Files aus Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Lösung 3: Aufheben der Sperre von Port 445 mithilfe Ihres ISP/IT-Administrators
Arbeiten Sie mit Ihrer IT-Abteilung oder dem ISP zusammen, um den Ausgang von Datenverkehr über Port 445 für [IP-Bereiche von Azure](https://www.microsoft.com/download/details.aspx?id=41653) zu öffnen.

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Lösung 4: REST-API-basierte Tools wie Storage-Explorer/PowerShell
Azure Files unterstützt neben SMB auch REST. Der REST-Zugriff erfolgt über Port 443 (Standard: TCP). Es gibt verschiedene mit der REST-API geschriebene Tools, die eine vielseitige Benutzeroberfläche bereitstellen. [Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) ist eines davon. [Laden Sie Storage-Explorer herunter, installieren Sie ihn](https://azure.microsoft.com/features/storage-explorer/), und stellen eine Verbindung mit Ihrer Dateifreigabe in Azure Files her. Sie können auch [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) verwenden, die auch die REST-API nutzt.


### <a name="cause-2-ntlmv1-is-enabled"></a>Ursache 2: NTLMv1 ist aktiviert

„Systemfehler 53“ oder „Systemfehler 87“ können auftreten, wenn die NTLMv1-Kommunikation auf dem Client aktiviert ist. Azure Files unterstützt nur die NTLMv2-Authentifizierung. Wenn NTLMv1 aktiviert ist, ist der Client weniger sicher. Aus diesem Grund wird die Kommunikation für Azure Files blockiert. 

Um zu bestimmen, ob dies die Ursache des Fehlers ist, überprüfen Sie, ob der folgende Registrierungsunterschlüssel auf den Wert 3 festgelegt ist:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa &gt; LmCompatibilityLevel**

Weitere Informationen finden Sie im Thema [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) im TechNet.

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Setzen Sie den Wert **LmCompatibilityLevel** auf den Standardwert 3 im folgenden Registrierungsunterschlüssel zurück:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Fehler 1816 „Nicht genügend Quoten verfügbar, um diesen Befehl zu verarbeiten“, wenn Sie in eine Azure-Dateifreigabe kopieren

### <a name="cause"></a>Ursache

Der Fehler 1816 tritt auf, wenn Sie die Obergrenze der parallelen offenen Handles erreichen, die für eine Datei auf dem Computer zulässig sind, auf dem die Dateifreigabe bereitgestellt wird.

### <a name="solution"></a>Lösung

Reduzieren Sie die Anzahl der gleichzeitig geöffneten Handles, indem Sie einige Handles schließen und es anschließend erneut versuchen. Weitere Informationen finden Sie unter [Checkliste zur Leistung und Skalierbarkeit von Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>Fehler „Zugriff verweigert“ beim Navigieren zu einer Azure-Dateifreigabe im Portal

Wenn Sie zu einer Azure-Dateifreigabe im Portal navigieren, erhalten Sie ggf. den folgenden Fehler:

Zugriff verweigert.  
Sie haben keinen Zugriff.  
Sie scheinen keinen Zugriff auf diesen Inhalt zu haben. Wenden Sie sich an den Besitzer, um Zugriff zu erhalten.  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Ursache 1: Ihr Benutzerkonto besitzt keinen Zugriff auf das Speicherkonto

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

Navigieren Sie zu dem Speicherkonto, in dem sich die Azure-Dateifreigabe befindet, klicken Sie auf **Zugriffssteuerung (IAM)**, und überprüfen Sie, ob Ihr Benutzerkonto Zugriff auf das Speicherkonto besitzt. Weitere Informationen finden Sie unter [Sichern Ihres Speicherkontos mit rollenbasierter Zugriffssteuerung (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Ursache 2: Virtuelle Netzwerk- oder Firewallregeln sind für das Speicherkonto aktiviert.

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Stellen Sie sicher, dass virtuelle Netzwerk- und Firewallregeln für das Speicherkonto ordnungsgemäß konfiguriert sind. Um zu testen, ob virtuelle Netzwerk- oder Firewallregeln das Problem verursachen, ändern Sie vorübergehend die Einstellung für das Speicherkonto in **Zugriff aus allen Netzwerken zulassen**. Weitere Informationen finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Langsames Kopieren von Dateien in und aus Azure Files unter Windows

Die Leistung ist möglicherweise langsam, wenn Sie versuchen, Dateien in den Azure-Dateidienst zu übertragen.

- Wenn Sie keine bestimmte Anforderung für die Mindest-E/A-Größe haben, empfehlen wir Ihnen für eine optimale Leistung die Verwendung von 1 MiB als E/A-Größe.
-   Wenn Sie die endgültige Größe einer Datei kennen, die Sie mit Schreibvorgängen erweitern, und Ihre Software keine Kompatibilitätsprobleme aufweist, wenn das noch nicht geschriebene Fragment in der Datei Nullen enthält, legen Sie die Dateigröße im Voraus fest (anstatt jeden Schreibvorgang zu einem Erweiterungsschreibvorgang zu machen).
-   Verwenden Sie die richtige Kopiermethode:
    -   Verwenden Sie [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) für Übertragungen zwischen zwei Dateifreigaben.
    -   Verwenden Sie [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) zwischen Dateifreigaben auf einem lokalen Computer.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Überlegungen zu Windows 8.1 oder Windows Server 2012 R2

Stellen Sie für Clients, die Windows 8.1 oder Windows Server 2012 R2 ausführen, sicher, dass der Hotfix [KB3114025](https://support.microsoft.com/help/3114025) installiert ist. Dieser Hotfix verbessert die Leistung beim Erstellen und Schließen eines Handle.

Sie können das folgende Skript ausführen, um zu überprüfen, ob der Hotfix installiert wurde:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Wenn der Hotfix installiert wurde, wird die folgende Ausgabe angezeigt:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Bei Windows Server 2012 R2-Images in Azure Marketplace ist der Hotfix KB3114025 ab Dezember 2015 standardmäßig installiert.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Kein Ordner mit einem Laufwerkbuchstaben im **Arbeitsplatz**

Wenn Sie eine Azure-Dateifreigabe als Administrator mithilfe des Befehls „net use“ zuordnen, scheint es so, als würde die Freigabe fehlen.

### <a name="cause"></a>Ursache

Standardmäßig wird der Windows-Datei-Explorer nicht als Administrator ausgeführt. Wenn Sie „net use“ von einer administrativen Eingabeaufforderung aus ausführen, ordnen Sie das Netzlaufwerk als Administrator zu. Da zugeordnete Laufwerke benutzerorientiert sind, zeigt das angemeldete Benutzerkonto die Laufwerke nicht an, wenn sie unter einem anderen Benutzerkonto eingebunden werden.

### <a name="solution"></a>Lösung
Binden Sie die Freigabe von einer Befehlszeile für Benutzer ohne Administratorrechte aus ein. Alternativ können Sie [diesem TechNet-Thema folgen](https://technet.microsoft.com/library/ee844140.aspx), um den Registrierungswert **EnableLinkedConnections** zu konfigurieren.

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
- Verwenden Sie die den Befehl cmdkey, um die Anmeldeinformationen in Credential Manager hinzuzufügen. Führen Sie dies über eine Befehlszeile im Kontext des Dienstkontos aus. Verwenden Sie dazu entweder eine interaktive Anmeldung oder „runas“.
  
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
Um eine Datei über das Netzwerk zu kopieren, müssen Sie sie zunächst entschlüsseln. Verwenden Sie eine der folgenden Methoden an:

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

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Fehler „AadDsTenantNotFound“ beim Aktivieren der Azure Active Directory-Authentifizierung für Azure Files mit der Fehlermeldung „Unable to locate active tenants with tenant Id aad-tenant-id“ (Es wurden keine aktiven Mandanten mit der Mandanten-ID „aad-tenant-id“ gefunden)

### <a name="cause"></a>Ursache

Der Fehler „AadDsTenantNotFound“ tritt auf, wenn Sie versuchen, die [Aktivierung der Azure Active Directory-Authentifizierung (AAD) für Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) für ein Speicherkonto vorzunehmen, für das [Azure Active Directory Domain Services (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) nicht im AAD-Mandanten des zugehörigen Abonnements erstellt wurde.  

### <a name="solution"></a>Lösung

Aktivieren Sie AAD DS im AAD-Mandanten des Abonnements, in dem Ihr Speicherkonto bereitgestellt ist. Sie benötigen Administratorrechte für den AAD-Mandanten, um eine verwaltete Domäne zu erstellen. Wenn Sie nicht der Administrator des Azure AD-Mandanten sind, wenden Sie sich an den Administrator und folgen Sie der schrittweisen Anleitung zum [Aktivieren von Azure Active Directory Domain Services über das Azure-Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.
[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.
