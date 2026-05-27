[blackbox-output-code-SXY7QEPXLG.html](https://github.com/user-attachments/files/28289471/blackbox-output-code-SXY7QEPXLG.html)
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEGA DELIVERY - Admin</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        body { background: #1a1a1a; min-height: 100vh; padding: 15px; }
        .container { max-width: 500px; margin: 0 auto; }
        h1 { color: #fff; text-align: center; margin-bottom: 20px; font-size: 1.6em; text-shadow: 2px 2px 0 #ffd700; }
        h1 span { color: #ffd700; }
        .badge { background: #ff4757; color: #fff; padding: 5px 12px; border-radius: 20px; font-size: 0.7em; display: inline-block; margin-bottom: 10px; }
        .card { background: #2d2d2d; border-radius: 12px; padding: 20px; border: 2px solid #0047ab; margin-bottom: 20px; }
        .card h2 { color: #ffd700; margin-bottom: 15px; font-size: 1.1em; }
        .form-group { margin-bottom: 12px; }
        .form-group label { display: block; color: #ccc; margin-bottom: 5px; font-weight: 600; font-size: 0.9em; }
        .form-group input { width: 100%; padding: 12px; border: 2px solid #404040; border-radius: 8px; font-size: 14px; background: #1a1a1a; color: #fff; }
        .form-group input:focus { outline: none; border-color: #0047ab; }
        .image-preview { width: 100%; height: 150px; background: #1a1a1a; border-radius: 8px; border: 2px dashed #404040; display: flex; align-items: center; justify-content: center; margin-bottom: 12px; overflow: hidden; cursor: pointer; }
        .image-preview img { width: 100%; height: 100%; object-fit: cover; }
        .image-preview span { color: #666; }
        .btn { width: 100%; padding: 15px; border: none; border-radius: 8px; font-size: 1em; font-weight: bold; cursor: pointer; text-transform: uppercase; }
        .btn-add { background: linear-gradient(135deg, #ffd700, #ffb700); color: #000; }
        .lista { margin-top: 20px; }
        .item { background: #2d2d2d; border-radius: 10px; padding: 15px; margin-bottom: 10px; display: flex; align-items: center; gap: 12px; border: 1px solid #404040; }
        .item img { width: 60px; height: 60px; border-radius: 8px; object-fit: cover; }
        .item-info { flex: 1; }
        .item-info h3 { color: #fff; font-size: 1em; }
        .item-info p { color: #888; font-size: 0.85em; }
        .btn-excluir { background: #ff4757; color: #fff; border: none; padding: 8px 12px; border-radius: 6px; cursor: pointer; }
        .msg { text-align: center; padding: 10px; border-radius: 8px; margin-bottom: 10px; display: none; }
        .msg-sucesso { background: #25d366; color: #fff; }
        .msg-erro { background: #ff4757; color: #fff; }
    </style>
</head>
<body>
    <div class="container">
        <div style="text-align: center;"><span class="badge">🔧 MODO ADMIN</span></div>
        <h1>🛵 MEGA <span>DELIVERY</span></h1>
        
        <div id="msg" class="msg"></div>

        <div class="card">
            <h2>➕ Adicionar Estabelecimento</h2>
            <div class="form-group">
                <label>Foto</label>
                <div class="image-preview" id="preview" onclick="document.getElementById('foto').click()">
                    <span>Clique para adicionar foto</span>
                </div>
                <input type="file" id="foto" accept="image/*" style="display: none;" onchange="previewImage()">
            </div>
            <div class="form-group">
                <label>Nome</label>
                <input type="text" id="nome" value="Doralice pasteis" placeholder="Ex: Lanches Mega">
            </div>
            <div class="form-group">
                <label>📞 Telefone</label>
                <input type="tel" id="telefone" value="(63) 9929-4772" placeholder="(11) 99999-9999">
            </div>
            <div class="form-group">
                <label>💬 WhatsApp</label>
                <input type="tel" id="whatsapp" value="556399294772" placeholder="5511999999999">
            </div>
            <div class="form-group">
                <label>📧 Email</label>
                <input type="email" id="email" placeholder="contato@email.com">
            </div>
            <div class="form-group">
                <label>📍 Endereço</label>
                <input type="text" id="endereco" placeholder="Rua exemplo, 123">
            </div>
            <button class="btn btn-add" onclick="adicionar()">✅ Adicionar</button>
        </div>

        <div class="card">
            <h2>📋 Cadastrados</h2>
            <div id="lista"></div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/12.13.0/firebase-app.js";
        import { getDatabase, ref, set, onValue, remove } from "https://www.gstatic.com/firebasejs/12.13.0/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyB-UQnZzky1OuBG2XuCwJ_vCrX0pWnM1tk",
            authDomain: "mega-delivery-49d6b.firebaseapp.com",
            projectId: "mega-delivery-49d6b",
            storageBucket: "mega-delivery-49d6b.firebasestorage.app",
            messagingSenderId: "1060401627051",
            appId: "1:1060401627051:web:261f9d0f01e8b0b2e81cee"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);

        let imagemBase64 = '';

        window.previewImage = function() {
            const input = document.getElementById('foto');
            const preview = document.getElementById('preview');
            if (input.files && input.files[0]) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    imagemBase64 = e.target.result;
                    preview.innerHTML = `<img src="${e.target.result}">`;
                };
                reader.readAsDataURL(input.files[0]);
            }
        };

        window.adicionar = function() {
            const nome = document.getElementById('nome').value;
            const telefone = document.getElementById('telefone').value;
            const whatsapp = document.getElementById('whatsapp').value;
            const email = document.getElementById('email').value;
            const endereco = document.getElementById('endereco').value;

            if (!nome) {
                mostrarMsg('Digite o nome!', 'erro');
                return;
            }

            const id = Date.now();
            const novo = { id, foto: imagemBase64, nome, telefone, whatsapp, email, endereco };

            set(ref(db, 'estabelecimentos/' + id), novo).then(() => {
                mostrarMsg('Adicionado com sucesso!', 'sucesso');
                document.getElementById('nome').value = '';
                document.getElementById('telefone').value = '';
                document.getElementById('whatsapp').value = '';
                document.getElementById('email').value = '';
                document.getElementById('endereco').value = '';
                document.getElementById('preview').innerHTML = '<span>Clique para adicionar foto</span>';
                imagemBase64 = '';
            }).catch(() => {
                mostrarMsg('Erro ao adicionar!', 'erro');
            });
        };

        window.excluir = function(id) {
            if (confirm('Excluir?')) {
                remove(ref(db, 'estabelecimentos/' + id));
