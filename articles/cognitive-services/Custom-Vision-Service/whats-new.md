---
title: Neuerungen in Custom Vision
titleSuffix: Azure Cognitive Services
description: Dieser Artikel enthält Neuigkeiten zu Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 57f3cf0cb15243d054da0111366f3a1dc0fb5349
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95739756"
---
# <a name="whats-new-in-custom-vision"></a>Neuerungen in Custom Vision

Informieren Sie sich über die Neuerungen im Dienst. Dabei kann es sich um Versionshinweise, Videos, Blogbeiträge und andere Informationen handeln. Legen Sie ein Lesezeichen für diese Seite an, um über den Dienst auf dem Laufenden zu bleiben.


## <a name="october-2020"></a>Oktober 2020 

### <a name="custom-base-model"></a>Benutzerdefiniertes Basismodell

- Einige Anwendungen verfügen über eine große Menge gemeinsamer Trainingsdaten, müssen aber ihre Modelle separat optimieren. Dies führt zu einer besseren Leistung für Images aus verschiedenen Quellen mit geringfügigen Unterschieden. In diesem Fall können Sie das erste Modell wie gewohnt mit einer großen Menge an Trainingsdaten trainieren. Rufen Sie dann **TrainProject** in der öffentlichen Vorschauversion 3.4 der API mit _CustomBaseModelInfo_ im Anforderungstext auf, um das in der ersten Phase trainierte Modell als Basismodell für Downstreamprojekte zu verwenden. Wenn das Quellprojekt und das Downstreamzielprojekt ähnliche Imagemerkmale aufweisen, können Sie eine bessere Leistung erwarten. 

### <a name="new-domain-information"></a>Neue Domäneninformationen

- Die Domäneninformationen, die von **GetDomains** in der öffentlichen Vorschauversion 3.4 der API von Custom Vision zurückgegeben werden, enthalten jetzt unterstützte exportierbare Plattformen, eine kurze Beschreibung der Modellarchitektur und die Größe des Modells für kompakte Domänen.

### <a name="training-divergence-feedback"></a>Feedback zu Trainingsabweichungen

- Die öffentliche Vorschauversion 3.4 der API von Custom Vision gibt jetzt **TrainingErrorDetails** aus dem Aufruf **GetIteration** zurück. Bei fehlerhaften Iterationen zeigt dies, ob der Fehler durch Trainingsabweichungen verursacht wurde, die mit mehr und qualitativ hochwertigeren Trainingsdaten behoben werden können.

## <a name="july-2020"></a>Juli 2020

### <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure

* Custom Vision unterstützt die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC), ein Autorisierungssystem für die Verwaltung des individuellen Zugriffs auf Azure-Ressourcen. Weitere Informationen zum Verwalten des Zugriffs auf Ihre Custom Vision-Projekte finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](./role-based-access-control.md).

### <a name="subset-training"></a>Teilmengentraining

* Beim Training eines Objekterkennungsprojekts können Sie optional auch nur mit einer Teilmenge Ihrer angewendeten Tags trainieren. Das kann sinnvoll sein, wenn bestimmte Tags noch nicht ausreichend angewendet wurden, andere jedoch schon. Sehen Sie sich den Artikel [Schnellstart: Erstellen eines Objekterkennungsprojekts mit dem Custom Vision SDK](./quickstarts/object-detection.md) zu C# oder Python an, um weitere Informationen zu erhalten.

### <a name="azure-storage-notifications"></a>Speicherbenachrichtigungen in Azure

* Sie können Ihr Custom Vision-Projekt in eine Azure-Blobspeicher-Warteschlange integrieren, um Pushbenachrichtigungen zu Trainings- und Exportaktivitäten für Projekte und zu Sicherungskopien von veröffentlichten Modellen zu erhalten. Dieses Feature ist hilfreich, um das ständige Abfragen des Diensts nach Ergebnissen zu vermeiden, wenn lange Vorgänge ausgeführt werden. Stattdessen können Sie die Benachrichtigungen für die Speicherwarteschlange in Ihren Workflow integrieren. Unter [Integrieren von Azure-Speicher für Benachrichtigungen und Sicherungen](./storage-integration.md) finden Sie weitere Informationen.

### <a name="copy-and-move-projects"></a>Kopieren und Verschieben von Projekten

* Sie können nun Projekte von einem Custom Vision-Konto in andere kopieren. Beispielsweise können Sie ein Projekt aus einer Entwicklungsumgebung in eine Produktionsumgebung verschieben oder ein Projekt in einem Konto in einer anderen Azure-Region sichern, um die Datensicherheit zu erhöhen. Unter [Kopieren und Verschieben von Custom Vision-Projekten](./copy-move-projects.md) finden Sie weitere Informationen.

## <a name="september-2019"></a>September 2019

### <a name="suggested-tags"></a>Vorgeschlagene Tags

* Die intelligente Bezeichnungserstellung auf der [Custom Vision-Website](https://www.customvision.ai/) generiert vorgeschlagene Tags für Ihre Trainingsbilder. Auf diese Weise können Sie eine große Anzahl von Bildern schneller beschriften, wenn Sie ein Custom Vision-Modell trainieren. Eine Anleitung für dieses Feature finden Sie unter [Schnelleres Beschriften von Bildern mit der intelligenten Bezeichnungserstellung](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>Cognitive Services-Updates

[Azure-Updateankündigungen für Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)