---
title: Verwenden des E-Mail-Diensts SendGrid (Node.js) | Microsoft Docs
description: Erfahren Sie, wie Sie E-Mails mit dem SendGrid-E-Mail-Dienst in Azure senden. Die Codebeispiele wurden mithilfe der Node.js-API geschrieben.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015422"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Senden von E-Mails mit SendGrid aus Node.js

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in C# geschrieben und verwenden die Node.js-API. Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen**, **Verwenden von Filtern** und **Aktualisieren von Eigenschaften**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Was ist der SendGrid-E-Mail-Dienst?

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

* Automatisches Versenden von Empfangsbestätigungen an Kunden
* Verwalten von Verteilungslisten zum Senden von monatlichen e-Flyern und speziellen Angeboten an Kunden
* Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder Kundenreaktionsfähigkeit
* Generieren von Berichten zur Identifizierung von Trends
* Weiterleiten von Kundenanfragen
* E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Erstellen eines SendGrid-Kontos

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Erstellen eines Verweises auf das SendGrid Node.js-Modul

Das SendGrid-Modul für Node.js kann über den Node Package Manager (NPM) installiert werden. Verwenden Sie dafür den folgenden Befehl:

```bash
npm install sendgrid
```

Nach der Installation können Sie das Modul mithilfe des folgenden Codes in Ihrer Anwendung anfordern:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

Das SendGrid-Modul exportiert die Funktionen **SendGrid** und **Email**.
**SendGrid** sendet E-Mails über die Web-API, **Email** dient zur Kapselung von E-Mails.

## <a name="how-to-create-an-email"></a>Gewusst wie: Erstellen einer E-Mail

Wenn Sie eine E-Mail-Nachricht mithilfe des SendGrid-Moduls senden möchten, müssen Sie sie zuerst mit der Funktion „E-Mail erstellen und dann mit der Funktion „SendGrid“ senden. Es folgt ein Beispiel für die Erstellung einer neuen Nachricht mithilfe der Funktion „E-Mail“:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Sie können auch eine HTML-Nachricht für Clients festlegen, bei denen dies durch Einstellung der HTML-Eigenschaft unterstützt wird. Beispiel:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Wenn die Text- und die HTML-Option ausgewählt sind, wird die Nachricht auf Clients, die keine HTML-Nachrichten unterstützen, im Textformat angezeigt.

Weitere Informationen zu allen von der Funktion „Email“ unterstützten Eigenschaften finden Sie unter [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Gewusst wie: Senden einer E-Mail

Nach der Erstellung einer E-Mail mithilfe der E-Mail-Funktion können Sie diese über die von SendGrid bereitgestellte Web-API senden. 

### <a name="web-api"></a>Web-API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Während die Beispiele oben die Übergabe eines E-Mail-Objekts und den Aufruf der Rückruf-Funktion zeigen, können Sie die Sende-Funktion auch direkt durch Festlegung der E-Mail-Eigenschaften aufrufen. Beispiel:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Gewusst wie: Hinzufügen einer Anlage
Anhänge können einer Nachricht hinzugefügt werden, indem die entsprechenden Dateinamen und Pfade in der Eigenschaft **files** angegeben werden. Im folgenden Beispiel wird das Senden eines Anhangs demonstriert:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> Wenn Sie die Eigenschaft **files** verwenden, muss die Datei über [fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile) verfügbar sein. Wenn die Datei, die Sie hinzufügen möchten, in Azure Storage gehostet wird, z.B. in einem Blobcontainer, müssen Sie sie zuerst an einen lokalen Speicherort oder auf ein Azure-Laufwerk kopieren, bevor sie mithilfe der Eigenschaft **files** als Anhang gesendet werden kann.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen und zur Nachverfolgung

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von Filtern. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics, Abonnement-Tracking usw. zu ermöglichen. Eine vollständige Liste der Filter finden Sie unter [Filtereinstellungen][Filter Settings].

Filter können mithilfe der Eigenschaft **filters** auf eine Nachricht angewendet werden.
Jeder Filter wird durch einen Hash mit filterspezifischen Einstellungen festgelegt.
Die folgenden Beispiele veranschaulichen die Filter für die Fußzeile und die Klickprotokollierung:

### <a name="footer"></a>Fußzeile

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Klickprotokollierung

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Gewusst wie: Aktualisieren von E-Mail-Eigenschaften

Einige E-Mail-Eigenschaften können mit **setProperty** überschrieben oder mit **addProperty** angefügt werden. Sie können z. B. zusätzliche Empfänger hinzufügen:

```javascript
email.addTo('jeff@contoso.com');
```

oder einen Filter setzen:

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Weitere Informationen finden Sie unter [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation][SendGrid API documentation].

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter diesen Links weitere Informationen.

* Repository für das SendGrid-Node.js-Modul: [sendgrid-nodejs][sendgrid-nodejs]
* SendGrid-API-Dokumentation: <https://sendgrid.com/docs>
* Spezielles SendGrid-Angebot für Azure-Kunden: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[cloudbasierter E-Mail-Dienst]: https://sendgrid.com/email-solutions
[transaktionale E-Mail-Übermittlung]: https://sendgrid.com/transactional-email
