---
title: Entwurfskonzepte mit Akustiksimulation
titlesuffix: Azure Cognitive Services
description: Dieser konzeptionelle Überblick erläutert, wie Projekt Akustik die akustische Simulation in den Soundentwurfsprozess integriert.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 4a1a0b15da091a1c020eb132f6b14b9ee14d334c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61335394"
---
# <a name="project-acoustics-design-process-concepts"></a>Entwurfskonzepte in Projekt Akustik

Dieser konzeptionelle Überblick erläutert, wie Projekt Akustik die physikalische akustische Simulation in den Soundentwurfsprozess integriert.

## <a name="sound-design-with-audio-dsp-parameters"></a>Soundentwurf mit DSP-Audioparametern

Interaktive 3D-Titel erreichen ihren besonderen Sound durch DSP-Audioblöcke (Digital Signal Processing), die in einer Audioengine gehostet werden. Diese Blöcke reichen in ihrer Komplexität von einfachem Mischen über Hall, Echo, Verzögerung, Equalizer, Komprimierung bis hin zu Limiting und anderen Effekten. Die Auswahl, Anordnung und Festlegung der Parameter für diese Effekte liegt in der Verantwortung des Sound Designers, der einen Audiographen erstellt, der die ästhetischen und spielerischen Ziele des akustischen Erlebnisses erreicht.

Wie passen sich diese Parameter bei einem interaktiven Titel an veränderte Bedingungen an, wenn sich die Sounds und der Zuhörer im 3D-Raum bewegen? Der Sound Designer arrangiert oft Volumes im gesamten Raum, die so programmiert sind, dass sie Parameteränderungen auslösen, um z.B. Änderungen bei Halleffekten zu erreichen, oder um Klänge im Mix zu unterdrücken, wenn der Zuhörer von einem Teil der Szene zu einem anderen wechselt. Es sind auch Akustiksysteme erhältlich, die einige dieser Effekte automatisieren können.

3D-Titel verwenden Beleuchtungs- und kinematische physikalische Systeme, die physikalisch motiviert, aber vom Designer angepasst sind, um eine Mischung aus Immersions- und Gameplayzielen zu erreichen. Ein visueller Designer legt keine individuellen Pixelwerte fest, sondern passt 3D-Modelle, Materialien und Lichttransportsysteme an, die alle physikalisch basiert sind, um visuelle Ästhetik und CPU-Kosten abzugleichen. Was wäre der entsprechende Prozess für Audio? Projekt Akustik ist der erste Schritt bei der Untersuchung dieser Frage. Zuerst werden wir darauf eingehen, was es bedeutet, akustische Energie durch einen Raum zu transportieren.

