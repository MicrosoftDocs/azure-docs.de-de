---
title: Azure Service Fabric-Plug-In für Eclipse | Microsoft-Dokumentation
description: Enthält eine Beschreibung der ersten Schritte mit dem Service Fabric-Plug-In für Eclipse.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: c33ecce5610dbef0dce13aa95f04ae4f0620603b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60950098"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Service Fabric-Plug-In für die Entwicklung von Eclipse-Java-Anwendungen
Eclipse ist eine der am häufigsten genutzten IDEs (Integrated Development Environments) für Java-Entwickler. In diesem Artikel wird beschrieben, wie Sie Ihre Eclipse-Entwicklungsumgebung für Azure Service Fabric einrichten. Hier erfahren Sie, wie Sie das Service Fabric-Plug-In installieren, eine Service Fabric-Anwendung erstellen und Ihre Service Fabric-Anwendung in einem lokalen Service Fabric-Cluster oder Service Fabric-Remotecluster in Eclipse bereitstellen. 

> [!NOTE]
> Das Eclipse-Plug-In wird unter Windows aktuell nicht unterstützt. 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Installieren oder Aktualisieren des Service Fabric-Plug-Ins in Eclipse
Sie können ein Service Fabric-Plug-In in Eclipse installieren. Das Plug-In kann dazu beitragen, den Prozess der Erstellung und Bereitstellung von Java-Diensten zu vereinfachen.

