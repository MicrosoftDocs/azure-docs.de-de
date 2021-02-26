---
title: Horizon SDK
titleSuffix: Azure Defender for IoT
description: Mit dem Horizon SDK können Azure Defender für IoT-Entwickler Zergliederungs-Plug-Ins entwerfen, die den Netzwerkdatenverkehr decodieren, damit er von automatisierten Defender für IoT-Netzwerkanalyseprogrammen verarbeitet werden kann.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 06663e12bbcaee2243be75d6aa9ea9cf4fd125bf
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523361"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Proprietäre Horizon-Protokollzergliederung

Horizon ist eine offene Entwicklungsumgebung (Open Development Environment, ODE) zum Sichern von IoT- und ICS-Geräten, auf denen proprietäre Protokolle ausgeführt werden.

Diese Umgebung bietet Kunden und Technologiepartnern folgende Lösungen:

- Unbegrenzte, vollständige Unterstützung für allgemeine, proprietäre, benutzerdefinierte Protokolle oder Protokolle, die von einem beliebigen Standard abweichen. 

- Ein neues Maß an Flexibilität und Umfang für DPI-Entwicklung.

- Ein Tool, das die OT-Sichtbarkeit und -Steuerung exponentiell erweitert, ohne dass ein Upgrade auf neue Defender für IoT-Plattformversionen durchgeführt werden muss.

- Die Sicherheit, die eine proprietäre Entwicklung (ohne Weitergabe vertraulicher Informationen) ermöglicht.

Mit dem Horizon SDK können Azure Defender für IoT-Entwickler Zergliederungs-Plug-Ins entwerfen, die den Netzwerkdatenverkehr decodieren, damit er von automatisierten Defender für IoT-Netzwerkanalyseprogrammen verarbeitet werden kann.

Protokollzergliederungen werden als externe Plug-Ins entwickelt und sind in eine Vielzahl von Defender für IoT-Dienste integriert. Beispielsweise Dienste, die Überwachungs-, Warnungs- und Berichterstellungsfunktionen bereitstellen.

## <a name="secure-development-environment"></a>Sichere Entwicklungsumgebung 

Die Horizon ODE ermöglicht die Entwicklung von benutzerdefinierten oder proprietären Protokollen, die nicht außerhalb eines Unternehmens weitergegeben werden können. Dabei sind gesetzliche Vorschriften oder unternehmensinterne Richtlinien mögliche Gründe.

Entwickeln Sie Zergliederungs-Plug-Ins ohne Folgendes: 

- Offenlegung jeglicher proprietärer Informationen darüber, wie Ihre Protokolle definiert sind

- Freigabe einer Ihrer vertraulichen PCAPs

- Verletzung von Compliancebestimmungen

## <a name="customization-and-localization"></a>Anpassung und Lokalisierung  

Das SDK unterstützt verschiedene Anpassungsoptionen, darunter:

  - Text für Funktionscodes 

  - Vollständiger Lokalisierungstext für Warnungen, Ereignisse und Protokollparameter Weitere Informationen finden Sie unter [Erstellen von Zuordnungsdateien (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Anzeigen vollständig lokalisierter Warnungen.":::

## <a name="horizon-architecture"></a>Horizon-Architektur

Das Architekturmodell umfasst drei Produktebenen.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender für IoT-Plattformebene

Ermöglicht die sofortige Integration und Überwachung in Echtzeit von benutzerdefinierten Zergliederungs-Plug-Ins in der Defender für IoT-Plattform, ohne dass ein Upgrade der Defender für IoT-Plattformversion erforderlich ist.

## <a name="defender-for-iot-services-layer"></a>Defender für IoT-Dienstebene

Jeder Dienst ist als Pipeline konzipiert, die von einem bestimmten Protokoll entkoppelt ist und eine effizientere, unabhängige Entwicklung ermöglicht.

Jeder Dienst ist als Pipeline konzipiert, die von einem bestimmten Protokoll entkoppelt ist. Dienste lauschen auf Datenverkehr in der Pipeline. Sie interagieren mit den Plug-In-Daten und dem von den Sensoren erfassten Datenverkehr, um die bereitgestellten Protokolle zu indizieren und den Datenverkehr zu analysieren, und ermöglichen eine effizientere und unabhängige Entwicklung.

## <a name="custom-dissector-layer"></a>Benutzerdefinierte Zergliederungsebene 

Ermöglicht die Erstellung von Plug-Ins mithilfe des proprietären Defender für IoT-SDKs (einschließlich C++-Implementierung und JSON-Konfiguration) für Folgendes: 

- Definieren der Identifizierung des Protokolls

- Definieren der Zuordnung der aus dem Datenverkehr zu extrahierenden Felder und deren Extraktion 

- Definieren der Integration in die Defender für IoT-Dienste

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="Integrierte Ebenen":::

Defender für IoT bietet grundlegende Zergliederungen für allgemeine Protokolle. Sie können Ihre Zergliederungen basierend auf diesen Protokollen erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

## <a name="what-this-sdk-contains"></a>Inhalt des SDK 

Dieses Kit enthält die für die Entwicklung erforderlichen Headerdateien. Der Entwicklungsprozess erfordert grundlegende Schritte und optionale fortgeschrittene Schritte, die in diesem SDK beschrieben werden.

Wenden Sie sich an <support@cyberx-labs.com>, um Informationen zum Empfangen von Headerdateien und anderen Ressourcen zu erhalten.

## <a name="about-the-environment-and-setup"></a>Informationen zur Umgebung und zum Setup 

### <a name="requirements"></a>Anforderungen 

- Die bevorzugte Entwicklungsumgebung ist Linux. Wenn Sie in einer Windows-Umgebung entwickeln, sollten Sie die Verwendung einer VM mit einem Linux-System in Erwägung ziehen.

- Verwenden Sie für den Kompiliervorgang GCC 7.4.0 oder höher. Verwenden Sie eine beliebige Standardklasse von stdlib, die unter C++ 17 unterstützt wird.

- Defender für IoT, Version 3.0 und höher.

### <a name="process"></a>Prozess

1. [Laden](https://www.eclipse.org/) Sie die Eclipse-IDE für C/ C++-Entwickler herunter. Sie können eine beliebige andere von Ihnen bevorzugte integrierte Entwicklungsumgebung verwenden. Dieses Dokument führt Sie durch die Konfiguration unter Verwendung der Eclipse-IDE.

1. Nachdem Sie die Eclipse-IDE gestartet und den Arbeitsbereich (in dem Ihre Projekte gespeichert werden) konfiguriert haben, drücken Sie die Tastenkombination **STRG+N** und erstellen ein C++-Projekt.

1. Legen Sie auf dem nächsten Bildschirm den Namen für das Protokoll fest, das Sie entwickeln möchten, und wählen Sie den Projekttyp als `Shared Library` und `AND Linux GCC` aus.

1. Bearbeiten Sie die Projekteigenschaften unter **C/C++ Build** > **Settings** > **Tool Settings** > **GCC C++ Compiler** > **Miscellaneous** > **Tick Position Independent Code** (C/C++-Build – Einstellungen – Tooleinstellungen – GCC C++-Compiler – Sonstiges – Teilpositionsunabhängiger Code).

1. Fügen Sie die Beispielcodes ein, die Sie mit dem SDK erhalten haben, und kompilieren Sie sie.

1. Fügen Sie die Artefakte (Bibliothek, config.json und Metadaten) in eine tar.gz-Datei ein und ändern Sie die Dateierweiterung in \<XXX>.hdp, wobei \<XXX> den Namen des Plug-Ins angibt.

### <a name="research"></a>Research 

Überprüfen Sie zunächst, dass Sie Folgendes durchgeführt haben:

- Lesen Sie die Protokollspezifikation, falls verfügbar.

- Informieren Sie sich über die Protokollfelder, die Sie extrahieren möchten.

- Planen Sie Ihre Zuordnungsziele.

## <a name="about-plugin-files"></a>Informationen zu Plug-In-Dateien 

Während des Entwicklungsprozesses werden drei Dateien definiert.

### <a name="json-configuration-file-required"></a>JSON-Konfigurationsdatei (erforderlich) 

Diese Datei sollte die Zergliederungs-ID und Deklarationen, Abhängigkeiten, Integrationsanforderungen, Validierungsparameter und Zuordnungsdefinitionen zur Übersetzung von Werten in Namen und Zahlen in Text definieren. Weitere Informationen finden Sie unter den folgenden Links:

- [Vorbereiten der Konfigurationsdatei (JSON)](#prepare-the-configuration-file-json)

- [Vorbereiten von Validierungen des Implementierungscodes](#prepare-implementation-code-validations)

- [Extrahieren von Gerätemetadaten](#extract-device-metadata)

- [Herstellen einer Verbindung mit einem Indexdienst (Baseline)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Implementierungscode: C++ (erforderlich)

Der Implementierungscode (CPP) analysiert den Rohdatenverkehr und ordnet ihn Werten wie Diensten, Klassen und Funktionscodes zu. Er extrahiert die Ebenenfelder und ordnet sie ihren Indexnamen aus den JSON-Konfigurationsdateien zu. Die aus CPP zu extrahierenden Felder sind in der Konfigurationsdatei definiert. Weitere Informationen finden Sie unter [Vorbereiten des Implementierungscodes (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Zuordnungsdateien (optional)

Sie können den Plug-In-Ausgabetext an die Anforderungen Ihrer Unternehmensumgebung anpassen.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migration":::

Sie können Zuordnungsdateien definieren und aktualisieren, um Text zu aktualisieren, ohne den Code zu ändern. Jede Datei kann ein oder mehrere Felder zuordnen:

  - Zuordnung von Feldwerten zu Namen, z. B. 1:Reset, 2:Start, 3:Stop.

  - Zuordnung von Text zur Unterstützung mehrerer Sprachen.

Weitere Informationen finden Sie unter [Erstellen von Zuordnungsdateien (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Erstellen eines Zergliederungs-Plug-Ins (Übersicht)

1. Lesen Sie den Abschnitt mit [Informationen zur Umgebung und zum Setup](#about-the-environment-and-setup).

2.  [Bereiten Sie den Implementierungscode vor (C++)](#prepare-the-implementation-code-c). Kopieren Sie die Datei **template.json**, und bearbeiten Sie sie gemäß Ihren Anforderungen. Ändern Sie die Schlüssel nicht. 

3. [Bereiten Sie die Konfigurationsdatei (JSON) vor](#prepare-the-configuration-file-json). Kopieren Sie die Datei **template.cpp**, und implementieren Sie eine Methode zur Außerkraftsetzung. Weitere Informationen finden Sie unter [horizon::protocol::BaseParser](#horizonprotocolbaseparser).

4. [Bereiten Sie Validierungen des Implementierungscodes vor](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Bereiten Sie den Implementierungscode vor (C++).

Die CPP-Datei ist ein Parser, der für Folgendes zuständig ist:

- Validierung des Paketheaders und der Nutzdaten (z. B. Headerlänge oder Nutzdatenstruktur)

- Extrahieren von Daten aus Header und Nutzdaten in definierte Felder

- Implementieren der Extraktion von konfigurierten Feldern durch die JSON-Datei

### <a name="what-to-do"></a>Aktion

Kopieren Sie die Datei **template.cpp**, und implementieren Sie eine Methode zur Außerkraftsetzung. Weitere Informationen finden Sie unter [horizon::protocol::BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Grundlegendes C++-Vorlagenbeispiel 

Dieser Abschnitt enthält die grundlegende Protokollvorlage mit Standardfunktionen für ein Beispiel des Defender für IoT Horizon-Protokolls.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Beschreibung der grundlegenden C++-Vorlage  

Dieser Abschnitt enthält die grundlegende Protokollvorlage mit einer Beschreibung der Standardfunktionen für ein Beispiel des Defender für IoT Horizon-Protokolls. 

### <a name="include-pluginpluginh"></a>#include „plugin/plugin.h“

Die vom Plug-In verwendete Definition. Die Headerdatei enthält alles, was zur Durchführung der Entwicklung erforderlich ist.

### <a name="horizonprotocolbaseparser"></a>horizon::protocol::BaseParser

Die Kommunikationsschnittstelle zwischen der Horizon-Infrastruktur und der Plug-In-Ebene. Weitere Informationen finden Sie im Abschnitt zur [Horizon-Architektur](#horizon-architecture), die eine Übersicht über die Ebenen bietet.

„processLayer“ ist die Methode, die zur Verarbeitung von Daten verwendet wird.

- Der erste Parameter im Funktionscode ist das Verarbeitungshilfsprogramm, das zum Abrufen bereits verarbeiteter Daten sowie zum Erstellen neuer Felder und Ebenen verwendet wird.

- Der zweite Parameter im Funktionscode sind die aktuellen Daten, die vom vorherigen Parser übergeben wurden.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Verwenden Sie diese Option, um die Instanz Ihres Parsers zu erstellen.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Codebeispiel für die Protokollfunktion 

Dieser Abschnitt enthält ein Beispiel dafür, wie die Codenummer (2 Byte) und die Nachrichtenlänge (4 Byte) extrahiert werden.

Dies geschieht entsprechend der in der JSON-Konfigurationsdatei angegebenen Bytereihenfolge, d. h., wenn das Protokoll eine *Little-Endian-Bytereihenfolge* aufweist und der Sensor auf einem Computer mit Little-Endian-Bytereihenfolge ausgeführt wird, erfolgt eine Konvertierung.

Eine Ebene wird auch zum Speichern von Daten erstellt. Verwenden Sie *fieldsManager* aus den Verarbeitungshilfsprogrammen, um neue Felder zu erstellen. Ein Feld kann nur einen der folgenden Typen aufweisen: *STRING* (Zeichenfolge), *NUMBER* (Zahl), *RAW DATA* (Rohdaten), *ARRAY* (eines bestimmten Typs) oder *COMPLEX* (Komplex). Diese Ebene kann eine Zahl, Rohdaten oder eine Zeichenfolge mit ID enthalten.

Im folgenden Beispiel werden die folgenden beiden Felder extrahiert:

- `function_code_number`

- `headerLength`

Eine neue Ebene wird erstellt, und das extrahierte Feld wird in diese Ebene kopiert.

Das folgende Beispiel beschreibt eine bestimmte Funktion, die die Hauptlogik für die Plug-In-Verarbeitung darstellt.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Zugehöriges JSON-Feld 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="Das zugehörige JSON-Feld.":::

## <a name="prepare-the-configuration-file-json"></a>Vorbereiten der Konfigurationsdatei (JSON)

Das Horizon SDK verwendet die standardmäßige JavaScript Object Notation (JSON), ein Lightweightformat für die Speicherung und den Transport von Daten und erfordert keine proprietären Skriptsprachen.

Dieser Abschnitt beschreibt minimale JSON-Konfigurationsdeklarationen, die zugehörige Struktur und bietet eine Beispielkonfigurationsdatei, die ein Protokoll definiert. Dieses Protokoll ist automatisch in den Geräteerkennungsdienst integriert.

## <a name="file-structure"></a>Dateistruktur

Im folgenden Beispiel wird die Dateistruktur beschrieben.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="Beispiel zur Dateistruktur":::

### <a name="what-to-do"></a>Aktion

Kopieren Sie die Vorlagedatei `config.json`, und bearbeiten Sie sie gemäß Ihren Anforderungen. Ändern Sie den Schlüssel nicht. In der [JSON-Konfigurationsdatei des Beispiels](#sample-json-configuration-file) sind die Schlüssel rot markiert. 

### <a name="file-naming-requirements"></a>Anforderungen zur Dateibenennung 

Die JSON-Konfigurationsdatei muss als `config.json` gespeichert werden.

### <a name="json-configuration-file-fields"></a>Felder der JSON-Konfigurationsdatei

In diesem Abschnitt werden die JSON-Konfigurationsfelder beschrieben, die Sie definieren werden. Ändern Sie die *Bezeichnungen* der Felder nicht.

### <a name="basic-parameters"></a>Grundlegende Parameter

In diesem Abschnitt werden grundlegende Parameter beschrieben.

| Parameterbezeichnung | BESCHREIBUNG | Typ |
|--|--|--|
| **ID** | Der Name des Protokolls. Löschen Sie den Standardwert und fügen Sie den Namen Ihres Protokolls so ein, wie er angezeigt wird. | String |
| **endianess** | Definiert die Codierung der Multibytedaten. Verwenden Sie nur den Begriff „little“ oder „big“. Übernommen aus der Protokollspezifikation oder der Aufzeichnung des Datenverkehrs | String |
| **sanity_failure_codes** | Dies sind die Codes, die vom Parser zurückgegeben werden, wenn ein Integritätskonflikt bezüglich der Identität des Codes besteht. Weitere Informationen finden Sie im Abschnitt zur Validierung der magischen Zahl in C++. | String |
| **malformed_codes** | Hierbei handelt es sich um ordnungsgemäß identifizierte Codes, bei denen jedoch ein Fehler erkannt wurde. Beispiel: Die Feldlänge ist zu kurz oder zu lang oder ein Wert ist ungültig. | String |
| **dissect_as** | Ein Array, das festlegt, wo der bestimmte Protokolldatenverkehr eintreffen soll. | TCP/UDP, Port usw. |
| **fields** | Die Deklaration, welche Felder aus dem Datenverkehr extrahiert werden sollen. Jedes Feld besitzt eine eigene ID (Name) und einen eigenen Typ (numeric, string, raw, array, complex). Beispiel: Die [Feldfunktion](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k), die in der Parser-Datei der Implementierung extrahiert wird. Die in der Konfigurationsdatei geschriebenen Felder sind die einzigen, die der Ebene hinzugefügt werden können. |  |

### <a name="other-advanced-fields"></a>Andere erweiterte Felder 

In diesem Abschnitt werden andere Felder beschrieben.

| Parameterbezeichnung | Beschreibung |
|-----------------|--------|
| **Positivlisten** | Sie können die Protokollwerte indizieren und in Data Mining-Berichten anzeigen. Diese Berichte spiegeln die Baseline Ihres Netzwerks wider. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Beispiel für die Data Mining-Ansicht"::: <br /> Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einem Indexdienst (Baseline)](#connect-to-an-indexing-service-baseline). |
| **firmware** | Sie können Firmwareinformationen extrahieren, Indexwerte definieren und Firmwarewarnungen für das Plug-In-Protokoll auslösen. Weitere Informationen finden Sie unter [Extrahieren von Firmwaredaten](#extract-firmware-data). |
| **value_mapping** | Sie können den Plug-In-Ausgabetext an die Anforderungen Ihrer Unternehmensumgebung anpassen, indem Sie Zuordnungsdateien definieren und aktualisieren. Beispiel: Zuordnung zu Sprachdateien. Änderungen können einfach am Text vorgenommen werden, ohne den Code zu ändern oder zu beeinflussen. Weitere Informationen finden Sie unter [Erstellen von Zuordnungsdateien (JSON)](#create-mapping-files-json). |

## <a name="sample-json-configuration-file"></a>JSON-Beispielkonfigurationsdatei

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Vorbereiten der Validierungen des Implementierungscodes

In diesem Abschnitt wird die Implementierung der Gültigkeitsprüfungsfunktionen für C++-Code beschrieben und ein Beispielcode bereitgestellt. Es stehen zwei Ebenen der Validierung zur Verfügung:

- Integrität

- Falsch formatierter Code

Sie müssen keinen Validierungscode erstellen, um ein funktionierendes Plug-In zu erstellen. Wenn Sie keinen Validierungscode vorbereiten, können Sie Data Mining-Berichte für Sensoren als Hinweis auf eine erfolgreiche Verarbeitung prüfen.

Feldwerte können in Zuordnungsdateien zum Text zugeordnet und problemlos aktualisiert werden, ohne die Verarbeitung zu beeinflussen.

## <a name="sanity-code-validations"></a>Validierungen von Integritätscode 

Damit wird überprüft, ob das übertragene Paket mit den Validierungsparametern des Protokolls übereinstimmt, was Ihnen hilft, das Protokoll im Datenverkehr zu identifizieren.

Verwenden Sie z. B. die ersten acht Bytes als *magische Zahl*. Wenn bei der Integrität ein Fehler auftritt, wird eine Integritätsfehlerantwort zurückgegeben.

Beispiel:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Wenn andere relevante Plug-Ins bereitgestellt wurden, wird das Paket anhand dieser überprüft.

## <a name="malformed-code-validations"></a>Validierungen von falsch formatiertem Code 

Falsch formatierte Prüfungen werden verwendet, nachdem das Protokoll positiv geprüft wurde.

Wenn bei der Verarbeitung der Pakete, basierend auf dem Protokoll, ein Fehler auftritt, wird eine Fehlerantwort zurückgegeben.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="Falsch formatierter Code":::

## <a name="c-sample-with-validations"></a>C++-Beispiel mit Validierungen

Gemäß der Funktion wird der Prozess ausgeführt, wie im folgenden Beispiel gezeigt.

### <a name="function-20"></a>Function 20 

- Sie wird als Firmware verarbeitet.

- Die Felder werden entsprechend der Funktion gelesen.

- Die Felder werden der Ebene hinzugefügt.

### <a name="function-10"></a>Function 10 

- Die Funktion enthält eine weitere Unterfunktion, die eine spezifischere Operation darstellt.

- Die Unterfunktion wird gelesen und der Ebene hinzugefügt.

Sobald dies geschehen ist, ist die Verarbeitung beendet. Der Rückgabewert zeigt an, ob die Zergliederungsebene erfolgreich verarbeitet wurde. Wenn dies der Fall war, kann die Ebene verwendet werden.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Extrahieren von Gerätemetadaten

Sie können die folgenden Metadaten zu Ressourcen extrahieren:

  - `Is_distributed_control_system` – Zeigt an, ob das Protokoll Teil des Prozessleitsystems (Distributed Control System, DCS) ist. (Beispiel: Das SCADA-Protokoll sollte „false“ ergeben)

  - `Has_protocol_address` – Gibt an, ob eine Protokolladresse vorhanden ist. Die bestimmte Adresse für das aktuelle Protokoll, z. B. ein Bezeichner einer MODBUS-Einheit.

  - `Is_scada_protocol` – Gibt an, ob das Protokoll für OT-Netzwerke spezifisch ist.

  - `Is_router_potential` – Gibt an, ob das Protokoll hauptsächlich von Routern verwendet wird. Beispiel: LLDP, CDP oder STP.

Um dies zu erreichen, muss die JSON-Konfigurationsdatei unter Verwendung der metadata-Eigenschaft aktualisiert werden.

## <a name="json-sample-with-metadata"></a>JSON-Beispiel mit Metadaten 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Extrahieren von Programmcode 

Wenn das Programmierereignis auftritt, können Sie den Codeinhalt extrahieren. Mit dem extrahierten Inhalt haben Sie folgende Möglichkeiten:

- Vergleichen Sie den Codedateiinhalt in verschiedenen Programmierereignissen.

- Lösen Sie eine Warnung zur nicht autorisierten Programmierung aus.  

- Lösen Sie ein Ereignis für das Empfangen der Programmcodedatei aus.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="Änderungsprotokoll der Programmierung":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Zeigen Sie die Programmierung an, indem Sie auf die Schaltfläche klicken.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="Warnung „Unauthorized PLC Programming“ (SPS-Programmierung nicht autorisiert)":::

Um dies zu erreichen, muss die JSON-Konfigurationsdatei unter Verwendung der `code_extraction`-Eigenschaft aktualisiert werden. 

### <a name="json-configuration-fields"></a>Felder der JSON-Konfiguration 

In diesem Abschnitt werden die Felder der JSON-Konfiguration beschrieben. 

- **method**

  Gibt an, wie Programmierereignisdateien empfangen werden. 

  ALL (jede Programmieraktion bewirkt, dass alle Codedateien empfangen werden, auch wenn es Dateien ohne Änderungen gibt).

- **file_type**  

  Gibt den Typ des Codeinhalts an.  

  TEXT (jede Codedatei enthält Textinformationen).

- **code_data_field**
  
  Gibt das Implementierungsfeld an, das verwendet werden soll, um den Codeinhalt bereitzustellen.

  FIELD.

- **code_name_field**

  Gibt das Implementierungsfeld an, das verwendet werden soll, um den Namen der Codierungsdatei bereitzustellen.

  FIELD.

- **size_limit**

  Gibt das Größenlimit der einzelnen Codedateiinhalte in BYTES an. Wenn eine Codedatei den festgelegten Grenzwert überschreitet, wird sie verworfen. Wenn dieses Feld nicht angegeben wird, ist der Standardwert 15.000.000, d. h. 15 MB.

  Zahl

- **metadata**

  Zeigt zusätzliche Informationen für eine Codedatei an.

  Array, das Objekte mit zwei Eigenschaften enthält:

    - Name (Zeichenfolge) – Gibt den Metadatenschlüssel an. XSense unterstützt derzeit nur den Benutzernamenschlüssel.
 
    - Value (Feld) – Gibt das Implementierungsfeld an, das verwendet werden soll, um die Metadatendaten bereitzustellen.

## <a name="json-sample-with-programming-code"></a>JSON-Beispiel mit Programmcode

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Benutzerdefinierte Horizon-Warnungen

Der Funktionscode einiger Protokolle kann einen Fehler anzeigen. Beispiel: Wenn das Protokoll einen Container mit einer bestimmten Chemikalie steuert, die immer bei einer bestimmten Temperatur gelagert werden muss. In diesem Fall liegt möglicherweise ein Funktionscode vor, der einen Fehler im Thermometer anzeigt. Wenn der Funktionscode z. B. 25 lautet, können Sie eine Warnung in der Webkonsole auslösen, die darauf hinweist, dass ein Problem mit dem Container vorliegt. In diesem Fall können Sie umfassende Paketwarnungen definieren.

Fügen Sie den Parameter **alerts** zur `config.json` des Plug-Ins hinzu.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>Felder der JSON-Konfiguration

In diesem Abschnitt werden die Felder der JSON-Konfiguration beschrieben. 

| Feldname | BESCHREIBUNG | Mögliche Werte |
|--|--|--|
| **ID** | Stellt eine einzelne Warnungs-ID dar. Sie muss in diesem Kontext eindeutig sein. | Numerischer Wert 0 bis 10000 |
| **Nachricht** | Informationen, die dem Benutzer angezeigt werden. Mit diesem Feld können Sie verschiedene Felder verwenden. | Verwenden Sie ein beliebiges Feld aus Ihrem Protokoll oder einem Protokoll einer untergeordneten Ebene. |
| **title** | Der Titel der Warnung. |  |
| **expression** | Wann Sie möchten, dass diese Warnung angezeigt wird. | Verwenden Sie ein beliebiges numerisches Feld, das sich auf niedrigeren Ebenen oder der aktuellen Ebene befindet.</br></br> Jedes Feld sollte in `{}` eingeschlossen sein, damit das SDK es als Feld erkennt. Die unterstützten logischen Operatoren sind:</br> == – Gleich</br> <= – Kleiner als oder gleich</br> >= – Größer als oder gleich</br> > – Größer als</br> < – Kleiner als</br> ~= – Ungleich |

## <a name="more-about-expressions"></a>Weitere Informationen zu Ausdrücken

Jedes Mal, wenn das Horizon SDK den Ausdruck aufruft und dieser *true* ergibt, wird eine Warnung im Sensor ausgelöst.

In derselben Warnung können mehrere Ausdrücke enthalten sein. Beispiel:

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Dieser Ausdruck überprüft den Funktionscode nur, wenn „packet ipv4 src“ 10.10.10.10 ist. Das ist eine unformatierte Darstellung der IP-Adresse, die numerisch dargestellt wird.

Sie können `and` oder `or` verwenden, um Ausdrücke zu verbinden.

## <a name="json-sample-custom-horizon-alerts"></a>JSON-Beispiel für benutzerdefinierte Horizon-Warnungen

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Herstellen einer Verbindung mit einem Indexdienst (Baseline)

Sie können die Protokollwerte indizieren und in Data Mining-Berichten anzeigen.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Ansicht der Data Mining-Option":::

Diese Werte können später zu bestimmten Texten zugeordnet werden, z. B. zur Zuordnung von Zahlen als Texte oder zum Hinzufügen von Informationen in einer beliebigen Sprache.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migration":::

Weitere Informationen finden Sie unter [Erstellen von Zuordnungsdateien (JSON)](#create-mapping-files-json).

Sie können auch Werte aus zuvor analysierten Protokollen verwenden, um zusätzliche Informationen zu extrahieren.

Für den auf TCP basierenden Wert können Sie z. B. die Werte von der IPv4-Ebene verwenden. Von dieser Ebene können Sie Werte wie die Quelle des Pakets und das Ziel extrahieren.

Um dies zu erreichen, muss die JSON-Konfigurationsdatei unter Verwendung der `whitelists`-Eigenschaft aktualisiert werden.

## <a name="allow-list-data-mining-fields"></a>Felder von Positivlisten (Data Mining)

Die folgenden Felder von Positivlisten sind verfügbar:

- name – Der Name, der für die Indizierung verwendet wird.

- alert_title – Ein kurzer, eindeutiger Titel, der das Ereignis erläutert.

- alert_text – Zusätzliche Informationen.

Es können mehrere Positivlisten hinzugefügt werden, wodurch eine vollständige Flexibilität bei der Indizierung ermöglicht wird.

## <a name="json-sample-with-indexing"></a>JSON-Beispiel mit Indizierung 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Extrahieren von Firmwaredaten

Sie können Firmwareinformationen extrahieren, Indexwerte definieren und Firmwarewarnungen für das Plug-In-Protokoll auslösen. Beispiel:

- Extrahieren Sie das Firmwaremodell oder die -version. Diese Informationen können zur Identifizierung von CVEs verwendet werden.

- Lösen Sie eine Warnung aus, wenn eine neue Firmwareversion erkannt wird.

Um dies zu erreichen, muss die JSON-Konfigurationsdatei unter Verwendung der firmware-Eigenschaft aktualisiert werden.

## <a name="firmware-fields"></a>Firmwarefelder

In diesem Abschnitt werden die Felder der JSON-Firmwarekonfiguration beschrieben.

- **name**
  
  Gibt an, wie das Feld in der Sensorkonsole dargestellt wird.

- **value**

 Gibt das Implementierungsfeld an, das verwendet werden soll, um die Daten bereitzustellen. 

- **firmware_index:**

  Auswahlmöglichkeiten:  
  - **Modell**: Das Gerätemodell Aktiviert die Erkennung von CVEs.
  - **serial**: Die Seriennummer des Geräts. Die Seriennummer ist nicht immer für alle Protokolle verfügbar. Dieser Wert ist für das jeweilige Gerät eindeutig.
  - **rack**: Gibt den Rackbezeichner an, wenn das Gerät zu einem Rack gehört.
  - **slot**: Der Bezeichner des Einschubfachs, wenn das Gerät zu einem Rack gehört.  
  - **module_address**: Wird zur Darstellung einer Hierarchie verwendet, wenn das Modul hinter einem anderen Gerät dargestellt werden kann. Kann anstelle einer Kombination aus Rack und Einschubfach angewendet werden, da es sich um eine einfachere Darstellung handelt.  
  - **firmware_version**: Kennzeichnet die Geräteversion. Aktiviert die Erkennung von CVEs.
  - **alert_text**: Zeigt Text an, der Firmwareabweichungen beschreibt, z. B. Versionsänderungen.  
  - **index_by**: Gibt die Felder an, die zur Identifizierung und Indizierung des Geräts verwendet werden. Im folgenden Beispiel wird das Gerät über seine IP-Adresse identifiziert. In bestimmten Protokollen kann ein komplexerer Index verwendet werden. Wenn z. B. ein anderes Gerät angeschlossen ist und Sie wissen, wie Sie dessen internen Pfad extrahieren können. So kann z. B. die ID der MODBUS-Einheit als Teil des Indexes verwendet werden, als andere Kombination aus IP-Adresse und Bezeichner der Einheit.
  - **firmware_fields**: Gibt an, welche Felder entsprechende Felder für Gerätemetadaten sind. In diesem Beispiel wird Folgendes verwendet: Modell, Revision und Name. Jedes Protokoll kann seine eigenen Firmwaredaten definieren.

## <a name="json-sample-with-firmware"></a>JSON-Beispiel mit Firmware 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Extrahieren von Geräteattributen 

Sie können die im Gerät verfügbaren Informationen in Bestands-, Data Mining- und anderen Berichten erweitern.

- Name

- Typ

- Hersteller

- Betriebssystem

Dazu muss die JSON-Konfigurationsdatei mithilfe der **Properties**-Eigenschaft aktualisiert werden. 

Sie können dies erledigen, nachdem Sie das grundlegende Plug-In erstellt und die erforderlichen Felder extrahiert haben.

## <a name="properties-fields"></a>Eigenschaftenfelder

In diesem Abschnitt werden die Felder der JSON-Eigenschaftenkonfiguration beschrieben. 

**config_file** 

Enthält den Dateinamen, der definiert, wie die einzelnen Schlüssel in den `key`-Feldern zu verarbeiten sind. Die Konfigurationsdatei selbst sollte im JSON-Format vorliegen und als Teil des Plug-In-Protokollordners einbezogen werden.

Jeder Schlüssel im JSON definiert den Satz von Aktionen, die ausgeführt werden sollen, wenn Sie diesen Schlüssel aus einem Paket extrahieren.

Jeder Schlüssel kann über Folgendes verfügen:

- **Paketdaten** – Gibt die Eigenschaften an, die auf der Grundlage der aus dem Paket extrahierten Daten aktualisiert werden würden (das Implementierungsfeld, das zur Bereitstellung dieser Daten verwendet wird).

- **Statische Daten** – Gibt einen vordefinierten Satz von `property-value`-Aktionen an, die aktualisiert werden sollen.

Die Eigenschaften, die in dieser Datei konfiguriert werden können, sind: 

- **Name** – Gibt den Gerätenamen an.

- **Typ** – Gibt den Gerätetyp an.

- **Hersteller** – Gibt den Gerätehersteller an.

- **Beschreibung** – Gibt das Firmwaremodell des Geräts an (niedrigere Priorität als „Modell“).

- **operatingSystem** – Gibt das Betriebssystem des Geräts an.

### <a name="fields"></a>Felder

| Feld | BESCHREIBUNG |
|--|--|
| Schlüssel | Gibt den Schlüssel an. |
| value | Gibt das Implementierungsfeld an, das verwendet werden soll, um die Daten bereitzustellen. |
| is_static_key | Gibt an, ob das `key`-Feld als Wert aus dem Paket abgeleitet wird oder ob es ein vordefinierter Wert ist. |

### <a name="working-with-static-keys-only"></a>Nur mit statischen Schlüsseln arbeiten

Wenn Sie mit statischen Schlüsseln arbeiten, dann müssen Sie die `config.file` konfigurieren. Sie können nur die JSON-Datei konfigurieren.

## <a name="json-sample-with-properties"></a>JSON-Beispiel mit Eigenschaften 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Erstellen von Zuordnungsdateien (JSON)

Sie können den Plug-In-Ausgabetext an die Anforderungen Ihrer Unternehmensumgebung anpassen, indem Sie Zuordnungsdateien definieren und aktualisieren. Änderungen können einfach am Text vorgenommen werden, ohne den Code zu ändern oder zu beeinflussen. Jede Datei kann ein oder mehrere Felder zuordnen.

- Zuordnung von Feldwerten zu Namen, z. B. 1:Reset, 2:Start, 3:Stop.

- Zuordnung von Text zur Unterstützung mehrerer Sprachen.

Es können zwei Arten von Zuordnungsdateien definiert werden.

  - [Einfache Zuordnungsdatei](#simple-mapping-file)

  - [Datei zur Zuordnung der Abhängigkeiten](#dependency-mapping-file)

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Ethernet":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="Ansicht der nicht behandelten Warnungen":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Liste bekannter Richtlinienverstöße":::

## <a name="file-naming-and-storage-requirements"></a>Dateibenennung und Speicheranforderungen 

Zuordnungsdateien sollten unter dem Ordner „Metadaten“ gespeichert werden.

Der Name der Datei sollte mit der ID der JSON-Konfigurationsdatei übereinstimmen.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Beispiel für eine JSON-Konfigurationsdatei":::

## <a name="simple-mapping-file"></a>Einfache Zuordnungsdatei 

Das folgende Beispiel stellt eine einfache JSON-Datei als Schlüsselwert dar.

Wenn Sie eine Positivliste erstellen und diese eines oder mehrere der zugeordneten Felder enthält. Der Wert wird von einer Zahl, einer Zeichenfolge oder einem beliebigen Typ in einen formatierten Text umgewandelt, der in der Zuordnungsdatei dargestellt wird.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Datei zur Zuordnung der Abhängigkeiten 

Um anzugeben, dass es sich um eine Abhängigkeitsdatei handelt, fügen Sie das Schlüsselwort `dependency` in die Zuordnungskonfiguration ein.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

Die Datei enthält eine Zuordnung zwischen dem Abhängigkeitsfeld und dem Funktionsfeld. Beispielsweise zwischen der Funktion und der Unterfunktion. Die Unterfunktion ändert sich entsprechend der bereitgestellten Funktion.

In der zuvor konfigurierten Positivliste ist keine Abhängigkeitskonfiguration vorhanden, wie unten dargestellt.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

Die Abhängigkeit kann sich auf einen bestimmten Wert oder ein Feld beziehen. Im folgenden Beispiel basiert sie auf einem Feld. Wenn Sie sie auf einen Wert basieren, definieren Sie den extrahierten Wert, der aus der Zuordnungsdatei gelesen werden soll.

Im folgenden Beispiel lautet die Abhängigkeit für denselben Wert des Feldes wie folgt.

Bei der Unterfunktion „five“ wird z. B. die Bedeutung funktionsabhängig geändert.

  - Wenn es sich um eine Lesefunktion handelt, bedeutet „five“ entsprechend „Arbeitsspeicher lesen“.

  - Wenn es sich um eine Schreibfunktion handelt, wird derselbe Wert zum Lesen aus einer Datei verwendet.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Beispieldatei

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>JSON-Beispiel mit Zuordnung

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Verpacken, Hochladen und Überwachen des Plug-Ins 

In diesem Abschnitt wird Folgendes beschrieben:

  - Verpacken des Plug-Ins

  - Hochladen des Plug-Ins

  - Überwachen und Debuggen des Plug-Ins zur Bewertung seiner Leistung

So verpacken Sie das Plug-In

1. Fügen Sie das **Artefakt** (Bibliothek, config.json oder Metadaten) zu einer `tar.gz`-Datei hinzu.

1. Ändern Sie die Dateierweiterung in \<XXX.hdp>, wobei \<XXX> der Name des Plug-Ins ist.

So melden Sie sich bei der Horizon-Konsole an

1.  Melden Sie sich bei der Befehlszeilenschnittstelle Ihres Sensors als Benutzer „Administrator“, „CyberX“ oder „Support“ an.

2.  In der Datei `/var/cyberx/properties/horizon.properties`: Ändern Sie die Eigenschaft **ui.enabled** in **true** (`horizon.properties:ui.enabled=true`).

3.  Melden Sie sich bei der Sensorkonsole an.

4.  Wählen Sie im Hauptmenü die Option **Horizon** aus.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Wählen Sie im Bereich auf der linken Seite die Option „Horizon“ aus.":::

    Die Horizon-Konsole wird geöffnet.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Ansicht der Horizon-Konsole und all ihrer Plug-Ins.":::

## <a name="plugins-pane"></a>Plug-Ins-Bereich

Der Bereich „Plug-Ins“ listet Folgendes auf:

  - Infrastruktur-Plug-Ins: Standardmäßig mit Defender für IoT installierte Infrastruktur-Plug-Ins.

  - Anwendungs-Plug-Ins: Anwendungs-Plug-Ins, die standardmäßig mit Defender für IoT installiert werden, und andere Plug-Ins, die von Defender für IoT- oder externen Entwicklern entwickelt wurden.
    
Aktivieren und deaktivieren Sie Plug-Ins, die mit dem Umschalter hochgeladen wurden.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="CIP-Umschalter":::

### <a name="uploading-a-plugin"></a>Hochladen eines Plug-Ins

Nachdem Sie Ihr Plug-In erstellt und verpackt haben, können Sie es auf den Defender für IoT-Sensor hochladen. Um eine vollständige Abdeckung Ihres Netzwerks zu erreichen, sollten Sie das Plug-In auf jeden Sensor in Ihrem Unternehmen hochladen.

So laden Sie ein Plug-In hoch:

1.  Melden Sie sich bei Ihrem Sensor an.


2. Wählen Sie die Option **Hochladen**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Laden Sie Ihre Plug-Ins hoch.":::

3. Suchen Sie Ihr Plug-In und ziehen Sie es in das Plug-In-Dialogfeld. Stellen Sie sicher, dass das Präfix `.hdp` lautet. Das Plug-In wird geladen.

## <a name="plugin-status-overview"></a>Statusübersicht für das Plug-In 

Das **Übersichtsfenster** der Horizon-Konsole enthält Informationen zu den von Ihnen hochgeladenen Plug-Ins, die Sie deaktivieren und aktivieren können.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Übersicht über die Horizon-Konsole":::

| Feld | BESCHREIBUNG |
|--|--|
| Application | Der Name des von Ihnen hochgeladenen Plug-ins. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="Ein- und Ausschalter"::: | Schalte Sie das Plug-In **ein** oder **aus**. Wenn Sie das Plug-in ausschalten, verarbeitet Defender für IoT nicht den im Plug-In definierten Protokolldatenverkehr. |
| Time | Die Uhrzeit, zu der die Daten zuletzt analysiert wurden. Die Aktualisierung erfolgt alle fünf Sekunden. |
| PPS | Die Anzahl von Paketen pro Sekunde. |
| Bandbreite | Die durchschnittliche Bandbreite, die innerhalb der letzten fünf Sekunden erkannt wurde. |
| Malforms | Falsch formatierte Prüfungen werden verwendet, nachdem das Protokoll positiv geprüft wurde. Wenn bei der Verarbeitung der Pakete, basierend auf dem Protokoll, ein Fehler auftritt, wird eine Fehlerantwort zurückgegeben.   <br><br>Diese Spalte zeigt die Anzahl der Fehler aufgrund falscher Formatierung in den letzten fünf Sekunden an. Weitere Informationen finden Sie unter [Validierungen von falsch formatiertem Code](#malformed-code-validations). |
| Warnungen | Pakete entsprechen der Struktur und Spezifikation, aber es gibt ein unerwartetes Verhalten, das auf der Warnungskonfiguration des Plug-Ins basiert. |
| Errors | Die Anzahl der Pakete, bei denen bei grundlegenden Protokollvalidierungen Fehler aufgetreten sind. Überprüft, ob das Paket mit den Protokolldefinitionen übereinstimmt. Die hier angezeigte Zahl gibt die Anzahl von Fehlern an, die in den letzten fünf Sekunden erkannt wurden. Weitere Informationen finden Sie unter [Validierungen von Integritätscode](#sanity-code-validations). |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="Symbol „Überwachen“"::: | Überprüfen Sie die Details zu den bei Ihrem Plug-In erkannten Malforms (Falschformatierungen) und Warnungen. |

## <a name="plugin-details"></a>Details zum Plug-In

Sie können das Echtzeit-Plug-In-Verhalten überwachen, indem Sie die Anzahl der bei Ihrem Plug-In erkannten *Malforms* und *Warnungen* analysieren. Eine Option steht zur Verfügung, um den Bildschirm einzufrieren und zur weiteren Untersuchung zu exportieren.

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Bildschirm „SNMP-Monitor“":::

Zum Überwachen:

Wählen Sie in der Übersicht die Schaltfläche „Überwachen“ für Ihr Plug-In aus.

## <a name="next-steps"></a>Nächste Schritte

Einrichten der [Horizon-API](references-horizon-api.md)
