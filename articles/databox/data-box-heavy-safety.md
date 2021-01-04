---
title: Sicherheit für Azure Data Box Heavy | Microsoft-Dokumentation
description: Beschreibt Sicherheitskonventionen, Richtlinien und Überlegungen und erläutert, wie Azure Data Box Heavy sicher installiert und betrieben werden kann.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/08/2019
ms.author: alkohli
ms.openlocfilehash: d2c7f729f4accf7c9ca212c6e4b4b5d565c3114c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021536"
---
# <a name="safely-install-and-operate-your-azure-data-box-heavy"></a>Sicheres Installieren und Betreiben von Azure Data Box Heavy

Dieser Artikel enthält Sicherheitsinformationen zu Azure Data Box Heavy.

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png)
![Symbol „Sicherheitshinweis lesen“](./media/data-box-heavy-safety/read-safety-and-health-information-icon.png) **Alle Anleitungen zuvor lesen**

Lesen Sie alle Sicherheitshinweise in diesem Artikel, bevor Sie Azure Data Box Heavy verwenden. Die Nichtbeachtung von Anweisungen kann zu Bränden, Stromschlägen oder anderen Verletzungen oder Schäden an Ihren Systemen führen.

## <a name="safety-icon-conventions"></a>Konventionen für Sicherheitssymbole
Die folgenden Symbole finden Sie in den Sicherheitsvorkehrungen, die beachtet werden müssen, wenn Sie Data Box einrichten und ausführen.

| Symbol | Beschreibung |
|:--- |:--- |
| ![Gefahrensymbol](./media/data-box-heavy-safety/warning-icon.png) **GEFAHR!** |Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen wird, wenn sie nicht vermieden wird. Dieses Signalwort ist auf extremste Situationen zu beschränken. |
| ![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) **WARNUNG!** |Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen könnte, wenn sie nicht vermieden wird. |
| ![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) **ACHTUNG!** |Zeigt eine Gefahrensituation an, die zu leichten bis mittelschweren Verletzungen führen könnte, wenn sie nicht vermieden wird. |
| ![Hinweissymbol](./media/data-box-heavy-safety/notice-icon.png) **HINWEIS:** |Zeigt als wichtig erachtete Informationen an, die allerdings nicht mit Gefahren in Verbindung stehen. |
| ![Symbol „Stromschlag“](./media/data-box-heavy-safety/electrical-shock-hazard-icon.png) **Gefahr durch Stromschlag** |Hochspannung. |
| ![Symbol „Hohes Gewicht“](./media/data-box-heavy-safety/heavy-weight-hazard-icon.png) **Hohes Gewicht** | |
| ![Symbol „Keine vom Benutzer zu wartenden Teile“](./media/data-box-heavy-safety/no-user-serviceable-parts-icon.png) **Keine vom Benutzer zu wartenden Teile** |Zugriff nur bei ausreichender Schulung. |
| ![Symbol „Sicherheitshinweis lesen“](./media/data-box-heavy-safety/read-safety-and-health-information-icon.png) **Alle Anleitungen zuvor lesen** | |
| ![Symbol „Kippgefahr“](./media/data-box-heavy-safety/tip-hazard-icon.png) **Kippgefahr** | |
| ![Symbol „Überlastkippgefahr“](./media/data-box-heavy-safety/overload-tip-hazard-icon.png) **Überlastkippgefahr** | |
| ![Symbol „Mehrere Stromquellen“](./media/data-box-heavy-safety/multiple-power-sources-icon.png) **Mehrere Stromquellen** | |

## <a name="handling-precautions"></a>Vorsichtsmaßnahmen bei der Handhabung

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) **WARNUNG!**

* Beim Bewegen und Handhaben der Transportkiste sind die richtige Ausrüstung (z.B. Hubwagen) und persönliche Schutzausrüstung (z.B. Handschuhe) zu verwenden.
* Die Rampe muss mit den mitgelieferten Schrauben an der Kiste befestigt werden, bevor Sie das Gerät entfernen, um Verletzungen oder Sachschäden zu vermeiden.

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) ![Symbol „Kippgefahr“](./media/data-box-heavy-safety/tip-hazard-icon.png) **Kippgefahr**

* Stellen Sie das Gerät auf eine ebene, harte und stabile Oberfläche, um eine mögliche Kipp- oder Quetschgefahr zu vermeiden.
* Vergewissern Sie sich, dass die Rollen arretiert sind, bevor Sie das Gerät überprüfen, einschalten und bedienen.

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) ![Symbol „Stromschlag“](./media/data-box-heavy-safety/electrical-shock-hazard-icon.png)![Symbol „Keine vom Benutzer zu wartenden Teile“](./media/data-box-heavy-safety/no-user-serviceable-parts-icon.png) **VORSICHT!** 

