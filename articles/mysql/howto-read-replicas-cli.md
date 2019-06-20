---
title: Erstellen und Verwalten von Lesereplikaten in Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie per Azure CLI Lesereplikate in Azure Database for MySQL einrichten und verwalten.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: ba8af55f7467e361136e4b0c57c97b4fa187cec0
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304956"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Informationen zum Erstellen und Verwalten von Lesereplikaten in Azure Database for MySQL mithilfe der Azure CLI

In diesem Artikel erfahren Sie, wie Sie mit der Azure CLI Lesereplikate im Azure Database for MySQL-Dienst erstellen und verwalten, die sich in der gleichen Azure-Region wie der Masterserver befinden.

> [!IMPORTANT]
> Sie können ein Lesereplikat in derselben Region wie Ihren Masterserver oder in einer anderen beliebigen Azure-Region erstellen. Die regionsübergreifende Replikation befindet sich derzeit in der öffentlichen Vorschauversion.

## <a name="prerequisites"></a>Voraussetzungen

- [Installieren der Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Ein [Azure Database for MySQL-Server](quickstart-create-mysql-server-database-using-azure-portal.md), der als Masterserver verwendet wird 

> [!IMPORTANT]
> Das Feature für Lesereplikate ist nur für Azure Database for MySQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Masterserver einer dieser Tarife festgelegt ist.

## <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

Ein Lesereplikatserver kann mit dem folgenden Befehl erstellt werden:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Für den Befehl `az mysql server replica create` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| name | mydemoreplicaserver | Der Name des neuen Replikatservers, der erstellt wird. |
| source-server | mydemoserver | Der Name oder die ID des vorhandenen Masterservers, von dem die Replikation erfolgt. |

Verwenden Sie den `--location`-Parameter, um ein regionsübergreifendes Lesereplikat zu erstellen. Im folgenden CLI-Beispiel wird das Replikat in der Region „USA, Westen“ erstellt.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Lesereplikate werden mit der gleichen Serverkonfiguration wie der Masterserver erstellt. Die Replikatserverkonfiguration kann nach der Erstellung geändert werden. Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den Masterserver, damit das Replikat über genügend Kapazität verfügt.

## <a name="stop-replication-to-a-replica-server"></a>Beenden der Replikation auf einem Replikatserver

> [!IMPORTANT]
> Das Beenden der Replikation auf einem Server kann nicht rückgängig gemacht werden. Wenn die Replikation zwischen einem Master und dem Replikat beendet wurde, kann dies nicht rückgängig gemacht werden. Der Replikatserver wird zu einem eigenständigen Server und unterstützt nun Lese- und Schreibvorgänge. Der Server kann nicht wieder in ein Replikat umgewandelt werden.

Die Replikation auf einem Lesereplikatserver kann mit dem folgenden Befehl beendet werden:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Für den Befehl `az mysql server replica stop` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver enthalten ist.  |
| name | mydemoreplicaserver | Der Name des Replikatservers, auf dem die Replikation beendet werden soll. |

## <a name="delete-a-replica-server"></a>Löschen eines Replikatservers

Zum Löschen eines Lesereplikatservers kann der Befehl **[az mysql server delete](/cli/azure/mysql/server)** ausgeführt werden.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Löschen eines Masterservers

> [!IMPORTANT]
> Wenn Sie einen Masterserver löschen, wird die Replikation auf allen Replikatservern beendet und der Masterserver selbst gelöscht. Replikatserver werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

Zum Löschen eines Masterservers können Sie den Befehl **[az mysql server delete](/cli/azure/mysql/server)** ausführen.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Auflisten von Replikaten für einen Masterserver

Führen Sie den folgenden Befehl aus, um alle Replikate für einen bestimmten Masterserver anzuzeigen: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Für den Befehl `az mysql server replica list` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| Servername | mydemoserver | Der Name oder die ID des Masterservers. |

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Lesereplikaten](concepts-read-replicas.md)
