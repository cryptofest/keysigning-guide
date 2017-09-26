# PGP Key Signing Party Guide

This guide should describe our way of doing things - sharing, signing and distributing keys. 

---

## TL;DR
* Share paper slips, employing careful checking
* Sign keys and identities which match the paper slips
* Send the signed keys via email

If anything goes wrong, you can simply delete the key, reimport it and start over.

`gpg --delete-key <keyid> && gpg --recv-keys <keyid>`

---

## 1. Key Sharing

The most important process of any key signing party is sharing your keys with others, which includes checking their identities as well.

### Printing Paper Slips

The distribution of keys is done by sharing paper slips which contain the individual's identities along with their email, name and fingerprint. These slips can be generated using a [paper slip generator](http://openpgp.quelltextlich.at/slip.html). You should bring quite a handful of them, as it is always better to have more.

### Sharing Keys

People should form two lines of the same length while facing each other. The formation should then start rotating in a manner of a tank belt after each pair is done checking and sharing slips.

![line-formation](line-formation.png)

Before you accept a paper slip of someone else, you **HAVE TO** do careful checking of their name and face by comparing their id card with their paper slip. If everything is in order, you may take the paper slip.

---

## 2. Signing

Signing all identities might take a while when you do things properly.

### Fetching Keys From a Keyserver

The paper slips you received should contain a fingerprint. Use that to fetch the key from a keyserver

`gpg --keyserver pool.sks-keyservers.net --recv-keys <keyid>`

### Checking the Fingerprint and Identities

Now print the fetched key's fingerprint using `gpg --fingerprint <keyid>` and check that it matches the fingerprint on the paper slip.

### Signing Valid Identities

When everything is in order, you may sign the key. We can choose the level of trust by using `--ask-cert-level`.

`gpg --sign-key --ask-cert-level <keyid>`

After going trough the prompt you will see this.

	How carefully have you verified the key you are about to sign actually belongs
	to the person named above?  If you don't know what to answer, enter "0".

  	(0) I will not answer. (default)
  	(1) I have not checked at all.
  	(2) I have done casual checking.
  	(3) I have done very careful checking.

	Your selection? (enter '?' for more information):

Since you have done careful checking, you can choose signature level 3.

If the key has multiple identities, you should then sign each identity individually. This is usually quite a lengthy process which can be automated using a script.

---

## 3. Redistributing Signed Keys

You have to export the key with your new signature, encrypt it and send it over to the owner.

The key should not be uploaded directly to a keyserver. Mailing the key is less convenient, but it is much safer. By sending it via email we make sure that the email address really belongs to the person you met on the key signing party. Furthermore, it allows the owner to check your signature before it goes public.

### Exporting and Encrypting

You may do this the simple way, where you issue a command to export the key, then you encrypt it and save it into a different file. But you can use this simple command as well.

`gpg -a --export <keyid> | gpg -ear <keyid> > <keyfile>.asc`

So if I were to export and encrypt my own key, it would look as the following.

`gpg -a --export ab68b241 | gpg -ear ab68b241 > ab68b241.asc`

This creates a file in your working directory, which is the file to be sent to the key owner.

#### Sending Via E-Mail

More cpomplicated but much safer way of key redistribution is mailing it as an encrypted attachment.

The mail title should describe that it contains the signed key. Make sure you attach the exported key, sign the message and encrypt it as well. This can be automated using a plugin for the most widely used email clients.

* Roundcube - [Enigma](https://github.com/roundcube/roundcubemail/tree/master/plugins/enigma)
* Thunderbird - [Enigmail](https://www.enigmail.net/index.php/en/)
* Claws Mail - [GPG Plugin](http://www.claws-mail.org/plugin.php?plugin=gpg)

After the key is uploaded, it can be deleted from the local keyring.

`gpg --delete-key <keyid>`

#### Uploading to a keyserver

Should you choose the easier way, you may upload the key to a keyserver directly.

`gpg --keyserver pool.sks-keyservers.net --send-keys <keyid>`

### Receiving keys

After you receive an email containing your key with a new signature, you should download the attachment and import it to your keyring.

`gpg -d <attachment> > <keyfile> & gpg --import <keyfile>`

The GnuPG output should say that a signature has been added to your key. Check the signature using `gpg --list-sig <keyid>`. If the signature is ok, you may upload it to a keyserver.

`gpg --keyserver pool.sks-keyservers.net --send-keys <keyid>`

And you are done!

