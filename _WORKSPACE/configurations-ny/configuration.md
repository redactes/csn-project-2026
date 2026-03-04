# --- Basis Configuratie ---
enable
configure terminal
hostname R1
no ip domain-lookup           # Voorkomt vertragingen door DNS-lookups bij typefouten

# --- Beveiliging en Gebruikers ---
enable secret cisco           # Beveiligd wachtwoord voor de 'enable' mode
username admin secret cisco   # Lokale user voor SSH-toegang

# --- SSH Instellingen ---
ip domain-name safeus.local
# Opmerking crypto key wordt gegenereerd met 1024 bits
crypto key generate rsa
1024
ip ssh version 2              # Gebruik de veiligere SSH versie 2

# --- Toegang tot de Lijnen ---
line console 0
 password cisco
 login

line vty 0 4
 login local                  # Gebruikersnaamwachtwoord uit lokale database
 transport input ssh          # Sta alleen SSH-verbindingen toe (geen Telnet)

# --- Fysieke Interfaces ---
interface g001
 description Verbinding naar R2
 ip address 10.101.2.225 255.255.255.252
 no shutdown

interface g000
 no shutdown                  # Activeer de fysieke interface voor sub-interfaces

# --- Sub-interfaces (Router-on-a-Stick) ---
interface g000.10
 encapsulation dot1Q 10       # VLAN 10 tagging
 ip address 10.101.0.254 255.255.255.0

interface g000.20
 encapsulation dot1Q 20       # VLAN 20 tagging
 ip address 10.101.1.254 255.255.255.0

interface g000.100
 encapsulation dot1Q 100      # VLAN 100 tagging
 ip address 10.101.2.126 255.255.255.192

interface g000.200
 encapsulation dot1Q 200      # VLAN 200 tagging
 ip address 10.101.2.62 255.255.255.192

# --- Statische Routing ---
# Routes naar de netwerken achter R2
ip route 10.101.10.0 255.255.255.0 10.101.2.226
ip route 10.101.11.0 255.255.255.0 10.101.2.226

# Default Gateway (Route voor al het overige verkeer)
ip route 0.0.0.0 0.0.0.0 10.101.2.226

end
write memory                   # Opslaan van de configuratie