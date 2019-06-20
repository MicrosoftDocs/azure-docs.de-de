---
title: Sicherheitsfilter zum Einschränken von Ergebnissen – Azure Search
description: Zugriffssteuerung für Azure Search-Inhalte mithilfe von Sicherheitsfiltern und Benutzeridentitäten
ms.service: search
ms.topic: conceptual
services: search
ms.date: 05/02/2019
author: brjohnstmsft
ms.author: brjohnst
manager: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: a222b9e506988929c25a560361611b8f78142053
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024369"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Sicherheitsfilter zum Einschränken von Ergebnissen in Azure Search

Sie können Sicherheitsfilter anwenden, um Suchergebnisse in Azure Search basierend auf der Benutzeridentität einzuschränken. Diese Suchfunktion erfordert im Allgemeinen das Vergleichen der Identität derjenigen, die die Suche anfordern, mit einem Feld, das die Prinzipien enthält, wer Berechtigungen für das Dokument besitzt. Wenn eine Übereinstimmung gefunden wird, erhält der Benutzer oder Prinzipal (z.B. eine Gruppe oder Rolle) Zugriff auf dieses Dokument.

Eine Möglichkeit, um das Filtern der Sicherheit zu erreichen, erfolgt über eine komplizierte Disjunktion von Gleichheitsausdrücken, z.B. `Id eq 'id1' or Id eq 'id2'` usw. Dieser Ansatz ist fehleranfällig, schwierig zu verwalten und verlangsamt die Antwortzeit von Anfragen um mehrere Sekunden, wenn die Liste Hunderte oder Tausende von Werten enthält. 

Ein einfacherer und schnellerer Ansatz erfolgt über die `search.in`-Funktion. Wenn Sie `search.in(Id, 'id1, id2, ...')` statt einem Gleichheitsausdruck verwenden, können Sie Antwortzeiten von unter einer Sekunde erwarten.

Dieser Artikel erläutert, wie Sie das Filtern von Sicherheit mithilfe der folgenden Schritte erreichen:
> [!div class="checklist"]
> * Erstellen Sie ein Feld, das die Prinzipalbezeichner enthält 
> * Aktualisieren Sie vorhandene Dokumente mit den relevanten Prinzipalbezeichnern, oder übertragen Sie diese mithilfe von Push
> * Geben Sie eine Suchanforderung mit `search.in` `filter` aus

>[!NOTE]
> Das Abrufen der Prinzipalbezeichner wird in diesem Dokument nicht behandelt. Sie sollten diese vom Anbieter Ihres Identitätsdiensts erhalten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein [Azure-Abonnement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), einen [Azure Search-Dienst](https://docs.microsoft.com/azure/search/search-create-service-portal) und einen [Azure Search-Index](https://docs.microsoft.com/azure/search/search-create-index-portal) verfügen.  

## <a name="create-security-field"></a>Erstellen des Sicherheitsfelds

Ihre Dokumente müssen ein Feld enthalten, in dem angegeben wird, welche Gruppen Zugriff erhalten. Diese Information stellt das Filterkriterium dar, nach dem Dokumente aus dem Resultset ausgewählt oder abgelehnt werden, das dem Aussteller zurückgegeben wird.
Angenommen, es gibt einen Index von gesicherten Dateien, und auf jede Datei kann von verschiedenen Benutzern zugegriffen werden.
1. Fügen Sie das Feld `group_ids` (Sie können einen beliebigen Namen wählen) als `Collection(Edm.String)` hinzu. Stellen Sie sicher, dass das `filterable`-Attribut des Felds auf `true` festgelegt ist, sodass Suchergebnisse basierend auf dem Zugriff gefiltert werden, den der Benutzer besitzt. Wenn Sie das `group_ids`-Feld für das Dokument mit dem `file_name` „secured_file_b“ beispielsweise auf `["group_id1, group_id2"]` festlegen, haben nur die Benutzer Lesezugriff auf die Datei, die zu den Gruppen-IDs „group_id1“ oder „group_id2“ gehören.
   Stellen Sie sicher, dass das `retrievable`-Attribut des Felds auf `false` festgelegt ist, sodass dieses nicht als Teil der Suchanforderung zurückgegeben wird.
2. Fügen Sie für dieses Beispiel ebenfalls die Felder `file_id` und `file_name` hinzu.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Verwenden der REST-API, um Daten mithilfe von Push in Ihren Index zu übertragen
  
Geben Sie eine HTTP POST-Anforderung an den URL-Endpunkt Ihres Indexes aus. Der Hauptteil der HTTP-Anforderung ist ein JSON-Objekt, das die Dokumente enthält, die hinzugefügt werden sollen:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

Geben Sie im Anforderungstext den Inhalt Ihrer Dokumente an:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Wenn Sie ein vorhandenes Dokument mit der Liste der Gruppen aktualisieren müssen, können Sie die `merge`- oder `mergeOrUpload`-Aktion verwenden:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Vollständige Details zum Hinzufügen oder Aktualisieren von Dokumenten finden Sie unter [Dokumente bearbeiten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Anwenden der Sicherheitsfilter

Für das Kürzen von Dokumenten basierend auf dem `group_ids`-Zugriff sollten Sie eine Suchabfrage mit einem `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))`-Filter eingeben, bei der „group_id1, group_id2,...“ die Gruppen sind, zu denen der Aussteller der Suchanforderung gehört.
Dieser Filter vergleicht alle Dokumente, für die das `group_ids`-Feld einen der angegebenen Bezeichner enthält.
Vollständige Details zum Durchsuchen von Dokumenten mithilfe von Azure Search finden Sie unter [Durchsuchen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Beachten Sie, dass dieses Beispiel veranschaulicht, wie Sie Dokumente mithilfe einer POST-Anforderung durchsuchen.

Geben Sie die HTTP POST-Anforderung aus:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Geben Sie den Filter im Anforderungstext an:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Es sollten die Dokumente zurückgegeben werden, bei denen `group_ids` entweder „group_id1“ oder „group_id2“ enthält. Das heißt, Sie erhalten die Dokumente, für die der Anforderungsaussteller über Lesezugriff verfügt.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Zusammenfassung

Auf diese Weise können Sie Ergebnisse basierend auf der Benutzeridentität und der `search.in()`-Funktion von Azure Search filtern. Sie können mithilfe dieser Funktion Prinzipalbezeichner für den anfordernden Benutzer übergeben, um sie mit den Prinzipalbezeichnern zu vergleichen, die dem entsprechenden Zieldokument zugeordnet sind. Wenn eine Suchanforderung verarbeitet wird, filtert die `search.in`-Funktion die Suchergebnisse heraus, für die keiner der Prinzipale des Benutzers über Lesezugriff verfügt. Der Prinzipalbezeichner kann beispielsweise Sicherheitsgruppen, Rollen oder sogar die Identität des Benutzers darstellen.
 
## <a name="see-also"></a>Weitere Informationen

+ [Auf der Active Directory-Identität basierende Zugriffssteuerung mit Azure Search-Filtern](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter in Azure Search](search-filters.md)
+ [Datensicherheit und Zugriffssteuerung in Azure Search-Vorgängen](search-security-overview.md)