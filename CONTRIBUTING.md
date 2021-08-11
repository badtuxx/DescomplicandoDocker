<!-- TOC -->

- [Contribuindo](#contribuindo)
- [Dica](#dica)

<!-- TOC -->

# Contribuindo

Sua contribuição é muito bem vinda!

Execute os passos a seguir sempre que desejar melhorar o conteúdo deste repositório.

* Instale os seguintes pacotes: ``git`` e um editor de texto de sua preferência.
* Crie um fork neste repositório. Veja este tutorial: https://help.github.com/en/github/getting-started-with-github/fork-a-repo
* Clone o repositório resultante do fork para o seu computador.
* Adicione a URL do repositório de origem com o comando a seguir.

```
git remote -v
git remote add upstream https://github.com/badtuxx/DescomplicandoDocker
git remote -v
```

* Crie uma branch usando o padrão:

```
git checkout -b BRANCH_NAME
```

* Certifique-se de que está na branch correta, usando o comando a seguir.

```
git branch
```

* A branch em uso contém o '*' antes do nome.
* Faça as alterações necessárias.
* Teste suas alterações (se necessário).
* Commit as suas alterações na branch recém criada, de preferência faça um commit para cada arquivo editado/criado.
* Envie os commits para o repositório remoto com o comando:

```git push --set-upstream origin BRANCH_NAME```.

* Crie um Pull Request (PR) para a branch `main` do repositório original. Veja este [tutorial](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork)
* Atualize o conteúdo com as sugestões do revisor (se necessário).
* Depois de aprovado e realizado o merge do seu PR, atualize as mudanças no seu repositório local com os comandos a seguir.

```bash
git checkout main
git pull upstream main
```

* Remova a branch local após a aprovação e merge do seu PR, usando o comando:

```
git branch -d BRANCH_NAME
```

* Atualize a branch ``main`` do seu repositório local.

```
git push origin main
```

* Envie a exclusão da branch local para o seu repositório no GitHub com o comando:

```
git push --delete origin BRANCH_NAME
```

* Para manter seu fork em sincronia com o repositório original, execute estes comandos:

```
git pull upstream main
git push origin main
```

Referência:
* https://blog.scottlowe.org/2015/01/27/using-fork-branch-git-workflow/

# Dica

**Você pode usar o editor de texto de sua preferência e que se sinta mais confortável de usar.**

Mas o VSCode (https://code.visualstudio.com), combinado com os plugins a seguir, auxilia o processo de edição/revisão, principalmente permitindo a pré-visualização do conteúdo antes do commit, analisando a sintaxe do Markdown e gerando o sumário automático, à medida que os títulos das seções vão sendo criados/alterados.

* Markdown-lint: https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint
* Markdown-toc: https://marketplace.visualstudio.com/items?itemName=AlanWalk.markdown-toc
* Markdown-all-in-one: https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one
* YAML: https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml
* Helm-intellisense: https://marketplace.visualstudio.com/items?itemName=Tim-Koehler.helm-intellisense
* GitLens: https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens
* Themes for VSCode:
    * https://code.visualstudio.com/docs/getstarted/themes
    * https://dev.to/thegeoffstevens/50-vs-code-themes-for-2020-45cc
    * https://vscodethemes.com/
