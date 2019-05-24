---
title: Notfallwiederherstellung für B2B-Integrationskonten – Azure Logic Apps | Microsoft-Dokumentation
description: Bereiten Sie sich auf die regionsübergreifende Notfallwiederherstellung in Azure Logic Apps vor.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/10/2017
ms.openlocfilehash: ac29ef7f0599cc41924ba1a5a00e46b0292e7e9b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967743"
---
# <a name="cross-region-disaster-recovery-for-b2b-integration-accounts-in-azure-logic-apps"></a>Regionsübergreifende Notfallwiederherstellung für B2B-Integrationskonten in Azure Logic Apps

B2B-Workloads umfassen Geldtransaktionen wie Aufträge und Rechnungen. Bei Notfallereignissen ist für Unternehmen eine schnelle Wiederherstellung von maßgeblicher Bedeutung, um die mit den Partnern getroffenen Vereinbarungen zum Servicelevel auf Unternehmensebene zu erfüllen. In diesem Artikel wird die Erstellung eines Geschäftskontinuitätsplans für B2B-Workloads beschrieben. 

* Vorbereitungen für die Notfallwiederherstellung 
* Durchführen eines Failovers auf die sekundäre Region während eines Notfallereignisses 
* Durchführen eines Fallbacks auf die primäre Region nach einem Notfallereignis

## <a name="disaster-recovery-readiness"></a>Vorbereitungen für die Notfallwiederherstellung  

1. Identifizieren Sie eine sekundäre Region, und erstellen Sie ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) in der sekundären Region.

2. Fügen Sie Partner, Schemas und Vereinbarungen für den erforderlichen Nachrichtenfluss hinzu, bei dem der Ausführungsstatus im Integrationskonto der sekundären Region repliziert werden muss.

   > [!TIP]
   > Stellen Sie sicher, dass die Integrationskontoartefakte in den Regionen konsistent benannt sind. 

3. Erstellen Sie eine Logik-App in der sekundären Region, um den Ausführungsstatus per Pull von der primären Region abzurufen. 

   Diese Logik-App muss einen *Trigger* und eine *Aktion* enthalten. 
   Dabei muss der Trigger mit dem Integrationskonto der primären Region und die Aktion mit dem Integrationskonto der sekundären Region verbunden werden. 
   Basierend auf dem angegebenen Zeitintervall fragt der Trigger die Ausführungsstatustabelle der primären Region ab und ruft gegebenenfalls die neuen Datensätze per Pull ab. Die Aktion aktualisiert sie im Integrationskonto der sekundären Region. 
   Auf diese Weise wird der inkrementelle Laufzeitstatus der primären Region in die sekundäre Region übertragen.

4. Die Geschäftskontinuität im Logic Apps-Integrationskonto unterstützt X12, AS2 und EDIFACT basierend auf B2B-Protokollen. Ausführliche Anweisungen erhalten Sie durch Auswählen der entsprechenden Links.

5. Es wird empfohlen, alle Ressourcen der primären Region auch in einer sekundären Region bereitzustellen. 

   Zu den Ressourcen der primären Region gehören Azure SQL-Datenbank oder Azure Cosmos DB, Azure Service Bus und Azure Event Hubs für Messaging, Azure API Management sowie das Azure Logic Apps-Feature von Azure App Service.   

6. Stellen Sie eine Verbindung zwischen der primären Region und der sekundären Region her. Erstellen Sie eine Logik-App in einer sekundären Region, um den Ausführungsstatus per Pull von der primären Region abzurufen. 

   Die Logik-App muss einen Trigger und eine Aktion enthalten. 
   Der Trigger muss mit dem Integrationskonto der primären Region verbunden werden. 
   Die Aktion muss mit dem Integrationskonto der sekundären Region verbunden werden. 
   Basierend auf dem angegebenen Zeitintervall fragt der Trigger die Ausführungsstatustabelle der primären Region ab und ruft gegebenenfalls die neuen Datensätze per Pull ab. 
   Die Aktion aktualisiert sie im Integrationskonto der sekundären Region. 
   Mit diesem Prozess wird der inkrementelle Laufzeitstatus der primären Region in die sekundäre Region übertragen.

Die Geschäftskontinuität im Logic Apps-Integrationskonto bietet Unterstützung basierend auf den B2B-Protokollen X12, AS2 und EDIFACT. Detaillierte Schritte zur Verwendung von X12 und AS2 finden Sie unter [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) und [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) in diesem Artikel.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Durchführen eines Failovers auf eine sekundäre Region während eines Notfallereignisses

