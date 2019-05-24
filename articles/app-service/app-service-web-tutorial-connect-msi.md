---
title: Schützen der SQL-Datenbankverbindung mittels einer verwalteten Identität – Azure App Service | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Sicherheit der Datenbankverbindung mithilfe einer verwalteten Identität erhöhen und wie Sie dieses Wissen auf andere Azure-Dienste übertragen.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: dd84f9b3b68d7a34903241caed7f1f93e685fb57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138966"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: Schützen der Azure SQL-Datenbank-Verbindung von App Service mittels einer verwalteten Identität

[App Service](overview.md) bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching in Azure. Außerdem steht eine [verwaltete Identität](overview-managed-identity.md) für Ihre App zur Verfügung. Hierbei handelt es sich um eine vorgefertigte Lösung zum Schutz des Zugriffs auf [Azure SQL-Datenbank](/azure/sql-database/) und andere Azure-Dienste. Verwaltete Identitäten in App Service machen Ihre App frei von Geheimnissen (wie etwa Anmeldeinformationen in Verbindungszeichenfolgen) und verbessern so die Sicherheit Ihrer App. In diesem Tutorial fügen Sie der ASP.NET-Beispiel-Web-App eine verwaltete Identität hinzu, die Sie hier erstellt: [Tutorial: Erstellen einer ASP.NET-App in Azure mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md). Danach stellt Ihre Beispiel-App ganz ohne Benutzername und Kennwort eine sichere Verbindung mit SQL-Datenbank her.

> [!NOTE]
> Dieses Szenario wird derzeit von .NET Framework 4.6 und höheren Versionen unterstützt, aber nicht von [.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows). [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) unterstützt das Szenario zwar, es wurde jedoch noch nicht in die Standardimages in App Service aufgenommen. 
>

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Aktivieren von verwalteten Identitäten
> * Gewähren von SQL-Datenbank-Zugriff für die verwaltete Identität
> * Konfigurieren des Anwendungscodes für die Authentifizierung mit SQL-Datenbank mittels Azure Active Directory-Authentifizierung
> * Gewähren minimaler Berechtigungen für die verwaltete Identität in SQL-Datenbank

