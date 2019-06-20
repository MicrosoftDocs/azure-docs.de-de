---
title: PostgreSQL-Erweiterungen in Azure Database for PostgreSQL – Hyperscale (Citus) (Vorschauversion)
description: Beschreibung der Möglichkeit zum Erweitern der Funktionalität von Datenbanken mithilfe von Erweiterungen in Azure-Datenbank für PostgreSQL
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4022c95bfda8cbdaed75876793bfbba4254a5c54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410250"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>PostgreSQL-Erweiterungen in Azure Database for PostgreSQL – Hyperscale (Citus) (Vorschauversion)

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
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Gibt einen Zeichenfolgentyp an, bei dem Groß-/Kleinschreibung beachtet werden muss. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Stellt einen Datentyp für mehrdimensionale Cubes bereit. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Gibt den Datentyp zum Speichern von Schlüssel-/Wertpaaren an. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Stellt Datentypen für internationale Produktnummerierungsnormen bereit. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Large Object-Wartung. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Stellt einen Datentyp für hierarchische baumähnliche Strukturen bereit. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Datentyp für die Darstellung von Liniensegmenten oder Gleitkommaintervallen. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Typ für Erste N-JSONB. |

### <a name="functions-extensions"></a>Funktionserweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funktionen für die automatische Inkrementierung von Feldern. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Bietet eine Möglichkeit, Großkreisentferungen auf der Erdoberfläche zu berechnen. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Stellt mehrere Funktionen bereit, um Ähnlichkeiten und den Abstand zwischen Zeichenfolgen zu ermitteln |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funktionen, um nachzuverfolgen, welcher Benutzer Änderungen in einer Tabelle vorgenommen hat. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Integeraggregator und -enumerator (veraltet). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Stellt Funktionen und Operatoren bereit, um Arrays mit Ganzzahlen ohne Nullen zu bearbeiten |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funktionen zur Verfolgung der letzten Änderungszeit. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Stellt kryptografische Funktionen bereit |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Verwaltet partitionierte Tabellen nach Zeit oder ID |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Stellt Funktionen und Operatoren bereit, um die Ähnlichkeit von alphanumerischen Texten basierend auf übereinstimmenden Trigrammen zu ermitteln |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funktionen für die Implementierung referentieller Integrität (veraltet). |
> | session\_analytics | Funktionen zum Abfragen von Hstore-Arrays. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Stellt Funktionen bereit, um ganze Tabellen einschließlich einer Kreuztabelle zu bearbeiten. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Ausgelöste Änderungsbenachrichtigungen. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funktionen für die Implementierung von Zeitreisen. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Generiert universell eindeutige Bezeichner (UUIDs) |

### <a name="full-text-search-extensions"></a>Erweiterungen für die Volltextsuche

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Bietet eine Textsuch-Wörterbuchvorlage für Integerwerte. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Wörterbuchvorlage für die Textsuche für die erweiterte Verarbeitung von Synonymen. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Ein Wörterbuch für die Textsuche, das Akzente (diakritische Zeichen) aus Lexemen entfernt |

### <a name="index-types-extensions"></a>Indextypenerweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom-Zugriffsmethode: Index, der auf einer Signaturdatei basiert. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Gibt Beispielklassen für den GIN-Operator an, der das B-Struktur-ähnliche Verhalten für bestimmte Datentypen implementiert |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Gibt Operatorklassen für den GiST-Index an, der die B-Struktur implementiert |

### <a name="language-extensions"></a>Spracherweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Ladbare prozedurale Sprache, PL/pgSQL. |

### <a name="hyperscale-extensions"></a>Hyperscale-Erweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Von Citus verteilte Datenbank. |
> | shard\_rebalancer | Sicheres Ausgleichen von Daten in einer Servergruppe, wenn Knoten hinzugefügt oder entfernt werden. |

