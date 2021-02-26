---
title: 'Schnellstart: Clientbibliothek oder REST-API für die Formularerkennung'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Clientbibliothek oder die REST-API für die Formularerkennung, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: Formularverarbeitung, automatisierte Datenverarbeitung
ms.openlocfilehash: d468f40d9de7fd7efb4ac7d8021a667e0c6c31f6
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584607"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>Schnellstart: Verwenden der Clientbibliothek oder REST-API für die Formularerkennung

Hier finden Sie Informationen zu den ersten Schritten mit der Formularerkennung in der Entwicklungssprache Ihrer Wahl. Die Azure-Formularerkennung ist ein kognitiver Dienst, mit dem Sie unter Verwendung von Technologie für maschinelles Lernen Software für die automatisierte Datenverarbeitung entwickeln können. Identifizieren und extrahieren Sie Schlüssel-Wert-Paare, Auswahlmarkierungen, Tabellendaten und Ähnliches aus Formulardokumenten. Der Dienst gibt strukturierte Daten aus, die auch die Beziehungen in der ursprünglichen Datei umfassen. Die Formularerkennung kann per REST-API oder per SDK verwendet werden. Führen Sie die nachfolgenden Schritte zum Installieren des SDK-Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. 

Verwenden Sie die Formularerkennung für Folgendes:

* [Analysieren des Layouts](#analyze-layout)
* [Analysieren von Rechnungen](#analyze-invoices)
* [Trainieren eines benutzerdefinierten Modells](#train-a-custom-model)
* [Analysieren von Formularen mit einem benutzerdefinierten Modell](#analyze-forms-with-a-custom-model)
* [Analysieren von Belegen](#analyze-receipts)
* [Analysieren von Visitenkarten](#analyze-business-cards)
* [Verwalten von benutzerdefinierten Modellen](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
