---
title: Sicherheitsplaybook in Azure Security Center | Microsoft-Dokumentation
description: Dieses Dokument enthält hilfreiche Informationen zur Automatisierung von Reaktionen auf Sicherheitsvorfälle mithilfe von Sicherheitsplaybooks in Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: ec16e6daec099adbede625c5ec6fe6909059143b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60907006"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Sicherheitsplaybook in Azure Security Center (Vorschauversion)
In diesem Dokument erfahren Sie, wie Sie mithilfe von Sicherheitsplaybooks in Azure Security Center auf sicherheitsbezogene Probleme reagieren können.

## <a name="what-is-security-playbook-in-security-center"></a>Was ist das Sicherheitsplaybook in Security Center?
Das Sicherheitsplaybook ist eine Sammlung von Prozeduren, die über Security Center ausgeführt werden können, wenn ein bestimmtes Playbook durch eine ausgewählte Warnung ausgelöst wird. Mithilfe von Sicherheitsplaybooks können Sie die Reaktion auf eine bestimmte, von Security Center erkannte Sicherheitswarnung automatisieren und orchestrieren. Da Sicherheitsplaybooks in Security Center auf [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) basieren, können Sie die Vorlagen verwenden, die in Logic Apps-Vorlagen unter der Kategorie „Sicherheit“ zur Verfügung stehen, und sie bei Bedarf an Ihre individuellen Anforderungen anpassen. Sie können aber auch neue Playbooks mithilfe des [Azure Logic Apps-Workflows](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) erstellen und dabei Security Center als Trigger verwenden.

> [!NOTE]
> Aufgrund der Nutzung von Azure Logic Apps fallen bei der Verwendung von Playbooks Gebühren an. Ausführlichere Informationen finden Sie auf der Preisseite von [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Erstellen eines Sicherheitsplaybooks in Security Center
Gehen Sie wie folgt vor, um in Security Center ein neues Sicherheitsplaybook zu erstellen:

1.  Öffnen Sie das Dashboard **Security Center**.
2.  Klicken Sie im linken Bereich im Abschnitt **Automation & Orchestration** (Automatisierung und Orchestrierung) auf **Playbooks** (Vorschauversion).

    ![Logik-App](./media/security-center-playbooks/security-center-playbooks-fig17.png)

3. Klicken Sie auf der Seite **Security Center - Playbooks** (Vorschauversion) auf die Schaltfläche **Hinzufügen**.

    ![Erstellen einer Logik-App](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. Geben Sie auf der Seite **Logik-App erstellen** die erforderlichen Informationen für die Erstellung Ihrer neuen Logik-App ein, und klicken Sie anschließend auf die Schaltfläche **Erstellen**. Nach Abschluss des Erstellungsvorgangs wird das neue Playbook in der Liste angezeigt. Klicken Sie andernfalls auf die Schaltfläche **Aktualisieren**. Klicken Sie auf das Playbook, um mit der Bearbeitung zu beginnen.

    ![Erstellen einer Logik-App](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. Der **Logik-App-Designer** wird angezeigt. Klicken Sie auf **Leere Logik-App**, um ein neues Playbook zu erstellen. Sie können auch eine der Vorlagen aus der Kategorie **Sicherheit** verwenden.

    ![Logik-App-Designer](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. Geben Sie im Feld **Alle Connectors und Trigger durchsuchen** die Zeichenfolge *Azure Security Center* ein, und wählen Sie **Beim Auslösen einer Antwort auf eine Azure Security Center-Warnung** aus.

    ![Trigger](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Nun können Sie definieren, was passieren soll, wenn das Playbook ausgelöst wird. Sie können eine Aktion, eine logische Bedingung, Parameterbedingungen oder Schleifen hinzufügen.

    ![Logik-App-Designer](./media/security-center-playbooks/security-center-playbooks-fig5.png)

## <a name="how-to-run-a-security-playbook-in-security-center"></a>Ausführen eines Sicherheitsplaybooks in Security Center

Ein Sicherheitsplaybook kann in Security Center zur Orchestrierung, zur Erlangung weiterer Informationen von anderen Diensten oder zur Wartung ausgeführt werden. Gehen Sie wie folgt vor, um auf die Playbooks zuzugreifen:

1.  Öffnen Sie das Dashboard **Security Center**.
2.  Klicken Sie im linken Bereich unter **Bedrohungserkennung** auf **Security incidents & alerts** (Sicherheitsvorfälle und -warnungen).

    ![Alerts](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Klicken Sie auf die Warnung, die Sie untersuchen möchten.
4.  Klicken Sie im oberen Bereich der Warnungsseite auf die Schaltfläche **Playbooks ausführen**.

    ![Playbook ausführen](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. Wählen Sie auf der Seite „Playbooks“ das gewünschte Playbook aus, und klicken Sie auf die Schaltfläche **Ausführen**. Wenn Sie das Playbook vor dem Auslösen anzeigen möchten, klicken Sie darauf, um den Designer zu öffnen.

    ![Playbooks](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Verlauf

Nach dem Ausführen des Playbooks können Sie auch auf vorherige Ausführungen sowie auf Schritte mit weiteren Informationen zum Status zuvor ausgeführter Playbooks zugreifen. Der Verlauf ist nach Warnung kontextualisiert. Der auf der Seite angezeigte Playbookverlauf hängt also mit der Warnung zusammen, die dieses Playbook ausgelöst hat.

![Verlauf](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Wenn Sie weitere Details zur Ausführung eines bestimmten Playbooks anzeigen möchten, klicken Sie auf das Playbook, um die Logik-App-Ausführungsseite mit dem gesamten Workflow aufzurufen.

![Details](./media/security-center-playbooks/security-center-playbooks-fig14.png)

In diesem Workflow wird die Ausführungsdauer der einzelnen Aufgaben angezeigt. Außerdem können Sie die einzelnen Aufgaben erweitert, um das jeweilige Ergebnis anzuzeigen.

### <a name="changing-an-existing-playbook"></a>Ändern eines vorhandenen Playbooks

Sie können ein vorhandenes Playbook in Security Center ändern, um eine Aktion oder Bedingungen hinzuzufügen. Klicken Sie dazu einfach auf der Registerkarte „Playbooks“ auf den Namen des Playbooks, das Sie ändern möchten. Daraufhin öffnet sich der Logik-App-Designer.

> [!NOTE]
> Weitere Informationen zum Erstellen eigener Playbooks mithilfe von Azure Logic Apps finden Sie unter [Erstellen Ihres ersten Logik-App-Workflows zur Automatisierung von Prozessen zwischen Cloud-Apps und Cloud-Diensten](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app).


## <a name="see-also"></a>Weitere Informationen
In diesem Dokument wurde die Verwendung von Playbooks in Azure Security Center erläutert. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Hier erfahren Sie, wie Sie Warnungen verwalten und auf Sicherheitsvorfälle in Security Center reagieren.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md). Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verstehen der Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Hier finden Sie Informationen zu den unterschiedlichen Arten von Sicherheitswarnungen.
* [Azure Security Center – Handbuch zur Problembehandlung](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Hier erfahren Sie, wie Sie allgemeine Probleme in Security Center behandeln.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md)festgelegt ist. Enthält häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/). Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.
