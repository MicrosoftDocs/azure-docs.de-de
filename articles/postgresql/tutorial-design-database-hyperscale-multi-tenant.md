---
title: 'Tutorial: Entwerfen einer mehrinstanzenfähigen Datenbank – Hyperscale (Citus) – Azure Database for PostgreSQL'
description: In diesem Tutorial erfahren Sie, wie Sie eine skalierbare mehrinstanzenfähige Anwendung mit Azure Database for PostgreSQL – Hyperscale (Citus) entwerfen und ausführen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: d8dee086f55c5596af50245b5271d98536eb7e57
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026198"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: Entwerfen einer mehrinstanzenfähigen Datenbank mithilfe von Azure Database for PostgreSQL – Hyperscale (Citus)

In diesem Tutorial verwenden Sie Azure Database for PostgreSQL – Hyperscale (Citus), um Folgendes zu lernen:

> [!div class="checklist"]
> * Erstellen einer Hyperscale (Citus)-Servergruppe
> * Verwenden des psql-Hilfsprogramms zum Erstellen eines Schemas
> * Knotenübergreifende Shard-Tabellen
> * Erfassen von Beispieldaten
> * Abfragen von Mandantendaten
> * Teilen von Daten zwischen Mandanten
> * Mandantenweises Anpassen des Schemas

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Verwenden des psql-Hilfsprogramms zum Erstellen eines Schemas

Nach dem Herstellen der Verbindung mit Azure Database for PostgreSQL – Hyperscale (Citus) mithilfe von psql können Sie ein paar einfache Aufgaben ausführen. Dieses Tutorial führt Sie durch die Erstellung einer Web-App, die Werbetreibenden das Nachverfolgen ihrer Kampagnen ermöglicht.

Die App kann von mehreren Firmen verwendet werden, erstellen wir also eine Tabelle, die die Firmen enthält, und eine weitere für ihre Kampagnen. Führen Sie in der psql-Konsole diese Befehle aus:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Jede Kampagne zahlt für die Ausführung von Werbeeinblendungen. Fügen Sie außerdem eine Tabelle für Werbeeinblendungen hinzu, indem Sie nach dem Code oben den folgenden Code in psql ausführen:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Schließlich verfolgen wir die Statistiken über Klicks und Eindrücke für die einzelnen Werbeeinblendungen nach:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Sie können jetzt die neu erstellten Tabellen in der Liste der Tabellen in psql anzeigen, indem Sie dies ausführen:

```postgres
\dt
```

Mehrmandantenfähige Anwendungen können Eindeutigkeit nur mandantenweise durchsetzen; dies ist der Grund dafür, warum alle Primär- und Fremdschlüssel die Firmen-ID enthalten.

## <a name="shard-tables-across-nodes"></a>Knotenübergreifende Shard-Tabellen

Eine Hyperscalebereitstellung speichert Tabellenzeilen basierend auf dem Wert einer vom Benutzer zugeordneten Spalte auf verschiedenen Knoten. Diese „Verteilungsspalte“ gibt an, welcher Mandant welche Zeilen besitzt.

Legen wir die Verteilungsspalte auf „company\_id“ fest, den Mandantenbezeichner. Führen Sie in psql diese Funktionen aus:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="ingest-sample-data"></a>Erfassen von Beispieldaten

Laden Sie jetzt außerhalb von psql, an einer normalen Befehlszeile, Beispieldatasets herunter:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Führen Sie – jetzt wieder in psql – ein Massenladen der Daten aus. Achten Sie darauf, psql in dem gleichen Verzeichnis auszuführen, in das Sie die Datendateien heruntergeladen hatten.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Diese Daten werden jetzt über die Workerknoten verteilt.

## <a name="query-tenant-data"></a>Abfragen von Mandantendaten

