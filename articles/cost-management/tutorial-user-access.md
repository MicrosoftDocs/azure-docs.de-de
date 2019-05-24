---
title: 'Tutorial: Zuweisen des Zugriffs mit Cloudyn in Azure | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie den Zugriff auf Cloudyn-Daten mit Benutzerkonten zuweisen, die Ebenen für den Zugriff auf Entitäten definieren.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: 4b309d54fae63f16574bb3799d77adfd89f91a57
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967081"
---
# <a name="tutorial-assign-access-to-cloudyn-data"></a>Tutorial: Zuweisen des Zugriffs auf Cloudyn-Daten

Der Zugriff auf Cloudyn-Daten wird über die Benutzer- oder Entitätsverwaltung bereitgestellt. Cloudyn-Benutzerkonten steuern den Zugriff auf *Entitäten* und administrative Funktionen. Es gibt zwei Arten des Zugriffs: Administrator und Benutzer. Sofern keine benutzerspezifische Änderung erfolgt, ermöglicht der Administratorzugriff eine unbeschränkte Nutzung sämtlicher Funktionen im Cloudyn-Portal, einschließlich Benutzerverwaltung, Empfängerlistenverwaltung und des Zugriffs auf alle Entitätsdaten auf Stammentitätsebene. Der Benutzerzugriff ist für Endbenutzer ausgelegt, die über ihren Zugriff auf Entitätsdaten Berichte anzeigen oder erstellen.

Entitäten geben die hierarchische Struktur Ihrer Geschäftsorganisation wieder. Sie stehen für Abteilungen, Geschäftsbereiche und Teams in Ihrer Organisation in Cloudyn. Die Entitätshierarchie hilft Ihnen dabei, die Ausgaben nach Entitäten genau nachzuverfolgen.

Bei der Registrierung Ihrer Azure-Vereinbarung oder Ihres Azure-Kontos wurde ein Konto mit Administratorberechtigung in Cloudyn erstellt, sodass Sie alle Schritte in diesem Tutorial ausführen können. Dieses Tutorial behandelt den Zugriff auf Cloudyn-Daten, einschließlich Benutzerverwaltung und Entitätsverwaltung. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines Benutzers mit Administratorzugriff
> * Erstellen eines Benutzers mit Benutzerzugriff
> * Löschen eines Benutzers
> * Löschen oder Exportieren von personenbezogenen Daten
> * Erstellen und Verwalten von Entitäten


Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Konto.
- Sie müssen entweder über eine Registrierung für die Testversion oder über ein kostenpflichtiges Abonnement für Cloudyn verfügen.

## <a name="create-a-user-with-admin-access"></a>Erstellen eines Benutzers mit Administratorzugriff

Obwohl Sie bereits über Administratorzugriff verfügen, benötigen Kollegen in Ihrer Organisation möglicherweise ebenfalls Administratorzugriff. Klicken Sie im Cloudyn-Portal auf das Zahnradsymbol in der rechten oberen Ecke, und wählen Sie **User Management** (Benutzerverwaltung) aus. Klicken Sie auf **Add New User** (Neuen Benutzer hinzufügen), um einen neuen Benutzer hinzuzufügen.

Geben Sie die erforderlichen Informationen zum Benutzer ein. Die **Anmelde-ID** muss eine gültige E-Mail-Adresse sein. Wählen Sie die erforderlichen Berechtigungen für die Benutzerverwaltung aus, damit der Benutzer andere Benutzer erstellen und ändern kann. Dank der Empfängerlistenverwaltung kann der Benutzer Empfängerlisten bearbeiten. Ein Link mit Anmeldeinformationen wird von Cloudyn per E-Mail an den Benutzer gesendet, wenn Sie **Notify user by email** (Benutzer per E-Mail benachrichtigen) aktivieren. Bei der ersten Anmeldung legt der Benutzer ein Kennwort fest.

