---
title: Azure Stack Edge Mini R – Sicherheitsleitfaden | Microsoft-Dokumentation
description: Beschreibt Sicherheitskonventionen, Richtlinien und Überlegungen und erläutert, wie Sie Ihr Azure Stack Edge Mini R-Gerät sicher installieren und betreiben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 507ceef0f13951eafdcb02d586f35c1d61764c4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465480"
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

> ![Symbol „Hinweis“](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **HINWEIS:** &nbsp;Änderungen oder Modifikationen am Gerät, die nicht ausdrücklich von Microsoft genehmigt wurden, können zum Erlöschen der Betriebserlaubnis des Benutzers für das Gerät führen.

Kanada und USA:

HINWEIS: Dieses Gerät wurde getestet und entspricht den Grenzwerten für ein Digitalgerät der Klasse A gemäß Teil 15 der FCC-Vorschriften. Diese Grenzwerte dienen dazu, einen angemessenen Schutz vor schädlichen Störungen zu bieten, wenn das Gerät in einer gewerblichen Umgebung betrieben wird. Dieses Gerät erzeugt und nutzt Hochfrequenzenergie und kann diese ausstrahlen. Wenn es nicht gemäß Bedienungsanleitung installiert und eingesetzt wird, kann es zu schädlichen Störungen der Hochfrequenzkommunikation kommen. Der Betrieb dieses Geräts in einem Wohngebiet kann zu schädlichen Störungen führen. In einem solchen Fall ist der Benutzer verpflichtet, die Störungen auf eigene Kosten zu beseitigen.

Dieses Gerät entspricht Teil 15 der Lizenzbestimmungen von FCC Rules and Industry Canada – ausgenommen RSS-Standard(s). Der Betrieb unterliegt den beiden folgenden Bedingungen: 1.) Dieses Gerät darf keine schädlichen Störungen verursachen. 2.) Dieses Gerät muss alle empfangenen Störungen akzeptieren, einschließlich Störungen, die einen unerwünschten Betrieb des Geräts verursachen können.

![Rechtliche Informationen, Warnung 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.
USA: (800) 426-9400, Kanada: (800) 933-4750

EUROPÄISCHE UNION: Eine Kopie der EU-Konformitätserklärung anfordern.

> ![Warnsymbol 13](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) Dies ist ein Produkt der Klasse A. In einem Privathaushalt kann dieses Produkt Funkstörungen verursachen. In einem solchen Fall kann der Benutzer aufgefordert werden, geeignete Maßnahmen zu ergreifen.

Entsorgung von Altbatterien sowie Elektro- und Elektronikgeräten:

![Warnsymbol 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Dieses Symbol auf dem Produkt oder seinen Batterien oder seiner Verpackung bedeutet, dass dieses Produkt und alle darin enthaltenen Batterien nicht über den Hausmüll entsorgt werden dürfen. Stattdessen liegt es in Ihrer Verantwortung, diese an einer geeigneten Sammelstelle für das Recycling von Batterien und Elektro- und Elektronikgeräten abzugeben. Diese getrennte Sammlung und Verwertung trägt dazu bei, natürliche Ressourcen zu schonen und mögliche negative Folgen für die menschliche Gesundheit und die Umwelt zu vermeiden, die sich aus dem möglichen Vorhandensein gefährlicher Stoffe in Batterien und Elektro- und Elektronikgeräten ergeben können, die durch unsachgemäße Entsorgung verursacht werden könnten. Weitere Informationen dazu, wo Sie Ihre Batterien und Ihren Elektro- und Elektronikschrott entsorgen können, erhalten Sie bei Ihrer Stadtverwaltung, Ihrem Hausmüllentsorger oder dem Geschäft, in dem Sie dieses Produkt gekauft haben. Kontaktieren Sie erecycle@microsoft.com, um weitere Informationen über die Entsorgung von Elektro- und Elektronikgeräten zu erhalten.

Dieses Produkt enthält Knopfzellenbatterien.
Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL, Telefonnummer: +353 1 295 3826, Faxnummer: +353 1 706 4110

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md)
