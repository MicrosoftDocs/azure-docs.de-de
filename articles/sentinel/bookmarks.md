---
title: Verwenden von Hunting-Lesezeichen für Datenuntersuchungen in Azure Sentinel
description: In diesem Artikel ist beschrieben, wie Azure Sentinel-Suchlesezeichen verwendet werden können, um den Überblick über Daten zu behalten.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: 5e3b6802bab2c5fae97be8e55c8d5c11ff570f78
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651311"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Behalten des Überblicks über Daten beim Hunting mit Azure Sentinel

Eine Bedrohungssuche erfordert in der Regel die Überprüfung sehr großer Mengen von Protokolldaten, um nach Beweisen für bösartiges Verhalten zu suchen. Während dieses Prozesses finden Ermittler Ereignisse, die sie sich merken, erneut besuchen und analysieren möchten, um mögliche Hypothesen zu validieren und die ganze Geschichte einer Gefährdung zu verstehen.

Hunting-Lesezeichen in Azure Sentinel helfen Ihnen dabei, indem sie die Abfragen, die Sie in **Azure Sentinel – Logs** ausgeführt haben, sowie die Abfrageergebnisse, die Sie für relevant halten, konservieren. Sie können auch Ihre kontextbezogenen Beobachtungen aufzeichnen und Ihre Ergebnisse durch Hinzufügen von Anmerkungen und Tags referenzieren. Mit Lesezeichen markierte Daten sind für Sie und Ihre Teamkollegen zur einfachen Zusammenarbeit sichtbar.

Sie können Ihre mit Lesezeichen markierten Daten jederzeit im Bereich **Hunting** auf der Registerkarte **Lesezeichen** erneut besuchen. Sie können Filter- und Suchoptionen verwenden, um bestimmte Daten für Ihre aktuelle Untersuchung schnell zu finden. Alternativ können Sie Ihre mit Lesezeichen markierten Daten direkt in der Tabelle **HuntingBookmark** in Ihrem Log Analytics-Arbeitsbereich anzeigen. Beispiel:

> [!div class="mx-imgBorder"]
> ![Anzeigen der Tabelle „HuntingBookmark“](./media/bookmarks/bookmark-table.png)

Durch Anzeigen von Lesezeichen aus der Tabelle können Sie mit Lesezeichen markierte Daten filtern, zusammenfassen und mit anderen Datenquellen verknüpfen, wodurch die Suche nach fundierten Beweisen vereinfacht wird.

Wenn Sie derzeit in der Vorschauversion etwas finden, das beim Hunting in Ihren Protokollen dringend behandelt werden muss, können Sie mit ein paar Klicks ein Lesezeichen erstellen und es zu einem Incident höher stufen oder das Lesezeichen zu einem bestehenden Incident hinzufügen. Weitere Informationen zu Incidents finden Sie unter [Tutorial: Untersuchen von Incidents mit Azure Sentinel](tutorial-investigate-cases.md). 

In der Vorschauversion können Sie auch Ihre mit Lesezeichen versehenen Daten darstellen, indem Sie in den Lesezeichendetails auf **Untersuchen** klicken. Dadurch wird der Untersuchungsvorgang gestartet, in dem Sie Ihre Ergebnisse mithilfe eines interaktiven Entität-Grafikdiagramms und einer Zeitachse anzeigen, untersuchen und visuell kommunizieren können.

## <a name="add-a-bookmark"></a>Hinzufügen eines Lesezeichens

1. Navigieren Sie im Azure-Portal zu **Sentinel** > **Bedrohungsverwaltung** > **Hunting**, um Abfragen zu verdächtigem und ungewöhnlichem Verhalten auszuführen.

2. Wählen Sie eine der Hunting-Abfragen und dann rechts in den Details der Hunting-Abfrage die Option **Abfrage ausführen** aus. 

3. Wählen Sie **Abfrageergebnisse anzeigen** aus. Beispiel:
    
    > [!div class="mx-imgBorder"]
    > ![Anzeigen von Abfrageergebnissen von Azure Sentinel-Hunting](./media/bookmarks/new-processes-observed-example.png)
    
    Diese Aktion öffnet die Abfrageergebnisse im Bereich **Protokolle**.

4. Verwenden Sie in der Liste mit den Protokollabfrageergebnissen die Kontrollkästchen, um eine oder mehrere Zeilen auszuwählen, in denen die für Sie interessanten Informationen enthalten sind.

5. Wählen Sie **Lesezeichen hinzufügen** aus:
    
    > [!div class="mx-imgBorder"]
    > ![Lesezeichen für Suche zur Abfrage hinzufügen](./media/bookmarks/add-hunting-bookmark.png)

