---
title: Typen von Testversionen im kommerziellen Microsoft-Marketplace
description: Typen von Testversionen im kommerziellen Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: trkeya
author: trkeya
ms.openlocfilehash: fd3fa04d2d7e868476838788dd9cf0e27c07aeca
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461761"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager-Testversion

Verwenden Sie diesen Typ, wenn Sie über ein Angebot auf Azure Marketplace oder AppSource verfügen, eine Testversion jedoch nur mit Azure-Ressourcen erstellen möchten. Eine ARM-Vorlage (Azure Resource Manager) ist ein codierter Container mit Azure-Ressourcen, den Sie so entwerfen, dass Ihre Lösung optimal präsentiert wird. Die Testversion übernimmt die bereitgestellte ARM-Vorlage und stellt alle erforderlichen Ressourcen in einer Ressourcengruppe bereit. Dies ist die einzige Testversionsoption für VM- und Azure-App-Angebote.

Wenn Sie mit ARM-Vorlagen nicht vertraut sind, lesen Sie [Was ist Azure Resource Manager?](../azure-resource-manager/management/overview.md) und [Verstehen der Struktur und Syntax von ARM-Vorlagen](../azure-resource-manager/templates/template-syntax.md), um zu erfahren, wie Sie Ihre eigenen Vorlagen erstellen und testen können.

Informationen zu einer **gehosteten** oder **Logik-App**-Testversion finden Sie unter [Was ist eine Testversion?](what-is-test-drive.md)

## <a name="technical-configuration"></a>Technische Konfiguration

Eine Bereitstellungsvorlage enthält alle Azure-Ressourcen, aus denen Ihre Lösung besteht. In den für dieses Szenario geeigneten Produkten werden nur Azure-Ressourcen verwendet. Legen Sie die folgenden Eigenschaften im Partner Center fest:

- **Regionen** (erforderlich): Es gibt derzeit 26 von Azure unterstützte Regionen, in denen Ihre Testversion zur Verfügung gestellt werden kann. In der Regel sollten Sie Ihre Testversion in den Regionen zur Verfügung stellen, in denen Sie die größte Anzahl von Kunden erwarten, damit sie für eine optimale Leistung die nächstgelegene Region auswählen können. Sie müssen sicherstellen, dass in Ihrem Abonnement alle Ressourcen in jeder von Ihnen ausgewählten Region bereitgestellt werden können.

- **Instanzen**: Wählen Sie den Typ („Heiß“ oder „Kalt“) und die Anzahl von verfügbaren Instanzen aus. Diese wird mit der Anzahl der Regionen multipliziert, in denen Ihr Angebot verfügbar ist.

  - **Heiß**: Dieser Instanztyp wird bereits bereitgestellt, und auf ihn kann in jeder ausgewählten Region zugegriffen werden. Die Kunden haben sofort Zugriff auf Instanzen vom Typ *Heiß* und müssen nicht auf die Bereitstellung warten. Der Nachteil dabei ist, dass diese Instanzen immer unter Ihrem Azure-Abonnement ausgeführt werden, sodass höhere Betriebszeitkosten anfallen. Es wird dringend empfohlen, mindestens eine Instanz des Typs *Heiß* festzulegen, da die meisten Kunden nicht auf eine vollständige Bereitstellung warten möchten. Wenn keine Instanz vom Typ *Heiß* verfügbar ist, nimmt die Verwendung durch die Kunden ab.

  - **Kalt**: Dieser Instanztyp stellt die Gesamtzahl von Instanzen dar, die pro Region bereitgestellt werden können. Bei Instanzen des Typs „Kalt“ muss die Bereitstellung der gesamten Resource Manager-Vorlage für die Testversion durchgeführt werden, wenn der Kunde die Testversion anfordert. Daher dauert das Laden von Instanzen des Typs *Kalt* weitaus länger als das Laden von Instanzen des Typs *Heiß*. Sie müssen jedoch nur für die Dauer der Testversion bezahlen. Instanzen vom Typ „Kalt“ werden *nicht* wie Instanzen vom Typ *Heiß* stets im Azure-Abonnement ausgeführt.

