---
title: Schemaabweichung im Zuordnungsdatenfluss
description: Erstellen robuster Datenflüsse in Azure Data Factory mit Schemaabweichung
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 11ddb2f40ee56b51c5ecbae11465093abb8e4feb
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027481"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Schemaabweichung im Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Eine Schemaabweichung liegt vor, wenn Ihre Quellen Metadaten häufig ändern. Felder, Spalten und Typen können bei laufendem Betrieb hinzugefügt, entfernt oder geändert werden. Ohne Vorkehrungen bei Schemaabweichungen wird Ihr Datenfluss anfällig für Änderungen an vorgelagerten Datenquellen. Wenn sich eingehende Spalten und Felder ändern, treten bei typischen ETL-Mustern Fehler auf, da sie dazu neigen, an diesen Quellennamen gebunden zu sein.

Zum Schutz gegen Schemaabweichung muss ein Datenflusstool Funktionen bieten, die Ihnen als Datentechniker Folgendes ermöglichen:

* Definieren von Quellen mit änderbaren Feldnamen, Datentypen, Werte und Größen
* Definieren von Transformationsparametern, die anstelle von hartcodierten Feldern und Werten mit Datenmustern arbeiten können
* Definieren von Ausdrücken, die Muster verstehen, um sie mit Eingangsfeldern abzugleichen, anstatt benannte Felder zu verwenden

Flexible und sich von Ausführung zu Ausführung ändernde Schemas werden von Azure Data Factory nativ unterstützt, sodass Sie eine generische Datentransformationslogik erstellen können, ohne die Datenflüsse erneut kompilieren zu müssen.

Sie müssen eine architektonische Entscheidung in Ihrem Datenfluss treffen, um die Schemaabweichung in Ihrem Fluss zu akzeptieren. Wenn Sie dies tun, können Sie Schutz gegen Schemaänderungen aus den Quellen bieten. Allerdings verlieren Sie die frühe Bindung Ihrer Spalten und Typen im gesamten Datenfluss. Azure Data Factory behandelt Schemaabweichungsflüsse als Flüsse mit später Bindung, sodass die abweichenden Spaltennamen bei der Erstellung Ihrer Transformationen Ihnen nicht im gesamten Fluss in den Schemaansichten zur Verfügung stehen.

Dieses Video bietet eine Einführung in einige der komplexen Lösungen, die Sie in ADF mithilfe der Schemaabweichungsfunktion für den Datenfluss einfach erstellen können. In diesem Beispiel erstellen wir basierend auf flexiblen Datenbankschemas wiederverwendbare Muster:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Schemaabweichung in der Quelle

Spalten, die Ihrem Datenfluss aus Ihrer Quelldefinition hinzugefügt werden, sind als abweichende Spalten definiert, wenn sie nicht in Ihrer Quellprojektion vorhanden sind. Ihre Quellprojektion können Sie sich auf der Registerkarte „Projektion“ der Quelltransformation ansehen. Wenn Sie ein Dataset für Ihre Quelle auswählen, verwendet ADF automatisch das Schema dieses Datasets und erstellt auf dieser Grundlage eine Projektion der Schemadefinition.

Bei einer Quelltransformation ist die Schemaabweichung definiert als das Lesen von Spalten, die nicht in Ihrem Datasetschema definiert sind. Um Schemaabweichung zu ermöglichen, wählen Sie in Ihrer Quellentransformation **Allow Schema Drift** (Schemaabweichung zulassen) aus.

![Schemaabweichung (Quelle)](media/data-flow/schemadrift001.png "Schemaabweichung (Quelle)")

Bei aktivierter Schemaabweichung werden während der Ausführung alle eingehenden Felder aus Ihrer Quelle gelesen und durch den gesamten Datenfluss zur Senke geleitet. Alle neu erkannten Spalten ( *abweichende Spalten* genannt) werden standardmäßig mit dem Datentyp „String“ empfangen. Wenn Sie möchten, dass der Datenfluss automatisch Datentypen abweichender Spalten ableiten kann, aktivieren Sie in Ihren Quelleinstellungen die Option **Infer drifted column types** (Abweichende Spaltentypen ableiten).

