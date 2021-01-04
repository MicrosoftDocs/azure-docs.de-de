---
title: 'Azure App Configuration-REST-API: Autorisierung'
description: Referenzseiten für die Autorisierung über die Azure App Configuration-REST-API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932641"
---
# <a name="authorization"></a>Autorisierung

Als Autorisierung wird der Prozess zum Bestimmen der Berechtigungen bezeichnet, die eine aufrufende Funktion beim Übermitteln einer Anforderung besitzt. Es gibt verschiedene Autorisierungsmethoden. Das für eine Anforderung verwendete Autorisierungsmodell richtet sich nach der verwendeten [Authentifizierungsmethode](./rest-api-authentication-index.md). Die Autorisierungsmodelle sind im Folgenden aufgeführt.

## <a name="hmac"></a>HMAC

Das mit der HMAC-Authentifizierung verknüpfte [Autorisierungsmodell](./rest-api-authorization-hmac.md) teilt Berechtigungen in schreibgeschützten Zugriff und Lese-/Schreibzugriff auf. Weitere Informationen dazu finden Sie auf der Seite [HMAC-Autorisierung](./rest-api-authorization-hmac.md).

## <a name="azure-active-directory"></a>Azure Active Directory

Das mit der Azure Active Directory-Authentifizierung verknüpfte [Autorisierungsmodell](./rest-api-authorization-azure-ad.md) verwendet Azure RBAC zur Steuerung von Berechtigungen. Weitere Informationen dazu finden Sie auf der Seite [Azure AD-Autorisierung](./rest-api-authorization-azure-ad.md).