- **Azure Resource Manager-Vorlage für Testversion:** Laden Sie die ZIP-Datei mit Ihrer Azure Resource Manager-Vorlage hoch. Im Schnellstartartikel [Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) erhalten Sie weitere Informationen zum Erstellen einer Azure Resource Manager-Vorlage.

    > [!note]
    > Für eine erfolgreiche Veröffentlichung ist es wichtig, die Formatierung der Resource Manager-Vorlage zu überprüfen. Hierfür gibt es zwei Möglichkeiten: (1) Verwendung eines [Online-API-Tools](/rest/api/resources/deployments/validate) oder (2) Durchführung einer [Testbereitstellung](../azure-resource-manager/templates/deploy-portal.md).

- **Dauer der Testversion** (erforderlich): Geben Sie die Anzahl von Stunden ein, für die die Testversion aktiv bleiben soll. Nach Ablauf dieses Zeitraums wird die Testversion automatisch beendet. Verwenden Sie nur ganze Zahlen (z. B. „2“ Stunden ist zulässig, „1,5“ hingegen nicht).

## <a name="write-the-test-drive-template"></a>Schreiben der Testversionsvorlage

Nachdem Sie das gewünschte Ressourcenpaket entworfen haben, schreiben und erstellen Sie die ARM-Vorlage für die Testversion. Da bei einer Testversion Bereitstellungen in einem vollständig automatisierten Modus ausgeführt werden, gelten für Testversionsvorlagen einige Einschränkungen:

### <a name="parameters"></a>Parameter

Die meisten Vorlagen verfügen über eine Reihe von Parametern, mit denen Ressourcennamen, Ressourcengrößen (z. B. Typen von Speicherkonten oder VM-Größen), Benutzernamen und Kennwörter, DNS-Namen usw. definiert werden. Wenn Sie Lösungen über das Azure-Portal bereitstellen, können Sie alle diese Parameter manuell eingeben, verfügbare DNS-Namen oder Speicherkontennamen auswählen usw.

![Liste der Parameter in einer Azure Resource Manager-Vorlage](media/test-drive/resource-manager-parameters.png)

Da eine Testversion jedoch automatisiert ohne Benutzerinteraktion ausgeführt wird, wird nur eine Teilmenge von Parameterkategorien unterstützt. Wenn ein Parameter in der ARM-Vorlage für die Testversion nicht unter eine der unterstützten Kategorien fällt, müssen Sie diesen Parameter durch einen Variablen- oder Konstantenwert ersetzen.

Sie können jeden gültigen Namen für die Parameter verwenden. In der Testversion wird die Parameterkategorie anhand des Metadatentypwerts erkannt. Geben Sie den Metadatentyp für jeden Vorlagenparameter an, andernfalls besteht die Vorlage die Überprüfung nicht:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> Alle Parameter sind optional. Sie müssen also keine Parameter verwenden, wenn Sie nicht möchten.

### <a name="accepted-parameter-metadata-types"></a>Unterstützte Metadatentypen für Parameter

