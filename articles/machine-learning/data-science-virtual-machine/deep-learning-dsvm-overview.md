---
title: Einführung zur Deep Learning Virtual Machine – Azure | Microsoft-Dokumentation
description: Wichtige Analyseszenarien und -komponenten für Deep Learning Virtual Machines
keywords: Deep Learning, KI, Data Science-Tools, virtueller Data Science-Computer, Tools für Data Science, Linux Data Science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 879f5939f110fb841ad160bf09f597edcdd86d31
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502191"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Einführung zur Deep Learning Virtual Machine

## <a name="why-deep-learning-virtual-machine"></a>Gründe für die Verwendung der Deep Learning Virtual Machine 

Immer häufiger werden bevorzugt Deep Learning-Algorithmen bzw. neuronale Deep Learning-Netze bei zahlreichen Problemen im Bereich des maschinellen Lernens eingesetzt. Sie erweisen sich insbesondere bei kognitiven von Computern durchgeführten Aufgaben als nützlich, wie etwa bei Bildern, Texten und Audio- bzw. Videoinhalten. In bestimmten Bereichen werden so Ergebnisse erzielt, die an die kognitiven Fähigkeiten von Menschen herankommen, was vor allem den fortschrittlichen Architekturen neuronaler Deep Learning-Netze und der Verfügbarkeit umfangreicher Datasets zum Trainieren von Modellen zu verdanken ist. Um Modelle mit derart großen Datasets trainieren zu können, ist beim Einsatz von Deep Learning eine immense Rechnerleistung erforderlich. Dank der Cloud und der Verfügbarkeit von GPUs (Graphics Processing Units) ist es nun möglich, komplexe neuronale Deep Learning-Architekturen zu erstellen und mit großen Datasets in leistungsstarken Computeinfrastrukturen in der Cloud zu trainieren.  Der [virtuelle Data Science-Computer](overview.md) (Data Science Virtual Machine, DSVM) bietet hierfür eine große Bandbreite an Tools und Beispielen für die Datenvorbereitung, maschinelles Lernen und Deep Learning. Doch eine der Herausforderungen für Benutzer ist es, Tools und Beispiele für bestimmte Szenarien wie Deep Learning zu ermitteln und zudem GPU-basierte VM-Instanzen einfacher bereitzustellen. Für diese Herausforderungen bietet die Deep Learning Virtual Machine (DLVM) eine Lösung. 

## <a name="what-is-deep-learning-virtual-machine"></a>Gründe für die Verwendung der Deep Learning Virtual Machine 
Die Deep Learning Virtual Machine ist eine speziell konfigurierte Variante der [Data Science Virtual Machine](overview.md) (DSVM), die den Einsatz von GPU-basierten VM-Instanzen zum Training von Deep Learning-Modellen vereinfacht. Unterstützung hierfür bieten Windows 2016 und die Ubuntu Data Science Virtual Machine.  Die Deep Learning Virtual Machine verwendet dieselben elementaren VM-Images (und bietet daher dasselbe umfangreiche Toolset) wie die DSVM, ist jedoch für einen einfacheren Einsatz von Deep Learning konfiguriert. Wir bieten auch nahtlose Beispiele für die Bild- und Textanalyse, die sich großflächig auf zahlreiche realistische KI-Szenarien anwenden lassen. Bei der Deep Learning Virtual Machine soll zudem die große Bandbreite an Tools und Beispielen für die DSVM leichter erkennbar gemacht werden, indem auf der Oberfläche ein Katalog mit den Tools und Beispielen für den virtuellen Computer angezeigt wird. In Bezug auf die Tools bietet die Deep Learning Virtual Machine verschiedene beliebte Deep Learning-Frameworks, Tools für den Abruf und die Vorabverarbeitung von Bildern sowie Textdaten. Eine umfassende Liste der Tools finden Sie auf der [Übersichtsseite zur Data Science Virtual Machine](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich anhand der folgenden Schritte mit der Deep Learning Virtual Machine vertraut:

* [Bereitstellen einer Deep Learning Virtual Machine](provision-deep-learning-dsvm.md)
* [Verwenden der Deep Learning Virtual Machine](use-deep-learning-dsvm.md)
* [Toolreferenz](dsvm-deep-learning-ai-frameworks.md)
* [Beispiele](dsvm-samples-and-walkthroughs.md)
