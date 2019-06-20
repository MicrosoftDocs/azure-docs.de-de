---
title: Moderieren von Text mit benutzerdefinierten Begriffslisten – Content Moderator
titlesuffix: Azure Cognitive Services
description: Erstellen Sie mithilfe der Listenverwaltungs-API benutzerdefinierte Listen von Benennungen, die mit der API für die Textmoderation verwendet werden sollen.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 28029fe92a207dba85e2ab5a22c08879b7172925
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097945"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderieren von Text mit benutzerdefinierten Benennungslisten in der API-Konsole

Die globale Standardliste von Benennungen in Azure Content Moderator reicht für den Großteil der Anforderungen an die Inhaltsmoderation aus. Allerdings müssen Sie eventuell auf Benennungen prüfen, die spezifisch für Ihre Organisation sind. Beispielsweise sollten Sie Namen von Mitbewerbern zur weiteren Überprüfung kennzeichnen. 

Erstellen Sie mithilfe der [Listenverwaltungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) benutzerdefinierte Listen von Benennungen, die mit der API für die Textmoderation verwendet werden sollen. Mit dem Vorgang **Text – Überprüfen** wird Ihr Text auf unerwünschte Ausdrücke geprüft und mit benutzerdefinierten und freigegebenen Blacklists abgeglichen.

> [!NOTE]
> Die Obergrenze liegt bei **fünf Benennungslisten**, wobei jede Liste **max. 10.000 Benennungen** enthalten kann.
>

Mit der Listenverwaltungs-API können Sie folgende Aufgaben durchführen:
- Erstellt eine Liste.
- Hinzufügen von Benennungen zu einer Liste.
- Abgleichen von Benennungen mit den Benennungen in einer Liste.
- Löschen von Benennungen aus einer Liste.
- Löschen einer Liste.
- Bearbeiten von Listeninformationen
- Aktualisieren des Index, damit Änderungen an der Liste in einer neuen Überprüfung berücksichtigt werden

## <a name="use-the-api-console"></a>Verwenden der API-Konsole

Bevor Sie die API in der Onlinekonsole testen können, benötigen Sie Ihren Abonnementschlüssel. Dieser Schlüssel befindet sich auf der Registerkarte **Einstellungen** im Feld **Ocp-Apim-Subscription-Key**. Weitere Informationen finden Sie in der [Übersicht](overview.md).

## <a name="refresh-search-index"></a>Aktualisieren des Suchindex

Nachdem Sie eine Benennungsliste geändert haben, müssen Sie den zugehörigen Index aktualisieren, damit er bei zukünftigen Überprüfungen berücksichtigt wird. Dieser Schritt entspricht der Vorgehensweise einer Suchmaschine auf Ihrem Desktop (sofern aktiviert) oder einer Internetsuchmaschine, die kontinuierlich ihren Index zur Einbeziehung neuer Dateien oder Seiten aktualisiert.

1. Wählen Sie in der [API-Referenz zur Verwaltung von Benennungslisten](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) im Menü auf der linken Seite **Benennungslisten** und dann **Suchindex aktualisieren** aus. 

   Die Seite **Benennungslisten – Suchindex aktualisieren** wird geöffnet.

2. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt. 

   ![Regionsauswahl auf der Seite „Benennungslisten – Suchindex aktualisieren“](images/test-drive-region.png)

   Die API-Konsole **Benennungslisten – Suchindex aktualisieren** wird geöffnet.

