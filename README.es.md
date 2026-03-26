# trendingcontent-agent

> Investigá tendencias y generá contenido listo para publicar en redes sociales — como una skill para Hermes/OpenClaw.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**trendingcontent** es una skill de agente IA en dos fases:

1. **Fase de investigación** — rastrea Reddit, X/Twitter, YouTube, TikTok, HackerNews, Bluesky y Brave Search para encontrar el contenido más relevante sobre tu tema en un período configurable (7, 15, 30 días o hasta 90).
2. **Fase de generación de contenido** — usa esos hallazgos para producir copies específicos por plataforma: hilos de Twitter/X, posts de LinkedIn y captions de Instagram.

Construida sobre [last30days](https://github.com/mvanhorn/last30days-skill) de [@mvanhorn](https://github.com/mvanhorn), con las siguientes mejoras:
- Períodos de tiempo variables (no solo 30 días)
- Generación de contenido para múltiples plataformas (Twitter, LinkedIn, Instagram)
- Control de tono (profesional, casual, educativo, viral)
- Salida multiidioma (inglés, español, portugués)
- Gestión de fuentes — podés activar o desactivar cada fuente individualmente
- Compatible con el estándar de skills de Hermes/OpenClaw

---

## Inicio rápido

### 1. Clonar e instalar

```bash
git clone https://github.com/gabogabucho/trendingcontent-agent.git
cd trendingcontent-agent
pip install -r requirements.txt
```

### 2. Configurar API keys

```bash
export SCRAPECREATORS_API_KEY="tu_key"   # requerida
export BRAVE_API_KEY="tu_key"            # opcional pero recomendada
```

O agregálas a tu archivo `.env`.

Conseguí una API key de ScrapeCreators en [scrapecreators.com](https://scrapecreators.com).
Conseguí una API key de Brave Search en [brave.com/search/api](https://brave.com/search/api/).

### 3. Ejecutar

```bash
python scripts/trendingcontent.py "inteligencia artificial" --days=7 --lang=es
```

---

## Uso

```
python scripts/trendingcontent.py <tema> [opciones]

Opciones:
  --days=N           Período: 7-90 días (por defecto: 30)
  --platform         twitter | linkedin | instagram | all (por defecto: all)
  --tone             professional | casual | educational | viral (por defecto: professional)
  --lang             en | es | pt (por defecto: en)
  --sources=F1,F2    Usar solo estas fuentes (separadas por coma)
  --disable=F1,F2    Desactivar fuentes específicas
  --list-sources     Ver todas las fuentes disponibles
  --quick            Menos fuentes, más rápido
  --deep             Más fuentes, más exhaustivo
  --research-only    Solo investigación, sin generar contenido
```

### Ejemplos

```bash
# Tendencias de IA últimos 7 días → todas las plataformas, en español
python scripts/trendingcontent.py "inteligencia artificial" --days=7 --lang=es

# Web3 → post de LinkedIn en español, tono profesional
python scripts/trendingcontent.py "web3" --days=15 --platform=linkedin --lang=es

# Clima tech → hilo viral en Twitter
python scripts/trendingcontent.py "cambio climático" --days=30 --platform=twitter --tone=viral --lang=es

# Tema de nicho — solo Reddit y YouTube (sin HackerNews)
python scripts/trendingcontent.py "pan de masa madre" --sources=reddit,youtube,brave --lang=es

# Desactivar fuentes que no aplican al tema
python scripts/trendingcontent.py "fintech" --disable=hackernews,tiktok --lang=es

# Ver todas las fuentes disponibles
python scripts/trendingcontent.py --list-sources

# Solo investigación, sin generar contenido
python scripts/trendingcontent.py "LLMs" --days=14 --research-only
```

---

## Gestión de fuentes

Distintos temas se benefician de distintas combinaciones de fuentes. Podés controlar exactamente cuáles se usan:

```bash
# Solo usar Reddit y Brave
python scripts/trendingcontent.py "criptomonedas" --sources=reddit,brave

# Usar todo excepto HackerNews (muy específico de tech)
python scripts/trendingcontent.py "moda sustentable" --disable=hackernews
```

### Fuentes disponibles

| Fuente | Descripción | Requiere |
|--------|-------------|----------|
| `reddit` | Posts de Reddit — discusión amplia de comunidad | ScrapeCreators API |
| `twitter` | Tweets de X/Twitter — reacciones en tiempo real | ScrapeCreators API |
| `youtube` | Videos + transcripciones de YouTube | ScrapeCreators + yt-dlp |
| `tiktok` | Videos virales de TikTok | ScrapeCreators API |
| `bluesky` | Posts de Bluesky — red social descentralizada | ScrapeCreators API |
| `hackernews` | Hacker News — muy específico de tech | API pública (sin key) |
| `brave` | Resultados web de Brave Search | Brave API key (opcional) |

**Consejo:** Para temáticas fuera del mundo tech, desactivá `hackernews`. Para temas muy de nicho, combiná `reddit + youtube + brave`.

---

## Como skill de Hermes/OpenClaw

Este repositorio sigue el [estándar de skills de Hermes](https://github.com/NousResearch/hermes-agent).
Para instalarla como skill:

```bash
# Copiar al directorio de skills de Hermes
cp -r . /ruta/a/hermes-agent/skills/social-media/trendingcontent/

# Agregar las API keys al .env de Hermes
echo "SCRAPECREATORS_API_KEY=tu_key" >> ~/.hermes/.env
echo "BRAVE_API_KEY=tu_key" >> ~/.hermes/.env
```

El agente descubrirá la skill automáticamente a través del archivo `SKILL.md`.

---

## Estructura del output

```
══════════════════════════════════════════════
TRENDINGCONTENT — BRIEF DE GENERACIÓN
══════════════════════════════════════════════
Tema:      inteligencia artificial
Período:   Últimos 7 días
Idioma:    Spanish
Tono:      Professional
Plataformas: Twitter, LinkedIn, Instagram

──────────────────────────────────────────────
RESUMEN DE INVESTIGACIÓN
──────────────────────────────────────────────
[contenido trending rankeado con títulos, scores y URLs]

──────────────────────────────────────────────
INSTRUCCIONES DE CONTENIDO
──────────────────────────────────────────────
[especificaciones por plataforma + guía de tono]
```

---

## Requisitos

- Python 3.9+
- `yt-dlp` — extracción de transcripciones de YouTube
- `requests` — llamadas HTTP
- ScrapeCreators API key (requerida)
- Brave Search API key (opcional)

---

## Créditos

- Motor de investigación original: [last30days-skill](https://github.com/mvanhorn/last30days-skill) de [@mvanhorn](https://github.com/mvanhorn) — Licencia MIT
- Capa de generación de contenido para redes sociales y empaquetado como skill: [@gabogabucho](https://twitter.com/gabogabucho)

---

## Licencia

Licencia MIT — ver [LICENSE](LICENSE) para más detalles.
