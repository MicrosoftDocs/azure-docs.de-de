---
title: 'Entwerfen einer Wissensdatenbank: QnA Maker'
description: Eine QnA Maker-Wissensdatenbank besteht aus einer Reihe von Frage-Antwort-Paaren (QnA) und optionalen Metadaten, die jedem QnA-Paar zugeordnet sind.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: ef5476ade205109f5dfede1b3bb2c3a4ae2e81a7
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506004"
---
# <a name="question-and-answer-pair"></a>Frage-Antwort-Paar

Eine Wissensdatenbank besteht aus Frage-Antwort-Paaren (Question and Answer – QnA).  Jedes Paar hat eine Antwort und enthält alle Informationen, die mit dieser _Antwort_ verknüpft sind. Eine Antwort ähnelt einer Datenbankzeile oder einer Datenstrukturinstanz.

## <a name="question-and-answer-pairs"></a>Frage-Antwort-Paare

Die **erforderlichen** Einstellungen in einem Frage-Antwort-Paar (QnA) lauten wie folgt:

* Eine **Frage**: Text der Benutzerabfrage für das maschinelle Lernen von QnA Maker, mit dem der Text der Benutzerfrage mit unterschiedlichen Formulierungen, aber derselben Antwort abgeglichen wird.
* Die **Antwort**: wird zurückgegeben, wenn eine Benutzerabfrage mit der zugeordneten Frage übereinstimmt.

Jedes Paar wird durch eine **ID** dargestellt.

Die **optionalen** Einstellungen für ein Paar umfassen Folgendes:

* **Alternative Formen der Frage:** helfen QnA Maker, die richtige Antwort für eine größere Vielfalt von Frageformulierungen zurückzugeben.
* **Metadaten**: Metadaten sind Tags in Form von Schlüssel-Wert-Paaren, die einem QnA-Paar zugeordnet sind. Metadatentags werden verwendet, um QnA-Paare zu filtern und die Datenmenge einzuschränken, für die ein Abfrageabgleich vorgenommen wird.
* **Mehrfachdurchlauf-Eingabeaufforderungen:** werden verwendet, um eine mehrteilige Unterhaltung fortzusetzen.

![QnA Maker-Wissensdatenbanken](media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Redaktionelles Hinzufügen zur Wissensdatenbank

Wenn Sie nicht über bereits vorhandene Inhalte zum Füllen der Wissensdatenbank verfügen, können Sie auch redaktionell im QnA Maker-Portal QnA-Paare hinzufügen. Informationen zum Aktualisieren Ihrer Wissensdatenbank finden Sie [hier](How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Lokales Bearbeiten Ihrer Wissensdatenbank

Nachdem eine Wissensdatenbank erstellt wurde, wird empfohlen, den Text der Wissensdatenbank im [QnA Maker-Portal](https://qnamaker.ai) zu bearbeiten, anstatt diesen in lokale Dateien zu exportieren und wieder zu importieren. Es kann jedoch vorkommen, dass Sie eine Wissensdatenbank lokal bearbeiten müssen.

Exportieren Sie die Wissensdatenbank auf der Seite **Einstellungen**, und bearbeiten Sie die Wissensdatenbank dann mit Microsoft Excel. Wenn Sie eine andere Anwendung verwenden, um die exportierte Datei zu bearbeiten, fügt die Anwendung eventuell Syntaxfehler ein, da sie nicht vollständig TSV-konform ist. TSV-Dateien von Microsoft Excel fügen in der Regel keine Formatierungsfehler ein.

Wenn Sie mit der Bearbeitung fertig sind, importieren Sie die TSV-Datei auf der Seite **Einstellungen**. Die aktuelle Wissensdatenbank wird dabei vollständig durch die importierte Wissensdatenbank ersetzt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Lebenszyklus einer Wissensdatenbank in QnA Maker](Concepts/development-lifecycle-knowledge-base.md)
