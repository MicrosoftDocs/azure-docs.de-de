---
title: Behalten des Überblicks über Daten bei einer Bedrohungssuche in Azure Sentinel Preview mithilfe von Suchlesezeichen | Microsoft-Dokumentation
description: In diesem Artikel ist beschrieben, wie Azure Sentinel-Suchlesezeichen verwendet werden können, um den Überblick über Daten zu behalten.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b1a438b9645dbb37d852eb0092355850d816872d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65207467"
---
# <a name="keep-track-of-data-during-hunting"></a>Behalten des Überblicks über Daten bei einer Bedrohungssuche

> [!IMPORTANT]
> Azure Sentinel ist aktuell als Public Preview verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Eine Bedrohungssuche erfordert in der Regel die Überprüfung sehr großer Mengen von Protokolldaten, um nach Beweisen für bösartiges Verhalten zu suchen. Während dieses Prozesses finden Ermittler Ereignisse, die sie sich merken, erneut besuchen und analysieren möchten, um mögliche Hypothesen zu validieren und die ganze Geschichte einer Gefährdung zu verstehen.
Suchlesezeichen helfen Ihnen dabei, indem sie die Abfragen, die Sie in Log Analytics ausgeführt haben, sowie die Abfrageergebnisse, die Sie für relevant halten, konservieren. Sie können auch Ihre kontextbezogenen Beobachtungen aufzeichnen und Ihre Ergebnisse durch Hinzufügen von Anmerkungen und Tags referenzieren. Mit Lesezeichen markierte Daten sind für Sie und Ihre Teamkollegen zur einfachen Zusammenarbeit sichtbar.   

Sie können Ihre mit Lesezeichen markierten Daten jederzeit auf der Seite **Suche** (Hunting) auf der Registerkarte **Lesezeichen** erneut besuchen. Sie können Filter- und Suchoptionen verwenden, um bestimmte Daten für Ihre aktuelle Untersuchung schnell zu finden. Alternativ können Sie Ihre mit Lesezeichen markierten Daten direkt in der Tabelle **HuntingBookmark** in Log Analytics anzeigen. Auf diese Weise können Sie mit Lesezeichen markierte Daten mit anderen Datenquellen filtern, zusammenfassen und verknüpfen, wodurch die Suche nach fundierten Beweisen vereinfacht wird.

Außerdem können Sie Ihre mit Lesezeichen markierten Daten visualisieren, indem Sie auf **Untersuchen** klicken. Dadurch wird der Untersuchungsvorgang gestartet, in dem Sie Ihre Ergebnisse mithilfe eines interaktiven Entität-Grafikdiagramms und einer Zeitachse anzeigen, untersuchen und visuell kommunizieren können.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Ausführen einer Log Analytics-Abfrage aus Azure Sentinel

1. Klicken Sie im Azure Sentinel-Portal auf **Suche** (Hunting), um Abfragen zu verdächtigem und regelwidrigem Verhalten auszuführen.

1. Um eine Bedrohungssuche auszuführen, wählen Sie eine Bedrohungsabfrage aus, und überprüfen Sie die Ergebnisse auf der linken Seite. 

