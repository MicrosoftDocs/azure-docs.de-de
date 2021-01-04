---
title: Verwenden des Azure Cosmos DB-Änderungsfeeds zum Visualisieren von Echtzeit-Datenanalysen
description: Dieser Artikel beschreibt, wie der Änderungsfeed von einem Einzelhandelsunternehmen genutzt werden kann, um Nutzungsmuster zu verstehen und Daten in Echtzeit zu analysieren und zu visualisieren.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/28/2019
ms.author: sngun
ms.custom: devx-track-java
ms.openlocfilehash: d0eef49ea82afe50c5e178de9ad5e82bcb0db0eb
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342163"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Verwenden des Azure Cosmos DB-Änderungsfeeds zum Visualisieren von Echtzeit-Datenanalysen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Der Azure Cosmos DB-Änderungsfeed ist ein Mechanismus, um einen kontinuierlichen und inkrementellen Feed von Datensätzen aus einem Azure Cosmos-Container abzurufen, während diese Datensätze erstellt oder geändert werden. Der Änderungsfeed unterstützt Ihre Arbeit, indem er den Container bezüglich Änderungen überwacht. Anschließend wird die sortierte Liste von geänderten Dokumenten in der Reihenfolge ausgegeben, in der sie geändert wurden. Weitere Informationen zum Änderungsfeed finden Sie im Artikel [Arbeiten mit dem Änderungsfeed](change-feed.md). 

Dieser Artikel beschreibt, wie der Änderungsfeed von einem E-Commerce-Unternehmen genutzt werden kann, um Benutzermuster zu verstehen und Daten in Echtzeit zu analysieren und zu visualisieren. Sie können Ereignisse wie das Betrachten eines Artikels durch einen Benutzer, das Hinzufügen eines Artikels zu seinem Warenkorb oder das Kaufen eines Artikels analysieren. Wenn eines dieser Ereignisse eintritt, wird ein neuer Datensatz erstellt und vom Änderungsfeed protokolliert. Der Änderungsfeed löst dann eine Reihe von Schritten aus, die zur Visualisierung von Metriken führen, mit der die Leistung und Aktivität des Unternehmens analysiert werden kann. Zu den Beispielmetriken, die Sie visualisieren können, gehören die Einnahmen, die einzelnen Besucher der Website, die beliebtesten Artikel und der Durchschnittspreis der angezeigten, zum Warenkorb hinzugefügten und der gekauften Artikel. Diese Beispielmetriken können einem E-Commerce-Unternehmen helfen, die Popularität seiner Website zu bewerten, seine Werbe- und Preisstrategien zu entwickeln und Entscheidungen darüber zu treffen, in welchen Bestand es investieren soll.

Schauen Sie sich zu Beginn das folgende Video über die Lösung an:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Lösungskomponenten
Im folgenden Diagramm finden Sie eine Darstellung des Datenflusses und der in die Lösung integrierten Komponenten:

:::image type="content" source="./media/changefeed-ecommerce-solution/project-visual.png" alt-text="Projektdarstellung" border="false":::
 
1. **Datengenerierung:** Der Datensimulator wird verwendet, um Einzelhandelsdaten zu generieren, die Ereignisse wie das Betrachten eines Artikels, das Hinzufügen eines Artikels zum Warenkorb und den Kauf eines Artikels darstellen. Mit dem Datensimulator können Sie große Beispieldatensätze erstellen. Die generierten Beispieldaten enthalten Dokumente im folgenden Format:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Die generierten Daten werden in einem Azure Cosmos-Container gespeichert.  

3. **Änderungsfeed:** Der Änderungsfeed überwacht den Azure Cosmos-Container auf Änderungen. Jedes Mal, wenn ein neues Dokument zur Sammlung hinzugefügt wird (d.h. wenn ein Ereignis eintritt, wie z.B. ein Benutzer betrachtet einen Artikel, einen fügt den Artikel zu seinem Warenkorb hinzufügt oder kauft einen Artikel), löst der Änderungsfeed eine [Azure-Funktion](../azure-functions/functions-overview.md) aus.  

