---
title: Azure Stream Analytics – Vorschaufeatures
description: Dieser Artikel beschreibt die Azure Stream Analytics-Features, die sich derzeit in der Vorschau befinden.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e63937cedf44b1642e091a4744d898a26422be10
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393685"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics – Vorschaufeatures

Dieser Artikel fasst alle Features zusammen, die sich derzeit in der Vorschau für Azure Stream Analytics befinden. Die Verwendung von Vorschaufeatures in einer Produktionsumgebung wird nicht empfohlen.

## <a name="public-previews"></a>Öffentliche Vorschauversionen

Die folgenden Features befinden sich in der öffentlichen Vorschau. Sie können diese Features heute nutzen, sollten sie aber nicht in Ihrer Produktionsumgebung einsetzen.

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Visual Studio Code für Azure Stream Analytics (veröffentlicht im Mai 2019)

Azure Stream Analytics-Aufträge können in Visual Studio Code erstellt werden. Siehe [Visual Studio Code-Schnellstart](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Erkennung von Anomalien

Azure Stream Analytics stellt neue Machine Learning-Modelle vor, die neben der bidirektionalen, langsam positiven und langsam negativen Trenderkennung auch die Erkennung von *Spikes* und *Dips* unterstützen. Weitere Informationen finden Sie auf der Seite [Anomalieerkennung in Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="integration-with-azure-machine-learning"></a>Integration mit Azure Machine Learning

Sie können Stream Analytics-Aufträge mit Machine Learning (ML)-Funktionen skalieren. Um weitere Informationen dazu, wie Sie ML-Funktionen in Ihrem Stream Analytics-Auftrag verwenden können, finden Sie unter [skalieren ein Stream Analytics-Auftrags mit Azure Machine Learning-Funktionen](stream-analytics-scale-with-machine-learning-functions.md). Schauen Sie sich ein praktisches Szenario an in [Durchführen von Standpunktanalysen mit Azure Stream Analytics und Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Benutzerdefinierte Aggregate in JavaScript

Azure Stream Analytics unterstützt in JavaScript geschriebene, benutzerdefinierte Aggregate (UDA), mit deren Hilfe Sie komplexe zustandsbehaftete Geschäftslogik implementieren können. Weitere Informationen zur Verwendung von UDAs finden Sie in der Dokumentation [Azure Stream Analytics – benutzerdefinierte JavaScript-Aggregate](stream-analytics-javascript-user-defined-aggregates.md) Dokumentation. 

### <a name="live-data-testing-in-visual-studio"></a>Livedatentests in Visual Studio

Visual Studio-Tools für Azure Stream Analytics erweitern die lokale Testfunktion, mit der Sie Ihre Abfragen anhand von Liveereignisstreams aus Cloudquellen wie Event Hub oder IoT Hub testen können. Erfahren Sie mehr über [Lokales Testen von Livedaten mithilfe von Azure Stream Analytics-Tools für Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Benutzerdefinierte NET-Funktionen auf IoT Edge

Mit benutzerdefinierten .NET-Standardfunktionen können Sie .NET-Standardcode als Teil Ihrer Streamingpipeline ausführen. Sie können einfache C#-Klassen erstellen oder komplette Projekte und Bibliotheken importieren. In Visual Studio wird das vollständige Erstellen von Dokumenten und Debuggen unterstützt. Weitere Informationen finden Sie unter [Entwickeln von benutzerdefinierten .NET Standard-Funktionen für Azure Stream Analytics Edge-Aufträge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Weitere Previews

Die folgenden Features stehen ebenfalls in der Preview zur Verfügung.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>Benutzerdefinierter C#-Deserialisierer für Azure Stream Analytics für IoT Edge und Clouds (im Mai 2019 angekündigt)

Entwickler können benutzerdefinierte Deserialisierer in C# implementieren, um Ereignisse zu deserialisieren, die von Azure Stream Analytics empfangen werden. Beispiele für Formate, die deserialisiert werden können, sind Parquet, Protobuf, XML oder ein beliebiges Binärformat. [Hier](https://aka.ms/asapreview1) können Sie sich für diese Preview anmelden.

### <a name="parquet-output-announced-may-2019"></a>Parquet Output (im Mai 2019 angekündigt)
Parquet ist ein Spaltenformat für die Big Data-Verarbeitung. Durch die Ausgabe von Daten im Parquet-Format in einem Data Lake können Sie Azure Stream Analytics nutzen, um umfangreiches Streaming-ETL zu betreiben und Batchverarbeitung auszuführen, Machine Learning-Algorithmen zu trainieren oder interaktive Abfragen Ihrer Verlaufsdaten auszuführen. [Hier](https://aka.ms/asapreview1) können Sie sich für diese Preview anmelden.

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>One-Click-Integration in Event Hubs (im Mai 2019 angekündigt) 
Mit dieser Integration können Sie eingehende Daten visualisieren und mit einem Klick im Event Hub-Portal beginnen, eine Stream Analytics-Abfrage zu schreiben. Sobald Ihre Abfrage fertiggestellt ist, können Sie sie mit nur wenigen Klicks als Produkt bereitstellen und Erkenntnisse in Echtzeit erhalten. Dadurch wird der Zeit- und Kostenaufwand für die Entwicklung von Lösungen für Echtzeitanalysen wesentlich verringert. [Hier](https://aka.ms/asapreview1) können Sie sich für diese Preview anmelden.

### <a name="support-for-azure-stack-announced-may-2019"></a>Unterstützung von Azure Stack (im Mai 2019 angekündigt)
Mit der Aktivierung dieses Features für die Azure IoT Edge-Runtime werden benutzerdefinierte Azure Stack-Features wie die native Unterstützung lokaler Ein- und Ausgaben in Azure Stack (z. B. Event Hubs, IoT Hub, Blob Storage) genutzt. Durch diese neue Integration können Sie hybride Architekturen erstellen, die Ihre Daten nah am Erstellungsort analysieren, wodurch Wartezeiten verringert und die gewonnenen Erkenntnisse maximiert werden.
[Hier](https://aka.ms/asapreview1) können Sie sich für diese Preview anmelden.

