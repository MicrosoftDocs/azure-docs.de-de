---
title: Überprüfen von Text anhand einer benutzerdefinierten Begriffsliste in C# – Content Moderator
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Text mit benutzerdefinierten Begriffslisten im Content Moderator SDK für C# moderieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 93d90232fb530a6c14c40558fc6a9974a1da42de
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900915"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>Überprüfen von Text anhand einer benutzerdefinierten Begriffsliste in C#

Die globale Standardliste von Benennungen in Azure Content Moderator reicht für den Großteil der Anforderungen an die Inhaltsmoderation aus. Allerdings müssen Sie eventuell auf Benennungen prüfen, die spezifisch für Ihre Organisation sind. Beispielsweise sollten Sie Namen von Mitbewerbern zur weiteren Überprüfung kennzeichnen. 

Sie können mit dem [Content Moderator SDK für .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) benutzerdefinierte Listen von Benennungen erstellen, die mit der API für die Textmoderation verwendet werden sollen.

Dieser Artikel enthält Informationen und Codebeispiele, die Ihnen den Einstieg in die Verwendung des Content Moderator SDK für .NET erleichtern, um Folgendes durchzuführen:
- Erstellt eine Liste.
- Hinzufügen von Benennungen zu einer Liste.
- Abgleichen von Benennungen mit den Benennungen in einer Liste.
- Löschen von Benennungen aus einer Liste.
- Löschen einer Liste
- Bearbeiten von Listeninformationen
- Aktualisieren des Index, damit Änderungen an der Liste in einer neuen Überprüfung berücksichtigt werden

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/), bevor Sie beginnen. 

## <a name="sign-up-for-content-moderator-services"></a>Registrieren für Content Moderator-Dienste

Um Content Moderator-Dienste über die REST-API oder über das SDK verwenden zu können, benötigen Sie einen Abonnementschlüssel. Abonnieren Sie den Content Moderator-Dienst über das [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator), um einen solchen Schlüssel zu erhalten.

## <a name="create-your-visual-studio-project"></a>Erstellen Ihres Visual Studio-Projekts

1. Fügen Sie Ihrer Projektmappe ein neues Projekt vom Typ **Konsolen-App (.NET Framework)** hinzu.

1. Geben Sie dem Projekt den Namen **TermLists** . Wählen Sie dieses Projekt als einzelnes Startprojekt für die Projektmappe aus.

### <a name="install-required-packages"></a>Installieren erforderlicher Pakete

Installieren Sie die folgenden NuGet-Pakete für das TermLists-Projekt:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualisieren der using-Anweisungen des Programms

Fügen Sie die folgenden `using`-Anweisungen hinzu:

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Erstellen des Content Moderator-Clients

Fügen Sie den folgenden Code hinzu, um einen Content Moderator-Client für Ihr Abonnement zu erstellen. Aktualisieren Sie die Felder `AzureEndpoint` und `CMSubscriptionKey` mit den Werten Ihrer Endpunkt-URL und Ihres Abonnementschlüssels. Diese finden Sie auf der Registerkarte **Schnellstart** Ihrer Ressource im Azure-Portal.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="add-private-properties"></a>Hinzufügen von privaten Eigenschaften

Fügen Sie die folgenden privaten Eigenschaften dem TermLists-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Erstellen einer Benennungsliste

Sie erstellen eine Begriffsliste mit **ContentModeratorClient.ListManagementTermLists.Create** . Der erste Parameter für **Create** ist eine Zeichenfolge, die einen MIME-Typ enthält. Dieser sollte „application/json“ lauten. Weitere Informationen finden Sie in der [API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). Der zweite Parameter ist ein **Body** -Objekt, das einen Namen und eine Beschreibung für die neue Begriffsliste enthält.

> [!NOTE]
> Die Obergrenze liegt bei **fünf Benennungslisten** , wobei jede Liste **max. 10.000 Benennungen** enthalten kann.

Fügen Sie die folgende Methodendefinition dem TermLists-Namespace (Program-Klasse) hinzu.

> [!NOTE]
> Die RPS-Rate (Requests Per Second, Anforderungen pro Sekunde) Ihres Content Moderator-Dienstschlüssels ist begrenzt. Bei Überschreitung des Grenzwerts löst das SDK eine Ausnahme mit dem Fehlercode 429 aus. Bei einem Schlüssel des Free-Tarifs ist die Anforderungsrate auf eine einzelne Anforderung pro Sekunde beschränkt.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>Aktualisieren von Name und Beschreibung der Begriffsliste

