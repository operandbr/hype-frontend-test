# Teste Técnico Dev Back-end Operand (Nível Pleno)

## Descrição do Projeto
Você foi contratado para desenvolver o backend de uma aplicação de **conversão de vídeos**. A aplicação permite que usuários autenticados enviem um vídeo, solicitem a conversão para um formato padrão e acompanhem o status até a finalização, podendo então baixar o arquivo convertido.

O foco do teste está no **backend (NestJS + Firebase)**, mas será necessário construir uma **interface mínima** (em qualquer framework de sua preferência) para interagir com o sistema. O frontend **não será avaliado** (pode ser o mais simples possível) — ele serve apenas como client para testar o fluxo.

---

## Funcionalidades Requeridas

### Autenticação (Firebase Authentication)
- O usuário deve conseguir fazer login usando Firebase Authentication.
- O backend deve validar o token do Firebase (`Authorization: Bearer <token>`) nas rotas protegidas.
- O backend deve garantir que cada usuário só possa acessar os próprios dados (jobs/vídeos).

Observação:
- Para simplificar, o tipo de login no frontend pode ser **Anonymous Auth** ou **Email/Senha** (fica a critério do candidato).
- Recuperação de senha é **opcional**.

---

### Gerenciamento de Vídeos (upload + job)
Cada usuário autenticado deve ser capaz de:
- Enviar um vídeo para o backend via **multipart/form-data**.
- Criar um registro no Firestore representando o vídeo/job.
- Solicitar a conversão do vídeo.
- Consultar o status do job.
- Baixar o arquivo final quando a conversão estiver concluída.

---

### Conversão de Vídeo (FFmpeg)
- A conversão deve ser feita usando **FFmpeg**.
- Deve existir apenas **1 preset fixo** (para simplificar), por exemplo: `MP4_720P` (1280x720, H.264 + AAC).
- O candidato **não precisa** suportar múltiplos presets, thumbnails, múltiplos codecs ou otimizações avançadas.

---

### Status do Job
O job deve possuir pelo menos os status:
- `UPLOADED` (upload concluído)
- `PROCESSING` (convertendo)
- `DONE` (finalizado com sucesso)
- `FAILED` (falhou)

---

### Validação e Permissões
- O backend deve validar se o usuário tem permissão para visualizar/baixar apenas seus próprios vídeos/jobs.
- O acesso ao banco (Firestore) e aos arquivos (Storage) deve ser restrito usando **regras de segurança do Firebase** (ou explicando como seriam as regras, caso utilize Admin SDK no servidor).

---

## Endpoints mínimos sugeridos (o candidato pode ajustar nomes)

### `POST /videos`
- Upload do vídeo (multipart/form-data)
- Salva no Firebase Storage e cria documento no Firestore com status `UPLOADED`

### `POST /videos/:videoId/convert`
- Inicia a conversão do vídeo (atualiza para `PROCESSING` e depois `DONE/FAILED`)

### `GET /videos/:videoId`
- Retorna os dados do job e status atual

### `GET /videos/:videoId/download`
- Disponibiliza o arquivo convertido quando `status = DONE` (ex.: URL assinada ou stream)

Observação:
- O processamento pode ser “assíncrono simples” (sem filas externas). Não é obrigatório usar Pub/Sub, Cloud Tasks, Redis, etc.

---

## Frontend (client) — obrigatório para testar (não avaliado)
Criar uma interface mínima, em qualquer framework, que permita:
- Login com Firebase Auth
- Selecionar e enviar vídeo
- Solicitar conversão
- Acompanhar status (polling simples)
- Baixar o arquivo quando estiver pronto

Interface simples e funcional; não é necessário design avançado.

---

## Requisitos Técnicos
- NestJS + TypeScript no backend
- Firebase Auth para autenticação (validação de ID Token no backend)
- Firestore para persistir jobs (metadados + status)
- Firebase Storage para armazenar input e output
- FFmpeg para conversão de vídeo
- Validação e Segurança: garantir isolamento por usuário (código + regras Firebase)

### Docker (obrigatório)
A aplicação deve ser dockerizada:
- Criar um [Dockerfile](Dockerfile) para o backend contendo o FFmpeg instalado
- (Opcional) Criar um [docker-compose.yml](docker-compose.yml) para facilitar a execução local
- O [README.md](README.md) deve explicar como rodar tudo via Docker (`docker build` / `docker compose up`)

---

## Entrega
- Código Fonte: um repositório no GitHub contendo todo o código-fonte da aplicação (backend e frontend demo).
- Instruções de Instalação: um [README.md](README.md) com instruções claras para rodar localmente, incluindo:
  - como configurar o Firebase (Auth, Firestore, Storage)
  - variáveis de ambiente necessárias
  - como instalar/rodar FFmpeg (ou via Docker)
  - como executar backend e frontend
- Documentação: breve descrição das decisões técnicas mais importantes (ex.: modelagem do Firestore, organização do Nest, como o FFmpeg é executado, como garantiu segurança por usuário).

O candidato deve estar apto, na data de entrevista técnica, para apresentar o projeto, suas características e funcionamento.

---

## Critérios de Avaliação
- Conhecimento de NestJS: organização em módulos, controllers/services, DTOs/validações, tratamento de erros.
- Integração com Firebase Auth: validação correta do token e proteção das rotas.
- Uso do Firestore: modelagem mínima consistente (status, timestamps) e leitura/escrita bem organizada.
- Uso do Firebase Storage: upload do arquivo de entrada e disponibilização do arquivo convertido.
- Conversão via FFmpeg: gerar um arquivo final reproduzível e atualizar status corretamente (`DONE/FAILED`).
- Segurança/Permissões: o usuário só acessa os próprios vídeos/jobs (no backend e, quando aplicável, nas regras).
- Boas práticas de código: legibilidade, organização, clareza do [README.md](README.md) e facilidade de execução local.
- Bom uso do Docker e Docker Compose.
