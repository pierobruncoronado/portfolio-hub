# docs/spec.md — Portfolio Hub

## Outcome + Why
One-page portfolio hub en URL pública que lleva a un screener de "¿quién es 
este?" a un demo vivo en <30 segundos. Why: la demostrabilidad es mi cuello 
de botella; el hub es la puerta de entrada que enlaza los 4 proyectos y sus 
demos en Render. Audiencia: recruiters/founders, en inglés, probablemente 
desde el celular.

## Decisiones cerradas (no re-litigar)
- One-page, project-first, dark theme, tipografía monospace.
- Cards: problema (1 línea) → stack → métrica real → links (Live Demo / 
  Repo / Case Study).
- El dinamismo viene de los demos vivos, NO de animaciones del hub.
- Stack: estático (HTML/CSS/JS o React build estático). Deploy: GitHub Pages 
  — gratis, no se pausa nunca, cero infraestructura nueva que monitorear.
- Skill frontend-design obligatoria para la dirección estética.
- Métricas copiadas verbatim de los CASE_STUDY: cero números inventados.

## Fuera de alcance (vinculante)
Blog, secciones "about me" largas, formulario de contacto (mailto basta), 
analytics, animaciones/scroll effects, toggle dark/light, CMS, i18n, 
testimonios, multi-página. Nada de esto antes de shippear.

## Criterios de aceptación
- [ ] URL pública en GitHub Pages responde; carga rápida (sin frameworks 
      pesados de runtime).
- [ ] 4 cards (analyst, gateway, clínica, calendar) con problema→stack→
      métrica→links. Clínica = "demo on request"; calendar = repo/case 
      study sin demo (por diseño, OAuth).
- [ ] Links de demo apuntan a Render con nota visible de cold-start 
      (30-60s free tier) — honestidad + evita que el screener crea que 
      está roto.
- [ ] Header: nombre + posicionamiento en 1 línea ("I ship LLM agents & 
      RAG systems to production") + GitHub/LinkedIn/email.
- [ ] Usable en móvil (verificado, no asumido).

## Timebox
1 día. Si revienta: recortar alcance (shippear con 2 cards), nunca extender.
