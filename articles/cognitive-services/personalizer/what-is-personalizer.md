---
title: Was ist die Personalisierung?
description: Die Personalisierung ist ein cloudbasierter Dienst, mit dem Sie die beste Benutzeroberfläche für Ihre Benutzer auswählen und dabei in Echtzeit von deren Verhalten lernen können.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: Personalisierung, Azure Personalisierung, Machine Learning
ms.openlocfilehash: 28aae130d062eaf57a66a9b90a6602c3874f1494
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094147"
---
# <a name="what-is-personalizer"></a>Was ist die Personalisierung?

Die Azure-Personalisierung ist ein cloudbasierter Dienst, mit dessen Hilfe Ihre Anwendungen für Benutzer den am besten geeigneten Inhalt anzeigen kann. Mit dem Personalisierungsdienst können Sie festlegen, welches Produkt Kunden vorgeschlagen werden soll, oder die optimale Position für eine Anzeige ermitteln. Nachdem der Inhalt für den Benutzer angezeigt wurde, überwacht das System das Echtzeitbenutzerverhalten und meldet dem Personalisierungsdienst eine Relevanzbewertung. Dies trägt zur kontinuierlichen Verbesserung des ML-Modells bei und unterstützt die Personalisierung bei der Wahl des besten Inhaltselements auf der Grundlage der empfangenen Kontextinformationen.

> [!TIP]
> Der Inhalt ist eine beliebige Informationseinheit, z. B. Text, Bilder, URLs, E-Mails oder andere Elemente, die zum Treffen einer Auswahl zum Anzeigen für Ihre Benutzer genutzt werden soll.

