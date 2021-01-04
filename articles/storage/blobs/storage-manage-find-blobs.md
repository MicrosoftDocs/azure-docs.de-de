---
title: Verwalten und Finden von Azure-Blobdaten mit Blobindextags (Vorschau)
description: Erfahren Sie, wie Sie Blobindextags verwenden, um Blobobjekte zu kategorisieren, zu verwalten und abzufragen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/19/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 4f84c3c2f6fc671a8cb6ac70313361540e3dd815
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523279"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Verwalten und Finden von Azure-Blobdaten mit Blobindextags (Vorschau)

Wenn der Umfang von Datasets zunimmt, kann die Suche nach einem bestimmten Objekt schwierig sein. Blobindextags verfügen über Funktionen für die Datenverwaltung und -ermittlung, indem Schlüssel-Wert-Indextagattribute verwendet werden. Sie können Objekte in einem einzelnen Container oder über alle Container Ihres Speicherkontos hinweg kategorisieren und suchen. Bei sich ändernden Datenanforderungen können Objekte dynamisch kategorisiert werden, indem die entsprechenden Indextags aktualisiert werden. Hierbei können die Objekte mit ihrer vorhandenen Containerorganisation an ihrem Ort bleiben.

Blobindextags ermöglichen Ihnen Folgendes:

- Dynamisches Kategorisieren Ihrer Blobs mit Schlüssel-Wert-Indextags
- Schnelles Auffinden bestimmter getaggter Blobs innerhalb einer gesamten Speicherkontoumgebung
- Angeben von bedingtem Verhalten für Blob-APIs basierend auf der Auswertung von Indextags
- Verwenden von Indextags für erweiterte Steuerungsmöglichkeiten bei Features wie der [Lebenszyklusverwaltung für Blobs](storage-lifecycle-management-concepts.md)

Stellen Sie sich ein Szenario vor, bei dem Sie über Millionen von Blobs in Ihrem Speicherkonto verfügen, auf die von vielen verschiedenen Anwendungen zugegriffen wird. Sie möchten alle zugehörigen Daten eines einzelnen Projekts finden. Sie sind sich nicht sicher, was alles dazugehört, weil die Daten über mehrere Container mit unterschiedlichen Namenskonventionen verteilt sein können. Alle Daten werden von Ihren Anwendungen aber mit projektabhängigen Tags hochgeladen. Anstatt Millionen von Blobs zu durchsuchen, um Namen und Eigenschaften zu vergleichen, können Sie `Project = Contoso` als Kriterium für die Ermittlung verwenden. Per Blobindex werden alle Container innerhalb Ihres gesamten Speicherkontos gefiltert, um in kürzester Zeit nur die 50 Blobs aus `Project = Contoso` zurückzugeben.

