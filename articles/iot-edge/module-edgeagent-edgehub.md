---
title: Referenz zu gewünschten EdgeAgent- und EdgeHub-Eigenschaften – Azure IoT Edge | Microsoft-Dokumentation
description: Enthält die spezifischen Eigenschaften und die dazugehörigen Werte für die Modulzwillinge edgeAgent und edgeHub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b6eb0c5b0d52bba3d34c9853a73b1f3e07b112a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61322706"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Eigenschaften der Modulzwillinge von IoT Edge-Agent und IoT Edge-Hub

Der IoT Edge-Agent und der IoT Edge-Hub sind die beiden Module, aus denen sich die IoT Edge-Runtime zusammensetzt. Weitere Informationen dazu, welche Aufgaben von einem Modul jeweils ausgeführt werden, finden Sie unter [Grundlegendes zur Azure IoT Edge-Laufzeit und ihrer Architektur](iot-edge-runtime.md). 

Dieser Artikel enthält die gewünschten Eigenschaften und gemeldeten Eigenschaften der Laufzeitmodulzwillinge. Weitere Informationen zur Bereitstellung von Modulen auf IoT Edge-Geräten finden Sie unter [Bereitstellung und Überwachung](module-deployment-monitoring.md).

## <a name="edgeagent-desired-properties"></a>Gewünschte EdgeAgent-Eigenschaften