Unter **User has admin access** (Benutzer verfügt über Administratorzugriff) ist die Stammentität Ihrer Organisation ausgewählt. Lassen Sie den Stamm ausgewählt, und speichern Sie die Benutzerinformationen. Durch das Auswählen der Stammentität verfügt der Benutzer nicht nur für die Stammentität in der Struktur über Administratorberechtigungen, sondern auch für alle Entitäten, die sich darunter befinden.  
  ![Beispiel: Administratorzugriff für Feld „Neuen Benutzer hinzufügen“](./media/tutorial-user-access/new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Erstellen eines Benutzers mit Benutzerzugriff
Typische Benutzer, die Zugriff auf Cloudyn-Daten wie Dashboards oder Berichte benötigen, sollten über den entsprechenden Benutzerzugriff zum Anzeigen verfügen. Erstellen Sie einen neuen Benutzer mit Benutzerzugriff ähnlich dem, den Sie mit Administratorzugriff erstellt haben, jedoch mit folgenden Unterschieden:

- Deaktivieren Sie **Allow User Management** (Benutzerverwaltung zulassen) und **Allow Recipient lists Management** (Empfängerlistenverwaltung zulassen), und deaktivieren Sie alle Elemente in der Liste **User has admin access** (Benutzer verfügt über Administratorzugriff).
- Aktivieren Sie in der Liste **User has user access** (Benutzer verfügt über Benutzerzugriff) die Entitäten, für die der Benutzer Zugriff benötigt.
- Sie können auch nach Bedarf Administratorrechte für den Zugriff auf bestimmte Entitäten zulassen.

![Beispiel: Benutzerzugriff für Feld „Neuen Benutzer hinzufügen“](./media/tutorial-user-access/new-user-access.png)

Ein Videotutorial zum Hinzufügen von Benutzern finden Sie unter [Adding Users to Cloudyn](https://youtu.be/Nzn7GLahx30) (Hinzufügen von Benutzern zu Cloudyn).

## <a name="delete-a-user"></a>Löschen eines Benutzers

Wenn Sie einen Benutzer löschen, bleiben alle Entitäten intakt, auf die der Benutzer Zugriff hat. Gespeicherte *persönliche* Berichte werden entfernt, wenn der Benutzer gelöscht wird. Gespeicherte *öffentliche* Berichte, die vom Benutzer erstellt wurden, werden nicht gelöscht.

Sie können sich nicht selbst als Benutzer löschen.

> [!WARNING]
> Wenn Sie einen Benutzer löschen, kann er nicht wiederhergestellt werden.

1.  Klicken Sie im Cloudyn-Portal rechts oben auf das Zahnradsymbol, und wählen Sie anschließend die Option **User Management** (Benutzerverwaltung).
2.  Wählen Sie in der Liste mit den Benutzern den Benutzer aus, den Sie löschen möchten, und klicken Sie dann auf **Benutzer löschen** (Papierkorbsymbol).
3.  Klicken Sie im Feld zum Löschen von Benutzern auf **Ja** und dann auf **OK**.


## <a name="delete-or-export-personal-data"></a>Löschen oder Exportieren von personenbezogenen Daten

Wenn Sie personenbezogene Daten für Cloudyn löschen oder exportieren möchten, müssen Sie ein Supportticket erstellen. Ein erstelltes Supportticket dient als formeller Antrag: ein so genannter Antrag einer betroffenen Person. Microsoft unternimmt dann unmittelbar die Schritte zum Entfernen des Kontos und Löschen von Kundendaten bzw. personenbezogenen Daten. Informationen dazu, wie Sie das Löschen bzw. Exportieren Ihrer Daten anfordern können, finden Sie unter [Data Subject Requests of Cloudyn Data](https://www.cloudyn.com/cloudyn-gdpr-requests) (Anträge betroffener Personen für Cloudyn-Daten).

## <a name="create-and-manage-entities"></a>Erstellen und Verwalten von Entitäten

Wenn Sie Ihre Kostenentitätshierarchie definieren, besteht eine bewährte Methode darin, die Struktur Ihrer Organisation zu identifizieren. Mithilfe von Entitäten können Sie Ausgaben nach einzelnen Konten oder Abonnements gliedern. Sie erstellen Kostenentitäten, um logische Gruppen für die Ausgabenverwaltung und -verfolgung anzulegen. Berücksichtigen Sie beim Erstellen der Struktur, wie die Kosten nach Geschäftseinheiten, Kostenstellen, Umgebungen und Vertriebsabteilungen unterteilt werden sollen oder müssen. Die Entitätsstruktur in Cloudyn ist aufgrund der Entitätsvererbung flexibel.

Einzelne Abonnements für Ihre Cloudkonten sind mit bestimmten Entitäten verknüpft. Sie können eine Entität dem Konto oder Abonnement eines Cloud-Dienstanbieters zuordnen. Entitäten sind daher mehrinstanzenfähig. Sie können mit Entitäten bestimmten Benutzern den Zugriff auf nur ihre Segmente des Unternehmens zuweisen. Auf diese Weise bleiben die Daten isoliert, selbst bei großen Bereichen eines Unternehmens wie Niederlassungen. Datenisolierung trägt zudem zu Governance bei.  

Bei der Registrierung Ihrer Azure-Vereinbarung oder Ihres Azure-Kontos bei Cloudyn wurden Ihre Azure-Ressourcendaten, einschließlich Auslastung, Leistung, Abrechnung und Tagdaten von Ihren Abonnements in Ihr Cloudyn-Konto kopiert. Ihre Entitätsstruktur müssen Sie jedoch manuell erstellen. Wenn Sie die Azure Resource Manager-Registrierung übersprungen haben, stehen nur Abrechnungsdaten und einige Ressourcenberichte im Cloudyn-Portal zur Verfügung.

Klicken Sie im Cloudyn-Portal auf das Zahnradsymbol in der rechten oberen Ecke, und wählen Sie **Cloud Accounts** (Cloudkonten) aus. Sie beginnen mit einer einzigen Entität (Stamm) und erstellen Ihre Entitätsstruktur unter dem Stamm. Hier sehen Sie ein Beispiel für eine Entitätshierarchie, die vielen IT-Organisationen nach Fertigstellung der Struktur ähnelt:

![Beispiel: Entitätsstruktur auf der Seite „Kontenverwaltung“](./media/tutorial-user-access/entity-tree.png)

Klicken Sie neben **Entities** (Entitäten) auf **Add Entity** (Entität hinzufügen). Geben Sie Informationen zu der Person oder Abteilung ein, die Sie hinzufügen möchten. Die Eingaben in den Feldern **Full Name** (Vollständiger Name) und **Email** müssen keinen vorhandenen Benutzern entsprechen. Wenn Sie eine Liste von Zugriffsebenen anzeigen möchten, suchen Sie in der Hilfe nach *Adding an entity* (Hinzufügen einer Entität).

![Beispiel: Entitätsname und Zugriffsebenen im Feld „Entität hinzufügen“](./media/tutorial-user-access/add-entity.png)

**Speichern** Sie nach Abschluss die Entität.

### <a name="entity-access-levels"></a>Zugriffsebenen von Entitäten

Mit Entitätszugriffsebenen und dem Benutzerzugriff können Sie festlegen, welche Art von Aktionen im Cloudyn-Portal verfügbar sind.

- **Enterprise**: Ermöglicht das Erstellen und Verwalten von untergeordneten Kostenentitäten.
- **Enterprise + Cost Allocation** (Enterprise + Kostenzuteilung): Ermöglicht das Erstellen und Verwalten von untergeordneten Kostenentitäten, einschließlich Kostenzuteilung für konsolidierte Konten.
- **Enterprise, Cost based on parent cost allocation** (Enterprise, Kosten basierend auf übergeordneter Kostenzuteilung): Ermöglicht das Erstellen und Verwalten von untergeordneten Kostenentitäten. Die Kosten für das Konto basieren auf dem Kostenzuteilungsmodell des übergeordneten Elements.
- **Custom Dashboards Only** (Nur benutzerdefinierte Dashboards): Dem Benutzer werden nur vordefinierte benutzerdefinierte Dashboards angezeigt.
- **Dashboards Only** (Nur Dashboards): Der Benutzer kann nur Dashboards anzeigen.

### <a name="create-a-cost-entity-hierarchy"></a>Erstellen einer Kostenentitätshierarchie

Zum Erstellen einer Kostenentitätshierarchie benötigen Sie ein Konto mit dem Zugriffstyp „Enterprise“ oder „Enterprise + cost allocation“ (Enterprise + Kostenzuteilung).

Klicken Sie im Cloudyn-Portal auf das Zahnradsymbol in der rechten oberen Ecke, und wählen Sie **Cloud Accounts** (Cloudkonten) aus. Die Struktur **Entitäten** wird im linken Bereich angezeigt. Erweitern Sie ggf. die Entitätsstruktur, damit Sie die Entität anzeigen können, die einem Konto zugeordnet werden soll.  Die Konten des Cloud-Dienstanbieters werden auf Registerkarten im rechten Bereich angezeigt. Wählen Sie eine Registerkarte aus, klicken Sie dann auf ein Konto/Abonnement, und verschieben Sie es per Drag&Drop in eine Entität. Im Feld **Verschieben** werden Sie darüber informiert, dass das Konto verschoben wurde. Klicken Sie auf **OK**.

Sie können einer Entität auch mehrere Konten zuordnen. Wählen Sie die Konten aus, und klicken Sie dann auf **Verschieben**. Wählen Sie im Feld zum Verschieben von Konten die Entität aus, in die Sie das Konto verschieben möchten, und klicken Sie dann auf **Speichern**. Im Feld zum Verschieben von Konten müssen Sie bestätigen, dass Sie die Konten verschieben möchten. Klicken Sie auf **Ja** und dann auf **OK**.

Ein Videotutorial zum Erstellen einer Kostenentitätshierarchie finden Sie unter [Creating a Cost Entity Hierarchy in Cloudyn](https://youtu.be/dAd9G7u0FmU) (Erstellen einer Kostenentitätshierarchie in Cloudyn).

Wenn Sie über ein Azure Enterprise Agreement verfügen, finden Sie ein Videotutorial zum Zuordnen von Konten und Abonnements zu Entitäten unter [Connecting to Azure Resource Manager with Cloudyn](https://youtu.be/oCIwvfBB6kk) (Herstellen einer Verbindung mit Azure Resource Manager mit Cloudyn).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Benutzers mit Administratorzugriff
> * Erstellen eines Benutzers mit Benutzerzugriff
> * Löschen eines Benutzers
> * Löschen oder Exportieren von personenbezogenen Daten
> * Erstellen und Verwalten von Entitäten


Fahren Sie mit dem folgenden Artikel fort, falls Sie den Azure Resource Manager-API-Zugriff für Ihre Konten nicht bereits aktiviert haben.

> [!div class="nextstepaction"]
> [Aktivieren von Azure-Abonnements und -Konten](activate-subs-accounts.md)
