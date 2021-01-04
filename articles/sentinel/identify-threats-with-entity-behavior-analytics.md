---
title: Erkennen komplexerer Bedrohungen mit User and Entity Behavior Analytics (UEBA) in Azure Sentinel | Microsoft-Dokumentation
description: Erstellen Sie Verhaltensbaselines für Entitäten (Benutzer, Hostnamen, IP-Adressen), und verwenden Sie diese zum Erkennen von anormalem Verhalten und Zero-Day-APTs (Advanced Persistent Threats).
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
ms.date: 08/19/2020
ms.author: yelevin
ms.openlocfilehash: a0a677342534cd92d5530199076e3d2e7084134d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95494776"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Erkennen komplexerer Bedrohungen mit User and Entity Behavior Analytics (UEBA) in Azure Sentinel

> [!IMPORTANT]
>
> - Die Features UEBA und Entitätsseiten sind nun in den folgenden Azure Sentinel-Regionen **allgemein verfügbar**:
>    - in den US-amerikanischen Regionen
>    - in der Region Europa, Westen
>    - in den australischen Regionen
>
> - In allen anderen Geografien und Regionen bleiben diese Features bis auf Weiteres in der **Vorschau**. Die [**zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>Was ist User and Entity Behavior Analytics (UEBA)?

### <a name="the-concept"></a>Das Konzept

Das Erkennen von Bedrohungen in einer Organisation und deren potenziellen Auswirkungen – sei es eine kompromittierte Entität oder einen böswilligen Insider – war schon immer ein zeitaufwändiger und arbeitsintensiver Prozess. Die Durchsicht von Warnungen, das Herstellen von Zusammenhängen und das aktive Hunting summieren sich zu einem enormen Zeit- und Arbeitsaufwand mit minimalem Erfolg. Zudem besteht die Gefahr, dass komplexe Bedrohungen einfach nicht erkannt werden. Insbesondere schwer zu fassende Bedrohungen wie Zero-Day-Exploits, gezielte Bedrohungen und Advanced Persistent Threats können für eine Organisation am gefährlichsten sein, weshalb deren Erkennung umso wichtiger ist.

Dank der UEBA-Funktion in Azure Sentinel gehört die Sisyphusarbeit und der ungewisse Ausgang der Bemühungen für Ihre Analysten der Vergangenheit an. Diese Funktion liefert zuverlässige, handlungsrelevante Informationen, sodass sich Ihre Analysten auf die Untersuchung und Problembehandlung konzentrieren können.

Azure Sentinel sammelt peergruppenübergreifend und über einen bestimmten Zeitraum hinweg Protokolle und Warnungen von allen verbundenen Datenquellen, analysiert sie und erstellt Baselineprofile für das Verhalten von Entitäten (Benutzer, Hosts, IP-Adressen, Anwendungen usw.) einer Organisation. Mit verschiedenen Techniken und Machine Learning-Funktionen kann Sentinel anomale Aktivitäten erkennen und Sie dabei unterstützen, festzustellen, ob eine Ressource kompromittiert wurde. Darüber hinaus können Sie mit dieser Funktion auch die relative Vertraulichkeitsstufe bestimmter Ressource ermitteln, Ressourcenpeergruppen erkennen und die potenzielle Auswirkung einer bestimmten kompromittierten Ressource (deren „Auswirkungsgrad“) bewerten. Mit diesen Informationen können Sie Ihre Untersuchungen und die Behandlung von Vorfällen effektiv priorisieren. 

### <a name="architecture-overview"></a>Übersicht über die Architektur

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Architektur von User and Entity Behavior Analytics":::

### <a name="security-driven-analytics"></a>Sicherheitsgesteuerte Analyse

Angeregt durch das Paradigma von Gartner für UEBA-Lösungen wird bei Azure Sentinel auf ein „Von außen nach innen“-Konzept gesetzt, das auf den folgenden drei Bezugsrahmen basiert:

- **Anwendungsfälle:** Azure Sentinel priorisiert relevante Angriffsvektoren und Szenarios, die auf Sicherheitsuntersuchungen basieren, die sich am MITRE ATT&CK-Framework für Taktiken, Techniken und Subtechniken orientieren, bei denen verschiedene Entitäten als Opfer, Täter oder Zentren in einer Kill Chain gelten. Somit konzentriert sich Azure Sentinel speziell auf die wertvollsten Protokolle, die eine Datenquelle liefern kann.

- **Datenquellen:** Azure Sentinel unterstützt in erster Linie Azure-Datenquellen. Datenquellen von Drittanbietern, die unseren Bedrohungsszenarios entsprechen, werden sehr sorgfältig ausgewählt.

- **Analysen:** Mithilfe von verschiedenen ML-Algorithmen (Machine Learning) erkennt Azure Sentinel anomale Aktivitäten und zeigt die Beweise klar und präzise in Form von kontextbezogenen Anreicherungen an. Einige Beispiele hierfür sind im Folgenden dargestellt.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Von-außen-nach-innen-Konzept für Verhaltensanalysen":::

Azure Sentinel stellt Artefakte dar, mit deren Hilfe Ihre Sicherheitsanalysten klare Vorstellungen von anomalen Aktivitäten im Kontext und im Vergleich zum Baselineprofil des jeweiligen Benutzers gewinnen. Von einem Benutzer (oder einem Host oder einer Adresse) durchgeführte Aktionen werden kontextbezogen bewertet, wobei das Ergebnis „true“ eine erkannte Anomalie angibt:
- bei verschiedenen geografischen Standorten, Geräten und Umgebungen
- in verschiedenen Zeiträumen und mit unterschiedlicher Häufigkeit (im Vergleich zum Verlauf des Benutzer)
- im Vergleich zum Verhalten von Peers
- im Vergleich zum Verhalten der Organisation

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Entitätskontext":::


### <a name="scoring"></a>Bewertung

Jede Aktivität wird basierend auf dem Verhaltenslernen der Benutzer und der Peers mit einem „Score für die Untersuchungspriorität“ bewertet, mit dem die Wahrscheinlichkeit angegeben wird, mit der ein bestimmter Benutzer eine bestimmte Aktivität durchführt. Aktivitäten mit besonders großer Anomalie werden mit dem höchsten Score (auf einer Skala von 0 bis 10) bewertet.

Ein Beispiel für die Verwendung von Verhaltensanalysen und deren Funktionsweise finden Sie im Artikel über [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136).

## <a name="entities-in-azure-sentinel"></a>Entitäten in Azure Sentinel

### <a name="entity-identifiers"></a>Entitätsbezeichner

Wenn Warnungen an Azure Sentinel gesendet werden, enthalten sie Datenelemente, die Azure Sentinel identifiziert und als Entitäten klassifiziert, z. B. Benutzerkonten, Hosts, IP-Adressen und andere. Gelegentlich kann diese Identifizierung eine Herausforderung darstellen, wenn die Warnung keine ausreichenden Informationen über die Entität enthält.

Benutzerkonten können z. B. auf mehr als eine Weise identifiziert werden: mithilfe des numerischen Bezeichners (GUID) eines Azure AD-Kontos oder des Werts des Benutzerprinzipalnamens (UPN) bzw. alternativ mithilfe einer Kombination aus seinem Benutzernamen und seinem NT-Domänennamen. Verschiedene Datenquellen können denselben Benutzer auf unterschiedliche Weise identifizieren. Deshalb fasst Azure Sentinel, wann immer möglich, diese Bezeichner zu einer einzelnen Entität zusammen, sodass diese ordnungsgemäß identifiziert werden kann.

Es kann jedoch vorkommen, dass einer Ihrer Ressourcenanbieter eine Warnung erstellt, in der eine Entität nicht ausreichend identifiziert wird, z. B. ein Benutzername ohne den Kontext des Domänennamens. In einem solchen Fall kann die Benutzerentität nicht mit anderen Instanzen desselben Benutzerkontos zusammengeführt werden, die dann als separate Entität identifiziert würden. Diese beiden Entitäten würden dann getrennt bleiben anstatt vereinigt zu werden.

Sie sollten sich vergewissern, dass alle Ihre Anbieter von Warnungen die Entitäten in den von ihnen erstellten Warnungen richtig identifizieren, um das Risiko eines solchen Ereignisses zu minimieren. Zusätzlich kann die Synchronisierung von Benutzerkontoentitäten mit Azure Active Directory ein vereinheitlichendes Verzeichnis erstellen, das in der Lage ist, Benutzerkontoentitäten zusammenzuführen.

Die folgenden Arten von Entitäten werden derzeit in Azure Sentinel identifiziert:

- Benutzerkonto (Konto)
- Host
- IP-Adresse (IP)
- Malware
- Datei
- Prozess
- Cloudanwendung (CloudApplication)
- Domänenname (DNS)
- Azure-Ressource
- Datei (FileHash)
- Registrierungsschlüssel
- Registrierungswert
- Sicherheitsgruppe
- URL
- IoT-Gerät
- Mailbox
- E-Mail-Cluster
- E-Mail
- Übermittlungs-E-Mail

### <a name="entity-pages"></a>Entitätsseiten

Wenn Sie bei einer Suche, in einer Warnung oder bei einer Untersuchung auf eine Entität (derzeit nur Benutzer und Hosts) stoßen, können Sie die Entität auswählen und gelangen dadurch auf eine **Entitätsseite**. Hierbei handelt es sich um ein Datenblatt mit nützlichen Informationen zu dieser Entität. Zu den Informationen auf dieser Seite gehören grundlegende Fakten über die Entität, eine Zeitachse mit für diese Entität wichtigen Ereignissen sowie Erkenntnisse über das Verhalten der Entität.
 
Entitätsseiten bestehen aus drei Teilen:
- Der linke Bereich enthält Informationen zur Identifizierung der Entität, die über Datenquellen wie Azure Active Directory, Azure Monitor, Azure Security Center und Microsoft Defender erfasst werden.

- Im mittleren Bereich wird eine grafische und textbezogene Zeitachse mit für die Entität wichtigen Ereignissen wie Warnungen, Lesezeichen und Aktivitäten angezeigt. Aktivitäten sind Aggregationen von wichtigen Ereignissen von Log Analytics. Die Abfragen, mit denen diese Aktivitäten erkannt werden, werden von Microsoft-Sicherheitsexpertenteams entwickelt.

- Im rechten Bereich werden Erkenntnisse über das Verhalten der Entität angezeigt. Mit diesen Erkenntnissen können Sie Anomalien und Sicherheitsbedrohungen schnell erkennen. Die Erkenntnisse werden von Microsoft-Sicherheitsexpertenteams entwickelt und basieren auf Anomalieerkennungsmodellen.

### <a name="the-timeline"></a>Die Zeitachse

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Zeitachse auf der Entitätsseite":::

Die Zeitachse ist ein wesentlicher Bestandteil des Beitrags der Entität zur Verhaltensanalyse in Azure Sentinel. Sie stellt den Verlauf von für eine Entität wichtigen Ereignissen dar, sodass Sie sich einen Überblick über die Aktivitäten der Entität innerhalb eines bestimmten Zeitraums verschaffen können.

Sie können den **Zeitbereich** aus verschiedenen vordefinierten Optionen (z. B. *letzte 24 Stunden*) auswählen oder einen benutzerdefinierten Zeitrahmen festlegen. Zudem können Sie Filter festlegen, mit denen die Informationen auf der Zeitachse auf bestimmte Ereignis- oder Warnungsarten beschränkt wird.

Die folgenden Elemente sind auf der Zeitachse enthalten:

- Warnungen: Benachrichtigungen, in denen die Entität als **zugeordnete Entität** definiert ist. Wenn Ihre Organisation [benutzerdefinierte Warnungen mit Analyseregeln](./tutorial-detect-threats-custom.md) erstellt hat, müssen Sie darauf achten, dass die Entitätszuordnung der Regeln ordnungsgemäß vorgenommen wurde.

- Lesezeichen: Lesezeichen, die eine bestimmte Entität enthalten, die auf der Seite angezeigt wird

- Aktivitäten: Aggregation von für die Entität wichtigen Ereignissen 
 
### <a name="entity-insights"></a>Erkenntnisse über Entitäten
 
Bei Erkenntnissen über Entitäten handelt es sich um Abfragen, die von Microsoft-Sicherheitsexperten definiert werden, um Ihren Analysten eine effizientere und effektivere Untersuchung zu ermöglichen. Die Erkenntnisse werden auf der Entitätsseite angezeigt. Sie stellen wichtige Sicherheitsinformationen über Hosts und Benutzer in Form von Tabellendaten und -diagrammen bereit. Dadurch, dass die Informationen hier angezeigt werden, müssen Sie keinen Umweg über Log Analytics machen. Zu den Erkenntnissen zählen Daten zu Anmeldungen, Gruppenerweiterungen, anomalen Ereignissen und vielem mehr sowie intelligente ML-Algorithmen zur Erkennung von anormalem Verhalten. Die Erkenntnisse basieren auf den folgenden Datentypen:
- syslog
- SecurityEvent
- Überwachungsprotokolle
- Anmeldeprotokolle
- Office-Aktivitäten
- Verhaltensanalyse (UEBA) 
 
### <a name="how-to-use-entity-pages"></a>Verwenden von Entitätsseiten

Entitätsseiten sind Teil von verschiedenen Verwendungsszenarios und können über Incident Management, das Untersuchungsdiagramm, Lesezeichen oder direkt über die Entitätssuchseite unter **User and Entity Behavior Analytics** im Hauptmenü von Azure Sentinel aufgerufen werden.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Anwendungsfälle für Entitätsseiten":::


## <a name="data-schema"></a>Datenschema

### <a name="behavior-analytics-table"></a>Tabelle der Verhaltensanalyse

| Feld                     | BESCHREIBUNG                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | Eindeutige ID des Mandanten                                      |
| SourceRecordId            | Eindeutige ID des EBA-Ereignisses                                   |
| TimeGenerated             | Zeitstempel des Auftretens der Aktivität                              |
| TimeProcessed             | Zeitstempel der Verarbeitung der Aktivität durch die EBA-Engine            |
| ActivityType              | Allgemeine Kategorie der Aktivität                                 |
| ActionType                | Normalisierter Name der Aktivität                                     |
| UserName                  | Benutzername des Benutzers, der die Aktivität initiiert hat                    |
| UserPrincipalName         | Vollständiger Benutzername des Benutzers, der die Aktivität initiiert hat               |
| EventSource               | Datenquelle, die das ursprüngliche Ereignis bereitgestellt hat                        |
| SourceIPAddress           | IP-Adresse, über die die Aktivität initiiert wurde                        |
| SourceIPLocation          | Land/Region, von dem bzw. der aus die Aktivität initiiert wurde, ergänzt durch die IP-Adresse |
| SourceDevice              | Hostname des Geräts, das die Aktivität initiiert hat                  |
| DestinationIPAddress      | IP-Adresse des Ziels der Aktivität                            |
| DestinationIPLocation     | Land/Region des Ziels der Aktivität, ergänzt durch die IP-Adresse     |
| DestinationDevice         | Name des Zielgeräts                                           |
| **UsersInsights**         | Kontextabhängige Anreicherungen der beteiligten Benutzer                            |
| **DevicesInsights**       | Kontextabhängige Anreicherungen der beteiligten Geräte                          |
| **ActivityInsights**      | Kontextabhängige Analyse von Aktivitäten basierend auf unserer Profilerstellung              |
| **InvestigationPriority** | Score für Anomalie zwischen 0 und 10 (0 = harmlos, 10 = äußerst anormal)         |
|

### <a name="querying-behavior-analytics-data"></a>Abfragen von Daten der Verhaltensanalyse

Mithilfe von [KQL](/azure/data-explorer/kusto/query/) können wir die Verhaltensanalysetabelle abfragen.

Wenn wir beispielsweise alle Fälle eines Benutzers ausfindig machen möchten, in denen bei der Anmeldung bei einer Azure-Ressource ein Fehler aufgetreten ist und der Benutzer zum ersten Mal versucht hat, eine Verbindung über ein bestimmtes Land herzustellen und Verbindungen aus diesem Land selbst für die Peers des Benutzers ungewöhnlich sind, können wir folgende Abfrage verwenden:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Metadaten der Benutzerpeers – Tabelle und Notebook

Die Metadaten der Benutzerpeers liefern wichtigen Kontext bei der Erkennung von Bedrohungen, bei der Untersuchung von Vorfällen sowie bei der Suche nach einer potenziellen Bedrohung. Sicherheitsanalysten können die normalen Aktivitäten der Peers eines Benutzers beobachten, um festzustellen, ob die Aktivitäten des Benutzers im Vergleich zu denen seiner Peers ungewöhnlich sind.

Azure Sentinel berechnet und bewertet die Peers eines Benutzers basierend auf der Azure AD-Sicherheitsgruppenmitgliedschaft des Benutzers, anhand von Mailinglisten usw. und speichert die Peers mit einer Bewertung zwischen 1 und 20 in der Tabelle **UserPeerAnalytics**. Im folgenden Screenshot ist das Schema der Tabelle „UserPeerAnalytics“ mit den acht ersten Peers des Benutzers Kendall Collins dargestellt. Azure Sentinel verwendet den TF-IDF-Algorithmus (*Term Frequency-Inverse Document Frequency*) zur Normalisierung der Gewichtung bei der Berechnung der Bewertung: je kleiner die Gruppe, umso höher die Gewichtung. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Screenshot: Tabelle mit Metadaten von Peers eines Benutzers":::

Zur Visualisierung der Metadaten der Peers des Benutzers können Sie [Jupyter Notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) verwenden, das im Azure Sentinel GitHub-Repository bereitgestellt wird. Eine ausführliche Anleitung zur Verwendung von Notebook finden Sie im Notebook mit [geführten Analysen zu Metadaten zur Benutzersicherheit](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb).

### <a name="permission-analytics---table-and-notebook"></a>Berechtigungsanalysen – Tabelle und Notebook

Mit der Berechtigungsanalyse können Sie die potenziellen Auswirkungen der Kompromittierung einer Ressource in einer Organisation durch einen Angreifer ermitteln. Diese Auswirkung wird auch als „Auswirkungsgrad“ der Ressource bezeichnet. Sicherheitsanalysten können mit diesen Informationen die Untersuchung und Behandlung von Vorfällen priorisieren.

Azure Sentinel bestimmt die direkten und transitiven Zugriffsrechte eines bestimmten Benutzers für Azure-Ressourcen durch die Auswertung der Azure-Abonnements, auf die der Benutzer direkt ober über Gruppen oder Dienstprinzipale zugreifen kann. Diese Informationen sowie eine umfassende Liste mit der Azure AD-Sicherheitsgruppenmitgliedschaft des Benutzers werden in der Tabelle **UserAccessAnalytics** gespeichert. Im folgenden Screenshot ist ein Beispiel für eine Zeile in der Tabelle „UserAccessAnalytics“ für den Benutzer Alex Johnson dargestellt. Die **Quellentität** ist das Benutzer- oder Dienstprinzipalkonto, während die **Zielentität** die Ressource ist, auf die die Quellentität Zugriff hat. Die Werte von **Zugriffsebene** und **Zugriffstyp** hängen vom Zugriffssteuerungsmodell der Zielentität ab. Wie Sie sehen, hat Alex die Zugriffsberechtigung „Mitwirkender“ für das Azure-Abonnement *Contoso Hotels Tenant*. Für das Abonnement wird das Zugriffssteuerungsmodell Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung) verwendet.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Screenshot: Tabelle der Benutzerzugriffsanalyse":::

