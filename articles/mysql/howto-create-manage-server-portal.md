---
title: Erstellen und Verwalten eines Servers mit Azure-Datenbank für MySQL im Azure-Portal
description: In diesem Artikel wird beschrieben, wie Sie schnell einen neuen Server mit Azure-Datenbank für MySQL erstellen und mithilfe des Azure-Portals verwalten.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 6d6f24475497382dd9e04d3335fb89d6f0bdd514
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459517"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Erstellen und Verwalten eines Servers mit Azure-Datenbank für MySQL im Azure-Portal
In diesem Artikel wird beschrieben, wie Sie auf schnelle Weise einen neuen Server für eine Azure Database for MySQL erstellen können. Außerdem erhalten Sie Informationen zum Verwalten des Servers mithilfe des Azure-Portals. Die Serververwaltung umfasst das Anzeigen von Serverdetails und Datenbanken, das Zurücksetzen des Kennworts, das Skalieren von Ressourcen und das Löschen des Servers.

## <a name="log-in-to-the-azure-portal"></a>Anmelden beim Azure-Portal
Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.

## <a name="create-an-azure-database-for-mysql-server"></a>Erstellen eines Servers für Azure-Datenbank für MySQL
Führen Sie die folgenden Schritte aus, um einen Azure Database for MySQL-Server mit dem Namen „mydemoserver“ zu erstellen.

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf die Schaltfläche **Ressource erstellen**.

2. Wählen Sie auf der Seite „Neu“ die Option **Datenbanken** und anschließend auf der Seite „Datenbanken“ die Option **Azure Database for MySQL** aus.

    > Ein Server mit Azure-Datenbank für MySQL wird mit einer definierten Gruppe von [Compute- und Speicherressourcen](./concepts-pricing-tiers.md) erstellt. Die Datenbank wird in einer Azure-Ressourcengruppe und auf einem Server mit Azure-Datenbank für MySQL erstellt.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Tragen Sie folgende Informationen in das Formular „Azure Database for MySQL“ ein:

    | **Formularfeld** | **Feldbeschreibung** |
    |----------------|-----------------------|
    | *Servername* | mydemoserver (Servername ist global eindeutig) |
    | *Abonnement* | mysubscription (im Dropdownmenü auswählen) |
    | *Ressourcengruppe* | myresourcegroup (neue Ressourcengruppe erstellen oder eine bereits vorhandene verwenden) |
    | *Quelle auswählen* | Blank (einen leeren MySQL-Server erstellen) |
    | *Serveradministratoranmeldung* | myadmin (Administratorkontoname einrichten) |
    | *Kennwort* | Kennwort des Administratorkontos festlegen |
    | *Kennwort bestätigen* | Kennwort des Administratorkontos bestätigen |
    | *Location* | Asien, Südosten (zwischen „Europa, Norden“ und „USA, Westen“ auswählen) |
    | *Version* | 5.7 (Serverversion für Azure Database for MySQL auswählen) |

4. Klicken Sie auf **Tarif**, um die Dienstebene und die Leistungsstufe für Ihren neuen Server anzugeben. Wählen Sie die Registerkarte **Allgemein** aus. *Gen 5*, *2 virtuelle Kerne*, *5 GB* und *7 Tage* sind die Standardwerte für **Computegeneration**, **Virtuelle Kerne**, **Speicher** und **Aufbewahrungszeit für Sicherung**. Sie können diese Schieberegler unverändert lassen. Wählen Sie zum Aktivieren der Serversicherungen in georedundantem Speicher unter **Optionen für Sicherungsredundanz** die Option **Georedundant** aus.

   ![Servertarif erstellen](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Klicken Sie auf **Erstellen**, um den Server bereitzustellen. Die Bereitstellung dauert einige Minuten.

    > Wählen Sie die Option **An das Dashboard anheften** aus, um das leichte Nachverfolgen Ihrer Bereitstellungen zu ermöglichen.

## <a name="update-an-azure-database-for-mysql-server"></a>Aktualisieren eines Servers mit Azure-Datenbank für MySQL
Nach der Bereitstellung des neuen Servers stehen dem Benutzer mehrere Optionen zum Konfigurieren des vorhandenen Servers zur Verfügung. Hierzu zählen beispielsweise das Zurücksetzen des Administratorkennworts und das zentrale Hoch- oder Herunterskalieren des Servers durch Ändern des virtuellen Kerns oder des Speichers.

### <a name="change-the-administrator-user-password"></a>Ändern des Administratorbenutzerkennworts
1. Klicken Sie auf dem Serverblatt **Übersicht** auf **Kennwort zurücksetzen**, um das Fenster für die Kennwortzurücksetzung anzuzeigen.

   ![Übersicht](./media/howto-create-manage-server-portal/overview.png)

2. Geben Sie ein neues Kennwort ein, und bestätigen Sie das Kennwort wie unten gezeigt im Fenster:

   ![Kennwort zurücksetzen](./media/howto-create-manage-server-portal/reset-password.png)

3. Klicken Sie auf **OK**, um das neue Kennwort zu speichern.

### <a name="scale-vcores-updown"></a>Zentrales Hoch-/Herunterskalieren von virtuellen Kernen

1. Klicken Sie unter **Einstellungen** auf **Tarif**.

2. Ändern Sie die Einstellung **Virtuelle Kerne**, indem Sie den Schieberegler auf den gewünschten Wert ziehen.

    ![Skalieren von Computeressourcen](./media/howto-create-manage-server-portal/scale-compute.png)

3. Klicken Sie zum Speichern der Änderungen auf **OK**.

### <a name="scale-storage-up"></a>Zentrales Hochskalieren des Speichers

1. Klicken Sie unter **Einstellungen** auf **Tarif**.

2. Ändern Sie die Einstellung **Speicher**, indem Sie den Schieberegler auf den gewünschten Wert ziehen.

    ![Skalieren des Speichers](./media/howto-create-manage-server-portal/scale-storage.png)

3. Klicken Sie zum Speichern der Änderungen auf **OK**.

## <a name="delete-an-azure-database-for-mysql-server"></a>Löschen eines Servers mit Azure-Datenbank für MySQL

1. Klicken Sie auf dem Serverblatt **Übersicht** auf die Schaltfläche **Löschen**, um die Bestätigungsaufforderung zum Löschen zu öffnen.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. Geben Sie den Namen des Servers in das Eingabefeld für die doppelte Bestätigung ein.

    ![Löschen bestätigen](./media/howto-create-manage-server-portal/confirm.png)

3. Klicken Sie auf die Schaltfläche **Löschen**, um das Löschen des Servers zu bestätigen. Warten Sie, bis auf der Benachrichtigungsleiste das Popup „MySQL-Server erfolgreich gelöscht“ angezeigt wird.

## <a name="list-the-azure-database-for-mysql-databases"></a>Auflisten der Datenbanken von Azure-Datenbank für MySQL
Scrollen Sie auf dem Serverblatt **Übersicht** nach unten, bis die Datenbankkachel im unteren Bereich angezeigt wird. In der Tabelle werden alle auf dem Server vorhandenen Datenbanken aufgeführt.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Anzeigen von Details zu einem Server mit Azure-Datenbank für MySQL
Klicken Sie unter **Einstellungen** auf **Eigenschaften**, um detaillierte Informationen zum Server anzuzeigen.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Erstellen eines Azure Database for MySQL-Servers mithilfe des Azure-Portals](./quickstart-create-mysql-server-database-using-azure-portal.md)