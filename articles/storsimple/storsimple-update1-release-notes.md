---
title: Versionsanmerkungen zu Update 1.2 der StorSimple 8000-Serie | Microsoft Docs
description: Beschreibt die neuen Features sowie Probleme und Problemumgehungen für Update 1.2 der StorSimple 8000-Serie.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a5ba4b27ae19999237edb75c50db36ef57a10f1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956686"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Versionshinweise zu Update 1.2 für Geräte der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht
Die folgenden Versionsanmerkungen beschreiben die neuen Features und weisen auf wichtige offene Punkte bei Update 1.2 der StorSimple 8000-Serie hin. Es enthält auch eine Liste der Updates für StorSimple-Software, -Treiber und -Datenträgerfirmware, die in dieser Version enthalten sind. 

Update 1.2 kann auf alle StorSimple-Geräte angewendet werden, auf denen Software vom Typ Release (GA), Update 0.1, Update 0.2 oder Update 0.3 ausgeführt wird. Update 1.2 ist nicht verfügbar, wenn auf Ihrem Gerät Update 1 oder Update 1.1 ausgeführt wird. Wenden Sie sich an den [Microsoft Support](./storsimple-8000-contact-microsoft-support.md) , wenn auf dem Gerät Release (GA) ausgeführt wird, um Hilfe bei der Installation dieses Updates zu erhalten.

Die folgende Tabelle enthält die Gerätesoftwareversionen, die den Updates 1, 1.1 und 1.2 entsprechen.

| Ausgeführtes Update | Passende Gerätesoftwareversion |
| --- | --- |
| Update 1.2 |6.3.9600.17584 |
| Update 1.1 |6.3.9600.17521 |
| Update 1.0 |6.3.9600.17491 |

Lesen Sie vor der Bereitstellung des Updates in Ihrer StorSimple-Lösung die Informationen in den Versionsanmerkungen sorgfältig durch. Weitere Informationen finden Sie unter [Installieren von Update 1.2 auf dem StorSimple-Gerät](./storsimple-8000-install-update-5.md). 

> [!IMPORTANT]
> * Die Installation dieses Updates (einschließlich der Windows-Updates) dauert ungefähr 5 bis 10 Stunden. 
> * Update 1.2 verfügt über Updates für Software, LSI-Treiber und Datenträgerfirmware. Führen Sie zum Installieren die Anleitung unter [Installieren von Update 1.2 auf Ihrem StorSimple-Gerät aus](./storsimple-8000-install-update-5.md).
> * Bei neuen Versionen werden Updates möglicherweise nicht sofort angezeigt, da diese in mehreren Phasen bereitgestellt werden. Suchen Sie in einigen Tagen erneut nach Updates, da diese bald verfügbar sind.
> 
> 

## <a name="whats-new-in-update-12"></a>Neuerungen in Update 1.2
Diese Features wurden zuerst mit Update 1 veröffentlicht, das für eine begrenzte Gruppe von Benutzern bereitgestellt wurde. Nach der Veröffentlichung von Update 1.2 kommen die meisten StorSimple-Benutzer in den Genuss der folgenden neuen Features und Verbesserungen:

* **Migration von Geräten der Serien 5000-7000 zu Geräten der Serie 8000** – Die vorliegende Version enthält eine neue Migrationsfunktion, die den Benutzern von Geräten der StorSimple-Serien 5000-7000 die Migration der Daten zu einem physisches Gerät der StorSimple 8000-Serie oder einem virtuellen Gerät ermöglicht. Das Migrationsfeature bietet zwei wichtige Nutzenversprechen:                                                                  
  
  * **Geschäftskontinuität**, durch Ermöglichen der Migration von vorhandenen Daten auf Geräten der 5000-7000 Serie auf Geräte der 8000 Serie.
  * **Verbesserte Featureangebote von Geräten der 8000 Serie**, wie z.B. effiziente zentralisierte Verwaltung mehrerer Geräte durch den StorSimple Manager-Dienst, hochwertigere Hardware und aktualisierte Firmware, virtuelle Geräte, Datenmobilität und Features in der zukünftigen Roadmap.
    
    Ausführliche Informationen zur Durchführung der Migration von einem Gerät der 5000-7000 Serie auf ein Gerät der 8000 Serie finden Sie im [Migrationshandbuch](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) . 
