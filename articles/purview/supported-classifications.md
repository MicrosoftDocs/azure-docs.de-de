---
title: Liste mit unterstützten Klassifizierungen
description: Auf dieser Seite sind die in Azure Purview unterstützten Systemklassifizierungen aufgelistet.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 7458b027add8be86d9491c674c2f1a0bc9fbc68c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550455"
---
# <a name="supported-classifications-in-azure-purview"></a>Unterstützte Klassifizierungen in Azure Purview

In diesem Artikel sind die Systemklassifizierungen aufgelistet, die in Azure Purview (Vorschauversion) unterstützt werden und definiert sind.

## <a name="defined-system-classifications"></a>Definierte Systemklassifizierungen

In Azure Purview werden Daten basierend auf [regulären Ausdrücken](https://wikipedia.org/wiki/Regular_expression) und einem [Bloom-Filter](https://wikipedia.org/wiki/Bloom_filter) klassifiziert. In der folgenden Liste sind das Format, das Muster und die Schlüsselwörter für die definierten Systemklassifizierungen von Azure Purview beschrieben.

Jeder Klassifizierungsname enthält das Präfix „MICROSOFT“.

## <a name="bloom-filter-classifications"></a>Klassifizierungen für Bloom-Filter

## <a name="city-country-and-place"></a>City, Country & Place (Ort, Land und Standort)

Für die Filter vom Typ „Ort, Land und Standort“ wurden die besten verfügbaren Datasets für die Aufbereitung der Daten genutzt.

## <a name="person"></a>Person

Für die Datenaufbereitung für den Bloom-Filter vom Typ „Person“ wurden die beiden folgenden Datasets verwendet:

- [US-Volkszählungsdaten aus dem Jahr 2010: Nachnamen (162.000 Einträge)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Beliebte Babynamen (Quelle: US-Sozialversicherungsnummer): Jahre 1880 bis 2019 (98.000 Einträge)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>Reguläre Ausdrücke: Klassifizierungen

## <a name="aba-routing"></a>ABA-Routing

### <a name="format"></a>Format

Neun Ziffern (formatiertes oder unformatiertes Muster)

### <a name="pattern"></a>Muster

Formatiert:

- Vier Ziffern, beginnend mit 0, 1, 2, 3, 6, 7 oder 8
- Ein Bindestrich
- Vier Ziffern
- Ein Bindestrich
- Eine Ziffer (unformatiert): neun aufeinanderfolgende Ziffern, beginnend mit 0, 1, 2, 3, 6, 7 oder 8

### <a name="keywords"></a>Keywords

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Nationale argentinische ID-Nummer (DNI)

### <a name="format"></a>Format

Acht Ziffern mit oder ohne Punkte

### <a name="pattern"></a>Muster

Acht Ziffern:

- Zwei Ziffern
- Optionaler Punkt
- Drei Ziffern
- Optionaler Punkt
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Australische Bankkontonummer

### <a name="format"></a>Format

Sechs bis zehn Ziffern mit oder ohne Bank State Branch-Nummer

### <a name="pattern"></a>Muster

Die Kontonummer ist sechs bis zehn Ziffern lang.

Bank State Branch-Nummer für Australien:

- Drei Ziffern
- Ein Bindestrich
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Australische Führerscheinnummer

### <a name="format"></a>Format
Neun Buchstaben und Ziffern

### <a name="pattern"></a>Muster
Neun Buchstaben und Ziffern:

- Zwei Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)
- Zwei Ziffern
- Fünf Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)

oder

- Ein bis zwei optionale Buchstaben (keine Beachtung der Groß-/Kleinschreibung)
- Vier bis neun Ziffern

oder

- Neun Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australia-medical-account-number"></a>Australische Krankenversicherungsnummer

### <a name="format"></a>Format

Zehn bis elf Ziffern

### <a name="pattern"></a>Muster

Zehn bis elf Ziffern:

- Erste Ziffer im Bereich 2 bis 6
- Neunte Ziffer ist eine Prüfziffer
- Zehnte Ziffer ist die Ausstellungsziffer
- Elfte Ziffer (optional) ist die individuelle Zahl

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_medical_account_number"></a>Keyword_Australia_Medical_Account_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Australische Reisepassnummer

### <a name="format"></a>Format

Ein Buchstabe, gefolgt von sieben Ziffern

### <a name="pattern"></a>Muster

Ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung), gefolgt von sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Keyword\_australia\_passport\_number

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Australische Steuernummer

### <a name="format"></a>Format

Acht bis neun Ziffern

### <a name="pattern"></a>Muster

Acht bis neun Ziffern, normalerweise wie folgt mit Leerstellen:

- Drei Ziffern
- Eine optionale Leerstelle
- Drei Ziffern
- Eine optionale Leerstelle
- Zwei bis drei Ziffern, wobei die letzte Ziffer eine Prüfziffer ist

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_tax_file_number"></a>Keyword\_australia\_tax\_file\_number

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>Belgische Nationalregisternummer

### <a name="format"></a>Format

Elf Ziffern plus optionale Trennzeichen

### <a name="pattern"></a>Muster

Elf Ziffern plus Trennzeichen:

- Sechs Ziffern und zwei optionale Punkte im Format „JJ.MM.TT“ für das Geburtsdatum
- Ein optionales Trennzeichen: Punkt, Bindestrich, Leerstelle
- Drei aufeinanderfolgende Ziffern (ungerade für männlich, gerade für weiblich)
- Ein optionales Trennzeichen: Punkt, Bindestrich, Leerstelle
- Zwei Prüfziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_belgium_national_number"></a>Keyword\_belgium\_national\_number

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Brasilianische CPF-Nummer

