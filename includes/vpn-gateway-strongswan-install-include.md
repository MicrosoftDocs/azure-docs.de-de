---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025950"
---
Die folgende Konfiguration wurde für die unten stehenden Schritte verwendet:

- Computer: Ubuntu Server 18.04
- Abhängigkeiten: strongSwan


Verwenden Sie die folgenden Befehle, um die erforderliche strongSwan-Konfiguration zu installieren:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Verwenden Sie den folgenden Befehl, um die Azure-Befehlszeilenschnittstelle zu installieren:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Weitere Anweisungen zum Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)