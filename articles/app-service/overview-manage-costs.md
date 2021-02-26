---
title: Planen der Kostenverwaltung für App Service
description: Hier erfahren Sie, wie Sie Kosten für Azure App Service mithilfe der Kostenanalyse im Azure-Portal planen und verwalten.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 3df08705859678525526f8fef198826f58249d8b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573363"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Planen und Verwalten von Kosten für Azure App Service

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

In diesem Artikel erfahren Sie, wie Sie die Kosten für Azure App Service planen und verwalten. Bevor Sie Ressourcen für den Dienst zur Kostenschätzung hinzufügen, verwenden Sie zunächst den Azure-Preisrechner, um die Kosten für App Service zu planen. Überprüfen Sie dann beim Hinzufügen von Azure-Ressourcen die voraussichtlichen Kosten. Nachdem Sie mit der Nutzung von App Service-Ressourcen begonnen haben, können Sie [Cost Management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)-Features verwenden, um Budgets festzulegen und Kosten zu überwachen. Sie können auch vorhergesagte Kosten überprüfen und Ausgabentrends ermitteln, um die Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind. Kosten für App Service stellen nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung dar. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für App Service erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die für Ihr Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern.

## <a name="relevant-costs-for-app-service"></a>Relevante Kosten für App Service

App Service wird in der Azure-Infrastruktur ausgeführt, durch die Kosten entstehen. Es ist wichtig zu verstehen, dass die zusätzliche Infrastruktur Kosten verursachen kann. Sie müssen diese Kosten verwalten, wenn Sie Änderungen an bereitgestellten Ressourcen vornehmen.

### <a name="costs-that-accrue-with-azure-app-service"></a>Durch Azure App Service entstehende Kosten

Abhängig davon, welches Feature Sie in App Service verwenden, werden möglicherweise die folgenden, Kosten verursachenden Ressourcen erstellt:

- **App Service-Plan**: Erforderlich zum Hosten einer App Service-App.
- **Dienstebene „Isoliert“** : Für eine App Service-Umgebung ist ein [virtuelles Netzwerk](../virtual-network/index.yml) erforderlich.
- **Sicherung**: Zum Erstellen von Sicherungen ist ein [Speicherkonto](../storage/index.yml) erforderlich.
- **Diagnoseprotokolle**: Sie können [Speicherkonto](../storage/index.yml) als Protokollierungsoption auswählen oder in [Azure Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) integrieren.
- **App Service-Zertifikate**: Zertifikate, die Sie in Azure erwerben, müssen in [Azure Key Vault](../key-vault/index.yml) verwaltet werden.

