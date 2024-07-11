#!/bin/bash

# Erstelle Verzeichnisse für Schulklassen und Templates
mkdir _schulklassen
mkdir _templates

# Erstelle Textdatei für Klasse PE23c
echo "Mueller
Kulici
Mena
Roggli
Rutishauser
Naef
Miljkovic
Putin
Trump" > _schulklassen/PE23c.txt

# Erstelle Textdatei für Klasse PE23d
echo "Berset
Roesti
Ronaldo
Reci
Messi
Aslani" > _schulklassen/PE23d.txt

# Erstelle Template-Dateien
echo "oeppis" > _templates/file1.docx
echo "etwas anders" > _templates/file2.xlsx
echo "namal öppis" > _templates/file3.pdf

echo "fertig"


#!/bin/bash

# Erstelle Verzeichnis für generierte Dateien
mkdir _gen

# Schleife durch alle Klassendateien
for class in $(ls _schulklassen/*.txt)
do
    # Extrahiere den Klassennamen aus dem Dateinamen
    klassenname=$(echo $class | cut -d "/" -f 2 | cut -d "." -f 1)
    echo "Klassenname ist: $klassenname"
    
    # Erstelle Verzeichnis für die Klasse
    mkdir "_gen/$klassenname"
    
    # Schleife durch alle Namen in der Klassendatei
    for sname in $(cat "_schulklassen/$klassenname.txt")
    do
        # Erstelle Verzeichnis für den Schüler und kopiere Templates
        mkdir "_gen/$klassenname/$sname"
        cp _templates/* "_gen/$klassenname/$sname/"
    done
done
