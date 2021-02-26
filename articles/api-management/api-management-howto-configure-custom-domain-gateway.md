---
title: Konfigurieren eines benutzerdefinierten Domänennamens für Ihr selbstgehostete Azure API Management-Gateway | Microsoft-Dokumentation
description: In diesem Thema werden die Schritte zum Konfigurieren eines benutzerdefinierten Domänennamens für ein selbstgehostetes Azure API Management-Gateway beschrieben.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: d52bf87b74ae9b1770ed5092738fd05eb9f54fde
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99490995"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>Konfigurieren eines benutzerdefinierten Domänennamens für ein selbstgehostetes Gateway

Wenn Sie ein [selbstgehostetes Azure API Management-Gateway](self-hosted-gateway-overview.md) bereitstellen, wird ihm kein Hostname zugewiesen, und es muss über seine IP-Adresse darauf verwiesen werden. In diesem Artikel erfahren Sie, wie Sie einem selbstgehosteten Gateway einen vorhandenen benutzerdefinierten DNS-Namen (auch als Hostname bezeichnet) zuordnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der in diesem Artikel beschriebenen Schritte benötigen Sie Folgendes:

-   Ein aktives Azure-Abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Eine API Management-Instanz. Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).
- Ein selbstgehostetes Gateway. Weitere Informationen finden Sie unter [Bereitstellen eines selbstgehosteten Gateways in Azure API Management](api-management-howto-provision-self-hosted-gateway.md).
-   Einen benutzerdefinierten Domänennamen, der sich in Ihrem Besitz oder im Besitz Ihrer Organisation befindet. Dieses Thema enthält keine Anleitung zum Erwerben eines benutzerdefinierten Domänennamens.
-   Ein DNS-Eintrag, der auf einem DNS-Server gehostet wird, der den benutzerdefinierten Domänennamen der IP-Adresse des selbstgehosteten Gateways zuordnet. Dieses Thema enthält keine Anleitung zum Hosten eines DNS-Eintrags.
-   Sie benötigen ein gültiges Zertifikat mit einem öffentlichen und privaten Schlüssel (.pfx). Der Antragstellername oder der alternative Antragstellername (Subject Alternative Name, SAN) muss dem Domänennamen entsprechen, damit die API Management-Instanz auf sichere Weise URLs über TLS verfügbar machen kann.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Hinzufügen eines benutzerdefinierten Domänenzertifikats zum API Management-Dienst

Fügen Sie die Datei eines benutzerdefinierten Domänenzertifikats (.PFX) zu Ihrer API Management-Instanz hinzu, oder verweisen Sie auf ein in Azure Key Vault gespeichertes Zertifikat. Führen Sie die Schritte unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management](api-management-howto-mutual-certificates.md) aus.

> [!NOTE]
> Es empfiehlt sich, ein Schlüsseltresorzertifikat für die Domäne des selbstgehosteten Gateways zu verwenden.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Festlegen eines benutzerdefinierten Domänennamens für Ihr selbstgehostetes Gateway über das Azure-Portal

1. Wählen Sie die **Gateways** unter **Bereitstellung und Infrastruktur** aus.
2. Wählen Sie das selbstgehostete Gateway aus, für das Sie den Domänennamen konfigurieren möchten.
3. Wählen Sie **Hostnamen** unter **Einstellungen** aus.
4. Wählen Sie **+ Hinzufügen** aus.
5. Geben Sie den Ressourcennamen für den Hostnamen in das Feld **Name** ein.
6. Geben Sie in das Feld **Hostname** den Domänennamen ein.
7. Wählen Sie ein Zertifikat in der Dropdownliste **Zertifikat** aus.
8. Aktivieren Sie das Kontrollkästchen **Clientzertifikat aushandeln**, wenn eine der APIs, die über dieses Gateway verfügbar gemacht werden, die Clientzertifikatsauthentifizierung verwendet.
    > [!WARNING]
    > Diese Einstellung wird von allen Domänennamen gemeinsam genutzt, die für das Gateway konfiguriert sind.
9. Wählen Sie **Hinzufügen** aus, um den benutzerdefinierten Domänennamen dem ausgewählten selbstgehosteten Gateway zuzuweisen.

## <a name="next-steps"></a>Nächste Schritte

[Upgrade and scale an API Management instance](upgrade-and-scale.md) (Upgraden und Skalieren einer API Management-Instanz)
