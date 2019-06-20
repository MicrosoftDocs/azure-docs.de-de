---
title: Verwalten von Azure Service Fabric-Anwendungen mithilfe der Azure Service Fabric-Befehlszeilenschnittstelle (sfctl)
description: Erfahren Sie, wie Sie Anwendungen aus einem Azure Service Fabric-Cluster mithilfe der Azure Service Fabric CLI bereitstellen und entfernen.
services: service-fabric
author: Christina-Kang
manager: chackdan
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 9b0f785a6a43f984708645084a8a8036326d3d24
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60621376"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Verwalten von Azure Service Fabric-Anwendungen mithilfe der Azure Service Fabric-Befehlszeilenschnittstelle (sfctl)

Erfahren Sie, wie Sie Anwendungen erstellen und löschen, die in einem Azure Service Fabric-Cluster ausgeführt werden.

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie die Azure Service Fabric CLI. Wählen Sie anschließend Ihren Service Fabric-Cluster aus. Weitere Informationen finden Sie unter [Erste Schritte mit der Service Fabric CLI](service-fabric-cli.md).

* Halten Sie ein Service Fabric-Anwendungspaket für die Bereitstellung bereit. Weitere Informationen zum Erstellen und Packen einer Anwendung finden Sie unter [Service Fabric-Anwendungsmodell](service-fabric-application-model.md).

## <a name="overview"></a>Übersicht

Um eine neue Anwendung bereitzustellen, gehen Sie folgendermaßen vor:

1. Laden Sie ein Anwendungspaket in den Service Fabric-Imagespeicher hoch.
2. Stellen Sie einen Anwendungstyp bereit.
3. Löschen Sie den Inhalt des Imagespeichers.
4. Geben Sie eine Anwendung an, und erstellen Sie diese.
5. Geben Sie Dienste an, und erstellen Sie diese.

Um eine vorhandene Anwendung zu entfernen, gehen Sie wie folgt vor:

1. Löschen Sie die Anwendung.
2. Heben Sie die Bereitstellung des zugeordneten Anwendungstyps auf.

## <a name="deploy-a-new-application"></a>Hinzufügen einer neuen Anwendung

Um eine neue Anwendung bereitzustellen, führen Sie die folgenden Aufgaben aus:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Laden Sie das Anwendungspaket in den Imagespeicher hoch

Laden Sie vor dem Erstellen einer Anwendung das Anwendungspaket in den Service Fabric-Imagespeicher hoch.

Wenn sich Ihr Anwendungspaket beispielsweise im Verzeichnis `app_package_dir` befindet, verwenden Sie die folgenden Befehle, um das Verzeichnis hochzuladen:

```azurecli
sfctl application upload --path ~/app_package_dir
```

Für große Anwendungspakete können Sie die `--show-progress`-Option festlegen, um den Status des Uploads angezeigt zu bekommen.

### <a name="provision-the-application-type"></a>Bereitstellen des Anwendungstyps

Wenn der Upload abgeschlossen ist, stellen Sie die Anwendung bereit. Um die Anwendung bereitzustellen, verwenden Sie den folgenden Befehl:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

Der Wert für `application-type-build-path` ist der Name des Verzeichnisses, in das Sie Ihr Anwendungspaket hochgeladen haben.

### <a name="delete-the-application-package"></a>Löschen des Anwendungspakets

Es wird empfohlen, nach erfolgreicher Registrierung der Anwendung das Anwendungspaket zu entfernen.  Sie können Systemressourcen freigeben, indem Sie Anwendungspakete aus dem Imagespeicher löschen.  Die Speicherung nicht verwendeter Anwendungspakete nimmt Speicherplatz in Anspruch und führt zu Leistungsproblemen der Anwendung. 

