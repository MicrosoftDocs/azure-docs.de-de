---
title: 'Schnellstart: Erstellen einer Verwaltungsgruppe mit JavaScript'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe von JavaScript eine Verwaltungsgruppe, um Ihre Ressourcen in einer Ressourcenhierarchie zu organisieren.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 99a2ed406d4837f6fc346e68b3b400003feed38c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099148"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Schnellstart: Erstellen einer Verwaltungsgruppe mit JavaScript

Bei Verwaltungsgruppen handelt es sich um Container, mit denen Sie Zugriff, Richtlinien und Konformität abonnementübergreifend verwalten können. Erstellen Sie diese Container, um eine effektive und effiziente Hierarchie zu erstellen, die mit [Azure Policy](../policy/overview.md) und mit der [rollenbasierten Zugriffssteuerung (Role-Based Access Controls, RBAC) von Azure](../../role-based-access-control/overview.md) verwendet werden kann. Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren von Ressourcen mit Azure-Verwaltungsgruppen](overview.md).

Es kann bis zu 15 Minuten dauern, bis die Erstellung der ersten Verwaltungsgruppe im Verzeichnis abgeschlossen ist. Bei der ersten Erstellung werden Prozesse zum Einrichten des Verwaltungsgruppendiensts in Azure für Ihr Verzeichnis ausgeführt. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist. Weitere Informationen finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Stellen Sie vor Beginn sicher, dass mindestens Version 12 von [Node.js](https://nodejs.org/) installiert ist.

- Alle Azure AD-Benutzer im Mandanten können eine Verwaltungsgruppe erstellen, ohne dafür die Schreibberechtigung für die Verwaltungsgruppe zu benötigen, wenn der [Hierarchieschutz](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nicht aktiviert ist. Diese neue Verwaltungsgruppe ist dann der Stammverwaltungsgruppe oder der [Standardverwaltungsgruppe](./how-to/protect-resource-hierarchy.md#setting---default-management-group) untergeordnet, und dem Ersteller wird die Rolle „Besitzer“ zugewiesen. Der Verwaltungsgruppendienst ermöglicht dies, damit Rollen nicht auf Stammebene zugewiesen werden müssen. Bei der Erstellung der Stammverwaltungsgruppe hat kein Benutzer auf sie Zugriff. Der Sinn dahinter, dass Verwaltungsgruppen zu Beginn auf Stammebene erstellt werden können, ist die Vermeidung der Hürde, erst nach den globalen Administratoren für Azure AD suchen zu müssen, bevor Verwaltungsgruppen verwendet werden können.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Anwendungseinrichtung

Um JavaScript zum Verwalten von Verwaltungsgruppen zu aktivieren, muss die Umgebung eingerichtet werden. Dieses Setup funktioniert überall dort, wo JavaScript verwendet werden kann, einschließlich [Bash unter Windows 10](/windows/wsl/install-win10).

1. Richten Sie ein Node.js-Projekt ein, indem Sie den folgenden Befehl ausführen.

   ```bash
   npm init -y
   ```

1. Fügen Sie einen Verweis auf das yargs-Modul hinzu.

   ```bash
   npm install yargs
   ```

1. Fügen Sie einen Verweis auf das Azure Resource Graph-Modul hinzu.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Fügen Sie einen Verweis auf die Active Directory-Authentifizierungsbibliothek hinzu.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Überprüfen Sie in _package.json_, ob `@azure/arm-managementgroups` in Version **1.1.0** oder höher und `@azure/ms-rest-nodeauth` in Version **3.0.5** oder höher vorliegen.

## <a name="create-the-management-group"></a>Erstellen der Verwaltungsgruppe

1. Erstellen Sie eine neue Datei mit dem Namen _index.js_, und geben Sie den folgenden Code ein.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Geben Sie den folgenden Befehl in das Terminal ein:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Ersetzen Sie jeden Tokenplatzhalter `<>` durch die _Verwaltungsgruppen-ID_ bzw. den _Anzeigenamen der Verwaltungsgruppe_.

   Wenn das Skript versucht, sich zu authentifizieren, wird im Terminal eine Meldung ähnlich der folgenden angezeigt:

   > Verwenden Sie zur Anmeldung einen Webbrowser, um die Seite https://microsoft.com/devicelogin zu öffnen. Geben Sie dann zur Authentifizierung den Code FGB56WJUGK ein.

   Wenn Sie sich beim Browser authentifizieren, wird das Skript weiterhin ausgeführt.

Das Ergebnis der Erstellung der Verwaltungsgruppe wird an die Konsole ausgegeben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die installierten Bibliotheken aus Ihrer Anwendung entfernen möchten, führen Sie den folgenden Befehl aus.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Verwaltungsgruppe zum Organisieren der Ressourcenhierarchie erstellt. Die Verwaltungsgruppe kann Abonnements oder andere Verwaltungsgruppen enthalten.

Weitere Informationen zu Verwaltungsgruppen und zur Verwaltung Ihrer Ressourcenhierarchie finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> Verwalten von Ressourcen mit Verwaltungsgruppen