---
title: Serverlose Computeebene
description: In diesem Artikel wird die neue serverlose Computeebene beschrieben und mit der vorhandenen bereitgestellten Computeebene für Azure SQL-Datenbank verglichen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 12/8/2020
ms.openlocfilehash: bd8f5a28b709a45e99e846fb4e242f774aca80c5
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96902509"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL-Datenbank – Serverlos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

„Serverless“ ist ein Computetarif für Einzeldatenbanken in Azure SQL-Datenbank, bei dem Computeressourcen basierend auf dem Workloadbedarf automatisch skaliert werden und die Nutzung dieser Ressourcen sekundengenau abrechnet wird. Wenn nur der verwendete Speicher in Rechnung gestellt wird, hält die serverlose Computeebene außerdem Datenbanken während inaktiver Zeiträume automatisch an und startet diese wieder, wenn es wieder zu Aktivität kommt.

## <a name="serverless-compute-tier"></a>Serverlose Computeebene

Die serverlose Computeebene für Singletons in Azure SQL-Datenbank wird durch einen automatischen Computeskalierungsbereich und eine Verzögerung durch automatisches Anhalten parametrisiert. Die Konfiguration dieser Parameter beeinflusst die Leistung und die Computekosten der Datenbank.

![Abrechnung – serverlos](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Leistungskonfiguration

- Die **Mindestanzahl virtueller Kerne** und die **maximale Anzahl virtueller Kerne** sind konfigurierbare Parameter, die den Bereich der Computekapazität definieren, die für die Datenbank verfügbar ist. Arbeitsspeicher- und E/A-Limits sind proportional zum angegebenen V-Kern-Bereich.  
- Die **Verzögerung durch automatisches Anhalten** ist ein konfigurierbarer Parameter, der die Zeitspanne definiert, für die die Datenbank inaktiv sein muss, bevor sie automatisch pausiert wird. Bei der nächsten Anmeldung oder einer anderen Aktivität wird die Ausführung der Datenbank automatisch fortgesetzt.  Alternativ kann das automatische Anhalten deaktiviert werden.

### <a name="cost"></a>Kosten

- Die Kosten für eine serverlose Datenbank setzen sich aus der Summe der Computekosten und der Speicherkosten zusammen.
- Wenn die Computenutzung zwischen dem minimal und dem maximal konfigurierten Grenzwert liegt, basieren die Computekosten auf den verwendeten virtuellen Kernen und dem verwendeten Speicher.
- Wenn die Computenutzung unter dem minimal konfigurierten Grenzwert liegt, basieren die Computekosten auf der konfigurierten Mindestanzahl an virtuellen Kernen und Speicher.
- Wenn die Datenbank angehalten wird, fallen keine Computekosten an, und es wird nur der verwendete Speicher berechnet.
- Die Speicherkosten werden auf die gleiche Weise berechnet wie in der bereitgestellten Computeebene.

Weitere Informationen finden Sie unter [Abrechnung](serverless-tier-overview.md#billing).

## <a name="scenarios"></a>Szenarien

Serverlos ist preis-/leistungsoptimiert für Einzeldatenbanken mit zeitweiligen, unvorhersehbaren Nutzungsmustern, bei denen eine gewisse Verzögerung in der Compute-Aufwärmphase nach Leerlaufzeiträumen ohne Nutzung akzeptabel ist. Die bereitgestellte Computeebene ist dagegen preis-/leistungsoptimiert für Einzeldatenbanken oder Datenbanken in Pools für elastische Datenbanken mit höherer durchschnittlicher Nutzung, bei denen eine Verzögerung in der Compute-Aufwärmphase nicht akzeptabel ist.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Ideal geeignete Szenarien für serverloses Computing

- Einzeldatenbanken mit wechselnden, unvorhersehbaren Nutzungsmustern, Perioden der Inaktivität und geringerer durchschnittlicher Computenutzung im Zeitverlauf.
- Einzeldatenbanken in der bereitgestellten Computeebene, die häufig neu skaliert werden, und Kunden, die die Neuskalierung des Computings an den Dienst delegieren möchten.
- Neue Einzeldatenbanken ohne Nutzungsverlauf, bei denen das Schätzen der Computegröße vor der Bereitstellung in SQL-Datenbank schwierig oder gar nicht möglich ist.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Ideal geeignete Szenarien für bereitgestelltes Computing

- Einzeldatenbanken mit regelmäßigeren, vorhersagbaren Nutzungsmustern und höherer durchschnittlicher Computenutzung im Zeitverlauf.
- Datenbanken, die keine Leistungskompromisse durch häufigeres Begrenzen des Speichers oder Verzögerung beim automatischen Fortsetzen aus dem angehaltenen Zustand tolerieren können.
- Mehrere Datenbanken mit wechselnden, unvorhersehbaren Nutzungsmustern, die für bessere Preis-/Leistungsoptimierung in Pools für elastische Datenbanken konsolidiert werden können.

## <a name="comparison-with-provisioned-compute-tier"></a>Vergleich mit der bereitgestellten Computeebene

Die folgende Tabelle enthält eine Zusammenfassung der Unterschiede zwischen der serverlosen Computeebene und der bereitgestellten Computeebene:

| | **Serverloses Computing**: | **Bereitgestelltes Computing** |
|:---|:---|:---|
|**Datenbanknutzungsmuster**| Wechselnde, unvorhersehbare Nutzung mit niedrigerer durchschnittlicher Computenutzung im Zeitverlauf. | Regelmäßigere Nutzungsmuster mit höherer durchschnittlicher Computenutzung im Zeitverlauf oder mehrere Datenbanken, die Pools für elastische Datenbanken verwenden.|
| **Aufwand bei der Leistungsverwaltung** |Geringer|Höher|
|**Compute-Skalierung**|Automatic|Manuell|
|**Compute-Reaktionsfähigkeit**|Geringer nach Inaktivitätszeiträumen|Unmittelbar|
|**Granularität bei der Abrechnung**|Pro Sekunde|Pro Stunde|

## <a name="purchasing-model-and-service-tier"></a>Kaufmodell und Dienstebene

„SQL-Datenbank – serverlos“ wird derzeit nur von der Ebene „Universell“ auf Hardware der Generation 5 im vCore-basierten Kaufmodell unterstützt.

## <a name="autoscaling"></a>Automatische Skalierung

### <a name="scaling-responsiveness"></a>Reaktionsfähigkeit hinsichtlich der Skalierung

Im Allgemeinen werden serverlose Datenbanken auf einem Computer mit ausreichender Kapazität zum unterbrechungsfreien Erfüllen des Ressourcenbedarfs für beliebige Volumen von angeforderten Computeressourcen innerhalb der Grenzen unterstützt, die durch den Wert für die maximale Anzahl virtueller Kerne festgelegt sind. Gelegentlich tritt automatisch ein Lastenausgleich auf, wenn der Computer den Ressourcenbedarf nicht innerhalb weniger Minuten erfüllen kann. Beispiel: Wenn vier virtuelle Kerne benötigt werden, aber nur zwei virtuelle Kerne verfügbar sind, dauert es unter Umständen einige Minuten, bis ein Lastausgleich vorgenommen wurde und vier virtuelle Kerne bereitgestellt werden. Die Datenbank bleibt während des Lastenausgleichs online, mit Ausnahme einer kurzen Zeitspanne am Schluss des Vorgangs, wenn Verbindungen verworfen werden.

### <a name="memory-management"></a>Speicherverwaltung

Arbeitsspeicher für serverlose Datenbanken wird häufiger als bei bereitgestellten Computedatenbanken freigegeben. Dieses Verhalten ist wichtig, um für serverlose Datenbanken die Kosten kontrollieren zu können, und es kann mit einer Beeinträchtigung der Leistung verbunden sein.

#### <a name="cache-reclamation"></a>Freigabe von Cache

Im Gegensatz zu bereitgestellten Computedatenbanken wird der Speicher aus dem SQL-Cache von einer serverlosen Datenbank freigegeben, wenn eine geringe CPU-Auslastung vorliegt oder der Cache kaum aktiv genutzt wird.

- Die aktive Cachenutzung gilt als niedrig, wenn die Gesamtgröße der zuletzt verwendeten Cacheeinträge für einen bestimmten Zeitraum unter einen Schwellenwert fällt.
- Beim Auslösen der Cachefreigabe wird die Größe des Zielspeichers inkrementell auf einen Bruchteil der vorherigen Größe reduziert, und der Freigabevorgang wird nur fortgesetzt, wenn die Auslastung niedrig bleibt.
- Während der Cachefreigabe entspricht die Richtlinie für die Auswahl der zu entfernenden Cacheeinträge der Auswahlrichtlinie für bereitgestellte Computedatenbanken bei hoher Speicherauslastung.
- Der Cache wird niemals auf eine Größe verkleinert, die unter der Mindestgröße für den Arbeitsspeicher liegt. Dies wird über die Mindestanzahl von virtuellen Kernen definiert und kann entsprechend konfiguriert werden.

Sowohl in serverlosen als auch in bereitgestellten Computedatenbanken können Cacheeinträge entfernt werden, wenn der gesamte verfügbare Arbeitsspeicher verwendet wird.

Beachten Sie, dass die aktive Cachenutzung je nach Verwendungsmuster trotz geringer CPU-Auslastung hoch bleiben und die Speicherfreigabe verhindern kann.  Außerdem kann es nach Ende der Benutzeraktivität zu einer zusätzlichen Verzögerung kommen, bevor die Speicherfreigabe aufgrund von periodischen Hintergrundprozessen erfolgt, die auf vorherige Benutzeraktivitäten reagieren.  Beispielsweise werden bei Löschvorgängen und QDS-Cleanuptasks inaktive Datensätze generiert, die zum Löschen markiert sind. Physisch werden sie jedoch erst gelöscht, wenn der inaktive Cleanupprozess inaktiver Datensätze ausgeführt wird, der das Lesen von Datenseiten in den Cache umfassen kann.

#### <a name="cache-hydration"></a>Cachehydration

Der SQL-Cache wächst an, während Daten auf die gleiche Weise und mit der gleichen Geschwindigkeit wie für bereitgestellte Datenbanken vom Datenträger abgerufen werden. Wenn die Datenbank ausgelastet ist, kann die Größe des Caches uneingeschränkt bis zum maximalen Arbeitsspeichergrenzwert zunehmen.

## <a name="autopausing-and-autoresuming"></a>Automatisches Anhalten und automatisches Fortsetzen

### <a name="autopausing"></a>Automatisches Anhalten

Das automatische Anhalten wird ausgelöst, wenn die folgenden Bedingungen für die Dauer der Verzögerung für automatisches Anhalten erfüllt sind:

- Anzahl der Sitzungen = 0
- CPU = 0 für Benutzerworkload im Benutzerpool

Es ist eine Option verfügbar, mit der das automatische Anhalten ggf. deaktiviert werden kann.

Die folgenden Features unterstützen nicht das automatische Anhalten, sondern nur die automatische Skalierung.  Bei Verwendung eines der folgenden Features sollte das automatische Anhalten deaktiviert werden, und die Datenbank bleibt online (ungeachtet der Dauer der Inaktivität der Datenbank):

- Georeplikation (aktive Georeplikation und Gruppen für automatisches Failover).
- Langzeitaufbewahrung (Long-Term Retention, LTR) von Sicherungen.
- In SQL-Datensynchronisierung verwendete Synchronisierungsdatenbank  Im Gegensatz zu Synchronisierungsdatenbanken unterstützen Hub-Datenbanken und Mitgliedsdatenbanken das automatische Anhalten.
- DNS-Aliasing
- die in elastischen Aufträgen (Vorschauversion) verwendete Auftragsdatenbank

Das automatische Anhalten wird während der Bereitstellung bestimmter Dienstupdates vorübergehend verhindert, die erfordern, dass die Datenbank online ist.  In solchen Fällen ist das automatische Anhalten wieder zulässig, sobald das Dienstupdate abgeschlossen ist.

### <a name="autoresuming"></a>Automatisches Fortsetzen

Das automatische Fortsetzen wird ausgelöst, wenn eine der folgenden Bedingungen erfüllt ist:

|Funktion|Trigger für automatisches Fortsetzen|
|---|---|
|Authentifizierung und Autorisierung|Anmeldename|
|Bedrohungserkennung|Aktivieren/Deaktivieren von Einstellungen für die Bedrohungserkennung auf der Datenbank- oder Serverebene.<br>Ändern von Einstellungen für die Bedrohungserkennung auf der Datenbank- oder Serverebene.|
|Datenermittlung und -klassifizierung|Hinzufügen, Ändern, Löschen oder Anzeigen von Vertraulichkeitsbezeichnungen|
|Überwachung|Anzeigen von Überwachungsdatensätzen.<br>Aktualisieren oder Anzeigen von Überwachungsrichtlinien.|
|Datenmaskierung|Hinzufügen, Ändern, Löschen oder Anzeigen von Datenmaskierungsregeln|
|Transparent Data Encryption|Anzeigen des Status der transparenten Datenverschlüsselung|
|Sicherheitsrisikobewertung|Ad-hoc-Scans und periodische Scans, falls aktiviert|
|Abfragedatenspeicher (Leistung)|Ändern oder Anzeigen von Abfragespeichereinstellungen|
|Empfehlungen zur Leistung|Anzeigen oder Anwenden von Empfehlungen zur Leistung|
|Automatische Optimierung|Anwendung und Überprüfung von Empfehlungen für die automatische Optimierung, z.B. die automatische Indizierung|
|Kopieren von Datenbanken|Erstellen von Datenbanken als Kopie.<br>Exportieren in eine BACPAC-Datei.|
|SQL-Datensynchronisierung|Die Synchronisierung zwischen Hub- und Mitgliedsdatenbanken, die nach einem konfigurierbaren Zeitplan oder manuell ausgeführt werden|
|Ändern bestimmter Datenbankmetadaten|Hinzufügen von neuen Datenbanktags.<br>Ändern der Mindest- und Höchstwerte für virtuelle Kerne oder der Verzögerung für das automatische Anhalten.|
|SQL Server Management Studio (SSMS)|Durch Verwenden von SSMS-Versionen vor 18.1 und Öffnen eines neuen Abfragefensters für eine Datenbank auf dem Server wird jede automatisch angehaltene Datenbank auf dem betreffenden Server fortgesetzt. Dieses Verhalten tritt nicht auf, wenn mindestens Version 18.1 von SSMS verwendet wird.|

Überwachung und Verwaltung sowie andere Lösungen, die einen der oben aufgeführten Vorgänge ausführen, lösen eine automatische Fortsetzung aus.

Das automatische Fortsetzen wird ebenfalls während der Bereitstellung bestimmter Dienstupdates ausgelöst, die erfordern, dass die Datenbank online ist.

### <a name="connectivity"></a>Konnektivität

Wenn eine serverlose Datenbank angehalten wird, wird die Datenbank bei der ersten Anmeldung fortgesetzt, und es wird ein Fehler (Fehlercode 40613) mit dem Hinweis zurückgegeben, dass die Datenbank nicht verfügbar ist. Sobald die Datenbank fortgesetzt wird, muss die Anmeldung wiederholt werden, um die Verbindung herzustellen. Datenbankclients mit Wiederholungslogik für Verbindungen dürfen nicht geändert werden.

### <a name="latency"></a>Latency

Die Wartezeit für das automatische Fortsetzen und das automatische Anhalten einer serverlosen Datenbank liegt normalerweise im Bereich von 1 für das automatische Fortsetzen und zwischen 1 und 10 Minuten für das automatische Anhalten.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Vom Kunden verwaltete transparente Datenverschlüsselung (BYOK)

Falls die [vom Kunden verwaltete transparente Datenverschlüsselung](transparent-data-encryption-byok-overview.md) (BYOK) verwendet und die serverlose Datenbank automatisch angehalten wird, wenn ein Schlüssel gelöscht oder widerrufen wird, verbleibt die Datenbank im automatisch angehaltenen Zustand.  Nach dem nächsten Fortsetzen der Datenbank kann in diesem Fall innerhalb von ungefähr 10 Minuten nicht mehr auf die Datenbank zugegriffen werden.  Sobald der Zugriff auf die Datenbank nicht mehr möglich ist, ist der Wiederherstellungsvorgang identisch mit dem für bereitgestellte Computedatenbanken.  Wenn die serverlose Datenbank zum Zeitpunkt des Löschens oder Widerrufens von Schlüsseln online ist, ist der Zugriff auf die Datenbank auch nach etwa 10 Minuten nicht mehr möglich, wie dies auch bei bereitgestellten Computedatenbanken der Fall ist.

## <a name="onboarding-into-serverless-compute-tier"></a>Integration in die serverlose Computeebene

Beim Erstellen einer neuen Datenbank bzw. Verschieben einer vorhandenen Datenbank in eine serverlose Computeebene gilt dasselbe Muster wie beim Erstellen einer neuen Datenbank in der bereitgestellten Computeebene. Dieser Vorgang umfasst die folgenden zwei Schritte:

1. Geben Sie das Dienstziel an. Das Dienstziel schreibt die Dienstebene, die Hardwaregeneration und die maximale Anzahl von virtuellen Kernen vor. Weitere Informationen zu Optionen für Dienstziele finden Sie unter [Limits für serverlose Ressourcen](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Geben Sie optional die Mindestanzahl virtueller Kerne und die Verzögerung für das automatische Anhalten an, um deren Standardwerte zu ändern. In der folgenden Tabelle werden die verfügbaren Werte für diese Parameter aufgeführt.

   |Parameter|Auswahlmöglichkeiten für Werte|Standardwert|
   |---|---|---|---|
   |Mindestanzahl virtueller Kerne|Hängt von den konfigurierten maximalen virtuellen Kernen ab (siehe [Ressourceneinschränkungen](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)).|0,5 V-Kerne|
   |Verzögerung für das automatische Anhalten|Minimum: 60 Minuten (1 Stunde)<br>Maximum: 10.080 Minuten (sieben Tage)<br>Inkremente: 10 Minuten<br>Automatisches Anhalten deaktivieren: -1|60 Minuten|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Erstellen einer neuen Datenbank in der serverlosen Computeebene

Die folgenden Beispiele erstellen eine neue Datenbank in der serverlosen Computeebene.

#### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Einzeldatenbank in Azure SQL-Datenbank über das Azure-Portal](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>Verwenden von PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Verwenden von Transact-SQL (T-SQL)

Bei Verwendung von T-SQL werden Standardwerte für die Mindestanzahl virtueller Kerne und die automatische Pausenverzögerung angewendet.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Weitere Informationen finden Sie unter [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Verschieben einer Datenbank aus der bereitgestellten Computeebene in die serverlose Computeebene

In den folgenden Beispielen wird eine Datenbank aus der bereitgestellten Computeebene in die serverlose Computeebene verschoben.

#### <a name="use-powershell"></a>Verwenden von PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Verwenden von Transact-SQL (T-SQL)

Bei Verwendung von T-SQL werden Standardwerte für die Mindestanzahl virtueller Kerne und die automatische Pausenverzögerung angewendet.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Weitere Informationen finden Sie unter [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Verschieben einer Datenbank aus der serverlosen Computeebene in die bereitgestellte Computeebene

Eine serverlose Datenbank kann auf die gleiche Weise in eine bereitgestellte Computeebene verschoben werden wie eine bereitgestellte Datenbank in eine serverlose Computeebene.

## <a name="modifying-serverless-configuration"></a>Ändern der serverlosen Konfiguration

### <a name="use-powershell"></a>Verwenden von PowerShell

Führen Sie zum Ändern der Ober- oder Untergrenze für V-Kerne sowie der Verzögerung für das automatische Anhalten den PowerShell-Befehl [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) mit den Argumenten `MaxVcore`, `MinVcore` und `AutoPauseDelayInMinutes` aus.

### <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

Führen Sie zum Ändern der Ober- oder Untergrenze für V-Kerne sowie der Verzögerung für das automatische Anhalten den Azure CLI-Befehl [az sql db update](/cli/azure/sql/db#az-sql-db-update) mit den Argumenten `capacity`, `min-capacity` und `auto-pause-delay` aus.


## <a name="monitoring"></a>Überwachung

### <a name="resources-used-and-billed"></a>Genutzte und berechnete Ressourcen

Die Ressourcen einer serverlosen Datenbank werden von App-Paket, SQL-Instanz und Benutzerressourcenpool-Entitäten gekapselt.

#### <a name="app-package"></a>App-Paket

Das App-Paket ist die „Außengrenze“ der Ressourcenverwaltung für eine Datenbank, wobei es keine Rolle spielt, ob sich die Datenbank auf einer serverlosen oder einer bereitgestellten Computeebene befindet. Das App-Paket enthält die SQL-Instanz und externe Dienste, die zusammen sämtliche Benutzer- und Systemressourcen umfassen, die von einer Datenbank in SQL-Datenbank genutzt werden. Beispiele für externe Dienste sind R und die Volltextsuche. Die SQL-Instanz bestimmt generell die allgemeine Ressourcennutzung für das gesamte App-Paket.

#### <a name="user-resource-pool"></a>Benutzerressourcenpool

Der Benutzerressourcenpool ist die „Innengrenze“ der Ressourcenverwaltung für eine Datenbank, wobei es keine Rolle spielt, ob sich die Datenbank auf einer serverlosen oder einer bereitgestellten Computeebene befindet. Der Benutzerressourcenpool beschränkt CPU und E/A für Benutzerworkload, die von DDL-Abfragen (z.B. CREATE und ALTER) und DML-Abfragen (z.B. SELECT, INSERT, UPDATE und DELETE) generiert wird. Diese Abfragen sind im Allgemeinen für den Großteil der Auslastung des App-Pakets verantwortlich.

### <a name="metrics"></a>Metriken

Metriken für die Überwachung des Ressourcenverbrauchs des App-Pakets und Benutzerpools einer serverlosen Datenbank sind in der folgenden Tabelle aufgeführt:

|Entität|Metrik|BESCHREIBUNG|Units|
|---|---|---|---|
|App-Paket|app_cpu_percent|Prozentsatz der von der App genutzten virtuellen Kerne, bezogen auf die maximal zulässigen virtuellen Kerne für die App.|Prozentwert|
|App-Paket|app_cpu_billed|Die Menge der Computeressourcen, die im Berichtszeitraum für die App abgerechnet wurden. Der während dieses Zeitraums zu zahlende Betrag ist das Produkt aus dieser Metrik und dem Einzelpreis für virtuelle Kerne. <br><br>Werte dieser Metrik werden bestimmt, indem der maximal genutzte Arbeitsspeicher und der pro Sekunde genutzte Speicher über einen Zeitraum aggregiert werden. Liegt die genutzte Menge unter der bereitgestellten Mindestmenge (festgelegt durch Mindestanzahl virtueller Kerne und Minimalwert für Speicher), wird die bereitgestellte Mindestmenge berechnet. Der Arbeitsspeicher wird in Einheiten aus virtuellen Kernen normalisiert, indem der Arbeitsspeicher in GB nach 3 GB pro virtuellem Kern neu skaliert wird. So kann die CPU bei der Abrechnung mit dem Arbeitsspeicher verglichen werden.|Virtueller Kern – Sekunden|
|App-Paket|app_memory_percent|Prozentsatz des von der App genutzten Speichers, bezogen auf den maximal zulässigen Speicher für die App.|Prozentwert|
|Benutzerpool|cpu_percent|Prozentsatz der von der Benutzerworkload genutzten virtuellen Kerne, bezogen auf die maximal zulässigen virtuellen Kerne für die Benutzerworkload.|Prozentwert|
|Benutzerpool|data_IO_percent|Prozentsatz der von der Benutzerworkload genutzten Daten-IOPS, bezogen auf die maximal zulässige Daten-IOPS für die Benutzerworkload.|Prozentwert|
|Benutzerpool|log_IO_percent|Prozentsatz der von der Benutzerworkload genutzten Protokollrate (MB/s), bezogen auf die maximal zulässige Protokollrate (MB/s) für die Benutzerworkload.|Prozentwert|
|Benutzerpool|workers_percent|Prozentsatz der von der Benutzerworkload genutzten Worker, bezogen auf die maximal zulässige Anzahl von Workern für die Benutzerworkload.|Prozentwert|
|Benutzerpool|sessions_percent|Prozentsatz der von der Benutzerworkload genutzten Sitzungen, bezogen auf die maximal zulässige Anzahl von Sitzungen für die Benutzerworkload.|Prozentwert|

### <a name="pause-and-resume-status"></a>Status für Anhalten und Fortsetzen

Im Azure-Portal wird der Datenbankstatus im Übersichtsbereich des Servers angezeigt, in dem die enthaltenen Datenbanken aufgelistet werden. Der Status der Datenbank wird auch im Übersichtsbereich für die Datenbank angezeigt.

Fragen Sie den Status für Anhalten und Fortsetzen einer Datenbank mit den folgenden Befehlen ab:

#### <a name="use-powershell"></a>Verwenden von PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Ressourceneinschränkungen

Ressourceneinschränkungen werden unter [serverlose Computeebene](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5) beschrieben.

## <a name="billing"></a>Abrechnung

Die abgerechnete Computeleistung basiert auf der maximal verwendeten CPU und dem verwendeten Arbeitsspeicher (pro Sekunde). Wenn die verwendete CPU und der verwendete Arbeitsspeicher kleiner als die bereitgestellte Mindestmenge sind, wird die bereitgestellte Menge abgerechnet. Der Arbeitsspeicher wird in Einheiten aus virtuellen Kernen normalisiert, indem der Arbeitsspeicher in GB nach 3 GB pro virtuellem Kern neu skaliert wird. So kann die CPU bei der Abrechnung mit dem Arbeitsspeicher verglichen werden.

- **Berechnete Ressource**: CPU und Arbeitsspeicher
- **Berechneter Betrag**: Einzelpreis virtueller Kern * Max. (Min. virtuelle Kerne, genutzte virtuelle Kerne, Min. Speicher GB * 1/3, genutzter Speicher GB * 1/3) 
- **Fakturierungsintervall**: Pro Sekunde

Der Einzelpreis für virtuelle Kerne ergibt sich aus den Kosten pro virtuellem Kern pro Sekunde. Informationen zu Einzelpreisen in einer bestimmten Region finden Sie auf der Seite [Azure SQL-Datenbank – Preise ](https://azure.microsoft.com/pricing/details/sql-database/single/).

Die genutzte Computekapazität wird mit der folgenden Metrik angegeben:

- **Metrik**: app_cpu_billed (virtueller Kern – Sekunden)
- **Definition**: Max. (min. virtuelle Kerne, genutzte virtuelle Kerne, min. Speicher GB * 1/3, genutzter Speicher GB * 1/3)
- **Berichtsfrequenz**: Pro Minute

Diese Menge wird pro Sekunde berechnet und über eine Minute aggregiert.

### <a name="minimum-compute-bill"></a>Mindestrechnungsbetrag für Computeressourcen

Wurde eine serverlose Datenbank angehalten, so beläuft sich der Rechnungsbetrag für Computeressourcen auf 0 (null).  Wurde sie nicht angehalten, so entspricht der Mindestrechnungsbetrag für Computeressourcen mindestens dem Betrag für die Anzahl von virtuellen Kernen basierend auf max. (min. virtuelle Kerne, min. Arbeitsspeicher GB * 1/3).

Beispiele:

- Angenommen, eine serverlose Datenbank wird nicht angehalten und ist so konfiguriert, dass die maximale Anzahl von virtuellen Kernen 8 und die minimale Anzahl von virtuellen Kernen 1 ist, was einem Mindestarbeitsspeicher von 3,0 GB entspricht.  Der Mindestrechnungsbetrag für Computeressourcen basiert dann auf max. (1 virtueller Kern, 3,0 GB * 1 virtueller Kern / 3 GB) = 1 virtueller Kern.
- Angenommen, eine serverlose Datenbank wird nicht angehalten und ist so konfiguriert, dass die maximale Anzahl von virtuellen Kernen 4 und die minimale Anzahl von virtuellen Kernen 0,5 ist, was einem Mindestarbeitsspeicher von 2,1 GB entspricht.  Der Mindestrechnungsbetrag für Computeressourcen basiert dann auf max. (0,5 virtuelle Kerne, 2,1 GB * 1 virtueller Kern / 3 GB) = 0,7 virtuelle Kerne.

Der [Preisrechner für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/calculator/?service=sql-database) für den Tarif Serverless kann verwendet werden, um den minimal konfigurierbaren Arbeitsspeicher zu bestimmen basierend auf der konfigurierten maximalen und minimalen Anzahl von virtuellen Kernen.  Wenn die konfigurierte Mindestanzahl von virtuellen Kernen größer als 0,5 ist, ist der Mindestrechnungsbetrag für Computeressourcen in der Regel unabhängig vom konfigurierten minimalen Arbeitsspeicher und basiert nur auf der konfigurierten minimalen Anzahl von virtuellen Kernen.

### <a name="example-scenario"></a>Beispielszenario

Erwägen Sie die Verwendung einer serverlosen Datenbank, für die für virtuelle Kerne ein Mindestwert von 1 und ein Höchstwert von 4 konfiguriert ist.  Dies entspricht ungefähr einem Arbeitsspeicher von mindestens 3 GB und maximal 12 GB.  Angenommen, die Verzögerung für automatisches Anhalten ist auf sechs Stunden festgelegt, und die Datenbankworkload ist während der ersten beiden Stunden eines Zeitraums von 24 Stunden aktiv und ansonsten inaktiv.    

In diesem Fall werden für die Datenbank Compute- und Speicherkosten während der ersten acht Stunden berechnet.  Die Datenbank ist zwar nach der zweiten Stunde inaktiv, aber für die nachfolgenden sechs Stunden werden basierend auf der minimalen bereitgestellten Computeleistung trotzdem noch Computekosten berechnet, während die Datenbank online ist.  Während die Datenbank angehalten ist, werden in der restlichen Zeit des 24-Stunden-Zeitraums nur Kosten für den Speicher berechnet.

Die genaue Berechnung der Computekosten für dieses Beispiel lautet:

|Zeitintervall|Pro Sekunde verwendete virtuelle Kerne|Pro Sekunde verwendete GB|Berechnete Computedimension|Für Zeitintervall berechnete Sekunden für virtuelle Kerne|
|---|---|---|---|---|
|0:00 - 1:00|4|9|Verwendete virtuelle Kerne|4 virtuelle Kerne * 3.600 Sekunden = 14.400 Sekunden für virtuelle Kerne|
|1:00 - 2:00|1|12|Verwendeter Arbeitsspeicher|12 GB * 1/3 * 3600 Sekunden = 14400 Sekunden für virtuelle Kerne|
|2:00 - 8:00|0|0|Mindestens bereitgestellter Arbeitsspeicher|3 GB * 1/3 * 21.600 Sekunden = 21.600 Sekunden für virtuelle Kerne|
|8:00 - 24:00|0|0|Keine Berechnung von Computeleistung während des Anhaltens|0 Sekunden für virtuelle Kerne|
|Gesamte berechnete Sekunden für virtuelle Kerne in 24 Stunden||||50.400 Sekunden für virtuelle Kerne|

Angenommen, der Compute-Einheitenpreis beträgt 0,000145 USD/V-Kern/Sekunde.  Die Computeleistung, die für diesen 24-Stunden-Zeitraum berechnet wird, ist dann das Produkt aus dem Preis der Compute-Einheit und den berechneten Sekunden für virtuelle Kerne: 0,000145 USD/V-Kern/Sekunde * 50400 Sekunden für virtuelle Kerne ~ 7,31 USD

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure-Hybridvorteil und reservierte Kapazität

Rabatte für Azure-Hybridvorteil (Azure Hybrid Benefit, AHB) und reservierte Kapazität gelten nicht für die serverlose Computeebene.

## <a name="available-regions"></a>Verfügbare Regionen

Die serverlose Computeebene ist weltweit verfügbar, mit Ausnahme der folgenden Regionen: „China, Osten“, „China, Norden“, „Deutschland, Mitte“, „Deutschland, Nordosten“ und „US Gov Iowa“.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den ersten Schritten finden Sie unter [Schnellstart: Erstellen einer Einzeldatenbank in Azure SQL-Datenbank über das Azure-Portal](single-database-create-quickstart.md).
- Ressourceneinschränkungen werden unter [Ressourceneinschränkungen für die serverlose Computeebene](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5) beschrieben.
