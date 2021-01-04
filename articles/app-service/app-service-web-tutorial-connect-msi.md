---
title: 'Tutorial: Zugreifen auf Daten mithilfe einer verwalteten Identität'
description: Hier erfahren Sie, wie Sie die Sicherheit der Datenbankverbindung mithilfe einer verwalteten Identität erhöhen und wie Sie dieses Wissen auf andere Azure-Dienste übertragen.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 1f6757a9f78e3c400d92fd65a0795ceae7570c99
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347573"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: Schützen der Azure SQL-Datenbank-Verbindung von App Service mittels einer verwalteten Identität

[App Service](overview.md) bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching in Azure. Außerdem steht eine [verwaltete Identität](overview-managed-identity.md) für Ihre App zur Verfügung. Hierbei handelt es sich um eine vorgefertigte Lösung zum Schutz des Zugriffs auf [Azure SQL-Datenbank](/azure/sql-database/) und andere Azure-Dienste. Verwaltete Identitäten in App Service machen Ihre App frei von Geheimnissen (wie etwa Anmeldeinformationen in Verbindungszeichenfolgen) und verbessern so die Sicherheit Ihrer App. In diesem Tutorial fügen Sie der in einem der folgenden Tutorials erstellten Beispiel-Web-App eine verwaltete Identität hinzu: 

- [Tutorial: Erstellen einer ASP.NET-App in Azure mit Azure SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutorial: Erstellen einer ASP.NET Core- und Azure SQL-Datenbank-App in Azure App Service](tutorial-dotnetcore-sqldb-app.md)

Danach stellt Ihre Beispiel-App ganz ohne Benutzername und Kennwort eine sichere Verbindung mit SQL-Datenbank her.

> [!NOTE]
> Die in diesem Tutorial behandelten Schritte gelten für die folgenden Versionen:
> 
> - .NET Framework 4.7.2 und höher
> - .NET Core 2.2 und höher
>

Sie lernen Folgendes:

> [!div class="checklist"]
> * Aktivieren von verwalteten Identitäten
> * Gewähren von SQL-Datenbank-Zugriff für die verwaltete Identität
> * Konfigurieren von Entity Framework für die Verwendung der Azure AD-Authentifizierung mit SQL-Datenbank
> * Herstellen einer Verbindung mit SQL-Datenbank über Visual Studio unter Verwendung der Azure AD-Authentifizierung

