---
title: Verwalten von Protokollen und Verschlüsselungen in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Protokolle (TLS, SSL) und Verschlüsselungen (DES) in Azure API Management verwalten.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: f809aaf872607e7fa61368832a3df74318f2a858
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141504"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Verwalten von Protokollen und Verschlüsselungen in Azure API Management

Azure API Management unterstützt mehrere Versionen des TLS-Protokolls für die Client- und Back-End-Seite sowie die 3DES-Verschlüsselung.

Dieser Leitfaden zeigt Ihnen, wie Sie Protokoll- und Verschlüsselungskonfigurationen für eine Azure API Management-Instanz verwalten können.

![Verwalten von Protokollen und Verschlüsselungen in API Management](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie den Schritten in diesem Artikel folgen können, benötigen Sie folgende Komponenten:

* Eine API Management-Instanz

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Verwalten von TLS-Protokollen und 3DES-Verschlüsselungen

1. Navigieren Sie im Azure-Portal zu Ihrer **API Management-Instanz**.
2. Wählen Sie im Menü **Protokolleinstellungen** aus.  
3. Aktivieren bzw. deaktivieren Sie die gewünschten Protokolle oder Verschlüsselungen.
4. Klicken Sie auf **Speichern**. Die Änderungen werden innerhalb einer Stunde angewendet.  

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Hier finden Sie weitere [Videos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) zu API Management.