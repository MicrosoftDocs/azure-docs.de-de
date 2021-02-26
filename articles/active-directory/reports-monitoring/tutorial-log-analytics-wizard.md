---
title: Konfigurieren des Protokollanalyseassistenten in Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie die Protokollanalyse konfiguriert wird.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca84fa57cb3a26337038275d1b7491154915c90e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574378"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Tutorial: Konfigurieren des Protokollanalyseassistenten


In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Konfigurieren eines Protokollanalyse-Arbeitsbereichs für ihre Überprüfungs- und Anmeldeprotokolle
> * Ausführen von Abfragen mithilfe der Kusto-Abfragesprache (Kusto Query Language, KQL)
> * Erstellen einer Warnungsregel, die Warnungen sendet, wenn ein bestimmtes Konto verwendet wird
> * Erstellen einer benutzerdefinierten Arbeitsmappe mithilfe der Schnellstartvorlage
> * Hinzufügen einer Abfrage zu einer vorhandenen Arbeitsmappenvorlage

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement mit mindestens einem Administrator mit P1-Lizenz. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für eine kostenlose Testversion registrieren](https://azure.microsoft.com/free/).

- Einen Azure AD-Mandanten.

- Einen Benutzer, der als globaler Administrator oder Sicherheitsadministrator für den Azure AD-Mandanten fungiert.


Machen Sie sich mit diesen Artikeln vertraut:

- [Tutorial: Sammeln und Analysieren von Ressourcenprotokollen von einer Azure-Ressource](../../azure-monitor/essentials/tutorial-resource-logs.md)

- [Integrieren von Aktivitätsprotokollen mit Log Analytics](./howto-integrate-activity-logs-with-log-analytics.md)

- [Verwalten eines Kontos für den Notfallzugriff in Azure AD](../roles/security-emergency-access.md)

- [KQL-Kurzübersicht](/azure/data-explorer/kql-quick-reference)

- [Azure Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md)



## <a name="configure-a-workspace"></a>Konfigurieren eines Arbeitsbereichs 

In diesem Verfahren wird beschrieben, wie Sie einen Protokollanalyse-Arbeitsbereich für ihre Überprüfungs- und Anmeldeprotokolle konfigurieren.
Das Konfigurieren eines Protokollanalyse-Arbeitsbereichs besteht aus zwei Hauptschritten:
 
1. Erstellen eines Protokollanalyse-Arbeitsbereichs
2. Festlegen von Diagnoseeinstellungen

**Konfigurieren eines Arbeitsbereichs:** 


1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com) an.

2. Suche nach **Protokollanalyse-Arbeitsbereichen**.

    ![Ressourcen, Dienste und Dokumente durchsuchen](./media/tutorial-log-analytics-wizard/search-services.png)

3. Klicken Sie auf der Seite der Protokollanalyse-Arbeitsbereiche auf **Hinzufügen**.

    ![Screenshot: Schaltfläche „Hinzufügen“ auf der Seite „Log Analytics-Arbeitsbereiche“](./media/tutorial-log-analytics-wizard/add.png)

