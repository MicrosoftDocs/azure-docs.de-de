---
title: Verwalten von Firewallregeln – Azure-Portal – Azure Database for MariaDB
description: Erstellen und Verwalten von Firewallregeln für Azure Database for MariaDB mithilfe des Microsoft Azure-Portals
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 895c8e51e16cb0b82ebf3edcf954dabe62b08ff4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021774"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Erstellen und Verwalten von Firewallregeln für Azure Database for MariaDB mithilfe des Microsoft Azure-Portals
Mithilfe von Firewallregeln auf Serverebene kann der Zugriff auf einen Azure Database for MariaDB-Server über eine bestimmte IP-Adresse oder über einen IP-Adressbereich verwaltet werden.

Virtual Network-Regeln (VNET) können auch verwendet werden, um den Zugriff auf Ihren Server zu sichern. Weitere Informationen finden Sie unter [Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln mithilfe des Azure-Portals](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Erstellen einer Firewallregel auf Serverebene im Azure-Portal

1. Klicken Sie auf der Seite des MariaDB-Servers unter der Überschrift „Einstellungen“ auf **Verbindungssicherheit**, um die Seite „Verbindungssicherheit“ für Azure Database for MariaDB zu öffnen.

   ![Azure-Portal – Klicken auf „Verbindungssicherheit“](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Klicken Sie auf der Symbolleiste auf **Meine IP-Adresse hinzufügen**. Dadurch wird automatisch eine Firewallregel mit der öffentlichen IP-Adresse Ihres Computers (gemäß Erkennung durch das Azure-System) erstellt.

   ![Azure-Portal – Klicken auf „Meine IP-Adresse hinzufügen“](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Überprüfen Sie Ihre IP-Adresse, bevor Sie die Konfiguration speichern. In einigen Situationen weicht die vom Azure-Portal erkannte IP-Adresse von der IP-Adresse ab, die für den Zugriff auf das Internet und die Azure-Server verwendet wird. Aus diesem Grund müssen Sie die Start-IP und die End-IP ändern, damit die Regel wie erwartet funktioniert.

   Verwenden Sie eine Suchmaschine oder ein anderes Onlinetool, um Ihre eigene IP-Adresse zu überprüfen. Suchen Sie in Bing beispielsweise nach „Wie lautet meine IP?“.

4. Fügen Sie weitere Adressräume hinzu. In den Firewallregeln für Azure Database for MariaDB können Sie eine einzelne IP-Adresse oder einen Adressbereich angeben. Wenn Sie die Regel auf eine einzelne IP-Adresse beschränken möchten, geben Sie dieselbe Adresse in die Felder „Start-IP“ und „End-IP“ ein. Durch Öffnen der Firewall wird es Administratoren, Benutzern und Anwendungen ermöglicht, auf dem MariaDB-Server auf alle Datenbanken zuzugreifen, für die sie über gültige Anmeldeinformationen verfügen.

   ![Azure-Portal – Firewallregeln](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Klicken Sie auf der Symbolleiste auf **Speichern**, um diese Firewallregel auf Serverebene zu speichern. Warten Sie auf die Bestätigung, dass das Update für die Firewallregeln erfolgreich war.

   ![Azure-Portal – Klicken auf „Speichern“](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Herstellen einer Verbindung über Azure
Um Anwendungen von Azure die Verbindung mit dem Azure Database for MariaDB-Server zu ermöglichen, müssen Azure-Verbindungen ermöglicht werden. Beispiele: Hosten einer Azure-Web-Apps-Anwendung oder einer auf einem virtuellen Azure-Computer ausgeführten Anwendung und Herstellen einer Verbindung über ein Azure Data Factory-Datenverwaltungsgateway. Die Ressourcen müssen sich nicht im gleichen virtuellen Netzwerk (VNET) oder in der gleichen Ressourcengruppe für die Firewallregel befinden, um diese Verbindungen zu ermöglichen. Wenn eine Azure-Anwendung versucht, eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Diese Arten von Verbindungen können auf unterschiedliche Weise ermöglicht werden. Eine Firewalleinstellung, bei der die Start- und Endadresse den Wert 0.0.0.0 aufweist, gibt an, dass diese Verbindungen zulässig sind. Alternativ können Sie im Portal im Bereich **Verbindungssicherheit** die Option **Zugriff auf Azure-Dienste erlauben** auf **EIN** festlegen und dann auf **Speichern** klicken. Ist der Verbindungsversuch nicht zulässig, erreicht die Anforderung den Azure Database for MariaDB-Server nicht.

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall derart, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Verwalten vorhandener Firewallregeln im Azure-Portal
Wiederholen Sie die Schritte zum Verwalten der Firewallregeln.
* Um den aktuellen Computer hinzuzufügen, klicken Sie auf **+ Meine IP-Adresse hinzufügen**. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um zusätzliche IP-Adressen hinzuzufügen, geben Sie **REGELNAME**, **START-IP** und **END-IP** ein. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um eine vorhandene Regel zu ändern, klicken Sie auf eines der Felder in der Regel, und ändern Sie den betreffenden Wert. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um eine vorhandene Regel zu löschen, klicken Sie auf die Auslassungspunkte [...] und dann auf **Löschen**. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte
 - Sie können auch ein Skript zum [Erstellen und Verwalten von Firewallregeln für Azure Database for MariaDB mithilfe der Azure CLI](howto-manage-firewall-cli.md) schreiben.
 - Sichern Sie den Zugriff auf Ihren Server noch mehr, indem Sie [VNET-Dienstendpunkte und -Regeln für Azure Database for PostgreSQL mithilfe des Azure-Portals erstellen und verwalten](howto-manage-vnet-portal.md).