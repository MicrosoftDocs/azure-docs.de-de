---
title: Verwalten von Firewallregeln – Azure-Portal – Azure Database for MySQL
description: Erstellen und Verwalten von Firewallregeln für Azure-Datenbank für MySQL mithilfe des Azure-Portals
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 032db324f9dd2f0d6a5dce5c4fd5c64342fed59f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540483"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL mithilfe des Azure-Portals
Mithilfe von Firewallregeln auf Serverebene kann der Zugriff auf einen Azure Database for MySQL-Server über eine bestimmte IP-Adresse oder über einen IP-Adressbereich verwaltet werden. 

Virtual Network-Regeln (VNET) können auch verwendet werden, um den Zugriff auf Ihren Server zu sichern. Weitere Informationen finden Sie unter [Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln mithilfe des Azure-Portals](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Erstellen einer Firewallregel auf Serverebene im Azure-Portal

1. Klicken Sie auf der Seite des MySQL-Servers unter der Überschrift „Einstellungen“ auf **Verbindungssicherheit**, um die Seite „Verbindungssicherheit“ für Azure Database for MySQL zu öffnen.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure-Portal – Klicken auf „Verbindungssicherheit“":::

2. Klicken Sie auf der Symbolleiste auf **Meine IP-Adresse hinzufügen**. Dadurch wird automatisch eine Firewallregel mit der öffentlichen IP-Adresse Ihres Computers (gemäß Erkennung durch das Azure-System) erstellt.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure-Portal – Klicken auf „Meine IP-Adresse hinzufügen“":::

3. Überprüfen Sie Ihre IP-Adresse, bevor Sie die Konfiguration speichern. In einigen Situationen weicht die vom Azure-Portal erkannte IP-Adresse von der IP-Adresse ab, die für den Zugriff auf das Internet und die Azure-Server verwendet wird. Aus diesem Grund müssen Sie die Start-IP und die End-IP ändern, damit die Regel wie erwartet funktioniert.

   Verwenden Sie eine Suchmaschine oder ein anderes Onlinetool, um Ihre eigene IP-Adresse zu überprüfen. Suchen Sie in Bing beispielsweise nach „Wie lautet meine IP?“.

4. Fügen Sie weitere Adressräume hinzu. In den Firewallregeln für Azure Database for MySQL können Sie eine einzelne IP-Adresse oder einen Adressbereich angeben. Wenn Sie die Regel auf eine einzelne IP-Adresse beschränken möchten, geben Sie dieselbe Adresse in die Felder „Start-IP“ und „End-IP“ ein. Durch Öffnen der Firewall wird es Administratoren, Benutzern und Anwendungen ermöglicht, auf dem MySQL-Server auf alle Datenbanken zuzugreifen, für die sie über gültige Anmeldeinformationen verfügen.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure-Portal – Firewallregeln":::

5. Klicken Sie auf der Symbolleiste auf **Speichern**, um diese Firewallregel auf Serverebene zu speichern. Warten Sie auf die Bestätigung, dass das Update für die Firewallregeln erfolgreich war.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure-Portal – Klicken auf „Speichern“":::

## <a name="connecting-from-azure"></a>Herstellen einer Verbindung über Azure
Um Anwendungen von Azure die Verbindung mit dem Azure Database for MySQL-Server zu ermöglichen, müssen Azure-Verbindungen ermöglicht werden. Beispiele: Hosten einer Azure-Web-Apps-Anwendung oder einer auf einem virtuellen Azure-Computer ausgeführten Anwendung und Herstellen einer Verbindung über ein Azure Data Factory-Datenverwaltungsgateway. Die Ressourcen müssen sich nicht im gleichen virtuellen Netzwerk (VNET) oder in der gleichen Ressourcengruppe für die Firewallregel befinden, um diese Verbindungen zu ermöglichen. Wenn eine Azure-Anwendung versucht, eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Diese Arten von Verbindungen können auf unterschiedliche Weise ermöglicht werden. Eine Firewalleinstellung, bei der die Start- und Endadresse den Wert 0.0.0.0 aufweist, gibt an, dass diese Verbindungen zulässig sind. Alternativ können Sie im Portal im Bereich **Verbindungssicherheit** die Option **Zugriff auf Azure-Dienste erlauben** auf **EIN** festlegen und auf **Speichern** klicken. Ist der Verbindungsversuch nicht zulässig, erreicht die Anforderung den Azure Database for MySQL-Server nicht.

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall derart, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Verwalten vorhandener Firewallregeln auf Serverebene mithilfe des Azure-Portals
Wiederholen Sie die Schritte zum Verwalten der Firewallregeln.
* Um den aktuellen Computer hinzuzufügen, klicken Sie auf **+ Meine IP-Adresse hinzufügen**. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um zusätzliche IP-Adressen hinzuzufügen, geben Sie **REGELNAME**, **START-IP** und **END-IP** ein. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um eine vorhandene Regel zu ändern, klicken Sie auf eines der Felder in der Regel, und ändern Sie den betreffenden Wert. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um eine vorhandene Regel zu löschen, klicken Sie auf die Auslassungspunkte [...] und dann auf **Löschen**. Klicken Sie zum Speichern der Änderungen auf **Speichern**.


## <a name="next-steps"></a>Nächste Schritte
- Sie können auch ein Skript zum [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL mithilfe der Azure CLI](howto-manage-firewall-using-cli.md) schreiben.
- Sichern Sie den Zugriff auf Ihren Server noch mehr, indem Sie [VNET-Dienstendpunkte und -Regeln für Azure Database for PostgreSQL mithilfe des Azure-Portals erstellen und verwalten](howto-manage-vnet-using-portal.md).
- Wenn Sie Unterstützung beim Herstellen einer Verbindung mit einem Server für Azure Database for MySQL benötigen, lesen Sie die Informationen unter [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md) (Verbindungsbibliotheken für Azure Database for MySQL).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
