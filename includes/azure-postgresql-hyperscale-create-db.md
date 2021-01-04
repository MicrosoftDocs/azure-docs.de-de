---
title: include file
description: include file
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 12/16/2020
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: 54cc31cdb0ddc7694568d60b1446f1cf99663e95
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616625"
---
## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Erstellen einer Azure Database for PostgreSQL-Instanz: Hyperscale (Citus)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

### <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.


Führen Sie die folgenden Schritte aus, um eine Azure-Datenbank für PostgreSQL-Server zu erstellen:
1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.
2. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann auf der Seite **Datenbanken** die Option **Azure-Datenbank für PostgreSQL** aus.
3. Klicken Sie als Bereitstellungsoption auf die Schaltfläche **Erstellen** unter **Hyperscale-Servergruppe (Citus)** .
4. Geben Sie im Formular für den neuen Server folgende Informationen an:
   - Ressourcengruppe: Klicken Sie auf den Link **Neue erstellen** unterhalb des Textfelds für dieses Feld. Geben Sie einen Namen ein, z.B. **myresourcegroup**.
   - Servergruppenname: Geben Sie einen eindeutigen Namen für die neue Servergruppe ein, der ebenfalls für eine Serverunterdomäne verwendet wird.
   - Administratorbenutzername: muss derzeit **citus** lauten und kann nicht geändert werden.
   - Kennwort: muss mindestens 8 Zeichen lang sein und Zeichen aus drei der folgenden Kategorien enthalten: Großbuchstaben des englischen Alphabets, Kleinbuchstaben des englischen Alphabets, Ziffern (0–9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
   - Standort: Verwenden Sie den Standort, der Ihren Benutzern am nächsten ist, damit sie möglichst schnell auf die Daten zugreifen können.

   > [!IMPORTANT]
   > Das hier angegebene Kennwort für den Serveradministrator ist erforderlich, um die Anmeldung am Server und bei den zugehörigen Datenbanken auszuführen. Behalten Sie diese Angaben im Kopf, oder notieren Sie sie zur späteren Verwendung.

5. Klicken Sie auf **Servergruppe konfigurieren**. Übernehmen Sie die Einstellungen in diesem Abschnitt unverändert, und klicken Sie auf **Speichern**.
6. Klicken Sie unten auf der Seite auf **Weiter: Netzwerk >** .

7. Klicken Sie auf der Registerkarte **Netzwerk** auf das Optionsfeld **Öffentlicher Endpunkt**.
   ![Öffentlicher Endpunkt ausgewählt](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Klicken Sie auf den Link **+ Aktuelle Client-IP-Adresse hinzufügen**.
   ![Hinzugefügte Client-IP-Adresse](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Der Azure-PostgreSQL-Server kommuniziert über Port 5432. Wenn Sie versuchen, eine Verbindung aus einem Unternehmensnetzwerk heraus herzustellen, wird der ausgehende Datenverkehr über Port 5432 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Hyperscale (Citus)-Cluster herstellen, wenn Ihre IT-Abteilung den Port 5432 öffnet.
   >

9. Klicken Sie auf **Bewerten + erstellen** und dann auf **Erstellen**, um den Server bereitzustellen. Die Bereitstellung dauert einige Minuten.
10. Die Seite leitet zur Überwachung der Bereitstellung um. Wenn sich der Livestatus von **Ihre Bereitstellung wird ausgeführt** in **Ihre Bereitstellung wurde abgeschlossen** ändert, klicken Sie links auf der Seite auf das Menüelement **Ausgaben**.
11. Die Seite „Ausgaben“ enthält einen Koordinatorhostnamen mit einer Schaltfläche daneben, um den Wert in die Zwischenablage zu kopieren. Notieren Sie diese Informationen zur späteren Verwendung.

### <a name="connect-to-the-database-using-psql"></a>Herstellen einer Verbindung mit der Datenbank mithilfe von psql

Wenn Sie Ihren Azure Database for PostgreSQL-Server erstellt haben, wird eine Standarddatenbank mit dem Namen **citus** erstellt. Um eine Verbindung mit Ihrem Datenbankserver herzustellen, benötigen Sie eine Verbindungszeichenfolge und das Administratorkennwort.

1. Rufen Sie die Verbindungszeichenfolge ab. Klicken Sie auf der Seite der Servergruppe auf das Menüelement **Verbindungszeichenfolgen**. (Sie finden es unter **Einstellungen**.) Suchen Sie die mit **psql** markierte Zeichenfolge. Sie hat folgendes Format:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Kopieren Sie die Zeichenfolge. Sie müssen „{your\_password}“ durch das Administratorkennwort ersetzen, das Sie weiter oben ausgewählt haben. Das System speichert Ihr Kennwort nicht als Klartext, daher kann es in der Verbindungszeichenfolge nicht angezeigt werden.

2. Öffnen Sie auf Ihrem lokalen Computer ein Terminalfenster.

3. Stellen Sie an der Eingabeaufforderung mit dem Hilfsprogramm [psql](https://www.postgresql.org/docs/current/app-psql.html) eine Verbindung mit Ihrem Azure Database for PostgreSQL-Server her. Übergeben Sie die Verbindungszeichenfolge in Anführungszeichen, und achten Sie darauf, dass sie Ihr Kennwort enthält:
   ```bash
   psql "host=..."
   ```

   Der folgende Befehl stellt z. B. eine Verbindung mit dem Koordinatorknoten der Servergruppe **mydemoserver** her:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
