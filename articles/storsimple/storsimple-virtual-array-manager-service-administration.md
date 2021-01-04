---
title: Microsoft Azure StorSimple Manager Virtual Array-Verwaltung | Microsoft Docs
description: Erfahren Sie, wie Sie Ihr lokales StorSimple Virtual Array mithilfe des StorSimple-Geräte-Manager-Diensts im Azure-Portal verwalten.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 2395e3c6f2026bc1024de38122bc20959fdcf9b7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967090"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Verwalten des StorSimple Virtual Array mithilfe des StorSimple Geräte-Manager-Diensts
![Setup-Prozessablauf](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Übersicht
In diesem Artikel wird die Benutzeroberfläche des StorSimple-Geräte-Manager-Diensts beschrieben und erläutert, wie Sie eine Verbindung herstellen und welche Optionen verfügbar sind. Darüber hinaus enthält der Artikel Links zu den Workflows, die über diese Benutzeroberfläche ausgeführt werden können.

In diesem Artikel lernen Sie Folgendes:

* Herstellen einer Verbindung mit dem StorSimple-Geräte-Manager-Dienst
* Navigieren auf der Benutzeroberfläche des StorSimple-Geräte-Managers
* Verwalten Ihres StorSimple Virtual Array über den StorSimple-Geräte-Manager-Dienst

> [!NOTE]
> Die Verwaltungsoptionen, die für das Gerät der StorSimple 8000er Serie verfügbar sind, finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](./storsimple-8000-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Herstellen einer Verbindung mit dem StorSimple-Geräte-Manager-Dienst
Der StorSimple-Geräte-Manager-Dienst wird in Microsoft Azure ausgeführt. Er stellt Verbindungen mit mehreren StorSimple Virtual Arrays her. Diese Geräte werden über ein zentrales browserbasiertes Microsoft Azure-Portal verwaltet. Gehen Sie wie folgt vor, um eine Verbindung mit dem StorSimple-Geräte-Manager-Dienst herzustellen:

#### <a name="to-connect-to-the-service"></a>So stellen Sie eine Verbindung mit dem Dienst her
1. Wechseln Sie zu [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Melden Sie sich mit den Anmeldeinformationen Ihres Microsoft-Kontos beim Microsoft Azure-Portal (rechts oben) an.
3. Navigieren Sie zu „Durchsuchen“ > „Filter“ unter „StorSimple-Geräte-Manager“, um alle Geräte-Manager in einem bestimmten Abonnement anzuzeigen.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Verwenden des StorSimple-Geräte-Manager-Diensts zum Durchführen von Verwaltungsaufgaben
Die folgende Tabelle enthält eine Zusammenfassung aller allgemeinen Verwaltungsaufgaben und komplexen Workflows, die in der Benutzeroberfläche des Übersichtsblatts für den StorSimple-Geräte-Manager-Dienst ausgeführt werden können. Diese Aufgaben sind nach den Blättern organisiert, auf denen sie jeweils initiiert werden.

Klicken Sie auf die Verfahren in der Tabelle, um weitere Informationen zu den einzelnen Workflows zu erhalten.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple-Geräte-Manager-Workflows
| Gewünschte Aktion | Verfahren |
| --- | --- |
| Erstellen von Diensten</br>Löschen von Diensten</br>Abrufen des Dienstregistrierungsschlüssels</br>Neugenerieren des Dienstregistrierungsschlüssels |[Bereitstellen des StorSimple-Geräte-Manager-Diensts](storsimple-virtual-array-manage-service.md) |
| Anzeigen der Aktivitätsprotokolle |[Verwenden der StorSimple-Dienstübersicht](storsimple-virtual-array-service-summary.md) |
| Deaktivieren eines Virtual Arrays</br>Löschen eines Virtual Arrays |[Deaktivieren oder Löschen eines Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Notfallwiederherstellung und Gerätefailover</br>Failovervoraussetzungen</br>Business Continuity Disaster Recovery (BCDR)</br>Fehler während der Notfallwiederherstellung |[Notfallwiederherstellung und Gerätefailover für das StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| Sichern von Freigaben und Volumes</br>Erstellen einer manuellen Sicherung</br>Ändern des Sicherungszeitplans</br>Anzeigen vorhandener Sicherungen |[Sichern des StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| Klonen von Freigaben aus einem Sicherungssatz</br>Klonen von Volumes aus einem Sicherungssatz</br>Wiederherstellung auf Elementebene (nur Dateiserver) |[Klonen aus einer Sicherung des StorSimple Virtual Array](storsimple-virtual-array-clone.md) |
| Informationen zu Speicherkonten</br>Hinzufügen von Speicherkonten</br>Bearbeiten eines Speicherkontos</br>Löschen von Speicherkonten |[Verwalten von Speicherkonten für das StorSimple Virtual Array](storsimple-virtual-array-manage-storage-accounts.md) |
| Informationen zu Access Control-Datensätzen</br>Hinzufügen oder Ändern von Access Control-Datensätzen </br>Löschen von Access Control-Datensätzen |[Verwalten von Access Control-Datensätzen für das StorSimple Virtual Array](storsimple-virtual-array-manage-acrs.md) |
| Anzeigen von Auftragsdetails |[Verwalten von StorSimple Virtual Array-Aufträgen](storsimple-virtual-array-manage-jobs.md) |
| Konfigurieren der Warnungseinstellungen</br>Warnungsbenachrichtigungen empfangen</br>Warnungen verwalten</br>Überprüfen von Warnungen |[Anzeigen und Verwalten von Warnungen für das StorSimple Virtual Array](storsimple-virtual-array-manage-alerts.md) |
| Ändern des Geräteadministratorkennworts |[Ändern des StorSimple Virtual Array-Geräteadministratorkennworts](storsimple-virtual-array-change-device-admin-password.md) |
| Softwareupdates installieren |[Virtual Array aktualisieren](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Verwenden Sie die [lokale Webbenutzeroberfläche](storsimple-ova-web-ui-admin.md) für die folgenden Aufgaben:
> 
> * [Verschlüsselungsschlüssel für Dienstdaten abrufen](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Unterstützungspaket erstellen](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Virtual Array beenden und neu starten](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Informationen zur Webbenutzeroberfläche und ihrer Verwendung finden Sie unter [Verwalten des StorSimple Virtual Arrays über die StorSimple-Webbenutzeroberfläche](storsimple-ova-web-ui-admin.md).