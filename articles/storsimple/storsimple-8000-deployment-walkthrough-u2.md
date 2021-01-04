---
title: Bereitstellen von Geräten der StorSimple 8000-Serie über das Azure-Portal
description: Enthält eine Beschreibung der Schritte und bewährten Methoden für die Bereitstellung des Geräts der StorSimple-Serie 8000 mit Update 3 und höher und des StorSimple-Geräte-Manager-Diensts.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: alkohli
ms.openlocfilehash: 2c9feb1131f6d2d0eb75ac71e27dc46c226c52c1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961055"
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>Bereitstellen lokaler StorSimple-Geräte (Update 3 und höher)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Übersicht
Willkommen bei der exemplarischen Vorgehensweise für die Bereitstellung von Microsoft Azure StorSimple-Geräten. Diese Bereitstellungstutorials beziehen sich auf Update 3 (oder höher) der StorSimple 8000-Serie. Diese Reihe von Tutorials enthält eine Konfigurationsprüfliste, Konfigurationsvoraussetzungen und detaillierte Konfigurationsschritte für Ihre StorSimple-Geräte.

Bei den Informationen in diesen Tutorials wird davon ausgegangen, dass Sie die Sicherheitsvorkehrungen geprüft und Ihr StorSimple-Gerät ausgepackt, installiert und verkabelt haben. Wenn Sie diese Aufgaben noch ausführen müssen, beginnen Sie mit der Prüfung der [Sicherheitsvorkehrungen](storsimple-8000-safety.md). Befolgen Sie die gerätespezifischen-Anweisungen zum Auspacken, Einbauen und Verkabeln des Geräts.

* [Auspacken, Einsetzen und Verkabeln – 8100](storsimple-8100-hardware-installation.md)
* [Auspacken, Einsetzen und Verkabeln – 8600](storsimple-8600-hardware-installation.md)

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Es wird empfohlen, dass Sie die Konfigurationsprüfliste überprüfen, bevor Sie beginnen. Die Bereitstellung und Konfiguration kann einige Zeit in Anspruch nehmen.

