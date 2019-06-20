---
title: Einrichten Ihrer Entwicklungsumgebung unter Linux | Microsoft Docs
description: Installieren Sie die Laufzeit und das SDK, und erstellen Sie einen lokalen Entwicklungscluster unter Linux. Nach Abschluss des Setups können Sie mit der Erstellung von Anwendungen beginnen.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: a063461d9da66d57a7bdc3311ae80dec7f2c98f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65470230"
---
# <a name="prepare-your-development-environment-on-linux"></a>Vorbereiten Ihrer Entwicklungsumgebung unter Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Zur Bereitstellung und Ausführung von [Azure Service Fabric-Anwendungen](service-fabric-application-model.md) auf Ihrem Linux-Entwicklungscomputer müssen Sie die Laufzeit und das allgemeine SDK installieren. Darüber hinaus können Sie auch optionale SDKs für die Java- und .NET Core-Entwicklung installieren. 

Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie eine native Installation unter Linux durchführen oder das Service Fabric-One-Box-Containerimage (`microsoft/service-fabric-onebox`) verwenden.

Die Service Fabric-Laufzeit und das SDK können nicht unter dem Windows-Subsystem für Linux installiert werden. Sie können Service Fabric-Entitäten, die an anderen Orten in der Cloud oder lokal gehostet werden, mit der Azure Service Fabric-Befehlszeilenschnittstelle (Command-Line Interface, CLI) verwalten. Diese wird unterstützt. Informationen zum Installieren der Befehlszeilenschnittstelle finden Sie unter [Azure Service Fabric CLI](./service-fabric-cli.md).


## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Betriebssystemversionen werden bei der Entwicklung unterstützt:

* Ubuntu 16.04 (`Xenial Xerus`)

    Stellen Sie sicher, dass das Paket `apt-transport-https` installiert ist.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (Unterstützung der Service Fabric-Vorschauversion)


## <a name="installation-methods"></a>Installationsmethoden

### <a name="script-installation-ubuntu"></a>Skriptinstallation (Ubuntu)

Ein Skript soll Ihnen als Hilfe beim Installieren der Service Fabric-Runtime und des allgemeinen Service Fabric SDK zusammen mit der **sfctl**-CLI dienen. Führen Sie die Schritte für die manuelle Installation im nächsten Abschnitt aus. Sie sehen die installierten Komponenten und die zugehörigen Lizenzen. Für die Ausführung des Skripts wird vorausgesetzt, dass Sie den Lizenzen für die gesamte zu installierende Software zustimmen.

Nach der erfolgreichen Ausführung des Skripts können Sie direkt zu [Einrichten eines lokalen Clusters](#set-up-a-local-cluster) springen.

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="manual-installation"></a>Manuelle Installation
Die Schritte zum manuellen Installieren der Service Fabric-Runtime und des allgemeinen SDK finden Sie im restlichen Teil dieser Anleitung.

## <a name="update-your-apt-sources-or-yum-repositories"></a>Aktualisieren Ihrer APT-Quellen oder Yum-Repositorys
Um das SDK und das dazugehörige Runtimepaket über das Befehlszeilenprogramm „apt-get“ installieren zu können, müssen Sie zunächst Ihre APT-Datenquellen (Advanced Packaging Tool) aktualisieren.

### <a name="ubuntu"></a>Ubuntu

1. Öffnen Sie ein Terminal.
2. Fügen Sie der Quellenliste das Service Fabric-Repository hinzu.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Fügen Sie das `dotnet`-Repository Ihrer Quellenliste hinzu.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

4. Fügen Sie Ihrem APT-Schlüsselbund den neuen GnuPG- bzw. GPG-Schlüssel (Gnu Privacy Guard) hinzu.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Fügen Sie Ihrem APT-Schlüsselbund den offiziellen Docker-GPG-Schlüssel hinzu.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Richten Sie das Docker-Repository ein.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Fügen Sie Ihrem APT-Schlüsselbund den Azul JDK-Schlüssel hinzu, und richten Sie sein Repository ein.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0x219BD9C9
    sudo apt-add-repository 'deb http://repos.azulsystems.com/ubuntu stable main'
    ```

8. Aktualisieren Sie Ihre Paketlisten auf der Grundlage der neu hinzugefügten Repositorys.

    ```bash
    sudo apt-get update
    ```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Unterstützung der Service Fabric-Vorschauversion)

1. Öffnen Sie ein Terminal.
2. Laden Sie Extra Packages for Enterprise Linux (EPEL) herunter, und installieren Sie es.

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Fügen Sie Ihrem System das EfficiOS-RHEL7-Paketrepository hinzu.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importieren Sie den EfficiOS-Paketsignaturschlüssel in den lokalen GPG-Schlüsselbund.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Fügen Sie Ihrem System das Microsoft-RHEL-Repository hinzu.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

6. Installieren Sie das .NET SDK.

    ```bash
    yum install rh-dotnet20 -y
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>Installieren und Einrichten des Service Fabric SDK für einen lokalen Cluster

Nach der Aktualisierung Ihrer Quellen können Sie das SDK installieren. Installieren Sie das Service Fabric SDK-Paket, bestätigen Sie die Installation, und stimmen Sie dem Lizenzvertrag zu.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   Mit den folgenden Befehlen wird das Akzeptieren der Lizenz für Service Fabric-Pakete automatisiert:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Unterstützung der Service Fabric-Vorschauversion)

