---
title: Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung
description: Dieser Artikel enthält grundlegende Informationen zu Ihrer Azure Cosmos DB-Rechnung sowie einige Beispiele.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: 3ac90b79053e59047dbe64598688e77b9df059d1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358710"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Grundlegendes zu Ihrer Azure Cosmos DB-Rechnung
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Als vollständig verwalteter cloudnativer Datenbankdienst vereinfacht Azure Cosmos DB die Abrechnung, da nur die Datenbankvorgänge und die Speichernutzung berechnet werden. Im Vergleich zu lokalen oder über IaaS gehostete Alternativen gibt es keine zusätzlichen Lizenzgebühren und keine Kosten für Hardware, Hilfsprogramme oder Gebäude. Wenn Sie dazu noch die Funktionalität zum Einbeziehen mehrerer Regionen berücksichtigen, sorgt der Datenbankdienst gegenüber vorhandenen lokalen oder über IaaS gehosteten Lösungen für eine deutliche Reduzierung der Kosten.

- **Datenbankvorgänge**: Die Art und Weise, wie Ihre Datenbankvorgänge abgerechnet werden, hängt vom Typ des Azure Cosmos-Kontos ab, das Sie verwenden.

  - **Bereitgestellter Durchsatz**: Die Abrechnung erfolgt auf Stundenbasis für den für die jeweilige Stunde bereitgestellten maximalen Durchsatz in Inkrementen von 100 RU/s.
  - **Serverlos**: Ihnen wird die Gesamtmenge der durch Ihre Datenbankvorgänge pro Stunde verbrauchten Anforderungseinheiten in Rechnung gestellt.

- **Storage**: Für die Gesamtmenge des Speichers (in GB), der für Daten und Indizes für eine angegebene Stunde genutzt wird, wird eine Pauschale berechnet.

Aktuelle Preisinformationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/cosmos-db/).

Dieser Artikel enthält einige Beispiele, damit die einzelnen Positionen auf der monatlichen Rechnung besser verständlich sind. Die in den Beispielen genannten Zahlen können von Ihren Zahlen abweichen, wenn Sie für Ihre Azure Cosmos-Container eine andere Menge als bereitgestellten Umsatz ausgewählt haben, die Container sich über mehrere Regionen erstrecken oder ein anderer Ausführungszeitraum abgerechnet wird (z. B. mehr als ein Monat). Die Abrechnung basiert in den Beispielen dieses Artikels jeweils auf den Preisinformationen, die auf der Seite [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/) angegeben sind.

