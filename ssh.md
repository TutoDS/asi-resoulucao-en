#### SSH <small>(Secure Shell)</small>

> **PERGUNTA COMUM:** faça com que o utilizador `root` de cada uma das máquinas aceda às outras sem que seja necessário digitar a password.

##### Gerar Chave SSH

Para gerar uma chave **SSH** basta digitar o comando `ssh-keygen -t rsa` e caso não pretenda alterar nenhum valor basta pressionar `enter` 3 vezes.

Para confirmar que a chave foi gerada com sucesso:
- **`ls ~/.ssh`:** terá que conter os seguintes ficheiros: `id_rsa` e `id_rsa.pub`;
- **`cat ~/.ssh/.id_rsa.pub`:** para visualizar a chave pública gerada
- **`cat ~/.ssh/.id_rsa`:** para visualizar a chave privada gerada

<br />

###### Alternativa <small>(usada para GitHub, GitLab, ...)</small>

Para gerar uma chave **SSH** para utilizador nos repositórios do **GitHub**, **GitLab**, etc, é necessário adicionar o email, alterando o comando anterior para `ssh-keygen -t rsa -b 4096 -C "<email>"`, mantendo-se as restantes informações iguais.

<br />

##### Copiar Chave SSH <small>(para outra máquina ou utilizador)</small>

Para copiar a chave **SSH** gerada basta executar o comando `ssh-copy-id -i ~/.ssh/id_rsa.pub <utilizador>@<ip-ou-dominio>`

> **Nota:** caso seja pretendido copiar a chave **SSH** para duas máquinas é necessário executar o comando 2 vezes alterado o utilizador e o ip/domínio.

##### Copiar Ficheiros por SSH

Para copiar ficheiros entre máquinas ou utilizadores pode ser utilizador o comando `scp` seguido do ficheiro, bem como os dados de **SSH** da máquina ou utilizador. Por exemplo:

**- Para outro utilizador:**

```bash
scp ~/data.txt <user>@<ip-ou-dominio>:~<pasta-user>/<pasta-de-destino>
```

**- Para outra máquina:**

```bash
scp ~/data.txt <user>@<ip-ou-dominio>
```

**- De outra máquina:**

```bash
scp <user>@<ip-ou-dominio>:<caminho-para-o-ficheiro>/data-txt <caminho-de-destino>
```

> **Nota:** no **`<caminho-de-destino>`** é possível utilizar um **`.`**, significando para a localização atual