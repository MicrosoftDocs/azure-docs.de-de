---
title: Erstellen eines Azure-Dienstprinzipals
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95096780"
---
## <a name="create-an-azure-service-principal"></a>Erstellen eines Azure-Dienstprinzipals

Damit Ihre Anwendung mit Ihrem Azure-Konto interagieren kann, benötigen Sie einen Azure-Dienstprinzipal zum Verwalten der Berechtigungen. Folgen Sie den Anweisungen unter [Erstellen eines Azure-Dienstprinzipals](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Wenn Sie einen Dienstprinzipal erstellen, sehen Sie, dass dieser einen Geheimniswert, eine ID und eine Anwendungs-ID aufweist. Speichern Sie die Anwendungs-ID und das Geheimnis für spätere Schritte an einem temporären Speicherort.