Wenn während eines Notfallereignisses die primäre Region für die Geschäftskontinuität nicht verfügbar ist, wird der Datenverkehr an die sekundäre Region weitergeleitet. Mit einer sekundären Region können Unternehmen Funktionen schnell wiederherstellen, um die mit ihren Partnern vereinbarten RPO/RTO zu erfüllen. Zudem wird dadurch der Aufwand zum Durchführen eines Failovers von einer Region auf eine andere Region auf ein Minimum beschränkt. 

Beim Kopieren von Kontrollnummern von der primären Region in eine sekundäre Region ist eine Latenzzeit zu erwarten. Um zu vermeiden, dass während eines Notfallereignisses doppelt generierte Kontrollnummern an Partner gesendet werden, wird empfohlen, die Kontrollnummern in den Vereinbarungen für die sekundäre Region mithilfe von [PowerShell-Cmdlets](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0) zu erhöhen.

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Durchführen eines Fallbacks auf eine primäre Region nach dem Notfallereignis

Um ein Fallback auf eine primäre Region durchzuführen, sobald sie verfügbar ist, gehen Sie folgendermaßen vor:

1. Legen Sie fest, dass Nachrichten von Partnern in der sekundären Region nicht mehr akzeptiert werden.  

2. Erhöhen Sie die generierten Kontrollnummern für alle Vereinbarungen für die primäre Region mithilfe von [PowerShell-Cmdlets](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Leiten Sie Datenverkehr von der sekundären Region an die primäre Region weiter.

4. Vergewissern Sie sich, dass die in der sekundären Region erstellte Logik-App, die den Ausführungsstatus per Pull von der primären Region abruft, aktiviert ist.

## <a name="x12"></a>X12 

Die Geschäftskontinuität für EDI X12-Dokumente basiert auf Kontrollnummern:

> [!TIP]
> Sie können Logik-Apps auch mithilfe der [X12-Schnellstartvorlage](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) erstellen. Als Voraussetzung für die Verwendung der Vorlage müssen Integrationskonten für die primäre und die sekundäre Region erstellt werden. Mit der Vorlage können zwei Logik-Apps erstellt werden, eine für empfangene Kontrollnummern und eine zweite für generierte Kontrollnummern. In den Logik-Apps werden die entsprechenden Trigger und Aktionen erstellt. Der Trigger wird mit dem Integrationskonto der primären Region und die Aktion mit dem Integrationskonto der sekundären Region verbunden.

**Voraussetzungen**

Um die Notfallwiederherstellung für eingehende Nachrichten zu aktivieren, wählen Sie die Einstellungen für die Überprüfung auf Duplikate in den Empfangseinstellungen für die X12-Vereinbarung aus.

![Auswählen der Einstellungen für die Überprüfung auf Duplikate](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Erstellen Sie eine [Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) in einer sekundären Region.    

2. Suchen Sie nach **X12**, und wählen Sie **X12 – Bei Änderung einer Kontrollnummer** aus.   

   ![Suchen nach „X12“](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Der Trigger fordert Sie zum Herstellen einer Verbindung mit einem Integrationskonto auf. 
   Der Trigger muss mit dem Integrationskonto einer primären Region verbunden werden.

3. Geben Sie einen Verbindungsnamen ein, wählen Sie in der Liste das *Integrationskonto der primären Region* aus, und wählen Sie **Erstellen** aus.   

   ![Name des Integrationskontos der primären Region](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. Die Einstellung **Anfangsdatum und -uhrzeit für die Abfrage von Änderungen** ist optional. Die **Häufigkeit** kann mit einem Intervall auf **Tag**, **Stunde**, **Minute** oder **Sekunde** festgelegt werden.   

   ![Datum/Uhrzeit und Häufigkeit](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Wählen Sie **Nächster Schritt** > **Aktion hinzufügen** aus.

   ![„Neuer Schritt“, „Aktion hinzufügen“](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Suchen Sie nach **X12**, und wählen Sie **X12 – Kontrollnummern hinzufügen oder aktualisieren** aus.   

   ![Hinzufügen oder Aktualisieren von Kontrollnummern](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Um eine Aktion mit dem Integrationskonto einer sekundären Region zu verbinden, wählen Sie **Verbindung ändern** > **Neue Verbindung hinzufügen** aus, um eine Liste der verfügbaren Integrationskonten anzuzeigen. Geben Sie einen Verbindungsnamen ein, wählen Sie in der Liste das *Integrationskonto der sekundären Region* aus, und wählen Sie **Erstellen** aus. 

   ![Name des Integrationskontos der sekundären Region](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Wechseln Sie zu unformatierten Eingaben, indem Sie auf das Symbol in der rechten oberen Ecke klicken.

   ![Wechseln zu unformatierten Eingaben](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Wählen Sie in der Auswahl des dynamischen Inhalts den Haupttext aus, und speichern Sie die Logik-App.

   ![Felder für dynamische Inhalte](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Basierend auf dem angegebenen Zeitintervall fragt der Trigger die von der primären Region empfangene Kontrollnummerntabelle ab und ruft gegebenenfalls die neuen Datensätze per Pull ab. 
   Die Aktion aktualisiert die Datensätze im Integrationskonto der sekundären Region. 
   Wenn keine Aktualisierungen vorhanden sind, wird für den Trigger der Status **Übersprungen** angezeigt.   

   ![Kontrollnummerntabelle](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Basierend auf dem angegebenen Zeitintervall wird der inkrementelle Laufzeitstatus einer primären Region in einer sekundären Region repliziert. Wenn während eines Notfallereignisses die primäre Region nicht verfügbar ist, wird der Datenverkehr zur Geschäftskontinuität an die sekundäre Region weitergeleitet. 

## <a name="edifact"></a>EDIFACT 

Die Geschäftskontinuität für EDI EDIFACT-Dokumente basiert auf Kontrollnummern.

**Voraussetzungen**

Um die Notfallwiederherstellung für eingehende Nachrichten zu aktivieren, wählen Sie die Einstellungen für die Überprüfung auf Duplikate in den Empfangseinstellungen für Ihre EDIFACT-Vereinbarung aus.

![Auswählen der Einstellungen für die Überprüfung auf Duplikate](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Erstellen Sie eine [Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) in einer sekundären Region.    

2. Suchen Sie nach **EDIFACT**, und wählen Sie **EDIFACT – Bei Änderung einer Kontrollnummer** aus.

   ![Suchen nach „EDIFACT“](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Der Trigger fordert Sie zum Herstellen einer Verbindung mit einem Integrationskonto auf. 
   Der Trigger muss mit dem Integrationskonto einer primären Region verbunden werden. 

3. Geben Sie einen Verbindungsnamen ein, wählen Sie in der Liste das *Integrationskonto der primären Region* aus, und wählen Sie **Erstellen** aus.    

   ![Name des Integrationskontos der primären Region](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. Die Einstellung **Anfangsdatum und -uhrzeit für die Abfrage von Änderungen** ist optional. Die **Häufigkeit** kann mit einem Intervall auf **Tag**, **Stunde**, **Minute** oder **Sekunde** festgelegt werden.    

   ![Datum/Uhrzeit und Häufigkeit](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Wählen Sie **Nächster Schritt** > **Aktion hinzufügen** aus.    

   ![„Neuer Schritt“, „Aktion hinzufügen“](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Suchen Sie nach **EDIFACT**, und wählen Sie **EDIFACT – Kontrollnummern hinzufügen oder aktualisieren** aus.   

   ![Hinzufügen oder Aktualisieren von Kontrollnummern](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Um eine Aktion mit dem Integrationskonto einer sekundären Region zu verbinden, wählen Sie **Verbindung ändern** > **Neue Verbindung hinzufügen** aus, um eine Liste der verfügbaren Integrationskonten anzuzeigen. Geben Sie einen Verbindungsnamen ein, wählen Sie in der Liste das *Integrationskonto der sekundären Region* aus, und wählen Sie **Erstellen** aus.

   ![Name des Integrationskontos der sekundären Region](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Wechseln Sie zu unformatierten Eingaben, indem Sie auf das Symbol in der rechten oberen Ecke klicken.

   ![Wechseln zu unformatierten Eingaben](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Wählen Sie in der Auswahl des dynamischen Inhalts den Haupttext aus, und speichern Sie die Logik-App.   

   ![Felder für dynamische Inhalte](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Basierend auf dem angegebenen Zeitintervall fragt der Trigger die von der primären Region empfangene Kontrollnummerntabelle ab und ruft gegebenenfalls die neuen Datensätze per Pull ab.
   Die Aktion aktualisiert die Datensätze im Integrationskonto der sekundären Region. 
   Wenn keine Aktualisierungen vorhanden sind, wird für den Trigger der Status **Übersprungen** angezeigt.

   ![Kontrollnummerntabelle](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Basierend auf dem angegebenen Zeitintervall wird der inkrementelle Laufzeitstatus einer primären Region in einer sekundären Region repliziert. Wenn während eines Notfallereignisses die primäre Region nicht verfügbar ist, wird der Datenverkehr zur Geschäftskontinuität an die sekundäre Region weitergeleitet. 

## <a name="as2"></a>AS2 

Die Geschäftskontinuität für Dokumente, die das AS2-Protokoll verwenden, basiert auf der Nachrichten-ID und dem MIC-Wert.

> [!TIP]
> Sie können Logik-Apps auch mithilfe der [AS2-Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/pull/3302) erstellen. Als Voraussetzung für die Verwendung der Vorlage müssen Integrationskonten für die primäre und die sekundäre Region erstellt werden. Mit der Vorlage kann eine Logik-App mit einem Trigger und einer Aktion erstellt werden. Die Logik-App erstellt eine Verbindung zwischen einem Trigger und dem Integrationskonto einer primären Region sowie zwischen einer Aktion und dem Integrationskonto einer sekundären Region.

1. Erstellen Sie eine [Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) in der sekundären Region.  

2. Suchen Sie nach **AS2**, und wählen Sie **AS2 – When a MIC value is created** (AS2 – Beim Erstellen eines MIC-Werts) aus.   

   ![Suchen nach „AS2“](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Ein Trigger fordert Sie zum Herstellen einer Verbindung mit einem Integrationskonto auf. 
   Der Trigger muss mit dem Integrationskonto einer primären Region verbunden werden. 
   
3. Geben Sie einen Verbindungsnamen ein, wählen Sie in der Liste das *Integrationskonto der primären Region* aus, und wählen Sie **Erstellen** aus.

   ![Name des Integrationskontos der primären Region](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. Die Einstellung **DateTime to start MIC value sync** (DateTime-Wert zum Starten der Synchronisierung des MIC-Werts) ist optional. Die **Häufigkeit** kann mit einem Intervall auf **Tag**, **Stunde**, **Minute** oder **Sekunde** festgelegt werden.   

   ![Datum/Uhrzeit und Häufigkeit](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Wählen Sie **Nächster Schritt** > **Aktion hinzufügen** aus.  

   ![„Neuer Schritt“, „Aktion hinzufügen“](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Suchen Sie nach **AS2**, und wählen Sie **AS2 – Add or update a MIC contents** (MIC-Inhalte hinzufügen oder aktualisieren) aus.  

   ![Hinzufügen oder Aktualisieren eines MIC-Werts](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Um eine Aktion mit dem Integrationskonto einer sekundären Region zu verbinden, wählen Sie **Verbindung ändern** > **Neue Verbindung hinzufügen** aus, um eine Liste der verfügbaren Integrationskonten anzuzeigen. Geben Sie einen Verbindungsnamen ein, wählen Sie in der Liste das *Integrationskonto der sekundären Region* aus, und wählen Sie **Erstellen** aus.

   ![Name des Integrationskontos der sekundären Region](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Wechseln Sie zu unformatierten Eingaben, indem Sie auf das Symbol in der rechten oberen Ecke klicken.

   ![Wechseln zu unformatierten Eingaben](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Wählen Sie in der Auswahl des dynamischen Inhalts den Haupttext aus, und speichern Sie die Logik-App.   

   ![Dynamischer Inhalt](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Basierend auf dem angegebenen Zeitintervall fragt der Trigger die Tabelle der primären Region ab und ruft gegebenenfalls die neuen Datensätze per Pull ab. Die Aktion aktualisiert sie im Integrationskonto der sekundären Region. 
   Wenn keine Aktualisierungen vorhanden sind, wird für den Trigger der Status **Übersprungen** angezeigt.  

   ![Tabelle der primären Region](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Basierend auf dem angegebenen Zeitintervall wird der inkrementelle Laufzeitstatus der primären Region in der sekundären Region repliziert. Wenn während eines Notfallereignisses die primäre Region nicht verfügbar ist, wird der Datenverkehr zur Geschäftskontinuität an die sekundäre Region weitergeleitet. 

## <a name="next-steps"></a>Nächste Schritte

[Überwachen von B2B-Nachrichten](logic-apps-monitor-b2b-message.md)

