# Rotas-da-API-
 Rotas da API (backend/src/routes/music.routes.js)

/**
 * Rotas de Músicas
 * Define endpoints RESTful para operações CRUD
 */
const express = require('express');
const router = express.Router();
const multer = require('multer');
const path = require('path');
const MusicController = require('../controllers/MusicController');

// Configuração do Multer para upload de arquivos
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/');
  },
  filename: (req, file, cb) => {
    // Gera nome único: timestamp-nomeoriginal.ext
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
    cb(null, uniqueSuffix + path.extname(file.originalname));
  }
});

const upload = multer({ 
  storage,
  limits: { fileSize: 20 * 1024 * 1024 }, // Limite 20MB
  fileFilter: (req, file, cb) => {
    // Aceita apenas arquivos de áudio
    const allowedMimes = ['audio/mpeg', 'audio/wav', 'audio/mp3'];
    if (allowedMimes.includes(file.mimetype)) {
      cb(null, true);
    } else {
      cb(new Error('Tipo de arquivo não suportado. Use MP3 ou WAV'));
    }
  }
});

// Rotas RESTful
router.get('/', MusicController.index);           // Listar todas
router.get('/search', MusicController.search);    // Busca
router.get('/:id', MusicController.show);         // Detalhes
router.get('/:id/stream', MusicController.stream); // Streaming
router.post('/', upload.single('audio'), MusicController.store); // Upload

module.exports = router;
