---
title: Konfigurieren von Geräteumleitungen – Azure
description: Konfigurieren Sie die Geräteumleitung für Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b60a6e53e8fecd71885204690231776ff69fc08f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018374"
---
# <a name="configure-device-redirections"></a>Konfigurieren von Geräteumleitungen

Durch Konfigurieren von Geräteumleitungen für Ihre Windows Virtual Desktop-Umgebung können Sie Drucker, USB-Geräte, Mikrofone und andere Peripheriegeräte in der Remotesitzung verwenden. Bei einigen Geräteumleitungen müssen RDP-Eigenschaften (Remotedesktopprotokoll) und Gruppenrichtlinieneinstellungen geändert werden.

## <a name="supported-device-redirections"></a>Unterstützte Geräteumleitungen

Unterschiedliche Clients unterstützen jeweils andere Geräteumleitungen. Eine vollständige Liste der von den einzelnen Clients unterstützten Geräteumleitungen finden Sie unter [Vergleichen der Clients](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare).

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Anpassen von RDP-Eigenschaften für einen Hostpool

Weitere Informationen zum Anpassen von RDP-Eigenschaften für einen Hostpool mit PowerShell oder dem Azure-Portal finden Sie unter [RDP-Eigenschaften](customize-rdp-properties.md). Die vollständige Liste der unterstützten RDP-Eigenschaften finden Sie unter [Unterstützte RDP-Dateieinstellungen](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Einrichten von Geräteumleitungen

Sie können die folgenden RDP-Eigenschaften und Gruppenrichtlinieneinstellungen verwenden, um die Geräteumleitungen zu konfigurieren.

### <a name="audio-input-microphone-redirection"></a>Umleitung der Audioeingabe (Mikrofon)

Legen Sie die folgende RDP-Eigenschaft fest, um die Audioeingabeumleitung zu konfigurieren:

- Mit `audiocapturemode:i:1` aktivieren Sie die Audioeingabeumleitung.
- Mit `audiocapturemode:i:0` deaktivieren Sie die Audioeingabeumleitung.

### <a name="audio-output-speaker-redirection"></a>Umleitung der Audioausgabe (Lautsprecher)

Legen Sie die folgende RDP-Eigenschaft fest, um die Audioausgabeumleitung zu konfigurieren:

- Mit `audiomode:i:0` aktivieren Sie die Audioausgabeumleitung.
- Mit `audiomode:i:1` und `audiomode:i:2` deaktivieren Sie die Audioausgabeumleitung.

### <a name="camera-redirection"></a>Umleitung der Kamera

Legen Sie die folgende RDP-Eigenschaft fest, um die Kameraumleitung zu konfigurieren:

- `camerastoredirect:s:*` leitet alle Kameras um.
- Mit `camerastoredirect:s:` deaktivieren Sie die Kameraumleitung.

>[!NOTE]
>Selbst wenn die `camerastoredirect:s:`-Eigenschaft deaktiviert ist, können lokale Kameras über die `devicestoredirect:s:`-Eigenschaft umgeleitet werden. Um die Kameraumleitung vollständig zu deaktivieren, legen Sie `camerastoredirect:s:` fest und stellen Sie entweder `devicestoredirect:s:` ein, oder definieren Sie eine Teilmenge von Plug & Play-Geräten, die keine Kamera enthält.

Sie können auch bestimmte Kameras mithilfe einer durch Semikolons getrennten Liste von KSCATEGORY_VIDEO_CAMERA-Schnittstellen umleiten, z. B. `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi`. 

### <a name="clipboard-redirection"></a>Umleitung der Zwischenablage

Legen Sie die folgende RDP-Eigenschaft fest, um die Umleitung der Zwischenablage zu konfigurieren:

- Mit `redirectclipboard:i:1` aktivieren Sie die Zwischenablagenumleitung.
- Mit `redirectclipboard:i:0` deaktivieren Sie die Zwischenablagenumleitung.

### <a name="com-port-redirections"></a>Umleitungen von COM-Ports

Legen Sie die folgende RDP-Eigenschaft fest, um die Umleitung von COM-Ports zu konfigurieren:

- Mit `redirectcomports:i:1` aktivieren Sie die Umleitung von COM-Ports.
- Mit `redirectcomports:i:0` deaktivieren Sie die Umleitung von COM-Ports.

### <a name="usb-redirection"></a>USB-Umleitung

Legen Sie zunächst die folgende RDP-Eigenschaft fest, um die Umleitung von USB-Geräten zu aktivieren:

- Mit `usbdevicestoredirect:s:*` aktivieren Sie die Umleitung von USB-Geräten.
- Mit `usbdevicestoredirect:s:` deaktivieren Sie die Umleitung von USB-Geräten.

Legen Sie dann die folgende Gruppenrichtlinie auf dem lokalen Gerät des Benutzers fest:

- Wechseln Sie zu **Computerkonfiguration** > **Richtlinien**> **Administrative Vorlagen** > **Windows-Komponenten** > **Remotedesktopdienste** > **Remotedesktopverbindungs-Client** > **RemoteFX USB-Geräteumleitung**.
- Wählen Sie **RDP-Umleitung für andere unterstützte RemoteFX USB-Geräte auf diesem Computer zulassen** aus.
- Wählen Sie die Option **Aktiviert** aus, und aktivieren Sie anschließend unter „Zugriffsrechte für RemoteFX USB-Umleitung“ das Kontrollkästchen **Administratoren und Benutzer**.
- Klicken Sie auf **OK**.

### <a name="plug-and-play-device-redirection"></a>Umleitung von Plug & Play-Geräten

Legen Sie die folgende RDP-Eigenschaft fest, um die Umleitung von Plug & Play-Geräten zu konfigurieren:

- Mit `devicestoredirect:s:*` aktivieren Sie die Umleitung aller Plug & Play-Geräte.
- Mit `devicestoredirect:s:` deaktivieren Sie die Umleitung von Plug & Play-Geräten.

Sie können auch mithilfe einer durch Semikolons getrennten Liste bestimmte Plug & Play-Geräte auswählen, z. B. `devicestoredirect:s:root\*PNP0F08`.

### <a name="local-drive-redirection"></a>Umleitung von lokalen Laufwerken

Legen Sie die folgende RDP-Eigenschaft fest, um die Umleitung von lokalen Laufwerken zu konfigurieren:

- Mit `drivestoredirect:s:*` aktivieren Sie die Umleitung aller Laufwerke.
- Mit `drivestoredirect:s:` deaktivieren Sie die Umleitung lokaler Laufwerke.

Sie können auch mithilfe einer durch Semikolons getrennten Liste bestimmte Laufwerke auswählen, z. B. `drivestoredirect:s:C:;E:;`.

### <a name="printer-redirection"></a>Umleitung von Druckern

Legen Sie die folgende RDP-Eigenschaft fest, um die Druckerumleitung zu konfigurieren:

- Mit `redirectprinters:i:1` aktivieren Sie die Druckerumleitung.
- Mit `redirectprinters:i:0` deaktivieren Sie die Druckerumleitung.

### <a name="smart-card-redirection"></a>Umleitung von Smartcards

Legen Sie die folgende RDP-Eigenschaft fest, um die Umleitung von Smartcards zu konfigurieren:

- Mit `redirectsmartcards:i:1` aktivieren Sie die Smartcardumleitung.
- Mit `redirectsmartcards:i:0` deaktivieren Sie die Smartcardumleitung.