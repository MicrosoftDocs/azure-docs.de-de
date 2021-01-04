---
title: 'Compute- und Speicheroptionen – Azure Database for MySQL: Flexible Server'
description: 'In diesem Artikel werden die Compute- und Speicheroptionen in Azure Database for MySQL: Flexible Server beschrieben.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 36f31ee390a6a208b202698ec9bda59b644c9e30
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534669"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Compute- und Speicheroptionen in Azure Database for MySQL: Flexible Server (Vorschau)

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Sie können eine Azure Database for MySQL Flexible Server-Instanz basierend auf drei unterschiedlichen Computeebenen erstellen: „Burstfähig“, „Allgemeiner Zweck“ und „Arbeitsspeicheroptimiert“. Die Computeebenen werden durch die zugrunde liegende VM-SKU unterschieden, die die B-Serie, die D-Serie und die E-Serie verwendet. Die Auswahl von Computeebene und -größe bestimmt den auf dem Server verfügbaren Arbeitsspeicher und die Anzahl der virtuellen Kerne (vCores). Über sämtliche Computeebenen hinweg wird dieselbe Speichertechnologie verwendet. Alle Ressourcen werden auf der MySQL-Serverebene bereitgestellt. Ein Server kann über eine oder mehrere Datenbanken verfügen.

| Ressource/Ebene | **Burstfähig** | **Allgemeiner Zweck** | **Arbeitsspeicheroptimiert** |
|:---|:----------|:--------------------|:---------------------|
| VM-Serie| B-Serie | Ddsv4-Serie | Edsv4-Serie|
| V-Kerne | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Arbeitsspeicher pro V-Kern | Variable | 4 GiB | 8 GiB * |
| Speichergröße | 5 GiB bis 16 TiB | 5 GiB bis 16 TiB | 5 GiB bis 16 TiB |
| Aufbewahrungszeitraum von Datenbanksicherungen | 1 bis 35 Tage | 1 bis 35 Tage | 1 bis 35 Tage |

\* Mit Ausnahme der E64ds_v4-SKU (Arbeitsspeicheroptimiert), die 504 GB Arbeitsspeicher aufweist

Um eine Computeebene auszuwählen, verwenden Sie die folgende Tabelle als Ausgangspunkt.

| Computeebene | Zielworkloads |
|:-------------|:-----------------|
| Burstfähig | Am besten geeignet für Workloads, die nicht kontinuierlich die vollständige CPU benötigen. |
| Universell | Geeignet für die meisten Unternehmensworkloads mit gängigen Compute- und Arbeitsspeicheranforderungen und skalierbarem E/A-Durchsatz. Hierzu zählen beispielsweise zum Hosten von Web- und mobilen Apps verwendete Server und andere Unternehmensanwendungen.|
| Arbeitsspeicheroptimiert | Geeignet für Hochleistungs-Datenbankworkloads, für die In-Memory-Leistung erforderlich ist, um eine schnellere Transaktionsverarbeitung und höhere Parallelität zu erzielen. Hierzu zählen beispielsweise Server für die Verarbeitung von Echtzeitdaten und leistungsstarke Transaktions- oder Analyse-Apps.|

