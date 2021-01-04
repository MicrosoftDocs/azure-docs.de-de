---
title: Logische Decodierung – Azure Database for PostgreSQL (Einzelserver)
description: Im Folgenden werden die logische Decodierung und wal2json für Change Data Capture in Azure Database for PostgreSQL (Einzelserver) beschrieben.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 0ea58050c5dc952392df56b4fb556a0998eef165
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938901"
---
# <a name="logical-decoding"></a>Logische Decodierung

Die [logische Decodierung in PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) ermöglicht das Streamen von Datenänderungen an externe Consumer. Die logische Decodierung wird besonders häufig für das Ereignisstreaming und Change Data Capture-Szenarien verwendet.

Für die logische Decodierung wird ein Ausgabe-Plug-In verwendet, um das Write-Ahead-Protokoll (WAL) von Postgres in ein lesbares Format zu konvertieren. Azure Database for PostgreSQL bietet die Ausgabe-Plug-Ins [wal2json](https://github.com/eulerto/wal2json), [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) und „pgoutput“. „pgoutput“ wird ab der PostgreSQL-Version 10 bereitgestellt.

Einen Überblick über die Funktionsweise der logischen Decodierung von Postgres finden Sie in [unserem Blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421). 

> [!NOTE]
> Die logische Replikation mit PostgreSQL-Veröffentlichung/-Abonnement wird für Azure Database for PostgreSQL-Einzelserver nicht unterstützt.


## <a name="set-up-your-server"></a>Einrichten des Servers 
Die logische Decodierung und [Lesereplikate](concepts-read-replicas.md) erhalten Informationen jeweils vom Write-Ahead-Protokoll (Write Ahead Log, WAL) von Postgres. Die beiden Features benötigen unterschiedliche Protokolliergrade von Postgres. Die logische Decodierung erfordert einen höheren Protokolliergrad als Lesereplikate.

Um den richtigen Protokolliergrad zu konfigurieren, verwenden Sie den Parameter für die Unterstützung der Azure-Replikation. Für die Unterstützung der Azure-Replikation gibt es drei Einstellungsoptionen:

* **Off**: Speichert am wenigsten Informationen im Write-Ahead-Protokoll. Diese Einstellung ist auf den meisten Azure Database for PostgreSQL-Servern nicht verfügbar.  
* **Replica**: Ausführlichere Informationen als bei **Off**. Dies ist der mindestens erforderliche Protokolliergrad, damit [Lesereplikate](concepts-read-replicas.md) funktionieren. Auf den meisten Servern ist dies die Standardeinstellung.
* **Logical**: Noch ausführlichere Informationen als bei **Replica**. Dies ist der mindestens erforderliche Protokolliergrad, damit die logische Decodierung funktioniert. Lesereplikate funktionieren bei dieser Einstellung ebenfalls.


### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

1. Legen Sie „azure.replication_support“ auf `logical` fest.
   ```azurecli-interactive
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. Starten Sie den Server neu, um die Änderung zu übernehmen.
   ```azurecli-interactive
   az postgres server restart --resource-group mygroup --name myserver
   ```
3. Wenn Sie Postgres 9.5 oder 9.6 ausführen und öffentlichen Netzwerkzugriff nutzen, fügen Sie die Firewallregel hinzu, um die öffentliche IP-Adresse des Clients, von dem aus Sie die logische Replikation ausführen, einzuschließen. Der Name der Firewallregel muss **_replrule** enthalten. Beispiel: *test_replrule*. Erstellen Sie mit dem Befehl [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) eine neue Firewallregel auf dem Server. 

### <a name="using-azure-portal"></a>Verwenden des Azure-Portals

1. Legen Sie die Azure-Replikationsunterstützung auf **logical** (logisch) fest. Wählen Sie **Speichern** aus.

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="Azure Database for PostgreSQL: Replikation: Azure-Replikationsunterstützung":::

2. Starten Sie den Server neu, um die Änderung zu übernehmen, indem Sie **Ja** auswählen.

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="Azure Database for PostgreSQL – Replikation – Neustart bestätigen":::

3. Wenn Sie Postgres 9.5 oder 9.6 ausführen und öffentlichen Netzwerkzugriff nutzen, fügen Sie die Firewallregel hinzu, um die öffentliche IP-Adresse des Clients, von dem aus Sie die logische Replikation ausführen, einzuschließen. Der Name der Firewallregel muss **_replrule** enthalten. Beispiel: *test_replrule*. Klicken Sie anschließend auf **Speichern**.

   :::image type="content" source="./media/concepts-logical/client-replrule-firewall.png" alt-text="Azure Database for PostgreSQL: Replikation – Hinzufügen einer Firewallregel":::

## <a name="start-logical-decoding"></a>Starten der logischen Decodierung

Die logische Decodierung kann über das Streamingprotokoll oder die SQL-Schnittstelle genutzt werden. Beide Methoden verwenden [Replikationsslots](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Ein Slot stellt einen Datenstrom von Änderungen aus einem Singleton dar.

Die Verwendung eines Replikationsslots erfordert die Replikationsberechtigungen von Postgres. Derzeit ist die Replikationsberechtigung nur für den Serveradministrator verfügbar. 

### <a name="streaming-protocol"></a>Streamingprotokoll
Häufig ist das Verarbeiten von Änderungen mithilfe des Streamingprotokolls vorzuziehen. Sie können einen eigenen Consumer/Connector erstellen oder ein Tool wie [Debezium](https://debezium.io/) verwenden. 

In der wal2json-Dokumentation finden Sie [ein Beispiel für die Verwendung des Streamingprotokolls mit pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>SQL-Schnittstelle
Im folgenden Beispiel wird die SQL-Schnittstelle mit dem wal2json-Plug-In verwendet.
 
1. Erstellen Sie einen Slot.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Geben Sie SQL-Befehle aus. Beispiel:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Verarbeiten Sie die Änderungen.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Die Ausgabe sieht wie folgt aus:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Löschen Sie den Slot, wenn Sie ihn nicht mehr benötigen.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Überwachen von Slots

Sie müssen die logische Decodierung überwachen. Alle nicht verwendeten Replikationsslots müssen gelöscht werden. Slots halten Verbindungen zu den Postgres-WAL-Protokollen und den relevanten Systemkatalogen aufrecht, bis Änderungen von einem Consumer gelesen wurden. Wenn Ihr Consumer ausfällt oder nicht ordnungsgemäß konfiguriert wurde, werden die nicht verarbeiteten Protokolle aufbewahrt und füllen den Speicher. Außerdem erhöhen nicht verarbeitete Protokolle das Risiko von Transaktions-ID-Umläufen. Beide Situationen können dazu führen, dass der Server nicht mehr verfügbar ist. Daher ist es wichtig, dass die logischen Replikationsslots kontinuierlich verarbeitet werden. Wenn ein logischer Replikationsslot nicht mehr verwendet wird, löschen Sie ihn sofort.

In der Spalte „active“ (aktiv) in der Ansicht „pg_replication_slots“ wird angegeben, ob ein Consumer mit einem Slot verbunden ist.
```SQL
SELECT * FROM pg_replication_slots;
```

[Legen Sie Warnungen](howto-alert-on-metric.md) für die Metriken *Verwendeter Speicher* und *Maximale Verzögerung zwischen Replikaten* fest, damit Sie benachrichtigt werden, wenn diese Werte die normalen Schwellenwerte überschreiten. 

> [!IMPORTANT]
> Sie müssen nicht verwendete Replikationsslots löschen. Andernfalls kann es vorkommen, dass der Server nicht mehr verfügbar wird.

## <a name="how-to-drop-a-slot"></a>Löschen von Slots
Wenn Sie einen Replikationsslot nicht aktiv nutzen, sollten Sie ihn löschen.

So löschen Sie einen Replikationsslot namens `test_slot` mithilfe von SQL
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Wenn Sie die logische Decodierung nicht mehr verwenden, ändern Sie „azure.replication_support“ zurück in `replica` oder `off`. Die von `logical` verwalteten WAL-Details sind ausführlicher und sollten deaktiviert werden, wenn die logische Decodierung nicht verwendet wird. 

 
## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [logischen Decodierung](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html) finden Sie in der Postgres-Dokumentation.
* Wenden Sie sich an [unser Team](mailto:AskAzureDBforPostgreSQL@service.microsoft.com), wenn Sie Fragen zur logischen Decodierung haben.
* Informieren Sie sich über [Lesereplikate](concepts-read-replicas.md).

