---
title: Azure-Protokollintegration – Häufig gestellte Fragen | Microsoft-Dokumentation
description: In diesem Artikel werden Fragen zur Azure-Protokollintegration beantwortet.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9f7d482b088003e3800debb2db9f6f26bda1672a
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298185"
---
# <a name="azure-log-integration-faq"></a>Azure-Protokollintegration – Häufig gestellte Fragen

In diesem Artikel werden häufig gestellte Fragen zur Azure-Protokollintegration beantwortet.

>[!IMPORTANT]
> Das Feature „Azure Log Integration“ wird ab dem 15.06.2019 als veraltet markiert. AzLog-Downloads wurden am 27. Juni 2018 deaktiviert. Um Unterstützung bei der künftigen Vorgehensweise zu erhalten, lesen Sie den Beitrag [Use Azure Monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Verwenden von Azure Monitor für die Integration mit SIEM-Tools). 

Die Azure-Protokollintegration ist ein Dienst des Windows-Betriebssystems, mit dem Sie nicht aufbereitete Protokolle aus Ihren Azure-Ressourcen in Ihre lokalen SIEM-Systeme (Security Information and Event Management, Sicherheitsinformationen und Ereignisverwaltung) integrieren können. Diese Integration bietet ein einheitliches Dashboard für alle Ihre Objekte, ob lokal oder in der Cloud. Sie können dann Sicherheitsereignisse für Ihre Anwendungen aggregieren, korrelieren, analysieren und entsprechende Warnungen ausgeben.

Die bevorzugte Methode für die Integration von Azure-Protokollen ist der Azure Monitor-Connector Ihres SIEM-Anbieters unter Verwendung dieser [Anweisungen](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Wenn jedoch der SIEM-Anbieter keinen Connector für Azure Monitor anbietet, können Sie Azure Log Integration möglicherweise als eine temporäre Lösung verwenden (sofern Ihr SIEM von Azure Log Integration unterstützt wird), bis ein solcher Connector verfügbar ist.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="is-the-azure-log-integration-software-free"></a>Ist die Integration der Software für die Azure-Protokollintegration kostenlos?

Ja. Es fallen keine Gebühren für die Integration der Software für die Azure-Protokollintegration an.

## <a name="where-is-azure-log-integration-available"></a>Wo ist die Azure-Protokollintegration erhältlich?

Derzeit ist sie im kommerziellen Azure sowie Azure Government erhältlich, in China und Deutschland ist sie nicht verfügbar.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Wie kann ich die Speicherkonten anzeigen, aus denen die Azure-Protokollintegration Azure-VM-Protokolle bezieht?

Führen Sie den Befehl **AzLog source list** aus.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Wie erkenne ich, aus welchem Abonnement die Protokolle der Azure-Protokollintegration stammen?

Bei Überwachungsprotokollen, die sich in den **AzureResourcemanagerJson**-Verzeichnissen befinden, ist die Abonnement-ID im Namen der Protokolldatei enthalten. Dies gilt auch für Protokolle im Ordner **AzureSecurityCenterJson**. Beispiel:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Bei Azure Active Directory-Überwachungsprotokollen ist die Mandanten-ID Teil des Namens.

Die Namen von Diagnoseprotokollen, die von einem Event-Hub gelesen werden, enthalten nicht die Abonnement-ID. Stattdessen enthalten sie den Anzeigenamen, der im Rahmen der Erstellung der Event Hub-Quelle angegeben wird. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Wie kann ich die Proxykonfiguration aktualisieren?

Falls Ihre Proxyeinstellung keinen direkten Azure-Speicherzugriff ermöglicht, öffnen Sie die Datei **AZLOG.EXE.CONFIG** (im Verzeichnis **C:\Programme\Microsoft Azure-Protokollintegration**). Fügen Sie in der Datei den Abschnitt **defaultProxy** mit der Proxyadresse Ihrer Organisation hinzu. Beenden Sie anschließend den Dienst mithilfe des Befehls **net stop AzLog**, und starten Sie ihn mithilfe des Befehls **net start AzLog** erneut.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress="http://127.0.0.1:8888"
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Wie kann ich die Abonnementinformationen in Windows-Ereignissen anzeigen?

Fügen Sie beim Hinzufügen der Quelle die Abonnement-ID an den Anzeigenamen an:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Der XML-Code des Ereignisses enthält die folgenden Metadaten – einschließlich der Abonnement-ID:

![Ereignis-XML][1]

## <a name="error-messages"></a>Fehlermeldungen
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Warum tritt beim Ausführen des Befehls ```AzLog createazureid``` der folgende Fehler auf?

Fehler

  *Failed to create AAD Application - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37 - Reason = 'Forbidden' - Message = 'Insufficient privileges to complete the operation.' (Fehler beim Erstellen der AAD-Anwendung. Mandant: 72f988bf-86f1-41af-91ab-2d7cd011db37. Ursache: Unzulässig. Meldung: Nicht genügend Berechtigungen zum Abschließen des Vorgangs.)*

Mit dem Befehl **azlog createazureid** wird versucht, in allen Azure AD-Mandanten für die Abonnements, auf die das angemeldete Azure-Konto Zugriff hat, einen Dienstprinzipal zu erstellen. Falls es sich bei Ihrer Azure-Anmeldung nur um einen Gastbenutzer des Azure AD-Mandanten handelt, tritt bei dem Befehl der Fehler mit dem Hinweis „Nicht genügend Berechtigungen zum Abschließen des Vorgangs“ auf. Bitten Sie den Mandantenadministrator, Ihr Konto als Benutzer hinzuzufügen.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Warum tritt beim Ausführen des Befehls **azlog authorize** der folgende Fehler auf?

Fehler

  *Warning creating Role Assignment - AuthorizationFailed: The client 'janedo\@microsoft.com' with object id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/write' over scope '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000'.* (Warnung beim Erstellen der Rollenzuweisung: Fehler bei der Autorisierung. Client „janedo@microsoft.com“ mit der Objekt-ID „xxx“ hat keine Berechtigung zum Ausführen der Aktion „Microsoft.Authorization/roleAssignments/write“ über Bereich „/subscriptions/70d95299-d689-4c97-b971-0d8ff000000“.)

Der Befehl **azlog authorize** weist dem mit **azlog createazureid** erstellten Azure AD-Dienstprinzipal für die angegebenen Abonnements die Rolle „Leser“ zu. Handelt es sich bei der Azure-Anmeldung nicht um einen Co-Administrator oder Besitzer des Abonnements, tritt ein Fehler mit einem Hinweis auf eine nicht erfolgreiche Autorisierung auf. Für diesen Vorgang wird die in Azure verfügbare rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) des Co-Administrators oder Besitzers benötigt.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Wo finde ich die Definition der Eigenschaften im Überwachungsprotokoll?

