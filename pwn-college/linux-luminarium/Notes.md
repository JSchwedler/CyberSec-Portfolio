# Linux Luminarium
## Schnellreferenz
- [Dateisystem](#dateisystem)
- [Shell Basics](#shell-basics)
- [Prozesse](#prozesse)
- [Berechtigungen](#berechtigungen)
- [Shell-Skripting](#shell-skripting)
- [Multiplexers](#multiplexers)

## Dateisystem
`/usr` steht für _Unix System Resource_ und enthält Systemressourcen<br>
`/usr/bin` sind binaries von Programmen<br>
`/usr/lib` sind geteilte Libraries<br>
`/usr/share` sind Programmressourcen (Icons, Assets)<br>
`/etc` ist Sys Config<br>
`/var` sind logs, caches, etc<br>
`/proc` Runtime Process Data<br><br>
`pwd` gibt aktuelles working directory aus<br>
`ls` listet alle Files aus aktuellem Verzeichnis auf<br>
`ls -l` zeigt je File Berechtigungsfalgs und Filetype an<br>
`ls -a` zeigt alle Files, auch Versteckte (`.NAME` versteckt Files)<br>
`grep SEARCHSTRING` sucht nach SEARCHSTRING<br>
`diff` zeigt den Unterschied zwischen zwei Files (Verglichen wird Zeile für Zeile)<br>
`touch` erstellt neues, leeres File<br>
`rm` löscht File<br>
`mv SOURCE DESTINATION` verschiebt SOURCE nach DESTINATION<br>
`cp SOURCE DESTINATION` kopiert SOURCE nach DESTINATION<br>
`mkdir NAME` steht für _make directory_ und erzeugt Verzeichnis mit dem Namen NAME<br>
`find` um Files zu finden; wenn kein Verzeichnis angegeben, dann wird in Aktuellem gesucht<br>
`ln -s` erzeigt einen symbolic link, auch soft link oder symlink genannt; der erzeugte Link zeigt nur auf ein anderes File<br>
`ln` erstellt hard links; zeigt direkt auf selbe Daten wie anderes File<br>
`man` steht für _manual_ und ermöglicht Informationen über Befehl zu finden, z.B. mögliche Parameter<br>

## Shell Basics
`?` stellt eine single-wildcard dar, d.h. es repräsentiert einen Character<br>
`*` stellt eine multi-wildcard dar, d.h. es repräsentiert null oder mehr Characters<br>
`[]` match't nur auf das den Inhalt zwischen den Klammern, z.B. [ab] auf "a" oder "b"<br>
`>` erstellt/ überschreibt file mit content von vor ">" (stdout)<br>	
Der File Descriptor (FD) ist eine Nummer, die den Kommunikationschannel beschreibt, wobei 0 für stdin, 1 für stdout und 2 für stdErr steht <br>
`>>` und `2>>` sind append-mode<br>
`>&` leitet zu einem anderen File Descriptor weiter<br>
`>` ist pipe-able, da errors nicht direkt pipe-able<br>
`|` stdout von links wird als stdin von rechts genutzt<br>
`grep -v SEARCHSTR` zeigt alles, was SEARCHSTR nicht enthält<br>
`sed` ist Stream EDitor und dient zum Bearbeiten von Datenströmen; Bsp: `sed "s/OLDW/newE/g"` bedeutet substitute "OLDW" word to replace "newW" global<br>
`tee` dupliziert Daten in beliebig vielen angegebenen Files<br>
Prozess Substitution: `<(COMMAND)` bedeutet Shell legt temp File an; `>(COMMAND)` bedeutet man schreibt zu COMMAND<br>
`mkfifo` erstellt benanntes Pipe; nutzt FIFO Prinzip; Input & Output müssen angeschlossen sein, sonst funktioniert Pipe nicht<br>
`$NAME` um auf Variable _NAME_ zugreifen<br>
`NAME=WERT` erstellt eine Variable mit dem Namen _NAME_ und dem Wert _WERT_; wichtig keine Leerzeichen vor oder nach dem '='<br>
`export NAME` exportiert die Variable _NAME_ in das _environment_, sodass sie nicht nur in der lokalen Shell gesetzt ist; kann auch direkt bei Variablendeklaration genutzt werden <br>
`sh` startet eine minimal Shell<br>
`env` zeigt alle exportierten Variablen an<br>
Command Substitution: `NAME=$(COMMAND)` so wird der Befehl _COMMAND_ in der Variable _NAME_ gespeichert<br>
`read` um Input einzulesen<br>
`read -p "PROMPT MESSAGE" NAME` zeigt dem User _PROMPT MESSAGE_ an und speichert den Userinput in die Variable _NAME_<br>
`read NAME < FILENAME` liest den Inhalt von _FILENAME_ ein und speichert ihn in die Variable _NAME_<br>
`tr FIRST SECOND` ersetzt _FIRST_ mit _SECOND_, z.B. `echo OWN | tr O P` erzeugt den Output PWN<br>
`tr -d CHAR` löscht den angegebenen Character<br>
`head FILE`<br> gibt die ersten n Lines aus der Datei _FILE_ aus (wenn nicht mit Parameter _-n ZAHL_ spezifiziert, ist Standard 10 Lines) 
`cut -d ' ' -f NR FILE` spaltet den Inhalt von der Datei _FILE_ anhand des in Parameter _-d_ angegebene Trennzeichen in Spalten und gibt dann die in Parameter _-f_ angegebene Spalte aus <br>
`sort` sortiert den Output; Standard ist alphabetisch: _-r_ ist Reverse; _-n_ sortiert nummerisch;  _-u_ zeigt nur Uniques (keine Duplicates); _-R_ ist eine zufällige Sortierung <br>
`echo *` gibt alle Files in aktuellem Verzeichnis aus<br>
Die _PATH_ Variable speichert die verschiedenen Verzeichnispfade, welche für die Auflösung der commands nötig sind. <br>
`which COMMAND` zeigt den Pfad zum command COMMAND.<br>
_.bashrc_ ist world-readable by default.<br>

## Prozesse
`ps` steht für _Process snapshot_ oder _Process status_ und listet alle aktuellen Prozesse auf; _-e_ zeigt jeden Prozess an; _-f_ zeigt das vollständige Format an <br>
BSD Syntax von `ps` ist _a_ zeigt Prozesse aller User; _x_ zeigt Prozesse, die nicht im Terminal laufen; _u_ erzeugt user-readable Output<br>
`kill PID` beendet Prozess mit der Prozess-ID _PID_<br>
_CTRL+Z_ suspended den aktuellen Command, sodass er im Hintergrund weiter läuft<br>
`fg` holt einen Command aus den Background wieder in den foreground<br>
`bg` setzt den Command im Hintergrund fort<br>
Befehl  direkt im Hintergrund starten via`COMMAND &`, d.h.  einfach "&" an den Befehl anhängen<br>
`echo $?` gibt letzten Exitcode aus<br>

## Berechtigungen
`/etc/passwd` enthält alle User vom Betriebssystem im Format _NAME:x:UserID:groupID:user_details:home_directory:default_Shell_<br>
`su` und `sudo` um root-Rechte zu erhalten; _su_ steht für substitute user; _sudo_ steht für superuser do<br>
`su USERNAME` meldet den aktuellen Nutzer als _USERNAME_ an<br>
Das Nutzerpasswort wurde früher in _/etc/passwd_ gespeichert, heute ist es in /etc/shadow; wenn diese Stelle leer ist, dann wurde kein Passwort gesetzt, wenn es ein langer String ist, dann ist es der Hash vom Nutzerpasswort<br>
Befehl `sudo` prüft Policies, ob der Nutzer den befehl als root ausführen darf (in /etc/sudoers festgelegt)<br>
Aufschlüsselung Berechtigungsflags von `ls -l`: erster Character gibt Filetype an; nächsten neun Characters geben Zugriffsberechtigungen an (ersten drei für Owner, nächsten drei für File-Gruppe und letzten drei für alle anderen Nutzer), dann kommt die Spalte _owner_ und danach die Spalte _owner group_.<br><br>

Authorisierung ist, was der Nutzer  auf dem System tun kann und wird via Policies geregelt. Authentication ist das beweisen, dass der Nutzer wirklich der Nutzer ist.<br>
Access Control ist Mechanismus, um Policy durchzusetzen. 
Modeling Access Control: Subject _S_ ist Actor auf dem System. Objekt _O_ ist Asset oder Objekt im System. Rights _R_ geben an, was _S_ an _O_ tun kann.<br>
Access Control List (ACL) braucht Authentication von _S_, Capability braucht keine Authentication für _S_. Capabilities müssen unfälschbar sein und die Propagation kontrolliert werden.<br>
ACL ist besser für Zugriffeinsicht von _O_, CAP ist besser für Zugriffseinsicht von _S_.<br>
POSIX ACL ist Standard und besteht aus 12 Bits für jedes File. Es ist logisch gruppiert in vier Sets von je drei Bits. Die erste Gruppe besteht aus SUID, SGID und Sticky-Bits. Die nächsten drei Sets geben Rechte für Owner, File Group und alle anderen bezüglich _read_,_write_ und _execute_ an. SUID steht für set user ID, SGID steht für set group ID.<br>
`chown USER FILE` ändert den Besitzer des Files _FILE_ zu dem User _USER_.<br>
`id` zeigt in welchen Gruppen der User ist. <br>
`chgrp` ändert die Gruppe vom angegeben File<br>
`chmod` ändert die Berechtigungen vom angegebenen File, z.B. _u+r_ bedeutet, dass der Owner zusätzlich lesen kann, _g-w_ bedeutet, dass die Gruppe nichts mehr ändern kann, _o-x_ bedeutet, dass andere es nicht mehr ausführen können. _o=-_ besagt, dass der owner keine Berechtigungen mehr hat. Man kann auch alle Berechtigungen in Form der neun Stellen angeben (die gleiche Form wie es bei ls -l steht).

## Shell Skripting
`COMMAND1; COMMAND2` lässt mehrere Commands mit einem Aufruf ausführen.<br>
`COMMAND1 && COMMAND2` stellt eine command chain dar, d.h. COMMAND2 wird nur ausgeführt, wenn COMMAND1 erfolgreich ausgeführt wurde.<br>
`COMMAND1 || COMMAND2` bedeutet, dass wenn COMMAND1 fehlschlägt, COMMAND2 ausgeführt wird.<br>
`#!/PATH` befindet sich wenn dann in der ersten Zeile eines Skriptes. _#!_ wird shebang genannt und _PATH_ gibt den Pfad zum Interpreter an, z.B. /bin/bash<br>
Um Argumente vom Skriptaufruf zu nutzen, kann man im Skriptcode via `$1 $2 ... $i` auf das entsprechende Argument (z.B. das i-te Argument) zugreifen.<br>
Eine Verzweigung wird wie folgt dargestellt:
```
if [ BEDINGUNG ]
then
    COMMAND1
elif [ BED ]
then
    COMMAND2
else
    COMMAND3
fi
```
Wichtig dabei ist, dass die öffnende Klammer jeweils von einem Leerzeichen umgeben ist und das ein Leerzeichen vor der schließenden Klammer ist.
## Multiplexers
`screen` erzeugt virtuelle Terminals im Terminal. Mittels _CTRL+A_ mit einem folgenden _d_ im virtuellen Terminal detached man vom virtuellen Terminal und kann sich danach mittels `screen -r` re-attachen.<br>
`screen -ls` listet alle laufenden virtuellen Terminals auf. Wenn mehrere virtuellen Terminals laufen, kann man mittels `screen -r PID` oder `screen -r NAME` zum gewünschten virtuellen Terminal re-attachen.<br>
Innerhalb eines virtuellen Terminals sind mehrere Windows möglich. Die Tastenkombination  _CTRL+A_ mit einem folgenden _c_ kann verwendet werden, um ein neues Window zu erzeugen,  _CTRL+A_ mit einem folgenden _n_ um zum vorherigen Window zu gelangen,  _CTRL+A_ mit einem folgenden _p_ um zum previous Window zurück zu kehren.  _CTRL+A_ mit einem folgenden _"_ zeigt alle vorhandenen Windows an.<br>
`tmux` ist die moderne Variante von _screens_. Es verwendet intern die Tastenkombination  _CTRL+B_. Um alle Vorhandenen anzeigen zu lassen wird tmux ls genutzt, um sich zu re-attachen wird `tmux attach` oder `tmux a` verwendet. Mit  _CTRL+B_ mit einem folgenden _w_ wird die Übersicht aller vorhandenen Windows angezeigt.<br>


## Weiterführende Links
- [Bash tutorial](https://bash.cyberciti.biz/guide/Main_Page)
- [Linux man pages](https://man7.org/linux/man-pages/)
- [Shell expensions](https://www.gnu.org/software/bash/manual/html_node/Shell-Expansions.html)
- [Linux I/O redirect](https://web.archive.org/web/20220629044814/http://bencane.com:80/2012/04/16/unix-shell-the-art-of-io-redirection/)
