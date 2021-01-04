---
title: Schützen von Azure VMware Solution-VMs mit Azure Security Center-Integration
description: Erfahren Sie, wie Sie Ihre Azure VMware Solution-VMs über ein einziges Dashboard in Azure Security Center mit den nativen Sicherheitstools von Azure schützen.
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 53669f2988a7ff7ab1150b155a65c7a187c6f1c8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369843"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Schützen von Azure VMware Solution-VMs mit Azure Security Center-Integration

Die nativen Sicherheitstools von Azure bieten eine sichere Infrastruktur für eine hybride Umgebung aus Azure, Azure VMware Solution und lokalen VMs. In diesem Artikel erfahren Sie, wie Sie Azure-Tools für die Sicherheit in einer hybriden Umgebung einrichten. Sie verwenden verschiedene Tools, um verschiedene Arten von Bedrohungen zu identifizieren und zu beseitigen.

## <a name="azure-native-services"></a>Native Azure-Dienste

Im Folgenden finden Sie eine kurze Übersicht über die einzelnen nativen Azure-Dienste:

- **Log Analytics-Arbeitsbereich:** Ein Log Analytics-Arbeitsbereich ist eine spezifische Umgebung zum Speichern von Protokolldaten. Jeder Arbeitsbereich verfügt über ein eigenes Datenrepository und eine eigene Konfiguration. Datenquellen und Lösungen werden so konfiguriert, dass sie ihre Daten in einem bestimmten Arbeitsbereich speichern.
- **Azure Security Center:** Azure Security Center ist ein einheitliches Verwaltungssystem für die Sicherheit der Infrastruktur. Es verstärkt den Sicherheitsstatus von Rechenzentren und bietet erweiterten Bedrohungsschutz über alle hybriden Workloads in der Cloud und der lokalen Umgebung hinweg.
- **Azure Sentinel:** Azure Sentinel ist eine cloudnative Lösung für Security Information & Event Management (SIEM) und die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR). Sie bietet intelligente Sicherheitsanalysen und Threat Intelligence in der gesamten Umgebung. Azure Sentinel ist eine Einzellösung für Warnungserkennung, Einblicke in Bedrohungen, proaktives Hunting und Reaktion auf Bedrohungen.

## <a name="topology"></a>Topologie

