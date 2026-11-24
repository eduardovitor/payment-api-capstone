# Relatório de Evidências Cloud-Native — Projeto Final Capstone (Trilha 3)

**Nome do Aluno(a):** Eduardo Vitor Vieira Torres 
**Turma:** Supply Chain Security (Turma 1746)  
**Trilha Escolhida:** Trilha 3 — Engenharia de Esteira em Nuvem & Keyless OIDC *(Tempo-alvo: ~95 min | Teto: 2h)*  
**Data:** 22/11/2026

- **Modalidade de Entrega:**
  - `[ X ] Trilha Única` *(primeira trilha entregue — responder também ao **Bloco Express** no final)*
  - `[ ] Modo Incremental` *(já entreguei trilha anterior — **Bloco Express dispensado**; reutilizando `vex_customizado.openvex.json` e `openvex_audit.rego`)*

---

## 1. Links Oficiais da Entrega — *Aula 08* (`1,5 pts`)
- **Repositório Público no GitHub:** `https://github.com/eduardovitor/payment-api-capstone`
- **Run Bloqueada pelo Conftest (`FAIL` intencional da `GPL-3.0`):** `https://github.com/eduardovitor/payment-api-capstone/actions/runs/45022828038/job/137209269488`
- **Run Verde Final (Após Remediação):** `https://github.com/eduardovitor/payment-api-capstone/actions/runs/45024107829`
- **Pacote Público no GHCR:** `ghcr.io/eduardovitor/payment-api-capstone/payment-api`
- **Registro Público no Rekor (`search.sigstore.dev`):** 4044597151

---

## 2. Hardening da Esteira, Rego v1 & Ciclo Red → Green — *Aulas 02, 03, 05 & 07* (`3,5 pts`)
- **Correções aplicadas antes do 1º push:**
  - `[ ] TODO 1 resolvido: id-token: write habilitado em golden_pipeline_capstone.yml`
  - `[ ] TODO 2 resolvido: actions/setup-node fixado por SHA-1 de 40 caracteres`
  - `[ ] policies/openvex_audit.rego e attestations/vex_customizado.openvex.json sem TODOs pendentes`
- **Log do bloqueio inicial da `gpl-math-lib` pelo Conftest na run vermelha:**
  ```text
  Run mkdir -p dist
  [info] checking github for release tag='v1.19.0' 
  [info] fetching release script for tag='v1.19.0' 
  [info] checking github for release tag='v1.19.0' 
  [info] using release tag='v1.19.0' version='1.19.0' os='linux' arch='amd64' 
  [info] installed /usr/local/bin/syft 
  FAIL - dist/sbom.cdx.json - main - VIOLACAO DE LICENCA: O componente 'gpl-math-lib@1.0.0' possui licenca proibida 'GPL-3.0-only'. Remova ou substitua a dependencia.

  2 tests, 1 passed, 0 warnings, 1 failure, 0 exceptions
  Error: Process completed with exit code 1.
  ```
- **Commit da remediação (remoção de `gpl-math-lib`):** `91f4da64c002d589adf7036498499061613c5ba9`

---

## 3. Verificação Pública Keyless (Fulcio + Rekor) & Atestação OpenVEX — *Aulas 04 & 06* (`3,5 pts`)
- **Comando público executado (via GitHub Codespaces, CLI local ou container):**
  ```bash
cosign verify --certificate-identity="https://github.com/eduardovitor/payment-api-capstone/.github/workflows/golden_pipeline_capstone.yml@refs/heads/main" --certificate-oidc-issuer="https://token.actions.githubusercontent.com" ghcr.io/eduardovitor/payment-api-capstone/payment-api@sha256:e9aacb7898b2d345c58c142e27793152c6276dc96e234fae67de820264b3e4a1
  ```
