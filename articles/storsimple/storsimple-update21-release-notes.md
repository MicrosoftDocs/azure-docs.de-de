---
title: Versionsanmerkungen zu Update 2.2 der StorSimple 8000-Serie | Microsoft Docs
description: Beschreibt die neuen Features sowie Probleme und Problemumgehungen für Update 2.2 der StorSimple 8000-Serie.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 73b9ecd03875b60ed2d9b9d4c8e8a3a0c8de3cfa
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956601"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Versionsanmerkungen zu Update 2.2 der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht
Die folgenden Versionsanmerkungen beschreiben die neuen Features und weisen auf wichtige offene Probleme bei Update 2.2 der StorSimple 8000-Serie hin. Es enthält auch eine Liste der StorSimple-Software-Updates, die in dieser Version enthalten sind.

Update 2.2 kann auf alle StorSimple-Geräte angewendet werden, auf denen Release (GA) oder Update 0.1 bis Update 2.1 ausgeführt wird. Update 2.2 ist die Geräteversion 6.3.9600.17708 zugeordnet.

Lesen Sie vor der Bereitstellung des Updates in Ihrer StorSimple-Lösung die Informationen in den Versionsanmerkungen sorgfältig durch.

> [!IMPORTANT]
> * Update 2.2 enthält nur Updates für die Software. Es dauert ungefähr 1,5-2 Stunden, dieses Update zu installieren. 
> * Wenn Sie Update 2.1 ausführen, wird empfohlen, dass Sie so bald wie möglich Update 2.2 anwenden.
> * Bei neuen Versionen werden Updates möglicherweise nicht sofort angezeigt, da diese in mehreren Phasen bereitgestellt werden. Warten Sie einige Tage, und suchen Sie dann erneut nach Updates, da diese bald verfügbar werden.
> 
> 

## <a name="whats-new-in-update-22"></a>Neuerungen in Update 2.2
Die folgenden wichtigen Verbesserungen wurden mit Update 2.2 durchgeführt.

* **Optimierte automatisierte Speicherplatzrückgewinnung** : Wenn Daten auf Volumes mit schlanker Speicherzuweisung gelöscht werden, müssen die nicht verwendeten Speicherblöcke wieder freigegeben werden. In dieser Version wurde der Prozess zur Speicherplatzrückgewinnung in der Cloud verbessert, was dazu führt, dass nicht genutzter Speicherplatz im Vergleich mit den vorherigen Versionen schneller wieder verfügbar ist.
* **Leistungssteigerungen der Momentaufnahme** : Update 2.2 hat die Verarbeitungsdauer einer Cloudmomentaufnahme in bestimmten Szenarien verbessert, in denen große Datenmengen verwendet werden und es nur geringe bis keine Datenänderungen gibt. Ein Szenario, das von dieser Erweiterung profitieren würde, wären die Archiv-Volumes.
* **Verstärkung der Supportpaketsammlung** : In dieser Version gibt es Verbesserungen im Hinblick auf das Sammeln und Hochladen des Supportpakets. 
* **Verbesserte Updatezuverlässigkeit** : In dieser Version sind Fehlerbehebungen enthalten, die zu einer verbesserten Updatezuverlässigkeit führen.

## <a name="issues-fixed-in-update-22"></a>Behobene Probleme in Update 2.2
Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in den Updates 2.2 und 2.1 behoben wurden.    