### <a name="format"></a>Format

Elf Ziffern, einschließlich einer Prüfziffer (formatiert oder unformatiert)

### <a name="pattern"></a>Muster

Formatiert:

- Drei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Bindestrich
- Zwei Ziffern als Prüfziffern

Unformatiert:

- Elf Ziffern, bei denen die letzten zwei Ziffern Prüfziffern sind

### <a name="keywords"></a>Keywords

#### <a name="keyword_brazil_cpf"></a>Keyword\_brazil\_cpf

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Brasilianische Nummer juristischer Personen (CNPJ)

### <a name="format"></a>Format

14 Ziffern, einschließlich Registrierungsnummer, Zweigstellennummer und Prüfziffern sowie Trennzeichen

### <a name="pattern"></a>Muster

14 Ziffern plus Trennzeichen:

- Zwei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Punkt
- Drei Ziffern (diese ersten acht Ziffern stehen für die Registrierungsnummer)
- Ein Schrägstrich
- Vierstellige Zweigstellennummer
- Ein Bindestrich
- Zwei Ziffern als Prüfziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_brazil_cnpj"></a>Keyword\_brazil\_cnpj

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>Brasilianischer Personalausweis (RG)

### <a name="format"></a>Format

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Muster

:::no-loc text="Registro Geral (old format):":::

- Zwei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Bindestrich
- Eine Ziffer als Prüfziffer

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 Stellen
- Ein Bindestrich
- Eine Ziffer als Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_brazil_rg"></a>Keyword\_brazil\_rg

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Kanadische Bankkontonummer

### <a name="format"></a>Format

Sieben oder zwölf Ziffern

### <a name="pattern"></a>Muster

Eine kanadische Bankkontonummer ist sieben oder zwölf Ziffern lang.

Kanadische Bankkonto-Überweisungsnummer:

- Fünf Ziffern
- Ein Bindestrich
- Drei Ziffern ODER
- Eine Null (&quot;0&quot;)
- Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_canada_bank_account_number"></a>Keyword\_canada\_bank\_account\_number

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Kanadische Führerscheinnummer

### <a name="format"></a>Format

Variiert je nach Provinz

### <a name="pattern"></a>Muster

Unterschiedliche Muster für Alberta, British Columbia, Manitoba, New Brunswick, Neufundland/Labrador, Nova Scotia, Ontario, Prince Edward Island, Quebec und Saskatchewan

### <a name="keywords"></a>Keywords

#### <a name="keyword_province_name_drivers_license_name"></a>Keyword\_[Name_der\_Provinz]\_drivers\_license\_name

- Abkürzung der Provinz, z. B. AB
- Name der Provinz, z. B. Alberta

#### <a name="keyword_canada_drivers_license"></a>Keyword\_canada\_drivers\_license

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Nummer des kanadischen Gesundheitsdiensts

### <a name="format"></a>Format

10 Stellen

### <a name="pattern"></a>Muster

10 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keyword_canada_health_service_number"></a>Keyword\_canada\_health\_service\_number

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Kanadische Reisepassnummer

### <a name="format"></a>Format

Zwei Großbuchstaben, gefolgt von sechs Ziffern

### <a name="pattern"></a>Muster

Zwei Großbuchstaben, gefolgt von sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_canada_passport_number"></a>Keyword\_canada\_passport\_number

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Persönliche kanadische Krankenversicherungsnummer (PHIN, Personal Health Identification Number)

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_canada_phin"></a>Keyword\_canada\_phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Keyword\_canada\_provinces

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Kanadische Sozialversicherungsnummer

### <a name="format"></a>Format

Neun Ziffern mit optionalen Bindestrichen oder Leerstellen

### <a name="pattern"></a>Muster

Formatiert:

- Drei Ziffern
- Ein Bindestrich oder eine Leerstelle
- Drei Ziffern
- Ein Bindestrich oder eine Leerstelle
- Drei Ziffern

Unformatiert: neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_sin"></a>Keyword\_sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Keyword\_sin\_collaborative

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Chilenische Personalausweisnummer

### <a name="format"></a>Format

Sieben bis acht Ziffern plus Trennzeichen, eine Prüfziffer oder ein Buchstabe

### <a name="pattern"></a>Muster

Sieben bis acht Ziffern plus Trennzeichen:

- Ein bis zwei Ziffern
- Optionaler Punkt
- Drei Ziffern
- Optionaler Punkt
- Drei Ziffern
- Ein Bindestrich
- Eine Ziffer oder ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung) als Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_chile_id_card"></a>Keyword\_chile\_id\_card

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Personalausweisnummer für Personen mit Wohnsitz in China (VRC)

### <a name="format"></a>Format

18 Ziffern

### <a name="pattern"></a>Muster

18 Ziffern:

- Sechs Ziffern als Adresscode
- Acht Ziffern im Format JJJJMMTT für das Geburtsdatum
- Drei Ziffern als Sortiercode
- Eine Ziffer als Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_china_resident_id"></a>Keyword\_china\_resident\_id

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Kreditkartennummer

### <a name="format"></a>Format

14 bis 16 Ziffern, die formatiert oder unformatiert sein können (dddddddddddddddd) und den Luhn-Test bestehen müssen.

