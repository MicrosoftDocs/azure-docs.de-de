---
title: 'Lokale StorSimple-Volumes: Häufig gestellte Fragen |Microsoft Docs'
description: Enthält Antworten auf häufig gestellte Fragen zu lokalen StorSimple-Volumes.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 483fa81b409e1bd740af85b431a86b6c814831e6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002713"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>Lokale StorSimple-Volumes: Häufig gestellte Fragen (FAQ)
## <a name="overview"></a>Übersicht
Hier finden Sie Fragen und Antworten, die sich auf das Erstellen eines lokalen StorSimple-Volumes, Konvertieren eines mehrstufigen Volumes in ein lokales Volume (und umgekehrt) oder Sichern und Wiederherstellen eines lokalen Volumes beziehen.

Die Fragen und Antworten sind in folgende Kategorien unterteilt:

* Erstellen eines lokalen Volumes
* Sichern eines lokalen Volumes
* Konvertieren eines mehrstufigen Volumes in ein lokales Volume
* Wiederherstellen eines lokalen Volumes
* Ausführen eines Failovers für ein lokales Volume

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Fragen zum Erstellen eines lokalen Volumes
**F.** Wie groß kann ein lokales Volume auf Geräten der 8000-Serie maximal sein?

**A** Auf Geräten mit StorSimple 8000 Series Update 3.0 können Sie lokale Volumes von bis zu 8,5 TB oder mehrstufige Volumes von bis zu 200 TB auf dem 8100-Gerät bereitstellen. Auf dem größeren 8600-Gerät können Sie lokale Volumes von bis zu 22,5 TB oder mehrstufige Volumes von bis zu 500 TB bereitstellen.

**F.** Ich habe vor Kurzem mein 8100-Gerät auf Update 3.0 aktualisiert. Wenn ich nun versuche, ein lokales Volume zu erstellen, beträgt die verfügbare maximale Größe statt 8,5 TB nur 6 TB. Warum kann ich kein Volume mit 8,5 TB erstellen?

**A** Wenn auf dem Gerät Update 3.0 ausgeführt wird, können Sie lokale Volumes mit bis zu 8,5 TB ODER mehrstufige Volumes mit bis zu 200 TB auf dem 8100-Gerät bereitstellen. Wenn auf Ihrem Gerät bereits mehrstufige Volumes bereitgestellt wurden, ist der Platz zum Erstellen eines lokalen Volumes proportional kleiner als diese Höchstgrenze. Beispiel: Wenn auf dem 8100-Gerät bereits mehrstufige Volumes mit ca. 106 TB (also der Hälfte der Kapazität mehrstufiger Volumes) bereitgestellt wurden, verringert sich die maximale Größe eines lokalen Volumes, das Sie auf dem 8100-Gerät erstellen können, dementsprechend auf 4 TB (ca. die Hälfte der maximalen Kapazität lokaler Volumes).

Da ein Teil des lokalen Speicherplatzes auf dem Gerät zum Hosten des Arbeitssatzes mehrstufiger Volumes verwendet wird, verringert sich der verfügbare Speicherplatz für die Erstellung eines lokalen Volumes, wenn das Gerät über mehrstufige Volumes verfügt. Umgekehrt verringert sich beim Erstellen eines lokalen Volumes der verfügbare Speicherplatz für mehrstufige Volumes proportional. In den folgenden Tabellen wird die Kapazität für mehrstufige Volumes auf 8100- und 8600-Geräten bei der Erstellung lokaler Volumes zusammengefasst.

#### <a name="update-30"></a>Update 3.0 

| Bereitgestellte Kapazität lokaler Volumes | Verfügbare Kapazität für mehrstufige Volumes – 8100 | Verfügbare Kapazität für mehrstufige Volumes – 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |Nicht verfügbar |277,8 TB |
| 15 TB |Nicht verfügbar |166,7 TB |
| 22,5 TB |Nicht verfügbar |0 TB |

**F.** Warum dauert die Erstellung eines lokalen Volumes so lange?

**A.** Lokale Volumes werden mit vollständiger Speicherzuweisung bereitgestellt. Um Platz auf den lokalen Ebenen des Geräts zu schaffen, können einige Daten von vorhandenen mehrstufigen Volumes während des Bereitstellungsvorgangs in die Cloud übertragen werden. Da dieser Vorgang von der Größe des bereitzustellenden Volumes, den vorhandenen Daten auf Ihrem Gerät und der verfügbaren Bandbreite für die Cloudverbindung abhängt, kann die Erstellung eines lokalen Volumes mehrere Stunden dauern.

