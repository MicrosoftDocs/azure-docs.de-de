---
title: Schnelles Bereitstellen einer vorhandenen App in einem Cluster
description: Verwenden Sie einen Azure Service Fabric-Cluster, um mit Visual Studio eine vorhandene Node.js-Anwendung zu hosten.
ms.topic: conceptual
ms.date: 12/06/2017
ms.custom: devx-track-js
ms.openlocfilehash: dfd1fe9db54925bc17a53a7686fa34d5ea53cd5a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013597"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Hosten einer Node.js.-Anwendung in Azure Service Fabric

Dieser Schnellstart dient Ihnen als Hilfe beim Bereitstellen einer vorhandenen Anwendung (hier: Node.js) für einen Service Fabric-Cluster, der in Azure ausgeführt wird.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass [Ihre Entwicklungsumgebung eingerichtet ist](service-fabric-get-started.md). Dies umfasst auch das Installieren des Service Fabric SDK sowie von Visual Studio 2019 oder 2015.

Sie müssen auch über eine vorhandene Node.js-Anwendung für die Bereitstellung verfügen. In diesem Schnellstart wird eine einfache Node.js-Website verwendet, die Sie [hier][download-sample] herunterladen können. Extrahieren Sie diese Datei in den Ordner `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\`, nachdem Sie das Projekt im nächsten Schritt erstellt haben.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][create-account] erstellen.

## <a name="create-the-service"></a>Erstellen des Diensts

Starten Sie Visual Studio als **Administrator**.

Erstellen Sie mit `CTRL`+`SHIFT`+`N` ein Projekt.

Wählen Sie im Dialogfeld **Neues Projekt** die Option **Cloud > Service Fabric-Anwendung** aus.

Geben Sie der Anwendung den Namen **MyGuestApp**, und klicken Sie auf **Erstellen**.

>[!IMPORTANT]
>Bei Node.js kann es leicht passieren, dass für Pfade die maximale Zeichenzahl von 260 überschritten wird, die für Windows gilt. Verwenden Sie für das eigentliche Projekt einen kurzen Pfad, z.B. **c:\code\svc1**. Optional können Sie **[diese Anweisungen](https://stackoverflow.com/a/41687101/1664231)** befolgen, um lange Dateipfade unter Windows 10 zuzulassen.
   
![Dialogfeld „Neues Projekt“ in Visual Studio][new-project]

Mit dem nächsten Dialogfeld können Sie eine beliebige Art von Service Fabric-Dienst erstellen. Wählen Sie für diesen Schnellstart die Option **Ausführbare Gastdatei**.

Geben Sie dem Dienst den Namen **MyGuestService**, und legen Sie für die Optionen auf der rechten Seite die folgenden Werte fest:

| Einstellung                   | value |
| ------------------------- | ------ |
| Codepaketordner       | _&lt;Ordner mit Ihrer Node.js-App&gt;_ |
| Codepaketverhalten     | Ordnerinhalte in Projekt kopieren |
| Programm                   | node.exe |
| Argumente                 | server.js |
| Arbeitsordner            | CodePackage |

Klicken Sie auf **OK**.

![Dialogfeld „Neuer Dienst“ in Visual Studio][new-service]

Visual Studio erstellt das Anwendungsprojekt und das Actordienst-Projekt und zeigt sie im Projektmappen-Explorer an.

Das Anwendungsprojekt (**MyGuestApp**) enthält Code nicht direkt. Das Projekt verweist auf eine Reihe von Dienstprojekten. Darüber hinaus enthält es drei weitere Arten von Inhalten:

* **Veröffentlichungsprofile**  
Tooleinstellungen für verschiedene Umgebungen.

* **Skripts**  
Ein PowerShell-Skript für die Bereitstellung bzw. Aktualisierung der Anwendung.

* **Anwendungsdefinition**  
Enthält das Anwendungsmanifest unter *ApplicationPackageRoot*. Die zugehörigen Anwendungsparameterdateien befinden sich unter *ApplicationParameters*. Sie definieren die Anwendung und ermöglichen es Ihnen, diese speziell für eine bestimmte Umgebung zu konfigurieren.
    
Eine Übersicht über den Inhalt des Dienstprojekts finden Sie unter [Erste Schritte mit Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="set-up-networking"></a>Einrichten von Netzwerken

In der von uns bereitgestellten Node.js-Beispiel-App wird Port **80** verwendet, und wir müssen Service Fabric mitteilen, dass dieser Port verfügbar gemacht werden muss.

Öffnen Sie die Datei **ServiceManifest.xml** im Projekt. Unten im Manifests befindet sich ein `<Resources> \ <Endpoints>`-Element, für das bereits ein Eintrag definiert ist. Ändern Sie diesen Eintrag, um `Port`, `Protocol` und `Type` hinzuzufügen. 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Bereitstellen in Azure

Wenn Sie **F5** drücken und das Projekt ausführen, wird es im lokalen Cluster bereitgestellt. Wir führen stattdessen aber die Bereitstellung in Azure durch.

Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen...** , um ein Dialogfeld für die Veröffentlichung in Azure zu öffnen.

![Dialogfeld zum Veröffentlichen in Azure für einen Service Fabric-Dienst][publish]

Wählen Sie das Zielprofil **PublishProfiles\Cloud.xml**.

Wählen Sie ein Azure-Konto für die Bereitstellung aus, falls Sie dies nicht bereits getan haben. Wenn Sie noch kein Azure-Konto besitzen, [können Sie sich dafür registrieren][create-account].

Wählen Sie unter **Verbindungsendpunkt** den Service Fabric-Cluster für die Bereitstellung aus. Falls kein Cluster vorhanden ist, wählen Sie die Option **&lt;Neuen Cluster erstellen...&gt;** aus, um ein Webbrowserfenster mit dem Azure-Portal zu öffnen. Weitere Informationen finden Sie unter [Erstellen eines Service Fabric-Clusters in Azure über das Azure-Portal](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal). 

Stellen Sie beim Erstellen des Service Fabric-Clusters sicher, dass Sie die Einstellung **Benutzerdefinierte Endpunkte** auf **80** festlegen.

![Konfiguration des Service Fabric-Knotentyps mit benutzerdefiniertem Endpunkt][custom-endpoint]

Die Erstellung eines neuen Service Fabric-Clusters nimmt einige Zeit in Anspruch. Navigieren Sie nach der Erstellung zurück zum Dialogfeld für die Veröffentlichung, und wählen Sie die Option **&lt;Aktualisieren&gt;** . Der neue Cluster ist im Dropdownfeld aufgeführt. Wählen Sie ihn aus.

Klicken Sie auf **Veröffentlichen**, und warten Sie, bis die Bereitstellung abgeschlossen ist.

Dies kann einige Minuten dauern. Nach Abschluss des Vorgangs kann es einige weitere Minuten dauern, bis die Anwendung vollständig verfügbar ist.

## <a name="test-the-website"></a>Testen der Website

Testen Sie Ihren Dienst in einem Webbrowser, nachdem er veröffentlicht wurde. 

Öffnen Sie zuerst das Azure-Portal, und suchen Sie nach Ihrem Service Fabric-Dienst.

Überprüfen Sie das Blatt „Übersicht“ der Dienstadresse. Verwenden Sie den Domänennamen aus der Eigenschaft _Endpunkt für Clientverbindungen_. Beispiel: `http://mysvcfab1.westus2.cloudapp.azure.com`.

![Blatt mit Service Fabric-Übersicht im Azure-Portal][overview]

Navigieren Sie zu dieser Adresse – dort wird die Antwort `HELLO WORLD` angezeigt.

## <a name="delete-the-cluster"></a>Löschen des Clusters

Denken Sie daran, alle Ressourcen zu löschen, die Sie für diese Schnellstartanleitung erstellt haben, da für diese Ressourcen Gebühren berechnet werden.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich eingehender über [ausführbare Gastanwendungsdateien](service-fabric-guest-executables-introduction.md).

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
