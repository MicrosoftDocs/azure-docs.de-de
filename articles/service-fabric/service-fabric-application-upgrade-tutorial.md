---
title: 'Tutorial: Service Fabric-Anwendungsupgrades'
description: Dieser Artikel bietet eine exemplarische Vorgehensweise für das Bereitstellen einer Service Fabric-Anwendung, das Ändern des Codes und das Einführen eines Upgrades mithilfe von Visual Studio.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: acde2f4e51bee29d2eefb0d5fbb54fbe421a41f1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996235"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Tutorial für das Upgraden von Service Fabric-Anwendungen mithilfe von Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric vereinfacht das Upgraden von Cloudanwendungen, indem sichergestellt wird, dass nur geänderte Dienste aktualisiert werden und die Anwendungsintegrität während des gesamten Upgradeprozesses überwacht wird. Falls ein Fehler auftritt, wird die Anwendung automatisch auf die vorherigen Version zurückgesetzt. Upgrades von Service Fabric-Anwendungen erfolgen *ohne Ausfallzeiten*, da die Anwendung ohne Ausfallzeiten aktualisiert werden kann. In diesem Tutorial wird beschrieben, wie ein paralleles Upgrade über Visual Studio erfolgt.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Schritt 1: Erstellen und Veröffentlichen des Visual Objects-Beispiels
Laden Sie zuerst die Anwendung [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) von GitHub herunter. Erstellen und veröffentlichen Sie dann die Anwendung, indem Sie mit der rechten Maustaste auf das Anwendungsprojekt **VisualObjects** klicken und im Menü „Service Fabric“ den Befehl **Veröffentlichen** auswählen.

![Kontextmenü einer Service Fabric-Anwendung][image1]

Durch Auswahl von **Veröffentlichen** wird ein Popupfenster geöffnet. Sie können das **Zielprofil** auf **PublishProfiles\Local.xml** festlegen. Das Fenster sollte wie folgt aussehen, bevor Sie auf **Veröffentlichen** klicken.

![Veröffentlichen einer Service Fabric-Anwendung][image2]

Sie können nun im Dialogfeld auf **Veröffentlichen** klicken. Nun können Sie in [Service Fabric Explorer den Cluster und die Anwendung anzeigen](service-fabric-visualizing-your-cluster.md). Die Anwendung Visual Objects enthält einen Webdienst, zu dem Sie in Ihrem Browser wechseln können, indem Sie `http://localhost:8081/visualobjects/` in die Adressleiste eingeben.  Sie sollten 10 unverankerte visuelle Objekte sehen, die sich auf dem Bildschirm bewegen.

**HINWEIS:** Bei Bereitstellen im Profil `Cloud.xml` (Azure Service Fabric) sollte die Anwendung unter **http://{ServiceFabricName}.{Region}.cloudapp.azure.com:8081/visualobjects/** verfügbar sein. Stellen Sie sicher, dass Sie `8081/TCP` im Load Balancer konfiguriert haben (der Load Balancer befindet sich in derselben Ressourcengruppe wie die Service Fabric-Instanz).

## <a name="step-2-update-the-visual-objects-sample"></a>Schritt 2: Aktualisieren des Beispiels „Visual Objects“
Sie werden feststellen, dass sich die visuellen Objekte mit der Version, die in Schritt 1 bereitgestellt wurde, nicht drehen. Wir aktualisieren diese Anwendung so, dass sich die visuellen Objekte drehen.

Wählen Sie das Projekt „VisualObjects.ActorService“ in der Projektmappe „VisualObjects“ aus, und öffnen Sie die Datei **VisualObjectActor.cs** . Navigieren Sie in der Datei zur `MoveObject`-Methode, kommentieren Sie `visualObject.Move(false)` aus, und heben Sie die Auskommentierung für `visualObject.Move(true)` auf. Diese Codeänderung bewirkt, dass sich die Objekte nach dem Upgrade des Diensts drehen.  **Jetzt können Sie die Projektmappe erstellen (nicht neu erstellen)** , die die geänderten Projekte erstellt. Wenn Sie die Option *Alles neu erstellen* auswählen, müssen Sie die Versionen für alle Projekte aktualisieren.

