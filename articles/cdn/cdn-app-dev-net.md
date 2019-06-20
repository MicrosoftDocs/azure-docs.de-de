---
title: Erste Schritte mit der Azure-CDN-Bibliothek für .NET | Microsoft Docs
description: Erfahren Sie, wie Sie in Visual Studio .NET-Anwendungen zum Verwalten von Azure CDN schreiben.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 838c76e6a383b61ff465f3ed7506af34c8cd01d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60579921"
---
# <a name="get-started-with-azure-cdn-development"></a>Erste Schritte mit der Azure CDN-Entwicklung
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Sie können die [Azure CDN-Bibliothek für .NET](/dotnet/api/overview/azure/cdn) verwenden, um die Erstellung und Verwaltung von CDN-Profilen und -Endpunkten zu automatisieren.  Dieses Tutorial führt Sie durch die Erstellung einer einfachen .NET-Konsolenanwendung, die einige der verfügbaren Vorgänge demonstriert.  Dieses Tutorial dient nicht dazu, alle Aspekte der Azure-CDN-Bibliothek für .NET ausführlich zu beschreiben.

Für dieses Tutorial benötigen Sie Visual Studio 2015.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) steht kostenlos zum Download zur Verfügung.

> [!TIP]
> Das [vervollständigte Projekt aus diesem Tutorial](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) steht auf MSDN zum Download bereit.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Erstellen des Projekts und Hinzufügen von NuGet-Paketen
Nachdem Sie nun eine Ressourcengruppe für Ihre CDN-Profile erstellt und der Azure AD-Anwendung die Berechtigung zum Verwalten von CDN-Profilen und -Endpunkten innerhalb dieser Gruppe erteilt haben, können Sie damit beginnen, Ihre Anwendung zu erstellen.

Klicken Sie in Visual Studio 2015 auf **Datei**, **Neu**, **Projekt...** , um das Dialogfeld „Neues Projekt“ zu öffnen.  Erweitern Sie **Visual C#** , und wählen Sie im linken Bereich **Windows** aus.  Klicken Sie im mittleren Bereich auf **Konsolenanwendung** .  Benennen Sie Ihr Projekt, und klicken Sie auf **OK**.  

![Neues Projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Das Projekt soll einige Azure-Bibliotheken verwenden, die in NuGet-Paketen enthalten sind.  Fügen Sie diese nun zum Projekt hinzu.

1. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und danach auf **Paket-Manager-Konsole**.
   
    ![Verwalten von NuGet-Paketen](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. Führen Sie in der Paket-Manager-Konsole den folgenden Befehl aus, um die **Active Directory Authentication Library (ADAL)** zu installieren:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Führen Sie den folgenden Befehl aus, um die **Azure CDN-Verwaltungsbibliothek**zu installieren:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Anweisungen, Konstanten, main-Methode und Hilfsmethoden
Schreiben Sie nun die Grundstruktur des Programms.

1. Ersetzen Sie auf der Registerkarte „Program.cs“ die `using` -Direktiven im oberen Bereich durch Folgendes:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Sie müssen einige Konstanten definieren, die von den Methoden verwendet werden.  Fügen Sie in der `Program`-Klasse vor der `Main`-Methode Folgendes hinzu.  Ersetzen Sie die Platzhalter einschließlich der **&lt;spitzen Klammern&gt;** nach Bedarf durch Ihre eigenen Werte.
   
    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";
   
    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Definieren Sie auf Ebene der Klasse diese beiden Variablen.  Sie werden diese später verwenden, um zu ermitteln, ob Ihr Profil und Endpunkt bereits vorhanden sind.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Ersetzen Sie die `Main` -Methode wie folgt:
   
   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();
   
       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };
   
       ListProfilesAndEndpoints(cdn);
   
       // Create CDN Profile
       CreateCdnProfile(cdn);
   
       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);
   
       Console.WriteLine();
   
       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);
   
       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);
   
       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);
   
       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Einige Ihrer Methoden werden die Benutzer auffordern, Fragen mit Ja oder Nein zu beantworten.  Fügen Sie die folgende Methode hinzu, um diesen Vorgang etwas zu erleichtern:
   
    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Nachdem wir die Grundstruktur für unser Programm geschrieben haben, müssen wir nun die Methoden erstellen, die von der `Main` -Methode aufgerufen werden.

