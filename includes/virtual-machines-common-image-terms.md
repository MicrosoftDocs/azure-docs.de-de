---
title: include file
description: include file
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178084"
---
## <a name="terminology"></a>Begriff

Ein Marketplace-Image in Azure hat die folgenden Attribute:

* **Herausgeber**: Die Organisation, die das Image erstellt hat. Beispiele: Canonical, MicrosoftWindowsServer
* **Angebot**: Name einer Gruppe verwandter Images, die von einem Herausgeber erstellt wurden. Beispiele: UbuntuServer, WindowsServer
* **SKU**: Eine Instanz eines Angebots, z.B. eine Hauptversion einer Distribution. Beispiele: 18.04-LTS, 2019-Datacenter
* **Version**: Die Versionsnummer einer Image-SKU. 

Um ein Marketplace-Image zu identifizieren, wenn Sie einen virtuellen Computer programmgesteuert bereitstellen, geben Sie diese Werte einzeln als Parameter an. Einige Tools akzeptieren einen Image-*URN*. Der URN kombiniert diese Werte und trennt sie durch einen Doppelpunkt (:): *Herausgeber*:*Angebot*:*SKU*:*Version*. In einem URN können Sie die Versionsnummer durch „latest“ ersetzen, um die neueste Version des Images auszuwählen. 

Wenn der Imageherausgeber zusätzliche Lizenz- und Kaufbedingungen angibt, müssen Sie diese Bedingungen akzeptieren und die programmgesteuerte Bereitstellung aktivieren. Sie müssen auch die Parameter des *Kaufplans* angeben, wenn Sie einen virtuellen Computer programmgesteuert bereitstellen. Siehe [Bereitstellen eines Images mit Marketplace-Nutzungsbedingungen](#deploy-an-image-with-marketplace-terms).
