---
title: Azure Service Fabric-Versionen
description: Versionshinweise für Azure Service Fabric Informationen zu den neuesten Features und Verbesserungen in Service Fabric
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 9960f15f2f91b51b73727ab421eb58dddc5bb2b3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485568"
---
# <a name="service-fabric-releases"></a>Service Fabric-Versionen

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Leitfäden zur Problembehandlung</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Problemverfolgung</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Supportoptionen</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Unterstützte Versionen</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Codebeispiele</a>

Dieser Artikel enthält weitere Informationen zu den neuesten Versionen und Updates für die Service Fabric-Runtime und -SDKs.

## <a name="whats-new-in-service-fabric"></a>Neuigkeiten in Service Fabric

### <a name="service-fabric-72"></a>Service Fabric 7.2 
Wir freuen uns, Ihnen mitteilen zu können, dass die Einführung von Release 7.2 der Service Fabric-Runtime zusammen mit Tools und SDK-Updates in den verschiedenen Azure-Regionen begonnen hat. Die Updates für .NET SDK, Java SDK und Service Fabric-Runtime sind über den Webplattform-Installer, NuGet-Pakete und Maven-Repositorys verfügbar.
## <a name="what-is-new-in-service-fabric-7"></a>Was ist neu in Service Fabric 7.2?
Dieses Release bietet eine Vielzahl von wichtigen Features und Verbesserungen. Hier sehen Sie einige der wichtigsten Features:
## <a name="key-announcements-in-72"></a>Wichtige Ankündigungen in 7.2
- **Vorschau**: [**Verwaltete Service Fabric-Cluster**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) befinden sich nun in der öffentlichen Vorschau. Verwaltete Service Fabric-Cluster sollen die Clusterbereitstellung und -verwaltung vereinfachen, indem die zugrunde liegenden Ressourcen, aus denen sich der Service Fabric-Cluster zusammensetzt, in einer einzelnen ARM-Ressource gekapselt werden. Weitere Einzelheiten finden Sie unter [Übersicht über verwaltete Service Fabric-Cluster](./overview-managed-cluster.md).
- **Vorschau**: [**Die Unterstützung zustandsloser Dienste mit mehr Instanzen als Knoten**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) befindet sich nun in der öffentlichen Vorschau. Eine Platzierungsrichtlinie ermöglicht das Erstellen mehrerer zustandsloser Instanzen einer Partition auf einem Knoten.
- [**FabricObserver (FO) 3.0**](https://aka.ms/sf/fabricobserver) ist nun verfügbar.
    - Sie können FabricObserver jetzt in Linux- und Windows-Clustern ausführen.
    - Sie können jetzt benutzerdefinierte Observer-Plug-Ins erstellen. Weitere Informationen und Code finden Sie in der [Infodatei zum Plug-In](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) und im [Beispielprojekt für das Plug-In](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin).
    - Sie können jetzt sämtliche Observer-Einstellungen über ein Upgrade von Anwendungsparametern ändern. Dies bedeutet, dass Sie FO nicht mehr erneut bereitstellen müssen, um bestimmte Observer-Einstellungen zu ändern. Weitere Informationen finden Sie im [Beispiel](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Unterstützung für Ubuntu 18.04 OneBox-Containerimages**](https://hub.docker.com/_/microsoft-service-fabric-onebox)
- **Vorschau**: [**Schlüsseltresorverweise für Service Fabric-Anwendungen unterstützen **NUR Geheimnisse mit Versionsangabe**. Geheimnisse ohne Versionsangabe werden nicht unterstützt.**](./service-fabric-keyvault-references.md)
- Das SF SDK erfordert das neueste Update 16.7.6 für VS 2019 oder Version 16.8 Preview 4, um neue zustandslose, zustandsbehaftete oder Akteurprojekte in .NET Framework zu erstellen. Wenn Sie nicht über das neueste VS-Update verfügen, verwenden Sie nach dem Erstellen des Dienstprojekts den Paket-Manager, um Microsoft.ServiceFabric.Services (Version 4.2.x) für zustandsbehaftete oder zustandslose Projekte und Microsoft.ServiceFabric.Actors (Version 4.2.x) für Akteurprojekte von nuget.org zu installieren.
- **RunToCompletion:** Service Fabric unterstützt das Konzept der Ausführung bis zum Abschluss für ausführbare Gastanwendungsdateien. Wenn die Ausführung des Replikats mit diesem Update abgeschlossen ist, werden die diesem Replikat zugewiesenen Clusterressourcen freigegeben.
- [**Die Unterstützung der Ressourcengovernance wurde verbessert**](./service-fabric-resource-governance.md), sodass jetzt auch die Angaben für Anforderungen und Grenzwerte für CPU- und Arbeitsspeicherressourcen möglich sind.

### <a name="service-fabric-72-releases"></a>Service Fabric 7.2-Releases
| Veröffentlichungsdatum | Release | Weitere Informationen |
|---|---|---|
| 21. Oktober 2020 | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|

### <a name="service-fabric-71"></a>Service Fabric 7.1
Aufgrund der aktuellen Corona-Pandemie und angesichts der vielen Herausforderungen, vor denen unsere Kunden stehen, stellen wir Version 7.1 zur Verfügung, es erfolgen aber keine automatischen Upgrades – auch nicht für Cluster, die für den Empfang von automatischen Upgrades eingerichtet sind. Wir setzen die automatischen Upgrades bis auf Weiteres aus, um sicherzustellen, dass Kunden Upgrades zu einem für sie geeigneten Zeitpunkt durchführen können. So lassen sich unerwartete Unterbrechungen vermeiden.

Sie können das Upgrade auf Version 7.1 über das [Azure-Portal](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) oder über eine [Azure Resource Manager-Bereitstellung](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template) durchführen.

Service Fabric-Cluster, für die automatische Upgrades aktiviert sind, empfangen das Upgrade auf Version 7.1 automatisch, sobald wir die Standardprozedur für das Rollout wieder aufnehmen. Wir werden den Start des Standardrollouts auf der [Website der Tech Community für Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) ankündigen.
Wir haben [hier](./service-fabric-versions.md#supported-versions) auch Updates zum Datum des Supportendes für Hauptversionen von 6.5 bis 7.1 veröffentlicht. 

## <a name="what-is-new-in-service-fabric-71"></a>Was ist neu in Service Fabric 7.1?
Wir freuen uns, das nächste Release von Service Fabric ankündigen zu können. Dieses Release bietet eine Vielzahl von wichtigen Features und Verbesserungen. Hier sehen Sie einige der wichtigsten Features:
## <a name="key-announcements"></a>Wichtige Ankündigungen
- **Allgemeine Verfügbarkeit** der [**verwalteten Service Fabric-Identitäten für Service Fabric-Anwendungen**](./concepts-managed-identity.md)
- [**Unterstützung von Ubuntu 18.04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Vorschau: Unterstützung kurzlebiger Betriebssystemdatenträger für VM-Skalierungsgruppen**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)**: Bei kurzlebigen Betriebssystemdatenträgern handelt es sich um Speicher, der auf dem lokalen virtuellen Computer erstellt und nicht in der externen Azure Storage-Instanz gespeichert wird. Sie werden für alle Service Fabric-Knotentypen (primär und sekundär) empfohlen, da kurzlebige Betriebssystemdatenträger im Vergleich zu herkömmlichen persistenten Betriebssystemdatenträgern Folgendes ermöglichen:
      -  Reduzieren der Lese-/Schreibwartezeit auf dem Betriebssystemdatenträger
      -  Schnellere Vorgänge zum Zurücksetzen/Durchführen eines Re-Imagings für die Knotenverwaltung
      -  Senken der Gesamtkosten (Die Datenträger sind kostenlos, und es fallen keine zusätzlichen Speicherkosten an.)
- Unterstützung für die Deklaration von [**Dienstendpunktzertifikaten von Service Fabric-Anwendungen anhand des allgemeinen Namens des Antragsstellers**](./service-fabric-service-manifest-resources.md).
- [**Unterstützung für Integritätstests bei containerbasierten Diensten**](./probes-codepackage.md): Unterstützung für einen Livetestmechanismus für containerbasierte Anwendungen. Der Livetest unterstützt die Ermittlung der Aktivität von containerisierten Anwendungen. Wenn sie nicht rechtzeitig reagieren, führt dies zu einem Neustart. 
- [**Unterstützung für Initialisierercodepakete**](./initializer-codepackages.md) für [Container](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) und [auf Gastcomputern ausführbare](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) Anwendungen. Dies ermöglicht die Ausführung von Codepaketen (z. B. Containern) in einer bestimmten Reihenfolge, um Dienstpakete zu initialisieren.
- **FabricObserver und ClusterObserver** sind zustandslose Anwendungen, die Service Fabric-Telemetriedaten zu verschiedenen Aspekten eines SF-Clusters erfassen. Beide Anwendungen können in Windows-Produktionsclustern bereitgestellt werden, um umfangreiche Telemetriedaten zu erfassen. Unterstützung für ApplicationInsights, EventSource und LogAnalytics ist implementiert.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer) – wird auf allen Knoten ausgeführt, generiert Integritätsereignisse und gibt Telemetriedaten aus, wenn vom Benutzer konfigurierte Schwellenwerte der Ressourcennutzung erreicht sind. Dieses Release enthält verschiedene Erweiterungen in Bezug auf Überwachung, Datenverwaltung, Details zu Integritätsereignissen und strukturierte Telemetrie.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) – wird auf einem Knoten ausgeführt und erfasst Telemetriedaten zur Integrität auf Clusterebene. In diesem Release überwacht ClusterObserver auch den Knotenstatus und gibt Telemetriedaten aus, wenn ein Knoten länger als für einen vom Benutzer festgelegten Zeitraum heruntergefahren ist, deaktiviert wird oder deaktiviert ist.

### <a name="improve-application-life-cycle-experience"></a>Verbessern der Features für den Anwendungslebenszyklus

- **[Vorschau: Ausgleich anfordern](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** : Bei geplanten Wartungsmaßnahmen für einen Dienst – z. B. bei Dienstupgrades oder Knotendeaktivierung – möchten Sie den Diensten ermöglichen, einen ordnungsgemäßen Verbindungsausgleich durchzuführen. Dieses Feature fügt in der Dienstkonfiguration eine Verzögerung beim Schließen von Instanzen hinzu. Bei geplanten Vorgängen entfernt Service Fabric die Adresse des Diensts aus der Ermittlung und wartet diese Zeitspanne ab, bevor der Dienst heruntergefahren wird.
- **[Automatisches Erkennen und automatischer Lastenausgleich von Subclustern](./cluster-resource-manager-subclustering.md)** : Subcluster werden erstellt, wenn Dienste mit verschiedenen Platzierungseinschränkungen über eine gemeinsame [Lastmetrik](./service-fabric-cluster-resource-manager-metrics.md) verfügen. Wenn die Lasten auf den verschiedenen Knotengruppen signifikant voneinander abweichen, geht der Service Fabric-Clusterressourcen-Manager davon aus, dass kein ausreichender Lastenausgleich für den Cluster durchgeführt wurde, auch wenn aufgrund der Platzierungseinschränkungen das bestmögliche Gleichgewicht vorliegt. Daher wird versucht, einen neuen Lastenausgleich für den Cluster durchzuführen, was potenziell zu unnötigen Dienstverlagerungen führen kann (da das „Ungleichgewicht“ nicht wesentlich verbessert werden kann). Ab diesem Release versucht der Clusterressourcen-Manager, diese Arten von Konfigurationen automatisch zu erkennen und zu ermitteln, wann das Ungleichgewicht durch Verlagerungen behoben werden kann und wann nichts unternommen werden sollte, da keine wesentliche Verbesserung erzielt werden kann.  
- [**Unterschiedliche Verlagerungskosten für sekundäre Replikate**](./service-fabric-cluster-resource-manager-movement-cost.md): Wir haben einen neuen Wert für Verlagerungskosten eingeführt: VeryHigh. Dieser sorgt in einigen Szenarien für mehr Flexibilität, da definiert werden kann, ob für sekundäre Replikate separate Verlagerungskosten angewendet werden sollen.
- Ein [**Livetestmechanismus**](./probes-codepackage.md) für containerbasierte Anwendungen wurde aktiviert. Der Livetest unterstützt die Ermittlung der Aktivität von containerisierten Anwendungen. Wenn sie nicht rechtzeitig reagieren, führt dies zu einem Neustart.
- [**Ausführung bis Abschluss oder einmalige Ausführung für Dienste**](./run-to-completion.md)**

### <a name="image-store-improvements"></a>Verbesserungen am Image Store
 - Service Fabric 7.1 verwendet **standardmäßig benutzerdefinierte Transportmechanismen zur sicheren Dateiübertragung zwischen Knoten**. Die Abhängigkeit von SMB-Dateifreigaben wurde in Version 7.1 entfernt. Die gesicherten SMB-Dateifreigaben sind auf Knoten noch vorhanden, die Image Store-Dienstreplikate enthalten, sodass Kunden sich entscheiden können, die Standardeinstellung zu deaktivieren und ein Upgrade oder ein Downgrade auf die alte Version auszuführen.
       
 ### <a name="reliable-collections-improvements"></a>Verbesserungen bei zuverlässigen Sammlungen

- [**Unterstützung für reinen In-Memory-Speicher für zustandsbehaftete Dienste unter Verwendung von zuverlässigen Sammlungen**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): Flüchtige zuverlässige Sammlungen ermöglichen die persistente Speicherung von Daten auf einem Datenträger, um Dauerhaftigkeit gegenüber umfangreichen Ausfällen zu gewährleisten. Sie können z. B. für Workloads wie replizierten Cache verwendet werden, bei dem gelegentliche Datenverluste toleriert werden können. Basierend auf den [Einschränkungen flüchtiger zuverlässiger Sammlungen](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) wird dies für Workloads empfohlen, die keine Persistenz benötigen, für Dienste, die die seltenen Vorkommen von Quorumverlusten behandeln.
- [**Vorschau: Service Fabric-Sicherungs-Explorer**](https://github.com/microsoft/service-fabric-backup-explorer): Um die Verwaltung der Sicherungen von zuverlässigen Sammlungen für zustandsbehaftete Service Fabric-Anwendungen zu vereinfachen, ermöglicht der Service Fabric-Sicherungs-Explorer Folgendes:
    - Überwachen und Überprüfen der Inhalte von zuverlässigen Sammlungen
    - Aktualisieren des aktuellen Zustands in einer konsistenten Ansicht
    - Erstellen von Sicherungen der aktuellen Momentaufnahme der zuverlässigen Sammlungen
    - Beheben von Datenbeschädigungen
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7.1-Releases
| Veröffentlichungsdatum | Release | Weitere Informationen |
|---|---|---|
| 20. April 2020 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Versionshinweise](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16. Juni 2020 | [Microsoft Azure Service Fabric 7.1 First Refresh](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517). | [Anmerkungen zu dieser Version](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20. Juli 2020 | [Microsoft Azure Service Fabric 7.1 Second Refresh](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12. August 2020 | [Microsoft Azure Service Fabric 7.1 Third Refresh](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10. September 2020 | [Microsoft Azure Service Fabric 7.1 Fourth Refresh](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|

### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 ist jetzt verfügbar! Sie können über das Azure-Portal oder über eine Azure Resource Manager-Bereitstellung ein Update auf Version 7.0 durchführen. Aufgrund von Kundenfeedback zu Releases am Jahresende beginnen wir mit der automatischen Aktualisierung von Clustern, die für den Empfang automatischer Upgrades eingerichtet wurden, erst im Januar.
Im Januar wird die Standardprozedur für das Rollout fortgesetzt, und Cluster mit aktivierten automatischen Upgrades empfangen das Update auf Version 7.0 automatisch. Wir werden eine weitere Ankündigung bereitstellen, bevor das Rollout beginnt.
Wir aktualisieren auch unsere geplanten Veröffentlichungstermine, um anzugeben, dass diese Richtlinie berücksichtigt wird. Hier finden Sie Updates zu unseren zukünftigen [Releasezeitplänen](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Dies ist die neueste Version von Service Fabric mit wichtigen Features und umfangreichen Verbesserungen.

### <a name="key-announcements"></a>Wichtige Ankündigungen
 - [**KeyVaultReference-Unterstützung für Anwendungsgeheimnisse (Vorschau)**](./service-fabric-keyvault-references.md): Service Fabric-Anwendungen, für die [verwaltete Identitäten](./concepts-managed-identity.md) aktiviert sind, können nun direkt auf eine Key Vault-Geheimnis-URL als Umgebungsvariable, Anwendungsparameter oder Containerrepository-Anmeldeinformationen verweisen. Service Fabric löst das Geheimnis mithilfe der verwalteten Identität der Anwendung automatisch auf. 
     
- **Verbesserte Upgradesicherheit für zustandslose Dienste**: Um die Verfügbarkeit während eines Anwendungsupgrades zu gewährleisten, haben wir neue Konfigurationen eingeführt, um die [Mindestanzahl von Instanzen für zustandslose Dienste](/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) zu definieren, die als verfügbar erachtet werden. Zuvor war dieser Wert für alle Dienste 1 und konnte nicht geändert werden. Mit dieser neuen Sicherheitsüberprüfung pro Dienst können Sie sicherstellen, dass Ihre Dienste bei Anwendungsupgrades, Clusterupgrades und anderen Wartungsarbeiten, die von den Integritäts- und Sicherheitsüberprüfungen von Service Fabric abhängig sind, eine Mindestanzahl von aktiven Instanzen beibehalten.
  
- [**Resourceneinschränkungen für Benutzerdienste**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): Benutzer können Ressourceneinschränkungen für die Benutzerdienste auf einem Knoten einrichten, um Szenarien wie die Erschöpfung von Ressourcen der Systemdienste von Service Fabric zu verhindern. 
  
- [**Sehr hohe Dienstverschiebungskosten**](./service-fabric-cluster-resource-manager-movement-cost.md) für einen Replikattyp. Replikate mit sehr hohen Verschiebungskosten werden nur verschoben, wenn eine Einschränkungsverletzung im Cluster vorliegt, die nicht auf andere Weise behoben werden kann. Weitere Informationen dazu, wann die Nutzung von „sehr hohen“ Verschiebungskosten angemessen ist, sowie weitere Überlegungen finden Sie im verknüpften Dokument.
  
-  **Zusätzliche Clustersicherheitsüberprüfungen**: In diesem Release haben wir eine konfigurierbare Quorumsicherheitsüberprüfung für den Seedknoten eingeführt. Auf diese Weise können Sie anpassen, wie viele Seedknoten während des Lebenszyklus von Clustern und in Verwaltungsszenarien verfügbar sein müssen. Vorgänge, die den Cluster unter den konfigurierten Wert bringen würden, werden blockiert. Zurzeit ist der Standardwert immer ein Quorum der Seedknoten. Wenn Sie z.B. 7 Seedknoten verwenden, würde ein Vorgang, der Sie unter 5 Seedknoten bringen würde, standardmäßig blockiert. Mit dieser Änderung können Sie den minimal sicheren Wert auf 6 festlegen, sodass nur ein Seedknoten gleichzeitig ausfallen darf.
   
- Unterstützung für [**Verwaltung des Sicherungs- und Wiederherstellungsdiensts in Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md) hinzugefügt. Dies ermöglicht die folgenden Aktivitäten direkt aus SFX heraus: Ermitteln des Sicherungs- und Wiederherstellungsdiensts, Erstellen von Sicherungsrichtlinien, Aktivieren automatischer Sicherungen, Erstellen von Ad-hoc-Sicherungen, Auslösen von Wiederherstellungsvorgängen und Durchsuchen vorhandener Sicherungen.

- Ankündigung der Verfügbarkeit von [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): Mit diesem Tool können Sie überprüfen, ob die in zuverlässigen Sammlungen verwendeten Typen bei einem parallelen Anwendungsupgrade aufwärts- und abwärtskompatibel sind. Dadurch können Upgradefehler oder Datenverluste und Datenbeschädigungen aufgrund fehlender oder inkompatibler Typen verhindert werden.

- [**Aktivieren stabiler Lesevorgänge für sekundäre Replikate**](./service-fabric-reliable-services-configuration.md#configuration-names-1): Durch stabile Lesevorgänge werden die Rückgaben sekundärer Replikate auf Werte beschränkt, die durch ein Quorum bestätigt wurden.

Dieses Release enthält außerdem viele neue Funktionen und Fehlerbehebungen sowie Unterstützungs-, Zuverlässigkeits- und Leistungsverbesserungen. Die vollständige Liste der Änderungen finden Sie in den [Versionshinweisen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0-Releases

| Veröffentlichungsdatum | Release | Weitere Informationen |
|---|---|---|
| 18. November 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30. Januar 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6\. Februar 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2\. März 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Service Fabric 6.5

Dieses Release umfasst Verbesserungen hinsichtlich Unterstützungsmöglichkeiten, Zuverlässigkeit und Leistung sowie neue Features, Fehlerbehebungen und Verbesserungen, um die Cluster- und Anwendungslebenszyklusverwaltung zu vereinfachen.

> [!IMPORTANT]
> Service Fabric 6.5 ist die endgültige Version mit Unterstützung für Service Fabric-Tools in Visual Studio 2015. Kunden wird empfohlen, in Zukunft auf [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) umzusteigen.

Dies sind die Neuigkeiten in Service Fabric 6.5:

- Service Fabric Explorer enthält einen [Image Store-Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) zum Überprüfen von Anwendungen, die Sie in den Imagespeicher hochgeladen haben.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) Version [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) umfasst viele Verbesserungen für die Selbstdiagnose. Kunden von POA wird empfohlen, auf diese Version umzusteigen.

- [EventStore-Dienst ist standardmäßig für Service Fabric 6.5-Cluster aktiviert](service-fabric-visualizing-your-cluster.md#event-store), es sei denn, Sie haben sich abgemeldet.

- [Replikatlebenszyklusereignisse](service-fabric-diagnostics-event-generation-operational.md#replica-events) wurden für zustandsbehaftete Dienste hinzugefügt.

- [Bessere Sichtbarkeit der Status von Startknoten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), einschließlich Clusterebenenwarnungen, wenn ein Startknoten (Seedknoten) fehlerhaft ist (*Ausgefallen*, *Entfernt* oder *Unbekannt*).

- Das [Service Fabric Application Disaster Recovery-Tool](https://github.com/Microsoft/Service-Fabric-AppDRTool) ermöglicht, dass zustandsbehaftete Service Fabric-Dienste nach einem Notfall im primären SF-Cluster schnell wiederhergestellt werden können. Daten aus dem primären Cluster werden ständig in der sekundären Standbyanwendung über regelmäßige Sicherung und Wiederherstellung synchronisiert.

- Visual Studio-Unterstützung für [Veröffentlichen von .NET Core-Apps in Linux-basierten Clustern](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md) wird für Service Fabric 6.5 (und höhere Versionen) automatisch installiert, wenn Sie ein Upgrade ausführen oder einen neuen Linux-Cluster in Azure erstellen.

- [SFCTL](./service-fabric-cli.md) wird in MacOS/Linux OneBox-Clustern standardmäßig erstellt.

Weitere Informationen finden Sie in den [Versionshinweisen zu Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5-Releases

| Veröffentlichungsdatum | Release | Weitere Informationen |
|---|---|---|
| 11. Juni 2019 | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2\. Juli 2019 | [Azure Service Fabric 6.5 Refresh-Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29. Juli 2019 | [Azure Service Fabric 6.5 Refresh-Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23. August 2019 | [Azure Service Fabric 6.5 Refresh-Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14. Oktober 2019 | [Azure Service Fabric 6.5 Refresh-Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Versionshinweise](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>Vorgängerversionen

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4-Releases

| Veröffentlichungsdatum | Release | Weitere Informationen |
|---|---|---|
| 30. November 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Versionshinweise](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12. Dezember 2018 | [Azure Service Fabric 6.4 Refresh Release jfür Windows-Cluster](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Versionshinweise](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4\. Februar 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Versionshinweise](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4\. März 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Versionshinweise](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8\. April 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Versionshinweise](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2\. Mai 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Versionshinweise](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28. Mai 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Versionshinweise](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)