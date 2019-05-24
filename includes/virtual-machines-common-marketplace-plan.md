---
title: Includedatei
description: Includedatei
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66117283"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Bereitstellen eines Images mit Marketplace-Nutzungsbedingungen

Für einige VM-Images im Azure Marketplace gelten zusätzliche Lizenz- und Kaufbedingungen, die Sie akzeptieren müssen, bevor Sie sie programmgesteuert bereitstellen können.  

Um einen virtuellen Computer über ein solches Image bereitzustellen, müssen Sie die Bedingungen des Images akzeptieren und die programmgesteuerte Bereitstellung aktivieren. Diese Aktionen müssen Sie pro Abonnement nur einmal durchführen. Anschließend müssen Sie jedes Mal, wenn Sie eine VM programmgesteuert über das Image bereitstellen, auch die Parameter des *Kaufplans* angeben.

In den folgenden Abschnitten werden diese Aktionen gezeigt:

* Herausfinden, ob für ein Marketplace-Image zusätzliche Lizenzbedingungen gelten 
* Programmgesteuertes Akzeptieren der Bedingungen
* Angeben von Parametern des Kaufplans, wenn Sie einen virtuellen Computer programmgesteuert bereitstellen

