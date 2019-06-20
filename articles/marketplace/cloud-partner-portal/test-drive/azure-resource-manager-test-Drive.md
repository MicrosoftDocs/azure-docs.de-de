---
title: Erstellen einer Azure Resource Manager-Testversion | Azure Marketplace
description: Erstellen einer Marketplace-Testversion mithilfe von Azure Resource Manager
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Patrick .Butler
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 92c55c7f15b3f350ad802157bf401f3e75983789
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606440"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager-Testversion

Dieser Artikel richtet sich an Herausgeber, die ihr Angebot im Azure Marketplace veröffentlichen oder die in AppSource präsent sind, ihre Testversion jedoch nur mit Azure-Ressourcen erstellen möchten.

Eine Azure Resource Manager-Vorlage ist ein codierter Container mit Azure-Ressourcen, den Sie entwerfen, um Ihre Lösung bestmöglich darzustellen. Wenn Sie nicht mit Resource Manager-Vorlagen vertraut sind, finden Sie unter [Grundlegendes zu Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) und [Erstellen von Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) Informationen zum Erstellen und Testen eigener Vorlagen.

In der Testversion wird die bereitgestellte Resource Manager-Vorlage verwendet und eine Bereitstellung aller erforderlichen Ressourcen aus dieser Resource Manager-Vorlage in einer Ressourcengruppe erstellt.

Wenn Sie eine Azure Resource Manager-Testversion erstellen möchten, müssen Sie folgende Schritte ausführen:

- Erstellen, Testen und Hochladen der Resource Manager-Vorlage für Ihre Testversion
- Konfigurieren aller erforderlichen Metadaten und Einstellungen zum Aktivieren der Testversion
- Erneutes Veröffentlichen Ihres Angebots mit der aktivierten Testversion

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Erstellen einer Azure Resource Manager-Testversion

Der Prozess zum Erstellen einer Azure Resource Manager-Testversion besteht aus folgenden Schritten:

1. Entwerfen Sie in einem Flussdiagramm, was Ihre Kunden tun sollen.
1. Definieren Sie, welche Funktionalität Ihre Kunden erstellen sollen.
1. Entscheiden Sie anhand der oben beschriebenen Definitionen, welche Komponenten und Ressourcen der Kunde zum Erreichen dieser Funktionalität benötigt (z.B. Dynamics 365-Instanz oder Website mit Datenbank).
1. Erstellen Sie den Entwurf lokal, und testen Sie die Funktionalität.
1. Packen Sie die Funktionalität in eine ARM-Vorlagenbereitstellung, und führen Sie hier die folgenden Schritte aus:
    1. Definieren Sie Folgendes: Welche Teile der Ressourcen sind Eingabeparameter,
    1. welche Teile sind Variablen,
    1. welche Ausgaben soll die Kundenfunktionalität erhalten.
1. Veröffentlichen und testen Sie die Testversion, und lassen Sie sie live schalten.

Der wichtigste Teil beim Erstellen einer Azure Resource Manager-Testversion ist die Definition der Szenarien für Ihre Kunden. Handelt es sich um ein Firewallprodukt, und möchten Sie zeigen, wie gut Angriffe durch Skripteinschleusung abgewehrt werden? Handelt es sich um ein Speicherprodukt, und soll veranschaulicht werden, wie schnell und einfach Dateien in der Lösung komprimiert werden?

Nehmen Sie sich genügend Zeit, um alle Möglichkeiten auszuwerten, wie Sie Ihr Produkt bestmöglich präsentieren können. Achten Sie besonders auf alle erforderlichen Ressourcen, die Sie benötigen, da sich dadurch das Packen der Resource Manager-Vorlage wesentlich vereinfacht.

Um das Firewallbeispiel fortzusetzen: Möglicherweise benötigen Sie in der Architektur eine öffentliche IP-Adresse für den Dienst und eine andere öffentliche IP-Adresse für die Website, die durch die Firewall geschützt wird. Jede IP-Adresse wird auf einem virtuellen Computer bereitgestellt und mit einer Netzwerksicherheitsgruppe und einer Netzwerkschnittstelle verbunden.

