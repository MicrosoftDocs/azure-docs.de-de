---
title: Ethik und verantwortungsvolle Verwendung – Personalisierung
titleSuffix: Azure Cognitive Services
description: Diese Richtlinien sollen Ihnen dabei helfen, die Personalisierung auf eine Weise zu implementieren, die Ihnen dabei hilft, Vertrauen in Ihr Unternehmen und Ihren Dienst aufzubauen. Achten Sie darauf, dass Sie sich Zeit nehmen, zu forschen, zu lernen und sich mit den Auswirkungen der Personalisierung auf das Leben der Benutzer auseinanderzusetzen. Lassen Sie sich im Zweifelsfall beraten.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 602da28f0c235fb0e797a493bc1160631c042a9d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005978"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Richtlinien für die verantwortungsvolle Implementierung der Personalisierung

Damit die Menschen und die Gesellschaft das volle Potenzial von KI erkennen, müssen Implementierungen so entworfen werden, dass sie das Vertrauen jener gewinnen, die ihre Anwendungen um KI erweitern, sowie der Benutzer von Anwendungen, die mit KI erstellt wurden. Diese Richtlinien sollen Ihnen dabei helfen, die Personalisierung auf eine Weise zu implementieren, die Ihnen dabei hilft, Vertrauen in Ihr Unternehmen und Ihren Dienst aufzubauen. Achten Sie darauf, dass Sie sich Zeit nehmen, zu forschen, zu lernen und sich mit den Auswirkungen der Personalisierung auf das Leben der Benutzer auseinanderzusetzen. Lassen Sie sich im Zweifelsfall beraten.

Diese Richtlinien stellen keine Rechtsberatung dar, und Sie sollten gesondert sicherstellen, dass Ihre Anwendung mit den schnellen Entwicklungen der Gesetzgebung auf diesem Gebiet und in Ihrer Branche Schritt hält.

