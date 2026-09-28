# Arquitetura e Modelo de Dados — Clique Saúde

## 1. Visão geral (diagrama de arquitetura)

```mermaid
flowchart LR
    subgraph Cliente
        Browser["Navegador"]
    end

    subgraph Front-end["Front-end — React SPA (Vite)"]
        SPA["React Router + Context API\n(AuthContext, telas, componentes)"]
        Mock["json-server\n(mock da API — db.json)"]
    end

    subgraph Back-end["Back-end — clique-saude-api"]
        Express["Express 5\nroutes → controllers → services → models"]
        Swagger["/api-docs\n(Swagger UI)"]
        JWT["Middlewares\n(autenticação JWT, admin, validação Zod)"]
    end

    subgraph Dados
        Prisma["Prisma ORM"]
        DB[("SQLite (dev)\nPostgreSQL (produção)")]
    end

    Browser --> SPA
    SPA -. "hoje (mock)" .-> Mock
    SPA -. "próximo passo: apontar\nBASE_URL para a API real" .-> Express
    Express --> JWT
    Express --> Swagger
    Express --> Prisma
    Prisma --> DB
```

**Por que duas "APIs" no diagrama?** O front-end (Atividades 1 e 3) foi
construído e testado contra um mock (`json-server`) que replica fielmente o
schema do back-end real. O back-end real (Atividades 2, 3 e 4) já está
pronto, testado (57 testes, ~96% de cobertura) e documentado via Swagger —
o único passo restante para integração total é trocar o `BASE_URL` do
serviço de API do front-end (`src/services/clinicaApi.js`) para apontar
para `clique-saude-api` em vez do mock. Essa decisão está documentada desde
o relatório da Atividade 1 (fase final do roadmap).

## 2. Modelo de dados (diagrama entidade-relacionamento)

```mermaid
erDiagram
    USUARIO ||--o{ CONSULTA : agenda
    MEDICO ||--o{ CONSULTA : atende
    MEDICO ||--o{ HORARIO_DISPONIVEL : possui

    USUARIO {
        int id PK
        string nome
        string email UK
        string senha "hash bcrypt"
        string tipo "paciente | admin"
    }

    MEDICO {
        int id PK
        string nome
        string crm UK
        string especialidade
        float valorConsulta
    }

    HORARIO_DISPONIVEL {
        int id PK
        int medicoId FK
        string diaSemana
        string horario
    }

    CONSULTA {
        int id PK
        int pacienteId FK
        int medicoId FK
        string data
        string hora
        string status "Agendado | Realizado | Cancelado"
        string observacoes
    }
```

Este é o schema real do Prisma
([`prisma/schema.prisma`](https://github.com/Weslley0129/atividade-pratica-de-extenss-o-2/blob/main/prisma/schema.prisma))
— o mesmo desde a Atividade 2, sem alterações estruturais.

## 3. Fluxo de agendamento de consulta

```mermaid
sequenceDiagram
    actor P as Paciente
    participant F as Front-end (SPA)
    participant A as API (auth + consultas)
    participant D as Banco de dados

    P->>F: Escolhe especialidade, médico, data e horário
    F->>A: GET /medicos/{id}/horarios
    A->>D: Busca agenda fixa do médico
    D-->>A: Dias/horários fixos
    A-->>F: Lista de horários fixos
    F->>A: GET /consultas (verifica ocupados na data)
    A-->>F: Consultas já agendadas naquele dia
    Note over F: Remove da lista os horários já ocupados
    P->>F: Confirma agendamento
    F->>A: POST /consultas { medicoId, data, hora }
    A->>A: Valida: não é fim de semana,\nmédico atende nesse dia/horário,\nhorário ainda livre
    alt horário disponível
        A->>D: Cria consulta (status "Agendado")
        D-->>A: Consulta criada
        A-->>F: 201 Created
        F-->>P: "Consulta agendada com sucesso!"
    else horário ocupado nesse meio-tempo
        A-->>F: 409 Conflito
        F-->>P: "Esse horário já está reservado. Escolha outro."
    end
```
