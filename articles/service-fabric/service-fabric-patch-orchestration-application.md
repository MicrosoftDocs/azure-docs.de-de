---
title: Azure Service Fabric-Anwendung für die Patchorchestrierung | Microsoft-Dokumentation
description: Anwendung zum Automatisieren von Betriebssystempatches in einem Service Fabric-Cluster.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: ccc0399b6ac886ec8d9ef7d207c3539f1d078070
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951918"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Patchen des Windows-Betriebssystem in Ihrem Service Fabric-Cluster

> 
> [!IMPORTANT]
> Die Anwendungsversion 1.2.* wird ab dem 30. April 2019 nicht mehr unterstützt. Führen Sie ein Upgrade auf die aktuelle Version aus.


[Automatische Betriebssystemimageupgrades mit Azure-VM-Skalierungsgruppen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) ist die bewährte Methode, um Ihre Betriebssysteme in Azure gepatcht zu halten. Die Patch Orchestration Application (POA) ist ein Wrapper für den RepairManager-Systemdienst von Service Fabric, der Konfigurationen basierend auf Patchplänen für Betriebssysteme für nicht in Azure gehostete Cluster ermöglicht. POA ist für nicht in Azure gehostete Cluster nicht erforderlich, aber die Planung von Patchinstallationen durch Upgradedomänen ist erforderlich, um die Service Fabric-Clusterhosts ohne Ausfallzeiten zu patchen.

POA ist eine Azure Service Fabric-Anwendung, mit der das Patchen von Betriebssystemen in einem Service Fabric-Cluster ohne Ausfallzeiten automatisiert werden kann.

Die App für die Patchorchestrierung umfasst die folgenden Features:

- **Automatische Installation von Betriebssystemupdates**. Betriebssystemupdates werden automatisch heruntergeladen und installiert. Clusterknoten werden bei Bedarf ohne Ausfall des Clusters neu gestartet.

- **Clusterfähiges Patchen und Integration von Integrität**. Beim Anwenden von Updates überwacht die App für die Patchorchestrierung die Integrität der Clusterknoten. Knoten im Cluster werden einzeln oder in Upgradedomänen aktualisiert. Wenn die Integrität des Clusters aufgrund des Patchingprozesses abnimmt, wird das Patchen angehalten, um eine Verschärfung des Problems zu verhindern.

## <a name="internal-details-of-the-app"></a>Interne Details der App

Die App für die Patchorchestrierung besteht aus den folgenden Teilkomponenten:

- **Koordinatordienst:** Dieser zustandsbehaftete Dienst ist für Folgendes zuständig:
    - Koordinieren der Windows Update-Aufträge im gesamten Cluster.
    - Speichern der Ergebnisse der abgeschlossenen Windows Update-Vorgänge.
- **Knoten-Agent-Dienst:** Dieser zustandslose Dienst wird auf allen Service Fabric-Clusterknoten ausgeführt. Der Dienst ist für Folgendes zuständig:
    - Bootstrapping des Knoten-Agent-NT-Diensts.
    - Überwachen des Knoten-Agent-NT-Diensts.
- **Knoten-Agent-NT-Dienst:** Dieser Windows NT-Dienst wird mit erhöhten Berechtigungen (SYSTEM) ausgeführt. Im Gegensatz dazu werden der Knoten-Agent-Dienst und der Koordinatordienst mit niedrigeren Berechtigungen ausgeführt (NETZWERKDIENST). Der Dienst ist für die Ausführung der folgenden Windows Update-Aufträge auf allen Clusterknoten zuständig:
    - Deaktivieren der automatischen Windows Update-Funktion auf dem Knoten.
    - Herunterladen und Installieren von Windows Update entsprechend der vom Benutzer bereitgestellten Richtlinie.
    - Neustarten des Computers nach der Windows Update-Installation.
    - Hochladen der Ergebnisse von Windows-Updates in den Koordinatordienst.
    - Senden von Integritätsberichten, falls der Vorgang nach allen Wiederholungsversuchen fehlschlägt.

> [!NOTE]
> Die App für die Patchorchestrierung verwendet den Reparatur-Manager-Systemdienst in Service Fabric, um die Knoten zu aktivieren und zu deaktivieren und um Integritätsprüfungen durchzuführen. Der von der App für die Patchorchestrierung erstellte Reparaturtask verfolgt den Windows Update-Fortschritt für jeden Knoten nach.

## <a name="prerequisites"></a>Voraussetzungen

> [!NOTE]
> Als Mindestversion für .NET Framework ist 4.6 erforderlich.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Aktivieren des Reparatur-Manager-Diensts (falls dieser nicht bereits ausgeführt wird)

Für die App für die Patchorchestrierung muss der Reparatur-Manager-Systemdienst im Cluster aktiviert sein.

#### <a name="azure-clusters"></a>Azure-Cluster

In Azure-Clustern auf Dauerhaftigkeitsstufe „Silver“ ist der Reparatur-Manager-Dienst standardmäßig aktiviert. Bei Azure-Clustern mit der Dauerhaftigkeitsstufe „Gold“ ist der Reparatur-Manager-Dienst möglicherweise aktiviert, abhängig davon, wann diese Cluster erstellt wurden. In Azure-Clustern auf Dauerhaftigkeitsstufe „Bronze“ ist der Reparatur-Manager-Dienst standardmäßig nicht aktiviert. Wenn der Dienst bereits aktiviert ist, wird er im Service Fabric Explorer im Abschnitt mit den Systemdiensten aufgeführt.

