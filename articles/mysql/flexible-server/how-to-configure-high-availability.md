---
title: Verwalten zonenredundanter Hochverfügbarkeit – Azure-Portal – Azure Database for MySQL Flexible Server
description: In diesem Artikel wird beschrieben, wie Sie die zonenredundante Hochverfügbarkeit in Azure Database for MySQL Flexible Server im Azure-Portal aktivieren oder deaktivieren.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: d65b074385311e74444929ef74901e402e29ec03
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241734"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Verwalten von zonenredundanter Hochverfügbarkeit für Azure Database for MySQL Flexible Server (Vorschau)

In diesem Artikel wird beschrieben, wie Sie die zonenredundante Hochverfügbarkeit in Azure Database for PostgreSQL – Flexible Server im Azure-Portal aktivieren oder deaktivieren.

Die Hochverfügbarkeitsfunktion stellt ein physisch getrenntes primäres und Standbyreplikat in unterschiedlichen Zonen bereit. Weitere Detailinformationen finden Sie unter [Dokumentation zu Hochverfügbarkeitskonzepten](./concepts/../concepts-high-availability.md). 

> [!IMPORTANT]
> Sie können die zonenredundante Hochverfügbarkeit nur während der Erstellung flexibler Server aktivieren.

Auf dieser Seite finden Sie Richtlinien zum Aktivieren oder Deaktivieren der Hochverfügbarkeit. Durch diesen Vorgang werden keine Ihrer anderen Einstellungen geändert, einschließlich der VNET-Konfiguration, der Firewalleinstellungen und der Sicherungsaufbewahrung. Analog dazu ist das Deaktivieren der Hochverfügbarkeit ein Onlinevorgang, der sich nicht auf die Konnektivität und die Vorgänge Ihrer Anwendung auswirkt.

> [!IMPORTANT]
> Zonenredundante Hochverfügbarkeit ist in einer begrenzten Anzahl von Regionen verfügbar: Asien (Südosten), USA (Westen 2), Europa (Westen) und USA (Osten).  

## <a name="enable-high-availability-during-server-creation"></a>Aktivieren von Hochverfügbarkeit während der Servererstellung

In diesem Abschnitt finden Sie spezifische Informationen für auf Hochverfügbarkeit bezogene Felder. Sie können diese Schritte ausführen, um Hochverfügbarkeit während der Erstellung Ihres flexiblen Servers bereitzustellen.

1.  Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option „Flexibler Server“ aus, und klicken Sie auf **Erstellen**.  Ausführliche Informationen zum Ausfüllen von Details wie **Abonnement** , **Ressourcengruppe** , **Servername** , **Region** und anderen Feldern finden Sie in der Dokumentation zur Vorgehensweise beim Erstellen des Servers.

2.  Aktivieren Sie das Kontrollkästchen für **Zonenredundante Hochverfügbarkeit** in der Option „Verfügbarkeit“.

3.  Wenn Sie die Standardwerte für „Compute“ und „Speicher“ ändern möchten, klicken Sie auf  **Server konfigurieren**.

4.  Wenn die Option „Hochverfügbarkeit“ aktiviert ist, steht die Ebene „Burstfähig“ nicht zur Auswahl. Sie können die Computeebene **Allgemeiner Zweck** oder **Arbeitsspeicheroptimiert** auswählen.

    > [!IMPORTANT]
    > Wir unterstützen zonenredundante Hochverfügbarkeit nur für die Tarife * *_Universell_* _ und _*_Arbeitsspeicheroptimiert_*_.

5.  Wählen Sie die _ *Computegröße* * für Ihre Option aus der Dropdownliste aus.

6.  Wählen Sie die **Speichergröße** in GiB mithilfe der Schiebereglerleiste aus, und wählen Sie den **Aufbewahrungszeitraum für Sicherungen** zwischen 7 Tagen und 35 Tagen aus.   

## <a name="disable-high-availability"></a>Deaktivieren der Hochverfügbarkeit

Führen Sie diese Schritte aus, um die Hochverfügbarkeit für Ihren flexiblen Server zu deaktivieren, der bereits mit Zonenredundanz konfiguriert ist.

1.  Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren vorhandenen flexiblen Azure Database for MySQL-Server aus.

2.  Klicken Sie auf der Seite „Flexibler Server“ im vorderen Bereich auf **Hochverfügbarkeit** , um die Seite „Hochverfügbarkeit“ zu öffnen.

3.  Klicken Sie auf das Kontrollkästchen **Zonenredundante Hochverfügbarkeit** , um die Option zu deaktivieren, und klicken Sie auf **Speichern** , um die Änderung zu speichern.

4.  Ein Bestätigungsdialogfeld wird angezeigt, in dem Sie die Deaktivierung der Hochverfügbarkeit bestätigen können.

5.  Klicken Sie auf die Schaltfläche **Hochverfügbarkeit deaktivieren** , um die Hochverfügbarkeit zu deaktivieren.

6.  Es wird eine Benachrichtigung angezeigt, die besagt, dass die Außerbetriebnahme der Bereitstellung der Hochverfügbarkeit ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zu [Geschäftskontinuität](./concepts-business-continuity.md)
-   Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
