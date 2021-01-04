---
title: 'Generieren einer Verbindungszeichenfolge mit PowerShell: Azure Database for MySQL'
description: Dieser Artikel enthält ein Azure PowerShell-Beispiel zum Generieren einer Verbindungszeichenfolge für die Verbindungsherstellung mit Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 9f960d32dcf1f359327dccc01eeb06825cc3a062
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541469"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Generieren einer Azure Database for MySQL-Verbindungszeichenfolge mit PowerShell

In diesem Artikel wird veranschaulicht, wie Sie eine Verbindungszeichenfolge für einen Azure Database for MySQL-Server generieren. Mit einer Verbindungszeichenfolge können Sie über zahlreiche verschiedene Anwendungen eine Verbindung mit einer Azure Database for MySQL-Instanz herstellen.

## <a name="requirements"></a>Requirements (Anforderungen)

In diesem Artikel werden die Ressourcen, die in der folgenden Anleitung erstellt wurden, als Ausgangspunkt verwendet:

* [Schnellstart: Erstellen eines Azure Database for MySQL-Servers mithilfe von PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Mit dem Cmdlet `Get-AzMySqlConnectionString` wird eine Verbindungszeichenfolge zum Herstellen einer Verbindung mit Azure Database for MySQL generiert. Im folgenden Beispiel wird die Verbindungszeichenfolge für einen PHP-Client von **mydemoserver** zurückgegeben.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Anpassen eines Azure Database for MySQL-Serverparameters mithilfe von Azure PowerShell](howto-configure-server-parameters-using-powershell.md)
