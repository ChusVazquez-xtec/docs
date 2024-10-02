dhcp.md
# Exercici 1: Instal.lació d'un servidor DHCP

!!! note "Descripció"

    **Objectiu**: Aprofundir i posar en pràctica els conceptes sobre DHCP
    
    **Desenvolupament**: Individual
    
    **Lliurament**: S'haurà d'entregar documentació


## 1. Preparació de l'escenari

Edita l’inventari de l’Ansible que tens instal.lat a la màquina "A" per tal de què ara tingui el següent contingut (on "ip.maq.B" i "ip.maq.C" han de substituïr-se pel seu valor adient):

``` ini title="/etc/ansible/hosts"
[victimes] 
ip.maq.B varB=manolo
ip.maq.C 
[victimes:vars] 
varGlobal=pepito
```

i executa la següent comanda, `ansible-playbook -e "varD=ana varE=felisa" hola.yml` , on "hola.yml" és un playbook amb el següent contingut. Observa quin és el contingut de cada variable mostrada (i raona per què):


``` yaml title="hola.yml"
- name: Playbook per veure els valors de variables definides a diferents llocs
  hosts: all  
  remote_user: pepito
  vars:
    varPlayD : "{{ varD }}"
    varPlayE : "{{ varE }}"
    varPlayF : "perico"
       tasks:
       - name: Veure les variables definides en l'inventori
         debug:  msg="varGlobal val {{ varGlobal }} i varB val {{ varB }}"
       - name: Veure les variables passades per paràmetre
         debug:  msg="varPlayD val {{ varPlayD }} i varPlayE val {{ varPlayE }}"
       - name: Veure les variables definides en el playbook
         debug:  msg="varPlayF val {{ varPlayF }}"
       - name: Veure les variables definides en la tasca
         vars: 
           varTaskG : "juan"
         debug:  msg="varTaskG val {{ varTaskG }}"
       - name: Veure el valor d'un determinat "fact"
         debug:  msg="La distribució de la víctima és {{ ansible_facts['distribution'] }}"
```

## Exercici 2

Crea un arxiu anomenat colors.yml a home/usuario amb el següent contingut:

``` yaml title="colors.yml"
favcolor: red
worstcolor: blue
```

i digues què mostra l'execució del següent playbook

``` yaml title="prova-colors.yml"
- name: Recollir variables d'un fitxer (sobreescrivint el valors definit al playbook...o no)
  hosts: all
  remote_user: pepito
  vars: 
    favcolor: blue
  vars_files:
    - /home/usuario/colors.yml
  tasks:
    - name: Mostrar els valors
      debug: msg="El valor de la variable 'favcolor' és {{ favcolor }} i el de 'worstcolor' és {{ worstcolor }}"
```

## Exercici 3

Prova el següent playbook, observa què passa i dedueix per a què serveixen les opcions `default` i `private`.

``` yaml title="playbook3.yml"
- name: Preguntar dades interactivament (els mateixos valors per totes les víctimes)
  hosts: all
  remote_user: pepito
  vars_prompt:
    - name: "nom"
      prompt: "What is your name?"
      default: "Simply the best"
      private: no
    - name: "contra"
      prompt: "What is your password?"
    - name: "favcolor"
      prompt: "What is your favorite color?"
      private: no
  tasks:
    - name: Veure el valor introduït
      debug:  msg="Your name is {{ nom }}, your pass is {{ contra }} , your color is {{ favcolor }}"
```

## Exercici 4

Executa el següent playbook i digues de quin tipus són els "facts" `{{ ansible_architecture }}`, `{{ ansible_apparmor }}` , `{{ ansible_all_ipv4_addresses }}` i `{{ ansible_mounts }}` i quins són els valors que el playbook mostra de cadascun d’ells.

``` yaml title="playbook4.yml"
- name: Ansible Variable Example Playbook
  hosts: victimes
  remote_user: pepito
  tasks:
  - name: Data types of some facts
    debug:
      msg: 
      - " Data type of 'ansible_architecture'  is {{ ansible_architecture | type_debug }} "
      - " Data type of 'ansible_apparmor' is {{ ansible_apparmor | type_debug }} "
      - " Data type of 'ansible_all_ipv4_addresses' is {{ ansible_all_ipv4_addresses | type_debug }} "
      - " Data type of 'ansible_mounts' is {{ ansible_mounts | type_debug }} "
  - name:  Printing a simple text value 
    debug:  'msg="One: {{ ansible_architecture }}"'
  - name: Accessing an entire list
    debug: 'msg="Two: {{ ansible_all_ipv4_addresses }}"'
  - name: Accessing the first element of a list
    debug:  'msg="Three: {{ ansible_all_ipv4_addresses[0] }}" '
  - name: Printing a dictionary
    debug:  'msg="Four: {{ ansible_apparmor }}"'
  - name: Accessing an element of dictionary
    debug: 'msg="Five: {{ ansible_apparmor.status }}"'
  - name: Printing a list of dictionaries
    debug:  'msg="Six: {{ ansible_mounts }}"'
  - name: Printing an element of the first element of the list of dictionaries
    debug:  'msg="Seven: {{ ansible_mounts[0].device }}"'
```

## Exercici 5

