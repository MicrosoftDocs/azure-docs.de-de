---
title: Includedatei
description: Includedatei
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66137116"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Was geschieht während der Bereitstellung mit meiner App?

Alle offiziell unterstützten Bereitstellungsmethoden haben eines gemeinsam: Sie nehmen Änderungen an den Dateien im Ordner `/home/site/wwwroot` Ihrer App vor. Dies sind dieselben Dateien, die in der Produktionsumgebung ausgeführt werden. Aus diesem Grund kann die Bereitstellung aufgrund gesperrter Dateien zu einem Fehler führen, oder die App zeigt während der Bereitstellung in der Produktion ein unerwartetes Verhalten, da nicht alle Dateien gleichzeitig aktualisiert werden. Es gibt mehrere Möglichkeiten, wie Sie diese Probleme umgehen können:

- Beenden Sie die App, oder aktivieren Sie den Offlinemodus für die App während der Bereitstellung. Weitere Informationen finden Sie unter [Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment) (Umgang mit gesperrten Dateien während der Bereitstellung).
- Stellen Sie einen [Stagingslot](../articles/app-service/deploy-staging-slots.md) bereit, bei dem [Automatisch tauschen](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) aktiviert ist. 
- Verwenden Sie stattdessen [Aus Paket ausführen](https://github.com/Azure/app-service-announcements/issues/84).
