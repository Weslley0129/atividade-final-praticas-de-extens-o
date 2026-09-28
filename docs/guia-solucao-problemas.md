# Guia de Solução de Problemas — Clique Saúde

Problemas comuns ao rodar ou usar o sistema, e como resolver.

## Ao rodar o projeto localmente

### "Nenhum horário livre nesse dia para este médico"

Não é um erro — cada médico atende em dias e horários fixos (agenda
comercial: segunda a sexta, 08:00–11:00 e 14:00–17:00). Se a data escolhida
cair num dia sem expediente daquele médico ou for fim de semana, nenhum
horário aparece. A própria tela mostra, logo acima do campo de data, a
frase **"Esse médico atende: ..."** com os dias/horários corretos —
escolha uma dessas datas.

### `npm run prisma:migrate` falha com erro de schema / `url` não suportado

Sintoma: o Prisma CLI instalado é uma versão major diferente da
`@prisma/client` (ex.: CLI 8.x com client 7.x, ou vice-versa), e o Prisma
7+ mudou a sintaxe de `schema.prisma`. Solução: reinstalar as duas
dependências fixando a mesma versão estável:

```bash
npm install prisma@5 @prisma/client@5
```

### No Windows, comandos do Prisma falham com `ENOENT` mesmo o arquivo existindo

Sintoma comum quando o projeto está numa pasta muito aninhada (caminho
maior que 260 caracteres) — o binário nativo do Prisma
(`schema-engine-windows.exe`) não é encontrado mesmo existindo no disco.
Solução: mapear o caminho para uma letra de unidade curta antes de rodar
os comandos:

```powershell
subst Z: "C:\caminho\muito\longo\até\o\projeto"
```

E rodar os comandos a partir de `Z:\` em vez do caminho completo.

### `npm test` do front-end falha ou trava aleatoriamente no Windows

O Vite 8 usa o bundler Rolldown (binário nativo em Rust, ainda em
amadurecimento), que pode gerar panics de memória sob paralelismo. Se isso
acontecer, confirme que `vitest.config.js` está com:

```js
pool: "forks",
poolOptions: { forks: { singleFork: true } },
```

### CI do GitHub Actions falha instantaneamente (em segundos) no job de testes

Verifique a versão do Node configurada no workflow (`.github/workflows/ci.yml`).
O binário nativo do Rolldown exige Node `^20.19.0` ou `>=22.12.0` — se o
runner resolver uma versão de Node 20 abaixo desse patch, o binário falha
ao carregar e derruba o Vitest antes de qualquer teste rodar. Use
`node-version: "22"`.

### `/api-docs` não abre ou mostra página em branco

- Confirme que o servidor do back-end está rodando (`npm run dev` em
  `clique-saude-api`, porta padrão `3333`).
- Verifique no terminal se não houve erro ao carregar `src/config/swagger.js`
  (por exemplo, dependência `swagger-jsdoc`/`swagger-ui-express` não
  instalada — rode `npm install` novamente).

## Ao usar o sistema (usuário final)

### "Esse horário já está reservado. Escolha outro." ao confirmar o agendamento

Outra pessoa reservou o mesmo horário poucos segundos antes de você. Volte
à etapa 3 e escolha outro horário — a lista é atualizada a cada consulta
agendada.

### Esqueci minha senha

O sistema ainda não implementa recuperação de senha por e-mail (o botão
"Esqueci minha senha" é um placeholder). Solução atual: peça para o
administrador recriar o acesso, ou use uma das contas de teste do seed
durante o desenvolvimento.

### Cadastrei um médico novo pela API, mas ele não aparece na lista do front-end

O front-end das Atividades 1/3 ainda consome o mock `json-server`
(`db.json`), não a API real — os dados não são compartilhados entre os
dois automaticamente. Para usar dados reais, edite `db.json` (ambiente de
mock) ou aponte `clinicaApi.js` para a API real (`clique-saude-api`), como
descrito na seção de arquitetura.

### Não vejo o "Painel administrativo" no menu

Esse menu só aparece para contas com `tipo: "admin"`. Entre com
`admin@cliquesaude.com` / `admin123` (conta de teste do seed) para
visualizá-lo.
