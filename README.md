const express = require('express');
const mysql = require('mysql2');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(express.json());

const db = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'tu_contraseña',
  database: 'nombre_de_tu_base'
});

db.connect((err) => {
  if (err) {
    console.log('Error de conexión:', err);
  } else {
    console.log('Conectado a MySQL');
  }
});

app.post('/login', (req, res) => {
  const { email, password } = req.body;

  db.query(
    'SELECT * FROM usuarios WHERE email = ? AND password = ?',
    [email, password],
    (err, result) => {
      if (err) {
        return res.status(500).send('Error en el servidor');
      }

      if (result.length > 0) {
        res.json({ success: true, user: result[0] });
      } else {
        res.status(401).json({ success: false, message: 'Credenciales incorrectas' });
      }
    }
  );
});

app.listen(3000, () => {
  console.log('API corriendo en http://localhost:3000');
});
