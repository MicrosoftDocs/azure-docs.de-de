---
title: Neustarten von Servern – Azure-Befehlszeilenschnittstelle – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie einen Azure Database for MariaDB-Server über die Azure CLI neu starten.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8ae69adfe83b871eb29c85fc4d03e817026ec006
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541367"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Neustarten eines Azure Database for MariaDB-Servers mithilfe der Azure CLI
In diesem Thema wird erläutert, wie Sie einen Azure Database for MariaDB-Server neu starten. Es kann vorkommen, dass Sie Ihren Server zu Wartungszwecken neu starten müssen. In diesem Fall kommt es zu einem kurzen Ausfall, weil der Vorgang vom Server ausgeführt wird.

Der Neustart des Servers wird blockiert, wenn der Dienst ausgelastet ist. Beispielsweise kann der Dienst einen zuvor angeforderten Vorgang (z. B. das Skalieren von virtuellen Kernen) verarbeiten.

Die für einen Neustart benötigte Zeit hängt vom MariaDB-Wiederherstellungsprozess ab. Um die Neustartzeit zu verkürzen, empfehlen wir, die Aktivitäten auf dem Server vor dem Neustart auf ein Minimum zu beschränken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

So schließen Sie diese Anleitung ab

- Sie benötigen einen [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-cli.md).
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.


## <a name="restart-the-server"></a>Neustarten des Servers

Starten Sie den Server mit dem folgenden Befehl neu:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Festlegen von Parametern in Azure Database for MariaDB](howto-configure-server-parameters-cli.md)