- **Saída real do `cosign verify` e `cosign verify-attestation --type openvex`:**
  ```text
    Verification for ghcr.io/eduardovitor/payment-api-capstone/payment-api@sha256:e9aacb7898b2d345c58c142e27793152c6276dc96e234fae67de820264b3e4a1 --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - The code-signing certificate was verified using trusted certificate authority certificates

[{"critical":{"identity":{"docker-reference":"ghcr.io/eduardovitor/payment-api-capstone/payment-api@sha256:e9aacb7898b2d345c58c142e27793152c6276dc96e234fae67de820264b3e4a1"},"image":{"docker-manifest-digest":"sha256:e9aacb7898b2d345c58c142e27793152c6276dc96e234fae67de820264b3e4a1"},"type":"https://openvex.dev/ns"},"optional":{}},{"critical":{"identity":{"docker-reference":"ghcr.io/eduardovitor/payment-api-capstone/payment-api@sha256:e9aacb7898b2d345c58c142e27793152c6276dc96e234fae67de820264b3e4a1"},"image":{"docker-manifest-digest":"sha256:e9aacb7898b2d345c58c142e27793152c6276dc96e234fae67de820264b3e4a1"},"type":"https://cyclonedx.org/bom"},"optional":{}},{"critical":{"identity":{"docker-reference":"ghcr.io/eduardovitor/payment-api-capstone/payment-api@sha256:e9aacb7898b2d345c58c142e27793152c6276dc96e234fae67de820264b3e4a1"},"image":{"docker-manifest-digest":"sha256:e9aacb7898b2d345c58c142e27793152c6276dc96e234fae67de820264b3e4a1"},"type":"https://sigstore.dev/cosign/sign/v1"},"optional":{}}]

Verification for ghcr.io/eduardovitor/payment-api-capstone/payment-api@sha256:e9aacb7898b2d345c58c142e27793152c6276dc96e234fae67de820264b3e4a1 --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - The code-signing certificate was verified using trusted certificate authority certificates
{"payload":"eyJfdHlwZSI6Imh0dHBzOi8vaW4tdG90by5pby9TdGF0ZW1lbnQvdjAuMSIsICJzdWJqZWN0IjpbeyJuYW1lIjoiZ2hjci5pby9lZHVhcmRvdml0b3IvcGF5bWVudC1hcGktY2Fwc3RvbmUvcGF5bWVudC1hcGkiLCAiZGlnZXN0Ijp7InNoYTI1NiI6ImU5YWFjYjc4OThiMmQzNDVjNThjMTQyZTI3NzkzMTUyYzYyNzZkYzk2ZTIzNGZhZTY3ZGU4MjAyNjRiM2U0YTEifX1dLCAicHJlZGljYXRlVHlwZSI6Imh0dHBzOi8vb3BlbnZleC5kZXYvbnMiLCAicHJlZGljYXRlIjp7IkBjb250ZXh0IjoiaHR0cHM6Ly9vcGVudmV4LmRldi9ucy92MC4yLjAiLCAiQGlkIjoiaHR0cHM6Ly9lbXByZXNhLmNvcnAuaW50ZXJuYWwvdmV4L3BheW1lbnQtYXBpLzIwMjYtMTEtMTgiLCAiYXV0aG9yIjoiU2VjT3BzICYgU3VwcGx5IENoYWluIERlZmVuc2UgQ29tbWl0dGVlIDxzZWNvcHNAZW1wcmVzYS5jb3JwLmludGVybmFsPiIsICJzdGF0ZW1lbnRzIjpbeyJpbXBhY3Rfc3RhdGVtZW50IjoiQSBmdW5jYW8gcXMgdnVsbmVyYXZlbCBuYW8gZSBpbnZvY2FkYSBwZWxhcyByb3RhcyBIVFRQIGRhIGFwbGljYWNhbyBwYXltZW50LWFwaS4gTyBwYXJzZXIgY3VzdG9taXphZG8gZGUgSlNPTiBpc29sYSBjb21wbGV0YW1lbnRlIG9zIHBhcmFtZXRyb3MuIiwgImp1c3RpZmljYXRpb24iOiJjb2RlX25vdF9yZWFjaGFibGUiLCAicHJvZHVjdHMiOlt7IkBpZCI6InBrZzpucG0vZXhwcmVzc0A0LjE4LjIifV0sICJzdGF0dXMiOiJub3RfYWZmZWN0ZWQiLCAidnVsbmVyYWJpbGl0eSI6eyJkZXNjcmlwdGlvbiI6IlZ1bG5lcmFiaWxpZGFkZSBkZSBQcm90b3R5cGUgUG9sbHV0aW9uIG5hIGJpYmxpb3RlY2EgcXMvZXhwcmVzcyIsICJuYW1lIjoiQ1ZFLTIwMjItMjQ5OTkifX0sIHsiaW1wYWN0X3N0YXRlbWVudCI6IkEgYXBsaWNhY2FvIGNvbnNvbWUgZXhjbHVzaXZhbWVudGUgbWV0b2RvcyBkZSBjb2xlY2FvIChsb2Rhc2guZ2V0LCBsb2Rhc2gubWVyZ2UpIHNlbSBtYW5pcHVsYWNhbyBkZSBzdHJpbmdzIHBvciB0ZW1wbGF0ZXMuIiwgImp1c3RpZmljYXRpb24iOiJ2dWxuZXJhYmxlX2NvZGVfY2Fubm90X2JlX2NvbnRyb2xsZWRfYnlfYWR2ZXJzYXJ5IiwgInByb2R1Y3RzIjpbeyJAaWQiOiJwa2c6bnBtL2xvZGFzaEA0LjE3LjIwIn1dLCAic3RhdHVzIjoibm90X2FmZmVjdGVkIiwgInZ1bG5lcmFiaWxpdHkiOnsiZGVzY3JpcHRpb24iOiJSZURvUyBlbSBsb2Rhc2gudGVtcGxhdGUiLCAibmFtZSI6IkNWRS0yMDIwLTI4NTAwIn19LCB7ImltcGFjdF9zdGF0ZW1lbnQiOiJPIG1pY3Jvc3NlcnZpY28gZXhww7VlIGFwZW5hcyBlbmRwb2ludHMgUkVTVCBKU09OIGUgbsOjbyB1dGlsaXphIHJlcy5yZWRpcmVjdCBjb20gcGFyw6JtZXRyb3MgZG8gdXN1w6FyaW8uIiwgImp1c3RpZmljYXRpb24iOiJ2dWxuZXJhYmxlX2NvZGVfbm90X2luX2V4ZWN1dGVfcGF0aCIsICJwcm9kdWN0cyI6W3siQGlkIjoicGtnOm5wbS9leHByZXNzQDQuMTguMiJ9XSwgInN0YXR1cyI6Im5vdF9hZmZlY3RlZCIsICJ2dWxuZXJhYmlsaXR5Ijp7ImRlc2NyaXB0aW9uIjoiT3BlbiBSZWRpcmVjdCBlbSB2ZXJzb2VzIGFudGVyaW9yZXMgZG8gRXhwcmVzcy5qcyBhbyBwYXNzYXIgVVJMcyBuYW8gY29uZmlhdmVpcyBlbSByZXMucmVkaXJlY3QoKSIsICJuYW1lIjoiQ1ZFLTIwMjQtMjkwNDEifX1dLCAidGltZXN0YW1wIjoiMjAyNi0xMS0xOFQxMjowMDowMFoiLCAidG9vbGluZyI6Ik9wZW5WRVggdjAuMi4wIC8gQ29zaWduIEF0dGVzdCB2Mi40IiwgInZlcnNpb24iOjF9fQ==","payloadType":"application/vnd.in-toto+json","signatures":[{"sig":"MEYCIQCluY6ZrCrOJ36S0Dlvyd/P08gir5bglobcFNfC4LS8OQIhALPQsSIUJqAXANCKjrJUF517wNROHjqLj1NLax71K5Ch"}]}
  ```

