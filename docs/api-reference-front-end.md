# Referência da API - NextCertify

Este documento descreve os endpoints da API do sistema NextCertify, baseando-se na implementação atual do backend (`Next-Event`) e no consumo realizado pelo frontend (`NextCertify`).

## Informações Gerais

- **URL Base:** `http://localhost:3000/api`
- **Formato de Dados:** JSON
- **Autenticação:** JWT (JSON Web Token) via Header `Authorization`.

---

## 🔐 Autenticação e Segurança

A maioria das rotas requer que o usuário esteja autenticado. O token JWT deve ser enviado no cabeçalho de todas as requisições protegidas.

**Exemplo de Header:**
```http
Authorization: Bearer <seu_token_aqui>
```

### Papéis de Usuário (Roles)
O sistema utiliza controle de acesso baseado em papéis:
- `admin`: Acesso total.
- `coordinator`: Gerencia usuários, certificados e alunos.
- `tutor`: Visualiza alunos e gerencia acompanhamentos.
- `scholarship_holder` (Bolsista): Envia certificados e visualiza relatórios individuais.

---

## 👥 Usuários (`/users`)

Gerenciamento de contas e autenticação.

| Método | Endpoint | Acesso | Descrição |
| :--- | :--- | :--- | :--- |
| `POST` | `/users` | Público | Cria um novo usuário (Cadastro). |
| `POST` | `/users/login` | Público | Autentica um usuário e retorna o token JWT. |
| `GET` | `/users` | Coordenador | Lista todos os usuários cadastrados. |
| `GET` | `/users/coordenadores` | Coordenador | Lista todos os coordenadores cadastrados. |
| `GET` | `/users/:id` | Autenticado | Obtém detalhes de um usuário específico. |
| `PUT` | `/users/:id` | Autenticado | Atualiza informações do perfil do usuário. |
| `PATCH` | `/users/:id/atribuir-papel` | Coordenador | Altera o papel (role) de um usuário. |
| `GET` | `/users/tutores` | Autenticado | Lista todos os tutores cadastrados. |
| `GET` | `/users/bolsistas` | Coordenador/Tutor | Lista todos os bolsistas cadastrados. |

---

## 📜 Certificados (`/certificates`)

Gestão de documentos e validações.

| Método | Endpoint | Acesso | Descrição |
| :--- | :--- | :--- | :--- |
| `POST` | `/certificates/upload` | Autenticado | Faz o upload de um certificado (Multipart Form-Data). |
| `GET` | `/certificates/user/:id` | Autenticado | Lista todos os certificados de um usuário específico. |
| `GET` | `/certificates` | Admin/Coord | Lista todos os certificados do sistema (com filtros). |
| `GET` | `/certificates/:id/download` | Autenticado | Faz o download do arquivo original do certificado. |
| `PATCH` | `/certificates/:id/status` | Admin/Coord | Modifica o status (Aprovado/Rejeitado) e adiciona comentários. |
| `DELETE` | `/certificates/:id` | Autenticado | Remove um certificado do sistema. |
| `GET` | `/certificates/report` | Aluno/Bolsista | Gera um relatório de horas baseado nos certificados. |

---

## 📊 Relatórios e Dashboard (`/relatorios` / `/bolsistas`)

Dados analíticos e visões consolidadas.

| Método | Endpoint | Acesso | Descrição |
| :--- | :--- | :--- | :--- |
| `GET` | `/relatorios/individual/aluno/:id` | Autenticado | Relatório detalhado de um aluno (horas, certificados). |
| `GET` | `/relatorios/individual/tutor/:id` | Autenticado | Relatório de atividades de um tutor. |
| `GET` | `/relatorios/coordenadores/:id` | Coordenador/Admin | Lista relatórios atribuídos a um coordenador. |
| `POST` | `/relatorios/consolidado` | Bolsista/Coord | Gera estatísticas globais do sistema. |
| `GET` | `/bolsistas/dashboard` | Bolsista | Dados específicos para o painel principal do bolsista. |
| `GET` | `/relatorios/template` | Autenticado | Retorna a estrutura (JSON) usada nos relatórios. |