Zur Visualisierung der Berechtigungsanalysedaten können Sie (das weiter oben bereits erwähnte) [Jupyter Notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) im Azure Sentinel GitHub-Repository verwenden. Eine ausführliche Anleitung zur Verwendung von Notebook finden Sie im Notebook mit [geführten Analysen zu Metadaten zur Benutzersicherheit](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb).

### <a name="hunting-queries-and-exploration-queries"></a>Hunting-Abfragen und Auswertungsabfragen

Azure Sentinel stellt basierend auf der Tabelle „BehaviorAnalytics“ einsatzbereite Hunting-Abfragen, Auswertungsabfragen und eine Arbeitsmappe bereit. Diese Tools stellen angereicherte Daten mit dem Schwerpunkt auf bestimmten Anwendungsfällen dar, die auf ein anormales Verhalten hindeuten. 

Erfahren Sie mehr über [Hunting und das Untersuchungsdiagramm](./hunting.md) in Azure Sentinel.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie die Funktionen von Azure Sentinel zur Analyse des Verhaltens von Entitäten kennengelernt. Eine praktische Anleitung zur Implementierung und zur Verwendung der gewonnenen Erkenntnisse finden Sie in den folgenden Artikeln:

- [Aktivieren von User and Entity Behavior Analytics](./enable-entity-behavior-analytics.md) in Azure Sentinel
- [Aufspüren von Sicherheitsbedrohungen](./hunting.md)