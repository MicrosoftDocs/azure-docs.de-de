---
title: Zulässige Zertifizierungsstellen für die Aktivierung von benutzerdefiniertem HTTPS
titleSuffix: Azure Content Delivery Network
description: Wenn Sie Ihr eigenes Zertifikat zur Aktivierung von HTTPS in einer benutzerdefinierten Domäne verwenden, müssen Sie für die Erstellung eine zulässige Zertifizierungsstelle (CA) verwenden.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573397"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Zulässige Zertifizierungsstellen für die Aktivierung von benutzerdefiniertem HTTPS

Es müssen bestimmte Zertifikatanforderungen erfüllt sein, wenn Sie [HTTPS mit Ihrem eigenen Zertifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) für eine benutzerdefinierte Azure Content Delivery Network-Domäne (CDN-Domäne) aktivieren. 

* Das Standardprofil **Azure CDN von Microsoft** erfordert ein Zertifikat einer der in der folgenden Liste aufgeführten genehmigten Zertifizierungsstellen. Bei Verwendung eines Zertifikats einer nicht genehmigten Zertifizierungsstelle oder eines selbstsignierten Zertifikats wird die Anforderung abgelehnt. 

* Die Profile vom Typ **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** akzeptieren jedes gültige Zertifikat jeder gültigen Zertifizierungsstelle. Verizon-Profile unterstützt keine selbstsignierten Zertifikate.

> [!NOTE]
> Die Option zum Aktivieren von HTTPS mit Ihrem eigenen Zertifikat für die benutzerdefinierte Domäne ist *nicht* für Profile vom Typ **Azure CDN Standard von Akamai** verfügbar. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

