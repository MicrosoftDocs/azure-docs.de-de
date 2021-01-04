---
title: Datennormalisierung in Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Azure Sentinel Daten aus vielen verschiedenen Quellen normalisiert, und das Normalisierungsschema im Detail dargelegt.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: a9d2cd48e3b686614f7361d2007f6f8183c2361e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657022"
---
# <a name="normalization-in-azure-sentinel"></a>Normalisierung in Azure Sentinel

In diesem Artikel wird die Datenschemanormalisierung in Azure Sentinel erläutert, unter besonderer Berücksichtigung von Netzwerkverbindungen und Sitzungsschema, zu dem ein Link bereitgestellt wird.

## <a name="what-is-normalization"></a>Was ist Normalisierung?

Das kombinierte Arbeiten mit verschiedenen Datentypen und Tabellen hält Herausforderungen bereit. Sie müssen sich mit vielen verschiedenen Datentypen und Schemas vertraut machen und für jeden einen separaten Satz von Analyseregeln, Workbooks und Hunting-Abfragen schreiben und verwenden – selbst für solche, die Gemeinsamkeiten aufweisen (beispielsweise den Bezug auf Firewallgeräte). Auch die Korrelation zwischen den verschiedenen Datentypen, die für Untersuchung und Hunting erforderlich ist, ist schwierig. Azure Sentinel bietet eine nahtlose Methode für die Verarbeitung von Daten aus verschiedenen Quellen in einheitlichen, normalisierten Sichten.

Das **Common Information Model** von Azure Sentinel besteht aus drei Aspekten:

- **Normalisierte Schemas** decken allgemeine Sätze von vorhersagbaren Ereignistypen (Tabellen) ab, mit denen sich leicht arbeiten lässt und auf denen einheitliche Funktionen aufgebaut werden können (z. B. eine Netzwerktabelle). Das Schema enthält darüber hinaus eine Konvention für normalisierte Spalten und die Formatstandardisierung (standardkonforme Darstellung von Daten wie IP-Adressen).

- **Parser** ordnen dem normalisierten Schema vorhandene Daten verschiedener Typen zu. Gemäß dem Modell können Daten zum Abfragezeitpunkt (mithilfe von Funktionen) oder zum Erfassungszeitpunkt in das normalisierte Schema analysiert (geparst) werden. Zurzeit wird nur die Analyse zur Abfragezeit unterstützt.

- **Die Inhalte für jedes normalisierte Schema** umfassen Analyseregeln, interaktive Workbooks, Hunting-Abfragen und weitere Inhalte.

### <a name="current-release"></a>Aktuelles Release

Mit diesem Release steht das [normalisierte Schema für Netzwerkverbindungen und Sitzungen](./normalization-schema.md) (v1.0.0) als öffentliche Vorschauversion zur Verfügung. Das Schema beschreibt Netzwerkverbindungen oder -sitzungen, etwa die durch Firewalls, Wire Data, NSG, Netflow, Proxysysteme und Websicherheits-Gateways protokollierten.  In Ergänzung dieses Schemas stehen ausgewählte Abfragezeitparser und Analyseregeln zur Verfügung, die das Schema verwenden.

Das Schema ist aktuell nur in Verbindung mit Abfragezeitparsern nutzbar (mehr dazu finden Sie im Abschnitt über Parser).

