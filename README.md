<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Unidade Escolar Genézia Arraes - Anexo - SESC</title>
  <script src="https://cdn.tailwindcss.com">
function importarMesclandoDados() {
  if (!dadosBackup) {
    mostrarToast('Selecione um arquivo de backup válido!', 'error');
    return;
  }

  document.getElementById('mensagemConfirmacao').textContent = 'Esta ação irá mesclar os dados do backup com os dados atuais. Nenhum dado será apagado. Deseja continuar?';

  const botaoConfirmar = document.getElementById('botaoConfirmar');
  botaoConfirmar.className = 'bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700';
  botaoConfirmar.onclick = confirmarImportacaoMesclado;

  document.getElementById('modalConfirmacao').classList.remove('hidden');
}

function confirmarImportacaoMesclado() {
  if (!dadosBackup) {
    mostrarToast('Backup inválido!', 'error');
    return;
  }

  const nomesDisciplinasExistentes = new Set(disciplinas);
  dadosBackup.disciplinas.forEach(d => {
    if (!nomesDisciplinasExistentes.has(d)) {
      disciplinas.push(d);
    }
  });

  const idsTurmasExistentes = new Set(turmas.map(t => t.id));
  dadosBackup.turmas.forEach(t => {
    if (!idsTurmasExistentes.has(t.id)) {
      turmas.push(t);
    }
  });

  const idsAlunosExistentes = new Set(alunos.map(a => a.id));
  dadosBackup.alunos.forEach(a => {
    if (!idsAlunosExistentes.has(a.id)) {
      alunos.push(a);
    }
  });

  for (const [alunoId, notasAluno] of Object.entries(dadosBackup.notas)) {
    if (!notas[alunoId]) {
      notas[alunoId] = notasAluno;
    } else {
      for (const [disciplina, valores] of Object.entries(notasAluno)) {
        notas[alunoId][disciplina] = valores;
      }
    }
  }

  localStorage.setItem('disciplinas', JSON.stringify(disciplinas));
  localStorage.setItem('turmas', JSON.stringify(turmas));
  localStorage.setItem('alunos', JSON.stringify(alunos));
  localStorage.setItem('notas', JSON.stringify(notas));

  atualizarListaDisciplinas();
  atualizarListaTurmas();
  atualizarSelectTurmas();
  atualizarEstatisticas();

  dadosBackup = null;
  document.getElementById('arquivoBackup').value = '';
  document.getElementById('infoArquivoBackup').classList.add('hidden');
  fecharModalConfirmacao();

  mostrarToast('Backup mesclado com sucesso!', 'success');
}


function lerArquivosBackup() {
  const input = document.getElementById('arquivoBackup');
  const arquivos = input.files;

  if (!arquivos || arquivos.length === 0) {
    return;
  }

  let arquivosLidos = 0;
  dadosBackup = { disciplinas: [], turmas: [], alunos: [], notas: {} };

  Array.from(arquivos).forEach(arquivo => {
    const reader = new FileReader();
    reader.onload = function(e) {
      try {
        const dados = JSON.parse(e.target.result);

        if (!dados.disciplinas || !dados.turmas || !dados.alunos || !dados.notas) {
          mostrarToast('Arquivo de backup inválido: ' + arquivo.name, 'error');
          return;
        }

        dadosBackup.disciplinas.push(...dados.disciplinas);
        dadosBackup.turmas.push(...dados.turmas);
        dadosBackup.alunos.push(...dados.alunos);
        for (const [alunoId, notasAluno] of Object.entries(dados.notas)) {
          dadosBackup.notas[alunoId] = notasAluno;
        }

        arquivosLidos++;
        if (arquivosLidos === arquivos.length) {
          document.getElementById('nomeArquivoBackup').textContent = arquivos.length + ' arquivo(s) selecionado(s)';
          document.getElementById('infoBackupTurmas').textContent = `Turmas: ${dadosBackup.turmas.length}`;
          document.getElementById('infoBackupAlunos').textContent = `Alunos: ${dadosBackup.alunos.length}`;
          document.getElementById('infoBackupDisciplinas').textContent = `Disciplinas: ${dadosBackup.disciplinas.length}`;
          document.getElementById('infoArquivoBackup').classList.remove('hidden');
        }

      } catch (error) {
        mostrarToast('Erro ao ler o arquivo de backup: ' + arquivo.name, 'error');
        console.error(error);
      }
    };

    reader.readAsText(arquivo);
  });
}


function salvarNotasBoletim() {
  const inputs = document.querySelectorAll('.boletim-nota-input');
  let alteradas = 0;

  inputs.forEach(input => {
    const alunoId = input.dataset.id;
    const disciplina = input.dataset.d;
    const bimestre = parseInt(input.dataset.b);
    const valor = input.value.trim() ? parseFloat(input.value) : '';

    if (!notas[alunoId]) notas[alunoId] = {};
    if (!notas[alunoId][disciplina]) notas[alunoId][disciplina] = ['', '', '', '', '', '', '', ''];

    notas[alunoId][disciplina][bimestre] = valor;
    alteradas++;
  });

  localStorage.setItem('notas', JSON.stringify(notas));
  mostrarToast(`${alteradas} nota(s) salvas diretamente no boletim!`, 'success');
}

</script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js">
function importarMesclandoDados() {
  if (!dadosBackup) {
    mostrarToast('Selecione um arquivo de backup válido!', 'error');
    return;
  }

  document.getElementById('mensagemConfirmacao').textContent = 'Esta ação irá mesclar os dados do backup com os dados atuais. Nenhum dado será apagado. Deseja continuar?';

  const botaoConfirmar = document.getElementById('botaoConfirmar');
  botaoConfirmar.className = 'bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700';
  botaoConfirmar.onclick = confirmarImportacaoMesclado;

  document.getElementById('modalConfirmacao').classList.remove('hidden');
}

function confirmarImportacaoMesclado() {
  if (!dadosBackup) {
    mostrarToast('Backup inválido!', 'error');
    return;
  }

  const nomesDisciplinasExistentes = new Set(disciplinas);
  dadosBackup.disciplinas.forEach(d => {
    if (!nomesDisciplinasExistentes.has(d)) {
      disciplinas.push(d);
    }
  });

  const idsTurmasExistentes = new Set(turmas.map(t => t.id));
  dadosBackup.turmas.forEach(t => {
    if (!idsTurmasExistentes.has(t.id)) {
      turmas.push(t);
    }
  });

  const idsAlunosExistentes = new Set(alunos.map(a => a.id));
  dadosBackup.alunos.forEach(a => {
    if (!idsAlunosExistentes.has(a.id)) {
      alunos.push(a);
    }
  });

  for (const [alunoId, notasAluno] of Object.entries(dadosBackup.notas)) {
    if (!notas[alunoId]) {
      notas[alunoId] = notasAluno;
    } else {
      for (const [disciplina, valores] of Object.entries(notasAluno)) {
        notas[alunoId][disciplina] = valores;
      }
    }
  }

  localStorage.setItem('disciplinas', JSON.stringify(disciplinas));
  localStorage.setItem('turmas', JSON.stringify(turmas));
  localStorage.setItem('alunos', JSON.stringify(alunos));
  localStorage.setItem('notas', JSON.stringify(notas));

  atualizarListaDisciplinas();
  atualizarListaTurmas();
  atualizarSelectTurmas();
  atualizarEstatisticas();

  dadosBackup = null;
  document.getElementById('arquivoBackup').value = '';
  document.getElementById('infoArquivoBackup').classList.add('hidden');
  fecharModalConfirmacao();

  mostrarToast('Backup mesclado com sucesso!', 'success');
}


function lerArquivosBackup() {
  const input = document.getElementById('arquivoBackup');
  const arquivos = input.files;

  if (!arquivos || arquivos.length === 0) {
    return;
  }

  let arquivosLidos = 0;
  dadosBackup = { disciplinas: [], turmas: [], alunos: [], notas: {} };

  Array.from(arquivos).forEach(arquivo => {
    const reader = new FileReader();
    reader.onload = function(e) {
      try {
        const dados = JSON.parse(e.target.result);

        if (!dados.disciplinas || !dados.turmas || !dados.alunos || !dados.notas) {
          mostrarToast('Arquivo de backup inválido: ' + arquivo.name, 'error');
          return;
        }

        dadosBackup.disciplinas.push(...dados.disciplinas);
        dadosBackup.turmas.push(...dados.turmas);
        dadosBackup.alunos.push(...dados.alunos);
        for (const [alunoId, notasAluno] of Object.entries(dados.notas)) {
          dadosBackup.notas[alunoId] = notasAluno;
        }

        arquivosLidos++;
        if (arquivosLidos === arquivos.length) {
          document.getElementById('nomeArquivoBackup').textContent = arquivos.length + ' arquivo(s) selecionado(s)';
          document.getElementById('infoBackupTurmas').textContent = `Turmas: ${dadosBackup.turmas.length}`;
          document.getElementById('infoBackupAlunos').textContent = `Alunos: ${dadosBackup.alunos.length}`;
          document.getElementById('infoBackupDisciplinas').textContent = `Disciplinas: ${dadosBackup.disciplinas.length}`;
          document.getElementById('infoArquivoBackup').classList.remove('hidden');
        }

      } catch (error) {
        mostrarToast('Erro ao ler o arquivo de backup: ' + arquivo.name, 'error');
        console.error(error);
      }
    };

    reader.readAsText(arquivo);
  });
}


function salvarNotasBoletim() {
  const inputs = document.querySelectorAll('.boletim-nota-input');
  let alteradas = 0;

  inputs.forEach(input => {
    const alunoId = input.dataset.id;
    const disciplina = input.dataset.d;
    const bimestre = parseInt(input.dataset.b);
    const valor = input.value.trim() ? parseFloat(input.value) : '';

    if (!notas[alunoId]) notas[alunoId] = {};
    if (!notas[alunoId][disciplina]) notas[alunoId][disciplina] = ['', '', '', '', '', '', '', ''];

    notas[alunoId][disciplina][bimestre] = valor;
    alteradas++;
  });

  localStorage.setItem('notas', JSON.stringify(notas));
  mostrarToast(`${alteradas} nota(s) salvas diretamente no boletim!`, 'success');
}

