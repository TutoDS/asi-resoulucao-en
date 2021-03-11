
#### Configurar Email

Em primeiro lugar é necessário verificar se o `postfix` e o `dovecot` estão instalados, para isso:

```bash
yum install postfix dovecot
```

Posteriormente é necessário realizar alterações no ficheiro de DNS criado na máquina do DNS Primário, mais especificamente no ficheiro  `/etc/bind/zones/contocontigo.pt.db` e realizar as alterações assinaladas no código que se segue:

```D
$TTL 1D
contocontigo.pt.        IN      SOA     ns1.contocontigo.pt.    . (
		2021021601      ;SERIAL
		28800           ;REFRESH
		3600            ;RETRY
		604800          ;EXPIRE
		38400           ;MINIMUM

	)

	IN      NS      10		ns1.contocontigo.pt.
	IN      NS      10		ns2.contocontigo.pt.
	IN		MX		10		smtp.contocontigo.pt.	# Adicionar esta linha


ns1     IN      A       50.40.30.20 # DNS Primário
ns2     IN      A       50.40.30.30 # DNS Secundário
smtp	IN		A		50.40.30.10 # Máquina onde será configurada os emails => adicionar esta linha

;RECORDS
@       IN      A       50.40.30.10 # IP Máquina HTTP
www     IN      CNAME   contocontigo.pt.
drive   IN      A       50.40.30.10
interno IN      A       50.40.30.10
```

> **Nota:** não esquecer de atualizar a versão

Após as alterações é necessário reiniciar o **`named.service`**: `systemctl reload named.service`

<br />

##### Testar

Para testar é possível utilizar o comando `nslokup`:

```bash
nslookup # dar enter
	> set type=mx
	> (nome da zona)
```

Para sair pressionar `CTRL + C`

<br />

##### Configurações Postfix <small>(para SMTP)</small>

Abrir o ficheiro `/etc/postfix/main.cf` (`nano /etc/postfix/main.cf`) e realizar as seguintes alterações:
1. Remover o comentário na linha `#myhostname = host.domain.tld` e alterar o valor de forma a ficar: `myhostname = smtp.contocontigo.pt`;
2. Remover o comentário na linha `#mydomain = doamin.tld` e alterar o valor de forma a ficar: `mydomain = contocontigo.pt`;
3. Remover o comentário na linha `#myorigin = $mydomain`;
4. Remover o comentário na linha `inet_interfaces = all`;
5. Comentar a linha `mydestination = $myhostname, localhost.$mydomain, localhost` e remover o comentário na linha `mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain`;
6. Guardar o ficheiro e sair do mesmo (no **nano** usar `CTRL + S` e `CTRL + X`).

Por fim reiniciar o **postfix** através do comando `systemctl restart postfix`.

<br />

##### Configurar Dovecot <small>(para IMAP)</small>

Aceder ao ficheiro `/etc/dovecot/dovecot.conf` (`nano /etc/dovecot/dovecot.conf`) e remover o comentário na seguinte linha: `protocols = imap pop3 lmtp submission` e guardar o ficheiro (no **nano** usar `CTRL + S` e `CTRL + X`).

Não esquecer reiniciar o **Dovecot** utilizando para tal: `systemctl restart dovecot`.

> **Nota:** caso exista outra linha que inicie por **`protocols`** comentar de forma a ficar apenas a linha referida acima.

<br />

##### Encaminhamento de Emails

Caso seja pedido para quando um utilizador receber um email, esse mesmo email seja encaminhado para outro é necessário criar o ficheiro `.forward` na pasta desse utilizador (ex.: `home/joao.sousa/.forward`).

Neste ficheiro deve ser adicionado o email para quem vai ser encaminhado, exemplo:

```bash
ana.santos@contocontigo.pt
```

Posto isto é necessário conceder as seguintes permissões `600`, usando para tal o comando: `chmod 600 .forward`

É possível testar de igual forma e verificar se o email é reencaminhado com sucesso através do comando `mail`.

<br />

##### Grupo de Emails

Em primeiro ligar é necessário editar o ficheiro `/etc/aliases`, seguindo os passos abaixo:
1. Editar o ficheiro com o **nano**: `nano /etc/aliases`
2. No final do ficheiro encontrar a linha **`support: postmaster`**
3. Trocar o conteúdo desta linha por:
   + `support: postmaster <nome-do-grupo> <email1>,<email2>,<email3>`
   + **Gravar o ficheiro:** <small>(com o **nano**)</small> `CTRL + S` e `CTRL + X`
4. Executar o comando: `postalias /etc/aliases`

Após ter executado estes passos é necessário mudar para o email principal (ex.: `joao.sousa@contocontigo.pt`):
1. **`su - joao.sousa`:** para mudar para o utilizador <small>(do email principal)</small>
2. Abrir o ficheiro **`.forward`** (`nano .forward`) e adicionar a seguinte linha:
   + `<nome-do-grupo>.<nome-da-zona>`
   + **Exemplo:** `grupocc.contocontigo.pt`
3. Guardar o ficheiro (com **nano** `CTRL + S` e `CTRL + X`) e conceder as seguintes permissões:
   + `chmod 640 .forward`

Desta forma, sempre que um email seja enviado para o email de exemplo (`joao.sousa@contocontigo.pt`), este será posteriormente reencaminhado para todos os emails do grupo declarado.

<br />

##### Criar utilizadores

Para adicionar um utilizador basta usar o comando `adduser` seguido do nome do mesmo. Neste caso seria:
* `adduser joao.sousa` para ficar `joao.sousa@contocontigo.pt`;
* `adduser ana.santos` para ficar `ana.santos@contocontigo.pt`.

<br />

###### Realizar testes de envio de emails <small>(Ex. para SMTP)</small>

Para testar o envio de emails é necessário instalar o **`mailx`**, para isso é necessário executar o comando: `yum install mailx` e posteriormente seguir os passos que se seguem:

1. **`su - <nome-de-utilizador>`:** para mudar de utilizador;
2. Enviar o email para outro utilizador:
	```bash
	mail -s "subject" <email-do-utilizador> # Enter
	Aqui vai o corpo do email
	.
	```

	Escrever `exit` ou `logout` para sair.

3. **`su - <nome-do-utilizador-que-recebeu-o-email>`:** para mudar para o utilizador que recebeu o email;
4. **`mail`:** para listar os emails mais recentes;
	+ Para sair escrever `exit` ou `logout`.