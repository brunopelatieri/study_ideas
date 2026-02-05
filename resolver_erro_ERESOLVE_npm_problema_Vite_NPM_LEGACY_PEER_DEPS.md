<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# como solucionar esse erro:

PS E:\\Downloads\\bkp-gln-empresarial-landing-page-deploy> npm install
npm error code ERESOLVE
npm error ERESOLVE could not resolve
npm error
npm error While resolving: framer-motion@11.11.17
npm error Found: react@19.2.4
npm error node_modules/react
npm error   react@"^19.2.4" from the root project
npm error   peer react@"^19" from @react-three/drei@10.0.0
npm error   node_modules/@react-three/drei
npm error     @react-three/drei@"10.0.0" from the root project
npm error   10 more (@react-three/fiber, lucide-react, react-dom, ...)
npm error
npm error Could not resolve dependency:
npm error peerOptional react@"^18.0.0" from framer-motion@11.11.17
npm error node_modules/framer-motion
npm error   framer-motion@"11.11.17" from the root project
npm error
npm error Conflicting peer dependency: react@18.3.1
npm error node_modules/react
npm error   peerOptional react@"^18.0.0" from framer-motion@11.11.17
npm error   node_modules/framer-motion
npm error     framer-motion@"11.11.17" from the root project
npm error
npm error Fix the upstream dependency conflict, or retry
npm error this command with --force or --legacy-peer-deps
npm error to accept an incorrect (and potentially broken) dependency resolution.
npm error
npm error
npm error For a full report see:
npm error C:\\Users\\Bruno P Goulart\\AppData\\Local\\npm-cache\\_logs\\2026-02-05T00_03_20_206Z-eresolve-report.txt
npm notice
npm notice New major version of npm available! 10.9.3 -> 11.9.0
npm notice Changelog: [https://github.com/npm/cli/releases/tag/v11.9.0](https://github.com/npm/cli/releases/tag/v11.9.0)
npm notice To update run: npm install -g npm@11.9.0
npm notice
npm error A complete log of this run can be found in: C:\\Users\\Bruno P Goulart\\AppData\\Local\\npm-cache\\_logs\\2026-02-05T00_03_20_206Z-debug-0.log
PS E:\\Downloads\\bkp-gln-empresarial-landing-page-deploy> npm run build

> bkp-gln-empresarial-landing-page@0.0.0 build
> vite build

'vite' não é reconhecido como um comando interno
ou externo, um programa operável ou um arquivo em lotes.
PS E:\\Downloads\\bkp-gln-empresarial-landing-page-deploy>

Vamos resolver o erro de ERESOLVE do npm e o problema com o Vite (vite não reconhecido) passo a passo.

Resumo direto

- O conflito ocorre entre a versão do React instalada (19.x) e a versão exigida como peer do Framer Motion (que, em algumas versões, exige React 18.x). Você precisa alinhar as versões ou contornar o peer-dependency com configurações do npm.
- O comando de build falha porque o Vite não está instalado/globalmente disponível no ambiente atual.

Soluções recomendadas

1) Tente instalar com resolução de peer mais permissiva

- Remova node_modules e o lockfile para começar de novo:
    - rm -rf node_modules
    - rm package-lock.json
- Reinstale usando a opção legacy-peer-deps (utiliza o comportamento antigo de resolução de dependências):
    - npm install --legacy-peer-deps
- Se preferir forçar: npm install --force
- Observação: usar legacy-peer-deps pode mascarar incompatibilidades; use com cuidado.

2) Alinhe as versões de React e Framer Motion

- Verifique seu package.json:
    - Se estiver com React 19.x, confirme a compatibilidade do Framer Motion que você está usando. Muitas versões estáveis ainda trabalham com React 18.x.
- Sugestão prática:
    - Baixe uma versão do Framer Motion que declare React 18 como peer, por exemplo framer-motion@11.x geralmente funciona com React 18.0+.
    - Em package.json, ajuste:
        - "react": "^18.2.0" (ou mantenha 19 apenas se houver pacote que suporte)
        - "framer-motion": "^11.11.17" (ou uma versão compatível com React 18)
    - Em seguida: npm install

3) Verifique o build com Vite

- O erro "'vite' não é reconhecido" indica que Vite não está instalado localmente no projeto ou não está no PATH do ambiente.
- Instale como dependência de desenvolvimento:
    - npm install --save-dev vite
- Ou, se já usa um script de build baseado em Vite, confirme se ele faz referência ao comando npx vite ou npm run build. Adicione vite aos devDeps conforme necessário.

4) Alternativas rápidas (menos recomendadas, mas úteis)

- Use o YARN como gerenciador de pacotes, que às vezes resolve dependências de forma diferente:
    - rm -rf node_modules
    - rm package-lock.json
    - yarn install
    - yarn build (ou npm run build se ainda usar npm)
- Segurar versões com npx npm-check-updates -u para atualizar dependências para as mais novas compatíveis, seguida de npm install.

Boas práticas

- Prefira manter o React na faixa estável (18.x) se estiver usando Framer Motion atual, para reduzir conflitos de peer.
- Sempre apague node_modules e o lockfile ao alterar grandes dependências para evitar residuais.
- Leia as mensagens de erro completas do npm (log em C:\Users\...\eresolve-report.txt) para identificar qual pacote está gerando o conflito.

Exemplos práticos de comandos

- Limpeza e instalação com peer legado:
    - rm -rf node_modules
    - rm package-lock.json
    - npm install --legacy-peer-deps
- Instalar Vite antes do build:
    - npm install --save-dev vite
    - npm run build

Se puder, compartilhe seu package.json (ou as versões atuais de react, framer-motion, e vite) para eu indicar uma configuração de dependências que garanta compatibilidade e um caminho claro até o build.
<span style="display:none">[^1][^10][^2][^3][^4][^5][^6][^7][^8][^9]</span>

<div align="center">⁂</div>

[^1]: https://stackoverflow.com/questions/71830754/npm-err-code-eresolve-npm-err-eresolve-unable-to-resolve-dependency-tree-in-re

[^2]: https://stackoverflow.com/questions/72596908/could-not-resolve-dependency-error-peer-react16-8-0-17-0-0-from-materia

[^3]: https://github.com/motiondivision/motion/issues/2854

[^4]: https://www.reddit.com/r/learnreactjs/comments/w0u6l4/what_does_this_error_mean_cant_resolve_peer/

[^5]: https://github.com/plouc/react-svg-buttons/pull/5

[^6]: https://stackoverflow.com/questions/72976149/npm-err-conflicting-peer-dependency-react18-0-0

[^7]: https://github.com/motiondivision/motion/issues/1405

[^8]: https://www.reddit.com/r/PayloadCMS/comments/1dhjxu4/errors_on_npm_install_anything/

[^9]: https://stackoverflow.com/questions/65549858/eresolve-unable-to-resolve-dependency-tree-when-installing-npm-react-facebook

[^10]: https://github.com/pmndrs/react-three-fiber/issues/3295

