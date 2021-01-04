---
title: Neustarten eines Servers – Azure CLI – Azure Database for PostgreSQL – Einzelserver
description: In diesem Artikel wird beschrieben, wie Sie eine Instanz von Azure Database for PostgreSQL neu starten können – Einzelserver mithilfe der Azure CLI
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f9881a4517f77587cecb2dcd04befaddb523965b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647646"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Neustarten von Azure Database for PostgreSQL – Einzelserver mithilfe der Azure CLI
In diesem Thema wird erläutert, wie Sie einen Azure Database for PostgreSQL-Server neu starten können. Es kann vorkommen, dass Sie Ihren Server zu Wartungszwecken neu starten müssen. In diesem Fall kommt es zu einem kurzen Ausfall, weil der Vorgang vom Server ausgeführt wird.

Der Neustart des Servers wird blockiert, wenn der Dienst ausgelastet ist. Beispielsweise kann der Dienst einen zuvor angeforderten Vorgang (z. B. das Skalieren von virtuellen Kernen) verarbeiten.
 
Die für einen Neustart benötigte Zeit hängt von dem PostgreSQL-Wiederherstellungsprozess ab. Um die Neustartzeit zu verkürzen, empfehlen wir, die Aktivitäten auf dem Server vor dem Neustart auf ein Minimum zu beschränken.

## <a name="prerequisites"></a>Voraussetzungen
So schließen Sie diese Anleitung ab
- Erstellen einer [Azure-Datenbank für PostgreSQL-Server](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="restart-the-server"></a>Neustarten des Servers

Starten Sie den Server mit dem folgenden Befehl neu:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Festlegen von Parametern in Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)
