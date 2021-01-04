---
title: Microsoft Teams in Windows Virtual Desktop – Azure
description: Hier erfahren Sie, wie Sie Microsoft Teams in Windows Virtual Desktop verwenden.
author: Heidilohr
ms.topic: how-to
ms.date: 11/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 101b3a05591a7815ba28756bb5b07e855b64e769
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505545"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Verwenden von Microsoft Teams in Windows Virtual Desktop

>[!IMPORTANT]
>Die Medienoptimierung für Teams wird für Microsoft 365 Government-Umgebungen (GCC) unterstützt. Die Medienoptimierung für Teams wird für GCC-High oder DoD nicht unterstützt.

>[!NOTE]
>Die Medienoptimierung für Microsoft Teams steht nur für den Windows Desktop-Client auf Windows 10-Computern zur Verfügung. Medienoptimierungen erfordern Windows Desktop-Clientversion 1.2.1026.0 oder höher.

Microsoft Teams auf Windows Virtual Desktop unterstützt Chat und Zusammenarbeit. Mit Medienoptimierungen werden auch Anruf- und Besprechungsfunktionen unterstützt. Weitere Informationen zur Verwendung von Microsoft Teams in VDI-Umgebungen (Virtual Desktop Infrastructure, virtualisierte Desktopinfrastruktur) finden Sie unter [Teams für virtualisierte Desktopinfrastruktur](/microsoftteams/teams-for-vdi/).

