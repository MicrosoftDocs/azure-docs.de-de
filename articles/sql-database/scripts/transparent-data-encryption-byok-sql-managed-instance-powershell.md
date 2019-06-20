---
title: 'PowerShell: Aktivieren von BYOK-TDE: verwaltete Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation'
description: Erfahren Sie, wie eine verwaltete Azure SQL-Datenbank-Instanz mithilfe von PowerShell für die Verwendung von Transparent Data Encryption (TDE) zur Verschlüsselung ruhender Daten in einem BYOK-Szenario konfiguriert wird.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: c2c4bd7bffd923430d0817cb6ea975f4c1596623
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729164"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Verwalten von Transparent Data Encryption in einer verwalteten Instanz mithilfe eines eigenen Azure Key Vault-Schlüssels (Vorschauversion)

In diesem PowerShell-Skriptbeispiel wird Transparent Data Encryption (TDE) in einem Bring Your Own Key-Szenario (BYOK) in der Vorschauversion für eine verwaltete Azure SQL-Datenbank-Instanz unter Verwendung eines Schlüssels aus Azure Key Vault konfiguriert. Weitere Informationen zu TDE mit BYOK-Unterstützung finden Sie unter [TDE mit Bring Your Own Key-Unterstützung für Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Voraussetzungen

- Eine vorhandene verwaltete Instanz. Siehe [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz mithilfe von PowerShell](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Sowohl die lokale Verwendung von PowerShell als auch die Verwendung von Azure Cloud Shell erfordert Azure PowerShell 1.1.1-preview oder eine höhere Vorschauversion. Wenn Sie ein Upgrade durchführen müssen, lesen Sie [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps), oder führen Sie das folgende Beispielskript zum Installieren des Moduls aus.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-scripts"></a>Beispielskripts

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../sql-database-powershell-samples.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
