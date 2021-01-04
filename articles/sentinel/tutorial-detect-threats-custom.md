---
title: Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen mit Azure Sentinel | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie benutzerdefinierte Analyseregeln erstellen, um mit Azure Sentinel Sicherheitsbedrohungen zu erkennen. Nutzen Sie die Ereignis- und Warnungsgruppierung, und informieren Sie sich über „AUTO DISABLED“.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: b685f716688cfbe732fa7d3566e1af97cc81272a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94652109"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Tutorial: Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen

Nachdem Sie [Ihre Datenquellen mit Azure Sentinel verbunden](quickstart-onboard.md) haben, können Sie benutzerdefinierte Regeln erstellen, mit denen Sie in Ihrer Umgebung nach bestimmten Kriterien suchen und bei einer Übereinstimmung Vorfälle generieren. Diese können Sie anschließend untersuchen. In diesem Tutorial erfahren Sie, wie Sie benutzerdefinierte Regeln zum Erkennen von Bedrohungen mit Azure Sentinel erstellen.

Dieses Tutorial unterstützt Sie bei der Gefahrenerkennung mit Azure Sentinel.
> [!div class="checklist"]
> * Erstellen von Analyseregeln
> * Automatisieren der Reaktionen auf Bedrohungen

## <a name="create-custom-analytics-rules"></a>Erstellen benutzerdefinierter Analyseregeln

Für die Suche nach verdächtigen Typen von Bedrohungen und Anomalien in Ihrer Umgebung können Sie benutzerdefinierte Analyseregeln erstellen. Diese Regeln stellen sicher, dass Sie im Bedarfsfall sofort benachrichtigt werden, um die Bedrohungen einzugrenzen, zu überprüfen und zu beheben.

1. Wählen Sie im Azure-Portal unter „Azure Sentinel“ die Option **Analytics** aus.

1. Wählen Sie in der oberen Menüleiste die Option **+Erstellen** und anschließend **Geplante Abfrageregel** aus. Dadurch wird der **Assistent für Analyseregeln** geöffnet.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query.png" alt-text="Erstellen einer geplanten Abfrage":::

1. Geben Sie auf der Registerkarte **Allgemein** einen eindeutigen **Namen** und eine **Beschreibung** an. Im Feld **Taktiken** stehen verschiedene Angriffskategorien zur Auswahl, nach denen die Regel klassifiziert werden kann. Legen Sie den **Schweregrad** der Warnung nach Bedarf fest. Beim Erstellen der Regel wird der **Status** standardmäßig auf **Aktiviert** eingestellt. Dies bedeutet, dass die Regel nach der Erstellung sofort ausgeführt wird. Wenn Sie die Regel nicht sofort ausführen möchten, wählen Sie **Deaktiviert** aus. Die Regel wird der Registerkarte **Aktive Regeln** hinzugefügt, von wo Sie diese bei Bedarf aktivieren können.

    ![Beginnen mit dem Erstellen einer benutzerdefinierten Analyseregel](media/tutorial-detect-threats-custom/general-tab.png)

