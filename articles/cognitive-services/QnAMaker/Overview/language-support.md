---
title: 'Sprachunterstützung: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Die Sprache einer Wissensdatenbank wirkt sich auf die Fähigkeit von QnA Maker zum automatischen Extrahieren von Fragen und Antworten aus Quellen aus. Sie beeinflusst auch die Relevanz der Ergebnisse, die QnA Maker als Antwort auf Benutzerabfragen zurückgibt. Eine Liste der von QnA Maker für Ihre Wissensdatenbank unterstützten Kulturen und natürlichen Sprachen. Mischen Sie Sprachen nicht in derselben Wissensdatenbank.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 496f7c75e6633089d4ca88a9e3cd7c76ee415780
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922407"
---
# <a name="language-support-for-qna-maker"></a>Sprachunterstützung für QnA Maker

Die Sprache einer Wissensdatenbank wirkt sich auf die Fähigkeit von QnA Maker zum automatischen Extrahieren von Fragen und Antworten aus [Quellen](../Concepts/data-sources-supported.md) aus. Sie beeinflusst auch die Relevanz der Ergebnisse, die QnA Maker als Antwort auf Benutzerabfragen zurückgibt.

## <a name="auto-extraction"></a>Automatisches Extrahieren
QnA Maker unterstützt das Extrahieren von Fragen und Antworten auf Seiten in jeder Sprache, aber für folgende Sprachen ist die Effektivität viel höher, da QnA Maker Stichwörter verwendet, um Fragen zu identifizieren.

|Unterstützte Sprachen| Gebietsschema|
|-----|----|
|Englisch|en-*|
|Französisch|fr-*|
|Italienisch|it-*|
|Deutsch|de-*|
|Spanisch|es-*|

## <a name="primary-language-detection"></a>Erkennung der primären Sprache

Die für die Erkennung verwendete primäre Sprache wird für die QnA Maker-Ressource und alle auf dieser Ressource erstellten Wissensdatenbanken festgelegt, wenn das erste Dokument oder die erste URL der ersten Wissensdatenbank hinzugefügt wird. Die Sprache kann nicht geändert werden. 

Wenn der Benutzer plant, mehrere Sprachen zu unterstützen, muss er für jede Sprache eine neue QnA Maker-Ressource verwenden. Erfahren Sie, wie Sie [eine sprachbasierte QnA Maker-Wissensdatenbank erstellen](../how-to/language-knowledge-base.md).  

Überprüfen Sie die primäre Sprache mit den folgenden Schritten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.  
1. Suchen und wählen Sie die Azure Search-Ressource, die als Teil Ihrer QnA Maker-Ressource erstellt wurde. Der Name der Azure Search-Ressource beginnt mit demselben Namen wie die QnA Maker-Ressource und hat den Typ **Suchdienst**. 
1. Wählen Sie auf der Seite **Übersicht** der Azure Search-Ressource die Option **Indizes** aus. 
1. Wählen Sie den Index **testkb** aus.
1. Wählen Sie die Registerkarte **Felder** aus. 
1. Zeigen Sie die **Analyse**-Spalte für die Felder **Fragen** und **Antwort** an. 


## <a name="query-matching-and-relevance"></a>Abfrageabgleich und Relevanz
QnA Maker nutzt [Sprachanalysefunktionen](https://docs.microsoft.com/rest/api/searchservice/language-support) in der Azure-Suche, um Ergebnisse bereitzustellen. Für en-*-Sprachen sind spezielle Features zur erneuten Rangzuweisung verfügbar, die eine höhere Relevanz ermöglichen.

Während die Azure Search-Funktionen für unterstützte Sprachen ebenbürtig ist, verfügt QnA Maker über ein zusätzliches Rangfolgemodul, die oberhalb der Azure Search-Ergebnisse ansetzen. In diesem Rangfolgemodul verwenden wir einige besondere semantische und wortbasierte Funktionen in en-*, die für andere Sprachen noch nicht verfügbar sind. Wir machen diese Funktionen nicht verfügbar, da sie Teil der internen Verarbeitung des QnA Maker-Rangfolgemoduls sind. 

QnA Maker [erkennt die Sprache der Wissensdatenbank](#primary-language-detection) während der Erstellung automatisch und richtet die Analysefunktion entsprechend ein. Sie können Wissensdatenbanken in den folgenden Sprachen erstellen. 

|Unterstützte Sprachen|
|-----|
|Arabisch|
|Armenisch|
Bangla|
|Baskisch|
|Bulgarisch|
|Katalanisch|
|Chinesisch (vereinfacht)|
|Chinesisch (traditionell)|
|Kroatisch|
|Tschechisch|
|Dänisch|
|Niederländisch|
|Englisch|
|Estnisch|
|Finnisch|
|Französisch|
|Galizisch|
|Deutsch|
|Griechisch|
|Gujarati|
|Hebräisch|
|Hindi|
|Ungarisch|
|Isländisch|
|Indonesisch|
|Irisch|
|Italienisch|
|Japanisch|
|Kannada|
|Koreanisch|
|Lettisch|
|Litauisch|
|Malayalam|
|Malaiisch|
|Norwegisch|
|Polnisch|
|Portugiesisch|
|Pandschabi|
|Rumänisch|
|Russisch|
|Serbisch (Kyrillisch)|
|Serbisch (Lateinisch)|
|Slowakisch|
|Slowenisch|
|Spanisch|
|Schwedisch|
|Tamilisch|
|Telugu|
|Thailändisch|
|Türkisch|
|Ukrainisch|
|Urdu|
|Vietnamesisch|