Weitere mit Kosten verbundene Ressourcen für App Service sind (Details finden Sie unter [App ServicePreise](https://azure.microsoft.com/pricing/details/app-service/)):

- [App Service-Domänen](manage-custom-dns-buy-domain.md): Ihrem Abonnement wird die Domänenregistrierung jährlich in Rechnung gestellt, wenn Sie die automatische Verlängerung aktivieren.
- [App Service-Zertifikate](configure-ssl-certificate.md#import-an-app-service-certificate): Eine einmalige Gebühr zum Zeitpunkt des Kaufs. Wenn Sie mehrere Unterdomänen schützen müssen, können Sie die Kosten senken, indem Sie anstelle mehrerer Standardzertifikate ein Platzhalterzertifikat kaufen.
- [IP-basierte Zertifikatbindungen](configure-ssl-bindings.md#create-binding): Die Bindung wird für ein Zertifikat auf App-Ebene konfiguriert. Für jede Bindung fallen Kosten an. Für die Dienstebene **Standard** und höher wird die erste IP-basierte Bindung nicht berechnet.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Nach dem Löschen von Ressourcen möglicherweise anfallende Kosten

Wenn Sie alle Apps in einem App Service-Plan löschen, verursacht der Plan weiterhin Kosten, basierend auf des für ihn konfigurierten Tarifs und der Anzahl von Instanzen. Um unerwünschte Kosten zu vermeiden, löschen Sie den Plan, oder skalieren Sie ihn auf den Tarif **Free** (Kostenlos) herunter.

Nachdem Sie Azure App Service-Ressourcen gelöscht haben, bleiben Ressourcen von verwandten Azure-Diensten möglicherweise bestehen. Hierfür fallen weiter Kosten an, bis Sie sie löschen. Beispiel:

- Das virtuelle Netzwerk, das Sie für einen App Service-Plan der Dienstebene **Isoliert** erstellt haben.
- Speicherkonten, die Sie zum Speichern von Sicherungen oder Diagnoseprotokollen erstellt haben.
- Key Vault, den Sie zum Speichern von App Service-Zertifikaten erstellt haben.
- Log Analytic-Namespaces, die Sie zum Versenden von Diagnoseprotokollen erstellt haben.
- [Instanz- oder Stempelreservierungen](#azure-reservations) für App Service, die noch nicht abgelaufen sind.

### <a name="using-monetary-credit-with-azure-app-service"></a>Verwenden von Guthaben mit Azure App Service

Sie können Azure App Service-Gebühren mit der Azure-Vorauszahlungsgutschrift (früher als Mindestverbrauch bezeichnet) bezahlen. Allerdings können Sie mit der Azure-Vorauszahlungsgutschrift keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus dem Azure Marketplace, bezahlen.

## <a name="estimate-costs"></a>Schätzen der Kosten

Eine einfache Möglichkeit zum Abschätzen und Optimieren Ihrer App Service-Kosten im Vorfeld ist die Verwendung des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/).

Um den Preisrechner zu verwenden, klicken Sie auf der Registerkarte **Produkte** auf **App Service**. Scrollen Sie dann nach unten, um mit dem Rechner zu arbeiten. Der folgende Screenshot zeigt nur ein Beispiel und gibt keine aktuellen Preise wider.

![Beispiel für die geschätzten Kosten im Azure-Preisrechner](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Überprüfen von geschätzten Kosten im Azure-Portal

Wenn Sie eine App Service-App oder einen App Service-Plan erstellen, werden Ihnen die geschätzten Kosten angezeigt.

So erstellen Sie eine App und zeigen den geschätzten Preis an

1. Scrollen Sie auf der Seite „Erstellen“ nach unten bis zu **App Service-Plan**, und klicken Sie auf **Neu erstellen**.
1. Geben Sie einen Namen an, und klicken Sie auf **OK**.
1. Klicken Sie als Nächstes neben **SKU und Größe** auf **Größe ändern**.
1. Überprüfen Sie den in der Zusammenfassung angezeigten geschätzten Preis. Der folgende Screenshot zeigt nur ein Beispiel und gibt keine aktuellen Preise wider.

    ![Überprüfen Sie die geschätzten Kosten für jeden der Tarife im Portal.](media/overview-manage-costs/pricing-estimates.png)

Wenn Ihr Azure-Abonnement über ein Ausgabenlimit verfügt, wird von Azure verhindert, dass Ihre Ausgaben den Guthabenbetrag übersteigen. Beim Erstellen und Nutzen von Azure-Ressourcen wird Ihr Guthaben verwendet. Wenn Sie Ihr Guthabenlimit erreicht haben, werden die von Ihnen bereitgestellten Ressourcen für den Rest des Abrechnungszeitraums deaktiviert. Sie können Ihr Guthabenlimit nicht ändern, aber Sie können es entfernen. Weitere Informationen zu Ausgabenlimits finden Sie unter [Azure-Ausgabenlimit](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="optimize-costs"></a>Optimieren von Kosten

Auf einer grundlegenden Ebene werden App Service-Apps über den App Service-Plan abgerechnet, in dem sie gehostet werden. Die Kosten für Ihre App Service-Bereitstellung hängen von ein paar Hauptfaktoren ab:

- **Tarif**: Auch als SKU des App Service-Plans bezeichnet. Höhere Dienstebenen (Tarife) bieten mehr CPU-Kerne, Arbeitsspeicher, Speicher, Features oder Kombinationen davon.
- **Instanzanzahl**: Dedizierte Dienstebenen (Basic und höher) lassen sich horizontal aufskalieren, und jede horizontal aufskalierte Instanz verursacht Kosten.
- **Stempelgebühr**: Im Tarif „Isoliert“ wird eine Pauschalgebühr auf Ihre App Service-Umgebung angewendet, unabhängig davon, wie viele Apps oder Workerinstanzen gehostet werden.

Ein App Service-Plan kann mehr als eine App hosten. Abhängig von Ihrer Bereitstellung könnten Sie Kosten sparen, indem Sie mehr Apps in einem App Service Plan hosten (d. h. Ihre Apps in weniger App Service Plänen hosten).

Details hierzu finden Sie unter [App Service-Plan – Übersicht](overview-hosting-plans.md).

### <a name="non-production-workloads"></a>Nichtproduktionsworkloads

Um App Service oder Ihre Lösung zu testen und dabei nur niedrige oder minimale Kosten zu erzeugen, können Sie damit beginnen, die beiden Tarife auf Einsteigerniveau zu verwenden, **Free** und **Shared**, die auf freigegebenen Instanzen gehostet werden. Um Ihre App auf dedizierten Instanzen mit besserer Leistung zu testen, können Sie ein Upgrade auf den Tarif **Basic** vornehmen, der sowohl Windows- als auch Linux-Apps unterstützt. 

> [!NOTE]
> **Preise für Azure Dev/Test**: Zum Testen von Vorproduktionsworkloads, die höhere Dienstebenen erfordern (alle Ebenen außer **Isoliert**) können Visual Studio-Abonnenten auch die [Azure Dev/Test-Preise](https://azure.microsoft.com/pricing/dev-test/) nutzen, die beträchtliche Rabatte bieten.
>
> Die Tarife **Free** und **Shared** als auch die Azure Dev/Test-Preisrabatte beinhalten keine finanziell abgesicherte SLA.

### <a name="production-workloads"></a>Produktionsworkloads

Für Produktionsworkloads wird der dedizierte Tarif **Standard** oder höher empfohlen. Während der Preis für höhere Tarife steigt, erhalten Sie auch mehr Arbeitsspeicher und Speicher und eine leistungsfähigere Hardware, sodass Sie eine höhere App-Dichte pro Compute-Instanz erreichen. Dies schlägt sich wiederum in einer niedrigeren Anzahl von Instanzen für dieselbe Anzahl von Apps und somit in niedrigeren Kosten nieder. Tatsächlich ist **Premium V3** (der höchste Nicht-**Isoliert**-Tarif) die kostengünstigste Methode, um Ihre App im großen Stil zu bedienen. Um die Einsparungen noch zu erhöhen, können Sie umfassende Rabatte auf [Premium V3-Reservierungen](#azure-reservations) erhalten.

> [!NOTE]
> **Premium V3** unterstützt sowohl Windows- als auch Linux-Container. 

Nachdem Sie den gewünschten Tarif ausgewählt haben, sollten Sie die im Leerlauf befindlichen Instanzen minimieren. In einer Bereitstellung für horizontales Skalieren können Sie durch unausgelastete Compute-Instanzen Geld verschwenden. Sie sollten die [automatische Skalierung konfigurieren](../azure-monitor/platform/autoscale-get-started.md), die im Tarif **Standard** und höher verfügbar ist. Indem Sie Zeitpläne für horizontales Skalieren sowie metrikbasierte Regeln für horizontales Skalieren erstellen, bezahlen Sie jederzeit nur für die tatsächlich benötigten Instanzen.

### <a name="azure-reservations"></a>Azure-Reservierungen

Wenn Sie beabsichtigen, eine bekannte Mindestanzahl von Compute-Instanzen für ein Jahr oder länger zu verwenden, sollten Sie den Tarif **Premium V3** nutzen und so die Instanzkosten drastisch senken, indem Sie diese Instanzen in Schritten von 1 oder 3 Jahren reservieren. Die monatlichen Kosteneinsparungen können bis zu 55 % pro Instanz betragen. Zwei Arten von Reservierungen sind möglich:

- **Windows (oder plattformunabhängig)** : Kann auf Windows- oder Linux-Instanzen in Ihrem Abonnement angewendet werden.
- **Linux-spezifisch**: Gilt nur für Linux-Instanzen in Ihrem Abonnement.

Die Preise für reservierte Instanzen gelten für die anwendbaren Instanzen in Ihrem Abonnement bis zu der Anzahl der Instanzen, die Sie reservieren. Die reservierten Instanzen sind Gegenstand der Abrechnung und nicht an bestimmte Compute-Instanzen gebunden. Wenn Sie zu einem beliebigen Zeitpunkt während des Reservierungszeitraums weniger Instanzen als reserviert ausführen, bezahlen Sie weiterhin für die reservierten Instanzen. Wenn Sie zu einem beliebigen Zeitpunkt während des Reservierungszeitraums mehr Instanzen als reserviert ausführen, bezahlen Sie die normalen, für die zusätzlichen Instanzen anfallenden Kosten.

Der Tarif **Isoliert** (App Service-Umgebung) unterstützt auch 1-Jahres- und 3-Jahres-Reservierungen zu reduzierten Preisen. Weitere Informationen finden Sie unter [Anwendung von Reservierungsrabatten auf Azure App Service](../cost-management-billing/reservations/reservation-discount-app-service.md).

## <a name="monitor-costs"></a>Überwachen der Kosten

Wenn Sie Azure-Ressourcen mit App Service verwenden, fallen Kosten an. Die Kosten pro Azure-Ressourcennutzungseinheit variieren nach dem Zeitintervall (Sekunden, Minuten, Stunden und Tage). Sobald mit der App Service-Nutzung begonnen wird, fallen Kosten an, und Sie können die Kosten in der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) sehen.

Bei Verwendung der Kostenanalyse können Sie App Service-Kosten in Diagrammen und Tabellen für unterschiedliche Zeitintervalle anzeigen. Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Sie können Kosten auch im Vergleich mit Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln. Außerdem können Sie erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten werden.
    
So zeigen Sie App Service-Kosten in der Kostenanalyse an

1. Melden Sie sich beim Azure-Portal an.
2. Öffnen Sie den Bereich im Azure-Portal, und wählen Sie **Kostenanalyse** im Menü aus. Navigieren Sie beispielsweise zu **Abonnements**, und wählen Sie dann ein Abonnement in der Liste und **Kostenanalyse** im Menü aus. Wählen Sie **Bereich** aus, um zu einem anderen Bereich der Kostenanalyse zu wechseln.
3. Standardmäßig werden die Kosten für Dienste im ersten Ringdiagramm angezeigt. Wählen Sie im Diagramm den mit „App Service“ bezeichneten Bereich aus.

Die tatsächlichen monatlichen Kosten werden beim ersten Öffnen der Kostenanalyse angezeigt. Im folgenden Beispiel werden alle monatlichen Nutzungskosten angezeigt.

![Beispiel der akkumulierten Kosten für ein Abonnement](media/overview-manage-costs/all-costs.png)

Wählen Sie zum Eingrenzen der Kosten auf einen Dienst, z. B. App Service, die Option **Filter hinzufügen** und dann **Dienstname** aus. Wählen Sie dann **App Service** aus.

Im folgenden Beispiel werden nur die Kosten für App Service angezeigt.

![Beispiel der akkumulierten Kosten für ServiceName](media/overview-manage-costs/service-specific-costs.png)

Im obigen Beispiel sind die aktuellen Kosten für den Dienst angegeben. Die Kosten nach Azure-Regionen (Standorten) und App Service-Kosten nach Ressourcengruppe werden ebenfalls angezeigt. Von hier aus können Sie die Kosten selbst überprüfen.

## <a name="create-budgets"></a>Erstellen von Budgets

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Sie können [Budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den beim Erstellen eines Budgets verfügbaren Filteroptionen finden Sie unter [Gruppen- und Filteroptionen](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen weitere Datenanalysen für Kosten ausführen müssen. Beispielsweise kann ein Finanzteam die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Preisgestaltung für Azure Storage. Informationen dazu finden Sie unter [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/).
- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->