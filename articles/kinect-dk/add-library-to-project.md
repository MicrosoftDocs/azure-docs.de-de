---
title: Hinzufügen der Azure Kinect-Bibliothek zu einem Visual Studio-Projekt
description: Erfahren Sie, wie Sie einem Visual Studio-Projekt das Azure Kinect-NuGet-Paket hinzufügen.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, Visual Studio 2017, Visual Studio 2019, nuget
ms.openlocfilehash: fd71f0d327b8c828cc9ddac5810757cccdffbcea
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359611"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Hinzufügen der Azure Kinect-Bibliothek zu einem Visual Studio-Projekt

Dieser Artikel führt Sie durch die Schritte zum Hinzufügen eines NuGet-Pakets für Azure Kinect zu Ihrem Visual Studio-Projekt.

## <a name="install-azure-kinect-nuget-package"></a>Installieren des Azure Kinect-NuGet-Pakets

So installieren Sie das Azure Kinect-NuGet-Paket:

1. Ausführliche Anweisungen zum Installieren eines NuGet-Pakets in Visual Studio finden Sie im [Schnellstart: Installieren und Verwenden eines Pakets in Visual Studio](/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Zum Hinzufügen des Pakets können Sie die Benutzeroberfläche des Paket-Managers verwenden, indem Sie im Projektmappen-Explorer mit der rechten Maustaste auf „Verweise“ klicken und „NuGet-Pakete verwalten“ auswählen.
3. Wählen Sie [nuget.org](https://www.nuget.org) als Paketquelle aus, wählen Sie die Registerkarte „Durchsuchen“ aus, und suchen Sie nach `Microsoft.Azure.Kinect.Sensor`.
4. Wählen Sie in der Liste das Paket aus, und installieren Sie es.

## <a name="use-azure-kinect-nuget-package"></a>Verwenden des Azure Kinect-NuGet-Pakets

Fügen Sie nach dem Hinzufügen des Pakets die Includes der Headerdatei zum Quellcode hinzu, wie etwa hier:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Jetzt sind Sie bereit, Ihre erste Anwendung zu erstellen](build-first-app.md)