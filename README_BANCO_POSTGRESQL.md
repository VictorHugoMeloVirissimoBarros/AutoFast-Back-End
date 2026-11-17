# Banco de dados do AutoFast (PostgreSQL)

Este projeto usa as entidades JPA em `src/main/java/br/gov/sp/fatec/autofast/model`.
O Hibernate esta configurado com `spring.jpa.hibernate.ddl-auto=update` e cria/atualiza
as tabelas a partir das anotacoes `@Entity`, `@Id`, `@Column`, `@ManyToOne`, `@OneToMany`,
`@OneToOne`, `@JoinColumn` e `@Inheritance` quando a aplicacao inicia.

## Requisitos

- PostgreSQL instalado e em execucao.
- Java 21.
- A senha do usuario PostgreSQL `postgres` definida durante a instalacao.

## 1. Criar o banco

No pgAdmin, conecte ao servidor PostgreSQL e abra o Query Tool no banco administrativo
`postgres`. Execute o conteudo de `database/00_criar_banco.sql` uma unica vez.

Alternativa no PowerShell (se o `psql` estiver no PATH):

```powershell
psql -U postgres -d postgres -f .\database\00_criar_banco.sql
```

Se o banco `autofast_db` ja existir, pule esta etapa.

## 2. Criar a estrutura das tabelas (opcional)

Conecte o Query Tool ao banco `autofast_db` e execute `database/01_criar_tabelas.sql`.

Tambem e possivel deixar o Hibernate criar as tabelas automaticamente na primeira
inicializacao. O script SQL e fornecido para documentacao, verificacao e criacao manual.
Em um banco novo, use uma das duas opcoes; nao e necessario executar ambas.

## 3. Informar a senha e iniciar o back-end (Windows PowerShell)

Abra o terminal na pasta `autofast` (a mesma pasta deste README) e execute:

```powershell
$env:DB_PASSWORD="SUA_SENHA_DO_POSTGRES"
$env:DB_USERNAME="postgres"
$env:DB_URL="jdbc:postgresql://localhost:5432/autofast_db"
.\mvnw.cmd spring-boot:run
```

Substitua `SUA_SENHA_DO_POSTGRES` pela senha que voce definiu. Nao compartilhe nem
commite sua senha. As variaveis acima valem apenas para a janela atual do PowerShell.

## 4. Conferir as tabelas

No pgAdmin, atualize `Databases > autofast_db > Schemas > public > Tables`.
A estrutura mapeada pelas entidades atuais e:

- `usuario` (classe base abstrata `Usuario`)
- `cliente` (herda de `usuario`, chave `usuario_id`)
- `mecanico` (herda de `usuario`, chave `usuario_id`)
- `veiculo`
- `peca`
- `orcamento`
- `mensagem`

## 5. Testar a API

Com o Spring Boot em execucao, teste por exemplo:

- `http://localhost:8080/api/clientes`
- `http://localhost:8080/api/mecanicos`
- `http://localhost:8080/api/veiculos`
- `http://localhost:8080/api/pecas`
- `http://localhost:8080/api/orcamentos`
- `http://localhost:8080/api/mensagens`

Uma lista vazia (`[]`) e uma resposta valida quando ainda nao existem registros.

## Observacoes importantes

- `ddl-auto=update` cria/atualiza tabelas, mas nao cria o proprio banco `autofast_db`.
- O script SQL representa as entidades atuais; se as entidades mudarem, revise o script.
- O banco nao cria usuarios de teste automaticamente.
- A entidade `Usuario` usa heranca `JOINED`; por isso cliente e mecanico tambem possuem
  uma linha correspondente na tabela `usuario`.
- Antes de publicar, configure credenciais como variaveis de ambiente no servidor.
- `ddl-auto=update` e conveniente para desenvolvimento e demonstracao; para producao,
  prefira migracoes versionadas (por exemplo, Flyway) e backups.
