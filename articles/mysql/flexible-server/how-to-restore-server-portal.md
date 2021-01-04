---
title: Wiederherstellen – Azure-Portal – Azure Database for MySQL – Flexible Server
description: In diesem Artikel wird beschrieben, wie Sie Wiederherstellungsvorgänge in Azure Database for MySQL im Azure-Portal durchführen.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 062d53fcb122ebacd004d7dca5e11f5a883354cd
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241955"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Zeitpunktwiederherstellung von Azure Database for MySQL – Flexible Server (Vorschau)


> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

In diesem Artikel wird Schritt für Schritt beschrieben, wie Sie Zeitpunktwiederherstellungen in einer Flexible Server-Instanz mithilfe von Sicherungen durchführen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

-   Sie müssen über Azure Database for MySQL Flexible Server verfügen.

## <a name="restore-to-the-latest-restore-point"></a>Wiederherstellen des letzten Wiederherstellungspunkts

Führen Sie die folgenden Schritte aus, um Ihre Flexible Server-Instanz mit der frühesten vorhandenen Sicherung wiederherzustellen.

1.  Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Flexible Server-Instanz aus, von der aus Sie die Sicherung wiederherstellen möchten.

2.  Klicken Sie im linken Bereich auf **Übersicht**.

3.  Klicken Sie auf der Seite „Übersicht“ auf **Wiederherstellen**.

    [Platzhalter]

4.  Die Wiederherstellungsseite wird angezeigt, und Sie können zwischen **Neuester Wiederherstellungspunkt** und „Benutzerdefinierter Wiederherstellungspunkt“ auswählen.

5.  Wählen Sie **Neuester Wiederherstellungspunkt** aus.


6.  Geben Sie einen neuen Servernamen im Feld **Auf neuem Server wiederherstellen** ein.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Frühester Wiederherstellungszeitpunkt":::

8.  Klicken Sie auf **OK**.

9.  Es wird eine Benachrichtigung angezeigt, dass der Wiederherstellungsvorgang eingeleitet wurde.

## <a name="restoring-to-a-custom-restore-point"></a>Wiederherstellen auf einen benutzerdefinierten Wiederherstellungspunkt

Führen Sie die folgenden Schritte aus, um Ihre Flexible Server-Instanz mit der frühesten vorhandenen Sicherung wiederherzustellen.

1.  Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Flexible Server-Instanz aus, von der aus Sie die Sicherung wiederherstellen möchten.

2.  Klicken Sie auf der Seite „Übersicht“ auf **Wiederherstellen**.

    [Platzhalter]

3.  Die Wiederherstellungsseite wird angezeigt, und Sie können zwischen „Frühester Wiederherstellungspunkt“ und „Benutzerdefinierter Wiederherstellungspunkt“ auswählen.

4.  Wählen Sie **Benutzerdefinierter Wiederherstellungspunkt** aus.

5.  Wählen Sie Datum und Uhrzeit aus.

6.  Geben Sie einen neuen Servernamen im Feld **Auf neuem Server wiederherstellen** ein.

6.  Geben Sie einen neuen Servernamen im Feld **Auf neuem Server wiederherstellen** ein. 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="Übersicht anzeigen":::
 
7.  Klicken Sie auf **OK**.

8.  Es wird eine Benachrichtigung angezeigt, dass der Wiederherstellungsvorgang eingeleitet wurde.

## <a name="next-steps"></a>Nächste Schritte

Platzhalter
