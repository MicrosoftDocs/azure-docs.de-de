---
title: 'Hauptversionsupgrade: Azure-Portal: Azure Database for MySQL Single Server'
description: In diesem Artikel wird beschrieben, wie Sie im Azure-Portal ein Upgrade der Hauptversion für Azure Database for MySQL Single Server durchführen.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753050"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Hauptversionsupgrade für Azure Database for MySQL Single Server im Azure-Portal

> [!IMPORTANT]
> Das Hauptversionsupgrade für Azure Database for MySQL Single Server befindet sich in der öffentlichen Vorschauphase.

In diesem Artikel wird beschrieben, wie Sie im Azure-Portal ein direktes Upgrade Ihrer MySQL-Hauptversion für Azure Database for MySQL Single Server durchführen.

Mit diesem Feature können Kunden einfach per Klick direkte Upgrades ihrer MySQL 5.6-Server auf MySQL-Version 5.7 durchführen, ohne dass Daten verschoben oder Verbindungszeichenfolgen für Anwendungen geändert werden müssen.

> [!Note]
> * Hauptversionsupgrade ist nur für das Upgrade von MySQL 5.6 auf MySQL 5.7 verfügbar<br>
> * Das Hauptversionsupgrade wird auf dem Replikatserver noch nicht unterstützt.
> * Der Server ist während des gesamten Upgradevorgangs nicht verfügbar. Es wird daher empfohlen, Upgrades während Ihres geplanten Wartungsfensters durchzuführen.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:
- [Azure Database for MySQL Single Server](quickstart-create-mysql-server-database-using-azure-portal.md)-Instanz

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Durchführung des Hauptversionsupgrades von MySQL 5.6 auf MySQL 5.7

Führen Sie die folgenden Schritte aus, um ein Hauptversionsupgrade für Ihren Azure Database for MySQL-Server 5.6 durchzuführen:

> [!IMPORTANT]
> Wir empfehlen, das Upgrade zunächst an einer wiederhergestellten Kopie des Servers durchzuführen, anstatt direkt ein Upgrade der Produktionsumgebung durchzuführen. Weitere Informationen finden Sie unter [Durchführen einer Zeitpunktwiederherstellung](howto-restore-server-portal.md#point-in-time-restore). 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren vorhandenen Azure Database for MySQL-Server 5.6 aus.

2. Klicken Sie auf der Seite **Übersicht** in der Symbolleiste auf die Schaltfläche **Upgrade**.

3. Wählen Sie im Abschnitt **Upgrade** die Option **OK** aus, um den Azure Database for MySQL-Server 5.6 auf Version 5.7 zu aktualisieren.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL: Übersicht über Upgrade":::

4. Mit einer Benachrichtigung wird bestätigt, dass der Upgradevorgang erfolgreich war.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**1. Wann wird dieses Upgrade-Feature allgemein verfügbar sein, da wir MySQL v5.6 in unserer Produktionsumgebung verwenden, das ein Upgrade erfordert?**

Die allgemeine Verfügbarkeit dieses Features ist vor der Einstellung von MySQL v5.6 geplant. Das Feature ist jedoch bereit für die Produktionsumgebung und wird von Azure voll unterstützt, sodass Sie es mit Zuversicht in Ihrer Umgebung ausführen können sollten. Als empfohlene bewährte Methode empfehlen wir Ihnen dringend, es zuerst auf einer wiederhergestellten Kopie des Servers auszuführen und zu testen, sodass Sie die Downtime während des Upgrades abschätzen können, und einen Anwendungskompatibilitätstest durchzuführen, bevor Sie es in der Produktionsumgebung ausführen. Weitere Informationen zum Erstellen einer Zeitpunktkopie Ihres Servers finden Sie unter [Durchführen einer Zeitpunktwiederherstellung](howto-restore-server-portal.md#point-in-time-restore). 

**2. Führt dies zu einer Downtime des Servers und wenn ja, wie lange dauert dies?**

Ja, der Server wird während des Upgradeprozesses nicht verfügbar sein, sodass wir Ihnen empfehlen, diesen Vorgang während Ihres geplanten Wartungsfensters durchzuführen. Die geschätzte Downtime hängt von der Datenbankgröße, der bereitgestellten Speichergröße (bereitgestellte IOPs) und der Anzahl der Tabellen in der Datenbank ab. Die Upgradezeit verhält sich direkt proportional zur Anzahl der Tabellen auf dem Server. Die Upgrades von Basic-SKU-Servern werden voraussichtlich länger dauern, da es sich um eine Standardspeicherplattform handelt. Um die Downtime für Ihre Serverumgebung abzuschätzen, empfehlen wir, das Upgrade zunächst auf einer wiederhergestellten Kopie des Servers durchzuführen.  

**3. Es wird darauf hingewiesen, dass es auf dem Replikatserver noch nicht unterstützt wird. Was bedeutet das konkret?**

Derzeit wird das Upgrade der Hauptversion für Replikatserver nicht unterstützt, was bedeutet, dass Sie es nicht für Server ausführen sollten, die an der Replikation beteiligt sind (weder Quell- noch Replikatserver). Wenn Sie das Upgrade der an der Replikation beteiligten Server testen möchten, bevor wir die Replikatunterstützung für das Upgrade-Feature hinzufügen, empfehlen wir folgende Schritte:

1. Während Ihrer geplanten Wartung [beenden Sie die Replikation und löschen den Replikatserver](howto-read-replicas-portal.md), nachdem Sie seinen Namen und alle Konfigurationsinformationen (Firewalleinstellungen, Konfiguration der Serverparameter, falls sie sich vom Quellserver unterscheidet) erfasst haben.
2. Führen Sie ein Upgrade des Quellservers durch.
3. Stellen Sie einen neuen Lesereplikatserver mit demselben Namen und denselben Konfigurationseinstellungen bereit, die in Schritt 1 erfasst wurden. Der neue Replikatserver weist automatisch v5.7 auf, nachdem der Quellserver auf v5.7 aktualisiert wurde.

**4. Was passiert, wenn wir das Upgrade unseres MySQL v5.6-Servers nicht vor dem 5. Februar 2021 durchführen?**

Sie können Ihren MySQL v5.6-Server weiterhin wie bisher betreiben. Azure **wird niemals** ein erzwungenes Upgrade auf Ihrem Server durchführen. Es gelten jedoch die in der Richtlinie [Azure Database for MySQL: Versionsrichtlinie](concepts-version-policy.md) dokumentierten Einschränkungen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich unter [Azure Database for MySQL: Versionsrichtlinie](concepts-version-policy.md).
