---
title: Azure SQL-Datenbank-Dienstebenen – DTU-basiertes Kaufmodell | Microsoft-Dokumentation
description: Erfahren Sie etwas über die Dienstebenen für Einzel- und Pooldatenbanken im DTU-basierten Kaufmodell, um verschiedene Compute- und Speichergrößen bereitzustellen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 5/10/2019
ms.openlocfilehash: 5d769e2198b714d611b3004e11ccc624d4c6acdf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546455"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Diensttarife beim DTU-basierten Kaufmodell

Dienstebenen beim DTU-basierten Kaufmodell unterscheiden sich durch eine Reihe von Computegrößen mit einer festen Menge an integriertem Speicher, einem festen Aufbewahrungszeitraum für Sicherungen und einem festen Preis. Alle Dienstebenen im DTU-basierten Kaufmodell ermöglichen das flexible Wechseln von Computegrößen ohne Ausfallzeiten. Einzeldatenbanken und Pools für elastische Datenbanken werden nach Dienstebene und Computegröße auf Stundenbasis abgerechnet.

> [!IMPORTANT]
> Für die verwaltete SQL-Datenbank-Instanz wird das DTU-basierte Kaufmodell nicht unterstützt. Weitere Informationen finden Sie unter [Verwaltete Azure SQL-Datenbank-Instanz](sql-database-managed-instance.md).
> [!NOTE]
> Weitere Informationen zu den V-Kern-basierten Dienstebenen finden Sie unter [Auswählen einer V-Kern-Dienstebene und von Compute-, Arbeitsspeicher-, Speicher- und E/A-Ressourcen](sql-database-service-tiers-vcore.md). Weitere Informationen zu den Unterschieden zwischen DTU-basierten Dienstebenen und V-Kern-basierten Dienstebenen finden Sie unter [Kaufmodelle für Azure SQL-Datenbank und Ressourcen](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Vergleich der DTU-basierten Dienstebenen

Die Auswahl einer Dienstebene hängt in erster Linie von den Anforderungen an Geschäftskontinuität, Speicher und Leistung ab.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|Zielworkload|Entwicklung und Produktion|Entwicklung und Produktion|Entwicklung und Produktion|
|Betriebszeit-SLA|99,99 %|99,99 %|99,99 %|
|Sicherungsaufbewahrung|7 Tage|35 Tage|35 Tage|
|CPU|Niedrig|Niedrig, Mittel, Hoch|Mittel, Hoch|
|E/A-Durchsatz (ungefähr) |2,5 IOPS pro DTU| 2,5 IOPS pro DTU | 48 IOPS pro DTU|
|E/A-Wartezeit (ungefähr)|5 ms (Lesen), 10 ms (Schreiben)|5 ms (Lesen), 10 ms (Schreiben)|2 ms (Lesen/Schreiben)|
|Columnstore-Indizierung |–|S3 und höher|Unterstützt|
|In-Memory-OLTP|–|–|Unterstützt|
|||||

> [!NOTE]
> Sie können eine kostenlose Azure SQL-Datenbank mit dem Diensttarif „Basic“ in Verbindung mit einem kostenlosen Azure-Konto erhalten, um Azure zu erkunden. Weitere Informationen finden Sie unter [Mit dem kostenlosen Azure-Konto eine verwaltete Clouddatenbank erstellen](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Begrenzungen für Einzeldatenbank-DTUs und Speicher

Computegrößen werden für Einzeldatenbanken als Datenbanktransaktionseinheiten (DTUs) und für Pools für elastische Datenbanken als elastische Datenbanktransaktionseinheiten (eDTUs) bezeichnet. Weitere Informationen zu DTUs und eDTUs finden Sie unter [DTU-basiertes Kaufmodell](sql-database-purchase-models.md#dtu-based-purchasing-model).

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| Maximale Speichergröße | 2 GB | 1 TB | 4 TB  |
| Maximale DTU-Anzahl | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Begrenzungen für eDTUs in Pools für elastische Datenbanken, Speicher und Pooldatenbanken

| | **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| Maximale Speichergröße pro Datenbank  | 2 GB | 1 TB | 1 TB |
| Maximale Speichergröße pro Pool | 156 GB | 4 TB | 4 TB |
| Maximale Anzahl von eDTUs pro Datenbank | 5 | 3000 | 4000 |
| Maximale Anzahl von eDTUs pro Pool | 1600 | 3000 | 4000 |
| Maximale Anzahl von Datenbanken pro Pool | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> In allen Regionen außer den folgenden ist im Premium-Tarif derzeit mehr als 1 TB Speicher verfügbar: Regionen „China, Osten“, „China, Norden“, „Deutschland, Mitte“, „Deutschland, Nordosten“, „USA, Westen-Mitte“, „US DoD“ und „US Government, Mitte“. In diesen Regionen ist der Speicher im Tarif „Premium“ auf 1 TB begrenzt.  Weitere Informationen finden Sie unter [Einschränkungen von P11 und P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>DTU-Vergleichstest

Physische Merkmale (CPU, Arbeitsspeicher, E/A), die jedem DTU-Measure zugeordnet sind, werden mithilfe eines Vergleichstests kalibriert, der eine realitätsnahe Datenbankworkload simuliert.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korrelieren von Vergleichstestergebnissen mit realer Datenbankleistung

Wichtig ist der Hinweis, dass alle Vergleichstests nur repräsentativen und informativen Charakter haben. Die mit der Vergleichstestanwendung erzielten Transaktionsraten entsprechen nicht denjenigen, die mit anderen Anwendungen erzielt werden. Der Vergleichstest umfasst eine Zusammenstellung verschiedener Typen von Transaktionen, die auf ein Schema angewendet werden, das einen Bereich von Tabellen und Datentypen enthält. Während für den Vergleichstest dieselben grundlegenden Vorgänge ausgeführt werden, die für alle OLTP-Workloads üblich sind, wird keine spezifische Klasse von Datenbank oder Anwendung abgebildet. Ziel des Vergleichstests ist das Bereitstellen eines vernünftigen Richtwerts für die relative Leistung einer Datenbank, der bei einer Hoch- oder Herunterskalierung der Computegröße erwartet werden kann. In der Praxis haben Datenbanken eine unterschiedliche Größe und Komplexität, müssen verschiedene Kombinationen von Workloads bewältigen und reagieren auf unterschiedliche Weise. Beispielsweise kann eine E/A-intensive Anwendung E/A-Schwellenwerte früher erreichen, oder eine CPU-intensive Anwendung kann CPU-Grenzwerte früher erreichen. Es gibt keine Garantie, dass eine bestimmte Datenbank bei steigender Last auf die gleiche Weise wie im Vergleichstest skaliert wird.

Der Vergleichstest und seine Methoden werden nachstehend ausführlich beschrieben.

### <a name="benchmark-summary"></a>Übersicht über den Vergleichstest

Die Benchmark misst die Leistung einer Kombination grundlegender Datenbankvorgänge, die bei OLTP-Workloads (Online Transaction Processing, Onlinetransaktionsverarbeitung) am häufigsten vorkommen. Obwohl der Vergleichstest speziell für das Cloud Computing konzipiert ist, wurden das Datenbankschema, der Datenbestand und Transaktionen so gestaltet, dass sie für die grundlegenden Elemente der am häufigsten vorkommenden OLTP-Workloads repräsentativ sind.

### <a name="schema"></a>Schema

Das Schema zeichnet sich durch ausreichende Vielfalt und Komplexität aus, um eine Vielzahl von Vorgängen zu unterstützen. Der Vergleichstest wird auf eine Datenbank angewendet, die aus sechs Tabellen besteht. Die Tabellen gehören zu drei Kategorien: feste Größe, Skalierung und anwachsend. Es gibt zwei Tabellen mit fester Größe, drei Skalierungstabellen und eine anwachsende Tabelle. Die Tabellen mit fester Größe haben eine konstante Anzahl von Zeilen. Die Skalierungstabellen haben eine Kardinalität, die proportional zur Datenbankleistung ist, sich jedoch während des Vergleichstests nicht ändert. Die anwachsende Tabelle hat anfänglich dieselbe Größe wie eine Skalierungstabelle, doch ihre Kardinalität ändert sich im Verlauf des Vergleichstests, da Zeilen eingefügt und gelöscht werden.

Das Schema enthält eine Kombination von Datentypen, wie z. B. ganze Zahl, numerisch, Zeichen und Datum/Uhrzeit. Das Schema enthält primäre und sekundäre Schlüssel, aber keine Fremdschlüssel, was bedeutet, dass es keine Einschränkungen der referentiellen Integrität zwischen Tabellen gibt.

Ein Datengenerierungsprogramm erzeugt die Daten für die ursprüngliche Datenbank. Ganzzahl- und numerische Daten werden anhand verschiedener Strategien generiert. Mitunter werden Werte zufällig über einen Bereich verteilt. In anderen Fällen wird eine Gruppe von Werten nach dem Zufallsprinzip permutiert, um sicherzustellen, dass eine bestimmte Verteilung beibehalten wird. Textfelder werden anhand einer gewichteten Liste von Wörtern generiert, um realistisch wirkende Daten zu erstellen.

Die Datenbankgröße basiert auf einem "Skalierungsfaktor". Der Skalierungsfaktor (abgekürzt SF) bestimmt die Kardinalität der Skalierungs- und anwachsenden Tabellen. Wie im folgenden Abschnitt "Benutzer und Geschwindigkeit" beschrieben, werden die Datenbankgröße, Anzahl der Benutzer und maximale Leistung allesamt proportional zueinander skaliert.

### <a name="transactions"></a>Transaktionen

Die Workload besteht aus neun Transaktionstypen, wie in der folgenden Tabelle dargestellt. Jede Transaktion ist darauf ausgelegt, eine bestimmte Gruppe von Systemmerkmalen in der Datenbank-Engine und in der Systemhardware in starker Abgrenzung zu den anderen Transaktionen hervorzuheben. Dieser Ansatz erleichtert das Bewerten der Auswirkung verschiedener Komponenten auf die Gesamtleistung. Beispielsweise erzeugt die Transaktion "Leseintensiv" eine hohe Anzahl von Lesevorgängen auf dem Datenträger.

| Transaktionstyp | BESCHREIBUNG |
| --- | --- |
| Nicht leseintensiv |SELECT, speicherintern, schreibgeschützt |
| Mittel leseintensiv |SELECT, zumeist speicherintern, schreibgeschützt |
| Leseintensiv |SELECT, zumeist nicht speicherintern, schreibgeschützt |
| Nicht aktualisierungsintensiv |UPDATE; speicherintern, lesen/schreiben |
| Aktualisierungsintensiv |UPDATE, zumeist nicht speicherintern, lesen/schreiben |
| Nicht einfügungsintensiv |INSERT, speicherintern; lesen/schreiben |
| Einfügungsintensiv |INSERT, zumeist nicht speicherintern, lesen/schreiben |
| Löschen |DELETE, Kombination aus speicherintern und nicht speicherintern, lesen/schreiben |
| CPU-intensiv |SELECT, speicherintern, relativ hohe CPU-Last, schreibgeschützt |

### <a name="workload-mix"></a>Kombination von Workloads

Transaktionen werden zufällig aus einer gewichteten Verteilung in der folgenden Kombination ausgewählt. Die Kombination weist ein Verhältnis von Lese- zu Schreibvorgängen von ca. 2:1 auf.

| Transaktionstyp | % der Kombination |
| --- | --- |
| Nicht leseintensiv |35 |
| Mittel leseintensiv |20 |
| Leseintensiv |5 |
| Nicht aktualisierungsintensiv |20 |
| Aktualisierungsintensiv |3 |
| Nicht einfügungsintensiv |3 |
| Einfügungsintensiv |2 |
| Löschen |2 |
| CPU-intensiv |10 |

### <a name="users-and-pacing"></a>Benutzer und Geschwindigkeit

Die Workload des Vergleichstests stammt von einem Tool, das Transaktionen über verschiedene Verbindungen übermittelt, um das Verhalten vieler gleichzeitiger Benutzer zu simulieren. Obwohl alle Verbindungen und Transaktionen computergeneriert sind, bezeichnen wir diese Verbindungen der Einfachheit halber als "Benutzer". Obwohl jeder Benutzer unabhängig von allen anderen Benutzern operiert, führen alle Benutzer denselben Zyklus der unten aufgeführten Schritte aus:

1. Herstellen einer Datenbankverbindung.
2. Wiederholen bis zur Aufforderung zum Beenden:
   - Auswählen einer Transaktion nach dem Zufallsprinzip (aus gewichteter Verteilung)
   - Ausführen der ausgewählten Transaktion und Messen der Antwortzeit
   - Warten auf eine Geschwindigkeitsverzögerung
3. Schließen der Datenbankverbindung
4. Beenden

Die Geschwindigkeitsverzögerung (in Schritt 2c) wird nach dem Zufallsprinzip ausgewählt, hat aber eine Verteilung mit durchschnittlich 1,0 Sekunden. Daher kann jeder Benutzer durchschnittlich höchstens eine Transaktion pro Sekunde generieren.

### <a name="scaling-rules"></a>Skalierungsregeln

Die Anzahl der Benutzer wird von der Größe der Datenbank (in Skalierungsfaktoreinheiten) bestimmt. Für alle fünf Skalierungsfaktoreinheiten gibt es einen Benutzer. Aufgrund der Geschwindigkeitsverzögerung kann ein Benutzer durchschnittlich höchstens eine Transaktion pro Sekunde generieren.

Beim Skalierungsfaktor 500 (SF=500) hat die Datenbank 100 Benutzer und kann eine maximale Rate von 100 Transaktionen pro Sekunde (TPS) erreichen. Zum Erreichen einer höheren TPS-Rate sind mehr Benutzer und eine größere Datenbank erforderlich.

### <a name="measurement-duration"></a>Messdauer

Ein gültiger Vergleichstestlauf erfordert eine stabile Messdauer von mindestens einer Stunde.

### <a name="metrics"></a>metrics

Die Hauptmetriken im Vergleichstest sind Durchsatz und Antwortzeit.

- Der Durchsatz ist die wesentliche Messgröße im Vergleichstest. Der Durchsatz wird als Transaktionen pro Zeiteinheit gemeldet, wobei alle Transaktionstypen gezählt werden.
- Die Antwortzeit ist ein Maß für die Vorhersagbarkeit von Leistung. Die Einschränkung der Antwortzeit variiert je nach Dienstklasse, wobei höhere Dienstklassen strengere Anforderungen an die Antwortzeit haben (siehe unten).

| Dienstklasse | Durchsatzmaß | Anforderung an die Antwortzeit |
| --- | --- | --- |
| Premium |Transaktionen pro Sekunde |95\. Perzentil bei 0,5 Sekunden |
| Standard |Transaktionen pro Minute |90\. Perzentil bei 1,0 Sekunden |
| Basic |Transaktionen pro Stunde |80\. Perzentil bei 2,0 Sekunden |

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Informationen zu bestimmten Computegrößen und Speichergrößen für Einzeldatenbanken finden Sie unter [DTU-basierte Ressourceneinschränkungen in SQL-Datenbank für Einzeldatenbanken](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Ausführliche Informationen zu Auswahlmöglichkeiten bestimmter Computegrößen und Speichergrößen für Pools für elastische Datenbanken finden Sie unter [DTU-basierte Ressourceneinschränkungen in SQL-Datenbank](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
