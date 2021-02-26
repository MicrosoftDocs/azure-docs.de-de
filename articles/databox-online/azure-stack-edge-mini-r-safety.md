---
title: Azure Stack Edge Mini R – Sicherheitsleitfaden | Microsoft-Dokumentation
description: Beschreibt Sicherheitskonventionen, Richtlinien und Überlegungen und erläutert, wie Sie Ihr Azure Stack Edge Mini R-Gerät sicher installieren und betreiben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: alkohli
ms.openlocfilehash: aa363b1eeddff6c3b10d8e36371becb8b690697c
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981036"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Sicherheitsanweisungen für Azure Stack Edge Mini R

![Warnsymbol 1](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![Symbol „Sicherheitshinweis lesen“](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
**LESEN SIE DIE SICHERHEITS- UND GESUNDHEITSINFORMATIONEN**

Lesen Sie alle Sicherheitsinformationen in diesem Artikel, bevor Sie Ihr Azure Stack Edge Mini R-Gerät verwenden, das sich aus einem Akku, einem Netzteil, einem Netzadapter für das Modul und einem Servermodul zusammensetzt. Die Nichtbeachtung von Anweisungen kann zu Bränden, Stromschlägen, Verletzungen oder Schäden an Ihren Systemen führen. Lesen Sie alle unten aufgeführten Sicherheitsinformationen, bevor Sie Azure Stack Edge Mini R verwenden.

## <a name="safety-icon-conventions"></a>Konventionen für Sicherheitssymbole

Die folgenden Signalwörter werden für Gefahrenwarnungen verwendet:

| Symbol | BESCHREIBUNG |
|:--- |:--- |
| ![Gefahrensymbol](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **GEFAHR:** Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen wird, wenn sie nicht vermieden wird. <br> **WARNUNG:** Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen könnte, wenn sie nicht vermieden wird. <br> **VORSICHT:** Zeigt eine Gefahrensituation an, die zu leichten bis mittelschweren Verletzungen führen könnte, wenn sie nicht vermieden wird.|
|

Beim Einrichten und Ausführen des Azure Stack Edge Mini R-Geräts sind die folgenden Gefahrensymbole zu beachten:

| Symbol | BESCHREIBUNG |
|:--- |:--- |
| ![Alle Anleitungen zuvor lesen](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Alle Anleitungen zuvor lesen |
| ![Symbol „Hinweis“](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **HINWEIS:** | Zeigt als wichtig erachtete Informationen an, die allerdings nicht mit Gefahren in Verbindung stehen. |
| ![Gefahrensymbol](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Gefahrensymbol |
| ![Symbol "Stromschlag"](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Gefahr eines Stromschlags |
| ![Nur für den Gebrauch im Innenbereich](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Nur für den Gebrauch im Innenbereich |
| ![Symbol "Keine vom Benutzer zu wartenden Teile"](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Keine vom Benutzer zu wartenden Teile Zugriff nur bei ausreichender Schulung. |
|

## <a name="handling-precautions-and-site-selection"></a>Vorsichtsmaßnahmen für die Arbeit und Standortauswahl

Für Azure Stack Edge Mini R-Geräte müssen die folgenden Vorsichtsmaßnahmen und Kriterien bei der Standortauswahl berücksichtigt werden:

![Warnsymbol 2](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![Symbol „Stromschlag“](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![Symbol „Keine vom Benutzer zu wartenden Teile“](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png)**VORSICHT:**

* Überprüfen Sie das *empfangene* Gerät auf Schäden. Wenn das Gerätegehäuse beschädigt ist, wenden Sie sich an den [Microsoft-Support](azure-stack-edge-placeholder.md), um ein Austauschgerät zu erhalten. Versuchen Sie nicht, das Gerät in Betrieb zu nehmen.
* Wenn Sie vermuten, dass das Gerät defekt ist, wenden Sie sich an den [Microsoft-Support](azure-stack-edge-placeholder.md), um ein Austauschgerät zu erhalten. Versuchen Sie nicht, das Gerät zu warten.
* Das Gerät enthält keine vom Benutzer zu wartenden Teile. Im Inneren sind gefährliche Spannungs-, Strom- und Leistungspegel vorhanden. Öffnen Sie es nicht. Senden Sie das Gerät zur Wartung an Microsoft zurück.

![Warnsymbol 3](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **VORSICHT:**

Folgende Empfehlungen gelten für den Systembetrieb:

* Nicht in der Nähe von Wärmequellen wie direktem Sonnenlicht und Heizkörpern.
* Der Standort darf nicht Feuchtigkeit oder Regen ausgesetzt sein.
* Der Bereich darf nur minimalen Vibrationen oder Stößen ausgesetzt sein.  Das System ist für Stöße und Vibrationen gemäß MIL-STD-810G konzipiert.
* Isoliert von starken elektromagnetischen Feldern, die von elektrischen Geräten erzeugt werden.
* Es dürfen keine Flüssigkeiten oder Fremdkörper in das System gelangen. Stellen Sie keine Getränke oder andere Behälter mit Flüssigkeiten auf das System bzw. in dessen Nähe.

![Warnsymbol 4](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![Symbol „Keine vom Benutzer zu wartenden Teile“](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **VORSICHT:**

* Dieses Gerät enthält einen Lithium-Akku. Versuchen Sie nicht, den Akku selbst zu warten. Die Akkus in diesem Gerät können nicht vom Benutzer gewartet werden. Explosionsgefahr bei Austausch der Batterie durch einen falschen Typ.

![Warnsymbol 5](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **VORSICHT:**

Laden Sie den Akku nur direkt im Azure Stack Edge Mini R-Gerät und nicht als separates Gerät.

![Warnsymbol 6](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **VORSICHT:**

* Der EIN/AUS-Schalter am Akku dient nur zum Trennen des Akkus vom Servermodul. Wenn der Netzadapter an den Akku angeschlossen ist, wird das Servermodul auch dann mit Strom versorgt, wenn sich der Schalter in der AUS-Position befindet.

![Warnsymbol 7](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **VORSICHT:**

* Verbrennen Sie den Akku nicht, und schließen Sie ihn nicht kurz. Er muss ordnungsgemäß recycelt oder entsorgt werden.

![Warnsymbol 8](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **VORSICHT:**

* Dieses System bietet die Möglichkeit, anstelle des bereitgestellten Netzteils einen Akku vom Typ 2590 zu verwenden. In diesem Fall muss der Endbenutzer sicherstellen, dass alle geltenden Sicherheits-, Transport-, Umwelt- und sonstigen nationalen/lokalen Vorschriften eingehalten werden.
* Wenn Sie das System mit einem Akku vom Typ 2590 betreiben, müssen Sie dafür sorgen, dass die Umgebungsbedingungen den Vorgaben des Akkuherstellers entsprechen.

![Warnsymbol 9](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **VORSICHT:**

Dieses Gerät verfügt über zwei SFP+-Ports, die mit optischen Transceivern verwendet werden können. Um Gefahren durch Laserstrahlen zu vermeiden, verwenden Sie ausschließlich Transceiver der Klasse 1.

## <a name="electrical-precautions"></a>Sicherheitsvorkehrungen für die Elektrik

Für das Azure Stack Edge Mini R-Gerät gelten die folgenden Sicherheitsvorkehrungen für die Elektrik:

![Warnsymbol 10](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![Symbol „Stromschlag“](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **WARNUNG:**

Bei Verwendung mit dem Netzadapter:

* Stellen Sie eine sichere elektrische Erdung des Netzkabels her. Das Wechselstromkabel hat einen dreiadrigen Erdungsstecker (einen Stecker mit Erdungsstift). Dieser Stecker passt nur in eine geerdete Steckdose. Umgehen Sie nicht den Zweck des Erdungsstifts.
* Da der Stecker des Netzkabels die Haupttrennvorrichtung ist, stellen Sie sicher, dass sich die Steckdosen in der Nähe des Geräts befinden und leicht zugänglich sind.
* Ziehen Sie die Netzkabel ab (durch Ziehen am Stecker, nicht am Kabel), und trennen Sie alle Kabel, wenn eine der folgenden Bedingungen vorliegt:

  * Das Netzkabel oder der Stecker sind ausgefranst oder anderweitig beschädigt.
  * Das Gerät ist Regen, übermäßiger Feuchtigkeit oder anderen Flüssigkeiten ausgesetzt.
  * Das Gerät wurde fallen gelassen, und das Gerätegehäuse wurde beschädigt.
  * Sie vermuten, dass das Gerät gewartet oder repariert werden muss.
* Trennen Sie die Einheit dauerhaft von der Stromzufuhr, bevor Sie sie bewegen oder wenn Sie glauben, dass sie in irgendeiner Weise beschädigt wurde.

* Stellen Sie eine geeignete Stromquelle mit elektrischem Überlastschutz bereit, um die folgenden Leistungsvorgaben zu erfüllen:

* Spannung: 100–240 V Wechselstrom
* Stromstärke: 1,7 A
* Häufigkeit: 50–60 Hz

![Warnsymbol 11](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![Symbol „Stromschlag“](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **WARNUNG:**

* Versuchen Sie nicht, die mit dem Gerät gelieferten Netzkabel zu modifizieren oder andere Netzkabel zu verwenden.

![Warnsymbol 12](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![Symbol „Stromschlag“](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![Nur für den Gebrauch im Innenbereich](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **WARNUNG:**

* Die mit diesem Symbol gekennzeichnete Stromversorgung ist nur für die Verwendung im Innenbereich vorgesehen.

## <a name="regulatory-information"></a>Rechtliche Informationen

Im Folgenden finden Sie rechtliche Informationen für ein Azure Stack Edge Mini R-Gerät mit der behördlichen Modellnummer: TMA01.

Das Azure Stack Edge Mini R-Gerät wurde für den Einsatz mit NRTL-gelisteten (UL, CSA, ETL usw.) und mit IEC/EN 60950-1- oder IEC/EN 62368-1-konformen IT-Geräten (mit CE-Zeichen) entwickelt.

In anderen Ländern als den USA und Kanada dürfen Netzwerkkabel (die nicht mit den Geräten bereitgestellt werden) mit einer Länge über 3 m nicht für dieses Gerät verwendet werden.

Das Gerät wurde für den Betrieb in folgenden Umgebungen konzipiert:

| Umgebung | Spezifikationen |
|:---  |:--- |
| Spezifikationen zu Systemtemperatur | <ul><li>Lagertemperatur: -20 &deg;C – 50 &deg;C (-4 &deg;F – 122 &deg;F)</li><li>Dauerbetrieb: 0 &deg;C – 40 &deg;C (32 &deg;F – 104 &deg;F)</li></ul> |
| Spezifikationen zur relativen Luftfeuchtigkeit | <ul><li>Speicher: 5–95 % relative Luftfeuchtigkeit</li><li>Betrieb: 10–90 % relative Luftfeuchtigkeit</li></ul>|
| Spezifikationen für die maximale Höhe | <ul><li>Betrieb: 4.572 m (15.000 Fuß)</li><li>Außerhalb des Betriebs: 12.192 m (40.000 Fuß)</li></ul>|

> ![Hinweissymbol 2](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **HINWEIS:** &nbsp;Änderungen oder Modifikationen am Gerät, die nicht ausdrücklich von Microsoft genehmigt wurden, können zum Erlöschen der Betriebserlaubnis des Benutzers für das Gerät führen.

#### <a name="canada-and-usa"></a>Kanada und USA:

> ![Hinweissymbol 3](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **HINWEIS:** &nbsp; Dieses Gerät wurde getestet und entspricht den Grenzwerten für ein Digitalgerät der Klasse A gemäß Teil 15 der FCC-Vorschriften. Diese Grenzwerte dienen dazu, einen angemessenen Schutz vor schädlichen Störungen zu bieten, wenn das Gerät in einer gewerblichen Umgebung betrieben wird. Dieses Gerät erzeugt und nutzt Hochfrequenzenergie und kann diese ausstrahlen. Wenn es nicht gemäß Bedienungsanleitung installiert und eingesetzt wird, kann es zu schädlichen Störungen der Hochfrequenzkommunikation kommen. Der Betrieb dieses Geräts in einem Wohngebiet kann zu schädlichen Störungen führen. In einem solchen Fall ist der Benutzer verpflichtet, die Störungen auf eigene Kosten zu beseitigen.

Der USB-WLAN-Adapter Netgear A6150, der mit diesem Gerät bereitgestellt wird, ist für das Tragen am menschlichen Körper vorgesehen und wurde auf Einhaltung der SAR-Grenzwerte (Specific Absorption Rate, spezifische Absorptionsrate) getestet. Der von der FCC festgelegte SAR-Grenzwert beträgt im Mittel 1,6 W/kg auf 1 g Gewebe. Wenn Sie das Produkt am Körper tragen oder verwenden, halten Sie einen Abstand von 10 mm vom Körper ein, um die HF-Anforderungen einzuhalten.

Der USB-WLAN-Adapter Netgear A6150 entspricht ANSI/IEEE C95.1-1999 und wurde gemäß den Messmethoden und -verfahren getestet, die im OET-Bulletin 65 unter Ergänzung C angegeben sind.

Netgear A6150: spezifische Absorptionsrate (SAR): Im Mittel 1,18 W/kg über 1 g Gewebe

Der USB-WLAN-Adapter Netgear A6150 darf nur mit zugelassenen Antennen verwendet werden. Das Gerät und seine Antennen dürfen nicht in der Nähe von anderen Antennen oder Transmittern oder in Verbindung mit diesen betrieben werden, außer in Übereinstimmung mit den von der FCC festgeschriebenen Verfahren für den Einsatz mehrerer Transmitter. Für Produkte, die in den USA verfügbar sind, können nur die Kanäle 1–11 genutzt werden. Die Auswahl anderer Kanäle ist nicht möglich.

Der Betrieb im Band 5.150–5.250 MHz ist nur für die Verwendung im Innenbereich vorgesehen, um das Risiko von Störungen durch die Nutzung gemeinsamer Kanäle mit Satellitensystemen zu verringern.

![Rechtliche Informationen, Warnung zur Verwendung im Innenbereich](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)


Es wird empfohlen, Hochleistungsradars als primäre Benutzer (Prioritätsbenutzer) der Bänder 5.250–5.350 MHz und 5.650–5.850 MHz zuzuordnen. Diese Radars können zu Interferenzen führen und Schäden bei LE-LAN-Geräten verursachen.

Dieses Gerät erzeugt und nutzt Hochfrequenzenergie und kann diese ausstrahlen. Wenn es nicht gemäß Bedienungsanleitung installiert und eingesetzt wird, kann es zu schädlichen Störungen der Hochfrequenzkommunikation kommen. Es kann jedoch auch bei einer sachgemäßen Installation nicht garantiert werden, dass keine Störungen auftreten.

Wenn dieses Gerät den Radio- oder Fernsehempfang stört (erkennbar durch das Aus- und Einschalten des Geräts), sollten Sie versuchen, die Störungen durch eine oder mehrere der folgenden Maßnahmen zu beheben:

- Ändern Sie die Ausrichtung oder den Standort der Empfangsantenne.
- Vergrößern Sie den Abstand zwischen dem Gerät und dem Empfänger.
- Schließen Sie das Gerät an einen anderen Stromkreis als den Empfänger an.
- Wenden Sie sich für weitere Hilfe an den Händler oder einen versierten Radio-/TV-Techniker.

Weitere Informationen zu Problemen mit Interferenzen finden Sie auf der FCC-Website unter [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). Sie können sich auch telefonisch unter „1-888-CALL FCC“ an die FCC wenden, um Datenblätter zu Störungen und Telefoninterferenzen zu erhalten.

Weitere Informationen zur Sicherheit bei Funkfrequenzen finden Sie auf der FCC-Website unter [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) und auf der Website von Industry Canada unter [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html).

Die EMC-Konformität dieses Geräts wurde unter Bedingungen getestet, in denen kompatible Peripheriegeräte und abgeschirmte Kabel zwischen den Systemkomponenten verwendet wurden. Es ist wichtig, zwischen Systemkomponenten kompatible Peripheriegeräte und abgeschirmte Kabel zu verwenden, um Störungen bei Radios, TV-Geräten und anderen elektronischen Geräten zu vermeiden.

Dieses Gerät entspricht Teil 15 der Lizenzbestimmungen von FCC Rules and Industry Canada – ausgenommen RSS-Standard(s). Der Betrieb unterliegt den beiden folgenden Bedingungen: 1.) Dieses Gerät darf keine schädlichen Störungen verursachen. 2.) Dieses Gerät muss alle empfangenen Störungen akzeptieren, einschließlich Störungen, die einen unerwünschten Betrieb des Geräts verursachen können.

![Rechtliche Informationen, Warnung 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA

USA: (800) 426-9400

Kanada: (800) 933-4750

FCC-ID des Netgear A6150-USB-WLAN-Adapters: PY318300429
 
IC-ID des Netgear A6150-USB-WLAN-Adapters: 4054A-18300429

Der USB-WLAN-Adapter Netgear A6150, der mit diesem Gerät bereitgestellt wird, entspricht dem SAR-Wert für die Verwendung durch die allgemeine Bevölkerung bzw. die unkontrollierte Verwendung laut IC RSS-102 und wurde gemäß den in IEEE 1528 festgelegten Messmethoden und -verfahren getestet. Halten Sie beim Tragen am Körper einen Mindestabstand von 10 mm ein.

Der USB-WLAN-Adapter Netgear A6150 entspricht dem HF-Grenzwert für Kanada in einer unkontrollierten Umgebung und wurde als sicher für den laut Handbuch vorgesehenen Einsatzbereich eingestuft. Um die HF-Strahlenbelastung weiter zu reduzieren, sollten Sie das Produkt so weit wie möglich vom Körper weg halten oder eine niedrigere Ausgabeleistung festlegen, sofern eine solche Funktion verfügbar ist.

Eine Tabelle mit dem SAR-Wert (Specific Absorption Rate, spezifische Absorptionsrate) über einen Mittelwert von 1 g Gewebe für jedes Produkt finden Sie im obigen Abschnitt zu den USA.

![Rechtliche Informationen, Warnung 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>EUROPÄISCHE UNION:

Fordern Sie eine Kopie der EU-Konformitätserklärung für dieses Gerät an.

Der USB-WLAN-Adapter Netgear A6150, der mit diesem Gerät bereitgestellt wird, ist mit der Direktive 2014/53/EU konform, die auf Anforderung bereitgestellt wird.

> ![Warnsymbol 13](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) Dies ist ein Produkt der Klasse A. In einem Privathaushalt kann dieses Produkt Funkstörungen verursachen. In einem solchen Fall kann der Benutzer aufgefordert werden, geeignete Maßnahmen zu ergreifen.

Entsorgung von Altbatterien sowie Elektro- und Elektronikgeräten:

![Warnsymbol 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Dieses Symbol auf dem Produkt oder seinen Batterien oder seiner Verpackung bedeutet, dass dieses Produkt und alle darin enthaltenen Batterien nicht über den Hausmüll entsorgt werden dürfen. Stattdessen liegt es in Ihrer Verantwortung, diese an einer geeigneten Sammelstelle für das Recycling von Batterien und Elektro- und Elektronikgeräten abzugeben. Diese getrennte Sammlung und Verwertung trägt dazu bei, natürliche Ressourcen zu schonen und mögliche negative Folgen für die menschliche Gesundheit und die Umwelt zu vermeiden, die sich aus dem möglichen Vorhandensein gefährlicher Stoffe in Batterien und Elektro- und Elektronikgeräten ergeben können, die durch unsachgemäße Entsorgung verursacht werden könnten. Weitere Informationen dazu, wo Sie Ihre Batterien und Ihren Elektro- und Elektronikschrott entsorgen können, erhalten Sie bei Ihrer Stadtverwaltung, Ihrem Hausmüllentsorger oder dem Geschäft, in dem Sie dieses Produkt gekauft haben. Kontaktieren Sie erecycle@microsoft.com, um weitere Informationen über die Entsorgung von Elektro- und Elektronikgeräten zu erhalten.

Dieses Produkt enthält Knopfzellenbatterien.

Der USB-WLAN-Adapter Netgear A6150, der mit diesem Gerät bereitgestellt wird, ist für das Tragen am menschlichen Körper vorgesehen und wurde auf Einhaltung der SAR-Grenzwerte (Specific Absorption Rate, spezifische Absorptionsrate) für am Körper getragene Geräte getestet. (Die Werte finden Sie weiter unten.) Wenn Sie das Produkt am Körper tragen oder verwenden, halten Sie einen Abstand von 10 mm vom Körper ein, um die HF-Anforderungen einzuhalten.

**Netgear A6150: spezifische Absorptionsrate (SAR):** Im Mittel 0,54 W/kg über 10 g Gewebe

 
Dieses Gerät darf in allen Mitgliedsstaaten der EU betrieben werden. Beachten Sie die nationalen und lokalen Vorschriften am Einsatzort des Geräts. Dieses Gerät darf in den folgenden Ländern nur in Innenräumen und im Frequenzbereich von 5.150–5.350 MHz betrieben werden:  

![EU-Länder mit Einschränkung auf den Gebrauch im Innenbereich](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

Die folgende Tabelle enthält die Frequenzbänder, die gemäß Artikel 10.8(a) und 10.8(b) der RED genutzt werden dürfen, sowie die maximale HF-Sendeleistung von drahtlosen Netgear-Produkten, die für den Verkauf in der EU zugelassen sind:

**WiFi**

| Frequenzbereich (MHz) | Verwendete Kanäle | Maximale Sendeleistung (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400–2483,5 | 1–13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5\.150–5.320   | 36–48   | 22,9 dBm (195 mW) |
| 5250–5350   | 52–64   | 22,9 dBm (195 mW) mit TPC <br> 19,9 dBm (97,7 mW) ohne TPC |
| 5470–5725   | 100–140 | 29,9 dBm (977 mW) mit TPC <br> 29,6 dBm (490 mW) ohne TPC |

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Rufnummer: +353 1 295 3826

Faxnummer: +353 1 706 4110

#### <a name="singapore"></a>SINGAPUR:

Der Netgear A6150-USB-WLAN-Adapter, der mit diesem Gerät bereitgestellt wird, erfüllt die IMDA-Standards.


## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md)
