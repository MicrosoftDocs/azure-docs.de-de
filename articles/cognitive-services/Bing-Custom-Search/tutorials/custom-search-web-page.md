---
title: 'Tutorial: Erstellen einer benutzerdefinierten Suchwebseite – benutzerdefinierte Bing-Suche'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial wird beschrieben, wie eine Instanz der benutzerdefinierten Bing-Suche konfiguriert und in eine Webseite integriert wird.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: a789cb3fde05d12a8793196043f1c246bbab6559
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342412"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Tutorial: Erstellen einer benutzerdefinierten Suchwebseite

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die benutzerdefinierte Bing-Suche ermöglicht das Erstellen einer maßgeschneiderten Suchbenutzeroberfläche für Themen, die Sie interessieren. Wenn Sie beispielsweise Besitzer einer Kampfsportwebsite sind, die eine Suchbenutzeroberfläche bietet, können Sie die Domänen, Unterwebsites und Webseiten angeben, die Bing durchsucht. Ihre Benutzer sehen Suchergebnisse, die auf den Inhalt zugeschnitten sind, für den sie sich interessieren, anstatt durch allgemeine Suchergebnisse blättern zu müssen, die möglicherweise irrelevante Inhalte enthalten. 

Dieses Tutorial zeigt, wie eine benutzerdefinierte Suchinstanz konfiguriert und in eine neue Webseite integriert wird.

Die folgenden Aufgaben werden beschrieben:

> [!div class="checklist"]
> - Erstellen einer Instanz für die benutzerdefinierte Suche
> - Hinzufügen aktiver Einträge
> - Hinzufügen blockierter Einträge
> - Hinzufügen angehefteter Einträge
> - Integrieren der benutzerdefinierte Suche in eine Webseite

## <a name="prerequisites"></a>Voraussetzungen

- Um dem Tutorial folgen zu können, benötigen Sie einen Abonnementschlüssel für die API für die benutzerdefinierte Bing-Suche.  Sie müssen im Azure-Portal eine [Ressource für die benutzerdefinierte Bing-Suche erstellen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch), um einen Schlüssel zu erhalten.
- Falls Sie Visual Studio 2017 oder höher noch nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden.

## <a name="create-a-custom-search-instance"></a>Erstellen einer Instanz für die benutzerdefinierte Suche

Erstellen Sie wie folgt eine Instanz für die benutzerdefinierte Bing-Suche:

1. Öffnen Sie einen Internetbrowser.  
  
