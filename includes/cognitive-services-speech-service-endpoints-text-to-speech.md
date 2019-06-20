---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178381"
---
### <a name="standard-and-neural-voices"></a>Standard- und neuronale Stimmen

Verwenden Sie diese Tabelle, um die Verfügbarkeit von Standardstimmen und neuronalen Stimmen nach Region/Endpunkt zu ermitteln:

| Region | Endpunkt | Standardstimmen | Neuronale Stimmen |
|--------|----------|-----------------|---------------|
| Australien (Osten) | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Kanada, Mitte | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| USA (Mitte) | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nein |
| Asien, Osten | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nein |
| USA (Ost) | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| USA (Ost) 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nein |
| Frankreich, Mitte | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nein |
| Indien, Mitte | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Japan, Osten | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nein |
| Korea, Mitte | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nein |
| USA Nord Mitte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nein |
| Nordeuropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nein |
| USA Süd Mitte | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Asien, Südosten | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| UK, Süden | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Europa, Westen | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| USA (Westen) | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nein |
| USA, Westen 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |

### <a name="custom-voices"></a>Benutzerdefinierte Stimmen

Wenn Sie einen benutzerdefinierten Voicefont erstellt haben, verwenden Sie den von Ihnen erstellten Endpunkt. Sie können auch die unten aufgeführten Endpunkte verwenden und die `{deploymentId}` durch die Bereitstellungs-ID für Ihr Stimmmodell ersetzen.

| Region | Endpunkt |
|--------|----------|
| Australien (Osten) | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kanada, Mitte | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA (Mitte) | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asien, Osten | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA (Ost) | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA (Ost) 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Frankreich, Mitte | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indien, Mitte | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japan, Osten | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Korea, Mitte | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Nord Mitte | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Nordeuropa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA (Mitte/Süden) | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asien, Südosten | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| UK, Süden | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa, Westen | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA (Westen) | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, Westen 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
