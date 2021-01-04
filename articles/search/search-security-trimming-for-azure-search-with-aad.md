---
title: Sicherheitsfilter zum Einschränken von Ergebnissen mit Active Directory
titleSuffix: Azure Cognitive Search
description: Hier finden Sie Informationen zu Sicherheitsberechtigungen auf Dokumentebene für Azure Cognitive Search-Suchergebnisse sowie zur Verwendung von Sicherheitsfiltern und AAD-Identitäten (Azure Active Directory).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/04/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 87337cf22bdb388c5873a2811bb9913c3e7f4d4e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994960"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Sicherheitsfilter zum Einschränken von Ergebnissen der kognitiven Azure-Suche mit Active Directory-Identitäten

Dieser Artikel zeigt, wie Sie mithilfe von Azure Active Directory-Sicherheitsidentitäten (AAD) und Filtern in der kognitiven Azure-Suche Suchergebnisse basierend auf der Benutzergruppenmitgliedschaft einschränken.

In diesem Artikel werden die folgenden Aufgaben behandelt:
> [!div class="checklist"]
> - Erstellen von AAD-Gruppen und -Benutzern
> - Zuordnen des Benutzers zur erstellten Gruppe
> - Zwischenspeichern der neuen Gruppen
> - Indizieren von Dokumenten mit zugeordneten Gruppen
> - Ausgeben einer Suchanfrage mit Gruppenbezeichnerfilter
> 
> [!NOTE]
> Beispielcodeausschnitte in diesem Artikel sind in C# geschrieben. Den vollständigen Quellcode finden Sie [auf GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Voraussetzungen

Ihr Index in der kognitiven Azure-Suche muss ein [Sicherheitsfeld](search-security-trimming-for-azure-search.md) zum Speichern der Liste mit Gruppenidentitäten enthalten, die über Lesezugriff auf das Dokument verfügen. Dieser Anwendungsfall geht von einer 1:1-Zuordnung zwischen einem sicherungsfähigen Objekt (z.B. der Hochschulbewerbung einer Einzelperson) und einem Sicherheitsfeld aus, in dem festgelegt ist, wer Zugriff auf dieses Objekt hat (Zulassungspersonal).

Sie müssen über AAD-Administratorberechtigungen verfügen, die in dieser exemplarischen Vorgehensweise zum Erstellen von Benutzern, Gruppen und Zuordnungen in AAD benötigt werden.

Ihre Anwendung muss außerdem bei AAD registriert werden, wie im folgenden Verfahren beschrieben.

### <a name="register-your-application-with-aad"></a>Registrieren Ihrer Anwendung bei AAD

In diesem Schritt wird Ihre Anwendung in AAD integriert, um Anmeldungen von Benutzer- und Gruppenkonten zu akzeptieren. Wenn Sie nicht als AAD-Administrator in Ihrer Organisation fungieren, müssen Sie möglicherweise einen [neuen Mandanten erstellen](../active-directory/develop/quickstart-create-new-tenant.md), um die folgenden Schritte auszuführen.

