## **ASI** - Época Normal <small>2020/2021</small>

| **Host** | **Funcionalidades** | **Endereço** | **Acesso** | **Obs.**
| --- | --- | --- | --- | --- | --- |
| EX1 | LVM, E-mail e HTTP | 50.40.30.10 | `root` / `epn2021` | Firewall ativa
| EX2 | DNS (primário) | 50.40.30.20 | `root` / `epn2021` | Firewall ativa
| EX3 | DNS (secundário) | 50.40.30.30 | `root` / `epn2021` | Firewall ativa

### Notas

Todas as máquinas possuem *firewall* ativa, para tal é necessário abrir as portas de DNS e HTTP/HTTPs:

```bash
firewall-cmd --zone=public --permanent --add-port 80/tcp
firewall-cmd --zone=public --permanent --add-port 443/tcp
firewall-cmd --zone=public --permanent --add-port 53/tcp
firewall-cmd --zone=public --permanent --add-port 53/udp
firewall-cmd --reload
```

Antes de configurar as máquinas garantir que está tudo instalado, se não instalar seguindo os comandos:

```
yum install lynx httpd bind bind-utils wget postfix dovecot mailx mod_ssl
```

#### Comandos mais utilizados

<div style="display: flex">
<div style="width: 50%">

**Reiniciar Apache <small>(CentOS)</small>**
1. `apachectl restart`
2. `systemctl restart httpd.service`
3. `service httpd restart`

</div>

<div style="width: 50%">

**Verificar Estado Apache <small>(CentOS)</small>**
1. `apachectl status`
2. `systemctl status httpd.service`
3. `service httpd status`

</div>
</div>

<br />

<div style="display: flex">
<div style="width: 50%">

**Iniciar Apache <small>(CentOS)</small>**
1. `apachectl start`
2. `systemctl start httpd.service`
3. `service httpd start`

</div>

<div style="width: 50%">

**Reiniciar BIND (DNS) <small>(CentOS)</small>**
1. `service named restart`
2. `systemctl restart named.service`

</div>
</div>

<br />

<div style="display: flex">
<div style="width: 50%">

**Verificar Estado BIND (DNS) <small>(CentOS)</small>**
1. `service named status`
2. `systemctl status named.service`

</div>

<div style="width: 50%">

**Iniciar BIND (DNS) <small>(CentOS)</small>**
1. `service named start`
2. `systemctl start named.service`

</div>
</div>

<br />

<div style="display: flex">
<div style="width: 50%">

**Verificar Estado BIND (DNS) <small>(CentOS)</small>**
1. `service named status`
2. `systemctl status named.service`

</div>

<div style="width: 50%">

**Iniciar BIND (DNS) <small>(CentOS)</small>**
1. `service named start`
2. `systemctl start named.service`

</div>
</div>

<br />

<div style="display: flex">
<div style="width: 50%">

**Reiniciar PostFix (Email) <small>(CentOS)</small>**
1. `systemctl restart postfix`

</div>

<div style="width: 50%">

**Reiniciar Dovecot (Email) <small>(CentOS)</small>**
1. `systemctl restart dovecot`

</div>
</div>

