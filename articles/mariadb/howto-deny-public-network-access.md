---
title: Zugriff auf öffentliches Netzwerk verweigern – Azure-Portal – Azure Database for MariaDB
description: Erfahren Sie mehr über das Konfigurieren des Verweigerns des Zugriffs auf öffentliche Netzwerke für Ihre Azure Database for MariaDB-Instanz im Azure-Portal.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 3117ebfd258c72bb97432871c2ea74d30c52f669
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242189"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>„Zugriff auf öffentliches Netzwerk verweigern“ in Azure Database for MariaDB im Azure-Portal

In diesem Artikel wird beschrieben, wie Sie eine Azure Database for MariaDB-Instanz so konfigurieren können, dass alle öffentlichen Konfigurationen verweigert und nur Verbindungen über private Endpunkte zugelassen werden, um die Netzwerksicherheit weiter zu erhöhen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

* Eine [Azure Database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)-Instanz

## <a name="set-deny-public-network-access"></a>Festlegen von „Zugriff auf öffentliches Netzwerk verweigern“

Gehen Sie wie folgt vor, um „Zugriff auf öffentliches Netzwerk verweigern“ für den MariaDB-Server zu aktivieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren vorhandenen Azure Database for MariaDB-Server aus.

1. Klicken Sie auf der Seite des MariaDB-Servers unter **Einstellungen** auf **Verbindungssicherheit** , um die Seite zur Konfiguration der Verbindungssicherheit zu öffnen.

1. Wählen Sie in „Zugriff auf öffentliches Netzwerk verweigern“ die Option **Ja** aus, um den öffentlichen Zugriff auf Ihren MariaDB-Server zu verweigern.

    ![Azure Database for MariaDB: Netzwerkzugriff verweigern](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

1. Eine Benachrichtigung bestätigt, dass die Verbindungssicherheitseinstellung erfolgreich aktiviert wurde.

    ![Azure Database for MariaDB: Netzwerkzugriff erfolgreich verweigert](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Warnungen zu Metriken erstellen](howto-alert-metric.md).