4. **Azure-Funktion:** Die Azure-Funktion verarbeitet die neuen Daten und sendet sie an einen [Azure Event Hub](../event-hubs/event-hubs-about.md).  

5. **Event Hub:** Der Azure Event Hub speichert diese Ereignisse und sendet sie für weitere Analysen an [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).  

6. **Azure Stream Analytics:** Azure Stream Analytics definiert Abfragen, um die Ereignisse zu verarbeiten und in Echtzeit Datenanalysen durchzuführen. Diese Daten werden dann an [Microsoft Power BI](/power-bi/desktop-what-is-desktop) gesendet.  

7. **Power BI:** Power BI wird zum Visualisieren der von Azure Stream Analytics gesendeten Daten verwendet. Sie können ein Dashboard erstellen, um anzuzeigen, wie sich die Metriken in Echtzeit ändern.  

## <a name="prerequisites"></a>Voraussetzungen

* Microsoft .NET Framework 4.7.1 oder höher

* Microsoft .NET Core 2.1 (oder höher)

* Visual Studio mit der Entwicklung für die universelle Windows-Plattform, .NET-Desktopentwicklung und ASP.NET- und Webentwicklungsworkloads

* Microsoft Azure-Abonnement

* Microsoft Power BI-Konto

* Laden Sie das [Übungseinheit für Azure Cosmos DB-Änderungsfeed](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) aus GitHub herunter. 

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen 

Erstellen Sie die Azure-Ressourcen – Azure Cosmos DB, Speicherkonto, Event Hub, Stream Analytics – die für die Lösung erforderlich sind. Sie werden diese Ressourcen über eine Azure Resource Manager-Vorlage bereitstellen. Verwenden Sie die folgenden Schritte, um diese Ressourcen bereitzustellen: 

1. Legen Sie die Windows PowerShell-Ausführungsrichtlinie auf **Unrestricted** fest. Öffnen Sie dafür **PowerShell als Administrator** , und führen Sie den folgenden Befehl aus:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Navigieren Sie im GitHub-Repository, das Sie im vorherigen Schritt heruntergeladen haben, zum Ordner **Azure Resource Manager** , und öffnen Sie die Datei **parameters.json**.  

3. Geben Sie die Werte für die Parameter„cosmosdbaccount_name“, „eventhubnamespace_name“, „storageaccount_name“ aus der **parameters.json** -Datei an. Die Namen, mit denen Sie die einzelnen Ressourcen bezeichnen, werden Sie später verwenden.  

4. Navigieren Sie im **Windows PowerShell** zum Ordner **Azure Resource Manager** , und führen den folgenden Befehl aus:

   ```powershell
   .\deploy.ps1
   ```
5. Geben Sie bei Aufforderung Ihre Azure- **Abonnement-ID** , **changefeedlab** als Ressourcengruppennamen und **run1** als Bereitstellungsnamen ein. Die Bereitstellung der Ressourcen kann nach dem Start bis zu 10 Minuten dauern.

## <a name="create-a-database-and-the-collection"></a>Erstellen einer Datenbank und der Sammlung

Sie können jetzt eine Sammlung erstellen, um Ereignisse auf der E-Commerce-Website zu speichern. Wenn ein Benutzer einen Artikel ansieht, in seinen Warenkorb legt oder kauft, erhält die Sammlung einen Datensatz, der die Aktion („angesehen“, „hinzugefügt“ oder „gekauft“), den Namen und den Preis des betreffenden Artikels und die ID-Nummer des betreffenden Benutzers enthält.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), und suchen Sie das **Azure Cosmos DB-Konto** , das durch die Vorlagenbereitstellung erstellt wurde.  