* **Verfügbarkeit im Azure-Portal für Behörden** – StorSimple ist jetzt im Azure-Portal für Behörden verfügbar. Erfahren Sie mehr über die [Bereitstellung eines StorSimple-Geräts im Azure-Portal für Behörden](./index.yml).
* **Unterstützung weiterer Cloud-Dienstanbieter** – Die weiteren unterstützten Cloud-Dienstanbieter sind Amazon S3, Amazon S3 mit RRS, HP und OpenStack (Beta).
* **Update auf die neuesten Storage-APIs** – Mit dieser Version wurde StorSimple auf die neuesten Azure Storage-Dienst-APIs aktualisiert. Geräte der StorSimple 8000-Serie, auf denen ältere Softwareversionen als Update 1 ausgeführt werden (Release, 0.1, 0.2 und 0.3), verwenden Versionen der Azure-Speicherdienst-APIs, die vor dem 17. Juli 2009 liegen. Wie in der aktualisierten [Ankündigung zum Ablösen von Storage-Dienstversionen](/archive/blogs/windowsazurestorage/microsoft-azure-storage-service-version-removal-update-extension-to-2016) bekannt gegeben, werden diese APIs bis zum 1. August 2016 als veraltet markiert. Es ist zwingend erforderlich, Update 1 für die StorSimple 8000-Serie vor dem 1. August 2016 anzuwenden. Andernfalls funktionieren StorSimple-Geräte nicht mehr ordnungsgemäß.
* **Unterstützung von zonenredundantem Speicher (ZRS)** – Durch das Upgrade auf die neueste Version der Speicher-APIs unterstützt die StorSimple 8000 Serie außer lokal redundantem Speicher (LRS) und georedundantem Speicher (GRS) auch zonenredundanten Speicher (ZRS). Ausführliche Informationen über zonenredundanten Speicher (ZRS) finden Sie in diesem [Artikel über Redundanzoptionen von Azure Storage](../storage/common/storage-redundancy.md) .
* **Verbesserte Bereitstellungs-und Update-Erfahrung** – In dieser Version wurden die Installations- und Update-Prozesse verbessert. Die Installation durch den Setup-Assistenten wurde verbessert, um dem Benutzer Feedback bereitzustellen, wenn Netzwerkkonfiguration und Firewall-Einstellungen nicht korrekt sind. Es wurden zusätzliche Diagnose-Cmdlets bereitgestellt, um Sie bei der Problembehandlung bei der Vernetzung des Geräts zu unterstützen. Weitere Informationen zu den neuen Diagnose-Cmdlets für die Problembehandlung finden Sie im [Artikel über die Problembehandlung bei der Bereitstellung](./storsimple-8000-troubleshoot-deployment.md) verwendet.

## <a name="issues-fixed-in-update-12"></a>Behobene Probleme in Update 1.2
Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in den Updates 1.2, 1.1 und 1 behoben wurden.    

