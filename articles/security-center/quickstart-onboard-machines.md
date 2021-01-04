---
title: Verbinden Ihrer Nicht-Azure-Computer mit Azure Security Center
description: Erfahren Sie, wie Sie Ihre Nicht-Azure-Computer mit Security Center verbinden.
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 572c657dd44db73b1401c72f0750fe8e000985ac
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734353"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>Verbinden Ihrer Nicht-Azure-Computer mit Security Center

Security Center kann den Sicherheitsstatus Ihrer Azure-fremden Computer überwachen. Dazu müssen diese jedoch mit Azure verbunden werden.

Sie können Azure-fremde Computer auf eine der folgenden Arten verbinden:

- Verwenden von Servern mit Azure Arc-Unterstützung (**empfohlen**)
- Auf den Security Center-Seiten im Azure-Portal (**Erste Schritte** und **Bestand**)

Beide Methoden werden auf dieser Seite beschrieben.

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Hinzufügen eines Azure-fremden Computers mit Azure Arc

Die Verwendung von Servern mit Azure Arc-Unterstützung ist die bevorzugte Methode zum Hinzufügen Ihrer Azure-fremden Computer zu Azure Security Center.

Ein Computer mit Servern mit Azure Arc-Unterstützung wird zu einer Azure-Ressource und wird in Security Center wie Ihre anderen Azure-Ressourcen mit Empfehlungen angezeigt.