Sie können Daten in zusätzliche Darstellungen analysieren und das [OSSEM-Benennungsmodell für Entitäten](https://ossemproject.com/cdm/entities/intro.html#) verwenden, um Spalten zu erstellen, die mit vorhandenen und künftigen normalisierten Tabellen konsistent sind.

## <a name="normalized-schema-and-parsing"></a>Normalisiertes Schema und Analyse

### <a name="how-our-normalized-schemas-are-defined"></a>Definition unserer normalisierten Schemas

Azure Sentinel ist am Common Information Model [Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html) ausgerichtet und ermöglicht daher die vorhersagbare Korrelation von Entitäten über normalisierte Tabellen hinweg. OSSEM ist ein von der Community betriebenes Projekt, das hauptsächlich auf die Dokumentation und Standardisierung von Sicherheitsereignisprotokollen aus unterschiedlichen Datenquellen und Betriebssystemen ausgerichtet ist. Außerdem stellt das Projekt ein Common Information Model (CIM) bereit, das während der Datennormalisierungsprozeduren von Datentechnikern verwendet werden kann, damit Sicherheitsanalytiker Daten über verschiedene Datenquellen hinweg abfragen und analysieren können.

Das [OSSEM-CIM](https://ossemproject.com/cdm/intro.html) definiert eine Reihe von Entitäten (z. B. Datei, Host, IP, Prozess) und eine Reihe von Attributen für jede derartige Entität. Darüber hinaus definiert CIM eine Reihe von Tabellen (beispielsweise die Tabelle [Netzwerksitzung](https://ossemproject.com/cdm/tables/network_session.html)), die relevante Attribute dieser Entitäten verwenden, was eine nahtlose und vorhersagbare Korrelation ermöglicht. Beachten Sie, dass Entitäten verschachtelt werden können (beispielsweise kann die Entität „Quelle“ eine Entität „Datei“ mit einem Namensattribut enthalten).

Weitere Informationen zur OSSEM-Entitätsstruktur finden Sie in der [offiziellen OSSEM-Referenz](https://ossemproject.com/cdm/guidelines/entity_structure.html).

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Implementierung der normalisierten Schemas in Azure Sentinel

In der Implementierung des OSSEM-CIMs in Azure Sentinel projizieren wir die OSSEM-Darstellung auf eine Log Analytics-Tabellendarstellung, gleich, ob es sich dabei um eine integrierte Tabelle oder eine mithilfe von Abfragezeitparsern oder Funktionen erstellte Tabelle handelt, die dieser Darstellung die vorhandenen Daten zuordnen. Für die Tabellendarstellung verketten wir die Entitätsnamen und Attributnamen gemäß OSSEM und ordnen sie kollektiv einem einzelnen Spaltennamen zu. Beispielsweise wird eine Quell-Entität, die eine Datei-Entität enthält, die ihrerseits eine Hash-Entität enthält, die ein MD5-Attribut enthält, in folgender Weise als Log Analytics-Spalte implementiert: SrcFileHashMd5. (OSSEM verwendet standardmäßig *snake_case*, während Azure Sentinel und Log Analytics *PascalCase* verwenden. In OSSEM würde eine solche Spalte „src_file_hash_md5“ lauten.)

In der Azure Sentinel-Implementierung sind möglicherweise zusätzliche benutzerdefinierte Felder vorhanden, da es Plattform-Anforderungen von Log Analytics und Anwendungsfälle geben kann, die für Azure Sentinel Kunden spezifisch sind.

### <a name="schema-reference"></a>Schemareferenz

Weitere Informationen finden Sie in dem [Schemareferenzdokument](./normalization-schema.md) sowie in der offiziellen [OSSEM-Projektdokumentation](https://ossemproject.com/cdm/intro.html).

Die Schemareferenz enthält außerdem die Wert- und Formatstandardisierung. Die Quelldateien, seien sie im ursprünglichen Zustand oder bereits analysiert, liegen möglicherweise nicht in einem Standardformat vor oder verwenden nicht die Standardwertliste des Schemas und müssen daher in die Standarddarstellung des Schemas konvertiert werden, um vollständig normalisiert werden zu können.

## <a name="parsers"></a>Parser

### <a name="what-is-parsing"></a>Die Funktion von Analyse

Bei einer verfügbaren Grundmenge von normalisierten Tabellen müssen Sie Ihre Daten in diese Tabellen transformieren (analysieren/zuordnen). Das heißt, sie extrahieren spezifische Daten aus ihrer Rohform in wohl definierte Spalten im normalisierten Schema. Die Analyse erfolgt in Azure Sentinel zur **Abfragezeit**: Parser werden als Log Analytics-Benutzerfunktionen erstellt (mithilfe der Kusto Query Language – KQL), die Daten in vorhandene Tabellen (wie etwa CommonSecurityLog, benutzerdefinierte Protokolltabellen, Syslog) im normalisierten Tabellenschema transformieren.

Die andere Art der Analyse, die von Azure Sentinel noch nicht unterstützt wird, erfolgt zur **Erfassungszeit** und ermöglicht es, Daten bei der Erfassung aus den Datenquellen direkt in den normalisierten Tabellen zu sammeln. Die Analyse zur Erfassungszeit bietet verbesserte Leistung, da das Datenmodell direkt abgefragt wird, ohne die Notwendigkeit der Verwendung von Funktionen.

### <a name="using-query-time-parsers"></a>Verwenden von Abfragezeitparsern

#### <a name="installing-a-parser"></a>Installieren eines Parsers

Die verfügbaren Abfragezeitparser stehen im [offiziellen GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0)) von Azure Sentinel zur Verfügung. Jeder Parser ist mit einer Versionsnummer versehen, damit die Kunden kommende Updates komfortabel feststellen und nutzen können. So installieren Sie einen Parser:

1. Kopieren Sie den relevanten Parserinhalt aus jeder relevanten KQL-Datei aus dem GitHub-Link oben in die Zwischenablage.

1. Öffnen Sie im Azure Sentinel-Portal die Seite „Protokolle“, und fügen Sie den Inhalt der KQL-Datei in den Protokollbildschirm ein. Klicken Sie dann auf **Speichern**.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Installieren eines neuen Parsers":::

1. Füllen Sie im Speichern-Dialogfeld die Felder wie folgt auf:
    1. **Name**: Es empfiehlt sich, den gleichen Namen wie im Feld **Function Alias** (Funktionsalias) zu verwenden (im Beispiel oben ist das *CheckPoint_Network_NormalizedParser*)
    
    1. **Speichern unter**: wählen Sie **Function** (Funktion) aus.

    1. **Funktionsalias**: Sollte gemäß der folgenden Benennungskonvention benannt werden: *PRODUKT_Netzwerk_NormalisierterParser* (im Beispiel oben *CheckPoint_Network_NormalizedParser*).

    1. **Kategorie**: Sie können eine vorhandene Kategorie auswählen oder eine neue Kategorie erstellen (z. B. *NormalizedNetworkSessionsParsers*)
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="Speichern des Parsers":::

Um die Parser ordnungsgemäß verwenden zu können, müssen Sie außerdem den Netzwerkschemaparser „Empty“ (Leer) (der eine leere Tabellensicht aller Felder des Netzwerksitzungschemas erstellt) und den Netzwerkmetaparser installieren (der alle aktivierten Parser zusammenführt, um eine einzelne Sicht der Daten aus verschiedenen Quellen im Netzwerkschema zu erstellen). Die Installation dieser beiden Parser erfolgt in ähnlicher Weise wie die oben aufgeführten Schritte.

Beim Speichern einer Abfragefunktion kann es erforderlich sein, den Abfrage-Explorer zu schließen und ihn erneut zu öffnen, damit die neue Funktion angezeigt wird.

#### <a name="using-the-parsers"></a>Verwenden der Parser

Nach der Aktivierung können Sie den Metaparser verwenden, um eine vereinheitlichte Sicht aller aktuell aktivierten Parser abzufragen. Navigieren Sie zu diesem Zweck zur Protokollseite von Sentinel, und fragen Sie den Metaparser ab:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Abfragen des Parsers":::
 
Sie können auf den Metaparser oder die einzelnen Parser auch mithilfe des Abfrage-Explorers auf der Protokollseite von Sentinel zugreifen, indem Sie auf „Abfrage-Explorer“ klicken:

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Abfrage-Explorer":::

Erweitern Sie in der rechten Seitenleiste den Bereich „Gespeicherte Abfragen“, und suchen Sie den Ordner ‚NormalizedNetworkParsers‘ (oder den Kategorienamen, den Sie beim Erstellen der Parser gewählt haben):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Suchen des Parsers":::

Sie können auf jeden einzelnen Parser klicken, die verwendete zugrundeliegende Funktion anzeigen und ihn ausführen (oder direkt über seinen Alias auf ihn zugreifen, wie oben beschrieben). Beachten Sie, dass einige Parser aus Komfortgründen die ursprünglichen Felder parallel zu den normalisierten Feldern beibehalten können. Dieses Verhalten kann leicht in der Abfrage des Parsers bearbeitet werden.

#### <a name="customizing-parsers"></a>Anpassen von Parsern

Sie können die Schritte oben wiederholen (den Parser im Abfrage-Explorer suchen), auf den relevanten Parser klicken und seine Funktionsimplementierung anzeigen.
Beispielsweise können Sie sich entscheiden, den Metaparser zu bearbeiten, um einzelne Parser hinzuzufügen/zu entfernen.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Anpassen Ihres Parsers":::
 
Sobald die Funktion geändert wurde, klicken Sie erneut auf „Speichern“, und verwenden Sie die gleichen Werte für Name, Alias und Kategorie. Ein Dialogfeld zum Überschreiben wird geöffnet – drücken Sie auf „OK“:

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="Sind Sie sicher":::

#### <a name="additional-information"></a>Zusätzliche Informationen

Erfahren Sie mehr über [gespeicherte Abfragen](../azure-monitor/log-query/example-queries.md) (die Implementierung der Abfragezeitparser) in Log Analytics.


## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie grundlegende Informationen zum Normalisierungsschema von Azure Sentinel erhalten. Das eigentliche Referenzschema finden Sie unter [Azure Sentinel: Schemareferenz zur Datennormalisierung](./normalization-schema.md).

* [Azure Sentinel-Blog](https://aka.ms/azuresentinelblog). Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.