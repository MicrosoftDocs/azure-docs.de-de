---
title: Erstellen von Workflows aus Vorlagen – Azure Logic Apps | Microsoft-Dokumentation
description: Schnelleres Erstellen von Workflows mit Logik-App-Vorlagen in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.date: 10/15/2017
ms.openlocfilehash: 134a8f9625b45a8196ebd47f10286093f6ba0d46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459624"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Erstellen von Logik-App-Workflows aus vorgefertigten Vorlagen

Damit Sie schneller mit dem Erstellen von Workflows beginnen können, werden von Logic Apps Vorlagen bereitgestellt. Hierbei handelt es sich um vorgefertigte Logik-Apps, die auf häufig verwendeten Mustern basieren. Verwenden Sie diese Vorlagen unverändert, oder passen Sie sie an Ihr Szenario an.

Hier sind einige Vorlagenkategorien angegeben:

| Vorlagentyp | BESCHREIBUNG | 
| ------------- | ----------- | 
| Enterprise Cloud-Vorlagen | Dienen zum Integrieren von Azure Blob, Dynamics CRM, Salesforce, Box, Azure Blob und enthalten andere Connectors für die Anforderungen Ihrer Unternehmenscloud. Sie können diese Vorlagen beispielsweise verwenden, um geschäftliche Leads zu organisieren oder Ihre Unternehmensdateidaten zu sichern. | 
| Persönliche Produktivitätsvorlagen | Dienen zum Verbessern der persönlichen Produktivität, indem tägliche Erinnerungen festgelegt, wichtige Arbeitselemente in Aufgabenlisten umgewandelt und langwierige Aufgaben bis hinunter zu einem Genehmigungsschritt durch einen einzelnen Benutzer automatisiert werden. | 
| Vorlagen für die Cloud für Consumer | Dienen zum Integrieren von sozialen Medien, z.B. Twitter, Slack und E-Mail. Sie sind nützlich, um Marketinginitiativen für soziale Medien zu stärken. Diese Vorlagen enthalten auch Aufgaben, die eine Steigerung der Produktivität bewirken, z.B. Cloudkopiervorgänge, indem Zeit für die Durchführung von herkömmlichen häufigen Aufgaben eingespart wird. | 
| Enterprise Integration Pack-Vorlagen | Dienen zum Konfigurieren von VETER-Pipelines (validate, extract, transform, enrich, route: überprüfen, extrahieren, transformieren, erweitern, weiterleiten), mit denen ein X12 EDI-Dokument über AS2 empfangen und in XML umgewandelt wird und X12-, EDIFACT- und AS2-Nachrichten verarbeitet werden. | 
| Vorlagen für Protokollmuster | Dienen zum Implementieren von Protokollmustern, z.B. Anforderung/Antwort per HTTP und Integrationen per FTP und SFTP. Verwenden Sie diese Vorlagen unverändert, oder erweitern Sie sie, um komplexe Protokollmuster abzubilden. | 
||| 

Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/), bevor Sie beginnen. Weitere Informationen zum Erstellen einer Logik-App finden Sie unter [Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Erstellen von Logik-Apps aus Vorlagen

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com "Azure-Portal") an, falls Sie dies noch nicht getan haben.