Der Modulzwilling für den IoT Edge-Agent heißt `$edgeAgent` und koordiniert die Kommunikationsvorgänge zwischen dem auf einem Gerät ausgeführten IoT Edge-Agent und IoT Hub. Die gewünschten Eigenschaften werden festgelegt, wenn ein Bereitstellungsmanifest auf einem bestimmten Gerät im Zuge einer Einzelgeräte- oder einer umfassenden Bereitstellung angewendet wird. 

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| -------- | ----------- | -------- |
| Schemaversion | Muss „1.0“ sein. | Ja |
| runtime.type | Muss „Docker“ sein. | Ja |
| runtime.settings.minDockerVersion | Legen Sie hier die für dieses Bereitstellungsmanifest mindestens erforderliche Docker-Version fest. | Ja |
| runtime.settings.loggingOptions | In eine Zeichenfolge umgewandelter JSON-Code mit den Protokollierungsoptionen für den IoT Edge-Agent-Container. [Docker-Protokollierungsoptionen](https://docs.docker.com/engine/admin/logging/overview/) | Nein |
| runtime.settings.registryCredentials<br>.{registryId}.username | Der Benutzername der Containerregistrierung. Für Azure Container Registry entspricht der Benutzername in der Regel dem Namen der Registrierung.<br><br> Registrierungsanmeldeinformationen werden für jedes nicht öffentliche Modulimage benötigt. | Nein |
| runtime.settings.registryCredentials<br>.{registryId}.password | Das Kennwort der Containerregistrierung. | Nein |
| runtime.settings.registryCredentials<br>.{registryId}.address | Die Adresse der Containerregistrierung. Für Azure Container Registry lautet die Adresse in der Regel *{Registryname}.azurecr.io*. | Nein |  
| systemModules.edgeAgent.type | Muss „Docker“ sein. | Ja |
| systemModules.edgeAgent.settings.image | Der URI des IoT Edge-Agent-Images. Der IoT Edge-Agent kann sich derzeit nicht selbst aktualisieren. | Ja |
| systemModules.edgeAgent.settings<br>.createOptions | In eine Zeichenfolge umgewandelter JSON-Code, der die Optionen für die Erstellung des IoT Edge-Agent-Containers enthält. [Docker-Erstellungsoptionen](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nein |
| systemModules.edgeAgent.configuration.id | Die ID der Bereitstellung, von der dieses Modul bereitgestellt wurde. | IoT Hub legt diese Eigenschaft fest, wenn das Manifest mithilfe einer Bereitstellung angewandt wird. Nicht Teil eines Bereitstellungsmanifests. |
| systemModules.edgeHub.type | Muss „Docker“ sein. | Ja |
| systemModules.edgeHub.type | Muss „running“ sein. | Ja |
| systemModules.edgeHub.restartPolicy | Muss „always“ sein. | Ja |
| systemModules.edgeHub.settings.image | Der URI des Images des IoT Edge-Hubs. | Ja |
| systemModules.edgeHub.settings<br>.createOptions | In eine Zeichenfolge umgewandelter JSON-Code mit den Optionen für die Erstellung des IoT Edge-Hubcontainers. [Docker-Erstellungsoptionen](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nein |
| systemModules.edgeHub.configuration.id | Die ID der Bereitstellung, von der dieses Modul bereitgestellt wurde. | IoT Hub legt diese Eigenschaft fest, wenn das Manifest mithilfe einer Bereitstellung angewandt wird. Nicht Teil eines Bereitstellungsmanifests. |
| modules.{moduleId}.version | Benutzerdefinierte Zeichenfolge, die die Version des Moduls darstellt. | Ja |
| modules.{moduleId}.type | Muss „Docker“ sein. | Ja |
| modules.{moduleId}.status | {"running" \| "stopped"} | Ja |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | Ja |
| modules.{moduleId}.settings.image | URI des Modulimage. | Ja |
| modules.{moduleId}.settings.createOptions | In eine Zeichenfolge umgewandelter JSON-Code mit den Optionen für die Erstellung des Modulcontainers. [Docker-Erstellungsoptionen](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nein |
| modules.{moduleId}.configuration.id | Die ID der Bereitstellung, von der dieses Modul bereitgestellt wurde. | IoT Hub legt diese Eigenschaft fest, wenn das Manifest mithilfe einer Bereitstellung angewandt wird. Nicht Teil eines Bereitstellungsmanifests. |

## <a name="edgeagent-reported-properties"></a>Gemeldete EdgeAgent-Eigenschaften

Zu den vom IoT Edge-Agent gemeldeten Eigenschaften gehören drei Hauptinformationen:

1. Status der Anwendung der zuletzt erkannten gewünschten Eigenschaften
2. Status der Module, die nach Angaben des IoT Edge-Agents derzeit auf dem Gerät ausgeführt werden
3. Kopie der gewünschten Eigenschaften, die derzeit auf dem Gerät ausgeführt werden

Diese letzte Information ist nützlich für den Fall, dass die letzten gewünschten Eigenschaften nicht erfolgreich von der Runtime angewendet wurden und das Gerät demzufolge noch ein vorheriges Bereitstellungsmanifest ausführt.

> [!NOTE]
> Die gemeldeten Eigenschaften des IoT Edge-Agents sind hilfreich, da sie mit der [IoT Hub-Abfragesprache](../iot-hub/iot-hub-devguide-query-language.md) abgefragt werden können, um den Status der Bereitstellungen großflächig zu untersuchen. Weitere Informationen zur Verwendung der IoT Edge-Agent-Eigenschaften für den Status finden Sie unter [Grundlegendes zu IoT Edge-Bereitstellungen für einzelne Geräte oder nach Bedarf](module-deployment-monitoring.md).

In der folgenden Tabelle sind die aus den gewünschten Eigenschaften kopierten Informationen nicht enthalten.

| Eigenschaft | BESCHREIBUNG |
| -------- | ----------- |
| lastDesiredVersion | Dieser Integerwert bezeichnet die letzte vom IoT Edge-Agent verarbeitete Version der gewünschten Eigenschaften. |
| lastDesiredStatus.code | Dieser Statuscode nennt die letzten gewünschten Eigenschaften, die der IoT Edge-Agent erkannt hat. Zulässige Werte: `200`: Erfolgreich, `400`: Ungültige Konfiguration, `412`: Ungültige Schemaversion, `417`: Die gewünschten Eigenschaften sind leer, `500`: Fehler |
| lastDesiredStatus.description | Textbeschreibung des Status |
| deviceHealth | `healthy`, wenn der Runtimestatus aller Module entweder `running` oder `stopped` ist, andernfalls `unhealthy` |
| configurationHealth.{deploymentId}.health | `healthy`, wenn der von der Bereitstellung {deploymentId} festgelegte Runtimestatus aller Module `running` oder `stopped` ist, andernfalls `unhealthy` |
| runtime.platform.OS | Meldung des auf dem Gerät ausgeführten Betriebssystems |
| runtime.platform.architecture | Meldung der CPU-Architektur auf dem Gerät |
| systemModules.edgeAgent.runtimeStatus | Gemeldeter Status des IoT Edge-Agents: {„running“ \| „unhealthy“} |
| systemModules.edgeAgent.statusDescription | Textbeschreibung des gemeldeten Status des IoT Edge-Agents. |
| systemModules.edgeHub.runtimeStatus | Aktueller Status des IoT Edge-Hubs: {„running“ \| „stopped“ \| „failed“ \| „backoff“ \| „unhealthy“} |
| systemModules.edgeHub.statusDescription | Textbeschreibung des aktuellen Status des IoT Edge-Hubs, falls „unhealthy“ |
| systemModules.edgeHub.exitCode | Der vom IoT Edge-Hubcontainer gemeldete Exitcode beim Beenden |
| systemModules.edgeHub.startTimeUtc | Zeitpunkt des letzten Starts des IoT Edge-Hubs |
| systemModules.edgeHub.lastExitTimeUtc | Zeitpunkt der letzten Beendigung des IoT Edge-Hubs |
| systemModules.edgeHub.lastRestartTimeUtc | Zeitpunkt des letzten Neustarts des IoT Edge-Hubs |
| systemModules.edgeHub.lastRestartTimeUtc | Neustarthäufigkeit dieses Moduls aufgrund der Neustartrichtlinie |
| modules.{moduleId}.runtimeStatus | Status des Moduls: {„running“ \| „stopped“ \| „failed“ \| „backoff“ \| „unhealthy“} |
| modules.{moduleId}.statusDescription | Textbeschreibung des Modulstatus, falls „unhealthy“. |
| modules.{moduleId}.exitCode | Der vom Modulcontainer gemeldete Exitcode beim Beenden |
| modules.{moduleId}.startTimeUtc | Zeitpunkt des letzten Modulstarts |
| modules.{moduleId}.lastExitTimeUtc | Zeitpunkt der letzten Modulbeendigung |
| modules.{moduleId}.lastRestartTimeUtc | Zeitpunkt des letzten Modulneustarts |
| modules.{moduleId}.restartCount | Neustarthäufigkeit dieses Moduls aufgrund der Neustartrichtlinie |

## <a name="edgehub-desired-properties"></a>Gewünschte EdgeHub-Eigenschaften

Der Modulzwilling für den IoT Edge-Hub wird als `$edgeHub` bezeichnet und koordiniert die Kommunikationsvorgänge zwischen dem auf einem Gerät ausgeführten IoT Edge-Hub und IoT Hub. Die gewünschten Eigenschaften werden festgelegt, wenn ein Bereitstellungsmanifest auf einem bestimmten Gerät im Zuge einer Einzelgeräte- oder einer umfassenden Bereitstellung angewendet wird. 

| Eigenschaft | BESCHREIBUNG | Im Bereitstellungsmanifest erforderlich |
| -------- | ----------- | -------- |
| Schemaversion | Muss „1.0“ sein. | Ja |
| routes.{routeName} | Eine Zeichenfolge, die eine IoT Edge-Hubroute darstellt. | Das `routes`-Element kann vorhanden, aber leer sein. |
| storeAndForwardConfiguration.timeToLiveSecs | Zeit in Sekunden, wie lange der IoT Edge-Hub Nachrichten nach einer Trennung von Routingendpunkten aufbewahrt, z.B. nach einer Trennung von IoT Hub oder einem lokalen Modul | Ja |

## <a name="edgehub-reported-properties"></a>Gemeldete EdgeHub-Eigenschaften

| Eigenschaft | BESCHREIBUNG |
| -------- | ----------- |
| lastDesiredVersion | Dieser Integerwert bezeichnet die letzte vom IoT Edge-Hub verarbeitete Version der gewünschten Eigenschaften. |
| lastDesiredStatus.code | Dieser Statuscode nennt die letzten gewünschten Eigenschaften, die der IoT Edge-Hub erkannt hat. Zulässige Werte: `200`: Erfolgreich, `400`: Ungültige Konfiguration, `500`: Fehler |
| lastDesiredStatus.description | Textbeschreibung des Status |
| clients.{device or moduleId}.status | Konnektivitätsstatus dieses Geräts oder Moduls. Mögliche Werte: {"connected" \| "disconnected"}. Nur Modulidentitäten können den Status „disconnected“ aufweisen. Nachgeschaltete Geräte, die eine Verbindung mit dem IoT Edge-Hub herstellen, werden nur bei erfolgreicher Verbindung angezeigt. |
| clients.{device or moduleId}.lastConnectTime | Letzter Verbindungszeitpunkt des Geräts oder Moduls |
| clients.{device or moduleId}.lastDisconnectTime | Letzter Trennungszeitpunkt des Geräts oder Moduls |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung dieser Eigenschaften zum Erstellen von Bereitstellungsmanifesten finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können](module-composition.md).
