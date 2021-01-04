---
title: 'Häufig gestellte Fragen zu Azure Security Center: Datensammlung und Agents'
description: Häufig gestellte Fragen zu Datensammlung, Agents und Arbeitsbereichen für Azure Security Center – einem Produkt, das Ihnen dabei hilft, Bedrohungen zu verhindern und zu erkennen und auf Bedrohungen zu reagieren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 64fa6c72e3bc37276dd108e3981bbefb5a2021a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444513"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Häufig gestellte Fragen: Datensammlung, Agents und Arbeitsbereiche

Security Center erfasst Daten von Ihren virtuellen Azure-Computern (virtual machines, VMs), VM-Skalierungsgruppen, IaaS-Containern und Azure-fremden Computern (auch lokal), um sie auf Sicherheitslücken und Bedrohungen zu überwachen. Die Daten werden mit dem Log Analytics-Agent erfasst. Der Agent liest verschiedene sicherheitsrelevante Konfigurationen und Ereignisprotokolle auf dem Computer und kopiert die Daten zur Analyse in den Arbeitsbereich.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Werden mir die Azure Monitor-Protokolle auf den von Security Center erstellten Arbeitsbereiche in Rechnung gestellt?

Nein. Von Security Center erstellte Arbeitsbereiche sind zwar für die knotenbasierte Abrechnung von Azure Monitor-Protokollen konfiguriert, es fallen jedoch keine Kosten für Azure Monitor-Protokolle an. Die Abrechnung von Security Center basiert immer auf Ihrer Security Center-Sicherheitsrichtlinie und den installierten Lösungen in einem Arbeitsbereich:

- **Azure Defender aus**: Security Center aktiviert die Lösung „SecurityCenterFree“ im Standardarbeitsbereich. Wenn Azure Defender deaktiviert ist, entstehen Ihnen keine Kosten.

- **Azure Defender ein**: Security Center aktiviert die Lösung „Security“ im Standardarbeitsbereich.

Weitere Informationen zu Preisen finden Sie unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Der Protokollanalyse-Tarif von Arbeitsbereichen, die von Security Center erstellt wurden, hat keine Auswirkungen auf die Security Center-Abrechnung.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>Was ist der Log Analytics-Agent?

Zur Überwachung auf Sicherheitsrisiken und Bedrohungen ist Azure Security Center vom [Log Analytics-Agent](../azure-monitor/platform/log-analytics-agent.md) abhängig. Dieser Agent wird auch vom Azure Monitor-Dienst verwendet. 

Der Agent wird manchmal als Microsoft Monitoring Agent (oder „MMA“ bezeichnet) bezeichnet. 

Der Agent sammelt verschiedene sicherheitsrelevante Konfigurationsdetails und Ereignisprotokolle von verbundenen Computern und kopiert die Daten zur weiteren Analyse in den Log Analytics-Arbeitsbereich. Beispiele für Daten dieser Art: Betriebssystemtyp und -version, Betriebssystemprotokolle (Windows-Ereignisprotokolle), ausgeführte Prozesse, Computername, IP-Adressen und angemeldeter Benutzer.

Stellen Sie sicher, dass auf ihren Computern eines der unterstützten Betriebssysteme für den Agent ausgeführt wird, wie auf den folgenden Seiten beschrieben:

* [Log Analytics-Agent für von Windows unterstützte Betriebssysteme](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

* [Log Analytics-Agent für von Linux unterstützte Betriebssysteme](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

Weitere Informationen zu den[vom Log Analytics-Agent gesammelten Daten](security-center-enable-data-collection.md).




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Wann kommt eine VM für die automatische Bereitstellung der Log Analytics-Agent-Installation infrage?

Windows- oder Linux-IaaS-VMs kommen unter folgenden Voraussetzungen infrage:

- Die Log Analytics-Agent-Erweiterung ist derzeit nicht auf der VM installiert.
- Die VM wird ausgeführt.
- Der [Azure-VM-Agent](../virtual-machines/extensions/agent-windows.md) für Windows oder Linux ist installiert.
- Die VM wird nicht als Appliance wie z.B. Webanwendungsfirewall oder Firewall der nächsten Generation verwendet.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Wo wird der standardmäßige Log Analytics-Arbeitsbereich erstellt?

Der Speicherort des standardmäßigen Arbeitsbereichs hängt von Ihrer Azure-Region ab:

- Bei virtuellen Computern in den USA und Brasilien befindet sich der Arbeitsbereich in den USA.
- Bei virtuellen Computern in Kanada befindet sich der Arbeitsbereich in Kanada.
- Bei virtuellen Computern in Europa befindet sich der Arbeitsbereich in Europa.
- Bei virtuellen Computern in Großbritannien befindet sich der Arbeitsbereich in Großbritannien.
- Bei virtuellen Computern in „Asien, Osten“ und „Asien, Südosten“ befindet sich der Arbeitsbereich in Asien.
- Bei virtuellen Computern in Korea befindet sich der Arbeitsbereich in Korea.
- Bei virtuellen Computern in Indien befindet sich der Arbeitsbereich in Indien.
- Bei virtuellen Computern in Japan befindet sich der Arbeitsbereich in Japan.
- Bei virtuellen Computern in China befindet sich der Arbeitsbereich in China.
- Bei virtuellen Computern in Australien befindet sich der Arbeitsbereich in Australien.


## <a name="what-security-events-are-collected-by-the-log-analytics-agent"></a>Welche Sicherheitsereignisse werden vom Log Analytics-Agent erfasst?

Eine vollständige Liste mit den Sicherheitsereignissen, die vom Agent gesammelt werden, finden Sie unter [Welche Ereignistypen werden für die Einstellungen „Allgemein“ und „Minimal“ gespeichert?](security-center-enable-data-collection.md#what-event-types-are-stored-for-common-and-minimal).

> [!IMPORTANT]
> Wenn Sie die Protokollierung aktiviert und eine sehr aktive Ressource zum Protokollieren ausgewählt haben (z. B. das Protokoll auf *ausführlich* festgelegt haben), könnte dies bei einigen Diensten wie Azure-Firewall erhebliche Auswirkungen auf die Speicheranforderungen Ihres Log Analytics-Arbeitsbereichs haben. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kann ich die von Security Center erstellten Standardarbeitsbereiche löschen?

**Das Löschen des Standardarbeitsbereichs wird nicht empfohlen.** Security Center verwendet die Standardarbeitsbereiche, um Sicherheitsdaten Ihrer virtuellen Computer zu speichern. Wenn Sie einen Arbeitsbereich löschen, kann Security Center diese Daten nicht erfassen, und einige Sicherheitsempfehlungen und Warnungen sind nicht verfügbar.

Entfernen Sie zur Wiederherstellung den Log Analytics-Agent auf den VMs, die mit dem gelöschten Arbeitsbereich verbunden sind. Security Center installiert den Agent erneut und erstellt neue Standardarbeitsbereiche.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Wie kann ich meinen vorhandenen Log Analytics-Arbeitsbereich verwenden?

Sie können einen vorhandenen Log Analytics-Arbeitsbereich zum Speichern der von Security Center gesammelten Daten auswählen. Wenn Sie Ihren vorhandenen Log Analytics-Arbeitsbereich verwenden möchten, gilt Folgendes:

- Der Arbeitsbereich muss Ihrem ausgewählten Azure-Abonnement zugeordnet sein.
- Sie müssen mindestens über Leseberechtigungen verfügen, um auf den Arbeitsbereich zugreifen zu können.

So wählen Sie einen vorhandenen Log Analytics-Arbeitsbereich aus:

1. Wählen Sie im Menü von Security Center **Preise und Einstellungen** aus.
1. Wählen Sie das relevante Abonnement aus.
1. Öffnen Sie die Seite **Automatische Bereitstellung**.
1. Wählen Sie für den Log Analytics-Agent **Konfiguration bearbeiten** aus. 

    :::image type="content" source="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png" alt-text="Die Konfiguration des Log Analytics-Agents, der bei Verwendung der automatischen Bereitstellung verwendet werden soll" lightbox="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png":::

1. Wählen Sie **Azure-VMs mit einem anderen Arbeitsbereich verbinden** und dann den vorhandenen Arbeitsbereich aus.

    :::image type="content" source="./media/security-center-enable-data-collection/choose-workspace.png" alt-text="Auswählen eines nicht standardmäßigen Arbeitsbereichs, an den der Log Analytics-Agent berichten soll" lightbox="./media/security-center-enable-data-collection/choose-workspace.png":::

    > [!TIP]
    > Die Liste enthält nur Arbeitsbereiche, auf die Sie Zugriff haben und die in Ihrem Azure-Abonnement enthalten sind.

1. Wählen Sie **Speichern** aus. Sie werden gefragt, ob Sie überwachte virtuelle Computer neu konfigurieren möchten.

    - Klicken Sie auf **Nein**, wenn die neuen Arbeitsbereichseinstellungen **nur auf neue virtuelle Computer angewendet werden sollen**. Die neuen Arbeitsbereichseinstellungen gelten nur für neue Agent-Installationen (neu ermittelte virtuelle Computer, auf denen der Log Analytics-Agent nicht installiert ist).
    - Klicken Sie auf **Ja**, wenn die neuen Arbeitsbereichseinstellungen **auf alle virtuellen Computer angewendet werden sollen**. Darüber hinaus wird jeder virtuelle Computer, der mit einem von Security Center erstellten Arbeitsbereich verbunden ist, nun mit dem neuen Zielarbeitsbereich verbunden.

    > [!NOTE]
    > Wenn Sie **Ja** auswählen, löschen Sie die von Security Center erstellten Arbeitsbereiche erst, wenn alle virtuellen Computer mit dem neuen Zielarbeitsbereich verbunden sind. Dieser Vorgang ist nicht erfolgreich, wenn ein Arbeitsbereich zu früh gelöscht wird.


## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Was passiert, wenn der Log Analytics-Agent bereits als Erweiterung auf der VM installiert wurde?<a name="mmaextensioninstalled"></a>

Wenn der Monitoring Agent als Erweiterung installiert ist, erlaubt die Konfiguration der Erweiterung auch Berichte an nur einen einzelnen Arbeitsbereich. Bereits vorhandene Verbindungen mit Benutzerarbeitsbereichen werden von Security Center nicht überschrieben. Security Center speichert Sicherheitsdaten eines virtuellen Computers in einem bereits verbundenen Arbeitsbereich, sofern darin die Lösung „Security“ oder „SecurityCenterFree“ installiert wurde. Security Center kann die Version der Erweiterung während dieses Vorgangs auf die neueste Version aktualisieren.

Weitere Informationen finden Sie unter [Automatische Bereitstellung bei einer bereits vorhandenen Agent-Installation](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Was geschieht, wenn der Log Analytics-Agent direkt ist auf dem Computer installiert wird, jedoch nicht als Erweiterung (direkter Agent)?<a name="directagentinstalled"></a>

Wenn der Log Analytics-Agent direkt auf dem virtuellen Computer (also nicht als Azure-Erweiterung) installiert ist, installiert Security Center die Log Analytics-Agent-Erweiterung und aktualisiert den Log Analytics-Agent ggf. auf die neueste Version.

Der installierte Agent erstellt seine Berichte weiterhin für die bereits konfigurierten Arbeitsbereiche und darüber hinaus für den in Security Center konfigurierten Arbeitsbereich (Multi-Homing wird auf Windows-Computern unterstützt).

Wenn der konfigurierte Arbeitsbereich ein Benutzerarbeitsbereich ist (nicht der Standardarbeitsbereich von Security Center), müssen Sie darin die Lösung „Security“ oder „SecurityCenterFree“ installieren, damit Security Center mit der Verarbeitung von Ereignissen von VMs und Computern, die ihre Berichte in diesem Arbeitsbereich erstellen, beginnen kann.

Auf Linux-Computern wird das Multi-Homing von Agents noch nicht unterstützt, weshalb, wenn eine bestehende Agent-Installation erkannt wird, keine automatische Bereitstellung erfolgt und die Konfiguration des Computers nicht geändert wird.

Wenn für vorhandene Computer in Abonnements, die vor dem 17. März 2019 in Security Center integriert wurden, ein vorhandener Agent erkannt wurde, wird die Log Analytics-Agent-Erweiterung nicht installiert, und der Computer ist nicht betroffen. Für diese Computer wird die Empfehlung „Monitoring Agent-Integritätsprobleme auf Ihren Computern beheben“ angezeigt, damit Sie die Installationsprobleme des Agents auf diesen Computern beheben können.

Weitere Informationen finden Sie im nächsten Abschnitt: [Was geschieht, wenn ein System Center Operations Manager- oder OMS-Direkt-Agent bereits auf meiner VM installiert ist?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Was, wenn auf meinem virtuellen Computer bereits ein System Center Operations Manager-Agent installiert ist?<a name="scomomsinstalled"></a>

Security Center installiert die Log Analytics-Agent-Erweiterung zusätzlich zum vorhandenen System Center Operations Manager-Agent. Der vorhandene Agent sendet weiterhin normal Berichte an den System Center Operations Manager-Server. Beachten Sie, dass die vom Operations Manager-Agent und dem Log Analytics-Agent gemeinsam genutzten Laufzeitbibliotheken bei diesem Vorgang auf die neueste Version aktualisiert werden. Hinweis: Falls Version 2012 des Operations Manager-Agents installiert ist, aktivieren Sie die automatische Bereitstellung nicht (Verwaltungsfunktionen können verloren gehen, wenn der Operations Manager-Server auch Version 2012 aufweist).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Was passiert, wenn ich diese Erweiterungen entferne?

Wenn Sie die Microsoft Monitoring-Erweiterung entfernen, kann Security Center keine Sicherheitsdaten des virtuellen Computers erfassen, und einige Sicherheitsempfehlungen und Warnungen sind nicht verfügbar. Innerhalb von 24 Stunden erkennt Security Center, dass die Erweiterung auf dem virtuellen Computer fehlt, und installiert sie erneut.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Wie verhindere ich die automatische Agent-Installation und die Arbeitsbereicherstellung?

Sie können die automatische Bereitstellung für Ihre Abonnements in der Sicherheitsrichtlinie deaktivieren, dies wird jedoch nicht empfohlen. Die Deaktivierung der automatischen Bereitstellung schränkt die Empfehlungen und Warnungen von Security Center ein. So deaktivieren Sie die automatische Bereitstellung:

1. Wählen Sie im Menü von Security Center **Preise und Einstellungen** aus.
1. Wählen Sie das relevante Abonnement aus.
1. Wenn für Ihr Abonnement Azure Defender aktiviert ist, öffnen Sie **Azure Defender-Pläne**, und wählen Sie **Azure Defender aus** aus.

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Aktivieren oder Deaktivieren von Azure Defender":::

1. Wählen Sie auf der Seite **Automatische Bereitstellung** den Stift aus, und deaktivieren Sie die automatische Bereitstellung auf der Seite **Sicherheitsrichtlinie – Datensammlung**.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Aktivieren der automatischen Bereitstellung für den Log Analytics-Agent":::

1. Wählen Sie **Speichern** aus.


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Sollte ich die automatische Agent-Installation und Arbeitsbereicherstellung deaktivieren?

> [!NOTE]
> Lesen Sie unbedingt die Abschnitte [Welche Auswirkungen hat die Deaktivierung der automatischen Bereitstellung?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) und [Welche Schritte werden bei Deaktivierung der automatischen Bereitstellung empfohlen?](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning), falls Sie entscheiden, die automatische Bereitstellung zu deaktivieren.

Möglicherweise möchten Sie die automatische Bereitstellung deaktivieren, wenn Folgendes auf Sie zutrifft:

- Die automatische Agent-Installation durch Security Center gilt für das gesamte Abonnement. Sie können die automatische Installation nicht auf eine Teilmenge der VMs anwenden. Wenn wichtige VMs nicht mit dem Log Analytics-Agent installiert werden können, sollten Sie die automatische Bereitstellung deaktivieren.
- Die Installation der Log Analytics-Agent-Erweiterung aktualisiert die Agent-Version. Dies gilt für einen direkten Agent und einen System Center Operations Manager-Agent (im letztgenannten teilen sich Operations Manager und der Log Analytics-Agent gemeinsame Laufzeitbibliotheken, die im Prozess aktualisiert werden). Wenn der installierte Operations Manager-Agent Version 2012 aufweist und aktualisiert wird, können Verwaltungsfunktionen verloren gehen, wenn die Version des Operations Manager-Servers ebenfalls 2012 ist. Erwägen Sie die Deaktivierung der automatischen Bereitstellung, wenn Sie Version 2012 des Operations Manager-Agents installiert haben.
- Wenn Sie über einen benutzerdefinierten Arbeitsbereich außerhalb des Abonnements (einen zentralen Arbeitsbereich) verfügen, sollten Sie die automatische Bereitstellung deaktivieren. Sie können die Log Analytics-Agent-Erweiterung manuell installieren und mit Ihrem Arbeitsbereich verbinden, ohne dass Security Center die Verbindung überschreibt.
- Wenn Sie das Erstellen mehrerer Arbeitsbereiche pro Abonnement vermeiden möchten und innerhalb des Abonnements über Ihren eigenen benutzerdefinierten Arbeitsbereich verfügen, haben Sie zwei Optionen:

   1. Sie können die automatische Bereitstellung deaktivieren. Legen Sie nach der Migration die standardmäßigen Arbeitsbereichseinstellungen wie in [Wie kann ich meinen vorhandenen Log Analytics-Arbeitsbereich verwenden?](#how-can-i-use-my-existing-log-analytics-workspace) beschrieben fest.

   1. Alternativ können Sie zulassen, dass die Migration abgeschlossen, der Log Analytics-Agent auf den VMs installiert und die Verbindung der VMs mit dem erstellten Arbeitsbereich hergestellt wird. Wählen Sie dann Ihren eigenen benutzerdefinierten Arbeitsbereich durch Einstellung der standardmäßigen Arbeitsbereichseinstellung mit Aktivierung zur Neukonfiguration der bereits installierten Agents. Weitere Informationen finden Sie unter [Wie kann ich meinen vorhandenen Log Analytics-Arbeitsbereich verwenden?](#how-can-i-use-my-existing-log-analytics-workspace).


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Welche Auswirkungen hat die Deaktivierung der automatischen Bereitstellung?

Wenn die Migration abgeschlossen ist, kann Security Center keine Sicherheitsdaten der VM erfassen, und einige Sicherheitsempfehlungen und Warnungen sind nicht verfügbar. Bei Deaktivierung installieren Sie den Log Analytics-Agent manuell. Siehe [Welche Schritte werden bei Deaktivierung der automatischen Bereitstellung empfohlen?](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Welche Schritte werden bei Deaktivierung der automatischen Bereitstellung empfohlen?

Damit Security Center Sicherheitsdaten auf virtuellen Computern erfassen und Empfehlungen und Warnungen bereitstellen kann, installieren Sie die Log Analytics-Agent-Erweiterung manuell. Eine Anleitung zur Installation finden Sie unter [Agent-Installation für Windows-VM](../virtual-machines/extensions/oms-windows.md) oder [Agent-Installation für Linux-VM](../virtual-machines/extensions/oms-linux.md).

Sie können eine Verbindung des Agent zu jedem vorhandenen benutzerdefinierten Arbeitsbereich oder in Security Center erstellten Arbeitsbereich herstellen. Wenn in einem benutzerdefinierten Arbeitsbereich die Lösungen „Security“ oder „SecurityCenterFree“ nicht aktiviert sind, müssen Sie eine Lösung anwenden. Wählen Sie zum Anwenden den benutzerdefinierten Arbeitsbereich oder das Abonnement aus, und wenden Sie einen Tarif über die Seite **Sicherheitsrichtlinie – Tarif** an.

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Aktivieren oder Deaktivieren von Azure Defender":::

Security Center aktiviert basierend auf dem ausgewählten Tarif die richtige Lösung im Arbeitsbereich.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Wie entferne ich durch Security Center installierte OMS-Erweiterungen?<a name="remove-oms"></a>

Sie können den Log Analytics-Agent manuell entfernen. Dies wird jedoch nicht empfohlen, da es die Empfehlungen und Warnungen von Security Center einschränkt.

> [!NOTE]
> Bei aktivierter Datensammlung wird der Agent nach dem Entfernen erneut installiert.  Daher müssen Sie vor dem manuellen Entfernen des Agents die Datensammlung deaktivieren. Die Anleitung zum Deaktivieren der Datensammlung finden Sie unter „Wie verhindere ich die automatische Agent-Installation und die Arbeitsbereicherstellung?“

So entfernen Sie den Agent manuell:

1.    Öffnen Sie im Portal **Log Analytics**.

1.    Wählen Sie auf der Seite „Log Analytics“ einen Arbeitsbereich aus:

1.    Wählen Sie die virtuellen Computer aus, die Sie nicht überwachen möchten, und wählen Sie anschließend **Trennen** aus.

   ![Entfernen des Agents][3]

> [!NOTE]
> Falls ein virtueller Linux-Computer bereits über einen OMS-Agent verfügt, bei dem es sich nicht um eine Erweiterung handelt, wird der Agent beim Entfernen der Erweiterung ebenfalls entfernt und muss neu installiert werden.


## <a name="how-do-i-disable-data-collection"></a>Wie deaktiviere ich Datensammlung?

Die automatische Bereitstellung wird dringend empfohlen, um Sicherheitswarnungen und -empfehlungen zu Systemupdates, zu Sicherheitsrisiken für das Betriebssystem und zu Endpoint Protection zu erhalten. Standardmäßig ist die automatische Bereitstellung deaktiviert.

Wenn Sie sie aktiviert haben, aber jetzt deaktivieren möchten:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) das **Security Center**, und wählen Sie **Preise und Einstellungen** aus.

1. Wählen Sie das Abonnement aus, für das Sie die automatische Bereitstellung deaktivieren möchten.

1. Schalten Sie unter **Automatische Bereitstellung** die Umschaltfläche	für den Log Analytics-Agent aus.


## <a name="how-do-i-enable-data-collection"></a>Wie aktiviere ich die Datensammlung?

Sie können die Datensammlung für Ihr Azure-Abonnement in der Sicherheitsrichtlinie aktivieren. Zum Aktivieren der Datensammlung [Melden Sie sich beim Azure-Portal an](https://portal.azure.com), und wählen Sie nacheinander **Durchsuchen**, **Security Center** und **Sicherheitsrichtlinie** aus. Wählen Sie das Abonnement aus, für das Sie die automatische Bereitstellung aktivieren möchten. Wenn Sie ein Abonnement auswählen, wird **Sicherheitsrichtlinie – Datensammlung** geöffnet. Wählen Sie unter **Automatische Bereitstellung** die Option **Ein** aus.


## <a name="what-happens-when-data-collection-is-enabled"></a>Was passiert, wenn die Datensammlung aktiviert wird?

Bei aktivierter automatischer Bereitstellung wird der Log Analytics-Agent von Security Center auf allen unterstützten und neu erstellten virtuellen Azure-Computern bereitgestellt. Die automatische Bereitstellung wird empfohlen, die manuelle Agent-Installation ist jedoch ebenfalls verfügbar. [Erfahren Sie, wie Sie die Log Analytics-Agent-Erweiterung installieren](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Der Agent aktiviert das Prozesserstellungsereignis 4688 und das Feld *CommandLine* im Ereignis 4688. Auf der VM erstellte neue Prozesse werden von EventLog aufgezeichnet und von den Erkennungsdiensten in Security Center überwacht. Weitere Informationen zu den für jeden neuen Prozess aufgezeichneten Details finden Sie unter [Description Fields in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields) (Beschreibungsfelder in 4688). Außerdem sammelt der Agent die auf dem virtuellen Computer erstellten 4688-Ereignisse und speichert sie in der Suche.

Der Agent aktiviert auch die Datensammlung für [Adaptive Anwendungssteuerungen](security-center-adaptive-application.md), und Security Center konfiguriert eine lokale AppLocker-Richtlinie im Überwachungsmodus, um alle Anwendungen zuzulassen. Diese Richtlinie bewirkt, dass AppLocker Ereignisse generiert, die dann von Security Center gesammelt und genutzt werden können. Es ist wichtig zu beachten, dass diese Richtlinie nicht auf Computern konfiguriert wird, auf denen bereits eine AppLocker-Richtlinie konfiguriert ist. 

Wenn Security Center verdächtige Aktivitäten auf dem virtuellen Computer erkennt, wird der Kunde per E-Mail benachrichtigt, sofern [Sicherheitskontaktinformationen](security-center-provide-security-contact-details.md) bereitgestellt wurden. Außerdem wird eine Warnung auf dem Sicherheitswarnungsdashboard von Security Center angezeigt.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Wird Security Center mit einem OMS-Gateway funktionieren?

Ja. Azure Security Center nutzt Azure Monitor zum Erfassen von Daten von Azure-VMs und -Servern mithilfe des Log Analytics-Agents.
Um die Daten zu sammeln, muss jede VM und jeder Server über HTTPS mit dem Internet verbunden sein. Die Verbindung kann direkt, über einen Proxy oder über das [OMS-Gateway](../azure-monitor/platform/gateway.md) hergestellt werden.


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Wirkt sich der Monitoring Agent auf die Leistung der Server aus?

Der Agent beansprucht eine äußerst geringe Menge von Systemressourcen und sollten nur eine geringe Auswirkung auf die Leistung haben. Weitere Informationen zu Auswirkungen auf die Leistung, zum Agent und zur Erweiterung finden Sie unter [Planungs- und Betriebshandbuch](security-center-planning-and-operations-guide.md#data-collection-and-storage).




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png