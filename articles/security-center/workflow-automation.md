---
title: Workflowautomatisierung in Azure Security Center | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Workflows in Azure Security Center erstellen und automatisieren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: a7341362183aee4a23556a164677bc320babdfec
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900839"
---
# <a name="automate-responses-to-security-center-triggers"></a>Automatisieren der Reaktionen auf Security Center-Trigger

Jedes Sicherheitsprogramm umfasst mehrere Workflows für die Reaktion auf Vorfälle. Diese Prozesse können das Benachrichtigen relevanter Stakeholder, das Starten eines Change Management-Prozesses und das Anwenden spezifischer Korrekturschritte umfassen. Sicherheitsexperten empfehlen, möglichst viele Schritte dieser Verfahren zu automatisieren. Durch Automatisierung wird der Aufwand reduziert. Außerdem können Sie so die Sicherheit erhöhen, indem Sie sicherstellen, dass die Prozessschritte schnell, konsistent und gemäß Ihren vordefinierten Anforderungen ausgeführt werden.

In diesem Artikel wird die Funktion zur Workflowautomatisierung von Azure Security Center beschrieben. Dieses Feature kann Logic Apps bei Sicherheitswarnungen und Empfehlungen auslösen. Beispielsweise können Sie von Security Center E-Mail-Nachrichten an einen bestimmten Benutzer senden, wenn eine Warnung auftritt. Außerdem erfahren Sie, wie Sie Logik-Apps mithilfe von [Azure Logic Apps](../logic-apps/logic-apps-overview.md) erstellen.


## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemein verfügbar (Generally Available, GA)|
|Preise:|Kostenlos|
|Erforderliche Rollen und Berechtigungen:|Rolle **Sicherheitsadministrator** oder **Besitzer** für die Ressourcengruppe<br>Außerdem sind Schreibberechtigungen für die Zielressource erforderlich<br><br>Damit Sie Azure Logic Apps-Workflows verwenden können, benötigen Sie zudem die folgenden Logic Apps-Rollen/-Berechtigungen:<br> Die Berechtigungen der Rolle - [Logik-App-Operator](../role-based-access-control/built-in-roles.md#logic-app-operator) oder der Lese-/Triggerzugriff für Logik-Apps sind erforderlich. (Diese Rolle kann keine Logik-Apps erstellen oder bearbeiten, sondern nur vorhandene *ausführen* .)<br> Die Berechtigungen der Rolle - [Logik-App-Mitwirkender](../role-based-access-control/built-in-roles.md#logic-app-contributor) sind für die Erstellung und Änderung von Logik-Apps erforderlich.<br>Wenn Sie Logik-App-Connectors verwenden möchten, benötigen Sie möglicherweise zusätzliche Anmeldeinformationen für die Anmeldung bei den jeweiligen Diensten (z. B. Ihren Instanzen von Outlook, Teams oder Slack).|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Ja](./media/icons/yes-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Erstellen einer Logik-App und Definieren des Zeitpunkts ihrer automatischen Ausführung 

1. Wählen Sie auf der Seitenleiste in Security Center **Workflowautomatisierung** aus.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Liste der Workflowautomatisierungen":::

    Auf dieser Seite können Sie neue Automatisierungsregeln erstellen sowie vorhandene aktivieren, deaktivieren oder löschen.

1. Um einen neuen Workflow zu definieren, klicken Sie auf **Workflowautomatisierung hinzufügen** . 

    Ein Bereich mit Optionen für die neue Automatisierung wird angezeigt. Darin können Sie Folgendes eingeben:
    1. Einen Namen und eine Beschreibung für die Automatisierung
    1. Die Trigger zum Auslösen dieses automatischen Workflows. Sie könnten beispielsweise Ihre Logik-App ausführen, wenn eine Sicherheitswarnung generiert wird, die „SQL“ enthält.

        > [!NOTE]
        > Wenn Ihr Trigger eine Empfehlung mit „untergeordneten Empfehlungen“ ist, z. B. **Ergebnisse der Sicherheitsrisikobewertung in Ihren SQL Datenbanken müssen beseitigt werden** , wird die Logik-App nicht bei jedem neuen Sicherheitsergebnis ausgelöst, sondern nur dann, wenn sich der Status der übergeordneten Empfehlung ändert.

    1. Die Logik-App, die ausgeführt wird, wenn die Triggerbedingungen erfüllt sind 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Liste der Workflowautomatisierungen":::

1. Klicken Sie im Abschnitt „Aktionen“ auf **Erstellen Sie eine neue** , um mit der Erstellung der Logik-App zu beginnen.

    Sie werden zu Azure Logic Apps umgeleitet.

    [![Erstellen einer neuen Logik-App](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Geben Sie einen Namen, eine Ressourcengruppe und einen Speicherort ein, und klicken Sie auf **Erstellen** .

1. Sie können in Ihrer neuen Logik-App zwischen integrierten, vordefinierten Vorlagen der Kategorie „Sicherheit“ auswählen. Sie können aber auch einen benutzerdefinierten Ereignisflow definieren, der beim Auslösen dieses Prozesses gestartet wird.

    > [!TIP]
    > Manchmal sind in einer Logikanwendung Parameter im Connector als Teil einer Zeichenfolge und nicht in einem eigenen Feld enthalten. Ein Beispiel für das Extrahieren von Parametern finden Sie in Schritt 14 von [Working with logic app parameters while building Azure Security Center workflow automations](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121) (Arbeiten mit Parametern der Logik-App beim Erstellen von Azure Security Center-Workflowautomatisierungen).

    Der Logik-App-Designer unterstützt diese Security Center-Trigger:

    * **Bei Erstellen oder Auslösen einer Azure Security Center-Empfehlung** : Wenn Ihre Logik-App auf einer Empfehlung basiert, die veraltet ist oder ersetzt wird, funktioniert die Automatisierung nicht mehr, und Sie müssen den Trigger aktualisieren. Informationen zum Nachverfolgen von Änderungen an Empfehlungen finden Sie unter [Versionshinweise für Azure Security Center](release-notes.md).

    * **Bei Erstellen oder Auslösen einer Azure Security Center-Warnung** : Sie können den Trigger so anpassen, dass er sich nur auf Warnungen mit den für sie interessanten Schweregraden bezieht.
    
    > [!NOTE]
    > Wenn Sie den älteren Trigger „Beim Auslösen einer Antwort auf eine Azure Security Center-Warnung“ verwenden, wird Ihre Logik-App nicht von der Funktion „Workflowautomatisierung“ gestartet. Verwenden Sie stattdessen einen der oben genannten Trigger. 

    [![Beispiel-Logik-App](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Kehren Sie nach dem Definieren Ihrer Logik-App zum Bereich zur Definition der Workflowautomatisierung („Workflowautomatisierung hinzufügen“) zurück. Klicken Sie auf **Aktualisieren** , um sicherzustellen, dass Ihre neue Logik-App als Auswahl verfügbar ist.

    ![Aktualisieren](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Wählen Sie die Logik-App aus, und speichern Sie die Automatisierung. Beachten Sie, dass in der Logik-App-Dropdownliste nur Logik-Apps mit unterstützenden Security Center-Connectors angezeigt werden.


## <a name="manually-trigger-a-logic-app"></a>Manuelles Auslösen einer Logik-App

Sie können Logic Apps auch manuell ausführen, wenn Sie sich eine beliebige Sicherheitswarnung oder -empfehlung ansehen.

Um eine Logik-App manuell auszuführen, öffnen Sie eine Warnung oder Empfehlung, und klicken Sie auf **Logik-App auslösen** :

[![Manuelles Auslösen einer Logik-App](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>Konfigurieren der Workflowautomatisierung im großen Stil mithilfe der bereitgestellten Richtlinien

Die Automatisierung der Prozesse Ihrer Organisation zur Überwachung und Reaktion auf Vorfälle kann die Zeit, die zum Untersuchen von Sicherheitsvorfällen und zur Durchführung entsprechender Gegenmaßnahmen benötigt wird, erheblich verkürzen.

Verwenden Sie für die Bereitstellung Ihrer Automatisierungskonfigurationen in Ihrer Organisation die unten beschriebenen von Azure Policy bereitgestellten „DeployIfNotExist“-Richtlinien zum Erstellen und Konfigurieren von Prozeduren für die Workflowautomatisierung.

Beginnen Sie mit [Vorlagen zur Workflowautomatisierung](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

So implementieren Sie diese Richtlinien

1. Klicken Sie in der folgenden Tabelle auf die Richtlinie, die Sie anwenden möchten:

    |Zielsetzung  |Richtlinie  |Richtlinien-ID  |
    |---------|---------|---------|
    |Workflowautomatisierung für Sicherheitswarnungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Warnungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Workflowautomatisierung für Sicherheitsempfehlungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Empfehlungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    ||||

    > [!TIP]
    > Sie können auch in Azure Policy nach diesen Richtlinien suchen:
    > 1. Öffnen Sie Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Liste der Workflowautomatisierungen":::
    > 2. Klicken Sie im Azure Policy-Menü auf **Definitionen** , und suchen Sie nach dem Namen der gewünschten Richtlinie. 

1. Klicken Sie auf der entsprechenden Azure Policy-Seite auf **Zuweisen** .
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Liste der Workflowautomatisierungen":::

1. Öffnen Sie alle Registerkarten, und legen Sie die Parameter wie gewünscht fest:
    1. Legen Sie auf der Registerkarte **Grundeinstellungen** den Bereich für die Richtlinie fest. Weisen Sie die Richtlinie für eine zentrale Verwaltung der Verwaltungsgruppe mit den Abonnements zu, die die Konfiguration für die Workflowautomatisierung verwenden sollen. 
    1. Legen Sie auf der Registerkarte **Parameter** die Ressourcengruppe und die Details für den Datentyp fest. 
        > [!TIP]
        > Jeder Parameter verfügt über eine QuickInfo, in der die verfügbaren Optionen erläutert werden.
        >
        > Die Registerkarte „Parameter“ in Azure Policy (1) bietet ähnliche Konfigurationsoptionen wie die Seite „Workflowautomatisierung“ in Security Center (2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="Liste der Workflowautomatisierungen" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Wenn Sie diese Zuweisung auf vorhandene Abonnements anwenden möchten, können Sie die Registerkarte **Wartung** öffnen und die Option zum Erstellen eines Wartungstasks auswählen.

1. Überprüfen Sie die Seite „Zusammenfassung“, und klicken Sie auf **Erstellen** .


## <a name="data-types-schemas"></a>Datentypenschemas

Um die unformatierten Ereignisschemas der Sicherheitswarnungen oder Empfehlungsereignisse anzuzeigen, die an die Logik-App-Instanz übermittelt werden, sehen Sie sich die [Schemas für Workflowautomatisierungs-Datentypen](https://aka.ms/ASCAutomationSchemas) an. Dies kann nützlich sein, wenn Sie nicht die oben genannten integrierten Logik-App-Connectors von Security Center verwenden, sondern den generischen HTTP-Connector der Logik-App. Sie können die Analyse bei Bedarf mit dem JSON-Ereignisschema manuell durchführen.


## <a name="faq-for-workflow-automation"></a>Häufig gestellte Fragen zur Workflowautomatisierung

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Unterstützt die Workflowautomatisierung irgendwelche Szenarien der Geschäftskontinuität oder Notfallwiederherstellung (BCDR)?

Wenn Sie Ihre Umgebung für BCDR-Szenarien vorbereiten, in denen in der Zielressource ein Ausfall oder ein anderer Notfass eintritt, liegt es in der Verantwortung der Organisation, durch Einrichten von Sicherungen gemäß den Richtlinien von Azure Event Hubs, Log Analytics-Arbeitsbereichen und der Logik-App Datenverluste zu verhindern.

Wir empfehlen Ihnen, für jede aktive Automatisierung eine identische (deaktivierte) Automatisierung zu erstellen und an einem anderen Ort zu speichern. Bei einem Ausfall können Sie diese Sicherungsautomatisierungen aktivieren und den normalen Betrieb aufrechterhalten.

Weitere Informationen zu [Business Continuity & Disaster Recovery für Azure Logic Apps](../logic-apps/business-continuity-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Logik-Apps erstellen, deren Ausführung in Security Center automatisieren und sie manuell ausführen. 

Verwandte Informationen finden Sie hier: 

- [Das Microsoft Learn-Modul über die Verwendung der Workflowautomatisierung zur Automatisierung einer Sicherheitsreaktion](/learn/modules/resolve-threats-with-azure-security-center/)
- [Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md)
- [Sicherheitswarnungen in Azure Security Center](security-center-alerts-overview.md)
- [Informationen zu Azure Logic Apps](../logic-apps/logic-apps-overview.md)
- [Connectors für Azure Logic Apps](../connectors/apis-list.md)
- [Workflowautomatisierungs-Datentypenschemas](https://aka.ms/ASCAutomationSchemas)