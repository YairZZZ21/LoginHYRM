// Requiere los paquetes necesarios
const express = require('express');
const mysql = require('mysql2');
const cors = require('cors');
const bcrypt = require('bcrypt');
require('dotenv').config();

// Inicializa la app de Express
const app = express();
app.use(cors());
app.use(express.json());

// Conexión a MySQL usando variables de entorno
const db = mysql.createConnection({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
});

db.connect((err) => {
  if (err) {
    console.log('Error de conexión:', err);
  } else {
    console.log('Conectado a MySQL');
  }
});

// Ruta de login
app.post('/login', (req, res) => {
  const { email, password } = req.body;

  db.query('SELECT * FROM usuarios WHERE email = ?', [email], (err, result) => {
    if (err) {
      return res.status(500).send('Error en el servidor');
    }

    if (result.length > 0) {
      const user = result[0];
      bcrypt.compare(password, user.password, (err, match) => {
        if (match) {
          res.json({ success: true, user });
        } else {
          res.status(401).json({ success: false, message: 'Contraseña incorrecta' });
        }
      });
    } else {
      res.status(401).json({ success: false, message: 'Usuario no encontrado' });
    }
  });
});

// Levanta el servidor
app.listen(3000, () => {
  console.log('API corriendo en http://localhost:3000');
});
