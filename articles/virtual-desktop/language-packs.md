---
title: Installieren von Language Packs auf Windows 10-VMs in Windows Virtual Desktop – Azure
description: Hier erfahren Sie, wie Sie Language Packs für Windows 10-VMs (mehrere Sitzungen) in Windows Virtual Desktop installieren.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ca4b5486b54de28497ea41765337bfa19f12e3d7
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573118"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Hinzufügen von Language Packs zu einem Image für Windows 10 (mehrere Sitzungen)

Windows Virtual Desktop ist ein Dienst, den Ihre Benutzer jederzeit und überall bereitstellen können. Daher ist es wichtig, dass die Benutzer die jeweilige Sprache, die in dem Image für Windows 10 Enterprise (mehrere Sitzungen) angezeigt wird, anpassen können.

Es gibt zwei Möglichkeiten, die Sprachanforderungen Ihrer Benutzer zu erfüllen:

- Erstellen Sie dedizierte Hostpools mit einem angepassten Image für jede Sprache.
- Fügen Sie Benutzer mit unterschiedlichen Sprach- und Lokalisierungsanforderungen demselben Hostpool hinzu, aber passen Sie deren Images an, um sicherzustellen, dass sie die gewünschte Sprache auswählen können.

Die letztere Methode ist sehr viel effizienter und kostengünstiger. Sie können aber entscheiden, welche Methode Ihren Anforderungen am besten entspricht. In diesem Artikel wird gezeigt, wie Sie die Sprachen für Ihre Images anpassen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen folgende Komponenten, um die Images für Windows 10 Enterprise (mehrere Sitzungen) so anzupassen, dass mehrere Sprachen hinzugefügt werden können:

- Einen virtuellen Azure-Computer (VM) mit Windows 10 Enterprise (mehrere Sitzungen), Version 1903 oder höher