2. Wählen Sie im Bereich **Daten-Explorer** **Neue Sammlung** und füllen Sie das Formular mit den folgenden Informationen aus:  

   * Wählen Sie im Feld **Datenbank-ID** die Option **Neu erstellen** , und geben Sie **changefeedlabdatabase** ein. Aktivieren Sie nicht das Kontrollkästchen **Durchsatz Bereitstellungsdatenbank**.  
   * Geben Sie für das Feld **Sammlungs-ID** **changefeedlabcollection** ein.  
   * In das Feld **Partitionsschlüssel** geben Sie **/Item** ein. Hier ist die Groß-/Kleinschreibung zu beachtet. Stellen Sie also sicher, dass die Informationen ordnungsgemäß eingegeben werden.  
   * Geben Sie im Feld **Durchsatz** **10000** ein.  
   * Klicken Sie auf die Schaltfläche **OK**.  

3. Erstellen Sie anschließend für die Änderungsfeedverarbeitung eine weitere Sammlung mit der Bezeichnung **leases**. Die Lease-Sammlung koordiniert das Verarbeiten der Änderungsfeeds über mehrere Worker. Eine separate Sammlung wird verwendet, um die Leases mit einer Lease pro Partition zu speichern.  

4. Kehren Sie zum Bereich **Daten-Explorer** zurück, wählen Sie **Neue Sammlung** und füllen Sie das Formular mit den folgenden Informationen aus:

   * Wählen Sie im Feld **Datenbank-ID** die Option **Vorhandene verwenden** , und geben Sie **changefeedlabdatabase** ein.  
   * Geben Sie für das Feld **Sammlungs-ID** **leases** ein.  
   * Für **Speicherkapazität** wählen Sie **Fest**.  
   * Behalten Sie im Feld **Durchsatz** den Standardwert bei.  
   * Klicken Sie auf die Schaltfläche **OK**.

## <a name="get-the-connection-string-and-keys"></a>Abrufen der Verbindungszeichenfolge und der Schlüssel

### <a name="get-the-azure-cosmos-db-connection-string"></a>Abrufen einer Azure Cosmos DB-Verbindungszeichenfolge

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), und suchen Sie das **Azure Cosmos DB-Konto** , das durch die Vorlagenbereitstellung erstellt wurde.  

2. Navigieren Sie zum Bereich **Schlüssel** , kopieren Sie die PRIMÄRE VERBINDUNGSZEICHENFOLGE, und fügen Sie sie in Notepad oder ein anderes Dokument ein, auf das Sie während der gesamten Übungseinheit Zugriff haben. Verwenden Sie die Bezeichnung **Cosmos DB-Verbindungszeichenfolge**. Sie müssen die Zeichenfolge später in den Code kopieren. Merken Sie sich also, wo Sie sie speichern.

### <a name="get-the-storage-account-key-and-connection-string"></a>Abrufen des Speicherkontoschlüssels und der Verbindungszeichenfolge

In Azure Storage-Konten können Benutzer Daten speichern. In dieser Übungseinheit verwenden Sie ein Speicherkonto zum Speichern von Daten, die von der Azure-Funktion verwendet werden. Die Azure-Funktion wird ausgelöst, wenn Änderungen an der Sammlung vorgenommen werden.

1. Kehren Sie zu Ihrer Ressourcengruppe zurück, und öffnen Sie das Speicherkonto, das Sie zuvor erstellt haben.  

2. Wählen Sie **Zugriffsschlüssel** im Menü auf der linken Seite.  

3. Kopieren Sie die Werte unter **key 1** und fügen Sie sie in Notepad oder ein anderes Dokument ein, auf das Sie während der gesamten Übungseinheit Zugriff haben. Verwenden Sie für **Schlüssel** die Bezeichnung **Speicherschlüssel** und für **Verbindungszeichenfolge** die Bezeichnung **Speicherverbindungszeichenfolge**. Sie müssen diese Zeichenfolgen später in den Code kopieren. Merken Sie sich also, wo Sie sie speichern.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Abrufen der Verbindungszeichenfolge des Event Hub-Namespace

Einen Azure Event Hub empfängt die Ereignisdaten und speichert und verarbeitet sie und leitet sie weiter. In dieser Übungseinheit erhält der Azure Event Hub jedes Mal ein Dokument, wenn ein neues Ereignis eintritt (d.h. ein Artikel wird von einem Benutzer angesehen, in den Warenkorb eines Benutzers gelegt oder von einem Benutzer gekauft) und leitet dieses Dokument dann an Azure Stream Analytics weiter.