Nachdem Sie das gewünschte Ressourcenpaket entworfen haben, schreiben und erstellen Sie die Resource Manager-Vorlage für die Testversion.

## <a name="writing-test-drive-resource-manager-templates"></a>Schreiben von Resource Manager-Vorlagen für die Testversion

In der Testversion werden Bereitstellungen in einem vollständig automatisierten Modus ausgeführt. Daher gelten für die Testversionsvorlagen einige Einschränkungen, die nachfolgend beschrieben werden.

### <a name="parameters"></a>Parameter

Die meisten Vorlagen umfassen eine Gruppe von Parametern. Parameter definieren Ressourcennamen, Ressourcengrößen (z.B. Typen von Speicherkonten oder VM-Größen), Benutzernamen und Kennwörter, DNS-Namen usw. Wenn Sie Lösungen über das Azure-Portal bereitstellen, können Sie alle diese Parameter manuell eingeben, verfügbare DNS-Namen oder Speicherkontennamen auswählen usw.

![Liste der Parameter in einer Azure Resource Manager-Vorlage](./media/azure-resource-manager-test-drive/param1.png)

Da eine Testversion jedoch in einem vollständig automatisierten Modus ohne Benutzerinteraktion ausgeführt wird, wird nur eine begrenzte Gruppe von Parameterkategorien unterstützt. Wenn ein Parameter in der Resource Manager-Vorlage für die Testversion nicht unter eine der unterstützten Kategorien fällt, müssen Sie **diesen Parameter durch einen Variablen- oder Konstantenwert ersetzen**.

Sie können jeden gültigen Namen für die Parameter verwenden. In der Testversion wird die Parameterkategorie anhand des Metadatentypwerts erkannt. Sie **müssen den Metadatentyp für jeden Vorlagenparameter angeben**, andernfalls besteht die Vorlage die Überprüfung nicht:

```json
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

Zudem ist zu beachten, dass **alle Parameter optional sind**. Wenn Sie also keine Parameter verwenden möchten, ist dies nicht erforderlich.

### <a name="accepted-parameter-metadata-types"></a>Unterstützte Metadatentypen für Parameter

| Metadatentyp   | Parametertyp  | BESCHREIBUNG     | Beispielwert    |
|---|---|---|---|
| **baseuri**     | Zeichenfolge          | Basis-URI des Bereitstellungspakets| https:\//\<\..\>.blob.core.windows.net/\<\..\> |
| **username**    | Zeichenfolge          | Neuer zufälliger Benutzername| admin68876      |
| **password**    | Sichere Zeichenfolge    | Neues zufälliges Kennwort | Lp!ACS\^2kh     |
| **session id**   | Zeichenfolge          | Eindeutige Sitzungs-ID für Testversion (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>userName

In der Testversion wird dieser Parameter mit einem **Basis-URI** des Bereitstellungspakets initialisiert, sodass Sie mit diesem Parameter den URI einer beliebigen in dem Paket enthaltenen Datei erstellen können.

```json
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

In Ihrer Vorlage können Sie mit diesem Parameter einen URI einer in dem Bereitstellungspaket für die Testversion enthaltenen Datei erstellen. Im folgenden Beispiel wird gezeigt, wie ein URI der verknüpften Vorlage erstellt wird:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>userName

In der Testversion wird dieser Parameter mit einem neuen zufälligen Benutzernamen initialisiert:

```json
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

Beispielwert:

    admin68876

Sie können zufällige oder konstante Benutzernamen für Ihre Lösung verwenden.

#### <a name="password"></a>password

In der Testversion wird dieser Parameter mit einem neuen zufälligen Kennwort initialisiert:

```json
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

Beispielwert:

    Lp!ACS^2kh

Sie können zufällige oder konstante Kennwörter für Ihre Lösung verwenden.

#### <a name="session-id"></a>session id

In der Testversion wird dieser Parameter mit einer eindeutigen GUID initialisiert, die die Sitzungs-ID der Testversion angibt:

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

