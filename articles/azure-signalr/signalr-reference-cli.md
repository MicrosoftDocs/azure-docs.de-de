---
title: Azure CLI-Beispiele – Azure SignalR-Dienst
description: Azure CLI-Beispiele – Azure SignalR-Dienst
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61071075"
---
# <a name="azure-cli-reference"></a>Azure CLI-Referenz

Die folgende Tabelle enthält Links zu Bash-Skripts für den Azure SignalR-Dienst, die mithilfe der Azure CLI 2.0 erstellt wurden.

| | |
|-|-|
|**Erstellen**||
| [Erstellen eines neuen SignalR-Diensts und einer neuen Ressourcengruppe](scripts/signalr-cli-create-service.md) | Dieses Skript erstellt eine neue Azure SignalR-Dienstressource in einer neuen Ressourcengruppe mit einem beliebigen Namen.  |
|**Integrieren**||
| [Erstellen eines neuen SignalR-Diensts und einer für die Verwendung von SignalR konfigurierten Web-App](scripts/signalr-cli-create-with-app-service.md) | Dieses Skript erstellt eine neue Azure SignalR-Dienstressource in einer neuen Ressourcengruppe mit einem beliebigen Namen. Es fügt auch eine neue Web-App und einen App Service-Plan hinzu, um eine ASP.NET Core-Web-App zu hosten, die den SignalR-Dienst verwendet. Die Web-App ist mit einer App-Einstellung konfiguriert, um eine Verbindung mit der neuen SignalR-Dienstressource herzustellen. |
| [Erstellen eines neuen SignalR-Diensts und einer für die Verwendung von SignalR und GitHub OAuth konfigurierten Web-App](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Dieses Skript erstellt eine neue Azure SignalR-Dienstressource in einer neuen Ressourcengruppe mit einem beliebigen Namen. Es fügt auch eine neue Azure Web-App und einen Hostingplan hinzu, um eine ASP.NET Core-Web-App zu hosten, die den SignalR-Dienst verwendet. Die Web-App ist mit App-Einstellungen für die Verbindungszeichenfolge für die neue SignalR-Dienstressource sowie mit geheimen Clientschlüsseln konfiguriert, um die [GitHub-Authentifizierung](https://developer.github.com/v3/guides/basics-of-authentication/) zu unterstützen. Dies wird im [Tutorial zur Authentifizierung](signalr-concept-authenticate-oauth.md) veranschaulicht. Die Web-App ist auch für die Verwendung eines lokalen Git-Repositorys als Bereitstellungsquelle konfiguriert. |
| | |
