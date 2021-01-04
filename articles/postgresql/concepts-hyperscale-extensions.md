---
title: Erweiterungen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Beschreibt die Möglichkeit zum Erweitern der Funktionalität von Datenbanken mithilfe von Erweiterungen in Azure Database for PostgreSQL mit Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 000f8a1457298901dcfc94bc5e0923e94ba35dc7
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620901"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-Erweiterungen in Azure Database for PostgreSQL – Hyperscale (Citus)

PostgreSQL bietet die Möglichkeit, die Funktionalität Ihrer Datenbank mithilfe von Erweiterungen zu erweitern. Durch Erweiterungen können mehrere SQL-bezogene Objekte zu einem einzigen Paket gebündelt und über einen einzigen Befehl in die Datenbank geladen oder aus dieser entfernt werden. Nach dem Laden in die Datenbank können Erweiterungen ebenso wie integrierte Features funktionieren. Weitere Informationen zu PostgreSQL-Erweiterungen finden Sie unter [Packen von zugehörigen Objekten in einer Erweiterung](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Verwenden von PostgreSQL-Erweiterungen

Bevor Sie PostgreSQL-Erweiterungen verwenden können, müssen diese in Ihrer Datenbank installiert werden. Um eine bestimmte Erweiterung zu installieren, führen Sie zum Laden der gepackten Objekte in Ihrer Datenbank den Befehl [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) über das psql-Tool aus.

Azure Database for PostgreSQL – Hyperscale (Citus) unterstützt eine Teilmenge der wichtigsten unten aufgeführten Erweiterungen. Andere Erweiterungen als die aufgelisteten Erweiterungen werden nicht unterstützt. Mit Azure Database for PostgreSQL können keine eigenen Erweiterungen erstellt werden.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Unterstützte Erweiterungen in Azure-Datenbank für PostgreSQL

In den folgenden Tabellen werden die standardmäßigen PostgreSQL-Erweiterungen aufgeführt, die derzeit von Azure-Datenbank für PostgreSQL unterstützt werden. Sie können diese Informationen auch abrufen, indem Sie `SELECT * FROM pg_available_extensions;` ausführen.

### <a name="data-types-extensions"></a>Datentypenerweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Gibt einen Zeichenfolgentyp an, bei dem Groß-/Kleinschreibung beachtet werden muss. |
> | [cube](https://www.postgresql.org/docs/current/static/cube.html) | Stellt einen Datentyp für mehrdimensionale Cubes bereit. |
> | [hll](https://github.com/citusdata/postgresql-hll) | Stellt eine HyperLogLog-Datenstruktur bereit. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Gibt den Datentyp zum Speichern von Schlüssel-Wert-Paaren an. |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | Stellt Datentypen für internationale Produktnummerierungsnormen bereit. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Large Object-Wartung. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Stellt einen Datentyp für hierarchische baumähnliche Strukturen bereit. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Datentyp für die Darstellung von Liniensegmenten oder Gleitkommaintervallen. |
> | [tdigest](https://github.com/tvondra/tdigest) | Datentyp für die Onlineakkumulation von rangbasierten Statistiken wie Quantilen und gekürzten Mittelwerten |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Typ für Erste N-JSONB. |

### <a name="full-text-search-extensions"></a>Erweiterungen für die Volltextsuche

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Bietet eine Textsuch-Wörterbuchvorlage für Integerwerte. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Wörterbuchvorlage für die Textsuche für die erweiterte Verarbeitung von Synonymen. |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Ein Wörterbuch für die Textsuche, das Akzente (diakritische Zeichen) aus Lexemen entfernt |

### <a name="functions-extensions"></a>Funktionserweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funktionen für die automatische Inkrementierung von Feldern. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Bietet eine Möglichkeit, Großkreisentferungen auf der Erdoberfläche zu berechnen. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Stellt mehrere Funktionen bereit, um Ähnlichkeiten und den Abstand zwischen Zeichenfolgen zu ermitteln |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funktionen, um nachzuverfolgen, welcher Benutzer Änderungen in einer Tabelle vorgenommen hat. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Integeraggregator und -enumerator (veraltet). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Stellt Funktionen und Operatoren bereit, um Arrays mit Ganzzahlen ohne Nullen zu bearbeiten |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funktionen zur Verfolgung der letzten Änderungszeit. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Verwaltet partitionierte Tabellen nach Zeit oder ID |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Stellt Funktionen und Operatoren bereit, um die Ähnlichkeit von alphanumerischen Texten basierend auf übereinstimmenden Trigrammen zu ermitteln |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Stellt kryptografische Funktionen bereit |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funktionen für die Implementierung referentieller Integrität (veraltet). |
> | session\_analytics | Funktionen zum Abfragen von Hstore-Arrays. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Stellt Funktionen bereit, um ganze Tabellen einschließlich einer Kreuztabelle zu bearbeiten. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Ausgelöste Änderungsbenachrichtigungen. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funktionen für die Implementierung von Zeitreisen. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Generiert universell eindeutige Bezeichner (UUIDs) |

### <a name="hyperscale-citus-extensions"></a>Hyperscale (Citus)-Erweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Von Citus verteilte Datenbank. |

### <a name="index-types-extensions"></a>Indextypenerweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom-Zugriffsmethode: Index, der auf einer Signaturdatei basiert. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Gibt Beispielklassen für den GIN-Operator an, der das B-Struktur-ähnliche Verhalten für bestimmte Datentypen implementiert. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Gibt Operatorklassen für den GiST-Index an, der die B-Struktur implementiert |

### <a name="language-extensions"></a>Spracherweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Ladbare prozedurale Sprache, PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Verschiedene Erweiterungen

> [!div class="mx-tableFixed"]
> | **Erweiterung** | **Beschreibung** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Verwaltungsfunktionen für PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funktionen zum Überprüfen der Beziehungsintegrität. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Ein Modul, das Verbindungen mit anderen PostgreSQL-Datenbanken innerhalb einer Datenbanksitzung unterstützt Weitere Informationen zu dieser Erweiterung finden Sie im Abschnitt „dblink und postgres_fdw“. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper für programmfremde Daten für den Flatfile-Zugriff. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Überprüfen Sie den Inhalt von Datenbankseiten auf niedriger Ebene. |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Bietet eine Möglichkeit, um die Vorgänge im freigegebenen Puffercache in Echtzeit mitzuverfolgen |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Auftragsplaner für PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Untersuchen von free space map (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Bietet eine Möglichkeit, um Relationsdaten in den Puffercache zu laden |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Bietet eine Möglichkeit, um Ausführungsstatistiken zu allen SQL-Anweisungen nachzuverfolgen, die von einem Server ausgeführt werden Weitere Informationen zu dieser Erweiterung finden Sie im Abschnitt „pg_stat_statements“. |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | Überprüft die Informationen zur Zuordnung von Sichtbarkeiten (Visibility Map, VM) und Informationen zur Sichtbarkeit auf Seitenebene. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Bietet eine Möglichkeit zum Anzeigen von Sperrinformationen auf Zeilenebene. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Bietet eine Möglichkeit zum Anzeigen von Statistiken auf Tupelebene. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Wrapper für programmfremde Daten, um auf in externen PostgreSQL-Servern gespeicherten Daten zuzugreifen Weitere Informationen zu dieser Erweiterung finden Sie im Abschnitt „dblink und postgres_fdw“.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informationen zu TLS-/SSL-Zertifikaten |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE-Methode, die die Anzahl von Zeilen als Grenzwert akzeptiert. |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE-Methode, die die Zeit in Millisekunden als Grenzwert akzeptiert. |
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


## <a name="pg_stat_statements"></a>pg_stat_statements
Die [pg\_stat\_statements-Erweiterung](https://www.postgresql.org/docs/current/pgstatstatements.html) wird auf jedem Azure Database for PostgreSQL-Server vorab geladen,um Ihnen eine Möglichkeit zur Nachverfolgung von Ausführungsstatistiken von SQL-Anweisungen bereitzustellen.

Mit der `pg_stat_statements.track`-Einstellung wird gesteuert, welche Anweisungen von der Erweiterung gezählt werden. Der Standardwert ist `top`. Dies bedeutet, dass alle direkt von Clients ausgegebenen Anweisungen nachverfolgt werden. Die beiden anderen Nachverfolgungsebenen sind `none` und `all`. Diese Einstellung kann als Serverparameter über das [Azure-Portal](./howto-configure-server-parameters-using-portal.md) oder die [Azure CLI](./howto-configure-server-parameters-using-cli.md) konfiguriert werden.

Zwischen den von pg_stat_statements bereitgestellten Abfrageausführungsinformationen und der Auswirkung auf die Serverleistung besteht ein Kompromiss, da jede SQL-Anweisung protokolliert wird. Wenn Sie die pg_stat_statements-Erweiterung nicht aktiv verwenden, empfiehlt es sich, `pg_stat_statements.track` auf `none` festzulegen. Einige Überwachungsdienste von Drittanbietern können sich auf pg_stat_statements beziehen, um Statistiken zur Abfrageleistung zu liefern. Bestätigen Sie daher, ob dieser Fall auf Sie zutrifft.

## <a name="dblink-and-postgres_fdw"></a>„dblink“ und „postgres_fdw“

Sie können „dblink“ und „postgres\_fdw“ verwenden, um eine Verbindung von einem PostgreSQL-Server mit einem anderen PostgreSQL-Server oder mit einer anderen Datenbank auf demselben Server herzustellen.  Der empfangende Server muss Verbindungen vom sendenden Server über die Firewall zulassen.  Um diese Erweiterungen für Verbindungen zwischen Azure Database for PostgreSQL-Servern oder Hyperscale (Citus)-Servergruppen zu verwenden, aktivieren Sie **Azure-Diensten und -Ressourcen den Zugriff auf diese Servergruppe (oder diesen Server) gestatten**.  Sie müssen diese Einstellung auch aktivieren, wenn Sie die Erweiterungen für ein Loopback zum selben Server verwenden möchten.
Die Einstellung **Azure-Diensten und -Ressourcen den Zugriff auf diese Servergruppe gestatten** finden Sie auf der Azure-Portalseite für die Hyperscale (Citus)-Servergruppe unter **Netzwerk**.  Derzeit werden von einzelnen Azure Database for PostgreSQL-Servern und Hyperscale (Citus) ausgehende Verbindungen mit Ausnahme von Verbindungen mit anderen Azure Database for PostgreSQL-Servern und Hyperscale (Citus)-Servergruppen nicht unterstützt.