Um das Anwendungspaket aus dem Imagespeicher zu löschen, verwenden Sie den folgenden Befehl:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` muss der Name des Verzeichnisses sein, das Sie beim Erstellen der Anwendung hochgeladen haben.

### <a name="create-an-application-from-an-application-type"></a>Erstellen einer Anwendung aus einem Anwendungstyp

Nachdem Sie die Anwendung bereitgestellt haben, verwenden Sie den folgenden Befehl, um Ihre Anwendung zu benennen und zu erstellen:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` ist der Name, den Sie für die Anwendungsinstanz verwenden möchten. Sie können zusätzliche Parameter aus dem zuvor bereitgestellten Anwendungsmanifest abrufen.

Der Anwendungsname muss mit dem Präfix `fabric:/` beginnen.

### <a name="create-services-for-the-new-application"></a>Dienste für die neue Anwendung erstellen

Nachdem Sie eine Anwendung erstellt haben, erstellen Sie Dienste aus der Anwendung. Im folgenden Beispiel erstellen wir einen neuen zustandslosen Dienst aus unserer Anwendung. Die Dienste, die Sie aus einer Anwendung erstellen können, sind in einem Dienstmanifest in dem zuvor bereitgestellten Anwendungspaket definiert.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Überprüfen der Anwendungsbereitstellung und -integrität

Um sicherzustellen, dass alle Komponenten fehlerfrei sind, verwenden Sie die folgenden integritätsbezogenen Befehle:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Um sicherzustellen, dass der Dienst fehlerfrei ist, verwenden Sie ähnliche Befehle zum Abrufen der Integrität des Dienstes und der Anwendung:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Fehlerfreie Dienste und Anwendungen weisen einen `HealthState`-Wert von `Ok` auf.

## <a name="remove-an-existing-application"></a>Entfernen einer vorhandenen Anwendung

Um eine Anwendung zu entfernen, führen Sie die folgenden Aufgaben aus:

### <a name="delete-the-application"></a>Löschen der Anwendung

Um die Anwendung zu löschen, verwenden Sie den folgenden Befehl:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Aufheben der Bereitstellung des Anwendungstyps

Nachdem Sie die Anwendung gelöscht haben, können Sie die Bereitstellung des Anwendungstyps aufheben, wenn Sie diesen nicht mehr benötigen. Um die Bereitstellung des Anwendungstyps aufzuheben, verwenden Sie den folgenden Befehl:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Der Typname und die Typversion müssen mit dem Namen und der Version im zuvor bereitgestellten Anwendungsmanifest übereinstimmen.

## <a name="upgrade-application"></a>Aktualisieren der Anwendung

Nach Erstellung Ihrer Anwendung können Sie dieselben Schritte wiederholen, um eine zweite Version Ihrer Anwendung bereitzustellen. Anschließend können mit einem Service Fabric-Anwendungsupgrade eine Umstellung auf die Ausführung der zweiten Version der Anwendung vornehmen. Weitere Informationen finden Sie in der Dokumentation zu [Service Fabric-Anwendungsupgrades](service-fabric-application-upgrade.md).

Zur Durchführung eines Upgrades müssen Sie zunächst die nächste Version der Anwendung über dieselben Befehle wie zuvor bereitstellen:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Dann wird empfohlen, ein überwachtes automatisches Upgrade ausführen und dann das Upgrade zu starten, indem Sie den folgenden Befehl ausführen:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Bei Upgrades werden vorhandene Parameter stets überschrieben. Anwendungsparameter müssen bei Bedarf als Argumente an den Upgradebefehl übergeben werden. Anwendungsparameter müssen als JSON-Objekt codiert werden.

Um alle zuvor festgelegten Parameter abzurufen, können Sie den Befehl `sfctl application info` ausführen.

Während ein Anwendungsupgrade im Gang ist, kann dessen Status mit dem Befehl `sfctl application upgrade-status` abgerufen werden.

Wenn schließlich ein Upgrade läuft und abgebrochen werden soll, können Sie mit dem Befehl `sfctl application upgrade-rollback` das Upgrade rückgängig machen.

## <a name="next-steps"></a>Nächste Schritte

* [Service Fabric CLI: Grundlagen](service-fabric-cli.md)
* [Erste Schritte mit Service Fabric unter Linux](service-fabric-get-started-linux.md)
* [Starten eines Service Fabric-Anwendungsupgrades](service-fabric-application-upgrade.md)