> [!NOTE]
> Die Abrechnung erfolgt für einen beliebigen Teil einer Stunde, nicht die Dauer von 60 Minuten. Alle in diesem Dokument gezeigten Beispiele basieren auf dem Preis für ein Azure Cosmos-Konto, das in einer Region in den USA bereitgestellt wird, bei der es sich nicht um eine Government-Region handelt. Die Preise und die Berechnung variieren je nach verwendeter Region. Aktuelle Preisinformationen finden Sie auf der Seite [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="billing-examples"></a>Abrechnungsbeispiele

### <a name="billing-example---provisioned-throughput-on-a-container-full-month"></a>Abrechnungsbeispiel: bereitgestellter Durchsatz für einen Container (ganzer Monat)

* Angenommen, Sie konfigurieren einen Durchsatz von 1.000 RUs/Sek. für einen Container, und zwar für 24 Stunden x 30 Tage im Monat = 720 Stunden.  

* 1\.000 RUs/Sek. entsprechen 10 Grundeinheiten von je 100 RUs/Sek. pro Stunde (d. h. 1.000/100 = 10), und zwar für jede Stunde, die der Container vorhanden ist. 

* Wenn man diese 10 Einheiten pro Stunde mit den Kosten der Grundeinheit von 100 RUs/Sek. = 0,008 US-Dollar multipliziert, ergeben sich Kosten von 0,08 US-Dollar pro Stunde. 

* Dann multipliziert man 0,08 US-Dollar mit der Anzahl der Stunden im Monat: 0,08 mal 24 Stunden mal 30 Tage = 57,60 US-Dollar für den Monat.  

* Auf der monatlichen Gesamtrechnung sind 7.200 Einheiten (à 100 RUs/Sek.) ausgewiesen, die 57,60 US-Dollar kosten.

### <a name="billing-example---provisioned-throughput-on-a-container-partial-month"></a>Abrechnungsbeispiel: bereitgestellter Durchsatz für einen Container (anteiliger Monat)

* Angenommen, Sie erstellen einen Container mit einem bereitgestellten Durchsatz von 2.500 RU/Sek. Der Container ist im betreffenden Monat 24 Stunden lang in Betrieb. (Er wird beispielsweise 24 Stunden nach der Erstellung wieder gelöscht).  

* Dann werden auf der Rechnung 600 Einheiten ausgewiesen (2.500 RUs/Sek. geteilt durch die Grundeinheit von 100 RUs/Sek. x 24 Stunden). Die Kosten belaufen sich auf 4,80 US-Dollar (600 Einheiten mal 0,008 US-Dollar pro Einheit).

* Die Gesamtrechnung für den Monat beträgt 4,80 US-Dollar.

### <a name="billing-example---serverless-container"></a>Abrechnungsbeispiel: serverloser Container

* Angenommen, wir erstellen einen serverlosen Container. 

* Im Laufe eines Monats geben wir Datenbankanforderungen aus, die insgesamt 500.000-Anforderungseinheiten nutzen. Die Kosten belaufen sich auf 0,125 USD (500.000 * 0,25 USD/Million).

* Die Gesamtrechnung für den Monat beträgt 0,125 USD.

### <a name="billing-rate-if-storage-size-changes"></a>Abrechnungssatz bei Änderung der Speichergröße

Die Einheit für die Speicherkapazität ist die pro Stunde maximal gespeicherte Datenmenge (in GB) und wird für einen Zeitraum von einem Monat abgerechnet. Wenn Sie beispielsweise in der ersten Hälfte des Monats 100 GB Speicher und in der zweiten Hälfte des Monats 50 GB Speicher nutzen, wird Ihnen eine Nutzung von 75 GB Speicher für diesen Monat in Rechnung gestellt.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Abrechnungssatz für Container, die weniger als eine Stunde aktiv sind

Ihnen wird für jede Stunde, die der Container oder die Datenbank vorhanden ist, die Stundenpauschale in Rechnung gestellt, und zwar unabhängig von der Nutzung oder davon, ob der Container oder die Datenbank weniger als eine Stunde aktiv ist. Wenn Sie beispielsweise einen Container oder eine Datenbank erstellen und diesen bzw. diese fünf Minuten später löschen, enthält Ihre Rechnung eine Stunde.

### <a name="billing-rate-when-provisioned-throughput-on-a-container-or-database-scales-updown"></a>Abrechnungsrate für bereitgestellten Durchsatz für einen Container oder Hoch-/Herunterskalieren einer Datenbank

Wenn Sie um 9.30 Uhr den bereitgestellten Durchsatz von 400 RUs/Sek. auf 1.000 RUs/Sek. erhöhen und um 10.45 Uhr wieder auf 400 RUs/Sek. reduzieren, wird Ihnen der Durchsatz von 1.000 RUs/Sek. für zwei Stunden berechnet. 

Wenn Sie um 9.30 Uhr den bereitgestellten Durchsatz für einen oder mehrere Container von 100.000 RUs/Sek. auf 200.000 RUs/Sek. erhöhen und um 10.45 Uhr wieder auf 100.000 RUs/Sek. reduzieren, wird Ihnen der Durchsatz von 200.000 RUs/Sek. für zwei Stunden berechnet.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Abrechnungsbeispiel: Mehrere Container, jeweils mit dediziert bereitgestelltem Durchsatz

* Wenn Sie in der Region „USA, Osten 2“ ein Azure Cosmos-Konto mit zwei Containern und einem bereitgestellten Durchsatz von 500 RUs/Sek. bzw. 700 RUs/Sek. erstellen, beläuft sich der insgesamt bereitgestellte Durchsatz auf 1.200 RUs/Sek.  

* Ihnen würde Folgendes berechnet: 1.200/100 x 0,008 US-Dollar = 0,096 US-Dollar pro Stunde. 

* Wenn Sie aufgrund geänderter Durchsatzanforderungen die Kapazität der einzelnen Container um jeweils 500 RU/Sek. erhöhen und einen neuen, unbegrenzten Container mit 20.000 RU/Sek. erstellen, beträgt Ihre bereitgestellte Gesamtkapazität 22.200 RU/Sek. (1.000 RU/Sek.+ 1.200 RU/Sek. + 20.000 RU/Sek.).  

* Ihre Rechnung sähe dann folgendermaßen aus: 0,008 x 222 = 1,776 US-Dollar pro Stunde. 

* Wenn in einem Monat mit 720 Stunden (24 Stunden x 30 Tage) Ihr bereitgestellter Durchsatz 500 Stunden lang 1.200 RUs/Sek. und während der restlichen 220 Stunden 22.200 RUs/Sek. betrug, wird auf Ihrer Monatsrechnung Folgendes ausgewiesen: (500 x 0,096 US-Dollar/Stunde) + (220 x 1,776 US-Dollar/Stunde) = 438,72 US-Dollar pro Monat.

:::image type="content" source="./media/understand-your-bill/bill-example1.png" alt-text="Abrechnungsbeispiel für dedizierten Durchsatz":::

### <a name="billing-example-containers-with-shared-provisioned-throughput-mode"></a>Abrechnungsbeispiel: Container mit gemeinsam genutztem (bereitgestelltem) Durchsatzmodus

* Wenn Sie in der Region „USA, Osten 2“ ein Azure Cosmos-Konto mit zwei Azure Cosmos-Datenbanken (mit einer Reihe von Containern, die sich den Durchsatz auf Datenbankebene teilen) und einem bereitgestellten Durchsatz von 50.000 RUs/Sek. bzw. 70.000 RUs/Sek. erstellen, verfügen Sie über einen bereitgestellten Gesamtdurchsatz von 120.000 RUs/Sek.  

* Ihnen würden 1.200 x 0,008 US-Dollar = 9,60 US-Dollar pro Stunde berechnet. 

* Wenn Sie aufgrund geänderter Durchsatzanforderungen den bereitgestellten Durchsatz für jede Datenbank um 10.000 RU/Sek. erhöhen und zur ersten Datenbank mit dem geteilten Durchsatz außerdem einen neuen Container mit einem dedizierten Durchsatz von 15.000 RU/Sek. hinzufügen, beträgt Ihre bereitgestellte Gesamtkapazität 155.000 RU/Sek. (60.000 RU/Sek. + 80.000 RU/Sek.+ 15.000 RU/Sek.).  

* Ihre Rechnung sähe dann folgendermaßen aus: 1.550 x 0,008 = 12,40 US-Dollar pro Stunde.  

* Wenn in einem Monat mit 720 Stunden Ihr bereitgestellter Durchsatz 300 Stunden lang 120.000 RUs/Sek. und während der restlichen 420 Stunden 155.000 RUs/Sek. betrug, weist Ihre Monatsrechnung Folgendes aus: (300 x 9,60 US-Dollar/Stunde) + (420 x 12,40 US-Dollar/Stunde) = 2.880 + 5.208 = 8.088 US-Dollar pro Monat. 

:::image type="content" source="./media/understand-your-bill/bill-example2.png" alt-text="Abrechnungsbeispiel für gemeinsam genutzten Durchsatz":::

## <a name="billing-examples-with-geo-replication"></a>Abrechnungsbeispiele mit Georeplikation  

Sie können jederzeit Azure-Regionen auf der ganzen Welt zu Ihrem Azure Cosmos-Datenbankkonto hinzufügen oder aus diesem entfernen. Der Durchsatz, den Sie für die verschiedenen Azure Cosmos-Datenbanken und -Container konfiguriert haben, wird in jeder Azure-Region reserviert, die mit Ihrem Azure Cosmos-Datenbankkonto verknüpft ist. Wenn der bereitgestellte Gesamtdurchsatz (RUs/Sek.), der für alle Datenbanken und Container in Ihrem Azure Cosmos-Datenbankkonto (pro Stunde) konfiguriert ist, die Summe „T“ ergibt und die Anzahl der mit Ihrem Datenbankkonto verknüpften Azure-Regionen „N“ ist, ist der bereitgestellte Durchsatz für Ihr Azure Cosmos-Datenbankkonto pro Stunde T x N RUs/Sek. Der bereitgestellte Durchsatz (Schreibanforderung für eine Region) kostet 0,008 US-Dollar/Stunde pro 100 RUs/Sek., und der bereitgestellte Durchsatz bei Schreibanforderungen für mehrere Regionen (Konfiguration für Schreibvorgänge in mehreren Regionen) kostet 0,016 US-Dollar/Stunde pro 100 RUs/Sek. (siehe [Preise](https://azure.microsoft.com/pricing/details/cosmos-db/)). Azure Cosmos DB erlaubt in jedem Fall das Lesen von Daten in den Regionen, und zwar unabhängig davon, ob die Schreibanforderung für eine oder mehrere Regionen ausgewählt wurde.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Abrechnungsbeispiel: Azure Cosmos DB-Konto mit mehreren Regionen und Schreibanforderung für eine Region

Angenommen, Sie verfügen über einen Azure Cosmos-Container in der Region „USA, Westen“. Der Container wird mit einem Durchsatz von 10.000 RUs/Sek. erstellt, und Sie benötigen in diesem Monat einen Speicher von 1 TB für Ihre Daten. Angenommen, Sie fügen zu Ihrem Azure Cosmos-Konto drei weitere Regionen („USA, Osten“, „Europa, Norden“ und „Asien, Osten“) mit jeweils der gleichen Speicherkapazität und dem gleichen Durchsatz hinzu. Ihre monatliche Gesamtrechnung (basierend auf einem Monat von 30 Tagen) sieht dann wie folgt aus: 

|**Element** |**Nutzung (Monat)** |**Rate** |**Monatliche Kosten** |
|---------|---------|---------|-------|
|Durchsatzabrechnung für Container in „USA, Westen“      | 10.000 RUs/Sek. x 24 x 30    |0,008 US-Dollar pro 100 RUs/Sek. pro Stunde   |576 US-Dollar|
|Durchsatzabrechnung für drei zusätzliche Regionen „USA, Osten“, „Europa, Norden“ und „Asien, Osten“       | 3 x 10.000 RUs/Sek. x 24 x 30    |0,008 US-Dollar pro 100 RUs/Sek. pro Stunde  |1\.728 US-Dollar|
|Speicherabrechnung für Container in „USA, Westen“      | 250 GB    |0,25 US-Dollar/GB  |62,50 US-Dollar|
|Speicherabrechnung für 3 zusätzliche Regionen „USA, Osten“, „Europa, Norden“ und „Asien, Osten“      | 3 x 250 GB    |0,25 US-Dollar/GB  |187,50 US-Dollar|
|**Gesamt**     |     |  |**2.554 US-Dollar**|

*Angenommen, Sie übertragen jeden Monat 100 GB Daten aus dem Container in der Region „USA, Westen“, in die Regionen „USA, Osten“, „Europa, Norden“ und „Asien, Osten“, um sie dort zu replizieren. Ausgehende Daten werden nach Datenübertragungsrate abgerechnet.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Abrechnungsbeispiel: Azure Cosmos DB-Konto mit mehreren Regionen und Schreibanforderung für mehrere Regionen

Angenommen, Sie erstellen einen Azure Cosmos-Container in der Region „USA, Westen“. Der Container wird mit einem Durchsatz von 10.000 RUs/Sek. erstellt, und Sie benötigen in diesem Monat einen Speicher von 1 TB für Ihre Daten. Nehmen wir an, Sie fügen nun drei weitere Regionen („USA, Osten“, „Europa, Norden“ und „Asien, Osten“) mit jeweils der gleichen Speicherkapazität und dem gleichen Durchsatz hinzu. Dabei sollen in allen mit Ihrem Azure Cosmos-Konto verknüpften Regionen Schreibvorgänge für die Container möglich sein. Ihre monatliche Gesamtrechnung (basierend auf einem Monat von 30 Tagen) sieht dann wie folgt aus:

|**Element** |**Nutzung (Monat)**|**Rate** |**Monatliche Kosten** |
|---------|---------|---------|-------|
|Durchsatzabrechnung für Container in „USA, Westen“ (Schreibanforderungen für alle Regionen)       | 10.000 RUs/Sek. x 24 x 30    |0,016 US-Dollar pro 100 RUs/Sek. pro Stunde    |1\.152 US-Dollar |
|Durchsatzabrechnung für drei zusätzliche Regionen „USA, Osten“, „Europa, Norden“ und „Asien, Osten“ (Schreibanforderungen für alle Regionen)        | (3+1) x 10.000 RUs/Sek. x 24 x 30    |0,016 US-Dollar pro 100 RUs/Sek. pro Stunde   |4\.608 US-Dollar |
|Speicherabrechnung für Container in „USA, Westen“      | 250 GB    |0,25 US-Dollar/GB  |62,50 US-Dollar|
|Speicherabrechnung für 3 zusätzliche Regionen „USA, Osten“, „Europa, Norden“ und „Asien, Osten“      | 3 x 250 GB    |0,25 US-Dollar/GB  |187,50 US-Dollar|
|**Gesamt**     |     |  |**6.010 US-Dollar**|

*Angenommen, Sie übertragen jeden Monat 100 GB Daten aus dem Container in der Region „USA, Westen“, in die Regionen „USA, Osten“, „Europa, Norden“ und „Asien, Osten“, um sie dort zu replizieren. Ausgehende Daten werden nach Datenübertragungsrate abgerechnet.*

### <a name="billing-example-azure-cosmos-account-with-multi-region-writes-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Abrechnungsbeispiel: Azure Cosmos-Konto mit Schreibvorgängen für mehrere Regionen, Durchsatz auf Datenbankebene einschließlich dediziertem Durchsatz für einige Container

Betrachten Sie das folgende Beispiel für ein Azure Cosmos-Konto mit mehreren Regionen, bei dem in allen Regionen Schreibvorgänge ausgeführt werden können (Konfiguration für Schreibvorgänge für mehrere Regionen). Der Einfachheit halber wird davon ausgegangen, dass die Speichergröße konstant bleibt und sich nicht ändert. Daher wird sie hier nicht aufgeführt, um das Beispiel einfacher zu halten. Der bereitgestellte Durchsatz schwankt während des Monats wie folgt (30 Tage bzw. 720 Stunden vorausgesetzt): 

[0-100 Stunden]\:  

* Es wird ein Azure Cosmos-Konto mit drei Regionen („USA, Westen“, „USA, Osten“, „Europa, Norden“) und Schreibanforderungen für alle Regionen erstellt 

* Es wird eine Datenbank (D1) mit einem gemeinsam genutzten Durchsatz von 10.000 RUs/Sek. erstellt 

* Es wird eine Datenbank (D2) mit einem gemeinsam genutzten Durchsatz von 30.000 RUs/Sek. erstellt  

* Es wird ein Container (C1) mit dediziertem Durchsatz von 20.000 RUs/Sek. erstellt 

[101-200 Stunden]\:  

* Die Datenbank (D1) wird auf 50.000 RUs/Sek. hochskaliert 

* Die Datenbank (D2) wird auf 70.000 RUs/Sek. hochskaliert  

* Der Container (C1) wird gelöscht  

[201-300 Stunden]\:  

* Der Container (C1) wird erneut erstellt mit dediziertem Durchsatz von 20.000 RUs/Sek. 

[301-400 Stunden]\:  

* Eine der Regionen wird aus dem Azure Cosmos-Konto entfernt (es gibt jetzt noch zwei Regionen mit Schreibanforderungen) 

* Die Datenbank (D1) wird auf 10.000 RUs/Sek. herunterskaliert 

* Die Datenbank (D2) wird auf 80.000 RUs/Sek. hochskaliert  

* Der Container (C1) wird erneut gelöscht 

[401-500 Stunden]\:  

* Die Datenbank (D2) wird auf 10.000 RUs/Sek. herunterskaliert  

* Der Container (C1) wird erneut erstellt mit dediziertem Durchsatz von 20.000 RUs/Sek. 

[501-700 Stunden]\:  

* Die Datenbank (D1) wird auf 20.000 RUs/Sek. hochskaliert  

* Die Datenbank (D2) wird auf 100.000 RUs/Sek. hochskaliert  

* Der Container (C1) wird erneut gelöscht  

[701-720 Stunden]\:  

* Die Datenbank (D2) wird auf 50.000 RUs/Sek. herunterskaliert  

Visuell werden die Änderungen im insgesamt bereitgestellten Durchsatz während der 720 Stunden im Monat in der folgenden Abbildung dargestellt: 

:::image type="content" source="./media/understand-your-bill/bill-example3.png" alt-text="Praxisbeispiel":::

Die monatliche Gesamtrechnung wird (30 Tage und 720 Stunden im Monat vorausgesetzt) wie folgt berechnet:

|**Stunden**  |**RUs/Sek.** |**Element** |**Nutzung (stündlich)** |**Kosten** |
|---------|---------|---------|-------|-------|
|[0-100] |D1: 10.000 <br/>D2: 30.000 <br/>C1: 20.000 |Durchsatzabrechnung für Container in „USA, Westen“ (Schreibanforderungen für alle Regionen)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |960 US-Dollar  |
| | |Durchsatzabrechnung für zwei weitere Regionen „USA, Osten“ und „Europa, Norden“ (Schreibanforderungen für alle Regionen)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |2\.880 US-Dollar  |
|[101-200] |D1: 50.000 <br/>D2: 70.000 <br/>C1: -- |Durchsatzabrechnung für Container in „USA, Westen“ (Schreibanforderungen für alle Regionen)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |1\.920 US-Dollar  |
| | |Durchsatzabrechnung für zwei weitere Regionen „USA, Osten“ und „Europa, Norden“ (Schreibanforderungen für alle Regionen)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |5\.760 US-Dollar  |
|[201-300]  |D1: 50.000 <br/>D2: 70.000 <br/>C1: 20.000 |Durchsatzabrechnung für Container in „USA, Westen“ (Schreibanforderungen für alle Regionen)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |2\.240 USD  |
| | |Durchsatzabrechnung für zwei weitere Regionen „USA, Osten“ und „Europa, Norden“ (Schreibanforderungen für alle Regionen)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |6\.720 US-Dollar |
|[301-400] |D1: 10.000 <br/>D2: 80.000 <br/>C1: -- |Durchsatzabrechnung für Container in „USA, Westen“ (Schreibanforderungen für alle Regionen)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |1\.440 US-Dollar   |
| | |Durchsatzabrechnung für zwei weitere Regionen „USA, Osten“ und „Europa, Norden“ (Schreibanforderungen für alle Regionen)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |2\.880 US-Dollar  |
|[401-500] |D1: 10.000 <br>D2: 10.000 <br>C1: 20.000 |Durchsatzabrechnung für Container in „USA, Westen“ (Schreibanforderungen für alle Regionen)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |640 US-Dollar  |
| | |Durchsatzabrechnung für zwei weitere Regionen „USA, Osten“ und „Europa, Norden“ (Schreibanforderungen für alle Regionen)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |1\.280 US-Dollar  |
|[501-700] |D1: 20.000 <br>D2: 100.000 <br>C1: -- |Durchsatzabrechnung für Container in „USA, Westen“ (Schreibanforderungen für alle Regionen)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |3\.840 US-Dollar  |
| | |Durchsatzabrechnung für zwei weitere Regionen „USA, Osten“ und „Europa, Norden“ (Schreibanforderungen für alle Regionen)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |7\.680 US-Dollar  |
|[701-720] |D1: 20.000 <br/>D2: 50.000 <br/>C1: -- |Durchsatzabrechnung für Container in „USA, Westen“ (Schreibanforderungen für alle Regionen)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |224 US-Dollar  |
| | |Durchsatzabrechnung für zwei weitere Regionen „USA, Osten“ und „Europa, Norden“ (Schreibanforderungen für alle Regionen)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |224 US-Dollar  |
|| |**Monatliche Gesamtkosten**  | |**38.688 US-Dollar**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Abrechnungsbeispiele mit Free-Tarif-Konten
Beim Free-Tarif für Azure Cosmos DB erhalten Sie die ersten 400 RUs/Sek. und 5 GB Speicher in Ihrem Konto kostenlos, angewendet auf der Kontoebene. Jegliche Nutzung, die über 400 RUs/Sek. und 5 GB Speicher hinausgeht, wird zu den regulären Preisen abgerechnet, die auf der Preisseite angegeben sind. Die Rechnung enthält keine Gebühr oder Position für die kostenlosen 400 RUs/Sek. bzw. 5 GB, sondern nur für die über den Free-Tarif hinausgehende RUs/Sek. und Speichernutzung. Die 400 RUs/Sek. gelten für jeden beliebigen Typ von RUs/Sek. – bereitgestellten Durchsatz, Autoskalierung und Schreibvorgänge für mehrere Regionen.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Abrechnungsbeispiel: Container oder Datenbank mit bereitgestelltem Durchsatz
- Angenommen, Sie erstellen eine Datenbank oder einen Container in einem Free-Tarif-Konto mit 400 RUs/Sek. und 5 GB Speicher.
- Dann werden auf Ihrer Rechnung keine Gebühren für diese Ressource angezeigt. Ihre stündlichen und monatlichen Kosten betragen 0 US-Dollar.
- Nehmen Sie jetzt an, in demselben Konto fügen Sie eine weitere Datenbank oder einen weiteren Container mit 1.000 RUs/Sek. und 10 GB Speicher hinzu.
- Dann zeigt Ihre Rechnung eine Gebühr für die 1.000 RUs/Sek. und 10 GB Speicher an. 

### <a name="billing-example---container-with-autoscale-throughput"></a>Abrechnungsbeispiel: Container mit automatisch skalierendem Durchsatz
- Angenommen, Sie erstellen in einem Konto mit Free-Tarif einen Container mit aktivierter Autoskalierung mit einem maximalen Durchsatz von 4.000 RU/Sek. Diese Ressource wird automatisch zwischen 400 RUs/Sek. und 4.000 RUs/Sek. skaliert. 
- Angenommen, in Stunde 1 bis 10 erfolgt der Durchsatz bei der Ressource mit dem minimalen Wert 400 RUs/Sek. Während Stunde 11 wird die Ressource auf 1.000 RUs/Sek. zentral hochskaliert und anschließend innerhalb dieser Stunde wieder auf 400 RUs/Sek. herunterskaliert.
- In den Stunden 1 bis 10 werden Ihnen 0 US-Dollar für den Durchsatz in Rechnung gestellt, da die 400 RUs/Sek. durch den Free-Tarif abgedeckt wurden. 
- In Stunde 11 werden Ihnen effektive 1.000 RUs/Sek. – 400 RUs/Sek. = 600 RUs/Sek. in Rechnung gestellt, da dies der höchste RUs/Sek.-Wert in dieser Stunde ist. Dies sind 6 Einheiten von 100 RUs/Sek. für die Stunde. Damit belaufen sich die gesamten Durchsatzkosten für diese Stunde auf 6 Einheiten x 0,012 US-Dollar = 0,072 US-Dollar. 
- Jeder Speicher, der über die ersten 5 GB hinausgeht, wird zu normalen Speicherraten abgerechnet. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Abrechnungsbeispiel: Konto mit mehreren Regionen und Schreibanforderung für eine Region
- Angenommen, Sie erstellen in einem Free-Tarif-Konto eine Datenbank oder einen Container mit 1200 RUs/Sek. und 10 GB Speicher. Sie replizieren das Konto in drei Regionen und haben ein Konto mit Schreibanforderung für eine Region.
- Insgesamt (ohne den Free-Tarif) würden für 3 x 1.200 RUs/Sek. = 3.600 RUs/Sek. und 3 x 10 GB = 30 GB Speicher in Rechnung gestellt.
- Beim Free-Tarif-Rabatt wird nach dem Entfernen von 400 RUs/Sek. und 5 GB Speicher ein effektiver bereitgestellter Durchsatz von 3.200 RUs/Sek. (32 Einheiten) zum Satz der Schreibanforderung für eine Region und für 25 GB Speicher in Rechnung gestellt.
- Dann würden die monatlichen Kosten für RUs/Sek. betragen: 32 Einheiten × 0,008 US-Dollar x 24 Stunden × 31 Tage = 190,46 US-Dollar. Die monatlichen Kosten für Speicher würden betragen: 25 GB x 0,25/GB = 6,25 US-Dollar. Die Gesamtkosten würden betragen: 190,46 US-Dollar + 6,25 US-Dollar = 196,71 US-Dollar.
- Hinweis: Wenn sich der Einzelpreis für RUs/Sek. oder Speicher in den Regionen unterscheidet, entsprechen die 400 RUs/Sek. und 5 GB Speicher den Tarifen der Region, in der das Konto erstellt wurde.

### <a name="billing-example---multi-region-account-with-multiple-write-regions"></a>Abrechnungsbeispiel: Konto mit mehreren Regionen und Konto mit Schreibanforderung für mehrere Regionen

In diesem Beispiel werden [Preise für Schreibvorgänge für mehrere Regionen](https://azure.microsoft.com/pricing/details/cosmos-db/) für Konten verwendet, die nach dem 1. Dezember 2019 erstellt wurden. 
- Angenommen, Sie erstellen in einem Free-Tarif-Konto eine Datenbank oder einen Container mit 1.200 RUs/Sek. und 10 GB Speicher. Sie replizieren das Konto in drei Regionen und haben ein Konto mit Schreibanforderung für mehrere Regionen. 
- Insgesamt (ohne den Free-Tarif) würden Ihnen für 3 x 1.200 RUs/Sek. = 3.600 RUs/Sek. und 3 x 10 GB = 30 GB Speicher in Rechnung gestellt.
- Beim Free-Tarif-Rabatt wird nach dem Entfernen von 400 RUs/Sek. und 5 GB Speicher ein effektiver bereitgestellter Durchsatz von 3.200 RUs/Sek. (32 Einheiten) zum Satz der Schreibanforderung für mehrere Regionen und für 25 GB Speicher in Rechnung gestellt.
- Dann würden die monatlichen Kosten für RUs/Sek. betragen: 32 Einheiten × 0,016 US-Dollar × 24 Stunden × 31 Tage = 380,93 US-Dollar. Die monatlichen Kosten für Speicher würden betragen: 25 GB x 0,25/GB = 6,25 US-Dollar. Die Gesamtkosten würden betragen: 380,93 US-Dollar + 6,25 US-Dollar = 387,18 US-Dollar.

## <a name="proactively-estimating-your-monthly-bill"></a>Vorausschauende Schätzung der monatlichen Rechnung  

Betrachten wir ein weiteres Beispiel, bei dem Sie vor dem Monatsende proaktiv Ihren Rechnungsbetrag schätzen möchten. Sie können Ihre Rechnung wie folgt schätzen:

**Speicherkosten**

* Durchschnittliche Datensatzgröße (KB): 1 
* Anzahl von Datensätzen: 100.000.000 
* Gesamtspeicher (GB): 100 
* Monatliche Kosten pro GB: 0,25 USD 
* Erwartete monatliche Speicherkosten: 25,00 USD 

**Durchsatzkosten**

|Vorgangsart| Anforderungen/Sek.| Durchschn. RU/Anforderung| RUs erforderlich|
|----|----|----|----|
|Schreiben| 100 | 5 | 500|
|Lesen| 400| 1| 400|

RUs/Sek. insgesamt: 500 + 400 = 900 Kosten pro Stunde: 900/100 x 0,008 US-Dollar = 0,072 US-Dollar Erwartete monatliche Durchsatzkosten (31 Tage vorausgesetzt): 0,072 US-Dollar x 24 x 31 = 53,57 US-Dollar

**Monatliche Gesamtkosten**

Monatliche Gesamtkosten = monatliche Speicherkosten + monatliche Durchsatzkosten = monatliche Gesamtkosten: 25,00 + 53,57 = 78,57 US-Dollar

*Die Preise hängen von der Region ab. Die aktuellen Preise finden Sie auf der Seite [Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Abrechnung mit reservierter Kapazität für Azure Cosmos DB

Mit der reservierten Kapazität für Azure Cosmos DB können Sie im Voraus bereitgestellten Durchsatz (reservierte Kapazität oder eine Reservierung) erwerben, der auf alle Azure Cosmos-Datenbanken und -Container (für jede API und jedes Datenmodell) in allen Azure-Regionen angewendet werden kann. Da sich die Preise für den bereitgestellten Durchsatz je nach Region unterscheiden, können Sie sich reservierte Kapazität als monetäres Guthaben vorstellen, das Sie mit einem Rabatt erworben haben und für die Zahlung des für den bereitgestellten Durchsatz fälligen Betrags zum jeweils in der Region geltenden Preis nutzen können. Nehmen wir beispielsweise an, Sie verfügen über ein Azure Cosmos-Konto mit einem einzelnen Container, für den 50.000 RU/Sek. bereitgestellt werden und der global in zwei Regionen („USA, Osten“ und „Japan, Osten“) repliziert wird. Bei der nutzungsbasierten Option würden Sie Folgendes zahlen:  

* In der Region „USA, Osten“: 50.000 RUs/Sek. zu einer Rate von 0,008 US-Dollar pro 100 RUs/Sek. in dieser Region 

* In der Region „Japan, Osten“: 50.000 RUs/Sek. zu einer Rate von 0,009 US-Dollar pro 100 RUs/Sek. in dieser Region

Ihre Gesamtrechnung (ohne reservierte Kapazität) sähe wie folgt aus (30 Tage oder 720 Stunden vorausgesetzt): 

|**Region**| **Stundenpreis pro 100 RUs/Sek.**|**Einheiten (RUs/Sek.)**|**Rechnungsbetrag (pro Stunde)**| **Rechnungsbetrag (pro Monat)**|
|----|----|----|----|----|
|East US|0,008 US-Dollar |50.000|4 US-Dollar|2\.880 US-Dollar |
|Japan, Osten|0,009 US-Dollar |50.000| 4,50 US-Dollar |3\.240 US-Dollar |
|Gesamt|||8,50 US-Dollar|6\.120 US-Dollar |

Angenommen, Sie hätten stattdessen reservierte Kapazität gekauft. Sie können reservierte Kapazität von 100.000 RUs/Sek. zum Preis von 56.064 US-Dollar für ein Jahr (20 % Rabatt) bzw. 6,40 US-Dollar pro Stunde erwerben. Die Preise für reservierte Kapazität finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).  

* Durchsatzkosten (nutzungsbasierte Bezahlung): 100.000 RUs/Sek. : 100 x 0,008 US-Dollar pro Stunde x 8.760 Stunden pro Jahr = 70.080 US-Dollar 

* Durchsatzkosten (mit reservierter Kapazität): 20 % Rabatt auf 70.080 US-Dollar = 56.064 US-Dollar 

Was Sie tatsächlich erworben haben, ist ein Guthaben von 8 US-Dollar pro Stunde für 100.000 RU/Sek. zum Preis von 6,40 US-Dollar pro Stunde (auf Basis des Listenpreises für die Region „USA, Osten“). Von dieser im Voraus bezahlten Durchsatzreservierung wird dann auf Stundenbasis die bereitgestellte Durchsatzkapazität abgezogen. Dies gilt für jede globale Azure-Region und für die entsprechenden regionalen Listenpreise, die für Ihr Abonnement festgelegt sind. In diesem Beispiel, in dem Sie 50.000 RUs/Sek. sowohl in der Region „USA, Osten“ als auch in „Japan, Osten“ bereitstellen, können Sie bereitgestellten Durchsatz im Wert von 8,00 US-Dollar pro Stunde abziehen. Die Überschreitung von 0,50 US-Dollar pro Stunde (bzw. 360 US-Dollar pro Monat) wird Ihnen dann in Rechnung gestellt. 

|**Region**| **Stundenpreis pro 100 RUs/Sek.**|**Einheiten (RUs/Sek.)**| **Rechnungsbetrag (pro Stunde)**| **Rechnungsbetrag (pro Monat)**|
|----|----|----|----|----|
|East US|0,008 US-Dollar |50.000|4 US-Dollar|2\.880 US-Dollar |
|Japan, Osten|0,009 US-Dollar |50.000| 4,50 US-Dollar |3\.240 US-Dollar |
|||Nutzungsbasierte Bezahlung|8,50 US-Dollar|6\.120 US-Dollar|
|Erworbene reservierte Kapazität|0,0064 US-Dollar (20 % Rabatt) |Kapazität im Wert von 100 RUs/Sek. bzw. 8 US-Dollar vorab erworben |\- 8 US-Dollar|\- 5.760 US-Dollar |
|Nettorechnungsbetrag|||0,50 US-Dollar |360 US-Dollar |

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie sich in folgenden Artikeln über die Kostenoptimierung in Azure Cosmos DB informieren:

* Weitere Informationen unter [Warum ist das Preismodell von Azure Cosmos DB kosteneffizient für den Kunden?](total-cost-ownership.md)
* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](./optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)