1. Kehren Sie zu Ihrer Ressourcengruppe zurück, und öffnen Sie den **Event Hub-Namespace** , den Sie vor der Übungseinheit erstellt und benannt haben.  

2. Wählen Sie **Freigegebene Zugriffsrichtlinien** im Menü auf der linken Seite.  

3. Wählen Sie **RootManageSharedAccessKey**. Kopieren Sie den Wert für **Verbindungszeichenfolge-primärer Schlüssel** und fügen Sie ihn in Notepad oder ein anderes Dokument ein, auf das Sie während der gesamten Übungseinheit Zugriff haben. Bezeichnen Sie ihn mit „Verbindungszeichenfolge des **Event Hub-Namespace** “. Sie müssen die Zeichenfolge später in den Code kopieren. Merken Sie sich also, wo Sie sie speichern.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Einrichten der Azure-Funktion zum Lesen des Änderungsfeeds

Wenn ein neues Dokument erstellt oder ein aktuelles Dokument in einem Cosmos-Container geändert wird, fügt der Änderungsfeed dieses geänderte Dokument automatisch zu seinem Verlauf der Sammlungsänderungen hinzu. Sie werden jetzt eine Azure-Funktion erstellen und ausführen, die den Änderungsfeed verarbeitet. Wenn ein Dokument in der von Ihnen erstellten Sammlung erstellt oder geändert wird, löst der Änderungsfeed die Azure-Funktion aus. Anschließend sendet die Azure-Funktion das geänderte Dokument an den Event Hub.

1. Kehren Sie zum Repository zurück, das Sie auf Ihrem Gerät geklont haben.  

2. Klicken Sie mit der rechten Maustaste auf die Datei **ChangeFeedLabSolution.sln** , und wählen Sie **Mit Visual Studio öffnen**.  

3. Navigieren Sie in Visual Studio zu **local.settings.json**. Geben Sie anschließend die von Ihnen notierten Werte in die entsprechenden Felder ein.  

4. Navigieren Sie zu **ChangeFeedProcessor.cs**. Führen Sie für die Parameter der **Run** -Funktion die folgenden Aktionen aus:  

   * Ersetzen Sie den Text **IHR SAMMLUNSNAME HIER** mit dem Namen der Sammlung. Wenn Sie die vorherigen Anweisungen beachtet haben, lautet der Name Ihre Sammlung „changefeedlabcollection“.  
   * Ersetzen Sie den Text **IHR LEASES-SAMMLUNSNAME HIER** mit dem Namen der Leases-Sammlung. Wenn Sie die vorherigen Anweisungen beachtet haben, lautet der Name Ihre Leases-Sammlung **leases**.  
   * Stellen Sie am oberen Rand von Visual Studio sicher, dass im Feld „Startprojekt“ links neben dem grünen Pfeil **ChangeFeedFunction** steht.  
   * Wählen Sie **Start** am oberen Rand der Seite, um das Programm auszuführen.  
   * Wenn die Funktion ausgeführt wird, wird zur Bestätigung in der Konsolenanwendung „Auftragshost gestartet“ angezeigt.

## <a name="insert-data-into-azure-cosmos-db"></a>Einfügen von Daten in Azure Cosmos DB 

Um zu sehen, wie der Änderungsfeed neue Aktionen auf einer E-Commerce-Website verarbeitet, müssen Daten simuliert werden, die die Benutzer beim Anschauen von Artikeln aus dem Produktkatalog, beim Hinzufügen dieser Artikel zu ihren Warenkörben und beim Kauf dieser Artikel darstellen. Diese Daten sind willkürlich und dienen dazu, zu replizieren, wie die Daten auf einer E-Commerce-Seite aussehen würden.

1. Navigieren Sie zurück zum Repository im Datei-Explorer, und klicken Sie mit der rechten Maustaste auf **ChangeFeedFunction.sln** , um diese erneut in einem neuen Visual Studio-Fenster zu öffnen.  

