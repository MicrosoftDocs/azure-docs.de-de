---
title: Erstellen einer Linux-Container-App unter Service Fabric in Azure | Microsoft-Dokumentation
description: In dieser Schnellstartanleitung erstellen Sie ein Docker-Image mit Ihrer Anwendung, übertragen Sie es per Push an eine Containerregistrierung und stellen dann Ihren Container in einem Service Fabric-Cluster bereit.
services: service-fabric
documentationcenter: linux
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/30/2019
ms.author: aljo,suhuruli
ms.custom: mvc
ms.openlocfilehash: 1487c9e3e57a2fe4dcdcab04aa81cd81315decfd
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302295"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Schnellstart: Bereitstellen von Linux-Containern in Service Fabric

Azure Service Fabric ist eine Plattform für verteilte Systeme zum Bereitstellen und Verwalten von skalierbaren und zuverlässigen Microservices und Containern.

In diesem Schnellstart wird veranschaulicht, wie Sie Linux-Container in einem Service Fabric-Cluster in Azure bereitstellen. Nach Abschluss des Vorgangs verfügen Sie über eine Abstimmungsanwendung mit einem Python-Web-Front-End und einem Redis-Back-End, die in einem Service Fabric-Cluster ausgeführt werden. Außerdem erfahren Sie, wie Sie ein Failover für eine Anwendung ausführen und eine Anwendung in Ihrem Cluster skalieren.

![Webseite der Voting-App][quickstartpic]

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

1. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

2. Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).

