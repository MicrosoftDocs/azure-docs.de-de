---
title: 'Generieren einer Verbindungszeichenfolge mit PowerShell: Azure Database for MariaDB'
description: Dieser Artikel enthält ein Azure PowerShell-Beispiel zum Generieren einer Verbindungszeichenfolge für die Verbindungsherstellung mit Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 9dee109c701d3760c93f39e639dcfd7cae07b595
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538052"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>Generieren einer Azure Database for MariaDB-Verbindungszeichenfolge mit PowerShell

In diesem Artikel wird veranschaulicht, wie Sie eine Verbindungszeichenfolge für einen Azure Database for MariaDB-Server generieren. Mit einer Verbindungszeichenfolge können Sie über zahlreiche verschiedene Anwendungen eine Verbindung mit einer Azure Database for MariaDB-Instanz herstellen.

## <a name="requirements"></a>Requirements (Anforderungen)

In diesem Artikel werden die Ressourcen, die in der folgenden Anleitung erstellt wurden, als Ausgangspunkt verwendet:

* [Schnellstart: Erstellen eines Servers für Azure Database for MariaDB mithilfe von PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Mit dem Cmdlet `Get-AzMariaDbConnectionString` wird eine Verbindungszeichenfolge zum Herstellen einer Verbindung mit Azure Database for MariaDB generiert. Im folgenden Beispiel wird die Verbindungszeichenfolge für einen PHP-Client von **mydemoserver** zurückgegeben.

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

Gültige Werte für den Parameter `Client` sind u. a.:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Anpassen eines Azure Database for MariaDB-Serverparameters mithilfe von PowerShell](howto-configure-server-parameters-using-powershell.md)
