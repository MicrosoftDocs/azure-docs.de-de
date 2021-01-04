---
title: Transformationen und Aufträge in Media Services
titleSuffix: Azure Media Services
description: Erfahren Sie, wie Sie Transformationen erstellen, um die Regeln zu beschreiben, gemäß denen Ihre Videos in Azure Media Services verarbeitet werden.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: inhenkel
ms.openlocfilehash: 4cb4bcf5eb205d27cbca764d5ec16890a23be5c6
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426780"
---
# <a name="transforms-and-jobs-in-media-services"></a>Transformationen und Aufträge in Media Services

In diesem Thema werden Details zu [Transformationen](/rest/api/media/transforms) und [Aufträgen](/rest/api/media/jobs) erläutert und die Beziehung zwischen diesen Entitäten erläutert.

## <a name="overview"></a>Übersicht

### <a name="transformsjobs-workflow"></a>Workflow für Transformationen/Aufträge

Das folgende Diagramm zeigt den Workflow für Transformationen/Aufträge:

![Workflow für Transformationen/Aufträge in Azure Media Services](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Typischer Workflow

1. Erstellen Sie eine Transformation.
2. Übermitteln Sie Aufträge unter dieser Transformation.
3. Listen Sie Transformationen auf.
4. Löschen Sie eine Transformation, wenn Sie nicht vorhaben, sie zukünftig zu verwenden.

#### <a name="example"></a>Beispiel

Angenommen, Sie möchten jeweils den ersten Frame aller Ihrer Videos als Miniaturbild extrahieren. Dazu müssen Sie die folgenden Schritte ausführen:

1. Legen Sie die Konfiguration oder Regel für die Verarbeitung Ihrer Videos fest: „den ersten Frame des Videos als Miniaturbild verwenden“.
2. Für jedes Video würden Sie dem Dienst Folgendes mitteilen:
    1. Wo dieses Video zu finden ist
    2. Wohin die Ausgabe (das Miniaturbild) geschrieben werden soll

Eine **Transformation** hilft Ihnen, die Konfiguration zu erstellen (Schritt 1) und dann Aufträge mithilfe dieser Konfiguration zu übermitteln (Schritt 2).

> [!NOTE]
> Eigenschaften von **Transform** und **Job** , die den Datetime-Typ haben, sind immer im UTC-Format angegeben.

## <a name="transforms"></a>Transformationen

Verwenden Sie **Transformationen** , um allgemeine Aufgaben zur Codierung oder Analyse von Videos zu konfigurieren. Jede **Transformation** beschreibt eine Vorgehensweise oder einen Workflow von Aufgaben zur Verarbeitung Ihrer Video- oder Audiodateien. Eine einzelne Transformation kann mehrere Regeln anwenden. Eine Transformation kann z.B. angeben, dass jedes Video in eine MP4-Datei mit einer festgelegten Bitrate codiert und eine Miniaturansicht aus dem ersten Bild im Video generiert werden soll. Sie würden für jede Regel, die Sie in die Transformation einfügen möchten, einen TransformOutput-Eintrag hinzufügen. Sie verwenden Voreinstellungen, um der Transformation mitzuteilen, wie die Eingabemediendateien verarbeitet werden sollen.

### <a name="viewing-schema"></a>Anzeigen des Schemas

In Media Services v3 sind Voreinstellungen stark typisierte Entitäten in der API selbst. Die „Schema“-Definition für diese Objekte finden Sie in [Offene API-Spezifikation (oder Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Sie finden die voreingestellten Definitionen (wie **StandardEncoderPreset** ) auch in der Referenzdokumentation der [REST-API](/rest/api/media/transforms/createorupdate#standardencoderpreset), des [.NET SDKs](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) oder eines anderen Media Services v3 SDKs.

### <a name="creating-transforms"></a>Erstellen von Transformationen

Sie können Transformationen mit REST, CLI oder einem der veröffentlichten SDKs erstellen. Die Media Services-v3-API wird von Azure Resource Manager gesteuert. Deshalb können Sie auch Resource Manager-Vorlagen verwenden, um Transformationen in Ihrem Media Services-Konto zu erstellen und bereitzustellen. Mithilfe der rollenbasierten Zugriffssteuerung von Azure können Sie den Zugriff auf Transformationen sperren.

### <a name="updating-transforms"></a>Aktualisieren von Transformationen

Wenn Sie Ihre [Transformation](/rest/api/media/transforms) aktualisieren müssen, verwenden Sie den **Aktualisierungsvorgang**. Er ist für das Vornehmen von Änderungen an der Beschreibung oder den Prioritäten der zugrundeliegenden TransformOutputs vorgesehen. Es empfiehlt sich, solche Updates vorzunehmen, wenn alle in Bearbeitung befindlichen Aufträge abgeschlossen wurden. Wenn Sie die Konfiguration umschreiben möchten, müssen Sie eine neue Transformation erstellen.

### <a name="transform-object-diagram"></a>Diagramm des Objekts „Transform“

In der folgenden Abbildung ist das Objekt **Transform** mit den referenzierten Objekten sowie den abgeleiteten Beziehungen dargestellt. Die grauen Pfeile stehen für einen Typ, die vom Auftrag referenziert werden, während die grünen Pfeile Klassenableitungsbeziehungen angeben.

Wählen Sie das Bild aus, um es in voller Größe anzeigen.  

[![Diagramm des Objekts „Transform“ mit den referenzierten Objekten, einschließlich der Klassenableitungsbeziehungen zwischen den Objekten.](./media/api-diagrams/transform-small.png)](./media/api-diagrams/transform-large.png#lightbox)

## <a name="jobs"></a>Aufträge

Ein **Auftrag** ist die eigentliche Anforderung an Media Services, die erstellte **Transformation** auf ein bestimmtes Eingabevideo oder auf einen Audioinhalt anzuwenden. Nachdem die Transformation erstellt wurde, können Sie mithilfe von Media Services-APIs oder der veröffentlichten SDKs Aufträge übermitteln. Der **Auftrag** gibt Informationen wie den Speicherort des Eingabevideos und den Speicherort für die Ausgabe an. Sie können den Speicherort Ihres Eingabevideos über HTTPS-URLs, SAS-URLs oder [Medienobjekte](/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Auftragseingabe über HTTPS

Verwenden Sie [Auftragseingabe über HTTPS](job-input-from-http-how-to.md), wenn Ihr Inhalt bereits über eine URL zugänglich ist und Sie die Quelldatei nicht in Azure speichern müssen (z. B. Import von S3). Diese Methode ist auch geeignet, wenn sich der Inhalt im Azure Blob Storage befindet, die Datei aber nicht in einem Medienobjekt gespeichert werden muss. Derzeit unterstützt diese Methode nur eine einzelne Datei für die Eingabe.

### <a name="asset-as-job-input"></a>Medienobjekt als Auftragseingabe

Verwenden Sie [Medienobjekt als Auftragseingabe](job-input-from-local-file-how-to.md), wenn sich der eingegebene Inhalt bereits in einem Medienobjekt befindet oder der Inhalt in einer lokalen Datei gespeichert ist. Es ist auch eine gute Option, wenn Sie planen, das Eingabemedienobjekt für Streaming oder Download zu veröffentlichen (z. B. wenn Sie die MP4-Datei zum Herunterladen veröffentlichen möchten, aber auch eine Sprach- oder Gesichtserkennung erfolgen soll). Diese Methode unterstützt Medienobjekte mit mehreren Dateien (z. B. MBR-Streamingsätze, die lokal verschlüsselt wurden).

### <a name="checking-job-progress"></a>Überprüfen des Auftragsfortschritts

Fortschritt und Status von Aufträgen können abgerufen werden, indem die Ereignisse mit Event Grid überwacht werden. Weitere Informationen finden Sie unter [Überwachen von Ereignissen mit EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Aktualisieren von Aufträgen

Der Updatevorgang für den Auftrag ( [Job](/rest/api/media/jobs)-Entität) kann verwendet werden, um die Eigenschaften *description* und *priority* nach dem Übermitteln des Auftrags zu ändern. Eine Änderung an der *priority* -Eigenschaft wird nur wirksam, wenn sich der Auftrag noch in der Warteschlange befindet. Wenn die Verarbeitung des Auftrags gestartet oder bereits beendet wurde, hat das Ändern der Priorität keine Auswirkungen.

### <a name="job-object-diagram"></a>Diagramm des Objekts „Job“

In der folgenden Abbildung ist das Objekt **Job** mit den referenzierten Objekten sowie den abgeleiteten Beziehungen dargestellt.

Klicken Sie auf Bild, um es in voller Größe anzeigen.  

[![Diagramm des Objekts „Job“ mit den referenzierten Objekten, einschließlich der Klassenableitungsbeziehungen zwischen den Objekten.](./media/api-diagrams/job-small.png)](./media/api-diagrams/job-large.png#lightbox)

## <a name="configure-media-reserved-units"></a>Konfigurieren von reservierten Einheiten für Medien

Für die Audioanalyse- und Videoanalyseaufträge, die von Media Services v3 oder Video Indexer ausgelöst werden, wird dringend empfohlen, Ihr Konto mit zehn S3-MRUs (Media Reserved Units, reservierte Einheiten für Medien) bereitzustellen. Erstellen Sie im [Azure-Portal](https://portal.azure.com/) ein Supportticket, falls Sie mehr als zehn S3-MRUs benötigen.

Details dazu finden Sie unter [Skalieren der Medienverarbeitung an der Befehlszeilenschnittstelle](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="see-also"></a>Weitere Informationen

* [Fehlercodes](/rest/api/media/jobs/get#joberrorcode)
* [Filterung, Sortierung und Paginierung von Media Services-Entitäten](entities-overview.md)

## <a name="next-steps"></a>Nächste Schritte

- Bevor Sie mit der Entwicklung beginnen, lesen Sie [Entwickeln mit Media Services v3-APIs](media-services-apis-overview.md) (Informationen zum Zugreifen auf APIs, Namenskonventionen usw.).
- Informationen finden Sie in den folgenden Tutorials:

    - [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos](stream-files-tutorial-with-rest.md)
    - [Tutorial: Hochladen, Codieren und Streamen von Videos](stream-files-tutorial-with-api.md)
    - [Tutorial: Analysieren von Videos mit Media Services v3](analyze-videos-tutorial-with-api.md)