### <a name="pattern"></a>Muster

Komplexes und zuverlässiges Muster, mit dem Karten aller Marken weltweit erkannt werden können, z. B. Visa, MasterCard, Discover Card, JCB, American Express, Geschenkkarten und Diner Card.

### <a name="keywords"></a>Keywords

#### <a name="keyword_cc_verification"></a>Keyword\_cc\_verification

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>Keyword\_cc\_name

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Kroatische Führerscheinnummer

Diese als vertraulich eingestufte Entität ist nur unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ verfügbar.

### <a name="format"></a>Format

Acht Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Keywords\_croatia\_eu\_driver's\_license\_number

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Kroatische ID-Kartennummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Personalausweisnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_croatia_id_card"></a>Keyword\_croatia\_id\_card

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>Kroatische persönliche ID-Nummer (OIB)

### <a name="format"></a>Format

Elf Ziffern

### <a name="pattern"></a>Muster

Elf Ziffern:

- 10 Stellen
- Letzte Ziffer ist eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_croatia_oib_number"></a>Keyword\_croatia\_oib\_number

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Dänische persönliche ID-Nummer

### <a name="format"></a>Format

Zehn Ziffern mit einem Bindestrich

### <a name="pattern"></a>Muster

Zehn Ziffern:

- Sechs Ziffern im Format TTMMJJ als Geburtsdatum
- Ein Bindestrich
- Vier Ziffern, wobei die letzte eine Prüfziffer ist

### <a name="keywords"></a>Keywords

#### <a name="keyword_denmark_id"></a>Keyword\_denmark\_id

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>EU-Debitkartennummer

### <a name="format"></a>Format

16 Ziffern

### <a name="pattern"></a>Muster

Komplexes und zuverlässiges Muster

### <a name="keywords"></a>Keywords

#### <a name="keyword_eu_debit_card"></a>Keyword\_eu\_debit\_card

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Keyword\_card\_terms\_dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Keyword\_card\_security\_terms\_dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Keyword\_card\_expiration\_terms\_dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>EU-Führerscheinnummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Führerscheinnummer“.

- Österreich
- Belgien
- Bulgarien
- Kroatien
- Zypern
- Tschechisch
- Dänemark
- Estland
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Niederlande
- Polen
- Portugal
- Rumänien
- Slowakei
- Slowenien
- Spanien
- Schweden
- Vereinigtes Königreich:

## <a name="eu-national-identification-number"></a>EU-Personalausweisnummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Personalausweisnummer“.

- Österreich
- Belgien
- Bulgarien
- Kroatien
- Zypern
- Tschechisch
- Dänemark
- Estland
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Niederlande
- Polen
- Portugal
- Rumänien
- Slowakei
- Slowenien
- Spanien
- Vereinigtes Königreich:

## <a name="eu-passport-number"></a>EU-Reisepassnummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Reisepassnummer“ (Entitäten im EU-Reisepassnummer-Paket).

- Österreich
- Belgien
- Bulgarien
- Kroatien
- Zypern
- Tschechisch
- Dänemark
- Estland
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Niederlande
- Polen
- Portugal
- Rumänien
- Slowakei
- Slowenien
- Spanien
- Schweden
- Vereinigtes Königreich:

## <a name="eu-social-security-number-or-equivalent-identification"></a>EU-Sozialversicherungsnummer oder vergleichbare Nummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Sozialversicherungsnummer“ oder einer vergleichbaren vertraulichen Nummer.

- Österreich
- Belgien
- Kroatien
- Tschechisch
- Dänemark
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Portugal
- Spanien
- Schweden

## <a name="eu-tax-identification-number"></a>EU-Steueridentifikationsnummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Steueridentifikationsnummer“.

- Österreich
- Belgien
- Bulgarien
- Kroatien
- Zypern
- Tschechisch
- Dänemark
- Estland
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Niederlande
- Polen
- Portugal
- Rumänien
- Slowakei
- Slowenien
- Spanien
- Schweden
- Vereinigtes Königreich:



## <a name="finland-national-id"></a>Nationale finnische ID-Nummer

### <a name="format"></a>Format

Sechs Ziffern plus ein Zeichen für das Jahrhundert plus drei Ziffern und eine Prüfziffer

### <a name="pattern"></a>Muster

Das Muster muss alle folgenden Elemente enthalten:

- Sechs Ziffern im Format TTMMJJ als Geburtsdatum
- Jahrhundertkennzeichnung („-“, „+“ oder „a“)
- Drei Ziffern als persönliche ID-Nummer
- Eine Ziffer oder ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung) als Prüfziffer

### <a name="keywords"></a>Keywords

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Finnische Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ verfügbar und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Kombination aus neun Buchstaben und Ziffern

### <a name="pattern"></a>Muster

Kombination aus neun Buchstaben und Ziffern:

- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Keyword\_finland\_passport\_number

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Französische Führerscheinnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

12 Stellen

### <a name="pattern"></a>Muster

Zwölf Ziffern mit Überprüfung zur Unterscheidung von ähnlichen Mustern, z. B. französischen Telefonnummern

### <a name="keywords"></a>Keywords

