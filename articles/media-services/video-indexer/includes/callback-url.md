---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994491"
---
Eine URL, die zum Benachrichtigen des Kunden über die folgenden Ereignisse (mithilfe einer POST-Anforderung) verwendet wird:

- Änderung des Indizierungszustands: 
    - Eigenschaften:    
    
        |Name|BESCHREIBUNG|
        |---|---|
        |id|Video-ID|
        |state|Videozustand|  
    - Beispiel: https:\//test.com/notifyme?projectName=MeinProjekt&id=1234abcd&state=Processed
- Im Video identifizierte Person:
  - Eigenschaften
    
      |Name|BESCHREIBUNG|
      |---|---|
      |id| Video-ID|
      |faceId|Die Gesichts-ID, die im Videoindex angezeigt wird|
      |knownPersonId|Die Personen-ID, die innerhalb eines Gesichtsmodells eindeutig ist|
      |personName|Der Name der Person|
        
    - Beispiel: https:\//test.com/notifyme?projectName=MeinProjekt&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 
