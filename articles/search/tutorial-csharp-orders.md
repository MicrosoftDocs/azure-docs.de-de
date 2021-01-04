---
title: C#-Tutorial zur Reihenfolge von Ergebnissen
titleSuffix: Azure Cognitive Search
description: In diesem C#-Tutorial wird veranschaulicht, wie Sie Suchergebnisse sortieren. Es basiert auf einem vorherigen hotels-Projekt mit einer Sortierung nach primären und sekundären Eigenschaften und enthält ein Bewertungsprofil zum Hinzufügen von Kriterien für die Erhöhung.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/02/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5a55a330f6f4fefb86f2c056cd0ca3b2ba5f4b29
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499593"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Tutorial: Sortieren von Suchergebnissen mithilfe des .NET SDK

In dieser Tutorialreihe wurden Ergebnisse in einem [Standardordner](index-add-scoring-profiles.md#what-is-default-scoring) zurückgegeben und angezeigt. In diesem Tutorial fügen Sie primäre und sekundäre Sortierungskriterien hinzu. Als Alternative zur Sortierung nach Zahlenwerten zeigt das letzte Beispiel, wie Ergebnisse nach einem benutzerdefinierten Bewertungsprofil angeordnet werden. Wir werden uns auch etwas näher mit der Anzeige von _komplexen Typen_ befassen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Sortieren von Ergebnissen basierend auf einer Eigenschaft
> * Sortieren von Ergebnissen basierend auf mehreren Eigenschaften
> * Filtern von Ergebnissen basierend auf der Entfernung von einem geografischen Punkt
> * Sortieren von Ergebnissen basierend auf einem Bewertungsprofil

## <a name="overview"></a>Übersicht

Dieses Tutorial erweitert das im Tutorial [Hinzufügen von Paginierung zu Suchergebnissen mithilfe des .NET SDK](tutorial-csharp-paging.md) erstellte Projekt zum unendlichen Scrollen.

Eine fertige Version des Codes in diesem Tutorial finden Sie im folgenden Projekt:

* [5-order-results (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Voraussetzungen

* Lösung [2b-add-infinite-scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll): Dieses Projekt kann entweder Ihre eigene im vorherigen Tutorial erstellte Version oder eine Kopie von GitHub sein.

Dieses Tutorial wurde aktualisiert, um das Paket [Azure.Search.Documents (Version 11)](https://www.nuget.org/packages/Azure.Search.Documents/) verwenden zu können. Eine frühere Version des .NET SDK finden Sie im [Codebeispiel Microsoft.Azure.Search (Version 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="order-results-based-on-one-property"></a>Sortieren von Ergebnissen basierend auf einer Eigenschaft

Wenn wir Ergebnisse basierend auf einer Eigenschaft (z. B. Hotelbewertung) anordnen, möchten wir nicht nur die geordneten Ergebnisse, sondern auch eine Bestätigung, dass die Reihenfolge korrekt ist. Durch das Hinzufügen des Bewertungsfelds zu den Ergebnissen können wir bestätigen, dass die Ergebnisse richtig sortiert wurden.

In dieser Übung werden wir auch die Anzeige der Ergebnisse für jedes Hotel etwas erweitern – um den günstigsten Zimmerpreis und den teuersten Zimmerpreis.

Zum Aktivieren der Sortierung muss keines der Modelle geändert werden. Nur die Ansicht und der Controller müssen aktualisiert werden. Öffnen Sie zunächst den Home-Controller.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Hinzufügen der OrderBy-Eigenschaft zu den Suchparametern

1. Fügen Sie die **OrderBy**-Option dem Namen der Eigenschaft hinzu. Fügen Sie in der Methode **Index(SearchData model)** den Suchparametern die folgende Zeile hinzu.

    ```cs
    OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Die Standardreihenfolge ist aufsteigend, aber Sie können der Eigenschaft **asc** hinzufügen, um dies deutlich zu machen. Die absteigende Reihenfolge wird durch Hinzufügen von **desc** festgelegt.

1. Führen Sie nun die App aus, und geben Sie einen beliebigen Suchbegriff ein. Die Ergebnisse können in der richtigen Reihenfolge sein oder auch nicht, da weder Sie als Entwickler noch der Benutzer eine einfache Möglichkeit haben, die Ergebnisse zu überprüfen!

1. Lassen Sie uns deutlich machen, dass die Ergebnisse nach der Bewertung geordnet sind. Ersetzen Sie zunächst die Klassen **box1** und **box2** in der Datei „hotels.css“ durch die folgenden Klassen (diese Klassen sind alle neuen, die wir für dieses Tutorial benötigen).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > CSS-Dateien werden von Browsern in der Regel zwischengespeichert, was dazu führen kann, dass eine alte CSS-Datei verwendet wird und Ihre Änderungen ignoriert werden. Eine gute Möglichkeit, dies zu umgehen, besteht darin, dem Link eine Abfragezeichenfolge mit einem Versionsparameter hinzuzufügen. Beispiel:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Aktualisieren Sie die Versionsnummer, wenn Sie glauben, dass eine alte CSS-Datei von Ihrem Browser verwendet wird.

1. Fügen Sie die Eigenschaft **Rating** dem Parameter **Select** in der Methode **Index(SearchData model)** hinzu.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

1. Öffnen Sie die Ansicht (index.cshtml), und ersetzen Sie die Renderingschleife ( **&lt;!-- Show the hotel data. --&gt;** ) durch den folgenden Code.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

1. Die Bewertung muss sowohl auf der ersten angezeigten Seite als auch auf den folgenden Seiten verfügbar sein, die über unendliches Scrollen aufgerufen werden. Für die letztgenannte dieser beiden Situationen müssen wir sowohl die Aktion **Next** im Controller als auch die Funktion **scrolled** in der Ansicht aktualisieren. Ändern Sie zunächst im Controller die Methode **Next** in den folgenden Code. Dieser Code erstellt und kommuniziert den Bewertungstext.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Aktualisieren Sie nun die Funktion **scrolled** in der Ansicht, um den Bewertungstext anzuzeigen.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

1. Führen Sie die App nun erneut aus. Suchen Sie nach einem gängigen Begriff wie „WiFi“ und vergewissern Sie sich, dass die Ergebnisse in absteigender Reihenfolge nach Hotelbewertung geordnet sind.

    ![Sortierung basierend auf Bewertung](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Sie werden feststellen, dass mehrere Hotels eine identische Bewertung haben, so dass ihr Erscheinen in der Anzeige wieder die Reihenfolge ist, in der die Daten gefunden werden, und die beliebig ist.

    Bevor wir uns mit dem Hinzufügen einer zweiten Ebene der Sortierung befassen, fügen wir noch Code hinzu, um den Bereich der Zimmerpreise anzuzeigen. Wir fügen diesen Code hinzu, um die Extraktion von Daten aus einem _komplexen Typ_ zu zeigen, und damit wir das Sortieren von Ergebnissen basierend dem Preis (vielleicht der günstigste zuerst) erörtern können.

### <a name="add-the-range-of-room-rates-to-the-view"></a>Hinzufügen des Zimmerpreisbereichs zur Ansicht

1. Fügen Sie dem Modell „Hotel.cs“ Eigenschaften mit den günstigsten und teuersten Zimmerpreisen hinzu.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

1. Berechnen Sie die Zimmerpreise am Ende der Aktion **Index(SearchData model)** im Home-Controller. Fügen Sie die Berechnungen nach dem Speichern von temporären Daten hinzu.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

1. Fügen Sie die Eigenschaft **Rooms** dem Parameter **Select** in der Aktionsmethode **Index(SearchData model)** des Controllers hinzu.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

1. Ändern Sie die Renderingschleife in der Ansicht, um den Preisbereich für die erste Seite der Ergebnisse anzuzeigen.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

1. Ändern Sie die Methode **Next** im Home-Controller, um den Preisbereich für nachfolgende Ergebnisseiten zu kommunizieren.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Aktualisieren Sie die Funktion **scrolled** in der Ansicht, um den Text der Zimmerpreise zu bearbeiten.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

1. Führen Sie die App aus, und vergewissern Sie sich, dass die Zimmerpreisbereiche angezeigt werden.

    ![Anzeigen von Zimmerpreisbereichen](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

Die **OrderBy**-Eigenschaft der Suchparameter akzeptiert keinen Eintrag wie **Rooms.BaseRate**, um den günstigsten Zimmerpreis bereitzustellen, auch wenn die Zimmer bereits nach Preis sortiert waren. In diesem Fall werden die Zimmer nicht nach Preis sortiert. Um Hotels im Beispieldatensatz nach Zimmerpreis sortiert anzuzeigen, müssten Sie die Ergebnisse in Ihrem Home-Controller sortieren und diese Ergebnisse in der gewünschten Reihenfolge an die Ansicht senden.

## <a name="order-results-based-on-multiple-values"></a>Sortieren von Ergebnissen basierend auf mehreren Werten

Die Frage ist nun, wie zwischen Hotels mit gleicher Bewertung unterschieden wird. Eine gute Methode wäre die Sortierung auf Grundlage der letzten Renovierung des Hotels. Mit anderen Worten, je neuer die Renovierung des Hotels ist, desto höher erscheint es in den Ergebnissen.

1. Zum Hinzufügen einer zweiten Sortierebene ändern Sie die Eigenschaften **OrderBy** und **Select** in der Methode **Index(SearchData model)** , um die Eigenschaft **LastRenovationDate** aufzunehmen.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >In die Liste **OrderBy** können beliebig viele Eigenschaften eingegeben werden. Wenn Hotels die gleiche Bewertung und das gleiche Renovierungsdatum hätten, könnte eine dritte Eigenschaft zur Unterscheidung eingegeben werden.

1. Auch hier müssen wir das Renovierungsdatum in der Ansicht sehen, nur um sicherzugehen, dass die Reihenfolge korrekt ist. Für eine Eigenschaft wie Renovierung wird wahrscheinlich nur das Jahr benötigt. Ändern Sie die Renderingschleife in der Ansicht in den folgenden Code.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

1. Ändern Sie die Methode **Next** im Home-Controller, um die Jahreskomponente des letzten Renovierungsdatums weiterzuleiten.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Ändern Sie die Funktion **scrolled** in der Ansicht, um den Renovierungstext anzuzeigen.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

1. Führen Sie die App aus. Suchen Sie nach einem gängigen Begriff wie „Pool“ oder „Aussicht“, und vergewissern Sie sich, dass Hotels mit der gleichen Bewertung nun in absteigender Reihenfolge des Renovierungsdatums angezeigt werden.

    ![Sortieren nach Renovierungsdatum](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtern von Ergebnissen basierend auf der Entfernung von einem geografischen Punkt

Bewertung und Renovierungsdatum sind Beispiele für Eigenschaften, die am besten in absteigender Reihenfolge angezeigt werden. Eine alphabetische Auflistung wäre ein Beispiel für eine gute Verwendung der aufsteigenden Reihenfolge (z. B. wenn es nur eine **OrderBy**-Eigenschaft gäbe und diese auf **HotelName** festgelegt wäre, dann würde eine alphabetische Reihenfolge angezeigt). Für unsere Beispieldaten wäre jedoch die Entfernung von einem geografischen Punkt besser geeignet.

Zum Anzeigen von Ergebnissen basierend auf der geografischen Entfernung sind mehrere Schritte erforderlich.

1. Filtern Sie alle Hotels heraus, die sich außerhalb eines bestimmten Radius um den angegebenen Punkt befinden, indem Sie einen Filter mit Längengrad-, Breitengrad- und Radiusparametern eingeben. Der Längengrad wird zuerst an die POINT-Funktion übergeben. Der Radius ist in Kilometern angegeben.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

1. Mit dem obigen Filter werden die Ergebnisse _nicht_ nach Entfernung geordnet, sondern nur die Ausreißer entfernt. Zum Sortieren der Ergebnisse geben Sie eine **OrderBy**-Einstellung ein, die die geoDistance-Methode angibt.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

1. Obwohl die Ergebnisse von Azure Cognitive Search mit einem Entfernungsfilter zurückgegeben wurden, wird die berechnete Entfernung zwischen den Daten und dem angegebenen Punkt _nicht_ zurückgegeben. Berechnen Sie diesen Wert in der Ansicht oder im Controller neu, wenn Sie ihn in den Ergebnissen anzeigen möchten.

    Der folgende Code berechnet die Entfernung zwischen zwei Lat-/Lng-Punkten.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

1. Nun müssen Sie diese Konzepte miteinander verbinden. Unser Tutorial behandelt jedoch nur diese Codeausschnitte, die Erstellung einer kartenbasierten App wird dem Leser als Übung überlassen. Um dieses Beispiel weiter zu vertiefen, sollten Sie entweder einen Städtenamen mit einem Radius eingeben oder einen Punkt auf einer Karte lokalisieren und einen Radius auswählen. Um diese Optionen weiter zu erkunden, beachten Sie die folgenden Ressourcen:

* [Dokumentation zu Azure Location Based Services (Vorschauversion)](../azure-maps/index.yml)
* [Suchen nach einer Adresse mit dem Suchdienst von Azure Maps](../azure-maps/how-to-search-for-address.md)

## <a name="order-results-based-on-a-scoring-profile"></a>Sortieren von Ergebnissen basierend auf einem Bewertungsprofil

Die bisherigen Beispiele im Tutorial zeigen, wie man nach Zahlenwerten (Bewertung, Renovierungsdatum, geografische Entfernung) ordnet, was einen _genauen_ Sortierprozess bietet. Einige Suchen und einige Daten bieten sich jedoch nicht für einen solch einfachen Vergleich zwischen zwei Datenelementen an. Azure Cognitive Search beinhaltet das Konzept von _Bewertung_. _Bewertungsprofile_ können für einen Datensatz angegeben werden, der verwendet werden kann, um komplexere und qualitativere Vergleiche zu ermöglichen. Diese sollten am nützlichsten sein, wenn man beispielsweise textbasierte Daten vergleicht, um zu entscheiden, welche zuerst angezeigt werden sollen.

Bewertungsprofile werden nicht von Benutzern definiert, sondern in der Regel von Administratoren eines Datensatzes. Für die Hoteldaten wurden mehrere Bewertungsprofile erstellt. Wir sehen uns an, wie ein Bewertungsprofil definiert ist, und versuchen dann, Code zu schreiben, um nach ihm zu suchen.

### <a name="how-scoring-profiles-are-defined"></a>So werden Bewertungsprofile definiert

Betrachten wir drei Beispiele für Bewertungsprofile und überlegen uns, wie sich jedes einzelne davon auf die Reihenfolge der Ergebnisse auswirken _sollte_. Als App-Entwickler schreiben Sie diese Profile nicht, sie werden vom Datenadministrator geschrieben. Es ist jedoch hilfreich, sich die Syntax anzusehen.

1. Dies ist das Standardbewertungsprofil für den Hoteldatensatz, der verwendet wird, wenn Sie keine **OrderBy**- oder **ScoringProfile**-Parameter angeben. Dieses Profil erhöht die _Bewertung_ für ein Hotel, wenn der Suchtext im Hotelnamen, in der Beschreibung oder in der Liste der Tags (Ausstattung) enthalten ist. Beachten Sie, wie bestimmte Felder von den Gewichtungen der Bewertung bevorzugt werden. Wenn der Suchtext in einem anderen Feld angezeigt wird, das unten nicht aufgeführt ist, hat er die Gewichtung 1. Je höher die Bewertung, desto eher wird ein Ergebnis natürlich in der Ansicht angezeigt.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

1. Das folgende Bewertungsprofil steigert die Bewertung erheblich, wenn ein übergebener Parameter eines oder mehrere der Tags (die wir als „Ausstattungen“ bezeichnen) enthält. Der wichtigste Aspekt dieses Profils ist, dass ein Parameter mit Text angegeben werden _muss_. Wenn der Parameter leer oder nicht angegeben ist, wird ein Fehler ausgegeben.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

1. In diesem dritten Beispiel bedeutet die Bewertung eine deutliche Steigerung der Punktzahl. Das Datum der letzten Renovierung erhöht auch die Punktzahl, allerdings nur, wenn diese Daten innerhalb von 730 Tagen (2 Jahren) ab dem aktuellen Datum liegen.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Nun lassen Sie uns sehen, ob diese Profile so funktionieren, wie wir es für richtig halten!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Hinzufügen von Code zur Ansicht, um Profile zu vergleichen

1. Öffnen Sie die Datei „index.cshtml“, und ersetzen Sie den Abschnitt &lt;body&gt; durch den folgenden Code.

    ```html
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. Öffnen Sie die Datei „SearchData.cs“, und ersetzen Sie die Klasse **SearchData** durch den folgenden Code.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Öffnen Sie die Datei „hotels.css“, und fügen Sie die folgenden HTML-Klassen hinzu.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Hinzufügen von Code zum Controller, um ein Bewertungsprofil festzulegen

1. Öffnen Sie die Home-Controller-Datei. Fügen Sie die folgende **using**-Anweisung hinzu (zur Unterstützung bei der Erstellung von Listen).

    ```cs
    using System.Linq;
    ```

1. Für dieses Beispiel benötigen wir den ersten Aufruf von **Index**, um etwas mehr zu tun, als nur die ursprüngliche Ansicht zurückzugeben. Das Verfahren sucht nun nach bis zu 20 Ausstattungen, die in der Ansicht angezeigt werden sollen.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. Wir benötigen zwei private Methoden, um die Facetten im Zwischenspeicher zu speichern und sie aus dem Zwischenspeicher wiederherzustellen und ein Modell auszufüllen.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. Wir müssen die Parameter **OrderBy** und **ScoringProfile** nach Bedarf festlegen. Ersetzen Sie die vorhandene Methode **Index(SearchData model)** durch die folgende.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Lesen Sie die Kommentare für jede der **switch**-Auswahlen durch.

5. Wir müssen keine Änderungen an der Aktion **Next** vornehmen, wenn Sie den zusätzlichen Code für den vorherigen Abschnitt über die Sortierung basierend mehreren Eigenschaften abgeschlossen haben.

### <a name="run-and-test-the-app"></a>Ausführen und Testen der App

1. Führen Sie die App aus. In der Ansicht sollte eine umfassende Reihe von Ausstattungen angezeigt werden.

1. Wenn Sie für die Sortierung „By numerical Rating“ (Nach numerischer Bewertung) auswählen, erhalten Sie die numerische Reihenfolge, die Sie bereits in diesem Tutorial implementiert haben, wobei unter den Hotels mit gleicher Bewertung das Renovierungsdatum entscheidend ist.

   ![Sortierung von „beach“ (Strand) basierend auf Bewertung](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Probieren Sie nun das Profil „By amenities“ (Nach Ausstattung) aus. Treffen Sie verschiedene Auswahlen von Ausstattungen, und überprüfen Sie, ob Hotels mit diesen Ausstattung auf der Ergebnisliste höhergestuft werden.

   ![Sortierung von „beach“ (Strand) basierend auf Profil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Probieren Sie das „By Renovated date/Rating profile“ (nach Renovierungsdatum/Bewertungsprofil) aus, um zu sehen, ob Sie das erhalten, was Sie erwarten. Nur vor kurzem renovierte Hotels sollten bedingt durch _freshness_ (Aktualität) höhergestuft werden.

### <a name="resources"></a>Ressourcen

Weitere Informationen finden Sie unter [Hinzufügen von Bewertungsprofilen zu einem Azure Cognitive Search-Index](./index-add-scoring-profiles.md).

## <a name="takeaways"></a>Wesentliche Punkte

Beachten Sie die folgenden Erkenntnisse aus diesem Projekt:

* Benutzer erwarten, dass die relevantesten Suchergebnisse zuerst angezeigt werden.
* Die Daten müssen strukturiert sein, damit die Sortierung einfach ist. Wir konnten zuerst nicht problemlos nach „am billigsten“ sortieren, da die Daten nicht so strukturiert sind, dass die Sortierung ohne zusätzlichen Code erfolgen kann.
* Die Sortierung kann mehrere Ebenen aufweisen, um auf einer höheren Sortierebene zwischen Ergebnissen zu unterscheiden, die einen gleichen Wert haben.
* Für einige Ergebnisse ist die Sortierung in aufsteigender Reihenfolge selbstverständlich (z. B. Entfernung von einem Punkt), für andere in absteigender Reihenfolge (z. B. Gästebewertung).
* Bewertungsprofile können definiert werden, wenn numerische Vergleiche für einen Datensatz nicht verfügbar oder nicht intelligent genug sind. Die Bewertung der einzelnen Ergebnisse trägt dazu bei, die Ergebnisse intelligent zu ordnen und anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie diese Reihe von C#-Tutorials abgeschlossen haben, sollten Sie nun über wertvolle Kenntnisse über die Azure Cognitive Search-APIs verfügen.

Weitere Informationen und Tutorials finden Sie unter [Microsoft Learn](/learn/browse/?products=azure) oder in anderen Tutorials in der [Azure Cognitive Search-Dokumentation](./index.yml).