---
title: Deklarieren von Modulen und Routen mit Bereitstellungsmanifesten – Azure IoT Edge | Microsoft-Dokumentation
description: Hier erfahren Sie, wie ein Bereitstellungsmanifest deklariert, welche Module auf welche Weise bereitgestellt werden sollen und wie Nachrichtenrouten zwischen ihnen erstellt werden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f4828b59ffa43365f48c002262368d383dfcff05
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389367"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Bereitstellen von Modulen und Einrichten von Routen in IoT Edge

Jedes IoT Edge-Gerät führt mindestens zwei Module aus, die die IoT Edge-Runtime ausmachen: „$edgeAgent“ und „$edgeHub“. Ein IoT Edge-Gerät kann mehrere zusätzliche Module für eine beliebige Anzahl von Prozessen ausführen. Verwenden Sie ein Bereitstellungsmanifest, um Ihrem Gerät mitzuteilen, welche Module installiert und wie sie für die Zusammenarbeit konfiguriert werden müssen. 

Das *Bereitstellungsmanifest* ist ein JSON-Dokument, das Folgendes beschreibt:

* Der **IoT Edge-Agent**-Modulzwilling, der drei Komponenten umfasst. 
  * Das Containerimage für jedes Modul, das auf dem Gerät ausgeführt wird.
  * Die Anmeldeinformationen für den Zugriff auf die Registrierungen des privaten Containers, die Modulimages enthalten.
  * Anweisungen dazu, wie jedes Modul erstellt und verwaltet werden soll.
* Den Modulzwilling des **IoT Edge-Hubs**, der u.a. festlegt, wie Nachrichten zwischen Modulen und schließlich an IoT Hub gesendet werden
* Ggf. die gewünschten Eigenschaften zusätzlicher Modulzwillinge

Alle IoT Edge-Geräte müssen mit einem Bereitstellungsmanifest konfiguriert werden. Eine neu installierte IoT Edge-Runtime meldet einen Fehlercode, solange kein gültiges Manifest konfiguriert ist. 