2. Navigieren Sie zur Datei **App.config**. Fügen Sie im `<appSettings>`-Block den Endpunkt und den eindeutigen **PRIMÄRSCHLÜSSEL** Ihres Azure Cosmos DB-Kontos hinzu, den Sie zuvor abgerufen haben.  

3. Fügen Sie die Namen für **Sammlung** und **Datenbank** ein. (Diese sollten **changefeedlabcollection** und **changefeedlabdatabase** lauten, sofern Sie sie nicht anders benennen möchten.)

   :::image type="content" source="./media/changefeed-ecommerce-solution/update-connection-string.png" alt-text="Aktualisieren von Verbindungszeichenfolgen":::
 
4. Speichern Sie die Änderungen auf alle bearbeiteten Dateien.  

5. Stellen Sie am oberen Rand von Visual Studio sicher, dass im Feld **Startprojekt** links neben dem grünen Pfeil **DataGenerator** steht. Wählen Sie anschließend **Start** am oberen Rand der Seite, um das Programm auszuführen.  
 
6. Warten Sie, bis das Programm ausgeführt wird. Die Sterne bedeuten, dass die Daten empfangen werden. Halten Sie das Programm nicht an – es ist wichtig, dass große Datenmengen gesammelt werden.  

7. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), dann zum Cosmos DB-Konto innerhalb Ihrer Ressourcengruppe und anschließend zum **Daten-Explorer**. Dort werden die randomisierten Daten angezeigt, die in Ihre **changefeedlabcollection** importiert wurden.
 
   :::image type="content" source="./media/changefeed-ecommerce-solution/data-generated-in-portal.png" alt-text="Im Portal generierte Daten":::

## <a name="set-up-a-stream-analytics-job"></a>Einrichten eines Stream Analytics-Auftrags

Azure Stream Analytics ist ein vollständig verwalteter Clouddienst für die Verarbeitung von Streamingdaten in Echtzeit. In dieser Übungseinheit werden Sie Stream Analytics verwenden, um neue Ereignisse aus dem Event Hub zu verarbeiten (d.h. wenn ein Artikel angesehen, einem Warenkorb hinzugefügt oder gekauft wird), diese Ereignisse in Echtzeitdatenanalyse einbinden und sie zur Visualisierung an Power BI zu senden.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer Ressourcengruppe und dann zu **streamjob1** (Stream Analytics-Auftrag, den Sie vor der Übungseinheit erstellt haben).  

2. Wählen Sie **Eingaben** , wie unten dargestellt.  

   :::image type="content" source="./media/changefeed-ecommerce-solution/create-input.png" alt-text="Erstellen einer Eingabe":::

3. Wählen Sie **+ Datenstromeingabe hinzufügen**. Wählen Sie dann im Dropdownmenü die Option **Event Hub** aus.  

4. Tragen Sie in das neue Eingabeformular die folgenden Details ein:

   * Geben Sie in das Alias-Feld **Eingabe** **input** ein.  
   * Wählen Sie die Option für **Event Hub aus Ihren Abonnements auswählen**.  
   * Legen Sie für das Feld **Abonnement** Ihr Abonnement fest.  
   * Geben Sie im Feld **Event Hub-Namespace** den Namen Ihres Event Hub-Namespace ein, den Sie vor der Übungseinheit erstellt haben.  
   * Wählen Sie im Feld **Event Hub-Name** die Option für **Vorhandene verwenden** und anschließend **event-hub1** aus dem Dropdownmenü.  
   * Behalten Sie im Feld für den Namen der **Event Hub-Richtlinie** den Standardwert bei.  
   * Behalten Sie für **Ereignisserialisierungsformat** **JSON** bei.  
   * Behalten Sie für **Verschlüsselungsfeld** **UTF-8** bei.  
   * Behalten Sie für **Typ der Ereigniskomprimierung** **None** bei.  
   * Wählen Sie die Schaltfläche **Speichern** aus.

5. Navigieren Sie zurück zur Stream Analytics-Auftragsseite, und wählen **Ausgaben**.  

6. Wählen Sie **+ Hinzufügen**. Wählen Sie dann im Dropdownmenü die Option **Power BI** aus.  

