---
title: 'Tutorial: Überwachen der Netzwerkkommunikation über das Azure-Portal'
description: In diesem Tutorial erfahren Sie, wie Sie die Netzwerkkommunikation zwischen zwei virtuellen Computern mit der Verbindungsmonitorfunktion von Azure Network Watcher überwachen.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: b734c4cdec022f2bf43ee7123b06331f67c10390
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570845"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Tutorial: Überwachen der Netzwerkkommunikation zwischen zwei virtuellen Computern über das Azure-Portal

> [!NOTE]
> In diesem Tutorial geht es um den Verbindungsmonitor (klassisch). Testen Sie den neuen und verbesserten [Verbindungsmonitor](connection-monitor-overview.md), um eine verbesserte Konnektivitätsüberwachung zu erhalten.

> [!IMPORTANT]
> Ab dem 1. Juli 2021 können Sie keine neuen Verbindungsmonitore in Verbindungsmonitor (klassisch) mehr hinzufügen, vorhandene Verbindungsmonitore, die vor dem 1. Juli 2021 erstellt wurden, können Sie jedoch weiterhin verwenden. [Migrieren Sie vor dem 29. Februar 2024 vom Verbindungsmonitor (klassisch) zum neuen Verbindungsmonitor](migrate-to-connection-monitor-from-connection-monitor-classic.md) in Azure Network Watcher, um Dienstunterbrechungen im Zusammenhang mit Ihren aktuellen Workloads zu minimieren.

Erfolgreiche Kommunikation zwischen einem virtuellen Computer (VM) und einem Endpunkt, z.B. einer anderen VM, kann für Ihre Organisation wichtig sein. In manchen Fällen werden Konfigurationsänderungen eingeführt, die die Kommunikation unterbrechen können. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von zwei virtuellen Computern
> * Überwachen der Kommunikation zwischen virtuellen Computern mit der Verbindungsmonitorfunktion des Network Watchers
> * Generieren von Warnungen zu Verbindungsmonitormetriken
> * Diagnostizieren eines Problems bei der Kommunikation zwischen zwei VMs und Informationen zu seiner Lösung

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.



## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-vms"></a>Virtuelle Computer erstellen

Erstellen Sie zwei virtuelle Computer.

### <a name="create-the-first-vm"></a>Erstellen des ersten virtuellen Computers

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie **Compute** und dann ein Betriebssystem aus. In diesem Tutorial wird **Windows Server 2016 Datacenter**.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **OK**:

    |Einstellung|Wert|
    |---|---|
    |Name|myVm1|
    |Benutzername| Geben Sie den gewünschten Benutzernamen ein.|
    |Kennwort| Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
    |Subscription| Wählen Sie Ihr Abonnement aus.|
    |Resource group| Klicken Sie auf **Neu erstellen**, und geben Sie **myResourceGroup** ein.|
    |Standort| Wählen Sie **USA, Osten** aus.|

4. Wählen Sie eine Größe für den virtuellen Computer aus, und klicken Sie dann auf **Auswählen**.
5. Wählen Sie unter **Einstellungen** die Option **Erweiterungen** aus. Wählen Sie **Erweiterung hinzufügen** und dann **Network Watcher-Agent für Windows** aus, wie in der folgenden Abbildung gezeigt:

    ![Erweiterung für den Network Watcher-Agent](./media/connection-monitor/nw-agent-extension.png)

6. Wählen Sie unter **Network Watcher-Agent für Windows** die Option **Erstellen** aus, unter **Erweiterung installieren** die Option **OK** und dann unter **Erweiterungen** die Option **OK**.
7. Akzeptieren Sie die Standardwerte für die verbleibenden **Einstellungen**, und wählen Sie **OK** aus.
8. Wählen Sie auf der Seite **Zusammenfassung** unter **Erstellen** die Option **Erstellen**, um die Bereitstellung des virtuellen Computers zu starten.

### <a name="create-the-second-vm"></a>Erstellen des zweiten virtuellen Computers