2. Klicken Sie im Hauptmenü von Azure auf **Ressource erstellen** > **Unternehmensintegration** > **Logik-App**.

   ![Azure-Portal, Neu, Enterprise Integration, Logik-App](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Erstellen Sie Ihre Logik-App mit den Einstellungen in der Tabelle unter dieser Abbildung:

   ![Angeben von Details zur Logik-App](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Einstellung | Wert | BESCHREIBUNG | 
   | ------- | ----- | ----------- | 
   | **Name** | *Name Ihrer Logik-App* | Geben Sie einen eindeutigen Namen für die Logik-App an. | 
   | **Abonnement** | *Name-Ihres-Azure-Abonnements* | Wählen Sie das gewünschte Azure-Abonnement aus. | 
   | **Ressourcengruppe** | *Name-Ihrer-Azure-Ressourcengruppe* | Erstellen bzw. wählen Sie eine [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md) für diese Logik-App und zum Organisieren aller Ressourcen, die der App zugeordnet sind. | 
   | **Location** | *Ihre-Azure-Datencenterregion* | Wählen Sie die Datencenterregion für die Bereitstellung Ihrer Logik-App aus, z.B. „USA, Westen“. | 
   | **Log Analytics** | **Aus** (Standard) oder **Ein** | Aktivieren Sie die [Diagnoseprotokollierung](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) für Ihre Logik-App über [Azure Monitor-Protokolle](../log-analytics/log-analytics-overview.md). Hierfür ist es erforderlich, dass Sie bereits über einen Log Analytics-Arbeitsbereich verfügen. | 
   |||| 

4. Wählen Sie die Option **An Dashboard anheften**, wenn Sie fertig sind. Ihre Logik-App wird dann automatisch in Ihrem Azure-Dashboard angezeigt und nach der Bereitstellung geöffnet. Wählen Sie **Erstellen**.

   > [!NOTE]
   > Wenn Sie Ihre Logik-App nicht anheften möchten, müssen Sie sie nach der Bereitstellung manuell suchen und öffnen, um fortfahren zu können.

   Nachdem Ihre Logik-App von Azure bereitgestellt wurde, wird der Designer für Logik-Apps geöffnet, und es wird eine Seite mit einem Einführungsvideo angezeigt. 
   Unterhalb des Videos sind Vorlagen für häufig verwendete Logik-App-Muster angegeben. 

5. Scrollen Sie weiter, um die Option **Vorlagen** nach dem Einführungsvideo und den häufig verwendeten Triggern anzuzeigen. Wählen Sie eine vordefinierte Vorlage aus. Beispiel:

   ![Auswählen einer Logik-App-Vorlage](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Wählen Sie die Option **Leere Logik-App**, um Ihre Logik-App von Grund auf neu zu erstellen.

   Wenn Sie eine vordefinierte Vorlage auswählen, können Sie weitere Informationen zu dieser Vorlage anzeigen. 
   Beispiel:

   ![Auswählen einer vordefinierten Vorlage](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Wählen Sie die Option **Diese Vorlage verwenden**, um mit der ausgewählten Vorlage fortzufahren. 

7. Basierend auf den Connectors in der Vorlage werden Sie aufgefordert, die folgenden Schritte auszuführen:

   * Melden Sie sich mit Ihren Anmeldeinformationen an Systemen oder Diensten an, auf die von der Vorlage verwiesen wird.

   * Erstellen Sie Verbindungen für alle Dienste oder Systeme, auf die von der Vorlage verwiesen wird. Geben Sie zum Erstellen einer Verbindung einen Namen für Ihre Verbindung an, und wählen Sie bei Bedarf die Ressource aus, die Sie verwenden möchten. 

   * Wählen Sie die Option **Weiter**, falls Sie diese Verbindung bereits eingerichtet haben.

   Beispiel:

   ![Erstellen von Verbindungen](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Wenn Sie fertig sind, wird Ihre Logik-App geöffnet und im Designer für Logik-Apps angezeigt.

   > [!TIP]
   > Wählen Sie in der Symbolleiste des Designers die Option **Vorlagen**, um zurück zur Vorlagenansicht zu wechseln. Mit dieser Aktion werden alle ungespeicherten Änderungen verworfen, und es wird eine Warnmeldung zur Bestätigung Ihrer Anforderung angezeigt.

8. Fahren Sie mit dem Erstellen Ihrer Logik-App fort.

   > [!NOTE] 
   > Viele Vorlagen enthalten Connectors, die unter Umständen bereits über vorab aufgefüllte erforderliche Eigenschaften verfügen. Für einige Vorlagen kann aber trotzdem das Angeben von Werten erforderlich sein, bevor Sie die Logik-App richtig bereitstellen können. Wenn Sie versuchen, die Bereitstellung ohne das Ausfüllen der fehlenden Eigenschaftenfelder durchzuführen, erhalten Sie eine Fehlermeldung. 

## <a name="update-logic-apps-with-templates"></a>Aktualisieren von Logik-Apps mit Vorlagen

1. Suchen Sie im [Azure-Portal](https://portal.azure.com "Azure-Portal") im Designer für Logik-Apps nach Ihrer Logik-App, und öffnen Sie sie.

2. Wählen Sie in der Symbolleiste des Designers die Option **Vorlagen**. Bei dieser Aktion werden alle nicht gespeicherten Änderungen verworfen, und es wird eine Warnmeldung angezeigt, damit Sie das Fortfahren bestätigen können. Wählen Sie **OK**, um den Vorgang zu bestätigen. Beispiel:

   ![Auswählen von „Vorlagen“](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Scrollen Sie weiter, um die Option **Vorlagen** nach dem Einführungsvideo und den häufig verwendeten Triggern anzuzeigen. Wählen Sie eine vordefinierte Vorlage aus. Beispiel:

   ![Auswählen einer Logik-App-Vorlage](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Wenn Sie eine vordefinierte Vorlage auswählen, können Sie weitere Informationen zu dieser Vorlage anzeigen. 
   Beispiel:

   ![Auswählen einer vordefinierten Vorlage](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Wählen Sie die Option **Diese Vorlage verwenden**, um mit der ausgewählten Vorlage fortzufahren. 

5. Basierend auf den Connectors in der Vorlage werden Sie aufgefordert, die folgenden Schritte auszuführen:

   * Melden Sie sich mit Ihren Anmeldeinformationen an Systemen oder Diensten an, auf die von der Vorlage verwiesen wird.

   * Erstellen Sie Verbindungen für alle Dienste oder Systeme, auf die von der Vorlage verwiesen wird. Geben Sie zum Erstellen einer Verbindung einen Namen für Ihre Verbindung an, und wählen Sie bei Bedarf die Ressource aus, die Sie verwenden möchten. 

   * Wählen Sie die Option **Weiter**, falls Sie diese Verbindung bereits eingerichtet haben.

   ![Erstellen von Verbindungen](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Ihre Logik-App wird jetzt geöffnet und im Designer für Logik-Apps angezeigt.

8. Fahren Sie mit dem Erstellen Ihrer Logik-App fort. 

   > [!TIP]
   > Wenn Sie Ihre Änderungen nicht gespeichert haben, können Sie Ihre Arbeit verwerfen und zu Ihrer vorherigen Logik-App zurückkehren. Wählen Sie in der Symbolleiste des Designers die Option **Verwerfen**.

> [!NOTE] 
> Viele Vorlagen enthalten Connectors, die unter Umständen bereits über vorab aufgefüllte erforderliche Eigenschaften verfügen. Für einige Vorlagen kann aber trotzdem das Angeben von Werten erforderlich sein, bevor Sie die Logik-App richtig bereitstellen können. Wenn Sie versuchen, die Bereitstellung ohne das Ausfüllen der fehlenden Eigenschaftenfelder durchzuführen, erhalten Sie eine Fehlermeldung.

## <a name="deploy-logic-apps-built-from-templates"></a>Bereitstellen von Logik-Apps, die aus Vorlagen erstellt wurden

Nachdem Sie Ihre Änderungen an der Vorlage vorgenommen haben, können Sie sie speichern. Bei dieser Aktion wird Ihre Logik-App auch automatisch veröffentlicht.

Wählen Sie auf der Symbolleiste des Designers **Speichern**.

![Speichern und Veröffentlichen Ihrer Logik-App](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Erstellen von Logik-Apps anhand von Beispielen, Szenarien, Kundenstimmen und exemplarischen Vorgehensweisen.

> [!div class="nextstepaction"]
> [Gängige Szenarien, Beispiele, Tutorials und exemplarische Vorgehensweisen für Azure Logic Apps](../logic-apps/logic-apps-examples-and-scenarios.md)