> [!NOTE]
>Die Azure Active Directory-Authentifizierung _unterscheidet sich_ von der [integrierten Windows-Authentifizierung](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) im lokalen Active Directory (AD DS) AD DS und Azure Active Directory verwenden komplett unterschiedliche Authentifizierungsprotokolle. Weitere Informationen finden Sie in der [Dokumentation zu Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel fährt dort fort, wo Sie hier aufgehört haben: [Tutorial: Erstellen einer ASP.NET-App in Azure mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md). Sollten Sie das Tutorial noch nicht absolviert haben, holen Sie dies jetzt nach. Alternativ können Sie die Schritte für Ihre eigene ASP.NET-App mit SQL-Datenbank anpassen.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-identities"></a>Aktivieren von verwalteten Identitäten

Verwenden Sie den Befehl [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) in Cloud Shell, um eine verwaltete Identität für Ihre Azure-App zu aktivieren. Ersetzen Sie im folgenden Befehl den Platzhalter *\<app name>*.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Das folgende Beispiel zeigt die Ausgabe nach der Erstellung der Identität in Azure Active Directory:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

Der Wert von `principalId` wird im nächsten Schritt verwendet. Wenn Sie die Details der neuen Identität in Azure Active Directory anzeigen möchten, führen Sie den folgenden optionalen Befehl mit dem Wert von `principalId` aus:

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>Gewähren von Datenbankzugriff für die Identität

Als Nächstes verwenden Sie den Befehl [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest) in Cloud Shell, um der verwalteten Identität Ihrer App Zugriff auf die Datenbank zu gewähren. Ersetzen Sie im folgenden Befehl die Platzhalter *\<server_name>* und „<principalid_from_last_step>“. Geben Sie für *\<admin_user>* einen Administratornamen ein.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

Die verwaltete Identität hat jetzt Zugriff auf Ihren Azure SQL-Datenbank-Server.

## <a name="modify-connection-string"></a>Ändern der Verbindungszeichenfolge

Verwenden Sie den Befehl [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell, um die zuvor festgelegte Verbindung für Ihre App zu ändern. Ersetzen Sie im folgenden Befehl *\<app name>* durch den Namen Ihrer App sowie *\<server_name>* und *\<db_name>* durch die Werte für Ihre SQL-Datenbank.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Ändern des ASP.NET-Codes

Öffnen Sie in Visual Studio die Paket-Manager-Konsole, und fügen Sie das NuGet-Paket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) hinzu:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.1.0-preview
```

Öffnen Sie _Models\MyDatabaseContext.cs_, und fügen Sie am Anfang der Datei die folgende `using`-Anweisung ein:

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

Fügen Sie in der `MyDatabaseContext`-Klasse den folgenden Konstruktor hinzu:

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

Dieser Konstruktor konfiguriert ein benutzerdefiniertes SqlConnection-Objekt für die Verwendung eines Zugriffstokens für Azure SQL-Datenbank aus App Service. Mit dem Zugriffstoken authentifiziert sich Ihre App Service-App mit ihrer verwalteten Identität bei Azure SQL-Datenbank. Weitere Informationen finden Sie unter [Abrufen von Tokens für Azure-Ressourcen](overview-managed-identity.md#obtaining-tokens-for-azure-resources). Die `if`-Anweisung ermöglicht es, die App weiterhin lokal mit LocalDB zu testen.

> [!NOTE]
> `SqlConnection.AccessToken` wird derzeit nur in .NET Framework 4.6 und höheren Versionen sowie in [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) unterstützt, aber nicht in [.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows).
>

Öffnen Sie zur Verwendung dieses neuen Konstruktors `Controllers\TodosController.cs`, und suchen Sie nach der Zeile `private MyDatabaseContext db = new MyDatabaseContext();`. Der vorhandene Code verwendet den `MyDatabaseContext`-Standardcontroller, um eine Datenbank unter Verwendung der Standardverbindungszeichenfolge zu erstellen, die vor der [von Ihnen vorgenommenen Änderung](#modify-connection-string) den Benutzernamen und das Kennwort als Klartext enthielt.

Ersetzen Sie die gesamte Zeile durch den folgenden Code:

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new System.Data.SqlClient.SqlConnection());
```

### <a name="publish-your-changes"></a>Veröffentlichen der Änderungen

Nun müssen die Änderungen nur noch in Azure veröffentlicht werden.

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **DotNetAppSqlDb**, und wählen Sie **Veröffentlichen** aus.

![Veröffentlichen über den Projektmappen-Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klicken Sie auf der Veröffentlichungsseite auf **Veröffentlichen**. Wenn die neue Webseite Ihre Aufgabenliste anzeigt, stellt Ihre App unter Verwendung der verwalteten Identität eine Verbindung mit der Datenbank her.

![Azure-App nach Code First-Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Die Aufgabenliste sollte sich nun wie gewohnt bearbeiten lassen.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Gewähren minimaler Berechtigungen für die Identität

In den obigen Schritten ist Ihnen vielleicht aufgefallen, dass Ihre verwaltete Identität mit SQL Server als Azure AD-Administrator verbunden ist. Um die minimale Berechtigungen für Ihre verwaltete Identität zu gewähren, müssen Sie sich bei Ihrem Azure SQL-Datenbank-Server als Azure AD-Administrator anmelden, und dann eine Azure Active Directory-Gruppe hinzufügen, die die verwaltete Identität enthält. 

### <a name="add-managed-identity-to-an-azure-active-directory-group"></a>Hinzufügen der verwalteten Identität zu einer Azure Active Directory-Gruppe

Fügen Sie die verwaltete Identität für Ihre App über Cloud Shell einer neuen Azure Active Directory-Gruppe namens _myAzureSQLDBAccessGroup_ hinzu, wie im folgenden Skript gezeigt:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Wenn Sie die Parameter `--query objectId --output tsv` weglassen, wird die vollständige JSON-Ausgabe für die einzelnen Befehle angezeigt.

### <a name="reconfigure-azure-ad-administrator"></a>Ändern der Konfiguration für den Azure AD-Administrator

Bis jetzt haben Sie die verwaltete Identität als Azure AD-Administrator für Ihre SQL-Datenbank zugewiesen. Da diese Identität nicht für die interaktive Anmeldung (zum Hinzufügen von Datenbankbenutzern) verwendet werden kann, müssen Sie Ihren echten Azure AD-Benutzer verwenden. Gehen Sie zum Hinzufügen Ihres Azure AD-Benutzers wie unter [Bereitstellen eines Azure Active Directory-Administrators für Ihren Azure SQL-Datenbank-Server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) beschrieben vor. 

> [!IMPORTANT]
> Entfernen Sie diesen Azure AD-Administrator für Ihre SQL-Datenbank nach dem Hinzufügen nur, wenn Sie den Azure AD-Zugriff auf die SQL-Datenbank vollständig (von allen Azure AD-Konten) deaktivieren möchten.
> 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Gewähren von Berechtigungen für die Azure Active Directory-Gruppe

Melden Sie sich in Cloud Shell mithilfe des Befehls „sqlcmd“ bei SQL-Datenbank an. Ersetzen Sie _\<server\_name>_ durch den Namen Ihres SQL-Datenbank-Servers, _\<db\_name>_ durch den von Ihrer App verwendeten Datenbanknamen sowie _\<AADuser\_name>_ und _\<AADpassword>_ durch die Anmeldeinformationen Ihres Azure AD-Benutzers.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

Führen Sie an der SQL-Eingabeaufforderung für die gewünschte Datenbank die folgenden Befehle aus, um die zuvor erstellte Azure Active Directory-Gruppe hinzuzufügen und der App die nötigen Berechtigungen zu erteilen. Beispiel: 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Geben Sie `EXIT` ein, um zur Cloud Shell-Eingabeaufforderung zurückzukehren. 

## <a name="next-steps"></a>Nächste Schritte

Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Aktivieren von verwalteten Identitäten
> * Gewähren von SQL-Datenbank-Zugriff für die verwaltete Identität
> * Konfigurieren des Anwendungscodes für die Authentifizierung mit SQL-Datenbank mittels Azure Active Directory-Authentifizierung
> * Gewähren minimaler Berechtigungen für die verwaltete Identität in SQL-Datenbank

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihrer Web-App einen benutzerdefinierten DNS-Namen zuordnen.

> [!div class="nextstepaction"]
> [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](app-service-web-tutorial-custom-domain.md)
