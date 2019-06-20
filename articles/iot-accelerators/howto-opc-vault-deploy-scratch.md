---
title: Bereitstellen des OPC Vault-Moduls in Azure ohne vorhandenes Projekt | Microsoft-Dokumentation
description: Bereitstellen eines OPC-Tresors von Grund auf
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 990b13d362b796f5e71f1bd41eb35fce9720fff4
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835164"
---
# <a name="deploy-opc-vault-from-scratch"></a>Bereitstellen eines OPC-Tresors von Grund auf

OPC Vault ist ein Microservice, mit dem der Zertifikatlebenszyklus für OPC UA-Server- und -Clientanwendungen in der Cloud konfiguriert, registriert und verwaltet werden kann. In diesem Artikel erfahren Sie, wie Sie OPC Vault von Grund auf bereitstellen.

## <a name="configuration-and-environment-variables"></a>Konfiguration und Umgebungsvariablen

Die Dienstkonfiguration wird mithilfe von ASP.NET Core-Konfigurationsadaptern in „appsettings.ini“ gespeichert. Das INI-Format ermöglicht es, Werte in einem lesbaren Format mit Kommentaren zu speichern.
Die Anwendung unterstützt auch das Einfügen von Umgebungsvariablen wie Anmeldeinformationen und Netzwerkdetails. (Dieser Abschnitt trägt ursprünglich den Titel „TODO Konfiguration und Umgebungsvariablen“).

Die Konfigurationsdatei im Repository verweist auf einige Umgebungsvariablen, die mindestens einmal erstellt werden müssen. Abhängig von Ihrem Betriebssystem und der integrierten Entwicklungsumgebung gibt es verschiedene Möglichkeiten, Umgebungsvariablen zu verwalten:

- Für Windows-Benutzer muss das Skript „env-vars-setup.cmd“ nur einmal vorbereitet und ausgeführt werden. Nach der Ausführung bleiben die Einstellungen über Terminalsitzungen und Neustarts hinweg erhalten.

- In Linux- und OSX-Umgebungen muss das Skript „env-vars-setup“ bei jedem Öffnen einer neuen Konsole ausgeführt werden. Je nach Betriebssystem und Terminal gibt es Möglichkeiten, Werte global zu erhalten. Die folgenden Seiten sollten weitere Informationen bereitstellen:

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio: Umgebungsvariablen können auch in Visual Studio unter den Projekteigenschaften festgelegt werden. Wählen Sie im linken Bereich „Konfigurationseigenschaften“ und „Umgebung“, um zu einem Abschnitt zu gelangen, in dem Sie mehrere Variablen hinzufügen können.

- IntelliJ Rider: Umgebungsvariablen können in jeder Laufzeitkonfiguration festgelegt werden, ähnlich wie bei IntelliJ IDEA https://www.jetbrains.com/help/idea/run-debug-configuration-application.html.

## <a name="run-and-debug-with-visual-studio"></a>Ausführen und Debuggen mit Visual Studio

Mit Visual Studio können Sie die Anwendung schnell und ohne Eingabeaufforderung öffnen, ohne etwas außerhalb der integrierten Entwicklungsumgebung zu konfigurieren.

Dies sind die erforderlichen Schritte zum Ausführen und Debuggen der Anwendung mit Visual Studio:

1. Öffnen Sie die Projektmappe mit der Datei `iot-opc-gds-service.sln`.

1. Wenn die Projektmappe geladen ist, klicken Sie mit der rechten Maustaste auf das Projekt `WebService`, und wählen Sie den Abschnitt `Debug` aus, um dorthin zu wechseln.

1. Definieren Sie im gleichen Abschnitt die erforderlichen Umgebungsvariablen.

1. Drücken Sie **F5** oder auf das Symbol **Ausführen**. Visual Studio sollte Ihren Browser öffnen und den Dienststatus im JSON-Format anzeigen.

## <a name="run-and-debug-with-intellij-rider"></a>Ausführen und Debuggen mit IntelliJ Rider

1. Öffnen Sie die Projektmappe mit der Datei `iot-opc-gds-service.sln`.

1. Wenn die Projektmappe geladen ist, wechseln Sie zu `Run > Edit Configurations`, und erstellen Sie eine neue `.NET Project`-Konfiguration.

1. Wählen Sie in der Konfiguration das WebService-Projekt aus.

1. Speichern Sie die Einstellungen, und führen Sie die Konfiguration aus, die über die IDE-Symbolleiste erstellt wurde.

1. Es sollten die Bootstrap-Dienstmeldungen im IntelliJ Run-Fenster mit Details wie der URL, unter der der Webdienst ausgeführt wird, und den Dienstprotokollen angezeigt werden.

## <a name="build-and-run-from-the-command-line"></a>Erstellen und Ausführen über die Befehlszeile

Der Skriptordner enthält einige Skripts für die häufigen Aufgaben:

- `build`: Alle Projekte kompilieren und die Tests durchführen.

- `compile`: Alle Projekte kompilieren.

- `run`: Die Projekte kompilieren und den Dienst ausführen, wodurch in Windows erhöhte Rechte für die Ausführung des Webdiensts erforderlich sind.

Die Skripts prüfen die Einrichtung der Umgebungsvariablen. Sie können die Umgebungsvariablen global in Ihrem Betriebssystem festlegen oder das Skript „env-vars-setup“ im Skriptordner verwenden.

### <a name="sandbox"></a>Sandbox

Die Skripts gehen davon aus, dass Sie Ihre Entwicklungsumgebung mit .NET Core und Docker konfiguriert haben. Sie können die Installation von .NET Core vermeiden und nur Docker installieren und den Befehlszeilenparameter `--in-sandbox` verwenden (oder die Kurzform `-s`). Beispiel:

- `build --in-sandbox`: Führt die Buildaufgabe innerhalb eines Docker-Containers aus (Kurzform `build -s`).

- `compile --in-sandbox`: Führt die Kompilierungsaufgabe innerhalb eines Docker-Containers aus (Kurzform `compile -s`).

- `run --in-sandbox`: Startet den Dienst innerhalb eines Docker-Containers (Kurzform `run -s`).

Die für die Sandbox verwendeten Docker-Images werden [hier](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet) in Docker Hub gehostet.

## <a name="package-the-application-to-a-docker-image"></a>Verpacken der Anwendung zu einem Docker-Image

Der Ordner `scripts` enthält einen Docker-Unterordner mit den Dateien, die erforderlich sind, um den Dienst in ein Docker-Image zu packen:

- `Dockerfile`: Spezifikationen für Docker-Images.
- `build`: Erstellen Sie einen Docker-Container, und speichern Sie das Image in der lokalen Registrierung.
- `run`: Führen Sie den Docker-Container über das in der lokalen Registrierung gespeicherte Image aus.
- `content`: Ein Ordner mit Dateien, die in das Image kopiert wurden, einschließlich des Einstiegspunktskripts.

## <a name="azure-iot-hub-setup"></a>Einrichten von Azure IoT Hub

Um den Microservice zu verwenden, richten Sie Ihr Azure IoT Hub für Entwicklungs- und Integrationstests ein.

Das Projekt umfasst einige Bash-Skripts, die Ihnen bei diesem Setup helfen:

- Neuen IoT Hub erstellen: `./scripts/iothub/create-hub.sh`

- Vorhandene Hubs auflisten: `./scripts/iothub/list-hubs.sh`

- IoT Hub-Details anzeigen (z. B. Schlüssel): `./scripts/iothub/show-hub.sh`

Falls Sie über mehrere Azure-Abonnements verfügen:

- Abonnementliste anzeigen: `./scripts/iothub/list-subscriptions.sh`

- Aktuelles Abonnement ändern: `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Einrichten der Entwicklung

### <a name="net-setup"></a>Einrichten von .NET

Der Projektworkflow wird über [.NET Core](https://dotnet.github.io) 1.x verwaltet, das Sie in Ihrer Umgebung installieren müssen, damit Sie alle Skripts ausführen und sicherstellen können, dass Ihre integrierte Entwicklungsumgebung wie erwartet funktioniert.

Wir stellen auch eine [Java-Version](https://github.com/Azure/iot-opc-gds-service-dotnet) dieses Projekts und andere Azure IoT PCS-Komponenten bereit.

### <a name="ide"></a>IDE

Hier sind einige der integrierten Entwicklungsumgebungen, mit denen Sie an Azure IoT PCS arbeiten können:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio für Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Einrichten von Git

Das Projekt umfasst einen Git-Hook, um einige Prüfungen zu automatisieren, bevor eine Codeänderung akzeptiert wird. Sie können die Tests manuell ausführen oder von der CI-Plattform ausführen lassen. Wir verwenden den folgenden Git-Hook, um alle Tests automatisch auszuführen, bevor Codeänderungen an GitHub gesendet werden, und um den Entwicklungsworkflow zu beschleunigen.

Wenn Sie den Hook zu einem beliebigen Zeitpunkt entfernen möchten, löschen Sie einfach die unter `.git/hooks` installierte Datei. Sie können den Hook vor dem Commit auch mit der Option `--no-verify` umgehen.

#### <a name="pre-commit-hook-with-sandbox"></a>Hook vor dem Commit mit Sandbox

Öffnen Sie zum Einrichten der enthaltenen Hooks eine Windows-/Linux-/MacOS-Konsole, und führen Sie Folgendes aus:

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

Mit dieser Konfiguration überprüft Git beim Einchecken von Dateien, ob die Anwendung alle Tests besteht, führt den Build und die Tests in einem Docker-Container aus, der mit allen Entwicklungsanforderungen konfiguriert ist.

#### <a name="pre-commit-hook-without-sandbox"></a>Hook vor dem Commit ohne Sandbox

> [!NOTE] 
> Der Hook ohne Sandbox erfordert [.NET Core](https://dotnet.github.io) im Systempfad.

Öffnen Sie zum Einrichten der enthaltenen Hooks eine Windows-/Linux-/MacOS-Konsole, und führen Sie Folgendes aus:

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
Mit dieser Konfiguration überprüft Git beim Einchecken von Dateien, ob die Anwendung alle Tests besteht, führt den Build und die Tests auf Ihrer Arbeitsstation mithilfe der in Ihrem Betriebssystem installierten Tools aus.

Leitfaden zum Projektcodestil:

- Wo dies angebracht ist, ist die Länge der Zeilen auf maximal 80 Zeichen begrenzt, um Codeüberprüfungen und Befehlszeilen-Editoren zu unterstützen.

- Codeblockeinzug mit vier Leerzeichen. Das Tabulatorzeichen sollte vermieden werden.

- Textdateien verwenden das Zeilenendeformat (LF) von Unix.

- Die Abhängigkeitsinjektion wird mit [Autofac](https://autofac.org) verwaltet.

- Die Felder der Webdienst-APIs werden mit Ausnahme von Metadaten die wortinterne gemischte Groß-/Kleinschreibung.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie OPC Vault von Grund auf bereitstellen, lautet unser Vorschlag für den nächsten Schritt:

> [!div class="nextstepaction"]
> [Bereitstellen von OPC Twin von Grund auf](howto-opc-twin-deploy-modules.md)