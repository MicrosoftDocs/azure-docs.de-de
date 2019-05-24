---
title: Verwenden von Cloudyn-Berichten in Azure | Microsoft-Dokumentation
description: Dieser Artikel beschreibt den Zweck der im Cloudyn-Portal enthaltenen Cloudyn-Berichte, damit Sie sie effektiv nutzen können.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: f056515e87d01d0a30fec7f792fcb6e5e91c0c89
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969043"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Im Cloudyn-Portal verfügbare Berichte

Dieser Artikel beschreibt den Zweck der Cloudyn-Berichte, die im Cloudyn-Portal enthalten sind. Es wird außerdem beschrieben, wie Sie die Berichte effektiv nutzen können. Die meisten Berichte sind intuitiv und weisen ein einheitliches Erscheinungsbild auf. Die meisten der Aktionen, die Sie in einem Bericht ausführen können, sind auch in anderen Berichten verfügbar. Eine Übersicht über das Verwenden von Cloudyn-Berichten, einschließlich des Anpassens und Speicherns oder des Planens von Berichten, finden Sie unter [Grundlagen zu Kostenberichten](understanding-cost-reports.md).

Azure Cost Management bietet ähnliche Funktionen wie Cloudyn. Azure Cost Management ist eine native Azure-Kostenverwaltungslösung. Die Lösung unterstützt Sie beim Analysieren von Kosten, beim Erstellen und Verwalten von Budgets, beim Exportieren von Daten sowie beim Prüfen von Optimierungsempfehlungen und beim Reagieren auf diese – und damit beim Sparen von Geld. Weitere Informationen finden Sie unter [Azure Cost Management](overview-cost-mgt.md).

## <a name="report-types"></a>Berichtstypen

Es gibt drei Arten von Cloudyn-Berichten:

- Berichte im Zeitverlauf. Beispiel: Bericht über Kosten im Zeitverlauf. Berichte im Zeitverlauf enthalten eine Zeitreihe von Daten über einen ausgewählten Zeitraum mit einer vordefinierten Auflösung sowie eine wöchentliche Auflösung für die letzten zwei Monate. Sie können mithilfe von Gruppierung und Filterung verschiedene Datenpunkte vergrößern.
  - Berichte im Zeitverlauf helfen dabei, Trends anzuzeigen und Spitzen oder Anomalien zu erkennen.
- Analyseberichte. Beispiel: Kostenanalysebericht. Diese Berichte enthalten aggregierten Daten über einen von Ihnen definierten Zeitraum und ermöglichen die Gruppierung und Filterung der Daten.
  - Analyseberichte können dabei helfen, Spitzen anzuzeigen und die Grundursachen von Anomalien zu ermitteln. Außerdem erhalten Sie eine abgestufte Aufschlüsselung Ihrer Daten.
- Tabellarische Berichte. Sie können jeden Bericht als Tabelle anzeigen, einige Berichte werden jedoch ausschließlich als Tabelle angezeigt. Diesen Berichten können Sie ausführliche Listen von Elementen entnehmen.
  - Empfehlungen sind tabellarische Berichte – es gibt keine Visualisierungen für Empfehlungen. Sie können jedoch Empfehlungsergebnisse visualisieren. Beispiel: Einsparungen im Zeitverlauf.
  - Tabellarische Berichte sind nützlich als Liste von Aktionen oder für den Datenexport zur weiteren Verarbeitung. Beispiel: Chargebackbericht.

Kostenberichte enthalten entweder _tatsächliche_ oder _amortisierte_ Kosten.

Berichte über tatsächliche Kosten enthalten die während des ausgewählten Zeitraums vorgenommenen Zahlungen. Beispielsweise werden alle einmaligen Gebühren wie etwa Käufe von reservierten Instanzen (RI) in Berichten über tatsächliche Kosten als Spitzen bei den Kosten angezeigt.

In Berichten über amortisierte Kosten werden einmalige Gebühren über den Zeitraum verteilt, für den sie gelten. Beispielsweise werden einmalige Gebühren für RI-Käufe über den Reservierungszeitraum verteilt und nicht als eine Spitze angezeigt. Die Amortisierungsansicht ist die einzige Möglichkeit, echte Trends zu erkennen und Kostenprognosen vorzunehmen.

In einigen Fällen wird die Amortisierung als separater Bericht angezeigt. Beispiele sind der Kostenanalysebericht und der Bericht zur Analyse der amortisierten Kosten. In anderen Fällen ist die Amortisierung eine Berichtsrichtlinie, z.B. die Berichte für Kostenzuteilung oder Kostenanalyse.

Sie können jeden Bericht für eine regelmäßige Ausgabe planen. Es können Schwellenwerte für Kostenberichte festgelegt werden, sodass sie auch für Warnungen nützlich sind.

## <a name="cost-analysis-vs-cost-allocation"></a>Kostenanalyse und Kostenzuteilung

In Berichten zur _Kostenanalyse_ werden Abrechnungsdaten von Ihren Cloudanbietern angezeigt. Mithilfe der Berichte können Sie verschiedene aus der Abrechnungsdatei einzeln entnommene Datensegmente gruppieren und detailliert anzeigen. Die Berichte ermöglichen eine differenzierte Navigation durch die Kosten in den Abrechnungsrohdaten Ihres Cloudanbieters.

In einigen _Kostenanalyseberichten_ werden die Kosten nicht nach Ressourcentags gruppiert. Außerdem werden tagbasierte Abrechnungsinformationen in Berichten nur aufgeführt, nachdem Sie Kosten zugeteilt haben, indem Sie ein Kostenmodell mit [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) erstellt haben.

_Kostenzuteilungsberichte_ sind verfügbar, nachdem Sie mit [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) ein Kostenmodell erstellt haben. In Cloudyn werden Kosten- und Abrechnungsdaten verarbeitet und die Daten mit den Nutzungs- und Tagdaten Ihrer Cloudkonten _abgeglichen_. Für den Abgleich der Daten benötigt Cloudyn Zugriff auf Ihre Nutzungsdaten. Wenn Sie über Konten ohne Anmeldeinformationen verfügen, werden diese als _nicht kategorisierte Ressourcen_ gekennzeichnet.

## <a name="dashboards"></a>Dashboards