Außerdem bieten Server mit Azure Arc-Unterstützung erweiterte Funktionen, wie z. B. die Möglichkeit, Gastkonfigurationsrichtlinien auf dem Computer zu aktivieren, den Log Analytics-Agent als Erweiterung bereitzustellen, die Bereitstellung mit anderen Azure-Diensten zu vereinfachen usw. Einen Überblick über die Vorteile finden Sie unter [Unterstützte Szenarien](../azure-arc/servers/overview.md#supported-scenarios).

Erfahren Sie mehr über [Server mit Azure Arc-Unterstützung](../azure-arc/servers/overview.md).

**So stellen Sie Azure Arc bereit**

- Für einen Computer führen Sie die Schritte in [Schnellstart: Verbinden eines Hybridcomputers mit Servern mit Azure Arc-Unterstützung](../azure-arc/servers/learn/quick-enable-hybrid-vm.md) aus.
- Informationen zum Herstellen einer Verbindung zwischen mehreren Computern und Servern mit Arc-Unterstützung im großen Stil finden Sie unter [Verbinden von Hybridcomputern mit Azure im großen Stil](../azure-arc/servers/onboard-service-principal.md).

> [!TIP]
> Wenn Sie unter AWS ausgeführte Computer integrieren, verarbeitet der Security Center-Connector für AWS die Azure Arc-Bereitstellung transparent für Sie. Weitere Informationen finden Sie unter [Verbinden Ihrer AWS-Konten mit Azure Security Center](quickstart-onboard-aws.md).

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Hinzufügen Azure-fremder Computer über das Azure-Portal

1. Öffnen Sie über das Menü von Security Center die Seite **Erste Schritte**.
1. Wählen Sie die Registerkarte **Erste Schritte** aus.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Registerkarte „Erste Schritte“ auf der Seite „Erste Schritte“" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Wählen Sie unter **Nicht-Azure-Server hinzufügen** die Option **Konfigurieren** aus.

    > [!TIP]
    > Sie können auch über die Schaltfläche **Nicht-Azure-Server hinzufügen** auf der Seite **Bestand** die Option „Computer hinzufügen“ öffnen.
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Hinzufügen von Azure-fremden Computern über die Seite „Ressourcenbestand“":::

    Es wird eine Liste Ihrer Log Analytics-Arbeitsbereiche angezeigt. Die Liste enthält, falls zutreffend, den Standardarbeitsbereich, der von Security Center für Sie erstellt wurde, wenn die automatische Bereitstellung aktiviert wurde. Wählen Sie diesen Arbeitsbereich oder einen anderen Arbeitsbereich aus, den Sie verwenden möchten.

    Sie können Computer einem vorhandenen Arbeitsbereich hinzufügen oder einen neuen Arbeitsbereich erstellen.

1. Wählen Sie optional zum Erstellen eines neuen Arbeitsbereichs **Neuen Arbeitsbereich erstellen** aus.

1. Wählen Sie in der Liste der Arbeitsbereiche für den entsprechenden Arbeitsbereich **Server hinzufügen** aus.
    Dadurch wird die Seite **Agent-Verwaltung** geöffnet.

    Wählen Sie hier das entsprechende nachstehende Verfahren aus, je nachdem, welchen Typ von Computern Sie integrieren möchten:

    - [Integrieren Ihrer Azure Stack-VMs](#onboard-your-azure-stack-vms)
    - [Integrieren Ihrer Linux-Computer](#onboard-your-linux-machines)
    - [Integrieren Ihrer Windows-Computer](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-vms"></a>Integrieren Ihrer Azure Stack-VMs

Zum Hinzufügen von Azure Stack-VMs benötigen Sie die Informationen auf der Seite **Agent-Verwaltung**, und Sie müssen für alle virtuellen Computer, die auf Ihrem Azure Stack ausgeführt werden, die VM-Erweiterung für **Azure Monitor-, Update- und Konfigurationsverwaltung** konfigurieren.

1. Kopieren Sie auf der Seite **Agent-Verwaltung** die **Arbeitsbereichs-ID** und den **Primärschlüssel** in Editor.
1. Melden Sie sich bei Ihrem **Azure Stack**-Portal an, und öffnen Sie die Seite **Virtuelle Computer**.
1. Wählen Sie den virtuellen Computer aus, der mit Security Center geschützt werden soll.
    >[!TIP]
    > Informationen zum Erstellen einer VM in Azure Stack finden Sie im [Schnellstart für Windows-VMs ](/azure-stack/user/azure-stack-quick-windows-portal) oder im [Schnellstart für Linux-VMs](/azure-stack/user/azure-stack-quick-linux-portal).
1. Wählen Sie **Erweiterungen**. Die Liste der auf dieser VM installierten VM-Erweiterungen wird angezeigt.
1. Wählen Sie die Registerkarte **Hinzufügen** aus. Im Menü **Neue Ressource** wird eine Liste der verfügbaren VM-Erweiterungen angezeigt.
1. Wählen Sie die Erweiterung **Azure Monitor-, Update- und Konfigurationsverwaltung** und dann **Erstellen** aus. Die Konfigurationsseite **Erweiterung installieren** wird geöffnet.
    >[!NOTE]
    > Falls die Erweiterung **Azure Monitor, Update- und Konfigurationsverwaltung** in Ihrem Marketplace nicht aufgeführt ist, können Sie sich an Ihren Azure Stack-Betreiber wenden, damit sie zur Verfügung gestellt wird.
1. Fügen Sie auf der Konfigurationsseite **Erweiterung installieren** die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel (Primärschlüssel)** ein, die Sie im vorherigen Schritt in Editor kopiert haben.
1. Wählen Sie zum Abschluss der Konfiguration **OK** aus. Als Status der Erweiterung wird **Bereitstellung erfolgreich** angezeigt. Es kann bis zu einer Stunde dauern, bis der virtuelle Computer in Security Center angezeigt wird.

### <a name="onboard-your-linux-machines"></a>Integrieren Ihrer Linux-Computer

Zum Hinzufügen von Linux-Computern benötigen Sie den Befehl wget von der Seite **Agent-Verwaltung**.

1. Kopieren Sie auf der Seite **Agent-Verwaltung** den Befehl **wget** in Editor. Speichern Sie diese Datei an einem Speicherort, auf den auf Ihrem Linux-Computer zugegriffen werden kann.
1. Öffnen Sie die Datei auf Ihrem Linux-Computer mit dem Befehl wget. Wählen Sie den gesamten Inhalt aus, kopieren Sie ihn, und fügen Sie ihn in eine Terminalkonsole ein.
1. Nach Abschluss der Installation können Sie überprüfen, ob der *omsagent* installiert wurde, indem Sie den Befehl *pgrep* ausführen. Der Befehl gibt die PID von *omsagent* zurück.
    Die Protokolle für den Agent finden Sie unter: */var/opt/microsoft/omsagent/\<workspace id>/log/* . Es kann bis zu 30 Minuten dauern, bis der neue Linux-Computer in Security Center angezeigt wird.

### <a name="onboard-your-windows-machines"></a>Integrieren Ihrer Windows-Computer

Zum Hinzufügen von Windows-Computern benötigen Sie die Informationen auf der Seite **Agent-Verwaltung**, und Sie müssen die entsprechende Agent-Datei (32/64 Bit) herunterladen.
1. Klicken Sie auf den Link **Windows-Agent herunterladen** für den entsprechenden Prozessortyp Ihres Computers, um die Setupdatei herunterzuladen.
1. Kopieren Sie auf der Seite **Agent-Verwaltung** die **Arbeitsbereichs-ID** und den **Primärschlüssel** in Editor.
1. Kopieren Sie die heruntergeladene Setupdatei auf den Zielcomputer, und führen Sie sie aus.
1. Führen Sie den Installations-Assistenten aus (**Weiter**, **Ich stimme zu**, **Weiter**, **Weiter**).
    1. Fügen Sie auf der Seite **Azure Log Analytics** die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel (Primärschlüssel)** ein, die Sie in Editor kopiert haben.
    1. Wenn der Computer Berichte an einen Log Analytics-Arbeitsbereich in Azure Government Cloud ausgeben soll, wählen Sie in der Dropdownliste **Azure Cloud** die Option **Azure US-Regierung** aus.
    1. Wenn der Computer über einen Proxyserver mit dem Log Analytics-Dienst kommunizieren muss, klicken Sie auf **Erweitert**, und stellen Sie die URL sowie die Portnummer des Proxyservers bereit.
    1. Wenn Sie alle Konfigurationseinstellungen eingegeben haben, wählen Sie **Weiter** aus.
    1. Überprüfen Sie auf der Seite **Bereit zum Installieren** die anzuwendenden Einstellungen, und wählen Sie **Installieren** aus.
    1. Wählen Sie auf der Seite **Die Konfiguration wurde erfolgreich abgeschlossen** die Option **Fertig stellen** aus.

Nach Abschluss wird **Microsoft Monitoring Agent** in der **Systemsteuerung** angezeigt. Sie können hier Ihre Konfiguration überprüfen und sicherstellen, dass der Agent verbunden ist.

Weitere Informationen zum Installieren und Konfigurieren des Agents finden Sie unter [Verbinden von Windows-Computern](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

::: zone-end

## <a name="verifying"></a>Überprüfen

Herzlichen Glückwunsch! Nun können Sie Ihre Azure- und Nicht-Azure-Computer an einem Ort anzeigen. Öffnen Sie die [Seite für den Ressourcenbestand](asset-inventory.md), und filtern Sie nach den entsprechenden Ressourcentypen. Die Typen werden durch diese Symbole unterschieden:

  ![ASC-Symbol für Azure-fremde Computer](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Nicht-Azure-Computer

  ![ASC-Symbol für Azure-Computer](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

  ![ASC-Symbol für Azure Arc-Server](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Server mit Azure Arc-Unterstützung:

## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde gezeigt, wie Sie in Azure Security Center Ihre Nicht-Azure-Computer hinzufügen. Um ihren Status zu überwachen, verwenden Sie die Inventartools wie auf der folgenden Seite erläutert:

- [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md)