* Überprüfen Sie das *empfangene* Gerät auf Schäden. Wenn das Gerätegehäuse beschädigt ist, wenden Sie sich an den [Microsoft-Support](data-box-disk-contact-microsoft-support.md), um ein Austauschgerät zu erhalten. Versuchen Sie nicht, das Gerät in Betrieb zu nehmen. 
* Das Gerät ist mit manipulationsgeschützten Schrauben ausgestattet. Wenn Sie vermuten, dass das Gerät defekt ist, wenden Sie sich an den [Microsoft-Support](data-box-disk-contact-microsoft-support.md), um ein Austauschgerät zu erhalten. Versuchen Sie nicht, das Gerät zu warten. 
* Das Gerät enthält keine vom Benutzer zu wartenden Teile. Im Inneren sind gefährliche Spannungs-, Strom- und Leistungspegel vorhanden. Öffnen Sie es nicht. Senden Sie das Gerät zur Wartung an Microsoft zurück.

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) ![Symbol „Hohes Gewicht“](./media/data-box-heavy-safety/heavy-weight-hazard-icon.png) **WARNUNG!** 

* Ein vollständig konfiguriertes Gehäuse kann bis zu 326 kg wiegen. Versuchen Sie nicht, es allein zu heben.
* Versuchen Sie nicht, das Gerät ohne entsprechende mechanische Hilfsmittel anzuheben. Beachten Sie, dass Versuche, dieses Gewicht anzuheben, schwere Verletzungen verursachen können.
* Beachten Sie beim Bewegen und Heben dieses Geräts die örtlichen Arbeitsschutzbestimmungen.
* Verwenden Sie beim Bewegen und Heben von Geräten mechanische oder andere geeignete Hilfsmittel.


![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) ![Symbol „Überlastkippgefahr“](./media/data-box-heavy-safety/overload-tip-hazard-icon.png) ![Symbol „Kippgefahr“](./media/data-box-heavy-safety/tip-hazard-icon.png)![Symbol „Hohes Gewicht“](./media/data-box-heavy-safety/heavy-weight-hazard-icon.png) **WARNUNG!**
* Data Box Heavy darf nicht als Tisch oder Arbeitsfläche verwendet werden. Das Hinzufügen jeglicher Art von Last kann eine potenzielle Gefahr darstellen, die zu Verletzungen oder Sachschäden führen kann.
* In Racks eingebaute Geräte dürfen nicht als Ablage oder Arbeitsbereich verwendet werden. Stellen Sie Data Box Heavy nicht auf in Racks eingebaute Geräte. Das Aufbringen jeglicher Art von Last auf eine ausgefahrene im Rack eingebaute Einheit kann eine potenzielle Kippgefahr darstellen, die zu Verletzungen, Tod oder Produktschäden führen kann.

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) **WARNUNG!**

* Das System ist für den Betrieb in einer typischen Büroumgebung konzipiert. Wählen Sie einen Standort aus, auf den Folgendes zutrifft:

    - Gut belüftet und fernab von Wärmequellen wie direktem Sonnenlicht und Heizkörpern.
    - Abseits von Vibrationen oder physischen Erschütterungen.
    - Isoliert von starken elektromagnetischen Feldern, die von elektrischen Geräten erzeugt werden.
    - Ausgestattet mit ordnungsgemäß geerdeten Steckdosen.
    - Ausreichend Platz für den Zugang zu den Netzkabeln, da diese als Haupttrennschalter für das Produkt dienen.

* Richten Sie das Gerät in einem Arbeitsbereich ein, der eine ausreichende Luftzirkulation um das Gerät gewährleistet.
* Installieren Sie das Gerät in einem klimatisierten Innenbereich ohne leitfähige Verunreinigungen, und sorgen Sie für ausreichende Luftzirkulation um das Gerät.
* Halten Sie das Gerät von Flüssigkeitsquellen und übermäßig feuchten Umgebungen fern.


## <a name="electrical-precautions"></a>Sicherheitsvorkehrungen für die Elektrik

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) ![Symbol „Stromschlag“](./media/data-box-heavy-safety/electrical-shock-hazard-icon.png) **WARNUNG!**

