---
title: 'Erstellen und Verwalten von Integrationskonten für B2B-Lösungen: Azure Logic Apps | Microsoft-Dokumentation'
description: Erstellen, Verknüpfen, Verschieben und Löschen von Integrationskonten für Unternehmensintegrations- und B2B-Lösungen mit Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60999617"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Erstellen und Verwalten von Integrationskonten für B2B-Lösungen mit Logik-Apps

Bevor Sie [Unternehmensintegrations- und B2B-Lösungen](../logic-apps/logic-apps-enterprise-integration-overview.md) mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) erstellen können, benötigen Sie zuerst ein Integrationskonto. In diesem Konto erstellen, speichern und verwalten Sie B2B-Artefakte, etwa Handelspartner, Vereinbarungen, Zuordnungen, Schemas, Zertifikate usw. Damit Ihre Logik-App die Artefakte in Ihrem Integrationskonto und die Logic Apps B2B-Connectors (z.B. XML-Überprüfung) verwenden kann, müssen Sie [Ihr Integrationskonto mit Ihrer Logik-App verknüpfen](#link-account). Um die Verknüpfung zu ermöglichen, müssen sich das Integrationskonto und die Logik-App am *gleichen* Azure-Standort (in der gleichen Region) befinden.

In diesem Artikel wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

* Erstellen Ihres Integrationskontos
* Verknüpfen Ihres Integrationskontos mit einer Logik-App
* Verschieben Ihres Integrationskontos in eine andere Azure-Ressourcengruppe bzw. ein anderes Azure-Abonnement
* Löschen Ihres Integrationskontos

Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> an.

## <a name="create-integration-account"></a>Integrationskonto erstellen

1. Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. Geben Sie im Suchfeld „Integrationskonten“ als Filter ein, und wählen Sie den Eintrag **Integrationskonten** aus.

   ![Suchen von Integrationskonten](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Klicken Sie unter **Integrationskonten** auf **Hinzufügen**.

   ![Auswählen von „Hinzufügen“ zum Erstellen eines Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Geben Sie Informationen zu Ihrem Integrationskonto ein: 

   ![Details für das Integrationskonto angeben](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Eigenschaft | Erforderlich | Beispielwert | BESCHREIBUNG | 
   |----------|----------|---------------|-------------|
   | Name | Ja | test-integration-account | Der Name für Ihr Integrationskonto. Verwenden Sie in diesem Beispiel den angegebenen Namen. | 
   | Abonnement | Ja | <*Name des Azure-Abonnements*> | Der Name des zu verwendenden Azure-Abonnements | 
   | Ressourcengruppe | Ja | test-integration-account-rg | Der Name der [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md), die zum Organisieren verwandter Ressourcen verwendet wird. Erstellen Sie für dieses Beispiel eine neue Ressourcengruppe mit dem angegebenen Namen. | 
   | Preisstufe | Ja | Kostenlos | Der Tarif, den Sie verwenden möchten. Wählen Sie für dieses Beispiel **Free** aus. Weitere Informationen finden Sie unter [Logic Apps-Grenzwerte und -Konfiguration](../logic-apps/logic-apps-limits-and-config.md) und [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Location | Ja | USA (Westen) | Die Region, in der die Informationen zu Ihrem Integrationskonto gespeichert werden sollen. Wählen Sie entweder den gleichen Standort wie für die Logik-App aus, oder erstellen Sie eine Logik-App am gleichen Standort wie das Integrationskonto. | 
   | Log Analytics-Arbeitsbereich | Nein | Aus | Behalten Sie die Einstellung **Aus** für die Diagnoseprotokollierung bei. | 
   ||||| 

4. Wählen Sie dann **An Dashboard anheften** und **Erstellen** aus.

   Die Bereitstellung Ihres Integrationskontos am ausgewählten Standort durch Azure dauert in der Regel eine Minute. Anschließend wird Ihr Integrationskonto geöffnet.

   ![Öffnen Ihres Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Damit Ihre Logik-App Ihr Integrationskonto verwenden kann, müssen Sie nun das Integrationskonto mit Ihrer Logik-App verknüpfen.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Verknüpfen mit Logik-App

Damit Ihre Logik-Apps auf ein Integrationskonto mit Ihren B2B-Artefakten (Handelspartner, Vereinbarungen, Zuordnungen und Schemas) zugreifen können, müssen Sie das Integrationskonto mit Ihrer Logik-App verknüpfen. 

> [!NOTE]
> Ihr Integrationskonto und Ihre Logik-App müssen sich in der gleichen Region befinden.

1. Suchen Sie im Azure-Portal nach Ihrer Logik-App, und öffnen Sie sie.

2. Wählen Sie im Menü Ihrer Logik-App unter **Einstellungen** die Option **Workfloweinstellungen** aus. Wählen Sie in der Liste **Wählen Sie ein Integrationskonto aus.** das Integrationskonto aus, das Sie mit Ihrer Logik-App verknüpfen möchten.

   ![Auswählen Ihres Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Wählen Sie zum Fertigstellen der Verknüpfung die Option **Speichern** aus.

   ![Auswählen Ihres Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Wenn Ihr Integrationskonto erfolgreich verknüpft wurde, wird in Azure eine Bestätigungsmeldung angezeigt. 

   ![Bestätigungslink](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Ihre Logik-App kann nun alle Artefakte in Ihrem Integrationskonto sowie die B2B-Connectors (etwa XML-Überprüfung und Flatfilecodierung- bzw. decodierung) verwenden.  

## <a name="unlink-from-logic-app"></a>Aufheben der Verknüpfung mit der Logik-App

Wenn Sie Ihre Logik-App mit einem anderen Integrationskonto verknüpfen oder kein Integrationskonto mehr mit Ihrer Logik-App verwenden möchten, können Sie die Verknüpfung über den Azure-Ressourcen-Explorer löschen.

1. Öffnen Sie in Ihrem Browser <a href="https://resources.azure.com" target="_blank">Azure-Ressourcen-Explorer (https://resources.azure.com)</a>. Achten Sie darauf, dass Sie mit den gleichen Azure-Anmeldeinformationen angemeldet sind.

   ![Azure-Ressourcen-Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. Geben Sie im Suchfeld den Namen Ihrer Logik-App ein, suchen Sie sie, und wählen Sie sie aus.

   ![Suchen und Auswählen der Logik-App](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Wählen Sie in der Titelleiste des Explorers **Lesen/Schreiben**.

   ![Aktivieren des Lese-/Schreibmodus](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Wählen Sie auf der Registerkarte **Daten** die Option **Bearbeiten** aus.

   ![Auswählen von „Bearbeiten“ auf der Registerkarte „Daten“](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. Suchen Sie im Editor die Eigenschaft `integrationAccount` für das Integrationskonto, und löschen Sie diese Eigenschaft, die im folgenden Format angegeben ist:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Beispiel:

   ![Suchen der Eigenschaftendefinition „integrationAccount“](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Klicken Sie auf der Registerkarte **Daten** auf **Put**, um die Änderungen zu speichern. 

   ![Auswählen von „Put“ zum Speichern der Änderungen](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. Überprüfen Sie im Azure-Portal in den **Workflow-Einstellungen** Ihrer Logik-App, ob die Eigenschaft **Integrationskonto** nun leer ist:

   ![Sicherstellen, dass das Integrationskonto nicht verknüpft ist](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Verschieben eines Integrationskontos

Sie können Ihr Integrationskonto in ein anderes Azure-Abonnement bzw. eine andere Azure-Ressourcengruppe verschieben.

1. Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. Geben Sie im Suchfeld „Integrationskonten“ als Filter ein, und wählen Sie den Eintrag **Integrationskonten** aus.

   ![Suchen Ihres Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Wählen Sie unter **Integrationskonten** das zu verschiebende Integrationskonto aus. Klicken Sie im Menü Ihres Integrationskontos unter **Einstellungen** auf **Eigenschaften**.

   ![Auswählen von „Eigenschaften“ unter „Einstellungen“](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Ändern Sie entweder die Azure-Ressourcengruppe oder das Azure-Abonnement für Ihr Integrationskonto.

   ![Auswählen von „Ressourcengruppe ändern“ oder „Abonnement ändern“](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Achten Sie anschließend darauf, alle Skripts mit den neuen Ressourcen-IDs für Ihre Artefakte zu aktualisieren.  

## <a name="delete-integration-account"></a>Löschen eines Integrationskontos

1. Wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. Geben Sie im Suchfeld „Integrationskonten“ als Filter ein, und wählen Sie den Eintrag **Integrationskonten** aus.

   ![Suchen Ihres Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Wählen Sie unter **Integrationskonten** das zu löschende Integrationskonto aus. Klicken Sie im Menü des Integrationskontos auf **Übersicht** und dann auf **Löschen**. 

   ![Auswählen des Integrationskontos Auswählen von „Löschen“ auf der Seite „Übersicht“](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Um zu bestätigen, dass Sie Ihr Integrationskonto löschen möchten, wählen Sie **Ja**.

   ![Auswählen von „Ja“ zum Bestätigen des Löschvorgangs](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Handelspartnern](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Erstellen von Vereinbarungen](../logic-apps/logic-apps-enterprise-integration-agreements.md)