## <a name="schema-drift-in-sink"></a>Schemaabweichung in Senke

Bei einer Senkentransformation erfolgt eine Schemabweichung, wenn Sie zusätzliche Spalten zusätzlich zu dem schreiben, was im Datenschema der Senke definiert ist. Um die Schemaabweichung zu ermöglichen, wählen Sie in Ihrer Senkentransformation **Allow Schema Drift** (Schemaabweichung zulassen) aus.

![Schemaabweichung (Senke)](media/data-flow/schemadrift002.png "Schemaabweichung (Senke)")

Wenn die Schemaabweichung aktiviert ist, stellen Sie sicher, dass auf der Registerkarte „Mapping“ (Zuordnung) der Schieberegler **Auto-Mapping** (Automatische Zuordnung) aktiviert ist. Wenn dieser Schieberegler eingeschaltet ist, werden alle eingehenden Spalten in Ihr Ziel geschrieben. Andernfalls müssen Sie die regelbasierte Zuordnung verwenden, um abweichende Spalten zu schreiben.

![Automatische Senkenzuordnung](media/data-flow/automap.png "Automatische Senkenzuordnung")

## <a name="transforming-drifted-columns"></a>Transformieren abweichender Spalten

Wenn Ihr Datenfluss abweichende Spalten aufweist, können Sie in Ihren Transformationen mit den folgenden Methoden auf diese zugreifen:

* Verwenden der Ausdrücke `byPosition` und `byName`, um auf eine Spalte explizit über Name oder Positionsnummer zu verweisen
* Hinzufügen eines Spaltenmusters in einer Transformation des Typs „Abgeleitete Spalte“ oder „Aggregat“, um es mit einer beliebigen Kombination aus Name, Datenstrom, Position, Ursprung oder Typ abzugleichen
* Hinzufügen einer regelbasierten Zuordnung in einer Auswahl- oder Senkentransformation, um abweichende Spalten über ein Muster mit Spaltenaliasen abzugleichen

Weitere Informationen zum Implementieren von Spaltenmustern finden Sie unter [Spaltenmuster im Zuordnungsdatenfluss](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Schnellaktion zum Zuordnen abweichender Spalten

Um explizit auf abweichende Spalten zu verweisen, können Sie in kürzester Zeit Zuordnungen für diese Spalten über eine Schnellaktion zur Datenvorschau erzeugen. Nach Aktivierung des [Debugmodus](concepts-data-flow-debug-mode.md) wechseln Sie zur Registerkarte „Datenvorschau“ und klicken auf **Aktualisieren** , um eine Datenvorschau abzurufen. Wenn Data Factory feststellt, dass abweichende Spalten vorhanden sind, können Sie auf **Map Drifted** (Abweichende zuordnen) klicken und eine abgeleitete Spalte erzeugen, mit der Sie in nachgelagerten Schemaansichten auf alle abweichenden Spalten verweisen können.

![Screenshot: Registerkarte „Datenvorschau“ mit hervorgehobener Option „Map Drifted“ (Abweichende zuordnen)](media/data-flow/mapdrifted1.png "Map drifted (Abweichende zuordnen)")

In der generierten Transformation des Typs „Abgeleitete Spalten“ werden alle abweichenden Spalten dem erkannten Namen und Datentyp zugeordnet. In der obigen Datenvorschau wird die Spalte „movieId“ als ganze Zahl erkannt. Nach Klicken auf **Map Drifted** (Abweichende zuordnen) wird movieId in der abgeleiteten Spalte als `toInteger(byName('movieId'))` definiert und bei nachgelagerten Transformationen in Schemaansichten berücksichtigt.

![Screenshot: Registerkarte mit den Einstellungen der abgeleiteten Spalte](media/data-flow/mapdrifted2.png "Map drifted (Abweichende zuordnen)")

## <a name="next-steps"></a>Nächste Schritte
In der [Datenfluss-Ausdruckssprache](data-flow-expression-functions.md) finden Sie zusätzliche Funktionen für Spaltenmuster und Schemaabweichung einschließlich byName und byPosition.
