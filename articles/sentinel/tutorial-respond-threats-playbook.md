---
title: 'Tutorial: Ausführen eines Playbooks in Azure Sentinel'
description: In diesem Tutorial wird beschrieben, wie Sie in Azure Sentinel mithilfe von Sicherheitsplaybooks automatisierte Reaktionen auf Bedrohungen im Zusammenhang mit sicherheitsbezogenen Problemen festlegen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: b6fd26b4965b92f5f06a008d67e2d585fd1b41b7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94652075"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Tutorial: Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel



In diesem Tutorial erfahren Sie, wie Sie in Azure Sentinel mithilfe von Sicherheitsplaybooks automatisierte Reaktionen auf Bedrohungen im Zusammenhang mit sicherheitsbezogenen Problemen festlegen, die von Azure Sentinel erkannt werden.


> [!div class="checklist"]
> * Grundlegendes zu Playbooks
> * Erstellen eines Playbooks
> * Ausführen eines Playbooks
> * Automatisieren der Reaktionen auf Bedrohungen


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Was ist ein Sicherheitsplaybook in Azure Sentinel?

Bei einem Sicherheitsplaybook handelt es sich um eine Sammlung von Prozeduren, die über Azure Sentinel als Reaktion auf eine Warnung ausgeführt werden können. Ein Sicherheitsplaybook ermöglicht die Automatisierung und Orchestrierung Ihrer Reaktion und kann manuell ausgeführt oder so konfiguriert werden, dass es automatisch ausgeführt wird, wenn bestimmte Warnungen ausgelöst werden. Da Sicherheitsplaybooks in Azure Sentinel auf [Azure Logic Apps](../logic-apps/logic-apps-overview.md) basieren, stehen Ihnen die Leistung, Anpassbarkeit und integrierten Vorlagen zur Verfügung, die Sie von Logic Apps gewohnt sind. Die einzelnen Playbooks werden zwar jeweils für das spezifische, von Ihnen ausgewählte Abonnement erstellt, auf der Seite „Playbooks“ werden jedoch alle Playbooks für alle ausgewählten Abonnements angezeigt.