Wir müssen unsere Anwendung auch mit einer Version versehen. Sie können die Option **Manifestversionen bearbeiten** von Visual Studio verwenden, um die Versionen zu ändern, nachdem Sie mit einem Rechtsklick das **VisualObjects**-Projekt ausgewählt haben. Bei Wahl dieser Option wird das Dialogfeld zum Bearbeiten von Versionen wie folgt angezeigt:

![Dialogfeld Versionsverwaltung][image3]

Aktualisieren Sie die Versionen für die geänderten Projekte und ihre Codepakete zusammen mit der Anwendung auf Version 2.0.0. Nach diesen Änderungen sollte das Manifest wie folgt aussehen (Änderungen sind fett hervorgehoben):

![Aktualisieren von Versionen][image4]

Die Visual Studio-Tools können automatische Rollups von Versionen durchführen, wenn Sie die Option **Anwendung und Dienstversionen automatisch aktualisieren** auswählen. Wenn Sie [SemVer](http://www.semver.org)verwenden, müssen Sie den Code und/oder die Version der Paketkonfiguration aktualisieren, wenn diese Option ausgewählt ist.

Speichern Sie die Änderungen, und aktivieren Sie nun das Kontrollkästchen **Anwendungs- und Dienstversionen automatisch aktualisieren** .

## <a name="step-3--upgrade-your-application"></a>Schritt 3:  Upgraden Ihrer Anwendung
Machen Sie sich mit den [Parametern für Anwendungsupgrades](service-fabric-application-upgrade-parameters.md) und dem [Upgradeprozess](service-fabric-application-upgrade.md) vertraut, um ein tieferes Verständnis der verschiedenen anwendbaren Upgradeparameter, Timeouts und Integritätskriterien zu erhalten. In dieser exemplarischen Vorgehensweise ist das Kriterium für die Evaluierung der Systemintegrität auf die Standardeinstellung (nicht überwachter Modus) festgelegt. Sie können diese Einstellungen konfigurieren, indem Sie **Upgradeeinstellungen konfigurieren** auswählen und die Parameter anschließend wie gewünscht ändern.

Nun können wir das Anwendungsupgrade über die Schaltfläche **Veröffentlichen** starten. Dadurch wird die Anwendung auf Version 2.0.0 aktualisiert, in der sich die Objekte drehen. Service Fabric aktualisiert Updatedomänen nacheinander (einige Objekte werden zuerst aktualisiert, dann folgen andere). Auf den Dienst kann während des Upgrades zugegriffen werden. Der Zugriff auf den Dienst kann mit Ihrem Client (Browser) überprüft werden.  

Sie können den Fortschritt des Upgradevorgangs mithilfe von Service Fabric Explorer über die Registerkarte **Upgrades in Progress** (Laufende Upgrades) der jeweiligen Anwendung überwachen.

Alle Updatedomänen sollten binnen wenigen Minuten aktualisiert sein und im Visual Studio-Ausgabefenster sollte angezeigt werden, dass das Upgrade abgeschlossen ist. Sie werden feststellen, dass sich jetzt *alle* visuellen Objekte in Ihrem Browserfenster drehen!

Sie können auch versuchen, als Übung Version 2.0.0 in Version 3.0.0 oder gar Version 2.0.0 zurück in Version 1.0.0 zu ändern. Experimentieren Sie mit den Timeouts und Integritätsrichtlinien, um sich schrittweise mit ihnen vertraut zu machen. Wenn die Bereitstellung in einem Azure-Cluster statt in einem lokalen Cluster erfolgt, sind die verwendeten Parameter möglicherweise unterschiedlich. Es wird empfohlen, dass Sie die Timeouts konservativ festlegen.

## <a name="next-steps"></a>Nächste Schritte
[Upgrade Ihrer Anwendung mithilfe von PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) werden Sie schrittweise durch das Upgrade der Anwendung mithilfe von PowerShell geführt.

Steuern Sie die Upgrades von Anwendungen mithilfe von [Upgradeparametern](service-fabric-application-upgrade-parameters.md).

Machen Sie sich mit der [Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)vertraut, um Ihre Anwendungsupgrades kompatibel zu machen.

Informieren Sie sich in [weiterführenden Themen](service-fabric-application-upgrade-advanced.md)darüber, wie Sie erweiterte Funktionen beim Upgrade Ihrer Anwendung nutzen.

Informationen zum Beheben gängiger Probleme bei Anwendungsupgrades finden Sie in den Anweisungen unter [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
