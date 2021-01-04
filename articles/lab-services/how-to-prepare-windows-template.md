---
title: Leitfaden zum Einrichten eines Windows-Vorlagencomputers | Microsoft-Dokumentation
description: Allgemeine Schritte zum Vorbereiten eines Windows-Vorlagencomputers in Lab Services.  Diese Schritte umfassen das Festlegen des Windows Update-Zeitplans, das Installieren von OneDrive und das Installieren von Office.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647901"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Leitfaden zum Einrichten eines Windows-Vorlagencomputers in Azure Lab Services

Wenn Sie einen Windows 10-Vorlagencomputer für Azure Lab Services einrichten, finden Sie hier einige bewährte Methoden und Tipps. Die unten aufgeführten Konfigurationsschritte sind alle optional.  Diese Vorbereitungsschritte können jedoch dazu beitragen, dass Ihre Kursteilnehmer produktiver sind. Sie können Unterrichtsunterbrechungen minimieren und sicherstellen, dass sie die neuesten Technologien verwenden.

>[!IMPORTANT]
>Dieser Artikel enthält PowerShell-Codeausschnitte, um den Prozess der Änderung der Computervorlage zu optimieren.  Alle gezeigten PowerShell-Skripts sollten in Windows PowerShell mit Administratorrechten ausgeführt werden. In Windows 10 besteht eine schnelle Möglichkeit darin, mit der rechten Maustaste auf das Startmenü zu klicken und dann „Windows PowerShell (Administrator)“ auszuwählen.

## <a name="install-and-configure-onedrive"></a>Installieren und Konfigurieren von OneDrive

Um zu verhindern, dass Daten von Kursteilnehmern verloren gehen, wenn ein virtueller Computer zurückgesetzt wird, wird empfohlen, dass Kursteilnehmer ihre Daten in der Cloud sichern.  Microsoft OneDrive kann Kursteilnehmer beim Schutz ihrer Daten unterstützen.  

### <a name="install-onedrive"></a>Installieren von OneDrive

