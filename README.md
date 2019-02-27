# bash-scripts
Hasznos bash szkriptek.

### Adminisztráció
#### Szeretném becsomagolni a mappa tartalmát, amiben benne állok ZIP-be.
`zip -r ~/csomag.zip *`

#### Nincsen zip telepítve, de be kell csomagolnon egy komplett mappát.
`tar cfzv csomag.tar.gz ./ezt-az-egeszet/`

### Szövegfeldolgozás


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

### Git
