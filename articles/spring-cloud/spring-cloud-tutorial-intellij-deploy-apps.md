---
title: 'Tutorial: Bereitstellen von Azure Spring Cloud-Anwendungen mithilfe von IntelliJ'
description: Bereitstellen von Anwendungen in Azure Spring Cloud mithilfe von IntelliJ
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 91edbbda43365b17b8276572804ba788dbee9f13
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426738"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Bereitstellen von Azure Spring Cloud-Anwendungen mithilfe von IntelliJ

**Dieser Artikel gilt für:** ✔️ Java

Das IntelliJ-Plug-In für Azure Spring Cloud unterstützt die Anwendungsbereitstellung über IntelliJ IDEA.  

Bevor Sie dieses Beispiel ausführen, können Sie den [grundlegenden Schnellstart](spring-cloud-quickstart.md) ausprobieren.

## <a name="prerequisites"></a>Voraussetzungen
* [JDK 8 Azul Zulu](/java/azure/jdk/java-jdk-install?preserve-view=true&view=azure-java-stable)
* [Maven 3.5.0 oder höher](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA, Community/Ultimate Edition, Version 2020.1/2020.2](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Installieren des Plug-Ins
Sie können das Azure-Toolkit für IntelliJ IDEA 3.43.0 über die **Plug-Ins** -Benutzeroberfläche von IntelliJ hinzufügen.

1. Starten Sie IntelliJ.  Wenn Sie zuvor ein Projekt geöffnet hatten, schließen Sie das Projekt, um das Dialogfeld „Willkommen“ anzuzeigen. Wählen Sie über den Link unten rechts **Konfigurieren** aus, und klicken Sie dann auf **Plug-Ins** , um das Dialogfeld „Plug-In-Konfiguration“ zu öffnen, und wählen Sie dann **Install Plugins from disk** (Plug-Ins von Datenträger installieren) aus.

    ![Auswählen von „Configure“ (Konfigurieren)](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Suchen Sie nach dem Azure-Toolkit für IntelliJ.  Klicken Sie auf **Installieren**.

    ![Installieren des Plug-Ins](media/spring-cloud-intellij-howto/install-plugin.png)

1. Klicken Sie auf **IDE neu starten**.

## <a name="tutorial-procedures"></a>Prozeduren des Tutorials
Mit den folgenden Prozeduren wird eine Hallo Welt Anwendung mithilfe von IntelliJ IDEA bereitgestellt.

* Projekt „gs-spring-boot“ öffnen
* In Azure Spring Cloud bereitstellen
* Streamingprotokolle anzeigen

## <a name="open-gs-spring-boot-project"></a>Projekt „gs-spring-boot“ öffnen

1. Laden Sie das Quellrepository für dieses Tutorial herunter, und entpacken Sie es oder klonen Sie es mithilfe des Git-Befehls Git Clone https://github.com/spring-guides/gs-spring-boot.git 
1. in „gs-spring-boot\complete“.
1. Öffnen Sie das IntelliJ-Dialogfeld **Willkommens** , und wählen Sie **Projekt importieren** aus, um den Importassistenten zu öffnen.
1. Wählen Sie den Ordner `gs-spring-boot\complete` aus.

    ![Importieren des Projekts](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>In Azure Spring Cloud bereitstellen
Für die Bereitstellung in Azure müssen Sie sich bei Ihrem Azure-Konto anmelden und Ihr Abonnement auswählen.  Anmeldeinformationen finden Sie unter [Installation und Anmeldung](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Klicken Sie im Projektexplorer von IntelliJ mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Azure** -> **In Azure Spring Cloud bereitstellen** aus.

    ![Bereitstellen in Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Übernehmen Sie den Namen für die App im Feld **Name**. **Name** bezieht sich auf die Konfiguration, nicht auf den App-Namen. Benutzer müssen ihn in der Regel nicht ändern.
1. Übernehmen Sie den Bezeichner des Projekts für das **Artefakt**.
1. Wählen Sie **App:** aus, und klicken Sie dann auf **App erstellen…** .

    ![Bereitstellen in Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Geben Sie den **App-Namen** ein, und klicken Sie dann auf **OK**.

    ![In Azure bereitstellen OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Starten Sie die Bereitstellung, indem Sie auf die Schaltfläche **Ausführen** klicken. 

    ![Bereitstellen in Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. Das Plug-In führt den Befehl `mvn package` für das Projekt aus, erstellt dann die neue App und stellt die vom `package`-Befehl generierte JAR-Datei bereit.

1. Wenn die App-URL im Ausgabefenster nicht angezeigt wird, finden Sie sie im Azure-Portal. Navigieren Sie von Ihrer Ressourcengruppe zur Instanz von Azure Spring Cloud.  Klicken Sie dann auf **Apps**.  Die ausgeführte App ist aufgelistet.

    ![Test-URL abrufen](media/spring-cloud-intellij-howto/get-test-url.png)

1. Navigieren Sie im Browser zu der URL.

    ![Navigieren in Browser 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Streamingprotokolle anzeigen
Abrufen der Protokolle:
1. Wählen Sie **Azure-Explorer** und dann **Spring Cloud** aus.
1. Klicken Sie mit der rechten Maustaste auf die ausgeführte App.
1. Wählen Sie in der Dropdownliste **Streamingprotokolle** aus.

    ![Streamingprotokolle auswählen](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Wählen Sie die Instanz aus.

    ![Instanz auswählen](media/spring-cloud-intellij-howto/select-instance.png)

1. Das Streamingprotokoll wird im Ausgabefenster angezeigt.

    ![Ausgabe des Streamingprotokolls](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Nächste Schritte
* [Vorbereiten der Spring-Anwendung für Azure Spring Cloud](./spring-cloud-tutorial-prepare-app-deployment.md)
* [Weitere Informationen zum Azure Toolkit für IntelliJ](/azure/developer/java/toolkit-for-intellij/)
