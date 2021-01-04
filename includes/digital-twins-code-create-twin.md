---
author: baanders
description: Includedatei für Azure Digital Twins – Code zum Erstellen eines Zwillings
ms.service: digital-twins
ms.topic: include
ms.date: 10/21/2020
ms.author: baanders
ms.openlocfilehash: 050496d028632c416e8694a0d6022747c00b6796
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356387"
---
```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
//Create the twin
client.CreateOrReplaceDigitalTwinAsync("myRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```