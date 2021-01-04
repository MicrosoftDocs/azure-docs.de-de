---
title: Abrufen von Wetterdaten von Partnern für Wetterdaten
description: In diesem Artikel wird beschrieben, wie Sie Wetterdaten von Partnern abrufen.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460873"
---
# <a name="get-weather-data-from-weather-partners"></a>Abrufen von Wetterdaten von Partnern für Wetterdaten

Azure FarmBeats unterstützt Sie beim Abrufen von Wetterdaten von Ihren entsprechenden Anbietern, indem Sie ein Docker-basiertes Connectorframework nutzen. Mit diesem Framework implementieren die Anbieter von Wetterdaten eine Docker-Instanz, die in FarmBeats integriert werden kann. Derzeit wird der folgende Anbieter von Wetterdaten unterstützt.

  ![FarmBeats-Partner](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

Die Wetterdaten können verwendet werden, um verwertbare Erkenntnisse zu generieren und KI/ML-Modelle in FarmBeats zu entwickeln.

## <a name="before-you-start"></a>Vorbereitung

Stellen Sie zum Abrufen von Wetterdaten sicher, dass Sie [FarmBeats](./install-azure-farmbeats.md) installiert haben. Die Integration von Wetterdaten wird in Version 1.2.11 und höher unterstützt. 

## <a name="enable-weather-integration-with-farmbeats"></a>Aktivieren der Integration von Wetterdaten für FarmBeats

So beginnen Sie mit dem Abrufen von Wetterdaten auf Ihrem FarmBeats-Datenhub:

1. Navigieren Sie zu Ihrem FarmBeats-Datenhub-Swagger (`https://farmbeatswebsite-api.azurewebsites.net/swagger`).

2. Wechseln Sie zur /Partner-API, und führen Sie eine POST-Anforderung aus. Verwenden Sie die folgende Eingabenutzlast:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Verwenden Sie beispielsweise die folgende Nutzlast, um Wetterdaten von DTN abzurufen. Sie können den Namen und die Beschreibung ganz nach Wunsch ändern.

   > [!NOTE]
   > Der folgende Schritt erfordert einen API-Schlüssel. Um einen Schlüssel für Ihr DTN-Abonnement zu erhalten, wenden Sie sich an DTN.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Weitere Informationen zum /Partner-Objekt finden Sie im [Anhang](get-weather-data-from-weather-partner.md#appendix) dieses Artikels.

   Mit dem vorherigen Schritt werden die Ressourcen bereitgestellt, die Docker die Ausführung in der FarmBeats-Umgebung des Kunden ermöglichen.  

   Die Bereitstellung der Ressourcen dauert ca. 10 bis 15 Minuten.

3. Überprüfen Sie den Status des /Partner-Objekts, das Sie im vorherigen Schritt erstellt haben. Führen Sie hierzu eine GET-Anforderung in der /Partner-API aus, und überprüfen Sie den Status des /Partner-Objekts. Nachdem der Partner von FarmBeats erfolgreich bereitgestellt wurde, wird der Status auf **Aktiv** festgelegt.

4. Führen Sie in der /JobType-API eine GET-Anforderung aus. Suchen Sie nach den Wetteraufträgen, die Sie zuvor beim Hinzufügen des Partners erstellt haben. Das Feld **pipelineName** in den Wetteraufträgen weist das folgende Format auf: **Partnername_Partnertyp_Auftragsname**.

      Jetzt verfügt Ihre FarmBeats-Instanz über einen aktiven Partner für Wetterdaten. Sie können Aufträge ausführen, um Wetterdaten für einen bestimmten Standort (Breiten- und Längengrad) und einen bestimmten Datumsbereich anzufordern. Die Auftragstypen enthalten Details dazu, welche Parameter für die Ausführung von Wetteraufträgen benötigt werden.

      Für DTN werden beispielsweise die folgenden Auftragstypen erstellt:
   
      - **get_dtn_daily_observations**: Hiermit werden tägliche Beobachtungen für einen bestimmten Standort und Zeitraum abgerufen.
      - **get_dtn_daily_forecasts**: Hiermit werden tägliche Vorhersagen für einen bestimmten Standort und Zeitraum abgerufen.
      - **get_dtn_hourly_observations**: Hiermit werden stündliche Beobachtungen für einen bestimmten Standort und Zeitraum abgerufen.
      - **get_dtn_hourly_forecasts**: Hiermit werden stündliche Vorhersagen für einen bestimmten Standort und Zeitraum abgerufen.

6. Notieren Sie sich die ID und die Parameter der Auftragstypen.

7. Wechseln Sie zur /Jobs-API, und führen Sie eine POST-Anforderung in /Jobs aus. Verwenden Sie die folgende Eingabenutzlast:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Verwenden Sie beispielsweise die folgende Nutzlast, um **get_dtn_daily_observations** auszuführen:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Im obigen Schritt werden die Wetteraufträge gemäß der Definition auf der Docker-Instanz des Partners ausgeführt, und die Wetterdaten werden in FarmBeats erfasst. Sie können den Status des Auftrags überprüfen, indem Sie eine GET-Anforderung in /Jobs ausführen. Suchen Sie in der Antwort nach **currentState**. Nach Abschluss des Vorgangs wird **currentState** auf **Succeeded** (Erfolgreich) festgelegt.

## <a name="query-ingested-weather-data"></a>Abfragen von erfassten Wetterdaten

Nach Abschluss der Wetteraufträge können Sie die erfassten Wetterdaten mit den REST-APIs Ihres FarmBeats-Datenhubs abfragen, um Modelle zu entwickeln oder verwertbare Erkenntnisse zu gewinnen.

So fragen Sie Wetterdaten mit einer FarmBeats-REST-API ab:

1. Navigieren Sie in Ihrem FarmBeats-Datenhub-[Swagger](https://yourdatahub.azurewebsites.net/swagger) zur /WeatherDataLocation-API, und führen Sie eine GET-Anforderung aus. Die Antwort enthält /WeatherDataLocation-Objekte, die für den von der Auftragsausführung angegebenen Standort (Breiten- und Längengrad) erstellt wurden. Notieren Sie sich die **ID** und die **weatherDataModelId** der Objekte.

2. Führen Sie wie zuvor in der /WeatherDataModel-API eine GET/{id}-Anforderung für die **weatherDataModelId** aus. Das Wetterdatenmodell zeigt alle Metadaten und Details zu den erfassten Wetterdaten. Im Objekt für das Wetterdatenmodell beispielsweise zeigt das Measure für das Wetter Details dazu, welche Wetterinformationen unterstützt und welche Typen und Einheiten jeweils angegeben werden. Beispiel:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Notieren Sie sich die Antwort auf den GET/{id}-Aufruf für das Wetterdatenmodell.

3. Wechseln Sie zur Telemetry-API, und führen Sie eine POST-Anforderung aus. Verwenden Sie die folgende Eingabenutzlast:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    Die Antwort zeigt die Wetterdaten, die für den angegebenen Zeitbereich verfügbar sind:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

Im vorherigen Beispiel zeigt die Antwort Daten für zwei Zeitstempel. Sie zeigt in den beiden Zeitstempeln auch den Measurenamen (temperature) und Werte der gemeldeten Wetterdaten. Beziehen Sie sich auf das zugehörige Wetterdatenmodell, um den Typ und die Einheit der gemeldeten Werte zu interpretieren.

## <a name="troubleshoot-job-failures"></a>Beheben von Auftragsfehlern

Zum Beheben von Auftragsfehlern [überprüfen Sie die Auftragsprotokolle](troubleshoot-azure-farmbeats.md#weather-data-job-failures).


## <a name="appendix"></a>Anhang

|        Partner   |  Details   |
| ------- | -------             |
|     DockerDetails – imageName         |          Name des Docker-Images. Beispielsweise „docker.io/mydockerimage“ (Image in „hub.docker.com“) oder „myazureacr.azurecr.io/mydockerimage“ (Image in Azure Container Registry) usw. Wenn keine Registrierung angegeben wird, lautet der Wert standardmäßig „hub.docker.com“.      |
|          DockerDetails – imageTag             |         Der Tagname des Docker-Images. Der Standardwert ist „latest“.     |
|  DockerDetails – credentials      |  Die Anmeldeinformationen zum Zugriff auf die private Docker-Instanz. Diese Anmeldeinformationen werden vom Partner bereitgestellt.   |
|  DockerDetails – azureBatchVMDetails – batchVMSKU     |    Azure Batch-VM-SKU. Weitere Informationen finden Sie unter [Alle verfügbaren Linux-VMs](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <BR> <BR> Gültige Werte sind „Small“, „ExtraLarge“, „Large“, „A8“, „A9“, „Medium“, „A5“, „A6“, „A7“, „STANDARD_D1“, „STANDARD_D2“, „STANDARD_D3“, „STANDARD_D4“, „STANDARD_D11“, „STANDARD_D12“, „STANDARD_D13“, „STANDARD_D14“, „A10“, „A11“, „STANDARD_D1_V2“, „STANDARD_D2_V2“, „STANDARD_D3_V2“, „STANDARD_D4_V2“, „STANDARD_D11_V2“, „STANDARD_D12_V2“, „STANDARD_D13_V2“, „STANDARD_D14_V2“, „STANDARD_G1“, „STANDARD_G2“, „STANDARD_G3“, „STANDARD_G4“, „STANDARD_G5“, „STANDARD_D5_V2“, „BASIC_A1“, „BASIC_A2“, „BASIC_A3“, „BASIC_A4“, „STANDARD_A1“, „STANDARD_A2“, „STANDARD_A3“, „STANDARD_A4“, „STANDARD_A5“, „STANDARD_A6“, „STANDARD_A7“, „STANDARD_A8“, „STANDARD_A9“, „STANDARD_A10“, „STANDARD_A11“, „STANDARD_D15_V2“, „STANDARD_F1“, „STANDARD_F2“, „STANDARD_F4“, „STANDARD_F8“, „STANDARD_F16“, „STANDARD_NV6“, „STANDARD_NV12“, „STANDARD_NV24“, „STANDARD_NC6“, „STANDARD_NC12“, „STANDARD_NC24“, „STANDARD_NC24r“, „STANDARD_H8“, „STANDARD_H8m“, „STANDARD_H16“, „STANDARD_H16m“, „STANDARD_H16mr“, „STANDARD_H16r“, „STANDARD_A1_V2“, „STANDARD_A2_V2“, „STANDARD_A4_V2“, „STANDARD_A8_V2“, „STANDARD_A2m_V2“, „STANDARD_A4m_V2“, „STANDARD_A8m_V2“, „STANDARD_M64ms“, „STANDARD_M128s“ und „STANDARD_D2_V3“. *Der Standardwert ist „STANDARD_D2_V2“.*  |
|    DockerDetails – azureBatchVMDetails – dedicatedComputerNodes   |  Anzahl von dedizierten Computerknoten pro Batchpool. Der Standardwert ist 1. |
|    DockerDetails – azureBatchVMDetails – nodeAgentSKUID          |    SKU-ID des Azure Batch-Knoten-Agents. Derzeit wird nur der Batch-Knoten-Agent „batch.node.ubuntu 18.04“ unterstützt.    |
| DockerDetails – partnerCredentials | Anmeldeinformationen für das Aufrufen der Partner-API in Docker. Der Partner stellt diese Informationen basierend auf dem unterstützten Autorisierungsmechanismus bereit, beispielsweise Benutzername und Kennwort oder ein API-Schlüssel. |
| partnerType | „Weather“. Andere Partnertypen in FarmBeats sind „Sensor“ und „Imagery“.  |
|  name   |   Der gewünschte Name des Partners im FarmBeats-System.   |
|  description |  Beschreibung   |

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Sensordaten aus Ihrer Azure FarmBeats-Instanz abgefragt haben, erfahren Sie jetzt, wie Sie [Karten generieren](generate-maps-in-azure-farmbeats.md#generate-maps).