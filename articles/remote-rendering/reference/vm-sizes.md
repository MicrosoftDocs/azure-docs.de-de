---
title: Servergrößen
description: Beschreibt die unterschiedlichen Servergrößen, die zugeordnet werden können
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e2687954fb05ce826e780ae0dbd3931d899885f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594399"
---
# <a name="server-sizes"></a>Servergrößen

Azure Remote Rendering ist als zwei Serverkonfigurationen verfügbar: `Standard` und `Premium`.

## <a name="polygon-limits"></a>Maximale Polygonzahl

Remote Rendering mit einem Server der Größe `Standard` hat eine maximale Szenengröße von 20 Millionen Polygonen. Remote Rendering mit der Größe `Premium` erzwingt kein hartes Maximum, die Leistung kann jedoch beeinträchtigt werden, wenn Ihre Inhalte die Renderingkapazität des Diensts überschreiten.

Wenn der Renderer auf einem Server mit der Größe „Standard“ diesen Grenzwert erreicht, schaltet er das Rendering in einen Schachbretthintergrund um:

![Der Screenshot zeigt ein Raster aus schwarzen und weißen Quadraten mit einem Menü „Extras“.](media/checkerboard.png)

## <a name="specify-the-server-size"></a>Angeben der Servergröße

Der gewünschte Serverkonfigurationstyp muss bei der Initialisierung der Renderingsitzung angegeben werden. Er kann nicht während einer laufenden Sitzung geändert werden. Die folgenden Codebeispiele zeigen, wo die Servergröße angegeben werden muss:

```cs
async void CreateRenderingSession(RemoteRenderingClient client)
{
    RenderingSessionCreationOptions sessionCreationOptions = default;
    sessionCreationOptions.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(sessionCreationOptions);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // do something with the session
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client)
{
    RenderingSessionCreationOptions sessionCreationOptions;
    sessionCreationOptions.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    client->CreateNewRenderingSessionAsync(sessionCreationOptions, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // do something with the session
        }
    });
}

```

Für die [PowerShell-Beispielskripts](../samples/powershell-example-scripts.md) muss die Servergröße in der Datei `arrconfig.json` angegeben werden:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Auswerten der Anzahl der Polygone durch den Renderer

Die Anzahl von Polygonen, die für den Einschränkungstest berücksichtigt werden, entspricht der Anzahl der Polygone, die tatsächlich an den Renderer übermittelt werden. Diese Geometrie ist in der Regel die Summe aller instanziierten Modelle, es gibt jedoch auch Ausnahmen. Folgende Geometrie ist **nicht eingeschlossen**:
* Geladene Modellinstanzen, die vollständig außerhalb des Ansichtsfrustums liegen.
* Modelle oder Modellteile, die mithilfe der [Komponente der hierarchischen Zustandsüberschreibung](../overview/features/override-hierarchical-state.md) unsichtbar geschaltet werden.

Dementsprechend ist es möglich, eine Anwendung zu schreiben, die auf die `standard`-Größe abzielt und mehrere Modelle mit einer Polygonzahl nahe des Limits für das einzelne Modell lädt. Wenn die Anwendung jeweils nur ein Modell anzeigt, wird das Schachbrett nicht ausgelöst.

### <a name="how-to-determine-the-number-of-polygons"></a>Bestimmen der Anzahl der Polygone

Es gibt zwei Möglichkeiten, die Anzahl der Polygone eines Modells oder einer Szene zu ermitteln, die zum Budgetlimit der `standard`-Konfigurationsgröße beitragen:
* Rufen Sie auf der Modellkonvertierungsseite die [JSON-Datei der Konvertierungsausgabe](../how-tos/conversion/get-information.md) ab, und überprüfen Sie den `numFaces`-Eintrag im Abschnitt [*inputStatistics*](../how-tos/conversion/get-information.md#the-inputstatistics-section) (Eingabestatistik).
* Wenn die Anwendung dynamische Inhalte verarbeitet, kann die Anzahl gerenderter Polygone während der Runtime dynamisch abgefragt werden. Verwenden Sie eine [Leistungsbewertungsabfrage](../overview/features/performance-queries.md#performance-assessment-queries), und suchen Sie in der `FrameStatistics`-Struktur nach dem Element `polygonsRendered`. Das Feld `PolygonsRendered` wird auf `bad` festgelegt, wenn der Renderer den Grenzwert für Polygone erreicht. Der Schachbretthintergrund wird stets mit einiger Verzögerung ausgeblendet, um sicherzustellen, dass nach dieser asynchronen Abfrage eine Benutzeraktion durchgeführt werden kann. Eine Benutzeraktion kann beispielsweise Modellinstanzen ausblenden oder löschen.

## <a name="pricing"></a>Preise

Eine detaillierte Aufschlüsselung der Preise für die einzelnen Konfigurationstypen finden Sie auf der Seite [Remote Rendering – Preise](https://azure.microsoft.com/pricing/details/remote-rendering).

## <a name="next-steps"></a>Nächste Schritte
* [PowerShell-Beispielskripts](../samples/powershell-example-scripts.md)
* [Modellkonvertierung](../how-tos/conversion/model-conversion.md)

