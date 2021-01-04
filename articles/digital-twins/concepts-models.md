---
title: Benutzerdefinierte Modelle
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie benutzerdefinierte Modelle von Azure Digital Twins verwendet werden, um Entitäten in Ihrer Umgebung zu beschreiben.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0a38f9b8135fed08a95df68f108e44c34fec6325
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955326"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Grundlegendes zu Zwillingsmodellen in Azure Digital Twins

Ein Hauptmerkmal von Azure Digital Twins ist die Fähigkeit, Ihr eigenes Vokabular zu definieren und Ihren Zwillingsgraph in den selbstdefinierten Begriffen Ihres Unternehmens zu erstellen. Dies wird mittels benutzerdefinierter **Modelle** ermöglicht. Sie können sich Modelle in einer Beschreibung Ihrer Umgebung als die Nomen vorstellen. 

Ein Modell ähnelt einer **Klasse** einer objektorientierten Programmiersprache und definiert eine Datenform für ein bestimmtes Konzept in Ihrer realen Arbeitsumgebung. Modelle haben Namen (wie z. B. *Raum* oder *Temperatursensor*) und enthalten Elemente wie Eigenschaften, Telemetrie/Ereignisse und Befehle, die beschreiben, welche Möglichkeiten dieser Entitätstyp in Ihrer Umgebung hat. Später nutzen Sie diese Modelle, um [**Digital Twins**](concepts-twins-graph.md) zu erstellen, die bestimmte dieser Typbeschreibung entsprechende Entitäten darstellen.

Azure Digital Twins-Modelle werden in der auf JSON-LD basierenden Sprache **Digital Twin Definition Language (DTDL)** dargestellt.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digital Twin Definition Language (DTDL) für Modelle

Modelle für Azure Digital Twins werden mithilfe von DTDL (Digital Twins Definition Language) definiert. DTDL basiert auf JSON-LD und ist von der Programmiersprache unabhängig. DTDL ist nicht ausschließlich für Azure Digital Twins bestimmt, sondern wird auch zur Darstellung von Gerätedaten in anderen IoT-Diensten wie [IoT Plug & Play](../iot-pnp/overview-iot-plug-and-play.md) verwendet. 

Azure Digital Twins arbeitet mit **DTDL _Version 2_**. Weitere Informationen zu dieser Version von DTDL finden Sie in der Dokumentation zur Spezifikation auf GitHub: [*Digital Twins Definition Language (DTDL): Version 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Die Verwendung von DTDL _Version 1_ mit Azure Digital Twins ist nun veraltet.

> [!NOTE] 
> Nicht alle Dienste, die DTDL verwenden, implementieren genau die gleichen Features von DTDL. Beispielsweise verwendet IoT Plug & Play keine DTDL-Features, die für Graphen gedacht sind, während Azure Digital Twins derzeit keine DTDL-Befehle implementiert.
>
> Weitere Informationen zu den DTDL-Features, die für Azure Digital Twins spezifisch sind, finden Sie in diesem Artikel im Abschnitt [ Besonderheiten der DTDL-Implementierung für Azure Digital Twins](#azure-digital-twins-dtdl-implementation-specifics).

## <a name="elements-of-a-model"></a>Elemente eines Modells

Innerhalb einer Modelldefinition ist das Element auf oberster Codeebene eine **Schnittstelle**. Damit wird das gesamte Modell gekapselt, während der Rest des Modells innerhalb der Schnittstelle definiert wird. 

Eine DTDL-Modellschnittstelle kann keines, eines oder mehrere der folgenden Felder enthalten:
* **Eigenschaft**: Eigenschaften sind Datenfelder, die den Zustand einer Entität repräsentieren (wie die Eigenschaften in vielen objektorientierten Programmiersprachen). Eigenschaften haben Sicherungsspeicher und können jederzeit gelesen werden.
* **Telemetrie**: Telemetriefelder stellen Messungen oder Ereignisse dar und dienen häufig zur Beschreibung von Sensormesswerten von Geräten. Im Gegensatz zu Eigenschaften wird Telemetrie nicht in einem digitalen Zwilling gespeichert. Telemetrie ist eine Reihe von zeitgebundenen Datenereignissen, die bei ihrem Auftreten verarbeitet werden müssen. Weitere Informationen zu den Unterschieden zwischen Eigenschaft und Telemetrie finden Sie unten im Abschnitt [*Eigenschaften im Vergleich zu Telemetrie*](#properties-vs-telemetry).
* **Komponenten**: Komponenten ermöglichen Ihnen nach Wunsch das Erstellen Ihrer Modellschnittstelle als Zusammensetzung anderer Schnittstellen. Ein Beispiel einer Komponente ist die Schnittstelle *Frontkamera* (und die weitere Komponentenschnittstelle *Rückkamera*), die bei der Definition eines Modells für ein *Smartphone* verwendet werden. Sie müssen zunächst eine Schnittstelle für *Frontkamera* so definieren, als wäre sie ihr eigenes Modell. Danach können Sie beim Definieren von *Smartphone* darauf verweisen.

    Beschreiben Sie mit einer Komponente etwas, das ein integraler Bestandteil Ihrer Lösung ist, aber keine separate Identität benötigt und im Digital Twin-Graph nicht unabhängig erstellt, gelöscht oder neu angeordnet werden muss. Wenn Sie möchten, dass Entitäten im Zwillingsgraphen unabhängig voneinander existieren, stellen Sie sie als separate Digital Twins verschiedener Modelle dar, die durch *Beziehungen* verbunden sind (siehe den nächsten Punkt).
    
    >[!TIP] 
    >Komponenten können auch zur Organisation verwendet werden, um Sätze verwandter Eigenschaften innerhalb einer Modellschnittstelle zu gruppieren. In diesem Fall können Sie sich jede Komponente als einen Namespace oder „Ordner“ innerhalb der Schnittstelle vorstellen.
* **Beziehung**: Anhand von Beziehungen können Sie darstellen, wie ein Digital Twin mit anderen Digital Twins zusammenarbeiten kann. Beziehungen können verschiedene semantische Bedeutungen darstellen, wie z. B. *enthält* („Raum enthält Boden“), *kühlt* („Klimaanlage kühlt Raum“), *Rechnungsempfänger* („Kompressor wird Benutzer in Rechnung gestellt“) usw. Beziehungen ermöglichen der Lösung, einen Graphen miteinander verbundener Entitäten zu erstellen.

> [!NOTE]
> Die [Spezifikation für DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definiert auch **Befehle**, d. h. Methoden, die auf einem Digital Twin ausgeführt werden können (z. B. ein Rücksetzbefehl oder ein Befehl zum Ein- oder Ausschalten eines Lüfters). Allerdings werden *Befehle in Azure Digital Twins derzeit nicht unterstützt*.

### <a name="properties-vs-telemetry"></a>Eigenschaften im Vergleich zu Telemetrie

Hier finden Sie einige zusätzliche Anleitungen zum Unterscheiden zwischen den DTDL-Feldern **Eigenschaft** und **Telemetrie** in Azure Digital Twins.

Zwischen Eigenschaften und Telemetrie für Azure Digital Twins-Modelle gibt es folgenden Unterschied:
* Bei **Eigenschaften** muss es Sicherungsspeicher geben. Dies bedeutet, dass Sie eine Eigenschaft jederzeit lesen und deren Wert abrufen können. Wenn die Eigenschaft schreibbar ist, können Sie darin auch einen Wert speichern.  
* **Telemetrie** ähnelt eher einem Datenstrom von Ereignissen. Es handelt sich dabei um eine Reihe von Datennachrichten mit nur kurzer Lebensdauer. Wenn Sie kein Lauschen für das Ereignis und keine Maßnahmen einrichten, die in diesem Fall zu ergreifen sind, gibt es zu einem späteren Zeitpunkt keine Ablaufverfolgung für das Ereignis. Sie können dann nicht mehr zur Ablaufverfolgung zurückkehren und diese lesen. 
  - In C#-Begriffen ist Telemetrie wie ein C#-Ereignis. 
  - In IoT-Begriffen ist Telemetrie normalerweise eine einzelne Messung, die von einem Gerät gesendet wird.

**Telemetrie** wird oft bei IoT-Geräten verwendet, weil viele Geräte die von ihnen generierten Messwerte nicht speichern können (oder an Speichern nicht interessiert sind). Sie senden die Messwerte einfach als Datenstrom von „Telemetrie“ereignissen. In diesem Fall können Sie auf dem Gerät nicht jederzeit nach dem neuesten Wert des Telemetriefelds fragen. Stattdessen müssen Sie auf die Nachrichten vom Gerät lauschen und bei deren Eintreffen Maßnahmen ergreifen. 

Wenn Sie ein Modell in Azure Digital Twins entwerfen, verwenden Sie deshalb in den meisten Fällen wahrscheinlich **Eigenschaften** zum Modellieren Ihrer Zwillinge. Dies erlaubt Ihnen die Nutzung des Sicherungsspeichers und die Möglichkeit zum Lesen und Abfragen der Datenfelder.

Telemetrie und Eigenschaften arbeiten oft zusammen, um den Dateneingang von Geräten zu verarbeiten. Da der gesamte Eingang bei Azure Digital Twins über [APIs](how-to-use-apis-sdks.md) erfolgt, verwenden Sie normalerweise deren Eingangsfunktion zum Lesen der Telemetrie oder der Eigenschaftsereignisse von Geräten und legen als Reaktion darauf eine Eigenschaft in ADT fest. 

Sie können ein Telemetrieereignis auch über die Azure Digital Twins-API veröffentlichen. Wie bei anderer Telemetrie handelt es sich dabei um ein kurzlebiges Ereignis, das zur Verarbeitung einen Listener benötigt.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Besonderheiten der DTDL-Implementierung für Azure Digital Twins

Damit ein DTDL-Modell mit Azure Digital Twins kompatibel ist, muss es diese Anforderungen erfüllen.

* Alle DTDL-Elemente auf oberster Ebene in einem Modell müssen den Typ *Schnittstelle* haben. Dies liegt daran, dass die APIs des Azure Digital Twins-Modells JSON-Objekte empfangen können, die entweder eine Schnittstelle oder ein Array von Schnittstellen darstellen. Infolgedessen sind auf der obersten Ebene keine anderen DTDL-Elementtypen zulässig.
* DTDL für Azure Digital Twins darf keine *Befehle* definieren.
* Azure Digital Twins ermöglicht für Komponenten nur eine einzelne Schachtelungsebene. Das bedeutet, dass eine Schnittstelle, die als Komponente verwendet wird, selbst keine Komponenten haben darf. 
* Schnittstellen können nicht inline innerhalb anderer DTDL-Schnittstellen definiert werden, sondern müssen als separate Entitäten auf oberster Ebene mit eigenen IDs definiert werden. Wenn dann eine andere Schnittstelle diese Schnittstelle als Komponente oder durch Vererbung einbinden möchte, kann sie auf ihre ID verweisen.

Azure Digital Twins beobachtet auch nicht das `writable`-Attribut für Eigenschaften oder Beziehungen. Obwohl dies gemäß DTDL-Spezifikationen festgelegt werden kann, wird der Wert von Azure Digital Twins nicht verwendet. Stattdessen werden diese von externen Clients, die über allgemeine Schreibberechtigungen für den Azure Digital Twins-Dienst verfügen, immer als beschreibbar behandelt.

## <a name="example-model-code"></a>Beispielmodellcode

Modelle von Digital Twin-Typen können ein einem beliebigen Text-Editor geschrieben werden. Die Sprache DTDL folgt der JSON-Syntax, weshalb Sie Modelle mit der Erweiterung *.json* speichern müssen. Durch Verwendung der JSON-Erweiterung wird vielen Text-Editoren zur Programmierung ermöglicht, eine grundlegende Syntaxprüfung und Hervorhebung für Ihre DTDL-Dokumente bereitzustellen. Es ist auch eine [DTDL-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) für [Visual Studio Code](https://code.visualstudio.com/) verfügbar.

Dieser Abschnitt enthält ein Beispiel für ein typisches Modell, das als DTDL-Schnittstelle geschrieben wurde. Das Modell beschreibt **Planeten** mit jeweils Name, Masse und Temperatur.
 
Beachten Sie, dass die-Planeten auch mit **Monden** interagieren können, die ihre Satelliten sind und **Krater** aufweisen können. Im folgenden Beispiel drückt das `Planet`-Modell Verbindungen mit diesen anderen Entitäten aus, indem auf zwei externe Modelle verwiesen wird: `Moon` und `Crater`. Diese Modelle werden auch im folgenden Beispielcode definiert, sind jedoch sehr einfach gehalten, sodass Sie nicht vom primären `Planet`-Beispiel ablenken.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

Die Felder des Modells sind wie folgt:

| Feld | Beschreibung |
| --- | --- |
| `@id` | Ein Bezeichner für das Modell. Muss das Format `dtmi:<domain>:<unique model identifier>;<model version number>` haben. |
| `@type` | Gibt die Art der beschriebenen Informationen an. Bei einer Schnittstelle ist der Typ *Schnittstelle*. |
| `@context` | Legt den [Kontext](https://niem.github.io/json/reference/json-ld/context/) für das JSON-Dokument fest. Für Modelle muss `dtmi:dtdl:context;2` verwendet werden. |
| `displayName` | [optional] Ermöglicht Ihnen, dem Modell auf Wunsch einen Anzeigenamen zu geben. |
| `contents` | Alle übrigen Schnittstellendaten werden hier als ein Array von Attributdefinitionen platziert. Jedes Attribut muss einen `@type` (eine *Eigenschaft*, *Telemetrie*, einen *Befehl*, eine *Beziehung* oder *Komponente*) enthalten, um die Art der Schnittstelleninformationen zu bestimmen, die beschrieben werden, und dann eine Reihe von Eigenschaften, die das eigentliche Attribut definieren (z. B. `name` und `schema` zur Definition einer *Eigenschaft*). |

> [!NOTE]
> Beachten Sie, dass die Komponentenschnittstelle (in diesem Beispiel *Crater*) im gleichen Array definiert ist wie die Schnittstelle, die sie verwendet (*Planet*). Komponenten müssen auf diese Weise in API-Aufrufen definiert werden, damit die Schnittstelle gefunden werden kann.

### <a name="possible-schemas"></a>Mögliche Schemas

Gemäß DTDL kann das Schema für die Attribute *Eigenschaft* und *Telemetrie* aus einfachen Standardtypen (`integer`, `double`, `string` und `Boolean`) und anderen Typen wie `DateTime` und `Duration` bestehen. 

Zusätzlich zu den einfachen Typen können die Felder *Eigenschaft* und *Telemetrie* diese komplexen Typen enthalten:
* `Object`
* `Map`
* `Enum`

*Telemetrie* felder unterstützen auch `Array`.

### <a name="model-inheritance"></a>Vererbung im Modell

Mitunter möchten Sie ein Modell eventuell weiter spezialisieren. Beispielsweise kann es sinnvoll sein, mit einem generischen Modell wie *Raum* und spezialisierte Varianten *Konferenzraum* und *Fitnessraum* zu arbeiten. Um Spezialisierung auszudrücken, unterstützt DTDL Vererbung. Schnittstellen können von einer oder mehreren anderen Schnittstellen erben. 

Im folgenden Beispiel wird das Modell *Planet* aus dem vorherigen DTDL-Beispiel als Untertyp des größeren Modells *CelestialBody* neu konzipiert. Das übergeordnete Modell wird zuerst definiert. Anschließend baut das untergeordnete Modell unter Verwendung des Felds `extends` darauf auf.

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

In diesem Beispiel trägt *CelestialBody* einen Namen, eine Masse und eine Temperatur zu *Planet* bei. Der Abschnitt `extends` ist ein Schnittstellenname oder Array von Schnittstellennamen (der es der erweiternden Schnittstelle ermöglicht, falls gewünscht, von mehreren übergeordneten Modellen zu erben).

Nach Anwendung der Vererbung macht die erweiternde Schnittstelle alle Eigenschaften in der gesamten Vererbungskette verfügbar.

Die erweiternde Schnittstelle kann keine Definitionen der übergeordneten Schnittstellen ändern, sondern sie nur ergänzen. Sie kann auch keine Fähigkeit neu definieren, die bereits in einer ihrer übergeordneten Schnittstellen definiert ist (selbst wenn die Fähigkeiten als gleich definiert sind). Wenn beispielsweise eine übergeordnete Schnittstelle die `double`-Eigenschaft *Masse* definiert, kann die erweiternde Schnittstelle keine Deklaration von *Masse* enthalten, selbst wenn es sich ebenfalls um eine `double`-Eigenschaft handelt.

## <a name="best-practices-for-designing-models"></a>Bewährte Methoden für das Entwerfen von Modellen

Wenn Sie beim Entwerfen von Modellen die Entitäten in Ihrer Umgebung wiedergeben möchten, kann es hilfreich sein, vorauszuschauen und die Implikationen zu betrachten, die Ihr Entwurf hinsichtlich [Abfragen](concepts-query-language.md) beinhaltet. Sie können Eigenschaften so entwerfen, dass Diagrammdurchläufe bei großen Resultsets vermieden werden. Sie können Beziehungen, die in einer einzelnen Abfrage beantwortet werden müssen, auch als einstufige Beziehungen modellieren.

### <a name="validating-models"></a>Validieren von Modellen

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="integrating-with-industry-standard-models"></a>Integrieren von Branchenstandardmodellen

Die Verwendung von Modellen, die auf Branchenstandards basieren oder eine standardmäßige Ontologiedarstellung nutzen (z. B. RDF oder OWL), bietet einen guten Ausgangspunkt für den Entwurf Ihrer Azure Digital Twins-Modelle. Die Verwendung von Branchenmodellen unterstützt außerdem die Standardisierung und Informationsfreigabe.

Für die Verwendung in Azure Digital Twins muss ein Modell in der auf JSON-LD basierenden Sprache [**DTDL (Digital Twins Definition Language)**](concepts-models.md) dargestellt werden. Deshalb wird in diesem Artikel beschrieben, wie Sie die Branchenstandardmodelle in DTDL darstellen und die vorhandenen Branchenkonzepte in die DTDL-Semantik integrieren, sodass diese von Azure Digital Twins verwendet werden können. Das DTDL-Modell dient dann als allgemeingültige Datenbasis für das Modell in Azure Digital Twins.

Es gibt zwei Hauptmethoden zur Integration von Branchenstandardmodellen in DTDL, die von Ihrer jeweiligen Situation abhängen:
* Wenn Sie die Modelle noch nicht erstellt haben, können Sie diese auf Grundlage **vorhandener DTDL-Anfangsontologien** entwerfen, die eine für Ihre Branche spezifische Sprache enthalten.
* Wenn Sie bereits über vorhandene Modelle verfügen, die auf einem Branchenstandard basieren, müssen Sie diese **in DTDL konvertieren**, um Sie in Azure Digital Twins zu übernehmen.

Weitere Informationen zu diesen beiden Prozessen finden Sie unter [*Vorgehensweise: Integrieren von Branchenstandardmodellen*](how-to-integrate-models.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Modelle mit den DigitalTwinModels-APIs verwaltet werden:
* [*Verwenden Verwalten von Azure Digital Twins-Modellen*](how-to-manage-model.md)

Oder erfahren Sie, wie Digital Twins auf Grundlage von Modellen erstellt werden:
* [*Konzepte: Digital Twins und der Digital Twins-Graph*](concepts-twins-graph.md)

