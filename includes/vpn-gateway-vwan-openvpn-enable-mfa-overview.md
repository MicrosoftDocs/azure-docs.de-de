---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995613"
---
Wenn Sie möchten, dass Benutzer vor der Gewährung des Zugriffs zur Angabe eines zweiten Authentifizierungsfaktor aufgefordert werden, können Sie Azure AD Multi-Factor Authentication (MFA) konfigurieren. Sie können MFA auf Benutzerbasis konfigurieren oder MFA über [bedingten Zugriff](../articles/active-directory/conditional-access/overview.md) nutzen.

* MFA auf Benutzerbasis kann ohne zusätzliche Kosten aktiviert werden. Wenn jedoch MFA auf Benutzerbasis aktiviert wird, wird der Benutzer zur Authentifizierung mit dem zweiten Faktor für alle Anwendungen aufgefordert, die an den Azure AD-Mandanten gebunden sind. Informationen zu den erforderlichen Schritten finden Sie unter [Option 1](#peruser).
* Der bedingte Zugriff ermöglicht eine differenziertere Kontrolle darüber, wie ein zweiter Faktor höher gestuft werden soll. Er kann die Zuweisung von MFA nur an VPN ermöglichen und andere Anwendungen ausschließen, die an den Azure AD-Mandanten gebunden sind. Informationen zu den erforderlichen Schritten finden Sie unter [Option 2](#conditional).