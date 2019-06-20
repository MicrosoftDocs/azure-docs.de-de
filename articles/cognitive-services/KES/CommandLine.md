---
title: Befehlszeilenschnittstelle – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Mithilfe der Befehlszeilenschnittstelle können Sie Index- und Grammatikdateien aus strukturierten Daten erstellen und diese dann als Webdienste bereitstellen.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815141"
---
# <a name="command-line-interface"></a>Befehlszeilenschnittstelle

Über die KES-Befehlszeilenschnittstelle (Knowledge Exploration Service) können Index- und Grammatikdateien aus strukturierten Daten erstellt und dann als Webdienste bereitgestellt werden.  Die allgemeine Syntax sieht wie folgt aus: `kes.exe <command> <required_args> [<optional_args>]`.  Sie können `kes.exe` ohne Argumente ausführen, um eine Liste von Befehlen anzuzeigen, oder `kes.exe <command>`, um eine Liste von Argumenten anzuzeigen, die für den angegebenen Befehl verfügbar sind.  Im Folgenden finden Sie eine Liste der verfügbaren Befehle:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>build_index-Befehl

Der Befehl **build_index** erstellt eine Binärindexdatei aus einer Schemadefinitionsdatei und einer Datendatei von zu indizierenden Objekten.  Mit der resultierenden Indexdatei können strukturierte Abfrageausdrücke ausgewertet oder Interpretationen von Abfragen in natürlicher Sprache in Verbindung mit einer kompilierten Grammatikdatei generiert werden.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parameter      | BESCHREIBUNG               |
|----------------|---------------------------|
| `<schemaFile>` | Eingabeschemapfad |
| `<dataFile>`   | Eingabedatenpfad   |
| `<indexFile>`  | Ausgabeindexpfad |
| `--description <description>` | Beschreibungszeichenfolge |
| `--remote <vmSize>`           | VM-Größe für das Remotebuild |

Diese Dateien können durch lokale Datei- oder URL-Pfade zu Azure-Blobs angegeben werden.  Die Schemadatei beschreibt die Struktur der Objekte, die indiziert werden, sowie die zu unterstützenden Vorgänge werden (siehe [Schemaformat](SchemaFormat.md)).  Die Datendatei listet die Objekte und Attributwerte auf, die zu indizieren sind (siehe [Datenformat](DataFormat.md)).  Wenn der Build erfolgreich ausgeführt wird, enthält die Ausgabeindexdatei eine komprimierte Darstellung der Eingabedaten, die die gewünschten Vorgänge unterstützen.  

Mithilfe des Befehls **describe_index** kann eine Beschreibungszeichenfolge optional angegeben werden, um anschließend einen Binärindex zu identifizieren.  