Beispiele für den Einstieg in die Verwendung des Blobindex finden Sie unter [Verwenden von Blobindextags (Vorschau) zum Verwalten und Suchen von Daten in Azure Blob Storage](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Blobindextags und Datenverwaltung

Bei Container- und Blobnamenpräfixen handelt es sich um eindimensionale Kategorisierungen. Blobindextags ermöglichen eine mehrdimensionale Kategorisierung für [Blobdatentypen (Block-, Anfüge- oder Seitenblob)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). Die mehrdimensionale Kategorisierung wird standardmäßig von Azure Blob Storage indiziert, damit Sie Ihre Daten schnell finden.

Gehen wir von den folgenden fünf Blobs in Ihrem Speicherkonto aus:

- *container1/transaction.csv*
- *container2/campaign.docx*
- *photos/bannerphoto.png*
- *archives/completed/2019review.pdf*
- *logs/2020/01/01/logfile.txt*


Diese Blobs werden durch ein Präfix getrennt, das aus *Containername/Name des virtuellen Ordners/Blobname* besteht. Sie können das Indextagattribut `Project = Contoso` für diese fünf Blobs festlegen, um sie derselben Kategorie zuzuweisen, ohne die aktuelle Präfixorganisation zu ändern. Durch das Hinzufügen von Indextags müssen Daten nicht mehr verschoben werden, weil sie über den Index gefiltert und gefunden werden können.

## <a name="setting-blob-index-tags"></a>Festlegen von Blobindextags

Bei Blobindextags handelt es sich um Schlüssel-Wert-Attribute, die auf neue oder vorhandene Objekte innerhalb Ihres Speicherkontos angewendet werden können. Sie können Indextags während des Uploadvorgangs angeben, indem Sie die Vorgänge [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) oder [Copy Blob](/rest/api/storageservices/copy-blob) und den optionalen Header `x-ms-tags` verwenden. Falls Sie in Ihrem Speicherkonto bereits über Blobs verfügen, können Sie [Set Blob Tags](/rest/api/storageservices/set-blob-tags) aufrufen und ein formatiertes XML-Dokument mit den Indextags im Anforderungstext übergeben.

> [!IMPORTANT]
> Das Festlegen von Blobindextags kann vom [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) und allen anderen Benutzern mit einer Shared Access Signature, die über die Berechtigung zum Zugreifen auf die Tags des Blobs verfügt (SAS-Berechtigung `t`), durchgeführt werden.
>
> Auch RBAC-Benutzer mit der Berechtigung `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` können diesen Vorgang durchführen.

Sie können ein einzelnes Tag auf Ihr Blob anwenden, um anzugeben, wann die Verarbeitung Ihrer Daten abgeschlossen wurde.

> "processedDate" = '2020-01-01'

Sie können mehrere Tags auf Ihr Blob anwenden, um eine ausführlichere Beschreibung für die Daten hinzuzufügen.

> "Project" = 'Contoso'  
> "Classified" = 'True'  
> "Status" = 'Unprocessed'  
> "Priority" = '01'

Um die vorhandenen Indextagattribute zu ändern, rufen Sie die vorhandenen Tagattribute ab, ändern sie und ersetzen sie durch den Vorgang [Set Blob Tags](/rest/api/storageservices/set-blob-tags). Um alle Indextags aus dem Blob zu entfernen, rufen Sie den Vorgang `Set Blob Tags` ohne Tagattribute auf. Da Blobindextags eine Unterressource der Blobdateninhalte sind, ändert `Set Blob Tags` weder die zugrunde liegenden Inhalte, noch die Uhrzeit der letzten Änderung oder das ETag eines Blobs. Sie können Indextags für alle aktuellen Basisblobs und die vorherigen Versionen erstellen oder ändern. Tags in Momentaufnahmen oder vorläufig gelöschten Blobs können aber nicht geändert werden.

Die nachstehenden Einschränkungen gelten für Blobindextags:

- Jedes Blob kann über bis zu zehn Blobindextags verfügen
- Tagschlüssel müssen zwischen einem und 128 Zeichen umfassen
- Tagwerte müssen zwischen 0 und 256 Zeichen umfassen
- Bei Tagschlüsseln und -werten wird die Groß-/Kleinschreibung beachtet
- Für Tagschlüssel und -werte werden nur Zeichenfolgen-Datentypen unterstützt. Alle Zahlen, Datumsangaben, Uhrzeiten oder Sonderzeichen werden als Zeichenfolgen gespeichert.
- Tagschlüssel und -werte müssen den folgenden Benennungsregeln entsprechen:
  - Alphanumerische Zeichen:
    - **a** bis **z** (Kleinbuchstaben)
    - **A** bis **Z** (Großbuchstaben)
    - **0** bis **9** (Zahlen)
  - Zulässige Sonderzeichen: Leerzeichen, Pluszeichen, Minuszeichen, Punkt, Doppelpunkt, Gleichheitszeichen, Unterstrich, Schrägstrich (` +-.:=_/`)

## <a name="getting-and-listing-blob-index-tags"></a>Abrufen und Auflisten von Blobindextags

Blobindextags werden als Unterressource gemeinsam mit den Blobdaten gespeichert und können unabhängig von den zugrunde liegenden Blobdateninhalten abgerufen werden. Blobindextags für ein einzelnes Blob können mit dem Vorgang [Get Blob Tags](/rest/api/storageservices/get-blob-tags) abgerufen werden. Auch der Vorgang [List Blobs](/rest/api/storageservices/list-blobs) mit dem Parameter `include:tags` gibt alle Blobs innerhalb eines Containers und die zugehörigen Blobindextags zurück.

> [!IMPORTANT]
> Das Abrufen und Festlegen von Blobindextags kann vom [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) und allen anderen Benutzern mit einer Shared Access Signature, die über die Berechtigung zum Zugreifen auf die Tags des Blobs verfügt (SAS-Berechtigung `t`), durchgeführt werden.
>
> Auch RBAC-Benutzer mit der Berechtigung `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` können diesen Vorgang durchführen.

Für alle Blobs mit mindestens einem Blobindextag wird `x-ms-tag-count` in den Vorgängen [List Blobs](/rest/api/storageservices/list-blobs), [Get Blob](/rest/api/storageservices/get-blob) und [Get Blob Properties](/rest/api/storageservices/get-blob-properties) zurückgegeben, um die Anzahl von Indextags im Blob anzugeben.

## <a name="finding-data-using-blob-index-tags"></a>Suchen nach Daten mithilfe von Blobindextags

Mit dem Indizierungsmodul werden Ihre Schlüssel-Wert-Attribute in einem mehrdimensionalen Index verfügbar gemacht. Nachdem Sie Ihre Indextags festgelegt haben, sind sie im Blob vorhanden und können sofort abgerufen werden. Es kann einige Zeit dauern, bis der Blobindex aktualisiert wurde. Nachdem der Blobindex aktualisiert wurde, können Sie die nativen Abfrage- und Ermittlungsfunktionen von Blob Storage verwenden.

Über den Vorgang [Find Blobs by Tags](/rest/api/storageservices/find-blobs-by-tags) können Sie eine gefilterte Gruppe mit Blobs abrufen, deren Indextags einem bestimmten Abfrageausdruck entsprechen. `Find Blobs by Tags` unterstützt das Filtern von Daten innerhalb aller Container oder innerhalb eines einzelnen Containers in Ihrem Speicherkonto. Da alle Indextagschlüssel und -werte Zeichenfolgen sind, wird für relationale Operatoren eine lexikografische Sortierung verwendet.

> [!IMPORTANT]
> Das Suchen nach Daten anhand von Blobindextags kann vom [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) und allen anderen Benutzern mit einer Shared Access Signature, die über die Berechtigung zum Suchen nach Blobs per Tag verfügt (SAS-Berechtigung `f`), durchgeführt werden.
>
> Auch RBAC-Benutzer mit der Berechtigung `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` können diesen Vorgang durchführen.

Für die Filterung des Blobindex gelten folgende Kriterien:

- Tagschlüssel müssen in doppelte Anführungszeichen (") eingeschlossen werden
- Tagwerte und Containernamen müssen in einfache Anführungszeichen (') eingeschlossen werden
- Das @-Zeichen ist nur für das Filtern nach einem bestimmten Containernamen zulässig (z. B. `@container = 'ContainerName'`)
- Filter werden mit lexikografischer Sortierung auf Zeichenfolgen angewendet
- Vorgänge für einseitige Bereiche sind für ein und denselben Schlüssel unzulässig (z. B. `"Rank" > '10' AND "Rank" >= '15'`)
- Bei Verwendung von REST zum Erstellen von Filterausdrücken müssen Zeichen URI-codiert sein

In der Tabelle unten sind alle zulässigen Operatoren für `Find Blobs by Tags` aufgeführt:

|  Operator  |  Beschreibung  | Beispiel |
|------------|---------------|---------|
|     =      |     Gleich     | `"Status" = 'In Progress'` |
|     >      |  Größer als | `"Date" > '2018-06-18'` |
|     >=     |  Größer als oder gleich | `"Priority" >= '5'` |
|     <      |  Kleiner als   | `"Age" < '32'` |
|     <=     |  Kleiner als oder gleich  | `"Company" <= 'Contoso'` |
|    AND     |  Logisches AND  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Eingrenzung auf einen bestimmten Container | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> Wenn Sie Tags festlegen und abfragen, sollten Ihnen die lexikografische Reihenfolge bekannt sein.
>
> - Zahlen kommen vor Buchstaben. Zahlen werden basierend auf der ersten Ziffer sortiert.
> - Großbuchstaben kommen vor Kleinbuchstaben.
> - Für Symbole gibt es keine Standardvorgehensweise. Einige Symbole kommen vor numerischen Werten. Andere Symbole kommen vor oder nach Buchstaben.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Bedingte Blobvorgänge mit Blobindextags

In REST-Versionen ab 2019-10-10 unterstützen die meisten [Blob-Dienst-APIs](/rest/api/storageservices/operations-on-blobs) jetzt den bedingten Header `x-ms-if-tags`. Mit diesem Header ist ein Vorgang nur dann erfolgreich, wenn die angegebene Blobindexbedingung erfüllt ist. Wenn die Bedingung nicht erfüllt ist, wird ein entsprechender Hinweis angezeigt: `error 412: The condition specified using HTTP conditional header(s) is not met`.

Der Header `x-ms-if-tags` kann mit den übrigen vorhandenen bedingten HTTP-Headern (If-Match, If-None-Match usw.) kombiniert werden. Wenn in einer Anforderung mehrere bedingte Header angegeben werden, müssen die Bedingungen aller Header erfüllt sein, damit der Vorgang erfolgreich ist. Alle bedingten Header werden effektiv mit einem logischen AND kombiniert.

In der Tabelle unten sind die zulässigen Operatoren für bedingte Vorgänge aufgeführt:

|  Operator  |  Beschreibung  | Beispiel |
|------------|---------------|---------|
|     =      |     Gleich     | `"Status" = 'In Progress'` |
|     <>     |   Ungleich   | `"Status" <> 'Done'` |
|     >      |  Größer als | `"Date" > '2018-06-18'` |
|     >=     |  Größer oder gleich | `"Priority" >= '5'` |
|     <      |  Kleiner als   | `"Age" < '32'` |
|     <=     |  Kleiner als oder gleich  | `"Company" <= 'Contoso'` |
|    AND     |  Logisches AND  | `"Rank" >= '010' AND "Rank" < '100'` |
|     oder     | Logisches OR   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Es gibt zwei zusätzliche Operatoren („Ungleich“ und „logisches OR“), die im bedingten Header `x-ms-if-tags` für Blobvorgänge zulässig, aber im Vorgang `Find Blobs by Tags` nicht vorhanden sind.

## <a name="platform-integrations-with-blob-index-tags"></a>Plattformintegration bei Blobindextags

Blobindextags sind nicht nur zum Kategorisieren, Verwalten und Durchsuchen Ihrer Blobdaten nützlich, sondern ermöglichen auch die Integration in andere Blob Storage-Features, z. B. die [Lebenszyklusverwaltung](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Lebenszyklusverwaltung

Mit dem neuen Element `blobIndexMatch` als Regelfilter in der Lebenszyklusverwaltung können Sie Daten in kältere Ebenen verschieben oder basierend auf den Indextags löschen, die auf Ihre Blobs angewendet wurden. Sie können Ihre Regeln präziser definieren und Blogs nur dann verschieben oder löschen, wenn sie den angegebenen Tagkriterien entsprechen.

Sie können einen Blobindexabgleich als eigenständigen Filtersatz in einer Lebenszyklusregel festlegen, um Aktionen auf getaggte Daten anzuwenden. Alternativ können Sie ein Präfix und einen Blobindex kombinieren, um Übereinstimmungen für spezifischere Datasets zu ermitteln. Beim Angeben von mehreren Filtern in einer Lebenszyklusregel wird ein logischer UND-Vorgang angewendet. Die Aktion wird nur angewendet, wenn sich für *alle* Filterkriterien eine Übereinstimmung ergibt.

Die folgende Beispielregel für die Lebenszyklusverwaltung gilt für Blockblobs in einem Container mit dem Namen *videofiles*. Mit der Regel werden Blobs nur dann in die Archivspeicherebene verschoben, wenn die Daten die folgenden Blobindextag-Kriterien erfüllen: `"Status" == 'Processed' AND "Source" == 'RAW'`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Beispiel einer Blobindexabgleichsregel für die Lebenszyklusverwaltung im Azure-Portal](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>Berechtigungen und Autorisierung

Sie können Zugriffsberechtigungen für Blobindextags erteilen, indem Sie einen der folgenden Ansätze verwenden:

- Verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC), um Berechtigungen für einen Azure Active Directory-Sicherheitsprinzipal (Azure AD) zu erteilen. Verwenden Sie Azure AD, um eine höhere Sicherheit und Benutzerfreundlichkeit zu erzielen. Weitere Informationen zur Verwendung von Azure AD mit Blobvorgängen finden Sie unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory](../common/storage-auth-aad.md).
- Verwenden Sie eine Shared Access Signature (SAS), um den Zugriff auf den Blobindex zu delegieren. Weitere Informationen zu SAS (Shared Access Signatures) finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../common/storage-sas-overview.md).
- Verwenden Sie die Kontozugriffsschlüssel zur Autorisierung von Vorgängen mit gemeinsam verwendetem Schlüssel. Weitere Informationen finden Sie unter [Authentifizieren mit gemeinsam verwendetem Schlüssel](/rest/api/storageservices/authorize-with-shared-key).

Blobindextags sind eine Unterressource der Blobdaten. Ein Benutzer mit Berechtigungen oder SAS-Token für das Lesen oder Schreiben von Blobs kann möglicherweise nicht auf die Blobindextags zugreifen.

### <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Benutzern, die eine [Azure AD-Identität](../common/storage-auth-aad.md) verwenden, können die folgenden Berechtigungen für die Arbeit mit Blobindextags zugewiesen werden.

| Vorgänge von Blobindextags                                          | Azure RBAC-Aktion                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Festlegen von Blobtags](/rest/api/storageservices/set-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write    |
| [Abrufen von Blobtags](/rest/api/storageservices/get-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read     |
| [Suchen nach Blobs anhand von Tags](/rest/api/storageservices/find-blobs-by-tags) | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action |

Für Vorgänge von Indextags sind zusätzliche Berechtigungen erforderlich, die über die Berechtigungen für die zugrunde liegenden Blobdaten hinausgehen. Die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) verfügt über Berechtigungen für alle drei Blobindextag-Vorgänge. [Storage-Blobdatenleser](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) verfügt nur über Berechtigungen für die Vorgänge `Find Blobs by Tags` und `Get Blob Tags`.

### <a name="sas-permissions"></a>SAS-Berechtigungen

Benutzern, die eine [Shared Access Signature (SAS)](../common/storage-sas-overview.md) verwenden, können bereichsbezogene Berechtigungen für das Arbeiten mit Blobindextags erteilt werden.

#### <a name="blob-sas"></a>Blob-SAS

Die folgenden Berechtigungen können in einer Blob-SAS erteilt werden, um den Zugriff auf Blobindextags zuzulassen. Lese- und Schreibberechtigungen für Blobs allein reichen nicht aus, um die zugehörigen Indextags lesen oder schreiben zu können.

| Berechtigung | URI-Symbol | Zulässige Vorgänge                |
|------------|------------|-----------------------------------|
| Indextags |     t      | Abrufen und Festlegen von Indextags für ein Blob |

#### <a name="container-sas"></a>Container-SAS

Die folgenden Berechtigungen können in einer Container-SAS erteilt werden, um das Filtern anhand von Blobtags zuzulassen. Die Berechtigung `Blob List` reicht nicht aus, um Blobs nach ihren Indextags filtern zu können.

| Berechtigung | URI-Symbol | Zulässige Vorgänge         |
|------------|------------|----------------------------|
| Indextags |     f      | Suchen nach Blobs anhand von Indextags |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Unterschiede zwischen Metadaten und Blobindextags

Sowohl Blobindextags als auch Metadaten ermöglichen das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Eigenschaften mit einer Blobressource. Beide können direkt abgerufen und festgelegt werden, ohne den Inhalt des Blobs zurückzugeben oder zu ändern. Sie können sowohl Metadaten als auch Indextags verwenden.

Nur Indextags werden vom nativen Blob Storage-Dienst automatisch indiziert und durchsuchbar gemacht. Metadaten können nicht nativ indiziert oder durchsucht werden. Sie müssen hierfür einen separaten Dienst wie [Azure Search](../../search/search-blob-ai-integration.md) verwenden. Blobindextags verfügen über zusätzliche Berechtigungen zum Lesen, Filtern und Schreiben, die unabhängig von den zugrunde liegenden Blobdaten erteilt werden. Für Metadaten werden die gleichen Berechtigungen wie für das Blob verwendet, und die Rückgabe bei den Vorgängen [Get Blob](/rest/api/storageservices/get-blob) und [Get Blob Properties](/rest/api/storageservices/get-blob-properties) erfolgt als HTTP-Header. Blobindextags werden im Ruhezustand mit einem [von Microsoft verwalteten Schlüssel](../common/storage-service-encryption.md) verschlüsselt. Metadaten werden im Ruhezustand mit demselben Verschlüsselungsschlüssel verschlüsselt, der für Blobdaten angegeben wurde.

In der folgenden Tabelle werden die Unterschiede zwischen Metadaten und Blobindextags veranschaulicht:

|              |   Metadaten   |   Blobindextags  |
|--------------|--------------|--------------------|
| **Einschränkungen**      | Kein numerisches Limit, 8 KB insgesamt, keine Beachtung der Groß-/Kleinschreibung | Maximal 10 Tags pro Blob, 768 Byte pro Tag, Beachtung der Groß-/Kleinschreibung |
| **Updates**    | Auf Archivebene nicht zulässig, `Set Blob Metadata` ersetzt alle vorhandenen Metadaten, `Set Blob Metadata` ändert die Uhrzeit der letzten Änderung des Blobs | Für alle Zugriffsebenen zulässig, `Set Blob Tags` ersetzt alle vorhandenen Tags, `Set Blob Tags` ändert nicht die Uhrzeit der letzten Änderung des Blobs |
| **Storage**     | Speicherung mit den Blobdaten | Unterressource der Blobdaten |
| **Indizierung & Abfragen** | Separater Dienst muss verwendet werden, z. B. Azure Search | In Blob Storage integrierte Indizierungs- und Abfragefunktionen |
| **Verschlüsselung** | Verschlüsselung im Ruhezustand mit demselben Verschlüsselungsschlüssel, der auch für Blobdaten verwendet wird | Verschlüsselung im Ruhezustand mit einem von Microsoft verwalteten Verschlüsselungsschlüssel |
| **Preise** | Die Größe der Metadaten ist in den Speicherkosten für ein Blob enthalten | Fixkosten pro Indextag |
| **Headerantwort** | Rückgabe von Metadaten als Header in `Get Blob` und `Get Blob Properties` | Von `Get Blob` oder `Get Blob Properties` zurückgegebene Taganzahl, Rückgabe von Tags nur durch `Get Blob Tags` und `List Blobs` |
| **Berechtigungen**  | Lese- oder Schreibberechtigungen für Blobdaten gelten auch für Metadaten | Zusätzliche Berechtigungen zum Lesen, Filtern oder Schreiben von Indextags erforderlich |
| **Benennung** | Die Metadatennamen müssen den Benennungsregeln für C#-Bezeichner entsprechen. | Blobindextags unterstützen einen größeren Umfang von alphanumerischen Zeichen. |

## <a name="pricing"></a>Preise

Die Preise für Blobindizes gelten für die öffentliche Vorschauphase und können sich für die Phase der allgemeinen Verfügbarkeit ändern. Ihnen wird die durchschnittliche monatliche Anzahl von Indextags innerhalb eines Speicherkontos berechnet. Für das Indizierungsmodul fallen keine Kosten an. Anforderungen von `Set Blob Tags`, `Get Blob Tags` und `Find Blobs by Tags` werden gemäß den entsprechenden Vorgangstypen berechnet. Weitere Informationen finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Regionale Verfügbarkeit und Unterstützung von Speicherkonten

Blobindextags sind nur für GPv2-Knoten (General Purpose v2, Universell V2) verfügbar, bei denen der hierarchische Namespace (HNS) deaktiviert ist. GPV1-Konten (General Purpose v1, Universell V1) werden nicht unterstützt. Sie können ein GPv1-Konto aber auf ein GPv2-Konto aktualisieren.

Indextags werden für Storage Premium-Konten nicht unterstützt. Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

In der öffentlichen Vorschauphase sind Blobindextags nur in den folgenden Regionen verfügbar:

- Kanada, Mitte
- Kanada, Osten
- Frankreich, Mitte
- Frankreich, Süden

Weitere Informationen zum Einstieg in Blobindex finden Sie unter [Verwenden von Blobindextags (Vorschau) zum Verwalten und Suchen von Daten in Azure Blob Storage](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Sie müssen Ihr Abonnement registrieren, bevor Sie die Blobindex-Vorschauversion für Ihre Speicherkonten verwenden können. Weitere Informationen finden Sie im Abschnitt [Bedingungen und bekannte Probleme](#conditions-and-known-issues) dieses Artikels.

### <a name="register-your-subscription-preview"></a>Registrieren Ihres Abonnements (Vorschau)

Da Blobindextags nur in der öffentlichen Vorschau verfügbar sind, müssen Sie Ihr Abonnement registrieren, um das Feature verwenden zu können. Um eine Anforderung zu senden, führen Sie die folgenden PowerShell- oder CLI-Befehle aus.

#### <a name="register-by-using-powershell"></a>Registrieren über PowerShell

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registrieren mithilfe der Azure CLI

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>Bedingungen und bekannte Probleme

In diesem Abschnitt sind bekannte Probleme und Bedingungen der öffentlichen Vorschauversion von Blobindextags beschrieben. Dieses Feature sollte erst für Produktionsworkloads verwendet werden, wenn die Phase der allgemeinen Verfügbarkeit (General Availability, GA) erreicht ist, weil sich das Verhalten ggf. noch ändern kann.

- Damit Sie in der Vorschau Blobindex für Ihr Speicherkonto in den unterstützten Regionen verwenden können, müssen Sie zuerst Ihr Abonnement registrieren.
- In der Vorschauphase werden nur GPv2-Konten unterstützt. Blob-, BlockBlobStorage- und Data Lake Gen2-Konten mit HNS-Aktivierung werden nicht unterstützt. GPv1-Konten werden nicht unterstützt.
- Beim Hochladen von Seitenblobs mit Indextags werden die Tags nicht beibehalten. Legen Sie die Tags nach dem Hochladen eines Seitenblobs fest.
- Wenn die Filterung auf einen einzelnen Container beschränkt ist, kann `@container` nur übergeben werden, wenn alle Indextags im Filterausdruck Gleichheitsprüfungen sind (Schlüssel=Wert).
- Wenn der Bereichsoperator mit der `AND`-Bedingung verwendet wird, muss derselbe Indextag-Schlüsselname angegeben werden (`"Age" > '013' AND "Age" < '100'`).
- Die Versionsverwaltung und der Blobindex werden nicht unterstützt. Blobindextags werden für verschiedene Versionen beibehalten, aber nicht an das Blobindexmodul übermittelt.
- Es gibt keine API, mit der Sie ermitteln können, ob Indextags indiziert wurden.
- Ein Failover des Kontos wird nicht unterstützt. Der Blobindex wird nach einem Failover möglicherweise nicht ordnungsgemäß aktualisiert.
- Die Lebenszyklusverwaltung unterstützt bei einem Blobindexabgleich nur Gleichheitsprüfungen.
- Bei `Copy Blob` werden keine Blobindextags vom Quellblob in das neue Zielblob kopiert. Sie können während des Kopiervorgangs die Tags angeben, die auf das Zielblob angewendet werden sollen.
- Der Vorgang `Copy Blob` (Async-Kopie) von einem anderen Speicherkonto mit angewendeten Tags auf den Zielblob bewirkt, dass das Blobindexmodul den Blob und seine Tags im Filtersatz nicht zurückgibt. Verwenden Sie `Copy Blob` aus der URL (Sync-Kopie).
- Tags werden bei der Erstellung von Momentaufnahmen beibehalten. Das Höherstufen einer Momentaufnahme wird nicht unterstützt und kann zu einem leeren Tagsatz führen.

## <a name="faq"></a>Häufig gestellte Fragen

**Kann ich Blobindex zum Filtern und Abfragen von Inhalten in meinen Blobs verwenden?**

Nein. Verwenden Sie die Abfragebeschleunigung oder die Azure-Suche, wenn Sie die Inhalte Ihrer Blobdaten durchsuchen müssen.

**Gibt es Anforderungen für Indextagwerte, die erfüllt werden müssen?**

Blobindextags unterstützen nur String-Datentypen, und bei Abfragen werden Ergebnisse in lexikografischer Reihenfolge zurückgegeben. Zahlen sollten mit Nullen aufgefüllt werden. Datumsangaben und Uhrzeiten sollten in einem ISO 8601-konformen Format gespeichert werden.

**Sind Blobindextags mit Azure Resource Manager-Tags verwandt?**

Nein, mit Resource Manager-Tags lassen sich Ressourcen der Steuerungsebene organisieren (Abonnements, Ressourcengruppen und Speicherkonten). Indextags ermöglichen die Blobverwaltung und Ermittlung auf der Datenebene.

## <a name="next-steps"></a>Nächste Schritte

Ein Beispiel für die Verwendung des Blobindex finden Sie unter [Verwenden von Blobindextags (Vorschau) zum Verwalten und Suchen von Daten in Azure Blob Storage](storage-blob-index-how-to.md).

Erfahren Sie mehr über die [Lebenszyklusverwaltung](storage-lifecycle-management-concepts.md), und legen Sie eine Regel mit Blobindexabgleich fest.