# Examen1-1-evaluacion_AdrianRodriguezSebastian

Crea un repositorio para contestar todo o exame.
Este repositorio ten que conter tódo-los ficheiros necesarios para xustifica-la túas respostas.

Contesta as seguintes preguntas, xustificándoas, nun README.md

**1. Explica métodos para 'abrir' unha consola/shell a un contenedor en execución**

Usando docker exec: O comando docker exec permite executar un comando nun contedor en execución. Para abrir unha consola interactiva usase o seguinte comando:
    docker exec -it <nome_do_contenedor> /bin/bash
y tambien con
    docker attach <nome_do_contenedor>

**2. No contenedor anterior (en execución), qué opciones tes que ter usado ó arrincalo para poder interactuar coas súas entradas e salidas**

docker run -it <nome_do_contenedor>
 

**3. Cómo sería un ficheiro docker-compose para que dous contenedores se comuniquen entre si nunha rede só deles?**


services:
  asir_bind9:
    container_name: asir_bind9
    image: ubuntu/bind9
    platform: linux/amd64  # Especifica la arquitectura de la imagen
    ports:
      - 54:53  # Redirige el puerto 53 del contenedor al puerto 54 del host
    networks:
      bind9_subnet:
        ipv4_address: 192.28.5.1  # IP estática para el contenedor Bind9
    volumes:
      - ./conf:/etc/bind/  # Mapea la carpeta local 'conf' a la configuración de Bind9
      - ./zonas:/var/lib/bind/  # Mapea la carpeta local 'zonas' a las zonas de Bind9

  cliente:
    container_name: cliente
    image: alpine  # Imagen ligera de Alpine
    tty: true
    stdin_open: true
    dns:
      - 192.28.5.1  # Configura el cliente para usar el DNS de Bind9
    networks:
      bind9_subnet:
        ipv4_address: 192.28.5.2  # IP estática para el contenedor cliente

networks:
  bind9_subnet:
    driver: bridge  # Usa una red de tipo 'bridge'
    ipam:
      config:
        - subnet: "192.28.0.0/16"  # Define el rango completo de IPs
          ip_range: "192.28.5.0/24"  # Rango de IPs asignados a los contenedores
          gateway: "192.28.5.254"  # Define la puerta de enlace de la red


**4. Qué tes que engadir ó ficheiro anterior para que un contenedor teña unha IP fixa?**
    No apartado network dos contenedores añadiremos unha IP especifica volvedose estatica e dicir q non cambia 

    networks:
      bind9_subnet:
        ipv4_address: 192.28.5.1  # IP estática para el contenedor

**5. Qué comando de consola podo usar para saber as ips dos contenedores anteriores?**
O comando q necesitamos para sabelo seria 
   Docker network inspect <nome_do_contenedor>

**6. Cál é a funcionalidade do apartado "ports" en docker compose?**
Serve para mapear os portos do contenedor a os portos da maquina host

**7. Para qué serve o rexistro CNAME? Pon un exemplo**
Serve para asignar un nome de dominio DNS con alias a outro co nome  principal

    alias IN CNAME Proba1

**8.Cómo podo facer para que a configuración dun contenedor DNS non se borre se creo outro contenedor?**
É no apartado de volumenes e onde se garda a configaracion do contenedor

    volumes:
      - ./conf:/etc/bind  #Gardar a direccion Bind
      - ./zonas:/var/lib/bind #Montar el directorio de zonas


**9. Engade unha zoa tendaelectronica.int no teu docker DNS que teña:**
- www á IP 172.16.0.1
- owncloud sexa un CNAME de www
- un rexistro de texto có contido "1234ASDF"
Comproba que todo funciona có comando "dig"
Mostra nos logs que o servicio funciona ben usando a saída da terminal ó levantar o compose ou có comando "docker logs [nomeContenedorOuID]"
(o apartado 9 realízase na máquina virtual)

Intente hacerlo pero me da error, en el archivo hay capturas (Examen1-1evalucaion>Capturas>), si ves en conf añadi el "dnssec-enable no" pero continua sin activar el contenedor. 
Aun asi lo que deberia aparecer seria lo siguiente:


Usando o comando:
    dig ns.tndaelectronica.int

;<<>>Dif 9.18.28-0ubuntu0.24.04.1-Ubuntu<<>> ns.tendaelectronica.int
;;global options: +cmd
;;Got answer:
;;->>HEADER<<- etc etc etc

;;ANSWER SECTION:
ns.tendaelectronica.int    38400    IN A    172.16.0.1

;; Query time 0 msec
;; Server


