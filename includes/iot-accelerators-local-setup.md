---
title: include file
description: include file
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178524"
---
## <a name="download-the-source-code"></a>Herunterladen des Quellcodes

Die Quellcoderepositorys für die Remoteüberwachung umfassen den Quellcode und die Docker-Konfigurationsdateien, die Sie zum Ausführen der Docker-Images für die Microservices benötigen.

Um eine lokale Version des Repositorys zu klonen und zu erstellen, navigieren Sie über Ihre Befehlszeilenumgebung zu einem geeigneten Ordner auf dem lokalen Computer. Führen Sie dann einen der folgenden Befehlssätze aus, um das .NET-Repository zu klonen:

Um die neueste Version der .NET-Microservice-Implementierungen herunterzuladen, führen Sie Folgendes aus:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Diese Befehle laden den Quellcode für alle Microservices zusätzlich zu den Skripts herunter, mit denen Sie die Microservices lokal ausführen. Obwohl Sie den Quellcode zum Ausführen der Microservices in Docker nicht benötigen, ist er hilfreich, wenn Sie den Solution Accelerator später ändern und Ihre Änderungen lokal testen möchten.

## <a name="deploy-the-azure-services"></a>Bereitstellen von Azure-Diensten

In diesem Artikel erfahren Sie zwar, wie Sie die Microservices lokal ausführen, doch sie hängen von Azure-Diensten ab, die in der Cloud ausgeführt werden. Stellen Sie die Azure-Dienste mit dem nachfolgenden Skript bereit. Bei den nachstehenden Skriptbeispielen wird davon ausgegangen, dass Sie das .NET-Repository auf einem Windows-Computer verwenden. Wenn Sie in einer anderen Umgebung arbeiten, passen Sie die Pfade, Dateierweiterungen und Pfadtrennzeichen entsprechend an.

### <a name="create-new-azure-resources"></a>Erstellen neuer Azure-Ressourcen

Wenn Sie die erforderlichen Azure-Ressourcen noch nicht erstellt haben, führen Sie die folgenden Schritte aus:

1. Navigieren Sie in Ihrer Befehlszeilenumgebung zum Ordner **\services\scripts\local\launch** in Ihrer geklonten Kopie des Repositorys.

1. Führen Sie die folgenden Befehle aus, um das CLI-Tool **pcs** zu installieren und sich bei Ihrem Azure-Konto anzumelden:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Führen Sie das Skript **start.cmd** aus. Das Skript fordert Sie zur Eingabe der folgenden Informationen auf:
   * Ein Lösungsname.
   * Das zu verwendende Azure-Abonnement.
   * Der Speicherort des zu verwendenden Azure-Rechenzentrums.

     Das Skript erstellt die Ressourcengruppe in Azure mit dem Namen Ihrer Projektmappe. Diese Ressourcengruppe enthält die Azure-Ressourcen, die der Solution Accelerator verwendet. Sie können diese Ressourcengruppe löschen, wenn Sie die entsprechenden Ressourcen nicht mehr benötigen.

     Mit dem Skript werden auf dem lokalen Computer zudem mehrere Umgebungsvariablen mit dem Präfix **PCS** hinzugefügt. Diese Umgebungsvariablen stellen die Details für die Remoteüberwachung bereit, damit sie aus einer Azure Key Vault-Ressource gelesen werden können. Diese Key Vault-Ressource ist die Quelle, aus der die Remoteüberwachung ihre Konfigurationswerte lesen wird.

     > [!TIP]
     > Nach Abschluss des Skripts werden außerdem die Umgebungsvariablen in der Datei **\<Ihr Stammordner\>\\.pcs\\\<Lösungsname\>.env** gespeichert. Sie können sie für zukünftige Bereitstellungen des Solution Accelerators verwenden. Beachten Sie, dass alle auf dem lokalen Computer festgelegten Umgebungsvariablen die Werte in der Datei **services\\scripts\\local\\.env** überschreiben, wenn Sie **docker-compose** ausführen.

1. Beenden Sie die Befehlszeilenumgebung.

### <a name="use-existing-azure-resources"></a>Verwenden vorhandener Azure-Ressourcen

Wenn Sie die erforderlichen Azure-Ressourcen bereits erstellt haben, erstellen Sie die entsprechenden Umgebungsvariablen auf dem lokalen Computer.
Legen Sie die Umgebungsvariablen für Folgendes fest:
* **PCS_KEYVAULT_NAME** – Der Name der Azure Key Vault-Ressource
* **PCS_AAD_APPID** – Die AAD-Anwendungs-ID
* **PCS_AAD_APPSECRET** – Das AAD-Anwendungsgeheimnis

Konfigurationswerte werden aus dieser Azure Key Vault-Ressource gelesen. Diese Umgebungsvariablen sind möglicherweise in der Datei **\<Ihr Stammordner\>\\.pcs\\\<Lösungsname\>.env** aus der Bereitstellung gespeichert. Beachten Sie, dass die auf dem lokalen Computer festgelegten Umgebungsvariablen die Werte in der Datei **services\\scripts\\local\\.env** überschreiben, wenn Sie **docker-compose** ausführen.

Ein Teil der vom Microservice benötigten Konfiguration wird in einer **Key Vault**-Instanz gespeichert, die bei der ersten Bereitstellung erstellt wurde. Die entsprechenden Variablen in Key Vault sollten nach Bedarf geändert werden.