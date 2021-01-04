---
title: 'Anwendungsupgrade: Weiterführende Themen'
description: Dieser Artikel behandelt einige weiterführende Themen in Bezug auf Upgrades von Service Fabric-Anwendungen.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: cc2fdc8f99b74078bd8d5274cbe52265ab8455ae
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022988"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric-Anwendungsupgrade: Erweiterte Themen

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Hinzufügen oder Entfernen von Diensttypen während eines Anwendungsupgrades

Wenn einer veröffentlichten Anwendung bei einem Upgrade ein neuer Diensttyp hinzugefügt wird, wird der neue Dienst der bereitgestellten Anwendung hinzugefügt. Ein solches Upgrade hat keine Auswirkungen auf Dienstinstanzen, die bereits Teil der Anwendung waren. Es muss jedoch eine Instanz des hinzugefügten Diensttyps erstellt werden, damit der neue Diensttyp aktiviert wird (siehe [New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Entsprechend können im Rahmen eines Upgrades Diensttypen auch aus einer Anwendung entfernt werden. Alle Dienstinstanzen des Diensttyps, der entfernt werden soll, müssen jedoch vor dem Upgrade entfernt werden (siehe [Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Vermeiden von Verbindungsabbrüchen bei geplanten Ausfallzeiten für zustandslose Dienste

Bei geplanten Ausfallzeiten für zustandslose Instanzen (z. B. Anwendungs-/Clusterupgrades oder Knotendeaktivierung) können Verbindungen abgebrochen werden, weil der verfügbar gemachte Endpunkt nach dem Ausfall der Instanz entfernt wird und hierdurch das Schließen der Verbindung erzwungen wird.

Um dies zu vermeiden, konfigurieren Sie das Feature *RequestDrain*, indem Sie der Dienstkonfiguration eine *Verzögerung beim Schließen von Instanzen* hinzufügen, um vorhandene Anforderungen aus dem auszugleichenden Cluster an den verfügbar gemachten Endpunkten zuzulassen. Dies wird dadurch erreicht, dass der von der zustandslosen Instanz angekündigte Endpunkt entfernt wird, *bevor* die Verzögerung vor dem Schließen der Instanz gestartet wird. Diese Verzögerung ermöglicht das ordnungsgemäße Entladen vorhandener Anforderungen, bevor die Instanz tatsächlich ausfällt. Clients werden beim Starten der Verzögerung von einer Rückruffunktion über die Endpunktänderung benachrichtigt, sodass sie den Endpunkt erneut auflösen und damit vermeiden können, dass neue Anforderungen an die Instanz gesendet werden, die ausfällt. Diese Anforderungen könnten von Clients stammen, die [Reverseproxy](./service-fabric-reverseproxy.md) oder Auflösungs-APIs des Dienstendpunkts mit dem Benachrichtigungsmodell ([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) zum Aktualisieren der Endpunkte verwenden.

### <a name="service-configuration"></a>Dienstkonfiguration

Es gibt mehrere Möglichkeiten, die Verzögerung auf der Dienstseite zu konfigurieren.

 * Geben Sie **beim Erstellen eines neuen Diensts** eine `-InstanceCloseDelayDuration` an:

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **Wenn Sie den Dienst im Abschnitt für die Standardeinstellungen im Anwendungsmanifest definieren**, weisen Sie die `InstanceCloseDelayDurationSeconds`-Eigenschaft zu:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Geben Sie beim Aktualisieren eines vorhandenen Diensts** eine `-InstanceCloseDelayDuration` an:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **Geben Sie beim Erstellen oder Aktualisieren eines vorhandenen Diensts über die ARM-Vorlage** den `InstanceCloseDelayDuration`-Wert an (unterstützte API-Mindestversion: 2019-11-01-preview):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>Clientkonfiguration

Um Benachrichtigungen zu erhalten, wenn sich ein Endpunkt geändert hat, sollten Clients einen Rückruf registrieren (siehe [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)).
Die Änderungsbenachrichtigung ist ein Hinweis darauf, dass sich die Endpunkte geändert haben. Der Client sollte die Endpunkte erneut auflösen und die Endpunkte nicht mehr verwenden, die nicht mehr angekündigt werden, da sie bald ausfallen werden.

### <a name="optional-upgrade-overrides"></a>Optionale Upgradeüberschreibungen

Zusätzlich zum Festlegen der Standardverzögerungsdauer pro Dienst können Sie die Verzögerung auch während des Anwendungs-/Clusterupgrades überschreiben, indem Sie die gleiche Option (`InstanceCloseDelayDurationSec`) verwenden:

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

Die außer Kraft gesetzte Verzögerungsdauer gilt nur für die aufgerufene Upgradeinstanz und ändert die Konfigurationen einzelner Dienst Verzögerungen nicht anderweitig. Beispielsweise können Sie diese Option verwenden, um eine Verzögerung von `0` anzugeben, um vorkonfigurierte Upgradeverzögerungen zu überspringen.

> [!NOTE]
> * Die Einstellungen zum Ausgleichen von Anforderungen können nicht verhindern, dass der Azure Load Balancer neue Anforderungen an die Endpunkte sendet, auf die der Ausgleich angewendet wird.
> * Ein beschwerdebasierter Auflösungsmechanismus wird nicht zu einem eleganten Ausgleich von Anforderungen führen, da er nach einem Fehler eine Dienstauflösung auslöst. Wie bereits beschrieben, sollte dies stattdessen erweitert werden, um die Änderungsbenachrichtigungen für den Endpunkt mit [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription) zu abonnieren.
> * Die Einstellungen werden nicht berücksichtigt, wenn es sich um ein Upgrade ohne Auswirkungen handelt, d. h. wenn die Replikate während des Upgrades nicht heruntergefahren werden.
>
>

> [!NOTE]
> Dieses Feature kann in vorhandenen Diensten mit dem oben erwähnten Cmdlet „Update-ServiceFabricService“ oder der ARM-Vorlage konfiguriert werden, wenn die Clustercodeversion 7.1.xxx oder höher lautet.
>
>

## <a name="manual-upgrade-mode"></a>Manueller Upgrademodus

> [!NOTE]
> Der *Monitored*-Upgrademodus wird für alle Service Fabric-Upgrades empfohlen.
> Der *UnmonitoredManual*-Upgrademodus sollte ausschließlich bei einem fehlerhaften oder angehaltenen Upgrade in Betracht gezogen werden. 
>
>

Beim *Monitored*-Modus wendet Service Fabric Integritätsrichtlinien an, um sicherzustellen, dass die Anwendung im Verlauf des Upgrades fehlerfrei ist. Wenn die Integritätsrichtlinien verletzt werden, wird abhängig von der angegebenen *FailureAction* das Upgrade entweder angehalten oder automatisch ein Rollback ausgeführt.

Im *UnmonitoredManual*-Modus hat der Anwendungsadministrator vollständige Kontrolle über den Fortschritt des Upgrades. Dieser Modus ist hilfreich, wenn eine benutzerdefinierte Auswertung von Integritätsrichtlinien durchgeführt werden soll oder wenn unkonventionelle Upgrades durchgeführt werden, bei denen die Überwachung der Systemintegrität vollständig umgangen wird (z.B. wenn die Anwendung bereits Datenverluste aufweist). Ein Upgrade in diesem Modus wird automatisch nach Abschluss jeder Upgradedomäne angehalten. Es muss mit [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) ausdrücklich fortgesetzt werden. Wenn ein Upgrade angehalten wurde und bereit ist, vom Benutzer fortgesetzt zu werden, lautet der Upgradestatus *RollforwardPending* (siehe [UpgradeState](/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Schließlich eignet sich der *UnmonitoredAuto*-Modus für schnelle Upgradedurchläufe während der Dienstentwicklung oder der Testphase, da keine Benutzereingaben erforderlich sind und keine Richtlinien zur Anwendungsintegrität ausgewertet werden.

## <a name="upgrade-with-a-diff-package"></a>Upgrade mit einem Diff-Paket

Anstatt ein vollständiges Anwendungspaket bereitzustellen, können Upgrades auch durch die Bereitstellung von Diff-Paketen durchgeführt werden. Diese enthalten nur die aktualisierten Code-/Konfigurations-/Datenpakete sowie das vollständige Anwendungsmanifest und sämtliche Dienstmanifeste. Vollständige Anwendungspakete werden für die Erstinstallation einer Anwendung im Cluster benötigt. Nachfolgende Upgrades können über vollständige Anwendungspakete oder Diff-Pakete erfolgen.  

Alle Verweise im Anwendungsmanifest oder den Dienstmanifesten eines Diff-Pakets, die im Anwendungspaket nicht gefunden werden können, werden automatisch durch die derzeit bereitgestellte Version ersetzt.

Szenarien für die Verwendung eines Diff-Pakets:

* Das Anwendungspaket ist sehr umfangreich und enthält Verweise auf mehrere Dienstmanifestdateien sowie mehrere Code-, Konfigurations- oder Datenpakete.
* Das Bereitstellungssystem generiert das Buildlayout direkt aus dem Anwendungsbuildprozess. In diesem Fall weisen neu erstellte Assemblys eine andere Prüfsumme auf, auch wenn sich der Code nicht geändert hat. Bei Verwendung eines vollständigen Anwendungspakets müssten Sie die Version in allen Codepaketen ändern. Bei Verwendung eines Diff-Pakets stellen Sie dagegen nur die geänderten Dateien und die Manifestdateien bereit, in denen sich die Version geändert hat.

Wenn eine Anwendung mit Visual Studio aktualisiert wird, wird automatisch ein Diff-Paket veröffentlicht. Um ein Diff-Paket manuell zu erstellen, müssen das Anwendungsmanifest und die Dienstmanifeste aktualisiert werden. Doch nur die geänderten Pakete dürfen in das endgültige Anwendungspaket eingeschlossen werden.

Beispielsweise beginnen wir mit der folgenden Anwendung (Versionsnummern werden zum einfacheren Verständnis angegeben):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Nehmen Sie an, dass Sie nur das Codepaket von Service1 mithilfe eines Diff-Pakets aktualisieren möchten. Ihre aktualisierte Anwendung enthält die folgenden Versionsänderungen:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

In diesem Fall aktualisieren Sie das Anwendungsmanifest auf 2.0.0 und das Dienstmanifest für Service1, um die Codepaketaktualisierung widerzuspiegeln. Der Ordner Ihres Anwendungspakets hat dann die folgende Struktur:

```text
app1/
  service1/
    code/
```

Das heißt, Sie erstellen wie gewohnt ein vollständiges Anwendungspaket und entfernen dann alle Ordner mit Code-/Konfigurations-/Datenpaketen, deren Version nicht geändert wurde.

## <a name="upgrade-application-parameters-independently-of-version"></a>Aktualisieren von Anwendungsparametern unabhängig von der Version

Manchmal ist es wünschenswert, die Parameter einer Service Fabric-Anwendung zu ändern, ohne die Manifestversion zu ändern. Dies kann ganz einfach mithilfe des Flags **-ApplicationParameter** mit dem Azure Service Fabric PowerShell-Cmdlet **Start-ServiceFabricApplicationUpgrade** erfolgen. Gehen Sie von einer Service Fabric-Anwendung mit den folgenden Eigenschaften aus:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Starten Sie nun das Anwendungsupgrade mithilfe des Cmdlets **Start ServiceFabricApplicationUpgrade**. Dieses Beispiel zeigt ein überwachtes Upgrade, es kann aber auch ein nicht überwachtes Upgrade verwendet werden. Eine vollständige Beschreibung der Flags, die von diesem Cmdlet akzeptiert werden, finden Sie in der [Referenz zum Azure Service Fabric PowerShell-Modul](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters).

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Vergewissern Sie sich nach dem Upgrade, dass die Anwendung über die aktualisierten Parameter und die gleiche Version verfügt:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Ausführen von Rollbacks für Anwendungsupgrades

Während Upgrades in einem von drei Modi ausgeführt werden können (*Monitored*, *UnmonitoredAuto* oder *UnmonitoredManual*), können Rollback nur im Modus *UnmonitoredAuto* oder *UnmonitoredManual* ausgeführt werden. Ein Rollback im *UnmonitoredAuto*-Modus funktioniert genauso wie das Rollforward – lediglich der Standardwert von *UpgradeReplicaSetCheckTimeout* unterscheidet sich (siehe [Parameter für Anwendungsupgrades](service-fabric-application-upgrade-parameters.md)). Ein Rollback im *UnmonitoredManual*-Modus funktioniert genauso wie das Rollforward – das Rollback wird automatisch nach jeder Upgradedomäne angehalten und muss explizit mit [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) fortgesetzt werden.

Rollbacks können automatisch ausgelöst werden, wenn die Integritätsrichtlinien eines Upgrades im *Monitored*-Modus mit einer *FailureAction* mit dem Wert *Rollback* verletzt werden (siehe [Parameter für Anwendungsupgrades](service-fabric-application-upgrade-parameters.md)), oder ausdrücklich mit [Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Während des Rollbacks können der Wert von *UpgradeReplicaSetCheckTimeout* und der Modus weiterhin jederzeit mit [Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps) geändert werden.

## <a name="next-steps"></a>Nächste Schritte
[Ihre Anwendung mit Visual Studio upgraden](service-fabric-application-upgrade-tutorial.md) beschreibt das Upgraden von Anwendungen mit Visual Studio.

[Ihre Anwendung mit PowerShell upgraden](service-fabric-application-upgrade-tutorial-powershell.md) beschreibt das Upgraden von Anwendungen mit PowerShell.

Steuern Sie die Upgrades von Anwendungen mithilfe von [Upgradeparametern](service-fabric-application-upgrade-parameters.md).

Machen Sie Ihre Anwendungsupgrades kompatibel, indem Sie sich mit der [Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)vertraut machen.

Informationen zum Beheben gängiger Probleme bei Anwendungsupgrades finden Sie in den Anweisungen unter [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md).
