---
title: Zugreifen auf Protokolle zu langsamen Abfragen – Azure-Befehlszeilenschnittstelle – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie mit der Azure-Befehlszeilenschnittstelle auf die Protokolle zu langsamen Abfragen in Azure Database for MariaDB zugreifen.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cea1571a48afe00350dde247c3c10e222a446247
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539871"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Konfigurieren von Protokollen für langsame Abfragen in Azure Database for Maria DB und Zugreifen auf diese Protokolle über die Azure-Befehlszeilenschnittstelle (Azure CLI)

Sie können die Protokolle für langsame Abfragen von Azure Database for MariaDB mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) herunterladen.

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Die [Azure CLI](/cli/azure/install-azure-cli) oder Azure Cloud Shell im Browser

## <a name="configure-logging"></a>Konfigurieren der Protokollierung
Sie können den Server mit folgenden Schritten für den Zugriff auf das Protokoll für langsame MySQL-Abfragen konfigurieren:
1. Aktivieren Sie die Protokollierung für langsame Abfragen, indem Sie den Parameter **slow\_query\_log** auf „ON“ festlegen.
2. Wählen Sie mit **log\_output** aus, wohin die Protokolle ausgegeben werden sollen. Wenn Protokolle sowohl in den lokalen Speicher als auch in die Azure Monitor-Diagnoseprotokolle übertragen werden sollen, wählen Sie **Datei** aus. Wenn Protokolle nur in Azure Monitor-Protokolle übertragen werden sollen, wählen Sie **Keine** aus.
3. Passen Sie andere Parameter an, z.B. **long\_query\_time** und **log\_slow\_admin\_statements**.

Unter [Konfigurieren von Serverparametern](howto-configure-server-parameters-cli.md) erfahren Sie, wie Sie den Wert dieser Parameter über die Azure-Befehlszeilenschnittstelle festlegen.

Beispielsweise aktiviert der folgende CLI-Befehl das Protokoll für langsame Abfragen, legt die Mindestlaufzeit für Protokollierung auf 10 Sekunden fest und deaktiviert die Protokollierung für die langsame Administratoranweisung. Schließlich werden die Konfigurationsoptionen für Ihre Überprüfung aufgelistet.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Auflisten von Protokollen für Azure Database for MariaDB-Server
Wenn für **log_output** „Datei“ festgelegt wurde, können Sie direkt aus dem lokalen Speicher des Servers auf Protokolle zugreifen. Um die verfügbaren Protokolldateien für langsame Abfragen für Ihren Server aufzulisten, führen Sie den Befehl [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) aus.

Sie können die Protokolldateien für den Server **mydemoserver.mariadb.database.azure.com** in der Ressourcengruppe **myresourcegroup** auflisten. Leiten Sie dann die Liste der Protokolldateien in eine Textdatei namens **log\_files\_list.txt** um.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Herunterladen von Protokollen vom Server
Wenn für **log_output** „Datei“ festgelegt wurde, können Sie einzelne Protokolldateien mit dem Befehl [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) von Ihrem Server herunterladen.

Laden Sie dem folgenden Beispiel entsprechend die spezifische Protokolldatei für den Server **mydemoserver.mariadb.database.azure.com** in der Ressourcengruppe **myresourcegroup** in Ihre lokale Umgebung herunter.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Protokolle für langsame Abfragen in Azure Database for MariaDB](concepts-server-logs.md).
