---
title: Ausführen einer selbstgehosteten Integration Runtime in einem Windows-Container
description: Erfahren Sie mehr über das Ausführen einer selbstgehosteten Integration Runtime in einem Windows-Container.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 7035da173102e59aec9c643381bad701d6facf38
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634537"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Ausführen einer selbstgehosteten Integration Runtime in einem Windows-Container

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird erläutert, wie Sie eine selbstgehostete Integration Runtime in einem Windows-Container ausführen.
Azure Data Factory stellt die offizielle Windows-Containerunterstützung für die selbstgehostete Integration Runtime bereit. Sie können den Quellcode des Docker-Builds herunterladen und den Prozess der Erstellung und Ausführung in Ihrer eigenen kontinuierlichen Lieferpipeline kombinieren. 

## <a name="prerequisites"></a>Voraussetzungen 
- [Anforderungen von Windows-Containern](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker, Version 2.3 und höher 
- Selbstgehostete Integration Runtime, Version 4.11.7512.1 und höher 
## <a name="get-started"></a>Erste Schritte 
1.  Installieren von Docker und Aktivieren von Windows-Containern 
2.  Herunterladen des Quellcodes von https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Herunterladen der neueste SHIR-Version in den Ordner „SHIR“ 
4.  Öffnen des Ordners in der Shell: 
```console
cd "yourFolderPath"
```

5.  Erstellen des Windows Docker-Images: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Ausführen des Docker-Containers: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY ist für diesen Befehl obligatorisch. NODE_NAME, ENABLE_HA und HA_PORT sind optional. Wenn Sie den Wert nicht festlegen, verwendet der Befehl die Standardwerte. Der Standardwert ENABLE_HA ist „false“, und HA_PORT ist 8060.

## <a name="container-health-check"></a>Überprüfung der Containerintegrität 
Nach einer Startdauer von 120 Sekunden wird regelmäßig alle 30 Sekunden eine Integritätsüberprüfung ausgeführt. Der IR-Integritätsstatus wird dem Containermodul bereitgestellt. 

## <a name="limitations"></a>Einschränkungen
Zurzeit werden die folgenden Features bei der Ausführung der selbstgehosteten Integration Runtime im Windows-Container nicht unterstützt:
- HTTP-Proxy 
- Verschlüsselte Knoten-Knoten-Kommunikation mit TLS/SSL-Zertifikat 
- Generieren und Importieren einer Sicherung 
- Daemondienst 
- Automatische Aktualisierung 

### <a name="next-steps"></a>Nächste Schritte
- Lesen Sie [Integration Runtime-Konzepte in Azure Data Factory](./concepts-integration-runtime.md).
- Informieren Sie sich über das [Erstellen einer selbstgehosteten Integration Runtime im Azure-Portal](./create-self-hosted-integration-runtime.md).