7. Um eine neue Power BI-Ausgabe zum Visualisieren des Durchschnittspreises zu erstellen, führen Sie die folgenden Aktionen aus:

   * Geben Sie im Feld **Ausgabealias** **averagePriceOutput** ein.  
   * Behalten Sie im Feld **Gruppenarbeitsbereich** **Verbindung zum Laden von Arbeitsbereichen autorisieren** bei.  
   * Geben Sie im Feld **Datasetname** **averagePrice** ein.  
   * Geben Sie im Feld **Tabellenname** **averagePrice** ein.  
   * Wählen Sie die Schaltfläche **Autorisieren** aus, und befolgen Sie die Anweisungen, um die Verbindung mit Power BI zu autorisieren.  
   * Wählen Sie die Schaltfläche **Speichern** aus.  

8. Rufen Sie anschließend wieder **streamjob1** auf, und wählen Sie **Abfrage bearbeiten** aus.

   :::image type="content" source="./media/changefeed-ecommerce-solution/edit-query.png" alt-text="Abfrage bearbeiten":::
 
9. Fügen Sie die folgende Abfrage im Abfragefenster ein. Die Abfrage **AVERAGE PRICE** berechnet den Durchschnittspreis aller Artikel, die von Benutzern angesehen, zu den Einkaufswagen der Benutzer hinzugefügt und von Benutzern gekauft werden. Anhand dieser Metriken können E-Commerce-Unternehmen entscheiden, zu welchen Preisen sie Artikel verkaufen und in welchen Bestand sie investieren wollen. Wenn beispielsweise der Durchschnittspreis der angesehenen Artikel viel höher ist als der Durchschnittspreis der gekauften Artikel, kann ein Unternehmen beschließen, weniger teure Artikel zu seinem Bestand hinzuzufügen.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Wählen Sie dann oben links die Option **Speichern** aus.  

11. Rufen Sie jetzt wieder **streamjob1** auf, und wählen Sie oben auf der Seite **Start** aus. Es kann einige Minuten dauern, bis Azure Stream Analytics gestartet wird. Anschließend wechselt die Anzeige von „Wird gestartet“ zu „Wird ausgeführt“.

## <a name="connect-to-power-bi"></a>Verbinden mit Power BI

Power BI ist eine Suite aus Business Analytics-Tools zum Analysieren von Daten und Teilen von Einblicken. Es ist ein gutes Beispiel dafür, wie Sie die analysierten Daten strategisch visuell darstellen können.

1. Melden Sie sich beim Power BI an, und navigieren Sie zu **Mein Arbeitsbereich** über das Menü auf der linken Seite.  

2. Wählen Sie **+ Erstellen** in der oberen rechten Ecke, und wählen Sie dann **Dashboard** aus, um ein Dashboard zu erstellen.  

3. Wählen Sie **+ Kachel hinzufügen** in der oberen rechten Ecke aus.  

4. Wählen Sie **Benutzerdefinierte Streamingdaten** aus, und wählen Sie dann die Schaltfläche **Weiter** aus.  
 
5. Wählen Sie **averagePrice** aus **IHRE DATASETS** aus, und wählen Sie dann **Weiter** aus.  

6. Wählen Sie im Feld **Visualisierungstyp** die Option **Balkendiagramm (gruppiert)** aus dem Dropdownmenü. Fügen Sie unter **Achse** eine Aktion hinzu. Überspringen Sie **Legende** ,ohne etwas hinzuzufügen. Fügen Sie dann im nächsten Abschnitt **Wert** **Avg** hinzu. Wählen Sie **Weiter** , benennen Sie das Diagramm, und wählen **Übernehmen**. Das neue Diagramm sollte jetzt auf dem Dashboard angezeigt werden.  

