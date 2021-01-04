---
title: Bereitstellen eines Speichersynchronisierungsdiensts
description: Stellen Sie die Cloudressource für die Azure-Dateisynchronisierung, einen Speichersynchronisierungsdienst, bereit. Ein gemeinsamer Textblock der Migrationsdokumentation.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043198"
---
Für diesen Schritt benötigen Sie die Anmeldeinformationen für Ihr Azure-Abonnement.

Die wichtigste Ressource, die für die Azure-Dateisynchronisierung konfiguriert werden muss, ist der *Speichersynchronisierungsdienst*. Es wird empfohlen, nur einen solchen Dienst für alle Server bereitzustellen, die aktuell oder in Zukunft für die Synchronisierung der gleichen Dateien verwendet werden. Erstellen Sie mehrere Speichersynchronisierungsdienste nur dann, wenn Sie über separate Server verfügen, zwischen denen niemals ein Datenaustausch stattfinden darf. Sie könnten z. B. über Server verfügen, die niemals dieselbe Azure-Dateifreigabe synchronisieren dürfen. Andernfalls ist die Best Practice ein einzelner Speichersynchronisierungsdienst.

Wählen Sie eine Azure-Region für Ihren Speichersynchronisierungsdienst aus, die sich in der Nähe Ihres Standorts befindet. Alle anderen Cloudressourcen müssen innerhalb derselben Region bereitgestellt werden. Für eine vereinfachte Verwaltung erstellen Sie in Ihrem Abonnement eine neue Ressourcengruppe für Synchronisierungs- und Speicherressourcen.

Weitere Informationen finden Sie im [Abschnitt über das Bereitstellen des Speichersynchronisierungsdiensts](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) im Artikel über das Bereitstellen der Azure-Dateisynchronisierung. Führen Sie nur die Schritte in diesem Teil des Artikels aus. Spätere Schritte enthalten Links zu anderen Abschnitten dieses Artikels.