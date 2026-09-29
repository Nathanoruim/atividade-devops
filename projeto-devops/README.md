# Projeto DevOps CI/CD

Projeto introdutório para demonstrar um site estático e sua publicação automática no GitHub Pages com GitHub Actions.

## Conceitos

- **DevOps** aproxima desenvolvimento e operações por meio de colaboração e automação.
- **CI (Integração Contínua)** integra alterações frequentemente e executa verificações para encontrar erros cedo.
- **CD (Entrega/implantação contínua)** prepara ou publica automaticamente uma versão após as verificações.
- **Runner** é a máquina que executa um workflow. Este projeto usa um GitHub-hosted Runner com Ubuntu.
- **Artefato** é um arquivo ou conjunto de arquivos produzido por um workflow. Aqui, o conteúdo do site é empacotado para o Pages.
- **`needs`** define dependência entre jobs. `publicar-site` só começa quando `validacao` termina com sucesso.

## Como o workflow funciona

O arquivo `.github/workflows/deploy.yml` inicia em cada `push` para `main` (e também pode ser iniciado manualmente). O job `validacao` baixa o repositório, verifica `index.html`, exibe informações do sistema e cria o artefato com `actions/upload-pages-artifact@v3`. Depois, `publicar-site` publica esse artefato com `actions/deploy-pages@v4`.

## Publicar no GitHub Pages

1. Crie um repositório no GitHub. Se o repositório for vazio, não inicialize com README ou outros arquivos.
2. Envie este projeto usando os comandos abaixo, substituindo `URL_DO_REPOSITORIO` pela URL real do seu repositório.
3. No repositório, abra **Settings → Pages** e em **Build and deployment → Source** selecione **GitHub Actions**.
4. Abra **Actions** e acompanhe o workflow **Deploy do Meu Site**. O primeiro `push` para `main` inicia a publicação.
5. Quando o job `publicar-site` terminar com sucesso, abra **Settings → Pages** para acessar o endereço publicado.

## Comandos Git

Execute na pasta `projeto-devops`:

```bash
git init
git add .
git commit -m "Cria projeto DevOps CI/CD"
git branch -M main
git remote add origin URL_DO_REPOSITORIO
git push -u origin main
```

## Atividade prática

Faça os exercícios em uma cópia de prática e restaure os arquivos ao estado original ao final. Cada alteração enviada para `main` inicia o workflow. Para repetir uma execução sem novo commit, use **Actions → Deploy do Meu Site → Run workflow**, se disponível.

### Parte 1 — Workflow, checkout e job

1. Em `deploy.yml`, altere o texto após `name:` e faça commit/push. Confira o novo nome na aba **Actions**.
2. Comente temporariamente a linha `uses: actions/checkout@v4` (adicione `#` no começo) e faça push. A etapa seguinte deve falhar porque o Runner não terá o arquivo do repositório no diretório de trabalho.
3. Veja a etapa com falha nos logs, descomente a linha, faça push e confirme que voltou a passar.
4. Altere o nome do job `validacao` e também o valor de `needs` para o mesmo identificador. Identificadores YAML de job devem ser válidos; faça push e confira a execução.

### Parte 2 — Dependência, smoke test e artefato

1. Adicione temporariamente uma etapa de validação com `run: exit 1`, faça push e veja `validacao` falhar. `publicar-site` será ignorado por causa de `needs`.
2. Remova essa etapa de falha, faça push e confirme que publicação volta a executar.
3. Renomeie temporariamente `index.html` e envie a alteração. A etapa `test -f index.html` deve falhar; restaure o nome e envie novamente.
4. Na execução bem-sucedida, abra o resumo do workflow e localize o artefato do Pages.
5. Para testar erro no upload, mude temporariamente `path: '.'` para um caminho que não existe, por exemplo `path: './pasta-inexistente'`. O passo de upload deve falhar. Restaure `path: '.'` e confirme a execução.

### Parte 3 — Runner e informações do sistema

1. Abra **Actions**, selecione uma execução e expanda os passos **Mostrar informações do Runner**, **Mostrar memória RAM** e **Mostrar quantidade de CPUs**. Os comandos `lsb_release -a`, `free -h` e `nproc` aparecem nos logs dessas etapas.
2. Um **GitHub-hosted Runner** é uma máquina virtual disponibilizada e gerenciada pelo GitHub para a execução. Um **self-hosted Runner** é uma máquina que você configura e mantém.

**GitHub-hosted — vantagens:** configuração rápida; manutenção da máquina pelo GitHub; integração simples com Actions.

**GitHub-hosted — desvantagens:** menos controle sobre a máquina; ambiente temporário a cada job; limites de uso e disponibilidade conforme o plano.

**Self-hosted — vantagens:** controle do sistema e das ferramentas; possibilidade de reutilizar recursos locais; acesso a rede ou hardware próprios.

**Self-hosted — desvantagens:** você cuida de atualizações e segurança; precisa manter a máquina disponível; configuração e diagnóstico ficam por sua conta.

### Parte 4 — Instalação e falha proposital

1. Crie um job separado no nível `jobs`, por exemplo `instalar-cowsay`, com `runs-on: ubuntu-latest` e uma etapa `run: sudo apt-get install -y cowsay`. Faça push e confira o resultado.
2. Cada job recebe um Runner separado. Assim, instalar um programa em um job não o instala no Runner do outro job.
3. Em uma etapa desse job, adicione temporariamente `comando_que_nao_existe`. O job deve falhar com erro de comando não encontrado.
4. Abra o job na aba **Actions**, expanda a etapa vermelha e leia as últimas linhas do log para identificar o comando que falhou. Remova o comando inválido, faça push e confirme a correção. Remova o job de demonstração ao terminar, se não quiser mantê-lo.

## Checklist da atividade

- [ ] Workflow com nome personalizado
- [ ] Job renomeado
- [ ] `needs` funcionando
- [ ] Smoke test funcionando
- [ ] Artefato criado
- [ ] SO do Runner identificado
- [ ] RAM identificada
- [ ] CPUs identificadas
- [ ] GitHub-hosted vs Self-hosted explicado
- [ ] Erro proposital realizado
- [ ] Erro explicado
- [ ] Pipeline corrigido
- [ ] Site publicado no GitHub Pages

## O que printar para entregar

- A árvore de arquivos do projeto.
- O workflow com seu nome personalizado e os jobs configurados.
- Uma execução bem-sucedida, mostrando `validacao` e `publicar-site`.
- Os logs de `lsb_release -a`, `free -h` e `nproc`.
- O artefato disponível no resumo da execução.
- Um exemplo de falha proposital e seu log, junto da execução corrigida.
- O site aberto no endereço do GitHub Pages.
- O checklist preenchido conforme os exercícios realizados.
