---
title: Was ist die Formularerkennung?
titleSuffix: Azure Cognitive Services
description: Der Azure-Formularerkennungsdienst ermöglicht das Erkennen und Extrahieren von Schlüssel-Wert-Paaren und Tabellendaten aus Ihren Formulardokumenten sowie das Extrahieren wichtiger Informationen aus Verkaufsbelegen und Visitenkarten.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: automatisierte Datenverarbeitung, Dokumentverarbeitung, automatisierte Dateneingabe, Formularverarbeitung
ms.openlocfilehash: 2d3a37165994a8c7ff9d46ac24e63b7f58404ce3
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584565"
---
# <a name="what-is-form-recognizer"></a>Was ist die Formularerkennung?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Die Azure-Formularerkennung ist ein kognitiver Dienst, mit dem Sie unter Verwendung von Technologie für maschinelles Lernen Software für die automatisierte Datenverarbeitung entwickeln können. Identifizieren und extrahieren Sie Text, Schlüssel-Wert-Paare, Auswahlmarkierungen, Tabellen und die Struktur aus Ihren Dokumenten. Der Dienst gibt strukturierte Daten aus, die unter anderem die Beziehungen in der ursprünglichen Datei sowie Begrenzungsrahmen und Konfidenz enthalten. Sie können schnell präzise und auf Ihre spezifischen Inhalte zugeschnittene Ergebnisse erzielen, ohne dass komplizierte manuelle Eingriffe oder umfangreiche Data Science-Kenntnisse erforderlich sind. Verwenden Sie die Formularerkennung, um Dateneingaben in Ihren Anwendungen zu automatisieren und Ihre Dokumentsuchfunktionen zu erweitern.

Die Formularerkennung setzt sich aus benutzerdefinierten Dokumentverarbeitungsmodellen, vordefinierten Modellen für Rechnungen, Belege und Visitenkarten und dem Layoutmodell zusammen. Sie können Formularerkennungsmodelle mithilfe einer REST-API oder über Clientbibliothek-SDKs aufrufen, um die Komplexität zu reduzieren und sie in Ihren Workflow oder Ihre Anwendung zu integrieren.

