---
title: Azure IoT Edge-Entwicklungsumgebung | Microsoft-Dokumentation
description: Erfahren Sie Informationen über die unterstützten Systeme und die Entwicklungstools von Erstanbietern, die Sie beim Erstellen von IoT Edge-Modulen unterstützen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4064acd6d2857ebc00a38ea7f6ff5d5e30bbd699
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041381"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Vorbereiten Ihrer Entwicklungs- und Testumgebung für IoT Edge

Azure IoT Edge verschiebt Ihre vorhandene Geschäftslogik auf Geräte, die an der Grenze (Edge) arbeiten. Um Ihre Anwendungen und Workloads für die Ausführung als [IoT Edge-Module](iot-edge-modules.md) vorzubereiten, müssen Sie sie als Container erstellen. Dieser Artikel enthält Anleitungen zur Konfiguration Ihrer Entwicklungsumgebung, damit Sie erfolgreich eine IoT Edge-Lösung erstellen können. Nachdem Sie Ihre Entwicklungsumgebung eingerichtet haben, können Sie lernen, wie Sie beim [Entwickeln Ihrer eigenen IoT Edge-Module](module-development.md) vorgehen.

Für jede IoT Edge-Lösung müssen immer mindestens zwei Computer in Betracht gezogen werden. Einer ist das eigentliche IoT Edge-Gerät (oder die Geräte), das das IoT Edge-Modul ausführt. Der andere ist der Entwicklungscomputer, den Sie zum Erstellen, Testen und Bereitstellen von Modulen verwenden. Dieser Artikel konzentriert sich in der Hauptsache auf den Entwicklungscomputer. Für Testzwecke kann beides der gleiche Computer sein. Sie können IoT Edge auf Ihrem Entwicklungscomputer ausführen und Module auf ihm bereitstellen.

## <a name="operating-system"></a>Betriebssystem

Azure IoT Edge kann auf einem bestimmten Satz [unterstützter Betriebssysteme](support.md) ausgeführt werden. Zum Zweck der Entwicklung für IoT Edge können die meisten Betriebssysteme verwendet werden, die eine Containerengine ausführen können. Die Containerengine ist eine Anforderung auf dem Entwicklungscomputer, um Ihre Module als Container zu erstellen und sie per Push in eine Containerregistrierung hochzuladen.

