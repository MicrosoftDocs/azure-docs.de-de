---
title: Konfigurieren einer GPU für Windows Virtual Desktop (Vorschauversion) – Azure
description: Erfahren Sie, wie Sie durch GPU beschleunigtes Rendering und schnellere Codierung in Windows Virtual Desktop (Vorschauversion) ermöglichen.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157880"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Konfigurieren Sie die Beschleunigung durch Graphics Processing Units (GPU) für Windows Virtual Desktop (Vorschauversion).

Die Vorschauversion von Windows Virtual Desktop unterstützt durch GPU beschleunigtes Rendern und Codieren, um die Leistung und Skalierbarkeit von Apps zu erhöhen. Die GPU-Beschleunigung ist besonders für grafikintensive Apps essentiell.

Anhand der Anweisungen in diesem Artikel können Sie eine GPU-optimierte Azure-VM erstellen, ihrem Hostpool hinzufügen und so konfigurieren, dass sie die GPU-Beschleunigung zum Rendern und Codieren verwendet. In diesem Artikel wird vorausgesetzt, dass Sie den Windows Virtual Desktop-Mandanten bereits konfiguriert haben.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Auswählen einer GPU-optimierten VM-Größe

Azure bietet mehrere [GPU-optimierte VM-Größen](/azure/virtual-machines/windows/sizes-gpu). Welche VM-Größe sich am besten für Ihren Hostpool eignet, hängt von mehreren Faktoren ab, z. B. von den Workloads Ihrer App, der gewünschten Benutzerfreundlichkeit und den Kosten. Grundsätzlich bieten größere und leistungsfähigere GPUs eine bessere Benutzerfreundlichkeit bei einer bestimmten Benutzerdichte.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Erstellen eines Hostpools, Bereitstellen der VM und Konfigurieren einer App-Gruppe