</script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js">
function importarMesclandoDados() {
  if (!dadosBackup) {
    mostrarToast('Selecione um arquivo de backup válido!', 'error');
    return;
  }

  document.getElementById('mensagemConfirmacao').textContent = 'Esta ação irá mesclar os dados do backup com os dados atuais. Nenhum dado será apagado. Deseja continuar?';

  const botaoConfirmar = document.getElementById('botaoConfirmar');
  botaoConfirmar.className = 'bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700';
  botaoConfirmar.onclick = confirmarImportacaoMesclado;

  document.getElementById('modalConfirmacao').classList.remove('hidden');
}

function confirmarImportacaoMesclado() {
  if (!dadosBackup) {
    mostrarToast('Backup inválido!', 'error');
    return;
  }

  const nomesDisciplinasExistentes = new Set(disciplinas);
  dadosBackup.disciplinas.forEach(d => {
    if (!nomesDisciplinasExistentes.has(d)) {
      disciplinas.push(d);
    }
  });

  const idsTurmasExistentes = new Set(turmas.map(t => t.id));
  dadosBackup.turmas.forEach(t => {
    if (!idsTurmasExistentes.has(t.id)) {
      turmas.push(t);
    }
  });

  const idsAlunosExistentes = new Set(alunos.map(a => a.id));
  dadosBackup.alunos.forEach(a => {
    if (!idsAlunosExistentes.has(a.id)) {
      alunos.push(a);
    }
  });

  for (const [alunoId, notasAluno] of Object.entries(dadosBackup.notas)) {
    if (!notas[alunoId]) {
      notas[alunoId] = notasAluno;
    } else {
      for (const [disciplina, valores] of Object.entries(notasAluno)) {
        notas[alunoId][disciplina] = valores;
      }
    }
  }

  localStorage.setItem('disciplinas', JSON.stringify(disciplinas));
  localStorage.setItem('turmas', JSON.stringify(turmas));
  localStorage.setItem('alunos', JSON.stringify(alunos));
  localStorage.setItem('notas', JSON.stringify(notas));

  atualizarListaDisciplinas();
  atualizarListaTurmas();
  atualizarSelectTurmas();
  atualizarEstatisticas();

  dadosBackup = null;
  document.getElementById('arquivoBackup').value = '';
  document.getElementById('infoArquivoBackup').classList.add('hidden');
  fecharModalConfirmacao();

  mostrarToast('Backup mesclado com sucesso!', 'success');
}


function lerArquivosBackup() {
  const input = document.getElementById('arquivoBackup');
  const arquivos = input.files;

  if (!arquivos || arquivos.length === 0) {
    return;
  }

  let arquivosLidos = 0;
  dadosBackup = { disciplinas: [], turmas: [], alunos: [], notas: {} };

  Array.from(arquivos).forEach(arquivo => {
    const reader = new FileReader();
    reader.onload = function(e) {
      try {
        const dados = JSON.parse(e.target.result);

        if (!dados.disciplinas || !dados.turmas || !dados.alunos || !dados.notas) {
          mostrarToast('Arquivo de backup inválido: ' + arquivo.name, 'error');
          return;
        }

        dadosBackup.disciplinas.push(...dados.disciplinas);
        dadosBackup.turmas.push(...dados.turmas);
        dadosBackup.alunos.push(...dados.alunos);
        for (const [alunoId, notasAluno] of Object.entries(dados.notas)) {
          dadosBackup.notas[alunoId] = notasAluno;
        }

        arquivosLidos++;
        if (arquivosLidos === arquivos.length) {
          document.getElementById('nomeArquivoBackup').textContent = arquivos.length + ' arquivo(s) selecionado(s)';
          document.getElementById('infoBackupTurmas').textContent = `Turmas: ${dadosBackup.turmas.length}`;
          document.getElementById('infoBackupAlunos').textContent = `Alunos: ${dadosBackup.alunos.length}`;
          document.getElementById('infoBackupDisciplinas').textContent = `Disciplinas: ${dadosBackup.disciplinas.length}`;
          document.getElementById('infoArquivoBackup').classList.remove('hidden');
        }

      } catch (error) {
        mostrarToast('Erro ao ler o arquivo de backup: ' + arquivo.name, 'error');
        console.error(error);
      }
    };

    reader.readAsText(arquivo);
  });
}


function salvarNotasBoletim() {
  const inputs = document.querySelectorAll('.boletim-nota-input');
  let alteradas = 0;

  inputs.forEach(input => {
    const alunoId = input.dataset.id;
    const disciplina = input.dataset.d;
    const bimestre = parseInt(input.dataset.b);
    const valor = input.value.trim() ? parseFloat(input.value) : '';

    if (!notas[alunoId]) notas[alunoId] = {};
    if (!notas[alunoId][disciplina]) notas[alunoId][disciplina] = ['', '', '', '', '', '', '', ''];

    notas[alunoId][disciplina][bimestre] = valor;
    alteradas++;
  });

  localStorage.setItem('notas', JSON.stringify(notas));
  mostrarToast(`${alteradas} nota(s) salvas diretamente no boletim!`, 'success');
}

</script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap');
    
    body {
      font-family: 'Roboto', sans-serif;
      background-color: #f0f4f8;
    }
    
    .card {
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
      transition: all 0.3s ease;
    }
    
    .card:hover {
      box-shadow: 0 10px 15px rgba(0, 0, 0, 0.1);
    }
    
    .btn-primary {
      transition: all 0.3s ease;
    }
    
    .btn-primary:hover {
      transform: translateY(-2px);
    }
    
    .school-header {
      background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
    }
    
    @media print {
      .no-print {
        display: none;
      }
      
      .print-only {
        display: block;
      }
    }

    .toast {
      position: fixed;
      bottom: 20px;
      right: 20px;
      padding: 12px 20px;
      border-radius: 4px;
      color: white;
      opacity: 0;
      transition: opacity 0.3s ease;
      z-index: 1000;
    }

    .toast.show {
      opacity: 1;
    }

    .tab-active {
      border-bottom: 3px solid #3b82f6;
    }

    .modal {
      transition: opacity 0.3s ease;
    }
  </style>
</head>
<body class="p-4 md:p-6">
  <div class="container mx-auto max-w-6xl">
    <div class="school-header text-white p-6 rounded-lg mb-6">
      <h1 class="text-center text-2xl md:text-3xl font-bold">Unidade Escolar Genézia Arraes - Anexo - SESC</h1>
      <p class="text-center text-sm md:text-base mt-2">Sistema de Gestão Acadêmica</p>
      <div class="text-center text-sm mt-3 opacity-90">
        <p>Travessa Santo Antônio, 749 - Bairro: Barro Vermelho</p>
        <p>São João do Piauí-PI / Contato: 89 9.9410-8800</p>
      </div>
    </div>

    <!-- Tabs de navegação -->
    <div class="flex flex-wrap mb-6 bg-white rounded-lg shadow overflow-hidden">
      <button id="tab-cadastros" class="tab-active py-3 px-6 font-medium text-gray-700 focus:outline-none" onclick="changeTab('cadastros')">Cadastros</button>
      <button id="tab-relatorios" class="py-3 px-6 font-medium text-gray-700 focus:outline-none" onclick="changeTab('relatorios')">Relatórios</button>
      <button id="tab-boletins" class="py-3 px-6 font-medium text-gray-700 focus:outline-none" onclick="changeTab('boletins')">Boletins</button>
      <button id="tab-backup" class="py-3 px-6 font-medium text-gray-700 focus:outline-none" onclick="changeTab('backup')">Backup</button>
    </div>

    <!-- Seção de Cadastros -->
    <div id="section-cadastros" class="grid grid-cols-1 md:grid-cols-2 gap-6">
      <!-- Cadastro de Disciplinas -->
      <div class="card bg-white rounded-lg overflow-hidden">
        <div class="bg-blue-600 text-white p-4 flex justify-between items-center">
          <h2 class="font-bold">Cadastro de Disciplinas</h2>
          <button class="text-white text-sm bg-blue-700 px-2 py-1 rounded hover:bg-blue-800" onclick="toggleCadastroMassaDisciplinas()">Cadastro em Massa</button>
        </div>
        <div id="cadastroIndividualDisciplina" class="p-4">
          <div class="flex gap-2 mb-4">
            <input type="text" id="disciplinaInput" class="flex-1 border rounded p-2" placeholder="Nome da Disciplina">
            <button class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700" onclick="cadastrarDisciplina()">Adicionar</button>
          </div>
          <ul id="listaDisciplinas" class="divide-y"></ul>
        </div>
        <div id="cadastroMassaDisciplina" class="p-4 hidden">
          <textarea class="w-full border rounded p-2 mb-3" id="disciplinasInput" rows="6" placeholder="Digite uma disciplina por linha"></textarea>
          <div class="flex gap-2">
            <button class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 flex-1" onclick="cadastrarDisciplinasEmMassa()">Cadastrar Disciplinas</button>
            <button class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600" onclick="toggleCadastroMassaDisciplinas()">Voltar</button>
          </div>
        </div>
      </div>

      <!-- Cadastro de Turma -->
      <div class="card bg-white rounded-lg overflow-hidden">
        <div class="bg-green-600 text-white p-4 flex justify-between items-center">
          <h2 class="font-bold">Cadastro de Turma</h2>
          <button class="text-white text-sm bg-green-700 px-2 py-1 rounded hover:bg-green-800" onclick="toggleCadastroMassaTurmas()">Cadastro em Massa</button>
        </div>
        <div id="cadastroIndividualTurma" class="p-4">
          <input type="text" id="turmaInput" class="w-full border rounded p-2 mb-3" placeholder="Nome da Turma">
          <input type="text" id="serieInput" class="w-full border rounded p-2 mb-3" placeholder="Série">
          <button class="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700 w-full" onclick="cadastrarTurma()">Cadastrar Turma</button>
          <div class="mt-4">
            <h3 class="font-medium mb-2">Turmas Cadastradas:</h3>
            <ul id="listaTurmas" class="divide-y"></ul>
          </div>
        </div>
        <div id="cadastroMassaTurma" class="p-4 hidden">
          <p class="text-sm text-gray-600 mb-2">Formato: Nome da Turma | Série</p>
          <textarea class="w-full border rounded p-2 mb-3" id="turmasInput" rows="6" placeholder="Exemplo:&#10;1º Ano A | 1º Ano do Ensino Médio&#10;2º Ano B | 2º Ano do Ensino Médio"></textarea>
          <div class="flex gap-2">
            <button class="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700 flex-1" onclick="cadastrarTurmasEmMassa()">Cadastrar Turmas</button>
            <button class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600" onclick="toggleCadastroMassaTurmas()">Voltar</button>
          </div>
        </div>
      </div>

      <!-- Cadastro em Massa de Alunos -->
      <div class="card bg-white rounded-lg overflow-hidden">
        <div class="bg-purple-600 text-white p-4">
          <h2 class="font-bold">Cadastro em Massa de Alunos</h2>
        </div>
        <div class="p-4">
          <select class="w-full border rounded p-2 mb-3" id="turmaSelecionada">
            <option value="" disabled selected>Selecione uma turma</option>
          </select>
          <textarea class="w-full border rounded p-2 mb-3" id="alunosInput" rows="4" placeholder="Digite um aluno por linha"></textarea>
          <button class="bg-purple-600 text-white px-4 py-2 rounded hover:bg-purple-700 w-full" onclick="cadastrarAlunos()">Cadastrar Alunos</button>
        </div>
      </div>

      <!-- Gerenciamento de Alunos -->
      <div class="card bg-white rounded-lg overflow-hidden">
        <div class="bg-amber-600 text-white p-4">
          <h2 class="font-bold">Gerenciamento de Alunos</h2>
        </div>
        <div class="p-4">
          <select class="w-full border rounded p-2 mb-3" id="turmaGerenciamento">
            <option value="" disabled selected>Selecione uma turma</option>
          </select>
          <div class="mt-2 max-h-64 overflow-y-auto">
            <ul id="listaAlunos" class="divide-y"></ul>
          </div>
        </div>
      </div>
    </div>

    <!-- Seção de Relatórios -->
    <div id="section-relatorios" class="grid grid-cols-1 md:grid-cols-2 gap-6 hidden">
      <!-- Relatório de Alunos por Turma -->
      <div class="card bg-white rounded-lg overflow-hidden">
        <div class="bg-indigo-600 text-white p-4">
          <h2 class="font-bold">Relatório de Alunos por Turma</h2>
        </div>
        <div class="p-4">
          <select class="w-full border rounded p-2 mb-3" id="turmaRelatorio">
            <option value="" disabled selected>Selecione uma turma</option>
          </select>
          <div id="relatorioAlunos" class="mt-4">
            <div class="flex justify-between font-medium border-b pb-2">
              <span>Nome do Aluno</span>
              <span>Ações</span>
            </div>
            <ul id="listaAlunosRelatorio" class="divide-y"></ul>
          </div>
          <button class="bg-indigo-600 text-white px-4 py-2 rounded hover:bg-indigo-700 w-full mt-4" onclick="gerarPDFRelatorio()">Gerar PDF</button>
        </div>
      </div>

      <!-- Estatísticas Gerais -->
      <div class="card bg-white rounded-lg overflow-hidden">
        <div class="bg-teal-600 text-white p-4">
          <h2 class="font-bold">Estatísticas Gerais</h2>
        </div>
        <div class="p-4">
          <div class="grid grid-cols-2 gap-4">
            <div class="bg-gray-100 p-4 rounded-lg text-center">
              <p class="text-gray-600">Total de Turmas</p>
              <p id="totalTurmas" class="text-2xl font-bold text-teal-600">0</p>
            </div>
            <div class="bg-gray-100 p-4 rounded-lg text-center">
              <p class="text-gray-600">Total de Alunos</p>
              <p id="totalAlunos" class="text-2xl font-bold text-purple-600">0</p>
            </div>
            <div class="bg-gray-100 p-4 rounded-lg text-center">
              <p class="text-gray-600">Total de Disciplinas</p>
              <p id="totalDisciplinas" class="text-2xl font-bold text-blue-600">0</p>
            </div>
            <div class="bg-gray-100 p-4 rounded-lg text-center">
              <p class="text-gray-600">Média de Alunos/Turma</p>
              <p id="mediaAlunosTurma" class="text-2xl font-bold text-amber-600">0</p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Seção de Boletins -->
    <div id="section-boletins" class="grid grid-cols-1 gap-6 hidden">
      <!-- Geração de Boletim -->
      <div class="card bg-white rounded-lg overflow-hidden">
        <div class="bg-rose-600 text-white p-4">
          <h2 class="font-bold">Geração de Boletim</h2>
        </div>
        <div class="p-4">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Turma</label>
              <select class="w-full border rounded p-2" id="turmaBoletim">
                <option value="" disabled selected>Selecione uma turma</option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Aluno</label>
              <select class="w-full border rounded p-2" id="alunoBoletim">
                <option value="" disabled selected>Selecione um aluno</option>
              </select>
            </div>
          </div>
          
          
