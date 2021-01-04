---
title: include file
description: include file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4ceae4e7e2d10c80a929a4a822c877da8d8478f0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748258"
---
Für die Bereitstellung in einer Azure-Web-App über FTP oder ein lokales Git kann ein *Bereitstellungsbenutzer* verwendet werden. Nach der Konfiguration des Bereitstellungsbenutzers können Sie ihn für alle Azure-Bereitstellungen verwenden. Der Benutzername und das Kennwort für die Bereitstellung auf Kontoebene unterscheiden sich von den Anmeldeinformationen für Ihr Azure-Abonnement. 

Führen Sie zum Konfigurieren des Bereitstellungsbenutzers den Befehl [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) in Azure Cloud Shell aus. Ersetzen Sie \<username> und \<password> durch Ihren Benutzernamen bzw. Ihr Kennwort für die Bereitstellung. 

- Der Benutzername muss in Azure eindeutig sein und darf bei lokalen Git-Pushes nicht das Symbol „\@“ enthalten. 
- Das Kennwort muss mindestens acht Zeichen lang sein und zwei der folgenden drei Elemente enthalten: Buchstaben, Zahlen und Symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

In der JSON-Ausgabe wird das Kennwort als `null` angezeigt. Wenn Sie den Fehler `'Conflict'. Details: 409` erhalten, müssen Sie den Benutzernamen ändern. Wenn Sie den Fehler `'Bad Request'. Details: 400` erhalten, müssen Sie ein sichereres Kennwort verwenden. 

Notieren Sie Ihren Benutzernamen und Ihr Kennwort für die Bereitstellung Ihrer Web-Apps.
