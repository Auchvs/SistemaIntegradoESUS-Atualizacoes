# Guia de publicação segura — versão 1.1.1

Este guia é para o computador administrativo que já possui o kit privado e a chave Ed25519 gerada durante a configuração.

## 1. Publicar o instalador inicial

1. Abra a página do repositório: https://github.com/Auchvs/SistemaIntegradoESUS-Atualizacoes
2. Clique em **Releases** e depois **Create a new release**.
3. Use a tag `v1.1.1` e o título `Sistema Integrado e-SUS APS 1.1.1`.
4. Anexe **somente** o arquivo `Instalador_Sistema_Integrado_eSUS_APS.exe` existente na pasta `SistemaIntegradoESUS_Builder/Output` do computador administrativo.
5. Publique a release. Não envie o ZIP privado de compilação, o pacote de desenvolvimento, scripts, chave privada, senhas, licenças ou dados do PEC.

## 2. Gerar o manifesto assinado no computador administrativo

Abra o Prompt de Comando (CMD) e entre na pasta raiz do kit privado que foi configurado para a versão 1.1.1 (a pasta que contém `RELEASE_ADMIN` e `SistemaIntegradoESUS_Builder`). Exemplo de comandos — substitua o primeiro caminho pelo local real do kit privado:

```bat
cd /d "C:\CAMINHO\PARA\KitESUS_Compilacao_PRIVADA_v1.1.1_CORRECAO_CAMINHO_CHAVE_2026-10-26"
py -3.13 ".\RELEASE_ADMIN\assinar_atualizacao.py" --versao 1.1.1 --instalador ".\SistemaIntegradoESUS_Builder\Output\Instalador_Sistema_Integrado_eSUS_APS.exe" --url-instalador "https://github.com/Auchvs/SistemaIntegradoESUS-Atualizacoes/releases/download/v1.1.1/Instalador_Sistema_Integrado_eSUS_APS.exe" --chave-privada "%USERPROFILE%\SistemaIntegradoESUS-Admin\chave_privada_atualizacao_v1_1_1.pem" --saida "%USERPROFILE%\Desktop\update.json" --notas "Primeira distribuicao publica da versao 1.1.1."
```

O script pedirá a senha da chave privada. Digite-a localmente; ela não deve ser compartilhada nem adicionada a nenhum repositório. Confirme que o arquivo `update.json` foi criado na Área de Trabalho e não o edite manualmente depois de assinado.

Se o nome ou o caminho real da chave for diferente, use o caminho exato que apareceu ao configurá-la. **Não gere uma chave nova** para essa versão; o instalador deve confiar na chave pública correspondente à chave privada usada para assinar o manifesto.

## 3. Publicar update.json

1. No repositório público, clique em **Add file → Upload files**.
2. Selecione o `update.json` recém-criado na Área de Trabalho.
3. Confirme que o destino é a raiz da branch `main` e que o nome do arquivo é exatamente `update.json`.
4. Faça o commit.

Depois, confirme que este endereço mostra JSON, não 404:
https://raw.githubusercontent.com/Auchvs/SistemaIntegradoESUS-Atualizacoes/main/update.json

## 4. Validar a primeira instalação

Instale a versão 1.1.1 no computador de teste. Ao abrir o aplicativo, ele consulta o canal após alguns segundos; também há o botão **Verificar atualizações**. Como o manifesto inicial tem versão 1.1.1 e o aplicativo instalado também é 1.1.1, o resultado esperado é “O sistema está atualizado”. Isso verifica o acesso ao manifesto e a assinatura, mas **não testa ainda a instalação de uma versão nova**.

Para testar a instalação de ponta a ponta, prepare uma versão real posterior (por exemplo, 1.1.2) com uma mudança efetiva, mantendo a mesma chave pública de confiança. Compile e teste o novo instalador, publique-o em uma nova Release/tag `v1.1.2`, gere um novo `update.json` assinado com versão 1.1.2 e a URL do novo instalador, e substitua o manifesto da branch `main`. Não anuncie uma versão 1.1.2 apontando para o instalador 1.1.1: isso pode provocar um ciclo de atualização incorreto.

## Regras de segurança

- Nunca publique a chave privada `.pem`, a senha, código-fonte, ZIPs privados, licenças individuais, credenciais PostgreSQL ou dados do PEC.
- O manifesto deve ser produzido pelo script `RELEASE_ADMIN/assinar_atualizacao.py`; não crie uma assinatura manual.
- O instalador baixado pelo atualizador só deve ser executado depois da validação da assinatura Ed25519 do manifesto e da correspondência do SHA-256.
- A primeira versão com atualizador configurado precisa ser instalada manualmente. As versões anteriores sem chave pública embutida não passam a confiar em atualizações apenas porque o repositório existe.
