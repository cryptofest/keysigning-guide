# PGP Key Signing Party Návod

This guide should describe our way of doing things - sharing, signing and distributing keys. 
Tento návod popisuje práci s PGP - sdílení, podepisování a distribuci klíčů.

---

## TL;DR
* Share paper slips, employing careful checking
* Sign keys and identities which match the paper slips
* Pošli podepsané klíče emailem

Neuploaduj podepsané klíče rovnou na keyserver! Odesláním podepsaného klíče emailem zajistíš že daná adresa opravdu patří tomu, kdo prohlašuje že je její majitel.

Když se něco nepovede, můžeš jednoduše smazat daný klíč, importovat jej znovu a začít od začátku.

	`gpg --delete-key <keyid>`
	`gpg --recv-keys <keyid>`

---

## 1. Sdílení klíče

Nejdůležitější proces na každé key signing party je sdílení tvých klíčů s ostatními a ověřování jejich identit.

### Tisk papírových lístků

Distribuce tvých klíčů je zajištěna sdílením papírových lístků s tvými identitami (jméno a email) a otiskem klíče. Tyto můžou být vygenerovány pomocí [paper slip generátoru](http://openpgp.quelltextlich.at/slip.html). Každý bi si jich měl přinést dostatek, protože je vždy lepší mít více než méně.

### Sdílení klíčů

Lidé by měli vytvořit dvě řady stejné délké tak, že vzniknou proti sobě páry. Jakmile jsou všechny páry hotovy s kontrolou a výměnou papírových lístků tato formace začne rotovat (jako pásy u tanku).

![line-formation](line-formation.png)

Předtím, než od kohokoliv přijmeš papírový lístek tak **MUSÍŠ** důkladně skontrolovat jeho jméno a obličej v porovnání s jeho občanským průkazem. Pokud je vše v pořádku, můžeš si vzít papírový lísteček.

---

## 2. Podepisování

Podepisování může nějakou dobu trvat pokud vše provádíš poctivě.

### Import klíčů z keyserveru

Papírové lístečky které jsi dostal by měli obsahovat fingerprint, použij ho pro získání klíče z keyserveru

`gpg --recv-keys <keyid>`

### Kontrola fingerprintů a identit

Nyní si vypiš fingerprint právě importovaného klíče pomocí `gpg --fingerprint <keyid>` a skontroluj že tento odpovídá fingerprintu na papírovém lístečku.

### Podepisování validních identit

Pokud je vše v pořádku, můžeš importovaný klíč podepsat vlastním klíčem. Můžeš zvolit míru důvěry pomocí `--ask-cert-level`.

`gpg --sign-key --ask-cert-level <keyid>`

Po projítí instrukcí na obrazovce uvidíš toto.

	How carefully have you verified the key you are about to sign actually belongs
	to the person named above?  If you don't know what to answer, enter "0".

  	(0) I will not answer. (default)
  	(1) I have not checked at all.
  	(2) I have done casual checking.
  	(3) I have done very careful checking.

	Your selection? (enter '?' for more information):

Protože jsi vše důkladně zkontrolovat tak můžeš zvolit podpis stupně 3.

Pokud má klíč více identit, měl by si podepsat každou identitu zvlášť. Tohle je většinou dost zdlouhavý proces, který lze automatizovat scriptem.

---

## 3. Redistribuce podepsaných klíčů

Nyní musíš klíč spolu s tvým novým podpisem exportovat, zašifrovat jej a poslat jej zpět majiteli.

Nenahrávej tento klíč na keyserver přímo, posílání emailem je zdlouhavější ale zajistíme že majitel klíče opravdu vlastní adresy, o kterých to tvrdí.

### Export a šofrování

Můžeš exportovat klíč a potom jej podepsat, nebo to také můžeš provést najednou.

`gpg -a --export <keyid> | gpg -ear <keyid> > <keyfile>.asc`

Takže kdybych chtěl exportovat a zašifrovat můj vlastní klíč, vypadalo by to takhle.

`gpg -a --export ab68b241 | gpg -ear ab68b241 > ab68b241.asc`

Tímto vytvoříš soubor v současném adresáři, který můžeš nyní odeslat emailem zpět majiteli.

### Odeslání emailem

Předmět emailu by měl značit že zpráva obsahuje podepsaný klíč. Ujisti se že jsi ke zprávě přiložil exportovaný klíč a zašifruj také samotnou zprávu. Potom už můžeš klíč odstranit ze své klíčenky.

`gpg --delete-key <keyid>`

### Příjem klíčů

Poté co obdržíš email s tvým klíčem s novým podpisem, můžeš jej importovat do tvé klíčenky.

`gpg --import <keyfile>`

GnuPG output by měl ohlásit, že podpis byl úspěšně přidán k tvému klíči. Můžeš jej nyní nahrát na keyserver.

`gpg --keyserver pool.sks-keyservers.net --send-keys <keyid>`

A tímto tvoje práce skončila!

