---
title: Erstellen und Verwalten von Handelspartnerverträgen – Azure Logic Apps
description: Erstellen und Verwalten von Vereinbarungen zwischen Handelspartnern mithilfe von Azure Logic Apps und Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 04/05/2019
ms.openlocfilehash: 26d653b873e959f0804e0456ed87ee68c39413e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720676"
---
# <a name="create-and-manage-trading-partner-agreements-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Erstellen und Verwalten von Handelspartnerverträgen mithilfe von Azure Logic Apps und Enterprise Integration Pack

Ein [Handelspartnervertrag](../logic-apps/logic-apps-enterprise-integration-partners.md) 
 hilft Organisationen und Unternehmen bei der reibungslosen Kommunikation, indem das branchenübliche Protokoll definiert wird, das beim Austausch von B2B-Nachrichten (Business-to-Business) verwendet wird. ** Vereinbarungen bieten allgemeine Vorteile. Beispiel:

* Sie ermöglichen Organisationen den Austausch von Informationen über ein bekanntes Format.
* Sie erhöhen die Effizienz beim Durchführen von B2B-Transaktionen.
* Sie können einfach erstellt, verwaltet und zum Erstellen von Unternehmensintegrationslösungen verwendet werden.

In diesem Artikel wird gezeigt, wie Sie eine AS2-, EDIFACT- oder X12-Vereinbarung erstellen, die Sie beim Erstellen von Unternehmensintegrationslösungen für B2B-Szenarien mithilfe von [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) und [Azure Logic Apps](../logic-apps/logic-apps-overview.md) verwenden können. Nachdem Sie eine Vereinbarung erstellt haben, können Sie dann die AS2-, EDIFACT- oder X12-Connectors für den Austausch von B2B-Nachrichten verwenden.

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

   | Eigenschaft | Erforderlich | Value | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Name** | Ja | <*agreement-name*> | Der Name für Ihre Vereinbarung. |
   | **Vereinbarungstyp** | Ja | **AS2**, **X12** oder **EDIFACT** | Der Protokolltyp für Ihre Vereinbarung. Wenn Sie Ihre Vereinbarungsdatei erstellen, muss der Inhalt dieser Datei mit dem Vereinbarungstyp übereinstimmen. | |  
   | **Hostpartner** | Ja | <*host-partner-name*> | Der Hostpartner stellt die Organisation dar, die die Vereinbarung angibt. |
   | **Hostidentität** | Ja | <*host-partner-identifier*> | Der Bezeichner des Hostpartners. |
   | **Gastpartner** | Ja | <*guest-partner-name*> | Der Gastpartner stellt die Organisation dar, die Geschäfte mit dem Hostpartner tätigt. |
   | **Gastidentität** | Ja | <*guest-partner-identifier*> | Der Bezeichner des Gastpartners. |
   | **Empfangseinstellungen** | Variabel | Variabel | Diese Eigenschaften legen fest, wie alle eingehenden Nachrichten behandelt werden sollen, die von der Vereinbarung empfangen werden. Weitere Informationen finden Sie unter dem jeweiligen Vereinbarungstyp: <p>- [AS2-Nachrichteneinstellungen](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact.md) <br>- [X12-Nachrichteneinstellungen](logic-apps-enterprise-integration-x12.md) |
   | **Sendeeinstellungen** | Variabel | Variabel | Diese Eigenschaften legen fest, wie alle ausgehenden Nachrichten behandelt werden sollen, die von der Vereinbarung gesendet werden. Weitere Informationen finden Sie unter dem jeweiligen Vereinbarungstyp: <p>- [AS2-Nachrichteneinstellungen](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact.md) <br>- [X12-Nachrichteneinstellungen](logic-apps-enterprise-integration-x12.md) |
   |||||

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
