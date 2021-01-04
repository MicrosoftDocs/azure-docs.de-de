---
title: ActiveDirectoryInteractive zum Herstellen einer Verbindung mit SQL
description: C#-Codebeispiel mit Erklärungen für das Herstellen der Verbindung mit Azure SQL-Datenbank mit dem SqlAuthenticationMethod.ActiveDirectoryInteractive-Modus.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 93831ec4c1dc3e34c2ea144e71b67dae711ee870
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841647"
---
# <a name="connect-to-azure-sql-database-with-azure-ad-multi-factor-authentication"></a>Herstellen einer Verbindung zu Azure SQL-Datenbank mit Azure AD Multi-Factor Authentication
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel enthält ein C#-Programm, das eine Verbindung mit einer Azure SQL-Datenbank herstellt. Das Programm nutzt den interaktiven Authentifizierungsmodus, der die [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) unterstützt.

Weitere Informationen zur Unterstützung der Multi-Factor Authentication für SQL-Tools finden Sie unter [Azure Active Directory-Unterstützung in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multi-Factor Authentication für Azure SQL-Datenbanken

Ab .NET Framework, Version 4.7.2, verfügt die Enumeration [`SqlAuthenticationMethod`](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) über den neuen Wert `ActiveDirectoryInteractive`. In einem C#-Clientprogramm weist dieser Enumerationswert das System an, den interaktiven Azure Active Directory-Modus (Azure AD) zu verwenden, der die Multi-Factor Authentication für Verbindungen mit einer Azure SQL-Datenbank unterstützt. Der Benutzer, der das Programm ausführt, sieht die folgenden Dialogfelder:

* Ein Dialogfeld, in dem ein Azure AD-Benutzername anzeigt und das Benutzerkennwort angefordert wird.

   Wenn die Domäne des Benutzers eine Azure AD-Verbunddomäne ist, wird dieses Dialogfeld nicht angezeigt, da kein Kennwort erforderlich ist.

   Wenn der Benutzer gemäß der Azure AD-Richtlinie die Multi-Factor Authentication verwenden muss, werden die nächsten beiden Dialogfelder angezeigt.

* Wenn ein Benutzer die Multi-Factor Authentication zum ersten Mal ausführt, wird er in einem Dialogfeld zur Angabe einer Mobiltelefonnummer für den Empfang von SMS aufgefordert. Jede Nachricht enthält den *Prüfcode*, den der Benutzer im nächsten Dialogfeld eingeben muss.

* Ein weiteres Dialogfeld fordert die Eingabe des Multi-Factor Authentication-Überprüfungscodes an, den das System an ein Mobiltelefon gesendet hat.

Informationen zum Konfigurieren von Azure AD, sodass Multi-Factor Authentication erforderlich ist, finden Sie unter [Erste Schritte mit Azure AD Multi-Factor Authentication in der Cloud](../../active-directory/authentication/howto-mfa-getstarted.md).

Screenshots zu diesen Dialogfeldern finden Sie unter [Konfigurieren der mehrstufigen Authentifizierung für SQL Server Management Studio und Azure AD](authentication-mfa-ssms-configure.md).

> [!TIP]
> Sie können die .NET Framework-APIs auf der Seite mit dem Tool [.NET API-Browser](/dotnet/api/) suchen.
>
> Mit dem optionalen [?term=&lt;Suchwert&gt;](/dotnet/api/?term=SqlAuthenticationMethod)-Parameter können Sie auch eine direkte Suche ausführen.

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurieren der C#-Anwendung im Azure-Portal

Zu Beginn wird vorausgesetzt, dass ein [logischer SQL-Server](logical-servers.md) erstellt wurde und verfügbar ist.

### <a name="register-your-app-and-set-permissions"></a>Registrieren der App und Festlegen von Berechtigungen

Zur Verwendung der Azure AD-Authentifizierung muss sich das C#-Programm als Azure AD-Anwendung registrieren. Um eine App zu registrieren, müssen Sie entweder Azure AD-Administrator oder ein Benutzer sein, dem die Azure AD-Rolle *Anwendungsentwickler* zugewiesen wurde. Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

Nach Abschluss einer App-Registrierung wird eine **Anwendungs-ID** generiert und angezeigt. Das Programm benötigt diese ID, um eine Verbindung herzustellen.

So können Sie sich registrieren und die erforderlichen Berechtigungen für die Anwendung festlegen:

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.

    ![App-Registrierung](./media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Sobald die App-Registrierung erstellt wurde, wird der Wert für die **Anwendungs-ID** generiert und angezeigt.

    ![Angezeigte App-ID](./media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Wählen Sie **API-Berechtigungen** > **Berechtigung hinzufügen** aus.

    ![Berechtigungseinstellungen für die registrierte App](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Wählen Sie **Von meiner Organisation verwendete APIs** aus, geben Sie im Suchfeld **Azure SQL-Datenbank** ein, und wählen Sie **Azure SQL-Datenbank** aus.

    ![Hinzufügen von Zugriff auf eine API für Azure SQL-Datenbank](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Wählen Sie **Delegierte Berechtigungen** > **user_impersonation** > **Berechtigungen hinzufügen** aus.

    ![Delegieren von Berechtigungen an die API für Azure SQL-Datenbank](./media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-server"></a>Festlegen eines Azure AD-Administrators für den Server

Damit Ihr C#-Programm ausgeführt wird, muss ein Administrator für den [logischen SQL-Server](logical-servers.md) Ihrem Server einen Azure AD-Administrator zuweisen.

Wählen Sie auf der Seite **SQL Server** die Option **Active Directory-Administrator** > **Administrator festlegen** aus.

Weitere Informationen zu Azure AD-Administratoren und Benutzern für Azure SQL-Datenbank finden Sie unter [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL-Datenbank](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Hinzufügen eines Benutzers ohne Administratorrechte zu einer bestimmten Datenbank (optional)

Ein Azure AD-Administrator für einen [logischen SQL-Server](logical-servers.md) kann das C#-Beispielprogramm ausführen. Ein Azure AD-Benutzer kann das Programm ausführen, wenn er der Datenbank hinzugefügt wurde. Ein Azure AD SQL-Administrator oder Azure AD-Benutzer, der bereits in der Datenbank vorhanden ist und für die Datenbank über die Berechtigung `ALTER ANY USER` verfügt,kann einen Benutzer hinzufügen.

Sie können der Datenbank mit dem SQL-Befehl [`Create User`](/sql/t-sql/statements/create-user-transact-sql) einen Benutzer hinzufügen. z. B. `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Weitere Informationen finden Sie unter [Verwenden der Azure Active Directory-Authentifizierung für die Authentifizierung bei SQL-Datenbank, der verwalteten Instanz oder Azure Synapse Analytics](authentication-aad-overview.md).

## <a name="new-authentication-enum-value"></a>Neuer Enumerationswert für die Authentifizierung

Das C#-Beispiel basiert auf dem [`System.Data.SqlClient`](/dotnet/api/system.data.sqlclient)-Namespace. Die Enumeration `SqlAuthenticationMethod` ist von besonderem Interesse für die Multi-Factor Authentication. Sie verfügt über die folgenden Werte:

* `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Verwenden Sie diesen Wert mit einem Azure AD-Benutzernamen, um Multi-Factor Authentication zu implementieren. Dieser Wert bildet den Schwerpunkt dieses Artikels. Er erzeugt eine interaktive Benutzeroberfläche durch die Anzeige von Dialogfeldern für das Kennwort des Benutzers und dann für die Multi-Factor Authentication-Überprüfung, wenn Multi-Factor Authentication für diesen Benutzer erzwungen wird. Dieser Wert steht ab .NET Framework Version 4.7.2 zur Verfügung.

* `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Verwenden Sie diesen Wert für ein *Verbundkonto*. Bei einem Verbundkonto ist der Benutzername der Windows-Domäne bekannt. Diese Authentifizierungsmethode unterstützt keine Multi-Factor Authentication.

* `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Verwenden Sie diesen Wert für Authentifizierungen, die Azure AD-Benutzernamen und -Kennwörter erfordern. Azure SQL-Datenbank führt die Authentifizierung durch. Diese Methode unterstützt keine Multi-Factor Authentication.

> [!NOTE]
> Wenn Sie .NET Core verwenden, werden Sie wahrscheinlich den Namespace [Microsoft.Data.SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) nutzen. Weitere Informationen finden Sie im folgenden [Blogbeitrag](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/).

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Festlegen von C#-Parameterwerten über das Azure-Portal

Damit das C#-Programm erfolgreich ausgeführt wird, müssen Sie statischen Feldern ordnungsgemäße Werte zuweisen. Hier sehen Sie Felder mit Beispielwerten. Sie erfahren auch, wo Sie die benötigten Werte im Azure-Portal finden.

| Name des statischen Felds | Beispielwert | Wo im Azure-Portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL-Server** > **Nach Name filtern** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active Directory** > **Benutzer** > **Neuer Gastbenutzer** |
| Initial_DatabaseName | „myDatabase“ | **SQL-Server** > **SQL-Datenbanken** |
| ClientApplicationID | „a94f9c62-97fe-4d19-b06d-111111111111“ | **Azure Active Directory** > **App-Registrierungen** > **Nach Namen suchen** > **Anwendungs-ID** |
| RedirectUri | new Uri("https://mywebserver.com/") | **Azure Active Directory** > **App-Registrierungen** > **Nach Namen suchen** >  *[Ihre-App-Registrierung]*  > **Einstellungen** > **RedirectURIs**<br /><br />Im Rahmen dieses Artikels ist jeder gültige Wert für RedirectUri geeignet, da er hier nicht verwendet wird. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Durchführen einer Überprüfung mit SQL Server Management Studio

Bevor Sie das C#-Programm ausführen, sollten Sie in SQL Server Management Studio (SSMS) überprüfen, ob das Setup und die Konfigurationen ordnungsgemäß funktionieren. Fehler im C#-Programm können dann im Quellcode eingegrenzt werden.

### <a name="verify-server-level-firewall-ip-addresses"></a>Überprüfen der IP-Adressen der Firewall auf Serverebene

Führen Sie SSMS auf demselben Computer und im selben Gebäude aus, auf bzw. in dem auch das C#-Programm ausgeführt werden soll. Für diesen Test kann ein beliebiger **Authentifizierungsmodus** verwendet werden. Bei Anzeichen, dass der Server Ihre IP-Adresse nicht akzeptiert, finden Sie hilfreiche Informationen unter [Firewallregeln auf Server- und Datenbankebene](firewall-configure.md).

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Überprüfen von Azure Active Directory Multi-Factor Authentication

Führen Sie SSMS erneut aus, wobei Sie **Authentifizierung** dieses Mal auf **Active Directory: universell mit MFA** festlegen. Diese Option erfordert SSMS, Version 17.5 oder höher.

Weitere Informationen finden Sie unter [Konfigurieren der Multi-Factor Authentication für SQL Server Management Studio und Azure AD](authentication-mfa-ssms-configure.md).

> [!NOTE]
> Wenn Sie die Datenbank als Gastbenutzer verwenden, müssen Sie zusätzlich den Azure AD-Domänennamen für die Datenbank angeben: Wählen Sie **Optionen** > **AD-Domänenname oder -Mandanten-ID** aus. Um den Domänennamen im Azure-Portal zu suchen, wählen Sie **Azure Active Directory** > **Benutzerdefinierte Domänennamen** aus. Im C#-Beispielprogramm muss kein Domänenname angegeben werden.

## <a name="c-code-example"></a>C#-Codebeispiel

> [!NOTE]
> Wenn Sie .NET Core verwenden, werden Sie wahrscheinlich den Namespace [Microsoft.Data.SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) nutzen. Weitere Informationen finden Sie im folgenden [Blogbeitrag](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/).

Das C#-Beispielprogramm basiert auf der DLL-Assembly [*Microsoft.IdentityModel.Clients.ActiveDirectory*](/dotnet/api/microsoft.identitymodel.clients.activedirectory).

Um das Paket zu installieren, wählen Sie in Visual Studio **Projekt** > **NuGet-Pakete verwalten** aus. Suchen und installieren Sie **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Dies ist ein Beispiel für C#-Quellcode.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  .
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  .
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Dies ist ein Beispiel für die C#-Testbefehlsausgabe.

```C#
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

& [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)