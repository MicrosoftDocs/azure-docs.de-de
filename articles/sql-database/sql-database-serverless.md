---
title: Azure SQL-Datenbank – serverlos (Vorschau) | Microsoft-Dokumentation
description: In diesem Artikel wird die neue serverlose Computeebene beschrieben und mit der vorhandenen bereitgestellten Computeebene verglichen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 06/05/2019
ms.openlocfilehash: c79d6a42cf7986bf120c406ceddfe2b024119435
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729072"
---
# <a name="sql-database-serverless-preview"></a>SQL-Datenbank – serverlos (Vorschau)

## <a name="serverless-compute-tier"></a>Serverlose Computeebene

SQL-Datenbank – serverlos (Vorschau) ist eine Einzeldatenbank-Computeebene, bei der die Computeressourcen automatische skaliert und Nutzung sekundengenau abgerechnet wird. 

Eine Datenbank in der serverlosen Computeebene wird durch den Computebereich parametrisiert, den sie nutzen kann, sowie durch eine Verzögerung durch automatisches Anhalten.

![Abrechnung – serverlos](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Leistung

- Die Mindestanzahl und die maximale Anzahl virtueller Kerne sind konfigurierbare Parameter, die den Bereich der Computekapazität definieren, die für die Datenbank verfügbar ist. Arbeitsspeicher- und E/A-Limits sind proportional zum angegebenen V-Kern-Bereich.  
- Die Verzögerung durch automatisches Anhalten ist ein konfigurierbarer Parameter, der die Zeitspanne definiert, für welche die Datenbank inaktiv sein muss, bevor sie automatisch pausiert wird. Bei der nächsten Anmeldung wird die Ausführung der Datenbank automatisch fortgesetzt.

### <a name="pricing"></a>Preise

- Die Gesamtrechnung für eine serverlose Datenbank setzt sich aus der Summe der Computerechnung und der Speicherrechnung zusammen.
Die Abrechnung für die Nutzung von Computeressourcen basiert auf der Menge der genutzten V-Kerne und des genutzten Speichers (pro Sekunde).
- Die mindestens berechneten Computeressourcen basieren auf den Minimalwerten für V-Kerne und Speicher.
- Bei angehaltener Datenbank wird lediglich Speicher in Rechnung gestellt.

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
|**Datenbanknutzungsmuster**| Wechselnde, unvorhersehbare Nutzung mit niedrigerer durchschnittlicher Computenutzung im Zeitverlauf. |  Regelmäßigere Nutzungsmuster mit höherer durchschnittlicher Computenutzung im Zeitverlauf oder mehrere Datenbanken, die Pools für elastische Datenbanken verwenden.|
| **Aufwand bei der Leistungsverwaltung** |Geringer|Höher|
|**Compute-Skalierung**|Automatisch|Manuell|
|**Compute-Reaktionsfähigkeit**|Geringer nach Inaktivitätszeiträumen|Unmittelbar|
|**Granularität bei der Abrechnung**|Pro Sekunde|Pro Stunde|

## <a name="purchasing-model-and-service-tier"></a>Kaufmodell und Dienstebene

„SQL-Datenbank – serverlos“ wird derzeit nur von der Ebene „Universell“ auf Hardware der Generation 5 im vCore-basierten Kaufmodell unterstützt.

## <a name="autoscale"></a>Autoscale

### <a name="scaling-responsiveness"></a>Reaktionsfähigkeit hinsichtlich der Skalierung

Im Allgemeinen werden Datenbanken auf einem Computer mit ausreichender Kapazität zum unterbrechungsfreien Erfüllen des Ressourcenbedarfs für beliebige Volumen von angeforderten Computeressourcen innerhalb der Grenzen unterstützt, die durch den Wert für die maximale Anzahl virtueller Kerne festgelegt sind. Gelegentlich tritt automatisch ein Lastenausgleich auf, wenn der Computer den Ressourcenbedarf nicht innerhalb weniger Minuten erfüllen kann. Die Datenbank bleibt während des Lastenausgleichs online, mit Ausnahme einer kurzen Zeitspanne am Schluss des Vorgangs, wenn Verbindungen verworfen werden.

### <a name="memory-management"></a>Speicherverwaltung

Arbeitsspeicher für serverlose Datenbanken wird häufiger als bei bereitgestellten Computedatenbanken freigegeben. Dieses Verhalten ist wichtig, um für serverlose Datenbanken die Kosten kontrollieren zu können, und es kann mit einer Beeinträchtigung der Leistung verbunden sein.

#### <a name="cache-reclamation"></a>Freigabe von Cache

Im Gegensatz zu bereitgestellten Computedatenbanken wird der Speicher aus dem SQL-Cache von einer serverlosen Datenbank freigegeben, wenn eine geringe CPU- oder Cacheauslastung vorliegt.

- Die Cacheauslastung wird als niedrig angesehen, wenn die Gesamtgröße der zuletzt verwendeten Cacheeinträge für einen bestimmten Zeitraum unter einen Schwellenwert fällt.
- Beim Auslösen der Cachefreigabe wird die Größe des Zielspeichers inkrementell auf einen Bruchteil der vorherigen Größe reduziert, und der Freigabevorgang wird nur fortgesetzt, wenn die Auslastung niedrig bleibt.
- Während der Cachefreigabe entspricht die Richtlinie für die Auswahl der zu entfernenden Cacheeinträge der Auswahlrichtlinie für bereitgestellte Computedatenbanken bei hoher Speicherauslastung.
- Der Cache wird niemals auf eine Größe verkleinert, die unter der Mindestgröße für den Arbeitsspeicher liegt. Dies wird über die Mindestanzahl von virtuellen Kernen definiert und kann entsprechend konfiguriert werden.

Sowohl in serverlosen als auch in bereitgestellten Computedatenbanken können Cacheeinträge entfernt werden, wenn der gesamte verfügbare Arbeitsspeicher verwendet wird.

#### <a name="cache-hydration"></a>Cachehydration

Der SQL-Cache wächst an, während Daten auf die gleiche Weise und mit der gleichen Geschwindigkeit wie für bereitgestellte Datenbanken vom Datenträger abgerufen werden. Wenn die Datenbank ausgelastet ist, kann die Größe des Caches uneingeschränkt bis zum maximalen Arbeitsspeichergrenzwert zunehmen.

## <a name="autopause-and-autoresume"></a>Automatisches Anhalten und automatisches Fortsetzen

### <a name="autopause"></a>Automatisches Anhalten

Das automatische Anhalten wird ausgelöst, wenn die folgenden Bedingungen für die Dauer der Verzögerung für automatisches Anhalten erfüllt sind:

- Anzahl der Sitzungen = 0
- CPU = 0 für Benutzerworkload im Benutzerpool

Es ist eine Option verfügbar, mit der das automatische Anhalten ggf. deaktiviert werden kann.

Das automatische Anhalten wird von den folgenden Features nicht unterstützt.  Das heißt, bei Verwendung eines der folgenden Features bleibt die Datenbank online, ungeachtet der Dauer der Inaktivität der Datenbank:

- Georeplikation (aktive Georeplikation und Gruppen für automatisches Failover).
- Langzeitaufbewahrung (Long-Term Retention, LTR) von Sicherungen.
- In SQL-Datensynchronisierung verwendete Synchronisierungsdatenbank

Das automatische Anhalten wird während der Bereitstellung bestimmter Dienstupdates vorübergehend verhindert, die zum Installieren des Updates erfordern, dass die Datenbank online ist.  In solchen Fällen ist das automatische Anhalten wieder zulässig, sobald das Dienstupdate abgeschlossen ist.

### <a name="autoresume"></a>Automatisches Fortsetzen

Das automatische Fortsetzen wird ausgelöst, wenn eine der folgenden Bedingungen erfüllt ist:

|Feature|Trigger für automatisches Fortsetzen|
|---|---|
|Authentifizierung und Autorisierung|Anmeldung|
|Bedrohungserkennung|Aktivieren/Deaktivieren von Einstellungen für die Bedrohungserkennung auf der Datenbank- oder Serverebene.<br>Ändern von Einstellungen für die Bedrohungserkennung auf der Datenbank- oder Serverebene.|
|Datenermittlung und -klassifizierung|Hinzufügen, Ändern, Löschen oder Anzeigen von Vertraulichkeitsbezeichnungen|
|Überwachung|Anzeigen von Überwachungsdatensätzen.<br>Aktualisieren oder Anzeigen von Überwachungsrichtlinien.|
|Datenmaskierung|Hinzufügen, Ändern, Löschen oder Anzeigen von Datenmaskierungsregeln|
|Transparent Data Encryption|Anzeigezustand oder Status der transparenten Datenverschlüsselung|
|Abfragedatenspeicher (Leistung)|Ändern oder Anzeigen von Abfragespeichereinstellungen; automatische Optimierung|
|Automatische Optimierung|Anwendung und Überprüfung von Empfehlungen für die automatische Optimierung, z.B. die automatische Indizierung|
|Kopieren von Datenbanken|Erstellen von Datenbanken als Kopie.<br>Exportieren in eine BACPAC-Datei.|
|SQL-Datensynchronisierung|Die Synchronisierung zwischen Hub- und Mitgliedsdatenbanken, die nach einem konfigurierbaren Zeitplan oder manuell ausgeführt werden|
|Ändern bestimmter Datenbankmetadaten|Hinzufügen von neuen Datenbanktags.<br>Ändern der Mindest- und Höchstwerte für virtuelle Kerne oder der Verzögerung für das automatische Anhalten.|
|SQL Server Management Studio (SSMS)|Durch Verwendung von SSMS Version 18 und Öffnen eines neuen Abfragefensters für eine Datenbank auf dem Server wird jede automatisch angehaltene Datenbank auf dem betreffenden Server fortgesetzt. Dieses Verhalten tritt nicht auf, wenn SSMS Version 17.9.1 verwendet wird und IntelliSense deaktiviert ist.|

Das automatische Fortsetzen wird ebenfalls während der Bereitstellung bestimmter Dienstupdates ausgelöst, die zum Installieren des Updates erfordern, dass die Datenbank online ist.

### <a name="connectivity"></a>Konnektivität

Wenn eine serverlose Datenbank angehalten wird, wird die Datenbank bei der ersten Anmeldung fortgesetzt, und es wird ein Fehler (Fehlercode 40613) mit dem Hinweis zurückgegeben, dass die Datenbank nicht verfügbar ist. Sobald die Datenbank fortgesetzt wird, muss die Anmeldung wiederholt werden, um die Verbindung herzustellen. Datenbankclients mit Wiederholungslogik für Verbindungen dürfen nicht geändert werden.

### <a name="latency"></a>Latency

Die Wartezeit für das automatische Fortsetzen und das automatische Anhalten einer serverlosen Datenbank liegt normalerweise im Bereich von 1 für das automatische Fortsetzen und zwischen 1 und 10 Minuten für das automatische Anhalten.

## <a name="onboarding-into-serverless-compute-tier"></a>Integration in die serverlose Computeebene

Beim Erstellen einer neuen Datenbank bzw. Verschieben einer vorhandenen Datenbank in eine serverlose Computeebene gilt dasselbe Muster wie beim Erstellen einer neuen Datenbank in der bereitgestellten Computeebene; dieser Vorgang umfasst die folgenden zwei Schritte:

1. Geben Sie den Namen des Dienstziels an. Das Dienstziel schreibt die Dienstebene, die Hardwaregeneration und die maximale Anzahl von virtuellen Kernen vor. Die folgende Tabelle enthält die verschiedenen Optionen für Dienstziele:

   |Name des Dienstziels|Dienstebene|Hardwaregeneration|Maximale Anzahl von V-Kernen|
   |---|---|---|---|
   |GP_S_Gen5_1|Allgemeiner Zweck|Gen5|1|
   |GP_S_Gen5_2|Allgemeiner Zweck|Gen5|2|
   |GP_S_Gen5_4|Allgemeiner Zweck|Gen5|4|

2. Geben Sie optional die Mindestanzahl virtueller Kerne und die Verzögerung für das automatische Anhalten an, um deren Standardwerte zu ändern. In der folgenden Tabelle werden die verfügbaren Werte für diese Parameter aufgeführt.

   |Parameter|Auswahlmöglichkeiten für Werte|Standardwert|
   |---|---|---|---|
   |Mindestanzahl virtueller Kerne|Beliebiger Wert aus {0,5, 1, 2, 4}, darf Maximalwert für V-Kerne nicht überschreiten|0,5 V-Kerne|
   |Verzögerung für das automatische Anhalten|Min: 360 Minuten (sechs Stunden)<br>Max: 10.080 Minuten (sieben Tage)<br>Inkremente: 60 Minuten<br>Automatisches Anhalten deaktivieren: -1|360 Minuten|

> [!NOTE]
> Das Verschieben einer vorhandenen Datenbank in eine serverlose Computeebene oder das Ändern der Computegröße mithilfe von T-SQL wird derzeit nicht unterstützt. Diese Vorgänge können jedoch über das Azure-Portal oder PowerShell ausgeführt werden.

### <a name="create-new-serverless-database-using-azure-portal"></a>Erstellen einer neuen serverlosen Datenbank über das Azure-Portal

Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Einzeldatenbank in Azure SQL-Datenbank über das Azure-Portal](sql-database-single-database-get-started.md).

