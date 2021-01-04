---
title: Aktivierungs- und Deaktivierungslebenszyklus des Azure Service Fabric-Hostings
description: In diesem Artikel wird der Lebenszyklus von Anwendungen und „ServicePackage“ auf einem Knoten erläutert.
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: f049b19703d37412d1ee1961aee6cb327efabe7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779599"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric-Hostinglebenszyklus

Dieser Artikel enthält eine Übersicht über die Ereignisse, die in Azure Service Fabric auftreten, wenn eine Anwendung auf einem Knoten aktiviert wird und verschiedene Clusterkonfigurationen zum Steuern des Verhaltens verwendet werden.

Bevor Sie fortfahren, müssen Sie die verschiedenen Konzepte und Beziehungen kennen, die unter [Modellieren von Anwendungen in Service Fabric][a1] erläutert werden. 

> [!NOTE]
> In diesem Artikel werden folgende Begriffe mit folgenden Bedeutungen verwendet, sofern nicht explizit erwähnt wird, dass sie in einer anderen Bedeutung verwendet werden:
>
> - *Replikat* bezieht sich sowohl auf ein Replikat eines zustandsbehafteten Diensts als auch auf eine Instanz eines zustandslosen Diensts.
> - *CodePackage* wird gleichbedeutend mit dem *ServiceHost*-Prozess verwendet, der einen *ServiceType* registriert und Replikate von Diensten dieses *ServiceType* hostet.
>

## <a name="activation-of-applicationservicepackage"></a>Aktivierung der Anwendung bzw. ServicePackage

Die Vorgehensweise für die Aktivierung lautet wie folgt:

1. Laden Sie das Anwendungspaket herunter. Beispiel: „ApplicationManifest.xml“.
2. Richten Sie die Umgebung für die Anwendung ein, zum Beispiel zum Erstellen von Benutzern.
3. Starten Sie die Nachverfolgung der Anwendung, um die Deaktivierung zu ermitteln.
4. Laden Sie ServicePackage herunter. Beispiel: „ServiceManifest.xml“, Code, Konfiguration, Datenpakete usw.
5. Richten Sie die Umgebung für das Dienstpaket ein, richten Sie zum Beispiel die Firewall ein, weisen Sie Ports für Endpunkte zu usw.
6. Starten Sie die Nachverfolgung des Dienstpakets, um die Deaktivierung zu ermitteln.
7. Starten Sie SetupEntryPoint von CodePackages, und warten Sie, bis der Vorgang abgeschlossen ist.
8. Starten Sie MainEntryPoint von CodePackages.

### <a name="servicetype-blocklisting"></a>Aufnehmen von ServiceType in die Blockliste
**ServiceTypeDisableFailureThreshold** bestimmt die Anzahl der Fehler (Aktivierungs-, Download-, CodePackage-Fehler). Anschließend wird die Aufnahme von ServiceType in die Blockliste geplant. Der erste Aktivierungs- oder Downloadfehler bzw. Absturz von CodePackage löst die Planung der Aufnahme von ServiceType in die Sperrliste aus. Die Konfiguration **ServiceTypeDisableGraceInterval** bestimmt das Toleranzintervall, nach dem ServiceType auf dem Knoten in die Sperrliste aufgenommen wird. Während dieser Aktionen werden parallel die Aktivierung, der Download und der Neustart von CodePackage erneut versucht. „Versucht“ bedeutet etwa, dass der erneute Start von CodePackage nach dem Absturz geplant wird oder dass Service Fabric erneut versucht, die Pakete herunterzuladen.

Nach der Aufnahme von ServiceType in die Sperrliste sollte der Integritätsfehler „System.Hosting hat einen Fehler für die ServiceTypeRegistration:ServiceType-Eigenschaft gemeldet. Der ServiceType wurde auf dem Knoten deaktiviert.“ angezeigt werden.

ServiceType wird auf dem Knoten erneut aktiviert, wenn Folgendes geschieht:
- Der Aktivierungsvorgang wird erfolgreich durchgeführt oder erreicht die Anzahl **ActivationMaxFailureCount** von Versuchen nach einem Fehler.
- Der Downloadvorgang wird erfolgreich durchgeführt oder erreicht die Anzahl **DeploymentMaxFailureCount** von Versuchen nach einem Fehler.
- Ein abgestürztes CodePackage wird wieder gestartet und registriert erfolgreich den ServiceType.

