# Instruções para agentes

Este repositório é a fonte dos `.proto` do TechFab ESP Framework. O que for mergeado aqui só chega aos consumidores depois de um tag e de um release. Siga o fluxo abaixo inteiro, sem pular etapas.

## Fluxo de release

1. Faça o merge do PR em `master`. Mudanças em `.proto` são apenas aditivas, conforme o `CONTRIBUTING.md`. A deleção do ramo após o merge é automática.
2. Crie o tag anotado `vX.Y.Z` sobre o commit de merge e envie para o remoto: `git tag -a vX.Y.Z -m "vX.Y.Z" <sha>` e `git push origin vX.Y.Z`. O número segue o último tag, com incremento minor quando há recurso novo.
3. O push do tag dispara o workflow `build.yml`, que gera o C e o Go e publica o tag `X.Y.Z` (sem o `v`) em `techfab-iot/c-proto` e o tag `vX.Y.Z` em `techfab-iot/go-proto`. Espere o workflow terminar com sucesso e confira os dois tags antes de seguir.
4. Crie o release no GitHub para o tag. O tag sozinho não basta: todo tag deste repositório tem um release correspondente, e o mais novo é marcado como Latest. Use `gh release create vX.Y.Z --repo techfab-iot/proto --title "vX.Y.Z" --latest --verify-tag --notes "<notas>"`.
5. Atualize os consumidores: troque a versão de `techfab-iot/c-proto` no `main/idf_component.yml` do firmware e rode `idf.py update-dependencies` (o `dependencies.lock` fixa o commit antigo e a build usa o c-proto desatualizado se essa etapa for esquecida).

## Notas do release

As notas ficam em português do Brasil, no formato dos releases anteriores: a seção `## What's Changed` com um item por mudança, um parágrafo curto sobre compatibilidade (por exemplo, "mudança apenas aditiva") e a linha `**Full Changelog**: https://github.com/techfab-iot/proto/compare/vA.B.C...vX.Y.Z`.

## Conferência rápida

- `gh release list --repo techfab-iot/proto --limit 3` mostra o novo release como Latest.
- `gh api repos/techfab-iot/c-proto/tags --jq '.[0].name'` devolve `X.Y.Z`.
- O firmware compila sem override local e o `managed_components/techfab-iot__c-proto` contém o símbolo novo.