7. Wenn Sie weitere Metriken visualisieren möchten, gehen Sie zurück zu **streamjob1** , und erstellen Sie drei zusätzliche Ausgaben mit den folgenden Feldern.

   a. **Ausgabealias:** IncomingRevenueOutput, Datasetname: IncomingRevenue, Tabellenname: IncomingRevenue  
   b. **Ausgabealias:** top5Output, Datasetname: top5, Tabellenname: top5  
   c. **Ausgabealias:** uniqueVisitorCountOutput, Datasetname: uniqueVisitorCount, Tabellenname: uniqueVisitorCount

   Wählen Sie dann **Abfrage bearbeiten** aus, und fügen Sie die folgenden Abfragen **über** der Abfrage ein, die Sie bereits geschrieben haben.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   Die TOP 5-Abfrage berechnet die ersten 5 Artikel, geordnet nach der Anzahl der Käufe. Anhand dieser Metriken können E-Commerce-Unternehmen beurteilen, welche Artikel am beliebtesten sind, und kann die Werbe-, Preis- und Bestandsentscheidungen des Unternehmens beeinflussen.

   Die REVENUE-Abfrage berechnet den Umsatz, indem sie die Preise aller gekauften Artikel pro Minute summiert. Anhand dieser Metriken können E-Commerce-Unternehmen ihre finanzielle Performance bewerten und darüber hinaus verstehen, zu welchen Tageszeiten die meisten Einnahmen erzielt werden. Dies kann Auswirkungen auf die Gesamtstrategie des Unternehmens haben, insbesondere auf das Marketing.

   Die UNIQUE VISITORS-Abfrage berechnet, wie viele Besucher sich alle 5 Sekunden auf der Website befinden, indem sie eindeutige Warenkorb-IDs ermittelt. Anhand dieser Metriken können E-Commerce-Unternehmen Ihre Website-Aktivitäten bewerten und Strategien zur Neukundengewinnung entwickeln.

8. Sie können jetzt auch Kacheln für diese Datasets hinzufügen.

   * Für TOP 5 wäre es sinnvoll, ein gruppiertes Säulendiagramm mit den Artikeln als Achse und der Anzahl als Wert zu erstellen.  
   * Für REVENUE wäre es sinnvoll, ein Liniendiagramm mit der Zeit als Achse und der Summe der Preise als Wert zu erstellen. Das anzuzeigende Zeitfenster sollte so groß wie möglich sein, um möglichst viele Informationen zu liefern.  
   * Für UNIQUE VISITORS wäre es sinnvoll, eine Kartenvisualisierung mit der Anzahl der einzelnen Besucher als Wert durchzuführen.

   So sieht ein Beispieldashboard mit diesen Diagrammen aus:

   :::image type="content" source="./media/changefeed-ecommerce-solution/visualizations.png" alt-text="Screenshot eines Beispieldashboards mit den Diagrammen „Average Price of Items by Action“, „Unique Visitors“, „Revenue“ und „Top 5 Items Purchased“":::

## <a name="optional-visualize-with-an-e-commerce-site"></a>Optional: Visualisieren mit einer E-Commerce-Website

Sie werden nun sehen, wie Sie Ihr neues Datenanalyse-Tool nutzen können, um sich mit einer echten E-Commerce-Site zu verbinden. Um die E-Commerce-Website zu erstellen, verwenden Sie eine Azure Cosmos-Datenbank, um die Liste der Produktkategorien (Damen, Herren, Unisex), den Produktkatalog und eine Liste der beliebtesten Artikel zu speichern.

