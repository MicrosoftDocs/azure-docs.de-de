---
title: 'Bereitstellen von Modulen und Routen mit Bereitstellungsmanifesten: Azure IoT Edge'
description: Hier erfahren Sie, wie ein Bereitstellungsmanifest deklariert, welche Module auf welche Weise bereitgestellt werden sollen und wie Nachrichtenrouten zwischen ihnen erstellt werden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 406420fcd517ceda8ea6eedfc955f54b15541f74
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366601"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Bereitstellen von Modulen und Einrichten von Routen in IoT Edge

Jedes IoT Edge-Gerät führt mindestens zwei Module aus, die die IoT Edge-Runtime ausmachen: „$edgeAgent“ und „$edgeHub“. Ein IoT Edge-Gerät kann mehrere zusätzliche Module für eine beliebige Anzahl von Prozessen ausführen. Verwenden Sie ein Bereitstellungsmanifest, um Ihrem Gerät mitzuteilen, welche Module installiert und wie sie für die Zusammenarbeit konfiguriert werden müssen.

Das *Bereitstellungsmanifest* ist ein JSON-Dokument, das Folgendes beschreibt:

* Der **IoT Edge-Agent**-Modulzwilling, der drei Komponenten umfasst:
  * Das Containerimage für jedes Modul, das auf dem Gerät ausgeführt wird.
  * Die Anmeldeinformationen für den Zugriff auf die Registrierungen des privaten Containers, die Modulimages enthalten.
  * Anweisungen dazu, wie jedes Modul erstellt und verwaltet werden soll.
* Den Modulzwilling des **IoT Edge-Hubs**, der u.a. festlegt, wie Nachrichten zwischen Modulen und schließlich an IoT Hub gesendet werden
* Die gewünschten Eigenschaften zusätzlicher Modulzwillinge (optional).

Alle IoT Edge-Geräte müssen mit einem Bereitstellungsmanifest konfiguriert werden. Eine neu installierte IoT Edge-Runtime meldet einen Fehlercode, solange kein gültiges Manifest konfiguriert ist.

