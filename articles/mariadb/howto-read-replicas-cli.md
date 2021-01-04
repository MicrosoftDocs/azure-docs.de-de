---
title: Verwalten von Lesereplikaten – Azure-Befehlszeilenschnittstelle, REST-API – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie mithilfe der Azure CLI und REST-API Lesereplikate in Azure Database for MariaDB einrichten und verwalten.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 70da1e9c70bf80737065362c68781652dd9ab6e5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023634"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Erstellen und Verwalten von Lesereplikaten in Azure Database for MariaDB mithilfe der Azure CLI und REST-API

In diesem Artikel wird beschrieben, wie Sie mithilfe der Azure CLI und REST-API Lesereplikate in Azure Database for MariaDB erstellen und verwalten.

## <a name="azure-cli"></a>Azure CLI
Sie können Lesereplikate mithilfe der Azure CLI erstellen und verwalten.

### <a name="prerequisites"></a>Voraussetzungen

- [Installieren der Azure CLI 2.0](/cli/azure/install-azure-cli)
- Ein [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-portal.md), der als Quellserver verwendet wird 

> [!IMPORTANT]
> Das Feature für Lesereplikate ist nur für Azure Database for MariaDB-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Quellserver einer der folgenden Tarife festgelegt ist.

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

> [!IMPORTANT]
> Wenn Sie ein Replikat für eine Quelle erstellen, die keine vorhandenen Replikate hat, startet die Quelle zunächst neu, um sich auf die Replikation vorzubereiten. Beachten Sie dies, und führen Sie diese Vorgänge nicht zu Spitzenzeiten durch.

Ein Lesereplikatserver kann mit dem folgenden Befehl erstellt werden:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Für den Befehl `az mariadb server replica create` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| name | mydemoreplicaserver | Der Name des neuen Replikatservers, der erstellt wird. |
| source-server | mydemoserver | Der Name oder die ID des vorhandenen Quellservers, der repliziert werden soll |

Verwenden Sie den `--location`-Parameter, um ein regionsübergreifendes Lesereplikat zu erstellen. 

Im folgenden CLI-Beispiel wird das Replikat in der Region „USA, Westen“ erstellt.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md). 

> [!NOTE]
> Lesereplikate werden mit der gleichen Serverkonfiguration wie der Masterserver erstellt. Die Replikatserverkonfiguration kann nach der Erstellung geändert werden. Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den Quellserver, damit das Replikat über genügend Kapazität verfügt.

### <a name="list-replicas-for-a-source-server"></a>Auflisten von Replikaten für einen Quellserver

Führen Sie den folgenden Befehl aus, um alle Replikate für einen bestimmten Quellserver anzuzeigen: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Für den Befehl `az mariadb server replica list` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| Servername | mydemoserver | Der Name oder die ID des Quellservers. |

### <a name="stop-replication-to-a-replica-server"></a>Beenden der Replikation auf einem Replikatserver

> [!IMPORTANT]
> Das Beenden der Replikation auf einem Server kann nicht rückgängig gemacht werden. Wenn die Replikation zwischen einer Quelle und dem Replikat beendet wurde, kann dies nicht rückgängig gemacht werden. Der Replikatserver wird zu einem eigenständigen Server und unterstützt nun Lese- und Schreibvorgänge. Der Server kann nicht wieder in ein Replikat umgewandelt werden.

Die Replikation auf einem Lesereplikatserver kann mit dem folgenden Befehl beendet werden:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Für den Befehl `az mariadb server replica stop` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver enthalten ist.  |
| name | mydemoreplicaserver | Der Name des Replikatservers, auf dem die Replikation beendet werden soll. |

### <a name="delete-a-replica-server"></a>Löschen eines Replikatservers

Sie können einen Lesereplikatserver löschen, indem Sie den Befehl **[az mariadb server delete](/cli/azure/mariadb/server)** ausführen.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Löschen eines Quellservers

> [!IMPORTANT]
> Wenn Sie einen Quellserver löschen, wird die Replikation auf allen Replikatservern beendet und der Quellserver selbst gelöscht. Replikatserver werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

Sie können den Befehl **[az mariadb server delete](/cli/azure/mariadb/server)** ausführen, um einen Quellserver zu löschen.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST-API
Sie können Lesereplikate mithilfe der [ Azure-REST-API](/rest/api/azure/) erstellen und verwalten.

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats
Sie können ein Lesereplikat mithilfe der [Create-API](/rest/api/mariadb/servers/create) erstellen:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md). 

Wenn Sie den Parameter `azure.replication_support` auf einem universellen oder arbeitsspeicheroptimierten Quellserver nicht auf **REPLICA** festgelegt und den Server nicht neu gestartet haben, tritt ein Fehler auf. Führen Sie diese beiden Schritte aus, bevor Sie ein Replikat erstellen.

Ein Replikat wird mit den gleichen Compute- und Speichereinstellungen erstellt wie der Master. Nach dem Erstellen des Replikats können einige der Einstellungen unabhängig vom Quellserver geändert werden: die Computegeneration, die virtuellen Kerne, der Speicher und der Aufbewahrungszeitraum für Sicherungen. Auch der Tarif kann unabhängig geändert werden, allerdings nicht in den oder aus dem Tarif „Basic“.


> [!IMPORTANT]
> Ändern Sie vor der Aktualisierung einer Quellservereinstellung auf einen neuen Wert die entsprechende Replikateinstellung in den gleichen oder einen höheren Wert. Diese Aktion sorgt dafür, dass das Replikat mit allen Änderungen auf dem Masterserver Schritt halten kann.

### <a name="list-replicas"></a>Auflisten von Replikaten
Sie können die Replikatliste eines Quellservers mithilfe der [API zum Auflisten von Replikaten](/rest/api/mariadb/replicas/listbyserver) anzeigen:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Beenden der Replikation auf einem Replikatserver
Sie können die Replikation zwischen einem Quellserver und einem Lesereplikat mithilfe der [Update-API](/rest/api/mariadb/servers/update) beenden.

Das Beenden der Replikation zwischen einem Quellserver und einem Lesereplikat kann nicht rückgängig gemacht werden. Das Lesereplikat wird zu einem eigenständigen Server, der sowohl Lese- als auch Schreibvorgänge unterstützt. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>Löschen eines Quell- oder Replikatservers
Zum Löschen eines Quell- oder Replikatservers wird die [Lösch-API](/rest/api/mariadb/servers/delete) verwendet:

Wenn Sie einen Quellserver löschen, wird die Replikation in alle Lesereplikate beendet. Die Lesereplikate werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Lesereplikaten](concepts-read-replicas.md)