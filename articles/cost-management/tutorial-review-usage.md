---
title: 'Tutorial: Überprüfen der Nutzung und der Kosten mit Cloudyn in Azure | Microsoft-Dokumentation'
description: In diesem Tutorial überprüfen Sie die Nutzung und die Kosten, um Trends nachzuverfolgen, Ineffizienz zu erkennen und Warnungen zu erstellen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: 2b701e618bdcbd50709360c55cfb9ceb68781ebc
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969087"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Tutorial: Überprüfen der Nutzung und der Kosten

Cloudyn zeigt die Nutzung und Kosten an, damit Sie Trends nachverfolgen, Ineffizienzen erkennen und Warnungen erstellen können. Alle Nutzungs- und Kostendaten werden in Cloudyn-Dashboards und -Berichten angezeigt. In den Beispielen dieses Tutorials erhalten Sie schrittweise Anleitungen zum Überprüfen von Nutzung und Kosten anhand von Dashboards und Berichten.

Azure Cost Management bietet ähnliche Funktionen wie Cloudyn. Azure Cost Management ist eine native Azure-Kostenverwaltungslösung. Die Lösung unterstützt Sie beim Analysieren von Kosten, beim Erstellen und Verwalten von Budgets, beim Exportieren von Daten sowie beim Prüfen von Optimierungsempfehlungen und beim Reagieren auf diese – und damit beim Sparen von Geld. Weitere Informationen finden Sie unter [Azure Cost Management](overview-cost-mgt.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Nachverfolgen der Nutzung und der Kosten
> * Erkennen von Ineffizienz bei der Nutzung
> * Erstellen von Warnungen für ungewöhnliche oder zu hohe Ausgaben
> * Exportieren von Daten

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Konto.
- Sie müssen entweder über eine Registrierung für die Testversion oder über ein kostenpflichtiges Abonnement für Cloudyn verfügen.

## <a name="open-the-cloudyn-portal"></a>Öffnen des Cloudyn-Portals

Die Nutzung und die Kosten werden generell im Cloudyn-Portal überprüft. Öffnen Sie im Azure-Portal das Cloudyn-Portal, oder navigieren Sie zu https://azure.cloudyn.com, und melden Sie sich an.

## <a name="track-usage-and-cost-trends"></a>Nachverfolgen der Nutzung und der Kosten

Sie verfolgen die tatsächlich für die Nutzung getätigten Ausgaben und die Kosten mit Berichten über den Lauf der Zeit, um Trends zu erkennen. Betrachten Sie Trends ausgehend vom Bericht über die tatsächlichen Kosten im Zeitverlauf. Klicken Sie im Menü oben links im Portal auf **Costs** > **Cost Analysis** > **Actual Cost Over Time** (Kosten > Kostenanalyse > Tatsächliche Kosten im Zeitverlauf). Beim erstmaligen Öffnen des Berichts sind keine Gruppen oder Filter darauf angewendet.

Dies ist ein Beispiel für einen Bericht:

![Beispiel für den Bericht „Actual Cost Over Time“ (Ist-Kosten im Zeitverlauf)](./media/tutorial-review-usage/actual-cost01.png)

Im Bericht werden alle Ausgaben in den letzten 30 Tagen aufgeführt. Wenn nur die Ausgaben für Azure-Dienste angezeigt werden sollen, wenden Sie die Gruppe „Service“ (Dienst) an, und filtern Sie nach allen Azure-Diensten. Die folgende Abbildung stellt gefilterte Dienste dar.

![Beispiel für die gefilterten Azure-Dienste](./media/tutorial-review-usage/actual-cost02.png)

Im vorherigen Beispiel fielen ab dem 29.10.2018 geringere Ausgaben an. Eine zu große Anzahl von Spalten kann aber einen offensichtlichen Trend verdecken. Sie können die Berichtsanzeige in ein Linien- oder Flächendiagramm ändern, um die Daten in anderen Darstellungen zu untersuchen. In der folgenden Abbildung ist der Trend deutlicher erkennbar.

![Beispiel eines rückläufigen Kostentrends für virtuelle Azure-Computer](./media/tutorial-review-usage/actual-cost03.png)

Wenn Sie mit dem Beispiel fortfahren, sehen Sie, dass die Kosten für die Azure-VM gefallen sind. Die Kosten für die anderen Azure-Dienste sind ab diesem Tag ebenfalls gefallen. Wodurch wurde diese Kostensenkung verursacht? In diesem Beispiel wurde ein großes Projekt abgeschlossen, sodass auch der Verbrauch für viele Azure-Dienste gefallen ist.

Ein Videotutorial zur Nachverfolgung von Nutzungs- und Kostentrends finden Sie unter [Analyzing your cloud billing data vs. time with Cloudyn](https://youtu.be/7LsVPHglM0g) (Analysieren Ihrer Cloudabrechnungsdaten im Vergleich zur Abrechnung anhand von Zeitintervallen mit Cloudyn).

## <a name="detect-usage-inefficiencies"></a>Erkennen von Ineffizienz bei der Nutzung

Optimizer-Berichte ermöglichen es, die Effizienz zu steigern, die Nutzung zu optimieren und Möglichkeiten zu ermitteln, Kosten für Ihre Cloudressourcen einzusparen. Sie sind insbesondere hilfreich durch Empfehlungen zur Kosteneffizienz, anhand derer VMs im Leerlauf oder kostenintensive VMs reduziert werden können.

Ein häufiges Problem, das in Organisationen auftritt, wenn sie erstmals Ressourcen in die Cloud verschieben, besteht in ihrer Virtualisierungsstrategie. Häufig wird ein Ansatz verfolgt, der auch beim Erstellen von virtuellen Computern für die lokale Virtualisierungsumgebung angewendet wird. Zudem wird davon ausgegangen, dass Kosten allein durch das Verschieben der lokalen VMs in die Cloud gesenkt werden, ohne dass diese dazu geändert werden. Ein solcher Ansatz trägt jedoch wahrscheinlich nicht zur Kostensenkung bei.

Das Problem liegt darin, dass die vorhandene Infrastruktur bereits bezahlt wurde. Benutzer können große VMs erstellen und betreiben. Ob sich diese im Leerlauf befinden oder nicht, hat nur geringfügige Konsequenzen. Das Verschieben großer oder im Leerlauf befindlicher VMs in die Cloud zieht mit großer Wahrscheinlichkeit einen *Anstieg* der Kosten nach sich. Die Kostenzuordnung für Ressourcen ist unverzichtbar, wenn Sie Verträge mit Anbietern von Clouddiensten abschließen. Sie haben für Ihre vertraglichen Pflichten zu zahlen, egal, ob Sie die Ressource vollständig nutzen oder nicht.

Im Bericht mit Empfehlungen zu kostengünstigen Größenanpassungen werden potenzielle jährliche Einsparungen ausgewiesen. Dabei wird die Kapazität des jeweiligen VM-Instanztyps mit den historischen Daten zu CPU- und Speichernutzung verglichen.  

Klicken Sie im Menü oben im Portal auf **Optimizer** (Optimierung) > **Sizing Optimization** (Größenoptimierung) > **Cost Effective Sizing Recommendations** (Empfehlungen zu kostengünstigen Größenanpassungen). Wenden Sie einen Filter an, um die Ergebnisse zu reduzieren, falls dies hilfreich ist. In der folgenden Abbildung finden Sie ein Beispiel.

![Bericht mit Empfehlungen zu kosteneffektiven Größen für virtuelle Azure-Computer](./media/tutorial-review-usage/sizing01.png)

In diesem Beispiel können 2.382 USD eingespart werden, wenn die Empfehlungen hinsichtlich der Änderung der VM-Instanztypen befolgt werden. Klicken Sie auf das Pluszeichen (+) unter **Details**, um die erste Empfehlung aufzurufen. Dies sind die Details der Empfehlung.

![Beispiel für Empfehlungsdetails](./media/tutorial-review-usage/sizing02.png)

Zeigen Sie die IDs der VM-Instanz an, indem Sie auf das Pluszeichen neben **List of Candidates** (Liste der Kandidaten) klicken.

![Beispiel für eine Liste mit VM-Kandidaten für die Größenänderung](./media/tutorial-review-usage/sizing03.png)

Ein Videotutorial zum Ermitteln von Ineffizienzen bei der Nutzung finden Sie unter [Optimizing VM Size in Cloudyn](https://youtu.be/1xaZBNmV704) (Optimieren der VM-Größe in Cloudyn).

In Azure Cost Management werden auch Empfehlungen zur Kosteneinsparung für Azure-Dienste bereitgestellt. Weitere Informationen finden Sie unter [Tutorial: Optimieren von Kosten mithilfe von Empfehlungen](tutorial-acm-opt-recommendations.md).

## <a name="create-alerts-for-unusual-spending"></a>Erstellen von Warnungen für ungewöhnliche Ausgaben

Durch Warnungen können Beteiligte automatisch über Anomalien bei den Ausgaben und das Risiko einer Budgetüberschreitung benachrichtigt werden. Sie können Warnungen auf der Grundlage von Berichten erstellen, die auf Budget- und Kostenschwellenwerten basierende Warnungen unterstützen.

In diesem Beispiel wird der **Bericht für Ist-Kosten im Zeitverlauf** verwendet, um eine Benachrichtigung zu senden, wenn sich die Ausgaben für einen virtuellen Azure-Computer Ihrem Gesamtbudget annähern. In diesem Szenario beträgt das Gesamtbudget 20.000 USD, und Sie möchten eine Benachrichtigung erhalten, wenn die Kosten sich der Hälfte Ihres Budgets (9.000 USD) annähern, und eine Warnung, wenn die Kosten 10.000 USD erreichen.

1. Wählen Sie oben im Menü des Cloudyn-Portals Folgendes aus: **Costs** > **Cost Analysis** > **Actual Cost Over Time** („Kosten“ > „Kostenanalyse“ > „Ist-Kosten im Zeitverlauf“).
2. Legen Sie **Groups** (Gruppen) auf **Service** (Dienst) und **Filter on the service** (Filtern nach Dienst) auf **Azure/VM** fest.
3. Wählen Sie in der rechten oberen Ecke des Berichts **Actions** (Aktionen) und anschließend **Schedule report** (Bericht planen) aus.
4. Wählen Sie im Dialogfeld **Save or Schedule this report** (Diesen Bericht speichern oder planen) die Registerkarte **Scheduling** (Planung) aus, um im gewünschten Intervall eine E-Mail mit dem Bericht zu erhalten. Achten Sie darauf, dass Sie **Send via email** (Per E-Mail senden) auswählen. Alle verwendeten Tags, Gruppierungen und Filter werden in den per E-Mail gesendeten Bericht eingeschlossen.
5. Wählen Sie die Registerkarte **Threshold** (Schwellenwert) und anschließend **Actual Cost vs. Threshold** (Tatsächliche Kosten im Vergleich zu Schwellenwert) aus.
   1. Geben Sie im Schwellenwertfeld für die **rote Warnung** den Wert „10000“ ein.
   2. Geben Sie im Schwellenwertfeld für die **gelbe Warnung** den Wert „9000“ ein.
   3. Geben Sie im Feld **Number of consecutive alerts** (Anzahl der aufeinanderfolgenden Warnungen) ein, wie viele aufeinanderfolgende Warnungen sie erhalten möchten. Wenn Sie die festgelegte Gesamtzahl von Warnungen erhalten haben, werden keine weiteren Warnungen mehr versendet.
6. Wählen Sie **Speichern** aus.

![Beispiel für rote und gelbe Warnungen auf der Grundlage von Ausgabeschwellenwerten](./media/tutorial-review-usage/schedule-alert01.png)

Sie können auch die Schwellenwertmetrik **Cost Percentage vs. Budget** (Prozentuale Kosten im Vergleich zu Budget) verwenden, um Warnungen zu erstellen. In diesem Fall können Sie die Schwellenwerte als Prozentsatz Ihres Budgets (anstelle von Geldbeträgen) angeben.

## <a name="export-data"></a>Exportieren von Daten

Sie können auf ähnliche Weise Warnungen für Berichte erstellen. Außerdem können Sie Daten aus jedem beliebigen Bericht exportieren. Sie können beispielsweise eine Liste von Cloudyn-Konten oder anderen Benutzerdaten exportieren. Zum Exportieren eines Bericht öffnen Sie den Bericht und klicken Sie oben rechts im Bericht auf **Actions** (Aktionen). Zu den Aktionen, die Sie ausführen können, gehört **Export all report data** (Alle Berichtsdaten exportieren). Mit dieser Aktion können Sie die Informationen herunterladen oder ausdrucken. Alternativ können Sie **Schedule report** (Bericht planen) auswählen, um das Senden des Berichts als E-Mail zu planen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Nachverfolgen der Nutzung und der Kosten
> * Erkennen von Ineffizienz bei der Nutzung
> * Erstellen von Warnungen für ungewöhnliche oder zu hohe Ausgaben
> * Exportieren von Daten


Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie anhand von Verlaufsdaten Ausgaben vorhersagen.

> [!div class="nextstepaction"]
> [Prognostizieren zukünftiger Ausgaben](tutorial-forecast-spending.md)
