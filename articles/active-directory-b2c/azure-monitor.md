---
title: Überwachen von Azure AD B2C mit Azure Monitor
titleSuffix: Azure AD B2C
description: Es wird beschrieben, wie Sie Azure AD B2C-Ereignisse mit Azure Monitor protokollieren, indem Sie die delegierte Ressourcenverwaltung verwenden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 01/29/2021
ms.openlocfilehash: e44a029c61db5a22513387772c2b0d7a3e4d1a40
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219229"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Überwachen von Azure AD B2C mit Azure Monitor

Verwenden Sie Azure Monitor, um Azure AD B2C-Protokolle (Azure Active Directory B2C) zur Anmeldung und [Überwachung](view-audit-logs.md) an verschiedene Überwachungslösungen zu leiten. Sie können die Protokolle entweder zur langfristigen Verwendung speichern oder in SIEM-Drittanbietertools (Security Information & Event Management) integrieren, um Einblicke in Ihre Umgebung zu gewinnen.

Sie können Protokollereignisse an folgende Komponenten weiterleiten:

* Ein Azure-[Speicherkonto](../storage/blobs/storage-blobs-introduction.md).
* Einen Azure [Log Analytics-Arbeitsbereich](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) (zum Analysieren von Daten, Erstellen von Dashboards und Warnen bei bestimmten Ereignissen).
* Einen Azure [Event Hub](../event-hubs/event-hubs-about.md) (mit Integration in Ihre Splunk- und Sumo Logic-Instanzen).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

In diesem Artikel erfahren Sie, wie Sie Protokolle in einen Azure Log Analytics-Arbeitsbereich übertragen. Anschließend können Sie ein Dashboard oder Warnungen erstellen, die auf Azure AD B2C-Benutzeraktivitäten basieren.

> [!IMPORTANT]
> Wenn Sie Azure AD B2C-Protokolle in andere Überwachungslösungen oder in ein Repository übertragen möchten, berücksichtigen Sie Folgendes: Azure AD B2C-Protokolle enthalten personenbezogene Daten. Diese Daten müssen mithilfe geeigneter technischer oder organisatorischer Maßnahmen so verarbeitet werden, dass die Sicherheit der personenbezogenen Daten gewährleistet ist – einschließlich Schutz vor nicht autorisierter oder gesetzeswidriger Verarbeitung.


## <a name="deployment-overview"></a>Übersicht über die Bereitstellung

Für Azure AD B2C wird die [Azure Active Directory-Überwachung](../active-directory/reports-monitoring/overview-monitoring.md) genutzt. Damit Sie in Ihrem Azure AD B2C-Mandanten die *Diagnoseeinstellungen* von Azure Active Directory aktivieren können, verwenden Sie [Azure Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md), um [eine Ressource zu delegieren](../lighthouse/concepts/azure-delegated-resource-management.md). Dadurch kann Azure AD B2C (**Dienstanbieter**) eine Azure AD-Ressource (**Kunde**) verwalten. Nachdem Sie die Schritte in diesem Artikel ausgeführt haben, haben Sie in Ihrem **Azure AD B2C**-Portal Zugriff auf die Ressourcengruppe *azure-ad-b2c-monitor*, die den [Log Analytics-Arbeitsbereich](../azure-monitor/learn/quick-create-workspace.md) enthält. Sie können die Protokolle auch von Azure AD B2C in den Log Analytics-Arbeitsbereich übertragen.

Bei der Bereitstellung autorisieren Sie in Ihrem Azure AD B2C-Verzeichnis einen Benutzer oder eine Gruppe zum Konfigurieren der Instanz des Log Analytics-Arbeitsbereichs in dem Mandanten, der Ihr Azure-Abonnement enthält. Zum Erstellen der Autorisierung stellen Sie eine [Azure Resource Manager](../azure-resource-manager/index.yml)-Vorlage auf Ihrem Azure AD-Mandanten bereit, der das Abonnement enthält.

Im folgenden Diagramm sind die Komponenten dargestellt, die Sie in Ihrem Azure AD- und Azure AD B2C-Mandanten konfigurieren.

![Ressourcengruppenprojektion](./media/azure-monitor/resource-group-projection.png)

Bei dieser Bereitstellung konfigurieren Sie sowohl Ihren Azure AD B2C-Mandanten als auch Ihren Azure AD-Mandanten, auf dem der Log Analytics-Arbeitsbereich gehostet wird. Dem Konto, mit dem die Bereitstellung ausgeführt wird, muss in beiden Mandanten die Rolle [Globaler Administrator](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) zugewiesen sein. Es ist auch wichtig sicherzustellen, dass Sie beim richtigen Verzeichnis angemeldet sind, wenn Sie die einzelnen Schritte wie beschrieben ausführen.

## <a name="1-create-or-choose-resource-group"></a>1. Erstellen oder Auswählen einer Ressourcengruppe

Erstellen Sie zuerst eine Ressourcengruppe (oder wählen Sie eine Ressourcengruppe aus), die den Log Analytics-Zielarbeitsbereich enthält, der Daten von Azure AD B2C empfangen soll. Den Namen der Ressourcengruppe geben Sie beim Bereitstellen der Azure Resource Manager-Vorlage an.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Portal auf der Symbolleiste das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren **Azure AD-Mandanten** enthält.
1. [Erstellen Sie eine Ressourcengruppe](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups), oder wählen Sie eine vorhandene Ressourcengruppe aus. In diesem Beispiel wird eine Ressourcengruppe namens *azure-ad-b2c-monitor* verwendet.

## <a name="2-create-a-log-analytics-workspace"></a>2. Erstellen eines Log Analytics-Arbeitsbereichs

Ein **Log Analytics-Arbeitsbereich** ist eine spezielle Umgebung für Azure Monitor-Protokolldaten. In diesem Log Analytics-Arbeitsbereich sammeln Sie Daten aus Azure AD B2C-[Überwachungsprotokollen](view-audit-logs.md), um sie anschließend mit Abfragen und Arbeitsmappen zu visualisieren oder Warnungen zu erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Portal auf der Symbolleiste das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren **Azure AD-Mandanten** enthält.
1. [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/learn/quick-create-workspace.md) In diesem Beispiel wird in der Ressourcengruppe namens *azure-ad-b2c-monitor* ein Log Analytics Arbeitsbereich namens *AzureAdB2C* verwendet.

## <a name="3-delegate-resource-management"></a>3. Delegieren der Ressourcenverwaltung

In diesem Schritt wählen Sie Ihren Azure AD B2C-Mandanten als **Dienstanbieter** aus. Außerdem definieren Sie die Autorisierungen, die Sie benötigen, um Gruppen in Ihrem Azure AD-Mandanten die entsprechenden integrierten Azure-Rollen zuzuweisen.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3.1 Abrufen der Azure AD B2C-Mandanten-ID

Rufen Sie zuerst die **Mandanten-ID** (auch Verzeichnis-ID genannt) Ihres Azure AD B2C-Verzeichnisses ab.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie im Portal auf der Symbolleiste das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren **Azure AD B2C**-Mandanten enthält.
1. Wählen Sie **Azure Active Directory** und dann **Übersicht** aus.
1. Notieren Sie sich die **Mandanten-ID**.

### <a name="32-select-a-security-group"></a>3.2 Auswählen einer Sicherheitsgruppe

Jetzt wählen Sie eine Azure AD B2C-Gruppe bzw. einen Benutzer aus, der oder dem Sie die Berechtigung für die Ressourcengruppe erteilen möchten, die Sie zuvor im Verzeichnis mit Ihrem Abonnement erstellt haben.  

Um die Verwaltung zu vereinfachen, empfiehlt es sich, für jede Rolle Azure AD-*Benutzergruppen* zu verwenden. Dies ermöglicht es Ihnen, der Gruppe einzelne Benutzer hinzuzufügen oder daraus zu entfernen, anstatt einem Benutzer Berechtigungen direkt zuzuweisen. In dieser exemplarischen Vorgehensweise wird eine Sicherheitsgruppe hinzugefügt.