3. Geben Sie im Feld **listId** die Listen-ID ein. Geben Sie Ihren Abonnementschlüssel ein, und wählen Sie dann **Senden** aus.

   ![Feld „Antwortinhalt“ in der Konsole „Benennungslisten-API – Suchindex aktualisieren“](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Erstellen einer Benennungsliste
1. Navigieren Sie zur [API-Referenz zur Verwaltung von Benennungslisten](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   Die Seite **Benennungslisten – Erstellen** wird geöffnet.

2. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt. 

   ![Regionsauswahl auf der Seite „Benennungslisten – Erstellen“](images/test-drive-region.png)

   Die API-Konsole **Benennungslisten – Erstellen** wird geöffnet.
 
3. Geben Sie in das Feld **Ocp-Apim-Subscription-Key** Ihren Abonnementschlüssel ein.

4. Geben Sie in das Feld **Anforderungstext** Werte für **Name** (z.B. MyList) und **Description** ein.

   ![Name und Beschreibung des Anforderungstexts in der Konsole „Benennungslisten – Erstellen“](images/try-terms-list-create-1.png)

5. Verwenden Sie Platzhalter für Schlüssel-Wert-Paare, um Ihrer Liste aussagekräftigere Metadaten zuzuweisen.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Fügen Sie Metadaten als Schlüssel-Wert-Paare und nicht als tatsächliche Benennungen zur Liste hinzu.
 
6. Wählen Sie **Senden** aus. Ihre Liste wird erstellt. Beachten Sie den **ID**-Wert, der der neuen Liste zugeordnet ist. Sie benötigen diese ID für andere Funktionen zur Verwaltung von Benennungslisten.

   ![Feld „Antwortinhalt“ in der Konsole „Benennungslisten – Erstellen“ mit Anzeige der Listen-ID](images/try-terms-list-create-2.png)
 
7. Fügen Sie Benennungen zu MyList hinzu. Klicken Sie im Menü auf der linken Seite unter **Benennung** auf **Benennung hinzufügen**. 

   Die Seite **Benennung – Benennung hinzufügen** wird geöffnet. 

8. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt. 

   ![Regionsauswahl auf der Seite „Benennung – Benennung hinzufügen“](images/test-drive-region.png)

   Die API-Konsole **Benennung – Benennung hinzufügen** wird geöffnet.
 
9. Geben Sie in das Feld **listId** die Listen-ID ein, die Sie generiert haben, und wählen Sie einen Wert für **language** aus. Geben Sie Ihren Abonnementschlüssel ein, und wählen Sie dann **Senden** aus.

   ![Abfrageparameter in der Konsole „Benennung – Benennung hinzufügen“](images/try-terms-list-create-3.png)
 
10. Um sicherzustellen, dass die Benennung der Liste hinzugefügt wurde, wählen Sie im Menü auf der linken Seite **Benennung** und dann **Alle Benennungen hinzufügen** aus. 

    Die API-Konsole **Benennung – Alle Benennungen abrufen** wird geöffnet.

11. Geben Sie in das Feld **listId** die Listen-ID und dann Ihren Abonnementschlüssel ein. Wählen Sie **Senden** aus.

12. Überprüfen sie im Feld **Antwortinhalt** die Benennungen, die Sie eingegeben haben.

    ![Feld „Antwortinhalt“ in der Konsole „Benennung – Alle Benennungen abrufen“ mit den von Ihnen eingegebenen Benennungen](images/try-terms-list-create-4.png)
 
13. Fügen Sie noch ein paar Benennungen hinzu. Nachdem Sie nun eine benutzerdefinierte Liste von Benennungen erstellt haben, [überprüfen Sie testweise einen Text](try-text-api.md) mit der benutzerdefinierten Benennungsliste. 

## <a name="delete-terms-and-lists"></a>Löschen von Benennungen und Listen

Das Löschen einer Benennung oder einer Liste ist einfach. Mit der API können Sie folgende Aufgaben durchführen:

- Löschen einer Benennung (**Benennung – Löschen**)
- Löschen aller Benennungen in einer Liste, jedoch nicht der Liste selbst (**Benennung – Alle Benennungen löschen**)
- Löschen einer Liste und aller zugehörigen Inhalte (**Benennungsliste – Löschen**)

Dieses Beispiel löscht eine einzelne Benennung.

1. Wählen Sie in der [API-Referenz zur Verwaltung von Benennungslisten](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) im linken Menü **Benennung** und dann **Löschen** aus. 

   Die Seite **Benennung – Löschen** wird geöffnet.

2. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt. 

   ![Regionsauswahl auf der Seite „Benennung – Löschen“](images/test-drive-region.png)

   Die API-Konsole **Benennung – Löschen** wird geöffnet.
  
3. Geben Sie in das Feld **listId** die ID der Liste ein, aus der eine Benennung gelöscht werden soll. Diese ID ist die Zahl (in unserem Beispiel **122**), die in der MyList-Konsole **Benennungslisten – Details abrufen** zurückgegeben wird. Geben Sie die Benennung ein, und wählen Sie eine Sprache aus.
 
   ![Abfrageparameter in der Konsole „Benennung – Löschen“](images/try-terms-list-delete-1.png)

4. Geben Sie Ihren Abonnementschlüssel ein, und wählen Sie dann **Senden** aus.

5. Um sicherzustellen, dass die Benennung gelöscht wurde, verwenden Sie die Konsole **Benennungslisten – Alle abrufen**.

   ![Feld „Antwortinhalt“ in der Konsole „Benennungslisten – Alle abrufen“ mit gelöschter Benennung](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Ändern von Listeninformationen

Sie können Name und Beschreibung einer Liste bearbeiten und Metadatenelemente hinzufügen.

1. Wählen Sie in der [API-Referenz zur Verwaltung von Benennungslisten](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) im linken Menü **Benennungslisten** und dann **Details aktualisieren** aus. 

   Die Seite **Benennungslisten – Details aktualisieren** wird geöffnet.

2. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt. 

   ![Regionsauswahl auf der Seite „Benennungslisten – Details aktualisieren“](images/test-drive-region.png)

   Die API-Konsole **Benennungslisten – Details aktualisieren** wird geöffnet.

3. Geben Sie in das Feld **listId** die Listen-ID und dann Ihren Abonnementschlüssel ein.

4. Nehmen Sie im Feld **Anforderungstext** die jeweiligen Änderungen vor, und klicken Sie dann auf **Senden**.

   ![Änderungen am Anforderungstext in der Konsole „Benennungslisten – Details aktualisieren“](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die REST-API in Ihrem Code, oder beginnen Sie mit dem [.NET-Schnellstart zu Benennungslisten](term-lists-quickstart-dotnet.md), die in Ihre Anwendung integriert werden sollen.
