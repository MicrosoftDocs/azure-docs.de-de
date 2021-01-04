---
title: include file
description: include file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015989"
---
| Resource | BESCHREIBUNG|
|----------|------------|
| **Imagequelle** | Dies ist eine Ressource, die zum Erstellen einer **Imageversion** in einem Imagekatalog verwendet werden kann. Eine Imagequelle kann eine vorhandene Azure-VM, die entweder [generalisiert oder spezialisiert](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images) ist, ein verwaltetes Image, eine Momentaufnahme oder eine Imageversion in einem anderen Imagekatalog sein. |
| **Imagekatalog** | Wie der Azure Marketplace ist ein **Imagekatalog** ein Repository zum Verwalten und Teilen von Images, aber Sie kontrollieren, wer Zugriff hat. |
| **Imagedefinition** | Imagedefinitionen werden in einem Katalog erstellt und enthalten Informationen zum Image und zu den Anforderungen für seine interne Verwendung. Dies schließt ein, ob das Image Windows oder Linux ist, Anmerkungen zu dieser Version und Anforderungen an den minimalen und maximalen Arbeitsspeicher. Es ist eine Definition eines Imagetyps. |
| **Imageversion** | Eine **Imageversion** ist, was Sie verwenden, um einen virtuellen Computer zu erstellen, wenn Sie einen Katalog verwenden. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Wie bei einem verwalteten Image wird, wenn Sie eine **Imageversion** zum Erstellen einer VM verwenden, wird die Imageversion verwendet, um neue Datenträger für den virtuellen Computer zu erstellen. Imageversionen können mehrmals verwendet werden. |