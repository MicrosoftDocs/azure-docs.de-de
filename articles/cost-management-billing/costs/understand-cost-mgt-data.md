---
title: Grundlegendes zu Azure Cost Management-Daten
description: In diesem Artikel erfahren Sie mehr über die Daten, die in Azure Cost Management enthalten sind, und wie häufig diese verarbeitet, gesammelt, angezeigt und geschlossen werden.
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2
ms.openlocfilehash: 97ae2ba26818bbc306da71af814d9b4f95858b6a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032574"
---
# <a name="understand-cost-management-data"></a>Grundlegendes zu Cost Management-Daten

Dieser Artikel soll Ihnen helfen, die in Azure Cost Management enthaltenen Azure-Kosten und -Nutzungsdaten besser zu verstehen. Zudem wird darin erläutert, wie oft Daten verarbeitet, gesammelt, angezeigt und geschlossen werden. Die Nutzung von Azure wird Ihnen monatlich in Rechnung gestellt. Bei Abrechnungszeiträumen/Abrechnungszyklen handelt es sich um monatliche Zeiträume. Die Start- und Enddaten von Zyklen variieren jedoch je nach Abonnementtyp. Wie oft Cost Management Nutzungsdaten erhält, hängt von verschiedenen Faktoren ab. Zu diesen Faktoren gehört unter anderem, wie lange die Verarbeitung der Daten dauert und wie häufig Azure-Dienste die Nutzung an das Abrechnungssystem weitergeben.

Cost Management umfasst alle Nutzungen und Käufe, einschließlich Reservierungen und Angebote von Drittanbietern für Enterprise Agreement-Konten (EA). Konten der Microsoft-Kundenvereinbarung und einzelne Abonnements in Tarifen mit nutzungsbasierter Bezahlung umfassen nur die Nutzung von Azure- und Marketplace-Diensten. Support- und andere Kosten sind nicht enthalten. Kosten werden so lange geschätzt, bis eine Rechnung generiert wurde, und sie werden bei Guthaben nicht berücksichtigt.

Bei einem neuen Abonnement können Cost Management-Features nicht sofort genutzt werden. Es kann bis zu 48 Stunden dauern, bis Sie alle Cost Management-Features verwenden können.

## <a name="supported-microsoft-azure-offers"></a>Unterstützte Microsoft Azure-Angebote

