---
title: Installieren und Ausführen von Docker-Containern für die Textanalyse-API
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Docker-Container für die Textanalyse-API, um die Verarbeitung natürlicher Sprache, z. B. Stimmungsanalysen, lokal durchzuführen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
keywords: Lokal, Docker, Container, Stimmungsanalyse, Verarbeitung natürlicher Sprache
ms.openlocfilehash: 088cf407320dcf8ef6705c57d86836531d9bdfe6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965165"
---
# <a name="install-and-run-text-analytics-containers"></a>Installieren und Ausführen von Containern für die Textanalyse

> [!NOTE]
> * Der Container für Standpunktanalyse v3 ist nun allgemein verfügbar. Die Container für Schlüsselbegriffserkennung und Sprachenerkennung sind als nicht geschlossene öffentliche Vorschau verfügbar.
> * Entitätsverknüpfung und NER stehen derzeit nicht als Container zur Verfügung.
> * Für den Zugriff auf die Textanalyse für Integritätscontainer ist ein [Anfrageformular](https://aka.ms/csgate) erforderlich. Derzeit wird Ihnen die Nutzung nicht in Rechnung gestellt.
> * Die Speicherorte für Containerimages haben sich möglicherweise vor Kurzem geändert. Lesen Sie diesen Artikel, um mehr zum aktualisierten Speicherort für diesen Container zu erfahren.

Container ermöglichen es Ihnen, die Textanalyse-APIs in ihrer eigenen Umgebung auszuführen und eignen sich hervorragend für Ihre spezifischen Anforderungen bezüglich Sicherheit und Datengovernance. Container für die Textanalyse ermöglichen eine erweiterte Verarbeitung von natürlicher Sprache anhand von unformatiertem Text und bieten drei Hauptfunktionen: Standpunktanalyse, Schlüsselbegriffserkennung und Sprachenerkennung. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

> [!IMPORTANT]
> Das kostenlose Konto ist auf 5.000 Transaktionen pro Monat beschränkt, und für Container sind nur die <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">Tarife <span class="docon docon-navigate-external x-hidden-focus"></span></a> **Free** und **Standard** gültig. Weitere Informationen zu den Transaktionsanforderungsraten finden Sie unter [Datengrenzwerte](../overview.md#data-limits).

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen eines Containers für die Textanalyse benötigen Sie den Hostcomputer und Containerumgebungen.

## <a name="preparation"></a>Vorbereitung

Zur Verwendung des Containers für die Textanalyse müssen die folgenden Voraussetzungen erfüllt sein:

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen.| 
|Textanalyseressource |Um den Container zu verwenden, benötigen Sie Folgendes:<br><br>Eine Azure-Ressource vom Typ [Textanalyse](../../cognitive-services-apis-create-account.md), um den entsprechenden API-Schlüssel und den URI des Endpunkts zu erhalten. Beide Werte stehen im Azure-Portal auf der Übersichts- und auf der Schlüsselseite der Textanalyse zur Verfügung und werden zum Starten des Containers benötigt.<br><br>**{API_KEY}** : Einer der beiden verfügbaren Ressourcenschlüssel auf der Seite **Schlüssel**<br><br>**{ENDPOINT_URI}** : Der Endpunkt, der auf der Seite **Übersicht** angegeben ist|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

In der folgenden Tabelle werden die minimalen und empfohlenen Spezifikationen für die Textanalysecontainer beschrieben. Mindestens 2 Gigabyte (GB) Arbeitsspeicher sind erforderlich, und jeder CPU-Kern muss eine Geschwindigkeit von mindestens 2,6 Gigahertz (GHz) aufweisen. Die zulässigen Transaktionen pro Abschnitt (Transactions Per Section, TPS) sind ebenfalls aufgeführt.

|  | Mindestspezifikationen für Hosts | Empfohlene Hostspezifikationen | Mindestanzahl von TPS | Maximale Anzahl von TPS|
|---|---------|-------------|--|--|
| **Sprachenerkennung, Schlüsselbegriffserkennung**   | 1 Kern, 2 GB Arbeitsspeicher | 1 Kern, 4 GB Arbeitsspeicher |15 | 30|
| **Standpunktanalyse v3**   | 1 Kern, 2 GB Arbeitsspeicher | 4 Kerne, 8 GB Arbeitsspeicher |15 | 30|
| **Text Analytics for Health: 1 Dokument/Anforderung**   |  4 Kerne, 10 GB Arbeitsspeicher | 6 Kerne, 12 GB Arbeitsspeicher |15 | 30|
| **Text Analytics for Health: 10 Dokumente/Anforderungen**   |  6 Kerne, 16 GB Arbeitsspeicher | 8 Kerne, 20 GB Arbeitsspeicher |15 | 30|

CPU-Kernanzahl und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

In der Microsoft Container Registry stehen Containerimages für die Textanalyse zur Verfügung.

# <a name="sentiment-analysis-v3"></a>[Standpunktanalyse v3](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Schlüsselbegriffserkennung (Vorschauversion)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Sprachenerkennung (Vorschauversion)](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Text Analytics for Health (Vorschau)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

**_

## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen Abrechnungseinstellungen.
1. [Fragen Sie den Vorhersageendpunkt des Containers ab.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um die Container auszuführen. Der Container wird so lange ausgeführt, bis Sie ihn beenden.

> [!IMPORTANT]
> In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. 
> * Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).
> * Der Container für die Standpunktanalyse v3 ist jetzt allgemein verfügbar. Dadurch werden in der Antwort [Stimmungsbezeichnungen](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) zurückgegeben. Die Container für die Schlüsselbegriffserkennung und die Sprachenerkennung verwenden Version 2 der API und befinden sich in der Vorschauphase.

# <a name="sentiment-analysis-v3"></a>[Standpunktanalyse v3](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Schlüsselbegriffserkennung (Vorschauversion)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Sprachenerkennung (Vorschauversion)](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Text Analytics for Health (Vorschau)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit.

Verwenden Sie für Container-APIs den Host `http://localhost:5000`.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](../text-analytics-resource-container-config.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Abrechnung

Der Container für die Textanalyse sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ _Textanalyse_ in Ihrem Azure-Konto. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für die Textanalyse kennengelernt. Zusammenfassung:

* Textanalyse stellt drei Linux-Container für Docker bereit, die verschiedene Funktionen beinhalten:
   * *Standpunktanalyse*
   * *Schlüsselbegriffserkennung (Vorschauversion)* 
   * *Sprachenerkennung (Vorschauversion)*
   * *Text Analytics for Health (Vorschau)*
* Containerimages werden aus der Microsoft Container Registry (MCR) oder einem Repository für Vorschaucontainer heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Containern für die Textanalyse über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z. B. den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](../text-analytics-resource-container-config.md).
* Unter [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md) finden Sie Informationen zum Beheben von Problemen im Zusammenhang mit der Funktion.
