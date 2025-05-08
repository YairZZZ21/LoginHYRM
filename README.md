const express = require('express');
const mysql = require('mysql2');
const cors = require('cors');
const bcrypt = require('bcrypt');
require('dotenv').config();

const app = express();
app.use(cors());
app.use(express.json());

// Configuración de la base de datos usando variables de entorno
const db = mysql.createConnection({
  host: process.env.DB_HOST,       // ejemplo: 'localhost'
  user: process.env.DB_USER,       // ejemplo: 'root'
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME    // nombre de la base de datos
});

db.connect((err) => {
  if (err) {
    console.log('Error de conexión:', err);
  } else {
    console.log('Conectado a MySQL');
  }
});

// Ruta para login
app.post('/login', (req, res) => {
  const { email, password } = req.body;

  db.query(
    'SELECT * FROM usuarios WHERE email = ?',
    [email],
    (err, result) => {
      if (err) {
        return res.status(500).send('Error en el servidor');
      }

      if (result.length > 0) {
        const user = result[0];

        bcrypt.compare(password, user.password, (err, isMatch) => {
          if (err) return res.status(500).send('Error al verificar la contraseña');

          if (isMatch) {
            res.json({ success: true, user });
          } else {
            res.status(401).json({ success: false, message: 'Credenciales incorrectas' });
          }
        });
      } else {
        res.status(401).json({ success: false, message: 'Credenciales incorrectas' });
      }
    }
  );
});

// Iniciar el servidor
app.listen(3000, () => {
  console.log('API corriendo en http://localhost:3000');
});
00, () => {
  console.log('API corriendo en http://localhost:3000');
});