Nachdem Sie einen Server erstellt haben, haben sich die Computeebene, die Computegröße und die Speichergröße geändert. Die Computeskalierung erfordert einen Neustart und dauert zwischen 60-120 Sekunden, während die Speicherskalierung keinen Neustart erfordert. Sie können den Aufbewahrungszeitraum für Sicherungen auch unabhängig nach oben oder unten anpassen. Weitere Informationen finden Sie im Abschnitt [Skalieren von Ressourcen](#scale-resources).

## <a name="compute-tiers-size-and-server-types"></a>Computeebenen, -größe und -servertypen

Computeressourcen können basierend auf Ebene und Größe ausgewählt werden. Dies bestimmt die Anzahl der virtuellen Kerne und die Größe des Arbeitsspeichers. Virtuelle Kerne stellen die logische CPU der zugrunde liegenden Hardware dar.

Die detaillierten Spezifikationen der verfügbaren Servertypen lauten wie folgt:

| Computegröße         | V-Kerne | Arbeitsspeichergröße (GiB) | 
|----------------------|--------|-------------------|
| **Burstfähig**        |        |                   | 
| Standard_B1s         | 1      | 1                 |  
| Standard_B1ms        | 1      | 2                 | 
| Standard_B2s         | 2      | 4                 |  
| **Allgemeiner Zweck**  |        |                   | 
| Standard_D2ds_v4     | 2      | 8                 |  
| Standard_D4ds_v4     | 4      | 16                | 
| Standard_D8ds_v4     | 8      | 32                | 
| Standard_D16ds_v4    | 16     | 64                | 
| Standard_D32ds_v4    | 32     | 128               |  
| Standard_D48ds_v4    | 48     | 192               |  
| Standard_D64ds_v4    | 64     | 256               | 
| **Arbeitsspeicheroptimiert** |        |                   |
| Standard_E2ds_v4     | 2      | 16                |
| Standard_E4ds_v4     | 4      | 32                |
| Standard_E8ds_v4     | 8      | 64                |
| Standard_E16ds_v4    | 16     | 128               |
| Standard_E32ds_v4    | 32     | 256               |
| Standard_E48ds_v4    | 48     | 384               |
| Standard_E64ds_v4    | 64     | 504               |

Weitere Informationen zu den verfügbaren Computeserien finden Sie in der Azure VM-Dokumentation für [Burstfähig (B-Serie)](../../virtual-machines/sizes-b-series-burstable.md), [Allgemeiner Zweck (Ddsv4-Serie)](../../virtual-machines/ddv4-ddsv4-series.md) und [Arbeitsspeicheroptimiert (Edsv4-Serie)](../../virtual-machines/edv4-edsv4-series.md).

## <a name="storage"></a>Storage

Der von Ihnen bereitgestellte Speicher definiert die Speicherkapazität, die für Ihren flexiblen Server zur Verfügung steht. Der Speicher wird für die Datenbankdateien, temporären Dateien, Transaktionsprotokolle und MySQL-Serverprotokolle verwendet. Auf allen Computeebenen werden mindestens 5 GiB und maximal 16 TiB Speicherplatz unterstützt. Der Speicher wird in Schritten von 1 GiB skaliert und kann nach der Erstellung des Servers hochskaliert werden.

>[!NOTE]
> Der Speicher kann nur zentral hochskaliert und nicht herunterskaliert werden.

Sie können Ihren Speicherverbrauch im Azure-Portal (mit Azure Monitor) mithilfe der Metriken für „Speicherbegrenzung“, „Speicher in Prozent“ und „Verwendeter Speicher“ überwachen. Weitere Informationen zu Metriken finden Sie im Artikel [Überwachung](./concepts-monitoring.md). 

### <a name="reaching-the-storage-limit"></a>Erreichen der Speicherbegrenzung

Wenn der auf dem Server verbrauchte Speicherplatz fast die bereitgestellte Begrenzung erreicht hat, wird der Server in den schreibgeschützten Modus versetzt, um verloren gegangene Schreibzugriffe auf den Server zu schützen. Server mit 100 GiB oder weniger bereitgestelltem Speicher werden als schreibgeschützt gekennzeichnet, wenn der freie Speicher weniger als fünf Prozent der bereitgestellten Speichergröße beträgt. Server mit mehr als 100 GiB bereitgestelltem Speicher werden als schreibgeschützt gekennzeichnet, wenn der freie Speicher weniger als 5 GiB beträgt.

Wenn Sie also beispielsweise 110 GiB Speicher bereitgestellt haben und die tatsächliche Auslastung 105 GiB überschreitet, wird der Server als schreibgeschützt gekennzeichnet. Wenn Sie andererseits 5 GiB Speicher bereitgestellt haben, wird der Server als schreibgeschützt gekennzeichnet, wenn der freie Speicher unter 256 MB sinkt.

Während der Dienst versucht, den Server als schreibgeschützt zu kennzeichnen, werden alle neuen Schreibtransaktionsanforderungen blockiert, und bestehende aktive Transaktionen werden weiterhin ausgeführt. Wenn der Server als schreibgeschützt festgelegt ist, führen alle nachfolgenden Schreibvorgänge und die Transaktionscommits zu einem Fehler. Leseabfragen werden weiterhin ununterbrochen fortgesetzt. 

Sie sollten den bereitgestellten Speicherplatz auf dem Server erhöhen, um den Server aus dem schreibgeschützten Modus zu bewegen. Hierfür können Sie das Azure-Portal oder die Azure CLI verwenden. Nach der Erhöhung ist der Server wieder bereit, Schreibtransaktionen zu akzeptieren.

Sie sollten eine Benachrichtigung einrichten, damit Sie informiert werden, wenn sich der Serverspeicher dem Schwellenwert nähert. So können Sie vermeiden, in den schreibgeschützten Zustand zu geraten. Weitere Informationen zu verfügbaren Metriken finden Sie im Artikel [Überwachung](./concepts-monitoring.md). 

Wir empfehlen Folgendes: <!--turn on storage auto-grow or to--> Sie sollten eine Warnung einrichten, damit Sie informiert werden, wenn sich der Serverspeicher dem Schwellenwert nähert. So können Sie vermeiden, in den schreibgeschützten Zustand zu geraten. Weitere Informationen finden Sie in der Dokumentation zu Warnungen zum [Einrichten einer Warnung](how-to-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Automatische Speichervergrößerung

Die automatische Speichervergrößerung ist für Azure Database for MySQL Flexible Server noch nicht verfügbar.

## <a name="iops"></a>IOPS
Der minimale effektive IOPS-Wert beträgt 100 über alle Computegrößen und die maximale effektive IOPS-Anzahl wird durch die beiden folgenden Attribute bestimmt: 
- Compute: Die maximalen effektiven IOPS können durch die maximal verfügbaren IOPS der gewählten Computegröße begrenzt sein.
- Speicher: Auf allen Computeebenen wird die IOPS-Größe mit der bereitgestellten Speichergröße in einem Verhältnis von 3:1 skaliert.

Sie können die verfügbaren effektiven IOPS skalieren, indem Sie den bereitgestellten Speicherplatz erhöhen oder zu einer größeren Computegröße wechseln (falls Ihre IOPS durch die Computegröße begrenzt sind). In der Vorschau beträgt die maximale Anzahl unterstützter effektiver IOPS 20.000 IOPS.

Nachstehend erfahren Sie mehr über die maximalen effektiven IOPS pro Computegröße unter Verwendung der Kombination von Compute und Speicher: 

| Computegröße         | Maximale Anzahl effektiver IOPS  | 
|----------------------|---------------------|
| **Burstfähig**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Allgemeiner Zweck**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **Arbeitsspeicheroptimiert** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_E8ds_v4    | 12800               | 
| Standard_E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

Die maximale Anzahl effektiver IOPS ist abhängig von den maximal verfügbaren IOPS pro Computegröße. Weitere Informationen finden Sie in der nachstehenden Formel und in der Spalte *Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s* in der Dokumentation zur [B-Serie](../../virtual-machines/sizes-b-series-burstable.md), [Ddsv4-Serie](../../virtual-machines/ddv4-ddsv4-series.md) und [Edsv4-Serie](../../virtual-machines/edv4-edsv4-series.md).

**Maximale Anzahl effektiver IOPS** = MINIMUM( *"Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s"* von Computegröße, bereitgestellter Speicher in GiB * 3)

Sie können Ihren E/A-Verbrauch im Azure-Portal (mit Azure Monitor) mit der Metrik [E/A in Prozent](./concepts-monitoring.md) überwachen. Wenn Sie mehr IOPS benötigen, müssen Sie verstehen, ob Sie durch die Computegröße oder den bereitgestellten Speicher eingeschränkt sind. Skalieren Sie die Computegröße oder den bereitgestellten Speicher Ihres Servers entsprechend.

## <a name="backup"></a>Backup

Der Dienst erstellt automatisch Sicherungen Ihres Servers. Sie können als Aufbewahrungszeitraum einen Bereich von 1 bis 35 Tagen auswählen. Weitere Informationen zu Sicherungen finden Sie im [Konzeptartikel zur Sicherung und Wiederherstellung](concepts-backup-restore.md).

## <a name="scale-resources"></a>Skalieren von Ressourcen

Nachdem Sie Ihren Server erstellt haben, können Sie die Computeebene, die Computegröße (virtuelle Kerne und Arbeitsspeicher) und die Speichermenge sowie den Aufbewahrungszeitraum für Sicherungen einzeln ändern. Die Computegröße kann hoch- oder herunterskaliert werden. Die Aufbewahrungsdauer für Sicherungen kann von 1 bis zu 35 Tagen zentral hoch- oder herunterskaliert werden. Die Speichergröße kann nur erhöht werden. Die Skalierung der Ressourcen kann über das Portal oder per Azure CLI durchgeführt werden.

> [!NOTE]
> Die Speichergröße kann nur erhöht werden. Nach der Erhöhung können Sie nicht mehr zu einer kleineren Speichergröße zurückkehren.

Wenn Sie die Computeebene oder die Computegröße ändern, wird der Server neu gestartet, damit der neue Servertyp wirksam wird. Während des Moments, in dem das System den Wechsel zum neuen Server durchführt, können keine neuen Verbindungen hergestellt werden, und für alle Transaktionen ohne Commit erfolgt ein Rollback. Dieses Fenster variiert, liegt aber in den meisten Fällen zwischen 60-120 Sekunden. 

Die Skalierung des Speichers und die Änderung des Aufbewahrungszeitraums von Sicherungen sind Onlinevorgänge und erfordern keinen Neustart des Servers.

## <a name="pricing"></a>Preise

Aktuelle Preisinformationen finden Sie auf der Seite [Azure-Datenbank für MySQL – Preise](https://azure.microsoft.com/pricing/details/MySQL/). Informationen zu den Kosten der gewünschten Konfiguration können Sie im [Azure-Portal](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) auf der Registerkarte **Compute und Speicher** anzeigen. Dort werden die monatlichen Kosten basierend auf den von Ihnen ausgewählten Optionen angegeben. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie den Azure-Preisrechner verwenden, um einen geschätzten Preis zu erhalten. Wählen Sie auf der Website des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/) die Option **Elemente hinzufügen** aus, erweitern Sie die Kategorie **Datenbanken**, wählen Sie **Azure Database for MySQL** und **Flexible Server** als Bereitstellungstyp aus, um die Optionen anzupassen.

Wenn Sie die Serverkosten optimieren möchten, können Sie folgende Tipps in Betracht ziehen:

- Skalieren Sie Ihre Computeebene oder Computegröße (virtuelle Kerne) herunter, wenn Compute nicht ausgelastet ist.
- Erwägen Sie den Wechsel auf die Computeebene „Burstfähig“, wenn Ihre Workload nicht die volle Computekapazität aus den Ebenen „Allgemeiner Zweck“ und „Arbeitsspeicheroptimiert“ kontinuierlich benötigt.
- Beenden Sie den Server, wenn er nicht verwendet wird.
- Verringern Sie den Aufbewahrungszeitraum der Sicherung, wenn eine längere Aufbewahrung der Sicherung nicht erforderlich ist.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich, wie Sie [im Portal einen MySQL-Server erstellen](quickstart-create-server-portal.md).
- Informieren Sie sich über die [Diensteinschränkungen](concepts-limitations.md).