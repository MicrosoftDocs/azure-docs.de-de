---
title: Metadateneigenschaften von Inhalten
titleSuffix: Azure Cognitive Search
description: Metadateneigenschaften von Blobs können Inhalte für Felder in einem Suchindex oder Informationen liefern, die das Indizierungsverhalten zur Laufzeit bestimmen. In diesem Artikel sind die von Azure Cognitive Search unterstützten Metadateneigenschaften aufgeführt.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 79443785dbd8619e22358631c9c36a3da4ef2e84
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99477286"
---
# <a name="content-metadata-properties-used-in-blob-indexing-in-azure-cognitive-search"></a>Metadateneigenschaften von Inhalten, die bei der Blobindizierung in Azure Cognitive Search verwendet werden

Blobs können verschiedene Inhalte aufweisen, von denen viele über Metadateneigenschaften verfügen, die bei der Indizierung von Blobs nützlich sein können. So wie Sie Suchfelder für standardmäßige Blobeigenschaften wie **`metadata_storage_name`** erstellen können, lassen sich auch Felder für Metadateneigenschaften erstellen, die für ein Dokumentformat spezifisch sind.

## <a name="supported-document-formats"></a>Unterstützte Dokumentformate

Azure Cognitive Search unterstützt die Blobindizierung für die folgenden Dokumentformate:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Eigenschaften nach Dokumentformat

In der folgenden Tabelle sind die Verarbeitungsschritte für jedes Dokumentformat zusammengefasst. Es werden die Metadateneigenschaften beschrieben, die von einem Blobindexer extrahiert werden.

| Dokumentformat/Inhaltstyp | Extrahierte Metadaten | Verarbeitungsdetails |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Entfernen von HTML-Markup und Extrahieren von Text |
| PDF (application/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrahieren von Text, z. B. eingebettete Dokumente (mit Ausnahme von Bildern) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahieren von Text, z. B. eingebettete Dokumente |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahieren von Text, z. B. eingebettete Dokumente |
| DOCM (application/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahieren von Text, z. B. eingebettete Dokumente |
| WORD XML (application/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Entfernen von XML-Markup und Extrahieren von Text |
| WORD 2003 XML (application/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Entfernen von XML-Markup und Extrahieren von Text |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahieren von Text, z. B. eingebettete Dokumente |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahieren von Text, z. B. eingebettete Dokumente |
| XLSM (application/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahieren von Text, z. B. eingebettete Dokumente |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahieren von Text, z. B. eingebettete Dokumente |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahieren von Text, z. B. eingebettete Dokumente |
| PPTM (application/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahieren von Text, z. B. eingebettete Dokumente |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahieren von Text (einschließlich aus Anlagen extrahierter Text). `metadata_message_to_email`, `metadata_message_cc_email` und `metadata_message_bcc_email` sind Zeichenfolgensammlungen, die übrigen Felder sind Zeichenfolgen.|
| ODT (application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahieren von Text, z. B. eingebettete Dokumente |
| ODS (application/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahieren von Text, z. B. eingebettete Dokumente |
| ODP (application/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extrahieren von Text, z. B. eingebettete Dokumente |
| ZIP (application/zip) |`metadata_content_type` |Extrahieren von Text aus allen Dokumenten im Archiv |
| GZ (application/gzip) |`metadata_content_type` |Extrahieren von Text aus allen Dokumenten im Archiv |
| EPUB (application/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrahieren von Text aus allen Dokumenten im Archiv |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Entfernen von XML-Markup und Extrahieren von Text |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrahieren von Text<br/>HINWEIS:  Wenn Sie mehrere Felder des Dokuments aus einem JSON-Blob extrahieren möchten, helfen Ihnen die ausführlichen Informationen unter [Indizierung der JSON-Blobs](search-howto-index-json-blobs.md) weiter. |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrahieren von Text, einschließlich Anlagen |
| RTF (application/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrahieren von Text|
| Nur-Text (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrahieren von Text|

## <a name="see-also"></a>Weitere Informationen

* [Indexer in der kognitiven Azure-Suche](search-indexer-overview.md)
* [Verstehen von Blob Storage-Daten mithilfe von KI](search-blob-ai-integration.md)
* [Übersicht über Blobindizierung](search-blob-storage-integration.md)