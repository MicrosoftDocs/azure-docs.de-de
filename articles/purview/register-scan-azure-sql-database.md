---
title: Registrieren und Überprüfen von Azure SQL-Datenbank
description: In diesem Tutorial wird beschrieben, wie Sie Azure SQL-Datenbank überprüfen.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 1fbeedd8643a777b29ebe4993eed7b664240621c
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920279"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Registrieren und Überprüfen von Azure SQL-Datenbank

In diesem Artikel erfahren Sie, wie Sie eine Datenquelle vom Typ „Azure SQL-Datenbank“ in Purview registrieren und eine Überprüfung dafür einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Datenquelle vom Typ „Azure SQL-Datenbank“ unterstützt die folgenden Funktionen:

- **Vollständige und inkrementelle Überprüfungen** zum Erfassen von Metadaten und Klassifizierungen in Azure SQL-Datenbank

- **Herkunft** zwischen Datenressourcen für Kopier- und Datenflussaktivitäten von ADF

### <a name="known-limitations"></a>Bekannte Einschränkungen

Azure Purview unterstützt nicht die Überprüfung von [Ansichten](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15) in Azure SQL-Datenbank. 

## <a name="prerequisites"></a>Voraussetzungen

1. Erstellen Sie ein neues Purview-Konto, wenn Sie noch keines besitzen.

1. Netzwerkzugriff zwischen dem Purview-Konto und Azure SQL-Datenbank


### <a name="set-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Authentifizierung zum Überprüfen von Azure SQL-Datenbank Wenn Sie eine neue Authentifizierung erstellen müssen, müssen Sie [den Datenbankzugriff auf SQL-Datenbank](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage) autorisieren. Es gibt drei Authentifizierungsmethoden, die von Purview aktuell unterstützt werden:

- SQL-Authentifizierung
- Dienstprinzipal
- Verwaltete Identität

#### <a name="sql-authentication"></a>SQL-Authentifizierung

> [!Note]
> Neue Anmeldenamen können nur mit den Anmeldenamen des Serverebenenprinzipals (im Bereitstellungsprozess erstellt) oder von Mitgliedern der `loginmanager`-Datenbankrolle in der Masterdatenbank erstellt werden. Nach dem Gewähren der Berechtigung dauert es ca. **15 Minuten**, bis im Purview-Konto die entsprechenden Berechtigungen zum Überprüfen der Ressourcen verfügbar sind.

Sie können die Anleitung unter [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) befolgen, um einen Anmeldenamen für Azure SQL-Datenbank zu erstellen, falls Sie noch keinen besitzen. Sie benötigen **Benutzername** und **Kennwort** für die nächsten Schritte.

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* für Azure SQL-Datenbank ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden.

#### <a name="service-principal-and-managed-identity"></a>Dienstprinzipal und verwaltete Identität

Sie müssen mehrere Schritte ausführen, um zuzulassen, dass von Purview der Dienstprinzipal oder die **verwaltete Purview-Identität** zum Überprüfen von Azure SQL-Datenbank verwendet werden kann.

   > [!Note]
   > Für Purview werden für die Überprüfung die **Anwendungs-ID (Client)** und der **geheime Clientschlüssel** benötigt.

##### <a name="create-or-use-an-existing-service-principal"></a>Erstellen oder Verwenden eines vorhandenen Dienstprinzipals

> [!Note]
> Überspringen Sie diesen Schritt, wenn Sie die **verwaltete Identität** von Purview verwenden.

Zur Nutzung eines Dienstprinzipals können Sie einen vorhandenen Dienstprinzipal verwenden oder einen neuen erstellen. 

> [!Note]
> Führen Sie die folgenden Schritte aus, falls Sie einen neuen Dienstprinzipal erstellen müssen:
> 1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
> 1. Wählen Sie im Menü auf der linken Seite die Option **Azure Active Directory** aus.
> 1. Wählen Sie **App-Registrierungen** aus.
> 1. Wählen Sie **+ Registrierung einer neuen Anwendung** aus.
> 1. Geben Sie einen Namen für die **Anwendung** (Dienstprinzipalname) ein.
> 1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.
> 1. Wählen Sie als Umleitungs-URI die Option **Web** aus, und geben Sie die gewünschte URL ein. Hierbei muss es sich nicht um eine reale oder geschäftliche URL handeln.
> 1. Klicken Sie anschließend auf **Registrieren**.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Konfigurieren der Azure AD-Authentifizierung im Datenbankkonto

