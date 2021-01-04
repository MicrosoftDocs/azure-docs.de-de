---
title: 'Erstellen eines Verbindungsmonitors: Azure-Portal'
titleSuffix: Azure Network Watcher
description: In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal in Verbindungsmonitor einen Monitor erstellen.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: a6ba9c02b384c1b30977d962659983d600c7b2ac
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545719"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>Erstellen eines Monitors in Verbindungsmonitor mit dem Azure-Portal

In diesem Artikel erfahren Sie, wie Sie Verbindungsmonitor verwenden, um die Kommunikation zwischen Ihren Ressourcen zu überwachen. In diesem Artikel wird das Erstellen eines Monitors über das Azure-Portal beschrieben. Verbindungsmonitor unterstützt Hybrid- und Azure-Cloudbereitstellungen.


## <a name="before-you-begin"></a>Vorbereitung 

In Verbindungsmonitoren, die Sie mit dem Verbindungsmonitor-Feature erstellen, können Sie sowohl lokale Computer als auch Azure-VMs als Quellen hinzufügen. Mit diesen Verbindungsmonitoren kann auch die Konnektivität mit Endpunkten überwacht werden. Die Endpunkte können sich in Azure oder unter einer beliebigen anderen URL oder IP-Adresse befinden.

Hier finden Sie einige Definitionen für den Einstieg:

* **Verbindungsmonitorressource**. Eine regionsspezifische Azure-Ressource. Alle folgenden Entitäten sind Eigenschaften einer Verbindungsmonitorressource.
* **Endpoint** (Endpunkt): Eine Quelle oder ein Ziel, die bzw. das an Konnektivitätsprüfungen beteiligt ist. Beispiele für Endpunkte sind:
    * Azure-VMs.
    * Virtuelle Azure-Netzwerke
    * Azure-Subnetze
    * Lokale Agents
    * Lokale Subnetze
    * Lokale benutzerdefinierte Netzwerke, die mehrere Subnetze enthalten
    * URLs und IPs
* **Testkonfiguration**. Eine protokollspezifische Konfiguration für einen Test. Je nach ausgewähltem Protokoll können Sie den Port, Schwellenwerte, die Testhäufigkeit und weitere Parameter definieren.
* **Testgruppe**. Die Gruppe, die Quellendpunkte, Zielendpunkte und Testkonfigurationen enthält. Ein Verbindungsmonitor kann mehrere Testgruppen enthalten.
* **Test:** Die Kombination aus einem Quellendpunkt, einem Zielendpunkt und einer Testkonfiguration. Ein Test ist die differenzierteste Ebene, auf der Überwachungsdaten verfügbar sind. Die Überwachungsdaten umfassen den Prozentsatz von Überprüfungen mit Fehlern und die Roundtripzeit.

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="Diagramm, das einen Verbindungsmonitor zeigt und die Beziehung zwischen Testgruppen und Tests definiert.":::


## <a name="create-a-connection-monitor"></a>Erstellen eines Verbindungsmonitors

Erstellen Sie wie folgt über das Azure-Portal in Verbindungsmonitor einen Monitor:

1. Navigieren Sie auf der Startseite des Azure-Portals zu **Network Watcher**.
1. Wählen Sie im linken Bereich im Abschnitt **Überwachung** die Option **Verbindungsmonitor** aus.

   Es werden alle Verbindungsmonitore angezeigt, die in Verbindungsmonitor erstellt wurden. Zum Anzeigen der Verbindungsmonitore, die im klassischen Verbindungsmonitor erstellt wurden, wechseln Sie zur Registerkarte **Verbindungsmonitor**.

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Screenshot: In Verbindungsmonitor erstellte Verbindungsmonitore":::
   
    
1. Wählen Sie im Dashboard **Verbindungsmonitor** oben links die Option **Erstellen** aus.

   

