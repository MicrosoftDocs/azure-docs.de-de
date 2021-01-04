---
title: Wranglingdatenflüsse in Azure Data Factory
description: Ein Übersicht über Wranglingdatenflüsse in Azure Data Factory
author: dcstwh
ms.author: weetok
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 607ed6167ec761a6bf380a2cc420eb72f808f0a3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494867"
---
# <a name="what-are-wrangling-data-flows"></a>Was sind Wranglingdatenflüsse?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Organisationen müssen Datenaufbereitung und Data Wrangling durchführen, um komplexe Daten, deren Menge täglich wächst, exakt analysieren zu können. Die Datenaufbereitung ist erforderlich, damit Organisationen die Daten in verschiedenen Geschäftsprozessen verwenden und die Amortisationszeit verkürzen können.

Wranglingdatenflüsse in Azure Data Factory ermöglicht Ihnen das iterative Vorbereiten von Daten ohne Code für die Cloud. Wranglingdatenflüsse können in [Power Query Online](/power-query/) integriert werden und stellen Power Query M-Funktionen für Data Factory-Benutzer bereit.

Wranglingdatenflüsse übersetzen M-Code, der vom Power Query Online Mashup Editor generiert wird, in Spark-Code für die Cloudskalierungsausführung.

Wranglingdatenflüsse sind besonders nützlich für Datentechniker oder für Datenintegratoren ohne Programmiererfahrung.

> [!NOTE]
> Wranglingdatenflüsse derzeit in der öffentlichen Vorschau verfügbar

## <a name="use-cases"></a>Anwendungsfälle

### <a name="fast-interactive-data-exploration-and-preparation"></a>Schnelle interaktive Untersuchung und Aufbereitung von Daten

Mehrere Datentechniker und Datenintegratoren ohne Programmiererfahrung können Datasets in der Cloud interaktiv untersuchen und aufbereiten. Mit zunehmender Menge, Vielfalt und Geschwindigkeit der Daten in Data Lakes benötigen Benutzer eine effektive Möglichkeit zum Untersuchen und Aufbereiten von Datasets. Beispielsweise müssen Sie möglicherweise ein Dataset erstellen, das über alle demografischen Informationen zu neuen Kunden seit 2017 verfügt. Sie nehmen keine Zuordnung zu einem bekannten Ziel vor. Vor dem Veröffentlichen im Lake unterziehen Sie Datasets der Untersuchung, dem Wrangling und der Aufbereitung, damit sie eine Anforderung erfüllen. Wranglingdatenflüsse werden oft für weniger formale Analyseszenarios verwendet. Die aufbereiteten Datasets können für Downstreamtransformationen und -Machine Learning-Vorgänge verwendet werden.

### <a name="code-free-agile-data-preparation"></a>Codefreie agile Datenaufbereitung

Die Datenintegratoren ohne Programmiererfahrung verbringen mehr als 60 % ihrer Zeit mit dem Suchen nach und Aufbereiten von Daten. Sie versuchen, dies ohne Code durchzuführen, um die operative Produktivität zu verbessern. Indem Datenintegratoren ohne Programmiererfahrung die Möglichkeit erhalten, Daten mit bekannten Tools wie Power Query Online skalierbar zu erweitern, zu strukturieren und zu veröffentlichen, wird ihre Produktivität erheblich verbessert. Ein Wranglingdatenfluss aktiviert in Azure Data Factory den vertrauten Power Query Online Mashup Editor, damit Datenintegratoren ohne Programmiererfahrung schnell Fehler beheben, Daten standardisieren und qualitativ hochwertige Daten zur Unterstützung von Geschäftsentscheidungen produzieren können.

### <a name="data-validation"></a>Datenüberprüfung

Überprüfen Sie Ihre Daten visuell ohne Code, um alle Ausreißer sowie Anomalien zu entfernen und sie in eine Form zu bringen, die eine schnelle Analyse ermöglicht.

## <a name="supported-sources"></a>Unterstützte Quellen

| Connector | Datenformat | Authentifizierungsart |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | Kontoschlüssel |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Dienstprinzipal |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | Kontoschlüssel, Dienstprinzipal |
| [Azure SQL-Datenbank](connector-azure-sql-database.md) | - | SQL-Authentifizierung |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-Authentifizierung |

## <a name="the-mashup-editor"></a>Mashup-Editor

Wenn Sie einen Wranglingdatenfluss erstellen, werden alle Quelldatasets zu Datenbankabfragen, die im Ordner **ADFResource** platziert werden. Standardmäßig zeigt die UserQuery auf die erste Datasetabfrage. Alle Transformationen sind für die UserQuery durchzuführen, da Änderungen an Datasetabfragen weder unterstützt noch beibehalten werden. Das Umbenennen, Hinzufügen und Löschen von Abfragen werden derzeit nicht unterstützt.

![Wrangling](media/wrangling-data-flow/editor.png)

Derzeit werden nicht alle Power Query M-Funktionen für Data Wrangling unterstützt, obwohl sie während der Erstellung verfügbar sind. Beim Erstellen von Wranglingdatenflüssen wird die folgende Fehlermeldung angezeigt, wenn eine Funktion nicht unterstützt wird:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Weitere Informationen zu unterstützten Transformationen finden Sie unter [Funktionen im Wranglingdatenfluss](wrangling-data-flow-functions.md).

Derzeit unterstützt der Wranglingdatenfluss nur das Schreiben in eine Senke.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [einen Wranglingdatenfluss erstellen](wrangling-data-flow-tutorial.md).