# Laudo Técnico

### Bug 1
- **Arquivo**: `lib/models/ocorrencia.dart`
- **Sintoma**: Mapeamento incorreto do identificador do autor.
- **Causa**: O campo `autorId` recebia o valor de `d['autorNome']` dentro do factory `Ocorrencia.fromDoc`.
- **Correção**: Alterado para carregar o campo correto `d['autorId']`.
- **Teste**: Validação do controle de dono em telas de edição.

### Bug 2
- **Arquivo**: `lib/models/ocorrencia.dart`
- **Sintoma**: Estado lógico de resolução invertido.
- **Causa**: O campo `resolvido` aplicava o operador de negação `!` sobre o booleano vindo do Firestore `!(d['resolvido'] as bool? ?? false)`.
- **Correção**: Removido o operador de negação para refletir o estado correto.
- **Teste**: Ocorrências salvas como falso no banco agora exibem "Em aberto" corretamente.

### Bug 3
- **Arquivo**: `lib/services/central_service.dart`
- **Sintoma**: O criador da ocorrência perdia permissão de dono ao tentar modificar o status.
- **Causa**: O método `registrar` salvava o e-mail no campo `autorId` em vez do identificador único `u.uid`.
- **Correção**: Alterado para salvar `u.uid` no campo `autorId`.
- **Teste**: Criação de item e validação da correspondência de ID com o usuário logado.

### Bug 4
- **Arquivo**: `lib/services/central_service.dart`
- **Sintoma**: Permissão negada ou erro de validação de proprietário ao alterar o status.
- **Causa**: O método `alterarStatus` enviava o valor original de `item.resolvido` sem invertê-lo.
- **Correção**: Atualizado o payload do Firestore para enviar `!item.resolvido`.
- **Teste**: Clique no botão alterna corretamente o estado entre resolvido e aberto.

### Bug 5
- **Arquivo**: `lib/services/central_service.dart`
- **Sintoma**: Pistas enviadas de uma ocorrência misturavam-se globalmente ou iam para um documento inválido.
- **Causa**: O método `enviarPista` usava um caminho fixo de documento `.doc('pistas')` em vez de utilizar a variável dinâmica `ocorrenciaId`.
- **Correção**: Substituído o texto fixo por `ocorrenciaId`.
- **Teste**: Pistas adicionadas em uma ocorrência aparecem exclusivamente nela.

### Bug 6
- **Arquivo**: `lib/services/central_service.dart`
- **Sintoma**: Texto em branco enviado nas pistas.
- **Causa**: O método `enviarPista` gravava uma string vazia `''` de forma estática no campo `mensagem`.
- **Correção**: Parametrizado para enviar a variável dinâmica `mensagem`.
- **Teste**: Mensagens digitadas aparecem legíveis na listagem de pistas.

### Bug 7
- **Arquivo**: `lib/pages/central_page.dart`
- **Sintoma**: Validação de formulário falhava ao aceitar campos vazios.
- **Causa**: A condicional de validação usava o operador `&&` (`titulo.text.trim().isEmpty && local.text.trim().isEmpty`), permitindo o envio se apenas um deles estivesse vazio.
- **Correção**: Trocado pelo operador lógico `||`.
- **Teste**: Tentativa de registrar sem título ou sem local exibe o SnackBar de bloqueio.

### Bug 8
- **Arquivo**: `lib/pages/central_page.dart`
- **Sintoma**: Filtro de listagem funcionava de forma inversa ao esperado.
- **Causa**: A lógica de alternar o switch invertia o valor enviado usando `!value` na função `setState`.
- **Correção**: Ajustado para repassar o valor puro do argumento `value`.
- **Teste**: Ativar o switch agora filtra adequadamente conforme o estado selecionado.

### Bug 9
- **Arquivo**: `lib/pages/central_page.dart`
- **Sintoma**: O filtro de listagem trazia itens resolvidos ao pedir itens não resolvidos.
- **Causa**: A filtragem por listagem de abertos buscava `o.resolvido` em vez de `!o.resolvido`.
- **Correção**: Alterado o predicado do filtro `.where` para buscar por `!o.resolvido`.
- **Teste**: Ativação do filtro oculta com precisão os itens marcados como encerrados.

### Bug 10
- **Arquivo**: `lib/pages/central_page.dart`
- **Sintoma**: Erro de compilação ou navegação quebrada ao acessar detalhes.
- **Causa**: A rota chamava a classe `DetalhePage` sem passar o parâmetro obrigatório `ocorrencia`.
- **Correção**: Passado o argumento através de `DetalhePage(ocorrencia: o)`.
- **Teste**: Clique no card redireciona para a tela correta carregando as informações do item.

### Bug 11
- **Arquivo**: `lib/pages/detalhe_page.dart`
- **Sintoma**: O botão de envio de pistas aceitava textos vazios e travava textos válidos.
- **Causa**: O método `enviar` continha uma trava invertida `if (pista.text.trim().isNotEmpty) return;`.
- **Correção**: Alterado o predicado para abortar quando estiver vazio (`isEmpty`).
- **Teste**: O botão bloqueia cliques com campo em branco e processa textos válidos.

### Bug 12
- **Arquivo**: `lib/services/central_service.dart`
- **Sintoma**: Ordem visual confusa com itens novos surgindo no final da lista.
- **Causa**: O método `acompanhar` ordenava de forma ascendente `descending: false` por data de criação.
- **Correção**: Mudado para `descending: true` trazendo as ocorrências mais recentes para o topo.
- **Teste**: Ocorrências novas aparecem imediatamente no início da tela.
