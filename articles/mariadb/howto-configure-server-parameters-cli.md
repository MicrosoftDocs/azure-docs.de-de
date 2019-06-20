---
title: Konfigurieren der Dienstparameter in Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie mit der Azure CLI-Befehlszeile die Dienstparameter in Azure Database for MariaDB konfigurieren.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 4e0bf45f1c67a5e07d6ed632f6560d094b673c0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61040050"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Anpassen der Serverkonfigurationsparameter mithilfe der Azure CLI
Sie können Konfigurationsparameter für einen Azure Database for MariaDB-Server mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) auflisten, anzeigen und aktualisieren. Auf Serverebene ist eine Teilmenge der Engine-Konfigurationen verfügbar und kann geändert werden.

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

## <a name="working-with-the-time-zone-parameter"></a>Arbeiten mit dem Zeitzonenparameter

### <a name="populating-the-time-zone-tables"></a>Auffüllen der Zeitzonentabellen

Die Zeitzonentabellen auf Ihrem Server können durch Aufrufen der gespeicherten Prozedur `az_load_timezone` über ein Tool wie die MariaDB-Befehlszeile oder MariaDB Workbench aufgefüllt werden.

> [!NOTE]
> Wenn Sie den Befehl `az_load_timezone` in MariaDB Workbench ausführen, müssen Sie unter Umständen zuerst den sicheren Aktualisierungsmodus mit `SET SQL_SAFE_UPDATES=0;` deaktivieren.

```sql
CALL mysql.az_load_timezone();
```

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
