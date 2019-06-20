---
title: Schlüssel-Wert-Speicher von Azure App Configuration | Microsoft-Dokumentation
description: Enthält eine Übersicht über die Speicherung von Konfigurationsdaten in Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: b25cc8c04aed8cd333ff4de5b12db6674323787d
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393600"
---
# <a name="keys-and-values"></a>Schlüssel und Werte

Azure App Configuration speichert Konfigurationsdaten als Schlüssel-Wert-Paare. Schlüssel-Wert-Paare sind eine einfache und flexible Methode zur Darstellung verschiedener Arten von Anwendungseinstellungen, mit denen Entwickler vertraut sind.

## <a name="keys"></a>Schlüssel

Schlüssel dienen als Name für Schlüssel-Wert-Paare und werden zum Speichern und Abrufen von entsprechenden Werten verwendet. Es ist eine häufige Vorgehensweise, Schlüssel mit einem Trennzeichen (z. B. `/` oder `:`) in einem hierarchischen Namespace zu organisieren. Verwenden Sie hierbei eine Konvention, die für Ihre Anwendung am besten geeignet ist. Bei App Configuration werden Schlüssel als Ganzes behandelt. Sie werden nicht analysiert, um zu ermitteln, wie ihre Namen strukturiert sind, und es werden auch keine Regeln dafür erzwungen.

Bei Verwendung von Konfigurationsdaten in Anwendungsframeworks sind möglicherweise bestimmte Benennungsschemas für Schlüssel-Wert-Paare vorgegeben. Für das Spring Cloud-Framework von Java werden beispielsweise `Environment`-Ressourcen definiert, die Einstellungen für eine Spring-Anwendung für die Parametrisierung durch Variablen, z. B. *application name* und *profile* bereitstellen. Schlüssel für Spring Cloud-bezogene Konfigurationsdaten beginnen normalerweise mit diesen beiden Elementen, die durch ein Trennzeichen getrennt sind.

Für in App Configuration gespeicherte Schlüssel wird die Groß-/Kleinschreibung beachtet, und es handelt sich um Unicode-basierte Zeichenfolgen. *app1* und *App1* sind in einem App-Konfigurationsspeicher zwei einzelne Schlüssel. Beachten Sie dies, wenn Sie in einer Anwendung Konfigurationseinstellungen verwenden, da für Konfigurationsschlüssel in einigen Frameworks die Groß-/Kleinschreibung nicht beachtet wird. Vom ASP.NET Core-Konfigurationssystem werden Schlüssel beispielsweise als Zeichenfolgen ohne Berücksichtigung der Groß-/Kleinschreibung behandelt. Um beim Abfragen von App Configuration in einer ASP.NET Core-Anwendung unvorhersehbares Verhalten zu vermeiden, verwenden Sie keine Schlüssel, die sich nur anhand der Groß-/Kleinschreibung unterscheiden.

