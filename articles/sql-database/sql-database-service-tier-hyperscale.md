---
title: Übersicht zu Hyperscale in Azure SQL-Datenbank | Microsoft-Dokumentation
description: In diesem Artikel wird die Dienstebene „Hyperscale“ im vCore-basierten Kaufmodell in Azure SQL-Datenbank beschrieben. Zudem werden die Unterschiede zu den Dienstebenen „Universell“ und „Unternehmenskritisch“ erläutert.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 0fe098bd644762fb291eb623a7b41cd987c7fa26
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779188"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>Dienstebene „Hyperscale“ für bis zu 100TB

Azure SQL-Datenbank basiert auf der an die Cloudumgebung angepasste Architektur der SQL Server-Datenbank-Engine, um die Verfügbarkeit von 99,99 % selbst bei Infrastrukturausfällen sicherzustellen. In Azure SQL-Datenbank werden drei Architekturmodelle verwendet:
- Universell/Standard 
-  Hyperscale
-  Unternehmenskritisch/Premium

Die Dienstebene „Hyperscale“ in Azure SQL-Datenbank ist die neueste Dienstebene im vCore-basierten Kaufmodell. Diese Dienstebene bietet eine hochgradig skalierbare Speicher- und Computeleistung, mit der die Speicher- und Computeressourcen für eine Azure SQL-Datenbank-Instanz mithilfe der Azure-Architektur weit über die Limits der Dienstebenen „Universell“ und „Unternehmenskritisch“ hinaus horizontal hochskaliert werden können.

