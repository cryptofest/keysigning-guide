# PGP Key Signing Party Návod

Tento návod popisuje práci s PGP - sdílení, podepisování a distribuci klíčů.

---

## TL;DR
* Osobně nasdílejte lístky s identitami a jejich otisky
* Podepiště identity, které souhlasí s daným lístkem
* Pošlete podepsané klíče majiteli

Když se něco nepovede, lze jednoduše daný klíč smazat, importovat jej znovu a začít od začátku.

`gpg --delete-key <keyid> & gpg --recv-keys <keyid>`

---

## 1. Sdílení klíčů

Nejdůležitější proces na každé key signing party je sdílení klíčů s ostatními a ověřování jejich identit.

### Tisk papírových lístků

Distribuce klíčů je zajištěna sdílením papírových lístků, na kterém jsou vytištěné identity (jméno a email) a otisky klíče. Ty můžou být vygenerovány pomocí [paper slip generátoru](http://openpgp.quelltextlich.at/slip.html). Každý bi si měl lístků přinést dostatek, protože je vždy lepší mít více než méně.

### Sdílení klíčů

Lidé by měli vytvořit dvě stejně dlouhé řady tak, že každý má naproti sobě partnera. Jakmile jsou všechny páry hotovy s kontrolou a výměnou papírových lístků, tato formace začne rotovat formou tankového pásu.

![line-formation](line-formation.png)

Před přijmutím papírového lístku je nutno zkontrolovat jméno a obličej majitele v porovnání s jeho občanským průkazem, či jinou oficiální identifikací. Pokud je vše v pořádku, papírový lísteček může být převzat.

---

## 2. Podepisování

Podepisování může nějakou dobu trvat, pokud je vše porváděno poctivě.

### Import klíčů z keyserveru

Obdržené papírové lístečky by měly obsahovat fingerprint (otisk), který se použije pro získání klíče z keyserveru.

`gpg --keyserver pool.sks-keyservers.net --recv-keys <keyid>`

### Kontrola fingerprintů a identit

Následuje vypsání importovaného klíče pomocí `gpg --fingerprint <keyid>` a zkontrolování toho, že odpovídá fingerprintu na papírovém lístečku.

### Podepisování validních identit

Pokud je vše v pořádku, může být importovaný klíč podepsán. Míru důvěry lze zvolit pomocí `--ask-cert-level`.

`gpg --sign-key --ask-cert-level <keyid>`

Po projítí instrukcí se v terminálu objeví toto.

	How carefully have you verified the key you are about to sign actually belongs
	to the person named above?  If you don't know what to answer, enter "0".

  	(0) I will not answer. (default)
  	(1) I have not checked at all.
  	(2) I have done casual checking.
  	(3) I have done very careful checking.

	Your selection? (enter '?' for more information):

Protože byla provedená důkladná kontrola, lze zvolit podpis úrovně 3.

Pokud má klíč více identit, měla by být každá podepsána zvlášť. To je většinou dost zdlouhavý proces, který lze automatizovat scriptem.

---

## 3. Redistribuce podepsaných klíčů

Nyní je třeba podepsaný klíč exportovat, zašifrovat a poslat jej zpět majiteli.

Klíč by měl být poslán emailem, nikoliv nahrán přímo za keyserver. Posílání emailem je zdlouhavější ale ověří se tak, že majitel klíče opravdu vlastní adresy, o kterých to tvrdí.

### Export a šofrování

Export i zašifrování klíče může být provedeno najednou.

`gpg -a --export <keyid> | gpg -ear <keyid> > <keyfile>.asc`

Kdybych tedy chtěl exportovat a zašifrovat můj vlastní klíč, vypadalo by to takhle.

`gpg -a --export ab68b241 | gpg -ear ab68b241 > ab68b241.asc`

Tímto se vytvoří soubor v aktivním adresáři, který se odešle zpět majiteli.

#### Odeslání emailem

Zdlouhavějí, ale bezpečnější cesta je poslání klíče přílohou v emailu.

Předmět emailu by měl značit, že zpráva obsahuje podepsaný klíč. Je třeba se ujistit, že k emailu byl přidán soubor s podepsaných klíčem a že samotná zpráva je také zašifrována. Tento proces lze automatizovat u běžných emailových klientů.

* Roundcube - [Enigma](https://github.com/roundcube/roundcubemail/tree/master/plugins/enigma)
* Thunderbird - [Enigmail](https://www.enigmail.net/index.php/en/)
* Claws Mail - [GPG Plugin](http://www.claws-mail.org/plugin.php?plugin=gpg)

Po odeslání lze klíč smazat z klokální klíčenky.

`gpg --delete-key <keyid>`

#### Nahrání na keyserver

Pokud se chcete vydat rychlejší cestou, je možné klíč nahrát přímo na keyserver.

`gpg --keyserver pool.sks-keyservers.net --send-keys <keyid>`

### Příjem klíčů

Po obdržení emailu s nově podepsaným klíčem, je nutné klíč dešifrovat a naimportovat do lokální klíčenky.

`gpg -d <attachment> > <keyfile> & gpg --import <keyfile>`

GnuPG output by měl ohlásit, že podpis byl úspěšně naimportován. Podpis je třeba zkontrolovat `gpg --list-sig <keyid>` a pokud je vše v pořádku, stačí ho nahrát na keyserver.

`gpg --keyserver pool.sks-keyservers.net --send-keys <keyid>`

Tímto je práce u konce.