In den Azure IoT Edge-Tutorials erstellen Sie ein Bereitstellungsmanifest mithilfe eines Assistenten im Azure IoT Edge-Portal. Sie können auch ein Bereitstellungsmanifest programmgesteuert mithilfe von REST oder des IoT Hub Service SDK anwenden. Weitere Informationen finden Sie unter [Grundlegendes zu IoT Edge-Bereitstellungen](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Erstellen eines Bereitstellungsmanifests

Im Grunde ist ein Bereitstellungsmanifest eine Liste von Modulzwillingen, die mit den gewünschten Eigenschaften konfiguriert sind. Ein Bereitstellungsmanifest teilt einem IoT Edge-Gerät (oder einer Gruppe von Geräten) mit, welche Module installiert und wie sie konfiguriert werden müssen. Bereitstellungsmanifeste enthalten die *gewünschten Eigenschaften* für jeden Modulzwilling. IoT Edge-Geräte geben die *gemeldeten Eigenschaften* für jedes Modul zurück.

Jedes Bereitstellungsmanifest erfordert zwei Module: `$edgeAgent` und `$edgeHub`. Diese Module gehören zur IoT Edge-Runtime, die das IoT Edge-Gerät und die darauf ausgeführten Module verwaltet. Weitere Informationen zu diesen Modulen finden Sie unter [Grundlegendes zur IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

Zusätzlich zu den beiden Runtimemodulen können Sie bis zu 50 eigene Module für die Ausführung auf einem IoT Edge-Gerät hinzufügen.

Ein Bereitstellungsmanifest, das nur die IoT Edge-Runtime („edgeAgent“ und „edgeHub“) enthält, ist gültig.

Bereitstellungsmanifeste haben die folgende Struktur:

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
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
    }
  }
}
```

## <a name="configure-modules"></a>Konfigurieren von Modulen

Definieren Sie, wie die IoT Edge-Runtime die Module in Ihrer Bereitstellung installiert. Der IoT Edge-Agent ist die Runtimekomponente, die Installation, Updates und Statusberichte für ein IoT Edge-Gerät verwaltet. Aus diesem Grund benötigt der Modulzwilling $edgeAgent die Konfigurations- und Verwaltungsinformationen für alle Module. Zu diesen Informationen zählen die Konfigurationsparameter für den IoT Edge-Agent selbst.

Die $edgeAgent-Eigenschaften weisen die folgende Struktur auf:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
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
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Die IoT Edge-Agent-Schemaversion 1.1 wurde zusammen mit IoT Edge, Version 1.0.10, veröffentlicht und ermöglicht eine Startreihenfolge für Module. Die Schemaversion 1.1 wird für jede IoT Edge-Bereitstellung empfohlen, in der Version 1.0.10 oder höher ausgeführt wird.

### <a name="module-configuration-and-management"></a>Modulkonfiguration und -verwaltung

In der Liste der gewünschten Eigenschaften des IoT Edge-Agents können Sie definieren, welche Module für ein IoT Edge-Gerät bereitgestellt werden und wie sie konfiguriert und verwaltet werden sollen.

Eine vollständige Liste der gewünschten Eigenschaften, die hinzugefügt werden können oder müssen, finden Sie unter [Properties of the IoT Edge agent and Edge hub](module-edgeagent-edgehub.md) (Eigenschaften des IoT Edge-Agents und IoT Edge-Hubs).

Beispiel:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Jedes Modul hat eine Eigenschaft **settings**, die das Modul **image**, eine Adresse für das Containerimage in einer Containerregistrierung und alle **createOptions** zum Konfigurieren des Images beim Start enthält. Weitere Informationen finden Sie unter [Konfigurieren von Erstellungsoptionen für Container für IoT Edge-Module](how-to-use-create-options.md).

Das edgeHub-Modul und die benutzerdefinierten Module haben auch drei Eigenschaften, die den IoT Edge-Agent informieren, wie sie verwaltet werden müssen:

* **Status:** Gibt an, ob das Modul bei der ersten Bereitstellung ausgeführt oder beendet werden soll. Erforderlich.
* **RestartPolicy**: Gibt an, wann und ob der IoT Edge-Agent das Modul neu starten soll, wenn es beendet wurde. Erforderlich.
* **StartupOrder**: *Eingeführt in IoT Edge, Version 1.0.10.* Gibt an, in welcher Reihenfolge der IoT Edge-Agent die Module bei der ersten Bereitstellung starten soll. Die Reihenfolge wird mit ganzen Zahlen deklariert, wobei ein Modul mit einem Startwert von „0“ zuerst gestartet wird, gefolgt von den höheren Zahlen. Für das Modul „edgeAgent“ gibt es keinen Startwert, weil es immer zuerst gestartet wird. Optional.

  Der IoT Edge-Agent initiiert die Module in der Reihenfolge ihres Startwerts, wartet aber nicht, bis jedes Modul abgeschlossen ist, bevor er zum nächsten wechselt.

  Die Startreihenfolge ist hilfreich, wenn einige Module von anderen abhängig sind. So möchten Sie beispielsweise, dass das edgeHub-Modul zuerst gestartet wird, damit es zum Weiterleiten von Nachrichten bereit ist, wenn die anderen Module gestartet werden. Oder Sie möchten ein Speichermodul vor den Modulen starten, die ihm Daten senden. Sie sollten Ihre Module jedoch immer so entwerfen, dass sie Fehler anderer Module behandeln. Es liegt in der Natur von Containern, dass sie jederzeit und beliebig oft beendet und neu gestartet werden können.

## <a name="declare-routes"></a>Deklarieren von Routen

Der IoT Edge-Hub verwaltet die Kommunikation zwischen Modulen, IoT Hub und beliebigen Blattgeräten. Deshalb enthält der „$edgeHub“-Modulzwilling die gewünschte Eigenschaft *Routen*. Diese deklariert, wie Nachrichten in einer Bereitstellung übermittelt werden. Sie können innerhalb einer Bereitstellung mehrere Routen verwenden.

Routen werden in den gewünschten **$edgeHub**-Eigenschaften mit der folgenden Syntax deklariert:

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Die IoT Edge Hub-Schemaversion 1.1 wurde zusammen mit IoT Edge, Version 1.0.10, veröffentlicht und ermöglicht Routenpriorisierung und Gültigkeitsdauer. Die Schemaversion 1.1 wird für jede IoT Edge-Bereitstellung empfohlen, in der Version 1.0.10 oder höher ausgeführt wird.

Für jede Route ist eine *Quelle*, aus der die Nachrichten gesendet werden, und eine *Senke* erforderlich, wohin die Nachrichten gesendet werden. Die *Bedingung* ist ein optionales Element, das Sie zum Filtern von Nachrichten verwenden können.

Sie können den Routen *Priorität* zuweisen, bei denen Sie sicherstellen möchten, dass deren Nachrichten zuerst verarbeitet werden. Dieses Feature ist hilfreich in Szenarien, in denen die Upstreamverbindung schwach oder eingeschränkt ist und Sie wichtige Daten haben, die über Standardtelemetrienachrichten priorisiert werden sollen.

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

### <a name="priority-and-time-to-live"></a>Priorität und Gültigkeitsdauer

Routen können entweder mit nur einer Zeichenfolge deklariert werden, die eine Route definiert, oder als ein Objekt, das eine Routenzeichenfolge, eine ganze Zahl für die Priorität und eine ganze Zahl für die Gültigkeitsdauer verwendet.

Option 1:

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

Option 2, eingeführt in IoT Edge, Version 1.0.10, zusammen mit der IoT Edge Hub-Schemaversion 1.1:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

Die Werte für **Priorität** können „0–9“ (einschließlich) sein, wobei „0“ die höchste Priorität hat. Nachrichten werden basierend auf ihren Endpunkten in die Warteschlange eingereiht. Alle Nachrichten mit Priorität „0“, deren Ziel ein bestimmter Endpunkt ist, werden verarbeitet, bevor Nachrichten mit Priorität „1“ und demselben Endpunkt als Ziel verarbeitet werden usw. Wenn mehrere Routen für denselben Endpunkt dieselbe Priorität haben, werden deren Nachrichten nach dem Prinzip „First-come-first-served“ („Wer zuerst kommt, mahlt zuerst“) verarbeitet. Wenn keine Priorität angegeben wurde, wird die Route der niedrigsten Priorität zugewiesen.

Die Eigenschaft **timeToLiveSecs** erbt ihren Wert vom IoT Edge Hub-Wert **storeAndForwardConfiguration**, sofern dieser Wert nicht explizit festgelegt wurde. Der Wert kann eine beliebige positive ganze Zahl sein.

Ausführliche Informationen zur Verwaltung von Prioritätswarteschlangen finden Sie auf der Referenzseite zu [Routenpriorität und Gültigkeitsdauer](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md).

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
        "schemaVersion": "1.1",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
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
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
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
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Eine vollständige Liste der Eigenschaften, die in „$edgeAgent“ und „$edgeHub“ hinzugefügt werden können oder müssen, finden Sie unter [Properties of the IoT Edge agent and Edge hub](module-edgeagent-edgehub.md) (Eigenschaften des IoT Edge-Agents und Edge-Hubs).

* Sie wissen jetzt, wie IoT Edge-Module verwendet werden. Im nächsten Schritt lernen Sie [Grundlegendes zu den Anforderungen und Tools für die Entwicklung von IoT Edge-Modulen](module-development.md) kennen.