> 
> [!NOTE]
> Ausführliche Informationen zu den Dienstebenen „Universell“ und „Unternehmenskritisch“ im vCore-basierten Kaufmodell finden Sie in den Dienstebenen [Universell](sql-database-service-tier-general-purpose.md) und [Unternehmenskritisch](sql-database-service-tier-business-critical.md). Einen Vergleich zwischen vCore-basiertem Kaufmodell und DTU-basiertem Kaufmodell finden Sie unter [Kaufmodelle für Azure SQL-Datenbank und Ressourcen](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Welche Funktionen bietet Hyperscale?

Die Dienstebene „Hyperscale“ in Azure SQL-Datenbank bietet folgende zusätzliche Funktionen:

- Unterstützung für eine Datenbankgröße von bis zu 100 TB
- Nahezu sofortige Datenbanksicherungen (basierend auf in Azure Blob Storage gespeicherten Dateimomentaufnahmen) unabhängig von der Größe und ohne E/A-Auswirkung auf Compute   
- Schnelle Datenbankwiederherstellungen (basierend auf Dateimomentaufnahmen) in Minuten statt Stunden oder Tagen (kein von der Datengröße abhängiger Vorgang)
- Höhere Gesamtleistung aufgrund eines höheren Protokolldurchsatzes und schnellere Transaktionscommits unabhängig von Datenmengen
- Schnelle horizontale Hochskalierung: Sie können einen oder mehrere schreibgeschützte Knoten zur Abladung Ihrer Leseworkload und zur Verwendung als unmittelbar betriebsbereite Standbyserver bereitstellen.
- Schnelle zentrale Hochskalierung: Sie können Ihre Computeressourcen in konstanter Zeit zentral hochskalieren, um hohe Workloads nach Bedarf zu bewältigen, und anschließend wieder zentral herunterskalieren, sobald sie nicht mehr benötigt werden.

Die Dienstebene „Hyperscale“ beseitigt viele praktische Einschränkungen, die normalerweise für Clouddatenbanken gelten. Während die meisten anderen Datenbanken durch die auf einem einzelnen Knoten verfügbaren Ressourcen eingeschränkt werden, gelten in der Dienstebene „Hyperscale“ keine solchen Limits. Aufgrund der flexiblen Speicherarchitektur wächst der Speicher nach Bedarf. Hyperscale-Datenbanken werden ganz ohne Definition einer maximalen Größe erstellt. Eine Hyperscale-Datenbank wächst nach Bedarf – und Ihnen wird nur die tatsächlich verwendete Kapazität in Rechnung gestellt. Für leseintensive Workloads bietet die Dienstebene „Hyperscale“ eine schnelle horizontale Skalierung, indem nach Bedarf zusätzliche Lesereplikate zur Abladung von Leseworkloads bereitgestellt werden.

Darüber hinaus ist die Zeit, die zum Erstellen von Datenbanksicherungen oder zum zentralen Hoch- oder Herunterskalieren erforderlich ist, nicht mehr an die Menge der Daten in der Datenbank gebunden. Hyperscale-Datenbanken können praktisch sofort gesichert werden. Außerdem können Sie eine Datenbank in Minutenschnelle in der Größenordnung von zig Terabyte hoch- oder herunterskalieren. Durch diese Funktion müssen Sie nicht befürchten, durch Ihre Auswahl bei der Anfangskonfiguration eingeschränkt zu werden.

Weitere Informationen zu den Computegrößen für die Dienstebene „Hyperscale“ finden Sie unter [Merkmale der Dienstebene](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Wer sollte die Dienstebene „Hyperscale“ in Betracht ziehen?

Die Dienstebene „Hyperscale“ wurde hauptsächlich für Kunden konzipiert, die große lokale Datenbanken verwenden und ihre Anwendungen durch das Verschieben in die Cloud modernisieren möchten, oder für Kunden, die bereits die Cloud verwenden und durch die maximale Datenbankgröße (1–4 TB) eingeschränkt werden. Außerdem eignet er sich für Kunden, die eine hohe Leistung und Skalierbarkeit im Hinblick auf Speicher- und Computeressourcen wünschen.

Die Dienstebene „Hyperscale“ unterstützt alle SQL Server-Workloads, wurde aber in erster Linie für OLTP optimiert. Die Dienstebene „Hyperscale“ unterstützt auch Hybrid- und Analyseworkloads (Data Mart).

> [!IMPORTANT]
> Pools für elastische Datenbanken bieten für die Dienstebene „Hyperscale“ keine Unterstützung.

## <a name="hyperscale-pricing-model"></a>Preismodell für „Hyperscale“

Die Dienstebene „Hyperscale“ ist nur im [V-Kern-Modell](sql-database-service-tiers-vcore.md) verfügbar. In Anpassung an die neue Architektur ist das Preismodell etwas anders gestaltet als bei den Dienstebenen „Universell“ oder „Unternehmenskritisch“:

- **Compute**:

  Der Preis für Compute-Einheiten bei „Hyperscale“ ist pro Replikat. Der Preis für den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) wird automatisch auf Replikate mit Leseskalierung angewendet. Wir erstellen standardmäßig ein primäres Replikat und ein schreibgeschütztes Replikat pro Hyperscale-Datenbank.  Benutzer können die Gesamtanzahl der Replikate einschließlich des primären Replikats von 1 bis 5 anpassen.

- **Speicher**:

  Beim Konfigurieren einer Hyperscale-Datenbank müssen Sie keine maximale Datengröße angeben. Im Hyperscaletarif werden Gebühren für den Speicher Ihrer Datenbank basierend auf der tatsächlichen Nutzung berechnet. Speicher wird automatisch zwischen 10GB und 100TB in Schritten zugeordnet, die zwischen 10 und 40GB dynamisch angepasst werden.  

Weitere Informationen zu den Hyperscale-Preisen finden Sie unter [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="distributed-functions-architecture"></a>Architektur mit verteilten Funktionen

Im Gegensatz zu herkömmlichen Datenbank-Engines, in denen alle Datenverwaltungsfunktionen an einem Standort/in einem Prozess zentralisiert sind (selbst die so genannten verteilten Datenbanken in modernen Produktionsumgebung verfügen über mehrere Kopien einer monolithischen Daten-Engine), trennt eine Hyperscale-Datenbank die Abfrageverarbeitungs-Engine, in der die Semantik verschiedener Daten-Engines abweicht, von den Komponenten, die eine langfristige Speicherung und Dauerhaftigkeit für die Daten bereitstellen. Auf diese Weise kann die Speicherkapazität nahtlos nach Bedarf horizontal hochskaliert werden. (Das Anfangsziel ist 100 TB). Schreibgeschützte Replikate nutzen dieselben Speicherkomponenten gemeinsam, daher ist keine Datenkopie erforderlich, um ein neues lesbares Replikat zu starten. 

Das folgende Diagramm veranschaulicht die verschiedenen Typen von Knoten in einer Hyperscale-Datenbank:

![Architektur](./media/sql-database-hyperscale/hyperscale-architecture.png)

Eine Hyperscale-Datenbank enthält die folgenden verschiedenen Knotentypen:

### <a name="compute-node"></a>Computeknoten

Im Computeknoten befindet sich die relationale Engine und damit auch alle Sprachelemente, die Abfrageverarbeitung usw. Alle Benutzerinteraktionen mit einer Hyperscale-Datenbank erfolgen über diese Computeknoten. Computeknoten umfassen SSD-basierte Caches (mit der Bezeichnung „RBPEX – Resilient Buffer Pool Extension“ im obigen Diagramm), um die Anzahl von Netzwerkroundtrips zu minimieren, die zum Abrufen einer Datenseite erforderlich sind. Es gibt einen primären Computeknoten, in dem alle Lese-/Schreibworkloads und Transaktionen verarbeitet werden. Mindestens ein sekundärer Computeknoten fungiert als unmittelbar betriebsbereiter Standbyserver für Failoverzwecke sowie als schreibgeschützter Computeknoten für die Abladung von Leseworkloads (falls diese Funktion gewünscht ist).

### <a name="page-server-node"></a>Seitenserverknoten

Seitenserver sind Systeme, die eine horizontale hochskalierte Speicher-Engine darstellen.  Jeder Seitenserver ist für eine Teilmenge der Seiten in der Datenbank zuständig.  In der Regel steuert jeder Seitenserver zwischen 128GB und 1TB Daten. Es werden keine Daten auf mehreren Seitenservern gemeinsam genutzt (abgesehen von Replikaten, die für Redundanz und Verfügbarkeit gespeichert werden). Die Aufgabe eines Seitenservers besteht darin, Datenbankseiten nach Bedarf für die Computeknoten bereitzustellen und die Seiten auf dem neuesten Stand zu halten, wenn die Daten in Transaktionen aktualisiert werden. Seitenserver werden durch die Wiedergabe von Protokolldatensätzen vom Protokolldienst aktuell gehalten. Außerdem verwalten Seitenserver SSD-basierte Caches zum Verbessern der Leistung. Eine langfristige Speicherung von Datenseiten wird in Azure Storage beibehalten, um eine zusätzliche Zuverlässigkeit zu gewährleisten.

### <a name="log-service-node"></a>Protokolldienstknoten

Der Protokolldienstknoten akzeptiert Protokolldatensätze vom primären Computeknoten, speichert sie in einem permanenten Zwischenspeicher und leitet die Protokolldatensätze an die übrigen Computeknoten (damit sie ihre Caches aktualisieren können) sowie die entsprechenden Seitenserver weiter, damit die Daten dort aktualisiert werden können. Auf diese Weise werden alle Datenänderungen vom primären Computeknoten über den Protokolldienst an alle sekundären Computeknoten und Seitenserver weitergegeben. Schließlich werden die Protokolldatensätze in den langfristigen Speicher in Azure Storage übertragen. Hierbei handelt es sich um ein unbegrenztes Speicherrepository. Durch diesen Mechanismus entfällt die Notwendigkeit einer häufigen Protokollkürzung. Der Protokolldienst weist außerdem einen lokalen Cache auf, um den Zugriff zu beschleunigen.

### <a name="azure-storage-node"></a>Azure-Speicherknoten

Der Azure-Speicherknoten ist das endgültige Ziel von Daten aus den Seitenservern. Dieser Speicher wird zu Sicherungszwecken sowie für die Replikation zwischen Azure-Regionen verwendet. Sicherungen bestehen aus Momentaufnahmen von Datendateien. Wiederherstellungsvorgänge sind über diese Momentaufnahmen schnell durchzuführen, und die Daten können auf einen beliebigen Zeitpunkt wiederhergestellt werden.

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen

Sicherungen basieren auf Dateimomentaufnahmen und erfolgen daher nahezu sofort. Durch die Trennung von Speicher- und Computeressourcen kann der Sicherungs-/Wiederherstellungsvorgang auf die Speicherebene verlegt werden, um die Verarbeitungslast auf dem primären Computeknoten zu reduzieren. Daher wird die Leistung der primären Computeknotens durch die Sicherung einer großen Datenbank nicht beeinträchtigt. Ebenso erfolgen Wiederherstellungen durch das Kopieren der Dateimomentaufnahme stellen daher keinen umfangreichen Datenvorgang dar. Bei Wiederherstellungen innerhalb desselben Speicherkontos kann der Wiederherstellungsvorgang schnell durchgeführt werden.

## <a name="scale-and-performance-advantages"></a>Skalierungs- und Leistungsvorteile

Mit der Möglichkeit, weitere Computeknoten schnell hoch- bzw. herunterzufahren, erlaubt die Hyperscale-Architektur eine erhebliche horizontale Leseskalierung und kann außerdem den primären Computeknoten freigeben, um weitere Schreibanforderungen zu verarbeiten. Darüber hinaus können die Computeknoten aufgrund der Hyperscale-Architektur mit freigegebenem Speicher schnell zentral hoch- oder herunterskaliert werden.

## <a name="create-a-hyperscale-database"></a>Erstellen einer Hyperscale-Datenbank

Eine Hyperscale-Datenbank kann über das [Azure-Portal](https://portal.azure.com) oder mit [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) oder [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) erstellt werden. Hyperscale-Datenbanken stehen nur bei Verwendung des [vCore-basierten Kaufmodells](sql-database-service-tiers-vcore.md) zur Verfügung.

Mit dem folgenden T-SQL-Befehl wird eine Hyperscale-Datenbank erstellt. Sie müssen sowohl die Edition als auch das Dienstziel in der `CREATE DATABASE`-Anweisung angeben.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Dadurch wird eine Hyperscale-Datenbank auf Gen5-Hardware mit 4 Kernen erstellt.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrieren vorhandener Azure SQL-Datenbank-Instanzen zur Dienstebene „Hyperscale“

Sie können Ihre vorhandenen Azure SQL-Datenbank-Instanzen über das [Azure-Portal](https://portal.azure.com) oder mit [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) oder [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) zu „Hyperscale“ verschieben. Dies erfolgt zurzeit im Rahmen einer unidirektionalen Migration. Datenbanken können nicht von Hyperscale zu einer anderen Dienstebene migriert werden. Es wird empfohlen, eine Kopie Ihrer Produktionsdatenbanken zu erstellen und für Proof of Concepts (POCs) eine Migration zu Hyperscale durchzuführen.

Mit dem folgenden T-SQL-Befehl wird eine Datenbank in die Dienstebene „Hyperscale“ verschoben. Sie müssen sowohl die Edition als auch das Dienstziel in der `ALTER DATABASE`-Anweisung angeben.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Herstellen einer Verbindung mit einem schreibgeschützten Replikat einer Hyperscale-Datenbank

Bei Hyperscale-Datenbanken bestimmt das Argument `ApplicationIntent` in der vom Client bereitgestellten Verbindungszeichenfolge, ob eine Verbindung an das Replikat mit Schreibzugriff oder an ein schreibgeschütztes sekundäres Replikat weitergeleitet wird. Wenn `ApplicationIntent` auf `READONLY` festgelegt ist und die Datenbank kein sekundäres Replikat aufweist, wird die Verbindung an das primäre Replikat weitergeleitet und weist standardmäßig das `ReadWrite`-Verhalten auf.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Notfallwiederherstellung für Hyperscale-Datenbanken
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Wiederherstellen einer Hyperscale-Datenbank in einem anderen geografischen Gebiet
Wenn Sie im Rahmen der Wiederherstellung im Notfall oder einer Übung, wegen eines Umzugs oder aus beliebigem anderen Grund eine Hyperskalierung für Azure SQL-Datenbank-Instanz in einer anderen Region wiederherstellen müssen als der, wo sie derzeit gehostet wird, besteht die primäre Methode in einer Geowiederherstellung der Datenbank.  Dies beinhaltet genau die gleichen Schritte, als würden Sie eine beliebige AZURE SQL DB-Instanz in einer anderen Region wiederherstellen:
1. Erstellen Sie eine SQL-Datenbank-Serverinstanz in der Zielregion, wenn Sie dort noch keinen geeigneten Server haben.  Dieser Server muss zu demselben Abonnement wie der ursprüngliche Server (Quelle) gehören.
2. Befolgen Sie die Anweisungen im Thema [Geowiederherstellung](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) der Seite zum Wiederherstellen von Azure SQL-Datenbank-Instanzen aus automatischen Sicherungen.

#### <a name="notes-on-geo-restores-of-a-hyperscale-database"></a>Hinweise zur Geowiederherstellung einer Hyperscale-Datenbank
Weil Quelle und Ziel sich in unterschiedlichen Regionen befinden, kann die Datenbank nicht wie in Nicht-Geowiederherstellungen Momentaufnahmenspeicher mit der Quelldatenbank gemeinsam nutzen, was zu einem sehr schnellen Abschluss führt.  Bei einer Geowiederherstellung einer Hyperscale-Datenbank werden auch dann Anpassungen des Datenumfangs durchgeführt, wenn das Ziel sich in der gekoppelten Region des georeplizierten Speichers befindet.  Dies bedeutet, dass die für eine Geowiederherstellung erforderliche Zeit zur Größe der wiederhergestellten Datenbank proportional ist.  Wenn das Ziel in der gekoppelten Region ist, befindet sich die Kopie in einem Rechenzentrum, was wesentlich schneller ist als eine Langstreckenkopie über das Internet, aber es werden immer noch alle Bits kopiert.

## <a name=regions></a>Verfügbare Regionen

Die Hyperskalierung für Azure SQL-Datenbank-Ebene ist zurzeit in den folgenden Regionen verfügbar:

- Australien (Osten)
- Australien, Südosten
- Brasilien Süd
- Kanada, Mitte
- USA (Mitte)
- China, Osten 2
- China, Norden 2
- Asien, Osten
- USA (Ost)
- USA (Ost) 2
- Frankreich, Mitte
- Japan, Osten
- Japan, Westen
- Korea, Mitte
- Korea, Süden
- USA Nord Mitte
- Nordeuropa
- Südafrika, Norden
- USA Süd Mitte
- Asien, Südosten
- UK, Süden
- UK, Westen
- Europa, Westen
- USA (Westen)
- USA, Westen 2

Wenn Sie eine Hyperscale-Datenbank in einer Region erstellen möchten, die nicht als unterstützt aufgelistet ist, können Sie eine Onboardinganforderung über das Azure-Portal senden. Wir arbeiten daran, die Liste der unterstützten Regionen zu erweitern, also nutzen Sie stets die aktuelle Liste der Regionen.

So fordern Sie die Fähigkeit zum Erstellen von Hyperscale-Datenbanken in nicht aufgelisteten Regionen an:

1. Navigieren Sie zum Azure-Blatt [Hilfe und Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

2. Klicken Sie auf [**Neue Supportanfrage**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ![Azure-Blatt „Hilfe und Support“](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. Wählen Sie unter **Problemtyp** den Eintrag **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.

4. Wählen Sie das Abonnement aus, das Sie zum Erstellen der Datenbank(en) verwenden möchten.

5. Wählen Sie unter **Kontingenttyp** den Eintrag **SQL-Datenbank** aus.

6. Klicken Sie auf **Weiter: Lösungen**.

1. Klicken Sie auf **Details angeben**.

    ![Problemdetails](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. Wählen Sie **Kontingenttyp für SQL-Datenbank** aus: **Andere Kontingentanforderung**.

9. Füllen Sie die folgende Vorlage aus:

    ![Kontingentdetails](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    Geben Sie in der Vorlage die folgenden Informationen ein:

    > Anforderung zum Erstellen einer Hyperskalierung für Azure SQL-Datenbank-Instanz in einer neuen Region<br/> Region: [Geben Sie die angeforderte Region ein]  <br/>
    > Compute-SKU/Gesamtzahl der Kerne einschließlich lesbarer Replikate <br/>
    > Geschätzte TB-Anzahl 
    >

10. Wählen Sie **Schweregrad C** aus.

11. Wählen Sie die entsprechende Kontaktmethode aus, und geben Sie die Details ein.

12. Klicken Sie auf **Speichern** und **Fortfahren**.

## <a name="known-limitations"></a>Bekannte Einschränkungen
Hierbei handelt es sich um die aktuellen Einschränkungen der Hyperscale-Dienstebene bezüglich der GA.  Wir arbeiten daran, möglichst viele dieser Einschränkungen zu entfernen.

| Problem | BESCHREIBUNG |
| :---- | :--------- |
| Im Bereich „Sicherungen verwalten“ für einen logischen Server werden Hyperscale-Datenbanken nicht angezeigt und vom SQL-Server gefiltert->  | Hyperscale verfügt über eine separate Methode zum Verwalten von Sicherungen, sodass die Einstellungen für langfristige Aufbewahrung und Aufbewahrung von Point-in-Time-Sicherungen nicht gelten/ungültig werden. Deshalb werden Hyperscale-Datenbanken nicht im Bereich „Sicherungen verwalten“ angezeigt. |
| Point-in-Time-Wiederherstellung | Nachdem eine Datenbank zur Dienstebene „Hyperscale“ migriert wurde, wird die Wiederherstellung des Zustands zu einem bestimmten Zeitpunkt vor der Migration nicht unterstützt.|
| Wiederherstellung einer Nicht-Hypserscale-DB in einer Hypserscale-DB und umgekehrt | Sie können weder eine Hyperscale-Datenbank in einer Nicht-Hyperscale-Datenbank noch eine Nicht-Hyperscale-Datenbank in einer Hyperscale-Datenbank wiederherstellen.|
| Wenn eine Datenbankdatei während der Migration aufgrund einer aktiven Workload anwächst und den Grenzwert von 1 TB pro Datei überschreitet, kann die Migration nicht ausgeführt werden | Gegenmaßnahmen: <br> – Migrieren Sie die Datenbank möglichst zu einem Zeitpunkt, wenn keine Aktualisierungsworkload ausgeführt wird.<br> – Versuchen Sie erneut, die Migration auszuführen. Dies ist erfolgreich, solange der Grenzwert von 1 TB während der Migration nicht überschritten wird.|
| Verwaltete Instanz | Die verwaltete Azure SQL-Datenbank-Instanz wird bei Hyperscale-Datenbanken derzeit nicht unterstützt. |
| Pools für elastische Datenbanken |  Pools für elastische Datenbanken werden mit Hyperskalierung für SQL-Datenbank derzeit nicht unterstützt.|
| Migration zu „Hyperscale“ ist derzeit ein unidirektionaler Vorgang | Nach der Migration einer Datenbank zu „Hyperscale“ kann sie nicht direkt zu einer anderen Dienstebene migriert werden. Derzeit besteht die einzige Möglichkeit zum Migrieren einer Datenbank von „Hyperscale“ zu einem anderen Diensttarif im Exportieren/Importieren mithilfe einer BACPAC-Datei.|
| Migration von Datenbanken mit In-Memory-Objekten | In-Memory-Objekte müssen gelöscht und als Nicht-In-Memory-Objekte neu erstellt werden, bevor eine Datenbank zur Dienstebene „Hyperscale“ migriert wird.|
| Datenänderungsverfolgung | Sie können die Datenänderungsverfolgung bei Hyperscale-Datenbanken nicht verwenden. |
| Georeplikation  | Sie können noch keine Georeplikation für Hyperskalierung für Azure SQL-Datenbank-Instanzen konfigurieren.  Sie können Geowiederherstellungen ausführen (Wiederherstellen der Datenbank in einem anderen geografischen Gebiet, für die Notfallwiederherstellung oder aus anderen Gründen). |
| TDE/AKV-Integration | Transparent Data Encryption mit Azure Key Vault (häufig als Bring-Your-Own-Key oder BYOK bezeichnet) wird noch nicht für Hyperskalierung für Azure SQL-Datenbank-Instanzen unterstützt, TDE mit vom Dienst verwalteten Schlüsseln jedoch vollständig. |
|Intelligente Datenbankfeatures | 1. Create Index-, Drop Index-Ratgebermodelle werden nicht für Hyperscale-Datenbanken trainiert. <br/>2. Schemaprobleme, DbParameterization – vor kurzem hinzugefügte Ratgeber werden für Hyperscale-Datenbanken nicht unterstützt.|



## <a name="next-steps"></a>Nächste Schritte

- Häufig gestellte Fragen zu diesem Thema finden Sie unter [Häufig gestellte Fragen zu Hyperscale](sql-database-service-tier-hyperscale-faq.md).
- Informationen zu Dienstebenen finden Sie unter [Dienstebenen](sql-database-service-tiers.md).
- Informationen zu Grenzwerten auf Server- und Abonnementebene finden Sie unter [Übersicht über Ressourcenlimits auf einem logischen Server](sql-database-resource-limits-logical-server.md).
- Informationen zu Einschränkungen des Kaufmodells für eine Einzeldatenbank finden Sie unter [Limits des vCore-basierten Kaufmodells für eine Einzeldatenbank in Azure SQL-Datenbank](sql-database-vcore-resource-limits-single-databases.md).
- Eine Liste der Features und einen Funktionsvergleich finden Sie unter [Allgemeine SQL-Features](sql-database-features.md).