6. Aktualisieren Sie optional im rechten Bereich **Lesezeichen hinzufügen** den Lesezeichennamen, und fügen Sie Tags und Anmerkungen hinzu, damit Sie später erkennen können, was an dem Element interessant war.

7. Verwenden Sie im Abschnitt **Abfrageinformationen** die Dropdownfelder, um Informationen aus den Abfrageergebnissen für die Entitätstypen **Konto**, **Host** und **IP-Adresse** zu extrahieren. Diese Aktion ordnet den ausgewählten Entitätstyp einer bestimmten Spalte aus dem Abfrageergebnis zu. Beispiel:
    
    > [!div class="mx-imgBorder"]
    > ![Zuordnen von Entitätstypen für Hunting-Lesezeichen](./media/bookmarks/map-entity-types-bookmark.png)
    
    Um das Lesezeichen im Untersuchungsdiagramm (derzeit in der Vorschauversion) anzuzeigen, müssen Sie mindestens einen Entitätstyp zuordnen, der entweder **Konto**, **Host** oder **IP-Adresse** lautet. 

5. Klicken Sie auf **Speichern**, um Ihre Änderungen zu übernehmen und das Lesezeichen hinzuzufügen. Alle mit Lesezeichen markierten Daten werden für andere Ermittler freigegeben, und dies ist ein erster Schritt in Richtung einer Umgebung für gemeinschaftliche Untersuchungen.

 
> [!NOTE]
> Die Ergebnisse der Protokollabfrage unterstützen Lesezeichen, wenn dieser Bereich von Azure Sentinel aus geöffnet wird. Sie wählen z. B. **Allgemein** > **Protokolle** auf der Navigationsleiste, dann Ereignislinks im Untersuchungsdiagramm oder eine Warnungs-ID aus den vollständigen Details eines Incidents aus (derzeit in der Vorschauversion). Sie können keine Lesezeichen erstellen, wenn der Bereich **Protokolle** von anderen Positionen aus geöffnet wird, z. B. direkt über Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Anzeigen und Aktualisieren von Lesezeichen 

1. Navigieren Sie im Azure-Portal zu **Sentinel** > **Bedrohungsverwaltung** > **Hunting**. 

2. Wählen Sie die Registerkarte **Lesezeichen** aus, um die Liste der Lesezeichen anzuzeigen.

3. Um Ihnen bei der Suche nach einem bestimmten Lesezeichen zu helfen, verwenden Sie das Suchfeld oder die Filteroptionen.

4. Wählen Sie einzelne Lesezeichen aus, um die Lesezeichendetails im rechten Detailbereich anzuzeigen.

5. Nehmen Sie bei Bedarf Ihre Änderungen vor, die automatisch gespeichert werden.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Untersuchen von Lesezeichen im Untersuchungsdiagramm

> [!IMPORTANT]
> Das Untersuchen von Lesezeichen im Untersuchungsdiagramm sowie das Untersuchen des Untersuchungsdiagramms selbst befindet sich derzeit in der Public Preview.
> Dieses Features werden ohne Vereinbarung zum Servicelevel bereitgestellt und sind nicht für Produktionsworkloads vorgesehen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Navigieren Sie im Azure-Portal zur Registerkarte **Sentinel** > **Bedrohungsverwaltung** > **Hunting** > **Lesezeichen**, und wählen Sie das bzw. die Lesezeichen aus, die Sie untersuchen möchten.

2. Stellen Sie in den Lesezeichendetails sicher, dass mindestens eine Entität zugeordnet ist. Beispielsweise werden für **ENTITÄTEN** Einträge für **IP**, **Computer** oder **Konto** angezeigt.

3. Klicken Sie auf **Untersuchen**, um das Lesezeichen im Untersuchungsdiagramm anzuzeigen.