Siehe:

* [Überwachen von Vorgängen mit Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Auflisten der Verwaltungsereignisse in einem Abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Wo finde ich Details zu Azure Security Center-Warnungen?

[Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Wie kann ich ändern, was von der VM-Diagnose erfasst wird?

Ausführliche Informationen zum Abrufen, Ändern und Festlegen der Konfiguration der Azure-Diagnose finden Sie unter [Aktivieren der Azure-Diagnose auf einem virtuellen Azure-Computer unter Windows mithilfe von PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Im folgenden Beispiel wird die typische Konfiguration der Azure-Diagnose abgerufen:

    Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

Im folgenden Beispiel wird die Konfiguration der Azure-Diagnose geändert. In dieser Konfiguration werden nur die Ereignis-ID 4624 und Ereignis-ID 4625 aus dem Sicherheitsereignisprotokoll erfasst. Microsoft Antimalware für Azure-Ereignisse werden auf der Grundlage des Systemereignisprotokolls erfasst. Weitere Informationen zur Verwendung von XPath-Ausdrücken finden Sie unter [Consuming Events](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)) (Nutzen von Ereignissen, in englischer Sprache).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

Im folgenden Beispiel wird die typische Konfiguration der Azure-Diagnose festgelegt:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Vergewissern Sie sich nach dem Vornehmen von Änderungen im Speicherkonto, dass die richtigen Ereignisse erfasst werden.

Wenn während der Installation und Konfiguration Probleme auftreten, erstellen Sie eine [Supportanfrage](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Wählen Sie **Protokollintegration** als Dienst aus, für den Sie Support anfordern.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Kann ich mithilfe der Azure-Protokollintegration Network Watcher-Protokolle in mein SIEM integrieren?

Azure Network Watcher generiert große Mengen von Protokollinformationen. Diese Protokolle sind nicht zum Senden an ein SIEM vorgesehen. Als Ziel für Network Watcher-Protokolle wird ausschließlich ein Speicherkonto unterstützt. Das Lesen dieser Protokolle sowie das Verfügbarmachen für ein SIEM werden von der Azure-Protokollintegration nicht unterstützt.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
