---
title: Bereitstellen einer Java-Anwendung mit Open Liberty/WebSphere Liberty in Azure Red Hat OpenShift 4-Clustern
description: Bereitstellen einer Java-Anwendung mit Open Liberty/WebSphere Liberty in Azure Red Hat OpenShift 4-Clustern.
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aro, openshift, red hat
ms.openlocfilehash: 0c17c911d1eefe646785314a26b6a9b1e964ca67
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493939"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Bereitstellen einer Java-Anwendung mit Open Liberty/WebSphere Liberty in Azure Red Hat OpenShift 4-Clustern

In dieser Anleitung wird veranschaulicht, wie Sie Ihre Java-, Java EE-, [Jakarta EE](https://jakarta.ee/)- oder [MicroProfile](https://microprofile.io/)-Anwendung auf der Open Liberty/WebSphere Liberty-Runtime ausführen und dann die Containeranwendung mithilfe des Open Liberty Operator in ein Azure Red Hat OpenShift 4-Cluster (ARO) bereitstellen. Dieser Artikel führt Sie durch die Vorbereitung einer Liberty-Anwendung, das Erstellen des Docker-Images der Anwendung und das Ausführen der Containeranwendung auf einem ARO 4-Cluster.  Weitere Informationen zu Open Liberty finden Sie auf der [Open Liberty-Projektseite](https://openliberty.io/). Weitere Informationen zu IBM WebSphere Liberty finden Sie auf der [WebSphere Liberty-Produktseite](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

Erfüllen Sie die folgenden Voraussetzungen aus, um dieses Handbuch erfolgreich durchzuarbeiten.

> [!NOTE]
> Für Azure Red Hat OpenShift sind mindestens 40 Kerne erforderlich, um einen OpenShift-Cluster zu erstellen und auszuführen. Das standardmäßige Azure-Ressourcenkontingent für ein neues Azure-Abonnement erfüllt diese Anforderung nicht. Weitere Informationen zum Anfordern einer Erhöhung des Ressourcenlimits finden Sie unter [Standardkontingent: Erhöhen der Grenzwerte nach VM-Serie](../azure-portal/supportability/per-vm-quota-requests.md) beschrieben. Beachten Sie, dass Ihr kostenloses Testabonnement nicht für eine Erhöhung des Kontingents qualifiziert ist. Bevor Sie eine Kontingenterhöhung anfordern, [führen Sie zuerst ein Upgrade auf ein Abonnement mit nutzungsbasierter Bezahlung durch](../cost-management-billing/manage/upgrade-azure-subscription.md).

1. Bereiten Sie einen lokalen Computer mit installiertem UNIX-ähnlichem Betriebssystem vor (z. B. Ubuntu, macOS).
1. Installieren Sie eine Java SE-Implementierung (z. B. [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Installieren Sie [Maven](https://maven.apache.org/download.cgi) 3.5.0 oder höher.
1. Installieren Sie [Docker](https://docs.docker.com/get-docker/) für Ihr Betriebssystem.
1. Installieren Sie [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) 2.0.75 oder höher.
1. Überprüfen und installieren Sie [`envsubst`](https://command-not-found.com/envsubst), wenn es nicht im Betriebssystem vorinstalliert ist.
1. Klonen Sie den Code für dieses Beispiel auf Ihrem lokalen System. Das Beispiel befindet sich auf [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro).
1. Führen Sie die Schritte in [Tutorial: Erstellen eines Azure Red Hat OpenShift 4-Clusters](./tutorial-create-cluster.md) aus.

   Der Schritt „Abrufen eines Red Hat-Pullgeheimnisses“ wird als optional bezeichnet, **ist jedoch für diesen Artikel erforderlich**.  Das Pullgeheimnis ermöglicht Ihrem Azure Red Hat OpenShift-Cluster, den Open Liberty Operator zu finden.

   Wenn Sie planen, arbeitsspeicherintensive Anwendungen im Cluster auszuführen, geben Sie die richtige Größe des virtuellen Computers für die Workerknoten mit dem `--worker-vm-size`-Parameter an. Beispielsweise ist `Standard_E4s_v3` die minimale Größe des virtuellen Computers, um den Elasticsearch Operator in einem Cluster zu installieren. Weitere Informationen finden Sie unter

   * [Erstellen eines Clusters mit Azure CLI](/cli/azure/aro?preserve-view=true&view=azure-cli-latest#az-aro-create)
   * [Unterstützte VM-Größen für Arbeitsspeicheroptimierung](./support-policies-v4.md#memory-optimized)
   * [Voraussetzungen für die Installation des Elasticsearch Operator](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Stellen Sie eine Verbindung mit dem Cluster her, indem Sie die Schritte in [Tutorial: Herstellen einer Verbindung mit einem Azure Red Hat OpenShift 4-Cluster](./tutorial-connect-cluster.md) ausführen.
   * Stellen Sie sicher, dass Sie die Schritte unter „Installieren der OpenShift-CLI“ ausführen, da der `oc`-Befehl weiter unten in diesem Artikel verwendet wird.
   * Notieren Sie sich die URL der Clusterkonsole, die wie `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`aussieht.
   * Notieren Sie sich die `kubeadmin`-Anmeldeinformationen.

1. Vergewissern Sie sich, dass Sie sich mit dem Token für Benutzer `kubeadmin` bei der OpenShift-CLI anmelden können.

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Aktivieren der integrierten Containerregistrierung für OpenShift

Mit den Schritten in diesem Tutorial erstellen Sie ein Docker-Image, das in eine Containerregistrierung gepusht werden muss, die für OpenShift zugänglich ist. Die einfachste Option ist die Verwendung der von OpenShift bereitgestellten integrierten Registrierung. Um die integrierte Containerregistrierung zu aktivieren, führen Sie die Schritte in [Konfigurieren der integrierten Containerregistrierung für Azure Red Hat OpenShift 4](built-in-container-registry.md) aus. In diesem Artikel werden drei Elemente dieser Schritte verwendet.

* Der Benutzername und das Kennwort des Azure AD-Benutzers für die Anmeldung bei der OpenShift-Webkonsole.
* Die Ausgabe von `oc whoami` nach dem Ausführen der Schritte zum Anmelden bei der OpenShift-CLI.  Dieser Wert wird für die Erörterung als **aad-user** bezeichnet.
* Die URL der Containerregistrierung.

Notieren Sie sich diese Elemente, wenn Sie die Schritte zum Aktivieren der integrierten Containerregistrierung ausführen.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Erstellen eines OpenShift-Namespace für die Java-App

1. Melden Sie sich mit den `kubeadmin`-Anmeldeinformationen über Ihren Browser bei der OpenShift-Webkonsole an.
2. Navigieren Sie zu **Verwaltung** > **Namespaces** > **Namespace erstellen**.
3. Tragen Sie `open-liberty-demo` für **Name** ein, und wählen Sie **Erstellen** aus, wie im Folgenden gezeigt.

   ![create namespace](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Erstellen eines Administrators für das Demoprojekt

Neben der Imageverwaltung werden dem **aad-user** auch administrative Berechtigungen für die Verwaltung von Ressourcen im Demoprojekt des ARO 4-Clusters zugewiesen.  Melden Sie sich bei der OpenShift-CLI an, und weisen Sie dem **aad-user** die erforderlichen Berechtigungen zu, indem Sie die folgenden Schritte ausführen.

1. Melden Sie sich mit den `kubeadmin`-Anmeldeinformationen über Ihren Browser bei der OpenShift-Webkonsole an.
1. Erweitern Sie rechts oben in der Webkonsole das Kontextmenü des angemeldeten Benutzers, und wählen Sie dann **Anmeldebefehl kopieren** aus.
1. Melden Sie sich bei Bedarf als derselbe Benutzer bei einem neuen Registerkartenfenster an.
1. Wählen Sie **Token anzeigen** aus.
1. Kopieren Sie den unter **Mit diesem Token anmelden** aufgeführten Wert in die Zwischenablage, und führen Sie ihn wie hier gezeigt in einer Shell aus.
1. Führen Sie die folgenden Befehle aus, um dem **aad-user** im Namespace `admin` die `open-liberty-demo`-Rolle zuzuweisen.

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Installieren des Open Liberty OpenShift Operator

Installieren Sie nach dem Erstellen des Clusters und dem Herstellen einer Verbindung den Open Liberty Operator.  Die Hauptstartseite für den Open Liberty Operator befindet sich auf [GitHub](https://github.com/OpenLiberty/open-liberty-operator).

1. Melden Sie sich mit den `kubeadmin`-Anmeldeinformationen über Ihren Browser bei der OpenShift-Webkonsole an.
2. Navigieren Sie zu **Operatoren** > **OperatorHub**, und suchen Sie **Liberty Operator öffnen**.
3. Wählen Sie in den Suchergebnissen **Liberty Operator öffnen** aus.
4. Wählen Sie **Installieren** aus.
5. Aktivieren Sie im Popupmenü **Operator-Abonnement erstellen** die Option **Alle Namespaces im Cluster (Standard)** für **Installationsmodus**, **Beta** für **Updatekanal** und **Automatisch** für **Genehmigungsstrategie**:

   ![create operator subscription for Open Liberty Operator](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Wählen Sie **Abonnieren** aus, und warten Sie ein oder zwei Minuten, bis der Open Liberty Operator angezeigt wird.
7. Beobachten Sie, ob der Open Liberty Operator den Status „Erfolgreich“ hat.  Wenn nicht, diagnostizieren und lösen Sie das Problem, bevor Sie fortfahren.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="„Installierte Operatoren“ zeigt an, dass Open Liberty installiert ist.":::

## <a name="prepare-the-liberty-application"></a>Vorbereiten der Liberty-Anwendung

Wir verwenden in diesem Handbuch eine Java EE 8-Anwendung als Beispiel. Da Open Liberty ein mit [Java EE 8 Full Profile](https://javaee.github.io/javaee-spec/javadocs/) kompatibler Server ist, kann die Anwendung problemlos ausgeführt werden.  Open Liberty ist auch [mit Jakarta EE 8 Full Profile kompatibel](https://jakarta.ee/specifications/platform/8/apidocs/).

### <a name="run-the-application-on-open-liberty"></a>Ausführen der Anwendung auf Open Liberty

Um die Anwendung auf Open Liberty auszuführen, müssen Sie eine Open Liberty-Serverkonfigurationsdatei erstellen, damit das [Liberty Maven-Plug-In](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) die Anwendung für die Bereitstellung verpacken kann. Das Liberty Maven-Plug-In ist nicht erforderlich, um die Anwendung in OpenShift bereitzustellen.  Wir verwenden es jedoch in diesem Beispiel mit dem Entwicklermodus (dev) von Open Liberty.  Im Entwicklermodus können Sie die Anwendung problemlos lokal ausführen. Führen Sie die folgenden Schritte auf dem lokalen Computer aus.

1. Kopieren Sie `2-simple/src/main/liberty/config/server.xml` nach `1-start/src/main/liberty/config`, wobei die vorhandene Datei der Länge 0 (null) überschrieben wird. Diese `server.xml` konfiguriert den Open Liberty-Server mit den Java EE-Features.
1. Kopieren Sie `2-simple/pom.xml` nach `1-start/pom.xml`.  In diesem Schritt wird das `liberty-maven-plugin` POM hinzugefügt.
1. Wechseln Sie in das Verzeichnis `1-start` des lokalen Klons.
1. Führen Sie `mvn clean package` in einer Konsole aus, um ein WAR-Paket `javaee-cafe.war` im Verzeichnis `./target` zu generieren.
1. Führen Sie `mvn liberty:dev` aus, um Open Liberty im Entwicklungsmodus zu starten.
1. Warten Sie, bis der Server gestartet wird. Die Konsolenausgabe sollte mit der folgenden Meldung enden:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Öffnen Sie `http://localhost:9080/` in Ihrem Browser, um die Startseite der Anwendung aufzurufen. Die Anwendung sieht etwa wie folgt aus:

   ![JavaEE Cafe Web UI](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Drücken Sie **STRG+C**, um die Anwendung und den Open Liberty-Server anzuhalten.

Das Verzeichnis `2-simple` des lokalen Klons zeigt das Maven-Projekt mit den obigen Änderungen, die bereits angewendet wurden.

## <a name="prepare-the-application-image"></a>Vorbereiten des Anwendungsimages

Zum Bereitstellen und Ausführen Ihrer Liberty-Anwendung in einem ARO 4-Cluster müssen Sie Ihre Anwendung mit [Open Liberty-Containerimages](https://github.com/OpenLiberty/ci.docker) oder [WebSphere Liberty-Containerimages](https://github.com/WASdev/ci.docker) als Docker-Image containerisieren.

### <a name="build-application-image"></a>Erstellen des Anwendungsimages

Führen Sie die folgenden Schritte durch, um das Anwendungsimage zu erstellen:

1. Wechseln Sie in das Verzeichnis `2-simple` des lokalen Klons.
2. Führen Sie `mvn clean package` zum Verpacken der Anwendung aus.
3. Führen Sie zum Erstellen des Anwendungsimages einen der folgenden Befehle aus.
   * Erstellen mit Open Liberty-Basisimage:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Erstellen mit WebSphere Liberty-Basisimage:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Lokales Ausführen der Anwendung mit Docker

Führen Sie die Containeranwendung vor dem Bereitstellen in einem Remotecluster mit Ihrer lokalen Docker-Version aus, um zu überprüfen, ob es funktioniert:

1. Führen Sie `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` in Ihrer Konsole aus.
2. Warten Sie, bis der Liberty-Server startet und die Anwendung erfolgreich bereitgestellt wird.
3. Öffnen Sie `http://localhost:9080/` in Ihrem Browser, um die Startseite der Anwendung aufzurufen.
4. Drücken Sie **STRG+C**, um die Anwendung und den Liberty-Server anzuhalten.

### <a name="push-the-image-to-the-container-image-registry"></a>Pushen des Images zur Containerregistrierung

Wenn Sie mit dem Status der Anwendung zufrieden sind, können Sie sie mithilfe der unten stehenden Anweisungen zur integrierten Containerimageregistrierung pushen.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Melden Sie sich bei der OpenShift-CLI als Azure AD-Benutzer an.

1. Melden Sie sich mit den Anmeldeinformationen eines Azure AD-Benutzers über Ihren Browser bei der OpenShift-Webkonsole an.

   1. Verwenden Sie eine InPrivate, Incognito- oder andere entsprechende Browserfensterfunktion, um sich bei der Konsole anzumelden.
   1. Wählen Sie **openid** aus.

   > [!NOTE]
   > Notieren Sie sich den Benutzernamen und das Kennwort, die Sie hier zum Anmelden verwenden. Dieser Benutzername und dieses Kennwort fungieren in diesem und anderen Artikeln als Administrator für andere Aktionen.
1. Melden Sie sich mit der OpenShift-CLI an, indem Sie die folgenden Schritte ausführen.  Für die Diskussion wird dieser Prozess als `oc login` bezeichnet.
   1. Erweitern Sie rechts oben in der Webkonsole das Kontextmenü des angemeldeten Benutzers, und wählen Sie dann **Anmeldebefehl kopieren** aus.
   1. Melden Sie sich bei Bedarf als derselbe Benutzer bei einem neuen Registerkartenfenster an.
   1. Wählen Sie **Token anzeigen** aus.
   1. Kopieren Sie den unter **Mit diesem Token anmelden** aufgeführten Wert in die Zwischenablage, und führen Sie ihn wie hier gezeigt in einer Shell aus.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Pushübertragung des Containerimages in die Containerregistrierung für OpenShift

Führen Sie diese Befehle aus, um das Image per Push in die Containerregistrierung für OpenShift zu übertragen.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

Die erfolgreiche Ausgabe ähnelt der folgenden.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Übertragen Sie das Image mit dem folgenden Befehl per Push an die integrierte Containerregistrierung.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Bereitstellen der Anwendung auf dem ARO 4-Cluster

Nun können Sie die Liberty-Beispielanwendung für den Azure Red Hat OpenShift 4-Cluster bereitstellen, den Sie zuvor erstellt haben, als Sie die Voraussetzungen durchgearbeitet haben.

### <a name="deploy-the-application-from-the-web-console"></a>Bereitstellen der Anwendung über die Webkonsole

Da wir den Open Liberty Operator verwenden, um Liberty-Anwendungen zu verwalten, müssen wir eine Instanz seiner *benutzerdefinierten Ressourcendefinition* vom Typ OpenLibertyApplication erstellen. Der Operator kümmert sich dann um alle Aspekte der Verwaltung der für die Bereitstellung erforderlichen OpenShift-Ressourcen.

1. Melden Sie sich mit den Anmeldeinformationen des Azure AD-Benutzers über Ihren Browser bei der OpenShift-Webkonsole an.
1. Erweitern Sie **Start**, und wählen Sie **Projekte** > **open-liberty-demo** aus.
1. Navigieren Sie zu **Operatoren** > **Installierte Operatoren**.
1. Wählen Sie in der Mitte der Seite **Open Liberty Operator** aus.
1. Wählen Sie in der Mitte der Seite **Open Liberty-Anwendung** aus.  Die Navigation von Elementen in der Benutzeroberfläche spiegelt die tatsächliche Eigenständigkeitshierarchie der verwendeten Technologien wider.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO-Java-Eigenständigkeit](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Wählen Sie **OpenLibertyApplication erstellen** aus.
1. Ersetzen Sie die generierte YAML-Datei durch Ihre eigene, die sich unter `<path-to-repo>/2-simple/openlibertyapplication.yaml` befindet.
1. Wählen Sie **Erstellen** aus. Sie kehren zur Liste der OpenLibertyApplications zurück.
1. Wählen Sie **javaee-cafe-simple** aus.
1. Wählen Sie in der Mitte der Seite **Ressourcen** aus.
1. Wählen Sie in der Tabelle den Link für **javaee-cafe-simple** mit der **Art** der **Route** aus.
1. Wählen Sie auf der Seite, die geöffnet wird, den Link unter **Speicherort** aus.

Die Startseite der Anwendung wird im Browser geöffnet.

### <a name="delete-the-application-from-the-web-console"></a>Löschen der Anwendung über die Webkonsole

Wenn Sie die Anwendung nicht mehr benötigen, führen Sie die folgenden Schritte aus, um die Anwendung aus OpenShift zu löschen.

1. Erweitern Sie im linken Navigationsbereich den Eintrag für **Operatoren**.
1. Wählen Sie **Installierte Operatoren** aus.
1. Wählen Sie **Open Liberty Operator** aus.
1. Wählen Sie in der Mitte der Seite **Open Liberty-Anwendung** aus.
1. Wählen Sie die vertikalen Auslassungspunkte (drei vertikale Punkte) und dann **OpenLiberty-Anwendung löschen** aus.

### <a name="deploy-the-application-from-cli"></a>Bereitstellen der Anwendung über die CLI

Anstatt die GUI der Webkonsole zu verwenden, können Sie die Anwendung über die CLI bereitstellen. Wenn Sie dies noch nicht getan haben, laden Sie das `oc`-Befehlszeilentool herunter, und installieren Sie es gemäß der Red Hat-Dokumentation [Getting Started with the CLI](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html). (Erste Schritte mit der C).

1. Melden Sie sich mit den Anmeldeinformationen des Azure AD-Benutzers über Ihren Browser bei der OpenShift-Webkonsole an.
2. Melden Sie sich bei der OpenShift-CLI mit dem Token für den Azure AD-Benutzer an.
3. Wechseln Sie in das Verzeichnis `2-simple` des lokalen Klons, und führen Sie die folgenden Befehle aus, um Ihre Liberty-Anwendung im ARO 4-Cluster bereitzustellen.  Die Befehlsausgabe wird auch inline angezeigt.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. Überprüfen Sie, ob `1/1` unter der Spalte `READY` angezeigt wird, bevor Sie fortfahren.  Wenn nicht, untersuchen und lösen Sie das Problem, bevor Sie fortfahren.
5. Ermitteln Sie mit dem Befehl `oc get route` den Host der Route zur Anwendung, wie hier gezeigt.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Sobald die Liberty-Anwendung ausgeführt wird, öffnen Sie die Ausgabe von **Route Host** in Ihrem Browser, um die Startseite der Anwendung aufzurufen.

### <a name="delete-the-application-from-cli"></a>Löschen der Anwendung über die CLI

Löschen Sie die Anwendung über die CLI, indem Sie diesen Befehl ausführen.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie den ARO-Cluster, indem Sie die Schritte im [-Tutorial ausführen: Löschen eines Azure Red Hat OpenShift 4-Clusters](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie Folgendes gelernt:
> [!div class="checklist"]
>
> * Vorbereiten der Liberty-Anwendung
> * Erstellen des Anwendungsimages
> * Ausführen der Containeranwendung auf einem ARO 4-Cluster mit GUI und CLI

Weitere Informationen finden Sie über die in diesem Handbuch verwendeten Verweise:

* [Open Liberty](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Open Liberty-Serverkonfiguration](https://openliberty.io/docs/ref/config/)
* [Liberty Maven-Plug-In](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Open Liberty-Containerimages](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty-Containerimages](https://github.com/WASdev/ci.docker)