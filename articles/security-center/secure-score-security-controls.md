---
title: Secure Score in Azure Security Center
description: Beschreibung des Secure Scores von Azure Security Center und seiner Sicherheitskontrollen
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 24822777b06fadf87ca446d9b7ff8ba4df34adc5
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007674"
---
# <a name="secure-score-in-azure-security-center"></a>Secure Score in Azure Security Center

## <a name="introduction-to-secure-score"></a>Einführung in Secure Score

Azure Security Center hat zwei Hauptziele: 

- Ihre aktuelle Sicherheitslage besser verstehen
- Ihre Sicherheit effizient und effektiv verbessern

Der zentrale Aspekt von Security Center, mit dem Sie diese Ziele erreichen können, ist die **Sicherheitsbewertung**.

Security Center führt eine ständige Bewertung Ihrer Ressourcen, Abonnements und Organisation in Bezug auf Sicherheitsprobleme durch. Anschließend werden alle Ergebnisse in einer einzigen Bewertung zusammengefasst, sodass Sie auf einen Blick Ihre aktuelle Sicherheitssituation erkennen können: je höher die Bewertung, desto geringer das ermittelte Risiko.

Die Sicherheitsbewertung wird auf den Seiten des Azure-Portals als Prozentwert angezeigt, aber auch die zugrunde liegenden Werte sind übersichtlich dargestellt:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Sicherheitsbewertung insgesamt, wie im Portal angezeigt":::

Um Ihre Sicherheit zu verbessern, sehen Sie auf der Seite „Empfehlungen“ von Security Center nach, welche Maßnahmen erforderlich sind, um Ihre Bewertung zu steigern. Jede Empfehlung enthält Anweisungen, die Ihnen helfen sollen, das spezifische Problem zu beheben.

Die Empfehlungen sind nach **Sicherheitskontrollen** gruppiert. Jede Sicherheitskontrolle ist eine logische Gruppe verwandter Sicherheitsempfehlungen, die anfällige Angriffsflächen widerspiegelt. Ihre Bewertung verbessert sich nur, wenn Sie *alle* Empfehlungen für eine einzelne Ressource innerhalb einer Sicherheitskontrolle umsetzen. Um zu sehen, wie gut jede einzelne Angriffsfläche in Ihrer Organisation geschützt ist, sehen Sie sich die Bewertungen für die einzelnen Sicherheitskontrollen an.

