---
title: Erstellen Ihrer ersten Azure Service Fabric-App unter Linux mit C# | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Service Fabric-Anwendung mit C# und .NET Core 2.0 erstellen und bereitstellen.
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: subramar
ms.openlocfilehash: 7bb9b1c545d30f66a09817ba977f12666a9f41c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394298"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Erstellen Ihrer ersten Azure Service Fabric-Anwendung
> [!div class="op_single_selector"]
> * [Java – Linux (Vorschauversion)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux (Vorschauversion)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric bietet SDKs, mit denen sich Dienste unter Linux sowohl in .NET Core als auch in Java erstellen lassen. In diesem Tutorial erfahren Sie, wie Sie eine Anwendung für Linux und einen Dienst mit C# unter .NET Core 2.0 erstellen.

## <a name="prerequisites"></a>Voraussetzungen
Vor Beginn des Tutorials müssen Sie zunächst [Ihre Linux-Entwicklungsumgebung einrichten](service-fabric-get-started-linux.md). Bei Verwendung von Mac OS X können Sie [mithilfe von Vagrant eine Linux-One-Box-Umgebung auf einem virtuellen Computer einrichten](service-fabric-get-started-mac.md).

Darüber hinaus sollte die [Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md) installiert werden.

### <a name="install-and-set-up-the-generators-for-c"></a>Installieren und Einrichten der Generatoren für C#
Die Gerüstbautools von Service Fabric unterstützen Sie beim Erstellen von Service Fabric-Anwendungen über das Terminal mithilfe von Yeoman-Vorlagengeneratoren. Führen Sie die folgenden Schritte aus, um die Service Fabric-Yeoman-Vorlagengeneratoren für C# einzurichten:

1. Installieren von Node.js und npm auf dem Computer

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. Installieren Sie den [Yeoman](https://yeoman.io/)-Vorlagengenerator auf dem Computer über npm.

   ```bash
   npm install -g yo
   ```
3. Installieren des Service Fabric-Yeoman-C#-Anwendungsgenerators über npm

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Erstellen der Anwendung
Eine Service Fabric-Anwendung kann einen oder mehrere Dienste enthalten, die jeweils eine bestimmte Rolle bei der Bereitstellung von Funktionen der Anwendung haben. Mit dem Service Fabric-[Yeoman](https://yeoman.io/)-Generator für C#, der im letzten Schritt installiert wurde, können Sie problemlos Ihren ersten Dienst erstellen und später weitere Dienste hinzufügen. Im nächsten Schritt erstellen wir mithilfe von Yeoman eine Anwendung mit einem einzelnen Dienst.

1. Geben Sie an einem Terminal den folgenden Befehl ein, um mit dem Erstellen des Gerüsts zu beginnen: `yo azuresfcsharp`
2. Benennen Sie Ihre Anwendung.
3. Wählen Sie die Art Ihres ersten Diensts aus, und benennen Sie ihn. Im Rahmen dieses Tutorials wählen wir einen Reliable Actor-Dienst aus.

   ![Service Fabric-Yeoman-Generator für C#][sf-yeoman]

> [!NOTE]
> Weitere Informationen zu den Optionen finden Sie unter [Übersicht über die Service Fabric-Programmiermodelle](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Erstellen der Anwendung
Die Yeoman-Vorlagen von Service Fabric enthalten ein Buildskript. Damit können Sie (nach Navigation zum Anwendungsordner) die App über das Terminal erstellen.

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung

Die erstellte Anwendung kann im lokalen Cluster bereitgestellt werden.

1. Stellen Sie eine Verbindung mit dem lokalen Service Fabric-Cluster her.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Führen Sie das in der Vorlage bereitgestellte Installationsskript aus, um das Anwendungspaket in den Imagespeicher des Clusters zu kopieren, den Anwendungstyp zu registrieren und eine Instanz der Anwendung zu erstellen.

    ```bash
    ./install.sh
    ```

Bei der Bereitstellung der erstellten Anwendung wird genau wie bei anderen Service Fabric-Anwendungen vorgegangen. Ausführliche Anweisungen finden Sie in der Dokumentation zum [Verwalten einer Service Fabric-Anwendung mit der Service Fabric-Befehlszeilenschnittstelle](service-fabric-application-lifecycle-sfctl.md).

Parameter für diesen Befehl finden Sie in den erstellten Manifesten im Anwendungspaket.

Öffnen Sie nach der Bereitstellung der Anwendung einen Browser, und navigieren Sie zu [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) unter [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Erweitern Sie dann den Knoten **Anwendungen**. Hier finden Sie nun einen Eintrag für Ihren Anwendungstyp und einen weiteren für die erste Instanz dieses Typs.

> [!IMPORTANT]
> Wenn Sie die Anwendung in einem sicheren Linux-Cluster in Azure bereitstellen möchten, müssen Sie ein Zertifikat konfigurieren, um Ihre Anwendung mit der Service Fabric-Runtime zu überprüfen. Dadurch wird Ihren Reliable Services-Diensten die Kommunikation mit den zugrunde liegenden Service Fabric-Runtime-APIs ermöglicht. Weitere Informationen finden Sie unter [Konfigurieren einer Reliable Services-App zur Ausführung in Linux-Clustern](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Starten des Testclients und Ausführen eines Failovers
Actor-Projekte führen keine eigenständigen Aktionen durch. Sie benötigen einen anderen Dienst oder Client, der ihnen Nachrichten sendet. Die Actor-Vorlage enthält ein einfaches Testskript, das Sie für die Interaktion mit dem Actor-Dienst verwenden können.

1. Führen Sie das Skript mithilfe des watch-Hilfsprogramms aus, um die Ausgabe des Actor-Diensts zu erhalten.

   Im Fall von MAC OS X müssen Sie zusätzlich folgende Befehle ausführen, um den Ordner „myactorsvcTestClient“ an denselben Speicherort innerhalb des Containers zu kopieren.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Suchen Sie in Service Fabric Explorer den Knoten, der das primäre Replikat für den Actor-Dienst hostet. Im folgenden Screenshot ist das „Node_3“:

    ![Suchen des primären Replikats in Service Fabric Explorer][sfx-primary]
3. Klicken Sie auf den Knoten, den Sie im vorherigen Schritt ermittelt haben, und wählen Sie im Aktionsmenü die Option **Deaktivieren (neu starten)** aus. Mit dieser Aktion wird ein Knoten in Ihrem lokalen Cluster neu gestartet und ein Failover auf ein sekundäres Replikat erzwungen, das auf einem anderen Knoten ausgeführt wird. Behalten Sie bei dieser Aktion die Ausgabe des Testclients im Auge, und beachten Sie, dass sich der Zähler trotz des Failovers weiter erhöht.

## <a name="adding-more-services-to-an-existing-application"></a>Hinzufügen weiterer Dienste zu einer vorhandenen Anwendung

Führen Sie zum Hinzufügen eines weiteren Diensts zu einer Anwendung, die bereits mit `yo` erstellt wurde, die folgenden Schritte aus:
1. Legen Sie das Verzeichnis auf den Stamm der vorhandenen Anwendung fest.  Beispiel: `cd ~/YeomanSamples/MyApplication`, wenn `MyApplication` die von Yeoman erstellte Anwendung ist.
2. Führen Sie `yo azuresfcsharp:AddService` aus.

## <a name="next-steps"></a>Nächste Schritte

* [Interagieren mit Service Fabric-Clustern mithilfe der Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md)
* Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).
* [Erste Schritte mit der Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
