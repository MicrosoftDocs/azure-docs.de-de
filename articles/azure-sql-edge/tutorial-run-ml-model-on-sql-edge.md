---
title: Bereitstellen eines ML-Modells in Azure SQL Edge mithilfe von ONNX
description: Im dritten Teil dieses dreiteiligen Azure SQL Edge-Tutorials zum Vorhersagen von Eisenerzverunreinigungen führen Sie die ONNX Machine Learning-Modelle in SQL Edge aus.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422194"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Bereitstellen eines ML-Modells in Azure SQL Edge mithilfe von ONNX 

Im dritten Teil dieses dreiteiligen Tutorials zum Vorhersagen von Eisenerzverunreinigungen in Azure SQL Edge führen Sie die folgenden Schritte aus:

1. Sie verwenden Azure Data Studio, um eine Verbindung mit SQL-Datenbank in der Azure SQL Edge-Instanz herzustellen.
2. Sie sagen Eisenerzverunreinigungen mit ONNX in Azure SQL Edge vorher.

## <a name="key-components"></a>Wichtigste Komponenten

1. Die Lösung verwendet einen Standardwert von 500 Millisekunden zwischen jeder an den Edge-Hub gesendeten Nachricht. Dieser Wert kann in der Datei **Program.cs** geändert werden. 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. Von der Lösung wurde eine Meldung mit den folgenden Attributen generiert. Sie können die Attribute gemäß den Anforderungen hinzufügen oder entfernen. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Stellen Sie eine Verbindung mit SQL-Datenbank in der Azure SQL Edge-Instanz her, um das ML-Modell zu trainieren, bereitzustellen und zu testen.

1. Öffnen Sie Azure Data Studio.

2. Starten Sie auf der Registerkarte **Willkommen** eine neue Verbindung mit den folgenden Details:

   |_Feld_|_Wert_|
   |-------|-------|
   |Verbindungstyp| Microsoft SQL Server|
   |Server|Öffentliche IP-Adresse in der VM, die für diese Demo erstellt wurde|
   |Username|sa|
   |Kennwort|Das starke Kennwort, das bei der Erstellung der Azure SQL Edge-Instanz verwendet wurde|
   |Datenbank|Standard|
   |Servergruppe|Standard|
   |Name (optional)|Geben Sie einen optionalen Namen an.|

3. Klicken Sie auf das Menü **Verbinden**

4. Öffnen Sie im Abschnitt **Datei** in dem Ordner, in dem Sie die Projektdateien auf Ihrem Computer geklont haben, die Datei **/DeploymentScripts/MiningProcess_ONNX.jpynb**.

5. Legen Sie den Kernel auf „Python 3“ fest.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von ONNX-Modellen in Azure SQL Edge finden Sie unter [Machine Learning und KI mit ONNX in SQL Edge](onnx-overview.md).