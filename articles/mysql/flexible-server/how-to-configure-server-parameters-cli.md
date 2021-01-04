---
title: 'Konfigurieren von Serverparametern – Azure CLI – Azure Database for MySQL: Flexible Server'
description: 'In diesem Artikel wird beschrieben, wie Sie mit der Azure CLI die Dienstparameter in Azure Database for MySQL: Flexible Server konfigurieren.'
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58e7c024d6494aee745884997e42b527c51ab237
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489538"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Konfigurieren von Serverparametern für Azure Database for MySQL: Flexible Server mit der Azure CLI

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Sie können Konfigurationsparameter für eine Instanz von Azure Database for MySQL: Flexible Server mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) auflisten, anzeigen und aktualisieren. Die Serverparameter werden beim Erstellen des Servers mit einem Standardwert und einem empfohlenen Wert konfiguriert.  

In diesem Artikel wird beschrieben, wie Serverparameter mithilfe der Azure CLI aufgelistet, angezeigt und aktualisiert werden können.

>[!Note]
> Serverparameter können mithilfe der [Azure CLI](./how-to-configure-server-parameters-cli.md) oder des [Azure-Portals](./how-to-configure-server-parameters-portal.md) auf Serverebene global aktualisiert werden.

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- [Eine Azure Database for MySQL: Flexible Server-Instanz](quickstart-create-server-cli.md)
- Das Befehlszeilenprogramm [Azure CLI](/cli/azure/install-azure-cli) oder Azure Cloud Shell im Browser

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Auflisten von Serverparametern für Azure Database for MySQL: Flexible Server
Um alle Parameter in einem Server mit ihren Werten aufzulisten, führen Sie den Befehl [az mysql flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter) aus.

Sie können die Serverparameter für den Server **mydemoserver.mysql.database.azure.com** in der Ressourcengruppe **myresourcegroup** auflisten.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Die Definition der einzelnen aufgeführten Parameter finden Sie in der MySQL-Referenz im Abschnitt [Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-parameter-details"></a>Anzeigen von Serverparameterdetails
Um Details zu einem bestimmten Parameter für einen Server anzuzeigen, führen Sie den Befehl [az mysql flexible-server parameter show](/cli/azure/mysql/flexible-server/parameter) aus.

Dieses Beispiel zeigt Details des Serverparameters **slow\_query\_log** für den Server **mydemoserver.mysql.database.azure.com** in der Ressourcengruppe **myresourcegroup**.
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Ändern eines Serverparameterwerts
Sie können auch den Wert eines bestimmten Serverparameters ändern und dadurch den zugrunde liegenden Konfigurationswert für die MySQL-Server-Engine aktualisieren. Um den Serverparameter zu aktualisieren, verwenden Sie den Befehl [az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter). 

So aktualisieren Sie den Serverparameter **slow\_query\_log** für den Server **mydemoserver.mysql.database.azure.com** in der Ressourcengruppe **myresourcegroup**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Wenn Sie den Wert eines Parameters zurücksetzen möchten, lassen Sie den optionalen Parameter `--value` weg. Der Dienst übernimmt dann den Standardwert. Im obigen Beispiel sieht dies so aus:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Dieser Code setzt **slow\_query\_log** auf den Standardwert **OFF** zurück. 

## <a name="setting-non-modifiable-server-parameters"></a>Festlegen nicht änderbarer Serverparameter

Wenn der Serverparameter, den Sie aktualisieren möchten, nicht geändert werden kann, können Sie den Parameter optional mithilfe von `init_connect` auf Verbindungsebene festlegen. Damit werden die Serverparameter für jeden Client, der mit dem Server verbinden wird, festgelegt. 

Aktualisieren Sie den Serverparameter **init\_connect** des Servers **mydemoserver.mysql.database.azure.com** in der Ressourcengruppe **myresourcegroup** , um Werte wie den Zeichensatz festzulegen.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect` kann zum Ändern von Parametern verwendet werden, für die auf Sitzungsebene keine SUPER-Berechtigungen erforderlich sind. Führen Sie zum Überprüfen, ob Sie den Parameter mit `init_connect` festlegen können, den Befehl `set session parameter_name=YOUR_DESIRED_VALUE;` aus. Falls ein Fehler der Art **Zugriff verweigert; Sie benötigen SUPER-Berechtigungen** angezeigt wird, ist das Festlegen des Parameters mit „init_connect“ nicht möglich.

## <a name="working-with-the-time-zone-parameter"></a>Arbeiten mit dem Zeitzonenparameter

### <a name="populating-the-time-zone-tables"></a>Auffüllen der Zeitzonentabellen

Die Zeitzonentabellen auf Ihrem Server können durch Aufrufen der gespeicherten Prozedur `mysql.az_load_timezone` über ein Tool wie die MySQL-Befehlszeile oder MySQL Workbench aufgefüllt werden.

> [!NOTE]
> Wenn Sie den Befehl `mysql.az_load_timezone` in MySQL Workbench ausführen, müssen Sie möglicherweise zuerst den sicheren Aktualisierungsmodus mit `SET SQL_SAFE_UPDATES=0;` deaktivieren.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Sie sollten den Server neu starten, um sicherzustellen, dass die Zeitzonentabellen ordnungsgemäß aufgefüllt werden.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Um die verfügbaren Zeitzonenwerte anzuzeigen, führen Sie den folgenden Befehl aus:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Festlegen der globalen Zeitzone

Die globale Zeitzone kann mithilfe des Befehls [az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter) festgelegt werden.

Der folgende Befehl ändert den Serverparameter **time\_zone** für den Server **mydemoserver.mysql.database.azure.com** in der Ressourcengruppe **myresourcegroup** in **US/Pacific**.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Festlegen der Sitzungszeitzone

Die Sitzungszeitzone kann durch Ausführen des Befehls `SET time_zone` in einem Tool wie der MySQL-Befehlszeile oder MySQL Workbench festgelegt werden. Im folgenden Beispiel wird die Zeitzone auf **US/Pacific** festgelegt.  

```sql
SET time_zone = 'US/Pacific';
```

Informationen zu [Datums- und Uhrzeitfunktionen](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) finden Sie in der MySQL-Dokumentation.


## <a name="next-steps"></a>Nächste Schritte

- Konfigurieren von [Serverparametern im Azure-Portal](./how-to-configure-server-parameters-portal.md)