| Nein. | Funktion | Problem | Im Update behoben | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell für StorSimple |Beim Remotezugriff eines Benutzers auf das StorSimple-Gerät mit Windows PowerShell für StorSimple und anschließendem Starten des Setup-Assistenten kam es zu einem Absturz, wenn die IP-Adresse von Data 0 eingegeben wurde. Dieses Problem wurde in Update 1 behoben. |Update 1 |Ja |Ja |
| 2 |Zurücksetzen auf Werkseinstellungen |Unter bestimmten Umständen kann sich das StorSimple-Gerät beim Zurücksetzen auf die Werkseinstellungen aufhängen und zeigt dann die folgende Nachricht an: **Zurücksetzung auf Werkseinstellungen wird ausgeführt (Phase 8)** . Dies kann vorkommen, wenn Sie STRG+C drücken, während das Cmdlet ausgeführt wird. Dieses Problem wurde jetzt behoben. |Update 1 |Ja |Nein |
| 3 |Zurücksetzen auf Werkseinstellungen |Nach der fehlgeschlagenen Zurücksetzung einer Dual-Controller-Factory auf die Werkseinstellungen konnte die Geräteregistrierung fortgesetzt werden. Dies führte zu einer nicht unterstützten Systemkonfiguration. In Update 1 wird eine Fehlermeldung angezeigt, und auf einem Gerät, das eine fehlgeschlagene Factory-Zurücksetzung aufweist, wird die Registrierung blockiert. |Update 1 |Ja |Nein |
| 4 |Zurücksetzen auf Werkseinstellungen |In einigen Fällen wurden falsch positive Warnungen aufgrund fehlender Übereinstimmung ausgelöst. Falsche Warnungen aufgrund fehlender Übereinstimmung werden auf Geräten mit Update 1 nicht mehr generiert. |Update 1 |Ja |Nein |
| 5 |Zurücksetzen auf Werkseinstellungen |Wenn die Zurücksetzung auf Werkseinstellungen vor der vollständigen Ausführung unterbrochen wurde, wechselte das Gerät in den Wiederherstellungsmodus und der Zugriff auf Windows PowerShell für StorSimple war nicht mehr möglich. Dieses Problem wurde jetzt behoben. |Update 1 |Ja |Nein |
| 6 |Notfallwiederherstellung |Ein Fehler der Notfallwiederherstellung wurde behoben, der ein Fehlschlagen der Notfallwiederherstellung beim Wiederherstellen von Sicherungen auf dem Zielgerät verursacht hat. |Update 1 |Ja |Ja |
| 7 |Überwachungs-LEDs |In einigen Fällen zeigten Überwachungs-LEDs auf der Geräterückseite nicht den korrekten Status an. Die blaue LED wurde ausgeschaltet. DATA 0- und DATA 1-LEDs blinkten, selbst wenn diese Schnittstellen nicht konfiguriert waren. Das Problem wurde behoben, und die Überwachungs-LEDs zeigen jetzt den korrekten Status an. |Update 1 |Ja |Nein |
| 8 |Überwachungs-LEDs |In bestimmten Fällen wurde das blaue Licht am aktiven Controller nach dem Anwenden von Update 1 ausgeschaltet, wodurch die Identifizierung des aktiven Controllers erschwert wurde. Dieses Problem wurde in dieser Patchversion behoben. |Update 1.2 |Ja |Nein |
| 9 |Netzwerkschnittstellen |In früheren Versionen konnte ein StorSimple-Gerät, das mit einem nicht routbaren Gateway konfiguriert war, offline geschaltet werden. In dieser Version wurde für DATA 0 die niedrigste Routingmetrik festgelegt. Daher wird selbst bei Cloud-Aktivierung anderer Schnittstellen der gesamte Cloud-Datenverkehr vom Gerät über DATA 0 geleitet. |Update 1 |Ja |Ja |
| 10 |Backups |Ein Fehler in Update 1, der zum Fehlschlagen von Sicherungen nach 24 Tagen führte, wurde in der Patchversion Update 1.1 behoben. |Update 1.1 |Ja |Ja |
| 11 |Backups |Ein Fehler in vorherigen Versionen führte zu einer schlechten Leistung für Cloudmomentaufnahmen mit niedrigen Änderungsraten. Dieser Fehler wurde in dieser Patchversion behoben. |Update 1.2 |Ja |Ja |
| 12 |Aktualisierungen |Ein Fehler in Update 1, bei dem ein Upgradefehler gemeldet wurde und dazu führte, dass die Controller in den Wiederherstellungsmodus wechselten, wurde in dieser Patchversion behoben. |Update 1.2 |Ja |Ja |

## <a name="known-issues-in-update-12"></a>Bekannte Probleme in Update 1.2
Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.

