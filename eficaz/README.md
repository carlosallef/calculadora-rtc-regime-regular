# Fork da Eficaz

Fork de [ReceitaFederal/calculadora-rtc-regime-regular](https://github.com/ReceitaFederal/calculadora-rtc-regime-regular)
(licença MIT, preservada em `LICENSE`). A branch `main` acompanha a da Receita e **não é usada** para build.

- A branch `eficaz` guarda só o que é nosso: `eficaz/Dockerfile`, `eficaz/VERSAO` e o workflow `eficaz-imagem.yml`.
- O build faz checkout da **tag `rtc-<versão>`** (o código da Receita intacto), confere que ela aponta para o commit
  registrado no ato oficial (Portaria Cotec 342/2026 para a 1.6.1) e publica `ghcr.io/carlosallef/rtc-regular:<versão>`.
- Trocar de versão: conferir o novo commit contra o ato, criar a tag `rtc-<versão>` nele e editar `eficaz/VERSAO`.
- Imagem: JRE 21, perfil `offline`, HTTP 8080 (`/api`), gestão 9101 (`/health`, `/metrics`). Sem autenticação:
  **nunca publicar porta para a internet**.