Der Dienstprinzipal oder die verwaltete Identität muss über die Berechtigung zum Abrufen von Metadaten für die Datenbank, die Schemas und die Tabellen verfügen. Darüber hinaus muss das Abfragen der Tabellen möglich sein, damit Stichproben für die Klassifizierung genommen werden können.

- [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure)
- Bei Verwendung einer verwalteten Identität verfügt Ihr Purview-Konto über eine eigene verwaltete Identität, bei der es sich im Grunde um den Purview-Namen handelt, den Sie bei der Erstellung eingegeben haben. Sie müssen einen Azure AD-Benutzer in Azure SQL-Datenbank erstellen, indem Sie genau den Namen der verwalteten Identität von Purview oder Ihren eigenen Dienstprinzipal angeben. Führen Sie dazu die Schritte im Tutorial unter [Erstellen des Dienstprinzipalbenutzers in Azure SQL-Datenbank](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial#create-the-service-principal-user-in-azure-sql-database) aus. Sie müssen der Identität die Berechtigung `db_owner` (**empfohlen**) zuweisen. SQL-Beispielsyntax für das Erstellen eines Benutzers und Gewähren der Berechtigung:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > `Username` ist Ihr eigener Dienstprinzipal oder eine verwaltete Identität von Purview.
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Hinzufügen des Dienstprinzipals zum Schlüsseltresor und zu den Anmeldeinformationen von Purview

> [!Note]
> Wenn Sie die **verwaltete Identität** von Purview verwenden möchten, können Sie diesen Schritt überspringen, da die standardmäßige verwaltete Identität von Purview bereits in den Anmeldeinformationen **Purview-MSI** enthalten ist.

Es ist erforderlich, die Anwendungs-ID und das Geheimnis des Dienstprinzipals abzurufen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Dienstprinzipal.
1. Kopieren Sie die Werte von **Anwendungs-ID (Client)** unter **Übersicht** und **Geheimer Clientschlüssel** unter **Zertifikate und Geheimnisse**.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie unter **Name** einen gewünschten Namen und unter **Geheimer Clientschlüssel** Ihres Dienstprinzipals den **Wert** ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Dienstprinzipal zum Einrichten Ihrer Überprüfung verwenden.

### <a name="firewall-settings"></a>Firewalleinstellungen

Der Datenbankserver muss die Aktivierung von Azure-Verbindungen zulassen. Dadurch kann Azure Purview den Server erreichen und eine Verbindung mit ihm herstellen. Sie können die Schrittanleitung zum [Herstellen von Verbindungen aus Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure) befolgen.

1. Navigieren Sie zu Ihrem Datenbankkonto.
1. Wählen Sie auf der Seite **Übersicht** den Servernamen aus.
1. Wählen Sie **Sicherheit > Firewalls und virtuelle Netzwerke** aus.
1. Wählen Sie für **Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** die Option **Ja** aus.

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Azure-Diensten und -Ressourcen den Zugriff auf diesen Server erlauben" border="true":::
    
> [!Note]
> Derzeit wird die VNET-Konfiguration von Azure Purview nicht unterstützt. Daher ist es nicht möglich, IP-basierte Firewalleinstellungen festzulegen.

## <a name="register-an-azure-sql-database-data-source"></a>Registrieren einer Datenquelle vom Typ „Azure SQL-Datenbank“

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue Azure SQL-Datenbank-Instanz zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie im linken Navigationsbereich die Option **Quellen** aus.

1. Wählen Sie **Registrieren** aus.

1. Wählen Sie unter **Register sources** (Quellen registrieren) die Option **Azure SQL-Datenbank** aus. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Gehen Sie auf dem Bildschirm **Register sources (Azure SQL Database)** (Quellen registrieren (Azure SQL-Datenbank)) wie folgt vor:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
1. Wählen Sie aus, wie auf Ihr gewünschtes Speicherkonto verwiesen werden soll:
   1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Servername** den entsprechenden Server aus.
   1. Alternativ können Sie auch die Option **Manuell eingeben** auswählen und einen **Servernamen** eingeben.
1. Wählen Sie **Fertig stellen** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> Beim Löschen der Überprüfung werden nicht die Ressourcen aus vorherigen Überprüfungen von Azure SQL-Datenbank gelöscht.

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