> [!NOTE]
>Die Azure AD-Authentifizierung _unterscheidet sich_ von der [integrierten Windows-Authentifizierung](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) im lokalen Active Directory (AD DS). AD DS und Azure AD verwenden grundverschiedene Authentifizierungsprotokolle. Weitere Informationen finden Sie in der [Dokumentation zu Azure AD Domain Services](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel fährt dort fort, wo Sie hier aufgehört haben: [Tutorial: Erstellen einer ASP.NET-App in Azure mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md) oder [Tutorial: Erstellen einer ASP.NET Core- und SQL-Datenbank-App in Azure App Service](tutorial-dotnetcore-sqldb-app.md). Absolvieren Sie zunächst eins der beiden Tutorials, sofern noch nicht geschehen. Alternativ können Sie die Schritte für Ihre eigene .NET-App mit SQL-Datenbank anpassen.

Vergewissern Sie sich zum Debuggen Ihrer App mit SQL-Datenbank als Back-End, dass Sie Clientverbindung von Ihrem Computer zugelassen haben. Falls nicht, fügen Sie die Client-IP mithilfe der Schritte unter the steps at [IP-Firewallregeln für Azure SQL-Datenbank and SQL Data Warehouse](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) hinzu.

Bereiten Sie die Umgebung für die Azure CLI vor.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Gewähren von Datenbankzugriff für Azure AD-Benutzer

Aktivieren Sie zunächst die Azure AD-Authentifizierung für SQL-Datenbank, indem Sie einen Azure AD-Benutzer als Active Directory-Administrator des Servers zuweisen. Dieser Benutzer unterscheidet sich von dem Microsoft-Konto, das Sie bei der Registrierung für Ihr Azure-Abonnement verwendet haben. Es muss sich um einen Benutzer handeln, den Sie in Azure AD erstellt, importiert, synchronisiert oder eingeladen haben. Weitere Informationen zu zulässigen Azure AD-Benutzern finden Sie unter [Funktionen und Einschränkungen von Azure AD](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

Enthält Ihr Azure AD-Mandant noch keinen Benutzer, erstellen Sie einen anhand der Schritte unter [Hinzufügen oder Löschen von Benutzern in Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Ermitteln Sie die Objekt-ID des Azure AD-Benutzers mithilfe von [`az ad user list`](/cli/azure/ad/user#az-ad-user-list), und ersetzen Sie den Platzhalter *\<user-principal-name>* . Das Ergebnis wird in einer Variablen gespeichert.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Wenn Sie eine Liste mit allen Benutzerprinzipalnamen in Azure AD anzeigen möchten, führen Sie `az ad user list --query [].userPrincipalName` aus.
>

Fügen Sie diesen Azure AD-Benutzer in Cloud Shell mithilfe des Befehls [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) als Active Directory-Administrator hinzu. Ersetzen Sie im folgenden Befehl *\<server-name>* durch den Namen des SQL-Datenbank-Servers (ohne das Suffix `.database.windows.net`).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Weitere Informationen zum Hinzufügen eines Active Directory-Administrators finden Sie unter [Bereitstellen eines Azure Active Directory-Administrators für Ihren Server](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

## <a name="set-up-visual-studio"></a>Einrichten von Visual Studio

### <a name="windows-client"></a>Windows-Client
Visual Studio für Windows ist in die Azure AD-Authentifizierung integriert. Fügen Sie in Visual Studio Ihren Azure AD-Benutzer hinzu, um in Visual Studio entwickeln und debuggen zu können. Wählen Sie dazu über das Menü **Datei** > **Kontoeinstellungen** aus, und klicken Sie auf **Konto hinzufügen**.

Wählen Sie über das Menü **Extras** > **Optionen** und anschließend **Azure Service Authentication** (Azure-Dienstauthentifizierung) > **Kontoauswahl** aus, um den Azure AD-Benutzer für die Azure-Dienstauthentifizierung festzulegen. Wählen Sie den Azure AD-Benutzer aus, den Sie hinzugefügt haben, und klicken Sie auf **OK**.

Nun können Sie Ihre App mit der SQL-Datenbank als Back-End entwickeln und debuggen und dabei die Azure AD-Authentifizierung verwenden.

### <a name="macos-client"></a>macOS-Client

Visual Studio für Mac ist nicht in die Azure AD-Authentifizierung integriert. Die Bibliothek [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), die Sie später verwenden werden, kann jedoch Token von der Azure CLI verwenden. Um die Entwicklung und das Debuggen in Visual Studio zu ermöglichen, müssen Sie zunächst die [Azure CLI auf dem lokalen Computer installieren](/cli/azure/install-azure-cli).

Wenn die Azure CLI auf dem lokalen Computer installiert ist, führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure AD-Benutzerkonto bei der Azure CLI anzumelden:

```azurecli
az login --allow-no-subscriptions
```
Nun können Sie Ihre App mit der SQL-Datenbank als Back-End entwickeln und debuggen und dabei die Azure AD-Authentifizierung verwenden.

## <a name="modify-your-project"></a>Ändern Ihres Projekts

Die Schritte für Ihr Projekt hängen davon ab, ob es sich um ein ASP.NET-Projekt oder ein ASP.NET Core-Projekt handelt.

- [Ändern des ASP.NET-Projekts](#modify-aspnet)
- [Ändern des ASP.NET Core-Projekts](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Ändern des ASP.NET-Projekts

Öffnen Sie in Visual Studio die Paket-Manager-Konsole, und fügen Sie das NuGet-Paket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) hinzu:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

Nehmen Sie in *Web.config* nacheinander folgende Änderungen vor:

- Fügen Sie in `<configSections>` die folgende Abschnittsdeklaration ein:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- (Die Deklaration muss nach dem schließenden Tag `</configSections>` eingefügt werden.) Fügen Sie anschließend den folgenden XML-Code für `<SqlAuthenticationProviders>` hinzu:

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Suchen Sie nach der Verbindungszeichenfolge `MyDbConnection`, und ersetzen Sie den Wert `connectionString` durch `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Ersetzen Sie die Platzhalter _\<server-name>_ und _\<db-name>_ durch Ihren Servernamen bzw. durch Ihren Datenbanknamen.

> [!NOTE]
> Der von Ihnen eben registrierte SQL-Authentifizierungsanbieter (SqlAuthenticationProvider) basiert auf der zuvor installierten AppAuthentication-Bibliothek. Standardmäßig wird eine systemseitig zugewiesene Identität verwendet. Um eine benutzerseitig zugewiesene Identität zu nutzen, müssen Sie eine zusätzliche Konfiguration bereitstellen. Lesen Sie die Informationen zur [Unterstützung der Verbindungszeichenfolge](../key-vault/general/service-to-service-authentication.md#connection-string-support) für die AppAuthentication-Bibliothek.

Das ist alles, was Sie benötigen, um eine Verbindung mit SQL-Datenbank herzustellen. Beim Debuggen in Visual Studio verwendet Ihr Code den Azure AD-Benutzer, den Sie unter [Einrichten von Visual Studio](#set-up-visual-studio) konfiguriert haben. Sie richten die SQL-Datenbank später ein, um eine Verbindung von der verwalteten Identität ihrer App Service-App zuzulassen.

Drücken Sie `Ctrl+F5`, um die App erneut auszuführen. Die gleiche CRUD-App in Ihrem Browser stellt nun unter Verwendung der Azure AD-Authentifizierung eine Direktverbindung mit der Azure SQL-Datenbank her. Dieses Setup ermöglicht das Ausführen von Datenbankmigrationen über Visual Studio.

### <a name="modify-aspnet-core"></a>Ändern des ASP.NET Core-Projekts

Öffnen Sie in Visual Studio die Paket-Manager-Konsole, und fügen Sie das NuGet-Paket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) hinzu:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

Unter [Tutorial: Erstellen einer ASP.NET Core- und SQL-Datenbank-App in Azure App Service](tutorial-dotnetcore-sqldb-app.md) wird die Verbindungszeichenfolge `MyDbConnection` überhaupt nicht verwendet, weil die lokale Entwicklungsumgebung eine Sqlite-Datenbankdatei und die Azure-Produktionsumgebung eine Verbindungszeichenfolge aus App Service nutzt. Bei der Active Directory-Authentifizierung müssen beide Umgebungen die gleiche Verbindungszeichenfolge verwenden. Ersetzen Sie in *appsettings.json* den Wert der Verbindungszeichenfolge `MyDbConnection` durch Folgendes:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Als Nächstes stellen Sie den Entity Framework-Datenbankkontext mit dem Zugriffstoken für SQL-Datenbank bereit. Fügen Sie in *Data\MyDatabaseContext.cs* den folgenden Code innerhalb der geschweiften Klammern des leeren Konstruktors `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` hinzu:

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Zur Vereinfachung und besseren Übersichtlichkeit wird synchroner Democode verwendet.

Das ist alles, was Sie benötigen, um eine Verbindung mit SQL-Datenbank herzustellen. Beim Debuggen in Visual Studio verwendet Ihr Code den Azure AD-Benutzer, den Sie unter [Einrichten von Visual Studio](#set-up-visual-studio) konfiguriert haben. Sie richten die SQL-Datenbank später ein, um eine Verbindung von der verwalteten Identität ihrer App Service-App zuzulassen. Die `AzureServiceTokenProvider`-Klasse speichert das Token im Arbeitsspeicher zwischen und ruft es kurz vor dem Ablaufdatum von Azure AD ab. Sie benötigen keinen benutzerdefinierten Code, um das Token zu aktualisieren.

> [!TIP]
> Wenn der von Ihnen konfigurierte Azure AD-Benutzer Zugriff auf mehrere Mandanten hat, rufen Sie `GetAccessTokenAsync("https://database.windows.net/", tenantid)` mit der gewünschten Mandanten-ID auf, um das geeignete Zugriffstoken abzurufen.

Drücken Sie `Ctrl+F5`, um die App erneut auszuführen. Die gleiche CRUD-App in Ihrem Browser stellt nun unter Verwendung der Azure AD-Authentifizierung eine Direktverbindung mit der Azure SQL-Datenbank her. Dieses Setup ermöglicht das Ausführen von Datenbankmigrationen über Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Verwenden von Konnektivität mit verwalteter Identität

Als Nächstes konfigurieren Sie Ihre App Service-App so, dass sie beim Herstellen der Verbindung mit SQL-Datenbank eine vom System zugewiesene verwaltete Identität verwendet.

> [!NOTE]
> Die Anweisungen in diesem Abschnitt gelten zwar für eine systemseitig zugewiesene Identität, eine benutzerseitig zugewiesene Identität kann jedoch genauso einfach verwendet werden. Dazu müssen Sie `az webapp identity assign command` ändern, um die gewünschte benutzerseitig zugewiesene Identität zuzuweisen. Stellen Sie dann beim Erstellen des SQL-Benutzers sicher, dass Sie den Namen der benutzerseitig zugewiesenen Identitätsressource anstelle des Websitenamens verwenden.

### <a name="enable-managed-identity-on-app"></a>Aktivieren einer verwalteten Identität für die App

Verwenden Sie den Befehl [az webapp identity assign](/cli/azure/webapp/identity#az-webapp-identity-assign) in Cloud Shell, um eine verwaltete Identität für Ihre Azure-App zu aktivieren. Ersetzen Sie im folgenden Befehl den Platzhalter *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Beispiel für die Ausgabe:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Erteilen von Berechtigungen für eine verwaltete Identität

> [!NOTE]
> Sie können die Identität einer [Azure AD-Gruppe](../active-directory/fundamentals/active-directory-manage-groups.md) hinzufügen und anschließend der Azure AD-Gruppe (und nicht der Identität) SQL-Datenbank-Zugriff erteilen. Mit den folgenden Befehlen wird beispielsweise die verwaltete Identität aus dem vorherigen Schritt zu einer neuen Gruppe namens _myAzureSQLDBAccessGroup_ hinzugefügt:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

Melden Sie sich in Cloud Shell mithilfe des Befehls „sqlcmd“ bei SQL-Datenbank an. Ersetzen Sie _\<server-name>_ _durch den Namen Ihres Servers, \<db-name>_ durch den von Ihrer App verwendeten Datenbanknamen sowie _\<aad-user-name>_ und _\<aad-password>_ durch die Anmeldeinformationen Ihres Azure AD-Benutzers.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Führen Sie an der SQL-Eingabeaufforderung für die gewünschte Datenbank die folgenden Befehle aus, um der App die nötigen Berechtigungen zu erteilen. Beispiel: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* ist der Name der verwalteten Identität in Azure AD. Wird die Identität vom System zugewiesen, ist der Name immer mit dem Namen Ihrer App Service-App identisch. Wenn Sie Berechtigungen für eine Azure AD-Gruppe erteilen möchten, verwenden Sie stattdessen den Anzeigenamen der Gruppe (etwa *myAzureSQLDBAccessGroup*).

Geben Sie `EXIT` ein, um zur Cloud Shell-Eingabeaufforderung zurückzukehren.

> [!NOTE]
> Die Back-End-Dienste verwalteter Identitäten [verwalten darüber hinaus einen Tokencache](overview-managed-identity.md#obtain-tokens-for-azure-resources), der das Token für eine Zielressource nur bei Ablauf aktualisiert. Wenn Sie beim Konfigurieren der Berechtigungen für SQL-Datenbank einen Fehler machen und die Berechtigungen ändern möchten, *nachdem* Sie versucht haben, mit Ihrer App ein Token abzurufen, erhalten Sie tatsächlich erst dann ein neues Token mit den aktualisierten Berechtigungen, wenn das zwischengespeicherte Token abläuft.

### <a name="modify-connection-string"></a>Ändern der Verbindungszeichenfolge

Zur Erinnerung: Die in *Web.config* oder *appsettings.json* vorgenommenen Änderungen funktionieren auch mit der verwalteten Identität. Sie müssen also lediglich die vorhandene Verbindungszeichenfolge in App Service entfernen, die von Visual Studio im Zuge der erstmaligen Bereitstellung Ihrer App erstellt wurde. Verwenden Sie den folgenden Befehl, und ersetzen Sie dabei den Platzhalter *\<app-name>* durch den Namen Ihrer App.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Veröffentlichen der Änderungen

Nun müssen die Änderungen nur noch in Azure veröffentlicht werden.

Wurden Sie vom **[Tutorial: Erstellen einer ASP.NET-App in Azure mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md)** weitergeleitet, veröffentlichen Sie Ihre Änderungen in Visual Studio. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **DotNetAppSqlDb**, und wählen Sie **Veröffentlichen** aus.

![Veröffentlichen über den Projektmappen-Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klicken Sie auf der Veröffentlichungsseite auf **Veröffentlichen**. 

Wurden Sie vom **[Tutorial: Erstellen einer ASP.NET Core- und SQL-Datenbank-App in Azure App Service](tutorial-dotnetcore-sqldb-app.md)** weitergeleitet, veröffentlichen Sie Ihre Änderungen mithilfe von Git mit den folgenden Befehlen:

```bash
git commit -am "configure managed identity"
git push azure master
```

Wenn die neue Webseite Ihre Aufgabenliste anzeigt, stellt Ihre App unter Verwendung der verwalteten Identität eine Verbindung mit der Datenbank her.

![Azure-App nach Code First-Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Die Aufgabenliste sollte sich nun wie gewohnt bearbeiten lassen.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Aktivieren von verwalteten Identitäten
> * Gewähren von SQL-Datenbank-Zugriff für die verwaltete Identität
> * Konfigurieren von Entity Framework für die Verwendung der Azure AD-Authentifizierung mit SQL-Datenbank
> * Herstellen einer Verbindung mit SQL-Datenbank über Visual Studio unter Verwendung der Azure AD-Authentifizierung

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihrer Web-App einen benutzerdefinierten DNS-Namen zuordnen.

> [!div class="nextstepaction"]
> [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](app-service-web-tutorial-custom-domain.md)
