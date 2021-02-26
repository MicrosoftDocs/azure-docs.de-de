---
title: Vertrauliche Anwendungen als Azure IoT Edge-Module
description: Mithilfe des Open Enclave SDK und über die API können Sie vertrauliche Anwendungen schreiben und als IoT Edge-Module für Confidential Computing bereitstellen.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: e63397176cae87178cfc5cc343aeed6f76099be2
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956432"
---
# <a name="confidential-computing-at-the-edge"></a>Confidential Computing im Edge-Bereich

Azure IoT Edge unterstützt vertrauliche Anwendungen, die innerhalb sicherer Enklaven auf dem Gerät ausgeführt werden. Verschlüsselung bietet Sicherheit für Daten während der Übertragung oder im Ruhezustand, aber Enklaven bieten Sicherheit für Daten und Workloads während der Verwendung. IoT Edge unterstützt Open Enclave als Standard für die Entwicklung vertraulicher Anwendungen.

Sicherheit war immer ein wichtiger Schwerpunkt des Internets der Dinge (Internet of Things, IoT), weil IoT-Geräte oft „draußen in der Welt“ sind und nicht innerhalb einer privaten Einrichtung gesichert werden. Durch dieses Risiko können Geräte manipuliert und gefälscht werden, weil sie für böswillige Benutzer physisch zugänglich sind. Bei IoT Edge-Geräten ist sogar noch mehr Vertrauenswürdigkeit und Integrität erforderlich, weil sie die Ausführung von vertraulichen Workloads im Edge-Bereich ermöglichen. Im Gegensatz zu gängigen Sensoren und Aktoren machen diese intelligenten Edge-Geräte möglicherweise vertrauliche Workloads verfügbar, die zuvor nur in geschützten Cloudumgebungen oder lokalen Umgebungen ausgeführt wurden.

Der Sicherheits-Manager [IoT Edge Security Manager](iot-edge-security-manager.md) ist ein Teil der Confidential Computing-Ressourcen. Der Sicherheits-Manager verwendet ein Hardwaresicherheitsmodul (HSM), um die Identitätsworkloads und laufenden Prozesse eines IoT Edge-Geräts zu schützen.

Ein weiterer Aspekt von Confidential Computing besteht im Schutz der Daten, die im Edge-Bereich verwendet werden. Eine *vertrauenswürdige Ausführungsumgebung* (Trusted Execution Environment, TEE) ist eine sichere, isolierte Umgebung auf einem Prozessor und wird manchmal als *Enklave* bezeichnet. Eine *vertrauliche Anwendung* ist eine Anwendung, die in einer Enklave ausgeführt wird. Aufgrund der Art von Enklaven werden vertrauliche Anwendungen vor anderen Apps geschützt, die im Hauptprozessor oder in der TEE ausgeführt werden.

## <a name="confidential-applications-on-iot-edge"></a>Vertrauliche Anwendungen in IoT Edge

Vertrauliche Anwendungen werden während der Übertragung und im Ruhezustand verschlüsselt. Nur für die Ausführung in einer vertrauenswürdigen Ausführungsumgebung werden sie entschlüsselt. Dieser Standard gilt für vertrauliche Anwendungen, die als IoT Edge-Module bereitgestellt werden.

Der Entwickler erstellt die vertrauliche Anwendung und packt sie als IoT Edge-Modul. Die Anwendung wird vor dem Pushen in die Containerregistrierung verschlüsselt. Die Anwendung bleibt während des gesamten IoT Edge-Bereitstellungsprozesses so lange verschlüsselt, bis das Modul auf dem IoT Edge-Gerät gestartet wird. Sobald sich die vertrauliche Anwendung in der TEE des Geräts befindet, wird sie entschlüsselt und kann mit der Ausführung beginnen.

![Diagramm: Vertrauliche Anwendungen werden in IoT Edge-Modulen verschlüsselt, bis sie in der sicheren Enklave bereitgestellt werden.](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

Vertrauliche Anwendungen in IoT Edge sind eine logische Erweiterung von [Azure Confidential Computing](../confidential-computing/overview.md). Workloads, die innerhalb sicherer Enklaven in der Cloud ausgeführt werden, können auch für die Ausführung innerhalb sicherer Enklaven im Edge-Bereich bereitgestellt werden.

## <a name="open-enclave"></a>Open Enclave

Das [Open Enclave SDK](https://openenclave.io/sdk/) ist ein Open-Source-Projekt, mit dessen Hilfe Entwickler vertrauliche Anwendungen für mehrere Plattformen und Umgebungen erstellen können. Das Open Enclave SDK funktioniert innerhalb der vertrauenswürdigen Ausführungsumgebung eines Geräts, während die Open Enclave-API als Schnittstelle zwischen der TEE und der Nicht-TEE-Verarbeitungsumgebung fungiert.

Open Enclave unterstützt mehrere Hardwareplattformen. Die IoT Edge-Unterstützung für Enklaven erfordert zurzeit das Open Portable TEE-Betriebssystem (OP-TEE OS). Weitere Informationen finden Sie unter [Open Enclave SDK für OP-TEE OS](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

Das Open Enclave-Repository enthält außerdem Beispiele, die Entwicklern den Einstieg erleichtern sollen. Wenn Sie weitere Informationen benötigen, wählen Sie einen der folgenden Einführungsartikel aus:

* [Entwickeln von Open Enclave SDK-Beispielen unter Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Entwickeln von Open Enclave SDK-Beispielen unter Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Hardware

Zurzeit ist [TrustBox von Scalys](https://scalys.com/trustbox-industrial/) das einzige Gerät, das mit Herstellerserviceverträgen für die Bereitstellung vertraulicher Anwendungen als IoT Edge-Module unterstützt wird. Die TrustBox basiert auf den Geräten TrustBox Edge und TrustBox EdgeXL, die beide mit dem Open Enclave SDK und Azure IoT Edge vorab geladen wurden.

Weitere Informationen finden Sie unter [Getting started with Open Enclave for the Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started) (Erste Schritte mit Open Enclave für die TrustBox von Scalys).

## <a name="develop-and-deploy"></a>Entwickeln und Bereitstellen

Wenn Sie zum Entwickeln und Bereitstellen Ihrer vertraulichen Anwendung bereit sind, kann Ihnen die [Microsoft Open Enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave)-Erweiterung für Visual Studio Code dabei helfen. Sie können entweder Linux oder Windows als Ihren Entwicklungscomputer zum Entwickeln von Modulen für die TrustBox verwenden.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich, wie Sie mit der Entwicklung vertraulicher Anwendungen als IoT Edge-Module mit der [Open Enclave-Erweiterung für Visual Studio Code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension) beginnen können.