Ferner sollten Sie beim Entwurf Ihrer Anwendung mithilfe von Personalisierung eine breite Palette von Verantwortungen berücksichtigen, die Sie bei der Entwicklung eines datenzentrierten KI-Systems übernehmen, einschließlich Ethik, Datenschutz, Schutz, Sicherheit, Inklusion, Transparenz und Verantwortlichkeit. Weitere Informationen hierzu finden Sie im Abschnitt [Empfohlene Literatur](#recommended-reading).

Sie können den folgenden Inhalt als eine anfängliche Checkliste verwenden und diese auf Ihr Szenario anpassen und dafür optimieren. Dieses Dokument besteht aus zwei Hauptabschnitten: Der erste widmet sich der Hervorhebung der Aspekte verantwortungsvoller Verwendung bei der Auswahl der Szenarien, Merkmale und Relevanzen für die Personalisierung. Der zweite behandelt einen Satz von Werten, von denen Microsoft überzeugt ist, dass sie bei der Erstellung von KI-Systemen berücksichtigt werden sollten, stellt umsetzbare Vorschläge bereit und zeigt Risiken auf, wie Ihre Verwendung von Personalisierung diese beeinflusst.


## <a name="your-responsibility"></a>Ihre Verantwortung

Alle Richtlinien für die verantwortungsvolle Implementierung basieren auf der Grundlage, dass Entwickler und Unternehmen, die Personalisierung verwenden, für die Auswirkungen der Verwendung diese Algorithmen in der Gesellschaft verantwortlich und rechenschaftspflichtig sind. Wenn Sie eine Anwendung entwickeln, die Ihre Organisation bereitstellen wird, sollten Sie Ihre Rolle und Verantwortung für deren Betrieb erkennen und wissen, wie sie sich auf die Menschen auswirkt. Wenn Sie eine Anwendung für die Bereitstellung durch einen Dritten entwerfen, kommen Sie zu einem gemeinsamen Beschluss mit ihm darüber, wer letztendlich für das Verhalten der Anwendung verantwortlich ist, und dokumentieren Sie diese Übereinkunft.

Vertrauen basiert auf der Annahme erfüllter Verpflichtungen – berücksichtigen Sie Ihre Benutzer, die Gesellschaft und den rechtlichen Rahmen, in dem Ihre Anwendungen arbeiten, um explizite und implizite Verpflichtungen zu identifizieren, denen sie möglicherweise unterliegen.

Microsoft ist ständig bestrebt, seine Tools und Dokumente zu entwickeln, um Ihnen zu helfen, diesen Verpflichtungen nachzukommen. [Stellen Sie Microsoft Feedback bereit](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D), wenn Sie glauben, dass zusätzliche Tools, Produktfunktionen und Dokumente Ihnen bei der Implementierung dieser Richtlinien für die Verwendung der Personalisierung helfen würden.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Faktoren für die verantwortungsvolle Implementierung der Personalisierung

Die Implementierung der Personalisierung kann für Ihre Benutzer und Ihr Unternehmen von großem Wert sein. Beginnen Sie mit der verantwortungsvollen Implementierung der Personalisierung, indem Sie die folgenden Richtlinien beachten bei:

* Der Auswahl von Anwendungsfällen zur Anwendung auf die Personalisierung.
* Der Erstellung von [Relevanzfunktionen](concept-rewards.md).
* Der Auswahl, welche [Merkmale](concepts-features.md) des Kontexts und mögliche Aktionen Sie für die Personalisierung verwenden möchten.


## <a name="choosing-use-cases-for-personalizer"></a>Auswählen von Anwendungsfällen für die Personalisierung

Die Verwendung eines Diensts, der lernt, Inhalte und Benutzeroberflächen zu personalisieren, ist hilfreich. Er kann aber auch falsch angewendet werden, wenn die Personalisierung negative Nebeneffekte in der realen Welt verursacht, wozu auch gehört, dass Benutzer sich nicht bewusst sind, dass Inhalte personalisiert werden.

Beispiele für die Verwendungen von Personalisierung mit erhöhtem Potenzial für negative Nebeneffekte oder fehlende Transparenz sind unter anderem Szenarien, in denen der „Nutzen“ (Relevanz) "von vielen langfristigen und komplexen Faktoren abhängig ist, die, wenn Sie zu stark vereinfacht in einen sofortigen Nutzen umgewandelt werden, zu nachteiligen Ergebnissen für Einzelpersonen führen können. Diese werden in der Regel als „Folgeentscheidungen“ bzw. Entscheidungen, die das Risiko eines Schadens mit sich bringen, angesehen. Beispiel:


* **Finanzen**: Personalisierungsangebote für Kredit-, Finanz-und Versicherungsprodukte, bei denen Risikofaktoren auf Daten basieren, die Einzelpersonen nicht kennen, nicht abrufen oder anfechten können.
* **Bildung**: Personalisierung von Rangfolgebewertungen für Schulkurse und Bildungseinrichtungen, bei denen Empfehlungen Voreingenommenheit propagieren und das Bewusstsein der Benutzer für andere Optionen verringern können.
* **Demokratie und Bürgerbeteiligung**: Personalisierung von Inhalten für Benutzer mit dem Ziel der Einflussnahme auf Meinungen hat Folgen und ist manipulativ.
* **Relevanzauswertung durch Drittanbieter**: Personalisierung von Elementen, bei denen die Relevanz auf einer späteren Auswertung des Benutzers durch Drittanbieter beruht, anstatt eine Relevanz durch das eigene Verhalten des Benutzers generieren zu lassen.
* **Intoleranz gegenüber Erkunden**: Jede Situation, in der das Erkundungsverhalten der Personalisierung Schaden verursachen kann.

Bei der Auswahl von Anwendungsfällen für die Personalisierung:

* Starten Sie den Entwurfsprozess unter Berücksichtigung der Aspekte, wie die Personalisierung Ihren Benutzern hilft.
* Berücksichtigen Sie die negativen Konsequenzen in der realen Welt, wenn wegen Mustern oder Erkundung einigen Elementen für Benutzer keine Relevanz zugewiesen wird.
* Berücksichtigen Sie, ob Ihr Anwendungsfall eine automatisierte Verarbeitung darstellt, die die betroffenen Personen erheblich betrifft, die unter der [DSGVO](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) Artikel 22 oder anderen Gesetzen geregelt ist.
* Berücksichtigen Sie Schleifen sich selbst erfüllender Prophezeiungen. Hierzu kann es kommen, wenn eine Personalisierungsrelevanz ein Modell so trainiert, dass es später eine demografische Gruppe weiter vom Zugriff auf relevante Inhalte ausschließt. Beispielsweise erhalten die meisten Menschen in einer einkommensschwachen Nachbarschaft keine Premium-Versicherungsangebote, woraufhin sich langsam die Tendenz entwickelt, dass ein solches Angebot niemandem mehr in der Nachbarschaft angezeigt wird, wenn keine ausreichende Untersuchung erfolgt.
* Speichern Sie Kopien von Modellen und Lernrichtlinien für den Fall, dass es erforderlich wird, die Personalisierung in Zukunft zu reproduzieren. Dies können Sie in regelmäßigen Abständen oder bei jeder Modellaktualisierung durchführen.
* Berücksichtigen Sie die Ebene der Erkundung, die für den Raum angemessen ist, und wie Sie diese als Tool zur Abmilderung von „Echokammer“-Effekten verwenden.


## <a name="selecting-features-for-personalizer"></a>Auswählen von Merkmalen für die Personalisierung

Personalisierung von Inhalten ist davon abhängig, dass nützliche Informationen über den Inhalt und den Benutzer vorhanden sind. Bedenken Sie dabei, dass bei einigen Anwendungen und Branchen manche Benutzermerkmale direkt oder indirekt als diskriminierend und potenziell illegal betrachtet werden können.

Berücksichtigen Sie die Effekte dieser Merkmale:

* **Demografie der Benutzer**: Merkmale hinsichtlich Geschlecht, Alter, Ethnie, Religion: Diese Merkmale können aus rechtlichen Gründen in bestimmten Anwendungen nicht zulässig sein. Außerdem kann es unethisch sein, unter diesen Aspekten zu personalisieren, weil die Personalisierung Verallgemeinerungen und Vorurteile propagieren würde. Ein Beispiel für diese Vorurteilspropagierung ist eine Stellenausschreibung für einen technischen Beruf, die älteren oder geschlechtsspezifischen Benutzergruppen nicht angezeigt wird.
* **Gebietsinformationen**: An vielen Orten der Welt können Standortinformationen (z. B. eine Postleitzahl oder Nachbarschaftsnamen) in hohem Maße mit Einkommen, Ethnie und Religion korrelieren.
* **Benutzerwahrnehmung von Fairness**: Selbst in Fällen, in denen Ihre Anwendung vernünftige Entscheidungen trifft, sollten Sie den Effekt berücksichtigen, dass Benutzer wahrnehmen, dass sich die in Ihrer Anwendung angezeigten Inhalte in einer Weise ändern, die mit Merkmalen korreliert zu sein scheint, die diskriminierend wären.
* **Unbeabsichtigte Vorurteile in Merkmalen**:       Es gibt Arten von Vorurteilen, die durch die Verwendung von Merkmalen eingeführt werden können, die sich nur auf eine Teilmenge der Population auswirken. Dies erfordert besondere Aufmerksamkeit, wenn Merkmale algorithmisch generiert werden, z. B. wenn mithilfe von Bildanalyse Elemente aus einem Bild extrahiert werden, oder mithilfe von Textanalyse Entitäten im Text ermittelt werden sollen. Machen Sie sich bewusst, welche Eigenschaften die Dienste haben, die Sie zum Erstellen dieser Merkmale verwenden.

Wenden Sie die folgenden Vorgehensweisen bei der Auswahl von Merkmalen an, um Kontexte und Aktionen an die Personalisierung zu senden:

* Berücksichtigen Sie die Rechtmäßigkeit und Ethik der Verwendung bestimmter Merkmale für einige Anwendungen und ob unschuldig aussehende Funktionen eventuell Stellvertreter für andere sein können, die Sie vermeiden möchten oder sollten.
* Seien Sie gegenüber Benutzern transparent, dass Algorithmen und Datenanalyse verwendet werden, um die ihnen angezeigten Optionen zu personalisieren.
* Stellen Sie sich folgende Fragen: Würden es meine Benutzer interessieren bzw. würden diese es begrüßen, wenn ich diese Informationen zur Personalisierung der Inhalte für sie nutze? Hätte ich ein gutes Gewissen dabei ihnen zu zeigen, wie die Entscheidung getroffen wurde, durch die bestimmte Punkte hervorgehoben oder ausgeblendet wurden?
* Verwenden Sie eher verhaltensbasierte als Klassifizierungs- oder Segmentierungsdaten, die auf anderen Merkmalen basieren. Demografische Informationen wurden traditionell von Einzelhändlern aus historischen Gründen verwendet – demografische Attribute schienen in einer vordigitalen Zeit einfach zu sammeln und umzusetzen zu sein, doch fragen Sie sich, wie relevant demografische Informationen sind, wenn Sie tatsächliche Interaktions-, Kontext- und historische Daten haben, die einen stärkeren Bezug zu den Vorlieben und der Identität der Benutzer haben.
* Berücksichtigen Sie, wie Sie verhindern können, dass Merkmale von bösartigen Benutzern gefälscht werden, was, wenn es in großem Umfang ausgenutzt würde, zu einem fehlgeleiteten Training der Personalisierung führen könnte, um bestimmte Klassen von Benutzern vorsätzlich zu stören, in Verlegenheit zu bringen und zu belästigen.
* Wenn geeignet und praktikabel, entwerfen Sie Ihre Anwendung so, dass sie es Ihren Benutzern gestattet, der Verwendung bestimmter persönlicher Merkmale zuzustimmen bzw. diese abzulehnen. Diese könnten unter Aspekten wie „Standortinformationen“, „Geräteinformationen“, „Frühere Einkäufe“ usw. gruppiert werden.


## <a name="computing-rewards-for-personalizer"></a>Berechnung von Relevanzen für die Personalisierung

Personalisierung ist bestrebt, die Auswahl der zu belohnenden (relevanten) Aktion zu verbessern, basierend auf der Grundlage der von der Geschäftslogik Ihrer Anwendung bereitgestellten Relevanzbewertung.

Eine gut aufgebaute Relevanzbewertung fungiert als fungiert als kurzfristiger Stellvertreter für ein Geschäftsziel, das an die Mission eines Unternehmens gebunden ist.

Beispielsweise veranlasst das Belohnen von Klicks dazu, dass der Personalisierungsdienst auf Kosten aller anderen Dinge nach Klicks suchen wird, selbst wenn das, worauf geklickt wird, vom Geschäftsergebnis ablenkt oder nicht daran gebunden ist.

Als gegensätzlichen Beispiel könnte eine Nachrichtensite Belohnungen an etwas sinnvolleres als Klicks binden wollen, z. B. „Hat der Benutzer ausreichend Zeit mit dem Lesen des Inhalts verbracht?“ oder „Hat der Benutzer auf relevante Artikel oder Verweise geklickt?“. Mit Personalisierung ist es einfach, Metriken eng an Belohnungen (Relevanzen) zu binden. Aber achten Sie darauf, kurzfristiges Benutzerengagement nicht mit guten Ergebnissen zu verwechseln.

### <a name="unintended-consequences-from-reward-scores"></a>Unbeabsichtigte Folgen von Relevanzbewertungen
Relevanzbewertungen können mit den besten Absichten erstellt werden, können aber dennoch unerwartete Folgen oder unbeabsichtigte Ergebnisse dabei haben, wie die Personalisierung Inhalten einen Rang zuweist.

Betrachten Sie die folgenden Beispiele:

* Das Belohnen der Personalisierung von Videoinhalten auf Grundlage des Prozentsatzes der angesehenen Länge des Videos führt wahrscheinlich zu einer Bevorzugung kürzerer Videos.
* Das Belohnen des Teilens in sozialen Netzwerken ohne eine Standpunktanalyse davon, wie das Teilen erfolgt ist oder des Inhalts selbst, kann zur Bevorzugung anstößiger, nicht moderierter oder hetzerischer Inhalte führen, die tendenziell zu einem sehr hohen „Engagement“ führen, aber nur sehr geringen Wert liefern.
* Das Belohnen von Aktionen an Benutzeroberflächenelementen, von denen der Benutzer keine Änderung erwartet, kann die Bedienfreundlichkeit und Vorhersagbarkeit der Benutzeroberfläche beeinträchtigen, wenn Schaltflächen überraschend und ohne Warnung ihren Platz oder ihre Funktion wechseln, und es somit bestimmten Gruppen erschweren, produktiv zu bleiben.

Implementieren Sie diese bewährten Methoden:

* Führen Sie Offlineexperimente mit Ihrem System durch, indem Sie verschiedene Belohnungsansätze verwenden, um Auswirkungen und Nebenwirkungen zu verstehen.
* Bewerten Sie Ihre Relevanzfunktionen, und fragen Sie sich, wie eine äußerst naive Person ihre Interpretation verbiegen und damit unerwünschte Ergebnisse erzielen würde.


## <a name="responsible-design-considerations"></a>Überlegungen zum verantwortungsvollen Design

Im Folgenden finden Sie Designbereiche für verantwortungsvolle Implementierungen von KI. Weitere Informationen zu diesem Framework finden Sie unter [The Future Computed](https://news.microsoft.com/futurecomputed/).

![KI-Werte aus „Future Computed“](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Verantwortlichkeit
*Personen, die KI-Systeme entwerfen und bereitstellen müssen dafür verantwortlich sein, wie ihre Systeme funktionieren*.

* Erstellen Sie interne Richtlinien für die Art der Implementierung von Personalisierung, dokumentieren und kommunizieren Sie diese an Ihr Team, die Führungskräfte und Lieferanten.
* Führen Sie regelmäßige Überprüfungen der Verfahren durch, wie Relevanzbewertungen berechnet werden, führen Sie Offlineauswertungen durch, um zu sehen, welche Merkmale die Personalisierung beeinflussen, und verwenden Sie die Ergebnisse, um nicht benötigte und unnötige Merkmale zu eliminieren.
* Kommunizieren Sie klar an Ihre Benutzer, wie Personalisierung verwendet wird, zu welchem Zweck und mit welchen Daten.
* Archivieren Sie Informationen und Ressourcen – z. B. Modelle, Lernrichtlinien und andere Daten, die die Personalisierung für ihre Funktion verwendet, um Ergebnisse reproduzieren zu können.

### <a name="transparency"></a>Transparenz
*KI-Systeme sollten verständlich sein*. Mit Personalisierung:

* *Geben Sie Benutzern Informationen darüber, wie der Inhalt personalisiert wurde.* Beispielsweise können Sie Ihren Benutzern eine Schaltfläche mit der Bezeichnung `Why These Suggestions?` (Warum diese Vorschläge?) anzeigen, über die angezeigt wird, welche Top-Merkmale des Benutzers und welche Aktionen eine Rolle bei den Ergebnissen der Personalisierung gespielt haben.
* Stellen Sie sicher, dass in Ihren Nutzungsbedingungen erwähnt wird, dass Sie Informationen über Benutzer und ihr Verhalten zum Personalisieren der Erfahrung verwenden.

### <a name="fairness"></a>Fairness
*KI-Systeme sollten alle Personen fair, also gleich behandeln.*

* Verwenden Sie Personalisierung nicht für Anwendungsfälle, in denen die Ergebnisse langfristig sind, Folgen haben oder tatsächliche Schäden beinhalten.
* Verwenden Sie keine Merkmale, die nicht für die Personalisierung von Inhalten geeignet sind oder die die Propagierung unerwünschter Vorurteile unterstützen. Beispielsweise sollten jedem Benutzer mit ähnlicher Finanzlage dieselben personalisierten Empfehlungen für Finanzprodukte angezeigt werden.
* Verstehen Sie Vorurteile, die inhärent in Merkmalen vorhanden sein können, die aus Editoren, algorithmischen Tools oder Benutzern selbst gewonnen werden.

### <a name="reliability-and-safety"></a>Zuverlässigkeit und Sicherheit
*KI-Systeme sollten zuverlässig und sicher funktionieren*. Für Personalisierung:

* *Stellen Sie der Personalisierung keine Aktionen bereit, die nicht ausgewählt werden sollten*. Beispielsweise sollten unangemessene Filme aus den zu personalisierenden Aktionen herausgefiltert werden, wenn eine Empfehlung für einen anonymen oder minderjährigen Benutzer ausgesprochen wird.
* *Verwalten Sie Ihr Personalisierungsmodell als Geschäftsressource*.  Überlegen Sie, wie häufig Sie das Ihrer Personalisierungsschleife zugrunde liegende Modell und die Lernrichtlinien speichern und sichern, und behandeln Sie es andernfalls als eine wichtige Geschäftsressource. Das Reproduzieren vergangener Ergebnisse ist wichtig, um sich selbst zu überwachen und Verbesserungen zu messen.
* *Stellen Sie Kanäle bereit, um direktes Feedback von Benutzern zu erhalten*. Zusätzlich zum Programmieren von Sicherheitsüberprüfungen, um zu gewährleisten, dass nur die gewünschten Benutzergruppen die richtigen Inhalte sehen, stellen Sie einen Feedbackmechanismus bereit, über den Benutzer Inhalte melden können, die eventuell überraschend oder verstörend sind. Insbesondere wenn Ihre Inhalte von Benutzern oder Drittanbietern stammen, sollten Sie die Verwendung von Microsoft Content Moderator oder zusätzlichen Tools zur Überprüfung und Validierung von Inhalten in Betracht ziehen.
* *Führen Sie häufige Offlineauswertungen durch*. Dies hilft Ihnen beim Überwachen von Trends und stellt sicher, dass Sie die Effektivität kennen.
* *Richten Sie einen Prozess ein, um bösartige Manipulationen zu erkennen und darauf zu reagieren*. Es gibt Akteure, die die Fähigkeit des maschinellen Lernens und von KI-Systemen zum Lernen aus ihren Umgebungen ausnutzen, um die Ergebnisse zu Ihren Gunsten zu verschieben. Wenn Ihre Verwendung der Personalisierung wichtige Entscheidungen beeinflussen kann, stellen Sie sicher, dass Sie über geeignete Mittel verfügen, um diese Angriffsarten zu erkennen und zu beseitigen, einschließlich der Überprüfung durch Menschen unter den entsprechenden Umständen.

### <a name="security-and-privacy"></a>Sicherheit und Datenschutz
*KI-Systeme sollten sicher sein und den Datenschutz respektieren*. Bei Verwendung der Personalisierung:

* *Informieren Sie Benutzer im Voraus darüber, welche Daten gesammelt und wie diese verwendet werden, und besorgen Sie sich deren Zustimmung im Voraus* unter Einhaltung Ihrer lokalen und branchenspezifischen Vorschriften.
* *Stellen Sie Benutzersteuerelemente zum Schutz der Privatsphäre bereit.* Ziehen Sie für Anwendungen, die persönliche Informationen speichern, in Betracht, eine einfach aufzufindenden Schaltfläche für Funktionen bereitzustellen wie:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

In einigen Fällen können diese gesetzlich vorgeschrieben sein. Berücksichtigen Sie die Abwägungen beim regelmäßigen Neutraining von Modellen, damit diese keine Spuren von gelöschten Daten enthalten.

### <a name="inclusiveness"></a>Inklusion
*Gehen Sie auf eine breite Palette menschlicher Bedürfnisse und Erfahrungen ein*.
* *Stellen Sie personalisierte Erfahrungen für barrierefreie Benutzeroberflächen bereit.* Die Effizienz, die sich aus einer guten Personalisierung ergibt – angewendet, um den Aufwand, die Menge an Bewegungen und unnötigen Wiederholungen bei Interaktionen zu reduzieren – kann besonders für Menschen mit Behinderungen von Vorteil sein.
* *Passen Sie das Anwendungsverhalten an den Kontext an*. Sie können die Personalisierung verwenden, um zwischen den Absichten in einem Chatbot zu unterscheiden, z. B. weil die richtige Interpretation kontextabhängig sein kann, und es nicht für alles eine einheitliche Lösung gibt.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktive Bereitschaft für erhöhten Datenschutz und Data Governance

Es ist schwierig, spezifische Änderungen in regulatorischen Kontexten vorherzusagen, aber im Allgemeinen wäre es ratsam, über den minimalen rechtlichen Rahmen hinauszugehen, um einen respektvollen Umgang mit persönlichen Daten zu gewährleisten und Transparenz und Wahlmöglichkeiten im Zusammenhang mit der algorithmischen Entscheidungsfindung zu schaffen.


* Erwägen Sie, für eine Situation im Voraus zu planen, in der es neue Beschränkungen für von Einzelpersonen gesammelte Daten geben kann, und wo die Notwendigkeit besteht, darzustellen, wie diese für die Entscheidungsfindung verwendet wurden.
* Berücksichtigen Sie dort zusätzliche Bereitschaft, wo marginalisierte, verletzliche Populationen zu Ihren Benutzern gehören können, wie Kinder, Benutzer in wirtschaftlich prekären Verhältnissen oder Benutzer, die anderweitig für die Beeinflussung durch algorithmische Manipulationen anfällig sind.
* Berücksichtigen Sie die weit verbreitete Unzufriedenheit mit der Art und Weise, in der sich Programme und Algorithmen zur Datenerfassung zum Zwecke der Zielgruppenadressierung und -beeinflussung entwickelt haben, und wie Sie bekannte strategische Fehler vermeiden können.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktive Bewertungen während Ihres Projektlebenszyklus

Erwägen Sie, Methoden für Teammitglieder, Benutzer und Geschäftsinhaber zu erstellen, über die Bedenken hinsichtlich der verantwortungsvollen Verwendung gemeldet werden können, und einen Prozess zu erstellen, der deren Lösung priorisiert und Vergeltungsmaßnahmen verhindert.

Jede Person, die sich Gedanken zu Nebeneffekten der Verwendung jeglicher Technologie macht, ist durch die eigene Perspektive und Lebenserfahrung eingeschränkt. Erweitern Sie den Bereich der verfügbaren Meinungen, indem Sie vielfältigere Stimmen in Ihre Teams und bei den Benutzern oder Beratungsgremien einbringen, damit diese frei sprechen können und dazu ermutigt werden. Berücksichtigen Sie Schulungs- und Lernmaterialien, um das Teamwissen in dieser Domäne noch zu erweitern und Fähigkeiten zur Diskussion komplexer und sensibler Themen auszubauen.

Erwägen Sie, Aufgaben zur verantwortungsvollen Verwendung wie alle anderen bereichsübergreifenden Aufgaben im Anwendungslebenszyklus zu behandeln, etwa Aufgaben im Zusammenhang mit der Benutzererfahrung, Sicherheit oder DevOps. Diese Aufgaben und ihre Anforderungen dürfen nicht nur als Nebensache betrachtet werden. Die verantwortungsvolle Verwendung sollte während des gesamten Lebenszyklus der Anwendung diskutiert und überprüft werden.

## <a name="questions-and-feedback"></a>Fragen und Feedback

Microsoft ist ständig bestrebt, Tools und Dokumente zu entwickeln, um Ihnen zu helfen, diesen Verpflichtungen nachzukommen. Unser Team lädt Sie dazu ein, [Microsoft Feedback bereitzustellen](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D), wenn Sie glauben, dass zusätzliche Tools, Produktfunktionen und Dokumente Ihnen bei der Implementierung dieser Richtlinien für die Verwendung der Personalisierung helfen würden.

## <a name="recommended-reading"></a>Empfohlene Literatur

* Informationen finden Sie in den sechs Prinzipien von Microsoft zur verantwortungsvollen Entwicklung von KI, die im Januar 2018 in dem Buch [The Future Computed](https://news.microsoft.com/futurecomputed/) veröffentlicht wurden.
* [Who Owns the Future?](https://www.goodreads.com/book/show/15802693-who-owns-the-future), von Jaron Lanier.
* [Weapons of Math Destruction](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction), von Cathy O'Neil
* [Ethics and Data Science](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/)von Patil, Hilary Mason, Mike Loukides.
* [ACM Code of Ethics](https://www.acm.org/code-of-ethics)
* [Genetic Information Nondiscrimination Act – GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [FATML Principles for Accountable Algorithms](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Nächste Schritte

[Merkmale: Aktion und Kontext](concepts-features.md).