#### <a name="keyword_french_drivers_license"></a>Keyword\_french\_drivers\_license

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Französischer Personalausweis (Carte Nationale d'Identité, CNI)

### <a name="format"></a>Format

12 Stellen

### <a name="pattern"></a>Muster

12 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keywords_france_eu_national_id_card"></a>Keywords\_france\_eu\_national\_id\_card

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Französische Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ verfügbar und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Neun Ziffern und Buchstaben

### <a name="pattern"></a>Muster

Neun Ziffern und Buchstaben:

- Zwei Ziffern
- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Fünf Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Französische Sozialversicherungsnummer (INSEE) oder vergleichbare Nummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Sozialversicherungsnummer“ und vergleichbaren vertraulichen Nummern enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

15 Stellen

### <a name="pattern"></a>Muster

Muss eines der beiden folgenden Muster aufweisen:

- 13 Ziffern, gefolgt von einer Leerstelle, gefolgt von zwei Ziffern ODER
- 15 aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_fr_insee"></a>Keyword\_fr\_insee

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Deutsche Führerscheinnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Kombination aus elf Ziffern und Buchstaben

### <a name="pattern"></a>Muster

Elf Ziffern und Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)

- Eine Ziffer oder ein Buchstabe
- Zwei Ziffern
- Sechs Ziffern oder Buchstaben
- Eine Ziffer
- Eine Ziffer oder ein Buchstabe

### <a name="keywords"></a>Keywords

#### <a name="keyword_german_drivers_license_number"></a>Keyword\_german\_drivers\_license\_number

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Deutsche Personalausweisnummer

### <a name="format"></a>Format

Seit dem 1. November 2010: Neun Buchstaben und Ziffern

Zwischen 1. April 1987 und 31. Oktober 2010: Zehn Ziffern

### <a name="pattern"></a>Muster

Seit dem 1. November 2010:

- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)
- Acht Ziffern

Zwischen 1. April 1987 und 31. Oktober 2010:

- 10 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keyword_germany_id_card"></a>Keyword\_germany\_id\_card

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Deutsche Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Zehn Ziffern oder Buchstaben

### <a name="pattern"></a>Muster

Das Muster muss alle folgenden Elemente enthalten:

- Das erste Zeichen ist eine Ziffer oder einer der folgenden Buchstaben: C, F, G, H, J, K
- Drei Ziffern
- Fünf Ziffern oder Buchstaben: C bis H, J bis N, P, R, T, V bis Z
- Eine Ziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_german_passport"></a>Keyword\_german\_passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>Griechische Personalausweisnummer

### <a name="format"></a>Format

Kombination aus sieben bis acht Buchstaben und Ziffern plus ein Bindestrich

### <a name="pattern"></a>Muster

Sieben Buchstaben und Ziffern (altes Format):

- Ein Buchstabe (beliebiger Buchstabe des griechischen Alphabets)
- Ein Bindestrich
- Sechs Ziffern

Acht Buchstaben und Ziffern (neues Format):

- Zwei Buchstaben, wobei die Großbuchstaben sowohl im griechischen als auch im lateinischen Alphabet vorkommen müssen (ABEZHIKMNOPTYX)
- Ein Bindestrich
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_greece_id_card"></a>Keyword\_greece\_id\_card

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>ID-Kartennummer aus Hongkong (HKID)

### <a name="format"></a>Format

Kombination aus acht bis neun Buchstaben und Ziffern plus optionaler Klammern um das letzte Zeichen

### <a name="pattern"></a>Muster

Kombination aus acht bis neun Buchstaben:

- Ein oder zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Sechs Ziffern
- Das letzte Zeichen (beliebige Ziffer oder der Buchstabe A) ist das Prüfzeichen und kann in Klammern stehen.

### <a name="keywords"></a>Keywords

#### <a name="keyword_hong_kong_id_card"></a>Keyword\_hong\_kong\_id\_card

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>IP-Adresse

### <a name="format"></a>Format

#### <a name="ipv4"></a>IPv4:

Komplexes Muster als formatierte (Punkte) oder unformatierte (keine Punkte) Version der IPv4-Adressen

#### <a name="ipv6"></a>IPv6:

Komplexes Muster für formatierte IPv6-Nummern (mit Doppelpunkten)

### <a name="pattern"></a>Muster

### <a name="keywords"></a>Keywords

#### <a name="keyword_ipaddress"></a>Keyword\_ipaddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Indische PAN (Permanent Account Number)

### <a name="format"></a>Format

Zehn Buchstaben oder Ziffern

### <a name="pattern"></a>Muster

Zehn Buchstaben oder Ziffern:

- Drei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Ein Buchstabe: C, P, H, F, A, T, B, L, J, G (ohne Beachtung der Groß-/Kleinschreibung)
- Ein Buchstabe
- Vier Ziffern
- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keyword_india_permanent_account_number"></a>Keyword\_india\_permanent\_account\_number

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Indische eindeutige ID-Nummer (Aadhaar)

### <a name="format"></a>Format

Zwölf Ziffern mit optionalen Leerstellen oder Bindestrichen

### <a name="pattern"></a>Muster

Zwölf Ziffern:

- Eine Ziffer (nicht 0 oder 1)
- Drei Ziffern
- Eine optionale Leerstelle oder ein optionaler Bindestrich
- Vier Ziffern
- Eine optionale Leerstelle oder ein optionaler Bindestrich
- Letzte Ziffer (Prüfziffer)

### <a name="keywords"></a>Keywords

#### <a name="keyword_india_aadhar"></a>Keyword\_india\_aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>Indonesische ID-Kartennummer (KTP)

### <a name="format"></a>Format

16 Ziffern mit optionalen Punkten

### <a name="pattern"></a>Muster

16 Ziffern:

- Zwei Ziffern als Provinzcode
- Ein Punkt (optional)
- Zwei Ziffern als Code für Regierungsbezirk oder Ort
- Zwei Ziffern als Code für den Unterbezirk
- Ein Punkt (optional)
- Sechs Ziffern im Format TTMMJJ als Geburtsdatum
- Ein Punkt (optional)
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_indonesia_id_card"></a>Keyword\_indonesia\_id\_card

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>IBAN (International Banking Account Number)

### <a name="format"></a>Format

Ländercode (zwei Buchstaben) plus Prüfziffern (zwei Ziffern) plus :::no-loc text="bban":::-Zahlen (bis zu 30 Zeichen)

### <a name="pattern"></a>Muster

Das Muster muss alle folgenden Elemente enthalten:

- Zwei Buchstaben als Ländercode
- Zwei Prüfziffern (gefolgt von einer optionalen Leerstelle)
- Ein bis sieben Gruppen mit vier Buchstaben oder Ziffern (kann durch Leerstellen getrennt werden)
- Ein bis drei Buchstaben oder Ziffern

Die Formate der einzelnen Länder unterscheiden sich geringfügig. Der Vertraulichkeitsinformationstyp „IBAN“ gilt für diese 60 Länder:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Keywords

Keine

## <a name="ireland-personal-public-service-pps-number"></a>Irische Sozialversicherungsnummer (Personal Public Service, PPS)

### <a name="format"></a>Format

Altes Format (bis 31. Dezember 2012):

- Sieben Ziffern, gefolgt von ein bis zwei Buchstaben

Neues Format (ab 1. Jan 2013):

- Sieben Ziffern, gefolgt von zwei Buchstaben

### <a name="pattern"></a>Muster

Altes Format (bis 31. Dezember 2012):

- Sieben Ziffern
- Ein oder zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)

Neues Format (ab 1. Jan 2013):

- Sieben Ziffern
- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung) als alphabetisches Prüfzeichen
- Ein optionaler Buchstabe aus dem Bereich A bis I oder &quot;W&quot;

### <a name="keywords"></a>Keywords

#### <a name="keywords_ireland_eu_national_id_card"></a>Keywords\_ireland\_eu\_national\_id\_card

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Israelische Bankkontonummer

### <a name="format"></a>Format

13 Ziffern

### <a name="pattern"></a>Muster

Formatiert:

- Zwei Ziffern
- Ein Bindestrich
- Drei Ziffern
- Ein Bindestrich
- Acht Ziffern

Unformatiert:

- 13 aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_israel_bank_account_number"></a>Keyword\_israel\_bank\_account\_number

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Israelische Personalausweisnummer

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_israel_national_id"></a>Keyword\_Israel\_National\_ID

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Italienische Führerscheinnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Kombination aus zehn Buchstaben und Ziffern

### <a name="pattern"></a>Muster

Kombination aus zehn Buchstaben und Ziffern:

- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)
- Buchstabe &quot;A&quot; oder &quot;V&quot; (ohne Beachtung der Groß-/Kleinschreibung)
- Sieben Ziffern
- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keyword_italy_drivers_license_number"></a>Keyword\_italy\_drivers\_license\_number

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Japanische Bankkontonummer

### <a name="format"></a>Format

Sieben oder acht Ziffern

### <a name="pattern"></a>Muster

Bankkontonummer:

- Sieben oder acht Ziffern
- Code für Bankkonto-Zweigstelle:
- Vier Ziffern
- Eine Leerstelle oder ein Bindestrich (optional)
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_bank_account"></a>Keyword\_jp\_bank\_account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Keyword\_jp\_bank\_branch\_code

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Japanische Führerscheinnummer

### <a name="format"></a>Format

12 Stellen

### <a name="pattern"></a>Muster

Zwölf aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_drivers_license_number"></a>Keyword\_jp\_drivers\_license\_number

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Japanische Reisepassnummer

### <a name="format"></a>Format

Zwei Buchstaben, gefolgt von sieben Ziffern

### <a name="pattern"></a>Muster

Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung), gefolgt von sieben Ziffern

#### <a name="keyword_jp_passport"></a>Keyword\_jp\_passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Japanische Personalausweisnummer

### <a name="format"></a>Format

Zwölf Buchstaben und Ziffern

### <a name="pattern"></a>Muster

Zwölf Buchstaben und Ziffern:

- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Acht Ziffern
- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_residence_card_number"></a>Keyword\_jp\_residence\_card\_number

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Japanische Melderegisternummer

### <a name="format"></a>Format

Elf Ziffern

### <a name="pattern"></a>Muster

Elf aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_resident_registration_number"></a>Keyword\_jp\_resident\_registration\_number

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Japanische Sozialversicherungsnummer (SIN)

### <a name="format"></a>Format

Sieben bis zwölf Ziffern

### <a name="pattern"></a>Muster

Sieben bis zwölf Ziffern:

- Vier Ziffern
- Ein Bindestrich (optional)
- Sechs Ziffern ODER
- Sieben bis zwölf aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_sin"></a>Keyword\_jp\_sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>Malaiische Personalausweisnummer

### <a name="format"></a>Format

Zwölf Ziffern mit optionalen Bindestrichen

### <a name="pattern"></a>Muster

Zwölf Ziffern:

- Sechs Ziffern im Format JJMMTT als Geburtsdatum
- Ein Bindestrich (optional)
- Zwei Buchstaben als Code für den Geburtsort
- Ein Bindestrich (optional)
- Drei zufällige Ziffern
- Einstelliger Code für das Geschlecht

