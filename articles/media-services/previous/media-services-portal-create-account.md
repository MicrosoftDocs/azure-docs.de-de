---
title: Erstellen eines Azure Media Services-Kontos über das Azure-Portal | Microsoft Docs
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie über das Azure-Portal ein Azure Media Services-Konto erstellen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: ddc1c7f2dd207cba18a8c080c8b14cc53c149a39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463323"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Erstellen eines Media Services-Kontos im Azure-Portal

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Über das Azure-Portal können Sie schnell ein AMS-Konto (Azure Media Services) erstellen. Sie können mit Ihrem Konto auf Mediendienste zugreifen, die Funktionen zum Speichern, Verschlüsseln, Codieren, Verwalten und Streamen von Medieninhalten in Azure bereitstellen. Beim Erstellen eines Media Services-Kontos erstellen Sie auch ein zugehöriges Speicherkonto (oder Sie verwenden ein vorhandenes). Wenn Sie ein Media Services-Konto löschen, bleiben die Blobs im zugehörigen Speicherkonto erhalten.

Sie können ein primäres Speicherkonto vom Typ „General Purpose v1“ oder „General Purpose v2“ verwenden. Derzeit können Sie im Azure-Portal nur v1 wählen, aber Sie können v2 hinzufügen, wenn Sie Ihr Konto per API oder PowerShell erstellen. Weitere Informationen zu Speichertypen finden Sie unter [Informationen zu Azure-Speicherkonten](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

Das Media Services-Konto und alle zugeordneten Speicherkonten müssen im gleichen Azure-Abonnement enthalten sein. Zur Vermeidung zusätzlicher Wartezeit und Kosten für ausgehenden Datenverkehr wird dringend empfohlen, Speicherkonten zu verwenden, die sich an demselben Ort wie das Media Services-Konto befinden.

In diesem Artikel wird beschrieben, wie Sie ein Media Services-Konto im Azure-Portal erstellen.

> [!NOTE]
> Weitere Informationen zur Verfügbarkeit von Azure Media Services-Features in verschiedenen Regionen finden Sie unter [Availability of Media Services features across datacenters](scenarios-and-availability.md#availability) (Datencenterübergreifende Verfügbarkeit von AMS-Features).

## <a name="prerequisites"></a>Voraussetzungen

Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-an-ams-account"></a>Erstellen eines AMS-Kontos

In diesem Abschnitt erfahren Sie, wie Sie ein AMS-Konto erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **+Neu** > **Web und mobil** > **Media Services**.
   
    ![Media Services – Erstellung](./media/media-services-create-account/media-services-new1.png)
3. Geben Sie unter **CREATE MEDIA SERVICES ACCOUNT** (MEDIA SERVICES-KONTO ERSTELLEN) die erforderlichen Werte ein.
   
    ![Media Services – Erstellung](./media/media-services-create-account/media-services-new3.png)
   
   1. Geben Sie unter **Kontoname**den Namen des neuen AMS-Kontos ein. Der Name eines Media Services-Kontos darf nur Kleinbuchstaben oder Ziffern ohne Leerzeichen enthalten und muss aus 3 bis 24 Zeichen bestehen.
   2. Wählen Sie unter „Abonnement“ das Azure-Abonnement aus, auf das Sie Zugriff haben.
   3. Wählen Sie unter **Ressourcengruppe**die neue oder vorhandene Ressource aus.  Eine Ressourcengruppe ist eine Sammlung von Ressourcen mit gleichem Lebenszyklus, gleichen Berechtigungen und gleichen Richtlinien. [Hier](../../azure-resource-manager/resource-group-overview.md#resource-groups) erhalten Sie weitere Informationen.
   4. Wählen Sie unter **Standort** die geografische Region aus, in der die Medien- und Metadaten-Datensätze für Ihr Media Services-Konto gespeichert werden sollen. Diese Region wird zum Verarbeiten und Streamen Ihrer Medien verwendet. Im Dropdownlistenfeld werden nur die verfügbaren Media Services-Regionen angezeigt. 
   5. Wählen Sie unter **Speicherkonto**ein Speicherkonto aus, das als Blobspeicher für die Medieninhalte aus Ihrem Media Services-Konto dienen soll. Sie können ein vorhandenes Speicherkonto in der gleichen geografischen Region auswählen, in der sich auch Ihr Media Services-Konto befindet, oder ein Speicherkonto erstellen. Ein neues Speicherkonto wird in derselben Region erstellt. Für Namen von Speicherkonten gelten die gleichen Regeln wie für Namen von Media Services-Konten.
      
       Weitere Informationen zum Speicher finden Sie [hier](../../storage/common/storage-introduction.md).
   6. Wählen Sie **An Dashboard anheften** aus, um den Status der Kontobereitstellung anzuzeigen.
4. Klicken Sie unten im Formular auf **Erstellen** .
   
    Nachdem die Erstellung des Kontos erfolgreich abgeschlossen wurde, wird die Seite mit der Übersicht geladen. In der Streamingendpunkt-Tabelle ist für das Konto ein Standard-Streamingendpunkt mit dem Status **Beendet** angegeben. 

    >[!NOTE]
    >Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen. 
   
## <a name="to-manage-your-ams-account"></a>Verwalten Ihres AMS-Kontos

Wenn Sie Ihr AMS-Konto verwalten (also etwa eine programmgesteuerte Verbindung mit der AMS-API herstellen, Videos hochladen, Medienobjekte codieren, den Inhaltsschutz konfigurieren oder den Auftragsstatus überwachen) möchten, wählen Sie im linken Bereich des Portals die Option **Einstellungen** aus. Navigieren Sie unter **Einstellungen** zu einem der verfügbaren Blätter (beispielsweise **API-Zugriff**, **Medienobjekte**, **Aufträge**, **Inhaltsschutz**).


## <a name="next-steps"></a>Nächste Schritte

Nun können Sie Dateien in Ihrer AMS-Konto hochladen. Weitere Informationen finden Sie unter [Upload files](media-services-portal-upload-files.md)(Hochladen von Dateien).

Informationen zum programmgesteuerten Zugreifen auf die AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md).

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

