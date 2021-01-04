---
title: Zugreifen auf Überwachungsprotokolle – Azure CLI – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie über die Azure CLI die Überwachungsprotokolle in Azure Database for MariaDB konfigurieren und darauf zugreifen.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c1d446d8ee2863077ad84c361876758336f5a3cb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540959"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>Konfigurieren von Überwachungsprotokollen für Azure Database for Maria DB und Zugreifen darauf in der Azure CLI

Sie können die [Überwachungsprotokolle in Azure Database for MariaDB](concepts-audit-logs.md) über die Azure CLI konfigurieren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens ist Folgendes erforderlich:

- Sie benötigen einen [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="configure-audit-logging"></a>Konfigurieren der Überwachungsprotokollierung

>[!IMPORTANT]
> Es wird empfohlen, nur die Ereignistypen und Benutzer zu protokollieren, die für Ihre Überwachungszwecke erforderlich sind, um sicherzustellen, dass die Leistung Ihres Servers nicht stark beeinträchtigt wird.

Aktivieren und konfigurieren Sie die Überwachungsprotokollierung mit den folgenden Schritten: 

1. Aktivieren Sie Überwachungsprotokolle, indem Sie den Parameter **audit_logs_enabled** auf ON festlegen. 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Wählen Sie die zu protokollierenden [Ereignistypen](concepts-audit-logs.md#configure-audit-logging) aus, indem Sie den Parameter **audit_log_events** aktualisieren.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Fügen Sie alle MariaDB-Benutzer hinzu, die von der Protokollierung ausgeschlossen werden sollen, indem Sie den Parameter **audit_log_exclude_users** aktualisieren. Geben Sie Benutzer an, indem Sie ihre MariaDB-Benutzernamen bereitstellen.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Fügen Sie alle MariaDB-Benutzer hinzu, die in die Protokollierung einbezogen werden sollen, indem Sie den Parameter **audit_log_exclude_users** aktualisieren. Geben Sie Benutzer an, indem Sie ihre MariaDB-Benutzernamen bereitstellen.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zu [Überwachungsprotokollen](concepts-audit-logs.md) in Azure Database for MariaDB.
- Informieren Sie sich, wie Überwachungsprotokolle im [Azure-Portal](howto-configure-audit-logs-portal.md) konfiguriert werden.