> [!IMPORTANT]
> Für das Service Fabric-Plug-In ist Eclipse Neon oder eine höhere Version erforderlich. Wie Sie Ihre Version von Eclipse überprüfen, erfahren Sie in der Anleitung im Anschluss an diesen Hinweis. Falls Sie eine ältere Version von Eclipse installiert haben, können Sie neuere Versionen von der [Eclipse-Website](https://www.eclipse.org) herunterladen. Es wird davon abgeraten, eine bereits vorhandene Installation von Eclipse durch eine neue Installation zu überschreiben. Entfernen Sie entweder die ältere Version, bevor Sie das Installationsprogramm ausführen, oder installieren Sie die neuere Version in einem anderen Verzeichnis. 
> 
> Unter Ubuntu sollten Sie die Installation direkt über die Eclipse-Website ausführen, anstatt ein Paketinstallationsprogramm (`apt` oder `apt-get`) zu verwenden. Dadurch wird sichergestellt, dass Sie die neueste Version von Eclipse erhalten. 

Installieren Sie Eclipse Neon oder eine höhere Version (erhältlich auf der [Eclipse-Website](https://www.eclipse.org)).  Installieren Sie außerdem mindestens Version 2.2.1 von Buildship. (Das Service Fabric-Plug-In ist mit älteren Versionen von Buildship nicht kompatibel.)
-   Die Versionen der installierten Komponenten können Sie in Eclipse unter **Hilfe** > **Über Eclipse** > **Installationsdetails** überprüfen.
-   Informationen zum Aktualisieren von Buildship finden Sie unter [Eclipse Buildship: Eclipse-Plug-Ins für Gradle][buildship-update].
-   Unter **Hilfe** > **Nach Updates suchen** können Sie nach Updates für Eclipse suchen und diese installieren.

Installieren Sie das Service Fabric-Plug-In. Navigieren Sie hierzu in Eclipse zu **Hilfe** > **Install New Software** (Neue Software installieren).
1. Geben Sie im Feld **Arbeiten Sie mit** die URL http:\//dl.microsoft.com/eclipse ein.
2. Klicken Sie auf **Hinzufügen**.

   ![Service Fabric-Plug-In für Eclipse][sf-eclipse-plugin-install]
3. Wählen Sie das Service Fabric-Plug-In aus, und klicken Sie auf **Weiter**.
4. Führen Sie die Schritte für die Installation aus, und akzeptieren Sie die Microsoft-Software-Lizenzbedingungen.
  
Falls Sie das Service Fabric-Plug-In bereits installiert haben, installieren Sie die aktuelle Version. 
1. Unter **Hilfe** > **Über Eclipse** > **Installationsdetails** können Sie prüfen, ob Updates verfügbar sind. 
2. Wählen Sie in der Liste mit den installierten Plug-Ins die Option „Service Fabric“, und klicken Sie auf **Aktualisieren**. Die verfügbaren Updates werden installiert.
3. Aktualisieren Sie nach der Aktualisierung des Service Fabric-Plug-Ins auch das Gradle-Projekt.  Klicken Sie mit der rechten Maustaste auf **build.gradle**, und klicken Sie auf **Aktualisieren**.

> [!NOTE]
> Wenn das Installieren oder Aktualisieren des Service Fabric-Plug-Ins lange dauert, kann dies an einer Eclipse-Einstellung liegen. In Eclipse werden Metadaten zu allen Änderungen von Updatewebsites gesammelt, die für Ihre Eclipse-Instanz registriert sind. Navigieren Sie zu **Available Software Sites** (Verfügbare Softwarestandorte), um das Vorhandensein eines Updates für das Service Fabric-Plug-In zu prüfen und das Update zu installieren. Deaktivieren Sie die Kontrollkästchen für alle Websites, mit Ausnahme des Kontrollkästchens, das auf den Speicherort des Service Fabric-Plug-Ins (http:\//dl.microsoft.com/eclipse/azure/servicefabric) verweist.

> [!NOTE]
>Sollte Eclipse auf Ihrem Mac nicht wie erwartet funktioniert oder die Ausführung als Administrator voraussetzen, navigieren Sie zum Ordner **ECLIPSE_INSTALLATION_PATH** und dort zum Unterordner **Eclipse.app/Contents/MacOS**. Führen Sie `./eclipse` aus, um Eclipse zu starten.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Erstellen einer Service Fabric-Anwendung in Eclipse

1.  Navigieren Sie in Eclipse zu **Datei** > **Neu** > **Other** (Andere). Wählen Sie die Option **Service Fabric-Projekt**, und klicken Sie dann auf **Weiter**.

    ![Service Fabric – Neues Projekt – Seite 1][create-application/p1]

2.  Geben Sie einen Namen für Ihr Projekt ein, und klicken Sie auf **Weiter**.

    ![Service Fabric – Neues Projekt – Seite 2][create-application/p2]

3.  Wählen Sie in der Liste mit den Vorlagen die Option **Dienstvorlage**. Wählen Sie den Typ der Dienstvorlage (Akteur, Zustandslos, Container oder Binäre Gastdatei), und klicken Sie auf **Weiter**.

    ![Service Fabric – Neues Projekt – Seite 3][create-application/p3]

4.  Geben Sie den Dienstnamen und die Dienstdetails ein, und klicken Sie auf **Fertig stellen**.

    ![Service Fabric – Neues Projekt – Seite 4][create-application/p4]

5. Klicken Sie beim Erstellen Ihres ersten Service Fabric-Projekts im Dialogfeld **Open Associated Perspective** (Zugeordnete Perspektive öffnen) auf **Ja**.

    ![Service Fabric – Neues Projekt – Seite 5][create-application/p5]

6.  Das neue Projekt sieht wie folgt aus:

    ![Service Fabric – Neues Projekt – Seite 6][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Erstellen einer Service Fabric-Anwendung in Eclipse

1.  Klicken Sie mit der rechten Maustaste auf Ihre neue Service Fabric-Anwendung, und wählen Sie dann die Option **Service Fabric**.

    ![Service Fabric-Kontextmenü][publish/RightClick]

2. Wählen Sie im Kontextmenü eine der folgenden Optionen aus:
    -   Klicken Sie auf **Build Application** (Anwendung erstellen), um die Anwendung ohne Bereinigung zu erstellen.
    -   Klicken Sie auf **Rebuild Application** (Anwendung erneut erstellen), um eine bereinigte Erstellung der Anwendung durchzuführen.
    -   Klicken Sie auf **Clean Application** (Anwendung bereinigen), um die Anwendung in Bezug auf die Buildartefakte zu bereinigen.
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Bereitstellen einer Service Fabric-Anwendung im lokalen Cluster mit Eclipse

Nachdem Sie Ihre Service Fabric-Anwendung erstellt haben, gehen Sie folgendermaßen vor, um sie im lokalen Cluster bereitzustellen.

1. Wenn Sie den lokalen Cluster nicht gestartet haben, befolgen Sie die Anweisungen in [Einrichten eines lokalen Clusters](./service-fabric-get-started-linux.md#set-up-a-local-cluster), um Ihren lokalen Cluster zu starten und sicherzustellen, dass er ausgeführt wird.
2. Klicken Sie mit der rechten Maustaste auf Ihre Service Fabric-Anwendung, und wählen Sie dann die Option **Service Fabric** aus.

    ![Service Fabric-Kontextmenü][publish/RightClick]

3.  Klicken Sie im Kontextmenü auf **Anwendung bereitstellen**.
4.  Sie können den Fortschritt des Bereitstellungsvorgangs im Konsolenfenster verfolgen.
5.  Um sicherzustellen, dass Ihre Anwendung ausgeführt wird, öffnen Sie den Service Fabric Explorer in Ihrem lokalen Cluster in einem Browserfenster [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Erweitern Sie den Knoten **Anwendungen**, und stellen Sie sicher, dass die Anwendung ausgeführt wird. 

Wie Sie Ihre Anwendung in Eclipse mit dem lokalen Cluster debuggen, lernen Sie unter [Debuggen eines Java-Diensts in Eclipse](./service-fabric-debugging-your-application-java.md).

Sie können Ihre Anwendung auch mit dem Befehl **Anwendung veröffentlichen** im lokalen Cluster bereitstellen:

1. Klicken Sie mit der rechten Maustaste auf Ihre Service Fabric-Anwendung, und wählen Sie dann die Option **Service Fabric** aus.
2. Klicken Sie im Kontextmenü auf **Anwendung veröffentlichen...**
3. Wählen Sie im Fenster **Anwendung veröffentlichen** als Zielprofil **PublishProfiles/Local.json** aus, und klicken Sie auf **Veröffentlichen**.

    ![Lokales Dialogfeld „Veröffentlichen“](./media/service-fabric-get-started-eclipse/localjson.png)

    Standardmäßig wird das Veröffentlichungsprofil „Local.json“ zur Veröffentlichung im lokalen Cluster eingerichtet. Weitere Informationen zu den Verbindungs- und Endpunktparametern in Veröffentlichungsprofilen finden Sie im nächsten Abschnitt.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Veröffentlichen Ihrer Service Fabric-Anwendung in Azure mit Eclipse

Um Ihre Anwendung in der Cloud zu veröffentlichen, führen Sie diese Schritte aus:

1. Um Ihre Anwendung in einem sicheren Cluster in der Cloud zu veröffentlichen, benötigen Sie ein X.509-Zertifikat für die Kommunikation mit Ihrem Cluster. In Test- und Entwicklungsumgebungen wird häufig das Clusterzertifikat verwendet. In Produktionsumgebungen sollte das Zertifikat ein Clientzertifikat sein, das sich vom Clusterzertifikat unterscheidet. Sie benötigen sowohl das Zertifikat als auch den privaten Schlüssel. Die Zertifikatdatei (und Schlüsseldatei) muss im PEM-Format vorliegen. Sie können eine PEM-Datei, die das Zertifikat und den privaten Schlüssel aus einer PFX-Datei enthält, mit dem folgenden openssl-Befehl erstellen:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Wenn die PFX-Datei nicht kennwortgeschützt ist, verwenden Sie `--passin pass:` für den letzten Parameter.

2. Öffnen Sie die **Cloud.json**-Datei im **PublishProfiles**-Verzeichnis. Sie müssen den Clusterendpunkt und die Sicherheitsanmeldeinformationen Ihrem Cluster gemäß konfigurieren.

   - Die `ConnectionIPOrURL`-Feld enthält die IP-Adresse oder URL Ihres Clusters. Beachten Sie, dass der Wert nicht das URL-Schema enthält (`https://`).
   - Standardmäßig sollte das `ConnectionPort`-Feld `19080` sein, es sei denn, Sie haben diesen Port explizit für Ihren Cluster geändert.
   - Das `ClientKey`-Feld sollte auf eine PEM- oder KEY-Datei im PEM-Format auf Ihrem lokalen Computer verweisen, die den privaten Schlüssel für Ihr Client- oder Clusterzertifikat enthält.
   - Das `ClientCert`-Feld sollte auf eine PEM- oder CRT-Datei im PEM-Format auf Ihrem lokalen Computer verweisen, die die Zertifikatdaten für Ihr Client- oder Clusterzertifikat enthält. 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Klicken Sie mit der rechten Maustaste auf Ihre Service Fabric-Anwendung, und wählen Sie dann die Option **Service Fabric** aus.
3. Klicken Sie im Kontextmenü auf **Anwendung veröffentlichen...**
3. Wählen Sie im Fenster **Anwendung veröffentlichen** als Zielprofil **PublishProfiles/Cloud.json** aus, und klicken Sie auf **Veröffentlichen**.

    ![Clouddialogfeld „Veröffentlichen“](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. Sie können den Fortschritt des Veröffentlichungsvorgangs im Konsolenfenster verfolgen.
5. Um sicherzustellen, dass Ihre Anwendung ausgeführt wird, öffnen Sie den Service Fabric Explorer in Ihrem Azure-Cluster in einem Browserfenster. Für das obige Beispiel wäre dies: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`. Erweitern Sie den Knoten **Anwendungen**, und stellen Sie sicher, dass die Anwendung ausgeführt wird. 


Wenn Ihre Anwendung Reliable Services-Dienste enthält, müssen Sie in sicheren Linux-Clustern auch ein Zertifikat konfigurieren, mit denen Ihre Dienste Service Fabric-Runtime-APIs aufrufen können. Weitere Informationen finden Sie unter [Konfigurieren einer Reliable Services-App zur Ausführung in Linux-Clustern](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Eine schnelle Einführung in die Bereitstellung einer in Java geschriebenen Service Fabric-Reliable Services-Anwendung in einem sicheren Linux-Cluster finden Sie unter [Schnellstart: Bereitstellen einer Java Reliable Services-Anwendung in Service Fabric](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Bereitstellen einer Service Fabric-Anwendung mit Eclipse-Ausführungskonfigurationen

Eine andere Möglichkeit zum Bereitstellen Ihrer Service Fabric-Anwendung ist die Verwendung von Eclipse-Ausführungskonfigurationen.

1. Navigieren Sie in Eclipse zu **Run** (Ausführen) > **Run Configurations** (Ausführungskonfigurationen).
2. Wählen Sie unter **Gradle Project** (Gradle-Projekt) die Ausführungskonfiguration **ServiceFabricDeployer** aus.
3. Stellen Sie im rechten Bereich auf der Registerkarte **Arguments** (Argumente) sicher, dass die Parameter **ip**, **port**, **clientCert** und **clientKey** für Ihre Bereitstellung richtig festgelegt sind. Standardmäßig werden die Parameter für die Bereitstellung im lokalen Cluster festgelegt, wie im folgenden Screenshot dargestellt. Zum Veröffentlichen Ihrer App in Azure können Sie die Parameter so ändern, dass sie die Endpunktdetails und Sicherheitsanmeldeinformationen für Ihren Azure-Cluster enthalten. Weitere Informationen finden Sie im vorherigen Abschnitt, [Veröffentlichen Ihrer Service Fabric-Anwendung in Azure mit Eclipse](#publish-your-service-fabric-application-to-azure-with-eclipse).

    ![Dialogfeld „Run Configurations“ (Ausführungskonfigurationen)](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Stellen Sie sicher, dass **Working Directory** (Arbeitsverzeichnis) auf die Anwendung verweist, die Sie bereitstellen möchten. Klicken Sie zum Ändern der Anwendung auf die Schaltfläche **Arbeitsbereich**, und wählen Sie die gewünschte Anwendung aus.
6. Klicken Sie auf **Übernehmen** und anschließend auf **Ausführen**.

Ihre Anwendung wird innerhalb weniger Augenblicke erstellt und bereitgestellt. Sie können den Bereitstellungsstatus in Service Fabric Explorer überwachen.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Hinzufügen eines Service Fabric-Diensts zur Service Fabric-Anwendung

Führen Sie die folgenden Schritte aus, um einer vorhandenen Service Fabric-Anwendung einen Service Fabric-Dienst hinzuzufügen:

1.  Klicken Sie mit der rechten Maustaste auf das Projekt, dem Sie einen Dienst hinzufügen möchten, und klicken Sie anschließend auf **Service Fabric**.

    ![Service Fabric – Dienst hinzufügen – Seite 1][add-service/p1]

2.  Klicken Sie auf **Add Service Fabric Service** (Service Fabric-Dienst hinzufügen), und führen Sie die Schritte zum Hinzufügen eines Diensts zum Projekt aus.
3.  Wählen Sie die Dienstvorlage aus, die Sie dem Projekt hinzufügen möchten, und klicken Sie auf **Weiter**.

    ![Service Fabric – Dienst hinzufügen – Seite 2][add-service/p2]

4.  Geben Sie den Dienstnamen (und bei Bedarf weitere Details) ein, und klicken Sie anschließend auf die Schaltfläche **Dienst hinzufügen**.  

    ![Service Fabric – Dienst hinzufügen – Seite 3][add-service/p3]

5.  Nachdem der Dienst hinzugefügt wurde, sieht die Gesamtstruktur des Projekts in etwa wie im folgenden Projekt aus:

    ![Service Fabric – Dienst hinzufügen – Seite 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Bearbeiten von Manifestversionen Ihrer Service Fabric-Java-Anwendung

Klicken Sie zum Bearbeiten von Manifestversionen mit der rechten Maustaste auf das Projekt, navigieren Sie zu **Service Fabric**, und wählen Sie in der Dropdownliste des Menüs die Option **Manifestversionen bearbeiten...** . Im Assistenten können Sie die Manifestversionen für das Anwendungsmanifest, das Dienstmanifest und die Versionen für die Pakete **Code**, **Config** und **Data** aktualisieren.

Wenn Sie die Option **Anwendung und Dienstversionen automatisch aktualisieren** aktivieren und dann eine Version aktualisieren, werden die Manifestversionen automatisch aktualisiert. Beispiel: Sie aktivieren zuerst das Kontrollkästchen und aktualisieren dann die Version von **Code** von 0.0.0 auf 0.0.1 und klicken auf **Fertig stellen**. Die Versionen des Dienstmanifests und des Anwendungsmanifests werden dann automatisch auf 0.0.1 aktualisiert.

## <a name="upgrade-your-service-fabric-java-application"></a>Aktualisieren der Service Fabric-Java-Anwendung

Angenommen, Sie haben für ein Upgradeszenario das Projekt **App1** erstellt, indem Sie das Service Fabric-Plug-In in Eclipse verwendet haben. Sie haben es bereitgestellt, indem Sie mit dem Plug-In eine Anwendung mit dem Namen **fabric:/App1Application** erstellt haben. Der Anwendungstyp lautet **App1ApplicationType**, und die Anwendungsversion ist 1.0. Nun möchten Sie die Anwendung aktualisieren, ohne die Verfügbarkeit zu unterbrechen.

Nehmen Sie zuerst Änderungen an Ihrer Anwendung vor, und erstellen Sie den geänderten Dienst anschließend neu. Aktualisieren Sie die Manifestdatei des geänderten Diensts (ServiceManifest.xml) mit den aktualisierten Versionen für den Dienst (und ggf. mit aktualisiertem Code, aktualisierter Konfiguration oder aktualisierten Daten). Passen Sie auch das Manifest der Anwendung (ApplicationManifest.xml) mit der aktualisierten Versionsnummer für die Anwendung und dem geänderten Dienst an.  

Wenn Sie mithilfe von Eclipse ein Upgrade für Ihre Anwendung durchführen möchten, können Sie ein dupliziertes Profil einer Ausführungskonfiguration erstellen. Verwenden Sie es anschließend, um die Anwendung wie gewünscht zu aktualisieren.

1.  Navigieren Sie zu **Ausführen** > **Run Configurations** (Ausführungskonfigurationen). Klicken Sie im linken Bereich auf den kleinen Pfeil links neben **Gradle Project** (Gradle-Projekt).
2.  Klicken Sie mit der rechten Maustaste auf **ServiceFabricDeployer**, und wählen Sie die Option **Duplizieren**. Geben Sie einen neuen Namen für diese Konfiguration ein, z.B. **ServiceFabricUpgrader**.
3.  Ändern Sie im rechten Bereich auf der Registerkarte **Argumente** den Wert **-Pconfig='deploy'** in **-Pconfig=upgrade**, und klicken Sie dann auf **Übernehmen**.

Mit diesem Prozess wird ein Profil für die Ausführungskonfiguration erstellt und gespeichert, das Sie jederzeit zum Aktualisieren Ihrer Anwendung verwenden können. Bei diesem Vorgang wird außerdem die aktuelle Version mit aktualisiertem Anwendungstyp aus der Anwendungsmanifestdatei abgerufen.

Das Upgrade der Anwendung dauert einige Minuten. Sie können die Anwendungsaktualisierung mit Service Fabric Explorer überwachen.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrieren alter Service Fabric-Java-Anwendungen für die Verwendung mit Maven
Vor Kurzem wurden Service Fabric-Java-Bibliotheken aus dem Service Fabric Java SDK in das Maven-Repository verschoben. Die neuen mit Eclipse erstellten Anwendungen generieren aktuelle Projekte (die mit Maven verwendet werden können). Sie können Ihre vorhandenen zustandslosen oder akteurbasierten Service Fabric-Java-Anwendungen, die zuvor das Service Fabric Java SDK verwendet haben, aktualisieren, sodass sie die Service Fabric-Java-Abhängigkeiten von Maven nutzen. Führen Sie die [hier](service-fabric-migrate-old-javaapp-to-use-maven.md) aufgeführten Schritte aus, um sicherzustellen, dass Ihre älteren Anwendung mit Maven verwendet werden können.

## <a name="next-steps"></a>Nächste Schritte

- Wie Sie in wenigen Schritten eine Java-Reliable Services-Anwendung erstellen und lokal sowie in Azure bereitstellen, erfahren Sie unter [Schnellstart: Bereitstellen einer Java Reliable Services-Anwendung in Service Fabric](./service-fabric-quickstart-java-reliable-services.md).
- Wie Sie eine Java-Anwendung auf Ihrem lokalen Cluster debuggen, lernen Sie unter [Debuggen eines Java-Diensts in Eclipse](./service-fabric-debugging-your-application-java.md).
- Wie Sie Service Fabric-Anwendungen überwachen und diagnostizieren, erfahren Sie unter [Überwachen und Diagnostizieren von Diensten in einer Entwicklungsumgebung auf einem lokalen Computer](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
