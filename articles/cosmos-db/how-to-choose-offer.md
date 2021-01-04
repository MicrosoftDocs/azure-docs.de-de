---
title: Auswählen zwischen manuell bereitgestelltem und automatisch skaliertem Durchsatz in Azure Cosmos DB
description: In diesem Artikel erfahren Sie, wie Sie für Ihre Workload zwischen dem standardmäßig (manuell) bereitgestellten und dem automatisch skalierten Durchsatz auswählen.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: d8a6471d53ad4b2428504f9c53cbec6bc1967c49
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089637"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Auswählen zwischen standardmäßig (manuell) bereitgestelltem und automatisch skaliertem Durchsatz 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB unterstützt zwei Arten oder Angebote von bereitgestelltem Durchsatz: den standardmäßig (manuell) bereitgestellten und den automatisch skalierten Durchsatz. Beide Durchsatztypen eignen sich für unternehmenskritische Workloads, die eine hohe Leistung und Skalierung erfordern und hinsichtlich des Durchsatzes, der Verfügbarkeit, der Latenz und der Konsistenz denselben Azure Cosmos DB-SLAs unterliegen.

In diesem Artikel wird beschrieben, wie Sie für Ihre Workload zwischen dem standardmäßig (manuell) bereitgestellten und dem automatisch skalierten Durchsatz auswählen. 

## <a name="overview-of-provisioned-throughput-types"></a>Übersicht über bereitgestellte Durchsatztypen
Bevor Sie sich eingehender mit den Unterschieden zwischen dem standardmäßig (manuell) bereitgestellten und dem automatisch skalierten Durchsatz befassen, müssen Sie zunächst verstehen, wie der bereitgestellte Durchsatz in Azure Cosmos DB funktioniert. 

Wenn Sie bereitgestellten Durchsatz verwenden, legen Sie den Durchsatz fest. Dieser wird in Anforderungseinheiten pro Sekunde (Request Units per Second, RU/s) gemessen, die für Ihre Workload erforderlich sind. Der Dienst stellt die Kapazität bereit, die zur Erfüllung der Durchsatzanforderungen benötigt wird. Datenbankvorgänge des Diensts, z. B. Lese-, Schreib- und Abfragevorgänge, verbrauchen eine bestimmte Menge von Anforderungseinheiten. Weitere Informationen zu [Anforderungseinheiten](request-units.md)

In der folgenden Tabelle wird der standardmäßig (manuell) bereitgestellte Durchsatz allgemein mit dem automatisch skalierten Durchsatz verglichen.

