---
title: Verwalten von Azure Media Services v3-Konten | Microsoft-Dokumentation
description: Um mit dem Verwalten, Verschlüsseln, Codieren, Analysieren und Streamen von Medieninhalten in Azure beginnen zu können, müssen Sie ein Media Services-Konto erstellen. In diesem Artikel wird erläutert, wie Azure Media Services v3-Konten verwaltet werden.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: inhenkel
ms.openlocfilehash: 77453aa2d8989a0542beffb1c88ebe7ee53d1506
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "96008326"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Verwalten von Azure Media Services v3-Konten

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Um mit dem Verwalten, Verschlüsseln, Codieren, Analysieren und Streamen von Medieninhalten in Azure beginnen zu können, müssen Sie ein Media Services-Konto erstellen. Wenn Sie ein Media Services-Konto erstellen, müssen Sie den Namen einer Azure Storage-Kontoressource angeben. Das angegebene Speicherkonto wird an Ihr Media Services-Konto angefügt. Das Media Services-Konto und alle zugeordneten Speicherkonten müssen im gleichen Azure-Abonnement enthalten sein. Weitere Informationen finden Sie unter [Speicherkonten](storage-account-concept.md).

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="moving-a-media-services-account-between-subscriptions"></a>Verschieben eines Media Services-Kontos zwischen Abonnements 

Wenn Sie ein Media Services-Konto in ein neues Abonnement verschieben möchten, müssen Sie zunächst die gesamte Ressourcengruppe, die das Media Services-Konto enthält, in das neue Abonnement verschieben. Dabei müssen alle angefügten Ressourcen verschoben werden: Azure Storage-Konten, Azure CDN-Profile usw. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Wie bei allen Ressourcen in Azure kann das Verschieben einer Ressourcengruppe einige Zeit in Anspruch nehmen.

> [!NOTE]
> Media Services v3 unterstützt das mehrinstanzenfähige Modell.

### <a name="considerations"></a>Überlegungen

* Erstellen Sie vor der Migration zu einem anderen Abonnement Sicherungen aller Daten in Ihrem Konto.
* Beenden Sie alle Streamingendpunkte und Livestreamingressourcen. Während der Verschiebung der Ressourcengruppe können die Benutzer nicht auf Ihre Inhalte zugreifen. 

> [!IMPORTANT]
> Starten Sie den Streamingendpunkt erst, nachdem der Verschiebevorgang erfolgreich abgeschlossen wurde.

### <a name="troubleshoot"></a>Problembehandlung

Wenn ein Media Services-Konto oder ein zugehöriges Azure Storage-Konto nach dem Verschieben der Ressourcengruppe „getrennt“ wird, versuchen Sie, die Speicherkontoschlüssel zu rotieren. Sollte das Rotieren der Speicherkontoschlüssel das Problem mit dem getrennten Media Services-Kontos nicht beheben, stellen Sie im Media Services-Konto über das Menü „Support und Problembehandlung“ eine neue Supportanfrage.  

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Kontos](./create-account-howto.md)
