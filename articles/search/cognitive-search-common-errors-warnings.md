---
title: Indexerfehler und -warnungen
titleSuffix: Azure Cognitive Search
description: Dieser Artikel enthält Informationen und Lösungen für häufige Fehler und Warnungen, die bei der KI-Anreicherung in der kognitiven Azure-Suche auftreten können.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: dcdc05ae696a207546eb62160fe89ea38d307058
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492266"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Beheben von häufigen Fehler und Warnungen bei Suchindexern in Azure Cognitive Search

Dieser Artikel enthält Informationen und Lösungen für häufige Fehler und Warnungen, die bei der Indizierung und KI-Anreicherung in Azure Cognitive Search auftreten können.

Die Indizierung wird angehalten, wenn die Fehleranzahl ['maxFailedItems'](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures) überschreitet. 

Wenn Sie möchten, dass Indexer diese Fehler ignorieren (und „fehlerhafte Dokumente“ überspringen), sollten Sie `maxFailedItems` und `maxFailedItemsPerBatch` wie [hier](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers) beschrieben aktualisieren.

> [!NOTE]
> Jedes fehlerhafte Dokumente mit dem dazugehörigen Dokumentschlüssel (falls verfügbar) wird im Status der Indexer-Ausführung als Fehler angezeigt. Sie können die [Index-API](/rest/api/searchservice/addupdate-or-delete-documents) verwenden, um die Dokumente zu einem späteren Zeitpunkt manuell hochzuladen, wenn Sie festgelegt haben, dass der Indexer Fehler toleriert.

Anhand der Fehlerinformationen in diesem Artikel können Sie Fehler beheben, damit die Indizierung fortgesetzt werden kann.

Die Indizierung wird von Warnungen nicht beendet, aber Warnungen weisen auf Bedingungen hin, die zu unerwarteten Ergebnissen führen können. Ob Sie Maßnahmen ergreifen oder nicht, hängt von den Daten und Ihrem Szenario ab.

Ab API-Version `2019-05-06` sind Indexerfehler und -warnungen auf der Elementebene strukturiert, sodass Ursachen und weiterführende Schritte besser verständlich sind. Sie enthalten die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| Schlüssel | Die Dokument-ID des Dokuments, das von dem Fehler oder der Warnung betroffen ist. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | Der Vorgangsname, der beschreibt, wo der Fehler oder die Warnung aufgetreten ist. Dies wird durch die folgende Struktur generiert: [category].[subcategory].[resourceType].[resourceName] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | Eine allgemeine Beschreibung des Fehlers oder der Warnung. | Skill konnte wegen eines Fehlers bei der Anforderung der Web-API nicht ausgeführt werden. |
| Details | Alle zusätzlichen Details, die beim Diagnostizieren des Problems hilfreich sein können, z. B. die WebApi-Antwort, wenn die Ausführung eines benutzerdefinierten Skills fehlgeschlagen ist. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 source, Func`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` ...Rest der Stapelüberwachung... |
| documentationLink | Ein Link zur relevanten Dokumentation mit ausführlichen Informationen zum Debuggen und Beheben des Problems. Dieser Link verweist häufig auf einen der folgenden Abschnitte auf dieser Seite. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>Error: Dokument konnte nicht gelesen werden

Der Indexer konnte das Dokument nicht aus der Datenquelle lesen. Möglich sind folgende Ursachen:

| `Reason` | Details/Beispiel | Lösung |
| --- | --- | --- |
| Inkonsistente Feldtypen in verschiedenen Dokumenten | „Der Typ des Werts stimmt nicht mit dem Spaltentyp überein. `'{47.6,-122.1}'` konnte nicht in der Autorenspalte gespeichert werden.  Erwarteter Typ: JArray.“  „Fehler beim Konvertieren des Datentyps nvarchar in float.“  „Fehler beim Konvertieren des %nvarchar-Werts ‚12 Monate‘ in den Datentyp int.“  "Arithmetischer Überlauffehler beim Konvertieren des Ausdrucks in den Datentyp int." | Stellen Sie sicher, dass der Typ aller Felds in verschiedenen Dokumenten identisch ist. Wenn das `'startTime'`-Feld des ersten Dokuments z.B. den Datentyp DateTime aufweist und im zweiten Dokument eine Zeichenfolge ist, wird dieser Fehler angezeigt. |
| Fehler aus dem zugrunde liegenden Dienst der Datenquelle | (aus Cosmos DB) `{"Errors":["Request rate is large"]}` | Überprüfen Sie die Speicherinstanz, um sicherzustellen, dass Sie fehlerfrei ist. Möglicherweise müssen Sie die Skalierung/Partitionierung anpassen. |
| Vorübergehende Probleme | Beim Empfangen von Ergebnissen vom Server ist ein Fehler auf Übertragungsebene aufgetreten. (Anbieter: TCP-Anbieter, Fehler: 0 – Eine vorhandene Verbindung wurde erzwungenermaßen vom Remotehost geschlossen | Gelegentlich treten unerwartete Konnektivitätsprobleme auf. Versuchen Sie später erneut, das Dokument über den Indexer laufen zu lassen. |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Error: Inhalte oder Metadaten konnten nicht aus dem Dokument extrahiert werden.
Der Indexer mit einer Blob-Datenquelle konnte die Inhalte oder Metadaten nicht aus dem Dokument extrahieren (beispielsweise aus einer PDF-Datei). Möglich sind folgende Ursachen:

| `Reason` | Details/Beispiel | Lösung |
| --- | --- | --- |
| Blob überschreitet das Größenlimit | Das Dokument ist `'150441598'` Bytes groß und überschreitet daher das Größenlimit von `'134217728'` Bytes für die Dokumentenextrahierung für Ihre aktuelle Dienstebene. | [Blob-Indizierungsfehler](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| Blob weist nicht unterstützten Inhaltstyp auf | Das Dokument weist den nicht unterstützten Inhaltstyp `'image/png'` auf | [Blob-Indizierungsfehler](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| Blob ist verschlüsselt | Das Dokument konnte nicht verarbeitet werden. Es ist möglicherweise verschlüsselt oder kennwortgeschützt. | Sie können das Blob mit [Blob-Einstellungen](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex) überspringen. |
| Vorübergehende Probleme | „Fehler beim Verarbeiten des Blobs: Anforderung abgebrochen: Die Anforderung wurde angebrochen.“ „Zeitüberschreitung beim Verarbeiten des Dokuments.“ | Gelegentlich treten unerwartete Konnektivitätsprobleme auf. Versuchen Sie später erneut, das Dokument über den Indexer laufen zu lassen. |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>Error: Dokument konnte nicht analysiert werden
Der Indexer hat das Dokument aus der Datenquelle gelesen, aber es gab ein Problem beim Konvertieren des Dokumentinhalts in das angegebene Feldzuordnungsschema. Möglich sind folgende Ursachen:

| `Reason` | Details/Beispiel | Lösung |
| --- | --- | --- |
| Dokumentschlüssel fehlt | Der Dokumentschlüssel darf nicht fehlen oder leer sein | Stellen Sie sicher, dass alle Dokumente über gültige Dokumentschlüssel verfügen. Der Dokumentschlüssel wird durch Festlegen der Eigenschaft „key“ als Teil der [Indexdefinition](/rest/api/searchservice/create-index#request-body) bestimmt. Indexer geben diesen Fehler aus, wenn die als „key“ gekennzeichnete Eigenschaft in einem bestimmten Dokument nicht gefunden werden kann. |
| Dokumentschlüssel ist ungültig | Der Dokumentschlüssel darf nicht länger als 1024 Zeichen sein | Ändern Sie den Dokumentschlüssel so, dass er die Überprüfungsanforderungen erfüllt. |
| Feldzuordnung konnte nicht auf ein Feld angewendet werden | Die Zuordnungsfunktion `'functionName'` konnte nicht auf das Feld `'fieldName'` angewendet werden. Das Array darf nicht NULL sein. Parametername: Bytes | Überprüfen Sie die [Feldzuordnungen](search-indexer-field-mappings.md), die im Indexer definiert sind, und vergleichen Sie sie mit den Daten des angegebenen Felds des fehlerhaften Dokuments. Es kann erforderlich sein, die Feldzuordnungen oder die Dokumentdaten zu ändern. |
| Feldwert konnte nicht gelesen werden | Der Wert der Spalte `'fieldName'` bei Index `'fieldIndex'` konnte nicht gelesen werden. Beim Empfangen von Ergebnissen vom Server ist ein Fehler auf Übertragungsebene aufgetreten. (Anbieter: TCP-Anbieter, Fehler: 0 – Eine vorhandene Verbindung wurde erzwungenermaßen vom Remotehost geschlossen.) | Diese Fehler sind in der Regel auf unerwartete Konnektivitätsprobleme beim zugrunde liegenden Dienst der Datenquelle zurückzuführen. Versuchen Sie später erneut, das Dokument über den Indexer laufen zu lassen. |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>Error: Das Ausgabefeld „`xyz`“ konnte aufgrund eines Deserialisierungsproblems beim Anwenden der Zuordnungsfunktion „`abc`“ nicht dem Suchindex zugeordnet werden.
Bei der Ausgabezuordnung ist möglicherweise ein Fehler aufgetreten, da die Ausgabedaten für die von Ihnen verwendete Zuordnungsfunktion das falsche Format aufweisen. Wenn Sie z. B. die Base64Encode-Zuordnungsfunktion auf binäre Daten anwenden, wird dieser Fehler generiert. Um das Problem zu beheben, führen Sie den Indexer erneut aus, ohne die Zuordnungsfunktion anzugeben, oder stellen Sie sicher, dass die Zuordnungsfunktion mit dem Datentyp des Ausgabefelds kompatibel ist. Weitere Informationen finden Sie unter [Ausgabefeldzuordnung](cognitive-search-output-field-mapping.md).

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>Error: Skill konnte nicht ausgeführt werden.
Der Indexer konnte einen Skill im Skillset nicht ausführen.

| `Reason` | Details/Beispiel | Lösung |
| --- | --- | --- |
| Vorübergehende Konnektivitätsprobleme | Ein vorübergehender Fehler ist aufgetreten. Versuchen Sie es später noch mal. | Gelegentlich treten unerwartete Konnektivitätsprobleme auf. Versuchen Sie später erneut, das Dokument über den Indexer laufen zu lassen. |
| Potenzieller Produktfehler | Ein unerwarteter Fehler ist aufgetreten. | Dies weist auf eine unbekannte Fehlerklasse hin und kann bedeuten, dass ein Produktfehler vorliegt. Erstellen Sie ein [Supportticket](https://ms.portal.azure.com/#create/Microsoft.Support), um Hilfe zu erhalten. |
| Fehler bei der Ausführung eines Skills | (Aus „Skill für Zusammenführung“:) Mindestens ein Offsetwert war ungültig und konnte nicht analysiert werden. Am Ende des Texts wurden Elemente eingefügt. | Beheben Sie das Problem anhand der Informationen in der Fehlermeldung. Für die Behebung dieser Art von Fehler sind Maßnahmen erforderlich. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Error: Skill konnte wegen eines Fehlers bei der Anforderung der Web-API nicht ausgeführt werden.
Bei der Ausführung eines Skills ist aufgrund eines Fehlers beim Aufruf der Web-API ein Fehler aufgetreten. Diese Fehlerklasse wird normalerweise angegeben, wenn benutzerdefinierte Skills verwendet werden. In diesem Fall müssen Sie den benutzerdefinierten Code debuggen, um das Problem zu beheben. Wenn der Fehler stattdessen einen integrierten Skill betrifft, finden Sie in der Fehlermeldung Hilfe zum Beheben des Problems.

Achten Sie beim Debuggen dieses Problems auf alle [Qualifikationseingaben-Warnungen](#warning-skill-input-was-invalid) für diese Qualifikation. Bei Ihrem Web-API-Endpunkt kann ein Fehler auftreten, weil ihm vom Indexer unerwartete Eingaben übergeben werden.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Error: Skill konnte wegen ungültiger Antwort auf den Web-API-Skill nicht ausgeführt werden.
Bei der Ausführung eines Skills ist ein Fehler aufgetreten, da beim Aufruf der Web-API eine ungültige Antwort zurückgegeben wurde. Diese Fehlerklasse wird normalerweise angegeben, wenn benutzerdefinierte Skills verwendet werden. In diesem Fall müssen Sie den benutzerdefinierten Code debuggen, um das Problem zu beheben. Wenn der Fehler stattdessen einen integrierten Skill betrifft, erstellen Sie ein [Supportticket](https://ms.portal.azure.com/#create/Microsoft.Support), um Hilfe zu erhalten.

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Error: Die Qualifikation wurde nicht innerhalb des Zeitlimits ausgeführt
Es gibt zwei Fälle, in denen möglicherweise diese Fehlermeldung angezeigt wird, die jeweils unterschiedlich behandelt werden sollten. Befolgen Sie die nachfolgenden Anweisungen, je nachdem, welche Qualifikation diesen Fehler zurückgegeben hat.

### <a name="built-in-cognitive-service-skills"></a>Integrierte Cognitive Services-Qualifikationen
Viele der integrierten kognitiven Qualifikationen, wie z.B. Spracherkennung, Entitätserkennung oder OCR, werden von einem Cognitive Service-API-Endpunkt unterstützt. Manchmal treten vorübergehende Probleme mit diesen Endpunkten auf, und eine Anforderung führt zu einem Timeout. Bei vorübergehenden Problemen gibt es keine Abhilfe, außer zu warten und es erneut versuchen. Zur Abhilfe können Sie überlegen, Ihren Indexer auf [Gemäß einem Zeitplan ausführen](search-howto-schedule-indexers.md) festzulegen. Die geplante Indizierung setzt an der Stelle an, an der sie unterbrochen wurde. Wenn die vorübergehenden Probleme gelöst sind, sollten die Indizierung und die Verarbeitung der kognitiven Qualifikationen bei der nächsten geplanten Ausführung fortgesetzt werden können.

Wenn dieser Fehler für einen integrierten kognitiven Skill weiterhin im gleichen Dokument angezeigt wird, reichen Sie ein [Supportticket](https://ms.portal.azure.com/#create/Microsoft.Support) ein, um Unterstützung zu erhalten, denn ein solches Verhalten wird nicht erwartet.

### <a name="custom-skills"></a>Benutzerdefinierte Qualifikationen
Wenn ein Timeout bei einer selbst erstellten benutzerdefinierten Qualifikation auftritt, können Sie verschiedene Dinge ausprobieren. Überprüfen Sie zunächst Ihre benutzerdefinierte Qualifikation, und stellen Sie sicher, dass sie nicht in einer unendlichen Schleife hängen geblieben ist und dass Sie ein konsistentes Ergebnis zurückgibt. Nachdem Sie dies überprüft haben, ermitteln Sie die Ausführungszeit ihrer Qualifikation. Wenn Sie in der Definition Ihrer benutzerdefinierten Qualifikation nicht explizit einen `timeout`-Wert festgelegt haben, beträgt der Standardwert für `timeout` 30 Sekunden. Wenn 30 Sekunden nicht lang genug für die Ausführung Ihrer Qualifikation sind, können Sie einen höheren `timeout`-Wert für die Definition Ihrer benutzerdefinierten Qualifikation angeben. Im folgenden finden Sie ein Beispiel für eine Definition einer benutzerdefinierten Qualifikation, bei der das Timeout auf 90 Sekunden festgelegt ist:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

Der maximale Wert, den Sie für den `timeout`-Parameter festlegen können, beträgt 230 Sekunden.  Wenn Ihre benutzerdefinierte Qualifikation nicht innerhalb von 230 Sekunden konsistent ausgeführt werden kann, sollten Sie ggf. die `batchSize` Ihrer benutzerdefinierten Qualifikation verringern, sodass weniger Dokumente innerhalb einer einzelnen Ausführung verarbeitet werden.  Wenn Sie die `batchSize` bereits auf 1 festgelegt haben, müssen Sie die Qualifikation neu schreiben, damit Sie in unter 230 Sekunden ausgeführt werden kann, oder Sie müssen sie anderweitig in mehrere benutzerdefinierte Qualifikationen aufteilen, damit die Ausführungszeit für jede einzelne benutzerdefinierte Qualifikation maximal 230 Sekunden beträgt. Weitere Informationen finden Sie in der [Dokumentation zu benutzerdefinierten Qualifikationen](cognitive-search-custom-skill-web-api.md).

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Error: „`MergeOrUpload`“ | „`Delete`“ des Dokuments in den Suchindex nicht möglich

Das Dokument wurde gelesen und verarbeitet, aber der Indexer konnte es nicht zum Suchindex hinzufügen. Möglich sind folgende Ursachen:

| `Reason` | Details/Beispiel | Lösung |
| --- | --- | --- |
| Ein Feld enthält einen Begriff, der zu groß ist. | Ein Begriff in Ihrem Dokument ist größer als der [Grenzwert von 32 KB](search-limits-quotas-capacity.md#api-request-limits). | Sie können diese Einschränkung vermeiden, indem Sie sicherstellen, dass das Feld nicht als filterbar, facettierbar oder sortierbar konfiguriert ist.
| Das Dokument ist zu groß für die Indizierung. | Ein Dokument ist größer als die [maximale API-Anforderungsgröße](search-limits-quotas-capacity.md#api-request-limits). | [Indizieren großer Datasets](search-howto-large-index.md)
| Das Dokument enthält zu viele Objekte in der Sammlung. | Bei einer Sammlung in Ihrem Dokument wurde die [maximale Anzahl der Elemente in allen komplexen Sammlungen pro Dokument](search-limits-quotas-capacity.md#index-limits) überschritten. Das Dokument mit dem Schlüssel `'1000052'` enthält `'4303'` Objekte in Sammlungen (JSON-Arrays). Höchstens `'3000'` Objekte sind in Sammlungen im gesamten Dokument zulässig. Entfernen Sie Objekte aus Sammlungen, und indizieren Sie das Dokument erneut. | Wir empfehlen, die komplexe Sammlung im Dokument auf einen Wert unter dem Grenzwert zu verkleinern und eine hohe Speicherauslastung zu vermeiden.
| Probleme beim Herstellen einer Verbindung mit dem Zielindex (die nach Wiederholungsversuchen weiterhin bestehen), da der Dienst durch eine andere Aufgabe ausgelastet ist, z.B. eine Abfrage oder Indizierung. | Die Verbindung zum Hochladen des Indexes konnte nicht hergestellt werden. Der Suchdienst ist stark ausgelastet. | [Hochskalieren des Suchdiensts](search-capacity-planning.md)
| Der Suchdienst wird für das Dienstupdate gepatcht oder befindet sich mitten in einer Neukonfiguration der Topologie. | Die Verbindung zum Hochladen des Indexes konnte nicht hergestellt werden. Der Suchdienst ist derzeit nicht verfügbar / Der Suchdienst befindet sich im Übergang. | Konfigurieren Sie den Dienst mit mindestens 3 Replikaten für eine Verfügbarkeit von 99,9 % gemäß [SLA-Dokumentation](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
| Fehler in der zugrunde liegenden Compute-/Netzwerkressource (selten) | Die Verbindung zum Hochladen des Indexes konnte nicht hergestellt werden. Ein unbekannter Fehler ist aufgetreten. | Konfigurieren Sie Indexer für die [Ausführung nach Zeitplan](search-howto-schedule-indexers.md), um den Betrieb nach einem Fehler wieder aufzunehmen.
| Eine Indizierungsanforderung an den Zielindex wurde aufgrund von Netzwerkproblemen nicht innerhalb eines Zeitlimits bestätigt. | Die Verbindung mit dem Suchindex konnte nicht rechtzeitig hergestellt werden. | Konfigurieren Sie Indexer für die [Ausführung nach Zeitplan](search-howto-schedule-indexers.md), um den Betrieb nach einem Fehler wieder aufzunehmen. Versuchen Sie außerdem, die [Batchgröße](/rest/api/searchservice/create-indexer#parameters) des Indexers zu verringern, wenn dieser Fehler weiterhin auftritt.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Error: Das Dokument konnte nicht indiziert werden, da einige Daten des Dokuments nicht zulässig waren.

Das Dokument wurde vom Indexer gelesen und verarbeitet, konnte jedoch aufgrund eines Konflikts zwischen der Konfiguration der Indexfelder und den vom Indexer extrahierten und verarbeiteten Daten nicht zum Suchindex hinzugefügt werden. Möglich sind folgende Ursachen:

| `Reason` | Details/Beispiel
| --- | ---
| Der Datentyp der vom Indexer extrahierten Felder ist nicht kompatibel mit dem Datenmodell des entsprechenden Zielindexfelds. | Das Datenfeld _data_ im Dokument mit dem Schlüssel „888“ enthält einen ungültigen Wert vom Typ „Edm.String“. Erwartet wurde der Typ „Collection(Edm.String)“. |
| Fehler beim Extrahieren einer JSON-Entität aus einem Zeichenfolgenwert. | Der Wert „of type ‚Edm.String‘“ des Felds _data_ konnte nicht als JSON-Objekt analysiert werden. Fehler: „Nach dem Analysieren eines Werts wurde ein unerwartetes Zeichen gefunden: ''. Pfad _path_ Zeile 1, Position 3162.“ |
| Fehler beim Extrahieren einer Sammlung von JSON-Entitäten aus einem Zeichenfolgenwert.  | Der Wert „of type ‚Edm.String‘“ des Felds _data_ konnte nicht als JSON-Array analysiert werden. Fehler: „Nach dem Analysieren eines Werts wurde ein unerwartetes Zeichen gefunden: ''. Pfad [0], Zeile 1, Position 27.“ |
| Im Quelldokument wurde ein unbekannter Typ gefunden. | Unbekannter Typ _unknown_ kann nicht indiziert werden. |
| Im Quelldokument wurde eine nicht kompatible Notation für Geografiepunkte verwendet. | WKT POINT-Zeichenfolgenliterale werden nicht unterstützt. Verwenden Sie stattdessen GeoJson-Punkt-Literale. |

Beachten Sie in allen diesen Fällen die Informationen unter [Unterstützte Datentypen](/rest/api/searchservice/supported-data-types) und [Datentypzuordnung für Indexer](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search), um sicherzustellen, dass Sie das Indexschema ordnungsgemäß erstellen und geeignete [Indexerfeldzuordnungen](search-indexer-field-mappings.md) eingerichtet haben. Die Fehlermeldung enthält Details, anhand derer die Quelle des Konflikts ermittelt werden kann.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Error: Die integrierte Änderungsnachverfolgungs-Richtlinie kann nicht verwendet werden, da die Tabelle einen zusammengesetzten Primärschlüssel aufweist.

Dies betrifft SQL-Tabellen und geschieht in der Regel, wenn der Schlüssel als zusammengesetzter Schlüssel definiert ist oder wenn in der Tabelle ein eindeutiger gruppierter Index definiert ist (d. h. wie in einem SQL-Index, nicht in einem Azure Search-Index). Der Hauptgrund ist, dass das Schlüsselattribut bei einem [eindeutigen gruppierten Index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) geändert wird, sodass es sich um einen zusammengesetzten Primärschlüssel handelt. Stellen Sie in diesem Fall sicher, dass die SQL-Tabelle keinen eindeutigen gruppierten Index aufweist oder dass Sie das Schlüsselfeld einem Feld zuordnen, das garantiert keine doppelten Werte aufweist.

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Error: Das Dokument konnte nicht innerhalb der maximalen Laufzeit des Indexers verarbeitet werden.

Dieser Fehler tritt auf, wenn der Indexer die Verarbeitung eines einzelnen Dokuments aus der Datenquelle nicht innerhalb der zulässigen Ausführungszeit beenden kann. Die [maximale Ausführungszeit](search-limits-quotas-capacity.md#indexer-limits) ist kürzer, wenn Skillsets verwendet werden. Wenn dieser Fehler auftritt und maxFailedItems auf einen anderen Wert als 0 festgelegt ist, umgeht der Indexer das Dokument bei zukünftigen Ausführungen, sodass die Indizierung fortgesetzt werden kann. Wenn kein Dokument übersprungen werden darf oder wenn dieser Fehler dauerhaft auftritt, sollten Sie die Dokumente in kleinere Dokumente aufteilen, damit ein Teilfortschritt innerhalb einer einzelnen Indexerausführung erfolgen kann.

<a name="could-not-project-document></a>

## <a name="error-could-not-project-document"></a>Error: Das Dokument konnte nicht projiziert werden.

Dieser Fehler tritt auf, wenn der Indexer versucht hat, [Daten in einen Wissensspeicher zu projizieren](knowledge-store-projection-overview.md) und bei diesem Versuch ein Fehler aufgetreten ist.  Dieser Fehler kann konsistent und behebbar oder ein vorübergehender Fehler der Projektionsausgabesenke sein. Um diesen zu beheben, müssen Sie möglicherweise warten und den Vorgang wiederholen.  Im Folgenden finden Sie eine Reihe bekannter Fehlerzustände und möglicher Lösungen.

| `Reason` | Details/Beispiel | Lösung |
| --- | --- | --- |
| Das Projektionsblob `'blobUri'` im Container `'containerName'` konnte nicht aktualisiert werden. |Der angegebene Container ist nicht vorhanden. | Der Indexer überprüft, ob der angegebene Container bereits erstellt wurde, und erstellt ihn bei Bedarf. Diese Überprüfung erfolgt jedoch nur einmal pro Indexer-Ausführung. Dieser Fehler bedeutet, dass der Container nach diesem Schritt gelöscht wurde.  Um diesen Fehler zu beheben, versuchen Sie Folgendes: Nehmen Sie keine Änderungen an Ihren Speicherkontoinformationen vor, warten Sie, bis der Indexer beendet wurde, und führen Sie den Indexer dann erneut aus. |
| Das Projektionsblob `'blobUri'` im Container `'containerName'` konnte nicht aktualisiert werden. |In die Übertragungsverbindung können keine Daten geschrieben werden: An existing connection was forcibly closed by the remote host. | Dies ist voraussichtlich ein vorübergehender Fehler bei Azure Storage, der daher durch erneutes Ausführen des Indexers behoben werden sollte. Wenn dieser Fehler konsistent auftritt, übermitteln Sie ein [Supportticket](https://ms.portal.azure.com/#create/Microsoft.Support), damit er weiter untersucht werden kann.  |
| Zeile `'projectionRow'` in Tabelle `'tableName'` konnte nicht aktualisiert werden. | Der Server ist ausgelastet. | Dies ist voraussichtlich ein vorübergehender Fehler bei Azure Storage, der daher durch erneutes Ausführen des Indexers behoben werden sollte. Wenn dieser Fehler konsistent auftritt, übermitteln Sie ein [Supportticket](https://ms.portal.azure.com/#create/Microsoft.Support), damit er weiter untersucht werden kann.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>Warnung: Die Qualifikationseingabe war ungültig.
Eine Eingabe für die Qualifikation fehlt, hat den falschen Typ oder ist aus einem anderen Grund ungültig. In der Warnmeldung wird die Auswirkung angegeben:
1) Skill konnte nicht ausgeführt werden.
2) Die Qualifikation wurde ausgeführt, hat jedoch unter Umständen unerwartete Ergebnisse.

Für kognitive Skills sind erforderliche Eingaben und optionale Eingaben definiert. Beispielsweise sind für den [Skill „Schlüsselbegriffserkennung“](cognitive-search-skill-keyphrases.md) die zwei erforderlichen Eingaben `text` und `languageCode` und keine optionalen Eingaben definiert. Benutzerdefinierte Qualifikationseingaben gelten jeweils als optionale Eingaben.

Wenn erforderliche Eingaben fehlen oder der Typ einer Eingabe nicht korrekt ist, wird die Qualifikation übersprungen und eine Warnung generiert. Übersprungene Skills generieren keine Ausgaben. Wenn andere Skills die Ausgaben des übersprungenen Skills verwenden, werden daher möglicherweise weitere Warnungen generiert.

Fehlt eine optionale Eingabe, wird die Qualifikation zwar ausgeführt, die fehlende Eingabe kann jedoch zu einer unerwarteten Ausgabe führen.

In beiden Fällen wird diese Warnung unter Umständen aufgrund der Form der Daten erwartet. Ein Beispiel: Bei einem Dokument mit Personeninformationen in Form der Felder `firstName`, `middleName` und `lastName` ist möglicherweise bei einigen Dokumenten keine Angabe für `middleName` vorhanden. Wenn Sie `middleName` als Eingabe an eine Qualifikation in der Pipeline übergeben, ist damit zu rechnen, dass diese Qualifikationseingabe nicht immer vorhanden ist. Ob aufgrund dieser Warnung eine Aktion erforderlich ist, hängt von Ihren Daten und von Ihrem Szenario ab.

Wenn Sie einen Standardwert für fehlende Eingaben angeben möchten, können Sie den [Skill „Bedingt“](cognitive-search-skill-conditional.md) verwenden, um einen Standardwert zu generieren. Verwenden Sie dann die Ausgabe des [Skills „Bedingt“](cognitive-search-skill-conditional.md) als Skilleingabe.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| `Reason` | Details/Beispiel | Lösung |
| --- | --- | --- |
| Die Skilleingabe weist den falschen Typ auf. | „Bei der erforderlichen Qualifikationseingabe handelt es sich nicht um eine Eingabe des erwarteten Typs (`String`). Name: `text`, Quelle: `/document/merged_content`.“  „Die erforderliche Qualifikationseingabe hat nicht das erwartete Format. Name: `text`, Quelle: `/document/merged_content`.“  „Durchlaufen des Nichtarrays `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` nicht möglich.“  „Auswählen von `0` im Nichtarray `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` nicht möglich.“ | Bei bestimmten Skills wird die Eingabe spezifischer Typen erwartet, beim [Skill „Stimmung“](cognitive-search-skill-sentiment.md) wird beispielsweise erwartet, dass `text` eine Zeichenfolge ist. Wenn die Eingabe einen anderen Wert als einen Zeichenfolgenwert angibt, wird der Skill nicht ausgeführt, und es werden keine Ausgaben generiert. Stellen Sie sicher, dass das Dataset Eingabewerte mit einheitlichem Typ enthält, oder verwenden Sie den [Skill „Benutzerdefinierte Web-API“](cognitive-search-custom-skill-web-api.md), um die Eingabe vorzuverarbeiten. Wenn Sie den Skill über ein Array durchlaufen, überprüfen Sie, ob `*` im Skillkontext und in der Eingabe an der jeweils richtigen Position gesetzt ist. Normalerweise sollten der Kontext und die Eingabequelle für Arrays mit `*` enden. |
| Die Skilleingabe fehlt. | „Eine erforderliche Qualifikationseingabe fehlt. Name: `text`, Quelle: `/document/merged_content`“ „Fehlender Wert: `/document/normalized_images/0/imageTags`.“  „Auswählen von `0` im Array `/document/pages` mit der Länge `0` nicht möglich.“ | Wenn diese Warnung für alle Ihre Dokumente angezeigt wird, enthalten die Eingabepfade sehr wahrscheinlich einen Tippfehler. Überprüfen Sie die Groß- und Kleinschreibung der Eigenschaftennamen. Prüfen Sie außerdem den Pfad auf überflüssige oder fehlende Sternchen (`*`), und stellen Sie sicher, dass die Dokumente aus der Datenquelle die erforderlichen Eingaben liefern. |
| Der Sprachcode für die Skilleingabe ist ungültig. | Die Skilleingabe `languageCode` enthält die Sprachcodes `X,Y,Z`, von denen mindestens einer ungültig ist. | [Weiter unten](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) finden Sie weitere Details. |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Warnung:  Qualifikationseingabe „languageCode“ enthält die Sprachcodes „X, Y, Z“, wovon mindestens einer ungültig ist.
Mindestens einer der an die optionale `languageCode`-Eingabe einer Downstreamqualifikation übergebenen Werte wird nicht unterstützt. Dies kann vorkommen, wenn Sie die Ausgabe von [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) an nachfolgende Qualifikationen übergeben und die Ausgabe aus mehr Sprachen besteht, als in diesen Downstreamqualifikationen unterstützt werden.

Wenn Sie wissen, dass Ihr Dataset nur eine einzige Sprache enthält, sollten Sie die [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) und die `languageCode`-Qualifikationseingabe entfernen und stattdessen den Qualifikationsparameter `defaultLanguageCode` für diese Qualifikation verwenden, vorausgesetzt, die Sprache wird für diese Qualifikation unterstützt.

Wenn Sie wissen, dass Ihr Dataset mehrere Sprachen enthält und Sie daher die Eingaben [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) und `languageCode` benötigen, sollten Sie ggf. eine [ConditionalSkill](cognitive-search-skill-conditional.md)-Qualifikation hinzufügen, um Text in nicht unterstützten Sprachen herauszufiltern, bevor der Text an die Downstreamqualifikation übergeben wird.  Hier ein Beispiel, wie dies für die Qualifikation „EntityRecognitionSkill“ aussehen könnte:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Im folgenden finden Sie einige Referenzen für die derzeit unterstützten Sprachen für jede der Qualifikationen, die diese Fehlermeldung verursachen können:
* [Unterstützte Sprachen für die Textanalyse](../cognitive-services/text-analytics/language-support.md) (für die Qualifikationen [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md) und [PIIDetectionSkill](cognitive-search-skill-pii-detection.md))
* [Unterstützte Sprachen für den Übersetzer](../cognitive-services/translator/language-support.md) (für die Qualifikation [Text TranslationSkill](cognitive-search-skill-text-translation.md))
* [Text SplitSkill](cognitive-search-skill-textsplit.md) Unterstützte Sprachen: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>Warnung: Die Qualifikationseingabe wurde abgeschnitten
Für kognitive Skills gelten Beschränkungen hinsichtlich der Länge von Text, der gleichzeitig analysiert werden kann. Wenn die Texteingabe dieser Qualifikationen diesen Grenzwert überschreitet, wird der Text abgeschnitten, um den Grenzwert einzuhalten, und die Anreicherung wird anschließend für diesen abgeschnittene Text ausgeführt. Dies bedeutet, dass die Qualifikation ausgeführt wird, aber nicht für all Ihre Daten.

Im unten gezeigten LanguageDetectionSkill-Beispiel kann das `'text'`-Eingabefeld diese Warnung auslösen, wenn das Zeichenlimit überschritten wird. Die Grenzwerte für Qualifikationseingaben finden Sie in der [Dokumentation zu Qualifikationen](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Wenn Sie sicherstellen möchten, dass der gesamte Text analysiert wird, sollten Sie die [Qualifikation „Aufteilung“](cognitive-search-skill-textsplit.md) verwenden.

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Warnung: Antwort auf Web-API-Skill enthält Warnungen.
Der Indexer konnte einen Skill im Skillset ausführen, aber die Antwort auf die Web-API-Anforderung ergab, dass während der Ausführung Warnungen aufgetreten sind. Überprüfen Sie die Warnungen, um zu verstehen, wie sich dies auf die Daten auswirkt und ob Handlungsbedarf besteht.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Warnung: Die aktuelle Indexerkonfiguration unterstützt keinen inkrementellen Fortschritt.

Diese Warnung tritt nur bei Cosmos DB-Datenquellen auf.

Der inkrementelle Status während der Indizierung stellt sicher, dass der Indexer bei der nächsten Ausführung an der gleichen Stelle fortgesetzt werden kann und nicht die gesamte Sammlung von vorn indizieren muss, wenn er durch vorübergehende Fehler oder das Erreichen des Ausführungszeitlimits unterbrochen wird. Dies ist besonders beim Indizieren umfangreicher Sammlungen wichtig.

Die Möglichkeit, einen nicht abgeschlossenen Indizierungsauftrag fortzusetzen, basiert darauf, dass Dokumente nach der Spalte `_ts` sortiert sind. Der Indexer bestimmt anhand des Zeitstempels, welches Dokument als Nächstes abgerufen wird. Wenn die Spalte `_ts` fehlt oder der Indexer nicht ermitteln kann, ob eine benutzerdefinierte Abfrage nach dieser Spalte sortiert ist, beginnt der Indexer am Anfang, und diese Warnung wird angezeigt.

Dieses Verhalten kann außer Kraft gesetzt werden, indem durch Verwendung der Konfigurationseigenschaft `assumeOrderByHighWatermarkColumn` der inkrementelle Fortschritt aktiviert und diese Warnung unterdrückt wird.

Weitere Informationen finden Sie unter [Inkrementeller Status und benutzerdefinierte Abfragen](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Warnung: Einige Daten sind während der Projektion verloren gegangen. Zeile „X“ in Tabelle „Y“ weist Zeichenfolgeneigenschaft „Z“ auf, die zu lang war.

Im [Table Storage-Dienst](https://azure.microsoft.com/services/storage/tables) ist die Größe der [Entitätseigenschaften](/rest/api/storageservices/understanding-the-table-service-data-model#property-types) begrenzt. Zeichenfolgen können maximal 32.000 Zeichen enthalten. Wird eine Zeile mit einer Zeichenfolgeneigenschaft, die länger als 32.000 Zeichen ist, projiziert, werden nur die ersten 32.000 Zeichen beibehalten. Um dieses Problem zu umgehen, vermeiden Sie das Projizieren von Zeilen mit Zeichenfolgeneigenschaften, die länger als 32.000 Zeichen sind.

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Warnung: Der extrahierte Text wurde auf x Zeichen gekürzt.
Indexer begrenzen die pro Dokument extrahierbare Textmenge. Dieser Grenzwert ist tarifabhängig: 32.000 Zeichen für den Tarif „Free“, 64.000 Zeichen für den Tarif „Basic“, 4 Millionen Zeichen für den Tarif „Standard“, 8 Millionen Zeichen für den Tarif „Standard S2“ und 16 Millionen Zeichen für den Tarif „Standard S3“. Abgeschnittener Text wird nicht indiziert. Zur Vermeidung dieser Warnung können Sie versuchen, Dokumente mit umfangreichem Text in mehrere kleinere Dokumente aufzuteilen. 

Weitere Informationen finden Sie unter [Indexergrenzwerte](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Warnung: Das Ausgabefeld „X“ konnte dem Suchindex nicht zugeordnet werden.
Ausgabefeldzuordnungen, die auf nicht vorhandene oder Nulldaten verweisen, führen zu Warnungen für jedes Dokument und damit zu einem leeren Indexfeld. Um dieses Problem zu umgehen, überprüfen Sie die Quellpfade des Ausgabefelds auf mögliche Tippfehler, oder legen Sie mit dem [bedingten Skill](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist) einen Standardwert fest. Weitere Informationen finden Sie unter [Ausgabefeldzuordnung](cognitive-search-output-field-mapping.md).

| `Reason` | Details/Beispiel | Lösung |
| --- | --- | --- |
| Durchlaufen des Nichtarrays nicht möglich | „Durchlaufen des Nichtarrays `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` nicht möglich.“ | Dieser Fehler tritt auf, wenn die Ausgabe kein Array ist. Wenn Sie der Ansicht sind, dass die Ausgabe ein Array sein sollte, überprüfen Sie den Pfad des angegebenen Ausgabequellfelds. Es könnte beispielsweise ein `*` im Namen des Quellfelds fehlen oder zu viel sein. Es ist auch möglich, dass die Eingabe für diese Qualifikation NULL ist, was zu einem leeren Array führt. Ähnliche Details finden Sie im Abschnitt [Die Qualifikationseingabe war ungültig](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid).    |
| Auswählen von `0` im Nichtarray nicht möglich | „Auswählen von `0` im Nichtarray `/document/pages` nicht möglich.“ | Dies kann vorkommen, wenn die Qualifikationsausgabe kein Array erzeugt und der Name des Ausgabequellfelds einen Arrayindex oder `*` im Pfad aufweist. Überprüfen Sie die in den Feldnamen der Ausgabequelle angegebenen Pfade und den Feldwert für den angegebenen Feldnamen. Ähnliche Details finden Sie im Abschnitt [Die Qualifikationseingabe war ungültig](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid).  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Warnung: Die Richtlinie zur Erkennung von Datenänderungen ist für die Verwendung der Schlüsselspalte „X“ konfiguriert.
[Richtlinien für die Datenänderungserkennung](/rest/api/searchservice/create-data-source#data-change-detection-policies) weisen bestimmte Anforderungen an die zum Erkennen von Änderungen verwendeten Spalten auf. Eine dieser Anforderungen besteht darin, dass diese Spalte jedes Mal aktualisiert wird, wenn das Quellelement geändert wird. Eine weitere Anforderung besteht darin, dass der neue Wert für diese Spalte größer als der vorherige Wert ist. Schlüsselspalten erfüllen diese Anforderung nicht, da Sie nicht bei jeder Aktualisierung geändert werden. Um dieses Problem zu umgehen, wählen Sie eine andere Spalte für die Änderungserkennungsrichtlinie aus.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Warnung: Der Dokumenttext scheint UTF-16-codiert zu sein, es fehlt jedoch eine Bytereihenfolge-Marke.

Die [Indexer-Analysemodi](/rest/api/searchservice/create-indexer#blob-configuration-parameters) müssen erkennen, wie Text codiert ist, bevor die Analyse gestartet werden kann. Die beiden gängigsten Methoden zum Codieren von Text sind UTF-16 und UTF-8. UTF-8 ist eine Codierung mit variabler Länge, bei der jedes Zeichen eine Länge zwischen 1 Byte und 4 Bytes aufweist. UTF-16 ist eine Codierung mit fester Länge, bei der jedes Zeichen 2 Bytes lang ist. Für UTF-16 gibt es zwei verschiedene Varianten: „Big-Endian“ und „Little-Endian“. Die Textcodierung wird durch eine sogenannte Bytereihenfolge-Marke bestimmt, eine Folge von Bytes vor dem Text.

| Codieren | Bytereihenfolge-Marke |
| --- | --- |
| UTF-16 Big-Endian | 0xFE 0xFF |
| UTF-16 Little-Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Wenn keine Bytereihenfolge-Marke vorhanden ist, wird eine UTF-8-Codierung des Texts angenommen.

Um diese Warnung zu umgehen, ermitteln Sie die Textcodierung für dieses Blob, und fügen Sie die entsprechende Bytereihenfolge-Marke hinzu.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Warnung: Cosmos DB Sammlung „X“ weist eine Indizierungsrichtlinie vom Typ „Verzögert“ auf. Unter Umständen gehen einige Daten verloren.

Sammlungen mit Indizierungsrichtlinien vom Typ [Verzögert](../cosmos-db/index-policy.md#indexing-mode) können nicht konsistent abgefragt werden, was dazu führt, dass Ihr Indexer Daten auslässt. Um diese Warnung zu umgehen, ändern Sie die Indizierungsrichtlinie in „Konsistent“.

## <a name="warning-the-document-contains-very-long-words-longer-than-64-characters-these-words-may-result-in-truncated-andor-unreliable-model-predictions"></a>Warnung: Das Dokument enthält sehr lange Wörter (länger als 64 Zeichen). Diese Wörter können zu abgeschnittenen und/oder unzuverlässigen Modellvorhersagen führen.

Diese Warnung wird vom Textanalysedienst weitergeleitet.  In einigen Fällen kann diese Warnung problemlos ignoriert werden, z. B. wenn Ihr Dokument eine lange URL enthält (bei der es sich wahrscheinlich nicht um einen Schlüsselausdruck oder eine steuernde Stimmungsanalyse usw. handelt).  Beachten Sie, dass ein Wort, das länger als 64 Zeichen ist, auf 64 Zeichen gekürzt wird, was die Modellvorhersagen beeinträchtigen kann.