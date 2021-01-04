---
title: Verwaltung des StorSimple-Geräte-Manager-Diensts | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts im Azure-Portal verwalten.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: 69a36f38cc76693f596b45d4df6e232bc01584d5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967974"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts

## <a name="overview"></a>Übersicht

Dieser Artikel beschreibt die Benutzeroberfläche des StorSimple-Geräte-Manager-Diensts und erläutert, wie Sie eine Verbindung herstellen und welche Optionen verfügbar sind. Darüber hinaus enthält der Artikel Links zu den Workflows, die über diese Benutzeroberfläche ausgeführt werden können. Die Anleitung gilt sowohl für das physische das physische StorSimple-Gerät als auch für das Cloudgerät.

In diesem Artikel lernen Sie Folgendes:

* Herstellen einer Verbindung mit dem StorSimple-Geräte-Manager-Dienst
* Verwalten Ihres StorSimple-Geräts mit dem StorSimple-Geräte-Manager-Dienst

## <a name="connect-to-storsimple-device-manager-service"></a>Herstellen einer Verbindung mit dem StorSimple-Geräte-Manager-Dienst

Der StorSimple-Geräte-Manager-Dienst wird in Microsoft Azure ausgeführt. Er verfügt über Verbindungen mit mehreren StorSimple-Geräten. Diese Geräte werden über ein zentrales browserbasiertes Microsoft Azure-Portal verwaltet. Gehen Sie wie folgt vor, um eine Verbindung mit dem StorSimple-Geräte-Manager-Dienst herzustellen:

#### <a name="to-connect-to-the-service"></a>So stellen Sie eine Verbindung mit dem Dienst her
1. Navigieren Sie zu [https://portal.azure.com/](https://portal.azure.com/).
2. Melden Sie sich mit den Anmeldeinformationen Ihres Microsoft-Kontos beim Microsoft Azure-Portal (rechts oben) an.
3. Führen Sie im linken Navigationsbereich einen Bildlauf nach unten durch, um auf den StorSimple-Geräte-Manager-Dienst zuzugreifen.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Verwalten des StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts

Die folgende Tabelle zeigt eine Zusammenfassung der allgemeinen Verwaltungsaufgaben und komplexen Workflows, die auf der Benutzeroberfläche des StorSimple-Geräte-Manager-Diensts ausgeführt werden können. Diese Aufgaben sind nach den Blättern organisiert, auf denen sie jeweils gestartet werden.

Klicken Sie auf die Verfahren in der Tabelle, um weitere Informationen zu den einzelnen Workflows zu erhalten.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple-Geräte-Manager-Workflows

| Gewünschte Aktion | Verfahren |
| --- | --- |
| Erstellen von Diensten</br>Löschen von Diensten</br>Dienstregistrierungsschlüssel abrufen</br>Dienstregistrierungsschlüssel erneut generieren |[Löschen eines StorSimple-Geräte-Manager-Diensts](storsimple-8000-manage-service.md) |
| Anzeigen der Aktivitätsprotokolle |[Verwenden der Übersicht über den StorSimple-Geräte-Manager-Dienst](storsimple-8000-service-dashboard.md) |
| Ändern des Verschlüsselungsschlüssels für Dienstdaten</br>Vorgangsprotokolle anzeigen |[Verwenden des Dashboards des StorSimple-Geräte-Manager-Diensts](storsimple-8000-service-dashboard.md) |
| Deaktivieren eines Geräts</br>Löschen eines Mediums |[Deaktivieren oder Löschen eines Geräts](storsimple-8000-deactivate-and-delete-device.md) |
| Über Notfallwiederherstellung und Gerätefailover informieren</br>Durchführen eines Failovers auf ein physisches Gerät</br>Durchführen eines Failovers auf ein virtuelles Gerät</br>Business Continuity Disaster Recovery (BCDR) |[Ausführen eines Failovers und einer Notfallwiederherstellung für das StorSimple-Gerät](storsimple-8000-device-failover-disaster-recovery.md) |
| Liste mit Sicherungen für ein Volume anzeigen</br>Wählen Sie einen Sicherungssatz aus</br>Löschen eines Sicherungssatzes |[Verwalten von Sicherungen](storsimple-8000-manage-backup-catalog.md) |
| Volume klonen |[Klonen eines Volumes](storsimple-8000-clone-volume-u2.md) |
| Sicherungssatz wiederherstellen |[Wiederherstellen eines Sicherungssatzes](storsimple-8000-restore-from-backup-set-u2.md) |
| Informationen zu Speicherkonten</br>Hinzufügen von Speicherkonten</br>Bearbeiten eines Speicherkontos</br>Löschen von Speicherkonten</br>Schlüsselrotation für Speicherkonten |[Verwalten von Speicherkonten](storsimple-8000-manage-storage-accounts.md) |
| Über Bandbreitenvorlagen informieren</br>Hinzufügen einer Bandbreitenvorlage</br>Bearbeiten einer Bandbreitenvorlage</br>Löschen einer Bandbreitenvorlage</br>Verwenden einer Standardbandbreitenvorlage</br>Erstellen einer Bandbreitenvorlage für einen gesamten Tag, die zu einem bestimmten Zeitpunkt startet |[Verwalten von Bandbreitenvorlagen](storsimple-8000-manage-bandwidth-templates.md) |
| Informationen zu Access Control-Datensätzen</br>Erstellen eines Zugriffssteuerungsdatensatzes</br>Bearbeiten von Zugriffssteuerungsdatensätzen</br>Löschen von Access Control-Datensätzen |[Verwalten von Zugriffssteuerungsdatensätzen](storsimple-8000-manage-acrs.md) |
| Anzeigen von Auftragsdetails</br>Abbrechen eines Auftrags |[Verwalten von Aufträgen](storsimple-8000-manage-jobs-u2.md) |
| Warnungsbenachrichtigungen empfangen</br>Warnungen verwalten</br>Überprüfen von Warnungen |[Anzeigen und Verwalten von StorSimple-Warnungen](storsimple-8000-manage-alerts.md) |
| Überwachungsdiagramme erstellen |[Überwachen von StorSimple-Geräten](./storsimple-8000-monitor-device.md) |
| Hinzufügen eines Volumecontainers</br>Ändern eines Volumecontainers</br>Löschen eines Volumecontainers |[Verwalten von Volumecontainern](storsimple-8000-manage-volume-containers.md) |
| Hinzufügen eines Volumes</br>Ändern von Volumes</br>Offlineschalten von Volumes</br>Löschen von Volumes</br>Volume überwachen |[Verwalten von Volumes](storsimple-8000-manage-volumes-u2.md) |
| Ändern von Geräteeinstellungen</br>Ändern von Zeiteinstellungen</br>Ändern von DNS.md-Einstellungen</br>Netzwerkschnittstellen konfigurieren |[Ändern der Gerätekonfiguration für Ihr StorSimple-Gerät](storsimple-8000-modify-device-config.md) |
| Anzeigen von Webproxyeinstellungen |[Konfigurieren des Webproxys für Ihr Gerät](storsimple-8000-configure-web-proxy.md) |
| Geräteadministratorkennwort ändern</br>Kennwort für StorSimple Snapshot Manager ändern |[Ändern von StorSimple-Kennwörtern](storsimple-8000-change-passwords.md) |
| Remoteverwaltung konfigurieren |[Herstellen einer Remoteverbindung mit dem StorSimple-Gerät](storsimple-8000-remote-connect.md) |
| Konfigurieren der Warnungseinstellungen |[Anzeigen und Verwalten von StorSimple-Warnungen](storsimple-8000-manage-alerts.md) |
| Konfigurieren von CHAP für Ihr StorSimple-Gerät |[Konfigurieren von CHAP für Ihr StorSimple-Gerät](./storsimple-8000-configure-chap.md) |
| Sicherungsrichtlinie hinzufügen</br>Hinzufügen oder Ändern eines Zeitplans</br>Löschen einer Sicherungsrichtlinie</br>Erstellen einer manuellen Sicherung</br>Erstellen einer benutzerdefinierten Sicherungsrichtlinie mit mehreren Volumes und Zeitplänen |[Verwalten von Sicherungsrichtlinien](storsimple-8000-manage-backup-policies-u2.md) |
| Gerätecontroller beenden</br>Gerätecontroller neu starten</br>Gerätecontroller herunterfahren</br>Gerät auf die Werkseinstellungen zurücksetzen</br>(nur bei lokalen Geräten) |[Verwalten des StorSimple-Gerätecontrollers](storsimple-8000-manage-device-controller.md) |
| Über StorSimple-Hardwarekomponenten informieren</br>Hardwarestatus überwachen</br>(nur bei lokalen Geräten) |[Überwachen der Hardwarekomponenten](storsimple-8000-monitor-hardware-status.md) |
| Unterstützungspaket erstellen |[Erstellen und Verwalten eines Unterstützungspakets](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Softwareupdates installieren |[Aktualisieren Ihres Geräts](storsimple-update-device.md) |

## <a name="next-steps"></a>Nächste Schritte

Informationen zu Problemen beim alltäglichen Betrieb Ihres StorSimple-Geräts oder zu Problemen mit dessen Hardwarekomponenten finden Sie hier:

* [Problembehandlung mithilfe des Diagnosetools](storsimple-8000-diagnostics.md)
* [StorSimple-Überwachungsindikatoren](storsimple-monitoring-indicators.md)

Falls sich die Probleme nicht beheben lassen und Sie eine Serviceanfrage erstellen müssen, finden Sie unter [Microsoft-Support kontaktieren](storsimple-8000-contact-microsoft-support.md)weitere Informationen.