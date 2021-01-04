---
title: Azure Stream Analytics auf IoT Edge
description: Erstellen von Edge-Aufträgen in Azure Stream Analytics und Bereitstellung dieser Aufträge auf Geräten, auf denen Azure IoT Edge ausgeführt wird.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 10/29/2020
ms.custom: seodec18
ms.openlocfilehash: cba81b8415f0f9cf7253e674e90ae09718b94d54
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130475"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics auf IoT Edge
 
Mit Azure Stream Analytics (ASA) unter IoT Edge können Entwickler Analyseinformationen nahezu in Echtzeit und näher an den IoT-Geräten bereitstellen, sodass die von den Geräten generierten Daten optimal verwertet werden können. Azure Stream Analytics ist für geringe Latenz, Resilienz, eine effiziente Bandbreitennutzung und Compliance konzipiert. So können Unternehmen jetzt eine Steuerlogik in der Nähe ihrer gewerblichen Betriebe bereitstellen und in der Cloud ausgeführte Big Data-Analysen ergänzen.  

Azure Stream Analytics auf IoT Edge wird innerhalb des [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/)-Frameworks ausgeführt. Nach dem Erstellen des Auftrags in ASA können Sie ihn mithilfe von IoT Hub bereitstellen und verwalten.

## <a name="scenarios"></a>Szenarien
![Übersichtsabbildung zu IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Latenzarme Befehls- und Steuerungsstruktur** : Systeme für die Fertigungssicherheit müssen beispielsweise im Bedarfsfall praktisch verzögerungsfrei auf Betriebsdaten reagieren. Mit ASA unter IoT Edge können Sie Sensordaten nahezu in Echtzeit analysieren und bei Erkennung von Anomalien Befehle ausgeben, um eine Maschine abzuschalten oder Warnungen auszugeben.
*   **Eingeschränkte Cloudkonnektivität** : Unternehmenswichtige Systeme wie beispielsweise ferngesteuerte Bergbaugeräte, vernetzte Schiffe oder Offshorebohrer müssen Daten auch dann analysieren und bei Bedarf reagieren können, wenn die Cloudverbindung nicht stabil ist. Mit ASA läuft Ihre Streaminglogik unabhängig von der Netzwerkkonnektivität, und Sie können auswählen, welche Daten Sie zur weiteren Verarbeitung oder Speicherung in die Cloud senden möchten.
* **Eingeschränkte Bandbreite** : Das von Düsentriebwerken oder vernetzten Autos erzeugte Datenvolumen kann so groß werden, dass die Daten gefiltert oder vor dem Versand in die Cloud einer Vorverarbeitung unterzogen werden müssen. Mithilfe von ASA können Sie Daten, die in die Cloud gesendet werden sollen, filtern und aggregieren.
* **Compliance** : Zur Einhaltung gesetzlicher Auflagen ist es möglicherweise erforderlich, einige Daten vor dem Senden in die Cloud lokal zu anonymisieren oder zu aggregieren.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-Aufträge in Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Was ist ein Edge-Auftrag?

ASA Edge-Aufträge werden in Containern ausgeführt, die auf [Azure IoT Edge-Geräten](../iot-edge/about-iot-edge.md) bereitgestellt sind. Sie bestehen aus zwei Teilen:
1.  Einem Cloudteil, der für die Auftragsdefinition zuständig ist: Benutzer definieren Eingaben, Ausgaben, Abfragen und andere Einstellungen (Ereignisse in falscher Reihenfolge usw.) in der Cloud.
2.  Einem Modul, das auf Ihren IoT-Geräten ausgeführt wird. Es enthält die ASA-Engine und empfängt die Auftragsdefinition aus der Cloud. 

ASA verwendet IoT Hub zur Bereitstellung von Edge-Aufträgen auf Geräten. Weitere Informationen zur IoT Edge-Bereitstellung [finden Sie hier](../iot-edge/module-deployment-monitoring.md).

![Azure Stream Analytics-Edgeauftrag](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Installationsanweisungen
Die grundsätzlichen Schritte sind in der folgenden Tabelle beschrieben. Ausführliche Informationen finden Sie in den nachfolgenden Abschnitten.

| Schritt | Notizen |
| --- | --- |
| **Erstellen eines Speichercontainers** | Speichercontainer werden verwendet, um die Auftragsdefinition zu speichern. Dort können Ihre IoT-Geräte darauf zugreifen. <br>  Sie können auch bereits vorhandene Speichercontainer verwenden. |
| **Erstellen eines ASA-Edge-Auftrags** | Sie erstellen einen neuen Auftrag und wählen **Edge** als **Hostingumgebung** aus. <br> Diese Aufträge werden aus der Cloud heraus erstellt/verwaltet und auf Ihren IoT Edge-Geräten ausgeführt. |
| **Einrichten der IoT Edge-Umgebung auf Ihren Geräten** | Anleitungen für [Windows](../iot-edge/quickstart.md) und [Linux](../iot-edge/quickstart-linux.md)|
| **Bereitstellen von ASA auf Ihren IoT Edge-Geräten** | Die ASA-Auftragsdefinition wird in den zuvor erstellten Speichercontainer exportiert. |

Führen Sie [dieses Schritttutorial](../iot-edge/tutorial-deploy-stream-analytics.md) aus, um Ihren ersten ASA-Auftrag auf IoT Edge bereitzustellen. Anhand des folgenden Videos können Sie nachvollziehen, wie ein Stream Analytics-Auftrag auf einem IoT Edge-Gerät ausgeführt wird:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Erstellen eines Speichercontainers
Ein Speichercontainer ist erforderlich, um die kompilierte ASA-Abfrage und die Auftragskonfiguration zu exportieren. Er wird verwendet, um Ihre spezifische Abfrage im ASA-Docker-Image zu konfigurieren. 
1. Befolgen Sie [diese Anweisungen](../storage/common/storage-account-create.md) zum Erstellen eines Speicherkontos im Azure-Portal. Sie können alle Standardoptionen beibehalten, um dieses Konto mit ASA zu verwenden.
2. Erstellen Sie im neu erstellten Speicherkonto einen Blobspeichercontainer:
    1. Klicken Sie auf **Blobs** und dann auf **+ Container**. 
    2. Geben Sie einen Namen ein, und konfigurieren Sie den Container als **Privat**.

#### <a name="create-an-asa-edge-job"></a>Erstellen eines ASA-Edge-Auftrags
> [!Note]
> In diesem Tutorial geht es schwerpunktmäßig um die Erstellung eines ASA-Auftrags mithilfe des Azure-Portals. Sie können auch das [Visual Studio-Plug-In verwenden, um einen ASA-Edge-Auftrag zu erstellen](./stream-analytics-tools-for-visual-studio-edge-jobs.md).

1. Erstellen Sie im Azure-Portal einen neuen „Stream Analytics-Auftrag“. [Hier Direktlink zum Erstellen eines neuen ASA-Auftrags einfügen](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob)

2. Wählen Sie auf dem Erstellungsbildschirm **Edge** als **Hostingumgebung** aus (siehe folgende Abbildung).

   ![Stream Analytics-Auftrag auf Edge erstellen](media/stream-analytics-edge/create-asa-edge-job.png)
3. Auftragsdefinition
    1. **Definieren von Eingabedatenströmen**. Definieren Sie mindestens einen Eingabedatenstrom für Ihren Auftrag.
    2. Definieren von Verweisdaten (optional)
    3. **Definieren von Ausgabedatenströmen**. Definieren Sie mindestens einen Ausgabedatenstrom für Ihren Auftrag. 
    4. **Definieren der Abfrage**. Definieren Sie mit dem Inline-Editor die ASA-Abfrage in der Cloud. Der Compiler führt eine automatische Prüfung auf aktivierte ASA-Edge-Syntax durch. Sie können die Abfrage auch durch Hochladen von Beispieldaten testen. 

4. Legen Sie im Menü **IoT Edge-Einstellungen** die Informationen zum Speichercontainer fest.

5. Festlegen optionaler Einstellungen
    1. **Ereignisreihenfolge**. Sie können im Portal eine Richtlinie für die falsche Reihenfolge konfigurieren. Die zugehörige Dokumentation finden Sie [hier](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Gebietsschema**. Hiermit legen Sie das Internationalisierungsformat fest.



> [!Note]
> Wenn eine Bereitstellung erstellt wird, exportiert ASA die Auftragsdefinition in einen Speichercontainer. Diese Auftragsdefinition bleibt während der gesamten Dauer einer Bereitstellung unverändert. Infolgedessen müssen Sie, wenn Sie einen auf Edge-Ebene ausgeführten Auftrag aktualisieren möchten, diesen in ASA bearbeiten und dann eine neue Bereitstellung in IoT Hub erstellen.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Einrichten Ihrer IoT Edge-Umgebung auf Ihren Geräten
Edge-Aufträge können auf Geräten bereitgestellt werden, auf denen Azure IoT Edge ausgeführt wird.
Hierzu müssen Sie die folgenden Schritte ausführen:
- Erstellen Sie eine IoT Hub-Instanz.
- Installieren Sie Docker und IoT Edge-Runtime auf Ihren Edge-Geräten.
- Legen Sie für Ihre Geräte den Status **IoT Edge-Geräte** in IoT Hub fest.

Diese Schritte sind in der IoT Edge-Dokumentation für [Windows](../iot-edge/quickstart.md) und [Linux](../iot-edge/quickstart-linux.md) beschrieben.  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Bereitstellen von ASA auf Ihren IoT Edge-Geräten
##### <a name="add-asa-to-your-deployment"></a>Hinzufügen von ASA zu Ihrer Bereitstellung
- Öffnen Sie im Azure-Portal IoT Hub, navigieren Sie zu **IoT Edge** , und klicken Sie auf das Gerät, auf das sich diese Bereitstellung beziehen soll.
- Wählen Sie **Module festlegen** , dann **+ Hinzufügen** und schließlich **Azure Stream Analytics-Modul** aus.
- Wählen Sie das Abonnement und den erstellten ASA-Edge-Auftrag aus. Klicken Sie auf Speichern.
![Hinzufügen des ASA-Moduls zu Ihrer Bereitstellung](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Während dieses Schritts erstellt ASA im Speichercontainer einen Ordner namens „EdgeJobs“, sofern dieser noch nicht vorhanden ist. Für jede Bereitstellung wird ein neuer Unterordner im Ordner „EdgeJobs“ erstellt.
> Wenn Sie Ihren Auftrag auf IoT Edge-Geräten bereitstellen, erstellt ASA eine Shared Access Signature (SAS) für die Auftragsdefinitionsdatei. Der SAS-Schlüssel wird mithilfe des Gerätezwillings sicher an die IoT Edge-Geräte übertragen. Das Ablaufdatum dieses Schlüssels liegt drei Jahre nach dem Datum seiner Erstellung. Wenn Sie einen IoT Edge-Auftrag aktualisieren, ändert sich die SAS, aber nicht die Imageversion. Halten Sie sich nach dem **Aktualisieren** an den Bereitstellungsworkflow. Auf dem Gerät wird eine Aktualisierungsbenachrichtigung protokolliert.


Weitere Informationen zu IoT Edge-Bereitstellungen finden Sie [auf dieser Seite](../iot-edge/module-deployment-monitoring.md).


##### <a name="configure-routes"></a>Konfigurieren von Routen
IoT Edge bietet die Möglichkeit zum deklarativen Weiterleiten von Nachrichten zwischen Modulen sowie zwischen Modulen und IoT Hub. Die vollständige Syntax ist [hier](../iot-edge/module-composition.md) beschrieben.
Namen der Eingaben und Ausgaben, die im ASA-Auftrag erstellt wurden, können als Endpunkte für das Routing verwendet werden.  

###### <a name="example"></a>Beispiel

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
Dieses Beispiel zeigt die Routen für das Szenario, das in der folgenden Abbildung skizziert ist. Es enthält einen Edge-Auftrag namens **ASA** , eine Eingabe namens **temperature** und eine Ausgabe namens **alert**.
![Beispieldiagramm zum Nachrichtenrouting](media/stream-analytics-edge/edge-message-routing-example.png)

Im Beispiel sind folgende Routen definiert:
- Jede von **tempSensor** versendete Nachricht wird an die Eingabe **temperature** des Moduls **ASA** gesendet.
- Alle Ausgaben des **ASA** -Moduls werden an den IoT Hub gesendet, der mit diesem Gerät (upstream$) verknüpft ist.
- Alle Ausgaben des **ASA** -Moduls werden an den Endpunkt **control** von **tempSensor** gesendet.


## <a name="technical-information"></a>Technische Informationen
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Aktuelle Einschränkungen bei IoT Edge-Aufträgen im Vergleich mit Cloudaufträgen
Ziel ist die Herstellung von Parität zwischen IoT Edge- und Cloudaufträgen. Die meisten Features der SQL-Abfragesprache werden unterstützt, sodass dieselbe Logik sowohl in der Cloud als auch auf IoT Edge ausgeführt werden kann.
Allerdings werden die folgenden Funktionen für Edge-Aufträge noch nicht unterstützt:
* Benutzerdefinierte Funktionen (User-defined Functions, UDF) in JavaScript. UDF sind in [C# für IoT Edge-Aufträge](./stream-analytics-edge-csharp-udf.md) (Vorschau) verfügbar.
* Benutzerdefinierte Aggregate (User-Defined Aggregates, UDA).
* Azure ML-Funktionen
* Verwendung von mehr als 14 Aggregaten in einem einzigen Schritt
* AVRO-Format für die Eingabe/Ausgabe. Gegenwärtig werden nur CSV und JSON unterstützt.
* Folgende SQL-Operatoren:
    * PARTITION BY
    * GetMetadataPropertyValue
* Richtlinie für die Eingangsverzögerung

### <a name="runtime-and-hardware-requirements"></a>Runtime- und Hardwareanforderungen
Um ASA unter IoT Edge auszuführen, benötigen Sie Geräte, die [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) ausführen können. 

ASA und Azure IoT Edge verwenden **Docker** -Container zum Bereitstellen einer portablen Lösung, die auf unterschiedlichen Hostbetriebssystemen (Windows, Linux) läuft.

ASA in IoT Edge ist als Windows- und Linux-Image sowohl für x86-64- als auch für ARM-Architekturen (Advanced RISC Machines) verfügbar. 


### <a name="input-and-output"></a>Eingabe und Ausgabe
#### <a name="input-and-output-streams"></a>Eingabe- und Ausgabestreams
ASA-Edge-Aufträge können Eingaben und Ausgaben von anderen Modulen auf IoT Edge-Geräten abrufen. Um Verbindungen von und mit bestimmten Modulen herzustellen, können Sie die Routingkonfiguration zum Zeitpunkt der Bereitstellung festlegen. Weitere Informationen finden Sie in der [Dokumentation zur IoT Edge-Modulzusammenstellung](../iot-edge/module-composition.md).

Für Ein- und Ausgaben werden die Formate CSV und JSON unterstützt.

Für jeden Eingabe- und Ausgabestream, die Sie in Ihrem ASA-Auftrag erstellen, wird ein entsprechender Endpunkt in Ihrem bereitgestellten Modul erstellt. Diese Endpunkte können in den Routen Ihrer Bereitstellung verwendet werden.

Derzeit werden nur Edge Hub-Typen der Eingabe- und Ausgabestreams unterstützt. Die Referenzeingabe unterstützt den Dateityp des Verweises. Andere Ausgaben lassen sich durch das Verwenden eines nachgeschalteten Cloudauftrags erreichen. Beispielsweise sendet ein in Edge gehosteter Stream Analytics-Auftrag eine Ausgabe an eine Edge Hub-Instanz, die sie dann an IoT Hub senden kann. Sie können einen zweiten in der Cloud gehosteten Azure Stream Analytics-Auftrag mit einer Eingabe aus IoT Hub verwenden und als Power BI-Ausgabe oder anderen Ausgabetyp erfolgen lassen.



##### <a name="reference-data"></a>Verweisdaten
Verweisdaten (auch als Nachschlagetabellen bezeichnet) sind eine begrenzte Menge von Daten, die statisch sind oder sich nur langsam ändern. Sie werden zum Nachschlagen oder Korrelieren mit Ihrem Datenstrom verwendet. Für den Einsatz von Verweisdaten in Ihrem Azure Stream Analytics-Auftrag verwenden Sie in Ihrer Abfrage in der Regel [Verweisdaten für JOIN-Vorgänge](/stream-analytics-query/reference-data-join-azure-stream-analytics). Weitere Informationen finden Sie unter[Verwenden von Referenzdaten für Suchvorgänge in Stream Analytics](stream-analytics-use-reference-data.md).

Es werden nur lokale Verweisdaten unterstützt. Wenn ein Auftrag auf einem IoT Edge-Gerät bereitgestellt wird, werden Verweisdaten über den benutzerdefinierten Dateipfad geladen.

Erstellen Sie in Edge wie folgt einen Auftrag mit Verweisdaten:

1. Erstellen Sie eine neue Eingabe für Ihren Auftrag.

2. Wählen Sie **Verweisdaten** als **Quelltyp** aus.

3. Halten Sie auf dem Gerät eine Verweisdatendatei bereit. Ordnen Sie die Verweisdatendatei für einen Windows-Container auf dem lokalen Laufwerk an, und geben Sie das lokale Laufwerk mit dem Docker-Container frei. Erstellen Sie für einen Linux-Container ein Docker-Volume, und fügen Sie die Datendatei auf dem Volume ein.

4. Legen Sie den Dateipfad fest. Verwenden Sie bei einem Windows-Hostbetriebssystem und Windows-Containern den absoluten Pfad: `E:\<PathToFile>\v1.csv`. Verwenden Sie bei einem Windows-Hostbetriebssystem und Linux-Containern oder einem Linux-Betriebssystem mit Linux-Containern den Pfad im Volume: `<VolumeName>/file1.txt`.

![Neue Verweisdateneingabe für Azure Stream Analytics-Auftrag in IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Die Aktualisierung der Verweisdaten in IoT Edge wird durch eine Bereitstellung ausgelöst. Nach dem Auslösen wählt das ASA-Modul die aktualisierten Daten aus, ohne die Ausführung des Auftrags zu beenden.

Es gibt zwei Möglichkeiten, die Verweisdaten zu aktualisieren:
* Aktualisieren des Verweisdatenpfads für Ihren ASA-Auftrag im Azure-Portal
* Aktualisieren der IoT Edge-Bereitstellung

## <a name="license-and-third-party-notices"></a>Lizenz- und Drittanbieterhinweise
* [Azure Stream Analytics auf IoT Edge-Lizenz](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Drittanbieterhinweis zu Azure Stream Analytics auf IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informationen zum Azure Stream Analytics-Modulimage 

Diese Versionsinformationen wurden zuletzt am 27.06.2019 aktualisiert:

- Image: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - Basisimage: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - Plattform:
      - Architektur: amd64
      - Betriebssystem: Linux
 
- Image: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - Basisimage: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - Plattform:
      - Architektur: ARM
      - Betriebssystem: Linux
 
- Image: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - Basisimage: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - Plattform:
      - Architektur: arm64
      - Betriebssystem: Linux
      
      
## <a name="get-help"></a>Hier erhalten Sie Hilfe
Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Tutorial zu ASA unter IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Entwickeln von Stream Analytics-Edge-Aufträgen mit Visual Studio-Tools](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Implementieren von CI/CD für Stream Analytics mithilfe von APIs](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/