---
title: Verbinden von Funktionen mit anderen Azure-Diensten
description: In diesem Artikel erfahren Sie, wie Sie einer vorhandenen Funktion in einem Azure Functions-Projekt Bindungen mit anderen Azure-Diensten hinzufügen.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258200"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>Verbinden von Funktionen mit Azure-Diensten mithilfe von Bindungen

Wenn Sie eine Funktion erstellen, wird der sprachspezifische Triggercode aus Triggervorlagen in Ihrem Projekt hinzugefügt. Wenn Sie Ihre Funktion mithilfe von Ein- oder Ausgabebindungen mit anderen Diensten verknüpfen möchten, müssen Sie in Ihrer Funktion bestimmte Bindungsdefinitionen hinzufügen. Weitere Informationen zu Bindungen finden Sie unter [Azure Functions-Trigger und Bindungskonzepte](functions-triggers-bindings.md).

## <a name="local-development"></a>Lokale Entwicklung       

Wenn Sie Funktionen lokal entwickeln, müssen Sie den Funktionscode aktualisieren, um Bindungen hinzuzufügen. Visual Studio Code kann das Hinzufügen von Bindungen zu einer Funktion vereinfachen.  

### <a name="visual-studio-code"></a>Visual Studio Code

Wenn Sie Ihre Funktion mit Visual Studio Code entwickeln und die Funktion eine function.json-Datei verwendet, kann die Azure Functions-Erweiterung automatisch eine Bindung zu einer vorhandenen function.json-Datei hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen von Ein- und Ausgabebindungen](functions-develop-vs-code.md#add-input-and-output-bindings).   

### <a name="manually-add-bindings-based-on-examples"></a>Manuelles Hinzufügen von Bindungen anhand von Beispielen

Beim Hinzufügen einer Bindung zu einer vorhandenen Funktion müssen Sie sowohl den Funktionscode als auch die function.json-Konfigurationsdatei aktualisieren, wenn diese von Ihrer Sprache verwendet wird. Sowohl die .NET-Klassenbibliothek als auch die Java-Funktionen verwenden Attribute anstelle der function.json-Datei. Daher müssen Sie diese stattdessen aktualisieren.

In der folgenden Tabelle finden Sie Beispiele für bestimmte Bindungstypen, mithilfe derer Sie vorhandene Funktionen aktualisieren können. Wählen Sie zunächst die Registerkarte für die Sprache aus, die in Ihrem Projekt verwendet wird. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure-Portal

Wenn Sie die Funktionen im [Azure-Portal](https://portal.azure.com) entwickeln, können Sie einer bestimmten Funktion auf der Registerkarte **Integration** Ein- und Ausgabebindungen hinzufügen. Die neuen Bindungen werden je nach Sprache entweder der Datei „function.json“ oder den Methodenattributen hinzugefügt. Die folgenden Artikel enthalten Beispiele für das Hinzufügen von Bindungen zu einer vorhandenen Funktion im Portal:

+ [Queue Storage-Ausgabebindung](functions-integrate-storage-queue-output-binding.md)
+ [Hinzufügen einer Cosmos DB-Ausgabebindung](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Nächste Schritte

+ [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
