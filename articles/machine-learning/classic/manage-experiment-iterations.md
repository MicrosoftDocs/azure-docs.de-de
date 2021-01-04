---
title: 'ML Studio (Classic): Anzeigen und erneutes Ausführen von Experimenten (Azure)'
description: Verwalten von Experimentausführungen in Azure Machine Learning Studio (Classic). Sie können die vorherigen Testläufe der Experimente zu jedem beliebigen Zeitpunkt noch einmal überprüfen, um die vorherigen Annahmen infrage zu stellen, neu aufzugreifen und letztendlich entweder zu bestätigen oder zu verfeinern.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: c958de5e49fbb7519ae71b13e9deba9af0c698de
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312875"
---
# <a name="manage-experiment-runs-in-azure-machine-learning-studio-classic"></a>Verwalten von Experimentausführungen in Azure Machine Learning Studio (Classic)

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Das Entwickeln eines solchen Vorhersageanalysemodells ist ein iterativer Prozess: Sie ändern die verschiedenen Funktionen und Parameter des Experiments, und die Ergebnisse nähern sich solange an, bis Sie der Ansicht sind, ein trainiertes, effektives Modell erreicht zu haben. Wichtig für diesen Prozess ist das Verfolgen der verschiedenen Iterationen der Experimentparameter und -konfigurationen.

Sie können die vorherigen Testläufe der Experimente zu jedem beliebigen Zeitpunkt noch einmal überprüfen, um die vorherigen Annahmen infrage zu stellen, neu aufzugreifen und letztendlich entweder zu bestätigen oder zu verfeinern. Wenn Sie ein Experiment ausführen, speichert Machine Learning Studio (klassisch) den Verlauf des Testlaufs, einschließlich Dataset, Modul sowie Portverbindungen und Parameter. In diesem Verlauf werden auch Ergebnisse, Laufzeitinformationen, z. B. Start- und Endzeiten, Protokollmeldungen und der Ausführungsstatus aufgezeichnet. Sie können jeden dieser Testläufe jederzeit überprüfen, um die Chronologie des Experiments und die Zwischenergebnisse  zu betrachten. Ein vorheriger Testlauf des Experiment kann sogar den Ausgangspunkt einer neuen Untersuchungsphase auf dem Weg zur Erstellung einfacher, komplexer oder mehrfacher Modellierungslösungen bilden.

> [!NOTE]
> Wenn Sie einen vorherigen Testlauf des Experiments anzeigen, wird diese Version des Experiments gesperrt und kann daher nicht bearbeitet werden. Sie können jedoch eine Kopie speichern, indem Sie auf **SAVE AS** (SPEICHERN UNTER) klicken und einen neuen Namen für die Kopie eingeben. Machine Learning Studio (klassisch) öffnet die neue Kopie, die Sie dann bearbeiten und ausführen können. Diese Kopie des Experiments steht in der Liste **EXPERIMENTS** (EXPERIMENTE) zusammen mit allen anderen Experimenten zur Verfügung.
> 
> 

## <a name="view-the-prior-run"></a>Anzeigen des vorherigen Testlaufs
Wenn ein Experiment geöffnet ist, das Sie mindestens einmal ausgeführt haben, können Sie den vorherigen Testlauf des Versuchs anzeigen, indem Sie im Eigenschaftenbereich auf **Prior Run** (Vorheriger Testlauf) klicken.

Angenommen, Sie erstellen ein Experiment und führen Versionen dieses Experiments um 11:23 Uhr 11:42 Uhr und 11:55 Uhr aus. Wenn Sie den letzten Testlauf des Experiments (11:55) öffnen und auf **Prior Run** (Vorheriger Testlauf) klicken, wird die Version, die Sie um 11:42 Uhr ausgeführt haben, geöffnet.

## <a name="view-the-run-history"></a>Anzeigen des Ausführungsverlaufs
Sie können alle vorherigen Testläufe eines Experiments anzeigen, indem Sie in einem geöffneten Experiment auf **View Run History** (Ausführungsverlauf anzeigen) klicken.

Angenommen, Sie erstellen ein Experiment mit dem Modul [Linear Regression][linear-regression], und Sie möchten beobachten, wie sich Änderungen des Werts von **Learning Rate** (Lernrate) auf die Ergebnisse Ihres Experiments auswirken. Sie führen das Experiment mehrmals mit unterschiedlichen Werten für diesen Parameter wie folgt aus:

