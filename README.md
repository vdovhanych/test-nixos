# nixos-lemp


Krátka dokumentace postupu instalace NixOS, konfigurace configuration.nix, vytvoření a konfigurace základní sql databáze.

Postup instalace NixOS
  - stáhnout iso @ https://channels.nixos.org/nixos-20.09/latest-nixos-gnome-x86_64-linux.iso
  - provést instalaci systému podle návodu @ https://nixos.org/manual/nixos/stable/
  - vygenerování configuration.nix a nahradit jej souborem z repo do umíštění /etc/nixos/configuration.nix
  
Popis konfigurace NixOS configuration.nix
  - nastavení prostředí GNOME
  - přidání aplikací do pole environment.systemPackages
  - Přidání potřebných příkazu pro spuštění všech potřebných služeb, httpd server (nginx), databáze (mariadb mysql) a podpora php na webovém serveru.
  
  Základní aktivace služeb: 
    - MariaDB 
        services.mysql = {
        enable = true;
        package = pkgs.mariadb;
        bind = "localhost";
        
    - Ngnix
        services.nginx = {
        enable = true;
        virtualHosts."localhost" = {
        root = "/var/www/webtest";
        locations."~ \.php$".extraConfig = ''
        fastcgi_pass  unix:${config.services.phpfpm.pools.mypool.socket};
        fastcgi_index index.php;
          };
        };
        
    - php-fpm
        services.phpfpm.pools.mypool = {  
        user = "nobody"; 
        settings = {
        pm = "dynamic";            
        "listen.owner" = config.services.nginx.user; 
        "pm.max_children" = 5;                                                                                                             
        "pm.start_servers" = 2; 
        "pm.min_spare_servers" = 1;
        "pm.max_spare_servers" = 3; 
        "pm.max_requests" = 500; 
          }; 
        };  
        
        
 Příklady konfigurace
  - Databáze
  
  services.mysql - příkaz udává nixu aby při instalaci připravil tuto službu pro celý systém 
  = {
        enable = true; - tímhle říkam že chci aby myslq služba byla zapnuta
        package = pkgs.mariadb; - vybírám jaký konkrétní pkg se použije z nix storu
        bind = "localhost"; - definuji použití pro localhost
        
  - Ukázková databáze "testDB" login stejný jako systém
    - Setup pro zobrazování aktuálních cen crypta a zda jsou podporovány v Trezoru. Rozděleno na name,supportedont,currentprice,rise
    
