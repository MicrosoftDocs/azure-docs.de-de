---
title: Konfigurieren von Serverparametern – Azure PowerShell – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie mit PowerShell die Dienstparameter in Azure Database for MySQL konfigurieren.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 973025dfd8c0141ed0884539fe5207cc64ec822c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541860"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>Konfigurieren von Serverparametern in Azure Database for MySQL mit PowerShell

Sie können Konfigurationsparameter für einen Azure Database for MySQL-Server mithilfe von PowerShell auflisten, anzeigen und aktualisieren. Auf Serverebene ist eine Teilmenge der Engine-Konfigurationen verfügbar und kann geändert werden.

>[!Note]
> Serverparameter können global auf Serverebene aktualisiert werden. Verwenden Sie dazu die [Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) oder das [Azure-Portal](./howto-server-parameters.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

- Lokale Installation des [Moduls „Az PowerShell“](/powershell/azure/install-az-ps) oder von [Azure Cloud Shell](https://shell.azure.com/) im Browser
- Ein [Azure Database for MySQL-Server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls „Az.MySql“ verfügbar ist, müssen Sie es separat über das AZ PowerShell-Modul installieren. Verwenden Sie dazu den folgenden Befehl: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Sobald das PowerShell-Modul „Az.MySql“ allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

Wenn Sie PowerShell lieber lokal verwenden möchten, stellen Sie mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) eine Verbindung mit Ihrem Azure-Konto her.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Auflisten der Serverkonfigurationsparameter für Azure Database for MySQL

Führen Sie das Cmdlet `Get-AzMySqlConfiguration` aus, um alle änderbaren Parameter eines Servers mit ihren Werten aufzulisten.

Mit dem folgenden Beispiel werden die Serverkonfigurationsparameter für den Server **mydemoserver** in der Ressourcengruppe **myresourcegroup** aufgelistet.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Die Definition der einzelnen aufgeführten Parameter finden Sie in der MySQL-Referenz im Abschnitt [Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Anzeigen von Details zu Serverkonfigurationsparametern

Um Details zu einem bestimmten Konfigurationsparameter für einen Server anzuzeigen, führen Sie das Cmdlet `Get-AzMySqlConfiguration` aus und geben dabei den Parameter **Name** an.

Mit diesem Beispiel werden Details des Serverkonfigurationsparameters **slow\_query\_log** für den Server **mydemoserver** in der Ressourcengruppe **myresourcegroup** angezeigt.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Ändern des Werts von Serverkonfigurationsparametern

Sie können den Wert eines bestimmten Serverkonfigurationsparameters ändern und dadurch den zugrunde liegenden Konfigurationswert für die MySQL-Server-Engine aktualisieren. Zum Aktualisieren der Konfiguration verwenden Sie das Cmdlet `Update-AzMySqlConfiguration`.

So aktualisieren Sie den Serverkonfigurationsparameter **slow\_query\_log** für den Server **mydemoserver** in der Ressourcengruppe **myresourcegroup**

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Automatische Speichervergrößerung für einen Azure Database for MySQL-Server mithilfe von PowerShell](howto-auto-grow-storage-powershell.md)