Erstellen Sie einen neuen Hostpool mit der gewählten VM-Größe. Anweisungen dazu finden Sie unter: [Tutorial: Erstellen eines Hostpools mit Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Die Vorschauversion von Windows Virtual Desktop unterstützt das durch GPU beschleunigte Rendern und Codieren für die folgenden Betriebssysteme:

* Windows 10, Version 1511 und höher
* Windows Server 2016 oder höher

Außerdem müssen Sie eine App-Gruppe konfigurieren oder die Standarddesktop-App-Gruppe „Desktop Application Group“ verwenden, die automatisch erstellt wird, wenn Sie einen neuen Hostpool erstellen. Anweisungen dazu finden Sie unter: [Tutorial: Verwalten von App-Gruppen für Windows Virtual Desktop (Vorschauversion)](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Die Vorschauversion von Windows Virtual Desktop unterstützt für GPU-fähige Hostpools nur den App-Gruppentyp „Desktop“. App-Gruppen des Typs „RemoteApp“ werden für GPU-fähige Hostpools nicht unterstützt.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installieren unterstützter Grafiktreiber auf Ihrer VM

Nach der Bereitstellung müssen Sie NVIDIA-Grafiktreiber installieren, um die GPU-Funktionen virtueller Azure-Computern der N-Serie in der Vorschauversion von Windows Virtual Desktop nutzen zu können. Befolgen Sie die Anweisungen unter [Install NVIDIA GPU drivers on N-series VMs running Windows (Installieren von NVIDIA-GPU-Treibern auf Windows-VMs der N-Serie)](/azure/virtual-machines/windows/n-series-driver-setup), um die Treiber entweder manuell oder mit der [NVIDIA-GPU-Treibererweiterung](/azure/virtual-machines/extensions/hpccompute-gpu-windows) zu installieren.

Beachten Sie, dass nur von Azure angebotene [NVIDIA-GRID-Treiber](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) für Windows Virtual Desktop (Vorschauversion) unterstützt werden.

Wenn die Treiber installiert wurden, muss die VM neu gestartet werden. Überprüfen Sie anhand der oben aufgelisteten Schritte, dass die Grafiktreiber erfolgreich installiert wurden.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurieren des durch GPU beschleunigten App-Rendering

Apps und Desktops, die mit Konfigurationen für mehrere Sitzungen ausgeführt werden, werden standardmäßig mit CPUs gerendert und nutzen nicht die verfügbaren GPUs. Konfigurieren Sie eine Gruppenrichtlinie für den Sitzungshost, um durch GPU beschleunigtes Rendering zu aktivieren:

1. Stellen Sie mit einem Konto mit lokalen Administratorrechten eine Verbindung mit dem Desktop der VM her.
2. Öffnen Sie das Startmenü, und geben Sie „gpedit.msc“ ein, um den Gruppenrichtlinien-Editor zu öffnen.
3. Navigieren Sie zu **Computer Configuration** > **Administrative Templates** > **Windows Components** > **Remote Desktop Services** > **Remote Desktop Session Host** > **Remote Session Environment** (Computerkonfiguration > Administrative Vorlagen > Windows-Komponenten > Remotedesktopdienste > Remotedesktop-Sitzungshost > Remotesitzungsumgebung).
4. Wählen Sie die Richtlinie **Standard-Hardwaregrafikadapter für alle Remotedesktopdienste-Sitzungen verwenden** aus, und **aktivieren Sie diese**, um das GPU-Rendering in der Remotesitzung zu aktivieren.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurieren der durch GPU beschleunigten Framecodierung

Der Remotedesktop codiert alle Grafiken, die von Apps und Desktops gerendert werden für die Übertragung an Remotedesktopclients (unabhängig davon, ob diese mit GPU oder CPU gerendert wurden). Standardmäßig nutzt der Remotedesktop die verfügbaren GPUs nicht für diese Codierung. Konfigurieren Sie eine Gruppenrichtlinie für den Sitzungshost, um durch GPU beschleunigte Framecodierung zu aktivieren. Führen Sie ähnlich wie oben die folgenden Schritte durch:

1. Wählen Sie die Richtlinie **H.264/AVC 444-Grafikmodus für Remotedesktopverbindungen priorisieren** aus, und **aktivieren Sie diese**, um den H.264/AVC 444-Codec in der Remotesitzung zu erzwingen.
2. Wählen Sie die Richtlinie **H.264/AVC-Hardwarecodierung für Remotedesktopverbindungen konfigurieren** aus, und **aktivieren Sie diese**, um die Hardwarecodierung für H.264/AVC in der Remotesitzung zu aktivieren.

    >[!NOTE]
    >Legen Sie in Windows Server 2016 die Option **AVC-Hardwarecodierung bevorzugen** auf **Immer versuchen** fest.

3. Erzwingen Sie ein Gruppenrichtlinienupdate, nachdem Sie die Gruppenrichtlinien bearbeitet haben. Öffnen Sie die Eingabeaufforderung, und geben Sie Folgendes ein:

    ```batch
    gpupdate.exe /force
    ```

4. Melden Sie sich bei der Remotedesktopsitzung ab.

## <a name="verify-gpu-accelerated-app-rendering"></a>Überprüfen des durch GPU beschleunigten App-Renderings

Führen Sie einen der folgenden Schritte durch, um zu überprüfen, dass Ihre Apps die GPUs für das Rendering verwenden:

* Verwenden Sie das Hilfsprogramm `nvidia-smi`, um die GPU-Nutzung beim Ausführen Ihrer Apps zu überprüfen. Dies wird im Artikel „Installieren von NVIDIA GPU-Treibern für virtuelle Computer der Serie N mit Windows“ unter [Überprüfen der Treiberinstallation](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) beschrieben.
* In unterstützten Betriebssystemversionen können Sie die GPU-Nutzung über den Task-Manager prüfen. Wählen Sie auf der Registerkarte „Leistung“ die GPU aus, um zu prüfen, ob diese von Apps genutzt wird.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Überprüfen der durch GPU beschleunigten Framecodierung

So können Sie überprüfen, ob der Remotedesktop die durch GPU beschleunigte Codierung verwendet:

1. Stellen Sie eine Verbindung mit dem Desktop der VM mithilfe des Windows Virtual Desktop-Clients her.
2. Starten Sie die Ereignisanzeige, und navigieren Sie zum folgenden Knoten: **Applications and Services Logs** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS** > **Operational** (Anwendungs- und Dienstprotokolle > Microsoft > Windows > RemoteDesktopServices-RdpCoreTS > Betriebsbereit)
3. Suchen Sie nach der Ereignis-ID 170, um zu überprüfen, ob die durch GPU beschleunigte Codierung verwendet wird. Wenn „Der AVC-Hardware-Encoder ist aktiviert: 1“ angezeigt wird, wird die GPU-Codierung verwendet.
4. Suchen Sie nach der Ereignis-ID 162, um zu überprüfen, ob der AVC 444-Modus aktiviert ist: Wenn „AVC aktiviert: 1, Anfangsprofil: 2048“ angezeigt wird, wird AVC 444 verwendet.

## <a name="next-steps"></a>Nächste Schritte

Anhand der beschriebenen Anweisungen sollten Sie nun die GPU-Beschleunigung auf einer einzigen Sitzungshost-VM verwenden können. Für das Verwenden der GPU-Beschleunigung für einen größeren Hostpool können Sie folgende Aspekte in Erwägung ziehen:

* Die Verwendung der [NVIDIA-GPU-Treibererweiterung](/azure/virtual-machines/extensions/hpccompute-gpu-windows), um die Treiberinstallation und -updates für mehrere VMs zu vereinfachen.
* Die Verwendung einer Active Directory Domain Services-Gruppenrichtlinie, um die Konfiguration von Gruppenrichtlinien für mehrere VMs zu vereinfachen. Weitere Informationen zur Bereitstellung von Gruppenrichtlinien in der AD DS-Domäne finden Sie unter [Working with Group Policy Objects (Verwenden von Gruppenrichtlinienobjekten)](https://go.microsoft.com/fwlink/p/?LinkId=620889).
