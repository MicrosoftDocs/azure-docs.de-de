---
title: Versionsanmerkungen zu Update 3 der StorSimple 8000-Serie
description: Beschreibt die neuen Features sowie Probleme und Problemumgehungen für Update 3 der StorSimple 8000-Serie.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e081acc357c1b8872dd3a25c552d4cbe9785e212
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007068"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Versionshinweise zu Update 3 für Geräte der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht
Die folgenden Versionsanmerkungen beschreiben die neuen Features und weisen auf wichtige offene Punkte bei Update 3 der StorSimple 8000-Serie hin. Es enthält auch eine Liste der StorSimple-Software-Updates, die in dieser Version enthalten sind. 

Update 3 kann auf alle StorSimple-Geräte angewendet werden, auf denen die GA-Version oder Update 0.1 bis Update 2.2 ausgeführt wird. Update 3 ist die Geräteversion 6.3.9600.17759 zugeordnet.

Lesen Sie vor der Bereitstellung des Updates in Ihrer StorSimple-Lösung die Informationen in den Versionsanmerkungen sorgfältig durch.

> [!IMPORTANT]
> * Update 3 bietet Updates für die Gerätesoftware, LSI-Treiber und -Firmware, Storport und Spaceport. Es dauert ungefähr 1,5-2 Stunden, dieses Update zu installieren. 
> * Bei neuen Versionen werden Updates möglicherweise nicht sofort angezeigt, da diese in mehreren Phasen bereitgestellt werden. Warten Sie einige Tage, und suchen Sie dann erneut nach Updates, da diese bald verfügbar werden.
> 
> 

## <a name="whats-new-in-update-3"></a>Neuerungen in Update 3
Die folgenden wichtigen Verbesserungen und Fehlerbehebungen sind an Update 3 erfolgt.

