---
title: Erstellen oder Verknüpfen paralleler Verzweigungen – Azure Logic Apps | Microsoft-Dokumentation
description: Vorgehensweise zum Erstellen oder Verknüpfen paralleler Verzweigungen für Workflows in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 2e1c155a371fa96e4f772f632a9585948b012e54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685065"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Erstellen oder Verknüpfen paralleler Verzweigungen für Workflowaktionen in Azure Logic Apps

Standardmäßig werden Ihre Aktionen in Logik-App-Workflows nacheinander ausgeführt. Zum gleichzeitigen Ausführen unabhängiger Aktionen können Sie [parallele Verzweigungen](#parallel-branches) erstellen und später in Ihrem Flow [diese Verzweigungen verknüpfen](#join-branches). 

> [!TIP] 
> Wenn Sie einen Auslöser verwenden, der ein Array empfängt, und für jedes Arrayelement einen Workflow ausführen möchten, können Sie dieses Array mit der [**Auslösereigenschaft** SplitOn](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) *aus dem Batch lösen*.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/). 

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Hinzufügen einer parallelen Verzweigung

Zum gleichzeitigen Ausführen unabhängiger Schritte können Sie neben einem vorhandenen Schritt parallele Verzweigungen hinzufügen. 

![Paralleles Ausführen von Schritten](media/logic-apps-control-flow-branches/parallel.png)

Ihre Logik-App wartet, bis alle Verzweigungen abgeschlossen sind, bevor der Workflow fortgesetzt wird. Parallele Verzweigungen werden nur ausgeführt, wenn ihre `runAfter`-Eigenschaftswerte dem Status des abgeschlossenen übergeordneten Schritts entsprechen. Beispielsweise wird die Ausführung von sowohl `branchAction1` als auch `branchAction2` nur festgelegt, wenn `parentAction` mit dem Status `Succeeded` abgeschlossen wird.

> [!NOTE]
> Bevor Sie beginnen, muss Ihre Logik-App bereits über einen Schritt verfügen, dem Sie parallele Verzweigungen hinzufügen können.

1. Öffnen Sie Ihre Logik-App im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> im Logik-App-Designer.

1. Bewegen Sie den Zeiger auf den Pfeil über dem Schritt, dem Sie parallele Branches hinzufügen möchten. Wählen Sie das daraufhin angezeigte **Pluszeichen** ( **+** ) aus, und wählen Sie dann **Parallelen Branch hinzufügen** aus. 

   ![Hinzufügen einer parallelen Verzweigung](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Suchen Sie über das Suchfeld nach der gewünschten Aktion, und wählen Sie sie aus.

   ![Suchen und Auswählen der gewünschten Aktion](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Ihre ausgewählte Aktion wird jetzt z. B. im parallelen Branch angezeigt:

   ![Suchen und Auswählen der gewünschten Aktion](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Fügen Sie für jeden parallelen Branch die gewünschten Schritte hinzu. Um einem Branch eine weitere Aktion hinzuzufügen, bewegen Sie den Mauszeiger unter die Aktion, an der Sie eine sequenzielle Aktion hinzufügen möchten. Wählen Sie das daraufhin angezeigte **Pluszeichen** ( **+** ) und dann **Aktion hinzufügen** aus.

   ![Hinzufügen einer sequenziellen Aktion zu einem parallelen Branch](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Suchen Sie über das Suchfeld nach der gewünschten Aktion, und wählen Sie sie aus.

   ![Suchen und Auswählen der sequenziellen Aktion](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Ihre ausgewählte Aktion wird jetzt z. B. im aktuellen Branch angezeigt:

   ![Suchen und Auswählen der sequenziellen Aktion](media/logic-apps-control-flow-branches/added-sequential-action.png)

[Verknüpfen Sie Ihre parallelen Verzweigungen](#join-branches), um Verzweigungen wieder zusammenführen. 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definition (JSON) einer parallelen Verzweigung

Wenn Sie in der Codeansicht arbeiten, können Sie die parallele Struktur stattdessen in der JSON-Definition der Logik-App definieren, z.B.:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Verknüpfen von parallelen Verzweigungen

Um parallele Verzweigungen zusammenführen fügen Sie einfach einen Schritt im unteren Bereich unter allen Verzweigungen hinzu. Dieser Schritt wird ausgeführt, nachdem die Ausführung aller parallelen Verzweigungen abgeschlossen ist.

![Verknüpfen von parallelen Verzweigungen](media/logic-apps-control-flow-branches/join.png)

1. Suchen oder öffnen Sie Ihre Logik-App im [Azure-Portal](https://portal.azure.com) im Logik-App-Designer. 

1. Wählen Sie unter den parallelen Branches, die Sie verknüpfen möchten, **Neuer Schritt** aus. 

   ![Hinzufügen eines zu verknüpfenden Schritts](media/logic-apps-control-flow-branches/add-join-step.png)

1. Suchen und wählen Sie im Suchfeld die gewünschte Aktion als Schritt aus, der die Branches verknüpft.

   ![Suchen und Auswählen der Aktion, die parallele Branches verknüpft](media/logic-apps-control-flow-branches/join-steps.png)

   Ihre parallelen Verzweigungen sind jetzt zusammengeführt.

   ![Verknüpfte Branches](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Verknüpfungsdefinition (JSON)

Wenn Sie in der Codeansicht arbeiten, können Sie die Verknüpfungsstruktur stattdessen in der JSON-Definition der Logik-App definieren, z.B.:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Featurevorschläge übermitteln oder darüber abstimmen möchten, besuchen Sie die [Website für Azure Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* [Ausführen von Schritten basierend auf einer Bedingung (Bedingungsanweisungen)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Ausführen von Schritten basierend auf verschiedenen Werten (switch-Anweisungen)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ausführen und Wiederholen von Schritten (Schleifen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Ausführen von Schritten basierend auf gruppierten Aktionsstatus (Bereiche)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
