---
title: Registrieren und Überprüfen einer lokalen SQL Server-Datenquelle
description: In diesem Tutorial ist beschrieben, wie Sie eine lokale SQL Server-Datenquelle mit einer selbstgehosteten Integration Runtime überprüfen können.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: b5f4218cfcd5f9ccfbe43efac46e2f70fdc30905
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574956"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Registrieren und Überprüfen einer lokalen SQL Server-Datenquelle

In diesem Artikel erfahren Sie, wie Sie eine SQL Server-Datenquelle in Purview registrieren und eine Überprüfung für diese einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Für eine lokale SQL Server-Datenquelle werden die folgenden Funktionen unterstützt:

- **Vollständige und inkrementelle Überprüfungen**, um Metadaten und Klassifizierungen aus einem lokalen Netzwerk oder aus einer SQL Server-Datenquelle zu erfassen, die auf einer Azure-VM installiert ist

- **Herkunft** zwischen Datenobjekten für Kopier-/Dataflowaktivitäten von ADF

Für eine lokale SQL Server-Datenquelle wird Folgendes unterstützt:

- Jede Version von SQL ab SQL Server 2019 bis zurück zu SQL Server 2000

- Authentifizierungsmethode: SQL-Authentifizierung

### <a name="known-limitations"></a>Bekannte Einschränkungen

Azure Purview unterstützt nicht die Überprüfung von [Ansichten](/sql/relational-databases/views/views) in SQL Server.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie vor dem Registrieren der Datenquellen zunächst ein Azure Purview-Konto. Weitere Informationen zum Erstellen eines Purview-Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).

- Richten Sie eine [selbstgehostete Integration Runtime](manage-integration-runtimes.md) ein, um die Datenquelle zu überprüfen.

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Es gibt nur eine Möglichkeit, die Authentifizierung für eine lokale SQL Server-Datenquelle einzurichten:

- SQL-Authentifizierung

### <a name="sql-authentication"></a>SQL-Authentifizierung

Das SQL-Konto muss Zugriff auf die **Masterdatenbank** besitzen. Dies liegt daran, dass sich `sys.databases` in der Masterdatenbank befindet. Der Purview-Scanner muss `sys.databases` durchlaufen, um alle SQL-Datenbankinstanzen auf dem Server zu finden.

#### <a name="using-an-existing-server-administrator"></a>Verwenden eines vorhandenen Serveradministrators

Wenn Sie einen vorhandenen Serveradministrator (sa) verwenden möchten, um Ihre lokale SQL Server-Datenquelle zu überprüfen, stellen Sie Folgendes sicher:

1. `sa` ist kein Windows-Authentifizierungskonto.

2. Die Anmeldung auf Serverebene, die Sie verwenden möchten, muss die Serverrollen „public“ und „sysadmin“ aufweisen. Sie können dies überprüfen, indem Sie eine Verbindung mit dem Server herstellen, zu SQL Server Management Studio (SSMS) navigieren, zu „Sicherheit“ navigieren, den Anmeldenamen auswählen, den Sie verwenden möchten, mit der rechten Maustaste auf **Eigenschaften** klicken und dann **Serverrollen** auswählen.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Anmeldung auf Serverebene":::

#### <a name="creating-a-new-login-and-user"></a>Erstellen einer neuen Anmeldung und eines neuen Benutzers

Führen Sie die folgenden Schritte aus, wenn Sie eine neue Anmeldung und einen neuen Benutzer erstellen möchten, der Ihre SQL Server-Datenquelle überprüfen (scannen) kann:

> [!Note]
   > Alle nachfolgenden Schritte können mit dem [hier](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql) bereitgestellten Code ausgeführt werden.

1. Navigieren Sie zu SQL Server Management Studio (SSMS), stellen Sie eine Verbindung mit dem Server her, navigieren Sie zu „Sicherheit“, klicken Sie mit der rechten Maustaste auf „Anmeldung“, und erstellen Sie eine neue Anmeldung. Wählen Sie „SQL Server-Authentifizierung“ aus.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Eine neue Anmeldung und einen neuen Benutzer erstellen":::

2. Wählen Sie „Serverrollen“ im linken Navigationsbereich aus, und stellen Sie sicher, dass die Rolle „public“ zugewiesen ist.

3. Wählen Sie „Benutzerzuordnung“ im linken Navigationsbereich aus, wählen Sie alle Datenbanken in der Zuordnung aus, und wählen Sie dann die folgende Datenbankrolle aus: **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="Benutzerzuordnung":::

4. Klicken Sie zum Speichern auf OK.

5. Navigieren Sie erneut zu dem von Ihnen erstellten Benutzer, indem Sie mit der rechten Maustaste klicken und **Eigenschaften** auswählen. Geben Sie ein neues Kennwort ein, und bestätigen Sie es. Wählen Sie die Option „Altes Kennwort angeben“ aus, und geben Sie das alte Kennwort ein. **Sie müssen Ihr Kennwort ändern, sobald Sie eine neue Anmeldung erstellen.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="Kennwort ändern":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Speichern Ihres SQL-Anmeldekennworts in einem Schlüsseltresor und Erstellen von Anmeldeinformationen in Purview

1. Navigieren Sie im Azure-Portal1 zu Ihrem Schlüsseltresor. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* aus Ihrer  SQL Server-Anmeldung ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden.

## <a name="register-a-sql-server-data-source"></a>Registrieren einer SQL Server-Datenquelle

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie im linken Navigationsbereich unter „Quellen und Überprüfen“ die Option **Integration Runtimes** aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls nicht, führen Sie die [hier](manage-integration-runtimes.md) beschriebenen Schritte aus, um eine selbstgehostete Integration Runtime für Überprüfungen auf einem lokalen virtuellen Computer oder auf einem virtuellen Azure-Computer mit Zugriff auf Ihr lokales Netzwerk zu erstellen.

1. Wählen Sie im linken Navigationsbereich die Option **Quellen** aus.

1. Wählen Sie **Registrieren** aus.

1. Wählen Sie **SQL Server** und dann **Weiter** aus.

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Die SQL Server-Datenquelle einrichten":::

5. Geben Sie einen Anzeigenamen und Serverendpunkt an, und wählen Sie dann **Fertig stellen** aus, um die Datenquelle zu registrieren. Hat Ihre SQL Server-Datenquelle z. B. den FQDN **foobar.database.windows.net**, geben Sie *foobar* als Serverendpunkt ein.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
