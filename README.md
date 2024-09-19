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



Aqui está um exemplo de como você pode criar um front-end básico usando React para interagir com a API de manutenção da tabela de clientes que criamos em Node.js.

Passo 1: Configuração do Projeto React
No terminal, crie um novo projeto React:

bash
Copiar código
npx create-react-app cliente-app
cd cliente-app
npm start
Passo 2: Instalação do Axios
Usaremos o axios para fazer as chamadas HTTP à API. Para instalar o axios, execute o seguinte comando no terminal:

bash
Copiar código
npm install axios
Passo 3: Implementação do Front-end
Aqui está o código para o front-end básico que faz integração com a API.

No arquivo src/App.js, substitua o conteúdo com o seguinte:

javascript
Copiar código
import React, { useState, useEffect } from 'react';
import axios from 'axios';

const apiURL = 'http://localhost:3000/clientes';

function App() {
  const [clientes, setClientes] = useState([]);
  const [novoCliente, setNovoCliente] = useState('');
  const [editando, setEditando] = useState(null);
  const [nomeEditado, setNomeEditado] = useState('');

  // Função para carregar os clientes da API
  const carregarClientes = async () => {
    try {
      const response = await axios.get(apiURL);
      setClientes(response.data);
    } catch (error) {
      console.error('Erro ao carregar clientes:', error);
    }
  };

  // Carregar os clientes quando o componente for montado
  useEffect(() => {
    carregarClientes();
  }, []);

  // Adicionar um novo cliente
  const adicionarCliente = async () => {
    if (novoCliente.trim()) {
      try {
        await axios.post(apiURL, { nm_cliente_cli: novoCliente });
        setNovoCliente('');
        carregarClientes(); // Atualizar a lista de clientes
      } catch (error) {
        console.error('Erro ao adicionar cliente:', error);
      }
    }
  };

  // Atualizar um cliente
  const atualizarCliente = async (id) => {
    if (nomeEditado.trim()) {
      try {
        await axios.put(`${apiURL}/${id}`, { nm_cliente_cli: nomeEditado });
        setEditando(null);
        setNomeEditado('');
        carregarClientes();
      } catch (error) {
        console.error('Erro ao atualizar cliente:', error);
      }
    }
  };

  // Excluir um cliente
  const excluirCliente = async (id) => {
    try {
      await axios.delete(`${apiURL}/${id}`);
      carregarClientes(); // Atualizar a lista de clientes
    } catch (error) {
      console.error('Erro ao excluir cliente:', error);
    }
  };

  return (
    <div style={{ padding: '20px' }}>
      <h1>Gerenciamento de Clientes</h1>

      {/* Formulário para adicionar cliente */}
      <div>
        <input
          type="text"
          placeholder="Nome do cliente"
          value={novoCliente}
          onChange={(e) => setNovoCliente(e.target.value)}
        />
        <button onClick={adicionarCliente}>Adicionar Cliente</button>
      </div>

      {/* Lista de clientes */}
      <h2>Clientes:</h2>
      <ul>
        {clientes.map((cliente) => (
          <li key={cliente.cd_codigo_cli}>
            {editando === cliente.cd_codigo_cli ? (
              <>
                <input
                  type="text"
                  value={nomeEditado}
                  onChange={(e) => setNomeEditado(e.target.value)}
                />
                <button onClick={() => atualizarCliente(cliente.cd_codigo_cli)}>Salvar</button>
                <button onClick={() => setEditando(null)}>Cancelar</button>
              </>
            ) : (
              <>
                {cliente.nm_cliente_cli}
                <button onClick={() => {
                  setEditando(cliente.cd_codigo_cli);
                  setNomeEditado(cliente.nm_cliente_cli);
                }}>Editar</button>
                <button onClick={() => excluirCliente(cliente.cd_codigo_cli)}>Excluir</button>
              </>
            )}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
Explicação do código:
Carregar clientes: A função carregarClientes usa o axios.get() para buscar todos os clientes da API e armazena a resposta no estado clientes.
Adicionar cliente: A função adicionarCliente usa o axios.post() para enviar o nome de um novo cliente para a API.
Atualizar cliente: A função atualizarCliente permite editar o nome de um cliente existente e envia a alteração para a API via axios.put().
Excluir cliente: A função excluirCliente usa o axios.delete() para remover um cliente da lista com base no cd_codigo_cli.
Editar cliente: Ao clicar no botão "Editar", o campo de texto correspondente ao nome do cliente se torna editável.
Passo 4: Rodar o Front-end
No terminal, dentro da pasta do projeto React, execute:

bash
Copiar código
npm start
Integração com a API:
O front-end agora deve estar disponível no http://localhost:3000.
A API REST do backend também deve estar rodando em http://localhost:3000, então pode haver conflitos de portas. Nesse caso, você pode alterar a porta do React ou do Node.js.
Para mudar a porta do React, abra o arquivo package.json e adicione:

json
Copiar código
"start": "PORT=3001 react-scripts start",
Agora, o front-end será servido na porta 3001, enquanto o back-end continuará na porta 3000.
