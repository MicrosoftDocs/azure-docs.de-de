---
title: Handelspartnervereinbarungen
description: Erstellen und Verwalten von Vereinbarungen zwischen Handelspartnern mithilfe von Azure Logic Apps und Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: c8cbfb619c9eed325161503f705bf5c4c0746265
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992947"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Erstellen und Verwalten von Handelspartnerverträgen in Azure Logic Apps

Ein [Handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*Vereinbarung* hilft Organisationen und Unternehmen, die nahtlos miteinander kommunizieren können, definieren Sie das spezifische Branche zum Standard-Protokoll verwenden, beim Austausch von Nachrichten für die Business-to-Business (B2B). Vereinbarungen bieten allgemeine Vorteile. Beispiel:

* Sie ermöglichen Organisationen den Austausch von Informationen über ein bekanntes Format.
* Sie erhöhen die Effizienz beim Durchführen von B2B-Transaktionen.
* Sie können einfach erstellt, verwaltet und zum Erstellen von Unternehmensintegrationslösungen verwendet werden.

In diesem Artikel wird gezeigt, wie Sie eine AS2-, EDIFACT- oder X12-Vereinbarung erstellen, die Sie beim Erstellen von Unternehmensintegrationslösungen für B2B-Szenarien mithilfe von [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) und [Azure Logic Apps](../logic-apps/logic-apps-overview.md) verwenden können. Nachdem Sie eine Vereinbarung erstellt haben, können Sie dann die AS2-, EDIFACT- oder X12-Connectors für den Austausch von B2B-Nachrichten verwenden.

Weitere Informationen zum Austauschen von RosettaNet-Nachrichten finden Sie unter [Exchange RosettaNet messages (Austauschen von RosettaNet-Nachrichten)](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement haben, [melden Sie sich für ein kostenloses Azure-Konto an](https://azure.microsoft.com/free/).

* Ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) zur Speicherung Ihrer Vereinbarung und anderer B2B-Artefakte. Dieses Integrationskonto muss mit Ihrem Azure-Abonnement verknüpft sein.

* Mindestens zwei [Handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md), die Sie bereits in Ihrem Integrationskonto erstellt haben. Eine Vereinbarung erfordert sowohl einen Host- als auch einen Gastpartner. Beide Partner müssen denselben Qualifizierer für die „Geschäftsidentität“ verwenden wie die zu erstellende Vereinbarung, z. B. AS2, X12 oder EDIFACT.

* Optional: Die Logik-App, in der Sie Ihre Vereinbarung verwenden möchten, und ein Trigger, der den Workflow Ihrer Logik-App startet. Sie benötigen keine Logik-App, wenn Sie nur Ihr Integrationskonto und B2B-Artefakte erstellen möchten. Bevor Ihre Logik-App die B2B-Artefakte in Ihrem Integrationskonto verwenden kann, müssen Sie jedoch Ihr Integrationskonto mit Ihrer Logik-App verknüpfen. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Erstellen von Vereinbarungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. Geben Sie im Suchfeld den Begriff „integration“ als Filter ein. Wählen Sie in den Ergebnissen diese Ressource aus: **Integrationskonten**

   ![Suchen Ihres Integrationskontos](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, in dem Sie die Vereinbarung erstellen möchten.

   ![Integrationskonto für die Erstellung der Vereinbarung auswählen](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. Wählen Sie im rechten Bereich unter **Komponenten** die Kachel **Vereinbarungen** aus.

   ![Wählen Sie „Vereinbarungen“ aus.](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Wählen Sie unter **Vereinbarungen** die Option **Hinzufügen** aus. Geben Sie im Bereich **Hinzufügen** Informationen zu Ihrer Vereinbarung an. Beispiel:

   ![„Hinzufügen“ auswählen](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Name** | Ja | <*agreement-name*> | Der Name für Ihre Vereinbarung. |
   | **Vereinbarungstyp** | Ja | **AS2**, **X12** oder **EDIFACT** | Der Protokolltyp für Ihre Vereinbarung. Wenn Sie Ihre Vereinbarungsdatei erstellen, muss der Inhalt dieser Datei mit dem Vereinbarungstyp übereinstimmen. | |  
   | **Hostpartner** | Ja | <*host-partner-name*> | Der Hostpartner stellt die Organisation dar, die die Vereinbarung angibt. |
   | **Hostidentität** | Ja | <*host-partner-identifier*> | Der Bezeichner des Hostpartners. |
   | **Gastpartner** | Ja | <*guest-partner-name*> | Der Gastpartner stellt die Organisation dar, die Geschäfte mit dem Hostpartner tätigt. |
   | **Gastidentität** | Ja | <*guest-partner-identifier*> | Der Bezeichner des Gastpartners. |
   | **Empfangseinstellungen** | Varies | Varies | Diese Eigenschaften legen fest, wie der Hostpartner alle Nachrichten vom in der Vereinbarung angegebenen Gastpartner empfängt. Weitere Informationen finden Sie unter dem jeweiligen Vereinbarungstyp: <p>- [AS2-Nachrichteneinstellungen](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact.md) <br>- [X12-Nachrichteneinstellungen](logic-apps-enterprise-integration-x12.md) |
   | **Sendeeinstellungen** | Varies | Varies | Die Eigenschaften legen fest, wie der Hostpartner alle Nachrichten an den in der Vereinbarung angegebenen Gastpartner sendet. Weitere Informationen finden Sie unter dem jeweiligen Vereinbarungstyp: <p>- [AS2-Nachrichteneinstellungen](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact.md) <br>- [X12-Nachrichteneinstellungen](logic-apps-enterprise-integration-x12.md) |
   |||||

   > [!IMPORTANT]
   > Die Auflösung für eine Vereinbarung hängt von der Übereinstimmung dieser Elemente ab, die in der Partner- und eingehenden Nachricht definiert sind:
   >
   > * Qualifizierer und Bezeichner des Absenders
   > * Qualifizierer und Bezeichner des Empfängers
   >
   > Falls sich diese Werte für Ihren Partner ändern, stellen Sie sicher, dass Sie die Vereinbarung entsprechend aktualisieren.

1. Wenn Sie die Erstellung Ihrer Vereinbarung abgeschlossen haben, wählen Sie auf der Seite **Hinzufügen** die Option **OK** aus, und kehren Sie zu Ihrem Integrationskonto zurück.

   Die Liste **Vereinbarungen** zeigt jetzt Ihre neue Vereinbarung an.

## <a name="edit-agreements"></a>Bearbeiten von Vereinbarungen

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Azure-Hauptmenü **Alle Dienste** aus.

1. Geben Sie im Suchfeld den Begriff „integration“ als Filter ein. Wählen Sie in den Ergebnissen diese Ressource aus: **Integrationskonten**

1. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, das die Vereinbarung enthält, die Sie bearbeiten möchten.

1. Wählen Sie im rechten Bereich unter **Komponenten** die Kachel **Vereinbarungen** aus.

1. Wählen Sie unter **Vereinbarungen** Ihre Vereinbarung und dann die Option **Bearbeiten** aus.

1. Nehmen Sie Ihre Änderungen vor und speichern Sie sie anschließend.

## <a name="delete-agreements"></a>Löschen von Vereinbarungen

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Azure-Hauptmenü **Alle Dienste** aus.

1. Geben Sie im Suchfeld den Begriff „integration“ als Filter ein. Wählen Sie in den Ergebnissen diese Ressource aus: **Integrationskonten**

1. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, das die zu löschende Vereinbarung enthält.

1. Wählen Sie im rechten Bereich unter **Komponenten** die Kachel **Vereinbarungen** aus.

1. Wählen Sie unter **Vereinbarungen** Ihre Vereinbarung und anschließend **Löschen** aus.

1. Bestätigen Sie, dass Sie die ausgewählte Vereinbarung löschen möchten.

## <a name="next-steps"></a>Nächste Schritte

* [Austauschen von AS2-Nachrichten](logic-apps-enterprise-integration-as2.md)
* [Austauschen von EDIFACT-Nachrichten](logic-apps-enterprise-integration-edifact.md)
* [Austauschen von X12-Nachrichten](logic-apps-enterprise-integration-x12.md)
