---
title: Vorbereiten und Anpassen eines VHD-Masterimages – Azure
description: Vorbereiten, Anpassen und Hochladen eines Windows Virtual Desktop-Masterimage in Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9e4cb4fe8a701600290d24c055e2be5187a711c5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023393"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Vorbereiten und Anpassen eines VHD-Masterimages

In diesem Artikel wird beschrieben, wie Sie ein VHD-Masterimage (Virtual Hard Disk) für den Upload in Azure vorbereiten und virtuelle Computer (VMs) erstellen und darauf Software installieren. Diese Anweisungen beziehen sich auf eine spezielle Konfiguration für Windows Virtual Desktop, die mit den in Ihrer Organisation vorhandenen Prozessen verwendet werden kann.

## <a name="create-a-vm"></a>Erstellen einer VM

Windows 10 Enterprise (mehrere Sitzungen) ist im Azure-Imagekatalog verfügbar. Es gibt zwei Möglichkeiten, dieses Image anzupassen.

Die erste Möglichkeit ist die Bereitstellung eines virtuellen Computers (VM) in Azure, indem Sie die Anleitung unter [Erstellen eines virtuellen Computers aus einem verwalteten Image](../virtual-machines/windows/create-vm-generalized-managed.md) befolgen und dann mit [Software: Vorbereitung und Installation](set-up-customize-master-image.md#software-preparation-and-installation) fortfahren.

Die zweite Möglichkeit ist die lokale Erstellung des Images, indem das Image heruntergeladen wird und eine Hyper-V-VM bereitgestellt und dann an Ihre Anforderungen angepasst wird. Dies wird im folgenden Abschnitt beschrieben.

### <a name="local-image-creation"></a>Lokales Erstellen des Images

Nachdem Sie das Image an einen lokalen Speicherort heruntergeladen haben, öffnen Sie **Hyper-V-Manager**, um eine VM mit der kopierten VHD zu erstellen. Die folgenden Anleitungen gelten für eine einfache Version. Eine ausführlichere Anleitung finden Sie unter [Erstellen eines virtuellen Computers in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Erstellen Sie wie folgt eine VM mit der kopierten VHD:

1. Öffnen Sie den **Assistenten „Neuer virtueller Computer“** .

2. Wählen Sie auf der Seite „Generation angeben“ die Option **Generation 1** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Seite „Generation angeben“. Die Option „Generation 1“ ist ausgewählt.](media/a41174fd41302a181e46385e1e701975.png)

3. Deaktivieren Sie unter „Prüfpunkttyp“ die Prüfpunkte, indem Sie das Kontrollkästchen deaktivieren.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Abschnitt „Prüfpunkttyp“ auf der Seite „Prüfpunkte“](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Sie können auch das folgende Cmdlet in PowerShell ausführen, um Prüfpunkte zu deaktivieren.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Datenträger mit fester Größe

Wenn Sie eine VM aus einer vorhandenen VHD erstellen, wird standardmäßig ein dynamischer Datenträger erstellt. Sie können stattdessen einen Datenträger mit fester Größe erstellen, indem Sie wie in der folgenden Abbildung gezeigt die Option **Datenträger bearbeiten...** wählen. Eine ausführlichere Anleitung finden Sie unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!div class="mx-imgBorder"]
> ![Screenshot: Option „Datenträger bearbeiten“](media/35772414b5a0f81f06f54065561d1414.png)

Sie können auch das folgende PowerShell-Cmdlet ausführen, um den Datenträger in einen Datenträger mit fester Größe zu ändern.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Software: Vorbereitung und Installation

In diesem Abschnitt erfahren Sie, wie Sie FSLogix und Windows Defender vorbereiten und installieren, und Sie lernen einige grundlegende Konfigurationsoptionen für Apps und die Registrierung Ihres Images kennen.

Wenn Sie Microsoft 365-Apps for Enterprise und OneDrive auf Ihrer VM installieren, navigieren Sie zu [Installieren von Office für ein VHD-Masterimage](install-office-on-wvd-master-image.md) und befolgen die Anweisungen, um die Apps zu installieren. Wenn Sie fertig sind, kehren Sie zu diesem Artikel zurück.

Wenn Ihre Benutzer auf bestimmte Branchenanwendungen zugreifen müssen, empfehlen wir Ihnen, die Installation durchzuführen, nachdem Sie die Anleitung in diesem Abschnitt befolgt haben.