Bevor Sie beginnen, können Sie die [Personalisierung mit dieser interaktiven Demo](https://personalizationdemo.azurewebsites.net/) ausprobieren.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Wie wählt die Personalisierung das beste Inhaltselement aus?

Für die Personalisierung wird das **vertiefende Lernen** verwendet, um anhand des gesamten Verhaltens und der Relevanzbewertungen aller Benutzer das beste Element (_Aktion_) auswählen zu können. Aktionen sind die Inhaltselemente, z. B. Nachrichtenartikel, bestimmte Filme oder Produkte.

Beim **Rangfolge**-Aufruf werden das Aktionselement, einschließlich der zugehörigen Features, und die Kontextfeatures verwendet, um das oberste Aktionselement auszuwählen:

* **Aktionen mit Features**: Inhaltselemente mit spezifischen Features für jedes Element.
* **Kontextfeatures**: Features Ihrer Benutzer, des Kontexts oder der Umgebung bei der Nutzung Ihrer App.

Im Feld für die **Relevanzaktion-ID** gibt der Rangfolge-Aufruf die ID dazu zurück, welches Inhaltselement (__Aktion__) dem Benutzer angezeigt werden soll.

Die dem Benutzer angezeigte __Aktion__ wird mithilfe von Machine Learning-Modellen ausgewählt, um zu versuchen, die Relevanz im Laufe der Zeit möglichst stark zu erhöhen.

### <a name="sample-scenarios"></a>Beispielszenarios

Werfen Sie einen Blick auf einige Szenarien, in denen die Personalisierung dazu verwendet werden kann, die besten Inhalte zur Anzeige für einen Benutzer auszuwählen.

|Inhaltstyp|Aktionen (mit Features)|Kontextfeatures|Zurückgegebene Relevanzaktion-ID<br>(anzuzeigender Inhalt)|
|--|--|--|--|
|Liste mit News|a. `The president...` (Inland, Politik, [Text])<br>b. `Premier League ...` (Weltweit, Sport, [Text, Bild, Video])<br> c. `Hurricane in the ...` (Regional, Wetter, [Text, Bild]|Gerät, von dem News gelesen werden<br>Monat oder Saison<br>|a `The president...`|
|Liste mit Filmen|1. `Star Wars` (1977, [Aktion, Abenteuer, Fantasy], George Lucas)<br>2. `Hoop Dreams` (1994, [Dokumentation, Sport], Steve James<br>3. `Casablanca` (1942, [Romanze, Drama, Krieg], Michael Curtiz)|Gerät, auf dem der Film angeschaut wird<br>Displaygröße<br>Typ des Benutzers<br>|3. `Casablanca`|
|Produktliste|i. `Product A` (3 kg, $$$$, Lieferung innerhalb von 24 Stunden)<br>ii. `Product B` (20 kg, $$, 2 Wochen Lieferzeit mit Zoll)<br>iii. `Product C` (3 kg, $$$, Lieferung innerhalb von 48 Stunden)|Gerät, über das der Einkauf erfolgt<br>Ausgabenebene des Benutzers<br>Monat oder Saison|ii. `Product B`|

Für die Personalisierung wird das vertiefende Lernen genutzt, um die beste Aktion auszuwählen. Dazu wird eine sogenannte _Relevanzaktion-ID_ verwendet. Das Machine Learning-Modell verwendet Folgendes: 

* Ein trainiertes Modell: Zuvor vom Personalisierungsdienst empfangene Informationen, die zum Verbessern des Machine Learning-Modells verwendet werden
* Aktuelle Daten: Spezifische Aktionen mit Features und Kontextfeatures

## <a name="when-to-use-personalizer"></a>Verwendung der Personalisierung

Die **Rangfolge**-[API](https://go.microsoft.com/fwlink/?linkid=2092082) der Personalisierung wird jedes Mal aufgerufen, wenn Ihre Anwendung Inhalte darstellt. Dies wird als **Ereignis** bezeichnet und durch eine _Ereignis-ID_ gekennzeichnet.

Die **Relevanz**-[API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) der Personalisierung kann in Echtzeit oder verzögert aufgerufen werden, um die jeweils am besten geeignete Anpassung an Ihre Infrastruktur zu erzielen. Sie bestimmen die Relevanzbewertung basierend auf Ihren Geschäftsanforderungen. Die Relevanzbewertung liegt zwischen 0 und 1. Dies kann ein einzelner Wert sein, z. B. „1“ für „Gut“ und „0“ für „Schlecht“. Sie können auch eine Zahl verwenden, die von einem Algorithmus ermittelt wird, den Sie anhand Ihrer Geschäftsziele und Metriken erstellt haben.

## <a name="content-requirements"></a>Inhaltsanforderungen

Verwenden Sie die Personalisierung, wenn für Ihren Inhalt Folgendes gilt:

* Verfügt über eine begrenzte Anzahl von Elementen (max. 50), die ausgewählt werden können. Bei einer längeren Liste sollten Sie eine [Empfehlungs-Engine verwenden](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution), um die Liste auf 50 Elemente zu reduzieren.
* Enthält Informationen zur Beschreibung des Inhalts, für den die Rangfolge nach Relevanz erstellt werden soll: _Aktionen mit Features_ und _Kontextfeatures_.
* Verfügt über mindestens 1.000 inhaltsbezogene Ereignisse pro Tag, damit die Personalisierung effektiv ist. Wenn die Personalisierung nicht den mindestens benötigten Datenverkehr erhält, dauert es länger, bis der Dienst das beste Inhaltselement ermittelt hat.

Da für die Personalisierung die gesamten Informationen nahezu in Echtzeit genutzt werden, um das beste Inhaltselement zurückgeben zu können, wird vom Dienst Folgendes nicht durchgeführt:
* Speichern und Verwalten von Informationen zum Benutzerprofil
* Protokollieren von Einstellungen oder des Verlaufs einzelner Benutzer
* Erzwingen von bereinigtem Inhalt mit Bezeichnungen

## <a name="how-to-design-for-and-implement-personalizer"></a>Entwerfen für die und Implementieren der Personalisierung

1. Führen Sie das [Entwerfen](concepts-features.md) und Planen für Inhalt, **_Aktionen_** und **_Kontext_** durch. Ermitteln Sie den Relevanzalgorithmus für die Bewertung der **_Relevanz_**.
1. Jede von Ihnen erstellte [Personalisierungsressource](how-to-settings.md) wird als gesonderte Lernschleife angesehen. Die Schleife empfängt sowohl jeweils den Rangfolge- als auch den Relevanz-Aufruf für diesen Inhalt bzw. die Benutzererfahrung.

    |Ressourcentyp| Zweck|
    |--|--|
    |[Ausbildungsmodus](concept-apprentice-mode.md) `E0`|Trainieren des Personalisierungsmodells ohne Beeinträchtigung der vorhandenen Anwendung und anschließendes Bereitstellen für das Onlinelernverhalten in einer Produktionsumgebung|
    |Standard, `S0`|Onlinelernverhalten in einer Produktionsumgebung|
    |Free, `F0`| Ausprobieren des Onlinelernverhaltens in einer Nichtproduktionsumgebung|

1. Fügen Sie die Personalisierung Ihrer Anwendung, der Website oder dem System hinzu:
    1. Fügen Sie der Personalisierung in Ihrer Anwendung, der Website oder dem System einen **Rangfolge**-Aufruf hinzu, um das am besten geeignete _Inhaltselement_ zu ermitteln, bevor der Inhalt dem Benutzer angezeigt wird.
    1. Zeigen Sie dem Benutzer das am besten geeignete _Inhaltselement_ an, das anhand der zurückgegebenen _Relevanzaktion-ID_ gekennzeichnet ist.
    1. Wenden Sie die _Geschäftslogik_ auf die gesammelten Informationen an, die das Verhalten des Benutzers angeben, um die **Relevanzbewertung** zu ermitteln. Beispiel:

    |Verhalten|Berechnete Relevanzbewertung|
    |--|--|
    |Benutzer hat das am besten geeignete _Inhaltselement_ (Relevanzaktion-ID) ausgewählt|**1**|
    |Benutzer hat anderen Inhalt ausgewählt|**0**|
    |Benutzer hat innegehalten und zunächst gescrollt, bevor er das am besten geeignete _Inhaltselement_ (Relevanzaktion-ID) ausgewählt hat|**0,5**|

    1. Fügen Sie einen **Relevanz**-Aufruf hinzu, bei dem eine Relevanzbewertung zwischen 0 und 1 gesendet wird.
        * Unmittelbar nach dem Anzeigen Ihrer Inhalte
        * Oder später in einem Offlinesystem
    1. [Werten Sie Ihre Schleife aus](concepts-offline-evaluation.md), indem Sie nach einer gewissen Nutzungsdauer eine Offlineauswertung durchführen. Bei der Offlineauswertung können Sie die Wirksamkeit des Personalisierungsdiensts testen und bewerten, ohne Ihren Code zu ändern oder die Benutzererfahrung zu beeinträchtigen.

## <a name="complete-a-quickstart"></a>Abschließen einer Schnellstartanleitung

Wir stellen Schnellstartanleitungen in C#, JavaScript und Python bereit. Jede Schnellstartanleitung enthält Informationen zu grundlegenden Entwurfsmustern und ist jeweils so konzipiert, dass Sie in weniger als zehn Minuten über ausführbaren Code verfügen. 

* [Schnellstart: Personalisierungsclientbibliothek](./quickstart-personalizer-sdk.md)

Nachdem Sie Gelegenheit hatten, erste Schritte mit dem Personalisierungsdienst auszuführen, erfahren Sie in unseren Tutorials mehr dazu, wie Sie die Personalisierung in Webanwendungen, Chatbots oder in einer Azure Notebooks-Instanz verwenden.

* [Tutorial: Verwenden der Personalisierung in einer .NET-Web-App](tutorial-use-personalizer-web-app.md)
* [Tutorial: Verwenden der Personalisierung in einem .NET-Chatbot](tutorial-use-personalizer-chat-bot.md)
* [Tutorial: Verwenden der Personalisierung in Azure Notebook](tutorial-use-azure-notebook-generate-loop-data.md)

## <a name="reference"></a>Verweis 

* [Personalisierung: C#/.NET SDK](/dotnet/api/overview/azure/cognitiveservices/client/personalizer?view=azure-dotnet)
* [Personalisierung: Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview)
* [Personalisierung: JavaScript SDK](/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)
* [Personalisierung: Python SDK](/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python)
* [REST-APIs](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Funktionsweise der Personalisierung](how-personalizer-works.md)
> [Was ist vertiefendes Lernen?](concepts-reinforcement-learning.md)