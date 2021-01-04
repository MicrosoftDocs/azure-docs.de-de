---
title: 'Azure Peering Service: Erstellen '
description: In diesem Tutorial erfahren Sie, wie Sie den Azure Peering Service und ein Präfix registrieren.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f8f0ee71a8dd00498d868bb6dc11b3fa083df766
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021094"
---
# <a name="tutorial-create-a-peering-service-connection"></a>Tutorial: Erstellen einer Peering Service-Verbindung

In diesem Tutorial wird gezeigt, wie Sie eine Peering Service-Ressource erstellen und eine Peering Service-Verbindung konfigurieren. 

1. Zum Registrieren einer Peering Service-Verbindung wählen Sie **Ressource erstellen** > **Peering Service** aus.

 
    ![Registrieren von Peering Service](./media/peering-service-portal/peering-servicecreate.png)

2. Geben Sie auf der Registerkarte **Grundlagen** auf der Seite **Erstellen einer Peering Service-Verbindung** die folgenden Details ein.
 
3. Wählen Sie das Abonnement und die Ressourcengruppe aus, das bzw. die dem Abonnement zugeordnet ist.

    ![Standardregisterkarte „Peering Service registrieren“](./media/peering-service-portal/peering-servicebasics.png)

4. Geben Sie im Feld **Name** den Namen ein, auf den die Peering Service-Instanz registriert werden soll.

5. Wählen Sie anschließend unten auf der Seite die Schaltfläche **Next:Configuration** aus. Die Seite **Konfiguration** wird angezeigt.
## <a name="configure-the-peering-service-connection"></a>Konfigurieren der Peering Service-Verbindung

1. Wählen Sie auf der Seite **Konfiguration** den Speicherort aus, an dem der Peering Service aktiviert werden muss. Wählen Sie dazu in der Dropdownliste **Peering Service-Speicherort** denselben Speicherort aus.

1. Wählen Sie den Dienstanbieter aus, von dem der Peering Service abgerufen werden muss. Wählen Sie dazu in der Dropdownliste **Peering Service-Anbieter** einen Anbieternamen aus.
 
1. Wählen Sie im unteren Bereich des Abschnitts **Präfixe** die Option **Neues Präfix erstellen** aus. Dann werden Textfelder angezeigt. Geben Sie anschließend den Namen der Präfixressource und die Präfixe ein, die dem Dienstanbieter zugeordnet werden.

1. Wählen Sie **Präfixschlüssel** aus, und fügen Sie den Präfixschlüssel hinzu, der Ihnen von Ihrem Anbieter (ISP oder IXP) zugeteilt wurde. Mit diesem Schlüssel kann MS das Präfix und den Anbieter überprüfen, die Ihr IP-Präfix zugeordnet haben.

    ![Screenshot, der die Registerkarte „Konfiguration“ der Seite „Peering Service-Verbindung erstellen“ zeigt, auf der Sie den Präfixschlüssel eingeben können.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Wählen Sie unten links auf der Seite die Schaltfläche **Überprüfen + erstellen** aus. Die Seite **Überprüfen + erstellen** wird angezeigt, und Azure überprüft Ihre Konfiguration.

 1. Wenn die Meldung **Überprüfung erfolgreich** wie hier angezeigt wird, wählen Sie **Erstellen** aus.

> ![Screenshot, der die Registerkarte „Überprüfen und erstellen“ der Seite „Peering Service-Verbindung erstellen“ zeigt.](./media/peering-service-portal/peering-service-prefix.png)

1. Nachdem Sie eine Peering Service Verbindung registriert haben, wird für die darin enthaltenen Präfixe eine zusätzliche Prüfung durchgeführt. Sie können den Prüfungsstatus unter dem Abschnitt **Präfixe** des Ressourcennamens überprüfen. Wenn die Überprüfung fehlschlägt, wird eine der folgenden Fehlermeldungen angezeigt:

   - Invalid Peering Service prefix, the prefix should be valid format, only Ipv4 prefix is supported. (Ungültiges Peering Service-Präfix; das Präfix sollte ein gültiges Format haben; unterstützt wird nur das Präfix „Ipv4“.)
   - Prefix was not received from Peering Service provider. (Es wurde kein Präfix vom Peering Service-Anbieter empfangen.)
   - Prefix announcement does not have a valid BGP community, please contact Peering Service provider. (Präfixankündigung enthält keine gültige BGP-Community; bitte kontaktieren Sie den Peering Service-Anbieter).
   - Backup route not found, please contact Peering Service provider. (Sicherungsroute nicht gefunden; bitte kontaktieren Sie den Peering Service-Anbieter.)
   - Prefix received with longer AS path, please contact Peering Service provider. (Präfix mit längerem AS-Pfad empfangen; bitte kontaktieren Sie den Peering Service-Anbieter).
   - Prefix received with private AS in the path, please contact Peering Service provider. (Präfix mit privatem AS im Pfad empfangen; bitte kontaktieren Sie den Peering Service-Anbieter.)

### <a name="add-or-remove-a-prefix"></a>Hinzufügen oder Entfernen eines Präfixes

Wählen Sie auf der Seite **Präfixe** die Option **Präfixe hinzufügen** aus, um Präfixe hinzuzufügen.

Wählen Sie neben dem aufgeführten Präfix die Auslassungspunkte (...) und dann die Option **Löschen** aus.

### <a name="delete-a-peering-service-connection"></a>Löschen einer Peering Service-Verbindung

Aktivieren Sie auf der Seite **Alle Ressourcen** das Kontrollkästchen für den Peering Service, und wählen Sie oben auf der Seite die Option **Löschen** aus.
## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Peering Service-Verbindung finden Sie unter [Peering Service-Verbindung](connection.md).
- Informationen zur Peering Service-Verbindungstelemetrie finden Sie unter [Peering Service-Verbindungstelemetrie](connection-telemetry.md).
- Informationen zum Messen der Telemetrie finden Sie unter [Messen der Verbindungstelemetrie](measure-connection-telemetry.md).
- Informationen zum Registrieren der Verbindung mithilfe von Azure PowerShell finden Sie unter [Registrieren einer Peering Service-Verbindung – Azure PowerShell](powershell.md).
- Informationen zum Registrieren der Verbindung über die Azure CLI finden Sie unter [Registrieren einer Peering Service-Verbindung – Azure CLI](cli.md).