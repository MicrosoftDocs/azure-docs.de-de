---
title: Aktives Lernen – Personalisierung
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: d758e8fc7952a414003746d39df9368f3274b08b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482059"
---
# <a name="active-learning-and-learning-policies"></a>Aktives Lernen und Lernrichtlinien 

Wenn Ihre Anwendung die Rangfolge-API aufruft, erhalten Sie einen Rang für den Inhalt. Anhand dieses Rangs kann die Geschäftslogik ermitteln, ob der Inhalt dem Benutzer angezeigt werden soll. Wenn Sie den nach Rang sortierten Inhalt anzeigen, handelt es sich um ein _aktives_ Rangfolgeereignis. Wenn Ihre Anwendung den nach Rang sortierten Inhalt nicht anzeigt, handelt es sich um ein _inaktives_ Rangfolgeereignis. 

Informationen zu aktiven Rangfolgeereignissen werden an die Personalisierung zurückgegeben. Auf der Grundlage dieser Informationen wird das Modell im Rahmen der aktuellen Lernrichtlinie weiter trainiert.

## <a name="active-events"></a>Aktive Ereignisse

Aktive Ereignisse müssen dem Benutzer immer angezeigt werden, und der Relevanzaufruf muss zurückgegeben werden, um die Lernschleife zu schließen. 

### <a name="inactive-events"></a>Inaktive Ereignisse 

Inaktive Ereignisse dürfen das zugrunde liegende Modell nicht verändern, da der Benutzer keine Gelegenheit hatte, etwas aus dem nach Rang sortierten Inhalt auszuwählen.

## <a name="dont-train-with-inactive-rank-events"></a>Nicht mit inaktiven Rangfolgeereignissen trainieren 

Es gibt Anwendungen, bei denen ggf. die Rangfolge-API aufgerufen werden muss, obwohl noch nicht bekannt ist, ob die Ergebnisse dem Benutzer angezeigt werden. 

Dies kann in folgenden Fällen erforderlich sein:

* Ein Element der Benutzeroberfläche wird vorab gerendert, dem Benutzer aber unter Umständen gar nicht angezeigt. 
* Ihre Anwendung führt eine vorausschauende Personalisierung durch, bei der Rangfolgeaufrufe mit weniger Echtzeitkontext verwendet werden, und die Ausgabe wird von der Anwendung unter Umständen gar nicht genutzt. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Deaktivieren von aktivem Lernen für inaktive Rangfolgeereignisse während des Rangfolgeaufrufs

Wenn Sie das automatische Lernen deaktivieren möchten, führen Sie den Rangfolgeaufruf mit `learningEnabled = False` aus.

Das Lernen für ein inaktives Ereignis wird implizit aktiviert, wenn Sie eine Relevanz für den Rang senden.

## <a name="learning-policies"></a>Lernrichtlinien

Die Lernrichtlinie bestimmt die spezifischen *Hyperparameter* des Modelltrainings. Modelle, die über die gleichen Daten verfügen, aber mit unterschiedlichen Richtlinien trainiert wurden, verhalten sich unterschiedlich.

### <a name="importing-and-exporting-learning-policies"></a>Importieren und Exportieren von Lernrichtlinien

Sie können Lernrichtliniendateien über das Azure-Portal importieren und exportieren. Dadurch können Sie vorhandene Richtlinien speichern, testen, ersetzen und zur späteren Referenz und Überprüfung als Artefakte in Ihrer Quellcodeverwaltung archivieren.

### <a name="learning-policy-settings"></a>Lernrichtlinieneinstellungen

Die Einstellungen in der **Lernrichtlinie** sollten nicht geändert werden. Ändern Sie die Einstellungen nur, wenn Sie deren Auswirkungen auf die Personalisierung verstehen. Andernfalls können Nebenwirkungen auftreten (bis hin zum Verlust der Gültigkeit von Personalisierungsmodellen).

### <a name="comparing-effectiveness-of-learning-policies"></a>Vergleichen der Effektivität von Lernrichtlinien

Mithilfe von [Offlineauswertungen](concepts-offline-evaluation.md) können Sie das Abschneiden verschiedener Lernrichtlinien anhand von Vergangenheitsdaten in Personalisierungsprotokollen vergleichen.

[Laden Sie Ihre eigenen Lernrichtlinien hoch](how-to-offline-evaluation.md), um sie mit der aktuellen Lernrichtlinie zu vergleichen.

### <a name="discovery-of-optimized-learning-policies"></a>Ermittlung optimierter Lernrichtlinien

Mittels einer [Offlineauswertung](how-to-offline-evaluation.md) kann die Personalisierung eine besser optimierte Lernrichtlinie erstellen. Eine besser optimierte Lernrichtlinie zeichnet sich in einer Offlineauswertung durch eine höhere Relevanz aus und liefert bessere Ergebnisse, wenn sie online bei der Personalisierung verwendet wird.

Die erstellte optimierte Lernrichtlinie kann direkt auf die Personalisierung angewendet werden, um die aktuelle Richtlinie umgehend zu ersetzen. Alternativ können Sie sie zur weiteren Auswertung speichern und später entscheiden, ob Sie sie verwerfen, speichern oder anwenden möchten.