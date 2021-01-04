---
title: Verwalten von DNS-Ressourceneintragssätzen und -Einträgen in Azure DNS
description: Mit Azure DNS können Sie beim Hosten Ihrer Domäne DNS-Ressourceneintragssätze und -Einträge verwalten.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 22ceba69ecf865d906021068a39a9d273b842ca2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965730"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Verwalten von DNS-Einträgen und - Ressourceneintragssätzen im Azure-Portal

In diesem Artikel wird gezeigt, wie Sie Ressourceneintragssätze und Einträge für die DNS-Zone im Azure-Portal verwalten.

Es ist wichtig, den Unterschied zwischen DNS-Ressourceneintragssätzen und einzelnen DNS-Ressourceneinträgen zu verstehen. Bei einem Ressourceneintragssatz handelt es sich um die Sammlung von Einträgen in einer Zone, die den gleichen Namen und den gleichen Typ aufweisen. Weitere Informationen finden Sie unter [Erstellen von DNS-Ressourceneintragssätzen und -Einträgen mit dem Azure-Portal](./dns-getstarted-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Erstellen eines neuen Ressourceneintragssatzes und eines Eintrags

Weitere Informationen zum Erstellen eines Ressourceneintragssatzes im Azure-Portal finden Sie unter [Erstellen von DNS-Ressourceneintragssätzen und -Einträgen mit dem Azure-Portal](./dns-getstarted-portal.md).

## <a name="view-a-record-set"></a>Anzeigen eines Ressourceneintragssatzes

1. Wechseln Sie im Azure-Portal zum Blatt **DNS-Zone** .
2. Suchen Sie den Ressourceneintragssatz, und wählen Sie ihn aus. Dies öffnet die Eigenschaften des Ressourceneintragssatzes.

    ![Suchen nach einem Ressourceneintragssatz](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Hinzufügen eines Eintrags zu einem Ressourceneintragssatz

Sie können einem Ressourceneintragssatz bis zu 20 Einträge hinzufügen. Ein Ressourceneintragssatz darf nicht zwei identische Einträge enthalten. Leere Ressourceneintragssätze (ohne Einträge) können erstellt werden, doch werden sie nicht im Azure DNS-Namenserver angezeigt. Ressourceneintragssätze vom Typ CNAME können höchstens einen Eintrag enthalten.

1. Klicken Sie auf dem Blatt **Eigenschaften des Ressourceneintragssatzes** Ihrer DNS-Zone auf den Ressourceneintragssatz, dem Sie einen Eintrag hinzufügen möchten.

    ![Auswählen eines Ressourceneintragssatzes](./media/dns-operations-recordsets-portal/selectset500.png)

2. Geben Sie die Einstellungen des Ressourceneintragssatzes an, indem Sie die Felder ausfüllen.

    ![Hinzufügen eines Eintrags](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Klicken Sie oben auf dem Blatt auf **Speichern** , um Ihre Einstellungen zu speichern. Schließen Sie dann das Blatt.
4. In der Ecke sehen Sie, dass der Eintrag gespeichert wird.

    ![Speichern eines Ressourceneintragssatzes](./media/dns-operations-recordsets-portal/saving150.png)

Nachdem der Eintrag gespeichert wurde, spiegeln die Werte auf dem Blatt **DNS-Zone** den neuen Eintrag wider.

## <a name="update-a-record"></a>{1}Update a record{2} (Datensatz aktualisieren)

Wenn Sie einen Eintrag innerhalb eines vorhandenen Ressourceneintragssatzes aktualisieren, hängt vom Eintragstyp ab, welche Felder Sie aktualisieren können.

1. Suchen Sie auf dem Blatt **Eigenschaften des Ressourceneintragssatzes** Ihres Ressourceneintragssatzes nach dem Eintrag.
2. Ändern Sie den Eintrag. Wenn Sie einen Eintrag ändern, können Sie die verfügbaren Einstellungen für den Eintrag ändern. Im folgenden Beispiel ist das Feld **IP-Adresse** ausgewählt, und die IP-Adresse wird gerade geändert.

    ![Ändern Sie eines Eintrags](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Klicken Sie oben auf dem Blatt auf **Speichern** , um Ihre Einstellungen zu speichern. In der oberen rechten Ecke wird die Benachrichtigung angezeigt, dass der Eintrag gespeichert wurde.

    ![Gespeicherter Ressourceneintragssatz](./media/dns-operations-recordsets-portal/saved150.png)

Nachdem der Eintrag gespeichert wurde, spiegeln die Werte für den Ressourceneintragssatz auf dem Blatt **DNS-Zone** den aktualisierten Eintrag wider.

## <a name="remove-a-record-from-a-record-set"></a>Entfernen eines Eintrags aus einem Ressourceneintragssatz

Sie können Einträge mit dem Azure-Portal aus einem Ressourceneintragssatz entfernen. Beachten Sie, dass durch das Entfernen des letzten Eintrags aus einem Ressourceneintragssatz nicht der Ressourceneintragssatz an sich gelöscht wird.

1. Suchen Sie auf dem Blatt **Eigenschaften des Ressourceneintragssatzes** Ihres Ressourceneintragssatzes nach dem Eintrag.
2. Klicken Sie auf den Eintrag, den Sie entfernen möchten. Wählen Sie dann **Entfernen**.

    ![Entfernen eines Eintrags](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Klicken Sie oben auf dem Blatt auf **Speichern** , um Ihre Einstellungen zu speichern.
4. Nachdem der Eintrag entfernt wurde, zeigen die Werte für den Eintrag auf dem Blatt **DNS-Zone** an, dass der Eintrag entfernt wurde.

## <a name="delete-a-record-set"></a><a name="delete"></a>Löschen eines Ressourceneintragssatzes

1. Klicken Sie auf dem Blatt **Eigenschaften des Ressourceneintragssatzes** Ihres Ressourceneintragssatzes auf **Entfernen**.

    ![Löschen eines Eintragssatzes](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Sie werden in einer Meldung gefragt, ob Sie den Ressourceneintragssatz wirklich löschen möchten.
3. Überprüfen Sie, ob der Name des Ressourceneintragssatzes dem entspricht, den Sie entfernen möchten, und klicken Sie dann auf **Ja**.
4. Überprüfen Sie auf dem Blatt **DNS-Zone** , ob der Ressourceneintragssatz nicht mehr sichtbar ist.

## <a name="work-with-ns-and-soa-records"></a>Arbeiten mit NS- und SOA-Einträgen

Automatisch erstellte NS- und SOA-Einträge werden anderes verwaltet als andere Ressourceneintragssätze.

### <a name="modify-soa-records"></a>Ändern von SOA-Einträgen

Sie können dem automatisch erstellten SOA-Eintragssatz am Zonen-Apex (Name = „\@“) weder Einträge hinzufügen noch Einträge daraus entfernen. Allerdings können Sie jeden Parameter im SOA-Eintrag (mit Ausnahme von „Host“) und die Gültigkeitsdauer des Ressourceneintragssatzes ändern.

### <a name="modify-ns-records-at-the-zone-apex"></a>Ändern von NS-Einträgen an der Zonenspitze

Der für die Zonenspitze festgelegte NS-Eintrag wird für jede DNS-Zone automatisch erstellt. Er enthält die Namen der Azure DNS-Namenserver, die der Zone zugewiesen sind.

Sie können diesem NS-Eintragssatz weitere Namenserver hinzufügen, um das gemeinsame Hosten von Domänen mit mehr als einem DNS-Anbieter zu unterstützen. Sie können auch die Gültigkeitsdauer und die Metadaten für diesen Datensatz ändern. Es ist aber nicht möglich, die vorab mit Daten aufgefüllten Azure-DNS-Namensserver zu entfernen oder zu ändern.

Beachten Sie, dass dies nur für den NS-Eintragssatz der Zonenspitze gilt. Andere NS-Eintragssätze in Ihrer Zone (zur Delegierung von untergeordneten Zonen) können ohne Einschränkungen geändert werden.

### <a name="delete-soa-or-ns-record-sets"></a>Löschen von SOA- oder NS-Ressourceneintragssätzen

Sie können den SOA- und NS-Ressourceneintragssatz am Zonen-Apex (Name = „\@“), die beim Erstellen der Zone automatisch erstellt werden, nicht löschen. Sie werden automatisch gelöscht, wenn Sie die Zone löschen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure DNS finden Sie unter [Azure DNS – Übersicht](dns-overview.md).
* Weitere Informationen zum Automatisieren von DNS finden Sie unter [Erstellen von DNS-Zonen und -Datensatzgruppen mithilfe des .NET SDK](dns-sdk.md).
* Weitere Informationen zu Reverse-DNS-Einträgen finden Sie unter [Übersicht über Reverse-DNS und die Unterstützung in Azure](dns-reverse-dns-overview.md).
* Weitere Informationen zu Azure DNS-Aliaseinträgen finden Sie unter [Azure DNS-Aliaseinträge – Übersicht](dns-alias.md).