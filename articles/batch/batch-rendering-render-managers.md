---
title: 'Unterstützung von Render-Managern: Azure Batch'
description: Verwenden von Azure für das Rendering mit Integration eines Azure Batch-Render-Managers
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: bcc66a73e3d7986b177b13eb309ad664a006b960
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118585"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Verwenden von Azure Batch mit Renderfarm-Managern

Wenn Sie eine vorhandene lokale Renderfarm nutzen, ist die Wahrscheinlichkeit hoch, dass die Kapazität und die Renderaufträge der Renderfarm mit einem Render-Manager gesteuert werden.

In Azure werden entweder die integrierte Unterstützung oder Add-Ons für beliebte Render-Manager bereitgestellt. Sie können dann Azure-VMs hinzufügen und entfernen, z.B. auch VMs mit nutzungsbasierter Anwendungslizenzierung und VMs mit niedriger Priorität.

Die folgenden Render-Manager werden unterstützt:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](http://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Verwenden von Azure mit PipelineFX Qube

Skripts und eine Anleitung zum Aktivieren von Azure Batch-Pool-VMs als Qube-Worker finden Sie im entsprechenden [GitHub-Repository](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Verwenden von Azure mit Royal Render

Royal Render verfügt standardmäßig über eine Azure- und Azure Batch-Integration, sodass Sie eine Renderfarm mit Azure-basierten VMs erweitern können. Eine Zusammenfassung hierzu finden Sie in den [Hilfedateien](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Ein Beispiel für einen Royal Render-Kunden, der die Azure-Integration nutzt, finden Sie unter dem [Jellyfish Pictures-Kundenbericht](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Verwenden von Azure mit Thinkbox Deadline

Skripts und eine Anleitung zum Aktivieren von Azure Batch-Pool-VMs als Deadline-Slaves finden Sie im entsprechenden [GitHub-Repository](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>Nächste Schritte

Testen Sie die Azure Batch-Integration für Ihren Render-Manager, indem Sie das entsprechende Plug-In und die Anleitung auf GitHub verwenden, falls zutreffend.