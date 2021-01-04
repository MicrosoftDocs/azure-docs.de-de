---
title: Hinzufügen von benutzerdefinierten Service Fabric-Integritätsberichten
description: Beschreibt, wie benutzerdefinierte Integritätsberichte an Integritätsentitäten von Azure Service Fabric gesendet werden. Enthält Empfehlungen zum Entwerfen und Implementieren hochwertiger Integritätsberichte.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: 6df434610a8f595ecca7f16e31f8a302373b02f9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001863"
---
# <a name="add-custom-service-fabric-health-reports"></a>Hinzufügen von benutzerdefinierten Service Fabric-Integritätsberichten
Azure Service Fabric bietet ein [Integritätsmodell](service-fabric-health-introduction.md) , das fehlerhafte Cluster- und Anwendungsbedingungen auf bestimmten Entitäten kennzeichnet. Das Integritätsmodell verwendet **Integritäts-Reporter** (Systemkomponenten und Watchdogs). Das Ziel ist die einfache und schnelle Diagnose und Reparatur. Dienstautoren müssen im Vorfeld an die Integrität denken. Jede Bedingung, die zur Beeinträchtigung der Integrität führen kann, sollte gemeldet werden, insbesondere wenn damit die Ursachen von Problemen ermittelt werden können. Die Integritätsinformationen können Zeit und Aufwand beim Debuggen und Untersuchen einsparen. Besonders deutlich wird der Nutzen, wenn der Dienst im gewünschten Umfang in der Cloud ausgeführt wird (private oder Azure-Cloud).