1. Klicken Sie auf **Abfrageergebnisse anzeigen** (View query results) auf der **Details**-Seite der Bedrohungsabfrage, um die Abfrageergebnisse in Log Analytics anzuzeigen. Es folgt ein Beispiel dafür, was Sie sehen, wenn Sie eine benutzerdefinierte SSH-Brute-Force-Angriffsabfrage ausführen.
  
   ![Ergebnisse anzeigen](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Hinzufügen eines Lesezeichens

1. Erweitern Sie in der Liste der Log Analytics-Abfrageergebnisse die Zeile, die die Informationen enthält, an denen Sie interessiert sind.

4. Wählen Sie die Auslassungspunkte (...) am Ende der Zeile aus, und wählen **Suchlesezeichen hinzufügen** (Add hunting bookmarks) aus.
5. Auf der rechts befindlichen Seite **Details** können Sie den Namen aktualisieren sowie Tags und Anmerkungen hinzufügen, damit Sie später erkennen können, was an dem Element interessant war.
6. Klicken Sie auf **Speichern**, um die vorgenommenen Änderungen zu speichern. Alle mit Lesezeichen markierten Daten werden für andere Ermittler freigegeben, und dies ist ein erster Schritt in Richtung einer Umgebung für gemeinschaftliche Untersuchungen.

   ![Ergebnisse anzeigen](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> Sie können auch Lesezeichen mit beliebigen Log Analytics-Abfragen, die über die Azure Sentinel-Seite für Log Analytics-Protokolle gestartet wurden, oder von Abfragen verwenden, die direkt auf der Log Analytics-Seite erstellt und über die Seite „Suche“ (Hunting) geöffnet wurden. Sie können kein Lesezeichen hinzufügen, wenn Sie Log Analytics außerhalb von Azure Sentinel starten. 

## <a name="view-and-update-bookmarks"></a>Anzeigen und Aktualisieren von Lesezeichen 

1. Klicken Sie im Azure Sentinel-Portal auf **Suche** (Hunting). 
2. Klicken Sie auf die Registerkarte **Lesezeichen** in der Mitte der Seite, um die Liste der Lesezeichen anzuzeigen.
3. Verwenden Sie das Suchfeld oder Filteroptionen, um nach einem bestimmten Lesezeichen zu suchen.
4. Wählen Sie ein einzelnes Lesezeichen im Raster unten aus, um die Lesezeichendetails im rechten Detailbereich anzuzeigen.
5. Um Tags und Anmerkungen zu aktualisieren, klicken Sie auf die bearbeitbaren Textfelder, und klicken Sie auf **Speichern**, damit Ihre Änderungen beibehalten werden.

   ![Ergebnisse anzeigen](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Anzeigen von mit Lesezeichen markierten Daten in Log Analytics 

Es gibt mehrere Optionen zum Anzeigen Ihrer mit Lesezeichen markierten Daten in Log Analytics. 

Die einfachste Möglichkeit zum Anzeigen von Abfragen, Ergebnissen oder Verläufen, die mit Lesezeichen markiert sind, besteht darin, das gewünschte Lesezeichen in der Tabelle **Lesezeichen** auszuwählen und die Links zu verwenden, die im Detailbereich bereitgestellt werden. Beispiele für Optionen: 
- Klicken Sie auf **Abfrage anzeigen**, um die Quellabfrage in Log Analytics anzuzeigen.  
- Klicken Sie auf **Lesezeichenverlauf anzeigen** (View bookmark history), um alle Lesezeichenmetadaten anzuzeigen: die Person, die die Aktualisierung vorgenommen hat, die aktualisierten Werte und die Uhrzeit, zu der die Aktualisierung erfolgt ist. 

- Sie können auch die unformatierten Lesezeichendaten für alle Lesezeichen anzeigen, indem Sie auf **Lesezeichenprotokolle** (Bookmark logs) über dem Lesezeichenraster klicken. In dieser Ansicht werden alle Ihre Lesezeichen in der Suchlesezeichentabelle mit den zugehörigen Metadaten angezeigt. Sie können KQL-Abfragen verwenden, um nach der neuesten Version des bestimmten Lesezeichens zu filtern, das Sie suchen.  


> [!NOTE]
> Es kann eine erhebliche Verzögerung (gemessen in Minuten) zwischen der Erstellung eines Lesezeichens und dem Zeitpunkt liegen, ab dem es in der **HuntingBookmark**-Tabelle angezeigt wird. Es wird empfohlen, zuerst Ihre Lesezeichen zu erstellen und sie dann zu analysieren, nachdem die Daten erfasst wurden. 

## <a name="delete-a-bookmark"></a>Löschen eines Lesezeichens
Wenn Sie ein Lesezeichen löschen möchten, führen Sie folgende Schritte aus: 
1.  Öffnen Sie die Registerkarte **Suchlesezeichen** (Hunting bookmark). 
2.  Wählen Sie das Ziellesezeichen aus.
3.  Wählen Sie die Auslassungspunkte (...) am Ende der Zeile aus, und wählen **Lesezeichen löschen** (Delete bookmark) aus.
    
Das Löschen eines Lesezeichens bewirkt, dass das Lesezeichen aus der Liste auf der Registerkarte **Lesezeichen** (Bookmarks) entfernt wird.  In der Log Analytics-Tabelle „HuntingBookmark“ sind frühere Lesezeicheneinträge weiter enthalten, aber für den letzten Eintrag wird der Wert von **SoftDelete** in „true“ geändert, damit einfach nach alten Lesezeichen gefiltert werden kann.  Das Löschen eines Lesezeichens bewirkt nicht, dass irgendwelche Entitäten aus den Untersuchungsvorgängen entfernt werden, die mit anderen Lesezeichen oder Benachrichtigungen verknüpft sind. 


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Bedrohungsuntersuchung mithilfe von Lesezeichen in Azure Sentinel ausführen. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:


- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Verwenden von Notebooks zur Ermittlung von Anomalien](notebooks.md)