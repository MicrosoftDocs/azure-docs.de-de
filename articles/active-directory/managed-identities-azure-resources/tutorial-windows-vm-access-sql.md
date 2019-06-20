---
title: Verwenden der systemseitig zugewiesenen verwalteten Identität eines virtuellen Windows-Computers für den Zugriff auf Azure SQL
description: In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Windows-Computers verwenden, um auf Azure SQL zuzugreifen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6d5452f23e830ca7a9ffe5ca5ed3d4aa12fb717
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66236044"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Tutorial: Verwenden der systemseitig zugewiesenen verwalteten Identität eines virtuellen Windows-Computers für den Zugriff auf Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene Identität für einen virtuellen Windows-Computer verwenden, um auf eine Azure SQL Server-Instanz zuzugreifen. Verwaltete Dienstidentitäten werden von Azure automatisch verwaltet und ermöglichen Ihnen die Authentifizierung für Dienste, die die Azure AD-Authentifizierung unterstützen, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Gewähren des Zugriffs auf eine Azure SQL-Server durch Ihre VM
> * Aktivieren der Azure AD-Authentifizierung für den SQL-Server
> * Erstellen eines in der Datenbank enthaltenen Benutzers, der die vom System zugewiesene Identität des virtuellen Computers darstellt
> * Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Aufrufen eines Azure SQL-Servers

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Gewähren des Zugriffs auf eine Datenbank auf einem Azure SQL-Server durch Ihre VM

