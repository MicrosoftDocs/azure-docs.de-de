---
title: Konfigurieren Sie fortlaufende Sicherungen und Zeitpunktwiederherstellungen über das Azure-Portal in Azure Cosmos DB.
description: Hier erfahren Sie, wie Sie den Wiederherstellungspunkt identifizieren und die fortlaufende Sicherung über das Azure-Portal konfigurieren können. Es wird gezeigt, wie ein aktives und gelöschtes Konto wiederhergestellt wird.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e0fb337defc69354aa625fe63bef9768dbc4cf1f
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538844"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>Konfigurieren und Verwalten von fortlaufender Sicherung und Zeitpunktwiederherstellung (Vorschau) – Verwenden des Azure-Portals
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Das Feature „Zeitpunktwiederherstellung“ (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich zurzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mithilfe des Azure Cosmos DB-Features „Zeitpunktwiederherstellung“ (Vorschau) können Sie nach einer versehentlichen Änderung in einem Container ein gelöschtes Konto, eine Datenbank oder einen Container wiederherstellen oder aber eine Wiederherstellung in einer beliebigen Region (in der es Sicherungen gab) durchführen. Der fortlaufende Sicherungsmodus ermöglicht Ihnen die Wiederherstellung auf jeden beliebigen Zeitpunkt innerhalb der letzten 30 Tage.

In diesem Artikel wird beschrieben, wie Sie den Wiederherstellungspunkt identifizieren und die fortlaufende Sicherung über das Azure-Portal konfigurieren können.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Bereitstellen eines Kontos mit fortlaufender Sicherung

Wählen Sie beim Erstellen eines neuen Azure Cosmos DB-Kontos für die Option **Sicherungsrichtlinie** den Modus **Fortlaufend** aus, um die Funktion „Zeitpunktwiederherstellung“ für das neue Konto zu aktivieren. Nachdem dieses Feature für das Konto aktiviert wurde, stehen alle Datenbanken und Container für die fortlaufende Sicherung zur Verfügung. Bei der Zeitpunktwiederherstellung werden Daten immer in einem neuen Konto wiederhergestellt. Zurzeit können Sie keine Daten in einem vorhandenen Konto wiederherstellen.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Bereitstellen eines Azure Cosmos DB-Kontos mit fortlaufender Sicherung." border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Wiederherstellen eines aktiven Kontos nach versehentlicher Änderung

Sie können über das Azure-Portal ein aktives Konto oder ausgewählte Datenbanken und Container darin wiederherstellen. Führen Sie die folgenden Schritte zum Wiederherstellen Ihrer Daten aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto, und öffnen Sie den Bereich **Zeitpunktwiederherstellung**.

   > [!NOTE]
   > Der Bereich „Wiederherstellen“ im Azure-Portal wird nur aufgefüllt, wenn Sie die Berechtigung `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` haben. Informationen zum Festlegen dieser Berechtigung finden Sie im Artikel [Backup and restore permissions](continuous-backup-restore-permissions.md) (Berechtigungen zum Sichern und Wiederherstellen).

1. Füllen Sie die folgenden Details für die Wiederherstellung aus:

   * **Wiederherstellungspunkt (UTC)**  – Ein Zeitstempel innerhalb der letzten 30 Tage. Das Konto sollte zu diesem Zeitstempel vorhanden sein. Sie können den Wiederherstellungspunkt in UTC (koordinierte Weltzeit) angeben. Der Wert kann bis zu der Sekunde gehen, zu der Sie die Wiederherstellung durchführen möchten. Wählen Sie den Link **Hier klicken** aus, um Hilfe zum [Identifizieren des Wiederherstellungspunkts](#event-feed) zu erhalten.

   * **Ort** – Die Zielregion, in der das Konto wiederhergestellt wird. Das Konto sollte in dieser Region zum angegebenen Zeitstempel vorhanden sein (z. B. „USA, Westen“ oder „USA, Osten“). Ein Konto kann nur in den Regionen wiederhergestellt werden, in denen das Quellkonto vorhanden war.

   * **Ressource wiederherstellen** – Sie können entweder **Gesamtes Konto** oder eine(n) **ausgewählte(n) Datenbank/Container** zum Wiederherstellen auswählen. Die Datenbanken und Container sollten zum angegebenen Zeitstempel vorhanden sein. Basierend auf dem ausgewählten Wiederherstellungspunkt und Ort werden Wiederherstellungsressourcen aufgefüllt. Dies ermöglicht es dem Benutzer, bestimmte Datenbanken oder Container auszuwählen, die wiederhergestellt werden müssen.

   * **Ressourcengruppe** – Die Ressourcengruppe, unter der das Zielkonto erstellt und wiederhergestellt wird. Die Ressourcengruppe muss bereits vorhanden sein.

   * **Wiederherstellungszielkonto** – Der Name des Zielkontos. Der Name des Zielkontos muss denselben Richtlinien wie beim Erstellen eines neuen Kontos folgen. Dieses Konto wird durch den Wiederherstellungsvorgang in derselben Region erstellt, in der Ihr Quellkonto vorhanden ist.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Stellen Sie ein aktives Konto nach einer versehentlichen Änderung über das Azure-Portal wieder her." border="true":::

1. Nachdem Sie die obengenannten Parameter ausgewählt haben, wählen Sie die Schaltfläche **Senden** aus, um eine Wiederherstellung zu starten. Die Wiederherstellungskosten sind eine einmalige Gebühr, die auf der Datenmenge und den Gebühren für den Speicher in der angegebenen Region basiert. Weitere Informationen finden Sie im Abschnitt [Preise](continuous-backup-restore-introduction.md#continuous-backup-pricing).

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Verwenden des Ereignisfeeds zum Identifizieren der Wiederherstellungszeit

Wenn Sie beim Eingeben der Zeit des Wiederherstellungspunkts im Azure-Portal Hilfe zum Identifizieren des Wiederherstellungspunkts benötigen, wählen Sie den Link **Hier klicken** aus, um das Blatt „Ereignisfeed“ zu öffnen. Der Ereignisfeed bietet eine Liste mit vollständiger Genauigkeit der Erstellungs-, Ersetzungs- und Löschereignisse für Datenbanken und Container des Quellkontos. 

Wenn Sie beispielsweise auf den Zeitpunkt wiederherstellen möchten, bevor ein bestimmter Container gelöscht oder aktualisiert wurde, überprüfen Sie diesen Ereignisfeed. Ereignisse werden in chronologisch absteigender zeitlicher Reihenfolge angezeigt, in der sie aufgetreten sind, mit den aktuellen Ereignissen ganz oben. Sie können die Ergebnisse durchsuchen und die Uhrzeit vor oder nach dem Ereignis auswählen, um Ihre Uhrzeit weiter einzugrenzen.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Verwenden Sie den Ereignisfeed zum Identifizieren der Zeit des Wiederherstellungspunkts." border="true":::

> [!NOTE]
> Änderungen an den Elementressourcen werden im Ereignisfeed nicht angezeigt. Sie können in den letzten 30 Tagen (solange es das Konto zu diesem Zeitpunkt gibt) jederzeit einen beliebigen Zeitstempel für die Wiederherstellung manuell angeben.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Wiederherstellen eines gelöschten Kontos

Über das Azure-Portal können Sie ein gelöschtes Konto innerhalb von 30 Tagen nach dem Löschvorgang vollständig wiederherstellen. Führen Sie die folgenden Schritte zum Wiederherstellen eines gelöschten Kontos aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Suchen Sie in der globalen Suchleiste nach „Azure Cosmos DB“-Ressourcen. Dort werden alle Ihre vorhandenen Konten aufgelistet.
1. Wählen Sie als Nächstes die Schaltfläche **Wiederherstellen** aus. Im Bereich „Wiederherstellen“ wird eine Liste der gelöschten Konten angezeigt, die innerhalb des Aufbewahrungszeitraums wiederhergestellt werden können. Er umfasst 30 Tage ab dem Löschzeitpunkt.
1. Wählen Sie das Konto aus, das Sie wiederherstellen möchten.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Stellen Sie ein gelöschtes Konto über das Azure-Portal wieder her." border="true":::

   > [!NOTE]
   > Hinweis: Der Bereich „Wiederherstellen“ im Azure-Portal wird nur aufgefüllt, wenn Sie die Berechtigung `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` haben. Informationen zum Festlegen dieser Berechtigung finden Sie im Artikel [Backup and restore permissions](continuous-backup-restore-permissions.md) (Berechtigungen zum Sichern und Wiederherstellen).

1. Wählen Sie ein Konto für die Wiederherstellung aus, und geben Sie die folgenden Details zum Wiederherstellen eines gelöschten Kontos ein:

   * **Wiederherstellungspunkt (UTC)**  – Ein Zeitstempel innerhalb der letzten 30 Tage. Das Konto sollte zu diesem Zeitstempel vorhanden gewesen sein. Geben Sie den Wiederherstellungspunkt in UTC (koordinierte Weltzeit) an. Der Wert kann bis zu der Sekunde gehen, zu der Sie die Wiederherstellung durchführen möchten.

   * **Ort** – Die Zielregion, in der das Konto wiederhergestellt werden muss. Das Quellkonto sollte in dieser Region zum angegebenen Zeitstempel vorhanden sein. Beispiel: „USA, Westen“ oder „USA, Osten“.  

   * **Ressourcengruppe** – Die Ressourcengruppe, in der das Zielkonto erstellt und wiederhergestellt wird. Die Ressourcengruppe muss bereits vorhanden sein.

   * **Wiederherstellungszielkonto** – Der Name des Zielkontos muss denselben Richtlinien wie beim Erstellen eines neuen Kontos folgen. Dieses Konto wird durch den Wiederherstellungsvorgang in derselben Region erstellt, in der Ihr Quellkonto vorhanden ist.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Nachverfolgen des Status eines Wiederherstellungsvorgangs

Wählen Sie nach dem Einleiten eines Wiederherstellungsvorgangs das Glockensymbol für **Benachrichtigung** in der oberen rechten Ecke des Portals aus. Daraufhin wird ein Link eingeblendet, über den der Status des gerade wiederhergestellten Kontos angezeigt wird. Während die Wiederherstellung ausgeführt wird, lautet der Status des Kontos „Wird erstellt“. Nach Abschluss des Wiederherstellungsvorgangs wird er in „Online“ geändert.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="Der Status des wiederhergestellten Kontos wird nach Abschluss des Vorgangs von „Wird erstellt“ in „Online“ geändert." border="true":::

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren und verwalten Sie die fortlaufende Sicherung mithilfe von [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-powershell.md) oder [Azure Resource Manager](continuous-backup-restore-template.md).
* [Ressourcenmodell des fortlaufenden Sicherungsmodus](continuous-backup-restore-resource-model.md)
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.