<div id="boletimContainer" class="border rounded-lg p-2 mb-4 hidden text-sm">
  <div id="boletimHeader" class="text-center mb-4">
    <h3 class="text-xl font-bold">Unidade Escolar Genézia Arraes - Anexo - SESC</h3>
    <p class="text-sm text-gray-600 mt-1">Travessa Santo Antônio, 749 - Bairro: Barro Vermelho</p>
    <p class="text-sm text-gray-600">São João do Piauí-PI / Contato: 89 9.9410-8800</p>
    <p class="text-gray-600 mt-3">Boletim Escolar</p>
    <div class="flex justify-between mt-4">
      <p><span class="font-medium">Aluno:</span> <span id="boletimNomeAluno"></span></p>
      <p><span class="font-medium">Turma:</span> <span id="boletimTurma"></span></p>
    </div>
    <p class="text-right"><span class="font-medium">Série:</span> <span id="boletimSerie"></span></p>
  </div>

  <div class="overflow-x-auto">
    <table class="w-full border-collapse text-sm leading-tight">
      <thead>
        <tr class="bg-gray-100">
          <th class="border p-2">Disciplina</th>
          <th class="border p-2">1º Bim N1</th>
          <th class="border p-2">1º Bim N2</th>
          <th class="border p-2">2º Bim N1</th>
          <th class="border p-2">2º Bim N2</th>
          <th class="border p-2">Média</th>
          <th class="border p-2">Recuperação</th>
<th class="border p-2">Status</th>
        </tr>
      </thead>
      <tbody id="boletimNotas">
        <!-- Conteúdo gerado dinamicamente via JS -->
      </tbody>
    </table>
  </div>

  <div class="mt-6">
    <p class="font-medium">Observações:</p>
    <p class="text-gray-600 text-sm">Média para aprovação: 6,0 | Frequência mínima: 75%</p>
  </div>
</div>

<div class="flex gap-4">
            <button class="bg-rose-600 text-white px-4 py-2 rounded hover:bg-rose-700 flex-1" onclick="visualizarBoletim()">Visualizar Boletim</button>
            <button class="bg-gray-600 text-white px-4 py-2 rounded hover:bg-gray-700 flex-1" onclick="gerarPDFBoletim()">Gerar PDF</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Seção de Backup -->
    <div id="section-backup" class="grid grid-cols-1 gap-6 hidden">
      <!-- Backup e Restauração -->
      <div class="card bg-white rounded-lg overflow-hidden">
        <div class="bg-gray-800 text-white p-4">
          <h2 class="font-bold">Backup e Restauração de Dados</h2>
        </div>
        <div class="p-4">
          <div class="mb-6">
            <h3 class="font-medium mb-2">Exportar Dados</h3>
            <p class="text-sm text-gray-600 mb-4">Faça o backup de todos os dados do sistema (turmas, alunos, disciplinas e notas).</p>
            <button class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 w-full" onclick="exportarDados()">
              <div class="flex items-center justify-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
                  <path fill-rule="evenodd" d="M3 17a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zm3.293-7.707a1 1 0 011.414 0L9 10.586V3a1 1 0 112 0v7.586l1.293-1.293a1 1 0 111.414 1.414l-3 3a1 1 0 01-1.414 0l-3-3a1 1 0 010-1.414z" clip-rule="evenodd" />
                </svg>
                Exportar Backup
              </div>
            </button>
          </div>
          
          <div class="mb-6">
            <h3 class="font-medium mb-2">Importar Dados</h3>
            <p class="text-sm text-gray-600 mb-4">Restaure os dados a partir de um arquivo de backup.</p>
            <div class="mb-3">
              <input type="file" id="arquivoBackup" class="hidden" accept=".json" multiple onchange="lerArquivosBackup()">
              <label for="arquivoBackup" class="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700 w-full flex items-center justify-center cursor-pointer">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
                  <path fill-rule="evenodd" d="M3 17a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zm3.293-7.707a1 1 0 011.414 0L9 10.586V3a1 1 0 112 0v7.586l1.293-1.293a1 1 0 111.414 1.414l-3 3a1 1 0 01-1.414 0l-3-3a1 1 0 010-1.414z" clip-rule="evenodd" transform="rotate(180 10 10)" />
                </svg>
                Selecionar Arquivo de Backup
              </label>
            </div>
            <div id="infoArquivoBackup" class="hidden">
              <div class="bg-gray-100 p-3 rounded mb-3">
                <p class="font-medium" id="nomeArquivoBackup">arquivo.json</p>
                <div class="text-sm text-gray-600 mt-1">
                  <p id="infoBackupTurmas">Turmas: 0</p>
                  <p id="infoBackupAlunos">Alunos: 0</p>
                  <p id="infoBackupDisciplinas">Disciplinas: 0</p>
                </div>
              </div>
              <button class="bg-amber-600 text-white px-4 py-2 rounded hover:bg-amber-700 w-full" onclick="importarDados()">
                <div class="flex items-center justify-center">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M4 2a1 1 0 011 1v2.101a7.002 7.002 0 0111.601 2.566 1 1 0 11-1.885.666A5.002 5.002 0 005.999 7H9a1 1 0 010 2H4a1 1 0 01-1-1V3a1 1 0 011-1zm.008 9.057a1 1 0 011.276.61A5.002 5.002 0 0014.001 13H11a1 1 0 110-2h5a1 1 0 011 1v5a1 1 0 11-2 0v-2.101a7.002 7.002 0 01-11.601-2.566 1 1 0 01.61-1.276z" clip-rule="evenodd" />
                  </svg>
                  Restaurar Dados
                </div>
              </button>
<button class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 w-full mt-2" onclick="importarMesclandoDados()">
  <div class="flex items-center justify-center">
    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
      <path d="M10 2a8 8 0 100 16 8 8 0 000-16zm1 11H9v-1h2v1zm0-2H9V5h2v6z" />
    </svg>
    Importar e Mesclar Dados
  </div>
