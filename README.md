# Práctica ServidorApacheWeb
  Contenedor Server:
 - Creación del contenedor apachesever
 - Escogo la imagen httpd
 - La red crear se llamará  br02.
 - El mapeo de los puertos sera el 900 y 80
 - Ruta del volumen : ./Webv2:/usr/local/apache2/htdocs
        ASIR_web_2:
    image: httpd 
    ports:
    - '900:80'
    volumes:
     - ./Webv2:/usr/local/apache2/htdocs
     
    networks:
       br02:
        ipv4_address: 10.1.0.10

## Contenedor DNS Bind
 <p>Imagen del contenedor: internetsystemsconsortium/bind9:9.16</p>
 <p> La red creada sera br02 con la ip estatica 10.1.0.9</p>
 <p>Los puertos utilizados son el 53:53</p>
 <p> La ruta del volumen es: conf:/etc/bind</p>

version: "2.2"
services:
  asir_bind9_2:
    image: internetsystemsconsortium/bind9:9.16
    ports:
      - 53:53
    volumes:
      - conf:/etc/bind
    networks:
      br02:
       ipv4_address: 10.1.0.9

Contenedor Cliente
  Imagen a emplear : kasmweb/desktop-deluxe:1.9.0-rolling
  Mapeo de puertos en el puerto 6901.
  Contraseña para acceder al cliente:0341.
         asir_cliente_2:
    image: kasmweb/desktop:1.9.0-rolling
    ports:
      - '6901:6901'
    environment:
       - VNC_PW=0341
  
    stdin_open: true  # docker run -i
    tty: true         # docker run -t
    networks:
      - br02

volumes:
  conf:
    


networks:
   br02:
    external: true


## Creacion de los Host virtuales:
<VirtualHost *:80>
    
    DocumentRoot "/usr/local/apache2/htdocs/index1.html"
    ServerName pagina1.example.com
    ErrorLog "logs/dummy-host2.example.com-error_log"
    CustomLog "logs/dummy-host2.example.com-access_log" common
</VirtualHost>


## Configuracion del DNS:

;
; BIND data file for example.com
;
$TTL	604800
@	IN	SOA	example.com root.example.com. (
			      2		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	ns.example.com.
@	IN	A	10.0.0.10
@	IN	AAAA	::1
ns  IN  A   10.0.0.254
example.com. IN  A   10.0.0.10
pagina1 IN  CNAME   example.com.

## ZONA:
zone "example.com" {
    type master;
    file "/etc/bind/db.example.com";
};

## FORWARD:
forwarders {
	8.8.8.8; //google.com
	8.8.4.4; //google.com
	};
