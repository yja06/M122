#!/bin/bash

# Log-Datei
log_file="crypto_log.txt"

# Funktion zum Schreiben ins Log
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a "$log_file"
}

# URLs für die API-Abfragen
solana_url="https://api.coingecko.com/api/v3/simple/price?ids=solana&vs_currencies=usd"
bitcoin_url="https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd"
cardano_url="https://api.coingecko.com/api/v3/simple/price?ids=cardano&vs_currencies=usd"

# Funktion zum Abrufen der Preise
get_crypto_price() {
    local url="$1"
    curl -s "$url" | jq -r '.[].usd'
}

# Preise abrufen
solana_price=$(get_crypto_price "$solana_url")
bitcoin_price=$(get_crypto_price "$bitcoin_url")
cardano_price=$(get_crypto_price "$cardano_url")

# HTML-Datei erstellen
html_file="crypto_prices.html"
echo "<html><body><h1>Aktuelle Kryptowährungspreise</h1>" > "$html_file"
echo "<table border='1' style='width:50%;border-collapse:collapse;'>" >> "$html_file"
echo "<tr style='background-color:#f2f2f2;'><th style='padding:8px;text-align:left;'>Kryptowährung</th><th style='padding:8px;text-align:left;'>Preis (USD)</th></tr>" >> "$html_file"
echo "<tr><td style='padding:8px;text-align:left;'>Solana</td><td style='padding:8px;text-align:left;'>$solana_price</td></tr>" >> "$html_file"
echo "<tr><td style='padding:8px;text-align:left;'>Bitcoin</td><td style='padding:8px;text-align:left;'>$bitcoin_price</td></tr>" >> "$html_file"
echo "<tr><td style='padding:8px;text-align:left;'>Cardano</td><td style='padding:8px;text-align:left;'>$cardano_price</td></tr>" >> "$html_file"
echo "</table></body></html>" >> "$html_file"

log "HTML-Datei mit aktuellen Preisen erstellt."

# Hauptprogramm
log "Skript ausgeführt und abgeschlossen."
