#### Configurar DNS

**Dados:**

-   **Domínio:** contocontigo.pt
-   **DNS:**
    -   **Primário:** EX2
    -   **Secundário:** EX3

##### Primário

No ficheiro `/etc/resolv.conf` adicionar na primeira linha (mantendo a linha já existente):

```dns
nameserver 50.40.30.20
```

Já no ficheiro `/etc/named.conf` realizar as seguintes alterações:

```
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

acl internals {50.40.30.0/24;}; # IP Local Network

options {
	listen-on port 53 { 127.0.0.1; 50.40.30.20; }; # Adicionar ip da máquina

	// ...

	allow-query     { localhost; internals; }; # Adicionar internals
	empty-zones-enable no; # Adicionar esta linha

	// ...
};
```

Ainda neste ficheiro é necessário criar as zonas, para tal, adicionar o seguinte código antes das linhas `include "/etc/named.rfcxxxx.zones";`:

```
zone "contocontigo.pt" IN { # Domínio
	type master;
	file "/etc/bind/zones/contocontigo.pt.db"; # Ficheiro que contêm os registos de DSNS
	allow-query {localhost;internals;};
	allow-transfer {50.40.30.30;}; # IP da máquina com o DNS Secundário
};
```

Agora é necessário criar o ficheiro **`contocontigo.pt.db`**, para tal seguir os seguintes passos:

1. `mkdir /etc/bind`
2. `mkdir /etc/bind/zones`
3. `nano /etc/bind/zones/contocontigo.pt.db`

Já dentro do ficheiro referido é necessário adicionar o seguinte código:

```D
$TTL 1D
contocontigo.pt.        IN      SOA     ns1.contocontigo.pt.    . (
		2021021601      ;SERIAL
		28800           ;REFRESH
		3600            ;RETRY
		604800          ;EXPIRE
		38400           ;MINIMUM

	)

	IN      NS      ns1.contocontigo.pt.
	IN      NS      ns2.contocontigo.pt.


ns1     IN      A       50.40.30.20 # DNS Primário
ns2     IN      A       50.40.30.30 # DNS Secundário

;RECORDS
@       IN      A       50.40.30.10 # IP Máquina HTTP
www     IN      CNAME   contocontigo.pt.
drive   IN      A       50.40.30.10
interno IN      A       50.40.30.10
```

> **Nota:** a linha `2021021601 ;SERIAL` possui a versão do ficheiro, sendo esta gerada através de `<ano><mês><dia><versão>`, sendo necessário incrementar a **versão** sempre que este ficheiro seja alterado.

No fim de todos estes passos é necessário reiniciar o **BIND** para tal usar um dos comandos abaixo:

1. `systemctl restart named.service`
2. `service named restart`

##### DNS Secundário

No ficheiro `/etc/resolv.conf` adicionar na primeira linha (mantendo a linha já existente):

```dns
nameserver 50.40.30.30 # IP da Máquina
nameserver 50.40.30.20 # IP da Máquina DNS Primário
```

Já no ficheiro `/etc/named.conf` realizar as seguintes alterações:

```
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

acl internals {50.40.30.0/24;}; # IP Local Network

options {
	listen-on port 53 { 127.0.0.1; 50.40.30.20; }; # Adicionar ip da máquina

	// ...

	allow-query     { localhost; internals; }; # Adicionar internals
	empty-zones-enable no; # Adicionar esta linha

	// ...
};
```

Ainda neste ficheiro é necessário criar as zonas, para tal, adicionar o seguinte código antes das linhas `include "/etc/named.rfcxxxx.zones";`:

```
zone "contocontigo.pt" IN {
	type slave;
	file "/var/named/slaves/contocontigo.pt.db";
	allow-query { localhost; internals; };
	masters { 50.40.30.20; };
};
```

> **Nota:** o ficheiro `/var/named/slaves/contocontigo.pt.db` será criado automaticamente pelo **Bind**.

No fim de todos estes passos é necessário reiniciar o **BIND** para tal usar um dos comandos abaixo:

1. `systemctl restart named.service`
2. `service named restart`

##### Persistência do `resolv.conf`

Para resolver o problema dos valores do ficheiro `/etc/resolv.conf` é necessário em primeiro lugar listar os `networks-scripts` existentes na maquina, para tal:

-   `ls /etc/sysconfig/network-scripts/`

O resultado deve ser algo semelhante a:

```bash
ifcfg-enp0s<X>  ifcfg-enp0s<X>
```

Os comandos que se seguem são necessários para ambos os ficheiros existentes:

-   Abrir o ficheiro no **nano**: `nano /etc/sysconfig/network-scrips/ifcfg-enp0s<X>`
-   Adicionar a seguinte linha:
    `PEERDNS=no`
-   Guardar e sair do arquivo (no **nano** usar `CTRL + S` e `CTRL + X`)

> **Nota:** não esquecer repetir estes 3 passos para todos os ficheiros existentes.
