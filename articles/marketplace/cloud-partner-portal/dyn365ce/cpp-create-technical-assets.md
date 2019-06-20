---
title: Erstellen technischer Ressourcen für Dynamics 365 for Customer Engagement | Azure Marketplace
description: Erstellen Sie die technischen Ressourcen für ein Dynamics 365 for Customer Engagement-Angebot.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/29/2018
ms.author: pabutler
ms.openlocfilehash: eff175264677d6b8ffb885229b5e68b306424335
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943109"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Erstellen der technischen Ressourcen für ein Azure-Anwendungsangebot

In der Regel entwickeln Sie Lösungen mithilfe des [SDK for Dynamics 365 Customer Engagement](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  Lösungen können eine Vielzahl von Formen annehmen, wie in [Programmiermodelle für Dynamics 365 Customer Engagement SDK](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models) beschrieben.  Wählen Sie die Form aus, die Ihren Anforderungen an die Lösung am besten entspricht.  Wenn Sie eine Lösung entwickeln, müssen Sie eine Reihe von Problemen berücksichtigen, z.B. Auswahl der Erweiterbarkeit, Lösungskomponenten und Versionskompatibilität.  Weitere Informationen finden Sie unter [Einführung in Lösungen](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions).

Die meisten in AppSource veröffentlichten Dynamics 365-Lösungen sind verwaltete Anwendungen, die als Paketdateien verteilt werden.


## <a name="creating-and-storing-the-package"></a>Erstellen und Speichern des Pakets

Die parallele Dokumentation zum Erstellen von Dynamics 365 for Customer Engagement-Angeboten finden Sie im Abschnitt [Veröffentlichen Sie die App für AppSource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource).  In den folgenden enthaltenen Themen werden das Erstellen der Lösungspaketdatei und ihr Hochladen in Azure Storage detailliert beschrieben:

- [Schritt 4: Erstellen eines AppSource-Pakets für die App](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource): Hier wird das Erstellen einer komprimierten Datei (ZIP) erläutert, die Ihre verwaltete Anwendung darstellt und Folgendes enthält: Lösungsressourcenordner, DLL mit benutzerdefiniertem Code, MIME-Typ-Informationsdatei, AppSource-Paketsymbol, Lizenzbedingungendatei (HTML) und Inhaltsdatei (XML).
- [Schritt 5: Speichern Sie das AppSource-Paket in Azure Storage, und generieren Sie eine URL mit dem SAS-Schlüssel](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage): Hier werden das Speichern einer AppSource-Paketdatei in einem Microsoft Azure-Blobspeicherkonto und das Verwenden eines Shared Access Signature-Schlüssels (SAS) zum Freigeben der Paketdatei erläutert. Ihre Paketdatei wird von Ihrem Azure Storage-Speicherort zur Zertifizierung abgerufen und dann für AppSource-Tests und -Veröffentlichung verwendet.


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie dies nicht bereits getan haben, [erstellen Sie Ihr Dynamics 365 for Customer Engagement-Angebot](./cpp-create-offer.md).  Dann können Sie [Ihr Angebot veröffentlichen](./cpp-publish-offer.md).