Weitere Informationen finden Sie unter [Berechnen Ihrer Sicherheitsbewertung](secure-score-security-controls.md#how-your-secure-score-is-calculated). 


## <a name="access-your-secure-score"></a>Zugriff auf Ihre Sicherheitsbewertung

Ihre Sicherheitsbewertung insgesamt sowie Ihre Bewertung pro Abonnement können Sie im Azure-Portal finden oder entsprechend den Angaben in den folgenden Abschnitten programmgesteuert abrufen:

- [Abrufen der Sicherheitsbewertung aus dem Portal](#get-your-secure-score-from-the-portal)
- [Abrufen der Sicherheitsbewertung über die REST-API](#get-your-secure-score-from-the-rest-api)
- [Abrufen der Sicherheitsbewertung aus Azure Resource Graph](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Abrufen ihrer Sicherheitsbewertung aus dem Portal

Von Security Center wird Ihre Bewertung im Portal auf der Übersichtsseite als erste Hauptkachel gut sichtbar angezeigt. Wenn Sie diese Kachel auswählen, gelangen Sie zur dedizierten Seite mit der Sicherheitsbewertung, auf der Sie die Bewertung nach Abonnement aufgeschlüsselt sehen. Wählen Sie ein einzelnes Abonnement aus, um die detaillierte Liste mit priorisierten Empfehlungen und die möglichen Auswirkungen anzuzeigen, die eine Umsetzung dieser Empfehlungen auf die Bewertung des Abonnements hat. 

Zur Wiederholung: Ihre Sicherheitsbewertung wird auf Seiten des Security Center-Portals an den folgenden Stellen gezeigt.

- Auf einer Kachel der **Übersicht** in Security Center (Hauptdashboard):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Die Sicherheitsbewertung auf dem Security Center-Dashboard":::

- Auf der Seite mit der dedizierten **Sicherheitsbewertung** können Sie die Sicherheitsbewertung für Ihr Abonnement und Ihre Verwaltungsgruppen sehen:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Sicherheitsbewertung für Abonnements auf der Security Center-Seite „Sicherheitsbewertung“":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Sicherheitsbewertung für Verwaltungsgruppen auf der Security Center-Seite „Sicherheitsbewertung“":::

    > [!NOTE]
    > Für alle Verwaltungsgruppen, für die Sie nicht über ausreichende Berechtigungen verfügen, wird als Bewertung „Eingeschränkt“ angezeigt. 

- Oben auf der Seite **Empfehlungen**:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Die Sicherheitsbewertung auf der Security Center-Seite „Empfehlungen“":::

### <a name="get-your-secure-score-from-the-rest-api"></a>Abrufen ihrer Sicherheitsbewertung über die REST-API

Sie können auf Ihre Bewertung über die Sicherheitsbewertungs-API zugreifen. Die API-Methoden bieten die Flexibilität, die Daten abzufragen und im Laufe der Zeit einen eigenen Berichtsmechanismus für Ihre Sicherheitsbewertungen zu erstellen. Beispielsweise können Sie die API [Sicherheitsbewertungen](/rest/api/securitycenter/securescores) verwenden, um die Bewertung für ein bestimmtes Abonnement zu erhalten. Darüber hinaus können Sie die API [Sicherheitsbewertungs-Steuerelemente](/rest/api/securitycenter/securescorecontrols) verwenden, um die Sicherheitssteuerelemente und die aktuelle Bewertung Ihrer Abonnements aufzulisten.

![Abrufen einer einzelnen Sicherheitsbewertung über die API](media/secure-score-security-controls/single-secure-score-via-api.png)

Beispiele für Tools, die auf der Sicherheitsbewertungs-API basieren, finden Sie im [Bereich zu den Sicherheitsbewertungen in unserer GitHub-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Abrufen der Sicherheitsbewertung aus Azure Resource Graph

Azure Resource Graph bietet mit zuverlässigen Funktionen zum Filtern, Gruppieren und Sortieren sofortigen Zugriff auf Ressourceninformationen in Ihren Cloudumgebungen. Es ist eine schnelle und effiziente Möglichkeit, Informationen über Azure-Abonnements programmgesteuert oder aus dem Azure-Portal heraus abzufragen. [Erfahren Sie mehr über Azure Resource Graph](../governance/resource-graph/index.yml).

So greifen Sie mithilfe von Azure Resource Graph auf Sicherheitsbewertungen für mehrere Abonnements zu:

1. Öffnen Sie im Azure-Portal **Azure Resource Graph-Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Die Empfehlungsseite des Azure Resource Graph-Explorer** wird gestartet." :::

1. Geben Sie Ihre Kusto-Abfrage ein (in den nachstehenden Beispielen finden Sie Anleitungen).

    - Diese Abfrage gibt die Abonnement-ID, die aktuelle Bewertung in Punkten und als Prozentsatz sowie die maximale Bewertung des Abonnements zurück. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Diese Abfrage gibt den Status aller Sicherheitskontrollen zurück. Für jede Sicherheitskontrolle erhalten Sie die Anzahl fehlerhafter Ressourcen sowie die aktuelle und maximale Bewertung. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Wählen Sie **Abfrage ausführen** aus.




## <a name="tracking-your-secure-score-over-time"></a>Nachverfolgen Ihrer Sicherheitsbewertung im zeitlichen Verlauf

Wenn Sie ein Power BI-Benutzer sind und über ein Pro-Konto verfügen, können Sie das Power BI-Dashboard **Sicherheitsbewertung im zeitlichen Verlauf** verwenden, um Ihre Sicherheitsbewertung nachzuverfolgen und Änderungen zu untersuchen.

> [!TIP]
> Sie finden dieses Dashboard sowie andere Tools für die programmgesteuerte Arbeit mit Sicherheitsbewertungen im dedizierten Bereich der Azure Security Center-Community auf GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Das Dashboard enthält die folgenden zwei Berichte, anhand derer Sie Ihren Sicherheitsstatus analysieren können:

- **Zusammenfassung der Ressourcen**: Dieser Bericht enthält zusammengefasste Daten zur Integrität Ihrer Ressourcen.
- **Zusammenfassung der Sicherheitsbewertung**: Dieser Bericht enthält zusammengefasste Daten zu Ihrem Bewertungsfortschritt. Verwenden Sie das Diagramm „Sicherheitsbewertung im zeitlichen Verlauf pro Abonnement“, um Änderungen in der Bewertung anzuzeigen. Wenn Sie eine drastische Änderung in der Bewertung feststellen, überprüfen Sie die Tabelle „Erkannte Änderungen, die sich auf Ihre Sicherheitsbewertung auswirken können“ auf mögliche Änderungen, die dazu geführt haben könnten. In dieser Tabelle werden gelöschte Ressourcen, neu bereitgestellte Ressourcen oder Ressourcen dargestellt, deren Sicherheitsstatus aufgrund einer der Empfehlungen geändert wurde.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="Das optionale Power BI-Dashboard für die Sicherheitsbewertung im zeitlichen Verlauf zum Nachverfolgen Ihrer Sicherheitsbewertung und zum Untersuchen von Änderungen":::





## <a name="how-your-secure-score-is-calculated"></a>Berechnen Ihrer Sicherheitsbewertung 

Der jeweilige Beitrag der einzelnen Sicherheitskontrollen zur gesamten Sicherheitsbewertung ist auf der Seite mit Empfehlungen eindeutig angegeben.

[![Mit dem erweiterten Secure Score (Vorschauversion) werden Sicherheitskontrollen eingeführt](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Um alle zu erzielenden Punkte für eine Sicherheitskontrolle zu erhalten, müssen alle Ihre Ressourcen allen Sicherheitsempfehlungen innerhalb der Sicherheitskontrolle entsprechen. Beispielsweise enthält Security Center mehrere Empfehlungen zum Schützen Ihrer Verwaltungsports. Sie müssen alle Empfehlungen umsetzen, um eine Änderung der Sicherheitsbewertung zu erzielen.

Beispielsweise wird die Sicherheitskontrolle „Systemupdates anwenden“ mit maximal sechs Punkten bewertet. Dieser Wert ist in der QuickInfo zur potenziellen Steigerung der Sicherheitskontrolle angegeben:

[![Sicherheitskontrolle „Systemupdates anwenden“](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Der maximal mögliche Wert für das Steuerelement „Systemupdates anwenden“ ist immer 6. In diesem Beispiel gibt es 50 Ressourcen. Der maximale Wert wird daher durch 50 geteilt, woraus sich ergibt, dass jede Ressource 0,12 Punkte beiträgt. 

* **Potenzielle Steigerung** (0,12 x 8 fehlerhafte Ressourcen = 0,96): Die Anzahl der noch verfügbaren Punkte des Steuerelements. Wenn Sie alle Empfehlungen für dieses Steuerelement umsetzen, steigt die Bewertung um 2 % (in diesem Fall um 0,96 Punkte, die auf 1 Punkt aufgerundet werden). 
* **Aktuelle Bewertung** (0,12 x 42 fehlerfreie Ressourcen = 5,04): Die aktuelle Bewertung für dieses Steuerelement. Jedes Kontrollelement trägt zur Gesamtbewertung bei. In diesem Beispiel trägt das Steuerelement 5,04 Punkte zur aktuellen Gesamtbewertung bei.
* **Maximale Bewertung**: Die maximale Anzahl von Punkten, die Sie durch Erfüllen aller Empfehlungen innerhalb eines Kontrollelements erhalten können. Die maximale Bewertung für ein Kontrollelement gibt die relative Bedeutung dieses Kontrollelements an. Verwenden Sie die maximale Bewertung, um die Probleme zu selektieren, die zuerst behandelt werden müssen. 


### <a name="calculations---understanding-your-score"></a>Berechnungen – Verstehen der Bewertung

|Metrik|Formel und Beispiel|
|-|-|
|**Aktuelle Bewertung der Sicherheitskontrolle**|<br>![Gleichung zum Berechnen der Bewertung einer Sicherheitskontrolle](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Jede einzelne Sicherheitskontrolle trägt zur Sicherheitsbewertung bei. Jede Ressource, die von einer Empfehlung innerhalb des Kontrollelements betroffen ist, trägt zur aktuellen Bewertung des Kontrollelements bei. Die aktuelle Bewertung für jedes Kontrollelement ist ein Maß für den Status der Ressourcen *innerhalb* des Kontrollelements.<br>![QuickInfos mit den Werten, die beim Berechnen der aktuellen Bewertung der Sicherheitskontrolle verwendet werden](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In diesem Beispiel wird die maximale Bewertung von 6 durch 78 dividiert, da dies die Summe aus fehlerfreien und fehlerhaften Ressourcen ist.<br>6 / 78 = 0,0769<br>Die Multiplikation dieses Ergebnisses mit der Anzahl der fehlerfreien Ressourcen (4) ergibt die aktuelle Bewertung:<br>0,0769 * 4 = **0,31**<br><br>|
|**Sicherheitsbewertung**<br>Ein Abonnement|<br>![Gleichung zum Berechnen der Sicherheitsbewertung eines Abonnements](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Sicherheitsbewertung eines einzelnen Abonnements, wenn alle Kontrollen aktiviert sind](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In diesem Beispiel gibt es ein einzelnes Abonnement, für das alle Sicherheitskontrollen verfügbar sind (potenzielle Höchstbewertung sind 60 Punkte). Die Bewertung gibt 28 Punkte der möglichen 60 Punkte an. Die verbleibenden 32 Punkte sind in den Zahlen für die potenzielle Bewertungssteigerung der Sicherheitskontrollen enthalten.<br>![Liste der Kontrollen und potenziellen Bewertungssteigerung](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Sicherheitsbewertung**<br>Mehrere Abonnements|<br>![Gleichung zum Berechnen der Sicherheitsbewertung für mehrere Abonnements](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Wenn Sie die kombinierte Bewertung für mehrere Abonnements berechnen, enthält Security Center eine *Gewichtung* für jedes Abonnement. Die relativen Gewichtungen für Ihre Abonnements werden von Security Center basierend auf Faktoren wie der Anzahl der Ressourcen bestimmt.<br>Die aktuelle Bewertung für die einzelnen Abonnements wird auf die gleiche Weise berechnet wie für ein einziges Abonnement, aber dann wird die Gewichtung wie in der Gleichung dargestellt angewendet.<br>Beim Anzeigen mehrerer Abonnements werden von Secure Score alle Ressourcen in allen aktivierten Richtlinien ausgewertet und nach deren kombinierter Auswirkung auf die Höchstbewertung der einzelnen Sicherheitskontrollen gruppiert.<br>![Sicherheitsbewertung für mehrere Abonnements, wenn alle Sicherheitskontrollen aktiviert sind](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Die kombinierte Bewertung ist **kein** Durchschnittswert, sondern vielmehr die Auswertung des Status aller Ressourcen in allen Abonnements.<br>Wenn Sie die Seite mit den Empfehlungen aufrufen und die potenziell verfügbaren Punkte addieren, werden Sie feststellen, dass es sich hierbei um die Differenz zwischen der aktuellen Bewertung (24) und der verfügbaren Höchstbewertung (60) handelt.|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Welche Empfehlungen sind in den Berechnungen zur Sicherheitsbewertung enthalten?

Nur integrierte Empfehlungen haben eine Auswirkung auf die Sicherheitsbewertung.

Empfehlungen, die als **Vorschau** gekennzeichnet sind, werden nicht in die Berechnungen Ihrer Sicherheitsbewertung einbezogen. Sie sollten nach Möglichkeit weiterhin korrigiert werden, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen.

Beispiel für eine Vorschauempfehlung:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Empfehlung mit dem Flag „Vorschau“":::

## <a name="improve-your-secure-score"></a>Verbessern Ihrer Sicherheitsbewertung

Zum Verbessern Ihrer Sicherheitsbewertung setzen Sie die Sicherheitsempfehlungen in Ihrer Empfehlungsliste um. Sie können jede Empfehlung manuell für die einzelnen Ressourcen umsetzen oder die Option **Schnelle Problembehebung** verwenden (falls verfügbar), um eine Empfehlung für eine Gruppe von Ressourcen schnell anzuwenden. Weitere Informationen finden Sie unter [Umsetzen von Empfehlungen](security-center-remediate-recommendations.md).

Eine andere Möglichkeit, Ihre Bewertung zu verbessern und sicherzustellen, dass Ihre Benutzer keine Ressourcen erstellen, die sich negativ auf Ihre Bewertung auswirken, besteht darin, die Erzwingungs- und Ablehnungsoptionen für die entsprechenden Empfehlungen zu konfigurieren. Weitere Informationen finden Sie unter [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Sicherheitskontrollen und deren Empfehlungen

In der folgenden Tabelle sind die Sicherheitskontrollen in Azure Security Center aufgelistet. Für jede Sicherheitskontrolle ist die maximale Anzahl von Punkten angegeben, die Ihrer Sicherheitsbewertung hinzugefügt wird, wenn Sie *alle* in der Sicherheitskontrolle aufgeführten Empfehlungen für *alle* Ihre Ressourcen umsetzen. 

Die Sicherheitsempfehlungen, die mit Security Center bereitgestellt werden, sind auf die verfügbaren Ressourcen in der Umgebung der jeweiligen Organisation zugeschnitten. Die Empfehlungen können durch [Deaktivieren von Richtlinien](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) und [Ausnehmen bestimmter Ressourcen von einer Empfehlung](exempt-resource.md) weiter angepasst werden. 
 
Die zugewiesenen Azure Policy-Initiativen sollten von jeder Organisation sorgfältig geprüft werden. 

> [!TIP]
> Ausführliche Informationen zur Überprüfung und Bearbeitung von Initiativen finden Sie unter [Arbeiten mit Sicherheitsrichtlinien](tutorial-security-policy.md). 

Die Standardsicherheitsinitiative von Security Center basiert zwar auf branchenüblichen bewährten Methoden und Standards, es gibt jedoch Szenarien, in denen die unten aufgeführten integrierten Empfehlungen möglicherweise nicht uneingeschränkt für Ihre Organisation geeignet sind. Daher ist es manchmal erforderlich, die Standardinitiative anzupassen (ohne die Sicherheit zu beeinträchtigen). Dadurch wird sichergestellt, dass sie mit den Richtlinien Ihrer Organisation sowie mit Branchenstandards, gesetzlichen Vorgaben und Benchmarks in Einklang steht, die Sie erfüllen müssen.<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Häufig gestellte Fragen zu Secure Score

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Ändert sich meine Sicherheitsbewertung, wenn ich nur drei von vier Empfehlungen in einer Sicherheitskontrolle umsetze?
Nein. Die Bewertung ändert sich erst, wenn Sie alle Empfehlungen für eine einzelne Ressource umgesetzt haben. Um die maximale Bewertung für ein Kontrollelement zu erhalten, müssen Sie alle Empfehlungen für alle Ressourcen umsetzen.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Wenn eine Empfehlung für mich nicht anwendbar ist und ich sie in der Richtlinie deaktiviere, ist dann die Sicherheitskontrolle erfüllt, und wird meine Sicherheitsbewertung entsprechend aktualisiert?
Ja. Es wird empfohlen, Empfehlungen zu deaktivieren, wenn sie in Ihrer Umgebung nicht anwendbar sind. Anweisungen zum Deaktivieren einer bestimmten Empfehlung finden Sie unter [Deaktivieren von Sicherheitsrichtlinien](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Wenn durch eine Sicherheitskontrolle keine Punkte für meine Sicherheitsbewertung erzielt werden, soll ich diese ignorieren?
In einigen Fällen wird als maximale Bewertung für ein Kontrollelement ein Wert größer 0 (null) angezeigt, jedoch ist die Auswirkung gleich null. Wenn der inkrementelle Wert für das Korrigieren von Ressourcen unerheblich ist, wird auf null gerundet. Ignorieren Sie diese Empfehlungen aber nicht, da Sie dennoch Sicherheitsverbesserungen bieten. Die einzige Ausnahme ist das Kontrollelement „Zusätzliche bewährte Methode“. Durch das Umsetzen dieser Empfehlungen erhöht sich die Bewertung nicht, doch Sie verbessern die Gesamtsicherheit.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden Secure Score und die damit eingeführten Sicherheitskontrollen beschrieben. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md)
- [Umsetzen von Empfehlungen in Azure Security Center](security-center-remediate-recommendations.md)
- [Anzeigen der GitHub-basierten Tools für die programmgesteuerte Arbeit mit Sicherheitsbewertungen](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)