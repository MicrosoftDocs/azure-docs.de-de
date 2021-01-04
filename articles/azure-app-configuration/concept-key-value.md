---
title: Grundlagen des Schlüssel-Wert-Speichers von Azure App Configuration
description: Informieren Sie sich über den Schlüssel-Wert-Speicher in Azure App Configuration, in der Konfigurationsdaten als Schlüsselwerte gespeichert werden. Schlüsselwerte sind eine Darstellung von Anwendungseinstellungen.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: ad9e96433a7ee72476ae2251c684d17ec7a6d1ce
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930244"
---
# <a name="keys-and-values"></a>Schlüssel und Werte

Azure App Configuration speichert Konfigurationsdaten als Schlüssel-Wert-Paare. Schlüssel-Wert-Paare sind eine einfache und flexible Darstellung von Anwendungseinstellungen, die von Entwicklern verwendet werden.

## <a name="keys"></a>Schlüssel

Schlüssel dienen als Bezeichner für Schlüssel-Wert-Paare und werden zum Speichern und Abrufen von entsprechenden Werten verwendet. Es ist eine häufige Vorgehensweise, Schlüssel mit einem Trennzeichen (z. B. `/` oder `:`) in einem hierarchischen Namespace zu organisieren. Verwenden Sie hierbei eine Konvention, die für Ihre Anwendung am besten geeignet ist. Bei App Configuration werden Schlüssel als Ganzes behandelt. Sie werden nicht analysiert, um zu ermitteln, wie ihre Namen strukturiert sind, und es werden auch keine Regeln dafür erzwungen.

Hier sehen Sie ein Beispiel für Schlüsselnamen in einer Hierarchiestruktur, die auf Komponentendiensten basiert:

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

Bei Verwendung von Konfigurationsdaten in Anwendungsframeworks sind möglicherweise bestimmte Benennungsschemas für Schlüssel-Wert-Paare vorgegeben. Beispielsweise definiert das Spring Cloud-Framework von Java `Environment`-Ressourcen, die Einstellungen für eine Spring-Anwendung bereitstellen.  Diese werden durch Variablen parametrisiert, die *Anwendungsname* und *Profil* enthalten. Schlüssel für Spring Cloud-bezogene Konfigurationsdaten beginnen normalerweise mit diesen beiden Elementen, die durch ein Trennzeichen getrennt sind.

Für in App Configuration gespeicherte Schlüssel wird die Groß-/Kleinschreibung beachtet, und es handelt sich um Unicode-basierte Zeichenfolgen. *app1* und *App1* sind in einem App Configuration-Speicher zwei unterschiedliche Schlüssel. Beachten Sie dies, wenn Sie in einer Anwendung Konfigurationseinstellungen verwenden, da für Konfigurationsschlüssel in einigen Frameworks die Groß-/Kleinschreibung nicht beachtet wird. Schlüssel sollten nicht durch Groß-/Kleinschreibung unterschieden werden.

Mit Ausnahme von `%` können Sie beliebige Unicodezeichen in Schlüsselnamen verwenden. Ein Schlüsselname darf auch nicht `.` oder `..` lauten. Für ein Schlüssel-Wert-Paar gilt ein kombiniertes Größenlimit von 10 KB. Dieses Limit umfasst alle Zeichen des Schlüssels, seinen Wert sowie alle zugeordneten optionalen Attribute. Innerhalb dieses Limits können Sie für Schlüssel viele hierarchische Ebenen verwenden.

### <a name="design-key-namespaces"></a>Entwerfen von Schlüsselnamespaces

Es gibt zwei allgemeine Vorgehensweisen beim Benennen von Schlüsseln, die für Konfigurationsdaten verwendet werden: flach und hierarchisch. Aus Sicht der Anwendungsnutzung ähneln sich diese Vorgehensweisen, aber die hierarchische Benennung bietet eine Reihe von Vorteilen:

* Einfachere Lesbarkeit. Trennzeichen in einem hierarchischen Schlüsselnamen fungieren in einem Satz als Leerstellen. Sie bieten außerdem natürliche Unterbrechungen zwischen Wörtern.
* Einfachere Verwaltung. Eine Schlüsselnamenhierarchie umfasst logische Gruppen mit Konfigurationsdaten.
* Einfachere Nutzung. Es ist einfacher, eine Abfrage zu schreiben, bei der sich für Schlüssel in einer hierarchischen Struktur Musterübereinstimmungen ergeben und nur ein Teil der Konfigurationsdaten abgerufen wird. Außerdem verfügen viele neuere Programmierframeworks über native Unterstützung für hierarchische Konfigurationsdaten, damit Ihre Anwendung spezifische Konfigurationssätze nutzen kann.

Sie können Schlüssel in App Configuration auf viele verschiedene Arten hierarchisch organisieren. Stellen Sie sich diese Schlüssel als [URIs](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) vor. Jeder hierarchische Schlüssel ist ein *Ressourcenpfad* mit einer oder mehreren Komponenten, die durch Trennzeichen verbunden sind. Wählen Sie aus, welches Zeichen Sie basierend auf den Anforderungen Ihrer Anwendung, Ihrer Programmiersprache oder Ihrem Framework verwenden möchten. Verwenden Sie für unterschiedliche Schlüssel verschiedene Trennzeichen in App Configuration.