> [!NOTE]
> Die auf der Microsoft Azure-Website veröffentlichten StorSimple-Bereitstellungsinformationen gelten nur für Geräte der StorSimple 8000-Reihe. Vollständige Informationen zu Geräten der Serie 7000 finden Sie unter: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Bereitstellungsinformationen zur Serie 7000 finden Sie in der [Schnellstartanleitung zum StorSimple-System](http://onlinehelp.storsimple.com/111_Appliance/)(in englischer Sprache). 


## <a name="deployment-steps"></a>Bereitstellungsschritte
Führen Sie die folgenden erforderlichen Schritte zum Konfigurieren Ihres StorSimple-Geräts und zum Verbinden des Geräts mit dem StorSimple-Geräte-Manager-Dienst aus. Zusätzlich zu diesen erforderlichen Schritten müssen Sie während der Bereitstellung möglicherweise optionale Schritte ausführen. Die schrittweise Bereitstellungsanleitung gibt auch an, wann Sie die folgenden optionalen Schritte jeweils ausführen sollten.

| Schritt | BESCHREIBUNG |
| --- | --- |
| **VORAUSSETZUNGEN** |Diese Schritte müssen zur Vorbereitung der Bereitstellung ausgeführt werden. |
| [Konfigurationsprüfliste für die Bereitstellung](#deployment-configuration-checklist) |Verwenden Sie diese Prüfliste, um vor und während der Bereitstellung Informationen zu erfassen und aufzuzeichnen. |
| [Voraussetzungen für die Bereitstellung](#deployment-prerequisites) |Mit diesen Schritten überprüfen Sie, ob die Umgebung für die Bereitstellung vorbereitet ist. |
|  | |
| **SCHRITTWEISE BEREITSTELLUNG** |Diese Schritte sind zur Bereitstellung Ihres StorSimple-Geräts in einer Produktionsumgebung erforderlich. |
| [Schritt 1: Erstellen eines neuen Diensts](#step-1-create-a-new-service) |Richten Sie Cloudverwaltung und -speicher für Ihr StorSimple-Gerät ein. *Überspringen Sie diesen Schritt, wenn Sie bereits über einen Dienst für andere StorSimple-Geräte verfügen*. |
| [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2-get-the-service-registration-key) |Verwenden Sie diesen Schlüssel zum Registrieren Ihres StorSimple-Geräts sowie zum Herstellen einer Verbindung mit dem Verwaltungsdienst. |
| [Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Verwenden Sie den Verwaltungsdienst, um das Gerät zum Abschluss der Einrichtung mit Ihrem Netzwerk zu verbinden und in Azure zu registrieren. |
| [Schritt 4: Abschließen der minimalen Geräteinstallation](#step-4-complete-minimum-device-setup)</br>[Bewährte Methode: Aktualisieren Ihres StorSimple-Geräts](#scan-for-and-apply-updates) |Verwenden Sie den Verwaltungsdienst, um die Gerätekonfiguration abzuschließen und es für die Bereitstellung von Speicher zu aktivieren. |
| [Schritt 5: Erstellen eines Volumecontainers](#step-5-create-a-volume-container) |Erstellen Sie einen Container zum Bereitstellen von Volumes. Ein Volumecontainer verfügt über Einstellungen für das Speicherkonto, die Bandbreite und die Verschlüsselung für alle darin enthaltenen Volumes. |
| [Schritt 6: Erstellen eines Volumes](#step-6-create-a-volume) |Stellen Sie Speichervolumes für Ihre Server auf dem StorSimple-Gerät bereit. |
| [Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes](#step-7-mount-initialize-and-format-a-volume)</br>[Optional: Konfigurieren Sie MPIO.](storsimple-8000-configure-mpio-windows-server.md) |Verbinden Sie Ihre Server mit dem vom Gerät bereitgestellten iSCSI-Speicher. Konfigurieren Sie optional MPIO, um sicherzustellen, dass Ihre Server Link-, Netzwerk- und Schnittstellenfehler tolerieren können. |
| [Schritt 8: Erstellen einer Sicherung](#step-8-take-a-backup) |Richten Sie Ihre Sicherungsrichtlinie zum Schutz Ihrer Daten ein. |
|  | |
| **WEITERE VERFAHREN** |Möglicherweise müssen Sie beim Bereitstellen Ihrer Lösung auf diese Prozeduren zurückgreifen. |
| [Konfigurieren eines neuen Speicherkontos für den Dienst](#configure-a-new-storage-account-for-the-service) | |
| [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](#use-putty-to-connect-to-the-device-serial-console) | |
| [Abrufen des IQN eines Windows Server-Hosts](#get-the-iqn-of-a-windows-server-host) | |
| [Erstellen einer manuellen Sicherung](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Konfigurationsprüfliste für die Bereitstellung
Bevor Sie Ihr Gerät bereitstellen, müssen Sie Informationen zum Konfigurieren der Software auf Ihrem StorSimple-Gerät sammeln. Wenn Sie einige dieser Informationen im Voraus sammeln, können Sie das StorSimple-Gerät in Ihrer Umgebung schneller bereitstellen. Laden Sie die Prüfliste herunter und verwenden Sie sie, um während der Bereitstellung des Geräts die Konfigurationsdetails zu notieren.

* [Konfigurationsprüfliste für StorSimple-Bereitstellung herunterladen (in englischer Sprache)](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Voraussetzungen für die Bereitstellung
In den folgenden Abschnitten werden die Konfigurationsvoraussetzungen für den StorSimple-Geräte-Manager-Dienst und das StorSimple-Gerät beschrieben.

### <a name="for-the-storsimple-device-manager-service"></a>Voraussetzungen für den StorSimple-Geräte-Manager-Dienst
Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie besitzen ein Microsoft-Konto mit Anmeldeinformationen für den Zugriff.
* Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.
* Ihr Microsoft Azure-Abonnement ist für den StorSimple-Geräte-Manager-Dienst aktiviert. Ihr Abonnement sollten Sie über das [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)erwerben.
* Sie haben Zugriff auf Terminalemulationssoftware wie PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Für das Gerät im Datencenter
Stellen Sie vor der Gerätekonfiguration sicher, dass das Gerät vollständig ausgepackt, in einem Rack installiert und für Stromversorgung, Netzwerk- und seriellen Zugriff verkabelt wurde, wie in folgenden Themen beschrieben:

* [Auspacken, Einsetzen und Verkabeln – 8100-Gerät](storsimple-8100-hardware-installation.md)
* [Auspacken, Einsetzen und Verkabeln – 8600-Gerät](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Für das Netzwerk im Datencenter
Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Die Ports in der Datencenter-Firewall werden geöffnet, um iSCSI- und Clouddatenverkehr zu ermöglichen, wie in [Netzwerkanforderungen für das StorSimple-Gerät](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)beschrieben.

## <a name="step-by-step-deployment"></a>SCHRITTWEISE BEREITSTELLUNG
Verwenden Sie die folgenden schrittweisen Anweisungen, um Ihr StorSimple-Gerät im Datencenter bereitzustellen.

## <a name="step-1-create-a-new-service"></a>Schritt 1: Erstellen eines neuen Diensts
Ein StorSimple-Geräte-Manager-Dienst kann mehrere StorSimple-Geräte verwalten. Führen Sie die folgenden Schritte aus, um eine Instanz des StorSimple-Geräte-Manager-Diensts zu erstellen.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> Wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben, müssen Sie mindestens ein Speicherkonto erstellen, nachdem Sie einen Dienst erstellt haben. Dieses Speicherkonto wird beim Erstellen eines Volumecontainers verwendet.
>
> * Wenn Sie nicht automatisch ein Speicherkonto erstellt haben, finden Sie unter [Konfigurieren eines neuen Speicherkontos für den Dienst](#configure-a-new-storage-account-for-the-service) ausführliche Anweisungen.
> * Wenn Sie die automatische Erstellung eines Speicherkontos aktiviert haben, fahren Sie mit [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2-get-the-service-registration-key)fort.


## <a name="step-2-get-the-service-registration-key"></a>Schritt 2: Abrufen des Dienstregistrierungsschlüssels
Nachdem der StorSimple-Geräte-Manager-Dienst eingerichtet wurde und ausgeführt wird, müssen Sie den Dienstregistrierungsschlüssel abrufen. Dieser Schlüssel wird zum Registrieren Ihres StorSimple-Geräts sowie zum Herstellen einer Verbindung mit dem Dienst verwendet.

Führen Sie die folgenden Schritte im Azure-Portal aus.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple
Verwenden Sie Windows PowerShell für StorSimple zum Abschließen der anfänglichen Installation Ihres StorSimple-Geräts wie im folgenden Verfahren erläutert. Sie müssen eine Terminalemulationssoftware verwenden, um diesen Schritt auszuführen. Weitere Informationen finden Sie unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Schritt 4: Abschließen der Mindesteinrichtung des Geräts
Sie müssen für die Gerätemindestkonfiguration des StorSimple-Geräts die folgenden Aufgaben ausführen: 

* Geben Sie einen Anzeigenamen für Ihr Gerät an.
* Legen Sie die Zeitzone des Geräts fest.
* Zuweisen fester IP-Adressen an beide Controller

Führen Sie die folgenden Schritte im Azure-Portal aus, um die minimale Gerätekonfiguration durchzuführen.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

Nach Abschluss der mindestens erforderlichen Geräteeinrichtung umfasst die bewährte Methode das [Suchen nach und Anwenden von Updates](#scan-for-and-apply-updates).

## <a name="step-5-create-a-volume-container"></a>Schritt 5: Erstellen eines Volumecontainers
Ein Volumecontainer verfügt über Einstellungen für das Speicherkonto, die Bandbreite und die Verschlüsselung für alle darin enthaltenen Volumes. Sie müssen einen Volumecontainer erstellen, bevor Sie mit der Bereitstellung von Volumes auf Ihrem StorSimple-Gerät beginnen können.

Führen Sie die folgenden Schritte im Azure-Portal aus, um einen Volumecontainer zu erstellen.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Schritt 6: Erstellen eines Volumes
Nach dem Erstellen eines Volumecontainers können Sie ein Speichervolume auf dem StorSimple-Gerät für Ihre Server bereitstellen. Führen Sie die folgenden Schritte im Azure-Portal aus, um ein Volume zu erstellen.

> [!IMPORTANT]
> Mit StorSimple-Geräte-Manager können sowohl schlanke als auch vollständig bereitgestellte Volumes erstellt werden. Sie können jedoch keine teilweise bereitgestellten Volumes erstellen.


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes
Die folgenden Schritte werden auf dem Windows Server-Host ausgeführt.

> [!IMPORTANT]
> * Um die Hochverfügbarkeit Ihrer StorSimple-Lösung sicherzustellen, empfiehlt es sich, vor der Konfiguration von iSCSI auf Ihren Hostservern optional MPIO auf diesen Hosts zu konfigurieren. Die MPIO-Konfiguration auf Hostservern sorgt dafür, dass die Server Fehler bei Links, dem Netzwerk oder einzelnen Schnittstellen tolerieren können.
> * Installations- und Konfigurationsanweisungen für MPIO und iSCSI auf einem Windows Server-Host finden Sie unter [Konfigurieren von MPIO für Ihr StorSimple-Gerät](storsimple-8000-configure-mpio-windows-server.md). Diese enthalten auch die Schritte zum Bereitstellen, Initialisieren und Formatieren von StorSimple-Volumes.
> * Installations- und Konfigurationsanweisungen für MPIO und iSCSI auf einem Linux-Host finden Sie unter [Konfigurieren von MPIO für Ihren StorSimple-Linux-Host](storsimple-configure-mpio-on-linux.md)

Wenn Sie MPIO nicht konfigurieren, führen Sie die folgenden Schritte aus, um Ihre StorSimple-Volumes auf einem Windows Server-Host bereitzustellen, zu initialisieren und zu formatieren.

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Schritt 8: Erstellen einer Sicherung
Sicherungen stellen Zeitpunktschutz für Volumes zur Verfügung und verbessern die Wiederherstellbarkeit bei gleichzeitiger Minimierung der Wiederherstellungszeiten. Für Ihr StorSimple-Gerät können zwei Arten von Sicherungen angefertigt werden: lokale Momentaufnahmen und Cloudmomentaufnahmen. Jeder dieser Sicherungstypen kann **geplant** sein oder **manuell** erfolgen.

Führen Sie die folgenden Schritte im Azure-Portal aus, um eine geplante Sicherung zu erstellen.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Sie können jederzeit eine manuelle Sicherung vornehmen. Anweisungen dazu finden Sie unter [Erstellen einer manuellen Sicherung](#create-a-manual-backup).

Sie haben die Gerätekonfiguration abgeschlossen.

## <a name="configure-a-new-storage-account-for-the-service"></a>Konfigurieren eines neuen Speicherkontos für den Dienst
Dies ist ein optionaler Schritt, den Sie nur dann ausführen müssen, wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben. Ein Microsoft Azure-Speicherkonto ist erforderlich, um einen StorSimple-Volumecontainer zu erstellen.

Wenn Sie ein Azure-Speicherkonto in einer anderen Region erstellen müssen, finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-account-create.md) schrittweise Anweisungen.

Führen Sie die folgenden Schritte im Azure-Portal auf der Seite **StorSimple-Geräte-Manager-Dienst** aus.

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole
Zum Herstellen einer Verbindung mit Windows PowerShell für StorSimple benötigen Sie eine Terminalemulationssoftware wie etwa PuTTY. Sie können PuTTY verwenden, wenn Sie über die serielle Konsole direkt auf das Gerät zugreifen oder eine Telnet-Sitzung von einem Remotecomputer aus öffnen.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Suchen nach und Anwenden von Updates
Die Aktualisierung eines Geräts kann mehrere Stunden dauern. Ausführliche Informationen zur Installation des aktuellen Updates finden Sie unter [Installieren von Update 5 auf Ihrem StorSimple-Gerät](storsimple-8000-install-update-5.md).


## <a name="get-the-iqn-of-a-windows-server-host"></a>Abrufen des IQN eines Windows Server-Hosts
Führen Sie die folgenden Schritte aus, um den IQN (iSCSI Qualified Name) eines Windows-Hosts abzurufen, auf dem Windows Server® 2012 ausgeführt wird.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Erstellen einer manuellen Sicherung
Führen Sie die folgenden Schritte im Azure-Portal aus, um bei Bedarf eine manuelle Sicherung eines einzelnen Volumes auf dem StorSimple-Gerät zu erstellen.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="view-the-pinout-diagram-for-serial-cable-for-storsimple"></a>Anzeigen des Pinbelegungsdiagramms für serielle Kabel für StorSimple
Das folgende Pinbelegungsdiagramm kann für das serielle StorSimple-Konsolenkabel verwendet werden.

Hier ist die DB9-Buchse P1 und der 3,5-mm-Stecker P2.

![Pinbelegungsdiagramm 1 für serielle StorSimple-Konsolenkabel](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram1.png)

Die Spitze der Stereobuchse ist PIN 3 RX, die Mitte PIN 2 TX und die Basis PIN 1 GND, wie in der folgenden Abbildung dargestellt.

![Pinbelegungsdiagramm 2 für serielle StorSimple-Konsolenkabel](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram2.png)



## <a name="next-steps"></a>Nächste Schritte
* [Konfigurieren Sie eine StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* [Verwalten Sie Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).