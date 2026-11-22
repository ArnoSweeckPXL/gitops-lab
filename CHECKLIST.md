# Checklist Argo CD

## Setup

- [x] De volledige lab en de drie oefeningen gelezen.
- [x] Docker, kubectl, k3d, Git en de CLI gecontroleerd.
- [x] Bestaand cluster `argocd-lab` met de juiste Kubernetes-versie gebruikt.
- [x] Zeven Argo CD-pods Running en Ready.
- [x] Argo CD-server en Windows-CLI op versie 3.5.4 gecontroleerd.
- [x] De Kustomize- en Helm-versies van de server bekeken.
- [x] Tunnel op 8443 en aanmelden via CLI gecontroleerd.
- [x] Aanmelden in de web-UI gecontroleerd.
- [x] Opnieuw gestart met een lege Application-lijst.
- [x] Poort 8080 en de andere clusters behouden.

## Stap 1: Git

- [x] Een nieuwe repository met branch `main` gemaakt.
- [x] De Deployment, Service, Ingress en Kustomization uit de cursus gemaakt.
- [x] Dev met namespace shop-dev, versie 6.15.0 en de juiste host gemaakt.
- [x] Prod met namespace shop-prod, versie 6.14.1 en twee replica's gemaakt.
- [x] Beide overlays gebouwd met `kubectl kustomize`.
- [x] De publieke repository `gitops-lab` gemaakt en gepusht.
- [x] De eigen GitHub-gebruikersnaam in de bestanden gezet.

## Stap 2: eerste Application

- [x] shop-dev zonder automatische sync toegepast.
- [x] OutOfSync en Missing gezien, met vijf gewenste objecten.
- [x] De diff bekeken en met de hand gesynchroniseerd.
- [x] Synced en Healthy gecontroleerd.
- [x] Via HTTP versie 6.15.0 en Hello from dev gecontroleerd.
- [x] De resourceboom tot aan de Pod in de web-UI bekeken.

## Stap 3: aanpassen via Git

- [x] Hello again from dev in Git gezet en gepusht.
- [x] Met refresh de nieuwe ontbrekende ConfigMap en de verschillen gezien.
- [x] Zonder prune gesynchroniseerd en twee ConfigMaps gezien.
- [x] De melding `1 resources require pruning` gezien.
- [x] Met prune gesynchroniseerd en nog een web-config overgehouden.
- [x] De nieuwe tekst via HTTP gecontroleerd.

## Stap 4: automatische sync

- [x] Prune en selfHeal aangezet voor dev.
- [x] Naar drie replica's geschaald en het herstel naar een gezien.
- [x] De Service verwijderd en het automatische herstel gezien.
- [x] Hello from GitOps via Git gepusht.
- [x] Automatische sync en prune gezien, zonder zelf een sync te starten.
- [x] Hello from GitOps via HTTP gecontroleerd.

## Stap 5: prod en rollback

- [x] shop-prod met handmatige sync gemaakt.
- [x] Prod op 6.14.1 via HTTP gecontroleerd.
- [x] De Application-bestanden gecommit en gepusht.
- [x] De prod-image naar 6.15.0 gezet en gepusht.
- [x] OutOfSync en alleen de gewijzigde imageregel gezien via CLI en de UI-DIFF.
- [x] Met de hand gesynchroniseerd en HTTP-versie 6.15.0 gezien.
- [x] Twee entries in de historiek gezien, ook in de UI met het Rollback-menu.
- [x] Naar history-ID 0 teruggerold; HTTP gaf weer 6.14.1.
- [x] Prod was na de rollback OutOfSync omdat Git nog 6.15.0 aangaf.
- [x] De releasecommit met git revert teruggedraaid en gepusht.
- [x] Daarna Synced zonder een nieuwe sync.
- [x] De rollback op dev geprobeerd en de verwachte weigering gezien.

## Stap 6: project

