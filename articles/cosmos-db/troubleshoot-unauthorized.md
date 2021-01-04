---
title: Troubleshooting von Ausnahmen bei nicht autorisierten Vorgängen in Azure Cosmos DB
description: Erfahren Sie mehr über das Diagnostizieren und Beheben von Ausnahmen bei nicht autorisierten Vorgängen.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: aa9bf1fd706ccf6064893f1141be5e5b2f185ff3
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411166"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Diagnose und Troubleshooting für Ausnahmen bei nicht autorisierten Vorgängen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401: The MAC signature found in the HTTP request is not the same as the computed signature. (Die MAC-Signatur in der HTTP-Anforderung entspricht nicht der berechneten Signatur.)
Die Fehlermeldung 401, dass die MAC-Signatur in der HTTP-Anforderung nicht der berechneten Signatur entspricht, kann in folgenden Szenarien auftreten.

Bei älteren SDKs kann die Ausnahme als ungültige JSON-Ausnahme anstelle der richtigen 401-Ausnahme über nicht autorisierte Vorgänge angezeigt werden. Bei neueren SDKs ist die Anzeige in diesem Szenario korrekt, und es wird eine gültige Fehlermeldung angezeigt.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
Die folgende Liste enthält bekannte Gründe und Lösungen für Ausnahmen aufgrund nicht autorisierter Vorgänge.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>Das häufigste Szenario ist, dass der Schlüssel nicht ordnungsgemäß rotiert wurde.
Die Fehlermeldung 401 zur MAC-Signatur wird kurz nach der Schlüsselrotation angezeigt und schließlich ohne Änderungen unterbrochen. 

#### <a name="solution"></a>Lösung:
Der Schlüssel wurde rotiert und entsprach nicht den [bewährten Methoden](secure-access-to-data.md#key-rotation). Abhängig von der Größe des Azure Cosmos DB-Kontos kann die Schlüsselrotation für das Azure Cosmos DB-Konto von nur wenigen Sekunden bis möglicherweise Tage dauern.

### <a name="the-key-is-misconfigured"></a>Der Schlüssel ist falsch konfiguriert. 
Die Fehlermeldung 401 zur MAC-Signatur ist konsistent und tritt bei allen Aufrufen auf, für die dieser Schlüssel verwendet wird.

#### <a name="solution"></a>Lösung:
Der Schlüssel ist für die Anwendung falsch konfiguriert, und für das Konto wird der falsche Schlüssel verwendet, oder es wurde nicht der gesamte Schlüssel kopiert.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>Die Anwendung verwendet die schreibgeschützten Schlüssel für Schreibvorgänge.
Die Fehlermeldung 401 zur MAC-Signatur tritt nur bei Schreibvorgängen wie dem Erstellen oder Ersetzen auf. Leseanforderungen sind jedoch erfolgreich.

#### <a name="solution"></a>Lösung:
Ändern Sie die Anwendung so, dass ein Schlüssel für Lese- und Schreibvorgänge verwendet wird, der es ermöglicht, dass Vorgänge erfolgreich abgeschlossen werden.

### <a name="race-condition-with-create-container"></a>Es gibt eine Racebedingung beim Erstellen eines Containers.
Die Fehlermeldung 401 zur MAC-Signatur tritt oft kurz nach der Erstellung eines Containers auf. Der Fehler tritt aber nur so lange auf, bis die Containererstellung abgeschlossen ist.

#### <a name="solution"></a>Lösung:
Es liegt eine Racebedingung bei der Containererstellung vor. Eine Anwendungsinstanz versucht, auf den Container zuzugreifen, bevor die Containererstellung abgeschlossen ist. Das häufigste Szenario für diese Racebedingung ist, dass der Container gelöscht und mit demselben Namen neu erstellt wird, während die Anwendung ausgeführt wird. Das SDK versucht, den neuen Container zu verwenden, aber die Containererstellung wird noch ausgeführt, sodass die Schlüssel noch nicht verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte
* [Diagnostizieren und Behandeln](troubleshoot-dot-net-sdk.md) von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
* Weitere Informationen zu Leistungsrichtlinien für [.NET Version 3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET Version 2](performance-tips.md)
* [Diagnostizieren und Behandeln](troubleshoot-java-sdk-v4-sql.md) von Problemen bei Verwendung des Java v4 SDK für Azure Cosmos DB.
* Weitere Informationen zu Leistungsrichtlinien für das [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).