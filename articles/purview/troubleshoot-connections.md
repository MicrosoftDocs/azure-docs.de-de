---
title: Problembehandlung für Ihre Verbindungen in Azure Purview
description: In diesem Artikel werden die Schritte zum Durchführen der Problembehandlung für Ihre Verbindungen in Azure Purview beschrieben.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: f76a05757f86308785d4ca678675b87b3fa9d63e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550481"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Problembehandlung für Ihre Verbindungen in Azure Purview

In diesem Artikel wird beschrieben, wie Sie die Problembehandlung für Verbindungsfehler durchführen, während Sie Überprüfungen für Datenquellen in Azure Purview einrichten.

## <a name="permission-the-credential-on-the-data-source"></a>Zuweisen einer Berechtigung für Anmeldeinformationen auf der Datenquelle

Wenn Sie eine verwaltete Identität oder einen Dienstprinzipal als Authentifizierungsmethode für Überprüfungen verwenden, müssen Sie diese Identitäten zulassen, damit Zugriff auf Ihre Datenquelle besteht.

Für jeden Quellentyp gilt eine bestimmte Anleitung:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL-Datenbank](register-scan-azure-sql-database.md)
- [Verwaltete Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Speichern Ihrer Anmeldeinformationen in Ihrem Schlüsseltresor und Verwenden der richtigen Angaben für Geheimnisname und -version

Darüber hinaus müssen Sie Ihre Anmeldeinformationen auf Ihrer Azure Key Vault-Instanz speichern und den richtigen Geheimnisnamen und die entsprechende Version verwenden.

Überprüfen Sie dies, indem Sie die folgenden Schritte ausführen:

1. Navigieren Sie zu Ihrer Key Vault-Instanz.
1. Wählen Sie **Settings** > **Secrets** (Einstellungen > Geheimnisse) aus.
1. Wählen Sie das Geheimnis aus, das Sie für die Authentifizierung bei Ihrer Datenquelle für Überprüfungen verwenden.
1. Wählen Sie die gewünschte Version aus, und überprüfen Sie die Richtigkeit des Kennworts bzw. des Kontoschlüssels, indem Sie auf **Geheimniswert anzeigen** klicken. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Überprüfen der Berechtigungen für die verwaltete Purview-Identität auf Ihrer Azure Key Vault-Instanz

Vergewissern Sie sich, dass zum Zugreifen auf Ihre Azure Key Vault-Instanz die richtigen Berechtigungen für die verwaltete Purview-Identität konfiguriert wurden.

Führen Sie die folgenden Schritte aus, um dies zu überprüfen:

1. Navigieren Sie zu Ihrem Schlüsseltresor und dann zum Abschnitt **Zugriffsrichtlinien**.
1. Vergewissern Sie sich, dass für Ihre verwaltete Purview-Identität im Abschnitt *Aktuelle Zugriffsrichtlinien* mindestens die Berechtigung zum Abrufen (**Get**) von Geheimnissen angezeigt wird.

Falls Ihre verwaltete Purview-Identität nicht aufgeführt ist, sollten Sie die Schritte unter [Anmeldeinformationen für die Quellenauthentifizierung in Azure Purview](manage-credentials.md) ausführen, um sie hinzuzufügen. 

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
