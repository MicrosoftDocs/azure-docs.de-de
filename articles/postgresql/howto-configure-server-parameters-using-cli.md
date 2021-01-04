---
title: Konfigurieren von Parametern – Azure Database for PostgreSQL (Einzelserver)
description: In diesem Artikel wird beschrieben, wie Sie mit der Azure-Befehlszeilenschnittstelle die Postgres-Parameter in Azure Database for PostgreSQL (Einzelserver) konfigurieren.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4231f348f99073406fcb6a5bef9bf0f84cacf2eb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005570"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Anpassen der Serverkonfigurationsparameter für Azure Database for PostgreSQL (Einzelserver) mithilfe der Azure-Befehlszeilenschnittstelle
Sie können Konfigurationsparameter für einen Azure PostgreSQL-Server mithilfe der Befehlszeilenschnittstelle (Azure CLI) auflisten, anzeigen und aktualisieren. Auf Serverebene ist eine Teilmenge der Engine-Konfigurationen verfügbar und kann geändert werden. 

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- Erstellen Sie einen Azure Database for PostgreSQL-Server und eine -Datenbank gemäß [Erstellen einer Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).
- Installieren Sie die Befehlszeilenschnittstelle [Azure CLI](/cli/azure/install-azure-cli) auf Ihrem Computer, oder verwenden Sie [Azure Cloud Shell](../cloud-shell/overview.md) im Azure-Portal über Ihren Browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Auflisten der Serverkonfigurationsparameter für Server für Azure-Datenbank für PostgreSQL
Führen Sie den Befehl [az postgres server configuration list](/cli/azure/postgres/server/configuration) aus, um alle änderbaren Parameter eines Servers mit ihren Werten aufzulisten.

Sie können die Serverkonfigurationsparameter für den Server **mydemoserver.postgres.database.azure.com** in der Ressourcengruppe **myresourcegroup** auflisten.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Anzeigen von Details zu Serverkonfigurationsparametern
Um Details zu einem bestimmten Konfigurationsparameter für einen Server anzuzeigen, führen Sie den Befehl [az postgres server configuration show](/cli/azure/postgres/server/configuration) aus.

Dieses Beispiel zeigt Details des Serverkonfigurationsparameters **log\_min\_messages** für den Server **mydemoserver.postgres.database.azure.com** in der Ressourcengruppe **myresourcegroup**.
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Ändern des Werts von Serverkonfigurationsparametern
Sie können den Wert eines bestimmten Serverkonfigurationsparameters ändern und dadurch den zugrunde liegenden Konfigurationswert für die PostgreSQL-Server-Engine aktualisieren. Um die Konfiguration zu aktualisieren, führen Sie den Befehl [az postgres server configuration set](/cli/azure/postgres/server/configuration) aus. 

Aktualisieren Sie den Serverkonfigurationsparameter **log\_min\_messages** des Servers **mydemoserver.postgres.database.azure.com** in der Ressourcengruppe **myresourcegroup**.
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Wenn Sie den Wert eines Konfigurationsparameters zurücksetzen möchten, lassen Sie einfach den optionalen Parameter `--value` weg. Der Dienst wendet dann den Standardwert an. Im obigen Beispiel könnte dies so aussehen:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Mit diesem Befehl wird die Konfiguration **log\_min\_messages** auf den Standardwert **WARNING** zurückgesetzt. Weitere Informationen zur Serverkonfiguration und zu den zulässigen Werten finden Sie in der PostgreSQL-Dokumentation zur [Serverkonfiguration](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Nächste Schritte
- [Neustart eines Servers](howto-restart-server-cli.md)
- Informationen dazu, wie Sie Serverprotokolle konfigurieren und darauf zugreifen, finden Sie unter [Serverprotokolle in Azure Database for PostgreSQL](concepts-server-logs.md).