| Nein | Funktion | Problem | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- |
| 1 |Host-Leistung |In der früheren Version wurden die hostseitigen Leistungsprobleme während der Erstellung eines lokalen Volumes und während der Konvertierung eines mehrstufigen Volumes in ein lokales Volume erfasst. Diese Probleme wurden in dieser Version behoben, was zu einer Verbesserung der Hostleistung während der Erstellung des Volume und des Konvertierungsverfahrens führt. |Ja |Nein |
| 2 |Lokale Volumes |In seltenen Fällen stürzt das System beim Erstellen eines lokalen Volumes ab. Dieser Fehler wurde in dieser Version behoben. |Ja |Nein |
| 3 |Staffelung |Es gab sporadische Abstürze als die Metadaten für die StorSimple Cloud Appliances (8010 und 8020) in die Cloud ausgelagert wurden. Dieses Problem wurde in dieser Version behoben. |Nein |Ja |
| 4 |Erstellung der Momentaufnahme |Es gab Probleme im Zusammenhang mit der Erstellung von inkrementellen Momentaufnahmen in Szenarien mit hohem Datenvolumen und geringen oder gar keinen Datenänderungen. Diese Probleme wurden in dieser Version behoben. |Ja |Ja |
| 5 |Openstack-Authentifizierung |Benutzer, die Openstack als Clouddienstanbieter verwendet haben, stießen in der Vergangenheit auf einen seltenen Fehler, der auf die Authentifizierung zurückzuführen ist, bei der der JSON-Parser zu einem Absturz führte. Dieser Fehler wurde in dieser Version behoben. |Ja |Nein |
| 6 |Hostseitige Kopie |In früheren Versionen der Software trat ein seltene Fehler im Zusammenhang mit der ODX-Zeitmessung beim Kopieren von Daten von einem Volume auf ein anderes Volume auf. Dies führt zu einem Controller-Failover und das System wechselt möglicherweise in den Wiederherstellungsmodus. Dieser Fehler wurde in dieser Version behoben. |Ja |Nein |
| 7 |Windows-Verwaltungsinstrumentation (WMI) |In früheren Versionen der Software gab es mehrere Instanzen von Webproxyfehlern mit der Ausnahme „\<ManagementException> Fehler beim Laden des Anbieters“. Dieser Fehler wurde einem WMI-Speicherverlust zugeordnet und ist nun behoben. |Ja |Nein |
| 8 |Aktualisieren |Bei vorherigen Versionen der Software kam es gelegentlich vor, dass Benutzern beim Versuch, nach Updates zu suchen oder Updates zu installieren, der Fehler „CisPowershellHcsscripterror“ angezeigt wurde. Dieses Problem wurde in dieser Version behoben. |Ja |Ja |
| 9 |Supportpaket |In dieser Version gibt es Verbesserungen im Hinblick auf das Sammeln und Hochladen des Supportpakets. |Ja |Ja |

## <a name="known-issues-in-update-22"></a>Bekannte Probleme in Update 2.2
Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.