### <a name="label-keys"></a>Bezeichnen von Schlüsseln

Schlüsselwerte in App Configuration können optional über das Attribut label (Bezeichnung) verfügen. Bezeichnungen werden genutzt, um zwischen Schlüsselwerten mit demselben Schlüssel zu unterscheiden. Ein Schlüssel *app1* mit den Bezeichnungen *A* und *B* steht in einem App Configuration-Speicher für zwei separate Schlüssel. Standardmäßig weist ein Schlüssel-Wert-Paar keine Bezeichnung auf. Verwenden Sie `\0` (URL-codiert als `%00`), um explizit auf ein Schlüssel-Wert-Paar ohne Bezeichnung zu verweisen.

Eine Bezeichnung ist praktisch zum Erstellen von Varianten eines Schlüssels. Mit Bezeichnungen werden häufig mehrere Umgebungen für den gleichen Schlüssel angegeben:

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Versionsverwaltung für Schlüssel-Wert-Paare

Verwenden Sie Bezeichnungen zum Erstellen mehrerer Versionen eines Schlüssel-Wert-Paars. Beispielsweise können Sie eine Zahl einer Anwendungsversion oder eine Git-Commit-ID in Bezeichnungen eingeben, um Schlüsselwerte zu identifizieren, die einem bestimmten Softwarebuild zugeordnet sind.

> [!NOTE]
> Wenn Sie nach den Änderungen zwischen Versionen suchen, speichert App Configuration automatisch alle bisherigen Änderungen an einem Schlüssel-Wert-Paar für einen gewissen Zeitraum. Weitere Informationen finden Sie unter [Point-in-Time-Momentaufnahme](./concept-point-time-snapshot.md).

### <a name="query-key-values"></a>Abfragen von Schlüssel-Wert-Paaren

Jeder Schlüsselwert wird anhand seines Schlüssels und einer Bezeichnung, die auch `\0` lauten kann, eindeutig identifiziert. Sie können einen App Configuration-Speicher auf Schlüssel-Wert-Paare abfragen, indem Sie ein Muster angeben. Der App Configuration-Speicher gibt alle Schlüssel-Wert-Paare zurück, die mit dem Muster und den entsprechenden Werten und Attributen übereinstimmen. Verwenden Sie die folgenden Schlüsselmuster in REST-API-Aufrufen für App Configuration:

| Key | BESCHREIBUNG |
|---|---|
| `key` wird weggelassen oder lautet `key=*` | Übereinstimmung mit allen Schlüsseln |
| `key=abc` | Exakte Übereinstimmung mit dem Schlüsselnamen **abc** |
| `key=abc*` | Übereinstimmung mit Schlüsselnamen, die mit **abc** beginnen |
| `key=abc,xyz` | Übereinstimmung mit Schlüsselnamen mit **abc** oder **xyz**. Auf fünf CSVs beschränkt. |

Sie können auch die folgenden Bezeichnungsmuster verwenden:

| Bezeichnung | BESCHREIBUNG |
|---|---|
| `label` wird weggelassen oder lautet `label=*` | Übereinstimmung mit einer beliebigen Bezeichnung, einschließlich `\0` |
| `label=%00` | Übereinstimmung mit der Bezeichnung `\0` |
| `label=1.0.0` | Exakte Übereinstimmung mit der Bezeichnung **1.0.0** |
| `label=1.0.*` | Übereinstimmung mit Bezeichnungen, die mit **1.0.** beginnen |
| `label=%00,1.0.0` | Übereinstimmung mit den Bezeichnungen `\0` oder **1.0.0**, auf fünf CSVs beschränkt |

> [!NOTE]
> `*`, `,` und `\` sind reserviertes Zeichen in Abfragen. Wenn ein reserviertes Zeichen in Ihren Schlüsselnamen oder Bezeichnungen verwendet wird, müssen Sie diese in Abfragen mit dem Escapezeichen `\{Reserved Character}` versehen.

## <a name="values"></a>Werte

Bei Werten, die Schlüsseln zugewiesen sind, handelt es sich ebenfalls um Unicode-Zeichenfolgen. Sie können alle Unicode-Zeichen für Werte verwenden.

### <a name="use-content-type"></a>Verwenden des Attributs „Content-Type“
Jedes Schlüssel-Wert-Paar in App Configuration verfügt über ein Attribut namens „content-type“. Sie können dieses Attribut optional verwenden, um Informationen über den Typ eines Werts in einem Schlüssel-Wert-Paar zu speichern, um Ihre Anwendung bei der ordnungsgemäßen Verarbeitung zu unterstützen. Sie können ein beliebiges Format für das Attribut „content-type“ verwenden. App Configuration verwendet [Medientypen]( https://www.iana.org/assignments/media-types/media-types.xhtml) (auch als MIME-Typen bezeichnet) für integrierte Datentypen, z. B. für Featureflags, Key Vault-Verweise und JSON-Schlüssel-Wert-Paare.

## <a name="next-steps"></a>Nächste Schritte

* [Point-in-Time-Momentaufnahme](./concept-point-time-snapshot.md)
* [Funktionsverwaltung](./concept-feature-management.md)
* [Behandlung von Ereignissen](./concept-app-configuration-event.md)
