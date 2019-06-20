---
title: Aktivieren von CI/CD mit dem Jenkins-Plug-In – Azure IoT Edge | Microsoft-Dokumentation
description: Die Azure IoT Edge-Erweiterung für Jenkins ermöglicht es Ihnen, IoT Edge-Entwicklungs- und Bereitstellungsaufgaben in Ihre vorhandene DevOps-Lösung zu integrieren.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 173e6ff91acd2ad28d7203b2b5db65e0ee0ecc43
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126340"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrieren von Azure IoT Edge in Jenkins-Pipelines

Azure IoT Edge bietet integrierte Unterstützung für Azure DevOps und Azure DevOps Projects, stellt aber auch ein Plug-In zur Erweiterung der DevOps-Funktionalität auf Jenkins bereit. [Jenkins](https://jenkins.io/) ist ein Open Source-Automatisierungsserver, der Plug-Ins verwendet, um zahlreiche Arten von Entwicklungs- und Bereitstellungsprojekten zu unterstützen, darunter auch IoT Edge. 

Das Azure IoT Edge-Plug-In für Jenkins konzentriert sich auf Continuous Integration (CI) und Continuous Development (CD). Sie können eine Build- und Pushpipeline erstellen, mit der Module erstellt und zugehörige Containerimages mithilfe von Push in Ihre Containerregistrierung übertragen werden. Anschließend erstellen Sie eine Releasepipeline, die Module auf Ihren IoT Edge-Geräten bereitstellt. 

Bevor Sie mit der Verwendung des IoT Edge-Plug-Ins für Jenkins beginnen können, benötigen Sie einen IoT-Hub in Azure und eine Containerregistrierung zum Speichern Ihrer Containerimages. Erteilen Sie Jenkins mithilfe eines Azure-Dienstprinzipals Berechtigungen als Mitwirkender für Ihren IoT-Hub, damit das Plug-In Bereitstellungen für Ihre IoT Edge-Geräte erstellen kann. 

Wenn Sie bereit für den Einstieg sind, sehen Sie sich die ausführlichen Installations- und Verwendungsinformationen für das [Azure IoT Edge-Plug-In für Jenkins](https://plugins.jenkins.io/azure-iot-edge) an.