* Stellen Sie eine sichere elektrische Erdung des Netzkabels her. Das Netzkabel hat einen dreiadrigen Erdungsstecker (einen Stecker mit Erdungsstift). Dieser Stecker passt nur in eine geerdete Steckdose. Umgehen Sie nicht den Zweck des Erdungsstifts.
* Da der Stecker des Netzkabels die Haupttrennvorrichtung ist, stellen Sie sicher, dass sich die Steckdosen in der Nähe des Geräts befinden und leicht zugänglich sind.
* Ziehen Sie das Netzkabel ab (durch Ziehen des Steckers, nicht des Kabels), und trennen Sie alle Kabel, wenn eine der folgenden Bedingungen vorliegt:

    - Das Netzkabel oder der Stecker sind ausgefranst oder anderweitig beschädigt.
    - Sie verschütten etwas in das Gerätegehäuse.
    - Das Gerät ist Regen oder übermäßiger Feuchtigkeit ausgesetzt.
    - Das Gerät wurde fallen gelassen und das Gerätegehäuse beschädigt.
    - Sie vermuten, dass das Gerät gewartet oder repariert werden muss.
* Trennen Sie die Einheit dauerhaft von der Stromzufuhr, bevor Sie sie bewegen oder wenn Sie glauben, dass sie in irgendeiner Weise beschädigt wurde.
* Stellen Sie eine geeignete Stromquelle mit elektrischem Überlastschutz bereit, um die folgenden Leistungsvorgaben zu erfüllen:

    - Spannung: 100 V bis 240 V Wechselstrom
    - Stromstärke: Maximal 6 A bis 10 A pro Netzkabel. Vier Netzkabel werden bereitgestellt.
    - Häufigkeit: 50 bis 60 Hz
* Versuchen Sie nicht, die mit dem Gerät gelieferten Netzkabel zu modifizieren oder andere Netzkabel zu verwenden. Die Netzkabel müssen die folgenden Kriterien erfüllen:
    - Das Netzkabel muss eine elektrische Leistung aufweisen, die größer ist als die des auf dem Produkt angegebenen elektrischen Nennstroms.
    - Das Netzkabel muss mit einem für die Steckdose geeigneten Erdungsstift oder -kontakt versehen sein.

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) ![Symbol „Stromschlag“](./media/data-box-heavy-safety/electrical-shock-hazard-icon.png) ![Symbol „Mehrere Stromquellen“ ](./media/data-box-heavy-safety/multiple-power-sources-icon.png) **WARNUNG!**

* Trennen Sie alle Netzkabel, um das Gerät vollständig vom Netz zu trennen.

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) **ACHTUNG!**

* Dieses Gerät enthält Knopfzellenbatterien. Versuchen Sie nicht, das Gerät zu warten. Batterien in diesem Gerät können nicht vom Benutzer gewartet werden. 
* **Nur für Wartungspersonal**: Explosionsgefahr, wenn die Batterie durch einen falschen Typ ersetzt wird. Entsorgen Sie die Altbatterien gemäß den Anweisungen.
* Laserperipheriegeräte oder Geräte sind vorhanden. Um Risiken oder Strahlenbelastung und/oder Verletzungen zu vermeiden, öffnen Sie nicht das Gehäuse von Laserperipheriegeräten oder Geräten. Laserperipheriegeräte oder Geräte sind nicht wartbar. Verwenden Sie für optische Transceiver nur die zertifizierte und zugelassene Laserklasse I.

![Hinweissymbol](./media/data-box-heavy-safety/notice-icon.png) **HINWEIS:**

Für den ordnungsgemäßen Betrieb Ihres Geräts und zur Vermeidung von Produktschäden:

* Stellen Sie sicher, dass die Vorder- und Hintertür bei laufendem Gerät vollständig geöffnet sind.

## <a name="regulatory-information"></a>Rechtliche Informationen

Dieser Abschnitt enthält rechtliche Informationen für Azure Data Box Heavy, Zulassungsmodellnummer DB020.

Für dieses Gerät gilt Folgendes:

- Eingestuft als IT-Gerät und entwickelt für den Betrieb in einer typischen Rechenzentrumsumgebung. Die Eignung dieses Produkts für andere Umgebungen kann weitere Prüfungen erfordern.
- Entwickelt für den Einsatz mit NRTL-gelisteten (UL, CSA, ETL usw.) und mit IEC/EN 60950-1 oder IEC/EN 62368-1 konformen IT-Geräten (mit CE-Zeichen).
- Entwickelt für den Betrieb in der folgenden Umgebung. 
    - Betriebstemperatur: 5° bis 35° C (41° bis 95° F)
    - Lagertemperatur: -40° C bis 65° C (-40° bis 149° F)
    - Relative Luftfeuchtigkeit: 20 % bis 85 % (nicht kondensierend) 
    - Betriebshöhe: Getestet bis 2.000 Meter (6.560 Fuß)

