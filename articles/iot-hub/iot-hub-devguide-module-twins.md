---
title: Grundlegendes zu Azure IoT Hub-Modulzwillingen | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Synchronisieren von Status und Daten zwischen IoT Hub und Ihren Geräten mithilfe von Modulzwillingen'
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: cd0a9a66f3014a39a73cf04badfc67cd2ff4c3de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61363461"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Verstehen und Verwenden von Modulzwillingen in IoT Hub

In diesem Artikel wird vorausgesetzt, dass Sie bereits den Artikel [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](iot-hub-devguide-device-twins.md) gelesen haben. In IoT Hub können unter jeder Geräteidentität bis zu 20 Modulidentitäten erstellt werden. Jede Modulidentität generiert implizit einen Modulzwilling. Modulzwillinge weisen Ähnlichkeiten mit Gerätezwillingen auf und stellen JSON-Dokumente dar, in denen Modulstatusinformationen wie Metadaten, Konfigurationen und Zustände gespeichert werden. Azure IoT Hub pflegt einen Modulzwilling für jedes Modul, das Sie mit IoT Hub verbinden. 

Auf der Geräteseite können Sie mithilfe der IoT Hub-Geräte-SDKs Module erstellen, die jeweils eine unabhängige Verbindung mit IoT Hub herstellen. Durch diese Funktionalität können Sie separate Namespaces für unterschiedliche Komponenten auf Ihrem Gerät verwenden. Ein Beispiel: Angenommen, Sie verfügen über einen Verkaufsautomaten mit drei verschiedene Sensoren. Jeder Sensor wird von einer anderen Abteilung in Ihrem Unternehmen gesteuert. Sie können für jeden Sensor jeweils ein eigenes Modul erstellen. Dadurch kann jede Abteilung nur Aufträge oder direkte Methoden an den Sensor senden, den sie steuert, was zur Vermeidung von Konflikten und Benutzerfehlern beiträgt.

 Modulidentität und Modulzwilling bieten die gleichen Funktionen wie Geräteidentität und Gerätezwilling, aber mit einer höheren Granularität. Dank dieser höheren Granularität können geeignete Geräte (etwa betriebssystembasierte Geräte oder Firmwaregeräte, die mehrere Komponenten verwalten) Konfigurationen und Zustände für die einzelnen Komponenten isolieren. Modulidentität und Modulzwillinge ermöglichen die Trennung von Verwaltungszuständigkeiten bei Verwendung von IoT-Geräten mit modularen Softwarekomponenten. Wir arbeiten daran, bis zur allgemeinen Verfügbarkeit von Modulzwillingen alle Funktionen für Gerätezwillinge auf der Modulzwillingsebene zu unterstützen. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieser Artikel beschreibt Folgendes:

* Die Struktur des Modulzwillings: *Tags*, *gewünschte Eigenschaften* und *gemeldete Eigenschaften*.
* Die Vorgänge, die Module und Back-Ends für Modulzwillinge ausführen können.

Weitere Informationen zur Verwendung von gemeldeten Eigenschaften, D2C-Nachrichten und Dateiuploads finden Sie bei Bedarf im [Leitfaden zur D2C-Kommunikation](iot-hub-devguide-d2c-guidance.md).

Weitere Informationen zu gewünschten Eigenschaften, direkten Methoden und C2D-Nachrichten finden Sie im [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md).

## <a name="module-twins"></a>Modulzwillinge

Modulzwillinge speichern modulspezifische Informationen, auf die Folgendes zutrifft:

* Module auf dem Gerät und IoT Hub können damit Modulzustände und -konfigurationen synchronisieren.

* Das Lösungs-Back-End kann mit ihnen lang andauernde Vorgänge abfragen und als Ziel verwenden.

Der Lebenszyklus eines Modulzwillings ist mit der entsprechenden [Modulidentität](iot-hub-devguide-identity-registry.md) verknüpft. Modulzwillinge werden implizit erstellt und gelöscht, wenn in IoT Hub eine Modulidentität erstellt oder gelöscht wird.

Ein Modulzwilling ist ein JSON-Dokument, das Folgendes enthält:

* **Tags**. Ein Abschnitt des JSON-Dokuments, in dem das Lösungs-Back-End Lese- und Schreibvorgänge ausführen kann. Tags sind für Module auf dem Gerät nicht sichtbar. Tags werden zu Abfragezwecken festgelegt.

* **Gewünschte Eigenschaften** Werden in Verbindung mit gemeldeten Eigenschaften zum Synchronisieren von Modulkonfigurationen oder -zuständen verwendet. Das Lösungs-Back-End kann gewünschte Eigenschaften festlegen, die von der Modul-App gelesen werden können. Die Modul-App kann auch Benachrichtigungen über Änderungen an den gewünschten Eigenschaften erhalten.