Beispielwert:

    b8c8693e-5673-449c-badd-257a405a6dee

Mit diesem Parameter können Sie bei Bedarf die Sitzung der Testversion eindeutig identifizieren.

### <a name="unique-names"></a>Eindeutige Namen

Für einige Azure-Ressourcen, z.B. Speicherkonten oder DNS-Namen, müssen global eindeutige Namen verwendet werden.

Das bedeutet, dass jedes Mal, wenn die Testversion die Resource Manager-Vorlage bereitstellt, eine **neue Ressourcengruppe mit einem eindeutigen Namen** für alle zugehörigen Ressourcen erstellt wird. Daher muss die Funktion [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) verwendet werden, die mit den Variablennamen für Ressourcengruppen-IDs verkettet ist, damit zufällige eindeutige Werte generiert werden:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Verketten Sie die Parameter- und Variablenzeichenfolgen (\'contosovm\') mit einer eindeutigen Ausgabezeichenfolge (\'resourceGroup().id\'), da so die Eindeutigkeit und Zuverlässigkeit der einzelnen Variablen sichergestellt wird.

Beispielsweise dürfen die meisten Ressourcennamen nicht mit einer Ziffer beginnen, eine eindeutige Zeichenfolgenfunktion kann jedoch eine Zeichenfolge zurückgeben, die mit einer Ziffer beginnt. Wenn Sie eindeutige Ausgaberohzeichenfolgen verwenden, treten daher bei den Bereitstellungen Fehler auf. 

Weitere Informationen zu Benennungsregeln und Einschränkungen für Ressourcen finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/guidance/guidance-naming-conventions).

### <a name="deployment-location"></a>Bereitstellungsort

Sie können die Testversion in verschiedenen Azure-Regionen zur Verfügung stellen. Benutzer sollen die nächstgelegene Region auswählen und so die bestmögliche Umgebung nutzen können.

Wenn in der Testversion eine Instanz des Labs erstellt wird, wird immer eine Ressourcengruppe in der von einem Benutzer ausgewählten Region erstellt und dann die Bereitstellungsvorlage in diesem Gruppenkontext ausgeführt. Daher sollte in Ihrer Vorlage der Bereitstellungsstandort in der Ressourcengruppe ausgewählt werden:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Dieser Standort ist dann für jede Ressource für eine bestimmte Lab-Instanz zu verwenden:

```json
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

Sie müssen sicherstellen, dass in Ihrem Abonnement alle Ressourcen, die Sie bereitstellen möchten, in allen ausgewählten Ressourcen bereitgestellt werden können. Zudem müssen Sie sicherstellen, dass Ihre VM-Images in allen aktivierten Regionen verfügbar sind, andernfalls wird die Bereitstellungsvorlage in einigen Regionen nicht ausgeführt.

### <a name="outputs"></a>Ausgaben

Normalerweise können Sie mit Resource Manager-Vorlagen die Bereitstellung durchführen, ohne dass eine Ausgabe generiert wird. Dies ist möglich, da Sie alle Werte kennen, die Sie zum Ausfüllen der Vorlagenparameter verwenden, und Sie die Eigenschaften der Ressourcen immer manuell überprüfen können.

Bei den Resource Manager-Vorlagen für die Testversion ist es jedoch wichtig, dass alle Informationen an die Testversion zurückgegeben werden. Dies ist erforderlich, um Zugriff auf das Lab (Website-URIs, VM-Hostnamen, Benutzernamen und Kennwörter) zu erhalten. Stellen Sie sicher, dass alle Ausgabenamen lesbar sind, da diese Variablen für den Kunden angezeigt werden.

Für Vorlagenausgaben gelten keine Einschränkungen. Es ist lediglich zu beachten, dass alle Ausgabewerte in der Testversion in **Zeichenfolgen** konvertiert werden. Wenn Sie also ein Objekt an die Ausgabe senden, wird für den Benutzer eine JSON-Zeichenfolge angezeigt.

Beispiel:

```json
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