1. Navigieren Sie zurück zum [Azure-Portal](https://portal.azure.com/). Navigieren Sie dann zu Ihrem **Cosmos DB-Konto** und anschließend zum **Daten-Explorer**.  

   Fügen Sie zwei Sammlungen unter **changefeedlabdatabase** - **products** und **categories** mit fester Speicherkapazität hinzu.

   Fügen Sie unter **changefeedlabdatabase** eine weitere Sammlung mit dem Namen **topItems** und dem Partitionsschlüssel **/Item** hinzu.

2. Wählen Sie die Sammlung **topItems** aus, und legen Sie unter **Skalierung und Einstellungen** die **Gültigkeitsdauer** auf **30 Sekunden** fest, sodass „topItems“ alle 30 Sekunden aktualisiert wird.

   :::image type="content" source="./media/changefeed-ecommerce-solution/time-to-live.png" alt-text="Gültigkeitsdauer":::

3. Um in die Sammlung **topItems** die am häufigsten gekauften Artikel einzutragen, navigieren Sie zurück zu **streamjob1** und fügen Sie eine neue **Ausgabe** hinzu. Wählen Sie **Cosmos DB**.

4. Füllen Sie die erforderlichen Felder aus, wie unten dargestellt.

   :::image type="content" source="./media/changefeed-ecommerce-solution/cosmos-output.png" alt-text="Cosmos-Ausgabe":::
 
5. Wenn Sie die optionale TOP 5-Abfrage im vorherigen Teil der Übungseinheit hinzugefügt haben, fahren Sie mit Teil 5a fort. Wenn nicht, fahren Sie mit Teil 5b fort.

   5a. Wählen Sie in **streamjob1** die Option **Abfrage bearbeiten** , und fügen Sie die folgende Abfrage in Ihrem Azure Stream Analytics-Abfrage-Editor unter der TOP 5-Abfrage, aber über den anderen Abfragen ein.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. Wählen Sie in **streamjob1** die Option **Abfrage bearbeiten** , und fügen Sie die folgende Abfrage in Ihrem Azure Stream Analytics-Abfrage-Editor über allen anderen Abfragen ein.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Öffnen Sie **EcommerceWebApp.sln** , und navigieren Sie zur Datei **Web.config** im **Projektmappen-Explorer**.  

7. Fügen Sie im `<appSettings>`-Block den zuvor gespeicherten **URI** und **PRIMÄRSCHLÜSSEL** unter **Ihren URI hier** und **Ihren Primärschlüssel hier** ein. Geben Sie anschließend Ihren **Datenbanknamen** und **Sammlungsname** an. (Diese sollten **changefeedlabdatabase** und **changefeedlabcollection** lauten, sofern Sie sie nicht anders benennen möchten.)

   Geben Sie den **Produktsammlungsnamen** , **Kategoriensammlungsnamen** und den **Top Items-Sammlungsnamen** ein, wie angegeben. (Diese sollten **products, categories und topItems** lauten, sofern Sie sie nicht anders benennen möchten.)  

8. Navigieren Sie zum **Auscheckordner** in **EcommerceWebApp.sln.** , und öffnen Sie ihn. Öffnen Sie dann die Datei **Web.config** in diesem Ordner.  

9. Fügen Sie im `<appSettings>`-Block den zuvor gespeicherten **URI** und **PRIMÄRSCHLÜSSEL** an den entsprechenden Stellen ein. Geben Sie anschließend Ihren **Datenbanknamen** und **Sammlungsname** an. (Diese sollten **changefeedlabdatabase** und **changefeedlabcollection** lauten, sofern Sie sie nicht anders benennen möchten.)  

10. Drücken Sie **Start** am oberen Rand der Seite, um das Programm auszuführen.  

11. Jetzt können Sie auf der E-Commerce-Website experimentieren. Wenn Sie einen Artikel ansehen, zu Ihrem Warenkorb hinzufügen, die Menge eines Artikels in Ihrem Warenkorb ändern oder einen Artikel kaufen, werden diese Ereignisse über den Cosmos DB-Änderungsfeed an den Event Hub, ASA und dann Power BI weitergeleitet. Wir empfehlen, den DataGenerator weiter auszuführen, um aussagekräftige Daten zum Datenverkehr zu generieren und einen realistischen Satz von „Hot Products“ auf der E-Commerce-Seite bereitzustellen.

## <a name="delete-the-resources"></a>Löschen der Ressourcen

Um die Ressourcen zu löschen, die Sie während dieser Übungseinheit erstellt haben, navigieren Sie im [Azure-Portal](https://portal.azure.com/) zur Ressourcengruppe, und wählen Sie dann oben auf der Seite im Menü die Option **Ressourcengruppe löschen** aus, und folgen Sie den Anweisungen.

## <a name="next-steps"></a>Nächste Schritte 
  
* Weitere Informationen zum Änderungsfeed finden Sie im Artikel [Verwenden der Unterstützung von Änderungsfeeds in Azure Cosmos DB](change-feed.md).