| Nein. | Funktion | Problem | Kommentare/Problemumgehung | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- | --- |
| 1 |Datenträgerquorum |In seltenen Fällen kann der Speicherpool offline geschaltet werden, wenn der Großteil der Datenträger im EBOD-Gehäuse eines 8600-Geräts getrennt wird, sodass kein Datenträgerquorum verfügbar ist. Der Speicherpool bleibt offline, auch wenn die Verbindung zu den Datenträgern wiederhergestellt wird. |Sie müssen das Gerät neu starten. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. |Ja |Nein |
| 2 |Falsche Controller-ID |Beim Austausch eines Controllers kann es vorkommen, dass Controller 0 als Controller 1 angezeigt wird. Während des Controlleraustauschs kann die Controller-ID anfänglich als ID des Peercontrollers angezeigt werden, wenn das Image vom Peerknoten geladen wurde. In seltenen Fällen kann dieses Verhalten auch nach einem Neustart des Systems auftreten. |Es ist keine Benutzeraktion erforderlich. Dieses Problem löst sich von selbst, nachdem der Controlleraustausch abgeschlossen ist. |Ja |Nein |
| 3 |Speicherkonten |Das Verwenden des Speicherdiensts zum Löschen des Speicherkontos wird nicht unterstützt. Dies führt dazu, dass keine Benutzerdaten abgerufen werden können. |Ja |Ja | |
| 4 |Gerätefailover |Mehrere Failover eines Volumecontainers von demselben Quellgerät auf verschiedene Zielgeräte werden nicht unterstützt. Das Gerätefailover von einem einzelnen nicht reagierenden Gerät auf mehrere Geräte führt dazu, dass die Volumecontainer auf dem ersten Gerät mit erfolgtem Failover die Dateneigentümerschaft verlieren. Wenn Sie diese Volumecontainer nach einem solchen Failover im klassischen Azure-Portal betrachten, werden sie anders angezeigt oder verhalten sie sich anders. | |Ja |Nein |
| 5 |Installation |Während der Installation von StorSimple-Adapter für SharePoint müssen Sie die IP-Adresse eines Geräts angeben, damit die Installation erfolgreich abgeschlossen wird. | |Ja |Nein |
| 6 |Webproxy |Wenn Ihre Webproxykonfiguration das Protokoll "HTTPS" verwendet, ist die Kommunikation zwischen dem Gerät und dem Dienst beeinträchtigt, und das Gerät wird offline geschaltet. Supportpakete werden bei diesem Vorgang ebenfalls generiert. Sie beanspruchen auf Ihrem Gerät erhebliche Ressourcen. |Stellen Sie sicher, dass "HTTP" als Protokoll für die Webproxy-URL angegeben ist. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](./storsimple-8000-configure-web-proxy.md). |Ja |Nein |
| 7 |Webproxy |Wenn Sie den Webproxy für ein registriertes Gerät konfigurieren und aktivieren, müssen Sie den aktiven Controller auf Ihrem Gerät neu starten. | |Ja |Nein |
| 8 |Hohe Cloud-Latenzen und hohe E/A-Arbeitsauslastung |Wenn Ihr StorSimple-Gerät mit einer Kombination aus sehr hohen Cloud-Latenzen (mehrere Sekunden) und hoher E/A-Arbeitsauslastung konfrontiert wird, verschlechtert sich die Leistung der Gerätevolumes, und es tritt ggf. der E/A-Fehler "Gerät nicht bereit" auf. |Sie müssen die Gerätecontroller manuell neu starten oder ein Gerätefailover ausführen, um dieses Problem zu beheben. |Ja |Nein |
| 9 |Azure PowerShell |Bei Verwendung des StorSimple-Cmdlets **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait**, um das erste Objekt zu markieren, damit ein neues **Volumecontainer**-Objekt erstellt werden kann, gibt das Cmdlet alle Objekte zurück. |Schließen Sie das Cmdlet wie folgt in Klammern ein: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Ja |Ja |
| 10 |Migration |Wenn mehrere Volumecontainer für die Migration übergeben werden, ist der ETA für die neueste Sicherung nur für den ersten Volumecontainer korrekt. Außerdem tritt nach erfolgter Migration der ersten 4 Sicherungen im ersten Volumecontainer Parallelmigration auf. |Es wird empfohlen, immer nur einen Volumecontainer gleichzeitig zu migrieren. |Ja |Nein |
| 11 |Migration |Nach der Wiederherstellung werden Volumes nicht der Sicherungsrichtlinie oder dem virtuellen Datenträger hinzugefügt. |Zum Erstellen von Sicherungen müssen diese Volumes einer Sicherungsrichtlinie hinzugefügt werden. |Ja |Ja |
| 12 |Migration |Nach Abschluss die Migration darf das Gerät der 5000/7000 Serie nicht auf die migrierten Datencontainer zugreifen. |Es wird empfohlen, die migrierten Datencontainer zu löschen, nachdem die Migration vollständig abgeschlossen ist. |Ja |Nein |
| 13 |Klonen und Notfallwiederherstellung |Ein StorSimple-Gerät mit Update 1 kann für ein Gerät, auf dem ältere Software als Update 1 ausgeführt wird, keinen Klonvorgang bzw. keine Notfallwiederherstellung durchführen. |Das Zielgerät muss auf Update 1 aktualisiert werden, damit diese Operationen ausgeführt werden können. |Ja |Ja |
| 14 |Migration |Die Konfigurationssicherung für die Migration kann auf einem Gerät der Serie 5000-7000 fehlschlagen, wenn Volumegruppen ohne zugehörige Volumes vorhanden sind. |Löschen Sie die leeren Volumegruppen ohne zugehörige Volumes, und wiederholen Sie dann die Konfigurationssicherung. |Ja |Nein |

## <a name="physical-device-updates-in-update-12"></a>Updates von physischen Geräten in Update 1.2
Wenn Patch-Update 1.2 auf ein physisches Gerät (mit einer älteren Version als Update 1) angewendet wird, wird die Softwareversion in 6.3.9600.17584 geändert.

## <a name="controller-and-firmware-updates-in-update-12"></a>Updates des Controllers und der Firmware in Update 1.2
Mit dieser Version werden der Treiber und die Datenträgerfirmware auf Ihrem Gerät aktualisiert.

* Weitere Informationen zum SAS-Controller-Update finden Sie unter [Update 1 für LSI-SAS-Controller im Microsoft Azure StorSimple-Gerät](https://support.microsoft.com/kb/3043005). 
* Weitere Informationen zum Datenträger-Firmware-Update finden Sie unter [Datenträger-Firmware-Update 1 für das Microsoft Azure StorSimple-Gerät](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Updates von virtuellen Geräten in Update 1.2
Dieses Update kann nicht auf das virtuelle Gerät angewendet werden. Es müssen neue virtuelle Geräte erstellt werden. 

## <a name="next-steps"></a>Nächste Schritte
* [Installieren Sie Update 1.2 auf dem Gerät](./storsimple-8000-install-update-5.md).