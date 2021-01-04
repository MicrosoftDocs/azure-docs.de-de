---
title: Zu beachtende Punkte bei der Verwendung von Video Indexer im großen Stil – Azure
titleSuffix: Azure Media Services
description: In diesem Thema wird erläutert, was bei der Verwendung von Video Indexer im großen Stil zu beachten ist.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: b955c0f494b757fd29c400194ef8b11314a89a03
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483609"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Zu beachtende Punkte bei der Verwendung von Video Indexer im großen Stil

Wenn Sie den Azure Media Services Video Indexer zur Indizierung von Videos verwenden und Ihr Videoarchiv immer größer wird, sollten Sie eine Skalierung in Betracht ziehen. 

In diesem Artikel werden Fragen wie die folgenden beantwortet:

* Gibt es technologische Einschränkungen, die ich berücksichtigen muss?
* Gibt es eine intelligente und effiziente Vorgehensweise?
* Kann ich verhindern, dass im Prozess zu viel Geld ausgegeben wird?

Der Artikel enthält sechs bewährte Methoden für die Verwendung von Video Indexer im großen Stil.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Erwägen Sie beim Hochladen von Videos die Verwendung einer URL anstelle eines Bytearrays.

Video Indexer bietet Ihnen die Option, Videos über eine URL hochzuladen oder die Datei direkt als Bytearray zu senden, wobei letzteres mit einigen Einschränkungen verbunden ist. Weitere Informationen finden Sie unter [Überlegungen und Einschränkungen zum Hochladen](upload-index-videos.md#uploading-considerations-and-limitations).

Zunächst weist es Einschränkungen bei der Dateigröße auf. Die Größe der Bytearray-Datei ist auf 2 GB begrenzt, während die Größenbeschränkung beim Hochladen unter Verwendung einer URL bei 30 GB liegt.

Betrachten Sie zudem nur einige der Probleme, die Ihre Leistung und damit Ihre Möglichkeit zur Skalierung beeinflussen können:

* Das Versenden von Dateien in mehreren Teilen bedeutet eine hohe Abhängigkeit von Ihrem Netzwerk, 
* Dienstzuverlässigkeit, 
* Konnektivität, 
* Uploadgeschwindigkeit, 
* verlorene Pakete im World Wide Web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Erste Überlegung zur Verwendung von Video Indexer im großen Stil":::

Wenn Sie Videos mithilfe einer URL hochladen, müssen Sie lediglich einen Pfad zum Speicherort einer Mediendatei angeben, und Video Indexer kümmert sich um den Rest (siehe das Feld `videoUrl` in der API zum [Hochladen von Videos](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload)).

> [!TIP]
> Verwenden Sie den optionalen Parameter `videoUrl` der API zum Hochladen von Videos.

Ein Beispiel für das Hochladen von Videos mithilfe einer URL finden Sie unter [diesem Beispiel](upload-index-videos.md#code-sample). Oder Sie können [AzCopy](../../storage/common/storage-use-azcopy-v10.md) als schnelle und zuverlässige Methode verwenden, um Ihren Inhalt auf ein Speicherkonto zu übertragen, von dem aus Sie ihn mit [SAS URL](../../storage/common/storage-sas-overview.md) an Video Indexer übermitteln können.

## <a name="increase-media-reserved-units-if-needed"></a>Bedarfsgesteuertes Erhöhen der reservierten Einheiten für Medien

Normalerweise benötigen Sie in der Proof of Concept-Phase, wenn Sie gerade mit der Verwendung von Video Indexer beginnen, nicht viel Rechenleistung. Wenn die Größe des Archivs von zu indizierenden Videos zunimmt und Sie möchten, dass der Prozess in einem Tempo abläuft, das Ihrem Anwendungsfall entspricht, müssen Sie Ihre Verwendung von Video Indexer hochskalieren. Daher sollten Sie darüber nachdenken, die Anzahl der von Ihnen verwendeten Computeressourcen zu erhöhen, wenn die derzeitige Rechenleistung einfach nicht ausreicht.

Wenn Sie bei Azure Media Services die Rechenleistung und Parallelisierung erhöhen möchten, müssen Sie auf [reservierte Einheiten (RUs) für Medien](../latest/concept-media-reserved-units.md) achten. Die reservierten Einheiten (RUs) für Medien sind die Compute-Einheiten, die die Parameter für Ihre Medienverarbeitungsaufgaben bestimmen. Die Anzahl der RUs wirkt sich auf die Anzahl der Medienaufgaben aus, die gleichzeitig in den einzelnen Konten verarbeitet werden können, und ihr Typ bestimmt die Verarbeitungsgeschwindigkeit. Ein Video kann mehr als eine RU erfordern, wenn seine Indizierung komplex ist. Wenn Ihre RUs ausgelastet sind, werden neue Aufgaben zu einer Warteschlange hinzugefügt, bis eine andere Ressource verfügbar ist.

Um effizient zu arbeiten und zu vermeiden, dass Ressourcen einen Teil der Zeit im Leerlauf sind, bietet Video Indexer ein automatisches Skalierungssystem, das die RUs herunterfährt, wenn weniger Verarbeitung erforderlich ist, und die RUs zu Spitzenzeiten hochfährt (bis zur vollständigen Nutzung sämtlicher RUs). Sie können diese Funktion aktivieren, indem Sie die [automatische Skalierung](manage-account-connected-to-azure.md#autoscale-reserved-units) in den Kontoeinstellungen aktivieren oder die [Update-Paid-Account-Azure-Media-Services-API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update) verwenden.

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Zweite Überlegung zur Verwendung von Video Indexer im großen Stil":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Reservierte AMS-Einheiten":::

Zum Minimieren der Indexierungsdauer und des geringen Durchsatzes empfehlen wir, mit 10 RUs vom Typ S3 zu beginnen. Wenn Sie später zur Unterstützung weiterer Inhalte oder einer höherer Parallelität hochskalieren und dafür mehr Ressourcen benötigen, können Sie [uns über das Supportsystem kontaktieren](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (nur für kostenpflichtige Konten), um die Zuteilung weiterer RUs anzufordern.

## <a name="respect-throttling"></a>Respektieren von Einschränkungen

Video Indexer ist für die Indizierung im großen Stil konzipiert, und wenn Sie es optimal nutzen möchten, sollten Sie sich auch über die Funktionen des Systems im Klaren sein und Ihre Integration entsprechend gestalten. Sie möchten keine Uploadanforderung für einen Batch von Videos senden, nur um festzustellen, dass einige der Filme nicht hochgeladen wurden und Sie einen HTTP 429-Antwortcode erhalten (aufgrund zu vieler Anforderungen). Das kann dadurch bedingt sein, dass Sie mehr Anforderungen gesendet haben, als der [von uns unterstützte Grenzwert von Filmen pro Minute](upload-index-videos.md#uploading-considerations-and-limitations) gestattet. Video Indexer fügt in der HTTP-Antwort einen `retry-after`-Header hinzu, der angibt, wann Sie es das nächste Mal versuchen sollten. Stellen Sie sicher, dass Sie ihn beachten, bevor Sie es mit Ihrer nächsten Anforderung versuchen.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Entwerfen Sie eine geeignete Integration, respektieren Sie die Einschränkung":::

## <a name="use-callback-url"></a>Verwenden einer Rückruf-URL

Wir empfehlen, dass Sie, anstatt den Status Ihrer Anforderung ab der Sekunde, in der Sie die Uploadanforderung gesendet haben, ständig abrufen, eine [Rückruf-URL](upload-index-videos.md#callbackurl) hinzufügen und warten, bis Sie von Video Indexer informiert werden. Sobald es eine Statusänderung in Ihrer Uploadanforderung gibt, erhalten Sie eine POST-Benachrichtigung an die von Ihnen angegebene URL.

Sie können eine Rückruf-URL als einen der Parameter der [API zum Hochladen von Videos](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) hinzufügen. Sehen Sie sich die Codebeispiele im [GitHub-Repository](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/) an. 

Als Rückruf-URL können Sie auch Azure Functions verwenden, eine serverlose ereignisgesteuerte Plattform, die über HTTP ausgelöst werden kann und einen folgenden Flow implementiert.

### <a name="callback-url-definition"></a>Definition der Rückruf-URL

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Verwenden Sie die für Sie richtigen Indizierungsparameter

Wenn Sie Entscheidungen im Zusammenhang mit der Verwendung von Video Indexer im großen Stil treffen, schauen Sie sich an, wie Sie es mit den richtigen Parametern für Ihre Anforderungen optimal nutzen können. Denken Sie über Ihren Anwendungsfall nach. Mithilfe der Definition verschiedener Parameter können Sie Geld sparen und den Indizierungsprozess für Ihre Videos beschleunigen.

Bevor Sie Ihr Video hochladen und indizieren, lesen Sie diese kurze [Dokumentation](upload-index-videos.md), und überprüfen Sie [indexingPreset](upload-index-videos.md#indexingpreset) und [streamingPreset](upload-index-videos.md#streamingpreset), um eine bessere Vorstellung von Ihren Optionen zu erhalten.

Legen Sie z. B. die Voreinstellung nicht auf Streaming fest, wenn Sie nicht vorhaben, das Video anzuschauen, und indizieren Sie keine Erkenntnisse aus Videoaufnahmen, wenn Sie nur Erkenntnisse aus Audioaufnahmen benötigen.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Indizieren in optimaler Auflösung, nicht in höchster Auflösung

Sie fragen sich vielleicht, welche Videoqualität Sie für die Indizierung Ihrer Videos benötigen. 

In vielen Fällen besteht bei der Indizierungsleistung fast kein Unterschied zwischen HD-Videos (720P) und 4K-Videos. Schließlich werden Sie fast dieselben Erkenntnisse mit derselben Konfidenz erhalten. Je höher die Qualität des hochgeladenen Films ist, desto größer ist auch die Dateigröße, was zu einer höheren Rechenleistung und einem höheren Zeitaufwand für das Hochladen des Videos führt.

Beispielsweise kann für das Feature zur Gesichtserkennung eine höhere Auflösung in dem Szenario helfen, in dem es viele kleine, aber kontextbezogen wichtige Gesichter gibt. Dies wird jedoch mit einer quadratischen Erhöhung der Laufzeit und einem erhöhten Risiko von falsch positiven Ergebnissen einhergehen.

Wir empfehlen Ihnen daher, sich zu vergewissern, dass Sie die richtigen Ergebnisse für Ihren Anwendungsfall erhalten, und diesen zunächst lokal zu testen. Laden Sie dasselbe Video in 720P und in 4K hoch, und vergleichen Sie die gewonnenen Erkenntnisse.

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen der von der API erstellten Azure Video Indexer-Ausgabe](video-indexer-output-json-v2.md)