![Ein Diagramm mit der Architektur der integrierten Sicherheit in Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

Der Log Analytics-Agent ermöglicht die Sammlung von Protokolldaten aus Azure, Azure VMware Solution und lokalen VMs. Die erfassten Daten werden an Azure Monitor-Protokolle gesendet und in einem Log Analytics-Arbeitsbereich gespeichert. Sie können den Log Analytics-Agent mithilfe der [Unterstützung Arc-fähiger Server für VM-Erweiterungen](../azure-arc/servers/manage-vm-extensions.md) für neue und vorhandene VMs bereitstellen. 

Sobald die Protokolle vom Log Analytics-Arbeitsbereich gesammelt werden, können Sie den Log Analytics-Arbeitsbereich mit Azure Security Center konfigurieren. Azure Security Center bewertet den Risikostatus von Azure VMware Solution-VMs und löst bei jedem kritischen Sicherheitsrisiko eine Warnung aus. Beispielsweise werden fehlende Betriebssystempatches, falsche Sicherheitskonfigurationen und [Endpunktschutz](../security-center/security-center-services.md) bewertet.

Sie können den Log Analytics-Arbeitsbereich mit Azure Sentinel für Folgendes konfigurieren: Erkennen von Warnungen, Einblicke in Bedrohungen, proaktives Hunting und Reaktion auf Bedrohungen. Im obigen Diagramm ist Azure Security Center über den Azure Security Center-Connector mit Azure Sentinel verbunden. Azure Security Center leitet das Sicherheitsrisiko in der Umgebung an Azure Sentinel weiter, damit dort ein Incident erstellt wird und eine Zuordnung zu anderen Bedrohungen erfolgt. Sie können auch eine geplante Abfrageregel erstellen, um unerwünschte Aktivitäten zu erkennen und in Incidents umzuwandeln.

## <a name="benefits"></a>Vorteile

- Sie können die Sicherheit Ihrer aus Azure, Azure VMware Solution und lokalen Diensten bestehenden Hybridumgebung über native Azure-Dienste verwalten.
- Mit einem Log Analytics-Arbeitsbereich können Sie die Daten oder Protokolle an einer zentralen Stelle sammeln und diese Daten dann an verschiedene native Azure-Dienste übermitteln.
- Azure Security Center bietet Sicherheitsfeatures wie Überwachung der Dateiintegrität, Erkennung dateiloser Angriffe, Bewertung von Betriebssystempatches, Bewertung von Fehlkonfigurationen der Sicherheit sowie Bewertung des Endpunktschutzes.
- Azure Sentinel ermöglicht Ihnen Folgendes:
    - Sammeln Sie Daten auf Cloudebene über alle Benutzer, Geräte, Anwendungen und Infrastrukturen hinweg, und zwar sowohl lokal als auch in verschiedenen Clouds.
    - Erkennen Sie bislang unbekannte Bedrohungen.
    - Untersuchen Sie Bedrohungen mit künstlicher Intelligenz, und verfolgen Sie verdächtige Aktivitäten im großen Stil.
    - Reagieren Sie dank der integrierten Orchestrierung und Automatisierung häufiger Aufgaben schnell auf Incidents.

## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

Sie benötigen einen Log Analytics-Arbeitsbereich, um Daten aus verschiedenen Quellen zu sammeln. Näheres hierzu erfahren Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/learn/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Bereitstellen von Security Center und Konfigurieren von Azure VMware Solution-VMs

Azure Security Center ist ein vorkonfiguriertes Tool und erfordert keine Bereitstellung. Suchen Sie im Azure-Portal nach **Security Center**, und wählen Sie den Eintrag aus.

### <a name="enable-azure-defender"></a>Aktivieren von Azure Defender

Azure Defender erweitert den erweiterten Bedrohungsschutz von Azure Security Center auf all Ihre Hybridworkloads – sowohl lokal als auch in der Cloud. Um also Ihre Azure VMware Solution-VMs zu schützen, müssen Sie Azure Defender aktivieren. 

1. Wählen Sie in Security Center die Option **Erste Schritte** aus.

2. Klicken Sie auf die Registerkarte **Upgrade**, und wählen Sie Ihr Abonnement oder Ihren Arbeitsbereich aus. 

3. Wählen Sie **Upgrade ausführen** aus, um Azure Defender zu aktivieren.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Hinzufügen von Azure VMware Solution-VMs zu Security Center

1. Suchen Sie im Azure-Portal nach **Azure Arc**, und wählen Sie den Eintrag aus.

2. Klicken Sie unter „Ressourcen“ auf **Server** und dann auf **+Hinzufügen**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Screenshot der Azure Arc-Seite „Server“ zum Hinzufügen einer Azure VMware Solution-VM zu Azure":::

3. Wählen Sie **Skript generieren** aus.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Screenshot einer Azure Arc-Seite mit der Option zum Hinzufügen eines Servers mithilfe eines interaktiven Skripts"::: 
 
4. Klicken Sie auf der Registerkarte **Voraussetzungen** auf **Weiter**.

5. Geben Sie auf der Registerkarte **Ressourcendetails** die folgenden Informationen ein: 
    - Subscription
    - Resource group
    - Region 
    - Betriebssystem
    - Details zum Proxyserver
    
    Wählen Sie anschließend **Next: Tags**.

6. Klicken Sie auf der Registerkarte **Tags** auf **Weiter**.

7. Klicken Sie auf der Registerkarte **Skript herunterladen und ausführen** auf **Herunterladen**.

8. Geben Sie Ihr Betriebssystem an, und führen Sie das Skript auf Ihrer Azure VMware Solution-VM aus.

## <a name="view-recommendations-and-passed-assessments"></a>Anzeigen von Empfehlungen und bestandenen Bewertungen

1. Wählen Sie in Azure Security Center **Inventar** aus.

2. Wählen Sie als Ressourcentyp **Server – Azure Arc** aus.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Screenshot der Azure Security Center-Seite „Inventar“ mit der unter „Ressourcentyp“ ausgewählten Option „Server – Azure Arc“":::

3. Wählen Sie den Namen Ihrer Ressource aus. Daraufhin wird eine Seite geöffnet, auf die Details zur Sicherheitsintegrität Ihrer Ressource angezeigt werden.

4. Wählen Sie unter **Empfehlungsliste** die Registerkarten **Empfehlungen**, **Bestandene Bewertungen** und **Nicht verfügbare Bewertungen** aus, um die jeweiligen Details anzuzeigen.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Screenshot von Azure Security Center mit Sicherheitsempfehlungen und -bewertungen":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Bereitstellen eines Azure Sentinel-Arbeitsbereichs

Azure Sentinel baut auf einem Log Analytics-Arbeitsbereich auf. Der erste Schritt beim Onboarding von Azure Sentinel besteht darin, den Log Analytics-Arbeitsbereich auszuwählen, den Sie für diesen Zweck einsetzen möchten.

1. Suchen Sie im Azure-Portal nach **Azure Sentinel**, und wählen Sie den Eintrag aus.

2. Klicken Sie auf der Seite mit den Azure Sentinel-Arbeitsbereichen auf **+Hinzufügen**.

3. Wählen Sie den Log Analytics-Arbeitsbereich aus, und klicken Sie auf **Hinzufügen**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Aktivieren des Datensammlers für sicherheitsrelevante Ereignisse in Azure VMware Solution-VMs

Jetzt sind Sie bereit, Azure Sentinel mit Ihren Datenquellen zu verbinden. Dies sind in diesem Fall sicherheitsrelevante Ereignisse.

1. Wählen Sie auf der Seite der Azure Sentinel-Arbeitsbereiche den konfigurierten Arbeitsbereich aus.

2. Wählen Sie unter „Konfiguration“ die Option **Datenconnectors** aus.

3. Wählen Sie in der Spalte „Connectorname“ den Eintrag **Sicherheitsereignisse** aus, und klicken Sie dann auf **Connectorseite öffnen**.

4. Wählen Sie auf der Connectorseite die Ereignisse aus, die Sie streamen möchten, und klicken Sie dann auf **Änderungen übernehmen**.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Screenshot der Azure Sentinel-Seite „Sicherheitsereignisse“, auf der Sie die zu streamenden Ereignisse auswählen können":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Verbinden von Azure Sentinel mit Azure Security Center  

1. Wählen Sie auf der Seite der Azure Sentinel-Arbeitsbereiche den konfigurierten Arbeitsbereich aus.

2. Wählen Sie unter „Konfiguration“ die Option **Datenconnectors** aus.

3. Wählen Sie **Azure Security Center** aus der Liste aus, und klicken Sie dann auf **Connectorseite öffnen**.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Screenshot der Seite „Datenconnectors“ in Azure Sentinel mit der Auswahl zum Herstellen einer Verbindung zwischen Azure Security Center und Azure Sentinel":::

4. Klicken Sie auf **Verbinden**, um Azure Security Center mit Azure Sentinel zu verbinden.

5. Aktivieren Sie **Incident erstellen**, um einen Incident für Azure Security Center zu generieren.

## <a name="create-rules-to-identify-security-threats"></a>Erstellen von Regeln zum Identifizieren von Sicherheitsbedrohungen

Nachdem Sie Datenquellen mit Azure Sentinel verbunden haben, können Sie Regeln erstellen, um basierend auf erkannten Bedrohungen Warnungen zu erstellen. Im folgenden Beispiel wird eine Regel erstellt, um Versuche zu identifizieren, sich mit einem falschen Kennwort bei einem Windows-Server anzumelden.

1. Wählen Sie auf der Übersichtsseite von Azure Sentinel unter „Konfigurationen“ die Option **Analyse** aus.

2. Wählen Sie unter „Konfigurationen“ die Option **Analyse** aus.

3. Klicken Sie auf **+Erstellen**, und wählen Sie in der Dropdownliste den Eintrag **Geplante Abfrageregel** aus.

4. Geben Sie auf der Registerkarte **Allgemein** die erforderlichen Informationen ein.

    - Name
    - BESCHREIBUNG
    - Taktik
    - Schweregrad
    - Status

    Klicken Sie auf **Weiter: Regellogik festlegen >** .

5. Geben Sie auf der Registerkarte **Regellogik festlegen** die erforderlichen Informationen ein.

    - Regelabfrage (gezeigt wird die Beispielabfrage)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Zuordnungsentität
    - Abfrageplanung
    - Benachrichtigungsschwellenwert
    - Ereignisgruppierung
    - Unterdrücken

    Wählen Sie **Weiter** aus.

6. Aktivieren Sie auf der Registerkarte **Incidenteinstellungen** die Option **Incidents aus Warnungen erstellen, die von dieser Analyseregel ausgelöst werden**, und klicken Sie auf **Weiter: Automatisierte Antwort >** .
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Screenshot des Analyseregel-Assistenten zum Erstellen einer neuen Regel in Azure Sentinel; die Option „Incidents aus Warnungen erstellen, die von dieser Analyseregel ausgelöst werden“ ist aktiviert":::

7. Klicken Sie auf **Weiter: Überprüfen >** .

8. Überprüfen Sie die Informationen auf der Registerkarte **Überprüfen + erstellen**, und wählen Sie **Erstellen** aus.

Nach dem dritten fehlerhaften Anmeldeversuch beim Windows-Server löst die erstellte Regel einen Incident für jeden erfolglosen Versuch aus.

## <a name="view-generated-alerts"></a>Anzeigen der generierten Warnungen

Sie können generierte Incidents mit Azure Sentinel anzeigen. Sie können auch Incidents zuweisen und nach der Auflösung schließen – alles innerhalb von Azure Sentinel.

1. Wechseln Sie zur Übersichtsseite von Azure Sentinel.

2. Wählen Sie unter „Bedrohungsmanagement“ die Option **Incidents** aus.

3. Wählen Sie einen Incident aus. Sie können diesen Incident einem Team zur Auflösung zuweisen.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Screenshot der Azure Sentinel-Seite mit Incidents mit ausgewähltem Incident und der Option zum Zuweisen zur Auflösung":::

    Nachdem das Problem behoben wurde, können Sie den Incident schließen.

## <a name="hunt-security-threats-with-queries"></a>Suchen nach Sicherheitsbedrohungen mithilfe von Abfragen

Sie können Abfragen erstellen oder vordefinierte Abfragen in Azure Sentinel verwenden, um Bedrohungen in Ihrer Umgebung zu identifizieren. In den folgenden Schritten wird eine vordefinierte Abfrage ausgeführt.

1. Wechseln Sie zur Übersichtsseite von Azure Sentinel.

2. Wählen Sie unter „Bedrohungsmanagement“ die Option **Hunting** aus. Eine Liste vordefinierter Abfragen wird angezeigt.

3. Wählen Sie eine Abfrage aus, und klicken Sie dann auf **Abfrage ausführen**.

4. Klicken Sie auf **Ergebnisse anzeigen**, um die Ergebnisse zu überprüfen.

### <a name="create-a-new-query"></a>Erstellen einer neuen Abfrage

1.  Wählen Sie unter „Bedrohungsmanagement“ die Option **Hunting** aus, und klicken Sie dann auf **+Neue Abfrage**.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Screenshot der Azure Sentinel-Seite für das Hunting mit hervorgehobener Option „+Neue Abfrage“":::

2. Geben Sie die folgenden Informationen ein, um eine benutzerdefinierte Abfrage zu erstellen.

    - Name
    - BESCHREIBUNG
    - Benutzerdefinierte Abfrage
    - Zuordnung eingeben
    - Taktik
    
3. Wählen Sie **Erstellen** aus. Danach können Sie die erstellte Abfrage auswählen und auf **Abfrage ausführen** und dann auf **Ergebnisse anzeigen** klicken.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie das [Azure Defender-Dashboard](../security-center/azure-defender-dashboard.md) verwenden.
- Informieren Sie sich über alle von [Azure Defender](../security-center/azure-defender.md) angebotenen Schutzfunktionen.
- Erfahren Sie mehr über die [Erweiterte Erkennung von mehrstufigen Angriffen in Azure Sentinel](../azure-monitor/learn/quick-create-workspace.md).