Sie können in Schlüsselnamen, die in App Configuration eingegeben werden, beliebige Unicode-Zeichen verwenden, mit Ausnahme von `*`, `,` und `\`. Diese Zeichen sind reserviert. Wenn Sie ein reserviertes Zeichen einfügen möchten, müssen Sie es wie folgt mit Escapezeichen versehen: `\{Reserved Character}`. Für ein Schlüssel-Wert-Paar gilt ein kombiniertes Größenlimit von 10.000 Zeichen. Dieses Limit umfasst alle Zeichen des Schlüssels, seinen Wert sowie alle zugeordneten optionalen Attribute. Innerhalb dieses Limits können Sie für Schlüssel viele hierarchische Ebenen verwenden.

### <a name="design-key-namespaces"></a>Entwerfen von Schlüsselnamespaces

Es gibt zwei allgemeine Vorgehensweisen beim Benennen von Schlüsseln, die für Konfigurationsdaten verwendet werden: flach und hierarchisch. Aus Sicht der Anwendungsnutzung ähneln sich diese Vorgehensweisen, aber die hierarchische Benennung bietet eine Reihe von Vorteilen:

* Einfachere Lesbarkeit. Um lange Folgen von Zeichen zu vermeiden, fungieren Trennzeichen in einem hierarchischen Schlüsselnamen als Leerstellen in einem Satz. Sie bieten außerdem natürliche Unterbrechungen zwischen Wörtern.
* Einfachere Verwaltung. Eine Schlüsselnamenhierarchie umfasst logische Gruppen mit Konfigurationsdaten.
* Einfachere Nutzung. Es ist einfacher, eine Abfrage zu schreiben, bei der sich für Schlüssel in einer hierarchischen Struktur Musterübereinstimmungen ergeben und nur ein Teil der Konfigurationsdaten abgerufen wird. Außerdem verfügen viele neuere Programmierframeworks über native Unterstützung für hierarchische Konfigurationsdaten, damit Ihre Anwendung spezifische Konfigurationssätze nutzen kann.

Sie können Schlüssel in App Configuration auf viele verschiedene Arten hierarchisch organisieren. Stellen Sie sich diese Schlüssel als [URIs](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) vor. Jeder hierarchische Schlüssel ist ein *Ressourcenpfad* mit einer oder mehreren Komponenten, die durch Trennzeichen verbunden sind. Wählen Sie aus, welches Zeichen Sie basierend auf den Anforderungen Ihrer Anwendung, Ihrer Programmiersprache oder Ihrem Framework verwenden möchten. Verwenden Sie für unterschiedliche Schlüssel verschiedene Trennzeichen in App Configuration.

Hier sind einige Beispiele dafür angegeben, wie Sie Ihre Schlüsselnamen in einer Hierarchie strukturieren können:

* Basierend auf Komponentendiensten

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Basierend auf Bereitstellungsregionen

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Bezeichnen von Schlüsseln

Schlüsselwerte in App Configuration können optional über das Attribut „label“ (Bezeichnung) verfügen. Bezeichnungen werden genutzt, um zwischen Schlüsselwerten mit demselben Schlüssel zu unterscheiden. Ein Schlüssel *app1* mit den Bezeichnungen *A* und *B* steht in einem App-Konfigurationsspeicher für zwei separate Schlüssel. Standardmäßig ist die Bezeichnung für einen Schlüsselwert leer oder `null`.

Eine Bezeichnung ist praktisch zum Erstellen von Varianten eines Schlüssels. Mit Bezeichnungen werden häufig mehrere Umgebungen für den gleichen Schlüssel angegeben:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Versionsverwaltung von Schlüsselwerten

App Configuration versieht Schlüsselwerte nicht automatisch mit Versionsangaben, wenn sie geändert werden. Verwenden Sie Bezeichnungen als Möglichkeit zum Erstellen von mehreren Versionen eines Schlüsselwerts. Beispielsweise können Sie eine Zahl einer Anwendungsversion oder eine Git-Commit-ID in Bezeichnungen eingeben, um Schlüsselwerte zu identifizieren, die einem bestimmten Softwarebuild zugeordnet sind.

Sie können in Bezeichnungen beliebige Unicodezeichen verwenden, mit Ausnahme von `*`, `,` und `\`. Diese Zeichen sind reserviert. Um ein reserviertes Zeichen einzufügen, müssen Sie es wie folgt mit Escapezeichen versehen: `\{Reserved Character}`.

### <a name="query-key-values"></a>Abfragen von Schlüsselwerten

Jeder Schlüsselwert wird anhand seines Schlüssels und einer Bezeichnung, die auch `null` lauten kann, eindeutig identifiziert. Sie fragen Schlüsselwerte aus einem App-Konfigurationsspeicher ab, indem Sie ein Muster angeben. Der App-Konfigurationsspeicher gibt alle Schlüsselwerte zurück, die mit dem Muster und den entsprechenden Werten und Attributen übereinstimmen. Verwenden Sie die folgenden Schlüsselmuster in REST-API-Aufrufen für App Configuration:

| Schlüssel | |
|---|---|
| `key` wird weggelassen oder lautet `key=*` | Übereinstimmung mit allen Schlüsseln |
| `key=abc` | Exakte Übereinstimmung mit dem Schlüsselnamen **abc** |
| `key=abc*` | Übereinstimmung mit Schlüsselnamen, die mit **abc** beginnen |
| `key=*abc` | Übereinstimmung mit Schlüsselnamen, die auf **abc** enden |
| `key=*abc*` | Übereinstimmung mit Schlüsselnamen, die **abc** enthalten |
| `key=abc,xyz` | Übereinstimmung mit Schlüsselnamen mit **abc** oder **xyz**, auf fünf CSVs beschränkt |

Sie können auch die folgenden Bezeichnungsmuster verwenden:

| Bezeichnung | |
|---|---|
| `label` wird weggelassen oder lautet `label=*` | Übereinstimmung mit einer beliebigen Bezeichnung, einschließlich `null` |
| `label=%00` | Übereinstimmung mit der Bezeichnung `null` |
| `label=1.0.0` | Exakte Übereinstimmung mit der Bezeichnung **1.0.0** |
| `label=1.0.*` | Übereinstimmung mit Bezeichnungen, die mit **1.0.** beginnen |
| `label=*.0.0` | Übereinstimmung mit Bezeichnungen, die auf **.0.0** enden |
| `label=*.0.*` | Übereinstimmung mit Bezeichnungen, die **.0.** enthalten |
| `label=%00,1.0.0` | Übereinstimmung mit den Bezeichnungen `null` oder **1.0.1**, auf fünf CSVs beschränkt |

## <a name="values"></a>Werte

Bei Werten, die Schlüsseln zugewiesen sind, handelt es sich ebenfalls um Unicode-Zeichenfolgen. Sie können alle Unicode-Zeichen für Werte verwenden. Jedem Wert ist ein optionaler, benutzerdefinierter Inhaltstyp zugeordnet. Verwenden Sie dieses Attribut, um Informationen (z. B. ein Codierungsschema) zu einem Wert zu speichern, die Ihrer Anwendung die richtige Verarbeitung ermöglichen.

In einem App-Konfigurationsspeicher gespeicherte Konfigurationsdaten, was alle Schlüssel und Werte einschließt, werden im ruhenden Zustand und während der Übertragung verschlüsselt. App Configuration ist keine Ersatzlösung für Azure Key Vault. Speichern Sie darin keine Anwendungsgeheimnisse.

## <a name="next-steps"></a>Nächste Schritte

* [Point-in-Time-Momentaufnahme](./concept-point-time-snapshot.md)  
* [Funktionsverwaltung](./concept-feature-management.md)  
