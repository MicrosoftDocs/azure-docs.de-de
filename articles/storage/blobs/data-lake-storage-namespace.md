---
title: Hierarchischer Namespace für Azure Data Lake Storage Gen2
description: Beschreibt das Konzept des hierarchischen Namespace für Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 26062d73ae4c61af77b15dd2cac0541f2a988d11
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912993"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Hierarchischer Namespace für Azure Data Lake Storage Gen2

Ein Schlüsselmechanismus, der es Azure Data Lake Storage Gen2 ermöglicht, Dateisystemleistung im Umfang und zu Preisen eines Objektspeichers bereitzustellen, ist das Hinzufügen eines **hierarchischen Namespace**. Dadurch kann die Sammlung von Objekten/Dateien innerhalb eines Kontos in einer Hierarchie von Verzeichnissen und geschachtelten Unterverzeichnissen auf gleiche Weise wie beim Dateisystem auf Ihrem Computer organisiert werden. Bei aktiviertem hierarchischem Namespace kann ein Speicherkonto die Skalierbarkeit und Kosteneffizienz eines Objektspeichers mit einer Dateisystemsemantik bereitstellen, die Analyse-Engines und -Frameworks vertraut ist.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Vorteile eines hierarchischen Namespace

Dateisysteme, die einen hierarchischen Namespace über Blob-Daten implementieren, bieten folgende Vorteile:

- **Atomarische Verzeichnisbearbeitung:** Objektspeicher nähern sich einer Verzeichnishierarchie, indem eine Konvention mit Einbettung von Schrägstrichen (/) in den Objektnamen zur Angabe von Pfadsegmenten übernommen wird. Diese Konvention funktioniert zwar beim Organisieren von Objekten, stellt aber keine Hilfe bei Aktionen wie dem Verschieben, Umbenennen oder Löschen von Verzeichnissen dar. Ohne wirkliche Verzeichnisse müssen Anwendungen möglicherweise Millionen einzelner Blobs zur Ausführung von Aufgaben auf Verzeichnisebene verarbeiten. Ein hierarchischer Namespace hingegen verarbeitet diese Aufgaben durch Aktualisieren eines einzelnen Eintrags (des übergeordneten Verzeichnisses).

    Diese wesentliche Verbesserung ist für viele Big Data-Analyseframeworks von besonderer Bedeutung. Tools wie Hive, Spark usw. schreiben häufig die Ausgabe an temporäre Speicherorte und benennen dann bei Abschluss des Auftrags den Speicherort um. Ohne hierarchischen Namespace kann dieses Umbenennen häufig länger dauern als der eigentliche Analyseprozess. Eine niedrigere Auftragslatenz bedeutet auch niedrigere Gesamtkosten für Analyseworkloads.

- **Vertrauter Schnittstellenstil:** Dateisysteme werden sowohl von Entwicklern als auch Benutzern gut verstanden. Es ist nicht erforderlich, beim Wechsel zur Cloud ein neues Speicherparadigma zu erlernen, da die von Data Lake Storage Gen2 angezeigte Dateisystemschnittstelle dem sowohl von kleinen als auch großen Computern verwendeten Paradigma entspricht.

Einer der Gründe, warum Objektspeicher bisher keine hierarchischen Namespaces unterstützt haben, ist, dass die Skalierbarkeit durch hierarchische Namespaces begrenzt wird. Der hierarchische Namespace für Data Lake Storage Gen2 kann jedoch linear skaliert werden und beeinträchtigt weder Datenkapazität noch Leistung.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Entscheiden, ob ein hierarchischer Namespace aktiviert werden soll

Nachdem Sie einen hierarchischen Namespace in Ihrem Konto aktiviert haben, können Sie ihn nicht mehr auf einen flachen Namespace zurücksetzen. Daher sollten Sie überlegen, ob es bei den Workloadtypen in Ihrem Objektspeicher sinnvoll ist, einen hierarchischen Namespace zu aktivieren.

Für einige Workloads bietet das Aktivieren des hierarchischen Namespace möglicherweise keine Vorteile. Beispiele dafür sind Backups, Imagespeicher und andere Anwendungen, bei denen die Objektorganisation getrennt von den eigentlichen Objekten gespeichert wird (z.B. in einer separaten Datenbank). 

Auch wenn die Unterstützung für Blob Storage-Features und das Azure-Dienstökosystem weiterhin zunimmt, gibt es einige Features und Azure-Dienste, die in Konten mit einem hierarchischen Namespace noch nicht unterstützt werden. Siehe [Bekannte Probleme](data-lake-storage-known-issues.md). 

Es empfiehlt sich im Allgemeinen, einen hierarchischen Namespace für Speicherworkloads zu aktivieren, die für Dateisysteme entwickelt wurden, in denen Verzeichnisse bearbeitet werden. Dazu gehören alle Workloads, die primär der Analyseverarbeitung dienen. Datasets, die ein hohes Maß an Organisation erfordern, profitieren ebenfalls vom Aktivieren eines hierarchischen Namespace.

Die Gründe für das Aktivieren eines hierarchischen Namespace werden durch eine Gesamtkostenanalyse ermittelt. Im Allgemeinen werden durch Verbesserungen der Workloadlatenz aufgrund beschleunigter Speicherung Computeressourcen über einen kürzeren Zeitraum benötigt. Die Latenz für viele Workloads kann aufgrund der atomischen Verzeichnisbearbeitung verbessert werden, die ein hierarchischer Namespace ermöglicht. Bei vielen Workloads stellt die Computeressource mehr als 85 % der Gesamtkosten dar, sodass auch nur eine geringfügige Verringerung der Workloadlatenz zu wesentlichen Einsparungen bei den Gesamtkosten führen kann. Selbst in Fällen, in denen durch das Aktivieren eines hierarchischen Namespace die Speicherkosten steigen, werden dennoch die Gesamtkosten aufgrund der niedrigeren Computekosten reduziert.

Informationen zum Analysieren der Unterschiede bei Datenspeicherpreisen, Transaktionspreisen und Reservierungspreisen für Speicherkapazitäten zwischen Konten mit einem flachen und einem hierarchischen Namespace finden Sie unter [Azure Data Lake Storage Gen2 – Preise](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Speicherkontos](../common/storage-account-create.md)