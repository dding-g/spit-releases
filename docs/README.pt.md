# spit

[English](../README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Português](README.pt.md)

Um pequeno aplicativo de bandeja para Windows e macOS que salva notas rápidas de uma linha em Markdown local. O editor simples fica pré-carregado; os arquivos são gravados em segundo plano. O guia em inglês é a referência.

O repositório do código-fonte é privado. O [repositório público de distribuição](https://github.com/dding-g/spit-releases) contém apenas arquivos oficiais de instalação, atualizações e guias de usuário.

## Instalação e uso

Baixe um arquivo publicado para seu sistema e CPU em [Releases](https://github.com/dding-g/spit-releases/releases) e extraia-o. [Site](https://spit-ashy.vercel.app). No macOS 12 ou posterior, copie `spit.app` para Applications. No Windows, execute `spit.exe` na pasta escolhida; é necessário Microsoft WebView2 Runtime. Não há instalador nem início automático ao entrar no sistema. macOS tem apenas assinatura ad-hoc, **sem Developer ID ou notarização**; Windows não tem assinatura Authenticode. Gatekeeper ou SmartScreen podem avisar ou bloquear. Abra somente arquivos de confiança.

Escolha `macos-arm64` para Apple silicon ou `macos-amd64` para Intel; no Windows, `windows-amd64` para x64 ou `windows-arm64` para ARM64. **Execução, atualização e reinicialização não foram verificadas no Windows**; compilação cruzada não é verificação de execução.

Na primeira execução, spit encontra os cofres registrados do Obsidian sem percorrer todo o disco recursivamente e escolhe o usado mais recentemente. Se não houver nenhum, selecione uma pasta nas configurações. Qualquer pasta existente serve; o Obsidian não é obrigatório.

- `Cmd + \` no macOS / `Ctrl + \` no Windows: mostrar ou ocultar o editor rápido.
- `Enter`: ocultar imediatamente e enviar para salvar em segundo plano. Durante a composição com IME, Enter não envia a nota.
- `Esc`: ocultar e manter o rascunho não enviado **somente nesta sessão**. Sair e abrir normalmente não o restaura; a exceção é a reinicialização explícita para atualizar descrita abaixo.
- Texto colado com várias linhas vira uma linha. Notas vazias são ignoradas; o limite é de 10.000 unidades de código UTF-16.
- Um clique esquerdo no ícone da bandeja/barra de menus abre ou fecha o **histórico somente leitura**, inicialmente em hoje. Escolha qualquer data salva, avance ou volte entre datas com notas, ou atualize. Não permite editar, excluir ou pesquisar e não é um widget permanente da área de trabalho ou do sistema.
- O menu da bandeja também abre o histórico, Nova nota e as configurações de pasta e idioma. Use Nova nota se o atalho global não puder ser registrado. Fechar uma janela não encerra o aplicativo; use Sair.

O idioma pode ser **detectado automaticamente pelo sistema/navegador** ou escolhido manualmente e salvo: inglês, coreano, japonês, chinês simplificado, espanhol, francês, alemão ou português. A escolha manual tem prioridade; idiomas não suportados e traduções ausentes usam inglês. Mudar o idioma preserva o rascunho atual.

## Atualizações e privacidade

spit verifica o GitHub Releases e baixa atualizações automaticamente em segundo plano. As configurações mostram estado, erros, verificação manual e reinicialização quando pronta. **Só instala e reinicia quando você clica em Reiniciar**; nunca interrompe a captura automaticamente. Aguarda os salvamentos em andamento e a persistência da fila, e recusa reiniciar durante a composição IME. Preserva o rascunho não enviado para restaurá-lo após a atualização; essa cópia exclusiva da atualização não torna persistentes os rascunhos das sessões normais.

Antes de instalar, verifica SHA-256 e a assinatura Ed25519 com a chave pública fixada no aplicativo; falhas bloqueiam a instalação. Essa assinatura é diferente da assinatura do sistema, da notarização Apple e da confiança de editor Windows. Verificações e downloads acessam GitHub e seus hosts de download, mas não enviam notas nem arquivos do cofre. A sincronização do cofre depende dos outros programas.

## Arquivos e recuperação

Ao selecionar `chosenRoot`, as notas são gravadas em `chosenRoot/admo/YYYY-MM-DD.md`. Se a pasta já se chama `admo`, ela é usada diretamente, sem criar `admo/admo`. Notas diárias existentes na raiz não são movidas. Cada nota mantém a data e a hora local do envio, mesmo que a gravação seja adiada:

```markdown
- 09:12:04 Comprar leite <!-- admo:UUID -->
```

O comentário HTML oculto identifica a nota para evitar duplicação durante a recuperação; ele continua visível no código-fonte. O texto é escapado em vez de interpretado como Markdown/HTML, e o conteúdo existente é preservado. O histórico lê arquivos datados na pasta efetiva `admo`, não o cofre inteiro, e reconhece marcadores antigos compatíveis.

As notas enviadas são primeiro preservadas na fila local `state.json`, depois acrescentadas e sincronizadas ao Markdown antes de sair da fila. Se a gravação falhar, continuam pendentes; o erro aparece nas configurações e na bandeja. Há novas tentativas a cada **5 segundos** e recuperação ao reiniciar. Se nem a fila local puder ser salva, o texto enviado volta ao rascunho.

Notas pendentes mantêm o destino original; mudar a pasta só afeta novas notas. A fila é processada em ordem: um destino anterior indisponível bloqueia as notas seguintes. Restaure a pasta ou as permissões para permitir a nova tentativa. Um estado corrompido **não é redefinido automaticamente**: preserve o arquivo indicado no erro antes de repará-lo.

Estado local, separado da pasta Markdown:

- macOS: `~/Library/Application Support/spit/state.json`
- Windows: `%AppData%\spit\state.json`

No local padrão, sem estado `spit`, migra-se o estado antigo de `admo`, depois de `adhd-memo`, sem sobrescrever o novo estado existente. A pasta Markdown e os marcadores existentes continuam `admo` por compatibilidade. Não se renomeiam, movem ou excluem pastas do cofre, e as notas pendentes mantêm seus destinos.

Notas e estado são **texto simples sem criptografia**. Ocultar o editor não confirma uma gravação durável: um encerramento forçado ou falta de energia antes de persistir a fila pode perder aquela entrada. Uma gravação interrompida pode deixar linhas parciais; a recuperação as preserva e acrescenta a nota completa. Falhas do sistema de arquivos/dispositivo e alterações simultâneas por editores ou sincronizadores estão fora da garantia.
