# Canal de atualizações — Sistema Integrado e-SUS APS

Este repositório público é exclusivo para **metadados de atualização assinados** e documentação do canal.

## Arquivos permitidos

- `update.json`: manifesto da versão, assinado pelo responsável pela publicação.
- Documentação pública do canal.

Os instaladores devem ser anexados a uma publicação em **GitHub Releases**; não devem ser commitados no histórico deste repositório.

## Segurança — nunca publicar

Nunca enviar para este repositório:
- código-fonte Python ou ZIPs de desenvolvimento/compilação;
- chave privada de assinatura (`.pem`, `.key`, `.pfx`, `.p12`) ou sua senha;
- credenciais, arquivos de configuração privados, licenças individuais;
- exportações, dados nominais, cache ou qualquer dado do PEC/e-SUS APS.

O aplicativo só deve aceitar um manifesto com assinatura válida e hash SHA-256 do instalador correspondente à chave pública incorporada na versão publicada. Não crie manualmente um `update.json` vazio ou sem assinatura.

## Endereço esperado do manifesto

`https://raw.githubusercontent.com/Auchvs/SistemaIntegradoESUS-Atualizacoes/main/update.json`

## Fluxo de publicação

1. Gerar e testar o instalador no computador administrativo protegido.
2. Publicar o instalador em GitHub Releases com uma tag de versão imutável, por exemplo `v1.1.1`.
3. No computador administrativo, gerar o manifesto com `RELEASE_ADMIN/assinar_atualizacao.py`, usando o instalador exato publicado, a URL HTTPS do recurso de Release e a chave privada armazenada fora do projeto.
4. Revisar o conteúdo do manifesto. Ele inclui versão, URL do instalador, SHA-256, notas da versão e assinatura digital; não inclui a chave privada nem dados do PEC.
5. Adicionar o `update.json` assinado à branch `main`.
6. Verificar o endereço público do manifesto e testar a validação no aplicativo antes de anunciar o canal de atualizações.

A primeira instalação com o atualizador configurado deve ser distribuída manualmente. As versões posteriores só devem ser anunciadas após o ciclo completo de assinatura, download e instalação ser testado em Windows.