**ActivationMaxFailureCount** und **DeploymentMaxFailureCount** geben die maximale Anzahl von Versuchen für das Aktivieren bzw. Herunterladen einer Anwendung durch Service Fabric auf einem Knoten. Danach aktiviert Service Fabric den ServiceType erneut für die Aktivierung. Dadurch erhält der Dienst eine weitere Möglichkeit zur Aktivierung. Wenn diese erfolgreich ist, wird der Fehler damit automatisch behoben. Ein neuer Aktivierungs-/Downloadvorgang, der durch die Platzierung und Aktivierung eines Replikats ausgelöst wird, kann die Aufnahme von ServiceType in die Sperrliste erneut auslösen, oder der Vorgang wird erfolgreich ausgeführt.

> [!NOTE]
> Wenn Ihr CodePackage den Absturz von ServiceType nicht registriert, wirkt sich das nicht auf ServiceType aus. Nur das CodePackage, das ein abstürzendes Replikat hostet, wirkt sich auf den ServiceType aus.
>

### <a name="codepackage-crash"></a>Absturz von CodePackage
Beim Absturz eines CodePackage wartet Service Fabric, bevor es erneut gestartet wird. Dieses als Backoff bezeichnete Warten erfolgt unabhängig davon, ob das Codepaket einen Typ bei der Service Fabric-Runtime registriert hat.

Der Backoffwert lautet „Min(RetryTime, **ActivationMaxRetryInterval**)“. Dieser Wert kann gemäß der Konfigurationseinstellung **ActivationRetryBackoffExponentiationBase** konstant, linear oder exponentiell erhöht werden.