> [!IMPORTANT]
> Um Berechtigungen für eine Azure AD-Gruppe hinzuzufügen, muss der **Gruppentyp** auf **Sicherheit** festgelegt werden. Diese Option wird bei der Erstellung der Gruppe ausgewählt. Weitere Informationen dazu finden Sie in [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Achten Sie darauf, dass im **Azure AD B2C**-Verzeichnis noch der Eintrag **Azure Active Directory** ausgewählt ist. Wählen Sie anschließend **Gruppen** und danach eine Gruppe aus. Wenn keine Gruppe vorhanden ist, erstellen Sie eine **Sicherheitsgruppe**, und fügen Sie dann Mitglieder hinzu. Weitere Informationen sind im Verfahren [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) beschrieben. 
1. Wählen Sie **Übersicht** aus, und notieren Sie sich die **Objekt-ID** der Gruppe.

### <a name="33-create-an-azure-resource-manager-template"></a>3.3 Erstellen einer Azure Resource Manager-Vorlage

Als Nächstes erstellen Sie eine Azure Resource Manager-Vorlage, die Azure AD B2C Zugriff auf die Azure AD-Ressourcengruppe gewährt, die Sie zuvor erstellt haben (z. B. *azure-ad-b2c-monitor*). Stellen Sie die Vorlage aus dem GitHub-Beispiel bereit, indem Sie die Schaltfläche **In Azure bereitstellen** verwenden. Dadurch wird das Azure-Portal geöffnet, und Sie können die Vorlage direkt im Portal konfigurieren und bereitstellen. Stellen Sie bei diesen Schritten sicher, dass Sie bei Ihrem Azure AD-Mandanten (nicht dem Azure AD B2C-Mandanten) angemeldet sind.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Portal auf der Symbolleiste das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren **Azure AD**-Mandanten enthält.
3. Verwenden Sie die Schaltfläche **In Azure bereitstellen**, um das Azure-Portal zu öffnen und die Vorlage direkt im Portal bereitzustellen. Weitere Informationen finden Sie unter [Erstellen einer Azure Resource Manager-Vorlage](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template).

   [![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Lighthouse-samples%2Fmaster%2Ftemplates%2Frg-delegated-resource-management%2FrgDelegatedResourceManagement.json)

5. Geben Sie auf der Seite **Benutzerdefinierte Bereitstellung** die folgenden Informationen ein:

   | Feld   | Definition |
   |---------|------------|
   | Subscription |  Wählen Sie das Verzeichnis aus, welches das Azure-Abonnement enthält, in dem die Ressourcengruppe *azure-ad-b2c-monitor* erstellt wurde. |
   | Region| Wählen Sie die Region aus, in der die Ressource bereitgestellt wird.  | 
   | MSP-Angebotsname| Ein Name, der diese Definition beschreibt. Beispiel: *Azure-AD-B2C-Monitoring*.  |
   | MSP-Angebotsbeschreibung| Eine kurze Beschreibung Ihres Angebots. Beispiel: *Ermöglicht Azure Monitor in Azure AD B2C*.|
   | Verwaltet durch Mandanten-ID| Die **Mandanten-ID** Ihres Azure AD B2C-Mandanten (auch als Verzeichnis-ID bezeichnet). |
   |Authorizations|Geben Sie ein JSON-Array von Objekten an, das die `principalId` und den `principalIdDisplayName` von Azure AD und die `roleDefinitionId` von Azure enthält. `principalId` ist die **Objekt-ID** der B2C-Gruppe bzw. des Benutzers, die oder der Zugriff auf die Ressourcen in diesem Azure-Abonnement haben soll. Geben Sie für diese exemplarische Vorgehensweise die Objekt-ID der Gruppe an, die Sie zuvor notiert haben. Verwenden Sie als `roleDefinitionId` den Wert der [integrierten Rolle](../role-based-access-control/built-in-roles.md) für die *Rolle „Mitwirkender“* : `b24988ac-6180-42a0-ab88-20f7382dd24c`.|
   | Ressourcengruppenname | Der Name der Ressourcengruppe, die Sie zuvor auf Ihrem Azure AD-Mandanten erstellt haben. Beispiel: *azure-ad-b2c-monitor*. |

   Im folgenden Beispiel wird ein Autorisierungs-Array mit einer Sicherheitsgruppe veranschaulicht.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

Nach der Bereitstellung der Vorlage kann es einige Minuten (in der Regel nicht länger als fünf Minuten) dauern, bis die Ressourcenprojektion abgeschlossen ist. Sie können die Bereitstellung in Ihrem Azure AD-Mandanten überprüfen und die Details der Ressourcenprojektion abrufen. Weitere Informationen finden Sie unter [Anzeigen und Verwalten von Dienstanbietern](../lighthouse/how-to/view-manage-service-providers.md).

## <a name="4-select-your-subscription"></a>4. Wählen Sie Ihr Abonnement aus.

Nachdem Sie die Vorlage bereitgestellt und einige Minuten auf den Abschluss der Ressourcenprojektion gewartet haben, können Sie mit den folgenden Schritten Ihr Abonnement Ihrem Azure AD B2C-Verzeichnis zuordnen.

1. Melden Sie sich vom Azure-Portal ab, wenn Sie aktuell angemeldet sind. (So können im nächsten Schritt Ihre Sitzungsanmeldeinformationen aktualisiert werden.)
2. Melden Sie sich mit Ihrem **Azure AD B2C**-Administratorkonto beim [Azure-Portal](https://portal.azure.com) an. Dieses Konto muss ein Mitglied der Sicherheitsgruppe sein, die Sie im Schritt [Delegieren der Ressourcenverwaltung](#3-delegate-resource-management) angegeben haben.
3. Wählen Sie auf der Symbolleiste im Portal das Symbol **Verzeichnis + Abonnement** aus.
4. Wählen Sie das Azure AD-Verzeichnis aus, welches das Azure-Abonnement und die erstellte Ressourcengruppe *azure-ad-b2c-monitor* enthält.

    ![Wechseln des Verzeichnisses](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Vergewissern Sie sich, dass Sie das richtige Verzeichnis und Abonnement ausgewählt haben. In diesem Beispiel werden alle Verzeichnisse und Abonnements ausgewählt.

    ![Auswahl aller Verzeichnisse im Verzeichnis- und Abonnementfilter](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. Konfigurieren von Diagnoseeinstellungen

Diagnoseeinstellungen definieren, wohin Protokolle und Metriken für eine Ressource gesendet werden sollen. Mögliche Ziele:

- [Azure-Speicherkonto](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)
- [Event Hubs](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)-Lösungen
- [Log Analytics-Arbeitsbereich](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

In diesem Beispiel verwenden Sie den Log Analytics-Arbeitsbereich, um ein Dashboard zu erstellen.

### <a name="51-create-diagnostic-settings"></a>5.1 Erstellen von Diagnoseeinstellungen

Jetzt können Sie im Azure-Portal [Diagnoseeinstellungen erstellen](../active-directory/reports-monitoring/overview-monitoring.md).

Konfigurieren Sie die Überwachungseinstellungen für Azure AD B2C-Aktivitätsprotokolle wie folgt:

1. Melden Sie sich mit Ihrem Azure AD B2C-Administratorkonto beim [Azure-Portal](https://portal.azure.com/) an. Dieses Konto muss ein Mitglied der Sicherheitsgruppe sein, die Sie im Schritt [Auswählen einer Sicherheitsgruppe](#32-select-a-security-group) angegeben haben.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie **Azure Active Directory** aus.
1. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.
1. Wenn Einstellungen für die Ressource vorhanden sind, wird eine Liste der bereits konfigurierten Einstellungen angezeigt. Wählen Sie entweder **Diagnoseeinstellung hinzufügen** aus, um eine neue Einstellung hinzuzufügen, oder wählen Sie **Bearbeiten** aus, um eine vorhandene Einstellung zu bearbeiten. Jede Einstellung kann höchstens einen der Zieltypen aufweisen.

    ![Bereich „Diagnoseeinstellungen“ im Azure-Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Wenn noch kein Name für die Einstellung vorhanden ist, geben Sie ihr einen Namen.
1. Aktivieren Sie das Kontrollkästchen für jedes Ziel, um die Protokolle zu senden. Wählen Sie **Konfigurieren** aus, um die **in der folgenden Tabelle beschriebenen** Einstellungen anzugeben.
1. Wählen Sie **An Log Analytics-Arbeitsbereich senden** und dann den **Namen des zuvor erstellten Arbeitsbereichs** (`AzureAdB2C`) aus.
1. Wählen Sie **AuditLogs** und **SignInLogs** aus.
1. Wählen Sie **Speichern** aus.

> [!NOTE]
> Es kann bis zu 15 Minuten dauern, bis ein Ereignis ausgegeben und [in einem Log Analytics-Arbeitsbereich angezeigt wird](../azure-monitor/platform/data-ingestion-time.md). Erfahren Sie außerdem mehr über [Wartezeiten bei der Active Directory-Berichterstellung](../active-directory/reports-monitoring/reference-reports-latencies.md), die sich auf die Veraltung von Daten auswirken können und eine wichtige Rolle bei der Berichterstellung spielen.

Wenn die Fehlermeldung „Um Diagnoseeinstellungen für die Verwendung von Azure Monitor für Ihr Azure AD B2C-Verzeichnis einzurichten, müssen Sie die delegierte Ressourcenverwaltung einrichten“, angezeigt wird, müssen Sie sicherstellen, dass Sie als Benutzer angemeldet sind, der Mitglied der [Sicherheitsgruppe](#32-select-a-security-group) ist, und [Ihr Abonnement ausgewählt haben](#4-select-your-subscription).

## <a name="6-visualize-your-data"></a>6. Visualisieren Ihrer Daten

Nun können Sie Ihren Log Analytics-Arbeitsbereich zum Visualisieren Ihrer Daten und zum Konfigurieren von Warnungen konfigurieren. Diese Konfigurationen können sowohl in Ihrem Azure AD-Mandanten als auch in Ihrem Azure AD B2C-Mandanten vorgenommen werden.

### <a name="61-create-a-query"></a>6.1 Erstellen einer Abfrage

Mithilfe von Protokollabfragen können Sie die Daten, die in Azure Monitor-Protokollen erfasst werden, in vollem Umfang nutzen. Eine leistungsstarke Abfragesprache ermöglicht es Ihnen, Daten aus mehreren Tabellen zusammenzufügen, größere Mengen an Daten zu aggregieren und komplexe Vorgänge mit möglichst wenig Code auszuführen. Beinahe jede Frage kann beantwortet und jede Analyse durchgeführt werden, solange unterstützende Daten erfasst wurden und Sie wissen, wie die richtige Abfrage erstellt werden muss. Weitere Informationen finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

1. Wählen Sie im **Log Analytics-Arbeitsbereich** die Option **Protokolle** aus.
1. Fügen Sie im Abfrage-Editor die folgende Abfrage in der [Kusto-Abfragesprache](/azure/data-explorer/kusto/query/) ein. Mit dieser Abfrage wird die Richtlinienverwendung in den letzten x Tagen nach Vorgang angezeigt. Der Standardzeitraum ist auf 90 Tage (90T) festgelegt. Beachten Sie, dass die Abfrage nur auf den Vorgang fokussiert ist, bei dem von der Richtlinie ein Token bzw. ein Code ausgegeben wird.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. Klicken Sie auf **Run** (Ausführen). Die Abfrageergebnisse werden unten im Bildschirm angezeigt.
1. Wenn Sie die Abfrage zur späteren Verwendung speichern möchten, wählen Sie **Speichern** aus.

   ![Log Analytics-Protokoll-Editor](./media/azure-monitor/query-policy-usage.png)

1. Geben Sie die folgenden Details an:

    - **Name**: Geben Sie den Namen Ihrer Abfrage ein.
    - **Speichern unter**: Wählen Sie `query` aus.
    - **Kategorie**: Wählen Sie `Log` aus.

1. Wählen Sie **Speichern** aus.

Sie können die Abfrage auch ändern, um die Daten mithilfe des [render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor)-Operators zu visualisieren.

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics-Protokoll-Editor – Kreisdiagramm](./media/azure-monitor/query-policy-usage-pie.png)

Weitere Beispiele finden Sie im [SIEM-GitHub-Repository](https://aka.ms/b2csiem) für Azure AD B2C.

### <a name="62-create-a-workbook"></a>6.2 Erstellen einer Arbeitsmappe

Arbeitsmappen bieten einen flexiblen Bereich für die Datenanalyse und die Erstellung umfassender visueller Berichte innerhalb des Azure-Portals. Mit ihnen können Sie mehrere Datenquellen in Azure nutzen und sie zu vereinheitlichten interaktiven Oberflächen kombinieren. Weitere Informationen finden Sie unter [Azure Monitor-Arbeitsmappen](../azure-monitor/platform/workbooks-overview.md).

Befolgen Sie die nachfolgenden Anweisungen, um mithilfe einer JSON-Katalogvorlage eine neue Arbeitsmappe zu erstellen. Diese Arbeitsmappe stellt für den Azure AD B2C-Mandanten ein Dashboard für **Benutzererkenntnisse** und **Authentifizierung** bereit.

1. Wählen Sie im **Log Analytics-Arbeitsbereich** die Option **Arbeitsmappen** aus.
1. Wählen Sie auf der Symbolleiste die Option **+ Neu** aus, um eine neue Arbeitsmappe zu erstellen.
1. Wählen Sie auf der Seite **Neue Arbeitsmappe** mithilfe der Option **</>** auf der Symbolleiste den **Erweiterten Editor** aus.

     ![Katalogvorlage](./media/azure-monitor/wrkb-adv-editor.png)

1. Wählen Sie **Katalogvorlage** aus.
1. Ersetzen Sie den JSON-Code in der **Katalogvorlage** durch den Inhalt aus der [grundlegenden Azure AD B2C-Arbeitsmappe](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json):
1. Wenden Sie mithilfe der Schaltfläche **Anwenden** die Vorlage an.
1. Wählen Sie auf der Symbolleiste die Schaltfläche **Bearbeitung abgeschlossen** aus, um die Bearbeitung der Arbeitsmappe abzuschließen.
1. Speichern Sie abschließend die Arbeitsmappe mithilfe der Schaltfläche **Speichern** auf der Symbolleiste.
1. Geben Sie einen **Titel** an, z. B. *Azure AD B2C-Dashboard*.
1. Wählen Sie **Speichern** aus.

    ![Speichern der Arbeitsmappe](./media/azure-monitor/wrkb-title.png)

In der Arbeitsmappe werden Berichte in Form eines Dashboards angezeigt.

![Erstes Dashboard in der Arbeitsmappe](./media/azure-monitor/wkrb-dashboard-1.png)

![Zweites Dashboard in der Arbeitsmappe](./media/azure-monitor/wrkb-dashboard-2.png)

![Drittes Dashboard in der Arbeitsmappe](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Erstellen von Warnungen

Warnungen werden von Warnungsregeln in Azure Monitor erstellt und können in regelmäßigen Abständen automatisch gespeicherte Abfragen oder benutzerdefinierte Protokollsuchen ausführen. Sie können Warnungen auf der Grundlage bestimmter Leistungsmetriken oder des Entstehens bestimmter Ereignisse, der Abwesenheit eines Ereignisses oder des Entstehens einer Anzahl von Ereignissen innerhalb eines bestimmten Zeitfensters erstellen. Warnungen können beispielsweise zur Benachrichtigung verwendet werden, wenn die durchschnittliche Anzahl von Anmeldungen einen bestimmten Schwellenwert überschreitet. Weitere Informationen finden Sie unter [Warnungen erstellen](../azure-monitor/learn/tutorial-response.md).


Verwenden Sie die folgenden Anweisungen, um eine neue Azure-Warnung zu erstellen, die eine [E-Mail-Benachrichtigung](../azure-monitor/platform/action-groups.md#configure-notifications) sendet, wenn die **Gesamtzahl der Anforderungen** im Vergleich zum vorherigen Zeitraum um 25 % zurückgegangen ist. Die Warnung wird alle fünf Minuten ausgeführt und sucht innerhalb des letzten 24-Stunden-Fensters nach einem derartigen Rückgang. Die Warnungen werden in der Kusto-Abfragesprache erstellt.


1. Wählen Sie im **Log Analytics-Arbeitsbereich** die Option **Protokolle** aus. 
1. Erstellen Sie mithilfe der folgenden Abfrage eine neue **Kusto-Abfrage**.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. Wählen Sie **Ausführen** aus, um die Abfrage zu testen. Die Ergebnisse sollten angezeigt werden, wenn die Gesamtzahl der Anforderungen innerhalb der letzten 24 Stunden um mindestens 25 % zurückgegangen ist.
1. Um eine auf der Grundlage der obigen Abfrage eine Warnungsregel zu erstellen, verwenden Sie die Option **+ Neue Warnungsregel** auf der Symbolleiste.
1. Wählen Sie auf der Seite **Warnungsregel erstellen** die Option **Bedingungsname** aus. 
1. Legen Sie auf der Seite **Signallogik konfigurieren** die folgenden Werte fest, und speichern Sie dann mit der Schaltfläche **Fertig** die Änderungen.
    * Warnungslogik: Legen Sie die **Anzahl der Ergebnisse** auf **größer als** **0** fest.
    * Auswertung basiert auf: Wählen Sie **1440** als Dauer (in Minuten) und **5** als Häufigkeit (in Minuten) aus. 

    ![Erstellen einer Warnungsregelbedingung](./media/azure-monitor/alert-create-rule-condition.png)

Navigieren Sie nach dem Erstellen der Warnung zum **Log Analytics-Arbeitsbereich**, und wählen Sie **Warnungen** aus. Auf dieser Seite werden alle Warnungen angezeigt, die innerhalb des Zeitbereichs ausgelöst wurden, der mit der Option **Zeitbereich** festgelegt wurde.  

### <a name="configure-action-groups"></a>Konfigurieren von Aktionsgruppen

Azure Monitor- und Service Health-Warnungen verwenden Aktionsgruppen, um Benutzer zu benachrichtigen, dass eine Warnung ausgelöst wurde. Sie können das Senden eines Sprachanrufs, einer SMS oder E-Mail aufnehmen oder verschiedene Arten von automatisierten Aktionen auslösen. Entsprechende Anleitungen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../azure-monitor/platform/action-groups.md).

Hier sehen Sie ein Beispiel für eine Warnung in Form einer E-Mail-Benachrichtigung. 

   ![E-Mail-Benachrichtigung](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Mehrere Mandanten

Zum Integrieren von Protokollen mehrerer Azure AD B2C Mandanten in denselben Log Analytics-Arbeitsbereich (bzw. Azure Storage-Konto oder Event Hub) benötigen Sie separate Bereitstellungen mit unterschiedlichen Werten für **MSP-Angebotsname**. Stellen Sie sicher, dass sich Ihr Log Analytics-Arbeitsbereich in derselben Ressourcengruppe befindet, die Sie in [Erstellen oder Auswählen einer Ressourcengruppe](#1-create-or-choose-resource-group) konfiguriert haben.

Wenn Sie mit mehreren Log Analytics-Arbeitsbereichen arbeiten, verwenden Sie [Arbeitsbereichsübergreifende Abfrage](../azure-monitor/log-query/cross-workspace-query.md), um Abfragen zu erstellen, die mehrere Arbeitsbereiche einschließen. Die folgende Abfrage führt beispielsweise zwei Überwachungsprotokolle von verschiedenen Mandanten zusammen, die auf derselben Kategorie basieren (z. B. Authentifizierung):

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>Ändern des Datenaufbewahrungszeitraums

Azure Monitor-Protokolle sind für die Skalierung und Unterstützung der täglichen Sammlung, Indizierung und Speicherung enormer Datenmengen aus beliebigen Quellen in Ihrem Unternehmen oder aus in Azure bereitgestellten Quellen konzipiert. Standardmäßig werden Protokolle 30 Tage lang aufbewahrt. Die Aufbewahrungsdauer kann jedoch auf bis zu zwei Jahre verlängert werden. Erfahren Sie mehr über das [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../azure-monitor/platform/manage-cost-storage.md). Nachdem Sie den Tarif ausgewählt haben, können Sie den [Datenaufbewahrungszeitraum ändern](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Beispiele finden Sie im [SIEM-Katalog](https://aka.ms/b2csiem) von Azure AD B2C. 

* Weitere Informationen zum Hinzufügen und Konfigurieren von Diagnoseeinstellungen in Azure Monitor finden Sie im [Tutorial: Sammeln und Analysieren von Ressourcenprotokollen von einer Azure-Ressource](../azure-monitor/insights/monitor-azure-resource.md).

* Informationen zum Streamen von Azure AD-Protokollen an einen Event Hub finden Sie im [Tutorial: Streamen von Azure Active Directory-Protokollen an einen Azure Event Hub](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