* **Gemeldete Eigenschaften** Werden in Verbindung mit gewünschten Eigenschaften zum Synchronisieren von Modulkonfigurationen oder -zuständen verwendet. Die Modul-App kann gemeldete Eigenschaften festlegen, die vom Lösungs-Back-End gelesen und abgefragt werden können.

* **Modulidentitätseigenschaften:** Der Stamm des JSON-Dokuments für einen Modulzwilling enthält die schreibgeschützten Eigenschaften der dazugehörigen Modulidentität aus der [Identitätsregistrierung](iot-hub-devguide-identity-registry.md).

![Architekturdarstellung eines Gerätezwillings](./media/iot-hub-devguide-device-twins/module-twin.jpg)

Das folgende Beispiel zeigt das JSON-Dokument für einen Modulzwilling:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

Im Stammobjekt befinden sich die Modulidentitätseigenschaften, Containerobjekte für `tags` sowie die Eigenschaften `reported` und `desired`. Der Container `properties` enthält einige schreibgeschützte Elemente (`$metadata`, `$etag` und `$version`), die in den Abschnitten zu den [Metadaten des Modulzwillings](iot-hub-devguide-module-twins.md#module-twin-metadata) und zur [optimistischen Nebenläufigkeit](iot-hub-devguide-device-twins.md#optimistic-concurrency) beschrieben sind.

### <a name="reported-property-example"></a>Beispiel für eine gemeldete Eigenschaft

Im vorherigen Beispiel enthält der Modulzwilling eine `batteryLevel`-Eigenschaft, die von der Modul-App gemeldet wird. Mit dieser Eigenschaft können Module abgefragt und Aktionen auf der Grundlage des letzten gemeldeten Akkustands ausgeführt werden. Ein weiteres Beispiel wären von der Modul-App gemeldete Modulfunktionen oder Konnektivitätsoptionen.

> [!NOTE]
> Gemeldete Eigenschaften tragen zur Vereinfachung von Szenarien bei, in denen das Lösungs-Back-End den letzten bekannten Wert einer Eigenschaft abfragen soll. Verwenden Sie [Gerät-zu-Cloud-Nachrichten](iot-hub-devguide-messages-d2c.md), wenn das Lösungs-Back-End Modultelemetriedaten in Form einer Reihe von Ereignissen mit Zeitstempel abfragen muss (beispielsweise eine Zeitreihe).

### <a name="desired-property-example"></a>Beispiel für eine gewünschte Eigenschaft

Im vorherigen Beispiel werden die gewünschten und gemeldeten Eigenschaften des Modulzwillings `telemetryConfig` vom Lösungs-Back-End und von der Modul-App verwendet, um die Telemetriekonfiguration für dieses Modul zu synchronisieren. Beispiel:

1. Das Lösungs-Back-End legt die gewünschte Eigenschaft mit dem gewünschten Konfigurationswert fest. Hier sehen Sie den Teil des Dokuments mit der festgelegten gewünschten Eigenschaft:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. Wenn die Modul-App verbunden ist, wird sie sofort über die Änderung informiert. Andernfalls erhält sie die Benachrichtigung, sobald die Verbindung erneut hergestellt wird. Anschließend meldet die Modul-App die aktualisierte Konfiguration (oder einen Fehler unter Verwendung der Eigenschaft `status`). Dies ist der Teil mit den gemeldeten Eigenschaften:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Das Lösungs-Back-End kann die Ergebnisse des Konfigurationsvorgangs auf vielen Modulen durch [Abfragen](iot-hub-devguide-query-language.md) des Modulzwillings nachverfolgen.

> [!NOTE]
> Die obigen Codeausschnitte sind Beispiele mit optimierter Lesbarkeit und zeigen eines der möglichen Verfahren zum Codieren einer Modulkonfiguration und des dazugehörigen Status. IoT Hub gibt kein bestimmtes Schema für die gewünschten und gemeldeten Eigenschaften in den Modulzwillingen vor.
> 
> 

## <a name="back-end-operations"></a>Back-End-Vorgänge
Das Lösungs-Back-End greift mithilfe folgender atomischer Vorgänge, die über HTTPS verfügbar gemacht werden, auf den Modulzwilling zu:

* **Abrufen des Modulzwillings anhand der ID:** Dieser Vorgang gibt das Dokument für den Modulzwilling zurück (einschließlich Tags sowie gewünschter und gemeldeter Systemeigenschaften).

* **Partielles Aktualisieren des Modulzwillings:** Dieser Vorgang ermöglicht es dem Lösungs-Back-End, einen Teil der Tags oder gewünschten Eigenschaften in einem Modulzwilling zu aktualisieren. Bei der partiellen Aktualisierung handelt es sich um ein JSON-Dokument, das eine beliebige Eigenschaft hinzufügt oder aktualisiert. Auf `null` festgelegte Eigenschaften werden entfernt. Im folgenden Beispiel wird eine neue gewünschte Eigenschaft mit dem Wert `{"newProperty": "newValue"}` erstellt, der vorhandene Wert von `existingProperty` wird mit `"otherNewValue"` überschrieben, und `otherOldProperty` wird entfernt. Ansonsten werden an vorhandenen Eigenschaften oder Tags keine weiteren Änderungen vorgenommen:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Ersetzen gewünschter Eigenschaften**. Dieser Vorgang ermöglicht dem Lösungs-Back-End, alle vorhandenen gewünschten Eigenschaften vollständig zu überschreiben und ein neues JSON-Dokument für `properties/desired` bereitzustellen.

* **Ersetzen von Tags**. Dieser Vorgang ermöglicht es dem Lösungs-Back-End, alle vorhandenen Tags vollständig zu überschreiben und ein neues JSON-Dokument für `tags` bereitzustellen.

* **Zwillingsbenachrichtigungen empfangen**. Mit diesem Vorgang kann das Lösungs-Back-End benachrichtigt werden, wenn der Zwilling geändert wird. Zu diesem Zweck muss Ihre IoT-Lösung eine Route erstellen die Datenquelle auf *twinChangeEvents* festlegen. Standardmäßig werden keine Zwillingsbenachrichtigungen gesendet, und es existieren noch keine solchen Routen. Wenn die Änderungsrate zu hoch ist, oder andere Gründe wie interne Fehler vorliegen, sendet der IoT Hub möglicherweise nur eine Benachrichtigung, die alle Änderungen enthält. Wenn Ihre Anwendung zuverlässige Prüfungen und Protokolle aller Zwischenzustände benötigt, sollten Sie D2C-Nachrichten verwenden. Die Zwillingsbenachrichtung umfasst Eigenschaften und einen Textkörper.

  - Eigenschaften

    | NAME | Wert |
    | --- | --- |
    $content-type | Anwendung/json |
    $iothub-enqueuedtime |  Uhrzeit, zu der die Benachrichtigung gesendet wurde |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | UTF-8 |
    deviceId | ID des Geräts |
    moduleId | ID des Moduls |
    hubName | Name des IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)-Zeitstempel des Vorgangs |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" oder "updateTwin" |

    Nachrichtensystemeigenschaften ist das Symbol `$` vorangestellt.

  - Body
        
    Dieser Abschnitt enthält alle Zwillingsänderungen in einem JSON-Format. Er verwendet das gleiche Format wie ein Patch, jedoch mit dem Unterschied, dass alle Zwillingsabschnitte enthalten sein können (Tags, properties.reported, properties.desired) und dass die $metadata-Elemente enthalten sind. Beispiel:

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Alle oben genannten Vorgänge unterstützen die [optimistische Nebenläufigkeit](iot-hub-devguide-device-twins.md#optimistic-concurrency) und erfordern die Berechtigung **ServiceConnect**, wie im Artikel [Steuern des Zugriffs auf IoT Hub](iot-hub-devguide-security.md) definiert.

Zusätzlich zu diesen Vorgängen kann das Lösungs-Back-End die Modulzwillinge über eine SQL-ähnliche [IoT Hub-Abfragesprache](iot-hub-devguide-query-language.md) abfragen.

## <a name="module-operations"></a>Modulvorgänge

Die Modul-App führt mithilfe folgender atomarer Vorgänge Aktionen für den Modulzwilling aus:

* **Abrufen des Modulzwillings:** Dieser Vorgang gibt das Dokument für den Modulzwilling für das derzeit verbundene Modul zurück (einschließlich Tags sowie gewünschter und gemeldeter Systemeigenschaften).

* **Teilweises Aktualisieren gemeldeter Eigenschaften**. Dieser Vorgang ermöglicht die partielle Aktualisierung der gemeldeten Eigenschaften des derzeit verbundenen Moduls. Dabei wird das gleiche JSON-Updateformat wie bei der partiellen Aktualisierung der gewünschten Eigenschaften durch das Lösungs-Back-End verwendet.

* **Beobachten gewünschter Eigenschaften**. Das derzeit verbundene Modul kann auf Wunsch benachrichtigt werden, sobald die gewünschten Eigenschaften aktualisiert werden. Das Modul erhält die gleiche Form der Aktualisierung (partielle oder vollständige Ersetzung), die durch das Lösungs-Back-End ausgeführt wird.

Alle oben genannten Vorgänge erfordern die Berechtigung **ModuleConnect**, wie im Artikel [Steuern des Zugriffs auf IoT Hub](iot-hub-devguide-security.md) definiert.

Die [Azure IoT-Geräte-SDKs](iot-hub-devguide-sdks.md) vereinfachen die Verwendung der oben beschriebenen Vorgänge, die mit vielen Sprachen und Plattformen erstellt wurden.

## <a name="tags-and-properties-format"></a>Format von Tags und Eigenschaften

Tags, gewünschte Eigenschaften und gemeldete Eigenschaften sind JSON-Objekte mit den folgenden Einschränkungen:

* Alle Schlüssel in JSON-Objekten sind UTF-8 UNICODE-Zeichenfolgen mit 64 Bytes, bei denen die Groß- und Kleinschreibung berücksichtigt werden muss. UNICODE-Steuerzeichen (Segmente C0 und C1) sowie `.` und `$` gehören nicht zu den zulässigen Zeichen.

* Alle Werte in JSON-Objekten können die folgenden JSON-Typen aufweisen: boolescher Wert, Zahl, Zeichenfolge, Objekt. Arrays sind nicht zulässig. Der maximale Wert für ganze Zahlen ist 4503599627370495 und der minimale Wert für ganze Zahlen ist -4503599627370496.

* Alle JSON-Objekte in Tags, gewünschten und gemeldeten Eigenschaften können eine maximale Tiefe von 5 haben. Das folgende Objekt ist z.B. gültig:

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* Alle Zeichenfolgenwerte können höchstens 512Byte lang sein.

## <a name="module-twin-size"></a>Größe des Modulzwillings

IoT Hub erzwingt eine Größenbegrenzung von je 8 KB für die jeweiligen Gesamtwerte von `tags`, `properties/desired` und `properties/reported`, ausgenommen schreibgeschützte Elemente.

Die Größe wird durch Zusammenzählen aller Zeichen mit Ausnahme von UNICODE-Steuerzeichen (Segmente C0 und C1) und Leerzeichen außerhalb von Zeichenfolgenkonstanten berechnet.

IoT Hub gibt für alle Vorgänge, die die Größe dieser Dokumente über den Grenzwert hinaus erhöhen würden, einen Fehler zurück.

## <a name="module-twin-metadata"></a>Metadaten des Modulzwillings

IoT Hub verwaltet den Zeitstempel der letzten Aktualisierung für jedes JSON-Objekt in den gewünschten und gemeldeten Eigenschaften des Modulzwillings. Zeitstempel verwenden UTC und sind im [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)-Format codiert: `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Beispiel:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Diese Informationen werden auf jeder Ebene (nicht nur die Zweige der JSON-Struktur) gespeichert, um Aktualisierungen beizubehalten, bei denen Objektschlüssel entfernt werden.

## <a name="optimistic-concurrency"></a>Optimistische Parallelität

Tags sowie gewünschte und gemeldete Eigenschaften unterstützen optimistische Parallelität.
Tags verfügen über ein ETag (gemäß [RFC7232](https://tools.ietf.org/html/rfc7232)), das der JSON-Darstellung des Tags entspricht. ETags können bei bedingten Aktualisierungsvorgängen über das Lösungs-Back-End verwendet werden, um die Konsistenz zu gewährleisten.

Gewünschte und gemeldete Eigenschaften des Modulzwillings verfügen zwar über keine ETags, aber über einen garantiert inkrementellen `$version`-Wert. Zur Gewährleistung der Konsistenz von Updates kann von der aktualisierenden Partei die Version genutzt werden (ähnlich wie ein ETag). Beispiele wären etwa eine Modul-App für eine gemeldete Eigenschaft oder das Lösungs-Back-End für eine gewünschte Eigenschaft.

Versionen sind auch nützlich, wenn ein beobachtender Agent (beispielsweise die Modul-App, die die gewünschten Eigenschaften beobachtet) Racebedingungen zwischen dem Ergebnis eines Abrufvorgangs und einer Aktualisierungsbenachrichtigung beheben muss. Im Abschnitt [Ablauf zur Wiederherstellung der Geräteverbindung](iot-hub-devguide-device-twins.md#device-reconnection-flow) finden Sie weitere Informationen. 

## <a name="next-steps"></a>Nächste Schritte

Um einige der in diesem Artikel beschriebenen Konzepte auszuprobieren, sehen Sie sich die folgenden IoT Hub-Tutorials an:

* [Erste Schritte mit der Modulidentität und dem Modulzwilling von IoT Hub unter Verwendung eines .NET-Back-Ends und eines .NET-Geräts](iot-hub-csharp-csharp-module-twin-getstarted.md)
