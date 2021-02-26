---
title: Übertragen einer Nutzlast zwischen Gerät und Device Provisioning-Dienst von Azure
description: In diesem Dokument wird beschrieben, wie Sie eine Nutzlast zwischen Gerät und Device Provisioning-Dienst (Device Provisioning Service, DPS) übertragen.
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: a3ee7f3fca3fff1cd401f26489b01fb9cc4e09c5
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259518"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>Übertragen von Nutzlasten zwischen Geräten und DPS
Mitunter benötigt DPS weitere Daten von Geräten, um sie ordnungsgemäß im richtigen IoT Hub bereitzustellen, und diese Daten müssen vom Gerät bereitgestellt werden. Ebenso kann DPS Daten an das Gerät zurückgeben, um clientseitige Logik zu ermöglichen. 

## <a name="when-to-use-it"></a>Einsatzgebiete
Diese Funktion kann als Ergänzung der [benutzerdefinierten Zuordnung](./how-to-use-custom-allocation-policies.md) verwendet werden. Beispiel: Sie möchten Ihre Geräte anhand des Gerätemodells ohne menschliche Eingriffe zuordnen. In diesem Fall verwenden Sie die [benutzerdefinierte Zuordnung](./how-to-use-custom-allocation-policies.md). Sie können das Gerät in diesem Fall für die Meldung der Modellinformationen im Rahmen des [Registrierungsgeräteaufrufs](/rest/api/iot-dps/runtimeregistration/registerdevice) konfigurieren. DPS übergibt die Nutzlast des Geräts an den benutzerdefinierten Zuweisungswebhook. Dann kann Ihre Funktion entscheiden, auf welchen IoT Hub dieses Gerät geht, wenn es Gerätemodellinformationen empfängt. Wenn der Webhook einige Daten an das Gerät zurückgeben möchte, werden die Daten auf ähnliche Weise als Zeichenfolge in der Antwort des Webhooks übergeben.  

## <a name="device-sends-data-payload-to-dps"></a>Gerät sendet Datennutzlast an DPS
Wenn Ihr Gerät einen [Registrierungsgeräteaufruf](/rest/api/iot-dps/runtimeregistration/registerdevice) an DPS sendet, kann dieser Aufruf erweitert werden, um andere Felder im Textkörper zu verwenden. Der Textkörper sieht wie folgt aus: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS gibt Daten an das Gerät zurück
Wenn der Webhook für die benutzerdefinierte Zuweisungsrichtlinie Daten an das Gerät zurückgeben möchte, werden die Daten als Zeichenfolge in der Antwort des Webhooks übergeben. Die Änderung ist unten im Abschnitt zur Nutzlast zu finden. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK-Unterstützung
Diese Funktion ist in den [Client-SDKs](./index.yml) für C, C#, Java und Node.js verfügbar.  

## <a name="next-steps"></a>Nächste Schritte
* Entwickeln mithilfe des [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) für Azure IoT Hub und des Device Provisioning-Diensts von Azure IoT Hub