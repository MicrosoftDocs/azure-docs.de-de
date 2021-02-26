---
title: Azure Stack Edge Pro R – Sicherheitsleitfaden | Microsoft-Dokumentation
description: Beschreibt Sicherheitskonventionen, Richtlinien und Überlegungen und erläutert, wie Sie Ihr Azure Stack Edge Pro R-Gerät sicher installieren und betreiben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: cc24839396116a5b581758b29b087c237a6f902b
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981339"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Sicherheitsanweisungen für Azure Stack Edge Pro R

![Symbol "Warnung"](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![Symbol "Sicherheitshinweis lesen"](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
**LESEN SIE DIE INFORMATIONEN ZUR SICHERHEIT UND INTEGRITÄT**

Lesen Sie alle Sicherheitshinweise in diesem Artikel, bevor Sie Ihr Azure Stack Edge Pro R-Gerät verwenden. Die Nichtbeachtung von Anweisungen kann zu Bränden, Stromschlägen, Verletzungen oder Schäden an Ihren Systemen führen. Lesen Sie alle unten aufgeführten Sicherheitsinformationen, bevor Sie Azure Stack Edge Pro R verwenden.

## <a name="safety-icon-conventions"></a>Konventionen für Sicherheitssymbole

Die folgenden Signalwörter werden für Gefahrenwarnungen verwendet:

| Symbol | BESCHREIBUNG |
|:--- |:--- |
| ![Gefahrensymbol](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **GEFAHR:** Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen wird, wenn sie nicht vermieden wird. <br> **WARNUNG:** Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen könnte, wenn sie nicht vermieden wird. <br> **VORSICHT:** Zeigt eine Gefahrensituation an, die zu leichten bis mittelschweren Verletzungen führen könnte, wenn sie nicht vermieden wird.|
|

Beim Einrichten und Ausführen des Azure Stack Edge Pro R-Geräts sind die folgenden Gefahrensymbole zu beachten:

| Symbol | BESCHREIBUNG |
|:--- |:--- |
| ![Alle Anleitungen zuvor lesen](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | Alle Anleitungen zuvor lesen |
| ![Symbol „Hinweis“](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **HINWEIS:** | Zeigt als wichtig erachtete Informationen an, die allerdings nicht mit Gefahren in Verbindung stehen. || ![Gefahrensymbol](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | Gefahrensymbol |
| ![Symbol "Kippgefahr"](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | Kippgefahr|
| ![Symbol "Hohes Gewicht"](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | Gefahr „Hohes Gewicht“|
| ![Symbol "Stromschlag"](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | Gefahr eines Stromschlags |
| ![Symbol "Keine vom Benutzer zu wartenden Teile"](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | Keine vom Benutzer zu wartenden Teile Zugriff nur bei ausreichender Schulung. |
| ![Symbol „Mehrere Stromquellen“](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | Mehrere Stromquellen Ziehen Sie alle Netzkabel ab, um die gesamte Stromversorgung des Geräts zu entfernen. |
| ![Symbol „Quetchkanten“](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | Es gibt Quetchkanten. |
| ![Symbol „Heiße Komponenten oder Oberflächen“](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | Weist auf heiße Komponenten oder Oberflächen hin. |
|

## <a name="handling-precautions-and-site-selection"></a>Vorsichtsmaßnahmen für die Arbeit und Standortauswahl

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **WARNUNG:**

* Geeignete Ausrüstung (z. B. Hubwagen) und persönliche Schutzausrüstung (z. B. Handschuhe) müssen beim Bewegen und Handhaben der Transportkiste verwendet werden.

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![Symbol „Kippgefahr“](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **WARNUNG:**

* Stellen Sie das Gerät auf eine ebene, harte und stabile Oberfläche, um eine mögliche Kipp- oder Quetschgefahr zu vermeiden.
* Stapeln Sie nicht mehr als zwei Geräte übereinander.
* Stellen Sie vor dem Stapeln sicher, dass das System sicher ist.
* Versetzen oder verschieben Sie gestapelte Geräte nicht.
* Stapeln Sie keine Geräte, die über externe Kabel mit Strom versorgt werden.
* Stellen Sie sicher, dass Stromkabel bei Stapelvorgängen nicht gequetscht oder beschädigt werden.
* Geräte dürfen nicht als Tisch oder Arbeitsbereich verwendet werden.

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![Symbol „Stromschlag“](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
![Symbol „Keine vom Benutzer zu wartenden Teile“](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **VORSICHT:**

* Überprüfen Sie das *empfangene* Gerät auf Schäden. Wenn das Gerätegehäuse beschädigt ist, wenden Sie sich an den [Microsoft-Support](azure-stack-edge-contact-microsoft-support.md), um ein Austauschgerät zu erhalten. Versuchen Sie nicht, das Gerät in Betrieb zu nehmen.
* Wenn Sie vermuten, dass das Gerät defekt ist, wenden Sie sich an den [Microsoft-Support](azure-stack-edge-contact-microsoft-support.md), um ein Austauschgerät zu erhalten. Versuchen Sie nicht, das Gerät zu warten.
* Das Gerät enthält keine vom Benutzer zu wartenden Teile. Im Inneren sind gefährliche Spannungs-, Strom- und Leistungspegel vorhanden. Öffnen Sie es nicht. Senden Sie das Gerät zur Wartung an Microsoft zurück.

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![Symbol „Hohes Gewicht“](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **WARNUNG:**

* Durch das Entfernen des Stromversorgungsmoduls der unterbrechungsfreien Stromversorgung (USV) werden spannungsführende Teile innerhalb der USV freigelegt. Führen Sie keine Fremdkörper in das Fach des Stromversorgungsmoduls ein.
* Versuchen Sie nicht, ein Azure Stack Edge Pro R Edge-Gerät selbst anzuheben. Ein Gehäuse kann zwischen 52 kg und 93 kg wiegen. Beim Bewegen und Heben von Geräten ist daher mechanische Hilfe oder ein anderes geeignetes Hilfsmittel zu verwenden. Beachten Sie beim Bewegen und Heben von Geräten die örtlichen Arbeitsschutzbestimmungen.
* Versuchen Sie nicht, das Gerät ohne entsprechende mechanische Hilfsmittel anzuheben. Beachten Sie, dass Versuche, dieses Gewicht anzuheben, schwere Verletzungen verursachen können.

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **WARNUNG:**

* Das System ist für den Betrieb in einer kontrollierten Umgebung konzipiert. Wählen Sie einen Standort aus, auf den Folgendes zutrifft:
  * Gut belüftet und fernab von Wärmequellen wie direktem Sonnenlicht und Heizkörpern.
  * Weder Feuchtigkeit noch Regen ausgesetzt.
  * Der Bereich darf nur minimalen Vibrationen oder Stößen ausgesetzt sein.  Das System ist für Stöße und Vibrationen gemäß MIL-STD-810G konzipiert.
  * Isoliert von starken elektromagnetischen Feldern, die von elektrischen Geräten erzeugt werden.
  * Ausgestattet mit ordnungsgemäß geerdeten Steckdosen.
  * Ausreichend Platz für den Zugang zu den Netzkabeln, da diese als Haupttrennschalter für das Produkt dienen.
* Ethernet-Kabel sind nicht im Lieferumfang des Produkts enthalten. Um elektromagnetische Störungen zu verringern, wird empfohlen, eine STP-Verkabelung (Shielded Twisted Pair) der Kategorie 6 zu verwenden.
* Stellen Sie das Gerät in einem Arbeitsbereich auf, der eine ausreichende Luftzirkulation um das Gerät ermöglicht. Stellen Sie sicher, dass die vordere und hintere Abdeckung vollständig entfernt wird, während das Gerät in Betrieb ist.
* Ethernet-Kabel sind nicht im Lieferumfang des Produkts enthalten. Um elektromagnetische Störungen zu verringern, wird empfohlen, ein abgeschirmtes CAT 6-Kabel (STP, Shielded Twisted Pair) zu verwenden.
* Installieren Sie das Gerät in einem klimatisierten Bereich ohne leitfähige Verunreinigungen, und sorgen Sie für ausreichende Luftzirkulation um das Gerät.
* Halten Sie das Gerät von Flüssigkeitsquellen und übermäßig feuchten Umgebungen fern.
* Es dürfen keine Flüssigkeiten oder Fremdkörper in das System gelangen. Stellen Sie keine Getränke oder andere Behälter mit Flüssigkeiten auf das System bzw. in dessen Nähe.

## <a name="heater-precautions"></a>Vorsichtsmaßnahmen für das Heizgerät

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![Symbol 1 „Heiße Komponenten oder Oberflächen“](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **WARNUNG:** 

* Der automatische Betrieb des Heizgeräts bei eingeschaltetem System kann aufgrund hoher Oberflächentemperaturen an der Abdeckung der Heizelementbaugruppe eine Berührungsgefahr darstellen. Berühren Sie diese Oberfläche nicht, während das System eingeschaltet ist. Gestatten Sie eine 10-minütige Abkühlphase nach dem Ausschalten des Systems.

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![Symbol 1 „Quetchkanten“](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **WARNUNG:** 

* Wenn das System eingeschaltet ist, kann die automatische Betätigung der hinteren Verteilerklappe eine Gefahr durch Quetchkanten bedeuten. Halten Sie die Hände von diesem Bereich fern, wenn das System eingeschaltet ist.

## <a name="electrical-precautions"></a>Sicherheitsvorkehrungen für die Elektrik

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![Symbol „Vorsicht Stromschlag“](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **WARNUNG:**

* Stellen Sie eine sichere elektrische Erdung des Netzkabels her. Das Wechselstromkabel hat einen dreiadrigen Erdungsstecker (einen Stecker mit Erdungsstift). Dieser Stecker passt nur in eine geerdete Steckdose. Umgehen Sie nicht den Zweck des Erdungsstifts.
* Da der Stecker des Netzkabels die Haupttrennvorrichtung ist, stellen Sie sicher, dass sich die Steckdosen in der Nähe des Geräts befinden und leicht zugänglich sind.
* Ziehen Sie die Netzkabel ab (durch Ziehen am Stecker, nicht am Kabel), und trennen Sie alle Kabel, wenn eine der folgenden Bedingungen vorliegt:

  * Das Netzkabel oder der Stecker sind ausgefranst oder anderweitig beschädigt.
  * Sie verschütten etwas in das Gerätegehäuse.
  * Das Gerät ist Regen oder übermäßiger Feuchtigkeit ausgesetzt.
  * Das Gerät wurde fallen gelassen und das Gerätegehäuse beschädigt.
  * Sie vermuten, dass das Gerät gewartet oder repariert werden muss.
* Trennen Sie die Einheit dauerhaft von der Stromzufuhr, bevor Sie sie bewegen oder wenn Sie glauben, dass sie in irgendeiner Weise beschädigt wurde.
* Um hohe Ableitströme zu vermeiden, wird bei einer einzelnen Transportbox mit mehreren unterbrechungsfreien Stromversorgungen (USV) empfohlen, jede USV an eine unabhängige Verzweigungsleitung anzuschließen. Falls jedoch eine Stromverteilungseinheit (Power Distribution Unit, PDU) oder ein anderes Gerät verwendet wird, bei dem die Sicherheitserdung jeder USV von einem einzelnen Erdungsleiter an der Zuleitung der PDU abhängt, muss die Erdungsklemme an der Außenseite jeder USV auch mit einem zusätzlichen Erdungsleiter des Gebäudes verwendet werden.

  > [!NOTE]
  > Wird eine PDU verwendet, die bereits über einen zusätzlichen Erdungsleiter verfügt, ist die Verwendung der zusätzlichen Erdungsklemme an der USV nicht erforderlich.

* Stellen Sie eine geeignete Stromquelle mit elektrischem Überlastschutz bereit, um die folgenden Leistungsvorgaben zu erfüllen:

  * Spannung: 100–240 V (Wechselstrom, AC)
  * Stromstärke: Maximal 20 A pro Netzkabel Netzkabel werden bereitgestellt.
  * Häufigkeit: 50–60 Hz

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![Symbol „Stromschlag“](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
![Mehrere Stromquellen](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **WARNUNG:**

* Bei Systemen ohne unterbrechungsfreie Stromversorgung (USV) ziehen Sie alle Netzkabel aus der Steckdose, um das Gerät vollständig vom Netz zu trennen.
* Ziehen Sie bei Systemen mit USV alle Netzkabel aus der Steckdose und verwenden Sie den USV-Netzschalter, um die gesamte Stromversorgung vom System zu trennen. Die USV enthält gefährliche Spannungen (Gleich- und Wechselstrom).

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![Symbol „Stromschlag“](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **WARNUNG:**

* Bei Systemen, die mit einer USV ausgestattet sind, birgt die Wechsel- und/oder Gleichspannung am USV-Ausgang, die entweder von Akkus oder Stromversorgung erzeugt wird, immer ein potenzielles Risiko der Wechselspannung. Um Schäden an Geräten oder Verletzungen zu vermeiden, gehen Sie immer davon aus, dass am Ausgang der USV Spannung anliegen kann, auch wenn sie von der primären Stromquelle getrennt ist.
* Bei Systemen, die mit USV ausgestattet sind, sind alle USV-gespeisten externen Anschlüsse Buchsen. Entfernen Sie nicht das Gehäuse, und stecken Sie nichts in diese oder andere Anschlüsse der USV.

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![Symbol „Stromschlag“](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **WARNUNG:**

* Versuchen Sie nicht, die mit dem Gerät gelieferten Netzkabel zu modifizieren oder andere Netzkabel zu verwenden.

![Symbol „Warnung“](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![Symbol „Keine vom Benutzer zu wartenden Teile“](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **VORSICHT:**

* Dieses Gerät enthält Lithium-Knopfzellen und/oder Lithium-Eisenphosphat-Akkus. Versuchen Sie nicht, die Geräte zu warten. Die Akkus in diesem Gerät können nicht vom Benutzer gewartet werden. Explosionsgefahr, wenn die Batterie durch einen falschen Typ ersetzt wird.
* Durch das Entfernen des Akkumoduls der unterbrechungsfreien Stromversorgung (USV) werden spannungsführende Teile innerhalb der USV freigelegt. Führen Sie keine Fremdkörper in das Fach des Akkumoduls ein.

## <a name="regulatory-information"></a>Rechtliche Informationen

Dieser Abschnitt enthält rechtliche Informationen für ein Azure Stack Edge Pro R-Gerät mit der behördlichen Modellnummer: Azure Stack Edge Pro R.

Das Azure Stack Edge Pro R Edge-Gerät wurde für den Einsatz mit NRTL-gelisteten (UL, CSA, ETL usw.) und mit IEC/EN 60950-1- oder IEC/EN 62368-1-konformen IT-Geräten (mit CE-Zeichen) entwickelt.

Das Gerät wurde für den Betrieb in folgenden Umgebungen konzipiert:

| Umgebung | Spezifikationen |
|:--- |:--- |
|Temperaturspezifikationen | <ul><li>Lagertemperatur: –33&deg;C–63&deg;C (–28&deg;F-145&deg;F) </li><li>Dauerbetrieb: 5&deg;C–43&deg;C (41&deg;F–110&deg;F)</li><li>Maximaler Temperaturverlauf (Betrieb und Lagerung): 20&deg;C/Std. (68&deg;F/Std.)</li></ul> |
|Spezifikationen zur relativen Luftfeuchtigkeit | <ul><li>Speicher: 5 % bis 95 % relative Luftfeuchtigkeit mit 33&deg;C (91&deg;F) maximalem Taupunkt. Die Atmosphäre darf zu keiner Zeit kondensierend sein.</li><li>In Betrieb: 5 % bis 85 % relative Feuchtigkeit mit einem maximalen Taupunkt von 29&deg;C (84,2&deg;F)</li></ul> |
| Spezifikationen für die maximale Höhe | <ul><li>Betrieb (ohne USV): 4.572 m</li><li>Betrieb (mit USV): 3.048 m</li><li>Speicher: 12.192 m</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![Hinweissymbol 2](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **HINWEIS:** &nbsp;Änderungen oder Modifikationen am Gerät, die nicht ausdrücklich von Microsoft genehmigt wurden, können zum Erlöschen der Betriebserlaubnis des Benutzers für das Gerät führen.

Kanada und USA:

HINWEIS: Dieses Gerät wurde getestet und entspricht den Grenzwerten für ein Digitalgerät der Klasse A gemäß Teil 15 der FCC-Vorschriften. Diese Grenzwerte dienen dazu, einen angemessenen Schutz vor schädlichen Störungen zu bieten, wenn das Gerät in einer gewerblichen Umgebung betrieben wird. Dieses Gerät erzeugt und nutzt Hochfrequenzenergie und kann diese ausstrahlen. Wenn es nicht gemäß Bedienungsanleitung installiert und eingesetzt wird, kann es zu schädlichen Störungen der Hochfrequenzkommunikation kommen. Der Betrieb dieses Geräts in einem Wohngebiet kann zu schädlichen Störungen führen. In einem solchen Fall ist der Benutzer verpflichtet, die Störungen auf eigene Kosten zu beseitigen.

Dieses Gerät entspricht Teil 15 der Lizenzbestimmungen von FCC Rules and Industry Canada – ausgenommen RSS-Standard(s). Der Betrieb unterliegt den beiden folgenden Bedingungen: 1.) Dieses Gerät darf keine schädlichen Störungen verursachen. 2.) Dieses Gerät muss alle empfangenen Störungen akzeptieren, einschließlich Störungen, die einen unerwünschten Betrieb des Geräts verursachen können.

![Rechtliche Informationen, Warnung 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.
USA: (800) 426-9400, Kanada: (800) 933-4750

EUROPÄISCHE UNION: Eine Kopie der EU-Konformitätserklärung anfordern. 

![Warnsymbol](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)

Dies ist ein Produkt der Klasse A. In einem Privathaushalt kann dieses Produkt Funkstörungen verursachen. In einem solchen Fall kann der Benutzer aufgefordert werden, geeignete Maßnahmen zu ergreifen.

Entsorgung von Altbatterien sowie Elektro- und Elektronikgeräten:

![Warnsymbol 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Dieses Symbol auf dem Produkt oder seinen Batterien oder seiner Verpackung bedeutet, dass dieses Produkt und alle darin enthaltenen Batterien nicht über den Hausmüll entsorgt werden dürfen. Stattdessen liegt es in Ihrer Verantwortung, diese an einer geeigneten Sammelstelle für das Recycling von Batterien und Elektro- und Elektronikgeräten abzugeben. Diese getrennte Sammlung und Verwertung trägt dazu bei, natürliche Ressourcen zu schonen und mögliche negative Folgen für die menschliche Gesundheit und die Umwelt zu vermeiden, die sich aus dem möglichen Vorhandensein gefährlicher Stoffe in Batterien und Elektro- und Elektronikgeräten ergeben können, die durch unsachgemäße Entsorgung verursacht werden könnten. Weitere Informationen dazu, wo Sie Ihre Batterien und Ihren Elektro- und Elektronikschrott entsorgen können, erhalten Sie bei Ihrer Stadtverwaltung, Ihrem Hausmüllentsorger oder dem Geschäft, in dem Sie dieses Produkt gekauft haben. Kontaktieren Sie erecycle@microsoft.com, um weitere Informationen über die Entsorgung von Elektro- und Elektronikgeräten zu erhalten.

Dieses Produkt enthält Knopfzellenbatterien.

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL, Telefonnummer: +353 1 295 3826, Faxnummer: +353 1 706 4110



## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Stack Edge Pro R Edge](azure-stack-edge-pro-r-deploy-prep.md)