Wenn auf Ihrem Entwicklungscomputer Azure IoT Edge nicht ausgeführt werden kann, fahren Sie fort, diesen Artikel zu lesen, um mehr über [Testtools](#testing-tools) zu erfahren, die Sie beim lokalen Testen und Debuggen unterstützen.

Das Betriebssystem Ihres Entwicklungscomputers muss nicht mit dem Betriebssystem Ihres IoT Edge-Geräts übereinstimmen. Allerdings muss das Containerbetriebssystem auf dem Entwicklungscomputer und dem IoT Edge-Gerät gleich sein. Beispielsweise können Sie Module auf einem Windows-Computer entwickeln und sie auf einem Linux-Gerät bereitstellen. Auf dem Windows-Computer müssen Linux-Container ausgeführt werden, um Module für das Linux-Gerät zu erstellen.

## <a name="container-engine"></a>Containerengine

Das grundlegende Konzept von IoT Edge besteht darin, dass Sie Ihre Geschäfts- und Cloudlogik remote auf Geräten bereitstellen können, indem Sie sie in Containern verpacken. Um Container zu erstellen, benötigen Sie eine Containerengine auf Ihrem Entwicklungscomputer.

Die einzige unterstützte Containerengine für IoT Edge-Geräte in der Produktion ist Moby. Allerdings kann jede Containerengine, die mit der Open Container Initiative kompatibel ist, wie etwa Docker, zum Erstellen von IoT Edge-Modulimages verwendet werden.

## <a name="development-tools"></a>Entwicklungstools

Sowohl Visual Studio als auch Visual Studio Code verfügen über Add-On-Erweiterungen, um die Entwicklung von IoT Edge-Lösungen zu unterstützen. Diese Erweiterungen stellen sprachspezifische Vorlagen bereit, die Sie beim Erstellen und Bereitstellen neuer IoT Edge-Szenarien unterstützen. Die Azure IoT Edge-Erweiterungen für Visual Studio und Visual Studio Code helfen Ihnen beim Programmieren, Erstellen, Bereitstellen und Debuggen Ihrer IoT Edge-Lösungen. Sie können eine gesamte IoT Edge-Projektmappe erstellen, die mehrere Module enthält, und die Erweiterungen aktualisieren automatisch eine Vorlage für ein Bereitstellungsmanifest mit jedem neu hinzugefügten Modul. Mit den Erweiterungen können Sie außerdem Ihre IoT-Geräte aus Visual Studio oder Visual Studio Code verwalten. Stellen Sie Module auf einem Gerät bereit, überwachen Sie ihren Status, und zeigen Sie die bei IoT Hub eingehenden Nachrichten an. Beide Erweiterungen verwenden das [IoT EdgeHub Dev Tool](#iot-edgehub-dev-tool), um ebenso lokale Ausführung und lokales Debuggen auf Ihrem Entwicklungscomputer zu ermöglichen.

Wenn Sie es vorziehen, mit anderen Editoren oder an der Befehlszeile zu entwickeln, stellt das Azure IoT Edge Dev Tool Befehle zur Verfügung, mit denen Sie an der Befehlszeile entwickeln und testen können. Sie können neue IoT Edge-Szenarien erstellen, Modulimages generieren, Module in einem Simulator ausführen und die an IoT Hub gesendeten Nachrichten überwachen.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-Erweiterung

Die Azure IoT Edge-Erweiterung für Visual Studio Code stellt Vorlagen für IoT Edge-Module zur Verfügung, die auf Programmiersprachen aufbauen, darunter C, C#, Java, Node.js und Python sowie Azure Functions in C#.

Weitere Informationen und Downloadmöglichkeiten finden Sie unter [Azure IoT-Tools für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Über die IoT Edge-Erweiterungen hinaus finden Sie es möglicherweise nützlich, weitere Erweiterungen für die Entwicklung zu installieren. Beispielsweise können Sie [Docker Support for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) verwenden, um Ihre Images, Container und Registrierungen zu verwalten. Darüber hinaus verfügen alle wichtigen unterstützten Sprachen über Erweiterungen für Visual Studio Code, die bei der Entwicklung von Modulen nützlich sein können.

#### <a name="prerequisites"></a>Voraussetzungen

Für die Modulvorlagen für einige Sprachen und Dienste gelten Voraussetzungen, die für die Erstellung von Projektordnern auf Ihrem Entwicklungscomputer mit Visual Studio Code erforderlich sind.

| Modulvorlage | Voraussetzung |
| --------------- | ------------ |
| Azure-Funktionen | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git-Client](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [Festlegen der JAVA_HOME-Umgebungsvariable](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge-Node.js-Modulgenerator](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git-Client](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Erweiterung für Visual Studio 2017/2019

Die Azure IoT Edge-Tools für Visual Studio bieten eine auf C# und C basierende IoT Edge-Modulvorlage.

Weitere Informationen und Downloadmöglichkeiten finden Sie unter [Azure IoT Edge Tools (Preview) for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) (Azure IoT Edge-Tools (Vorschauversion) für Visual Studio 2017) bzw. [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) (Azure IoT Edge-Tools für Visual Studio 2019).

### <a name="iot-edge-dev-tool"></a>IoT Edge Dev Tool

Das Azure IoT Edge Dev Tool vereinfacht die IoT Edge-Entwicklung mit Funktionen für die Befehlszeile. Dieses Tool stellt CLI-Befehle zum Entwickeln, Debuggen und Testen von Modulen bereit. Das IoT Edge Dev Tool funktioniert mit Ihrem Entwicklungssystem, gleich, ob Sie die Abhängigkeiten manuell auf Ihrem System installiert haben oder den IoT Edge Dev Container verwenden.

Weitere Informationen, auch zum Einstieg, finden Sie im [IoT Edge Dev Tool-Wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Testtools

Es sind verschiedene Testtools verfügbar, die Ihnen die Simulation von IoT Edge-Geräten oder das effizientere Debuggen von Modulen erleichtern. Die folgende Tabelle zeigt einen allgemeinen Vergleich der Tools; jedes Tool wird anschließend in einzelnen Abschnitten genauer beschrieben.

Für Produktionsbereitstellungen wird nur die IoT Edge-Runtime unterstützt, mithilfe der folgenden Tools können Sie aber IoT Edge-Geräte für Entwicklungs- und Testzwecke simulieren oder auf einfache Weise erstellen. Diese Tools schließen sich nicht gegenseitig aus sondern können vielmehr zu einer vollständigen Entwicklungsumgebung kombiniert werden.

| Tool | Andere Bezeichnung | Unterstützte Plattformen | Am besten geeignet für: |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub Dev Tool  | iotedgehubdev | Windows, Linux, macOS | Simulieren eines Geräts zum Debuggen von Modulen. |
| IoT Edge Dev-Container | iotedgedev | Windows, Linux, macOS | Entwickeln ohne die Installation von Abhängigkeiten. |
| IoT Edge Runtime in einem Container | iotedgec | Windows, Linux, macOS, ARM | Testen auf einem Gerät, das die Runtime möglicherweise nicht unterstützt. |
| IoT Edge-Gerätecontainer | toolboc/azure-iot-edge-device-container | Windows, Linux, macOS, ARM | Testen in einem Szenario mit vielen IoT Edge-Geräten in großem Maßstab. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub Dev Tool

Das Azure IoT EdgeHub Dev Tool stellt eine lokale Entwicklungs- und Debugumgebung zur Verfügung. Das Tool ermöglicht das Starten von IoT Edge-Modulen ohne die IoT Edge-Runtime, so dass Sie IoT Edge-Module und -Projektmappen lokal erstellen, entwickeln, testen und debuggen können. Sie brauchen keine Images per Push in eine Containerregistrierung hochzuladen und auf einem Gerät bereitzustellen, um sie zu testen.

Das IoT EdgeHub Dev Tool wurde für die gemeinsame Verwendung mit den Visual Studio- und Visual Studio Code-Erweiterungen sowie mit dem IoT Edge Dev Tool entwickelt. Es unterstützt sowohl die innere Schleife der Entwicklung als auch die äußere Schleife mit Testing und ist auch in die DevOps-Tools integriert.

Weitere Informationen, auch zur Installation, finden Sie unter [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>IoT Edge Dev-Container

Der Azure IoT Edge Dev-Container ist ein Docker-Container, der alle Abhängigkeiten enthält, die Sie für die IoT Edge-Entwicklung benötigen. Dieser Container macht den Einstieg leicht, ganz gleich, in welcher Sprache Sie entwickeln möchten, einschließlich C#, Python, Node.js und Java. Sie brauchen lediglich eine Containerengine zu installieren, wie Docker oder Moby, um die Inhalte auf Ihren Entwicklungscomputer herunterzuladen.

Weitere Informationen finden Sie unter [Azure IoT Edge Dev-Container](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge Runtime in einem Container

Die IoT Edge-Runtime in einem Container stellt eine vollständige Laufzeitumgebung zur Verfügung, die Ihre Verbindungszeichenfolge als Umgebungsvariable annimmt. Dieser Container ermöglicht Ihnen das Testen von IoT Edge-Modulen und -Szenarien auf einem System, das die Runtimes vielleicht nicht nativ unterstützt, wie z. B. macOS. Alle von Ihnen bereitgestellten Module werden außerhalb des Runtimecontainers gestartet. Wenn Sie möchten, dass die Runtime und eventuell bereitgestellte Module koexistent im gleichen Container vorhanden sind, erwägen Sie stattdessen den IoT Edge-Gerätecontainer.

Weitere Informationen finden Sie unter [Ausführen von Azure IoT Edge in einem Container](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>IoT Edge-Gerätecontainer

Der IoT Edge-Gerätecontainer ist ein vollständiges IoT Edge-Gerät, das für den Start auf einem beliebigen Computer mit einer Containerengine bereit ist. Der Gerätecontainer enthält die IoT Edge-Runtime und eine Containerengine. Jede Instanz des Containers bildet dabei ein funktionell völlig eigenständiges IoT Edge-Gerät. Der Gerätecontainer unterstützt das Remotedebuggen von Modulen, sofern es eine Netzwerkroute zu den Modulen gibt. Der Gerätecontainer eignet sich gut, um schnell eine große Anzahl von IoT Edge-Geräten zum Testen von bedarfsorientierten Szenarien oder Azure Pipelines zu erstellen. Er unterstützt darüber hinaus die Bereitstellung auf Kubernetes mithilfe von Helm.

Weitere Informationen finden Sie unter [Azure IoT Edge-Gerätecontainer](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>DevOps-Tools

Wenn Sie für die Entwicklung von Lösungen in großem Maßstab für umfangreiche Produktionsszenarien bereit sind, nutzen Sie moderne DevOps-Prinzipien, einschließlich Automation, Überwachung und optimierten Softwareentwicklungsprozessen. IoT Edge verfügt über Erweiterungen, die DevOps-Tools unterstützen, darunter Azure DevOps, Azure DevOps Projects und Jenkins. Wenn Sie eine vorhandene Pipeline anpassen oder ein anderes DevOps-Tool, wie CircleCI oder TravisCI verwenden möchten, ist das mit den im IoT Edge Dev Tool integrierten Befehlszeilenfunktionen möglich.

Weitere Informationen, Anleitungen und Beispiele finden Sie auf den folgenden Seiten:

* [Continuous Integration und Continuous Deployment für Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [Erstellen einer CI/CD-Pipeline für IoT Edge mit Azure DevOps Starter](how-to-devops-starter.md)
* [Azure IoT Edge-Jenkins-Plug-In](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge-DevOps-GitHub-Repository](https://github.com/toolboc/IoTEdge-DevOps)