Standardmäßig wird der Index auf dem lokalen Computer erstellt.  Außerhalb der Azure-Umgebung sind lokale Builds auf Datendateien mit bis zu 10.000 Objekten beschränkt.  Wenn das --remote-Flag angegeben ist, wird der Index auf einer temporär erstellten Azure-VM mit der angegebenen Größe erstellt.  Dadurch können mit Azure-VMs mit mehr Arbeitsspeicher effizient große Indizes erstellt werden.  Um eine Auslagerung zu vermeiden, die den Buildprozess verlangsamt, wird empfohlen, eine VM mit einem 3-fach größeren RAM als der Eingabedatendatei zu verwenden.  Eine Liste der verfügbaren VM-Größen finden Sie unter [Größen für virtuelle Computer](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Um Builds zu beschleunigen, sortieren Sie die Objekte in der Datendatei nach sinkender Wahrscheinlichkeit vor.

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>build_grammar-Befehl

Der **build_grammar**-Befehl kompiliert eine im XML-Format angegebene Grammatik für eine binäre Grammatikdatei.  Die resultierende Grammatikdatei kann in Verbindung mit eine Indexdatei verwendet werden, um Interpretationen von Abfragen in natürlicher Sprache zu generieren.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parameter       | BESCHREIBUNG               |
|-----------------|---------------------------|
| `<xmlFile>`     | Eingabepfad der XML-Grammatikspezifikation |
| `<grammarFile>` | Ausgabepfad der kompilierten Grammatik         |

Diese Dateien können durch lokale Datei- oder URL-Pfade zu Azure-Blobs angegeben werden.  Die Grammatikspezifikation beschreibt die Gruppe von gewichteten Ausdrücken in natürlicher Sprache und ihre semantischen Interpretationen (siehe [Grammatikformat](GrammarFormat.md)).  Wenn der Build erfolgreich ausgeführt wird, enthält die Ausgabegrammatikdatei eine binäre Darstellung der Grammatikspezifikation, um eine schnelle Decodierung zu ermöglichen.

<a name="host_service-command"/>

## <a name="hostservice-command"></a>host_service-Befehl

Der Befehl **host_service** hostet eine Instanz des KES-Dienstes auf dem lokalen Computer.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parameter       | BESCHREIBUNG                |
|-----------------|----------------------------|
| `<grammarFile>` | Eingabepfad der Binärgrammatik         |
| `<indexFile>`   | Eingabepfad des Binärindex           |
| `--port <port>` | Lokale Portnummer.  Standardwert: 8.000 |

Diese Dateien können durch lokale Datei- oder URL-Pfade zu Azure-Blobs angegeben werden.  Ein Webdienst wird unter http://localhost:&lt ;port&gt; / gehostet.  Eine Liste der unterstützten Vorgänge finden Sie unter [Web-APIs](WebAPI.md).

Außerhalb der Azure-Umgebung sind lokal gehostete Dienste auf Indexdateien mit einer Größe von bis zu 1 MB, 10 Anforderungen pro Sekunde und insgesamt 1000 Aufrufe beschränkt.  Um diese Einschränkungen zu umgehen, führen Sie **host_service** auf einer Azure-VM aus, oder stellen Sie sie mit **deploy_service** für einen Azure-Clouddienst bereit.

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>deploy_service-Befehl

Der Befehl **deploy_service** stellt eine Instanz des KES-Diensts für einen Azure-Clouddienst bereit.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parameter       | BESCHREIBUNG                  |
|-----------------|------------------------------|
| `<grammarFile>` | Eingabepfad der Binärgrammatik           |
| `<indexFile>`   | Eingabepfad des Binärindex             |
| `<serviceName>` | Name des Zielclouddiensts |
| `<vmSize>`      | Größe der VM für Clouddienste     |
| `--slot <slot>` | Clouddienstslot: „staging“ (Standard), „production“ |

Diese Dateien können durch lokale Datei- oder URL-Pfade zu Azure-Blobs angegeben werden.  Dienstnamen geben einen vorkonfigurierten Azure-Clouddienst an (siehe [Erstellen und Bereitstellen eines Clouddiensts](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Der Befehl stellt automatisch den KES-Dienst über VMs mit der angegebenen Größe für den angegebenen Azure-Clouddienst bereit.  Um eine Auslagerung zu vermeiden, die die Leistung erheblich beeinträchtigt, wird empfohlen, eine VM mit einen um 1 GB größeren Arbeitsspeicher als der Eingabeindexdatei zu verwenden.  Eine Liste der verfügbaren VM-Größen finden Sie unter [Größen für Clouddienste](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Standardmäßig wird der Dienst für die Stagingumgebung bereitgestellt und optional mit dem --slot-Parameter überschrieben.  Eine Liste der unterstützten Vorgänge finden Sie unter [Web-APIs](WebAPI.md).

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>describe_index-Befehl

Der Befehl **describe_index** gibt Informationen über eine Indexdatei aus, wie etwa Schema und Beschreibung.

`kes.exe describe_index <indexFile>`

| Parameter     | BESCHREIBUNG      |
|---------------|------------------|
| `<indexFile>` | Eingabeindexpfad |

Diese Datei kann durch einen lokalen oder URL-Pfad zu einem Azure-Blob angegeben werden.  Die Ausgabebeschreibungszeichenfolge kann mithilfe des --description-Parameters des **build_index**-Befehls angegeben werden.

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>describe_grammar-Befehl

Der **describe_grammar**-Befehl gibt die ursprüngliche Grammatikspezifikation aus, die zum Erstellen der Binärgrammatik verwendet wird.

`kes.exe describe_grammar <grammarFile>`

| Parameter       | BESCHREIBUNG      |
|-----------------|------------------|
| `<grammarFile>` | Eingabegrammatikpfad |

Diese Datei kann durch einen lokalen oder URL-Pfad zu einem Azure-Blob angegeben werden.