Mit der Medienoptimierung für Microsoft Teams verarbeitet der Windows Desktop-Client Audio- und Videodaten lokal für Anrufe und Besprechungen von Teams. Sie können Microsoft Teams weiterhin auf Windows Virtual Desktop mit anderen Clients ohne Optimierung von Anrufen und Besprechungen verwenden. Die Chat- und Zusammenarbeitsfunktionen von Teams werden auf allen Plattformen unterstützt. Wenn Sie lokale Geräte in Ihre Remotesitzung umleiten möchten, lesen Sie [Anpassen der Remotedesktopprotokoll-Eigenschaften für einen Hostpool](#customize-remote-desktop-protocol-properties-for-a-host-pool).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Microsoft Teams in Windows Virtual Desktop verwenden können, müssen Sie Folgendes tun:

- [Bereiten Sie Ihr Netzwerk](/microsoftteams/prepare-network/) für Microsoft Teams vor.
- Installieren Sie den [Windows Desktop-Client](connect-windows-7-10.md) auf einem Windows 10- oder Windows 10 IoT Enterprise-Gerät, das die [Hardwareanforderungen für Microsoft Teams auf einem Windows-PC](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/) erfüllt.
- Stellen Sie eine Verbindung mit einer Windows 10-VM mit mehreren Sitzungen oder einer virtuellen Windows 10 Enterprise-VM her.

## <a name="install-the-teams-desktop-app"></a>Installieren der Teams-Desktop-App

In diesem Abschnitt erfahren Sie, wie Sie die Teams-Desktop-App auf Ihrem Windows 10-Multisession- oder Windows 10-Enterprise-VM-Image installieren. Weitere Informationen finden Sie unter [Installieren oder Aktualisieren der Desktop-App für Teams in VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi).

### <a name="prepare-your-image-for-teams"></a>Vorbereiten des Images für Teams

Um die Medienoptimierung für Teams zu aktivieren, legen Sie den folgenden Registrierungsschlüssel auf dem Host fest:

1. Führen Sie im Startmenü **RegEdit** als Administrator aus. Navigieren Sie zu **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams**. Erstellen Sie den Schlüssel „Teams“, wenn er noch nicht vorhanden ist.

2. Erstellen Sie den folgenden Wert für den Teams-Schlüssel:

| Name             | type   | Daten/Wert  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Installieren des Teams-WebSocket-Diensts

Installieren Sie den neuesten [Remotedesktop-WebRTC-Redirectordienst](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt) auf Ihrem VM-Image. Wenn ein Installationsfehler auftritt, installieren Sie das [aktuelle Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads), und versuchen Sie es erneut.

#### <a name="latest-websocket-service-versions"></a>Neueste Versionen des WebSocket-Diensts

In der folgenden Tabelle sind die neuesten Versionen des WebSocket-Diensts aufgeführt:

|Version        |Veröffentlichungsdatum  |
|---------------|--------------|
|1.0.2006.11001 |28.07.2020    |
|0.11.0         |29.05.2020    |

#### <a name="updates-for-version-10200611001"></a>Updates für Version 1.0.2006.11001

- Es wurde ein Problem behoben, bei dem das Minimieren der Teams-App während eines Anrufs oder einer Besprechung dazu führte, dass eingehende Videos nicht angezeigt wurden.
- Unterstützung für die Auswahl eines Monitors zur Freigabe in Desktopsitzungen mit mehreren Monitoren wurde hinzugefügt.

### <a name="install-microsoft-teams"></a>Installieren von Microsoft Teams

Sie können die Teams-Desktop-App mit einer Pro-Computer- oder Pro-Benutzer-Installation bereitstellen. So installieren Sie Microsoft Teams in Ihrer Windows Virtual Desktop-Umgebung:

1. Laden Sie das [Teams-MSI-Paket](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) herunter, das Ihrer Umgebung entspricht. Es wird empfohlen, das 64-Bit-Installationsprogramm auf einem 64-Bit-Betriebssystem zu verwenden.

      > [!IMPORTANT]
      > Mit dem neuesten Update der Teams-Desktopclientversion 1.3.00.21759 wurde ein Problem behoben, bei dem Teams im Chat, in Kanälen und im Kalender eine UTC-Zeitzone angezeigt hat. In der neuen Version des Clients wird die Zeitzone der Remotesitzung angezeigt.

2. Führen Sie einen der folgenden Befehle aus, um die MSI-Datei auf der Host-VM zu installieren:

    - Pro-Benutzer-Installation

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        Diese Vorgehensweise ist die Standardinstallation, bei der Teams im **%AppData%** -Benutzerordner installiert wird. Teams kann nicht ordnungsgemäß mit einer Pro-Benutzer-Installation funktionieren, wenn diese sich in einer nicht beständigen Einrichtung befindet.

    - Pro-Computer-Installation

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        Dadurch wird Teams auf einem 32-Bit-Betriebssystem im Ordner „Programme (x86)“ und auf einem 64-Bit-Betriebssystem im Ordner „Programme“ installiert. An diesem Punkt ist das Golden Image-Setup fertiggestellt. Die Installation von Teams pro Computer ist für nicht beständige Setups erforderlich.

        Bei der Installation von Teams können zwei Flags festgelegt werden, **ALLUSER=1** und **ALLUSERS=1**. Es ist wichtig, den Unterschied zwischen diesen Parametern zu verstehen. Der Parameter **ALLUSER=1** wird nur in VDI-Umgebungen verwendet, um eine Pro-Computer-Installation anzugeben. Der Parameter **ALLUSERS=1** kann in Nicht-VDI- und in VDI-Umgebungen verwendet werden. Wenn Sie diesen Parameter festlegen, wird das computerweite Installationsprogramm von **Teams in der Systemsteuerung** unter „Programme und Features“ sowie in den Windows-Einstellungen unter „Apps & Features“ angezeigt. Alle Benutzer mit Administratoranmeldeinformationen auf dem Computer können Teams deinstallieren.

        > [!NOTE]
        > Benutzer und Administratoren können den automatischen Start für Teams während der Anmeldung zu diesem Zeitpunkt nicht deaktivieren.

3. Führen Sie den folgenden Befehl aus, um MSI auf der Host-VM zu deinstallieren:

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Hierdurch wird Teams je nach Betriebssystemumgebung aus dem Ordner „Programme (x86)“ oder „Programme“ deinstalliert.

      > [!NOTE]
      > Wenn Sie Teams mit der MSI-Einstellung „ALLUSER=1“ installieren, werden automatische Updates deaktiviert. Es wird empfohlen, Teams mindestens einmal im Monat zu aktualisieren. Weitere Informationen zum Bereitstellen der Teams-Desktop-App finden Sie unter [Bereitstellen der Teams-Desktop-App auf der VM](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/).

### <a name="verify-media-optimizations-loaded"></a>Überprüfen, ob Medienoptimierungen geladen wurden

Nachdem Sie den WebSocket-Dienst und die Teams-Desktop-App installiert haben, führen Sie die folgenden Schritte aus, um zu überprüfen, ob die Teams-Medienoptimierungen geladen wurden:

1. Beenden Sie die Teams-Anwendung, und starten Sie sie erneut.

2. Wählen Sie Ihr Benutzerprofilbild und dann **Informationen zu** aus.

3. Wählen Sie **Version** aus.

      Wenn Medienoptimierungen geladen wurden, zeigt das Banner **WVD-Medien optimiert** an. Wenn das Banner **WVD-Medien nicht verbunden** anzeigt, beenden Sie die Teams-App, und versuchen Sie es erneut.

4. Wählen Sie Ihr Benutzerprofilbild und dann **Einstellungen** aus.

      Wenn Medienoptimierungen geladen wurden, werden die lokal verfügbaren Audiogeräte und Kameras im Gerätemenü aufgelistet. Wenn im Menü **Remoteaudio** angezeigt wird, beenden Sie die Teams-App, und versuchen Sie es erneut. Wenn die Geräte weiterhin nicht im Menü angezeigt werden, überprüfen Sie die Datenschutzeinstellungen auf Ihrem lokalen PC. Stellen Sie sicher, dass unter **Einstellungen** > **Datenschutz** > **App-Berechtigungen** die Einstellung **Apps den Zugriff auf Ihr Mikrofon erlauben** auf **Ein** festgelegt ist. Trennen Sie die Verbindung zur Remotesitzung, stellen Sie sie dann wieder her, und überprüfen Sie die Audio- und Videogeräte erneut. Um an Anrufen und Besprechungen mit Video teilzunehmen, müssen Sie auch Apps den Zugriff auf Ihre Kamera erlauben.

      Wenn die Optimierungen nicht geladen werden, deinstallieren Sie sie, installieren Sie Teams neu, und überprüfen Sie sie erneut.

## <a name="known-issues-and-limitations"></a>Einschränkungen und bekannte Probleme

Die Verwendung von Teams in einer virtualisierten Umgebung unterscheidet sich von der Verwendung von Teams in einer nicht virtualisierten Umgebung. Weitere Informationen zu den Einschränkungen von Teams in virtualisierten Umgebungen finden Sie unter [Bekannte Probleme und Einschränkungen](/microsoftteams/teams-for-vdi#known-issues-and-limitations).

### <a name="client-deployment-installation-and-setup"></a>Clientbereitstellung, Installation und Setup

- Bei der Installation pro Computer wird Teams auf VDI nicht auf dieselbe Weise automatisch aktualisiert wie Nicht-VDI-Teams-Clients. Um den Client zu aktualisieren, müssen Sie das VM-Image aktualisieren, indem Sie eine neue MSI-Datei installieren.
- Die Medienoptimierung für Teams wird nur für den Windows Desktop-Client auf Windows 10-Computern unterstützt.
- Die Verwendung expliziter, auf einem Endpunkt definierter HTTP-Proxys wird nicht unterstützt.

### <a name="calls-and-meetings"></a>Anrufe und Besprechungen

- Der Desktopclient für Teams in Windows Virtual Desktop-Umgebungen unterstützt keine Liveereignisse. Sie sollten derzeit stattdessen über den [Teams-Webclient](https://teams.microsoft.com) an Liveereignissen in Ihrer Remotesitzung teilnehmen.
- Anrufe oder Besprechungen unterstützen derzeit keine Anwendungsfreigabe. Desktopsitzungen unterstützen die Desktopfreigabe.
- Übergabe und Übernahme der Steuerung werden derzeit nicht unterstützt.
- Auf Windows Virtual Desktop unterstützt Teams nur jeweils eine eingehende Videoeingabe. Dies bedeutet, dass immer dann, wenn jemand versucht, seinen Bildschirm zu teilen, der Bildschirm desjenigen anstelle des Bildschirms des Besprechungsleiters angezeigt wird.
- Aufgrund von WebRTC-Einschränkungen ist die Auflösung eingehender und ausgehender Videostreams auf 720p beschränkt.
- Die Teams-App unterstützt keine HID-Schaltflächen oder LED-Steuerelemente in Verbindung mit anderen Geräten.

Informationen zu bekannten Problemen bei Teams, die sich nicht auf virtualisierte Umgebungen beziehen, finden Sie unter [Unterstützung von Microsoft Teams in Ihrer Organisation](/microsoftteams/known-issues).

## <a name="uservoice-site"></a>UserVoice-Website

Geben Sie auf der [UserVoice-Website](https://microsoftteams.uservoice.com/) von Teams Ihr Feedback zu Microsoft Teams auf Windows Virtual Desktop.

## <a name="collect-teams-logs"></a>Sammeln von Teams-Protokollen

Wenn bei der Teams-Desktop-App in Ihrer Windows Virtual Desktop-Umgebung Probleme auftreten, sammeln Sie Clientprotokolle auf der Host-VM unter **%appdata%\Microsoft\Teams\logs.txt**.

Wenn Probleme bei Anrufen und Besprechungen auftreten, sammeln Sie Teams-Webclientprotokolle mit der Tastenkombination **STRG** + **ALT** + **UMSCHALT** + **1**. Die Protokolle werden auf der Host-VM in **%userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME.txt** geschrieben.

## <a name="contact-microsoft-teams-support"></a>Kontaktieren des Microsoft Teams-Supports

Wenn Sie sich an den Microsoft Teams-Support wenden möchten, besuchen Sie das [Microsoft 365-Admin Center](/microsoft-365/admin/contact-support-for-business-products).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Anpassen der Remotedesktopprotokoll-Eigenschaften für einen Hostpool

Durch Anpassen der RDP-Eigenschaften (Remotedesktopprotokoll) eines Hostpools, z. B. Unterstützung mehrerer Monitore, Aktivieren des Mikrofons und Audioumleitung, können Sie für Benutzer eine optimale Bedienumgebung bereitstellen, die ihren Anforderungen entspricht.

Das Aktivieren von Geräteumleitungen ist nicht erforderlich, wenn Teams mit Medienoptimierung verwendet wird. Wenn Sie Teams ohne Medienoptimierung verwenden, legen Sie die folgenden RDP-Eigenschaften fest, um die Mikrofon- und Kameraumleitung zu aktivieren:

- `audiocapturemode:i:1` aktiviert die Audioerfassung vom lokalen Gerät und leitet Audioanwendungen in die Remotesitzung um.
- `audiomode:i:0` legt die Audiowiedergabe auf dem lokalen Computer fest.
- `camerastoredirect:s:*` leitet alle Kameras um.

Weitere Informationen finden Sie unter [Anpassen der Remotedesktopprotokoll-Eigenschaften für einen Hostpool](customize-rdp-properties.md).
