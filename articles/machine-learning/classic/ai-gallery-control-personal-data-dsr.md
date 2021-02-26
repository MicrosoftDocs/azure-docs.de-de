---
title: 'ML Studio (Classic): Verwalten der Daten von Azure KI-Katalog (Azure)'
description: Sie können Ihre im Produkt enthaltenen Benutzerdaten im Azure KI-Katalog über die Benutzeroberfläche oder die Azure KI-Katalog-API exportieren und löschen. In diesem Artikel erfahren Sie, welche Schritte erforderlich sind.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: mldocs
ms.openlocfilehash: 3461b8026079d089bacca9f12aa94e13f5cb2874
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519234"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Anzeigen und Löschen von im Produkt enthaltenen Benutzerdaten im Azure KI-Katalog



Sie können Ihre im Produkt enthaltenen Benutzerdaten im Azure KI-Katalog über die Benutzeroberfläche oder die Azure KI-Katalog-API anzeigen und löschen. In diesem Artikel erfahren Sie, wie das geht.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Anzeigen Ihrer Daten im KI-Katalog über die Benutzeroberfläche

Sie können veröffentlichte Elemente über die Benutzeroberfläche der Azure KI-Katalog-Website anzeigen. Benutzer können sowohl öffentliche als auch nicht aufgelistete Lösungen, Projekte, Experimente und andere veröffentlichte Elemente anzeigen:

1.    Melden Sie sich beim [Azure KI-Katalog](https://gallery.azure.ai/) an.
2.    Klicken Sie auf das Profilfoto in der oberen rechten Ecke und dann auf den Kontonamen, um Ihre Profilseite zu laden.
3.    Die Profilseite enthält alle im Katalog veröffentlichten Elemente, einschließlich nicht aufgelisteter Einträge.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Verwenden der Azure KI-Katalog-API zum Anzeigen der Daten

Sie können programmgesteuert über die Katalog-API des KI-Katalogs, die über https://catalog.cortanaanalytics.com/entities zugänglich ist, gesammelte Daten anzeigen. Zum Anzeigen der Daten benötigen Sie Ihre Autor-ID. Um nicht aufgelistete Entitäten über die Katalog-API anzuzeigen, benötigen Sie ein Zugriffstoken.

Katalogantworten werden im JSON-Format zurückgegeben.

### <a name="get-an-author-id"></a>Abrufen einer Autor-ID
Die Autor-ID basiert auf der E-Mail-Adresse, die beim Veröffentlichen im Azure KI-Katalog verwendet wurde. Sie wird nicht geändert:

1. Melden Sie sich beim [Azure KI-Katalog](https://gallery.azure.ai/) an.
2. Klicken Sie auf das Profilfoto in der oberen rechten Ecke und dann auf den Kontonamen, um Ihre Profilseite zu laden.
3. Die URL in der Adressleiste zeigt im Anschluss an `authorId=` die alphanumerische ID. Beispielsweise für die URL: \
    `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

    ID des Autors: \
    `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Abrufen eines Zugriffstokens

Um nicht aufgelistete Entitäten über die Katalog-API anzuzeigen, benötigen Sie ein Zugriffstoken. Ohne ein Zugriffstoken können Benutzer weiterhin öffentliche Entitäten und andere Benutzerinformationen anzeigen.

Um ein Zugriffstoken abzurufen, müssen Sie den Header `DataLabAccessToken` einer HTTP-Anforderung überprüfen, die der Browser an die Katalog-API sendet, während Sie angemeldet sind:

1.    Melden Sie sich beim [Azure KI-Katalog](https://gallery.azure.ai/) an.
2.    Klicken Sie auf das Profilfoto in der oberen rechten Ecke und dann auf den Kontonamen, um Ihre Profilseite zu laden.
3.    Öffnen Sie durch Drücken von F12 den Browserbereich „Entwicklertools“, wählen Sie die Registerkarte „Netzwerk“ aus, und aktualisieren Sie die Seite. 
4. Filtern Sie die Anforderungen nach der Zeichenfolge *Katalog*, indem Sie diese im Textfeld „Filter“ eingeben.
5.    Suchen Sie in den Anforderungen an die URL `https://catalog.cortanaanalytics.com/entities` nach einer GET-Anforderung, und wählen Sie die Registerkarte *Header* aus. Scrollen Sie nach unten bis zum Abschnitt *Anforderungsheader*.
6.    Unter dem Header `DataLabAccessToken` befindet sich das alphanumerische Token. Zum besseren Schutz Ihrer Daten sollten Sie dieses Token niemandem bekanntgeben.

### <a name="view-user-information"></a>Anzeigen von Benutzerinformationen
Zeigen Sie mithilfe der in den vorherigen Schritten abgerufenen Autor-ID Informationen im Profil eines Benutzers an, indem Sie `[AuthorId]` in der folgenden URL ersetzen:

`https://catalog.cortanaanalytics.com/users/[AuthorID]`

Beispielsweise gibt die folgende URL-Anforderung:

`https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

eine Antwort wie folgt zurück:

```json
{"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}
```

### <a name="view-public-entities"></a>Anzeigen öffentlicher Entitäten

Die Katalog-API speichert Informationen zu im Azure KI-Katalog veröffentlichten Entitäten, die Sie auch direkt auf der [KI-Katalog-Website](https://gallery.azure.ai/) anzeigen können. 

Zum Anzeigen von veröffentlichten Entitäten rufen Sie die folgende URL auf, wobei Sie `[AuthorId]` durch die Autor-ID ersetzen, die Sie im Abschnitt [Abrufen einer Autor-ID](#get-an-author-id) weiter oben erhalten haben.

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'`

Beispiel:

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'`

### <a name="view-unlisted-and-public-entities"></a>Anzeigen von nicht aufgelisteten und öffentlichen Entitäten

Bei dieser Abfrage werden nur öffentliche Entitäten angezeigt. Um alle Entitäten (einschließlich der nicht aufgelisteten Entitäten) anzuzeigen, stellen Sie das Zugriffstoken bereit, das Sie im vorherigen Abschnitt erhalten haben.

1.    Erstellen Sie mit einem Tool wie [Postman](https://www.getpostman.com) eine HTTP-GET-Anforderung an die Katalog-URL, wie in [Abrufen eines Zugriffstokens](#get-your-access-token) beschrieben.
2.    Erstellen Sie einen HTTP-Anforderungsheader namens `DataLabAccessToken` mit dem für das Zugriffstoken festgelegten Wert.
3.    Übergeben Sie die HTTP-Anforderung.

> [!TIP]
> Wenn nicht aufgelistete Entitäten in den Antworten der Katalog-API nicht angezeigt werden, hat der Benutzer möglicherweise ein ungültiges oder abgelaufenes Zugriffstoken verwendet. Melden Sie sich vom Azure KI-Katalog ab, und wiederholen Sie die unter [Abrufen eines Zugriffstokens](#get-your-access-token) beschriebenen Schritte, um das Token zu erneuern. 