- Language ISO, Feature on Demand (FOD) Disk 1 und Inbox Apps ISO für die vom Image verwendete Betriebssystemversion. Diese können Sie hier herunterladen: 
     
     - Language ISO:
        - [Windows 10, Version 1903 oder 1909, Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, Version 2004 oder 20H2, Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD Disk 1 ISO:
        - [Windows 10, Version 1903 oder 1909, FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, Version 2004 oder 20H2, FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Inbox Apps ISO:
        - [Windows 10, Version 1903 oder 1909, Inbox Apps ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, Version 2004, Inbox Apps ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, Version 20H2, Inbox Apps ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Wenn Sie die Images mithilfe von (LXP)-ISO-Dateien lokalisieren, müssen Sie auch die entsprechende LXP-ISO-Datei herunterladen, um die beste Sprachumgebung zu erhalten.
        - Wenn Sie Windows 10 verwenden, Version 1903 oder 1909:
          - [Windows 10, Version 1903 oder 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Wenn Sie Windows 10, Version 2004 oder 20h2 verwenden, beachten Sie die Informationen in [Hinzufügen von Sprachen in Windows 10: Bekannte Probleme](/windows-hardware/manufacture/desktop/language-packs-known-issue), um herauszufinden, welche der folgenden LXP-ISOs für Sie geeignet ist:
          - [Windows 10 Version 2004 oder 20H2 **9B** LXP-ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10 Version 2004 oder 20H2 **9C** LXP-ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, Version 2004 oder 20H2 **10C** LXP-ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)

- Eine Azure Files-Freigabe oder eine Dateifreigabe auf einer Windows-Dateiserver-VM

>[!NOTE]
>Auf die Dateifreigabe (Repository) muss von der Azure-VM aus zugegriffen werden können, die zum Erstellen des benutzerdefinierten Images verwendet werden soll.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Erstellen eines Inhaltsrepositorys für Sprachpakete und Features bei Bedarf (FODs)

So erstellen Sie das Inhaltsrepository für Sprachpakete und FODs und ein Repository für die Inbox Apps-Pakete:

1. Laden Sie auf einer Azure-VM die Windows 10-ISO-Datei (mehrere Sprachen), FODs und Inbox Apps für Images für Windows 10 Enterprise (mehrere Sitzungen), Version 1903/1909 und 2004, über die Links unter [Voraussetzungen](#prerequisites) herunter.

2. Öffnen Sie die ISO-Dateien auf der VM, und binden Sie sie ein.

3. Wechseln Sie zur Language Pack-ISO, kopieren Sie den Inhalt aus den Ordnern **LocalExperiencePacks** und **x64\\langpacks**, und fügen Sie dann den Inhalt in die Dateifreigabe ein.

4. Wechseln Sie zur **FOD-ISO-Datei**, kopieren Sie den gesamten Inhalt, und fügen Sie ihn in die Dateifreigabe ein.
5. Navigieren Sie zum Ordner **amd64fre** in der Inbox Apps-ISO-Datei, und kopieren Sie den Inhalt im Repository für die Inbox Apps, die Sie vorbereitetet haben.

     >[!NOTE]
     > Wenn Sie mit begrenztem Speicherplatz arbeiten, kopieren Sie nur die Dateien für die Sprachen, von denen Sie wissen, dass Ihre Benutzer sie benötigen. Sie können die Dateien anhand der Sprachcodes in den Dateinamen voneinander unterscheiden. Beispielsweise enthält die Datei für Französisch den Code „fr-FR“ im Namen. Eine vollständige Liste der Sprachcodes für alle verfügbaren Sprachen finden Sie unter [Verfügbare Sprachen für Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Einige Sprachen erfordern zusätzliche Schriftarten, die in Satellitenpaketen mit anderen Benennungskonventionen enthalten sind. Beispielsweise enthalten die Namen der Dateien für japanische Schrift die Zeichenfolge „Jpan“.
     >
     > [!div class="mx-imgBorder"]
     > ![Beispiel der Sprachpakete für Japanisch mit der Sprachkennung „Jpan“ im Dateinamen](media/language-pack-example.png)

6. Legen Sie die Berechtigungen für die Freigabe des Inhaltsrepositorys für Sprachen so fest, dass Sie über Lesezugriff von der VM aus verfügen, die Sie zum Erstellen des benutzerdefinierten Images verwenden möchten.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Manuelles Erstellen eines Images für Windows 10 Enterprise (mehrere Sitzungen)

Führen Sie die folgenden Schritte aus, um ein Image für Windows 10 Enterprise (mehrere Sitzungen) manuell zu erstellen:

1. Stellen Sie eine Azure-VM bereit. Dann navigieren Sie zum Azure-Katalog, und wählen Sie die von Ihnen verwendete aktuelle Version von Windows 10 Enterprise (mehrere Sitzungen) aus.
2. Nachdem Sie die VM bereitgestellt haben, stellen Sie mithilfe von RDP als lokaler Administrator eine Verbindung her.
3. Vergewissern Sie sich, dass die VM über alle aktuellen Windows-Updates verfügt. Laden Sie bei Bedarf die Updates herunter, und starten Sie die VM neu.
4. Stellen Sie eine Verbindung mit dem Repository für die Sprachpaket-, FOD- und Inbox Apps-Dateifreigabe her, und binden Sie das Repository als Laufwerk (z. B. Laufwerk E) ein.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Automatisches Erstellen eines Images für Windows 10 Enterprise (mehrere Sitzungen)

Wenn Sie Sprachen lieber über einen automatisierten Prozess installieren möchten, können Sie ein Skript in PowerShell einrichten. Mithilfe des folgenden Skriptbeispiels können Sie die Sprachpakete für Spanisch (Spanien), Französisch (Frankreich) und Chinesisch (VRC) sowie die Satellitenpakete für Windows 10 Enterprise (mehrere Sitzungen), Version 2004, installieren. Mit dem Skript werden das Benutzeroberflächen-Sprachpaket und alle erforderlichen Satellitenpakete in das Image integriert. Sie können Sie dieses Skript aber auch abändern, um andere Sprachen zu installieren. Stellen Sie lediglich sicher, dass Sie das Skript in einer PowerShell-Sitzung mit erhöhten Rechten ausführen, da es andernfalls nicht funktioniert.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

>[!IMPORTANT]
>Für die Windows 10 Enterprise-Versionen 1903 und 1909 ist die Paketdatei `Microsoft-Windows-Client-Language-Pack_x64_<language-code>.cab` nicht erforderlich.

Je nach Anzahl der Sprachen, die installiert werden müssen, kann die Ausführung des Skripts einige Zeit in Anspruch nehmen.

Sobald die Skriptausführung abgeschlossen ist, vergewissern Sie sich, dass die Sprachpakete ordnungsgemäß installiert wurden. Dazu navigieren Sie zu **Start** > **Einstellungen** > **Uhrzeit und Sprache** > **Sprache**. Wenn die Sprachdateien vorhanden sind, ist die Einrichtung abgeschlossen.

Nachdem Sie dem Windows-Image weitere Sprachen hinzugefügt haben, müssen auch die Inbox Apps aktualisiert werden, um die hinzugefügten Sprachen zu unterstützen. Dies kann erfolgen, indem die vorinstallierten Apps mit dem Inhalt aus der Inbox Apps-ISO-Datei aktualisiert werden. Soll diese Aktualisierung in einer nicht verbundenen Umgebung (kein Internetzugriff von der VM möglich) ausgeführt werden, können Sie das folgende PowerShell-Skriptbeispiel verwenden, um den Prozess zu automatisieren.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>Die in der ISO-Datei enthaltenen Inbox Apps liegen nicht in den neuesten Versionen der vorinstallierten Windows-Apps vor. Um die neueste Version aller Apps zu erhalten, müssen Sie die Apps über die Windows Store-App aktualisieren und eine manuelle Suche nach Updates durchführen, nachdem Sie die zusätzlichen Sprachen installiert haben.

Denken Sie anschließend daran, die Freigabe wieder zu trennen.

## <a name="finish-customizing-your-image"></a>Abschließen der Imageanpassung

Nachdem Sie die Language Packs installiert haben, können Sie weitere Software installieren, die Sie dem angepassten Image hinzufügen möchten.

Sobald Sie die Anpassung des Images abgeschlossen haben, müssen Sie das Tools für die Systemvorbereitung (sysprep) ausführen.

Zum Ausführen von Sysprep gehen Sie folgendermaßen vor:

1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus, um das Image zu generalisieren:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Halten Sie die VM an, und erfassen Sie sie in einem verwalteten Image. Folgen Sie dazu den Anweisungen unter [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](../virtual-machines/windows/capture-image-resource.md).

3. Sie können jetzt das angepasste Image zum Bereitstellen eines Windows Virtual Desktop-Hostpools verwenden. Informationen dazu, wie Sie einen Hostpool bereitstellen, finden Sie im [Tutorial: Erstellen eines Hostpools mit dem Azure-Portal](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Aktivieren von Sprachen in der App für Windows-Einstellungen

Zum Schluss müssen Sie, nachdem Sie den Hostpool bereitgestellt haben, die Sprache zur Sprachliste jedes Benutzers hinzufügen, damit er seine bevorzugte Sprache im Menü „Einstellungen“ auswählen kann.

Um sicherzustellen, dass die Benutzer die von Ihnen installierten Sprachen auswählen können, melden Sie sich als Benutzer an, und führen Sie dann das folgende PowerShell-Cmdlet aus, um die installierten Language Packs zum Menü „Sprachen“ hinzuzufügen. Sie können dieses Skript auch als automatisierte Aufgabe oder Anmeldeskript einrichten, die bzw. das aktiviert wird, wenn sich der Benutzer bei seiner Sitzung anmeldet.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Nachdem ein Benutzer seine Spracheinstellungen geändert hat, muss er sich von seiner Windows Virtual Desktop-Sitzung abmelden und sich erneut anmelden, damit die Änderungen wirksam werden. 

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie etwas über bekannte Probleme bei Language Packs erfahren möchten, finden Sie weitere Informationen unter [Hinzufügen von Sprachpaketen in Windows 10, Version 1803 und höher: Bekannte Probleme](/windows-hardware/manufacture/desktop/language-packs-known-issue).

Wenn Sie weitere Fragen zu Windows 10 Enterprise (mehrere Sitzungen) haben, sehen Sie sich die Informationen unter [Häufig gestellte Fragen](windows-10-multisession-faq.md) an.