|BESCHREIBUNG|Standard (manuell)|Autoscale|
|-------------|------|-------|
|Am besten geeignet für|Workloads mit stetigem oder vorhersagbarem Datenverkehr|Workloads mit variablem oder nicht vorhersagbarem Datenverkehr Weitere Informationen finden Sie unter [Anwendungsfälle des automatisch skalierten Durchsatzes](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Funktionsweise|Sie stellen eine festgelegte Anzahl von RU/s (`T`) bereit, die sich im Zeitverlauf nicht ändert, sofern Sie sie nicht manuell bearbeiten. Pro Sekunde können Sie einen Durchsatz von bis zu `T` RU/s verwenden. <br/><br/>Wenn Sie den standardmäßig (manuell) bereitgestellten Durchsatz z. B. auf 400 RU/s festlegen, bleibt dieser auch bei 400 RU/s.|Sie legen die maximale Anzahl von RU/s fest (`Tmax`), die das System nicht überschreiten soll. Der Durchsatz `T` wird im System automatisch skaliert, sodass `0.1* Tmax <= T <= Tmax`. <br/><br/>Wenn Sie die maximale Anzahl von RU/s für die Autoskalierung z. B. auf 4.000 RU/s festlegen, wird im System zwischen 400–4.000 RU/s skaliert.|
|Einsatzgebiete|Sie möchten Ihre Durchsatzkapazität (RU/s) manuell verwalten und selbst skalieren.<br/><br/>Ihre bereitgestellten RU/s werden stark und konsistent genutzt. Wenn Sie in einem Monat eine bestimmte Anzahl `T` von RU/s bereitstellen und diese in 66 % (oder mehr) der Stunden aufbrauchen, werden Sie wahrscheinlich mit dem standardmäßig (manuell) bereitgestellten Durchsatz sparen.<br/><br/>Diese Schätzung basiert auf einem Vergleich zwischen dem Wert von `T` beim standardmäßig (manuell) bereitgestellten Durchsatz und dem Wert von `Tmax` beim automatisch skalierten Durchsatz. |Azure Cosmos DB soll Ihre Durchsatzkapazität (RU/s) und die Skalierung der Nutzung entsprechend verwalten.<br/><br/>Die Nutzung Ihrer RU/s ist variabel oder schwer vorhersagbar. Wenn Sie in einem Monat eine bestimmte Höchstzahl `Tmax` von RU/s per Autoskalierung bereitstellen und diese Höchstzahl `Tmax` in 66 % (oder weniger) der Stunden aufbrauchen, werden Sie wahrscheinlich mit dem automatisch skalierten Durchsatz sparen.<br/><br/>Diese Schätzung basiert auf einem Vergleich zwischen dem Wert von `Tmax` beim automatisch skalierten Durchsatz und dem Wert von `T` beim standardmäßig (manuell) bereitgestellten Durchsatz.|
|Abrechnungsmodell|Die Abrechnung erfolgt auf Stundenbasis für die bereitgestellten RU/s und unabhängig davon, wie viele RUs verbraucht wurden.<br/><br/>Beispiel: <li>Sie stellen 400 RU/s bereit.</li><li>1\. Stunde: keine Anforderungen</li><li>2\. Stunde: Anforderungen im Umfang von 400 RU/s</li><br/><br/>Für beide Stunden (erste und zweite) werden Ihnen 400 RU/s zum [Tarif des standardmäßig (manuell) bereitgestellten Durchsatzes](https://azure.microsoft.com/pricing/details/cosmos-db/) in Rechnung gestellt.|Die Abrechnung erfolgt auf Stundenbasis. Ihnen wird die höchste Anzahl von RU/s berechnet, auf die das System in der Stunde skaliert wurde. <br/><br/>Beispiel: <li>Sie legen als Höchstzahl für die Autoskalierung 4.000 RU/s fest (System skaliert von 400 bis 4.000 RU/s).</li><li>1\. Stunde: Das System wurde auf einen Höchstwert von 3.500 RU/s skaliert.</li><li>2\. Stunde: Das System wurde aufgrund von zu geringer Auslastung auf den Minimalwert von 400 RU/s (immer 10 % von `Tmax`) herunterskaliert.</li><br/><br/>Ihnen werden die 3.500 RU/s in der 1. Stunde und die 400 RU/s in 2. Stunde zum [Tarif des automatisch skalierten Durchsatzes](https://azure.microsoft.com/pricing/details/cosmos-db/) in Rechnung gestellt. Der für die Autoskalierung geltende Tarif pro RU/s beträgt das 1,5-Fache des Tarifs für den standardmäßig (manuell) bereitgestellten Durchsatz.
|Was geschieht, wenn die bereitgestellten RU/s überschritten werden?|Die Anzahl der bereitgestellten RU/s bleibt konstant bei der festgelegten Menge. Alle Anforderungen, die in einer Sekunde die Anzahl der bereitgestellten RU/s übersteigen, unterliegen einer Durchsatzbeschränkung. In der Antwort wird empfohlen, vor einem neuen Versuch noch etwas zu warten. Sie können die Anzahl der RU/s bei Bedarf manuell erhöhen oder verringern.| Das System skaliert die RU/s bis hin zu der für die Autoskalierung festgelegten Höchstzahl von RU/s. Alle Anforderungen, die in einer Sekunde die Höchstzahl der automatisch skalierten RU/s übersteigen, unterliegen einer Durchsatzbeschränkung. In der Antwort wird empfohlen, vor einem neuen Versuch noch etwas zu warten.|

## <a name="understand-your-traffic-patterns"></a>Grundlegendes zu Ihren Datenverkehrsmustern

### <a name="new-applications"></a>Neue Anwendungen ###

Wenn Sie eine neue Anwendung erstellen und Ihr Datenverkehrsmuster noch nicht kennen, sollten Sie mit den Einstiegspunkt-RU/s (oder der Mindestzahl von RU/s) beginnen, um eine überflüssige Bereitstellung am Anfang zu vermeiden. Wenn Ihre Anwendung klein ist und keine hohe Skalierbarkeit erfordert, sollten Sie nur die minimalen Einstiegspunkt-RU/s bereitstellen, um die Kosten zu optimieren. Für kleine Anwendungen mit geringem zu erwartenden Datenverkehr können Sie auch den Kapazitätsmodus [serverlos](throughput-serverless.md) in Betracht ziehen.

Unabhängig davon, ob Sie standardmäßige (manuelle) Skalierung oder Autoskalierung verwenden möchten, sollten Sie Folgendes berücksichtigen:

Wenn Sie den standardmäßig (manuell) bereitgestellten Durchsatz mit einem Einstiegspunkt von 400 RU/s bereitstellen, können Sie nicht mehr als 400 RU/s nutzen, es sei denn, Sie ändern den festgelegten Durchsatz manuell. Ihnen werden pro Stunde 400 RU/s zum Tarif des standardmäßig (manuell) bereitgestellten Durchsatzes berechnet.

Wenn Sie den Durchsatz per Autoskalierung mit einem Einstiegspunkt von maximal 4.000 RU/s bereitstellen, skaliert die Ressource von 400 bis 4.000 RU/s. Da der automatisch skalierte Durchsatz pro RU/s mit dem 1,5-fachen Tarif des standardmäßig (manuell) bereitgestellten Durchsatzes abgerechnet wird, wird Ihre Rechnung für die Stunden, in denen das System auf den Minimalwert von 400 RU/s herunterskaliert wurde, höher als ausfallen, als wenn die 400 RU/s manuell bereitgestellt worden wären. Dafür können Sie mit der Autoskalierung jederzeit bis zu 4.000 RU/s nutzen, falls die Auslastung Ihrer Anwendung plötzlich steigt, ohne dass eine Benutzeraktion erforderlich ist. Sie sollten generell die Vorteile abwägen, die ein konstant verfügbarer Höchstwert von RU/s mit sich bringt, der zum 1,5-fachen Tarif abgerechnet wird.

Mit dem [Capacity Planer](estimate-ru-with-capacity-planner.md) von Azure Cosmos DB können Sie Ihre Durchsatzanforderungen schätzen lassen. 

### <a name="existing-applications"></a>Bestehende Anwendungen ###

Wenn Sie eine vorhandene Anwendung mit einem standardmäßig (manuell) bereitgestellten Durchsatz haben, können Sie mit [Azure Monitor-Metriken](../azure-monitor/insights/cosmosdb-insights-overview.md) bestimmen, ob sich Ihr Datenverkehrsmuster für die Autoskalierung eignet. 

Suchen Sie dazu zuerst für die Datenbank oder den Container nach der [normalisierten Verbrauchsmetrik für Anforderungseinheiten](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric). Die normalisierte Auslastung ist ein Maß für die aktuelle Nutzung Ihres standardmäßig (manuell) bereitgestellten Durchsatzes. Je näher die Zahl bei 100 % liegt, desto näher sind Sie der vollständigen Nutzung der bereitgestellten RU/s. [Weitere Informationen zur Metrik](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric)

Ermitteln Sie als Nächstes, wie sich die normalisierte Auslastung im Zeitverlauf ändert. Ermitteln Sie die höchste normalisierte Auslastung für jede Stunde. Berechnen Sie dann die durchschnittliche normalisierte Auslastung in allen Stunden. Wenn Sie feststellen, dass die durchschnittliche Auslastung weniger als 66 % beträgt, sollten Sie das Aktivieren der automatischen Skalierung für Ihre Datenbank oder Ihren Container in Betracht ziehen. Wenn hingegen die durchschnittliche Auslastung mehr als 66 % beträgt, wird empfohlen, beim standardmäßigen (manuell) bereitgestellten Durchsatz zu bleiben.

> [!TIP]
> Wenn Ihr Konto für die Verwendung von Schreibvorgängen in mehreren Regionen konfiguriert ist und mehr als eine Region umfasst, ist die Rate pro 100 RU/s bei manuell bereitgestelltem und automatisch skaliertem Durchsatz identisch. Das bedeutet, dass die Aktivierung der automatischen Skalierung unabhängig von der Auslastung keine zusätzlichen Kosten verursacht. Daher ist es immer ratsam, die automatische Skalierung bei Schreibvorgängen in mehreren Regionen zu verwenden, wenn Sie über mehr als eine Region verfügen, um von den Einsparungen zu profitieren, da nur für die RU/s gezahlt werden muss, auf die Ihre Anwendung skaliert wird. Wenn Sie über Schreibvorgänge in mehreren Regionen und eine einzelne Region verfügen, stellen Sie anhand der durchschnittlichen Auslastung fest, ob die automatische Skalierung zu Kosteneinsparungen führt. 

#### <a name="examples"></a>Beispiele

Nachfolgend wird für zwei verschiedene Beispielworkloads analysiert, ob sie für den manuell bereitgestellten oder automatisch skalierten Durchsatz geeignet sind. Zur Veranschaulichung der allgemeinen Vorgehensweise werden drei Verlaufsstunden analysiert, um den Kostenunterschied zwischen manueller Bereitstellung und automatischer Skalierung zu ermitteln. Bei Produktionsworkloads empfiehlt es sich, einen Verlauf von 7 bis 30 Tagen (oder länger, falls verfügbar) zu verwenden, um ein Muster für die Nutzung von RU/s zu erstellen.

> [!NOTE]
> Alle in diesem Dokument gezeigten Beispiele basieren auf dem Preis für ein Azure Cosmos-Konto, das in einer Region in den USA bereitgestellt wird, bei der es sich nicht um eine Government-Region handelt. Die Preise und die Berechnung variieren je nach verwendeter Region. Aktuelle Preisinformationen finden Sie auf der Seite [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

Voraussetzungen:
- Der manuell bereitgestellte Durchsatz beträgt derzeit 30.000 RU/s. 
- Die Region ist mit Schreibvorgängen in einer einzelnen Region und einer Region konfiguriert. Falls mehrere Regionen verfügbar sind, werden die stündlichen Kosten mit der Anzahl der Regionen multipliziert.
- Es werden die öffentlichen Preise für manuell bereitgestellten (0,008 USD pro 100 RU/s pro Stunde) und automatisch skalierten Durchsatz (0,012 USD pro 100 RU/s pro Stunde) für Konten mit Schreibzugriff auf eine einzelne Region verwendet. Einzelheiten finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/cosmos-db/). 

#### <a name="example-1-variable-workload-autoscale-recommended"></a>Beispiel 1: Variable Workload (automatische Skalierung empfohlen)

Sehen Sie sich zunächst den normalisierten RU-Verbrauch an. Diese Workload weist einen variablen Datenverkehr mit einem normalisierte RU-Verbrauch zwischen 6 und 100 % auf. Es gibt gelegentliche Spitzen von 100 %, die nur schwer vorhergesagt werden können, jedoch viele Stunden mit geringer Auslastung. 

:::image type="content" source="media/how-to-choose-offer/variable-workload_use_autoscale.png" alt-text="Workload mit variablem Datenverkehr – normalisierter RU-Verbrauch zwischen 6 und 100 % für alle Stunden":::

Vergleichen Sie nun die Kosten für die manuelle Bereitstellung eines Durchsatzes von 30.000 RU/s mit der Festlegung von maximal 30.000 RU/s für die automatische Skalierung (Skalierung zwischen 3000 und 30.000 RU/s). 

Nun wird der bisherige Verlauf analysiert. Angenommen, die Auslastung entspricht der folgenden Tabelle. Die durchschnittliche Auslastung in diesen drei Stunden liegt bei 39 %. Da der normalisierte RU-Verbrauch durchschnittlich weniger als 66 % beträgt, wird durch die automatische Skalierung eine Ersparnis erzielt. 

Beachten Sie, dass bei einer Nutzung von 6 % in Stunde 1 für die automatische Skalierung 10 % der maximalen Anzahl von RU/s (der Mindestwert pro Stunde) in Rechnung gestellt werden. Obwohl die Kosten für die automatische Skalierung in bestimmten Stunden höher als für den manuell bereitgestellten Durchsatz sein können, ist die automatische Skalierung insgesamt günstiger, solange die durchschnittliche Auslastung in allen Stunden weniger als 66 % beträgt.

|Zeitraum  | Auslastung |Abgerechnete RU/s bei automatischer Skalierung  |Option 1: Manuelle Bereitstellung von 30.000 RU/s  | Option 2: Automatische Skalierung zwischen 3000 und 30,000 RU/s |
|---------|---------|---------|---------|---------|
|Stunde 1  | 6 %  |     3000  |  30.000 x 0,008 / 100 = 2,40 USD        |   3000 x 0,012 / 100 = 0,36 USD      |
|Stunde 2  | 100 %  |     30.000    |  30.000 x 0,008 / 100 = 2,40 USD       |  30.000 x 0,012 / 100 = 3,60 USD      |
|Stunde 3 |  11 %  |     3300    |  30.000 x 0,008 / 100 = 2,40 USD       |    3300 x 0,012 / 100 = 0,40 USD     |
|**Gesamt**   |  |        |  7,20 USD       |    4,36 USD (39 % Ersparnis)    |

#### <a name="example-2-steady-workload-manual-throughput-recommended"></a>Beispiel 2: Konstante Workload (manuell bereitgestellter Durchsatz empfohlen)

Diese Workload weist einen stetigen Datenverkehr mit einem normalisierten RU-Verbrauch zwischen 72 und 100 % auf. Wenn 30.000 RU/s bereitgestellt werden, bedeutet das einen Verbrauch zwischen 21.600 und 30.000 RU/s.

:::image type="content" source="media/how-to-choose-offer/steady_workload_use_manual_throughput.png" alt-text="Workload mit stetigem Datenverkehr – normalisierter RU-Verbrauch zwischen 72 und 100 % für alle Stunden":::

Vergleichen Sie nun die Kosten für die manuelle Bereitstellung eines Durchsatzes von 30.000 RU/s mit der Festlegung von maximal 30.000 RU/s für die automatische Skalierung (Skalierung zwischen 3000 und 30.000 RU/s).

Angenommen, der Auslastungsverlauf entspricht der folgenden Tabelle. Die durchschnittliche Auslastung in diesen drei Stunden liegt bei 88 %. Da der normalisierte RU-Verbrauch durchschnittlich mehr als 66 % beträgt, wird durch die manuelle Bereitstellung eine Ersparnis erzielt.

Im Allgemeinen gilt: Wenn die durchschnittliche Auslastung in allen 730 Stunden in einem Monat höher als 66 % ist, wird durch die manuelle Bereitstellung eine Ersparnis erzielt. 

| Zeitraum | Auslastung |Abgerechnete RU/s bei automatischer Skalierung  |Option 1: Manuelle Bereitstellung von 30.000 RU/s  | Option 2: Automatische Skalierung zwischen 3000 und 30,000 RU/s |
|---------|---------|---------|---------|---------|
|Stunde 1  | 72 %  |     21.600   |  30.000 x 0,008 / 100 = 2,40 USD        |   21.600 x 0,012 / 100 = 2,59 USD      |
|Stunde 2  | 93%  |     28.000    |  30.000 x 0,008 / 100 = 2,40 USD       |  28.000 x 0,012 / 100 = 3,36 USD       |
|Stunde 3 |  100 %  |     30.000    |  30.000 x 0,008 / 100 = 2,40 USD       |    30.000 x 0,012 / 100 = 3,60 USD     |
|**Gesamt**   |  |        |  7,20 USD       |    9,55 USD     |

> [!TIP]
> Beim standardmäßig (manuell) bereitgestellten Durchsatz können Sie mithilfe der normalisierten Auslastungsmetrik die tatsächlichen RU/s schätzen, die Sie verwenden würden, wenn der Durchsatz automatisch skaliert werden würde. Multiplizieren Sie dazu die normalisierte Auslastung zu einem bestimmten Zeitpunkt mit den aktuell bereitgestellten Standard-RU/s (manuell bereitgestellt). Wenn Sie beispielsweise 5.000 RU/s bereitgestellt haben und die normalisierte Auslastung bei 90 % liegt, dann beträgt die Nutzung der RU/s 0,9 x 5.000 = 4.500 RU/s. Wenn Sie feststellen, dass Ihr Datenverkehrsmuster variabel ist, Sie aber zu viele oder zu wenige RU/s bereitgestellt haben, sollten Sie die Autoskalierung aktivieren und die Einstellung für die maximale Anzahl von RU/s entsprechend ändern.

#### <a name="how-to-calculate-average-utilization"></a>Berechnen der durchschnittlichen Auslastung
Bei der automatischen Skalierung wird die höchste Anzahl von RU/s abgerechnet, auf die in einer Stunde skaliert wird. Wenn Sie den normalisierten RU-Verbrauch im Zeitverlauf analysieren, ist es wichtig, bei der Berechnung des Durchschnitts die höchste Auslastung pro Stunde zu verwenden. 

Wenn Sie den Durchschnittswert der höchsten Auslastung in allen Stunden berechnen möchten, gehen Sie folgendermaßen vor:
1. Legen Sie die **Aggregation** für die Metrik „Normalisierter RU-Verbrauch“ auf **Max** fest.
1. Legen Sie die **Zeitgranularität** auf 1 Stunde fest.
1. Navigieren Sie zu **Diagrammoptionen**.
1. Wählen Sie die Option „Balkendiagramm“ aus. 
1. Wählen Sie unter **Freigabe** die Option **In Excel herunterladen** aus. Berechnen Sie anhand der generierten Kalkulationstabelle die durchschnittliche Auslastung in allen Stunden. 

:::image type="content" source="media/how-to-choose-offer/variable-workload-highest-util-by-hour.png" alt-text="Anzeigen des normalisierten RU-Verbrauchs nach Stunde: 1) Zeitgranularität auf 1 Stunde festlegen, 2) Diagrammeinstellungen bearbeiten, 3) Option „Balkendiagramm“ auswählen, 4) Unter „Freigabe“ die Option „In Excel herunterladen“ auswählen, um den Durchschnitt in allen Stunden zu berechnen":::

## <a name="measure-and-monitor-your-usage"></a>Messen und Überwachen der Auslastung
Nachdem Sie einen Durchsatztyp festgelegt haben, sollten Sie Ihre Anwendung im Zeitverlauf überwachen und bei Bedarf Anpassungen vornehmen. 

Wenn Sie die Autoskalierung verwenden, können Sie sich mit Azure Monitor die bereitgestellte maximale Anzahl von RU/s ( **Autoscale Max Throughput** [Maximaler Durchsatz bei Autoskalierung]) sowie die RU/s anzeigen lassen, auf die das System zurzeit skaliert ist ( **Provisioned Throughput** [Bereitgestellter Durchsatz]). Nachstehend finden Sie ein Beispiel für eine variable oder nicht vorhersehbare Workload mit automatisch skaliertem Durchsatz. Beachten Sie, dass das System die RU/s auf den Mindestwert von 10 % der maximalen RU/s skaliert (in diesem Fall 5.000 RU/s bzw. 50.000 RU/s), wenn kein Datenverkehr vorhanden ist. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Beispiel einer Workload mit automatischer Skalierung: maximal 50.000 RU/s für automatische Skalierung, Durchsatz zwischen 5000 und 50.000 RU/s":::

> [!NOTE]
> Wenn Sie den standardmäßig (manuell) bereitgestellten Durchsatz verwenden, bezieht sich die Metrik **Provisioned Throughput** (Bereitgestellte Durchsatz) auf den Wert, den Sie als Benutzer festgelegt haben. Bei Verwendung des automatisch skalierten Durchsatzes bezieht sich diese Metrik auf die Anzahl von RU/s, auf die das System zurzeit skaliert ist.

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie den [RU-Planer](https://cosmos.azure.com/capacitycalculator/), um den Durchsatz für Ihre Workloads zu schätzen.
* Mit [Azure Monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) können Sie Ihre vorhandenen Workloads überwachen.
* Informieren Sie sich darüber, wie Sie [automatisch skalierten Durchsatz für eine Datenbank oder einen Container in Azure Cosmos DB bereitstellen](how-to-provision-autoscale-throughput.md).
* Erfahren Sie mehr in den [häufig gestellten Fragen zur Autoskalierung](autoscale-faq.md).