![Screenshot der mit Hallzonen überlagerten AltSpace-Szene](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulsantworten: Akustisches Verbinden von zwei Punkten im Raum

Wenn Sie mit Audiodesign vertraut sind, kennen Sie möglicherweise akustische Impulsantworten. Eine akustische Impulsantwort modelliert den Transport eines Sounds aus einer Quelle zu einem Zuhörer. Eine Impulsantwort kann daher alle interessanten Effekte der Raumakustik wie Okklusion und Nachhall erfassen. Impulsantworten weisen auch bestimmte leistungsfähige Eigenschaften auf, die es ermöglichen, DSP-Audioeffekte zu skalieren. Das Addieren von zwei Audiosignalen und die Verarbeitung mit einer Impulsantwort ergibt das gleiche Ergebnis wie das separate Anwenden der Impulsantwort auf jedes Signal und das Hinzufügen der Ergebnisse. Die akustische Übertragung und die Impulsantworten hängen auch nicht vom zu verarbeitenden Audio ab, sondern nur von der zu modellierenden Szene sowie den Positionen von Quelle und Zuhörer. Kurz gesagt, eine Impulsantwort beeinflusst die Wirkung der Szene auf die Schallausbreitung.

Eine Impulsantwort erfasst jeden interessanten raumakustischen Effekt. Wir können ihn mit einem Filter effizient auf das Audio anwenden, und wir können Impulsantworten aus Messung oder Simulation erhalten. Aber was wäre, wenn die Akustik nicht ganz der Physik entsprechen soll, sondern vielmehr den emotionalen Anforderungen einer Szene angepasst werden soll? Ähnlich wie Pixelwerte ist eine Impulsantwort nur eine Liste von Tausenden von Zahlen, wie können wir sie also an die ästhetischen Bedürfnisse anpassen? Und was geschieht, wenn wir eine Okklusion/Obstruktion verwenden möchten, die beim Passieren von Türen oder hinter Hindernissen fließend variiert? Wie viele Impulsantworten benötigen wir, um einen weichen Effekt zu erzielen? Was geschieht, wenn sich die Quelle schnell bewegt? Wie erfolgt die Interpolation?

Es klingt schwierig, Simulation und Impulsantworten für einige Aspekte der Akustik in interaktiven Titeln zu verwenden. Aber wir können immer noch ein Audiotransportsystem erstellen, das Designanpassungen unterstützt, wenn wir unsere Impulsantworten aus der Simulation mit unseren bekannten DSP-Audioeffektparametern verbinden können.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Verbinden der Simulation mit Audio-DSP mit Parametern

Eine Impulsantwort enthält jeden interessanten (und alle uninteressanten) akustischen Effekte. DSP-Audioblöcke können, wenn ihre Parameter richtig festgelegt sind, einen interessanten akustischen Effekt erzeugen. Die Verwendung der akustischen Simulation zum Steuern eines DSP-Audioblocks zur Automatisierung des Audiotransports in einer 3D-Szene ist nur eine Frage des Messens der DSP-Audioparameter aus einer Impulsantwort. Diese Messung ist für bestimmte gängige und wichtige akustische Effekte wie Okklusion, Obstruktion, Portale und Hall genau bekannt.

Aber wenn die Simulation direkt mit den DSP-Audioparametern verbunden ist, wo ist dann die Designeranpassung? Was haben wir gewonnen? Nun, wir gewinnen eine beträchtliche Menge an Speicher zurück, indem wir Impulsantworten verwerfen und einige DSP-Parameter beibehalten. Und damit der Designer einen gewissen Einfluss auf das Endergebnis hat, müssen wir nur einen Weg finden, den Designer zwischen der Simulation und dem Audio-DSP einzubinden.

![Diagramm mit stilisierter Impulsantwort mit überlagerten Parametern](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Soundentwurf durch Transformieren von DSP-Audioparametern aus der Simulation

Bedenken Sie die Wirkung einer Sonnenbrille auf Ihren Blick auf die Welt. An einem sonnigen Tag kann die Brille verhindern, dass sie zu sehr geblendet werden. In einem dunklen Raum sehen Sie möglicherweise gar nichts mehr. Die Brille legt nicht in allen Situationen eine bestimmte Helligkeit fest, sondern sie macht einfach alles dunkler.

Wenn wir Simulation verwenden, um unseren DSP-Audioblock mit Okklusions- und Nachhallparametern zu steuern, können wir nach dem Simulator einen Filter hinzufügen, um die Parameter anzupassen, die der DSP-Block „sieht“. Der Filter würde eine gewisse Okklusion oder einen Hallverlauf nicht erzwingen, ähnlich wie eine Sonnenbrille nicht in jedem Raum für die gleiche Helligkeit sorgt. Der Filter kann aber dazu führen, dass jeder Okkluder weniger verdeckt. Oder mehr verdeckt. Durch das Hinzufügen und Anpassen eines „Verdunkelungs“-Okklusionsparameterfilters hätten große, offene Räume immer noch wenig bis gar keinen Okklusionseffekt, während die Türöffnungen von einem mittleren zu einem starken Okklusionseffekt führen würden, während die Gleichmäßigkeit der Effektübergänge, die die Simulation bietet, erhalten bleibt.

In diesem Paradigma ändert sich die Aufgabe des Designers von der Auswahl der akustischen Parameter für jede einzelne Situation in die Auswahl und Anpassung der Filter, um sie auf die wichtigsten DSP-Parameter der Simulation anzuwenden. Die Aktivitäten des Designers beziehen sich nicht mehr nur auf das Einrichten fließender Übergänge, sondern auch auf komplexere Aspekte wie die Intensität von Okklusions- und Nachhalleffekten und das Vorhandensein von Quellen in der Mischung. Wenn es die Situation erfordert, ist natürlich ein Filter immer verfügbar, um einfach zur Auswahl der DSP-Parameter für eine bestimmte Quelle in einer bestimmten Situation zurückzukehren.

## <a name="sound-design-in-project-acoustics"></a>Sound Design im Projekt Akustik

Das Projekt Akustik-Paket integriert alle oben beschriebenen Komponenten: einen Simulator, einen Encoder, der Parameter extrahiert und die Akustikressource erstellt, einen DSP-Audioblock und eine Auswahl an Filtern. Sound Design mit Projekt Akustik beinhaltet die Auswahl von Parametern für die Filter, die die aus der Simulation abgeleiteten und auf den DSP-Audioblock angewendeten Okklusions- und Nachhallparameter anpassen. Dabei werden die dynamischen Steuerelemente innerhalb des Spiele-Editors und der Audioengine angezeigt.

## <a name="next-steps"></a>Nächste Schritte
* Testen des Designparadigmas mit dem [Projekt Akustik-Schnellstart für Unity](unity-quickstart.md) oder dem [Projekt Akustik-Schnellstart für Unreal](unreal-quickstart.md)
* Untersuchen der [Projekt Akustik-Designsteuerelemente für Unity](unity-workflow.md) oder der [Projekt Akustik-Designsteuerelemente für Unreal](unreal-workflow.md)