- [x] Project shop met de eigen repo, namespace shop-* en alleen Namespace als clusterresource gemaakt.
- [x] Dev en prod naar project shop verplaatst; beide bleven gezond.
- [x] De rogue-Application buiten de map argocd gemaakt en toegepast.
- [x] Unknown en de fout over de bestemming kube-system gezien.
- [x] De rogue-Application en het testbestand weer verwijderd.

## Stap 7: ApplicationSet en root

- [x] De Git-directorygenerator en de template uit de cursus gemaakt.
- [x] Automatische sync voor alles behalve prod ingesteld met templatePatch.
- [x] De twee losse Application-bestanden verwijderd.
- [x] root.yaml gecommit, gepusht en toegepast.
- [x] Root beheert het project en de ApplicationSet; beide zijn gezond.
- [x] Dev en prod hebben ApplicationSet als eigenaar.
- [x] Dev gebruikt Auto-Prune en prod blijft Manual.
- [x] Dezelfde dev-Pod en de bestaande prod-historiek zijn behouden.
- [x] Root en ApplicationSets > shop bekeken in de web-UI.

## Stap 8: Helm

- [x] De podinfo-Application via Git toegevoegd, zonder kubectl apply of helm install.
- [x] Chart 6.15.0, twee replica's en CreateNamespace gebruikt.
- [x] Root maakte de Application en de chart werd automatisch uitgerold.
- [x] Twee Running/Ready-pods en Hello from a Helm chart gecontroleerd.
- [x] In helm list stond geen podinfo-release.

## Oefening 1: staging

- [x] Alleen namespace.yaml en kustomization.yaml voor staging via Git toegevoegd.
- [x] shop-staging verscheen vanzelf en synchroniseerde automatisch.
- [x] Hello from staging via de juiste host op poort 8080 gecontroleerd.
- [x] De finalizer bekeken en staging via Git weer verwijderd.
- [x] Gecontroleerd dat de Application en namespace weg zijn.

## Oefening 2: hook

- [x] De migrate Job met PreSync, BeforeHookCreation, backoffLimit 0 en sleep 5 gemaakt.
- [x] De Job en Hello from dev, migrated in dezelfde commit gezet.
- [x] De Job kwam eerst; pas daarna kwamen de nieuwe ConfigMap en Deployment.
- [x] De Job laten mislukken met exit 1 en tegelijk de tekst veranderd.
- [x] De fout en retries bekeken; de oude tekst bleef zichtbaar.
- [x] De fix meteen gepusht en gezien dat de retry nog de kapotte commit gebruikte.
- [x] Met terminate-op de oude operatie gestopt en het herstel gecontroleerd.
- [x] Alleen het Job-commando aangepast en gezien dat de hook niet opnieuw liep.

## Oefening 3: QA

- [x] De foutieve podinfo-qa.yaml buiten argocd opgeslagen en toegepast.
- [x] De fout over project, repository en namespace gevonden; project naar default gezet en opnieuw toegepast.
- [x] De fout over chart 6.99.0 gevonden; versie naar 6.15.0 gezet en opnieuw toegepast.
- [x] De ontbrekende namespace gevonden; CreateNamespace toegevoegd en opnieuw toegepast.
- [x] De lopende retry gestopt en een handmatige sync gestart.
- [x] Synced maar Progressing gezien door de verkeerde image 6.15.9; de Pods beschreven.
- [x] Image 6.15.0 toegepast en automatisch twee gezonde Pods gekregen.
- [x] De HTTP-respons gecontroleerd en QA daarna opgeruimd.

## Eindcontrole en cleanup

- [x] Alle vier Applications zijn Synced en Healthy.
- [x] De juiste replica's, versies en teksten zijn gecontroleerd.
- [x] Geen tijdelijke staging-, rogue- of QA-resources meer aanwezig.
- [x] Geen wachtwoorden of Secrets in Git.
- [ ] Cluster verwijderen: niet uitgevoerd, zodat poort 8080 beschikbaar blijft.
- [ ] Tunnel stoppen: niet uitgevoerd, zodat 8443 bereikbaar blijft.