Die Leistungsdaten der Stromversorgung entnehmen Sie dem mit dem Gerät gelieferten Typenschild. 

![Hinweissymbol](./media/data-box-heavy-safety/notice-icon.png) **HINWEIS:** 

Änderungen oder Modifikationen am Gerät, die nicht ausdrücklich von Microsoft genehmigt wurden, können zum Erlischen der Betriebserlaubnis des Benutzers führen.

**Kanada und USA:**

![Hinweissymbol](./media/data-box-heavy-safety/notice-icon.png) **HINWEIS:** 

Dieses Gerät wurde getestet und entspricht den Grenzwerten für ein Digitalgerät der Klasse A gemäß Teil 15 der FCC-Vorschriften. Diese Grenzwerte dienen dazu, einen angemessenen Schutz vor schädlichen Störungen zu bieten, wenn das Gerät in einer gewerblichen Umgebung betrieben wird. Dieses Gerät erzeugt und nutzt Hochfrequenzenergie und kann diese ausstrahlen. Wenn es nicht gemäß Bedienungsanleitung installiert und eingesetzt wird, kann es zu schädlichen Störungen der Hochfrequenzkommunikation kommen. Der Betrieb dieses Geräts in einem Wohngebiet kann zu schädlichen Störungen führen. In einem solchen Fall ist der Benutzer verpflichtet, die Störungen auf eigene Kosten zu beseitigen.

Dieses Gerät entspricht Teil 15 der Lizenzbestimmungen von FCC Rules and Industry Canada – ausgenommen RSS-Standard(s). Der Betrieb unterliegt den beiden folgenden Bedingungen: 

- Dieses Gerät darf keine schädlichen Störungen verursachen.
- Dieses Gerät muss alle empfangenen Störungen akzeptieren, einschließlich Störungen, die einen unerwünschten Betrieb des Geräts verursachen können.

![Screenshot einer für Kanada erforderlichen Benachrichtigung](./media/data-box-heavy-safety/canada.png)

CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.
USA: (800) 426-9400, Kanada: (800) 933-4750

**Europäische Union:**

Eine Kopie der EU-Konformitätserklärung [anfordern](mailto:CSI_Compliance@microsoft.com).

![Warnsymbol](./media/data-box-heavy-safety/warning-icon.png) **WARNUNG!** 

Dies ist ein Produkt der Klasse A. In einem Privathaushalt kann dieses Produkt Funkstörungen verursachen. In einem solchen Fall kann der Benutzer aufgefordert werden, geeignete Maßnahmen zu ergreifen.

**Entsorgung von Altbatterien sowie Elektro- und Elektronikgeräten:**

![Symbol „Entsorgung von Altbatterien“](./media/data-box-heavy-safety/battery-disposal-icon.png)

Dieses Symbol auf dem Produkt oder seinen Batterien oder seiner Verpackung bedeutet, dass dieses Produkt und alle darin enthaltenen Batterien nicht über den Hausmüll entsorgt werden dürfen. Stattdessen liegt es in Ihrer Verantwortung, diese an einer geeigneten Sammelstelle für das Recycling von Batterien und Elektro- und Elektronikgeräten abzugeben. Diese getrennte Sammlung und Verwertung trägt dazu bei, natürliche Ressourcen zu schonen und mögliche negative Folgen für die menschliche Gesundheit und die Umwelt zu vermeiden, die sich aus dem möglichen Vorhandensein gefährlicher Stoffe in Batterien und Elektro- und Elektronikgeräten ergeben können, die durch unsachgemäße Entsorgung verursacht werden könnten. Weitere Informationen dazu, wo Sie Ihre Batterien und Ihren Elektro- und Elektronikschrott entsorgen können, erhalten Sie bei Ihrer Stadtverwaltung, Ihrem Hausmüllentsorger oder dem Geschäft, in dem Sie dieses Produkt gekauft haben. Kontaktieren Sie *erecycle\@microsoft.com*, um weitere Informationen über die Entsorgung von Elektro- und Elektronikgeräten zu erhalten.

Dieses Produkt enthält Knopfzellenbatterien.

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL, Telefonnummer: +353 1 295 3826, Faxnummer: +353 1 706 4110 

<!--**Japan**

![Japan](./media/data-box-heavy-safety/japan.png)-->

<!--**South Korea**

![South Korea](./media/data-box-heavy-safety/south-korea.png)

-->

Nach Durchsicht dieser Sicherheitshinweise können Sie Ihr Gerät einrichten und verkabeln.

## <a name="next-steps"></a>Nächste Schritte

* [Verkabeln und Anschließen von Data Box Heavy](data-box-heavy-deploy-set-up.md)


