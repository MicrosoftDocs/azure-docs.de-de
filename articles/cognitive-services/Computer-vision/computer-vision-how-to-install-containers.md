---
title: Installieren von Read-OCR-Docker-Containern des maschinellen Sehens
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Read-OCR-Docker-Container des maschinellen Sehens, um lokal Text aus Bildern und Dokumenten zu extrahieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: Lokal, OCR, Docker, Container
ms.openlocfilehash: fc5d281a6c0c9dd9620109de9d8deea27462ad0e
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008586"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Installieren von Read-OCR-Docker-Containern (Vorschau) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Container ermöglichen Ihnen, die APIs für maschinelles Sehen in Ihrer eigenen Umgebung auszuführen. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen. In diesem Artikel erfahren Sie, wie Sie Container für maschinelles Sehen herunterladen, installieren und ausführen.

Mit dem *Read*-OCR-Container können Sie gedruckten und handschriftlichen Text aus Bildern und Dokumenten mit Unterstützung für die Dateiformate JPEG, PNG, BMP, PDF und TIFF extrahieren. Weitere Informationen finden Sie in der Dokumentation zur [Lese-API](concept-recognizing-text.md#read-api).

## <a name="read-32-preview-container"></a>Read 3.2-Vorschaucontainer

> [!NOTE]
> Der Read 3.0-Vorschaucontainer wurde als veraltet markiert. 

Der Read 3.2-OCR-Vorschaucontainer bietet Folgendes:
* Neue Modelle für erweiterte Genauigkeit
* Unterstützung für mehrere Sprachen innerhalb desselben Dokuments
* Unterstützung für insgesamt 73 Sprachen Eine vollständige Liste finden Sie unter [Sprachunterstützung für maschinelles Sehen](./language-support.md#optical-character-recognition-ocr).
* Ein einzelner Vorgang für Dokumente und Bilder
* Unterstützung für größere Dokumente und Bilder
* Zuverlässigkeitsbewertungen
* Unterstützung für Dokumente mit gedrucktem und handgeschriebenem Text
* Möglichkeit, Text nur von ausgewählten Seiten in einem Dokument zu extrahieren
* Wählen Sie für die Reihenfolge der Textzeilenausgabe statt der Standardrichtung eine natürlichere Leserichtung aus.
* Klassifizierung der Textzeilen als handschriftlich oder nicht nur für lateinische Sprachen

Wenn Sie heute Read 2.0-Container verwenden, lesen Sie den [Migrationsleitfaden](read-container-migration-guide.md), um sich über die Änderungen in den neuen Versionen zu informieren.

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung der Container müssen die folgenden Voraussetzungen erfüllt sein:

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen.| 
|Ressource für maschinelles Sehen |Um den Container zu verwenden, benötigen Sie Folgendes:<br><br>Eine Azure-Ressource vom Typ **Maschinelles Sehen** sowie den zugehörigen API-Schlüssel und Endpunkt-URI. Beide Werte stehen auf der Übersichts- und auf der Schlüsselseite der Ressource zur Verfügung und werden zum Starten des Containers benötigt.<br><br>**{API_KEY}** : Einer der beiden verfügbaren Ressourcenschlüssel auf der Seite **Schlüssel**<br><br>**{ENDPOINT_URI}** : Der Endpunkt, der auf der Seite **Übersicht** angegeben ist|

Wenn Sie kein Azure-Abonnement haben, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/), bevor Sie beginnen.

## <a name="request-approval-to-run-the-container"></a>Anfordern der Genehmigung für die Containerausführung

Füllen Sie das [Anforderungsformular](https://aka.ms/csgate) aus, und übermitteln Sie es, um die Genehmigung für die Containerausführung anzufordern. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Unterstützung von Advanced Vector Extensions

Der **Hostcomputer** ist der Computer, auf dem der Docker-Container ausgeführt wird. Der Host *muss* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) unterstützen. Sie können die AVX2-Unterstützung auf Linux-Hosts mit dem folgenden Befehl überprüfen:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> Der Hostcomputer *muss* AVX2 unterstützen. Ohne AVX2-Unterstützung funktioniert der Container *nicht* ordnungsgemäß.

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Für das Lesen stehen Containerimages zur Verfügung.

| Container | Container Registry/Repository/Imagename |
|-----------|------------|
| Read 2.0-preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Read 3.2-preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2` |

Verwenden Sie den Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), um ein Containerimage herunterzuladen.

### <a name="docker-pull-for-the-read-container"></a>„docker pull“ für den Container für das Lesen

# <a name="version-32-preview"></a>[Version 3.2 (Vorschauversion)](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2
```

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen Abrechnungseinstellungen. Es sind noch weitere [Beispiele](computer-vision-resource-container-config.md) für den Befehl `docker run` verfügbar. 
1. [Fragen Sie den Vorhersageendpunkt des Containers ab.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um den Container auszuführen. Genaue Informationen dazu, wie Sie die Werte `{ENDPOINT_URI}` und `{API_KEY}` abrufen, erhalten Sie unter [Ermitteln erforderlicher Parameter](#gathering-required-parameters).

Es sind [Beispiele](computer-vision-resource-container-config.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.

# <a name="version-32-preview"></a>[Version 3.2 (Vorschauversion)](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt den Container für das Lesen aus dem Containerimage aus
* Ordnet 8 CPU-Kerne und 18 GB Arbeitsspeicher zu.
* Macht den TCP-Port 5000 verfügbar und ordnet eine Pseudo-TTY-Verbindung für den Container zu.
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt den Container für das Lesen aus dem Containerimage aus
* Ordnet 8 CPU-Kerne und 16 GB Arbeitsspeicher zu
* Macht den TCP-Port 5000 verfügbar und ordnet eine Pseudo-TTY-Verbindung für den Container zu.
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

---


Es sind noch weitere [Beispiele](./computer-vision-resource-container-config.md#example-docker-run-commands) für den Befehl `docker run` verfügbar. 

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).

Wenn Sie einen höheren Durchsatz (beispielsweise bei der Verarbeitung mehrseitiger Dateien) benötigen, ziehen Sie die Bereitstellung mehrerer Container [in einem Kubernetes-Cluster](deploy-computer-vision-on-premises.md) mithilfe von [Azure Storage](../../storage/common/storage-account-create.md) und [Azure Queue](../../storage/queues/storage-queues-introduction.md) in Betracht.

Wenn Sie Azure Storage zum Speichern von Images für die Verarbeitung verwenden, können Sie eine [Verbindungszeichenfolge](../../storage/common/storage-configure-connection-string.md) erstellen, die beim Aufrufen des Containers verwendet werden soll.

So finden Sie die Verbindungszeichenfolge:

1. Navigieren Sie im Azure-Portal zu **Speicherkonten**, und suchen Sie Ihr Konto.
2. Klicken Sie in der linken Navigationsliste auf **Zugriffsschlüssel**.
3. Die Verbindungszeichenfolge befindet sich unterhalb von **Verbindungszeichenfolge**.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit. 

# <a name="version-32-preview"></a>[Version 3.2 (Vorschauversion)](#tab/version-3-2)

Verwenden Sie für Container-APIs den Host `http://localhost:5000`. Sie können den Swagger-Pfad unter `http://localhost:5000/swagger/vision-v3.2-preview-read/swagger.json` anzeigen.

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

Verwenden Sie für Container-APIs den Host `http://localhost:5000`. Sie können den Swagger-Pfad unter `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` anzeigen.

---

### <a name="asynchronous-read"></a>Asynchrones Lesen


# <a name="version-32-preview"></a>[Version 3.2 (Vorschauversion)](#tab/version-3-2)

Sie können die Vorgänge `POST /vision/v3.2/read/analyze` und `GET /vision/v3.2/read/operations/{operationId}` kombiniert verwenden, um asynchron in einem Bild zu lesen, ähnlich wie der Dienst für maschinelles Sehen die entsprechenden REST-Vorgänge verwendet. Die asynchrone POST-Methode gibt eine `operationId` zurück, die als Bezeichner für die HTTP GET-Anforderung verwendet wird.


Wählen Sie auf der Swagger-Benutzeroberfläche `Analyze` aus, um die Option im Browser zu erweitern. Wählen Sie dann **Jetzt ausprobieren** > **Datei auswählen** aus. In diesem Beispiel verwenden wir das folgende Bild:

![Tabstopps und Leerzeichen](media/tabs-vs-spaces.png)

Wenn die asynchrone POST-Anforderung erfolgreich ausgeführt wurde, gibt sie den Statuscode **HTTP 202** zurück. Teil der Antwort ist ein `operation-location`-Header, der den Ergebnisendpunkt für die Anforderung enthält.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

Die `operation-location` ist die vollqualifizierte URL, auf die über HTTP GET zugegriffen wird. Hier ist die JSON-Antwort der Ausführung der URL `operation-location` aus dem vorherigen Bild:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

Sie können die Vorgänge `POST /vision/v2.0/read/core/asyncBatchAnalyze` und `GET /vision/v2.0/read/operations/{operationId}` kombiniert verwenden, um asynchron in einem Bild zu lesen, ähnlich wie der Dienst für maschinelles Sehen die entsprechenden REST-Vorgänge verwendet. Die asynchrone POST-Methode gibt eine `operationId` zurück, die als Bezeichner für die HTTP GET-Anforderung verwendet wird.

Wählen Sie auf der Swagger-Benutzeroberfläche `asyncBatchAnalyze` aus, um die Option im Browser zu erweitern. Wählen Sie dann **Jetzt ausprobieren** > **Datei auswählen** aus. In diesem Beispiel verwenden wir das folgende Bild:

![Tabstopps und Leerzeichen](media/tabs-vs-spaces.png)

Wenn die asynchrone POST-Anforderung erfolgreich ausgeführt wurde, gibt sie den Statuscode **HTTP 202** zurück. Teil der Antwort ist ein `operation-location`-Header, der den Ergebnisendpunkt für die Anforderung enthält.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

Die `operation-location` ist die vollqualifizierte URL, auf die über HTTP GET zugegriffen wird. Hier ist die JSON-Antwort der Ausführung der URL `operation-location` aus dem vorherigen Bild:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> Wenn Sie mehrere Lesecontainer hinter einem Lastenausgleich bereitstellen (z. B. unter Docker Compose oder Kubernetes), benötigen Sie einen externen Cache. Da der Verarbeitungscontainer und der GET-Anforderungscontainer möglicherweise nicht identisch sind, werden die Ergebnisse in einem externen Cache gespeichert und dort für die Container freigegeben. Ausführliche Informationen zu Cacheeinstellungen finden Sie unter [Konfigurieren von Docker-Containern für maschinelles Sehen](./computer-vision-resource-container-config.md).

### <a name="synchronous-read"></a>Synchrones Lesen

Mit dem folgenden Vorgang können Sie ein Bild synchron lesen: 

# <a name="version-32-preview"></a>[Version 3.2 (Vorschauversion)](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Wenn das Bild vollständig gelesen wird, dann – und nur dann – gibt die API eine JSON-Antwort zurück. Die einzige Ausnahme hierzu ist das Auftreten eines Fehlers. Bei einem Fehler wird der folgende JSON-Code zurückgegeben:

```json
{
    "status": "Failed"
}
```

Das JSON-Antwortobjekt enthält dasselbe Objektdiagramm wie die asynchrone Version. Wenn Sie ein JavaScript-Benutzer sind und Typsicherheit wünschen, können Sie die JSON-Antwort mithilfe von TypeScript umwandeln.

Ein Beispiel für einen Anwendungsfall finden Sie in <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">dieser TypeScript-Sandbox <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Wählen Sie **Ausführen** aus, um die Benutzerfreundlichkeit visuell darzustellen.

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](./computer-vision-resource-container-config.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Abrechnung

Die Cognitive Services-Container senden Abrechnungsinformationen an Azure und verwenden dafür die entsprechende Ressource in Ihrem Azure-Konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](./computer-vision-resource-container-config.md).

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für maschinelles Sehen kennengelernt. Zusammenfassung:

* Für das maschinelle Sehen ist ein Linux-Container für Docker verfügbar, der das Lesen kapselt.
* Containerimages werden aus der Containerregistrierung „Containervorschau“ in Azure heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Lesecontainern über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (etwa das analysierte Bild oder den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](computer-vision-resource-container-config.md).
* Lesen Sie [Übersicht über maschinelles Sehen](overview.md), um weitere Informationen zur Erkennung von gedrucktem und handschriftlichem Text zu erhalten.
* Details zu den vom Container unterstützten Methoden finden Sie unter [Maschinelles Sehen-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b).
* Unter [Häufig gestellte Fragen (FAQ)](FAQ.md) finden Sie Informationen zum Beheben von Problemen im Zusammenhang mit der Funktionalität des maschinellen Sehens.
* Verwenden weiterer [Cognitive Services-Container](../cognitive-services-container-support.md)
