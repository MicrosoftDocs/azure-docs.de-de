---
title: Vorgehensweise beim Scannen von Azure Cosmos DB (SQL-API)
description: In diesem Leitfaden wird beschrieben, wie Sie eine Überprüfung für Azure Cosmos DB (SQL-API) durchführen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: e1d2035b787380d9b93943b92fbe81c09fc6a527
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550935"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Vorgehensweise beim Registrieren und Scannen von Azure Cosmos DB (SQL-API)

In diesem Artikel wird beschrieben, wie Sie ein Azure Cosmos DB-Konto (SQL-API) in Azure Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Für Azure Cosmos DB (SQL-API) werden vollständige und inkrementelle Überprüfungen zum Erfassen der Metadaten und des Schemas unterstützt. Darüber hinaus werden die Daten bei Überprüfungen basierend auf den system- und benutzerdefinierten Klassifizierungsregeln automatisch klassifiziert.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie vor dem Registrieren der Datenquellen zunächst ein Azure Purview-Konto. Weitere Informationen zum Erstellen eines Purview-Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
- Sie müssen ein Azure Purview-Datenquellenadministrator sein.

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Es gibt nur eine Möglichkeit, die Authentifizierung für Azure Cosmos DB (SQL-API) einzurichten:

- Kontoschlüssel
 
### <a name="account-key"></a>Kontoschlüssel

Wenn **Kontoschlüssel** als Authentifizierungsmethode ausgewählt wird, müssen Sie Ihren Zugriffsschlüssel abrufen und im Schlüsseltresor speichern:

1. Navigieren Sie zu Ihrem Cosmos DB-Konto im Azure-Portal. 
1. Wählen Sie **Einstellungen** > **Schlüssel** aus. 
1. Kopieren Sie Ihren *Schlüssel*, und speichern Sie ihn für die nächsten Schritte.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Schlüssel* für Ihr Speicherkonto ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Schlüssel zum Einrichten Ihrer Überprüfung verwenden.

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Registrieren eines Azure Cosmos DB-Kontos (SQL-API)

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog ein neues Azure Cosmos DB-Konto (SQL-API) zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im linken Navigationsbereich die Option **Quellen** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter **Quellen registrieren** die Option **Azure Cosmos DB (SQL-API)** aus.
1. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Gehen Sie auf dem Bildschirm **Quellen registrieren (Azure Cosmos DB (SQL-API))** wie folgt vor:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
1. Wählen Sie aus, wie auf Ihr gewünschtes Speicherkonto verwiesen werden soll:
   1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Cosmos DB-Kontoname** das entsprechende Cosmos DB-Konto aus.
   1. Alternativ können Sie auch die Option **Manuell eingeben** auswählen und einen Dienstendpunkt (URL) eingeben.
1. Wählen Sie **Fertig stellen** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="Optionen für die Quellenregistrierung" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