Die Formularerkennung umfasst folgende Dienste:
* **[Layout-API:](#layout-api)** Dient zum Extrahieren von Text, Auswahlmarkierungen und Tabellenstrukturen sowie der zugehörigen Begrenzungsrahmenkoordinaten aus Dokumenten.
* **[Benutzerdefinierte Modelle:](#custom-models)** Dienen zum Extrahieren von Text, Schlüssel-Wert-Paaren, Auswahlmarkierungen und Tabellendaten aus Formularen. Diese Modelle werden mit Ihren eigenen Daten trainiert, sodass Sie auf Ihre Formulare zugeschnitten sind.
* **[Vordefinierte Modelle:](#prebuilt-models)** Dienen zum Extrahieren eindeutiger Formulartypen mithilfe vordefinierter Modelle. Aktuell sind folgende vordefinierte Modelle verfügbar:
    * [Invoices](./concept-invoices.md)
    * [Verkaufsbelege](./concept-receipts.md)
    * [Visitenkarten](./concept-business-cards.md)


## <a name="try-it-out"></a>Ausprobieren

Wenn Sie den Formularerkennungsdienst ausprobieren möchten, wechseln Sie zum Benutzeroberflächentool für Onlinebeispiele:


# <a name="v20"></a>[v2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [Formularerkennung ausprobieren](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)
> [!div class="nextstepaction"]
> [Formularerkennung ausprobieren](https://fott-preview.azurewebsites.net/)

---

Zum Ausprobieren des Formularerkennungsdiensts benötigen Sie ein Azure-Abonnement (kann [hier](https://azure.microsoft.com/free/cognitive-services) kostenlos erstellt werden), einen Endpunkt für eine [Formularerkennungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) und einen entsprechenden Schlüssel.

## <a name="layout-api"></a>Layout-API

Die Formularerkennung kann Text, Auswahlmarkierungen und die Tabellenstruktur (die Zeilen- und Spaltennummern, die dem Text zugeordnet sind) aus Dokumenten extrahieren. Hierzu kommen eine hochauflösende optische Zeichenerkennung (Optical Character Recognition, OCR) und ein erweitertes Deep Learning-Modell zum Einsatz. Weitere Informationen finden Sie im [Konzeptleitfaden für das Layout](./concept-layout.md).

:::image type="content" source="./media/tables-example.jpg" alt-text="Tabellenbeispiel" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Benutzerdefinierte Modelle

Benutzerdefinierte Formularerkennungsmodelle werden mit Ihren eigenen Daten trainiert, und Sie benötigen zu Beginn nur fünf Beispieleingabeformulare. Ein trainiertes Dokumentverarbeitungsmodell kann strukturierte Daten ausgeben, die die Beziehungen in der Originaldatei enthalten. Nachdem das Modell trainiert wurde, können Sie es testen, neu trainieren und schließlich verwenden, um Daten aus weiteren Formularen zuverlässig nach Ihren Bedürfnissen zu extrahieren.

Beim Trainieren von benutzerdefinierten Modellen haben Sie die folgenden Optionen: Training mit beschrifteten Daten und ohne beschriftete Daten.

### <a name="train-without-labels"></a>Trainieren ohne Beschriftungen

Standardmäßig verwendet die Formularerkennung nicht überwachtes Lernen, um das Layout und die Beziehungen zwischen Feldern und Einträgen in Ihren Formularen nachzuvollziehen. Anhand der übermittelten Eingabeformulare gruppiert der Algorithmus die Formulare nach Typ, erkennt, welche Schlüssel und Tabellen vorhanden sind, und ordnet Schlüsseln Werte und Tabellen Einträge zu. Hierfür ist keine manuelle Datenbeschriftung oder intensive Codierung und Wartung erforderlich, und Sie sollten diese Methode zuerst testen.

Tipps zum Sammeln von Trainingsdokumenten finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](./build-training-data-set.md).

### <a name="train-with-labels"></a>Trainieren mit Beschriftungen

Wenn Sie mit beschrifteten Daten trainieren, extrahiert das Modell relevante Werte mit überwachtem Lernen, wobei die von Ihnen bereitgestellten beschrifteten Formulare verwendet werden. Dies führt zu Modellen mit besserer Leistung und kann Modelle hervorbringen, die mit komplexen Formularen oder Formularen arbeiten, die Werte ohne Schlüssel enthalten.

Die Formularerkennung verwendet die [Layout-API](#layout-api), um die erwarteten Größen und Positionen von gedruckten und handschriftlichen Textelementen zu erlernen. Anschließend werden benutzerdefinierte Beschriftungen verwendet, um die Schlüssel-Wert-Zuordnungen in den Dokumenten zu erlernen. Wir empfehlen, fünf manuell beschriftete Formulare gleichen Typs (gleiche Struktur) zu verwenden, um mit dem Trainieren eines neuen Modells zu beginnen, und weitere beschriftete Daten nach Bedarf hinzuzufügen, um die Modellgenauigkeit zu verbessern.

[Trainieren eines Formularerkennungsmodells mit Beschriftungen mithilfe des Tools für die Beschriftung von Beispielen](./quickstarts/label-tool.md)


> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


## <a name="prebuilt-models"></a>Vordefinierte Modelle

Die Formularerkennung enthält außerdem vordefinierte Modelle zur automatisierten Datenverarbeitung eindeutiger Formulartypen.

### <a name="prebuilt-invoice-model"></a>Vordefiniertes Rechnungsmodell
Das vordefinierte Rechnungsmodell extrahiert Daten aus Rechnungen in verschiedenen Formaten und gibt strukturierte Daten zurück. Dieses Modell extrahiert wichtige Informationen wie Rechnungs-ID, Kundendetails, Anbieterdetails, Lieferadresse, Rechnungsadresse, Summe, Steuer, Zwischensumme und Ähnliches. Darüber hinaus wird das vordefinierte Rechnungsmodell darauf trainiert, den gesamten Text und sämtliche Tabellen auf der Rechnung zu analysieren und zurückzugeben. Weitere Informationen finden Sie im [Konzeptleitfaden für Rechnungen](./concept-invoices.md).

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Beispielrechnung" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Vordefiniertes Belegmodell

Das vordefinierte Belegmodell wird zum Lesen englischsprachiger Verkaufsbelege aus Australien, Kanada, dem Vereinigten Königreich, Indien und den USA verwendet – des Typs, der von Restaurants, Tankstellen, Einzelhandel usw. verwendet wird. Dieses Modell extrahiert wichtige Informationen wie Zeitpunkt und Datum der Transaktion, Händlerinformationen, Steuer- und Summenbeträge, Einzelposten und mehr. Darüber hinaus wird das vorgefertigte Belegmodell dazu trainiert, den gesamten Text eines Belegs zu analysieren und zurückzugeben. Weitere Informationen finden Sie im Leitfaden [Konzepte zu Belegen](./concept-receipts.md).

:::image type="content" source="./media/overview-receipt.jpg" alt-text="Beispielbeleg" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-business-cards-model"></a>Vordefiniertes Visitenkartenmodell

Mit dem Visitenkartenmodell können Sie Informationen wie Name, Position, Adresse, E-Mail-Adresse, Unternehmen und Telefonnummern einer Person aus englischsprachigen Visitenkarten extrahieren. Weitere Informationen finden Sie im Leitfaden [Konzepte zu Visitenkarten](./concept-business-cards.md).

:::image type="content" source="./media/overview-business-card.jpg" alt-text="Beispielvisitenkarte" lightbox="./media/overview-business-card.jpg":::


## <a name="get-started"></a>Erste Schritte

Verwenden Sie das [Beispieltool für die Formularerkennung](https://fott.azurewebsites.net/) oder eine Schnellstartanleitung, um sich mit den ersten Schritten beim Extrahieren von Daten aus Ihren Formularen vertraut zu machen. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.

* [Schnellstart zur Clientbibliothek/REST-API](./quickstarts/client-library.md) (alle Sprachen, mehrere Szenarien)
* Schnellstartanleitungen für Webbenutzeroberfläche
  * [Trainieren mit Beschriftungen: Tool für die Beschriftung von Beispielen](quickstarts/label-tool.md)
* REST-Beispiele (GitHub)
 * Extrahieren von Text, Auswahlmarkierungen und Tabellenstruktur aus Dokumenten
    * [Extrahieren von Layoutdaten: Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
  * Trainieren von benutzerdefinierten Modellen und Extrahieren von Formulardaten
    * [Trainieren ohne Beschriftungen: Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
    * [Trainieren mit Beschriftungen: Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
  * Extrahieren von Daten aus Rechnungen
    * [Extrahieren von Rechnungsdaten: Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
  * Extrahieren von Daten aus Verkaufsbelegen
    * [Extrahieren von Verkaufsbelegdaten: Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
  * Extrahieren von Daten aus Visitenkarten
    * [Extrahieren von Visitenkartendaten – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>Überprüfen der REST-APIs

Verwenden Sie die folgenden APIs zum Trainieren von Modellen und Extrahieren strukturierter Daten aus Formularen.

|Name |Beschreibung |
|---|---|
| **Analysieren des Layouts** | Analysieren Sie ein als Stream übergebenes Dokument, um Text, Auswahlmarkierungen, Tabellen und die Struktur aus dem Dokument zu extrahieren. |
| **Trainieren eines benutzerdefinierten Modells**| Trainieren eines neuen Modells zur Analyse Ihrer Formulare mit fünf Formularen gleichen Typs. Legen Sie den Parameter _useLabelFile_ auf `true` fest, um mit manuell beschrifteten Daten zu trainieren. |
| **Analysieren des Formulars** |Analysieren Sie ein als Stream übergebenes Formular, um Text, Schlüssel-Wert-Paare und Tabellen mit Ihrem benutzerdefinierten Modell aus dem Formular zu extrahieren.  |
| **Analysieren von Rechnungen** | Analysieren Sie eine Rechnung, um zentrale Informationen, Tabellen und anderen Rechnungstext zu extrahieren.|
| **Analysieren des Belegs** | Analysieren Sie ein Belegdokument, um zentrale Informationen und anderen Belegtext zu extrahieren.|
| **Analysieren von Visitenkarten** | Analysieren Sie eine Visitenkarte, um wichtige Informationen und Text zu extrahieren.|


# <a name="v20"></a>[v2.0](#tab/v2-0)
Sehen Sie sich die [Referenzdokumentation zur REST-API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) an, um mehr zu erfahren. Wenn Sie mit einer früheren Version der API vertraut sind, finden Sie im Artikel [Neuerungen](./whats-new.md) weitere Informationen zu den aktuellen Änderungen.

# <a name="v21"></a>[v2.1](#tab/v2-1)
Sehen Sie sich die [Referenzdokumentation zur REST-API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeWithCustomForm) an, um mehr zu erfahren. Wenn Sie mit einer früheren Version der API vertraut sind, finden Sie im Artikel [Neuerungen](./whats-new.md) weitere Informationen zu den aktuellen Änderungen.

---

## <a name="input-requirements"></a>Eingabeanforderungen

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Lokales Bereitstellen unter Verwendung von Docker-Containern

[Verwenden Sie Container für die Formularerkennung (Vorschauversion)](form-recognizer-container-howto.md), um API-Features lokal bereitzustellen. Mithilfe dieses Docker-Containers können Sie den Dienst näher an Ihre Daten heranbringen, um Compliance- oder Sicherheitsanforderungen zu erfüllen oder anderen betrieblichen Anforderungen gerecht zu werden. 

## <a name="service-availability-and-redundancy"></a>Dienstverfügbarkeit und Redundanz

### <a name="is-form-recognizer-service-zone-resilient"></a>Ist der Formularerkennungsdienst zonenresilient?

Ja. Der Formularerkennungsdienst ist standardmäßig zonenresilient.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Wie konfiguriere ich den Formularerkennungsdienst für Zonenresilienz?

Es ist keine Kundenkonfiguration erforderlich, um Zonenresilienz zu ermöglichen. Zonenresilienz für Formularerkennungsressourcen ist standardmäßig verfügbar und wird vom Dienst selbst verwaltet.


## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Formularerkennungsdienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

Durchlaufen Sie einen [Schnellstart](quickstarts/client-library.md) zu den ersten Schritten zum Schreiben einer Formularverarbeitungs-App mit der Formularerkennung in der Entwicklungssprache Ihrer Wahl.