2. Navigieren Sie zum benutzerdefinierten Such[portal](https://customsearch.ai).  
  
3. Melden Sie sich mit einem Microsoft-Konto (MSA) am Portal an. Klicken Sie auf **Microsoft-Konto erstellen**, wenn Sie kein MSA besitzen. Wenn Sie das Portal zum ersten Mal nutzen, werden Sie nach Berechtigungen für den Zugriff auf Ihre Daten gefragt. Klicken Sie auf **Ja**.  
  
4. Klicken Sie nach der Anmeldung auf **Neue benutzerdefinierte Suche**. Geben Sie im Fenster **Neue benutzerdefinierte Suchinstanz erstellen** einen Namen ein, der sinnvoll ist und den Typ des Inhalts beschreibt, den die Suche zurückgibt. Sie können den Namen jederzeit ändern.  
  
   ![Screenshot des Felds „Neue benutzerdefinierte Suchinstanz erstellen“](../media/newCustomSrch.png)  
  
5. Klicken Sie auf „OK“, geben Sie eine URL an, und ob Unterseiten der URL eingeschlossen werden sollen.  
  
   ![Screenshot der URL-Definitionsseite](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Hinzufügen aktiver Einträge

Um Ergebnisse von bestimmten Websites oder URLs einzuschließen, fügen Sie diese der Registerkarte **Aktiv** hinzu.

1. Klicken Sie auf der Seite **Konfiguration** auf die Registerkarte **Aktiv**, und geben Sie die URL mindestens einer Website ein, die Sie in die Suche einbeziehen möchten.

    ![Screenshot der Registerkarte „Aktiv“ des Definitions-Editors](../media/customSrchEditor.png)

2. Um zu bestätigen, dass Ihre Instanz Ergebnisse zurückgibt, geben Sie eine Abfrage im Vorschaubereich auf der rechten Seite ein. Bing gibt nur Ergebnisse für öffentliche Websites zurück, die Bing indiziert hat.

## <a name="add-blocked-entries"></a>Hinzufügen blockierter Einträge

Um Ergebnisse von bestimmten Websites oder URLs auszuschließen, fügen Sie diese der Registerkarte **Blockiert** hinzu.

1. Klicken Sie auf der Seite **Konfiguration** auf die Registerkarte **Blockiert**, und geben Sie die URL mindestens einer Website ein, die Sie von der Suche ausschließen möchten.

    ![Screenshot der Registerkarte „Blockiert“ des Definitions-Editors](../media/blockedCustomSrch.png)


2. Um zu bestätigen, dass Ihre Instanz keine Ergebnisse aus blockierten Websites zurückgibt, geben Sie eine Abfrage im Vorschaubereich auf der rechten Seite ein. 

## <a name="add-pinned-entries"></a>Hinzufügen angehefteter Einträge

Um eine bestimmte Webseite am Anfang der Suchergebnisse anzuheften, fügen Sie auf der Registerkarte **Angeheftet** die Webseite und den Abfragebegriff hinzu. Die Registerkarte **Angeheftet** enthält eine Liste mit Webseiten- und Abfragebegriffpaaren, die die Webseite angeben, die als oberstes Ergebnis für eine bestimmte Abfrage angezeigt wird. Die Webseite wird nur dann angeheftet, wenn die Abfragezeichenfolge des Benutzers basierend auf der Übereinstimmungsbedingung der Pin mit der Abfragezeichenfolge der Pin übereinstimmt. Nur indizierte Webseiten werden in Suchvorgängen angezeigt. Weitere Informationen finden Sie unter [Konfigurieren der Benutzeroberfläche für die benutzerdefinierte Bing-Suche](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results).

1. Klicken Sie auf der Seite **Konfiguration** auf die Registerkarte **Angeheftet**, und geben Sie die Webseite und den Abfragebegriff der Webseite ein, die als oberstes Ergebnis zurückgegeben werden soll.  
  
2. Standardmäßig muss die Abfragezeichenfolge des Benutzers exakt mit der Abfragezeichenfolge Ihrer Pin für Bing übereinstimmen, damit die Webseite als oberstes Suchergebnis zurückgegeben wird. Um die Übereinstimmungsbedingung zu ändern, bearbeiten Sie die Pin (klicken Sie auf das Bleistiftsymbol), klicken Sie in der Spalte **Abfrageübereinstimmungsbedingung** auf „Genau“, und wählen Sie die für Ihre Anwendung geeignete Übereinstimmungsbedingung aus.  
  
    ![Screenshot der Registerkarte „Angeheftet“ des Definitions-Editors](../media/pinnedCustomSrch.png)
  
3. Um zu bestätigen, dass die Instanz die angegebene Webseite als oberstes Ergebnis zurückgibt, geben Sie den Abfrageausdruck ein, den Sie im Vorschaubereich auf der rechten Seite angeheftet haben.

## <a name="configure-hosted-ui"></a>Konfigurieren der gehosteten Benutzeroberfläche

Die benutzerdefinierte Suche stellt eine gehostete Benutzeroberfläche zum Rendern der JSON-Antwort von Ihrer benutzerdefinierten Suchinstanz bereit. So definieren Sie die Benutzeroberfläche:

1. Klicken Sie auf die Registerkarte **Gehostete Benutzeroberfläche**.  
  
2. Wählen Sie ein Layout aus.  
  
   ![Screenshot des Schritts zum Auswählen des Layouts der gehosteten Benutzeroberfläche](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Wählen Sie ein Farbdesign aus.  
  
   ![Screenshot zum Auswählen des Farbdesigns der gehosteten Benutzeroberfläche](./media/custom-search-hosted-ui-select-color-theme.png)  

   Wenn Sie das Farbdesign zur besseren Integration in Ihre Web-App optimieren müssen, klicken Sie auf **Design anpassen**. Nicht alle Farbkonfigurationen sind auf alle Layoutdesigns anwendbar. Um eine Farbe zu ändern, geben Sie den RGB-Hexadezimalwert der Farbe (z.B. #366eb8) in das entsprechende Textfeld ein. Alternativ klicken Sie auf die Farbschaltfläche und dann auf die Schattierung, die für Sie geeignet ist. Beachten Sie bei der Farbenauswahl stets die Barrierefreiheit.
  
   ![Screenshot zum Anpassen des Farbdesigns der gehosteten Benutzeroberfläche](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Geben Sie die zusätzlichen Konfigurationsoptionen an.  
  
   ![Screenshot des Schritts der zusätzlichen Konfigurationen der gehosteten Benutzeroberfläche](./media/custom-search-hosted-ui-additional-configurations.png)  
  
   Um erweiterte Konfigurationen zu nutzen, klicken Sie auf **Erweiterte Konfigurationen anzeigen**. Dadurch werden Konfigurationen wie z.B. *Linkziel* den Optionen für die Websuche, *Filter aktivieren* den Bild- und Videooptionen und *Suchfeld-Textplatzhalter* den verschiedenen Optionen hinzugefügt.

   ![Screenshot des Schritts der erweiterten Konfigurationen der gehosteten Benutzeroberfläche](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Wählen Sie Ihre Abonnementschlüssel in den Dropdownlisten aus. Alternativ können Sie den Abonnementschlüssel manuell eingeben.
  
   ![Screenshot: Abonnementschlüssel der gehosteten Benutzeroberfläche](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Nutzen der gehosteten Benutzeroberfläche

Es gibt zwei Möglichkeiten, die gehostete Benutzeroberfläche zu nutzen.  

- Option 1: Integrieren Sie den bereitgestellten JavaScript-Codeausschnitt in Ihre Anwendung.
- Option 2: Verwenden Sie den bereitgestellten HTML-Endpunkt.

Im weiteren Verlauf dieses Tutorials wird **Option 1: JavaScript-Codeausschnitt** beschrieben.  

## <a name="set-up-your-visual-studio-solution"></a>Einrichten Ihrer Visual Studio-Projektmappe

1. Öffnen Sie auf Ihrem Computer **Visual Studio**.  
  
2. Wählen Sie im Menü **Datei** die Option **Neu** und anschließend **Projekt** aus.  
  
3. Wählen Sie im Fenster **Neues Projekt** die Option **Visual C#-/Web-/ ASP.NET Core-Webanwendung** aus, benennen Sie Ihr Projekt, und klicken Sie dann auf **OK**.  
  
   ![Screenshot des Fensters des neuen Projekts](./media/custom-search-new-project.png)  
  
4. Wählen Sie im Fenster **Neue ASP.NET Core-Webanwendung** die Option **Webanwendung** aus, und klicken Sie auf **OK**.  
  
   ![Screenshot: Fenster „Neue ASP.NET Core-Webanwendung“](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>Bearbeiten von „Index.cshtml“

1. Erweitern Sie im **Projektmappen-Explorer** die Option **Seiten**, und doppelklicken Sie auf **index.cshtml**, um die Datei zu öffnen.  
  
   ![Screenshot des Projektmappen-Explorers mit erweiterten Seiten und ausgewählter Datei „index.cshtml“](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. Löschen Sie in „index.cshtml“ den gesamten Code ab Zeile 7 (einschließlich).  
  
   ```razor
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }    
   ```  
  
3. Fügen Sie ein Zeilenumbruchelement und ein div-Element hinzu, das als Container fungiert.  
  
   ```html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch"></div>
   ```  
  
4. Scrollen Sie auf der Seite **Gehostete Benutzeroberfläche** zum Abschnitt mit dem Titel **Nutzen der Benutzeroberfläche**. Klicken Sie auf die *Endpunkte*, um auf den JavaScript-Codeausschnitt zuzugreifen. Sie können den Codeausschnitt auch abrufen, indem Sie auf **Produktion** und dann die Registerkarte **Gehostete Benutzeroberfläche** klicken.
  
   <!-- Get new screenshot after prod gets new bits
   ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
   -->
  
5. Fügen Sie das Skriptelement in den Container ein, den Sie hinzugefügt haben.  
  
   ``` html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
   </div>
   ```  
  
6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **wwwroot**, und klicken Sie auf **Im Browser anzeigen**.  
  
   ![Screenshot des Projektmappen-Explorers: Auswählen der Option „Ansicht“ aus dem wwwroot-Kontextmenü im Browser](./media/custom-search-webapp-view-in-browser.png)  

Die neue benutzerdefinierte Suchwebseite sollte ähnlich wie das folgende Beispiel aussehen:

![Screenshot der benutzerdefinierten Suchwebseite](./media/custom-search-webapp-browse-index.png)

Beim Ausführen einer Suche werden die Ergebnisse wie folgt gerendert:

![Screenshot der benutzerdefinierten Suchergebnisse](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aufrufen eines Endpunkts für die benutzerdefinierte Bing-Suche (C#)](../call-endpoint-csharp.md)