1. Auf der Registerkarte **Regellogik festlegen** können Sie im Feld **Regelabfrage** eine Abfrage direkt eingeben. Alternativ können Sie auch in Log Analytics eine Abfrage erstellen, diese kopieren und in das Feld einfügen.
 
   ![Erstellen einer Abfrage in Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Beachten Sie auf der rechten Seite den Bereich **Ergebnisvorschau**, in dem Azure Sentinel die Anzahl der von der Abfrage generierten Ergebnisse (Protokollereignisse) anzeigt. Die Anzeige wird beim Schreiben und Konfigurieren der Abfrage dynamisch geändert. Das Diagramm zeigt die Anzahl der Ergebnisse im definierten Zeitraum, der durch die Einstellungen im Abschnitt **Abfrageplanung** bestimmt wird.
    - Wenn Sie feststellen, dass Ihre Abfrage zu häufig bzw. zu viele Warnungen auslöst, können Sie im Abschnitt **Warnungsschwellenwert** eine Baseline festlegen.

      Mit der folgenden Beispielabfrage werden Sie gewarnt, wenn in Azure Activity eine ungewöhnliche Anzahl von Ressourcen erstellt wird.

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > - Die Abfrage sollte zwischen 1 und 10.000 Zeichen lang sein und darf weder „search \*“ noch „union \*“ enthalten.
        >
        > - Das Erstellen von Azure Data Explorer-Abfragen mit ADX-Funktionen innerhalb des Log Analytics-Abfragefensters **wird nicht unterstützt**.

    1. Im Abschnitt **Entitäten zuordnen** können Sie Parameter aus den Abfrageergebnissen mit von Azure Sentinel erkannten Entitäten verknüpfen. Diese Entitäten bilden die Grundlage für weitere Analysen und auch für das Gruppieren von Warnungen in Incidents auf der Registerkarte **Incidenteinstellungen**.
  
    1. Legen Sie im Abschnitt **Abfrageplanung** die folgenden Parameter fest:

       1. Legen Sie unter **Abfrage ausführen alle** fest, wie häufig die Abfrage ausgeführt werden soll: häufig (alle fünf Minuten) oder selten (einmal am Tag).

       1. Legen Sie unter **Datensuche für letzte** den Zeitraum der von der Abfrage abgedeckten Daten fest: z. B. Daten der letzten 10 Minuten oder der letzten 6 Stunden.

          > [!NOTE]
          > **Abfrageintervalle und Rückblickperiode**
          > - Diese beiden Einstellungen sind bis zu einem Punkt voneinander unabhängig. Sie können in einem kurzen Intervall eine Abfrage für einen Zeitraum ausführen, der länger ist als das Intervall (was zu sich überschneidenden Abfragen führt). Sie können jedoch für eine Abfrage kein Intervall festlegen, das den Abdeckungszeitraum überschreitet, da dies zu Lücken in der Gesamtabdeckung der Abfrage führen würde.
          >
          > **Erfassungsverzögerung**
          > - Azure Sentinel führt geplante Analyseregeln mit einer **fünfminütigen Verzögerung** nach der geplanten Zeit aus, um die **Wartezeit** zu berücksichtigen, die zwischen der Erstellung eines Ereignisses in der Quelle und dessen Erfassung in Azure Sentinel auftreten kann, und um eine vollständige Abdeckung ohne Datenduplizierung zu gewährleisten.

    1. Definieren Sie im Abschnitt **Warnungsschwellenwert** eine Baseline. Legen Sie beispielsweise **Warnung generieren, wenn für die Anzahl der Abfrageergebnisse Folgendes gilt:** auf **Ist größer als** fest, und geben Sie die Zahl 1000 ein, wenn die Regel nur dann eine Warnung generieren soll, wenn die Abfrage bei jeder Ausführung mehr als 1000 Ergebnisse zurückgibt. Da dies ein Pflichtfeld ist, müssen Sie, wenn Sie keine Baseline festlegen möchten (d. h., wenn bei jedem Ereignis eine Warnung registriert werden soll), im Zahlenfeld den Wert „0“ eingeben.
    
    1. Wählen Sie unter **Ereignisgruppierung** eine von zwei Methoden aus, um **Ereignisse** in **Warnungen** zu gruppieren: 

       - **Group all events into a single alert** (Alle Ereignisse in einer einzelnen Warnung gruppieren) (Standardeinstellung). Die Regel generiert bei jeder Ausführung eine einzelne Warnung, solange die Anzahl der von der Abfrage zurückgegebenen Ergebnisse den oben angegebenen **Warnungsschwellenwert** überschreitet. Die Warnung enthält eine Zusammenfassung aller in den Ergebnissen zurückgegebenen Ereignisse. 

       - **Warnung für jedes Ereignis auslösen**. Die Regel generiert eine eindeutige Warnung für jedes Ereignis, das von der Abfrage zurückgegeben wird. Dies ist hilfreich, wenn die Ereignisse einzeln angezeigt oder nach bestimmten Parametern – etwa Benutzer oder Hostname – gruppiert werden sollen. Sie können diese Parameter in der Abfrage definieren.
    
       Derzeit ist die Anzahl von Warnungen, die eine Regel generieren kann, auf 20 begrenzt. Wenn in einer bestimmten Regel **Ereignisgruppierung** auf **Warnung für jedes Ereignis auslösen** festgelegt ist und die Abfrage der Regel mehr als 20 Ereignisse zurückgibt, generiert jedes der ersten 19 Ereignisse eine eindeutige Warnung, und die 20. Warnung fasst den gesamten Satz zurückgegebener Ereignisse zusammen. Mit anderen Worten: Die 20. Warnung ist die Warnung, die auch mit der Option **Group all events into a single alert** (Alle Ereignisse in einer einzelnen Warnung gruppieren) generiert wird.

       > [!NOTE]
       > Was ist der Unterschied zwischen **Ereignissen** und **Warnungen**?
       >
       > - Ein **Ereignis** ist eine Beschreibung eines einzelnen Vorkommens. Beispielsweise kann ein einzelner Eintrag in einer Protokolldatei als Ereignis gezählt werden. In diesem Kontext bezeichnet ein Ereignis ein einzelnes Ergebnis, das von einer Abfrage in einer Analyseregel zurückgegeben wird.
       >
       > - Eine **Warnung** ist eine Sammlung von Ereignissen, die gemeinsam im Hinblick auf die Sicherheit von Bedeutung sind. Eine Warnung kann ein einzelnes Ereignis beinhalten, wenn das Ereignis bedeutende Auswirkungen auf die Sicherheit hat, z. B. eine Administratoranmeldung aus dem Ausland außerhalb der Geschäftszeiten.
       >
       > - Und was sind **Incidents**? Die interne Logik von Azure Sentinel erstellt **Incidents** aus **Warnungen** oder Gruppen von Warnungen. Die Arbeit der Analysten – Selektierung, Untersuchung und Abhilfe – konzentriert sich auf die Incidentwarteschlange.
       > 
       > Azure Sentinel erfasst unformatierte Ereignisse aus Datenquellen und bereits verarbeitete Warnungen von anderen. Es muss jederzeit unterschieden werden, ob es sich um Ereignisse oder Warnungen handelt.

       > [!IMPORTANT]
       > Die Ereignisgruppierung ist derzeit als öffentliche Vorschauversion verfügbar. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. Im Abschnitt **Unterdrückung** können Sie die Einstellung **Ausführung der Abfrage beenden, wenn eine Warnung generiert wurde** auf **Ein** festlegen, wenn Sie nach dem Eingang einer Warnung das Ausführen dieser Regel für einen Zeitraum unterbrechen möchten, der das Abfrageintervall überschreitet. Wenn Sie diese Option aktivieren, müssen Sie unter **Abfrageausführung beenden für:** den Zeitraum festlegen, in dem die Abfrage nicht ausgeführt werden soll (bis zu 24 Stunden).

1. Auf der Registerkarte **Incidenteinstellungen** können Sie auswählen, ob und wie Azure Sentinel Warnungen in verwertbare Incidents umwandelt. Wenn Sie diese Registerkarte unverändert lassen, erstellt Azure Sentinel einen eigenen separaten Incident von jeder einzelnen Warnung. Durch Ändern der Einstellungen auf dieser Registerkarte können Sie festlegen, dass keine Incidents erstellt oder mehrere Warnungen zu einem einzelnen Incident gruppiert werden.

   > [!IMPORTANT]
   > Die Registerkarte „Incidenteinstellungen“ ist derzeit als öffentliche Vorschauversion verfügbar. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. Im Abschnitt **Incidenteinstellungen** ist die Option **Incidents aus Warnungen erstellen, die von dieser Analyseregel ausgelöst werden** standardmäßig auf **Aktiviert** eingestellt. Dies bedeutet, dass Azure Sentinel einen eigenen separaten Incident von jeder einzelnen Warnung erstellt, die von der Regel ausgelöst wird.
       - Wenn durch diese Regel nicht alle Incidents erstellt werden sollen (z. B. wenn Sie mit dieser Regel nur Informationen für die nachfolgende Analyse sammeln möchten), legen Sie diese Einstellung auf **Deaktiviert** fest.

    1. Wenn aus einer Gruppe von bis zu 150 ähnlichen oder wiederkehrenden Warnungen (siehe Hinweis) ein einzelner Incident erstellt werden soll, legen Sie im Abschnitt **Warnungsgruppierung** die Option **Alle verwandten Warnungen, die durch diese Analyseregel ausgelöst werden, in Incidents gruppieren** auf **Aktiviert** fest, und legen Sie die folgenden Parameter fest.

    - **Gruppe auf Warnungen beschränken, die innerhalb des ausgewählten Zeitraums erstellt werden**: Bestimmen Sie den Zeitraum, in dem ähnliche oder wiederkehrende Warnungen gruppiert werden sollen. Alle entsprechenden Warnungen innerhalb dieses Zeitraums generieren zusammen einen Incident oder eine Gruppe von Incidents (abhängig von den unten aufgeführten Gruppierungseinstellungen). Bei Warnungen außerhalb dieses Zeitraums wird ein separater Incident oder eine Reihe von Incidents generiert.

    - **Von dieser Analyseregel ausgelöste Warnungen in einem einzigen Incident zusammenfassen und gruppieren nach**: Wählen Sie die Grundlage für die Gruppierung der Warnungen aus:

        - **Gruppieren von Warnungen in einem einzigen Incident, wenn alle Entitäten übereinstimmen**: Warnungen werden gruppiert, wenn sie identische Werte für jede der zugeordneten Entitäten aufweisen (weiter oben definiert auf der Registerkarte „Regellogik festlegen“). Dies ist die empfohlene Einstellung.

        - **Gruppieren aller von dieser Regel ausgelösten Warnungen in einem einzigen Incident**: Alle von dieser Regel generierten Warnungen werden zusammengefasst, auch wenn sie keine identischen Werte haben.

        - **Gruppieren von Warnungen in einem einzigen Incident, wenn die ausgewählten Entitäten übereinstimmen**: Warnungen werden gruppiert, wenn sie identische Werte für einige der zugeordneten Entitäten (Auswahl in Dropdownliste) aufweisen. Sie sollten diese Einstellung verwenden, wenn Sie z. B. separate Incidents auf Basis der Quell- oder Ziel-IP-Adressen erstellen möchten.

    - **Geschlossene übereinstimmende Incidents erneut öffnen**: Wenn ein Incident gelöst oder geschlossen wurde und später eine weitere Warnung generiert wird, die zu diesem Incident gehören würde, haben Sie folgende Möglichkeiten: Legen Sie diese Einstellung auf **Aktiviert** fest, wenn der geschlossene Incident erneut geöffnet werden soll, oder übernehmen Sie die Einstellung **Deaktiviert**, wenn durch die Warnung ein neuer Incident erzeugt werden soll.
    
        > [!NOTE]
        > Bis zu 150 Warnungen können in einem einzelnen Incident gruppiert werden. Wenn mehr als 150 Warnungen von einer Regel generiert werden, die sie zu einem einzelnen Incident gruppiert, wird ein neuer Incident mit denselben Incidentinformationen wie der ursprüngliche Incident generiert, und die überzähligen Warnungen werden in dem neuen Incident gruppiert.

1. Wählen Sie auf der Registerkarte **Automatische Reaktionen** alle Playbooks aus, die automatisch ausgeführt werden sollen, wenn durch die benutzerdefinierte Regel eine Warnung generiert wird. Weitere Informationen zum Erstellen und Automatisieren von Playbooks finden Sie unter [Reaktionen auf Bedrohungen](tutorial-respond-threats-playbook.md).

1. Wählen Sie **Überprüfen und erstellen** aus, um alle Einstellungen für die neue Warnungsregel zu überprüfen, und wählen Sie dann **Erstellen** aus, um die Warnungsregel zu initialisieren.
  
1. Nachdem die Warnung erstellt wurde, wird der Tabelle unter **Aktive Regeln** eine benutzerdefinierte Regel hinzugefügt. Über diese Liste können Sie die einzelnen Regeln aktivieren, deaktivieren oder löschen.

1. Die Ergebnisse der von Ihnen erstellten Warnungsregeln können Sie auf der Seite **Vorfälle** anzeigen. Hier können Sie Bedrohungen eingrenzen, [Vorfälle untersuchen](tutorial-investigate-cases.md) und die Bedrohungen beseitigen.


> [!NOTE]
> In Azure Sentinel generierte Warnungen stehen über [Microsoft Graph Security](/graph/security-concept-overview) zur Verfügung. Weitere Informationen finden Sie unter [Verwenden der Sicherheits-API von Microsoft Graph](/graph/api/resources/security-api-overview).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Fehler bei der Ausführung einer geplanten Regel oder beim Hinzufügen von „AUTO DISABLED“ zum Namen

Ein Fehler bei der Ausführung einer geplanten Abfrage ist zwar selten, aber nicht ausgeschlossen. Azure Sentinel klassifiziert Fehler auf der Grundlage des spezifischen Fehlertyps und der Umstände, die dazu geführt haben, als vorübergehende oder dauerhafte Fehler.

#### <a name="transient-failure"></a>Vorübergehender Fehler

Ein vorübergehender Fehler tritt aufgrund einer temporären Situation auf, die sich bald wieder normalisiert. Zu diesem Zeitpunkt wird die Regelausführung erfolgreich angewendet. Dies sind einige Beispiele für Fehler, die von Azure Sentinel als vorübergehend klassifiziert werden:

- Die Ausführung einer Regelabfrage dauert zu lang und führt zu einem Timeout.
- Es bestehen Konnektivitätsprobleme zwischen Datenquellen und Log Analytics bzw. zwischen Log Analytics und Azure Sentinel.
- Alle anderen neuen und unbekannten Fehler werden als vorübergehende Fehler eingestuft.

Wenn ein vorübergehender Fehler auftritt, versucht Azure Sentinel weiterhin, die Regel gemäß den vordefinierten und immer kürzeren Intervallen bis zu einem bestimmten Punkt noch mal auszuführen. Danach wird die Regel nur zum nächsten geplanten Zeitpunkt wieder ausgeführt. Eine Regel wird aufgrund eines vorübergehenden Fehlers nie automatisch deaktiviert.

#### <a name="permanent-failure---rule-auto-disabled"></a>Dauerhafter Fehler: Regel automatisch deaktiviert

Ein dauerhafter Fehler tritt aufgrund einer Änderung der Bedingungen auf, die normalerweise die Ausführung der Regel ermöglichen und ohne menschliches Eingreifen nicht wiederhergestellt werden können. Im Folgenden finden Sie einige Beispiele für Fehler, die als permanente Fehler klassifiziert werden:

- Der Zielarbeitsbereich (für den die Regelabfrage ausgeführt wurde) wurde gelöscht.
- Die Zieltabelle (für die die Regelabfrage ausgeführt wurde) wurde gelöscht.
- Azure Sentinel wurde aus dem Zielarbeitsbereich entfernt.
- Eine von der Regelabfrage verwendete Funktion ist nicht mehr gültig. Sie wurde entweder geändert oder entfernt.
- Die Berechtigungen für eine der Datenquellen der Regelabfrage wurden geändert.
- Eine der Datenquellen der Regelabfrage wurde gelöscht oder getrennt.

**Im Fall einer vordefinierten Anzahl aufeinanderfolgender dauerhafter Fehler mit demselben Typ und derselben Regel** versucht Azure Sentinel nicht mehr, die Regel auszuführen und führt stattdessen die folgenden Schritte aus:

- Die Regel wird deaktiviert.
- Die Wörter **AUTO DISABLED** werden am Anfang des Regelnamens hinzugefügt.
- Der Grund für den Fehler (und die Deaktivierung) wird der Regelbeschreibung hinzugefügt.

Sie können mühelos erkennen, ob automatisch deaktivierte Regeln vorhanden sind, indem Sie die Regelliste nach Namen sortieren. Die automatisch deaktivierten Regeln werden am Anfang der Liste angezeigt.

SOC-Manager sollten sicherstellen, dass die Regelliste regelmäßig auf automatisch deaktivierte Regeln überprüft wird.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Einführung erhalten, wie Bedrohungen mithilfe von Azure Sentinel erkannt werden.

Informationen zum Automatisieren der Reaktionen auf Bedrohungen finden Sie im Tutorial [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel](tutorial-respond-threats-playbook.md).