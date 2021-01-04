---
title: Direkte Änderung der SQL Server-Version
description: Erfahren Sie, wie Sie die Version Ihrer SQL Server-VM in Azure ändern.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 5dbd5725c666afe04d57d4432f0a4798fcb34c03
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358842"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Direktes Ändern der SQL Server-Version auf Azure-VMs

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird beschrieben, wie Sie die Version von Microsoft SQL Server auf einem virtuellen Windows-Computer (VM) in Microsoft Azure ändern.

## <a name="prerequisites"></a>Voraussetzungen

Für ein direktes Upgrade von SQL Server gelten die folgenden Bedingungen:

- Die Setupmedien der gewünschten SQL Server-Version sind erforderlich. Kunden mit [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) können Ihre Installationsmedien im [Microsoft Business Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx) abrufen. Kunden ohne Software Assurance können die Setupmedien in einem SQL Server-VM-Image aus Azure Marketplace mit einer späteren SQL Server-Version verwenden (in der Regel unter C:\SQLServerFull zu finden).
- Editionsupgrades sollten den [unterstützten Upgradepfaden folgen](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15).

## <a name="planning-for-version-change"></a>Planen der Versionsänderung

Es wird empfohlen, vor der Versionsänderung Folgendes zu überprüfen:

1. Überprüfen Sie die Neuerungen in der Version, auf die das Upgrade erfolgen soll:

   - Neuerungen in [SQL Server 2019](/sql/sql-server/what-s-new-in-sql-server-ver15)
   - Neuerungen in [SQL Server 2017](/sql/sql-server/what-s-new-in-sql-server-2017)
   - Neuerungen in [SQL Server 2016](/sql/sql-server/what-s-new-in-sql-server-2016)


