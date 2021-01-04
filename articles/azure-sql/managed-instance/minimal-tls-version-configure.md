---
title: 'Konfigurieren einer minimalen TLS-Version: verwaltete Instanz'
description: Erfahren Sie, wie Sie die minimale TLS-Version für verwaltete Instanzen konfigurieren.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788399"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Konfigurieren der minimalen TLS-Version in Azure SQL Managed Instance
Die Einstellung der Mindestversion für [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) ermöglicht Kunden die Steuerung der TLS-Version, die von Azure SQL Managed Instance verwendet wird.

Derzeit unterstützen wir TLS 1.0, 1.1 und 1.2. Durch Festlegen einer TLS-Mindestversion wird sichergestellt, dass nachfolgende neuere TLS-Versionen unterstützt werden. Die Auswahl der TLS-Version 1.1 beispielsweise bedeutet, dass nur Verbindungen mit TLS 1.1 und 1.2 akzeptiert werden. Verbindungen mit TLS 1.0 werden abgelehnt. Wenn Sie überprüft haben, ob Ihre Anwendung TLS-Version 1.2 unterstützt, empfiehlt es sich, die Mindestversion auf 1.2 festzulegen, da diese Version Fehlerbehebungen für Sicherheitsrisiken enthält, die in früheren Versionen gefunden wurden. Gleichzeitig ist 1.2 die höchste TLS-Version, die derzeit in Azure SQL Managed Instance unterstützt wird.

Kunden mit Anwendungen, die ältere TLS-Versionen erfordern, wird empfohlen, die TLS-Mindestversion gemäß den Anforderungen der Anwendungen festzulegen. Kunden, die Anwendungen mit unverschlüsselten Verbindungen verwenden, wird empfohlen, keine TLS-Mindestversion festzulegen. 

Weitere Informationen finden Sie unter [Überlegungen zu TLS für Verbindungen mit einer SQL-Datenbank](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Nachdem Sie eine TLS-Mindestversion für den Server festgelegt haben, tritt bei Anmeldeversuchen von Clients, die eine niedrigere TLS-Version verwenden, folgender Fehler auf:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Festlegen einer TLS-Mindestversion über PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Das folgende Skript erfordert das [Azure PowerShell-Modul](/powershell/azure/install-az-ps).

Das folgende PowerShell-Skript zeigt, wie Sie die Eigenschaft **TLS-Mindestversion** auf Instanzebene mit `Get` und `Set` abrufen und festlegen können:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Festlegen einer TLS-Mindestversion über die Azure CLI

> [!IMPORTANT]
> Für alle Skripts in diesem Abschnitt ist die [Azure CLI](/cli/azure/install-azure-cli) erforderlich.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in einer Bash-Shell

Das folgende CLI-Skript veranschaulicht, wie Sie die Einstellung **TLS-Mindestversion** in einer Bash-Shell ändern:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```