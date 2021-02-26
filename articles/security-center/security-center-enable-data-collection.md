---
title: Automatische Bereitstellung von Agents für Azure Security Center | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die automatische Bereitstellung des Log Analytics-Agents und anderer von Azure Security Center verwendeter Agents einrichten.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 8fa2a06b1310e7cd825c918e92ea7af9b9b488de
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596166"
---
# <a name="auto-provisioning-agents-and-extensions-from-azure-security-center"></a>Automatische Bereitstellung von Agents und Erweiterungen aus Azure Security Center

Security Center sammelt Daten von Ihren Azure-VMs, VM-Skalierungsgruppen, IaaS-Containern und Nicht-Azure-Computern (auch lokal), um sie auf Sicherheitsrisiken und Bedrohungen zu überwachen. 

Die Datensammlung ist erforderlich, um einen Einblick in fehlende Updates, falsch konfigurierte Sicherheitseinstellungen des Betriebssystems, den Status des Endpunktschutzes sowie Integritäts- und Bedrohungsschutz bereitzustellen. Die Datensammlung ist nur für Computeressourcen (virtuelle Computer, VM-Skalierungsgruppen, IaaS-Container und Azure-fremde Computer) erforderlich. Sie können auch von Azure Security Center profitieren, wenn Sie keine Agents bereitstellen. Allerdings ist dann die Sicherheit eingeschränkt, und die oben aufgeführten Funktionen werden nicht unterstützt.  

Daten werden wie folgt gesammelt:

- Mit dem **Log Analytics-Agent**, der verschiedene sicherheitsrelevante Konfigurationen und Ereignisprotokolle auf dem Computer liest und die Daten zur Analyse in Ihren Arbeitsbereich kopiert. Beispiele für Daten dieser Art: Betriebssystemtyp und -version, Betriebssystemprotokolle (Windows-Ereignisprotokolle), ausgeführte Prozesse, Computername, IP-Adressen und angemeldeter Benutzer.
- Mit **Sicherheitserweiterungen** wie dem [Azure Policy-Add-On für Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), die auch Daten zu speziellen Ressourcentypen für Security Center bereitstellen können.

> [!TIP]
> Mit dem Umfang von Security Center hat auch die Anzahl von Ressourcentypen zugenommen, die überwacht werden können. Zudem sind mehr Erweiterungen verfügbar. Die automatische Bereitstellung wurde erweitert, um durch die Nutzung der Funktionen von Azure Policy zusätzliche Ressourcentypen zu unterstützen.

:::image type="content" source="./media/security-center-enable-data-collection/auto-provisioning-options.png" alt-text="Seite mit den Einstellungen für die automatische Bereitstellung in Security Center":::


## <a name="availability"></a>Verfügbarkeit

| Aspekt                  | Details                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status des Release:          | **Feature**: Die automatische Bereitstellung ist allgemein verfügbar (GA).<br>**Agent und Erweiterungen:** Der Log Analytics-Agent für virtuelle Azure-Computer ist allgemein verfügbar, der Microsoft Dependency-Agent befindet sich in der Vorschauversion, das Richtlinien-Add-On für Kubernetes ist allgemein verfügbar.                |
| Preise:                | Kostenlos                                                                                                                                                                                                                         |
| Unterstützte Ziele: | ![Ja](./media/icons/yes-icon.png) Azure-Computer<br>![Nein](./media/icons/no-icon.png) Azure Arc-Computer<br>![Nein](./media/icons/no-icon.png) Kubernetes-Knoten<br>![Nein](./media/icons/no-icon.png) Virtual Machine Scale Sets |
| Clouds:                 | ![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Ja](./media/icons/yes-icon.png) US Gov, China Gov, andere Gov-Clouds                                                                                                      |
|                         |                                                                                                                                                                                                                              |


