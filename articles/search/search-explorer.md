---
title: Suchexplorer-Tool zum Abfragen von Daten im Azure-Portal – Azure Search
description: Verwenden Sie die im Azure-Portal verfügbaren Tools wie den Suchexplorer, um Indizes in Azure Search abzufragen. Geben Sie Suchbegriffe oder vollqualifizierte Suchzeichenfolgen mit erweiterter Syntax ein.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 392699182859a090c13304f63d28a78b95a65ec7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024023"
---
# <a name="search-explorer-for-querying-data-in-azure-search"></a>Suchexplorer zum Abfragen von Daten in Azure Search 

In diesem Artikel erfahren Sie, wie Sie einen vorhandenen Azure Search-Index mithilfe des **Suchexplorers** im Azure-Portal abfragen. Mit dem Suchexplorer können Sie einfache oder vollständige Lucene-Abfragezeichenfolgen an einen beliebigen Index in Ihrem Dienst übermitteln. 

   ![Befehl für den Suchexplorer im Portal](./media/search-explorer/search-explorer-cmd2.png "Befehl für den Suchexplorer im Portal")


Hilfe zu den ersten Schritten finden Sie unter [So starten Sie den Suchexplorer](#start-search-explorer).

## <a name="basic-search-strings"></a>Grundlegende Suchzeichenfolgen

In den folgenden Beispielen wird vom integrierten Beispielimmobilienindex ausgegangen. Hilfe zum Erstellen dieses Index finden Sie unter [Schnellstart: Importieren, Indizieren und Abfragen im Azure-Portal](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>Beispiel 1: leere Suche

Führen Sie für einen ersten Blick auf Ihre Inhalte eine leere Suche aus, indem Sie ohne Angabe von Begriffen auf **Suchen** klicken. Eine leere Suche ist eine sinnvolle erste Abfrage, da sie vollständige Dokumente zurückgibt, sodass Sie sich mit dem Aufbau des Dokuments vertraut machen können. Bei einer leeren Suche gibt es keinen Suchrang, und die Dokumente werden in beliebiger Reihenfolge (`"@search.score": 1` für alle Dokumente) zurückgegeben. Standardmäßig werden in einer Suchanforderung 50 Dokumente zurückgegeben.

Die äquivalente Syntax für eine leere Suche ist `*` oder `search=*`.

   ```Input
   search=*
   ```

   **Ergebnisse**
   
   ![Beispiel für eine leere Abfrage](./media/search-explorer/search-explorer-example-empty.png "Beispiel für unqualifizierte oder leere Abfrage")

### <a name="example-2---free-text-search"></a>Beispiel 2: Freitextsuche

Freiformabfragen mit oder ohne Operatoren sind nützlich zum Simulieren von benutzerdefinierten Abfragen, die von einer benutzerdefinierten App an Azure Search gesendet werden. Beachten Sie, dass bei der Angabe von Abfragebegriffen oder -ausdrücken der Suchrang eine Rolle spielt. Im folgenden Beispiel wird eine Freitextsuche veranschaulicht.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Ergebnisse**

   Sie können die Ergebnisse mit STRG+F nach bestimmten Begriffen durchsuchen.

   ![Beispiel für Freitextabfrage](./media/search-explorer/search-explorer-example-freetext.png "Beispiel für Freitextabfrage")

### <a name="example-3---count-of-matching-documents"></a>Beispiel 3: Anzahl übereinstimmender Dokumente 

Fügen Sie **$count** hinzu, um die Anzahl der Übereinstimmungen in einem Index abzurufen. Bei einer leeren Suche ist die Anzahl die Gesamtanzahl der Dokumente im Index. Bei einer qualifizierten Suche ist es die Anzahl der Dokumente, die der eingegebenen Abfrage entsprechen.

   ```Input1
   $count=true
   ```
   **Ergebnisse**

   ![Beispiel für die Anzahl der Dokumente](./media/search-explorer/search-explorer-example-count.png "Beispiel für die Anzahl der übereinstimmenden Dokumente")

### <a name="example-4---restrict-fields-in-search-results"></a>Beispiel 4: Beschränken der Felder in den Suchergebnissen

Fügen Sie **$select** hinzu, um die Ergebnisse auf die explizit benannten Felder zu beschränken und damit die Lesbarkeit der Ausgabe im **Suchexplorer** zu erhöhen. Um die Suchzeichenfolge und **$count=true** beizubehalten, stellen Sie den Argumenten das Präfix **&** voran. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Ergebnisse**

   ![Beispiel für das Beschränken der Felder](./media/search-explorer/search-explorer-example-selectfield.png "Beschränken der Felder in den Suchergebnissen")

### <a name="example-5---return-next-batch-of-results"></a>Beispiel 5: Zurückgeben des nächsten Batches von Ergebnissen

Azure Search gibt die ersten 50 Übereinstimmungen basierend auf dem Suchrang zurück. Um den nächsten Satz von übereinstimmenden Dokumenten abzurufen, fügen Sie **$top=100,&$skip=50** an, um das Resultset auf 100 Dokumente zu vergrößern (Standardwert 50, Höchstwert 1.000) und die ersten 50 Dokumente zu überspringen. Denken Sie daran, dass Sie Suchkriterien angeben müssen, z.B. einen Abfragebegriff oder -ausdruck, um priorisierte Ergebnisse zu erhalten. Beachten Sie, dass sich die Suchbewertungen verringern, je weiter Sie in die Suchergebnisse vordringen.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100,&$skip=50
   ```

   **Ergebnisse**

   ![Batchsuchergebnisse](./media/search-explorer/search-explorer-example-topskip.png "Zurückgeben des nächsten Batches von Suchergebnissen")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filterausdrücke (größer als, kleiner als, gleich)

Verwenden Sie den Parameter **$filter**, wenn Sie anstelle einer Freitextsuche präzise Kriterien angeben möchten. In diesem Beispiel wird nach einer Anzahl von Schlafzimmern größer als 3 gesucht: `search=seattle condo&$filter=beds gt 3&$count=true`

   ![Filterausdruck](./media/search-explorer/search-explorer-example-filter.png "Filtern nach Kriterien")

## <a name="order-by-expressions"></a>Sortierausdrücke

Fügen Sie **$orderby** hinzu, um die Ergebnisse nach einem anderen Feld als der Suchbewertung zu sortieren. Als Beispielausdruck zum Testen können Sie `search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc` verwenden.

   ![orderby-Ausdruck](./media/search-explorer/search-explorer-example-ordery.png "Ändern der Sortierreihenfolge")

Die Ausdrücke **$filter** und **$orderby** sind OData-Konstrukte. Weitere Informationen finden Sie unter [OData Expression Syntax for Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (OData-Ausdruckssyntax für Azure Search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>So starten Sie den Suchexplorer

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) im Dashboard die Seite mit dem Suchdienst, oder [suchen Sie Ihren Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in der Liste mit den Diensten.

2. Klicken Sie auf der Dienstübersichtsseite auf **Suchexplorer**.

   ![Befehl für den Suchexplorer im Portal](./media/search-explorer/search-explorer-cmd2.png "Befehl für den Suchexplorer im Portal")

3. Wählen Sie den abzufragenden Index aus.

   ![Auswählen des abzufragenden Index](./media/search-explorer/search-explorer-changeindex-se2.png "Auswählen des abzufragenden Index")

4. Legen Sie optional die API-Version fest. Standardmäßig wird die aktuelle allgemein verfügbare API-Version ausgewählt. Sie können jedoch eine Vorschauversion oder eine ältere API auswählen, wenn die Syntax, die Sie verwenden möchten, versionsspezifisch ist.

5. Nachdem der Index und die API-Version ausgewählt wurden, geben Sie Suchbegriffe oder die vollqualifizierten Abfrageausdrücke in der Suchleiste ein und klicken auf **Suchen**, um die Suche auszuführen.

   ![Eingeben der Suchbegriffe und Klicken auf „Suchen“](./media/search-explorer/search-explorer-query-string-example.png "Eingeben der Suchbegriffe und Klicken auf „Suchen“")

Tipps für die Suche im **Suchexplorer**:

+ Ergebnisse werden als ausführliche JSON-Dokumente zurückgegeben, sodass Sie den Dokumentaufbau und den Inhalt vollständig anzeigen können. Sie können in den Beispielen gezeigte Abfrageausdrücke verwenden, um die zurückgegebenen Felder einzuschränken.

+ Dokumente bestehen aus allen Feldern, die im Index als **abrufbar** gekennzeichnet sind. Klicken Sie zum Anzeigen von Indexattributen im Portal auf der Übersichtsseite der Suche in der Liste **Indizes** auf *realestate-us-sample*.

+ Freiformabfragen ähneln der Eingabe in kommerziellen Webbrowsern und eignen sich zum Testen der Endbenutzererfahrung. Beim integrierten Beispielimmobilienindex könnten Sie z.B. „Seattle apartments lake washington“ eingeben und dann mit STRG+F Begriffe in den Suchergebnissen finden. 

+ Abfrage- und Filterausdrücke müssen in einer Syntax formuliert sein, die von Azure Search unterstützt wird. Der Standardwert ist eine [einfache Syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), Sie können aber optional für leistungsstarke Abfragen die [vollständige Lucene-Syntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) verwenden. [Filterausdrücke](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) folgen einer OData-Syntax.


## <a name="next-steps"></a>Nächste Schritte

Die folgenden Ressourcen enthalten zusätzliche Informationen und Beispiele für die Abfragesyntax:

 + [Einfache Abfragesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene-Abfragesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene-Abfragebeispiele](search-query-lucene-examples.md) 
 + [OData-Filterausdruckssyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 