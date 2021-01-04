---
title: Neustarten von Servern – Azure PowerShell – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie einen Azure Database for MariaDB-Server mit Azure PowerShell neu starten.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 14cde5b1302c46b819bb7d841fb5b84a43c580c0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538936"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Neustarten eines Azure Database for MariaDB-Servers mithilfe von Azure PowerShell

In diesem Thema wird erläutert, wie Sie einen Azure Database for MariaDB-Server neu starten. Möglicherweise müssen Sie Ihren Server zu Wartungszwecken neu starten. Während des Vorgangs kommt es dann zu einem kurzen Ausfall.

Der Neustart des Servers wird blockiert, wenn der Dienst ausgelastet ist. Beispielsweise kann der Dienst einen zuvor angeforderten Vorgang (z. B. das Skalieren von virtuellen Kernen) verarbeiten.

Die für einen Neustart benötigte Zeit hängt vom Azure Database for MariaDB-Wiederherstellungsprozess ab. Zum Verkürzen der Neustartzeit wird empfohlen, die Aktivitäten auf dem Server vor dem Neustart auf ein Minimum zu beschränken.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

- Lokale Installation des [Moduls „Az PowerShell“](/powershell/azure/install-az-ps) oder von [Azure Cloud Shell](https://shell.azure.com/) im Browser
- [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls „Az.MariaDb“ verfügbar ist, müssen Sie es separat über das Azure PowerShell-Modul installieren. Verwenden Sie dazu den folgenden Befehl: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Sobald das PowerShell-Modul Az.MariaDb allgemein verfügbar ist, wird es in zukünftige Releases des Az-PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

Wenn Sie PowerShell lieber lokal verwenden möchten, stellen Sie mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto her.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Neustarten des Servers

Starten Sie den Server mit dem folgenden Befehl neu:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Servers für Azure Database for MariaDB mithilfe von PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)