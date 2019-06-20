---
title: 'Satzpaarbildung und -zuordnung: Custom Translator'
titleSuffix: Azure Cognitive Services
description: Während der Ausführung des Trainings werden aus den Sätzen in parallelen Dokumenten Paare gebildet, bzw. die Sätze werden einander zugeordnet. Custom Translator lernt Übersetzungen Satz für Satz durch Lesen eines Satzes und der zugehörigen Übersetzung. Anschließend werden Wörter und Ausdrücke in diesen beiden Sätzen einander zugeordnet.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 99538fd18067377efcc9e744e9ec611e0f685273
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389595"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Satzpaarbildung und -zuordnung in parallelen Dokumenten

Während des Trainings werden aus den Sätzen in parallelen Dokumenten Paare gebildet, bzw. die Sätze werden einander zugeordnet. Custom Translator zeigt die Anzahl von Sätzen, für die ein Paar gebildet werden konnte, als „zugeordnete Sätze“ in jedem der Datasets an.

## <a name="pairing-and-alignment-process"></a>Paarbildungs- und Zuordnungsprozess

Custom Translator lernt Übersetzungen von Sätzen Satz für Satz. Dazu wird ein Satz in der Quelle und anschließend die Übersetzung dieses Satzes im Ziel gelesen. Danach werden Wörter und Ausdrücke in diesen beiden Sätzen einander zugeordnet. Dieser Prozess ermöglicht die Erstellung einer Zuordnung zwischen den Wörtern und Ausdrücken in einem Satz und den entsprechenden Wörtern und Ausdrücken in der Übersetzung des Satzes. Durch die Zuordnung soll sichergestellt werden, dass das System mit Sätzen trainiert wird, bei denen es sich um gegenseitige Übersetzungen handelt.

## <a name="pre-aligned-documents"></a>Vorab zugeordnete Dokumente

Wenn Sie wissen, dass Sie über parallele Dokumente verfügen, können Sie die Satzzuordnung außer Kraft setzen, indem Sie vorab zugeordnete Textdateien bereitstellen. Sie können alle Sätze aus beiden Dokumenten in eine Textdatei mit einem Satz pro Zeile extrahieren und die Datei mit der Erweiterung `.align` hochladen. Die Erweiterung `.align` signalisiert Custom Translator, dass die Satzzuordnung übersprungen werden soll.

Um optimale Ergebnisse zu erzielen, sollten Sie darauf achten, dass Ihre Dateien einen Satz pro Zeile enthalten. Verwenden Sie keine Zeilenumbruchzeichen in einem Satz, da dies zu Zuordnungsfehlern führen kann.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Empfohlene Mindestanzahl von extrahierten und zugeordneten Sätzen

In der folgenden Tabelle ist die für ein erfolgreiches Training erforderliche Mindestanzahl von extrahierten Sätzen und zugeordneten Sätzen in den einzelnen Datasets aufgeführt. Die empfohlene Mindestanzahl von extrahierten Sätzen ist wesentlich höher als die empfohlene Mindestanzahl von zugeordneten Sätzen. Dadurch wird der Tatsache Rechnung getragen, dass bei der Satzzuordnung möglicherweise nicht alle extrahierten Sätze erfolgreich zugeordnet werden können.

| Dataset   | Empfohlene Mindestanzahl von extrahierten Sätzen | Empfohlene Mindestanzahl von zugeordneten Sätzen | Höchstanzahl von zugeordneten Sätzen |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Training   | 10.000                                     | 2.000                                    | Keine Obergrenze                 |
| Optimierung     | 2.000                                      | 500                                      | 2.500                          |
| Testen    | 2.000                                      | 500                                      | 2.500                          |
| Wörterbuch | 0                                          | 0                                        | Keine Obergrenze                 |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie ein [Wörterbuch](what-is-dictionary.md) in Custom Translator verwenden.
