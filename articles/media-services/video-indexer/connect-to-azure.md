---
title: Erstellen eines Video Indexer-Kontos im Azure-Portal
titlesuffix: Azure Media Services
description: In diesem Artikel wird veranschaulicht, wie Sie im Azure-Portal ein Video Indexer-Konto erstellen.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 33493f1bdff6071737aad4bfb8c7d0e5e22896db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799845"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Erstellen eines Video Indexer-Kontos mit Azure-Verbindung

Beim Erstellen eines Video Indexer-Kontos können Sie ein kostenloses Testkonto (mit einer bestimmten Anzahl von kostenlosen Indizierungsminuten) oder eine kostenpflichtige Option wählen (ohne Einschränkung durch eine Kontingentvorgabe). Bei der kostenlosen Testversion stellt Video Indexer bis zu 600 Minuten an kostenloser Indizierungszeit für Websitebenutzer und bis zu 2.400 Minuten an kostenloser Indizierungszeit für API-Benutzer bereit. Bei der kostenpflichtigen Option erstellen Sie ein Video Indexer-Konto, das mit Ihrem Azure-Abonnement und einem Azure Media Services-Konto verbunden ist. Sie bezahlen für die Minuten der Indizierungszeit und die Gebühren für das Media Services-Konto. 

In diesem Artikel wird veranschaulicht, wie Sie ein Video Indexer-Konto erstellen, das mit einem Azure-Abonnement und einem Azure Media Services-Konto verknüpft ist. Außerdem werden die Schritte zur Verbindungsherstellung mit Azure unter Verwendung des automatischen Flows (Standard) vorgestellt. Darüber hinaus wird gezeigt, wie Sie eine manuelle Verbindung mit Azure herstellen (erweitert).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement.

    Wenn Sie noch kein Azure-Abonnement haben, können Sie sich für ein [kostenloses Azure-Testkonto](https://azure.microsoft.com/free/) anmelden.

* Eine Azure AD-Domäne (Active Directory).

    Wenn Sie nicht über eine Azure AD-Domäne verfügen, können Sie diese mit Ihrem Azure-Abonnement erstellen. Weitere Informationen finden Sie unter [Verwalten von benutzerdefinierten Domänennamen in Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md).

* Einen Benutzer und ein Mitglied in Ihrer Azure AD-Domäne. Sie verwenden dieses Mitglied beim Herstellen der Verbindung für Ihr Video Indexer-Konto mit Azure.

    Dieser Benutzer muss ein Azure AD-Benutzer mit einem Geschäfts-, Schul- oder Unikonto sein. Es darf kein persönliches Konto wie „outlook.com“, „live.com“ oder „hotmail.com“ verwendet werden.

    ![Alle AAD-Benutzer](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Zusätzliche Voraussetzungen für den automatischen Flow

Einen Benutzer und ein Mitglied in Ihrer Azure AD-Domäne. Sie verwenden dieses Mitglied beim Herstellen der Verbindung für Ihr Video Indexer-Konto mit Azure.

Dieser Benutzer muss ein Mitglied Ihres Azure-Abonnements sein, der entweder der Rolle **Besitzer** oder den beiden Rollen **Mitwirkender** und **Benutzerzugriffsadministrator** zugewiesen sein muss. Ein Benutzer kann zweimal mit zwei Rollen hinzugefügt werden. Einmal mit „Mitwirkender“ und einmal mit „Benutzerzugriffsadministrator“.

![Zugriffssteuerung](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Zusätzliche Voraussetzungen für den manuellen Flow

Registrieren Sie den EventGrid-Ressourcenanbieter mit dem Azure-Portal.

Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu **Abonnements** > [Abonnement] > **ResourceProviders**. 

Suchen Sie nach **Microsoft.Media** und **Microsoft.EventGrid**. Klicken Sie auf **Registrieren**, falls der Status nicht „Registriert“ lautet. Die Registrierung nimmt einige Minuten in Anspruch.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Herstellen einer Verbindung mit Azure

1. Navigieren Sie zur [Video Indexer](https://www.videoindexer.ai/)-Website, und melden Sie sich an.

2. Klicken Sie auf die Schaltfläche **Neues Konto erstellen**:

    ![Herstellen einer Verbindung mit Azure](./media/create-account/connect-to-azure.png)

3. Wenn die Liste mit den Abonnements angezeigt wird, können Sie das gewünschte Abonnement auswählen.

    ![Herstellen einer Verbindung für Video Indexer mit Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Wählen Sie aus den unterstützten Standorten eine Azure-Region aus: „USA, Westen 2“, „Europa, Norden“ oder „Asien, Osten“.
5. Wählen Sie unter **Azure Media Services-Konto** eine dieser Optionen:

    * Wählen Sie **Neue Ressourcengruppe erstellen**, um ein neues Media Services-Konto zu erstellen. Geben Sie einen Namen für Ihre Ressourcengruppe an.

        Azure erstellt Ihr neues Konto unter Ihrem Abonnement, einschließlich eines neuen Azure Storage-Kontos. Ihr neues Media Services-Konto verfügt über eine Standard-Anfangskonfiguration mit einem Streamingendpunkt und zehn reservierten Einheiten vom Typ „S3“.
    * Wählen Sie **Vorhandene Ressource verwenden**, um ein vorhandenes Media Services-Konto zu verwenden. Wählen Sie Ihr Konto in der Liste mit den Konten aus.

        Ihr Media Services-Konto muss über dieselbe Region wie Ihr Video Indexer-Konto verfügen. 

        > [!NOTE]
        > Um die Indizierungsdauer zu reduzieren und den Durchsatz zu verbessern, wird dringend empfohlen, den Typ und die Anzahl von [reservierten Einheiten](../previous/media-services-scale-media-processing-overview.md ) in Ihrem Media Services-Konto auf **10 reservierte S3-Einheiten** festzulegen. Siehe [Verwenden des Portals zum Ändern der reservierten Einheiten](../previous/media-services-portal-scale-media-processing.md).

    * Um Ihre Verbindung manuell zu konfigurieren, klicken Sie auf den Link **Zur manuellen Konfiguration wechseln**.

        Ausführliche Informationen finden Sie im nachstehenden Abschnitt [Manuelle Verbindungsherstellung mit Azure (erweiterte Option)](#connect-to-azure-manually-advanced-option).
6. Wählen Sie **Verbinden**, wenn Sie fertig sind. Dieser Vorgang kann einige Minuten dauern. 

    Nachdem Sie die Verbindung mit Azure hergestellt haben, wird Ihr neues Video Indexer-Konto in der Kontoliste angezeigt:

    ![Neues Konto](./media/create-account/new-account.png)

7. Navigieren zu Ihrem neuen Konto

## <a name="connect-to-azure-manually-advanced-option"></a>Manuelle Verbindungsherstellung mit Azure (erweiterte Option)

Wenn die Verbindungsherstellung mit Azure nicht erfolgreich war, können Sie versuchen, das Problem über eine manuelle Verbindungsherstellung zu beheben.

> [!NOTE]
> Die folgenden drei Konten sollten unbedingt in derselben Region vorhanden sein: das Video Indexer-Konto, dessen Verbindung mit dem Media Services-Konto Sie herstellen, sowie das mit demselben Media Services-Konto verbundene Azure Storage-Konto.

### <a name="create-and-configure-a-media-services-account"></a>Erstellen und Konfigurieren eines Media Services-Konto

1. Verwenden Sie das [Azure](https://portal.azure.com/)-Portal, um ein Azure Media Services-Konto zu erstellen, wie beschrieben unter [Erstellen eines Kontos](../previous/media-services-portal-create-account.md).

    Wenn Sie ein Speicherkonto für Ihr Media Services-Konto erstellen, wählen Sie als Kontoart **StorageV2** und für die Replikation **Georedundant (GRS)** aus.

    ![Neues Azure Media Services-Konto](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Notieren Sie sich die Media Services-Ressource und die Kontonamen. Diese Informationen benötigen Sie für die Schritte im nächsten Abschnitt.

2. Ändern Sie den Typ und die Anzahl von [reservierten Einheiten](../previous/media-services-scale-media-processing-overview.md ) im erstellten Media Services-Konto in **10 reservierte S3-Einheiten**. Siehe [Verwenden des Portals zum Ändern der reservierten Einheiten](../previous/media-services-portal-scale-media-processing.md).
3. Bevor Sie Ihre Videos in der Video Indexer-Webanwendung abspielen können, müssen Sie den standardmäßigen **Streamingendpunkt** des neuen Media Services-Kontos starten.

    Klicken Sie im neuen Media Services-Konto auf **Streamingendpunkte**. Wählen Sie den Streamingendpunkt aus, und klicken Sie auf „Starten“.

    ![Neues Azure Media Services-Konto](./media/create-account/create-ams-account2.png)

4. Für die Video Indexer-Authentifizierung bei der Media Services-API muss eine AD-Anwendung erstellt werden. Die folgenden Schritte leiten Sie durch den Azure AD-Authentifizierungsvorgang, der in [Erste Schritte mit der Azure AD-Authentifizierung mithilfe des Azure-Portals](../previous/media-services-portal-get-started-with-aad.md) beschrieben wird:

    1. Wählen Sie im neuen Media Services-Konto **API-Zugriff** aus.
    2. Wählen Sie die [Dienstprinzipalauthentifizierung](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication) aus.
    3. Rufen Sie die Client-ID und das Clientgeheimnis ab, wie beschrieben in [Abrufen der Client-ID und des geheimen Clientschlüssels](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret).

        Nachdem Sie **Einstellungen**->**Schlüssel** ausgewählt, eine **Beschreibung** hinzugefügt und auf **Speichern** geklickt haben, werden die Schlüsselwerte aufgefüllt.

        Wenn der Schlüssel abläuft, muss der Kontobesitzer sich an den Video Indexer-Support wenden, um den Schlüssel zu erneuern.

        > [!NOTE]
        > Notieren Sie sich den Schlüsselwert und die Anwendungs-ID. Diese Informationen benötigen Sie für die Schritte im nächsten Abschnitt.

### <a name="connect-manually"></a>Manuelle Verbindungsherstellung

Klicken Sie auf der Seite **Video Indexer** im Dialogfeld [Video Indexer mit einem Azure-Abonnement verknüpfen](https://www.videoindexer.ai/) auf den Link **Zur manuellen Konfiguration wechseln**.

Geben Sie im Dialogfeld die folgenden Informationen ein:

|Einstellung|BESCHREIBUNG|
|---|---|
|Region für Video Indexer-Konto|Der Name der Region für das Video Indexer-Konto. Zur Verbesserung der Leistung und Senkung der Kosten wird dringend empfohlen, den Namen der Region anzugeben, in der sich die Azure Media Services-Ressourcen und das Azure Storage-Konto befinden. |
|Azure Active Directory-Mandant (AAD)|Der Name des Azure AD-Mandanten, z.B. „contoso.onmicrosoft.com“. Die Informationen zum Mandanten können über das Azure-Portal abgerufen werden. Platzieren Sie den Cursor in der oberen rechten Ecke über dem Namen des angemeldeten Benutzers. Der Name befindet sich rechts neben **Domäne**.|
|Abonnement-ID|Wählen Sie das Azure-Abonnement aus, unter dem die Verbindung erstellt werden soll. Die Abonnement-ID kann über das Azure-Portal abgerufen werden. Klicken Sie im linken Navigationsbereich auf **Alle Dienste**, und suchen Sie nach „Abonnements“. Wählen Sie **Abonnements** und dann in der Liste Ihrer Abonnements die gewünschte ID aus.|
|Name der Azure Media Services-Ressourcengruppen|Der Name der Ressourcengruppe, in der Sie das Media Services-Konto erstellt haben.|
|Name der Media Services-Ressource|Der Name des Azure Media Services-Kontos, das Sie im vorherigen Abschnitt erstellt haben.|
|Anwendungs-ID|Die Azure AD-Anwendungs-ID (mit Berechtigungen für das angegebene Media Services-Konto), die Sie im vorherigen Abschnitt erstellt haben.|
|Anwendungsschlüssel|Der Azure AD-Anwendungsschlüssel, den Sie im vorherigen Abschnitt erstellt haben. |

## <a name="considerations"></a>Überlegungen

Für Azure Media Services gilt Folgendes:

* Wenn Sie die Verbindung automatisch herstellen, werden in Ihrem Azure-Abonnement eine neue Ressourcengruppe, das Media Services-Konto und ein Speicherkonto angezeigt.
* Bei der automatischen Verbindungsherstellung legt Video Indexer die Einstellung für **Reservierte Einheiten für Medien** auf 10 S3-Einheiten fest:

    ![Reservierte Einheiten für Media Services](./media/create-account/ams-reserved-units.png)

* Falls Sie eine Verbindung mit einem vorhandenen Media Services-Konto hergestellt haben, ändert Video Indexer die vorhandene Konfiguration für **Reservierte Einheiten für Medien** nicht.

   Unter Umständen müssen Sie den Typ und die Anzahl von reservierten Einheiten für Medien gemäß Ihrer geplanten Last anpassen. Beachten Sie hierbei Folgendes: Wenn die Last hoch ist und Sie nicht über genügend Einheiten oder eine ausreichend hohe Geschwindigkeit verfügen, kann es bei der Videoverarbeitung zu Timeoutfehlern kommen.

* Bei der Verbindungsherstellung mit einem neuen Media Services-Konto startet Video Indexer dafür automatisch den standardmäßigen **Streamingendpunkt**:

    ![Media Services-Streamingendpunkt](./media/create-account/ams-streaming-endpoint.png)

    Streamingendpunkte benötigen relativ viel Zeit zum Starten. Deshalb kann es einige Minuten dauern, bis Ihre Videos gestreamt und in der Video Indexer-Webanwendung angesehen werden können.

* Falls Sie eine Verbindung mit einem vorhandenen Media Services-Konto hergestellt haben, ändert Video Indexer die Konfiguration für den standardmäßigen Streamingendpunkt nicht. Wenn kein ausgeführter **Streamingendpunkt** vorhanden ist, ist es für Sie nicht möglich, Videos über dieses Media Services-Konto oder in Video Indexer anzusehen.

## <a name="next-steps"></a>Nächste Schritte

Sie können programmgesteuert mit Ihrem Testkonto bzw. Ihren Video Indexer-Konten interagieren, die mit Azure verbunden sind. Befolgen Sie hierzu die Anweisungen unter [Verwenden von APIs](video-indexer-use-apis.md).

Sie sollten hierfür denselben Azure AD-Benutzer verwenden, den Sie beim Herstellen der Verbindung mit Azure genutzt haben.


