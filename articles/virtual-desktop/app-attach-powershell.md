---
title: 'Windows Virtual Desktop: PowerShell-Vorschau zum Anfügen von Apps mit MSIX – Azure'
description: Einrichten des MSIX-Features zum Anfügen von Apps für Windows Virtual Desktop über PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1196982fedc7321805e36cceed27c90e43a6e705
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558337"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>Einrichten des MSIX-Features zum Anfügen von Apps (Vorschau) über PowerShell

> [!IMPORTANT]
> Das MSIX-Features zum Anfügen von Apps befindet sich zurzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Neben dem Azure-Portal können Sie das MSIX-Feature zum Anfügen von Apps (Vorschau) auch manuell mit PowerShell einrichten. In diesem Artikel erfahren Sie, wie Sie PowerShell verwenden, um das MSIX-Feature zum Anfügen von Apps einzurichten.

## <a name="requirements"></a>Anforderungen

>[!IMPORTANT]
>Bevor Sie beginnen, sollten Sie [dieses Formular](https://aka.ms/enablemsixappattach) ausfüllen und übermitteln, um das MSIX-Feature zum Anfügen von Apps in Ihrem Abonnement zu aktivieren. Wenn Ihre Anforderung noch nicht genehmigt wurde, funktioniert das MSIX-Feature zum Anfügen von Apps nicht. Die Genehmigung von Anforderungen kann an Werktagen bis zu 24 Stunden dauern. Sie erhalten eine E-Mail, wenn Ihre Anforderung akzeptiert und abgeschlossen wurde.

Folgendes benötigen Sie, um das MSIX-Feature zum Anfügen von Apps zu konfigurieren:

- Eine funktionierende Windows Virtual Desktop-Bereitstellung. Informationen zur Bereitstellung von Windows Virtual Desktop (klassisch) finden Sie unter [Erstellen eines Mandanten in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Informationen zum Bereitstellen von Windows Virtual Desktop mit Azure Resource Manager-Integration finden Sie unter [Erstellen eines Hostpools mit dem Azure-Portal](./create-host-pools-azure-marketplace.md).
- Ein Windows Virtual Desktop-Hostpool mit mindestens einem aktiven Sitzungshost.
- Dieser Hostpool muss sich in der Überprüfungsumgebung befinden.
- Eine Desktop-/Remote-App-Gruppe.
- Das MSIX-Pakettool.
- Eine MSIX-gepackte Anwendung, die in ein MSIX-Image erweitert wurde, das in eine Dateifreigabe hochgeladen wird.
- Eine Dateifreigabe in der Windows Virtual Desktop-Bereitstellung, in der das MSIX-Paket gespeichert wird.
- Die Dateifreigabe, in die Sie das MSIX-Image hochgeladen haben, muss auch für alle virtuellen Computer (VMs) im Hostpool zugänglich sein. Benutzer benötigen Leseberechtigungen, um auf das Image zugreifen zu können.
- Laden Sie PowerShell Core herunter, und installieren Sie die Anwendung.
- Laden Sie das Azure PowerShell-Modul in der öffentlichen Vorschau herunter, und erweitern Sie es in einen lokalen Ordner.
- Installieren Sie das Azure-Modul, indem Sie das folgende Cmdlet ausführen:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Melden Sie sich bei Azure an, und importieren Sie das Modul.

Wenn Sie alle Anforderungen erfüllt haben, öffnen Sie PowerShell Core in einer erweiterten Eingabeaufforderung, und führen Sie das folgende Cmdlet aus:

```powershell
Connect-AzAccount
```

Nachdem Sie es ausgeführt haben, authentifizieren Sie Ihr Konto unter Verwendung Ihrer Anmeldeinformationen. In diesem Fall werden Sie möglicherweise zur Eingabe einer Geräte-URL oder eines Tokens aufgefordert.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Importieren des Moduls „Az.WindowsVirtualDesktop“

Sie benötigen das Modul „Az.DesktopVirtualization“, um die Anweisungen in diesem Artikel zu befolgen.

>[!NOTE]
>Für die öffentliche Vorschau werden wir das Modul als separate ZIP-Dateien bereitstellen, die Sie manuell importieren müssen.

Bevor Sie beginnen, können Sie das folgende Cmdlet ausführen, um festzustellen, ob das Modul „Az.DesktopVirtualization“ bereits für Ihre Sitzung oder auf dem virtuellen Computer installiert ist:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Wenn Sie eine vorhandene Kopie des Moduls deinstallieren und neu beginnen möchten, führen Sie dieses Cmdlet aus:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Wenn das Modul auf Ihrer VM gesperrt ist, führen Sie dieses Cmdlet aus, um es zu entsperren:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Nachdem diese Bereinigung abgeschlossen ist, ist es an der Zeit, das Modul zu importieren.

1. Führen Sie das folgende Cmdlet aus, und drücken Sie dann die Taste **R**, wenn Sie aufgefordert werden, der Ausführung des benutzerdefinierten Codes zuzustimmen.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Nachdem Sie das Cmdlet für den Import ausgeführt haben, prüfen Sie, ob es die Cmdlets für MSIX enthält, indem Sie das folgende Cmdlet ausführen:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Wenn die Cmdlets vorhanden sind, sollte die Ausgabe wie folgt aussehen:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Wenn diese Ausgabe nicht angezeigt wird, schließen Sie alle PowerShell- und PowerShell Core-Sitzungen, und versuchen Sie es erneut.

## <a name="set-up-helper-variables"></a>Einrichten von Hilfsprogrammvariablen

Nachdem Sie das Modul importiert haben, müssen Sie die Hilfsprogrammvariablen einrichten. In den folgenden Beispielen wird gezeigt, wie Sie die einzelnen Aktionen ausführen.

So rufen Sie Ihre Abonnement-ID ab

```powershell
Get-AzContext -ListAvailable | fl
```

So wählen Sie den Kontext eines Azure-Mandanten und -Abonnements mit einem Namen aus

```powershell
$obj = Select-AzContext -Name "<Name>"
```

So legen Sie die Abonnementvariable fest

```powershell
$subId = $obj.Subscription.Id
```

So legen Sie den Namen des Arbeitsbereichs fest

```powershell
$ws = "<WorksSpaceName>"
```

So legen Sie den Hostpoolnamen fest

```powershell
$hp = "<HostPoolName>"
```

So richten Sie die Ressourcengruppe ein, in der die Sitzungshost-VMs konfiguriert sind

```powershell
$rg = "<ResourceGroupName>"
```

Und schließlich zur Bestätigung, dass Sie alle Variablen ordnungsgemäß festgelegt haben:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Hinzufügen eines MSIX-Pakets zu einem Hostpool

Nachdem Sie alles eingerichtet haben, ist es an der Zeit, das MSIX-Paket zu einem Hostpool hinzuzufügen. Dazu benötigen Sie zunächst den UNC-Pfad zum MSIX-Image.

Verwenden Sie den UNC-Pfad, und führen Sie dieses Cmdlet aus, um das MSIX-Image zu erweitern:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Führen Sie dieses Cmdlet aus, um das MSIX-Paket zum gewünschten Hostpool hinzuzufügen:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Wenn Sie fertig sind, bestätigen Sie, dass das Paket mit diesem Cmdlet erstellt wurde:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Entfernen eines MSIX-Pakets aus einem Hostpool

So entfernen Sie ein Paket aus einem Hostpool

Rufen Sie mit diesem Cmdlet eine Liste aller Pakete ab, die mit einem Hostpool verknüpft sind, und suchen Sie dann in der Ausgabe den Namen des Pakets, das Sie entfernen möchten:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Alternativ können Sie mit diesem Cmdlet auch ein bestimmtes Paket basierend auf seinem Anzeigenamen abrufen:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Führen Sie dieses Cmdlet aus, um das Paket zu entfernen:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Veröffentlichen von MSIX-Apps in einer App-Gruppe

Sie können die Anweisungen in diesem Abschnitt nur befolgen, wenn Sie die Anweisungen in den vorherigen Abschnitten abgeschlossen haben. Wenn Sie über einen Hostpool mit einem aktiven Sitzungshost und mindestens einer Desktop-App-Gruppe verfügen und dem Hostpool ein MSIX-Paket hinzugefügt haben, sind Sie bereit.

Um eine App aus dem MSIX-Paket in einer App-Gruppe zu veröffentlichen, müssen Sie ihren Namen finden und diesen Namen dann im Cmdlet für die Veröffentlichung verwenden.

So veröffentlichen Sie eine App

Führen Sie dieses Cmdlet aus, um alle verfügbaren App-Gruppen aufzulisten:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Wenn Sie den Namen der App-Gruppe gefunden haben, in der Sie Apps veröffentlichen möchten, verwenden Sie den Namen in diesem Cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Schließlich müssen Sie die App veröffentlichen.

- Um eine MSIX-Anwendung in einer Desktop-App-Gruppe zu veröffentlichen, führen Sie dieses Cmdlet aus:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Um die App in einer Remote-App-Gruppe zu veröffentlichen, führen Sie stattdessen dieses Cmdlet aus:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Wenn ein Benutzer sowohl einer Remote-App-Gruppe als auch einer Desktop-App-Gruppe im selben Hostpool zugewiesen ist, werden ihm bei der Verbindung mit seinem Remotedesktop MSIX-Apps aus beiden Gruppen angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Stellen Sie unserer Community unter [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) Ihre Fragen zu dieser Funktion.

Sie können Ihr Feedback in Bezug auf Windows Virtual Desktop auch im [Windows Virtual Desktop-Feedback-Hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) hochladen.

Folgende Artikel sind eventuell hilfreich:

- [Glossar des MSIX-Features zum Anfügen von Apps](app-attach-glossary.md)
- [Häufig gestellte Fragen zum MSIX-Feature zum Anfügen von Apps](app-attach-faq.md)