### <a name="keywords"></a>Keywords

#### <a name="keyword_malaysia_id_card_number"></a>Keyword\_malaysia\_id\_card\_number

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>Niederländische persönliche Identifikationsnummer (BSN)

### <a name="format"></a>Format

Acht bis neun Ziffern mit optionalen Leerstellen

### <a name="pattern"></a>Muster

Acht bis neun Ziffern:

- Drei Ziffern
- Eine Leerstelle (optional)
- Drei Ziffern
- Eine Leerstelle (optional)
- Zwei bis drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_netherlands_eu_national_id_card"></a>Keywords\_netherlands\_eu\_national\_id\_card

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>Neuseeländische Nummer des Gesundheitsministeriums

### <a name="format"></a>Format

Drei Buchstaben, eine Leerstelle (optional) und vier Ziffern

### <a name="pattern"></a>Muster

- Drei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung) außer „I“ und „O“
- Eine Leerstelle (optional)
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_nz_terms"></a>Keyword\_nz\_terms

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Norwegische Identifikationsnummer

### <a name="format"></a>Format

Elf Ziffern

### <a name="pattern"></a>Muster

Elf Ziffern:

- Sechs Ziffern im Format TTMMJJ als Geburtsdatum
- Dreistellige individuelle Nummer
- Zwei Prüfziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_norway_id_number"></a>Keyword\_norway\_id\_number

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Philippinische einheitliche Mehrzweck-Identifikationsnummer

### <a name="format"></a>Format

Zwölf Ziffern, getrennt durch Bindestriche

### <a name="pattern"></a>Muster

Zwölf Ziffern:

- Vier Ziffern
- Ein Bindestrich
- Sieben Ziffern
- Ein Bindestrich
- Eine Ziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_philippines_id"></a>Keyword\_philippines\_id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Polnischer Personalausweis

### <a name="format"></a>Format

Drei Buchstaben und sechs Ziffern

### <a name="pattern"></a>Muster

Drei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung), gefolgt von sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>Nationale polnische ID-Nummer (PESEL)

### <a name="format"></a>Format

Elf Ziffern

### <a name="pattern"></a>Muster

- Sechs Ziffern als Geburtsdatum im Format JJMMTT
- Vier Ziffern
- Eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_pesel_identification_number"></a>Keyword\_pesel\_identification\_number

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Polnische Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Zwei Buchstaben und sieben Ziffern

### <a name="pattern"></a>Muster

Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung), gefolgt von sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Portugiesische ID-Kartennummer (Citizen Card)

### <a name="format"></a>Format

Acht Ziffern

### <a name="pattern"></a>Muster

Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_portugal_citizen_card"></a>Keyword\_portugal\_citizen\_card

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Portugiesische Führerscheinnummer

Diese als vertraulich eingestufte Entität ist nur unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ verfügbar.

### <a name="format"></a>Format

Zwei Muster: zwei Buchstaben, gefolgt von fünf bis acht Ziffern mit Sonderzeichen

### <a name="pattern"></a>Muster

Muster 1: Zwei Buchstaben, gefolgt von fünf bis sechs Ziffern mit Sonderzeichen:

- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Ein Bindestrich
- Fünf oder sechs Ziffern
- Eine Leerstelle
- Eine Ziffer

Muster 2: Ein Buchstabe, gefolgt von sechs/acht Ziffern mit Sonderzeichen:

- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)
- Ein Bindestrich
- Sechs oder acht Ziffern
- Eine Leerstelle
- Eine Ziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Keywords\_portugal\_eu\_driver's\_license\_number

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>Nationale saudi-arabische ID-Nummer

### <a name="format"></a>Format

10 Stellen

### <a name="pattern"></a>Muster

Zehn aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_saudi_arabia_national_id"></a>Keyword\_saudi\_arabia\_national\_id

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Singapur: Nationale Registrierungs-ID-Kartennummer (NRIC)

### <a name="format"></a>Format

Neun Buchstaben und Ziffern

### <a name="pattern"></a>Muster

- Neun Buchstaben und Ziffern:
- Buchstabe &quot;F&quot;, &quot;G&quot;, &quot;S&quot; oder &quot;T&quot; (ohne Beachtung der Groß-/Kleinschreibung)
- Sieben Ziffern
- Ein alphabetisches Prüfzeichen

### <a name="keywords"></a>Keywords

#### <a name="keyword_singapore_nric"></a>Keyword\_singapore\_nric

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>Südafrikanische ID-Nummer

### <a name="format"></a>Format

13 Ziffern, ggf. mit Leerstellen

### <a name="pattern"></a>Muster

13 Ziffern:

- Sechs Ziffern im Format JJMMTT als Geburtsdatum
- Vier Ziffern
- Eine Ziffer als Indikator für Staatsbürgerschaft
- Ziffer &quot;8&quot; oder &quot;9&quot;
- Eine Ziffer (Prüfsummenziffer)

### <a name="keywords"></a>Keywords

#### <a name="keyword_south_africa_identification_number"></a>Keyword\_south\_africa\_identification\_number

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Südkoreanische Einwohnerregistrierungsnummer (Resident Registration Number)

### <a name="format"></a>Format

13 Ziffern mit einem Bindestrich

### <a name="pattern"></a>Muster

13 Ziffern:

- Sechs Ziffern im Format JJMMTT als Geburtsdatum
- Ein Bindestrich
- Eine Ziffer basierend auf Jahrhundert und Geschlecht
- Vierstelliger Code für Geburtsregion
- Eine Ziffer zur Unterscheidung für Personen, bei denen die vorherigen Nummern identisch sind
- Eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_south_korea_resident_number"></a>Keyword\_south\_korea\_resident\_number

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>Spanische Sozialversicherungsnummer (SSN)

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Sozialversicherungsnummer“ oder vergleichbaren vertraulichen Nummern enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Elf bis zwölf Ziffern

### <a name="pattern"></a>Muster

Elf bis zwölf Ziffern:

- Zwei Ziffern
- Ein Schrägstrich (optional)
- Sieben bis acht Ziffern
- Ein Schrägstrich (optional)
- Zwei Ziffern

### <a name="keywords"></a>Keywords

Keine

## <a name="sweden-national-id"></a>Nationale schwedische ID-Nummer

### <a name="format"></a>Format

Zehn oder zwölf Ziffern und ein optionales Trennzeichen

### <a name="pattern"></a>Muster

Zehn oder zwölf Ziffern und ein optionales Trennzeichen:

- Zwei Ziffern (optional)
- Sechs Ziffern als Datumsformat JJMMTT
- Trennzeichen &quot;-&quot; oder &quot;+&quot; (optional)
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_swedish_national_identifier"></a>Keywords\_swedish\_national\_identifier

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Schwedische Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Acht Ziffern

### <a name="pattern"></a>Muster

Acht aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_sweden_passport"></a>Keyword\_sweden\_passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>SWIFT-Code

### <a name="format"></a>Format

Vier Buchstaben, gefolgt von 5 bis 31 Buchstaben oder Ziffern

### <a name="pattern"></a>Muster

Vier Buchstaben, gefolgt von 5 bis 31 Buchstaben oder Ziffern:

- Vier Buchstaben als Bankcode (ohne Beachtung der Groß-/Kleinschreibung)
- Eine optionale Leerstelle
- 4 bis 28 Buchstaben oder Ziffern (einfache Bankkontonummer, BBAN)
- Eine optionale Leerstelle
- Ein bis drei Buchstaben oder Ziffern (Rest der BBAN)

### <a name="keywords"></a>Keywords

#### <a name="keyword_swift"></a>Keyword\_swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Taiwanesische Personalausweisnummer

### <a name="format"></a>Format

Ein Buchstabe (Englisch), gefolgt von neun Ziffern

### <a name="pattern"></a>Muster

Ein Buchstabe (Englisch), gefolgt von neun Ziffern:

- Ein Buchstabe (Englisch, ohne Beachtung der Groß-/Kleinschreibung)
- Ziffer &quot;1&quot; oder &quot;2&quot;
- Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_taiwan_national_id"></a>Keyword\_taiwan\_national\_id

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Taiwanesische Reisepassnummer

### <a name="format"></a>Format

- Nummer des biometrischen Reisepasses: Neun Ziffern
- Nummer des nicht biometrischen Reisepasses: Neun Ziffern

### <a name="pattern"></a>Muster

Nummer des biometrischen Reisepasses:

- Zeichen &quot;3&quot;
- Acht Ziffern

Nummer des nicht biometrischen Reisepasses:

- Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_taiwan_passport"></a>Keyword\_taiwan\_passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Nummer des taiwanesischen Einwohnerzertifikats (ARC/TARC)

### <a name="format"></a>Format

Zehn Buchstaben und Ziffern

### <a name="pattern"></a>Muster

Zehn Buchstaben und Ziffern:

- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_taiwan_resident_certificate"></a>Keyword\_taiwan\_resident\_certificate

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>Vereinigtes Königreich: Führerscheinnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Kombination aus 18 Buchstaben und Ziffern im angegebenen Format

### <a name="pattern"></a>Muster

18 Buchstaben und Ziffern:

- Fünf Buchstaben (ohne Beachtung der Groß-/Kleinschreibung) oder die Ziffer &quot;9&quot; anstelle eines Buchstabens
- Eine Ziffer
- Fünf Ziffern als Datumsformat MMTTJ für das Geburtsdatum (Inkrementierung des siebten Zeichens mit 50 für weibliche Fahrer, also 51 bis 62 anstelle von 01 bis 12)
- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung) oder die Ziffer &quot;9&quot; anstelle eines Buchstabens
- Fünf Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_uk_drivers_license"></a>Keyword\_uk\_drivers\_license

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>Vereinigtes Königreich: Wählerverzeichnisnummer

### <a name="format"></a>Format

Zwei Buchstaben, gefolgt von ein bis vier Ziffern

### <a name="pattern"></a>Muster

Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung), gefolgt von ein bis vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_uk_electoral"></a>Keyword\_uk\_electoral

- council nomination
- nomination form
- electoral register
- electoral roll

## <a name="uk-national-health-service-number"></a>Vereinigtes Königreich: National Health Service-Nummer (NHS)

### <a name="format"></a>Format

10 bis 17 Ziffern, getrennt durch Leerstellen

### <a name="pattern"></a>Muster

10 bis 17 Ziffern:

- Entweder drei oder zehn Ziffern
- Eine Leerstelle
- Drei Ziffern
- Eine Leerstelle
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_uk_nhs_number"></a>Keyword\_uk\_nhs\_number

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Keyword\_uk\_nhs\_number1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Keyword\_uk\_nhs\_number\_dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>Vereinigtes Königreich: Sozialversicherungsnummer (NINO)

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Personalausweisnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Sieben Zeichen oder neun Zeichen, getrennt durch Leerstellen oder Bindestriche

