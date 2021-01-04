---
title: Automatische Speichervergrößerung – Azure PowerShell – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie die automatische Speichervergrößerung in Azure Database for MariaDB mithilfe von PowerShell aktivieren können.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 87438c406ea2b31942003d9211cf5e285628f15d
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540993"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Automatische Speichervergrößerung für einen Azure Database for MariaDB-Server mithilfe von PowerShell

In diesem Artikel wird beschrieben, wie Sie die Speichervergrößerung für einen Azure Database for MariaDB-Server konfigurieren können, ohne die Workload zu beeinträchtigen.

Die automatische Speichervergrößerung verhindert, dass Ihr Server [das Speicherlimit erreicht](concepts-pricing-tiers.md#reaching-the-storage-limit) und dann schreibgeschützt wird. Bei Servern mit maximal 100 GB bereitgestelltem Speicher wird die Größe um 5 GB erhöht, wenn der freie Speicherplatz unter 10 % liegt. Bei Servern mit mehr als 100 GB bereitgestelltem Speicher wird die Größe um 5 % erhöht, wenn der freie Speicherplatz unter 10 GB liegt. Die maximalen Speichergrenzwerte gelten so, wie im Abschnitt „Speicher“ des Artikels [Azure Database for MariaDB – Tarife](concepts-pricing-tiers.md#storage) angegeben.

> [!IMPORTANT]
> Beachten Sie, dass der Speicher nur zentral hochskaliert und nicht herunterskaliert werden kann.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

- Lokale Installation des [Moduls „Az PowerShell“](/powershell/azure/install-az-ps) oder von [Azure Cloud Shell](https://shell.azure.com/) im Browser
- [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls „Az.MariaDb“ verfügbar ist, müssen Sie es separat über das Azure PowerShell-Modul installieren. Verwenden Sie dazu den folgenden Befehl: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Sobald das PowerShell-Modul Az.MariaDb allgemein verfügbar ist, wird es in zukünftige Releases des Az-PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

Wenn Sie PowerShell lieber lokal verwenden möchten, stellen Sie mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto her.

## <a name="enable-mariadb-server-storage-auto-grow"></a>Aktivieren der automatischen Speichervergrößerung für den MariaDB-Server

Aktivieren Sie die automatische Speichervergrößerung auf einem vorhandenen Server mit dem folgenden Befehl:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Aktivieren Sie die automatische Speichervergrößerung beim Erstellen eines neuen Servers mit dem folgenden Befehl:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zum Erstellen und Verwalten von Lesereplikaten in Azure Database for MariaDB mithilfe von PowerShell](howto-read-replicas-powershell.md)
