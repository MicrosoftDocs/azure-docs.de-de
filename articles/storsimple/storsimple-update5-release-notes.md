---
title: Versionshinweise zu Update 5 der StorSimple 8000-Serie
description: Beschreibt die neuen Features sowie Probleme und Problemumgehungen für Update 5 der StorSimple 8000-Serie.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021043"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Versionshinweise zu Update 5 der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht

Die folgenden Versionshinweise beschreiben die neuen Features und weisen auf wichtige offene Punkte bei Update 5 der StorSimple 8000-Serie hin. Es enthält auch eine Liste der StorSimple-Software-Updates, die in dieser Version enthalten sind.

Update 5 kann auf alle StorSimple-Geräte angewendet werden, auf denen Update 0.1 bis Update 4 ausgeführt wird. Update 5 ist die Geräteversion 6.3.9600.17845 zugeordnet.

Lesen Sie vor der Bereitstellung des Updates in Ihrer StorSimple-Lösung die Informationen in den Versionsanmerkungen sorgfältig durch.

> [!IMPORTANT]
> * Update 5 ist ein obligatorisches Update und muss sofort installiert werden. Weitere Informationen finden Sie unter [Anwenden von Update 5](storsimple-8000-install-update-5.md).
> * Update 5 umfasst Gerätesoftware, Datenträgerfirmware, Betriebssystemsicherheit und andere Betriebssystemupdates. Es dauert ungefähr 4 Stunden, dieses Update zu installieren. Ein Firmwareupdate auf dem Datenträger ist ein Update, das zu einer Unterbrechung und zu einer Ausfallzeit Ihres Geräts führt. Wir empfehlen Ihnen die Anwendung von Update 5, um Ihr Gerät auf dem neuesten Stand zu halten.
> * Bei neuen Versionen werden Updates möglicherweise nicht sofort angezeigt, da diese in mehreren Phasen bereitgestellt werden. Warten Sie einige Tage, und suchen Sie dann erneut nach Updates, da diese bald verfügbar werden.

## <a name="whats-new-in-update-5"></a>Neuerungen in Update 5

Die folgenden wichtigen Verbesserungen und Fehlerbehebungen wurden in Update 5 vorgenommen.

* **Verwenden von Azure Active Directory (AAD) zum Authentifizieren beim StorSimple-Geräte-Manager-Dienst** – ab Update 5 wird Azure Active Directory für die Authentifizierung beim StorSimple-Geräte-Manager-Dienst verwendet. Der alte Authentifizierungsmechanismus wird ab Dezember 2017 als veraltet markiert. Alle Benutzer müssen die neuen Authentifizierungs-URLs in ihre Firewallregeln aufnehmen. Weitere Informationen finden Sie unter den [Authentifizierungs-URLs, die in den Netzwerkanforderungen für das StorSimple-Gerät aufgeführt werden](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Wenn die Authentifizierungs-URL nicht in den Firewallregeln enthalten ist, wird Benutzern eine kritische Warnung darüber angezeigt, dass ihr StorSimple-Gerät sich nicht beim Dienst authentifizieren konnte. Wenn die Benutzer diese Warnung erhalten, müssen sie die neue Authentifizierungs-URL aufnehmen. Weitere Informationen finden Sie unter [StorSimple-Netzwerkwarnungen](storsimple-8000-manage-alerts.md#networking-alerts).

* **Neue Version von StorSimple Snapshot Manager:** Mit Update 5 wird eine neue Version von StorSimple Snapshot Manager veröffentlicht. Sie ist mit allen StorSimple-Geräten mit Update 4 oder höher kompatibel. Es wird empfohlen, auf diese Version zu aktualisieren. Die vorherige Version von StorSimple Snapshot Manager wird für StorSimple-Geräte mit Update 3 oder einer älteren Version verwendet. [Laden Sie die entsprechende Version von StorSimple Snapshot Manager herunter](https://www.microsoft.com/en-us/download/details.aspx?id=44220), und lesen Sie die Informationen zum [Bereitstellen von StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Behobene Probleme in Update 5

Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in Update 5 behoben wurden.

| Nein | Funktion | Problem | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell-Remotenutzung |In der vorherigen Version erhielten Benutzer eine Fehlermeldung, wenn sie versuchten, über Windows PowerShell eine Remoteverbindung mit der StorSimple Cloud Appliance herzustellen. Dieses Problem gibt es in dieser Version nicht mehr. |Nein |Ja |
| 2 |Bandbreitenvorlagen |In früheren Versionen gab es ein Problem mit Bandbreitenvorlagen, das zu einer geringeren Bandbreite führte, als für das Gerät konfiguriert war. Dieses Problem wurde in dieser Version behoben. |Ja |Ja |
| 3 |Failover |Wenn in vorherigen Versionen ein Gerät mit einer großen Anzahl von Volumes per Failover an ein anderes Gerät mit Update 4 übergeben wurde, trat beim Anwenden der Zugriffssteuerungsdatensätze ein Fehler auf. Dieses Problem wurde in dieser Version behoben. |Ja |Ja |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Bekannte Probleme in Update 5 aus vorherigen Versionen

Es gibt keine neuen bekannten Probleme in Update 5. Eine Liste der Probleme, die aus vorherigen Versionen in Update 5 übertragen wurden, finden Sie unter [Versionshinweise zu Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Aktualisierungen des SAS-Controllers (Serial Attached SCSI) und der Firmware in Update 5

Diese Version enthält SAS-Controller, LSI-Treiber und Firmwareupdates. Weitere Informationen zum Installieren dieser Updates finden Sie auf Ihrem StorSimple-Gerät unter [Installieren von Update 5](storsimple-8000-install-update-5.md).

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aktualisierungen an der StorSimple Cloud Appliance in Update 5

Dieses Update gilt nicht für die StorSimple Cloud Appliance (auch bekannt als das virtuelle Gerät). Neue Cloudgeräte müssen mit dem Update 5-Image erstellt werden. Weitere Informationen zum Erstellen einer StorSimple Cloud Appliance finden Sie unter [Bereitstellen und Verwalten einer StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Nächster Schritt

Erfahren Sie, wie Sie [Update 5 auf Ihrem StorSimple-Gerät installieren](storsimple-8000-install-update-5.md).