Wenn Sie OneDrive manuell herunterladen und installieren möchten, finden Sie weitere Informationen auf den Downloadseiten für [OneDrive](https://onedrive.live.com/about/download/) oder [OneDrive for Business](https://onedrive.live.com/about/business/).

Sie können auch das folgende PowerShell-Skript verwenden.  Die neueste Version von OneDrive wird mit diesem Skript automatisch heruntergeladen und installiert.  Nachdem der OneDrive-Client installiert wurde, führen Sie das Installationsprogramm aus.  In unserem Beispiel verwenden wir den Schalter `/allUsers`, um OneDrive für alle Benutzer auf dem Computer zu installieren. Außerdem verwenden wir den Schalter `/silent`, um OneDrive unbeaufsichtigt zu installieren.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive-Anpassungen

Es gibt viele [Anpassungen, die für OneDrive ](/onedrive/use-group-policy) vorgenommen werden können. Im Folgenden werden einige der gängigeren Anpassungen behandelt.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Automatisches Verschieben von bekannten Windows-Ordnern in OneDrive

Ordner wie Dokumente, Downloads und Bilder werden häufig zum Speichern von Kursteilnehmerdateien verwendet. Um sicherzustellen, dass diese Ordner in OneDrive gesichert werden, empfiehlt es sich, diese Ordner in OneDrive zu verschieben.

Wenn Sie mit einem Computer arbeiten, der Active Directory nicht verwendet, können Benutzer diese Ordner manuell in OneDrive verschieben, nachdem sie sich bei OneDrive authentifiziert haben.

1. Öffnen Sie den Datei-Explorer.
2. Klicken Sie mit der rechten Maustaste auf den Ordner „Dokumente“, „Downloads“ oder „Bilder“.
3. Navigieren Sie zu „Eigenschaften“ > „Speicherort“.  Verschieben Sie den Ordner in einen neuen Ordner im OneDrive-Verzeichnis.

Wenn Ihr virtueller Computer mit Active Directory verbunden ist, können Sie den Vorlagencomputer so festlegen, dass die Kursteilnehmer automatisch aufgefordert werden, bekannte Ordner in OneDrive zu verschieben.  

Sie müssen zunächst Ihre Organisations-ID abrufen.  Weitere Anweisungen finden Sie unter [Abrufen Ihrer Microsoft 365-Organisations-ID](/onedrive/find-your-office-365-tenant-id).  Sie können die Organisations-ID auch mit den folgenden PowerShell-Befehlen abrufen.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Wenn Sie über Ihre Organisations-ID verfügen, legen Sie für OneDrive über den folgenden PowerShell-Befehl fest, dass eine Aufforderung zum Verschieben bekannter Ordner in OneDrive angezeigt wird.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Verwenden von „OneDrive-Dateien bei Bedarf“

Kursteilnehmer verfügen möglicherweise über viele Dateien in ihren OneDrive-Konten. Um Speicherplatz auf dem Computer zu sparen und die Downloadzeit zu verkürzen, empfiehlt es sich, alle Dateien, die im OneDrive-Konto des Kursteilnehmers gespeichert sind, als „Dateien bei Bedarf“ zu kennzeichnen.  OneDrive-Dateien bei Bedarf werden nur heruntergeladen, wenn ein Benutzer auf die Datei zugreift.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Automatisches Anmelden von Benutzern bei OneDrive

OneDrive kann so eingerichtet werden, dass die Anmeldung automatisch mit Windows-Anmeldeinformationen des angemeldeten Benutzers erfolgt.  Die automatische Anmeldung ist nützlich für Kurse, bei denen sich Kursteilnehmer mit ihren Schul- oder Unianmeldeinformationen anmelden.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Das Lernprogramm deaktivieren, das am Ende der OneDrive-Einrichtung angezeigt wird

Mit dieser Einstellung können Sie verhindern, dass das Tutorial am Ende des OneDrive-Setups in einem Webbrowser gestartet wird.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Maximale Größe einer Datei festlegen, die automatisch heruntergeladen wird

Diese Einstellung wird in Verbindung mit der unbeaufsichtigten Anmeldung von Benutzern beim OneDrive-Synchronisierungsclient mit ihren Windows-Anmeldeinformationen auf Geräten verwendet, auf denen „OneDrive-Dateien bei Bedarf“ nicht aktiviert ist. Jeder Benutzer, der über einen OneDrive-Speicher verfügt, der größer als der angegebene Schwellenwert (in MB) ist, wird aufgefordert, die Ordner auszuwählen, die synchronisiert werden sollen, bevor der OneDrive-Synchronisierungsclient („OneDrive.exe“) die Dateien herunterlädt.  Im folgenden Beispiel ist „1111-2222-3333-4444“ die Organisations-ID, und mit „0005000“ wird ein Schwellenwert von 5 GB festgelegt.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Installieren und Konfigurieren von Microsoft 365

### <a name="install-microsoft-365"></a>Installieren von Microsoft 365

Wenn Ihr Vorlagencomputer Office benötigt, empfehlen wir die Installation von Office über das [Office-Bereitstellungstool](https://www.microsoft.com/download/details.aspx?id=49117). Sie müssen im [Microsoft 365 Apps Admin Center](https://config.office.com/) eine wiederverwendbare Konfigurationsdatei erstellen, um die Architektur, die benötigten Office-Features und die Updatehäufigkeit auszuwählen.

1. Navigieren Sie zum [Microsoft 365 Apps Admin Center](https://config.office.com/), und laden Sie Ihre eigene Konfigurationsdatei herunter.
2. Laden Sie das [Office-Bereitstellungstool](https://www.microsoft.com/download/details.aspx?id=49117) herunter.  Die heruntergeladene Datei trägt den Namen `setup.exe`.
3. Führen Sie `setup.exe /download configuration.xml` aus, um Office-Komponenten herunterzuladen.
4. Führen Sie `setup.exe /configure configuration.xml` aus, um Office-Komponenten zu installieren.

### <a name="change-the-microsoft-365-update-channel"></a>Ändern des Microsoft 365-Updatekanals

Mit dem Office-Konfigurationstool können Sie festlegen, wie häufig Office Updates erhält. Wenn Sie ändern müssen, wie häufig Office nach der Installation Updates erhält, können Sie die URL des Updatekanals ändern. Die URL-Adressen für den Updatekanal finden Sie unter [Ändern des Updatekanals für Microsoft 365 Apps für Geräte in Ihrer Organisation](/deployoffice/change-update-channels). Im folgenden Beispiel wird gezeigt, wie Sie Microsoft 365 für die Verwendung des monatlichen Updatekanals einrichten.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Installieren und Konfigurieren von Updates

### <a name="install-the-latest-windows-updates"></a>Installieren der neuesten Windows-Updates

Es wird empfohlen, dass Sie die neuesten Microsoft-Updates aus Sicherheitsgründen auf dem Vorlagencomputer installieren, bevor Sie die Vorlagen-VM veröffentlichen.  Außerdem kann potenziell vermieden werden, dass Kursteilnehmer in ihrer Arbeit unterbrochen werden, wenn Updates zu unerwarteten Zeiten ausgeführt werden.

1. Öffnen Sie im Startmenü die Option **Einstellungen**.
2. Klicken Sie auf „**Update** und Sicherheit“.
3. Klicken Sie auf **Nach Updates suchen**.
4. Updates werden heruntergeladen und installiert.

Sie können auch PowerShell verwenden, um den Vorlagencomputer zu aktualisieren.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Einige Updates erfordern möglicherweise, dass der Computer neu gestartet wird.  Sie werden aufgefordert, einen Neustart durchzuführen, wenn dieser erforderlich ist.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Installieren der neuesten Updates für Microsoft Store-Apps

Es wird empfohlen, alle Microsoft Store-Apps auf die neuesten Versionen zu aktualisieren.  Hier finden Sie Anweisungen zum manuellen Aktualisieren von Anwendungen aus dem Microsoft Store.  

1. Starten Sie die Anwendung **Microsoft Store**.
2. Klicken Sie in der oberen Ecke der Anwendung auf die Auslassungspunkte (...) neben Ihrem Benutzerfoto.
3. Wählen Sie aus dem Dropdownmenü **Herunterladen** und „Updates“ aus.
4. Klicken Sie auf die Schaltfläche **Update abrufen**.

Sie können auch PowerShell verwenden, um Microsoft Store-Anwendungen zu aktualisieren, die bereits installiert sind.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Beenden automatischer Windows-Updates

Nachdem Sie Windows auf die neueste Version aktualisiert haben, können Sie ggf. die Beendigung von Windows-Updates in Erwägung ziehen.  Automatische Updates können möglicherweise geplante Kurszeiten beeinträchtigen.  Wenn Ihr Kurs länger dauert, sollten Sie die Kursteilnehmer bitten, manuell nach Updates zu suchen oder automatische Updates für eine Zeit außerhalb der geplanten Unterrichtszeiten festzulegen.  Weitere Informationen zu Anpassungsoptionen für Windows Update finden Sie unter [Verwalten zusätzlicher Windows Update-Einstellungen](/windows/deployment/update/waas-wu-settings).

Automatische Windows-Updates können mithilfe des folgenden PowerShell-Skripts beendet werden.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Installieren von Language Packs für Fremdsprachen

Wenn Sie zusätzliche Sprachen auf dem virtuellen Computer installieren müssen, können Sie diese über den Microsoft Store hinzufügen.

1. Starten Sie den Microsoft Store.
2. Suchen Sie nach „Language Pack“ (Sprachpaket).
3. Wählen Sie die zu installierende Sprache aus.

Wenn Sie bereits bei der Vorlagen-VM angemeldet sind, verwenden Sie die Verknüpfung „Sprachpaket installieren“ (`ms-settings:regionlanguage?activationSource=SMC-IA-4027670`), um direkt zur entsprechenden Einstellungsseite zu gelangen.

## <a name="remove-unneeded-built-in-apps"></a>Entfernen nicht benötigter integrierter Apps

Windows 10 verfügt über viele integrierte Anwendungen, die möglicherweise nicht für Ihren jeweiligen Kurs benötigt werden. Um das Computerimage für Kursteilnehmer zu vereinfachen, können Sie einige Anwendungen auf dem Vorlagencomputer deinstallieren.  Verwenden Sie das PowerShell-Cmdlet `Get-AppxPackage`, um eine Liste der installierten Anwendungen anzuzeigen.  Das folgende Beispiel zeigt alle installierten Anwendungen, die entfernt werden können.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Um eine Anwendung zu entfernen, verwenden Sie das Cmdlet Remove-Appx.  Im folgenden Beispiel wird gezeigt, wie alle Xbox-bezogenen Elemente entfernt werden.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Installieren allgemeiner unterrichtsbezogener Anwendungen

Installieren Sie andere Apps, die häufig für den Unterricht verwendet werden, über die Windows Store-App. Zu den Vorschlägen zählen Anwendungen wie die [Microsoft Whiteboard-App](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4) und [Minecraft Education Edition](https://education.minecraft.net/). Diese Anwendungen müssen manuell über den Windows Store oder über die entsprechenden Websites auf der Vorlagen-VM installiert werden.

## <a name="conclusion"></a>Zusammenfassung

In diesem Artikel wurden optionale Schritte zum Vorbereiten Ihrer Windows-Vorlagen-VM für einen effektive Kurs gezeigt.  Die Schritte umfassen das Installieren von OneDrive und Microsoft 365 sowie der Updates für Windows und der für Microsoft Store-Apps.  Außerdem wurde erläutert, wie Updates gemäß einem Zeitplan festgelegt werden, der für Ihren Kurs am besten geeignet ist.  

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Verwaltung von Kosten finden Sie im Artikel zum Steuern des Verhaltens beim Herunterfahren von Windows: [Leitfaden zum Steuern des Verhaltens beim Herunterfahren von Windows](how-to-windows-shutdown.md)