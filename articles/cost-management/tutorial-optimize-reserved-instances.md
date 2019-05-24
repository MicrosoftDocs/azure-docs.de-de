---
title: Tutorial – Optimieren von Kosten für reservierte Instanzen mit Cloudyn in Azure | Microsoft-Dokumentation
description: In diesem Tutorial wird beschrieben, wie Sie die Kosten für Ihre reservierten Instanzen für Azure und Amazon Web Services (AWS) optimieren.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: 6af6a6b334d29c6ff813a9b2ff35153aed239b1b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967154"
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>Tutorial: Optimieren von reservierten Instanzen

In diesem Tutorial wird beschrieben, wie Cloudyn Sie beim Optimieren Ihrer Kosten für reservierte Instanzen und der Nutzung für Azure und Amazon Web Services (AWS) unterstützen kann. Bei beiden Cloud-Dienstanbietern gehen Sie mit einer reservierten Instanz einen langfristigen Vertrag ein, bei dem Sie sich im Voraus zur Nutzung der VM verpflichten. Hieraus können sich unter Umständen erhebliche Einsparungen gegenüber der Verwendung des Standardpreismodells mit nutzungsabhängiger Bezahlung ergeben. Potenzielle Einsparungen werden nur realisiert, wenn Sie die Kapazität Ihrer reservierten Instanzen vollständig nutzen.

In diesem Tutorial wird beschrieben, wie Azure und reservierte AWS-Instanzen (RIs) von Cloudyn unterstützt werden. Außerdem wird beschrieben, wie Sie die Kosten der reservierten Instanzen optimieren können. Vor allem sollten Sie sicherstellen, dass Ihre Reservierungen vollständig genutzt werden. In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Verstehen der Grundlagen von Azure-RI-Kosten
> * Verstehen der Vorteile von RIs
> * Optimieren von Azure-RI-Kosten
> * Anzeigen von RI-Kosten
> * Bewerten der Kosteneffizienz von Azure-RIs
> * Optimieren von AWS-RI-Kosten
> * Erwerben von empfohlenen RIs
> * Ändern von nicht genutzten Reservierungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Konto.
- Sie müssen entweder über eine Registrierung für die Testversion oder über ein kostenpflichtiges Abonnement für Cloudyn verfügen.
- Sie müssen RIs in Azure oder AWS erworben haben.

## <a name="understand-azure-ri-costs"></a>Verstehen der Grundlagen von Azure-RI-Kosten

Wenn Sie Azure Reserved VM Instances erwerben, zahlen Sie im Voraus für die zukünftige Nutzung. Mit der Vorauszahlung werden die Kosten für die zukünftige Nutzung der VMs wie folgt abgedeckt:

- Für einen bestimmten Typ
- In einer bestimmten Region
- Für einen Zeitraum von einem oder drei Jahren
- Bis zu einer bestimmten Menge erworbener VMs

Sie können Ihre erworbenen Azure Reserved VM Instances im Azure-Portal unter [Reservierungen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) anzeigen.

Der Begriff _Azure Reserved VM Instance_ bezieht sich nur auf ein Preismodell. Ihre ausgeführten VMs ändern sich hierdurch in keiner Weise. Der Begriff ist Azure-spezifisch, und die allgemeinere Bezeichnung ist _reservierte Instanz_ oder _Reservierung_. Reservierte Instanzen, die Sie erworben haben, gelten nicht für bestimmte VMs, sondern für alle passenden VMs. Ein Beispiel ist eine Reservierung für einen VM-Typ, der in einer Region ausgeführt wird, die Sie für Ihre erworbene Reservierung ausgewählt haben.

