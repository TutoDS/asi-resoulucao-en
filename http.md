#### Configurar HTTP & HTTPS

##### contocontigo.pt

Para criar o **VHost** para o contocontigo.pt é necessário inicialmente criar o ficheiro de configuração, para tal: `nano /etc/httpd/conf.d/contocontigo.pt.conf`

Já dentro deste ficheiro é necessário adicionar o seguinte código:

```apache
<VirtualHost *:80>
	ServerName      www.contocontigo.pt
	ServerAlias     contocontigo.pt
	DocumentRoot    "/var/www/site"

	<Directory "/var/www/site">
			AllowOverride All
			Order allow,deny
			Allow from all
	</Directory>
</VirtualHost>
```

Após isso, guardar o ficheiro e reiniciar o apache através de um dos comandos que se seguem:

1. `apachectl restart`
2. `systemctl restart httpd.service`
3. `service httpd restart`

Posto isto é necessário criar a pasta, sendo ainda recomendado colocar um ficheiro `index.html` para testar posteriormente no lynx. Para tal basta seguir os comandos:

1. `mkdir /var/www/site`
2. `echo "<h1>Conto Contigo</h1>" > /var/www/site/index.html`

Caso seja **HTTPS** é necessário realizar algumas alterações. Em primeiro lugar é necessário gerar o certificado, executando para tal o comando que se segue.

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/pki/tls/private/apache-selsigned.key -out /etc/pki/tls/certs/apache-selfsigned.crt
```

> **Nota:** verificar que o `mod_ssl` se encontra instalado (`yum install mod_ssl`)

De seguida, é necessário voltar ao ficheiro de configuração do **VHost** (`/etc/httpd/conf.d/contocontigo.pt.conf`) e alterar o código existente para:

```apache
<VirtualHost *:443>
        ServerName      www.contocontigo.pt
        ServerAlias     contocontigo.pt
        DocumentRoot    "/var/www/site"

        <Directory "/var/www/site">
                AllowOverride All
                Order allow,deny
                Allow from all
        </Directory>

        SSLEngine on
        SSLCertificateFile /etc/pki/tls/certs/apache-selfsigned.crt
        SSLCertificateKeyFile /etc/pki/tls/private/apache-selsigned.key
</VirtualHost>
```

<br />

##### interno.contocontigo.pt

Para criar o **VHost** para o interno.contocontigo.pt é necessário inicialmente criar o ficheiro de configuração, para tal: `nano /etc/httpd/conf.d/interno.contocontigo.pt.conf`

Já dentro deste ficheiro é necessário adicionar o seguinte código:

```apache
<VirtualHost *:80>
	ServerName      interno.contocontigo.pt
	DocumentRoot    "/var/www/interno"

	<Directory "/var/www/interno">
			AllowOverride All
			Order allow,deny
			Allow from all
	</Directory>
</VirtualHost>
```

Após isso, guardar o ficheiro e reiniciar o apache através de um dos comandos que se seguem:

1. `apachectl restart`
2. `systemctl restart httpd.service`
3. `service httpd restart`

Posto isto é necessário criar a pasta, sendo ainda recomendado colocar um ficheiro `index.html` para testar posteriormente no lynx. Para tal basta seguir os comandos:

1. `mkdir /var/www/interno`
2. `echo "<h1>Conto Contigo - Interno</h1>" > /var/www/interno/index.html`

<br />

##### drive.contocontigo.pt

Para criar o **VHost** para o drive.contocontigo.pt é necessário inicialmente criar o ficheiro de configuração, para tal: `nano /etc/httpd/conf.d/drive.contocontigo.pt.conf`

Já dentro deste ficheiro é necessário adicionar o seguinte código:

```apache
<VirtualHost *:80>
	ServerName      drive.contocontigo.pt
	DocumentRoot    "/var/www/drive"

	<Directory "/var/www/drive">
			AllowOverride All
			Order allow,deny
			Allow from all
	</Directory>
</VirtualHost>
```

Após isso, guardar o ficheiro e reiniciar o apache através de um dos comandos que se seguem:

1. `apachectl restart`
2. `systemctl restart httpd.service`
3. `service httpd restart`

Posto isto é necessário criar a pasta, sendo ainda recomendado colocar um ficheiro `index.html` para testar posteriormente no lynx. Para tal basta seguir os comandos:

1. `mkdir /var/www/drive`
2. `echo "<h1>Conto Contigo - Drive</h1>" > /var/www/drive/index.html`

<br />

##### Testar acesso ao site

Para testar o acesso aos "sites" criados pode ser utilizado o **Lynx** (`yum install lynx`), bastando executar o comando `lynx` seguido de `http://` ou `https://` e o domínio.

Exemplo: `lynx https://contocontigo.pt`

> **Nota:** para ser possível testar o acesso ao site na máquina do HTTP (neste caso a **EX1**) é necessário adicionar no ficheiro `/etc/resolv.conf` os nameservers do **DNS** primário e secundário.
> Para tal, adicionar na primeira linha (mantendo a existente) `nameserver <ip-dns-primario>` e adicionar uma linha logo de seguida com `nameserver <ip-dns-secundario>`