## <a name="authentication"></a>Authentication
Bevor Sie die Azure CDN Management Library verwenden können, müssen Sie den Dienstprinzipal authentifizieren und ein Authentifizierungstoken abrufen.  Diese Methode verwendet die ADAL, um das Token abzurufen.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Wenn Sie die individuelle Benutzerauthentifizierung verwenden, sieht die `GetAccessToken` -Methode etwas anders aus.

> [!IMPORTANT]
> Verwenden Sie dieses Codebeispiel nur, wenn Sie die individuelle Benutzerauthentifizierung anstelle eines Dienstprinzipals verwenden möchten.
> 
> 

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Ersetzen Sie `<redirect URI>` durch den Umleitungs-URI, den Sie bei der Registrierung der Anwendung in Azure AD eingegeben haben.

## <a name="list-cdn-profiles-and-endpoints"></a>Auflisten von CDN-Profilen und -Endpunkten
Jetzt können Sie CDN-Vorgänge ausführen.  Als Erstes listet die Methode alle Profile und Endpunkte in Ihrer Ressourcengruppe auf. Wenn eine Übereinstimmung für die in den Konstanten angegebenen Profil- und Endpunktnamen gefunden wird, vermerkt die Methode diese Namen für die spätere Verwendung, damit keine Duplikate erstellt werden.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Erstellen von CDN-Profilen und -Endpunkten
Als Nächstes erstellen Sie ein Profil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Danach erstellen Sie einen Endpunkt.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> Das obige Beispiel weist dem Endpunkt einen Ursprung namens *Contoso* mit dem Hostnamen `www.contoso.com` zu.  Ändern Sie dies in den Hostnamen Ihres Ursprungs.
> 
> 

## <a name="purge-an-endpoint"></a>Bereinigen eines Endpunkts
Eine häufige Aufgabe, die Sie auch in Ihr Programm aufnehmen sollten, ist das Löschen von Inhalten auf dem Endpunkt.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> Im obigen Beispiel gibt die Zeichenfolge `/*` an, dass ich sämtliche Daten aus dem Stammverzeichnis des Endpunktpfads löschen möchte.  Dies entspricht der Option **Alles bereinigen** im Bereinigungsdialogfeld des Azure-Portals. In der `CreateCdnProfile`-Methode habe ich das Profil durch Angabe des Codes `Sku = new Sku(SkuName.StandardVerizon)` als **Azure CDN from Verizon** erstellt. Der Vorgang wird also erfolgreich ausgeführt.  Bei Profilen vom Typ **Azure CDN from Akamai** wird **Alles löschen** hingegen nicht unterstützt. Wenn ich also in diesem Tutorial ein Akamai-Profil verwendet hätte, müsste ich bestimmte Pfade zum Löschen angeben.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Löschen von CDN-Profilen und -Endpunkten
Die letzten Methoden löschen den Endpunkt und das Profil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Ausführen des Programms
Jetzt können Sie das Programm kompilieren und ausführen, indem Sie in Visual Studio auf die Schaltfläche **Starten** klicken.

![Ausgeführtes Programm](./media/cdn-app-dev-net/cdn-program-running-1.png)

Wenn das Programm die obige Eingabeaufforderung erreicht, wechseln Sie zurück zu Ihrer Ressourcengruppe im Azure-Portal. Sie sollten feststellen, dass das Profil erstellt wurde.

![Erfolg!](./media/cdn-app-dev-net/cdn-success.png)

Bestätigen Sie die Aufforderung, um den Rest des Programms auszuführen.

![Abgeschlossenes Programm](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Nächste Schritte
[Laden Sie das Beispiel herunter](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c), um sich das abgeschlossene Projekt dieser exemplarischen Vorgehensweise anzusehen.

Weitere Informationen zur Azure CDN-Verwaltungsbibliothek für .NET finden Sie in der [Referenz auf MSDN](/dotnet/api/overview/azure/cdn).

Verwalten Sie Ihre CDN-Ressourcen mit [PowerShell](cdn-manage-powershell.md).

