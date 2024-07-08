#!/bin/bash

# Log-Datei
log_file="installation_log.txt"

# Funktion um das Log zu schreiben
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a "$log_file"
}

# Funktion um die Konfigurationsdatei zu lesen
read_config() {
    if [ ! -f "$1" ]; then
        log "Fehler: Konfigurationsdatei $1 nicht gefunden."
        exit 1
    fi
    source "$1"
}

# Funktion zur Installation der Tools
install_tools() {
    log "Installation der Tools beginnt..."
    for tool in "${tools[@]}"; do
        if ! dpkg -l | grep -qw "$tool"; then
            sudo apt-get install -y "$tool"
            if [ $? -eq 0 ]; then
                log "Erfolgreich installiert: $tool"
            else
                log "Fehler bei der Installation: $tool"
            fi
        else
            log "$tool ist bereits installiert."
        fi
    done
}

# Funktion für die Durchführung von Tests
run_tests() {
    log "Tests beginnen..."
    for tool in "${tools[@]}"; do
        if dpkg -l | grep -qw "$tool"; then
            log "Test bestanden: $tool ist installiert."
        else
            log "Test fehlgeschlagen: $tool ist nicht installiert."
        fi
    done
}

# Hauptprogramm
echo "Bitte geben Sie den Pfad zur Konfigurationsdatei an:"
read config_file

log "Start des Installationsskripts"
read_config "$config_file"
install_tools
run_tests
log "Installation und Tests abgeschlossen."