| Metadatentyp   | Parametertyp  | BESCHREIBUNG     | Beispielwert    |
|---|---|---|---|
| **baseuri**     | Zeichenfolge          | Basis-URI des Bereitstellungspakets| `https:\//\<\..\>.blob.core.windows.net/\<\..\>` |
| **username**    | Zeichenfolge          | Neuer zufälliger Benutzername| admin68876      |
| **password**    | Sichere Zeichenfolge    | Neues zufälliges Kennwort | Lp!ACS\^2kh     |
| **session id**   | Zeichenfolge          | Eindeutige Sitzungs-ID für Testversion (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

In der Testversion wird dieser Parameter mit einem **Basis-URI** des Bereitstellungspakets initialisiert, sodass Sie mit diesem Parameter den URI einer beliebigen im Paket enthaltenen Datei erstellen können.

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Verwenden Sie den Parameter in Ihrer Vorlage, um für beliebige im Bereitstellungspaket für die Testversion enthaltene Dateien einen URI zu erstellen. Im folgenden Beispiel wird gezeigt, wie ein URI der verknüpften Vorlage erstellt wird:

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

In der Testversion wird dieser Parameter mit einem neuen zufälligen Benutzernamen initialisiert:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Beispielwert: `admin68876`

Sie können zufällige oder konstante Benutzernamen für Ihre Lösung verwenden.

#### <a name="password"></a>password

In der Testversion wird dieser Parameter mit einem neuen zufälligen Kennwort initialisiert:

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Beispielwert: `Lp!ACS^2kh`

Sie können zufällige oder konstante Kennwörter für Ihre Lösung verwenden.

#### <a name="session-id"></a>session id

In der Testversion wird dieser Parameter mit einer eindeutigen GUID initialisiert, mit der die Sitzungs-ID der Testversion angegeben wird:

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

Beispielwert: `b8c8693e-5673-449c-badd-257a405a6dee`

Mit diesem Parameter können Sie bei Bedarf die Sitzung der Testversion eindeutig identifizieren.

### <a name="unique-names"></a>Eindeutige Namen

Für einige Azure-Ressourcen, z.B. Speicherkonten oder DNS-Namen, müssen global eindeutige Namen verwendet werden. Das bedeutet, dass jedes Mal, wenn die Testversion die ARM-Vorlage bereitstellt, eine neue Ressourcengruppe mit einem eindeutigen Namen für alle zugehörigen Ressourcen erstellt wird. Daher müssen Sie die Funktion [uniquestring](../azure-resource-manager/templates/template-functions.md) verwenden, die mit den Variablennamen für Ressourcengruppen-IDs verkettet ist, damit zufällige eindeutige Werte generiert werden:

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Verketten Sie unbedingt die Parameter- und Variablenzeichenfolgen (`contosovm`) mit einer eindeutigen Ausgabezeichenfolge (`resourceGroup().id`), da so die Eindeutigkeit und Zuverlässigkeit der einzelnen Variablen sichergestellt wird.

Beispielsweise dürfen die meisten Ressourcennamen nicht mit einer Ziffer beginnen, eine eindeutige Zeichenfolgenfunktion kann jedoch eine Zeichenfolge zurückgeben, die mit einer Ziffer beginnt. Wenn Sie eindeutige Ausgaberohzeichenfolgen verwenden, treten daher bei den Bereitstellungen Fehler auf.

Weitere Informationen zu Benennungsregeln und Einschränkungen für Ressourcen finden Sie in [diesem Artikel](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Bereitstellungsort

Sie können die Testversion in verschiedenen Azure-Regionen zur Verfügung stellen. Benutzer sollen die nächstgelegene Region auswählen und so die bestmögliche Umgebung nutzen können.

Wenn in der Testversion eine Instanz des Labs erstellt wird, wird immer eine Ressourcengruppe in der von einem Benutzer ausgewählten Region erstellt und dann die Bereitstellungsvorlage in diesem Gruppenkontext ausgeführt. Daher sollte in Ihrer Vorlage der Bereitstellungsstandort in der Ressourcengruppe ausgewählt werden:

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Dieser Standort ist dann für jede Ressource für eine bestimmte Lab-Instanz zu verwenden:

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Stellen Sie sicher, dass alle Ressourcen, die Sie bereitstellen möchten, in allen ausgewählten Regionen für Ihr Abonnement bereitgestellt werden können. Zudem müssen Sie sicherstellen, dass Ihre VM-Images in allen Regionen verfügbar sind, die Sie aktivieren möchten, andernfalls funktioniert die Bereitstellungsvorlage in einigen Regionen nicht.

### <a name="outputs"></a>Ausgaben

Normalerweise können Sie mit Resource Manager-Vorlagen die Bereitstellung durchführen, ohne dass eine Ausgabe generiert wird. Dies ist möglich, da Sie alle Werte kennen, die Sie zum Ausfüllen der Vorlagenparameter verwenden, und Sie die Eigenschaften der Ressourcen immer manuell überprüfen können.

Bei den Resource Manager-Vorlagen für die Testversion ist es jedoch wichtig, dass alle Informationen an die Testversion zurückgegeben werden. Dies ist erforderlich, um Zugriff auf das Lab (Website-URIs, VM-Hostnamen, Benutzernamen und Kennwörter) zu erhalten. Stellen Sie sicher, dass alle Ausgabenamen verständlich sind, da diese Variablen für den Kunden sichtbar sind.

Für Vorlagenausgaben gelten keine Einschränkungen. Alle Ausgabewerte in der Testversion werden in Zeichenfolgen konvertiert. Wenn Sie also ein Objekt an die Ausgabe senden, wird für den Benutzer eine JSON-Zeichenfolge angezeigt.

Beispiel:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Grenzwerte für Abonnements

Berücksichtigen Sie die Einschränkungen von Abonnements und Diensten. Wenn Sie z. B. bis zu zehn virtuelle Computer mit 4 Kernen bereitstellen möchten, müssen Sie sich vergewissern, dass in dem Abonnement, das Sie für das Lab verwenden, 40 Kerne verwendet werden können. Weitere Informationen zu den Einschränkungen bei Azure-Abonnement und -Dienst finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md). Da mehrere Testversionen gleichzeitig ausgeführt werden können, sollten Sie überprüfen, ob in Ihrem Abonnement die Anzahl der Kerne multipliziert mit der Gesamtzahl der Testversionen, die gleichzeitig ausgeführt werden können, verarbeitet werden kann.

### <a name="what-to-upload"></a>Hochladen der Vorlage

Die ARM-Vorlage für die Testversion wird als ZIP-Datei hochgeladen, die verschiedene Bereitstellungsartefakte enthalten kann, jedoch auf jedem Fall eine Datei mit dem Namen `main-template.json` enthalten muss. Dies ist die ARM-Bereitstellungsvorlage, die von der Testversion zum Instanziieren eines Labs verwendet wird. Wenn Sie neben dieser Datei weitere Ressourcen verwenden, können Sie in der Vorlage auf diese als externe Ressourcen verweisen oder sie in die ZIP-Datei einfügen.

Bei der Veröffentlichungszertifizierung werden das Bereitstellungspaket in der Testversion entzippt und die zugehörigen Inhalte in einem internen Blobcontainer der Testversion eingefügt. Die Containerstruktur entspricht der Struktur des Bereitstellungspakets:

| package.zip                       | Blobcontainer der Testversion         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

Der URI dieses Blobcontainers wird als Basis-URI bezeichnet. Da jede Version des Labs über einen eigenen Blobcontainer verfügt, ist jeder Version des Labs auch ein eigener Basis-URI zugewiesen. In der Testversion kann ein Basis-URI des entpackten Bereitstellungspakets über Vorlagenparameter an die Vorlage übergeben werden.

### <a name="transform-template-examples-for-test-drive"></a>Transformieren von Vorlagenbeispielen für die Testversion

Der Vorgang des Transformierens einer Ressourcenarchitektur in eine Resource Manager-Vorlage für die Testversion kann kompliziert erscheinen. Weitere Informationen finden Sie in diesen Beispielen für eine optimale Transformation aktueller Bereitstellungsvorlagen unter [Was ist eine Testversion?](what-is-test-drive.md#transforming-examples)

## <a name="test-drive-deployment-subscription-details"></a>Abonnementdetails für die Bereitstellung der Testversion

Im letzten Abschnitt werden die Eingaben vorgenommen, um die Testversionen durch Herstellen einer Verbindung mit Ihrem Azure-Abonnement und mit Azure Active Directory (AD) automatisch bereitzustellen.

![Abonnementdetails für die Bereitstellung der Testversion](media/test-drive/deployment-subscription-details.png)

1. Rufen Sie eine **Azure-Abonnement-ID** ab. Diese gewährt Zugriff auf Azure-Dienste und das Azure-Portal. Im Abonnement werden die Ressourcenverwendung protokolliert und die Dienste abgerechnet. Wenn Sie noch nicht über ein separates Azure-Abonnement nur für Testversionen verfügen, erstellen Sie nun eines. Sie finden Azure-Abonnement-IDs (z. B. `1a83645ac-1234-5ab6-6789-1h234g764ghty1`), indem Sie sich beim Azure-Portal anmelden und im linken Navigationsmenü **Abonnements** auswählen.

   ![Azure-Abonnements](media/test-drive/azure-subscriptions.png)

2. Rufen Sie eine **Azure AD-Mandanten-ID** ab. Wenn Sie bereits über eine Mandanten-ID verfügen, finden Sie sie im Azure-Portal unter **Azure Active Directory** > **Eigenschaften** > **Verzeichnis-ID**:

   ![Azure Active Directory-Eigenschaften](media/test-drive/azure-active-directory-properties.png)

   Wenn Sie über keine Mandanten-ID verfügen, erstellen Sie eine neue in Azure Active Directory. Wenn Sie Hilfe bei der Einrichtung benötigen, finden Sie weitere Informationen unter [Schnellstart: Einrichten eines Mandanten](../active-directory/develop/quickstart-create-new-tenant.md).

3. **Azure AD-App-ID**: Erstellen und registrieren Sie eine neue Anwendung. In dieser Anwendung werden Vorgänge in Ihrer Testversionsinstanz ausgeführt.

   1. Navigieren Sie zu dem neu erstellten oder einem bereits vorhandenen Verzeichnis, und wählen Sie Azure Active Directory im Filterbereich aus.
   2. Suchen Sie **App-Registrierungen**, und wählen Sie **Hinzufügen** aus.
   3. Geben Sie einen Anwendungsnamen an.
   4. Wählen Sie den **Typ** **Web-App/API** aus.
   5. Geben Sie einen beliebigen Wert in der Anmelde-URL an. Dieses Feld wird nicht verwendet.
   6. Klicken Sie auf **Erstellen**.
   7. Wählen Sie nach der Erstellung der Anwendung **Eigenschaften** > **Set the application as multi-tenant** (Anwendung als mehrinstanzenfähig festlegen) und dann **Speichern** aus.

4. Wählen Sie **Speichern** aus.

5. Kopieren Sie die Anwendungs-ID für diese registrierte App, und fügen Sie sie im Feld der Testversion ein.

   ![ID der Azure AD-Anwendung](media/test-drive/azure-ad-application-id-detail.png)

6. Da die Anwendung zum Durchführen der Bereitstellung im Abonnement verwendet wird, muss die Anwendung im Abonnement als Mitwirkender hinzugefügt werden:

   1. Wählen Sie den Typ von **Abonnement** aus, den Sie für die Testversion verwenden.
   1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.
   1. Wählen Sie die Registerkarte **Rollenzuweisungen** und dann **Rollenzuweisung hinzufügen** aus.

      ![Hinzufügen eines neuen Zugriffssteuerungsprinzipals](media/test-drive/access-control-principal.jpg)

   1. Legen Sie **Rolle** und **Zugriff zuweisen zu** wie dargestellt fest. Geben Sie im Feld **Auswählen** den Namen der Azure AD-Anwendung ein. Wählen Sie die Anwendung aus, der Sie die Rolle **Mitwirkender** zuweisen möchten.

      ![Hinzufügen der Berechtigungen](media/test-drive/access-control-permissions.jpg)

   1. Wählen Sie **Speichern** aus.

7. Generieren Sie einen Authentifizierungsschlüssel für die **Azure AD-App**. Fügen Sie unter **Schlüssel** eine **Schlüsselbeschreibung** hinzu, legen Sie die Dauer auf **Läuft nie ab** fest (ein abgelaufener Schlüssel führt dazu, dass die Testversion in der Produktion nicht mehr funktioniert), und wählen Sie **Speichern** aus. Kopieren Sie diesen Wert, und fügen Sie ihn im entsprechenden Feld für die Testversion ein.

![Schlüssel für die Azure AD-Anwendung](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>Erneut veröffentlichen

Nachdem Sie alle Felder für Ihre Testversion ausgefüllt haben, können Sie Ihr Angebot **erneut veröffentlichen**. Nachdem Ihre Testversion die Zertifizierung bestanden hat, testen Sie Ihr Angebot in der Vorschau:

1. Starten Sie eine Testversion in der Benutzeroberfläche.
1. Öffnen Sie Ihr Azure-Abonnement im Azure-Portal.
1. Vergewissern Sie sich, dass die Testversion ordnungsgemäß bereitgestellt wird.

   ![Azure-Portal](media/test-drive/azure-portal.png)

Löschen Sie keine Testversionsinstanzen, die für Kunden bereitgestellt wurden. Der Dienst der Testversion entfernt diese Ressourcengruppen automatisch, wenn Kunden die Testversion nicht mehr verwenden.

Wenn Sie mit Ihrer Vorschauversion zufrieden sind, können Sie sie **veröffentlichen**. Als letzten Schritt im Überprüfungsprozess wird die gesamte End-to-End-Benutzererfahrung kontrolliert. Wenn wir das Angebot ablehnen, senden wir eine E-Mail an den technischen Kontakt für Ihr Angebot, in dem erläutert wird, was korrigiert werden muss.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie die Anweisungen zum Erstellen Ihres Angebots im Partner Center aufgerufen haben, verwenden Sie den Zurück-Pfeil, um zu diesem Thema zurückzukehren.
- Weitere Informationen zu anderen Testversionen finden Sie unter [Was ist eine Testversion?](what-is-test-drive.md)