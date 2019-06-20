---
title: include file
description: include file
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 14f2e663e3db81684a73c4ea093ed0403cbb09ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178249"
---
> [!NOTE]
> Nach 20 Minuten Inaktivität kann bei einer Web-App ein Timeout auftreten. Der Zeitgeber wird nur durch Anforderungen an die eigentliche Web-App zurückgesetzt. Das Anzeigen der Konfiguration der App im Azure-Portal oder das Senden von Anforderungen an die Website mit erweiterten Tools (`https://<app_name>.scm.azurewebsites.net`) führt nicht zum Zurücksetzen des Zeitgebers. Wenn Ihre App fortlaufende oder geplante WebJobs ausführt, aktivieren Sie **Always On**, um sicherzustellen, dass die WebJobs zuverlässig ausgeführt werden. Dieses Feature steht nur in den [Tarifen](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) „Basic“, „Standard“ und „Premium“ zur Verfügung.
