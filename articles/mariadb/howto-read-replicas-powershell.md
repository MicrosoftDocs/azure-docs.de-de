---
title: Verwalten von Lesereplikaten – Azure PowerShell – Azure Database for MariaDB
description: Hier erfahren Sie, wie Sie mit PowerShell Lesereplikate in Azure Database for MariaDB einrichten und verwalten.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a5921026c8f260683f091870850350fcc4619f6b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538613"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>Informationen zum Erstellen und Verwalten von Lesereplikaten in Azure Database for MariaDB mithilfe von PowerShell

In diesem Artikel erfahren Sie, wie Sie Lesereplikate im Azure Database for MariaDB-Dienst über PowerShell erstellen und verwalten. Weitere Informationen zu Lesereplikaten finden Sie in der [Übersicht](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Sie können Lesereplikate mithilfe von PowerShell erstellen und verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

- Lokale Installation des [Moduls „Az PowerShell“](/powershell/azure/install-az-ps) oder von [Azure Cloud Shell](https://shell.azure.com/) im Browser
- [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls „Az.MariaDb“ verfügbar ist, müssen Sie es separat über das Azure PowerShell-Modul installieren. Verwenden Sie dazu den folgenden Befehl: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Sobald das PowerShell-Modul Az.MariaDb allgemein verfügbar ist, wird es in zukünftige Releases des Az-PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

Wenn Sie PowerShell lieber lokal verwenden möchten, stellen Sie mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto her.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Das Feature für Lesereplikate ist nur für Azure Database for MariaDB-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Quellserver einer der folgenden Tarife festgelegt ist.

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

> [!IMPORTANT]
> Wenn Sie ein Replikat für eine Quelle erstellen, die keine vorhandenen Replikate hat, startet die Quelle zunächst neu, um sich auf die Replikation vorzubereiten. Beachten Sie dies, und führen Sie diese Vorgänge nicht zu Spitzenzeiten durch.

Ein Lesereplikatserver kann mit dem folgenden Befehl erstellt werden:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Für den Befehl `New-AzMariaDbServerReplica` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| Name | mydemoreplicaserver | Der Name des neuen Replikatservers, der erstellt wird. |

Verwenden Sie den **Location**-Parameter, um ein regionsübergreifendes Lesereplikat zu erstellen. Im folgenden Beispiel wird ein Replikat in der Region **USA, Westen** erstellt.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md).

Standardmäßig werden Lesereplikate mit derselben Serverkonfiguration wie der Quellserver erstellt, es sei denn, der Parameter **Sku** wird angegeben.

> [!NOTE]
> Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den Quellserver, damit das Replikat über genügend Kapazität verfügt.

### <a name="list-replicas-for-a-source-server"></a>Auflisten von Replikaten für einen Quellserver

Führen Sie den folgenden Befehl aus, um alle Replikate für einen bestimmten Quellserver anzuzeigen:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Für den Befehl `Get-AzMariaDReplica` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| ServerName | mydemoserver | Der Name oder die ID des Quellservers. |

### <a name="delete-a-replica-server"></a>Löschen eines Replikatservers

Zum Löschen eines Lesereplikatservers können Sie das Cmdlet `Remove-AzMariaDbServer` ausführen.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Löschen eines Quellservers

> [!IMPORTANT]
> Wenn Sie einen Quellserver löschen, wird die Replikation auf allen Replikatservern beendet und der Quellserver selbst gelöscht. Replikatserver werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

Zum Löschen eines Quellservers können Sie das Cmdlet `Remove-AzMariaDbServer` ausführen.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Neustarten eines Azure Database for MariaDB-Servers mithilfe von PowerShell](howto-restart-server-powershell.md)