##### <a name="azure-portal"></a>Azure-Portal
Sie können den Reparatur-Manager beim Einrichten des Clusters über das Azure-Portal aktivieren. Wählen Sie bei der Clusterkonfiguration unter **Add-On-Features** die Option **Reparatur-Manager einschließen** aus.
![Abbildung zur Aktivierung des Reparatur-Managers über das Azure-Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager-Bereitstellungsmodell
Alternativ können Sie den Reparatur-Manager-Dienst für neue und vorhandene Service Fabric-Cluster über das [Azure Resource Manager-Bereitstellungsmodell](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) aktivieren. Rufen Sie die Vorlage für den Cluster ab, den Sie bereitstellen möchten. Sie können entweder die Beispielvorlagen verwenden oder eine benutzerdefinierte Vorlage für das Azure Resource Manager-Bereitstellungsmodell erstellen. 

So aktivieren Sie den Reparatur-Manager-Dienst mithilfe einer [Vorlage für das Azure Resource Manager-Bereitstellungsmodell](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)

1. Überprüfen Sie zunächst, ob `apiversion` für die Ressource `Microsoft.ServiceFabric/clusters` auf `2017-07-01-preview` festgelegt ist. Wenn eine andere Einstellung vorliegt, müssen Sie `apiVersion` auf den Wert `2017-07-01-preview` oder höher aktualisieren:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Aktivieren Sie dann den Reparatur-Manager-Dienst, indem Sie folgenden `addonFeatures`-Abschnitt nach dem Abschnitt `fabricSettings` hinzufügen:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Nachdem Sie die Clustervorlage mit diesen Änderungen aktualisiert haben, wenden Sie die Änderungen an, und warten Sie, bis das Upgrade abgeschlossen wurde. Sie sehen nun, dass der Reparatur-Manager-Systemdienst im Cluster ausgeführt wird. Der Name im Abschnitt für Systemdienste im Service Fabric Explorer lautet`fabric:/System/RepairManagerService`. 

### <a name="standalone-on-premises-clusters"></a>Eigenständige lokale Cluster

Sie können über die [Konfigurationseinstellungen für eigenständige Windows-Cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) den Reparatur-Manager-Dienst auf neuen und vorhandenen Service Fabric-Clustern aktivieren.

So aktivieren Sie den Reparatur-Manager-Dienst

1. Überprüfen Sie zunächst, dass `apiversion` unter [General cluster configurations](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) (Allgemeine Clusterkonfigurationen) auf `04-2017` oder höher festgelegt ist:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Aktivieren Sie dann den Reparatur-Manager-Dienst, indem Sie folgenden `addonFeatures`-Abschnitt nach dem Abschnitt `fabricSettings` hinzufügen, wie unten gezeigt:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Aktualisieren Sie das Clustermanifest mit diesen Änderungen, und verwenden Sie dabei das aktualisierte Clustermanifest zum [Erstellen eines neuen Clusters](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) oder zum [Aktualisieren der Clusterkonfiguration](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Sobald der Cluster mit dem aktualisierten Clustermanifest ausgeführt wird, wird der in Ihrem Cluster ausgeführte Reparatur-Manager-Systemdienst mit dem Namen `fabric:/System/RepairManagerService` im Service Fabric Explorer im Abschnitt „Systemdienste“ angezeigt.

### <a name="configure-windows-updates-for-all-nodes"></a>Konfigurieren von Windows-Updates für alle Knoten

Automatische Windows-Updates können zu einer Verringerung der Verfügbarkeit führen, da mehrere Clusterknoten gleichzeitig neu gestartet werden können. Die App für die Patchorchestrierung versucht standardmäßig, die automatische Windows Update-Funktion auf jedem Clusterknoten zu deaktivieren. Wenn die Einstellungen jedoch von einem Administrator bzw. durch eine Gruppenrichtlinie verwaltet werden, empfiehlt es sich, die Windows Update-Richtlinie explizit auf „Bei Download benachrichtigen“ festzulegen.

## <a name="download-the-app-package"></a>Herunterladen des App-Pakets

Um das Anwendungspaket herunterzuladen, rufen Sie die GitHub-[Releaseseite](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) der Patch Orchestration Application auf.

## <a name="configure-the-app"></a>Konfigurieren der App

Das Verhalten der App für die Patchorchestrierung kann Ihren Anforderungen entsprechend konfiguriert werden. Überschreiben Sie die Standardwerte, indem Sie während der Erstellung bzw. Aktualisierung einer Anwendung den Anwendungsparameter übergeben. Anwendungsparameter können durch Angeben von `ApplicationParameter` in den Cmdlets `Start-ServiceFabricApplicationUpgrade` oder `New-ServiceFabricApplication` festgelegt werden.

|**Parameter**        |**Typ**                          | **Details**|
|:-|-|-|
|MaxResultsToCache    |Long                              | Maximale Anzahl von Windows Update-Ergebnissen, die zwischengespeichert werden sollen. <br>Der Standardwert ist 3000, wobei Folgendes angenommen wird: <br> – Es sind 20 Knoten vorhanden. <br> – Jeden Monat können fünf Updates auf einem Knoten erfolgen. <br> – Pro Vorgang können zehn Ergebnisse vorliegen. <br> – Es sollen die Ergebnisse für die letzten drei Monaten gespeichert werden. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy gibt die Richtlinie an, die vom Koordinatordienst zum Installieren von Windows-Updates auf den Service Fabric-Clusterknoten verwendet werden soll.<br>                         Zulässige Werte sind: <br>                                                           <b>NodeWise</b>. Windows Update wird immer nur auf jeweils einem Knoten installiert. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update wird immer nur in jeweils einer Upgradedomäne installiert. (Höchstens alle Knoten in einer Upgradedomäne können Windows Update verwenden.)<br> Entscheiden Sie mithilfe des Abschnitts [Häufig gestellte Fragen](#frequently-asked-questions), welche Richtlinie für Ihren Cluster am besten geeignet ist.
|LogsDiskQuotaInMB   |Long  <br> (Standard: 1024)               |Maximale Größe der Protokolle für die App für die Patchorchestrierung in MB, die lokal auf jedem Knoten beibehalten werden können.
| WUQuery               | Zeichenfolge<br>(Standard: „IsInstalled=0“)                | Abfrage zum Abrufen von Windows-Updates. Weitere Informationen finden Sie unter [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
| InstallWindowsOSOnlyUpdates | Boolean <br> (Standard: false)                 | Verwenden Sie dieses Flag, um zu steuern, welche Updates heruntergeladen und installiert werden sollen. Folgende Werte sind zulässig. <br>TRUE: installiert nur Updates des Windows-Betriebssystems.<br>FALSE: installiert alle verfügbaren Updates auf dem Computer.          |
| WUOperationTimeOutInMinutes | Int <br>(Standard: 90)                   | Gibt den Timeoutwert für jeden Windows Update-Vorgang an (Suchen/Herunterladen/Installieren). Wenn der Vorgang nicht innerhalb des angegebenen Timeoutzeitraums abgeschlossen ist, wird er abgebrochen.       |
| WURescheduleCount     | Int <br> (Standard: 5)                  | Gibt an, wie oft der Dienst das Windows Update maximal erneut plant, falls bei dem Vorgang wiederholt ein Fehler auftritt.          |
| WURescheduleTimeInMinutes | Int <br>(Standard: 30) | Das Intervall, nach dem der Dienst das Windows-Update erneut plant, falls der Fehler weiterhin besteht. |
| WUFrequency           | Durch Trennzeichen getrennte Zeichenfolge (Standard: „Wöchentlich, Mittwoch, 7:00:00“)     | Die Häufigkeit, mit der Windows-Updates installiert werden sollen. Folgende Formate und Werte sind möglich: <br>– Monatlich, TT, HH:MM:SS, z.B. Monatlich, 5,12:22:32.<br>Zulässige Werte für das Feld „TT“ (Tag) sind Zahlen im Bereich 1–28 und „last“. <br> – Wöchentlich, TAG, HH:MM:SS, z.B. Wöchentlich, Dienstag, 12:22:32  <br> – Täglich, HH:MM:SS, z.B. Täglich, 12:22:32.  <br> – Keine: Gibt an, dass keine Windows-Updates durchgeführt werden sollen.  <br><br> Die Uhrzeiten sind in UTC angegeben.|
| AcceptWindowsUpdateEula | Boolean <br>(Standardwert: true) | Wenn Sie dieses Flag festlegen, akzeptiert die Anwendung den Endbenutzer-Lizenzvertrag für Windows Update für den Besitzer des Computers.              |

> [!TIP]
> Wenn Windows Update sofort ausgeführt werden soll, legen Sie `WUFrequency` relativ zum Zeitpunkt der Anwendungsbereitstellung fest. Angenommen, Sie haben einen Testcluster mit fünf Knoten und möchten die App ca. um 17:00 Uhr (UTC) bereitstellen. Wenn Sie davon ausgehen, dass das Upgrade oder die Bereitstellung der Anwendung maximal 30 Minuten dauert, legen Sie WUFrequency auf „Täglich, 17:30:00“ fest.

## <a name="deploy-the-app"></a>Bereitstellen der App

1. Führen Sie alle erforderlichen Schritte zur Vorbereitung des Clusters aus.
2. Stellen Sie die App für die Patchorchestrierung wie jede andere Service Fabric-App bereit. Sie können die App mit PowerShell bereitstellen. Führen Sie die Schritte unter [Bereitstellen und Entfernen von Anwendungen mit PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) aus.
3. Übergeben Sie zum Konfigurieren der Anwendung zum Zeitpunkt der Bereitstellung `ApplicationParameter` an das `New-ServiceFabricApplication`-Cmdlet. Zur Vereinfachung wurde das Skript „Deploy.ps1“ zusammen mit der Anwendung bereitgestellt. So verwenden Sie das Skript

    - Stellen Sie mit `Connect-ServiceFabricCluster` eine Verbindung mit einem Service Fabric-Cluster her.
    - Führen Sie das PowerShell-Skript „Deploy.ps1“ mit einem geeigneten `ApplicationParameter`-Wert aus.

> [!NOTE]
> Speichern Sie das Skript und den Anwendungsordner „PatchOrchestrationApplication“ im gleichen Verzeichnis.

## <a name="upgrade-the-app"></a>Aktualisieren der App

Um eine vorhandene App für die Patchorchestrierung mithilfe von PowerShell zu aktualisieren, führen Sie die Schritte unter [Service Fabric-Anwendungsupgrade mithilfe von PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) aus.

## <a name="remove-the-app"></a>Entfernen der App

Führen Sie die Schritte unter [Bereitstellen und Entfernen von Anwendungen mit PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) aus, um die Anwendung zu entfernen.

Zur Vereinfachung wurde das Skript „Undeploy.ps1“ zusammen mit der Anwendung bereitgestellt. So verwenden Sie das Skript

  - Stellen Sie mit ```Connect-ServiceFabricCluster``` eine Verbindung mit einem Service Fabric-Cluster her.

  - Führen Sie das PowerShell-Skript „Undeploy.ps1“ aus.

> [!NOTE]
> Speichern Sie das Skript und den Anwendungsordner „PatchOrchestrationApplication“ im gleichen Verzeichnis.

## <a name="view-the-windows-update-results"></a>Anzeigen der Ergebnisse von Windows Update

Die App für die Patchorchestrierung macht eine REST-API verfügbar, um dem Benutzer die Verlaufsergebnisse anzuzeigen. Beispiel für die JSON-Ausgabe für das Ergebnis:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

Felder des JSON werden unten beschrieben.

Feld | Werte | Details
-- | -- | --
OperationResult | 0: Erfolgreich<br> 1: Erfolgreich mit Fehlern<br> 2: Fehlgeschlagen<br> 3 : Abgebrochen<br> 4: Abgebrochen mit Timeout | Gibt das Ergebnis des Gesamtvorgangs an (schließt normalerweise die Installation eines oder mehrerer Updates ein).
ResultCode | Das Gleiche wie bei OperationResult | Diese Feld gibt das Ergebnis eines Installationsvorgangs für ein einzelnes Update an.
OperationType | 1: Installation<br> 0: Suchen und Herunterladen| „Installation“ ist der einzige Wert für OperationType, der standardmäßig in den Ergebnissen angezeigt wird.
WindowsUpdateQuery | Der Standardwert ist „IsInstalled=0“. |Windows Update-Abfrage, die für die Suche nach Updates verwendet wurde. Weitere Informationen finden Sie unter [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | TRUE: Neustart war erforderlich<br> FALSE: Neustart war nicht erforderlich | Gibt an, ob ein Neustart erforderlich war, um die Installation des Updates abzuschließen.
OperationStartTime | Datetime | Gibt den Zeitpunkt an, zu dem der Vorgang (Herunterladen/Installation) gestartet wurde.
OperationTime | Datetime | Gibt den Zeitpunkt an, zu dem der Vorgang (Herunterladen/Installation) abgeschlossen wurde.
HRESULT | 0 – erfolgreich<br> Sonstiges – Fehler| Gibt den Grund des Fehlers beim Windows Update mit UpdateID „7392acaf-6a85-427c-8a8d-058c25beb0d6“ an.

Wenn noch keine Aktualisierung geplant ist, ist die JSON-Ausgabe leer.

Melden Sie sich beim Cluster an, um Windows Update-Ergebnisse abzufragen. Ermitteln Sie dann die Replikatadresse des primären Knotens für den Koordinatordienst, und öffnen Sie diese URL im Browser: http://&lt;REPLIKAT-IP&gt;:&lt;Anwendungsport&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

Der REST-Endpunkt für den Koordinatordienst verfügt über einen dynamischen Port. Die genaue URL finden Sie im Service Fabric Explorer. Die Ergebnisse sind beispielsweise unter `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults` verfügbar.

![Abbildung des REST-Endpunkts](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Wenn der Reverseproxy auf dem Cluster aktiviert ist, können Benutzer auch von außerhalb des Clusters auf die URL zugreifen.
Aufzurufender Endpunkt: http://&lt;SERVER-URL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Führen Sie zum Aktivieren des Reverseproxys auf dem Cluster die Schritte unter [Reverseproxy in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) aus. 

> 
> [!WARNING]
> Sobald der Reverseproxy konfiguriert wurde, können alle Microservices im Cluster, die einen HTTP-Endpunkt verfügbar machen, außerhalb des Clusters aufgerufen werden.

## <a name="diagnosticshealth-events"></a>Diagnose/Integritätsereignisse

Im folgenden Abschnitt wird erläutert, wie das Debuggen / die Diagnose von Problemen mit Patchupdates über die Patch Orchestration Application in Service Fabric-Clustern durchgeführt wird.

> [!NOTE]
> Sie sollten Version v1.4.0 der POA installieren, um viele der unten aufgerufenen Verbesserungen bei der Selbstdiagnose nutzen zu können.

Der NodeAgentNTService erstellt [Reparaturtasks](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) zum Installieren von Updates auf den Knoten. Jeder Task wird dann vom Koordinatordienst anhand der Richtlinie zur Taskgenehmigung vorbereitet. Die vorbereiteten Tasks werden schließlich vom Repair Manager genehmigt, der keinen Task genehmigt, wenn der Cluster sich in einem fehlerhaften Zustand befindet. Sie erfahren nun Schritt für Schritt, wie Updates auf einem Knoten durchgeführt werden.

1. Der auf jedem Knoten ausgeführte NodeAgentNTService sucht zum geplanten Zeitpunkt nach verfügbaren Windows Updates. Wenn Updates verfügbar sind, werden sie auf den Knoten heruntergeladen.
2. Sobald die Updates heruntergeladen sind, erstellt NodeAgentNTService einen entsprechenden Reparaturtask für den Knoten mit dem Namen „POS___<eindeutige ID>“. Sie können diese Reparaturtasks mithilfe des Cmdlets [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) oder in SFX im Detailbereich des Knotens anzeigen. Nach Erstellen des Reparaturtasks erfolgt ein schneller Wechsel zum [Claimed-Zustand](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).
3. Der Koordinatordienst sucht in regelmäßigen Abständen nach Reparaturtasks im Claimed-Zustand und aktualisiert sie auf Grundlage der TaskApprovalPolicy zum Preparing-Zustand. Wenn die TaskApprovalPolicy als NodeWise konfiguriert ist, wird ein Reparaturtask, der einem Knoten entspricht, nur dann vorbereitet, wenn sich derzeit kein anderer Reparaturtask im Zustand Preparing/Approved/Executing/Restoring befindet. Ebenso ist dann, wenn die TaskApprovalPolicy als UpgradeWise konfiguriert ist, jederzeit sichergestellt, dass Tasks im oben genannten Zustand nur für Knoten vorhanden sind, die zu derselben Upgradedomäne gehören. Sobald ein Reparaturtask in den Preparing-Zustand gesetzt wird, ist der entsprechende Service Fabric-Knoten mit der Absicht „Neustart“ [deaktiviert](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps).

   POA (v1.4.0 und höher) sendet Ereignisse mit der Eigenschaft „ClusterPatchingStatus“ an den Koordinatordienst, um die Knoten anzuzeigen, die gepatcht werden. Das untere Bild zeigt, dass Updates auf „_poanode_0“ installiert werden:

    [![Abbildung des Clusterpatchzustands](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. Sobald der Knoten deaktiviert ist, wird der Reparaturtask in den Executing-Zustand gesetzt. Beachten Sie: Sobald ein Reparaturtask im Preparing-Zustand stecken bleibt, weil ein Knoten im Deaktivierungszustand stecken bleibt, kann dies zum Blockieren neuer Reparaturtasks führen und so das Patchen des Clusters anhalten.
5. Sobald ein Reparaturtask sich im Executing-Zustand befindet, beginnt die Patchinstallation auf diesem Knoten. Sobald der Patch installiert ist, kann der Knoten je nach Patch neu gestartet werden oder auch nicht. Posten Sie, dass der Reparaturtask in den Restoring-Zustand gesetzt wird, sodass der Knoten wieder aktiviert und dann als abgeschlossen gekennzeichnet wird.

   In v1.4.0 und höheren Versionen der Anwendung können Sie den Zustand des Updates den Integritätsereignissen im NodeAgentService unter der Eigenschaft „WUOperationStatus-[Knotenname]“ entnehmen. Die hervorgehobenen Abschnitte in den folgenden Abbildungen zeigen den Zustand des Windows Updates für Knoten „poanode_0“ und „poanode_2“:

   [![Abbildung des Zustands des Windows Update-Vorgangs](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Abbildung des Zustands des Windows Update-Vorgangs](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Sie können die Details auch mithilfe von PowerShell ermitteln, indem Sie eine Verbindung mit dem Cluster herstellen und den Zustand des Reparaturtasks mit [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) abrufen. Im unten stehenden Beispiel befindet sich der Task „POS__poanode_2_125f2969-933 c-4774-85d1-ebdf85e79f15“ im DownloadComplete-Zustand. Dies bedeutet, dass Updates auf den Knoten „poanode_2“ heruntergeladen wurden und versucht wird, sie zu installieren, sobald der Task in den Executing-Zustand gesetzt ist.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Um noch mehr herauszufinden, melden Sie sich bei den spezifischen virtuellen Computern an, und entnehmen Sie den Windows-Ereignisprotokollen weitere Informationen zu dem Problem. Der oben genannte Reparaturtask kann nur diese Executor-Unterzustände haben:

      ExecutorSubState | Detail
    -- | -- 
      None=1 |  Bedeutet, dass kein laufender Vorgang auf dem Knoten vorlag. Mögliche Statusübergänge.
      DownloadCompleted=2 | Downloadvorgang wurde erfolgreich, mit Teilfehler oder Fehler abgeschlossen.
      InstallationApproved=3 | Downloadvorgang wurde früher abgeschlossen und Repair Manager hat die Installation genehmigt.
      InstallationInProgress=4 | Entspricht dem Zustand der Ausführung des Reparaturtasks.
      InstallationCompleted=5 | Installation wurde erfolgreich, mit Teilerfolg oder Fehler abgeschlossen.
      RestartRequested=6 | Patchinstallation wurde abgeschlossen und eine Neustartaktion auf dem Knoten steht aus.
      RestartNotNeeded=7 |  Neustart war nach dem Abschluss der Patchinstallation nicht erforderlich.
      RestartCompleted=8 | Neustart wurde erfolgreich abgeschlossen.
      OperationCompleted=9 | Windows Update-Vorgang wurde erfolgreich abgeschlossen.
      OperationAborted=10 | Windows Update-Vorgang wurde abgebrochen.

6. In v1.4.0 der Anwendung und höher wird bei Abschluss eines Updateversuchs auf einem Knoten ein Ereignis mit der Eigenschaft „WUOperationStatus-[Knotenname]“ auf dem NodeAgentService gepostet, um mitzuteilen, wann der nächste Versuch gestartet wird, das Update herunterzuladen und zu installieren. Siehe Abbildung unten:

     [![Abbildung des Zustands des Windows Update-Vorgangs](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>Diagnoseprotokolle

Protokolle für die App für die Patchorchestrierung werden als Teil der Service Fabric-Laufzeitprotokolle erfasst.

Hinweis für Benutzer, die Protokolle über das gewünschte Diagnosetool/die gewünschte Diagnosepipeline erfassen möchten: Die Patch Orchestration Application protokolliert Ereignisse über [event source](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1) unter Verwendung der folgenden festen Anbieter-IDs:

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Integritätsberichte

Die App für die Patchorchestrierung veröffentlicht in folgenden Fällen auch Integritätsberichte für den Koordinatordienst oder den Knoten-Agent-Dienst:

#### <a name="the-node-agent-ntservice-is-down"></a>Ausfall des Knoten-Agent-NT-Diensts

Wenn der Knoten-Agent-NT-Dienst auf einem Knoten ausgefallen ist, wird für den Knoten-Agent-Dienst ein Integritätsbericht mit Warnstufe generiert.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Nicht aktivierter Reparatur-Manager-Dienst

Wenn der Reparatur-Manager-Dienst im Cluster nicht gefunden werden kann, wird für den Koordinatordienst ein Integritätsbericht mit Warnstufe generiert.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

F: **Warum befindet sich mein Cluster im Status „Fehler“, wenn die App für die Patchorchestrierung ausgeführt wird?**

A. Während des Installationsprozesses deaktiviert die App für die Patchorchestrierung Knoten und/oder startet diese neu. Dies kann dazu führen, dass die Integrität des Clusters vorübergehend sinkt.

Basierend auf der Richtlinie für die Anwendung kann während eines Patchvorgangs ein Knoten *oder* eine ganze Upgradedomäne gleichzeitig heruntergefahren werden.

Nach Abschluss der Windows Update-Installation werden die Knoten neu gestartet und erneut aktiviert.

Im folgenden Beispiel befand sich der Cluster vorübergehend in einem Fehlerzustand, da zwei Knoten ausgefallen waren und die Richtlinie „MaxPercentageUnhealthyNodes“ verletzt wurde. Dies ist ein temporärer Fehler, der nur auftritt, solange der Patchvorgang ausgeführt wird.

![Abbildung eines Clusters mit Fehler](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Sollte das Problem dauerhaft auftreten, lesen Sie die Informationen im Abschnitt zur Problembehandlung.

F: **Die App für die Patchorchestrierung befindet sich im Status „Warnung“.**

A. Überprüfen Sie, ob der für die Anwendung gesendete Integritätsbericht Informationen zur Ursache enthält. Üblicherweise enthält die Warnung Details zum Problem. Wenn das Problem vorübergehend ist, ist zu erwarten, dass die Anwendung automatisch wiederhergestellt wird.

F: **Was kann ich tun, wenn mein Cluster einen Fehler aufweist und ich ein dringendes Betriebssystemupdate ausführen muss?**

A. Die App für die Patchorchestrierung installiert keine Updates, solange der Cluster sich in einem fehlerhaften Zustand befindet. Versuchen Sie, Ihren Cluster in einen fehlerfreien Zustand zu versetzen, um den Workflow der App für die Patchorchestrierung fortzuführen.

F: **Soll ich TaskApprovalPolicy für meinen Cluster auf NodeWise oder UpgradeDomainWise festlegen?**

A. UpgradeDomainWise beschleunigt das gesamte Clusterpatchen, weil alle Knoten einer Upgradedomäne gleichzeitig gepatcht werden. Dies bedeutet, dass alle Knoten einer Upgradedomäne während des Patchprozesses nicht verfügbar wären (im Zustand [Deaktiviert](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

Im Gegensatz dazu patcht die NodeWise-Richtlinie nur jeweils einen Knoten, was bedeutet, dass das gesamte Clusterpatchen mehr Zeit in Anspruch nehmen würde. Es wäre jedoch während des Patchprozesses nur maximal ein Knoten nicht verfügbar (im Zustand [Deaktiviert](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

Wenn Ihr Cluster die Ausführung auf N-1 der Upgradedomänen während des Patchzyklus tolerieren kann (wobei N die Gesamtzahl der Upgradedomänen im Cluster ist), können Sie die Richtlinie UpgradeDomainWise festlegen, andernfalls NodeWise.

F: **Wie lange dauert das Patchen eines Knotens?**

A. Das Patchen eines Knotens kann Minuten (z.B. [Windows Defender-Definitionsupdates](https://www.microsoft.com/en-us/wdsi/definitions)) bis Stunden (z.B. [kumulative Windows-Updates](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)) dauern. Der Zeitaufwand für das Patchen eines Knotens hängt größtenteils von Folgendem ab: 
 - Der Größe der Updates.
 - Der Anzahl der Updates, die in einem Patchfenster angewendet werden müssen.
 - Der zum Installieren der Updates, Neustarten des Knotens (falls erforderlich) und Beenden der nach dem Neustart auszuführenden Installationsschritte benötigten Zeit.
 - Der Leistung von virtuellem/physischem Computer und den Netzwerkbedingungen.

F: **Wie lange dauert es, einen kompletten Cluster zu patchen?**

A. Die zum Patchen eines kompletten Clusters benötigte Zeit hängt von den folgenden Faktoren ab:

- Der zum Patchen eines Knotens benötigten Zeit.
- Richtlinie des Koordinatordiensts. – Die Standardrichtlinie `NodeWise` führt dazu, dass nur jeweils ein Knoten gepatcht wird, was langsamer wäre als `UpgradeDomainWise`. Beispiel:  Angenommen, das Patchen eines Knotens dauert etwa eine Stunde, und es wird ein Cluster mit 20 Knoten (gleichen Typs) gepatcht, bei denen es sich um fünf Upgradedomänen mit jeweils vier Knoten handelt.
    - Das Patchen des gesamten Clusters sollte mit Richtlinie `NodeWise` ca. 20 Stunden dauern.
    - Es sollte mit Richtlinie `UpgradeDomainWise` ca. 5 Stunden dauern.
- Clusterlast: Bei jedem Patchvorgang muss die Kundenworkload zu anderen verfügbaren Knoten im Cluster verschoben werden. Knoten, die gerade gepatcht werden, sind während dieser Zeit im Zustand [Deaktiviert](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling). Wenn der Cluster in der Nähe der Spitzenlast ausgeführt wird, würde die Deaktivierung länger dauern. Daher scheint der gesamte Patchprozess unter solchen Belastungsbedingungen langsam zu sein.
- Clusterintegritätsfehler während des Patchens: Jede [Beeinträchtigung](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) der [Integrität des Clusters](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) würde den Patchprozess unterbrechen. Dies würde die zum Patchen des gesamten Clusters erforderliche Zeit erhöhen.

F: **Warum werden in den über REST-APIs abgerufenen Windows Update-Ergebnissen einige Updates angezeigt, nicht aber im Windows Update-Verlauf auf dem Computer?**

A. Einige Produktupdates werden nur im jeweiligen Update-/Patchverlauf angezeigt. So werden beispielsweise Windows Defender-Updates unter Windows Server 2016 im Windows Update-Verlauf angezeigt oder nicht.

F: **Kann die App für die Patchorchestrierung für das Patchen des Entwicklungsclusters (Einzelknotencluster) verwendet werden?**

A. Nein, die App für die Patchorchestrierung kann nicht für Patchvorgänge in Einzelknotenclustern verwendet werden. Diese Einschränkung ist entwurfsbedingt, da es zu Ausfallzeiten bei [Service Fabric-Systemdiensten](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) oder anderen Kunden-Apps kommen kann, sodass ein Reparaturauftrag für einen Patch nie vom Reparatur-Manager genehmigt werden würde.

F: **Wie patche ich Clusterknoten unter Linux?**

A. Weitere Informationen zum Orchestrieren von Updates unter Linux finden Sie unter [Automatische Betriebssystemimageupgrades mit Azure-VM-Skalierungsgruppen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

F.**Warum dauert der Updatezyklus so lange?**

A. Fragen Sie den JSON-Ergebnistext ab, sehen Sie die Updatezykluseinträge aller Knoten durch, und dann können Sie versuchen, mithilfe von OperationStartTime und OperationTime(OperationCompletionTime) herauszufinden, wieviel Zeit die Updateinstallation auf jedem Knoten in Anspruch genommen hat. Wenn innerhalb eines großen Zeitfensters kein Update stattfand, könnte eine Ursache sein, dass der Cluster sich im Fehlerzustand befand und der Repair Manager darum keine anderen POA-Reparaturtasks genehmigt hat. Wenn die Updateinstallation auf einem beliebigen Knoten lange gedauert hat, wurde der Knoten möglicherweise seit längerer Zeit nicht mehr aktualisiert, sodass die Installation vieler Updates ausstand, was viel Zeit in Anspruch genommen hat. Es ist auch möglich, dass das Patchen auf einem Knoten blockiert ist, weil der Knoten im Deaktivierungszustand stecken geblieben ist, was in der Regel vorkommt, weil das Deaktivieren des Knotens zu Quorums-/Datenverlustsituationen führt.

F: **Warum muss der Knoten deaktiviert werden, wenn POA ihn patcht?**

A. Patch Orchestration Application deaktiviert den Knoten mit „Neustart“-Absicht, wodurch alle auf dem Knoten ausgeführten Service Fabric-Dienste beendet / neu zugewiesen werden. Dies soll sicherstellen, dass Anwendungen keine Mischung aus alten und neuen DLLs verwenden, darum sollte kein Knoten gepatcht werden, ohne deaktiviert zu werden.

## <a name="disclaimers"></a>Haftungsausschlüsse

- Die App für die Patchorchestrierung akzeptiert den Endbenutzer-Lizenzvertrag von Windows Update im Namen des Benutzers. Diese Einstellung kann optional in der Konfiguration der Anwendung deaktiviert werden.

- Die App für die Patchorchestrierung sammelt Telemetriedaten zum Nachverfolgen von Nutzung und Leistung. Die Telemetrieeinstellung der Anwendung folgt der Telemetrieeinstellung der Service Fabric-Laufzeit (standardmäßig ist die Einstellung aktiviert).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="a-node-is-not-coming-back-to-up-state"></a>Ein Knoten wird nicht wieder in den betriebsbereiten Zustand versetzt.

**Der Knoten kann sich aus folgenden Gründen im deaktivierten Zustand befinden:**

Eine Sicherheitsprüfung steht aus. Um in dieser Situation Abhilfe zu schaffen, stellen Sie sicher, dass genügend Knoten in fehlerfreiem Zustand verfügbar sind.

**Der deaktivierte Zustand des Knotens kann aus folgenden Gründen nicht aufgehoben werden**:

- Der Knoten wurde manuell deaktiviert.
- Der Knoten wurde aufgrund eines laufenden Azure-Infrastrukturauftrags deaktiviert.
- Der Knoten wurde von der App für die Patchorchestrierung vorübergehend deaktiviert, um Patches auf den Knoten aufzuspielen.

**Der Ausfall des Knoten kann aus folgenden Gründen nicht aufgehoben werden**:

- Der Knoten wurde manuell in den Zustand „Ausgefallen“ versetzt.
- Der Knoten wird gerade neu gestartet (dies kann auch durch die App für die Patchorchestrierung ausgelöst worden sein).
- Der Knoten ist aufgrund eines fehlerhaften virtuellen Computers oder aufgrund von Netzwerkverbindungsproblemen ausgefallen.

### <a name="updates-were-skipped-on-some-nodes"></a>Updates wurden auf einigen Knoten übersprungen

Die App für die Patchorchestrierung versucht, Windows-Updates gemäß der Neuplanungsrichtlinie zu installieren. Der Dienst versucht gemäß der Anwendungsrichtlinie, den Knoten wiederherzustellen und das Update zu überspringen.

In einem solchen Fall wird ein Integritätsbericht mit Warnstufe für den Knoten-Agent-Dienst generiert. Das Ergebnis der Windows Update-Funktion enthält ebenfalls mögliche Fehlerursachen.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Während der Installation eines Updates wird der Cluster in einen Fehlerzustand versetzt.

Ein fehlerhaftes Windows Update kann die Integrität einer Anwendung oder eines Clusters auf einem bestimmten Knoten oder in einer Upgradedomäne verringern. Die App für die Patchorchestrierung reagiert nicht mehr auf folgende Windows Update-Vorgänge, bis der Cluster wieder fehlerfrei ist.

Ein Administrator muss eingreifen und ermitteln, weshalb die Integrität der Anwendung oder des Clusters aufgrund von Windows Update beeinträchtigt wurde.

## <a name="release-notes"></a>Versionsinformationen

>[!NOTE]
> Versionshinweise und Releases ab Version 1.4.0 finden Sie auf der GitHub-[Releaseseite](https://github.com/microsoft/Service-Fabric-POA/releases/).

### <a name="version-110"></a>Version 1.1.0
- Öffentliche Version

### <a name="version-111"></a>Version 1.1.1
- Korrektur eines Fehlers in „SetupEntryPoint“ von „NodeAgentService“, der die Installation von „NodeAgentNTService“ verhindert hat.

### <a name="version-120"></a>Version 1.2.0

- Korrektur von Fehler im Zusammenhang mit dem Workflow für den Systemneustart.
- Korrektur eines Fehlers bei der Erstellung von RM-Aufgaben, durch den die Integritätsüberprüfung während Reparaturvorbereitungsaufgaben nicht wie erwartet erfolgt ist.
- Änderung des Startmodus für den Windows-Dienst „POANodeSvc“ von automatisch in verzögert automatisch.

### <a name="version-121"></a>Version 1.2.1

- Fehlerbehebung im Workflow zum Herunterskalieren eines Clusters. Logik der automatischen Speicherbereinigung für POA-Reparaturaufgaben eingeführt, die nicht vorhandenen Knoten angehören.

### <a name="version-122"></a>Version 1.2.2

- Verschiedene Fehlerbehebungen
- Binärdateien werden jetzt signiert.
- SFPKG-Link für die Anwendung wurde hinzugefügt.

### <a name="version-130"></a>Version 1.3.0

- Nach dem Festlegen von InstallWindowsOSOnlyUpdates auf FALSE werden jetzt alle verfügbaren Updates installiert.
- Die Logik für das Deaktivieren automatischer Updates wurde geändert. Dies behebt einen Fehler, durch den automatische Updates auf Server 2016 und höher nicht deaktiviert wurden.
- Einschränkungen der parametrisierten Platzierung für beide Microservices von POA für erweiterte Anwendungsfälle.

### <a name="version-131"></a>Version 1.3.1
- Behebung der Regression, die dazu führt, dass POA 1.3.0 unter Windows Server 2012 R2 oder einer niedrigeren Version aufgrund eines Fehlers beim Deaktivieren automatischer Updates nicht funktioniert 
- Behebung eines Fehlers, der dazu führt, dass die InstallWindowsOSOnlyUpdates-Konfiguration immer als „True“ verwendet wird
- Änderung des Standardwerts von „InstallWindowsOSOnlyUpdates“ in „False“

### <a name="version-132"></a>Version 1.3.2
- Behebung eines Problems, das den Patchinglebenszyklus auf einem Knoten beeinflusst, wenn Knoten einen Namen aufweisen, der Teil des aktuellen Knotennamens ist. Bei diesen Knoten wird das Patching u. U. nicht ausgeführt, oder der Neustart steht aus. 