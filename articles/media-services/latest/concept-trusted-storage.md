---
title: Vertrauenswürdiger Speicher für Media Services
description: Dank der Authentifizierung verwalteter Identitäten kann von Media Services per vertrauenswürdigem Speicherzugriff auf das Speicherkonto zugegriffen werden, das mit einer Firewall oder mit einer VNet-Einschränkung konfiguriert wurde.
keywords: vertrauenswürdiger Speicher, verwaltete Identitäten
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: e8d21e57f9a844b3cc0538f4805780829a1350f4
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428587"
---
# <a name="trusted-storage-for-media-services"></a>Vertrauenswürdiger Speicher für Media Services

Wenn Sie ein Media Services-Konto erstellen, müssen Sie es einem Speicherkonto zuordnen. Von Media Services kann per Systemauthentifizierung oder Authentifizierung verwalteter Identitäten auf dieses Speicherkonto zugegriffen werden. Media Services überprüft, ob sich das Media Services-Konto und das Speicherkonto im gleichen Abonnement befinden und ob der Benutzer, der die Zuordnung hinzufügt, Zugriff auf das Speicherkonto hat (mittels rollenbasierter Zugriffssteuerung für Azure Resource Manager).

## <a name="trusted-storage-with-a-firewall"></a>Vertrauenswürdiger Speicher mit einer Firewall

Wenn Sie jedoch eine Firewall einsetzen möchten, um Ihr Speicherkonto zu schützen und vertrauenswürdigen Speicher zu ermöglichen, ist die Authentifizierung mit [verwalteten Identitäten](concept-managed-identities.md) die bevorzugte Option. Dadurch kann von Media Services per vertrauenswürdigem Speicherzugriff auf das Speicherkonto zugegriffen werden, das mit einer Firewall oder mit einer VNET-Einschränkung konfiguriert wurde. Dadurch kann von Media Services per vertrauenswürdigem Speicherzugriff auf das Speicherkonto zugegriffen werden, das mit einer Firewall oder mit einer VNET-Einschränkung konfiguriert wurde.

> [!NOTE]
> Sie müssen den Zugriff „Mitwirkender an Storage-Blobdaten“ der Funktion „Verwaltete Identität“ von Azure Media Services gewähren, damit Media Services Speicherkontodaten lesen und schreiben kann.  Das Gewähren der allgemeinen Rolle „Mitwirkender“ funktioniert nicht, da dadurch nicht die ordnungsgemäßen Berechtigungen auf Datenebene aktiviert werden.

## <a name="further-reading"></a>Weiterführende Themen

Um die Methoden zum Erstellen von vertrauenswürdigem Speicher mit verwalteten Identitäten zu verstehen, lesen Sie [Verwaltete Identitäten und Media Services](concept-managed-identities.md).

Weitere Informationen zu vertrauenswürdigen Microsoft-Diensten finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Funktionen verwalteter Identitäten für Sie und Ihre Azure-Anwendungen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).