1. Navigieren Sie zu [**App-Registrierungsportal**](https://apps.dev.microsoft.com) >  **Konvergente App** > **App hinzufügen**.
2. Geben Sie einen Namen für Ihre Anwendung ein, und klicken Sie auf **Erstellen**. 
3. Wählen Sie auf der Seite „Meine Anwendungen“ Ihre neu registrierte Anwendung aus.
4. Klicken Sie auf der Seite für die Anwendungsregistrierung auf **Plattformen** > **Plattform hinzufügen**, und wählen Sie **Web-API** aus.
5. Wechseln Sie weiterhin von der Anwendungsregistrierungsseite aus zu **Microsoft Graph-Berechtigungen** > **Hinzufügen**.
6. Fügen Sie unter „Berechtigungen auswählen“ die folgenden delegierten Berechtigungen hinzu, und klicken Sie auf **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph stellt eine API bereit, die über eine REST-API programmgesteuerten Zugriff auf AAD bietet. Das Codebeispiel für diese exemplarische Vorgehensweise verwendet die Berechtigungen zum Aufrufen der Microsoft Graph-API, um Gruppen, Benutzer und Zuordnungen zu erstellen. Die APIs werden außerdem zum Zwischenspeichern von Gruppenbezeichnern für eine schnellere Filterung verwendet.

## <a name="create-users-and-groups"></a>Erstellen von Benutzern und Gruppen

Wenn Sie einer vorhandenen Anwendung eine Suchfunktion hinzufügen, liegen in AAD möglicherweise bereits Benutzer- und Gruppenbezeichner vor. In diesem Fall können Sie die nächsten drei Schritte überspringen. 

Wenn Sie jedoch über keine vorhandenen Benutzer verfügen, können Sie die Sicherheitsprinzipale mithilfe der Microsoft Graph-APIs erstellen. Die folgenden Codeausschnitte veranschaulichen, wie Sie Bezeichner generieren, die zu Datenwerten für das Sicherheitsfeld in Ihrem Index für die kognitive Azure-Suche werden. In unserer hypothetischen Anwendung für die Hochschulzulassung entspräche dies den Sicherheitsbezeichnern für das Zulassungspersonal.

Die Benutzer- und Gruppenmitgliedschaft kann sich – besonders in großen Organisationen – häufig ändern. Code zum Erstellen von Benutzer- und Gruppenidentitäten muss häufig genug ausgeführt werden, um Änderungen in der Organisationsmitgliedschaft zu erfassen. Ebenso erfordert Ihr Index für die kognitive Azure-Suche einen ähnlichen Aktualisierungsplan, um den aktuellen Status der zugelassenen Benutzer und Ressourcen widerzuspiegeln.

### <a name="step-1-create-aad-group"></a>Schritt 1: Erstellen der [AAD-Gruppe](/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-user"></a>Schritt 2: Erstellen des [AAD-Benutzers](/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Schritt 3: Zuordnen von Benutzer und Gruppe
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Schritt 4: Zwischenspeichern der Gruppenbezeichner
Um die Netzwerklatenz zu verringern, können Sie optional die Benutzer-Gruppen-Zuordnungen zwischenspeichern, sodass bei einer Suchanfrage Gruppen aus dem Cache zurückgegeben werden und ein Roundtrip zu AAD entfällt. Sie können mit der [AAD-Batch-API](/graph/json-batching) eine einzelne HTTP-Anforderung mit mehreren Benutzern senden und den Cache erstellen.

Microsoft Graph ist für die Verarbeitung einer großen Anzahl von Anforderungen konzipiert. Wenn die Anzahl von Anforderungen zu hoch ist, gibt Microsoft Graph einen Fehler mit dem HTTP-Statuscode 429 aus. Weitere Informationen finden Sie unter [Microsoft Graph-Drosselung](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indizieren von Dokumenten mit den zugelassenen Gruppen

Abfragevorgänge in der kognitiven Azure-Suche werden über einen Index für die kognitive Azure-Suche ausgeführt. In diesem Schritt importiert ein Indizierungsvorgang durchsuchbare Daten in einen Index, die als Sicherheitsfilter verwendeten Bezeichner eingeschlossen. 

Die kognitive Azure-Suche führt keine Authentifizierung von Benutzeridentitäten durch und stellt keine Logik zur Verfügung, um festzustellen, welche Inhalte ein Benutzer anzeigen darf. Im Anwendungsfall für die Sicherheitskürzung wird davon ausgegangen, dass Sie für die Zuordnung zwischen einem sensiblen Dokument und dem Gruppenbezeichner sorgen, der Zugriff auf dieses Dokument hat, und dass diese Informationen vollständig in einen Suchindex importiert wurden. 

Im hypothetischen Beispiel würde der Textkörper der PUT-Anforderung in einem Index für die kognitive Azure-Suche den Aufsatz oder die Aufzeichnung eines Bewerbers für eine Hochschule sowie den Gruppenbezeichner enthalten, der die Berechtigung zur Anzeige dieses Inhalts erteilt. 

Im generischen Beispiel, das im Codebeispiel für diese exemplarische Vorgehensweise verwendet wird, könnte die Indexaktion wie folgt aussehen:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Ausgeben einer Suchanfrage

Zum Zweck der Sicherheitskürzung sind die Werte im Sicherheitsfeld des Index statische Werte, die zum Ein- oder Ausschließen von Dokumenten in Suchergebnissen verwendet werden. Wenn der Gruppenbezeichner für Zulassungen beispielsweise „A11B22C33D44-E55F66G77-H88I99JKK“ lautet, werden alle Dokumente in einem Index für die kognitive Azure-Suche, die diesen Bezeichner im Sicherheitsfeld enthalten, in die an den Anforderer zurückgesendeten Suchergebnisse eingeschlossen (oder aus diesen ausgeschlossen).

Um die in Suchergebnissen zurückgegebenen Dokumente basierend nach Gruppen von Benutzern zu filtern, die die Anforderung ausgeben, führen Sie die folgenden Schritte aus.

### <a name="step-1-retrieve-users-group-identifiers"></a>Schritt 1: Abrufen der Gruppenbezeichner des Benutzers

Wenn die Gruppen des Benutzers nicht bereits zwischengespeichert wurden oder der Cache abgelaufen ist, geben Sie die [groups](/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)-Anforderung aus.
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Schritt 2: Erstellen der Suchanfrage

Vorausgesetzt, dass Sie über die Gruppenmitgliedschaft des Benutzers verfügen, können Sie die Suchanfrage mit den geeigneten Filterwerten ausgeben.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Schritt 3: Verarbeiten der Ergebnisse

Die Antwort umfasst eine gefilterte Liste mit Dokumenten, für die der Benutzer über Anzeigeberechtigungen verfügt. Je nachdem, wie Sie die Seite mit den Suchergebnissen aufbauen, können Sie visuelle Hinweise zum gefilterten Resultset einschließen.

## <a name="conclusion"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise haben Sie Techniken zur Verwendung von AAD-Anmeldungen zum Filtern von Dokumenten in Ergebnissen der kognitiven Azure-Suche kennengelernt, um Dokumente aus den Ergebnissen auszuschließen, die nicht mit den Filtern der Anforderung übereinstimmen.

## <a name="see-also"></a>Weitere Informationen

+ [Identitätsbasierte Zugriffssteuerung mit Filtern der kognitiven Azure-Suche](search-security-trimming-for-azure-search.md)
+ [Filter in Azure Cognitive Search](search-filters.md)
+ [Datensicherheit und Zugriffssteuerung in Vorgängen der kognitiven Azure-Suche](search-security-overview.md)