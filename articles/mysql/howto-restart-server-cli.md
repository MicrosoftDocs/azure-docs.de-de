---
title: Neustarten eines Azure Database for MySQL-Servers mithilfe der Azure CLI
description: In diesem Artikel wird beschrieben, wie Sie einen Azure Database for MySQL-Server über die Azure CLI neu starten.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: d00aa35437f93c010ce48f3036b4a684910702c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160425"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Neustarten eines Azure Database for MySQL-Servers mithilfe der Azure CLI
In diesem Thema wird erläutert, wie Sie einen Azure Database for MySQL-Server neu starten. Es kann vorkommen, dass Sie Ihren Server zu Wartungszwecken neu starten müssen. In diesem Fall kommt es zu einem kurzen Ausfall, weil der Vorgang vom Server ausgeführt wird.

Der Neustart des Servers wird blockiert, wenn der Dienst ausgelastet ist. Beispielsweise kann der Dienst einen zuvor angeforderten Vorgang (z. B. das Skalieren von virtuellen Kernen) verarbeiten.

Die für einen Neustart benötigte Zeit hängt vom MySQL-Wiederherstellungsprozess ab. Um die Neustartzeit zu verkürzen, empfehlen wir, die Aktivitäten auf dem Server vor dem Neustart auf ein Minimum zu beschränken.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:
- Eine [Serverinstanz von Azure Database for MySQL](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Diese Anleitung setzt die Verwendung von Azure CLI-Version 2.0 oder höher voraus. Geben Sie zum Bestätigen der Version an der Eingabeaufforderung von Azure CLI `az --version` ein. Informationen zum Ausführen einer Installation oder eines Upgrades finden Sie unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Neustarten des Servers

Starten Sie den Server mit dem folgenden Befehl neu:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Festlegen von Parametern in Azure Database for MySQL](howto-configure-server-parameters-using-cli.md)