**F.** Wie lange dauert die Erstellung eines lokalen Volumes?

**A.** Da lokal angeheftete Volumes mit vollständiger Speicherzuweisung bereitgestellt werden, werden vorhandene Daten von mehrstufigen Volumes während der Bereitstellung möglicherweise in die Cloud übertragen. Daher hängt die Dauer zum Erstellen eines lokalen Volumes von mehreren Faktoren ab, u. a. von der Größe des Volumes, den Daten auf dem Gerät und der verfügbaren Bandbreite. Auf einem neu installierten Gerät ohne Volumes dauert die Erstellung eines lokalen Volumes etwa zehn Minuten pro Terabyte an Daten. Die Erstellung eines lokalen Volumes kann jedoch basierend auf den oben erläuterten Faktoren auf einem verwendeten Gerät mehrere Stunden dauern.

**F.** Ich möchte ein lokales Volume erstellen. Gibt es bewährte Verfahren, die beachtet werden sollten?

**A.** Lokale Volumes eignen sich für Workloads, die jederzeit lokale Garantien für Daten erfordern und empfindlich auf Cloudlatenzen reagieren. Wenn Sie die Nutzung lokaler Volumes für Ihre Workloads in Betracht ziehen, berücksichtigen Sie Folgendes:

* Lokale Volumes werden mit vollständiger Speicherzuweisung bereitgestellt. Die Erstellung lokaler Volumes wirkt sich auf den verfügbaren Platz für mehrstufige Volumes aus. Aus diesem Grund wird empfohlen, mit kleineren Volumes zu beginnen und bei steigenden Speicheranforderungen hochzuskalieren.
* Die Bereitstellung lokaler Volumes ist ein zeitintensiver Vorgang, bei dem unter Umständen vorhandene Daten von mehrstufigen Volumes in die Cloud übertragen werden. Dies kann zu einer Beeinträchtigung der Leistung auf diesen Volumes führen.
* Die Bereitstellung von lokalen Volumes ist ein zeitaufwendiger Vorgang. Die tatsächliche Dauer hängt von mehreren Faktoren ab: der Größe des Volumes, das bereitgestellt wird, den Daten auf Ihrem Gerät und der verfügbaren Bandbreite. Wenn Sie Ihre vorhandenen Volumes nicht in der Cloud gesichert haben, dauert die Volumeerstellung länger. Es wird empfohlen, vor dem Bereitstellen eines lokalen Volumes Cloudmomentaufnahmen Ihrer vorhandenen Volumes zu erstellen.
* Sie können vorhandene mehrstufige Volumes in lokale Volumes konvertieren. Bei dieser Konvertierung muss auf dem Gerät Platz für das geplante lokale Volume geschaffen werden (zudem müssen ggf. ausgelagerte Daten aus der Cloud geladen werden). Dies ist ein zeitaufwendiger Vorgang, der von den oben besprochenen Faktoren abhängig ist. Sie sollten Ihre vorhandenen Volumes vor der Konvertierung sichern, da der Prozess langsamer ist, wenn vorhandene Volumes nicht gesichert wurden. Während dieses Vorgangs treten auf dem Gerät möglicherweise Leistungseinbußen auf.

