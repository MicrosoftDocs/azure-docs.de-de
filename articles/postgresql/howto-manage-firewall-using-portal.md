---
title: Verwalten von Firewallregeln – Azure-Portal – Azure Database for PostgreSQL – Einzelserver
description: Erstellen und Verwalten von Firewallregeln für Azure Database for PostgreSQL (Einzelserver) im Azure-Portal
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 02bd4927216b6b60d2720e6f32c5768499e310bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022199"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Erstellen und Verwalten von Firewallregeln für Azure Database for PostgreSQL (Einzelserver) im Azure-Portal
Mithilfe von Firewallregeln auf Serverebene kann der Zugriff auf einen Server für Azure-Datenbank für PostgreSQL über eine bestimmte IP-Adresse oder über einen IP-Adressbereich verwaltet werden.

Virtual Network-Regeln (VNET) können auch verwendet werden, um den Zugriff auf Ihren Server zu sichern. Weitere Informationen finden Sie unter [Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln mithilfe des Azure-Portals](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- Einen Server zum [Erstellen einer Azure-Datenbank für PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Erstellen einer Firewallregel auf Serverebene im Azure-Portal
1. Klicken Sie auf der Seite des PostgreSQL-Servers unter der Überschrift „Einstellungen“ auf **Verbindungssicherheit**, um die Seite „Verbindungssicherheit“ für Azure Database for PostgreSQL zu öffnen.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure-Portal – Klicken auf „Verbindungssicherheit“":::

2. Klicken Sie auf der Symbolleiste auf **Meine IP-Adresse hinzufügen**. Dadurch wird automatisch eine Firewallregel mit der öffentlichen IP-Adresse Ihres Computers (gemäß Erkennung durch das Azure-System) erstellt.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure-Portal – Klicken auf „Meine IP-Adresse hinzufügen“":::

3. Überprüfen Sie Ihre IP-Adresse, bevor Sie die Konfiguration speichern. In einigen Situationen weicht die vom Azure-Portal erkannte IP-Adresse von der IP-Adresse ab, die für den Zugriff auf das Internet und die Azure-Server verwendet wird. Aus diesem Grund müssen Sie die Start-IP und die End-IP ändern, damit die Regel wie erwartet funktioniert.
   Verwenden Sie eine Suchmaschine oder ein anderes Onlinetool, um Ihre eigene IP-Adresse zu überprüfen. Suchen Sie beispielsweise nach „Wie lautet meine IP?“.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Bing-Suche für „Wie lautet meine IP?“":::

4. Fügen Sie weitere Adressräume hinzu. In den Firewallregeln für Azure Database for PostgreSQL können Sie eine einzelne IP-Adresse oder einen Adressbereich angeben. Wenn Sie die Regel auf eine einzelne IP-Adresse beschränken möchten, geben Sie dieselbe Adresse in das Feld für Start-IP und End-IP ein. Mit dem Öffnen der Firewall wird es Administratoren, Benutzern und Anwendungen ermöglicht, auf dem PostgreSQL-Server auf alle Datenbanken zuzugreifen, für die sie über gültige Anmeldeinformationen verfügen.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure-Portal – Firewallregeln":::

5. Klicken Sie auf der Symbolleiste auf **Speichern**, um diese Firewallregel auf Serverebene zu speichern. Warten Sie auf die Bestätigung, dass das Update für die Firewallregeln erfolgreich war.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure-Portal – Klicken auf „Speichern“":::

## <a name="connecting-from-azure"></a>Herstellen einer Verbindung über Azure
Um Anwendungen von Azure die Verbindung mit dem Azure Database for PostgreSQL-Server zu ermöglichen, müssen Azure-Verbindungen ermöglicht werden. Beispiele: Hosten einer Azure-Web-Apps-Anwendung oder einer auf einem virtuellen Azure-Computer ausgeführten Anwendung und Herstellen einer Verbindung über ein Azure Data Factory-Datenverwaltungsgateway. Die Ressourcen müssen sich nicht im gleichen virtuellen Netzwerk (VNET) oder in der gleichen Ressourcengruppe für die Firewallregel befinden, um diese Verbindungen zu ermöglichen. Wenn eine Azure-Anwendung versucht, eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Diese Arten von Verbindungen können auf unterschiedliche Weise ermöglicht werden. Eine Firewalleinstellung, bei der die Start- und Endadresse den Wert 0.0.0.0 aufweist, gibt an, dass diese Verbindungen zulässig sind. Alternativ können Sie im Portal im Bereich **Verbindungssicherheit** die Option **Zugriff auf Azure-Dienste erlauben** auf **EIN** festlegen und auf **Speichern** klicken. Ist der Verbindungsversuch nicht zulässig, erreicht die Anforderung den Azure Database for PostgreSQL-Server nicht.

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall derart, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Verwalten vorhandener Firewallregeln auf Serverebene über das Azure-Portal
Wiederholen Sie die Schritte zum Verwalten der Firewallregeln.
* Um den aktuellen Computer hinzuzufügen, klicken Sie auf die Schaltfläche **+ Meine IP-Adresse hinzufügen**. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um zusätzliche IP-Adressen hinzuzufügen, geben Sie den Regelnamen, die Start-IP-Adresse und die End-IP-Adresse ein. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um eine vorhandene Regel zu ändern, klicken Sie auf eines der Felder in der Regel, und ändern Sie den betreffenden Wert. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um eine vorhandene Regel zu löschen, klicken Sie auf die Auslassungspunkte [...] und dann auf **Löschen**. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte
- Sie können auch ein Skript zum [Erstellen und Verwalten von Firewallregeln für Azure Database for PostgreSQL mithilfe der Azure CLI](howto-manage-firewall-using-cli.md) schreiben.
- Sichern Sie den Zugriff auf Ihren Server noch mehr, indem Sie [VNET-Dienstendpunkte und -Regeln für Azure Database for PostgreSQL mithilfe des Azure-Portals erstellen und verwalten](howto-manage-vnet-using-portal.md).
- Wenn Sie Unterstützung beim Herstellen einer Verbindung mit einem Server für Azure Database for PostgreSQL benötigen, lesen Sie die Informationen unter [Datenverbindungsbibliotheken für Azure Database for PostgreSQL](concepts-connection-libraries.md).