Die folgenden Informationen zeigen die derzeit unterstützten [Microsoft Azure-Angebote](https://azure.microsoft.com/support/legal/offer-details/) im Azure Cost Management. Ein Azure-Angebot ist der Typ von Azure-Abonnement, das Sie besitzen. Die Daten sind ab dem in **Daten verfügbar ab** angegebenen Datum in Cost Management verfügbar. Wenn Angebote in einem Abonnement geändert werden, sind die Kosten vor dem Datum der Angebotsänderung nicht verfügbar.

| **Kategorie**  | **Angebotsname** | **Kontingent-ID** | **Angebotsnummer** | **Daten verfügbar ab** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Mai 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Mai 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Microsoft Azure Enterprise | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Mai 2014<sup>1</sup> |
| **Microsoft-Kundenvereinbarung** | Microsoft Azure-Plan | EnterpriseAgreement_2014-09-01 | – | März 2019<sup>3</sup> |
| **Microsoft-Kundenvereinbarung** | Microsoft Azure-Plan für Dev/Test | MSDNDevTest_2014-09-01 | – | März 2019<sup>3</sup> |
| **Von Partnern unterstützte Microsoft-Kundenvereinbarung** | Microsoft Azure-Plan | CSP_2015-05-01, CSP_MG_2017-12-01 und CSPDEVTEST_2018-05-01<br><br>Die Kontingent-ID wird für Abonnements im Rahmen von Microsoft-Kundenvereinbarungen und für ältere CSP-Abonnements wiederverwendet. Derzeit werden nur Abonnements im Rahmen von Microsoft-Kundenvereinbarungen unterstützt. | – | Oktober 2019 |
| **Microsoft Developer Network (MSDN)** | MSDN Platforms<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2\. Oktober 2018<sup>2</sup> |
| **Nutzungsbasierte Bezahlung** | Nutzungsbasierte Bezahlung                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2\. Oktober 2018<sup>2</sup> |
| **Nutzungsbasierte Bezahlung** | Dev/Test Pay-As-You-Go         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2\. Oktober 2018<sup>2</sup> |
| **Nutzungsbasierte Bezahlung** | Microsoft Partner Network      | MPN_2014-09-01 | MS-AZR-0025P | 2\. Oktober 2018<sup>2</sup> |
| **Nutzungsbasierte Bezahlung** | Kostenlose Testversion<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2\. Oktober 2018<sup>2</sup> |
| **Nutzungsbasierte Bezahlung** | Azure in Open<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2\. Oktober 2018<sup>2</sup> |
| **Nutzungsbasierte Bezahlung** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2\. Oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise – MPN<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2\. Oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Professional<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2\. Oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Test Professional<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2\. Oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2\. Oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise: BizSpark<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2\. Oktober 2018<sup>2</sup> |

_<sup>**1**</sup> Daten vor Mai 2014 finden Sie im [Azure Enterprise Portal](https://ea.azure.com)._

_<sup>**2**</sup> Daten vor dem 2. Oktober 2018 finden Sie im [Azure-Kontocenter](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Microsoft-Kundenvereinbarungen wurden ab März 2019 abgeschlossen und weisen keine Verlaufsdaten vor diesem Zeitpunkt auf._

_<sup>**4**</sup> Verlaufsdaten für Abonnements, die auf Guthaben oder Vorauszahlung basieren, stimmen möglicherweise nicht mit Ihrer Rechnung überein. Siehe [Verlaufsdaten stimmen möglicherweise nicht mit Ihrer Rechnung überein](#historical-data-might-not-match-invoice) weiter unten._

Folgende Angebote werden noch nicht unterstützt:

| Category  | **Angebotsname** | **Kontingent-ID** | **Angebotsnummer** |
| --- | --- | --- | --- |
| **Azure Deutschland** | Azure Deutschland – nutzungsbasierte Bezahlung | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government – nutzungsbasierte Bezahlung | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Deutschland in CSP für die Microsoft Cloud Deutschland   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Nutzungsbasierte Bezahlung**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Nutzungsbasierte Bezahlung** | Azure for Students<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Nutzungsbasierte Bezahlung**                 | Microsoft Azure Sponsorship | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Supportpläne** | Standard-Support                    | Default_2014-09-01 | MS-AZR-0041P |
| **Supportpläne** | Professional Direct-Support         | Default_2014-09-01 | MS-AZR-0042P |
| **Supportpläne** | Developer Support                   | Default_2014-09-01 | MS-AZR-0043P |
| **Supportpläne** | Supportplan Deutschland                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Supportpläne** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Supportpläne** | Azure Government Pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Supportpläne** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

### <a name="free-trial-to-pay-as-you-go-upgrade"></a>Upgrade einer kostenlosen Testversion auf nutzungsbasierte Bezahlung

Informationen zur Verfügbarkeit der kostenlosen Dienste nach einem Upgrade auf die nutzungsbasierte Bezahlung finden Sie in den [FAQ zum kostenlosen Azure-Konto](https://azure.microsoft.com/free/free-account-faq/).

### <a name="determine-your-offer-type"></a>Bestimmen des Angebotstyps

Wenn Sie keine Daten für ein Abonnement sehen und feststellen möchten, ob Ihr Abonnement zu den unterstützten Angeboten gehört, können Sie überprüfen, ob Ihr Abonnement unterstützt wird. Um zu überprüfen, ob ein Azure-Abonnement unterstützt wird, melden Sie sich beim Azure-Portal an. Wählen Sie anschließend **Alle Dienste** im linken Menübereich aus. Wählen Sie in der Liste der Dienste **Abonnements** aus. Klicken Sie im Menü der Abonnementliste auf das Abonnement, das Sie überprüfen möchten. Ihr Abonnement wird auf der Registerkarte „Übersicht“ angezeigt und Sie können das **Angebot** und **Angebots-ID** sehen. Die folgende Abbildung zeigt ein Beispiel.

![Beispiel für die Registerkarte „Abonnementübersicht“ mit Anzeige von Angebot und Angebots-ID](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>In Cost Management enthaltene Kosten

Die folgenden Tabellen zeigen Daten, die in Cost Management enthalten sind oder nicht. Alle Kosten werden so lange geschätzt, bis eine Rechnung generiert wurde. Die angezeigten Kosten beinhalten keine kostenlosen und Prepaid-Guthaben.

| **Enthalten** | **Nicht enthalten** |
| --- | --- |
| Nutzung durch Azure-Dienste<sup>5</sup>        | Supportgebühren – Weitere Informationen finden Sie unter [Erläuterung der Rechnungsbedingungen](../understand/understand-invoice.md). |
| Nutzung von Marketplace-Angeboten<sup>6</sup> | Steuern – Weitere Informationen finden Sie unter [Erläuterung der Rechnungsbedingungen](../understand/understand-invoice.md). |
| Marketplace-Käufe<sup>6</sup>      | Gutschriften – Weitere Informationen finden Sie unter [Erläuterung der Rechnungsbedingungen](../understand/understand-invoice.md). |
| Reservierungskäufe<sup>7</sup>      |  |
| Amortisierung von Reservierungskäufen<sup>7</sup>      |  |

_<sup>**5**</sup> Die Nutzung des Azure-Diensts basiert auf Reservierungen und ausgehandelten Preisen._

_<sup>**6**</sup> Marketplace-Käufe sind für MSDN- und Visual Studio-Angebote derzeit nicht verfügbar._

_<sup>**7**</sup> Reservierungskäufe sind zurzeit nur für EA-Konten (Enterprise Agreement) und Konten mit Microsoft-Kundenvereinbarung verfügbar._

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Verwendung von Tags in Kosten- und Nutzungsdaten

Azure Cost Management empfängt Tags mit jedem Nutzungsdatensatz, der von den einzelnen Diensten übermittelt wird. Für diese Tags gelten folgende Einschränkungen:

- Tags werden nicht implizit von der übergeordneten Ressourcengruppe geerbt und müssen direkt auf Ressourcen angewendet werden.
- Ressourcentags werden nur für in Ressourcengruppen bereitgestellte Ressourcen unterstützt.
- Es ist möglich, dass einige bereitgestellte Ressourcen keine Tags unterstützen oder keine Tags in Nutzungsdaten einschließen.
- Ressourcentags werden nur in Nutzungsdaten eingeschlossen, während das Tag angewendet ist. Tags werden nicht auf historische Daten angewendet.
- Ressourcentags stehen in Cost Management erst zur Verfügung, nachdem die Daten aktualisiert wurden.
- Ressourcentags sind nur in Cost Management verfügbar, wenn die Ressource aktiv ist/ausgeführt wird und Nutzungsdatensätze generiert (also beispielsweise nicht, wenn die Zuordnung eines virtuellen Computers aufgehoben wurde).
- Für die Tagverwaltung ist Zugriff vom Typ „Mitwirkender“ auf die einzelnen Ressourcen erforderlich.
- Für die Verwaltung von Tagrichtlinien ist Zugriff vom Typ „Besitzer“ oder „Mitwirkender an Richtlinien“ auf eine Verwaltungsgruppe, ein Abonnement oder eine Ressourcengruppe erforderlich.
    
Sollte in Cost Management ein bestimmtes Tag nicht angezeigt werden, überprüfen Sie Folgendes:

- Wurde das Tag direkt auf die Ressource angewendet?
- Wurde das Tag vor mehr als 24 Stunden angewendet?
- Unterstützt der Ressourcentyp Tags? Von den folgenden Ressourcentypen werden keine Tags in Nutzungsdaten unterstützt (Stand: 1. Dezember 2019). Die vollständige Liste der unterstützten Tags finden Sie unter [Tagunterstützung für Azure-Ressourcen](../../azure-resource-manager/management/tag-support.md).
    - Azure Active Directory B2C-Verzeichnisse
    - Azure Bastion
    - Azure Firewalls
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Load Balancer
    - Network Watcher
    - Notification Hubs
    - Service Bus
    - Time Series Insights
    
Im Anschluss finden Sie einige Tipps zur Verwendung von Tags:

- Planen Sie voraus, und definieren Sie eine Taggingstrategie, die es Ihnen ermöglicht, Kosten nach Organisation, Anwendung, Umgebung usw. aufzuschlüsseln.
- Verwenden Sie Azure Policy, um Ressourcengruppentags für einzelne Ressourcen zu kopieren und Ihre Taggingstrategie umzusetzen.
- Verwenden Sie die Tags-API zusammen mit „Query“ oder „UsageDetails“, um alle Kosten auf der Grundlage der aktuellen Tags zu erhalten.


## <a name="cost-and-usage-data-updates-and-retention"></a>Aktualisierung und Aufbewahrung von Kosten- und Nutzungsdaten

Kosten- und Nutzungsdaten sind in Kostenverwaltung + Abrechnung im Azure-Portal und in den unterstützenden APIs innerhalb von 8–24 Stunden verfügbar. Beachten Sie beim Überprüfen der Kosten folgende Punkte:

- Jeder Azure-Dienst (z. B. Storage, Compute und SQL) gibt die Nutzung in unterschiedlichen Intervallen aus. Es kann sein, dass Daten für einige Dienste früher angezeigt werden als für andere.
- Die geschätzten Gebühren für den aktuellen Abrechnungszeitraum werden sechsmal pro Tag aktualisiert.
- Die geschätzten Gebühren für den aktuellen Abrechnungszeitraum können sich mit zunehmender Nutzung ändern.
- Jede Aktualisierung ist kumulativ und enthält alle Einzelposten und Informationen der vorherigen Aktualisierung.
- Azure finalisiert oder _schließt_ den aktuellen Abrechnungszeitraum bis zu 72 Stunden (drei Kalendertage) nach Ablauf des Abrechnungszeitraums.

Die folgenden Beispiele veranschaulichen, wie Abrechnungszeiträume enden können:

* Abonnements mit Enterprise Agreement (EA) – Wenn der Abrechnungsmonat am 31. März endet, werden die geschätzten Gebühren bis zu 72 Stunden später aktualisiert. In diesem Beispiel bis Mitternacht am (UTC) 4. April.
* Abonnements mit nutzungsbasierter Bezahlung – Wenn der Abrechnungsmonat am 15. Mai endet, können die geschätzten Gebühren bis zu 72 Stunden später aktualisiert werden. In diesem Beispiel bis Mitternacht am (UTC) 19. April.

Wenn die Kosten- und Nutzungsdaten in Kostenverwaltung + Abrechnung verfügbar sind, werden sie mindestens 7 Jahre lang aufbewahrt.

### <a name="rerated-data"></a>Neu bewertete Daten

Unabhängig davon, ob Sie die Cost Management-APIs, Power BI oder das Azure-Portal zum Abrufen von Daten verwenden, ist zu erwarten, dass die Gebühren des aktuellen Abrechnungszeitraums neu bewertet werden und sich somit ändern, bis die Rechnung geschlossen ist.

## <a name="cost-rounding"></a>Runden von Kosten

Die in Cost Management angezeigten Kosten sind gerundet. Von der Abfrage-API zurückgegebene Kosten sind nicht gerundet. Beispiel:

- Kostenanalyse im Azure-Portal: Gebühren werden gemäß Standardrundungsregeln gerundet. Werte über 0,5 werden also aufgerundet, darunter werden die Kosten abgerundet. Eine Rundung wird nur vorgenommen, wenn Werte angezeigt werden. Bei der Datenverarbeitung und -aggregation erfolgt keine Rundung. Kosten werden von der Kostenanalyse beispielsweise wie folgt aggregiert:
  -    Gebühr 1: 0,004 USD
  - Gebühr 2: 0,004 USD
  -    Aggregierte Gebühr: 0,004 + 0,004 = 0,008. Angezeigte Gebühr: 0,01 USD.
- Abfrage-API: Gebühren werden mit acht Dezimalstellen angezeigt und nicht gerundet.

## <a name="historical-data-might-not-match-invoice"></a>Verlaufsdaten stimmen möglicherweise nicht mit Rechnung überein

Verlaufsdaten für Angebote, die auf Guthaben oder Vorauszahlung basieren, stimmen möglicherweise nicht mit Ihrer Rechnung überein. Bei einigen Azure-Angeboten der Kategorien „Nutzungsbasierte Bezahlung“, „MSDN“ und „Visual Studio“ können Azure-Guthaben und Vorauszahlungen auf die Rechnung angewendet werden. Die in Cost Management angezeigten Verlaufsdaten basieren allerdings nur auf den geschätzten Nutzungsgebühren. Cost Management-Verlaufsdaten enthalten keine Zahlungen und Guthaben. Daher stimmen die Verlaufsdaten, die für die folgenden Angebote angezeigt werden, möglicherweise nicht genau mit Ihrer Rechnung überein.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Kostenlose Testversion (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Weitere Informationen

- Falls Sie den ersten Schnellstart für Cost Management noch nicht abgeschlossen haben, lesen Sie ihn unter [Erste Schritte in der Analyse von Kosten](./quick-acm-cost-analysis.md).