---

## 🏫 Tutores (`/tutores`)

| Método | Endpoint | Acesso | Descrição |
| :--- | :--- | :--- | :--- |
| `GET` | `/tutores/meus-alunos` | Tutor | Lista os alunos vinculados ao tutor logado. |
| `GET` | `/tutores/:id/alunos` | Coordenador | Lista os alunos vinculados a um tutor específico. |

---

## 📅 Tutoria e Períodos

Gerenciamento de ciclos de tutoria e avaliações.

### Períodos de Tutoria (`/periodo-tutoria`)
| Método | Endpoint | Acesso | Descrição |
| :--- | :--- | :--- | :--- |
| `GET` | `/periodo-tutoria` | Autenticado | Lista os períodos de tutoria cadastrados. |
| `POST` | `/periodo-tutoria` | Coordenador | Cria um novo período de tutoria. |
| `PUT` | `/periodo-tutoria/:id` | Coordenador | Atualiza um período existente. |
| `DELETE` | `/periodo-tutoria/:id` | Coordenador | Remove um período de tutoria. |

### Avaliações e Acompanhamento
| Método | Endpoint | Acesso | Descrição |
| :--- | :--- | :--- | :--- |
| `POST` | `/avaliacao-tutoria` | Autenticado | Submete uma nova avaliação de tutoria. |
| `POST` | `/form-acompanhamento` | Autenticado | Salva um formulário de acompanhamento mensal. |
| `GET` | `/form-acompanhamento` | Autenticado | Lista formulários (aceita `tutorId` como query string). |

---

## ⚙️ Configurações e Vínculos

| Método | Endpoint | Acesso | Descrição |
| :--- | :--- | :--- | :--- |
| `POST` | `/alocar-tutor-aluno` | Coordenador | Vincula um aluno a um tutor/bolsista em um período. |
| `GET` | `/alocar-tutor-aluno` | Autenticado | Lista vínculos (aceita filtros por `periodoId`, `tutorId`, etc.). |
| `DELETE` | `/alocar-tutor-aluno/:id` | Coordenador | Remove um vínculo de alocação. |
| `POST` | `/carga-horaria-minima` | Coordenador | Define horas mínimas por categoria para um período. |
| `GET` | `/carga-horaria-minima` | Autenticado | Lista cargas horárias mínimas configuradas. |
| `DELETE` | `/carga-horaria-minima/:id` | Coordenador | Remove uma configuração de carga horária. |

---

## 📢 Notificações (`/notifications`)

| Método | Endpoint | Acesso | Descrição |
| :--- | :--- | :--- | :--- |
| `GET` | `/notifications` | Autenticado | Lista as notificações do usuário logado. |
| `GET` | `/notifications/unread-count` | Autenticado | Retorna a contagem de notificações não lidas. |
| `PATCH` | `/notifications/:id/read` | Autenticado | Marca uma notificação específica como lida. |
| `PATCH` | `/notifications/mark-all-read` | Autenticado | Marca todas as notificações do usuário como lidas. |

---

## 🛠️ Tratamento de Erros

A API utiliza códigos de status HTTP padrão:
- `200 OK`: Sucesso.
- `201 Created`: Recurso criado com sucesso.
- `400 Bad Request`: Erro de validação ou parâmetros incorretos.
- `401 Unauthorized`: Token ausente ou inválido.
- `403 Forbidden`: Usuário não tem permissão para este recurso.
- `404 Not Found`: Recurso não encontrado.
- `500 Internal Server Error`: Erro inesperado no servidor.

**Corpo de erro padrão:**
```json
{
  "error": "Descrição curta do erro",
  "message": "Detalhes adicionais sobre o problema."
}
```
