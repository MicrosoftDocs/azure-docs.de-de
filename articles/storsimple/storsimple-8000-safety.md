---
title: Sicheres Installieren und Betreiben eines Geräts aus der StorSimple 8000-Serie
description: Beschreibt Sicherheitskonventionen, Richtlinien und Überlegungen. Darüber hinaus wird erläutert, wie das StorSimple-Gerät sicher installiert und verwendet wird.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 3adbd292d42fd6fd2a0e149e103c17968a6f084b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950753"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Sichere Installation und Verwendung des StorSimple-Geräts
![Symbol „Warnung“](./media/storsimple-safety/IC740879.png)
![Symbol „Sicherheitshinweis lesen“](./media/storsimple-safety/IC740885.png) **LESEN SIE DIE SICHERHEITS- UND GESUNDHEITSINFORMATIONEN**

Lesen Sie alle Informationen zur Sicherheit und Integrität in diesem Artikel, die sich auf Ihr Microsoft Azure StorSimple-Gerät beziehen. Bewahren Sie für die künftige Verwendung alle gedruckten Handbücher auf, die im Lieferumfang des StorSimple-Geräts enthalten waren. Wenn die Anweisungen nicht befolgt werden und das Produkt nicht ordnungsgemäß eingerichtet, verwendet und gewartet wird, kann sich das Risiko von schwerwiegenden Verletzungen, Todesfällen oder Schäden am Gerät/an den Geräten erhöhen. Eine [herunterladbare Version dieses Handbuchs](https://www.microsoft.com/download/details.aspx?id=44233) ist ebenfalls verfügbar.

## <a name="safety-icon-conventions"></a>Konventionen für Sicherheitssymbole
Die folgenden Symbole finden Sie in den Sicherheitsvorkehrungen, die beachtet werden müssen, wenn Sie Ihr Microsoft Azure StorSimple-Gerät einrichten und ausführen.

| Symbol | BESCHREIBUNG |
|:--- |:--- |
| ![Symbol „Gefahr“](./media/storsimple-safety/IC740879.png) **GEFAHR!** |Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen wird, wenn sie nicht vermieden wird. Dieses Signalwort ist auf extremste Situationen zu beschränken. |
| ![Symbol „Warnung“](./media/storsimple-safety/IC740879.png) **WARNUNG!** |Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen könnte, wenn sie nicht vermieden wird. |
| ![Symbol „Warnung“](./media/storsimple-safety/IC740879.png) **ACHTUNG!** |Zeigt eine Gefahrensituation an, die zu leichten bis mittelschweren Verletzungen führen könnte, wenn sie nicht vermieden wird. |
| ![Symbol „Hinweis“](./media/storsimple-safety/IC740881.png) **HINWEIS:** |Zeigt als wichtig erachtete Informationen an, die allerdings nicht mit Gefahren in Verbindung stehen. |
| ![Symbol „Vorsicht Stromschlag“](./media/storsimple-safety/IC740882.png) **Gefahr durch Stromschlag** |Hochspannung |
| ![Symbol „Hohes Gewicht“](./media/storsimple-safety/IC740883.png) **Hohes Gewicht** | |
| ![Symbol „Keine vom Benutzer zu wartenden Teile“](./media/storsimple-safety/IC740879.png) **Keine vom Benutzer zu wartenden Teile** |Zugriff nur bei ausreichender Schulung. |
| ![Symbol "Sicherheitshinweis lesen"](./media/storsimple-safety/IC740885.png)**Alle Anleitungen zuvor lesen** | |
| ![Symbol „Kippgefahr“](./media/storsimple-safety/IC740886.png) **Kippgefahr** | |

## <a name="handling-precautions"></a>Vorsichtsmaßnahmen bei der Handhabung
![Symbol „Warnung“](./media/storsimple-safety/IC740879.png) ![Symbol „Hohes Gewicht“](./media/storsimple-safety/IC740883.png) **WARNUNG!** 

So wird die Gefahr von Verletzungen verringert:

* Ein vollständig konfiguriertes Gehäuse kann bis zu 32 kg wiegen. Versuchen Sie nicht, es allein zu heben.
* Stellen Sie vor dem Verschieben des Gehäuses immer sicher, dass zwei Personen verfügbar sind, um das Gewicht zu tragen. Denken Sie daran: Wenn eine Person versucht, das Gewicht alleine zu heben, kann dies zu Verletzungen führen.
* Heben Sie das Gehäuse nicht an den Griffen der Stromversorgungs- und Kühleinheiten (PCMs) auf der Rückseite der Einheit an. Diese sind nicht dafür ausgelegt, das Gewicht zu tragen.

## <a name="connection-precautions"></a>Vorsichtsmaßnahmen bei der Verbindung
![Symbol „Warnung“](./media/storsimple-safety/IC740879.png) ![Symbol „Vorsicht Stromschlag“](./media/storsimple-safety/IC740882.png) **WARNUNG!**

So reduzieren Sie die Wahrscheinlichkeit, dass Verletzungen, Elektroschocks oder Tod eintreten:

* Unterbrechen Sie bei mehreren AC-Quellen die gesamte Stromversorgung für eine vollständige Isolation.
* Trennen Sie die Einheit dauerhaft von der Stromzufuhr, bevor Sie sie bewegen oder wenn Sie glauben, dass sie in irgendeiner Weise beschädigt wurde.
* Stellen Sie eine sichere elektrische Erdverbindung für die Anschlussleitungen her. Überprüfen Sie, ob das Erden des Gehäuses die nationalen und lokalen Anforderungen erfüllt, bevor Sie die Stromzufuhr öffnen.
* Stellen Sie sicher, dass die Stromzufuhr immer gekappt ist, bevor Sie ein PCM aus dem Gehäuse entfernen.
* Da der Stecker für das Stromkabel das wichtigste Gerät zum Trennen von der Stromverbindung ist, stellen Sie sicher, dass sich die Steckdosen in der Nähe der Geräte befinden und leicht zugänglich sind.

![Symbol „Warnung“](./media/storsimple-safety/IC740879.png) ![Symbol „Vorsicht Stromschlag“](./media/storsimple-safety/IC740882.png) **WARNUNG!**

So reduzieren Sie die Wahrscheinlichkeit von Überhitzung oder Brand bei elektrischen Verbindungen:

* Stellen Sie eine geeignete Stromquelle mit elektrischem Überlastungsschutz bereit, um die Anforderungen der technischen Spezifikationen zu erfüllen.
* Verwenden Sie keine verzweigten Stromkabel („Y“-Leitungen).
* Um die Anforderungen für Sicherheit, Emissionen und Temperaturüberwachung zu erfüllen, sollten keine Abdeckungen entfernt werden, und alle Einschübe sollten mit Plug-in-Modulen oder leeren Laufwerken aufgefüllt werden.
* Stellen Sie sicher, dass die Geräte so verwendet werden, wie vom Hersteller vorgesehen. Wenn die Geräte in einer Weise verwendet werden, die nicht vom Hersteller angegebenen wurden, kann der Schutz in den Geräten beeinträchtigt werden.

![Symbol „Hinweis“](./media/storsimple-safety/IC740881.png) **HINWEIS:**

Für den ordnungsgemäßen Betrieb Ihrer Geräte und um Schäden am Produkt zu vermeiden:

* Die RJ45-Ports auf der Rückseite des Geräts sind ausschließlich für eine Ethernet-Verbindung ausgelegt. Sie dürfen nicht mit einem Telekommunikationsnetz verbunden werden.
* Achten Sie darauf, das Gerät auf einem Gestell zu platzieren, das eine vorder- bis hinterseitige Kühlung bietet.
* Alle Plug-In-Module und leere Platinen sind Teil des Systemgehäuses. Sie dürfen nur entfernt werden, wenn sie sofort ersetzt werden können. Das System darf nicht ohne die vollständige Anzahl an Modulen oder Platinen ausgeführt werden.

## <a name="rack-system-precautions"></a>Vorsichtsmaßnahmen beim Gestellsystem
Wenn Sie das Gerät auf einem Gestellsystem montieren, müssen die folgenden Sicherheitsanforderungen berücksichtigt werden.

![Symbol „Warnung“](./media/storsimple-safety/IC740879.png) ![Symbol „Kippgefahr“](./media/storsimple-safety/IC740886.png) **WARNUNG!**

So reduzieren Sie die Wahrscheinlichkeit einer Verletzung durch Kippen:

* Das Gestell sollte das Gesamtgewicht der installierten Gehäuse tragen können und über stabilisierende Eigenschaften verfügen, die dafür geeignet sind, das Gestell vom Kippen oder vom Umstoßen während der Montage oder des normalen Gebrauchs abzuhalten.
* Wenn das Gestell beladen wird, sollte es von unten nach oben beladen und von oben nach unten geleert werden.
* Entfernen Sie nicht mehr als eine Einheit gleichzeitig vom Gestell, damit die Gefahr vermieden wird, dass es umkippt.

![Symbol „Warnung“](./media/storsimple-safety/IC740879.png) ![Symbol „Vorsicht Stromschlag“](./media/storsimple-safety/IC740882.png) **WARNUNG!**

So reduzieren Sie die Wahrscheinlichkeit, dass Verletzungen, Elektroschocks oder Tod eintreten:

* Das Gestell sollte über ein sicheres elektrisches Verteilungssystem verfügen. Es muss einen Überstromschutz für das Gehäuse bieten und darf nicht von der Gesamtzahl der installierten Gehäuse überladen werden. Beachten Sie auch die auf dem Fabrikatsschild genannte Belastbarkeit für den Stromverbrauch.
* Das elektrische Verteilungssystem muss eine zuverlässige Grundlage für jedes Gehäuse im Gestell bereitstellen.
* Das elektrische Verteilungssystem muss so entworfen sein, dass der gesamte Erdableitstrom von allen Netzteilen in allen Gehäusen berücksichtigt wird. Beachten Sie, dass die Stromzufuhr in jedem Gehäuse über einen Erdableitstrom von maximal 1,0 mA bei 60 Hz 264 Volt verfügt. Das Gestell muss möglicherweise folgendermaßen gekennzeichnet werden: „HOHE ABLEITSTRÖME. Eine Verbindung an Masse ist vor dem Stromanschluss unbedingt erforderlich.“
* Wenn das Gestell mit den Gehäusen aufgestellt ist, muss es die Sicherheitsanforderungen UL 60950-1 und IEC 60950-1/EN 60950-1 erfüllen.

![Symbol „Hinweis“](./media/storsimple-safety/IC740881.png) **HINWEIS:**

Für die ordnungsgemäße Kühlung des Gestellsystems:

* Stellen Sie sicher, dass der Gestellaufbau die maximale vom Gehäuse erzeugte Umgebungstemperatur von 35 Grad Celsius berücksichtigt. Kühlen Sie den Raum, in dem sich das Gestellsystem befindet, und stellen Sie sicher, dass der Luftstrom aus dem Klimaanlagenschacht im Rechenzentrum ausreichend ist.
* Das System wird mit einer Installation mit niedrigem Druck und hinterer Lüftung betrieben (der hintere Druck durch die Gestelltüren und -hindernisse darf 5 Pascal [0,5 mm auf der Wasserstandsanzeige] nicht überschreiben).

## <a name="power-cooling-module-pcm-precautions"></a>Vorsichtsmaßnahmen bei der Stromversorgungs- und Kühleinheit (PCM)

Das Gerät ist für den Betrieb mit zwei Kühleinheiten ausgelegt. Jede der Kühleinheiten verfügt über eine Stromversorgung und ein Doppelachsgebläse. In kritischem Zustand lässt das System das Versagen einer Stromzufuhr zu, während der normale Betrieb aufrecht erhalten wird. Zwei Kühleinheiten (und daher Stromzufuhren) müssen immer installiert sein. Ein einzelnes PCM bietet keine redundante Stromversorgung. Aus diesem Grund kann der Ausfall von nur einem PCM zu Ausfallzeiten und Datenverlusten führen.

![Symbol „Warnung“](./media/storsimple-safety/IC740879.png) ![Symbol „Vorsicht Stromschlag“](./media/storsimple-safety/IC740882.png) **WARNUNG!**

So reduzieren Sie die Wahrscheinlichkeit, dass Verletzungen, Elektroschocks oder Tod eintreten:

* Entfernen Sie nicht die Abdeckungen vom PCM. Innen besteht die Gefahr von Elektroschocks. Um ein PCM zurückzugeben und Ersatz zu erhalten, [wenden Sie sich an den Microsoft-Support](./storsimple-8000-contact-microsoft-support.md).

![Symbol „Hinweis“](./media/storsimple-safety/IC740881.png) **HINWEIS:**

Für den ordnungsgemäßen Betrieb Ihrer Geräte und um Schäden am Produkt zu vermeiden:

* Ersetzen Sie das fehlerhafte PCM innerhalb von 24 Stunden. Nach dem Entfernen eines PCMs zum Austausch muss der Ersatz innerhalb von 10 Minuten nach dem Entfernen installiert werden.
* Ein PCM kann nicht entfernt werden, sofern kein Ersatz sofort installiert werden kann. Das Gehäuse darf nicht ohne alle an der richtigen Stelle vorhandenen Module betrieben werden.

## <a name="electrostatic-discharge-esd-precautions"></a>Vorsichtsmaßnahmen bei der elektrostatischen Entladung (ESD)

![Symbol „Hinweis“](./media/storsimple-safety/IC740881.png) **HINWEIS:**

Beachten Sie die folgenden, auf ESD bezogenen Vorsichtsmaßnahmen.

* Stellen Sie sicher, dass Sie ein geeignetes Ableitband oder einen geeigneten Ableitriemen installiert und überprüft haben.
* Beachten Sie beim Umgang mit Modulen und Komponenten alle herkömmlichen ESD-Vorsichtsmaßnahmen.
* Vermeiden Sie den Kontakt mit der Rückwandplatinenkomponenten und Modulverbindungen.
* ESD-Schäden werden nicht von der Gewährleistung abgedeckt.

## <a name="battery-disposal-precautions"></a>Vorsichtsmaßnahmen bei der Batterieentsorgung

Das Netzteil verwendet eine speziellen Batterie, um den Inhalt des Arbeitsspeichers während temporärer, kurzfristiger Stromausfälle zu schützen. Die Batterie ist im PCM eingesetzt. Beachten Sie folgende Informationen zum Akku.

![Warnsymbol](./media/storsimple-safety/IC740879.png) **WARNUNG!**

Um das Risiko von Kurzschlüssen, Bränden, Explosionen, Verletzungen oder Todesfällen zu verringern:

* Entsorgen Sie Batterien gemäß nationaler/regionaler Bestimmungen.
* Nicht auseinander bauen, zerbrechen oder über 60 Grad Celsius erhitzen oder verbrennen. Ersetzen Sie die Batterie des PCM mit durch eine mitgelieferte Batterie. Das Verwenden einer anderen Batterie kann Brände oder Explosionen verursachen.
* Verwenden Sie Schutzkappen auf den Batterien, wenn diese von der Stromversorgung entfernt werden.

![Symbol „Hinweis“](./media/storsimple-safety/IC740881.png) **HINWEIS:**

Beim Versand oder bei anderweitiger Beförderung der Batterien auf dem Luftweg befolgen Sie den Leitfaden der IATA „Lithium Battery Guidance“ unter [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx).

Nachdem Sie diese Sicherheitshinweise gelesen haben, ist der nächste Schritt, das Gerät zu entpacken, aufzustellen und zu an den Strom anzuschließen.

## <a name="next-steps"></a>Nächste Schritte

* Wenn es sich um ein 8100-Gerät handelt, navigieren Sie zu [Installieren des StorSimple 8100-Geräts](storsimple-8100-hardware-installation.md).
* Wenn es sich um ein 8600-Gerät handelt, navigieren Sie zu [Installieren des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md).