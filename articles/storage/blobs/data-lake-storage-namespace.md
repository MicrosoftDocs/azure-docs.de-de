---
title: Hierarchischer Namespace für Azure Data Lake Storage Gen2
description: Beschreibt das Konzept des hierarchischen Namespace für Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: e0d888db5f8de137783a3f9282ca7f85d8a30fc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939455"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Hierarchischer Namespace für Azure Data Lake Storage Gen2

Ein Schlüsselmechanismus, der es Azure Data Lake Storage Gen2 ermöglicht, Dateisystemleistung im Umfang und zu Preisen eines Objektspeichers bereitzustellen, ist das Hinzufügen eines **hierarchischen Namespace**. Dadurch kann die Sammlung von Objekten/Dateien innerhalb eines Kontos in einer Hierarchie von Verzeichnissen und geschachtelten Unterverzeichnissen auf gleiche Weise wie beim Dateisystem auf Ihrem Computer organisiert werden. Bei aktiviertem hierarchischem Namespace kann ein Speicherkonto die Skalierbarkeit und Kosteneffizienz eines Objektspeichers mit einer Dateisystemsemantik bereitstellen, die Analyse-Engines und -Frameworks vertraut ist.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Vorteile des hierarchischen Namespace

Dateisysteme, die einen hierarchischen Namespace über Blob-Daten implementieren, bieten folgende Vorteile:

- **Atomarische Verzeichnisbearbeitung:** Objektspeicher verarbeiten eine Verzeichnishierarchie, indem sie eine Konvention zur Einbettung von Schrägstrichen (/) in den Objektnamen anwenden, um Pfadsegmente zu bezeichnen. Diese Konvention funktioniert zwar beim Organisieren von Objekten, stellt aber keine Hilfe bei Aktionen wie dem Verschieben, Umbenennen oder Löschen von Verzeichnissen dar. Ohne wirkliche Verzeichnisse müssen Anwendungen möglicherweise Millionen einzelner Blobs zur Ausführung von Aufgaben auf Verzeichnisebene verarbeiten. Der hierarchische Namespace hingegen verarbeitet diese Aufgaben durch Aktualisieren eines einzelnen Eintrags (des übergeordneten Verzeichnisses).

    Diese wesentliche Verbesserung ist für viele Big Data-Analyseframeworks von besonderer Bedeutung. Tools wie Hive, Spark usw. schreiben häufig die Ausgabe an temporäre Speicherorte und benennen dann bei Abschluss des Auftrags den Speicherort um. Ohne hierarchischen Namespace kann dieses Umbenennen häufig länger dauern als der eigentliche Analyseprozess. Eine niedrigere Auftragslatenz bedeutet auch niedrigere Gesamtkosten für Analyseworkloads.

- **Vertrauter Schnittstellenstil:** Dateisysteme werden sowohl von Entwicklern als auch Benutzern gut verstanden. Es ist nicht erforderlich, beim Wechsel zur Cloud ein neues Speicherparadigma zu erlernen, da die von Data Lake Storage Gen2 angezeigte Dateisystemschnittstelle dem sowohl von kleinen als auch großen Computern verwendeten Paradigma entspricht.

Einer der Gründe, warum Objektspeicher bisher keine hierarchischen Namespaces unterstützt haben, ist, dass die Skalierbarkeit durch hierarchische Namespaces begrenzt wird. Der hierarchische Namespace für Data Lake Storage Gen2 kann jedoch linear skaliert werden und beeinträchtigt weder Datenkapazität noch Leistung.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Aktivierungsempfehlung für den hierarchischen Namespace

Es empfiehlt sich, den hierarchischen Namespace für Speicherworkloads zu aktivieren, die für Dateisysteme entwickelt wurden, in denen Verzeichnisse bearbeitet werden. Dazu gehören alle Workloads, die primär der Analyseverarbeitung dienen. Datasets, die ein hohes Maß an Organisation erfordern, profitieren ebenfalls vom Aktivieren des hierarchischen Namespace.

Die Gründe für das Aktivieren des hierarchischen Namespace werden durch eine Gesamtkostenanalyse ermittelt. Im Allgemeinen werden durch Verbesserungen der Workloadlatenz aufgrund beschleunigter Speicherung Computeressourcen über einen kürzeren Zeitraum benötigt. Die Latenz für viele Workloads kann aufgrund der atomarischen Verzeichnisbearbeitung verbessert werden, die der hierarchische Namespace ermöglicht. Bei vielen Workloads stellt die Computeressource mehr als 85 % der Gesamtkosten dar, sodass auch nur eine geringfügige Verringerung der Workloadlatenz zu wesentlichen Einsparungen bei den Gesamtkosten führen kann. Selbst in Fällen, in denen durch das Aktivieren des hierarchischen Namespace die Speicherkosten steigen, werden dennoch die Gesamtkosten aufgrund der niedrigeren Computekosten reduziert.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Deaktivierungsempfehlung für den hierarchischen Namespace

Für einige Workloads des Objektspeichers bietet das Aktivieren des hierarchischen Namespace möglicherweise keine Vorteile. Beispiele dafür sind Backups, Imagespeicher und andere Anwendungen, bei denen die Objektorganisation getrennt von den eigentlichen Objekten gespeichert wird (z.B. in einer separaten Datenbank).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Speicherkontos](./data-lake-storage-quickstart-create-account.md)
