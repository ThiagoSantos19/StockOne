<div align="center">
  <img src="https://github.com/user-attachments/assets/a93eca32-7650-46dc-8900-1204e0288e95" width="100px"/>
</div>

# StockOne - Sistema de Automação Inteligente para Restaurantes

![.NET](https://img.shields.io/badge/.NET-8.0-blue)
![ASP.NET Core](https://img.shields.io/badge/ASP.NET%20Core-MVC-green)
![Entity Framework](https://img.shields.io/badge/Entity%20Framework-Core-orange)
![Bootstrap](https://img.shields.io/badge/Bootstrap-5-purple)

## Visão Geral

**StockOne** é um sistema SaaS (Software as a Service) de automação inteligente desenvolvido para resolver o caos operacional em restaurantes causado pela falta de sincronia entre vendas de aplicativos de delivery (como o iFood) e a gestão de estoque manual.

### Problema Resolvido

Restaurantes frequentemente vendem produtos online cujos ingredientes acabaram no estoque, gerando cancelamentos, prejuízo e insatisfação do cliente.

### Solução

A cada venda, o sistema deduz automaticamente os insumos do inventário com base em fichas técnicas pré-cadastradas. Se um ingrediente-chave acabar, o sistema pausa automaticamente a venda do prato correspondente no iFood.

## Público-Alvo

- **Gestores/Proprietários**: Usam o sistema para configurar, analisar dados e tomar decisões de compra.
- **Equipe da Cozinha**: Usa uma interface para visualizar e gerenciar a fila de produção de pedidos.

## Arquitetura e Stack Tecnológica

### Backend
- **Framework**: ASP.NET Core MVC com C# (.NET 8.0)
- **Arquitetura**: Monolítica com separação clara de responsabilidades (Controllers, Services, Repositories)
- **Padrão de Projeto**: Repository Pattern para abstração de acesso a dados

### Banco de Dados
- **SGBD**: SQL Server
- **ORM**: Entity Framework Core (Code-First)

### Frontend
- **Engine**: Razor Views (renderização no servidor)
- **Framework CSS**: Bootstrap 5
- **Ícones**: Bootstrap Icons

### Autenticação e Autorização
- **Sistema**: ASP.NET Core Identity
- **Roles**: "Gestor" e "Cozinha"

### Hospedagem
- **Plataforma**: Microsoft Azure
- **Serviços**: Azure App Service e Azure SQL Database

### CI/CD
- **Ferramenta**: GitHub Actions

## Estrutura do Projeto

```
StockOne.Web/
├── Controllers/          # Controladores MVC
│   ├── HomeController.cs
│   ├── InsumosController.cs
│   ├── ProdutosController.cs
│   ├── PedidosController.cs
│   └── CozinhaController.cs
├── Data/                 # Contexto do Entity Framework
│   └── ApplicationDbContext.cs
├── Models/               # Modelos de domínio
│   ├── Enums/
│   │   └── UnidadeMedida.cs
│   ├── ApplicationUser.cs
│   ├── Ingredient.cs
│   ├── Product.cs
│   ├── ItemFichaTecnica.cs
│   └── Pedido.cs
├── Repositories/         # Padrão Repository
│   ├── IGenericRepository.cs
│   └── GenericRepository.cs
├── Services/             # Lógica de negócio
│   ├── IEstoqueService.cs
│   ├── EstoqueService.cs
│   ├── ICardapioService.cs
│   └── CardapioService.cs
├── Views/                # Views Razor
│   ├── Home/
│   ├── Insumos/
│   ├── Produtos/
│   ├── Pedidos/
│   ├── Cozinha/
│   └── Shared/
├── wwwroot/              # Arquivos estáticos
├── .github/
│   └── workflows/
│       └── deploy.yml    # Workflow do GitHub Actions
├── appsettings.json
├── Program.cs
└── README.md
```

## Configuração e Instalação

### Pré-requisitos

- [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) ou [SQL Server LocalDB](https://learn.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb)
- [Visual Studio 2022](https://visualstudio.microsoft.com/) ou [Visual Studio Code](https://code.visualstudio.com/)

### Passos de Instalação

1. **Clone o repositório**

```bash
git clone https://github.com/seu-usuario/StockOne.git
cd StockOne/StockOne.Web
```

2. **Configure a Connection String**

Edite o arquivo `appsettings.json` e ajuste a connection string para o seu ambiente:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=StockOneDb;Trusted_Connection=True;MultipleActiveResultSets=true"
  }
}
```

Para Azure SQL Database:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=tcp:seu-servidor.database.windows.net,1433;Initial Catalog=StockOneDb;Persist Security Info=False;User ID=seu-usuario;Password=sua-senha;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
  }
}
```

3. **Restaure os pacotes NuGet**

```bash
dotnet restore
```

4. **Execute as Migrations**

```bash
dotnet ef migrations add InitialCreate
dotnet ef database update
```

5. **Execute o projeto**

```bash
dotnet run
```

O aplicativo estará disponível em `https://localhost:5001` ou `http://localhost:5000`.

## Usuário Padrão

O sistema cria automaticamente um usuário Gestor para facilitar os testes:

- **Email**: gestor@stockone.com
- **Senha**: Gestor@123
- **Role**: Gestor

## Funcionalidades Principais

### Módulo 1: Autenticação e Gestão de Acesso
- Login e registro de usuários
- Sistema de Roles: "Gestor" e "Cozinha"
- Proteção de rotas baseada em roles

### Módulo 2: Gestão de Estoque (CRUD para Gestores)
- **Insumos**: Cadastro, edição, visualização e exclusão de insumos
- **Produtos**: Cadastro, edição, visualização e exclusão de produtos
- **Ficha Técnica**: Associação de insumos aos produtos com quantidades específicas

### Módulo 3: Lógica de Negócio Principal
- **EstoqueService**:
  - `DeduzirInsumosPorVenda`: Deduz insumos do estoque com base na venda
  - `VerificarDisponibilidadeProduto`: Verifica se há estoque suficiente para produzir um produto

### Módulo 4: Interfaces de Usuário
- **Dashboard do Gestor**: Exibe alertas de estoque baixo e links rápidos para módulos
- **Fila de Produção**: Interface para a equipe da cozinha visualizar e marcar pedidos como prontos
- **Auto-refresh**: A fila de produção atualiza automaticamente a cada 10 segundos

## Fluxo de Trabalho

1. **Gestor cadastra insumos** no sistema (ex: Tomate, Queijo, Massa)
2. **Gestor cadastra produtos** (ex: Pizza Margherita)
3. **Gestor cria a ficha técnica** do produto, associando insumos e quantidades
4. **Sistema recebe pedido do iFood** (simulado)
5. **Sistema deduz insumos** do estoque automaticamente
6. **Sistema verifica disponibilidade** de todos os produtos
7. **Sistema pausa produtos** sem estoque no iFood
8. **Equipe da cozinha** visualiza o pedido na fila de produção
9. **Equipe marca o pedido como pronto** quando concluído

## 🚀 Deploy no Azure

### Configuração do Azure

1. **Crie um App Service** no Azure Portal
2. **Crie um Azure SQL Database**
3. **Configure a Connection String** no App Service (Configuration > Connection strings)
4. **Obtenha o Publish Profile** do App Service (Download publish profile)

### Configuração do GitHub Actions

1. **Adicione o Publish Profile como Secret** no GitHub:
   - Vá em Settings > Secrets and variables > Actions
   - Crie um novo secret chamado `AZURE_WEBAPP_PUBLISH_PROFILE`
   - Cole o conteúdo do arquivo de publish profile

2. **Atualize o workflow** (`.github/workflows/deploy.yml`):
   - Substitua `AZURE_WEBAPP_NAME` pelo nome do seu App Service

3. **Faça push para a branch main**:

```bash
git add .
git commit -m "Initial commit"
git push origin main
```

O deploy será executado automaticamente via GitHub Actions.

## Estrutura do Banco de Dados

### Tabelas Principais

- **AspNetUsers**: Usuários do sistema (Identity)
- **AspNetRoles**: Roles do sistema (Identity)
- **Ingredients**: Insumos do estoque
- **Products**: Produtos do cardápio
- **ItemFichasTecnicas**: Relação entre produtos e insumos (ficha técnica)
- **Pedidos**: Fila de pedidos recebidos

### Relacionamentos

- `Product` 1:N `ItemFichaTecnica` N:1 `Ingredient`
- `Product` 1:N `Pedido`

## Tecnologias Utilizadas

- **ASP.NET Core 8.0 MVC**
- **Entity Framework Core 8.0**
- **ASP.NET Core Identity**
- **SQL Server**
- **Bootstrap 5**
- **Bootstrap Icons**
- **Razor Views**
- **GitHub Actions**
- **Azure App Service**
- **Azure SQL Database**

## Próximos Passos (Roadmap)

- [ ] Integração com a API do iFood
- [ ] Dashboard com gráficos e relatórios
- [ ] Notificações em tempo real (SignalR)
- [ ] Histórico de pedidos e vendas
- [ ] Relatórios de compra sugeridos
- [ ] Aplicativo mobile para a equipe da cozinha
- [ ] Multi-tenancy (suporte a múltiplos restaurantes)

## Contribuindo

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues e pull requests.

## Contato

Para dúvidas ou sugestões, entre em contato através de (email: stockOne@gmail.com).

---

**StockOne** - Automatizando a gestão de estoque para restaurantes modernos.


## Apêndice Glossário de Termos

Este glossário define os principais termos, tecnologias e conceitos utilizados no desenvolvimento do StockOne.

## Tecnologias e Frameworks

- ASP.NET Core MVC: É o framework principal utilizado para construir a aplicação web. Ele organiza o projeto seguindo o padrão arquitetural MVC (Model-View-Controller), que separa a lógica de negócio (Model), a interface do usuário (View) e o controle do fluxo da aplicação (Controller).

- Entity Framework Core (EF Core): É o ORM (Object-Relational Mapper) utilizado no projeto. Ele faz a "ponte" entre o código C# (classes como Produto, Insumo) e o banco de dados relacional (tabelas do SQL Server), permitindo manipular o banco de dados usando objetos, sem a necessidade de escrever SQL manualmente.

- ASP.NET Core Identity: É o framework de associação da Microsoft, responsável por toda a parte de autenticação (login, registro) e autorização (níveis de acesso, como "Gestor" e "Cozinha"). Ele gerencia usuários, senhas e roles de forma segura.

- SQL Server: É o SGBD (Sistema de Gerenciamento de Banco de Dados) utilizado para armazenar todos os dados da aplicação em um ambiente de produção.

- SQL Server LocalDB: Uma versão mais leve do SQL Server, ideal para o ambiente de desenvolvimento. Ela roda sob demanda na máquina do desenvolvedor, sem a necessidade de instalar e gerenciar um serviço completo de banco de dados.

- Razor Views: É a "engine" de templates do ASP.NET Core que permite escrever código HTML e C# no mesmo arquivo (.cshtml) para gerar as páginas web dinamicamente no servidor.

- Bootstrap: Um framework de CSS popular que fornece componentes de interface (botões, formulários, menus) prontos para uso, garantindo que o sistema tenha um design responsivo e moderno com menos esforço.

## Conceitos e Padrões

- SaaS (Software as a Service): Um modelo de licenciamento e entrega de software no qual o software é hospedado centralmente (na nuvem) e licenciado em regime de assinatura. O StockOne é um SaaS para restaurantes.

- ORM (Object-Relational Mapping): Uma técnica que converte dados entre o sistema de tipos de uma linguagem orientada a objetos (como C#) e as tabelas de um banco de dados relacional. O Entity Framework Core é a implementação de ORM deste projeto.

- Repository Pattern: Um padrão de projeto que cria uma camada de abstração entre a lógica de negócio e a camada de acesso a dados. Ele centraliza o código de acesso ao banco de dados em classes específicas ("repositórios"), tornando o sistema mais organizado, testável e fácil de manter.

- Code-First: Uma abordagem de desenvolvimento do Entity Framework Core onde as classes do C# (os Models) são a "fonte da verdade". Com base nelas, o EF Core gera e atualiza automaticamente o esquema do banco de dados através de migrations.

- CI/CD (Continuous Integration/Continuous Deployment): Um conjunto de práticas de automação para o ciclo de desenvolvimento. CI (Integração Contínua) consiste em mesclar automaticamente as alterações de código de vários desenvolvedores em um repositório central. CD (Implantação Contínua) consiste em implantar automaticamente a aplicação em produção após a aprovação dos testes.

- Ficha Técnica: No contexto do StockOne, é o coração do sistema. Refere-se à lista de todos os insumos e suas respectivas quantidades necessárias para preparar um único produto do cardápio.

## Ferramentas e Plataformas

- Microsoft Azure: A plataforma de computação em nuvem da Microsoft onde a aplicação é hospedada.

- Azure App Service: O serviço do Azure utilizado para hospedar a aplicação web StockOne, gerenciando toda a infraestrutura de servidor.

- Azure SQL Database: A versão em nuvem do SQL Server, oferecida como um serviço gerenciado pelo Azure.

- GitHub Actions: A ferramenta de automação de CI/CD integrada ao GitHub, utilizada para construir e implantar automaticamente o StockOne no Azure a cada alteração na branch main.
