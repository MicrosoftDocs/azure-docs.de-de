---
title: Azure Functions-Ausgabe für Azure Stream Analytics
description: In diesem Artikel wird Azure Functions als Ausgabe für Azure Stream Analytics beschrieben.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ffaf66cbb5d9fa0b294f5749a1923684f16c2979
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488088"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Functions-Ausgabe für Azure Stream Analytics

Azure Functions ist ein serverloser Computedienst, mit dem Sie Code bedarfsgesteuert ausführen können, ohne eine explizite Infrastruktur bereitstellen oder verwalten zu müssen. Mit diesem Dienst können Sie Codes implementieren, die durch in Azure- oder Partnerdiensten auftretende Ereignisse ausgelöst werden. Aufgrund der Möglichkeit, auf Trigger zu antworten, ist Azure Functions die ideale Ausgabe für Azure Stream Analytics. Mithilfe dieses Ausgabeadapters können Benutzer eine Verbindung zwischen Stream Analytics und Azure Functions herstellen und als Reaktion auf verschiedenste Ereignisse ein Skript oder einen Codeausschnitt ausführen.

Die Azure Functions-Ausgabe aus Stream Analytics ist nicht in den Regionen Azure China 21Vianet und Azure Deutschland (T-Systems International) verfügbar. Eine Verbindung mit Azure Functions innerhalb eines virtuellen Netzwerks (VNet) aus einem Stream Analytics-Auftrag, der in einem mehrinstanzenfähigen Cluster ausgeführt wird, wird ebenfalls nicht unterstützt.

Azure Stream Analytics ruft Azure Functions über HTTP-Trigger auf. Der Azure Functions-Ausgabeadapter wird mit folgenden konfigurierbaren Eigenschaften zur Verfügung gestellt:

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Funktionen-App |Der Name der Azure Functions-App. |
| Funktion |Der Name der Funktion in der Azure Functions-App. |
| Schlüssel |Wenn Sie eine Azure-Funktion aus einem anderen Abonnement verwenden möchten, können Sie dazu den Schlüssel für den Zugriff auf Ihre Funktion angeben. |
| Max Batch Size |Eine Eigenschaft, mit der Sie die maximale Größe für jeden Ausgabebatch festlegen können, der an Ihre Azure-Funktion gesendet wird. Die Eingabeeinheit ist Bytes. Standardmäßig ist dieser Wert auf 262,144 Bytes (256 KB) festgelegt. |
| Max Batch Count  |Eine Eigenschaft, mit der Sie die maximale Anzahl von Ereignissen in jedem Batch angeben können, die an Azure Functions gesendet werden. Der Standardwert ist 100. |

Azure Stream Analytics erwartet den HTTP-Status 200 von der Functions-App für Batches, die erfolgreich verarbeitet wurden.

Die Größe der an Azure Functions gesendeten Batches wird verringert, wenn in Azure Stream Analytics Ausnahme 413 (HTTP-Anforderungseinheit zu groß) durch Azure Functions auftritt. Verwenden Sie in Ihrem Azure-Funktionscode diese Ausnahme, um sicherzustellen, dass Azure Stream Analytics keine übermäßig großen Batches sendet. Stellen Sie außerdem sicher, dass die Werte für die maximal zulässige Batchanzahl und -größe in der Funktion mit den Werten übereinstimmen, die im Stream Analytics-Portal eingegeben wurden.

> [!NOTE]
> Während der Testverbindung sendet Stream Analytics einen leeren Batch an Azure Functions, um zu testen, ob die Verbindung zwischen den beiden funktioniert. Stellen Sie sicher, dass Ihre Functions-App Anforderungen für leere Batches verarbeitet, um sicherzustellen, dass die Testverbindung erfolgreich ist.

Zudem wird in Fällen, in denen kein Ereignis in einem Zeitfenster auftritt, keine Ausgabe generiert. Als Ergebnis wird die **computeResult**-Funktion nicht aufgerufen. Dieses Verhalten entspricht den integrierten Aggregatfunktionen im Fenstermodus.

## <a name="partitioning"></a>Partitionierung

Der Partitionsschlüssel basiert auf der PARTITION BY-Klausel in der Abfrage. Die Anzahl der Ausgabeschreiber folgt der Eingabepartitionierung für [vollständig parallelisierte Abfragen](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Ausgabebatchgröße

Die Standardbatchgröße beträgt 262.144 Bytes (256 KB). Die Standardereignisanzahl pro Batch beträgt 100. Die Batchgröße ist konfigurierbar und kann in den Ausgabeoptionen von Stream Analytics erhöht oder verringert werden.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mit der Azure CLI](quick-create-azure-cli.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe einer ARM-Vorlage](quick-create-azure-resource-manager.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mit Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags in Visual Studio Code](quick-create-visual-studio-code.md)