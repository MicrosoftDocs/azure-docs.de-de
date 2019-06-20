---
title: 'Migrieren von Wissensdatenbanken: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Die Migration einer Wissensdatenbank erfordert den Export aus einer Wissensdatenbank und den Import in eine andere.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8ff3c497372a761bd8a02ae81bc897c8ee297bd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794867"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrieren einer Knowledge Base durch Exportieren und Importieren

Die Migration einer Wissensdatenbank erfordert den Export aus einer Wissensdatenbank und den Import in eine andere. 

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* Richten Sie einen neuen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md) ein.

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrieren einer Wissensdatenbank aus QnA Maker
1. Melden Sie sich beim [QnA Maker-Portal](https://qnamaker.ai) an.
1. Wählen Sie die Wissensdatenbank aus, die Sie migrieren möchten.

1. Wählen Sie auf der Seite **Settings** (Einstellungen) die Option **Wissensdatenbank exportieren** (Export knowledge base), um eine TSV-Datei mit dem Inhalt der Wissensdatenbank herunterzuladen, d.h. Fragen, Antworten, Metadaten und die Namen der Datenquellen, aus denen sie extrahiert wurden.

1. Wählen Sie im oberen Menü die Option **Create a knowledge base** (Wissensdatenbank erstellen) aus, um eine leere Wissensdatenbank zu erstellen. 

    ![Festlegen von Datenquellen](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Weisen Sie dem Dienst einen **Namen** zu. Doppelte Namen werden ebenso wie Sonderzeichen unterstützt.

1. Klicken Sie auf **Erstellen**.

    ![Erstellen der Knowledge Base](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Öffnen Sie in dieser neuen Wissensdatenbank die Registerkarte **Einstellungen**, und klicken Sie auf **Import knowledge base** (Wissensdatenbank importieren). Dadurch werden die Fragen, Antworten und Metadaten importiert und die Namen der Datenquellen beibehalten, aus denen sie extrahiert wurden.

   ![Importieren der Knowledge Base](../media/qnamaker-how-to-migrate-kb/Import.png)

1. **Testen** Sie die neue Knowledge Base im Testbereich. Erfahren Sie mehr über das [Testen Ihrer Knowledge Base](../How-To/test-knowledge-base.md).
1. **Veröffentlichen** Sie die Knowledge Base. Erfahren Sie mehr über das [Veröffentlichen Ihrer Knowledge Base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Sie können den Endpunkt in Ihrer Anwendung oder im Botcode verwenden. Informationen zum Erstellen eines QnA-Bots finden Sie [hier](../Tutorials/create-qna-bot.md).

    ![QnA Maker-Werte](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    Nun wird der gesamte Inhalt der Knowledge Base – Fragen, Antworten und Metadaten sowie die Namen der Quelldateien und der URLs – in die neue Knowledge Base importiert. 

## <a name="chat-logs-and-alterations"></a>Chatprotokolle und Varianten
Varianten (Synonyme) ohne Berücksichtigung von Groß-/Kleinbuchstaben werden nicht automatisch importiert. Verwenden Sie die [V4-APIs](https://go.microsoft.com/fwlink/?linkid=2092179), um die Änderungen in die neue Wissensdatenbank zu verschieben.

Die Migration von Chatprotokollen ist nicht möglich, da die neue Wissensdatenbank Application Insights zum Speichern von Chatprotokollen verwendet. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bearbeiten einer Knowledge Base](../How-To/edit-knowledge-base.md)