Digues abans de provar-les, quin creus que seria el valor del missatge mostrat pel mòdul debug a les diferents tasques següents, i després comprova que ho hagis encertat provant-les i observant el resultat:

``` yaml
- name: Ansible Filters Example Playbook
  hosts: victimes
  remote_user: pepito
  vars:
    llista: [1, 2, 3, 3, 2]
    cadena: "M'agraden els macarrons i els canelons"
  tasks:
  - name: U
    debug: msg="{{ cosa | default("lalala") }}"
  - name: Dos
    debug: msg="{{ cadena | password_hash("sha512","abcd") }}"
  - name: Tres
    debug: msg="{{ cadena | regex_findall("ca") }} i també {{ cadena | regex_search("^ca") }}"
  - name: Quatre
    debug: msg="{{ cadena | regex_replace("ca","kA") }}"
  - name: Cinc
    debug: msg="{{ llista | unique }}"
  - name: Sis
    debug: msg="{{ llista | join("+") }}"
  - name: Set
    debug: msg="{{ llista | intersect([1,3,4,3]) }}"
  - name: Vuit
    debug: msg="{{ "https://www.google.com/search?source=hp&q=macarrons&uact=5" | urlsplit("path") }}"
  - name: Nou
    debug: msg="{{ "https://www.google.com/search?source=hp&q=macarrons&uact=5" | urlsplit("query")  }}"
  - name: Deu
    debug: msg="{{ "/etc/pam.d/cups" | basename }}"
  - name: Onze
    debug: msg="{{ "/etc/pam.d/cups" | dirname }}"
  - name: Dotze
    debug: msg="{{ {"a":"1","b":"2"} | length }}"
```

## Exercici 6

Digues, sense provar el playbook següent què creus que mostrarà el mòdul debug a pantalla i seguidament prova'l per comprovar si has encertat:


``` yaml title="playbook6.yml"
- name: Misteri misteriós
  hosts: victimes
  remote_user: pepito
  tasks:
  - name: Lelele
    shell: "cat /etc/fstab"
    register: tintin
  - name: Lololo
    debug: msg="{{ tintin.stdout_lines }}"
```

## Exercici 7

Si tenim un fitxer anomenat "unfitxer.yml" amb el següent contingut...: 

``` yaml title="unfitxer.yml"
 - name: Lololo
   debug: msg="{{ tintin.stdout_lines }}"
```
 ...i, a la mateixa carpeta, tenim el playbook següent, digues, sense provar-lo, què creus que es mostrarà a pantalla i seguidament prova'l per comprovar si has encertat: 

``` yaml title="playbook7.yml"
- name: Misteri misteriós
   hosts: victimes
   remote_user: pepito
   tasks:
     - name: Lelele
       shell: "cat /etc/fstab"
       register: tintin
     - import_tasks: unfitxer.yml
```

## Exercici 8

 ¿Quina diferència hi ha entre el que es veuria en executar aquesta tasca:

``` yaml title="misteri2.yml"
- name: Misteri2
  debug: msg="{{lookup('env','HOME')}}"
```
i el que es veuria executant aquesta altra, la qual fa servir el "fact" ansible_env?

``` yaml title="misteri3.yml"
- name: Misteri3
  debug: msg="{{ ansible_env.HOME }}"
```

## Exercici 9

Escriu un playbook que contingui dues tasques a executar a les víctimes: la primera ha d'instal.lar un paquet (el que tu vulguis) i la segona ha de posar en marxa el servei Cups. Aquesta segona ha d'estar etiquetada. A partir d'aquí:

1. Executa el playbook sense indicar cap etiqueta. ¿Quines tasques s'executen?
2.  Executa el playbook indicant l'etiqueta de 2ª tasca amb el paràmetre -t. ¿Quines tasques s'executen ara? ¿Què passa si ara executes el playbook indicant l'etiqueta d'aquesta 2ª tasca amb el paràmetre --skip-tags?

## Exercici 10

Escriu un playbook que: 
 
* Instal·li el servidor Apache a les màquines víctimes
  
* Que just després d'aquesta instal.lació, mitjançant un "handler", habiliti el servei 
* Que copïi un fitxer "index.html" de la màquina controladora a la carpeta "/var/www/html" de les màquines víctimes 
* Que, just després d'aquesta còpia, mitjançant un altre "handler", posi en marxa el servei

## Exercici 1

En la màquina controladora, crea un arxiu anomena "index.html.j2" amb el següent contingut:

``` html title="index.html.j2"
<!DOCTYPE><html><body>
<h1>{{ ansible_hostname }}</h1><h3>{{ ansible_os_family }}</h3>
<p>{{ apache_test_message }}</p>
</body></html>
```
i també aquest playbook i executa’l:

``` yaml title="playbook11.yml"
- name: Mirar que el servidor Apache està instal·lat i iniciat amb una pàgina web  
  hosts: all
  remote_user: pepito
  become: yes
  vars:
    apache_test_message: Aquest missatge pot ser canviat
  tasks:
    - package: name=apache2 state=present
    - template: src=index.html.j2 dest=/var/www/html/index.html
    - service: name=apache2 state=started enabled=yes
```

Finalment accedeix amb el navegador de la màquina real al servidor Apache que hauria d'estar funcionant a la màquina B. 

¿Què veus i perquè? 

¿I si accedeixes al servidor Apache que està funcionant a la màquina C?



        
