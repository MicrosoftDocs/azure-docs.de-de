---
title: include file
description: include file
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028222"
---
Eine in Azure Batch ausgeführte Aufgabe kann während der Ausführung Ausgabedaten erzeugen. Oft müssen Aufgabenausgabedaten gespeichert werden, damit sie von anderen Aufgaben des Auftrags, von der Clientanwendung, die den Auftrag ausgeführt hat, oder von beiden abgerufen werden können. Aufgaben schreiben Ausgabedaten in das Dateisystem eines Batch-Computeknotens, doch alle Daten im Knoten gehen verloren, wenn ein Reimaging ausgeführt wird oder der Knoten den Pool verlässt. Außerdem können Aufgaben eine Vermerkdauer für Dateien aufweisen, nach der die von der Aufgabe erstellten Dateien gelöscht werden. Aus diesen Gründen es ist wichtig, Aufgabenausgaben, die Sie später benötigen, in einem Datenspeicher wie [Azure Storage](../articles/storage/index.yml) beizubehalten.

Informationen zu den Optionen für Speicherkonten in Batch finden Sie unter [Batch-Konten und Azure Storage-Konten](../articles/batch/accounts.md#azure-storage-accounts).