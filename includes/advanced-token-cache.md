---
title: include file
description: include file
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628005"
---
Sie können mit der Tokencacheimplementierung von MSAL Hintergrund-Apps, APIs und Diensten die Verwendung des Zugriffstokencaches ermöglichen, um bei Abwesenheit von Benutzern weiterhin in deren Auftrag zu agieren. Dies ist besonders nützlich, wenn die Hintergrund-Apps und Dienste im Auftrag des Benutzers weiterhin funktionieren müssen, nachdem der Benutzer die Front-End-Web-App beendet hat.

Heutzutage verwenden die meisten Hintergrundprozesse [Anwendungsberechtigungen](/graph/auth/auth-concepts#microsoft-graph-permissions), wenn sie mit den Daten eines Benutzers arbeiten müssen, ohne dass dieser zum Authentifizieren oder erneuten Authentifizieren anwesend ist. Da Anwendungsberechtigungen häufig eine Zustimmung des Administrators erfordern, was eine Erhöhung von Berechtigungen erfordert, entstehen vermeidbare Reibungsverluste, wenn der Entwickler nicht beabsichtigte, eine Berechtigung abzurufen, die über die Berechtigung hinausgeht, in die der Benutzer ursprünglich für seine App eingewilligt hat.

Dieses Codebeispiel auf GitHub zeigt, wie Sie diese vermeidbaren Reibungsverluste vermeiden, indem Sie über Hintergrund-Apps auf den Tokencache von MSAL zugreifen:

 [Zugreifen auf den Tokencache des angemeldeten Benutzers über Hintergrund-Apps, APIs und Dienste](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)