---

## 4. Artifacts & Self-Check — *Aula 08* (`0,5 pt`)
- **Artifacts baixados da aba Summary da run verde (`supply-chain-evidence-bundle`):**
  - `[ ] dist/sbom.cdx.json`
  - `[ ] dist/dossie_conformidade.json`
  - `[ ] attestations/vex_customizado.openvex.json`
- **Saída do `python3 tools/selfcheck_capstone.py --trilha 3`:**
  ```text
  [Cole aqui o resultado do selfcheck --trilha 3]
  ```

---

## 5. Bloco Express Conceitual — *Aulas 01 & 02* (`1,0 pt`)
*(Dispensado automaticamente caso você marque a opção **Modo Incremental** no cabeçalho acima!)*

1. **SLSA v1.0 / NIST SSDF:** Por que o build executado no GitHub Actions com `id-token: write` e registro no Rekor atende a garantias superiores às de um build manual local?
   
Em um build manual, a assinatura de arquivos do repositório (artefatos) geralmente depende de chaves privadas armazenadas localmente. Para a segurança de build de ambientes automatizados, id-token: write cumpre um papel fundamental que é o de permitir a atuação do Github Actions como provedor OIDC para emissão de um token JWT identificador válido apenas para aquele job específico. Esse token é utilizado posteriormente para gerar um certificado de assinatura digital pela ferramenta Cosign, eliminando a necessidade de gerenciar chaves privadas locais.

Mesmo com assinatura digital automatizada com token efêmero, é necessário ser capaz de conferir os dados da procedência de uma determinada assinatura digital. Nesse sentido, o Rekor é um registro cronológico público imutável de assinaturas digitais geradas pelo Cosign para que qualquer um seja capaz de aferir a validade de determinada assinatura digital.
   
2. **Proteção contra Dependency Confusion:** Qual a função do arquivo `src/.npmrc` e da flag `npm install --ignore-scripts` executada na esteira?

Um arquivo .npmrc serve para armazenar configurações personalizadas que controlam como o npm se comporta ao instalar, publicar ou gerenciar pacotes. Ele ajuda a garantir que pacotes de dependência sejam instalados a partir dos escopos corretos (público ou privado), evitando um ataque de dependency confusion. Já o npm install --ignore-scripts, ele faz a instalação de dependências impedindo que qualquer script de ciclo de vida seja executado, prevenindo que possíveis scripts maliciosos injetados sejam executados no projeto.
   