* **Automatisierte Speicherplatzrückgewinnung** : Ab Update 3 werden auf dem Standbycontroller des Systems Algorithmen zur Speicherplatzrückgewinnung ausgeführt, was zu einer schnelleren Ausführung führt. Weitere Informationen zu den Ports, die zum Arbeiten mit Speicherplatzrückgewinnung erforderlich sind, finden Sie unter [StorSimple-Netzwerkanforderungen](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Leistungsverbesserungen** : Update 3 bietet eine verbesserte Lese-/Schreibleistung in der Cloud.
* **Migrationsbezogene Verbesserungen** : In dieser Version sind verschiedene Fehlerbehebungen und Verbesserungen an der Migration von Geräten der 5000er/7000er Serie auf Geräte der 8000er Serie erfolgt. Weitere Informationen zur Verwendung der Migrationsfunktion finden Sie unter [Migration von Geräten der 5000er/7000er Serie auf Geräte der 8000er Serie](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Überwachungsbezogene Korrekturen** : In dieser Version wurden Fehler im Zusammenhang mit Überwachungsdiagrammen, dem Dienstdashboard und dem Gerätedashboard korrigiert.

## <a name="issues-fixed-in-update-3"></a>Behobene Probleme in Update 3
Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in Update 3 behoben wurden.    

| Nein | Funktion | Problem | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- |
| 1 |Hostseitige Datenmigration |In der vorherigen Version wurde die StorSimple Cloud Appliance während einer hostseitigen Datenmigration offline geschaltet. Dieses Problem wurde in dieser Version behoben. |Nein |Ja |
| 2 |Lokale Volumes |In der vorherigen Version gab es bei lokalen Volumes Probleme im Zusammenhang mit E/A-, Volumekonvertierungs- und Datenpfadfehlern. Diese Probleme gibt es in dieser Version nicht mehr. |Ja |Nein |
| 3 |Überwachung |Es gab mehrere Probleme im Zusammenhang mit dem Melden von Einheiten und der Überwachung sowie mit Diagrammen im Gerätedashboard, da für lokale Volumes falsche Informationen angezeigt wurden. Diese Probleme wurden in dieser Version behoben. |Ja |Nein |
| 4 |Schreibintensive E/A-Vorgänge |Wenn StorSimple für Workloads mit vielen Schreibvorgängen verwendet wird, konnte gelegentlich ein Fehler auftreten, bei dem der Arbeitssatz in der Cloud abgestuft wurde. Dieser Fehler wurde in dieser Version behoben. |Ja |Ja |
| 5 |Backup |Wenn ein Benutzer in den früheren Versionen der Software eine Sicherung eines Remoteklons erstellte, konnte es in seltenen Fällen zu Cloudfehlern kommen, die ein Timeout des Vorgangs verursachten. In dieser Version wurde das Problem behoben, sodass der Vorgang erfolgreich abgeschlossen wird. |Ja |Ja |
| 6 |Sicherungsrichtlinie |In einigen seltenen Fällen trat in den früheren Versionen der Software ein Fehler im Zusammenhang mit dem Löschen der Sicherungsrichtlinie auf. Dieses Problem wurde in dieser Version behoben. |Ja |Ja |

## <a name="known-issues-in-update-3"></a>Bekannte Probleme in Update 3
Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme in dieser Version.

| Nein. | Funktion | Problem | Kommentare/Problemumgehung | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- | --- |
| 1 |Datenträgerquorum |In seltenen Fällen kann der Speicherpool offline geschaltet werden, wenn der Großteil der Datenträger im EBOD-Gehäuse eines 8600-Geräts getrennt wird, sodass kein Datenträgerquorum verfügbar ist. Der Speicherpool bleibt offline, auch wenn die Verbindung zu den Datenträgern wiederhergestellt wird. |Sie müssen das Gerät neu starten. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support, um Informationen zu den nächsten Schritten zu erhalten. |Ja |Nein |
| 2 |Falsche Controller-ID |Beim Austausch eines Controllers kann es vorkommen, dass Controller 0 als Controller 1 angezeigt wird. Während des Controlleraustauschs kann die Controller-ID anfänglich als ID des Peercontrollers angezeigt werden, wenn das Image vom Peerknoten geladen wurde. In seltenen Fällen kann dieses Verhalten auch nach einem Neustart des Systems auftreten. |Es ist keine Benutzeraktion erforderlich. Dieses Problem löst sich von selbst, nachdem der Controlleraustausch abgeschlossen ist. |Ja |Nein |
| 3 |Speicherkonten |Das Verwenden des Speicherdiensts zum Löschen des Speicherkontos wird nicht unterstützt. Dies führt dazu, dass keine Benutzerdaten abgerufen werden können. | |Ja |Ja |
| 4 |Gerätefailover |Mehrere Failover eines Volumecontainers von demselben Quellgerät auf verschiedene Zielgeräte werden nicht unterstützt. Das Failover von einem einzelnen nicht reagierenden Gerät auf mehrere Geräte führt dazu, dass die Volumecontainer auf dem ersten Gerät mit erfolgtem Failover die Dateneigentümerschaft verlieren. Wenn Sie diese Volumecontainer nach einem solchen Failover im klassischen Azure-Portal betrachten, werden sie anders angezeigt oder verhalten sie sich anders. | |Ja |Nein |
| 5 |Installation |Während der Installation von StorSimple-Adapter für SharePoint müssen Sie die IP-Adresse eines Geräts angeben, damit die Installation erfolgreich abgeschlossen wird. | |Ja |Nein |
| 6 |Webproxy |Wenn Ihre Webproxykonfiguration das Protokoll "HTTPS" verwendet, ist die Kommunikation zwischen dem Gerät und dem Dienst beeinträchtigt, und das Gerät wird offline geschaltet. Supportpakete werden bei diesem Vorgang ebenfalls generiert. Sie beanspruchen auf Ihrem Gerät erhebliche Ressourcen. |Stellen Sie sicher, dass "HTTP" als Protokoll für die Webproxy-URL angegeben ist. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](storsimple-8000-configure-web-proxy.md). |Ja |Nein |
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
| 22 |Aktualisierungen |Beim Anwenden von Update 3 wird auf der Seite **Wartung** im klassischen Azure-Portal die folgende Meldung im Zusammenhang mit Update 2 angezeigt: „Update 2 für die StorSimple 8000-Serie ermöglicht es, dass Microsoft proaktiv Protokollinformationen von Ihrem Gerät erfassen kann, wenn potenzielle Probleme erkannt werden“. Diese Meldung ist irreführend, da sie impliziert, dass das Gerät auf Update 2 aktualisiert wird. Nach dem erfolgreichen Aktualisieren des Geräts auf Update 3 wird diese Meldung nicht mehr angezeigt. |Dieses Verhalten wird in einer zukünftigen Version behoben. |Ja |Nein |

## <a name="controller-and-firmware-updates-in-update-3"></a>Updates des Controllers und der Firmware in Update 3
Diese Version bietet Updates von LSI-Treibern und -Firmware. Weitere Informationen zum Installieren von LSI-Treiber und -Firmware-Updates finden Sie unter [Installieren von Update 3 auf Ihrem StorSimple-Gerät](./index.yml) .

## <a name="virtual-device-updates-in-update-3"></a>Updates von virtuellen Geräten in Update 3
Dieses Update gilt nicht für die StorSimple Cloud Appliance (auch bekannt als das virtuelle Gerät). Es müssen neue virtuelle Geräte erstellt werden. 

## <a name="next-step"></a>Nächster Schritt
Erfahren Sie, wie Sie [Update 3 auf Ihrem StorSimple-Gerät installieren](./index.yml) .