#### LVM <small>(Logical Volume Manager)</small>

> **PERGUNTA:** Para alojar a BD da empresa, na EX1 crie um VolumeGroup com o nome `vgdb01` contento os *logical volumes* e *mountpoints* de acordo com a tabela abaixo. Garanta que os *mountpoints* são estabelecidos no arranque do sistema.
> 1. Aumentar o espaço de `/db/log` em 100MB
> 2. Aumentar o espaço de `/db` em 100MB podendo reduzir ao `/db/data` se necessário

> **OBSERVAÇÕES:** já existem 2 discos de 300MB <small>aproximadamente</small> cada (`/dev/sdb` e `/dev/sdc`)

| **Logical Volume** | **MountPoint** | **Tamanho** |
| --- | --- | --- |
| `lv_dbbin` | `/db` | 150 MB |
| `lv_dblog` | `/db/log` | 100 MB |
| `lv_dbdata1` | `/db/data` | 2000MB |

##### Criar *VolumeGroup*

Para criar um ***VolumeGroup*** é necessário em primeiro lugar listar todos os discos, de forma a identificar que discos irão pertencer a este ***VolumeGroup***

`vgcreate <nome-do-grupo> <disco1> <disco2> <disco3>`

##### Criar *Logical Volume*

Por sua vez, para criar um *Logical Volume* é necessário executar o seguinte commando:

`Lvcreate -n <nome> -L <tamanho> <volume-group>`

Sendo que o `<volume-group>` será o *VolumeGroup* criado acima.

##### Formatar *FileSystems*

Para formatar os *FileSystems* pode ser utilizado o comando:

```bash
Mktfs.ext4 -b 4096 /dev/<volume-group>/<logical-volume>
```

> **Nota:** isto para formatar para o formato **EXT4**


##### Criar Diretórios

Para criar diretórios basta usar o comando `mkdir`, tal como é usado para criar uma pasta normal:

```bash
mkdir <caminho>/<pasta>
```

##### Estabelecer no Arranque do Sistema

Para estabelecer no arranque do sistema é preciso editar o ficheiro `/etc/fstab` (`nano /etc/fstab`) e realizar as seguintes alterações:

1. Adicionar o código que se segue depois da linha `/dev/mapper/cl-swap     none                    swap    defaults        0 0`:

	```bash
	/dev/<volume-group>/<logical-volume>	/path/folder	ext4	defaults	1 1
	```
2. Guardar o ficheiro (no **nano** usar `CTRL + S` e `CTRL + X`) e usar o comando `mount -a` para testar;
   * Caso não surja nenhum erro as entradas foram registadas corretamente.
3. Caso pretenda visualizar as alterações realizadas utilizar o commando `df -k`.

##### Aumentar Espaço

* **Listar *VolumeGroups*:** `vgs`
* **Listar *LogicalVolumes*:** `lvs`

Para aumentar o espaço de um *LogicalVolume* é necessário seguir os passos abaixo:

1. Aumentar o tamanho do *VolumeGroup* (com o espaço existente livre) utilizando o comando `vgextend <volume-group> <disco>`;
2. Para visualizar os *LogicalVolumes* executar o comando `df -k`;
3. Para aumentar o tamanho de um *LogicalVolume* é necessário usar os comandos:
   + `lvextend -L +<tamanho>M /dev/<volume-group>/<dir>`
   + `resize2fs /dev/<volume-group>/<dir>`
4. Para visualizar as alterações executar novamente o comando `df -k`.

<br />

> Nota: falta redução de espaço