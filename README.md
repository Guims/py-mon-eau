# py-mon-eau

Version 0.0.28

Get your water meter data from your online Suez account (<www.toutsurmoneau.fr>) as well as other sites managed by Suez and using the same interface.

Two Classes are provided:

- `AsyncClient` is an asynchronous client
- `Client` is a legacy synchronous client, compatible with module `pySuez`

## Installation

```bash
pip install toutsurmoneau
```

## CLI Usage

```bash
toutsurmoneau [-h] -u _user_name_here_ -p _password_here_ [-c _meter_id_] [-e _action_]
```

## API Usage

### Async use

```python
import toutsurmoneau
import aiohttp
import asyncio


async def the_job():
    async with aiohttp.ClientSession() as session:
        obj = toutsurmoneau.AsyncClient(
            username='_username_here_', password='_password_here_', session=session)
        return await obj.async_contracts()

print(f">>{asyncio.run(the_job())}")
```

### Sync use

```python
import toutsurmoneau

client = toutsurmoneau.Client('_username_here_', '_password_here_')

print(f">>{client.contracts()}")
```

The sync object returns the same values as `pySuez`.

## History

This module was inspired from [pySuez from Ooii](https://github.com/ooii/pySuez).

## Dev data

Open developer tools in browser, and select the network tab.
Then apply the following filter:

```text
-nr-data -google -1password -cookiebot -acticdn -xiti -cloudfront -qualtrics
```

## Debugging

As the API is not really public, it changes sometimes, and the module also needs some adjustments.

## Contribution

Release procedure:

```bash
source .venv/bin/activate
make inst
make bumpver
make release
make publish
```


## Sécurité de l'authentification (fork Guims, 0.0.28)

Cette version est destinée aux tests Home Assistant avec Suez :

- une seule tentative de connexion par instance de client (aucune réauthentification automatique après expiration ou refus) ;
- appels API séquentiels pour éviter les authentifications concurrentes ;
- pas de mots de passe, cookies, jetons CSRF ni contenu de réponse dans les journaux de débogage de ce client ;
- les erreurs réseau/API sont propagées sans les masquer derrière des appels supplémentaires ;
- récupération du dernier index au maximum sur le mois courant et le précédent.

**Limites :** la politique anti-abus de Suez n'est pas documentée ici. Ces changements réduisent les requêtes risquées, mais ne prouvent pas la cause d'un blocage de compte. Une nouvelle instance (après un redémarrage HA) peut effectuer une nouvelle tentative de connexion ; l'intégration Home Assistant garde un verrou persistant après erreur.

Ne pas utiliser de tentative répétée en cas de refus de connexion.
