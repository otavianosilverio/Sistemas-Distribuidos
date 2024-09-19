# Sistemas-Distribuidos

# Exemplo de API REST - Node.js

Aqui está um exemplo de uma API REST em Node.js usando o framework Express para realizar operações de `GET`, `POST`, `PUT` e `DELETE` em uma tabela de clientes no MySQL. A tabela possui as colunas `cd_codigo_cli` (int) e `nm_cliente_cli` (varchar(10)).

### Passo 1: Instalação das dependências
Você precisará instalar as bibliotecas necessárias. No terminal, execute:

```bash
npm init -y
npm install express mysql2 body-parser
```

### Passo 2: Criando o banco de dados MySQL
Aqui está o SQL para criar a tabela `clientes`:

```sql
CREATE DATABASE IF NOT EXISTS sistema_clientes;

USE sistema_clientes;

CREATE TABLE IF NOT EXISTS clientes (
    cd_codigo_cli INT AUTO_INCREMENT PRIMARY KEY,
    nm_cliente_cli VARCHAR(10) NOT NULL
);
```

### Passo 3: Implementação da API

Crie um arquivo `app.js` com o código abaixo:

```javascript
const express = require('express');
const mysql = require('mysql2');
const bodyParser = require('body-parser');

const app = express();
app.use(bodyParser.json());

// Configuração da conexão com o banco de dados MySQL
const db = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: 'sua_senha',
    database: 'sistema_clientes'
});

db.connect((err) => {
    if (err) {
        console.error('Erro ao conectar ao MySQL:', err);
    } else {
        console.log('Conectado ao MySQL.');
    }
});

// Rota GET para listar todos os clientes
app.get('/clientes', (req, res) => {
    const sql = 'SELECT * FROM clientes';
    db.query(sql, (err, results) => {
        if (err) throw err;
        res.json(results);
    });
});

// Rota GET para buscar um cliente por ID
app.get('/clientes/:id', (req, res) => {
    const { id } = req.params;
    const sql = 'SELECT * FROM clientes WHERE cd_codigo_cli = ?';
    db.query(sql, [id], (err, result) => {
        if (err) throw err;
        if (result.length > 0) {
            res.json(result[0]);
        } else {
            res.status(404).json({ message: 'Cliente não encontrado' });
        }
    });
});

// Rota POST para adicionar um novo cliente
app.post('/clientes', (req, res) => {
    const { nm_cliente_cli } = req.body;
    const sql = 'INSERT INTO clientes (nm_cliente_cli) VALUES (?)';
    db.query(sql, [nm_cliente_cli], (err, result) => {
        if (err) throw err;
        res.status(201).json({ message: 'Cliente adicionado', id: result.insertId });
    });
});

// Rota PUT para atualizar um cliente
app.put('/clientes/:id', (req, res) => {
    const { id } = req.params;
    const { nm_cliente_cli } = req.body;
    const sql = 'UPDATE clientes SET nm_cliente_cli = ? WHERE cd_codigo_cli = ?';
    db.query(sql, [nm_cliente_cli, id], (err, result) => {
        if (err) throw err;
        if (result.affectedRows > 0) {
            res.json({ message: 'Cliente atualizado' });
        } else {
            res.status(404).json({ message: 'Cliente não encontrado' });
        }
    });
});

// Rota DELETE para remover um cliente
app.delete('/clientes/:id', (req, res) => {
    const { id } = req.params;
    const sql = 'DELETE FROM clientes WHERE cd_codigo_cli = ?';
    db.query(sql, [id], (err, result) => {
        if (err) throw err;
        if (result.affectedRows > 0) {
            res.json({ message: 'Cliente removido' });
        } else {
            res.status(404).json({ message: 'Cliente não encontrado' });
        }
    });
});

// Iniciar o servidor
app.listen(3000, () => {
    console.log('Servidor rodando na porta 3000');
});
```

### Explicação das rotas:

1. **GET** `/clientes`: Retorna a lista de todos os clientes.
2. **GET** `/clientes/:id`: Retorna os dados de um cliente específico com base no `cd_codigo_cli`.
3. **POST** `/clientes`: Adiciona um novo cliente.
4. **PUT** `/clientes/:id`: Atualiza os dados de um cliente específico.
5. **DELETE** `/clientes/:id`: Remove um cliente específico.

### Passo 4: Rodar o servidor

No terminal, execute o comando:

```bash
node app.js
```

Agora sua API estará rodando na porta 3000.

Você pode testar as requisições com o Postman ou cURL para verificar a funcionalidade da API.

Claro! Abaixo estão as rotas da API REST que implementamos para manipulação da tabela de clientes no banco de dados MySQL.

### Rotas da API

1. **GET** `/clientes`  
   Retorna todos os clientes cadastrados no banco de dados.
   
   **Exemplo de Requisição:**
   ```bash
   GET http://localhost:3000/clientes
   ```

   **Resposta:**
   ```json
   [
     {
       "cd_codigo_cli": 1,
       "nm_cliente_cli": "Joao"
     },
     {
       "cd_codigo_cli": 2,
       "nm_cliente_cli": "Maria"
     }
   ]
   ```

2. **GET** `/clientes/:id`  
   Retorna os dados de um cliente específico com base no `cd_codigo_cli`.

   **Exemplo de Requisição:**
   ```bash
   GET http://localhost:3000/clientes/1
   ```

   **Resposta:**
   ```json
   {
     "cd_codigo_cli": 1,
     "nm_cliente_cli": "Joao"
   }
   ```

   **Caso o cliente não seja encontrado:**
   ```json
   {
     "message": "Cliente não encontrado"
   }
   ```

3. **POST** `/clientes`  
   Adiciona um novo cliente ao banco de dados.

   **Exemplo de Requisição (corpo da requisição em JSON):**
   ```bash
   POST http://localhost:3000/clientes
   Content-Type: application/json
   ```
   ```json
   {
     "nm_cliente_cli": "Pedro"
   }
   ```

   **Resposta:**
   ```json
   {
     "message": "Cliente adicionado",
     "id": 3
   }
   ```

4. **PUT** `/clientes/:id`  
   Atualiza os dados de um cliente específico com base no `cd_codigo_cli`.

   **Exemplo de Requisição (corpo da requisição em JSON):**
   ```bash
   PUT http://localhost:3000/clientes/1
   Content-Type: application/json
   ```
   ```json
   {
     "nm_cliente_cli": "Joao Silva"
   }
   ```

   **Resposta:**
   ```json
   {
     "message": "Cliente atualizado"
   }
   ```

   **Caso o cliente não seja encontrado:**
   ```json
   {
     "message": "Cliente não encontrado"
   }
   ```

5. **DELETE** `/clientes/:id`  
   Remove um cliente específico com base no `cd_codigo_cli`.

   **Exemplo de Requisição:**
   ```bash
   DELETE http://localhost:3000/clientes/1
   ```

   **Resposta:**
   ```json
   {
     "message": "Cliente removido"
   }
   ```

   **Caso o cliente não seja encontrado:**
   ```json
   {
     "message": "Cliente não encontrado"
   }
   ```

Essas rotas cobrem todas as operações CRUD (Create, Read, Update, Delete) para a tabela de clientes.
