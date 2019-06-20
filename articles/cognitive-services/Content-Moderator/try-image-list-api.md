---
title: Moderieren von Bildern mit benutzerdefinierten Listen und der API-Konsole – Content Moderator
titlesuffix: Azure Content Moderator
description: Verwenden Sie die Listenverwaltungs-API in Azure Content Moderator zum Erstellen von benutzerdefinierter Bildlisten.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7efa5114a903ba88010ec44f2f1038331df62948
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097979"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderieren von Bildern mit benutzerdefinierten Bildlisten in der API-Konsole

Verwenden Sie die [Listenverwaltungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) in Azure Content Moderator zum Erstellen von benutzerdefinierter Bildlisten. Verwenden Sie die benutzerdefinierten Bildlisten mit der Bildmoderations-API. Beim Vorgang der Bildmoderation wird Ihr Bild ausgewertet. Wenn Sie benutzerdefinierte Listen erstellen, vergleicht der Vorgang diese zudem mit den Bildern in Ihren benutzerdefinierten Listen. Mithilfe von benutzerdefinierten Listen können Sie das Bild blockieren oder zulassen.

> [!NOTE]
> Die Obergrenze liegt bei **fünf Bildlisten**, wobei jede Liste **max. 10.000 Bilder** enthalten kann.
>

Mit der Listenverwaltungs-API können Sie folgende Aufgaben durchführen:

- Erstellt eine Liste.
- Hinzufügen von Bildern zu einer Liste
- Abgleichen von Bildern mit Bildern in einer Liste
- Löschen von Bildern aus einer Liste
- Löschen einer Liste
- Bearbeiten von Listeninformationen
- Aktualisieren des Index, damit Änderungen an der Liste in einer neuen Überprüfung berücksichtigt werden

## <a name="use-the-api-console"></a>Verwenden der API-Konsole
Bevor Sie die API in der Onlinekonsole testen können, benötigen Sie Ihren Abonnementschlüssel. Dieser befindet sich auf der Registerkarte **Einstellungen** im Feld **Ocp-Apim-Subscription-Key**. Weitere Informationen finden Sie in der [Übersicht](overview.md).

## <a name="refresh-search-index"></a>Aktualisieren des Suchindex

Nachdem Sie eine Bildliste geändert haben, müssen Sie den zugehörigen Index aktualisieren, damit er bei zukünftigen Überprüfungen berücksichtigt wird. Dieser Schritt entspricht der Vorgehensweise einer Suchmaschine auf Ihrem Desktop (sofern aktiviert) oder einer Internetsuchmaschine, die kontinuierlich ihren Index zur Einbeziehung neuer Dateien oder Seiten aktualisiert.

1. Wählen Sie in der [API-Referenz zur Verwaltung von Bildlisten](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) im linken Menü **Bildlisten** und dann **Suchindex aktualisieren** aus.

   Die Seite **Bildlisten – Suchindex aktualisieren** wird geöffnet.

2. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt. 
 
    ![Regionsauswahl auf der Seite „Bildlisten – Suchindex aktualisieren“](images/test-drive-region.png)

    Die API-Konsole **Bildlisten – Suchindex aktualisieren** wird geöffnet.

