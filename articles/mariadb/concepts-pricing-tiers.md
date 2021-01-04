---
title: Tarife – Azure Database for MariaDB
description: Erfahren Sie mehr über die verschiedenen Tarife für Azure Database for MariaDB einschließlich Computegeneration, Speichertypen, Speichergröße, V-Kerne, Arbeitsspeicher und Aufbewahrungszeiträume für Sicherungen.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: b5b5a506b2f932d20a617634ace7ebf02093fbfa
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536284"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB – Tarife

Sie können eine Azure Database for MariaDB-Serverinstanz basierend auf drei unterschiedlichen Tarifen erstellen: „Basic“, „Allgemein“ und „Arbeitsspeicheroptimiert“. Die Tarife unterscheiden sich anhand der bereitstellbaren Menge an Rechenleistung in V-Kernen, des Arbeitsspeichers pro V-Kern und der zum Speichern der Daten verwendeten Speichertechnologie. Alle Ressourcen werden auf der MariaDB-Serverebene bereitgestellt. Ein Server kann über eine oder mehrere Datenbanken verfügen.

| Resource | **Grundlegend** | **Allgemeiner Zweck** | **Arbeitsspeicheroptimiert** |
|:---|:----------|:--------------------|:---------------------|
| Computegeneration | Gen 5 |Gen 5 | Gen 5 |
| V-Kerne | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Arbeitsspeicher pro V-Kern | 2 GB | 5 GB | 10 GB |
| Speichergröße | 5 GB bis 1 TB | 5 GB bis 4 TB | 5 GB bis 4 TB |
| Aufbewahrungszeitraum von Datenbanksicherungen | 7 bis 35 Tage | 7 bis 35 Tage | 7 bis 35 Tage |

Um einen Tarif auszuwählen, verwenden Sie die folgende Tabelle als Ausgangspunkt.

| Tarif | Zielworkloads |
|:-------------|:-----------------|
| Basic | Workloads mit geringen Anforderungen an Rechen- und E/A-Leistung. Beispiele hierfür sind Server, die für die Entwicklung, für Tests oder für kleine, selten verwendete Anwendungen verwendet werden. |
| Universell | Geeignet für die meisten Unternehmensworkloads mit gängigen Compute- und Arbeitsspeicheranforderungen und skalierbarem E/A-Durchsatz. Hierzu zählen beispielsweise zum Hosten von Web- und mobilen Apps verwendete Server und andere Unternehmensanwendungen.|
| Arbeitsspeicheroptimiert | Geeignet für Hochleistungs-Datenbankworkloads, für die In-Memory-Leistung erforderlich ist, um eine schnellere Transaktionsverarbeitung und höhere Parallelität zu erzielen. Hierzu zählen beispielsweise Server für die Verarbeitung von Echtzeitdaten und leistungsstarke Transaktions- oder Analyse-Apps.|

