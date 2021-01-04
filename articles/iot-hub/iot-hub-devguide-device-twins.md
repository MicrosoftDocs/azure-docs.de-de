---
title: Grundlegendes zu Azure IoT Hub-Gerätezwillingen | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Verwenden Sie Gerätezwillinge zum Synchronisieren von Status und Daten zwischen IoT Hub und Ihren Geräten.'
author: nehsin
manager: philmea
ms.author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: b83faecb16ac09a47a0ade25474f7a5b3ecd4296
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400927"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Verstehen und Verwenden von Gerätezwillingen in IoT Hub

*Gerätezwillinge* sind JSON-Dokumente, in denen Gerätestatusinformationen gespeichert werden, einschließlich Metadaten, Konfigurationen und Bedingungen. Azure IoT Hub pflegt einen Gerätezwilling für jedes Gerät, das Sie mit IoT Hub verbinden. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieser Artikel beschreibt Folgendes:

* Die Struktur des Gerätezwillings: *Tags*, *gewünschte Eigenschaften* und *gemeldete Eigenschaften*.
* Die Vorgänge, die Geräte-Apps und Back-Ends auf Gerätezwillingen ausführen können

Verwenden Sie Gerätezwillinge für Folgendes:

* Speichern gerätespezifischer Metadaten in der Cloud – beispielsweise den Aufstellungsort eines Automaten.

* Melden aktueller Zustandsinformationen – beispielsweise verfügbare Funktionen und Bedingungen aus Ihrer Geräte-App. Beispiel: Ein Gerät ist per Mobilfunk oder WLAN mit Ihrem IoT Hub verbunden.

* Synchronisieren des Zustands von Workflows mit langer Ausführungsdauer zwischen Geräte-App und Back-End-App. Beispielsweise wenn das Lösungs-Back-End die neu zu installierende Firmwareversion angibt und die Geräte-App die verschiedenen Phasen des Aktualisierungsvorgangs meldet.

* Abfragen von Metadaten, Konfiguration oder Status des Geräts

Weitere Informationen zur Verwendung von gemeldeten Eigenschaften, D2C-Nachrichten und Dateiuploads finden Sie bei Bedarf im [Leitfaden zur D2C-Kommunikation](iot-hub-devguide-d2c-guidance.md).

Weitere Informationen zu gewünschten Eigenschaften, direkten Methoden und C2D-Nachrichten finden Sie im [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md).

## <a name="device-twins"></a>Gerätezwillinge

Auf Gerätezwillingen werden gerätespezifische Informationen gespeichert, auf die Folgendes zutrifft:

* Geräte und Back-Ends können mit ihnen Gerätezustände und -konfigurationen synchronisieren.

* Das Lösungs-Back-End kann mit ihnen lang andauernde Vorgänge abfragen und als Ziel verwenden.

Der Lebenszyklus eines Gerätezwillings ist mit der entsprechenden [Geräteidentität](iot-hub-devguide-identity-registry.md) verknüpft. Gerätezwillinge werden implizit erstellt und gelöscht, wenn in IoT Hub eine Geräteidentität erstellt oder gelöscht wird.

Ein Gerätezwilling ist ein JSON-Dokument, das Folgendes enthält:

* **Tags**. Ein Abschnitt des JSON-Dokuments, in dem das Lösungs-Back-End Lese- und Schreibvorgänge ausführen kann. Tags sind für Geräte-Apps nicht sichtbar.

* **Gewünschte Eigenschaften** Werden in Verbindung mit gemeldeten Eigenschaften zum Synchronisieren von Gerätekonfigurationen oder -zuständen verwendet. Das Lösungs-Back-End kann gewünschte Eigenschaften festlegen, die von der Geräte-App gelesen werden können. Die Geräte-App kann auch Benachrichtigungen über Änderungen an den gewünschten Eigenschaften erhalten.

* **Gemeldete Eigenschaften** Werden in Verbindung mit gewünschten Eigenschaften zum Synchronisieren von Gerätekonfigurationen oder -zuständen verwendet. Die Geräte-App kann gemeldete Eigenschaften festlegen, die vom Lösungs-Back-End gelesen und abgefragt werden können.

