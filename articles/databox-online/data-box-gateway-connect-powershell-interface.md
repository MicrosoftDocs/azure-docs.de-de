---
title: Herstellen einer Verbindung mit und Verwalten von Microsoft Azure Data Box Gateway-Geräten über die Windows PowerShell-Schnittstelle | Microsoft-Dokumentation
description: Beschreibt, wie Sie den Data Box Gateway über die Windows PowerShell-Schnittstelle verbinden und dann verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64415158"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Verwalten eines Azure Data Box Gateway-Geräts mittels Windows PowerShell

Mit der Azure Data Box Gateway-Lösung können Sie Daten über das Netzwerk an Azure senden. In diesem Artikel werden einige der Konfigurations- und Verwaltungsaufgaben für Ihr Data Box Gateway-Gerät beschrieben. Sie können das Azure-Portal, die lokale Webbenutzeroberfläche oder die Windows PowerShell-Schnittstelle verwenden, um Ihr Gerät zu verwalten.

Der Schwerpunkt dieses Artikels liegt auf den Aufgaben, die mithilfe der PowerShell-Schnittstelle ausgeführt werden.

Dieser Artikel enthält die folgenden Verfahren:

- Herstellen einer Verbindung mit der PowerShell-Schnittstelle
- Starten einer Supportsitzung
- Unterstützungspaket erstellen
- Hochladen des Zertifikats
- Starten in einer Nicht-DHCP-Umgebung
- Anzeigen von Geräteinformationen

## <a name="connect-to-the-powershell-interface"></a>Herstellen einer Verbindung mit der PowerShell-Schnittstelle

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Unterstützungspaket erstellen

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Hochladen des Zertifikats

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Starten in einer Nicht-DHCP-Umgebung

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Anzeigen von Geräteinformationen

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Nächste Schritte

- Stellen Sie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) im Azure-Portal bereit.
