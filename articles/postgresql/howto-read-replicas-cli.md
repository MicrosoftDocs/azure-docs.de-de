---
title: Verwalten von Lesereplikaten – Azure CLI, REST-API – Azure Database for PostgreSQL – Einzelserver
description: Erfahren Sie, wie Sie Lesereplikate für Azure Database for PostgreSQL verwalten – Einzelserver über die Azure CLI und die REST-API.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2fa8794066739302d2f32acb13c936c524dc89a8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422347"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Erstellen und Verwalten von Lesereplikaten über die Azure CLI und die REST-API

In diesem Artikel erfahren Sie, wie Sie Lesereplikate in Azure Database for PostgreSQL über die Azure CLI und die REST-API erstellen und verwalten. Weitere Informationen zu Lesereplikaten finden Sie in der [Übersicht](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Azure-Replikationsunterstützung
[Lesereplikate](concepts-read-replicas.md) und [logische Decodierung](concepts-logical.md) sind beide vom Write-Ahead-Protokoll (WAL) von Postgres abhängig. Diese beiden Features erfordern unterschiedliche Ebenen der Protokollierung durch Postgres. Die logische Decodierung erfordert einen höheren Protokolliergrad als Lesereplikate.

Um den richtigen Protokolliergrad zu konfigurieren, verwenden Sie den Parameter für die Unterstützung der Azure-Replikation. Für die Unterstützung der Azure-Replikation gibt es drei Einstellungsoptionen:

* **Off**: Speichert am wenigsten Informationen im Write-Ahead-Protokoll. Diese Einstellung ist auf den meisten Azure Database for PostgreSQL-Servern nicht verfügbar.  
* **Replica**: Ausführlichere Informationen als bei **Off**. Dies ist der mindestens erforderliche Protokolliergrad, damit [Lesereplikate](concepts-read-replicas.md) funktionieren. Auf den meisten Servern ist dies die Standardeinstellung.
* **Logical**: Noch ausführlichere Informationen als bei **Replica**. Dies ist der mindestens erforderliche Protokolliergrad, damit die logische Decodierung funktioniert. Lesereplikate funktionieren bei dieser Einstellung ebenfalls.


> [!NOTE]
> Beim Bereitstellen von Lesereplikaten für persistente, sehr schreibintensive primäre Workloads kann die Verzögerung der Replikation immer weiter anwachsen und erreicht möglicherweise den Stand des primären Servers gar nicht mehr. Damit kann auch die Speicherauslastung auf dem primären Server ansteigen, da die WAL-Dateien erst gelöscht werden, wenn sie im Replikat empfangen wurden.

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Sie können Lesereplikate mithilfe der Azure CLI erstellen und verwalten.

### <a name="prerequisites"></a>Voraussetzungen

- [Installieren der Azure CLI 2.0](/cli/azure/install-azure-cli)
- Ein [Azure Database for PostgreSQL-Server](quickstart-create-server-up-azure-cli.md), der als primärer Server verwendet wird.


### <a name="prepare-the-primary-server"></a>Vorbereiten des primären Servers

1. Überprüfen Sie den Wert `azure.replication_support` des primären Servers. Er sollte mindestens REPLICA sein, damit Lesereplikate funktionieren.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Wenn `azure.replication_support` nicht mindestens REPLICA ist, legen Sie dies fest. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Starten Sie den Server neu, um die Änderung zu übernehmen.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

Der Befehl [az postgres server replica create](/cli/azure/postgres/server/replica#az-postgres-server-replica-create) erfordert die folgenden Parameter:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| name | mydemoserver-replica | Der Name des neuen Replikatservers, der erstellt wird. |
| source-server | mydemoserver | Der Name oder die Ressourcen-ID des vorhandenen primären Servers, von dem die Replikation erfolgt. Verwenden Sie die Ressourcen-ID, wenn sich Replikat- und Masterressourcengruppen unterscheiden sollen. |

Im folgenden CLI-Beispiel wird das Replikat in der gleichen Region wie der Master erstellt.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Verwenden Sie den `--location`-Parameter, um ein regionsübergreifendes Lesereplikat zu erstellen. Im folgenden CLI-Beispiel wird das Replikat in der Region „USA, Westen“ erstellt.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md). 

Wenn Sie den Parameter `azure.replication_support` auf einem universellen oder arbeitsspeicheroptimierten primären Server nicht auf **REPLICA** festgelegt und den Server nicht neu gestartet haben, erhalten Sie eine Fehlermeldung. Führen Sie diese beiden Schritte aus, bevor Sie ein Replikat erstellen.

> [!IMPORTANT]
> Lesen Sie den Abschnitt zu [Überlegungen zur Übersicht über Lesereplikate](concepts-read-replicas.md#considerations).
>
> Bevor eine Primärservereinstellung auf einen neuen Wert aktualisiert wird, aktualisieren Sie die Replikateinstellung auf den gleichen oder einen größeren Wert. Diese Aktion sorgt dafür, dass das Replikat mit allen Änderungen auf dem Masterserver Schritt halten kann.

### <a name="list-replicas"></a>Auflisten von Replikaten
Sie können die Liste der Replikate eines primären Servers mit dem Befehl [az postgres server replica list](/cli/azure/postgres/server/replica#az-postgres-server-replica-list) anzeigen.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Beenden der Replikation auf einem Replikatserver
Sie können die Replikation zwischen einem primären Server und einem Lesereplikat mit dem Befehl [az postgres server replica stop](/cli/azure/postgres/server/replica#az-postgres-server-replica-stop) beenden.

Das Beenden der Replikation zwischen einem primären Server und einem Lesereplikat kann nicht mehr rückgängig gemacht werden. Das Lesereplikat wird zu einem eigenständigen Server, der sowohl Lese- als auch Schreibvorgänge unterstützt. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Löschen eines primären oder Replikatservers
Wenn Sie einen primären oder Replikatserver löschen möchten, verwenden Sie den Befehl [az postgres server delete](/cli/azure/postgres/server#az-postgres-server-delete).

Wenn Sie einen primären Server löschen, wird die Replikation auf allen Lesereplikaten beendet. Die Lesereplikate werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST-API
Sie können Lesereplikate mithilfe der [ Azure-REST-API](/rest/api/azure/) erstellen und verwalten.

### <a name="prepare-the-primary-server"></a>Vorbereiten des primären Servers

1. Überprüfen Sie den Wert `azure.replication_support` des primären Servers. Er sollte mindestens REPLICA sein, damit Lesereplikate funktionieren.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Wenn `azure.replication_support` nicht mindestens REPLICA ist, legen Sie dies fest.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Starten Sie den Server neu](/rest/api/postgresql/servers/restart), um die Änderung zu übernehmen.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats
Sie können ein Lesereplikat mithilfe der [Create-API](/rest/api/postgresql/servers/create) erstellen:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md). 

Wenn Sie den Parameter `azure.replication_support` auf einem universellen oder arbeitsspeicheroptimierten primären Server nicht auf **REPLICA** festgelegt und den Server nicht neu gestartet haben, erhalten Sie eine Fehlermeldung. Führen Sie diese beiden Schritte aus, bevor Sie ein Replikat erstellen.

Ein Replikat wird mit den gleichen Compute- und Speichereinstellungen erstellt wie der Master. Nachdem ein Replikat erstellt wurde, können mehrere Einstellungen unabhängig vom primären Server geändert werden: die Computegeneration, die virtuellen Kerne, der Speicher und der Aufbewahrungszeitraum für Sicherungen. Auch der Tarif kann unabhängig geändert werden, allerdings nicht in den oder aus dem Tarif „Basic“.


> [!IMPORTANT]
> Bevor eine Primärservereinstellung auf einen neuen Wert aktualisiert wird, aktualisieren Sie die Replikateinstellung auf den gleichen oder einen größeren Wert. Diese Aktion sorgt dafür, dass das Replikat mit allen Änderungen auf dem Masterserver Schritt halten kann.

### <a name="list-replicas"></a>Auflisten von Replikaten
Sie können die Replikatliste eines primären Servers mithilfe der [Replica List-API](/rest/api/postgresql/replicas/listbyserver) anzeigen:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Beenden der Replikation auf einem Replikatserver
Sie können die Replikation zwischen einem primären Server und einem Lesereplikat mithilfe der [Update-API](/rest/api/postgresql/servers/update) beenden.

Das Beenden der Replikation zwischen einem primären Server und einem Lesereplikat kann nicht mehr rückgängig gemacht werden. Das Lesereplikat wird zu einem eigenständigen Server, der sowohl Lese- als auch Schreibvorgänge unterstützt. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Löschen eines primären oder Replikatservers
Zum Löschen eines primären oder Replikatservers verwenden Sie die [Delete-API](/rest/api/postgresql/servers/delete):

Wenn Sie einen primären Server löschen, wird die Replikation auf allen Lesereplikaten beendet. Die Lesereplikate werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Lesereplikate in Azure Database for PostgreSQL](concepts-read-replicas.md).
* Erhalten Sie Informationen zum [Erstellen und Verwalten von Lesereplikaten im Azure-Portal](howto-read-replicas-portal.md).