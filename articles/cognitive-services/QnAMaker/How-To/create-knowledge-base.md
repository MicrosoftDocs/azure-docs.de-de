---
title: Erstellen einer Wissensdatenbank
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Verwenden Sie das Dienstportal für die QnA Maker-API, um eine Wissensdatenbank mit Geplauder hinzuzufügen und zu erstellen. Dadurch wird Ihre App ansprechender. Fügen Sie einen vorkonfigurierten Satz beliebter Themen als Ausgangspunkt für das Geplauder Ihres Bots hinzu, und sparen Sie damit die Zeit und Kosten, diese Elemente von Grund auf neu zu schreiben.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 6b64315d8639cf8a7204ee809598567ec76fd188
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541793"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Schnellstart: Erstellen einer Wissensdatenbank über das QnA Maker-API-Dienstportal

Über das Dienstportal für die QnA Maker-API können Sie beim Erstellen einer Wissensdatenbank ganz einfach Ihre vorhandenen Datenquellen hinzufügen. Aus den folgenden Dokumenttypen können Sie eine neue QnA Maker-Wissensdatenbank erstellen:

<!-- added for scanability -->
* FAQ-Seiten
* Produkthandbücher
* Strukturierte Dokumente

Fügen Sie eine Persönlichkeit für Geplauder ein, um Ihre Informationen für Ihre Benutzer ansprechender zu machen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

## <a name="create-a-new-knowledge-base"></a>Erstellen einer neuen Wissensdatenbank

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen am [QnA Maker-Portal](https://qnamaker.ai) an, und wählen Sie **Wissensdatenbank erstellen** aus.

1. Wenn Sie noch keinen QnA Maker-Dienst erstellt haben, wählen Sie **Create a QnA service** (QnA-Dienst erstellen) aus. 

1. Wählen Sie Ihren Azure-Mandanten, den Azure-Abonnementnamen und den dem QnA Maker-Dienst zugeordneten Azure-Ressourcennamen aus den Listen in **Schritt 2** im QnA Maker-Portal aus. Wählen Sie einen Azure QnA Maker-Dienst als Host für die Wissendatenbank aus.

    ![Einrichten eines QnA-Diensts](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Geben Sie den Namen Ihrer Wissensdatenbank und die Datenquellen für die neue Wissensdatenbank ein.

    ![Festlegen von Datenquellen](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Weisen Sie dem Dienst einen **Namen** zu. Doppelte Namen werden ebenso wie Sonderzeichen unterstützt.
    - Fügen Sie URLs für die Daten hinzu, die extrahiert werden sollen. Weitere Informationen zu den unterstützten Ausgangstypen finden Sie [hier](../Concepts/data-sources-supported.md).
    - Laden Sie Dateien für die Daten hoch, die extrahiert werden sollen. Unter [Preise](https://aka.ms/qnamaker-pricing) erfahren Sie, wie viele Dokumente Sie hinzufügen können.
    - Wenn Sie QnAs manuell hinzufügen möchten, können Sie **Schritt 4**, der in der vorhergehenden Abbildung gezeigt wird, überspringen.

1. Fügen Sie Ihrer Wissensdatenbank **Geplauder** hinzu. Fügen Sie Geplauderunterstützung für Ihren Bot hinzu, indem Sie eine der Persönlichkeiten auswählen. 

    ![Hinzufügen von Geplauder zu Wissensdatenbanken](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Wählen Sie **Wissensdatenbank erstellen** aus.

    ![Erstellen der Knowledge Base](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Das Extrahieren der Daten dauert einige Minuten.

    ![Extraktion](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Wenn Ihre Wissensdatenbank erfolgreich erstellt wurde, werden Sie auf die Seite **Wissensdatenbank** weitergeleitet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit der Wissensdatenbank fertig sind, entfernen Sie sie im QnA Maker-Portal.

## <a name="next-steps"></a>Nächste Schritte

Zur Kosteneinsparung können Sie einige, aber nicht alle Azure-Ressourcen, die für QnA Maker erstellt wurden, [freigeben](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker).

> [!div class="nextstepaction"]
> [Hinzufügen von persönlichem Geplauder](./chit-chat-knowledge-base.md)