* **Geräteidentitätseigenschaften**. Der Stamm des JSON-Dokuments für einen Gerätezwilling enthält die schreibgeschützten Eigenschaften der zugehörigen Geräteidentität aus der [Identitätsregistrierung](iot-hub-devguide-identity-registry.md). Die Eigenschaften `connectionStateUpdatedTime` und `generationId` werden nicht einbezogen.

![Screenshot: Eigenschaften von Gerätezwillingen](./media/iot-hub-devguide-device-twins/twin.png)

Das folgende Beispiel zeigt das JSON-Dokument für einen Gerätezwilling:

```json
{
    "deviceId": "devA",
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

Im Stammobjekt befinden sich die Geräteidentitätseigenschaften und Containerobjekte für `tags` sowie die `reported`- und `desired`-Eigenschaften. Der Container `properties` enthält einige schreibgeschützte Elemente (`$metadata`, `$etag` und `$version`), die in den Abschnitten zu den [Metadaten des Gerätezwillings](iot-hub-devguide-device-twins.md#device-twin-metadata) und zur [optimistischen Nebenläufigkeit](iot-hub-devguide-device-twins.md#optimistic-concurrency) beschrieben sind.

### <a name="reported-property-example"></a>Beispiel für eine gemeldete Eigenschaft

Im vorherigen Beispiel enthält der Gerätezwilling eine `batteryLevel`-Eigenschaft, die von der Geräte-App gemeldet wird. Mit dieser Eigenschaft können Geräte anhand des letzten gemeldeten Akkustands abgefragt und Aktionen auf ihnen ausgeführt werden. Ein weiteres Beispiel wäre das Melden von Gerätefunktionen oder Verbindungsoptionen durch das Gerät.

> [!NOTE]
> Gemeldete Eigenschaften tragen zur Vereinfachung von Szenarien bei, in denen das Lösungs-Back-End den letzten bekannten Wert einer Eigenschaft abfragen soll. Verwenden Sie [Gerät-zu-Cloud-Nachrichten](iot-hub-devguide-messages-d2c.md), wenn das Lösungs-Back-End Gerätetelemetriedaten in Form von Folgen von Ereignissen mit Zeitstempeln abfragen muss, beispielsweise als Zeitreihe.

### <a name="desired-property-example"></a>Beispiel für eine gewünschte Eigenschaft

Im vorherigen Beispiel werden die gewünschten und gemeldeten Eigenschaften des `telemetryConfig`-Gerätezwillings vom Lösungs-Back-End und von der Geräte-App verwendet, um die Telemetriekonfiguration für dieses Gerät zu synchronisieren. Beispiel:

1. Das Lösungs-Back-End legt die gewünschte Eigenschaft mit dem gewünschten Konfigurationswert fest. Hier sehen Sie den Teil des Dokuments mit der festgelegten gewünschten Eigenschaft:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Die Geräte-App wird sofort über die Änderung benachrichtigt, wenn sie verbunden ist oder später eine Verbindung herstellt. Anschließend meldet die Geräte-App die aktualisierte Konfiguration (oder einen Fehler über die `status`-Eigenschaft). Dies ist der Teil mit den gemeldeten Eigenschaften:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Das Lösungs-Back-End kann die Ergebnisse des Konfigurationsvorgangs auf vielen Geräten durch [Abfragen](iot-hub-devguide-query-language.md) von Gerätezwillingen nachverfolgen.

> [!NOTE]
> Die obigen Codeausschnitte sind Beispiele mit optimierter Lesbarkeit und stellen eine Möglichkeit zum Codieren einer Konfiguration und des Status eines Geräts dar. IoT Hub setzt kein bestimmtes Schema für die gewünschten und gemeldeten Eigenschaften in den Gerätezwillingen voraus.
> 

Mithilfe von Zwillingen können Sie Vorgänge mit langer Ausführungsdauer (beispielsweise Firmwareupdates) synchronisieren. Weitere Informationen zur Verwendung von Eigenschaften zum geräteübergreifenden Synchronisieren und Verfolgen von Vorgängen mit langer Ausführungsdauer finden Sie unter [Verwenden von gewünschten Eigenschaften zum Konfigurieren von Geräten](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Back-End-Vorgänge

Das Lösungs-Back-End greift mithilfe folgender atomischer Vorgänge, die über HTTPS verfügbar gemacht werden, auf den Gerätezwilling zu:

* **Abrufen des Gerätezwillings mittels ID**. Dieser Vorgang gibt das Dokument für den Gerätezwilling zurück – einschließlich Tags sowie gewünschter und gemeldeter Systemeigenschaften.

* **Partielles Aktualisieren des Gerätezwillings**. Dieser Vorgang ermöglicht es dem Lösungs-Back-End, die Tags oder gewünschten Eigenschaften in einem Gerätezwilling teilweise zu aktualisieren. Bei der partiellen Aktualisierung handelt es sich um ein JSON-Dokument, das eine beliebige Eigenschaft hinzufügt oder aktualisiert. Auf `null` festgelegte Eigenschaften werden entfernt. Im folgenden Beispiel wird eine neue gewünschte Eigenschaft mit dem Wert `{"newProperty": "newValue"}` erstellt, der vorhandene Wert von `existingProperty` wird mit `"otherNewValue"` überschrieben, und `otherOldProperty` wird entfernt. Ansonsten werden an vorhandenen Eigenschaften oder Tags keine weiteren Änderungen vorgenommen:

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

* **Zwillingsbenachrichtigungen empfangen**. Mit diesem Vorgang kann das Lösungs-Back-End benachrichtigt werden, wenn der Zwilling geändert wird. Zu diesem Zweck muss Ihre IoT-Lösung eine Route erstellen die Datenquelle auf *twinChangeEvents* festlegen. Standardmäßig existieren keine solchen Routen, daher werden keine Zwillingsbenachrichtigungen gesendet. Wenn die Änderungsrate zu hoch ist, oder andere Gründe wie interne Fehler vorliegen, sendet der IoT Hub möglicherweise nur eine Benachrichtigung, die alle Änderungen enthält. Wenn Ihre Anwendung zuverlässige Prüfungen und Protokolle aller Zwischenzustände benötigt, sollten Sie D2C-Nachrichten verwenden. Die Zwillingsbenachrichtung umfasst Eigenschaften und einen Textkörper.

  - Eigenschaften

    | Name | Wert |
    | --- | --- |
    $content-type | Anwendung/json |
    $iothub-enqueuedtime |  Uhrzeit, zu der die Benachrichtigung gesendet wurde |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | UTF-8 |
    deviceId | ID des Geräts |
    hubName | Name des IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)-Zeitstempel des Vorgangs |
    iothub-message-schema | twinChangeNotification |
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

Neben diesen Vorgängen kann das Lösungs-Back-End auch folgende Aktionen ausführen:

* Abfragen der Gerätezwillinge mithilfe der SQL-ähnlichen [IoT Hub-Abfragesprache](iot-hub-devguide-query-language.md)

* Ausführen von Vorgängen für große Mengen von Gerätezwillingen mithilfe von [Aufträgen](iot-hub-devguide-jobs.md)

## <a name="device-operations"></a>Gerätevorgänge

Die Geräte-App greift mithilfe folgender atomarer Vorgänge auf den Gerätezwilling zu:

* **Abrufen des Gerätezwillings**. Dieser Vorgang gibt das Dokument für den Gerätezwilling (einschließlich gewünschter und gemeldeter Systemeigenschaften) für das derzeit verbundene Gerät zurück. (Tags sind für Geräte-Apps nicht sichtbar.)

* **Teilweises Aktualisieren gemeldeter Eigenschaften**. Dieser Vorgang ermöglicht die teilweise Aktualisierung der gemeldeten Eigenschaften des derzeit verbundenen Geräts. Dabei wird das gleiche JSON-Updateformat wie bei der partiellen Aktualisierung der gewünschten Eigenschaften durch das Lösungs-Back-End verwendet.

* **Beobachten gewünschter Eigenschaften**. Das derzeit verbundene Gerät kann benachrichtigt werden, sobald die gewünschten Eigenschaften aktualisiert werden. Das Gerät erhält die gleiche Form der Aktualisierung (partielle oder vollständige Ersetzung), die durch das Lösungs-Back-End ausgeführt wird.

Alle oben genannten Vorgänge erfordern die Berechtigung **DeviceConnect**, wie im Artikel [Steuern des Zugriffs auf IoT Hub](iot-hub-devguide-security.md) definiert.

Die [Azure IoT-Geräte-SDKs](iot-hub-devguide-sdks.md) vereinfachen die Verwendung der oben beschriebenen Vorgänge, die mit vielen Sprachen und Plattformen erstellt wurden. Weitere Informationen zu den Details der IoT Hub-Grundtypen für die Synchronisierung gewünschter Eigenschaften finden Sie im [Ablauf zur Wiederherstellung der Geräteverbindung](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Format von Tags und Eigenschaften

Tags, gewünschte Eigenschaften und gemeldete Eigenschaften sind JSON-Objekte mit den folgenden Einschränkungen:

* **Schlüssel**: Alle Schlüssel in JSON-Objekten sind UTF-8-codiert, die Groß-/Kleinschreibung muss beachtet werden, und ihre Länge beträgt bis zu 1 KB. UNICODE-Steuerzeichen (Segmente C0 und C1) sowie `.`, `$` und „SP“ gehören nicht zu den zulässigen Zeichen.

* **Werte**: Alle Werte in JSON-Objekten können die folgenden JSON-Typen aufweisen: boolescher Wert, Zahl, Zeichenfolge, Objekt. Arrays werden ebenfalls unterstützt.

    * Ganze Zahlen können den Minimalwert „-4503599627370496“ und den Maximalwert „4503599627370495“ haben.

    * Zeichenfolgenwerte sind UTF-8-codiert und können eine maximale Länge von 4 Bytes haben.

* **Tiefe**: Die maximale Tiefe von JSON-Objekten in Tags, gewünschten Eigenschaften und gemeldeten Eigenschaften ist „10“. Das folgende Objekt beispielsweise ist gültig:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Größe des Gerätezwillings

IoT Hub erzwingt eine Größenbeschränkung von 8 KB auf den Wert `tags` und eine Größenbeschränkung von jeweils 32 KB auf die Werte `properties/desired` und `properties/reported`. In diesen Summen sind keine schreibgeschützten Elemente wie `$etag`, `$version` und `$metadata/$lastUpdated` enthalten.

Die Größe von Zwillingen wird folgendermaßen berechnet:

* Für jede Eigenschaft im JSON-Dokument berechnet IoT Hub kumulativ und addiert die Länge des Eigenschaftenschlüssels und Eigenschaftswerts.

* Eigenschaftenschlüssel werden als UTF8-codierte Zeichenfolgen betrachtet.

* Einfache Eigenschaftswerte werden als UTF8-codierte Zeichenfolgen, numerische Werte (8 Bytes) oder boolesche Werte (4 Bytes) betrachtet.

* Die Größe der UTF8-codierten Zeichenfolgen wird berechnet, indem alle Zeichen gezählt werden – ausgenommen UNICODE-Steuerzeichen (Segmente C0 und C1).

* Komplexe Eigenschaftswerte (geschachtelte Objekte) werden basierend auf der Aggregatgröße der darin enthaltenen Eigenschaftenschlüssel und Eigenschaftswerte berechnet.

IoT Hub gibt für alle Vorgänge, die die Größe der Dokumente `tags`, `properties/desired` und `properties/reported` über den Grenzwert hinaus erhöhen würden, einen Fehler zurück.

## <a name="device-twin-metadata"></a>Metadaten des Gerätezwillings

IoT Hub verwaltet den Zeitstempel der letzten Aktualisierung für jedes JSON-Objekt in den gewünschten und gemeldeten Eigenschaften des Gerätezwillings. Zeitstempel verwenden UTC und sind im [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)-Format codiert: `YYYY-MM-DDTHH:MM:SS.mmmZ`.

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
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
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

Gewünschte und gemeldete Eigenschaften des Gerätezwillings verfügen zwar über keine ETags, aber über einen garantiert inkrementellen `$version`-Wert. Zur Gewährleistung der Konsistenz von Updates kann von der aktualisierenden Partei die Version genutzt werden (ähnlich wie ein ETag). Beispiele wären etwa eine Geräte-App für eine gemeldete Eigenschaft oder das Lösungs-Back-End für eine gewünschte Eigenschaft.

Versionen sind auch nützlich, wenn ein beobachtender Agent (beispielsweise die Geräte-App, die die gewünschten Eigenschaften beobachtet) Racebedingungen zwischen dem Ergebnis eines Abrufvorgangs und einer Aktualisierungsbenachrichtigung beheben muss. Im Abschnitt [Ablauf zur Wiederherstellung der Geräteverbindung](iot-hub-devguide-device-twins.md#device-reconnection-flow) finden Sie weitere Informationen.

## <a name="device-reconnection-flow"></a>Ablauf zur Wiederherstellung der Geräteverbindung

IoT Hub behält keine Aktualisierungsbenachrichtigungen für gewünschte Eigenschaften für nicht verbundene Geräte bei. Ein Gerät, das eine Verbindung herstellt, muss daher zusätzlich zum Abonnieren von Aktualisierungsbenachrichtigungen das gesamte Dokument der gewünschten Eigenschaften abrufen. Wenn die Gefahr von Racebedingungen zwischen Aktualisierungsbenachrichtigungen und dem vollständigen Abruf besteht, muss der folgende Ablauf sichergestellt werden:

1. Geräte-App stellt eine Verbindung mit einem IoT Hub her.
2. Geräte-App abonniert Aktualisierungsbenachrichtigungen für gewünschte Eigenschaften.
3. Geräte-App ruft das vollständige Dokument für gewünschte Eigenschaften ab.

Die Geräte-App kann alle Benachrichtigungen ignorieren, bei denen `$version` kleiner oder gleich der Version des vollständigen abgerufenen Dokuments ist. Diese Herangehensweise ist möglich, da IoT Hub sicherstellt, dass die Versionsnummern immer erhöht werden.

> [!NOTE]
> Diese Logik ist in den [Azure IoT-Geräte-SDKs](iot-hub-devguide-sdks.md) bereits implementiert. Diese Beschreibung ist nur dann nützlich, wenn die Geräte-App keine der Azure IoT-Geräte-SDKs verwenden kann und die MQTT-Schnittstelle direkt programmiert werden muss.
> 

## <a name="additional-reference-material"></a>Weiteres Referenzmaterial

Weitere Referenzthemen im IoT Hub-Entwicklerhandbuch:

* Im Artikel [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md) werden die verschiedenen Endpunkte beschrieben, die jeder IoT-Hub für Laufzeit- und Verwaltungsvorgänge verfügbar macht.

* Unter [Kontingente und Drosselung](iot-hub-devguide-quotas-throttling.md) werden die Kontingente für den IoT Hub-Dienst und das Drosselungsverhalten beschrieben, das bei Verwendung des Diensts zu erwarten ist.

* Im Artikel [Verstehen und Verwenden von Azure IoT Hub SDKs](iot-hub-devguide-sdks.md) werden die verschiedenen Sprach-SDKs aufgelistet, die Sie bei der Entwicklung von Geräte- und Dienst-Apps für die Interaktion mit IoT Hub verwenden können.

* Im Artikel [IoT Hub-Abfragesprache für Gerätezwillinge, Aufträge und Nachrichtenrouting](iot-hub-devguide-query-language.md) wird die IoT Hub-Abfragesprache beschrieben, mit der Sie Informationen zu Gerätezwillingen und Aufträgen aus IoT Hub abrufen können.

* Der Artikel [Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT](iot-hub-mqtt-support.md) enthält weitere Informationen zur IoT Hub-Unterstützung für das MQTT-Protokoll.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun etwas über Gerätezwillinge erfahren haben, sind möglicherweise die folgenden Themen aus dem IoT Hub-Entwicklerhandbuch für Sie interessant:

* [Verstehen und Verwenden von Modulzwillingen in IoT Hub](iot-hub-devguide-module-twins.md)
* [Aufrufen einer direkten Methode auf einem Gerät](iot-hub-devguide-direct-methods.md)
* [Planen von Aufträgen auf mehreren Geräten](iot-hub-devguide-jobs.md)

Um einige der in diesem Artikel beschriebenen Konzepte auszuprobieren, sehen Sie sich die folgenden IoT Hub-Tutorials an:

* [Verwenden des Gerätezwillings](iot-hub-node-node-twin-getstarted.md)
* [Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)
* [Geräteverwaltung mit Azure IoT-Tools für VS Code](iot-hub-device-management-iot-toolkit.md)
