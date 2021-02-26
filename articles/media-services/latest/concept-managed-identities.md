---
title: Verwaltete Identitäten
description: Media Services kann mit verwalteten Azure-Identitäten verwendet werden.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 71a2b8f0734de80f71dbb2372f8600b464d6c606
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258438"
---
# <a name="managed-identities"></a>Verwaltete Identitäten

Die Verwaltung von Geheimnissen und Anmeldeinformationen für eine sichere Kommunikation zwischen verschiedenen Diensten stellt für Entwickler eine häufige Herausforderung dar. In Azure brauchen Entwickler dank verwalteter Identitäten keine Anmeldeinformationen mehr zu verwalten. Für die Azure-Ressource in Azure AD wird eine Identität bereitgestellt, mit der Azure Active Directory (Azure AD)-Token abgerufen werden.

Es gibt derzeit zwei Szenarien, in denen verwaltete Identitäten mit Media Services verwendet werden können:

- Verwendung der verwalteten Identität des Media Services Kontos für den Zugriff auf Speicherkonten

- Verwendung der verwalteten Identität des Media Services Kontos für den Zugriff auf Kundenschlüssel über Key Vault

In den nächsten beiden Abschnitten werden die Schritte der beiden Szenarien beschrieben.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Verwenden der verwalteten Identität des Media Services Kontos für den Zugriff auf Speicherkonten

1. Erstellen Sie ein Media Services-Kontos mit einer verwalteten Identität.
1. Gewähren Sie dem Prinzipal der verwalteten Identität Zugriff auf ein Speicherkonto, das Sie besitzen.
1. Von Media Services kann daraufhin unter Verwendung der verwalteten Identität in Ihrem Namen auf das Speicherkonto zugegriffen werden.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Verwenden der verwalteten Identität des Media Services Kontos für den Zugriff auf Kundenschlüssel über Key Vault

1. Erstellen Sie ein Media Services-Kontos mit einer verwalteten Identität.
1. Gewähren Sie dem Prinzipal der verwalteten Identität Zugriff auf eine Key Vault-Instanz, die Sie besitzen.
1. Konfigurieren Sie das Media Services-Konto für die Verwendung der kundenschlüsselbasierten Kontoverschlüsselung.
1. Von Media Services wird unter Verwendung der verwalteten Identität in Ihrem Namen auf Key Vault zugegriffen.

Weitere Informationen zu kundenseitig verwalteten Schlüsseln und zu Key Vault finden Sie unter [Bring Your Own Key (vom Kunden verwaltete Schlüssel) in Media Services](concept-use-customer-managed-keys-byok.md).

## <a name="tutorials"></a>Tutorials

Die folgenden Tutorials enthalten beide oben erwähnten Szenarien:

- [Tutorial: Verwenden von kundenseitig verwalteten Schlüsseln (BYOK) mit Media Services über das Azure-Portal](tutorial-byok-portal.md)
- [Tutorial: Verwenden von kundenseitig verwalteten Schlüsseln (BYOK) mit der Media Services-REST-API](tutorial-byok-postman.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Funktionen verwalteter Identitäten für Sie und Ihre Azure-Anwendungen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).
