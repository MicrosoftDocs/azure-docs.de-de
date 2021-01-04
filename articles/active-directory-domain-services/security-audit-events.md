---
title: Aktivieren von Sicherheitsüberwachungen für Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Sicherheitsüberwachungen aktivieren, um die Protokollierung von Ereignissen für Analyse und Warnungen in Azure AD Domain Services zu zentralisieren.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 13bdc8797af8facaa73d3e43ecfbe504a6bd1dc2
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618874"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Aktivieren von Sicherheitsüberwachungen für Azure AD Domain Services

Sicherheitsüberwachungen für Azure Active Directory Domain Services (Azure AD DS) ermöglichen Azure das Streamen von Sicherheitsereignissen an Zielressourcen. Zu diesen Ressourcen zählen Azure Storage, Azure Log Analytics-Arbeitsbereiche oder Azure Event Hub. Nach dem Aktivieren von Sicherheitsüberwachungsereignissen sendet Azure AD DS alle überwachten Ereignisse für die ausgewählte Kategorie an die Zielressource.

Sie können Ereignisse in Azure Storage archivieren und mit Azure Event Hubs Ereignisse an SIEM-Software (Security Information & Event Management) oder eine vergleichbare Lösung streamen oder über das Azure-Portal mithilfe von Azure Log Analytics-Arbeitsbereichen eigene Analysen durchführen.

> [!IMPORTANT]
> Azure AD DS-Sicherheitsüberwachungen sind nur für verwaltete Domänen verfügbar, die auf Azure Resource Manager basieren. Informationen zur Migration finden Sie unter [Migrieren von Azure AD DS vom klassischen VNET-Modell zu Resource Manager][migrate-azure-adds].

## <a name="security-audit-destinations"></a>Ziele der Sicherheitsüberwachung

Für die Azure AD DS-Sicherheitsüberwachungen können Sie Azure Storage, Azure Event Hubs oder Azure Log Analytics-Arbeitsbereiche als Zielressource verwenden. Diese Ziele können kombiniert werden. Sie können z. B. Azure Storage zum Archivieren von Sicherheitsüberwachungsereignissen, einen Azure Log Analytics-Arbeitsbereich jedoch zum kurzfristigen Analysieren und Melden der Informationen verwenden.

In der folgenden Tabelle sind die Szenarien für die einzelnen Zielressourcentypen dargestellt.

> [!IMPORTANT]
> Die Zielressource muss vor der Aktivierung der Sicherheitsüberwachungen von Azure AD DS erstellt werden. Sie können diese Ressourcen über das Azure-Portal, mit Azure PowerShell oder mithilfe der Azure CLI erstellen.

