---
title: Konfigurieren von Serverparametern – Azure-Befehlszeilenschnittstelle – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie mit der Azure CLI-Befehlszeile die Dienstparameter in Azure Database for MariaDB konfigurieren.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4009d8047dae7bf8d9ba66566ff8797fa09a8878
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538137"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-cli"></a>Konfigurieren von Serverparametern in Azure Database for MariaDB mit der Azure CLI
Sie können Konfigurationsparameter für einen Azure Database for MariaDB-Server mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) auflisten, anzeigen und aktualisieren. Auf Serverebene ist eine Teilmenge der Engine-Konfigurationen verfügbar und kann geändert werden.

>[!Note]
> Serverparameter können global auf Serverebene aktualisiert werden. Verwenden Sie dazu die [Azure CLI](./howto-configure-server-parameters-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) oder das [Azure-Portal](./howto-server-parameters.md).

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- [Einen Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Das Befehlszeilenprogramm [Azure CLI](/cli/azure/install-azure-cli) oder Azure Cloud Shell im Browser

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Auflisten der Serverkonfigurationsparameter für Azure Database for MariaDB
Führen Sie den Befehl [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) aus, um alle änderbaren Parameter eines Servers mit ihren Werten aufzulisten.

Sie können die Serverkonfigurationsparameter für den Server **mydemoserver.mariadb.database.azure.com** in der Ressourcengruppe **myresourcegroup** auflisten.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Die Definition der einzelnen aufgeführten Parameter finden Sie in der MariaDB-Referenz im Abschnitt [Server System Variables](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Anzeigen von Details zu Serverkonfigurationsparametern
Um Details zu einem bestimmten Konfigurationsparameter für einen Server anzuzeigen, führen Sie den Befehl [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) aus.

Dieses Beispiel zeigt Details des Serverkonfigurationsparameters **slow\_query\_log** für den Server **mydemoserver.mariadb.database.azure.com** in der Ressourcengruppe **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Ändern des Werts von Serverkonfigurationsparametern
Sie können den Wert eines bestimmten Serverkonfigurationsparameters ändern und dadurch den zugrunde liegenden Konfigurationswert für die MariaDB-Server-Engine aktualisieren. Um die Konfiguration zu aktualisieren, führen Sie den Befehl [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) aus. 

So können Sie den Serverkonfigurationsparameter **slow\_query\_log** für den Server **mydemoserver.mariadb.database.azure.com** in der Ressourcengruppe **myresourcegroup** aktualisieren:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Wenn Sie den Wert eines Konfigurationsparameters zurücksetzen möchten, lassen Sie den optionalen Parameter `--value` weg. Der Dienst wendet dann den Standardwert an. Im obigen Beispiel sieht dies so aus:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Dieser Code setzt die Konfiguration **slow\_query\_log** auf den Standardwert **OFF** zurück. 

## <a name="setting-parameters-not-listed"></a>Nicht aufgeführte Einstellungsparameter
Wenn der Serverparameter, den Sie aktualisieren möchten, nicht im Azure-Portal aufgeführt ist, können Sie den Parameter optional mithilfe von `init_connect` auf Verbindungsebene festlegen. Damit werden die Serverparameter für jeden Client festgelegt, der sich mit dem Server verbindet. 

Aktualisieren Sie den Serverkonfigurationsparameter **init\_connect** des Servers **mydemoserver.mariadb.database.azure.com** in der Ressourcengruppe **myresourcegroup**, um Werte wie den Zeichensatz festzulegen.
```azurecli-interactive
az mariadb server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Arbeiten mit dem Zeitzonenparameter

### <a name="populating-the-time-zone-tables"></a>Auffüllen der Zeitzonentabellen

Die Zeitzonentabellen auf Ihrem Server können durch Aufrufen der gespeicherten Prozedur `mysql.az_load_timezone` über ein Tool wie die MariaDB-Befehlszeile oder MariaDB Workbench aufgefüllt werden.

> [!NOTE]
> Wenn Sie den Befehl `mysql.az_load_timezone` in MariaDB Workbench ausführen, müssen Sie unter Umständen zuerst den sicheren Aktualisierungsmodus mit `SET SQL_SAFE_UPDATES=0;` deaktivieren.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Sie sollten den Server neu starten, um sicherzustellen, dass die Zeitzonentabellen ordnungsgemäß aufgefüllt werden. Um den Server neu zu starten, verwenden Sie das [Azure-Portal](howto-restart-server-portal.md) oder die [Befehlszeilenschnittstelle](howto-restart-server-cli.md).

Um die verfügbaren Zeitzonenwerte anzuzeigen, führen Sie den folgenden Befehl aus:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Festlegen der globalen Zeitzone

Die globale Zeitzone kann mithilfe des Befehls [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) festgelegt werden.

Der folgende Befehl ändert den Serverkonfigurationsparameter **time\_zone** für den Server **mydemoserver.mariadb.database.azure.com** in der Ressourcengruppe **myresourcegroup** in **US/Pacific**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Festlegen der Sitzungszeitzone

Die Sitzungszeitzone kann durch Ausführen des Befehls `SET time_zone` in einem Tool wie der MariaDB-Befehlszeile oder MariaDB Workbench festgelegt werden. Im folgenden Beispiel wird die Zeitzone auf **US/Pacific** festgelegt.  

```sql
SET time_zone = 'US/Pacific';
```

Informationen zu [Datums- und Uhrzeitfunktionen](https://mariadb.com/kb/en/library/date-time-functions/) finden Sie in der MariaDB-Dokumentation.

## <a name="next-steps"></a>Nächste Schritte

- Konfigurieren von [Serverparametern im Azure-Portal](howto-server-parameters.md)
