#### Crontab

> **PERGUNTA EXAME:** Faça com que o programa `backup-db.sh` do utilizado **admin** na **EX1** seja executado na crontab diariamente entre as 23:15 e as 00:45 com frequência de 15 minutos.
> **Intervalo:** 23:30 | 23:45 | 00:00 | 00:15 | 00:30 | 00:45

##### Estrutura

| **Min.** | **Horas** | **Dias-mês** | **Meses** | **Dias-semana** | **Comando**    |
| -------- | --------- | ------------ | --------- | --------------- | -------------- |
| 15-59/15 | 23        | \*           | \*        | \*              | `backup-db.sh` |
| 0-45/15  | 0         | 0            | \*        | \*              | `backup-db.sh` |

No exemplo a cima, a **crontab** iria executar aos 45 minutos da hora 23 e 00 (meia-noite), bem como a cada 15 minuts da hora 0, sendo esta executada diariamente.

##### Executar

Para executar a **crontab** é necessário utilizador o comando `crontab -e`, tal como é apresentado no exemplo que se segue:

```bash
crontab -e # Enter
15-59/15 23 * * * backup-db.sh
0-45/15 0 * * * backup-db.sh
```

Pressionando para sair a tecla `ESC` e digitando de seguida `:wq!`.

###### _Cheat Sheet_ VIM <small>(mais usados)</small>

-   **`:wq` ou `:x` ou `ZZ`:** guardar e fechar
-   **`:q!`:** fechar sem gravar
-   **`:wqa`:** gravar e fechar todas as abas
-   **`S`:** para inserir texto
-   **`ESC`:** para sair do modo de insereção ou outro

##### Verificar a **Crontab**

Para verificar o conteúdo da **cronta** é possível utilizar o comando `crontab -l`