| Zielressource | Szenario |
|:---|:---|
|Azure Storage| Dieses Ziel sollte verwendet werden, wenn Sie in erster Linie Sicherheitsüberwachungsereignisse zu Archivierungszwecken speichern möchten. Andere Ziele können zwar ebenfalls zu Archivierungszwecken verwendet werden, doch bieten diese Ziele Funktionen, die über die reine Archivierung hinausgehen. <br /><br />Bevor Sie Azure AD DS-Sicherheitsüberwachungsereignisse aktivieren, müssen Sie zunächst ein [Azure Storage-Konto erstellen](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Dieses Ziel sollte verwendet werden, wenn Sie in erster Linie Sicherheitsüberwachungsereignisse an Zusatzsoftware (z.B. SIEM-Software (Security Information & Event Management) oder Datenanalysesoftware) weitergeben möchten.<br /><br />Bevor Sie Azure AD DS-Sicherheitsüberwachungsereignisse aktivieren, müssen Sie [im Azure Portal einen Event Hub erstellen](../event-hubs/event-hubs-create.md).|
|Azure Log Analytics-Arbeitsbereich| Dieses Ziel sollte verwendet werden, wenn Sie in erster Linie Sicherheitsüberwachungen direkt über das Azure-Portal analysieren und überprüfen möchten.<br /><br />Bevor Sie Azure AD DS-Sicherheitsüberwachungsereignisse aktivieren, müssen Sie [im Azure-Portal einen Log Analytics-Arbeitsbereich erstellen](../azure-monitor/learn/quick-create-workspace.md).|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Aktivieren von Sicherheitsüberwachungsereignissen über das Azure-Portal

Führen Sie die folgenden Schritte aus, um Azure AD DS-Sicherheitsüberwachungsereignisse über das Azure-Portal zu aktivieren.

> [!IMPORTANT]
> Azure AD DS-Sicherheitsüberwachungen gelten nicht rückwirkend. Ereignisse aus der Vergangenheit können nicht abgerufen oder wiedergegeben werden. Azure AD DS kann nur Ereignisse senden, die nach der Aktivierung von Sicherheitsüberwachungen auftreten.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Suchen Sie oben im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie im Fenster „Azure AD DS“ auf der linken Seite **Diagnoseeinstellungen** aus.
1. Standardmäßig ist keine Diagnoseeinstellung konfiguriert. Wählen Sie als erstes **Diagnoseeinstellung hinzufügen** aus.

    ![Hinzufügen einer Diagnoseeinstellung für Azure AD Domain Services](./media/security-audit-events/add-diagnostic-settings.png)

1. Geben Sie einen Namen für die Diagnosekonfiguration ein, z.B. *aadds-auditing*.

    Aktivieren Sie das Kontrollkästchen für das gewünschte Ziel der Sicherheitsüberwachung. Zur Auswahl stehen Azure Storage-Konto, Azure Event Hub oder Log Analytics-Arbeitsbereich. Diese Zielressourcen müssen bereits in Ihrem Azure-Abonnement vorhanden sein. In diesem Assistenten können die Zielressourcen nicht erstellt werden.

    ![Aktivieren des gewünschten Ziels und des Typs der zu erfassenden Überwachungsereignisse](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure Storage**
        * Wählen Sie **In einem Speicherkonto archivieren** und dann **Konfigurieren** aus.
        * Wählen Sie das **Abonnement** und dann das **Speicherkonto** aus, das Sie zum Archivieren von Sicherheitsüberwachungsereignissen verwenden möchten.
        * Wählen Sie abschließend **OK** aus.
    * **Azure Event Hubs**
        * Wählen Sie **An einen Event Hub streamen** und dann **Konfigurieren** aus.
        * Wählen Sie das **Abonnement** und dann den **Event Hub-Namespace** aus. Geben Sie bei Bedarf auch unter **Event Hub-Name** und **Event Hub-Richtlinienname** einen Namen ein.
        * Wählen Sie abschließend **OK** aus.
    * **Azure Log Analytics-Arbeitsbereiche**
        * Wählen Sie **An Log Analytics senden** aus, und wählen Sie dann das **Abonnement**  und den **Log Analytics Arbeitsbereich** aus, den Sie zum Speichern von Sicherheitsüberwachungsereignissen verwenden möchten.

1. Wählen Sie die Protokollkategorien aus, die für die betreffende Zielressource einbezogen werden sollen. Wenn Sie die Überwachungsereignisse an ein Azure Storage-Konto senden, können Sie auch eine Aufbewahrungsrichtlinie mit der Anzahl der Tage für die Aufbewahrung der Daten konfigurieren. Mit der Standardeinstellung *0* werden alle Daten aufbewahrt, und die Ereignisse werden nach einem bestimmten Zeitraum nicht rotiert.

    Innerhalb einer einzelnen Konfiguration können für jede Zielressource verschiedene Protokollkategorien ausgewählt werden. Dadurch können Sie beispielsweise auswählen, welche Protokollkategorien für Log Analytics aufbewahrt und welche archiviert werden sollen.

1. Wenn Sie fertig sind, wählen Sie **Speichern** aus, um die Änderungen zu übergeben. Unmittelbar nach dem Speichern der Konfiguration beginnen die Zielressourcen, Azure AD DS-Sicherheitsüberwachungsereignisse zu empfangen.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Aktivieren von Sicherheitsüberwachungsereignissen mit Azure PowerShell

Führen Sie die folgenden Schritte aus, um Azure AD DS-Sicherheitsüberwachungsereignisse mit Azure PowerShell zu aktivieren. Führen Sie bei Bedarf zuerst die unter [Installieren des Azure PowerShell-Moduls und Verbinden mit Ihrem Azure-Abonnement](/powershell/azure/install-az-ps) beschriebenen Schritte aus.

> [!IMPORTANT]
> Azure AD DS-Sicherheitsüberwachungen gelten nicht rückwirkend. Ereignisse aus der Vergangenheit können nicht abgerufen oder wiedergegeben werden. Azure AD DS kann nur Ereignisse senden, die nach der Aktivierung von Sicherheitsüberwachungen auftreten.

1. Authentifizieren Sie sich mit dem Cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Ihrem Azure-Abonnement. Geben Sie Ihre Azure-Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Erstellen Sie die Zielressource für die Sicherheitsüberwachungsereignisse.

    * **Azure Storage** - [Erstellen eines Speicherkontos mit Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell).
    * **Azure Event Hubs** - [Erstellen eines Event Hubs mit Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Gegebenenfalls müssen Sie auch mit dem Cmdlet [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) eine Autorisierungsregel erstellen, um dem *Namespace* des Event Hubs Azure AD DS-Berechtigungen zu gewähren. Die Autorisierungsregel muss die Rechte **Verwalten**, **Lauschen** und **Senden** enthalten.

        > [!IMPORTANT]
        > Stellen Sie sicher, dass Sie die Autorisierungsregel für den Namespace des Event Hubs und nicht für den Event Hub selbst festlegen.

    * **Azure Log Analytics-Arbeitsbereiche** - [Erstellen eines Log Analytics-Arbeitsbereichs mit Azure PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

1. Rufen Sie mit dem Cmdlet [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) die Ressourcen-ID für Ihre verwaltete Azure AD DS-Domäne ab. Erstellen Sie eine Variable mit dem Namen *$aadds.ResourceId* für den Wert:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Konfigurieren Sie mit dem Cmdlet [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) die Azure-Diagnoseeinstellungen, um die Zielressource für Sicherheitsüberwachungsereignisse von Azure AD Domain Services zu verwenden. In den folgenden Beispielen wird die Variable *$aadds.ResourceId* aus dem vorherigen Schritt verwendet.

    * **Azure Storage** – Ersetzen Sie *storageAccountId* durch den Namen Ihres Speicherkontos:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure Event Hubs** – Ersetzen Sie *eventHubName* durch den Namen Ihres Event Hubs und *eventHubRuleId* durch die ID Ihrer Autorisierungsregel:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure Log Analytics-Arbeitsbereich** – Ersetzen Sie *workspaceId* durch die ID des Log Analytics-Arbeitsbereichs:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Abfragen und Anzeigen von Sicherheitsüberwachungsereignissen mit Azure Monitor

Log Analytics-Arbeitsbereiche ermöglichen das Anzeigen und Analysieren der Sicherheitsüberwachungsereignisse mithilfe von Azure Monitor und der Kusto-Abfragesprache. Diese Abfragesprache ist für die schreibgeschützte Verwendung konzipiert und bietet leistungsstarke Analysefunktionen mit einer leicht verständlichen Syntax. Weitere Informationen zu den ersten Schritten mit Kusto-Abfragesprachen finden Sie in den folgenden Artikeln:

* [Azure Monitor-Dokumentation](../azure-monitor/index.yml)
* [Erste Schritte mit Log Analytics in Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md)
* [Erste Schritte mit Protokollabfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Erstellen und Freigeben von Dashboards von Log Analytics-Daten](../azure-monitor/learn/tutorial-logs-dashboards.md)

Die folgenden Beispielabfragen können zum Starten der Analyse von Sicherheitsüberwachungsereignissen von Azure AD DS verwendet werden.

### <a name="sample-query-1"></a>Beispielabfrage 1

Alle Kontosperrungsereignisse der letzten sieben Tage anzeigen:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Beispielabfrage 2

Alle Kontosperrungsereignisse (*4740*) zwischen dem 3. Juni 2020, 9 Uhr, und dem 10. Juni 2020 um Mitternacht aufsteigend nach Datum und Uhrzeit anzeigen:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Beispielabfrage 3

Kontoanmeldeereignisse der letzten sieben Tage (von heute) für das Konto „user“ anzeigen:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Beispielabfrage 4

Kontoanmeldeereignisse der letzten sieben Tage (von heute) für das Konto „user“ anzeigen, bei denen ein falsches Kennwort verwendet wurde (*0xC0000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Beispielabfrage 5

Kontoanmeldeereignisse der letzten sieben Tage (von heute) für das Konto „user“ anzeigen, bei denen versucht wurde, bei gesperrtem Konto eine Anmeldung durchzuführen (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Beispielabfrage 6

Die Anzahl von Kontoanmeldeereignissen der letzten sieben Tage (von heute) für alle Anmeldeversuche aller gesperrten Benutzer anzeigen:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Kategorien für Überwachungsereignisse

Azure AD DS-Sicherheitsüberwachungen sind auf die herkömmliche Überwachung für herkömmliche AD DS-Domänencontroller abgestimmt. In Hybridumgebungen können Sie vorhandene Überwachungsmuster wiederverwenden, damit bei der Ereignisanalyse die gleiche Logik verwendet werden kann. Abhängig von dem zu analysierenden Szenario oder dem Szenario, in dem Sie eine Problembehandlung durchführen müssen, sind die verschiedenen Überwachungsereigniskategorien gezielt einzusetzen.

Die folgenden Überwachungsereigniskategorien sind verfügbar:

| Name der Überwachungskategorie | BESCHREIBUNG |
|:---|:---|
| Kontoanmeldung|Dient zur Überwachung von Versuchen, Kontodaten auf einem Domänencontroller oder in einer lokalen Sicherheitskontenverwaltung (Security Accounts Manager, SAM) zu authentifizieren.</p>Versuche, auf einen bestimmten Computer zuzugreifen, werden anhand von An- und Abmelderichtlinieneinstellungen und entsprechenden Ereignissen nachverfolgt. Bei den Einstellungen und Ereignissen in dieser Kategorie steht die verwendete Kontodatenbank im Mittelpunkt. Diese Kategorie umfasst folgende Unterkategorien:<ul><li>[Überprüfung der Anmeldeinformationen überwachen](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos-Authentifizierungsdienst überwachen](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Ticketvorgänge des Kerberos-Diensts überwachen](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Andere Anmelde-/Abmeldeereignisse überwachen](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Kontoverwaltung|Dient zur Überwachung von Änderungen an Benutzer- und Computerkonten/-gruppen. Diese Kategorie umfasst folgende Unterkategorien:<ul><li>[Anwendungsgruppenverwaltung überwachen](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Computerkontoverwaltung überwachen](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Verteilergruppenverwaltung überwachen](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Andere Kontoverwaltungsereignisse überwachen](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Sicherheitsgruppenverwaltung überwachen](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Benutzerkontenverwaltung überwachen](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Detaillierte Nachverfolgung|Dient zur Überwachung der Aktivitäten einzelner Anwendungen und Benutzer auf dem Computer, um die Nutzung eines Computers nachzuvollziehen. Diese Kategorie umfasst folgende Unterkategorien:<ul><li>[DPAPI-Aktivität überwachen](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP-Aktivität überwachen](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Prozesserstellung überwachen](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Prozessbeendung überwachen](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC-Ereignisse überwachen](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Directory Services-Zugriff|Dient zur Überwachung von Zugriffs- und Änderungsversuchen für Objekte in Active Directory Domain Services (AD DS). Diese Überwachungsereignisse werden nur auf Domänencontrollern protokolliert. Diese Kategorie umfasst folgende Unterkategorien:<ul><li>[Detaillierte Verzeichnisdienstreplikation überwachen](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Verzeichnisdienstzugriff überwachen](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Verzeichnisdienständerungen überwachen](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Verzeichnisdienstreplikation überwachen](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Anmelden/Abmelden|Dient zur Überwachung von Anmeldeversuchen bei einem Computer (interaktiv oder über ein Netzwerk). Diese Ereignisse ermöglichen die Nachverfolgung von Benutzeraktivitäten sowie die Erkennung potenzieller Angriffe auf Netzwerkressourcen. Diese Kategorie umfasst folgende Unterkategorien:<ul><li>[Kontosperrung überwachen](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Benutzer-/Geräteansprüche überwachen](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec-Erweiterungsmodus überwachen](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Mitgliedschaft in der Überwachungsgruppe](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec-Hauptmodus überwachen](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec-Schnellmodus überwachen](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Abmelden überwachen](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Anmelden überwachen](/windows/security/threat-protection/auditing/audit-logon)</li><li>[Netzwerkrichtlinienserver überwachen](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Andere Anmelde-/Abmeldeereignisse überwachen](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Spezielle Anmeldung überwachen](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Objektzugriff| Dient zur Überwachung von Zugriffsversuchen auf bestimmte Objekte oder Objekttypen in einem Netzwerk oder auf einem Computer. Diese Kategorie umfasst folgende Unterkategorien:<ul><li>[Anwendung generiert überwachen](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Zertifizierungsdienste überwachen](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Detaillierte Dateifreigabe überwachen](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Dateifreigabe überwachen](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Dateisystem überwachen](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Filterplattformverbindung überwachen](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Filterplattform: Verworfene Pakete überwachen](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Handleänderung überwachen](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Kernelobjekt überwachen](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Andere Objektzugriffsereignisse überwachen](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registrierung überwachen](/windows/security/threat-protection/auditing/audit-registry)</li><li>[Wechselmedien überwachen](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM überwachen](/windows/security/threat-protection/auditing/audit-sam)</li><li>[Staging zentraler Zugriffsrichtlinien überwachen](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Richtlinienänderung|Dient zur Überwachung von Änderungen an wichtigen Sicherheitsrichtlinien auf einem lokalen System oder in einem lokalen Netzwerk. Richtlinien werden in der Regel von Administratoren eingerichtet, um Netzwerkressourcen zu schützen. Die Überwachung von Änderungen an diesen Richtlinien bzw. von entsprechenden Änderungsversuchen kann ein wichtiger Aspekt der Sicherheitsverwaltung für ein Netzwerk sein. Diese Kategorie umfasst folgende Unterkategorien:<ul><li>[Richtlinienänderungen überwachen](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Authentifizierungsrichtlinienänderung überwachen](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Autorisierungsrichtlinienänderung überwachen](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Filterplattform-Richtlinienänderung überwachen](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC-Richtlinienänderung auf Regelebene überwachen](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Andere Richtlinienänderungsereignisse überwachen](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Berechtigungen| Dient zur Überwachung der Verwendung bestimmter Berechtigungen auf einem oder mehreren Systemen. Diese Kategorie umfasst folgende Unterkategorien:<ul><li>[Nicht sensible Verwendung von Rechten überwachen](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Sensible Verwendung von Rechten überwachen](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Andere Rechteverwendungsereignisse überwachen](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|System| Dient zur Überwachung von Änderungen auf der Systemebene eines Computers, die nicht durch die anderen Kategorien abgedeckt sind und potenzielle Auswirkungen auf die Sicherheit haben. Diese Kategorie umfasst folgende Unterkategorien:<ul><li>[IPsec-Treiber überwachen](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Andere Systemereignisse überwachen](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Sicherheitsstatusänderung überwachen](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Sicherheitssystemerweiterung überwachen](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Systemintegrität überwachen](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Ereignis-IDs nach Kategorie

 Die Sicherheitsüberwachungen von Azure AD DS zeichnen die folgenden Ereignis-IDs auf, wenn die jeweilige Aktion ein überwachbares Ereignis auslöst:

| Name der Ereigniskategorie | Ereignis-IDs |
|:---|:---|
|Kontoanmeldung|4767, 4774, 4775, 4776, 4777|
|Kontoverwaltung|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Detaillierte Nachverfolgung|Keine|
|DS-Zugriff|5136, 5137, 5138, 5139, 5141|
|Anmelden/Abmelden|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Objektzugriff|Keine|
|Richtlinienänderung|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Berechtigungen|4985|
|System|4612, 4621|

## <a name="next-steps"></a>Nächste Schritte

Spezielle Informationen zu Kusto finden Sie in den folgenden Artikeln:

* [Übersicht](/azure/kusto/query/) über die Kusto-Abfragesprache
* [Kusto-Tutorial](/azure/kusto/query/tutorial) zur Vermittlung von Abfragegrundlagen
* [Beispielabfragen](/azure/kusto/query/samples) zur Vermittlung neuer Sichtweisen auf Ihre Daten
* [Bewährte Methoden](/azure/kusto/query/best-practices) für Kusto zur Optimierung Ihrer Abfragen

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md