In den Azure IoT Edge-Tutorials erstellen Sie ein Bereitstellungsmanifest mithilfe eines Assistenten im Azure IoT Edge-Portal. Sie können auch ein Bereitstellungsmanifest programmgesteuert mithilfe von REST oder des IoT Hub Service SDK anwenden. Weitere Informationen finden Sie unter [Grundlegendes zu IoT Edge-Bereitstellungen](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Erstellen eines Bereitstellungsmanifests

Im Grunde ist ein Bereitstellungsmanifest eine Liste von Modulzwillingen, die mit den gewünschten Eigenschaften konfiguriert sind. Ein Bereitstellungsmanifest teilt einem IoT Edge-Gerät (oder einer Gruppe von Geräten) mit, welche Module installiert und wie sie konfiguriert werden müssen. Bereitstellungsmanifeste enthalten die *gewünschten Eigenschaften* für jeden Modulzwilling. IoT Edge-Geräte geben die *gemeldeten Eigenschaften* für jedes Modul zurück. 

Jedes Bereitstellungsmanifest erfordert zwei Module: `$edgeAgent` und `$edgeHub`. Diese Module gehören zur IoT Edge-Runtime, die das IoT Edge-Gerät und die darauf ausgeführten Module verwaltet. Weitere Informationen zu diesen Modulen finden Sie unter [Grundlegendes zur IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

Zusätzlich zu den beiden Runtimemodulen können Sie bis zu 20 eigene Module hinzufügen, die auf einem IoT Edge-Gerät ausgeführt werden. 

Ein Bereitstellungsmanifest, das nur die IoT Edge-Runtime („edgeAgent“ und „edgeHub“) enthält, ist gültig.

Bereitstellungsmanifeste haben die folgende Struktur:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Konfigurieren von Modulen

Definieren Sie, wie die IoT Edge-Runtime die Module in Ihrer Bereitstellung installiert. Der IoT Edge-Agent ist die Runtimekomponente, die Installation, Updates und Statusberichte für ein IoT Edge-Gerät verwaltet. Deswegen benötigt der „$edgeAgent“-Modulzwilling die Konfigurations- und Verwaltungsinformationen für alle Module. Zu diesen Informationen zählen die Konfigurationsparameter für den IoT Edge-Agent selbst. 

Eine vollständige Liste der Eigenschaften, die hinzugefügt werden können oder müssen, finden Sie unter [Properties of the IoT Edge agent and Edge hub](module-edgeagent-edgehub.md) (Eigenschaften des IoT Edge-Agents und IoT Edge-Hubs).

Die $edgeAgent-Eigenschaften weisen die folgende Struktur auf:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Deklarieren von Routen

Der IoT Edge-Hub verwaltet die Kommunikation zwischen Modulen, IoT Hub und beliebigen Blattgeräten. Deshalb enthält der „$edgeHub“-Modulzwilling die gewünschte Eigenschaft *Routen*. Diese deklariert, wie Nachrichten in einer Bereitstellung übermittelt werden. Sie können innerhalb einer Bereitstellung mehrere Routen verwenden.

Routen werden in den gewünschten **$edgeHub**-Eigenschaften mit der folgenden Syntax deklariert:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Für jede Route sind eine Quelle und eine Senke erforderlich, die Bedingung ist jedoch optional. Sie kann bei Bedarf zum Filtern von Nachrichten verwendet werden. 


### <a name="source"></a>`Source`

Die Quelle gibt an, woher die Nachrichten stammen. IoT Edge kann Nachrichten von Modulen oder Blattknotengeräten weiterleiten. 

Unter Verwendung der IoT-SDKs können Module mithilfe der ModuleClient-Klasse spezifische Ausgabewarteschlangen für ihre Nachrichten deklarieren. Ausgabewarteschlangen sind nicht erforderlich, aber Sie sind hilfreich für die Verwaltung mehrerer Routen. Blattknotengeräten können die DeviceClient-Klasse der IoT-SDKs verwenden, um Nachrichten auf die gleiche Weise an IoT Edge-Gatewaygeräte zu senden, wie sie Nachrichten an IoT Hub senden würden. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Azure IoT Hub-SDKs](../iot-hub/iot-hub-devguide-sdks.md).

Die Quelleigenschaft kann die folgenden Werte haben:

| `Source` | BESCHREIBUNG |
| ------ | ----------- |
| `/*` | Alle Gerät-zu-Cloud-Nachrichten oder Benachrichtigungen über Änderungen am Zwilling, die von Modulen oder Blattgeräten gesendet wurden |
| `/twinChangeNotifications` | Alle Änderungen am Zwilling (gemeldete Eigenschaften), die von Modulen oder Blattgeräten gesendet wurden |
| `/messages/*` | Alle Gerät-zu-Cloud-Nachrichten, die von einem Modul über eine beliebige oder keine Ausgabe, oder von einem Blattknotengerät gesendet wurden |
| `/messages/modules/*` | Alle Gerät-zu-Cloud-Nachrichten, die von einem Modul über eine beliebige oder keine Ausgabe versendet wurden |
| `/messages/modules/<moduleId>/*` | Alle Gerät-zu-Cloud-Nachrichten, die von einem bestimmten Modul über eine beliebige oder keine Ausgabe gesendet wurden |
| `/messages/modules/<moduleId>/outputs/*` | Alle Gerät-zu-Cloud-Nachrichten, die von einem bestimmten Modul über eine beliebige Ausgabe gesendet wurden |
| `/messages/modules/<moduleId>/outputs/<output>` | Alle Gerät-zu-Cloud-Nachrichten, die von einem bestimmten Modul über eine bestimmte Ausgabe gesendet wurden |

### <a name="condition"></a>Bedingung
Die Bedingung ist in einer Routendeklaration optional. Wenn Sie alle Nachrichten von der Quelle an die Senke übergeben möchten, lassen Sie die **WHERE**-Klausel ganz weg. Alternativ können Sie die [IoT Hub-Abfragesprache](../iot-hub/iot-hub-devguide-routing-query-syntax.md) verwenden, um nach bestimmten Nachrichten oder Nachrichtentypen zu filtern, die die Bedingung erfüllen. IoT Edge-Routen unterstützen keine Nachrichtenfilterung, die auf Zwillingstags oder -eigenschaften basiert. 

Das Format der Nachrichten, die zwischen Modulen in IoT Edge übermittelt werden, entspricht dem Format der Nachrichten, die zwischen Ihren Geräten und Azure IoT Hub übermittelt werden. Alle Nachrichten liegen im JSON-Format vor und verfügen über die Parameter **systemProperties**, **appProperties** und **body**. 

Sie können Abfragen für alle drei Parameter erstellen. Verwenden Sie dazu die folgende Syntax: 

* Systemeigenschaften: `$<propertyName>` oder `{$<propertyName>}`
* Anwendungseigenschaften: `<propertyName>`
* Texteigenschaften: `$body.<propertyName>` 

Beispiele zum Erstellen von Abfragen für Nachrichteneigenschaften finden Sie unter [D2C-Nachrichtenrouten-Abfrageausdrücke](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Ein spezifisches Beispiel für IoT Edge ist das Filtern nach Nachrichten, die von einem Blattgerät gesendet wurden und bei einem Gatewaygerät eingegangen sind. Nachrichten von Modulen enthalten eine die Systemeigenschaft **connectionModuleId**. Wenn Sie Nachrichten von Blattgeräten direkt an IoT Hub weiterleiten möchten, verwenden Sie die folgende Route, um Modulnachrichten auszuschließen:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Senke
Die Senke definiert, wohin die Nachrichten gesendet werden. Nur Module und IoT Hub können Nachrichten empfangen. Nachrichten können nicht an andere Geräte weitergeleitet werden. Es gibt keine Platzhalteroptionen in der Senkeneigenschaft. 

Die Senkeneigenschaft kann die folgenden Werte haben:

| Senke | BESCHREIBUNG |
| ---- | ----------- |
| `$upstream` | Sendet die Nachricht an IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Sendet die Nachricht an eine bestimmte Eingabe eines bestimmten Moduls |

IoT Edge bietet At-Least-Once-Garantien. Der IoT Edge-Hub speichert Nachrichten lokal, falls eine Route die Nachricht nicht an die entsprechende Senke übermitteln kann. Dieser Fall kann beispielsweise eintreten, wenn der IoT Edge-Hub keine Verbindung mit IoT Hub herstellen kann oder das Zielmodul nicht verbunden ist.

Der IoT Edge-Hub speichert die Nachrichten bis zu dem Zeitpunkt, der in der `storeAndForwardConfiguration.timeToLiveSecs`-Eigenschaft in den gewünschten [IoT Edge-Hubeigenschaften](module-edgeagent-edgehub.md) angegeben ist.

## <a name="define-or-update-desired-properties"></a>Definieren oder Aktualisieren der gewünschten Eigenschaften 

Das Bereitstellungsmanifest gibt gewünschte Eigenschaften für Module an, die auf dem IoT Edge-Gerät bereitgestellt wurden. Die im Bereitstellungsmanifest angegebenen gewünschten Eigenschaften überschreiben alle gewünschten Eigenschaften, die aktuell im Modulzwilling vorhanden sind.

Wenn Sie die gewünschten Eigenschaften eines Modulzwillings nicht im Bereitstellungsmanifest angeben, nimmt IoT Hub keine Änderungen am Modulzwilling vor. Stattdessen können Sie die gewünschten Eigenschaften programmgesteuert festlegen.

Die gleichen Verfahren, mit denen Sie Gerätezwillinge ändern, werden auch zum Ändern von Modulzwillingen verwendet. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Beispiel für ein Bereitstellungsmanifest

Hier sehen Sie ein Beispiel für ein gültiges Bereitstellungsmanifestdokument:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Eine vollständige Liste der Eigenschaften, die in „$edgeAgent“ und „$edgeHub“ hinzugefügt werden können oder müssen, finden Sie unter [Properties of the IoT Edge agent and Edge hub](module-edgeagent-edgehub.md) (Eigenschaften des IoT Edge-Agents und Edge-Hubs).

* Sie wissen jetzt, wie IoT Edge-Module verwendet werden. Im nächsten Schritt lernen Sie [Grundlegendes zu den Anforderungen und Tools für die Entwicklung von IoT Edge-Modulen](module-development.md) kennen.