Um Ihrem virtuellen Computer Zugriff auf eine Datenbank in einer Azure SQL Server-Instanz zu gewähren, können Sie eine bereits vorhandene SQL Server-Instanz verwenden oder eine neue erstellen. Zum Erstellen eines neuen Servers und einer Datenbank mithilfe des Azure-Portals befolgen Sie diesen [Azure SQL-Schnellstart](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Es gibt in der [Azure SQL-Dokumentation](https://docs.microsoft.com/azure/sql-database/) auch Schnellstarts, in denen die Azure-Befehlszeilenschnittstelle und Azure PowerShell verwendet werden.

Zur Gewährung des Datenbankzugriffs für Ihren virtuellen Computer sind zwei Schritte erforderlich:

1. Aktivieren der Azure AD-Authentifizierung für den SQL-Server
2. Erstellen eines in der Datenbank **enthaltenen Benutzers**, der die vom System zugewiesene Identität des virtuellen Computers darstellt

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Aktivieren der Azure AD-Authentifizierung für den SQL-Server

[Konfigurieren Sie die Azure AD-Authentifizierung für die SQL Server-Instanz](/azure/sql-database/sql-database-aad-authentication-configure) mithilfe der folgenden Schritte:

1.  Wählen Sie im Azure-Portal im Navigationsbereich links **SQL-Server** aus.
2.  Klicken Sie auf den SQL-Server, für den die Azure AD-Authentifizierung aktiviert werden soll.
3.  Klicken Sie auf dem Blatt im Bereich **Einstellungen** auf **Active Directory-Administrator**.
4.  Klicken Sie auf der Befehlsleiste auf **Administrator festlegen**.
5.  Wählen Sie ein Azure AD-Benutzerkonto als Administrator des Servers aus, und klicken Sie auf **Auswählen**.
6.  Klicken Sie auf der Befehlsleiste auf **Speichern**.

## <a name="create-a-contained-user-in-the-database-that-represents-the-vms-system-assigned-identity"></a>Erstellen eines in der Datenbank enthaltenen Benutzers, der die vom System zugewiesene Identität des virtuellen Computers darstellt

Für den nächsten Schritt benötigen Sie [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Bevor Sie beginnen, sollten Sie die folgenden Artikel mit Hintergrundinformationen zur Azure AD-Integration lesen:

- [Universelle Authentifizierung bei SQL-Datenbank und SQL Data Warehouse (SSMS-Unterstützung für MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL-Datenbank oder SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

SQL-Datenbank benötigt eindeutige AAD-Anzeigenamen. Mit dieser Option müssen die für verwaltete Identität aktivierten AAD-Konten wie z.B. Benutzer, Gruppen und Dienstprinzipale (Anwendungen) sowie VM-Namen in Bezug auf den Anzeigenamen eindeutig im AAD definiert werden. SQL-Datenbank überprüft den AAD-Anzeigenamen während der T-SQL-Erstellung solcher Benutzer. Wenn er nicht eindeutig ist, kann der Befehl, der die Angabe eines AAD-Anzeigenamens für ein bestimmtes Konto anfordert, nicht ausgeführt werden.

1. Starten Sie SQL Server Management Studio.
2. Geben Sie im Dialogfeld **Mit Server verbinden** den Namen Ihres SQL-Servers im Feld **Servername** ein.
3. Wählen Sie im Feld **Authentifizierung** die Option **Active Directory: universell mit MFA-Unterstützung** aus.
4. Geben Sie im Feld **Benutzername** den Namen des Azure AD-Kontos ein, das Sie als Serveradministrator festlegen möchten, z.B. helen@woodgroveonline.com.
5. Klicken Sie auf **Options**.
6. Geben Sie im Feld **Mit Datenbank verbinden** den Namen der systemfremden Datenbank ein, die Sie konfigurieren möchten.
7. Klicken Sie auf **Verbinden**. Schließen Sie den Anmeldevorgang ab.
8. Erweitern Sie im **Objekt-Explorer** den Ordner **Datenbanken**.
9. Klicken Sie mit der rechten Maustaste auf eine Benutzerdatenbank, und klicken Sie dann auf **Neue Abfrage**.
10. Geben Sie im Abfragefenster die folgende Zeile ein, und klicken Sie auf der Symbolleiste auf **Ausführen**:

    > [!NOTE]
    > Im folgenden Befehl ist `VMName` der Name des virtuellen Computers, auf dem Sie im Abschnitt mit den Voraussetzungen die vom System zugewiesene Identität aktiviert haben.
    
    ```
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```
    
    Der Befehl sollte erfolgreich ausgeführt werden und den enthaltenen Benutzer für die vom System zugewiesene Identität des virtuellen Computers erstellen.
11. Leeren Sie das Abfragefenster, geben Sie die folgende Zeile ein, und klicken Sie auf der Symbolleiste auf **Ausführen**:

    > [!NOTE]
    > Im folgenden Befehl ist `VMName` der Name des virtuellen Computers, auf dem Sie im Abschnitt mit den Voraussetzungen die vom System zugewiesene Identität aktiviert haben.
    
    ```
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    Der Befehl sollte erfolgreich abgeschlossen werden und dem enthaltenen Benutzer die Möglichkeit gewähren, die gesamte Datenbank zu lesen.

Auf dem virtuellen Computer ausgeführter Code kann jetzt unter Verwendung der systemseitig zugewiesenen verwalteten Identität ein Token abrufen und damit die Authentifizierung bei der SQL Server-Instanz durchführen.

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-sql"></a>Abrufen eines Zugriffstokens mithilfe der systemseitig zugewiesenen verwalteten Identität des virtuellen Computers und Verwenden dieses Zugriffstokens zum Aufrufen von Azure SQL

Azure SQL unterstützt die Azure AD-Authentifizierung nativ, sodass Zugriffstoken, die mit verwalteten Identitäten für Azure-Ressourcen abgerufen wurden, direkt angenommen werden können. Verwenden Sie das Verfahren für **Zugriffstoken**, um eine Verbindung mit SQL herzustellen. Dieses ist Teil des Azure SQL-Integration in Azure AD und unterscheidet sich vom Bereitstellen von Anmeldeinformationen in der Verbindungszeichenfolge.

Mit dem folgenden .NET-Codebeispiel wird eine Verbindung mit SQL mithilfe eines Zugriffstokens hergestellt. Dieser Code muss auf dem virtuellen Computer ausgeführt werden, um auf den Endpunkt der systemseitig zugewiesenen verwalteten Identität des virtuellen Computers zugreifen zu können. Für die Verwendung des Verfahrens mit Zugriffstoken ist **.NET Framework 4.6** oder höher erforderlich. Ersetzen Sie die Werte für AZURE-SQL-SERVERNAME und DATABASE entsprechend. Beachten Sie, dass die Ressourcen-ID für Azure SQL `https://database.windows.net/` lautet.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Eine andere schnelle Möglichkeit zum Testen der umfassenden Einrichtung ohne Schreiben und Bereitstellen einer App auf dem virtuellen Computer bietet PowerShell.

1.  Navigieren Sie im Portal zu **Virtuelle Computer**, wechseln Sie dann zu Ihrem virtuellen Windows-Computer, und klicken Sie in der **Übersicht** auf **Verbinden**.
2.  Geben Sie Ihren **Benutzernamen** und Ihr **Kennwort** ein, das Sie beim Erstellen des virtuellen Windows-Computers hinzugefügt haben.
3.  Sie haben nun eine **Remotedesktopverbindung** mit dem virtuellen Computer erstellt. Öffnen Sie jetzt **PowerShell** in der Remotesitzung.
4.  Erstellen Sie mithilfe des PowerShell-Befehls `Invoke-WebRequest` eine Anforderung an den lokalen Endpunkt der verwalteten Identität, um ein Zugriffstoken für Azure SQL zu erhalten.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    Konvertieren Sie die Antwort aus einem JSON-Objekt in ein PowerShell-Objekt. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extrahieren Sie das Zugriffstoken aus der Antwort.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5. Öffnen Sie eine Verbindung mit dem SQL-Server. Denken Sie daran, die Werte für AZURE-SQL-SERVERNAME und DATABASE zu ersetzen.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Als Nächstes erstellen Sie eine Abfrage und senden diese an den Server. Denken Sie daran, den Wert für TABLE zu ersetzen.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Überprüfen Sie den Wert von `$DataSet.Tables[0]` zum Anzeigen der Ergebnisse der Abfrage.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene verwaltete Identität für den Zugriff auf Azure SQL Server verwenden. Weitere Informationen zu Azure SQL Server:

> [!div class="nextstepaction"]
> [Azure SQL-Datenbank-Dienst](/azure/sql-database/sql-database-technical-overview)
