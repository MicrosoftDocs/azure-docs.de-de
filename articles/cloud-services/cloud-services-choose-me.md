---
title: Was ist Azure Cloud Services? | Microsoft-Dokumentation
description: Weitere Informationen zu Azure Cloud Services
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: ce88dcaedf32f293fc121cda2a088388c99badee
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337516"
---
# <a name="overview-of-azure-cloud-services"></a>Übersicht zu Azure Cloud Services
Azure Cloud Services sind ein Beispiel für [Platform-as-a-Service](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Diese Technologie unterstützt genau wie [Azure App Service](../app-service/overview.md) skalierbare und zuverlässige Anwendungen mit geringen Betriebskosten. Azure Cloud Services werden wie App Service auf virtuellen Computern (VMs) gehostet. Sie haben jedoch mehr Kontrolle über die virtuellen Computer. Sie können Ihre eigene Software auf virtuellen Computern installieren, die Azure Cloud Services verwenden, und remote darauf zugreifen.

![Azure Cloud Services-Diagramm](./media/cloud-services-choose-me/diagram.png)

Mehr Kontrolle bedeutet leider oft auch weniger Benutzerfreundlichkeit. Wenn Sie die zusätzlichen Verwaltungsoptionen nicht benötigen, ist es im Vergleich zu Azure Cloud Services meist schneller und einfacher, Webanwendungen im Feature Web-Apps in App Service zu implementieren.

Es gibt zwei Arten von Azure Cloud Services-Rollen. Der einzige Unterschied besteht darin, wie Ihre Rolle auf den VMs gehostet wird:

* **Webrolle**: Ihre App wird mithilfe von IIS automatisch bereitgestellt und gehostet.

* **Workerrolle**: IIS wird nicht verwendet; die App wird eigenständig ausgeführt.

Einfache Anwendungen verwenden beispielsweise lediglich eine einzige Webrolle für eine Website. Komplexere Anwendungen verwenden eine Webrolle für eingehende Benutzeranforderungen und delegieren die Verarbeitung dieser Anforderungen an Workerrollen. (Für diese Kommunikation wird [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) oder [Azure Queue Storage](../storage/common/storage-introduction.md) verwendet.)

Die voranstehende Abbildung zeigt, dass alle VMs in einer einzigen Anwendung im gleichen Clouddienst laufen. Benutzer greifen über eine einzige öffentliche IP-Adresse auf die Anwendung zu, und die Anforderungen werden automatisch auf die einzelnen VMs verteilt. Die virtuellen Computer in einer Azure Cloud Services-Anwendung werden von der Plattform so [skaliert und bereitgestellt](cloud-services-how-to-scale-portal.md), dass einzelne Fehlerquellen (Single Points of Failure, SPOF) vermieden werden.

Wichtig: Anwendungen werden zwar auf virtuellen Computern ausgeführt, Azure Cloud Services stellen jedoch PaaS und nicht Infrastructure-as-a-Service (IaaS) bereit. Dies können Sie sich folgendermaßen vorstellen. Mit IaaS, z.B. Azure Virtual Machines, erstellen und konfigurieren Sie zuerst die Umgebung, in der Ihre Anwendung ausgeführt wird. Dann stellen Sie Ihre Anwendung in dieser Umgebung bereit. Sie sind zu einem großen Teil für die Verwaltung dieser Umgebung verantwortlich und müssen beispielsweise neue gepatchte Versionen des Betriebssystems auf den einzelnen virtuellen Computern bereitstellen. Im Gegensatz dazu ist die Umgebung bei PaaS gewissermaßen bereits vorhanden. Somit müssen Sie in diesem Fall nur noch Ihre Anwendung bereitstellen. Sie müssen sich weder um die Verwaltung der Plattform noch um das Aufspielen neuer Betriebssystemversionen kümmern.

## <a name="scaling-and-management"></a>Skalierung und Verwaltung
Mit Azure Cloud Services erstellen Sie keine virtuellen Computer. Stattdessen teilen Sie Azure mithilfe einer Konfigurationsdatei mit, wie viele Instanzen Sie benötigen, beispielsweise drei Webrolleninstanzen und zwei Workerrolleninstanzen. Die Plattform erstellt diese dann für Sie. Sie wählen zwar die [Größe](cloud-services-sizes-specs.md) der zugrunde liegenden virtuellen Computer aus, müssen diese aber nicht selbst erstellen. Wenn Ihre Anwendung eine höhere Last zu bewältigen hat, können Sie bei Azure weitere virtuelle Computer anfordern. Nimmt die Last wieder ab, können Sie die Instanzen herunterfahren, sodass sie keine weiteren Kosten verursachen.

Azure Cloud Services-Anwendungen werden Benutzern üblicherweise in zwei Schritten bereitgestellt. Zunächst führt ein Entwickler den [Upload der Anwendung](cloud-services-how-to-create-deploy-portal.md) in den Stagingbereich der Plattform durch. Wenn der Entwickler die Anwendung live schalten möchte, kann er sie über das Azure-Portal vom Staging in die Produktion wechseln. Der [Wechsel zwischen Staging und Produktion](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) erfolgt ohne Ausfallzeit. Dadurch können laufende Anwendungen ohne Beeinträchtigung der Benutzer auf neue Versionen aktualisiert werden.

## <a name="monitoring"></a>Überwachung
Azure Cloud Services bieten außerdem Überwachungsfunktionen. Genau wie bei Virtual Machines werden Ausfälle von physischen Servern erkannt und die entsprechenden virtuellen Computer auf einem anderen Server neu gestartet. Neben Hardwarefehlern erkennen Azure Cloud Services aber auch Ausfälle von virtuellen Computern und Anwendungen. Im Gegensatz zu Virtual Machines verfügt Cloud Services in jeder Web- und Workerrolle über einen Agent. Dadurch können bei einem Ausfall neue virtuelle Computer und Anwendungsinstanzen gestartet werden.

Das PaaS-Konzept von Azure Cloud Services hat noch weitere Auswirkungen. So müssen etwa Anwendungen für diese Technologie so entwickelt werden, dass sie auch dann ordnungsgemäß funktionieren, wenn eine beliebige Web- oder Workerrolle ausfällt. Aus diesem Grund dürfen Azure Cloud Services-Anwendungen ihren Zustand nicht im Dateisystem der eigenen virtuellen Computer speichern. Im Gegensatz zu VMs, die mit Virtual Machines erstellt werden, sind Schreibvorgänge auf Azure Cloud Services-VMs nicht persistent. Es gibt nichts Vergleichbares zu einem Virtual Machines-Datenträger. Azure Cloud Services-Anwendungen müssen daher sämtliche Zustandsinformationen in Azure SQL-Datenbank, Blobs, Tabellen oder auf andere externe Speichermedien schreiben. Auf diese Weise erstellte Anwendungen zeichnen sich durch eine bessere Skalierbarkeit und Fehlerresistenz aus – zwei wichtige Aspekte von Azure Cloud Services.

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen einer Clouddienst-App in .NET](cloud-services-dotnet-get-started.md) 
* [Erstellen einer Clouddienst-App in Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Erstellen einer Clouddienst-App in PHP](../cloud-services-php-create-web-role.md) 
* [Erstellen einer Clouddienst-App in Python](cloud-services-python-ptvs.md)



