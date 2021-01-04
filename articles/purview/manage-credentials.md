---
title: Erstellen und Verwalten von Anmeldeinformationen für Überprüfungen
description: In diesem Artikel werden die Schritte zum Erstellen und Verwalten von Anmeldeinformationen in Azure Purview beschrieben.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c991559d550b351ce70bcc5834f96f313f856a82
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550328"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Anmeldeinformationen für die Quellenauthentifizierung in Azure Purview

In diesem Artikel wird beschrieben, wie Sie Anmeldeinformationen in Azure Purview erstellen können, um gespeicherte Authentifizierungsinformationen schnell wiederverwenden und auf Ihre Datenquellenüberprüfungen anwenden zu können.

## <a name="prerequisites"></a>Voraussetzungen

* Azure Key Vault-Instanz. Falls Sie noch nicht über eine Instanz verfügen, können Sie diesen Link (Link zum Artikel zur Key Vault-Erstellung einfügen) verwenden, um Informationen zur Erstellung zu erhalten.

## <a name="introduction"></a>Einführung
Bei Anmeldeinformationen handelt es sich um Informationen für die Authentifizierung, die von Azure Purview für die Authentifizierung bei Ihren registrierten Datenquellen genutzt werden können. Ein Objekt für Anmeldeinformationen kann für verschiedene Arten von Authentifizierungsszenarien (z. B. Standardauthentifizierung mit Benutzername/Kennwort) erstellt werden. Hierfür werden basierend auf der ausgewählten Authentifizierungsmethode die erforderlichen spezifischen Informationen erfasst. Für die Anmeldeinformationen werden Ihre vorhandenen Azure Key Vault-Geheimnisse verwendet, um bei der Erstellung der Anmeldeinformationen vertrauliche Authentifizierungsinformationen abzurufen.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Verwenden einer verwalteten Purview-Identität zum Einrichten von Überprüfungen
Wenn Sie die verwaltete Purview-Identität zum Einrichten von Überprüfungen verwenden, müssen Sie nicht explizit Anmeldeinformationen erstellen und Ihren Schlüsseltresor für die Speicherung nicht mit Purview verknüpfen. Eine ausführliche Anleitung zum Hinzufügen der verwalteten Purview-Identität, um den Zugriff auf die Überprüfung Ihrer Datenquellen zu ermöglichen, finden Sie unten in den Abschnitten zur Authentifizierung von Datenquellen:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL-Datenbank](register-scan-azure-sql-database.md)
- [Verwaltete Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Erstellen von Azure Key Vault-Verbindungen in Ihrem Azure Purview-Konto

Bevor Sie Anmeldeinformationen erstellen können, müssen Sie Ihrem Azure Purview-Konto mindestens eine Ihrer vorhandenen Azure Key Vault-Instanzen zuordnen.

1. Navigieren Sie über das Azure Purview-Navigationsmenü zum Verwaltungscenter und dann zu den Anmeldeinformationen.

2. Wählen Sie in der Befehlsleiste unter „Anmeldeinformationen“ die Option „Manage Key Vault Connections“ (Key Vault-Verbindungen verwalten) aus.

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Verwalten von AKV-Verbindungen":::

3. Wählen Sie im Bereich „Manage Key Vault Connections“ (Key Vault-Verbindungen verwalten) die Option „+ Neu“ aus. 

4. Geben Sie die erforderlichen Informationen ein, und wählen Sie anschließend „Erstellen“ aus.

5. Vergewissern Sie sich, dass die Zuordnung Ihrer Key Vault-Instanz zu Ihrem Azure Purview-Konto erfolgreich war.

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Anzeigen von AKV-Verbindungen":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Gewähren des Zugriffs auf Ihre Azure Key Vault-Instanz für die verwaltete Purview-Identität

Navigieren Sie zu Ihrem Schlüsseltresor, und wählen Sie „Zugriffsrichtlinien“ > „Zugriffsrichtlinie hinzufügen“ aus. Gewähren Sie in der Dropdownliste für die Geheimnisberechtigungen die Berechtigung zum Abrufen (Get), und wählen Sie „Prinzipal“ als MSI für Purview aus. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Hinzufügen des MSI für Purview zu AKV":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Zugriffsrichtlinie hinzufügen":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Speichern der Zugriffsrichtlinie":::

## <a name="create-a-new-credential"></a>Erstellen von neuen Anmeldeinformationen

Derzeit werden für Purview die folgenden Arten von Anmeldeinformationen unterstützt:
* Standardauthentifizierung: Sie fügen das **Kennwort** dem Schlüsseltresor als Geheimnis hinzu.
* Dienstprinzipal: Sie fügen den **Dienstprinzipalschlüssel** dem Schlüsseltresor als Geheimnis hinzu. 
* SQL-Authentifizierung: Sie fügen das **Kennwort** dem Schlüsseltresor als Geheimnis hinzu.
* Kontoschlüssel: Sie fügen den **Kontoschlüssel** dem Schlüsseltresor als Geheimnis hinzu.

Hier sind weitere Informationen zum Hinzufügen von Geheimnissen zu einem Schlüsseltresor angegeben: (Artikel zum Schlüsseltresor einfügen)

Nachdem Sie Ihre Geheimnisse in Ihrem Schlüsseltresor gespeichert haben, erstellen Sie Ihre neuen Anmeldeinformationen, indem Sie in der Befehlsleiste für die Anmeldeinformationen die Option „+ Neu“ auswählen. Geben Sie die erforderlichen Informationen an, z. B. die Authentifizierungsmethode und eine Key Vault-Instanz für die Auswahl eines Geheimnisses. Klicken Sie auf „Erstellen“, nachdem Sie alle Details eingegeben haben.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Neue Anmeldeinformationen":::

Vergewissern Sie sich, dass Ihre neuen Anmeldeinformationen in der entsprechenden Liste angezeigt werden und einsatzbereit sind.

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Anzeigen von Anmeldeinformationen":::

## <a name="manage-your-key-vault-connections"></a>Verwalten Ihrer Key Vault-Verbindungen

1. Suchen/Finden von Key Vault-Verbindungen anhand des Namens

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Schlüsseltresor suchen":::

1. Löschen von Key Vault-Verbindungen
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Löschen von Schlüsseltresoren":::

## <a name="manage-your-credentials"></a>Verwalten Ihrer Anmeldeinformationen

1. Suchen/Finden von Anmeldeinformationen anhand des Namens
  
2. Auswählen und Durchführen von Updates für vorhandene Anmeldeinformationen

3. Löschen von Anmeldeinformationen