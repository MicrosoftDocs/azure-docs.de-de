---
title: Analysieren der Azure-Kosten mit der Power BI-App
description: In diesem Artikel wird erläutert, wie Sie die Azure Cost Management-Power BI-App installieren und verwenden.
author: bandersmsft
ms.author: banders
ms.date: 1/29/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: d3439f5a86c445898d95191a32496b5f0c86be0b
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070156"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Analysieren der Kosten mit der Azure Cost Management-Power BI-App für Enterprise Agreements (EA)

In diesem Artikel wird erläutert, wie Sie die Azure Cost Management-Power BI-App installieren und verwenden. Die App unterstützt Sie bei der Analyse und Verwaltung Ihrer Azure-Kosten in Power BI. Mit der App können Sie Kosten und Nutzungstrends überwachen sowie Optionen zur Kostenoptimierung identifizieren, um Ihre Ausgaben zu reduzieren.

Die Azure Cost Management-Power BI-App unterstützt derzeit nur Kunden mit [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).

Die Anpassbarkeit ist für die App eingeschränkt. Wenn Sie die Standardfilter, -ansichten und -visualisierungen ändern und erweitern möchten, um sie an Ihre Anforderungen anzupassen, sollten Sie stattdessen den [Azure Cost Management-Connector in Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management) nutzen. Mit dem Azure Cost Management-Connector können Sie zusätzliche Daten aus anderen Quellen einfügen, um angepasste Berichte zu erstellen und so einen Gesamtüberblick über Ihre Geschäftskosten zu erhalten. Der Connector unterstützt auch Microsoft-Kundenvereinbarungen.

> [!NOTE]
> Das Herunterladen der PBIX-Datei wird von Power BI-Vorlagen-Apps nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

- Zum Installieren und Verwenden der App ist eine [Power BI Pro-Lizenz](/power-bi/service-self-service-signup-for-power-bi) erforderlich.
- Zum Herstellen einer Verbindung mit Daten müssen Sie ein [Unternehmensadministratorkonto](../manage/understand-ea-roles.md) verwenden. Die Rolle „Unternehmensadministrator (mit Leseberechtigung)“ wird unterstützt.

## <a name="installation-steps"></a>Installationsschritte

So installieren Sie die App:

1. Öffnen Sie die [Azure Cost Management-Power BI-App](https://aka.ms/costmgmt/ACMApp).
2. Wählen Sie auf der Power BI-AppSource-Seite die Option **Jetzt anfordern** aus.
3. Wählen Sie **Weiter** aus, um den Nutzungsbedingungen und der Datenschutzrichtlinie zuzustimmen.
4. Wählen Sie im Feld **Diese Power BI-App installieren?** die Option **Installieren** aus.
5. Erstellen Sie bei Bedarf einen Arbeitsbereich, und wählen Sie **Weiter** aus.
6. Nach Abschluss der Installation wird eine Benachrichtigung mit dem Hinweis angezeigt, dass Ihre neue App bereit ist.
7. Wählen Sie **Zu App wechseln** aus.
8. Wählen Sie unter **Erste Schritte mit Ihrer neuen App** unter **Ihre Daten verbinden** die Option **Verbinden** aus.  
  ![Erste Schritte mit Ihrer neuen App: Verbinden](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. Geben Sie im angezeigten Dialogfeld unter **BillingProfileIdOrEnrollmentNumber** Ihre EA-Registrierungsnummer ein. Geben Sie die Anzahl von Monaten ein, für die Daten abgerufen werden sollen. Übernehmen Sie den Standardwert **Registrierungsnummer** für **Bereich**, und wählen Sie dann **Weiter** aus.  
  ![Eingeben der EA-Registrierungsinformationen](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. Im nächsten Dialogfeld wird eine Verbindung mit Azure hergestellt, und die für Empfehlungen für reservierte Instanzen erforderlichen Daten werden abgerufen. *Lassen Sie die konfigurierten Standardwerte unverändert*, und wählen Sie **Anmelden** aus.  
  ![Screenshot des Dialogfelds „Connect to Azure Cost Management App“ (Verbindung mit Azure Cost Management-App herstellen) mit Standardwerten](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. Im letzten Installationsschritt wird eine Verbindung mit ihrer EA-Registrierung hergestellt und ein [Unternehmensadministratorkonto](../manage/understand-ea-roles.md) benötigt. Wählen Sie **Anmelden** aus, um sich bei Ihrer EA-Registrierung zu authentifizieren. In diesem Schritt wird auch eine Datenaktualisierungsaktion in Power BI gestartet.  
  ![Herstellen einer Verbindung mit der EA-Registrierung](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > Der Datenaktualisierungsvorgang kann einige Zeit in Anspruch nehmen. Die Dauer hängt von der angegebenen Anzahl von Monaten und der Menge der zu synchronisierenden Datenmenge ab.
12. Wählen Sie zum Überprüfen des Datenaktualisierungsstatus die Registerkarte **Datasets** im Arbeitsbereich aus. Sehen Sie sich die Angabe neben dem Zeitstempel „Aktualisiert“ an. Ist die Aktualisierung noch nicht abgeschlossen, gibt ein Indikator an, dass die Aktualisierung noch ausgeführt wird.  
  ![Aktualisieren von Daten](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

Wählen Sie nach Abschluss der Datenaktualisierung die Azure Cost Management-App aus, um die vorab erstellten Berichte anzuzeigen.

## <a name="reports-available-with-the-app"></a>Mit der App verfügbare Berichte

Die folgenden Berichte sind in der App verfügbar:

**Erste Schritte**: Enthält nützliche Links zur Dokumentation und Links zum Abgeben von Feedback.

**Kontoübersicht**: Der Bericht enthält eine monatliche Zusammenfassung der Informationen, u. a.:

- Gebühren für Guthaben
- Neue Einkäufe
- Azure Marketplace-Gebühren
- Überschreitungen und Gesamtgebühren

**Usage by Subscriptions and Resource Groups** (Nutzung nach Abonnements und Ressourcengruppen): Enthält eine Ansicht der Kosten im Zeitverlauf sowie Diagramme mit den Kosten nach Abonnement und Ressourcengruppe.

**Usage by Services** (Nutzung nach Diensten): Enthält eine Ansicht der Nutzung im Zeitverlauf nach Kategorie der Verbrauchseinheit (MeterCategory). Sie können Ihre Nutzungsdaten nachverfolgen und einen Drilldown für Anomalien ausführen, um Spitzen oder Abfälle in der Nutzung zu verstehen.

**Top 5 Usage drivers** (Die fünf wichtigsten Nutzungsfaktoren): Dieser Bericht enthält eine Kostenzusammenfassung, die nach den fünf wichtigsten MeterCategory-Werten gefiltert ist, sowie die entsprechenden Namen der Verbrauchseinheit (MeterName).

**Windows Server AHB Usage** (Windows Server-AHB-Nutzung): Dieser Bericht zeigt die Anzahl virtueller Computer, für die der Azure-Hybridvorteil aktiviert ist. Darüber hinaus enthält er die Anzahl der von den virtuellen Computern verwendeten Kerne/vCPUs.

![Vollständiger Bericht zu Azure-Hybridvorteilen](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

Der Bericht identifiziert außerdem virtuelle Windows-Computer, für die der Hybridvorteil **aktiviert** ist, die jedoch _weniger als_ acht vCPUs enthalten. Er veranschaulicht zudem, für welche virtuellen Computer der Hybridvorteil **nicht aktiviert** ist und die _mindestens_ acht vCPUs enthalten. Diese Informationen helfen Ihnen bei der vollständigen Nutzung Ihres Hybridvorteils. Wenden Sie den Vorteil auf Ihre teuersten virtuellen Computer an, um die möglichen Einsparungen zu maximieren.

![Azure-Hybridvorteile: Weniger als acht vCPUs, vCPUs nicht aktiviert](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

**RI Chargeback** (Verbrauchsbasierte Kostenzuteilung für reservierte Instanzen): Anhand dieses Berichts können Sie nachvollziehen, wo und in welcher Höhe der Vorteil für reservierte Instanzen (Reserved Instances, RIs) pro Region, Abonnement, Ressourcengruppe oder Ressource angewendet wird. Der Bericht nutzt Daten zur amortisierten Nutzung für die Ansicht.

Sie können einen Filter für _chargetype_ anwenden, um Daten zur RI-Unterauslastung anzuzeigen.

Weitere Informationen zu amortisierten Daten finden Sie unter [Abrufen von Reservierungskosten und Nutzung laut Enterprise Agreement](../reservations/understand-reserved-instance-usage-ea.md).

**RI Savings** (Einsparungen für reservierte Instanzen): Dieser Bericht enthält die durch Reservierungen erzielten Einsparungen für das Abonnement, die Ressourcengruppe und die Ressourcenebene. Er enthält folgende Informationen:

- Kosten mit Reservierung
- Geschätzte bedarfsgesteuerte Kosten, wenn die Reservierung nicht auf die Nutzung angewendet wurde
- Von der Reservierung erzielte Kosteneinsparungen

 Im Bericht werden alle überschüssigen Kosten für nicht ausgelastete Reservierungen von der Gesamtersparnis abgezogen. Diese überschüssigen Kosten würden ohne Reservierung nicht entstehen.

Die Daten zur amortisierten Nutzung können als Grundlage verwendet werden.

<a name="shared-recommendation"></a>
**VM RI Coverage (shared recommendation)** (Abdeckung reservierter VM-Instanzen (Empfehlung für gemeinsame Nutzung)): Der Bericht ist in die bedarfsgesteuerte VM-Nutzung und die Nutzung reservierter VM-Instanzen im ausgewählten Zeitraum aufgeteilt. Er enthält Empfehlungen für den Erwerb reservierter VM-Instanzen in einem gemeinsamen Bereich.

Wählen Sie zur Verwendung des Berichts den Drilldownfilter aus.

![Bericht zur Abdeckung reservierter VM-Instanzen: Auswählen des Drilldownfilters](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

Wählen Sie die Region aus, die Sie analysieren möchten. Wählen Sie anschließend die Instanzgrößenflexibilitäts-Gruppe und weitere Optionen aus.

Für jede Drilldownebene werden die folgenden Filter auf den Bericht angewendet:

- Bei den Abdeckungsdaten auf der rechten Seite handelt es sich um den Filter, der angibt, wie viel von der Nutzung zum Preis für das bedarfsorientierte Modell abgerechnet wird und wie viel durch die Reservierung abgedeckt ist.
- Empfehlungen sind ebenfalls gefiltert.

Die Tabelle mit Empfehlungen enthält Empfehlungen für den Reservierungserwerb auf der Grundlage der verwendeten VM-Größen.

Mithilfe der Werte _Normalized Size_ (Normalisierte Größe) und _Recommended Quantity Normalized_ (Normalisierte empfohlene Menge) können Sie den Kauf auf die kleinste Größe für eine Instanzgrößenflexibilitäts-Gruppe normalisieren. Die Informationen sind hilfreich, wenn Sie nur eine Reservierung für alle Größen in der Instanzgrößenflexibilitäts-Gruppe erwerben möchten.

![Empfehlungen für reservierte Instanzen](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

**VM RI Coverage (single recommendation)** (Abdeckung reservierter VM-Instanzen (Empfehlung für Einzelnutzung)): Der Bericht ist in die bedarfsgesteuerte VM-Nutzung und die Nutzung reservierter VM-Instanzen im ausgewählten Zeitraum aufgeteilt. Er enthält Empfehlungen für den Erwerb reservierter VM-Instanzen in einem Abonnementbereich.

Ausführliche Informationen zur Verwendung des Berichts finden Sie im Abschnitt zum Bericht [VM RI Coverage (shared recommendation)](#shared-recommendation) (Abdeckung reservierter VM-Instanzen (Empfehlung für gemeinsame Nutzung)).

**RI purchases** (Käufe reservierter Instanzen): Der Bericht zeigt die Käufe reservierter Instanzen im angegebenen Zeitraum.

**Preisblatt**: Der Bericht enthält eine detaillierte Liste der Preise für ein Abrechnungskonto oder eine EA-Registrierung.

## <a name="troubleshoot-problems"></a>Behandeln von Problemen

Sollten bei Ihnen Probleme mit der Power BI-App auftreten, helfen Ihnen unter Umständen die folgenden Informationen zur Problembehandlung weiter.

### <a name="error-processing-the-data-in-the-dataset"></a>Fehler beim Verarbeiten der Daten im Dataset

Sie erhalten möglicherweise eine Fehlermeldung wie die folgende:

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

Anstelle von `<TableName>` wird ein Tabellenname angezeigt.

#### <a name="cause"></a>Ursache

Der Standardwert `Enrollment Number` für **Bereich** wurde in der Verbindung mit Cost Management geändert.

#### <a name="solution"></a>Lösung

Stellen Sie erneut eine Verbindung mit Cost Management her, und legen Sie den Wert für **Bereich** auf `Enrollment Number` fest. Geben Sie nicht die Registrierungsnummer Ihrer Organisation, sondern `Enrollment Number` genau so ein, wie in der folgenden Abbildung gezeigt:

![Eingeben der EA-Registrierungsinformationen](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  

### <a name="budgetamount-error"></a>BudgetAmount-Fehler

Sie erhalten möglicherweise eine Fehlermeldung wie die folgende:

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>Ursache

Dieser Fehler tritt aufgrund eines Fehlers im Zusammenhang mit den zugrunde liegenden Metadaten auf. Das Problem: Im Azure-Portal ist unter **Kostenverwaltung > Budget** kein Budget verfügbar. Die Fehlerbehebung wird gerade für den Power BI Desktop- und den Power BI-Dienst bereitgestellt. 

#### <a name="solution"></a>Lösung

- Bis zur Behebung des Fehlers können Sie das Problem umgehen, indem Sie im Azure-Portal auf der Abrechnungskonto-/EA-Registrierungsebene ein Testbudget hinzufügen. Das Testbudget beseitigt die Blockierung der Power BI-Verbindung. Weitere Informationen zum Erstellen eines Budgets finden Sie unter [Tutorial: Erstellen und Verwalten von Azure-Budgets](tutorial-acm-create-budgets.md).


### <a name="invalid-credentials-for-azureblob-error"></a>Ungültige Anmeldeinformationen für AzureBlob

Sie erhalten möglicherweise eine Fehlermeldung wie die folgende:

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>Ursache

Dieser Fehler tritt auf, wenn Sie die Authentifizierungsmethode für die AutoFitComboMeter-Blobverbindung ändern.

#### <a name="solution"></a>Lösung

1. Stellen Sie eine Verbindung mit Ihren Daten her.
1. Lassen Sie nach der Eingabe Ihrer EA-Registrierung und der Anzahl von Monaten die Standardwerte **Anonym** (für die Authentifizierungsmethode) und **Keine** (für die Datenschutzebene) unverändert.  
  ![Screenshot des Dialogfelds „Connect to Azure Cost Management App“ (Verbindung mit Azure Cost Management-App herstellen) mit eingegebenen Werten für „Anonym“ und „Keine“](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png)  
1. Legen Sie auf der nächsten Seite **OAuth2** als Authentifizierungsmethode und **Keine** für die Datenschutzebene fest. Melden Sie sich anschließend an, um sich mit Ihrer Registrierung zu authentifizieren. Dadurch wird auch eine Power BI-Datenaktualisierung gestartet.


## <a name="data-reference"></a>Datenverweis

Nachfolgend sind die über die App verfügbaren Daten zusammengefasst. Die Tabelle enthält darüber hinaus Links zu APIs mit ausführlichen Informationen zu Datenfeldern und -werten.

| **Tabellenverweis** | **Beschreibung** |
| --- | --- |
| **AutoFitComboMeter** | In der App enthaltene Daten zum Normalisieren der RI-Empfehlung und -Nutzung auf die kleinste Größe in der Instanzfamiliengruppe |
| [**Saldozusammenfassung**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Zusammenfassung des Saldos für Enterprise Agreements |
| [**Budgets**](/rest/api/consumption/budgets/get#definitions) | Budgetdetails zum Anzeigen der tatsächlichen Kosten oder Nutzung für vorhandene Budgetziele |
| [**Preisblätter**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | Geltende Verbrauchseinheitenpreise für das angegebene Abrechnungsprofil oder die angegebene EA-Registrierung |
| [**Gebühren für reservierte Instanzen**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | Gebühren im Zusammenhang mit Ihren reservierten Instanzen in den letzten 24 Monaten |
| [**Empfehlungen für reservierte Instanzen (gemeinsame Nutzung)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | Empfehlungen für den Kauf reservierter Instanzen, basierend auf den Nutzungstrends für alle Ihre Abonnements für die letzten sieben Tage |
| [**Empfehlungen für reservierte Instanzen (Einzelnutzung)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | Empfehlungen für den Kauf reservierter Instanzen, basierend auf den Nutzungstrends für Ihr Einzelabonnement für die letzten sieben Tage |
| [**Nutzungsdetails zu reservierten Instanzen**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | Nutzungsdetails zu Ihren vorhandenen reservierten Instanzen im letzten Monat |
| [**Nutzungszusammenfassung für reservierte Instanzen**](/rest/api/consumption/reservationssummaries/list) | Tägliche Azure-Reservierungsnutzung in Prozent |
| [**Nutzungsdetails**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Eine Aufschlüsselung der verbrauchten Menge und der geschätzten Gebühren für das jeweilige Abrechnungsprofil in der EA-Registrierung |
| [**Nutzungsdetails (amortisiert)**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Eine Aufschlüsselung der verbrauchten Menge und der geschätzten amortisierten Gebühren für das jeweilige Abrechnungsprofil in der EA-Registrierung |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Konfigurieren von Datenaktualisierungen, zum Freigeben von Berichten sowie zu zusätzlichen Berichtsanpassungen finden Sie in den folgenden Artikeln:

- [Konfigurieren von geplanten Aktualisierungen](/power-bi/refresh-scheduled-refresh)
- [Freigeben von Power BI-Dashboards und -Berichten für Kollegen und andere Personen](/power-bi/service-share-dashboards)
- [Abonnieren von Berichten und Dashboards im Power BI-Dienst für sich selbst und andere](/power-bi/service-report-subscribe)
- [Herunterladen eines Berichts aus dem Power BI-Dienst in Power BI Desktop (Vorschau)](/power-bi/service-export-to-pbix)
- [Speichern eines Berichts im Power BI-Dienst und in Power BI Desktop](/power-bi/service-report-save)
- [Erstellen eines Berichts im Power BI-Dienst durch Importieren eines Datasets](/power-bi/service-report-create-new)