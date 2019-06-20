---
title: Installieren von Office für ein VHD-Masterimage – Azure
description: Erfahren Sie, wie Sie Office auf einem Windows Virtual Desktop-Masterimage (Vorschauversion) in Azure installieren und anpassen.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: cb9edbb508ddd993dcefbf69eb06b4f0d4156485
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742546"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installieren von Office für ein VHD-Masterimage

In diesem Artikel erfahren Sie, wie Sie Office 365 ProPlus, OneDrive und andere gängige Anwendungen auf einem VHD-Masterimage (virtuelle Festplatte) für den Upload in Azure installieren. Wenn Ihre Benutzer auf bestimmte Branchenanwendungen zugreifen müssen, wird empfohlen, die Installation durchzuführen, nachdem Sie die Anweisungen in diesem Artikel befolgt haben.

In diesem Artikel wird vorausgesetzt, dass Sie bereits eine VM (virtueller Computer) erstellt haben. Wenn Sie das noch nicht getan haben, führen Sie die Anweisungen unter [Vorbereiten und Anpassen eines VHD-Masterimages](set-up-customize-master-image.md#create-a-vm) aus.

In diesem Artikel wird außerdem davon ausgegangen, dass Sie über erhöhte Rechte für die VM verfügen, unabhängig davon, ob diese in Azure oder Hyper-V-Manager bereitgestellt wird. Wenn das nicht der Fall ist, führen Sie die Anweisungen unter [Erhöhen der Zugriffsrechte zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin) aus.

>[!NOTE]
>Diese Anleitung gilt für eine Konfiguration, die für Windows Virtual Desktop (Vorschauversion) spezifisch ist und für die vorhandenen Prozesse Ihrer Organisation genutzt werden kann.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installieren von Office im Modus „Aktivierung gemeinsam genutzter Computer“

Mit der Aktivierung gemeinsam genutzter Computer können Sie Office 365 ProPlus auf einem Computer in Ihrem Unternehmen bereitstellen, auf den mehrere Benutzer zugreifen. Weitere Informationen zur Aktivierung gemeinsam genutzter Computer finden Sie im [Überblick über die Aktivierung gemeinsam genutzter Computer für Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Verwenden Sie das [Office-Bereitstellungstool](https://www.microsoft.com/download/details.aspx?id=49117), um Office zu installieren. Windows 10 Enterprise (mehrere Sitzungen) unterstützt nur die folgenden Versionen von Office:
- Office 365 ProPlus
- Office 365-Business, das ein Microsoft 365 Business-Abonnement enthält

Für das Office-Bereitstellungstool wird eine XML-Konfigurationsdatei benötigt. Informationen zum Anpassen des folgenden Beispiels finden Sie unter [Konfigurationsoptionen für das Office-Bereitstellungstool](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Mit dieser von uns bereitgestellten XML-Beispieldatei für die Konfiguration wird Folgendes durchgeführt:

- Installieren von Office über den Insiderkanal und Bereitstellen von Updates über den Insiderkanal bei deren Ausführung
- Verwenden der X64-Architektur
- Deaktivieren automatischer Updates
- Installieren von Visio und Project
- Entfernen aller vorhandenen Installationen von Office und Migrieren der zugehörigen Einstellungen
- Aktivierung gemeinsam genutzter Computer

>[!NOTE]
>Das Schablonensuchfeature in Visio funktioniert während der Konfiguration der Vorschau in Windows Virtual Desktop nicht.

Folgendes wird dagegen von dieser XML-Beispielkonfigurationsdatei nicht erledigt:

- Installieren von Skype for Business
- Installieren von OneDrive im Modus „Pro Benutzer“ Weitere Informationen finden Sie unter [Installieren von OneDrive im Modus „Pro Computer“](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Die Aktivierung gemeinsam genutzter Computer kann über Gruppenrichtlinienobjekte (GPOs) oder Registrierungseinstellungen eingerichtet werden. Das Gruppenrichtlinienobjekt befindet sich unter **Computerkonfiguration\\Richtlinien\\Administrative Vorlagen\\Microsoft Office 2016 (Computer)\\Lizenzierungseinstellungen**.

Das Office-Bereitstellungstool enthält die Datei „setup.exe“. Führen Sie den folgenden Befehl an einer Befehlszeile aus, um Office zu installieren:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Beispieldatei „configuration.xml“

Mit dem folgenden XML-Beispielcode wird das Insider-Release installiert.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Das Office-Team empfiehlt die Verwendung der 64-Bit-Installation für den Parameter **OfficeClientEdition**.

Nach der Installation von Office können Sie das Standardverhalten von Office aktualisieren. Führen Sie die folgenden Befehle einzeln oder in einer Batchdatei aus, um das Verhalten zu aktualisieren.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Installieren von OneDrive im Modus „Pro Computer“

Normalerweise wird OneDrive im Modus „Pro Benutzer“ installiert. In dieser Umgebung sollte die Anwendung pro Computer installiert werden.

Hier ist beschrieben, wie Sie OneDrive im Modus „Pro Computer“ installieren:

1. Erstellen Sie zunächst einen Speicherort zum Bereitstellen des OneDrive-Installationsprogramms. Hierfür ist ein lokaler Datenträgerordner oder ein Speicherort vom Typ [\\\\unc](file://unc) geeignet.

2. Laden Sie die Datei „OneDriveSetup.exe“ mit diesem Link an Ihren bereitgestellten Speicherort herunter: <https://aka.ms/OneDriveWVD-Installer>.

3. Wenn Sie Office mit OneDrive installiert und **\<ExcludeApp ID="OneDrive" /\>** weggelassen haben, sollten Sie alle vorhandenen OneDrive-Installationen im Modus „Pro Benutzer“ entfernen, indem Sie an einer Eingabeaufforderung mit erhöhten Rechten den folgenden Befehl ausführen:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Führen Sie diesen Befehl an einer Eingabeaufforderung mit erhöhten Rechten aus, um den Registrierungswert **AllUsersInstall** festzulegen:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Führen Sie diesen Befehl aus, um OneDrive im Modus „Pro Computer“ zu installieren:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Führen Sie diesen Befehl aus, um OneDrive mit der Anmeldung für alle Benutzer zu starten:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Aktivieren Sie die Option **Silently configure user account** (Benutzerkonto im Hintergrund konfigurieren), indem Sie den folgenden Befehl ausführen.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Sie können bekannte Windows-Ordner umleiten und in OneDrive verschieben, indem Sie den folgenden Befehl ausführen.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Teams und Skype

Windows Virtual Desktop unterstützt Skype for Business und Teams nicht.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun Office auf dem Image installiert haben, können Sie damit fortfahren, Ihr VHD-Masterimage anzupassen. Informationen dazu finden Sie unter [Vorbereiten und Anpassen eines VHD-Masterimages](set-up-customize-master-image.md).