Führen Sie die Schritte in [Erstellen des ersten virtuellen Computers](#create-the-first-vm) erneut mit folgenden Änderungen aus:

|Schritt|Einstellung|Wert|
|---|---|---|
| 1 | Auswählen einer **Ubuntu Server**-Version |                                                                         |
| 3 | Name                                  | myVm2                                                                   |
| 3 | Authentifizierungsart                   | Fügen Sie Ihren öffentlichen SSH-Schlüssel ein, oder wählen Sie **Kennwort** aus, und geben Sie ein Kennwort ein. |
| 3 | Resource group                        | Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup** aus.                 |
| 6 | Erweiterungen                            | **Network Watcher-Agent für Linux**                                             |

Die Bereitstellung des virtuellen Computers dauert einige Minuten. Warten Sie, bis die Bereitstellung des virtuellen Computers abgeschlossen ist, bevor Sie mit den weiteren Schritten fortfahren.

## <a name="create-a-connection-monitor"></a>Erstellen eines Verbindungsmonitors

Erstellen Sie einen Verbindungsmonitor zum Überwachen der Kommunikation über TCP-Port 22 von *myVm1* mit *myVm2*.

1. Wählen Sie im Portal auf der linken Seite **Alle Dienste** aus.
2. Beginnen Sie mit der Eingabe von *Network Watcher* im Feld **Filter**. Wählen Sie **Network Watcher** aus, wenn der Begriff in den Suchergebnissen angezeigt wird.
3. Wählen Sie unter **ÜBERWACHUNG** die Option **Verbindungsmonitor** aus.
4. Wählen Sie **+ Hinzufügen**.
5. Geben Sie die Informationen zu der Verbindung, die Sie überwachen möchten, ein, oder wählen Sie sie aus, und wählen Sie dann **Hinzufügen** aus. Im dem Beispiel, das im folgenden Bild dargestellt wird, wird die Verbindung zwischen den virtuellen Computern *myVm1* und *myVm2* über Port 22 überwacht:

    | Einstellung                  | Wert               |
    | ---------                | ---------           |
    | Name                     | myVm1-myVm2(22)     |
    | `Source`                   |                     |
    | Virtueller Computer          | myVm1               |
    | Destination              |                     |
    | Wählen Sie einen virtuellen Computer aus. |                     |
    | Virtueller Computer          | myVm2               |
    | Port                     | 22                  |

    ![Hinzufügen eines Verbindungsmonitors](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Anzeigen eines Verbindungsmonitors

1. Führen Sie die in [Erstellen eines Verbindungsmonitors](#create-a-connection-monitor) beschriebenen Schritte 1 bis 3 aus, um die Verbindungsüberwachung anzuzeigen. Sie sehen eine Liste der vorhandenen Verbindungsmonitore, wie in der folgenden Abbildung gezeigt:

    ![Verbindungsmonitore](./media/connection-monitor/connection-monitors.png)

2. Wählen Sie den Monitor mit dem Namen **myVm1-myVm2(22)** aus, wie in der vorherigen Abbildung dargestellt, um Details für den Monitor anzuzeigen, wie in der nächsten Abbildung gezeigt:

    ![Monitordetails](./media/connection-monitor/vm-monitor.png)

    Beachten Sie die folgenden Informationen:

    | Element                     | Wert                      | Details                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Erreichbar                  | Informiert Sie darüber, ob der Endpunkt erreichbar ist.|
    | DURCHSCHN. ROUNDTRIPZEIT          | Informiert Sie über die Roundtripzeit zum Herstellen der Verbindung, in Millisekunden. Der Verbindungsmonitor prüft die Verbindung alle 60 Sekunden, sodass Sie die Latenz im Laufe der Zeit überwachen können.                                         |
    | Hops                     | Der Verbindungsmonitor Informiert Sie über die Hops zwischen den beiden Endpunkten. In diesem Beispiel besteht die Verbindung zwischen zwei virtuellen Computern im selben virtuellen Netzwerk, es gibt also nur einen Hop zur IP-Adresse 10.0.0.5. Wenn bestehende System- oder benutzerdefinierte Routen Datenverkehr zwischen den VMs z.B. über ein VPN-Gateway oder eine virtuelle Netzwerkappliance leiten, werden zusätzliche Hops aufgelistet.                                                                                                                         |
    | STATUS                   | Die grünen Häkchen an jedem Endpunkt informieren Sie darüber, dass jeder Endpunkt fehlerfrei ist.    ||

## <a name="generate-alerts"></a>Generieren von Warnungen

Warnungen werden von Warnungsregeln in Azure Monitor erstellt und können in regelmäßigen Abständen automatisch gespeicherte Abfragen oder benutzerdefinierte Protokollsuchen ausführen. Eine generierte Warnung kann automatisch eine oder mehrere Aktionen ausführen, z.B. die Benachrichtigung einer Person oder das Starten eines anderen Prozesses. Beim Festlegen einer Warnungsregel bestimmt die jeweils betroffene Ressource die Liste mit den verfügbaren Metriken, die Sie zum Generieren von Warnungen verwenden können.

1. Wählen Sie im Azure-Portal den Dienst **Monitor** und dann **Warnungen** > **Neue Warnungsregel**.
2. Klicken Sie auf **Ziel auswählen**, und wählen Sie dann die gewünschten Ressourcen aus. Wählen Sie das **Abonnement** aus, und legen Sie **Ressourcentyp** fest, um nach dem zu verwendenden Verbindungsmonitor zu filtern.

    ![Fenster „Warnungen“ mit ausgewähltem Ziel](./media/connection-monitor/set-alert-rule.png)
1. Nachdem Sie eine gewünschte Ressource ausgewählt haben, können Sie **Kriterien hinzufügen** wählen. Der Network Watcher enthält [Metriken zum Erstellen von Warnungen](../azure-monitor/alerts/alerts-metric-near-real-time.md#metrics-and-dimensions-supported). Legen Sie **Verfügbare Signale** auf die Metriken ProbesFailedPercent und AverageRoundtripMs fest:

    ![Seite „Warnungen“ mit ausgewählten Signalen](./media/connection-monitor/set-alert-signals.png)
1. Geben Sie die Warnungsdetails an, z.B. Name, Beschreibung und Schweregrad der Warnungsregel. Sie können der Warnung auch eine Aktionsgruppe hinzufügen, um die Antwort auf die Warnung zu automatisieren und anzupassen.

## <a name="view-a-problem"></a>Anzeigen eines Problems

Standardmäßig ermöglicht Azure die Kommunikation über alle Ports zwischen virtuellen Computern im selben virtuellen Netzwerk. Im Laufe der Zeit könnten Sie oder eine Person in Ihrer Organisation Standardregeln von Azure außer Kraft setzen und so versehentlich einen Kommunikationsfehler auslösen. Führen Sie die folgenden Schritte aus, um ein Kommunikationsproblem zu erzeugen, und zeigen Sie den Verbindungsmonitor dann erneut an:

1. Geben Sie oben im Portal im Suchfeld *myResourceGroup* ein. Wenn die Ressourcengruppe **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie sie aus.
2. Wählen Sie die Netzwerksicherheitsgruppe **myVm2-nsg** aus.
3. Wählen Sie **Eingangssicherheitsregeln** und dann **Hinzufügen** aus, wie in der folgenden Abbildung gezeigt:

    ![Eingangssicherheitsregeln](./media/connection-monitor/inbound-security-rules.png)

4. Die Standardregel, die Kommunikation zwischen allen VMs in einem virtuellen Netzwerk zulässt, ist die Regel mit dem Namen **AllowVnetInBound**. Erstellen Sie eine Regel mit einer höheren Priorität (niedrigere Zahl) als die Regel **AllowVnetInBound**, die eingehende Kommunikation über Port 22 verweigert. Wählen Sie die folgenden Informationen aus, bzw. geben Sie sie ein, übernehmen Sie die verbleibenden Standardeinstellungen, und wählen Sie dann **Hinzufügen** aus:

    | Einstellung                 | Wert          |
    | ---                     | ---            |
    | Zielportbereiche | 22             |
    | Aktion                  | Verweigern           |
    | Priority                | 100            |
    | Name                    | DenySshInbound |

5. Da der Verbindungsmonitor in Intervallen von 60 Sekunden prüft, warten Sie einige Minuten, und wählen Sie auf der linken Seite des Portals **Network Watcher**, dann **Verbindungsmonitor** und schließlich erneut den Monitor **myVm1-myVm2(22)** aus. Die Ergebnisse unterscheiden sich nun, wie in der folgenden Abbildung gezeigt:

    ![Monitor mit Details zum Fehler](./media/connection-monitor/vm-monitor-fault.png)

    In der Statusspalte für die **myvm2529**-Netzwerkschnittstelle erkennen Sie ein rotes Ausrufezeichensymbol.

6. Um zu erfahren, warum sich der Status geändert hat, wählen Sie 10.0.0.5 in der vorherigen Abbildung aus. Der Verbindungsmonitor informiert Sie über den Grund für den Kommunikationsfehler: *Der Datenverkehr wurde aufgrund der folgenden NSG-Regel blockiert: UserRule_DenySshInbound*.

    Wenn Sie nicht wussten, dass jemand die Sicherheitsregel implementiert hat, die Sie in Schritt 4 erstellt haben, würden Sie vom Verbindungsmonitor erfahren, dass die Regel das Kommunikationsproblem verursacht. Sie könnten die Regel dann ändern, außer Kraft setzen oder entfernen, um die Kommunikation zwischen den VMs wiederherzustellen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit allen ihren Ressourcen, wenn Sie sie nicht mehr benötigen:

1. Geben Sie im oben im Portal im Feld *Suche* die Zeichenfolge **myResourceGroup** ein. Wenn **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie für **Geben Sie den Ressourcengruppennamen ein:** den Namen *myResourceGroup* ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, eine Verbindung zwischen zwei VMs zu überwachen. Sie haben erfahren, dass eine Netzwerksicherheits-Gruppenregel die Kommunikation mit einem virtuellen Computer verhindert hat. Informationen zu allen anderen Antworten, die der Verbindungsmonitor zurückgeben kann, finden Sie unter [Antworttypen](network-watcher-connectivity-overview.md#response). Sie können auch eine Verbindung zwischen einem virtuellen Computer, einem vollständig qualifizierten Domänennamen, einem Uniform Resource Identifier oder einer IP-Adresse überwachen.

An einem gewissen Punkt werden Sie feststellen, dass Ressourcen in einem virtuellen Netzwerk nicht mit Ressourcen in anderen Netzwerken kommunizieren können, die durch ein virtuelles Azure-Netzwerkgateway verbunden sind. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie ein Problem mit einem Gateway für virtuelle Netzwerke diagnostizieren.

> [!div class="nextstepaction"]
> [Diagnostizieren von Problemen mit der Kommunikation zwischen Netzwerken](diagnose-communication-problem-between-networks.md)
