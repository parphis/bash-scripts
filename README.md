# bash-scripts
Hasznos bash szkriptek.

### Adminisztráció
#### Adobe Reader telepítése Debian 9-re. (utils/AdbeRdr9.5.5-1_i386linux_enu.deb)
```
wget ftp://ftp.adobe.com/pub/adobe/reader/unix/9.x/9.5.5/enu/AdbeRdr9.5.5-1_i386linux_enu.deb
gdebi AdbeRdr9.5.5-1_i386linux_enu.deb
```

#### A Network Manager-el szeretnék hozzáadni egy openvpn konfig fájlt.
apt-get install network-manager-openvpn-gnome openvpn-systemd-resolved
nmcli connection import type openvpn file /home/parphis/viamap/coenodat/okologia_marta.gergely.ovpn 

#### Adott egy fájl, benne fájlnevekkel. Ezek helyfoglalását, méretét kell kiszámolni egyesével.
`for f in `cat files.log`; do s=`stat -c%s $f`; let "s=$s/1024"; echo $s done`

#### Szeretném becsomagolni a mappa tartalmát, amiben benne állok ZIP-be.
`zip -r ~/csomag.zip *`

#### Nincsen zip telepítve, de be kell csomagolnon egy komplett mappát.
`tar cfzv csomag.tar.gz ./ezt-az-egeszet/`

#### Szeretnék felmásolni SSH-n keresztül egy fájlt a távoli gépre. A -r recurzíven másol, a -P megmondja a portot, ha nem az alap 22-es az.
`scp -r -P 52347 valami.zip  USER@HOST:FULLPATH`

#### Kell egy mappa, de ne legyen hiba, ha már létezik.
`mkdir -p ujmappa`

#### Debian alatt kell használnom a Novell mappákat
Találtam egy csomagot, amit lehet telepíteni https://launchpadlibrarian.net/122879995/ncpfs_2.2.6.orig.tar.gz,
de @jmarton által összeállított csomagban benne van minden.
Függőségei: libncp_2.2.6-9, és libpam-ncp_2.2.6.-9
Telepítés után így lehet mountolni (mindegyik meghajtó egy-egy mappaként fog megjelenni):
`ncpmount -S ORCA -A [IP] -U [novell-user] -u [local-user] [mount-point]`

### Szövegfeldolgozás
#### Új sor karakter van a sztring végén, de nem kellene, hogy ott legyen.
`permission=${permission//[$'\t\r\n']}`

#### Szeretném egy tömbbe tölteni egy szövegfájl egyes sorait. (BASH >4)
```
readarray arr < ../DSpace/dspace_groups.lst
echo ${arr[0]} # a 0. eleme a tömbnek
```

#### Egy sztringnek kell az első négy karaktere.
`ls -1 . | cut -c1-4`

#### Adott egy mappa, tele fájlokkal. A fájlok nevei számok és jpg a végük. Ezeket szeretném másik mappákban mozgatni úgy, hogy a mappa neve a fájl nevének az első négy karaktere legyen.
`find . -type f -name "*.jpg" | while read i; do d="$(echo `basename $i` | cut -c1-4)"; mkdir -p "../$d/"; mv $i "../$d/"; done`

### Keresés
#### Keresek egy fájlt, aminek nem ismerem a teljes nevét. A keresés kiindulópontja az aktuális mappa.
`find . -type f -name "*_url.txt"`

#### Keresek egy szövegrészt egy adott fájlban, és szeretném azt is tudni, melyik sorban van.
`grep -Rn "https://" --include "*.php" . | while read x; do echo $x; done`
`find . -type f -name "*.php" -exec grep -rne 'https' {} \;`

#### Keresek egy szövegrészt egy adott fájlban, és szeretném tudni, melyik sorban van, de csak egy részét szeretném látni.
`grep -Rn "https://" --include "*.php" ./subfolder | while read x; do echo ${x:0:230}; done`


### Programozás
#### Szeretnék összehasonlítani két fájlt.
`vimdiff 20190222190840_341733007_final_array.txt 20190222204937_17047325_final_array.txt`

#### Futtatnék PHP szkriptet, de ne kelljen hozzá forrásfájlt készíteni.
`php -r 'echo phpinfo();'`

#### Szeretnék csak belenézni egy gzip-be, de nincsen MC.
`vim file.gz`

#### JavaScriptben van egy nagyon hosszú sztring, és ezt szeretném átnézni, de a `console.log` metódus csak egy részét mutatja meg.
```
  var link = document.createElement('a');
  link.setAttribute('href', 'data:text/plain,'+jsonString);
  link.setAttribute('download', 'jsonString.txt');
  document.getElementsByTagName("body")[0].appendChild(link).click();
```

#### Szeretnék látni egy fájlt hexa formában, mint ahogy például az MC hexa nézete, csak mindezt Vim-ben.
```
:e <a fájl>
:%!xxd
```

### Git
#### LFS telepítése, beállítása
```
git lfs install # minden repo-ra külön kell meghívni
git lfs track "*.EXT" # többféle minta is illeszthető ide, de lehet szerkeszteni a .gitignore fájlt is kézzel
git add .gitattributes
```
Ha már a history-ban lévő állományokra is érvényesíteni kell, hogy LFS-ben legyenek tárolva, akkor vagy szerkeszteni kell azokat, vagy `rebase` paranccsal újraírni a history-t.

### ImageMagick
#### Kellene n darab kép adott mérettel, amik mond másként néznek ki.
```
#!/bin/bash

convert -size 80x80 xc:green -random-threshold 1x99% background.ppm;

for i in `seq -w 01 40`
do
convert background.ppm -fill white -draw "scale 4,4 gravity center text 0,0 '$i'" -quality 98 image_$i.jpg
done
exit;
```

### Vagrant

#### Vagrant-DSpace telepítésnél jött elő, hogy a Github-ról nem tudott letöltődni a DSpace repo.
Hogy ez megoldódjon, a hoszt gépen kellett a Github felhasználómnak az SSH publikus kulcsát hozzáadni az `ssh-add` paranccsal az ssh ügynökhöz. Így már le tudta tölteni a Gitről a DSpace cuccokat.

### Raspberry Pi

#### Képfájlt szeretnék sima `dd` paranccsal másolni a kártyára
(Installing OS images on Linux)[https://www.raspberrypi.org/documentation/installation/installing-images/linux.md]
`lsblk` vagy hasonló paranccsal kell megnézni a diszk/sd kártya nevét, és azt kell beírni az `of=`-hoz.

```
unzip -p 2018-11-13-raspbian-stretch.zip | sudo dd of=/dev/sdX bs=4M conv=fsync status=progress
```
A `bs=4M` helyett lehet, hogy `1M` kellhet.

