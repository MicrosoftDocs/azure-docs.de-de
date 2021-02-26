---
title: Z-Fighting-Minimierung
description: In diesem Artikel werden Verfahren zum Verringern von Z-Fighting-Artefakten beschrieben.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: a399565d62b20f62b72257bcb9f3beb2c910ac98
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594060"
---
# <a name="z-fighting-mitigation"></a>Z-Fighting-Minimierung

Wenn zwei Oberflächen überlappen, ist unklar, welche über der anderen gerendert werden soll. Das Ergebnis unterscheidet sich sogar per Pixel, was zu Artefakten führt, die von der Kameraansicht abhängen. Daher kann es zu einem sehr offensichtlichem Flimmern der Muster kommen, wenn die Kamera oder das Gittermodell bewegt wird. Diese Artefakte werden als *Z-Fighting* bezeichnet. Bei AR- und VR-Anwendungen wird das Problem verstärkt, da sich am Kopf angebrachte Geräte immer bewegen. Damit Unannehmlichkeiten für Benutzer verhindert werden können, wird die Funktion zur Minderung von Z-Fighting in Azure Remote Rendering zur Verfügung gestellt.

## <a name="z-fighting-mitigation-modes"></a>Z-Fighting-Minderungsmodi

|Situation                        | Ergebnis                               |
|---------------------------------|:-------------------------------------|
|Reguläres Z-Fighting               |![Keine deterministische Rangfolge zwischen roten und grünen Quadraten](./media/zfighting-0.png)|
|Aktivierte Z-Fighting-Minderung    |![Rotes Quadrat hat Vorrang](./media/zfighting-1.png)|
|Aktivierte Schachbrettmusterhervorhebung|![Die Reihenfolge zwischen roten und grünen Quadraten wechselt im Schachbrettmuster](./media/zfighting-2.png)|

Mit dem folgenden Code wird die Z-Fighting-Minderung aktiviert:

```cs
void EnableZFightingMitigation(RenderingSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Connection.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Connection()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```

> [!NOTE]
> Die Z-Fighting-Minderung ist eine globale Einstellung, die sich auf alle gerenderten Gittermodelle auswirkt.

## <a name="reasons-for-z-fighting"></a>Gründe für Z-Fighting

Z-Fighting tritt hauptsächlich aufgrund von zwei Gründen auf:

1. Wenn Oberflächen sehr weit von der Kamera entfernt sind, verschlechtert sich die Genauigkeit der Tiefenwerte, wodurch die Werte nicht mehr unterschieden werden können.
1. Wenn sich Oberflächen in einem Gittermodell physisch überlappen.

Das erste Problem kann jederzeit auftreten und ist schwer zu beseitigen. Wenn es in Ihrer Anwendung auftritt, sollten Sie das Verhältnis zwischen den Distanzen der *nahen Ebene* und der *entfernten Ebene* möglichst gering sein. Zum Beispiel wird dieses Problem bei einer Distanz von „0,01“ der nahen Ebene und „1.000“ der entfernten Ebene weitaus früher ausgelöst als bei einer Distanz von „0,1“ der nahen Ebene und „20“ der entfernten Ebene.

Das zweite Problem ist ein Indikator für schlecht geschriebenen Inhalt. In der echten Welt können sich zwei Objekte nicht gleichzeitig an genau derselben Stelle befinden. Je nach Anwendung möchten Benutzer möglicherweise wissen, ob überlappende Oberflächen vorhanden sind und wo sie sich befinden. Beispielsweise sollte eine CAD-Darstellung eines Gebäudes, die als Grundlage für eine reale Konstruktion dient, keine physisch unmöglichen Oberflächenüberschneidungen enthalten. Für die visuelle Untersuchung wird der Hervorhebungsmodus zur Verfügung gestellt, der potenzielles Z-Fighting mit einem animierten Schachbrettmuster anzeigt.

## <a name="limitations"></a>Einschränkungen

Die bereitgestellte Z-Fighting-Minderung stellt die bestmögliche Maßnahme dar. Es wird jedoch nicht garantiert, dass sämtliches Z-Fighting beseitigt wird. Außerdem wird automatisch eine Oberfläche der anderen vorgezogen. Wenn also Oberflächen vorliegen, die zu nah aneinander sind, kann es vorkommen, dass die „falsche Oberfläche“ auf der anderen liegt. Ein Problemfall tritt häufig auf, wenn Text oder andere Bilder auf eine Oberfläche angewendet werden. Bei aktivierter Z-Fighting-Minderung könnten diese Details einfach verschwinden.

## <a name="performance-considerations"></a>Überlegungen zur Leistung

* Durch Aktivieren der Z-Fighting-Minderung kommt es nur zu geringem oder gar keinem zusätzlichen Leistungsaufwand.
* Darüber hinaus kommt es bei Aktivierung der Z-Fighting-Überlagerung zu einem nicht trivialen Leistungsmehraufwand. Dieser kann jedoch je nach Darstellung variieren.

## <a name="api-documentation"></a>API-Dokumentation

* [C# RenderingConnection.ZFightingMitigationSettings (Eigenschaft)](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.zfightingmitigationsettings)
* [C++ RenderingConnection::ZFightingMitigationSettings()](/cpp/api/remote-rendering/renderingconnection#zfightingmitigationsettings)

## <a name="next-steps"></a>Nächste Schritte

* [Renderingmodi](../../concepts/rendering-modes.md)
* [Late Stage Reprojection (LSR)](late-stage-reprojection.md)