---
language: de
anchor: nameEncryption
title: Namen verschlüsseln
---
<p class="lead">Neben den Dateiinhalten werden auch die Dateinamen verschlüsselt.</p>

Cryptomator nutzt den Modus <a href="http://tools.ietf.org/html/rfc5297" target="_blank">AES-SIV</a>, um Dateien und Verzeichnisnamen zu verschlüsseln. Zusätzlich wird eine eindeutige Verzeichnis-ID des übergeordneten Verzeichnisses als Zusatzdaten übergeben. Dies verhindert das Verschieben von verschlüsselten Dateien in andere Verzeichnisse.

<pre>
cipheredName := base32(aesSiv(cleartextName, parentDirId, encryptionMasterKey, macMasterKey))
</pre>

Bei Dateien ist das Ergebnis der verschlüsselte Name.

Handelt es sich hingegen um ein Verzeichnis, wird ein Unterstrich angehängt. Dann wird eine Datei dieses Namens erstellt, in die wir eine eindeutige Kennzeichnung (genauer eine <abbr title="Universally unique identifier" class="initialism">UUID</abbr>) schreiben. Das dazugehörige Verzeichnis wird dann an folgendem Ort gespeichert:

<pre>
dirId := createUuid()
dirIdHash := base32(sha1(aesSiv(dirId, null, encryptionMasterKey, macMasterKey)))
dirPath := vaultRoot + &apos;/d/&apos; + substr(dirIdHash, 0, 2) + &apos;/&apos; + substr(dirIdHash, 2, 30)
</pre>

Indem alle Verzeichnisse nebeneinander liegen, wird nicht nur die Verzeichnishierarchie verschleiert, sondern es wird auch die Pfadtiefe begrenzt, um die Kompatibilität mit einigen Cloud-Diensten sicherzustellen.