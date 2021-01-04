---
title: Anpassen von RDP-Eigenschaften mit PowerShell Windows Virtual Desktop (klassisch) – Azure
description: Vorgehensweise zum Anpassen von RDP-Eigenschaften für Windows Virtual Desktop (klassisch) mit PowerShell-Cmdlets.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5110e97e52939ea2115bb839768cc7ab96802961
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020707"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Anpassen der Remotedesktopprotokoll-Eigenschaften für einen Windows Virtual Desktop (klassisch)-Hostpool

>[!IMPORTANT]
>Dieser Inhalt gilt für den Windows Virtual Desktop-Dienst (klassisch), der keine Windows Virtual Desktop-Objekte in Azure Resource Manager unterstützt. Wenn Sie Windows Virtual Desktop-Objekte in Azure Resource Manager verwalten möchten, helfen Ihnen die Informationen in [diesem Artikel](../customize-rdp-properties.md) weiter.

Durch Anpassen von RDP-Eigenschaften (Remotedesktopprotokoll) eines Hostpools, z.B. Unterstützung mehrerer Monitore und Audioumleitung, können Sie für Benutzer eine optimale Bedienumgebung bereitstellen, die deren Anforderungen entspricht. RDP-Eigenschaften können Sie in Windows Virtual Desktop über den **-CustomRdpProperty**-Parameter im **Set-RdsHostPool**-Cmdlet anpassen.

Eine vollständige Liste der unterstützten Eigenschaften samt deren Standardwerten finden Sie unter [Unterstützte RDP-Dateieinstellungen](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

Zunächst müssen Sie das [Windows Virtual Desktop-PowerShell-Modul herunterladen und importieren](/powershell/windows-virtual-desktop/overview/), um es in Ihrer PowerShell-Sitzung verwenden zu können. Führen Sie anschließend das folgende Cmdlet aus, um sich bei Ihrem Konto anzumelden:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>RDP-Standardeigenschaften

Veröffentlichte RDP-Dateien enthalten standardmäßig die folgenden Eigenschaften:

|RDP-Eigenschaften | Desktops | RemoteApps |
|---|---| --- |
| Mehrfachmonitor-Modus | Aktiviert | – |
| Aktivierte Laufwerksumleitungen | Laufwerke, Zwischenablage, Drucker, COM-Anschlüsse, USB-Geräte und Smartcards| Laufwerke, Zwischenablage und Drucker |
| Remoteaudio-Modus | Lokale Wiedergabe | Lokale Wiedergabe |

Alle benutzerdefinierten Eigenschaften, die Sie für den Hostpool definieren, überschreiben diese Standardeinstellungen.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Hinzufügen oder Bearbeiten einer einzelnen benutzerdefinierten RDP-Eigenschaft

Wenn Sie eine einzelne benutzerdefinierte RDP-Eigenschaft hinzufügen oder bearbeiten möchten, führen Sie das folgende PowerShell-Cmdlet aus:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Screenshot des PowerShell-Cmdlets Get-RDSRemoteApp mit hervorgehobenem Namen und Anzeigenamen, um eine benutzerdefinierte RDP-Eigenschaft zu bearbeiten.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Hinzufügen oder Bearbeiten von mehreren benutzerdefinierten RDP-Eigenschaften

Wenn Sie mehrere benutzerdefinierte RDP-Eigenschaften hinzufügen oder bearbeiten möchten, führen Sie die folgenden PowerShell-Cmdlets aus, wobei Sie die benutzerdefinierten RDP-Eigenschaften als eine Zeichenfolge mit Semikolons als Trennzeichen angeben:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Screenshot des PowerShell-Cmdlets Set-RDSRemoteApp mit hervorgehobenem Namen und Anzeigenamen, um eine benutzerdefinierte RDP-Eigenschaft zu bearbeiten.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Zurücksetzen aller benutzerdefinierten RDP-Eigenschaften

Sie können eine einzelne benutzerdefinierte RDP-Eigenschaft auf deren Standardwert zurücksetzen, indem Sie den Anweisungen unter [Hinzufügen oder Bearbeiten einer einzelnen benutzerdefinierten RDP-Eigenschaft](#add-or-edit-a-single-custom-rdp-property) folgen, oder Sie können alle benutzerdefinierten RDP-Eigenschaften für einen Hostpool zurücksetzen, indem Sie das folgende PowerShell-Cmdlet ausführen:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Screenshot des PowerShell-Cmdlets Get-RDSRemoteApp mit hervorgehobenem Namen und Anzeigenamen.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die RDP-Eigenschaften eines bestimmten Hostpools angepasst haben, können Sie sich bei einem Windows Virtual Desktop-Client anmelden, um die Einstellungen als Teil einer Benutzersitzung zu testen. In den nächsten zwei Vorgehensweisen erfahren Sie, wie Sie mit dem ausgewählten Client eine Verbindung zu einer Sitzung herstellen:

- [Herstellen einer Verbindung mit dem Windows-Desktopclient](connect-windows-7-10-2019.md)
- [Herstellen einer Verbindung mit dem Webclient](connect-web-2019.md)