Weitere Informationen zum [Erstellen eines lokalen Volumes](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**F.** Kann ich mehrere lokale Volumes gleichzeitig erstellen?

**A.** Ja, aber Aufträge zum Erstellen und Erweitern lokaler Volumes werden nacheinander verarbeitet.

Lokale Volumes werden mit vollständiger Speicherzuweisung bereitgestellt. Dafür muss auf dem Gerät lokal Speicherplatz geschaffen werden (was unter Umständen dazu führt, dass vorhandene Daten von mehrstufigen Volumes während des Bereitstellungsvorgangs in die Cloud übertragen werden). Während der Ausführung eines Bereitstellungsauftrags werden andere Aufträge zur Erstellung lokaler Volumes daher in die Warteschlange verschoben, bis der Auftrag abgeschlossen ist.

Wenn ein vorhandenes lokales Volume erweitert oder ein mehrstufiges Volume in ein lokales Volume konvertiert wird, wird die Erstellung eines neuen lokalen Volumes in die Warteschlange verschoben, bis der vorherige Auftrag abgeschlossen ist. Wenn Sie ein lokales Volume erweitern, muss auch der lokale Speicherplatz für das Volume erweitert werden. Bei der Konvertierung von einem mehrstufigen in ein lokales Volume muss lokaler Speicherplatz für das resultierende lokale Volume freigegeben werden. Bei beiden Vorgängen ist das Freigeben oder Erweitern des lokalen Speicherplatzes ein zeitintensiver Vorgang.

Sie können diese Aufträge im Azure StorSimple-Geräte-Manager-Dienst auf dem Blatt **Aufträge** anzeigen. Der Auftrag, der derzeit verarbeitet wird, wird kontinuierlich aktualisiert, um den Status der Speicherplatzbereitstellung widerzuspiegeln. Die übrigen Aufträge für lokale Volumes werden als aktiv gekennzeichnet, ihr Fortschritt wird jedoch nicht aktualisiert, und sie werden in der Reihenfolge ausgewählt, in der sie der Warteschlange hinzugefügt wurden.

**F.** Ich habe ein lokales Volume gelöscht. Warum wird der freigegebene Speicherplatz im verfügbaren Speicherplatz nicht angezeigt, wenn ich versuche, ein neues Volume zu erstellen?

**A.** Wenn Sie ein lokales Volume löschen, wird der für neue Volumes verfügbare Speicherplatz möglicherweise nicht sofort aktualisiert. Der StorSimple-Geräte-Manager-Dienst aktualisiert den verfügbaren lokalen Speicherplatz ungefähr stündlich. Es wird empfohlen, eine Stunde zu warten, bevor Sie versuchen, das neue Volume zu erstellen.

**F.** Werden lokale Volumes auf Cloud Appliances unterstützt?

**A.** Lokale Volumes werden auf der Cloud Appliance (8010- und 8020-Geräte, zuvor als virtuelles StorSimple-Gerät bezeichnet) nicht unterstützt.

**F.** Kann ich Azure PowerShell-Cmdlets zum Erstellen und Verwalten lokaler Volumes verwenden?

**A.** Nein, Sie können keine lokalen Volumes über Azure PowerShell-Cmdlets erstellen (alle mithilfe von Azure PowerShell erstellten Volumes sind mehrstufig). Darüber hinaus wird empfohlen, die Azure PowerShell-Cmdlets nicht zum Ändern der Eigenschaften eines lokalen Volumes zu verwenden, da dadurch das Volume ungewollt in ein mehrstufiges Volume geändert wird.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Fragen zum Sichern eines lokalen Volumes
**F.** Werden lokale Momentaufnahmen der lokalen Volumes unterstützt?

**A.** Ja, Sie können lokale Momentaufnahmen der lokalen Volumes erstellen. Es wird jedoch dringend empfohlen, die lokalen Volumes regelmäßig mit Cloudmomentaufnahmen zu sichern, um den Schutz Ihrer Daten im Notfall zu gewährleisten.

Beachten Sie, dass lokale Momentaufnahmen lokaler Volumes auch in die Cloud ausgelagert werden können und nicht unbedingt auf der lokalen Stufe des Geräts verbleiben.

**F.** Gibt es Richtlinien zum Verwalten lokaler Momentaufnahmen für lokale Volumes?

**A.** Häufige lokale Momentaufnahmen können in Kombination mit umfassenden Datenänderungen auf dem lokalen Volume dazu führen, dass der lokale Speicherplatz auf dem Gerät schnell verbraucht ist und Daten auf mehrstufigen Volumes in die Cloud übertragen werden. Daher wird empfohlen, die Anzahl der lokalen Momentaufnahmen möglichst gering zu halten.

**F.** Ich habe eine Warnung mit dem Hinweis erhalten, dass meine lokalen Momentaufnahmen der lokalen Volumes möglicherweise ungültig gemacht werden. Wann kann dieser Fall eintreten?

**A.** Häufige lokale Momentaufnahmen können in Kombination mit umfassenden Datenänderungen auf dem lokalen Volume dazu führen, dass der lokale Speicherplatz auf dem Gerät schnell verbraucht ist. Wenn die lokalen Ebenen des Geräts stark ausgelastet sind, kann ein längerer Ausfall der Cloud dazu führen, dass das Gerät voll wird. Eingehende Schreibvorgänge auf dem Volume haben unter Umständen eine Invalidierung der Momentaufnahmen zur Folge (da kein Speicherplatz zum Aktualisieren der Momentaufnahmen für den Verweis auf ältere Datenblöcke verfügbar ist, die überschrieben wurden). In diesem Fall werden die Schreibvorgänge auf dem Volume weiterhin bedient, aber die lokalen Momentaufnahmen sind möglicherweise ungültig. Vorhandene Cloudmomentaufnahmen sind davon nicht betroffen.

Die Warnmeldung soll Sie darüber informieren, dass solch eine Situation eintreten kann, und sicherstellen, dass Sie sich rechtzeitig Maßnahmen ergreifen, indem Sie entweder die Zeitpläne für lokale Momentaufnahmen anpassen, um weniger häufig lokale Momentaufnahmen zu erstellen, oder ältere lokale Momentaufnahmen löschen, die nicht mehr erforderlich sind.

Wenn die lokalen Momentaufnahmen ungültig werden, erhalten Sie eine Warnung mit der Information, dass die lokalen Momentaufnahmen für die spezifische Sicherungsrichtlinie ungültig gemacht wurden, sowie die Liste mit den Zeitstempeln der lokalen Momentaufnahmen, die ungültig gemacht wurden. Diese Momentaufnahmen werden automatisch gelöscht, und Sie können sie nicht mehr im Azure-Portal auf dem Blatt **Sicherungskataloge** anzeigen.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Fragen zum Konvertieren eines mehrstufigen Volumes in ein lokales Volume
**F.** Ich habe bemerkt, dass das Gerät beim Konvertieren eines mehrstufigen Volumes in ein lokales Volume langsam reagiert. Warum ist das so?

**A.** Die Konvertierung umfasst zwei Schritte:

1. Bereitstellen von Speicherplatz auf dem Gerät für das lokale Volume, das in Kürze konvertiert wird
2. Herunterladen ausgelagerter Daten aus der Cloud, um lokale Garantien zu gewährleisten

Bei beiden Schritten handelt es sich um Vorgänge mit langer Ausführungsdauer, die von der Größe des zu konvertierenden Volumes, den Daten auf dem Gerät und der verfügbaren Bandbreite abhängig sind. Da einige Daten von vorhandenen mehrstufigen Volumes im Rahmen des Bereitstellungsprozesses möglicherweise in die Cloud übergehen, kann es während dieser Zeit zu Leistungseinbußen auf dem Gerät kommen. Darüber hinaus kann der Konvertierungsprozess in folgenden Fällen langsamer sein:

* Vorhandene Volumes wurden nicht in der Cloud gesichert. Daher wird empfohlen, die Volumes vor dem Initiieren einer Konvertierung zu sichern.
* Richtlinien zur Bandbreitendrosselung wurden angewendet, die die verfügbare Bandbreite der Cloudverbindung einschränken können. Aus diesem Grund sollten Sie sicherstellen, dass eine dedizierte Bandbreite von mindestens 40 MBit/s für die Cloudverbindung verfügbar ist.
* Der Konvertierungsvorgang kann aufgrund der oben erläuterten Faktoren einige Stunden dauern. Daher wird empfohlen, den Vorgang am Wochenende bzw. nicht während Spitzenzeiten auszuführen, damit Endbenutzer nicht beeinträchtigt werden.

Weitere Informationen zum [Konvertieren eines mehrstufigen Volumes in ein lokales Volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**F.** Kann ich den Vorgang für die Volumekonvertierung abbrechen?

**A.** Nein, der Konvertierungsvorgang kann nach seiner Initiierung nicht mehr abgebrochen werden. Wie in der vorherigen Frage erläutert, sollten Sie potenzielle Leistungsprobleme bedenken, die während des Prozesses auftreten können, und sich beim Planen der Konvertierung an die oben genannten Empfehlungen halten.

**F.** Was geschieht mit dem Volume, wenn die Konvertierung fehlschlägt?

**A.** Bei der Volumekonvertierung können aufgrund von Cloudkonnektivitätsproblemen Fehler auftreten. Nach mehreren nicht erfolgreichen Versuchen, ausgelagerte Dateien aus der Cloud abzurufen, beendet das Gerät die Konvertierung möglicherweise. In solch einem Szenario ist der Volumetyp der gleiche wie vor der Konvertierung. Zudem geschieht Folgendes:

* Eine kritische Warnung wird angezeigt, um Sie über den Volumekonvertierungsfehler zu informieren. Weitere Informationen zu [Warnungen im Zusammenhang mit lokalen Volumes](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Wenn Sie ein mehrstufiges Volume in ein lokales Volume konvertieren, weist das Volume weiterhin Eigenschaften eines mehrstufigen Volumes auf, da sich möglicherweise immer noch Daten in der Cloud befinden. Es wird empfohlen, die Konnektivitätsprobleme zu beheben und den Konvertierungsvorgang zu wiederholen.
* Dasselbe gilt bei der fehlgeschlagenen Konvertierung eines lokalen Volumes in ein mehrstufiges Volume: Obwohl das Volume als lokales Volume gekennzeichnet wird, wird es als mehrstufiges Volume ausgeführt (da Daten möglicherweise in die Cloud übergegangen sind). Es belegt jedoch weiterhin Speicherplatz auf den lokalen Ebenen des Geräts. Dieser Speicherplatz ist nicht für andere lokale Volumes verfügbar. Es wird empfohlen, den Vorgang zu wiederholen, um sicherzustellen, dass die Volumekonvertierung abgeschlossen ist und der lokale Speicherplatz auf dem Gerät freigegeben werden kann.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Fragen zum Wiederherstellen eines lokalen Volumes
**F.** Werden lokale Volumes sofort wiederhergestellt?

**A.** Ja, lokale Volumes werden sofort wiederhergestellt. Sobald die Metadateninformationen für das Volume im Rahmen des Wiederherstellungsvorgangs aus der Cloud abgerufen werden, wird das Volume online geschaltet, und vom Host kann darauf zugegriffen werden. Lokale Garantien für die Volumedaten sind erst vorhanden, wenn alle Daten aus der Cloud heruntergeladen wurden. Während der Wiederherstellung kann die Leistung auf diesen Volumes beeinträchtigt sein.

**F.** Wie lange dauert die Wiederherstellung eines lokalen Volumes?

**A.** Lokale Volumes werden sofort wiederhergestellt und online geschaltet, wenn die Volumemetadateninformationen aus der Cloud abgerufen werden. Die Volumedaten werden weiterhin im Hintergrund heruntergeladen. Dieser letzte Teil des Wiederherstellungsvorgangs, also das Abrufen der lokalen Garantien für die Volumedaten, ist ein zeitintensiver Prozess. Es kann mehrere Stunden dauern, bis alle Daten wieder lokal verfügbar sind. Die Dauer zum Abschließen dieses Vorgangs hängt von mehreren Faktoren ab, etwa der Größe des wiederherzustellenden Volumes und der verfügbaren Bandbreite. Wenn das ursprüngliche Volume, das wiederhergestellt wird, gelöscht wurde, wird zusätzlich Zeit darauf verwendet, im Rahmen des Wiederherstellungsvorgangs den lokalen Speicherplatz auf dem Gerät freizugeben.

**F.** Ich muss für mein vorhandenes lokales Volume eine ältere (bei der Auslagerung des Volumes erstellte) Momentaufnahme wiederherstellen. Wird das Volume in diesem Fall als mehrstufiges Volume wiederhergestellt?

**A.** Nein, das Volume wird als lokales Volume wiederhergestellt. Die Momentaufnahme wurde zwar zum Zeitpunkt der Volumeauslagerung erstellt, beim Wiederherstellen vorhandener Volumes verwendet StorSimple jedoch immer den derzeit vorhandenen Volumetyp auf dem Datenträger.

**F.** Ich habe mein lokales Volume vor Kurzem erweitert, nun muss ich die Daten jedoch auf einen Zeitpunkt zurücksetzen, zu dem das Volume kleiner war. Wird bei der Wiederherstellung die Größe des aktuellen Volumes geändert, und muss ich das Volume nach der Wiederherstellung erweitern?

**A.** Ja, bei der Wiederherstellung wird die Größe des Volumes geändert, und Sie müssen das Volume nach der Wiederherstellung erweitern.

**F.** Kann ich den Volumetyp während der Wiederherstellung ändern?

**A.** Nein, Sie können den Volumetyp während der Wiederherstellung nicht ändern.

* Volumes, die gelöscht wurden, werden als der in der Momentaufnahme gespeicherte Typ wiederhergestellt.
* Vorhandene Volumes werden basierend auf ihrem aktuellen Typ wiederhergestellt und zwar unabhängig von dem in der Momentaufnahme gespeicherten Typ (siehe die zwei vorherigen Fragen).

**F.** Ich muss mein lokales Volume wiederherstellen, habe jedoch die Momentaufnahme des falschen Zeitpunkts ausgewählt. Kann ich den aktuellen Wiederherstellungsvorgang abbrechen?

**A.** Ja, Sie können einen gerade ausgeführten Wiederherstellungsvorgang abbrechen. Das Volume wird auf den Zustand zu Beginn der Wiederherstellung zurückgesetzt. Alle Schreibvorgänge, die auf dem Volume während der Wiederherstellung vorgenommen wurden, gehen jedoch verloren.

**F.** Ich habe einen Wiederherstellungsvorgang auf einem meiner lokalen Volumes gestartet, und jetzt wird im Backlogkatalog eine Momentaufnahme angezeigt, an deren Erstellung ich mich nicht erinnere. Wofür wird sie verwendet?

**A.** Hierbei handelt es sich um die temporäre Momentaufnahme, die vor dem Wiederherstellungsvorgang erstellt und für Rollbackvorgänge verwendet wird, falls die Wiederherstellung abgebrochen wird oder fehlschlägt. Löschen Sie diese Momentaufnahme nicht. Sie wird nach Abschluss der Wiederherstellung automatisch gelöscht. Dieses Verhalten ist möglich, wenn der Wiederherstellungsauftrag nur lokale Volumes oder eine Mischung aus lokalen und mehrstufigen Volumes enthält. Wenn der Wiederherstellungsauftrag nur mehrstufige Volumes enthält, tritt dieses Verhalten nicht auf.

**F.** Kann ich ein lokales Volume klonen?

**A.** Ja, das ist möglich. Das lokale Volume wird jedoch standardmäßig als mehrstufiges Volume geklont. Weitere Informationen zum [Klonen eines lokalen Volumes](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Fragen zum Ausführen eines Failovers für ein lokales Volume
**F.** Ich muss für mein Gerät ein Failover auf ein anderes physisches Gerät ausführen. Erhalte ich beim Failover meiner lokalen Volumes lokale oder mehrstufige Volumes?

**A.** Für die lokalen Volumes erfolgt ein Failover des Typs „Lokal“, wenn auf dem Zielgerät der StorSimple 8000-Serie Update 3 oder höher ausgeführt wird.

Weitere Informationen zu [Failover und Notfallwiederherstellung bei lokalen Volumes mit verschiedenen Versionen](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**F.** Werden lokale Volumes bei der Notfallwiederherstellung sofort wiederhergestellt?

**A.** Ja, lokale Volumes werden beim Failover sofort wiederhergestellt. Sobald die Metadateninformationen für das Volume im Rahmen des Failovervorgangs aus der Cloud abgerufen werden, wird das Volume auf dem Gerät online geschaltet, und vom Host kann darauf zugegriffen werden. In der Zwischenzeit werden die Volumedaten weiterhin im Hintergrund heruntergeladen. Während des Failovers treten möglicherweise Leistungseinbußen auf diesen Volumes auf.

**F.** Der Failoverauftrag wird als abgeschlossen angezeigt. Wie kann ich den Status eines lokalen Volumes nachverfolgen, das auf dem Zielgerät wiederhergestellt wird?

**A.** Bei einem Failover wird der Failoverauftrag als abgeschlossen markiert, sobald alle Volumes in der Failovergruppe wiederhergestellt und auf dem Zielgerät online geschaltet wurden. Dazu gehören alle lokalen Volumes, für die möglicherweise ein Failover ausgeführt wurde. Lokale Garantien der Daten sind jedoch nur verfügbar, wenn alle Daten für das Volume heruntergeladen wurden. Sie können den Status für jedes lokale Volume, für das ein Failover ausgeführt wurde, durch die Überwachung der entsprechenden Wiederherstellungsaufträge nachverfolgen, die im Rahmen des Failovers erstellt werden. Diese individuellen Wiederherstellungsaufträge werden nur für lokale Volumes erstellt.

**F.** Kann ich den Volumetyp während des Failovers ändern?

**A.** Nein, Sie können den Volumetyp während eines Failovers nicht ändern. Bei einem Failover auf ein anderes physisches Gerät, auf dem Update 3 der StorSimple 8000-Serie ausgeführt wird, wird für die Volumes basierend auf dem in der Momentaufnahme gespeicherten Volumetyp ein Failover ausgeführt.

**F.** Kann ich für einen Volumecontainer mit lokalen Volumes ein Failover auf die Cloud Appliance ausführen?

**A.** Ja, das ist möglich. Bei einem Failover der lokalen Volumes erhalten Sie mehrstufige Volumes. Weitere Informationen zu [Failover und Notfallwiederherstellung bei lokalen Volumes mit verschiedenen Versionen](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