Sie aktualisieren die Begriffslisteninformationen mit **ContentModeratorClient.ListManagementTermLists.Update** . Der erste Parameter für **Update** ist die Begriffslisten-ID. Der zweite Parameter ist ein MIME-Typ. Dieser sollte „application/json“ lauten. Weitere Informationen finden Sie in der [API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). Der dritte Parameter ist ein **Body** -Objekt, das den neuen Namen und die Beschreibung enthält.

Fügen Sie die folgende Methodendefinition dem TermLists-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>Hinzufügen eines Begriffs zu einer Begriffsliste

Fügen Sie die folgende Methodendefinition dem TermLists-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>Abrufen aller Begriffe in einer Begriffsliste

Fügen Sie die folgende Methodendefinition dem TermLists-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>Hinzufügen von Code zum Aktualisieren des Suchindexes

Nachdem Sie eine Begriffsliste geändert haben, aktualisieren Sie ihren Suchindex, damit die Änderungen verwendet werden, wenn Sie das nächste Mal mit der Begriffsliste einen Text überprüfen. Dies entspricht der Vorgehensweise einer Suchmaschine auf Ihrem Desktop (sofern aktiviert) oder einer Internetsuchmaschine, die kontinuierlich ihren Index zur Einbeziehung neuer Dateien oder Seiten aktualisiert.

Sie aktualisieren einen Begriffslisten-Suchindex mit **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod** .

Fügen Sie die folgende Methodendefinition dem TermLists-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>Überprüfen von Bildschirmtext mithilfe einer Begriffsliste

Sie überprüfen Text mithilfe einer Begriffsliste mit **ContentModeratorClient.TextModeration.ScreenText** , die folgende Parameter akzeptiert.

- Die Sprache der Begriffe in der Begriffsliste.
- Ein MIME-Typ wie „text/html“, „text/xml“, „text/markdown“ oder „text/plain“.
- Der zu überprüfende Text.
- Ein boolescher Wert. Legen Sie für dieses Feld **true** zur Autokorrektur der Texte vor der Überprüfung fest.
- Ein boolescher Wert. Legen Sie dieses Feld auf **true** fest, um persönliche Daten im Text zu erkennen.
- Die Begriffslisten-ID.

Weitere Informationen finden Sie in der [API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** gibt ein **Screen** -Objekt zurück, das über eine **Terms** -Eigenschaft verfügt, die alle Begriffe auflistet, die Content Moderator in der Prüfung erkannt hat. Beachten Sie: Wenn Content Moderator während der Prüfung keine Begriffe erkannt hat, hat die **Terms** -Eigenschaft den Wert **NULL** .

Fügen Sie die folgende Methodendefinition dem TermLists-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>Löschen von Begriffen und Listen

Das Löschen eines Begriffs oder einer Liste ist einfach. Mit dem SDK können Sie folgende Aufgaben durchführen:

- Löschen eines Begriffs. ( **ContentModeratorClient.ListManagementTerm.DeleteTerm** )
- Löschen aller Begriffe in einer Liste, jedoch nicht der Liste selbst. ( **ContentModeratorClient.ListManagementTerm.DeleteAllTerms** )
- Löschen einer Liste und aller zugehörigen Inhalte ( **ContentModeratorClient.ListManagementTerm.DeleteAllTerms** )

### <a name="delete-a-term"></a>Löschen eines Begriffs

Fügen Sie die folgende Methodendefinition dem TermLists-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>Löschen aller Begriffe in einer Begriffsliste

Fügen Sie die folgende Methodendefinition dem TermLists-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>Löschen einer Begriffsliste

Fügen Sie die folgende Methodendefinition dem TermLists-Namespace (Program-Klasse) hinzu.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>Erstellen der Methode „Main“

Fügen Sie die **Main** -Methodendefinition dem Namespace **TermLists** (Klasse **Program** ) hinzu. Schließen Sie zum Schluss die Klasse **Program** und den Namespace **TermLists** .

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>Führen Sie die Anwendung aus, um die Ausgabe zu sehen.

Die Konsolenausgabe sieht dann wie folgt aus:

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>Nächste Schritte

Rufen Sie das [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) und die [Visual Studio-Projektmappe](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dafür sowie andere Content Moderator-Schnellstarts für .NET ab, und beginnen Sie mit der Integration.