1. Geben Sie auf der Registerkarte **Grundlagen** die Informationen zu Ihrem Verbindungsmonitor ein: 
   * **Name des Verbindungsmonitors**: Geben Sie einen Namen für Ihren Verbindungsmonitor ein. Verwenden Sie die Standardbenennungsregeln für Azure-Ressourcen.
   * **Abonnement**: Wählen Sie ein Abonnement für Ihren Verbindungsmonitor aus.
   * **Region**: Wählen Sie eine Region für Ihren Verbindungsmonitor aus. Dabei kommen nur die Quell-VMs infrage, die in dieser Region erstellt wurden.
   * **Arbeitsbereichskonfiguration**: Wählen Sie einen benutzerdefinierten Arbeitsbereich oder den Standardarbeitsbereich aus. Ihr Arbeitsbereich enthält Ihre Überwachungsdaten.
       * Aktivieren Sie das Kontrollkästchen, um den Standardarbeitsbereich zu verwenden. 
       * Wenn Sie einen benutzerdefinierten Arbeitsbereich auswählen möchten, deaktivieren Sie das Kontrollkästchen. Wählen Sie dann das Abonnement und die Region für Ihren benutzerdefinierten Arbeitsbereich aus. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Screenshot, der die Registerkarte „Grundlagen“ im Verbindungsmonitor zeigt.":::
   
1. Wählen Sie unten auf der Registerkarte die Option **Weiter: Testgruppen** aus.

