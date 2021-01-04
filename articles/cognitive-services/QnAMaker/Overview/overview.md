---
title: Was ist der QnA Maker-Dienst?
description: QnA Maker ist ein cloudbasierter NLP-Dienst zur mühelosen Erstellung einer natürlichen Konversationsebene für Ihre Daten. Er kann verwendet werden, um für eine beliebige Eingabe in natürlicher Sprache die am besten geeignete Antwort aus Ihrer benutzerdefinierten Wissensdatenbank (Knowledge Base, KB) zu finden.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 05/26/2020
ms.custom: cog-serv-seo-aug-2020
keywords: QnA Maker, Chatbots mit wenig Code, Konversationen mit Mehrfachdurchläufen
ms.openlocfilehash: 9655f64da455fb7a7124a97e6dc2d1d8437d05b7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353135"
---
# <a name="what-is-qna-maker"></a>Was ist QnA Maker?

QnA Maker ist ein cloudbasierter NLP-Dienst (Natural Language Processing, Verarbeitung natürlicher Sprache), mit dem Sie eine natürliche Konversationsebene für Ihre Daten erstellen können. Er wird dazu verwendet, für eine beliebige Eingabe die am besten geeignete Antwort aus Ihrer benutzerdefinierten Wissensdatenbank (Knowledge Base, KB) zu finden.

QnA Maker wird häufig mithilfe von Clientanwendungen für die Konversation erstellt, darunter Anwendungen für soziale Medien, Chatbots und sprachaktivierte Desktopanwendungen.

## <a name="when-to-use-qna-maker"></a>Einsatzgebiete von QnA Maker

* **Statische Informationen:** Verwenden Sie QnA Maker, wenn Ihre Antwort-Wissensdatenbank statische Informationen enthält. Diese Wissensdatenbank ist speziell auf Ihre Anforderungen zugeschnitten und wurde anhand von Dokumenten wie [PDFs und URLs](../index.yml) erstellt.
* **Bereitstellung der gleichen Antwort für eine Anforderung, eine Frage oder einen Befehl:** Wenn verschiedene Benutzer die gleiche Frage stellen, erhalten sie die gleiche Antwort.
* **Filterung statischer Informationen auf der Grundlage von Metainformationen:** Fügen Sie [Metadatentags](../how-to/metadata-generateanswer-usage.md) hinzu, um zusätzliche Filteroptionen bereitzustellen, die für die Benutzer Ihrer Clientanwendung und die Informationen relevant sind. Zu den gängigen Metadateninformationen zählen [Geplauder](../how-to/chit-chat-knowledge-base.md), Inhaltstyp oder -format, Inhaltszweck und Inhaltsaktualität.
* **Verwaltung einer Botkonversation, die statische Informationen beinhaltet:** Ihre Wissensdatenbank liefert eine Antwort für den Konversationstext oder Befehl eines Benutzers. Ist die Antwort Teil eines vordefinierten Konversationsablaufs (dargestellt in Ihrer Wissensdatenbank mit [Mehrfachdurchlauf-Kontext](../how-to/multiturn-conversation.md)), kann der Bot diesen Ablauf problemlos bereitstellen.

## <a name="what-is-a-knowledge-base"></a>Was ist eine Wissensdatenbank?

QnA Maker [importiert Ihre Inhalte](../index.yml) in eine Wissensdatenbank, die aus Frage-Antwort-Paaren besteht. Im Zuge des Importvorgangs werden Informationen zur Beziehung zwischen den Teilen Ihrer strukturierten und teilweise strukturierten Inhalte extrahiert, um Beziehungen zwischen den Frage-Antwort-Paaren zu implizieren. Sie können diese Frage-Antwort-Paare bearbeiten oder neue hinzufügen.

Der Inhalt des Frage-Antwort-Paars umfasst Folgendes:
* Alle alternativen Formen der Frage
* Metadatentags zum Filtern von Antwortoptionen während der Suche
* Folgeaufforderungen zum weiteren Optimieren der Suche

