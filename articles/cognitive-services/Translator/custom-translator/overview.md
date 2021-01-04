---
title: Worum handelt es sich bei Custom Translator?
titleSuffix: Azure Cognitive Services
description: Custom Translator bietet ähnliche Funktionen wie Microsoft Translator Hub für die statistische maschinelle Übersetzung (Statistical Machine Translation, SMT), aber ausschließlich für Systeme für neuronale maschinelle Übersetzung (Neural Machine Translation, NMT).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: 6a7301fd878eb65e4c1dd540275611bcf9348afc
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241742"
---
# <a name="what-is-custom-translator"></a>Worum handelt es sich bei Custom Translator?

[Custom Translator](https://portal.customtranslator.azure.ai) ist ein Feature des Microsoft Translator-Diensts, der Übersetzungsunternehmen, App-Entwicklern und Sprachdienstanbietern das Erstellen von angepassten Systemen für neuronale maschinelle Übersetzung (NMT) ermöglicht. Das angepasste Übersetzungssystem wird nahtlos in vorhandene Anwendungen, Workflows und Websites integriert.

Mit [Custom Translator](https://portal.customtranslator.azure.ai) erstellte Übersetzungssysteme sind über dieselbe cloudbasierte, [sichere](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), leistungsstarke, hochgradig skalierbare [Textübersetzungs-API V3](../reference/v3-0-translate.md?tabs=curl) von Microsoft verfügbar, die jeden Tag Milliarden von Übersetzungen ermöglicht.

Custom Translator unterstützt mehr als drei Dutzend Sprachen und nimmt eine direkte Zuordnung an die für NMT verfügbaren Sprachen vor. Eine vollständige Liste finden Sie unter [Sprach- und Regionsunterstützung für die Textübersetzungs-API](../language-support.md#customization).

## <a name="features"></a>Features

Custom Translator bietet verschiedene Funktionen zum Erstellen eines benutzerdefinierten Übersetzungssystems und den anschließenden Zugriff darauf.

|Funktion  |BESCHREIBUNG  |
|---------|---------|
|[Nutzen von neuronaler maschineller Übersetzungstechnologie](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Verbessern Sie Ihre Übersetzung durch die Nutzung der von Custom Translator bereitgestellten neuronalen maschinellen Übersetzung (NMT).       |
|[Erstellen von Systemen, die Ihre Geschäftsterminologie kennen](what-are-parallel-documents.md)     |  Mithilfe von parallelen Dokumenten können Sie Übersetzungssysteme erstellen, die die Terminologie verstehen, die in Ihrem Unternehmen und der Branche verwendet wird.       |
|[Verwenden eines Wörterbuchs zum Erstellen Ihrer Modelle](what-is-dictionary.md)     |   Wenn Sie kein Trainingsdataset haben, können Sie ein Modell nur mit Wörterbuchdaten trainieren.       |
|[Zusammenarbeit mit anderen Benutzern](how-to-manage-settings.md#share-your-workspace)     |   Teilen Sie Ihre Arbeit mit verschiedenen Personen, um mit Ihrem Team zusammenarbeiten.     |
|[Zugriff auf Ihr benutzerdefiniertes Übersetzungsmodell](../reference/v3-0-translate.md?tabs=curl)     |  Der Zugriff auf Ihr benutzerdefiniertes Übersetzungsmodell in Ihren vorhandenen Anwendungen/Programmen ist jederzeit über die Microsoft-Textübersetzungs-API V3 möglich.       |

## <a name="get-better-translations"></a>Bessere Übersetzungen

Microsoft Translator hat [Neural Machine Translation (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) im Jahr 2016 veröffentlicht. NMT hat für erhebliche Fortschritte bei der Übersetzungsqualität gesorgt, welche die der branchenüblichen Technologie der [statistischen maschinellen Übersetzung (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) übertrifft. Da NMT den Kontext vollständiger Sätze vor der Übersetzung besser erfasst, liefert sie Übersetzungen in höherer Qualität, die menschlicher klingen und flüssiger sind. [Custom Translator](https://portal.customtranslator.azure.ai) stellt NMT für Ihre benutzerdefinierten Modelle bereit und sorgt so für eine bessere Qualität der Übersetzungen.

Sie können zuvor übersetzte Dokumente verwenden, um ein Übersetzungssystem zu erstellen. Diese Dokumente enthalten domänenspezifische Terminologie und verfügen über einen besseren Stil als ein Standardübersetzungssystem. Benutzer können Dokumente in den folgenden Dateiformaten hochladen: ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX und XLSX.

Custom Translator akzeptiert auch Daten, die auf Dokumentebene parallel sind, um die Datenerfassung und -vorbereitung effektiver zu gestalten. Wenn Sie über Zugriff auf Versionen mit dem gleichen Inhalt in mehreren Sprachen verfügen, die sich jedoch in separaten Dokumenten befinden, kann Custom Translator Sätze über mehrere Dokumente hinweg automatisch abgleichen.

Wenn der geeignete Typ und die entsprechende Menge von Trainingsdaten bereitgestellt werden, ist es keine Seltenheit, dass die [BLEU-Bewertung](what-is-bleu-score.md) durch die Verwendung von Custom Translator um 5 bis 10 Punkt ansteigt.

## <a name="be-productive-and-cost-effective"></a>Produktiv und kosteneffektiv

Mit [Custom Translator](https://portal.customtranslator.azure.ai) sind für das Trainieren und Bereitstellen eines benutzerdefinierten Systems keine Programmierkenntnisse erforderlich.

Mithilfe des sicheren [Custom Translator](https://portal.customtranslator.azure.ai)-Portals können Benutzer Trainingsdaten hochladen, Systeme trainieren, Systeme testen und sie über eine intuitive Benutzeroberfläche in einer Produktionsumgebung bereitstellen. Das System steht dann innerhalb weniger Stunden (die tatsächliche Zeit hängt von der Größe der Trainingsdaten ab) zu umfassenden Nutzung bereit.

Der Zugriff auf [Custom Translator](https://portal.customtranslator.azure.ai) ist auch programmgesteuert über eine [dedizierte API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (derzeit in der Vorschauphase) möglich. Die API ermöglicht Benutzern das Verwalten der Erstellung oder Aktualisierung von Training in regelmäßigen Abständen über ihre eigenen Apps oder Webdienste.

Die Kosten für die Verwendung eines benutzerdefinierten Modells zum Übersetzen von Inhalt basiert auf dem Tarif für die Textübersetzungs-API des Benutzers. Informationen zu Tarifen für Cognitive Services finden Sie auf der [Preiswebseite der Textübersetzungs-API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Sicheres Übersetzen – jederzeit und überall in allen Ihren Apps und Diensten

Der Zugriff auf benutzerdefinierte Systeme und deren Integration in beliebige Produkte oder Geschäftsworkflows und auf jedem Gerät sind mit der Microsoft-Textübersetzungs-API über standardmäßige REST-Technologie nahtlos möglich.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Preisdetails](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Erfahren Sie im [Schnellstart](quickstart-build-deploy-custom-model.md), wie ein Übersetzungsmodell in Custom Translator erstellt wird.
