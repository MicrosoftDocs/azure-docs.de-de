---
title: include file
description: Includedatei für vertrauliche Clientszenario-Landing Pages (Daemon, Web-App, Web-API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996004"
---
## <a name="add-a-client-secret-or-certificate"></a>Hinzufügen eines geheimen Clientschlüssels oder eines Zertifikats

Wie bei jeder vertraulichen Clientanwendung müssen Sie ein Geheimnis oder ein Zertifikat hinzufügen, das als *Anmeldeinformationen* für die Anwendung fungiert, damit die Authentifizierung ohne Benutzerinteraktion erfolgen kann.

Sie können das [Azure-Portal](#add-client-credentials-by-using-the-azure-portal) oder ein Befehlszeilentool wie [PowerShell](#add-client-credentials-by-using-powershell) verwenden, um der Registrierung Ihrer Client-App Anmeldeinformationen hinzuzufügen.

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Hinzufügen von Clientanmeldeinformationen im Azure-Portal

Führen Sie zum Hinzufügen von Anmeldeinformationen zur App-Registrierung Ihrer vertraulichen Clientanwendung die unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](../articles/active-directory/develop/quickstart-register-app.md) beschriebenen Schritte für den gewünschten Anmeldeinformationstyp aus:

* [Geheimen Clientschlüssel hinzufügen](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Hinzufügen eines Zertifikats](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>Hinzufügen von Clientanmeldeinformationen mithilfe von PowerShell

Alternativ können Sie Anmeldeinformationen mithilfe von PowerShell hinzufügen, wenn Sie Ihre Anwendung bei Microsoft Identity Platform registrieren.

Im Codebeispiel [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) auf GitHub wird gezeigt, wie Sie einen geheimen Anwendungsschlüssel oder ein Zertifikat beim Registrieren einer Anwendung hinzufügen:

- Ausführliche Informationen zum Hinzufügen eines **geheimen Clientschlüssels** mithilfe von PowerShell finden Sie unter [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Ausführliche Informationen zum Hinzufügen eines **Zertifikats** mithilfe von PowerShell finden Sie unter [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