1. Fügen Sie Quellen, Ziele und Testkonfigurationen in Ihren Testgruppen hinzu. Informationen zum Einrichten Ihrer Testgruppen finden Sie unter [Erstellen von Testgruppen im Verbindungsmonitor](#create-test-groups-in-a-connection-monitor). 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Screenshot, die Registerkarte „Testgruppen“ im Verbindungsmonitor zeigt.":::

1. Wählen Sie unten auf der Registerkarte die Option **Weiter: Warnungen erstellen**. Informationen zum Erstellen von Warnungen finden Sie unter [Erstellen von Warnungen im Verbindungsmonitor](#create-alerts-in-connection-monitor).

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="Screenshot, der die Registerkarte „Warnung erstellen“ zeigt.":::

1. Wählen Sie unten auf der Registerkarte die Option **Weiter: Überprüfen + erstellen**.

1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die grundlegenden Informationen und Testgruppen, bevor Sie den Verbindungsmonitor erstellen. Wenn Sie den Verbindungsmonitor bearbeiten müssen, können Sie hierzu zu den entsprechenden Registerkarten zurückkehren. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="Screenshot, der die Registerkarte „Überprüfen und erstellen“ im Verbindungsmonitor zeigt.":::
   > [!NOTE] 
   > Auf der Registerkarte **Überprüfen und erstellen** werden die monatlichen Kosten für die jeweilige Verbindungsmonitor-Phase angezeigt. Derzeit werden in der Spalte **Aktuelle Kosten/Monat** keine Kosten angezeigt. Sobald der Verbindungsmonitor allgemein verfügbar ist, wird in dieser Spalte eine monatliche Gebühr angegeben. 
   > 
   > Auch während der ersten Verbindungsmonitor-Phase fallen Gebühren für die Log Analytics-Erfassung an.

1. Wenn Sie den Verbindungsmonitor erstellen möchten, wählen Sie unten auf der Registerkarte **Überprüfen + erstellen** die Option **Erstellen** aus.

Der Verbindungsmonitor erstellt die Verbindungsmonitorressource im Hintergrund.

## <a name="create-test-groups-in-a-connection-monitor"></a>Erstellen von Testgruppen in einem Verbindungsmonitor

Jede Testgruppe in einem Verbindungsmonitor enthält Quellen und Ziele, die auf Netzwerkparameter getestet werden. Sie werden mithilfe von Testkonfigurationen auf den Prozentsatz von Überprüfungen mit Fehlern und die Roundtripzeit getestet.

Wenn Sie eine Testgruppe in einem Verbindungsmonitor erstellen möchten, geben Sie im Azure-Portal Werte für die folgenden Felder an:

* **Testgruppe deaktivieren**: Sie können dieses Kontrollkästchen aktivieren, um die Überwachung für alle in der Testgruppe angegebenen Quellen und Ziele zu deaktivieren. Diese Auswahl ist standardmäßig deaktiviert.
* **Name**: Geben Sie Ihrer Testgruppe einen Namen.
* **Quellen**: Sie können sowohl Azure-VMs als auch lokale Computer als Quellen angeben, wenn Agents darauf installiert sind. Informationen zum Installieren eines Agents für Ihre Quelle finden Sie unter [Installieren von Überwachungs-Agents](./connection-monitor-overview.md#install-monitoring-agents).
   * Wählen Sie zum Auswählen von Azure-Agents die Registerkarte **Azure-Endpunkte** aus. Hier werden nur VMs angezeigt, die an die Region gebunden sind, die Sie beim Erstellen des Verbindungsmonitors angegeben haben. Standardmäßig sind VMs in den Abonnements gruppiert, denen Sie angehören. Diese Gruppen sind reduziert. 
   
       Sie können von der **Abonnement** ebene einen Drilldown auf andere Hierarchieebenen ausführen:

      **Abonnement** > **Ressourcengruppe** > **VNETs** > **Subnetz** > **VMs mit Agents**

      Außerdem lässt sich der Selektor **Gruppieren nach** ändern, sodass die Struktur mit einer beliebigen anderen Ebene beginnt. Wenn Sie z. B. nach virtuellem Netzwerk gruppieren, werden die VMs mit Agents in der Hierarchie **VNET** > **Subnetz** > **VMs mit Agents** angezeigt.

       Wenn Sie ein VNET, ein Subnetz oder eine einzelne VM auswählen, wird die entsprechende Ressourcen-ID als Endpunkt festgelegt. Standardmäßig sind alle virtuellen Computer im ausgewählten VNET oder Subnetz, die über die Azure Network Watcher-Erweiterung verfügen, an der Überwachung beteiligt. Um den Bereich zu verkleinern, wählen Sie entweder bestimmte Subnetze oder Agents aus, oder Sie ändern den Wert der Eigenschaft „Bereich“. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Screenshot, der den Bereich „Quellen hinzufügen“ und die Registerkarte „Azure-Endpunkte“ im Verbindungsmonitor zeigt.":::

   * Zum Auswählen lokaler Agents wählen Sie die Registerkarte **Nicht-Azure-Endpunkte** aus. Standardmäßig sind Agents in Arbeitsbereichen nach Region gruppiert. Für alle diese Arbeitsbereiche ist der Netzwerkleistungsmonitor konfiguriert. 
   
       Wenn Sie dem Arbeitsbereich den Netzwerkleistungsmonitor hinzufügen müssen, rufen Sie ihn aus dem [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) ab. Informationen zum Hinzufügen des Netzwerkleistungsmonitors finden Sie unter [Überwachungslösungen in Azure Monitor](../azure-monitor/insights/solutions.md). 
   
       Unter **Verbindungsmonitor erstellen** ist auf der Registerkarte **Grundlagen** die Standardregion ausgewählt. Wenn Sie die Region ändern, können Sie Agents aus Arbeitsbereichen in der neuen Region auswählen. Sie können ein/en oder mehrere Agents oder Subnetze auswählen. In der Ansicht **Subnetz** können Sie bestimmte IP-Adressen für die Überwachung auswählen. Wenn Sie mehrere Subnetze hinzufügen, wird ein benutzerdefiniertes lokales Netzwerk mit dem Namen **OnPremises_Network_1** erstellt. Sie können den Selektor **Gruppieren nach** ändern, um nach Agents zu gruppieren.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Screenshot, der den Bereich „Quellen hinzufügen“ und die Registerkarte „Nicht-Azure-Endpunkte“ im Verbindungsmonitor zeigt.":::

   * Um zuletzt verwendete Endpunkte auszuwählen, können Sie die Registerkarte **Zuletzt verwendeter Endpunkt** verwenden. 
   
   * Wenn Sie die Einrichtung von Quellen abgeschlossen haben, wählen Sie am unteren Rand der Registerkarte **Fertig** aus. Sie können weiterhin grundlegende Eigenschaften wie den Endpunktnamen bearbeiten, indem Sie den Endpunkt in der Ansicht **Testgruppe erstellen** auswählen. 

* **Ziele:** Sie können die Konnektivität mit einer Azure-VM, einem lokalen Computer oder anderen Endpunkten (öffentliche IP-Adresse, URL oder FQDN) überwachen, indem Sie diese als Ziele festlegen. In einer einzelnen Testgruppe können Sie Azure-VMs, lokale Computer, Office 365-URLs, Dynamics 365-URLs und benutzerdefinierte Endpunkte hinzufügen.

    * Wenn Sie Azure-VMs als Ziele auswählen möchten, wechseln Sie zur Registerkarte **Azure-Endpunkte**. Die Azure-VMs sind standardmäßig in einer Abonnementhierarchie gruppiert, die sich in der Region befindet, die Sie unter **Verbindungsmonitor erstellen** auf der Registerkarte **Grundlagen** ausgewählt haben. Sie können die Region ändern und Azure-VMs aus der neuen Region auswählen. Anschließend können Sie von der **Abonnement** ebene einen Drilldown auf andere Hierarchieebenen ausführen, wie Sie es auch beim Festlegen der Azure-Endpunkte der Quelle können.

      Sie können VNETs, Subnetze oder einzelne VMs auswählen, wie Sie es auch beim Festlegen der Azure-Endpunkte der Quelle können. Wenn Sie ein VNET, ein Subnetz oder eine einzelne VM auswählen, wird die entsprechende Ressourcen-ID als Endpunkt festgelegt. Standardmäßig sind alle virtuellen Computer im ausgewählten VNET oder Subnetz, die über die Network Watcher-Erweiterung verfügen, an der Überwachung beteiligt. Um den Bereich zu verkleinern, wählen Sie entweder bestimmte Subnetze oder Agents aus, oder Sie ändern den Wert der Eigenschaft „Bereich“. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<Screenshot, der den Bereich „Ziele hinzufügen“ und die Registerkarte „Azure-Endpunkte“ zeigt.>":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<Screenshot, der den Bereich „Ziele hinzufügen“ auf Abonnementebene zeigt.>":::
       
    
    * Um Nicht-Azure-Agents als Ziele auszuwählen, wählen Sie die Registerkarte **Nicht-Azure-Endpunkte** aus. Standardmäßig sind Agents in Arbeitsbereichen nach Region gruppiert. Für alle diese Arbeitsbereiche ist der Netzwerkleistungsmonitor konfiguriert. 
    
      Wenn Sie dem Arbeitsbereich den Netzwerkleistungsmonitor hinzufügen müssen, rufen Sie ihn aus dem Azure Marketplace ab. Informationen zum Hinzufügen des Netzwerkleistungsmonitors finden Sie unter [Überwachungslösungen in Azure Monitor](../azure-monitor/insights/solutions.md). 

      Unter  **Verbindungsmonitor erstellen** ist auf der Registerkarte  **Grundlagen**  die Standardregion ausgewählt. Wenn Sie die Region ändern, können Sie Agents aus Arbeitsbereichen in der neuen Region auswählen. Sie können ein/en oder mehrere Agents oder Subnetze auswählen. In der Ansicht **Subnetz** können Sie bestimmte IP-Adressen für die Überwachung auswählen. Wenn Sie mehrere Subnetze hinzufügen, wird ein benutzerdefiniertes lokales Netzwerk mit dem Namen **OnPremises_Network_1** erstellt.  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Screenshot, der den Bereich „Ziele hinzufügen“ und die Registerkarte „Nicht-Azure-Endpunkte“ zeigt.":::
    
    * Um öffentliche Endpunkte als Ziele auszuwählen, wählen Sie die Registerkarte **Externe Adressen** aus. Die Liste der Endpunkte umfasst die Office 365- und Dynamics 365-Test-URLs, die nach Namen gruppiert sind. Sie können auch Endpunkte auswählen, die in anderen Testgruppen im selben Verbindungsmonitor erstellt wurden. 
    
        Zum Hinzufügen eines Endpunkts wählen Sie oben rechts die Option **Endpunkt hinzufügen** aus. Geben Sie dann einen Endpunktnamen und eine URL, eine IP-Adresse oder einen FQDN an.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Screenshot, der zeigt, wo Sie öffentliche Endpunkte als Ziele im Verbindungsmonitor hinzufügen können.":::

    * Um zuletzt verwendete Endpunkte auszuwählen, wechseln Sie zur Registerkarte  **Zuletzt verwendeter Endpunkt** .
    * Wenn Sie die Auswahl von Zielen abgeschlossen haben, wählen Sie **Fertig** aus. Sie können weiterhin grundlegende Eigenschaften wie den Endpunktnamen bearbeiten, indem Sie den Endpunkt in der Ansicht **Testgruppe erstellen** auswählen. 

* **Testkonfigurationen**: Sie können einer Testgruppe eine oder mehrere Testkonfigurationen hinzufügen. Sie erstellen eine neue Testkonfiguration, indem Sie die Registerkarte **Neue Konfiguration** verwenden. Oder Sie fügen eine Testkonfiguration aus einer anderen Testgruppe im selben Verbindungsmonitor von der Registerkarte **Vorhandene auswählen** hinzu.

    * **Name der Testkonfiguration**: Geben Sie der Testkonfiguration einen Namen.
    * **Protokoll:** Wählen Sie **TCP**, **ICMP** oder **HTTP** aus. Wenn Sie HTTP in HTTPS ändern möchten, wählen Sie **HTTP** als Protokoll und dann **443** als Port aus.
        * **TCP-Testkonfiguration erstellen**: Dieses Kontrollkästchen wird nur angezeigt, wenn Sie in der Liste **Protokoll** die Option **HTTP** ausgewählt haben. Aktivieren Sie dieses Kontrollkästchen, um eine weitere Testkonfiguration zu erstellen, die dieselben Quellen und Ziele verwendet, die Sie an anderer Stelle in der Konfiguration angegeben haben. Die neue Testkonfiguration erhält den Namen **\<name of test configuration>_networkTestConfig**.
        * **Traceroute deaktivieren**: Dieses Kontrollkästchen ist gültig, wenn das Protokoll TCP oder ICMP ist. Aktivieren Sie dieses Kontrollkästchen, damit Quellen weder die Topologie noch Hop-by-Hop-Roundtripzeit ermitteln.
    * **Zielport**: Sie können einen Zielport Ihrer Wahl angeben.
        * **Am Port lauschen**: Dieses Kontrollkästchen gilt, wenn das Protokoll TCP ist. Aktivieren Sie dieses Kontrollkästchen, um den ausgewählten TCP-Port zu öffnen, wenn er nicht bereits geöffnet ist. 
    * **Testhäufigkeit**: In dieser Liste geben Sie an, wie oft die Quellen die Ziele über das angegebene Protokoll und den angegebenen Port pingen. Sie können Intervalle von 30 Sekunden, 1 Minute, 5 Minuten, 15 Minuten oder 30 Minuten auswählen. Wählen Sie **Benutzerdefiniert** aus, um eine andere Häufigkeit einzugeben, die zwischen 30 Sekunden und 30 Minuten liegt. Die Quellen testen die Konnektivität mit den Zielen auf Grundlage des von Ihnen ausgewählten Werts. Wenn Sie z. B. 30 Sekunden auswählen, überprüfen die Quellen die Konnektivität mit dem Ziel mindestens einmal alle 30 Sekunden.
    * **Schwellenwert für erfolgreichen Vorgang**: Sie können Schwellenwerte für die folgenden Netzwerkparameter festlegen:
       * **Überprüfungen mit Fehlern**: Legen Sie den Prozentsatz der Überprüfungen fest, die Fehler aufweisen können, wenn Quellen die Konnektivität mit Zielen anhand der von Ihnen angegebenen Kriterien überprüfen. Beim TCP- oder ICMP-Protokoll kann der Prozentsatz der Überprüfungen mit Fehlern dem Prozentsatz der Paketverluste gleichgesetzt werden. Beim HTTP-Protokoll stellt dieser Wert den Prozentsatz von HTTP-Anforderungen dar, die keine Antwort erhielten.
       * **Roundtripzeit**: Legen Sie die Roundtripzeit in Millisekunden fest, die von Quellen zum Herstellen einer Verbindung mit dem Ziel über die Testkonfiguration benötigt werden darf.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Screenshot, der zeigt, wo Sie eine Testkonfiguration im Verbindungsmonitor einrichten können.":::
       
## <a name="create-alerts-in-connection-monitor"></a>Erstellen von Warnungen im Verbindungsmonitor

Sie können Warnungen für fehlgeschlagene Tests einrichten, die auf den in Testkonfigurationen festgelegten Schwellenwerten basieren.

Wenn Sie Warnungen für einen Verbindungsmonitor erstellen möchten, geben Sie im Azure-Portal Werte für diese Felder an: 

- **Warnung erstellen**: Sie können dieses Kontrollkästchen aktivieren, um eine Metrikwarnung in Azure Monitor zu erstellen. Wenn Sie dieses Kontrollkästchen aktivieren, werden die anderen Felder für die Bearbeitung aktiviert. Zusätzliche Gebühren für die Warnung fallen an, basierend auf den [Preisen für Warnungen](https://azure.microsoft.com/pricing/details/monitor/). 

- **Bereich** > **Ressource** > **Hierarchie**: Diese Werte werden basierend auf den Werten, die auf der Registerkarte **Grundlagen** angegeben sind, automatisch ausgefüllt.

- **Bedingungsname**: Die Warnung wir für die `Test Result(preview)`-Metrik erstellt. Wenn das Ergebnis des Verbindungsmonitortests fehlerhaft ist, wird die Warnungsregel ausgelöst. 

- **Aktionsgruppenname**: Sie können Ihre E-Mail-Adresse direkt eingeben, oder Sie können Warnungen über Aktionsgruppen erstellen. Wenn Sie Ihre E-Mail-Adresse direkt eingeben, wird eine Aktionsgruppe namens **NPM Email ActionGroup** erstellt. Die E-Mail-ID wird dieser Aktionsgruppe hinzugefügt. Wenn Sie sich für die Verwendung von Aktionsgruppen entscheiden, müssen Sie eine zuvor erstellte Aktionsgruppe auswählen. Wie Sie eine Aktionsgruppe erstellen, erfahren Sie unter [Aktionsgruppen im Azure-Portal erstellen](../azure-monitor/platform/action-groups.md). Nachdem die Warnung erstellt wurde, können Sie [Ihre Warnungen verwalten](../azure-monitor/platform/alerts-metric.md#view-and-manage-with-azure-portal). 

- **Name der Warnungsregel**: Der Name des Verbindungsmonitors.

- **Regel beim Erstellen aktivieren**: Aktivieren Sie dieses Kontrollkästchen, um die Warnungsregel auf Grundlage der Bedingung zu aktivieren. Deaktivieren Sie dieses Kontrollkästchen, wenn Sie die Regel erstellen möchten, ohne Sie zu aktivieren. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="Screenshot, der die Registerkarte „Erstellen“ im Verbindungsmonitor zeigt.":::

## <a name="scale-limits"></a>Skalierungslimits

Für Verbindungsmonitore gelten diese Skalierungslimits:

* Maximale Anzahl von Verbindungsmonitoren pro Abonnement und Region: 100
* Maximale Anzahl von Testgruppen pro Verbindungsmonitor: 20
* Maximale Anzahl von Quellen und Zielen pro Verbindungsmonitor: 100
* Maximale Anzahl von Testkonfigurationen pro Verbindungsmonitor: 2 über das Azure-Portal

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Überwachungsdaten analysieren und Warnungen festlegen](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Erfahren Sie, wie Sie [Probleme in Ihrem Netzwerk diagnostizieren](./connection-monitor-overview.md#diagnose-issues-in-your-network).