---
title: 'Geschützte Web-API: Übersicht | Microsoft Azure'
description: Erfahren Sie, wie Sie eine geschützte Web-API erstellen (Übersicht).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080102"
---
# <a name="scenario-protected-web-api"></a>Szenario: Geschützte Web-API

In diesem Szenario lernen Sie, wie Sie eine Web-API verfügbar machen und wie Sie diese schützen können, damit nur authentifizierte Benutzer darauf zugreifen können. Sie sollten authentifizierten Benutzer mit Arbeits- und Uni-/Schulkonten oder Benutzern mit persönlichen Microsoft-Konten erlauben, Ihre Web-API zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Besonderheiten

Beachten Sie die folgenden Besonderheiten beim Schützen Ihrer Web-APIs:

- Ihre App-Registrierung muss mindestens einen Gültigkeitsbereich verfügbar machen. Die Tokenversion, die von Ihrer Web-API akzeptiert wird, hängt von der Zielgruppe der Anmeldung ab.
- Die Konfiguration des Codes für die Web-API muss das Token überprüfen, das beim Aufrufen der Web-API verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [App-Registrierung](scenario-protected-web-api-app-registration.md)
