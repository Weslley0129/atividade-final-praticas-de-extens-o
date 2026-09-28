# Clique Saúde — Documentação Final (PPE III, Atividade 4)

Este repositório é a **entrega final** de Projetos e Práticas de Extensão III:
reúne a documentação técnica completa do sistema **Clique Saúde**, separada
dos repositórios de código para servir como porta de entrada única para
quem for avaliar ou dar manutenção no projeto.

> Este repositório **não contém código-fonte** — ele documenta os dois
> projetos abaixo, que continuam sendo desenvolvidos e versionados
> separadamente.

## Os projetos

| Repositório | O que é | Link |
|---|---|---|
| `clique-saude-react` | Front-end (SPA React) — Atividades 1 e 3 de PPE III | [praticas-de-extenss-o-3](https://github.com/Weslley0129/praticas-de-extenss-o-3) |
| `clique-saude-api` | Back-end real (Node/Express/Prisma) — Atividades 2, 3 e 4 (Swagger) | [atividade-pratica-de-extenss-o-2](https://github.com/Weslley0129/atividade-pratica-de-extenss-o-2) |

## Índice da documentação

- 📘 [Manual do usuário](docs/manual-do-usuario.md) — como usar o sistema, tela a tela, com screenshots.
- 🏗️ [Arquitetura e modelo de dados](docs/arquitetura.md) — diagramas de arquitetura, fluxo e banco de dados.
- 🛠️ [Guia de solução de problemas](docs/guia-solucao-problemas.md) — erros comuns e como resolver.
- 📽️ [Apresentação final](docs/apresentacao/) — slides e roteiro do vídeo de apresentação.
- 🔌 **Documentação interativa da API (Swagger/OpenAPI)**: com o back-end
  rodando localmente (`npm run dev` em `clique-saude-api`), acesse
  `http://localhost:3333/api-docs`. Veja como cada endpoint foi documentado
  em [`clique-saude-api/src/routes`](https://github.com/Weslley0129/atividade-pratica-de-extenss-o-2/tree/main/src/routes)
  e a definição base em [`src/config/swagger.js`](https://github.com/Weslley0129/atividade-pratica-de-extenss-o-2/blob/main/src/config/swagger.js).

## Instruções de instalação e execução

Cada projeto tem seu próprio `README.md` com instruções detalhadas. Resumo:

```bash
# 1. Clonar os dois repositórios
git clone https://github.com/Weslley0129/praticas-de-extenss-o-3.git clique-saude-react
git clone https://github.com/Weslley0129/atividade-pratica-de-extenss-o-2.git clique-saude-api

# 2. Front-end (usa json-server como mock da API, porta 5173 + 3001)
cd clique-saude-react
npm install
npm run dev

# 3. Back-end real (Node/Express/Prisma, porta 3333)
cd ../clique-saude-api
npm install
cp .env.example .env
npm run prisma:migrate
npm run seed
npm run dev
```

Logins de teste (criados pelo seed, iguais nos dois projetos):

- Paciente: `paciente@cliquesaude.com` / `123456`
- Admin: `admin@cliquesaude.com` / `admin123`

## Tecnologias utilizadas

| Camada | Tecnologia |
|---|---|
| Front-end | React 19 + Vite, React Router, Context API |
| Back-end | Node.js + Express 5, arquitetura em camadas |
| Banco de dados | SQLite (dev) via Prisma ORM |
| Autenticação | JWT + bcrypt |
| Validação | Zod |
| Documentação da API | Swagger / OpenAPI 3.0 |
| Testes | Vitest + React Testing Library (front) · Jest + Supertest (back) |
| CI/CD | GitHub Actions (lint/test/build em cada push, nos dois repositórios) |

## Arquitetura do sistema

Ver diagramas completos em [`docs/arquitetura.md`](docs/arquitetura.md).
Resumo: SPA React consumindo uma API REST em camadas
(`routes → controllers → services → models`), com Prisma isolando o acesso
ao banco de dados e JWT protegendo as rotas sensíveis.

## Fluxo de contribuições e boas práticas

Projeto acadêmico individual (PPE III), mas seguindo práticas de mercado:

- Commits pequenos e descritivos, em português, no imperativo.
- Toda mudança de código passa pela suíte de testes automatizados antes do
  commit (`npm test`) e pelo CI (GitHub Actions) antes de ser considerada
  "pronta".
- Regras de negócio (disponibilidade de horário, permissões
  paciente/admin) ficam isoladas na camada de `services`, nunca em
  controllers ou rotas.
- Erros esperados são sempre instâncias de `AppError` (ou subclasse),
  capturadas por um middleware central — nunca um `try/catch` solto
  formatando resposta de erro manualmente.

## Links para documentação adicional

- [Relatório da Atividade 1](https://github.com/Weslley0129/praticas-de-extenss-o-3/blob/main/Relatorio_Atividade1_PPEIII.pdf) — revisão do sistema anterior, roadmap e diagrama arquitetural.
- [README do front-end](https://github.com/Weslley0129/praticas-de-extenss-o-3/blob/main/README.md)
- [README do back-end](https://github.com/Weslley0129/atividade-pratica-de-extenss-o-2/blob/main/README.md)
