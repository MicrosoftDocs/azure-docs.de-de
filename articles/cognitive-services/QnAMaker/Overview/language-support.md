---
title: 'Sprachunterstützung: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Eine Liste der von QnA Maker für Ihre Wissensdatenbank unterstützten Kulturen und natürlichen Sprachen. Mischen Sie Sprachen nicht in derselben Wissensdatenbank.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 5035a81838ace0678f0cbb68ad2b9325de498003
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353118"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Sprachunterstützung für QnA Maker-Ressourcen und Wissensdatenbanken

In diesem Artikel werden die Sprachunterstützungsoptionen für QnA Maker-Ressourcen und -Wissensdatenbanken beschrieben. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Die Sprache für den Dienst ist ausgewählt, wenn Sie die erste Wissensdatenbank in der Ressource erstellen. Alle weiteren Wissensdatenbanken in der Ressource müssen die gleiche Sprache aufweisen. 

Die Sprache bestimmt die Relevanz der Ergebnisse, die QnA Maker als Antwort auf Benutzerabfragen bereitstellt. Die QnA Maker-Ressource und alle Wissensdatenbanken innerhalb dieser Ressource unterstützen nur eine einzelne Sprache. Diese einzelne Sprache ist erforderlich, um bestmögliche Antwortergebnisse für eine Abfrage zu erzielen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

In QnA Maker verwaltet haben Sie die Wahl, Spracheinstellungen auf Ebene der einzelnen Wissensdatenbank vorzunehmen. Diese Einstellung kann nur mit der Wissensdatenbank des Diensts aktiviert werden. Nach der Festlegung können Spracheinstellungen für den Dienst nicht mehr geändert werden. 

Wenn Sie sich für wissensdatenbankspezifische Spracheinstellungen entscheiden, dürfen Sie Wissensdatenbanken in verschiedenen Sprachen im Dienst selbst erstellen. 

---

## <a name="single-language-per-resource"></a>Einzelne Sprache pro Ressource

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Beachten Sie Folgendes:

* Ein QnA Maker-Dienst und alle seine Wissensdatenbanken unterstützen nur eine Sprache
* Die Sprache wird explizit festgelegt, wenn die erste Wissensdatenbank des Diensts erstellt wird.
* Die Sprache wird durch die Dateien und URLs bestimmt, die beim Erstellen der Wissensdatenbank hinzugefügt werden.
* Die Sprache kann für andere Wissensdatenbanken im Dienst nicht geändert werden.
* Die Sprache wird vom Cognitive Search-Dienst (Bewerter Nr. 1) und dem QnA Maker-Dienst (Bewerter Nr. 2) verwendet, um die beste Antwort auf eine Abfrage zu generieren.

# <a name="qnamaker-managed-preview"></a>[QnA Maker verwaltet (Vorschau)](#tab/v2)
![Spracheinstellung in QnA Maker verwaltet](../media/language-support/language-setting-managed.png)

Wenn Sie das **Kontrollkästchen zum Aktivieren der Spracheinstellung pro Wissensdatenbank nicht aktivieren**, beachten Sie Folgendes: 
* Ein QnA Maker-Dienst und alle seine Wissensdatenbanken unterstützen nur eine Sprache.
* Die Sprache wird explizit festgelegt, wenn die erste Wissensdatenbank des Diensts erstellt wird
* Die Sprache wird durch die Dateien und URLs bestimmt, die beim Erstellen der Wissensdatenbank hinzugefügt werden
* Die Sprache kann für andere Wissensdatenbanken im Dienst nicht geändert werden
* Die Sprache wird vom Cognitive Search-Dienst (Bewerter Nr. 1) und dem QnA Maker-Dienst (Bewerter Nr. 2) verwendet, um die beste Antwort auf eine Abfrage zu generieren

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Unterstützung mehrerer Sprachen in einer QnA Maker-Ressource

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)
Diese Funktion wird in unserer aktuellen allgemein verfügbaren (GA) stabilen Version nicht unterstützt. Probieren Sie QnA Maker verwaltet aus, um diese Funktionalität zu testen. 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)
* Wenn Sie die erste Wissensdatenbank in Ihrem Dienst erstellen, erhalten Sie die Möglichkeit, die Spracheinstellung pro Wissensdatenbank zu aktivieren. Aktivieren Sie das Kontrollkästchen, um Wissensdatenbanken, die zu verschiedenen Sprachen gehören, in einem Dienst zu erstellen.
* Die Spracheinstellungsoption kann für den Dienst nicht mehr geändert werden, sobald die erste Wissensdatenbank erstellt wurde.
* Wenn Sie für jede Wissensdatenbank spezifische Spracheinstellungen aktivieren, haben Sie anstelle eines Testindexes für den Dienst über einen Testindex pro Wissensdatenbank. 

![Spracheinstellung in QnA Maker verwaltet](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Unterstützung mehrerer Sprachen in einer Wissensdatenbank

Wenn Sie ein Wissensdatenbank-System unterstützen müssen, das mehrere Sprachen umfasst, stehen die folgenden Methoden zur Wahl:

* Verwenden Sie den [Translator-Diensts](../../translator/translator-info-overview.md), um eine Frage in eine einzelne Sprache zu übersetzen, bevor die Frage an Ihre Wissensdatenbank gesendet wird. Dies erlaubt es Ihnen, sich auf die Qualität einer einzelnen Sprache und die Qualität der alternativen Fragen und Antworten zu konzentrieren.
* Erstellen Sie für jede Sprache eine QnA Maker-Ressource und eine Wissensdatenbank innerhalb dieser Ressource. Auf diese Weise können Sie für jede Sprache separate alternative Frage- und Antworttexte mit stärkerer Nuancierung verwenden. Dadurch erhalten Sie eine viel größere Flexibilität, allerdings um den Preis eines deutlich höheren Verwaltungsaufwands, wenn sich die Fragen oder Antworten über die Sprachen hinweg unterscheiden.


## <a name="languages-supported"></a>Unterstützte Sprachen

Die folgende Liste enthält die Sprachen, die für QnA Maker-Ressourcen unterstützt werden. 

|Sprache|
|--|
|Arabisch|
|Armenisch|
|Bengalisch|
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

## <a name="query-matching-and-relevance"></a>Abfrageabgleich und Relevanz
QnA Maker nutzt [Sprachanalysefunktionen von Azure Cognitive Search](/rest/api/searchservice/language-support), um Ergebnisse bereitzustellen.

Während die Azure Cognitive Search-Funktionen für unterstützte Sprachen ebenbürtig sind, verfügt QnA Maker über ein zusätzliches Rangfolgemodul, das oberhalb der Azure-Suchergebnisse ansetzt. In diesem Rangfolgemodul verwenden wir einige besondere semantische und wortbasierte Funktionen in den folgenden Sprachen.

|Sprachen mit zusätzlichem Rangfolgemodul|
|--|
|Chinesisch|
|Tschechisch|
|Niederländisch|
|Englisch|
|Französisch|
|Deutsch|
|Ungarisch|
|Italienisch|
|Japanisch|
|Koreanisch|
|Polnisch|
|Portugiesisch|
|Spanisch|
|Schwedisch|

Diese zusätzliche Rangbewertung ist zählt zu den internen Mechanismen des Rangfolgemoduls von QnA Maker.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Sprachauswahl](../index.yml)