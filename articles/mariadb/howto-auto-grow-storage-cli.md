---
title: Automatisches Vergrößern des Azure Database for MariaDB-Speichers über die Azure-Befehlszeilenschnittstelle
description: In diesem Artikel wird beschrieben, wie Sie die automatische Speichervergrößerung für Azure Database for MariaDB per Azure CLI aktivieren können.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3c7fc5165766ff5b492f05047000814475235280
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516423"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Automatisches Vergrößern des Azure Database for MariaDB-Speichers per Azure CLI
In diesem Artikel wird beschrieben, wie Sie die Speichervergrößerung für einen Azure Database for MariaDB-Server konfigurieren können, ohne die Workload zu beeinträchtigen.

Der Server, für den die [Speicherbegrenzung erreicht wird](concepts-pricing-tiers.md#reaching-the-storage-limit), ist auf „Schreibgeschützt“ festgelegt. Wenn die automatische Speichervergrößerung dann für Server mit weniger als 100 GB bereitgestelltem Speicher aktiviert wird, wird die bereitgestellte Speichergröße um 5 GB erhöht, sobald der freie Speicher unter 1 GB oder zehn Prozent des bereitgestellten Speichers sinkt. Bei Servern mit mehr als 100 GB bereitgestelltem Speicher wird die bereitgestellte Speichergröße um fünf Prozent erhöht, sobald der freie Speicherplatz unter fünf Prozent der bereitgestellten Speichergröße sinkt. Dabei gelten die [hier](concepts-pricing-tiers.md#storage) beschriebenen Grenzwerte für maximalen Speicher.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens ist Folgendes erforderlich:

- Sie benötigen einen [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="enable-mariadb-server-storage-auto-grow"></a>Aktivieren der automatischen Speichervergrößerung für den MariaDB-Server

Aktivieren Sie die automatische Speichervergrößerung auf einem vorhandenen Server mit dem folgenden Befehl:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aktivieren Sie die automatische Speichervergrößerung beim Erstellen eines neuen Servers mit dem folgenden Befehl:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Warnungen zu Metriken erstellen](howto-alert-metric.md).