![Beispielfrage und -antwort mit Metadaten](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Nach der Veröffentlichung Ihrer Wissensdatenbank sendet eine Clientanwendung eine Benutzerfrage an Ihren Endpunkt. Ihr QnA Maker-Dienst verarbeitet die Frage und gibt die beste Antwort zurück.

## <a name="create-a-chat-bot-programmatically"></a>Programmgesteuertes Erstellen eines Chatbots

Nach der Veröffentlichung einer QnA Maker Wissensdatenbank sendet eine Clientanwendung eine Frage an den Endpunkt Ihrer Wissensdatenbank und erhält die Ergebnisse in Form einer JSON-Antwort. Eine gängige Clientanwendung für QnA Maker ist ein Chatbot.

![Stellen einer Frage an einen Bot und Erhalten einer Antwort aus dem Inhalt der Wissensdatenbank](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Schritt|Aktion|
|:--|:--|
|1|Die Clientanwendung sendet die frei formulierte _Frage_ des Benutzers „How do I programmatically update my Knowledge Base?“ (Wie kann ich meine Wissensdatenbank programmgesteuert aktualisieren?) an den Endpunkt Ihrer Wissensdatenbank.|
|2|QnA Maker verwendet die trainierte Wissensdatenbank, um die korrekte Antwort sowie ggf. weitere Folgeaufforderungen bereitzustellen, die zur Eingrenzung der Suche nach der besten Antwort beitragen. QnA Maker gibt eine Antwort im JSON-Format zurück.|
|3|Die Clientanwendung entscheidet auf der Grundlage der JSON-Antwort über den weiteren Verlauf der Konversation. Mögliche Entscheidungen wären etwa, die beste Antwort anzuzeigen und weitere Optionen zu präsentieren, um die Suche nach der besten Antwort einzugrenzen. |
|||

## <a name="build-low-code-chat-bots"></a>Erstellen von Chatbots mit wenig Code

Eine Wissensdatenbank kann vollständig über das QnA Maker-Portal erstellt werden. Sie können Dokumente in ihrer aktuellen Form in Ihre Wissensdatenbank importieren. Diese Dokumente (häufig gestellte Fragen, Handbücher, Arbeitsblätter, Webseiten oder Ähnliches) werden in Frage-Antwort-Paaren konvertiert. Jedes Paar wird nach Folgeaufforderungen analysiert und mit anderen Paaren verknüpft. Das endgültige _Markdownformat_ unterstützt eine hochwertige Darstellung mit Bildern und Links.

Veröffentlichen Sie die Wissensdatenbank nach der Bearbeitung ganz ohne Programmieraufwand für einen funktionierenden [Azure-Web-App-Bot](https://azure.microsoft.com/services/bot-service/). Testen Sie Ihren Bot im [Azure-Portal](https://portal.azure.com), oder setzen Sie die Entwicklung nach dem Herunterladen fort.

## <a name="high-quality-responses-with-layered-ranking"></a>Hochwertige Antworten mit Bewertung mit mehreren Ebenen

Das QnA Maker-System basiert auf einem Bewertungsansatz mit mehreren Ebenen. Die Daten werden in Azure Search gespeichert. Dies ist auch gleichzeitig die erste Bewertungsebene. Die besten Ergebnisse von Azure Search werden dann an das NLP-Neubewertungsmodell von QnA Maker übergeben, um die endgültigen Ergebnisse und die Zuverlässigkeitsbewertung zu generieren.

## <a name="multi-turn-conversations"></a>Konversationen mit Mehrfachdurchläufen

QnA Maker bietet Eingabeaufforderungen mit Mehrfachdurchläufen und aktives Lernen, um Sie bei der Verbesserung Ihrer grundlegenden Frage-Antwort-Paare zu unterstützen.

**Eingabeaufforderungen mit Mehrfachdurchläufen** ermöglichen die Verknüpfung von Frage-Antwort-Paaren. Diese Verknüpfung ermöglicht es der Clientanwendung, eine Top-Antwort zurückzugeben, und stellt weitere Fragen bereit, um die Suche nach einer abschließenden Antwort einzugrenzen.

Nachdem die Wissensdatenbank Benutzerfragen über den veröffentlichten Endpunkt erhalten hat, wendet QnA Maker **aktives Lernen** auf diese Fragen aus der Praxis an, um Änderungen an Ihrer Wissensdatenbank vorzuschlagen, die zur Verbesserung der Qualität beitragen.

## <a name="development-lifecycle"></a>Lebenszyklus der Entwicklung

QnA Maker bietet Erstellungs-, Trainings- und Veröffentlichungsfunktionen sowie Zusammenarbeitsberechtigungen und lässt sich somit in den gesamten Entwicklungszyklus integrieren.

> [!div class="mx-imgBorder"]
> ![Konzeptuelle Darstellung des Entwicklungszyklus](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="complete-a-quickstart"></a>Abschließen einer Schnellstartanleitung

Wir stellen Schnellstartanleitungen für die gängigsten Programmiersprachen bereit. Diese enthalten Informationen zu grundlegenden Entwurfsmustern und sind jeweils so konzipiert, dass Sie in weniger als zehn Minuten über ausführbaren Code verfügen. Die folgende Liste enthält Schnellstartanleitungen zu jedem Feature:

* [Erste Schritte mit der QnA Maker-Clientbibliothek](../quickstarts/quickstart-sdk.md)
* [Erste Schritte mit dem QnA Maker-Portal](../quickstarts/create-publish-knowledge-base.md)
* [Erste Schritte mit QnA Maker-REST-APIs](../quickstarts/quickstart-rest-curl.md)


## <a name="next-steps"></a>Nächste Schritte
QnA Maker bietet alles, was Sie zum Erstellen, Verwalten und Bereitstellen Ihrer benutzerdefinierten Wissensdatenbank benötigen.

> [!div class="nextstepaction"]
> [Überprüfen der aktuellen Änderungen](../whats-new.md)