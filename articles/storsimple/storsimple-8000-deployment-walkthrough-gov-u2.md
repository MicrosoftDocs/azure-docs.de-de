---
title: Bereitstellen von Geräten der StorSimple 8000-Serie im Government-Portal | Microsoft-Dokumentation
description: Beschreibung der Schritte und bewährten Methoden für die Bereitstellung von Geräten der StorSimple 8000-Serie mit Update 3 und höher und des StorSimple-Geräte-Manager-Diensts im Azure Government-Portal.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: alkohli
ms.openlocfilehash: d736c09fc1c9490f79dfc526895970e01b8b45cc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963180"
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>Bereitstellen lokaler StorSimple-Geräte im Government-Portal

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Übersicht
Willkommen bei der exemplarischen Vorgehensweise für die Bereitstellung von Microsoft Azure StorSimple-Geräten. Diese Bereitstellungstutorials betreffen die StorSimple 8000-Serie mit Update3 und höher im Azure Governmment-Portal. Diese Reihe von Tutorials enthält eine Konfigurationsprüfliste, Konfigurationsvoraussetzungen und ausführliche Konfigurationsschritte für Ihre StorSimple-Geräte.

Bei den Informationen in diesen Tutorials wird davon ausgegangen, dass Sie die Sicherheitsvorkehrungen geprüft und Ihr StorSimple-Gerät ausgepackt, installiert und verkabelt haben. Wenn Sie diese Aufgaben noch ausführen müssen, beginnen Sie mit der Prüfung der [Sicherheitsvorkehrungen](storsimple-8000-safety.md). Befolgen Sie die gerätespezifischen-Anweisungen zum Auspacken, Einbauen und Verkabeln des Geräts.

* [Auspacken, Einsetzen und Verkabeln – 8100](storsimple-8100-hardware-installation.md)
* [Auspacken, Einsetzen und Verkabeln – 8600](storsimple-8600-hardware-installation.md)

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Es wird empfohlen, dass Sie die Konfigurationsprüfliste überprüfen, bevor Sie beginnen. Die Bereitstellung und Konfiguration kann einige Zeit in Anspruch nehmen.

