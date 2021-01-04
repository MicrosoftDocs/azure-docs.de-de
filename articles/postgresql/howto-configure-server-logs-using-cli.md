---
title: Verwalten von Protokollen – Azure CLI – Azure Database for PostgreSQL – Einzelserver
description: In diesem Artikel wird beschrieben, wie Sie mit der Azure CLI die Serverprotokolle (.log-Dateien) in Azure Database for PostgreSQL (Einzelserver) konfigurieren und auf diese zugreifen.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71dac0b6ea2202e712280607a73e860ae68bdb73
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005587"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurieren der und Zugreifen auf die Serverprotokolle mithilfe der Azure CLI
Sie können die PostgreSQL-Serverfehlerprotokolle mithilfe der Befehlszeilenschnittstelle (Azure CLI) herunterladen. Der Zugriff auf Transaktionsprotokolle wird jedoch nicht unterstützt. 

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- [Azure Database for PostgreSQL-Server](quickstart-create-server-database-azure-cli.md)
- Das Befehlszeilenprogramm [Azure CLI](/cli/azure/install-azure-cli) oder Azure Cloud Shell im Browser

## <a name="configure-logging"></a>Konfigurieren der Protokollierung
Sie können den Server für den Zugriff auf Abfrage- und Fehlerprotokolle konfigurieren. Fehlerprotokolle können Informationen über „auto-vacuum“, Verbindungen und Prüfpunkte enthalten.
1. Aktivieren Sie die Protokollierung.
2. Um die Abfragenprotokollierung zu aktivieren, aktualisieren Sie „**log\_statement**“ und „**log\_min\_duration\_statement**“.
3. Aktualisieren Sie den Aufbewahrungszeitraum.

Weitere Informationen finden Sie unter [Anpassen der Serverkonfigurationsparameter](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Auflisten von Protokollen
Um die verfügbaren Protokolldateien für Ihren Server aufzulisten, führen Sie den Befehl [az postgres server-logs list](/cli/azure/postgres/server-logs) aus.

Sie können die Protokolldateien für den Server **mydemoserver.postgres.database.azure.com** in der Ressourcengruppe **myresourcegroup** auflisten. Leiten Sie dann die Liste der Protokolldateien in eine Textdatei namens **log\_files\_list.txt** um.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Herunterladen von Protokollen vom Server auf den lokalen Computer
Mit dem Befehl [az postgres server-logs download](/cli/azure/postgres/server-logs) können Sie einzelne Protokolldateien für Ihren Server herunterladen. 

Laden Sie dem folgenden Beispiel entsprechend die spezifische Protokolldatei für den Server **mydemoserver.postgres.database.azure.com** in der Ressourcengruppe **myresourcegroup** in Ihre lokale Umgebung herunter.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu Serverprotokollen finden Sie unter [Serverprotokolle in Azure Database for PostgreSQL](concepts-server-logs.md).
- Weitere Informationen zu Serverparametern finden Sie unter [Anpassen der Serverkonfigurationsparameter über die Azure CLI](howto-configure-server-parameters-using-cli.md).
