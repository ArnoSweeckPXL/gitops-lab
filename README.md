# Argo CD lab

Lab van [PXL Systems Expert](https://pxl-systems-expert.github.io/kubernetes-course/#/labs/16-argocd?id=setup).

Dit is de shop uit de cursus. Dev wordt automatisch gesynchroniseerd. Prod wordt met de hand gesynchroniseerd.

## Omgeving

- Windows met PowerShell.
- Cluster: `argocd-lab` met Kubernetes `v1.36.5+k3s1`.
- Argo CD en CLI: `v3.5.4`.
- De bestaande installatie is opnieuw gebruikt.
- De shop gebruikt poort `8080`. De mapping op `8081` is ook behouden.
- Andere clusters zijn niet aangepast.

## Mappen

- `deploy/base`: de gewone shopbestanden.
- `deploy/overlays/dev`: instellingen voor dev en de migratiejob.
- `deploy/overlays/prod`: instellingen voor prod.
- `argocd`: het project, de ApplicationSet en de Helm-Application.
- `root.yaml`: start de bestanden uit de map `argocd`.
- `podinfo-qa.yaml`: de verbeterde versie van oefening 3.

## Toegang

De tunnel moet blijven draaien:

```powershell
kubectl --context k3d-argocd-lab port-forward svc/argocd-server -n argocd 8443:443
```

De webpagina staat op `https://localhost:8443`. De gebruiker is `admin`. Het wachtwoord staat niet in Git.

## Wat er getest is

Dev is eerst met de hand gesynchroniseerd. Daarna is de tekst aangepast via Git. Zonder prune bleven er twee ConfigMaps staan. Met prune bleef er een over.

Daarna is automatische sync aangezet. Na het schalen naar drie replica's ging dev terug naar een replica. Een verwijderde Service kwam ook vanzelf terug.

Prod begon op versie `6.14.1`. Na de release stond er `6.15.0`. De rollback zette prod terug op `6.14.1`. Daarna is de releasecommit teruggedraaid met `git revert`.

Het project `shop` liet de test naar `kube-system` niet toe. De ApplicationSet heeft dev en prod overgenomen. De bestaande dev-Pod en de historiek van prod bleven behouden.

De Helm-Application gebruikt twee Pods. `helm list -A` toont geen podinfo-release, omdat Argo CD de chart uitrolt.

## Oefeningen

### 1. Staging

Voor staging waren alleen `namespace.yaml` en `kustomization.yaml` nodig. De ApplicationSet maakte zelf `shop-staging`. De tekst was Hello from staging. Na het verwijderen van de map in Git verdwenen de Application en de namespace ook.

### 2. Migratiejob

De PreSync-job liep voor de nieuwe ConfigMap en Deployment. Met `exit 1` mislukte de job. De nieuwe tekst kwam toen niet online; de oude shop bleef werken.

Na het pushen van de fix bleef de lopende retry de kapotte commit gebruiken. Met `argocd app terminate-op shop-dev` kon de nieuwe versie daarna wel doorlopen. De tekst werd Hello from dev, fixed.

Bij alleen een wijziging aan het Job-commando gebeurde er geen nieuwe sync. De Job hield dezelfde UID. De hook wordt pas uitgevoerd bij een volgende echte sync.

### 3. QA

De vier fouten zijn apart verbeterd. Na iedere verbetering is het bestand opnieuw toegepast.

| Fout | Waar gezien | Oplossing |
| --- | --- | --- |
| Project shop liet de repository en namespace niet toe | InvalidSpecError bij de Application | Project default gebruiken |
| Chart 6.99.0 bestond niet | ComparisonError bij de Application | Chart 6.15.0 gebruiken |
| Namespace podinfo-qa ontbrak | De sync-operatie meldde namespace not found | CreateNamespace=true toevoegen |
| Image 6.15.9 bestond niet | Application was Synced maar Progressing; de Pods hadden ErrImagePull | Image 6.15.0 gebruiken |

Bij de namespacefix bleef de retry de oude opties gebruiken. Daarom waren `terminate-op` en een handmatige sync nodig. De imagefix werd daarna wel automatisch gesynchroniseerd. QA had twee gezonde Pods en is na de controle weer verwijderd.

## Laatste resultaat

| Application | Status | Pods | Versie |
| --- | --- | --- | --- |
| root | Synced / Healthy | n.v.t. | n.v.t. |
| shop-dev | Synced / Healthy | 1 | 6.15.0 |
| shop-prod | Synced / Healthy | 2 | 6.14.1 |
| podinfo | Synced / Healthy | 2 | 6.15.0 |

## Handige controles

```powershell
kubectl --context k3d-argocd-lab get applications -n argocd
kubectl --context k3d-argocd-lab get pods -n shop-dev
kubectl --context k3d-argocd-lab get pods -n shop-prod
kubectl --context k3d-argocd-lab get pods -n podinfo

curl.exe -s -H 'Host: dev.shop.example.com' http://localhost:8080
curl.exe -s -H 'Host: shop.example.com' http://localhost:8080
```

In PowerShell is `curl.exe` gebruikt. Voor zoeken in uitvoer is `Select-String` gebruikt in plaats van `grep`.

De checklist staat in [CHECKLIST.md](CHECKLIST.md). Logs en screenshots staan alleen lokaal in `evidence/`. Tools en aanmeldgegevens staan ook niet in Git.

## Cleanup

Het cluster is niet verwijderd en de tunnel is niet gestopt. Zo blijft de lab bereikbaar op poort 8080 en 8443. De tijdelijke staging-, rogue- en QA-omgevingen zijn wel verwijderd.