3. Geben Sie im Feld **listId** die Listen-ID ein. Geben Sie Ihren Abonnementschlüssel ein, und wählen Sie dann **Senden** aus.

   ![Feld „Antwortinhalt“ in der Konsole „Bildlisten – Suchindex aktualisieren“](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Erstellen einer Bildliste

1. Navigieren Sie zur [API-Referenz zur Verwaltung von Bildlisten](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   Die Seite **Bildlisten – Erstellen** wird geöffnet. 

3. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt.

   ![Regionsauswahl auf der Seite „Bildlisten – Erstellen“](images/test-drive-region.png)

   Die API-Konsole **Bildlisten – Erstellen** wird geöffnet.
 
4. Geben Sie in das Feld **Ocp-Apim-Subscription-Key** Ihren Abonnementschlüssel ein.

5. Geben Sie in das Feld **Anforderungstext** Werte für **Name** (z.B. MyList) und **Description** ein.

   ![Name und Beschreibung des Anforderungstexts in der Konsole „Bildlisten – Erstellen“](images/try-terms-list-create-1.png)

6. Verwenden Sie Platzhalter für Schlüssel-Wert-Paare, um Ihrer Liste aussagekräftigere Metadaten zuzuweisen.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Fügen Sie Metadaten als Schlüssel-Wert-Paare und nicht als tatsächliche Bilder hinzu.
 
7. Wählen Sie **Senden** aus. Ihre Liste wird erstellt. Beachten Sie den **ID**-Wert, der der neuen Liste zugeordnet ist. Sie benötigen diese ID für andere Funktionen zur Verwaltung von Bildlisten.

   ![Feld „Antwortinhalt“ in der Konsole „Bildlisten – Erstellen“ mit Anzeige der Listen-ID](images/try-terms-list-create-2.png)
 
8. Fügen Sie als Nächstes Bilder zu MyList hinzu. Wählen Sie im Menü auf der linken Seite **Bild** und dann **Bild hinzufügen** aus.

   Die Seite **Bild – Bild hinzufügen** wird geöffnet. 

9. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt.

   ![Regionsauswahl auf der Seite „Bild – Bild hinzufügen“](images/test-drive-region.png)

   Die API-Konsole **Bild – Bild hinzufügen** wird geöffnet.
 
10. Geben Sie in das Feld **listId** die ID der von Ihnen generierten Liste und dann die URL des Bilds ein, die hinzugefügt werden soll. Geben Sie Ihren Abonnementschlüssel ein, und wählen Sie dann **Senden** aus.

11. Um sicherzustellen, dass das Bild der Liste hinzugefügt wurde, wählen Sie im Menü auf der linken Seite **Bild** und dann **Alle Bild-IDs abrufen** aus.

    Die API-Konsole **Bild – Alle Bild-IDs abrufen** wird geöffnet.
  
12. Geben Sie in das Feld **listId** die Listen-ID und dann Ihren Abonnementschlüssel ein. Wählen Sie **Senden** aus.

    ![Feld „Antwortinhalt“ in der Konsole „Bild – Alle Bild-IDs abrufen“ mit den von Ihnen eingegebenen Benennungen](images/try-image-list-create-11.png)
 
10. Fügen Sie noch ein paar Bilder hinzu. Nachdem Sie nun eine benutzerdefinierte Liste von Bildern erstellt haben, können Sie [Bilder mithilfe der Liste der benutzerdefinierten Bilder auswerten](try-image-api.md). 

## <a name="delete-images-and-lists"></a>Löschen von Bildern und Listen

Das Löschen eines Bilds oder einer Liste ist einfach. Mit der API können Sie folgende Aufgaben durchführen:

- Löschen eines Images. (**Bild – Löschen**)
- Löschen aller Bilder in einer Liste, jedoch nicht der Liste selbst (**Bild – Alle Bilder löschen**)
- Löschen einer Liste und aller zugehörigen Inhalte (**Bildlisten – Löschen**)

Dieses Beispiel löscht ein einzelnes Bild:

1. Wählen Sie in der [API-Referenz zur Verwaltung von Bildlisten](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) im linken Menü **Bild** und dann **Löschen** aus. 

   Die Seite **Bild – Löschen** wird geöffnet.

2. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt. 

   ![Regionsauswahl auf der Seite „Bild – Löschen“](images/test-drive-region.png)
 
   Die API-Konsole **Bild – Löschen** wird geöffnet.
 
3. Geben Sie in das Feld **listId** die ID der Liste ein, aus der ein Bild gelöscht werden soll.  Dies ist die Nummer, die in der Konsole **Bild – Alle Bild-IDs abrufen** für MyList-zurückgegeben wird. Geben Sie dann die **ImageId** des zu löschenden Bilds ein. 

In unserem Beispiel ist die Listen-ID **58953**. Dies ist der Wert für **ContentSource**. Die Bild-ID ist **59021**. Dies ist der Wert für **ContentIds**.

1. Geben Sie Ihren Abonnementschlüssel ein, und wählen Sie dann **Senden** aus.

1. Um sicherzustellen, dass das Bild gelöscht wurde, verwenden Sie die Konsole **Bild – Alle Bild-IDs abrufen**.
 
## <a name="change-list-information"></a>Ändern von Listeninformationen

Sie können Name und Beschreibung einer Liste bearbeiten und Metadatenelemente hinzufügen.

1. Wählen Sie in der [API-Referenz zur Verwaltung von Bildlisten](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) im linken Menü **Bildlisten** und dann **Details aktualisieren** aus. 

   Die Seite **Bildlisten – Details aktualisieren** wird geöffnet.

2. Wählen Sie für die **OpenAPI-Testkonsole** die Region aus, die Ihrem Standort am ehesten nahekommt.  

    ![Regionsauswahl auf der Seite „Bildlisten – Details aktualisieren“](images/test-drive-region.png)

    Die API-Konsole **Bildlisten – Details aktualisieren** wird geöffnet.
 
3. Geben Sie in das Feld **listId** die Listen-ID und dann Ihren Abonnementschlüssel ein.

4. Nehmen Sie im Feld **Anforderungstext** die jeweiligen Änderungen vor, und klicken Sie auf der Seite dann auf die Schaltfläche **Senden**.

   ![Änderungen am Anforderungstext in der Konsole „Bildlisten – Details aktualisieren“](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die REST-API in Ihrem Code, oder beginnen Sie mit dem [.NET-Schnellstart zu Bildlisten](image-lists-quickstart-dotnet.md), die in Ihre Anwendung integriert werden sollen.
