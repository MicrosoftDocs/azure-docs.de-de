---
title: Erstellen eines Unterstützungspakets für ein Gerät der StorSimple 8000-Serie
description: Hier erfahren Sie, wie Sie ein Unterstützungspaket für Ihr Gerät der StorSimple 8000-Serie erstellen, entschlüsseln und bearbeiten.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017167"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Erstellen und Verwalten eines Unterstützungspakets für ein Gerät der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht

Ein StorSimple-Unterstützungspaket ist ein einfach zu verwendender Mechanismus, der alle relevanten Protokolle zur Unterstützung des Microsoft Supports bei der Behandlung von Problemen mit StorSimple-Geräten erfasst. Die erfassten Protokolle werden verschlüsselt und komprimiert.

Dieses Tutorial enthält detaillierte Anleitungen für die Erstellung und Verwaltung des Unterstützungspakets für Ihr Gerät der StorSimple 8000-Serie. Wenn Sie mit einem StorSimple Virtual Array arbeiten, wechseln Sie zu [Generieren eines Protokollpakets](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Unterstützungspaket erstellen

In einigen Fällen müssen Sie das Unterstützungspaket über Windows PowerShell für StorSimple manuell erstellen. Beispiel:

* Wenn Sie vertrauliche Informationen vor dem Freigeben für den Microsoft Support aus Ihren Protokolldateien entfernen müssen.
* Wenn das Hochladen des Pakets aufgrund von Konnektivitätsproblemen schwierig ist.

Sie können das manuell generierte Unterstützungspaket per E-Mail für den Microsoft Support freigeben. Führen Sie die folgenden Schritte aus, um in Windows PowerShell ein Unterstützungspaket für StorSimple zu erstellen.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>So erstellen Sie ein Unterstützungspaket in Windows PowerShell für StorSimple

1. Geben Sie den folgenden Befehl ein, um auf dem Remotecomputer, über den eine Verbindung mit dem StorSimple-Gerät hergestellt wird, eine Windows PowerShell-Sitzung als Administrator zu starten:
   
    `Start PowerShell`
2. Stellen Sie in der Windows PowerShell-Sitzung eine Verbindung mit der SSAdmin-Konsole des Geräts her:
   
   1. Geben Sie an der Eingabeaufforderung Folgendes ein:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Geben Sie im angezeigten Dialogfeld Ihr Geräteadministratorkennwort ein. Das Standardkennwort lautet _Password1_.
     
      ![PowerShell-Anmeldeinformationen-Dialogfeld](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Klicken Sie auf **OK**.
   4. Geben Sie an der Eingabeaufforderung Folgendes ein:
     
      `Enter-PSSession $MS`
3. Geben Sie in der geöffneten Sitzung den entsprechenden Befehl ein.
   
   * Geben Sie für kennwortgeschützte Netzwerkfreigaben Folgendes ein:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Sie werden aufgefordert, ein Kennwort und eine Verschlüsselungspassphrase (da das Unterstützungspaket verschlüsselt ist) einzugeben. Daraufhin wird ein Unterstützungspaket im Standardordner erstellt (Gerätename, an den Datum und Uhrzeit angehängt werden).
   * Bei Freigaben ohne Kennwortschutz wird der `-Credential` -Parameter nicht benötigt. Geben Sie Folgendes ein:
     
       `Export-HcsSupportPackage`
     
       Das Unterstützungspaket wird für beide Controller im Standardordner erstellt. Bei dem Paket handelt es sich um eine verschlüsselte, komprimierte Datei, die zur Problembehandlung an den Microsoft-Support gesendet werden kann. Weitere Informationen finden Sie unter [Kontaktaufnahme mit dem Microsoft-Support](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Die Parameter des Export-HcsSupportPackage-Cmdlets

Sie können die folgenden Parameter mit dem Export-HcsSupportPackage-Cmdlet verwenden.

| Parameter | Erforderlich/Optional | BESCHREIBUNG |
| --- | --- | --- |
| `-Path` |Erforderlich |Dient zum Angeben des Orts des freigegebenen Netzwerkordners für das Unterstützungspaket. |
| `-EncryptionPassphrase` |Erforderlich |Dient zum Angeben einer Passphrase für die Verschlüsselung des Unterstützungspakets. |
| `-Credential` |Optional |Dient zum Angeben von Anmeldeinformationen für den Zugriff auf den freigegebenen Netzwerkordner. |
| `-Force` |Optional |Dient zum Überspringen des Bestätigungsschritts für die Verschlüsselungspassphrase. |
| `-PackageTag` |Optional |Dient zum Angeben eines Verzeichnisses unter *Path* , in dem das Unterstützungspaket platziert werden soll. Standardmäßig wird Folgendes verwendet: [Gerätename]-[aktuelles Datum und aktuelle Uhrzeit:JJJJ-MM-TT-HH-mm-ss]. |
| `-Scope` |Optional |Geben Sie **Cluster** (Standardeinstellung) an, um ein Unterstützungspaket für beide Controller zu erstellen. Wenn Sie nur ein Paket für den aktuellen Controller erstellen möchten, geben Sie **Controller** an. |

## <a name="edit-a-support-package"></a>Bearbeiten eines Unterstützungspakets

Nachdem Sie ein Unterstützungspaket generiert haben, müssen Sie das Paket vielleicht bearbeiten, um vertrauliche Informationen zu entfernen. Dies kann Volumenamen, Geräte-IP-Adressen und Sicherungsnamen aus den Protokolldateien umfassen.

> [!IMPORTANT]
> Unterstützungspakete können nur bearbeitet werden, wenn sie mit Windows PowerShell für StorSimple generiert wurden. Pakete, die im Azure-Portal mit dem StorSimple-Geräte-Manager-Dienst erstellt wurden, können nicht bearbeitet werden.

Wenn Sie ein Unterstützungspaket vor dem Hochladen an die Microsoft Support-Website bearbeiten möchten, müssen Sie es zunächst entschlüsseln, die Dateien bearbeiten und anschließend wieder verschlüsseln. Führen Sie die folgenden Schritte aus:

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>So bearbeiten Sie ein Unterstützungspaket in Windows PowerShell für StorSimple

1. Generieren Sie ein Unterstützungspaket, wie bereits unter [So erstellen Sie ein Unterstützungspaket in Windows PowerShell für StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple)beschrieben.
2. [Laden Sie das Skript](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokal auf den Client herunter.
3. Importieren Sie das Windows PowerShell-Modul. Geben Sie den Pfad des lokalen Ordners an, in den Sie das Skript heruntergeladen haben. Geben Sie zum Importieren des Moduls Folgendes ein:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Bei allen Dateien es sich um komprimierte, verschlüsselte *AES* -Dateien. Geben Sie zum Dekomprimieren und Entschlüsseln der Dateien Folgendes ein:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Beachten Sie, dass nun für alle Dateien die tatsächlichen Dateierweiterungen angezeigt werden.
   
    ![Bearbeiten des Unterstützungspakets](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Geben Sie die bei der Paketerstellung verwendete Verschlüsselungspassphrase ein, wenn Sie dazu aufgefordert werden.
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. Navigieren Sie zum Ordner mit den Protokolldateien. Da die Protokolldateien nun dekomprimiert und entschlüsselt sind, werden sie mit ihren ursprünglichen Dateierweiterungen angezeigt. Entfernen Sie alle kundenspezifischen Informationen wie Volumenamen und Geräte-IP-Adressen aus den Dateien, und speichern Sie sie.
7. Schließen Sie die Dateien, damit sie mit Gzip komprimiert und mit AES-256 verschlüsselt werden. Dies macht die Übertragung des Unterstützungspakets über ein Netzwerk sicherer und schneller. Geben Sie zum Komprimieren und Verschlüsseln der Dateien Folgendes ein:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Bearbeiten des Unterstützungspakets 2](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Geben Sie eine Verschlüsselungspassphrase für das geänderte Unterstützungspaket ein, wenn Sie dazu aufgefordert werden.
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. Notieren Sie sich die Passphrase, um Sie auf Anfrage dem Support von Microsoft mitteilen zu können.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Beispiel: Bearbeiten von Dateien in einem Unterstützungspaket auf einer kennwortgeschützten Freigabe

Das folgende Beispiel veranschaulicht das Entschlüsseln, Bearbeiten und erneute Verschlüsseln eines Unterstützungspakets:

```powershell
PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Open-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Close-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32>
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [im Unterstützungspaket gesammelten Informationen](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs).
* Informieren Sie sich darüber, wie Sie [mithilfe von Unterstützungspaketen und Geräteprotokollen Probleme bei der Gerätebereitstellung behandeln](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts verwalten](storsimple-8000-manager-service-administration.md).

