---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971329"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Erstellen eines Media Services-Kontos

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Klicken Sie auf **+ Ressource erstellen** > **Medien** > **Media Services**.
1. Geben Sie im Abschnitt **Erstellen eines Media Services-Kontos** die erforderlichen Werte ein.

    | Name | BESCHREIBUNG |
    | ---|---|
    |**Kontoname**|Geben Sie den Namen des neuen Media Services-Kontos ein. Der Name eines Media Services-Kontos darf nur Kleinbuchstaben oder Ziffern ohne Leerzeichen enthalten und muss aus 3 bis 24 Zeichen bestehen.|
    |**Abonnement**|Wenn Sie über mehrere Abonnements verfügen, wählen Sie eines aus der Liste der Azure-Abonnements aus, auf die Sie Zugriff haben.|
    |**Ressourcengruppe**|Wählen Sie die neue oder vorhandene Ressource aus. Eine Ressourcengruppe ist eine Sammlung von Ressourcen mit gleichem Lebenszyklus, gleichen Berechtigungen und gleichen Richtlinien. [Hier](../../../azure-resource-manager/management/overview.md#resource-groups)erhalten Sie weitere Informationen.|
    |**Location**|Wählen Sie die geografische Region aus, in der die Medien- und Metadaten-Datensätze für Ihr Media Services-Konto gespeichert werden sollen. Diese Region wird zum Verarbeiten und Streamen Ihrer Medien verwendet. Im Dropdownlistenfeld werden nur die verfügbaren Media Services-Regionen angezeigt. |
    |**Speicherkonto**|Wählen Sie ein Speicherkonto aus, das als Blobspeicher für die Medieninhalte aus Ihrem Media Services-Konto dienen soll. Sie können ein vorhandenes Speicherkonto in derselben geografischen Region wie Ihr Media Services-Konto auswählen oder ein neues Speicherkonto erstellen. Ein neues Speicherkonto wird in derselben Region erstellt. Für Namen von Speicherkonten gelten die gleichen Regeln wie für Namen von Media Services-Konten.<br/><br/>Sie müssen über ein **primäres** Speicherkonto verfügen. Darüber hinaus können Sie beliebig viele **sekundäre** Speicherkonten an Ihr Media Services-Konto anfügen. Sie können das Azure-Portal verwenden, um sekundäre Speicherkonten hinzuzufügen. Weitere Informationen finden Sie unter [Azure Storage-Konten mit Azure Media Services-Konten](../storage-account-concept.md).<br/><br/>Das Media Services-Konto und alle zugeordneten Speicherkonten müssen im gleichen Azure-Abonnement enthalten sein. Zur Vermeidung zusätzlicher Wartezeit und Kosten für ausgehenden Datenverkehr wird dringend empfohlen, Speicherkonten zu verwenden, die sich an demselben Ort wie das Media Services-Konto befinden.|
    |**Erweiterte Einstellungen**| Sie können ein Konto mit einer vom System verwalteten Identität erstellen, indem Sie das Optionsfeld **Systemverwaltet** aktivieren.  Wenn Sie diese Option aktivieren, können Sie kundenseitig verwaltete Schlüssel oder BYOK (Bring Your Own Key) und Media Services verwenden, um vertrauenswürdigen Speicher zu ermöglichen.  Weitere Informationen zu kundenseitig verwalteten Schlüsseln finden Sie unter [Bring Your Own Key (vom Kunden verwaltete Schlüssel) in Media Services](../concept-use-customer-managed-keys-byok.md). Außerdem werden [verwaltete Identitäten](../concept-managed-identities.md) ebenfalls aktiviert.

1. Wählen Sie **An Dashboard anheften** aus, um den Status der Kontobereitstellung anzuzeigen.
1. Klicken Sie unten im Formular auf **Erstellen** .