4.  Führen Sie auf der Seite **Log Analytics-Arbeitsbereich erstellen** die folgenden Schritte aus:

    ![Erstellen eines Log Analytics-Arbeitsbereichs](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Wählen Sie Ihr Abonnement aus.

    2. Wählen Sie eine Ressourcengruppe aus.
 
    3. Geben Sie im Textfeld **Name** einen Namen ein (z. B. MytestWorkspace1).

    4. Wählen Sie Ihre Region aus.

5. Klicken Sie auf **Überprüfen und erstellen**.

    ![Überprüfen und Erstellen](./media/tutorial-log-analytics-wizard/review-create.png)

6. Klicken Sie auf **Erstellen**, und warten Sie, bis die Bereitstellung erfolgreich war. Möglicherweise müssen Sie die Seite aktualisieren, um den neuen Arbeitsbereich anzuzeigen.

    ![Erstellen](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. Suchen Sie nach **Azure Active Directory**.

    ![Screenshot: Azure Active Directory in der Azure-Suche](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. Klicken Sie im Abschnitt **Überwachen** auf **Diagnoseeinstellung**.

    ![Screenshot: Unter „Überwachen“ ausgewählte Option „Diagnoseeinstellungen“](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. Klicken Sie auf der Seite **Diagnoseeinstellungen** auf **Diagnoseeinstellung hinzufügen**.

    ![Hinzufügen einer Diagnoseeinstellung](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. Führen Sie auf der Seite **Diagnoseeinstellung** die folgenden Schritte aus:

    ![Diagnoseeinstellungen auswählen](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. Wählen Sie unter **Kategoriedetails** die Optionen **AuditLogs** und **SigninLogs** aus.

    2. Wählen Sie unter **Zieldetails** die Option **An Log Analytics senden** und anschließend Ihren neuen Protokollanalyse-Arbeitsbereich aus. 
   
    3. Klicken Sie auf **Speichern**. 

## <a name="run-queries"></a>Ausführen von Abfragen  

In diesem Verfahren wird das Ausführen von Abfragen mithilfe der **Kusto-Abfragesprache** (Kusto Query Language, KQL) gezeigt.


**Ausführen einer Abfrage:**


1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Azure Active Directory**.

    ![Screenshot: Azure Active Directory in der Azure-Suche](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Klicken Sie im Abschnitt **Überwachung** auf **Protokolle**.

4. Klicken Sie auf der Seite **Protokolle** auf **Erste Schritte**.

5. Geben Sie im Textfeld **Suche* Ihre Abfrage ein.

6. Klicken Sie auf **Ausführen**.  


### <a name="kql-query-examples"></a>KQL-Abfragebeispiele

Nehmen Sie 10 zufällige Einträge aus den Eingabedaten:

`SigninLogs | take 10`

Anmeldungen anzeigen, bei denen der bedingte Zugriff erfolgreich war

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Zählen, wie viele Erfolge vorhanden waren

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


Aggregierte Anzahl der erfolgreichen Anmeldungen nach Benutzer und Tag:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Anzeigen, wie oft ein Benutzer einen bestimmten Vorgang innerhalb eines bestimmten Zeitraums durchführt:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


Pivotieren der Ergebnisse nach Vorgangsname

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Zusammenführen von Überprüfungs- und Anmeldeprotokollen mit einem inneren Join:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated, UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


Anzeigen der Anzahl von Anmeldungen nach Client-App-Typ:

`SigninLogs | summarize count() by ClientAppUsed`

Anmeldungen nach Tag zählen:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

Nehmen Sie 5 zufällige Einträge, und projizieren Sie die Spalten, die in den Ergebnissen angezeigt werden sollen:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


Nehmen Sie die ersten 5 in absteigender Reihenfolge, und projizieren Sie die Spalten, die Sie sehen möchten.

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Erstellen Sie eine neue Spalte, indem Sie die Werte mit zwei anderen Spalten kombinieren:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Erstellen einer Warnungsregel  

Dieses Verfahren zeigt, wie Sie Warnungen senden, wenn das BreakGlass-Konto verwendet wird.

**So erstellen Sie eine Warnungsregel:**


1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Azure Active Directory**.

    ![Screenshot: Azure Active Directory in der Azure-Suche](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Klicken Sie im Abschnitt **Überwachung** auf **Protokolle**.

4. Klicken Sie auf der Seite **Protokolle** auf **Erste Schritte**.

5. Geben Sie im Textfeld **Suche** Folgendes ein: `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. Klicken Sie auf **Ausführen**.  

7. Klicken Sie auf der Symbolleiste auf **Neue Warnungsregel**.

    ![Neue Warnungsregel](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. Überprüfen Sie auf der Seite **Warnungsregel erstellen**, ob der Bereich korrekt ist.

9. Klicken Sie unter **Bedingung** auf: **Immer wenn die benutzerdefinierte Protokollsuche größer als <logic undefined> ist**

    ![Standardbedingung](./media/tutorial-log-analytics-wizard/default-condition.png)

10. Führen Sie auf der Seite **Signallogik konfigurieren** im Abschnitt **Warnungslogik** die folgenden Schritte aus:

    ![Warnungslogik](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. Wählen Sie für **Basierend auf** die Option **Anzahl der Ergebnisse** aus.

    2. Wählen Sie für **Operator** die Option **Größer als** aus.

    3. Wählen Sie für **Schwellenwert** den Wert **0** aus. 

11. Führen Sie auf der Seite **Signallogik konfigurieren** im Abschnitt **Auswertung basierend auf** die folgenden Schritte aus:

    ![Auswertung basierend auf](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. Wählen Sie für **Zeitraum (in Minuten)** den Wert **5** aus.

    2. Wählen Sie für **Häufigkeit (in Minuten)** den Wert **5** aus.

    3. Klicken Sie auf **Fertig**. 

12. Klicken Sie unter **Aktionsgruppe** auf **Aktionsgruppe auswählen**. 

    ![Aktionsgruppe](./media/tutorial-log-analytics-wizard/action-group.png)

13. Klicken Sie bei **Aktionsgruppe zum Anfügen an diese Warnungsregel auswählen** auf **Aktionsgruppe erstellen**. 

    ![Erstellen einer Aktionsgruppe](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. Führen Sie auf der Seite **Aktionsgruppe erstellen** die folgenden Schritte aus:

    ![Instanzendetails](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. Geben Sie im Textfeld **Aktionsgruppenname** den Text **Meine Aktionsgruppe** ein.

    2. Geben Sie im Textfeld **Anzeigename** den Text **Meine Aktion** ein.

    3. Klicken Sie auf **Überprüfen + erstellen**. 

    4. Klicken Sie auf **Erstellen**.


15. Führen Sie unter **Anpassen** die folgenden Schritte aus:

    ![Aktionen anpassen](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. Wählen Sie **E-Mail-Betreff** aus.

    2. Geben Sie im Textfeld **Betreffzeile** Folgendes ein: `Breakglass account has been used`

16. Führen Sie unter **Warnungsregeldetails** die folgenden Schritte aus:

    ![Warnungsregeldetails](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. Geben Sie im Textfeld **Name der Warnungsregel** Folgendes ein: `Breakglass account`

    2. Geben Sie im Textfeld **Beschreibung** Folgendes ein: `Your emergency access account has been used`.

17. Klicken Sie auf **Warnungsregel erstellen**.   


## <a name="create-a-custom-workbook"></a>Benutzerdefinierte Arbeitsmappe erstellen

In diesem Verfahren wird gezeigt, wie Sie mithilfe der Schnellstartvorlage eine neue Arbeitsmappe erstellen.




1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Azure Active Directory**.

    ![Screenshot: Azure Active Directory in der Azure-Suche](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Klicken Sie im Abschnitt **Überwachung** auf **Arbeitsmappen**.

    ![Screenshot: „Überwachung“ im Azure-Portalmenü mit ausgewählter Option „Arbeitsmappen“](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Klicken Sie im Abschnitt **Schnellstart** auf **Leer**.

    ![Schnellstart](./media/tutorial-log-analytics-wizard/quick-start.png)

5. Klicken Sie auf **Hinzufügen**.

    ![Hinzufügen einer Arbeitsmappe](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. Klicken Sie **Text hinzufügen**.

    ![Text hinzufügen](./media/tutorial-log-analytics-wizard/add-text.png)


7. Geben Sie im Textfeld `# Client apps used in the past week`ein, und klicken Sie dann auf **Bearbeitung abgeschlossen**.

    ![Arbeitsmappentext](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. Klicken Sie in der neuen Arbeitsmappe auf **Hinzufügen**, und klicken Sie dann auf **Abfrage hinzufügen**.

    ![Abfrage hinzufügen](./media/tutorial-log-analytics-wizard/add-query.png)

9. Geben Sie im Abfragetextfeld Folgendes ein: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. Klicken auf **Abfrage ausführen**.

    ![Screenshot: Schaltfläche „Abfrage ausführen“](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Klicken Sie in der Symbolleiste unter **Visualisierungs** auf **Kreisdiagramm**.

    ![Kreisdiagramm](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. Klicken Sie auf **Bearbeitung abgeschlossen**.

    ![Bearbeitung abgeschlossen](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Hinzufügen einer Abfrage zu einer Arbeitsmappenvorlage  

In diesem Verfahren wird gezeigt, wie einer vorhandenen Arbeitsmappenvorlage eine Abfrage hinzugefügt wird. Das Beispiel basiert auf einer Abfrage, die die Verteilung von Erfolgen und Fehlern für den bedingten Zugriff zeigt.


1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Azure Active Directory**.

    ![Screenshot: Azure Active Directory in der Azure-Suche](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Klicken Sie im Abschnitt **Überwachung** auf **Arbeitsmappen**.

    ![Screenshot: „Überwachung“ im Menü mit ausgewählter Option „Arbeitsmappen“](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Klicken Sie im Abschnitt **Bedingter Zugriff** auf **Erkenntnisse und Berichte zum bedingten Zugriff**.

    ![Screenshot: Option „Erkenntnisse und Berichte zum bedingten Zugriff“](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. Klicken Sie auf der Symbolleiste auf **Bearbeiten**.

    ![Screenshot: Schaltfläche „Bearbeiten“](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Klicken Sie in der Symbolleiste auf die drei Punkte, dann auf **Hinzufügen** und dann auf **Abfrage hinzufügen**.

    ![Arbeitsmappenabfrage hinzufügen](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. Geben Sie im Abfragetextfeld Folgendes ein: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. Klicken auf **Abfrage ausführen**.

    ![Screenshot: Schaltfläche „Abfrage ausführen“ zum Ausführen dieser Abfrage](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. Klicken Sie auf **Zeitbereich**, und wählen Sie dann **In Abfrage festlegen** aus.

10. Klicken Sie auf **Visualisierung**, und wählen Sie dann **Balkendiagramm** aus. 

11. Klicken Sie auf **Erweiterte Einstellungen**, geben Sie als Diagrammtitel `Conditional Access status over the last 20 days` ein, und klicken Sie dann auf **Bearbeitung abgeschlossen**. 

    ![Diagrammtitel festlegen](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie im nächsten Artikel, wie Sie Geräteidentitäten im Azure-Portal verwalten.
> [!div class="nextstepaction"]
> [Überwachung](overview-monitoring.md)