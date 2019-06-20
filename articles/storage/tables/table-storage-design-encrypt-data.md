---
title: Verschlüsseln von Azure-Speichertabellendaten | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Verschlüsseln der Tabellendaten im Azure-Speicher.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60326002"
---
# <a name="encrypt-table-data"></a>Verschlüsseln von Tabellendaten
Die .NET-Clientbibliothek für Azure Storage unterstützt die Verschlüsselung der Eigenschaften von Zeichenfolgenentitäten für Einfüge- und Ersetzungsvorgänge. Die verschlüsselten Zeichenfolgen werden als binäre Eigenschaften für den Dienst gespeichert, und sie werden nach der Entschlüsselung wieder in Zeichenfolgen konvertiert.    

Für Tabellen müssen die Benutzer zusätzlich zur Verschlüsselungsrichtlinie die Eigenschaften angeben, die verschlüsselt werden sollen. Dies kann erfolgen, indem ein [EncryptProperty]-Attribut (für POCO-Entitäten, die von "TableEntity" abgeleitet werden) oder ein Verschlüsselungsresolver in den Anforderungsoptionen angegeben werden. Ein Verschlüsselungsresolver ist ein Delegat, der einen Partitionsschlüssel, einen Zeilenschlüssel und einen Eigenschaftennamen annimmt und einen booleschen Wert zurückgibt, der angibt, ob die Eigenschaft verschlüsselt werden soll. Bei der Verschlüsselung verwendet die Clientbibliothek diese Informationen, um zu entscheiden, ob eine Eigenschaft beim Schreiben in das Netzwerk verschlüsselt werden soll. Der Delegat bietet zudem die Möglichkeit einer Logik bezüglich der Verschlüsselung der Eigenschaften. (Beispiel: Wenn X, dann wird Eigenschaft A verschlüsselt, andernfalls werden die Eigenschaften A und B verschlüsselt.) Es ist nicht notwendig, diese Informationen beim Lesen oder Abfragen von Entitäten bereitzustellen.

## <a name="merge-support"></a>Unterstützung des Zusammenführens

Das Zusammenführen wird derzeit nicht unterstützt. Da eine Teilmenge der Eigenschaften möglicherweise bereits mit einem anderen Schlüssel verschlüsselt wurde, führen das einfache Zusammenführen der neuen Eigenschaften und das Aktualisieren der Metadaten zu Datenverlusten. Das Zusammenführen erfordert entweder zusätzliche Dienstaufrufe, um die bereits vorhandene Entität aus dem Dienst zu lesen, oder die Verwendung eines neuen Schlüssels pro Eigenschaft. Beide Verfahren sind aus Leistungsgründen nicht geeignet.     

Informationen zum Verschlüsseln von Tabellendaten finden Sie unter [Clientseitige Verschlüsselung und der Azure-Schlüsseltresor für Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Nächste Schritte

- [Entwurfsmuster für die Tabelle](table-storage-design-patterns.md)
- [Modellieren von Beziehungen](table-storage-design-modeling.md)
- [Modellieren von Beziehungen](table-storage-design-modeling.md)
- [Entwurf für die Datenänderung](table-storage-design-for-modification.md)
