---
title: 'Migrieren von Wissensdatenbanken: QnA Maker'
description: Die Migration einer Wissensdatenbank erfordert den Export aus einer Wissensdatenbank und den Import in eine andere.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: c89ab375cb02824a08ff57e6b5278dd9299126ff
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350924"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrieren einer Knowledge Base durch Exportieren und Importieren

Die Migration ist der Prozess zum Erstellen einer neuen Wissensdatenbank auf der Grundlage einer vorhandenen Wissensdatenbank. Sie können eine Migration aus verschiedenen Gründen ausführen:

* Sicherungs- und Wiederherstellungsprozesse
* CI/CD-Pipeline
* Verschieben von Regionen

Die Migration einer Wissensdatenbank erfordert den Export aus einer vorhandenen Wissensdatenbank und den Import in eine andere.

> [!NOTE]
> Befolgen Sie die folgenden Anweisungen, um Ihre vorhandene Wissensdatenbank auf eine neue von QnA Maker verwaltete Version zu migrieren (Vorschau).

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/), bevor Sie beginnen.
* Richten Sie einen neuen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md) ein.

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrieren einer Wissensdatenbank aus QnA Maker
1. Melden Sie sich beim [QnA Maker-Portal](https://qnamaker.ai) an.
1. Wählen Sie die ursprüngliche Wissensdatenbank aus, die Sie migrieren möchten.

1. Wählen Sie auf der Seite **Settings** (Einstellungen) die Option **Wissensdatenbank exportieren** (Export knowledge base), um eine TSV-Datei mit dem Inhalt der ursprünglichen Wissensdatenbank herunterzuladen, d. h. Fragen, Antworten, Metadaten, Folgeaufforderungen und die Namen der Datenquellen, aus denen sie extrahiert wurden. Die QnA-IDs, die mit den Fragen und Antworten exportiert werden, können mithilfe der [Update-API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update) zum Aktualisieren eines bestimmten QnA-Paares verwendet werden. Die QnA-ID für ein bestimmtes QnA-Paar bleibt über mehrere Exportvorgänge hinweg unverändert.

1. Wählen Sie im oberen Menü die Option **Create a knowledge base** (Wissensdatenbank erstellen) aus, um eine _leere_ Wissensdatenbank zu erstellen. Sie ist leer, da Sie bei ihrer Erstellung keine URLs oder Dateien hinzufügen werden. Diese werden nach der Erstellung während des Importschritts hinzugefügt.

    Konfigurieren Sie die Wissensdatenbank. Legen Sie nur den neuen Namen der Wissensdatenbank fest. Doppelte Namen werden ebenso wie Sonderzeichen unterstützt.

    Wählen Sie keine Werte aus Schritt 4 aus, da diese Werte beim Importieren der Datei überschrieben werden.

1. Wählen Sie in Schritt 5 die Option **Erstellen** aus.

1. Öffnen Sie in dieser neuen Wissensdatenbank die Registerkarte **Einstellungen**, und klicken Sie auf **Import knowledge base** (Wissensdatenbank importieren). Dadurch werden die Fragen, Antworten, Metadaten und Folgeaufforderungen importiert sowie die Namen der Datenquellen beibehalten, aus denen sie extrahiert wurden. **Die in der neuen Wissensdatenbank erstellten QnA-Paare müssen dieselbe QnA-ID aufweisen wie in der exportierten Datei**. Dies hilft Ihnen dabei, ein exaktes Replikat der Wissensdatenbank zu erstellen.

   > [!div class="mx-imgBorder"]
   > [![Importieren der Wissensdatenbank](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Testen** Sie die neue Knowledge Base im Testbereich. Erfahren Sie mehr über das [Testen Ihrer Knowledge Base](../How-To/test-knowledge-base.md).

1. **Veröffentlichen** Sie die Wissensdatenbank, und erstellen Sie einen Chatbot. Erfahren Sie mehr über das [Veröffentlichen Ihrer Knowledge Base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Programmgesteuertes Migrieren einer Wissensdatenbank von QnA Maker

Die Migration kann programmgesteuert über die folgenden REST-APIs ausgeführt werden:

**Export**

* [Herunterladen der Wissensdatenbank-API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**Importieren**

* [Ersetzen der API (erneutes Laden mit der gleichen Wissensdatenbank-ID)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [Erstellen der API (Laden mit der neuen Wissensdatenbank-ID)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Chatprotokolle und Varianten
Varianten (Synonyme) ohne Berücksichtigung von Groß-/Kleinbuchstaben werden nicht automatisch importiert. Verwenden Sie die [V4-APIs](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase), um die Änderungen in die neue Wissensdatenbank zu verschieben.

Die Migration von Chatprotokollen ist nicht möglich, da die neue Wissensdatenbank Application Insights zum Speichern von Chatprotokollen verwendet.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bearbeiten einer Knowledge Base](../How-To/edit-knowledge-base.md)