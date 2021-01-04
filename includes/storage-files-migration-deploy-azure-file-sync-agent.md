---
title: Bereitstellen des Azure-Dateisynchronisierungs-Agents
description: Bereitstellen des Agents für die Azure-Dateisynchronisierung Ein gemeinsamer Textblock der Migrationsdokumentation.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: f038392f03b94aa2c2450531c9da4a11d9900295
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043172"
---
In diesem Abschnitt installieren Sie den Azure-Dateisynchronisierungs-Agent auf Ihrer Windows Server-Instanz.

Im [Bereitstellungshandbuch](../articles/storage/files/storage-sync-files-deployment-guide.md) erfahren Sie, dass Sie die **verstärkte Sicherheitskonfiguration für Internet Explorer** deaktivieren müssen. Diese Sicherheitsmaßnahme trifft auf die Azure-Dateisynchronisierung nicht zu. Durch das Deaktivieren können Sie sich problemlos bei Azure authentifizieren.

Öffnen Sie PowerShell, und installieren Sie die erforderlichen PowerShell-Module mithilfe der folgenden Befehle. Stellen Sie sicher, dass Sie das vollständige Modul und den NuGet-Anbieter installieren, wenn Sie dazu aufgefordert werden.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Wenn auf Ihrem Server Probleme mit der Internetverbindung auftreten, sollten Sie diese Probleme jetzt beheben. Bei der Azure-Dateisynchronisierung wird eine beliebige verfügbare Internetverbindung verwendet. Konfigurationen, in denen für die Internetverbindung ein Proxyserver erforderlich ist, werden ebenfalls unterstützt. Sie können entweder jetzt einen computerweiten Proxy konfigurieren oder während der Agentinstallation einen Proxy angeben, der ausschließlich für die Azure-Dateisynchronisierung verwendet wird.

Wenn die Konfiguration eines Proxy bedeutet, dass Sie Ihre Firewalls für diesen Server öffnen müssen, könnte dieser Ansatz für Sie geeignet sein. Am Ende der Serverinstallation und nach abgeschlossener Serverregistrierung werden in einem Bericht zur Netzwerkkonnektivität die genauen Endpunkt-URLs in Azure angezeigt, mit denen die Azure-Dateisynchronisierung für Ihre ausgewählte Region kommunizieren muss. Der Bericht enthält zudem Informationen dazu, weshalb diese Kommunikation erforderlich ist. Sie können den Bericht darüber hinaus verwenden, um die Firewalls auf diesem Server zu sperren und nur bestimmte URLs zuzulassen.

Sie können auch eine konservativere Vorgehensweise wählen, bei der Sie die Firewalls nicht öffnen, sondern die Kommunikation der Server stattdessen auf DNS-Namespaces einer höheren Ebene beschränken. Weitere Informationen finden Sie unter [Proxy- und Firewalleinstellungen der Azure-Dateisynchronisierung](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Halten Sie sich an ihre eigenen bewährten Methoden für Netzwerke.

Am Ende des Assistenten für die Serverinstallation wird ein Assistent zur Serverregistrierung geöffnet. Registrieren Sie den Server bei der zuvor genannten Azure-Speichersynchronisierungsdienst-Ressource.

Im Bereitstellungshandbuch sind diese Schritte näher beschrieben. Dabei wird auch auf die PowerShell-Module eingegangen, die zuerst installiert werden sollten: [Installation des Azure-Dateisynchronisierungs-Agents](../articles/storage/files/storage-sync-files-deployment-guide.md).

Verwenden Sie den neuesten Agent. Sie können ihn aus dem Microsoft Download Center herunterladen: [Azure-Dateisynchronisierungs-Agent](https://aka.ms/AFS/agent "Download des Azure-Dateisynchronisierungs-Agents").

Nach der Installation und Registrierung des Servers können Sie überprüfen, ob dieser Schritt erfolgreich ausgeführt wurde. Wechseln Sie im Azure-Portal zur Storage Sync Service-Ressource. Wechseln Sie im linken Menü zu **Registrierte Server**. Ihr Server sollte in dieser Liste enthalten sein.