- Konstante: Wenn „**ActivationRetryBackoffExponentiationBase** == 0“ vorliegt, dann gilt „RetryTime = **ActivationRetryBackoffInterval**;“
- Linear:  Wenn „**ActivationRetryBackoffExponentiationBase** == 0“ vorliegt, dann gilt RetryTime = ContinuousFailureCount × **ActivationRetryBackoffInterval**. Hierbei entspricht ContinousFailureCount der Anzahl der Abstürze oder fehlerhaften Aktivierungen von CodePackage.
- Exponentiell: RetryTime = (**ActivationRetryBackoffInterval** in Sekunden) × (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
Sie können das Verhalten steuern, indem Sie die Werte ändern. Wenn Sie z. B. mehrere schnelle Neustartversuche durchführen möchten, können Sie eine lineare Erhöhung verwenden, indem Sie **ActivationRetryBackoffExponentiationBase** auf 0 und **ActivationRetryBackoffInterval** auf 10 festlegen. Wenn bei diesen Einstellungen ein CodePackage abstürzt, beginnt das Intervall nach 10 Sekunden. Wenn das Paket weiterhin abstürzt, ändert sich das Backoff auf 20, 30, 40 Sekunden usw., bis die Aktivierung erfolgreich war oder das Codepaket deaktiviert wurde. 
    
Die maximale Zeit, die Service Fabric nach einem Fehler wartet, ist durch den Wert **ActivationMaxRetryInterval** vorgegeben.
    
Wenn Ihr CodePackage abstürzt und neu gestartet wird, muss es für den von **CodePackageContinuousExitFailureResetInterval** vorgegebenen Zeitraum aktiv bleiben, damit Service Fabric es als fehlerfrei ansieht. Dann wird der frühere Fehler im Integritätsbericht mit „OK“ überschrieben, und ContinousFailureCount wird zurückgesetzt.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage registriert ServiceType nicht
Angenommen ein CodePackage bleibt aktiv und soll einen ServiceType registrieren, aber führt dies nicht durch. In diesem Fall generiert Service Fabric nach **ServiceTypeRegistrationTimeout** eine Warnung (HealthReport), die angibt, dass ServiceType nicht innerhalb des erwarteten Zeitraums konfiguriert wurde.

### <a name="activation-failure"></a>Aktivierungsfehler
Service Fabric verwendet immer ein lineares Backoff (wie bei CodePackage-Abstürzen), wenn während der Aktivierung ein Fehler erkannt wird. Das bedeutet, dass der Aktivierungsvorgang nach 100 Sekunden (0 + 10 + 20 + 30 + 40) aufgegeben wird (der erste Wiederholungsversuch erfolgt sofort). Anschließend werden keine neuen Aktivierungsversuche durchgeführt.
    
Für das maximale Backoff kann **ActivationMaxRetryInterval** und für Wiederholungen kann **ActivationMaxFailureCount** verwendet werden.

### <a name="download-failure"></a>Downloadfehler
Service Fabric verwendet immer ein lineares Backoff, wenn während des Downloads ein Fehler auftritt. Das bedeutet, dass der Aktivierungsvorgang nach 100 Sekunden (0 + 10 + 20 + 30 + 40) aufgegeben wird (der erste Wiederholungsversuch erfolgt sofort). Danach wird der Downloadvorgang nicht erneut versucht. Das lineare Backoff für Downloads entspricht ContinuousFailureCount × *_DeploymentRetryBackoffInterval_*. Das maximale Backoff beträgt **DeploymentMaxRetryInterval**. Wie bei Aktivierungen können Downloadvorgänge so oft wiederholt werden, wie es von **ActivationMaxFailureCount** vorgegeben wird.

> [!NOTE]
> Bevor Sie diese Einstellungen ändern, sollten Sie einige Beispiele berücksichtigen.

* Wenn das CodePackage weiterhin abstürzt und Backoffs verursacht, wird der ServiceType deaktiviert. Wenn die Aktivierungskonfiguration jedoch für schnelle Neustarts eingerichtet ist, kann das CodePackage mehrmals aktiviert werden, bevor der ServiceType in die Sperrliste aufgenommen wird. Beispiel: Angenommen Ihr CodePackage registriert den ServiceType mit Service Fabric und stürzt dann ab. In diesem Fall wird der **ServiceTypeDisableGraceInterval**-Zeitraum abgebrochen, sobald der Host eine Typregistrierung empfängt. Dies kann wiederholt werden, bis das Backoff Ihres CodePackage länger als **ServiceTypeDisableGraceInterval** ist. Anschließend wird der ServiceType auf dem Knoten in die Sperrliste eingetragen. Das Eintragen des ServiceType in die Sperrliste kann länger dauern als erwartet.

* Wenn das Service Fabric-System bei Aktivierungen ein Replikat auf einem Knoten platzieren muss, fordert der ReconfigurationAgent (RA) das Hostingsubsystem auf, die Anwendung zu aktivieren. Die Aktivierungsanforderung wird alle 15 Sekunden (durch die Konfigurationseinstellung **RAPMessageRetryInterval** festgelegt) wiederholt. Damit das Service Fabric-System weiß, dass der ServiceType in die Sperrliste eingetragen wurde, muss der Aktivierungsvorgang des Hosts länger als das Wiederholungsintervall und das **ServiceTypeDisableGraceInterval** sein. Nehmen Sie z. B. an, dass für den Cluster **ActivationMaxFailureCount** auf 5 und **ActivationRetryBackoffInterval** auf 1 Sekunde festgelegt ist. Dies bedeutet, dass der Aktivierungsvorgang nach 0 + 1 + 2 + 3 + 4 = 10 Sekunden (denken Sie daran, dass der erste Wiederholungsversuch sofort erfolgt) aufgegeben wird. Danach gibt der Host weitere Wiederholungsversuche auf. In diesem Fall wird der Aktivierungsvorgang abgeschlossen und nach 15 Sekunden nicht mehr wiederholt. Das liegt daran, dass alle zulässigen Wiederholungsversuche durch Service Fabric innerhalb von 15 Sekunden ausgeführt wurden. Jede Wiederholung von ReconfigurationAgent erstellt einen neuen Aktivierungsvorgang im Hostingsubsystem, und das Muster wird wiederholt. Daher wird der ServiceType auf dem Knoten nie in die Sperrliste eingetragen. Da der ServiceType auf dem Knoten nicht in die Sperrliste gelangt, wird das Replikat nicht verschoben und auf einem anderen Knoten versucht.
> 

## <a name="deactivation"></a>Deaktivierung

Wenn ein ServicePackage auf einem Knoten aktiviert ist, wird er für die Deaktivierung überwacht. 

Die Deaktivierung funktioniert auf zwei Arten:

- Regelmäßig:  Nach jedem **DeactivationScanInterval** erfolgt eine Prüfung auf ServicePackages, die NIE ein Replikat gehostet haben. Diese werden dann als Kandidaten für die Deaktivierung markiert.
- ReplicaClose: Wenn ein Replikat geschlossen wird, ruft die Deactivator-Entität einen DecrementUsageCount-Wert ab. Wenn der Wert auf 0 (null) fällt, hostet das ServicePackage kein Replikat und ist daher ein Kandidat für die Deaktivierung.

 Die Kandidaten für die Deaktivierung werden basierend auf dem Aktivierungsmodus [Exclusive/Shared][a2] geplant: nach **DeactivationGraceInterval** für SharedMode und nach **ExclusiveModeDeactivationGraceInterval** für ExclusiveMode. Wenn in diesem Zeitraum ein neues Replikat platziert wird, erfolgt ein Abbruch der Deaktivierung.

### <a name="periodically"></a>Regelmäßig:
Im Folgenden werden einige Beispiele für regelmäßige Deaktivierungen erläutert:

Beispiel 1: Angenommen die Deactivator-Entität führt zum Zeitpunkt von „Time T(**DeactivationScanInterval**)“ eine Überprüfung durch. Die nächste Überprüfung erfolgt um „2T“. Angenommen, eine ServicePackage-Aktivierung trat um „T+1“ auf. Dieses ServicePackage hostet kein Replikat und muss daher deaktiviert werden. Damit das ServicePackage als Kandidat für die Deaktivierung sein kann, muss es über den Zeitraum von „T“ kein Replikat aufweisen. Das bedeutet, es kann um „2T+1“ deaktiviert werden. Daher wird die Überprüfung um „2T“ das ServicePackage nicht als Kandidat für die Deaktivierung einstufen. Der nächste Deaktivierungszyklus um „3T“ plant dieses ServicePackage für die Deaktivierung ein, weil es dann über den Zeitraum von „T“ kein Replikat aufgewiesen hat.  

Beispiel 2: Angenommen ein ServicePackage wird zum Zeitpunkt „T–1“ aktiviert und die Deactivator-Entität führt um „T“ eine Überprüfung durch. Das ServicePackage hostet kein Replikat. Dann wird dieses ServicePackage bei der nächsten Überprüfung um „2T“ als Kandidat für die Deaktivierung eingestuft.  

Beispiel 3: Angenommen ein ServicePackage wird zum Zeitpunkt „T–1“ aktiviert und die Deactivator-Entität führt um „T“ eine Überprüfung durch. Das ServicePackage hostet noch kein Replikat. Jedoch wird um „T+1“ ein Replikat platziert. Der Host erhält einen IncrementUsageCount-Wert. d. h., ein Replikat wird erstellt. Dieses ServicePackage wird also um „2T“ nicht für die Deaktivierung eingeplant. Da es ein Replikat enthält, fährt die Deaktivierung mit der ReplicaClose-Logik fort, die weiter unten erläutert wird.

Beispiel 4: Angenommen, Ihr ServicePackage ist groß (z. B. 10 GB), dann kann der Download auf den Knoten einige Zeit dauern. Sobald eine Anwendung aktiviert wird, wird ihr Lebenszyklus von der Deactivator-Entität überwacht. Wenn **DeactivationScanInterval** auf einen kleinen Wert festgelegt wurde, könnten Probleme auftreten, bei denen Ihr ServicePackage nicht über ausreichend Zeit für die Aktivierung auf dem Knoten verfügt, da der Download zu viel Zeit erfordert. Dieses Problem können Sie umgehen, indem Sie [das ServicePackage vorab auf den Knoten herunterladen][p1] oder das **DeactivationScanInterval** erhöhen. 

> [!NOTE]
> Ein ServicePackage kann zu einem beliebigen Zeitpunkt zwischen (**DeactivationScanInterval** bis 2 × **_DeactivationScanInterval_*) + **DeactivationGraceInterval** /** ExclusiveModeDeactivationGraceInterval** deaktiviert werden. 
>

### <a name="replica-close"></a>Schließen von Replikaten
Bei der Deaktivierung werden die Replikate gezählt, die ein ServicePackage enthält. Wenn ein ServicePackage ein Replikat enthält, das geschlossen wird oder ausfällt, erhält der Host einen DecrementUsageCount-Wert. Wenn ein Replikat geöffnet wird, erhält der Host einen IncrementUsageCount-Wert. Der DecrementUsageCount-Wert bedeutet, dass das ServicePackage nun ein Replikat weniger enthält. Wenn die Anzahl auf 0 (null) fällt, wird das ServicePackage für die Deaktivierung eingeplant. Der Zeitpunkt für die Deaktivierung ergibt sich aus **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval**. 

Beispiel: Angenommen, diese Verringerung erfolgt zum Zeitpunkt „T“, und das ServicePackage soll zum Zeitpunkt 2T+X(**DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval**) deaktiviert werden. Wenn der Host in diesem Zeitraum einen IncrementUsage-Wert erhält, was bedeutet, dass ein Replikat erstellt wurde, wird die Deaktivierung abgebrochen.

> [!NOTE]
> Was bedeuten diese Konfigurationseinstellungen?
**DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval:** Der Zeitraum für ein ServicePackage, ein weiteres Replikat zu hosten, nachdem es bereits ein Replikat gehostet hat. 
**DeactivationScanInterval:** Der Mindestzeitraum für ein ServicePackage, ein Replikat zu hosten, wenn es noch nie ein Replikat gehostet hat, z. B., wenn es nicht verwendet wurde.
>

### <a name="ctrlc"></a>STRG+C
Sobald ein ServicePackage **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** übergibt und noch kein Replikat hostet, kann die Deaktivierung nicht mehr abgebrochen werden. Das CodePackage empfängt einen STRG+C-Handler, d. h., das die Deaktivierungspipeline muss durchlaufen werden, um den Prozess zu beenden. Wenn in diesem Zeitraum ein neues Replikat für dasselbe ServicePackage platziert wird, wird diese Platzierung nicht erfolgreich ausgeführt, da ein Übergang von Deaktivierung zur Aktivierung nicht möglich ist.

## <a name="cluster-configs"></a>Clusterkonfigurationen

Dabei handelt es sich um Konfigurationen mit Standardwerten, die sich auf die Aktivierung/Deaktivierung auswirken.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold:** Der Standardwert lautet „1“. Dies ist der Schwellenwert für die Anzahl der Fehler. Bei Überschreitung wird FailoverManager (FM) benachrichtigt. FM deaktiviert den Diensttyp auf diesem Knoten und versucht, die Workload auf einem anderen Knoten zu platzieren.
**ServiceTypeDisableGraceInterval:** Der Standardwert beträgt „30 Sekunden“ und beschreibt das Zeitintervall, nach dem der Diensttyp deaktiviert werden kann.
**ServiceTypeRegistrationTimeout:** Der Standardwert beträgt „300 Sekunden“ und beschreibt das Zeitlimit, in dem der ServiceType bei Service Fabric registriert werden muss.

### <a name="activation"></a>Aktivierung
**ActivationRetryBackoffInterval:** Der Standardwert beträgt „10 Sekunden“ und beschreibt das Backoffintervall für jeden Aktivierungsfehler.
**ActivationMaxFailureCount:** Der Standardwert lautet „20“. Dies ist die maximale Anzahl von Versuchen, die Aktivierung nach einem Fehler zu wiederholen, bevor aufgegeben wird. 
**ActivationRetryBackoffExponentiationBase:** Der Standardwert lautet „1,5“.
**ActivationMaxRetryInterval:** Der Standardwert beträgt „3600 Sekunden“ und beschreibt den maximalen Backoffzeitraum für die Aktivierung bei Fehlern.
**CodePackageContinuousExitFailureResetInterval:** Der Standardwert beträgt „300 Sekunden“ und beschreibt das Zeitlimit zum Zurücksetzen der Anzahl der kontinuierlichen Beendigungsfehler für CodePackage.

### <a name="download"></a>Download
**DeploymentRetryBackoffInterval:** Standardwert: 10 Backoffintervall für den Fehler bei der Bereitstellung.
**DeploymentMaxRetryInterval:** Der Standardwert beträgt „3600 Sekunden“ und beschreibt den maximalen Backoffzeitraum für die Bereitstellung bei Fehlern.
**DeploymentMaxFailureCount:** Der Standardwert lautet „20“. Die Anwendungsbereitstellung wird DeploymentMaxFailureCount Mal wiederholt, bevor ein Fehler für die Bereitstellung der betreffenden Anwendung auf dem Knoten auftritt.

### <a name="deactivation"></a>Deaktivierung
**DeactivationScanInterval:** Der Standardwert beträgt „600 Sekunden“ und beschreibt den Mindestzeitraum für ein ServicePackage, ein Replikat zu hosten, wenn es noch nie ein Replikat gehostet hat, z. B., wenn es nicht verwendet wurde.
**DeactivationGraceInterval:** Der Standardwert beträgt „60 Sekunden“ und beschreibt den Zeitraum für ein ServicePackage, ein weiteres Replikat zu hosten, nachdem es bereits ein Replikat gehostet hat, wenn das Prozessmodell **Shared** vorliegt.
**ExclusiveModeDeactivationGraceInterval:** Der Standardwert beträgt „1 Sekunden“ und beschreibt den Zeitraum für ein ServicePackage, ein weiteres Replikat zu hosten, nachdem es bereits ein Replikat gehostet hat, wenn das Prozessmodell **Exclusive** vorliegt.

## <a name="next-steps"></a>Nächste Schritte
[Packen][a3] und Vorbereiten einer Anwendung für die Bereitstellung

[Bereitstellen und Entfernen von Anwendungen][a4] Dieser Artikel beschreibt, wie Sie Anwendungsinstanzen mithilfe von PowerShell verwalten.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