1. Es wird empfohlen, die [Kompatibilitätszertifizierung](/sql/database-engine/install-windows/compatibility-certification) für die Version zu prüfen, zu der Sie wechseln möchten, damit Sie mithilfe der Datenbank-Kompatibilitätsgrade die Auswirkungen des Upgrades minimieren können.
1. In den folgenden Artikeln erfahren Sie, wie Sie zu einem erfolgreichen Ergebnis kommen:

   - [Video: Modernizing SQL Server | Pam Lahoud & Pedro Lopes | 20 Years of PASS](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [Assistent für Datenbankexperimente für AB-Tests](/sql/dea/database-experimentation-assistant-overview)
   - [Upgraden von Datenbanken mit dem Abfrageoptimierungs-Assistenten](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [Ändern des Datenbank-Kompatibilitätsgrads und Verwenden des Abfragedatenspeichers](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>Upgrade der SQL Server-Version

> [!WARNING]
> Bei einem Upgrade der SQL Server-Version werden der Dienst für SQL Server sowie alle zugeordneten Dienste, z. B. Analysis Services und R Services, neu gestartet.

Für ein Upgrade der SQL Server-Version beschaffen Sie sich die SQL Server-Setupmedien für die spätere Version mit [Unterstützung des Upgradepfads](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15) von SQL Server, und führen Sie dann die folgenden Schritte aus:

1. Sichern Sie die Datenbanken, einschließlich der System- (außer tempdb) und Benutzerdatenbanken, bevor Sie den Vorgang starten. Sie können auch mithilfe von Azure Backup-Diensten eine anwendungskonsistente Sicherung auf VM-Ebene erstellen.
1. Rufen Sie in den SQL Server-Installationsmedien „Setup.exe“ auf.
1. Der Installations-Assistent startet das SQL Server-Installationscenter. Um ein Upgrade einer vorhandenen Instanz von SQL Server durchzuführen, wählen Sie im Navigationsbereich **Installation** und dann **Upgrade einer früheren Version SQL Server** aus.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="Auswahl des Upgrades der Version von SQL Server":::

1. Aktivieren Sie auf der Seite **Product Key** eine Option, um anzugeben, ob Sie auf eine kostenlose Edition von SQL Server aktualisieren oder über einen PID-Schlüssel für eine Produktionsversion des Produkts verfügen. Weitere Informationen finden Sie unter [Editions and supported features of SQL Server 2019 (15.x) (Editionen und unterstützte Features von SQL Server 2019 [15.x]](/sql/sql-server/editions-and-components-of-sql-server-version-15) und [Unterstützte Versions- und Editionsupgrades in SQL Server 2016](/sql/database-engine/install-windows/supported-version-and-edition-upgrades).
1. Klicken Sie auf **Weiter**, bis Sie zur Seite **Zum Upgrade bereit** gelangen. Wählen Sie dann **Upgrade** aus. Das Setupfenster reagiert möglicherweise einige Minuten nicht mehr, während die Änderung wirksam wird. Auf der Seite **Abgeschlossen** wird bestätigt, dass das Upgrade abgeschlossen wurde. Eine Schritt-für-Schritt-Anleitung für das Upgrade finden Sie unter [Das gesamte Verfahren](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure).

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="Seite „Abgeschlossen“":::

Wenn Sie nicht nur die Version, sondern auch die SQL Server-Edition geändert haben, aktualisieren Sie auch die Edition, und beziehen Sie sich auf den Abschnitt **Überprüfen von Version und Edition im Portal**, um die SQL Server-VM-Instanz zu ändern.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="Ändern der Metadaten der Version":::

## <a name="downgrade-the-version-of-sql-server"></a>Downgrade der Version von SQL Server

Für ein Downgrade der SQL Server-Version müssen Sie SQL Server vollständig deinstallieren und unter Verwendung der gewünschten Version neu installieren. Dies ist vergleichbar mit einer Neuinstallation von SQL Server, da es nicht möglich ist, die frühere Datenbank aus einer späteren Version in der neu installierten früheren Version wiederherzustellen. Die Datenbanken müssen neu erstellt werden. Wenn Sie beim Upgrade auch die Edition von SQL Server geändert haben, ändern Sie die Eigenschaft **Edition** der SQL Server-VM im Azure-Portal in den neuen Editionswert. Dadurch werden die mit dieser VM verbundenen Meta- und Abrechnungsdaten aktualisiert.

> [!WARNING]
> Ein direktes Downgrade von SQL Server wird nicht unterstützt.

Führen Sie für ein Downgrade der SQL Server-Version die folgenden Schritte aus:

1. Vergewissern Sie sich, dass Sie kein Feature verwenden, das [nur in der späteren Version ](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx) verfügbar ist.
1. Sichern Sie die Datenbanken, einschließlich der System- (außer tempdb) und Benutzerdatenbanken.
1. Exportieren Sie alle erforderlichen Objekte auf Serverebene (wie Serverauslöser, Rollen, Anmeldungen, verknüpfte Server, Aufträge, Anmeldeinformationen und Zertifikate).
1. Wenn es keine Skripts gibt, um Ihre Benutzerdatenbanken mit der früheren Version neu zu erstellen, müssen Sie für alle Objekte ein Skript erstellen und alle Daten mithilfe von BCP.exe, SSIS oder DACPAC exportieren.

   Stellen Sie sicher, dass Sie die richtigen Optionen auswählen, wenn Sie Skripts für Elemente wie Zielversion, abhängige Objekte und erweiterte Optionen erstellen.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="Skriptoptionen":::

1. Führen Sie eine vollständige Deinstallation von SQL Server und aller zugeordneten Dienste durch.
1. Starten Sie den virtuellen Computer neu.
1. Installieren Sie SQL Server mithilfe der Medien für die gewünschte Version des Programms.
1. Installieren Sie die neuesten Service Packs und kumulativen Updates.
1. Importieren Sie alle erforderlichen Objekte auf Serverebene (die in Schritt 3 exportiert wurden).
1. Erstellen Sie alle erforderlichen Benutzerdatenbanken neu (mithilfe der erstellten Skripts oder der Dateien aus Schritt 4).

## <a name="verify-the-version-and-edition-in-the-portal"></a>Überprüfen der Version und der Edition im Portal

Nachdem Sie die Version von SQL Server geändert haben, registrieren Sie Ihre SQL Server-VM mit der [SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md) erneut, damit Sie die Version von SQL Server im Azure-Portal anzeigen können. Die aufgelistete Versionsnummer sollte nun die neu aktualisierte Version und Edition Ihrer SQL Server-Installation widerspiegeln.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="Überprüfen der Version":::

> [!NOTE]
> Wenn Sie die VM bereits mit der SQL-IaaS-Agent-Erweiterung registriert haben, [heben Sie die Registrierung auf](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension), und [registrieren Sie dann die SQL Server-VM-Ressource erneut](sql-agent-extension-manually-register-single-vm.md#register-with-extension), damit sie die richtige Version und Edition von SQL Server erkennt, die auf der VM installiert ist. Dadurch werden die mit dieser VM verbundenen Meta- und Abrechnungsdaten aktualisiert.

## <a name="remarks"></a>Bemerkungen

- Es wird empfohlen, nach Abschluss des Upgrades Sicherungen zu veranlassen, Statistiken zu aktualisieren, Indizes neu zu erstellen und die Konsistenz zu prüfen. Sie können auch die einzelnen Datenbank-Kompatibilitätsgrade überprüfen, um sicherzustellen, dass sie Ihrem gewünschten Grad entsprechen.
- Nachdem SQL Server auf der VM aktualisiert wurde, stellen Sie sicher, dass die Eigenschaft **Edition** von SQL Server im Azure-Portal für die Rechnungsstellung mit der installierten Editionsnummer übereinstimmt.
- Die Möglichkeit, die [Edition zu ändern](change-sql-server-edition.md#change-edition-in-portal), ist ein Feature der SQL-IaaS-Agent-Erweiterung. Beim Bereitstellen eines Azure Marketplace-Images über das Azure-Portal wird eine SQL Server-VM mit der Erweiterung automatisch registriert. Kunden, die SQL Server selbst installieren, müssen jedoch [ihre SQL Server-VM manuell registrieren](sql-agent-extension-manually-register-single-vm.md).
- Wenn Sie Ihre SQL Server-VM-Ressource verwerfen, wird die hartcodierte Editionseinstellung des Images wiederhergestellt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.md)
- [Preisinformationen für SQL Server auf virtuellen Windows-Computern](pricing-guidance.md)
- [SQL Server auf Windows-VMs – Versionshinweise](doc-changes-updates-release-notes.md)