---
title: include file
description: include file
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016364"
---
## <a name="overview-of-ssh-and-keys"></a>Übersicht über SSH und Schlüssel

[SSH](https://www.ssh.com/ssh/) ist ein Protokoll für verschlüsselte Verbindungen, das die sichere Anmeldung über ungesicherte Verbindungen ermöglicht. SSH ist das Standardverbindungsprotokoll für in Azure gehostete virtuelle Linux-Computer. SSH stellt bereits eine verschlüsselte Verbindung bereit. Bei Verwendung von Kennwörtern für SSH-Verbindungen ist die VM aber anfällig für Brute-Force-Angriffe. Es wird empfohlen, über SSH mithilfe eines Schlüsselpaars aus einem öffentlichen und einem privaten Schlüssel (auch *SSH-Schlüssel* genannt) eine Verbindung mit einer VM herzustellen. 

- Der *öffentliche Schlüssel* wird auf Ihrer Linux-VM platziert.

- Der *private Schlüssel* bleibt auf dem lokalen System. Bewahren Sie diesen privaten Schlüssel sicher auf. Geben Sie ihn nicht weiter.

Wenn Sie einen SSH-Client verwenden, um eine Verbindung mit Ihrer Linux-VM (die den öffentlichen Schlüssel besitzt) herzustellen, testet die Remote-VM den Client, um sicherzustellen, dass er über den richtigen privaten Schlüssel verfügt. Wenn der Client über den privaten Schlüssel verfügt, erhält er Zugriff auf den virtuellen Computer. 

Abhängig von den Sicherheitsrichtlinien in Ihrer Organisation können Sie ein einzelnes Paar aus einem öffentlichen und einem privaten Schlüssel für den Zugriff auf mehrere Azure-VMs und -Dienste verwenden. Sie benötigen nicht für jeden virtuellen Computer oder Dienst, auf den Sie zugreifen möchten, ein separates Schlüsselpaar. 

Sie können Ihren öffentlichen Schlüssel beliebig weitergeben, aber auf Ihren privaten Schlüssel können nur Sie (bzw. Ihre lokale Sicherheitsinfrastruktur) zugreifen.