| Nein. | Funktion | Problem | Kommentare/Problemumgehung | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- | --- |
| 1 |Datenträgerquorum |In seltenen Fällen kann der Speicherpool offline geschaltet werden, wenn der Großteil der Datenträger im EBOD-Gehäuse eines 8600-Geräts getrennt wird, sodass kein Datenträgerquorum verfügbar ist. Der Speicherpool bleibt offline, auch wenn die Verbindung zu den Datenträgern wiederhergestellt wird. |Sie müssen das Gerät neu starten. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. |Ja |Nein |
| 2 |Falsche Controller-ID |Beim Austausch eines Controllers kann es vorkommen, dass Controller 0 als Controller 1 angezeigt wird. Während des Controlleraustauschs kann die Controller-ID anfänglich als ID des Peercontrollers angezeigt werden, wenn das Image vom Peerknoten geladen wurde. In seltenen Fällen kann dieses Verhalten auch nach einem Neustart des Systems auftreten. |Es ist keine Benutzeraktion erforderlich. Dieses Problem löst sich von selbst, nachdem der Controlleraustausch abgeschlossen ist. |Ja |Nein |
| 3 |Speicherkonten |Das Verwenden des Speicherdiensts zum Löschen des Speicherkontos wird nicht unterstützt. Dies führt dazu, dass keine Benutzerdaten abgerufen werden können. | |Ja |Ja |
| 4 |Gerätefailover |Mehrere Failover eines Volumecontainers von demselben Quellgerät auf verschiedene Zielgeräte werden nicht unterstützt. Das Failover von einem einzelnen nicht reagierenden Gerät auf mehrere Geräte führt dazu, dass die Volumecontainer auf dem ersten Gerät mit erfolgtem Failover die Dateneigentümerschaft verlieren. Wenn Sie diese Volumecontainer nach einem solchen Failover im klassischen Azure-Portal betrachten, werden sie anders angezeigt oder verhalten sie sich anders. | |Ja |Nein |
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
| 15 |Azure PowerShell-Cmdlets und lokale Volumes |Sie können über Azure PowerShell-Cmdlets kein lokales Volume erstellen. (Alle Volumes, die Sie mithilfe von Azure PowerShell erstellen, sind mehrstufig.) |Verwenden Sie immer den StorSimple Manager-Dienst, um lokale Volumes zu konfigurieren. |Ja |Nein |
| 16 |Für lokale Volumes verfügbarer Speicherplatz |Wenn Sie ein lokales Volume löschen, wird der für neue Volumes verfügbare Speicherplatz möglicherweise nicht sofort aktualisiert. Der StorSimple Manager-Dienst aktualisiert den verfügbaren lokalen Speicherplatz ungefähr stündlich. |Warten Sie eine Stunde, bevor Sie versuchen, das neue Volume zu erstellen. |Ja |Nein |
| 17 |Lokale Volumes |Der Wiederherstellungsauftrag macht die temporäre Momentaufnahmensicherung im Sicherungskatalog verfügbar, jedoch nur während der Dauer des Wiederherstellungsauftrags. Er macht außerdem eine Gruppe virtueller Datenträger mit dem Präfix **tmpCollection** auf der Seite **Sicherungsrichtlinien** verfügbar, jedoch nur während der Dauer des Wiederherstellungsauftrags. |Dieses Verhalten ist möglich, wenn der Wiederherstellungsauftrag nur lokale Volumes oder eine Mischung aus lokalen und mehrstufigen Volumes enthält. Wenn der Wiederherstellungsauftrag nur mehrstufige Volumes enthält, tritt dieses Verhalten nicht auf. Es ist kein Benutzereingriff erforderlich. |Ja |Nein |
| 18 |Lokale Volumes |Wenn Sie einen Wiederherstellungsauftrag abbrechen und direkt danach ein Controllerfailover auftritt, zeigt der Wiederherstellungsauftrag **Fehler** anstelle von **Abgebrochen** an. Wenn es bei einem Wiederherstellungsauftrag zu einem Fehler kommt und direkt danach ein Controllerfailover auftritt, zeigt der Wiederherstellungsauftrag **Abgebrochen** anstelle von **Fehler** an. |Dieses Verhalten ist möglich, wenn der Wiederherstellungsauftrag nur lokale Volumes oder eine Mischung aus lokalen und mehrstufigen Volumes enthält. Wenn der Wiederherstellungsauftrag nur mehrstufige Volumes enthält, tritt dieses Verhalten nicht auf. Es ist kein Benutzereingriff erforderlich. |Ja |Nein |
| 19 |Lokale Volumes |Wenn Sie einen Wiederherstellungsauftrag abbrechen oder es bei einer Wiederherstellung zu einem Fehler kommt und danach ein Controllerfailover auftritt, wird auf der Seite **Aufträge** ein zusätzlicher Wiederherstellungsauftrag angezeigt. |Dieses Verhalten ist möglich, wenn der Wiederherstellungsauftrag nur lokale Volumes oder eine Mischung aus lokalen und mehrstufigen Volumes enthält. Wenn der Wiederherstellungsauftrag nur mehrstufige Volumes enthält, tritt dieses Verhalten nicht auf. Es ist kein Benutzereingriff erforderlich. |Ja |Nein |
| 20 |Lokale Volumes |Wenn Sie versuchen, ein mehrstufiges Volume (erstellt und geklont mit Update 1.2 oder früher) in ein lokales Volume zu konvertieren, und das Gerät nicht genügend Speicherplatz bietet oder die Cloud nicht verfügbar ist, kann dies zu einer Beschädigung des Klons/der Klone führen. |Dieses Problem tritt nur bei Volumes auf, die mit einer Softwareversion vor Update 2.1 erstellt und geklont wurden. Dieses Szenario sollte eher selten vorkommen. | | |
| 21 |Volumekonvertierung |Aktualisieren Sie die einem Volume zugeordneten ACRs nicht, während eine Volumekonvertierung (mehrstufig in lokal oder umgekehrt) ausgeführt wird. Eine Aktualisierung der ACRs kann zur Datenbeschädigung führen. |Aktualisieren Sie die ACRs bei Bedarf vor der Volumekonvertierung, und nehmen Sie keine weiteren ACR-Updates vor, während die Konvertierung stattfindet. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Updates des Controllers und der Firmware in Update 2.2
Diese Version enthält nur-Software-Updates. Wenn Sie jedoch von einer Version vor Update 2 aktualisieren, müssen Sie Treiber-, Storport-, Spaceport- und (in einigen Fällen) Datenträgerfirmware-Updates auf Ihrem Gerät installieren.

Weitere Informationen zum Installieren von Treiber-, Storport-, Spaceport- und Datenträgerfirmware-Updates finden Sie unter [Installieren von Update 2.2](./storsimple-8000-install-update-5.md) auf Ihrem StorSimple-Gerät.

## <a name="virtual-device-updates-in-update-22"></a>Updates von virtuellen Geräten in Update 2.2
Dieses Update kann nicht auf das virtuelle Gerät angewendet werden. Es müssen neue virtuelle Geräte erstellt werden. 

## <a name="next-step"></a>Nächster Schritt
Erfahren Sie, wie Sie [Update 2.2 auf Ihrem StorSimple-Gerät installieren](./storsimple-8000-install-update-5.md) .