Nach der Servererstellung können Sie die Anzahl von virtuellen Kernen und den Tarif (mit Ausnahme eines Wechsels zu oder von Basic) innerhalb weniger Sekunden ändern. Außerdem haben Sie die Möglichkeit, die Speichermenge einzeln zu erhöhen und den Aufbewahrungszeitraum für Sicherungen zu erhöhen oder zu verringern, ohne dass bei der Anwendung Ausfallzeiten auftreten. Der Sicherungsspeichertyp kann nach der Servererstellung nicht mehr geändert werden. Weitere Informationen finden Sie im Abschnitt [Skalieren von Ressourcen](#scale-resources).

## <a name="compute-generations-and-vcores"></a>Computegenerationen und V-Kerne

Computeressourcen werden in Form von virtuellen Kernen bereitgestellt und repräsentieren die logische CPU der zugrunde liegenden Hardware. Logische CPUs der Generation 5 basieren auf Intel-Prozessoren vom Typ E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Storage

Der von Ihnen bereitgestellte Speicher definiert die Speicherkapazität, die für Ihren Azure Database for MariaDB-Server zur Verfügung steht. Der Speicher wird für die Datenbankdateien, temporären Dateien, Transaktionsprotokolle und MariaDB-Serverprotokolle verwendet. Außerdem wird durch die Gesamtmenge an bereitgestelltem Speicher die E/A-Kapazität Ihres Servers definiert.

| Speicherattribute   | Basic | Universell | Arbeitsspeicheroptimiert |
|:---|:----------|:--------------------|:---------------------|
| Speichertyp | Storage Basic | Allgemeiner Speicher | Allgemeiner Speicher |
| Speichergröße | 5 GB bis 1 TB | 5 GB bis 4 TB | 5 GB bis 4 TB |
| Speicherinkrementgröße | 1 GB | 1 GB | 1 GB |
| IOPS | Variable |3 IOPS/GB<br/>Min. 100 IOPS<br/>Max. 6.000 IOPS | 3 IOPS/GB<br/>Min. 100 IOPS<br/>Max. 6.000 IOPS |

Während und nach der Erstellung des Servers können Sie zusätzliche Speicherkapazität hinzufügen und dem System erlauben, den Speicher auf der Grundlage des Speicherbedarfs Ihrer Workload automatisch zu vergrößern.

>[!NOTE]
> Der Speicher kann nur zentral hochskaliert und nicht herunterskaliert werden.

Der Tarif „Basic“ umfasst keine IOPS-Garantie. Für die Tarife „Allgemein“ und „Arbeitsspeicheroptimiert“ wird der IOPS-Wert gegenüber der bereitgestellten Speichergröße in einem Verhältnis von 3:1 skaliert.

Sie können Ihren E/A-Verbrauch im Azure-Portal oder mit Azure CLI-Befehlen überwachen. Die wichtigen zu überwachenden Metriken sind das [Speicherlimit, der Speicherprozentsatz, der genutzte Speicher und der E/A-Prozentsatz](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Großspeicher (Vorschauversion)

Wir erhöhen die Speicherlimits für die Tarife „Universell“ und „Arbeitsspeicheroptimiert“. Neu erstellte Server, für die die Vorschauversion verwendet wird, können bis zu 16 TB an Speicher bereitstellen. Der IOPS-Wert kann im Verhältnis 3:1 auf bis zu 20.000 IOPS skaliert werden. Wie beim derzeit allgemein verfügbaren Speicher auch, können Sie nach der Erstellung des Servers zusätzliche Speicherkapazität hinzufügen und dem System erlauben, den Speicher auf Grundlage des Speicherbedarfs Ihrer Workload automatisch zu vergrößern.

| Speicherattribute | Universell | Arbeitsspeicheroptimiert |
|:-------------|:--------------------|:---------------------|
| Speichertyp | Azure Storage Premium | Azure Storage Premium |
| Speichergröße | 32 GB bis 16 TB| 32 bis 16 TB |
| Speicherinkrementgröße | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>Min. 100 IOPS<br/>Max. 20.000 IOPS| 3 IOPS/GB<br/>Min. 100 IOPS<br/>Max. 20.000 IOPS |

> [!IMPORTANT]
> Großspeicher befindet sich in den folgenden Regionen derzeit in der öffentlichen Vorschauphase: „USA, Osten“, „USA, Osten 2“, „Brasilien, Süden“, „USA, Mitte“, „USA, Westen“, „USA, Norden-Mitte“, „USA, Süden-Mitte“, „Europa, Norden“, „Europa, Westen“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „Asien, Südosten“, „Asien, Osten“, „Japan, Osten“, „Japan, Westen“, „Südkorea, Mitte“, „Südkorea, Süden“, „Australien, Osten“, „Australien, Südosten“, „USA, Westen 2“, „USA, Westen-Mitte“, „Kanada, Osten“ und „Kanada, Mitte“.
>
> Alle anderen Regionen unterstützen bis zu 4 TB Speicherplatz und bis zu 6.000 IOPS.
>

### <a name="reaching-the-storage-limit"></a>Erreichen der Speicherbegrenzung

Server mit 100 GB oder weniger bereitgestelltem Speicher werden als schreibgeschützt gekennzeichnet, wenn der freie Speicher weniger als fünf Prozent der bereitgestellten Speichergröße beträgt. Server mit mehr als 100 GB bereitgestelltem Speicher werden als schreibgeschützt gekennzeichnet, wenn der freie Speicher weniger als 5 GB beträgt.

Wenn Sie also beispielsweise 110 GB Speicher bereitgestellt haben und die tatsächliche Auslastung 105 GB überschreitet, wird der Server als schreibgeschützt gekennzeichnet. Wenn Sie andererseits 5 GB Speicher bereitgestellt haben, wird der Server als schreibgeschützt gekennzeichnet, wenn der freie Speicher unter 256 MB sinkt.

Während der Dienst versucht, den Server als schreibgeschützt zu kennzeichnen, werden alle neuen Schreibtransaktionsanforderungen blockiert, und bestehende aktive Transaktionen werden weiterhin ausgeführt. Wenn der Server als schreibgeschützt festgelegt ist, führen alle nachfolgenden Schreibvorgänge und die Transaktionscommits zu einem Fehler. Leseabfragen werden weiterhin ununterbrochen fortgesetzt. Nachdem Sie den bereitgestellten Speicher erhöht haben, ist der Server für die erneute Annahme von Schreibtransaktionen bereit.

Sie sollten die automatische Speichervergrößerung aktivieren oder eine Benachrichtigung einrichten, damit Sie informiert werden, wenn sich der Serverspeicher dem Grenzwert nähert. So können Sie den schreibgeschützten Zustand vermeiden. Weitere Informationen finden Sie in der Dokumentation zum [Einrichten einer Benachrichtigung](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Automatische Speichervergrößerung

Die automatische Speichervergrößerung verhindert, dass der Server nicht mehr über genügend Speicherplatz verfügt und schreibgeschützt wird. Wenn die automatische Speichervergrößerung aktiviert ist, wird der Speicher automatisch ohne Beeinträchtigung der Workload vergrößert. Bei Servern mit 100 GB oder weniger bereitgestelltem Speicher wird die bereitgestellte Speichergröße um 5 GB erhöht, sobald der freie Speicher unter zehn Prozent des bereitgestellten Speichers sinkt. Bei Servern mit mehr als 100 GB bereitgestelltem Speicher wird die bereitgestellte Speichergröße um fünf Prozent erhöht, sobald der freie Speicherplatz unter 10 GB der bereitgestellten Speichergröße sinkt. Dabei gelten die maximalen, oben beschriebenen Speichergrenzwerte.

Wenn Sie also beispielsweise 1000 GB Speicher bereitgestellt haben und die tatsächliche Auslastung 990 GB überschreitet, wird die Speichergröße des Servers auf 1050 GB erhöht. Bei 10 GB bereitgestelltem Speicher wird die Speichergröße alternativ auf 15 GB erhöht, wenn weniger als 1 GB Speicher frei ist.

Beachten Sie, dass der Speicher nur zentral hochskaliert und nicht herunterskaliert werden kann.

## <a name="backup"></a>Backup

Azure Database for MariaDB stellt bis zu 100 % Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher zur Verfügung. Alle Sicherungsspeicher, die Sie über diesen Betrag hinaus verwenden, werden in GB pro Monat abgerechnet. Beispiel: Wenn Sie einen Server mit 250 GB bereitstellen, verfügen Sie über 250 GB an zusätzlichem Speicher, der kostenlos für Serversicherungen zur Verfügung steht. Speicher für Sicherungen, die die 250 GB überschreiten, wird gemäß dem [Preismodell](https://azure.microsoft.com/pricing/details/mariadb/) abgerechnet. Informationen zu Faktoren, die sich auf die Sicherungsspeichernutzung sowie die Überwachung und Steuerung der Sicherungsspeicherkosten beziehen, finden Sie in der [Dokumentation zur Sicherung](concepts-backup.md).

## <a name="scale-resources"></a>Skalieren von Ressourcen

Nachdem Sie Ihren Server erstellt haben, können Sie die virtuellen Kerne, den Tarif (mit Ausnahme eines Wechsels zu oder von Basic), die Speichermenge und den Aufbewahrungszeitraum für Sicherungen unabhängig voneinander ändern. Der Sicherungsspeichertyp kann nach der Servererstellung nicht mehr geändert werden. Die Anzahl virtueller Kerne kann zentral hoch- oder herunterskaliert werden. Die Aufbewahrungsdauer für Sicherungen kann von 7 bis zu 35 Tagen zentral hoch- oder herunterskaliert werden. Die Speichergröße kann nur erhöht werden. Die Skalierung der Ressourcen kann entweder über das Portal oder per Azure CLI durchgeführt werden. 

Beim Ändern der Anzahl von virtuellen Kernen oder des Tarifs wird eine Kopie des ursprünglichen Servers mit der neuen Computezuteilung erstellt. Sobald der neue Server betriebsbereit ist und ausgeführt wird, werden die Verbindungen auf den neuen Server verschoben. Während des Moments, in dem das System den Wechsel zum neuen Server durchführt, können keine neuen Verbindungen hergestellt werden, und für alle Transaktionen ohne Commit erfolgt ein Rollback. Die Länge dieses Zeitfensters variiert, aber normalerweise dauert der Vorgang nicht länger als eine Minute.

Das Skalieren des Speichers und das Ändern der Aufbewahrungsdauer für Sicherungen sind reine Onlinevorgänge. Es gibt keine Ausfallzeit, und Ihre Anwendung wird nicht beeinträchtigt. Da der IOPS-Wert in Abhängigkeit der Größe des bereitgestellten Speichers skaliert wird, können Sie die IOPS-Menge, die für Ihren Server verfügbar ist, durch das zentrale Hochskalieren des Speichers erhöhen.

## <a name="pricing"></a>Preise

Aktuelle Preisinformationen finden Sie auf der Seite [Azure-Datenbank für MySQL – Preise](https://azure.microsoft.com/pricing/details/mariadb/). Informationen zu den Kosten der gewünschten Konfiguration können Sie im [Azure-Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) anzeigen. Die monatlichen Kosten für die von Ihnen ausgewählten Optionen werden auf der Registerkarte **Tarif** angegeben. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie den Azure-Preisrechner verwenden, um einen geschätzten Preis zu erhalten. Wählen Sie auf der Website des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/) die Option **Elemente hinzufügen** aus, erweitern Sie die Kategorie **Datenbanken**, und wählen Sie **Azure Database for MariaDB** aus, um die Optionen anzupassen.

## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich über die [Beschränkungen](concepts-limits.md).
- Informieren Sie sich, wie Sie [im Azure-Portal einen MariaDB-Server erstellen](quickstart-create-mariadb-server-database-using-azure-portal.md).