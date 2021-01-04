---
title: 'Einschränkungen und Begrenzungen: QnA Maker'
description: QnA Maker weist Metagrenzwerte für Teile der Wissensdatenbank und des Diensts auf. Es ist wichtig, Ihre Wissensdatenbank innerhalb dieser Grenzwerte zu halten, um sie testen und veröffentlichen zu können.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 1a384eed542d0c09a973e0e68288f9fc1660cc96
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380899"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Grenzwerte und Grenzen für QnA Maker-Wissensdatenbanken

Bei den folgenden QnA Maker-Grenzwerten handelt es sich um eine Kombination aus den [Grenzwerten für den Azure Cognitive Search-Dienst](../../search/search-limits-quotas-capacity.md) und den [Grenzwerten für den QnA Maker-Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Sie müssen mit beiden Grenzwerten vertraut sein, um zu verstehen, wie viele Wissensdatenbanken Sie pro Ressource erstellen können und wie groß die Wissensdatenbanken jeweils werden können.

## <a name="knowledge-bases"></a>Wissensdatenbanken

Die maximale Anzahl von Wissensdatenbanken basiert auf den [Grenzwerten für den Azure Cognitive Search-Dienst](../../search/search-limits-quotas-capacity.md).

|**Azure Cognitive Search-Tarif** | **Free** | **Grundlegend** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximal zulässige Anzahl der veröffentlichten Wissensdatenbanken|2|14|49|199|199|2\.999|

 Wenn Ihr Tarif beispielsweise 15 zulässige Indizes aufweist, können Sie 14 Wissensdatenbanken veröffentlichen (1 Index pro veröffentlichter Wissensdatenbank). Der fünfzehnte Index (`testkb`) wird für alle Wissensdatenbanken zum Erstellen und Testen verwendet.

## <a name="extraction-limits"></a>Grenzwerte für die Extraktion

### <a name="file-naming-constraints"></a>Benennungskonventionen für Dateien

Dateinamen dürfen keines der folgenden Zeichen enthalten:

|Verwenden Sie nicht das Zeichen|
|--|
|Einfache Anführungszeichen `'`|
|Doppelte Anführungszeichen `"`|

### <a name="maximum-file-size"></a>Maximale Dateigröße

|Format|Max. Dateigröße (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximale Anzahl von Dateien

Die maximale Anzahl von Dateien, die extrahiert werden können, und die maximale Dateigröße basieren auf den **[Grenzwerten für Ihren QnA Maker-Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

> [!NOTE]
> QnA Maker verwaltet (Vorschau) ist ein kostenloser Dienst ohne Begrenzung der Anzahl der Quellen, die hinzugefügt werden können. Der Durchsatz ist derzeit auf 10 Transaktionen pro Sekunde sowohl für Verwaltungs-APIs als auch für Vorhersage-APIs begrenzt.

### <a name="maximum-number-of-deep-links-from-url"></a>Maximale Anzahl von Deep-Links für die URL

Die maximale Anzahl von Deep-Links, die zum Extrahieren von Fragen und Antworten (QnA) aus einer URL-Seite durchforstet werden können, beträgt **20**.

## <a name="metadata-limits"></a>Grenzwerte für Metadaten

Metadaten werden als textbasiertes Schlüssel-Werte-Paar dargestellt, z. B. `product:windows 10`. Die Metadaten werden in Kleinbuchstaben gespeichert und verglichen.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Nach Azure Cognitive Search-Tarif

Die maximale Anzahl von Metadatenfeldern pro Wissensdatenbank basiert auf den **[Grenzwerten für den Azure Cognitive Search-Dienst](../../search/search-limits-quotas-capacity.md)** .

|**Azure Cognitive Search-Tarif** | **Free** | **Grundlegend** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximale Anzahl der Metadatenfelder pro QnA Maker-Dienst (für alle Knowledge Bases)|1\.000|100*|1\.000|1\.000|1\.000|1\.000|

### <a name="by-name-and-value"></a>Nach Name und Wert

Die Länge und die zulässigen Zeichen für den Metadatennamen und -wert sind in der folgenden Tabelle aufgeführt:

|Element|Zulässige Zeichen|RegEx-Musterabgleich|Maximale Anzahl von Zeichen|
|--|--|--|--|
|Name (Schlüssel)|Zulässig:<br>alphanumerische Zeichen (Buchstaben und Ziffern)<br>`_` (Unterstrich)<br> Darf keine Leerzeichen enthalten.|`^[a-zA-Z0-9_]+$`|100|
|Wert|Alles zulässig mit Ausnahme von:<br>`:` (Doppelpunkt)<br>`|` (senkrechter Strich)<br>Nur ein Wert ist zulässig.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Grenzwerte für die Inhalte einer Knowledge Base
Allgemeine Grenzwerte für die Inhalte in der Knowledge Base:
* Länge des Antworttexts: 25.000
* Länge des Fragentexts: 1.000
* Länge des Texts für den Metadatenschlüssel: 100
* Länge des Texts für den Metadatenwert: 500
* Unterstützte Zeichen für den Metadatennamen: Buchstaben, Ziffern und `_`
* Unterstützte Zeichen für den Metadatenwerte: Alle Zeichen außer `:` und `|`
* Länge des Dateinamens: 200
* Unterstützte Dateiformate: „.tsv“, „.pdf“, „.txt“, „.docx“, „.xlsx“.
* Maximale Anzahl von alternativen Fragen: 300
* Maximale Anzahl von Frage-Antwort-Paaren: Abhängig vom ausgewählten **[Azure Cognitive Search-Tarif](../../search/search-limits-quotas-capacity.md#document-limits)** . Ein Frage-Antwort-Paar wird einem Dokument im Azure Cognitive Search-Index zugeordnet.
* URL/HTML-Seite: Eine Million Zeichen

## <a name="create-knowledge-base-call-limits"></a>Grenzwerte für Aufrufe zum Erstellen einer Knowledge Base
Dabei handelt es sich um die Grenzwerte für die einzelnen Aktionen zum Erstellen einer Knowledge Base, d.h. Klicken auf *Create KB* (Knowledge Base erstellen) oder Aufrufen der CreateKnowledgeBase-API.
* Empfohlene maximale Anzahl von alternativen Fragen pro Antwort: 300
* Maximale Anzahl von URLs: 10
* Maximale Anzahl von Dateien: 10

## <a name="update-knowledge-base-call-limits"></a>Grenzwerte für Aufrufe zum Aktualisieren einer Knowledge Base
Dabei handelt es sich um die Grenzwerte für die einzelnen Aktualisierungsaktionen, d.h. Klicken auf *Save and train* (Speichern und trainieren) oder Aufrufen der UpdateKnowledgeBase-API.
* Länge jedes Quellnamens: 300
* Empfohlene maximale Anzahl hinzugefügter oder gelöschter alternativer Fragen: 300
* Maximale Anzahl hinzugefügter oder gelöschter Metadatenfelder: 10
* Maximale Anzahl der URLs, die aktualisiert werden können: 5

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wann und wie [Diensttarife](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) geändert werden:
