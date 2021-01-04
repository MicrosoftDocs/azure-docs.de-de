---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 30de9181fd23a29b28973d01899f0b23fca3ae89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014573"
---
## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemein verfügbar (Generally Available, GA)|
|Preise:|**Azure Defender für Containerregistrierungen** wird gemäß den Angaben in der [Preisübersicht](../articles/security-center/security-center-pricing.md) abgerechnet.|
|Unterstützte Registrierungen und Images:|Linux-Images in ACR-Registrierungen, auf die mit Shellzugriff über das öffentliche Internet zugegriffen werden kann|
|Nicht unterstützte Registrierungen und Images:|Windows-Images<br>„Private“ Registrierungen<br>Registrierungen, deren Zugriff durch eine Firewall, einen Dienstendpunkt oder private Endpunkte wie Azure Private Link beschränkt ist<br>Extrem minimalistische Images wie [Docker-Scratch](https://hub.docker.com/_/scratch/)-Images oder Images ohne Distribution, die nur eine Anwendung und deren Laufzeitabhängigkeiten ohne Paket-Manager, Shell oder Betriebssystem enthalten|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsleseberechtigter** und [Azure Container Registry: Rollen und Berechtigungen](../articles/container-registry/container-registry-roles.md)|
|Clouds:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Kommerzielle Clouds<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov – derzeit wird nur das Feature zur Überprüfung bei Pushvorgang unterstützt. Weitere Informationen finden Sie unter [Wann werden Images überprüft?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)<br>:::image type="icon" source="../articles/security-center/media/icons/no-icon.png" border="false"::: China Gov/andere Gov-Clouds|
|||