Die Service Fabric-Berichterstatter überwachen Bedingungen, die von Bedeutung sind. Sie erstatten basierend auf ihrer lokalen Anzeige Bericht über diese Bedingungen. Der [Integritätsspeicher](service-fabric-health-introduction.md#health-store) aggregiert die von allen Reportern gesendeten Integritätsdaten, um die globale Integrität der Entitäten zu ermitteln. Das Modell ist umfassend, flexibel und einfach anzuwenden. Die Qualität der Integritätsberichte bestimmt die Genauigkeit der Integritätsanzeige des Clusters. Falsch positive Ergebnisse, die irrtümlicherweise Fehler anzeigen, können Upgrades oder andere Dienste beeinträchtigen, die Integritätsdaten verwenden. Beispiele für solche Dienste sind Reparaturdienste und Alarmmechanismen. Daher sind einige Überlegungen erforderlich, um Bericht bereitzustellen, die wichtige Bedingungen auf bestmögliche Weise erfassen.

Bei der Gestaltung und Implementierung der Integritätsberichterstellung haben Watchdogs und Systemkomponenten folgende Aufgaben:

* Sie definieren, welche Bedingung für sie von Bedeutung ist, wie sie überwacht wird und welche Auswirkungen sie auf die Cluster- oder Anwendungsfunktionalität hat. Auf der Grundlage dieser Informationen entscheiden sie über die Integritätsberichtseigenschaft und den Integritätszustand.
* Sie bestimmen die [Entität](service-fabric-health-introduction.md#health-entities-and-hierarchy) , für die der Bericht gilt.
* Sie bestimmen, wo die Berichterstellung erfolgen soll, entweder innerhalb des Diensts oder über einen internen oder externen Watchdog.
* Sie definieren eine Quelle zum Identifizieren des Berichterstatters.
* Sie wählen eine Strategie für die Berichterstellung (in regelmäßigen Abständen oder bei Übergängen). Empfohlen wird eine Berichterstellung in regelmäßigen Abständen, da sie einen einfacheren Code erfordert und weniger fehleranfällig ist.
* Sie bestimmen, wie lange der Bericht über fehlerhafte Bedingungen im Integritätsspeicher bleibt und wie er gelöscht wird. Auf der Grundlage dieser Informationen entscheiden Sie über die Gültigkeitsdauer des Berichts und über das Verhalten beim Entfernen nach der Ablaufzeit.

Wie bereits erwähnt, kann die Berichterstellung von folgenden Orten aus erfolgen:

* Vom überwachten Service Fabric-Dienstreplikat.
* Von internen Watchdogs, die als Service Fabric-Dienste bereitgestellt werden (etwa ein zustandsloser Service Fabric-Dienst, der Bedingungen überwacht und Berichte ausgibt). Watchdogs können auf allen Knoten bereitgestellt oder dem überwachten Dienst zugeordnet werden.
* Von internen Watchdogs, die auf den Service Fabric-Knoten ausgeführt werden, jedoch *nicht* als Service Fabric-Dienste implementiert werden.
* Von externen Watchdogs, die die Ressource von *außerhalb* des Service Fabric-Clusters überwachen (etwa ein Überwachungsdienst wie Gomez).

> [!NOTE]
> Standardmäßig enthält das Cluster von den Systemkomponenten gesendete Integritätsberichte. Weitere Informationen finden Sie unter [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Die Benutzerberichte müssen für bereits vom System erstellte [Integritätsentitäten](service-fabric-health-introduction.md#health-entities-and-hierarchy) gesendet werden.
> 
> 

Sobald das Design für die Integritätsberichterstellung feststeht, können Integritätsberichte problemlos gesendet werden. Mithilfe von [FabricClient](/dotnet/api/system.fabric.fabricclient) können Sie Berichte zur Integrität erstellen, wenn der Cluster nicht [sicher](service-fabric-cluster-security.md) ist oder der Fabric-Client über Administratorrechte verfügt. Die Berichterstellung kann über die API mit [FabricClient.HealthManager.ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), über PowerShell oder über REST erfolgen. Zur Verbesserung der Leistung fassen Konfigurationselemente Berichte zusammen.

> [!NOTE]
> Integritätsberichte sind synchronisiert und repräsentieren nur die auf der Clientseite durchgeführten Überprüfungen. Die Tatsache, dass der Bericht vom Integritätsclient oder von Objekten vom Typ `Partition` oder `CodePackageActivationContext` akzeptiert wird, bedeutet nicht, dass er im Speicher angewendet wird. Er wird asynchron gesendet und möglicherweise mit anderen Berichten zusammengefasst. Bei der Verarbeitung auf dem Server kann trotzdem noch ein Fehler auftreten – etwa aufgrund einer veralteten Sequenznummer oder weil die Entität gelöscht wurde, auf die der Bericht angewendet werden muss.
> 
> 

## <a name="health-client"></a>Integritätsclient
Die Integritätsberichte werden über einen Integritätsclient innerhalb des Fabric-Clients an den Integritäts-Manager gesendet. Der Integritäts-Manager speichert Berichte im Integritätsspeicher. Der Integritätsclient kann mit folgenden Einstellungen konfiguriert werden:

* **HealthReportSendInterval:** Die Verzögerung zwischen dem Hinzufügen des Berichts zum Client und dem Senden des Berichts an den Integritäts-Manager. Dadurch können Berichte in einer einzelnen Nachricht zusammengefasst werden, anstatt für jeden Bericht eine eigene Nachricht zu senden. So wird die Leistung verbessert. Standardwert: 30 Sekunden.
* **HealthReportRetrySendInterval:** Das Intervall, in dem der Integritätsclient kumulierte Integritätsberichte erneut an den Integritäts-Manager sendet. Standardwert: 30 Sekunden, Mindestwert: 1 Sekunde
* **HealthOperationTimeout:** Das Zeitlimit für eine Berichtsnachricht, die an den Integritäts-Manager gesendet wurde. Wenn bei einer Nachricht das Zeitlimit überschritten wird, wiederholt der Integritätsclient den Sendevorgang, bis der Integritäts-Manager die Verarbeitung des Berichts bestätigt. Standardwert: 2 Minuten.

> [!NOTE]
> Wenn die Berichte zusammengefasst werden, muss der Fabric-Client mindestens für die Dauer von „HealthReportSendInterval“ aktiv bleiben, um sicherzustellen, dass sie gesendet werden. Wenn die Nachricht verloren geht oder der Integritäts-Manager die Berichte aufgrund von vorübergehenden Fehlern nicht anwenden kann, muss der Fabric-Client länger aktiv bleiben, um einen erneuten Versuch zu ermöglichen.
> 
> 

Bei der Pufferung auf dem Client wird auf die Eindeutigkeit der Berichte geachtet. Beispiel: Wenn ein bestimmter schlechter Reporter pro Sekunde 100 Berichte für die gleiche Eigenschaft der gleichen Entität sendet, werden die Berichte jeweils durch die letzte Version ersetzt. In der Clientwarteschlange ist höchstens ein solcher Bericht vorhanden. Wenn die Batchverarbeitung konfiguriert ist, wird pro Sendeintervall nur ein Bericht an den Integritäts-Manager gesendet. Dieser Bericht ist der zuletzt hinzugefügte Bericht, der den aktuellen Zustand der Entität angibt.
Geben Sie Konfigurationsparameter an, wenn `FabricClient` durch Übergabe von [FabricClientSettings](/dotnet/api/system.fabric.fabricclientsettings) mit den gewünschten Werten für integritätsbezogene Einträge erstellt wird.

Im folgenden Beispiel wird ein Fabric-Client erstellt und angegeben, dass die Berichte unmittelbar nach dem Hinzufügen gesendet werden sollen. Bei Zeitüberschreitungen und Fehlern, bei denen Wiederholungsversuche möglich sind, wird alle 40 Sekunden ein Wiederholungsversuch ausgeführt.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Es wird empfohlen, die Standardeinstellungen für den Fabric-Client mit der Einstellung von 30 Sekunden für `HealthReportSendInterval` beizubehalten. Diese Einstellung stellt eine optimale Leistung aufgrund der Batchverarbeitung sicher. Verwenden Sie für wichtige Berichte, die so schnell wie möglich gesendet werden müssen, in der [FabricClient.HealthClient.ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth)-API `HealthReportSendOptions` mit `true` für „Direkt“. Direkte Berichte umgehen das Batchintervall. Verwenden Sie dieses Flag mit Vorsicht. Die Batchverarbeitung für den Integritätsclient sollte möglichst häufig genutzt werden. Ein direktes Senden ist auch nützlich, wenn der Fabric-Client geschlossen wird (z.B. wenn der Prozess einen ungültigen Zustand ermittelt hat und heruntergefahren werden muss, um Nebeneffekte zu verhindern). Es stellt ein bestmögliches Senden der kumulierte Berichte sicher. Wenn ein Bericht mit dem Flag „Direkt“ hinzugefügt wird, geht der Integritätsclient alle kumulierten Berichte seit dem letzten Sendevorgang in Batchverarbeitung durch.

Beim Erstellen einer Verbindung mit einem Cluster über PowerShell können dieselben Parameter angegeben werden. Das folgende Beispiel startet eine Verbindung mit einem lokalen Cluster:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Ähnlich einer API können Berichte mit dem Schalter `-Immediate` direkt gesendet werden, unabhängig vom Wert `HealthReportSendInterval`.

Bei REST werden die Berichte zum Service Fabric-Gateway gesendet, das über einen internen Fabric-Client verfügt. Standardmäßig ist dieser Client zum Senden von Berichten in Batches alle 30 Sekunden konfiguriert. Sie können das Batchintervall mit der Clusterkonfigurationseinstellung `HttpGatewayHealthReportSendInterval` unter `HttpGateway` ändern. Wie bereits erwähnt, ist eine bessere Option das Senden der Berichte mit `Immediate` auf „TRUE“ festgelegt. 

> [!NOTE]
> Um sicherzustellen, dass nicht autorisierte Dienste keine Integritätsberichte für die im Cluster enthaltenen Entitäten ausgeben, sollten Sie den Server so konfigurieren, dass er Anforderungen nur von abgesicherten Clients akzeptiert. Das für die Berichterstellung verwendete `FabricClient` -Element muss über aktivierte Sicherheitsfunktionen verfügen, um mit dem Cluster (beispielsweise mit Kerberos- oder Zertifikatauthentifizierung) kommunizieren zu können. Erfahren Sie mehr über [Clustersicherheit](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Erstellen von Berichten aus Diensten mit niedriger Berechtigung
Wenn Service Fabric-Dienste keinen Administratorzugriff auf den Cluster haben, können Sie Integritätsberichte für Entitäten aus dem aktuellen Kontext durch `Partition` oder `CodePackageActivationContext` erstellen.

* Verwenden Sie für zustandslose Dienste [IStatelessServicePartition.ReportInstanceHealth](/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) , um einen Bericht über die aktuelle Dienstinstanz zu erstellen.
* Verwenden Sie für zustandsbehaftete Dienste [IStatefulServicePartition.ReportReplicaHealth](/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) , um einen Bericht über das aktuelle Replikat zu erstellen.
* Verwenden Sie [IServicePartition.ReportPartitionHealth](/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) , um einen Bericht über die aktuelle Partitionsentität zu erstellen.
* Verwenden Sie [CodePackageActivationContext.ReportApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) , um einen Bericht über die aktuelle Anwendung zu erstellen.
* Verwenden Sie [CodePackageActivationContext.ReportDeployedApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) , um einen Bericht über die aktuelle Anwendung zu erstellen, die auf dem aktuellen Knoten bereitgestellt wird.
* Verwenden Sie [CodePackageActivationContext.ReportDeployedServicePackageHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth), um einen Bericht über ein Servicepaket der Anwendung zu erstellen, die auf dem aktuellen Knoten bereitgestellt wird.

> [!NOTE]
> Intern enthalten `Partition` und `CodePackageActivationContext` einen Integritätsclient, der mit den Standardeinstellungen konfiguriert ist. Wie für den [Integritätsclient](service-fabric-report-health.md#health-client) erläutert, werden Berichte als Batch verarbeitet und per Timer gesendet. Die Objekte sollten aktiv gehalten werden, damit sie den Bericht senden können.
> 
> 

Sie können beim Senden von Berichten über `Partition` und `CodePackageActivationContext`-Integritäts-APIs `HealthReportSendOptions` angeben. Wenn Sie wichtige Berichte haben, die möglichst bald gesendet werden müssen, verwenden Sie `HealthReportSendOptions` mit `true` für „Direkt“. Direkte Berichte umgehen das Batchintervall des internen Integritätsclients. Wie bereits erwähnt, sollten Sie dieses Flag umsichtig verwenden. Die Batchverarbeitung für den Integritätsclient sollte möglichst häufig genutzt werden.

## <a name="design-health-reporting"></a>Entwerfen der Integritätsberichterstellung
Der erste Schritt zum Generieren hochwertiger Berichte ist das Identifizieren der Bedingungen, welche die Dienstintegrität beeinträchtigen können. Jegliche Bedingung, die beim – oder noch besser vor dem – Auftreten von Problemen im Dienst oder Cluster zu deren Erkennung beiträgt, kann Milliarden Euro einsparen. Zu den Vorteilen gehören weniger Ausfallzeiten, weniger nächtliche Untersuchungen und Behebungen von Problemen sowie eine höhere Kundenzufriedenheit.

Nachdem die Bedingungen identifiziert wurden, müssen Watchdog-Autoren die beste Möglichkeit für deren Überwachung ermitteln, um ein ausgeglichenes Verhältnis zwischen Aufwand und Nützlichkeit zu erzielen. Betrachten Sie beispielsweise einen Dienst, der mithilfe verschiedener temporärer Dateien auf einer Freigabe komplexe Berechnungen durchführt. Ein Watchdog kann die Freigabe überwachen, um sicherzustellen, dass genügend Speicherplatz verfügbar ist. Er könnte auf Benachrichtigungen bezüglich Datei- bzw. Verzeichnisänderungen lauschen. Er könnte eine Warnung ausgeben, wenn ein anfänglicher Schwellenwert erreicht wird, bzw. einen Fehler, wenn die Freigabe voll ist. Bei einer Warnung könnte ein Reparatursystem mit dem Bereinigen von älteren Dateien auf der Freigabe beginnen. Bei einem Fehler könnte ein Reparatursystem das Dienstreplikat auf einen anderen Knoten verschieben. Beachten Sie, wie die Bedingungszustände hinsichtlich der Integrität beschrieben werden: als Zustand einer Bedingung, die als fehlerfrei (OK) oder fehlerhaft (Warnung oder Fehler) angesehen werden kann.

Nachdem die Überwachungsdetails eingerichtet wurden, muss ein Watchdog-Autor ermitteln, wie er den Watchdog implementiert. Wenn die Bedingungen von innerhalb des Diensts bestimmt werden können, kann der Watchdog Teil der überwachten Diensts an sich sein. Der Dienstcode kann beispielsweise die Freigabenutzung überprüfen und dann bei jedem Schreibversuch in eine Datei einen Bericht erstellen. Der Vorteil dieses Ansatzes ist die einfache Berichterstellung. Es muss darauf geachtet werden, dass Watchdogfehler nicht die Funktionalität des Diensts beeinträchtigen.

Die Berichterstellung von innerhalb des überwachten Diensts ist nicht immer möglich. Ein Watchdog innerhalb des Diensts kann möglicherweise die Bedingungen nicht erkennen. Er verfügt möglicherweise nicht über die Logik oder die Daten, um sie zu bestimmen. Der Aufwand zur Überwachung der Bedingungen kann hoch sein. Auch gelten die Bedingungen möglicherweise nicht für einen Dienst, sondern beeinträchtigen stattdessen die Interaktion zwischen Diensten. Als weitere Option können Watchdogs im Cluster als separate Prozesse ausgeführt werden. Die Watchdogs überwachen die Bedingungen und erstellen Berichte, ohne die Hauptdienste in irgendeiner Weise zu beeinträchtigen. Diese Watchdogs können beispielsweise als zustandslose Dienste in dieselbe Anwendung implementiert und auf allen Knoten oder auf denselben Knoten wie der Dienst bereitgestellt werden.

Mitunter ist es jedoch auch nicht möglich, einen Watchdog im Cluster auszuführen. Wenn es sich bei den überwachten Bedingungen um die Verfügbarkeit oder die Funktionalität des Diensts aus der Sicht des Benutzers handelt, empfiehlt es sich, die Watchdogs am selben Ort wie die Benutzerclients auszuführen. Dort können sie die Vorgänge so testen, wie Benutzer sie aufrufen. Sie können z.B. einen Watchdog verwenden, der sich außerhalb des Clusters befindet, Anforderungen an den Dienst ausgibt und die Latenz sowie die Richtigkeit des Ergebnisses überprüft. (Beispiel für einen Rechnerdienst: Gibt 2 + 2 in einem angemessenen Zeitraum 4 zurück?)

Nachdem Sie die Watchdog-Details festgelegt haben, sollten Sie eine Quell-ID auswählen, die den Watchdog eindeutig identifiziert. Wenn im Cluster mehrere Watchdogs mit dem gleichen Typ enthalten sind, müssen sie Berichte zu unterschiedlichen Entitäten erstellen oder im Fall von Berichten zur selben Entität eine unterschiedliche Quell-ID oder Eigenschaft verwenden. Auf diese Weise können ihre Berichte gleichzeitig vorhanden sein. Die Eigenschaft des Integritätsberichts sollte die überwachte Bedingung erfassen. (Im vorangegangenen Beispiel könnte die Eigenschaft **ShareSize** sein.) Wenn mehrere Berichte für die gleiche Bedingung gelten, sollte die Eigenschaft einige dynamische Informationen beinhalten, sodass mehrere Berichte gleichzeitig vorhanden sein können. Beispiel: Wenn mehrere Freigaben überwacht werden müssen, kann der Name der Eigenschaft **ShareSize-Freigabename** lauten.

> [!NOTE]
> Verwenden Sie den Integritätsspeicher *nicht* zum Speichern von Statusinformationen. Hinsichtlich der Integrität sollten nur integritätsbezogene Informationen gemeldet werden, da sie sich auf die Integritätsevaluierung einer Entität auswirken. Der Integritätsspeicher wurde nicht als Allzweckspeicher entwickelt. Er aggregiert mithilfe von Integritätsevaluierungslogik alle Daten im Integritätszustand. Das Senden von Informationen, die sich nicht auf die Integrität beziehen (etwa Statusberichte mit dem Integritätszustand „OK“), wirkt sich nicht auf den aggregierten Integritätszustand aus, kann jedoch die Leistung des Integritätsspeichers beeinträchtigen.
> 
> 

Als Nächstes legen Sie fest, für welche Entität Berichte erstellt werden sollen. In den meisten Fällen wird die Entität durch die Bedingung eindeutig identifiziert. Wählen Sie die Entität mit der bestmöglichen Granularität aus. Wenn sich eine Bedingung auf alle Replikate in einer Partition auswirkt, erstellen Sie Berichte nicht über den Dienst, sonder über die Partition. Es gibt jedoch Ausnahmefälle, in denen weitere Überlegungen erforderlich sind. Wenn sich die Bedingung auf eine Entität wie ein Replikat auswirkt, jedoch der Wunsch besteht, sie für mehr als nur die Replikatgültigkeitsdauer zu kennzeichnen, sollte die Berichterstellung auf der Partition erfolgen. Wenn andernfalls das Replikat gelöscht wird, bereinigt der Integritätsspeicher alle zugehörigen Berichte. Watchdog-Autoren müssen auch die Lebensdauer der Entität und des Berichts berücksichtigen. Es muss klar sein, wann ein Bericht in einem Speicher bereinigt werden soll (beispielsweise wenn ein für eine Entität gemeldeter Fehler nicht mehr zutrifft).

Sehen wir uns ein Beispiel mit den beschriebenen Aspekten an. Stellen Sie sich eine Service Fabric-Anwendung vor, die aus einem zustandsbehafteten, persistenten Masterdienst und zustandslosen sekundären Diensten besteht, die auf allen Knoten bereitgestellt sind (je ein sekundärer Diensttyp für einen Aufgabentyp). Der Master beinhaltet eine Verarbeitungswarteschlange, die von sekundären Diensten auszuführende Befehle enthält. Die sekundären Dienste führen die eingehenden Anforderungen aus und senden Bestätigungssignale zurück. Eine überwachbare Bedingung ist die Länge der Verarbeitungswarteschlange des Masters. Wenn die Länge der Masterwarteschlange einen Schwellenwert erreicht, wird eine Warnung gemeldet. Diese Warnung gibt an, dass die sekundären Dienste die Last nicht bewältigen können. Wenn die Warteschlange die maximale Länge erreicht hat und Befehle abgebrochen werden, wird ein Fehler gemeldet, da der Dienst nicht wiederhergestellt werden kann. Die Berichte können für die Eigenschaft **QueueStatus** erstellt werden. Der Watchdog befindet sich innerhalb des Diensts. Er hat regelmäßig Berichte über das primäre Masterreplikat gesendet. Die Gültigkeitsdauer beträgt 2 Minuten, und der Bericht wird regelmäßig alle 30 Sekunden gesendet. Wenn das primäre Replikat ausfällt, wird der Bericht automatisch aus dem Speicher bereinigt. Wenn das Dienstreplikat aktiv ist, aber blockiert wird oder ein anderes Problem vorliegt, läuft der Bericht im Integritätsspeicher ab. In diesem Fall wird die Entität als fehlerhaft bewertet.

Eine weitere Bedingung, die überwacht werden kann, ist die Ausführungszeit der Aufgabe. Der Master verteilt Aufgaben basierend auf dem Aufgabentyp an die sekundären Dienste. Je nach Design könnte der Master den Aufgabenstatus von den sekundären Diensten abrufen. Er könnte auch warten, bis die sekundären Dienste Bestätigungssignale zurücksenden, wenn sie fertig sind. In letzterem Fall ist darauf zu achten, dass Situationen erkannt werden, in denen sekundäre Dienste ausfallen oder Nachrichten verloren gehen. Eine Option ist, dass der Master eine Ping-Anforderung an denselben sekundären Dienst sendet, der den Status zurücksendet. Wird kein Status empfangen, wertet der Master das als Fehler und plant die Aufgabe neu. Bei diesem Verhalten wird davon ausgegangen, dass die Aufgaben idempotent sind.

Die überwachte Bedingung kann als Warnung interpretiert werden, wenn die Aufgabe nicht innerhalb eines bestimmten Zeitraums ausgeführt wird (**t1**; beispielsweise 10 Minuten). Wird die Aufgabe nicht rechtzeitig abgeschlossen (**t2**; beispielsweise 20 Minuten) kann die überwachte Bedingung als Fehler interpretiert werden. Die Berichterstellung kann auf verschiedene Arten erfolgen:

* Das primäre Masterreplikat gibt regelmäßig Berichte über sich selbst aus. Sie können eine Eigenschaft für alle ausstehenden Aufgaben in der Warteschlange verwenden. Wenn mindestens eine Aufgabe länger dauert, ist der Berichtsstatus für die Eigenschaft **PendingTasks** eine Warnung bzw. ein Fehler. Wenn keine ausstehenden Aufgaben vorhanden sind oder die Ausführung aller Aufgaben gestartet wurde, lautet der Berichtsstatus „OK“. Die Aufgaben sind persistent. Bei einem Ausfall des primären Diensts kann also der neue primäre Dienst weiterhin ordnungsgemäß Berichte erstellen.
* Ein anderer Watchdog-Prozess (in der Cloud oder extern) überprüft von außerhalb basierend auf dem gewünschten Aufgabenergebnis, ob die Aufgaben abgeschlossen wurden. Wenn die Schwellenwerte nicht beachtet werden, wird ein Bericht über den Masterdienst gesendet. Ein Bericht wird auch für jede Aufgabe gesendet, die die Aufgabenkennung enthält (Beispiel: **PendingTask+Aufgabenkennung**). Berichte sollten nur für Fehlerzustände gesendet werden. Legen Sie die Gültigkeitsdauer auf wenige Minuten fest, und konfigurieren Sie die Berichte so, dass sie nach Ablauf der Gültigkeitsdauer entfernt werden, um die Bereinigung zu gewährleisten.
* Der sekundäre Dienst, der eine Aufgabe ausführt, meldet, wenn die Ausführung länger dauert als erwartet. Er erstellt Berichte für die Dienstinstanz über die Eigenschaft **PendingTasks**. Der Bericht ermittelt zwar die fehlerhafte Dienstinstanz, erfasst aber nicht die Situation, in der die Instanz ausfällt. Die Berichte werden dann bereinigt. Es können Berichte über den sekundären Dienst erstellt werden. Wenn der sekundäre Dienst die Aufgabe abschließt, löscht die sekundäre Instanz den Bericht aus dem Speicher. Der Bericht erfasst nicht die Situation, in der die Bestätigungsnachricht verloren gegangen ist und die Aufgabe aus Mastersicht nicht abgeschlossen wurde.

In den oben beschriebenen Fällen werden jedoch Berichte erstellt. Die Erfassung der Berichte erfolgt in der Anwendungsintegrität im Zuge der Integritätsevaluierung.

## <a name="report-periodically-vs-on-transition"></a>Regelmäßig oder bei einem Übergang erstellte Berichte
Mithilfe des Integritätsberichterstellungsmodells können Watchdogs Berichte in regelmäßigen Abständen oder bei Übergängen senden. Watchdog-Berichte sollten regelmäßig erstellt werden, da der Code erheblich einfacher und weniger fehleranfällig ist. Die Watchdogs sollten so einfach wie möglich gehalten werden, um Fehler zu vermeiden, die falsche Berichte auslösen. Falsche *Fehlerberichte* wirken sich auf Integritätsevaluierungen und auf Szenarien in Verbindung mit der Integrität aus, dazu gehören auch Upgrades. Durch falsche Berichte bezüglich der *Fehlerfreiheit* werden Probleme im Cluster ausgeblendet, was nicht erwünscht ist.

Für die regelmäßige Berichterstellung kann der Watchdog mit einem Timer implementiert werden. Bei einem Timerrückruf kann der Watchdog den Zustand überprüfen und basierend auf dem aktuellen Zustand einen Bericht senden. Sie müssen nicht sehen, welcher Bericht zuvor gesendet wurde. Auch das Messaging muss nicht optimiert werden. Der Integritätsclient verfügt über eine Batchverarbeitungslogik zur Verbesserung der Leistung. Solange der Integritätsclient aktiviert ist, führt er intern Wiederholversuche durch, bis der Bericht vom Integritätsspeicher bestätigt wurde oder der Watchdog einen neueren Bericht mit gleicher Entität, Eigenschaft und Quelle generiert.

Die Berichterstellung bei Übergängen erfordert eine sorgfältige Zustandsbehandlung. Der Watchdog überwacht bestimmte Bedingungen und erstellt nur Berichte, wenn sich diese ändern. Der Vorteil dieses Ansatzes: Es werden weniger Berichte benötigt. Nachteilig ist die komplexe Logik des Watchdogs. Der Watchdog muss zudem die Bedingungen oder die Berichte verwalten, damit sie auf Zustandsänderungen überprüft werden können. Bei einem Failover muss auf Berichte geachtet werden, die hinzugefügt aber noch nicht an den Integritätsspeicher gesendet wurden. Die Sequenznummer muss immer zunehmen. Andernfalls werden die Berichte als veraltet abgelehnt. In den seltenen Fällen von Datenverlusten müssen möglicherweise der Zustand des Reporters und des Integritätsspeichers synchronisiert werden.

Berichte zu Übergängen sind für Dienste sinnvoll, die mithilfe von `Partition` oder `CodePackageActivationContext` über sich selbst berichten. Wenn das lokale Objekt (Replikat oder bereitgestelltes Dienstpaket / bereitgestellte Anwendung) entfernt wird, werden auch alle zugehörigen Berichte entfernt. Diese automatische Bereinigung verringert den Bedarf für eine Synchronisierung zwischen Reporter und Integritätsspeicher. Wenn der Bericht sich auf die übergeordnete Partition oder die übergeordnete Anwendung bezieht, muss auf ein Failover geachtet werden, um veraltete Berichte im Integritätsspeicher zu vermeiden. Eine Logik muss hinzugefügt werden, um den richtigen Status aufrechtzuerhalten und den Bericht aus dem Speicher zu löschen, wenn er nicht mehr benötigt wird.

## <a name="implement-health-reporting"></a>Implementieren der Integritätsberichterstellung
Sobald die Entitäts- und Berichtdetails geklärt sind, können Integritätsberichte über die API, PowerShell oder REST gesendet werden.

### <a name="api"></a>API
Für die Berichterstellung über die API müssen Benutzer einen spezifischen Integritätsbericht für den Entitätstyp erstellen, für den die Berichterstellung durchgeführt werden soll. Übergeben Sie den Bericht an einen Integritätsclient. Alternativ können Sie eine Integritätsinformation erstellen und sie an korrekte Berichterstellungsmethoden für `Partition` oder `CodePackageActivationContext` übergeben, um Berichte über aktuelle Entitäten zu erstellen.

Das folgende Beispiel zeigt die regelmäßige Berichterstellung durch einen Watchdog innerhalb des Clusters. Der Watchdog überprüft, ob über einen Knoten auf eine externe Ressource zugegriffen werden kann. Die Ressource wird von einem Dienstmanifest innerhalb der Anwendung benötigt. Wenn die Ressource nicht verfügbar ist, können die anderen Dienste innerhalb der Anwendung trotzdem ordnungsgemäß funktionieren. Aus diesem Grund wird der Bericht über die bereitgestellte Dienstpaketentität alle 30 Sekunden gesendet.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Senden Sie Integritätsberichte mit **Send-ServiceFabric *EntityType* HealthReport**.

Das folgende Beispiel zeigt die regelmäßige Berichterstellung für CPU-Werte auf einem Knoten. Die Berichte sollten alle 30 Sekunden gesendet werden, und sie haben eine Gültigkeitsdauer von 2 Minuten. Wenn sie ablaufen, weist der Reporter Probleme auf. Daher wird der Knoten als fehlerhaft bewertet. Wenn der CPU-Wert über einem Schwellenwert liegt, enthält der Bericht als Integritätszustand eine Warnung. Wenn der CPU-Wert länger als die konfigurierte Zeit über dem Schwellenwert bleibt, wird dies als Fehler gemeldet. Andernfalls sendet der Reporter als Integritätszustand „OK“.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Im folgenden Beispiel wird eine vorübergehende Warnung für ein Replikat gemeldet. Dabei werden zunächst die Partitions-ID und die Replikat-ID für den gewünschten Dienst abgerufen. Anschließend wird ein Bericht von **PowershellWatcher** über die Eigenschaft **ResourceDependency** gesendet. Der Bericht ist nur zwei Minuten lang von Bedeutung und wird automatisch aus dem Speicher entfernt.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Senden Sie Integritätsberichte über REST mithilfe von POST-Anforderungen, die an die gewünschte Entität gerichtet sind und die Beschreibung des Integritätsberichts enthalten. Informieren Sie sich über das Senden von REST-basierten [Clusterintegritätsberichten](/rest/api/servicefabric/report-the-health-of-a-cluster) oder [Dienstintegritätsberichten](/rest/api/servicefabric/report-the-health-of-a-service). Alle Entitäten werden unterstützt.

## <a name="next-steps"></a>Nächste Schritte
Basierend auf den Integritätsdaten können Dienstautoren und Cluster- bzw. Anwendungsadministratoren Möglichkeiten für die Nutzung der Informationen erwägen. Sie können beispielsweise Warnungen auf Grundlage des Integritätszustands einrichten, um schwerwiegende Probleme abzufangen, bevor Ausfälle provoziert werden. Administratoren können auch Reparatursysteme einrichten, um Probleme automatisch zu beheben.

[Einführung in die Service Fabric-Integritätsüberwachung](service-fabric-health-introduction.md)

[Anzeigen von Service Fabric-Integritätsberichten](service-fabric-view-entities-aggregated-health.md)

[Melden und Überprüfen der Dienstintegrität](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)