```bash
sudo yum install servicefabricsdkcommon
```

Die Service Fabric-Runtime aus der SDK-Installation enthält die Pakete in der folgenden Tabelle: 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | AzulJDK 1.8 | Implizit von npm | neueste |
RHEL | - | OpenJDK 1.8 | Implizit von npm | neueste |

## <a name="set-up-a-local-cluster"></a>Einrichten eines lokalen Clusters
Starten Sie nach Abschluss der Installation einen lokalen Cluster.

1. Führen Sie das Clustersetupskript aus.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Navigieren Sie in einem Webbrowser zu [Service Fabric Explorer](http://localhost:19080/Explorer) (`http://localhost:19080/Explorer`). Beim Starten des Clusters wird das Dashboard von Service Fabric Explorer angezeigt. Die Clustereinrichtung kann einige Minuten dauern. Sollte sich die URL in Ihrem Browser nicht öffnen lassen oder das System in Service Fabric Explorer nicht als bereit angezeigt werden, versuchen Sie es nach einigen Minuten noch einmal.

    ![Service Fabric Explorer unter Linux][sfx-linux]

    Nun können Sie vorgefertigte Service Fabric-Anwendungspakete oder neue, auf Gastcontainern oder ausführbaren Gastdateien basierende Anwendungspakete bereitstellen. Wenn Sie neue Dienste mit dem Java oder .NET Core SDK erstellen möchten, führen Sie die optionalen Einrichtungsschritte aus den nächsten Abschnitten aus.


> [!NOTE]
> Eigenständige Cluster werden unter Linux nicht unterstützt.


> [!TIP]
> Falls Sie über einen SSD-Datenträger verfügen, wird empfohlen, einen SSD-Ordnerpfad zu übergeben, indem Sie `--clusterdataroot` mit „devclustersetup.sh“ verwenden, um eine höhere Leistung zu erzielen.

## <a name="set-up-the-service-fabric-cli"></a>Einrichten der Service Fabric-Befehlszeilenschnittstelle

Die [Service Fabric CLI](service-fabric-cli.md) enthält Befehle für die Interaktion mit Service Fabric-Entitäten, etwa Clustern und Anwendungen. Befolgen Sie die Anweisungen unter [Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md), um die CLI zu installieren.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Einrichten von Yeoman-Generatoren für Container und ausführbare Gastdateien
Die Gerüstbautools von Service Fabric unterstützen Sie beim Erstellen von Service Fabric-Anwendungen über das Terminal mithilfe von Yeoman-Vorlagengeneratoren. Führen Sie die folgenden Schritte aus, um die Service Fabric-Yeoman-Vorlagengeneratoren einzurichten:

1. Installieren Sie Node.js und npm auf dem Computer.

    ```bash
    sudo apt-add-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Installieren Sie den [Yeoman](https://yeoman.io/)-Vorlagengenerator über npm auf dem Computer:

    ```bash
    sudo npm install -g yo
    ```
3. Installieren Sie den Service Fabric-Yeo-Containergenerator und den Generator für die ausführbare Gastdatei über npm:

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

Erstellen Sie nach der Installation der Generatoren ausführbare Gastdateien oder Containerdienste, indem Sie `yo azuresfguest` bzw. `yo azuresfcontainer` ausführen.

## <a name="set-up-net-core-20-development"></a>Einrichten der .NET Core 2.0-Entwicklung

Installieren Sie das [.NET Core 2.0 SDK für Ubuntu](https://www.microsoft.com/net/core#linuxubuntu), um mit dem [Erstellen von Service Fabric-C#-Anwendungen](service-fabric-create-your-first-linux-application-with-csharp.md) zu beginnen. Pakete für Service Fabric-Anwendungen mit .NET Core 2.0 werden auf NuGet.org gehostet (derzeit in der Vorschauphase).

## <a name="set-up-java-development"></a>Einrichten der Java-Entwicklung

Installieren Sie Gradle zum Ausführen von Buildaufgaben, um Service Fabric-Dienste mit Java zu erstellen. Führen Sie den folgenden Befehl aus, um Gradle zu installieren. Die Service Fabric-Java-Bibliotheken werden aus Maven abgerufen.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (Unterstützung der Service Fabric-Vorschauversion)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

Sie müssen auch den Service Fabric-Yeo-Generator für ausführbare Java-Dateien installieren. Stellen Sie sicher, dass [Yeoman installiert ist](#set-up-yeoman-generators-for-containers-and-guest-executables), und führen Sie dann den folgenden Befehl aus:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Installieren des Eclipse-Plug-Ins (optional)

Das Eclipse-Plug-In für Service Fabric können Sie über die Eclipse-IDE für Java-Entwickler oder für Java EE-Entwickler installieren. Sie können Eclipse verwenden, um zusätzlich zu Service Fabric-Java-Anwendungen Anwendungen mit ausführbarer Gastanwendungsdatei und Containeranwendungen für Service Fabric zu erstellen.

> [!IMPORTANT]
> Für das Service Fabric-Plug-In ist Eclipse Neon oder eine höhere Version erforderlich. Wie Sie Ihre Version von Eclipse überprüfen, erfahren Sie in der Anleitung im Anschluss an diesen Hinweis. Falls Sie eine ältere Version von Eclipse installiert haben, können Sie neuere Versionen von der [Eclipse-Website](https://www.eclipse.org) herunterladen. Es wird davon abgeraten, eine bereits vorhandene Installation von Eclipse durch eine neue Installation zu überschreiben. Entfernen Sie entweder die ältere Version, bevor Sie das Installationsprogramm ausführen, oder installieren Sie die neuere Version in einem anderen Verzeichnis.
> 
> Unter Ubuntu sollten Sie die Installation direkt über die Eclipse-Website ausführen, anstatt ein Paketinstallationsprogramm (`apt` oder `apt-get`) zu verwenden. Dadurch wird sichergestellt, dass Sie die neueste Version von Eclipse erhalten. Die Eclipse-IDE kann für Java-Entwickler oder für Java EE-Entwickler installiert werden.

1. Vergewissern Sie sich in Eclipse, dass bei Ihnen mindestens Eclipse Neon und mindestens die Buildship-Version 2.2.1 installiert sind. Ermitteln Sie die Version der installierten Komponenten unter **Hilfe** > **Über Eclipse** > **Installationsdetails**. Anweisungen zum Aktualisieren von Buildship finden Sie unter [Eclipse Buildship: Eclipse-Plug-Ins für Gradle][buildship-update].

2. Wählen Sie zum Installieren des Service Fabric-Plug-Ins **Hilfe** > **Neue Software installieren...** aus.

3. Geben Sie im Feld **Work with** (Arbeiten mit) Folgendes ein: **https://dl.microsoft.com/eclipse** .

4. Wählen Sie **Hinzufügen**.

    ![Seite „Verfügbare Software“][sf-eclipse-plugin]

5. Wählen Sie das Plug-In **ServiceFabric** aus, und klicken Sie auf **Weiter**.

6. Führen Sie die Installationsschritte aus. Akzeptieren Sie anschließend den Endbenutzer-Lizenzvertrag.

Falls Sie das Service Fabric-Plug-In für Eclipse bereits installiert haben, vergewissern Sie sich, dass Sie über die aktuelle Version verfügen. Die Version können Sie unter **Hilfe** > **Über Eclipse** > **Installationsdetails** überprüfen. Suchen Sie in der Liste der installierten Plug-Ins nach „Service Fabric“. Klicken Sie auf **Aktualisieren**, falls eine neuere Version verfügbar ist.

Weitere Informationen finden Sie unter [Service Fabric-Plug-In für die Entwicklung von Eclipse-Java-Anwendungen](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Aktualisieren des SDKs und der Laufzeit

Führen Sie die folgenden Befehle aus, um das Update auf die aktuelle Version des SDK und der Runtime durchzuführen:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Für die Aktualisierung der Java SDK-Binärdateien aus Maven müssen Sie die Versionsdetails der entsprechenden Binärdatei in der Datei ``build.gradle`` so aktualisieren, dass sie auf die aktuelle Version verweisen. Informationen dazu, wo genau die Version aktualisiert werden muss, finden Sie in jeder ``build.gradle``-Datei in den [Beispielen für die ersten Schritte mit Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Die Aktualisierung der Pakete kann die Beendigung Ihres lokalen Entwicklungsclusters zur Folge haben. Starten Sie den lokalen Cluster nach einem Upgrade anhand der Anweisungen in diesem Artikel neu.

## <a name="remove-the-sdk"></a>Entfernen des SDK
Führen Sie die folgenden Befehle aus, um die Service Fabric SDKs zu entfernen:

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (Unterstützung der Service Fabric-Vorschauversion)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe von Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe des Service Fabric-Plug-Ins für Eclipse](service-fabric-get-started-eclipse.md)
* [Erstellen der ersten Java-Anwendung unter Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX (Vorbereiten Ihrer Entwicklungsumgebung unter OSX)](service-fabric-get-started-mac.md)
* [Vorbereiten einer Linux-Entwicklungsumgebung unter Windows](service-fabric-local-linux-cluster-windows.md)
* [Verwalten Ihrer Anwendungen mithilfe der Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
* [Unterschiede zwischen Service Fabric unter Linux und Windows](service-fabric-linux-windows-differences.md)
* [Erste Schritte mit der Service Fabric CLI](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