| Der Wert von "Learning Rate" | Startzeit des Testlaufs |
| --- | --- |
| 0,1 |11.9.2014 16:18:58 |
| 0.2 |11.9.2014 16:24:33 |
| 0,4 |11.9.2014 16:28:36 |
| 0.5 |11.9.2014 16:33:31 |

Wenn Sie auf **VIEW RUN HISTORY** (AUSFÜHRUNGSVERLAUF ANZEIGEN) klicken, wird eine Liste aller Testläufe angezeigt:

![Beispiel für einen Ausführungsverlauf](./media/manage-experiment-iterations/viewrunhistory.jpg)

Klicken Sie auf einen dieser Testläufe, um eine Momentaufnahme des Experiments vom Zeitpunkt der Ausführung anzuzeigen. Die Konfiguration, die Parameterwerte, die Kommentare und die Ergebnisse werden alle gespeichert, damit eine vollständige Aufzeichnung dieses Testlaufs des Experiments zur Verfügung steht.

> [!TIP]
> Um die Iterationen des Experiments zu dokumentieren, können Sie den Titel der einzelnen Testläufe ändern, die **Zusammenfassung** des Experiments im Eigenschaftenbereich aktualisieren und einzelnen Module Kommentare hinzufügen oder die Kommentare aktualisieren, um die Änderungen zu protokollieren. Titel, Zusammenfassung und Modulkommentare werden bei jedem Testlauf des Experiments gespeichert.
> 
> 

In der Liste der Experimente auf der Registerkarte **EXPERIMENTS** in Machine Learning Studio (klassisch) wird immer die neueste Version eines Experiments angezeigt. Wenn Sie einen früheren Testlauf des Experiments öffnen (mit **Prior Run** [Vorheriger Testlauf] oder **VIEW RUN HISTORY** [AUSFÜHRUNGSVERLAUF ANZEIGEN]), können Sie zur Entwurfsversion zurückkehren, indem Sie auf **VIEW RUN HISTORY** klicken und die Iteration auswählen, für die unter **STATE** (STATUS) der Status **Editable** (Bearbeitbar) angezeigt wird.

## <a name="run-a-previous-experiment"></a>Ausführen eines vorhergehenden Experiments
Wenn Sie auf **Prior Run** (Vorheriger Testlauf) oder **VIEW RUN HISTORY** (AUSFÜHRUNGSVERLAUF ANZEIGEN) klicken und einen vorherigen Testlauf öffnen, wird ein fertiges Experiment im schreibgeschützten Modus angezeigt.

Wenn Sie ausgehend von der Konfiguration eines vorherigen Testlaufs eine Iteration des Experiments beginnen möchten, können Sie dazu den Testlauf öffnen und auf **SAVE AS** (SPEICHERN UNTER) klicken. Damit wird ein neues Experiment mit einem neuen Titel, einem leeren Ausführungsverlauf sowie allen Komponenten und Parameterwerten des vorherigen Testlaufs erstellt. Dieses neue Experiment wird auf der Registerkarte **EXPERIMENTS** auf der Startseite von Machine Learning Studio (klassisch) aufgeführt. Sie können es ändern und ausführen und dadurch einen neuen Ausführungsverlauf für diese Iteration des Experiments anlegen. 

Nehmen wir beispielsweise an, Sie haben den Ausführungsverlauf des Experiments aus dem vorherigen Abschnitt. Sie möchten sehen, was geschieht, wenn Sie den Parameter **Learning rate** (Lernrate) auf 0,4 festlegen und verschiedene Werte für den Parameter **Number of training epochs** (Anzahl der Trainingsperioden) ausprobieren.

1. Klicken Sie auf **VIEW RUN HISTORY** (AUSFÜHRUNGSVERLAUF ANZEIGEN), und öffnen Sie die Iteration des Experiments, die Sie um 16:28:36 Uhr ausgeführt haben (in der Sie den Parameterwert 0,4 festgelegt haben).
2. Klicken Sie auf **SAVE AS** (SPEICHERN UNTER).
3. Geben Sie einen neuen Titel ein, und klicken Sie auf das Häkchen für **OK** . Es wird eine neue Kopie des Experiments erstellt.
4. Ändern Sie den Parameter **Number of training epochs** (Anzahl der Trainingsperioden).
5. Klicken Sie auf **RUN** (AUSFÜHREN).

Sie können diese Version  des Experiments nun weiterhin ändern und ausführen, wobei ein neuer Ausführungsverlauf zur Dokumentation Ihrer Arbeit erstellt wird.

<!-- Module References -->
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression