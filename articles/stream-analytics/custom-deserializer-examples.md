---
title: Lesen von Eingaben in beliebigen Formaten mithilfe von benutzerdefinierten .NET-Deserialisierern in Azure Stream Analytics
description: In diesem Artikel werden das Serialisierungsformat und die Schnittstellen erläutert, mit denen benutzerdefinierte .NET-Deserialisierer für Cloud- und Edgeaufträge in Azure Stream Analytics definiert werden.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4adf0b8a7c12abed9689b9ac0cc9c5d5c8c3f980
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488433"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Lesen von Eingabe in beliebigen Formaten mithilfe benutzerdefinierter .NET-Deserialisierer

Mithilfe benutzerdefinierter .NET-Deserialisierer können in Azure Stream Analytics-Aufträgen Daten in anderen Formaten als den drei [integrierten Datenformaten](stream-analytics-parsing-json.md) gelesen werden. In diesem Artikel werden das Serialisierungsformat und die Schnittstellen erläutert, mit denen benutzerdefinierte .NET-Deserialisierer für Cloud- und Edgeaufträge in Azure Stream Analytics definiert werden. Außerdem enthält dieser Artikel Beispieldeserialisierer für das Protokollpuffer- und CSV-Format.

## <a name="net-custom-deserializer"></a>Benutzerdefinierter .NET-Deserialisierer

Die folgenden Codebeispiele sind die Schnittstellen, über die der benutzerdefinierte Deserialisierer definiert und `StreamDeserializer<T>` implementiert wird.

`UserDefinedOperator` ist die Basisklasse für alle benutzerdefinierten Streamingoperatoren. Sie initialisiert `StreamingContext` für den Kontext mit dem Mechanismus zum Veröffentlichen der Diagnose, die Sie zum Debuggen von Problemen mit dem Deserialisierer benötigen.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Der nachfolgende Codeausschnitt betrifft die Deserialisierung für Streamingdaten. 

Überspringbare Fehler müssen mithilfe des Elements `IStreamingDiagnostics` ausgegeben werden, das über die Initialize-Methode von `UserDefinedOperator` übergeben wird. Alle Ausnahmen werden als Fehler behandelt, und der Deserialisierer wird neu erstellt. Nach einer bestimmten Anzahl von Fehlern wechselt der Auftrag in einen Fehlerstatus.

`StreamDeserializer<T>` deserialisiert einen Stream in ein Objekt vom Typ `T`. Die folgenden Bedingungen müssen erfüllt sein:

1. „T“ ist eine Klasse oder eine Struktur.
1. Für alle öffentlichen Felder in „T“ gilt Folgendes:
    1. Sie weisen einen der Typen [sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] bzw. Entsprechungen mit zugelassenen Nullwerten auf.
    1. Sie sind eine andere Struktur oder Klasse, für die dieselben Regeln gelten.
    1. Sie sind ein Array vom Typ `T2`, für das dieselben Regeln gelten.
    1. Sie entsprechen IList`T2`, wobei für „T2“ dieselben Regeln gelten.
    1. Sie weisen keine rekursiven Typen auf.

Der Parameter `stream` ist der Stream, der das serialisierte Objekt enthält. `Deserialize` gibt eine Sammlung von `T`-Instanzen zurück.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` enthält den Kontext mit dem Mechanismus zum Veröffentlichen der Diagnose für benutzerdefinierte Operatoren.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` ist die Diagnose für benutzerdefinierte Operatoren, einschließlich Serialisierungsmodul, Deserialisierer und benutzerdefinierter Funktionen.

`WriteError` schreibt eine Fehlermeldung in Ressourcenprotokolle und sendet den Fehler an die Diagnose.

`briefMessage` ist eine kurze Fehlermeldung. Diese Meldung wird in der Diagnose angezeigt und vom Produktteam für das Debuggen verwendet. Fügen Sie keine vertraulichen Informationen ein, und beschränken Sie die Meldung auf maximal 200 Zeichen.

`detailedMessage` ist eine ausführliche Fehlermeldung, die nur in den Ressourcenprotokollen im Speicher hinzugefügt wird. Diese Meldung darf maximal 2.000 Zeichen umfassen.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Beispiele für Deserialisierer

In diesem Abschnitt wird erläutert, wie Sie benutzerdefinierte Deserialisierer für Protobuf und CSV schreiben. Weitere Beispiele, z. B. AVRO-Format für Event Hub Capture, finden Sie in GitHub unter [Azure Stream Analytics](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Protokollpufferformat (Protobuf)

Es folgt ein Beispiel für die Verwendung des Protokollpufferformats.

Es wird von der folgenden Protokollpufferdefinition ausgegangen.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

Durch Ausführen von `protoc.exe` über **Google.Protobuf.Tools** wird in NuGet eine CS-Datei mit der Definition generiert. Die generierte Datei wird hier nicht gezeigt. Sie müssen sicherstellen, dass die im Stream Analytics-Projekt verwendete Version von Protobuf NuGet mit der Protobuf-Version übereinstimmt, die zum Generieren der Eingabe verwendet wurde. 

Der folgende Codeausschnitt gilt für die Implementierung des Deserialisierers. Dabei wird vorausgesetzt, dass die generierte Datei im Projekt enthalten ist. Diese Implementierung ist nur ein einfacher Wrapper für die generierte Datei.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Der folgende Codeausschnitt gilt für einen einfachen CSV-Deserialisierer und gibt außerdem Verteilungsfehler an.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Serialisierungsformat für REST-APIs

Jede Stream Analytics-Eingabe hat ein **Serialisierungsformat**. Weitere Informationen zu Eingabeoptionen finden Sie in der Dokumentation zu [Eingabe-REST-APIs](/rest/api/streamanalytics/2016-03-01/inputs).

Der folgende JavaScript-Code ist ein Beispiel für das Serialisierungsformat des .NET-Deserialisierers bei Verwendung der REST-API:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` muss eine Klasse sein, die `StreamDeserializer<T>` implementiert. Dies wird im folgenden Abschnitt beschrieben.

## <a name="region-support"></a>Unterstützung für Regionen

Diese Funktion ist in den folgenden Regionen verfügbar:

* USA, Westen-Mitte
* Nordeuropa
* East US
* USA (Westen)
* USA (Ost) 2
* Europa, Westen

Sie können [Unterstützung](https://aka.ms/ccodereqregion) für weitere Regionen anfordern.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Wann ist diese Funktion in allen Azure-Regionen verfügbar?

Diese Funktion steht in [6 Regionen](#region-support) zur Verfügung. Wenn Sie diese Funktion in einer anderen Region verwenden möchten, können Sie eine entsprechende [Anforderung senden](https://aka.ms/ccodereqregion). Die Unterstützung für alle Azure-Regionen ist in Planung.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Kann ich ähnlich wie bei der GetMetadataPropertyValue-Funktion in meinen Eingaben auf MetadataPropertyValue zugreifen?

Dies wird nicht unterstützt. Wenn Sie diese Funktion benötigen, können Sie in [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese) für diese Anforderung abstimmen.

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Kann ich meine Implementierung des Deserialisierers für die Community freigeben, sodass andere Benutzer sie verwenden können?

Nach der Implementierung des Deserialisierers können Sie ihn für die Community freigeben. Übermitteln Sie den Code an das [Azure Stream Analytics-Repository in GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitations-of-using-custom-deserializers-in-stream-analytics"></a>Welche sonstigen Einschränkungen gelten für die Verwendung benutzerdefinierter Deserialisierer in Stream Analytics?

Wenn die Eingabe im Protobuf-Format mit einem Schema vorliegt, das den `MapField`-Typ enthält, können Sie keinen benutzerdefinierten Deserialisierer implementieren. Außerdem unterstützen benutzerdefinierte Deserialisierer weder Beispiel- noch Vorschaudaten. 

## <a name="next-steps"></a>Nächste Schritte

* [Benutzerdefinierte .NET-Deserialisierer für Azure Stream Analytics-Cloudaufträge](custom-deserializer.md)