Anweisungen zur Verwendung des Untersuchungsdiagramms finden Sie unter [Ausführliche Untersuchung mit dem Untersuchungsdiagramm](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Hinzufügen von Lesezeichen zu einem neuen oder bestehenden Incident

> [!IMPORTANT]
> Das Hinzufügen von Lesezeichen zu einem neuen oder bestehenden Incident befindet sich derzeit in der Public Preview.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Navigieren Sie im Azure-Portal zur Registerkarte **Sentinel** > **Bedrohungsverwaltung** > **Hunting** > **Lesezeichen**, und wählen Sie das bzw. die Lesezeichen aus, die Sie zu einem Incident hinzufügen möchten.

2. Wählen Sie **Incidentaktionen (Vorschauversion)** auf der Befehlsleiste aus:
    
    > [!div class="mx-imgBorder"]
    > ![Lesezeichen zu Incident hinzufügen](./media/bookmarks/incident-actions.png)

3. Wählen Sie bei Bedarf entweder **Neuen Incident erstellen** oder **Zum vorhandenem Incident hinzufügen** aus. Führen Sie dann folgende Schritte aus:
    
    - Für einen neuen Incident: Aktualisieren Sie optional die Details für den Incident, und wählen Sie dann **Erstellen** aus.
    - Zum Hinzufügen eines Lesezeichens zu einem vorhandenen Incident: Wählen Sie einen Incident und anschließend **Hinzufügen** aus. 

So zeigen Sie das Lesezeichen innerhalb des Incidents an Navigieren Sie zu **Sentinel** > **Bedrohungsverwaltung** > **Incidents**, und wählen Sie den Incident mit Ihrem Lesezeichen aus. Wählen Sie **Alle Informationen anzeigen** und dann die Registerkarte **Lesezeichen** aus.

> [!TIP]
> Als Alternative zur Option **Incidentaktionen (Vorschau)** in der Befehlsleiste können Sie das Kontextmenü (**...**) für mindestens ein Lesezeichen verwenden, um Optionen zu **Neuen Incident erstellen**, **Zu vorhandenem Incident hinzufügen** und **Aus Incident entfernen** auszuwählen. 

## <a name="view-bookmarked-data-in-logs"></a>Anzeigen von mit Lesezeichen markierten Daten in Protokollen

Wählen Sie das Lesezeichen auf der Registerkarte **Hunting** > **Lesezeichen** aus, um die mit Lesezeichen versehenen Abfragen, Ergebnisse oder deren Verlauf anzuzeigen, und verwenden Sie die im Detailbereich angegebenen Links: 

- **Quellabfrage anzeigen**, um die Quellabfrage im Bereich **Protokolle** anzuzeigen.

- **Lesezeichenprotokolle anzeigen**, um alle Lesezeichenmetadaten anzuzeigen, z. B. die Person, die die Aktualisierung vorgenommen hat, die aktualisierten Werte und die Uhrzeit, zu der die Aktualisierung erfolgt ist.

Sie können auch die unformatierten Lesezeichendaten für alle Lesezeichen anzeigen, indem Sie **Lesezeichenprotokolle** auf der Registerkarte **Hunting** > **Lesezeichen** auf der Befehlsleiste auswählen:

> [!div class="mx-imgBorder"]
> ![Lesezeichenprotokolle](./media/bookmarks/bookmark-logs.png)

In dieser Ansicht werden alle Ihre Lesezeichen mit den zugehörigen Metadaten angezeigt. Sie können KQL-Abfragen ([Keyword Query Language](/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference)) verwenden, um nach der neuesten Version des bestimmten Lesezeichens zu filtern, das Sie suchen.

> [!NOTE]
> Es kann eine erhebliche Verzögerung (gemessen in Minuten) zwischen der Erstellung eines Lesezeichens und dem Zeitpunkt liegen, ab dem es auf der Registerkarte **Lesezeichen** angezeigt wird.

## <a name="delete-a-bookmark"></a>Löschen eines Lesezeichens
 
1.  Navigieren Sie im Azure-Portal zur Registerkarte **Sentinel** > **Bedrohungsverwaltung** > **Hunting** > **Lesezeichen**, und wählen Sie das bzw. die Lesezeichen aus, die Sie löschen möchten. 

2. Klicken Sie mit der rechten Maustaste auf Ihre Auswahl, und wählen Sie die Option zum Löschen des Lesezeichens bzw. der Lesezeichen aus. Beispiel: **Lesezeichen löschen**, wenn Sie nur ein Lesezeichen ausgewählt haben, und **2 Lesezeichen löschen**, wenn Sie zwei Lesezeichen ausgewählt haben.
    
Das Löschen eines Lesezeichens bewirkt, dass das Lesezeichen aus der Liste auf der Registerkarte **Lesezeichen** (Bookmarks) entfernt wird. In der Tabelle **HuntingBookmark** für Ihren Log Analytics-Arbeitsbereich sind frühere Lesezeicheneinträge weiter enthalten, aber für den letzten Eintrag wird der Wert von **SoftDelete** in „true“ geändert, um das Filtern nach alten Lesezeichen zu erleichtern. Das Löschen eines Lesezeichens bewirkt nicht, dass irgendwelche Entitäten aus den Untersuchungsvorgängen entfernt werden, die mit anderen Lesezeichen oder Benachrichtigungen verknüpft sind. 


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Bedrohungsuntersuchung mithilfe von Lesezeichen in Azure Sentinel ausführen. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:


- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Verwenden von Notebooks zur Ermittlung von Anomalien](notebooks.md)