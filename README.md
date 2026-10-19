# DIO - Trilha .NET - Nuvem com Microsoft Azure
www.dio.me

## Desafio de projeto
Para este desafio, você precisará usar seus conhecimentos adquiridos no módulo de Nuvem com Microsoft Azure, da trilha .NET da DIO.

## Contexto
Você precisa construir um sistema de RH, onde para essa versão inicial do sistema o usuário poderá cadastrar os funcionários de uma empresa. 

Essa cadastro precisa precisa ter um CRUD, ou seja, deverá permitir obter os registros, criar, salvar e deletar esses registros. A sua aplicação também precisa armazenar logs de toda e qualquer alteração que venha a ocorrer com um funcionário.

## Premissas
A sua aplicação deverá ser do tipo Web API, Azure Functions ou MVC, fique a vontade para implementar a solução que achar mais adequado.

A sua aplicação deverá ser implantada no Microsoft Azure, utilizando o App Service para a API, SQL Database para o banco relacional e Azure Table para armazenar os logs.

A sua aplicação deverá armazenar os logs de todas as alterações que venha a acontecer com o funcionário. Os logs deverão serem armazenados em uma Azure Table.

A sua classe principal, a classe Funcionario e a FuncionarioLog, deve ser a seguinte:

![Diagrama da classe Funcionario](Imagens/diagrama_classe.png)

A classe FuncionarioLog é filha da classe Funcionario, pois o log terá as mesmas informações da Funcionario.

Não se esqueça de gerar a sua migration para atualização no banco de dados.

## Métodos esperados
É esperado que você crie o seus métodos conforme a seguir:


**Swagger**


![Métodos Swagger](Imagens/swagger.png)


**Endpoints**


| Verbo  | Endpoint                | Parâmetro | Body               |
|--------|-------------------------|-----------|--------------------|
| GET    | /Funcionario/{id}       | id        | N/A                |
| PUT    | /Funcionario/{id}       | id        | Schema Funcionario |
| DELETE | /Funcionario/{id}       | id        | N/A                |
| POST   | /Funcionario            | N/A       | Schema Funcionario |

Esse é o schema (model) de Funcionario, utilizado para passar para os métodos que exigirem:

```json
{
  "nome": "Nome funcionario",
  "endereco": "Rua 1234",
  "ramal": "1234",
  "emailProfissional": "email@email.com",
  "departamento": "TI",
  "salario": 1000,
  "dataAdmissao": "2022-06-23T02:58:36.345Z"
}
```

## Ambiente
Este é um diagrama do ambiente que deverá ser montado no Microsoft Azure, utilizando o App Service para a API, SQL Database para o banco relacional e Azure Table para armazenar os logs.

![Diagrama da classe Funcionario](Imagens/diagrama_api.png)


## Solução
O projeto foi implementado conforme as regras descritas no desafio:
- **`FuncionarioController.cs`**:
  - `Criar`: persiste o funcionário no banco relacional SQL (`SaveChanges`) e gera o registro de log na Azure Table Storage (`UpsertEntity`) com `TipoAcao.Inclusao`.
  - `Atualizar`: atualiza todas as propriedades do funcionário (`Nome`, `Endereco`, `Ramal`, `EmailProfissional`, `Departamento`, `Salario`, `DataAdmissao`), persiste as mudanças no banco SQL (`Update` e `SaveChanges`) e gera o registro de log na Azure Table Storage (`UpsertEntity`) com `TipoAcao.Atualizacao`.
  - `Deletar`: remove o funcionário do banco relacional SQL (`Remove` e `SaveChanges`) e gera o registro de log na Azure Table Storage (`UpsertEntity`) com `TipoAcao.Remocao`.

---

## Configuração de Credenciais da Azure (Repositório Público)

> [!IMPORTANT]
> **Atenção:** Por questões de segurança e boas práticas de desenvolvimento (DevOps / DevSecOps), **nenhuma chave de acesso, senha ou connection string real foi versionada neste repositório**.

Para executar o projeto, você deve configurar suas próprias credenciais no arquivo `appsettings.json` (ou `appsettings.Development.json`):

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "ConexaoPadrao": "Server=tcp:<SEU_SERVIDOR_AZURE_SQL>.database.windows.net,1433;Initial Catalog=<SEU_BANCO_DE_DADOS>;Persist Security Info=False;User ID=<SEU_USUARIO>;Password=<SUA_SENHA>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    "SAConnectionString": "DefaultEndpointsProtocol=https;AccountName=<SUA_STORAGE_ACCOUNT>;AccountKey=<SUA_CHAVE_STORAGE_ACCOUNT>;EndpointSuffix=core.windows.net",
    "AzureTableName": "FuncionarioLog"
  }
}
```

### Onde obter essas informações no Portal da Azure:
1. **`ConexaoPadrao` (Azure SQL Database)**:
   - Acesse o recurso do seu **SQL Database** no portal do Azure.
   - No menu lateral esquerdo, vá em **Connection strings** (Cadeias de conexão).
   - Copie a string do tipo **ADO.NET** e substitua `{your_user}` e `{your_password}` com o usuário e senha definidos na criação do banco.
   - Certifique-se de liberar o IP de sua máquina (ou habilitar "Allow Azure services and resources to access this server") no firewall do Azure SQL Server.

2. **`SAConnectionString` (Azure Storage Account)**:
   - Acesse sua **Storage Account** no portal do Azure.
   - No menu lateral esquerdo, selecione **Security + networking** > **Access keys** (Chaves de acesso).
   - Clique em **Show** ao lado da Connection String de uma das chaves (`key1` ou `key2`) e copie o valor.

3. **`AzureTableName`**:
   - Nome da tabela no Azure Table Storage onde os logs serão gravados (o padrão configurado é `FuncionarioLog`). Se a tabela não existir, ela será criada automaticamente pela aplicação.

---

## Como Executar o Projeto Localmente

1. **Clonar o repositório:**
   ```bash
   git clone https://github.com/SEU_USUARIO/trilha-net-azure-desafio.git
   cd trilha-net-azure-desafio
   ```

2. **Restaurar dependências e compilar:**
   ```bash
   dotnet restore
   dotnet build
   ```

3. **Aplicar as Migrations no Banco de Dados SQL:**
   ```bash
   dotnet ef database update
   ```
   *(Caso não tenha a CLI do Entity Framework instalada, instale com: `dotnet tool install --global dotnet-ef`)*

4. **Executar a API:**
   ```bash
   dotnet run
   ```

5. **Acessar a Documentação Swagger:**
   - Navegue até `https://localhost:<porta>/swagger` para testar os endpoints da API interativamente.

---

## Publicação no Microsoft Azure

1. **Azure App Service**:
   - Crie um recurso de **App Service** (Web App) com runtime `.NET 6`.
   - Nas **Configurações** (**Configuration**) do App Service, adicione as connection strings ou variáveis de ambiente para `ConexaoPadrao` e `SAConnectionString`.
2. **Deploy**:
   - Realize a publicação via Visual Studio, VS Code (Azure App Service extension), GitHub Actions ou Azure CLI (`az webapp up`).