> [!NOTE]
> Aufgrund der Nutzung von Azure Logic Apps fallen bei der Verwendung von Playbooks Gebühren an. Ausführlichere Informationen finden Sie auf der Preisseite von [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Wenn Sie beispielsweise einen Angriff auf Ihre Netzwerkressourcen befürchten, können Sie eine Warnung für schädliche IP-Adressen festlegen, die auf Ihr Netzwerk zugreifen. Anschließend können Sie ein Playbook mit folgenden Eigenschaften erstellen:
1. Wenn die Warnung ausgelöst wird, wird in ServiceNow oder in einem anderen IT-Ticketsystem ein Ticket erstellt.
2. Es wird eine Nachricht an Ihren Sicherheitskanal in Microsoft Teams oder Slack gesendet, um Ihre Sicherheitsanalysten auf den Vorfall aufmerksam zu machen.
3. Sämtliche Informationen in der Warnung werden an den leitenden Netzwerkadministrator sowie an den leitenden Sicherheitsadministrator gesendet. Die E-Mail enthält auch zwei Schaltflächen: **Blockieren** und **Ignorieren**.
4. Das Playbook wird nach Eingang einer Antwort der Administratoren weiter ausgeführt.
5. Wenn die Administratoren **Blockieren** auswählen, wird die IP-Adresse in der Firewall blockiert, und der Benutzer wird in Azure AD deaktiviert.
6. Wenn die Administratoren **Ignorieren** auswählen, werden die Warnung in Azure Sentinel und der Vorfall in ServiceNow geschlossen.

Sicherheitsplaybooks können manuell oder automatisch ausgeführt werden. Bei der manuellen Ausführung können Sie bei Eingang einer Warnung auswählen, dass ein Playbook als Reaktion auf die ausgewählte Warnung ausgeführt werden soll. Bei der automatischen Ausführung legen Sie im Zuge der Erstellung der Korrelationsregel fest, dass ein Playbook (oder auch mehrere Playbooks) ausgeführt werden soll, wenn die Warnung ausgelöst wird.


## <a name="create-a-security-playbook"></a>Erstellen eines Sicherheitsplaybooks

Gehen Sie wie folgt vor, um in Azure Sentinel ein neues Sicherheitsplaybook zu erstellen:

1. Öffnen Sie das Dashboard **Azure Sentinel**.
2. Wählen Sie unter **Konfiguration** die Option **Playbooks** aus.

   ![Logik-App](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Klicken Sie auf der Seite **Azure Sentinel – Playbooks** auf die Schaltfläche **Hinzufügen**.

   ![Erstellen einer Logik-App](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Geben Sie auf der Seite **Logik-App erstellen** die erforderlichen Informationen für die Erstellung Ihrer neuen Logik-App ein, und klicken Sie anschließend auf **Erstellen**. 

5. Wählen Sie im [**Logik-App-Designer**](../logic-apps/logic-apps-overview.md) die gewünschte Vorlage aus. Wenn Sie sich für eine Vorlage entscheiden, die Anmeldeinformationen erfordert, müssen Sie diese angeben. Alternativ können Sie auch ein neues leeres Playbook erstellen. Wählen Sie **Leere Logik-App** aus. 

   ![Screenshot: Bereich „Leere Logik-App“](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Im daraufhin angezeigten Logik-App-Designer können Sie entweder eine neue Logik-App erstellen oder die Vorlage bearbeiten. Weitere Informationen zum Erstellen eines Playbooks mit Logic Apps finden Sie [hier](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Wenn Sie ein leeres Playbook erstellen, geben Sie im Feld **Alle Connectors und Trigger durchsuchen** die Zeichenfolge *Azure Sentinel* ein, und wählen Sie **Beim Auslösen einer Antwort auf eine Azure Sentinel-Warnung** aus. <br>Das neu erstellte Playbook wird in der Liste **Playbooks** angezeigt. Sollte es nicht angezeigt werden, klicken Sie auf **Aktualisieren**.

1. Verwenden Sie die Funktionen vom Typ **Entitäten abrufen**, mit denen Sie die relevanten Entitäten aus der Liste **Entitäten** abrufen können, etwa Konten, IP-Adressen und Hosts. Dies ermöglicht Ihnen das Ausführen von Aktionen für bestimmte Entitäten.

7. Nun können Sie definieren, was passieren soll, wenn das Playbook ausgelöst wird. Sie können eine Aktion, eine logische Bedingung, Parameterbedingungen oder Schleifen hinzufügen.

   ![Logik-App-Designer](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Ausführen eines Sicherheitsplaybooks

Sie können ein Playbook bei Bedarf ausführen.

So führen Sie ein Playbook bei Bedarf aus:

1. Wählen Sie auf der Seite **Incidents** einen Incident aus, und klicken Sie anschließend auf **View full details** (Details vollständig anzeigen).

2. Klicken Sie auf der Registerkarte **Warnungen** auf die Warnung, für die Sie das Playbook ausführen möchten, scrollen Sie ganz nach rechts, klicken Sie auf **Playbooks anzeigen**, und wählen Sie in der Liste mit den verfügbaren Playbooks für das Abonnement ein Playbook zum **Ausführen** aus. 



## <a name="automate-threat-responses"></a>Automatisieren der Reaktionen auf Bedrohungen

SIEM/SOC-Teams können gelegentlich von Sicherheitswarnungen geradezu überschwemmt werden. Dabei kann das Warnungsaufkommen so hoch sein, dass die zur Verfügung stehenden Sicherheitsadministratoren überlastet sind. Dies führt oftmals dazu, dass zahlreiche Warnungen nicht untersucht werden können und Organisationen anfällig für nicht erkannte Angriffe werden. 

Viele – wenn nicht sogar der Großteil – dieser Warnungen entsprechen wiederkehrenden Mustern, die mithilfe spezifischer, definierter Wartungsaktionen verarbeitet werden können. Azure Sentinel ermöglicht Ihnen in Playbooks bereits das Definieren von Wartungsaktionen. Darüber hinaus ist es möglich, Echtzeitautomatisierung als Teil der Playbookdefinition festzulegen, um Ihnen die vollständige Automatisierung einer definierten Reaktion auf bestimmte Sicherheitswarnungen zu ermöglichen. Mithilfe der Echtzeitautomatisierung können zuständige Teams die Arbeitsauslastung erheblich reduzieren, indem sie die Routinereaktionen auf wiederkehrende Warnungstypen vollständig automatisieren, sodass Sie sich auf eindeutige Warnungen, die Analyse von Mustern, die Ermittlung von Bedrohungen und mehr konzentrieren können.

So automatisieren Sie Reaktionen:

1. Wählen Sie die Warnung aus, für die Sie die Reaktion automatisieren möchten.
1. Wählen Sie auf der Seite **Warnungsregel bearbeiten** unter **Real-time automation** (Echtzeitautomatisierung) für **Triggered playbooks** (Ausgelöste Playbooks) das Playbook aus, das bei einer Übereinstimmung mit dieser Warnungsregel ausgeführt werden soll.
1. Wählen Sie **Speichern** aus.

   ![Echtzeitautomatisierung](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie ein Playbook in Azure Sentinel ausführen. Im [nächsten Artikel](hunting.md) erfahren Sie, wie Sie mithilfe von Azure Sentinel proaktiv nach Bedrohungen suchen.