### <a name="create-new-serverless-database-using-powershell"></a>Erstellen einer neuen serverlosen Datenbank unter Verwendung von PowerShell

Im folgenden Beispiel wird eine neue Datenbank in der serverlosen Computeebene erstellt, die durch das Dienstziel GP_S_Gen5_4 definiert wird; es werden Standardwerte für die Mindestanzahl virtueller Kerne und die Verzögerung für das automatische Anhalten angegeben.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelayInMinutes 720
```

### <a name="move-provisioned-compute-database-into-serverless-compute-tier"></a>Verschieben einer bereitgestellten Computedatenbank in die serverlose Computeebene

Im folgenden Beispiel wird eine vorhandene einzelne Datenbank aus der bereitgestellten Computeebene in die serverlose Computeebene verschoben. In diesem Beispiel werden die Mindestanzahl virtueller Kerne, die maximale Anzahl virtueller Kerne und die Verzögerung für das automatische Anhalten explizit angegeben.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-serverless-database-into-provisioned-compute-tier"></a>Verschieben einer serverlosen Datenbank in die bereitgestellte Computeebene

Eine serverlose Datenbank kann auf die gleiche Weise in eine bereitgestellte Computeebene verschoben werden wie eine bereitgestellte Datenbank in eine serverlose Computeebene.

## <a name="modifying-serverless-configuration"></a>Ändern der serverlosen Konfiguration

### <a name="maximum-vcores"></a>Maximale Anzahl virtueller Kerne

Das Ändern der maximalen Anzahl virtueller Kerne erfolgt durch Ausführen des Befehls [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) in PowerShell mit dem `MaxVcore`-Argument.

### <a name="minimum-vcores"></a>Mindestanzahl virtueller Kerne

Das Ändern der Mindestanzahl virtueller Kerne erfolgt durch Ausführen des Befehls [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) in PowerShell mit dem `MinVcore`-Argument.

### <a name="autopause-delay"></a>Verzögerung für das automatische Anhalten

Das Ändern der Verzögerung für das automatische Anhalten erfolgt durch Ausführen des Befehls [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) in PowerShell mit dem `AutoPauseDelayInMinutes`-Argument.

## <a name="monitoring"></a>Überwachung

### <a name="resources-used-and-billed"></a>Genutzte und berechnete Ressourcen

Die Ressourcen einer serverlosen Datenbank werden durch die folgenden Einheiten gekapselt:

#### <a name="app-package"></a>App-Paket

Das App-Paket ist die „Außengrenze“ der Ressourcenverwaltung für eine Datenbank, wobei es keine Rolle spielt, ob sich die Datenbank auf einer serverlosen oder einer bereitgestellten Computeebene befindet. Das App-Paket enthält die SQL-Instanz und externe Dienste, die zusammen sämtliche Benutzer- und Systemressourcen umfassen, die von einer Datenbank in SQL-Datenbank genutzt werden. Beispiele für externe Dienste sind R und die Volltextsuche. Die SQL-Instanz bestimmt generell die allgemeine Ressourcennutzung für das gesamte App-Paket.

#### <a name="user-resource-pool"></a>Benutzerressourcenpool

Der Benutzerressourcenpool ist die „Innengrenze“ der Ressourcenverwaltung für eine Datenbank, wobei es keine Rolle spielt, ob sich die Datenbank auf einer serverlosen oder einer bereitgestellten Computeebene befindet. Der Benutzerressourcenpool beschränkt CPU und E/A für Benutzerworkload, die von DDL-Abfragen (z.B. CREATE und ALTER) und DML-Abfragen (z.B. SELECT, INSERT, UPDATE und DELETE) generiert wird. Diese Abfragen sind im Allgemeinen für den Großteil der Auslastung des App-Pakets verantwortlich.

### <a name="metrics"></a>metrics

|Entität|Metrik|BESCHREIBUNG|Units|
|---|---|---|---|
|App-Paket|app_cpu_percent|Prozentsatz der von der App genutzten virtuellen Kerne, bezogen auf die maximal zulässigen virtuellen Kerne für die App.|Prozentsatz|
|App-Paket|app_cpu_billed|Die Menge der Computeressourcen, die im Berichtszeitraum für die App abgerechnet wurden. Der während dieses Zeitraums zu zahlende Betrag ist das Produkt aus dieser Metrik und dem Einzelpreis für virtuelle Kerne. <br><br>Werte dieser Metrik werden bestimmt, indem der maximal genutzte Arbeitsspeicher und der pro Sekunde genutzte Speicher über einen Zeitraum aggregiert werden. Liegt die genutzte Menge unter der bereitgestellten Mindestmenge (festgelegt durch Mindestanzahl virtueller Kerne und Minimalwert für Speicher), wird die bereitgestellte Mindestmenge berechnet. Der Arbeitsspeicher wird in Einheiten aus virtuellen Kernen normalisiert, indem der Arbeitsspeicher in GB nach 3 GB pro virtuellem Kern neu skaliert wird. So kann die CPU bei der Abrechnung mit dem Arbeitsspeicher verglichen werden.|Virtueller Kern – Sekunden|
|App-Paket|app_memory_percent|Prozentsatz des von der App genutzten Speichers, bezogen auf den maximal zulässigen Speicher für die App.|Prozentsatz|
|Benutzerpool|cpu_percent|Prozentsatz der von der Benutzerworkload genutzten virtuellen Kerne, bezogen auf die maximal zulässigen virtuellen Kerne für die Benutzerworkload.|Prozentsatz|
|Benutzerpool|data_IO_percent|Prozentsatz der von der Benutzerworkload genutzten Daten-IOPS, bezogen auf die maximal zulässige Daten-IOPS für die Benutzerworkload.|Prozentsatz|
|Benutzerpool|log_IO_percent|Prozentsatz der von der Benutzerworkload genutzten Protokollrate (MB/s), bezogen auf die maximal zulässige Protokollrate (MB/s) für die Benutzerworkload.|Prozentsatz|
|Benutzerpool|workers_percent|Prozentsatz der von der Benutzerworkload genutzten Worker, bezogen auf die maximal zulässige Anzahl von Workern für die Benutzerworkload.|Prozentsatz|
|Benutzerpool|sessions_percent|Prozentsatz der von der Benutzerworkload genutzten Sitzungen, bezogen auf die maximal zulässige Anzahl von Sitzungen für die Benutzerworkload.|Prozentsatz|
____

> [!NOTE]
> Metriken sind im Azure-Portal im Datenbankbereich für eine einzelne Datenbank unter **Überwachung** verfügbar.

### <a name="pause-and-resume-status"></a>Status für Anhalten und Fortsetzen

Im Azure-Portal wird der Datenbankstatus im Übersichtsbereich des Servers angezeigt, in dem die enthaltenen Datenbanken aufgelistet werden. Der Status der Datenbank wird auch im Übersichtsbereich für die Datenbank angezeigt.

Fragen Sie den Status für Anhalten und Fortsetzen einer Datenbank mit dem folgenden PowerShell-Befehl ab:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Ressourceneinschränkungen

Ressourceneinschränkungen werden unter [Serverlose Computeebene](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier) beschrieben.

## <a name="billing"></a>Abrechnung

Die abgerechnete Computeleistung basiert auf der maximal verwendeten CPU und dem verwendeten Arbeitsspeicher (pro Sekunde). Wenn die verwendete CPU und der verwendete Arbeitsspeicher kleiner als die bereitgestellte Mindestmenge sind, wird die bereitgestellte Menge abgerechnet. Der Arbeitsspeicher wird in Einheiten aus virtuellen Kernen normalisiert, indem der Arbeitsspeicher in GB nach 3 GB pro virtuellem Kern neu skaliert wird. So kann die CPU bei der Abrechnung mit dem Arbeitsspeicher verglichen werden.

- **Berechnete Ressource**: CPU und Arbeitsspeicher
- **Berechneter Betrag**: Einzelpreis virtueller Kern * Max. (Min. virtuelle Kerne, genutzte virtuelle Kerne, Min. Speicher GB * 1/3, genutzter Speicher GB * 1/3) 
- **Fakturierungsintervall**: Pro Sekunde

Der Einzelpreis für virtuelle Kerne ergibt sich aus den Kosten pro virtuellem Kern pro Sekunde. Informationen zu Einzelpreisen in einer bestimmten Region finden Sie auf der Seite [Azure SQL-Datenbank – Preise ](https://azure.microsoft.com/pricing/details/sql-database/single/).

Die genutzte Computekapazität wird mit der folgenden Metrik angegeben:

- **Metrik**: app_cpu_billed (virtueller Kern – Sekunden)
- **Definition**: Max. (min. virtuelle Kerne, genutzte virtuelle Kerne, min. Speicher GB · 1/3, genutzter Speicher GB · 1/3)
- **Berichtsfrequenz**: Pro Minute

Diese Menge wird pro Sekunde berechnet und über eine Minute aggregiert.

Erwägen Sie die Verwendung einer serverlosen Datenbank, für die für virtuelle Kerne ein Mindestwert von 1 und ein Höchstwert von 4 konfiguriert ist.  Dies entspricht ungefähr einem Arbeitsspeicher von mindestens 3 GB und maximal 12 GB.  Angenommen, die Verzögerung für automatisches Anhalten ist auf sechs Stunden festgelegt, und die Datenbankworkload ist während der ersten beiden Stunden eines Zeitraums von 24 Stunden aktiv und ansonsten inaktiv.    

In diesem Fall werden für die Datenbank Compute- und Speicherkosten während der ersten acht Stunden berechnet.  Die Datenbank ist zwar nach der zweiten Stunde inaktiv, aber für die nachfolgenden sechs Stunden werden basierend auf der minimalen bereitgestellten Computeleistung trotzdem noch Computekosten berechnet, während die Datenbank online ist.  Während die Datenbank angehalten ist, werden in der restlichen Zeit des 24-Stunden-Zeitraums nur Kosten für den Speicher berechnet.

Die genaue Berechnung der Computekosten für dieses Beispiel lautet:

|Zeitintervall|Pro Sekunde verwendete virtuelle Kerne|Pro Sekunde verwendete GB|Berechnete Computedimension|Für Zeitintervall berechnete Sekunden für virtuelle Kerne|
|---|---|---|---|---|
|0:00 - 1:00|4|9|Verwendete virtuelle Kerne|4 virtuelle Kerne · 3.600 Sekunden = 14.400 Sekunden für virtuelle Kerne|
|1:00 - 2:00|1|12|Verwendeter Arbeitsspeicher|12 GB * 1/3 * 3600 Sekunden = 14400 Sekunden für virtuelle Kerne|
|2:00 - 8:00|0|0|Mindestens bereitgestellter Arbeitsspeicher|3 GB * 1/3 * 21.600 Sekunden = 21.600 Sekunden für virtuelle Kerne|
|8:00 - 24:00|0|0|Keine Berechnung von Computeleistung während des Anhaltens|0 Sekunden für virtuelle Kerne|
|Gesamte berechnete Sekunden für virtuelle Kerne in 24 Stunden||||50\.400 Sekunden für virtuelle Kerne|

Angenommen, der Compute-Einzelpreis beträgt 0,000073 USD/V-Kern/Sekunde.  Die Computeleistung, die für diesen 24-Stunden-Zeitraum berechnet wird, ist dann das Produkt aus dem Preis der Compute-Einheit und den berechneten Sekunden für virtuelle Kerne: 0,000073 USD/V-Kern/Sekunde · 50.400 Sekunden für virtuelle Kerne = 3,68 USD

## <a name="available-regions"></a>Verfügbare Regionen

Die serverlose Computeebene ist weltweit verfügbar, mit Ausnahme der folgenden Regionen: Australien, Mitte, China, Osten, China, Norden, Frankreich, Süden, Deutschland, Mitte, Deutschland, Nordosten, Indien, Westen, Südkorea, Süden, Südafrika, Westen, Vereinigtes Königreich, Norden, Vereinigtes Königreich, Süden, Vereinigtes Königreich, Westen und USA, Westen-Mitte.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den ersten Schritten finden Sie unter [Schnellstart: Erstellen einer Einzeldatenbank in Azure SQL-Datenbank über das Azure-Portal](sql-database-single-database-get-started.md).
- Ressourceneinschränkungen werden unter [Ressourceneinschränkungen für die serverlose Computeebene](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier) beschrieben.