Dashboards in Cloudyn bieten eine allgemeine Ansicht von Berichten. Dashboards bestehen aus Gadgets, und jedes Gadget ist im Wesentlichen eine Miniaturansicht eines Berichts. Wenn Sie [Berichte anpassen](understanding-cost-reports.md#save-and-schedule-reports), speichern Sie diese unter „Meine Berichte“. Sie werden dann dem Dashboard hinzugefügt. Weitere Informationen zu Dashboards finden Sie unter [Anzeigen der wichtigsten Kostenmetriken mit Dashboards](dashboards.md).

## <a name="budget-information-in-reports"></a>Budgetinformationen in Berichten

Viele Cloudyn-Berichte enthalten Budgetinformationen, nachdem Sie manuell ein Budget erstellt haben. Berichte enthalten also keine Budgetinformationen, bis Sie ein Budget erstellen. Weitere Informationen finden Sie unter [Budgetmanagementeinstellungen](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Berichte und Berichterstellungsfeatures

Cloudyn umfasst die folgenden Berichte und Berichterstellungsfeatures.

### <a name="cost-navigator-report"></a>Kostennavigator-Bericht

Der Kostennavigator-Bericht ist eine schnelle Möglichkeit, Ihren abgerechneten Verbrauch in einer Dashboardansicht anzuzeigen. Er weist einen Satz von Filtern und grundlegenden Ansichten auf, mit denen Sie sofort eine zusammenfassende Ansicht der Kosten der Organisation anzeigen können. Die Kosten werden nach Datum angezeigt. Da der Bericht als anfängliche Ansicht Ihrer Kosten vorgesehen ist, ist er nicht so flexibel oder so umfassend wie viele andere Berichte oder benutzerdefinierte Dashboards, die Sie selbst erstellen.

Standardmäßig enthält der Bericht folgende Hauptansichten:

- Die Kosten über die Zeit als Balkendiagramm für eine Arbeitswoche. Sie können den **Datumsbereich** für das Balkendiagramm ändern.
- Ausgaben nach Dienst als Kreisdiagramm.
- Ressourcenkategorisierung nach Tags als Kreisdiagramm.
- Ausgaben nach Kostenentitäten als Kreisdiagramm.
- Gesamtkosten pro Datum als Listenansicht.

### <a name="cost-analysis-report"></a>Bericht „Kostenanalyse“

Der Kostenanalysebericht ist eine Berechnung von Showback und Chargeback, basierend auf Ihrer Richtlinie. Er aggregiert Ihre Cloudnutzung in einem ausgewählten Zeitraum, nachdem alle Zuteilungsregeln auf Ihre Kosten angewandt wurden. Beispielsweise werden die Kosten nach Tags berechnet, die Kosten für nicht markierte Ressourcen werden neu zugewiesen, und die Auslastung der reservierten Instanzen wird optional zugeordnet.

Die in [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) festgelegten Richtlinien werden im Kostenanalysebericht verwendet, und die Ergebnisse werden dann mit Informationen aus den Rohdaten Ihres Cloudanbieters kombiniert.

Wie wird dieser Bericht berechnet? Im Cloudyn-Dienst wird durch Anwenden der _Kontenaffinität_ sichergestellt, dass bei der Kostenzuteilung die Integrität der einzelnen verknüpften Konten beibehalten wird. Durch die Affinität wird sichergestellt, dass einem Konto, das einen bestimmten Dienst nicht verwendet, keine Kosten für diesen Dienst zugeordnet werden. Die in diesem Konto anfallenden Kosten verbleiben im Konto und werden nicht über die Zuordnungsrichtlinien berechnet. Angenommen, Sie verfügen über fünf verknüpfte Konten. Wenn nur in drei dieser Konten Speicherdienste verwendet werden, werden die Kosten für Speicherdienste nur Tags in den drei Konten zugeteilt.

Sie können den Bericht „Cost Analysis“ (Kostenanalyse) für Folgendes verwenden:

- Berechnen von Chargeback/Showback Ihrer Organisation
- Kategorisieren all Ihrer Kosten
- Anzeigen einer aggregierten Ansicht Ihrer gesamten Bereitstellung für einen bestimmten Zeitraum
- Anzeigen der Kosten nach Tagkategorien basierend auf den im Kostenmodell erstellten Richtlinien

So verwenden Sie den Bericht „Cost Analysis“ (Kostenanalyse)

1. Wählen Sie einen Datumsbereich aus.
2. Fügen Sie nach Bedarf Tags hinzu.
3. Fügen Sie Gruppen hinzu.
4. Wählen Sie ein Kostenmodell aus, das Sie zuvor erstellt haben.

### <a name="cost-over-time-report"></a>Bericht für die Kosten im Lauf der Zeit

Im Bericht für die Kosten im Lauf der Zeit werden die Ergebnisse einer Kostenzuteilung als Zeitreihe gezeigt. Sie können Trends beobachten und Unregelmäßigkeiten in Ihrer Bereitstellung erkennen. Im Wesentlichen werden die über einen definierten Zeitraum verteilten Kosten angezeigt. Der Bericht enthält die hauptsächlichen Kostenbeiträge, einschließlich der laufenden Kosten und einmaligen Gebühren für reservierte Instanzen, die innerhalb eines ausgewählten Zeitraums aufgewendet werden. In diesem Bericht können die in [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) festgelegten Richtlinien verwendet werden.

Sie können den Bericht „Cost Over Time“ (Kosten im Zeitverlauf) für Folgendes verwenden:

- Anzeigen von Änderungen im Zeitverlauf und der Einwirkungen, die sich von einem Tag (oder Datumsbereich) auf den anderen ändern
- Analysieren der Kosten im Zeitverlauf für eine bestimmte Instanz
- Analysieren der Ursachen für den Kostenanstieg für eine bestimmte Instanz

So verwenden Sie den Bericht „Cost Over Time“ (Kosten im Zeitverlauf)

1. Wählen Sie einen Datumsbereich aus.
2. Fügen Sie nach Bedarf Tags hinzu.
3. Fügen Sie Gruppen hinzu.
4. Wählen Sie ein Kostenmodell aus, das Sie zuvor erstellt haben.
5. Wählen Sie Ist-Kosten oder amortisierte Kosten aus.
6. Legen Sie fest, ob Zuordnungsregeln angewendet werden, um die Ansicht der Abrechnungsrohdaten anzuzeigen oder um Kosten in der Ansicht neu zu berechnen.

### <a name="actual-cost-analysis-report"></a>Bericht zur Analyse der Ist-Kosten

Im Bericht zur Analyse der Ist-Kosten werden die Anbieterkosten ohne Änderungen dargestellt. Die hauptsächlichen Kostenbeiträge werden einschließlich der laufenden Kosten und einmaligen Gebühren angezeigt.

Sie können über den Bericht Kosteninformationen für Ihre Abonnements anzeigen. Im Bericht werden Azure-Abonnements in der Form **Kontoname** und **Kontonummer** angezeigt. Für **verknüpfte Konten** werden AWS-Abonnements angezeigt. Um die Kosten pro Abonnement als Aufschlüsselung für jedes Konto anzuzeigen, wählen Sie unter **Groups** (Gruppen) den Typ Ihres Abonnements aus.

Sie können den Bericht „Actual Cost Analysis“ (Analyse der Ist-Kosten) für Folgendes verwenden:

- Analysieren und Überwachen der innerhalb eines angegebenen Zeitraums aufgewendeten rohen Anbieterkosten
- Planen einer Schwellenwertwarnung
- Analysieren der unveränderten angefallenen Kosten für Ihre Konten und Entitäten

### <a name="actual-cost-over-time-report"></a>Bericht für Ist-Kosten im Zeitverlauf

Der Bericht „Actual Cost Over Time“ (Ist-Kosten im Zeitverlauf) ist ein Standardbericht zur Kostenanalyse, in dem Kosten über eine definierte Zeitauflösung verteilt werden. Im Bericht werden Ausgaben im Zeitverlauf angezeigt, sodass Sie Trends beobachten und Unregelmäßigkeiten bei den Kosten erkennen können. In diesem Bericht werden die hauptsächlichen Kostenbeiträge, einschließlich der laufenden Kosten und einmaligen Gebühren für reservierte Instanzen, angezeigt, die innerhalb eines ausgewählten Zeitraums aufgewendet werden.

Sie können den Bericht „Actual Cost Over Time“ (Ist-Kosten im Zeitverlauf) für Folgendes verwenden:

- Anzeigen von Kostentrends über einen bestimmten Zeitraum
- Suchen von Unregelmäßigkeiten bei den Kosten
- Anzeigen aller kostenbezogenen Belange in Bezug auf Cloudanbieter

### <a name="amortized-cost-reports"></a>Berichte zu amortisierten Kosten

In dieser Gruppe von Berichten zu amortisierten Kosten werden linearisierte nicht nutzungsbasierte Dienstgebühren oder einmalig zu zahlende Kosten angezeigt und diese Kosten gleichmäßig während ihrer Lebensdauer aufgeteilt. Beispiele für einmalige Gebühren:

- Jährliche Supportgebühren
- Jährliche Gebühren für Sicherheitskomponenten
- Gebühren für den Kauf von reservierten Instanzen
- Einige Azure Marketplace-Elemente

In der Abrechnungsdatei werden einmalige Gebühren gekennzeichnet, wenn das Start- und das Enddatum (Zeitstempel) der Dienstnutzung gleiche Werte aufweisen. Der Cloudyn-Dienst erkennt diese dann als einmalige Gebühren, die amortisiert werden. Andere nutzungsbasierte Dienste mit Kosten für bedarfsgesteuerte Nutzung werden nicht amortisiert.

Berichte zu amortisierten Kosten schließen ein:

- Analyse der amortisierten Kosten
- Amortisierte Kosten im Zeitverlauf

### <a name="cost-analysis-report"></a>Bericht „Kostenanalyse“

Der Bericht „Cost Analysis“ (Kostenanalyse) bietet Einblick in die Cloudnutzung und die für die Cloud anfallenden Kosten innerhalb eines ausgewählten Zeitraums. Die in [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) festgelegten Richtlinien werden im Kostenanalysebericht verwendet.

Wie wird dieser Bericht in Cloudyn berechnet?

In Cloudyn wird durch Anwenden der _Kontenaffinität_ sichergestellt, dass bei der Kostenzuteilung die Integrität der einzelnen verknüpften Konten beibehalten wird. Durch die Affinität wird sichergestellt, dass einem Konto, das einen bestimmten Dienst nicht verwendet, auch keine Kosten für diesen Dienst zugeordnet werden. Die in diesem Konto anfallenden Kosten verbleiben im Konto und werden nicht über die Zuordnungsrichtlinien berechnet. Angenommen, Sie verfügen über fünf verknüpfte Konten. Wenn nur in drei dieser Konten Speicherdienste verwendet werden, werden die Kosten für Speicherdienste nur Tags in den drei Konten zugeteilt.

Sie können den Bericht „Cost Analysis“ (Kostenanalyse) für Folgendes verwenden:

- Anzeigen einer aggregierten Ansicht Ihrer gesamten Bereitstellung für einen bestimmten Zeitraum
- Anzeigen der Kosten nach Tagkategorien basierend auf den im Kostenmodell erstellten Richtlinien

### <a name="cost-over-time-report"></a>Bericht für die Kosten im Lauf der Zeit

Im Bericht für Kosten im Zeitverlauf werden Ausgaben im Zeitverlauf angezeigt, sodass Sie Trends beobachten und Unregelmäßigkeiten in Ihrer Bereitstellung erkennen können. Im Wesentlichen werden die über einen definierten Zeitraum verteilten Kosten angezeigt. Der Bericht enthält die hauptsächlichen Kostenbeiträge, einschließlich der laufenden Kosten und einmaligen Gebühren für reservierte Instanzen, die innerhalb eines ausgewählten Zeitraums aufgewendet werden. In diesem Bericht können die in [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) festgelegten Richtlinien verwendet werden.

Sie können den Bericht „Cost Over Time“ (Kosten im Zeitverlauf) für Folgendes verwenden:

- Anzeigen von Änderungen im Zeitverlauf und der Einwirkungen, die sich von einem Tag (oder Datumsbereich) auf den anderen ändern
- Analysieren der Kosten im Zeitverlauf für eine bestimmte Instanz
- Analysieren der Ursachen für den Kostenanstieg für eine bestimmte Instanz

### <a name="custom-charges-report"></a>Benutzerdefinierter Gebührenbericht

Unternehmens- und CSP-Benutzer stellen häufig zusätzliche Dienste für ihre internen oder externen Kunden bereit, die zu ihrem eigenen Cloudressourcenverbrauch hinzukommen. Sie definieren benutzerdefinierte Gebühren für zusätzliche Dienste oder Rabatte, die den Abrechnungs- oder Chargebackberichten der Kunden als benutzerdefinierte Einzelposten hinzugefügt werden.

Benutzerdefinierte Dienstgebühren geben Dienste wieder, die normalerweise in einer Rechnung nicht aufgeführt werden. Die erstellten benutzerdefinierten Gebühren werden dann in Kostenberichten angezeigt.

*Benutzerdefinierte Gebühren sind nicht das gleiche wie benutzerdefinierte Preise*. In der Liste der benutzerdefinierten Gebühren werden nicht die verschiedenen Preise angezeigt, die Sie möglicherweise berechnen. Beispielsweise werden AWS-Abrechnungsgebühren erst angezeigt, wenn Sie in Rechnung gestellt wurden.

So erstellen Sie eine benutzerdefinierte Gebühr

1. Klicken Sie unter **Custom Charges** (Benutzerdefinierte Gebühren) auf **Add New** (Neue hinzufügen). Das Dialogfeld _Add New Custom Charge_ (Neue benutzerdefinierte Gebühr hinzufügen) wird angezeigt.
2. Geben Sie unter **Provider Name** (Anbietername) den Namen des Anbieters ein.
3. Geben Sie unter **Service Name** (Dienstname) den Typ des Diensts ein.
4. Fügen Sie unter **Description** (Beschreibung) eine Beschreibung für die benutzerdefinierte Gebühr hinzu.
5. Geben Sie unter **Type** (Typ) den ausgewählten **Prozentsatz** ein, und wählen Sie dann in der Dropdownliste „Services“ (Dienste) die Dienste aus, die als benutzerdefinierte Gebühren in die Kostenberichte eingeschlossen werden sollen.
6. Wählen Sie unter **Payment** (Zahlung) aus, ob es sich um eine einmalige oder eine laufende Gebühr handeln soll. Wenn es sich bei der Gebühr um laufende Kosten handelt, wählen Sie „Amortized“ (Amortisiert) aus, wenn die Gebühr amortisiert werden soll, und wählen Sie dann die Anzahl der Monate aus.
7. Wenn Sie eine einmalige Gebühr ausgewählt haben, geben Sie in **Daten** (Datumsangaben) unter **Effective Date** (Gültigkeitsdatum) das Datum ein, an dem die Gebühr bezahlt wird. Wenn Sie laufende Kosten ausgewählt haben, geben Sie den Datumsbereich vom Start- bis zum Enddatum für die Gebühr ein.
8. Wählen Sie in der Struktur **Entities** (Entitäten) die Entitäten aus, auf die Sie die Gebühr anwenden möchten, und wählen Sie dann **On** (Auf) aus.

_Wenn Gebühren einer Entität zugewiesen sind, können Benutzer diese nicht ändern. Gebühren, die von einem Administrator einer übergeordneten Entität hinzugefügt wurden, sind schreibgeschützt._

So zeigen Sie benutzerdefinierte Gebühren an

Benutzerdefinierte Gebühren werden in Kostenberichten angezeigt. Öffnen Sie beispielsweise den Bericht „Actual Cost Analysis“ (Analyse der Ist-Kosten), und wählen Sie dann unter **Extended Filters** (Erweiterte Filter) die Option **Standalone** (Eigenständig) aus. Filtern Sie dann, sodass **Benutzerdefinierte Gebühren** angezeigt werden.

### <a name="cost-allocation-360"></a>Cost Allocation 360

Sie verwenden Cost Allocation 360 zum Erstellen von benutzerdefinierten Kostenzuordnungsmodellen, damit Sie verbrauchten Cloudressourcen Kosten zuweisen können. Viele Berichte enthalten Informationen aus benutzerdefinierten Kostenmodellen, die Sie mit benutzerdefinierten Kostenmodellen erstellt haben. In einigen Berichten werden zudem nur Informationen angezeigt, nachdem Sie ein benutzerdefiniertes Kostenmodell mit Kostenzuteilung erstellt haben.

Weitere Informationen zum Erstellen von benutzerdefinierten Kostenmodellen finden Sie unter [Tutorial: Verwalten von Kosten mithilfe von Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Bericht über Budget im Zeitverlauf

Der Bericht über Budget im Zeitverlauf ermöglicht Ihnen einen Vergleich der hauptsächlichen Kostenbeiträge für Ihr Budget. Das zugewiesene Budget wird im Bericht angezeigt, sodass Sie Ihren Budgetverbrauch (über/unter/entsprechend Erwartung) über die Zeit einsehen können. Über „Show/Hide Fields“ (Felder ein-/ausblenden) oben im Bericht können Sie die Kosten, das Budget, die akkumulierten Kosten und das Gesamtbudget anzeigen.

### <a name="current-month-projected-cost-report"></a>Bericht zu den vorhergesagten Kosten für den aktuellen Monat

Der Bericht zu den vorhergesagten Kosten für den aktuellen Monat enthält eine Kostenzusammenfassung seit Monatsanfang bis zum aktuellen Datum. Dieser Bericht zeigt die Kosten vom Anfang des Monats und dem vorhergehenden Monat sowie die voraussichtlichen Gesamtkosten für den aktuellen Monat an. Die voraussichtlichen Kosten für den aktuellen Monat werden als Summe der monatlichen Kosten bis zum aktuellen Datum und einer Projektion auf Grundlage der in den letzten 30 Tagen überwachten Kosten berechnet.

Verwenden Sie den Bericht zu den vorhergesagten Kosten für den aktuellen Monat für Folgendes:

- Vorhersage der monatlichen Kosten nach Dienst
- Vorhersage der monatlichen Kosten nach Konto

### <a name="annual-projected-cost-report"></a>Bericht über die vorhergesagten jährlichen Kosten

Dem Bericht über die vorhergesagten jährlichen Kosten können Sie die vorhergesagten jährlichen Kosten basierend auf früheren Ausgabentrends entnehmen. Er zeigt die voraussichtlichen Gesamtkosten für die nächsten 12 Monate an. Die Vorhersagen erfolgen mithilfe einer Trendfunktion mit Extrapolation der nächsten 12 Monate, basierend auf dem Verbrauch der letzten 30 Tage.

### <a name="budget-management-settings"></a>Budgetmanagementeinstellungen

Im Budgetmanagement können Sie ein Budget für Ihr Geschäftsjahr festlegen.

So fügen Sie einer Entität ein Budget hinzu

1. Wählen Sie auf der Seite „Budgetverwaltung“ unter **Entität** die Entität, in der Sie das Budget erstellen möchten.
2. Wählen Sie unter „Budgetjahr“ das Jahr, für das Sie das Budget erstellen möchten.
3. Legen Sie für jeden Monat Ihr Budget fest, und klicken Sie dann auf **Save** (Speichern).

So importieren Sie eine Datei für das Jahresbudget:

1. Wählen Sie unter **Aktionen** die Option **Export**, um eine leere CSV-Vorlage als Basis für Ihr Budget herunterzuladen.
2. Tragen Sie in die CSV-Datei Ihre Budgeteinträge ein, und speichern Sie sie lokal.
3. Wählen Sie unter **Aktionen** die Option **Import**.
4. Wählen Sie Ihre gespeicherte Datei aus, und klicken Sie auf **OK**.

Um Ihr abgeschlossenes Budget als CSV-Datei zu exportieren, wählen Sie unter **Aktionen** die Option **Export**, um die Datei herunterzuladen.

Nach Abschluss wird Ihr Budget in den Berichten zur Kostenanalyse und zum Vergleich der Kosten und des Budgets im Lauf der Zeit aufgeführt. Sie können auch Berichte basierend auf Budgetschwellenwerten planen.

### <a name="azure-resource-explorer-report"></a>Azure-Ressourcen-Explorer-Bericht

Im Azure-Ressourcen-Explorer-Bericht wird eine Gesamtliste aller in Cloudyn verfügbaren Azure-Ressourcen angezeigt. Um den Bericht effektiv verwenden zu können, sollten für Ihre Azure-Konten erweiterte Metriken aktiviert sein. Erweiterte Metriken bieten Cloudyn Zugriff auf Ihre virtuellen Azure-Computer. Weitere Informationen finden Sie unter [Hinzufügen erweiterter Metriken für virtuelle Azure-Computer](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Bericht über Azure-Ressourcen im Lauf der Zeit

Der Bericht über Azure-Ressourcen im Lauf der Zeit zeigt eine Aufschlüsselung aller Ressourcen an, die über einen bestimmten Zeitraum laufen. Um den Bericht effektiv verwenden zu können, sollten für Ihre Azure-Konten erweiterte Metriken aktiviert sein. Erweiterte Metriken bieten Cloudyn Zugriff auf Ihre virtuellen Azure-Computer. Weitere Informationen finden Sie unter [Hinzufügen erweiterter Metriken für virtuelle Azure-Computer](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Instanz-Explorer-Bericht

Im Instanz-Explorer-Bericht können Sie verschiedene Metriken für die Ressourcen Ihrer virtuellen Computer anzeigen. Sie können einen Drilldown in spezifische Instanzen ausführen, um Informationen wie die folgenden anzuzeigen:
- Ausführungsintervalle der Instanz
- Lebenszyklus im ausgewählten Zeitraum
- CPU-Auslastung
- Netzwerkeingaben
- Ausgabedatenverkehr
- Aktive Datenträger

Im Instanz-Explorer-Bericht werden alle laufenden Intervalle innerhalb des definierten Datumsbereichs gesammelt und die Daten entsprechend aggregiert. Um jedes laufende Intervall im ausgewählten Datumsbereich anzuzeigen, erweitern Sie die Instanz. Die Kosten für jede Instanz werden anhand des ausgewählten Datumsbereichs basierend auf AWS- und Azure-Listenpreisen berechnet. Es werden keine Rabatte angewandt. Sie können dem Bericht mithilfe von „Show/Hide Fields“ (Felder ein-/ausblenden) zusätzliche Felder hinzufügen.

Verwenden Sie den Instanz-Explorer-Bericht für Folgendes:

- Berechnen der geschätzten Kosten pro Computer
- Erstellen einer vollständigen Liste aller Computer, die während eines Zeitbereichs aktiv waren, einschließlich der aggregierten Ausführungsstunden
- Erstellen einer Liste nach Clouddienstanbieter oder Konto
- Anzeigen der während eines Zeitraums erstellten oder beendeten Computer
- Anzeigen aller derzeit beendeten Computer
- Anzeigen der Tags der einzelnen Computer

### <a name="instances-over-time-report"></a>Bericht für Instanzen im Lauf der Zeit

Im Bericht für Instanzen im Lauf der Zeit sehen Sie die maximale Anzahl von Computern, die während des ausgewählten Zeitraums aktiv waren. Wenn die Auflösung nach Woche oder Monat definiert ist, sind die Ergebnisse die maximale Anzahl von aktiven Computern an einem bestimmten Tag während dieses Monats. Wählen Sie einen Datumsbereich aus, um die Filter für die Anzeige des Berichts auszuwählen.

### <a name="instance-utilization-over-time-report"></a>Bericht für Instanznutzung im Lauf der Zeit

Dieser Bericht zeigt eine Aufschlüsselung der CPU- oder Arbeitsspeichernutzung im Lauf der Zeit für all Ihre Instanzen an.

### <a name="compute-power-cost-over-time-report"></a>Bericht für die Kosten der Computeleistung im Lauf der Zeit

Der Bericht für die Kosten der Computeleistung im Lauf der Zeit bietet eine Aufschlüsselung der Computeleistung über einen angegebenen Datumsbereich. Andere Berichte zeigen zwar die Anzahl der ausgeführten Computer oder der Ausführungsstunden an, dieser Bericht zeigt jedoch Kernstunden, Computeeinheitstunden oder GB-RAM-Stunden an.

Verwenden Sie den Bericht für Folgendes:

- Überprüfen der Computeleistung innerhalb eines angegebenen Datumsbereichs
- Anzeigen der Computezeiten anhand von Kostenzuteilungsmodellen

Dieser Bericht ist mit Ihren [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-Richtlinien verknüpft, daher werden Ergebnisse basierend auf den definierten Tags und Ihrer ausgewählten Kostenrichtlinie angezeigt. Wenn Sie keine Richtlinie erstellt haben, werden keine Ergebnisse angezeigt.

### <a name="compute-power-average-cost-over-time-report"></a>Bericht für die durchschnittlichen Kosten der Computeleistung im Lauf der Zeit

Verwenden Sie den Bericht für die durchschnittlichen Kosten der Computeleistung im Lauf der Zeit, um mehr als nur die Kosten für jeden ausgeführten Computer anzuzeigen. Der Bericht zeigt die durchschnittlichen Kosten pro Instanzstunde, Kernstunde, Computeeinheitstunde und GB-RAM-Stunde an. Der Bericht bietet einen Einblick in die Effizienz Ihrer Bereitstellung.

Dieser Bericht ist mit Ihren [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-Richtlinien verknüpft, daher werden Ergebnisse basierend auf den definierten Tags und Ihrer ausgewählten Kostenrichtlinie angezeigt. Wenn Sie keine Richtlinie erstellt haben, werden keine Ergebnisse angezeigt.

### <a name="s3-cost-over-time-report"></a>Bericht für S3-Kosten im Lauf der Zeit

Der Bericht für S3-Kosten im Lauf der Zeit bietet eine Aufschlüsselung der Kosten für den Amazon Simple Storage Service (S3) pro Bucket im Lauf der Zeit für einen angegebenen Zeitraum. Der Bericht hilft Ihnen dabei, die Buckets zu finden, die Ihre hauptsächlichen Kosten verursachen, und zeigt Trends in Ihrer S3-Nutzung und Ihren Ausgaben für S3.

### <a name="s3-distribution-of-cost-report"></a>Bericht über S3-Verteilung der Kosten

Verwenden Sie den Bericht, um Ihre S3-Kosten für den letzten Monat nach Bucket und Speicherklasse zu analysieren. In der Kreisdiagrammansicht können Sie den Schwellenwert für die Sichtbarkeit festlegen. In der Tabellenansicht können Sie außerdem Teilergebnisse anzeigen.

### <a name="s3-bucket-properties-report"></a>Bericht über S3-Bucketeigenschaften

Verwenden Sie den Bericht, um die S3-Bucketeigenschaften anzuzeigen. In der Kreisdiagrammansicht können Sie den Schwellenwert für die Sichtbarkeit festlegen. In der Tabellenansicht können Sie außerdem Teilergebnisse anzeigen.

### <a name="rds-instances-over-time-report"></a>Bericht für RDS-Instanzen im Lauf der Zeit

Verwenden Sie den Bericht, um eine Aufschlüsselung aller RDS-Instanzen (Amazon Relational Database Service) anzuzeigen, die während des angegebenen Zeitraums ausgeführt wurden.

### <a name="rds-active-instances-report"></a>Bericht über aktive RDS-Instanzen

Verwenden Sie den Bericht, um aktive RDS-Instanzen zu analysieren. Erweitern Sie im Bericht die Position, um weitere Informationen anzuzeigen.

### <a name="azure-reserved-instances-report"></a>Bericht über Azure Reserved Instances

Der Bericht über Azure Reserved Instances bietet Ihnen eine Gesamtübersicht über alle Azure Reserved Instances. Im Bericht wird jeder Kauf als eigene Position angezeigt. Außerdem werden im Bericht Informationen zu diesem Kauf angezeigt, z.B. das Konto für den Kauf, der Typ des Kaufs und der Instanztyp, die verbleibenden Tage usw. Sie können Berichtsdaten mithilfe von „Show/Hide Fields“ (Felder ein-/ausblenden) ein- bzw. ausblenden.

Mit dem Bericht über Azure Reserved Instances können Sie Folgendes anzeigen:

- Liste aller Reservierungen nach Kaufdatum
- Verbleibende Zeit bis zum Ablauf der RI
- Einmalige Gebühren
- Konto, das RIs gekauft hat, und Zeitpunkt des Kaufs

### <a name="aws-reserved-instances-report"></a>Bericht über reservierte AWS-Instanzen

Der Bericht über reservierte AWS-Instanzen bietet Ihnen eine Gesamtübersicht über alle reservierten AWS-Instanzen. Im Bericht wird jeder Kauf als eigene Position angezeigt. Dazu kommen Informationen zu diesem Kauf, z.B. das Konto für den Kauf, der Typ des Kaufs und der Instanztyp, die verbleibenden Tage usw. Sie können Berichtsdaten mithilfe von „Show/Hide Fields“ (Felder ein-/ausblenden) ein- bzw. ausblenden.

Mit dem Bericht überreservierte AWS-Instanzen können Sie Folgendes anzeigen:

- Liste aller Reservierungen nach Kaufdatum
- Verbleibende Zeit bis zum Ablauf der RI
- Einmalige Gebühren
- Ursprüngliche Kauf-ID (Reservierungs-ID)
- Konto, das RIs gekauft hat, und Zeitpunkt des Kaufs

### <a name="ec2-ri-buying-recommendations-report"></a>Bericht über EC2-RI-Kaufempfehlungen

Die Grundlage für die Cloudressourcennutzung ist das Bedarfsmodell, in dem Ressourcen erst bei Verwendung Kosten verursachen. Es gibt keine Vorabverpflichtung – Sie bezahlen nur für die tatsächliche Verwendung.

AWS bietet ein alternatives Preismodell für die EC2-Dienste (Elastic Compute Cloud) – die reservierte Instanz (RI). Dieses Preismodell garantiert Benutzern die benötigte Kapazität für die Dauer der RI. Die RI bietet erhebliche Rabatte gegenüber den Preisen für Nutzung bei Bedarf. Im Gegenzug gehen Benutzer eine Vorabverpflichtung für die Nutzung einer virtuellen Instanz ein. Die Verpflichtung ist an eine bestimmte Familie, Größe, Verfügbarkeitszone sowie ein Betriebssystem über den Zeitraum der Verpflichtung (ein oder drei Jahre) gebunden. Mithilfe der RI kann AWS zukünftige Kapazitäten effizient planen und sich eine Kundenverpflichtung für die Nutzung der Dienste sichern.

Es gibt drei Vorauszahlungsoptionen für RIs:

- Gesamtbetrag an Tag 0 mit dem größten Rabatt.
- Keine Vorauszahlung: Die Kosten der RI werden in monatlichen Raten über die Dauer der RI mit dem geringsten Rabatt bezahlt.
- Teilvorauszahlung: ¼ bis ½ des Preises wird im Voraus bezahlt und der Rest in monatlichen Raten mit einem Rabatt, der etwas geringer als der Tarif mit vollständiger Bezahlung im Voraus ist.

Cloudyn bewertet die Betriebszeit jedes Computers in den letzten 30 Tagen. Cloudyn empfiehlt den Kauf von RIs, wenn der Betrieb des Computers mit einer RI beim aktuellen Betriebszeitgrad kostengünstiger ist.

Der Bericht zeigt die Begründung für die Empfehlungen, um das meiste Geld im Lauf des Jahres einsparen zu können. In den Empfehlungen wird vorgeschlagen, Instanzen bei Bedarf durch RIs zu ersetzen. Sie können RIs direkt aus dem Bericht kaufen.

Auf jeder Registerkarte wird ein vollständiger Bericht geöffnet. Zu den bedeutenden Abschnitten auf den Registerkarten gehören die folgenden:

- **EC2 RI Purchase Impact** (Auswirkung des EC2-RI-Kaufs): Dieser Abschnitt bietet eine Simulation des Unterschieds zwischen einer bedarfsgesteuerten im Vergleich zu reservierten Instanzen. Klicken Sie auf **Zoom in** (Vergrößern), um den vollständigen Bericht zur Auswirkung des EC2-RI-Kaufs mit den bereits für Ihre Empfehlung definierten Filtern anzuzeigen. Dieser Bericht zeigt die Auswirkungen für alle möglichen RI-Käufe an. Sie können die erwartete durchschnittliche Betriebszeit anpassen, um das Einsparungspotenzial beim Kauf reservierter EC2-Instanzen anzuzeigen.

- **Saving Analysis** (Einsparungsanalyse): Dieser Abschnitt enthält die möglichen erzielten Einsparungen beim Befolgen der Cloudyn-Empfehlungen sowie den Monat, in dem die Einsparungen erzielt werden. Die tatsächlichen Einsparungen und der eingesparte prozentuale Anteil sind rot markiert.

- **EC2 RI Type Comparison** (EC2-RI-Typvergleich): In diesem Abschnitt werden die wichtigsten Auswirkungen der von Cloudyn empfohlenen Bereitstellung auf die Rendite hervorgehoben, einschließlich aller relevanten Optionen. In den Ergebnissen dieses Berichts wird davon ausgegangen, dass der Computer mit einer Betriebszeit von 100 % ausgeführt wird. Klicken Sie auf **Zoom in** (Vergrößern), um den detaillierten Bericht zu öffnen.

- **Instances Over Time** (Instanzen im Lauf der Zeit): Dieser Abschnitt enthält eine Aufschlüsselung aller Instanzen im Zusammenhang mit der Empfehlung, ausgeschlüsselt in OnDemand, reservierte Instanzen und Spot. Klicken Sie auf **Zoom in** (Vergrößern), um den detaillierten Bericht zu öffnen.
- **Breakeven Points** (Gewinnschwellen): Dieser Abschnitt enthält eine Tabelle aller möglichen empfohlenen Bereitstellungen mit der Rendite und dem Monat, in dem die Rendite erfolgt. Klicken Sie auf **Zoom in** (Vergrößern), um den detaillierten Bericht zu öffnen.

### <a name="ec2-reservations-over-time-report"></a>Bericht für EC2-Reservierungen im Lauf der Zeit

Der Bericht für EC2-Reservierungen im Lauf der Zeit verfolgt den Status der Nutzung Ihrer gekauften EC2-RIs. Sie können die Auflösung des Berichts auf Stunde, Tag oder Woche festlegen.

Verwenden Sie den Bericht für Folgendes:

- Anzeigen der gekauften Reservierungen, die verwendet oder nicht verwendet wurden.
- Führen Sie einen Drilldown zur Auflösung nach Stunde aus, um die RI-Nutzung pro Stunde anzuzeigen.

### <a name="savings-over-time-report"></a>Bericht über Einsparungen im Lauf der Zeit

Verwenden Sie den Bericht über Einsparungen im Lauf der Zeit, um die mit reservierten Instanzen sowie Spot-Instanzen erreichten Einsparungen anzuzeigen. Der Bericht zeigt die Rendite im Lauf der Zeit infolge der RI-Käufe an.

Um Einsparungen von RIs anzuzeigen, gruppieren Sie die Ergebnisse nach **Preismodell**, und wählen Sie **Reservation** (Reservierung) aus. Um die von einem bestimmten Konto oder einem Instanztyp erreichten RI-Einsparungen anzuzeigen, fügen Sie die relevante Gruppierung hinzu, und filtern Sie nach dem Konto oder dem Instanztyp.

Um die Einsparungen durch die Verwendung von Spot-Instanzen anzuzeigen, filtern Sie das **Preismodell** nach **Spot**. Der Standardfilter für diesen Bericht ist nach RI und Spot-Instanzen.

### <a name="rds-ri-buying-recommendations-report"></a>Bericht über RDS-RI-Kaufempfehlungen

Im Bericht über RDS-RI-Kaufempfehlungen wird empfohlen, wann RDS-RIs anstelle von Instanzen nach Bedarf verwendet werden sollten.

Auf jeder Registerkarte wird ein vollständiger Bericht geöffnet. Zu den bedeutenden Abschnitten auf den Registerkarten gehören die folgenden:

- **RDS RI Purchase Impact** (Auswirkung des RDS-RI-Kaufs): Dieser Abschnitt bietet eine Simulation des Unterschieds zwischen einer bedarfsgesteuerten im Vergleich zu reservierten Instanzen. Klicken Sie auf **Zoom in** (Vergrößern), um den vollständigen Bericht zur Auswirkung des RDS-RI-Kaufs mit den bereits für Ihre Empfehlung definierten Filtern anzuzeigen. Sie können dem Bericht die Auswirkungen für alle möglichen RI-Käufe entnehmen.  Sie können die erwartete durchschnittliche Betriebszeit anpassen und die möglichen Einsparungen durch den Erwerb von RIs anzeigen.
- **Saving Analysis** (Einsparungsanalyse): Dieser Abschnitt enthält die möglichen erzielten Einsparungen beim Befolgen der Cloudyn-Empfehlungen sowie den Monat, in dem die Einsparungen erzielt werden. Die tatsächlichen Einsparungen und der eingesparte prozentuale Anteil sind rot markiert.

- **RDS RI Type Comparison** (RDS-RI-Typvergleich): In diesem Abschnitt werden die wichtigsten Auswirkungen der empfohlenen Bereitstellung auf die Rendite hervorgehoben, einschließlich aller relevanten Optionen. In den Ergebnissen dieses Berichts wird davon ausgegangen, dass der Computer mit einer Betriebszeit von 100 % ausgeführt wird. Klicken Sie auf **Zoom in** (Vergrößern), um den detaillierten Bericht für den ausgewählten Computer zu öffnen.
- **Instances Over Time** (Instanzen im Lauf der Zeit): Dieser Abschnitt enthält eine Aufschlüsselung aller Instanzen im Zusammenhang mit der Empfehlung, OnDemand, reservierte Instanzen und Spot. Klicken Sie auf **Zoom in** (Vergrößern), um den detaillierten Bericht zu öffnen.

- **Breakeven Points** (Gewinnschwelle): Dieser Abschnitt enthält eine Tabelle aller möglichen empfohlenen Bereitstellungen mit der Rendite und den Monat, in dem die Rendite erfolgt. Klicken Sie auf **Zoom in** (Vergrößern), um den detaillierten Bericht zu öffnen.

### <a name="rds-reservations-over-time-report"></a>Bericht für RDS-Reservierungen im Lauf der Zeit

Verwenden Sie den Bericht für RDS-Reservierungen im Lauf der Zeit, um eine Aufschlüsselung Ihrer genutzten und nicht genutzten Reservierungen innerhalb des angegebenen Zeitraums anzuzeigen.

### <a name="reserved-instance-purchase-impact-report"></a>Bericht über die Auswirkung des Kaufs einer reservierten Instanz

Im Bericht über die Auswirkung des Kaufs von EC2-RIs können Sie die Kosten für reservierte Instanzen im Vergleich mit den Kosten nach Bedarf über die Zeit simulieren. Er trägt dazu bei, dass Sie bessere Kaufentscheidungen treffen können. Passen Sie die Filter wie z.B. durchschnittliche Betriebszeit, Laufzeit, Plattform und andere an, um informierte Entscheidungen treffen zu können, wenn Sie RI-Käufe in Betracht ziehen.

### <a name="cost-effective-sizing-recommendations-report"></a>Bericht über Empfehlungen zu kosteneffektiven Größen

Der Bericht über Empfehlungen zu kosteneffektiven Größen bietet Ergebnisse für AWS und Azure. Bei AWS-Benutzern werden Ihre RI-Käufe in Betracht gezogen, und die Ergebnisse schließen keine Computer ein, die als RIS ausgeführt werden. Dieser Bericht enthält eine Liste von nicht ausgelasteten Instanzen, die Kandidaten für eine Verkleinerung sind. Die Empfehlungen basieren auf Ihrer Nutzung und den Leistungsdaten der letzten 30 Tage. In jeder Empfehlung gibt es eine Liste der Kandidaten für eine Verkleinerung, die Begründung für die Verkleinerung und einen Link zu den vollständigen Details und den Leistungsmetriken der Instanz. Außerdem werden relevante Empfehlungen für Wechsel zu Instanztypen einer neueren Generation aufgeführt.

Sie können die Liste der für eine Verkleinerung empfohlenen Instanz-IDs in diesem Bericht nicht herunterladen. Um Instanz-IDs herunterzuladen, verwenden Sie den Bericht über alle Größenempfehlungen.

Betrachten Sie das folgende Beispiel für eine Verkleinerung:

Sie haben sechs ausgeführte m3.xlarge-Instanzen. Die Analyse durch Cloudyn zeigt, dass fünf von ihnen eine niedrige CPU-Auslastung aufweisen. Ziehen Sie in Betracht, sie zu verkleinern.

Im Bericht über Kostenauswirkung wird die Auswirkung auf die Kosten berechnet. In diesem Beispiel sehen Sie durch Erweitern der Position, dass der aktuelle Preis für eine m3.xlarge-Instanz (Linux/Unix) bei 0,266 US-Dollar pro Stunde und der Preis für eine m3.large-Instanz (Linux/Unix) bei 0,133 US-Dollar pro Stunde liegt. Die jährlichen Kosten betragen also 11.651 US-Dollar für fünf m3.xlarge-Instanzen, die zu 100 % ausgelastet sind. Die jährlichen Kosten betragen 5.825 US-Dollar für fünf m3.large-Instanzen, die zu 100 % ausgelastet sind. Die potenziellen Einsparungen betragen 5.825 US-Dollar.

Um die Begründungen für kosteneffektive Größen anzuzeigen, klicken Sie auf +, um die Position zu erweitern. Unter **Details** finden Sie folgende Informationen:

- Der Abschnitt **Recommendation Justification** (Empfehlungsbegründung) enthält die aktuelle Bereitstellung und die Anzahl der Instanzen, für die eine Verkleinerung empfohlen wird.
- Der Abschnitt **Cost Impact** (Kostenauswirkung) enthält die Berechnung zum Ermitteln der potenziellen Einsparungen.
- Der Abschnitt **Potential Annual Savings** (Potenzielle jährliche Einsparungen) enthält die potenziellen jährlichen Einsparungen durch eine Verkleinerung gemäß den Empfehlungen von Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Bericht über alle Größenempfehlungen

Dieser Bericht enthält eine Liste von nicht ausgelasteten Instanzen, die Kandidaten für eine Verkleinerung sind. Die Empfehlungen basieren auf Ihrer Nutzung und den Leistungsdaten der letzten 30 Tage. In jeder Empfehlung können Sie vollständige Details und Leistungsmetriken der Instanz anzeigen.

Wenn Sie reservierte AWS-Instanzen gekauft haben, enthält dieser Bericht die Ergebnisse für alle ausgeführten Instanzen, einschließlich Instanzen, die als RIs ausgeführt werden.

Mit dem Bericht über alle Größenempfehlungen können Sie Folgendes erreichen:

- Anzeigen einer Liste aller Instanzen, die Kandidaten für eine Verkleinerung sind.
- Exportieren einer Berichtsliste mit den Instanznamen und -IDs.

Klicken Sie zum Anzeigen von Details zur Empfehlung für eine bestimmte Instanz auf **+**. Der Abschnitt „Recommendation Details“ (Details zur Empfehlung) bietet eine Übersicht über die Empfehlung.

Der Abschnitt **Tags** enthält eine Liste der Tagschlüssel und -werte für die ausgewählte Instanz. Verwenden Sie „Tags“ im linken Bereich, um den Abschnitt zu filtern.

Der Abschnitt **CPU Utilization** (CPU-Auslastung) enthält die CPU-Auslastung für die Instanz über den letzten Monat nach Tag.

Klicken Sie auf den Graphen, um einen Drilldown durchzuführen und den Bericht der Instanz-CPU im Lauf der Zeit anzuzeigen, dem Sie eine Aufschlüsselung der Instanzen entnehmen können.

- Verwenden Sie **Felder anzeigen/ausblenden** zum Hinzufügen oder Entfernen von Feldern: Zeitstempel, durchschnittliche CPU-Belegung, min. CPU-Belegung, max. CPU-Belegung.
- Verwenden Sie **Date Range** (Datumsbereich), um ein Datum oder einen Datumsbereich einzugeben und einen Drilldown in eine bestimmte Instanz-ID durchzuführen.
- Verwenden Sie **Extended Filters** (Erweiterte Filter), um alle Instanz-IDs oder eine bestimmte anzuzeigen.
- Klicken Sie auf **Zoom in** (Vergrößern), um den Bericht zur CPU-Auslastung zu öffnen.

Wenn die Instanz noch nicht für 30 Tage überwacht wurde, werden unvollständige Daten angezeigt.

Der Abschnitt **Memory Utilization (GB)** (Arbeitsspeicherauslastung (GB)) enthält Informationen über die Speicherauslastung. Für AWS-Benutzer stehen Arbeitsspeichermetriken nicht automatisch zur Verfügung und müssen pro Instanz über AWS hinzugefügt werden. AWS berechnet Ihnen Gebühren, wenn Sie Arbeitsspeichermetriken für EC2-Instanzen aktivieren.

Der Abschnitt **Memory Utilization (%)** (Arbeitsspeicherauslastung (%)) enthält den genutzten prozentualen Anteil des Arbeitsspeichers.

Der Abschnitt **Network Input Traffic** (Eingangs-Netzwerkdatenverkehr) zeigt eine Momentaufnahme über die Zeit des Netzwerkdatenverkehrs mit Durchschnitt und Maximum für die ausgewählte Instanz. Zeigen Sie auf die Zeilen, um das Datum und den maximalen Datenverkehr für diesen Zeitraum anzuzeigen. Klicken Sie auf **Zoom in** (Vergrößern), um den Bericht für Eingangs-Netzwerkdatenverkehr zu öffnen.

Der Abschnitt **Network Output Traffic** (Ausgangs-Netzwerkdatenverkehr) zeigt eine Momentaufnahme des Netzwerkdatenverkehrs für die ausgewählte Instanz an. Zeigen Sie auf die Zeilen, um das Datum und den maximalen Datenverkehr für diesen Zeitraum anzuzeigen. Klicken Sie auf **Zoom in** (Vergrößern), um den Bericht für Ausgangs-Netzwerkdatenverkehr zu öffnen.

### <a name="instance-metrics-explorer-report"></a>Metrik-Explorer-Bericht pro Instanz

Der Metrik-Explorer-Bericht pro Instanz zeigt cloudübergreifende Leistungsmetriken pro Instanz. Verwenden Sie den Bericht, um Instanzen anzuzeigen, die basierend auf Metrikschwellenwerten für CPU, Arbeitsspeicher und Netzwerkleistung zu stark oder zu gering ausgelastet sind.

So zeigen Sie die cloudübergreifende Leistung pro Instanz an

1. Wählen Sie unter **Date Range** (Datumsbereich) einen Datumsbereich aus, für den Sie die Leistung anzeigen möchten.
2. Wählen Sie unter **Tags** alle Tags aus, die Sie anzeigen möchten.
3. Wählen Sie unter **Filters** die Filter aus, die im Bericht angezeigt werden sollen.
4. Passen Sie unter **Extended Filters** (Erweiterte Filter) die Berichtsschwellenwerte für Folgendes an:
    - Durchschnittliche CPU-Auslastung
    - Maximale CPU-Auslastung
    - Durchschnittliche Arbeitsspeicherbelegung
    - Maximale Arbeitsspeicherbelegung
5. Klicken Sie unter **Extended Filters** (Erweiterte Filter) auf **Show** (Anzeigen), und wählen Sie dann den Typ der Instanzen aus, die angezeigt werden sollen.

So zeigen Sie Metriken für eine bestimmte Instanz im Lauf der Zeit an

- Wechseln Sie zum Metrik-Explorer-Bericht pro Instanz, und klicken Sie auf **+**, um Details anzuzeigen.

### <a name="rds-sizing-recommendations-report"></a>Bericht über RDS-Größenempfehlungen

Der Bericht über RDS-Größenempfehlungen stellt RDS-Größenempfehlungen zur Optimierung Ihrer Cloudnutzung RDS bereit. Er enthält eine Liste von nicht ausgelasteten Instanzen, die Kandidaten für eine Verkleinerung sind. Die Empfehlungen von Cloudyn basieren auf der Nutzung und den Leistungsdaten der letzten 30 Tage. Sie können Empfehlungen nach Kontoname, Region, Instanztyp und Status filtern.

### <a name="sizing-threshold-manager-report"></a>Sizing Threshold Manager-Bericht

Die integrierten Empfehlungen von Cloudyn werden mit einem komplexen Algorithmus berechnet, um genaue Empfehlungen für die Größen anzugeben. Sie können die Schwellenwerte für Verkleinerungsempfehlungen anpassen.

So passen Sie die Schwellenwerte für Größenempfehlungen manuell an

1. Passen Sie in Sizing Threshold Manager die folgenden Schwellenwerte nach Wunsch an:
    - Durchschnittliche CPU-Nutzung in %
    - Maximale CPU-Nutzung in %
    - Durchschnittliche Arbeitsspeichernutzung in %
    - Maximale Arbeitsspeichernutzung in %
3. Klicken Sie auf **Apply** (Übernehmen), um die Änderungen zu speichern.
4. Die Änderungen werden sofort auf alle Ihre Empfehlungen angewandt.

So stellen Sie die Standardschwellenwerte wieder her

- Klicken Sie in Sizing Threshold Manager auf **Restore Defaults** (Standards wiederherstellen).

### <a name="compute-instance-types-report"></a>Computeinstanztypen-Bericht

Verwenden Sie den Computeinstanztypen-Bericht für Folgendes:

- Anzeigen von Instanztypen nach Dienst, Familie, API-Name und Name
- Anzeigen von Details wie CPU, ECU, RAM und Netzwerk

Sie können mithilfe der **Suche** bestimmte Positionen suchen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über das Verwenden von Berichten, einschließlich des Anpassens oder Speicherns und Planens von Berichten finden Sie unter [Grundlagen zu Kostenberichten](understanding-cost-reports.md).
- Informationen zu den in Cloudyn enthaltenen Dashboards und zum Erstellen eigener benutzerdefinierter Dashboards finden Sie unter [Anzeigen der wichtigsten Kostenmetriken mit Dashboards](dashboards.md).
