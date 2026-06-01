# Fernando

## Responsabilidades

- Exchange API e Exchange Service
- Documentação relacionada ao módulo de câmbio

## Repositórios

- [exchange](https://github.com/pma2026-carol-fernando-thais/exchange.git)
- [exchange-service](https://github.com/pma2026-carol-fernando-thais/exchange-service.git)

## Bottlenecks Implementados

### Caching com Redis

O `exchange-service` consulta uma API externa (`awesomeapi.com.br`) a cada requisição, introduzindo latência e risco de indisponibilidade por rate limit.

**Solução:** cache Redis com TTL de 60 segundos por par de moedas. Após a primeira consulta, requisições ao mesmo par (ex: `USD-BRL`) retornam em ~1ms em vez de ~300ms:

```python
cache_key = f"rate:{source}:{target}"
cached = redis_client.get(cache_key)
if cached:
    return ProviderRate(**json.loads(cached))
# busca externa apenas se não estiver em cache
redis_client.setex(cache_key, CACHE_TTL, json.dumps({...}))
```
