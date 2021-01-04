---
title: Sichern und Wiederherstellen – Azure-Portal – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie mit dem Azure-Portal einen Server in Azure-Datenbank für MySQL wiederherstellen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 9bc31cf8fee2669634ff366caac77cb090baf075
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000299"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Sichern und Wiederherstellen eines Servers in Azure Database for MySQL mit dem Azure-Portal

## <a name="backup-happens-automatically"></a>Automatische Sicherung
Azure Database for MySQL-Server werden regelmäßig gesichert, um Wiederherstellungsfunktionen zu ermöglichen. Mithilfe dieses Features können Sie für den Server und alle dazugehörigen Datenbanken einen Zustand zu einem früheren Zeitpunkt auf einem neuen Server wiederherstellen.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:
- Einen [Azure Database for MySQL-Server und eine Datenbank](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Festlegen der Sicherungskonfiguration

Bei der Erstellung des Servers treffen Sie im Fenster **Tarif** die Entscheidung, ob dafür lokal redundante oder georedundante Sicherungen erstellt werden sollen.

> [!NOTE]
> Nachdem ein Server erstellt wurde, kann die Redundanzart (georedundant oder lokal redundant) nicht mehr gewechselt werden.
>

Bei der Erstellung eines Servers über das Azure-Portal wählen Sie im Fenster **Tarif** entweder **Lokal redundant** oder **Georedundant** für die Sicherungen Ihres Servers aus. Außerdem wählen Sie in diesem Fenster die **Aufbewahrungszeit für Sicherung** aus. Hier wird angegeben, wie lange die Sicherungen gespeichert werden sollen (in Tagen).

   :::image type="content" source="./media/howto-restore-server-portal/pricing-tier.png" alt-text="Tarif – Auswählen der Sicherungsredundanz":::

Weitere Informationen zum Festlegen dieser Werte während der Erstellung finden Sie unter [Erstellen eines Servers für Azure Database for MySQL über das Azure-Portal](quickstart-create-mysql-server-database-using-azure-portal.md).

Die Option „Aufbewahrungszeit für Sicherung“ kann auf einem Server mit den folgenden Schritten geändert werden:
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie Ihren Server für Azure Database for MySQL aus. Mit dieser Aktion wird die Seite **Übersicht** geöffnet.
3. Wählen Sie im Menü unter **EINSTELLUNGEN** die Option **Tarif**. Mit dem Schieberegler können Sie die **Aufbewahrungszeit für Sicherung** auf einen Wert zwischen 7 und 35 Tagen festlegen.
Im folgenden Screenshot wurde der Wert auf 34 Tage erhöht.
:::image type="content" source="./media/howto-restore-server-portal/3-increase-backup-days.png" alt-text="Erhöhung der Aufbewahrungszeit für Sicherung":::

4. Klicken Sie auf **OK**, um die Änderung zu bestätigen.

Mit „Aufbewahrungszeit für Sicherung“ wird auch gesteuert, für welchen zurückliegenden Zeitraum eine Point-in-Time-Wiederherstellung durchgeführt werden kann, da dies auf den verfügbaren Sicherungen basiert. Die Point-in-Time-Wiederherstellung wird im folgenden Abschnitt näher beschrieben. 

## <a name="point-in-time-restore"></a>Wiederherstellung bis zu einem bestimmten Zeitpunkt
Mit Azure Database for MySQL können Sie den Zustand des Servers zu einem bestimmten Zeitpunkt und auf einer neuen Kopie des Servers wiederherstellen. Sie können diesen neuen Server verwenden, um Ihre Daten wiederherzustellen, oder Ihre Clientanwendungen so einrichten, dass sie auf diesen neuen Server verweisen.

Beispiel: Wenn eine Tabelle heute um 12 Uhr versehentlich gelöscht wurde, können Sie den Zustand von kurz vor 12 Uhr wiederherstellen und die fehlende Tabelle und die Daten von dieser neuen Kopie des Servers abrufen. Die Point-in-Time-Wiederherstellung wird nicht auf Datenbankebene, sondern auf der Serverebene durchgeführt.

Mithilfe der folgenden Schritte wird der Status des Beispielservers zu einem bestimmten Zeitpunkt wiederhergestellt:
1. Wählen Sie im Azure-Portal Ihren Azure Database for MySQL-Server aus. 

2. Klicken Sie in der Symbolleiste auf der Seite **Übersicht** des Servers auf **Wiederherstellen**.

   :::image type="content" source="./media/howto-restore-server-portal/2-server.png" alt-text="Azure Database for MySQL – Übersicht – Schaltfläche „Wiederherstellen“":::

3. Geben Sie im Formular „Wiederherstellen“ die erforderlichen Informationen ein:

   :::image type="content" source="./media/howto-restore-server-portal/3-restore.png" alt-text="Azure Database for MySQL – Wiederherstellungsinformationen":::
   - **Wiederherstellungspunkt**: Wählen Sie den Zeitpunkt aus, für den der Zustand wiederhergestellt werden soll.
   - **Zielserver**: Geben Sie einen Namen für den neuen Server an.
   - **Standort**: Sie können die Region nicht auswählen. Standardmäßig ist dieser Wert mit dem Wert für den Quellserver identisch.
   - **Tarif:** Sie können diese Parameter nicht ändern, wenn Sie eine Point-in-Time-Wiederherstellung durchführen. Er ist mit dem Wert für den Quellserver identisch. 

4. Klicken Sie auf **OK**, um den Zustand eines Servers zu einem bestimmten Zeitpunkt wiederherzustellen. 

5. Suchen Sie nach Abschluss der Wiederherstellung den neuen erstellten Server, um zu überprüfen, ob die Daten wie erwartet wiederhergestellt wurden.

Der neue Server, der durch die Point-in-Time-Wiederherstellung erstellt wurde, verfügt über den gleichen Serveradministrator-Anmeldenamen (und das dazugehörige Kennwort), der für den vorhandenen Server zum gewählten Zeitpunkt gültig war. Sie können das Kennwort auf der Seite **Übersicht** des neuen Servers ändern.

Zusätzlich gibt es nach Abschluss des Wiederherstellungsvorgangs zwei Serverparameter, die nach dem Wiederherstellungsvorgang auf Standardwerte zurückgesetzt werden (und nicht vom primären Server übernommen werden)
*   time_zone: Dieser Wert wird auf den DEFAULT-Wert **SYSTEM** festgelegt.
*   event_scheduler: Dieser Wert wird auf dem wiederhergestellten Server auf **OFF** festgelegt.

Sie müssen den Wert vom primären Server kopieren und ihn auf dem wiederhergestellten Server festlegen, indem Sie den [Serverparameter](howto-server-parameters.md) erneut konfigurieren.

Der neue Server, der während einer Wiederherstellung erstellt wird, weist nicht die VNET-Dienstendpunkte auf, die auf dem ursprünglichen Server vorhanden waren. Diese Regeln müssen separat für diesen neuen Server eingerichtet werden. Firewallregeln vom ursprünglichen Server werden wiederhergestellt.

## <a name="geo-restore"></a>Geowiederherstellung
Wenn Sie Ihren Server für georedundante Sicherungen konfiguriert haben, kann aus der Sicherung dieses vorhandenen Servers ein neuer Server erstellt werden. Dieser neue Server kann in allen Regionen erstellt werden, in denen Azure Database for MySQL verfügbar ist.  

1. Klicken Sie in der linken oberen Ecke des Portals auf die Schaltfläche **Ressource erstellen** (+). Wählen Sie **Datenbanken** > **Azure-Datenbank für MySQL** aus.

   :::image type="content" source="./media/howto-restore-server-portal/1_navigate-to-mysql.png" alt-text="Navigieren zu Azure Database for MySQL":::
 
2. Geben Sie das Abonnement, die Ressourcengruppe und den Namen des neuen Servers an. 

3. Wählen Sie unter **Datenquelle** die Option **Sicherung** aus. Dadurch wird eine Dropdownliste mit Servern geladen, für die georedundante Sicherungen aktiviert sind.
   
   :::image type="content" source="./media/howto-restore-server-portal/3-geo-restore.png" alt-text="Auswählen der Datenquelle":::
    
   > [!NOTE]
   > Für einen neu erstellten Server kann möglicherweise nicht sofort eine Geowiederherstellung durchgeführt werden. Es kann einige Stunden dauern, bis die erforderlichen Metadaten aufgefüllt wurden.
   >

4. Wählen Sie die Dropdownliste **Sicherung** aus.
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore-backup.png" alt-text="Auswählen der Dropdownliste „Sicherung“":::

5. Wählen Sie den Quellserver für die Wiederherstellung aus.
   
   :::image type="content" source="./media/howto-restore-server-portal/5-select-backup.png" alt-text="Auswählen der Sicherung":::

6. Für den Server werden Standardwerte für die **Anzahl virtueller Kerne**, den **Aufbewahrungszeitraum für Sicherungen**, die **Option für Sicherungsredundanz**, die **Engine-Version** und die **Administrator-Anmeldeinformationen** verwendet. Wählen Sie **Weiter**. 
   
   :::image type="content" source="./media/howto-restore-server-portal/6-accept-backup.png" alt-text="Fortfahren mit Sicherung":::

7. Geben Sie im Rest des Formulars Ihre bevorzugten Einstellungen an. Sie können einen beliebigen **Standort** auswählen.

    Nach der Wahl des Standorts können Sie **Server konfigurieren** auswählen, um die **Computegeneration** (sofern in der ausgewählten Region verfügbar), die Anzahl von **virtuellen Kernen**, den **Aufbewahrungszeitraum für Sicherungen** und die **Option für Sicherungsredundanz** zu aktualisieren. Das Ändern des **Tarifs** („Basic“, „Allgemein“ oder „Arbeitsspeicheroptimiert“) oder der Größe des **Speichers** während der Wiederherstellung wird nicht unterstützt.

   :::image type="content" source="./media/howto-restore-server-portal/7-create.png" alt-text="Ausfüllen des Formulars"::: 

8. Wählen Sie **Überprüfen + erstellen** aus, um ihre Auswahl zu überprüfen. 

9. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Dieser Vorgang kann einige Minuten dauern.

Der neue Server, der durch die Geowiederherstellung erstellt wurde, verfügt über den gleichen Serveradministrator-Anmeldenamen (und das dazugehörige Kennwort), der für den vorhandenen Server bei der Initiierung der Wiederherstellung gültig war. Sie können das Kennwort auf der Seite **Übersicht** des neuen Servers ändern.

Der neue Server, der während einer Wiederherstellung erstellt wird, weist nicht die VNET-Dienstendpunkte auf, die auf dem ursprünglichen Server vorhanden waren. Diese Regeln müssen separat für diesen neuen Server eingerichtet werden. Firewallregeln vom ursprünglichen Server werden wiederhergestellt.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu den [Sicherungen](concepts-backup.md)
- Weitere Informationen zu [Replikaten](concepts-read-replicas.md)
- Weitere Informationen zu den Optionen für [Geschäftskontinuität](concepts-business-continuity.md)