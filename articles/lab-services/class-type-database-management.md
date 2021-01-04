---
title: Einrichten eines Labs zum Vermitteln von Kenntnissen zur Verwaltung von relationalen Datenbanken | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Lab einrichten, um die Verwaltung von relationalen Datenbanken zu unterrichten.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 32079d6dc31cf3fcad66976737630472129b4271
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462414"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Einrichten eines Labs zum Vermitteln von Kenntnissen zur Verwaltung von relationalen Datenbanken

In diesem Artikel wird beschrieben, wie Sie ein Lab für einen Grundkurs zur Datenbankverwaltung in Azure Lab Services einrichten. Datenbankkonzepte werden in den Einführungskursen vermittelt, die in den meisten Informatikfachbereichen an Hochschulen angeboten werden. Structured Query Language (SQL) ist ein internationaler Standard. SQL ist die Standardsprache für die Verwaltung relationaler Datenbank; hierzu zählen das Hinzufügen, Aufrufen und Verwalten von Inhalten in einer Datenbank.  Ihre größten Vorteile liegen in der schnellen Verarbeitung, erwiesenen Zuverlässigkeit und flexiblen Nutzung.

In diesem Artikel erfahren Sie, wie Sie eine Vorlage für virtuelle Computer in einem Lab mit MySQL Database Server und SQL Server 2019-Server einrichten.  [MySQL](https://www.mysql.com/) ist ein frei verfügbares Open Source-RDBMS (Relational Database Management System, Managementsystem für relationale Datenbanken).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) ist die neueste Version von des RDBMS von Microsoft.

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten dieses Labs benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie im [Tutorial zum Einrichten eines Lab-Kontos](tutorial-setup-lab-account.md).  Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen für das Lab-Konto, die in der nachfolgenden Tabelle beschrieben werden. Weitere Informationen zum Aktivieren von Marketplace-Images finden Sie unter [Angeben von für Lab-Ersteller verfügbaren Marketplace-Images](./specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| ------------------- | ------------ |
|Marketplace-Image| Aktivieren Sie das Image „SQL Server 2019 Standard on Windows Server 2019“ für die Verwendung in Ihrem Lab-Konto.|

### <a name="lab-settings"></a>Lab-Einstellungen

Verwenden Sie beim Einrichten eines Classroom-Labs die Einstellungen in der unten stehenden Tabelle.  Weitere Informationen zum Erstellen eines Classroom-Labs finden Sie im Tutorial [Einrichten eines Classroom-Labs](tutorial-setup-classroom-lab.md).

| Lab-Einstellungen | Wert/Anweisungen |
| ------------ | ------------------ |
|VM-Größe| Mittel. Diese Größe eignet sich am besten für relationale Datenbanken, speicherinternes Caching und Analysen.|
|VM-Image| SQL Server 2019 Standard unter Windows Server 2019|

## <a name="template-machine-configuration"></a>Konfiguration des Vorlagencomputers

Zum Installieren von MySQL unter Windows Server 2019 können Sie die in [Install and Run MySQL Community Server on a Virtual Machine](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine) (Installieren und Ausführen von MySQL Community Server auf einem virtuellen Computer) beschriebenen Schritte ausführen.

SQL Server 2019 ist im beim Erstellen des neuen Labs ausgewählten VM-Image vorinstalliert.

## <a name="cost-estimate"></a>Kostenschätzung

Betrachten wir eine mögliche Kostenschätzung für diesen Kurs.  Wir setzen einen Kurs mit 25 Kursteilnehmern an.  Es ist eine Kursdauer von 20 Stunden geplant.  Zudem erhält jeder Kursteilnehmer ein Kontingent von 10 Stunden für Hausaufgaben und Aufgaben außerhalb der regulären Kurszeiten.  Als Größe des virtuellen Computers wurde „mittel“ ausgewählt, das entspricht 42 Lab-Einheiten.

Hier ist ein Beispiel für eine mögliche Kostenschätzung für diesen Kurs:

25 Kursteilnehmer \* (20 geplante Stunden + 10 Kontingentstunden) \* 0,42 USD pro Stunde = 315,00 USD

Weitere Informationen zu den Preisen finden Sie unter [Azure Lab Services-Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Zusammenfassung

In diesem Artikel wurden die erforderlichen Schritte zum Erstellen eines Labs für Grundkonzepte zur Datenbankverwaltung mit MySQL und SQL Server erläutert. Sie können ein ähnliches Setup für andere Datenbankkurse nutzen.

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Erstellen und Verwalten einer Vorlage](how-to-create-manage-template.md)
- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)