Ein weiterer zu berücksichtigender Aspekt sind die Limits für Abonnements und Dienste. Wenn Sie z.B. bis zu zehn virtuelle Computer mit 4 Kernen bereitstellen möchten, müssen Sie sich vergewissern, dass in dem Abonnement, das Sie für das Lab verwenden, 40 Kerne verwendet werden können.

Weitere Informationen zu Grenzwerten für Azure-Abonnements und Azure-Dienste in [diesem Artikel](https://docs.microsoft.com/azure/azure-subscription-service-limits). Da mehrere Testversionen gleichzeitig ausgeführt werden können, sollten Sie überprüfen, ob in Ihrem Abonnement die Anzahl der Kerne multipliziert mit der Gesamtzahl der Testversionen, die gleichzeitig ausgeführt werden können, verarbeitet werden kann.

### <a name="what-to-upload"></a>Hochladen der Vorlage

Die Resource Manager-Vorlage für die Testversion wird als ZIP-Datei hochgeladen, die verschiedene Bereitstellungsartefakte enthalten kann, jedoch eine Datei mit dem Namen **main-template.json** enthalten muss. Bei dieser Datei handelt es sich um die Azure Resource Manager-Bereitstellungsvorlage, über die in der Testversion ein Lab instanziiert wird.

Wenn Sie neben dieser Datei weitere Ressourcen festgelegt haben, können Sie in der Vorlage auf sie als externe Ressourcen verweisen oder sie in der ZIP-Datei einfügen.

Bei der Veröffentlichungszertifizierung werden das Bereitstellungspaket in der Testversion entzippt und die zugehörigen Inhalte in einem internen Blobcontainer der Testversion eingefügt. Die Containerstruktur entspricht der Struktur des Bereitstellungspakets:

| package.zip                       | Blobcontainer der Testversion         |
|---|---|
| main-template.json                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json  |
| templates/solution.json           | https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json |
| scripts/warmup.ps1                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1  |


Der URI dieses Blobcontainers wird als Basis-URI bezeichnet. Jeder Version des Labs ist ein eigener Blobcontainer zugewiesen. Daher ist jeder Version des Labs auch ein eigener Basis-URI zugewiesen. In der Testversion kann ein Basis-URI des entpackten Bereitstellungspakets über Vorlagenparameter in die Vorlage übergeben werden.

## <a name="transforming-template-examples-for-test-drive"></a>Transformieren von Vorlagenbeispielen für die Testversion

Der Vorgang zum Transformieren einer Ressourcenarchitektur in einer Resource Manager-Vorlage für die Testversion kann schwierig sein. Zur Vereinfachung dieses Vorgangs haben wir [hier](./transforming-examples-for-test-drive.md) Beispiele für die optimale Transformation aktueller Bereitstellungsvorlagen erstellt.

## <a name="how-to-publish-a-test-drive"></a>Veröffentlichen einer Testversion

Nachdem Sie Ihre Testversion erstellt haben, werden in diesem Abschnitt die einzelnen Felder beschrieben, für die Sie Eingaben vornehmen müssen, damit Sie die Testversion erfolgreich veröffentlichen können.

![Aktivieren der Testversion auf der Benutzeroberfläche](./media/azure-resource-manager-test-drive/howtopub1.png)

Im ersten und wichtigsten Feld müssen Sie festlegen, ob die Testversion für Ihr Angebot aktiviert werden soll. Wenn Sie **Ja** auswählen, wird das restliche Formular mit allen erforderlichen Feldern angezeigt, die Sie ausfüllen müssen. Wenn Sie **Nein** auswählen, wird das Formular deaktiviert. Wenn Sie die Veröffentlichung dann erneut mit der deaktivierten Testversion durchführen, wird die Testversion aus der Produktion entfernt.

Hinweis: Wenn Testversionen von Benutzern aktiv verwendet werden, werden diese Testversionen weiterhin ausgeführt, bis die Sitzung der Benutzer abgelaufen ist.

### <a name="details"></a>Details

Im nächsten Abschnitt werden die Details zu Ihrem Angebot der Testversion ausgefüllt.

![Detaillierte Informationen zur Testversion](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beschreibung:** *Erforderlich* Hier geben Sie die Beschreibung der Inhalte der Testversion ein. Hier kann der Kunde nachlesen, welche Szenarien die Testversion für Ihr Produkt abdeckt. 

**Benutzerhandbuch:** *Erforderlich* Dies ist die ausführliche Beschreibung zur Verwendung Ihrer Testversion. Mit diesem Benutzerhandbuch kann der Kunde genau die vorgesehenen Schritte in der Testversion ausführen und durchlaufen. Es ist wichtig, dass die Inhalte einfach nachzuvollziehen und auszuführen sind. (Es muss sich um eine PDF-Datei handeln.)

**Test Drive Demo Video** (Demovideo für Testversion): *Empfohlen* Ähnlich dem Benutzerhandbuch empfiehlt es sich, ein Videotutorial für Ihre Testversion einzufügen. Der Kunde sieht sich dieses Tutorial vor oder während der Verwendung der Testversion an und kann genau die vorgesehenen Schritte in der Testversion ausführen und durchlaufen. Es ist wichtig, dass die Inhalte einfach nachzuvollziehen und auszuführen sind.

- **Name:** Titel Ihres Videos
- **Link:** muss eine eingebettete URL von YouTube oder Vimeo sein. Ein Beispiel zum Abrufen der eingebetteten URL ist nachfolgend aufgeführt:
- **Miniaturansicht:** muss ein hochwertiges Bild sein (533 x 324 Pixel). Es wird empfohlen, hier einen Screenshot eines Teils der Testversion einzufügen.

Unten wird gezeigt, wie diese Felder in der Testversion von Kunden angezeigt werden.

![Position der Felder der Testversion im Marketplace-Angebot](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technische Konfiguration

Im nächsten Abschnitt geben Sie an, an welchen Speicherort die Resource Manager-Vorlage für die Testversion hochgeladen wird, und definieren die spezielle Ausführung der Testversioninstanzen.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instanzen:** *Erforderlich* Hier konfigurieren Sie die Anzahl der gewünschten Instanzen, die Regionen und wie schnell Kunden die Testversion erhalten können.

- **Instanzen:** Über „Regionen auswählen“ legen Sie fest, in welchen Regionen die Resource Manager-Vorlage für die Testversion bereitgestellt wird. Es empfiehlt sich, nur eine Region auszuwählen, in der sich erwartungsgemäß die meisten Ihrer Kunden befinden.
- **Heiß:** Anzahl der Testversioninstanzen, die pro ausgewählter Region bereits bereitgestellt sind und auf Zugriff warten. Kunden haben sofort Zugriff auf diese Testversionen und müssen nicht auf die Bereitstellung warten. Der Nachteil dabei ist, dass diese Instanzen immer unter Ihrem Azure-Abonnement ausgeführt werden, sodass höhere Betriebszeitkosten anfallen. Es wird dringend empfohlen, **mindestens eine Instanz des Typs „Heiß“** festzulegen, da die meisten Kunden nicht warten möchten, bis die vollständige Bereitstellung abgeschlossen ist, und somit die Verwendung der Testversion durch Kunden sinkt.
- **Warm:** Anzahl der Testversioninstanzen pro Region, die bereitgestellt wurden und für die der virtuelle Computer beendet und in Azure Storage gespeichert wurde. Die Wartezeit bei Instanzen des Typs „Warm“ ist länger als bei Instanzen des Typs „Heiß“, dagegen sind jedoch die Betriebszeitkosten für die Speicherung auch weniger hoch.
- **Kalt:** Anzahl der Testversioninstanzen pro Region, die bereitgestellt werden können. Bei Instanzen des Typs „Kalt“ muss die Bereitstellung der gesamten Resource Manager-Vorlage für die Testversion zu dem Zeitpunkt durchgeführt werden, zu dem der Kunde die Testversion anfordert. Der Vorgang dauert daher länger als bei Instanzen des Typs „Heiß“ oder „Warm“. Es fallen jedoch nur für die Dauer der aktiven Testversion Kosten an.

Berechnen Sie nun die Gesamtanzahl der möglichen gleichzeitig ausführbaren Testversionen, die Sie zur Verfügung stellen, und überprüfen Sie, ob diese Zahl innerhalb der Kontingentgrenze Ihres Abonnements liegt:

**(Anzahl der ausgewählten Regionen x Instanzen des Typs „Heiß“) + (Anzahl der ausgewählten Regionen x Instanzen des Typs „Warm“) + (Anzahl der ausgewählten Regionen x Instanzen des Typs „Kalt“)**

**Dauer der Testversion (Stunden):** *Erforderlich* Zeitraum, in dem die Testversion aktiv bleibt, angegeben in Stunden. Nach Ablauf dieses Zeitraums wird die Testversion automatisch beendet.

**Test Drive Resource Manager template** (Resource Manager-Vorlage für Testversion): *Erforderlich* Laden Sie hier Ihre Resource Manager-Vorlage hoch. Dies ist die Datei, die Sie im vorherigen Abschnitt oben erstellt haben. Geben Sie der Hauptvorlagendatei den Namen „main-template.json“, und stellen Sie sicher, dass die Resource Manager-Vorlage Ausgabeparameter für erforderliche Hauptvariablen enthält. (Es muss sich um eine ZIP-Datei handeln.)

**Zugriffsinformationen:** *Erforderlich* Nachdem ein Kunde seine Testversion erhalten hat, erhält er auch die Zugriffsinformationen. In diesen Anweisungen können die nützlichen Ausgabeparameter aus der Resource Manager-Vorlage für die Testversion angegeben werden. Verwenden Sie zum Einfügen von Ausgabeparametern doppelte geschweifte Klammern (z.B. **{{Ausgabename}}** ). So werden sie am Speicherort korrekt eingefügt. (Zur Darstellung im Front-End wird die HTML-Formatierung von Zeichenfolgen empfohlen.)

### <a name="test-drive-deployment-subscription-details"></a>Abonnementdetails für Bereitstellung der Testversion

Im letzten Abschnitt werden die Eingaben vorgenommen, um die Testversionen durch Herstellen einer Verbindung mit Ihrem Azure-Abonnement und mit Azure Active Directory (AD) automatisch bereitzustellen.

![Abonnementdetails für Bereitstellung der Testversion](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-Abonnement-ID:** *Erforderlich* Diese gewährt Zugriff auf Azure-Dienste und das Azure-Portal. Im Abonnement werden die Ressourcenverwendung protokolliert und die Dienste abgerechnet. Wenn Sie noch nicht über ein **separates** Azure-Abonnement nur für Testversionen verfügen, erstellen Sie nun eines. Sie finden die Azure-Abonnement-IDs, indem Sie sich im Azure-Portal anmelden und im Menü auf der linken Seite zu den Abonnements navigieren. (Beispiel: „a83645ac-1234-5ab6-6789-1h234g764ghty“)

![Azure-Abonnements](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD Tenant ID** (Azure AD-Mandanten-ID): *Erforderlich* Wenn Sie bereits über eine Mandanten-ID verfügen, finden Sie sie wie unten gezeigt unter „Eigenschaften“ -\> „Verzeichnis-ID“.

![Azure Active Directory-Eigenschaften](./media/azure-resource-manager-test-drive/subdetails3.png)

Erstellen Sie andernfalls einen neuen Mandanten in Azure Active Directory.

![Liste der Azure Active Directory-Mandanten](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definieren der Organisation, der Domäne und des Lands bzw. der Region für den Azure AD-Mandanten](./media/azure-resource-manager-test-drive/subdetails5.png)

![Bestätigen der Auswahl](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD-Anwendungs-ID:** *Erforderlich* Im nächsten Schritt wird eine neue Anwendung erstellt und registriert. In dieser Anwendung werden Vorgänge in Ihrer Testversioninstanz ausgeführt.

1. Navigieren Sie zu dem neu erstellten oder einem bereits vorhandenen Verzeichnis, und wählen Sie Azure Active Directory im Filterbereich aus.
2. Suchen Sie „App-Registrierungen“, und klicken Sie auf „Hinzufügen“.
3. Geben Sie einen Anwendungsnamen an.
4. Wählen Sie den Typ „Web-App/API“ aus.
5. Geben Sie in „Anmelde-URL“ einen beliebigen Wert an. Dieses Feld wird nicht verwendet.
6. Klicken Sie auf "Erstellen".
7. Navigieren Sie nach der Erstellung der Anwendung zu „Eigenschaften“ -\> „Set the application as multi-tenant“ (Anwendung als mehrinstanzenfähig festlegen), und klicken Sie auf „Speichern“.

Klicken Sie auf Speichern. Im letzten Schritt wird die Anwendungs-ID für diese registrierte Anwendung abgerufen und im Feld der Testversion eingefügt.

![ID der Azure AD-Anwendung](./media/azure-resource-manager-test-drive/subdetails7.png)

Wenn die Anwendung zum Durchführen der Bereitstellung im Abonnement verwendet wird, muss die Anwendung im Abonnement als Mitwirkender hinzugefügt werden. Dazu müssen folgende Schritte ausgeführt werden:

1. Navigieren Sie zum Blatt „Abonnements“, und wählen Sie das entsprechende Abonnement aus, das nur für die Testversion verwendet wird.
1. Klicken Sie auf **Zugriffssteuerung (IAM)** .
1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**.  ![Hinzufügen eines neuen Zugriffssteuerungsprinzipals](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klicken Sie auf **Rollenzuweisung hinzufügen**.
1. Legen Sie die Rolle als **Mitwirkender** fest.
1. Geben Sie den Namen der Azure AD-Anwendung ein, und wählen Sie die Anwendung aus, um die Rolle zuzuweisen.
    ![Hinzufügen der Berechtigungen](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klicken Sie auf **Speichern**.

**Azure AD App Key** (Azure AD-App-Schlüssel): *Erforderlich* Im letzten Feld wird ein Authentifizierungsschlüssel generiert. Geben Sie unter „Schlüssel“ eine Beschreibung des Schlüssels an, legen Sie die Dauer so fest, dass er nie abläuft, und wählen Sie dann „Speichern“ aus. Es ist **wichtig**, dass der Schlüssel nicht abläuft, da dies andernfalls zu Fehlern der Testversion in der Produktion führt. Kopieren Sie diesen Wert, und fügen Sie ihn im entsprechenden Feld für die Testversion ein.

![Schlüssel für die Azure AD-Anwendung](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie alle Felder für die Testversion ausgefüllt haben, überprüfen Sie sie, und führen Sie dann die **erneute Veröffentlichung** des Angebots durch. Nachdem Ihre Testversion die Zertifizierung bestanden hat, sollten Sie die **Vorschauversion** Ihres Angebots umfangreich testen. Starten Sie eine Testversion in der Benutzeroberfläche, öffnen Sie dann Ihr Azure-Abonnement im Azure-Portal, und vergewissern Sie sich, dass Ihre Testversionen vollständig und korrekt bereitgestellt werden.

![Azure-Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Es ist wichtig, dass Sie keine Testversioninstanzen löschen, da diese für Kunden bereitgestellt werden. Der Dienst der Testversion entfernt diese Ressourcengruppen automatisch, nachdem Kunden die Testversion nicht mehr verwenden.

Wenn Sie mit Ihrer Vorschauversion zufrieden sind, können Sie sie **veröffentlichen**. Nach der Veröffentlichung des Angebots erfolgt eine letzte Überprüfung durch Microsoft, in der das gesamte Angebot erneut gründlich überprüft wird. Wenn Ihr Angebot aus einem bestimmten Grund abgelehnt wird, wird eine Benachrichtigung an den Engineering-Kontakt für Ihr Angebot gesendet, in dem erläutert wird, was korrigiert werden muss.

Wenn Sie weitere Fragen haben, Tipps zur Problembehandlung benötigen oder Ihre Testversion optimieren möchten, lesen Sie [Häufig gestellte Fragen, Problembehandlung und bewährte Methoden](./marketing-and-best-practices.md).
