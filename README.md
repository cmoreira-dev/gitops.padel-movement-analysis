# gitops.template

Template pra novos repos `gitops.<app>` de aplicações desenvolvidas por mim (não
addons de terceiros — pra isso, ver o padrão `helm/<addon>` com dependency direta
no chart upstream, usado em `gitops.core-addons`).

O `helm/` deste template já vem com o `generic-app` (`gitops.generic-app-chart`)
como dependency — cobre Deployment + Service + HTTPRoute (Gateway API) +
ExternalSecret. Pra usar num app novo:

1. Copiar este template pro novo repo `gitops.<app>`.
2. Em `helm/Chart.yaml`, trocar `<app-name>` pelo nome real do app.
3. Preencher `helm/values.yaml` (`image.repository`/`tag`, `service.targetPort`,
   `httpRoute.hostnames`, etc.) — comentários no arquivo indicam o que é
   obrigatório vs. opcional. Referência completa dos campos:
   `gitops.generic-app-chart/charts/generic-app/values.yaml`.
4. Rodar `helm dependency update helm/` pra resolver o subchart antes de commitar
   (gera `helm/Chart.lock` e `helm/charts/`).
5. `argocd/` fica vazio (só o `.gitkeep`) — o `ApplicationSet/gitops-repos` gera a
   Application automaticamente pra qualquer repo `gitops.*`, apontando pra esse
   diretório. Só popular se o app precisar de algo além do que o Helm chart cobre.

> **Pendência conhecida:** a dependency acima usa `git+https://...`, que exige o
> plugin `helm-git` no ArgoCD (repo-server) pra resolver em sync — hoje **não
> está configurado** em `infra-as-code/homelab-bootsrap-k3s/k8s-addons/argocd/values.yaml`.
> Sem isso, o `helm dependency update` funciona local (se você tiver o plugin
> instalado), mas o ArgoCD vai falhar ao sincronizar de verdade. Resolver antes
> do primeiro app real usar este template.