3. Installieren Sie das [Service Fabric SDK und die CLI](service-fabric-get-started-linux.md#installation-methods).

4. [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Abrufen des Anwendungspakets

Zum Bereitstellen von Containern in Service Fabric benötigen Sie eine Gruppe von Manifestdateien (die Anwendungsdefinition), in denen die einzelnen Container und die Anwendung beschrieben ist.

Klonen Sie in einer Konsole mithilfe von „git“ eine Kopie der Anwendungsdefinition, und navigieren Sie anschließend in Ihrem Klon zum Verzeichnis `Voting`.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Erstellen von Service Fabric-Clustern

Für die Bereitstellung der Anwendung in Azure benötigen Sie einen Service Fabric-Cluster, in dem die Anwendung ausgeführt wird. Die folgenden Befehle erstellen einen Cluster mit fünf Knoten in Azure.  Diese Befehle erstellen auch ein selbstsigniertes Zertifikat, fügen es einem Schlüsseltresor hinzu und laden das Zertifikat lokal herunter. Das neue Zertifikat dient zum Sichern des Clusters, wenn er bereitgestellt wird, sowie zum Authentifizieren von Clients.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Der Web-Front-End-Dienst ist für das Lauschen auf eingehenden Datenverkehr über Port 80 konfiguriert. Port 80 ist auf Ihren virtuellen Clustercomputern und dem Azure-Lastenausgleich standardmäßig geöffnet.
>

## <a name="configure-your-environment"></a>Konfigurieren der Umgebung

Service Fabric bietet mehrere Tools, mit denen Sie einen Cluster und die dazugehörigen Anwendungen verwalten können:

- Service Fabric Explorer: Ein browserbasiertes Tool.
- Service Fabric-Befehlszeilenschnittstelle (CLI): Basiert auf der Azure CLI. 
- PowerShell-Befehle

In dieser Schnellstartanleitung verwenden Sie die Service Fabric-Befehlszeilenschnittstelle sowie Service Fabric Explorer (ein webbasiertes Tool). Damit Sie Service Fabric Explorer verwenden können, müssen Sie die PFX-Zertifikatdatei in den Browser importieren. Für die PFX-Datei ist standardmäßig kein Kennwort festgelegt.

Der Standardbrowser in Ubuntu 16.04 ist Mozilla Firefox. Klicken Sie zum Importieren des Zertifikats rechts oben in Firefox auf die Menüschaltfläche und anschließend auf **Einstellungen**. Suchen Sie über das Suchfeld auf der Seite **Einstellungen** nach „Zertifikate“. Klicken Sie auf **Zertifikate anzeigen**, klicken Sie auf die Registerkarte **Ihre Zertifikate**, klicken Sie auf **Importieren**, und befolgen Sie die Anweisungen zum Importieren des Zertifikats.

   ![Installieren des Zertifikats in Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Bereitstellen der Service Fabric-Anwendung

1. Stellen Sie mithilfe der CLI eine Verbindung mit dem Service Fabric-Cluster in Azure her. Der Endpunkt ist der Verwaltungsendpunkt für Ihren Cluster. Die PEM-Datei haben Sie im vorherigen Abschnitt erstellt. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Verwenden Sie das Installationsskript, um die Definition der Voting-Anwendung in den Cluster zu kopieren, den Anwendungstyp zu registrieren und eine Instanz der Anwendung zu erstellen.  Die PEM-Zertifikatdatei muss sich im gleichen Verzeichnis wie die Datei *install.sh* befinden.

    ```bash
    ./install.sh
    ```

3. Navigieren Sie in einem Webbrowser zum Service Fabric Explorer-Endpunkt für Ihren Cluster. Der Endpunkt hat das folgende Format: **https://\<URL Ihres Azure Service Fabric-Clusters>:19080/Explorer**. Beispiel: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`. </br>

4. Erweitern Sie den Knoten **Anwendungen**, um zu prüfen, ob nun ein Eintrag für den Voting-Anwendungstyp und die von Ihnen erstellte Instanz vorhanden ist.

    ![Service Fabric Explorer][sfx]

5. Navigieren Sie in einem Webbrowser zur URL des Clusters (beispielsweise `http://containertestcluster.eastus.cloudapp.azure.com:80`), um eine Verbindung mit dem ausgeführten Container herzustellen. Die Abstimmungsanwendung sollte im Browser angezeigt werden.

    ![Webseite der Voting-App][quickstartpic]

> [!NOTE]
> Service Fabric-Anwendungen können auch mit Docker Compose bereitgestellt werden. Verwenden Sie beispielsweise den folgenden Befehl, um die Anwendung mithilfe von Docker Compose im Cluster bereitzustellen und zu installieren:
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Ausführen eines Failovers für einen Container in einem Cluster

Bei einem Ausfall sorgt Service Fabric dafür, dass Ihre Containerinstanzen automatisch auf andere Knoten im Cluster verschoben werden. Sie können die Container eines Knotens auch manuell entfernen und diese geregelt auf andere Knoten im Cluster verschieben. Service Fabric bietet mehrere Skalierungsoptionen für Dienste. In den folgenden Schritten wird Service Fabric Explorer verwendet.

Führen Sie die folgenden Schritte aus, um für den Front-End-Container ein Failover auszuführen:

1. Öffnen Sie Service Fabric Explorer in Ihrem Cluster. Beispiel: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Klicken Sie in der Strukturansicht auf den Knoten **fabric:/Voting/azurevotefront**, und erweitern Sie den (durch eine GUID dargestellten) Partitionsknoten. Beachten Sie den Knotennamen in der Strukturansicht. Hier werden die Knoten angezeigt, auf denen der Container derzeit ausgeführt wird (beispielsweise `_nodetype_1`).
3. Erweitern Sie in der Strukturansicht den Knoten **Knoten**. Klicken Sie neben dem Knoten, auf dem der Container ausgeführt wird, auf die Auslassungspunkte.
4. Wählen Sie **Neu starten** aus, um den Knoten neu zu starten, und bestätigen Sie die Neustartaktion. Der Neustart bewirkt, dass für den Container ein Failover auf einen anderen Knoten im Cluster ausgeführt wird.

    ![Knotenansicht in Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalieren von Anwendungen und Diensten in einem Cluster

Service Fabric-Dienste können für einen Cluster auf einfache Weise skaliert werden, um die Last für die Dienste zu bewältigen. Sie skalieren einen Dienst, indem Sie die Anzahl von Instanzen ändern, die im Cluster ausgeführt werden.

Führen Sie die folgenden Schritte aus, um den Web-Front-End-Dienst zu skalieren:

1. Öffnen Sie Service Fabric Explorer in Ihrem Cluster. Beispiel: `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Klicken Sie in der Strukturansicht neben dem Knoten **fabric:/Voting/azurevotefront** auf die drei Auslassungspunkte und anschließend auf **Scale Service** (Dienst skalieren).

    ![Service Fabric Explorer: Starten der Dienstskalierung][containersquickstartscale]

    Sie können jetzt angeben, dass die Anzahl von Instanzen des Web-Front-End-Diensts skaliert werden soll.

3. Ändern Sie die Anzahl in **2**, und klicken Sie auf **Scale Service** (Dienst skalieren).
4. Klicken Sie in der Strukturansicht auf den Knoten **fabric:/Voting/azurevotefront**, und erweitern Sie den (durch eine GUID dargestellten) Partitionsknoten.

    ![Service Fabric Explorer: Dienstskalierung abgeschlossen][containersquickstartscaledone]

    Sie sehen jetzt, dass der Dienst über zwei Instanzen verfügt. In der Strukturansicht wird angezeigt, auf welchen Knoten die Instanzen ausgeführt werden.

Mit dieser einfachen Verwaltungsaufgabe haben Sie die Ressourcen verdoppelt, die dem Front-End-Dienst zur Bewältigung der Benutzerauslastung zur Verfügung stehen. Es ist wichtig zu verstehen, dass Sie nicht mehrere Instanzen eines Diensts benötigen, damit er zuverlässig ausgeführt wird. Sollte ein Dienst ausfallen, stellt Service Fabric sicher, dass im Cluster eine neue Dienstinstanz ausgeführt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie das Deinstallationsskript (uninstall.sh) aus der Vorlage, um die Anwendungsinstanz aus dem Cluster zu löschen und die Registrierung des Anwendungstyps aufzuheben. Es dauert etwas, bis die Instanz durch dieses Skript bereinigt wurde. Aus diesem Grund sollte das Installationsskript nicht unmittelbar nach diesem Skript ausgeführt werden. Sie können mithilfe von Service Fabric Explorer ermitteln, ob die Instanz entfernt und die Registrierung des Anwendungstyps aufgehoben wurde.

```bash
./uninstall.sh
```

Die einfachste Möglichkeit zum Löschen des Clusters und aller darin genutzten Ressourcen besteht darin, die Ressourcengruppe zu löschen.

Melden Sie sich bei Azure an, und wählen Sie die Abonnement-ID aus, mit der Sie den Cluster entfernen möchten. Melden Sie sich beim Azure-Portal an, um Ihre Abonnement-ID zu ermitteln. Löschen Sie die Ressourcengruppe und alle Clusterressourcen mithilfe des Befehls [az group delete](/cli/azure/group?view=azure-cli-latest).

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Nach Abschluss der Clusterverwendung können Sie das Zertifikat aus Ihrem Zertifikatspeicher entfernen. Beispiel: 
- Unter Windows: Verwenden Sie das [MMC-Snap-In „Zertifikate“](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Beim Hinzufügen des Snap-Ins muss **Mein Benutzerkonto** ausgewählt sein. Navigieren Sie zu `Certificates - Current User\Personal\Certificates`, und entfernen Sie das Zertifikat.
- Auf einem Macintosh: Verwenden Sie die Keychain-App.
- Unter Ubuntu: Führen Sie die Schritte aus, die Sie zum Anzeigen von Zertifikaten verwendet haben, und entfernen Sie das Zertifikat.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Linux-Containeranwendung in einem Service Fabric-Cluster in Azure bereitgestellt, ein Failover für die Anwendung ausgeführt und die Anwendung im Cluster skaliert. Weitere Informationen zur Verwendung von Linux-Containern in Service Fabric finden Sie im Tutorial für Linux-Container-Apps.

> [!div class="nextstepaction"]
> [Erstellen einer Linux-Container-App](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
