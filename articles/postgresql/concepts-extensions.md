---
title: Verwenden von PostgreSQL-Erweiterungen in Azure Database for PostgreSQL – Einzelserver
description: Beschreibung der Möglichkeit zum Erweitern der Funktionalität von Datenbanken mithilfe von Erweiterungen in Azure Database for PostgreSQL – Einzelserver.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 962e2b10136cf1cbab7cc5d3d06059922c363b15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410274"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-Erweiterungen in Azure Database for PostgreSQL – Einzelserver
PostgreSQL bietet die Möglichkeit, die Funktionalität Ihrer Datenbank mithilfe von Erweiterungen zu erweitern. Durch Erweiterungen können mehrere SQL-bezogene Objekte zu einem einzigen Paket gebündelt und über einen einzigen Befehl in die Datenbank geladen oder aus dieser entfernt werden. Nach dem Laden in die Datenbank können Erweiterungen ebenso wie integrierte Features funktionieren. Weitere Informationen zu PostgreSQL-Erweiterungen finden Sie unter  [Packen von zugehörigen Objekten in einer Erweiterung](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Wie werden PostgreSQL-Erweiterungen verwendet?
Bevor Sie PostgreSQL-Erweiterungen verwenden können, müssen diese in Ihrer Datenbank installiert werden. Um eine bestimmte Erweiterung zu installieren, führen Sie zum Laden der gepackten Objekte in Ihrer Datenbank den Befehl  [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html)  über das psql-Tool aus.

Azure Database for PostgreSQL unterstützt eine Teilmenge der wichtigsten unten aufgeführten Erweiterungen. Andere Erweiterungen außer den hier aufgeführten werden nicht unterstützt. Mit dem Azure Database for PostgreSQL-Dienst können Sie keine eigenen Erweiterungen erstellen.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Unterstützte Erweiterungen in Azure-Datenbank für PostgreSQL
In den folgenden Tabellen werden die standardmäßigen PostgreSQL-Erweiterungen aufgeführt, die derzeit von Azure-Datenbank für PostgreSQL unterstützt werden. Sie können diese Informationen auch abrufen, indem Sie `SELECT * FROM pg_available_extensions;` ausführen.

### <a name="data-types-extensions"></a>Datentypenerweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Stellt einen Datentyp für automatisch verschlüsselte Kennwörter bereit. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Gibt einen Zeichenfolgentyp an, bei dem Groß-/Kleinschreibung beachtet werden muss. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Stellt einen Datentyp für mehrdimensionale Cubes bereit. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Gibt den Datentyp zum Speichern von Schlüssel-/Wertpaaren an. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Stellt Datentypen für internationale Produktnummerierungsnormen bereit. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Stellt einen Datentyp für hierarchische baumähnliche Strukturen bereit. |

### <a name="functions-extensions"></a>Funktionserweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Bietet eine Möglichkeit, Großkreisentferungen auf der Erdoberfläche zu berechnen. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Stellt mehrere Funktionen bereit, um Ähnlichkeiten und den Abstand zwischen Zeichenfolgen zu ermitteln |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Stellt Funktionen und Operatoren bereit, um Arrays mit Ganzzahlen ohne Nullen zu bearbeiten |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Stellt kryptografische Funktionen bereit |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Verwaltet partitionierte Tabellen nach Zeit oder ID |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Stellt Funktionen und Operatoren bereit, um die Ähnlichkeit von alphanumerischen Texten basierend auf übereinstimmenden Trigrammen zu ermitteln |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Stellt Funktionen bereit, um ganze Tabellen einschließlich einer Kreuztabelle zu bearbeiten. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Generiert universell eindeutige Bezeichner (UUIDs) |
> | [orafce](https://github.com/orafce/orafce) | Bietet eine Teilmenge von Funktionen und Paketen, die von kommerziellen Datenbanken emuliert werden. |

### <a name="full-text-search-extensions"></a>Erweiterungen für die Volltextsuche

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Bietet eine Textsuch-Wörterbuchvorlage für Integerwerte. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Ein Wörterbuch für die Textsuche, das Akzente (diakritische Zeichen) aus Lexemen entfernt |

### <a name="index-types-extensions"></a>Indextypenerweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Gibt Beispielklassen für den GIN-Operator an, der das B-Struktur-ähnliche Verhalten für bestimmte Datentypen implementiert |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Gibt Operatorklassen für den GiST-Index an, der die B-Struktur implementiert |

### <a name="language-extensions"></a>Spracherweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Ladbare prozedurale Sprache, PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Verschiedene Erweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Bietet eine Möglichkeit, um die Vorgänge im freigegebenen Puffercache in Echtzeit mitzuverfolgen |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Bietet eine Möglichkeit, um Relationsdaten in den Puffercache zu laden |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Bietet eine Möglichkeit, um Ausführungsstatistiken zu allen SQL-Anweisungen nachzuverfolgen, die von einem Server ausgeführt werden (Eine Notiz zu dieser Erweiterung finden Sie unten.) |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Bietet eine Möglichkeit zum Anzeigen von Sperrinformationen auf Zeilenebene. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Bietet eine Möglichkeit zum Anzeigen von Statistiken auf Tupelebene. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper für programmfremde Daten, um auf in externen PostgreSQL-Servern gespeicherten Daten zuzugreifen (Eine Notiz zu dieser Erweiterung finden Sie unten.)|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Stellt eine Möglichkeit zum Erstellen hypothetischer Indizes bereit, die weder CPU noch Speicherplatz benötigen. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Ein Modul, das Verbindungen mit anderen PostgreSQL-Datenbanken innerhalb einer Datenbanksitzung unterstützt (Eine Notiz zu dieser Erweiterung finden Sie unten.) |


### <a name="postgis-extensions"></a>PostGIS-Erweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Räumliche und geografische Objekte für PostgreSQL |
> | address\_standardizer, address\_standardizer\_data\_us | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. Wird als Unterstützung für den Normalisierungsschritt zur Geocodierung von Adressen verwendet. |
> | [pgrouting](https://pgrouting.org/) | Erweitert die PostGIS/PostgreSQL-Geodatenbank um die Bereitstellung von Funktionen zum räumlichen Routing |


### <a name="time-series-extensions"></a>Zeitreihenerweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | Eine Zeitreihen-SQL-Datenbank, die automatisierte Partitionierung für schnellere Erfassung und Abfragen unterstützt. Bietet zeitlich orientierte Analysefunktionen, Optimierungen, und es skaliert PostgreSQL für Zeitreihenworkloads. TimescaleDB wurde entwickelt und ist eine eingetragene Marke von [Timescale, Inc.](https://www.timescale.com/). (Eine Notiz zu dieser Erweiterung finden Sie unten.) |


## <a name="pgstatstatements"></a>pg_stat_statements
Die [pg\_stat\_statements-Erweiterung](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) wird auf jedem Azure Database for PostgreSQL-Server vorab geladen,um Ihnen eine Möglichkeit zur Nachverfolgung von Ausführungsstatistiken von SQL-Anweisungen bereitzustellen.
Die Einstellung `pg_stat_statements.track`, die steuert, welche Anweisungen von der Erweiterung gezählt werden, ist standardmäßig auf `top` festgelegt, was bedeutet, dass alle Anweisungen, die direkt von Clients ausgestellt werden, nachverfolgt werden. Die beiden anderen Nachverfolgungsebenen sind `none` und `all`. Diese Einstellung kann als Serverparameter über das [Azure-Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) oder die [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) konfiguriert werden.

Zwischen den von pg_stat_statements bereitgestellten Abfrageausführungsinformationen und der Auswirkung auf die Serverleistung besteht ein Kompromiss, da jede SQL-Anweisung protokolliert wird. Wenn Sie die pg_stat_statements-Erweiterung nicht aktiv verwenden, empfiehlt es sich, `pg_stat_statements.track` auf `none` festzulegen. Beachten Sie, dass sich einige Überwachungsdienste von Drittanbietern auf pg_stat_statements beziehen können, um Statistiken zur Abfrageleistung zu liefern. Bestätigen Sie also, ob dieser Fall auf Sie zutrifft oder nicht.

## <a name="dblink-and-postgresfdw"></a>„dblink“ und „postgres_fdw“
„dblink“ und „postgres_fdw“ ermöglichen Ihnen das Herstellen einer Verbindung von einem PostgreSQL-Server mit einem anderen oder mit einer anderen Datenbank auf demselben Server. Der empfangende Server muss Verbindungen vom sendenden Server über die Firewall zulassen. Bei Verwendung dieser Erweiterungen für die Verbindung zwischen Azure Database for PostgreSQL-Server kann dazu die Einstellung „Zugriff auf Azure-Dienste erlauben“ auf EIN gesetzt werden. Dies ist auch erforderlich, wenn Sie die Erweiterungen für ein Loopback zum selben Server verwenden möchten. Die Einstellung „Zugriff auf Azure-Dienste erlauben“ finden Sie auf der Azure-Portalseite für den Postgres-Server unter „Verbindungssicherheit“. Durch Aktivieren von „Zugriff auf Azure-Dienste erlauben“ werden alle Azure-Dienste auf die Whitelist gesetzt.

Derzeit werden ausgehende Verbindungen von Azure Database for PostgreSQL nicht unterstützt – mit Ausnahme von Verbindungen mit anderen Azure Database for PostgreSQL-Servern.

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB ist eine Zeitreihendatenbank, die als eine Erweiterung für PostgreSQL verpackt wird. TimescaleDB bietet zeitlich orientierte Analysefunktionen, Optimierungen, und es skaliert Postgres für Zeitreihenworkloads.

[Weitere Informationen zu TimescaleDB](https://docs.timescale.com/latest), einer eingetragenen Marke von [Timescale, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Installieren von TimescaleDB
Um TimescaleDB installieren zu können, müssen Sie es in die freigegebenen im Voraus geladenen Bibliotheken des Servers einbeziehen. Nach einer Änderung an diesen Bibliotheken von Postgre muss **der Server neu gestartet werden**, damit sie wirksam wird.

> [!NOTE]
> TimescaleDB kann für Azure Database for PostgreSQL, Version 9.6 und 10, aktiviert werden.

Verwenden des [Azure-Portals](https://portal.azure.com/):

1. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.

2. Wählen Sie auf der Seitenleiste **Serverparameter** aus.

3. Suchen Sie nach dem Parameter `shared_preload_libraries`.

4. Kopieren und fügen Sie Folgendes als Wert für `shared_preload_libraries` ein.
   ```
   timescaledb
   ```

5. Wählen Sie **Speichern** aus, um Ihre Änderungen beizubehalten. Sobald die Änderung gespeichert wurde, erhalten Sie eine entsprechende Benachrichtigung. 

6. Nach Empfang der Benachrichtigung müssen Sie den Server **neu starten**, damit diese Änderungen übernommen werden. Die Vorgehensweise zum Neustart eines Servers finden Sie unter [Neustart eines Azure Database for PostgreSQL-Servers über das Azure-Portal](howto-restart-server-portal.md).


Sie können jetzt TimescaleDB in Ihrer Postgres-Datenbank aktivieren. Stellen Sie eine Verbindung mit der Datenbank her, und geben Sie den folgenden Befehl ein:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Wenn ein Fehler angezeigt wird, bestätigen Sie, dass Sie nach dem Speichern von „shared_preload_libraries“ [Ihren Server neu gestartet haben](howto-restart-server-portal.md). 

Sie können jetzt eine TimescaleDB-Hypertable [ganz neu erstellen](https://docs.timescale.com/getting-started/creating-hypertables) oder [vorhandene Zeitreihendaten in PostgreSQL migrieren](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Nächste Schritte
Wenn Sie eine Erweiterung fehlt, die Sie verwenden möchten, lassen Sie es uns wissen. Stimmen Sie in unserem [Kundenfeedback-Forum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) über vorhandene Anfragen ab, oder teilen Sie uns weiteres Feedback und Wünsche mit.