Wenn die Anwendung Daten für einen einzelnen Mandanten anfordert, kann die Datenbank die Abfrage auf einem einzelnen Workerknoten ausführen. Abfragen für einzelne Mandanten führen eine Filterung nach einer einzelnen Mandanten-ID durch. Beispielsweise filtert die folgende Abfrage `company_id = 5` nach Werbeeinblendungen und Eindrücken. Versuchen Sie, sie in psql auszuführen, um die Ergebnisse anzuzeigen.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Teilen von Daten zwischen Mandanten

Bis jetzt wurden alle Tabellen nach `company_id` verteilt, aber einige Daten „gehören“ nicht selbstverständlich zu einem bestimmten Mandanten und können geteilt werden. Beispielsweise ist es denkbar, dass alle Firmen auf der Plattform für Beispielwerbeeinblendungen geografische Informationen für ihre Zielgruppe auf der Grundlage der IP-Adressen abrufen möchten.

Erstellen Sie eine Tabelle zum Speichern von gemeinsam verwendeten geografischen Informationen. Führen Sie die folgenden Befehle in psql aus:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Machen Sie als Nächstes `geo_ips` zu einer „Verweistabelle“, und speichern Sie eine Kopie der Tabelle auf jedem Workerknoten.

```sql
SELECT create_reference_table('geo_ips');
```

Laden Sie sie mit Beispieldaten. Achten Sie darauf, diesen Befehl in psql in dem Verzeichnis auszuführen, in das Sie das Dataset heruntergeladen hatten.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Das Verknüpfen der Klicktabelle mit „geo\_ips“ ist auf allen Knoten wirksam.
Hier ist eine Verknüpfung, mit der Sie die Standorte aller Personen finden, die auf eine Werbeeinblendung geklickt haben:
290. Versuchen Sie, die Abfrage in psql auszuführen.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Mandantenweises Anpassen des Schemas

Für jeden Mandanten kann es erforderlich sein, besondere Informationen zu speichern, die von anderen nicht benötigt werden. Allerdings verwenden alle Mandanten eine gemeinsame Infrastruktur mit einem identischen Datenbankschema. Wo können die zusätzlichen Daten untergebracht werden?

Ein Trick besteht darin, einen Spaltentyp mit offenem Ende zu verwenden, wie JSONB von PostgreSQL.  Unser Schema weist ein JSONB-Feld in `clicks` mit dem Namen `user_data` auf.
Eine Firma (z.B. Firma 5) kann die Spalte verwenden, um nachzuverfolgen, ob der Benutzer von einem mobilen Gerät aus zugreift.

Mit dieser Abfrage finden Sie heraus, wer öfter klickt – mobile oder herkömmliche Benutzer:

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Wir können diese Abfrage für eine einzelne Firma optimieren, indem wir einen [Teilindex](https://www.postgresql.org/docs/current/static/indexes-partial.html) erstellen.

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Allgemeiner ausgedrückt können wir [GIN-Indizes](https://www.postgresql.org/docs/current/static/gin-intro.html) für alle Schlüssel und Wert in der Spalte erstellen.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Servergruppe erstellt. Wenn Sie diese Ressourcen nicht mehr benötigen, löschen Sie die Servergruppe. Klicken Sie auf der Seite *Übersicht* für Ihre Servergruppe auf die Schaltfläche *Löschen*. Wenn Sie auf einer Popupseite dazu aufgefordert werden, bestätigen Sie den Namen der Servergruppe, und klicken Sie abschließend auf die Schaltfläche *Löschen*.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie eine Hyperscale (Citus)-Servergruppe bereitstellen. Sie haben mithilfe von psql eine Verbindung mit ihr hergestellt, haben ein Schema erstellt und Daten verteilt. Sie haben gelernt, Daten sowohl innerhalb von als auch zwischen Mandanten abzufragen und das Schema mandantenweise anzupassen.

- Weitere Informationen zu [Knotentypen](./concepts-hyperscale-nodes.md) von Servergruppen
- Bestimmen der besten [Anfangsgröße](howto-hyperscale-scale-initial.md) für Ihre Servergruppe