### <a name="miscellaneous-extensions"></a>Verschiedene Erweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Verwaltungsfunktionen für PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funktionen zum Überprüfen der Beziehungsintegrität. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper für programmfremde Daten für den Flatfile-Zugriff. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Überprüfen Sie den Inhalt von Datenbankseiten auf niedriger Ebene. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Bietet eine Möglichkeit, um die Vorgänge im freigegebenen Puffercache in Echtzeit mitzuverfolgen |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Auftragsplaner für PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Untersuchen von free space map (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Bietet eine Möglichkeit, um Relationsdaten in den Puffercache zu laden |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Bietet eine Möglichkeit, um Ausführungsstatistiken zu allen SQL-Anweisungen nachzuverfolgen, die von einem Server ausgeführt werden (Eine Notiz zu dieser Erweiterung finden Sie unten.) |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | Überprüfen Sie die Informationen zur Zuordnung von Sichtbarkeiten (VM) und Sichtbarkeit auf Seitenebene. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Bietet eine Möglichkeit zum Anzeigen von Sperrinformationen auf Zeilenebene. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Bietet eine Möglichkeit zum Anzeigen von Statistiken auf Tupelebene. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper für programmfremde Daten, um auf in externen PostgreSQL-Servern gespeicherten Daten zuzugreifen (Eine Notiz zu dieser Erweiterung finden Sie unten.)|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informationen zu SSL-Zertifikaten. |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE-Methode, die die Anzahl von Zeilen als Grenzwert akzeptiert. |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE-Methode, die die Zeit in Millisekunden als Grenzwert akzeptiert. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Stellt eine Möglichkeit zum Erstellen hypothetischer Indizes bereit, die weder CPU noch Speicherplatz benötigen. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Ein Modul, das Verbindungen mit anderen PostgreSQL-Datenbanken innerhalb einer Datenbanksitzung unterstützt (Eine Notiz zu dieser Erweiterung finden Sie unten.) |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-Abfrage und XSLT. |


### <a name="postgis-extensions"></a>PostGIS-Erweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Räumliche und geografische Objekte für PostgreSQL |
> | address\_standardizer, address\_standardizer\_data\_us | Wird verwendet, um eine Adresse in dessen zugehörige Elemente zu analysieren. Wird als Unterstützung für den Normalisierungsschritt zur Geocodierung von Adressen verwendet. |
> | postgis\_sfcgal | PostGIS SFCGAL-Funktionen. |
> | postgis\_tiger\_geocoder | Geocoder und Reverse-Geocoder für PostGIS Tiger. |
> | postgis\_topology | Räumliche PostGIS-Topologietypen und -funktionen. |


## <a name="pgstatstatements"></a>pg_stat_statements
Die [pg\_stat\_statements-Erweiterung](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) wird auf jedem Azure Database for PostgreSQL-Server vorab geladen,um Ihnen eine Möglichkeit zur Nachverfolgung von Ausführungsstatistiken von SQL-Anweisungen bereitzustellen.
Die Einstellung `pg_stat_statements.track`, die steuert, welche Anweisungen von der Erweiterung gezählt werden, ist standardmäßig auf `top` festgelegt, was bedeutet, dass alle Anweisungen, die direkt von Clients ausgestellt werden, nachverfolgt werden. Die beiden anderen Nachverfolgungsebenen sind `none` und `all`. Diese Einstellung kann als Serverparameter über das [Azure-Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) oder die [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) konfiguriert werden.

Zwischen den von pg_stat_statements bereitgestellten Abfrageausführungsinformationen und der Auswirkung auf die Serverleistung besteht ein Kompromiss, da jede SQL-Anweisung protokolliert wird. Wenn Sie die pg_stat_statements-Erweiterung nicht aktiv verwenden, empfiehlt es sich, `pg_stat_statements.track` auf `none` festzulegen. Beachten Sie, dass sich einige Überwachungsdienste von Drittanbietern auf pg_stat_statements beziehen können, um Statistiken zur Abfrageleistung zu liefern. Bestätigen Sie also, ob dieser Fall auf Sie zutrifft oder nicht.

## <a name="dblink-and-postgresfdw"></a>„dblink“ und „postgres_fdw“
„dblink“ und „postgres_fdw“ ermöglichen Ihnen das Herstellen einer Verbindung von einem PostgreSQL-Server mit einem anderen oder mit einer anderen Datenbank auf demselben Server. Der empfangende Server muss Verbindungen vom sendenden Server über die Firewall zulassen. Bei Verwendung dieser Erweiterungen für die Verbindung zwischen Azure Database for PostgreSQL-Server kann dazu die Einstellung „Zugriff auf Azure-Dienste erlauben“ auf EIN gesetzt werden. Dies ist auch erforderlich, wenn Sie die Erweiterungen für ein Loopback zum selben Server verwenden möchten. Die Einstellung „Zugriff auf Azure-Dienste erlauben“ finden Sie auf der Azure-Portalseite für den Postgres-Server unter „Verbindungssicherheit“. Durch Aktivieren von „Zugriff auf Azure-Dienste erlauben“ werden alle Azure-Dienste auf die Whitelist gesetzt.

Derzeit werden ausgehende Verbindungen von Azure Database for PostgreSQL nicht unterstützt – mit Ausnahme von Verbindungen mit anderen Azure Database for PostgreSQL-Servern.