### <a name="pattern"></a>Muster

Zwei mögliche Muster:

- Zwei Buchstaben (gültige Nummern enthalten in diesem Präfix nur bestimmte Zeichen, die bei diesem Muster überprüft werden; ohne Beachtung der Groß-/Kleinschreibung)
- Sechs Ziffern
- Entweder „A“, „B“, „C“ oder „D“ (wie beim Präfix sind auch im Suffix nur bestimmte Zeichen zulässig; ohne Beachtung der Groß-/Kleinschreibung).

oder

- Zwei Buchstaben
- Eine Leerstelle oder ein Bindestrich
- Zwei Ziffern
- Eine Leerstelle oder ein Bindestrich
- Zwei Ziffern
- Eine Leerstelle oder ein Bindestrich
- Zwei Ziffern
- Eine Leerstelle oder ein Bindestrich
- Entweder „A“, „B“, „C“ oder „D“


### <a name="keywords"></a>Keywords

#### <a name="keyword_uk_nino"></a>Keyword\_uk\_nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>Vereinigtes Königreich: Eindeutige Steuerzahlerreferenznummer

Diese als vertraulich eingestufte Nummer ist nur für folgende Zwecke verfügbar:

- Richtlinien zur Verhinderung von Datenverlust
- Richtlinien zur Konformität der Kommunikation
- Information Governance
- Datensatzverwaltung
- Microsoft Cloud App Security

### <a name="format"></a>Format

Zehn Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

10 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keywords_uk_eu_tax_file_number"></a>Keywords\_uk\_eu\_tax\_file\_number

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>US-Bankkontonummer

### <a name="format"></a>Format

6 bis 17 Ziffern

### <a name="pattern"></a>Muster

6 bis 17 aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_usa_bank_account"></a>Keyword\_usa\_Bank\_Account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>US-Führerscheinnummer

### <a name="format"></a>Format

Je nach Bundesstaat

### <a name="pattern"></a>Muster

Je nach Bundesstaat. Beispiel für New York:

- Neun Ziffern im Format ddd sind gültig.
- Neun Ziffern im Format „ddddddddd“ sind ungültig.

### <a name="keywords"></a>Keywords

#### <a name="keyword_us_drivers_license_abbreviations"></a>Keyword\_us\_drivers\_license\_abbreviations

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Keyword\_us\_drivers\_license

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Keyword\_[state\_name]\_drivers\_license\_name

- Abkürzung für Bundesstaat (z. B. &quot;NY&quot;)
- Name des Bundesstaats (z. B. &quot;New York&quot;)

## <a name="us-individual-taxpayer-identification-number-itin"></a>Persönliche Steuernummer in den USA (ITIN)

### <a name="format"></a>Format

Neun Ziffern, die mit einer &quot;9&quot; beginnen und eine &quot;7&quot; oder &quot;8&quot; als vierte Ziffer enthalten, optional mit Leerstellen oder Bindestrichen

### <a name="pattern"></a>Muster

Formatiert:

- Ziffer &quot;9&quot;
- Zwei Ziffern
- Eine Leerstelle oder ein Bindestrich
- Eine &quot;7&quot; oder &quot;8&quot;
- Eine Ziffer
- Eine Leerstelle oder ein Bindestrich
- Vier Ziffern

Unformatiert:

- Ziffer &quot;9&quot;
- Zwei Ziffern
- Eine &quot;7&quot; oder &quot;8&quot;
- Fünf Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_itin"></a>Keyword\_itin

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>US-Sozialversicherungsnummer (Social Security Number, SSN)

### <a name="format"></a>Format

Neun Ziffern (formatiertes oder unformatiertes Muster)

>[!Note]
> Bei Ausstellung vor Mitte 2011 verfügt eine SSN über eine feste Formatierung, bei der bestimmte Teile der Nummer in bestimmte Bereiche fallen müssen, damit die Nummer gültig ist (aber es ist keine Prüfsumme vorhanden).
>

### <a name="pattern"></a>Muster

Vier Funktionen suchen in vier unterschiedlichen Mustern nach SSNs:

- Mit „Func\_ssn“ werden SSNs mit fester Formatierung von vor 2011 ermittelt, die Bindestriche oder Leerstellen (ddd-dd-dddd ODER ddd dd dddd) enthalten.
- Mit „Func\_unformatted\_ssn“ werden SSNs mit fester Formatierung von vor 2011 ermittelt, die unformatiert sind und neun aufeinanderfolgende Ziffern (ddddddddd) enthalten.
- Mit „Func\_randomized\_formatted\_ssn“ werden nach 2011 ausgestellte SSNs ermittelt, die mit Bindestrichen oder Leerstellen (ddd-dd-dddd ODER ddd dd dddd) formatiert sind.
- Mit „Func\_randomized\_unformatted\_ssn“ werden nach 2011 ausgestellte SSNs ermittelt, die unformatiert sind und neun aufeinanderfolgende Ziffern (ddddddddd) enthalten.

### <a name="keywords"></a>Keywords

#### <a name="keyword_ssn"></a>Keyword\_ssn

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>USA/Vereinigtes Königreich: Reisepassnummer

Die als vertraulich eingestufte Reisepassnummer für das Vereinigte Königreich ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