### <a name="set-up-user-profile-container-fslogix"></a>Einrichten des Benutzerprofilcontainers (FSLogix)

Befolgen Sie die Anleitung unter [Erstellen eines Profilcontainers für einen Hostpool unter Verwendung einer Dateifreigabe](create-host-pools-user-profile.md#configure-the-fslogix-profile-container), um den FSLogix-Container als Teil des Images einzubeziehen. Sie können die Funktionalität des FSLogix-Containers testen, indem Sie [diese Schnellstartanleitung](/fslogix/configure-cloud-cache-tutorial/) verwenden.

### <a name="configure-windows-defender"></a>Konfigurieren von Windows Defender

Wenn Windows Defender auf dem virtuellen Computer konfiguriert ist, sollten Sie sicherstellen, dass beim Anfügen von VHD- und VHDX-Dateien nicht der gesamte Inhalt gescannt wird.

Bei dieser Konfiguration wird nur das Scannen von VHD- und VHDX-Dateien während des Anfügens entfernt, aber es ergibt sich keine Auswirkung auf die Echtzeitüberprüfung.

Eine ausführlichere Anleitung zur Konfiguration von Windows Defender unter Windows Server finden Sie unter [Konfigurieren von Windows Defender Antivirus-Ausschlüssen auf Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Weitere Informationen zum Konfigurieren von Windows Defender, um bestimmte Dateien vom Scannen auszuschließen, finden Sie unter [Konfigurieren und Validieren von Ausschlüssen basierend auf Dateierweiterung und Speicherort des Ordners](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Deaktivieren automatischer Updates

So deaktivieren Sie „Automatische Updates“ über lokale Gruppenrichtlinien:

1. Öffnen Sie **Editor für lokale Gruppenrichtlinien\\Administrative Vorlagen\\Windows-Komponenten\\Windows Update**.
2. Klicken Sie mit der rechten Maustaste auf **Automatische Updates konfigurieren**, und legen Sie die Option auf **Deaktiviert** fest.

Sie können auch den folgenden Befehl an einer Eingabeaufforderung ausführen, um automatische Updates zu deaktivieren.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Festlegen des Startlayouts für Windows 10-PCs (optional)

Führen Sie diesen Befehl aus, um ein Startlayout für Windows 10-PCs anzugeben.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Einrichten der Zeitzonenumleitung

Die Zeitzonenumleitung kann auf Gruppenrichtlinienebene erzwungen werden, da alle VMs in einem Hostpool Teil derselben Sicherheitsgruppe sind.

Führen Sie die Umleitung für Zeitzonen wie folgt durch:

1. Öffnen Sie auf dem Active Directory-Server die **Gruppenrichtlinien-Verwaltungskonsole**.
2. Erweitern Sie Ihre Domäne und die Gruppenrichtlinienobjekte.
3. Klicken Sie mit der rechten Maustaste auf das **Gruppenrichtlinienobjekt**, das Sie für die Gruppenrichtlinieneinstellungen erstellt haben, und wählen Sie **Bearbeiten**.
4. Navigieren Sie im **Gruppenrichtlinienverwaltungs-Editor** zu **Computerkonfiguration** > **Richtlinien** > **Administrative Vorlagen** > **Windows-Komponenten** > **Remotedesktopdienste** > **Remotedesktop-Sitzungshost** > **Geräte- und Ressourcenumleitung**.
5. Aktivieren Sie die Einstellung **Zeitzonenumleitung zulassen**.

Sie können diesen Befehl auch auf dem Masterimage ausführen, um Zeitzonen umzuleiten:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Deaktivieren der Speicheroptimierung

Für Windows Virtual Desktop-Sitzungshosts, für die Windows 10 Enterprise oder Windows 10 Enterprise (mehrere Sitzungen) verwendet wird, empfehlen wir die Deaktivierung der Speicheroptimierung. Sie können die Speicheroptimierung im Menü „Einstellungen“ unter **Speicher** deaktivieren, wie im folgenden Screenshot gezeigt:

> [!div class="mx-imgBorder"]
> ![Screenshot: Menü „Speicher“ unter „Einstellungen“. Die Option „Speicheroptimierung“ ist deaktiviert.](media/storagesense.png)

Sie können die Einstellung auch über die Registrierung ändern, indem Sie den folgenden Befehl ausführen:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Einbinden der zusätzlichen Sprachunterstützung

In diesem Artikel wird nicht beschrieben, wie Sie die Unterstützung von Sprachen und Regionen konfigurieren. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Add languages to Windows images](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/) (Hinzufügen von Sprachen zu Windows-Images)
- [Features on demand](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/) (On-Demand-Features)
- [Language and region Features on Demand (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/) (Sprachen und Regionen: On-Demand-Features)

### <a name="other-applications-and-registry-configuration"></a>Konfiguration anderer Anwendungen und der Registrierung

In diesem Abschnitt wird die Konfiguration der Anwendungen und Betriebssysteme behandelt. Alle Konfigurationen in diesem Abschnitt erfolgen über Registrierungseinträge mit Befehlszeilen- und Registrierungstools.

>[!NOTE]
>Sie können die bewährten Methoden für die Konfiguration implementieren, indem Sie entweder Gruppenrichtlinienobjekte (GPOs) oder Registrierungsimporte verwenden. Der Administrator kann je nach den Anforderungen der Organisation eine der beiden Optionen wählen.

Führen Sie diesen Befehl aus, um unter Windows 10 Enterprise (mehrere Sitzungen) über den Feedback-Hub Telemetriedaten zu erfassen:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Führen Sie den folgenden Befehl aus, um Watson-Abstürze zu beheben:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Geben Sie die folgenden Befehle in den Registrierungs-Editor ein, um Probleme mit der Unterstützung der 5k-Auflösung zu beheben. Sie müssen die Befehle ausführen, bevor Sie den parallelen Stapel aktivieren können.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Vorbereiten des Images für den Upload in Azure

Nachdem Sie die Konfiguration abgeschlossen und alle Anwendungen installiert haben, können Sie die Anleitung unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) befolgen, um das Image vorzubereiten.

Stellen Sie nach dem Vorbereiten des Images für den Upload sicher, dass die VM im Zustand „Aus“ oder „Zuordnung aufgehoben“ verbleibt.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Hochladen eines Masterimages in ein Speicherkonto in Azure

Dieser Abschnitt gilt nur, wenn das Masterimage lokal erstellt wurde.

In der folgenden Anleitung wird beschrieben, wie Sie Ihr Masterimage in ein Azure-Speicherkonto hochladen. Falls Sie nicht bereits über ein Azure-Speicherkonto verfügen, können Sie die Anleitung in [diesem Artikel](/azure/developer/javascript/tutorial-vscode-static-website-node-03) verwenden, um ein Konto zu erstellen.

1. Konvertieren Sie das VM-Image (VHD) in „Fixed“ (Fest), falls dies noch nicht geschehen ist. Wenn Sie das Image nicht in „Fixed“ (Fest) konvertieren, ist die Imageerstellung nicht erfolgreich.

2. Laden Sie die VHD in einen Blobcontainer in Ihrem Speicherkonto hoch. Sie können den Upload mit dem [Storage-Explorer-Tool](https://azure.microsoft.com/features/storage-explorer/) schnell durchführen. Weitere Informationen zum Storage-Explorer-Tool finden Sie in [diesem Artikel](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows).

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Fenster „Suchen“ im Tool Microsoft Azure Storage-Explorer Das Kontrollkästchen „Upload .vhd or vhdx files as page blobs (recommended)“ (VHD-/VHDX-Dateien als Seitenblobs hochladen (empfohlen))“ ist aktiviert.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Navigieren Sie anschließend in Ihrem Browser zum Azure-Portal, und suchen Sie nach „Images“. Ihre Suche sollte Sie zur Seite **Image erstellen** führen, wie im folgenden Screenshot dargestellt:

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Seite „Image erstellen“ im Azure-Portal mit Beispielwerten für das Image](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Nachdem Sie das Image erstellt haben, sollte eine Benachrichtigung wie im folgenden Screenshot angezeigt werden:

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Benachrichtigung „Image erfolgreich erstellt“](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun über ein Image verfügen, können Sie Hostpools erstellen oder aktualisieren. Weitere Informationen zum Erstellen und Aktualisieren von Hostpools finden Sie in den folgenden Artikeln:

- [Erstellen eines Hostpools mit einer Azure Resource Manager-Vorlage](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Tutorial: Erstellen eines Hostpools mit Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell.md)
- [Erstellen eines Profilcontainers für einen Hostpool unter Verwendung einer Dateifreigabe](create-host-pools-user-profile.md)
- [Konfigurieren der Lastenausgleichsmethode für Windows Virtual Desktop](configure-host-pool-load-balancing.md)