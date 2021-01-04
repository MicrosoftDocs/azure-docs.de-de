---
title: Verschieben von Azure-Regionen – Azure-Portal – Azure Database for MySQL
description: Verschieben Sie einen Azure Database for MySQL-Server mithilfe eines Lesereplikats und des Azure-Portals aus einer Azure-Region in eine andere.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.openlocfilehash: 279526d31adf25d1f4cd5a6d9b15519679bd88b9
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540228"
---
# <a name="move-an-azure-database-for-mysql-server-to-another-region-by-using-the-azure-portal"></a>Verschieben eines Azure Database for MySQL-Servers mithilfe des Azure-Portals in eine andere Region

Es gibt verschiedene Szenarien, in denen ein vorhandener Azure Database for MySQL-Server aus einer Region in eine andere verschoben wird. Ein derartiges Szenario könnte beispielsweise darin bestehen, dass Sie im Rahmen Ihrer Planung für die Notfallwiederherstellung einen Produktionsserver in eine andere Region verschieben möchten.

Sie können ein [regionsübergreifendes Lesereplikat](concepts-read-replicas.md#cross-region-replication) für Azure Database for MySQL verwenden, um die Verschiebung in eine andere Region vorzunehmen. Hierzu erstellen Sie zunächst ein Lesereplikat in der Zielregion. Beenden Sie anschließend die Replikation auf dem Lesereplikatserver, um ihn zu einem eigenständigen Server zu machen, der sowohl Lese- als auch Schreibdatenverkehr akzeptiert. 

> [!NOTE]
> Der Schwerpunkt des vorliegenden Artikels liegt auf dem Verschieben des Servers in eine andere Region. Wenn Sie Ihren Server in eine andere Ressourcengruppe oder ein anderes Abonnement verschieben möchten, finden Sie entsprechende Informationen im Artikel [Verschieben](../azure-resource-manager/management/move-resource-group-and-subscription.md). 

## <a name="prerequisites"></a>Voraussetzungen

- Das Feature für Lesereplikate ist nur für Azure Database for MySQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Quellserver einer der folgenden Tarife festgelegt ist.

- Stellen Sie sicher, dass sich der Azure Database for MySQL-Quellserver in der Azure-Region befindet, aus der die Verschiebung erfolgen soll.

## <a name="prepare-to-move"></a>Vorbereiten der Verschiebung

Führen Sie die folgenden Schritte aus, um mithilfe des Azure-Portals einen regionsübergreifenden Lesereplikatserver in der Zielregion zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie den vorhandenen Azure Database for MySQL-Server aus, den Sie als Quellserver verwenden möchten. Mit dieser Aktion wird die Seite **Übersicht** geöffnet.
1. Wählen Sie im Menü unter **EINSTELLUNGEN** die Option **Replikation** aus.
1. Wählen Sie **Replikat hinzufügen**.
1. Geben Sie einen Namen für den Replikatserver ein.
1. Wählen Sie den Standort für den Replikatserver aus. Der Standardstandort ist mit dem des Quellserver identisch. Vergewissern Sie sich, dass Sie den Zielspeicherort ausgewählt haben, an dem das Replikat bereitgestellt werden soll.
1. Wählen Sie **OK** aus, um die Erstellung des Replikats zu bestätigen. Während der Replikaterstellung werden die Daten vom Quellserver auf das Replikat kopiert. Die Erstellung kann mehrere Minuten oder sogar länger dauern, dies hängt von der Größe des Quellservers ab.

>[!NOTE]
> Wenn Sie ein Replikat erstellen, erbt es nicht die VNET-Dienstendpunkte des Quellservers. Diese Regeln müssen separat für das Replikat eingerichtet werden.

## <a name="move"></a>Move

> [!IMPORTANT]
> Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.
> Stellen Sie vor dem Beenden der Replikation auf einem Lesereplikat sicher, dass das Replikat alle erforderlichen Daten enthält.

Das Beenden der Replikation auf dem Replikatserver macht diesen zu einem eigenständigen Server. Führen Sie die folgenden Schritte aus, um im Azure-Portal die Replikation in das Replikat zu beenden:

1. Nachdem das Replikat erstellt wurde, suchen Sie den Azure Database for MySQL-Quellserver, und wählen Sie ihn aus. 
1. Wählen Sie im Menü unter **EINSTELLUNGEN** die Option **Replikation** aus.
1. Wählen Sie den Replikatserver aus.
1. Wählen Sie **Replikation beenden** aus.
1. Klicken Sie auf **OK**, um zu bestätigen, dass Sie die Replikation beenden möchten.

## <a name="clean-up-source-server"></a>Bereinigen des Quellservers

Möglicherweise möchten Sie den Azure Database for MySQL-Quellserver löschen. Führen Sie dazu die folgenden Schritte aus:

1. Nachdem das Replikat erstellt wurde, suchen Sie den Azure Database for MySQL-Quellserver, und wählen Sie ihn aus.
1. Wählen Sie im Fenster **Übersicht** die Option **Löschen** aus.
1. Geben Sie den Namen des Quellservers ein, um zu bestätigen, dass Sie ihn löschen möchten.
1. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mithilfe des Azure-Portals einen Azure Database for MySQL-Server aus einer Region in eine andere verschoben und anschließend die nicht mehr benötigten Quellressourcen bereinigt. 

- Weitere Informationen zu [Lesereplikaten](concepts-read-replicas.md)
- Weitere Informationen zum [Verwalten von Lesereplikaten im Azure-Portal](howto-read-replicas-portal.md)
- Weitere Informationen zu den Optionen für [Geschäftskontinuität](concepts-business-continuity.md)