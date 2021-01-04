---
author: baanders
description: Includedatei zum Einrichten der lokalen Authentifizierung für DefaultAzureCredential in Azure Digital Twins-Beispielen – ohne Einführung
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011257"
---
Mit `DefaultAzureCredential` sucht das Beispiel nach Anmeldeinformationen in Ihrer lokalen Umgebung, wie eine Azure-Anmeldung in einer lokalen [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) oder in Visual Studio oder Visual Studio Code. Aus diesem Grund sollten Sie sich *lokal* über einen dieser Mechanismen bei Azure anmelden, um Anmeldeinformationen für das Beispiel einzurichten.

Wenn Sie Visual Studio oder Visual Studio Code zum Ausführen des Codebeispiels verwenden, stellen Sie sicher, dass Sie bei diesem Editor mit denselben Anmeldeinformationen für Azure angemeldet sind, die Sie für den Zugriff auf Ihre Instanz von Azure Digital Twins verwenden möchten.

Andernfalls können Sie die [lokale Azure CLI installieren](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), eine Eingabeaufforderung auf Ihrem Computer starten und den Befehl `az login` ausführen, um sich bei Ihrem Azure-Konto anzumelden. Wenn Sie nach der Anmeldung Ihr Codebeispiel ausführen, sollte es Sie automatisch anmelden.