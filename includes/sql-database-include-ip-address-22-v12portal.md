---
title: Firewallregeln auf Serverebene
description: Firewallregeln auf Serverebene
keywords: sql connection,connection string
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178170"
---
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie in der Liste links **Alle Dienste** aus.

3. Scrollen Sie und wählen Sie **SQL-Server** aus.

    ![Suchen Sie Ihren Azure SQL-Datenbank-Server im Portal][b21-FindServerInPortal]
5. Geben Sie im Filtertextfeld zunächst den Namen des Servers ein. Die Zeile wird angezeigt.

6. Wählen Sie die Zeile für den Server aus. Ein Blatt für den Server wird angezeigt.

7. Wählen Sie auf dem Serverblatt **Einstellungen** aus.

8. Wählen Sie **Firewall** aus.

    ![Wählen Sie „Einstellungen > Firewall“ aus.][b31-SettingsFirewallNavig]
9. Wählen Sie **Client-IP hinzufügen** aus. Geben Sie einen Namen für die neue Regel in das erste Textfeld ein.

10. Geben Sie die niedrigen und hohen IP-Adresswerte für den Bereich ein, den Sie aktivieren möchten.

    * Es kann praktisch sein, wenn der niedrige Wert auf **.0** und der hohe Wert auf **.255** endet.

11. Wählen Sie **Speichern** aus.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