> [!NOTE]
> Die auf der Microsoft Azure-Website veröffentlichten StorSimple-Bereitstellungsinformationen gelten nur für Geräte der StorSimple 8000-Reihe. Vollständige Informationen zu Geräten der Serie 7000 finden Sie unter: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Bereitstellungsinformationen zur Serie 7000 finden Sie in der [Schnellstartanleitung zum StorSimple-System](http://onlinehelp.storsimple.com/111_Appliance/)(in englischer Sprache).


## <a name="deployment-steps"></a>Bereitstellungsschritte
Führen Sie die folgenden erforderlichen Schritte zum Konfigurieren Ihres StorSimple-Geräts und zum Verbinden des Geräts mit dem StorSimple-Geräte-Manager-Dienst aus. Zusätzlich zu diesen erforderlichen Schritten müssen Sie während der Bereitstellung möglicherweise optionale Schritte und Verfahren ausführen. Die schrittweise Bereitstellungsanleitung gibt auch an, wann Sie die folgenden optionalen Schritte jeweils ausführen sollten.

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
| [Schritt 4: Abschließen der Mindesteinrichtung des Geräts](#step-4-complete-minimum-device-setup) </br>Optional: Aktualisieren Sie Ihr StorSimple-Gerät. |Verwenden Sie den Verwaltungsdienst, um die Gerätekonfiguration abzuschließen und es für die Bereitstellung von Speicher zu aktivieren. |
| [Schritt 5: Erstellen eines Volumecontainers](#step-5-create-a-volume-container) |Erstellen Sie einen Container zum Bereitstellen von Volumes. Ein Volumecontainer verfügt über Einstellungen für das Speicherkonto, die Bandbreite und die Verschlüsselung für alle darin enthaltenen Volumes. |
| [Schritt 6: Erstellen eines Volumes](#step-6-create-a-volume) |Stellen Sie ein oder mehrere Speichervolumes für Ihre Server auf dem StorSimple-Gerät bereit. |
| [Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes](#step-7-mount-initialize-and-format-a-volume) </br>Optional: Konfigurieren Sie MPIO. |Verbinden Sie Ihre Server mit dem vom Gerät bereitgestellten iSCSI-Speicher. Konfigurieren Sie optional MPIO, um sicherzustellen, dass Ihre Server Link-, Netzwerk- und Schnittstellenfehler tolerieren können. |
| [Schritt 8: Erstellen einer Sicherung](#step-8-take-a-backup) |Richten Sie Ihre Sicherungsrichtlinie zum Schutz Ihrer Daten ein. |
|  | |
| **WEITERE VERFAHREN** |Möglicherweise müssen Sie beim Bereitstellen Ihrer Lösung auf diese Prozeduren zurückgreifen. |
| [Konfigurieren eines neuen Speicherkontos für den Dienst](#configure-a-new-storage-account-for-the-service) | |
| [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](#use-putty-to-connect-to-the-device-serial-console) | |
| [Suchen nach und Anwenden von Updates](#scan-for-and-apply-updates) | |
| [Abrufen des IQN eines Windows Server-Hosts](#get-the-iqn-of-a-windows-server-host) | |
| [Erstellen einer manuellen Sicherung](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Konfigurationsprüfliste für die Bereitstellung
Bevor Sie Ihr StorSimple-Gerät bereitstellen, müssen Sie Informationen zum Konfigurieren der Software auf Ihrem Gerät sammeln. Wenn Sie einige dieser Informationen im Voraus erfassen, beschleunigt dies die Bereitstellung des StorSimple-Geräts in Ihrer Umgebung. Laden Sie die diese Prüfliste herunter, und verwenden Sie sie, um während der Bereitstellung des Geräts die Konfigurationsdetails zu notieren.

[Konfigurationsprüfliste für StorSimple-Bereitstellung herunterladen (in englischer Sprache)](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Voraussetzungen für die Bereitstellung
In den folgenden Abschnitten werden die Konfigurationsvoraussetzungen für den StorSimple-Geräte-Manager-Dienst und das StorSimple-Gerät beschrieben.

### <a name="for-the-storsimple-device-manager-service"></a>Voraussetzungen für den StorSimple-Geräte-Manager-Dienst
Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie besitzen ein Microsoft-Konto mit Anmeldeinformationen für den Zugriff.
* Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.
* Ihr Microsoft Azure-Abonnement ist für den StorSimple-Geräte-Manager-Dienst aktiviert. Ihr Abonnement sollten Sie über das [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)erwerben.
* Sie haben Zugriff auf Terminalemulationssoftware wie PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Für das Gerät im Datencenter
Stellen Sie vor der Konfiguration des Geräts Folgendes sicher:

* Ihr Gerät wurde vollständig ausgepackt, in einem Rack installiert und für Stromversorgung, Netzwerk- und seriellen Zugriff verkabelt, wie in folgenden Themen beschrieben:
  
  * [Auspacken, Einsetzen und Verkabeln – 8100-Gerät](storsimple-8100-hardware-installation.md)
  * [Auspacken, Einsetzen und Verkabeln – 8600-Gerät](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Für das Netzwerk im Datencenter
Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Die Ports in der Datencenter-Firewall werden geöffnet, um iSCSI- und Clouddatenverkehr zu ermöglichen, wie in [Netzwerkanforderungen für das StorSimple-Gerät](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)beschrieben.

## <a name="step-by-step-deployment"></a>SCHRITTWEISE BEREITSTELLUNG
Verwenden Sie die folgenden schrittweisen Anweisungen, um Ihr StorSimple-Gerät im Datencenter bereitzustellen.

## <a name="step-1-create-a-new-service"></a>Schritt 1: Erstellen eines neuen Diensts
Ein StorSimple-Geräte-Manager-Dienst kann mehrere StorSimple-Geräte verwalten. Führen Sie die folgenden Schritte aus, um eine neue Instanz des StorSimple-Geräte-Manager-Diensts zu erstellen.

[!INCLUDE [storsimple-8000-create-new-service-gov](../../includes/storsimple-8000-create-new-service-gov.md)]

> [!IMPORTANT]
> Wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben, müssen Sie mindestens ein Speicherkonto erstellen, nachdem Sie einen Dienst erstellt haben. Dieses Speicherkonto wird beim Erstellen eines Volumecontainers verwendet.
> 
> * Wenn Sie nicht automatisch ein Speicherkonto erstellt haben, finden Sie unter [Konfigurieren eines neuen Speicherkontos für den Dienst](#configure-a-new-storage-account-for-the-service) ausführliche Anweisungen.
> * Wenn Sie die automatische Erstellung eines Speicherkontos aktiviert haben, fahren Sie mit [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2-get-the-service-registration-key)fort.


## <a name="step-2-get-the-service-registration-key"></a>Schritt 2: Abrufen des Dienstregistrierungsschlüssels
Nachdem der StorSimple-Geräte-Manager-Dienst eingerichtet wurde und ausgeführt wird, müssen Sie den Dienstregistrierungsschlüssel abrufen. Dieser Schlüssel wird zum Registrieren Ihres StorSimple-Geräts sowie zum Herstellen einer Verbindung mit dem Dienst verwendet.

Führen Sie im Government-Portal die folgenden Schritte aus.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple
Verwenden Sie Windows PowerShell für StorSimple zum Abschließen der anfänglichen Installation Ihres StorSimple-Geräts wie im folgenden Verfahren erläutert. Sie müssen eine Terminalemulationssoftware verwenden, um diesen Schritt auszuführen. Weitere Informationen finden Sie unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-gov](../../includes/storsimple-8000-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Schritt 4: Abschließen der Mindesteinrichtung des Geräts
Sie müssen für die Gerätemindestkonfiguration des StorSimple-Geräts die folgenden Aufgaben ausführen:

* Geben Sie einen Anzeigenamen für Ihr Gerät an.
* Legen Sie die Zeitzone des Geräts fest.
* Zuweisen fester IP-Adressen an beide Controller

Führen Sie im Azure Government-Portal die folgenden Schritte aus, um die Mindesteinrichtung des Geräts auszuführen.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

## <a name="step-5-create-a-volume-container"></a>Schritt 5: Erstellen eines Volumecontainers
Ein Volumecontainer verfügt über Einstellungen für das Speicherkonto, die Bandbreite und die Verschlüsselung für alle darin enthaltenen Volumes. Sie müssen einen Volumecontainer erstellen, bevor Sie mit der Bereitstellung von Volumes auf Ihrem StorSimple-Gerät beginnen können.

Führen Sie im Government-Portal die folgenden Schritte aus, um einen Volumecontainer zu erstellen.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Schritt 6: Erstellen eines Volumes
Nach dem Erstellen eines Volumecontainers können Sie ein Speichervolume auf dem StorSimple-Gerät für Ihre Server bereitstellen. Führen Sie im Government-Portal die folgenden Schritte aus, um ein Volume zu erstellen.

> [!IMPORTANT]
> Mit dem StorSimple-Geräte-Manager können nur mit schlanker Speicherzuweisung bereitgestellte Volumes erstellt werden.  Sie können jedoch keine teilweise bereitgestellten Volumes erstellen.

[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes
Führen Sie die folgenden Schritte auf Ihrem Windows Server-Host aus.

> [!IMPORTANT]
> * Um die Hochverfügbarkeit Ihrer StorSimple-Lösung sicherzustellen, empfiehlt es sich, vor der Konfiguration von iSCSI auf Ihren Hostservern optional MPIO auf diesen Hosts zu konfigurieren. Die MPIO-Konfiguration auf Hostservern sorgt dafür, dass die Server Fehler bei Links, dem Netzwerk oder einzelnen Schnittstellen tolerieren können.
> * Installations- und Konfigurationsanweisungen für MPIO und iSCSI auf einem Windows Server-Host finden Sie unter [Konfigurieren von MPIO für Ihr StorSimple-Gerät](./storsimple-8000-configure-mpio-windows-server.md). Diese enthalten auch die Schritte zum Bereitstellen, Initialisieren und Formatieren von StorSimple-Volumes.
> * Installations- und Konfigurationsanweisungen für MPIO und iSCSI auf einem Linux-Host finden Sie unter [Konfigurieren von MPIO für Ihren StorSimple-Linux-Host](storsimple-configure-mpio-on-linux.md)

Wenn Sie MPIO nicht konfigurieren, führen Sie die folgenden Schritte aus, um Ihre StorSimple-Volumes auf einem Windows Server-Host bereitzustellen, zu initialisieren und zu formatieren.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Schritt 8: Erstellen einer Sicherung
Sicherungen stellen Zeitpunktschutz für Volumes zur Verfügung und verbessern die Wiederherstellbarkeit bei gleichzeitiger Minimierung der Wiederherstellungszeiten. Für Ihr StorSimple-Gerät können zwei Arten von Sicherungen angefertigt werden: lokale Momentaufnahmen und Cloudmomentaufnahmen. Jeder dieser Sicherungstypen kann **geplant** sein oder **manuell** erfolgen.

Führen Sie im Government-Portal die folgenden Schritte aus, um eine geplante Sicherung zu erstellen.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Sie können jederzeit eine manuelle Sicherung vornehmen. Anweisungen dazu finden Sie unter [Erstellen einer manuellen Sicherung](#create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Konfigurieren eines neuen Speicherkontos für den Dienst
Dies ist ein optionaler Schritt, den Sie nur dann ausführen müssen, wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben. Ein Microsoft Azure-Speicherkonto ist erforderlich, um einen StorSimple-Volumecontainer zu erstellen.

Wenn Sie ein Azure-Speicherkonto in einer anderen Region erstellen müssen, finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-account-create.md) schrittweise Anweisungen.

Führen Sie im Government-Portal auf der Seite für den **StorSimple-Geräte-Manager-Dienst** die folgenden Schritte aus.

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole
Zum Herstellen einer Verbindung mit Windows PowerShell für StorSimple benötigen Sie eine Terminalemulationssoftware wie etwa PuTTY. Sie können PuTTY verwenden, wenn Sie über die serielle Konsole direkt auf das Gerät zugreifen oder eine Telnet-Sitzung von einem Remotecomputer aus öffnen.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Suchen nach und Anwenden von Updates
Die Aktualisierung eines Geräts kann mehrere Stunden dauern. Ausführliche Informationen zur Installation des aktuellen Updates finden Sie unter [Install Update 4](storsimple-8000-install-update-4.md) (Installieren von Update 4).

## <a name="get-the-iqn-of-a-windows-server-host"></a>Abrufen des IQN eines Windows Server-Hosts
Führen Sie die folgenden Schritte aus, um den IQN (iSCSI Qualified Name) eines Windows-Hosts abzurufen, auf dem Windows Server® 2012 ausgeführt wird.

[!INCLUDE [Get IQN of your Windows Server host](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Erstellen einer manuellen Sicherung
Führen Sie im Government-Portal die folgenden Schritte aus, um ein manuelles On-Demand-Backup für ein einzelnes Volume auf dem StorSimple-Gerät zu erstellen.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Nächste Schritte
* Konfigurieren eines [virtuellen Geräts](storsimple-8000-cloud-appliance-u2.md)
* Verwenden des [StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md) zum Verwalten Ihres StorSimple-Geräts