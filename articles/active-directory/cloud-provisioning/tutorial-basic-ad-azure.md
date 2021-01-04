---
title: 'Tutorial: Grundlegende lokale Active Directory- und Azure AD-Umgebung'
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b6acaa76d21cb1321feb5466c4fb7a9bacfa846
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348108"
---
# <a name="tutorial-basic-active-directory-environment"></a>Tutorial: Grundlegende Active Directory-Umgebung

In diesem Tutorial werden die einzelnen Schritte zum Erstellen einer grundlegenden Active Directory Umgebung erläutert. 

![Diagramm: Grundlegende Azure AD-Umgebung](media/tutorial-single-forest/diagram1.png)

Mithilfe der Umgebung, die Sie im Tutorial erstellen, können Sie verschiedene Aspekte von Hybrididentitätsszenarien testen. Diese Umgebung ist zudem Voraussetzung für einige Tutorials.  Wenn Sie bereits über eine vorhandene Active Directory Umgebung verfügen, können Sie diese stattdessen verwenden.  Diese Informationen richten sich an Einzelpersonen ohne Vorkenntnisse.

Dieses Tutorial besteht aus folgenden Elementen:
## <a name="prerequisites"></a>Voraussetzungen
Im Folgenden finden Sie die erforderlichen Komponenten für die Durchführung dieses Tutorials:
- Ein Computer mit installiertem [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview).  Es wird empfohlen, dies entweder auf einem [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os)- oder einem [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)-Computer durchzuführen.
- Ein [externer Netzwerkadapter](/virtualization/hyper-v-on-windows/quick-start/connect-to-network), um dem virtuellen Computer die Kommunikation mit dem Internet zu ermöglichen.
- [Ein Azure-Abonnement](https://azure.microsoft.com/free)
- Eine Kopie von Windows Server 2016.
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> In diesem Tutorial werden PowerShell-Skripts verwendet, sodass Sie die Tutorialumgebung in kürzester Zeit erstellen können.  Jedes der Skripts verwendet Variablen, die am Anfang jedes Skripts deklariert werden.  Sie können und sollten die Variablen entsprechend Ihrer Umgebung ändern.
>
>Mit den verwendeten Skripts wird vor der Installation des Agents für die Azure AD Connect-Cloudbereitstellung eine allgemeine Active Directory-Umgebung erstellt.  Sie sind für alle Tutorials relevant.
>
> Kopien der in diesem Tutorial verwendeten PowerShell-Skripts sind unter [diesem Link](https://github.com/billmath/tutorial-phs) auf GitHub verfügbar.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers
Um die Hybrididentitätsumgebung einzurichten und auszuführen, müssen Sie als Erstes einen virtuellen Computer erstellen, der als lokaler Active Directory-Server verwendet wird.  Gehen Sie folgendermaßen vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>Durchführen der Betriebssystembereitstellung
Um das Erstellen des virtuellen Computers durchzuführen, müssen Sie die Installation des Betriebssystems abschließen.

1. Doppelklicken Sie im Hyper-V-Manager auf den virtuellen Computer.
2. Klicken Sie auf die Schaltfläche „Starten“.
3. Sie werden zu Folgendem aufgefordert: „Drücken Sie eine beliebige Taste, um von CD oder DVD zu starten“. Fahren Sie fort, und tun Sie dies.
4. Wählen Sie auf dem Windows Server-Startbildschirm Ihre Sprache aus, und klicken Sie auf **Weiter**.
5. Klicken Sie auf **Jetzt installieren**.
6. Geben Sie Ihren Lizenzschlüssel ein, und klicken Sie auf **Weiter**.
7. Aktivieren Sie „Ich stimme den Lizenzbedingungen zu“, und klicken Sie auf **Weiter**.
8. Wählen Sie **Benutzerdefiniert:  Nur Windows installieren (Erweitert)** aus.
9. Klicken Sie auf **Weiter**.
10. Nach Abschluss der Installation starten Sie den virtuellen Computer neu, melden sich an und führen Windows-Updates aus, um sicherzustellen, dass die VM auf dem neuesten Stand ist.  Installieren Sie die neuesten Updates.

## <a name="install-active-directory-prerequisites"></a>Installieren der erforderlichen Active Directory-Komponenten
Nachdem Sie nun über einen virtuellen Computer verfügen, müssen Sie vor der Installation von Active Directory noch ein paar Vorbereitungen treffen.  Das heißt, dass Sie den virtuellen Computer umbenennen, eine statische IP-Adresse und DNS-Informationen festlegen und die Remoteserver-Verwaltungstools installieren müssen.   Gehen Sie folgendermaßen vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>Erstellen einer Windows Server AD-Umgebung
Nachdem nun der virtuelle Computer erstellt und umbenannt wurde und über eine statische IP-Adresse verfügt, können Sie als Nächstes die Active Directory Domain Services installieren und konfigurieren.  Gehen Sie folgendermaßen vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Erstellen eines Windows Server AD-Benutzers
Nachdem nun die Active Directory-Umgebung vorhanden ist, benötigen Sie ein Testkonto.  Dieses Konto wird in unserer lokalen Active Directory-Umgebung erstellt und dann mit Azure AD synchronisiert.  Gehen Sie folgendermaßen vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>Erstellen eines Azure AD-Mandanten
Jetzt müssen Sie einen Azure AD-Mandanten erstellen, damit Sie Ihre Benutzer mit der Cloud synchronisieren können.  Gehen Sie zum Erstellen eines neuen Azure AD-Mandanten wie folgt vor:

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), und melden Sie sich mit einem Konto an, das über ein Azure-Abonnement verfügt.
2. Wählen Sie das **Plussymbol (+)** aus, und suchen Sie nach **Azure Active Directory**.
3. Wählen Sie in den Suchergebnissen **Azure Active Directory** aus.
4. Klicken Sie auf **Erstellen**.</br>
![Screenshot: Seite „Azure Active Directory“ im Azure-Portal](media/tutorial-single-forest/create1.png)</br>
5. Geben Sie einen **Namen für die Organisation** an, zusammen mit dem **Namen der Anfangsdomäne**. Klicken Sie anschließend auf **Erstellen**. Hierdurch wird Ihr Verzeichnis erstellt.
6. Sobald dies abgeschlossen ist, klicken Sie auf **diesen Link**, um das Verzeichnis zu verwalten.

## <a name="create-a-global-administrator-in-azure-ad"></a>Erstellen eines globalen Administrators in Azure AD
Nachdem nun ein Azure AD-Mandant vorhanden ist, erstellen Sie ein globales Administratorkonto.  Gehen Sie zum Erstellen des globalen Administratorkontos wie folgt vor:

1.  Wählen Sie unter **Verwalten** die Option **Benutzer** aus.</br>
![Screenshot: Menü „Übersicht“ mit ausgewählter Option „Benutzer“](media/tutorial-single-forest/administrator1.png)</br>
2.  Wählen Sie **Alle Benutzer** und dann **+ Neuer Benutzer** aus.
3.  Geben Sie für diesen Benutzer einen Namen und Benutzernamen an. Dieser wird Ihr globaler Administrator für den Mandanten. Sie sollten außerdem die **Verzeichnisrolle** in **Globaler Administrator** ändern. Sie können auch das temporäre Kennwort anzeigen. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.</br>
![Erstellen](media/tutorial-single-forest/administrator2.png)</br>
4. Sobald dies abgeschlossen ist, öffnen Sie einen neuen Webbrowser, und melden Sie sich mit dem neuen globalen Administratorkonto und dem temporären Kennwort bei „myapps.microsoft.com“ an.
5. Ändern Sie das Kennwort für den globalen Administrator in einen Wert, den Sie sich merken können.

## <a name="optional--additional-server-and-forest"></a>Optional:  Zusätzlicher Server und/oder Gesamtstruktur
Der folgende optionale Abschnitt enthält die Schritte zum Erstellen eines zusätzlichen Servers und/oder einer Gesamtstruktur.  Diese Gesamtstruktur kann in einigen der komplexeren Tutorials (z.B. [Pilotcloudbereitstellung für eine vorhandene synchronisierte AD-Gesamtstruktur](tutorial-pilot-aadc-aadccp.md)) verwendet werden.

Wenn Sie nur einen zusätzlichen Server benötigen, können Sie den Vorgang nach dem Schritt **Erstellen des virtuellen Computers** beenden und den Server mit der vorhandenen Domäne verknüpfen, die zuvor erstellt wurde.  

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>Durchführen der Betriebssystembereitstellung
Um das Erstellen des virtuellen Computers durchzuführen, müssen Sie die Installation des Betriebssystems abschließen.

1. Doppelklicken Sie im Hyper-V-Manager auf den virtuellen Computer.
2. Klicken Sie auf die Schaltfläche „Starten“.
3. Sie werden zu Folgendem aufgefordert: „Drücken Sie eine beliebige Taste, um von CD oder DVD zu starten“. Fahren Sie fort, und tun Sie dies.
4. Wählen Sie auf dem Windows Server-Startbildschirm Ihre Sprache aus, und klicken Sie auf **Weiter**.
5. Klicken Sie auf **Jetzt installieren**.
6. Geben Sie Ihren Lizenzschlüssel ein, und klicken Sie auf **Weiter**.
7. Aktivieren Sie „Ich stimme den Lizenzbedingungen zu“, und klicken Sie auf **Weiter**.
8. Wählen Sie **Benutzerdefiniert:  Nur Windows installieren (Erweitert)** aus.
9. Klicken Sie auf **Weiter**.
10. Nach Abschluss der Installation starten Sie den virtuellen Computer neu, melden sich an und führen Windows-Updates aus, um sicherzustellen, dass die VM auf dem neuesten Stand ist.  Installieren Sie die neuesten Updates.

### <a name="install-active-directory-prerequisites"></a>Installieren der erforderlichen Active Directory-Komponenten
Nachdem Sie nun über einen virtuellen Computer verfügen, müssen Sie vor der Installation von Active Directory noch ein paar Vorbereitungen treffen.  Das heißt, dass Sie den virtuellen Computer umbenennen, eine statische IP-Adresse und DNS-Informationen festlegen und die Remoteserver-Verwaltungstools installieren müssen.   Gehen Sie folgendermaßen vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>Erstellen einer Windows Server AD-Umgebung
Nachdem nun der virtuelle Computer erstellt und umbenannt wurde und über eine statische IP-Adresse verfügt, können Sie als Nächstes die Active Directory Domain Services installieren und konfigurieren.  Gehen Sie folgendermaßen vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD environment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Erstellen eines Windows Server AD-Benutzers
Nachdem nun die Active Directory-Umgebung vorhanden ist, benötigen Sie ein Testkonto.  Dieses Konto wird in unserer lokalen Active Directory-Umgebung erstellt und dann mit Azure AD synchronisiert.  Gehen Sie folgendermaßen vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Zusammenfassung
Nun verfügen Sie über eine Umgebung, die für vorhandene Tutorials und zum Testen zusätzlicher Features der Cloudbereitstellung verwendet werden kann.

## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Was ist die Azure AD Connect-Cloudbereitstellung?](what-is-cloud-provisioning.md)