## <a name="why-use-auto-provisioning"></a>Gründe für die Verwendung der automatischen Bereitstellung
Alle Agents und Erweiterungen, die auf dieser Seite beschrieben werden, *können* manuell installiert werden (siehe [Manuelle Installation des Log Analytics-Agents](#manual-agent)). Die **automatische Bereitstellung** verringert jedoch den Verwaltungsaufwand, indem alle erforderlichen Agents und Erweiterungen auf vorhandenen – und neuen – Computern installiert werden, um eine schnellere Sicherheitsabdeckung für alle unterstützten Ressourcen sicherzustellen. 

Wir empfehlen, die automatische Bereitstellung zu aktivieren, sie ist jedoch standardmäßig deaktiviert.

## <a name="how-does-auto-provisioning-work"></a>Funktionsweise der automatischen Bereitstellung
Die Einstellungen für die automatische Bereitstellung von Security Center enthalten eine Umschaltfläche für jeden unterstützten Erweiterungstyp. Wenn Sie die automatische Bereitstellung einer Erweiterung aktivieren, weisen Sie die entsprechende **Richtlinie für Bereitstellung, falls nicht vorhanden** zu, um sicherzustellen, dass die Erweiterung auf allen vorhandenen und zukünftigen Ressourcen dieses Typs bereitgestellt wird.

> [!TIP]
> Weitere Informationen zu Azure Policy-Auswirkungen, einschließlich „DeployIfNotExists“ (Bereitstellung, falls nicht vorhanden), finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md).

## <a name="enable-auto-provisioning-of-the-log-analytics-agent"></a>Aktivieren der automatischen Bereitstellung des Log Analytics-Agents <a name="auto-provision-mma"></a>
Wenn die automatische Bereitstellung für den Log Analytics-Agent aktiviert ist, stellt Security Center den Agent auf allen unterstützten und neu erstellten Azure-VMs bereit. Die Liste der unterstützten Plattformen finden Sie unter [Unterstützte Plattformen in Azure Security Center](security-center-os-coverage.md).

So aktivieren Sie die automatische Bereitstellung des Log Analytics-Agents:

1. Wählen Sie im Menü von Security Center **Preise und Einstellungen** aus.
1. Wählen Sie das relevante Abonnement aus.
1. Legen Sie auf der Seite **Automatische Bereitstellung** den Status des Agents auf **Ein** fest.
1. Definieren Sie im Bereich mit den Konfigurationsoptionen den zu verwendenden Arbeitsbereich.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Konfigurationsoptionen für die automatische Bereitstellung von Log Analytics-Agents auf VMs" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Azure-VMs mit den von Security Center erstellten Standardarbeitsbereichen verbinden**: Security Center erstellt eine neue Ressourcengruppe und einen Standardarbeitsbereich am selben geografischen Standort und verbindet den Agent mit diesem Arbeitsbereich. Wenn ein Abonnement VMs an mehreren geografischen Standorten enthält, erstellt Security Center mehrere Arbeitsbereiche, um die Einhaltung der Datenschutzanforderungen sicherzustellen.

        Für den Arbeitsbereich und die Ressourcengruppe gilt folgende Namenskonvention: 
        - Arbeitsbereich: DefaultWorkspace-[Abonnement-ID]-[Region] 
        - Ressourcengruppe: DefaultResourceGroup-[Region] 

        Security Center aktiviert gemäß dem für das Abonnement festgelegten Tarif automatisch eine Security Center-Lösung im Arbeitsbereich. 

        > [!TIP]
        > Falls Sie Fragen zu Standardarbeitsbereichen haben, finden Sie hier weitere Informationen:
        >
        > - [Werden mir die Azure Monitor-Protokolle auf den von Security Center erstellten Arbeitsbereiche in Rechnung gestellt?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Wo wird der standardmäßige Log Analytics-Arbeitsbereich erstellt?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Kann ich die von Security Center erstellten Standardarbeitsbereiche löschen?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Azure-VMs mit einem anderen Arbeitsbereich verbinden**: Wählen Sie in der Dropdownliste den Arbeitsbereich aus, in dem die gesammelten Daten gespeichert werden sollen. Die Dropdownliste enthält sämtliche Arbeitsbereiche für alle Ihrer Abonnements. Sie können diese Option verwenden, um Daten von VMs zu sammeln, die in anderen Abonnements ausgeführt werden, und sie im ausgewählten Arbeitsbereich zu speichern.  

        Wenn Sie bereits über einen vorhandenen Log Analytics-Arbeitsbereich verfügen, können Sie diesen verwenden (erfordert Lese- und Schreibberechtigungen für den Arbeitsbereich). Diese Option ist hilfreich, wenn Sie einen zentralen Arbeitsbereich in Ihrer Organisation verwenden und diesen für die Sammlung von Sicherheitsdaten verwenden möchten. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](../azure-monitor/logs/manage-access.md).

        Wenn für den ausgewählten Arbeitsbereich bereits eine Security- oder SecurityCenterFree-Lösung aktiviert ist, werden die Preise automatisch festgelegt. Installieren Sie andernfalls eine Security Center-Lösung im Arbeitsbereich:

        1. Öffnen Sie im Menü von Security Center **Preise und Einstellungen**.
        1. Wählen Sie den Arbeitsbereich aus, mit dem die Agents verbunden werden sollen.
        1. Wählen Sie **Azure Defender ein** oder **Azure Defender aus** aus.

1. Wählen Sie in der Konfiguration der **sicherheitsrelevanten Windows-Ereignisse** die Menge an unformatierten Ereignisdaten aus, die Sie speichern möchten:
    - **Keine:** deaktiviert die Speicherung von Sicherheitsereignissen. Dies ist die Standardeinstellung.
    - **Minimal:** speichert eine kleine Gruppe von Ereignissen. Verwenden Sie diese Option, wenn Sie das Ereignisvolumen minimieren möchten.
    - **Allgemein:** speichert eine Gruppe von Ereignissen, die die Anforderungen der meisten Kunden erfüllt und einen vollständigen Überwachungspfad bietet.
    - **Alle Ereignisse:** für Kunden, die alle Ereignisse speichern möchten.

    > [!TIP]
    > Informationen zum Festlegen dieser Optionen auf der Arbeitsbereichsebene finden Sie unter [Festlegen der Option für sicherheitsrelevante Ereignisse auf der Arbeitsbereichsebene](#setting-the-security-event-option-at-the-workspace-level).
    > 
    > Weitere Informationen zu diesen Optionen finden Sie unter [Optionen für sicherheitsrelevante Windows-Ereignisse für den Log Analytics-Agent](#data-collection-tier).

1. Klicken Sie im Konfigurationsbereich auf **Übernehmen**.

1. Wählen Sie **Speichern** aus. Wenn ein Arbeitsbereich bereitgestellt werden muss, kann die Installation des Agents bis zu 25 Minuten dauern.

1. Sie werden gefragt, ob Sie überwachte VMs, die zuvor mit einem Standardarbeitsbereich verbunden waren, erneut konfigurieren möchten:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="Überprüfen der Optionen zum erneuten Konfigurieren überwachter virtueller Computer":::

    - **Nein**: Ihre neuen Arbeitsbereichseinstellungen gelten nur für neu ermittelte VMs, auf denen der Log Analytics-Agent nicht installiert ist.
    - **Ja**: Ihre neuen Arbeitsbereichseinstellungen gelten für alle VMs, und jede VM, die derzeit mit einem von Security Center erstellten Arbeitsbereich verbunden ist, wird erneut mit dem neuen Zielarbeitsbereich verbunden.

   > [!NOTE]
   > Wenn Sie **Ja** auswählen, löschen Sie die von Security Center erstellten Arbeitsbereiche erst, wenn alle VMs mit dem neuen Zielarbeitsbereich verbunden sind. Dieser Vorgang ist nicht erfolgreich, wenn ein Arbeitsbereich zu früh gelöscht wird.


## <a name="enable-auto-provisioning-of-extensions"></a>Aktivieren der automatischen Bereitstellung von Erweiterungen

So aktivieren Sie die automatische Bereitstellung einer anderen Erweiterung als den Log Analytics-Agent: 

1. Wählen Sie im Azure-Portal im Menü von Security Center **Preise & Einstellungen** aus.
1. Wählen Sie das relevante Abonnement aus.
1. Wählen Sie **Automatische Bereitstellung** aus.
1. Wenn Sie die automatische Bereitstellung für den Microsoft Dependency-Agent aktivieren, muss der Log Analytics-Agent ebenfalls auf automatische Bereitstellung eingestellt sein. 
1. Schalten Sie den Status für die jeweilige Erweiterung auf **Ein** um.

    :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="Umschaltfläche zum Aktivieren der automatischen Bereitstellung für das Azure Policy-Add-On für Kubernetes (K8s)":::

1. Wählen Sie **Speichern** aus. Die Azure-Richtlinie wird zugewiesen, und ein Wartungstask wird erstellt.

    |Durchwahl  |Policy  |
    |---------|---------|
    |Policy-Add-On für Kubernetes|[Azure Policy-Add-On für Azure Kubernetes Service-Cluster bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
    |Microsoft Dependency-Agent (Vorschauversion) (Windows-VMs)|[Dependency-Agent für Windows-VMs bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
    |Microsoft Dependency-Agent (Vorschauversion) (Linux-VMs)|[Dependency-Agent für Linux-VMs bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|



## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Optionen für sicherheitsrelevante Windows-Ereignisse für den Log Analytics-Agent <a name="data-collection-tier"></a> 

Die Auswahl einer Datensammlungsebene in Azure Security Center wirkt sich nur auf die *Speicherung* von sicherheitsrelevanten Ereignissen in Ihrem Log Analytics-Arbeitsbereich aus. Unabhängig von der Ebene, die Sie für die Speicherung von sicherheitsrelevanten Ereignissen in Ihrem Arbeitsbereich auswählen, sammelt und analysiert der Log Analytics-Agent weiterhin die für den Bedrohungsschutz von Security Center erforderlichen sicherheitsrelevanten Ereignisse. Wenn Sie sicherheitsrelevante Ereignisse speichern, können Sie diese Ereignisse in Ihrem Arbeitsbereich untersuchen, durchsuchen und überprüfen.

### <a name="requirements"></a>Requirements (Anforderungen) 
Zum Speichern von Daten zu sicherheitsrelevanten Windows-Ereignissen ist Azure Defender erforderlich. [Weitere Informationen zu Azure Defender](azure-defender.md).

Für das Speichern von Daten in Log Analytics fallen möglicherweise zusätzliche Gebühren für die Datenspeicherung an. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="information-for-azure-sentinel-users"></a>Informationen für Azure Sentinel-Benutzer 
Benutzer von Azure Sentinel: Beachten Sie, dass das Erfassen von sicherheitsrelevanten Ereignissen für einen einzelnen Arbeitsbereich entweder in Azure Security Center oder in Azure Sentinel konfiguriert werden kann, aber nicht in beiden Diensten. Wenn Sie Azure Sentinel zu einem Arbeitsbereich hinzufügen möchten, der bereits Benachrichtigungen aus Azure Security Center empfängt und für das Erfassen sicherheitsrelevanter Ereignisse konfiguriert ist, haben Sie zwei Möglichkeiten:
- Behalten Sie das Erfassen von sicherheitsrelevanten Ereignissen im Azure Security Center bei. Sie können diese Ereignisse sowohl in Azure Sentinel als auch in Azure Defender abfragen und analysieren. Allerdings können Sie in Azure Sentinel weder den Verbindungsstatus des Connectors überwachen noch dessen Konfiguration ändern. Wenn dies wichtig für Sie ist, ziehen Sie die zweite Möglichkeit in Betracht.
- Deaktivieren Sie die Sammlung von sicherheitsrelevanten Ereignissen in Azure Security Center (indem Sie in der Konfiguration des Log Analytics-Agents **Windows-Sicherheitsereignisse** auf **Keine** festlegen). Fügen Sie dann den Connector für sicherheitsrelevante Ereignisse in Azure Sentinel hinzu. Wie bei der ersten Möglichkeit können Sie Ereignisse sowohl in Azure Sentinel als auch in Azure Defender/ASC abfragen und analysieren. Nun können Sie allerdings in Azure Sentinel – und nur dort – den Verbindungsstatus des Connectors überwachen und dessen Konfiguration ändern.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>Welche Ereignistypen werden für die Einstellungen „Allgemein“ und „Minimal“ gespeichert?
Diese Gruppen wurden für typische Szenarien konzipiert. Überlegen Sie sich vor der Implementierung, welche Ihre Anforderungen erfüllt.

Bei der Bestimmung der Ereignisse für die Optionen **Allgemein** und **Minimal** haben wir mit Kunden zusammengearbeitet und uns über Branchenstandards informiert, um mehr über die ungefilterte Häufigkeit der einzelnen Ereignisse und deren Verwendung zu erfahren. Dabei haben wir uns an den folgenden Richtlinien orientiert:

- **Minimal:** Diese Gruppe deckt nur Ereignisse ab, die auf eine erfolgreiche Sicherheitsverletzung hindeuten, sowie wichtige Ereignisse, die nur sehr selten auftreten. So deckt diese Gruppe beispielsweise erfolgreiche und nicht erfolgreiche Benutzeranmeldungen (Ereignis-IDs 4624 und 4625), aber keine Abmeldungen ab. Diese sind zwar für die Überwachung wichtig, nicht aber für die Erkennung, und sie treten zudem relativ häufig auf. Der Großteil des Datenvolumens dieser Gruppe ist auf Anmeldeereignisse und auf das Prozesserstellungsereignis (Ereignis-ID 4688) zurückzuführen.
- **Allgemein:** Diese Gruppe bietet einen vollständigen Benutzerüberwachungspfad. So enthält die Gruppe beispielsweise sowohl Benutzeranmeldungen als auch Benutzerabmeldungen (Ereignis-ID 4634). Wir schließen Überwachungsaktionen wie Sicherheitsgruppenänderungen, wichtige Domänencontroller-Kerberos-Vorgänge und andere Ereignisse ein, die von Branchenorganisationen empfohlen werden.

Ereignisse mit sehr geringem Volumen wurden in die Gruppe „Allgemein“ aufgenommen, da das Hauptargument für ihre Verwendung (im Vergleich zu allen anderen Ereignissen) in der Verringerung des Volumens besteht (und nicht darin, bestimmte Ereignisse herauszufiltern).

Im Anschluss finden Sie eine vollständige Aufschlüsselung der Sicherheits- und AppLocker-Ereignis-IDs für die einzelnen Gruppen:

| Datenschicht | Indikatoren für gesammelte Ereignisse |
| --- | --- |
| Wenig | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Allgemein | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Wenn Sie ein Gruppenrichtlinienobjekt (Group Policy Object, GPO) verwenden, wird empfohlen, das Überwachungsrichtlinien-Prozesserstellungsereignis 4688 und das Feld *CommandLine* im Ereignis 4688 zu aktivieren. Weitere Informationen zum Prozesserstellungsereignis 4688 finden Sie in den [häufig gestellten Fragen](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled) zu Security Center. Weitere Informationen zu diesen Überwachungsrichtlinien finden Sie unter [Empfehlungen zur Überwachungsrichtlinie](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Um die Datensammlung für [Adaptive Anwendungssteuerungen](security-center-adaptive-application.md) zu aktivieren, konfiguriert Security Center eine lokale AppLocker-Richtlinie im Überwachungsmodus, um alle Anwendungen zuzulassen. Damit generiert AppLocker Ereignisse, die dann von Security Center gesammelt und genutzt werden können. Es ist wichtig zu beachten, dass diese Richtlinie nicht auf Computern konfiguriert wird, auf denen bereits eine AppLocker-Richtlinie konfiguriert ist. 
> - Zum Erfassen von [Ereignis-ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156) der Windows-Filterplattform müssen Sie [Filterplattformverbindung überwachen](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) aktivieren (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable).
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>Festlegen der Option für sicherheitsrelevante Ereignisse auf der Arbeitsbereichsebene

Sie können die Ebene der Daten, die zu sicherheitsrelevanten Ereignissen gespeichert werden sollen, auf der Arbeitsbereichsebene definieren.

1. Wählen Sie im Azure-Portal im Menü von Security Center **Preise & Einstellungen** aus.
1. Wählen Sie den relevanten Arbeitsbereich aus. Die einzigen Datensammlungsereignisse für einen Arbeitsbereich sind die auf dieser Seite beschriebenen sicherheitsrelevanten Windows-Ereignisse.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="Festlegen der Daten zu sicherheitsrelevanten Ereignissen, die in einem Arbeitsbereich gespeichert werden sollen":::

1. Wählen Sie die Menge zu speichernder Rohereignisdaten aus, und klicken Sie auf **Speichern**.

## <a name="manual-agent-provisioning"></a>Manuelle Agent-Bereitstellung <a name="manual-agent"></a>
 
So installieren Sie den Log Analytics-Agent manuell:

1. Deaktivieren Sie die automatische Bereitstellung.

1. Erstellen Sie optional einen Arbeitsbereich.

1. Aktivieren Sie Azure Defender in dem Arbeitsbereich, für den Sie den Log Analytics-Agent installieren:

    1. Wählen Sie im Menü von Security Center **Preise und Einstellungen** aus.

    1. Legen Sie den Arbeitsbereich fest, für den Sie den Agent installieren. Stellen Sie sicher, dass sich der Arbeitsbereich im gleichen Abonnement befindet, das Sie in Security Center verwenden, und dass Sie über Lese-/Schreibberechtigungen für den Arbeitsbereich verfügen.

    1. Wählen Sie **Azure Defender ein** und dann **Speichern** aus.

       >[!NOTE]
       >Wenn für den Arbeitsbereich bereits eine **Security**- oder **SecurityCenterFree**-Lösung aktiviert ist, werden die Preise automatisch festgelegt. 

1. Wenn Sie Agents mithilfe einer Resource Manager-Vorlage auf neuen VMs bereitstellen möchten, installieren Sie den Log Analytics-Agent:

   - [Installieren des Log Analytics-Agents für Windows](../virtual-machines/extensions/oms-windows.md)
   - [Installieren des Log Analytics-Agents für Linux](../virtual-machines/extensions/oms-linux.md)

1. Folgen Sie zum Bereitstellen von Agents auf Ihren vorhandenen VMs den Anweisungen unter [Sammeln von Daten von einem virtuellen Azure-Computer mit Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md) (der Abschnitt **Sammeln von Ereignis- und Leistungsdaten** ist optional).

1. Wenn Sie die Agents mithilfe von PowerShell bereitstellen möchten, folgen Sie den Anweisungen in der Dokumentation zu VMs:

    - [Für Windows-Computer](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Für Linux-Computer](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> Anweisungen für das Onboarding von Security Center mit PowerShell finden Sie unter [Automatisieren des Onboardings von Azure Security Center mit PowerShell](security-center-powershell-onboarding.md).


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatische Bereitstellung bei einer bereits vorhandenen Agent-Installation <a name="preexisting"></a> 

Die folgenden Anwendungsfälle geben an, wie die automatische Bereitstellung funktioniert, wenn bereits ein Agent oder eine Erweiterung installiert ist. 

- **Der Log Analytics-Agent ist auf dem Computer installiert, aber nicht als Erweiterung (Direkt-Agent)** : Wenn der Log Analytics-Agent direkt auf der VM installiert ist (also nicht als Azure-Erweiterung), installiert Security Center die Log Analytics-Agent-Erweiterung und aktualisiert den Log Analytics-Agent ggf. auf die aktuelle Version.
Der installierte Agent erstellt seine Berichte weiterhin für die bereits konfigurierten Arbeitsbereiche und darüber hinaus für den in Security Center konfigurierten Arbeitsbereich (Multi-Homing wird auf Windows-Computern unterstützt).
Wenn der konfigurierte Arbeitsbereich ein Benutzerarbeitsbereich ist (nicht der Standardarbeitsbereich von Security Center), müssen Sie darin die Lösung „security“ oder „securityFree“ installieren, damit Security Center mit der Verarbeitung von Ereignissen von VMs und Computern, die ihre Berichte in diesem Arbeitsbereich erstellen, beginnen kann.

    Auf Linux-Computern wird das Multi-Homing von Agents noch nicht unterstützt, weshalb, wenn eine bestehende Agent-Installation erkannt wird, keine automatische Bereitstellung erfolgt und die Konfiguration des Computers nicht geändert wird.

    Wenn für vorhandene Computer in Abonnements, für die das Onboarding in Security Center vor dem 17. März 2019 durchgeführt wurde, ein vorhandener Agent erkannt wurde, wird die Log Analytics-Agent-Erweiterung nicht installiert, und der Computer ist nicht betroffen. Für diese Computer wird die Empfehlung „Monitoring Agent-Integritätsprobleme auf Ihren Computern beheben“ angezeigt, damit Sie die Installationsprobleme des Agents auf diesen Computern beheben können.
  
- **Der System Center Operations Manager-Agent ist auf dem Computer installiert**: Security Center installiert die Log Analytics-Agent-Erweiterung parallel zum vorhandenen Operations Manager. Der vorhandene Operations Manager-Agent sendet weiterhin normal Berichte an den Operations Manager-Server. Der Operations Manager-Agent und der Log Analytics-Agent nutzen gemeinsame Laufzeitbibliotheken, die bei diesem Vorgang auf die neueste Version aktualisiert werden. Wenn die Operations Manager-Agentversion 2012 installiert ist, aktivieren Sie die automatische Bereitstellung **nicht**.

- **Eine VM-Erweiterung ist bereits vorhanden**:
    - Wenn der Monitoring Agent als Erweiterung installiert ist, erlaubt die Konfiguration der Erweiterung auch Berichte an nur einen einzelnen Arbeitsbereich. Bereits vorhandene Verbindungen mit Benutzerarbeitsbereichen werden von Security Center nicht überschrieben. Security Center speichert Sicherheitsdaten des virtuellen Computers im bereits verbundenen Arbeitsbereich, sofern darin die Lösung „security“ oder „securityFree“ installiert wurde. Security Center kann die Version der Erweiterung während dieses Vorgangs auf die neueste Version aktualisieren.  
    - Um festzustellen, an welchen Arbeitsbereich die vorhandene Erweiterung Daten sendet, führen Sie den Test zum [Überprüfen der Konnektivität mit Azure Security Center](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center) aus. Alternativ können Sie Log Analytics-Arbeitsbereiche öffnen, einen Arbeitsbereich und den virtuellen Computer auswählen und sich die Log Analytics-Agent-Verbindung ansehen. 
    - Wenn Sie über eine Umgebung verfügen, in der der Log Analytics-Agent auf Clientarbeitsstationen installiert ist und an einen vorhandenen Log Analytics-Arbeitsbereich berichtet, überprüfen Sie die Liste der [von Azure Security Center unterstützten Betriebssysteme](security-center-os-coverage.md), um sicherzustellen, dass Ihr Betriebssystem unterstützt wird. Weitere Informationen finden Sie unter [Log Analytics-Bestandskunden](./faq-azure-monitor-logs.md).
 

## <a name="disable-auto-provisioning"></a>Deaktivieren der automatischen Bereitstellung <a name="offprovisioning"></a>

Wenn Sie die automatische Bereitstellung deaktivieren, werden keine Agents auf neuen VMs bereitgestellt.

So deaktivieren Sie die automatische Bereitstellung eines Agents:

1. Wählen Sie im Portal im Security Center-Menü **Preise & Einstellungen** aus.
1. Wählen Sie das relevante Abonnement aus.
1. Wählen Sie **Automatische Bereitstellung** aus.
1. Schalten Sie den Status für den jeweiligen Agent auf **Aus** um.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Umschaltflächen zum Deaktivieren der automatischen Bereitstellung für die einzelnen Agent-Typen":::

1. Wählen Sie **Speichern** aus. Wenn die automatische Bereitstellung deaktiviert ist, wird der Abschnitt mit der standardmäßigen Arbeitsbereichskonfiguration nicht angezeigt:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="Wenn die automatische Bereitstellung deaktiviert ist, ist die Zelle unter „Konfiguration“ leer.":::


> [!NOTE]
>  Wenn Sie die automatische Bereitstellung deaktivieren, wird der Log Analytics-Agent nicht von virtuellen Azure-Computern entfernt, auf denen der Agent bereitgestellt wurde. Informationen zum Entfernen der OMS-Erweiterungs finden Sie unter [Wie entferne ich durch Security Center installierte OMS-Erweiterungen?](faq-data-collection-agents.md#remove-oms).
>


## <a name="troubleshooting"></a>Problembehandlung

-   Informationen zum Identifizieren von Problemen bei der automatischen Bereitstellung finden Sie unter [Integritätsprobleme des Überwachungs-Agents](security-center-troubleshooting-guide.md#mon-agent).

-  Informationen zum Identifizieren von Netzwerkanforderungen für den Überwachungs-Agent finden Sie unter [Beheben von Problemen mit den Netzwerkanforderungen für den Überwachungs-Agent](security-center-troubleshooting-guide.md#mon-network-req).
-   Informationen zum Identifizieren von Problemen beim manuellen Onboarding finden Sie unter [Behandeln von Problemen beim Operations Management Suite-Onboarding](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- So identifizieren Sie Probleme mit nicht überwachten VMs und physischen Computern:

    Ein virtueller oder physischer Computer gilt als nicht von Security Center überwacht, wenn auf dem Computer die Log Analytics-Agent-Erweiterung nicht ausgeführt wird. Auf einem Computer kann bereits ein lokaler Agent installiert sein, z.B. der OMS-Direkt-Agent oder der System Center Operations Manager-Agent. Computer mit diesen Agents werden als nicht überwacht angesehen, da diese Agents in Security Center nicht vollständig unterstützt werden. Die Log Analytics-Agent-Erweiterung ist erforderlich, um von allen Security Center-Vorteilen vollständig profitieren zu können.

    Weitere Informationen zu den Gründen, aus denen Security Center VMs und Computer, die für die automatische Bereitstellung initialisiert wurden, nicht erfolgreich überwachen kann, finden Sie unter [Integritätsprobleme von Monitoring Agent](security-center-troubleshooting-guide.md#mon-agent).




## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde die Funktionsweise der Datensammlung und der automatischen Bereitstellung in Security Center erläutert. Weitere Informationen zu Security Center finden Sie auf den folgenden Seiten:

- [Azure Security Center – Häufig gestellte Fragen](faq-general.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.

In diesem Artikel wird die Installation eines Log Analytics-Agents und das Festlegen eines Log Analytics-Arbeitsbereichs beschrieben, in dem die gesammelten Daten gespeichert werden. Beide Vorgänge sind erforderlich, um die Datensammlung aktivieren. Ganz gleich, ob Sie einen neuen oder vorhandenen Arbeitsbereich verwenden, fallen für das Speichern von Daten in Log Analytics möglicherweise zusätzliche Gebühren für die Datenspeicherung an. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/).