Erworbene reservierte Instanzen gelten nur für die grundlegende Hardware. Softwarelizenzen einer VM werden hierdurch nicht abgedeckt. Es kann beispielsweise sein, dass Sie eine Instanz reservieren und über eine passende VM mit Windows verfügen. Durch die reservierte Instanz werden nur die Grundkosten der VM abgedeckt. In diesem Beispiel zahlen Sie für alle erforderlichen Windows-Lizenzen den vollen Preis. Erwägen Sie die Nutzung der [Azure-Hybridvorteile](https://azure.microsoft.com/pricing/hybrid-benefit), um einen Rabatt auf das Betriebssystem oder andere Software zu erhalten, die auf Ihren VMs ausgeführt wird. Hybridvorteile sind mit einem Rabatttyp für Ihre Softwarelizenzen verbunden, der dem Rabatt für reservierte Instanzen für die Basis-VMs ähnelt.

Die Auslastung der reservierten Instanzen hat keine direkten Auswirkungen auf die Kosten. Anders ausgedrückt: Es ist dasselbe, ob Sie eine VM mit einer CPU-Auslastung von 100% oder 0% betreiben: Sie zahlen im Voraus für die Zuteilung der VM, nicht für die tatsächliche Nutzung.

In der folgenden Abbildung ist dargestellt, wie sich die Kosten für die bedarfsgesteuerte VM-Standardnutzung gegenüber den Kosten für reservierte Instanzen verhalten:

![Kosten für bedarfsabhängige Nutzung und reservierte Instanzen](./media/tutorial-optimize-reserved-instances/azure01.png)



Die roten Balken zeigen die kumulierten Kosten für den Erwerb einer reservierten Instanz. Sie zahlen nur die einmalige Gebühr. Die VM-Nutzung ist kostenlos. Die blauen Balken zeigen die kumulierten Kosten derselben VM, die basierend auf der nutzungsbasierten Bezahlung oder dem Preismodell für bedarfsabhängige Nutzung ausgeführt wird. Im siebten oder achten Monat der VM-Nutzung befindet sich der *Break-even-Point*. In diesem Beispiel sparen Sie also ab dem achten Monat Kosten.

## <a name="benefits-of-ris"></a>Vorteile von RIs

Jeder Erwerb einer reservierten Instanz gilt für eine VM mit einer bestimmten Größe und einem bestimmten Standort. Ein Beispiel hierfür ist „D2s\_v3“ am Standort „USA, Westen“ in der folgenden Abbildung:

![Details zu reservierten Azure-Instanzen](./media/tutorial-optimize-reserved-instances/azure02.png)

Der Erwerb einer reservierten Instanz bringt Vorteile, wenn eine VM eine ausreichende Anzahl von Stunden ausgeführt wird, damit der Break-even-Point der Reservierung erreicht wird. Die VM muss zur Größe und einem Standort Ihrer reservierten Instanz passen. Im obigen Diagramm liegt der Break-even-Point beispielsweise ungefähr bei siebeneinhalb Monaten. Der Erwerb lohnt sich also, wenn die VM für die Reservierung mindestens 7,5 Monate (7,5 \* 30 Tage \* 24 Stunden = 5.400 Stunden) lang ausgeführt wird. Falls die VM weniger als 5.400 Stunden ausgeführt wird, ist die Reservierung teurer als die nutzungsbasierte Bezahlung.

Der Break-even-Point kann für jede VM-Größe und jeden Standort variieren. Außerdem spielt der vereinbarte Betrag für die nutzungsbasierte Bezahlung der VM eine Rolle. Prüfen Sie vor dem Erwerb den Break-even-Point, der für Ihren Fall gilt.

Ein weiterer zu berücksichtigender Faktor beim Erwerb der Reservierung ist der Umfang der reservierten Instanz. Der Umfang bestimmt, ob der Vorteil der Reservierung allgemeingültig ist oder nur für ein bestimmtes Abonnement gilt. Freigegebene reservierte Instanzen werden für Ihre gesamten Abonnements nach dem Zufallsprinzip auf die passenden VMs angewendet, die zuerst gefunden werden.

Der Umfang „Freigegeben“ für den Erwerb ist am flexibelsten und wird allgemein empfohlen. Die Wahrscheinlichkeit, dass Sie Ihre gesamten reservierten Instanzen nutzen, ist beim Umfang „Freigegeben“ erheblich höher. Wenn der Besitzer eines Abonnements aber für die reservierte Instanz bezahlt, kann es unter Umständen auch erforderlich sein, den Umfang „Einzelnes Abonnement“ zu erwerben.

## <a name="optimize-azure-ri-costs"></a>Optimieren von Azure-RI-Kosten

Cloudyn unterstützt reservierte Instanzen und Hybridvorteile wie folgt:

- Anzeige der Kosten, die Preismodellen zugeordnet sind
- Nachverfolgung der RI-Nutzung
- Bewertung der Auswirkungen von RIs
- Zuteilung der RI-Kosten gemäß Ihren Richtlinien

Die erste Aktion, die Sie vor dem Erwerb einer reservierten Instanz durchführen sollten, ist die Bewertung der mit dem RI-Erwerb verbundenen Auswirkungen:

- Wie hoch sind die Kosten?
- Wie viel sparen Sie?
- Wo liegt der Break-even-Point?

Der Bericht zu den Auswirkungen des Erwerbs von reservierten Instanzen („Reserved Instance Purchase Impact“) dient Ihnen als Hilfe bei der Beantwortung dieser Fragen.

## <a name="assess-azure-ri-cost-effectiveness"></a>Bewerten der Kosteneffizienz von Azure-RIs

Navigieren Sie im Cloudyn-Portal zu **Optimizer** > **RI Comparison** (Optimierer > RI-Vergleich), und wählen Sie die Option **Reserved Instance Purchase Impact** (Auswirkungen des Erwerbs von reservierten Instanzen).

Wählen Sie im Bericht „Reserved Instance Purchase Impact“ eine VM-Größe (Instanztyp), einen Standort (Region), einen Reservierungszeitraum, eine Menge und die erwartete Laufzeit aus. Anschließend können Sie bewerten, ob Sie mit Ihrem Erwerb Kosten sparen.

Wenn Sie beispielsweise eine Reservierung für eine VM vom Typ „DS1\_v2“ in „USA, Osten“ erwerben und diese ein ganzes Jahr lang rund um die Uhr ausgeführt wird, können Sie pro Jahr 369,48 US-Dollar sparen. Der Break-even-Point liegt bei fünf Monaten. Sehen Sie sich die folgende Abbildung an:

![Break-even-Point für reservierte Azure-Instanz](./media/tutorial-optimize-reserved-instances/azure03.png)

Wenn die VM nur 50% der Zeit ausgeführt wird, liegt der Break-even-Point bei zehn Monaten, und Sie sparen pro Jahr nur 49,74 US-Dollar. In diesem Beispiel ergibt sich für Sie durch den Erwerb der Reservierung für diese Instanz unter Umständen kein Vorteil. Sehen Sie sich die folgende Abbildung an:

![Beispiel für den Break-even-Point für virtuelle Azure-Computer](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>Anzeigen von RI-Kosten

Wenn Sie eine Reservierung erwerben, leisten Sie eine einmalige Zahlung. Es gibt zwei Möglichkeiten, die Zahlung in Cloudyn anzuzeigen:

- Ist-Kosten
- Amortisierte Kosten

### <a name="actual-reserved-instance-cost"></a>Ist-Kosten für die reservierte Instanz

In den Berichten „Actual Cost Analysis“ (Analyse der Ist-Kosten) und „Analysis Over Time“ (Zeitbasierte Analyse) werden die vollen Beträge angezeigt, die sie für die Reservierung bezahlt haben (ab dem Monat des Erwerbs). Hiermit können Sie Ihre tatsächlichen Ausgaben für einen bestimmten Zeitraum anzeigen.

Navigieren Sie im Cloudyn-Portal zu **Costs** > **Cost Analysis** (Kosten > Kostenanalyse), und klicken Sie dann entweder auf **Actual Cost Analysis** (Analyse der Ist-Kosten) oder auf **Actual Cost Over Time** (Zeitbasierte Analyse). Legen Sie als Nächstes die Filter fest. Filtern Sie beispielsweise nur den Azure/VM-Dienst, und gruppieren Sie nach dem Ressourcentyp und dem Preismodell. Sehen Sie sich die folgende Abbildung an:

![Beispiel für die Ist-Kosten reservierter Instanzen](./media/tutorial-optimize-reserved-instances/azure05.png)

Sie können in diesem Beispiel nach dem Dienst **Azure/VM** filtern und nach **Price Model** (Preismodell) und **Resource Type** (Ressourcentyp) gruppieren. Dies ist in der folgenden Abbildung dargestellt:

![Beispiel für Berichtsgruppen und Filter für Ist-Kosten, gruppiert nach Preismodell und Ressourcentyp](./media/tutorial-optimize-reserved-instances/azure06.png)

Sie können auch die Arten der geleisteten Zahlungen analysieren, z.B. einmalige Gebühren, Nutzungsgebühren und Lizenzgebühren.

### <a name="amortized-reserved-instance-cost"></a>Amortisierte Kosten der reservierten Instanz

Sie leisten eine Vorauszahlung, die beim Erwerben einer RI im Monat des Erwerbs angezeigt wird. Sie ist in den nachfolgenden Rechnungen nicht enthalten. Es kann also etwas irreführend sein, wenn Sie sich die monatliche Nutzung ansehen. Ihre wirklichen monatlichen Kosten umfassen die monatliche Nutzung zuzüglich des proportionalen (amortisierten) Teils der zuvor geleisteten einmaligen Zahlungen. Mit dem Bericht zu den amortisierten Kosten können Sie den tatsächlichen Stand ermitteln.

Die amortisierten Kosten für reservierte Instanzen werden berechnet, indem die einmalige Reservierungsgebühr für den Reservierungszeitraum amortisiert wird. In Berichten zu den Ist-Kosten sind die einmaligen Gebühren im Monat des Reservierungserwerbs angegeben. Die täglichen und monatlichen Ausgaben tauchen in den Ist-Kosten der Bereitstellung nicht auf. In Berichten zu den amortisierten Kosten werden die Ist-Kosten der Bereitstellung in Abhängigkeit der Zeit angegeben.  Nur im Bericht zu den amortisierten Kosten können Sie Ihren wirklichen Kostentrend verfolgen. Dies ist auch die einzige Möglichkeit, Ihre zukünftigen Ausgaben zu prognostizieren.

Im Bericht zu den Ist-Kosten wurde für einen RI-Erwerb am 16. November eine Spitze von 747 US-Dollar angegeben. Im Bericht zu den amortisierten Kosten (siehe folgende Abbildung) sind am 16. November die Teilkosten für den Tag angegeben. Ab dem 17. November werden die amortisierten RI-Kosten in Höhe von 747/365 = 2,05 US-Dollar aufgeführt. Hierbei können Sie übrigens auch erkennen, dass die erworbene Reservierung nicht genutzt wird, und Sie können eine Optimierung erzielen, indem Sie zu einer anderen VM-Größe wechseln.

Navigieren Sie zum Anzeigen zu **Costs** > **Cost Analysis** (Kosten > Kostenanalyse), und klicken Sie dann auf **Amortized Cost Analysis** (Analyse der amortisierten Kosten) oder auf **Amortized Cost Over Time** (Amortisierte Kosten im Zeitverlauf).

![Beispielbericht mit amortisierten Kosten der reservierten Instanz](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>Optimieren von AWS-RI-Kosten

Reservierte Instanzen stellen eine offene Verpflichtung dar. Sie sind hilfreich, wenn Sie VMs dauerhaft nutzen möchten, da reservierte Instanzen kostengünstiger als bedarfsgesteuerte Instanzen sind. Allerdings müssen sie ausreichend genutzt werden. Die Verpflichtung besteht darin, Ressourcen (normalerweise VMs) über einen definierten Zeitraum zu nutzen. Der Zeitraum beträgt ein oder drei Jahre. Wenn Sie die Verpflichtung durch den Erwerb eingehen, leisten Sie im Voraus die Zahlung für die Ressourcen, um sie zu reservieren. Es kann aber sein, dass Sie nicht immer alle reservierten Ressourcen vollständig nutzen.

Ggf. bewerten Sie beispielsweise Ihre Umgebung und ermitteln, dass Sie im letzten Jahr konstant 20 D2-Standardinstanzen ausgeführt haben. Hierfür können Sie eine Reservierung erwerben und unter Umständen erhebliche Kosten sparen. Ein anderes Beispiel ist die Verpflichtung zur Nutzung von zehn MA4-Instanzen pro Jahr. Sie haben aber ggf. bisher nur fünf davon eingesetzt. Dies sind beides Beispiele für eine ineffiziente RI-Nutzung. Es gibt zwei Möglichkeiten zur Optimierung der Kosten für reservierte Instanzen mit Cloudyn-Optimierungsberichten:

- Prüfen der Kaufempfehlungen basierend auf Ihrem bisherigen Nutzungsverlauf
- Ändern von nicht genutzten Reservierungen

Sie verwenden die Berichte _EC2 RI Buying Recommendations_ (EC2-RI-Kaufempfehlungen) und _EC2 Currently Unused Reservations_ (Derzeit ungenutzte EC2-Reservierungen), um die Nutzung und die Kosten für Ihre reservierten Instanzen zu verbessern.

## <a name="buy-recommended-ris"></a>Erwerben von empfohlenen RIs

Cloudyn vergleicht die bedarfsgesteuerte Instanznutzung mit potenziellen reservierten Instanzen. Wenn Einsparpotenziale erkannt werden, werden im Bericht „EC2 Buying Recommendations“ (EC2-RI-Kaufempfehlungen) entsprechende Empfehlungen angezeigt.

Klicken Sie im Berichtsmenü am oberen Rand des Portals auf **Optimizer** (Optimierung) > **Pricing Optimization** (Preisoptimierung) > **EC2 RI Buying Recommendations** (EC2-RI-Kaufempfehlungen).

Die folgende Abbildung enthält Kaufempfehlungen aus dem Bericht.

![Beispiel für Kaufempfehlungen im EC2-Kaufempfehlungsbericht](./media/tutorial-optimize-reserved-instances/aws01.png)

In diesem Beispiel verfügt das Cloudyn\_A-Konto über Kaufempfehlungen für 32 reservierte Instanzen. Wenn Sie allen Kaufempfehlungen nachkommen, können Sie pro Jahr bis zu 137.770 US-Dollar sparen. Beachten Sie, dass in den Kaufempfehlungen von Cloudyn vorausgesetzt wird, dass die Nutzung für ausgeführte Workloads konsistent bleibt.

Klicken Sie unter **Justifications** (Begründungen) jeweils auf das Pluszeichen (**+**), um die Details zu den einzelnen Kaufempfehlungen anzuzeigen. Hier ist ein Beispiel für die erste Empfehlung in der Liste angegeben.

![Beispiel für Kaufbegründungsdetails](./media/tutorial-optimize-reserved-instances/aws02.png)

Im vorherigen Beispiel wird angezeigt, dass die bedarfsgesteuerte Ausführung der Workload pro Jahr 90.456 US-Dollar kosten würde. Wenn Sie die Reservierung aber im Voraus erwerben, kostet dieselbe Workload 56.592 US-Dollar. Sie sparen also 33.864 US-Dollar pro Jahr.

Klicken Sie auf das Pluszeichen neben **EC2 RI Purchase Impact** (Auswirkungen des EC2-RI-Erwerbs), um Ihren Break-even-Point für den Zeitraum eines Jahres anzuzeigen und zu ermitteln, wann Ihre Investition zu Kostenvorteilen führt. Im folgenden Beispiel übersteigen die kumulierten Kosten für den bedarfsgesteuerten Betrieb nach ungefähr acht Monaten nach dem Erwerb die kumulierten RI-Kosten:

![Beispiel für Kaufauswirkungsdetails](./media/tutorial-optimize-reserved-instances/aws03.png)

Ab diesem Zeitpunkt sparen Sie Kosten.

Sie können Instanzen in Abhängigkeit der Zeit (**Instances over Time**) anzeigen, um die Genauigkeit der vorgeschlagenen Kaufempfehlung zu überprüfen. In diesem Beispiel sehen Sie, dass für die Workload in den letzten 30 Tagen durchschnittlich sechs Instanzen verwendet wurden.

![Beispiel für den bisherigen Nutzungsverlauf von Instanzen](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>Ändern von nicht genutzten Reservierungen

In vielen Computingumgebungen der Nutzer von Cloudressourcen sind häufig nicht verwendete Reservierungen vorhanden. Indem Sie sicherstellen, dass nicht verwendete Reservierungen vollständig genutzt werden, können Sie Kosten sparen, wenn Sie Reservierungen zur Anpassung an Ihre aktuellen Anforderungen ändern. Es kann beispielsweise sein, dass Sie über ein Abonnement mit D3-Standardinstanzen unter Linux verfügen. Wenn Sie die Reservierung nicht vollständig verwenden, können Sie den Instanztyp ändern. Oder Sie können die nicht genutzten Ressourcen in eine andere Reservierung oder in ein anderes Konto verschieben.

AWS bietet reservierte Instanzen für bestimmte Verfügbarkeitszonen und Regionen an. Wenn Sie reservierte Instanzen für eine bestimmte Verfügbarkeitszone erworben haben, können Sie die Reservierungen nicht zwischen Zonen verschieben. Es ist aber leicht möglich, regional reservierte Instanzen zwischen Zonen zu verschieben, indem Sie den Bericht **EC2 Currently Unused Reservations** (Derzeit ungenutzte EC2-Reservierungen) verwenden. Alternativ dazu können Sie sie so ändern, dass sie für einen regionalen Bereich gelten und passende Instanzen auf alle Verfügbarkeitszonen verteilt werden.

Klicken Sie oben im Portal im Berichtsmenü auf **Optimizer** > **Inefficiencies** > **EC2 Currently Unused Reservations** (Optimierer > Ineffizienzen > Derzeit ungenutzte EC2-Reservierungen).

In den folgenden Abbildungen ist der Bericht mit den nicht genutzten reservierten Instanzen dargestellt.

![Beispiel für Zusammenfassungsinformationen zu ungenutzten Reservierungen](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

Klicken Sie auf das Pluszeichen unter **Details**, um Reservierungsdetails für eine bestimmte Reservierung anzuzeigen.

![Bespiel für Details zu ungenutzten Reservierungen](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

Im vorherigen Beispiel sind in den verschiedenen Verfügbarkeitszonen insgesamt 77 nicht genutzte Reservierungen enthalten. Die erste Reservierung verfügt über 51 nicht genutzte Instanzen. Weiter unten in der Liste sind potenzielle Änderungen von Reservierungsinstanzen angegeben, die Sie vornehmen können, indem Sie den Instanztyp **m3.2xlarge** in der Verfügbarkeitszone **us-east-1c** verwenden.

Klicken Sie für die erste Reservierung in der Liste auf **Modify** (Ändern), um die Seite **Modify RI** (RI ändern) zu öffnen, auf der Daten zur Reservierung angezeigt werden.

![Beispiel für änderbare Reservierungen](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

Reservierte Instanzen, die Sie ändern können, sind aufgeführt. In der folgenden Abbildung mit einem Beispiel sind 51 nicht genutzte Reservierungen zu sehen, die sie ändern können, aber für die beiden Reservierungen werden 54 benötigt. Wenn Sie Ihre nicht genutzten Reservierungen ändern und alle verwenden, werden vier Instanzen weiterhin bedarfsgesteuert ausgeführt. Teilen Sie Ihre nicht genutzten Reservierungen für dieses Beispiel auf, sodass für die erste Reservierung 30 und für die zweite Reservierung 21 verwendet werden.

Klicken Sie auf das Pluszeichen des ersten Reservierungseintrags, und legen Sie die **Reservierungsmenge** auf **30** fest. Legen Sie die Reservierungsmenge für den zweiten Eintrag auf **21** fest, und klicken Sie anschließend auf **Übernehmen**.

![Beispiel für Reservierungsmengenänderungen](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

Alle nicht genutzten Instanzen für die Reservierung werden vollständig verwendet, und 51 Instanzen werden nicht mehr bedarfsgesteuert ausgeführt. In diesem Beispiel spart Ihre Organisation Kosten, indem die bedarfsgesteuerte Nutzung erheblich reduziert wird und Reservierungen verwendet werden, die bereits bezahlt wurden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie folgende Aufgaben durchgeführt:

> [!div class="checklist"]
> * Verstehen der Grundlagen von Azure-RI-Kosten
> * Verstehen der Vorteile von RIs
> * Optimieren von Azure-RI-Kosten
> * Anzeigen von RI-Kosten
> * Bewerten der Kosteneffizienz von Azure-RIs
> * Optimieren von AWS-RI-Kosten
> * Erwerben von empfohlenen RIs
> * Ändern von nicht genutzten Reservierungen


Im nächsten Tutorial erfahren Sie, wie Sie den Zugriff auf Daten steuern.

> [!div class="nextstepaction"]
> [Steuern des Zugriffs auf Daten](tutorial-user-access.md)