</button>

            </div>
          </div>
          
          <div>
            <h3 class="font-medium mb-2">Limpar Dados</h3>
            <p class="text-sm text-gray-600 mb-4">Atenção: Esta ação irá apagar todos os dados do sistema.</p>
            <button class="bg-red-600 text-white px-4 py-2 rounded hover:bg-red-700 w-full" onclick="confirmarLimparDados()">
              <div class="flex items-center justify-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
                  <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z" clip-rule="evenodd" />
                </svg>
                Limpar Todos os Dados
              </div>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal para edição de notas -->
  <div id="modalNotas" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center hidden modal">
    <div class="bg-white rounded-lg p-6 w-full max-w-md">
      <h3 class="text-xl font-bold mb-4">Editar Notas - <span id="modalAlunoNome"></span></h3>
      <div id="modalDisciplinas" class="space-y-4 max-h-96 overflow-y-auto">
        <!-- Disciplinas e notas serão inseridas aqui -->
      </div>
      <div class="flex justify-end gap-2 mt-6">
        <button class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600" onclick="fecharModalNotas()">Cancelar</button>
        <button class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700" onclick="salvarNotas()">Salvar</button>
      </div>
    </div>
  </div>

  <!-- Modal de confirmação -->
  <div id="modalConfirmacao" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center hidden modal">
    <div class="bg-white rounded-lg p-6 w-full max-w-md">
      <h3 class="text-xl font-bold mb-2">Confirmação</h3>
      <p id="mensagemConfirmacao" class="text-gray-700 mb-6">Tem certeza que deseja realizar esta ação?</p>
      <div class="flex justify-end gap-2">
        <button class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600" onclick="fecharModalConfirmacao()">Cancelar</button>
        <button id="botaoConfirmar" class="bg-red-600 text-white px-4 py-2 rounded hover:bg-red-700">Confirmar</button>
      </div>
    </div>
  </div>

  <!-- Toast para notificações -->
  <div id="toast" class="toast bg-green-500">Operação realizada com sucesso!</div>

  <script>
    // Armazenamento de dados
    let disciplinas = JSON.parse(localStorage.getItem('disciplinas')) || [];
    let turmas = JSON.parse(localStorage.getItem('turmas')) || [];
    let alunos = JSON.parse(localStorage.getItem('alunos')) || [];
    let notas = JSON.parse(localStorage.getItem('notas')) || {};
    
    // Variáveis temporárias
    let alunoEditando = null;
    let dadosBackup = null;
    
    // Inicialização
    document.addEventListener('DOMContentLoaded', function() {
      atualizarListaDisciplinas();
      atualizarListaTurmas();
      atualizarSelectTurmas();
      atualizarEstatisticas();
      
      // Inicializar jsPDF
      window.jsPDF = window.jspdf.jsPDF;
    });
    
    // Funções de navegação por tabs
    function changeTab(tab) {
      // Desativar todas as tabs
      document.getElementById('tab-cadastros').classList.remove('tab-active');
      document.getElementById('tab-relatorios').classList.remove('tab-active');
      document.getElementById('tab-boletins').classList.remove('tab-active');
      document.getElementById('tab-backup').classList.remove('tab-active');
      
      // Esconder todas as seções
      document.getElementById('section-cadastros').classList.add('hidden');
      document.getElementById('section-relatorios').classList.add('hidden');
      document.getElementById('section-boletins').classList.add('hidden');
      document.getElementById('section-backup').classList.add('hidden');
      
      // Ativar a tab selecionada
      document.getElementById('tab-' + tab).classList.add('tab-active');
      document.getElementById('section-' + tab).classList.remove('hidden');
      
      // Atualizar dados específicos da tab
      if (tab === 'relatorios') {
        atualizarSelectTurmasRelatorio();
        atualizarEstatisticas();
      } else if (tab === 'boletins') {
        atualizarSelectTurmasBoletim();
      }
    }
    
    // Funções para alternar entre cadastro individual e em massa
    function toggleCadastroMassaDisciplinas() {
      const cadastroIndividual = document.getElementById('cadastroIndividualDisciplina');
      const cadastroMassa = document.getElementById('cadastroMassaDisciplina');
      
      if (cadastroIndividual.classList.contains('hidden')) {
        cadastroIndividual.classList.remove('hidden');
        cadastroMassa.classList.add('hidden');
      } else {
        cadastroIndividual.classList.add('hidden');
        cadastroMassa.classList.remove('hidden');
      }
    }
    
    function toggleCadastroMassaTurmas() {
      const cadastroIndividual = document.getElementById('cadastroIndividualTurma');
      const cadastroMassa = document.getElementById('cadastroMassaTurma');
      
      if (cadastroIndividual.classList.contains('hidden')) {
        cadastroIndividual.classList.remove('hidden');
        cadastroMassa.classList.add('hidden');
      } else {
        cadastroIndividual.classList.add('hidden');
        cadastroMassa.classList.remove('hidden');
      }
    }
    
    // Funções para disciplinas
    function cadastrarDisciplina() {
      const input = document.getElementById('disciplinaInput');
      const nomeDisciplina = input.value.trim();
      
      if (nomeDisciplina) {
        if (!disciplinas.includes(nomeDisciplina)) {
          disciplinas.push(nomeDisciplina);
          localStorage.setItem('disciplinas', JSON.stringify(disciplinas));
          atualizarListaDisciplinas();
          input.value = '';
          mostrarToast('Disciplina cadastrada com sucesso!', 'success');
          atualizarEstatisticas();
        } else {
          mostrarToast('Esta disciplina já está cadastrada!', 'error');
        }
      } else {
        mostrarToast('Digite o nome da disciplina!', 'error');
      }
    }
    
    function cadastrarDisciplinasEmMassa() {
      const input = document.getElementById('disciplinasInput');
      const textoInput = input.value.trim();
      
      if (!textoInput) {
        mostrarToast('Digite pelo menos uma disciplina!', 'error');
        return;
      }
      
      const novasDisciplinas = textoInput.split('\n').filter(d => d.trim() !== '');
      
      if (novasDisciplinas.length === 0) {
        mostrarToast('Digite pelo menos uma disciplina válida!', 'error');
        return;
      }
      
      let adicionadas = 0;
      let duplicadas = 0;
      
      novasDisciplinas.forEach(nomeDisciplina => {
        nomeDisciplina = nomeDisciplina.trim();
        if (nomeDisciplina && !disciplinas.includes(nomeDisciplina)) {
          disciplinas.push(nomeDisciplina);
          adicionadas++;
        } else if (disciplinas.includes(nomeDisciplina)) {
          duplicadas++;
        }
      });
      
      localStorage.setItem('disciplinas', JSON.stringify(disciplinas));
      atualizarListaDisciplinas();
      input.value = '';
      
      let mensagem = `${adicionadas} disciplina(s) cadastrada(s) com sucesso!`;
      if (duplicadas > 0) {
        mensagem += ` ${duplicadas} disciplina(s) duplicada(s) foram ignoradas.`;
      }
      
      mostrarToast(mensagem, 'success');
      atualizarEstatisticas();
      
      // Voltar para o cadastro individual
      toggleCadastroMassaDisciplinas();
    }
    
    function removerDisciplina(index) {
      disciplinas.splice(index, 1);
      localStorage.setItem('disciplinas', JSON.stringify(disciplinas));
      atualizarListaDisciplinas();
      mostrarToast('Disciplina removida com sucesso!', 'success');
      atualizarEstatisticas();
    }
    
    function atualizarListaDisciplinas() {
      const lista = document.getElementById('listaDisciplinas');
      lista.innerHTML = '';
      
      disciplinas.forEach((disciplina, index) => {
        const li = document.createElement('li');
        li.className = 'py-2 flex justify-between items-center';
        li.innerHTML = `
          <span>${disciplina}</span>
          <button class="text-red-500 hover:text-red-700" onclick="removerDisciplina(${index})">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z" clip-rule="evenodd" />
            </svg>
          </button>
        `;
        lista.appendChild(li);
      });
    }
    
    // Funções para turmas
    function cadastrarTurma() {
      const nomeTurma = document.getElementById('turmaInput').value.trim();
      const serie = document.getElementById('serieInput').value.trim();
      
      if (nomeTurma && serie) {
        const turmaNova = {
          id: Date.now().toString(),
          nome: nomeTurma,
          serie: serie
        };
        
        turmas.push(turmaNova);
        localStorage.setItem('turmas', JSON.stringify(turmas));
        
        document.getElementById('turmaInput').value = '';
        document.getElementById('serieInput').value = '';
        
        atualizarListaTurmas();
        atualizarSelectTurmas();
        mostrarToast('Turma cadastrada com sucesso!', 'success');
        atualizarEstatisticas();
      } else {
        mostrarToast('Preencha todos os campos!', 'error');
      }
    }
    
    function cadastrarTurmasEmMassa() {
      const input = document.getElementById('turmasInput');
      const textoInput = input.value.trim();
      
      if (!textoInput) {
        mostrarToast('Digite pelo menos uma turma!', 'error');
        return;
      }
      
      const linhas = textoInput.split('\n').filter(linha => linha.trim() !== '');
      
      if (linhas.length === 0) {
        mostrarToast('Digite pelo menos uma turma válida!', 'error');
        return;
      }
      
      let adicionadas = 0;
      let invalidas = 0;
      
      linhas.forEach(linha => {
        const partes = linha.split('|');
        
        if (partes.length === 2) {
          const nomeTurma = partes[0].trim();
          const serie = partes[1].trim();
          
          if (nomeTurma && serie) {
            const turmaNova = {
              id: Date.now().toString() + adicionadas,
              nome: nomeTurma,
              serie: serie
            };
            
            turmas.push(turmaNova);
            adicionadas++;
          } else {
            invalidas++;
          }
        } else {
          invalidas++;
        }
      });
      
      localStorage.setItem('turmas', JSON.stringify(turmas));
      atualizarListaTurmas();
      atualizarSelectTurmas();
      input.value = '';
      
      let mensagem = `${adicionadas} turma(s) cadastrada(s) com sucesso!`;
      if (invalidas > 0) {
        mensagem += ` ${invalidas} linha(s) inválida(s) foram ignoradas.`;
      }
      
      mostrarToast(mensagem, 'success');
      atualizarEstatisticas();
      
      // Voltar para o cadastro individual
      toggleCadastroMassaTurmas();
    }
    
    function removerTurma(id) {
      // Remover alunos da turma
      alunos = alunos.filter(aluno => aluno.turmaId !== id);
      localStorage.setItem('alunos', JSON.stringify(alunos));
      
      // Remover turma
      turmas = turmas.filter(turma => turma.id !== id);
      localStorage.setItem('turmas', JSON.stringify(turmas));
      
      atualizarListaTurmas();
      atualizarSelectTurmas();
      mostrarToast('Turma removida com sucesso!', 'success');
      atualizarEstatisticas();
    }
    
    function atualizarListaTurmas() {
      const lista = document.getElementById('listaTurmas');
      lista.innerHTML = '';
      
      turmas.forEach(turma => {
        const li = document.createElement('li');
        li.className = 'py-2 flex justify-between items-center';
        li.innerHTML = `
          <div>
            <span class="font-medium">${turma.nome}</span>
            <span class="text-sm text-gray-500 ml-2">${turma.serie}</span>
          </div>
          <button class="text-red-500 hover:text-red-700" onclick="removerTurma('${turma.id}')">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z" clip-rule="evenodd" />
            </svg>
          </button>
        `;
        lista.appendChild(li);
      });
    }
    
    function atualizarSelectTurmas() {
      const selectTurmaSelecionada = document.getElementById('turmaSelecionada');
      const selectTurmaGerenciamento = document.getElementById('turmaGerenciamento');
      
      // Limpar selects
      selectTurmaSelecionada.innerHTML = '<option value="" disabled selected>Selecione uma turma</option>';
      selectTurmaGerenciamento.innerHTML = '<option value="" disabled selected>Selecione uma turma</option>';
      
      // Adicionar turmas aos selects
      turmas.forEach(turma => {
        const option1 = document.createElement('option');
        option1.value = turma.id;
        option1.textContent = `${turma.nome} - ${turma.serie}`;
        selectTurmaSelecionada.appendChild(option1);
        
        const option2 = document.createElement('option');
        option2.value = turma.id;
        option2.textContent = `${turma.nome} - ${turma.serie}`;
        selectTurmaGerenciamento.appendChild(option2);
      });
      
      // Adicionar event listeners
      selectTurmaGerenciamento.addEventListener('change', function() {
        atualizarListaAlunos(this.value);
      });
    }
    
    function atualizarSelectTurmasRelatorio() {
      const selectTurmaRelatorio = document.getElementById('turmaRelatorio');
      
      // Limpar select
      selectTurmaRelatorio.innerHTML = '<option value="" disabled selected>Selecione uma turma</option>';
      
      // Adicionar turmas ao select
      turmas.forEach(turma => {
        const option = document.createElement('option');
        option.value = turma.id;
        option.textContent = `${turma.nome} - ${turma.serie}`;
        selectTurmaRelatorio.appendChild(option);
      });
      
      // Adicionar event listener
      selectTurmaRelatorio.addEventListener('change', function() {
        atualizarRelatorioAlunos(this.value);
      });
    }
    
    function atualizarSelectTurmasBoletim() {
      const selectTurmaBoletim = document.getElementById('turmaBoletim');
      
      // Limpar select
      selectTurmaBoletim.innerHTML = '<option value="" disabled selected>Selecione uma turma</option>';
      
      // Adicionar turmas ao select
      turmas.forEach(turma => {
        const option = document.createElement('option');
        option.value = turma.id;
        option.textContent = `${turma.nome} - ${turma.serie}`;
        selectTurmaBoletim.appendChild(option);
      });
      
      // Adicionar event listener
      selectTurmaBoletim.addEventListener('change', function() {
        atualizarSelectAlunosBoletim(this.value);
      });
    }
    
    // Funções para alunos
    function cadastrarAlunos() {
      const turmaId = document.getElementById('turmaSelecionada').value;
      const alunosText = document.getElementById('alunosInput').value.trim();
      
      if (!turmaId) {
        mostrarToast('Selecione uma turma!', 'error');
        return;
      }
      
      if (!alunosText) {
        mostrarToast('Digite os nomes dos alunos!', 'error');
        return;
      }
      
      const nomesAlunos = alunosText.split('\n').filter(nome => nome.trim() !== '');
      
      if (nomesAlunos.length === 0) {
        mostrarToast('Digite pelo menos um aluno!', 'error');
        return;
      }
      
      let contador = 0;
      
      nomesAlunos.forEach(nome => {
        nome = nome.trim();
        if (nome) {
          const alunoExistente = alunos.find(a => a.nome === nome && a.turmaId === turmaId);
          
          if (!alunoExistente) {
            const novoAluno = {
              id: Date.now().toString() + contador,
              nome: nome,
              turmaId: turmaId
            };
            
            alunos.push(novoAluno);
            contador++;
          }
        }
      });
      
      localStorage.setItem('alunos', JSON.stringify(alunos));
      document.getElementById('alunosInput').value = '';
      mostrarToast(`${contador} aluno(s) cadastrado(s) com sucesso!`, 'success');
      atualizarEstatisticas();
    }
    
    function removerAluno(id) {
      // Remover notas do aluno
      if (notas[id]) {
        delete notas[id];
        localStorage.setItem('notas', JSON.stringify(notas));
      }
      
      // Remover aluno
      alunos = alunos.filter(aluno => aluno.id !== id);
      localStorage.setItem('alunos', JSON.stringify(alunos));
      
      // Atualizar lista de alunos
      const turmaId = document.getElementById('turmaGerenciamento').value;
      atualizarListaAlunos(turmaId);
      mostrarToast('Aluno removido com sucesso!', 'success');
      atualizarEstatisticas();
    }
    
    function atualizarListaAlunos(turmaId) {
      const lista = document.getElementById('listaAlunos');
      lista.innerHTML = '';
      
      if (!turmaId) return;
      
      const alunosDaTurma = alunos.filter(aluno => aluno.turmaId === turmaId);
      
      if (alunosDaTurma.length === 0) {
        lista.innerHTML = '<li class="py-2 text-gray-500">Nenhum aluno cadastrado nesta turma.</li>';
        return;
      }
      
      alunosDaTurma.sort((a, b) => a.nome.localeCompare(b.nome)).forEach(aluno => {
        const li = document.createElement('li');
        li.className = 'py-2 flex justify-between items-center';
        li.innerHTML = `
          <span>${aluno.nome}</span>
          <div class="flex gap-2">
            <button class="text-blue-500 hover:text-blue-700" onclick="editarNotas('${aluno.id}')">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                <path d="M13.586 3.586a2 2 0 112.828 2.828l-.793.793-2.828-2.828.793-.793zM11.379 5.793L3 14.172V17h2.828l8.38-8.379-2.83-2.828z" />
              </svg>
            </button>
            <button class="text-red-500 hover:text-red-700" onclick="removerAluno('${aluno.id}')">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z" clip-rule="evenodd" />
              </svg>
            </button>
          </div>
        `;
        lista.appendChild(li);
      });
    }
    
    // Funções para relatórios
    function atualizarRelatorioAlunos(turmaId) {
      const lista = document.getElementById('listaAlunosRelatorio');
      lista.innerHTML = '';
      
      if (!turmaId) return;
      
      const alunosDaTurma = alunos.filter(aluno => aluno.turmaId === turmaId);
      
      if (alunosDaTurma.length === 0) {
        lista.innerHTML = '<li class="py-2 text-gray-500">Nenhum aluno cadastrado nesta turma.</li>';
        return;
      }
      
      alunosDaTurma.sort((a, b) => a.nome.localeCompare(b.nome)).forEach(aluno => {
        const li = document.createElement('li');
        li.className = 'py-2 flex justify-between items-center';
        li.innerHTML = `
          <span>${aluno.nome}</span>
          <div class="flex gap-2">
            <button class="text-blue-500 hover:text-blue-700" onclick="visualizarBoletimAluno('${aluno.id}')">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                <path d="M10 12a2 2 0 100-4 2 2 0 000 4z" />
                <path fill-rule="evenodd" d="M.458 10C1.732 5.943 5.522 3 10 3s8.268 2.943 9.542 7c-1.274 4.057-5.064 7-9.542 7S1.732 14.057.458 10zM14 10a4 4 0 11-8 0 4 4 0 018 0z" clip-rule="evenodd" />
              </svg>
            </button>
            <button class="text-green-500 hover:text-green-700" onclick="editarNotas('${aluno.id}')">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                <path d="M13.586 3.586a2 2 0 112.828 2.828l-.793.793-2.828-2.828.793-.793zM11.379 5.793L3 14.172V17h2.828l8.38-8.379-2.83-2.828z" />
              </svg>
            </button>
          </div>
        `;
        lista.appendChild(li);
      });
    }
    
    function gerarPDFRelatorio() {
      const turmaId = document.getElementById('turmaRelatorio').value;
      
      if (!turmaId) {
        mostrarToast('Selecione uma turma!', 'error');
        return;
      }
      
      const turma = turmas.find(t => t.id === turmaId);
      const alunosDaTurma = alunos.filter(aluno => aluno.turmaId === turmaId);
      
      if (alunosDaTurma.length === 0) {
        mostrarToast('Não há alunos nesta turma!', 'error');
        return;
      }
      
      // Criar PDF
      const doc = new jsPDF();
      
      // Título
      doc.setFontSize(16);
      doc.text('Unidade Escolar Genézia Arraes - Anexo - SESC', 105, 20, { align: 'center' });
      
      // Endereço
      doc.setFontSize(10);
      doc.text('Travessa Santo Antônio, 749 - Bairro: Barro Vermelho', 105, 28, { align: 'center' });
      doc.text('São João do Piauí-PI / Contato: 89 9.9410-8800', 105, 34, { align: 'center' });
      
      doc.setFontSize(14);
      doc.text('Relatório de Alunos', 105, 45, { align: 'center' });
      
      doc.setFontSize(12);
      doc.text(`Turma: ${turma.nome} - ${turma.serie}`, 105, 55, { align: 'center' });
      
      // Lista de alunos
      doc.setFontSize(12);
      doc.text('Lista de Alunos:', 20, 70);
      
      let y = 80;
      alunosDaTurma.sort((a, b) => a.nome.localeCompare(b.nome)).forEach((aluno, index) => {
        doc.text(`${index + 1}. ${aluno.nome}`, 30, y);
        y += 10;
        
        // Adicionar nova página se necessário
        if (y > 270) {
          doc.addPage();
          y = 20;
        }
      });
      
      // Rodapé
      const data = new Date();
      doc.setFontSize(10);
      doc.text(`Gerado em: ${data.toLocaleDateString()} às ${data.toLocaleTimeString()}`, 105, 280, { align: 'center' });
      
      // Salvar PDF
      doc.save(`Relatório_${turma.nome}_${turma.serie}.pdf`);
      mostrarToast('PDF gerado com sucesso!', 'success');
    }
    
    // Funções para estatísticas
    function atualizarEstatisticas() {
      document.getElementById('totalTurmas').textContent = turmas.length;
      document.getElementById('totalAlunos').textContent = alunos.length;
      document.getElementById('totalDisciplinas').textContent = disciplinas.length;
      
      const mediaAlunos = turmas.length > 0 ? (alunos.length / turmas.length).toFixed(1) : '0';
      document.getElementById('mediaAlunosTurma').textContent = mediaAlunos;
    }
    
    // Funções para notas
    function editarNotas(alunoId) {
      const aluno = alunos.find(a => a.id === alunoId);
      if (!aluno) return;
      
      alunoEditando = alunoId;
      document.getElementById('modalAlunoNome').textContent = aluno.nome;
      
      const modalDisciplinas = document.getElementById('modalDisciplinas');
      modalDisciplinas.innerHTML = '';
      
      
      disciplinas.forEach(disciplina => {
        const notasAluno = notas[alunoId] && notas[alunoId][disciplina] ? notas[alunoId][disciplina] : ['', '', '', '', '', '', '', ''];

        const div = document.createElement('div');
        div.className = 'border rounded p-3';
        div.innerHTML = `
          <h4 class="font-medium mb-2">${disciplina}</h4>
          <div class="grid grid-cols-4 gap-2">
            ${[0, 1, 2, 3].map(i => `
              <div>
                <label class="block text-xs text-gray-600">${i + 1}º Bim - N1</label>
                <input type="number" min="0" max="10" step="0.1" class="w-full border rounded p-1 nota-input"
                  data-disciplina="\${disciplina}" data-bimestre="\${i * 2}" value="\${notasAluno[i * 2]}">
              </div>
              <div>
                <label class="block text-xs text-gray-600">${i + 1}º Bim - N2</label>
                <input type="number" min="0" max="10" step="0.1" class="w-full border rounded p-1 nota-input"
                  data-disciplina="\${disciplina}" data-bimestre="\${i * 2 + 1}" value="\${notasAluno[i * 2 + 1]}">
              </div>
            `).join('')}
          </div>
        `;
        modalDisciplinas.appendChild(div);
      });

      
      document.getElementById('modalNotas').classList.remove('hidden');
    }
    
    function fecharModalNotas() {
      document.getElementById('modalNotas').classList.add('hidden');
      alunoEditando = null;
    }
    
    function salvarNotas() {
      if (!alunoEditando) return;
      
      if (!notas[alunoEditando]) {
        notas[alunoEditando] = {};
      }
      
      const inputs = document.querySelectorAll('.nota-input');
      inputs.forEach(input => {
        const disciplina = input.dataset.disciplina;
        const bimestre = parseInt(input.dataset.bimestre);
        const valor = input.value.trim() ? parseFloat(input.value) : '';
        
        if (!notas[alunoEditando][disciplina]) {
          notas[alunoEditando][disciplina] = ['', '', '', ''];
        }
        
        notas[alunoEditando][disciplina][bimestre] = valor;
      });
      
      localStorage.setItem('notas', JSON.stringify(notas));
      fecharModalNotas();
      mostrarToast('Notas salvas com sucesso!', 'success');
    }
    
    // Funções para boletim
    function atualizarSelectAlunosBoletim(turmaId) {
      const selectAlunoBoletim = document.getElementById('alunoBoletim');
      
      // Limpar select
      selectAlunoBoletim.innerHTML = '<option value="" disabled selected>Selecione um aluno</option>';
      
      if (!turmaId) return;
      
      // Adicionar alunos ao select
      const alunosDaTurma = alunos.filter(aluno => aluno.turmaId === turmaId);
      
      alunosDaTurma.sort((a, b) => a.nome.localeCompare(b.nome)).forEach(aluno => {
        const option = document.createElement('option');
        option.value = aluno.id;
        option.textContent = aluno.nome;
        selectAlunoBoletim.appendChild(option);
      });
      
      // Esconder boletim
      document.getElementById('boletimContainer').classList.add('hidden');
    }
    
    function visualizarBoletim() {
      const turmaId = document.getElementById('turmaBoletim').value;
      const alunoId = document.getElementById('alunoBoletim').value;
      
      if (!turmaId || !alunoId) {
        mostrarToast('Selecione uma turma e um aluno!', 'error');
        return;
      }
      
      const aluno = alunos.find(a => a.id === alunoId);
      const turma = turmas.find(t => t.id === turmaId);
      
      if (!aluno || !turma) {
        mostrarToast('Aluno ou turma não encontrados!', 'error');
        return;
      }
      
      // Preencher cabeçalho do boletim
      document.getElementById('boletimNomeAluno').textContent = aluno.nome;
      document.getElementById('boletimTurma').textContent = turma.nome;
      document.getElementById('boletimSerie').textContent = turma.serie;
      
      // Preencher notas
      const boletimNotas = document.getElementById('boletimNotas');
      boletimNotas.innerHTML = '';
      
      disciplinas.forEach(disciplina => {
        const notasAluno = notas[alunoId] && notas[alunoId][disciplina] ? notas[alunoId][disciplina] : ['', '', '', ''];
        
        // Calcular média
        
        let soma = 0;
        let count = 0;
        for (let i = 0; i < 8; i += 2) {
          if (notasAluno[i] !== '' && notasAluno[i+1] !== '') {
            const mediaBim = (parseFloat(notasAluno[i]) + parseFloat(notasAluno[i+1])) / 2;
            soma += mediaBim;
            count++;
          }
        }
    
        notasAluno.forEach(nota => {
          if (nota !== '') {
            soma += parseFloat(nota);
            count++;
          }
        });
        
        const media = count > 0 ? (soma / count).toFixed(1) : '-';
        const situacao = media !== '-' ? (parseFloat(media) >= 6 ? 'Aprovado' : 'Reprovado') : '-';
        const corMedia = media !== '-' ? (parseFloat(media) >= 6 ? 'text-green-600' : 'text-red-600') : '';
        
        
        const statusBimestres = [];

        for (let i = 0; i < 4; i++) {
          const n1 = parseFloat(notasAluno[i * 2]);
          const n2 = parseFloat(notasAluno[i * 2 + 1]);

          if (!isNaN(n1) && !isNaN(n2)) {
            const mediaBim = (n1 + n2) / 2;
            const status = mediaBim >= 6 ? '✓' : 'Recup';
            statusBimestres.push(status);
          } else {
            statusBimestres.push('-');
          }
        }

        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td class="border p-2">${disciplina}</td>
          <td class="border p-2 text-center"><input type="number" step="0.1" min="0" max="10" value="${notasAluno[0] !== '' ? notasAluno[0] : ''}" data-d="${disciplina}" data-b="0" data-id="${aluno.id}" class="w-16 border rounded text-center boletim-nota-input"></td>
          <td class="border p-2 text-center"><input type="number" step="0.1" min="0" max="10" value="${notasAluno[1] !== '' ? notasAluno[1] : ''}" data-d="${disciplina}" data-b="1" data-id="${aluno.id}" class="w-16 border rounded text-center boletim-nota-input"></td>
          <td class="border p-2 text-center"><input type="number" step="0.1" min="0" max="10" value="${notasAluno[2] !== '' ? notasAluno[2] : ''}" data-d="${disciplina}" data-b="2" data-id="${aluno.id}" class="w-16 border rounded text-center boletim-nota-input"></td>
          <td class="border p-2 text-center"><input type="number" step="0.1" min="0" max="10" value="${notasAluno[3] !== '' ? notasAluno[3] : ''}" data-d="${disciplina}" data-b="3" data-id="${aluno.id}" class="w-16 border rounded text-center boletim-nota-input"></td>
          <td class="border p-2 text-center"><input type="number" step="0.1" min="0" max="10" value="${notasAluno[4] !== '' ? notasAluno[4] : ''}" data-d="${disciplina}" data-b="4" data-id="${aluno.id}" class="w-16 border rounded text-center boletim-nota-input"></td>
          <td class="border p-2 text-center"><input type="number" step="0.1" min="0" max="10" value="${notasAluno[5] !== '' ? notasAluno[5] : ''}" data-d="${disciplina}" data-b="5" data-id="${aluno.id}" class="w-16 border rounded text-center boletim-nota-input"></td>
          <td class="border p-2 text-center"><input type="number" step="0.1" min="0" max="10" value="${notasAluno[6] !== '' ? notasAluno[6] : ''}" data-d="${disciplina}" data-b="6" data-id="${aluno.id}" class="w-16 border rounded text-center boletim-nota-input"></td>
          <td class="border p-2 text-center"><input type="number" step="0.1" min="0" max="10" value="${notasAluno[7] !== '' ? notasAluno[7] : ''}" data-d="${disciplina}" data-b="7" data-id="${aluno.id}" class="w-16 border rounded text-center boletim-nota-input"></td>
          <td class="border p-2 text-center font-medium ${corMedia}">${media}</td>
          <td class="border p-2 text-center">${statusBimestres[0]}</td>
          <td class="border p-2 text-center">${statusBimestres[1]}</td>
          <td class="border p-2 text-center">${statusBimestres[2]}</td>
          <td class="border p-2 text-center">${statusBimestres[3]}</td>
        `;

        boletimNotas.appendChild(tr);
      });
      
      // Mostrar boletim
      document.getElementById('boletimContainer').classList.remove('hidden');
    }
    
    function visualizarBoletimAluno(alunoId) {
      // Mudar para a tab de boletins
      changeTab('boletins');
      
      const aluno = alunos.find(a => a.id === alunoId);
      if (!aluno) return;
      
      // Selecionar turma
      const selectTurmaBoletim = document.getElementById('turmaBoletim');
      selectTurmaBoletim.value = aluno.turmaId;
      
      // Atualizar select de alunos
      atualizarSelectAlunosBoletim(aluno.turmaId);
      
      // Selecionar aluno (com timeout para garantir que o select foi atualizado)
      setTimeout(() => {
        const selectAlunoBoletim = document.getElementById('alunoBoletim');
        selectAlunoBoletim.value = alunoId;
        
        // Visualizar boletim
        visualizarBoletim();
      }, 100);
    }
    
    function gerarPDFBoletim() {
      const turmaId = document.getElementById('turmaBoletim').value;
      const alunoId = document.getElementById('alunoBoletim').value;
      
      if (!turmaId || !alunoId) {
        mostrarToast('Selecione uma turma e um aluno!', 'error');
        return;
      }
      
      const aluno = alunos.find(a => a.id === alunoId);
      const turma = turmas.find(t => t.id === turmaId);
      
      if (!aluno || !turma) {
        mostrarToast('Aluno ou turma não encontrados!', 'error');
        return;
      }
      
      // Verificar se o boletim está visível
      if (document.getElementById('boletimContainer').classList.contains('hidden')) {
        visualizarBoletim();
      }
      
      // Criar PDF a partir do boletim
      const boletimElement = document.getElementById('boletimContainer');
      
      html2canvas(boletimElement).then(canvas => {
        const imgData = canvas.toDataURL('image/png');
        const pdf = new jsPDF('p', 'mm', 'a4');
        const imgWidth = 210;
        const imgHeight = canvas.height * imgWidth / canvas.width;
        
        pdf.addImage(imgData, 'PNG', 0, 0, imgWidth, imgHeight);
        pdf.save(`Boletim_${aluno.nome}.pdf`);
        mostrarToast('PDF gerado com sucesso!', 'success');
      });
    }
    
    // Funções para backup e restauração
    function exportarDados() {
      const dadosExportacao = {
        disciplinas: disciplinas,
        turmas: turmas,
        alunos: alunos,
        notas: notas,
        dataExportacao: new Date().toISOString()
      };
      
      const jsonString = JSON.stringify(dadosExportacao, null, 2);
      const blob = new Blob([jsonString], { type: 'application/json' });
      const url = URL.createObjectURL(blob);
      
      const a = document.createElement('a');
      a.href = url;
      a.download = `backup_escola_${formatarDataArquivo(new Date())}.json`;
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);
      URL.revokeObjectURL(url);
      
      mostrarToast('Backup exportado com sucesso!', 'success');
    }
    
    function formatarDataArquivo(data) {
      const dia = String(data.getDate()).padStart(2, '0');
      const mes = String(data.getMonth() + 1).padStart(2, '0');
      const ano = data.getFullYear();
      const hora = String(data.getHours()).padStart(2, '0');
      const minuto = String(data.getMinutes()).padStart(2, '0');
      
      return `${dia}-${mes}-${ano}_${hora}-${minuto}`;
    }
    
    function lerArquivoBackup() {
      const input = document.getElementById('arquivoBackup');
      const arquivo = input.files[0];
      
      if (!arquivo) {
        return;
      }
      
      const reader = new FileReader();
      reader.onload = function(e) {
        try {
          dadosBackup = JSON.parse(e.target.result);
          
          // Verificar se o arquivo tem a estrutura correta
          if (!dadosBackup.disciplinas || !dadosBackup.turmas || !dadosBackup.alunos || !dadosBackup.notas) {
            mostrarToast('Arquivo de backup inválido!', 'error');
            return;
          }
          
          // Mostrar informações do backup
          document.getElementById('nomeArquivoBackup').textContent = arquivo.name;
          document.getElementById('infoBackupTurmas').textContent = `Turmas: ${dadosBackup.turmas.length}`;
          document.getElementById('infoBackupAlunos').textContent = `Alunos: ${dadosBackup.alunos.length}`;
          document.getElementById('infoBackupDisciplinas').textContent = `Disciplinas: ${dadosBackup.disciplinas.length}`;
          
          document.getElementById('infoArquivoBackup').classList.remove('hidden');
        } catch (error) {
          mostrarToast('Erro ao ler o arquivo de backup!', 'error');
          console.error(error);
        }
      };
      
      reader.readAsText(arquivo);
    }
    
    function importarDados() {
      if (!dadosBackup) {
        mostrarToast('Selecione um arquivo de backup válido!', 'error');
        return;
      }
      
      // Confirmar importação
      document.getElementById('mensagemConfirmacao').textContent = 'Esta ação irá substituir todos os dados atuais pelos dados do backup. Deseja continuar?';
      
      const botaoConfirmar = document.getElementById('botaoConfirmar');
      botaoConfirmar.className = 'bg-amber-600 text-white px-4 py-2 rounded hover:bg-amber-700';
      botaoConfirmar.onclick = confirmarImportacao;
      
      document.getElementById('modalConfirmacao').classList.remove('hidden');
    }
    
    function confirmarImportacao() {
      // Importar dados
      disciplinas = dadosBackup.disciplinas;
      turmas = dadosBackup.turmas;
      alunos = dadosBackup.alunos;
      notas = dadosBackup.notas;
      
      // Salvar no localStorage
      localStorage.setItem('disciplinas', JSON.stringify(disciplinas));
      localStorage.setItem('turmas', JSON.stringify(turmas));
      localStorage.setItem('alunos', JSON.stringify(alunos));
      localStorage.setItem('notas', JSON.stringify(notas));
      
      // Atualizar interface
      atualizarListaDisciplinas();
      atualizarListaTurmas();
      atualizarSelectTurmas();
      atualizarEstatisticas();
      
      // Limpar dados de backup
      dadosBackup = null;
      document.getElementById('arquivoBackup').value = '';
      document.getElementById('infoArquivoBackup').classList.add('hidden');
      
      // Fechar modal
      fecharModalConfirmacao();
      
      mostrarToast('Dados restaurados com sucesso!', 'success');
    }
    
    function confirmarLimparDados() {
      document.getElementById('mensagemConfirmacao').textContent = 'Esta ação irá apagar TODOS os dados do sistema (turmas, alunos, disciplinas e notas). Esta ação não pode ser desfeita. Deseja continuar?';
      
      const botaoConfirmar = document.getElementById('botaoConfirmar');
      botaoConfirmar.className = 'bg-red-600 text-white px-4 py-2 rounded hover:bg-red-700';
      botaoConfirmar.onclick = limparDados;
      
      document.getElementById('modalConfirmacao').classList.remove('hidden');
    }
    
    function limparDados() {
      // Limpar dados
      disciplinas = [];
      turmas = [];
      alunos = [];
      notas = {};
      
      // Limpar localStorage
      localStorage.removeItem('disciplinas');
      localStorage.removeItem('turmas');
      localStorage.removeItem('alunos');
      localStorage.removeItem('notas');
      
      // Atualizar interface
      atualizarListaDisciplinas();
      atualizarListaTurmas();
      atualizarSelectTurmas();
      atualizarEstatisticas();
      
      // Fechar modal
      fecharModalConfirmacao();
      
      mostrarToast('Todos os dados foram apagados!', 'success');
    }
    
    function fecharModalConfirmacao() {
      document.getElementById('modalConfirmacao').classList.add('hidden');
    }
    
    // Funções utilitárias
    function mostrarToast(mensagem, tipo) {
      const toast = document.getElementById('toast');
      toast.textContent = mensagem;
      
      // Definir cor com base no tipo
      if (tipo === 'error') {
        toast.className = 'toast bg-red-500';
      } else {
        toast.className = 'toast bg-green-500';
      }
      
      // Mostrar toast
      toast.classList.add('show');
      
      // Esconder toast após 3 segundos
      setTimeout(() => {
        toast.classList.remove('show');
      }, 3000);
    }
  
function importarMesclandoDados() {
  if (!dadosBackup) {
    mostrarToast('Selecione um arquivo de backup válido!', 'error');
    return;
  }

  document.getElementById('mensagemConfirmacao').textContent = 'Esta ação irá mesclar os dados do backup com os dados atuais. Nenhum dado será apagado. Deseja continuar?';

  const botaoConfirmar = document.getElementById('botaoConfirmar');
  botaoConfirmar.className = 'bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700';
  botaoConfirmar.onclick = confirmarImportacaoMesclado;

  document.getElementById('modalConfirmacao').classList.remove('hidden');
}

function confirmarImportacaoMesclado() {
  if (!dadosBackup) {
    mostrarToast('Backup inválido!', 'error');
    return;
  }

  const nomesDisciplinasExistentes = new Set(disciplinas);
  dadosBackup.disciplinas.forEach(d => {
    if (!nomesDisciplinasExistentes.has(d)) {
      disciplinas.push(d);
    }
  });

  const idsTurmasExistentes = new Set(turmas.map(t => t.id));
  dadosBackup.turmas.forEach(t => {
    if (!idsTurmasExistentes.has(t.id)) {
      turmas.push(t);
    }
  });

  const idsAlunosExistentes = new Set(alunos.map(a => a.id));
  dadosBackup.alunos.forEach(a => {
    if (!idsAlunosExistentes.has(a.id)) {
      alunos.push(a);
    }
  });

  for (const [alunoId, notasAluno] of Object.entries(dadosBackup.notas)) {
    if (!notas[alunoId]) {
      notas[alunoId] = notasAluno;
    } else {
      for (const [disciplina, valores] of Object.entries(notasAluno)) {
        notas[alunoId][disciplina] = valores;
      }
    }
  }

  localStorage.setItem('disciplinas', JSON.stringify(disciplinas));
  localStorage.setItem('turmas', JSON.stringify(turmas));
  localStorage.setItem('alunos', JSON.stringify(alunos));
  localStorage.setItem('notas', JSON.stringify(notas));

  atualizarListaDisciplinas();
  atualizarListaTurmas();
  atualizarSelectTurmas();
  atualizarEstatisticas();

  dadosBackup = null;
  document.getElementById('arquivoBackup').value = '';
  document.getElementById('infoArquivoBackup').classList.add('hidden');
  fecharModalConfirmacao();

  mostrarToast('Backup mesclado com sucesso!', 'success');
}


function lerArquivosBackup() {
  const input = document.getElementById('arquivoBackup');
  const arquivos = input.files;

  if (!arquivos || arquivos.length === 0) {
    return;
  }

  let arquivosLidos = 0;
  dadosBackup = { disciplinas: [], turmas: [], alunos: [], notas: {} };

  Array.from(arquivos).forEach(arquivo => {
    const reader = new FileReader();
    reader.onload = function(e) {
      try {
        const dados = JSON.parse(e.target.result);

        if (!dados.disciplinas || !dados.turmas || !dados.alunos || !dados.notas) {
          mostrarToast('Arquivo de backup inválido: ' + arquivo.name, 'error');
          return;
        }

        dadosBackup.disciplinas.push(...dados.disciplinas);
        dadosBackup.turmas.push(...dados.turmas);
        dadosBackup.alunos.push(...dados.alunos);
        for (const [alunoId, notasAluno] of Object.entries(dados.notas)) {
          dadosBackup.notas[alunoId] = notasAluno;
        }

        arquivosLidos++;
        if (arquivosLidos === arquivos.length) {
          document.getElementById('nomeArquivoBackup').textContent = arquivos.length + ' arquivo(s) selecionado(s)';
          document.getElementById('infoBackupTurmas').textContent = `Turmas: ${dadosBackup.turmas.length}`;
          document.getElementById('infoBackupAlunos').textContent = `Alunos: ${dadosBackup.alunos.length}`;
          document.getElementById('infoBackupDisciplinas').textContent = `Disciplinas: ${dadosBackup.disciplinas.length}`;
          document.getElementById('infoArquivoBackup').classList.remove('hidden');
        }

      } catch (error) {
        mostrarToast('Erro ao ler o arquivo de backup: ' + arquivo.name, 'error');
        console.error(error);
      }
    };

    reader.readAsText(arquivo);
  });
}


function salvarNotasBoletim() {
  const inputs = document.querySelectorAll('.boletim-nota-input');
  let alteradas = 0;

  inputs.forEach(input => {
    const alunoId = input.dataset.id;
    const disciplina = input.dataset.d;
    const bimestre = parseInt(input.dataset.b);
    const valor = input.value.trim() ? parseFloat(input.value) : '';

    if (!notas[alunoId]) notas[alunoId] = {};
    if (!notas[alunoId][disciplina]) notas[alunoId][disciplina] = ['', '', '', '', '', '', '', ''];

    notas[alunoId][disciplina][bimestre] = valor;
    alteradas++;
  });

  localStorage.setItem('notas', JSON.stringify(notas));
  mostrarToast(`${alteradas} nota(s) salvas diretamente no boletim!`, 'success');
}

</script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'957735b1f2f6f638',t:'MTc1MTIxODgzNC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();
function importarMesclandoDados() {
  if (!dadosBackup) {
    mostrarToast('Selecione um arquivo de backup válido!', 'error');
    return;
  }

  document.getElementById('mensagemConfirmacao').textContent = 'Esta ação irá mesclar os dados do backup com os dados atuais. Nenhum dado será apagado. Deseja continuar?';

  const botaoConfirmar = document.getElementById('botaoConfirmar');
  botaoConfirmar.className = 'bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700';
  botaoConfirmar.onclick = confirmarImportacaoMesclado;

  document.getElementById('modalConfirmacao').classList.remove('hidden');
}

function confirmarImportacaoMesclado() {
  if (!dadosBackup) {
    mostrarToast('Backup inválido!', 'error');
    return;
  }

  const nomesDisciplinasExistentes = new Set(disciplinas);
  dadosBackup.disciplinas.forEach(d => {
    if (!nomesDisciplinasExistentes.has(d)) {
      disciplinas.push(d);
    }
  });

  const idsTurmasExistentes = new Set(turmas.map(t => t.id));
  dadosBackup.turmas.forEach(t => {
    if (!idsTurmasExistentes.has(t.id)) {
      turmas.push(t);
    }
  });

  const idsAlunosExistentes = new Set(alunos.map(a => a.id));
  dadosBackup.alunos.forEach(a => {
    if (!idsAlunosExistentes.has(a.id)) {
      alunos.push(a);
    }
  });

  for (const [alunoId, notasAluno] of Object.entries(dadosBackup.notas)) {
    if (!notas[alunoId]) {
      notas[alunoId] = notasAluno;
    } else {
      for (const [disciplina, valores] of Object.entries(notasAluno)) {
        notas[alunoId][disciplina] = valores;
      }
    }
  }

  localStorage.setItem('disciplinas', JSON.stringify(disciplinas));
  localStorage.setItem('turmas', JSON.stringify(turmas));
  localStorage.setItem('alunos', JSON.stringify(alunos));
  localStorage.setItem('notas', JSON.stringify(notas));

  atualizarListaDisciplinas();
  atualizarListaTurmas();
  atualizarSelectTurmas();
  atualizarEstatisticas();

  dadosBackup = null;
  document.getElementById('arquivoBackup').value = '';
  document.getElementById('infoArquivoBackup').classList.add('hidden');
  fecharModalConfirmacao();

  mostrarToast('Backup mesclado com sucesso!', 'success');
}


function lerArquivosBackup() {
  const input = document.getElementById('arquivoBackup');
  const arquivos = input.files;

  if (!arquivos || arquivos.length === 0) {
    return;
  }

  let arquivosLidos = 0;
  dadosBackup = { disciplinas: [], turmas: [], alunos: [], notas: {} };

  Array.from(arquivos).forEach(arquivo => {
    const reader = new FileReader();
    reader.onload = function(e) {
      try {
        const dados = JSON.parse(e.target.result);

        if (!dados.disciplinas || !dados.turmas || !dados.alunos || !dados.notas) {
          mostrarToast('Arquivo de backup inválido: ' + arquivo.name, 'error');
          return;
        }

        dadosBackup.disciplinas.push(...dados.disciplinas);
        dadosBackup.turmas.push(...dados.turmas);
        dadosBackup.alunos.push(...dados.alunos);
        for (const [alunoId, notasAluno] of Object.entries(dados.notas)) {
          dadosBackup.notas[alunoId] = notasAluno;
        }

        arquivosLidos++;
        if (arquivosLidos === arquivos.length) {
          document.getElementById('nomeArquivoBackup').textContent = arquivos.length + ' arquivo(s) selecionado(s)';
          document.getElementById('infoBackupTurmas').textContent = `Turmas: ${dadosBackup.turmas.length}`;
          document.getElementById('infoBackupAlunos').textContent = `Alunos: ${dadosBackup.alunos.length}`;
          document.getElementById('infoBackupDisciplinas').textContent = `Disciplinas: ${dadosBackup.disciplinas.length}`;
          document.getElementById('infoArquivoBackup').classList.remove('hidden');
        }

      } catch (error) {
        mostrarToast('Erro ao ler o arquivo de backup: ' + arquivo.name, 'error');
        console.error(error);
      }
    };

    reader.readAsText(arquivo);
  });
}


function salvarNotasBoletim() {
  const inputs = document.querySelectorAll('.boletim-nota-input');
  let alteradas = 0;

  inputs.forEach(input => {
    const alunoId = input.dataset.id;
    const disciplina = input.dataset.d;
    const bimestre = parseInt(input.dataset.b);
    const valor = input.value.trim() ? parseFloat(input.value) : '';

    if (!notas[alunoId]) notas[alunoId] = {};
    if (!notas[alunoId][disciplina]) notas[alunoId][disciplina] = ['', '', '', '', '', '', '', ''];

    notas[alunoId][disciplina][bimestre] = valor;
    alteradas++;
  });

  localStorage.setItem('notas', JSON.stringify(notas));
  mostrarToast(`${alteradas} nota(s) salvas diretamente no boletim!`, 'success');
}

</script>
<script>
function atualizarBoletimNotas(disciplinasAluno) {
  const corpo = document.getElementById("boletimNotas");
  corpo.innerHTML = "";

  disciplinas.forEach(disc => {
    const notas = (disciplinasAluno && disciplinasAluno[disc]) || ["", "", "", ""];
    const media = (notas.filter(n => n !== "").map(Number).reduce((a,b) => a+b, 0)) / notas.filter(n => n !== "").length;
    const mediaFinal = isNaN(media) ? "" : media.toFixed(1);
    const recup = media < 6 ? "Sim" : (mediaFinal !== "" ? "Não" : "");
    const status = mediaFinal === "" ? "" : (parseFloat(mediaFinal) >= 6 ? "Aprovado" : "Reprovado");

    const row = document.createElement("tr");
    row.innerHTML = `
      <td class="border p-2">${disc}</td>
      <td class="border p-2"><input type="number" class="w-full text-center boletim-nota-input" data-id="" data-d="${disc}" data-b="0" value="${notas[0] || ""}"></td>
      <td class="border p-2"><input type="number" class="w-full text-center boletim-nota-input" data-id="" data-d="${disc}" data-b="1" value="${notas[1] || ""}"></td>
      <td class="border p-2"><input type="number" class="w-full text-center boletim-nota-input" data-id="" data-d="${disc}" data-b="2" value="${notas[2] || ""}"></td>
      <td class="border p-2"><input type="number" class="w-full text-center boletim-nota-input" data-id="" data-d="${disc}" data-b="3" value="${notas[3] || ""}"></td>
      <td class="border p-2 text-center media-final">${mediaFinal}</td>
      <td class="border p-2 text-center recup">${recup}</td>
      <td class="border p-2 text-center status">${status}</td>
    `;
    corpo.appendChild(row);
  });

  calcularMedias();
}

function calcularMedias() {
  document.querySelectorAll("#boletimNotas tr").forEach(row => {
    const inputs = row.querySelectorAll("input");
    let soma = 0, qtd = 0;
    inputs.forEach(input => {
      const val = parseFloat(input.value);
      if (!isNaN(val)) {
        soma += val;
        qtd++;
      }
    });
    const media = qtd ? (soma / qtd).toFixed(1) : "";
    row.querySelector(".media-final").textContent = media;
    row.querySelector(".recup").textContent = media && parseFloat(media) < 6 ? "Sim" : (media ? "Não" : "");
    row.querySelector(".status").textContent = media ? (parseFloat(media) >= 6 ? "Aprovado" : "Reprovado") : "";
  });
}

// Recalcular ao digitar
document.addEventListener("input", e => {
  if (e.target.matches(".boletim-nota-input")) calcularMedias();
});
</script>


<script>
function salvarNotasBoletim() {
  const alunoId = document.getElementById("boletimNomeAluno").dataset.id;
  if (!alunoId) return alert("Nenhum aluno selecionado.");

  const aluno = alunos.find(a => a.id == alunoId);
  if (!aluno) return;

  const notas = {};
  document.querySelectorAll("#boletimNotas tr").forEach(row => {
    const disciplina = row.children[0].textContent.trim();
    const inputs = row.querySelectorAll("input");
    notas[disciplina] = Array.from(inputs).slice(0, 4).map(i => parseFloat(i.value) || "");
  });

  aluno.boletim = notas;
  localStorage.setItem("alunos", JSON.stringify(alunos));
  alert("Notas salvas com sucesso!");
}
</script>

</body>
</html>
