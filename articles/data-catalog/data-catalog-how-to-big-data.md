---
title: Katalogisieren von Big Data in Azure Data Catalog
description: Anleitungsartikel, der die Muster zur Verwendung von Azure Data Catalog mit Big Data-Datenquellen hervorhebt, einschließlich Azure Blob Storage, Azure Data Lake und Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: e79e5e16b300fd02b9c9124f9677747834f22813
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009431"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Katalogisieren von Big Data in Azure Data Catalog

## <a name="introduction"></a>Einführung

**Microsoft Azure Data Catalog** ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Der Dienst unterstützt Benutzer beim Ermitteln, Nachvollziehen und Verwenden von Datenquellen und Unternehmen dabei, mehr Nutzen aus ihren vorhandenen Datenquellen, wie z.B. Big Data, zu ziehen.

**Azure Data Catalog** unterstützt die Registrierung von Azure Storage-Blobs und -Verzeichnissen sowie Hadoop HDFS-Dateien und -Verzeichnisse. Die teilweise strukturierte Art dieser Datenquellen bietet enorme Flexibilität. Doch um den größten Nutzen aus ihrer Registrierung bei **Azure Data Catalog** zu ziehen, müssen Benutzer berücksichtigen, wie die Datenquellen organisiert sind.

## <a name="directories-as-logical-data-sets"></a>Verzeichnisse als logische Datensätze

Ein allgemeines Muster für die Organisation von großen Datenquellen ist, Verzeichnisse als logische Datensätze zu behandeln. Verzeichnisse der obersten Ebene werden verwendet, um einen Datensatz zu definieren, während Unterordner Partitionen definieren und die darin enthaltenen Dateien die Daten selbst speichern.

Ein Beispiel für dieses Muster könnte sein:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

In diesem Beispiel stellen „vehicle_maintenance_events“ und „location_tracking_events“ logische Datensätze dar. Jeder dieser Ordner enthält die Datendateien, die nach Jahr und Monat in Unterordnern organisiert sind. Jeder dieser Ordner kann potenziell hunderte oder tausende von Dateien enthalten.

In diesem Muster ist es vermutlich nicht sinnvoll, einzelne Dateien bei **Azure Data Catalog** zu registrieren. Registrieren Sie stattdessen die Verzeichnisse, die die Datensätze darstellen, die für die mit den Daten arbeitenden Benutzer von Bedeutung sind.

## <a name="reference-data-files"></a>Verweisdatendateien

Ein ergänzendes Muster besteht darin, Verweisdatensätze als einzelne Dateien zu speichern. Diese Datensätze können als der "kleine" Teil der Big Data betrachtet werden und ähneln oft Dimensionen in einem analytischen Datenmodell. Verweisdatendateien enthalten Datensätze, die zum Bereitstellen des Kontexts für den größten Teil der Datendateien verwendet werden, die an anderer Stelle im Big Data-Speicher liegen.

Ein Beispiel für dieses Muster könnte sein:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Wenn ein Analytiker oder Datenwissenschaftler mit den in größeren Verzeichnisstrukturen enthaltenen Daten arbeitet, können die Daten in diesen Verweisdateien dazu verwendet werden, detailliertere Informationen zu Entitäten bereitzustellen, die nur durch den Namen oder die ID im größeren Datensatz bezeichnet werden.

In diesem Muster ist es sinnvoll, die einzelnen Verweisdatendateien bei **Azure Data Catalog** zu registrieren. Jede Datei stellt einen Datensatz dar, und jede kann einzeln kommentiert und ermittelt werden.

## <a name="alternate-patterns"></a>Alternative Muster

Die im vorherigen Abschnitt beschriebenen Muster sind nur zwei Möglichkeiten, wie ein großer Datenspeicher organisiert werden kann. Jede Implementierung ist dabei anders. Unabhängig davon, wie Ihre Datenquellen strukturiert sind, sollten Sie sich beim Registrieren großer Datenquellen bei **Azure Data Catalog** darauf konzentrieren, die Dateien und Verzeichnisse zu registrieren, die die Datensätze darstellen, die für andere Personen in Ihrer Organisation von Nutzen sind. Das Registrieren aller Dateien und Verzeichnisse kann den Katalog überlasten, wodurch es den Benutzern erschwert wird, das zu suchen, was sie benötigen.

## <a name="summary"></a>Zusammenfassung

Das Registrieren von Datenquellen bei **Azure Data Catalog** erleichtert deren Ermittlung und Verständnis. Durch das Registrieren und Kommentieren der Big Data-Dateien und -Verzeichnisse, die logische Datensätze darstellen, können Ihre Benutzer leichter von ihnen benötigte große Datenquellen suchen und verwenden.
