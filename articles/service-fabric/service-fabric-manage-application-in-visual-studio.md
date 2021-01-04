---
title: Verwalten von Anwendungen in Visual Studio
description: Verwenden Sie Visual Studio zum Erstellen, Entwickeln, Packen, Bereitstellen und Debuggen Ihrer Azure Service Fabric-Anwendungen und -Dienste.
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 1fd9295a0dead180767febcc5339ef0a25cb1e48
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574546"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Verwenden von Visual Studio zum Vereinfachen des Schreibens und Verwaltens Ihrer Service Fabric-Anwendung
Sie können Ihre Azure Service Fabric-Anwendungen und -Dienste in Visual Studio verwalten. Sobald Sie die [Einrichtung Ihrer Entwicklungsumgebung](service-fabric-get-started.md)abgeschlossen haben, können Sie mit Visual Studio Service Fabric-Anwendungen erstellen, Dienste hinzufügen und Anwendungen packen, registrieren und im lokalen Entwicklungscluster bereitstellen.

## <a name="deploy-your-service-fabric-application"></a>Bereitstellen der Service Fabric-Anwendung
Beim Bereitstellen einer Anwendung werden die folgenden Schritte standardmäßig zu einem einfachen Vorgang zusammengefasst:

1. Erstellen des Anwendungspakets
2. Hochladen des Anwendungspakets in den Image-Speicher
3. Registrieren des Anwendungstyps
4. Entfernen ausgeführter Anwendungsinstanzen
5. Erstellen einer Anwendungsinstanz

Sie können in Visual Studio **F5** drücken, um die Anwendung bereitzustellen und den Debugger an alle Anwendungsinstanzen anzufügen. Mit **STRG+F5** können Sie eine Anwendung ohne Debuggen bereitstellen oder sie mithilfe des Veröffentlichungsprofils in einem lokalen bzw. Remotecluster veröffentlichen.

### <a name="application-debug-mode"></a>Debugmodus für die Anwendung
Visual Studio stellt eine Eigenschaft mit dem Namen **Anwendungsdebugmodus** bereit, die steuert, wie Visual Studio Anwendungsbereitstellungen im Rahmen des Debuggens behandeln soll.

#### <a name="to-set-the-application-debug-mode-property"></a>So legen Sie die „Anwendungsdebugmodus“-Eigenschaft fest
1. Wählen Sie im Kontextmenü des Service Fabric-Anwendungsprojekts (*.sfproj) **Eigenschaften** aus (oder drücken Sie die Taste **F4**).
2. Legen Sie im Fenster **Eigenschaften** die Eigenschaft **Anwendungsdebugmodus** fest.

![Festlegen der „Application Debug Mode“-Eigenschaft][debugmodeproperty]

#### <a name="application-debug-modes"></a>Anwendungsdebugmodi

1. **Anwendung aktualisieren** Dieser Modus ermöglicht das schnelle Ändern und Debuggen Ihres Codes und unterstützt die Bearbeitung statischer Webdateien beim Debuggen. Dieser Modus funktioniert nur, wenn sich Ihr lokaler Entwicklungscluster im 1-Knoten-Modus befindet. Dies ist der Anwendungsdebug-Standardmodus.
2. **Anwendung entfernen** bewirkt, dass die Anwendung entfernt wird, wenn die Debugsitzung endet.
3. **Automatisches Upgrade** Die Anwendung wird weiter ausgeführt, nachdem die Debugsitzung beendet wurde. Die nächste Debugsitzung behandelt die Bereitstellung als Upgrade. Beim Upgradevorgang werden alle Daten beibehalten, die Sie in einer vorherigen Debugsitzung eingegeben haben.
4. **Anwendung beibehalten** Die Anwendung wird weiter im Cluster ausgeführt, nachdem die Debugsitzung beendet wurde. Zu Beginn der nächsten Debugsitzung wird die Anwendung entfernt.

Beim **Automatischen Upgrade** werden Daten beibehalten, wenn die Anwendungsupgradefunktionen von Service Fabric angewendet werden. Weitere Informationen zum Upgrade von Anwendungen sowie zur Durchführung eines Upgrades in der Praxis finden Sie unter [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Fügen Sie der Service Fabric-Anwendung einen Dienst hinzu.
Sie können der Anwendung neue Dienste hinzufügen, um den Funktionsumfang der Anwendung zu erweitern. Um sicherzustellen, dass der Dienst in das Anwendungspaket aufgenommen wird, fügen Sie den Dienst über das Menüelement **Neuer Fabric-Dienst** hinzu.

![Hinzufügen eines neuen Service Fabric-Diensts][newservice]

Wählen Sie den Service Fabric-Projekttyp aus, der zur Anwendung hinzugefügt werden soll, und geben Sie einen Namen für den Dienst an.  Lesen Sie die Informationen in [Auswählen eines Frameworks für den Dienst](service-fabric-choose-framework.md) , um zu entscheiden, welcher Diensttyp verwendet werden soll.

![Wählen Sie einen Projekttyp des Service Fabric-Diensts aus, der Ihrer Anwendung hinzugefügt werden soll.][addserviceproject]

Der neue Dienst wird zu Ihrer Lösung und dem vorhandenen Anwendungspaket hinzugefügt. Die Servicereferenzen und eine Standarddienstinstanz werden zum Anwendungsmanifest hinzugefügt. Dies führt dazu, dass der Dienst erstellt und gestartet wird, wenn Sie die Anwendung das nächste Mal bereitstellen.

![Der neue Dienst wird zum Anwendungsmanifest hinzugefügt.][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Packen der Service Fabric-Anwendung
Um die Anwendung und die zugehörigen Dienste in einem Cluster bereitzustellen, müssen Sie ein Anwendungspaket erstellen.  Das Paket ordnet das Anwendungsmanifest, die Dienstmanifeste und andere erforderliche Dateien in einem bestimmten Layout an.  Visual Studio richtet das Paket im Projektordner der Anwendung im Verzeichnis "Pkg" ein und verwaltet es.  Klicken Sie zum Erstellen oder Aktualisieren des Anwendungspakets im Kontextmenü der **Anwendung** auf **Packen**.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Entfernen von Anwendungen und Anwendungstypen mit dem Cloud-Explorer
Einfache Clusterverwaltungsvorgänge können in Visual Studio mit dem Cloud-Explorer ausgeführt werden, den Sie über das Menü **Ansicht** starten können. Damit können Sie beispielsweise Anwendung löschen und die Bereitstellung von Anwendungstypen in lokalen Clustern oder Remoteclustern aufheben.

![Entfernen einer Anwendung][removeapplication]

> [!TIP]
> Informationen zu komplexeren Clusterverwaltungsfunktionen finden Sie unter [Visualisieren Ihres Clusters mit Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nächste Schritte
* [Service Fabric-Anwendungsmodell](service-fabric-application-model.md)
* [Service Fabric-Anwendungsbereitstellung](service-fabric-deploy-remove-applications.md)
* [Verwalten von Anwendungsparametern für mehrere Umgebungen](service-fabric-manage-multiple-environment-app-configuration.md)
* [Debuggen einer Service Fabric-Anwendung](service-fabric-debugging-your-application.md)
* [Visualisieren des Clusters mit Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
